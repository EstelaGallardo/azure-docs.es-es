---
title: Tutorial de la infraestructura de Azure de ejemplo | Microsoft Docs
description: "Obtenga información sobre las directrices clave de diseño e implementación para implementar una infraestructura de ejemplo en Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 37e1783e10bf1cf84431a8d40b1e3d81b47188d2
ms.lasthandoff: 03/03/2017


---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Tutorial de la infraestructura de Azure de ejemplo para máquinas virtuales Linux

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artículo le guía a través de la creación de una infraestructura de aplicación de ejemplo. Detallaremos el diseño de una infraestructura para una tienda en línea sencilla que reúna todas las directrices y decisiones relacionadas con las convenciones de nomenclatura, los conjuntos de disponibilidad, las redes virtuales, los equilibradores de carga y, realmente, la implementación de sus máquinas virtuales (VM).

## <a name="example-workload"></a>Carga de trabajo de ejemplo
Adventure Works Cycles desea crear una aplicación de la tienda en línea en Azure que conste de:

* Dos servidores nginx que ejecuten el cliente front-end en un nivel web
* Dos servidores nginx que procesen datos y pedidos en un nivel de aplicación
* Dos servidores MongoDB parte de un clúster particionado para almacenar pedidos y datos de productos en un nivel de base de datos
* Dos controladores de dominio de Active Directory para los proveedores y las cuentas de cliente en un nivel de autenticación
* Todos los servidores se encuentran en dos subredes:
  * Una subred front-end para los servidores web 
  * Una subred back-end para los servidores de aplicaciones, el clúster de MongoDB y los controladores de dominio

![Diagrama de distintos niveles de infraestructura de aplicaciones](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

El tráfico web seguro entrante debe ser de carga equilibrada entre los servidores web a medida que los clientes examinan la tienda en línea. El tráfico de procesamiento de pedidos en forma de solicitudes HTTP procedente de los servidores web debe equilibrarse entre los servidores de aplicaciones. Además, la infraestructura debe diseñarse para alta disponibilidad.

El diseño resultante incluirá:

* Una suscripción y una cuenta de Azure
* Un único grupo de recursos
* Cuentas de almacenamiento
* Una red virtual con dos subredes
* Conjuntos de disponibilidad para las máquinas virtuales con un rol similar
* Máquinas virtuales

Todo lo anterior seguirá estas convenciones de nomenclatura:

* Adventure Works Cycles usa **[carga de trabajo de TI]-[ubicación]-[recurso de Azure]** como prefijo
  * En este ejemplo, "**azos**" (siglas en inglés de "tienda en línea de Azure") es el nombre de la carga de trabajo de TI y "**use**" (siglas en inglés de "este de EE. UU. 2") es la ubicación.
* Las cuentas de almacenamiento usan adventureazosusesa**[descripción]**
  * Se agregó adventure al prefijo para proporcionar exclusividad y que los nombres de cuentas de almacenamiento no admiten el uso de guiones.
* Las redes virtuales usan AZOS-USE-VN**[número]**
* Los conjuntos de disponibilidad usan azos-use-as-**[rol]**
* Los nombres de máquinas virtuales usan azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Suscripciones y cuentas de Azure
Adventure Works Cycles usa la suscripción Enterprise, denominada Adventure Works Enterprise Subscription, para proporcionar la facturación de esta carga de trabajo de TI.

## <a name="storage-accounts"></a>Cuentas de almacenamiento
Adventure Works Cycles determinó que necesitaba dos cuentas de almacenamiento:

* **adventureazosusesawebapp** para el almacenamiento estándar de los servidores web, los servidores de aplicaciones y los controladores de dominio y sus discos de datos.
* **adventureazosusesadbclust** para el almacenamiento premium de los servidores de clústeres particionados MongoDB y sus discos de datos.

## <a name="virtual-network-and-subnets"></a>Red virtual y subredes
Dado que la red virtual no necesita una conectividad continua con la red local de Adventure Work Cycles, la empresa optó por una red virtual solo en la nube.

Creó una red virtual solo en la nube con la siguiente configuración a través del Portal de Azure:

* Nombre: AZOS-USE-VN01
* Ubicación: Este de EE. UU. 2
* Espacio de direcciones de red virtual: 10.0.0.0/8
* Primera subred:
  * Nombre: FrontEnd
  * Espacio de direcciones: 10.0.1.0/24
* Segunda subred:
  * Nombre: BackEnd
  * Espacio de direcciones: 10.0.2.0/24

## <a name="availability-sets"></a>Conjuntos de disponibilidad
Para mantener la alta disponibilidad de los cuatro niveles de su tienda en línea, Adventure Works Cycles optó por cuatro conjuntos de disponibilidad:

* **azos-use-as-web** para los servidores web
* **azos-use-as-app** para los servidores de aplicaciones
* **azos-use-as-db** para los servidores del clúster particionado de MongoDB
* **azos-use-as-dc** para los controladores de dominio

## <a name="virtual-machines"></a>Máquinas virtuales
Adventure Works Cycles decidió los siguientes nombres para sus máquinas virtuales de Azure:

* **azos-use-vm-web01** para el primer servidor web
* **azos-use-vm-web02** para el segundo servidor web
* **azos-use-vm-app01** para el primer servidor de aplicaciones
* **azos-use-vm-app02** para el segundo servidor de aplicaciones
* **azos-use-vm-db01** para el primer servidor MongoDB en el clúster
* **azos-use-vm-db02** para el segundo servidor MongoDB en el clúster
* **azos-use-vm-dc01** para el primer controlador de dominio
* **azos-use-vm-dc02** para el segundo controlador de dominio

Aquí está la configuración resultante.

![Infraestructura de aplicaciones final implementada en Azure](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

Esta configuración incluye:

* Una red virtual solo en la nube con dos subredes (FrontEnd y BackEnd)
* Dos cuentas de almacenamiento
* Cuatro conjuntos de disponibilidad, uno para cada nivel de la tienda en línea
* Las máquinas virtuales para los cuatro niveles
* Un conjunto externo de carga equilibrada para el tráfico web basado en HTTPS de Internet a los servidores web
* Un conjunto interno de carga equilibrada para el tráfico web sin cifrar de los servidores web a los servidores de aplicaciones
* Un único grupo de recursos

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


