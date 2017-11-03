---
title: "Az Azure Functions Microsoft Graph-kötések |} Microsoft Docs"
description: "A Microsoft Graph-eseményindítók és kötések az Azure Functions használatának megismerése."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.openlocfilehash: 8cf2e4e9e9007549dbdc931b4485c4230c536479
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-microsoft-graph-bindings"></a>Az Azure Functions Microsoft Graph-kötések
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk azt ismerteti, hogyan konfigurálását és a Microsoft Graph-eseményindítók és kötések az Azure Functions használatát.
Ezekkel, az Azure Functions segítségével adatokat, az elemzések és a származó események dolgozhat a [Microsoft Graph](https://graph.microsoft.io).

A Microsoft Graph-bővítmény a következő kötéseket biztosít:
- Egy [auth lexikális elem bemeneti kötése](#token-input) lehetővé teszi a Microsoft Graph API-k kommunikál.
- Egy [Excel-táblázat bemeneti kötése](#excel-input) lehetővé teszi az Excel-adatok olvasása.
- Egy [Excel-táblázat kimeneti kötése](#excel-output) lehetővé teszi az Excel adatok módosítását.
- Egy [OneDrive fájl bemeneti kötése](#onedrive-input) lehetővé teszi, hogy olvassa a fájlokat a onedrive-ról.
- Egy [OneDrive fájl kimeneti kötése](#onedrive-output) lehetővé teszi a fájlokat a onedrive-on írni.
- Egy [Outlook üzenet kimeneti kötése](#outlook-output) lehetővé teszi, hogy küldjenek e-maileket az Outlook alkalmazásból.
- A gyűjtemény [Microsoft Graph webhook eseményindítók és kötések](#webhooks) lehetővé teszi a Microsoft Graph származó események reagálni.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> A Microsoft Graph kötések még csak előzetes verziójúak.

## <a name="setting-up-the-extensions"></a>A bővítmények beállítása

A Microsoft Graph kötések keresztül érhetők el _bővítmények kötés_. Kötési bővítmények az Azure Functions futtatókörnyezettel összetevőket. Ez a szakasz bemutatja, hogyan állíthat be a Microsoft Graph és a hitelesítési jogkivonat bővítmények.

### <a name="enabling-functions-20-preview"></a>Előzetes funkciók 2.0 engedélyezése

Csak az Azure Functions 2.0 előzetes kötés bővítmények csak érhetők el. 

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

További tudnivalókért lásd: [bemutatásához az Azure Functions futásidejű verziók](functions-versions.md).

### <a name="installing-the-extension"></a>A bővítmény telepítése

Azure-portálról egy bővítmény telepítéséhez szükséges keresse meg a sablon vagy a kötés, amely hivatkozik rá. Hozzon létre egy új függvényt, és a "Microsoft Graph" forgatókönyv kiválasztása a sablon kiválasztására szolgáló képernyő megjelenítéséhez. Válassza ki a forgatókönyvet a sablonok egyikét. Azt is megteheti keresse meg a meglévő funkcióinak "Integráció" fülre, és válassza ki az ebben a témakörben ismertetett kötések.

Mindkét esetben megjelenik egy figyelmeztetés amely adja meg, hogy telepíteni kell a bővítményt. Kattintson a **telepítése** beszerzése a bővítményt.

> [!Note] 
> Egyes kiterjesztések csak egyszer függvény alkalmazásonkénti telepíteni kell. A portál a telepítési folyamat egy fogyasztás terv akár 10 percet vehet igénybe.

Ha a Visual Studio használ, a bővítmények kaphat a NuGet-csomagok telepítése:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>Konfigurálása az App Service hitelesítés / engedélyezés

Az ebben a témakörben ismertetett módon kötések használandó identitás szükséges. Ez lehetővé teszi a Microsoft Graph érvényesítése engedélyeket, és a naplózási kapcsolati. Az identitás elérésekor az alkalmazás vagy magának az alkalmazásnak a felhasználó lehet. Ezzel az identitással konfigurálásához szüksége lesz beállítása [App Service hitelesítés / engedélyezés](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) az Azure Active Directoryban. Akkor is kell igényelnie a funkciók igényelnek erőforrás engedéllyel.

> [!Note] 
> A Microsoft Graph-kiterjesztés csak az AAD-hitelesítés támogatja. Felhasználók kell a munkahelyi vagy iskolai fiókkal bejelentkezni.

Ha az Azure portálon, a parancssorba a bővítmény telepítéséhez alatt jelenik meg egy figyelmeztetés, amely megkéri, hogy az App Service hitelesítés konfigurálása / engedélyezés és a kérelem azokat az engedélyeket a sablonból vagy a kötést igényel. Kattintson a **AAD konfigurálása most** vagy **engedélyek hozzáadása most** szükség szerint.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>Hitelesítési lexikális elem bemeneti kötése

A kötés egy AAD token lekérdezi a megadott erőforrás, és átadja a kód karakterláncként. Az erőforrás értéke lehet bármely, amelyhez az alkalmazás engedélyekkel rendelkezik. 

### <a name="configuring-an-auth-token-input-binding"></a>Egy hitelesítési lexikális elem bemeneti kötése konfigurálása

Maga a kötés nem szükséges minden AAD engedélyt, de attól függően, hogy hogyan használja a tokent, szükség lehet további engedélyek kéréséhez. Ellenőrizze az erőforrás eléréséhez a jogkivonatok szeretné követelményeinek.

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a változó nevét, a hitelesítési jogkivonat függvény a kódban használt. Lásd: [hitelesítési token használatával bemeneti kötése kódból](#token-input-code).|
|**típusa**|Szükséges – kell állítani `token`.|
|**iránya**|Szükséges – kell állítani `in`.|
|**identitás**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code>-Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code>-A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code>-A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code>-Identitást a függvény alkalmazás használja.</li></ul>|
|**felhasználói azonosítóját** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**erőforrás**|Szükséges – egy AAD erőforrás URL-CÍMÉT, amelynek a token vonatkozó kérelem.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>Egy hitelesítési lexikális elem bemeneti kötése kódból használatával

A token mindig karakterláncként jelenik meg a kódot.

#### <a name="sample-getting-user-profile-information"></a>Példa: Felhasználói profil adatainak lekérése

Tegyük fel, a következő function.json a lexikális elem bemeneti kötése a HTTP-eseményindítóval definiáló:

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

Az alábbi C# minta egy HTTP híváshoz a Microsoft Graph használja a jogkivonatot, és az eredményt adja vissza:

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

A következő JS-mintát használja a jogkivonatot egy HTTP híváshoz a Microsoft Graph, és visszaadja az eredményt. Az a `function.json` újabb módosítása `$return` való `res` első.

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





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Excel-táblázat bemeneti kötése

A kötés a onedrive-on tárolt Excel-táblázat tartalmának beolvasása.

### <a name="configuring-an-excel-table-input-binding"></a>Egy Excel-táblázat bemeneti kötése konfigurálása

A kötés a következő AAD engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználói fájlok olvasása|

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a változó nevét, az Excel-táblázat függvény a kódban használt. Lásd: [használata az Excel-táblázat bemeneti kötése kódból](#excel-input-code).|
|**típusa**|Szükséges – kell állítani `excel`.|
|**iránya**|Szükséges – kell állítani `in`.|
|**identitás**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code>-Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code>-A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code>-A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code>-Identitást a függvény alkalmazás használja.</li></ul>|
|**felhasználói azonosítóját** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**elérési út**|Szükséges – az Excel-munkafüzet a onedrive-on elérési útja.|
|**worksheetName**|A táblázatban található a munkalapon.|
|**Táblanév**|A tábla neve. Ha nincs megadva, a munkalap tartalma lesz.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>Az Excel bemeneti táblakötéssel kódból használatával

A kötés mutatja meg a következő típusú .NET funkciók:
- String [] []
- Microsoft.Graph.WorkbookTable
- Egyéni objektumtípusok (strukturális modellkötést használ)

#### <a name="sample-reading-an-excel-table"></a>Példa: Excel-táblázat olvasása

Tegyük fel, a következő function.json egy HTTP-eseményindítóval bemeneti Excel-kötést definiáló:

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

Az alábbi C# minta beolvassa a megadott tábla tartalmát, és visszaadja azokat a felhasználó hozzáadása:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

A következő JS minta ad hozzá, beolvassa a megadott tábla tartalmát, és visszaadja azokat a felhasználó számára. Az a `function.json` újabb módosítása `$return` való `res` első.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Excel-táblázat kimeneti kötése

A kötés a onedrive-on tárolt Excel-táblázat tartalmának módosítása.

### <a name="configuring-an-excel-table-output-binding"></a>Excel-táblázat konfigurálása kimeneti kötése

A kötés a következő AAD engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználó fájljaihoz|

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a változó nevét, a hitelesítési jogkivonat függvény a kódban használt. Lásd: [használata az Excel-táblázat kimeneti kötése kódból](#excel-output-code).|
|**típusa**|Szükséges – kell állítani `excel`.|
|**iránya**|Szükséges – kell állítani `out`.|
|**identitás**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code>-Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code>-A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code>-A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code>-Identitást a függvény alkalmazás használja.</li></ul>|
|**felhasználói azonosítóját** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**elérési út**|Szükséges – az Excel-munkafüzet a onedrive-on elérési útja.|
|**worksheetName**|A táblázatban található a munkalapon.|
|**Táblanév**|A tábla neve. Ha nincs megadva, a munkalap tartalma lesz.|
|**frissítés típusa**|Szükséges – olyan változást kíván végezni a táblában. A következő értékek egyike lehet:<ul><li><code>update</code>-Váltja fel a onedrive-ban a tábla tartalmát.</li><li><code>append</code>-Ad hozzá a tartalom a onedrive-ban a tábla végéhez hozzon létre új sort.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>Excel-táblázat segítségével kimeneti kötése kódból

A kötés mutatja meg a következő típusú .NET funkciók:
- String [] []
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Egyéni objektumtípusok (strukturális modellkötést használ)

#### <a name="sample-adding-rows-to-an-excel-table"></a>Példa: Sorok hozzáadása egy Excel-táblázat

Tegyük fel, hogy a következő, amely meghatározza egy HTTP-eseményindítóval, az Excel function.json kimeneti kötése:

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


Az alábbi C# minta hozzáadja az új sort a táblázathoz (feltételezi, hogy egy oszlop lehet) a bemeneti lekérdezési karakterlánc alapján:

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

A következő JS-minta hozzáadja a bemeneti lekérdezési karakterlánc alapján egy új sort a táblázathoz (feltételezni, hogy egy oszlop). Az a `function.json` újabb módosítása `$return` való `res` első.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>Onedrive vállalati verzió fájl bemeneti kötése

A kötés beolvassa a onedrive-on tárolt fájl tartalmát.

### <a name="configuring-a-onedrive-file-input-binding"></a>A onedrive vállalati verzió fájl bemeneti kötése konfigurálása

A kötés a következő AAD engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználói fájlok olvasása|

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a fájl függvény a kódban használt változó neve. Lásd: [OneDrive-fájllal bemeneti kötése kódból](#onedrive-input-code).|
|**típusa**|Szükséges – kell állítani `onedrive`.|
|**iránya**|Szükséges – kell állítani `in`.|
|**identitás**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code>-Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code>-A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code>-A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code>-Identitást a függvény alkalmazás használja.</li></ul>|
|**felhasználói azonosítóját** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**elérési út**|Kötelező – a onedrive-on a fájl elérési útja.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>A onedrive vállalati verzió fájl bemeneti kötés kódból használata

A kötés mutatja meg a következő típusú .NET funkciók:
- Byte]
- Az adatfolyam
- Karakterlánc
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>Példa: A fájl olvasásához a onedrive-ról

Tegyük fel, a következő function.json egy HTTP-eseményindítóval OneDrive bemeneti kötést definiáló:

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

Az alábbi C# minta beolvassa a fájlt, a lekérdezési karakterláncban megadott, és annak naplói:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

A következő JS minta beolvassa a fájlt, a lekérdezési karakterláncban megadott, és annak hosszát adja vissza. Az a `function.json` újabb módosítása `$return` való `res` első.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>Onedrive vállalati verzió fájl kimeneti kötése

A kötés módosítja a onedrive-on tárolt fájl tartalmát.

### <a name="configuring-a-onedrive-file-output-binding"></a>A onedrive vállalati verzió konfigurálásával fájl kimeneti kötése

A kötés a következő AAD engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználó fájljaihoz|

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a fájl függvény a kódban használt változó neve. Lásd: [OneDrive-fájllal kimeneti kötése kódból](#onedrive-output-code).|
|**típusa**|Szükséges – kell állítani `onedrive`.|
|**iránya**|Szükséges – kell állítani `out`.|
|**identitás**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code>-Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code>-A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code>-A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code>-Identitást a függvény alkalmazás használja.</li></ul>|
|**felhasználói azonosítóját** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**elérési út**|Kötelező – a onedrive-on a fájl elérési útja.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>A onedrive vállalati verzió használatával fájl kimeneti kötése kódból

A kötés mutatja meg a következő típusú .NET funkciók:
- Byte]
- Az adatfolyam
- Karakterlánc
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>Példa: A onedrive-on a fájl írása

Tegyük fel, hogy a következő function.json, amely meghatározza egy HTTP-eseményindítóval, a onedrive vállalati verzió a kimeneti kötése:

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

Az alábbi C# minta szöveges lekérése a lekérdezési karakterláncot, és írja azt egy szövegfájlt (a fenti konfigurációban meghatározott FunctionsTest.txt) a hívó OneDrive gyökerében:

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
A következő JS minta szöveg lekérése a lekérdezési karakterláncot, és a hívó OneDrive gyökerében egy szövegfájlt (a fenti konfigurációs FunctionsTest.txt) írja azt. Az a `function.json` újabb módosítása `$return` való `res` első.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Outlook üzenet kimeneti kötése

Az Outlook alkalmazásból e-mail üzenetet küld.

### <a name="configuring-an-outlook-message-output-binding"></a>Az Outlook üzenet konfigurálása kimeneti kötése

A kötés a következő AAD engedélyekkel kell rendelkeznie:
|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználói e-maileket küldjön|

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a funkciókódot szerepel az e-mail üzenetet a változó nevét. Lásd: [az Outlook-üzenet használata kimeneti kötése kódból](#outlook-output-code).|
|**típusa**|Szükséges – kell állítani `outlook`.|
|**iránya**|Szükséges – kell állítani `out`.|
|**identitás**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code>-Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code>-A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code>-A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code>-Identitást a függvény alkalmazás használja.</li></ul>|
|**felhasználói azonosítóját** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>Az Outlook-üzenet használata kimeneti kötése kódból

A kötés mutatja meg a következő típusú .NET funkciók:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- Karakterlánc
- Egyéni objektumtípusok (strukturális modellkötést használ)

#### <a name="sample-sending-an-email-through-outlook"></a>Példa: Küld egy e-mailt az Outlook alkalmazásból

Tegyük fel, hogy a következő, amely meghatározza egy HTTP-eseményindítóval egy Outlook üzenettel function.json kimeneti kötése:

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

Az alábbi C# minta a hívó egy levelet küld a lekérdezési karakterláncban megadott címzett:

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

A következő JS-minta a hívó egy levelet küld a lekérdezési karakterláncban megadott címzett:

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





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>A Microsoft Graph webhook kötések

Webhook lehetővé teszi a Microsoft Graph események reagálni. Webhook támogatásához funkciók szükségesek létrehozása, frissítése és reagálnak _webhook előfizetések_. A teljes webhook megoldás a következő kötések kombinációja lesz szükség:
- A [Microsoft Graph webhook eseményindító](#webhook-trigger) lehetővé teszi a bejövő webhook reagálni.
- A [Microsoft Graph webhook előfizetés bemeneti kötése](#webhook-input) lehetővé teszi a meglévő előfizetések listában, és opcionálisan frissítse azokat.
- A [Microsoft Graph webhook előfizetés kimeneti kötése](#webhook-output) lehetővé teszi a létrehozása vagy törlése a webhook előfizetések.

Magukat a kötések nem igényelnek AAD engedéllyel, de az erőforrástípushoz reagálni kívánja vonatkozó engedélyek kéréséhez szüksége lesz. Az egyes erőforrás szükséges engedélyek, amelyek listájáért lásd: [előfizetése engedélyei között](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

További információ a webhookok beállításáról lásd: [használata a Microsoft Graph webhookok].





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>A Microsoft Graph webhook eseményindító

Ehhez az eseményindítóhoz lehetővé teszi, hogy a Microsoft Graph a bejövő webhook reagálni a függvény. Az eseményindító-példányokhoz több Microsoft Graph erőforrástípus reagálni.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>A Microsoft Graph webhook eseményindító konfigurálása

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a funkciókódot szerepel az e-mail üzenetet a változó nevét. Lásd: [az Outlook-üzenet használata kimeneti kötése kódból](#outlook-output-code).|
|**típusa**|Szükséges – kell állítani `graphWebhook`.|
|**iránya**|Szükséges – kell állítani `trigger`.|
|**a resourceType**|Kötelező – a graph-erőforrás, amelynek ez a funkció válaszolnia kell webhook. A következő értékek egyike lehet:<ul><li><code>#Microsoft.Graph.Message</code>-Outlook üzenetek módosításait.</li><li><code>#Microsoft.Graph.DriveItem</code>-OneDrive legfelső szintű elemek.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> A függvény az alkalmazás csak is rendelkezik egy olyan funkció, amely a regisztrált egy adott `resourceType` érték.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>A Microsoft Graph webhook eseményindító kódból használatával

A kötés mutatja meg a következő típusú .NET funkciók:
- Az erőforráshoz Microsoft Graph SDK típusok írja be, például Microsoft.Graph.Message, Microsoft.Graph.DriveItem
- Egyéni objektumtípusok (strukturális modellkötést használ)

A kötést használó kódban példákért lásd: [Microsoft Graph webhook minták](#webhook-samples).



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>A Microsoft Graph webhook előfizetés bemeneti kötése

A kötés lehetővé teszi az függvény alkalmazás által kezelt előfizetések listájából. A kötés olvassa be az alkalmazás tárolási függvény, és nem tükrözi az alkalmazáson kívül létrehozott más előfizetések.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>A Microsoft Graph webhook előfizetés bemeneti kötése konfigurálása

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a funkciókódot szerepel az e-mail üzenetet a változó nevét. Lásd: [az Outlook-üzenet használata kimeneti kötése kódból](#outlook-output-code).|
|**típusa**|Szükséges – kell állítani `graphWebhookSubscription`.|
|**iránya**|Szükséges – kell állítani `in`.|
|**szűrő**| Ha beállítása `userFromRequest`, akkor a kötés csak be fogja olvasni a hívó felhasználó által birtokolt előfizetések (csak érvényes [HTTP-eseményindítóval]).| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>Egy Microsoft Graph webhook előfizetés bemeneti kötés kódból

A kötés mutatja meg a következő típusú .NET funkciók:
- String]
- Egyéni objektum típusú tömbök
- Newtonsoft.Json.Linq.JObject]
- Microsoft.Graph.Subscription]

A kötést használó kódban példákért lásd: [Microsoft Graph webhook minták](#webhook-samples).


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>A Microsoft Graph webhook előfizetés kimeneti kötése

A kötés lehetővé teszi létrehozása, törlése és a Microsoft Graph webhook előfizetések frissítése.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Előfizetés konfigurálása a Microsoft Graph webhook kimeneti kötése

A kötés támogatja-e a következő tulajdonságokkal:

|Tulajdonság|Leírás|
|--------|--------|
|**név**|Kötelező – a funkciókódot szerepel az e-mail üzenetet a változó nevét. Lásd: [az Outlook-üzenet használata kimeneti kötése kódból](#outlook-output-code).|
|**típusa**|Szükséges – kell állítani `graphWebhookSubscription`.|
|**iránya**|Szükséges – kell állítani `out`.|
|**identitás**|Szükséges – az identitás, amely a művelet végrehajtásához használható. A következő értékek egyike lehet:<ul><li><code>userFromRequest</code>-Csak érvényes [HTTP-eseményindítóval]. A hívó felhasználó használja.</li><li><code>userFromId</code>-A korábban bejelentkezett felhasználó használ, a megadott. Tekintse meg a <code>userId</code> tulajdonság.</li><li><code>userFromToken</code>-A megadott jogkivonat által képviselt identitást használja. Tekintse meg a <code>userToken</code> tulajdonság.</li><li><code>clientCredentials</code>-Identitást a függvény alkalmazás használja.</li></ul>|
|**felhasználói azonosítóját** |Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromId`. Egy egyszerű társított felhasználói azonosító egy korábban bejelentkezett felhasználó.|
|**userToken**|Ha szükséges, és csak akkor, ha _identitás_ értéke `userFromToken`. A függvény alkalmazásra érvényes jogkivonat. |
|**a művelet**|Kötelező – meghatározza a kötés műveletet végre kell hajtania. A következő értékek egyike lehet:<ul><li><code>create</code>– Egy új előfizetést regisztrál.</li><li><code>delete</code>-A megadott előfizetés törlése.</li><li><code>refresh</code>-A megadott előfizetés biztosítható, hogy lejárjanak frissíti.</li></ul>|
|**subscriptionResource**|Ha szükséges, és csak akkor, ha a _művelet_ értéke `create`. Adja meg a Microsoft Graph-erőforrás a változásokat a figyelendő. Lásd: [használata a Microsoft Graph webhookok]. |
|**changeType**|Ha szükséges, és csak akkor, ha a _művelet_ értéke `create`. Az értesítés emeli előfizetett erőforrás módosítása típusát jelöli. A támogatott értékek a következők: `created`, `updated`, `deleted`. Több érték is használható együtt, vesszővel tagolt lista használatával.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>A Microsoft Graph webhook használatával előfizetés kimeneti kötése kódból

A kötés mutatja meg a következő típusú .NET funkciók:
- Karakterlánc
- Microsoft.Graph.Subscription

A kötést használó kódban példákért lásd: [Microsoft Graph webhook minták](#webhook-samples).
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>A Microsoft Graph webhook minták

#### <a name="sample-creating-a-subscription"></a>Példa: Előfizetés létrehozása

Tegyük fel, a következő function.json, amely meghatározza egy HTTP-eseményindítóval egy előfizetés kimenet kötés a létrehozási művelet segítségével:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
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

Az alábbi C# minta regisztrálja a webhook, amely a függvény alkalmazás értesítést, ha a hívó felhasználó kap egy Outlook üzenet:

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

A következő JS minta regisztrálja a webhook, amely a függvény alkalmazás értesítést, ha a hívó felhasználó kap egy Outlook üzenet:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>Példa: Értesítések kezelése

Tegyük fel, a következő function.json, amely meghatározza a Graph webhook eseményindító Outlook üzenetek kezeléséhez:

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

Az alábbi C# minta reagál a beérkező e-mail üzenetek, és naplózza a címzett által küldött és a tárgy "Az Azure Functions" tartalmazó törzse:

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

A következő JS minta reagál a beérkező e-mail üzenetek, és naplózza a címzett által küldött és a tárgy "Az Azure Functions" tartalmazó törzse:

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

#### <a name="sample-deleting-subscriptions"></a>Példa: Előfizetések törlése

Tegyük fel, a következő function.json, amely meghatározza egy HTTP-eseményindítóval egy előfizetés bemeneti kötése és egy előfizetés kimeneti kötése a delete művelet segítségével:

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

Az alábbi C# minta lekérdezi a hívó felhasználó az összes előfizetést, és végül törli őket:

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

A következő JS minta lekérdezi az összes előfizetést a hívó felhasználó, és végül törli őket:

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

#### <a name="sample-refreshing-subscriptions"></a>Példa: Előfizetések frissítése

Az előfizetések frissítése kétféleképpen történhet:
- Az Alkalmazásidentitás segítségével az összes előfizetés kezelésére. Ehhez szükséges engedélye nélkül az Azure Active Directory-rendszergazda segítségét. Ez az Azure Functions által támogatott összes nyelven használható.
- Az egyes előfizetések társított minden egyes felhasználói azonosítóját. Ha manuálisan identitás használatára Ehhez szükséges néhány egyéni kód végrehajtja a kötést. Ez csak a .NET-funkciók által használható.

Mindkét lehetőség alább láthatók.

**Az alkalmazás azonosítójának használatával**

Tegyük fel, a következő function.json, amely meghatározza egy időzítő indítófeltételt egy előfizetés bemeneti kötése előfizetés kimenet kötés, az alkalmazás identitásával:

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

Az alábbi C# minta frissíti az előfizetések időzítő, az alkalmazás identitásával:

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

A következő JS minta frissíti az előfizetések időzítő, az alkalmazás identitásával:

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

**Dinamikus felhasználói azonosítók használata**

Az alternatív lehetőségnél tegyük fel, hogy a következő function.json, amely meghatározza egy időzítő indítófeltételt van az előfizetés-kötése késleltető bemeneti kötése a funkciókódot:

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

Az alábbi C# minta időzítő, minden felhasználói azonosító használata a kódban a kimeneti kötés létrehozásával előfizetések frissítése:
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



[HTTP-eseményindítóval]: functions-bindings-http-webhook.md
[használata a Microsoft Graph webhookok]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
