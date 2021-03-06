---
title: Implementar trabajos web con Visual Studio
description: "Obtenga información sobre cómo implementar trabajos web de Azure en aplicaciones web del servicio de aplicaciones de Azure con Visual Studio."
services: app-service
documentationcenter: 
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: fcbd9e10e4cc336dc6ea37f84201249e14b1af91
ms.openlocfilehash: 9f792f6ea082461f3304516fc9b4c3273e2f50b8
ms.lasthandoff: 02/16/2017


---
# <a name="deploy-webjobs-using-visual-studio"></a>Implementar trabajos web con Visual Studio
## <a name="overview"></a>Información general
En este tema se explica cómo usar Visual Studio para implementar un proyecto de aplicación de consola en una aplicación web en [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) como [WebJob de Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Para información sobre cómo implementar WebJobs con [Azure Portal](https://portal.azure.com), consulte [Ejecución de tareas en segundo plano con WebJobs](web-sites-create-web-jobs.md).

Cuando Visual Studio implementa un proyecto de aplicación de consola con funcionalidad WebJobs, realiza dos tareas:

* Copia archivos de tiempo de ejecución a la carpeta apropiada en la aplicación web (*App_Data/jobs/continuous* para WebJobs continuos, *App_Data/jobs/triggered* para WebJobs programados y a petición).
* Configura [trabajos del Programador de Azure](#scheduler) para WebJobs que se programan para ejecutarse en momentos concretos. (Esto no se necesita para WebJobs continuos.)

Un proyecto con funcionalidad WebJobs tiene los siguientes elementos agregados:

* El paquete de NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Un archivo [webjob-publish-settings.json](#publishsettings) que contiene configuración de implementación y del programador. 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Puede agregar estos elementos a un proyecto de aplicación de consola existente o usar una plantilla para crear un nuevo proyecto de aplicación de consola con funcionalidad WebJobs. 

Puede implementar un proyecto como un WebJob por sí mismo o vincularlo a un proyecto web que se implemente automáticamente siempre que implemente el proyecto web. Para vincular proyectos, Visual Studio incluye el nombre del proyecto con funcionalidad WebJob en un archivo [webjobs-list.json](#webjobslist) en el proyecto web.

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Requisitos previos
Las características de implementación de WebJobs están disponibles en Visual Studio 2015 al instalar el SDK de Azure para .NET:

* [SDK de Azure para .NET (Visual Studio 2015)](http://go.microsoft.com/fwlink/?linkid=518003)

## <a id="convert"></a>Activación de la implementación de trabajos web para un proyecto de Aplicación de consola existente
Tiene dos opciones:

* [Activación de la implementación automática con un proyecto web](#convertlink).
  
    Configure un proyecto de aplicación de consola existente de forma que se implemente automáticamente como un WebJob cuando implemente un proyecto web. Utilice esta opción cuando desee ejecutar su trabajo web en la misma aplicación web en la que ejecuta la aplicación web relacionada.
* [Activación de la implementación sin un proyecto web](#convertnolink).
  
    Configure un proyecto de aplicación de consola existente para implementar como WebJob por sí mismo, sin vínculo a un proyecto web. Use esta opción cuando desee ejecutar un trabajo web en una aplicación web por sí mismo, sin ninguna aplicación web ejecutándose en dicha aplicación web. Puede que desee hacer esto para poder escalar los recursos de su WebJob independientemente de los recursos de la aplicación web.

### <a id="convertlink"></a> Activación de la implementación de WebJobs automática con un proyecto web
1. Haga clic con el botón derecho en el proyecto web en el **Explorador de soluciones** y luego haga clic en **Agregar** > **Proyecto existente como WebJob de Azure**.
   
    ![Proyecto existente como trabajo web de Azure](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    Aparecerá el cuadro de diálogo [Agregar WebJob de Azure](#configure) .
2. En la lista desplegable **Nombre de proyecto** , seleccione el proyecto de aplicación de consola para agregar como WebJob.
   
    ![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Complete el cuadro de diálogo [Agregar WebJob de Azure](#configure) y haga clic en **Aceptar**. 

### <a id="convertnolink"></a> Activación de la implementación de WebJobs sin un proyecto web
1. Haga clic con el botón derecho en el proyecto de aplicación de consola en el **Explorador de soluciones** y haga clic en **Publicar como WebJob de Azure**. 
   
    ![Publicar como WebJob de Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    Aparecerá el cuadro de diálogo [Agregar WebJob de Azure](#configure) , con el proyecto seleccionado en el cuadro **Nombre de proyecto** .
2. Complete el cuadro de diálogo [Agregar WebJob de Azure](#configure) y haga clic en **Aceptar**.
   
   Aparece el asistente de **Publicación web** .  Si no quiere realizar la publicación inmediatamente, cierre el asistente. La configuración que ha escrito se guarda para cuando desee [implementar el proyecto](#deploy).

## <a id="create"></a>Creación de un nuevo proyecto con funcionalidad WebJobs
Para crear un nuevo proyecto con funcionalidad WebJobs, puede usar la plantilla de proyecto de aplicación de consola y habilitar la implementación de WebJobs tal y como se explicó en la [sección anterior](#convert). Como alternativa, puede usar la plantilla para nuevos proyectos WebJobs:

* [Uso de la plantilla para nuevos proyectos de WebJobs para WebJob independiente](#createnolink)
  
    Cree un proyecto y configúrelo para implementarse por sí mismo como WebJob, sin vínculo a un proyecto web. Use esta opción cuando desee ejecutar un trabajo web en una aplicación web por sí mismo, sin ninguna aplicación web ejecutándose en dicha aplicación web. Puede que desee hacer esto para poder escalar los recursos de su WebJob independientemente de los recursos de la aplicación web.
* [Uso de la plantilla para nuevos proyectos de WebJobs para un WebJob vinculado a un proyecto web](#createlink)
  
    Cree un proyecto configurado para implementarse automáticamente como WebJob cuando se implementa un proyecto web en la misma solución. Utilice esta opción cuando desee ejecutar su trabajo web en la misma aplicación web en la que ejecuta la aplicación web relacionada.

> [!NOTE]
> La plantilla new-project de WebJobs instala automáticamente los paquetes NuGet e incluye código en *Program.cs* para el [SDK de WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Si no desea utilizar el SDK de WebJobs, o bien si desea utilizar un trabajo web programado en lugar de continuo, quite o cambie la instrucción `host.RunAndBlock` en *Program.cs*.
> 
> 

### <a id="createnolink"></a> Uso de la plantilla para nuevos proyectos de WebJobs para WebJob independiente
1. Haga clic en **Archivo** > **Nuevo proyecto** y, en el cuadro de diálogo **Nuevo proyecto**, haga clic en **Nube** > **WebJob de Microsoft Azure**.
   
    ![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
2. Siga las instrucciones mostradas anteriormente para [crear el proyecto de aplicación de consola para un proyecto de WebJobs independiente](#convertnolink).

### <a id="createlink"></a> Uso de la plantilla para nuevos proyectos de WebJobs para un WebJob vinculado a un proyecto web
1. Haga clic con el botón derecho en el proyecto web en el **Explorador de soluciones** y haga clic en **Agregar** > **Nuevo proyecto de WebJob de Azure**.
   
    ![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    Aparecerá el cuadro de diálogo [Agregar WebJob de Azure](#configure) .
2. Complete el cuadro de diálogo [Agregar WebJob de Azure](#configure) y haga clic en **Aceptar**.

## <a id="configure"></a>Cuadro de diálogo Agregar WebJob de Azure
El cuadro de diálogo **Agregar WebJob de Azure** le permite especificar el nombre de trabajo web y la configuración de programación para su trabajo web. 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Los campos de este cuadro de diálogo se corresponden con los campos del cuadro de diálogo **Nuevo trabajo** del Portal de Azure. Para obtener más información, consulte [Ejecución de tareas en segundo plano con WebJobs](web-sites-create-web-jobs.md).

Para un trabajo web programado (no un trabajo web continuo), Visual Studio crea una colección de trabajos del [Programador de Azure](/services/scheduler/) si todavía no existe y, a su vez, crea un trabajo en la colección:

* La colección de trabajos del programador se llama *WebJobs-{regionname}* donde *{regionname}* se refiere a la región en la que se hospeda el sitio web. Por ejemplo: Por ejemplo: WebJobs-OesteEE.UU.
* El trabajo del programador se llama *{webappname}-{webjobname}*. Por ejemplo: MyWebJob MyWebApp. 

> [!NOTE]
> * Para obtener información sobre la implementación de línea de comandos, consulte [Activación de la línea de comandos o de la entrega continua de WebJobs de Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Si configura un **trabajo recurrente** y establece la frecuencia de periodicidad en un número de minutos, el servicio Programador de Azure no será gratuito. Otras periodicidades (horas, días, etc.) son gratuitas.
> * Si implementa un WebJob y, a continuación, decide que desea cambiar el tipo de WebJob y volver a implementarlo, deberá eliminar el archivo webjobs-publish-settings.json. Esto hará que Visual Studio muestre de nuevo las opciones de publicación para que pueda cambiar el tipo de WebJob.
> * Si implementa un WebJob y más tarde cambia el modo de ejecución de continuo a no continuo o viceversa, Visual Studio crea un nuevo WebJob en Azure cuando vuelva a implementar. Si cambia otra configuración de programación pero deja el mismo modo de ejecución o cambia entre el modo Programado y Bajo demanda, Visual Studio actualiza el trabajo existente en lugar de crear uno nuevo.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Cuando crea una aplicación de consola para la implementación de WebJobs, Visual Studio instala el paquete de NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) y almacena la información de programación en un archivo *webjob-publish-settings.json* en la carpeta *Properties* del proyecto WebJobs. A continuación se muestra un ejemplo de ese archivo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

Puede editar este archivo directamente y Visual Studio proporciona IntelliSense. El archivo de esquema se almacena en [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) y se puede ver allí.  

> [!NOTE]
> * Si configura un **trabajo recurrente** y establece la frecuencia de periodicidad en un número de minutos, el servicio Programador de Azure no será gratuito. Otras periodicidades (horas, días, etc.) son gratuitas.
> 
> 

## <a id="webjobslist"></a>webjobs-list.json
Cuando vincule un proyecto con funcionalidad WebJobs a un proyecto web, Visual Studio almacenará el nombre del proyecto WebJobs en un archivo *webjobs-list.json* en la carpeta *Properties* del proyecto web. La lista puede contener varios proyectos WebJobs, tal y como se muestra en el siguiente ejemplo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Puede editar este archivo directamente y Visual Studio proporciona IntelliSense. El archivo de esquema se almacena en [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) y se puede ver allí.

## <a id="deploy"></a>Implementación de un proyecto de WebJobs
Un proyecto WebJobs que ha vinculado a un proyecto web se implementa automáticamente con este último. Para obtener información sobre la implementación de proyectos web, consulte [Implementación en aplicaciones web](web-sites-deploy.md).

Para implementar un proyecto de WebJobs por sí mismo, haga clic con el botón derecho en **Explorador de soluciones** y luego haga clic en **Publicar como WebJob de Azure**. 

![Publicar como WebJob de Azure](./media/websites-dotnet-deploy-webjobs/paw.png)

Para un trabajo web independiente, aparece el mismo asistente **Publicación web** que se usa para los proyectos web, pero con menos configuraciones disponibles para cambiar.

## <a id="nextsteps"></a>Pasos siguientes
Este artículo explica cómo implementar WebJobs con Visual Studio. Para más información sobre cómo implementar WebJobs de Azure, consulte [Recursos recomendados de WebJobs de Azure: Implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).


