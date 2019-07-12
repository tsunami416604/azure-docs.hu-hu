---
title: Az Azure Active Directoryval hitelesíti blob és üzenetsor adatok elérését az ügyfélalkalmazás
description: Azure Active Directory használatával egy ügyfélalkalmazás belül hitelesítéshez, az OAuth 2.0 jogkivonat beszerzése és az Azure Blob storage és Queue storage kérelmek engedélyezését végzi.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: da10b70b85e284173abbd1779fb1d39f477ca0cd
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723200"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Hozzáférés egy alkalmazás hitelesítése az Azure Active Directoryval, blobok és üzenetsorok

Azure Active Directory (Azure AD) használatával az Azure Blob storage és Queue storage egyik legfőbb előnye az, hogy a hitelesítő adatok már nem kell tárolni a kódban. Ehelyett az OAuth 2.0 hozzáférési jogkivonatban is kérhet a Microsoft identity platform (korábbi nevén az Azure AD). Az Azure AD akkor hitelesíti a rendszerbiztonsági tag (felhasználó, csoport vagy szolgáltatásnév) az alkalmazás futtatása. Ha a hitelesítés sikeres, az Azure AD az alkalmazáshoz a hozzáférési jogkivonatot ad vissza, és az alkalmazás használhatja a hozzáférési jogkivonatot az Azure Blob storage és Queue storage kérelmek hitelesítéséhez.

Ez a cikk bemutatja, hogyan konfigurálhatja a natív alkalmazás vagy webalkalmazás-hitelesítéshez és a Microsoft identity platform 2.0. A kód példa funkciók .NET, de más nyelv használata hasonló megközelítést. A Microsoft identity platform 2.0 kapcsolatos további információkért lásd: [a Microsoft identity platform (2.0-s verzió) – áttekintés](../../active-directory/develop/v2-overview.md).

Az OAuth 2.0 kód engedélyezési folyamatával áttekintését lásd: [hozzáférés engedélyezése az Azure Active Directory webes alkalmazásokhoz az OAuth 2.0-kód használatával adja meg a folyamat](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Szerepkör hozzárendelése egy Azure AD rendszerbiztonsági tag

Hitelesítést végezni az Azure Storage-alkalmazás a rendszerbiztonsági tag, először konfigurálnia kell a rendszerbiztonsági tag szerepköralapú hozzáférés-vezérlés (RBAC) beállításait. Az Azure Storage határozza meg a beépített RBAC-szerepkör, amely magában foglalja a tárolók és a várólisták engedélyeit. Az RBAC szerepkör van rendelve egy rendszerbiztonsági tag, amikor a rendszerbiztonsági tag hozzáférést ennek az erőforrásnak. További információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC Azure Blob és üzenetsor adatok](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Regisztrálja az alkalmazást az Azure AD-bérlő

Az első lépés az Azure AD-vel történő tárolási erőforrásokhoz való hozzáférés engedélyezésére az Azure AD-bérlő az ügyfélalkalmazás Regisztrálás a [az Azure portal](https://portal.azure.com). Ha regisztrálja az ügyfélalkalmazásban, meg kell adnia az Azure AD-alkalmazással kapcsolatos információkat. Az Azure AD majd biztosít egy ügyfél-azonosító (más néven egy *Alkalmazásazonosító*), hogy használhatja-e az alkalmazás társítása az Azure ad-vel futásidőben. Az ügyfél-azonosító kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](../../active-directory/develop/app-objects-and-service-principals.md).

Az Azure Storage-alkalmazás regisztrálásához hajtsa végre a lépéseket, látható [a rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platform az](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Az alábbi képen látható beállításokat egy webalkalmazás regisztrálásához:

![Képernyőfelvétel: a storage-alkalmazás regisztrálása az Azure ad-vel](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Natív alkalmazás regisztrálhatja alkalmazását, ha bármely érvényes URI-azonosítóját is megadhat a **átirányítási URI-t**. Natív alkalmazások esetében ezt az értéket nem kell valódi URL-cím. Webalkalmazások esetén az átirányítási URI-t kell lennie egy érvényes URI-t, mert azt adja meg az URL-címet, amelyre a jogkivonatok találhatók.

Miután regisztrálta az alkalmazást, láthatja az alkalmazás azonosítója (vagy ügyfél-azonosító) alatt **beállítások**:

![Képernyőfelvétel: az ügyfél-azonosító](./media/storage-auth-aad-app/app-registration-client-id.png)

Egy alkalmazás regisztrálása az Azure ad-vel kapcsolatos további információkért lásd: [alkalmazások integrálása az Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>A regisztrált alkalmazás engedélyek megadása az Azure Storage

Ezután adja meg az alkalmazás engedélyeit az Azure Storage API-k meghívására. Ebben a lépésben lehetővé teszi, hogy az alkalmazás az Azure Storage az Azure AD-kérések hitelesítéséhez.

1. Az a **áttekintése** regisztrált egy alkalmazást, válassza a lap **API-engedélyek megtekintése**.
1. Az a **API-engedélyek** szakaszban jelölje be **adjon hozzá egy engedélyt** válassza **Microsoft APIs**.
1. Válassza ki **Azure Storage** megjelenítendő eredmények listájában a **kérelem API-engedélyek** ablaktáblán.
1. A **milyen engedélyeket igényel az alkalmazás?** , ellenőrizze, hogy a rendelkezésre álló engedély típusa **delegált engedélyek**. Ez a beállítás alapértelmezés szerint meg van jelölve.
1. Az a **engedélyek kiválasztása** szakaszában a **kérelem API-engedélyek** ablaktáblán jelölje be a jelölőnégyzetet a **user_impersonation**, majd kattintson a **hozzáadása engedélyek**.

    ![Tároló ábrázoló engedélyeinek képernyőképe](media/storage-auth-aad-app/registered-app-permissions-1.png)

A **API-engedélyek** panelen most már látható, hogy a regisztrált Azure AD-alkalmazásokhoz férhet hozzá a Microsoft Graph és az Azure Storage. Engedélyek a Microsoft Graph automatikusan, ha az alkalmazás regisztrálása az Azure ad-ben.

![Képernyőfelvétel: regisztrálja Alkalmazásengedélyek](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Ügyfél titkos kulcs létrehozása

Az alkalmazásnak kell ügyfélkódot identitása igazolásához a jogkivonat kérése során. A titkos ügyfélkulcsot hozzáadásához kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás regisztrációját az Azure Portalon.
1. Válassza ki a **tanúsítványok és titkos kulcsok** beállítás.
1. A **ügyfél titkos kódok**, kattintson a **új titkos ügyfélkulcsot** hozhat létre egy új titkos kulcsot.
1. Adja meg a titkos kulcs leírását, és válassza ki a kívánt lejárati időközét.
1. Az új titkos kód közvetlenül egy biztonságos helyre másolja. A teljes érték jelenik meg, hogy csak egyszer.

    ![Ábrázoló képernyőkép titkos Ügyfélkód](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Klienskódtárak token beszerzése

Miután az alkalmazás regisztrálása és megadta annak ezt az adatok Azure Blob storage és Queue storage elérésére jogosult, kód is hozzáadhat egy rendszerbiztonsági tag hitelesíteni, és a egy OAuth 2.0 jogkivonat beszerzése az alkalmazás. Hitelesítéséhez, és beszerezni a jogkivonatot, vagy az egyik használhatja a [a Microsoft identity platform hitelesítési tárak](../../active-directory/develop/reference-v2-libraries.md) vagy egy másik olyan nyílt forráskódú kódtár, amely támogatja az OpenID Connect 1.0. Az alkalmazás a hozzáférési jogkivonat egy Azure Blob storage és Queue storage kérelem engedélyezéséhez használhatja.

Amelyhez-jogkivonatok beszerzésének támogatott forgatókönyvek listája: a [forgatókönyvek](https://aka.ms/msal-net-scenarios) szakaszában a [Microsoft hitelesítési tár (MSAL) a .NET-hez](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub-adattárban.

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód példa: Egy blokkblob létrehozása

A kód példa bemutatja, hogyan kaphat hozzáférési tokent az Azure ad-ből. A hozzáférési jogkivonatot a megadott felhasználó hitelesítéséhez és majd a blokkblob létrehozására irányuló kérelem engedélyezéséhez használatos. A minta működéséhez hajtsa végre a fenti szakaszokban ismertetett lépéseket.

A jogkivonat kéréséhez, a következő értékeket az alkalmazás regisztrációját a lesz szüksége:

- Az Azure AD-tartomány neve. Ezt az értéket beolvasni a **áttekintése** az Azure Active Directory lapján.
- A bérlő (vagy a könyvtár) azonosítója. Ezt az értéket beolvasni a **áttekintése** az alkalmazás regisztrációs lapján.
- Az ügyfél (vagy alkalmazás) azonosítója. Ezt az értéket beolvasni a **áttekintése** az alkalmazás regisztrációs lapján.
- Az ügyfél átirányítási URI. Ezt az értéket beolvasni a **hitelesítési** beállításait az alkalmazás regisztrációját.
- A titkos ügyfélkulcsot értéke. Ez az érték beolvasása, amelyre korábban kimásolt azt a helyet.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Az Azure AD-hitelesítés az ismert értékek

A rendszerbiztonsági tag az Azure AD-hitelesítés, néhány jól ismert értéket is a kódban kell.

#### <a name="azure-ad-authority"></a>Az Azure AD-szolgáltatót

A Microsoft nyilvános felhő, az alapszintű Azure AD-szolgáltatót a következő, ahol *bérlőazonosító* az Active Directory-bérlő azonosítója (vagy a címtár-azonosító):

`https://login.microsoftonline.com/<tenant-id>/`

A bérlő Azonosítóját az Azure AD-bérlő, a hitelesítéshez használandó azonosítja. Azt is nevezzük a címtár-azonosító A Bérlőazonosító lekéréséhez lépjen a **áttekintése** az Azure Portalon az alkalmazás regisztrációs lapján, és másolja az értéket onnan.

#### <a name="storage-resource-id"></a>Storage erőforrás-azonosító

Az Azure Storage erőforrás-azonosítója használatával, amelyek engedélyezik a kéréseket az Azure Storage-jogkivonat beszerzése:

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>Hozzon létre egy tárfiókot és tárolót

A mintakód futtatásához, hozzon létre egy tárfiókot, mint az Azure Active Directory ugyanazon az előfizetésen belül. Ezután hozzon létre egy tárolót a storage-fiókon belül. A mintakód létrehoz egy blokkblobot ebben a tárolóban.

Ezután explicit módon hozzárendelheti az **Storage-Blobadatok Közreműködője** szerepkört ahhoz a felhasználói fiókhoz, amely alatt futni fog a mintakódot. Az Azure Portalon a szerepkör hozzárendelése, lásd: [hozzáférést biztosít az Azure blob és üzenetsor az adatokat az RBAC az Azure Portalon](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Amikor létrehoz egy Azure Storage-fiókot, akkor nem lesznek automatikusan hozzárendelve engedélyeket az eléréséhez az Azure AD-n keresztül adatokat. Kell explicit módon saját magának egy RBAC szerepkör hozzárendelése az Azure Storage. Az előfizetés, erőforráscsoport, tárfiók, vagy a tároló vagy üzenetsor szintjén rendelhet.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Hozzon létre egy webalkalmazást, amely engedélyezi a hozzáférést a Blob storage, Azure AD-vel

Ha az alkalmazás eléri az Azure Storage, azt, nem pedig a felhasználó nevében, ami azt jelenti, hogy a blob és üzenetsor erőforrásaihoz hozzáférnek, a bejelentkezett felhasználó engedélyeivel használatával. Ez a Kódpélda próbálkozzon, egy webalkalmazást, amely kéri a felhasználót, jelentkezzen be az Azure AD identitás van szükség. Hozzon létre egy saját, vagy a Microsoft által biztosított mintaalkalmazás használja.

A befejezett minta-webalkalmazáshoz, amely tokenbeolvasás és a egy blob létrehozása az Azure Storage segítségével érhető el a [GitHub](https://aka.ms/aadstorage). Felülvizsgálata és a befejezett mintakódot futtató példakódok megértéséhez hasznos lehet. Az elkészült mintát futtatásával kapcsolatos útmutatásért lásd: részre [nézet, és futtassa az elkészült mintát](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Mutató hivatkozásokat tudjon felvenni, és utasításokkal  

A Visual Studióból telepítse az Azure Storage ügyféloldali kódtárat. Az a **eszközök** menüjében válassza **Nuget-Csomagkezelő**, majd **Package Manager Console**. Írja be a következő parancsokat a konzolablakban a szükséges csomagok telepítéséhez az Azure Storage ügyféloldali kódtára a .NET-hez készült be:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Ezután adja hozzá a következő using utasításokat a HomeController.cs fájlba:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Egy blokkblob létrehozása

Adja hozzá az alábbi kódrészlet egy blokkblob létrehozása:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Az OAuth 2.0 jogkivonat blob és üzenetsor-műveletek engedélyezése, a HTTPS kell használnia.

A fenti példában a .NET ügyféloldali kódtár kezeli a blokkblob típusú létrehozására vonatkozó kérelem engedélyezése. Az Azure Storage ügyfélkódtáraival más nyelven is kezelni az engedélyt Önnek a kérelem. Azonban ha egy Azure Storage műveletet hívja meg az OAuth-token, a REST API használatával, majd szüksége lesz az OAuth-jogkivonat használatával a kérelem engedélyezéséhez.

Az OAuth hozzáférési tokenek Blob és üzenetsor-szolgáltatás műveletek hívására, át kell adnia a hozzáférési jogkivonat a **engedélyezési** fejléc használatával a **tulajdonosi** séma, és adja meg a szolgáltatás verziója 2017-11-09 vagy nagyobb, mint az alábbi példában látható:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Az OAuth jogkivonat beszerzése az Azure ad-ből

Ezután adjon meg egy metódust, amely az Azure ad-ből tokent kér. A kért jogkivonatot lesz a felhasználó nevében, és a GetTokenOnBehalfOfUser módszert használjuk.

Ne feledje, hogy ha a legutóbb bejelentkezett, és a egy jogkivonatot a kért a `storage.azure.com` erőforrás, szüksége lesz a felhasználó a felhasználói felület, ahol a felhasználók engedélyezhetik, hogy egy műveletet, a felhasználók nevében jelenthet. Megkönnyítése érdekében, hogy szeretne-e a tényleges a `MsalUiRequiredException` és bővítik igénylése felhasználói beleegyezés, az alábbi példában látható módon:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Hozzájárulás az a folyamat egy felhasználó egy alkalmazást, hogy a felhasználók nevében védett erőforrások eléréséhez engedély. A Microsoft identity platform 2.0 támogatja a növekményes jóváhagyás, ami azt jelenti, hogy a rendszerbiztonsági tag minimális engedélykészletet kezdetben igényelheti és engedélyek hozzáadása idővel, igény szerint. Amikor a kód egy hozzáférési jogkivonatot kér, adja meg az alkalmazás által igényelt egy adott időpontban szerint az engedélyek hatóköre a `scope` paraméter. Növekményes hozzájárulási kapcsolatos további információkért lásd: részre **növekményes és a dinamikus hozzájárulási** a [miért frissítse a Microsoft identity platform (2.0-s verzió)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

A következő metódust a hitelesítési tulajdonságokat növekményes hozzájárulás kérése hoz létre:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Megtekintheti, és a befejezett minta futtatása

Futtassa a mintaalkalmazást, először klónoznia, vagy töltse le [GitHub](https://aka.ms/aadstorage). Frissítse az alkalmazást a következő szakaszokban leírtak szerint.

### <a name="provide-values-in-the-settings-file"></a>Adja meg a beállítások fájlban

Ezután frissítse a *appsettings.json* fájlt a saját értékeire a következőképpen:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Frissítés a storage-fiók és tároló neve

Az a *HomeController.cs* fájlt, frissítse az URI-t, amely hivatkozik a blokkblobok használata a tárfiók és tároló neve:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Implicit engedélyezési folyamat engedélyezése

A minta futtatásához szükség lehet az alkalmazás regisztrációját a típusú implicit engedélyezés folyamat konfigurálásához. Kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás regisztrációját az Azure Portalon.
1. A kezelés területen válassza ki a **hitelesítési** beállítás.
1. A **speciális beállítások**, a a **típusú Implicit engedélyezés** részen jelölje be a jelölőnégyzeteket, engedélyezheti a hozzáférési jogkivonatok és azonosító-jogkivonatokat, az alábbi képen látható módon:

    ![Képernyőfelvétel: a beállítások implicit engedélyezési folyamat engedélyezése](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Frissítés a portot használják a localhost

A minta futtatásakor, hogy az átirányítási URI használata az alkalmazás regisztrációját a megadott frissíteni szeretné tapasztalhatja a *localhost* futásidőben hozzárendelt port. Az átirányítási URI-t, a hozzárendelt port használatára való frissítéséhez kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás regisztrációját az Azure Portalon.
1. A kezelés területen válassza ki a **hitelesítési** beállítás.
1. A **átirányítási URI-k**, szerkessze a portot, úgy, hogy a mintaalkalmazás által használt az alábbi képen látható módon:

    ![Képernyőfelvétel: az alkalmazás regisztrációs átirányítási URI-k](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>További lépések

- A Microsoft identity platform kapcsolatos további információkért lásd: [Microsoft identitásplatformja](https://docs.microsoft.com/azure/active-directory/develop/).
- Az Azure storage szolgáltatáshoz az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC tárolási adatok](storage-auth-aad-rbac.md).
- Felügyelt identitások az Azure-erőforrások az Azure Storage használatával kapcsolatos további információkért lásd: [hitelesíti a blobok és üzenetsorok az Azure Active Directory és a felügyelt identitásokból hozzáférést az Azure-erőforrások](storage-auth-aad-msi.md).
