---
title: "Uso de Azure Portal para crear clústeres de Azure HDInsight con Data Lake Store | Microsoft Docs"
description: "Uso de Azure Portal para crear y usar clústeres de HDInsight con Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: d1d780eb2c635f60409396804c0b8b706e59127b
ms.lasthandoff: 03/07/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Creación de clústeres de HDInsight con Data Lake Store mediante Azure Portal
> [!div class="op_single_selector"]
> * [Uso de Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso de PowerShell (para el almacenamiento predeterminado)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso de PowerShell (para almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Uso de Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

En este artículo se analiza el uso de Azure Portal para crear clústeres de HDInsight con acceso a Azure Data Lake Store. Para los tipos de clúster compatibles, puede usar Data Lake Store como almacenamiento predeterminado o como una cuenta de almacenamiento adicional. 

Cuando se usa Data Lake Store como almacenamiento adicional, la cuenta de almacenamiento predeterminada para los clústeres sigue siendo Azure Blob Storage y los archivos relacionados con el clúster (como los registros) todavía se escriben en el almacenamiento predeterminado. Sin embargo, los datos que desea procesar se pueden almacenar en una cuenta de Data Lake Store. El uso de Data Lake Store como una cuenta de almacenamiento adicional no afecta al rendimiento o la capacidad de lectura o escritura en el almacenamiento del clúster.

Estas son algunas consideraciones importantes que deben tenerse en cuenta al usar HDInsight con Data Lake Store:

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento predeterminado está disponible para la versión 3.5 de HDInsight.

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento predeterminado *no está disponible* para clústeres de HDInsight Premium.

* La opción para crear clústeres de HDInsight con acceso a Data Lake Store como almacenamiento está disponible para las versiones 3.2, 3.4 y 3.5. de HDInsight.

* En clústeres HBase (Windows y Linux), Data Lake Store *no se admite* como una opción de almacenamiento, ya sea almacenamiento predeterminado o almacenamiento adicional.

* En clústeres de Storm (Windows y Linux), puede usar Data Lake Store para escribir datos de una topología de Storm. Puede usar Data Lake Store para datos de referencia que luego puede leer una topología de Storm. Para obtener más información, consulte [Usar el Almacén de Data Lake en una topología de Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, asegúrese de que ha cumplido los requisitos siguientes:

* **Una suscripción de Azure**. Vaya a [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Una cuenta de Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción a Azure Data Lake Store mediante Azure Portal](data-lake-store-get-started-portal.md).

* **Una entidad de servicio de Azure Active Directory**. En este tutorial se proporcionan instrucciones sobre cómo crear entidades de servicio en Azure Active Directory (Azure AD). Sin embargo, para crear una entidad de servicio, debe ser administrador de Azure AD. Si ya lo es, puede hacer caso omiso a este requisito previo y continuar con el tutorial.

 >[!NOTE]
 >Solamente puede crear una entidad de servicio si es administrador de Azure AD. Su administrador de Azure AD debe generar una entidad de servicio antes de crear un clúster de HDInsight con Data Lake Store. Además, la entidad de servicio debe crearse con un certificado, tal y como se describe en [Creación de entidad de servicio con certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal
En esta sección, se crea un clúster de Hadoop en HDInsight que usa Data Lake Store como almacenamiento adicional. En esta versión, para un clúster de Hadoop, puede usar Data Lake Store solo como almacenamiento adicional para el clúster. El almacenamiento predeterminado sigue siendo Azure Blob Storage. En primer lugar, crea la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Para iniciar el aprovisionamiento de un clúster de HDInsight, siga las instrucciones descritas en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md).

3. En la hoja **Almacenamiento**, especifique si desea usar Blob Storage o Data Lake Store como almacenamiento predeterminado y luego lleve a cabo una de las acciones siguientes:

 * Para usar Data Lake Store como almacenamiento predeterminado, vaya al paso 4.

 * Para usar Blob Storage como almacenamiento predeterminado:

    a. En **Tipo de almacenamiento principal**, seleccione **Azure Storage**.

    b. En **Método de selección**, realice una de las acciones siguientes:      * Para especificar una cuenta de almacenamiento que forma parte de su suscripción de Azure, seleccione **Mis suscripciones** y después seleccione la cuenta de almacenamiento.      * Para especificar una cuenta de almacenamiento que está fuera de su suscripción de Azure, seleccione **Clave de acceso** y luego proporcione la información de la cuenta de almacenamiento externa.

    c. En **Contenedor predeterminado**, escriba el nombre de contenedor predeterminado que se sugiere en el portal o especifique el suyo propio.

 Cuando use Blob Storage como almacenamiento predeterminado, puede seguir utilizando Data Lake Store como almacenamiento adicional para el clúster. Para ello, haga clic en **Acceso a Data Lake Store** y después vaya al paso 5.

 ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

4. Para usar Data Lake Store como almacenamiento predeterminado:

 a. En **Tipo de almacenamiento principal**, haga clic en **Data Lake Store**.

 b. Seleccione una cuenta existente de Data Lake Store, especifique la ruta de acceso de la carpeta raíz en la que se van a guardan los archivos específicos del clúster, establezca **Ubicación** en **Este de EE. UU. 2** y, por último, haga clic en **Acceso a Data Lake Store**.

 >[!NOTE]
 >Puede usar esta opción solo con clústeres de HDInsight 3.5.(Standard Edition). En los clústeres de HDInsight 3.5 esta opción no está disponible para el tipo de clúster de HBase.

 En la siguiente captura de pantalla, la ruta de acceso de la carpeta raíz es /clusters/myhdiadlcluster, donde *myhdiadlcluster* es el nombre del clúster que se está creando. En tal caso, asegúrese de que la carpeta */clústeres* ya existe en la cuenta de Data Lake Store. La carpeta *myhdiadlcluster* se crea durante la creación del clúster. De forma similar, si la ruta de acceso raíz se estableció en */hdinsight/clusters/data/myhdiadlcluster*, asegúrese de que */hdinsight/clústeres/datos/* existe en la cuenta de Data Lake Store.

 ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

5. En la hoja **Acceso a Data Lake Store**, realice una de las siguientes acciones:

 * Para usar a una entidad de servicio existente, vaya al paso 6.
 * Para crear una entidad de servicio:

    a. En la hoja **Acceso a Data Lake Store**, haga clic en **Crear nuevo** y luego haga clic en **Entidad de servicio**.

    b. En la hoja **Crear una entidad de servicio**, especifique los valores necesarios.  

    c. Haga clic en **Crear**.  
    Se crean automáticamente un certificado y una aplicación de Azure AD.

    ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

    También puede hacer clic en **Descargar certificado** para descargar el certificado asociado a la entidad de servicio creada. La descarga del certificado es útil si desea usar la misma entidad de servicio cuando cree clústeres de HDInsight adicionales. Haga clic en **Seleccionar**.

6. Para usar una entidad de servicio existente:

 a. En la hoja **Acceso a Data Lake Store**, haga clic en **Usar existente** y luego haga clic en **Entidad de servicio**.

 b. En la hoja **Seleccionar una entidad de servicio**, busque una entidad de servicio existente. Haga clic en la entidad de servicio que desee usar y después en **Seleccionar**.

 c. En la hoja **Acceso a Data Lake Store**, cargue el certificado (archivo .pfx) asociado a la entidad de servicio seleccionada y especifique la contraseña del certificado.

 ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

7. En la hoja **Acceso a Data Lake Store**, haga clic en **Acceso**.  
La hoja **Seleccionar permisos de archivo** se abre de forma predeterminada. En ella se muestran todas las cuentas de Data Lake Store de su suscripción.

8. Seleccione la cuenta de Data Lake Store que desea asociar con el clúster.
 Se muestran todos los archivos y carpetas de esa cuenta. A continuación, puede asignar permisos en el nivel de archivo o carpeta. Para asignar permisos en el nivel raíz de la cuenta, active la casilla situada junto al nombre de cuenta.

 ![Incorporación de una entidad de servicio a un clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Incorporación de una entidad de servicio a un clúster de HDInsight")

 > [!NOTE]
 > Si usa la cuenta de Data Lake Store como almacenamiento predeterminado para un clúster, asigne permisos a la entidad de servicio en el nivel raíz de la cuenta de Data Lake Store.

9. Si desea asignar permisos para archivos o carpetas dentro de una cuenta, seleccione la cuenta de Data Lake Store en la ventana **Seleccionar permisos de archivo**.

10. En el panel de la derecha, seleccione los archivos o las carpetas a los que desea asignar permisos, seleccione los permisos (Lectura, Escritura o Ejecución), especifique si los permisos se aplican de forma recursiva también a los elementos secundarios y luego haga clic en **Seleccionar**.

    ![Asignación de permisos de entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Asignación de permisos de entidad de servicio al clúster de HDInsight")

11. En la ventana **Asignar los permisos seleccionados**, para asignar los permisos para la entidad de servicio de Azure Active Directory en la cuenta, los archivos o las carpetas que seleccionó, haga clic en **Ejecutar**.

    ![Asignación de permisos de entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Asignación de permisos de entidad de servicio al clúster de HDInsight")

12. Una vez que los permisos se han asignado correctamente, haga clic en **Listo** en todas las hojas abiertas hasta que regrese a la hoja **Acceso a Data Lake Store**.

13. En **Acceso a Data Lake Store**, haga clic en **Seleccionar** y continúe con la creación del clúster, tal y como se describe en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

14. Una vez completada la configuración del clúster, en la hoja de clúster, compruebe los resultados llevando a cabo una de las siguientes acciones o las dos:

 * Para comprobar que el almacenamiento asociado para el clúster es la cuenta de Data Lake Store especificada, haga clic en **Cuentas de almacenamiento** en el panel izquierdo.

        ![Add service principal to HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Add service principal to HDInsight cluster")

 * Para comprobar que la entidad de servicio está correctamente asociada con el clúster de HDInsight, haga clic en **Acceso a Data Lake Store** en el panel izquierdo.

       ![Add service principal to HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="examples"></a>Ejemplos

Después de haber configurado el clúster con Data Lake Store como almacenamiento, consulte estos ejemplos sobre cómo usar el clúster de HDInsight para analizar los datos que están almacenados en Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Ejecución de una consulta de Hive en datos de Data Lake Store (como almacenamiento principal)

Para ejecutar una consulta de Hive, use la interfaz de vistas de Hive en el portal de Ambari. Para instrucciones sobre cómo usar las vistas de Hive de Ambari, consulte [Uso de Vista de Hive con Hadoop en HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md).

Cuando se trabaja con datos de Data Lake Store, hay algunas cadenas que se deben cambiar.

Si utiliza, por ejemplo, el clúster que creó con Data Lake Store como almacenamiento principal, la ruta de acceso a los datos es: *adl://<nombre_cuenta_data_lake_store>/azuredatalakestore.net/path/to/file*. Una consulta de Hive para crear una tabla a partir de los datos de ejemplo almacenados en la cuenta de Data Lake Store tiene un aspecto similar al siguiente:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descripciones:
* `adl://hdiadlstorage.azuredatalakestore.net/` es la raíz de la cuenta de Data Lake Store.
* `/clusters/myhdiadlcluster` es la raíz de los datos de clúster que especificó al crear el clúster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` es la ubicación del archivo de ejemplo que usó en la consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Ejecución de una consulta de Hive en datos de Data Lake Store (como almacenamiento adicional)

Si el clúster que creó usa Blob Storage como almacenamiento predeterminado, los datos de ejemplo no están contenidos en la cuenta de Azure Data Lake Store que se usa como almacenamiento adicional. En tal caso, transfiera primero los datos desde Blob Storage a Data Lake Store y luego ejecute las consultas tal y como se muestra en el ejemplo anterior.

Para información sobre la copia de datos de Blob Storage a Data Lake Store, consulte los siguientes artículos:

* [Uso de Distcp para copiar datos entre los blobs de Azure Storage y Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Uso de AdlCopy para copiar datos de los blobs de Azure Storage a Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Uso de Data Lake Store con un clúster de Spark
Puede usar un clúster de Spark para ejecutar trabajos de Spark en los datos que se almacenan en Data Lake Store. Para más información, consulte [Uso de un clúster de HDInsight Spark para analizar los datos en Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Usar el Almacén de Data Lake en una topología de Storm
El Almacén de Data Lake se puede usar para escribir datos de una topología de Storm. Para obtener instrucciones sobre cómo posibilitar este escenario, consulte [Uso del Almacén de Azure Data Lake con Apache Storm con HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Consulte también
* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

