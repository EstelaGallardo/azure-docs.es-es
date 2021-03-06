---
title: "Preparación para publicar o implementar una aplicación de Azure desde Visual Studio | Microsoft Docs"
description: "Obtenga más información sobre los procedimientos para configurar los servicios de cuenta de almacenamiento y en la nube y establecer la configuración de la aplicación de Azure."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 01623fa76175091439d5a571fb8b8f96aee01c4c
ms.openlocfilehash: 5d747a7c34b340c6f98dabb2c3ff4d3340e74e4a


---
# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Preparación para publicar o implementar una aplicación de Azure desde Visual Studio
## <a name="overview"></a>Información general
Para poder publicar un proyecto de servicio en la nube, debe configurar los siguientes servicios:

* Un **servicio en la nube** para ejecutar sus roles en el entorno de Azure
* Una **cuenta de almacenamiento** que proporcione acceso a los servicios Blob, Cola y Tabla.

Use los siguientes procedimientos para configurar estos servicios y la aplicación

## <a name="create-a-cloud-service"></a>un servicio en la nube
Para publicar un servicio en la nube en Azure, debe crear primero un servicio en la nube, que ejecuta sus roles en el entorno de Azure. Puede crear un servicio en la nube en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885), como se describe en la sección **Para crear un servicio en la nube mediante el Portal de Azure clásico**posteriormente en este tema. También puede crear un servicio en la nube en Visual Studio mediante el asistente para publicación.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Para crear un servicio en la nube mediante Visual Studio
1. Abra el menú contextual del proyecto de Azure y elija **Publicar**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)
2. Si no ha iniciado sesión, inicie sesión con su nombre de usuario y contraseña para la cuenta Microsoft o la cuenta de organización asociada a su suscripción de Azure.
3. Elija el botón **Siguiente** para avanzar a la página **Configuración**.

    ![Configuración común del Asistente para la publicación](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)
4. En la lista **Cloud Services**, elija **Crear nuevo**. Aparecerá el cuadro de diálogo **Crear servicios de Azure** .
5. Escriba el nombre del servicio en la nube. El nombre forma parte de la dirección URL del servicio y, por tanto, debe ser único globalmente. El nombre no distingue mayúsculas de minúsculas.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Para crear un servicio en la nube mediante el Portal de Azure clásico
1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkId=253103) en el sitio web de Microsoft.
2. (opcional) Para mostrar una lista de servicios en la nube que ya creó, elija el vínculo Servicios en la nube situado a la izquierda de la página.
3. Elija el icono **+** situado en la esquina inferior izquierda y, en el menú que aparece, elija **Servicio en la nube**. Aparecerá otra pantalla con dos opciones: **Creación rápida** y **Creación personalizada**. Si elige **Creación rápida**, puede crear un servicio en la nube especificando simplemente su dirección URL y la región en la que se hospedará físicamente. Si elige **Creación personalizada**, puede publicar inmediatamente un servicio en la nube especificando un paquete (archivo .cspkg), un archivo de configuración (.cscfg) y un certificado. No se requiere la creación personalizada si va a publicar el servicio en la nube mediante el comando **Publicar** en un proyecto de Azure. El comando **Publicar** está disponible en el menú contextual para un proyecto de Azure.
4. Elija **Creación rápida** para publicar el servicio en la nube más tarde mediante Visual Studio.
5. Especifique un nombre para el servicio en la nube. La dirección URL completa aparecerá junto al nombre.
6. En la lista, elija la región donde se encuentra la mayoría de los usuarios.
7. En la parte inferior de la ventana, elija el vínculo **Crear servicio en la nube** .

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
Una cuenta de almacenamiento proporciona acceso a los servicios Blob, Cola y Tabla. Puede crear una cuenta de almacenamiento mediante Visual Studio o el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Para crear una cuenta de almacenamiento mediante Visual Studio
1. En el **Explorador de soluciones**, abra el menú contextual del nodo **Almacenamiento** y elija **Crear cuenta de almacenamiento**.

    ![Crear una nueva cuenta de almacenamiento de Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)
2. Seleccione o escriba la siguiente información para la nueva cuenta de almacenamiento en el cuadro de diálogo **Crear cuenta de almacenamiento** .

   * Suscripción de Azure a la que desee agregar la cuenta de almacenamiento.
   * Nombre que quiere usar para la nueva cuenta de almacenamiento.
   * La región o el grupo de afinidad (por ejemplo, Oeste de EE. UU. o Asia oriental).
   * El tipo de replicación que desea usar para la cuenta de almacenamiento, por ejemplo, con redundancia geográfica.
3. Cuando finalice, elija **Crear**. La nueva cuenta de almacenamiento aparecerá en la lista **Almacenamiento** del **Explorador de servidores**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Para crear una cuenta de almacenamiento mediante el Portal de Azure clásico
1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkId=253103) en el sitio web de Microsoft.
2. (Opcional) Para ver las cuentas de almacenamiento, elija el vínculo **Almacenamiento** en el panel situado a la izquierda de la página.
3. En la esquina inferior izquierda de la página, elija el icono **+** .
4. En el menú que aparece, elija **Almacenamiento** y luego **Creación rápida**.
5. Asigne a la cuenta de almacenamiento un nombre que dé como resultado una dirección URL única.
6. Asigne un nombre al servicio en la nube. La dirección URL completa aparecerá junto al nombre.
7. En la lista de regiones, elija la región donde se encuentra la mayoría de los usuarios.
8. Especifique si desea habilitar la replicación geográfica. Si habilita la replicación geográfica, los datos se guardarán en varias ubicaciones físicas para reducir la posibilidad de pérdida. Esta característica hace que el almacenamiento sea más costoso, pero puede reducir el costo habilitando la ubicación geográfica al crear la cuenta de almacenamiento en lugar de agregar la característica más adelante. Para obtener más información, consulte [Replicación geográfica](http://go.microsoft.com/fwlink/?LinkId=253108).
9. En la parte inferior de la ventana, elija el vínculo **Crear cuenta de almacenamiento** .

Después de crear la cuenta de almacenamiento, verá las direcciones URL que puede usar para tener acceso a recursos en cada uno de los servicios de almacenamiento de Azure, además de las claves de acceso principal y secundaria para la cuenta. Use estas claves para autenticar las solicitudes realizadas en los servicios de almacenamiento.

> [!NOTE]
> La clave de acceso secundaria proporciona el mismo acceso a la cuenta de almacenamiento que la clave de acceso principal y se genera como copia de seguridad en caso de que la clave de acceso principal estuviese en peligro. Además, se recomienda volver a generar las claves de acceso de forma periódica. Puede modificar la configuración de una cadena de conexión para que use la clave secundaria mientras se regenera la clave principal y, luego, volver a modificarla para que use la clave principal regenerada mientras se regenera la clave secundaria.
>
>

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Configuración de la aplicación para que use servicios proporcionados por la cuenta de almacenamiento
Debe configurar cualquier rol que tenga acceso a los servicios de almacenamiento para que use los servicios de almacenamiento de Azure que usted cree. Para ello, puede usar varias configuraciones del servicio en el proyecto de Azure. De forma predeterminada, se crean dos en su proyecto de Azure. Al usar varias configuraciones del servicio, puede utilizar la misma cadena de conexión en el código, pero tiene un valor diferente en la cadena de conexión de cada configuración del servicio. Por ejemplo, puede usar una configuración del servicio para ejecutar y depurar la aplicación localmente con el emulador de almacenamiento de Azure y otra configuración del servicio para publicar la aplicación en Azure. Para obtener más información sobre configuraciones del servicio, consulte [Configuración de un proyecto de Azure mediante varias configuraciones del servicio](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Para configurar la aplicación para que use servicios proporcionados por la cuenta de almacenamiento
1. Abra la solución de Azure en Visual Studio. En el Explorador de soluciones, abra el menú contextual de cada rol del proyecto de Azure que acceda a los servicios de almacenamiento y elija **Propiedades**. Se mostrará una página con el nombre del rol en el editor de Visual Studio. La página muestra los campos de la pestaña **Configuración** .
2. En las páginas de propiedades del rol, elija **Configuración**.
3. En la lista **Configuración del servicio** , elija el nombre de la configuración del servicio que quiere editar. Si desea realizar cambios en todas las configuraciones del servicio para este rol, puede elegir **Todas las configuraciones**.  Para más información acerca de cómo actualizar configuraciones del servicio, consulte la sección **Administrar cadenas de conexión para cuentas de almacenamiento** en el tema [Configuración de los roles para un servicio en la nube de Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
4. Para modificar cualquier configuración de la cadena de conexión, elija el botón **...** situado junto a la configuración. Aparecerá el cuadro de diálogo **Crear cadena de conexión de almacenamiento** .
5. En **Conectar utilizando**, elija la opción **Su suscripción**.
6. En la lista de **Suscripción** , elija su suscripción. Si la lista de suscripciones no incluye la que desea, elija el vínculo **Descargar configuración de publicación** .
7. En la lista **Nombre de cuenta** elija el nombre de la cuenta de almacenamiento. Azure Tools obtiene automáticamente las credenciales de la cuenta de almacenamiento mediante el archivo .publishsettings. Para especificar manualmente las credenciales de la cuenta de almacenamiento, elija la opción **Credenciales especificadas manualmente** y continúe con el procedimiento. Puede obtener el nombre de la cuenta de almacenamiento y la clave principal en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885). Si no quiere especificar manualmente la configuración de la cuenta de almacenamiento, elija el botón **Aceptar** para cerrar el cuadro de diálogo.
8. Elija el vínculo de credenciales **Especificar nombre de cuenta de almacenamiento** .
9. Escriba el nombre de la cuenta de almacenamiento en **Nombre de cuenta** .

   > [!NOTE]
   > Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885)y elija el botón **Almacenamiento** . El portal muestra una lista de cuentas de almacenamiento. Si elige una cuenta, se abre una página para ella. Puede copiar el nombre de la cuenta de almacenamiento que aparece en esta página. Si usa una versión anterior del Portal clásico, el nombre de la cuenta de almacenamiento aparece en la vista **Cuentas de almacenamiento** . Para copiar este nombre, resáltelo en la ventana **Propiedades** de esta vista y presione las teclas CTRL+C. Para pegar el nombre en Visual Studio, elija el cuadro de texto **Nombre de cuenta** y presione las teclas CTRL+V.
   >
   >
10. En el cuadro **Clave de cuenta** , escriba la clave principal o cópiela del [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885)y péguela.
     Para copiar esta clave:

    1. En la parte inferior de la página de la cuenta de almacenamiento correspondiente, elija el botón **Administrar claves** .
    2. En la página **Administrar claves de acceso** , seleccione el texto de la clave de acceso principal y presione las teclas CTRL+C.
    3. En Azure Tools, pegue la clave en el cuadro **Clave de cuenta** .
    4. Debe seleccionar una de las siguientes opciones para determinar cómo tendrá acceso el servicio a la cuenta de almacenamiento:

       * **Usar HTTP**. Se trata de la opción estándar. Por ejemplo: `http://<account name>.blob.core.windows.net`.
       * **Usar HTTPS** para una conexión segura. Por ejemplo: `https://<accountname>.blob.core.windows.net`.
       * **Especificar extremos personalizados** para cada uno de los tres servicios. Luego puede escribir estos extremos en el campo para el servicio específico.

         > [!NOTE]
         > Si crea extremos personalizados, puede crear una cadena de conexión más compleja. Cuando se usa este formato de cadena, se pueden especificar extremos de servicio de almacenamiento que incluyan el nombre de dominio personalizado que se registre para la cuenta de almacenamiento con el servicio Blob. También puede conceder acceso solo a los recursos de blob en un contenedor único a través de una firma de acceso compartido. Para obtener más información sobre cómo crear extremos personalizados, consulte [Configuración de las cadenas de conexión de Azure Storage](storage/storage-configure-connection-string.md).
         >
         >
11. Para guardar estos cambios de la cadena de conexión, elija el botón **Aceptar** y luego el botón **Guardar** de la barra de herramientas. Después de guardar estos cambios, puede obtener el valor de esta cadena de conexión en el código mediante [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Al publicar su aplicación en Azure, elija la configuración del servicio que contiene la cuenta de almacenamiento de Azure para la cadena de conexión. Una vez publicada la aplicación, compruebe que funciona según lo previsto con los servicios de almacenamiento de Azure

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de la publicación de aplicaciones en Azure desde Visual Studio, consulte [Publicar un servicio en la nube mediante Azure Tools](vs-azure-tools-publishing-a-cloud-service.md).



<!--HONumber=Dec16_HO2-->


