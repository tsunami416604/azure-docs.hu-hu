---
title: Azure Functions-kötések Microsoft Graph
description: Megtudhatja, hogyan használhatók Microsoft Graph eseményindítók és kötések a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 9dd4067d066362f5842b504971afbc59fd0717a3
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212206"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Azure Functions-kötések Microsoft Graph

Ez a cikk azt ismerteti, hogyan lehet konfigurálni és dolgozni Microsoft Graph eseményindítókat és kötéseket a Azure Functionsban. Ezekkel a Azure Functions használatával dolgozhat a [Microsoft Graph](https://developer.microsoft.com/graph)adatokkal, elemzésekkel és eseményekkel.

A Microsoft Graph bővítmény a következő kötéseket biztosítja:
- Az [Auth token bemeneti kötése](#token-input) lehetővé teszi, hogy bármilyen Microsoft Graph API-val kommunikáljon.
- Az [Excel-táblázat bemeneti kötése](#excel-input) lehetővé teszi az adatok Excelből való beolvasását.
- Az Excel- [táblázat kimeneti kötése](#excel-output) lehetővé teszi az Excel-adatokat módosítását.
- A [OneDrive fájl bemeneti kötése](#onedrive-input) lehetővé teszi fájlok olvasását a OneDrive.
- A [OneDrive fájl kimeneti kötése](#onedrive-output) lehetővé teszi a OneDrive-fájlokba való írást.
- Az [Outlook-üzenet kimeneti kötése](#outlook-output) lehetővé teszi e-mailek küldését az Outlookon keresztül.
- [Microsoft Graph webhook-eseményindítók és-kötések](#webhooks) gyűjteménye lehetővé teszi, hogy reagáljon a Microsoft Graph eseményeire.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph kötések jelenleg előzetes verzióban érhetők el Azure Functions 2. x és újabb verziók esetében. Nem támogatottak a functions 1. x verziójában.

## <a name="packages"></a>Csomagok

Az Auth token bemeneti kötése a [Microsoft. Azure. webjobs. Extensions. AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet csomagban található. A többi Microsoft Graph kötést a [Microsoft. Azure. webjobs. Extensions. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) csomagban biztosítjuk. A csomagok forráskódja az [Azure-functions-microsoftgraph-Extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>A bővítmények beállítása

Microsoft Graph kötések a _kötési bővítmények_segítségével érhetők el. A kötési bővítmények választható összetevők a Azure Functions futtatókörnyezethez. Ez a szakasz bemutatja, hogyan állíthatja be a Microsoft Graph és az Auth token bővítményeket.

### <a name="enabling-functions-20"></a>A függvények engedélyezése 2,0

A kötési bővítmények csak a 2,0-es Azure Functions számára érhetők el. 

További információ arról, hogyan állíthatja be a functions alkalmazást a functions Runtime 2,0-es verziójának használatára: [Azure functions futtatókörnyezet verzióinak megcélzása](set-runtime-version.md).

### <a name="installing-the-extension"></a>A bővítmény telepítése

Egy bővítménynek a Azure Portalból való telepítéséhez navigáljon egy olyan sablonhoz vagy kötéshez, amely hivatkozik rá. Hozzon létre egy új függvényt, és a sablon kiválasztása képernyőn válassza a "Microsoft Graph" forgatókönyvet. Válasszon ki egy sablont ebből a forgatókönyvből. Másik lehetőségként megnyithatja egy meglévő függvény "integrálás" lapját, és kiválaszthatja a cikkben ismertetett kötések egyikét.

Mindkét esetben egy figyelmeztetés jelenik meg, amely megadja a telepítendő bővítményt. A bővítmény beszerzéséhez kattintson a **telepítés** gombra. Az egyes bővítményeket csak egyszer kell telepíteni egy Function App-alkalmazásban. 

> [!Note] 
> A portálon belüli telepítési folyamat akár 10 percet is igénybe vehet a használati tervben.

Ha a Visual studiót használja, a bővítményeket [a cikkben korábban felsorolt NuGet-csomagok](#packages)telepítésével érheti el.

### <a name="configuring-authentication--authorization"></a>Hitelesítés/engedélyezés konfigurálása

Az ebben a cikkben ismertetett kötések használatához identitást kell használni. Ez lehetővé teszi, hogy a Microsoft Graph érvényesítse az engedélyeket és naplózza az interakciókat. Az identitás lehet az alkalmazáshoz vagy az alkalmazáshoz hozzáférő felhasználó is. Az identitás konfigurálásához állítsa be [app Service hitelesítés/engedélyezés](../app-service/overview-authentication-authorization.md) lehetőséget Azure Active Directory. A függvények által igényelt erőforrás-engedélyeket is kérnie kell.

> [!Note] 
> Az Microsoft Graph-bővítmény csak az Azure AD-hitelesítést támogatja. A felhasználóknak munkahelyi vagy iskolai fiókkal kell bejelentkezniük.

Ha a Azure Portal használja, a bővítmény telepítéséhez a rendszer figyelmeztetést jelenít meg. A figyelmeztetés felszólítja App Service hitelesítés/engedélyezés konfigurálására, valamint a sablon vagy a kötés által igényelt engedélyek igénylésére. Kattintson az **Azure ad konfigurálása most** vagy az **engedélyek megadása most** lehetőségre.



<a name="token-input"></a>
## <a name="auth-token"></a>Hitelesítési jogkivonat

Az Auth token bemeneti kötése egy Azure AD-tokent kap egy adott erőforráshoz, és karakterláncként adja meg a kódot. Az erőforrás lehet bármely, amelyhez az alkalmazás rendelkezik engedéllyel. 

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#auth-token---example)
* [Attribútumok](#auth-token---attributes)
* [Konfigurálás](#auth-token---configuration)
* [Használat](#auth-token---usage)

### <a name="auth-token---example"></a>Hitelesítési jogkivonat – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Hitelesítési jogkivonat – C# parancsfájl – példa

A következő példa beolvassa a felhasználói profil adatait.

A fájl *function.js* egy olyan http-triggert határoz meg, amely token bemeneti kötéssel rendelkezik:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A C# parancsfájl a token használatával HTTP-hívást hajt végre a Microsoft Graph, és visszaadja az eredményt:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Hitelesítési jogkivonat – JavaScript-példa

A következő példa beolvassa a felhasználói profil adatait.

A fájl *function.js* egy olyan http-triggert határoz meg, amely token bemeneti kötéssel rendelkezik:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A JavaScript-kód a token használatával HTTP-hívást hajt végre a Microsoft Graph, és visszaadja az eredményt.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Hitelesítési jogkivonat – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [jogkivonat](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) attribútumot.

### <a name="auth-token---configuration"></a>Hitelesítési jogkivonat – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Token` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – a hitelesítési jogkivonat kódjában használt változó neve. Lásd: [hitelesítési jogkivonat bemeneti kötésének használata kódból](#token-input-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `token` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `in` .|
|**identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak http- [trigger]esetén érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó identitását használja a megadott AZONOSÍTÓval. Tekintse meg a <code>userId</code> tulajdonságot.</li><li><code>userFromToken</code> -A megadott jogkivonat által jelölt identitást használja. Tekintse meg a <code>userToken</code> tulajdonságot.</li><li><code>clientCredentials</code> – A Function alkalmazás identitását használja.</li></ul>|
|**userId**|**UserId**  |Csak akkor szükséges, ha az _identitás_ értéke: `userFromId` . Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználói azonosító.|
|**userToken**|**UserToken**|Csak akkor szükséges, ha az _identitás_ értéke: `userFromToken` . A Function alkalmazáshoz érvényes jogkivonat. |
|**Erőforrás**|**erőforrás**|Kötelező – egy Azure AD-erőforrás URL-címe, amelyhez a jogkivonatot kéri.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Hitelesítési jogkivonat – használat

Maga a kötés nem igényel Azure AD-engedélyeket, azonban a jogkivonat felhasználási módjától függően előfordulhat, hogy további engedélyeket kell kérnie. Tekintse át a jogkivonattal elérni kívánt erőforrás követelményeit.

A jogkivonat mindig karakterláncként jelenik meg a kódban.

> [!Note]
> Ha a-t vagy a-t helyi fejlesztéssel fejleszti `userFromId` `userFromToken` `userFromRequest` , a szükséges tokent [manuálisan is megszerezheti](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) , és a `X-MS-TOKEN-AAD-ID-TOKEN` kérések fejlécében megadható a hívó ügyfélalkalmazás.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-bemenet

Az Excel-táblázat bemeneti kötése beolvassa a OneDrive-ben tárolt Excel-táblázat tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#excel-input---example)
* [Attribútumok](#excel-input---attributes)
* [Konfigurálás](#excel-input---configuration)
* [Használat](#excel-input---usage)

### <a name="excel-input---example"></a>Excel input – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel input – C# parancsfájl – példa

A fájl következő *function.js* egy olyan http-triggert határoz meg, amely egy Excel bemeneti kötéssel rendelkezik:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A következő C#-kód beolvassa a megadott tábla tartalmát, és visszaadja azokat a felhasználónak:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel-bemenet – JavaScript-példa

A fájl következő *function.js* egy olyan http-triggert határoz meg, amely egy Excel bemeneti kötéssel rendelkezik:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A következő JavaScript-kód beolvassa a megadott tábla tartalmát, és visszaadja azokat a felhasználónak.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel-bemenet – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja az [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribútumot.

### <a name="excel-input---configuration"></a>Excel-bemenet – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Excel` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – az Excel-táblázat függvény kódjában használt változó neve. Lásd: [Excel-táblázat bemeneti kötésének használata kódból](#excel-input-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `excel` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `in` .|
|**identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak http- [trigger]esetén érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó identitását használja a megadott AZONOSÍTÓval. Tekintse meg a <code>userId</code> tulajdonságot.</li><li><code>userFromToken</code> -A megadott jogkivonat által jelölt identitást használja. Tekintse meg a <code>userToken</code> tulajdonságot.</li><li><code>clientCredentials</code> – A Function alkalmazás identitását használja.</li></ul>|
|**userId**|**UserId**  |Csak akkor szükséges, ha az _identitás_ értéke: `userFromId` . Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználói azonosító.|
|**userToken**|**UserToken**|Csak akkor szükséges, ha az _identitás_ értéke: `userFromToken` . A Function alkalmazáshoz érvényes jogkivonat. |
|**elérési útja**|**Elérési út**|Kötelező – a OneDrive elérési útja az Excel-munkafüzethez.|
|**worksheetName**|**WorksheetName**|Az a munkalap, amelyben a tábla található.|
|**tableName**|**Táblanév**|A tábla neve. Ha nincs megadva, a rendszer a munkalap tartalmát fogja használni.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel input – használat

Ehhez a kötéshez a következő Azure AD-engedélyek szükségesek:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználói fájlok olvasása|

A kötés a következő típusokat teszi elérhetővé a .NET-függvények számára:
- karakterlánc [] []
- Microsoft. Graph. WorkbookTable
- Egyéni objektumtípusok (a strukturális modell kötésének használatával)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-kimenet

Az Excel kimeneti kötése módosítja a OneDrive-ben tárolt Excel-táblázat tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#excel-output---example)
* [Attribútumok](#excel-output---attributes)
* [Konfigurálás](#excel-output---configuration)
* [Használat](#excel-output---usage)

### <a name="excel-output---example"></a>Excel kimenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel kimenet – C# parancsfájl – példa

A következő példa sorokat szúr be egy Excel-táblába.

A fájl *function.js* egy Excel kimeneti KÖTÉSű http-triggert definiál:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A C#-szkript kódja új sort hoz létre a táblához (feltételezhető, hogy egyoszlopos) a lekérdezési karakterlánc bemenete alapján:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Excel kimenet – JavaScript-példa

A következő példa sorokat szúr be egy Excel-táblába.

A fájl *function.js* egy Excel kimeneti KÖTÉSű http-triggert definiál:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A következő JavaScript-kód egy új sort szúr be a táblába (feltételezve, hogy egyoszlopos) a lekérdezési karakterlánc bemenete alapján.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel-kimenet – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja az [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribútumot.

### <a name="excel-output---configuration"></a>Excel-kimenet – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Excel` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – a hitelesítési jogkivonat kódjában használt változó neve. Lásd: [Excel-táblázat kimeneti kötésének használata kódból](#excel-output-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `excel` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `out` .|
|**identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak http- [trigger]esetén érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó identitását használja a megadott AZONOSÍTÓval. Tekintse meg a <code>userId</code> tulajdonságot.</li><li><code>userFromToken</code> -A megadott jogkivonat által jelölt identitást használja. Tekintse meg a <code>userToken</code> tulajdonságot.</li><li><code>clientCredentials</code> – A Function alkalmazás identitását használja.</li></ul>|
|**UserId** |**userId** |Csak akkor szükséges, ha az _identitás_ értéke: `userFromId` . Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználói azonosító.|
|**userToken**|**UserToken**|Csak akkor szükséges, ha az _identitás_ értéke: `userFromToken` . A Function alkalmazáshoz érvényes jogkivonat. |
|**elérési útja**|**Elérési út**|Kötelező – a OneDrive elérési útja az Excel-munkafüzethez.|
|**worksheetName**|**WorksheetName**|Az a munkalap, amelyben a tábla található.|
|**tableName**|**Táblanév**|A tábla neve. Ha nincs megadva, a rendszer a munkalap tartalmát fogja használni.|
|**Frissítés típusa**|**Frissítés típusa**|Kötelező – a táblára való váltás típusa. A következő értékek egyike lehet:<ul><li><code>update</code> – Lecseréli a tábla tartalmát a OneDrive.</li><li><code>append</code> – Új sorok létrehozásával hozzáadja a hasznos adatokat a OneDrive tábla végéhez.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel-kimenet – használat

Ehhez a kötéshez a következő Azure AD-engedélyek szükségesek:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználói fájlokhoz|

A kötés a következő típusokat teszi elérhetővé a .NET-függvények számára:
- karakterlánc [] []
- Newtonsoft.Jsbekapcsolva. LINQ. JObject
- Microsoft. Graph. WorkbookTable
- Egyéni objektumtípusok (a strukturális modell kötésének használatával)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Fájl bemenete

A OneDrive fájl bemeneti kötése beolvassa a OneDrive-ben tárolt fájl tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#file-input---example)
* [Attribútumok](#file-input---attributes)
* [Konfigurálás](#file-input---configuration)
* [Használat](#file-input---usage)

### <a name="file-input---example"></a>Fájl bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Fájl bemenet – C# parancsfájl – példa

A következő példa egy OneDrive tárolt fájlt olvas be.

A fájl *function.js* egy http-triggert határoz meg egy OneDrive-fájl bemeneti kötésével:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A C# parancsfájl beolvassa a lekérdezési karakterláncban megadott fájlt, és naplózza a hosszát:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Fájl bevitele – JavaScript-példa

A következő példa egy OneDrive tárolt fájlt olvas be.

A fájl *function.js* egy http-triggert határoz meg egy OneDrive-fájl bemeneti kötésével:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A következő JavaScript-kód beolvassa a lekérdezési karakterláncban megadott fájlt, és visszaadja a hosszát.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Fájl bemenete – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribútumot.

### <a name="file-input---configuration"></a>Fájl bemenete – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `OneDrive` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – a fájlhoz tartozó függvény kódjában használt változó neve. Lásd: [OneDrive-fájl bemeneti kötésének használata kódból](#onedrive-input-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `onedrive` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `in` .|
|**identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak http- [trigger]esetén érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó identitását használja a megadott AZONOSÍTÓval. Tekintse meg a <code>userId</code> tulajdonságot.</li><li><code>userFromToken</code> -A megadott jogkivonat által jelölt identitást használja. Tekintse meg a <code>userToken</code> tulajdonságot.</li><li><code>clientCredentials</code> – A Function alkalmazás identitását használja.</li></ul>|
|**userId**|**UserId**  |Csak akkor szükséges, ha az _identitás_ értéke: `userFromId` . Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználói azonosító.|
|**userToken**|**UserToken**|Csak akkor szükséges, ha az _identitás_ értéke: `userFromToken` . A Function alkalmazáshoz érvényes jogkivonat. |
|**elérési útja**|**Elérési út**|Kötelező – a fájl OneDrive elérési útja.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Fájl bevitele – használat

Ehhez a kötéshez a következő Azure AD-engedélyek szükségesek:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználói fájlok olvasása|

A kötés a következő típusokat teszi elérhetővé a .NET-függvények számára:
- bájt []
- Adatfolyam
- sztring
- Microsoft. Graph. DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Fájl kimenete

A OneDrive fájl kimeneti kötése módosítja a OneDrive-ben tárolt fájl tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#file-output---example)
* [Attribútumok](#file-output---attributes)
* [Konfigurálás](#file-output---configuration)
* [Használat](#file-output---usage)

### <a name="file-output---example"></a>Fájl kimenete – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Fájl kimenete – C# parancsfájl – példa

Az alábbi példa egy, a OneDrive-ben tárolt fájlba ír.

A fájl *function.js* a OneDrive kimeneti kötést tartalmazó http-triggert definiál:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A C# parancsfájl a lekérdezési karakterlánc szövegét írja le, és egy szövegfájlba (FunctionsTest.txt az előző példában definiált módon) a hívó OneDrive gyökerére írja:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Fájl kimenete – JavaScript-példa

Az alábbi példa egy, a OneDrive-ben tárolt fájlba ír.

A fájl *function.js* a OneDrive kimeneti kötést tartalmazó http-triggert definiál:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A JavaScript-kód beolvassa a lekérdezési karakterlánc szövegét, és egy szövegfájlba (FunctionsTest.txt a fenti konfigurációban meghatározott módon) írja a hívó OneDrive gyökerébe.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Fájl kimenete – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribútumot.

### <a name="file-output---configuration"></a>Fájl kimenete – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `OneDrive` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – a fájlhoz tartozó függvény kódjában használt változó neve. Lásd: [OneDrive-fájl kimeneti kötésének használata kódból](#onedrive-output-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `onedrive` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `out` .|
|**identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak http- [trigger]esetén érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó identitását használja a megadott AZONOSÍTÓval. Tekintse meg a <code>userId</code> tulajdonságot.</li><li><code>userFromToken</code> -A megadott jogkivonat által jelölt identitást használja. Tekintse meg a <code>userToken</code> tulajdonságot.</li><li><code>clientCredentials</code> – A Function alkalmazás identitását használja.</li></ul>|
|**UserId** |**userId** |Csak akkor szükséges, ha az _identitás_ értéke: `userFromId` . Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználói azonosító.|
|**userToken**|**UserToken**|Csak akkor szükséges, ha az _identitás_ értéke: `userFromToken` . A Function alkalmazáshoz érvényes jogkivonat. |
|**elérési útja**|**Elérési út**|Kötelező – a fájl OneDrive elérési útja.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Fájl kimenete – használat

Ehhez a kötéshez a következő Azure AD-engedélyek szükségesek:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználói fájlokhoz|

A kötés a következő típusokat teszi elérhetővé a .NET-függvények számára:
- bájt []
- Adatfolyam
- sztring
- Microsoft. Graph. DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-kimenet

Az Outlook-üzenet kimeneti kötése e-mail üzenetet küld az Outlookon keresztül.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#outlook-output---example)
* [Attribútumok](#outlook-output---attributes)
* [Konfigurálás](#outlook-output---configuration)
* [Használat](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook kimenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook kimenet – C# parancsfájl – példa

Az alábbi példa egy e-mailt küld az Outlookon keresztül.

A fájl *function.js* egy Outlook-üzenet kimeneti kötésével rendelkező http-triggert definiál:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

A C#-szkript kódja a hívótól érkező e-mailt küld a lekérdezési karakterláncban megadott címzettnek:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Outlook kimenet – JavaScript-példa

Az alábbi példa egy e-mailt küld az Outlookon keresztül.

A fájl *function.js* egy Outlook-üzenet kimeneti kötésével rendelkező http-triggert definiál:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

A JavaScript-kód a hívótól érkező e-mailt küld a lekérdezési karakterláncban megadott címzettnek:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Outlook kimenete – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja az [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) attribútumot.

### <a name="outlook-output---configuration"></a>Outlook kimenet – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Outlook` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – a levelezési üzenethez tartozó függvény kódjában használt változó neve. Lásd: [Outlook-üzenet kimeneti kötésének használata kódból](#outlook-output-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `outlook` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `out` .|
|**identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak http- [trigger]esetén érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó identitását használja a megadott AZONOSÍTÓval. Tekintse meg a <code>userId</code> tulajdonságot.</li><li><code>userFromToken</code> -A megadott jogkivonat által jelölt identitást használja. Tekintse meg a <code>userToken</code> tulajdonságot.</li><li><code>clientCredentials</code> – A Function alkalmazás identitását használja.</li></ul>|
|**userId**|**UserId**  |Csak akkor szükséges, ha az _identitás_ értéke: `userFromId` . Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználói azonosító.|
|**userToken**|**UserToken**|Csak akkor szükséges, ha az _identitás_ értéke: `userFromToken` . A Function alkalmazáshoz érvényes jogkivonat. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook-kimenet – használat

Ehhez a kötéshez a következő Azure AD-engedélyek szükségesek:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|E-mail küldése felhasználóként|

A kötés a következő típusokat teszi elérhetővé a .NET-függvények számára:
- Microsoft. Graph. Message
- Newtonsoft.Jsbekapcsolva. LINQ. JObject
- sztring
- Egyéni objektumtípusok (a strukturális modell kötésének használatával)






## <a name="webhooks"></a>Webhookok

A webhookok lehetővé teszik a Microsoft Graph eseményeire való reagálást. A webhookok támogatásához a függvényeknek a _webhook-előfizetések_létrehozásához, frissítéséhez és az azokra való reagáláshoz is szükségük van. A teljes webhook-megoldás a következő kötések kombinációját igényli:
- A [Microsoft Graph webhook-trigger](#webhook-trigger) lehetővé teszi, hogy reagáljon egy bejövő webhookra.
- A [Microsoft Graph webhook előfizetésének bemeneti kötése](#webhook-input) lehetővé teszi a meglévő előfizetések listázását és igény szerint frissítését.
- A [Microsoft Graph webhook-előfizetés kimeneti kötése](#webhook-output) lehetővé teszi webhook-előfizetések létrehozását vagy törlését.

Maguk a kötések nem igényelnek Azure AD-engedélyeket, de a reagálni kívánt erőforrás típusára vonatkozó engedélyeket kell kérnie. Az egyes erőforrástípusok számára szükséges engedélyek listáját itt tekintheti meg: előfizetés- [engedélyek](/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

A webhookokkal kapcsolatos további információkért lásd: [Microsoft Graph webhookok használata].





## <a name="webhook-trigger"></a>Webhook-trigger

A Microsoft Graph webhook-trigger lehetővé teszi, hogy a függvény reagáljon egy bejövő webhookra a Microsoft Graph. Ennek az eseményindítónak minden példánya egy Microsoft Graph-erőforrástípusra reagálhat.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-trigger---example)
* [Attribútumok](#webhook-trigger---attributes)
* [Konfigurálás](#webhook-trigger---configuration)
* [Használat](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook-trigger – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook trigger – C# parancsfájl – példa

Az alábbi példa a beérkező Outlook-üzenetekhez tartozó webhookokat kezeli. Webhook-trigger használatához létre kell [hoznia egy előfizetést](#webhook-output---example), és [frissítheti az előfizetést](#webhook-subscription-refresh) , hogy megakadályozza a lejáratát.

A fájl *function.js* webhook-triggert definiál:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

A C#-parancsfájl a bejövő levelek üzeneteire lép át, és naplózza a címzett által küldött és a tárgyban található "Azure Functions" kifejezést tartalmazó törzset:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Webhook-trigger – JavaScript-példa

Az alábbi példa a beérkező Outlook-üzenetekhez tartozó webhookokat kezeli. Webhook-trigger használatához létre kell [hoznia egy előfizetést](#webhook-output---example), és [frissítheti az előfizetést](#webhook-subscription-refresh) , hogy megakadályozza a lejáratát.

A fájl *function.js* webhook-triggert definiál:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

A JavaScript-kód a beérkező levelek üzeneteire lép át, és naplózza a címzett által küldött és a tárgyban található "Azure Functions" kifejezést tartalmazó törzset:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Webhook-trigger – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [GraphWebhookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) attribútumot.

### <a name="webhook-trigger---configuration"></a>Webhook-trigger – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `GraphWebhookTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – a levelezési üzenethez tartozó függvény kódjában használt változó neve. Lásd: [Outlook-üzenet kimeneti kötésének használata kódból](#outlook-output-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `graphWebhook` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `trigger` .|
|**resourceType**|**ResourceType**|Kötelező – a Graph-erőforrás, amelynek a függvénynek válaszolnia kell a webhookokra. A következő értékek egyike lehet:<ul><li><code>#Microsoft.Graph.Message</code> – az Outlook-üzenetekben végrehajtott módosítások.</li><li><code>#Microsoft.Graph.DriveItem</code> – a OneDrive-gyökér elemein végrehajtott módosítások.</li><li><code>#Microsoft.Graph.Contact</code> – a személyes névjegyek módosításai az Outlookban.</li><li><code>#Microsoft.Graph.Event</code> – az Outlook naptár elemein végrehajtott módosítások.</li></ul>|

> [!Note]
> A Function alkalmazásnak csak egy függvénye lehet, amely egy adott értékre van regisztrálva `resourceType` .

### <a name="webhook-trigger---usage"></a>Webhook-trigger – használat

A kötés a következő típusokat teszi elérhetővé a .NET-függvények számára:
- Microsoft Graph az erőforrástípus, például `Microsoft.Graph.Message` vagy `Microsoft.Graph.DriveItem` .
- Egyéni objektumtípusok (a strukturális modell kötésének használatával)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook bemenet

A Microsoft Graph webhook bemeneti kötése lehetővé teszi a Function app által kezelt előfizetések listájának beolvasását. A kötés beolvassa a Function app Storage-ból, így az nem tükrözi az alkalmazáson kívül létrehozott egyéb előfizetéseket.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-input---example)
* [Attribútumok](#webhook-input---attributes)
* [Konfigurálás](#webhook-input---configuration)
* [Használat](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook bemenet – C# parancsfájl – példa

A következő példa lekéri a hívó felhasználó összes előfizetését, és törli őket.

A fájl *function.js* egy előfizetési bemeneti KÖTÉSSEL rendelkező http-triggert és egy előfizetési kimeneti kötést határoz meg, amely a DELETE műveletet használja:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A C# parancsfájl-kód beolvassa az előfizetéseket, és törli őket:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Webhook bemenet – JavaScript-példa

A következő példa lekéri a hívó felhasználó összes előfizetését, és törli őket.

A fájl *function.js* egy előfizetési bemeneti KÖTÉSSEL rendelkező http-triggert és egy előfizetési kimeneti kötést határoz meg, amely a DELETE műveletet használja:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A JavaScript-kód lekéri az előfizetéseket, és törli őket:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Webhook bemenete – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) attribútumot.

### <a name="webhook-input---configuration"></a>Webhook bemenete – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `GraphWebhookSubscription` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – a levelezési üzenethez tartozó függvény kódjában használt változó neve. Lásd: [Outlook-üzenet kimeneti kötésének használata kódból](#outlook-output-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `graphWebhookSubscription` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `in` .|
|**szűrő**|**Szűrő**| Ha a értékre van állítva `userFromRequest` , akkor a kötés csak a hívó felhasználó tulajdonában lévő előfizetéseket kéri le (csak a [http-triggerrel]érvényes).| 

### <a name="webhook-input---usage"></a>Webhook bevitele – használat

A kötés a következő típusokat teszi elérhetővé a .NET-függvények számára:
- karakterlánc []
- Egyéni objektumtípus-tömbök
- Newtonsoft.Jsbekapcsolva. LINQ. JObject []
- Microsoft. Graph. előfizetés []





## <a name="webhook-output"></a>Webhook kimenete

A webhook előfizetésének kimeneti kötése lehetővé teszi a Microsoft Graph webhook-előfizetések létrehozását, törlését és frissítését.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-output---example)
* [Attribútumok](#webhook-output---attributes)
* [Konfigurálás](#webhook-output---configuration)
* [Használat](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook kimenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook kimenet – C# parancsfájl – példa

A következő példa létrehoz egy előfizetést. [Frissítheti az előfizetést](#webhook-subscription-refresh) , hogy megakadályozza a lejáratát.

A fájl *function.js* egy előfizetési kimenettel rendelkező http-triggert határoz meg a Create művelet használatával:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A C# parancsfájl regisztrálja azt a webhookot, amely értesíti a Function alkalmazást, ha a hívó felhasználó Outlook-üzenetet kap:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Webhook kimenet – JavaScript-példa

A következő példa létrehoz egy előfizetést. [Frissítheti az előfizetést](#webhook-subscription-refresh) , hogy megakadályozza a lejáratát.

A fájl *function.js* egy előfizetési kimenettel rendelkező http-triggert határoz meg a Create művelet használatával:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A JavaScript-kód regisztrálja a webhookot, amely értesíti a Function alkalmazást, ha a hívó felhasználó Outlook-üzenetet kap:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook kimenete – attribútumok

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) attribútumot.

### <a name="webhook-output---configuration"></a>Webhook kimenete – konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `GraphWebhookSubscription` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**név**| n.a. |Kötelező – a levelezési üzenethez tartozó függvény kódjában használt változó neve. Lásd: [Outlook-üzenet kimeneti kötésének használata kódból](#outlook-output-code).|
|**típusa**| n.a. |Kötelező – a következőre kell beállítani: `graphWebhookSubscription` .|
|**irányba**| n.a. |Kötelező – a következőre kell beállítani: `out` .|
|**identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak http- [trigger]esetén érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó identitását használja a megadott AZONOSÍTÓval. Tekintse meg a <code>userId</code> tulajdonságot.</li><li><code>userFromToken</code> -A megadott jogkivonat által jelölt identitást használja. Tekintse meg a <code>userToken</code> tulajdonságot.</li><li><code>clientCredentials</code> – A Function alkalmazás identitását használja.</li></ul>|
|**userId**|**UserId**  |Csak akkor szükséges, ha az _identitás_ értéke: `userFromId` . Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználói azonosító.|
|**userToken**|**UserToken**|Csak akkor szükséges, ha az _identitás_ értéke: `userFromToken` . A Function alkalmazáshoz érvényes jogkivonat. |
|**művelet**|**Művelet**|Kötelező – meghatározza azt a műveletet, amelyet a kötésnek végre kell hajtania. A következő értékek egyike lehet:<ul><li><code>create</code> -Új előfizetés regisztrálása.</li><li><code>delete</code> -Egy megadott előfizetés törlése.</li><li><code>refresh</code> – Frissíti a megadott előfizetést, hogy a lejárati állapotban maradjon.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Csak akkor szükséges, ha a _művelet_ a következőre van beállítva: `create` . Meghatározza a változásokhoz figyelni kívánt Microsoft Graph erőforrást. Lásd: [webhookok használata Microsoft Graphban]. |
|**changeType**|**ChangeType**|Csak akkor szükséges, ha a _művelet_ a következőre van beállítva: `create` . Megadja az előfizetett erőforrás változásának típusát, amely értesítést fog létrehozni. A támogatott értékek a következők: `created` , `updated` , `deleted` . Vesszővel tagolt lista használatával több érték is egyesíthető.|

### <a name="webhook-output---usage"></a>Webhook kimenete – használat

A kötés a következő típusokat teszi elérhetővé a .NET-függvények számára:
- sztring
- Microsoft. Graph. előfizetés




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Webhook-előfizetés frissítése

Az előfizetések frissítésének két módja van:

- Az alkalmazás identitásával kezelheti az összes előfizetést. Ehhez hozzá kell járulnia egy Azure Active Directory-rendszergazdától. Ezt a Azure Functions által támogatott összes nyelv használhatja.
- Az egyes előfizetésekhez tartozó identitást az egyes felhasználói AZONOSÍTÓk manuális kötésével használhatja. Ehhez szükség lesz egy egyéni kódra a kötés végrehajtásához. Ezt csak a .NET függvények használhatják.

Ez a szakasz példákat tartalmaz a következő módszerekre:

* [Példa az alkalmazás identitására](#webhook-subscription-refresh---app-identity-example)
* [Példa felhasználói identitásra](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook-előfizetés frissítése – példa az alkalmazás identitására

Tekintse meg a nyelvspecifikus példát:

* [C#-szkript (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Alkalmazás-identitás frissítése – C# parancsfájl – példa

Az alábbi példa az alkalmazás identitását használja az előfizetés frissítéséhez.

A *function.js* egy előfizetési bemeneti kötést és egy előfizetési kimeneti kötést tartalmazó időzítő triggert definiál:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

A C# parancsfájl-kód frissíti az előfizetéseket:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Alkalmazás-identitás frissítése – C# parancsfájl – példa

Az alábbi példa az alkalmazás identitását használja az előfizetés frissítéséhez.

A *function.js* egy előfizetési bemeneti kötést és egy előfizetési kimeneti kötést tartalmazó időzítő triggert definiál:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

A JavaScript-kód frissíti az előfizetéseket:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Webhook-előfizetés frissítése – példa felhasználói identitásra

Az alábbi példa a felhasználói identitást használja az előfizetés frissítéséhez.

A fájl *function.js* egy időzítő-triggert határoz meg, és elhalasztja az előfizetés bemeneti kötését a következő függvény kódjában:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A C# parancsfájl-kód frissíti az előfizetéseket, és létrehozza a kimeneti kötést a kódban, az egyes felhasználói identitások használatával:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

[HTTP-eseményindító]: functions-bindings-http-webhook.md
[Webhookok használata Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
