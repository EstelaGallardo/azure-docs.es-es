---
title: "Conexión a un clúster de Azure Container Service | Microsoft Docs"
description: "Conexión a un clúster de Kubernetes, DC/OS o Docker Swarm en Azure Container Service desde un equipo remoto"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, DC/OS, Azure
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 31897e11abfe70ed08381f0d13c6bdabe56c28ed
ms.openlocfilehash: 05ff751255000220be3b59d013b6106473e4732b
ms.lasthandoff: 03/02/2017


---
# <a name="make-a-remote-connection-to-a-kuburnetes-dcos-or-docker-swarm-cluster"></a>Establecimiento de una conexión remota a un clúster de Kuburnetes, DC/OS o Docker Swarm
Después de crear un clúster de Azure Container Service, es preciso conectarse al clúster para implementar y administrar las cargas de trabajo. En este artículo se describe cómo conectarse a la máquina virtual maestra del clúster desde un equipo remoto. 

Los clústeres de Kubernetes, DC/OS y Docker Swarm proporcionan los puntos de conexión de HTTP localmente. En el caso de Kubernetes, dicho punto de conexión se expone de forma segura en Internet y para acceder a él es preciso ejecutar la herramienta de línea de comandos `kubectl` desde cualquier equipo conectado a Internet. 

En el caso de DC/OS y Docker Swarm, es preciso crear un túnel SSH (Secure Shell) a un sistema interno. Después de establecer el túnel, puede ejecutar comandos que utilizan los puntos de conexión HTTP y ver la interfaz de web del clúster desde el sistema local. 


## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Kubernetes, DC/OS o Swarm [implementado en Azure Container Service](container-service-deployment.md).
* Archivo de clave privada RSA de SSH, correspondiente a la clave pública agregada al clúster durante la implementación. Estos comandos asumen que la clave privada SSH está en `$HOME/.ssh/id_rsa` en el equipo. Para más información, consulte estas instrucciones para [OS X y Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) o [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). Si la conexión SSH no funciona, es posible que tenga que [restablecer las claves SSH](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Conexión a un clúster de Kubernetes

Para instalar y configurar `kubectl` en el equipo, siga estos pasos.

> [!NOTE] 
> En Linux u OS X, puede que tenga que ejecutar los comandos de esta sección mediante `sudo`.
> 

### <a name="install-kubectl"></a>Instalación de kubectl
Una forma de instalar esta herramienta es usar la el comando `az acs kubernetes install-cli` de la CLI de Azure 2.0. Para ejecutar este comando, asegúrese de que [ha instalado](/cli/azure/install-az-cli2) la CLI de Azure 2.0 más reciente y que ha iniciado sesión en una cuenta de Azure (`az login`).

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Como alternativa, puede descargar el cliente más reciente directamente desde la [página de versiones de Kubernetes](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). Para más información, consulte [Installing and Setting up kubectl](https://kubernetes.io/docs/user-guide/prereqs/) (Instalación y configuración de kubectl).

### <a name="download-cluster-credentials"></a>Descarga de las credenciales del clúster
Una vez que tenga `kubectl` instalado, debe copiar las credenciales del clúster en la máquina. Una manera de obtener las credenciales es usar el comando `az acs kubernetes get-credentials`. Pase el nombre del grupo de recursos y el nombre del recurso del servicio de contenedor:


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

El comando descarga las credenciales del clúster en `$HOME/.kube/config`, donde `kubectl` espera que se encuentre.

Como alternativa, puede usar `scp` para copiar de forma segura el archivo de `$HOME/.kube/config` en la máquina virtual maestra al equipo local. Por ejemplo:

```console
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Si se encuentra en Windows, debe usar Bash en Ubuntu en Windows, el cliente de copia de archivos seguro PuTTy o una herramienta similar.



### <a name="use-kubectl"></a>Uso de kubectl

Una vez que haya configurado `kubectl`, puede probar la conexión con una lista de los nodos del clúster:

```console
kubectl get nodes
```

Puede probar otros comandos `kubectl`. Por último, puede ver el panel de Kubernetes. En primer lugar, ejecute un proxy al servidor de API de Kubernetes:

```console
kubectl proxy
```

La interfaz de usuario de Kubernetes está disponible en: `http://localhost:8001/ui`.

Para más información, consulte el [inicio rápido de Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Conexión a un clúster de DC/OS o Swarm

Para usar los clústeres de DC/OS y Docker Swarm implementados mediante Azure Container Service, siga estas instrucciones para crear un túnel SSH (Secure Shell) desde su sistema local Linux, OS X o Windows. 

> [!NOTE]
> Estas instrucciones se centran en la tunelización de tráfico TCP a través de SSH. También puede iniciar una sesión SSH interactiva con uno de los sistemas de administración del clúster interno, si bien esto no es recomendable. Al trabajar directamente en un sistema interno, pueden producirse cambios en la configuración de manera involuntaria.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Creación de un túnel de SSH en Linux u OS X
Lo primero que se hace al crear un túnel de SSH en Linux u OS X es buscar el nombre DNS público de los patrones de carga equilibrada. Siga estos pasos:


1. En [Azure Portal](https://portal.azure.com), desplácese el grupo de recursos que contiene el clúster del servicio de contenedor. Expanda el grupo de recursos de forma que se muestren todos los recursos. 

2. Haga clic en el recurso del servicio de contenedor y, a continuación, haga clic en **Información general**. El **nombre de dominio completo del maestro** del clúster se muestra en **Información esencial**. Guarde este nombre para usarlo más adelante. 

    ![Nombre DNS público](media/pubdns.png)

    También puede ejecutar el comando `az acs show` en el servicio del contenedor. Busque la propiedad **Master Profile:fqdn** en el resultado del comando.

3. Ahora, abra un shell y ejecute el comando `ssh` mediante la especificación de los valores siguientes: 

    **LOCAL_PORT** es el puerto TCP en el servicio del túnel al que conectarse. Para Swarm, establezca esta opción en 2375. Para DC/OS, establezca esta opción en 80.  
    **REMOTE_PORT** es el puerto del punto de conexión que desea exponer. Para Swarm, utilice el puerto 2375. Para DC/OS, utilice el puerto 80.  
    **USERNAME** es el nombre de usuario que se especificó cuando se implementó el clúster.  
    **DNSPREFIX** es el prefijo DNS que proporcionó al implementar el clúster.  
    **REGION** es la región en la que está ubicado el grupo de recursos.  
    **PATH_TO_PRIVATE_KEY** [OPCIONAL] es la ruta de acceso a la clave privada correspondiente a la clave pública que proporcionó al crear el clúster. Esta opción se usa con la marca `-i`.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com 
    ```
    > [!NOTE]
    > El puerto de conexión SSH es el 2200 y no el puerto 22 estándar. En un clúster con más de una máquina virtual maestra, éste es el puerto de conexión a la primera máquina virtual maestra.
    > 



En las secciones siguientes encontrar los ejemplos de DC/OS y Swarm.    

### <a name="dcos-tunnel"></a>Túnel de DC/OS
Para abrir un túnel para los puntos de conexión relacionados con DC/OS, ejecute un comando similar al siguiente:

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> Puede especificar un puerto local distinto del puerto 80, como, por ejemplo, el puerto 8888. Sin embargo, algunos vínculos de la interfaz de usuario web podrían no funcionar cuando se utiliza este puerto.

Ahora puede acceder a los puntos de conexión de DC/OS desde el sistema local a través de las siguientes direcciones URL (suponiendo que se usa el puerto local 80):

* DC/OS: `http://localhost:80/`
* Marathon: `http://localhost:80/marathon`
* Mesos: `http://localhost:80/mesos`

De igual forma, se puede acceder a las API de REST de cada aplicación a través de este túnel.

### <a name="swarm-tunnel"></a>Túnel de Swarm
Para abrir un túnel al punto de conexión relacionado con Swarm, ejecute un comando similar al siguiente:

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```

Ahora puede establecer la variable de entorno DOCKER_HOST de la forma siguiente. Puede usar la interfaz de la línea de comandos (CLI) de Docker de la forma habitual.

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Creación de un túnel de SSH en Windows
Existen varias opciones para crear túneles de SSH en Windows. En esta sección se describe cómo usar PuTTY para crear el túnel.

1. [Descargue PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) en el sistema Windows.

2. Ejecute la aplicación.

3. Escriba un nombre de host que conste del nombre de usuario de administrador de clústeres y el nombre DNS público del primer patrón del clúster. El **nombre de host** es similar a `adminuser@PublicDNSName`. Escriba 2200 en el campo **Puerto**.

    ![Configuración 1 de PuTTY](media/putty1.png)

4. Seleccione **SSH > Auth** (SSL > Aut.). Agregue una ruta de acceso al archivo de claves privadas (en formato. ppk) para la autenticación. Puede usar una herramienta como [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para generar este archivo a partir de la clave SSH utilizada para crear el clúster.

    ![Configuración 2 de PuTTY](media/putty2.png)

5. Seleccione **SSH > Tunnels** (SSH > Túneles) y configure los siguientes puertos reenviados:

    * **Puerto de origen:** use el 80 para DC/OS o el 2375 para Swarm.
    * **Destino:** use localhost:80 para DC/OS o localhost:2375 para Swarm.

    En el siguiente ejemplo se configura para DC/OS, pero su aspecto sería similar para Docker Swarm.

    > [!NOTE]
    > El puerto 80 no debe estar en uso cuando se cree este túnel.
    > 

    ![Configuración 3 de PuTTY](media/putty3.png)

6. Cuando haya terminado, haga clic en **Session > Save** (Sesión > Guardar) para guardar la configuración de la conexión.

7. Para conectarse a la sesión de PuTTY, haga clic en **Open** (Abrir). Cuando se conecte, podrá ver la configuración del puerto en el registro de eventos de PuTTY.

    ![Registro de eventos de PuTTY](media/putty4.png)

Cuando haya configurado el túnel de DC/OS, podrá acceder a los puntos de conexión relacionados en:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Una vez que haya configurado el túnel de Docker Swarm, abra la configuración de Windows para configurar una variable de entorno de sistema denominada `DOCKER_HOST` con un valor de `:2375`. Después, podrá acceder al clúster de Swarm a través de la CLI de Docker.

## <a name="next-steps"></a>Pasos siguientes
Implementar y administrar contenedores en un clúster:

* [Trabajo con Azure Container Service y Kubernetes](container-service-kubernetes-ui.md)
* [Administración de contenedores con la API de REST](container-service-mesos-marathon-rest.md)
* [Administración de contenedores con Docker Swarm](container-service-docker-swarm.md)


