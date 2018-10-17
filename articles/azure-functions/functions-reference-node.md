---
title: Az Azure Functions JavaScript-fejlesztői útmutatója |} A Microsoft Docs
description: Megismerheti, hogyan hozhat létre a függvények JavaScript használatával.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: eb9387cec98621e27aff7dcb40b8897e326c6706
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353492"
---
# <a name="azure-functions-javascript-developer-guide"></a>Az Azure Functions JavaScript-fejlesztői útmutató
Ez az útmutató az Azure Functions JavaScript írása jainak részleteivel kellene információt tartalmaz.

JavaScript-függvény egy exportált `function` , amely végrehajtja a adatvezérelt ([eseményindítók konfigurált function.json](functions-triggers-bindings.md)). Minden függvény átadása egy `context` objektum, amely fogadó és küldő kötés adatok, naplózás, és a futtatókörnyezet való kommunikáció során használatos.

Ez a cikk feltételezi, hogy már elolvasta a [Azure Functions fejlesztői segédanyagai](functions-reference.md). Továbbá ajánlatos, hogy követte az oktatóanyag a "Gyors útmutatók" [az első függvény létrehozása](functions-create-first-function-vs-code.md).

## <a name="folder-structure"></a>gyökérmappa-szerkezetében

A projekt JavaScript szükséges gyökérmappa-szerkezetében az alábbihoz hasonló. Vegye figyelembe, hogy ez az alapértelmezett módosíthatók: tekintse meg a [parancsfájl](functions-reference-node.md#using-scriptfile) további részletekért című szakasz.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
 | - bin
```

A projekt gyökerében van egy megosztott [host.json](functions-host-json.md) fájlt, amely a függvényalkalmazás konfigurálása használható. Minden függvény rendelkezik a saját kódfájl (.js) és a kötési konfigurációs fájl (function.json) nevű mappa.

A kötési bővítményeket szükséges [verzió 2.x](functions-versions.md) a Functions runtime vannak meghatározva a `extensions.csproj` fájlt, a tényleges függvénytárfájlok a `bin` mappát. Ha helyileg fejlesztésével, akkor meg kell [regisztrálja a kötési bővítményeket](functions-triggers-bindings.md#local-development-azure-functions-core-tools). Amikor fejlesztéséről az Azure Portalon, a regisztrációt, készen áll.

## <a name="exporting-a-function"></a>Egy függvény exportálása

JavaScript-függvények keresztül kell exportálni [ `module.exports` ](https://nodejs.org/api/modules.html#modules_module_exports) (vagy [ `exports` ](https://nodejs.org/api/modules.html#modules_exports)). Az alapértelmezett esetben az exportált függvény kell lennie a fájlból, az Exportálás nevű kivitel `run`, vagy az Exportálás nevű `index`. A függvény az alapértelmezett hely a `index.js`, ahol `index.js` szülő könyvtárába a kapcsolódó oszt `function.json`. Vegye figyelembe, hogy neve `function.json`a könyvtár (szülő) mindig a függvényalkalmazás nevére. 

Konfigurálja a fájl helyét, és exportálja a függvényalkalmazás nevére, olvassa el [konfigurálása a függvény belépési pont](functions-reference-node.md#configure-function-entry-point) alatt.

Az exportált függvénybelépési pont mindig szükség van egy `context` az első paraméterként az objektumot.

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```
```javascript
// You can also use 'arguments' to dynamically handle inputs
module.exports = async function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
};
```

Eseményindítók és kötések bemeneti (vazby prvku `direction === "in"`) is lehet a függvénynek átadott paraméterek. A függvény ugyanabban a sorrendben vannak meghatározva, a rendszer átad *function.json*. Bemenet a JavaScript használatával dinamikusan kezelheti [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) objektum. Például, ha rendelkezik `function(context, a, b)` , és módosítsa a következőre `function(context, a)`, továbbra is használhatja az értékét `b` lépésként tekintse át a függvény kódját a `arguments[2]`.

Összes kötését iránya, függetlenül is továbbít a `context` objektumba a `context.bindings` tulajdonság.

### <a name="exporting-an-async-function"></a>Egy aszinkron függvény exportálása
Ha a JavaScript használatával [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) nyilatkozat vagy egyszerű JavaScript [ígéretek](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) (nem érhető el a függvények v1.x), nem explicit módon kell meghívni a [ `context.done` ](#contextdone-method) visszahívási, hogy jelezze, hogy befejeződött-e a függvényt. A függvény befejezi az exportált aszinkron függvény/Promise befejezéséről.

Például ez az egyszerű függvény, amely naplózza, hogy lett elindítva, és azonnal a végrehajtás befejeződik.
``` javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Egy aszinkron függvény exportálásakor kimeneti kötések érvénybe is konfigurálhatja a `return` értéket. Ez az egy alternatív módszer használatával Kimenetek hozzárendelésével a [ `context.bindings` ](#contextbindings-property) tulajdonság.

Egy kimeneti-nal hozzárendelendő `return`, módosítsa a `name` tulajdonságot `$return` a `function.json`.
```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```
A JavaScript-függvény kód nézhet ki:
```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="context-object"></a>környezeti objektumra
A futtatókörnyezet-használja egy `context` objektum át adat és a függvényt, és lehetővé teszi, hogy futtatókörnyezetével.

A `context` objektum mindig a függvény első paraméterként, és szerepelnie kell, mert módszerek például `context.done` és `context.log`, amely ahhoz, hogy a futtatókörnyezet megfelelően használni. Elnevezheti az objektum bármit szeretne (például `ctx` vagy `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Context.Bindings tulajdonság

```
context.bindings
```
Egy elnevezett objektumot, amely tartalmazza a bemeneti és kimeneti adatokat ad vissza. Ha például a következő kötési meghatározások a a *function.json* lehetővé teszi, hogy a várólista tartalmának eléréséhez `context.bindings.myInput` és a egy üzenetsor a kimenetek hozzárendelése `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
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

Vegye figyelembe, hogy ha szeretné, adja meg a kimeneti kötés használatával a `context.done` metódus helyett a `context.binding` objektum (lásd alább).

### <a name="contextbindingdata-property"></a>context.bindingData tulajdonság

```
context.bindingData
```
Egy eseményindító metaadatok és a függvény meghívási adatokat tartalmazó nevű objektumot ad vissza (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Az eseményindító metaadatok egy példa: Ez [event hubs példa](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Context.Done metódus
```
context.done([err],[propertyBag])
```

Arról tájékoztatja a futtatókörnyezet, amely a kód befejeződött. Ha a függvényt használ a JavaScript [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarace (érhető el a függvények verzió 8 + csomópontja használatával 2.x-es), nem szeretné használni `context.done()`. A `context.done` visszahívási implicit módon nevezzük.

Ha a függvény nem egy aszinkron függvény **hívja meg** `context.done` tájékoztatja a futtatókörnyezet, hogy helyesek-e a függvényt. A végrehajtás időtúllépést okoz, ha hiányzik.

A `context.done` módszer lehetővé teszi, hogy vissza mindkét egy felhasználó által definiált hiba történt a modul és a egy kimeneti kötés adatokat tartalmazó JSON-objektum adnia. Az átadott tulajdonságok `context.done` bármit beállítása felülírja a `context.bindings` objektum.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context.log metódus  

```
context.log(message)
```
A nyomkövetési szint függvény streamnaplókba vizsgálatát teszi lehetővé. A `context.log`, további naplózási módszerek érhetők, amelyek segítségével a függvény naplóihoz, a többi nyomkövetési szint:


| Módszer                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **Hiba (_üzenet_)**   | Naplózás, vagy alacsonyabb hibaszintet ír.   |
| **Figyelmeztetés (_üzenet_)**    | Figyelmeztetési szint naplózás vagy alacsonyabb ír. |
| **info(_message_)**    | Írási és naplózás, vagy alacsonyabb információ szintet.    |
| **verbose(_message_)** | Részletes webhelyszintű naplózás ír.           |

A következő példa ír a naplófájlba a figyelmeztetési nyomkövetési szint:

```javascript
context.log.warn("Something has happened."); 
```
Is [konfigurálja a nyomkövetési szintű küszöbértéket, a naplózáshoz](#configure-the-trace-level-for-console-logging) a host.json fájlban. Naplók írásáról további információkért lásd: [nyomkövetési kimenetek írása](#writing-trace-output-to-the-console) alatt.

Olvasási [Azure Functions figyelése](functions-monitoring.md) tudhat meg többet a megtekintése, és a függvény naplóinak lekérdezéséhez.

## <a name="binding-data-type"></a>Kötés adattípus

Bemeneti kötés adattípusát meghatározásához használja a `dataType` tulajdonság a kötés-definícióban. Olvassa el a tartalom HTTP-kérés bináris formátumú, például a típus használja `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

A beállítások `dataType` vannak: `binary`, `stream`, és `string`.

## <a name="writing-trace-output-to-the-console"></a>A konzol nyomkövetési írása 

A függvények, használja a `context.log` módszerek nyomkövetési írni a konzolon. A Functions 2.x, nyomkövetési keresztül ouputs `console.log` rögzítve lesznek a Függvényalkalmazás szintjén. Ez azt jelenti, hogy a kimenet `console.log` nem kötődnek, egy adott függvény meghívási, és ezért nem jelenik meg egy adott függvény naplóihoz. Azok, azonban továbbterjeszti az Application Insightsba. A Functions v1.x, nem használhat `console.log` írni a konzolon. 

Meghívásakor `context.log()`, az üzenet íródik a konzolt a nyomkövetési szint, amely a _info_ nyomkövetési szint. A következő kódot ír a konzolban, amikor az információ a nyomkövetési szintet:

```javascript
context.log({hello: 'world'});  
```

Ez a kód megegyezik a fenti kód:

```javascript
context.log.info({hello: 'world'});  
```

Ezt a kódot ír a konzolban, amikor a hiba szintje:

```javascript
context.log.error("An error has occurred.");  
```

Mivel _hiba_ van a legmagasabb nyomkövetési szint a nyomkövetési íródik a kimenetet a minden nyomkövetési szintek mindaddig, amíg naplózás engedélyezve van.

Az összes `context.log` módszereket támogatja a paraméter formátuma a Node.js által támogatott [util.format metódus](https://nodejs.org/api/util.html#util_util_format_format). Vegye figyelembe a következő kódra, amely az alapértelmezett nyomkövetési szint használatával írja a függvény naplóihoz:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Ugyanazt a kódot is írhat a következő formátumban:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>A nyomkövetési szintet konzol naplózásának konfigurálása

Functions segítségével határozza meg a küszöbérték nyomkövetési szintet a konzolt, ami lehetővé teszi az egyszerű vezérlési módszert nyomkövetés a konzoljára írt az a funkciók írásához. Összes nyomkövetési konzolon küszöbértéke beállításához használja a `tracing.consoleLevel` tulajdonság a host.json fájlban. Ez a beállítás minden függvény a függvényalkalmazásban vonatkozik. Az alábbi példa állítja be a részletes naplózás engedélyezése a nyomkövetési küszöbértéket:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Az értékek **consoleLevel** nevei megegyeznek a `context.log` módszereket. A konzol összes nyomkövetési naplózás letiltásához állítsa **consoleLevel** való _ki_. További információkért lásd: [host.json referencia](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és kötések

HTTP és a webhook eseményindítók és a HTTP-kimeneti kötések használata kérések és válaszok objektumok, amelyek a HTTP-üzenetkezelés.  

### <a name="request-object"></a>Támogatásikérelem-objektum

A `context.req` (kérelem) objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás                                                    |
| ------------- | -------------------------------------------------------------- |
| _Törzs_        | Egy objektum, amely tartalmazza a kérelem törzsében.               |
| _A fejlécek_     | Egy objektum, amely tartalmazza a kérelem fejlécében.                   |
| _Metódus_      | A kérelem HTTP-metódus.                                |
| _originalUrl_ | A kérelem URL-címe                                        |
| _params_      | Egy objektum, amely tartalmazza a kérés útválasztási paramétereit. |
| _Lekérdezés_       | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  |
| _rawBody_     | A karakterláncként az üzenet törzsében.                           |


### <a name="response-object"></a>Válaszobjektum

A `context.res` (válasz) objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Leírás                                               |
| --------- | --------------------------------------------------------- |
| _Törzs_    | Egy objektum, amely a válasz törzse tartalmazza.         |
| _A fejlécek_ | A válaszfejlécek tartalmazó objektum.             |
| _isRaw_   | Azt jelzi, hogy a válasz formázás kihagyva.    |
| _Állapot_  | A válasz HTTP-állapotkódot.                     |

### <a name="accessing-the-request-and-response"></a>A kérés- és hozzáférés 

Ha HTTP-eseményindítók használata során a HTTP kérés- és objektumokat számos módon érheti el:

+ A `req` és `res` tulajdonságai a `context` objektum. Ezzel a módszerrel használhatja a hagyományos minta adatokhoz való hozzáférésének HTTP a context objektumot, az összes használata helyett a `context.bindings.name` mintát. Az alábbi példa bemutatja, hogyan lehet hozzáférni a `req` és `res` az objektumokat a `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ A nevesített bemeneti és kimeneti kötések. Ezzel a módszerrel a HTTP-eseményindítóval és kötések működik ugyanaz, mint bármely másik kötés. Az alábbi példa a válasz objektum beállítja a használatával egy nevesített `response` kötést: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ _[Csak válasz]_  Meghívásával `context.res.send(body?: any)`. HTTP-választ jön létre a bemeneti `body` , a válasz törzse. `context.done()` implicit módon nevezzük.

+ _[Csak válasz]_  Meghívásával `context.done()`. HTTP-kötést egy különleges visszaadja a választ, az átadott a `context.done()` metódust. A következő HTTP-kimeneti kötés határozza meg a `$return` kimeneti paraméterként:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Csomópont verziója

Az alábbi táblázat az egyes főverziója a Functions futtatókörnyezete által használt Node.js-verzió:

| Functions-verzió | Node.js verziója | 
|---|---|
| 1.x | 6.11.2 (futásidejű zárolta) |
| 2.x  | _Aktív LTS_ és _aktuális_ Node.js-verzió (8.11.1 és ajánlott 10.6.0). A verzió-beállításokat a WEBSITE_NODE_DEFAULT_VERSION [Alkalmazásbeállítás](functions-how-to-use-azure-function-app-settings.md#settings).|

Láthatja, hogy a jelenlegi verzió a modul által használt, a fenti alkalmazás beállítás bejelölésével vagy a Nyomtatás `process.version` a függvényeket.

## <a name="dependency-management"></a>Függőségkezelés
A JavaScript-kódba közösségi kódtárak használatához, amint azt az alábbi példában, győződjön meg arról, hogy az összes függősége telepítve van-e a Függvényalkalmazás Azure-ban kell.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Meg kell határozni egy `package.json` fájlt a Függvényalkalmazás gyökérmappájában. Meghatározása a fájl lehetővé teszi, hogy a függvények az alkalmazás megosztása azonos gyorsítótárazott csomagok, ami a legjobb teljesítményt biztosítja. Verzióütközés merül fel, ha fel tudja oldani hozzáadásával egy `package.json` egy adott függvény mappában található fájl.  

Alkalmazások üzembe helyezésére függvény a forráskezelőből, minden `package.json` fájl a tárházban, akkor egy `npm install` üzembe helyezés során a mappában. De a portál vagy a parancssori felület használatával történő telepítésekor kell manuálisan telepítenie a csomagokat.

A Függvényalkalmazás-csomagok telepítése a két módja van: 

### <a name="deploying-with-dependencies"></a>A függőségek telepítése
1. Az összes előfeltételként szükséges csomagok telepítéséhez a helyileg futó `npm install`.

2. A kód üzembe helyezése, és ellenőrizze, hogy a `node_modules` mappában megtalálható az üzembe helyezés. 


### <a name="using-kudu"></a>A Kudu használata
1. Nyissa meg a következőt: `https://<function_app_name>.scm.azurewebsites.net`.

2. Kattintson a **hibakeresési konzolt** > **CMD**.

3. Lépjen a `D:\home\site\wwwroot`, majd húzza a package.json fájlt a **wwwroot** mappában található az oldal felső részén.  
    Feltölthet fájlokat a függvényalkalmazáshoz, egyéb módon is. További információkért lásd: [függvény alkalmazásfájlok frissítése](functions-reference.md#fileupdate). 

4. Miután feltöltötte a package.json fájlt, futtassa a `npm install` parancsot a **Kudu távoli végrehajtás konzol**.  
    Ez a művelet tölti le a csomagokat a package.json fájlban jelzett, és újraindítja a függvényalkalmazást.

## <a name="environment-variables"></a>Környezeti változók
Egy környezeti változó vagy olyan alkalmazás, beállítás értékét, amelyet `process.env`, amint az itt látható a `GetEnvironmentVariable` függvény:

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

## <a name="configure-function-entry-point"></a>Függvénybelépési pont konfigurálása

A `function.json` tulajdonságok `scriptFile` és `entryPoint` konfigurálása a helyét és nevét az exportált függvény is használható. Ezek lehetnek fontos, ha a JavaScript transpiled.

### <a name="using-scriptfile"></a>Használatával `scriptFile`

Alapértelmezés szerint JavaScript-függvény hajtja végre a `index.js`, egy közös szülő könyvtárába annak megfelelő fájl `function.json`.

`scriptFile` a mappastruktúra a következőhöz hasonló beolvasásához használható:
```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

A `function.json` a `myNodeFunction` tartalmaznia kell egy `scriptFile` a fájl futtatása az exportált funkcióval mutató tulajdonság.
```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Használatával `entryPoint`

A `scriptFile` (vagy `index.js`), a függvény használatával kell exportálni `module.exports` található, és futtassa. Alapértelmezés szerint a függvény aktiválódik, ha az exportálás csak a fájl, az Exportálás nevű `run`, vagy az Exportálás nevű `index`.

Ennek használatával konfigurálható `entryPoint` a `function.json`:
```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

A Functions 2.x, amely támogatja a `this` paramétert a felhasználói függvényeket, a függvény kódját is ki lehet a következő:
```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };
    
    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Ebben a példában fontos megjegyezni, hogy az objektum exportálása folyamatban van, noha nincsenek nincs jelentéscsomagot körül végrehajtások közötti állapot megőrzése.

## <a name="considerations-for-javascript-functions"></a>JavaScript-függvények szempontjai

JavaScript-függvények használata, a következő szakaszokban ismertetett szempontok figyelembe.

### <a name="choose-single-vcpu-app-service-plans"></a>Válasszon egy vcpu-nkénti App Service-csomagok

Amikor létrehoz egy függvényalkalmazást, amelyet az App Service-csomagot használ, azt javasoljuk, hogy kiválaszt egy csomagban több vcpu-k helyett egy egyszeri vcpu-nkénti terv. Még ma függvények JavaScript-függvények sokkal hatékonyabban futtatható egyetlen vcpu-nkénti virtuális gépeken, és nagyobb virtuális gépek használata nem eredményez a várt teljesítménnyel kapcsolatos fejlesztések. Ha szükséges, manuálisan horizontális felskálázása további egyetlen vcpu-nkénti Virtuálisgép-példányok hozzáadásával, vagy engedélyezheti az automatikus méretezés. További információkért lásd: [példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>TypeScript és CoffeeScript támogatása
Közvetlen támogatást még nem létezik automatikus fordítása TypeScript vagy CoffeeScript a modulon keresztül, mert az ilyen támogatás kezelendő kívül a futtatókörnyezet üzembe helyezéskor. 

### <a name="cold-start"></a>Hidegindítási
Fejlesztése az Azure Functions a kiszolgáló nélküli üzemeltetési modell, ritkán használt indításakor vannak a valóság. "Hidegindítás" arra utal, hogy a Függvényalkalmazás indításakor tétlen időszak után első alkalommal hosszabb ideig tart indul el. A JavaScript-függvények nagy függőségi fa-, ez okozhat fő lassulás. Annak érdekében, hogy a folyamat, ha lehetséges, gyorsítható [a függvények futtató egy csomagfájlt](run-functions-from-deployment-package.md). Alapértelmezés szerint ez a modell használatának számos központi telepítési módszer, de ha nagy kiküszöbölik tapasztaló és a egy csomagfájlt nem futnak, ez lehet egy nagy javítása.

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)

