---
title: "Conexión segura a un clúster de Azure Service Fabric | Microsoft Docs"
description: "Se describe cómo autenticar el acceso de cliente a un clúster de Service Fabric y cómo proteger la comunicación entre los clientes y un clúster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 52f9a3146852ef83c31bd93e1c538e12f0d953eb
ms.openlocfilehash: e44ecf5860becffb39d199e36d36d96f50bf7cf3
ms.lasthandoff: 02/16/2017


---
# <a name="connect-to-a-secure-cluster"></a>Conexión a un clúster seguro
Cuando un cliente se conecta a un nodo del clúster de Service Fabric, se puede autenticar al cliente y establecer la comunicación segura mediante la seguridad basada en certificados o Azure Active Directory (AAD). Esta autenticación garantiza que solo los usuarios autorizados pueden tener acceso al clúster, y a las aplicaciones implementadas, y realizar tareas de administración.  Con anterioridad se debe haber habilitado la seguridad basada en certificados o AAD en el clúster cuando se creó este.  Para más información sobre escenarios de seguridad de clúster, consulte [Protección de un clúster de Service Fabric](service-fabric-cluster-security.md). Si se conecta a un clúster protegido con certificados, [configure el certificado de cliente](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) en el equipo que se conectará al clúster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>Conexión a un clúster seguro mediante la CLI de Azure
Los siguientes comandos de la CLI de Azure describen cómo conectarse a un clúster seguro. 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conexión a un clúster seguro mediante un certificado de cliente
Los detalles del certificado deben corresponder a un certificado de los nodos del clúster. 

Si el certificado tiene entidades de certificación (CA), debe agregar el parámetro `--ca-cert-path` como en el ejemplo siguiente: 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Si tiene varias entidades de certificación, use una coma como delimitador. 

Si el nombre común del certificado no coincide con el punto de conexión de la conexión, puede usar el parámetro `--strict-ssl-false` para omitir la comprobación. 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

Si desea omitir la comprobación de la entidad de certificación, puede agregar el parámetro ``--reject-unauthorized-false`` como se muestra en el siguiente comando:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Para conectarse a un clúster protegido con un certificado autofirmado, utilice el siguiente comando de eliminación de la comprobación de la entidad de certificación y la comprobación de nombre común:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

Después de conectarse, debería poder [ejecutar otros comandos de la CLI](service-fabric-azure-cli.md) para interactuar con el clúster. 

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Conexión a un clúster con PowerShell
Antes de realizar operaciones en un clúster a través de PowerShell, primero establezca una conexión al clúster. La conexión de clúster se utiliza para todos los comandos subsiguientes en la sesión de PowerShell determinada.

### <a name="connect-to-an-unsecure-cluster"></a>Conexión a un clúster no seguro

Para conectarse a un clúster no seguro, proporcione la dirección del punto de conexión del clúster al comando **Connect-ServiceFabricCluster**:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Conexión a un clúster seguro mediante la CLI de Azure con Azure Active Directory

Para conectarse a un clúster segura que utiliza Azure Active Directory para autorizar el acceso de administrador de clústeres, proporcione la huella digital del certificado del clúster y use la marca *AzureActiveDirectory*.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conexión a un clúster seguro mediante un certificado de cliente
Ejecute el siguiente comando de PowerShell para conectarse a un clúster seguro que utiliza certificados de cliente para autorizar el acceso de administrador. Proporcione la huella digital del certificado del clúster y la huella digital del certificado de cliente al que se han concedido permisos para administrar el clúster. Los detalles del certificado deben corresponder a un certificado de los nodos del clúster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* es la huella digital del certificado de servidor instalado en los nodos del clúster. *FindValue* es la huella digital del certificado de cliente de administración.
Cuando los parámetros están rellenos, el comando es parecido al del ejemplo siguiente: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Conexión a un clúster seguro mediante Windows Active Directory
Si el clúster independiente se implementa mediante la seguridad de AD, conéctese a este agregando el modificador "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Conexión a un clúster mediante las API de FabricClient
El SDK de Service Fabric proporciona la clase [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) para la administración de clúster. Para usar las API de FabricClient, obtenga el paquete de NuGet Microsoft.ServiceFabric.

### <a name="connect-to-an-unsecure-cluster"></a>Conexión a un clúster no seguro

Para conectarse a un clúster remoto no seguro, cree una instancia de FabricClient y proporcionar la dirección del clúster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Para el código que se ejecuta desde dentro de un clúster, por ejemplo, en un servicio confiable, cree una clase FabricClient *sin* especificar la dirección del clúster. FabricClient se conecta a la puerta de enlace de administración local en el nodo que está ejecutando actualmente el código, de modo que se evita un salto de red adicional.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conexión a un clúster seguro mediante un certificado de cliente

Los nodos del clúster deben tener certificados válidos cuyo nombre común o nombre DNS de SAN aparezca en la [propiedad RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) establecida en [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). La realización de este proceso permite la autenticación mutua entre el cliente y los nodos del clúster.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Conexión interactiva a un clúster seguro con Azure Active Directory

En el ejemplo siguiente se usa Azure Active Directory para el certificado de servidor y la identidad de clientes para la identidad del servidor.

Una ventana de diálogo se abre automáticamente para un inicio de sesión interactivo al conectarse al clúster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Conexión no interactiva a un clúster seguro con Azure Active Directory

El ejemplo siguiente se basa en Microsoft.IdentityModel.Clients.ActiveDirectory, versión: 2.19.208020213.

Para más información sobre la obtención de tokens de AAD, vea [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Conexión a un clúster seguro sin conocer los metadatos anteriores con Azure Active Directory

En el ejemplo siguiente se utiliza la obtención de tokens no interactivos, pero puede utilizarse el mismo enfoque para crear una experiencia de obtención de tokens interactivos personalizados. Los metadatos de Azure Active Directory necesarios para la obtención de tokens se leen desde la configuración de clúster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Conexión a un clúster seguro mediante Service Fabric Explorer
Para acceder a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) para un clúster determinado, dirija el explorador a:

`http://<your-cluster-endpoint>:19080/Explorer`

La dirección URL completa también está disponible en el panel de elementos esenciales del clúster del portal de Azure.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Conexión a un clúster seguro mediante la CLI de Azure con Azure Active Directory

Para conectarse a un clúster que está protegido con AAD, dirija el explorador a:

`https://<your-cluster-endpoint>:19080/Explorer`

Se le pedirá automáticamente que inicie sesión con AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conexión a un clúster seguro mediante un certificado de cliente

Para conectarse a un clúster que está protegido con certificados, dirija el explorador a:

`https://<your-cluster-endpoint>:19080/Explorer`

Automáticamente se le pedirá que seleccione un certificado de cliente.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configuración de un certificado de cliente en el equipo remoto
Deben utilizarse, al menos, dos certificados para proteger el clúster, uno para el certificado de servidor y clúster, y otro para el acceso de cliente.  Se recomienda utilizar también certificados secundarios adicionales y certificados de acceso de cliente.  Para proteger la comunicación entre un cliente y un nodo del clúster mediante la seguridad basada en certificados, primero debe obtener e instalar el certificado de cliente. El certificado se puede instalar en el almacén personal (Mi) del equipo local o en el almacén personal del usuario actual.  También necesitará la huella digital del certificado de servidor para que el cliente pueda autenticar el clúster.

Ejecute el siguiente cmdlet de PowerShell para configurar el certificado de cliente en el equipo que usará para acceder al clúster.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Si es un certificado autofirmado, debe importarlo al almacén "TrustedPeople" de la máquina para poder usar este certificado para conectarse a un clúster seguro.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Pasos siguientes
* [Proceso de actualización del clúster de Service Fabric y expectativas del usuario](service-fabric-cluster-upgrade.md)
* [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).
* [Introducción al modelo de estado de Service Fabric](service-fabric-health-introduction.md)
* [Seguridad de aplicaciones y RunAs](service-fabric-application-runas-security.md)


