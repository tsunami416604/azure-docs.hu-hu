---
title: "Az Azure Functions JavaScript-fejlesztői útmutatója |} Microsoft Docs"
description: "Megtudhatja, hogyan fejleszthet függvények JavaScript használatával."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 1aaeeed2740179555c024792562a950f4fd6b29d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-javascript-developer-guide"></a>Az Azure Functions JavaScript fejlesztői útmutató
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

Az Azure Functions JavaScript élményét megkönnyíti, hogy egy függvénynek, amely szerint átadott exportálása egy `context` objektum kommunikál a futtatókörnyezet és fogadása és adatküldés kötéseken keresztül.

Ez a cikk feltételezi, hogy Ön már elolvasta a [Azure Functions fejlesztői segédanyagai](functions-reference.md).

## <a name="exporting-a-function"></a>Egy függvény exportálása
Minden JavaScript-funkcióként kell exportálnia az egyetlen `function` keresztül `module.exports` található a függvény, majd futtassa a futási időben. Ez a funkció mindig tartalmaznia kell egy `context` objektum.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

A kötések `direction === "in"` függvény argumentumaként, ami azt jelenti, amelyeket felhasználhat továbbítódnak [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) dinamikusan kezelje az új bemeneti adatokat (például használatával `arguments.length` az ismétlés a bemeneti adatok). Ez a funkció esetén kényelmes csak egy eseményindító és nincs további bevitel, mivel nélkül hivatkozó kiszámítható hozzáfér az eseményindítóra vonatkozó információt a `context` objektum.

Az argumentumok mindig továbbítódnak a sorrendben történik a függvény *function.json*, még akkor is, ha nem adja meg azokat a kivitel utasításban. Például, ha rendelkezik `function(context, a, b)` és módosítsa úgy, hogy `function(context, a)`, továbbra is használhatja a értékének `b` szakaszra függvény kódban `arguments[2]`.

Összes kötését, irány, attól függetlenül is továbbítódnak a `context` (lásd a következő parancsfájl) objektum. 

## <a name="context-object"></a>környezeti objektumot
A futtatókörnyezet használ egy `context` objektum és a függvény az adatok, és lehetővé teszik, hogy a futtatókörnyezet kommunikálni.

A `context` objektum mindig a függvény az első paraméter, és szerepelnie kell függvénykötésnek mert metódusok, mint `context.done` és `context.log`, amelyek ahhoz, hogy a futtatókörnyezet helyesen használni. Nevezze el az lehet az objektum függetlenül szeretné (például `ctx` vagy `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>Context.Bindings tulajdonság

```
context.bindings
```
Egy elnevezett objektum beállítása/beolvasása, amely a bemeneti és kimeneti adatokat tartalmaz. Például a következő kötés meghatározást a *function.json* megadható, hogy a várólista tartalmának eléréséhez a `context.bindings.myInput` objektum. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>Context.Done módszer
```
context.done([err],[propertyBag])
```

Figyelmeztet, hogy a kód befejezte a futtatókörnyezet. Meg kell hívnia `context.done`, vagy ellenkező esetben a futásidejű soha nem tudja, hogy a függvény befejeződött, és végrehajtása időtúllépést okoz. 

A `context.done` módszer lehetővé teszi a átadása vissza mindkét egy felhasználó által definiált hiba történt a futtatókörnyezet és tulajdonságai egy tulajdonságcsomagot, amelyek felülírják a tulajdonságok a `context.bindings` objektum.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Context.log módszer  

```
context.log(message)
```
Lehetővé teszi a adatfolyam, a nyomkövetési szint konzolnaplófájlokban írni. A `context.log`, további naplózási módszerek elérhetőek, amelyek lehetővé teszik a konzol naplófájlba más nyomkövetési szinten:


| Módszer                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **Hiba (_üzenet_)**   | Naplózás vagy alacsonyabb hibaszintet ír.   |
| **Figyelmeztetés (_üzenet_)**    | Figyelmeztetési szintű naplózás vagy alacsonyabb ír. |
| **Info (_üzenet_)**    | Információ szintet naplózás vagy alacsonyabb ír.    |
| **részletes (_üzenet_)** | A részletes szint naplózás ír.           |

A következő példa ír a figyelmeztetési nyomkövetési szint a konzolhoz:

```javascript
context.log.warn("Something has happened."); 
```
Állítsa be a nyomkövetési szintű küszöbértéke a host.json fájl van bejelentkezve, vagy kapcsolja ki.  A naplókba írásával kapcsolatban további információkért tekintse meg a következő szakaszban.

## <a name="binding-data-type"></a>Kötési adattípus

Egy bemeneti kötése adattípusát megadásához használja a `dataType` tulajdonság kötése definíciójában. Olvassa el a tartalom HTTP-kérések bináris formátumú, például típust használjon `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Más beállításokat a `dataType` vannak `stream` és `string`.

## <a name="writing-trace-output-to-the-console"></a>A konzol nyomkövetés írása 

A Funkciók, használja a `context.log` módszerek nyomkövetés írni a konzolt. Ezen a ponton nem használható `console.log` írni a konzolt.

A hívás esetén `context.log()`, az üzenetet a nyomkövetési szint, amely a konzol ír a _info_ nyomkövetési szint. A következő kódot ír az információ a nyomkövetési szintet a konzolhoz:

```javascript
context.log({hello: 'world'});  
```

Az előzőekben látható kód értéke megegyezik a következő kódot:

```javascript
context.log.info({hello: 'world'});  
```

A következő kódot ír a hibaszintet a konzolhoz:

```javascript
context.log.error("An error has occurred.");  
```

Mivel _hiba_ a legmagasabb nyomkövetési szint a nyomkövetési ír a kimeneti minden nyomkövetési szintű mindaddig, amíg naplózás engedélyezve van.  


Minden `context.log` módszerek támogatják a paraméter formátuma a Node.js által támogatott [util.format metódus](https://nodejs.org/api/util.html#util_util_format_format). Vegye figyelembe az alábbi kód, amely a konzol ír az alapértelmezett nyomkövetési szint használatával:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Ugyanazt a kódot is kiírhatja a következő formátumban:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>A nyomkövetési szint konzol naplózás konfigurálása

Funkciók lehetővé teszi a küszöbérték nyomkövetési szint írásra, a konzolt, amely megkönnyíti a módon nyomkövetési adatokat a konzoljára írt a funkciók a vezérlő meghatározása. Összes adat konzolon küszöbértéke beállításához használja a `tracing.consoleLevel` tulajdonság a host.json fájlban. A függvény alkalmazás összes funkciójának alkalmazza a beállítást. A következő példa a küszöbértéket nyomkövetési részletes naplózás engedélyezése:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Az értékek **consoleLevel** nevének felel meg a `context.log` módszerek. A konzol összes nyomkövetési naplózás letiltásához állítsa **consoleLevel** való _ki_. További információkért lásd: [host.json hivatkozás](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és kötések

A HTTP és a webhook eseményindítók és a HTTP kimeneti kötések kérés- és -objektumok segítségével határoz meg a HTTP-üzenetküldést.  

### <a name="request-object"></a>Kérelem objektum

A `request` objektum tulajdonságai a következők:

| Tulajdonság      | Leírás                                                    |
| ------------- | -------------------------------------------------------------- |
| _törzs_        | A kérelem törzsét tartalmazó objektum.               |
| _fejlécek_     | A kérelem fejlécében tartalmazó objektum.                   |
| _módszer_      | A kérelem HTTP-metódust.                                |
| _originalUrl_ | A kérelem URL-CÍMÉT.                                        |
| _paraméterei_      | A kérés útválasztási paramétereit tartalmazó objektum. |
| _lekérdezés_       | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  |
| _rawBody_     | A karakterlánc az üzenet törzsét.                           |


### <a name="response-object"></a>Válasz objektum

A `response` objektum tulajdonságai a következők:

| Tulajdonság  | Leírás                                               |
| --------- | --------------------------------------------------------- |
| _törzs_    | A választörzs tartalmazó objektum.         |
| _fejlécek_ | Egy objektum, amely tartalmazza a response fejlécekkel együtt.             |
| _isRaw_   | Azt jelzi, hogy formázás ki van kapcsolva a választ.    |
| _állapot_  | A HTTP-állapotkód: a válasz.                     |

### <a name="accessing-the-request-and-response"></a>A kérelem és válasz elérése 

HTTP-eseményindítók használata során is elérheti HTTP kérelem-válasz objektumok három módokon:

+ Az elnevezett bemeneti és kimeneti kötések. Ezzel a módszerrel a HTTP-eseményindítóval és kötések működik ugyanaz, mint bármely más kötés. A következő példa egy elnevezett használatával állítja be a válasz objektum `response` kötés: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ A `req` és `res` tulajdonságainak a `context` objektum. Ily módon használhatja a hagyományos mintát fér hozzá a HTTP adatokhoz a context objektumot, a teljes használata helyett a `context.bindings.name` mintát. A következő példa bemutatja, hogyan férhet hozzá a `req` és `res` az objektumokat a `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Meghívásával `context.done()`. HTTP-kötés olyan különleges visszaadja a választ, átadott a `context.done()` metódust. A következő HTTP kimeneti kötése definiál egy `$return` kimeneti paraméterként:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    A kimeneti kötés vár, amelyen meg kell adnia a válasz hívásakor `done()`, az alábbiak szerint:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Csomópont-verziót és a csomag felügyelet
A csomópont verziója jelenleg zárolva van `6.5.0`. Jelenleg vizsgálja a további verziók támogatása, és így konfigurálható.

Az alábbi lépéseket lehetővé teszik, hogy a csomagok tartalmazzák a függvény alkalmazásban: 

1. Nyissa meg a következőt: `https://<function_app_name>.scm.azurewebsites.net`.

2. Kattintson a **konzol Debug** > **CMD**.

3. Ugrás a `D:\home\site\wwwroot`, majd húzza a package.json fájlt a **wwwroot** mappát a a lap felső részén.  
    Feltöltheti fájlok az függvény alkalmazásban más módokon is. További információkért lásd: [függvény alkalmazásfájlok frissítése](functions-reference.md#fileupdate). 

4. Miután a package.json fájl feltöltése, futtassa a `npm install` parancsot a **Kudu távoli végrehajtás konzol**.  
    Ez a művelet letölti a csomagot a package.json fájlban jelzett, és a függvény alkalmazás újraindul.

Miután telepítette a szükséges csomagokat, importálja a fájlokat a függvény meghívásával `require('packagename')`, az alábbi példa szerint:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Meg kell határozni egy `package.json` fájl, a függvény alkalmazás gyökérmappájában. Ennek révén a legjobb teljesítmény érdekében az azonos gyorsítótárazott csomagok megosztani az alkalmazásban funkciók meghatározása a fájl segítségével. Ha verziószáma, hogyan oldható meg hozzáadásával egy `package.json` fájl a mappában, egy adott funkció.  

## <a name="environment-variables"></a>Környezeti változók
Egy környezeti változó vagy olyan alkalmazás, beállítás értékét, amelyet `process.env`, ahogy az az alábbi példakód:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>JavaScript-funkcióként szempontjai

JavaScript-funkcióként használatakor figyelembe a következő két szakaszban ismertetett szempontok alapján.

### <a name="choose-single-core-app-service-plans"></a>Válasszon egy mag App Service-csomagokról

Egy függvény alkalmazást, amely használja az App Service-csomag létrehozásakor azt javasoljuk, hogy kiválassza a több mag terv helyett egy egymagos terv. Napjainkban funkciók fut JavaScript-funkcióként hatékonyabban egymagos virtuális gépeken, és nagyobb virtuális gépek használata nem eredményez a várt teljesítménynövekedést. Szükség esetén manuálisan méretezheti ki további egymagos Virtuálisgép-példányok hozzáadásával, vagy engedélyezheti az automatikus skálázása. További információkért lásd: [méretezése példányszám manuális vagy automatikus](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Géppel és CoffeeScript támogatása
Közvetlen támogatási még nem létezik automatikus fordítása géppel vagy CoffeeScript a futtatókörnyezet keresztül, mert az ilyen támogatás kezelendő kívül a futtatókörnyezetet, a központi telepítéskor. 

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)

