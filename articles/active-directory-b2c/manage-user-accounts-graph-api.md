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
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178874"
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

## <a name="next-steps"></a>Következő lépések

Azure AD B2C erőforrásokhoz támogatott Microsoft Graph API-műveletek teljes indexét itt tekintheti meg: [Azure ad B2C számára elérhető Microsoft Graph műveletek](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
