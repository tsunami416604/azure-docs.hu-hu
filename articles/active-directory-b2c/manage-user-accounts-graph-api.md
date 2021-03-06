---
title: Felhasználók kezelése a Microsoft Graph API-val
titleSuffix: Azure AD B2C
description: Egy Azure AD B2C bérlő felhasználóinak kezelése a Microsoft Graph API meghívásával és az alkalmazás identitásának használatával a folyamat automatizálása érdekében.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6abc3316e18fc70a2969bc220fd75e10e10f0e6e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507778"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Azure AD B2C felhasználói fiókok kezelése Microsoft Graph

A Microsoft Graph lehetővé teszi, hogy a Microsoft Graph API-ban létrehozási, olvasási, frissítési és törlési módszerekkel kezelhesse a Azure AD B2C címtárban lévő felhasználói fiókokat. A meglévő felhasználói tárolókat áttelepítheti egy Azure AD B2C bérlőre, és más felhasználóifiók-kezelési műveleteket hajthat végre a Microsoft Graph API meghívásával.

Az alábbi szakaszokban megtekintheti Azure AD B2C felhasználói kezelésének főbb szempontjait a Microsoft Graph API-val. Az itt bemutatott Microsoft Graph API-műveletek, típusok és tulajdonságok a Microsoft Graph API-dokumentációban megjelenő részhalmaza.

## <a name="register-a-management-application"></a>Felügyeleti alkalmazás regisztrálása

Ahhoz, hogy az Ön által írt felhasználói felügyeleti alkalmazások vagy parancsfájlok kommunikálhatnak a Azure AD B2C-bérlő erőforrásaival, szüksége lesz egy alkalmazás-regisztrációra, amely erre engedélyt ad.

A kezelési alkalmazás által használható alkalmazás-regisztráció létrehozásához kövesse a jelen útmutató cikk lépéseit:

[Azure AD B2C kezelése Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Felhasználói felügyeleti Microsoft Graph műveletek

A következő felhasználói kezelési műveletek érhetők el a [Microsoft Graph API](/graph/api/resources/user)-ban:

- [Felhasználók listájának beolvasása](/graph/api/user-list)
- [Felhasználó létrehozása](/graph/api/user-post-users)
- [Felhasználó beolvasása](/graph/api/user-get)
- [Felhasználó frissítése](/graph/api/user-update)
- [Felhasználó törlése](/graph/api/user-delete)

## <a name="user-properties"></a>Felhasználói tulajdonságok

### <a name="display-name-property"></a>Megjelenítendő név tulajdonság

A a `displayName` felhasználó Azure Portal felhasználói felügyeletében megjelenítendő név, a hozzáférési jogkivonat Azure ad B2C pedig visszaadja az alkalmazásnak. Ezt a tulajdonságot kötelező megadni.

### <a name="identities-property"></a>Identitások tulajdonság

Az alábbi identitás-típusokhoz lehet hozzárendelni egy felhasználói fiókot, amely lehet fogyasztó, partner vagy polgár:

- **Helyi** identitás – a felhasználónevet és a jelszót a rendszer helyileg tárolja a Azure ad B2C könyvtárban. Gyakran hivatkozunk ezekre az identitásokra "helyi fiókokként".
- **Összevont** identitás – más néven *közösségi* vagy *vállalati* fiók, a felhasználó identitását egy összevont identitás-szolgáltató kezeli, például Facebook, Microsoft, ADFS vagy Salesforce.

Az ügyfél fiókkal rendelkező felhasználók több identitással is bejelentkezhetnek. Ilyen például a Felhasználónév, az e-mail, az alkalmazott azonosítója, a kormányzati azonosító és egyebek. Egyetlen fiók több identitással is rendelkezhet, amelyek helyi és közösségi, ugyanazzal a jelszóval.

A Microsoft Graph API-ban a helyi és az összevont identitások is a User `identities` attribútumban tárolódnak, amely [objectIdentity][graph-objectIdentity]típusú. A `identities` gyűjtemény a felhasználói fiókba való bejelentkezéshez használt identitások készletét jelöli. Ez a gyűjtemény lehetővé teszi, hogy a felhasználó a hozzá tartozó identitásokkal jelentkezzen be a felhasználói fiókba.

| Tulajdonság   | Típus |Leírás|
|:---------------|:--------|:----------|
|signInType|sztring| Megadja a felhasználói bejelentkezési típusokat a címtárban. Helyi fiók esetén:,,,,  `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3`  `userName` vagy bármely más hasonló típus. A közösségi fiókot a következőre kell beállítani:  `federated` .|
|kiállító|sztring|Megadja az identitás kiállítóját. Helyi fiókok esetében (ahol a **signInType** nem `federated` ), ez a tulajdonság a helyi B2C-bérlő alapértelmezett tartománynevét adja meg, például: `contoso.onmicrosoft.com` . A közösségi identitás (ahol  a signInType `federated` ) értéke a kibocsátó neve, például:`facebook.com`|
|issuerAssignedId|sztring|A kiállító által a felhasználóhoz rendelt egyedi azonosítót határozza meg. A **kibocsátó** és a **issuerAssignedId** kombinációjának egyedinek kell lennie a bérlőn belül. Helyi fiók esetén, ha a **signInType** értéke `emailAddress` vagy `userName` , a felhasználó bejelentkezési nevét jelöli.<br>Ha a **signInType** értéke: <ul><li>`emailAddress` (vagy kezdődik a `emailAddress` Like `emailAddress1` ) **issuerAssignedId** érvényes e-mail-címnek kell lennie</li><li>`userName`(vagy bármely más érték) a **issuerAssignedId** egy [e-mail-cím érvényes helyi részének](https://tools.ietf.org/html/rfc3696#section-3) kell lennie</li><li>`federated`, a **issuerAssignedId** az összevont fiók egyedi azonosítóját jelöli.</li></ul>|

A következő **identitások** tulajdonság egy helyi fiók identitásával, egy bejelentkezési névvel, egy e-mail-címmel és egy közösségi identitással. 

 ```json
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

Az összevont identitások esetében az azonosítótól függően a **issuerAssignedId** egy adott felhasználó egyedi értéke egy alkalmazás-vagy fejlesztési fiókban. Konfigurálja a Azure AD B2C szabályzatot ugyanazzal az alkalmazás-AZONOSÍTÓval, amelyet korábban a közösségi szolgáltató vagy egy másik alkalmazás rendelt hozzá ugyanazon a fejlesztési fiókon belül.

### <a name="password-profile-property"></a>Password Profile tulajdonság

Helyi identitás esetén a **passwordProfile** tulajdonság megadása kötelező, és tartalmazza a felhasználó jelszavát. A tulajdonság értékének a következőnek `forceChangePasswordNextSignIn` kell lennie: `false` .

Összevont (közösségi) identitás esetén a **passwordProfile** tulajdonság nem szükséges.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Jelszóházirend tulajdonsága

A Azure AD B2C jelszóházirend (helyi fiókok esetében) a Azure Active Directory [erős jelszó-erősségi](../active-directory/authentication/concept-sspr-policy.md) házirenden alapul. A Azure AD B2C regisztrációs vagy bejelentkezési és jelszó-visszaállítási szabályzatok ezt az erős jelszót igénylik, és nem járnak le a jelszavak.

A felhasználói áttelepítési forgatókönyvek esetében, ha az áttelepíteni kívánt fiókok gyengébb jelszóval rendelkeznek, mint az Azure AD B2C által kényszerített [erős jelszó](../active-directory/authentication/concept-sspr-policy.md) , letilthatja a jelszó erős követelményét. Az alapértelmezett jelszóházirend módosításához állítsa a tulajdonságot a következőre: `passwordPolicies` `DisableStrongPassword` . A felhasználói kérelem létrehozása például a következőképpen módosítható:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Bővítmény tulajdonságai

Minden ügyfélre kiterjedő alkalmazás egyedi követelményekkel rendelkezik a gyűjtött adatokhoz. A Azure AD B2C bérlője a tulajdonságok területen tárolt beépített információkkal, például a megadott névvel, a vezetéknévvel, a várossal és az irányítószámmal rendelkezik. A Azure AD B2C használatával kiterjesztheti az egyes felhasználói fiókokban tárolt tulajdonságok készletét. Az egyéni attribútumok definiálásával kapcsolatos további információkért lásd: [Egyéni attribútumok](user-flow-custom-attributes.md).

Microsoft Graph API támogatja a bővítmény-attribútumokkal rendelkező felhasználók létrehozását és frissítését. A Graph API-bővítmény attribútumai az egyezmény használatával vannak elnevezve `extension_ApplicationClientID_attributename` , ahol a a `ApplicationClientID` (z) az alkalmazás **alkalmazás-(ügyfél-) azonosítója** `b2c-extensions-app` (a Azure Portal **Alkalmazásregisztrációk**  >  **összes alkalmazásában** található). Vegye figyelembe, hogy a bővítmény attribútumának nevében szereplő **alkalmazás-azonosító (ügyfél)** nem tartalmaz kötőjelet. Például:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Mintakód: felhasználói fiókok programozott felügyelete

Ez a mintakód egy olyan .NET Core Console-alkalmazás, amely a [Microsoft Graph SDK](/graph/sdks/sdks-overview) -val együttműködik a Microsoft Graph API-val. A kód azt mutatja be, hogyan hívhatja meg az API-t, hogy programozott módon felügyelje a felhasználókat egy Azure AD B2C bérlőn.
[Letöltheti a minta archívumot](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [böngészheti a tárházat](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) a githubon, vagy megnyithatja az adattárat:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

A mintakód beszerzése után konfigurálja a környezetet, majd hozza létre a projektet:

1. Nyissa meg a projektet a [Visual Studióban](https://visualstudio.microsoft.com) vagy a [Visual Studio Code](https://code.visualstudio.com)-ban.
1. Nyissa meg a következő fájlt: `src/appsettings.json`.
1. A `appSettings` szakaszban cserélje le a `your-b2c-tenant` nevet a bérlő nevére, és a `Application (client) ID` `Client secret` felügyeleti alkalmazás regisztrációjának értékeire (lásd a cikk [felügyeleti alkalmazás regisztrálása](#register-a-management-application) című szakaszát).
1. Nyisson meg egy konzol ablakot a tárház helyi klónján belül, váltson át a `src` címtárra, majd hozza létre a projektet:
    ```console
    cd src
    dotnet build
    ```
1. Futtassa az alkalmazást a `dotnet` paranccsal:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Az alkalmazás megjeleníti a végrehajtható parancsok listáját. Például az összes felhasználó beszerzése, egyetlen felhasználó beszerzése, egy felhasználó törlése, a felhasználó jelszavának frissítése és a tömeges importálás.

### <a name="code-discussion"></a>Kód-vitafórum

A mintakód a [Microsoft Graph SDK](/graph/sdks/sdks-overview)-t használja, amelynek célja, hogy leegyszerűsítse a Microsoft Graphhoz hozzáférő, magas színvonalú, hatékony és rugalmas alkalmazások kialakítását.

A Microsoft Graph API-nak benyújtott minden kérelemhez hozzáférési jogkivonat szükséges a hitelesítéshez. A megoldás a [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet csomag használatát teszi lehetővé, amely a Microsoft Authentication Library (MSAL) hitelesítési forgatókönyv-alapú burkolóját biztosítja a Microsoft Graph SDK-val való használatra.

A `RunAsync` metódus a _program.cs_ fájlban:

1. Az Alkalmazásbeállítások beolvasása a _appsettings.js_ fájlból
1. A [OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) folyamatát használva inicializálja a hitelesítési szolgáltatót. Az ügyfél hitelesítő adatainak megadása esetén az alkalmazás hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához.
1. Beállítja a Microsoft Graph szolgáltatási ügyfelet az Auth szolgáltatóval:

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

A rendszer ezt követően a inicializált *GraphServiceClient* használja a _UserService.cs_ a felhasználói felügyeleti műveletek végrehajtásához. Például a bérlőben lévő felhasználói fiókok listájának beolvasása:

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

[Az Microsoft Graph SDK-kat használó API-hívások a](/graph/sdks/create-requests) Microsoft Graph információk olvasására és írására, a `$select` visszaadott tulajdonságok szabályozására, az egyéni lekérdezési paraméterek megadására, valamint a `$filter` és a lekérdezés paramétereinek használatára vonatkozó információkat tartalmaznak `$orderBy` .

## <a name="next-steps"></a>További lépések

Azure AD B2C erőforrásokhoz támogatott Microsoft Graph API-műveletek teljes indexét itt tekintheti meg: [Azure ad B2C számára elérhető Microsoft Graph műveletek](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)