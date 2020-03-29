---
title: Microsoft Graph kötések az Azure Functionshez
description: Ismerje meg, hogyan használhatja a Microsoft Graph eseményindítókat és kötéseket az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 770187693e5bac6e059dfd20455099fcc695b74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715033"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph kötések az Azure Functionshez

Ez a cikk bemutatja, hogyan konfigurálhatja és dolgozhat a Microsoft Graph eseményindítók és kötések az Azure Functionsben. Ezekkel az Azure Functions segítségével dolgozhat a [Microsoft Graph](https://developer.microsoft.com/graph)ból származó adatokkal, elemzésekkel és eseményekkel.

A Microsoft Graph bővítmény a következő kötéseket biztosítja:
- Az [auth token bemeneti kötés](#token-input) lehetővé teszi, hogy bármely Microsoft Graph API-val kommunikáljon.
- Az [Excel-tábla bemeneti kötése](#excel-input) lehetővé teszi az adatok excelből történő olvasását.
- Az [Excel-tábla kimeneti kötése](#excel-output) lehetővé teszi az Excel-adatok módosítását.
- A [OneDrive-fájlbemeneti kötés](#onedrive-input) lehetővé teszi fájlok olvasását a OneDrive-ról.
- A [OneDrive-fájlkimeneti kötés](#onedrive-output) lehetővé teszi, hogy fájlokba írjon a OneDrive-on.
- Az [Outlook-üzenetek kimeneti kötése](#outlook-output) lehetővé teszi, hogy e-maileket küldjön az Outlookon keresztül.
- A [Microsoft Graph webhook-eseményindítók és -kötések](#webhooks) gyűjteménye lehetővé teszi, hogy reagáljon a Microsoft Graph eseményeire.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> A Microsoft Graph-kötések jelenleg előzetes verzióban vannak az Azure Functions 2.x-es vagy újabb verziójához. Ezeket a Functions 1.x-es verziója nem támogatja.

## <a name="packages"></a>Csomagok

Az auth token bemeneti kötésa a [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet csomagban található. A többi Microsoft Graph-kötés a [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) csomagban található. A csomagok forráskódja az [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub-repository.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>A bővítmények beállítása

A Microsoft Graph kötések _kötési kiterjesztéseken_keresztül érhetők el. A kötési bővítmények az Azure Functions futásidejű opcionális összetevői. Ez a szakasz bemutatja, hogyan állíthatja be a Microsoft Graph és az auth token kiterjesztéseket.

### <a name="enabling-functions-20-preview"></a>A Functions 2.0 előzetes verziójának engedélyezése

A kötésbővítmények csak az Azure Functions 2.0 előzetes verziójához érhetők el. 

A függvényalkalmazások functions futásidejű verziójának preview 2.0-s verziójának beállításáról az [Azure Functions futásidejű verzióinak célzása](set-runtime-version.md)című témakörben talál további információt.

### <a name="installing-the-extension"></a>A bővítmény telepítése

Bővítmény telepítése az Azure Portalról, keresse meg a sablonvagy kötés, amely hivatkozik rá. Hozzon létre egy új függvényt, és a sablonkijelölési képernyőn válassza a "Microsoft Graph" forgatókönyvet. Válassza ki az egyik sablont ebből a forgatókönyvből. Másik lehetőségként megnyithatja egy meglévő függvény "Integrálás" lapját, és kiválaszthatja a cikkben szereplő kötések egyikét.

Mindkét esetben megjelenik egy figyelmeztetés, amely meghatározza a telepítendő bővítményt. A bővítmény beszerzéséhez kattintson a **Telepítés** gombra. Minden bővítményt csak egyszer kell telepíteni függvényalkalmazásonként. 

> [!Note] 
> A portálon keresztüli telepítési folyamat akár 10 percet is igénybe vehet egy felhasználási tervben.

Ha visual studiót használ, a bővítményeket a [cikk korábbi részén felsorolt NuGet csomagok](#packages)telepítésével szerezheti be.

### <a name="configuring-authentication--authorization"></a>Hitelesítés konfigurálása / engedélyezés

Az ebben a cikkben ismertetett kötések identitást igényelnek. Ez lehetővé teszi, hogy a Microsoft Graph engedélyeket és naplózási műveleteket kényszerítsen ki. Az identitás lehet az alkalmazáshoz vagy magához az alkalmazáshoz hozzáférő felhasználó. Az identitás konfigurálásához állítsa be [az App Service hitelesítése / engedélyezése az](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) Azure Active Directoryval. A függvények által igényelt erőforrás-engedélyeket is kérnie kell.

> [!Note] 
> A Microsoft Graph bővítmény csak az Azure AD-hitelesítést támogatja. A felhasználóknak munkahelyi vagy iskolai fiókkal kell bejelentkezniük.

Ha az Azure Portalon, megjelenik egy figyelmeztetés alatt a figyelmeztetést a bővítmény telepítésére. A figyelmeztetés az App Service hitelesítése / engedélyezése konfigurálására és a sablon vagy a kötés által igényelt engedélyek kérésére kéri. Kattintson **az Azure AD konfigurálása most** vagy engedélyek hozzáadása **most** a megfelelő.



<a name="token-input"></a>
## <a name="auth-token"></a>Hitelesítési token

Az auth token bemeneti kötelező egy Azure AD-jogkivonatot kap egy adott erőforráshoz, és a kódot karakterláncként biztosítja. Az erőforrás bármelyik lehet, amelyhez az alkalmazás rendelkezik engedélyekkel. 

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#auth-token---example)
* [Attribútumok](#auth-token---attributes)
* [Konfiguráció](#auth-token---configuration)
* [Használat](#auth-token---usage)

### <a name="auth-token---example"></a>Hitelesítési token - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#auth-token---c-script-example)
* [Javascript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Auth token - C# script példa

A következő példa felhasználói profiladatokat kap.

A *function.json* fájl egy jogkivonatbemeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A C# parancsfájlkód a jogkivonatot használja a Microsoft Graph HTTP-hívásához, és az eredményt adja vissza:

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

#### <a name="auth-token---javascript-example"></a>Hitelesítési token - JavaScript példa

A következő példa felhasználói profiladatokat kap.

A *function.json* fájl egy jogkivonatbemeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A JavaScript-kód a jogkivonatot használja a Microsoft Graph HTTP-hívásának meghívásához, és visszaadja az eredményt.

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

### <a name="auth-token---attributes"></a>Hitelesítési token - attribútumok

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) attribútumot.

### <a name="auth-token---configuration"></a>Hitelesítési token - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Token` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – az auth token függvénykódjában használt változónév. Lásd: [Hitelesítési token bemeneti kötés](#token-input-code)használata a kódból .|
|**Típus**| n/a |Kötelező - a `token`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `in`beállításnak a beállítására kell.|
|**Identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. Az alábbi értékek egyike lehet:<ul><li><code>userFromRequest</code>- Csak [HTTP triggerrel]érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code>- Egy korábban bejelentkezett felhasználó identitását használja a megadott azonosítóval. Nézze <code>userId</code> meg az ingatlant.</li><li><code>userFromToken</code>- A megadott jogkivonat által képviselt identitást használja. Nézze <code>userToken</code> meg az ingatlant.</li><li><code>clientCredentials</code>- A függvényalkalmazás identitását használja.</li></ul>|
|**Userid**|**Userid**  |Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromId` Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználóazonosító.|
|**userToken**|**UserToken**|Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromToken` A függvényalkalmazásra érvényes jogkivonat. |
|**Erőforrás**|**Erőforrás**|Kötelező – Egy Azure AD-erőforrás URL-címét, amelyhez a jogkivonatot kérik.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Hitelesítési token - használat

Maga a kötés nem igényel Azure AD-engedélyeket, de a ttól függően, hogy a jogkivonat ot hogyan használják, előfordulhat, hogy további engedélyeket kell kérnie. Ellenőrizze a jogkivonattal elérni kívánt erőforrás követelményeit.

A jogkivonat mindig karakterláncként jelenik meg a kódban.

> [!Note]
> Ha helyileg fejleszti `userFromId` `userFromToken` valamelyik `userFromRequest` , vagy a lehetőségek, a szükséges `X-MS-TOKEN-AAD-ID-TOKEN` jogkivonat ot lehet [beszerezni manuálisan,](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) és megadható a kérelem fejlécében egy hívó ügyfélalkalmazás.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-bevitel

Az Excel-táblázat bemeneti kötése beolvassa a OneDrive-ban tárolt Excel-táblázat tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#excel-input---example)
* [Attribútumok](#excel-input---attributes)
* [Konfiguráció](#excel-input---configuration)
* [Használat](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-bevitel - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#excel-input---c-script-example)
* [Javascript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel-bevitel - C# parancsfájl példa

A következő *function.json* fájl egy Excel bemeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A következő C# parancsfájlbeolvassa a megadott tábla tartalmát, és visszaadja azokat a felhasználónak:

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

#### <a name="excel-input---javascript-example"></a>Excel-bevitel – Példa JavaScript

A következő *function.json* fájl egy Excel bemeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

### <a name="excel-input---attributes"></a>Excel-bevitel - attribútumok

A [C# osztálytárakban](functions-dotnet-class-library.md)használja az [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribútumot.

### <a name="excel-input---configuration"></a>Excel-bemenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Excel` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – az Excel-tábla függvénykódjában használt változónév. Lásd: [Excel-tábla bemeneti kötés használata a kódból](#excel-input-code).|
|**Típus**| n/a |Kötelező - a `excel`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `in`beállításnak a beállítására kell.|
|**Identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. Az alábbi értékek egyike lehet:<ul><li><code>userFromRequest</code>- Csak [HTTP triggerrel]érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code>- Egy korábban bejelentkezett felhasználó identitását használja a megadott azonosítóval. Nézze <code>userId</code> meg az ingatlant.</li><li><code>userFromToken</code>- A megadott jogkivonat által képviselt identitást használja. Nézze <code>userToken</code> meg az ingatlant.</li><li><code>clientCredentials</code>- A függvényalkalmazás identitását használja.</li></ul>|
|**Userid**|**Userid**  |Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromId` Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználóazonosító.|
|**userToken**|**UserToken**|Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromToken` A függvényalkalmazásra érvényes jogkivonat. |
|**Elérési út**|**Elérési út**|Kötelező – a OneDrive-on az Excel-munkafüzethez vezető elérési út.|
|**munkalapneve**|**Munkalapneve**|Az a munkalap, amelyben a tábla található.|
|**táblaneve**|**TableName**|A tábla neve. Ha nincs megadva, a munkalap tartalmát fogja használni.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel-bevitel - használat

Ez a kötés a következő Azure AD-engedélyeket igényli:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználói fájlok olvasása|

A kötés a következő típusokat teszi ki a .NET függvényeknek:
- karakterlánc[][]
- Microsoft.Graph.MunkafüzetTábla
- Egyéni objektumtípusok (szerkezeti modellkötéshasználatával)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-kimenet

Az Excel kimeneti kötése módosítja a OneDrive-ban tárolt Excel-táblázat tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#excel-output---example)
* [Attribútumok](#excel-output---attributes)
* [Konfiguráció](#excel-output---configuration)
* [Használat](#excel-output---usage)

### <a name="excel-output---example"></a>Excel kimenet - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#excel-output---c-script-example)
* [Javascript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel kimenet - C# script példa

A következő példa sorokat ad hozzá egy Excel-táblázathoz.

A *function.json* fájl egy Excel kimeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A C# parancsfájlkód új sort ad a táblához (amelyet egyoszloposnak feltételeznek) a lekérdezési karakterlánc bemenete alapján:

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

#### <a name="excel-output---javascript-example"></a>Excel kimenet - Példa JavaScript

A következő példa sorokat ad hozzá egy Excel-táblázathoz.

A *function.json* fájl egy Excel kimeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A következő JavaScript-kód új sort ad hozzá a táblához (amely egyoszloposnak számít) a lekérdezési karakterlánc bemenete alapján.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel kimenet - attribútumok

A [C# osztálytárakban](functions-dotnet-class-library.md)használja az [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) attribútumot.

### <a name="excel-output---configuration"></a>Excel kimenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Excel` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – az auth token függvénykódjában használt változónév. Lásd: [Excel-tábla kimeneti kötéshasználata a kódból](#excel-output-code).|
|**Típus**| n/a |Kötelező - a `excel`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `out`beállításnak a beállítására kell.|
|**Identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. Az alábbi értékek egyike lehet:<ul><li><code>userFromRequest</code>- Csak [HTTP triggerrel]érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code>- Egy korábban bejelentkezett felhasználó identitását használja a megadott azonosítóval. Nézze <code>userId</code> meg az ingatlant.</li><li><code>userFromToken</code>- A megadott jogkivonat által képviselt identitást használja. Nézze <code>userToken</code> meg az ingatlant.</li><li><code>clientCredentials</code>- A függvényalkalmazás identitását használja.</li></ul>|
|**Userid** |**Userid** |Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromId` Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználóazonosító.|
|**userToken**|**UserToken**|Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromToken` A függvényalkalmazásra érvényes jogkivonat. |
|**Elérési út**|**Elérési út**|Kötelező – a OneDrive-on az Excel-munkafüzethez vezető elérési út.|
|**munkalapneve**|**Munkalapneve**|Az a munkalap, amelyben a tábla található.|
|**táblaneve**|**TableName**|A tábla neve. Ha nincs megadva, a munkalap tartalmát fogja használni.|
|**updateType (updateType)**|**UpdateType (UpdateType)**|Kötelező – a tábla módosításának típusa. Az alábbi értékek egyike lehet:<ul><li><code>update</code>- Lecseréli a táblázat tartalmát a OneDrive-on.</li><li><code>append</code>- Új sorok létrehozásával hozzáadja a tartalom a táblázat végéhez a OneDrive-ban.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel kimenet - használat

Ez a kötés a következő Azure AD-engedélyeket igényli:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználói fájlokhoz|

A kötés a következő típusokat teszi ki a .NET függvényeknek:
- karakterlánc[][]
- Newtonsoft.Json.Linq.JTárgy
- Microsoft.Graph.MunkafüzetTábla
- Egyéni objektumtípusok (szerkezeti modellkötéshasználatával)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Fájlbemenet

A OneDrive-fájlbemeneti kötés beolvassa a OneDrive-ban tárolt fájlok tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#file-input---example)
* [Attribútumok](#file-input---attributes)
* [Konfiguráció](#file-input---configuration)
* [Használat](#file-input---usage)

### <a name="file-input---example"></a>Fájlbevitel - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#file-input---c-script-example)
* [Javascript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Fájlbevitel - C# parancsfájl példa

A következő példa a OneDrive-on tárolt fájlt olvassa be.

A *function.json* fájl onedrive-fájlbemeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A C# parancsfájlkód beolvassa a lekérdezési karakterláncban megadott fájlt, és naplózza annak hosszát:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Fájlbevitel – Példa JavaScript

A következő példa a OneDrive-on tárolt fájlt olvassa be.

A *function.json* fájl onedrive-fájlbemeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A következő JavaScript-kód beolvassa a lekérdezési karakterláncban megadott fájlt, és visszaadja annak hosszát.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Fájlbemenet - attribútumok

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribútumot.

### <a name="file-input---configuration"></a>Fájlbemenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `OneDrive` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – a fájl függvénykódjában használt változónév. Lásd: [OneDrive-fájlbemeneti kötés használata a kódból](#onedrive-input-code).|
|**Típus**| n/a |Kötelező - a `onedrive`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `in`beállításnak a beállítására kell.|
|**Identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. Az alábbi értékek egyike lehet:<ul><li><code>userFromRequest</code>- Csak [HTTP triggerrel]érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code>- Egy korábban bejelentkezett felhasználó identitását használja a megadott azonosítóval. Nézze <code>userId</code> meg az ingatlant.</li><li><code>userFromToken</code>- A megadott jogkivonat által képviselt identitást használja. Nézze <code>userToken</code> meg az ingatlant.</li><li><code>clientCredentials</code>- A függvényalkalmazás identitását használja.</li></ul>|
|**Userid**|**Userid**  |Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromId` Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználóazonosító.|
|**userToken**|**UserToken**|Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromToken` A függvényalkalmazásra érvényes jogkivonat. |
|**Elérési út**|**Elérési út**|Kötelező – a OneDrive elérési útja a fájlhoz.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Fájlbemenet - használat

Ez a kötés a következő Azure AD-engedélyeket igényli:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Felhasználói fájlok olvasása|

A kötés a következő típusokat teszi ki a .NET függvényeknek:
- bájt[]
- Stream
- sztring
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Fájl kimenete

A OneDrive-fájlkimeneti kötés módosítja a OneDrive-ban tárolt fájlok tartalmát.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#file-output---example)
* [Attribútumok](#file-output---attributes)
* [Konfiguráció](#file-output---configuration)
* [Használat](#file-output---usage)

### <a name="file-output---example"></a>Fájlkimenet - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#file-output---c-script-example)
* [Javascript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Fájlkimenet - C# parancsfájl példa

A következő példa a OneDrive-on tárolt fájlba ír.

A *function.json* fájl onedrive kimeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A C# parancsfájl szöveget kap a lekérdezési karakterláncból, és egy szövegfájlba (functionstest.txt az előző példában meghatározott) írja a hívó OneDrive-jának gyökerében:

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

#### <a name="file-output---javascript-example"></a>Fájlkimenet - Példa JavaScript

A következő példa a OneDrive-on tárolt fájlba ír.

A *function.json* fájl onedrive kimeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A JavaScript-kód beveszi a szöveget a lekérdezési karakterláncból, és egy szövegfájlba (functionstest.txt a fenti konfigurációban meghatározott) írja a hívó OneDrive-jának gyökerében.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Fájlkimenet - attribútumok

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) attribútumot.

### <a name="file-output---configuration"></a>Fájlkimenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `OneDrive` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – a fájl függvénykódjában használt változónév. Lásd: [OneDrive-fájlkimeneti kötés használata a kódból](#onedrive-output-code).|
|**Típus**| n/a |Kötelező - a `onedrive`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `out`beállításnak a beállítására kell.|
|**Identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. Az alábbi értékek egyike lehet:<ul><li><code>userFromRequest</code>- Csak [HTTP triggerrel]érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code>- Egy korábban bejelentkezett felhasználó identitását használja a megadott azonosítóval. Nézze <code>userId</code> meg az ingatlant.</li><li><code>userFromToken</code>- A megadott jogkivonat által képviselt identitást használja. Nézze <code>userToken</code> meg az ingatlant.</li><li><code>clientCredentials</code>- A függvényalkalmazás identitását használja.</li></ul>|
|**Userid** |**Userid** |Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromId` Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználóazonosító.|
|**userToken**|**UserToken**|Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromToken` A függvényalkalmazásra érvényes jogkivonat. |
|**Elérési út**|**Elérési út**|Kötelező – a OneDrive elérési útja a fájlhoz.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Fájlkimenet - használat

Ez a kötés a következő Azure AD-engedélyeket igényli:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|Teljes hozzáférés a felhasználói fájlokhoz|

A kötés a következő típusokat teszi ki a .NET függvényeknek:
- bájt[]
- Stream
- sztring
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-kimenet

Az Outlook-üzenet kimeneti kötése e-mailt küld az Outlook programon keresztül.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#outlook-output---example)
* [Attribútumok](#outlook-output---attributes)
* [Konfiguráció](#outlook-output---configuration)
* [Használat](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook kimenet - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#outlook-output---c-script-example)
* [Javascript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook-kimenet - C# parancsfájl példa

A következő példa e-mailt küld az Outlookon keresztül.

A *function.json* fájl egy Outlook-üzenetkimeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A C# parancsfájlkód a hívó féltől küld e-mailt a lekérdezési karakterláncban megadott címzettnek:

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

#### <a name="outlook-output---javascript-example"></a>Outlook-kimenet – Példa JavaScript

A következő példa e-mailt küld az Outlookon keresztül.

A *function.json* fájl egy Outlook-üzenetkimeneti kötéssel rendelkező HTTP-eseményindítót határoz meg:

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

A JavaScript-kód a hívó féltől a lekérdezési karakterláncban megadott címzettnek küld e-mailt:

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

### <a name="outlook-output---attributes"></a>Outlook kimenet - attribútumok

A [C# osztálytárakban](functions-dotnet-class-library.md)használja az [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) attribútumot.

### <a name="outlook-output---configuration"></a>Outlook kimenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Outlook` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – az e-mail függvénykódjában használt változónév. Lásd: [Outlook-üzenet kimeneti kötés használata a kódból](#outlook-output-code).|
|**Típus**| n/a |Kötelező - a `outlook`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `out`beállításnak a beállítására kell.|
|**Identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. Az alábbi értékek egyike lehet:<ul><li><code>userFromRequest</code>- Csak [HTTP triggerrel]érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code>- Egy korábban bejelentkezett felhasználó identitását használja a megadott azonosítóval. Nézze <code>userId</code> meg az ingatlant.</li><li><code>userFromToken</code>- A megadott jogkivonat által képviselt identitást használja. Nézze <code>userToken</code> meg az ingatlant.</li><li><code>clientCredentials</code>- A függvényalkalmazás identitását használja.</li></ul>|
|**Userid**|**Userid**  |Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromId` Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználóazonosító.|
|**userToken**|**UserToken**|Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromToken` A függvényalkalmazásra érvényes jogkivonat. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook-kimenet - használat

Ez a kötés a következő Azure AD-engedélyeket igényli:

|Erőforrás|Engedély|
|--------|--------|
|Microsoft Graph|E-mail küldése felhasználóként|

A kötés a következő típusokat teszi ki a .NET függvényeknek:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JTárgy
- sztring
- Egyéni objektumtípusok (szerkezeti modellkötéshasználatával)






## <a name="webhooks"></a>Webhookok

A webhookok lehetővé teszik, hogy reagáljon a Microsoft Graph eseményeire. A webhookok támogatásához funkciókra van szükség a _webhook-előfizetések_létrehozásához, frissítéséhez és az azokra való reagáláshoz. A teljes webhook-megoldás hoz a következő kötések kombinációját igényli:
- A [Microsoft Graph webhook-eseményindító](#webhook-trigger) lehetővé teszi, hogy reagáljon a bejövő webhook.
- A [Microsoft Graph webhook előfizetési bemeneti kötés](#webhook-input) lehetővé teszi a meglévő előfizetések listázását és szükség esetén frissíteni őket.
- A [Microsoft Graph webhook-előfizetési kimeneti kötés](#webhook-output) lehetővé teszi webhook-előfizetések létrehozását vagy törlését.

Maguk a kötések nem igényelnek Azure AD-engedélyeket, de az erőforrástípushoz kapcsolódó engedélyeket kell kérnie, amelyekre reagálni kíván. Az egyes erőforrástípusokhoz szükséges engedélyek listáját az előfizetési engedélyek című [témakörben tetszés szerint.](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0)

A webhookokról a [Webhookok kezelése a Microsoft Graph ban című témakörben]talál további információt.





## <a name="webhook-trigger"></a>Webhook-eseményindító

A Microsoft Graph webhook-eseményindító lehetővé teszi, hogy egy függvény reagáljon a Microsoft Graph bejövő webhookjára. Ennek az eseményindítónak minden példánya egy Microsoft Graph-erőforrástípusra reagálhat.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-trigger---example)
* [Attribútumok](#webhook-trigger---attributes)
* [Konfiguráció](#webhook-trigger---configuration)
* [Használat](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook-eseményindító - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#webhook-trigger---c-script-example)
* [Javascript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook-eseményindító - C# parancsfájl példa

A következő példa kezeli a webhookokat a bejövő Outlook-üzenetekhez. Webhook-eseményindító használatához [hozzon létre egy előfizetést,](#webhook-output---example)és [frissítheti az előfizetést,](#webhook-subscription-refresh) hogy megakadályozza annak lejáratát.

A *function.json* fájl webhook-eseményindítót határoz meg:

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

A C# parancsfájlkód reagál a bejövő e-mail üzenetekre, és naplózza a címzett által küldött és a tárgyban "Azure Functions" tartalmazó törzset:

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

#### <a name="webhook-trigger---javascript-example"></a>Webhook-eseményindító - JavaScript példa

A következő példa kezeli a webhookokat a bejövő Outlook-üzenetekhez. Webhook-eseményindító használatához [hozzon létre egy előfizetést,](#webhook-output---example)és [frissítheti az előfizetést,](#webhook-subscription-refresh) hogy megakadályozza annak lejáratát.

A *function.json* fájl webhook-eseményindítót határoz meg:

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

A JavaScript-kód reagál a bejövő e-mail üzenetekre, és naplózza a címzett által küldött és a tárgyban "Azure Functions" (Azure Functions) tartalmazó törzset:

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

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [GraphWebhookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) attribútumot.

### <a name="webhook-trigger---configuration"></a>Webhook-eseményindító - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `GraphWebhookTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – az e-mail függvénykódjában használt változónév. Lásd: [Outlook-üzenet kimeneti kötés használata a kódból](#outlook-output-code).|
|**Típus**| n/a |Kötelező - a `graphWebhook`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `trigger`beállításnak a beállítására kell.|
|**resourceType típus**|**ResourceType**|Kötelező – az a gráferőforrás, amelynek ez a függvény nek válaszolnia kell a webhookokra. Az alábbi értékek egyike lehet:<ul><li><code>#Microsoft.Graph.Message</code>- az Outlook-üzenetek módosítása.</li><li><code>#Microsoft.Graph.DriveItem</code>- a OneDrive gyökérelemeinek módosításai.</li><li><code>#Microsoft.Graph.Contact</code>- változások a személyes kapcsolatok az Outlook.</li><li><code>#Microsoft.Graph.Event</code>- az Outlook naptárelemeinek módosítása.</li></ul>|

> [!Note]
> Egy függvényalkalmazás csak egy olyan függvényt `resourceType` használhat, amely egy adott értékhez van regisztrálva.

### <a name="webhook-trigger---usage"></a>Webhook-eseményindító - használat

A kötés a következő típusokat teszi ki a .NET függvényeknek:
- Az erőforrástípushoz kapcsolódó Microsoft Graph SDK-típusok, például `Microsoft.Graph.Message` vagy `Microsoft.Graph.DriveItem`a.
- Egyéni objektumtípusok (szerkezeti modellkötéshasználatával)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook bemenet

A Microsoft Graph webhook bemeneti kötés lehetővé teszi, hogy lekérje a függvényalkalmazás által kezelt előfizetések listáját. A kötés beolvassa a függvényalkalmazás-tárolóból, így nem tükrözi az alkalmazáson kívülről létrehozott más előfizetéseket.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-input---example)
* [Attribútumok](#webhook-input---attributes)
* [Konfiguráció](#webhook-input---configuration)
* [Használat](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook bemenet - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#webhook-input---c-script-example)
* [Javascript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook-bemenet - C# parancsfájl példa

A következő példa lekéri a hívó felhasználó összes előfizetését, és törli azokat.

A *function.json* fájl egy HTTP-eseményindítót határoz meg egy előfizetési bemeneti kötéssel és egy előfizetési kimeneti kötéssel, amely a törlési műveletet használja:

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

A C# parancsfájl kód leveszi az előfizetéseket, és törli őket:

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

#### <a name="webhook-input---javascript-example"></a>Webhook bemenet - JavaScript példa

A következő példa lekéri a hívó felhasználó összes előfizetését, és törli azokat.

A *function.json* fájl egy HTTP-eseményindítót határoz meg egy előfizetési bemeneti kötéssel és egy előfizetési kimeneti kötéssel, amely a törlési műveletet használja:

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

A JavaScript-kód megkapja az előfizetéseket, és törli őket:

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

### <a name="webhook-input---attributes"></a>Webhook bemenet - attribútumok

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) attribútumot.

### <a name="webhook-input---configuration"></a>Webhook bemenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `GraphWebhookSubscription` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – az e-mail függvénykódjában használt változónév. Lásd: [Outlook-üzenet kimeneti kötés használata a kódból](#outlook-output-code).|
|**Típus**| n/a |Kötelező - a `graphWebhookSubscription`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `in`beállításnak a beállítására kell.|
|**Szűrő**|**Szűrő**| Ha `userFromRequest`a beállítás a , akkor a kötés csak a hívó felhasználó tulajdonában lévő előfizetéseket olvassa be (csak [HTTP-eseményindítóval]érvényes).| 

### <a name="webhook-input---usage"></a>Webhook bemenet - használat

A kötés a következő típusokat teszi ki a .NET függvényeknek:
- karakterlánc[]
- Egyéni objektumtípus-tömbök
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Webhook kimenet

A webhook-előfizetés kimeneti kötése lehetővé teszi webhook-előfizetések létrehozását, törlését és frissítését a Microsoft Graph-ban.

Ez a szakasz a következő alszakaszokat tartalmazza:

* [Példa](#webhook-output---example)
* [Attribútumok](#webhook-output---attributes)
* [Konfiguráció](#webhook-output---configuration)
* [Használat](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook kimenet - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#webhook-output---c-script-example)
* [Javascript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook kimenet - C# parancsfájl példa

A következő példa létrehoz egy előfizetést. Az [előfizetés frissítésével](#webhook-subscription-refresh) megakadályozhatja annak lejáratát.

A *function.json* fájl egy HTTP-eseményindítót határoz meg egy előfizetési kimeneti kötéssel a létrehozási művelet használatával:

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

A C# parancsfájlkód regisztrál egy webhookot, amely értesíti ezt a függvényalkalmazást, amikor a hívó felhasználó Outlook-üzenetet kap:

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

#### <a name="webhook-output---javascript-example"></a>Webhook kimenet - JavaScript példa

A következő példa létrehoz egy előfizetést. Az [előfizetés frissítésével](#webhook-subscription-refresh) megakadályozhatja annak lejáratát.

A *function.json* fájl egy HTTP-eseményindítót határoz meg egy előfizetési kimeneti kötéssel a létrehozási művelet használatával:

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

A JavaScript-kód regisztrál egy webhookot, amely értesíti ezt a függvényalkalmazást, amikor a hívó felhasználó Outlook-üzenetet kap:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook kimenet - attribútumok

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) attribútumot.

### <a name="webhook-output---configuration"></a>Webhook kimenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `GraphWebhookSubscription` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**név**| n/a |Kötelező – az e-mail függvénykódjában használt változónév. Lásd: [Outlook-üzenet kimeneti kötés használata a kódból](#outlook-output-code).|
|**Típus**| n/a |Kötelező - a `graphWebhookSubscription`beállításnak a beállítására kell.|
|**direction**| n/a |Kötelező - a `out`beállításnak a beállítására kell.|
|**Identitás**|**Identitás**|Kötelező – a művelet végrehajtásához használt identitás. Az alábbi értékek egyike lehet:<ul><li><code>userFromRequest</code>- Csak [HTTP triggerrel]érvényes. A hívó felhasználó identitását használja.</li><li><code>userFromId</code>- Egy korábban bejelentkezett felhasználó identitását használja a megadott azonosítóval. Nézze <code>userId</code> meg az ingatlant.</li><li><code>userFromToken</code>- A megadott jogkivonat által képviselt identitást használja. Nézze <code>userToken</code> meg az ingatlant.</li><li><code>clientCredentials</code>- A függvényalkalmazás identitását használja.</li></ul>|
|**Userid**|**Userid**  |Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromId` Egy korábban bejelentkezett felhasználóhoz társított egyszerű felhasználóazonosító.|
|**userToken**|**UserToken**|Akkor és csak akkor _szükséges,_ ha az identitás a beállítás . `userFromToken` A függvényalkalmazásra érvényes jogkivonat. |
|**Akció**|**Művelet**|Kötelező - megadja a kötés által végrehajtott műveletet. Az alábbi értékek egyike lehet:<ul><li><code>create</code>- Új előfizetést regisztrál.</li><li><code>delete</code>- Egy adott előfizetés törlése.</li><li><code>refresh</code>- Egy megadott előfizetés frissítése, hogy megakadályozza a lejáratát.</li></ul>|
|**előfizetéserőforrás**|**Előfizetéserőforrás**|Akkor és csak akkor _szükséges,_ ha a művelet beállítása `create`. Megadja azt a Microsoft Graph erőforrást, amelyet a rendszer a módosítások figyelése érdekében figyel. Lásd: [Webhookok kezelése a Microsoft Graphban]. |
|**changeType (típus módosítása)**|**Változástípusa**|Akkor és csak akkor _szükséges,_ ha a művelet beállítása `create`. A feliratkozott erőforrás azon változásának típusát jelzi, amely értesítést fog kelteni. A támogatott értékek `created` `updated`a `deleted`következők: , , . Több érték kombinálható vesszővel tagolt listával.|

### <a name="webhook-output---usage"></a>Webhook kimenet - használat

A kötés a következő típusokat teszi ki a .NET függvényeknek:
- sztring
- Microsoft.Graph.Előfizetés




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Webhook-előfizetés frissítése

Az előfizetések frissítésének két megközelítése van:

- Használja az alkalmazás identitását az összes előfizetés kezelésére. Ehhez egy Azure Active Directory-rendszergazda hozzájárulása szükséges. Ezt az Azure Functions által támogatott összes nyelv használhatja.
- Az egyes előfizetésekhez társított identitást az egyes felhasználói azonosítók manuális kötésével használja. Ehhez a kötés végrehajtásához néhány egyéni kódra lesz szükség. Ezt csak a .NET függvények használhatják.

Ez a szakasz egy példát tartalmaz az alábbi megközelítések mindegyikére:

* [Példa alkalmazásidentitásra](#webhook-subscription-refresh---app-identity-example)
* [Példa felhasználói identitásra](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook Előfizetés frissítése – alkalmazásidentitási példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Alkalmazásidentitás frissítése - C# parancsfájl példa

A következő példa az alkalmazásidentitás t használja az előfizetés frissítéséhez.

A *function.json* egy időzítő-eseményindítót határoz meg egy előfizetési bemeneti kötéssel és egy előfizetés kimeneti kötésével:

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

A C# parancsfájlkód frissíti az előfizetéseket:

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

### <a name="app-identity-refresh---c-script-example"></a>Alkalmazásidentitás frissítése - C# parancsfájl példa

A következő példa az alkalmazásidentitás t használja az előfizetés frissítéséhez.

A *function.json* egy időzítő-eseményindítót határoz meg egy előfizetési bemeneti kötéssel és egy előfizetés kimeneti kötésével:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Webhook Előfizetés frissítése – felhasználói identitás példa

A következő példa a felhasználói identitás t használja az előfizetés frissítéséhez.

A *function.json* fájl egy időzítő eseményindítót határoz meg, és elhalasztja az előfizetés bemeneti kötését a függvénykódhoz:

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

A C# parancsfájlkód frissíti az előfizetéseket, és létrehozza a kimeneti kötést a kódban, az egyes felhasználók identitását használva:

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
> [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)

[HTTP-eseményindító]: functions-bindings-http-webhook.md
[Webhookok kezelése a Microsoft Graphban]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
