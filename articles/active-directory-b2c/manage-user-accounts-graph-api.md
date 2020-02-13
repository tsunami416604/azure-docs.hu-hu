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
ms.openlocfilehash: 71b437f57f9d9e6e18af88d6413269cac6f66c47
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161664"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: az Azure AD-Graph API használata

Azure Active Directory B2C (Azure AD B2C) bérlők több ezer vagy több millió felhasználót tartalmazhatnak. Ez azt jelenti, hogy számos gyakori bérlői felügyeleti feladatot programozott módon kell végrehajtani. Elsődleges példa a felhasználók kezelése.

Előfordulhat, hogy egy meglévő felhasználói tárolót kell áttelepítenie egy B2C-bérlőre. Előfordulhat, hogy a felhasználói regisztrációt a saját oldalán szeretné tárolni, és felhasználói fiókokat szeretne létrehozni a Azure AD B2C könyvtárban a jelenetek mögött. Az ilyen típusú feladatok lehetővé teszik felhasználói fiókok létrehozását, olvasását, frissítését és törlését. Ezeket a feladatokat az Azure AD Graph API használatával hajthatja végre.

A B2C-bérlők esetében két elsődleges mód van a Graph API való kommunikációra:

* **Interaktív**, egyszeri futtatású feladatok esetén a feladatok végrehajtásakor rendszergazdai fiókként kell működnie a B2C-bérlőben. Ehhez a módhoz a rendszergazdának be kell jelentkeznie a hitelesítő adatokkal, mielőtt a rendszergazda bármilyen hívást el tud végezni a Graph API.
* **Automatizált**, folyamatos feladatok esetén a felügyeleti feladatok elvégzéséhez szükséges jogosultságokkal ellátott valamilyen típusú szolgáltatásfiókot kell használnia. Az Azure AD-ben ezt egy alkalmazás regisztrálásával és az Azure AD-be történő hitelesítéssel teheti meg. Ezt egy olyan *alkalmazás-azonosító* használatával végezheti el, amely a [OAuth 2,0 ügyfél hitelesítő adatait](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)használja. Ebben az esetben az alkalmazás önmaga, nem pedig felhasználóként működik a Graph API meghívásához.

Ebből a cikkből megtudhatja, hogyan hajthatja végre az automatikus használati esetet. Olyan .NET 4,5 `B2CGraphClient` hoz létre, amely felhasználói létrehozási, olvasási, frissítési és törlési (szifilisz-) műveleteket hajt végre. Az ügyfél egy Windows parancssori felülettel (CLI) fog rendelkezni, amely lehetővé teszi különböző módszerek meghívását. A kód azonban nem interaktív, automatizált módon viselkedik.

## <a name="prerequisites"></a>Előfeltételek

Alkalmazások vagy felhasználók létrehozása előtt Azure AD B2C bérlőre van szükség. Ha még nem rendelkezik ilyennel, [hozzon létre egy Azure Active Directory B2C bérlőt](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

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

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.
1. A **kezelés**területen válassza a **szerepkörök és rendszergazdák**lehetőséget.
1. Válassza ki a **felhasználói rendszergazda** szerepkört.
1. Válassza a **hozzárendelés hozzáadása**elemet.
1. A **kijelölés** szövegmezőbe írja be a korábban regisztrált alkalmazás nevét, például *managementapp1*. Válassza ki az alkalmazást, amikor megjelenik a keresési eredmények között.
1. Válassza a **Hozzáadás** lehetőséget. Az engedélyek teljes propagálása eltarthat néhány percig.

A Azure AD B2C alkalmazás már rendelkezik a felhasználók törléséhez szükséges további engedélyekkel vagy a B2C-bérlő jelszavának frissítésével.

## <a name="get-the-sample-code"></a>A mintakód letöltése

A kód minta egy olyan .NET-konzol alkalmazás, amely a [Active Directory-hitelesítési tár (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) használja az Azure AD-Graph API való interakcióhoz. A kód azt mutatja be, hogyan hívhatja meg az API-t, hogy programozott módon felügyelje a felhasználókat egy Azure AD B2C bérlőn.

[Letöltheti a minta archívumot](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) vagy klónozott a GitHub-tárházat:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

A mintakód beszerzése után konfigurálja a környezetet, majd hozza létre a projektet:

1. Nyissa meg az `B2CGraphClient\B2CGraphClient.sln` elemet a Visual Studióban.
1. A **B2CGraphClient** projektben nyissa meg az *app. config* fájlt.
1. Cserélje le a `<appSettings>` szakaszt a következő XML-fájlra. Ezután cserélje le a `{your-b2c-tenant}`t a bérlő nevére, és `{Application ID}` és `{Client secret}` a korábban feljegyzett értékekkel.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Hozza létre a megoldást. Kattintson a jobb gombbal a **B2CGraphClient** -megoldásra a megoldáskezelő, majd válassza a **megoldás Újraépítés**elemet.

Ha a Build sikeres, a `B2C.exe` Console alkalmazás megtalálható a `B2CGraphClient\bin\Debug`ban.

## <a name="review-the-sample-code"></a>Tekintse át a mintakódot

A B2CGraphClient használatához nyisson meg egy parancssort (`cmd.exe`), és váltson a projekt `Debug` könyvtárába. Ezután futtassa a `B2C Help` parancsot.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Az `B2C Help` parancs az elérhető alparancsok rövid leírását jeleníti meg. Minden alkalommal, amikor meghívja valamelyik alparancsát, `B2CGraphClient` küld egy kérést az Azure AD-Graph APInak.

Az alábbi fejezetek azt ismertetik, hogy az alkalmazás kódja hogyan hívja meg az Azure AD Graph API.

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése

Az Azure AD Graph APIre irányuló minden kérelemhez hozzáférési jogkivonat szükséges a hitelesítéshez. a `B2CGraphClient` a nyílt forráskódú Active Directory-hitelesítési tár (ADAL) használatával segíti a hozzáférési jogkivonatok beszerzését. A ADAL megkönnyíti a tokenek beszerzését azáltal, hogy egy segítő API-t biztosít, és gondoskodik néhány fontos adatról, például a hozzáférési tokenek gyorsítótárazásáról. A jogkivonatok lekéréséhez azonban nem kell ADAL-t használnia. Ehelyett lekérheti a jogkivonatokat a HTTP-kérések manuális elindításával.

> [!NOTE]
> Az Azure AD Graph API használható hozzáférési jogkivonatok beszerzéséhez a ADAL v2 vagy újabb verzióját kell használnia. A ADAL v1 nem használható.

`B2CGraphClient` végrehajtásakor a létrehozza a `B2CGraphClient` osztály egy példányát. Az osztály konstruktora a ADAL hitelesítési állványzatot állítja be:

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

Példaként használja az `B2C Get-User` parancsot.

Ha a `B2C Get-User` további argumentumok nélkül hívja meg, az alkalmazás meghívja a `B2CGraphClient.GetAllUsers()` metódust. `GetAllUsers()` ezután meghívja a `B2CGraphClient.SendGraphGetRequest()`, amely egy HTTP GET-kérést küld az Azure AD Graph APInak. Mielőtt `B2CGraphClient.SendGraphGetRequest()` elküldi a GET kérelmet, először a ADAL használatával szerzi be a hozzáférési jogkivonatot:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

A ADAL `AuthenticationContext.AcquireToken()` metódus meghívásával lekérheti a Graph API hozzáférési jogkivonatát. A ADAL ezután visszaadja az alkalmazás identitását jelölő `access_token`.

### <a name="read-users"></a>Felhasználók beolvasása

Ha szeretné lekérni a felhasználók listáját, vagy egy adott felhasználót az Azure AD-Graph API, küldhet egy HTTP-`GET` kérelmet a `/users` végpontnak. A bérlő összes felhasználója számára a következőhöz hasonló kérelem fog kinézni:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

A kérelem megtekintéséhez futtassa a következőt:

 ```cmd
 B2C Get-User
 ```

Két fontos szempontot kell figyelembe venni:

* A ADAL használatával beszerzett hozzáférési jogkivonat a `Bearer` séma használatával kerül a `Authorization` fejlécbe.
* A B2C-bérlők esetében `api-version=1.6`lekérdezési paramétert kell használnia.

Mindkét részlet kezelése a `B2CGraphClient.SendGraphGetRequest()` metódusban történik:

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

Amikor felhasználói fiókokat hoz létre a B2C-bérlőben, HTTP-`POST` kérelmet küldhet a `/users` végpontnak. A következő HTTP `POST`-kérelem egy példát mutat be a bérlőben létrehozandó felhasználóra.

A felhasználói felhasználók létrehozásához a következő kérelemben szereplő tulajdonságok többsége szükséges. A `//` megjegyzései szerepelnek az ábrán – nem tartalmazzák azokat a tényleges kérelemben.

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

A `Create-User` parancs bemeneti paraméterként egy JSON-fájlt használ, amely egy felhasználói objektum JSON-ábrázolását tartalmazza. A kódban két minta JSON-fájl található: `usertemplate-email.json` és `usertemplate-username.json`. Ezeket a fájlokat az igényeinek megfelelően módosíthatja. A fenti kötelező mezőkön kívül számos választható mező is szerepel a fájlokban.

A kötelező és a nem kötelező mezőkkel kapcsolatos további információkért lásd: [entitás és összetett típus referenciája | Graph API hivatkozás](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

Láthatja, hogyan épül fel a POST kérelem `B2CGraphClient.SendGraphPostRequest()`:

* Hozzáférési jogkivonatot csatol a kérelem `Authorization` fejlécébe.
* Beállítja `api-version=1.6`.
* Magában foglalja a kérelem törzsében található JSON felhasználói objektumot.

> [!NOTE]
> Ha a meglévő felhasználói tárolóból áttelepíteni kívánt fiókoknál alacsonyabb a jelszó erőssége, mint az [Azure ad B2C által kényszerített erős jelszó](user-flow-password-complexity.md), a `passwordPolicies` tulajdonság `DisableStrongPassword` értékével letilthatja az erős jelszóra vonatkozó követelményt. Például módosíthatja az előző felhasználói kérelmet a következő módon: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok frissítése

Amikor frissíti a felhasználói objektumokat, a folyamat a felhasználói objektumok létrehozásához használthoz hasonló, de a HTTP `PATCH` metódust használja:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Próbálja meg frissíteni a felhasználót úgy, hogy módosítja a JSON-fájlok egyes értékeit, majd a `B2CGraphClient` használatával futtatja a következő parancsok egyikét:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

A kérelem elküldésével kapcsolatos részletekért tekintse meg a `B2CGraphClient.SendGraphPatchRequest()` metódust.

### <a name="search-users"></a>Felhasználók keresése

A B2C-bérlőben lévő felhasználókat a következő módokon keresheti meg:

* Hivatkozzon a felhasználó **objektumazonosítóára**.
* Hivatkozzon a bejelentkezési termékazonosító, a `signInNames` tulajdonságra.
* Hivatkozzon a OData érvényes paraméterek bármelyikére. Például: "givenName", "vezetéknév", "displayName" stb.

Futtassa a következő parancsok egyikét egy felhasználó kereséséhez:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Például:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>Felhasználók törlése

A felhasználók törléséhez használja a HTTP `DELETE` metódust, és hozza létre az URL-címet a felhasználó objektum-azonosítójával:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Ha példát szeretne látni, írja be ezt a parancsot, és tekintse meg a konzolra kinyomtatott törlési kérelmet:

```cmd
B2C Delete-User <object-id-of-user>
```

A kérelem elküldésével kapcsolatos részletekért tekintse meg a `B2CGraphClient.SendGraphDeleteRequest()` metódust.

A felhasználók felügyelete mellett számos más műveletet is végrehajthat az Azure AD Graph API. Az [Azure AD Graph API dokumentációja](/previous-versions/azure/ad/graph/api/api-catalog) részletesen ismerteti az egyes műveleteket, valamint a mintavételi kérelmeket.

## <a name="use-custom-attributes"></a>Egyéni attribútumok használata

A legtöbb fogyasztói alkalmazásnak bizonyos típusú egyéni felhasználói profilt kell tárolnia. Ennek egyik módja, ha egyéni attribútumot határoz meg a B2C-bérlőben. Ezt az attribútumot ezután ugyanúgy kezelheti, mint bármely más tulajdonságot egy felhasználói objektumon. Frissítheti az attribútumot, törölheti az attribútumot, lekérdezheti az attribútumot, megküldheti az attribútumot jogcímként a bejelentkezési jogkivonatokban, és így tovább.

Ha egyéni attribútumot szeretne definiálni a B2C-bérlőben, tekintse meg a [B2C egyéni attribútumok referenciáját](user-flow-custom-attributes.md).

A B2C-bérlőben definiált egyéni attribútumok a következő `B2CGraphClient` parancsok használatával tekinthetők meg:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A kimenet felfedi az egyes egyéni attribútumok részleteit. Például:

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

A teljes nevet, például a `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`t használhatja a felhasználói objektumok tulajdonsága. Frissítse a JSON-fájlt az új tulajdonsággal és egy értékkel a tulajdonsághoz, majd futtassa a következőt:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Következő lépések

`B2CGraphClient`használatával olyan szolgáltatásalkalmazás van, amely programozott módon képes kezelni a B2C-bérlői felhasználókat. `B2CGraphClient` a saját alkalmazás identitását használja az Azure AD-Graph API való hitelesítéshez. Emellett a tokeneket is megvásárolja az ügyfél titkos kódjának használatával.

A funkciónak a saját alkalmazásba való beépítésekor jegyezze fel a B2C-alkalmazások néhány kulcsfontosságú pontját:

* Adja meg az alkalmazás számára a szükséges engedélyeket a bérlőn.
* A Graph API meghívásakor használja a `api-version=1.6`.
* Ha felhasználói felhasználókat hoz létre és frissít, néhány tulajdonságot meg kell adni a fentiekben leírtak szerint.
