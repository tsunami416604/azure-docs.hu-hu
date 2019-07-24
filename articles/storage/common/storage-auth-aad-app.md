---
title: Hitelesítés a Azure Active Directoryekkel az ügyfélalkalmazás blob-és üzenetsor-adatainak eléréséhez
description: A Azure Active Directory használatával végezhet hitelesítést egy ügyfélalkalmazás alapján, beszerezzen egy OAuth 2,0-tokent, és engedélyezi a kérelmeket az Azure Blob Storage-hoz és a várólista-tárolóhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/15/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ffae7467e9f94c68cf004b74c9791f2d9cda3171
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250006"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Hitelesítés Azure Active Directory alkalmazással a blobokhoz és a várólistákhoz való hozzáféréshez

A Azure Active Directory (Azure AD) az Azure Blob Storage vagy a üzenetsor Storage szolgáltatással való használatának egyik legfőbb előnye, hogy a hitelesítő adatait már nem kell a kódban tárolni. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformról (korábban Azure AD). Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD visszaadja a hozzáférési jogkivonatot az alkalmazásnak, és az alkalmazás a hozzáférési token használatával engedélyezheti a kérelmeket az Azure Blob Storage-ba vagy a várólista-tárolóba.

Ez a cikk bemutatja, hogyan konfigurálhatja a natív alkalmazást vagy webalkalmazást a Microsoft Identity platform 2,0-alapú hitelesítéshez. A kód példa funkciók .NET, de más nyelv használata hasonló megközelítést. A Microsoft Identity platform 2,0-es verziójával kapcsolatos további információkért lásd: [Microsoft Identity platform (v 2.0) – áttekintés](../../active-directory/develop/v2-overview.md).

Az OAuth 2.0 kód engedélyezési folyamatával áttekintését lásd: [hozzáférés engedélyezése az Azure Active Directory webes alkalmazásokhoz az OAuth 2.0-kód használatával adja meg a folyamat](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Szerepkör kiosztása egy Azure AD rendszerbiztonsági tag számára

Hitelesítést végezni az Azure Storage-alkalmazás a rendszerbiztonsági tag, először konfigurálnia kell a rendszerbiztonsági tag szerepköralapú hozzáférés-vezérlés (RBAC) beállításait. Az Azure Storage olyan beépített RBAC-szerepköröket határoz meg, amelyek magukban foglalják a tárolók és a várólisták engedélyeit. Az RBAC szerepkör van rendelve egy rendszerbiztonsági tag, amikor a rendszerbiztonsági tag hozzáférést ennek az erőforrásnak. További információ: a [hozzáférési jogosultságok kezelése az Azure Blob és a üzenetsor adatai számára a RBAC használatával](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Regisztrálja az alkalmazást az Azure AD-bérlő

Az Azure AD-nek a tárolási erőforrásokhoz való hozzáférés engedélyezésére való használatának első lépéseként regisztrálja az ügyfélalkalmazás egy Azure AD-Bérlővel a [Azure Portal](https://portal.azure.com). Az ügyfélalkalmazás regisztrálása során információkat nyújt az alkalmazásról az Azure AD-hez. Az Azure AD majd biztosít egy ügyfél-azonosító (más néven egy *Alkalmazásazonosító*), hogy használhatja-e az alkalmazás társítása az Azure ad-vel futásidőben. Az ügyfél-azonosító kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](../../active-directory/develop/app-objects-and-service-principals.md).

Az Azure Storage-alkalmazás regisztrálásához kövesse a gyors útmutatóban [megjelenő lépéseket: Alkalmazás regisztrálása a Microsoft Identity platformon](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Az alábbi képen a webalkalmazások regisztrálásának általános beállításai láthatók:

![A Storage-alkalmazás Azure AD-vel való regisztrálását bemutató képernyőkép](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Natív alkalmazás regisztrálhatja alkalmazását, ha bármely érvényes URI-azonosítóját is megadhat a **átirányítási URI-t**. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI azonosítónak érvényes URI-nak kell lennie, mert meghatározza azt az URL-címet, amelyhez a tokenek meg vannak határozva.

Miután regisztrálta az alkalmazást, láthatja az alkalmazás azonosítója (vagy ügyfél-azonosító) alatt **beállítások**:

![Az ügyfél-azonosítót ábrázoló képernyőkép](./media/storage-auth-aad-app/app-registration-client-id.png)

Egy alkalmazás regisztrálása az Azure ad-vel kapcsolatos további információkért lásd: [alkalmazások integrálása az Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>A regisztrált alkalmazás engedélyek megadása az Azure Storage

Ezután adja meg az alkalmazás engedélyeit az Azure Storage API-k meghívásához. Ez a lépés lehetővé teszi, hogy az alkalmazás engedélyezze az Azure Storage-ba irányuló kéréseket az Azure AD-vel.

1. A regisztrált alkalmazás **Áttekintés** lapján válassza az **API-engedélyek megtekintése**lehetőséget.
1. Az **API-engedélyek** szakaszban válassza az **engedély hozzáadása** lehetőséget, és válassza a **Microsoft API**-k elemet.
1. Az eredmények listájában válassza az **Azure Storage** lehetőséget, hogy megjelenjen a **kérelem API-engedélyei** panel.
1. **Milyen típusú engedélyek szükségesek az alkalmazáshoz?** , figyelje meg, hogy a rendelkezésre álló engedélyek típusa delegált **engedély**. Ez a beállítás alapértelmezés szerint be van jelölve.
1. A **kérelem API-engedélyei** ablaktábla **engedélyek kiválasztása** szakaszában jelölje be a **user_impersonation**melletti jelölőnégyzetet, majd kattintson az **engedélyek hozzáadása**elemre.

    ![A tárterületre vonatkozó engedélyeket megjelenítő képernyőkép](media/storage-auth-aad-app/registered-app-permissions-1.png)

Az **API-engedélyek** ablaktábla mostantól azt mutatja, hogy a regisztrált Azure ad-alkalmazás Microsoft Graph és az Azure Storage szolgáltatáshoz is hozzáférhet. Az Azure AD-vel való első regisztráláskor az engedélyek Microsoft Graph automatikusan megadhatók.

![Az alkalmazásra vonatkozó engedélyek regisztrálását bemutató képernyőkép](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Az alkalmazásnak szüksége van egy ügyfél titkos kulcsára, hogy igazolja az identitását a jogkivonat kérésekor. Az ügyfél titkos kulcsának hozzáadásához kövesse az alábbi lépéseket:

1. Navigáljon az alkalmazás regisztrálásához a Azure Portal.
1. Válassza ki a **tanúsítványok & titkok** beállítást.
1. Az **ügyfél**titkos kulcsa területen kattintson az **új ügyfél titka** lehetőségre új titkos kulcs létrehozásához.
1. Adja meg a titkos kulcs leírását, és válassza ki a kívánt lejárati időközt.
1. Az új titok értékének azonnali másolása biztonságos helyre. A teljes érték csak egyszer jelenik meg.

    ![Az ügyfél titkos kulcsát ábrázoló képernyőfelvétel](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Az ügyfél kódtárai a tokenek beszerzéséhez

Miután regisztrálta az alkalmazást, és engedélyt kapott az Azure Blob Storage-ban vagy a várólista-tárolóban lévő adathozzáférésre, hozzáadhat programkódot az alkalmazáshoz egy rendszerbiztonsági tag hitelesítéséhez és egy OAuth 2,0 token beszerzéséhez. A jogkivonat hitelesítéséhez és beszerzéséhez használhatja a [Microsoft Identity platform hitelesítési kódtárainak](../../active-directory/develop/reference-v2-libraries.md) egyikét, vagy egy másik, az OpenID Connect 1,0-et támogató nyílt forráskódú könyvtárat. Az alkalmazás ezután a hozzáférési token használatával engedélyezheti a kérést az Azure Blob Storage-ban vagy a várólista-tárolóban.

A jogkivonatok beszerzését támogató forgatókönyvek listáját a [Microsoft Authentication Library (MSAL) a .net GitHub-](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) adattárhoz [című szakaszában találja](https://aka.ms/msal-net-scenarios) .

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Az Azure AD-hitelesítés az ismert értékek

A rendszerbiztonsági tag az Azure AD-hitelesítés, néhány jól ismert értéket is a kódban kell.

### <a name="azure-ad-authority"></a>Az Azure AD-szolgáltatót

A Microsoft nyilvános felhő, az alapszintű Azure AD-szolgáltatót a következő, ahol *bérlőazonosító* az Active Directory-bérlő azonosítója (vagy a címtár-azonosító):

`https://login.microsoftonline.com/<tenant-id>/`

A bérlő Azonosítóját az Azure AD-bérlő, a hitelesítéshez használandó azonosítja. Más néven a címtár-azonosító. A bérlő AZONOSÍTÓjának lekéréséhez navigáljon a Azure Portalban az alkalmazás regisztrációjának **Áttekintés** lapjára, és onnan másolja az értéket.

### <a name="azure-storage-resource-id"></a>Azure Storage-erőforrás azonosítója

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód – példa: Blokkos blob létrehozása

A kód példa bemutatja, hogyan kaphat hozzáférési tokent az Azure ad-ből. A hozzáférési jogkivonatot a megadott felhasználó hitelesítéséhez és majd a blokkblob létrehozására irányuló kérelem engedélyezéséhez használatos. A minta működéséhez hajtsa végre a fenti szakaszokban ismertetett lépéseket.

A jogkivonat igényléséhez az alkalmazás regisztrációja után a következő értékekre lesz szüksége:

- Az Azure AD-tartomány neve. Ezt az értéket a Azure Active Directory **Áttekintés** lapjáról kérheti le.
- A bérlő (vagy könyvtár) azonosítója. Ezt az értéket az alkalmazás regisztrációjának **Áttekintés** lapjáról kérheti le.
- Az ügyfél (vagy alkalmazás) azonosítója. Ezt az értéket az alkalmazás regisztrációjának **Áttekintés** lapjáról kérheti le.
- Az ügyfél átirányítási URI-ja. Az alkalmazás regisztrációjának **hitelesítési** beállításaiból olvassa be ezt az értéket.
- Az ügyfél titkos kulcsának értéke. Az érték lekérése abból a helyről, amelyre korábban már másolta.

### <a name="create-a-storage-account-and-container"></a>Hozzon létre egy tárfiókot és tárolót

A kód futtatásához hozzon létre egy Storage-fiókot a Azure Active Directoryével megegyező előfizetésen belül. Ezután hozzon létre egy tárolót a Storage-fiókon belül. A mintakód egy blokk-blobot hoz létre ebben a tárolóban.

Ezután explicit módon rendelje hozzá a **Storage blob** -adatközreműködői szerepkört ahhoz a felhasználói fiókhoz, amelyen a kódot futtatni fogja. Ennek a szerepkörnek a Azure Portal való hozzárendelésével kapcsolatos útmutatásért lásd: [hozzáférés biztosítása az Azure blobhoz és az üzenetsor-kezelés a RBAC a Azure Portal](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Azure Storage-fiók létrehozásakor nem rendel automatikusan engedélyeket az Azure AD-n keresztüli adateléréshez. Kell explicit módon saját magának egy RBAC szerepkör hozzárendelése az Azure Storage. Az előfizetés, erőforráscsoport, tárfiók, vagy a tároló vagy üzenetsor szintjén rendelhet.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Webalkalmazás létrehozása, amely engedélyezi a blob Storage-hoz való hozzáférést az Azure AD-vel

Ha az alkalmazás hozzáfér az Azure Storage-hoz, azt a felhasználó nevében teszi meg, ami azt jelenti, hogy a blob vagy a várólista erőforrásai hozzáférnek a bejelentkezett felhasználó engedélyeivel. A kód kipróbálásához például egy webalkalmazásra van szükség, amely felszólítja a felhasználót, hogy jelentkezzen be egy Azure AD-identitás használatával. Létrehozhat saját, vagy használhatja a Microsoft által biztosított minta alkalmazást.

Egy befejezett minta webalkalmazás, amely tokent vásárol, és az Azure Storage-ban lévő blob létrehozásához használja a [githubon](https://aka.ms/aadstorage). A befejezett minta áttekintése és futtatása hasznos lehet a példákban szereplő példák megismerése érdekében. A befejezett minta futtatásával kapcsolatos utasításokért tekintse meg a következő témakört: [nézet és a befejezett minta futtatása](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Mutató hivatkozásokat tudjon felvenni, és utasításokkal  

Telepítse az Azure Storage ügyféloldali kódtárat a Visual studióból. Az a **eszközök** menüjében válassza **Nuget-Csomagkezelő**, majd **Package Manager Console**. Írja be a következő parancsokat a konzol ablakába a szükséges csomagok telepítéséhez a .NET-hez készült Azure Storage ügyféloldali kódtáraból:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Ezután adja hozzá a következő using utasításokat a HomeController.cs-fájlhoz:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Blokkos blob létrehozása

Adja hozzá a következő kódrészletet egy blokk blob létrehozásához:

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
> A blob-és üzenetsor-műveletek OAuth 2,0-tokenrel való engedélyezéséhez a HTTPS protokollt kell használnia.

A fenti példában a .NET ügyféloldali kódtár kezeli a blokkblob típusú létrehozására vonatkozó kérelem engedélyezése. Az Azure Storage ügyféloldali kódtárai más nyelveken is kezelik a kérések engedélyezését. Azonban ha egy Azure Storage műveletet hívja meg az OAuth-token, a REST API használatával, majd szüksége lesz az OAuth-jogkivonat használatával a kérelem engedélyezéséhez.

Az OAuth hozzáférési tokenek Blob és üzenetsor-szolgáltatás műveletek hívására, át kell adnia a hozzáférési jogkivonat a **engedélyezési** fejléc használatával a **tulajdonosi** séma, és adja meg a szolgáltatás verziója 2017-11-09 vagy nagyobb, mint az alábbi példában látható:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Az OAuth jogkivonat beszerzése az Azure ad-ből

Ezután adjon hozzá egy metódust, amely jogkivonatot kér az Azure AD-től a felhasználó nevében. Ez a metódus határozza meg azt a hatókört, amelyhez engedélyeket kell megadni. Az engedélyekkel és hatókörökkel kapcsolatos további információkért tekintse [meg a Microsoft Identity platform végpontjának engedélyek és](../../active-directory/develop/v2-permissions-and-consent.md)hozzájárulások című témakörét.

Az erőforrás-AZONOSÍTÓval hozza létre azt a hatókört, amelyhez a jogkivonat beszerzését kéri. A példa a hatókört az erőforrás-azonosító és a beépített `user_impersonation` hatókör használatával hozza létre, amely azt jelzi, hogy a jogkivonatot a felhasználó nevében kérték.

Ne feledje, hogy előfordulhat, hogy a felhasználót egy olyan felülettel kell bemutatnia, amely lehetővé teszi a felhasználó számára, hogy a jogkivonatot az ő nevében kérje. Ha beleegyezik a jóváhagyásra, a példa megkeresi a **MsalUiRequiredException** , és egy másik módszert hív meg a jóváhagyás iránti kérés megkönnyítése érdekében:

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
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

A hozzájárulás egy olyan felhasználó, aki engedélyt ad egy alkalmazásnak a védett erőforrások elérésére a nevükben. A Microsoft Identity platform 2,0 támogatja a növekményes hozzáférést, ami azt jelenti, hogy egy rendszerbiztonsági tag először is kérheti le az engedélyek minimális készletét, és szükség szerint adja hozzá az engedélyeket. Ha a kód hozzáférési jogkivonatot kér, adja meg az alkalmazás által a `scope` paraméterben megadott időpontban szükséges engedélyek hatókörét. További információ a növekményes beleegyező engedélyekről: a [Microsoft Identity platform (v 2.0) frissítése](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)

A következő módszer a növekményes belefoglalási kérelemhez tartozó hitelesítési tulajdonságokat hozza létre:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
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

## <a name="view-and-run-the-completed-sample"></a>A befejezett minta megtekintése és futtatása

A minta alkalmazás futtatásához először klónozott vagy töltse le a [githubról](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Ezután frissítse az alkalmazást a következő szakaszokban leírtak szerint.

### <a name="provide-values-in-the-settings-file"></a>Értékek megadása a beállítási fájlban

Ezután frissítse a *appSettings. JSON* fájlt a saját értékeivel, a következőképpen:

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

### <a name="update-the-storage-account-and-container-name"></a>A Storage-fiók és a tároló nevének frissítése

A *HomeController.cs* fájlban frissítse a blokk blobra hivatkozó URI-t a Storage-fiók és a tároló nevének használatára:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Implicit engedélyezési folyamat engedélyezése

Előfordulhat, hogy a minta futtatásához konfigurálnia kell a implicit engedélyezési folyamatot az alkalmazás regisztrálásához. Kövesse az alábbi lépéseket:

1. Navigáljon az alkalmazás regisztrálásához a Azure Portal.
1. A kezelés szakaszban válassza ki a **hitelesítési** beállítást.
1. A **Speciális beállítások**területen az **implicit támogatás** szakaszban jelölje be a hozzáférési jogkivonatok és azonosító tokenek engedélyezéséhez szükséges jelölőnégyzeteket az alábbi ábrán látható módon:

    ![Az implicit engedélyezési folyamat beállításainak engedélyezését bemutató képernyőkép](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>A localhost által használt port frissítése

A minta futtatásakor előfordulhat, hogy frissítenie kell az alkalmazás regisztrációjában megadott átirányítási URI-t, hogy használhassa a futtatókörnyezethez hozzárendelt *localhost* portot. Az átirányítási URI-nak a hozzárendelt port használatára való frissítéséhez kövesse az alábbi lépéseket:

1. Navigáljon az alkalmazás regisztrálásához a Azure Portal.
1. A kezelés szakaszban válassza ki a **hitelesítési** beállítást.
1. Az **átirányítási URI**-k területen szerkessze a minta alkalmazás által használt portot úgy, ahogy az a következő képen látható:

    ![Az alkalmazás regisztrálására szolgáló átirányítási URI-ket bemutató képernyőkép](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>További lépések

- További információ a Microsoft Identity platformról: [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/).
- Ha többet szeretne megtudni az Azure Storage RBAC szerepköreiről, tekintse meg a [hozzáférési jogosultságok kezelése a RBAC](storage-auth-aad-rbac.md)szolgáltatással című témakört.
- További információ az Azure-erőforrások felügyelt identitások használatáról az Azure Storage-ban: a [blobok és várólisták hozzáférésének hitelesítése az Azure-erőforrások Azure Active Directory és felügyelt identitásával](storage-auth-aad-msi.md).
