---
title: A Graph API használata Azure Active Directory B2C
description: Egy Azure AD B2C bérlő felhasználóinak kezelése az Azure AD-Graph API meghívásával és az alkalmazás identitásának használatával a folyamat automatizálása érdekében.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2585b47d049047cc191bfc284c4486361917f1ed
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802061"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Az Azure AD Graph API használata

Azure Active Directory B2C (Azure AD B2C) bérlők több ezer vagy több millió felhasználót tartalmazhatnak. Ez azt jelenti, hogy számos gyakori bérlői felügyeleti feladatot programozott módon kell végrehajtani. Elsődleges példa a felhasználók kezelése.

Előfordulhat, hogy egy meglévő felhasználói tárolót kell áttelepítenie egy B2C-bérlőre. Előfordulhat, hogy a felhasználói regisztrációt a saját oldalán szeretné tárolni, és felhasználói fiókokat szeretne létrehozni a Azure AD B2C könyvtárban a jelenetek mögött. Az ilyen típusú feladatok lehetővé teszik felhasználói fiókok létrehozását, olvasását, frissítését és törlését. Ezeket a feladatokat az Azure AD Graph API használatával hajthatja végre.

A B2C-bérlők esetében két elsődleges mód van a Graph API való kommunikációra:

* **Interaktív**, egyszeri futtatású feladatok esetén a feladatok végrehajtásakor rendszergazdai fiókként kell működnie a B2C-bérlőben. Ehhez a módhoz a rendszergazdának be kell jelentkeznie a hitelesítő adatokkal, mielőtt a rendszergazda bármilyen hívást el tud végezni a Graph API.
* **Automatizált**, folyamatos feladatok esetén a felügyeleti feladatok elvégzéséhez szükséges jogosultságokkal ellátott valamilyen típusú szolgáltatásfiókot kell használnia. Az Azure AD-ben ezt egy alkalmazás regisztrálásával és az Azure AD-be történő hitelesítéssel teheti meg. Ezt egy olyan *alkalmazás-azonosító* használatával végezheti el, amely a [OAuth 2,0 ügyfél hitelesítő adatait](../active-directory/develop/service-to-service.md)használja. Ebben az esetben az alkalmazás önmaga, nem pedig felhasználóként működik a Graph API meghívásához.

Ebből a cikkből megtudhatja, hogyan hajthatja végre az automatikus használati esetet. Létre fog hozni egy .net 4,5 `B2CGraphClient` -et, amely felhasználói létrehozási, olvasási, frissítési és törlési (szifilisz-) műveleteket hajt végre. Az ügyfél egy Windows parancssori felülettel (CLI) fog rendelkezni, amely lehetővé teszi különböző módszerek meghívását. A kód azonban nem interaktív, automatizált módon viselkedik.

>[!IMPORTANT]
> Az [Azure ad Graph API](../active-directory/develop/active-directory-graph-api-quickstart.md) használatával felügyelheti a felhasználókat egy Azure ad B2C könyvtárban. Az Azure AD Graph API eltér a Microsoft Graph API-tól. További információ ebben az MSDN blogbejegyzésben: [Microsoft Graph vagy Azure ad Graph](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

## <a name="prerequisites"></a>Előfeltételek

Alkalmazások vagy felhasználók létrehozása előtt Azure AD B2C bérlőre van szükség. Ha még nem rendelkezik ilyennel, [hozzon létre egy Azure Active Directory B2C bérlőt](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Alkalmazás regisztrálása

Ha Azure AD B2C Bérlővel rendelkezik, regisztrálnia kell a felügyeleti alkalmazást a [Azure Portal](https://portal.azure.com)használatával. Azt is meg kell adnia, hogy a felügyeleti feladatok elvégzéséhez szükséges engedélyeket az automatikus parancsfájl vagy a felügyeleti alkalmazás nevében kell megadni.

### <a name="register-application-in-azure-active-directory"></a>Alkalmazás regisztrálása a Azure Active Directoryban

Ha az Azure AD Graph APIt a B2C-Bérlővel szeretné használni, regisztrálnia kell egy alkalmazást az Azure Active Directory alkalmazás regisztrációs munkafolyamata segítségével.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>API-hozzáférési engedélyek kiosztása

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Ügyfél titkos kulcsának létrehozása

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Most már rendelkezik egy olyan alkalmazással, amely jogosult a Azure AD B2C-bérlőben lévő felhasználók *létrehozására*, *olvasására*és *frissítésére* . Folytassa a következő szakasszal a felhasználói *törlési* és *jelszó-frissítési* engedélyek hozzáadásához.

## <a name="add-user-delete-and-password-update-permissions"></a>Felhasználói törlési és jelszó-frissítési engedélyek hozzáadása

A korábban megadott *olvasási és írási címtár-* adathozzáférési engedély **nem** tartalmazza a felhasználók törlésének vagy a jelszavuk frissítésének lehetőségét.

Ha szeretné, hogy az alkalmazás törölje a felhasználókat vagy frissítse a jelszavakat, meg kell adnia a *felhasználó rendszergazdai* szerepkörét.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és váltson arra a könyvtárra, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki **Azure ad B2C**.
1. A **kezelés**területen válassza a **szerepkörök és rendszergazdák**lehetőséget.
1. Válassza ki a **felhasználói rendszergazda** szerepkört.
1. Válassza a **hozzárendelés hozzáadása**elemet.
1. A **kijelölés** szövegmezőbe írja be a korábban regisztrált alkalmazás nevét, például *managementapp1*. Válassza ki az alkalmazást, amikor megjelenik a keresési eredmények között.
1. Válassza a **Hozzáadás** lehetőséget. Az engedélyek teljes propagálása eltarthat néhány percig.

A Azure AD B2C alkalmazás már rendelkezik a felhasználók törléséhez szükséges további engedélyekkel vagy a B2C-bérlő jelszavának frissítésével.

## <a name="get-the-sample-code"></a>A mintakód letöltése

A kód minta egy olyan .NET-konzol alkalmazás, amely a [Active Directory-hitelesítési tár (ADAL)](../active-directory/develop/active-directory-authentication-libraries.md) használja az Azure AD-Graph API való interakcióhoz. A kód azt mutatja be, hogyan hívhatja meg az API-t, hogy programozott módon felügyelje a felhasználókat egy Azure AD B2C bérlőn.

[Letöltheti a minta archívumát](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) vagy klónozott a GitHub-tárházat:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

A mintakód beszerzése után konfigurálja a környezetet, majd hozza létre a projektet:

1. Nyissa meg az `B2CGraphClient\B2CGraphClient.sln` elemet a Visual Studióban.
1. A **B2CGraphClient** projektben nyissa meg az *app. config* fájlt.
1. Cserélje le `<appSettings>` a szakaszt a következő XML-fájlra. Ezután cserélje `{your-b2c-tenant}` le a nevet a bérlő nevére `{Application ID}` , `{Client secret}` és a korábban feljegyzett értékekre.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Hozza létre a megoldást. Kattintson a jobb gombbal a **B2CGraphClient** -megoldásra a megoldáskezelő, majd válassza a **megoldás Újraépítés**elemet.

Ha a Build sikeres, a `B2C.exe` konzol alkalmazás a következő címen érhető el:. `B2CGraphClient\bin\Debug`

## <a name="review-the-sample-code"></a>Tekintse át a mintakódot

A B2CGraphClient használatához nyisson meg egy parancssort (`cmd.exe`), és váltson a `Debug` projekt könyvtárába. Ezután futtassa a `B2C Help` parancsot.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

A `B2C Help` parancs az elérhető alparancsok rövid leírását jeleníti meg. Minden alkalommal, amikor meghívja valamelyik alparancsát, `B2CGraphClient` küld egy kérést az Azure ad-Graph APInak.

Az alábbi fejezetek azt ismertetik, hogy az alkalmazás kódja hogyan hívja meg az Azure AD Graph API.

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Az Azure AD Graph APIre irányuló minden kérelemhez hozzáférési jogkivonat szükséges a hitelesítéshez. `B2CGraphClient`a nyílt forráskódú Active Directory-hitelesítési tár (ADAL) használatával segíti a hozzáférési jogkivonatok beszerzését. A ADAL megkönnyíti a tokenek beszerzését azáltal, hogy egy segítő API-t biztosít, és gondoskodik néhány fontos adatról, például a hozzáférési tokenek gyorsítótárazásáról. A jogkivonatok lekéréséhez azonban nem kell ADAL-t használnia. Ehelyett lekérheti a jogkivonatokat a HTTP-kérések manuális elindításával.

> [!NOTE]
> Az Azure AD Graph API használható hozzáférési jogkivonatok beszerzéséhez a ADAL v2 vagy újabb verzióját kell használnia. A ADAL v1 nem használható.

A végrehajtásakor a a `B2CGraphClient` osztály egy példányát hozza létre. `B2CGraphClient` Az osztály konstruktora a ADAL hitelesítési állványzatot állítja be:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Tegyük fel, `B2C Get-User` hogy a parancsot példaként használjuk.

Ha `B2C Get-User` a meghívása további argumentumok nélkül megtörténik, `B2CGraphClient.GetAllUsers()` az alkalmazás meghívja a metódust. `GetAllUsers()`Ezután meghívja `B2CGraphClient.SendGraphGetRequest()`a-t, amely egy HTTP Get-kérést küld az Azure ad-Graph APInak. A `B2CGraphClient.SendGraphGetRequest()` Get kérelem elküldése előtt először a ADAL használatával szerzi be a hozzáférési jogkivonatot:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

A ADAL `AuthenticationContext.AcquireToken()` metódus meghívásával lekérheti a Graph API hozzáférési jogkivonatát. A ADAL ezután visszaadja az alkalmazás identitását jelképező értéket `access_token` .

### <a name="read-users"></a>Felhasználók beolvasása

Ha szeretné lekérni a felhasználók listáját, vagy egy adott felhasználót az Azure ad-Graph API, http `GET` -kérést küldhet a `/users` végpontnak. A bérlő összes felhasználója számára a következőhöz hasonló kérelem fog kinézni:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

A kérelem megtekintéséhez futtassa a következőt:

 ```cmd
 B2C Get-User
 ```

Két fontos szempontot kell figyelembe venni:

* A ADAL használatával beszerzett hozzáférési jogkivonat a `Authorization` `Bearer` séma használatával kerül a fejlécbe.
* B2C-bérlők esetén a lekérdezési paramétert `api-version=1.6`kell használnia.

Mindkét részletet a `B2CGraphClient.SendGraphGetRequest()` metódus kezeli:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok létrehozása

Amikor felhasználói fiókokat hoz létre a B2C-bérlőben, http `POST` -kérést küldhet a `/users` végpontnak. A következő http `POST` -kérelem egy példát mutat be a bérlőben létrehozandó felhasználóra.

A felhasználói felhasználók létrehozásához a következő kérelemben szereplő tulajdonságok többsége szükséges. A `//` megjegyzések szerepelnek az ábrán – nem tartalmazzák azokat a tényleges kérelemben.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

A kérelem megtekintéséhez futtassa a következő parancsok egyikét:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

A `Create-User` parancs bemeneti paraméterként egy JSON-fájlt használ, amely egy felhasználói objektum JSON-ábrázolását tartalmazza. A kód mintájában két minta JSON-fájl található: `usertemplate-email.json` és `usertemplate-username.json`. Ezeket a fájlokat az igényeinek megfelelően módosíthatja. A fenti kötelező mezőkön kívül számos választható mező is szerepel a fájlokban.

A kötelező és a nem kötelező mezőkkel kapcsolatos további információkért lásd: [entitás és összetett típus referenciája | Graph API hivatkozás](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

Láthatja, hogyan épül fel a POST kérelem a `B2CGraphClient.SendGraphPostRequest()`következő helyen:

* Hozzáférési jogkivonatot `Authorization` csatol a kérelem fejlécébe.
* Beállítja `api-version=1.6`.
* Magában foglalja a kérelem törzsében található JSON felhasználói objektumot.

> [!NOTE]
> Ha a meglévő felhasználói tárolóból áttelepíteni kívánt fiókoknál alacsonyabb a jelszó erőssége, mint az [Azure ad B2C által kényszerített erős jelszó](active-directory-b2c-reference-password-complexity.md), akkor az erős jelszó megkövetelését `DisableStrongPassword` a következő `passwordPolicies`értékkeltilthatjale:tulajdonság. Módosíthatja például az előző felhasználói kérést a következő módon: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok frissítése

Amikor frissíti a felhasználói objektumokat, a folyamat a felhasználói objektumok létrehozásához használthoz hasonló, de a http `PATCH` -metódust használja:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Próbálja meg frissíteni a felhasználót úgy, hogy módosítja a JSON-fájlok egyes értékeit, `B2CGraphClient` majd a paranccsal futtatja a következő parancsok egyikét:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

A kérelem `B2CGraphClient.SendGraphPatchRequest()` elküldésével kapcsolatos részletekért tekintse meg a metódust.

### <a name="search-users"></a>Felhasználók keresése

A B2C-bérlő felhasználóinak kétféleképpen kereshet:

* Hivatkozzon a felhasználó **objektumazonosítóára**.
* Hivatkozzon a bejelentkezési termékazonosító, a `signInNames` tulajdonságra.

Futtassa a következő parancsok egyikét egy felhasználó kereséséhez:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Példa:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
```

### <a name="delete-users"></a>Felhasználók törlése

A felhasználók törléséhez használja a http `DELETE` metódust, és hozza létre az URL-címet a felhasználó objektum-azonosítójával:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Ha példát szeretne látni, írja be ezt a parancsot, és tekintse meg a konzolra kinyomtatott törlési kérelmet:

```cmd
B2C Delete-User <object-id-of-user>
```

A kérelem `B2CGraphClient.SendGraphDeleteRequest()` elküldésével kapcsolatos részletekért tekintse meg a metódust.

A felhasználók felügyelete mellett számos más műveletet is végrehajthat az Azure AD Graph API. Az [Azure AD Graph API dokumentációja](/previous-versions/azure/ad/graph/api/api-catalog) részletesen ismerteti az egyes műveleteket, valamint a mintavételi kérelmeket.

## <a name="use-custom-attributes"></a>Egyéni attribútumok használata

A legtöbb fogyasztói alkalmazásnak bizonyos típusú egyéni felhasználói profilt kell tárolnia. Ennek egyik módja, ha egyéni attribútumot határoz meg a B2C-bérlőben. Ezt az attribútumot ezután ugyanúgy kezelheti, mint bármely más tulajdonságot egy felhasználói objektumon. Frissítheti az attribútumot, törölheti az attribútumot, lekérdezheti az attribútumot, megküldheti az attribútumot jogcímként a bejelentkezési jogkivonatokban, és így tovább.

Ha egyéni attribútumot szeretne definiálni a B2C-bérlőben, tekintse meg a [B2C egyéni attribútumok referenciáját](active-directory-b2c-reference-custom-attr.md).

A B2C-bérlőben definiált egyéni attribútumok az alábbi `B2CGraphClient` parancsokkal tekinthetők meg:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A kimenet felfedi az egyes egyéni attribútumok részleteit. Példa:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Használhatja a teljes nevet, például `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`a (z) tulajdonságot a felhasználói objektumokban. Frissítse a JSON-fájlt az új tulajdonsággal és egy értékkel a tulajdonsághoz, majd futtassa a következőt:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>További lépések

A használatával `B2CGraphClient`olyan szolgáltatásalkalmazás is rendelkezésre áll, amely programozott módon felügyelheti a B2C-bérlői felhasználókat. `B2CGraphClient`a saját alkalmazás-identitását használja az Azure AD-Graph API való hitelesítéshez. Emellett a tokeneket is megvásárolja az ügyfél titkos kódjának használatával.

A funkciónak a saját alkalmazásba való beépítésekor jegyezze fel a B2C-alkalmazások néhány kulcsfontosságú pontját:

* Adja meg az alkalmazás számára a szükséges engedélyeket a bérlőn.
* Jelenleg a ADAL (nem MSAL) kell használnia a hozzáférési tokenek lekéréséhez. (A protokollon keresztüli üzenetküldést közvetlenül is elküldheti függvénytár használata nélkül.)
* A Graph API meghívásakor használja `api-version=1.6`a t.
* Ha felhasználói felhasználókat hoz létre és frissít, néhány tulajdonságot meg kell adni a fentiekben leírtak szerint.
