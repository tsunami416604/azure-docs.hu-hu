---
title: Az Azure Active Directory hitelesítést blob és üzenetsor adatok elérését (előzetes verzió) az alkalmazásokból |} A Microsoft Docs
description: Azure Active Directory használatával az alkalmazáson belül hitelesítéshez, és ezután a blobok és üzenetsorok (előzetes verzió) a kérelmek engedélyezését végzi.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/21/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 6d283840116a5e1f996602fd792456d3b8e8d9a0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456091"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues-preview"></a>Hozzáférés egy alkalmazás hitelesítése az Azure Active Directoryval, blobok és üzenetsorok (előzetes verzió)

Azure Active Directory (Azure AD) használatával az Azure Storage egyik legfőbb előnye az, hogy a hitelesítő adatok már nem kell tárolni a kódban. Ehelyett az OAuth 2.0 hozzáférési jogkivonatot kérhet az Azure ad-ből. Az Azure AD kezeli a hitelesítést, a rendszerbiztonsági tag (felhasználó, csoport vagy szolgáltatásnév) az alkalmazás futtatása. Ha a hitelesítés sikeres, az Azure AD az alkalmazáshoz a hozzáférési jogkivonatot ad vissza, és az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Storage-kérelmek engedélyezését végzi.

Ez a cikk bemutatja, hogyan hitelesítést az alkalmazás konfigurálása az Azure ad-ben. A kód példa funkciók .NET, de más nyelv használata hasonló megközelítést.

Mielőtt egy rendszerbiztonsági tag hitelesítheti az Azure Storage-alkalmazás, konfigurálja a szerepköralapú hozzáférés-vezérlés (RBAC) beállításait, hogy a rendszerbiztonsági tag. Az Azure Storage határozza meg, amely magában foglalja a tárolók és a várólisták engedélyeit RBAC-szerepkörökhöz. Az RBAC szerepkör van rendelve egy rendszerbiztonsági tag, amikor a rendszerbiztonsági tag hozzáférést ennek az erőforrásnak. További információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).

Az OAuth 2.0 kód engedélyezési folyamatával áttekintését lásd: [hozzáférés engedélyezése az Azure Active Directory webes alkalmazásokhoz az OAuth 2.0-kód használatával adja meg a folyamat](../../active-directory/develop/v1-protocols-oauth-code.md).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Az RBAC szerepkör hozzárendelése egy Azure AD rendszerbiztonsági tag

Hitelesítést végezni az Azure Storage-alkalmazás a rendszerbiztonsági tag, először konfigurálnia kell a rendszerbiztonsági tag szerepköralapú hozzáférés-vezérlés (RBAC) beállításait. Az Azure Storage határozza meg, amely magában foglalja a tárolók és a várólisták engedélyeit RBAC-szerepkörökhöz. Az RBAC szerepkör van rendelve egy rendszerbiztonsági tag, amikor a rendszerbiztonsági tag hozzáférést ennek az erőforrásnak. További információkért lásd: [kezelés hozzáférési jogosultságok RBAC (előzetes verzió) az adatok Azure Blob és üzenetsor](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Regisztrálja az alkalmazást az Azure AD-bérlő

Az első lépés az Azure AD-vel történő tárolási erőforrásokhoz való hozzáférés engedélyezésére az ügyfélalkalmazás regisztrál az Azure AD-bérlővel. Az alkalmazás regisztrálása lehetővé teszi, hogy, hogy az Azure meghívható [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) a kódból. Az ADAL API-alkalmazását az Azure ad-ben való hitelesítéshez használt biztosít. Az alkalmazás regisztrációja is lehetővé teszi, hogy engedélyezze a hívásokat az alkalmazásból az Azure Storage API-k egy hozzáférési jogkivonattal.

Ha regisztrálja az alkalmazást, adja meg információkat az alkalmazásról, az Azure ad-hez. Az Azure AD majd biztosít egy ügyfél-azonosító (más néven egy *Alkalmazásazonosító*), hogy használhatja-e az alkalmazás társítása az Azure ad-vel futásidőben. Az ügyfél-azonosító kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban](../../active-directory/develop/app-objects-and-service-principals.md).

Az Azure Storage-alkalmazás regisztrálásához kövesse a [egy alkalmazás hozzáadása](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) szakasz [alkalmazások integrálása az Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Natív alkalmazás regisztrálhatja alkalmazását, ha bármely érvényes URI-azonosítóját is megadhat a **átirányítási URI-t**. Az érték nem kell valódi végpontnak lennie.

![Képernyőfelvétel: a storage-alkalmazás regisztrálása az Azure ad-vel](./media/storage-auth-aad-app/app-registration.png)

Miután regisztrálta az alkalmazást, láthatja az alkalmazás azonosítója (vagy ügyfél-azonosító) alatt **beállítások**:

![Képernyőfelvétel: az ügyfél-azonosító](./media/storage-auth-aad-app/app-registration-client-id.png)

Egy alkalmazás regisztrálása az Azure ad-vel kapcsolatos további információkért lásd: [alkalmazások integrálása az Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>A regisztrált alkalmazás engedélyek megadása az Azure Storage

Ezt követően kell biztosítania az alkalmazás engedélyeit az Azure Storage API-k meghívására. Ez a lépés lehetővé teszi, hogy az alkalmazás hitelesítéséhez az Azure Storage az Azure AD-hívások.

1. Válassza ki az Azure Portal bal oldali navigációs ablaktáblán, **minden szolgáltatás**, és keressen rá a **Alkalmazásregisztrációk**.
2. Keresse meg az előző lépésben létrehozott regisztrált alkalmazás nevére.
3. Válassza ki a regisztrált alkalmazást, és kattintson a **beállítások**. Az a **API-hozzáférés** szakaszban jelölje be **szükséges engedélyek**.
4. Az a **szükséges engedélyek** panelen kattintson a **Hozzáadás** gombra.
5. A **API kiválasztása**, "Az Azure Storage" keresése és kiválasztása **Azure Storage** az eredmények listájában.

    ![Tároló ábrázoló engedélyeinek képernyőképe](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. Alatt **engedélyek kiválasztása**, melletti jelölőnégyzetet a **az Azure Storage elérése**, és kattintson a **kiválasztása**.
7. Kattintson a **Done** (Kész) gombra.

A **szükséges engedélyek** windows most látható, hogy az Azure AD-alkalmazást az Azure Storage és Azure Active Directory hozzáféréssel rendelkezik. Engedélyek az Azure AD automatikusan amikor először regisztrál az alkalmazás Azure ad-ben.

![Képernyőfelvétel: regisztrálja Alkalmazásengedélyek](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód példa: Egy blokkblob létrehozása

A kód példa bemutatja, hogyan kaphat hozzáférési tokent az Azure ad-ből. A hozzáférési jogkivonatot a megadott felhasználó hitelesítéséhez és majd a blokkblob létrehozására irányuló kérelem engedélyezéséhez használatos. A minta működéséhez hajtsa végre a fenti szakaszokban ismertetett lépéseket.

> [!NOTE]
> Az Azure Storage-fiók tulajdonosai akkor nem lesznek automatikusan hozzárendelve engedélyeket az adatok eléréséhez. Kell explicit módon saját magának egy RBAC szerepkör hozzárendelése az Azure Storage. Az előfizetés, erőforráscsoport, tárfiók, vagy a tároló vagy üzenetsor szintjén rendelhet. 
>
> Például a storage-fiók tulajdonosa, és a saját felhasználói identitás, futtassa a mintakódot, hozzá kell rendelnie az RBAC-szerepkör a Blobadatok Közreműködője saját magának. Ellenkező esetben a hívást a blob létrehozása sikertelen lesz, és HTTP-állapotkód: 403 (tiltott). További információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Az Azure AD-hitelesítés az ismert értékek

A rendszerbiztonsági tag az Azure AD-hitelesítés, néhány jól ismert értéket is a kódban kell.

#### <a name="azure-ad-authority"></a>Az Azure AD-szolgáltatót

A Microsoft nyilvános felhő, az alapszintű Azure AD-szolgáltatót a következő, ahol *bérlőazonosító* az Active Directory-bérlő azonosítója (vagy a címtár-azonosító):

`https://login.microsoftonline.com/<tenant-id>/`

A bérlő Azonosítóját az Azure AD-bérlő, a hitelesítéshez használandó azonosítja. A Bérlőazonosító lekéréséhez kövesse az ismertetett lépéseket **a Bérlőazonosító beszerzése az Azure Active Directory**.

#### <a name="storage-resource-id"></a>Storage erőforrás-azonosító

Az Azure Storage erőforrás-azonosítója használatával az Azure Storage-kérelmek hitelesítéséhez szükséges jogkivonat beszerzése:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>A Bérlőazonosító beszerzése az Azure Active Directory

A Bérlőazonosító lekéréséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Active Directoryban.
2. Kattintson a **Tulajdonságok** elemre.
3. Másolja a megadott GUID értéket a **címtár-azonosító**. Ennek az értéknek is nevezik a bérlő azonosítója.

![Képernyőfelvétel: hogyan kell másolni a bérlő azonosítója](./media/storage-auth-aad-app/aad-tenant-id.png)

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
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
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

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> Az Azure AD-integráció az Azure Storage for Azure Storage-műveletek HTTPS használatát igényli.

A fenti példában a .NET ügyféloldali kódtár kezeli a blokkblob típusú létrehozására vonatkozó kérelem engedélyezése. Egyéb tároló ügyfélkódtárai kezelik az engedélyt Önnek a kérelem. Azonban ha egy Azure Storage műveletet hívja meg az OAuth-token, a REST API használatával, majd szüksége lesz az OAuth-jogkivonat használatával a kérelem engedélyezéséhez.   

Az OAuth hozzáférési tokenek Blob és üzenetsor-szolgáltatás műveletek hívására, át kell adnia a hozzáférési jogkivonat a **engedélyezési** fejléc használatával a **tulajdonosi** séma, és adja meg a szolgáltatás verziója 2017-11-09 vagy nagyobb, mint az alábbi példában látható:

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

Azure Storage REST műveletek engedélyezése kapcsolatos további információkért lásd: [hitelesítés az Azure Active Directoryval (előzetes verzió)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory).

## <a name="next-steps"></a>További lépések

- Az Azure storage szolgáltatáshoz az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).
- Felügyelt identitások az Azure-erőforrások az Azure Storage használatával kapcsolatos további információkért lásd: [hitelesítés blobok és üzenetsorok az Azure-ral a hozzáférést az Azure-erőforrások (előzetes verzió) által felügyelt identitások](storage-auth-aad-msi.md).
- Ismerje meg, hogyan jelentkezhet be az Azure CLI és PowerShell az Azure AD-identitásnak, lásd: [CLI vagy a PowerShell (előzetes verzió) az Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).
- Az Azure-Blobok és üzenetsorok az Azure AD-integrációval kapcsolatos további információkért lásd: az Azure Storage csapat blogja tenne fel, [bejelentése a megtekintése az Azure AD-hitelesítés az Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



