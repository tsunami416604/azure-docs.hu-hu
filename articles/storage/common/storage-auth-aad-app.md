---
title: Hitelesítés az Azure AD (előzetes verzió) Storage-alkalmazás |} Microsoft Docs
description: Hitelesítés az Azure AD egy Azure Storage-alkalmazás (előzetes verzió).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 1bf4a8bba3b93c16f67d46f65292709ef2a1bba2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660875"
---
# <a name="authenticate-with-azure-ad-from-an-azure-storage-application-preview"></a>A hitelesítést az Azure AD egy Azure Storage-alkalmazás (előzetes verzió)

A kulcs az Azure Active Directoryval (Azure AD) az Azure Storage előnye, hogy a hitelesítő adatok már nem kell tárolni a kódban. Ehelyett olyan OAuth 2.0 hozzáférési jogkivonatot kérhet az Azure AD. Az Azure AD kezeli a hitelesítést a rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatásnév) az alkalmazás futtatása. Ha hitelesítés sikeres, az Azure AD az alkalmazásnak a hozzáférési jogkivonatot ad vissza, és az alkalmazás ezután használhatja a hozzáférési jogkivonat Azure Storage kérelmek hitelesítéséhez.

Ez a cikk bemutatja, hogyan konfigurálhatja az alkalmazást, a hitelesítés az Azure ad-val. Egy hasonló módszert használja, a kód például szolgáltatások .NET, de a többi nyelvet.

Előtt egy rendszerbiztonsági tag képes hitelesíteni az Azure Storage-alkalmazás, állítsa be a szerepköralapú hozzáférés-vezérlést (RBAC) beállításait, hogy a rendszerbiztonsági tag. Az Azure Storage határozza meg, amely magában foglalja a tárolók és a várólisták engedélyeit RBAC-szerepkörök. Az RBAC szerepkör van rendelve egy rendszerbiztonsági tag, a rendszerbiztonsági tag jár, hogy az erőforrás elérésére. További információkért lásd: [kezelése hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) storage-adatokkal](storage-auth-aad-rbac.md).

A OAuth 2.0 code grant flow áttekintéséért lásd: [hozzáférés engedélyezése a Azure Active Directory webes alkalmazásokat az OAuth 2.0 kód használatával adja meg folyamat](../../active-directory/develop/active-directory-protocols-oauth-code.md).

> [!IMPORTANT]
> Ez az előzetes kiadás csak nem üzemi használatra készült. Éles szolgáltatásiszint-szerződések (SLA) nem lesz elérhető, amíg az Azure AD integrálása az Azure Storage általánosan elérhető van deklarálva. Ha az Azure AD-integrációs jelenleg nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokenje az alkalmazásokban. Az előzetes kapcsolatos további információkért lásd: [hitelesíti a hozzáférést az Azure Active Directoryval (előzetes verzió) Azure Storage](storage-auth-aad.md).
>
> Az előzetes RBAC szerepkör-hozzárendelések terjesztése akár öt percet is eltarthat.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Az alkalmazás regisztrálása az Azure AD-bérlő

Az első lépés az Azure AD tárolási erőforrásokhoz való hozzáférés engedélyezése az Azure AD-bérlő regisztrál az ügyfélalkalmazást. Az alkalmazás regisztrálása lehetővé teszi az Azure hívja [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) a felhasználói kódból. Az adal-t az API-k biztosít hitelesítéséhez az Azure AD az alkalmazásból. Az alkalmazás regisztrálása is lehetővé teszi hívásokat az alkalmazásból az Azure Storage API-k olyan hozzáférési jogkivonatot az engedélyezésére.

Ha regisztrálja az alkalmazást, adja meg információkat az Azure AD az alkalmazással kapcsolatos. Az Azure AD majd biztosít egy ügyfél-Azonosítót (más néven egy *Alkalmazásazonosító*), hogy használja-e az alkalmazás társítása az Azure AD futásidőben. Az ügyfél-azonosító kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directoryban](../../active-directory/develop/active-directory-application-objects.md).

Az Azure Storage-alkalmazás regisztrálásához kövesse a [egy alkalmazás hozzáadása](../../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) szakasz [alkalmazások integrálása az Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Natív alkalmazás regisztrálnia az alkalmazást, ha bármilyen érvényes URI-JÁNAK megadhatja a **átirányítási URI-**. Az érték nem egy tényleges végpontnak lennie kell.

![Képernyőfelvétel a storage-alkalmazás regisztrálása az Azure ad-val megjelenítő](./media/storage-auth-aad-app/app-registration.png)

Az alkalmazás regisztrálása után megjelenik az alkalmazás azonosítója (vagy ügyfél-azonosító) alatt **beállítások**:

![Képernyőfelvétel az ügyfél-azonosító](./media/storage-auth-aad-app/app-registration-client-id.png)

Egy alkalmazás regisztrálása az Azure ad-vel kapcsolatos további információkért lásd: [alkalmazások integrálása az Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Adja meg a regisztrált alkalmazás engedélyeket Azure Storage

Ezt követően kell biztosítania az Alkalmazásengedélyek Azure Storage API-k meghívásához. Ez a lépés lehetővé teszi, hogy az alkalmazás hívások Azure Storage Azure AD-val való engedélyezéséhez.

1. Az Azure portál bal oldali navigációs ablaktábláján válassza **minden szolgáltatás**, keresse meg a **App regisztrációk**.
2. Keresse meg az előző lépésben létrehozott regisztrált alkalmazás nevét.
3. Válassza ki a regisztrált alkalmazást, majd kattintson a **beállítások**. Az a **API-hozzáférés** szakaszban jelölje be **szükséges engedélyek**.
4. Az a **szükséges engedélyek** panelen kattintson a **Hozzáadás** gombra.
5. A **API kiválasztása**, keresse meg az "Azure Storage", és válassza ki **Azure Storage** eredmények listájáról.

    ![Tároló engedélyeinek ábrázoló képernyőfelvétel](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. A **engedélyként válassza**, melletti jelölőnégyzetet **Access Azure Storage**, és kattintson a **kiválasztása**.
7. Kattintson a **Done** (Kész) gombra.

A **szükséges engedélyek** windows már helyesen jelenik meg, hogy az Azure AD alkalmazás Azure Active Directory és az Azure Storage is hozzáfér. Engedélyekkel az Azure AD automatikusan amikor először regisztrál az alkalmazást az Azure ad-val.

![Bejelentkezéshez regisztrálni Alkalmazásengedélyek](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET-példakód: hozzon létre egy blokkblob

A kód példa bemutatja, hogyan férhetnek hozzá egy token az Azure AD. A megadott felhasználói kérelem létrehozása egy blokkblob majd helyszerepkörre, és a hozzáférési jogkivonat segítségével. Ez a minta működéséhez először kövesse az előző részben.

> [!NOTE]
> Az Azure Storage-fiókjának tulajdonos nem automatikusan rendelt engedélyek adatok eléréséhez. Meg kell explicit módon saját kezűleg egy Szerepalapú szerepkör hozzárendelése az Azure Storage. A előfizetés, a erőforráscsoport, a tárfiók, vagy a tároló vagy a várólista szintjén hozzá lehet rendelni. 
>
> Például futtatásához a mintakód egy tárfiókot, ahol egy olyan tulajdonost, és a saját felhasználói identitás, kell rendelnie a Szerepalapú szerepkör Blob adatok közreműködői a saját maga. Ellenkező esetben a blob létrehozásához a hívás sikertelen lesz, és HTTP-állapotkód 403 (tiltott). További információkért lásd: [kezelése hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) storage-adatokkal](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Az Azure AD-hitelesítéshez az ismert értékek

Hitelesítés az Azure AD egy rendszerbiztonsági tag, néhány jól ismert értéket is a kódban kell.

#### <a name="azure-ad-oauth-endpoint"></a>Az Azure AD OAuth-végpont

A kiinduló az Azure AD authority végpont az OAuth 2.0 értéke a következő, ahol *bérlőazonosító* az Active Directory-bérlőazonosító beszerzése (vagy a könyvtár-azonosítója):

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

A bérlő azonosítója az Azure AD-bérlő a hitelesítéshez használandó azonosítja. Bérlői azonosító lekéréséhez kövesse a témakörben ismertetett lépéseket **bérlői azonosító lekérése az Azure Active Directory**.

#### <a name="storage-resource-id"></a>Storage erőforrás-azonosító

Az Azure Storage erőforrás-azonosító segítségével szerezzen be egy tokent, az Azure Storage kérelmek hitelesítéséhez:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Az Azure Active Directory Bérlőazonosító beszerzése

Ahhoz, hogy a bérlő azonosítója, kövesse az alábbi lépéseket:

1. Az Azure-portálon válassza ki az Active Directory.
2. Kattintson a **Tulajdonságok** elemre.
3. A megadott GUID-érték másolja a **könyvtár-azonosítója**. Ez az érték rövidítése a bérlő azonosítója.

![Képernyőfelvétel a változásszinkronizálás bemutatja, hogyan másolja a bérlő azonosítója](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Vegye fel a hivatkozásokat, és utasítások segítségével  

A Visual Studióban az Azure Storage ügyféloldali kódtár előzetes verzióját telepítse. Az a **eszközök** menü **Nuget-Csomagkezelő**, majd **Csomagkezelő konzol**. A konzolba írja be a következő parancsot:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Ezt követően adja hozzá a következő using utasításokat a a kódot:

```dotnet
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Az OAuth jogkivonat beszerzése az Azure AD

Ezután adja hozzá az Azure ad-jogkivonatot kérő metódus. A jogkivonatot kérni, hívja meg a [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) metódust.

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/"; // Storage resource endpoint
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token"; // Azure AD OAuth endpoint
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthEndpoint, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>A blokkblob létrehozása

Végezetül a hozzáférési jogkivonat segítségével hozható létre új tároló hitelesítő adatait, és ezeket a hitelesítő adatokat a blob létrehozásához használja:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
```

> [!NOTE]
> Az Azure AD integrálása az Azure Storage Azure Storage műveletekhez HTTPS használatát igényli.

## <a name="next-steps"></a>További lépések

- Az Azure-tárolás RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelése hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) storage-adatokkal](storage-auth-aad-rbac.md).
- Az Azure Storage felügyelt szolgáltatás identitás használatával kapcsolatos további tudnivalókért lásd: [hitelesítés az Azure Managed Service identitás (előzetes verzió) Azure AD-val](storage-auth-aad-msi.md).
- Az Azure parancssori felület és a PowerShell használatával az Azure AD identitással bejelentkezni, lásd: [CLI vagy a PowerShell használatával (előzetes verzió) Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).
- Az Azure AD integrálása az Azure-blobokat és üzenetsorokat kapcsolatos további információkért lásd a következő Azure Storage csapat blogbejegyzésben, [az Azure betekintő AD hitelesítés az Azure Storage bejelentése](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



