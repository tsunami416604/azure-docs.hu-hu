---
title: "Használja az Azure Key Vault-webalkalmazások |} Microsoft Docs"
description: "Ez az oktatóanyag segítségével megtudhatja, hogyan használható az Azure Key Vault egy webalkalmazás."
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 09/15/2017
ms.author: adhurwit
ms.openlocfilehash: 1846305e6834145046cf9903714c68e9a6fd4f7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Használja az Azure Key Vault-webalkalmazások

## <a name="introduction"></a>Bevezetés

Ez az oktatóanyag segítségével az Azure Key Vault használata egy webalkalmazás az Azure-ban. Végigvezeti a titkos kulcs eléréséhez, hogy a webes alkalmazás használható az Azure Key Vault folyamatán.

**Oktatóanyag áttekintésének várható időtartama:** 15 perc

Áttekintést az Azure Key Vaultról a [What is Azure Key Vault?](key-vault-whatis.md) (Mi az Azure Key Vault?) című cikkben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez szüksége lesz:

* Egy URI-t az Azure Key Vault a titkos kulcs
* Az Azure Active Directoryval, a kulcstároló eléréséhez használt regisztrált egy ügyfél-Azonosítót és egy Ügyfélkulcsot webalkalmazás
* A webalkalmazás. Fogja azt megjelenítő webalkalmazásként Azure szolgáltatásba telepített ASP.NET MVC alkalmazásnak az lépéseit.

>[!IMPORTANT]
>* Ez a minta egy régebbi módja a manuális létesítési AAD identitások függ. Jelenleg egy új szolgáltatás nevű kép [felügyelt szolgáltatás identitásának (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), amely automatikusan telepíthetik AAD identitások. Tekintse meg az alábbi minta a [github](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) további tájékoztatást talál.

> [!NOTE]
>* Fontos, hogy végrehajtotta a felsorolt [Ismerkedés az Azure Key Vault](key-vault-get-started.md) ehhez az oktatóanyaghoz, hogy az URI-t a titkos kulcs és az ügyfél-azonosító és a titkos ügyfélkódot webalkalmazás.


A webes alkalmazás, amely a Key Vault hozzáférhetnek az Azure Active Directoryban regisztrálva van, és megkapta-e hozzáféréssel a Key Vault a. Ha nem ez a helyzet, vissza regisztrálása a az alkalmazás az első lépéseket bemutató oktatóanyaghoz, és ismételje meg a ismertetett lépéseket.

Ez az oktatóanyag célja alapjainak webalkalmazások létrehozása az Azure webalkalmazás-fejlesztőknek. Azure Web Apps kapcsolatos további információkért lásd: [webalkalmazások áttekintése](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Adja hozzá a Nuget-csomagok

Két csomagot, amelyet a webes alkalmazás telepítve van.

* Active Directory Authentication Library - Azure Active Directoryban való interakció és az identitásfelügyelet metódust tartalmaz.
* Az Azure Key Vault Library - kommunikáció az Azure Key Vault metódust tartalmaz.

Mindkét ezeket a csomagokat is telepíthető, a Package Manager konzolon az Install-Package paranccsal.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Web.Config módosítása

Nincsenek három-alkalmazásbeállításokat, amelyek hozzá kell adni a web.config fájlban az alábbiak szerint.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Ha nem kívánja futtatni, Azure Web Apps, majd adjon a tényleges ClientId, a titkos Ügyfélkulcs és a titkos kulcs URI-értékek a Web.config fájlban. Egyéb hagyja ki ezeket az üres értékeket, mert bővítjük a tényleges értékek egy további szintű biztonság Azure-portálon.

## <a id="gettoken"></a>Módszer segítségével Access Token hozzáadása

A Key Vault API használatához szükséges olyan hozzáférési jogkivonatot. A Key Vault ügyfél kezeli a Key Vault API-hívások, de kell megadnia, az egy függvénynek, amely lekérdezi a hozzáférési jogkivonat.  

Az alábbiakban olvashatja a kódot, és szerezze be a hozzáférési tokent az Azure Active Directoryból. Ez a kód bárhol lépjen az alkalmazásban. Szeretnék Utils vagy EncryptionHelper osztályt.  

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
```

> [!NOTE]
>* Jelenleg a hitelesítés legegyszerűbb módja a Felügyeltszolgáltatás-identitás (Managed Service Identity, MSI) nevű új funkció. További részletekért tekintse meg a következő példát, amely egy [.NET-alapú alkalmazásban található MSI-vel rendelkező kulcstartót](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) használ, valamint tekintse meg az [MSI App Service és Functions szolgáltatásokkal való használatával foglalkozó](https://docs.microsoft.com/en-us/azure/app-service/app-service-managed-service-identity) oktatóanyagot. 
>* Ügyfél-azonosító és a titkos ügyfélkódot használata a hitelesítés az Azure AD-alkalmazást egy másik módja. És használja azt a webes alkalmazásban és a kulcskezelést teljesebb körű vezérlése kizárás lehetővé teszi. De azt alapulnak, hogy a titkos Ügyfélkulcs és a konfigurációs beállításaiban, amely az egyes szerint kockázatos, ha a titkos kulcsot, amely a konfigurációs beállításokat a védeni kívánt lehet. Egy ügyfél-azonosító és a tanúsítvány használata helyett az ügyfél-azonosító és a titkos ügyfélkódot hitelesítéséhez az Azure AD-alkalmazás leírását a következő kapcsolatban.

## <a id="appstart"></a>A titkos kulcsot, az alkalmazás lépések beolvasása

Most szükséges kód a Key Vault API és a titkos kulcs beolvasása. Az alábbi kód bárhova helyezhető, mindaddig, amíg előtt kell használnia az nevezik. Ezzel a kóddal rendelkezik I az alkalmazás indítás esemény a Global.asax helyezze el, hogy a kezdőlapon egyszer fut, és elérhetővé teszi a titkos kulcsot az alkalmazáshoz.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Alkalmazásbeállítások hozzáadása az Azure portálon (nem kötelező)

Ha az Azure Web Apps, az Azure-portálon AppSettings hozzáadhat a tényleges értékek. Ezzel a tényleges értékek nem lesz a Web.config fájlban, de a portálon, melyekben külön hozzáférés-vezérlés képességeinek védett. Ezeket az értékeket a Web.config fájlban megadott értékek kerülnek behelyettesítésre. Győződjön meg arról, hogy a nevek megegyeznek-e.

![Az Azure portál megjeleníti-Alkalmazásbeállítások][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>A hitelesítést egy tanúsítványt a titkos Ügyfélkulcsot helyett

Egy Azure AD-alkalmazást hitelesítéséhez egy másik úgy, hogy egy ügyfél-Azonosítót és a tanúsítvány helyett egy ügyfél-azonosító és a titkos ügyfélkódot. A tanúsítvány használata az Azure Web Apps lépései a következők:

1. Futtasson, vagy hozzon létre egy tanúsítványt
2. A tanúsítvány társítása egy Azure AD-alkalmazást
3. Kód felvétele a webalkalmazás a tanúsítvány használatára
4. Tanúsítvány hozzáadása a webalkalmazáshoz

### <a name="get-or-create-a-certificate"></a>Futtasson, vagy hozzon létre egy tanúsítványt

Egy teszttanúsítványt használunk a célokra. Az alábbiakban néhány parancsokat, amelyek a fejlesztői parancssor segítségével hozzon létre egy tanúsítványt. Módosítsa a könyvtárat, ahová a létrehozott tanúsítvány fájlok.  Emellett a kezdő és záró dátumot a tanúsítvány, használja az aktuális dátum plusz 1 év.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 03/07/2017 -e 03/07/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Jegyezze fel a záró dátum és a jelszót a .pfx (ebben a példában: 07/31/2016 és test123). Szüksége lesz rájuk az alábbi.

Teszttanúsítványt létrehozásával kapcsolatos további információkért lásd: [Útmutató: A saját tesztelése tanúsítvány létrehozása](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>A tanúsítvány társítása egy Azure AD-alkalmazást

Most, hogy rendelkezik egy tanúsítvánnyal, rendelje hozzá azt egy Azure AD-alkalmazást szeretné. Jelenleg az Azure-portál nem támogatja a munkafolyamat; Ez a PowerShell használatával elvégezhető. Futtassa a következő parancsok futtatásával assoicate a tanúsítvány az Azure AD-alkalmazást:

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Ezek a parancsok futtatása után megtekintheti az alkalmazást az Azure ad-ben. Ha keres, győződjön meg arról, "A vállalat tulajdonában lévő alkalmazások" helyett "Alkalmazások a vállalat használ" a keresés párbeszédpanelen válasszon.

Az Azure AD-alkalmazás objektumának és szolgáltatásnév objektumok kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Kód felvétele a webalkalmazás a tanúsítvány használatára

Most azt felveszi kód a webalkalmazásban a cert eléréséhez, és a hitelesítéshez használandó.

Először nincs kódot, amely a tanúsítvány.

```cs
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

Vegye figyelembe, hogy a StoreLocation – CurrentUser LocalMachine helyett. És, hogy azt is biztosítja "false", a keresés metódus, mert a vizsgálati cert használjuk.

Ezután van, amely a CertificateHelper használ, és létrehoz egy ClientAssertionCertificate, amelyre szükség van hitelesítési kódot.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Ez az új kód segítségével kérheti le a hozzáférési jogkivonat. Ez a felváltja az előző példában GetToken metódust. I adott azt egy másik nevet kényelmét szolgálja.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

I rendelkezik helyezték összes ezt a kódot a webalkalmazás projekt Utils osztály a használat megkönnyítése érdekében.

Az utolsó kódváltoztatást az Application_Start metódus van. Először igazolnia kell betölteni a ClientAssertionCertificate GetCert() metódust. És majd a visszahívási metódus, amely azt adja meg, amikor hoz létre egy új KeyVaultClient módosítjuk. Vegye figyelembe, hogy ez a felváltja a kódot, amely azt az előző példában rendelkezett.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>A tanúsítvány felvétele a webalkalmazás az Azure portálon keresztül

A tanúsítvány felvétele a webalkalmazás egyszerű lépésből áll. Először nyissa meg az Azure portálra, és keresse meg a webes alkalmazást. A webalkalmazás beállítási paneljén kattintson az "egyéni tartományok és SSL" bejegyzés. A panelen, hogy megnyílik fogja tölthet fel a létrehozott tanúsítványát, amelyet az előző példában KVWebApp.pfx, győződjön meg arról, hogy ne felejtse el a jelszót a PFX.

![A tanúsítvány felvétele egy webalkalmazást az Azure-portálon][2]

A legutolsó dolog, hogy végre kell hajtani, hogy hozzáadjon egy alkalmazásbeállítást a webes alkalmazás, amelynek a neve webhely\_terhelés\_TANÚSÍTVÁNYOKAT és a *. Ezzel biztosíthatja, hogy minden tanúsítvány vannak betöltve. Ha csak a feltöltött tanúsítványok betölteni, ezután megadhatja az ujjlenyomatok vesszővel tagolt listája.

A tanúsítvány felvétele a webes alkalmazás kapcsolatos további információkért lásd: [tanúsítványok használata az Azure-webhelyek alkalmazásokban](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>A tanúsítvány felvétele a Key Vault egy titkos kulcsként

Ahelyett, hogy a tanúsítványt közvetlenül a Web App service feltölteni, tárolja egy titkos kulcsként kulcstároló, és telepítse onnan. Ez az egy kétlépéses folyamat a következő blogbejegyzésben [telepítése Azure Web App tanúsítványt Key Vault keresztül](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Következő lépések

Programozási hivatkozások: [Azure Key Vault C# ügyfél API-referencia](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
