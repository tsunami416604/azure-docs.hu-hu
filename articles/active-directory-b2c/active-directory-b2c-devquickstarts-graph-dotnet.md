---
title: A Graph API - használja az Azure AD B2C |} Microsoft Docs
description: Hogyan hívhatja meg a Graph API-val egy B2C-bérlő automatizálja az Alkalmazásidentitás használatával.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/07/2017
ms.author: davidmu
ms.openlocfilehash: 2f95df26abcd2c0d5b62c395f92c359170d6d701
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Az Azure AD B2C: Használja az Azure AD Graph API

>[!NOTE]
> Kell használnia a [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) felhasználóinak az Azure AD B2C-címtár kezeléséhez. Ez eltér a Microsoft Graph API-val. További információkat [itt](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/) talál.

Az Azure Active Directory (Azure AD) B2C-bérlők általában nagyon nagy. Ez azt jelenti, hogy több bérlő felügyeletének általános feladatai van szükség, programozott módon. Egy elsődleges példája felhasználók kezelése. Szükség lehet egy meglévő felhasználó tároló áttelepítése a B2C-bérlő. Érdemes lehet a saját oldalon felhasználói regisztráció és a háttérben Azure AD B2C-címtárban lévő felhasználói fiókokat hozhat létre. Ilyen típusú feladatok elvégzéséhez szükség van a szolgáltatásvezérlési létrehozása, olvasása, frissítése, és törölje a felhasználói fiókokat. Ezeket a feladatokat az Azure AD Graph API használatával teheti meg.

A B2C bérlőre nincsenek két elsődleges módja a Graph API-val folytatott kommunikáció.

* Interaktív, egyszer futó feladatok akkor szerepét a B2C-bérlő a rendszergazdai fiók a feladatok végrehajtásakor. Ebben a módban a rendszergazda számára, hogy a hitelesítő adatokkal jelentkezhetnek be, hogy a rendszergazda a Graph API bármely hívások végrehajtása előtt szükséges.
* Automatikus, folyamatos feladatokat valamilyen adja meg a felügyeleti feladatok végrehajtásához szükséges jogosultságokkal rendelkező szolgáltatásfiókot kell használnia. Az Azure AD meg ehhez az alkalmazásnak, és az Azure AD hitelesíti. Ehhez használja az **Alkalmazásazonosító** használó a [OAuth 2.0 ügyfél hitelesítő adatai megadják](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Ebben az esetben az alkalmazás úgy működik, mint maga nem felhasználóként, a Graph API hívása.

Ebben a cikkben mutatjuk be az automatikus használati eset végrehajtása. Annak bemutatásához, azt fogja összeállítása a .NET 4.5 `B2CGraphClient` , amely elvégzi a felhasználó létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek. Az ügyfél egy Windows parancssori felület (CLI), mely lehetővé teszi a különböző módszerek meghívására lesz. Azonban a kód írása a nem interaktív, automatikus módon viselkedik.

## <a name="get-an-azure-ad-b2c-tenant"></a>Az Azure AD B2C-bérlő beszerzése
Előtt hozzon létre az alkalmazások vagy felhasználók számára, vagy minden kommunikálni az Azure AD, szüksége lesz egy Azure AD B2C-bérlő és a bérlő egy globális rendszergazdai fiókját. Ha már, a bérlő nincs [Ismerkedés az Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Az alkalmazás regisztrálása-bérlőben
Miután a B2C-bérlő elkészült, az alkalmazás használatával regisztrálnia kell a [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> A Graph API használata a B2C bérlőre, szüksége lesz egy dedikált alkalmazás regisztrálása az általános *App regisztrációk* az Azure portál menüjében **nem** az Azure AD B2C  *Alkalmazások* menü. Nem használhat újra a már meglévő B2C alkalmazások az Azure AD B2C regisztrált *alkalmazások* menü.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki az Azure AD B2C-bérlő fiókja kiválasztja az oldal jobb felső sarkában.
3. A bal oldali navigációs ablaktábláján válassza **minden szolgáltatás**, kattintson a **App regisztrációk**, és kattintson a **hozzáadása**.
4. Kövesse az utasításokat az új alkalmazás létrehozásához. 
    1. Válassza ki **Web App / API** az alkalmazás típusa.    
    2. Adjon meg **bármely átirányítási URI** (pl. https://B2CGraphAPI), mert nincs megfelelő ehhez a példához.  
5. Az alkalmazás lesz most megjelenjenek az alkalmazások listájának kattintson rá az beszerzése a **Alkalmazásazonosító** (más néven Ügyfélazonosítót). Másolja, szüksége lehet rájuk egy későbbi szakasz ismerteti.
6. A beállítások menüben kattintson a **kulcsok** , és adja hozzá egy új kulcsot (más néven ügyfélkulcs). Is másolja azt egy későbbi szakasz ismerteti a használatra.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurálása létrehozása, olvasása, és az alkalmazás engedélyeinek frissítése
Most szeretné beolvasni a szükséges engedélyekkel létrehozása, olvasása, frissítése és törlése a felhasználók számára az alkalmazás konfigurálása.

1. Folytatás az Azure-portál alkalmazás regisztrációk menüben, válassza ki az alkalmazást.
2. A beállítások menüben kattintson a **szükséges engedélyek**.
3. A szükséges engedélyek menüben kattintson a **Windows Azure Active Directory**.
4. Hozzáférés engedélyezése menüben válasszon ki a **címtáradatok olvasása és írása** engedélyt **Alkalmazásengedélyek** kattintson **mentése**.
5. Végezetül vissza a szükséges engedélyek menüben kattintson a a **engedélyt adjon** gombra.

Most már rendelkezik egy alkalmazás, amely jogosult létrehozása, olvasása és frissítése a B2C-bérlő felhasználóit.

> [!NOTE]
> Támogatást nyújtó engedélyek biztosítják, hogy teljesen feldolgozni néhány percet igénybe vehet.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Az alkalmazás törlése engedélyek konfigurálása
Jelenleg a *címtáradatok olvasása és írása* engedély does **nem** közé tartozik például a felhasználók törlése a törlések ezt. Ha szeretne adni az alkalmazás felhasználók törlésének lehetőségét, PowerShell érintő további lépések elvégzéséhez szüksége lesz, egyéb esetben ugorjon a következő szakaszban.

Először, ha még nincs telepítve, telepítse a [Azure AD PowerShell v1 modult (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Telepítése után a PowerShell modul csatlakozni az Azure AD B2C-bérlő.

> [!IMPORTANT]
> Kell használnia a B2C bérlői rendszergazdai fiókot, amely **helyi** a B2C bérlő számára. Ezek a fiókok néznek ki: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Most fogjuk használni a **Alkalmazásazonosító** a parancsfájlban az alábbi rendelje hozzá az alkalmazás a felhasználói fiók rendszergazdai szerepkört, amely lehetővé teszi, hogy a felhasználók törlése. Ezeket a szerepköröket rendelkezik a jól ismert azonosítók, így az összes végre kell hajtani rendszer bemeneti a **Alkalmazásazonosító** az alábbi parancsfájl.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Az alkalmazás most is jogosult felhasználók törlése a B2C-bérlő.

## <a name="download-configure-and-build-the-sample-code"></a>Töltse le, konfigurálása, és állítsa be a mintakód
Először letöltötte a mintakódot, és lekérése is fusson. Majd most elindítjuk azt részletes bemutatása.  Is [letöltötte a mintakódot .zip-fájlként](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Akkor is klónozhatja egy olyan könyvtárba, az Ön által választott:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Nyissa meg a `B2CGraphClient\B2CGraphClient.sln` Visual Studio-megoldást a Visual Studióban. Az a `B2CGraphClient` projektre, nyissa meg a fájlt `App.config`. A három alkalmazásbeállítást cserélje le a saját értékeit:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

A jobb gombbal a `B2CGraphClient` megoldás és a minta Újraépítés. Ha sikeres, most rendelkeznie kell egy `B2C.exe` található végrehajtható fájl `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>A Graph API használatával hozhat létre felhasználói CRUD műveleteihez
A B2CGraphClient használatához nyisson meg egy `cmd` Windows parancsot a parancssorba, és módosítsa a könyvtárat a `Debug` könyvtár. Ezután futtassa a `B2C Help` parancsot.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Ez megjeleníti minden egyes parancsnál rövid leírása. Minden alkalommal, amikor aktiválják az alábbi parancsok egyikét `B2CGraphClient` egy kérést küld az Azure AD Graph API-t.

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése
Bármely kérelem a Graph API olyan hozzáférési jogkivonatot igényel a hitelesítéshez. `B2CGraphClient` a hozzáférési jogkivonatok szerezni a nyílt forráskódú Active Directory Authentication Library (ADAL) használja. ADAL egyszerűbbé teszi a token beszerzési biztosító egyszerű API-t, és néhány fontos részleteket, például a gyorsítótár hozzáférési jogkivonatok figyelembe vételével. Adal-t használó lekérni a jogkivonatokat, azonban nincs. Jogkivonatok HTTP-kérelmek létrehozásával is beszerezheti.

> [!NOTE]
> A fenti ADAL v2 ahhoz, hogy kommunikálhasson a Graph API-t használ.  Ahhoz, hogy az Azure AD Graph API-val használható hozzáférési jogkivonatok lekérésére ADAL v2 és v3 kell használnia.
> 
> 

Ha `B2CGraphClient` fut, létrehoz egy példányát a `B2CGraphClient` osztály. Ez az osztály konstruktorában állít be egy ADAL-hitelesítés állványok:

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

Fogjuk használni a `B2C Get-User` példaként parancsot. Ha `B2C Get-User` minden további, a parancssori felület hívások bemeneti adatok nélkül meghívták a `B2CGraphClient.GetAllUsers(...)` metódust. Ez a metódus meghívja `B2CGraphClient.SendGraphGetRequest(...)`, amely elküld egy HTTP GET kérést a Graph API-val. Mielőtt `B2CGraphClient.SendGraphGetRequest(...)` küld a GET kérelmek, először nyer access token ADAL használatával:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Kaphat access token a Graph API hívása az ADAL `AuthenticationContext.AcquireToken(...)` metódust. Adal-t adja vissza egy `access_token` , amely jelzi, hogy az alkalmazás azonosítóját.

### <a name="read-users"></a>Olvassa el a felhasználók
Ha azt szeretné, a felhasználók listáját, vagy kérjen egy adott felhasználó a Graph API-val, HTTP küldhet `GET` elküldeni a kérelmet a `/users` végpont. A bérlő számára az összes kérelem így néz ki:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

A kérelem megtekintéséhez futtassa:

 ```cmd
 B2C Get-User
 ```

Két fontos dolgot figyelembe venni:

* A hozzáférési jogkivonat ADAL keresztül szerzett hozzáadódik a `Authorization` fejléc használatával a `Bearer` séma.
* A B2C bérlőre, kell használnia a következő lekérdezésparaméter `api-version=1.6`.

Ezen adatok mindegyikét kezeli a `B2CGraphClient.SendGraphGetRequest(...)` módszert:

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

### <a name="create-consumer-user-accounts"></a>Felhasználói fiókok létrehozása
A B2C-bérlő felhasználói fiókokat hoz létre, amikor HTTP küldhet `POST` elküldeni a kérelmet a `/users` végpont:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

A legtöbb ezeket a tulajdonságokat a kérésben identitásrendszerében a felhasználók létrehozásához szükségesek. További tudnivalókért kattintson [Itt](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Vegye figyelembe, hogy a `//` illusztrációs megjelent megjegyzések. Ne szerepeljen az azokat a tényleges kérelmet.

A kérelem megtekintéséhez futtassa a következő parancsok egyikét:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

A `Create-User` parancs fogadja bemeneti paraméterként egy .JSON kiterjesztésű fájlt. Ez tartalmazza a user objektum JSON-ábrázolását. Példakód két minta .JSON kiterjesztésű fájl van: `usertemplate-email.json` és `usertemplate-username.json`. Ezeket a fájlokat a saját igényeinek megfelelően módosíthatja. A kötelező mezőket a fenti mellett több választható mezőket, melyekkel ezeket a fájlokat szerepelnek. A választható mezőket a részletek megtalálhatók a [Azure AD Graph API entitáshivatkozás](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Láthatja, hogyan a POST-kérelmet a összeállított `B2CGraphClient.SendGraphPostRequest(...)`.

* A hozzáférési token csatol a `Authorization` a kérelem fejlécében.
* Állítja a `api-version=1.6`.
* Ez magában foglalja a JSON felhasználói objektum, a kérelem törzsében.

> [!NOTE]
> Ha az áttelepítendő ki egy meglévő felhasználói fiókokat alacsonyabb jelszó erőssége, mint a [kényszeríti ki az Azure AD B2C erős jelszó erőssége](https://msdn.microsoft.com/library/azure/jj943764.aspx), letilthatja az erős jelszó követelmény használatával a `DisableStrongPassword` értéket a `passwordPolicies` tulajdonság. Például a következőképpen fent megadott felhasználói kérés módosíthatja: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Felhasználói fiókok frissítése
Ha frissíti a felhasználói objektumok, a folyamat hasonlít a felhasználói objektumok létrehozására használhatja. Ez a folyamat a HTTP Protokollt használ, de `PATCH` módszert:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Próbálja meg frissíteni a felhasználó frissítése a JSON-fájlokat az új adatokat. Ezután `B2CGraphClient` az alábbi parancsok egyikét futtatja:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Vizsgálja meg a `B2CGraphClient.SendGraphPatchRequest(...)` metódus talál részletes információt a kérelem küldése.

### <a name="search-users"></a>Felhasználók keresése
Felhasználók B2C-bérlőben lévő több módon is kereshet. Egy, a felhasználó azonosító vagy a felhasználó bejelentkezési azonosítót használja, a két objektum (azaz a `signInNames` tulajdonság).

Egy adott felhasználó keresése a következő parancsok egyikét futtatja:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Íme néhány példa:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Felhasználók törlése
A felhasználó törlése történik egyszerű. A HTTP protokollal `DELETE` metódus és a szerkezetet az URL-cím a megfelelő objektumazonosító:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Példa megtekintéséhez írja be ezt a parancsot, és tekintse meg a törlési kérelmet, amelyet a program a konzolhoz:

```cmd
B2C Delete-User <object-id-of-user>
```

Vizsgálja meg a `B2CGraphClient.SendGraphDeleteRequest(...)` metódus talál részletes információt a kérelem küldése.

Az Azure AD Graph API felhasználók kezelése mellett sok más műveletet végezheti el. A [Azure AD Graph API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) minden művelet együtt minta kérelmek részleteit.

## <a name="use-custom-attributes"></a>Egyéni attribútumok használata
A legtöbb fogyasztói alkalmazásokat kell valamilyen egyéni felhasználói profil adatait tárolja. Ehhez egyik módja a B2C-bérlő ad meg egy egyedi attribútumot. Ez az attribútum úgy, ahogy bármely más tulajdonság úgy kezelje, a user objektum akkor kezelni. Az attribútum, törli az attribútumot, az attribútum alapján, küldése az attribútum a bejelentkezési jogkivonatokat, és több jogcímként.

A B2C-bérlő egyéni attribútumot megadásához tekintse meg a [B2C egyéni attribútumhivatkozás](active-directory-b2c-reference-custom-attr.md).

Megtekintheti a használatával a B2C-bérlő definiált egyéni attribútumok `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A kimeneti ezeket a funkciókat, mint felfedi összes egyéni attribútumot, részleteit:

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

Használhatja a teljes nevet, például a `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, a felhasználói objektumok tulajdonságainál.  Frissítse a .JSON kiterjesztésű fájlt az új tulajdonságot, és a tulajdonság értékét, és futtassa:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

A `B2CGraphClient`, programozott módon kezelje a B2C bérlő felhasználók szolgáltatás-alkalmazással. `B2CGraphClient` saját alkalmazás identitását használja az Azure AD Graph API felé történő hitelesítésre. Egy ügyfélkulcsot a jogkivonatok is megkapja. Mivel használhatja ezt a funkciót az alkalmazásba, ne felejtse néhány fő szempontot B2C-alkalmazásokhoz:

* Adja meg az alkalmazás a bérlő a megfelelő engedélyeket kell.
* Most kell használnia az adal-t (nem MSAL) jogkivonatot beolvasni. (Ön is is küldhet közvetlen, szalagtár használata nélkül.)
* A Graph API hívásakor használható `api-version=1.6`.
* Amikor létrehozása és frissítése identitásrendszerében a felhasználók, néhány tulajdonságok szükségesek, fent leírt módon.

Ha bármilyen kérdése vagy a műveletek hajtsa végre a B2C-bérlő a Graph API segítségével szeretné, ez a cikk megjegyzést szóljon, vagy problémát fájlt a Githubon code minta tárházban.

