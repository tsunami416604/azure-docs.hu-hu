---
title: Biztonságos csatlakozás egy Azure Service Fabric-fürthöz
description: Ez a témakör azt ismerteti, hogy miként lehet hitelesíteni az ügyfélhozzáférést egy Service Fabric-fürthöz, és hogyan lehet biztonságossá tenni az ügyfelek és a fürt közötti kommunikációt.
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: a1f4abbabe428a09492efefca4a8da9801b9f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258576"
---
# <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz

Amikor egy ügyfél csatlakozik egy Service Fabric-fürt csomóponthoz, az ügyfél hitelesíthető és biztonságos kommunikáció tanaként létesíthető tanúsítványbiztonság vagy az Azure Active Directory (AAD) használatával. Ez a hitelesítés biztosítja, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és az üzembe helyezett alkalmazásokhoz, és hajthassanak el felügyeleti feladatokat.  A tanúsítvány- vagy AAD-biztonság korábban engedélyezve kell lennie a fürtön a fürt létrehozásakor.  A fürtbiztonsági forgatókönyvekről a [Fürtbiztonság](service-fabric-cluster-security.md)című témakörben talál további információt. Ha tanúsítványokkal védett fürthöz csatlakozik, [állítsa be az ügyféltanúsítványt](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) a fürthöz csatlakozó számítógépen. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Csatlakozás biztonságos fürthöz az Azure Service Fabric CLI (sfctl) használatával

A Service Fabric CLI (sfctl) használatával többféleképpen is csatlakozhat egy biztonságos fürthöz. Ha a hitelesítés ügyféltanúsítvány használatával történik, a tanúsítvány adatainak meg kell egyeznie egy, a fürtcsomópontokon telepített tanúsítvány adataival. Ha a tanúsítvány hitelesítésszolgáltatókkal rendelkezik, meg kell adnia a megbízható hitelesítésszolgáltatókat is.

A `sfctl cluster select` parancs segítségével csatlakozhat egy fürthöz.

Az ügyféltanúsítványok két különböző módon is megadhatók, akár tanúsítványként, akár kulcspárként, akár egyetlen PFX-fájlként. A jelszóval védett PEM fájlok esetén a rendszer automatikusan kéri a jelszó megadását. Ha az ügyféltanúsítványt PFX-fájlként szerezte be, először konvertálja a PFX-fájlt PEM-fájllá a következő paranccsal. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Ha a .pfx fájl nem jelszóval védett, használja -passin pass: az utolsó paraméter.

Ha az ügyféltanúsítványt pem fájlként szeretné megadni, adja meg a fájl elérési útját az `--pem` argumentumban. Példa:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

A jelszóval védett pem fájlok a parancsok futtatása előtt jelszót kérnek.

Tanúsítvány megadásához a kulcspár `--cert` a `--key` és az argumentumokkal adja meg az egyes fájlok fájlelérési útjait.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Előfordulhat, hogy a teszt- vagy fejlesztőfürtök védelmére használt tanúsítványok nem felelnek meg a tanúsítványok érvényesítésének. A tanúsítványellenőrzés megkerüléséhez adja meg a `--no-verify` beállítást. Példa:

> [!WARNING]
> Ne használja `no-verify` ezt a beállítást éles Service Fabric-fürtökhöz való csatlakozáskor.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Ezenkívül megadhatja a megbízható hitelesítésszolgáltatók vagy az egyes cert-ek könyvtárainak elérési útjait is. Az elérési utak megadásához `--ca` használja az argumentumot. Példa:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

A csatlakozás után [más sfctl-parancsokat](service-fabric-cli.md) is futtathat a fürtkezeléséhez.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Csatlakozás fürthöz a PowerShell használatával
Mielőtt műveleteket hajtvégre a fürtön a PowerShellen keresztül, először hozzon létre egy kapcsolatot a fürttel. A fürtkapcsolat az adott PowerShell-munkamenet összes további parancsához használatos.

### <a name="connect-to-an-unsecure-cluster"></a>Csatlakozás nem biztonságos fürthöz

Nem biztonságos fürthöz való csatlakozáshoz adja meg a fürt végpontcímét a **Connect-ServiceFabricCluster** parancsnak:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Csatlakozás biztonságos fürthöz az Azure Active Directory használatával

Ha olyan biztonságos fürthöz szeretne csatlakozni, amely az Azure Active Directory használatával engedélyezi a fürt rendszergazdai hozzáférését, adja meg a fürttanúsítvány ujjlenyomatát, és használja az *AzureActiveDirectory-jelzőt.*  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Csatlakozás biztonságos fürthöz ügyféltanúsítvánnyal
Futtassa a következő PowerShell parancsot, hogy csatlakozzon egy biztonságos fürthöz, amely ügyféltanúsítványokat használ a rendszergazdai hozzáférés engedélyezéséhez. 

#### <a name="connect-using-certificate-common-name"></a>Csatlakozás tanúsítvány köznapi névvel
Adja meg a fürttanúsítvány köznapi nevét és a fürtkezelésre engedélyekkel rendelkező ügyféltanúsítvány köznapi nevét. A tanúsítvány részleteinek meg kell egyezniük a fürtcsomópontokon lévő tanúsítvánnyal.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*A ServerCommonName* a fürtcsomópontokra telepített kiszolgálói tanúsítvány köznapi neve. *A FindValue* a rendszergazdai ügyféltanúsítvány köznapi neve. A paraméterek kitöltésekénél a parancs a következő példához hasonlóan néz ki:
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

#### <a name="connect-using-certificate-thumbprint"></a>Csatlakozás tanúsítványujjlenyomattal
Adja meg a fürttanúsítvány ujjlenyomatát és a fürtkezelésre engedélyekkel rendelkező ügyféltanúsítvány ujjlenyomatát. A tanúsítvány részleteinek meg kell egyezniük a fürtcsomópontokon lévő tanúsítvánnyal.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*A ServerCertThumbprint* a fürtcsomópontokra telepített kiszolgálói tanúsítvány ujjlenyomata. *A FindValue* a rendszergazdai ügyféltanúsítvány ujjlenyomata.  A paraméterek kitöltésekénél a parancs a következő példához hasonlóan néz ki:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Csatlakozás biztonságos fürthöz a Windows Active Directory használatával
Ha az önálló fürt az AD-biztonság használatával van telepítve, csatlakozzon a fürthöz a "WindowsCredential" kapcsoló hozzáfűzésével.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Csatlakozás fürthöz a FabricClient API-k használatával
A Service Fabric SDK biztosítja a [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) osztály fürtkezelés. A FabricClient API-k használatához a Microsoft.ServiceFabric NuGet csomagot.

### <a name="connect-to-an-unsecure-cluster"></a>Csatlakozás nem biztonságos fürthöz

Távoli nem biztonságos fürthöz való csatlakozáshoz hozzon létre egy FabricClient-példányt, és adja meg a fürtcímét:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

A fürtön belül futó kód hoz létre egy FabricClient t a fürt címének megadása *nélkül.* FabricClient csatlakozik a helyi felügyeleti átjáró a csomóponton a kód jelenleg fut, elkerülve egy további hálózati ugrás.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Csatlakozás biztonságos fürthöz ügyféltanúsítvánnyal

A fürt csomópontjainak érvényes tanúsítvánnyal kell rendelkezniük, amelynek köznapi neve vagy DNS-neve a SAN-ban megjelenik a [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) [RemoteCommonNames tulajdonságában.](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) Ezt a folyamatot követően lehetővé teszi a kölcsönös hitelesítést az ügyfél és a fürt csomópontok között.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Csatlakozás biztonságos fürthöz interaktív módon az Azure Active Directory használatával

A következő példa az Azure Active Directoryt használja az ügyfélidentitáshoz és a kiszolgálói identitás kiszolgálói tanúsítványhoz.

A fürthöz való csatlakozáskor automatikusan megjelenik egy párbeszédablak az interaktív bejelentkezéshez.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Csatlakozás nem interaktív módon biztonságos fürthöz az Azure Active Directory használatával

A következő példa a Microsoft.IdentityModel.Clients.ActiveDirectory, Verzió: 2.19.208020213.

Az AAD tokenek beszerzéséről a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx)című témakörben talál további információt.

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Csatlakozás biztonságos fürthöz előzetes metaadat-ismeret nélkül az Azure Active Directory használatával

A következő példa nem interaktív jogkivonat-beszerzést használ, de ugyanazt a megközelítést lehet használni egy egyéni interaktív jogkivonat-létrehozási élmény létrehozásához. Az Azure Active Directory hoz szükséges metaadatok at token beszerzése a fürt konfigurációjából olvasható.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Csatlakozás biztonságos fürthöz a Service Fabric Intézővel
Ha egy adott [fürthöz](service-fabric-visualizing-your-cluster.md) szeretné elérni a Service Fabric Explorert, mutasson a böngészőre:

`http://<your-cluster-endpoint>:19080/Explorer`

A teljes URL-cím is elérhető az Azure Portal fürt essentials ablaktábláján.

Ha böngészővel szeretne biztonságos fürthöz csatlakozni Windows vagy OS X rendszeren, importálhatja az ügyféltanúsítványt, és a böngésző kérni fogja, hogy a tanúsítványt használja-e a fürthöz való csatlakozáshoz.  Linux-gépeken a tanúsítványt speciális böngészőbeállításokkal kell importálni (minden böngésző különböző mechanizmusokkal rendelkezik), és a lemezen lévő tanúsítvány helyére kell mutatni. További információ: [Ügyféltanúsítvány beállítása.](#connectsecureclustersetupclientcert)

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Csatlakozás biztonságos fürthöz az Azure Active Directory használatával

Ha AAD-vel védett fürthöz szeretne csatlakozni, mutasson a böngészőre:

`https://<your-cluster-endpoint>:19080/Explorer`

A rendszer automatikusan kéri, hogy jelentkezzen be az AAD-vel.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Csatlakozás biztonságos fürthöz ügyféltanúsítvánnyal

Ha tanúsítványokkal védett fürthöz szeretne csatlakozni, mutasson a böngészőre:

`https://<your-cluster-endpoint>:19080/Explorer`

A rendszer automatikusan kéri egy ügyféltanúsítvány kiválasztását.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Ügyféltanúsítvány beállítása a távoli számítógépen

Legalább két tanúsítványt kell használni a fürt védelméhez, egyet a fürt höz és a kiszolgálói tanúsítványhoz, egyet pedig az ügyfélhozzáféréshez.  Azt javasoljuk, hogy további másodlagos tanúsítványokat és ügyfél-hozzáférési tanúsítványokat is használjon.  Az ügyfél és a fürtcsomópont közötti kommunikáció tanúsítványbiztonsággal történő biztosításához először be kell szereznie és telepítenie kell az ügyféltanúsítványt. A tanúsítvány telepíthető a helyi számítógép vagy az aktuális felhasználó Személyes (Saját) tárolójába.  A kiszolgálói tanúsítvány ujjlenyomatára is szüksége van, hogy az ügyfél hitelesíthesse a fürtöt.

* Windows rendszeren: Kattintson duplán a PFX-fájlra, és kövesse a lépéseket a tanúsítvány a `Certificates - Current User\Personal\Certificates` személyes tárolóban való telepítéséhez. Másik lehetőségként használhatja a PowerShell parancsot:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Ha önaláírt tanúsítványról van szó, importálnia kell azt a számítógép "megbízható személyek" tárolójában, mielőtt ezzel a tanúsítvánnyal csatlakozhatna egy biztonságos fürthöz.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Mac rendszeren: Kattintson duplán a PFX-fájlra, és kövesse a lépéseket a tanúsítvány a kulcskarikában való telepítéséhez.

## <a name="next-steps"></a>További lépések

* [A Service Fabric fürtfrissítési folyamata és az Ön tőlünk kapott elvárások](service-fabric-cluster-upgrade.md)
* [A Service Fabric-alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)
* [A Service Fabric health modell bemutatása](service-fabric-health-introduction.md)
* [Alkalmazásbiztonság és futtatási adatok](service-fabric-application-runas-security.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
