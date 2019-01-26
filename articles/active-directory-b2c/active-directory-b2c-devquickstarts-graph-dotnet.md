---
title: A Graph API az Azure Active Directory B2C használata |} A Microsoft Docs
description: Útmutató a Graph API meghívása egy B2C-bérlő Alkalmazásidentitás használatával automatizálható a folyamat.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a9a85b02fe3dceb5edf0e09299c772154042f229
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079647"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Az Azure AD Graph API használata

>[!NOTE]
> Kell használnia a [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) az Azure AD B2C-címtár felhasználóinak kezelését. Ez eltér a Microsoft Graph API-ból. További információkat [itt](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/) talál.

Az Azure Active Directory (Azure AD) B2C-bérlők általában nagyon nagy. Ez azt jelenti, hogy számos gyakori bérlő felügyeleti feladatokat kell végrehajtani ahhoz, programozott módon. Egy elsődleges például, felhasználók kezelése. Szüksége lehet egy meglévő felhasználó-tároló áttelepítése B2C-bérlőre. Érdemes a saját oldalon a felhasználói regisztráció üzemeltethet, és a háttérben az Azure AD B2C-címtárban lévő felhasználói fiókokat hozhat létre. Ilyen típusú feladatok van szükség, hogy a létrehozás, Olvasás, frissítés, és törölje a felhasználói fiókokat. Ezeket a feladatokat az Azure AD Graph API használatával teheti meg.

B2C-bérlők a Graph API-val való kommunikáció során két elsődleges módban van.

* Interaktív, egyszeri futtatású feladatok esetén meg kell szerepét egy rendszergazdai fiókot a B2C-bérlőben a feladatok végrehajtásakor. Ebben a módban előírja a rendszergazda számára, hogy a rendszergazda bármilyen a Graph API-hívások végrehajtása előtt jelentkezzen be hitelesítő adataival.
* Automatikus, a folyamatos feladatokat valamilyen típusú adja meg a felügyeleti feladatok végrehajtásához szükséges jogosultságokkal rendelkező szolgáltatásfiókot kell használnia. Az Azure ad-ben akkor ehhez az alkalmazások regisztrálásának és az Azure AD-hitelesítés. Ez történik, az egy **Alkalmazásazonosító** , amely használja a [OAuth 2.0-ügyfél hitelesítő adatainak megadása](../active-directory/develop/service-to-service.md). Ebben az esetben az alkalmazás funkcionál, magát, nem pedig a felhasználó a Graph API hívása.

Ebben a cikkben megismerheti, hogyan hajthat végre automatikus használati eset. A .NET 4.5-ös épít `B2CGraphClient` , amely végrehajtja a felhasználó létrehozása, olvasása, frissítése és törlési (CRUD) műveleteket. Az ügyfél egy Windows-parancssori felület (CLI), amely lehetővé teszi, hogy különböző metódusokat hívhat meg lesz. Azonban a kód írása a nem interaktív, automatizált módon viselkednek.

## <a name="get-an-azure-ad-b2c-tenant"></a>Azure AD B2C-bérlő beszerzése
Alkalmazások vagy felhasználók hozhat létre, meg kell egy Azure AD B2C-bérlőben. Ha már nincs egy bérlő [első lépései az Azure AD B2C-vel](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>A bérlő az alkalmazás regisztrálása
Miután egy B2C-bérlő, az alkalmazást a regisztrálnia kell a [az Azure portal](https://portal.azure.com).

> [!IMPORTANT]
> A Graph API-val való használatához a B2C-bérlőt egy alkalmazást a regisztrálnia kell a *Alkalmazásregisztrációk* szolgáltatást az Azure Portalon **nem** Azure AD B2C *alkalmazások*menüben. Az alábbi utasítások alapján a megfelelő menü vezethet. Nem használhatja újra a meglévő B2C-alkalmazásokat az Azure AD B2C-ben regisztrált *alkalmazások* menü.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a fiókot az oldal jobb felső sarokban, válassza ki az Azure AD B2C-bérlő.
3. A bal oldali navigációs ablaktáblán válassza ki a **minden szolgáltatás**, kattintson a **Alkalmazásregisztrációk**, és kattintson a **hozzáadása**.
4. Kövesse az utasításokat az új alkalmazás létrehozásához. 
    1. Válassza ki **webalkalmazás / API** az alkalmazás típusaként.    
    2. Adja meg **bármely bejelentkezési URL-** (pl. https://B2CGraphAPI) , mert nem releváns ebben a példában.  
5. Az alkalmazás fogja jelennek meg az alkalmazások listájában kattintson rá beszerzése a **Alkalmazásazonosító** (más néven Ügyfélazonosítót). Másolja, egy későbbi részében szüksége lesz rá.
6. A beállítások menüben kattintson a **kulcsok**.
7. Az a **jelszavak** szakaszban adja meg a kulcs leírása, és válassza ki az időtartamot, és kattintson **mentése**. Másolja a kulcs értékét (más néven az ügyfél titkos kódot) használni egy későbbi szakaszban olvashat.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Konfigurálása létrehozását, olvasását, és az alkalmazás engedélyeinek frissítése
Most, konfigurálnia kell az alkalmazás létrehozása, olvasása, frissítése és törlése a felhasználók az összes szükséges engedélyeket.

1. Folytatás az Azure Portalon Alkalmazásregisztrációk menü, válassza ki az alkalmazását.
2. A beállítások menüben kattintson a **szükséges engedélyek**.
3. A szükséges engedélyek menüben kattintson a **Windows Azure Active Directory**.
4. Hozzáférés engedélyezése menüben válassza a **címtáradatok olvasása és írása** engedélyt **Alkalmazásengedélyek** kattintson **mentése**.
5. Végül a szükséges engedélyek menüben kattintson a a **engedélyek megadása** gombra.

Most már olyan alkalmazás, amely jogosult arra, hogy létrehozása, olvasása és frissítése a B2C-bérlő felhasználóit.

> [!NOTE]
> Próbáltak engedélyek biztosítják, hogy teljesen feldolgozni pár percet is igénybe vehet.
> 
> 

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Törlés konfigurálása vagy jelszó engedélyek az alkalmazás frissítése
Jelenleg a *címtáradatok olvasása és írása* engedély does **nem** ízelítő a felhasználó törölheti vagy frissítheti a felhasználói jelszavakat. Ha szeretné lehetővé teszik az alkalmazás törlése felhasználók vagy a jelszavak a frissítés, elvégezheti ezeket a PowerShell érintő további lépéseket kell, ellenkező esetben továbbléphet a következő szakaszra.

Először, ha már nincs telepítve, telepítse a [Azure AD PowerShell v1-modul (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Miután telepítette a PowerShell-modult az Azure AD B2C-bérlő csatlakozni.

> [!IMPORTANT]
> Kell használnia a B2C bérlő rendszergazdai fiók, amely **helyi** a B2C-bérlőre. Ezek a fiókok néznek ki: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Most használjuk a **Alkalmazásazonosító** az alkalmazás a felhasználói fiók rendszergazdai szerepkör hozzárendelése az alábbi szkriptben. Ezek a szerepkörök rendelkezik a jól ismert azonosítók, így az összes kell tennie a bemeneti a **Alkalmazásazonosító** az alábbi parancsprogram.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Az alkalmazás most is a felhasználó törölheti vagy frissítheti a B2C-bérlő a jelszavak engedélyekkel rendelkezik.

## <a name="download-configure-and-build-the-sample-code"></a>Töltse le, konfigurálja és hozza létre a mintakódot
Először töltse le a mintakódot, és lekérése is működjön. Ezután azt vesz igénybe, közelebbről.  Is [letöltötte a mintakódot a .zip-fájlként](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Akkor is klónozhatja tetszőleges könyvtárba:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Nyissa meg a `B2CGraphClient\B2CGraphClient.sln` Visual Studio-megoldást a Visual Studióban. Az a `B2CGraphClient` projekt, nyissa meg a fájlt `App.config`. Cserélje le a saját értékeit az három beállításait:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ezután kattintson a jobb gombbal a `B2CGraphClient` megoldás, és készítse el a mintát. Ha sikeres, most már egy `B2C.exe` található végrehajtható fájl `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Felhasználói CRUD-műveletek létrehozása a Graph API-val
Nyissa meg a B2CGraphClient használatához egy `cmd` Windows parancsot a parancssorba, és váltson át a `Debug` könyvtár. Ezután futtassa a `B2C Help` parancsot.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Ez megjeleníti minden egyes parancsot rövid leírását. Minden alkalommal, amikor hívhat meg egyet az alábbi parancsok `B2CGraphClient` kérést küld az Azure AD Graph API-t.

### <a name="get-an-access-token"></a>Hozzáférési jogkivonat lekérése
Bármilyen kérelmet a Graph API egy hozzáférési jogkivonatot a hitelesítéshez szükséges. `B2CGraphClient` a nyílt forráskódú Active Directory Authentication Library (ADAL) használ a hozzáférési tokenek beszerzése érdekében. Adal-t egyszerűbbé teszi a token beszerzése által egy egyszerű API-t biztosít, és néhány fontos részleteket, például a hozzáférési jogkivonatok gyorsítótárazása figyelembe vételével. Nem kell tokenekhez, azonban az ADAL használatával. Jogkivonatok megkaphassa elvégezte a HTTP-kérelmekre.

> [!NOTE]
> Ez a kódminta ADAL v2 annak érdekében, hogy a Graph API-val folytatott kommunikációhoz használ.  Hozzáférési jogkivonatok, amely használható az Azure AD Graph API eléréséhez ADAL v2 és v3 kell használnia.
> 
> 

Amikor `B2CGraphClient` fut, létrehoz egy példányát a `B2CGraphClient` osztály. Ez az osztály konstruktorában beállítja az ADAL-hitelesítéshez a keret létrehozásához:

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

Használjuk a `B2C Get-User` példaként parancsot. Amikor `B2C Get-User` meghívása nélkül további bemenetet, a parancssori felület meghívja a `B2CGraphClient.GetAllUsers(...)` metódus. Ez a metódus meghívja `B2CGraphClient.SendGraphGetRequest(...)`, amely elküld egy HTTP GET kérelem a Graph API-hoz. Mielőtt `B2CGraphClient.SendGraphGetRequest(...)` küld a GET kérés, először kap egy hozzáférési jogkivonat ADAL használatával:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Akkor is szükséges hozzáférési jogkivonat beszerzése a Graph API-hoz az ADAL meghívásával `AuthenticationContext.AcquireToken(...)` metódust. Adal-t adja vissza egy `access_token` , amely az alkalmazás azonosítóját jelöli.

### <a name="read-users"></a>Olvassa el a felhasználók
Ha azt szeretné, a felhasználók listája vagy az egy adott felhasználó beolvasása a Graph API-ból, küldhet egy olyan HTTP `GET` kérelmet a `/users` végpont. A felhasználók a bérlő összes kérelem a következőhöz hasonló:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

A kérelem megtekintéséhez futtassa:

 ```cmd
 B2C Get-User
 ```

Két dolgot fontos megjegyezni:

* A hozzáférési jogkivonattal beszerzett adal adnak hozzá a `Authorization` fejléc használatával a `Bearer` sémát.
* A B2C-bérlők, a lekérdezési paramétert kell használnia `api-version=1.6`.

Ezeket az adatokat mindkét kezeli a `B2CGraphClient.SendGraphGetRequest(...)` módszer:

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
A B2C-bérlő felhasználói fiókokat hozhat létre, amikor egy olyan HTTP elküldheti `POST` kérelmet a `/users` végpont:

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

Ezek a tulajdonságok a kéréshez a legtöbb identitásrendszerében a felhasználók létrehozásához szükségesek. További tudnivalókért kattintson [Itt](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Vegye figyelembe, hogy a `//` megjegyzések megjelent az ábrán látható. Ne foglalja bele őket egy tényleges kérést.

A kérelem megtekintéséhez futtassa a következő parancsok egyikét:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

A `Create-User` parancs fogadja bemeneti paraméterként egy .JSON kiterjesztésű fájlt. Ez tartalmazza a felhasználói objektum JSON-ábrázolását. Két minta .JSON kiterjesztésű fájlok vannak a mintakód: `usertemplate-email.json` és `usertemplate-username.json`. Ezeket a fájlokat, igény szerint módosíthatja. Szükséges a fenti mezőkön kívül több választható mező, amelyet használhat szerepelnek ezek a fájlok. Az opcionális mezők értékét a részletek megtalálhatók a [Azure AD Graph API entitáshivatkozás](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Láthatja, hogyan jön létre a POST-kérés a `B2CGraphClient.SendGraphPostRequest(...)`.

* A hozzáférési jogkivonat csatolja a `Authorization` a kérelem fejlécében.
* Beállít `api-version=1.6`.
* A JSON-user objektum tartalmazza a kérés törzsében.

> [!NOTE]
> Ha a fiókok, amelyeket szeretné áttelepíteni egy meglévő felhasználó-áruházból, mint az alacsonyabb jelszó erőssége a [kényszeríti ki az Azure AD B2C-vel erős jelszó erőssége](https://msdn.microsoft.com/library/azure/jj943764.aspx), letilthatja az erős jelszót a követelmény használatával a `DisableStrongPassword` az érték a `passwordPolicies` tulajdonság. Például módosíthatja a következőképpen fent megadott felhasználói kérés: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok frissítése
Amikor frissíti a felhasználói objektumok, a folyamat hasonlít a felhasználói objektumok létrehozásához használt. Ez a folyamat a HTTP Protokollt használ, de `PATCH` módszer:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Próbálja meg frissíteni egy felhasználó által a JSON-fájlok frissítése az új adatokkal. Ezután `B2CGraphClient` az alábbi parancsok egyikét futtatja:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Vizsgálja meg a `B2CGraphClient.SendGraphPatchRequest(...)` módszer részletes tájékoztatás a kérelem elküldéséhez.

### <a name="search-users"></a>Felhasználók keresése
A B2C-bérlőben lévő többféleképpen kereshet a felhasználók számára. Egy, a felhasználói objektum azonosítója vagy két használatával a felhasználó bejelentkezési azonosítóval (azaz a `signInNames` tulajdonság).

Keresés egy adott felhasználó a következő parancsok egyikét futtatja:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Az alábbiakban néhány példa:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Felhasználók törlése
A felhasználó törléséhez folyamat nagyon egyszerű. A HTTP protokollal `DELETE` metódust, és az URL-CÍMÉT a megfelelő szerkezet objektumazonosító:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Látható egy példa, adja meg ezt a parancsot, és tekintse meg a törlési kérelmet, amelyet a program a konzolhoz:

```cmd
B2C Delete-User <object-id-of-user>
```

Vizsgálja meg a `B2CGraphClient.SendGraphDeleteRequest(...)` módszer részletes tájékoztatás a kérelem elküldéséhez.

Felhasználók kezelése mellett az Azure AD Graph API-val számos más művelet elvégzése. A [Azure AD Graph API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) részletesen minden művelet, mintául szolgáló kérelmek együtt.

## <a name="use-custom-attributes"></a>Egyéni attribútumok használata
A legtöbb otthoni használatra szánt alkalmazásai kell valamilyen típusú egyéni felhasználói profil adatait tárolja. Ezt megteheti egy módja határozza meg az egyéni attribútum a B2C-bérlőben. Ezt az attribútumot, minden más tulajdonság kezelnie a user objektum ugyanúgy majd kezelnie. Az attribútum módosítására, az attribútum törlése, attribútum lekérdezése, küldjön az attribútum a bejelentkezési tokeneket, és több jogcímként.

Vlastní atribut meghatározásához a B2C-bérlőben, tekintse meg a [B2C egyéni attribútumhivatkozás](active-directory-b2c-reference-custom-attr.md).

Megtekintheti a használatával a B2C-bérlőben definiált egyéni attribútumok `B2CGraphClient`:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Ezek a függvények kimenete felfedi például minden egyes egyéni attribútum részleteit:

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

Használhatja a teljes nevet, például `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, a felhasználói objektumok tulajdonságot.  Frissítse a .JSON kiterjesztésű fájlt az új tulajdonság és a egy tulajdonság értéke, és futtassa:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

Használatával `B2CGraphClient`, egy szolgáltatásalkalmazást, amely programozott módon kezelheti a B2C-bérlő felhasználóinak rendelkezik. `B2CGraphClient` használja a saját Alkalmazásidentitás hitelesítéséhez az Azure AD Graph API-hoz. Ügyfél titkos kulcs használatával tokeneket is szerez. Beépíti a ezt a funkciót az alkalmazásba, ne felejtse el a B2C-alkalmazások néhány fő szempontot:

* Adja meg az alkalmazás a bérlő a megfelelő engedélyeket kell.
* Most szeretné használni az adal-t (nem az MSAL) hozzáférési. (Akkor is is küldhet közvetlen, kódtár használata nélkül.)
* Ha a Graph API meghívása, `api-version=1.6`.
* Létrehozásakor és fogyasztói felhasználók frissítésére, néhány tulajdonságok szükségesek, a fent leírt módon.

Esetleges kérdéseivel és a kérések műveletekhez, amelyet szeretne végrehajtani a Graph API-val a B2C-bérlő van, ha a Megjegyzés meghagyása ebben a cikkben, vagy egy problémát a GitHub code mintaadattár fájlt.

