---
title: "Planeamiento de la capacidad y el escalado para la replicación de VMware en Azure | Microsoft Docs"
description: "Use este artículo para planear la capacidad y escala cuando replique VM de VMware en Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/05/2017
ms.author: rayne
translationtype: Human Translation
ms.sourcegitcommit: 993449b7840f5077f23b3809439b89f27759e35d
ms.openlocfilehash: 1a991d1e4ac20019695fb557310e1981b5b491ec
ms.lasthandoff: 03/01/2017


---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Planeamiento de la capacidad y el escalado para la replicación de VMware con Azure Site Recovery

Use este artículo para averiguar cómo planear la capacidad y el escalado cuando se replican VM de VMware locales y servidores físicos en Azure con [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>¿Cómo se puede iniciar el planeamiento de la capacidad?

Recopile información sobre el entorno de replicación mediante [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc). Esto incluye información sobre el número de máquinas virtuales que son compatibles e incompatibles, discos por máquina virtual, actividad de datos por disco, requisito de ancho de banda de red y la infraestructura de Azure necesaria para una replicación y conmutación por error de pruebas/conmutación por error correctas.

## <a name="capacity-considerations"></a>Consideraciones de capacidad

**Componente** | **Detalles** |
--- | --- | ---
**Replicación** | **Tasa máxima de cambios diaria**: una máquina protegida solo puede usar un servidor de procesos, y un servidor de procesos puede gestionar una tasa de cambios diaria de hasta 2 TB. Por lo tanto, la tasa máxima de cambios diaria que admite una máquina protegida es de 2 TB.<br/><br/> **Rendimiento máximo**: una máquina replicada puede pertenecer a una cuenta de almacenamiento en Azure. Una cuenta de almacenamiento estándar puede controlar un máximo de 20 000 solicitudes por segundo y se recomienda mantener la cantidad de E/S por segundo en una máquina de origen en 20 000. Por ejemplo, si tiene una máquina de origen con 5 discos y cada disco genera 120 E/S por segundo (8 K de tamaño) en el origen, se encontrará dentro del límite de 500 de Azure por E/S por segundo por disco. La cantidad de cuentas de almacenamiento necesarias = E/S por segundo de origen total/20 000.
**Servidor de configuración** | El servidor de configuración debe poder controlar la capacidad de tasa de cambios diaria en todas las cargas de trabajo que se ejecutan en máquinas protegidas, y necesita el ancho de banda suficiente para realizar una replicación continua de los datos en el almacenamiento de Azure.<br/><br/> Como procedimiento recomendado, se sugiere que el servidor de configuración se encuentre en la misma red y mismo segmento de LAN que las máquinas que desea proteger. De todos modos, puede estar en una red distinta, pero las máquinas que desea proteger deben contar con la visibilidad de red L3 en ella.<br/><br/> En la tabla siguiente resumen las recomendaciones de tamaño para el servidor de configuración.
**Servidor de proceso** | El primer servidor de procesos está instalado en el servidor de configuración de forma predeterminada. Puede implementar servidores de procesos adicionales para escalar el entorno. Observe lo siguiente:<br/><br/> El servidor de procesos recibe datos de replicación provenientes de las máquinas protegidas y los optimiza con almacenamiento en caché, compresión y cifrado antes de enviarlos a Azure. La máquina del servidor de procesos debe tener los recursos suficientes para realizar estas tareas.<br/><br/> El servidor de procesos utiliza la memoria caché basada en disco. Se recomienda tener un disco de caché independiente con 600 GB o más de capacidad para controlar los cambios en los datos almacenados ante la eventualidad de una interrupción o un cuello de botella en la red.

## <a name="size-recommendations-for-the-configuration-server"></a>Recomendaciones de tamaño del servidor de configuración

**CPU** | **Memoria** | **Tamaño del disco de caché** | **Frecuencia de cambio de datos** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz) | 16 GB | <&300; GB | 500 GB o menos | Replicar menos de 100 máquinas.
12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Replicar entre 100 y 150 máquinas.
16 vCPU (2 sockets * 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Replicar entre 150 y 200 máquinas.
Implementar otro servidor de procesos | | | 2 TB | Implemente servidores de procesos adicionales si replica más de 200 máquinas o si la tasa de cambios de datos diaria supera los 2 TB.

Donde:

* Cada máquina de origen está configurada con 3 discos de 100 GB cada una.
* Usamos almacenamiento de pruebas comparativas de 8 unidades SAS de 10 K RPM con RAID 10 para las mediciones de disco de caché.

## <a name="size-recommendations-for-the-process-server"></a>Recomendaciones de tamaño para el servidor de procesos

Si necesita proteger más de 200 máquinas, o si la tasa de cambios diaria es mayor que 2 TB, puede agregar servidores de procesos adicionales para controlar la carga de replicación. Para escalar horizontalmente, puede:

* Aumentar el número de servidores de configuración. Por ejemplo, puede proteger hasta 400 máquinas con dos servidores de configuración.
* Agregar servidores de procesos adicionales y utilizarlos para controlar el tráfico en lugar (o además) del servidor de configuración.

Esta tabla describe un escenario en el cual:

* No pretende utilizar el servidor de configuración como un servidor de procesos.
* Ha configurado un servidor de procesos adicional.
* Ha configurado máquinas virtuales protegidas para utilizar el servidor de procesos adicional.
* Cada máquina de origen protegida está configurada con tres discos de 100 GB cada uno.

**Servidor de configuración** | **Servidores de procesos adicionales** | **Tamaño del disco de caché** | **Frecuencia de cambio de datos** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memoria | 4 vCPU (2 sockets * 2 núcleos @ 2,5 GHz), 8 GB de memoria | <&300; GB | 250 GB o menos | Replicar 85 máquinas o menos.
8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memoria | 8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz), 12 GB de memoria | 600 GB | 250 GB a 1 TB | Replicar entre 85 y 150 máquinas.
12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz), 18 GB de memoria | 12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz), 24 GB de memoria | 1 TB | 1 TB a 2 TB | Replicar entre 150 y 225 máquinas.

La manera en la que escalará los servidores depende de su preferencia con respecto a un modelo de escalado vertical u horizontal.  Puede escalar verticalmente con la implementación de algunos servidores de procesos y de configuración de alto nivel, mientras que, para escalar horizontalmente, debe implementar más servidores con menos recursos. Por ejemplo, si necesita proteger 220 máquinas, podría elegir una de las siguientes opciones:

* Configure el servidor de configuración con 12 vCPU y 18 GB de memoria, un servidor de procesos adicional con 12 vCPU y 24 GB de memoria y, además, configure las máquinas protegidas para que solo utilicen el servidor de procesos adicional.
* O bien, podría configurar dos servidores de configuración (2 con 8 vCPU y 16 GB de RAM) y dos servidores de procesos adicionales (1 con 8 vCPU y 1 con 4 vCPU para controlar 135 + 85 (220) máquinas) y configurar las máquinas protegidas para que solo utilicen los servidores de procesos adicionales.


## <a name="control-network-bandwidth"></a>Ancho de banda de red de control

Puede utilizar [la herramienta de planeación de implementación](https://aka.ms/asr-deployment-planner-doc) para calcular el ancho de banda necesario para la replicación (replicación inicial y, a continuación, la diferencial). Para controlar el uso de ancho de banda de la replicación tiene varias opciones:

* **Limitar ancho de banda**: el tráfico de VMware que se replica en Azure pasa a través de un servidor de procesos específico. También puede limitar el ancho de banda en las máquinas que se ejecutan como servidores de procesos.
* **Influir en el ancho de banda**: puede influir en el ancho de banda utilizado para la replicación mediante un par de claves del Registro:
  * El valor del Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** especifica el número de subprocesos que se utilizan para la transferencia de datos (replicación inicial o diferencial) de un disco. Un valor mayor aumenta el ancho de banda de red utilizado para la replicación.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** especifica el número de subprocesos usados para la transferencia de datos durante la conmutación por recuperación.

### <a name="throttle-bandwidth"></a>Limitar ancho de banda
1. Abra el complemento MMC de Copia de seguridad de Microsoft Azure en la máquina que actúa como el servidor de procesos. De manera predeterminada, hay disponible un acceso directo para Microsoft Azure Backup en el escritorio o en la siguiente ruta de acceso: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.

    ![Limitar ancho de banda](./media/site-recovery-vmware-to-azure/throttle1.png)
3. En la pestaña **Limitación**, seleccione **Habilitar límite de uso del ancho de banda de Internet para las operaciones de copia de seguridad** y defina los límites durante las horas de trabajo y fuera de las horas de trabajo. Los intervalos válidos van de 512 Kbps a 102 Mbps por segundo.

    ![Limitar ancho de banda](./media/site-recovery-vmware-to-azure/throttle2.png)

También puede utilizar el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para establecer la limitación. Aquí tiene un ejemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que no se requiere ninguna limitación.

#### <a name="influence-network-bandwidth-for-a-vm"></a>Control del uso de ancho de banda de red para una VM

1. En el Registro de la VM, vaya a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para influir en el tráfico de ancho de banda en un disco de replicación, modifique el valor de **UploadThreadsPerVM**o cree la clave en caso de que no exista.
   * Para influir en el ancho de banda para el tráfico de conmutación por recuperación de Azure, modifique el valor **DownloadThreadsPerVM**.
2. El valor predeterminado es 4. En una red "sobreaprovisionada", se deben cambiar los valores predeterminados de estas claves de registro. El valor máximo es 32. Supervise el tráfico para optimizar el valor.


## <a name="deploy-additional-process-servers"></a>Implementar servidores de procesos adicionales

Si debe escalar horizontalmente la implementación a más de 200 máquinas de origen o si la tasa de renovación diaria total supera los 2 TB, necesitará servidores de procesos adicionales para controlar el volumen del tráfico. Siga estas instrucciones para configurar el servidor de procesos. Después de configurar el servidor, debe migrar las máquinas de origen para que lo utilicen.

1. En **Servidores de Site Recovery**, haga clic en el servidor de configuración > **Servidor de procesos**.

    ![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. En **Tipo de servidor**, haga clic en **Process server (on-premises)** (Servidor de procesos [local]).

    ![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Descargue el archivo de instalación unificada de Site Recovery y ejecútelo para instalar el servidor de procesos y registrarlo en el almacén.
4. En **Antes de comenzar**, seleccione **Add additional process servers to scale out deployment** (Agregar servidores de procesos adicionales para el escalado horizontal de la implementación).
5. Complete el asistente tal y como hizo cuando [instaló](#step-2-set-up-the-source-environment) el servidor de configuración.

    ![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. En **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP del servidor de configuración y la frase de contraseña. Para obtener la frase de contraseña, ejecute **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** en el servidor de configuración.

    ![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para utilizar el nuevo servidor de procesos
1. En **Configuración** > **Site Recovery servers** (Servidores de Site Recovery), haga clic en el servidor de configuración y expanda **Servidores de procesos**.

    ![Actualizar servidor de procesos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Haga clic con el botón derecho en el servidor de procesos que se utiliza actualmente y haga clic en **Cambiar**.

    ![Actualizar servidor de procesos](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. En **Seleccionar servidor de procesos de destino**, elija el nuevo servidor de procesos que desea usar y elija las máquinas virtuales que controlará el nuevo servidor de procesos. Haga clic en el icono de información para obtener detalles sobre el servidor. Aparece el espacio promedio que se necesita para replicar cada máquina virtual seleccionada en el nuevo servidor de procesos para ayudarlo a tomar decisiones relacionadas con la carga. Haga clic en la marca de verificación para comenzar a replicar en el nuevo servidor de procesos.








