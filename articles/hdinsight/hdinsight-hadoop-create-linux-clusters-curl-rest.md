---
title: "Creación de Azure HDInsight (Hadoop) mediante cURL y REST | Microsoft Docs"
description: "Aprenda a crear clústeres de HDInsight mediante cURL, plantillas de Azure Resource Manager y la API de REST de Azure. Puede especificar el tipo de clúster (Hadoop, HBase o Storm) o usar scripts para instalar componentes personalizados."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: c8ed0760650f7fb3c85eef4ec7f72c60bb0efd00
ms.lasthandoff: 02/21/2017


---
# <a name="create-hdinsight-clusters-using-curl-and-the-azure-rest-api"></a>Creación de clústeres de HDInsight mediante cURL y la API de REST de Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Aprenda a crear un clúster de HDInsight mediante una plantilla de Azure Resource Manager y la API de REST de Azure.

La API de REST de Azure permite realizar operaciones de administración en servicios hospedados en la plataforma Azure, incluida la creación de recursos como, por ejemplo, clústeres de HDInsight.

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **CLI de Azure 2.0 (versión preliminar)**. La CLI de Azure se usa para crear una entidad de servicio, que luego se emplea para generar tokens de autenticación para solicitudes a la API de REST de Azure. Para obtener más información sobre la versión preliminar de la CLI de Azure 2.0, consulte [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) (Introducción a la CLI de Azure 2.0).

* **cURL**. Esta utilidad está disponible a través del sistema de administración de paquetes, o se puede descargar desde [http://curl.haxx.se/](http://curl.haxx.se/).

  > [!NOTE]
  > Si usa PowerShell para ejecutar los comandos de este documento, debe quitar primero el alias `curl` que crea de forma predeterminada. Este alias usa Invoke-WebRequest en lugar de cURL. Si no quita este alias, puede que reciba errores con algunos de los comandos usados en este documento.
  >
  > Para quitar este alias, use el comando siguiente desde el símbolo del sistema de PowerShell:
  >
  > `Remove-item alias:curl`
  >
  > Una vez quitado el alias, debe poder usar la versión de cURL que instaló en el sistema.

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>Creación de una plantilla

Las plantillas de Azure Resource Manager son documentos JSON que describen un **grupo de recursos** y todos los recursos incluidos (por ejemplo, HDInsight). Este enfoque basado en la plantilla le permite definir todos los recursos que necesita para HDInsight en una plantilla.

Lo siguiente es una combinación de los archivos de plantilla y de parámetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que crea un clúster basado en Linux, con una contraseña para proteger la cuenta de usuario de SSH.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "location": {
                       "type": "string",
                       "allowedValues": ["Central US",
                       "East Asia",
                       "East US",
                       "Japan East",
                       "Japan West",
                       "North Europe",
                       "South Central US",
                       "Southeast Asia",
                       "West Europe",
                       "West US"],
                       "metadata": {
                           "description": "The location where all azure resources are deployed."
                       }
                   },
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[parameters('location')]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[parameters('location')]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.5",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "location": {
                   "value": "North Europe"
               },
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Este ejemplo se usa en los pasos de este documento. Sustituya los *valores* del ejemplo en la sección **Parámetros** con los valores de su clúster.

> [!IMPORTANT]
> La plantilla usa el número predeterminado de nodos de trabajo (4) para un clúster de HDInsight. Si planea crear más de 32 nodos de trabajo, tiene que seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Inicio de sesión en la suscripción de Azure

Siga los pasos documentados en [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) (Introducción a la CLI de Azure 2.0). y conéctese a su suscripción con el comando `az login`.

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

> [!NOTE]
> Estos pasos son una versión resumida de la sección *Creación de entidad de servicio con contraseña* del documento [Uso de la CLI de Azure para crear a una entidad de servicio para acceder a recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md#create-service-principal-with-password). Estos pasos crean una entidad de servicio que sirve para autenticarse en la API de REST de Azure.

1. Desde una línea de comandos, use el siguiente comando para enumerar las suscripciones de Azure.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    En la lista, seleccione la suscripción que desee usar y anote las columnas **Subscription_ID** y __Tenant_ID__. Copie estos valores.

2. Use los comandos siguientes para crear una aplicación en Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Reemplace los valores de `--display-name`, `--homepage`, `--identifier-uris` y por sus propios valores. Especifique una contraseña para la nueva entrada de Active Directory.

   > [!NOTE]
   > Los valores `--home-page` y `--identifier-uris` no necesitan hacer referencia a una página web real hospedada en Internet. Deben ser URI únicos.

   El valor devuelto por este comando es el __Id. de aplicación__ para la nueva aplicación. Guarde esta cadena.

3. Use el siguiente comando para crear una entidad de servicio con el **Id. de aplicación**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     El valor devuelto por este comando es el __Id. de objeto__. Guarde esta cadena.

4. Asigne el rol **Propietario** a la entidad de servicio mediante el valor de **Id. de objeto**. Utilice el **identificador de suscripción** que obtuvo anteriormente.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Obtención de un token de autenticación

Use el siguiente comando para recuperar un token de autenticación:

    curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=AppID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=password" \
    --data-urlencode "resource=https://management.azure.com/"

    Replace **TenantID**, **AppID**, and **password** with the values obtained or used previously.

    If this request is successful, you receive a 200 series response and the response body contains a JSON document.

    The JSON document returned by this request contains an element named **access_token**. The value of **access_token** is used to authentication requests to the REST API.

   ```json
   {
       "token_type":"Bearer",
       "expires_in":"3599",
       "expires_on":"1463409994",
       "not_before":"1463406094",
       "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
   }
   ```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Use lo siguiente para crear un grupo de recursos.

* Reemplace **SubscriptionID** por el identificador de la suscripción que recibió al crear la entidad de servicio.
* Reemplace **AccessToken** por el token de acceso que recibió en el paso anterior.
* Reemplace **DataCenterLocation** por el centro de datos en el que desea crear el grupo de recursos y los recursos. Por ejemplo, "Centro-Sur de EE.UU.".
* Reemplace **ResourceGroupName** por el nombre que desea usar para este grupo:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Si esta solicitud se realiza correctamente, recibirá una respuesta 200 series y el cuerpo de respuesta contendrá un documento JSON que incluye información sobre el grupo. El elemento `"provisioningState"` contiene un valor de `"Succeeded"`.

## <a name="create-a-deployment"></a>de una implementación

Ejecute el siguiente comando para implementar la plantilla en el grupo de recursos.

* Reemplace **SubscriptionID** y **AccessToken** por los valores usados anteriormente.
* Reemplace **ResourceGroupName** por el nombre del grupo de recursos que creó en la sección anterior.
* Reemplace **DeploymentName** por el nombre que desea usar para esta implementación.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> Si guarda la plantilla en un archivo, puede usar el comando siguiente en lugar de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Si esta solicitud se realiza correctamente, recibirá una respuesta 200 series y el cuerpo de respuesta contendrá un documento JSON que incluye información sobre la operación de implementación.

> [!IMPORTANT]
> La implementación ya se envió, pero aún no se ha completado. La implementación puede tardar varios minutos, normalmente unos 15, en completarse.

## <a name="check-the-status-of-a-deployment"></a>Comprobación del estado de la implementación

Utilice el comando siguiente para comprobar el estado de la implementación:

* Reemplace **SubscriptionID** y **AccessToken** por los valores usados anteriormente.
* Reemplace **ResourceGroupName** por el nombre del grupo de recursos que creó en la sección anterior.

```bash
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Este comando devuelve un documento JSON que incluye información sobre la operación de implementación. El elemento `"provisioningState"` contiene el estado de la implementación. Si contiene un valor de `"Succeeded"`, la implementación se ha completado correctamente.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya creó un clúster de HDInsight correctamente, use lo siguiente para aprender a trabajar con el clúster.

### <a name="hadoop-clusters"></a>Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

