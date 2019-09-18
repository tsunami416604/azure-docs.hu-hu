---
title: A Azure Active Directory B2C Graph API használata | Microsoft Docs
description: A B2C-bérlő Graph APIának meghívása alkalmazás-identitás használatával a folyamat automatizálásához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7fcbbbfcc2192160ca852538c015a365518e448
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065946"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Az Azure AD Graph API használata

>[!NOTE]
> Az [Azure AD Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) használatával felügyelheti a felhasználókat egy Azure ad B2C könyvtárban. Ez eltér a Microsoft Graph API-tól. További információkat [itt](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/) talál.

A Azure Active Directory B2C (Azure AD B2C) bérlők általában nagyon nagy méretűek. Ez azt jelenti, hogy számos gyakori bérlői felügyeleti feladatot programozott módon kell végrehajtani. Elsődleges példa a felhasználók kezelése. Előfordulhat, hogy egy meglévő felhasználói tárolót kell áttelepítenie egy B2C-bérlőre. Előfordulhat, hogy a felhasználói regisztrációt a saját oldalán szeretné tárolni, és felhasználói fiókokat szeretne létrehozni a Azure AD B2C könyvtárban a jelenetek mögött. Az ilyen típusú feladatok lehetővé teszik felhasználói fiókok létrehozását, olvasását, frissítését és törlését. Ezeket a feladatokat az Azure AD Graph API használatával végezheti el.

A B2C-bérlők esetében két elsődleges mód van a Graph API való kommunikációra.

* Interaktív, egyszeri futtatású feladatok esetén a feladatok végrehajtásakor rendszergazdai fiókként kell működnie a B2C-bérlőben. Ehhez a módhoz a rendszergazdának be kell jelentkeznie a hitelesítő adatokkal, mielőtt a rendszergazda bármilyen hívást el tud végezni a Graph API.
* Automatizált, folyamatos feladatok esetén a felügyeleti feladatok elvégzéséhez szükséges jogosultságokkal ellátott valamilyen típusú szolgáltatásfiókot kell használnia. Az Azure AD-ben ezt egy alkalmazás regisztrálásával és az Azure AD-be történő hitelesítéssel teheti meg. Ezt egy olyan **alkalmazás-azonosító** használatával végezheti el, amely a [OAuth 2,0 ügyfél hitelesítő adatait](../active-directory/develop/service-to-service.md)használja. Ebben az esetben az alkalmazás önmaga, nem pedig felhasználóként működik a Graph API meghívásához.

Ebből a cikkből megtudhatja, hogyan hajthatja végre az automatikus használat esetét. Létre fog hozni egy .net 4,5 `B2CGraphClient` -et, amely felhasználói létrehozási, olvasási, frissítési és törlési (szifilisz-) műveleteket hajt végre. Az ügyfél egy Windows parancssori felülettel (CLI) fog rendelkezni, amely lehetővé teszi különböző módszerek meghívását. A kód azonban úgy van írva, hogy nem interaktív, automatizált módon viselkedjen.

## <a name="get-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő beszerzése
Alkalmazások vagy felhasználók létrehozása előtt Azure AD B2C bérlőre van szükség. Ha még nem rendelkezik Bérlővel, [Ismerkedjen meg Azure ad B2Cával](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Az alkalmazás regisztrálása a bérlőben
A B2C-bérlőt követően regisztrálnia kell az alkalmazást a [Azure Portal](https://portal.azure.com)használatával.

> [!IMPORTANT]
> Ha a Graph APIt a B2C-Bérlővel szeretné használni, regisztrálnia kell egy alkalmazást az Azure Portal alkalmazás *regisztrációs* szolgáltatásával, **nem** pedig az Azure ad B2C's- *alkalmazások* menüjében. A következő utasítások a megfelelő menüre mutatnak. Az Azure AD B2C's- *alkalmazások* menüjében regisztrált meglévő B2C-alkalmazások nem használhatók újra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a Azure AD B2C bérlőt a lap jobb felső sarkában található fiók kiválasztásával.
3. A bal oldali navigációs panelen válassza a **minden szolgáltatás**, majd az **alkalmazás-regisztrációk**lehetőséget, és kattintson az **új regisztráció**elemre.
4. Kövesse az utasításokat az új alkalmazás létrehozásához.
    1. Adjon hozzá egy megfelelő nevet
    2. **Csak az ebben a szervezeti könyvtárban lévő fiókok** kijelölése
    3. Az alkalmazás típusaként válassza a **web** lehetőséget, és adja meg a **bejelentkezési URL** - `https://B2CGraphAPI`címet (például), mivel ez nem releváns ehhez a példához.
    4. Kattintson a regisztrálás gombra.
5. Az alkalmazás ekkor megjelenik az alkalmazások listájában, és az **alkalmazás-azonosító** (más néven ügyfél-azonosító) beszerzéséhez kattintson rá. Másolja be, mert szüksége lesz rá egy későbbi szakaszban.
6. A beállítások menüben kattintson a **tanúsítványok & Secrets**elemre.
7. Az **ügyfél titkai** szakaszban kattintson az **új ügyfél titka**elemre, adjon meg egy leírást a titkos kulcshoz, és válasszon egy időtartamot, majd kattintson a **Hozzáadás**gombra. Másolja a titkos kulcs (más néven ügyfél titka) értékét egy későbbi szakaszban való használatra.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Az alkalmazás létrehozására, olvasására és frissítésére vonatkozó engedélyek konfigurálása
Most konfigurálnia kell az alkalmazást, hogy megkapja az összes szükséges engedélyt a felhasználók létrehozásához, olvasásához, frissítéséhez és törléséhez.

1. Folytassa a Azure Portal alkalmazás regisztrációi menüjében, majd válassza ki az alkalmazást.
2. A beállítások menüben kattintson a **szükséges engedélyek**elemre.
3. A szükséges engedélyek menüben kattintson a **Windows Azure Active Directory**elemre.
4. A hozzáférés engedélyezése menüben jelölje be a **címtárbeli adatok olvasása és írása** engedély az **alkalmazás engedélyei** közül, majd kattintson a **Mentés**gombra.
5. Végül vissza a szükséges engedélyek menüben kattintson az **engedélyek megadása** gombra.

Most már rendelkezik egy olyan alkalmazással, amely jogosult a B2C-bérlő felhasználóinak létrehozására, olvasására és frissítésére.

> [!NOTE]
> Az engedélyek megadása több percet is igénybe vehet.
>
>

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Az alkalmazáshoz tartozó jelszó törlésének vagy frissítésének konfigurálása
Jelenleg az *olvasási és írási könyvtári* engedély nem tartalmazza a felhasználók törlésének vagy a felhasználói jelszavak frissítésének lehetőségét. Ha azt szeretné, hogy az alkalmazás lehetővé tegye a felhasználók törlését vagy a jelszavak frissítését, akkor a PowerShellt érintő további lépéseket is el kell végeznie, a következő szakaszra ugorhat.

Először is, ha még nincs telepítve, telepítse az [Azure ad PowerShell v1 modult (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Miután telepítette a PowerShell-modult, kapcsolódjon a Azure AD B2C bérlőhöz.

> [!IMPORTANT]
> Egy B2C bérlői rendszergazdai fiókot kell használnia, amely **helyi** a B2C-bérlőhöz. Ezek a fiókok így néz ki myusername@myb2ctenant.onmicrosoft.com:.

```powershell
Connect-MsolService
```

Most az **alkalmazás azonosítóját** fogjuk használni az alábbi parancsfájlban az alkalmazás a felhasználói fiók rendszergazdai szerepkörének hozzárendeléséhez. Ezek a szerepkörök jól ismert azonosítókkal rendelkeznek, ezért mindössze annyit kell tennie, hogy az alábbi szkriptben adja meg az **alkalmazás azonosítóját** .

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Az alkalmazás mostantól jogosult a felhasználók törlésére vagy a B2C-bérlő jelszavának frissítésére is.

## <a name="download-configure-and-build-the-sample-code"></a>A mintakód letöltése, konfigurálása és összeállítása
Először töltse le a mintát, és futtassa az alkalmazást. Ezt követően részletesebben is szemügyre vesszük.  [A mintakód. zip-fájlként tölthető le](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Azt is megteheti, hogy egy tetszőleges könyvtárba klónozott:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Nyissa `B2CGraphClient\B2CGraphClient.sln` meg a Visual Studio-megoldást a Visual Studióban. A projektben nyissa meg a `App.config`fájlt. `B2CGraphClient` Cserélje le a három alkalmazás beállításait a saját értékeire:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ezután kattintson a `B2CGraphClient` jobb gombbal a megoldásra, és hozza létre újra a mintát. Ha a művelet sikeres, a alkalmazásban `B2C.exe` `B2CGraphClient\bin\Debug`található végrehajtható fájlnak kell lennie.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Felhasználói szifilisz-műveletek készítése a Graph API használatával
A B2CGraphClient használatához nyisson meg egy `cmd` Windows-parancssort, és módosítsa a könyvtárat a `Debug` könyvtárba. Ezután futtassa a `B2C Help` parancsot.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Ez az egyes parancsok rövid leírását jeleníti meg. Minden alkalommal, amikor meghívja az egyik parancsot `B2CGraphClient` , egy kérést küld az Azure ad Graph API.

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése
A Graph APIre irányuló minden kérelemhez hozzáférési jogkivonat szükséges a hitelesítéshez. `B2CGraphClient`a nyílt forráskódú Active Directory-hitelesítési tár (ADAL) használata a hozzáférési tokenek beszerzéséhez. A ADAL megkönnyíti a tokenek beszerzését azáltal, hogy egyszerű API-t biztosít, és gondoskodik néhány fontos adatról, például a hozzáférési tokenek gyorsítótárazásáról. A jogkivonatok lekéréséhez nem kell ADAL-t használnia. A jogkivonatokat a HTTP-kérések készítésével is lekérheti.

> [!NOTE]
> Ez a mintakód a ADAL v2 protokollt használja a Graph API való kommunikációhoz.  Az Azure AD Graph API használható hozzáférési jogkivonatok beszerzéséhez a ADAL v2 vagy a v3 verziót kell használnia.
>
>

A futtatáskor létrehozza a `B2CGraphClient` osztály egy példányát. `B2CGraphClient` Az osztály konstruktora egy ADAL hitelesítési állványzatot állít be:

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

A `B2C Get-User` parancsot példaként fogjuk használni. Ha `B2C Get-User` a meghívása további bemenetek nélkül megtörténik, a `B2CGraphClient.GetAllUsers(...)` CLI meghívja a metódust. Ez a metódus `B2CGraphClient.SendGraphGetRequest(...)`meghívja a-t, amely egy HTTP Get kérelmet küld a Graph APInak. A `B2CGraphClient.SendGraphGetRequest(...)` Get kérelem elküldése előtt először egy hozzáférési jogkivonatot kap a ADAL használatával:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

A ADAL `AuthenticationContext.AcquireToken(...)` metódus meghívásával lekérheti a Graph API hozzáférési jogkivonatát. A ADAL ezután visszaadja az alkalmazás identitását jelképező értéket `access_token` .

### <a name="read-users"></a>Felhasználók beolvasása
Ha szeretné lekérni a felhasználók listáját, vagy egy adott felhasználót szeretne beolvasni a Graph APIból, http `GET` -kérést küldhet a `/users` végpontnak. A bérlő összes felhasználója számára a következőhöz hasonló kérelem fog kinézni:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

A kérelem megtekintéséhez futtassa a következőt:

 ```cmd
 B2C Get-User
 ```

Két fontos szempontot kell figyelembe venni:

* A ADAL-n keresztül beszerzett hozzáférési jogkivonat a `Authorization` `Bearer` séma használatával kerül a fejlécbe.
* B2C-bérlők esetén a lekérdezési paramétert `api-version=1.6`kell használnia.

Mindkét részletet a `B2CGraphClient.SendGraphGetRequest(...)` metódus kezeli:

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
Amikor felhasználói fiókokat hoz létre a B2C-bérlőben, http `POST` -kérést küldhet a `/users` végpontnak:

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

A kérelemben szereplő tulajdonságok többsége a felhasználói felhasználók létrehozásához szükséges. További információért kattintson [ide](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Vegye figyelembe, `//` hogy a megjegyzések szerepelnek az ábrán. Ne vegye fel őket a tényleges kérelembe.

A kérelem megtekintéséhez futtassa a következő parancsok egyikét:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

A `Create-User` parancs egy. JSON fájlt használ bemeneti paraméterként. Ez egy felhasználói objektum JSON-ábrázolását tartalmazza. A mintakód két minta. JSON fájlból áll: `usertemplate-email.json` és. `usertemplate-username.json` Ezeket a fájlokat az igényeinek megfelelően módosíthatja. A fenti kötelező mezőkön kívül számos választható mezőt is használhat a fájlokban. A nem kötelező mezők részleteit az [Azure AD Graph API entitás hivatkozásában](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity)találja.

Láthatja, hogyan épül fel a POST- `B2CGraphClient.SendGraphPostRequest(...)`kérelem.

* Hozzáférési jogkivonatot `Authorization` csatol a kérelem fejlécébe.
* Beállítja `api-version=1.6`.
* Magában foglalja a kérelem törzsében található JSON felhasználói objektumot.

> [!NOTE]
> Ha a meglévő felhasználói tárolóból áttelepíteni kívánt fiókoknál alacsonyabb a jelszó erőssége, mint az [Azure ad B2C által kényszerített erős jelszó erőssége](/previous-versions/azure/jj943764(v=azure.100)), akkor letilthatja az erős jelszó megkövetelését `DisableStrongPassword` a következő értékkel: `passwordPolicies`tulajdonság. A fentiekben megadott felhasználói kérelem például a következő módon módosítható: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
>
>

### <a name="update-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok frissítése
Amikor frissíti a felhasználói objektumokat, a folyamat hasonló a felhasználói objektumok létrehozásához használthoz. Ez a folyamat azonban a http `PATCH` -metódust használja:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Próbálja meg frissíteni a felhasználót a JSON-fájlok új adattal való frissítésével. Ezután a következő parancsok `B2CGraphClient` egyikének futtatásához használhatja a parancsot:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

A kérelem `B2CGraphClient.SendGraphPatchRequest(...)` elküldésével kapcsolatos részletekért tekintse meg a metódust.

### <a name="search-users"></a>Felhasználók keresése
A B2C-bérlőben lévő felhasználókat több módon is megkeresheti. Az egyik a felhasználó objektumazonosító vagy kettő használatával a felhasználó bejelentkezési termékazonosító (azaz a `signInNames` tulajdonsággal).

Egy adott felhasználó kereséséhez futtassa a következő parancsok egyikét:

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
A felhasználó törlésének folyamata egyszerű. Használja a http `DELETE` -metódust, és hozza létre az URL-címet a megfelelő azonosítójú objektummal:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Ha példát szeretne látni, írja be ezt a parancsot, és tekintse meg a konzolra kinyomtatott törlési kérelmet:

```cmd
B2C Delete-User <object-id-of-user>
```

A kérelem `B2CGraphClient.SendGraphDeleteRequest(...)` elküldésével kapcsolatos részletekért tekintse meg a metódust.

A felhasználók felügyelete mellett számos más műveletet is végrehajthat az Azure AD Graph API. Az [Azure AD Graph API dokumentációja](/previous-versions/azure/ad/graph/api/api-catalog) részletesen ismerteti az egyes műveleteket, valamint a mintavételi kérelmeket.

## <a name="use-custom-attributes"></a>Egyéni attribútumok használata
A legtöbb fogyasztói alkalmazásnak bizonyos típusú egyéni felhasználói profilt kell tárolnia. Ennek egyik módja, ha egyéni attribútumot határoz meg a B2C-bérlőben. Ezt az attribútumot ezután ugyanúgy kezelheti, mint bármely más tulajdonságot egy felhasználói objektumon. Frissítheti az attribútumot, törölheti az attribútumot, lekérdezheti az attribútumot, megküldheti az attribútumot jogcímként a bejelentkezési jogkivonatokban, és így tovább.

Ha egyéni attribútumot szeretne definiálni a B2C-bérlőben, tekintse meg a [B2C egyéni attribútumok referenciáját](active-directory-b2c-reference-custom-attr.md).

A B2C-bérlőben definiált egyéni attribútumok a használatával `B2CGraphClient`tekinthetők meg:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

A függvények kimenete felfedi az egyes egyéni attribútumok részleteit, például a következőket:

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

Használhatja a teljes nevet, például `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`a (z) tulajdonságot a felhasználói objektumokban.  Frissítse a. JSON-fájlt az új tulajdonsággal és a tulajdonság értékével, majd futtassa a következő parancsot:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

A használatával `B2CGraphClient`olyan szolgáltatásalkalmazás is rendelkezésre áll, amely programozott módon felügyelheti a B2C-bérlői felhasználókat. `B2CGraphClient`a saját alkalmazás-identitását használja az Azure AD-Graph API való hitelesítéshez. Emellett a tokeneket is megvásárolja az ügyfél titkos kódjának használatával. Ha ezt a funkciót beépíti az alkalmazásba, jegyezze fel a B2C-alkalmazások néhány kulcsfontosságú pontját:

* A megfelelő engedélyeket kell adnia az alkalmazásnak a bérlőben.
* Jelenleg a ADAL (nem MSAL) kell használnia a hozzáférési tokenek lekéréséhez. (A protokollon keresztüli üzenetküldést közvetlenül is elküldheti függvénytár használata nélkül.)
* A Graph API meghívásakor használja `api-version=1.6`a t.
* Ha felhasználói felhasználókat hoz létre és frissít, néhány tulajdonságot meg kell adni a fentiekben leírtak szerint.

Ha bármilyen kérdése vagy kérése van a B2C-bérlő Graph API használatával végrehajtandó műveletekkel kapcsolatban, hagyjon megjegyzést a cikkhez, vagy adjon meg egy problémát a GitHub-kód minta adattárában.

