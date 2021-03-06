---
title: "Instalación y uso de Giraph en HDInsight (Hadoop) basado en Linux | Microsoft Docs"
description: "Aprenda a instalar Giraph en clústeres de HDInsight basados en Linux mediante acciones de script. Las acciones de script le permiten personalizar el clúster durante la creación; así, puede cambiar la configuración del clúster o instalar utilidades y servicios."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5ec4b964066687b506686709c3dc5ed5b402fbaf
ms.openlocfilehash: 699626f24e6f66f4b149feda5eb292e64d4b6647


---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalar Giraph en clústeres de Hadoop de HDInsight y usar Giraph para procesar gráficos a gran escala

Puede instalar Giraph en cualquier tipo de clúster de Hadoop en HDInsight de Azure usando una **acción de script** para personalizar un clúster.

En este tema aprenderá a instalar Giraph con la acción de script. Una vez haya instalado Giraph, aprenderá a utilizarlo en las aplicaciones más habituales, como procesar gráficos de gran escala.

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="a-namewhatisawhat-is-giraph"></a><a name="whatis"></a>¿Qué es Giraph?

[Apache Giraph](http://giraph.apache.org/) permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con HDInsight de Azure. Los gráficos modelan las relaciones entre los objetos, como las conexiones entre los enrutadores en una red de gran tamaño como Internet, o las relaciones entre personas de las redes sociales (conocidas en ocasiones como gráficos sociales). El procesamiento gráfico le permite razonar sobre las relaciones entre los objetos de un gráfico. En este sentido, le ayuda por ejemplo a:

* Identificar los posibles amigos según las relaciones actuales.

* Identificar la ruta más corta entre dos equipos de una red.

* Calcular la posición de las páginas web.

> [!WARNING]
> Los componentes proporcionados con el clúster de HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft lo ayudará a aislar y resolver problemas relacionados con estos componentes.
> 
> Los componentes personalizados, como Giraph, reciben soporte técnico comercialmente razonable para ayudarlo a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Los proyectos de Apache también tienen sitios de proyecto en [http://apache.org](http://apache.org) (por ejemplo, [Hadoop](http://hadoop.apache.org/)).


## <a name="what-the-script-does"></a>Funcionamiento del script

Este script realiza las acciones siguientes:

* Instala Giraph en `/usr/hdp/current/giraph`

* Copia el archivo `giraph-examples.jar` en el almacenamiento predeterminado (WASB) del clúster: `/example/jars/giraph-examples.jar`

## <a name="a-nameinstallainstall-giraph-using-script-actions"></a><a name="install"></a>Instalación de Giraph mediante acciones de script

En la siguiente ubicación se encuentra disponible un script de ejemplo para instalar Giraph en un clúster de HDInsight.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Esta sección proporciona instrucciones sobre cómo usar el script de ejemplo al crear el clúster mediante el Portal de Azure. 

> [!NOTE]
> También se puede utilizar Azure PowerShell, la CLI de Azure, el SDK de .NET de HDInsight o las plantillas de Azure Resource Manager para aplicar las acciones de script. También puede aplicar acciones de script a clústeres que ya se estén ejecutando. Para obtener más información, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md).

1. Comience a crear un clúster siguiendo los pasos que se describen en [Creación de clústeres de HDInsight basados en Linux](hdinsight-hadoop-create-linux-clusters-portal.md), pero no complete la operación.

2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y proporcione la información siguiente:
   
   * **NOMBRE**: escriba un nombre descriptivo para la acción de script.

   * **URI del SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **PRINCIPAL**: active esta opción.

   * **TRABAJADOR**: deje esta opción desactivada.

   * **ZOOKEEPER**: deje esta opción desactivada.

   * **PARÁMETROS**: deje este campo en blanco.

3. En la parte inferior de **Acciones de scripts**, use el botón **Seleccionar** para guardar la configuración. Por último, use el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración opcional** para guardar la información de configuración opcional.

4. Continúe creando el clúster, tal como se describe en [Creación de clústeres de HDInsight basados en Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="a-nameusegiraphahow-do-i-use-giraph-in-hdinsight"></a><a name="usegiraph"></a>¿Cómo uso Giraph en HDInsight?

Una vez terminada la creación del clúster, siga estos pasos para ejecutar el ejemplo SimpleShortestPathsComputation incluido con Giraph. Así se realiza la implementación de [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) básica para buscar la ruta más corta entre los objetos de un gráfico.

1. Conéctese al clúster de HDInsight con SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para obtener más información sobre el uso de SSH con HDInsight, vea lo siguiente:
   
   * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix, OS X o Windows](hdinsight-hadoop-linux-use-ssh-unix.md)

   * [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Use la siguiente instrucción para crear un nuevo archivo denominado **tiny_graph.txt**:
   
    ```
    nano tiny_graph.txt
    ```
   
    Use el siguiente contenido para este archivo:
   
    ```
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```
   
    Estos datos describen una relación entre los objetos de un gráfico dirigido, mediante el formato `[source_id, source_value,[[dest_id], [edge_value],...]]`. Cada línea representa una relación entre un objeto `source_id` y uno varios objetos `dest_id`. El valor `edge_value` (o peso) se puede considerar como la fuerza o la distancia de la conexión entre `source_id` y `dest\_id`.
   
    Extrapolados, y usando el valor (o peso) como la distancia entre los objetos, los datos anteriores podrían parecerse a estos:
   
    ![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Para guardar el archivo, presione **Ctrl+X**, a continuación, **Y**; finalmente, **Entrar** para aceptar el nombre de archivo.

4. Use la siguiente instrucción para almacenar los datos en el almacenamiento principal del clúster de HDInsight:
   
    ```
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Ejecute el ejemplo SimpleShortestPathsComputation con el siguiente comando:
   
    ```
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```
   
    Los parámetros que se usan con este comando se describen en la tabla siguiente.
   
   | Parámetro | Qué hace |
   | --- | --- |
   | `jar /usr/hdp/current/giraph/giraph-examples.jar` |El archivo jar que contiene los ejemplos. |
   | `org.apache.giraph.GiraphRunner` |La clase que se usa para iniciar los ejemplos. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |El ejemplo que se usa. En este caso, calculará la ruta más corta entre el identificador 1 y los restantes identificadores del gráfico. |
   | `-ca mapred.job.tracker=headnodehost:9010` |El nodo principal del clúster. |
   | `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` |El formato de entrada que se va a usar para los datos de entrada. |
   | `-vip /example/data/tiny_graph.txt` |El archivo de datos de entrada. |
   | `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` |El formato de salida. En este caso, el Id. y el valor como texto sin formato. |
   | `-op /example/output/shortestpaths` |La ubicación de salida. |
   | `-w 2` |El número de trabajados que se usan. En este caso, 2. |
   
    Para obtener más información sobre estos y otros parámetros que se usan en los ejemplos de Giraph, consulte la [guía de inicio rápido de Giraph](http://giraph.apache.org/quick_start.html).

6. Una vez finalizado el trabajo, los resultados se almacenarán en dos archivos de salida en el directorio **wasb:///example/out/shotestpaths**. Los nombres de los archivos de salida empezarán por **part-m-** y terminarán en un número que indica el primer, segundo, etc., archivo. Use la siguiente instrucción para ver la salida:
   
    ```
    hdfs dfs -text /example/output/shortestpaths/*
    ```
   
    La salida debe ser similar a la siguiente:
   
        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0
   
    El ejemplo SimpleShortestPathComputation se ha codificado de forma rígida para comenzar por el identificador de objeto 1 y encontrar la ruta más corta a los demás objetos. Así que la salida se debe leer como `destination_id distance`, donde la distancia es el valor (o peso) de los bordes recorridos entre el identificador de objeto 1 y el identificador de destino.
   
    Visualizando esto, puede verificar los resultados recorriendo las rutas más cortas entre el Id. 1 y todos los demás objetos. Tenga en cuenta que la ruta más corta entre el Id. 1 y el Id. 4 es 5. Esta es la distancia total entre el <span style="color:orange">identificador 1 y 3</span>, y, a continuación, <span style="color:red">identificador 3 y 4</span>.
   
    ![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Pasos siguientes

* [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md). Hue es una interfaz de usuario web que simplifica la creación, la ejecución y el guardado de trabajos de Pig y Hive, así como el examen del almacenamiento predeterminado de su clúster de HDInsight.

* [Instalación de R en clústeres de HDInsight](hdinsight-hadoop-r-scripts-linux.md): instrucciones para usar la personalización de clústeres para instalar y usar R en clústeres de Hadoop de HDInsight. R es un entorno y lenguaje de código abierto para computación estadística. Proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.

* [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.




<!--HONumber=Feb17_HO2-->


