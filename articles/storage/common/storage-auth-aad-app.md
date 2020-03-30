---
title: Jogkivonat beszerzése az Azure AD-től az ügyfélalkalmazásból érkező kérelmek engedélyezéséhez
titleSuffix: Azure Storage
description: Az Azure Active Directory használatával egy ügyfélalkalmazásból hitelesítheti magát, oauth 2.0-s jogkivonatot szerezhet be, és engedélyezheti az Azure Blob-tárolóés várólista-tárolókra vonatkozó kérelmeket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268482"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Jogkivonat beszerzése az Azure AD-től az ügyfélalkalmazásból érkező kérelmek engedélyezéséhez

Az Azure Active Directory (Azure AD) Azure Blob storage vagy queue storage használatával való használatának egyik fő előnye, hogy a hitelesítő adatokat már nem kell tárolni a kódban. Ehelyett egy OAuth 2.0 hozzáférési jogkivonatot kérhet a Microsoft identity platformról (korábban Azure AD). Az Azure AD hitelesíti az alkalmazást futtató rendszerbiztonsági tag (egy felhasználó, csoport vagy egyszerű szolgáltatás). Ha a hitelesítés sikeres, az Azure AD visszaadja a hozzáférési jogkivonatot az alkalmazásnak, és az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Blob-tároló vagy a várólista-tároló kéréseinek engedélyezéséhez.

Ez a cikk bemutatja, hogyan konfigurálhatja a natív alkalmazást vagy webalkalmazást a Microsoft identity platform 2.0-s hitelesítésére. A példakód a .NET-et tartalmazza, de más nyelvek is hasonló megközelítést alkalmaznak. A Microsoft identity platform 2.0-s platformjáról a [Microsoft identity platform (2.0-s verzió) – áttekintés című témakörben olvashat bővebben.](../../active-directory/develop/v2-overview.md)

Az OAuth 2.0-s kódtámogatási folyamat áttekintését az [Azure Active Directory webalkalmazásokhoz való hozzáférés engedélyezése az OAuth 2.0-s kódtámogatási folyamat használatával című témakörben találja.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Szerepkör hozzárendelése Azure AD rendszerbiztonsági taghoz

Ha egy rendszerbiztonsági tag az Azure Storage-alkalmazás, először konfigurálja szerepköralapú hozzáférés-vezérlési (RBAC) beállításokat, hogy a rendszerbiztonsági tag. Az Azure Storage olyan beépített RBAC-szerepköröket határoz meg, amelyek a tárolók és várólisták engedélyeit foglalják magukban. Ha az RBAC szerepkör egy rendszerbiztonsági taghoz van rendelve, az adott rendszerbiztonsági tag hozzáférést kap az erőforráshoz. További információ: [Manage access rights to Azure Blob and Queue data with RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Regisztrálja az alkalmazást egy Azure AD-bérlővel

Az első lépés az Azure AD használatával a tárolási erőforrásokhoz való hozzáférés engedélyezéséhez az ügyfélalkalmazás regisztrálása egy Azure AD-bérlővel az [Azure Portalról.](https://portal.azure.com) Az ügyfélalkalmazás regisztrálásakor adja meg az alkalmazásadatait az Azure AD-nek. Az Azure AD ezután egy ügyfél-azonosítót (más néven *alkalmazásazonosítót)* biztosít, amelyet az alkalmazás azure AD-vel való társítására használ futásidőben. Az ügyfélazonosítóról az [Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban](../../active-directory/develop/app-objects-and-service-principals.md)című témakörben olvashat bővebben.

Az Azure Storage-alkalmazás regisztrálásához kövesse a rövid útmutatóban látható [lépéseket: Alkalmazás regisztrálása a Microsoft identity platformmal.](../../active-directory/develop/quickstart-configure-app-access-web-apis.md) Az alábbi képen a webalkalmazások regisztrálásának általános beállításai láthatók:

![Képernyőkép a tárolási alkalmazás regisztrálásáról az Azure AD-vel](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Ha natív alkalmazásként regisztrálja az alkalmazást, bármilyen érvényes URI-t megadhat az **átirányítási**URI-hoz. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI-nak érvényes URI-nak kell lennie, mert megadja azt az URL-címet, amelyhez a jogkivonatok rendelkezésre állnak.

Miután regisztrálta az alkalmazást, a **Beállítások**csoportban megjelenik az alkalmazásazonosító (vagy ügyfélazonosító):

![Az ügyfélazonosítót bemutató képernyőkép](./media/storage-auth-aad-app/app-registration-client-id.png)

Az alkalmazások Azure AD-vel való regisztrálásáról az [Alkalmazások integrálása](../../active-directory/develop/quickstart-v2-register-an-app.md)az Azure Active Directoryval című témakörben talál további információt.

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>A regisztrált alkalmazásengedélyek megadása az Azure Storage-nak

Ezután adja meg az alkalmazás engedélyeket az Azure Storage API-k hívásához. Ez a lépés lehetővé teszi, hogy az alkalmazás az Azure Storage-ra vonatkozó kérelmeket engedélyezze az Azure AD-vel.

1. A regisztrált alkalmazás **Áttekintő** lapján válassza az **API-engedélyek megtekintése**lehetőséget.
1. Az **API-engedélyek** csoportban válassza az **Engedély hozzáadása** lehetőséget, és válassza a **Microsoft API-k**lehetőséget.
1. Válassza ki az **Azure Storage** az eredmények listájából az **API-engedélyek kérése** ablaktábla megjelenítéséhez.
1. A **Milyen típusú engedélyeket igényel az alkalmazás?** csoportban vegye figyelembe, hogy az elérhető engedélytípus **a Delegált engedélyek.** Ez a beállítás alapértelmezés szerint az Ön számára van kiválasztva.
1. Az **API-engedélyek kérése** ablaktábla **Engedélyek kiválasztása** szakaszában jelölje be a **user_impersonation**melletti jelölőnégyzetet, majd kattintson az **Engedélyek hozzáadása**gombra.

    ![Képernyőkép a tárhelyhez való engedélyekről](media/storage-auth-aad-app/registered-app-permissions-1.png)

Az **API-engedélyek** ablaktábla most azt mutatja, hogy a regisztrált Azure AD-alkalmazás hozzáfér a Microsoft Graph és az Azure Storage. Az engedélyek automatikusan megkapnak a Microsoft Graph számára, amikor először regisztrálja az alkalmazást az Azure AD-vel.

![Képernyőkép az alkalmazásregisztrálási engedélyekről](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Ügyféltitok létrehozása

Az alkalmazásnak szüksége van egy ügyféltitokra, hogy bizonyítsa a személyazonosságát, amikor jogkivonatot kér. Az ügyféltitok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg az alkalmazásregisztrációt az Azure Portalon.
1. Válassza ki a **Tanúsítványok & titkos kulcsok** beállítást.
1. Az **Ügyféltitkok csoportban**kattintson **az Új ügyféltitok** elemre az új titkos titok létrehozásához.
1. Adja meg a titkos kulcsot, és válassza ki a kívánt lejárati időközt.
1. Azonnal másolja az új titok értékét egy biztonságos helyre. A teljes érték csak egyszer jelenik meg.

    ![Az ügyféltkatit ábrázoló képernyőkép](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Ügyfélkódtárak tokenbeszerzéshez

Miután regisztrálta az alkalmazást, és engedélyt adott számára az Azure Blob-tároló vagy a várólista-tároló adatainak eléréséhez, kódot adhat hozzá az alkalmazáshoz egy rendszerbiztonsági tag hitelesítéséhez és egy OAuth 2.0-s jogkivonat beszerzéséhez. A jogkivonat hitelesítéséhez és beszerzéséhez használhatja a [Microsoft identitásplatform hitelesítési kódtárait](../../active-directory/develop/reference-v2-libraries.md) vagy egy másik, openID Connect 1.0-s verziót támogató nyílt forráskódú tárat. Az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Blob-tároló vagy a várólista-tároló elleni kérelem engedélyezéséhez.

Azon forgatókönyvek listáját, amelyekhez a tokenek beolvasása támogatott, tekintse meg a [Hitelesítési folyamatok](/en-us/azure/active-directory/develop/msal-authentication-flows) szakasza a [Microsoft Authentication Library tartalom](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Jól ismert értékek az Azure AD-vel való hitelesítéshez

A rendszerbiztonsági tag hitelesítéséhez az Azure AD,néhány jól ismert értékeket a kódban.

### <a name="azure-ad-authority"></a>Azure AD-szolgáltató

A Microsoft nyilvános felhő, az alap Azure AD-szolgáltató a következő, ahol *a bérlő-id* az Active Directory-bérlői azonosító (vagy címtár-azonosító):

`https://login.microsoftonline.com/<tenant-id>/`

A bérlői azonosító azonosítja a hitelesítéshez használandó Azure AD-bérlőt. Könyvtárazonosítónak is nevezik. A bérlői azonosító lekéréséhez keresse meg az **alkalmazás regisztrációját** az Azure Portalon, és másolja az értéket onnan.

### <a name="azure-storage-resource-id"></a>Azure Storage-erőforrás azonosítója

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Példa : Blokkblob létrehozása

A példakód bemutatja, hogyan szerezhet be egy hozzáférési jogkivonatot az Azure AD-től. A hozzáférési jogkivonat a megadott felhasználó hitelesítésére szolgál, majd engedélyezi a blokkblob létrehozására vonatkozó kérelmet. A minta működéséhez először kövesse az előző szakaszokban ismertetett lépéseket.

A jogkivonat igényléséhez az alkalmazás regisztrációjából a következő értékekre lesz szüksége:

- Az Azure AD-tartomány neve. Ezt az értéket az Azure Active Directory **áttekintő lapjáról** olvassa be.
- A bérlő (vagy könyvtár) azonosítója. Ezt az értéket az alkalmazásregisztráció Áttekintés lapjáról olvassa **be.**
- Az ügyfél (vagy alkalmazás) azonosítója. Ezt az értéket az alkalmazásregisztráció Áttekintés lapjáról olvassa **be.**
- Az ügyfél átirányítási URI. Olvassa be ezt az értéket az alkalmazás **regisztrációjának hitelesítési** beállításai között.
- Az ügyféltitok értéke. Ennek az értéknek a beolvasása abból a helyből, ahamelykorábban másolta.

### <a name="create-a-storage-account-and-container"></a>Tárfiók és tároló létrehozása

A kódminta futtatásához hozzon létre egy tárfiókot az Azure Active Directoryval azonos előfizetésen belül. Ezután hozzon létre egy tárolót a tárfiókon belül. A mintakód létrehoz egy blokkblobot ebben a tárolóban.

Ezután explicit módon rendelje hozzá a **Storage Blob Data Contributor** szerepkört ahhoz a felhasználói fiókhoz, amely alatt a mintakódot futtatni fogja. A szerepkör azure-portálon való hozzárendeléséről az [Azure-blob- és várólista-adatok hoz való hozzáférés megadása az RBAC-mal című témakörben található az Azure Portalon.](storage-auth-aad-rbac-portal.md)

> [!NOTE]
> Amikor létrehoz egy Azure Storage-fiókot, nem kap automatikusan engedélyeket az Azure AD-n keresztüli adatok eléréséhez. Explicit módon hozzá kell rendelnie magának egy RBAC szerepkört az Azure Storage-hoz. Hozzárendelheti az előfizetés, az erőforráscsoport, a tárfiók vagy a tároló vagy a várólista szintjén.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Hozzon létre egy webalkalmazást, amely engedélyezi a hozzáférést a Blob storage-hoz az Azure AD-vel

Amikor az alkalmazás hozzáfér az Azure Storage-hoz, ezt a felhasználó nevében teszi, ami azt jelenti, hogy a blob- vagy várólista-erőforrások a bejelentkezett felhasználó engedélyeivel érhetők el. A példakód kipróbálásához szüksége van egy webalkalmazásra, amely felszólítja a felhasználót, hogy jelentkezzen be egy Azure AD-identitás használatával. Létrehozhatja saját ját, vagy használhatja a Microsoft által biztosított mintaalkalmazást.

Egy befejezett minta webalkalmazás, amely beszerzi a jogkivonatot, és használja az Azure Storage blob létrehozásához érhető el a [GitHubon.](https://aka.ms/aadstorage) A kitöltött minta áttekintése és futtatása hasznos lehet a kódpéldák megértéséhez. A befejezett minta futtatásáról a Befejezett minta megtekintése és futtatása című szakaszban talál [útmutatást.](#view-and-run-the-completed-sample)

#### <a name="add-references-and-using-statements"></a>Hivatkozások hozzáadása és utasítások használata  

A Visual Studio,telepítse az Azure Storage-ügyfélkönyvtár. A **Tools** (Eszközök) menüben válassza a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Package Manager Console** (Csomagkezelői konzol) lehetőséget. Írja be a következő parancsokat a konzolablakba a szükséges csomagok telepítéséhez az Azure Storage ügyfélrendszertárból a .NET számára:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Ezután adja hozzá a következőket a HomeController.cs fájlhoz a következő utasítások használatával:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Blokkblob létrehozása

Adja hozzá a következő kódrészletet egy blokkblob létrehozásához:

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
> Blob- és várólista-műveletek OAuth 2.0-s jogkivonattal történő engedélyezéséhez HTTPS-t kell használnia.

A fenti példában a .NET ügyféltár kezeli a blokkblob létrehozásához való kérelem engedélyezését. A más nyelvekhez készült Azure Storage-ügyfélkódtárak is kezelik a kérelem engedélyezését. Ha azonban egy Azure Storage-műveletet hív meg egy OAuth-jogkivonattal a REST API használatával, akkor engedélyeznie kell a kérést az OAuth-jogkivonat használatával.

Blob- és várólista-szolgáltatás műveleteinek oauth-hozzáférési jogkivonatokkal történő hívásához adja át a hozzáférési jogkivonatot az **engedélyezési** fejlécben a **tulajdonosi** séma használatával, és adja meg a 2017-11-09-es vagy újabb szolgáltatásverziót, ahogy az az alábbi példában látható:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>OAuth-jogkivonat beszereznie az Azure AD-től

Ezután adjon hozzá egy metódust, amely a felhasználó nevében jogkivonatot kér az Azure AD-től. Ez a módszer határozza meg azt a hatókört, amelyhez az engedélyeket meg kell adni. Az engedélyekről és a hatókörökről további információt [az Engedélyek és hozzájárulás a Microsoft identity platform végpontjában című témakörben talál.](../../active-directory/develop/v2-permissions-and-consent.md)

Az erőforrás-azonosító segítségével a jogkivonat megszerzéséhez hatókör létrehozásához. A példa az erőforrás-azonosító és a beépített `user_impersonation` hatókör használatával hozlétre a hatókört, ami azt jelzi, hogy a jogkivonatot a felhasználó nevében kérik.

Ne feledje, hogy előfordulhat, hogy a felhasználónak olyan felületet kell bemutatnia, amely lehetővé teszi a felhasználó beleegyezését a token nevének kérelmezéséhez. Ha hozzájárulásra van szükség, a példa elkapja a **MsalUiRequiredException-t,** és egy másik módszert hív meg a hozzájárulási kérelem megkönnyítésére:

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

A hozzájárulás az a folyamat, amelynek során a felhasználó engedélyt ad egy alkalmazásnak a védett erőforrásokhoz való hozzáférésre a nevükben. A Microsoft identity platform 2.0 támogatja a növekményes jóváhagyást, ami azt jelenti, hogy a rendszerbiztonsági tag kezdetben kérheti az engedélyek minimális készletét, és szükség szerint engedélyeket adhat hozzá. Amikor a kód hozzáférési jogkivonatot kér, adja meg az alkalmazásnak `scope` a paraméterben adott időpontban szükséges engedélyek hatókörét. A növekményes hozzájárulásról a Miért [frissítsen a Microsoft identity platformra (2.0) című](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) **részBen található Növekményes és dinamikus hozzájárulás** című szakaszban.

A következő módszer a növekményes hozzájárulás igényléséhez szükséges hitelesítési tulajdonságokat alkotja:

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

A mintaalkalmazás futtatásához először klónozza vagy töltse le a [GitHubról.](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal) Ezután frissítse az alkalmazást a következő szakaszokban leírtak szerint.

### <a name="provide-values-in-the-settings-file"></a>Értékek megadása a beállításfájlban

Ezután frissítse az *appsettings.json* fájlt a saját értékeivel, az alábbiak szerint:

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

### <a name="update-the-storage-account-and-container-name"></a>A tárfiók és a tároló nevének frissítése

A *HomeController.cs* fájlban frissítse az URI-t, amely a blokkblobra hivatkozik a tárfiók és a tároló nevének használatához:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Implicit támogatási folyamat engedélyezése

A minta futtatásához szükség lehet az implicit támogatási folyamat konfigurálására az alkalmazás regisztrációjához. Kövesse az alábbi lépéseket:

1. Nyissa meg az alkalmazásregisztrációt az Azure Portalon.
1. A Kezelés csoportban válassza a **Hitelesítés beállítást.**
1. A **Speciális beállítások csoportban** **jelölje** be a jelölőnégyzeteket a hozzáférési jogkivonatok és azonosítójogtok engedélyezéséhez, ahogy az az alábbi képen látható:

    ![Képernyőkép az implicit támogatási folyamat beállításainak engedélyezéséről](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>A localhost által használt port frissítése

A minta futtatásakor előfordulhat, hogy frissítenie kell az alkalmazásregisztrációban megadott átirányítási URI-t a futásidőben hozzárendelt *localhost* port használatához. Ha frissíteni szeretné az átirányítási URI-t a hozzárendelt port használatára, kövesse az alábbi lépéseket:

1. Nyissa meg az alkalmazásregisztrációt az Azure Portalon.
1. A Kezelés csoportban válassza a **Hitelesítés beállítást.**
1. Az **Irányított urik átirányítása csoportban**a portot úgy szerkeszti, hogy megegyezzen a mintaalkalmazás által használtporttal, ahogy az az alábbi képen látható:

    ![Képernyőkép az alkalmazásregisztrációhoz szükséges átirányítási URI-król](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a Microsoft identitásplatformról, olvassa el a [Microsoft identity platform című témakört.](https://docs.microsoft.com/azure/active-directory/develop/)
- Ha többet szeretne tudni az Azure Storage RBAC-szerepköreiről, [olvassa el a Tárolási adatokhoz való hozzáférési jogok kezelése az RBAC-mal című témakört.](storage-auth-aad-rbac.md)
- Ha meg szeretné tudni, hogy miként szeretné használni az Azure-erőforrások felügyelt identitásait az Azure Storage szolgáltatással, olvassa [el a Blobokhoz és várólistákhoz való hozzáférés hitelesítése az Azure Active Directoryval és az Azure Resources felügyelt identitásaival.](storage-auth-aad-msi.md)
