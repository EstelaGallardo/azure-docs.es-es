---
title: Carga de un certificado de Azure Management API | Microsoft Docs
description: "Aprenda a cargar el certificado de Management API para el Portal de Azure clásico."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 783390791f27a9bf8ea400403a8b0f866f8c5cee
ms.openlocfilehash: 0f1ae486340270cac5cfdb5d44485d05cb996388


---
# <a name="upload-an-azure-management-api-management-certificate"></a>Carga de un certificado de administración de API de Administración de Azure
Los certificados de administración le permiten autenticar con la API de administración de servicio proporcionada por Azure. Muchos programas y herramientas (como Visual Studio o el SDK de Azure) utilizarán estos certificados para automatizar la configuración y la implementación de varios servicios de Azure. **Solo se aplica al Portal de Azure clásico**.

> [!WARNING]
> Por lo tanto, tenga cuidado. Estos tipos de certificados permiten a quien se autentica con ellos administrar la suscripción a la que están asociados.
>
>

Para obtener [más](cloud-services/cloud-services-certs-create.md#what-are-management-certificates) información acerca de los certificados de Azure (incluida la creación de un certificado autofirmado) si lo necesita.

También puede usar [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) para autenticar el código de cliente para fines de automatización.

## <a name="upload-a-management-certificate"></a>Carga de un certificado de administración
Una vez que tenga un certificado de administración creado, (archivo .cer con solo la clave pública) puede cargarlo en el portal. Cuando el certificado esté disponible en el portal, cualquiera que tenga un certificado que coincida (clave privada) puede conectarse a través de la API de administración y obtener acceso a los recursos de la suscripción asociada.

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com).
2. Asegúrese de seleccionar la suscripción correcta a la que desee asociar un certificado. Presione el texto **Suscripciones** texto en la parte superior derecha del portal.

    ![Configuración](./media/azure-api-management-certs/subscription.png)
3. Una vez que tenga la suscripción correcta seleccionada, presione **Configuración** en el lado izquierdo del portal (es posible que necesite desplazarse hacia abajo).

    ![Configuración](./media/azure-api-management-certs/settings.png)
4. Presione la pestaña **Certificados de administración** .

    ![Configuración](./media/azure-api-management-certs/certificates-tab.png)
5. Presione el botón **Cargar** .

    ![Configuración](./media/azure-api-management-certs/upload.png)
6. Complete la información del cuadro de diálogo y presione la **marca de verificación**de completado.

    ![Configuración](./media/azure-api-management-certs/upload-dialog.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene un certificado de administración asociado a una suscripción, puede conectarse mediante programación (después de haber instalado localmente el certificado correspondiente) a la [API de REST de administración de servicios](https://msdn.microsoft.com/library/azure/mt420159.aspx) y automatizar los distintos recursos de Azure que también están asociados a esa suscripción.



<!--HONumber=Dec16_HO1-->


