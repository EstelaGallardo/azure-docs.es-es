---
title: "Eliminación de asignaciones de usuario o grupo de una aplicación empresarial en la versión preliminar de Azure Active Directory | Microsoft Docs"
description: "Cómo eliminar la asignación del acceso de un usuario o grupo de una aplicación empresarial en Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b38a613a1f93d9c36254f7642655f716e048e9e3


---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Eliminación de asignaciones de usuario o grupo de una aplicación empresarial en la versión preliminar de Azure Active Directory
Es fácil evitar que se asigne acceso a un usuario o grupo a una de sus aplicaciones empresariales en la versión preliminar de Azure Active Directory (Azure AD). [¿Qué es la versión preliminar?](active-directory-preview-explainer.md)  Debe tener los permisos adecuados para administrar la aplicación empresarial. En la versión preliminar actual, debe ser administrador global del directorio.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>¿Cómo se puede eliminar una asignación de grupo o usuario?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Azure Active Directory** en el cuadro de texto y presione **ENTRAR**.
3. En la hoja **Azure Active Directory - *nombreDelDirectorio*** (es decir, la hoja de Azure AD del directorio que está administrando), seleccione **Aplicaciones empresariales**.

    ![Apertura de Enterprise apps (Aplicaciones empresariales)](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. En la hoja **Aplicaciones empresariales**, seleccione **Todas las aplicaciones**. Verá una lista de las aplicaciones que puede administrar.
5. En la hoja **Enterprise applications (Aplicaciones empresariales) - Todas las aplicaciones** , seleccione una aplicación.
6. En la hoja ***appname*** (es decir, la hoja con el nombre de la aplicación seleccionada en el título), seleccione **Usuarios y grupos**.

    ![Selección de usuarios o grupos](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. En la hoja ***appname***** - Asignación de usuarios y grupos**, seleccione uno o varios usuarios o grupos y, luego, seleccione el comando**Quitar**. Confirme la decisión en el símbolo del sistema.

    ![Selección del comando Eliminar](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Pasos siguientes
* [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
* [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Deshabilitar los inicios de sesión de los usuarios de una aplicación empresarial](active-directory-coreapps-disable-app-azure-portal.md)
* [Cambio del nombre o el logotipo de una aplicación empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)



<!--HONumber=Nov16_HO3-->


