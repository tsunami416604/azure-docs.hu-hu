---
title: Biztonságos kapcsolódás az Azure Service Fabric-fürt |} A Microsoft Docs
description: Az ügyfél hozzáférjen a Service Fabric-fürt és az ügyfelek és a egy fürt közötti kommunikáció biztonságossá tételére módját ismerteti.
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
ms.date: 01/29/2019
ms.author: ryanwi
ms.openlocfilehash: 55564de4a3c5ff2d3ba3ddc5e68fa3d1b2d51e71
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296392"
---
# <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz

Amikor egy ügyfél csatlakozik egy Service Fabric-fürt csomópontja, az ügyfél tanúsítvány security vagy Azure Active Directory (AAD) használatával létrehozott hitelesített és biztonságos kommunikációt is lehet. A hitelesítés biztosítja, hogy csak a jogosult felhasználók hozzáférhetnek a fürt és üzembe helyezett alkalmazások, és felügyeleti feladatokat hajthat végre.  Tanúsítvány vagy AAD biztonsági kell korábban engedélyezve van a fürt a fürt létrehozásakor.  A fürtök biztonsági forgatókönyveit további információkért lásd: [biztonsági fürt](service-fabric-cluster-security.md). Ha a tanúsítványok biztonságos fürthöz csatlakozik [állítsa be az ügyféltanúsítványt](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) a számítógépen, amely kapcsolódik a fürthöz. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Az Azure Service Fabric parancssori felület (sfctl) használatával egy biztonságos fürthöz való kapcsolódáshoz

Nincsenek különböző módokon használatával a Service Fabric parancssori felület (sfctl) egy biztonságos fürthöz való csatlakozáshoz. Ha a hitelesítés ügyféltanúsítvány használatával történik, a tanúsítvány adatainak meg kell egyeznie egy, a fürtcsomópontokon telepített tanúsítvány adataival. Ha a tanúsítvány rendelkezik tanúsítványszolgáltatók (CA), továbbá adja meg a megbízható hitelesítésszolgáltatók szeretné.

A fürt használatával csatlakozhat a `sfctl cluster select` parancsot.

Ügyféltanúsítványok két különböző fashions, vagy egy tanúsítványból és a kulcs pár, egyetlen PFX-fájlban adható meg. Jelszóval védett PEM-fájlok, automatikusan kéri a jelszót. Az ügyfél-tanúsítványt egy PFX-fájlba szerezte be, ha először konvertálja a PFX-fájlt egy PEM-fájlt a következő paranccsal. 

```bash
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Ha a .pfx-fájl nem jelszóval védett, használja a pass - passin: a legutóbbi paraméterhez.

Adja meg az ügyféltanúsítványt egy pem-fájlt, adja meg a fájl elérési útját a `--pem` argumentum. Példa:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Jelszóval védett pem-fájlok felszólítja jelszó bármely parancs futtatása előtt.

Adjon meg egy tanúsítványt, a kulcs pár használata a `--cert` és `--key` adja meg a megfelelő fájlkiszolgálókon fájlelérési út argumentumokat.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Néha a tanúsítványok, tesztelési vagy fejlesztési fürtök védelmére szolgáló tanúsítvány érvényesítése sikertelen. Tanúsítvány-ellenőrzés megkerülését, adja meg a `--no-verify` lehetőséget. Példa:

> [!WARNING]
> Ne használja a `no-verify` lehetőséget a Service Fabric-fürtök éles környezetben való csatlakozáskor.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Emellett megbízható CA-tanúsítványok, illetve az egyes tanúsítványok könyvtárak elérési útjait is megadhat. Az elérési utak megadásához használja a `--ca` argumentum. Példa:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

A csatlakozás után meg kell tudni [más sfctl-parancsok futtatásához](service-fabric-cli.md) való kommunikáljanak a fürttel.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Csatlakozás a fürthöz PowerShell használatával
Mielőtt egy fürtön a Powershellen keresztül műveleteket végez, először a fürt kapcsolatot létesíteni. A megadott PowerShell-munkamenetben a fürt kapcsolati minden ezt követő parancsok szolgál.

### <a name="connect-to-an-unsecure-cluster"></a>Csatlakozás biztonságos fürthöz

A nem biztonságos fürtökhöz csatlakozhat, adja meg a fürt végpont címe, a **Connect-ServiceFabricCluster** parancsot:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory használatával egy biztonságos fürthöz való kapcsolódáshoz

Szeretne csatlakozni egy biztonságos fürt, amely az Azure Active Directoryt használja a fürt rendszergazdai hozzáférés engedélyezésére, adja meg a fürt tanúsítvány ujjlenyomatát, és használja a *AzureActiveDirectory* jelzőt.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Egy olyan ügyféltanúsítvány használatával egy biztonságos fürthöz való kapcsolódáshoz
Futtassa a következő PowerShell-parancsot egy rendszergazdai hozzáférés engedélyezésére az ügyféltanúsítványok használó biztonságos fürthöz való csatlakozáshoz. 

#### <a name="connect-using-certificate-common-name"></a>Csatlakozás a tanúsítvány köznapi nevét használó
Adja meg a fürt tanúsítvány köznapi nevét és az ügyféltanúsítványt, amely rendelkezik engedéllyel a fürtkezeléshez köznapi nevével. A tanúsítvány adatainak meg kell egyeznie a fürtcsomópontokon egy tanúsítványt.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* a fürtcsomópontokon telepített tanúsítvány köznapi neve. *FindValue* a rendszergazdai ügyfél tanúsítványa köznapi neve. A paraméterek ki vannak töltve, ha a parancs az alábbi példához hasonlóan néz ki:
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

#### <a name="connect-using-certificate-thumbprint"></a>Csatlakozás a tanúsítvány-ujjlenyomat használatával
Adja meg a fürt tanúsítvány-ujjlenyomat és, hogy rendelkezik engedéllyel a kiszolgálófürt-felügyelet az ügyféltanúsítvány ujjlenyomatát. A tanúsítvány adatainak meg kell egyeznie a fürtcsomópontokon egy tanúsítványt.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* van a fürtcsomópontokon telepített tanúsítvány ujjlenyomatával. *FindValue* van, a rendszergazda-ügyfél tanúsítványának ujjlenyomata.  A paraméterek ki vannak töltve, ha a parancs az alábbi példához hasonlóan néz ki:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Windows Active Directory használatával egy biztonságos fürthöz való kapcsolódáshoz
Ha az önálló fürtöt helyezünk üzembe AD biztonsági, csatlakozhat a fürthöz a kapcsoló "WindowsCredential" hozzáfűzésével.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Csatlakozhat a fürthöz a FabricClient API-k használatával
A Service Fabric SDK biztosítja a [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) osztály a fürt felügyeletéhez. A FabricClient API-kat használ, kérje le a Microsoft.ServiceFabric NuGet-csomagot.

### <a name="connect-to-an-unsecure-cluster"></a>Csatlakozás biztonságos fürthöz

Egy távoli nem biztonságos fürthöz való kapcsolódáshoz, FabricClient példányt hoz létre, és adja meg a fürt címét:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

A fürtön belül futó kód, például egy Reliable Service-ben hozzon létre egy FabricClient *nélkül* adja meg a fürt címét. FabricClient csatlakozik a csomóponton a kódot, jelenleg fut a helyi felügyeleti átjáró egy több hálózati Ugrás elkerülésére.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Egy olyan ügyféltanúsítvány használatával egy biztonságos fürthöz való kapcsolódáshoz

A fürt csomópontjainak rendelkeznie kell érvényes tanúsítványok amelynek köznapi név vagy az SAN DNS-név megjelenik a [RemoteCommonNames tulajdonság](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) beállított [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Ez a folyamat a következő lehetővé teszi, hogy az ügyfél és a fürt csomópontjai közötti kölcsönös hitelesítés.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Interaktív módon az Azure Active Directory használatával egy biztonságos fürthöz való kapcsolódáshoz

Az alábbi példa az ügyfél identitás- és kiszolgálói tanúsítvány a kiszolgáló identitását az Azure Active Directory.

Egy párbeszédablak automatikusan kap, miszerint az interaktív bejelentkezéshez a fürthöz való csatlakozáskor.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Nem interaktív módon az Azure Active Directory használatával egy biztonságos fürthöz való kapcsolódáshoz

Az alábbi példa támaszkodik a Microsoft.IdentityModel.Clients.ActiveDirectory, verzió: 2.19.208020213.

További információ az AAD token beszerzéséhez: [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Az Azure Active Directory előzetes metaadatok tudomása nélkül egy biztonságos fürthöz való kapcsolódáshoz

Az alábbi példa a nem interaktív token beszerzéséhez használja, de ugyanezzel a módszerrel hozhat létre egy egyéni interaktív token beszerzéséhez élmény használható. A token beszerzéséhez szükséges az Azure Active Directory-metaadatok olvasása az fürtkonfiguráció.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer használatával egy biztonságos fürthöz való kapcsolódáshoz
Elérni [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) egy adott fürtben, a pont a böngészőben:

`http://<your-cluster-endpoint>:19080/Explorer`

A teljes URL-címet is a fürt alapvető erőforrások panelje az Azure Portalon érhető el.

Windows- vagy OS X rendszeren egy böngészővel a biztonságos fürthöz való csatlakozáshoz, az ügyfél-tanúsítványt importálhatja, és a böngésző megkéri, a tanúsítvány a fürthöz való kapcsolódáshoz használandó.  Linuxos gépeken a tanúsítványt kell importálni használatával (minden böngésző rendelkezik használható különböző mechanizmusokat) speciális böngésző beállításait, majd mutasson a tanúsítvány helye a lemezen. Olvasási [ügyféltanúsítvány beállítása](#connectsecureclustersetupclientcert) további információt.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory használatával egy biztonságos fürthöz való kapcsolódáshoz

Az aad-vel védett fürthöz csatlakozhat a böngészőben a pont:

`https://<your-cluster-endpoint>:19080/Explorer`

Automatikusan kell kéri, jelentkezzen be aad-ben.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Egy olyan ügyféltanúsítvány használatával egy biztonságos fürthöz való kapcsolódáshoz

Szeretne csatlakozni egy fürtöt, amely a tanúsítványok védi, mutasson a böngészőben:

`https://<your-cluster-endpoint>:19080/Explorer`

Automatikusan kell kéri válasszon ügyféltanúsítványt.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>A távoli számítógépen ügyfél tanúsítvány beállítása

A fürt egy, a fürt és a kiszolgáló tanúsítványának és egy másik, az ügyfél hozzáférésének biztonságossá tételéhez legalább két tanúsítványokat kell használni.  Azt javasoljuk, hogy is használja további másodlagos tanúsítványokat és hozzáférési ügyféltanúsítványok.  Ügyfél és a egy fürt csomópontja, tanúsítvány-rendszerbiztonság használatával közötti kommunikáció védelméhez, először szerezze be és telepítheti az ügyféltanúsítványt. A tanúsítványt a személyes (saját) tárolóba a helyi számítógépen vagy a jelenlegi felhasználó is telepíthető.  A tanúsítvány ujjlenyomatát is, hogy az ügyfél hitelesíthető a fürt szükséges.

* Windows rendszeren: Kattintson duplán a PFX-fájlt, és kövesse az utasításokat követve telepítse a tanúsítványt a személyes tárban `Certificates - Current User\Personal\Certificates`. A PowerShell-parancsot is használhatja:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Ha önaláírt tanúsítványt, meg kell importálni kell a számítógép "megbízható személyek" tárolóban, mielőtt használhatná ezt a tanúsítványt egy biztonságos fürthöz való csatlakozáshoz.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Mac gépen: Kattintson duplán a PFX-fájlt, és kövesse az utasításokat a tanúsítvány a kulcskarikában telepítéséhez.

## <a name="next-steps"></a>További lépések

* [Service Fabric-fürt frissítési folyamat és az Ön elvárásainak](service-fabric-cluster-upgrade.md)
* [A Visual Studióban a Service Fabric-alkalmazások kezelése](service-fabric-manage-application-in-visual-studio.md)
* [A Service Fabric Health-modell bemutatása](service-fabric-health-introduction.md)
* [Alkalmazás biztonságának és futtató](service-fabric-application-runas-security.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
