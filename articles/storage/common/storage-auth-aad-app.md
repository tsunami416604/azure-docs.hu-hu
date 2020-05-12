---
title: Jogkivonat beszerzése az Azure AD-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez
titleSuffix: Azure Storage
description: A Azure Active Directory használatával végezhet hitelesítést egy ügyfélalkalmazás alapján, beszerezzen egy OAuth 2,0-tokent, és engedélyezi a kérelmeket az Azure Blob Storage-hoz és a várólista-tárolóhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: has-adal-ref
ms.openlocfilehash: 0cda75469edaa183ed6553a431b9ad13b611db7d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201072"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Jogkivonat beszerzése az Azure AD-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez

A Azure Active Directory (Azure AD) az Azure Blob Storage vagy a üzenetsor Storage szolgáltatással való használatának egyik legfőbb előnye, hogy a hitelesítő adatait már nem kell a kódban tárolni. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformról (korábban Azure AD). Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD visszaadja a hozzáférési jogkivonatot az alkalmazásnak, és az alkalmazás a hozzáférési token használatával engedélyezheti a kérelmeket az Azure Blob Storage-ba vagy a várólista-tárolóba.

Ez a cikk bemutatja, hogyan konfigurálhatja a natív alkalmazást vagy webalkalmazást a Microsoft Identity platform 2,0-alapú hitelesítéshez. A kód például a .NET, más nyelveken pedig hasonló megközelítést használ. A Microsoft Identity platform 2,0-es verziójával kapcsolatos további információkért lásd: [Microsoft Identity platform (v 2.0) – áttekintés](../../active-directory/develop/v2-overview.md).

A OAuth 2,0 kód engedélyezési folyamatának áttekintését lásd: [hozzáférés engedélyezése Azure Active Directory webalkalmazásokhoz a OAuth 2,0 Code Grant flow használatával](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Szerepkör kiosztása egy Azure AD rendszerbiztonsági tag számára

Egy rendszerbiztonsági tag Azure Storage-alkalmazásból történő hitelesítéséhez először konfigurálja a szerepkör alapú hozzáférés-vezérlés (RBAC) beállításait az adott rendszerbiztonsági tag számára. Az Azure Storage olyan beépített RBAC-szerepköröket határoz meg, amelyek magukban foglalják a tárolók és a várólisták engedélyeit. Ha a RBAC szerepkör hozzá van rendelve egy rendszerbiztonsági tag számára, a rendszerbiztonsági tag hozzáférést kap az adott erőforráshoz. További információ: a [hozzáférési jogosultságok kezelése az Azure Blob és a üzenetsor adatai számára a RBAC használatával](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Alkalmazás regisztrálása Azure AD-Bérlővel

Az Azure AD-nek a tárolási erőforrásokhoz való hozzáférés engedélyezésére való használatának első lépéseként regisztrálja az ügyfélalkalmazás egy Azure AD-Bérlővel a [Azure Portal](https://portal.azure.com). Az ügyfélalkalmazás regisztrálása során információkat nyújt az alkalmazásról az Azure AD-hez. Az Azure AD egy ügyfél-azonosítót (más néven *alkalmazás-azonosítót*) biztosít, amelyet az alkalmazás az Azure ad-vel való hozzárendeléséhez használ futásidőben. Az ügyfél-AZONOSÍTÓval kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../../active-directory/develop/app-objects-and-service-principals.md).

Az Azure Storage-alkalmazás regisztrálásához kövesse a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)című témakörben ismertetett lépéseket. Az alábbi képen a webalkalmazások regisztrálásának általános beállításai láthatók:

![A Storage-alkalmazás Azure AD-vel való regisztrálását bemutató képernyőkép](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Ha natív alkalmazásként regisztrálja az alkalmazást, megadhat bármely érvényes URI-t az **átirányítási URI**-hoz. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI azonosítónak érvényes URI-nak kell lennie, mert meghatározza azt az URL-címet, amelyhez a tokenek meg vannak határozva.

Az alkalmazás regisztrálását követően az alkalmazás-azonosító (vagy ügyfél-azonosító) a **Beállítások**területen jelenik meg:

![Az ügyfél-azonosítót ábrázoló képernyőkép](./media/storage-auth-aad-app/app-registration-client-id.png)

Az alkalmazások Azure AD-vel való regisztrálásával kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>A regisztrált alkalmazás engedélyeinek megadása az Azure Storage-ban

Ezután adja meg az alkalmazás engedélyeit az Azure Storage API-k meghívásához. Ez a lépés lehetővé teszi, hogy az alkalmazás engedélyezze az Azure Storage-ba irányuló kéréseket az Azure AD-vel.

1. A regisztrált alkalmazás **Áttekintés** lapján válassza az **API-engedélyek megtekintése**lehetőséget.
1. Az **API-engedélyek** szakaszban válassza az **engedély hozzáadása** lehetőséget, és válassza a **Microsoft API**-k elemet.
1. Az eredmények listájában válassza az **Azure Storage** lehetőséget, hogy megjelenjen a **kérelem API-engedélyei** panel.
1. **Milyen típusú engedélyek szükségesek az alkalmazáshoz?**, figyelje meg, hogy a rendelkezésre álló engedélyek típusa **delegált engedély**. Ez a beállítás alapértelmezés szerint be van jelölve.
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

A jogkivonatok beszerzését támogató forgatókönyvek listáját a [Microsoft Authentication Library tartalmának](/azure/active-directory/develop/msal-overview) [hitelesítési folyamatok](/en-us/azure/active-directory/develop/msal-authentication-flows) szakasza tartalmazza.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Az Azure AD-vel való hitelesítés jól ismert értékei

Egy rendszerbiztonsági tag Azure AD-vel történő hitelesítéséhez meg kell adnia néhány jól ismert értéket a kódban.

### <a name="azure-ad-authority"></a>Azure AD-szolgáltató

A Microsoft nyilvános felhőben az alapszintű Azure AD-szolgáltató a következő, ahol a *bérlő azonosítója* a Active Directory bérlői azonosítója (vagy a címtár azonosítója):

`https://login.microsoftonline.com/<tenant-id>/`

A bérlő azonosítója azonosítja a hitelesítéshez használandó Azure AD-bérlőt. Más néven a címtár-azonosító. A bérlő AZONOSÍTÓjának lekéréséhez navigáljon a Azure Portalban az alkalmazás regisztrációjának **Áttekintés** lapjára, és onnan másolja az értéket.

### <a name="azure-storage-resource-id"></a>Azure Storage-erőforrás azonosítója

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód – példa: Blokkos blob létrehozása

A Code példa bemutatja, hogyan kérhető le hozzáférési token az Azure AD-ből. A hozzáférési jogkivonat a megadott felhasználó hitelesítésére szolgál, majd egy blokk-blob létrehozására vonatkozó kérést engedélyez. A minta működésének megkezdéséhez kövesse az előző szakaszokban ismertetett lépéseket.

A jogkivonat igényléséhez az alkalmazás regisztrációja után a következő értékekre lesz szüksége:

- Az Azure AD-tartomány neve. Ezt az értéket a Azure Active Directory **Áttekintés** lapjáról kérheti le.
- A bérlő (vagy könyvtár) azonosítója. Ezt az értéket az alkalmazás regisztrációjának **Áttekintés** lapjáról kérheti le.
- Az ügyfél (vagy alkalmazás) azonosítója. Ezt az értéket az alkalmazás regisztrációjának **Áttekintés** lapjáról kérheti le.
- Az ügyfél átirányítási URI-ja. Az alkalmazás regisztrációjának **hitelesítési** beállításaiból olvassa be ezt az értéket.
- Az ügyfél titkos kulcsának értéke. Az érték lekérése abból a helyről, amelyre korábban már másolta.

### <a name="create-a-storage-account-and-container"></a>Storage-fiók és-tároló létrehozása

A kód futtatásához hozzon létre egy Storage-fiókot a Azure Active Directoryével megegyező előfizetésen belül. Ezután hozzon létre egy tárolót a Storage-fiókon belül. A mintakód egy blokk-blobot hoz létre ebben a tárolóban.

Ezután explicit módon rendelje hozzá a **Storage blob-adatközreműködői** szerepkört ahhoz a felhasználói fiókhoz, amelyen a kódot futtatni fogja. Ennek a szerepkörnek a Azure Portal való hozzárendelésével kapcsolatos útmutatásért lásd: [hozzáférés biztosítása az Azure blobhoz és az üzenetsor-kezelés a RBAC a Azure Portal](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Azure Storage-fiók létrehozásakor nem rendel automatikusan engedélyeket az Azure AD-n keresztüli adateléréshez. Explicit módon hozzá kell rendelnie egy RBAC-szerepkört az Azure Storage-hoz. Az előfizetés, az erőforráscsoport, a Storage-fiók, a tároló vagy a várólista szintjén rendelhető hozzá.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Webalkalmazás létrehozása, amely engedélyezi a blob Storage-hoz való hozzáférést az Azure AD-vel

Ha az alkalmazás hozzáfér az Azure Storage-hoz, azt a felhasználó nevében teszi meg, ami azt jelenti, hogy a blob vagy a várólista erőforrásai hozzáférnek a bejelentkezett felhasználó engedélyeivel. A kód kipróbálásához például egy webalkalmazásra van szükség, amely felszólítja a felhasználót, hogy jelentkezzen be egy Azure AD-identitás használatával. Létrehozhat saját, vagy használhatja a Microsoft által biztosított minta alkalmazást.

Egy befejezett minta webalkalmazás, amely tokent vásárol, és az Azure Storage-ban lévő blob létrehozásához használja a [githubon](https://aka.ms/aadstorage). A befejezett minta áttekintése és futtatása hasznos lehet a példákban szereplő példák megismerése érdekében. A befejezett minta futtatásával kapcsolatos utasításokért tekintse meg a következő témakört: [nézet és a befejezett minta futtatása](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Hivatkozások hozzáadása és utasítások használata  

Telepítse az Azure Storage ügyféloldali kódtárat a Visual studióból. A **Tools** (Eszközök) menüben válassza a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Package Manager Console** (Csomagkezelői konzol) lehetőséget. Írja be a következő parancsokat a konzol ablakába a szükséges csomagok telepítéséhez a .NET-hez készült Azure Storage ügyféloldali kódtáraból:

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

A fenti példában a .NET-ügyfél könyvtára kezeli a kérelem engedélyezését a blokk blob létrehozásához. Az Azure Storage ügyféloldali kódtárai más nyelveken is kezelik a kérések engedélyezését. Ha azonban az REST API használatával OAuth-tokenrel rendelkező Azure Storage-műveletet hív meg, akkor a OAuth-token használatával engedélyeznie kell a kérést.

A blobok és Queue szolgáltatás műveletek OAuth hozzáférési jogkivonatokkal történő meghívásához adja át a hozzáférési jogkivonatot az **engedélyezési** fejlécben a **tulajdonosi** séma használatával, és adjon meg egy 2017-11-09-es vagy magasabb verziójú Service-verziót az alábbi példában látható módon:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>OAuth-token beszerzése az Azure AD-ből

Ezután adjon hozzá egy metódust, amely jogkivonatot kér az Azure AD-től a felhasználó nevében. Ez a metódus határozza meg azt a hatókört, amelyhez engedélyeket kell megadni. Az engedélyekkel és hatókörökkel kapcsolatos további információkért tekintse [meg a Microsoft Identity platform végpontjának engedélyek és](../../active-directory/develop/v2-permissions-and-consent.md)hozzájárulások című témakörét.

Az erőforrás-AZONOSÍTÓval hozza létre azt a hatókört, amelyhez a jogkivonat beszerzését kéri. A példa a hatókört az erőforrás-azonosító és a beépített hatókör használatával hozza létre `user_impersonation` , amely azt jelzi, hogy a jogkivonatot a felhasználó nevében kérték.

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

A hozzájárulás egy olyan felhasználó, aki engedélyt ad egy alkalmazásnak a védett erőforrások elérésére a nevükben. A Microsoft Identity platform 2,0 támogatja a növekményes hozzáférést, ami azt jelenti, hogy egy rendszerbiztonsági tag először is kérheti le az engedélyek minimális készletét, és szükség szerint adja hozzá az engedélyeket. Ha a kód hozzáférési jogkivonatot kér, adja meg az alkalmazás által a paraméterben megadott időpontban szükséges engedélyek hatókörét `scope` . További információ **a növekményes** beleegyező engedélyekről: a [Microsoft Identity platform (v 2.0) frissítése](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)

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
