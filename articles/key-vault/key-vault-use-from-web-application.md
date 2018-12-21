---
title: Az Azure Key Vault használata egy webalkalmazásból – oktatóanyag | Microsoft Docs
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Key Vaultot egy webalkalmazásból.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: barclayn
ms.openlocfilehash: 85eb7762437bff8d9f6f6a360fc9cc1ea2e6e145
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718214"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>Oktatóanyag: Az Azure Key Vault használata egy webalkalmazásból

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Key Vaultot egy webalkalmazásból az Azure-ban. Bemutatja, hogyan érhető el egy titkos kód egy Azure Key Vault-kulcstartóból egy webalkalmazásban való használathoz. Az oktatóanyag erre a folyamatra épül, és titkos ügyfélkulcs helyett tanúsítványt használ. Ez az oktatóanyag olyan webfejlesztők számára készült, akik tisztában vannak a webalkalmazások Azure-ban való létrehozásának alapjaival.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Alkalmazásbeállítások hozzáadása a web.config fájlhoz
> * Metódus hozzáadása hozzáférési jogkivonat lekéréséhez
> * A jogkivonat lekérése az alkalmazás indításakor
> * Hitelesítés tanúsítvánnyal

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő elemekre lesz szüksége:

* Egy Azure Key Vault-kulcstartóban tárolt titkos kulcshoz tartozó URI
* Az Azure Active Directoryban regisztrált és a Key Vaulthoz hozzáférő webalkalmazás ügyfél-azonosítója és titkos ügyfélkulcsa
* Egy webalkalmazás. Ez az oktatóanyag az Azure-ban webalkalmazásként üzembe helyezett ASP.NET MVC alkalmazás lépéseit mutatja be.

Végezze el a [Bevezetés az Azure Key Vault használatába](key-vault-get-started.md) című szakaszt egy titkos kód URI-jének, egy ügyfél-azonosítónak és egy titkos ügyfélkulcsnak a lekéréséhez, és regisztrálja az alkalmazást. A webalkalmazás hozzá fog férni a tárolóhoz, és regisztrálva kell lennie az Azure Active Directoryban. Hozzáférési jogosultságokkal is rendelkeznie kell a Key Vaulthoz. Ha nem így van, térjen vissza az első lépéseket ismertető oktatóanyag Alkalmazás regisztrálása című szakaszára, és ismételje meg az ott leírt lépéseket. További információ az Azure Web Apps létrehozásáról: [A Web Apps áttekintése](../app-service/overview.md).

Ez a minta az Azure Active Directory-identitások manuális kiépítésétől függ. Érdemes inkább az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) lehetőséget választani, amely automatikusan létrehozza az Azure AD-identitásokat. További információt [a GitHubon lévő mintában](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/), valamint a kapcsolódó, [az App Service és a Functions használatát ismertető oktatóanyagban](https://docs.microsoft.com/azure/app-service/overview-managed-identity) talál. Az [Azure-webalkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához](tutorial-web-application-keyvault.md) című oktatóanyag kifejezetten a Key Vaulttal foglalkozik.

## <a id="packages"></a>NuGet-csomagok hozzáadása

A webalkalmazásnak két csomagot kell telepítenie.

* Active Directory Authentication Library – az Azure Active Directoryval való kommunikációra és a felhasználó identitásának kezelésére szolgáló metódusokkal rendelkezik
* Azure Key Vault Library – az Azure Key Vaulttal való kommunikációra szolgáló metódusokkal rendelkezik

Ezek a csomagok az Install-Package paranccsal telepíthetők a csomagkezelő konzolon.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>A web.config fájl módosítása

Három alkalmazásbeállítást kell hozzáadnia a web.config fájlhoz a következőképpen. A tényleges értékeket az Azure Portalon fogjuk hozzáadni egy további biztonsági réteg létrehozásához.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>Metódus hozzáadása hozzáférési jogkivonat lekéréséhez

A Key Vault API használatához hozzáférési jogkivonatra van szükség. A Key Vault-ügyfél kezeli a Key Vault API-hívásokat, de ehhez meg kell adnia egy olyan függvényt, amely lekérdezi a hozzáférési jogkivonatot. A következő példa egy kód, amely hozzáférési jogkivonatot kér le az Azure Active Directoryból. Ez a kód az alkalmazásban bárhová kerülhet. Én Utils vagy EncryptionHelper osztályt szeretek hozzáadni.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>A titkos kód lekérése az alkalmazás indításakor

Most kóddal kell hívnunk a Key Vault API-t, hogy lekérjük a titkos kódot. Az alábbi kód bárhol elhelyezhető, ha azelőtt hívja meg, mielőtt szükség lenne a használatára. Ezt a kódot a Global.asax alkalmazásindítási eseményébe tettem, így egyszer fut az induláskor, és elérhetővé teszi a titkos kódot az alkalmazás számára.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Alkalmazásbeállítások hozzáadása az Azure Portalon

Az Azure-webalkalmazásban most hozzáadhatja az alkalmazásbeállítások tényleges értékeit az Azure Portalon. A lépés végrehajtásával a tényleges érték nem szerepel a web.config fájlban, hanem a portál védi, ahol Ön különálló hozzáférés-vezérlési képességekkel rendelkezik. A rendszer ezekkel az értékekkel helyettesíti a web.config fájlba írt értékeket. Győződjön meg arról, hogy a nevek egyeznek.

![Az Azure Portalon megjelenő alkalmazásbeállítások][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Hitelesítés tanúsítvánnyal titkos ügyfélkulcs helyett

Most, hogy megismerkedett az Azure AD-alkalmazás ügyfél-azonosítóval és titkos ügyfélkulccsal történő hitelesítésével, használjunk egy ügyfél-azonosítót és egy tanúsítványt. Ha tanúsítványt szeretne használni egy Azure-webalkalmazásban, használja a következő lépéseket:

1. Tanúsítvány beszerzése vagy létrehozása
2. Tanúsítvány társítása egy Azure AD-alkalmazáshoz
3. Kód hozzáadása a webalkalmazáshoz a tanúsítvány használatához
4. Tanúsítvány hozzáadása a webalkalmazáshoz

### <a name="get-or-create-a-certificate"></a>Tanúsítvány beszerzése vagy létrehozása

 Teszttanúsítványt készítünk ehhez az oktatóanyaghoz. Itt talál egy szkriptet önaláírt tanúsítvány létrehozásához. Módosítsa a könyvtárt arra a helyre, ahol létre szeretné hozni a tanúsítványfájlokat.  A tanúsítvány kezdő és záró dátumaként használhatja az aktuális dátumot plusz egy évet.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = 'KVWebApp.pfx'
$CerFilePath = 'KVWebApp.cer'
$DNSName = 'MyComputer.Contoso.com'
$Password = 'MyPassword"'

$StoreLocation = 'CurrentUser' #be aware that LocalMachine requires elevated privileges
$CertBeginDate = Get-Date
$CertExpiryDate = $CertBeginDate.AddYears(1)

$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\$StoreLocation\My" -NotBefore $CertBeginDate -NotAfter $CertExpiryDate -KeySpec Signature
Export-PfxCertificate -cert $Cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $Cert -FilePath $CerFilePath 
```

Jegyezze fel a záró dátum és a jelszót a .pfx (ebben a példában: 2019. május 15. és SajátJelszó). Ezekre az alábbi szkripthez lesz szükség. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Tanúsítvány társítása egy Azure AD-alkalmazáshoz

Most, hogy van egy tanúsítványa, társítsa egy Azure AD-alkalmazással. A társítás a PowerShellen keresztül végezhető el. Az alábbi parancsokkal társíthatja a tanúsítványt az Azure AD-alkalmazással:

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

A parancsok futtatása után láthatja az alkalmazást az Azure AD-ben. Az alkalmazásregisztrációk keresésekor győződjön meg arról, hogy a keresés párbeszédpanelen a „Minden alkalmazás” helyett a **Saját alkalmazások** van kiválasztva. 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Kód hozzáadása a webalkalmazáshoz a tanúsítvány használatához

Most kódot adunk a webalkalmazáshoz a tanúsítvány elérése érdekében, és hitelesítésre használjuk. 

Először van egy kód a tanúsítvány eléréséhez. Figyelje meg, hogy a StoreLocation a LocalMachine helyett CurrentUser értékű. És hogy „false” értéket adunk meg a Find metódushoz, mert teszttanúsítványt használunk.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



A következő a kód, amely a CertificateHelper elemet használja, és létrehozza a hitelesítéshez szükséges ClientAssertionCertificate elemet.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Itt látható a hozzáférési jogkivonat lekérésére szolgáló új kód. Ez a kód az előző példában szereplő GetToken elemet váltja fel. Az egyszerűség kedvéért más nevet adtam neki. Az egyszerű használat érdekében az összes kódot a webalkalmazás-projekt Utils osztályába tettem.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



Az utolsó kódmódosítás az Application_Start metódusban van. Először meg kell hívnunk a GetCert() metódust a ClientAssertionCertificate betöltéséhez. Ezután módosítjuk az új KeyVaultClient létrehozásakor megadott visszahívási metódust. Ez a kód lecseréli az előző példában szereplő kódot.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Tanúsítvány hozzáadása a webalkalmazáshoz az Azure Portalon

A tanúsítvány webalkalmazáshoz való hozzáadása egy egyszerű kétlépéses folyamat. Először lépjen az Azure Portalra, és keresse meg a webalkalmazást. A webalkalmazás Beállítások területén kattintson az **SSL-beállítások** elemre. Amikor megnyílik, töltse fel az előző példában létrehozott KVWebApp.pfx tanúsítványt. Fontos, hogy jegyezze meg a .pfx fájl jelszavát.

![Tanúsítvány hozzáadása egy webalkalmazáshoz az Azure Portalon][2]

Végül hozzá kell adnia egy alkalmazásbeállítást a WEBSITE\_LOAD\_CERTIFICATES nevű és * értékű webalkalmazáshoz. Ez a lépés gondoskodik róla, hogy az összes tanúsítvány be legyen töltve. Ha csak a feltöltött tanúsítványokat szeretné betölteni, akkor adja meg az ujjlenyomataik vesszővel elválasztott listáját.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor már nincs rájuk szükség, törölje az oktatóanyaghoz használt App Service-t, Key Vaultot és Azure AD-alkalmazást.  


## <a id="next"></a>Következő lépések
> [!div class="nextstepaction"]
>[Azure Key Vault felügyeleti API-referencia](/dotnet/api/overview/azure/keyvault/management).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 