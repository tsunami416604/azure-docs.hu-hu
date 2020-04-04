---
title: Felhasználók kezelése a Microsoft Graph API-val
titleSuffix: Azure AD B2C
description: A felhasználók kezelése egy Azure AD B2C-bérlőben a Microsoft Graph API hívásával és egy alkalmazásidentitás használatával a folyamat automatizálásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637332"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Azure AD B2C felhasználói fiókok kezelése a Microsoft Graph segítségével

A Microsoft Graph lehetővé teszi, hogy az Azure AD B2C címtárban lévő felhasználói fiókok kezelése a Microsoft Graph API-ban hozzon létre, olvasd el, frissítse és törölje a metódusokat. Áttelepíthet egy meglévő felhasználói tárolót egy Azure AD B2C-bérlőre, és a Microsoft Graph API-val más felhasználói fiókkezelési műveleteket hajthat végre.

Az alábbi szakaszokban az Azure AD B2C felhasználói felügyelet a Microsoft Graph API-val a legfontosabb szempontok at mutatják be. Az itt bemutatott Microsoft Graph API-műveletek, -típusok és -tulajdonságok a Microsoft Graph API referenciadokumentációjában megjelenő műveletek egy részét képezik.

## <a name="register-a-management-application"></a>Felügyeleti alkalmazás regisztrálása

Mielőtt bármely felhasználófelügyeleti alkalmazás vagy parancsfájl írása i a konkretincia az Azure AD B2C-bérlő, szüksége van egy alkalmazás regisztráció, amely megadja az engedélyeket erre.

Az útmutató cikk lépéseit követve hozzon létre egy alkalmazásregisztrációt, amelyet a felügyeleti alkalmazás használhat:

[Az Azure AD B2C kezelése a Microsoft Graph segítségével](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Felhasználókezelés Microsoft Graph-műveletek

A [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/user)a következő felhasználókezelési műveleteket használja:

- [Felhasználók listájának beszereznie](https://docs.microsoft.com/graph/api/user-list)
- [Felhasználó létrehozása](https://docs.microsoft.com/graph/api/user-post-users)
- [Felhasználó beszerezni](https://docs.microsoft.com/graph/api/user-get)
- [Felhasználó frissítése](https://docs.microsoft.com/graph/api/user-update)
- [Felhasználó törlése](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Felhasználói tulajdonságok

### <a name="display-name-property"></a>Név tulajdonság megjelenítése

A `displayName` a felhasználó számára az Azure Portal felhasználói felügyeletében megjelenítendő név, és az Azure AD B2C hozzáférési jogkivonatban visszatér az alkalmazáshoz. Ez a tulajdonság kötelező.

### <a name="identities-property"></a>Identitások tulajdonsága

Az ügyfélfiók, amely lehet fogyasztó, partner vagy állampolgár, társítható az alábbi identitástípusokhoz:

- **Helyi** identitás – A felhasználónév és a jelszó helyileg az Azure AD B2C könyvtárban tárolódik. Gyakran hivatkozunk ezekre az identitásokra "helyi fiókokként".
- **Összevont** identitás – *Közösségi* vagy *vállalati* fiókoknéven is ismert, a felhasználó identitását egy összevont identitásszolgáltató, például a Facebook, a Microsoft, az ADFS vagy a Salesforce kezeli.

Az ügyfélfiókkal rendelkező felhasználók több identitással is bejelentkezhetnek. Például felhasználónév, e-mail, alkalmazottazonosító, kormányzati azonosító és mások. Egy fiók több identitással is rendelkezhet, helyi és közösségi, ugyanazzal a jelszóval.

A Microsoft Graph API-ban mind a helyi, mind az `identities` összevont identitások a felhasználói attribútumban tárolódnak, amely [objectIdentity][graph-objectIdentity]típusú. A `identities` gyűjtemény a felhasználói fiókba való bejelentkezéshez használt identitások készletét jelöli. Ez a gyűjtemény lehetővé teszi a felhasználó számára, hogy a felhasználói fiókba a hozzá tartozó identitások bármelyikével jelentkezzen be.

| Tulajdonság   | Típus |Leírás|
|:---------------|:--------|:----------|
|jelInType|sztring| Megadja a címtárban lévő felhasználói bejelentkezési típusokat. Helyi fiók `emailAddress`esetén: `emailAddress2` `emailAddress3`, `userName` `emailAddress1`, , , , vagy bármely más típus. A közösségi fiókot `federated`a számára kell állítani.|
|kiállító|sztring|Megadja az identitás kibocsátóját. Helyi fiókok esetén (ahol **a signInType** nem `federated`) ez a tulajdonság a `contoso.onmicrosoft.com`helyi B2C-bérlő alapértelmezett tartományneve, például . A közösségi identitás (ahol a `federated` **signInType** ) esetében az érték a kibocsátó neve, például`facebook.com`|
|issuerAssignedId|sztring|Megadja a kibocsátó által a felhasználóhoz rendelt egyedi azonosítót. A **kibocsátó** és a **kibocsátóAssignedId** kombinációjának egyedinek kell lennie a bérlőn belül. Helyi fiók esetén, ha **a signInType** vagy `emailAddress` `userName`a , a felhasználó bejelentkezési nevét jelöli.<br>Ha **a signInType** beállítása: <ul><li>`emailAddress`(vagy `emailAddress` kezdődik, `emailAddress1`mint ) **issuerADazonosítóazonosítónak** érvényes e-mail címnek kell lennie</li><li>`userName`(vagy bármely más érték), **a issuerAssignedId-nek** [az e-mail cím](https://tools.ietf.org/html/rfc3696#section-3) érvényes helyi részének kell lennie</li><li>`federated`, **a issuerAssignedId** az összevont fiók egyedi azonosítóját jelöli</li></ul>|

A következő **Identitások** tulajdonság, egy helyi fiók identitása bejelentkezési névvel, egy e-mail-cím bejelentkezési cím, és egy közösségi identitás. 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Összevont identitások esetén az identitásszolgáltatótól függően a **issuerAssignedId** egy egyedi érték egy adott felhasználó számára alkalmazásonként vagy fejlesztési fiókonként. Konfigurálja az Azure AD B2C-szabályzatot ugyanazzal az alkalmazásazonosítóval, amelyet korábban a közösségi szolgáltató vagy egy másik alkalmazás rendelt hozzá ugyanazon a fejlesztési fiókon belül.

### <a name="password-profile-property"></a>Jelszóprofil tulajdonsága

Helyi identitás esetén a **passwordProfile** tulajdonság szükséges, és tartalmazza a felhasználó jelszavát. A `forceChangePasswordNextSignIn` tulajdonságnak `false`a beállítására kell, hogy legyen.

Összevont (közösségi) identitás esetén a **passwordProfile** tulajdonság nem kötelező.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Jelszóházirend tulajdonsága

Az Azure AD B2C jelszóházirend (a helyi fiókok) az Azure Active Directory [erős jelszóerősség-házirend.](../active-directory/authentication/concept-sspr-policy.md) Az Azure AD B2C-regisztráció vagy bejelentkezési és jelszó-visszaállítási szabályzatok megkövetelik ezt az erős jelszóerősség, és nem jár le a jelszavakat.

A felhasználói áttelepítési forgatókönyvek, ha a fiókok áttelepíteni kívánt gyengébb jelszó erőssége, mint az Azure AD B2C által kényszerített [erős jelszóerősséget,](../active-directory/authentication/concept-sspr-policy.md) letilthatja az erős jelszókövetelmény. Az alapértelmezett jelszóházirend módosításához `passwordPolicies` állítsa `DisableStrongPassword`a tulajdonságot a értékre. A felhasználói kérelem létrehozása például a következőképpen módosulhat:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Bővítmény tulajdonságai

Minden ügyfél felé néző alkalmazás egyedi követelményeket támaszt az összegyűjtendő információkkal kapcsolatban. Az Azure AD B2C-bérlő egy beépített készlettel rendelkezik a tulajdonságokban, például a keresztnév, a vezetéknév, a város és az irányítószám között tárolt információk. Az Azure AD B2C segítségével kiterjesztheti az egyes ügyfélfiókokban tárolt tulajdonságok készletét. Az egyéni attribútumok meghatározásáról további információt [az egyéni attribútumok (felhasználói folyamatok)](user-flow-custom-attributes.md) és [az egyéni attribútumok (egyéni házirendek)](custom-policy-custom-attributes.md)című témakörben talál.

A Microsoft Graph API támogatja a bővítményattribútumokkal rendelkező felhasználók létrehozását és frissítését. A Graph API bővítményattribútumait a `extension_ApplicationObjectID_attributename`konvenció használatával nevezi el a program. Példa:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Kódminta

Ez a kódminta egy .NET Core konzolalkalmazás, amely a [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) segítségével kommunikál a Microsoft Graph API-val. A kód bemutatja, hogyan hívja meg az API-t az Azure AD B2C-bérlőben lévő felhasználók programozott kezeléséhez.
[Letöltheti a mintaarchívumot](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), [böngészhet a](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) GitHubon lévő tárházban, vagy klónozhatja a tárházat:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Miután megszerezte a kódmintát, konfigurálja a környezetéhez, majd építse létre a projektet:

1. Nyissa meg a projektet a [Visual Studio](https://visualstudio.microsoft.com) vagy a Visual Studio [Code programban.](https://code.visualstudio.com)
1. Nyissa meg a következő fájlt: `src/appsettings.json`.
1. A `appSettings` szakaszban `your-b2c-tenant` cserélje le a bérlő `Application (client) ID` nevét, és `Client secret` a felügyeleti alkalmazás regisztrációjának értékeit (lásd a jelen cikk [felügyeleti alkalmazás regisztrálása](#register-a-management-application) című szakaszát).
1. Nyisson meg egy konzolablakot a tártár `src` helyi klónjában, váltson át a könyvtárba, majd építse fel a projektet:
    ```console
    cd src
    dotnet build
    ```
1. Futtassa az `dotnet` alkalmazást a következő paranccsal:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

Az alkalmazás megjeleníti a végrehajtható parancsok listáját. Például az összes felhasználó lekérni, egyetlen felhasználót beszerezni, törölni egy felhasználót, frissíteni a felhasználó jelszavát, és tömeges importálást.

### <a name="code-discussion"></a>Kódmegbeszélés

A mintakód a [Microsoft Graph SDK-t](https://docs.microsoft.com/graph/sdks/sdks-overview)használja, amelynek célja a Microsoft Graph-hoz hozzáférő kiváló minőségű, hatékony és rugalmas alkalmazások létrehozásának egyszerűsítése.

A Microsoft Graph API-ra irányuló kérelmek hitelesítéshez hozzáférési jogkivonatot igényelnek. A megoldás a [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet csomagot használja, amely a Microsoft Graph SDK-val való használatra a Microsoft Authentication Library (MSAL) hitelesítési forgatókönyv-alapú burkolóját biztosítja.

A `RunAsync` módszer a _Program.cs_ fájlban:

1. Alkalmazásbeállítások beolvasása az _appsettings.json_ fájlból
1. Inicializálja az auth szolgáltató t [OAuth 2.0 ügyfél hitelesítő adatok támogatási](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) folyamat. Az ügyfél hitelesítő adatok támogatási folyamat, az alkalmazás képes-hoz kap egy hozzáférési jogkivonatot a Microsoft Graph API-t.
1. Beállítja a Microsoft Graph szolgáltatásügyfelet az hitelesítésszolgáltatóval:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Az inicializált *GraphServiceClient* ezután _UserService.cs_ a felhasználókezelési műveletek végrehajtásához. Például a bérlőfelhasználói fiókok listájának beszerzése:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Az API-hívások kezdeményezése a Microsoft Graph SDK-k használatával](https://docs.microsoft.com/graph/sdks/create-requests) információkat `$select` tartalmaz arról, hogyan lehet adatokat olvasni `$filter` és `$orderBy` írni a Microsoft Graph-ból, hogyan szabályozhatja a visszaadott tulajdonságokat, egyéni lekérdezési paramétereket adhat meg, valamint a és a lekérdezési paramétereket.

## <a name="next-steps"></a>További lépések

Az Azure AD B2C-erőforrásokhoz támogatott Microsoft Graph API-műveletek teljes indexét az [Azure AD B2C számára elérhető Microsoft Graph-műveletek](microsoft-graph-operations.md)ben találja.

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
