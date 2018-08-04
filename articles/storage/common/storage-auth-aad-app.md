---
title: Az Azure Active Directory hitelesítést blob és üzenetsor adatok elérését (előzetes verzió) az alkalmazásokból |} A Microsoft Docs
description: Azure Active Directory használatával az alkalmazáson belül hitelesítéshez, és ezt engedélyezze az Azure Storage-erőforrások (előzetes verzió) kéréseket.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: tamram
ms.openlocfilehash: f8c798307f27c5f96b15517e1f5bfb9d1762fec2
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506198"
---
# <a name="authenticate-with-azure-active-directory-from-an-azure-storage-application-preview"></a>Hitelesítés Azure Active Directoryval az Azure Storage-alkalmazás (előzetes verzió)

Azure Active Directory (Azure AD) használatával az Azure Storage egyik legfőbb előnye az, hogy a hitelesítő adatok már nem kell tárolni a kódban. Ehelyett az OAuth 2.0 hozzáférési jogkivonatot kérhet az Azure ad-ből. Az Azure AD kezeli a hitelesítést, a rendszerbiztonsági tag (felhasználó, csoport vagy szolgáltatásnév) az alkalmazás futtatása. Ha a hitelesítés sikeres, az Azure AD az alkalmazáshoz a hozzáférési jogkivonatot ad vissza, és az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Storage-kérelmek engedélyezését végzi.

Ez a cikk bemutatja, hogyan hitelesítést az alkalmazás konfigurálása az Azure ad-ben. A kód példa funkciók .NET, de más nyelv használata hasonló megközelítést.

Mielőtt egy rendszerbiztonsági tag hitelesítheti az Azure Storage-alkalmazás, konfigurálja a szerepköralapú hozzáférés-vezérlés (RBAC) beállításait, hogy a rendszerbiztonsági tag. Az Azure Storage határozza meg, amely magában foglalja a tárolók és a várólisták engedélyeit RBAC-szerepkörökhöz. Az RBAC szerepkör van rendelve egy rendszerbiztonsági tag, amikor a rendszerbiztonsági tag hozzáférést ennek az erőforrásnak. További információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).

Az OAuth 2.0 kód engedélyezési folyamatával áttekintését lásd: [hozzáférés engedélyezése az Azure Active Directory webes alkalmazásokhoz az OAuth 2.0-kód használatával adja meg a folyamat](../../active-directory/develop/active-directory-protocols-oauth-code.md).

> [!IMPORTANT]
> Ebben az előzetes verzióban csak a nem éles használatra szolgál. Éles szolgáltatásiszint-szerződések (SLA) nem lesz elérhető, amíg az Azure AD integrálása az Azure Storage általánosan elérhető lett deklarálva. Ha az Azure AD-integrációs még nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokeneket az alkalmazásokban. Az előzetes verzióra vonatkozó további információkért lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md).
>
> Az előzetes verzióban RBAC szerepkör-hozzárendelések eltarthat propagálása akár öt perc alatt.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Regisztrálja az alkalmazást az Azure AD-bérlő

Az első lépés az Azure AD-vel történő tárolási erőforrásokhoz való hozzáférés engedélyezésére az ügyfélalkalmazás regisztrál az Azure AD-bérlővel. Az alkalmazás regisztrálása lehetővé teszi, hogy, hogy az Azure meghívható [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) a kódból. Az ADAL API-alkalmazását az Azure ad-ben való hitelesítéshez használt biztosít. Az alkalmazás regisztrációja is lehetővé teszi, hogy engedélyezze a hívásokat az alkalmazásból az Azure Storage API-k egy hozzáférési jogkivonattal.

Ha regisztrálja az alkalmazást, adja meg információkat az alkalmazásról, az Azure ad-hez. Az Azure AD majd biztosít egy ügyfél-azonosító (más néven egy *Alkalmazásazonosító*), hogy használhatja-e az alkalmazás társítása az Azure ad-vel futásidőben. Az ügyfél-azonosító kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](../../active-directory/develop/active-directory-application-objects.md).

Az Azure Storage-alkalmazás regisztrálásához kövesse a [egy alkalmazás hozzáadása](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md#adding-an-application) szakasz [alkalmazások integrálása az Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Natív alkalmazás regisztrálhatja alkalmazását, ha bármely érvényes URI-azonosítóját is megadhat a **átirányítási URI-t**. Az érték nem kell valódi végpontnak lennie.

![A storage-alkalmazás regisztrálása az Azure ad-vel bemutató képernyőkép](./media/storage-auth-aad-app/app-registration.png)

Miután regisztrálta az alkalmazást, láthatja az alkalmazás azonosítója (vagy ügyfél-azonosító) alatt **beállítások**:

![Képernyőfelvétel az ügyfél-azonosító](./media/storage-auth-aad-app/app-registration-client-id.png)

Egy alkalmazás regisztrálása az Azure ad-vel kapcsolatos további információkért lásd: [alkalmazások integrálása az Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>A regisztrált alkalmazás engedélyek megadása az Azure Storage

Ezt követően kell biztosítania az alkalmazás engedélyeit az Azure Storage API-k meghívására. Ez a lépés lehetővé teszi, hogy az alkalmazás hitelesítéséhez az Azure Storage az Azure AD-hívások.

1. Válassza ki az Azure Portal bal oldali navigációs ablaktáblán, **minden szolgáltatás**, és keressen rá a **Alkalmazásregisztrációk**.
2. Keresse meg az előző lépésben létrehozott regisztrált alkalmazás nevére.
3. Válassza ki a regisztrált alkalmazást, és kattintson a **beállítások**. Az a **API-hozzáférés** szakaszban jelölje be **szükséges engedélyek**.
4. Az a **szükséges engedélyek** panelen kattintson a **Hozzáadás** gombra.
5. A **API kiválasztása**, "Az Azure Storage" keresése és kiválasztása **Azure Storage** az eredmények listájában.

    ![Tároló engedélyeinek ábrázoló képernyőfelvétel](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. Alatt **engedélyek kiválasztása**, melletti jelölőnégyzetet a **az Azure Storage elérése**, és kattintson a **kiválasztása**.
7. Kattintson a **Done** (Kész) gombra.

A **szükséges engedélyek** windows most látható, hogy az Azure AD-alkalmazást az Azure Storage és Azure Active Directory hozzáféréssel rendelkezik. Engedélyek az Azure AD automatikusan amikor először regisztrál az alkalmazás Azure ad-ben.

![Bejelentkezéshez Alkalmazásengedélyek regisztrálása](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET mintakód: block blob létrehozása

A kód példa bemutatja, hogyan kaphat hozzáférési tokent az Azure ad-ből. A hozzáférési jogkivonatot a megadott felhasználó hitelesítéséhez és majd a blokkblob létrehozására irányuló kérelem engedélyezéséhez használatos. A minta működéséhez hajtsa végre a fenti szakaszokban ismertetett lépéseket.

> [!NOTE]
> Az Azure Storage-fiók tulajdonosai akkor nem lesznek automatikusan hozzárendelve engedélyeket az adatok eléréséhez. Kell explicit módon saját magának egy RBAC szerepkör hozzárendelése az Azure Storage. Az előfizetés, erőforráscsoport, tárfiók, vagy a tároló vagy üzenetsor szintjén rendelhet. 
>
> Például a storage-fiók tulajdonosa, és a saját felhasználói identitás, futtassa a mintakódot, hozzá kell rendelnie az RBAC-szerepkör a Blobadatok Közreműködője saját magának. Ellenkező esetben a hívást a blob létrehozása sikertelen lesz, és HTTP-állapotkód: 403 (tiltott). További információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Az Azure AD-hitelesítés az ismert értékek

A rendszerbiztonsági tag az Azure AD-hitelesítés, néhány jól ismert értéket is a kódban kell.

#### <a name="azure-ad-oauth-endpoint"></a>Az Azure AD OAuth-végpont

Az alapszintű Azure AD-szolgáltató végpont az OAuth 2.0 a következő, ahol *bérlőazonosító* az Active Directory-bérlő azonosítója (vagy a címtár-azonosító):

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

A bérlő Azonosítóját az Azure AD-bérlő, a hitelesítéshez használandó azonosítja. A Bérlőazonosító lekéréséhez kövesse az ismertetett lépéseket **a Bérlőazonosító beszerzése az Azure Active Directory**.

#### <a name="storage-resource-id"></a>Storage erőforrás-azonosító

Az Azure Storage erőforrás-azonosítója használatával az Azure Storage-kérelmek hitelesítéséhez szükséges jogkivonat beszerzése:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>A Bérlőazonosító beszerzése az Azure Active Directory

A Bérlőazonosító lekéréséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Active Directoryban.
2. Kattintson a **Tulajdonságok** elemre.
3. Másolja a megadott GUID értéket a **címtár-azonosító**. Ennek az értéknek is nevezik a bérlő azonosítója.

![A Bérlőazonosító másolása bemutató képernyőkép](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Mutató hivatkozásokat tudjon felvenni, és utasításokkal  

A Visual Studióban az Azure Storage ügyféloldali kódtár előzetes verzióját telepítse. Az a **eszközök** menüjében válassza **Nuget-Csomagkezelő**, majd **Package Manager Console**. Az ügyféloldali kódtár legújabb verziójának telepítéséhez a .NET-hez a konzolba írja be a következő parancsot:

```
Install-Package WindowsAzure.Storage
```

Az ADAL legújabb verzióját is telepítheti:

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

Ezután adja hozzá a következő using utasításokat a kódját:

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Az OAuth jogkivonat beszerzése az Azure ad-ből

Ezután adjon meg egy metódust, amely az Azure ad-ből tokent kér. A jogkivonat kéréséhez, hívja meg a [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) metódust. Győződjön meg arról, hogy rendelkezik-e a következő értékeket a korábban már követte a lépéseket:

- A Bérlőazonosító (könyvtár)
- (Alkalmazás) ügyfél-azonosító
- Ügyfél átirányítási URI

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token";
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

Végül a hozzáférési jogkivonat segítségével hozzon létre új tároló hitelesítő adatait, és ezeket a hitelesítő adatokat használja a blob létrehozásához:

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
> Az Azure AD-integráció az Azure Storage for Azure Storage-műveletek HTTPS használatát igényli.

## <a name="next-steps"></a>További lépések

- Az Azure storage szolgáltatáshoz az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).
- Az Azure Storage Felügyeltszolgáltatás-identitás használatával kapcsolatos további információkért lásd: [hitelesítés az Azure Felügyeltszolgáltatás-identitás (előzetes verzió) az Azure AD-vel](storage-auth-aad-msi.md).
- Ismerje meg, hogyan jelentkezhet be az Azure CLI és PowerShell az Azure AD-identitásnak, lásd: [CLI vagy a PowerShell (előzetes verzió) az Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).
- Az Azure-Blobok és üzenetsorok az Azure AD-integrációval kapcsolatos további információkért lásd: az Azure Storage csapat blogja tenne fel, [bejelentése a megtekintése az Azure AD-hitelesítés az Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



