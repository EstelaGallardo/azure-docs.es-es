---
title: "Compilación de línea de comandos de Azure | Microsoft Docs"
description: "Compilación de línea de comandos de Azure"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e189bf8241266443bee4dbbb5faf1cffa983bdb4


---
# <a name="command-line-build-for-azure"></a>Compilación de línea de comandos de Azure
## <a name="overview"></a>Información general
Puede crear un paquete de implementación de Azure ejecutando MSBuild en un símbolo del sistema. Puede configurar y definir compilaciones de depuración, ensayo y producción, además de automatizar algunos de los procesos de compilación.

## <a name="microsoft-build-engine-msbuild"></a>Microsoft Build Engine (MSBuild)
Con Microsoft Build Engine (MSBuild) puede compilar productos en entornos de laboratorio de compilación en los que Visual Studio no está instalado. MSBuild utiliza un formato XML para archivos de proyecto que es ampliable y totalmente compatible con Microsoft. En este formato de archivo, puede describir los elementos que deben crearse en una o más plataformas y configuraciones.

También puede ejecutar MSBuild en un símbolo del sistema, enfoque que se describe en este tema. Estableciendo propiedades en un símbolo del sistema, puede crear configuraciones específicas de un proyecto. De forma similar, también puede definir los destinos que creará el comando MSBuild. Para obtener más información acerca de los parámetros de línea de comandos y MSBuild, consulte [Referencia de la línea de comandos de MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="installation"></a>Instalación
Tal y como se describe en los procedimientos anteriores, debe instalar el software y las herramientas en el servidor de compilación para poder crear un paquete de Azure con MSBuild.

1. Instale .NET Framework 4 o posterior, que incluye MSBuild.
2. Instale las [Herramientas de creación de Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (busque MicrosoftAzureAuthoringTools-x64.msi o MicrosoftAzureAuthoringTools-x86.msi).
3. Instale las [Bibliotecas de Azure para .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (busque MicrosoftAzureLibsForNet-x64.msi o MicrosoftAzureLibs-x86.msi).
4. Copie el archivo Microsoft.WebApplication.targets desde una instalación de Visual Studio en otro equipo.
   
    El archivo se encuentra en el directorio C:\Archivos de programa (x86)\MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 para Visual Studio 2012), y debe copiarlo en el mismo directorio en que se encuentra el servidor de compilación.
5. Instale las [Herramientas de Azure para Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).
   
    Busque WindowsAzureTools.vs120.exe para compilar proyectos de Visual Studio 2013.

## <a name="msbuild-parameters"></a>Parámetros de MSBuild
La manera más sencilla de crear un paquete consiste en ejecutar MSBuild con la opción `/t:Publish` . De forma predeterminada, este comando crea un directorio con respecto a la carpeta raíz para el proyecto, como ProjectDir\bin\Configuration\app.publish\. Al compilar un proyecto de Azure, se generan dos archivos, el propio archivo de paquete y el archivo de configuración complementario:

* Project.cspkg
* ServiceConfiguration.TargetProfile.cscfg

De manera predeterminada, cada proyecto de Azure incluye un archivo de configuración del servicio para las compilaciones locales (depuración) y otro para las compilaciones en la nube (ensayo o producción), pero puede agregar o quitar archivos de configuración del servicio según sea necesario. Cuando compila un paquete dentro de Visual Studio, se le preguntará cuál archivo de configuración del servicio se debe incluir junto con el paquete. Cuando compila un paquete con MSBuild, el archivo de configuración del servicio local se incluye de manera predeterminada. Para incluir un archivo de configuración del servicio diferente, establezca la propiedad `TargetProfile` del comando de MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Si desea utilizar un directorio alternativo para el paquete y los archivos de configuración almacenados, establezca la ruta de acceso mediante la opción `/p:PublishDir=Directory\`, incluido el separador de barra diagonal inversa final.

## <a name="deployment"></a>Implementación
Una vez compilado el paquete, puede implementarlo en Azure. Para obtener un tutorial que muestra ese proceso, consulte el sitio web de Azure. Para obtener información sobre cómo automatizar el proceso, consulte [Entrega continua para Cloud Services de Azure](cloud-services/cloud-services-dotnet-continuous-delivery.md).




<!--HONumber=Nov16_HO3-->


