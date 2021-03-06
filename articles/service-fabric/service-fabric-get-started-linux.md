---
title: "Configuración del entorno de desarrollo en Linux | Microsoft Docs"
description: "Instale el SDK y el motor en tiempo de ejecución, y cree un clúster de desarrollo local en Linux. Después de completar esta instalación, estará listo para crear aplicaciones."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 1e961eccbc4fb8af90c7da831429c942f92bdf79
ms.lasthandoff: 03/11/2017


---
# <a name="prepare-your-development-environment-on-linux"></a>Preparación del entorno de desarrollo en Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Para implementar y ejecutar [aplicaciones de Azure Service Fabric](service-fabric-application-model.md) en la máquina de desarrollo de Linux, instale el motor de tiempo de ejecución y el SDK común. También puede instalar SDK opcionales para Java y .NET Core.

## <a name="prerequisites"></a>Requisitos previos

### <a name="supported-operating-system-versions"></a>Versiones de sistemas operativos compatibles
Se admiten las siguientes versiones de sistemas operativos para desarrollo:

* Ubuntu 16.04 ("Xenial Xerus")

## <a name="update-your-apt-sources"></a>Actualización de los orígenes de apt
Para instalar el SDK y el paquete en tiempo de ejecución asociado mediante apt-get, primero debe actualizar los orígenes de apt.

1. Abra un terminal.
2. Agregue el repositorio de Service Fabric a su lista de orígenes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Agregue el repositorio de dotnet a su lista de orígenes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```
4. Agregue la nueva clave GPG a su conjunto de claves apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Actualice las listas de paquetes según los repositorios recién agregados.

    ```bash
    sudo apt-get update
    ```
## <a name="install-and-set-up-the-sdk"></a>Instalación y configuración del SDK
Una vez actualizados los orígenes, puede instalar el SDK.

1. Instale el paquete del SDK de Service Fabric. Se le pedirá que confirme la instalación y acepte un contrato de licencia.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. Ejecute el script de instalación del SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```


## <a name="set-up-the-azure-cross-platform-cli"></a>Instalación de la CLI multiplataforma de Azure
La [CLI multiplataforma de Azure][azure-xplat-cli-github] incluye comandos para interactuar con las entidades de Service Fabric, incluidos los clústeres y las aplicaciones. Como se basa en Node.js, [asegúrese de que Node esté instalado][install-node] antes de continuar con las instrucciones siguientes:

1. Clone el repositorio de github en la máquina de desarrollo.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. Cambie al repositorio clonado e instale dependencias de CLI mediante Node Package Manager (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. Cree un vínculo simbólico desde la carpeta bin/azure del repositorio clonado a /usr/bin/azure para que se agregue a la ruta de acceso y los comandos están disponibles desde cualquier directorio.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. Por último, habilite los comandos de finalización automática de Service Fabric.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> Los comandos de Service Fabric todavía no están disponibles en la CLI de Azure 2.0.

## <a name="set-up-a-local-cluster"></a>Instalación de un clúster local
Si todo se instaló correctamente, debe poder iniciar un clúster local.

1. Ejecute el script de instalación del clúster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Abra el explorador y navegue hasta http://localhost:19080/Explorer. Si el clúster se ha iniciado, debería ver el panel Service Fabric Explorer.

    ![Service Fabric Explorer en Linux][sfx-linux]

En este punto, puede implementar paquetes de aplicación de Service Fabric precompilados o nuevos basados en contenedores de invitado o archivos ejecutables de invitado. Para compilar nuevos servicios usando los SDK de .NET Core o de Java, siga los pasos de configuración opcionales proporcionados en las siguientes secciones.


> [!NOTE]
> En Linux, no son compatibles tampoco los clústeres independientes: solo se admiten clústeres one box y de varios equipos de Linux de Azure en la versión preliminar.
>
>

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Instalación del SDK de Java y el complemento para Eclipse Neon (opcional)
El SDK de Java proporciona las bibliotecas y plantillas necesarias para compilar servicios de Service Fabric con Java.

1. Instale el paquete del SDK de Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Ejecute el script de instalación del SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Puede instalar el complemento de Eclipse para Service Fabric desde el **IDE de Eclipse para desarrolladores de Java**.

1. En Eclipse, asegúrese de tener instaladas las versiones más recientes de Eclipse **Neon** y Buildship (1.0.17 o versiones posteriores). Puede comprobar las versiones de los componentes instalados en **Help > Installation Details** (Ayuda > Detalles de la instalación). Para actualizar Buildship, siga [estas instrucciones][buildship-update].
2. Para instalar el complemento de Service Fabric, elija **Help > Install New Software...** (Ayuda > Instalar nuevo software...).
3. En el cuadro de texto "Trabajar con", escriba: http://dl.windowsazure.com/eclipse/servicefabric
4. Haga clic en Agregar.
    ![Complemento de Eclipse][sf-eclipse-plugin]
5. Elija el complemento de Service Fabric y haga clic en Siguiente.
6. Lleve a cabo la instalación y acepte el contrato de licencia de usuario final.

Si ya tiene instalado el complemento de Eclipse para Service Fabric, compruebe que se trata de la versión más reciente. Puede comprobar si se puede actualizar aún más acudiendo a: ``Help => Installation Details``. A continuación, busque Service Fabric en la lista de complementos instalados y haga clic en actualizar. Si hay alguna actualización pendiente, se recupera y se instala.

Para más información sobre cómo usar el complemento de Eclipse para Service Fabric para crear, compilar, implementar, actualizar una aplicación de Java de Service Fabric, consulte nuestra guía detallada de [introducción al trabajo en Eclipse con Service Fabric](service-fabric-get-started-eclipse.md).

## <a name="install-the-net-core-sdk-optional"></a>Instalación del SDK de .NET Core (opcional)
El SDK de .NET Core proporciona las bibliotecas y plantillas necesarias para compilar servicios de Service Fabric con .NET Core multiplataforma.

1. Instale el paquete del SDK de .NET Core.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Ejecute el script de instalación del SDK.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>Actualización del SDK y el tiempo de ejecución

Para actualizar a la versión más reciente del SDK y el tiempo de ejecución, ejecute los siguientes pasos (quite los SDK de la lista que no desee actualizar o instalar):

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric, servicefabricsdkcommon, servicefabricsdkcsharp, servicefabricsdkjava
   ```

Para actualizar la CLI, vaya al directorio donde clonó la CLI y ejecute `git pull` para realizar la actualización.

## <a name="next-steps"></a>Pasos siguientes
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Creación de su primera aplicación de CSharp en Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)
* [Uso de la CLI de Azure para administrar las aplicaciones de Service Fabric](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

