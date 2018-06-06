---
title: Az Azure Functions Microsoft Graph kötései
description: A Microsoft Graph-eseményindítók és kötések az Azure Functions használatának megismerése.
services: functions
author: mattchenderson
manager: cfowler
editor: ''
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 3b9a7d998e7153318b21adcada7c143b428e591f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724774"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Az Azure Functions Microsoft Graph kötései

Ez a cikk azt ismerteti, hogyan konfigurálását és a Microsoft Graph-eseményindítók és kötések az Azure Functions használatát. Ezekkel, az Azure Functions segítségével adatokat, az elemzések és a származó események dolgozhat a [Microsoft Graph](https://graph.microsoft.io).

A Microsoft Graph-bővítmény a következő kötéseket biztosít:
- Egy [auth lexikális elem bemeneti kötése](#token-input) lehetővé teszi a Microsoft Graph API-k kommunikál.
- Egy [Excel-táblázat bemeneti kötése](#excel-input) lehetővé teszi az Excel-adatok olvasása.
- Egy [Excel-táblázat kimeneti kötése](#excel-output) lehetővé teszi az Excel adatok módosítását.
- A [OneDrive fájl bemeneti kötése](#onedrive-input) lehetővé teszi, hogy olvassa a fájlokat a onedrive-ról.
- A [OneDrive fájl kimeneti kötése](#onedrive-output) lehetővé teszi a fájlokat a onedrive-on írni.
- Egy [Outlook üzenet kimeneti kötése](#outlook-output) lehetővé teszi, hogy küldjenek e-maileket az Outlook alkalmazásból.
- A gyűjtemény [Microsoft Graph webhook eseményindítók és kötések](#webhooks) lehetővé teszi a Microsoft Graph származó események reagálni.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> A Microsoft Graph kötések jelenleg előzetes verzióban érhetők az Azure Functions verziójához 2.x. Ezek funkciók verziójában nem támogatott 1.x.

## <a name="packages"></a>Csomagok

A hitelesítési jogkivonat bemeneti kötése megtalálható a [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet-csomagot. A más Microsoft Graph kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) csomag. A csomagok forráskódja van a [azure-funkciók-microsoftgraph-bővítmény](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub-tárházban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>A bővítmények beállítása

A Microsoft Graph kötések keresztül érhetők el _bővítmények kötés_. Kötési bővítmények az Azure Functions futtatókörnyezettel összetevőket. Ez a szakasz bemutatja, hogyan állíthat be a Microsoft Graph és a hitelesítési jogkivonat bővítmények.

### <a name="enabling-functions-20-preview"></a>Előzetes funkciók 2.0 engedélyezése

Csak az Azure Functions 2.0 előzetes kötés bővítmények érhetők el. 

A Functions futtatókörnyezete 2.0 előzetes verzióját használja egy függvény alkalmazás telepítésével kapcsolatos információkért lásd: [bemutatásához az Azure Functions futásidejű verziók](set-runtime-version.md).

### <a name="installing-the-extension"></a>A bővítmény telepítése

Azure-portálról egy bővítmény telepítéséhez keresse meg a sablon vagy a kötést, amely hivatkozik rá. Hozzon létre egy új függvényt, és a "Microsoft Graph" forgatókönyv kiválasztása a sablon kiválasztására szolgáló képernyő megjelenítéséhez. Válassza ki a forgatókönyvet a sablonok egyikét. Azt is megteheti egy meglévő függvény "Integráció" fülre, keresse meg és válassza ki a cikkben szereplő kötéseket.

Mindkét esetben megjelenik egy figyelmeztetés amely adja meg, hogy telepíteni kell a bővítményt. Kattintson a **telepítése** beszerzése a bővítményt. Egyes kiterjesztések csak egyszer függvény alkalmazásonkénti telepíteni kell. 

> [!Note] 
> A portál a telepítési folyamat egy fogyasztás terv akár 10 percet vehet igénybe.

Visual Studio használatakor kaphat a bővítmények telepítése [a NuGet-csomagok, az ebben a cikkben felsorolt](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurálása a hitelesítési / engedélyezési

A cikkben ismertetett kötések használandó identitás szükséges. Ez lehetővé teszi a Microsoft Graph érvényesítése engedélyeket, és a naplózási kapcsolati. Az identitás elérésekor az alkalmazás vagy magának az alkalmazásnak a felhasználó lehet. Adja meg ezt az identitást, állítsa be [App Service hitelesítés / engedélyezés](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) az Azure Active Directoryban. Akkor is kell igényelnie a funkciók igényelnek erőforrás engedéllyel.

> [!Note] 
> A Microsoft Graph-kiterjesztés csak az Azure AD-alapú hitelesítés támogatja. Felhasználók kell a munkahelyi vagy iskolai fiókkal bejelentkezni.

Az Azure portál használata, akkor megjelenik egy figyelmeztetés, alatt a parancssorba a bővítmény telepítéséhez. A figyelmeztetés kéri, hogy konfigurálja az App Service hitelesítés / engedélyezés és a kérelem azokat az engedélyeket a sablonból vagy a kötést igényel. Kattintson a **az Azure AD konfigurálása most** vagy **engedélyek hozzáadása most** szükség szerint.



<a name="token-input"></a>
## <a name="auth-token"></a>Hitelesítési jogkivonat

A hitelesítési jogkivonat bemeneti kötése token beolvasása az Azure AD egy adott erőforráshoz, és átadja a kód karakterláncként. Az erőforrás értéke lehet bármely, amelyhez az alkalmazás engedélyekkel rendelkezik. 

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#auth-token---example)
* [Attribútumok](#auth-token---attributes)
* [Konfigurálás](#auth-token---configuration)
* [Használat](#auth-token---usage)

### <a name="auth-token---example"></a>Hitelesítési kód – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Hitelesítési jogkivonat - C# parancsfájl – példa

Az alábbi példában a felhasználói profillal kapcsolatos információk lekérése.

A *function.json* fájl a lexikális elem bemeneti kötése a HTTP-eseményindítóval határozza meg:

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

A C# parancsfájlkód egy HTTP híváshoz a Microsoft Graph használja a jogkivonatot, és az eredményt adja vissza:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Hitelesítési jogkivonat - JavaScript – példa

Az alábbi példában a felhasználói profillal kapcsolatos információk lekérése.

A *function.json* fájl a lexikális elem bemeneti kötése a HTTP-eseményindítóval határozza meg:

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

A JavaScript-kódot egy HTTP híváshoz a Microsoft Graph használja a jogkivonatot, és visszaadja az eredményt.

```js
const rp = require('request-promise');

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

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) attribútum.

### <a name="auth-token---configuration"></a>Hitelesítési jogkivonat - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `Token` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a változó nevét, a hitelesítési jogkivonat függvény a kódban használt. Lásd: [hitelesítési token használatával bemeneti kötése kódból](#token-input-code).|
|**type**||Szükséges – kell állítani `token`.|
|**direction**||Szükséges – kell állítani `in`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> -Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code> -A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> -A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> -Identitást a függvény alkalmazás használja.</li></ul>|
|**userId**|**Felhasználói azonosítóját**  |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**UserToken**|**UserToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**Erőforrás**|**resource**|Szükséges – az Azure AD forrás URL-cím, amelynek a token vonatkozó kérelem.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Hitelesítési jogkivonat - használat

A kötés maga nem szükséges minden Azure AD engedélyt, de attól függően, hogy hogyan használja a tokent, szükség lehet további engedélyek kéréséhez. Ellenőrizze az erőforrás eléréséhez a jogkivonatok szeretné követelményeinek.

A token mindig karakterláncként jelenik meg a kódot.




<a name="excel-input"></a>
## <a name="excel-input"></a>Excel bemeneti

Az Excel-táblázat bemeneti kötése a onedrive-on tárolt Excel-táblázat tartalmának beolvasása.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#excel-input---example)
* [Attribútumok](#excel-input---attributes)
* [Konfigurálás](#excel-input---configuration)
* [Használat](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel bemenet – C# parancsfájl – példa

A következő *function.json* fájl határozza meg a HTTP-eseményindítóval bemeneti Excel-kötést:

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

A következő C# parancsfájlkód beolvassa a megadott tábla tartalmát, és azokat a felhasználó vissza:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel - bemeneti JavaScript – példa

A következő *function.json* fájl határozza meg a HTTP-eseményindítóval bemeneti Excel-kötést:

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

A következő JavaScript-kód beolvassa a megadott tábla tartalmát, és visszaadja azokat a felhasználó.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel bemenet – attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribútum.

### <a name="excel-input---configuration"></a>Excel-bemenet - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `Excel` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a változó nevét, az Excel-táblázat függvény a kódban használt. Lásd: [használata az Excel-táblázat bemeneti kötése kódból](#excel-input-code).|
|**type**||Szükséges – kell állítani `excel`.|
|**direction**||Szükséges – kell állítani `in`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> -Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code> -A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> -A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> -Identitást a függvény alkalmazás használja.</li></ul>|
|**userId**|**Felhasználói azonosítóját**  |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**UserToken**|**UserToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**path**|**Elérési út**|Szükséges – az Excel-munkafüzet a onedrive-on elérési útja.|
|**worksheetName**|**worksheetName**|A táblázatot tartalmazó munkalap.|
|**Táblanév**|**Táblanév**|A tábla neve. Ha nincs megadva, a munkalap tartalma lesz.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel bemeneti - használat

Ehhez a kötéshez az alábbi engedélyekkel kell rendelkeznie az Azure AD:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználók fájljainak olvasása|

A kötés mutatja meg a következő típusú .NET funkciók:
- String [] []
- Microsoft.Graph.WorkbookTable
- Egyéni objektumtípusok (strukturális modellkötést használ)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-kimenet

Az Excel kimeneti kötés módosítja a onedrive-on tárolt Excel-táblázat tartalmának.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#excel-output---example)
* [Attribútumok](#excel-output---attributes)
* [Konfigurálás](#excel-output---configuration)
* [Használat](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel kimeneti - C# parancsfájl – példa

A következő példa egy Excel-táblázat sorokat ad.

A *function.json* fájl határozza meg az Excel egy HTTP-eseményindítóval kimeneti kötése:

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

A C# parancsfájlkód hozzáadja az új sort a táblázathoz (feltételezi, hogy egy oszlop lehet) a bemeneti lekérdezési karakterlánc alapján:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
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

#### <a name="excel-output---javascript-example"></a>Excel - kimeneti JavaScript – példa

A következő példa egy Excel-táblázat sorokat ad.

A *function.json* fájl határozza meg az Excel egy HTTP-eseményindítóval kimeneti kötése:

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

### <a name="excel-output---attributes"></a>Excel - kimeneti attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribútum.

### <a name="excel-output---configuration"></a>Excel-kimenet - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `Excel` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a változó nevét, a hitelesítési jogkivonat függvény a kódban használt. Lásd: [használata az Excel-táblázat kimeneti kötése kódból](#excel-output-code).|
|**type**||Szükséges – kell állítani `excel`.|
|**direction**||Szükséges – kell állítani `out`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> -Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code> -A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> -A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> -Identitást a függvény alkalmazás használja.</li></ul>|
|**Felhasználói azonosítóját** |**userId** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**UserToken**|**UserToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**path**|**Elérési út**|Szükséges – az Excel-munkafüzet a onedrive-on elérési útja.|
|**worksheetName**|**worksheetName**|A táblázatot tartalmazó munkalap.|
|**Táblanév**|**Táblanév**|A tábla neve. Ha nincs megadva, a munkalap tartalma lesz.|
|**updateType**|**Frissítés típusa**|Szükséges – olyan változást kíván végezni a táblában. A következő értékek egyike lehet:<ul><li><code>update</code> -Váltja fel a onedrive-ban a tábla tartalmát.</li><li><code>append</code> -Ad hozzá a tartalom a onedrive-ban a tábla végéhez hozzon létre új sort.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel kimeneti - használat

Ehhez a kötéshez az alábbi engedélyekkel kell rendelkeznie az Azure AD:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználó fájljaihoz|

A kötés mutatja meg a következő típusú .NET funkciók:
- String [] []
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Egyéni objektumtípusok (strukturális modellkötést használ)





<a name="onedrive-input"></a>
## <a name="file-input"></a>A bemeneti fájl

A onedrive vállalati verzió fájl bemeneti kötése beolvassa a onedrive-on tárolt fájl tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#file-input---example)
* [Attribútumok](#file-input---attributes)
* [Konfigurálás](#file-input---configuration)
* [Használat](#file-input---usage)

### <a name="file-input---example"></a>Fájl bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Fájl bemenet – C# parancsfájl – példa

A következő példa egy fájlt a onedrive-on tárolt olvassa be.

A *function.json* fájl határozza meg a HTTP-eseményindítóval, a OneDrive-fájl bemeneti kötést:

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

A C# parancsfájlkód beolvassa a fájlt, a lekérdezési karakterláncban megadott, és annak naplói:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Bemenet – fájl JavaScript – példa

A következő példa egy fájlt a onedrive-on tárolt olvassa be.

A *function.json* fájl határozza meg a HTTP-eseményindítóval, a OneDrive-fájl bemeneti kötést:

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

A következő JavaScript-kód beolvassa a fájlt, a lekérdezési karakterláncban megadott, és annak hosszát adja vissza.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Fájl bemenet – attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribútum.

### <a name="file-input---configuration"></a>Fájl a bemeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `OneDrive` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a fájl függvény a kódban használt változó neve. Lásd: [OneDrive-fájllal bemeneti kötése kódból](#onedrive-input-code).|
|**type**||Szükséges – kell állítani `onedrive`.|
|**direction**||Szükséges – kell állítani `in`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> -Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code> -A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> -A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> -Identitást a függvény alkalmazás használja.</li></ul>|
|**userId**|**Felhasználói azonosítóját**  |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**UserToken**|**UserToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**path**|**Elérési út**|Kötelező – a onedrive-on a fájl elérési útja.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Bemeneti - fájl használata

Ehhez a kötéshez az alábbi engedélyekkel kell rendelkeznie az Azure AD:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználók fájljainak olvasása|

A kötés mutatja meg a következő típusú .NET funkciók:
- Byte]
- Stream
- karakterlánc
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Kimeneti fájl

A onedrive vállalati verzió fájl kimeneti kötés módosítja a onedrive-on tárolt fájl tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#file-output---example)
* [Attribútumok](#file-output---attributes)
* [Konfigurálás](#file-output---configuration)
* [Használat](#file-output---usage)

### <a name="file-output---example"></a>Kimeneti - fájl – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Fájl kimeneti - C# parancsfájl – példa

A következő példa a onedrive-on tárolt fájlba írja.

A *function.json* fájl határozza meg egy, a onedrive vállalati verzió HTTP-eseményindítóval kimeneti kötése:

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

A C# parancsfájlkód szöveg lekérése a lekérdezési karakterláncot, és a hívó OneDrive gyökerében írja azt egy szövegfájlt (az előző példában a FunctionsTest.txt):

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```

#### <a name="file-output---javascript-example"></a>Kimeneti - fájl JavaScript – példa

A következő példa a onedrive-on tárolt fájlba írja.

A *function.json* fájl határozza meg egy, a onedrive vállalati verzió HTTP-eseményindítóval kimeneti kötése:

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

A JavaScript-kód szöveg lekérése a lekérdezési karakterláncot, és írja azt egy szövegfájlt (a fenti konfigurációban meghatározott FunctionsTest.txt) a hívó OneDrive gyökerében.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Fájl – a kimeneti attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribútum.

### <a name="file-output---configuration"></a>Fájl kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `OneDrive` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a fájl függvény a kódban használt változó neve. Lásd: [OneDrive-fájllal kimeneti kötése kódból](#onedrive-output-code).|
|**type**||Szükséges – kell állítani `onedrive`.|
|**direction**||Szükséges – kell állítani `out`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> -Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code> -A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> -A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> -Identitást a függvény alkalmazás használja.</li></ul>|
|**Felhasználói azonosítóját** |**userId** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**UserToken**|**UserToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**path**|**Elérési út**|Kötelező – a onedrive-on a fájl elérési útja.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Kimeneti - fájl használata

Ehhez a kötéshez az alábbi engedélyekkel kell rendelkeznie az Azure AD:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználó fájljaihoz|

A kötés mutatja meg a következő típusú .NET funkciók:
- Byte]
- Stream
- karakterlánc
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook kimeneti

Az Outlook üzenet kimeneti kötése küld egy e-mail üzenetet az Outlook alkalmazásból.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#outlook-output---example)
* [Attribútumok](#outlook-output---attributes)
* [Konfigurálás](#outlook-output---configuration)
* [Használat](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Outlook kimenete – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook kimeneti - C# parancsfájl – példa

Az alábbi példa elküld egy e-mailt az Outlook alkalmazásból.

A *function.json* fájl határozza meg az Outlook egy HTTP-eseményindítóval üzenet kimeneti kötése:

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

A C# parancsfájlkód a hívó egy levelet küld a lekérdezési karakterláncban megadott címzett:

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
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

#### <a name="outlook-output---javascript-example"></a>Outlook - kimeneti JavaScript – példa

Az alábbi példa elküld egy e-mailt az Outlook alkalmazásból.

A *function.json* fájl határozza meg az Outlook egy HTTP-eseményindítóval üzenet kimeneti kötése:

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

A JavaScript-kódot a hívó egy levelet küld a lekérdezési karakterláncban megadott címzett:

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

### <a name="outlook-output---attributes"></a>Outlook kimeneti - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) attribútum.

### <a name="outlook-output---configuration"></a>Outlook kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `Outlook` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a funkciókódot szerepel az e-mail üzenetet a változó nevét. Lásd: [az Outlook-üzenet használata kimeneti kötése kódból](#outlook-output-code).|
|**type**||Szükséges – kell állítani `outlook`.|
|**direction**||Szükséges – kell állítani `out`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> -Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code> -A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> -A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> -Identitást a függvény alkalmazás használja.</li></ul>|
|**userId**|**Felhasználói azonosítóját**  |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**UserToken**|**UserToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook kimeneti - használat

Ehhez a kötéshez az alábbi engedélyekkel kell rendelkeznie az Azure AD:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználói e-maileket küldjön|

A kötés mutatja meg a következő típusú .NET funkciók:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- karakterlánc
- Egyéni objektumtípusok (strukturális modellkötést használ)






## <a name="webhooks"></a>Webhookok

Webhook lehetővé teszi a Microsoft Graph események reagálni. Webhook támogatásához funkciók szükségesek létrehozása, frissítése és reagálnak _webhook előfizetések_. A teljes webhook megoldás a következő kötések kombinációja szükséges:
- A [Microsoft Graph webhook eseményindító](#webhook-trigger) lehetővé teszi a bejövő webhook reagálni.
- A [Microsoft Graph webhook előfizetés bemeneti kötése](#webhook-input) lehetővé teszi a meglévő előfizetések listában, és opcionálisan frissítse azokat.
- A [Microsoft Graph webhook előfizetés kimeneti kötése](#webhook-output) lehetővé teszi a létrehozása vagy törlése a webhook előfizetések.

A kötések maguk nem szükséges minden Azure AD engedélyt, de kell igényelnie az erőforrástípushoz reagálni kívánja vonatkozó engedélyeket. Az egyes erőforrás szükséges engedélyek, amelyek listájáért lásd: [előfizetése engedélyei között](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

További információ a webhookok: [A Microsoft Graph webhookok használata].





## <a name="webhook-trigger"></a>Webhook trigger

A Microsoft Graph webhook eseményindító lehetővé teszi, hogy a Microsoft Graph a bejövő webhook reagálni a függvény. Az eseményindító-példányokhoz több Microsoft Graph erőforrástípus reagálni.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-trigger---example)
* [Attribútumok](#webhook-trigger---attributes)
* [Konfigurálás](#webhook-trigger---configuration)
* [Használat](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook eseményindító - C# parancsfájl – példa

A következő példában bejövő Outlook üzenetek webhookok kezeli. Használatához a webhook indul el, akkor [előfizetés létrehozása](#webhook-output---example), ráadásul [az előfizetés frissítése](#webhook-subscription-refresh) , nehogy azt a lejár.

A *function.json* fájl határozza meg a webhook eseményindító:

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

A C# parancsfájlkód reagál a beérkező e-mail üzenetek, és naplózza a címzett által küldött és a tárgy "Az Azure Functions" tartalmazó törzse:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Webhook eseményindító - JavaScript – példa

A következő példában bejövő Outlook üzenetek webhookok kezeli. Használatához a webhook indul el, akkor [előfizetés létrehozása](#webhook-output---example), ráadásul [az előfizetés frissítése](#webhook-subscription-refresh) , nehogy azt a lejár.

A *function.json* fájl határozza meg a webhook eseményindító:

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

A JavaScript-kód reagál a beérkező e-mail üzenetek, és naplózza a címzett által küldött és a tárgy "Az Azure Functions" tartalmazó törzse:

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

### <a name="webhook-trigger---attributes"></a>Webhook eseményindító - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) attribútum.

### <a name="webhook-trigger---configuration"></a>Webhook eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `GraphWebHookTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a funkciókódot szerepel az e-mail üzenetet a változó nevét. Lásd: [az Outlook-üzenet használata kimeneti kötése kódból](#outlook-output-code).|
|**type**||Szükséges – kell állítani `graphWebhook`.|
|**direction**||Szükséges – kell állítani `trigger`.|
|**resourceType**|**ResourceType**|Kötelező – a graph-erőforrás, amelynek ez a funkció válaszolnia kell webhook. A következő értékek egyike lehet:<ul><li><code>#Microsoft.Graph.Message</code> -Outlook üzenetek módosításait.</li><li><code>#Microsoft.Graph.DriveItem</code> -OneDrive legfelső szintű elemek.</li><li><code>#Microsoft.Graph.Contact</code> -az Outlook névjegyalbumba végzett módosítások.</li><li><code>#Microsoft.Graph.Event</code> -Outlook naptárelemek módosításait.</li></ul>|

> [!Note]
> Egy függvény alkalmazást csak egyetlen funkció regisztrált van egy adott `resourceType` érték.

### <a name="webhook-trigger---usage"></a>Webhook eseményindító - használat

A kötés mutatja meg a következő típusú .NET funkciók:
- Microsoft Graph SDK meg kell adnia az erőforrástípus kapcsolódik, mint `Microsoft.Graph.Message` vagy `Microsoft.Graph.DriveItem`.
- Egyéni objektumtípusok (strukturális modellkötést használ)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook bemeneti

A Microsoft Graph webhook bemeneti kötés lehetővé teszi az függvény alkalmazás által kezelt előfizetések listájából. A kötés olvassa be az alkalmazás tárolási függvény, így ezek nem feltétlenül más az alkalmazáson kívül létrehozott előfizetések.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-input---example)
* [Attribútumok](#webhook-input---attributes)
* [Konfigurálás](#webhook-input---configuration)
* [Használat](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook bemenet – C# parancsfájl – példa

Az alábbi példa lekérdezi a hívó felhasználó az összes előfizetést, és törli őket.

A *function.json* fájl egy előfizetés bemeneti kötése a HTTP-eseményindítóval határozza meg, és egy előfizetés kimeneti kötése, amely használja a delete művelet:

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

A C# parancsfájlkód lekérdezi az előfizetéseket, és törli őket:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Webhook bemenet – JavaScript – példa

Az alábbi példa lekérdezi a hívó felhasználó az összes előfizetést, és törli őket.

A *function.json* fájl egy előfizetés bemeneti kötése a HTTP-eseményindítóval határozza meg, és egy előfizetés kimeneti kötése, amely használja a delete művelet:

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

A JavaScript-kód lekérdezi az előfizetéseket, és törli őket:

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

### <a name="webhook-input---attributes"></a>Webhook bemenet – attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attribútum.

### <a name="webhook-input---configuration"></a>Webhook bemenet - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `GraphWebHookSubscription` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a funkciókódot szerepel az e-mail üzenetet a változó nevét. Lásd: [az Outlook-üzenet használata kimeneti kötése kódból](#outlook-output-code).|
|**type**||Szükséges – kell állítani `graphWebhookSubscription`.|
|**direction**||Szükséges – kell állítani `in`.|
|**filter**|**Szűrő**| Ha beállítása `userFromRequest`, akkor a kötés csak be fogja olvasni a hívó felhasználó által birtokolt előfizetések (csak érvényes [HTTP-eseményindítóval]).| 

### <a name="webhook-input---usage"></a>Webhook bemeneti - használat

A kötés mutatja meg a következő típusú .NET funkciók:
- String]
- Egyéni objektum típusú tömbök
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Webhook kimeneti

A webhook előfizetés kimeneti kötés létrehozása, törlése és a frissítés a Microsoft Graph webhook előfizetések segítségével.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-output---example)
* [Attribútumok](#webhook-output---attributes)
* [Konfigurálás](#webhook-output---configuration)
* [Használat](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook kimenete – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook kimeneti - C# parancsfájl – példa

A következő példa egy előfizetést hoz létre. Is [az előfizetés frissítése](#webhook-subscription-refresh) , nehogy azt a lejár.

A *function.json* fájl határozza meg a HTTP-eseményindítóval, egy előfizetés kimenet kötés a létrehozási művelet segítségével:

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

A C# parancsfájlkód regisztrálja a webhook, amely a függvény alkalmazás értesítést, ha a hívó felhasználó kap egy Outlook üzenet:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Webhook kimeneti - JavaScript – példa

A következő példa egy előfizetést hoz létre. Is [az előfizetés frissítése](#webhook-subscription-refresh) , nehogy azt a lejár.

A *function.json* fájl határozza meg a HTTP-eseményindítóval, egy előfizetés kimenet kötés a létrehozási művelet segítségével:

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

A JavaScript-kód regisztrálja a webhook, amely a függvény alkalmazás értesítést, ha a hívó felhasználó kap egy Outlook üzenet:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook kimeneti - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) attribútum.

### <a name="webhook-output---configuration"></a>Webhook kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `GraphWebHookSubscription` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**name**||Kötelező – a funkciókódot szerepel az e-mail üzenetet a változó nevét. Lásd: [az Outlook-üzenet használata kimeneti kötése kódból](#outlook-output-code).|
|**type**||Szükséges – kell állítani `graphWebhookSubscription`.|
|**direction**||Szükséges – kell állítani `out`.|
|**Identitás**|**Identitáskezelés**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code> -Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code> -A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code> -A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code> -Identitást a függvény alkalmazás használja.</li></ul>|
|**userId**|**Felhasználói azonosítóját**  |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**UserToken**|**UserToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**A művelet**|**Művelet**|Kötelező – meghatározza a kötés műveletet végre kell hajtania. A következő értékek egyike lehet:<ul><li><code>create</code> – Egy új előfizetést regisztrál.</li><li><code>delete</code> -A megadott előfizetés törlése.</li><li><code>refresh</code> -A megadott előfizetés biztosítható, hogy lejárjanak frissíti.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Ha szükséges, és csak akkor, ha a _művelet_ értéke `create`. Adja meg a Microsoft Graph-erőforrás a változásokat a figyelendő. Lásd: [A Microsoft Graph webhookok használata]. |
|**changeType**|**ChangeType**|Ha szükséges, és csak akkor, ha a _művelet_ értéke `create`. Az értesítés emeli előfizetett erőforrás módosítása típusát jelöli. A támogatott értékek a következők: `created`, `updated`, `deleted`. Több érték is használható együtt, vesszővel tagolt lista használatával.|

### <a name="webhook-output---usage"></a>Webhook kimeneti - használat

A kötés mutatja meg a következő típusú .NET funkciók:
- karakterlánc
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Webhook előfizetés frissítése

Az előfizetések frissítése kétféleképpen történhet:

- Az Alkalmazásidentitás segítségével az összes előfizetés kezelésére. Ehhez szükséges engedélye nélkül az Azure Active Directory-rendszergazda segítségét. Ez az Azure Functions által támogatott összes nyelven használható.
- Az egyes előfizetések társított minden egyes felhasználói azonosítóját. Ha manuálisan identitás használatára Ehhez szükséges néhány egyéni kód végrehajtja a kötést. Ez csak a .NET-funkciók által használható.

Ez a szakasz példa ezen módszerek mindegyikéhez:

* [Alkalmazás identitását – példa](#webhook-subscription-refresh---app-identity-example)
* [Felhasználói azonosító – példa](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook előfizetés frissítése - alkalmazás identitását – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Alkalmazás identitás frissítési - C# parancsfájl – példa

A következő példa egy előfizetés frissítése az alkalmazás identitását használja.

A *function.json* határozza meg az előfizetés egy időzítő indítófeltételt bemeneti kötése és az előfizetés kimeneti kötése:

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

A C# parancsfájlkód az előfizetések frissítése:

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Alkalmazás identitás frissítési - C# parancsfájl – példa

A következő példa egy előfizetés frissítése az alkalmazás identitását használja.

A *function.json* határozza meg az előfizetés egy időzítő indítófeltételt bemeneti kötése és az előfizetés kimeneti kötése:

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

A JavaScript-kódot az előfizetések frissítése:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Webhook előfizetés frissítése – felhasználói identitás – példa

Az alábbi példában a felhasználói identitás előfizetés frissítése.

A *function.json* fájl egy időzítő indítófeltételt határozza meg, és eltér a funkciókódot előfizetés bemeneti kötése:

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

A C# parancsfájlkód frissíti az előfizetéseket, és hozza létre a kimeneti kötése a kód, minden felhasználói azonosító megadása:

```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
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
            log.Info($"Refreshing subscription {subscription}");
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
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

[HTTP-eseményindítóval]: functions-bindings-http-webhook.md
[A Microsoft Graph webhookok használata]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
