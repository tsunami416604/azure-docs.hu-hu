---
title: Microsoft Graph-kötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használhatja a Microsoft Graph-eseményindítók és kötések az Azure Functions szolgáltatásban.
services: functions
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: b77b035d829218d7416da4c6d57919e75b278e02
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288900"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph-kötések az Azure Functions szolgáltatáshoz

Ez a cikk bemutatja, hogyan konfigurálhat és a Microsoft Graph eseményindítók és kötések az Azure Functions használata. Az ilyen az Azure Functions használatával adatait, elemzéseket és események használata az [Microsoft Graph](https://developer.microsoft.com/graph).

A Microsoft Graph-bővítmény a következő kötéseket biztosít:
- Egy [hitelesítési tokent bemeneti kötést](#token-input) lehetővé teszi, hogy bármely Microsoft Graph API használatához.
- Egy [Excel-táblázatot a bemeneti kötést](#excel-input) lehetővé teszi, hogy adatokat olvasni az Excel.
- Egy [Excel-tábla kimeneti kötésének](#excel-output) lehetővé teszi, hogy módosítsa az Excel-adatok.
- A [OneDrive-fájl bemeneti kötést](#onedrive-input) lehetővé teszi, hogy olvassa el a fájlokat a onedrive-ról.
- A [OneDrive-fájl kimeneti kötésének](#onedrive-output) lehetővé teszi, hogy a onedrive vállalati verzióbeli fájlok írni.
- Egy [Outlook-üzenet kimeneti kötésének](#outlook-output) lehetővé teszi, hogy az Outlook használatával e-mailt.
- A gyűjtemény [Microsoft Graph-webhook eseményindítók és kötések](#webhooks) lehetővé teszi, hogy a Microsoft Graph reagálhat rájuk.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> A Microsoft Graph-kötések jelenleg az Azure Functions verziója előzetes verzióban 2.x. Nem támogatott funkciók verzióban 1.x.

## <a name="packages"></a>Csomagok

A hitelesítési jogkivonat bemeneti kötés van megadva a [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet-csomagot. Az egyéb Microsoft Graph-kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) csomagot. A csomagok forráskódja a [azure-functions-microsoftgraph-bővítmény](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>A bővítmények beállítása

A Microsoft Graph-kötések keresztül érhetők el _kötési bővítményeket_. Kötési bővítményeket is választható összetevők az Azure Functions futtatókörnyezettel. Ez a szakasz bemutatja, hogyan állíthatja be a Microsoft Graph és a hitelesítési jogkivonat bővítmények.

### <a name="enabling-functions-20-preview"></a>Functions 2.0 előzetes verziójának engedélyezése

Kötési bővítményeket csak az Azure Functions 2.0 előzetes verzióban érhető el. 

Egy függvényalkalmazás 2.0 előzetes verzióját, a Functions futtatókörnyezete használandó beállításával kapcsolatos további információkért lásd: [bemutatásához az Azure Functions runtime verziók](set-runtime-version.md).

### <a name="installing-the-extension"></a>A bővítmény telepítése

-Bővítményének telepítése az Azure Portalon lépjen a sablon és kötése hivatkozik rá. Hozzon létre egy új függvényt, és a sablon kiválasztására szolgáló képernyő megjelenítéséhez válassza a "Microsoft Graph" forgatókönyvet. Válassza ki a forgatókönyvet a sablonok egyikét. Azt is megteheti egy meglévő függvényt "Integrálás" lapján keresse meg és válassza ki a kötéseket a cikkben szereplő egyik.

Mindkét esetben figyelmeztetés fog megjelenni, amely megadja, hogy a bővítmény telepíthető. Kattintson a **telepítése** a bővítmény beszerzése. Minden bővítmény csak függvény alkalmazásonként egyszer telepíteni kell. 

> [!Note] 
> A portál a telepítési folyamat a használatalapú csomag is igénybe vehet akár 10 percet.

Ha használja a Visual Studio, a bővítmények beszerezheti telepítésével [a NuGet-csomagokat a cikk korábbi részében felsorolt](#packages).

### <a name="configuring-authentication--authorization"></a>Hitelesítés / engedélyezés

A következő cikkben ismertetett kötések szükséges használandó identitást. Ez lehetővé teszi a Microsoft Graph kényszerítése az engedélyeket, és kapcsolati naplózása. Az identitás lehet egy felhasználó az alkalmazás vagy maga az alkalmazás eléréséhez. Adja meg ezt az identitást, állítsa be a [App Service-hitelesítés / engedélyezés](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) az Azure Active Directoryval. Akkor is kell kérnie a függvényekben erőforrás engedélyek.

> [!Note] 
> A Microsoft Graph-bővítmény csak az Azure AD-hitelesítést támogatja. Felhasználók kell bejelentkezni a munkahelyi vagy iskolai fiókkal jelentkezik be.

Használata az Azure Portalon, megjelenik egy figyelmeztetés alatt a rendszer kéri a bővítmény telepítéséhez. A figyelmeztetés felszólítja, hogy konfigurálja az App Service-hitelesítés / engedélyezés és a kérés a sablonból vagy a kötés engedélyeket igényel. Kattintson a **konfigurálása most már Azure ad-ben** vagy **engedélyek hozzáadása** megfelelő módon.



<a name="token-input"></a>
## <a name="auth-token"></a>Hitelesítési jogkivonat

A hitelesítési jogkivonat bemeneti kötést egy Azure AD-token beolvasása egy adott erőforráshoz, és átadja a kód karakterláncként. Az erőforrás, amelynek bármilyen lehet az alkalmazás engedélyekkel rendelkezik. 

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#auth-token---example)
* [Attribútumok](#auth-token---attributes)
* [Konfigurálás](#auth-token---configuration)
* [Használat](#auth-token---usage)

### <a name="auth-token---example"></a>Hitelesítési tokent – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Hitelesítési jogkivonat - C#-szkript példa

Az alábbi példa lekéri a felhasználói profil adatait.

A *function.json* fájl határozza meg a HTTP-trigger egy token bemeneti kötéssel:

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

A C#-szkriptkódot használja a jogkivonatot, hogy a Microsoft Graph HTTP-hívás, és visszaadja az eredményt:

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

#### <a name="auth-token---javascript-example"></a>Hitelesítési jogkivonat - JavaScript-példa

Az alábbi példa lekéri a felhasználói profil adatait.

A *function.json* fájl határozza meg a HTTP-trigger egy token bemeneti kötéssel:

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

A JavaScript-kódot használja a jogkivonatot, hogy a Microsoft Graph HTTP-hívás, és visszaadja az eredményt.

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

### <a name="auth-token---attributes"></a>Hitelesítési jogkivonat - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) attribútum.

### <a name="auth-token---configuration"></a>Hitelesítési jogkivonat - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Token` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a függvénykódot a hitelesítési jogkivonat használt változó neve. Lásd: [egy hitelesítési tokent használó bemeneti kötést kódból](#token-input-code).|
|**type**||Kötelező – kell állítani `token`.|
|**direction**||Kötelező – kell állítani `in`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, a művelet végrehajtásához használt. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak érvényes [HTTP-eseményindító]. A hívó felhasználó identitást használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó használja a megadott azonosítóval. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> – A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> – A függvény alkalmazás identitását használja.</li></ul>|
|**userId**|**Felhasználói azonosító**  |Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromId`. A felhasználó résztvevő-azonosító egy korábban bejelentkezett felhasználóhoz társított.|
|**userToken**|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromToken`. A jogkivonat érvényes, a függvényalkalmazás. |
|**Erőforrás**|**resource**|Szükséges – az Azure ad-ben erőforrás URL-címe, amelyhez a jogkivonatot kért.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Hitelesítési jogkivonat - használat

Maga a kötés nem igényel minden olyan Azure AD-engedélyekről, de attól függően, hogyan használja a jogkivonatot, szükség lehet további engedélyek kéréséhez. Az erőforrás-hozzáférési jogkivonattal kíván a követelmények ellenőrzéséhez.

A jogkivonat mindig megjelenik kód karakterláncként.




<a name="excel-input"></a>
## <a name="excel-input"></a>Az Excel-bemenet

Az Excel-táblázat bemeneti kötésnek a onedrive vállalati verzióban tárolt Excel-táblázat tartalmának beolvasása.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#excel-input---example)
* [Attribútumok](#excel-input---attributes)
* [Konfigurálás](#excel-input---configuration)
* [Használat](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-bevitel – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Az Excel bemeneti – C#-szkript példa

A következő *function.json* fájl határozza meg a HTTP-trigger bemeneti kötéssel Excel:

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

A következő C#-szkriptkódot beolvassa a megadott tábla tartalmát, és visszaadja azokat a felhasználó számára:

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

#### <a name="excel-input---javascript-example"></a>Az Excel - bemeneti JavaScript-példa

A következő *function.json* fájl határozza meg a HTTP-trigger bemeneti kötéssel Excel:

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

A következő JavaScript-kódot beolvassa a megadott tábla tartalmát, és visszaadja azokat a felhasználó.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Az Excel - bemeneti attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribútum.

### <a name="excel-input---configuration"></a>Excel-input - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Excel` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a az Excel-táblázatban a függvény kódját a használt változó neve. Lásd: [használatával egy Excel-táblázat bemeneti kötést kódból](#excel-input-code).|
|**type**||Kötelező – kell állítani `excel`.|
|**direction**||Kötelező – kell állítani `in`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, a művelet végrehajtásához használt. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak érvényes [HTTP-eseményindító]. A hívó felhasználó identitást használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó használja a megadott azonosítóval. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> – A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> – A függvény alkalmazás identitását használja.</li></ul>|
|**userId**|**Felhasználói azonosító**  |Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromId`. A felhasználó résztvevő-azonosító egy korábban bejelentkezett felhasználóhoz társított.|
|**userToken**|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromToken`. A jogkivonat érvényes, a függvényalkalmazás. |
|**path**|**Elérési út**|Kötelező – a onedrive-on az Excel-munkafüzet elérési útját.|
|**worksheetName**|**worksheetName**|A táblázatot tartalmazó munkalap.|
|**Táblanév**|**Táblanév**|A tábla neve. Ha nincs megadva, a munkalap tartalmát fogja használni.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Az Excel bemeneti - használat

Ennek a kötésnek a következő Azure AD-engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználók fájljainak olvasása|

A kötés tünteti fel a .NET-funkciók a következők:
- String [] []
- Microsoft.Graph.WorkbookTable
- Egyéni objektumtípusok (szerkezeti modellek kötés használatával)










<a name="excel-output"></a>
## <a name="excel-output"></a>Az Excel-kimenet

Az Excel kimeneti kötés módosítja a onedrive vállalati verzióban tárolt Excel-táblázatot a tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#excel-output---example)
* [Attribútumok](#excel-output---attributes)
* [Konfigurálás](#excel-output---configuration)
* [Használat](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-kimenet – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Az Excel kimenete – C#-szkript példa

Az alábbi példa sorokat ad hozzá egy Excel-táblázatban.

A *function.json* fájl határozza meg a HTTP-trigger egy Excel-kimeneti kötést:

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

A C#-szkriptkódot ad hozzá egy új sort a táblában (feltételezni, hogy egy oszlop), a bemeneti lekérdezési karakterlánc alapján:

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

#### <a name="excel-output---javascript-example"></a>Az Excel kimenete – JavaScript-példa

Az alábbi példa sorokat ad hozzá egy Excel-táblázatban.

A *function.json* fájl határozza meg a HTTP-trigger egy Excel-kimeneti kötést:

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

A következő JavaScript-kód hozzáadja a bemeneti lekérdezési karakterlánc alapján egy új sort a táblázathoz (feltételezni, hogy egy oszlop).

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Az Excel - kimeneti attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribútum.

### <a name="excel-output---configuration"></a>Excel-kimenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Excel` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a függvénykódot a hitelesítési jogkivonat használt változó neve. Lásd: [használatával egy Excel-táblázat kimeneti kötés kódból](#excel-output-code).|
|**type**||Kötelező – kell állítani `excel`.|
|**direction**||Kötelező – kell állítani `out`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, a művelet végrehajtásához használt. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak érvényes [HTTP-eseményindító]. A hívó felhasználó identitást használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó használja a megadott azonosítóval. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> – A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> – A függvény alkalmazás identitását használja.</li></ul>|
|**Felhasználói azonosító** |**userId** |Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromId`. A felhasználó résztvevő-azonosító egy korábban bejelentkezett felhasználóhoz társított.|
|**userToken**|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromToken`. A jogkivonat érvényes, a függvényalkalmazás. |
|**path**|**Elérési út**|Kötelező – a onedrive-on az Excel-munkafüzet elérési útját.|
|**worksheetName**|**worksheetName**|A táblázatot tartalmazó munkalap.|
|**Táblanév**|**Táblanév**|A tábla neve. Ha nincs megadva, a munkalap tartalmát fogja használni.|
|**updateType**|**Frissítés típusa**|Kötelező – a táblázaton végrehajtani kívánt módosítás típusa. A következő értékek egyike lehet:<ul><li><code>update</code> -Váltja fel a OneDrive-ban a tábla tartalmát.</li><li><code>append</code> – Ad hozzá a tartalom a onedrive-on a tábla végére új sorok létrehozásával.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Az Excel kimenete – használat

Ennek a kötésnek a következő Azure AD-engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználó fájljaihoz|

A kötés tünteti fel a .NET-funkciók a következők:
- String [] []
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Egyéni objektumtípusok (szerkezeti modellek kötés használatával)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Bemeneti fájllal

A OneDrive-fájl bemeneti kötésnek a onedrive-on tárolt fájl tartalmának beolvasása.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#file-input---example)
* [Attribútumok](#file-input---attributes)
* [Konfigurálás](#file-input---configuration)
* [Használat](#file-input---usage)

### <a name="file-input---example"></a>Fájl bevitel – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Fájl bemeneti – C#-szkript példa

Az alábbi példa egy fájlt a onedrive vállalati verzióban tárolt beolvasása.

A *function.json* fájl határozza meg a HTTP-trigger egy OneDrive-fájl bemeneti kötéssel:

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

A C#-szkriptkódot beolvassa a fájlt, a lekérdezési karakterláncban megadott, és naplózza az hossza:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Bemeneti - fájlt a JavaScript-példa

Az alábbi példa egy fájlt a onedrive vállalati verzióban tárolt beolvasása.

A *function.json* fájl határozza meg a HTTP-trigger egy OneDrive-fájl bemeneti kötéssel:

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

A következő JavaScript-kódot beolvassa a lekérdezési karakterláncban megadott fájlt, és annak hosszát adja vissza.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Fájl – bemeneti attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribútum.

### <a name="file-input---configuration"></a>Bemenet - fájl konfigurálása

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `OneDrive` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a fájl a függvény kódját a használt változó neve. Lásd: [egy OneDrive-fájl használatával bemeneti kötést kódból](#onedrive-input-code).|
|**type**||Kötelező – kell állítani `onedrive`.|
|**direction**||Kötelező – kell állítani `in`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, a művelet végrehajtásához használt. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak érvényes [HTTP-eseményindító]. A hívó felhasználó identitást használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó használja a megadott azonosítóval. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> – A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> – A függvény alkalmazás identitását használja.</li></ul>|
|**userId**|**Felhasználói azonosító**  |Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromId`. A felhasználó résztvevő-azonosító egy korábban bejelentkezett felhasználóhoz társított.|
|**userToken**|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromToken`. A jogkivonat érvényes, a függvényalkalmazás. |
|**path**|**Elérési út**|Kötelező – a onedrive-on a fájl elérési útját.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Fájl bemeneti - használat

Ennek a kötésnek a következő Azure AD-engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználók fájljainak olvasása|

A kötés tünteti fel a .NET-funkciók a következők:
- byte]
- Stream
- sztring
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Kimeneti fájl

A OneDrive-fájl kimeneti kötés módosítja egy onedrive-on tárolt fájl tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#file-output---example)
* [Attribútumok](#file-output---attributes)
* [Konfigurálás](#file-output---configuration)
* [Használat](#file-output---usage)

### <a name="file-output---example"></a>Fájl kimeneti – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Fájl kimenete – C#-szkript példa

Az alábbi példa egy onedrive vállalati verzióban tárolt fájlba írja.

A *function.json* fájl határozza meg a HTTP-trigger egy onedrive-on a kimeneti kötést:

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

A C#-szkriptkódot szöveget olvas be a lekérdezési karakterláncot, és a hívó OneDrive gyökérmappájában írja azt egy szövegfájlt (ahogyan az előző példában FunctionsTest.txt):

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

Az alábbi példa egy onedrive vállalati verzióban tárolt fájlba írja.

A *function.json* fájl határozza meg a HTTP-trigger egy onedrive-on a kimeneti kötést:

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

A JavaScript-kódot szöveget olvas be a lekérdezési karakterláncot, és írja azt egy szövegfájlt (a fenti konfigurációban meghatározott FunctionsTest.txt) a hívó OneDrive gyökérmappájában.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Fájl kimeneti - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribútum.

### <a name="file-output---configuration"></a>A fájl kimeneti - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `OneDrive` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a fájl a függvény kódját a használt változó neve. Lásd: [használatával egy OneDrive-fájl kimeneti kötés kódból](#onedrive-output-code).|
|**type**||Kötelező – kell állítani `onedrive`.|
|**direction**||Kötelező – kell állítani `out`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, a művelet végrehajtásához használt. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak érvényes [HTTP-eseményindító]. A hívó felhasználó identitást használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó használja a megadott azonosítóval. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> – A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> – A függvény alkalmazás identitását használja.</li></ul>|
|**Felhasználói azonosító** |**userId** |Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromId`. A felhasználó résztvevő-azonosító egy korábban bejelentkezett felhasználóhoz társított.|
|**userToken**|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromToken`. A jogkivonat érvényes, a függvényalkalmazás. |
|**path**|**Elérési út**|Kötelező – a onedrive-on a fájl elérési útját.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Fájl kimeneti - használat

Ennek a kötésnek a következő Azure AD-engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználó fájljaihoz|

A kötés tünteti fel a .NET-funkciók a következők:
- byte]
- Stream
- sztring
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-kimenet

Az Outlook-üzenet kimeneti kötés küld egy e-mail üzenetet az Outlook keresztül.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#outlook-output---example)
* [Attribútumok](#outlook-output---attributes)
* [Konfigurálás](#outlook-output---configuration)
* [Használat](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Outlook-kimenet – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Az Outlook kimenete – C#-szkript példa

Az alábbi példában az Outlook használatával e-mailt küld.

A *function.json* fájl határozza meg a HTTP-trigger egy Outlook-üzenet kimeneti kötést:

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

A C#-szkriptkódot a hívó egy levelet küld egy címzettnek, a lekérdezési karakterláncban megadott:

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

#### <a name="outlook-output---javascript-example"></a>Az Outlook kimenete – JavaScript-példa

Az alábbi példában az Outlook használatával e-mailt küld.

A *function.json* fájl határozza meg a HTTP-trigger egy Outlook-üzenet kimeneti kötést:

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

A JavaScript-kódot a hívó egy levelet küld egy címzettnek, a lekérdezési karakterláncban megadott:

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

### <a name="outlook-output---attributes"></a>Outlook - kimeneti attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) attribútum.

### <a name="outlook-output---configuration"></a>A kimenet az Outlook - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Outlook` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a függvénykódot az e-mail-üzenetet az használt változó neve. Lásd: [az Outlook-üzenet használata kimeneti kötés kódból](#outlook-output-code).|
|**type**||Kötelező – kell állítani `outlook`.|
|**direction**||Kötelező – kell állítani `out`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, a művelet végrehajtásához használt. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak érvényes [HTTP-eseményindító]. A hívó felhasználó identitást használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó használja a megadott azonosítóval. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> – A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> – A függvény alkalmazás identitását használja.</li></ul>|
|**userId**|**Felhasználói azonosító**  |Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromId`. A felhasználó résztvevő-azonosító egy korábban bejelentkezett felhasználóhoz társított.|
|**userToken**|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromToken`. A jogkivonat érvényes, a függvényalkalmazás. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Az Outlook kimenete – használat

Ennek a kötésnek a következő Azure AD-engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|E-mail küldése a felhasználó nevében|

A kötés tünteti fel a .NET-funkciók a következők:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- sztring
- Egyéni objektumtípusok (szerkezeti modellek kötés használatával)






## <a name="webhooks"></a>Webhookok

Webhookok lehetővé teszik a Microsoft Graph reagálhat rájuk. Webhookok támogatása érdekében függvények létrehozása, frissítése és reagálhat a szükséges _webhookelőfizetését_. Egy webhook teljes körű megoldás a következő kötéseket kombinációja van szükség:
- A [Microsoft Graph-webhook eseményindító](#webhook-trigger) lehetővé teszi, hogy egy bejövő webhook reagálhat rájuk.
- A [Microsoft Graph-webhookelőfizetés bemeneti kötést](#webhook-input) lehetővé teszi, hogy a meglévő előfizetések listában, és szükség esetén frissítse azokat.
- A [Microsoft Graph-webhookelőfizetés kimeneti kötésének](#webhook-output) lehetővé teszi, hogy hozzon létre vagy webhook előfizetések törlése.

Maguk a kötések nem szükséges minden olyan Azure AD-engedélyekről, de kell igényelnie reagálhat a kívánt erőforrások típusának megfelelő engedélyekkel. Egyes erőforrástípusok szükséges engedélyek, amelyek listáját lásd: [előfizetése engedélyei](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions).

További információ a webhookok: [webhookok a Microsoft Graph-használata].





## <a name="webhook-trigger"></a>Webhook trigger

A Microsoft Graph-webhook eseményindító lehetővé teszi, hogy a Microsoft Graph reagálni egy bejövő webhook függvény. Ez az eseményindító minden példánya reagálhat a Microsoft Graph-erőforrás egy típusa.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-trigger---example)
* [Attribútumok](#webhook-trigger---attributes)
* [Konfigurálás](#webhook-trigger---configuration)
* [Használat](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook-eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook-eseményindító - C#-szkript példa

A következő példában bejövő Outlook-üzenetek webhookjait kezeli. Használandó webhook aktiválja, [hozzon létre egy előfizetést](#webhook-output---example), és [az előfizetés frissítése](#webhook-subscription-refresh) megakadályozhatja, hogy hamarosan lejár.

A *function.json* fájl határozza meg a webhook eseményindítóhoz:

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

A C#-szkriptkódot reagál a beérkező e-mail üzenetek, és naplózza a címzett által küldött és az "Azure Functions" tartalmazó a tárgy törzse:

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

#### <a name="webhook-trigger---javascript-example"></a>Webhook-eseményindító - JavaScript-példa

A következő példában bejövő Outlook-üzenetek webhookjait kezeli. Használandó webhook aktiválja, [hozzon létre egy előfizetést](#webhook-output---example), és [az előfizetés frissítése](#webhook-subscription-refresh) megakadályozhatja, hogy hamarosan lejár.

A *function.json* fájl határozza meg a webhook eseményindítóhoz:

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

A JavaScript-kódot reagál a beérkező e-mail üzenetek, és naplózza a címzett által küldött és az "Azure Functions" tartalmazó a tárgy törzse:

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

### <a name="webhook-trigger---attributes"></a>Webhook-eseményindító - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) attribútum.

### <a name="webhook-trigger---configuration"></a>Webhook-eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `GraphWebHookTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a függvénykódot az e-mail-üzenetet az használt változó neve. Lásd: [az Outlook-üzenet használata kimeneti kötés kódból](#outlook-output-code).|
|**type**||Kötelező – kell állítani `graphWebhook`.|
|**direction**||Kötelező – kell állítani `trigger`.|
|**resourceType**|**ResourceType**|Kötelező – a graph-erőforrás, amelynek ez a függvény válaszolnia kell webhook. A következő értékek egyike lehet:<ul><li><code>#Microsoft.Graph.Message</code> – Outlook-üzenetek végzett módosítások.</li><li><code>#Microsoft.Graph.DriveItem</code> – OneDrive legfelső szintű elemek végzett módosítások.</li><li><code>#Microsoft.Graph.Contact</code> – személyes Outlook-névjegyek formájában módosításait.</li><li><code>#Microsoft.Graph.Event</code> -az Outlook naptári elemek végzett módosítások.</li></ul>|

> [!Note]
> A függvényalkalmazás csak rendelkezhet egy függvényt, amely regisztrálva van egy adott `resourceType` értéket.

### <a name="webhook-trigger---usage"></a>Webhook-eseményindító - használat

A kötés tünteti fel a .NET-funkciók a következők:
- Például az erőforrás típusa, a Microsoft Graph SDK típusokat `Microsoft.Graph.Message` vagy `Microsoft.Graph.DriveItem`.
- Egyéni objektumtípusok (szerkezeti modellek kötés használatával)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook bemenet

A Microsoft Graph-webhook bemeneti kötés lehetővé teszi, hogy a függvényalkalmazás által kezelt előfizetések listájának beolvasása. A kötés beolvassa a függvény alkalmazástár, így azt nem tükrözi az alkalmazáson kívül létrehozott más előfizetések.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-input---example)
* [Attribútumok](#webhook-input---attributes)
* [Konfigurálás](#webhook-input---configuration)
* [Használat](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook bevitel – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook bemeneti – C#-szkript példa

Az alábbi példa lekéri az összes előfizetés hívó felhasználó esetében, és törli őket.

A *function.json* fájl határozza meg a HTTP-trigger egy előfizetés bemeneti kötéssel és a egy előfizetés kimeneti kötést, amely a törlési művelet használja:

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

A C#-szkriptkódot az előfizetések beolvasása, és törli őket:

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

#### <a name="webhook-input---javascript-example"></a>Webhook bemeneti – JavaScript-példa

Az alábbi példa lekéri az összes előfizetés hívó felhasználó esetében, és törli őket.

A *function.json* fájl határozza meg a HTTP-trigger egy előfizetés bemeneti kötéssel és a egy előfizetés kimeneti kötést, amely a törlési művelet használja:

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

A JavaScript-kódot az előfizetések beolvasása, és törli őket:

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

### <a name="webhook-input---attributes"></a>Webhook bemeneti - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attribútum.

### <a name="webhook-input---configuration"></a>Webhook bemenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `GraphWebHookSubscription` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a függvénykódot az e-mail-üzenetet az használt változó neve. Lásd: [az Outlook-üzenet használata kimeneti kötés kódból](#outlook-output-code).|
|**type**||Kötelező – kell állítani `graphWebhookSubscription`.|
|**direction**||Kötelező – kell állítani `in`.|
|**filter**|**Szűrő**| Ha beállítása `userFromRequest`, majd a kötés csak lekéri a hívó felhasználó tulajdonában lévő előfizetéseket (csak érvényes [HTTP-eseményindító]).| 

### <a name="webhook-input---usage"></a>Webhook bemeneti - használat

A kötés tünteti fel a .NET-funkciók a következők:
- String]
- Egyéni objektum típusú tömbök
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Webhook-kimenet

A webhook előfizetés kimeneti kötés lehetővé teszi létrehozása, törlése és frissítése a Microsoft Graph-webhook előfizetések.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-output---example)
* [Attribútumok](#webhook-output---attributes)
* [Konfigurálás](#webhook-output---configuration)
* [Használat](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook kimenet – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook kimenete – C#-szkript példa

Az alábbi példa létrehoz egy előfizetést. Is [az előfizetés frissítése](#webhook-subscription-refresh) megakadályozhatja, hogy hamarosan lejár.

A *function.json* fájl egy előfizetés kimeneti kötés létrehozása művelet használata a HTTP-trigger határozza meg:

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

A C#-szkriptkódot regisztrál egy webhookot, amely a függvényalkalmazás értesíteni fogjuk, ha a hívó felhasználó kap egy Outlook-üzenet:

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

#### <a name="webhook-output---javascript-example"></a>Webhook kimenete – JavaScript-példa

Az alábbi példa létrehoz egy előfizetést. Is [az előfizetés frissítése](#webhook-subscription-refresh) megakadályozhatja, hogy hamarosan lejár.

A *function.json* fájl egy előfizetés kimeneti kötés létrehozása művelet használata a HTTP-trigger határozza meg:

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

A JavaScript-kódot egy webhookot, amely a függvényalkalmazás értesíteni fogjuk, ha a hívó felhasználó kap egy Outlook-üzenet regisztrálása:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook kimenete – attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attribútum.

### <a name="webhook-output---configuration"></a>Webhook kimeneti - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `GraphWebHookSubscription` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a függvénykódot az e-mail-üzenetet az használt változó neve. Lásd: [az Outlook-üzenet használata kimeneti kötés kódból](#outlook-output-code).|
|**type**||Kötelező – kell állítani `graphWebhookSubscription`.|
|**direction**||Kötelező – kell állítani `out`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, a művelet végrehajtásához használt. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> – Csak érvényes [HTTP-eseményindító]. A hívó felhasználó identitást használja.</li><li><code>userFromId</code> – Egy korábban bejelentkezett felhasználó használja a megadott azonosítóval. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> – A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> – A függvény alkalmazás identitását használja.</li></ul>|
|**userId**|**Felhasználói azonosító**  |Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromId`. A felhasználó résztvevő-azonosító egy korábban bejelentkezett felhasználóhoz társított.|
|**userToken**|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értékre van állítva `userFromToken`. A jogkivonat érvényes, a függvényalkalmazás. |
|**a művelet**|**Művelet**|Kötelező – meghatározza a kötés műveletet végre kell hajtania. A következő értékek egyike lehet:<ul><li><code>create</code> – Egy új előfizetést regisztrál.</li><li><code>delete</code> – A megadott előfizetés törlése.</li><li><code>refresh</code> – Frissíti egy megadott előfizetés, amely megakadályozza az hamarosan lejár.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Ha szükséges, és csak akkor, ha a _művelet_ értékre van állítva `create`. Adja meg a Microsoft Graph-erőforrás figyelni kívánt módosítások. Lásd: [webhookok a Microsoft Graph-használata]. |
|**changeType**|**ChangeType**|Ha szükséges, és csak akkor, ha a _művelet_ értékre van állítva `create`. Az előfizetett erőforrásban, amely kiváltja az értesítést a módosítás típusát jelzi. A támogatott értékek a következők: `created`, `updated`, `deleted`. Több érték kombinálhatók, vesszővel tagolt lista használatával.|

### <a name="webhook-output---usage"></a>Webhook kimenete – használat

A kötés tünteti fel a .NET-funkciók a következők:
- sztring
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Webhook előfizetés frissítése

Az előfizetések frissítése kétféleképpen történhet:

- Az alkalmazásazonosító használatával az összes előfizetés kezelése. Ehhez az Azure Active Directory rendszergazdája. a jóváhagyás szükséges Ez az Azure Functions által támogatott összes nyelv által használható.
- Az identitáshoz társított minden egyes előfizetés manuálisan kötés az egyes felhasználói azonosítóját. Ezt a beállítást egyéni kódokat végrehajtja a kötést. Ez csak használható .NET-függvényekkel.

Ez a szakasz tartalmaz például ezek a módszerek mindegyike esetében:

* [Alkalmazás-identitást példa](#webhook-subscription-refresh---app-identity-example)
* [Felhasználói identitás példa](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook előfizetés frissítése - alkalmazás identitását példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Alkalmazás identitás frissítése – C#-szkript példa

Az alábbi példában az identitása előfizetés frissítése.

A *function.json* előfizetéssel időzítő eseményindító meghatározása bemeneti kötést és a egy előfizetés kimeneti kötést:

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

A C#-szkriptkódot az előfizetések frissítése:

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

### <a name="app-identity-refresh---c-script-example"></a>Alkalmazás identitás frissítése – C#-szkript példa

Az alábbi példában az identitása előfizetés frissítése.

A *function.json* előfizetéssel időzítő eseményindító meghatározása bemeneti kötést és a egy előfizetés kimeneti kötést:

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

A JavaScript-kód frissíti az előfizetések:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Webhook előfizetés frissítése – például a felhasználói identitás

Az alábbi példában a felhasználói identitás egy előfizetés frissítése.

A *function.json* fájl egy időzítő eseményindító meghatározása, és az előfizetés bemeneti kötést a függvénykódot késleltet:

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

A C#-szkriptkódot az előfizetések frissül, és létrehozza a kimeneti kötés a code-ban minden egyes felhasználói identitás használatával:

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

[HTTP-eseményindító]: functions-bindings-http-webhook.md
[Webhookok a Microsoft Graph-használata]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
