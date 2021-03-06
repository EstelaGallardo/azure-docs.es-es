---
title: Acceso a nubes privadas de Azure con Visual Studio | Microsoft Docs
description: "Obtenga información sobre cómo tener acceso a recursos de nube privada mediante Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26fea09810e4a5ed1dc19123a5354905ec3e37ea


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acceso a nubes privadas de Azure con Visual Studio
## <a name="overview"></a>Información general
De forma predeterminada, Visual Studio admite extremos REST de nube pública de Azure. Aunque esto puede ser un problema si se usa Visual Studio en una nube privada de Azure. Puede usar certificados para configurar Visual Studio para tener acceso a extremos REST de nube privada de Azure. Puede obtener estos certificados a través del archivo de configuración de publicación de Azure.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Para obtener acceso a una nube privada de Azure en Visual Studio
1. En el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885) de la nube privada, descargue el archivo de configuración de publicación o póngase en contacto con su administrador para obtenerlo. En la versión pública de Azure, el vínculo para descargarlo es [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (El archivo que descargue debe tener una extensión .publishsettings).
2. En el **Explorador de servidores** en Visual Studio, elija el nodo **Azure** y, en el menú contextual, elija el comando **Administrar suscripciones**.
   
    ![Comando Administrar suscripciones](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)
3. En el cuadro de diálogo **Administrar suscripciones de Microsoft Azure**, elija la pestaña **Certificados** y luego el botón **Importar**.
   
    ![Importación de certificados de Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)
4. En el cuadro de diálogo **Importar suscripciones de Microsoft Azure**, vaya a la carpeta en la que guardó el archivo de configuración de publicación y elija el botón **Importar**. Con ello importa los certificados del archivo de configuración de publicación a Visual Studio. Ahora debe poder interactuar con los recursos de nube privada.
   
    ![Importación de la configuración de publicación](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Pasos siguientes
[Publicación en un servicio en la nube de Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Descarga e importación de la configuración de publicación y la información de suscripción](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)




<!--HONumber=Nov16_HO3-->


