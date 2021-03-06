---
title: "Creación de un espacio de nombres de Service Bus mediante Azure Portal | Microsoft Docs"
description: "Cómo crear un espacio de nombres de Service Bus mediante Azure Portal."
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/30/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 4a972b9b8b52a90f27afda98d8bdc661016d1fe1
ms.openlocfilehash: ba7093f8a2c06ab4cecf11207174a4871435ae64
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Creación de un espacio de nombres de Service Bus mediante Azure Portal
Un espacio de nombres es un contenedor común para todos los componentes de mensajería. Varias colas y temas pueden residir en un único espacio de nombres, y los espacios de nombres suelen servir de contenedores de aplicación. Actualmente existen dos formas diferentes de crear espacios de nombres del Bus de servicio.

1. Portal de Azure (este artículo)
2. [Plantillas de Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Creación de un espacio de nombres en Azure Portal
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

¡Enhorabuena! Ha creado un espacio de nombres de mensajería del Bus de servicio.

## <a name="next-steps"></a>Pasos siguientes
Consulte nuestros [ejemplos de GitHub][github-samples] que muestran algunas de las funciones más avanzadas de mensajería de Azure Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

