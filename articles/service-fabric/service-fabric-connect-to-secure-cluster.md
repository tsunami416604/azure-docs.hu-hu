---
title: Biztonságos kapcsolódás az Azure Service Fabric-fürt |} Microsoft Docs
description: Ismerteti, hogyan hitelesítheti az ügyfelek hozzáférhessenek a Service Fabric-fürt és az ügyfelek és a fürt közötti kommunikáció biztonságossá tétele.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: f2a181fbae8ab1e08669021c42c5b4be08f66172
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364811"
---
# <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz

Amikor egy ügyfél csatlakozik egy Service Fabric-fürt csomópontja, akkor az ügyfél jött létre a biztonsági tanúsítvány, vagy az Azure Active Directory (AAD) biztonságos és hitelesített kommunikáció lehet. A hitelesítés biztosítja, hogy csak a jogosult felhasználók férhetnek hozzá a fürtöt és alkalmazások telepítését, és felügyeleti feladatok elvégzésére.  Tanúsítvány vagy AAD biztonsági kell korábban engedélyezve van a fürt a fürt létrehozásakor.  A fürt biztonsági forgatókönyvek további információkért lásd: [fürt biztonsági](service-fabric-cluster-security.md). Ha a tanúsítványok által védett fürthöz csatlakozik [állítsa be az ügyféltanúsítvány](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) a számítógépen, amely kapcsolódik a fürthöz. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Csatlakozás Azure Service Fabric CLI-vel (sfctl) biztonságos fürthöz

Csatlakozás a Service Fabric CLI-vel (sfctl) biztonságos fürthöz néhány különböző módja van. Ha a hitelesítés ügyféltanúsítvány használatával történik, a tanúsítvány adatainak meg kell egyeznie egy, a fürtcsomópontokon telepített tanúsítvány adataival. Ha a tanúsítvány rendelkezik tanúsítványszolgáltatók (CA), továbbá adja meg a megbízható hitelesítésszolgáltatók szeretné.

A fürt használatával csatlakozhat a `sfctl cluster select` parancsot.

Ügyféltanúsítványokat a tanúsítvány és kulcspár, vagy a egyetlen pem-fájl két különböző módokon értelmezik a adható meg. A jelszóval védett `pem` fájlok, a rendszer kérni fogja a automatikusan meg kell adnia a jelszót.

Adja meg az ügyféltanúsítványt a pem-fájl, adja meg az elérési út a `--pem` argumentum. Példa:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Jelszóval védett pem-fájlok kérni fogja a parancs futtatása előtt jelszót.

Adja meg a tanúsítványt, a kulcs pár használja a `--cert` és `--key` argumentumok megadása a fájl minden egyes fájlok elérési útjait.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Néha a tanúsítványok használatával teszi biztonságossá a teszt- vagy fejlesztési fürtök tanúsítvány érvényesítése sikertelen. A tanúsítványellenőrzés kihagyása, adja meg a `--no-verify` lehetőséget. Példa:

> [!WARNING]
> Ne használja a `no-verify` beállítást az éles Service Fabric-fürtök történő csatlakozás során.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Emellett a megbízható CA-tanúsítványok, vagy egyedi Tanúsítványos könyvtárak elérési útjait is megadhat. Az elérési utak megadásához használja a `--ca` argumentum. Példa:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

A kapcsolódás után meg kell tudni [további parancsok futtatásának jogára sfctl](service-fabric-cli.md) amellyel kommunikálni tud a fürt.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Csatlakozás fürthöz a PowerShell használatával
Mielőtt elvégezné a műveleteket a PowerShell segítségével egy fürtön, először kapcsolatot létrehozni a fürtöt. A fürtkapcsolat szolgál minden további parancs a megadott PowerShell-munkamenetben.

### <a name="connect-to-an-unsecure-cluster"></a>Csatlakozás nem biztonságos fürthöz

Egy nem biztonságos fürthöz csatlakozzon, adja meg a fürt végpont címét, hogy a **Connect-ServiceFabricCluster** parancs:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Csatlakozás az Azure Active Directoryval biztonságos fürthöz

Szeretne csatlakozni egy biztonságos fürt, amely a fürt rendszergazdai hozzáférés hitelesítése az Azure Active Directory, adja meg a fürt tanúsítvány ujjlenyomatát, és használja a *AzureActiveDirectory* jelzőt.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Csatlakozás egy ügyfél-tanúsítvány használatával biztonságos fürthöz
A következő parancsot PowerShell rendszergazdai hozzáférés hitelesítése ügyféltanúsítványok használó biztonságos fürtben való kapcsolódáshoz. 

#### <a name="connect-using-certificate-common-name"></a>Kapcsolódás a tanúsítvány egyszerű neve
Adja meg a fürt tanúsítvány egyszerű neve és az ügyféltanúsítványt, amely rendelkezik engedéllyel a fürtkezeléshez köznapi neve. A tanúsítvány részleteinél meg kell egyeznie a fürtcsomópontokon egy tanúsítványt.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* van a fürtcsomópontokon telepített kiszolgálói tanúsítvány köznapi neve. *Findvalue –* a rendszergazdai ügyféltanúsítvány köznapi neve. Ha a paraméter ki van töltve, a parancs néz ki a következő példa:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Csatlakozás a tanúsítvány ujjlenyomata
Adja meg a fürt tanúsítvány ujjlenyomata és, hogy rendelkezik engedéllyel a kiszolgálófürt-felügyelet az ügyféltanúsítvány ujjlenyomata. A tanúsítvány részleteinél meg kell egyeznie a fürtcsomópontokon egy tanúsítványt.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* az ujjlenyomatot a kiszolgálói tanúsítvány a fürtcsomóponton telepítve van. *Findvalue –* van a felügyeleti ügyfél tanúsítvány ujjlenyomatát.  Ha a paraméter ki van töltve, a parancs néz ki a következő példa:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Csatlakozás a Windows Active Directory használatával biztonságos fürthöz
Ha a különálló fürt AD biztonsági használatával lett telepítve, csatlakozzon a fürthöz a következő kapcsolóhoz: "WindowsCredential" hozzáfűzésével.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Csatlakozzon a fürthöz, a FabricClient API-k használatával
A Service Fabric SDK biztosít a [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) a fürtkezeléshez osztály. A FabricClient API-k használatához kérje a Microsoft.ServiceFabric NuGet csomag.

### <a name="connect-to-an-unsecure-cluster"></a>Csatlakozás nem biztonságos fürthöz

A csatlakozás nem biztonságos távoli fürthöz, FabricClient példány létrehozása, és adja meg a fürt címe:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

A fürtön belül futó kód, például egy megbízható szolgáltatásban hozzon létre egy FabricClient *nélkül* fürt címének megadása. FabricClient csatlakozik a helyi felügyeleti átjáró a csomóponton, a kód jelenleg fut, szükségtelenné téve az extra hálózati ugrások.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Csatlakozás egy ügyfél-tanúsítvány használatával biztonságos fürthöz

A fürt csomópontjaihoz rendelkeznie kell érvényes tanúsítványok közös névvel, vagy a SAN DNS-neve megtalálható a [RemoteCommonNames tulajdonság](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) be [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Ezt az eljárást lehetővé teszi, hogy az ügyfél és a fürt csomópontjai közötti kölcsönös hitelesítés.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Csatlakozás Azure Active Directory használatával interaktívan biztonságos fürthöz

A következő példa az Azure Active Directory identitás- és a kiszolgáló ügyféltanúsítványt a kiszolgáló identitását.

A párbeszédpanelen automatikusan ugrik fel a interaktív bejelentkezéshez a fürthöz való csatlakozáskor.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Csatlakozás Azure Active Directory nem interaktív használatával biztonságos fürthöz

Az alábbi példa támaszkodik Microsoft.IdentityModel.Clients.ActiveDirectory, verzió: 2.19.208020213.

Az AAD-jogkivonat megszerzése további információkért lásd: [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Csatlakozás az Azure Active Directoryval előzetes metaadatok tudomása nélkül biztonságos fürthöz

Az alábbi példában a nem interaktív token beszerzési, de ugyanezt a megközelítést lehet összeállításához kell használni a egyéni interaktív jogkivonat beszerzése érdekében. Az Azure Active Directory foglalásához szükséges metaadatokat jelenti a token beszerzése a fürtkonfiguráció olvasható.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Csatlakozás biztonságos fürthöz a Service Fabric Explorerrel
Eléréséhez [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) egy adott fürtben, mutasson a böngészőt, hogy:

`http://<your-cluster-endpoint>:19080/Explorer`

A teljes URL-címet is érhető el a fürt essentials ablaktábláján az Azure-portálon.

Biztonságos fürtben, a Windows vagy OS X böngésző használatával szeretne csatlakozni, az ügyfél-tanúsítványt importálhatja, és a böngésző felszólítja a fürthöz való kapcsolódáshoz használandó tanúsítvány.  Linux-gépek a tanúsítványt kell importálni használja (minden böngésző rendelkezik különböző mechanizmusok) speciális böngésző beállításait, majd mutasson a tanúsítvány helye a lemezen a.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Csatlakozás az Azure Active Directoryval biztonságos fürthöz

A fürt AAD-ben biztosított csatlakozhat, mutasson a böngészőt, hogy:

`https://<your-cluster-endpoint>:19080/Explorer`

Automatikusan kell kéri aad-ben bejelentkezni.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Csatlakozás egy ügyfél-tanúsítvány használatával biztonságos fürthöz

Szeretne csatlakozni egy fürt, amely a tanúsítványok által védett, mutasson a böngészőt, hogy:

`https://<your-cluster-endpoint>:19080/Explorer`

Automatikusan kell kéri egy ügyféltanúsítványt választhat.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>A távoli számítógépen ügyfél tanúsítvány beállítása
Legalább két tanúsítványt használjon a fürt egy, a fürt és a kiszolgálói tanúsítvány és egy másik, az ügyfél hozzáférésének biztonságossá tételéhez.  Javasoljuk, hogy is használjon további másodlagos és az ügyfél-hozzáférési tanúsítványokat.  Az ügyfél és egy fürt csomópontja, használja a biztonsági tanúsítvány közötti kommunikáció védelme érdekében először beszerzése és telepíti az ügyféltanúsítványt. A tanúsítványt a tárolóba személyes (a) a helyi számítógépen vagy az aktuális felhasználó telepítheti.  A tanúsítvány ujjlenyomatát is, hogy az ügyfél képes hitelesíteni a fürt kell.

Futtassa a következő PowerShell-parancsmag segítségével állítsa be az ügyféltanúsítványt a számítógépen, ahol a fürt eléréséhez.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Ha önaláírt tanúsítványt, meg kell importálni szeretné a számítógép "megbízható személyeknek" tárolóban, ezt a tanúsítványt a csatlakozni a biztonságos fürt használatba vétele előtt.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>További lépések

* [Service Fabric-fürt verziófrissítés folyamatáról és az Ön elvárásainak](service-fabric-cluster-upgrade.md)
* [A Visual Studio a Service Fabric-alkalmazások kezelése](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric állapotfigyelő modell bemutatása](service-fabric-health-introduction.md)
* [Alkalmazás biztonságának és RunAs](service-fabric-application-runas-security.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
