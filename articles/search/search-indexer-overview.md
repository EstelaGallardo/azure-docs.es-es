---
title: Indexadores en Azure Search | Microsoft Docs
description: "Rastree Base de datos SQL de Azure, DocumentDB o Almacenamiento de Azure para extraer los datos utilizables en búsquedas y rellenar un índice de Búsqueda de Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 4bcd31a200024a182ee3d5a21bcbcb621fed595f
ms.openlocfilehash: fd46641709d260f8b468556972aae14205fdb515

---

# <a name="indexers-in-azure-search"></a>Indexadores de Búsqueda de Azure
> [!div class="op_single_selector"]
>
> * [Información general](search-indexer-overview.md)
> * [Portal](search-import-data-portal.md)
> * [SQL de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [DocumentDB](search-howto-index-documentdb.md)
> * [Almacenamiento de blobs (vista previa)](search-howto-indexing-azure-blob-storage.md)
> * [Indexing Azure Table Storage with Azure Search (Indexación de Almacenamiento de tablas de Azure con Búsqueda de Azure)](search-howto-indexing-azure-tables.md)
>
>

Un **indexador** de Búsqueda de Azure es un rastreador (crawler) que extrae datos y metadatos utilizables en búsquedas de un origen de datos externo y rellena un índice basado en las asignaciones de un campo a otro entre el índice y su origen de datos. Este enfoque se denomina a veces "modelo de extracción", porque el servicio extrae datos sin que sea preciso escribir código que inserte datos en un índice.

Puede utilizar un indexador como único medio para la ingesta de datos. o bien utilizar una combinación de técnicas que incluyan el uso de un indexador para cargar solo algunos de los campos en el índice.

Puede ejecutar los indexadores a petición o con una programación de actualización periódica de datos que se ejecuta con una frecuencia de incluso cada quince minutos. Actualizaciones más frecuentes requieren un modelo de inserción que actualiza simultáneamente los datos en Búsqueda de Azure y su origen de datos externo.

## <a name="approaches-for-creating-and-managing-indexers"></a>Enfoques para crear y administrar indexadores
Para los indexadores disponibles generalmente, como SQL Azure o DocumentDB, puede crearlos y administrarlos mediante estos enfoques:

* [Portal > Asistente para la importación de datos ](search-get-started-portal.md)
* [API de REST de servicio](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

## <a name="basic-configuration-steps"></a>Pasos básicos de configuración
Los indexadores pueden ofrecer características que son exclusivas del origen de datos. En este sentido, algunos aspectos de la configuración de orígenes de datos o indexadores varían según el tipo de indexador. No obstante, todos los indexadores comparten composición básica y requisitos. Más adelante, se explican los pasos que son comunes a todos los indexadores.

### <a name="step-1-create-an-index"></a>Paso 1: Creación de un índice
Un indexador automatizará algunas tareas relacionadas con la ingesta de datos, pero la creación de un índice no es una de ellas. Como requisito previo, debe tener un índice predefinido con campos que coincidan con los del origen de datos externo. Para más información sobre la estructura de un índice, consulte [Crear índice (API de REST de servicios de búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### <a name="step-2-create-a-data-source"></a>Paso 2: Creación de un origen de datos
Un indexador extrae datos de un **origen de datos** que contiene información, por ejemplo, una cadena de conexión. Actualmente se admiten los siguientes orígenes de datos:

* [Base de datos SQL de Azure o SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [DocumentDB](search-howto-index-documentdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md), que se usa para extraer texto de PDF, documentos de Office, HTML o XML
* [Almacenamiento de tablas de Azure](search-howto-indexing-azure-tables.md)

Los orígenes de datos se configuran y administran independientemente de los indexadores que los usan, lo que significa que varios indexadores pueden usar un origen de datos para cargar más de un índice a la vez.

### <a name="step-3create-and-schedule-the-indexer"></a>Paso 3: Creación y programación del indexador
La definición del indexador es una construcción que especifica el índice, el origen de datos y una programación. Un indexador puede hacer referencia a un origen de datos desde otro servicio, siempre que ese origen de datos pertenezca a la misma suscripción. Para más información sobre la estructura de un indexador, consulte [Crear el indizador (API de REST de servicios de búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene el concepto básico, el paso siguiente consiste en revisar los requisitos y las tareas específicos de cada tipo de origen de datos.

* [Base de datos SQL de Azure o SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [DocumentDB](search-howto-index-documentdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md), que se usa para extraer texto de PDF, documentos de Office, HTML o XML
* [Almacenamiento de tablas de Azure](search-howto-indexing-azure-tables.md)
* [Indexación de blobs CSV con el indexador de blobs de Búsqueda de Azure (versión preliminar)](search-howto-index-csv-blobs.md)
* [Indexación de blobs JSON con el indexador de blobs de Búsqueda de Azure (versión preliminar)](search-howto-index-json-blobs.md)



<!--HONumber=Jan17_HO3-->


