---
title: Informe de uso sin licencia | Microsoft Docs
description: "El informe de uso sin licencia ayuda a identificar usuarios sin licencia que utilizan características de Azure AD de pago,"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3400d00112b4b66cceef602dba5cb8666e49e0e4
ms.lasthandoff: 12/28/2016


---
# <a name="unlicensed-usage-report"></a>Informe de uso sin licencia
El informe de uso sin licencia ayuda a identificar usuarios sin licencia que utilizan características de Azure AD de pago, lo que permite hacer un mejor uso de las licencias que se han adquirido e identificar en qué momento se necesitan más licencias. 

El informe muestra el uso activo de las características de pago en los últimos 30 días. 

## <a name="report-structure"></a>Estructura del informe
| Nombre de la columna | Descripción |
|:--- |:--- |
| Usuario sin licencia |Nombre del usuario |
| Característica |El nombre de la característica. Por ejemplo: acceso condicional |
| Aplicación a la que se accede |El nombre de la aplicación a la que se accede con la característica. Por ejemplo: Office 365 SharePoint Online |

> [!NOTE]
> Si se ha eliminado una cuenta de usuario, la columna "Usuario sin licencia" se rellenará con un identificador, como 1003000090D8B285
> 
> 

## <a name="conditional-access-feature"></a>Características de acceso condicional
A los usuarios sin licencia se les marcará cuando accedan a un servicio al que se haya aplicado la directiva de acceso condicional, siempre que no tengan una licencia de Azure AD Premium. 

Esto se aplica a las directivas de MFA/ubicación, así como a las directivas de dispositivos que usan Intune.

## <a name="see-also"></a>Consulte también
* [Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access.md)
* [Introducción al acceso condicional a Azure AD](active-directory-conditional-access-azuread-connected-apps.md) 


