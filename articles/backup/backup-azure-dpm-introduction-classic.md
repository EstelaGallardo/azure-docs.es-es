---
title: "Copia de seguridad de cargas de trabajo de DPM en el Portal de Azure clásico | Microsoft Docs"
description: "Una introducción a la copia de seguridad de servidores DPM mediante el servicio Copia de seguridad de Azure"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: System Center Data Protection Manager, Data Protection Manager, copia de seguridad de DPM
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2016
ms.author: nkolli;giridham;markgal
translationtype: Human Translation
ms.sourcegitcommit: 2224ddf52283d7da599b1b4842ca617d28b28668
ms.openlocfilehash: f4a5412615e23cc90acc54a9c7430b01fe77dbcf


---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparación para la copia de seguridad de cargas de trabajo en Azure con DPM
> [!div class="op_single_selector"]
> * [Servidor de Copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Servidor de copia de seguridad de Azure (clásico)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (clásico)](backup-azure-dpm-introduction-classic.md)
>
>

En este artículo se proporciona una introducción al uso de Copia de seguridad de Microsoft Azure con el objeto de proteger los servidores y las cargas de trabajo de System Center Data Protection Manager (DPM). Cuando lo lea, comprenderá:

* Cómo funciona la copia de seguridad de servidor DPM de Azure
* Los requisitos previos para lograr una experiencia fluida de la copia de seguridad
* Los errores típicos y cómo resolverlos
* Escenarios admitidos

Datos de aplicación y archivo de copia de seguridad de System Center DMP. Los datos con copia de seguridad en DPM se pueden almacenar en una cinta o en un disco, o se puede crear una copia de seguridad de ellos en Azure con Copia de seguridad de Microsoft Azure. DPM interactúa con Copia de seguridad de Azure de la manera siguiente:

* **DPM implementado como un servidor físico o una máquina virtual local** : si DPM se implementa como un servidor físico o una máquina virtual de Hyper-V local, además de en disco y cinta, puede crear una copia de seguridad de los datos en un almacén de Copia de seguridad de Azure.
* **DPM implementado como una máquina virtual de Azure** : desde System Center 2012 R2 con Update 3, DPM puede implementarse como una máquina virtual de Azure. Si DPM se implementa como una máquina virtual de Azure, puede crear una copia de seguridad de los datos en discos de Azure conectados a la máquina virtual de Azure DPM, o puede descargar el almacenamiento de datos creando una copia de seguridad de ellos en un almacén de Copia de seguridad de Azure.

## <a name="why-backup-your-dpm-servers"></a>¿Por qué crear una copia de seguridad de los servidores DPM?
Algunas de las ventajas empresariales del uso de Copia de seguridad de Azure para la copia de seguridad de servidores DMP son las siguientes:

* Para la implementación de DPM local, puede usar Copia de seguridad de Azure como alternativa a la implementación a largo plazo en una cinta.
* Para las implementaciones de DPM en Azure, Copia de seguridad de Azure le permite descargar el almacenamiento del disco de Azure, de forma que es posible escalar verticalmente gracias a que los datos más antiguos se almacenan en Copia de seguridad de Azure y los nuevos en el disco.

## <a name="how-does-dpm-server-backup-work"></a>Cómo funciona la copia de seguridad de servidor DPM
Para hacer una copia de seguridad de una máquina virtual, primero se necesita una instantánea de los datos en un momento específico. El servicio de Copia de seguridad de Azure inicia el trabajo de copia de seguridad a la hora programada y desencadena la extensión para tomar una instantánea de copia de seguridad. La extensión de copia de seguridad se coordina con el servicio VSS en el invitado para conseguir coherencia y llama a la API de instantánea de blob del servicio de almacenamiento de Azure cuando se alcanza la coherencia. Esto se hace para obtener una instantánea coherente de los discos de la máquina virtual, sin tener que apagarla.

Una vez tomada la instantánea, los datos se transfieren por el servicio de Copia de seguridad de Azure al almacén de copia de seguridad. El servicio se encarga de identificar y transferir solo los bloques que han cambiado desde la última copia de seguridad, lo que hace que el almacenamiento de copias de seguridad y la red sean eficaces. Una vez completada la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación. Este punto de recuperación se puede ver en el Portal de Azure clásico.

> [!NOTE]
> Para las máquinas virtuales de Linux, solo es posible la copia de seguridad coherente con archivos.
>
>

## <a name="prerequisites"></a>Requisitos previos
Prepare Copia de seguridad de Azure para crear copias de seguridad de los datos de DPM de la manera siguiente:

1. **Crear un almacén de copia de seguridad** : cree un almacén en la consola de Copia de seguridad de Azure.
2. **Descargar las credenciales de almacén** : en Copia de seguridad de Azure, cargue el certificado de administración que creó en el almacén.
3. **Instalar Azure Backup Agent y registrar el servidor** : en Copia de seguridad de Azure, instale el agente en cada servidor DPM y registre cada servidor en el almacén de copia de seguridad.

[!INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>Requisitos y limitaciones
* DPM se puede ejecutar como un servidor físico o como una máquina virtual de Hyper-V, instalado en System Center 2012 SP1 o System Center 2012 R2. También se puede ejecutar como una máquina virtual de Azure que ejecuta System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 3 para DPM 2012 R2, o como una máquina virtual de Windows en VMWare que se ejecuta en System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 5.
* Si ejecuta DPM con System Center 2012 SP1, debe instalar el paquete acumulativo de actualizaciones 2 para System Center Data Protection Manager SP1. Esto es necesario para poder instalar Azure Backup Agent.
* El servidor DPM debe tener instalado Windows PowerShell y .Net Framework 4.5.
* DPM puede realizar una copia de seguridad de la mayoría de las cargas de trabajo en Copia de seguridad de Azure. Para obtener una lista completa de compatibilidad, vea a continuación los elementos admitidos en Copia de seguridad de Azure.
* Los datos almacenados en Copia de seguridad de Azure no se pueden recuperar con la opción "copiar en cinta".
* Necesitará una cuenta de Azure con la característica Copia de seguridad de Azure habilitada. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Lea acerca de los [Precios de Backup](https://azure.microsoft.com/pricing/details/backup/).
* El uso de Copia de seguridad de Azure requiere la instalación de Azure Backup Agent en los servidores de los que desee realizar una copia de seguridad. Cada servidor debe tener disponible como espacio de almacenamiento local al menos un 10 % del tamaño de los datos de los que se va a realizar la copia de seguridad. Por ejemplo, realizar una copia de seguridad de 100 GB de datos requiere un mínimo de 10 GB de espacio libre en la ubicación temporal. Aunque el mínimo sea el 10 %, se recomienda dejar libre un 15 % de espacio de almacenamiento local, que se utilizará para la ubicación de caché.
* Los datos se almacenarán en el almacenamiento de almacén de Azure. No hay ningún límite en la cantidad de datos de los que puede realizar una copia de seguridad en el almacén de Copia de seguridad de Azure; sin embargo, el tamaño de un origen de datos (por ejemplo, una máquina virtual o una base de datos) no debe superar los 54 400 GB.

Los siguientes tipos de archivo se admiten para la copia de seguridad en Azure:

* Cifrados (solo copias de seguridad completas)
* Comprimidos (copias de seguridad incrementales compatibles)
* Dispersos (copias de seguridad incrementales compatibles)
* Comprimidos y dispersos (tratados como dispersos)

No se admiten los siguientes:

* Servidores de sistemas de archivos que distinguen entre mayúsculas y minúsculas.
* Vínculos físicos (omitidos)
* Puntos de reanálisis (omitidos)
* Cifrados y comprimidos (omitidos)
* Cifrados y dispersos (omitidos)
* Flujo comprimido
* Flujo disperso

> [!NOTE]
> A partir de System Center 2012 DPM con SP1 en adelante, puede realizar una copia de seguridad de las cargas protegidas por DPM en Azure con Copia de seguridad de Microsoft Azure.
>
>



<!--HONumber=Jan17_HO4-->


