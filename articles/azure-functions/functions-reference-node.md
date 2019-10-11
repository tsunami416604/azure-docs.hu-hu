---
title: JavaScript fejlesztői referenciája Azure Functionshoz | Microsoft Docs
description: Ismerje meg, hogyan fejlesztheti a függvényeket a JavaScript használatával.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-függvények, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 86bacbe22ce23fc4b0355374d81a96310e59178a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255014"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript fejlesztői útmutató

Ez az útmutató a JavaScript-Azure Functions írásának bonyolult adatait tartalmazza.

A JavaScript-függvény egy olyan exportált @no__t – 0, amely az aktiváláskor fut ([a triggerek a function. JSON fájlban vannak konfigurálva](functions-triggers-bindings.md)). Az összes függvénynek átadott első argumentum egy `context` objektum, amely a kötési adatok fogadására és küldésére, a naplózásra és a futtatókörnyezettel folytatott kommunikációra szolgál.

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions fejlesztői referenciát](functions-reference.md). Az első függvény létrehozásához a [Visual Studio Code](functions-create-first-function-vs-code.md) használatával vagy [a portálon](functions-create-first-azure-function.md)hajtsa végre a functions rövid útmutatót.

Ez a cikk a [írógéppel való alkalmazásfejlesztés](#typescript)támogatását is támogatja.

## <a name="folder-structure"></a>Mappa szerkezete

A JavaScript-projekthez szükséges mappastruktúrát a következőhöz hasonlóan néz ki. Ez az alapértelmezett érték módosítható. További információért lásd az alábbi [scriptFile](#using-scriptfile) szakaszt.

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
```

A projekt gyökerében található egy megosztott [Host. JSON](functions-host-json.md) fájl, amely a Function alkalmazás konfigurálására használható. Minden függvényhez tartozik egy, a saját programkódját (. js) és a kötési konfigurációs fájlt (function. JSON) tartalmazó mappa. @No__t-0 szülőjének neve mindig a függvény neve.

A functions futtatókörnyezet [2. x verziójában](functions-versions.md) szükséges kötési kiterjesztések a `extensions.csproj` fájlban vannak meghatározva, a `bin` mappában található tényleges függvénytár-fájlokkal. Helyi fejlesztés esetén [regisztrálnia kell a kötési bővítményeket](./functions-bindings-register.md#extension-bundles). A Azure Portal funkcióinak fejlesztésekor ez a regisztráció történik.

## <a name="exporting-a-function"></a>Függvény exportálása

A JavaScript-függvényeket [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (vagy [`exports`](https://nodejs.org/api/modules.html#modules_exports)) használatával kell exportálni. Az exportált függvénynek JavaScript-függvénynek kell lennie, amely az aktiváláskor végrehajtódik.

Alapértelmezés szerint a functions futtatókörnyezet `index.js` értékben keresi a függvényt, ahol a `index.js` a hozzá tartozó `function.json`. Az alapértelmezett esetben az exportált függvény csak a fájlból, vagy a `run` vagy `index` nevű exportálásból exportálható. A fájl helyének és a függvény exportálási nevének konfigurálásához olvassa el a [függvény belépési pontjának konfigurálását](functions-reference-node.md#configure-function-entry-point) alább.

Az exportált függvény számos argumentumot adott meg a végrehajtáskor. Az első szükséges argumentum mindig egy `context` objektum. Ha a függvény szinkronban van (nem ad vissza ígéretet), akkor meg kell adnia a `context` objektumot, mivel a helyes használat érdekében a `context.done` hívása szükséges.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Aszinkron függvény exportálása
Ha az [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarációt vagy az egyszerű JavaScriptet [ígéri](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) a functions futtatókörnyezet 2. x verziójában, nem kell explicit módon meghívnia a [`context.done`](#contextdone-method) visszahívást, hogy jelezze a függvény befejezését. A függvény akkor fejeződik be, amikor az exportált aszinkron függvény/ígéret befejeződik. Az 1. x verziójú futtatókörnyezetet célzó függvények esetén a kód végrehajtásának végrehajtásakor továbbra is a [`context.done`](#contextdone-method) hívást kell meghívni.

A következő példa egy egyszerű függvény, amely naplózza, hogy elindították, és azonnal befejezi a végrehajtást.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Aszinkron függvény exportálásakor beállíthatja a kimeneti kötést is, hogy a `return` értéket adja meg. Ez akkor ajánlott, ha csak egy kimeneti kötése van.

Ha `return` használatával szeretne kimenetet rendelni, módosítsa a `name` tulajdonságot a következőre: `$return`, `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Ebben az esetben a függvénynek a következő példához hasonlóan kell kinéznie:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Kötések 
A JavaScriptben a [kötések](functions-triggers-bindings.md) konfigurálva és definiálva vannak egy függvény function. JSON fájljában. A függvények számos módon működnek együtt a kötésekkel.

### <a name="inputs"></a>Bemenetek
A bemenet két kategóriára oszlik Azure Functionsban: az egyik az trigger bemenete, a másik pedig a további bemenet. A trigger és más bemeneti kötések (`direction === "in"`) kötései háromféle módon olvashatók be a függvényekben:
 - **_[Ajánlott]_ A függvénynek átadott paraméterek.** A függvénynek a *function. JSON*fájlban megadott sorrendben adják át őket. A *function. JSON* fájlban definiált `name` tulajdonságnak nem kell megegyeznie a paraméter nevével, bár ez a következő:.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **A [`context.bindings`](#contextbindings-property) objektum tagjaként.** Az egyes tagok neve a *function. JSON*fájlban megadott `name` tulajdonság.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **A JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) objektumot használó bemenetként.** Ez lényegében ugyanaz, mint a bemenetek paraméterként való továbbítása, de lehetővé teszi a bemenetek dinamikus kezelését.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Kimenetek
A kimeneteket (`direction === "out"`) a függvény számos módon képes írni. A kötés a *function. JSON* fájlban megadott `name` tulajdonsága minden esetben megfelel a függvényben írt Object tag nevének. 

Az alábbi módszerek egyikével rendelhet adatokat a kimeneti kötésekhez (ne kombinálja ezeket a metódusokat):

- **_[Több kimenet esetén ajánlott)_ Objektum visszaadása.** Ha aszinkron/Promise returning függvényt használ, a hozzárendelt kimeneti adatokat tartalmazó objektumot adhat vissza. Az alábbi példában a kimeneti kötések neve "httpResponse" és "queueOutput" a *function. JSON*fájlban.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Ha szinkron függvényt használ, ezt az objektumot [`context.done`](#contextdone-method) (lásd: példa) használatával adhatja vissza.
- **_[Egyetlen kimenethez ajánlott_ Közvetlenül egy értéket ad vissza, és a $return kötési nevet használja.** Ez csak az aszinkron/Promise Returns függvények esetében működik. Lásd: példa az [aszinkron függvények exportálására](#exporting-an-async-function). 
- **Értékek Kiosztása `context.bindings` értékre** Az értékeket közvetlenül is hozzárendelheti a Context. kötésekhez.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Kötések adattípusa

Egy bemeneti kötés adattípusának megadásához használja a `dataType` tulajdonságot a kötés definíciójában. Ha például bináris formátumban szeretné beolvasni egy HTTP-kérelem tartalmát, használja a `binary` típust:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

A `dataType` beállításai: `binary`, `stream` és `string`.

## <a name="context-object"></a>környezeti objektum
A futásidejű `context` objektum használatával továbbítja az adatokhoz a függvényt, és lehetővé teszi a kommunikációt a futtatókörnyezettel. A környezeti objektum használható a kötések adatainak olvasására és beállítására, a naplók írására és a `context.done` visszahívás használatára, ha az exportált függvény szinkronban van.

A `context` objektum mindig a függvény első paramétere. A csomagnak szerepelnie kell, mert olyan fontos módszerekkel rendelkezik, mint a `context.done` és a `context.log`. Megadhatja az objektum nevét, amit szeretne (például `ctx` vagy `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Context. binds tulajdonság

```js
context.bindings
```

Egy elnevezett objektumot ad vissza, amely a kötési adatok olvasására vagy hozzárendelésére szolgál. A bemeneti és trigger kötési adatokat a `context.bindings` tulajdonság tulajdonságainak olvasásával érheti el. A kimeneti kötési adatokat hozzá lehet rendelni a `context.bindings` értékhez való hozzáadásával

Például a function. JSON következő kötési definíciói lehetővé teszik egy üzenetsor tartalmának elérését `context.bindings.myInput` értékről, és kimenetek hozzárendelését egy várólistához `context.bindings.myOutput` használatával.

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

Kiválaszthatja, hogy a `context.binding` objektum helyett a `context.done` metódussal definiálja a kimeneti kötési adatokat (lásd alább).

### <a name="contextbindingdata-property"></a>Context. bindingData tulajdonság

```js
context.bindingData
```

Egy elnevezett objektumot ad vissza, amely trigger-metaadatokat és függvény Meghívási adatokat tartalmaz (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Az eseményindító metaadatainak példáját itt tekintheti meg: [példa az Event hub](functions-bindings-event-hubs.md#trigger---javascript-example)-ra.

### <a name="contextdone-method"></a>Context. Done metódus

```js
context.done([err],[propertyBag])
```

Lehetővé teszi, hogy a futtatókörnyezet tudja, hogy a kód befejeződött. Ha a függvény a [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarációt használja, nincs szükség a `context.done()` használatára. A `context.done` visszahívás implicit módon van meghívva. Az aszinkron függvények a 8. csomópontban vagy egy újabb verzióban érhetők el, amelyhez a functions futtatókörnyezet 2. x verziója szükséges.

Ha a függvény nem aszinkron függvény, **meg kell hívnia** a `context.done`, hogy tájékoztassa a futtatókörnyezetet arról, hogy a függvény kész. Ha hiányzik a végrehajtás, időtúllépés történik.

A `context.done` módszer lehetővé teszi, hogy a felhasználó által definiált hibát visszaadja a futtatókörnyezetnek, valamint egy olyan JSON-objektumot, amely kimeneti kötési adatokat tartalmaz. A (z) `context.done` értékre átadott tulajdonságok felülírják a `context.bindings` objektumon beállított összes értéket.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context. log metódus  

```js
context.log(message)
```

Lehetővé teszi, hogy az alapértelmezett nyomkövetési szinten írjon a streaming Function naplóiba. @No__t – 0 esetén további naplózási módszerek érhetők el, amelyek lehetővé teszik a függvények naplóinak más nyomkövetési szinteken való írását:


| Módszer                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **hiba (_üzenet_)**   | A hiba szintű naplózás vagy az alacsonyabb értékre ír.   |
| **Figyelmeztetés (_üzenet_)**    | Figyelmeztetési szintű naplózás vagy alacsonyabb értékre írás. |
| **információ (_üzenet_)**    | Az adatszintű naplózás vagy az alacsonyabb értékre írja az adatokat.    |
| **részletes (_üzenet_)** | Írás a részletes szintű naplózásba.           |

A következő példa egy naplót ír a figyelmeztetési nyomkövetési szintre:

```javascript
context.log.warn("Something has happened."); 
```

A [naplózási szint küszöbértékét beállíthatja](#configure-the-trace-level-for-console-logging) a Host. JSON fájlban való bejelentkezéshez. A naplók írásával kapcsolatos további információkért lásd: [nyomkövetési kimenetek írása](#writing-trace-output-to-the-console) alább.

Olvassa el a [figyelési Azure functions](functions-monitoring.md) , ha többet szeretne megtudni a függvények naplóinak megtekintéséről és lekérdezéséről.

## <a name="writing-trace-output-to-the-console"></a>Nyomkövetési kimenet írása a konzolra 

A functions-ben a `context.log` metódusokkal írhat nyomkövetési kimenetet a konzolra. A functions v2. x-ben a `console.log` értéket használó nyomkövetési kimenetek a függvényalkalmazás szinten vannak rögzítve. Ez azt jelenti, hogy a `console.log` kimenetei nem kötődnek egy adott függvény meghívásához, és nem jelennek meg egy adott függvény naplóiban. Azonban a Application Insights propagálják őket. A függvények v1. x verziójában nem használhatja a `console.log` értéket a konzolra való íráshoz.

Ha `context.log()` hívást hív meg, az üzenet a konzolra kerül az alapértelmezett nyomkövetési szinten, amely az _adatok_ nyomkövetési szintje. A következő kód a-konzolra ír az információs nyomkövetési szinten:

```javascript
context.log({hello: 'world'});  
```

Ez a kód egyenértékű a fenti kóddal:

```javascript
context.log.info({hello: 'world'});  
```

Ez a kód a konzolon a hiba szintjén írja a következőt:

```javascript
context.log.error("An error has occurred.");  
```

Mivel a _hiba_ a legmagasabb nyomkövetési szint, ez a nyomkövetés minden nyomkövetési szinten a kimenetbe kerül, amíg a naplózás engedélyezve van.

Az összes `context.log` módszer ugyanazt a paraméter-formátumot támogatja, amelyet a Node. js [util. Format metódusa](https://nodejs.org/api/util.html#util_util_format_format)támogat. Vegye figyelembe a következő kódot, amely az alapértelmezett nyomkövetési szinttel írja be a függvény naplóit:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Ugyanezt a kódot a következő formátumban is megírhatja:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>A konzol naplózási szintjének konfigurálása

Az 1. x függvények lehetővé teszik a küszöbérték nyomkövetési szintjének meghatározását a konzolra való íráshoz, így könnyen szabályozható, hogy a rendszer hogyan írja a nyomkövetéseket a konzolra a függvényből. A konzolra írt összes nyomkövetés küszöbértékének megadásához használja a `tracing.consoleLevel` tulajdonságot a Host. JSON fájlban. Ez a beállítás a Function alkalmazás összes függvényére érvényes. A következő példa a nyomkövetési küszöbértéket állítja be a részletes naplózás engedélyezéséhez:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

A **consoleLevel** értékei megfelelnek az `context.log` metódusok neveinek. Ha le szeretné tiltani az összes nyomkövetési naplózást a konzolon, állítsa a **consoleLevel** beállítást _ki_értékre. További információ: [Host. JSON-dokumentáció](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és-kötések

A HTTP-és webhook-eseményindítók és a HTTP-kimeneti kötések a HTTP-üzenetküldést a kérelmek és válaszok objektumok használatával jelölik.  

### <a name="request-object"></a>Kérelem objektum

A `context.req` (kérelem) objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás                                                    |
| ------------- | -------------------------------------------------------------- |
| _törzse_        | Egy objektum, amely tartalmazza a kérelem törzsét.               |
| _fejlécek_     | Egy objektum, amely a kérések fejléceit tartalmazza.                   |
| _metódus_      | A kérelem HTTP-metódusa.                                |
| _originalUrl_ | A kérelem URL-címe.                                        |
| _paraméterei_      | Egy objektum, amely a kérelem útválasztási paramétereit tartalmazza. |
| _lekérdezés_       | Egy objektum, amely tartalmazza a lekérdezési paramétereket.                  |
| _rawBody_     | Az üzenet törzse sztringként.                           |


### <a name="response-object"></a>Válaszobjektum

A `context.res` (Response) objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Leírás                                               |
| --------- | --------------------------------------------------------- |
| _törzse_    | Egy objektum, amely tartalmazza a válasz törzsét.         |
| _fejlécek_ | Egy objektum, amely tartalmazza a válasz fejléceit.             |
| _isRaw_   | Azt jelzi, hogy a válasz formátuma kimarad.    |
| _állapota_  | A válasz HTTP-állapotkódot.                     |

### <a name="accessing-the-request-and-response"></a>A kérelem és válasz elérése 

HTTP-eseményindítók használata esetén a HTTP-kérelem és a válasz-objektumok többféleképpen is elérhetők:

+ **A `context` objektum `req` és `res` tulajdonságai közül.** Ily módon a környezeti objektumból a HTTP-adatok eléréséhez a hagyományos mintázatot használhatja ahelyett, hogy a teljes `context.bindings.name` mintát kellene használnia. Az alábbi példa bemutatja, hogyan érheti el a `req` és a `res` objektumokat a `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Az elnevezett bemeneti és kimeneti kötések közül.** Így a HTTP-trigger és a kötések ugyanúgy működnek, mint bármely más kötés. A következő példa egy elnevezett `response` kötés használatával állítja be a válasz objektumot: 

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
+ **_[Válasz csak]_ @No__t-2 meghívásával.** A rendszer egy HTTP-választ hoz létre a `body` bemenettel a válasz törzsében. a `context.done()` implicit módon van meghívva.

+ **_[Válasz csak]_ @No__t-2 meghívásával.** A speciális HTTP-kötés a `context.done()` metódusnak átadott választ adja vissza. A következő HTTP kimeneti kötés a `$return` kimeneti paramétert definiálja:

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

A következő táblázat a függvények futtatókörnyezetének egyes főbb verziói által használt Node. js-verziót mutatja be:

| Függvények verziója | Node. js-verzió | 
|---|---|
| 1. x | 6.11.2 (a futtatókörnyezet zárolta) |
| 2. x  | _Aktív LTS_ -és _karbantartási LTS_ Node. js-verziók (~ 10 ajánlott). A verzió megcélzása az Azure-ban a WEBSITE_NODE_DEFAULT_VERSION [alkalmazás beállításának](functions-how-to-use-azure-function-app-settings.md#settings) `~10` értékre való beállításával.|

A futtatókörnyezet által használt aktuális verziót úgy tekintheti meg, ha ellenőrzi a fenti alkalmazás beállítását, vagy a `process.version` bármely függvényből való nyomtatásával.

## <a name="dependency-management"></a>Függőségkezelés
Ha a JavaScript-kódban a közösségi kódtárakat szeretné használni, ahogy az az alábbi példában is látható, gondoskodnia kell arról, hogy az Azure-ban minden függőség telepítve legyen az függvényalkalmazás.

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
> @No__t-0 fájlt kell megadnia a függvényalkalmazás gyökerében. A fájl definiálása lehetővé teszi, hogy az alkalmazás összes funkciója ugyanazt a gyorsítótárazott csomagot használja, amely a legjobb teljesítményt biztosítja. Ha ütközés lép fel, azt megoldhatja egy `package.json` fájl hozzáadásával egy adott függvény mappájába.  

Ha a forrásoldali vezérlőből telepíti a függvények alkalmazásait, a tárházban található bármelyik @no__t 0 fájl az üzembe helyezés során egy `npm install`-et indít el a mappájába. Ha azonban a portálon vagy a CLI-n keresztül végzi a telepítést, manuálisan kell telepítenie a csomagokat.

A függvényalkalmazás kétféleképpen telepíthet csomagokat: 

### <a name="deploying-with-dependencies"></a>Üzembe helyezés függőségekkel
1. Telepítse az összes szükséges csomagot helyileg a `npm install` futtatásával.

2. Telepítse a kódot, és ellenőrizze, hogy a `node_modules` mappa szerepel-e a központi telepítésben. 


### <a name="using-kudu"></a>A kudu használata
1. Nyissa meg a következőt: `https://<function_app_name>.scm.azurewebsites.net`.

2. Kattintson a **Debug Console** > **cmd**elemre.

3. Lépjen a `D:\home\site\wwwroot` elemre, majd húzza a Package. JSON fájlt a **wwwroot** mappába az oldal felső felén.  
    Más módokon is feltölthet fájlokat a Function alkalmazásba. További információ: [Function app Files frissítése](functions-reference.md#fileupdate). 

4. A Package. JSON fájl feltöltése után futtassa a `npm install` parancsot a **kudu távoli végrehajtási konzolon**.  
    Ez a művelet letölti a Package. JSON fájlban jelzett csomagokat, és újraindítja a Function alkalmazást.

## <a name="environment-variables"></a>Környezeti változók

A függvények, az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai a végrehajtás során környezeti változókként jelennek meg. Ezekhez a beállításokhoz `process.env` használatával férhet hozzá, ahogy az itt látható a második és harmadik hívásban `context.log()`, ahol a `AzureWebJobsStorage` és a `WEBSITE_SITE_NAME` környezeti változót naplózzák:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Helyileg futtatva az Alkalmazásbeállítások a [Local. Settings. JSON](functions-run-local.md#local-settings-file) Project fájlból lesznek beolvasva.

## <a name="configure-function-entry-point"></a>Függvény belépési pontjának konfigurálása

Az exportált függvény helyének és nevének konfigurálásához a `function.json` tulajdonságok `scriptFile` és a `entryPoint` használható. Ezek a tulajdonságok akkor lehetnek fontosak, ha a JavaScript transpiled.

### <a name="using-scriptfile"></a>@No__t-0 használata

Alapértelmezés szerint a JavaScript-függvény `index.js`, egy olyan fájl, amely ugyanazokat a szülő könyvtárat osztja meg, mint a megfelelő `function.json`.

a `scriptFile` használható a következő példához hasonló mappa-struktúra beszerzéséhez:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

A `myNodeFunction` `function.json` értékének tartalmaznia kell egy `scriptFile` tulajdonságot, amely a fájlra mutat, és az exportált függvénnyel fut.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>@No__t-0 használata

@No__t-0 (vagy `index.js`) esetében a függvényt az `module.exports` használatával kell exportálni, hogy azok megtalálhatók és fussanak. Alapértelmezés szerint az aktiváláskor végrehajtandó függvény az egyetlen Exportálás az adott fájlból, az `run` nevű exportálás, vagy a `index` nevű exportálás.

Ez a `function.json` `entryPoint` használatával konfigurálható, ahogy az alábbi példában is látható:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

A (z) v2. x függvényben, amely támogatja a `this` paramétert a felhasználói függvényekben, a függvény kódja a következő példához hasonlóan lehet:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Ebben a példában fontos megjegyezni, hogy bár egy objektum exportálása folyamatban van, nincs garancia a végrehajtások állapotának megőrzésére.

## <a name="local-debugging"></a>Helyi hibakeresés

Ha a `--inspect` paraméterrel indult el, a Node. js folyamat egy hibakeresési ügyfelet figyel a megadott porton. Azure Functions 2. x verzióban megadhat argumentumokat, amelyek a kódot futtató Node. js-folyamatba kerülnek, ha a környezeti változót vagy az Alkalmazásbeállítások `languageWorkers:node:arguments = <args>` értéket adja hozzá. 

A helyi hibakereséshez adja hozzá a `"languageWorkers:node:arguments": "--inspect=5858"` értéket a [Local. Settings. JSON](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) fájl `Values` eleméhez, és csatolja a hibakeresőt az 5858-as porthoz.

A VS Code használatakor a rendszer automatikusan hozzáadja a `--inspect` paramétert a projekt Launch. JSON fájljának `port` értékének használatával.

Az 1. x verzióban a `languageWorkers:node:arguments` beállítás nem fog működni. A hibakeresési portot a Azure Functions Core Tools [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) paraméterrel lehet kiválasztani.

## <a name="typescript"></a>TypeScript

Ha a functions futtatókörnyezet 2. x verzióját célozza meg, a [Visual Studio Code](functions-create-first-function-vs-code.md) -hoz és a [Azure functions Core Tools](functions-run-local.md) -hoz egyaránt Azure functions is létrehozhat Function apps-alkalmazásokat olyan sablon használatával, amely támogatja az írógéppel Function app-projekteket. A sablon `package.json` és `tsconfig.json` projektfájlt hoz létre, amelyek egyszerűbbé teszik a JavaScript-függvények transpile, futtatását és közzétételét az eszközökön.

A generált `.funcignore` fájl használatával jelezheti, hogy mely fájlok ki vannak zárva, amikor egy projekt közzé van téve az Azure-ban.  

Az transpiled (. TS) a `dist` kimeneti könyvtárban lévő JavaScript-fájlokba (. js) kerülnek. Az írógéppel sablon a [`scriptFile` paramétert](#using-scriptfile) használja `function.json` értékben, hogy jelezze a megfelelő. js fájl helyét a `dist` mappában. A kimeneti helyet a sablon állítja be úgy, hogy `outDir` paramétert használ az `tsconfig.json` fájlban. Ha módosítja ezt a beállítást vagy a mappa nevét, a futtatókörnyezet nem találja a futtatandó kódot.

> [!NOTE]
> Az írógéppel való kísérleti támogatás létezik a functions futtatókörnyezet 1. x verziója. A kísérleti verzió transpiles írógéppel fájlokat a JavaScript-fájlokba a függvény meghívásakor. A 2. x verzióban ezt a kísérleti támogatást felváltotta az eszköz által vezérelt módszer, amely a gazdagép inicializálása előtt és a telepítési folyamat során transpilation.

A helyi fejlesztés és üzembe helyezés módja a fejlesztői eszköztől függ.

### <a name="visual-studio-code"></a>Visual Studio-kód

A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) bővítményhez készült Azure functions lehetővé teszi a függvények fejlesztését a géppel. Az alapvető eszközök a Azure Functions bővítmény követelménye.

Ha a Visual Studio Code-ban szeretne létrehozni egy írógéppel Function-alkalmazást, válassza a `TypeScript` lehetőséget a Function-alkalmazás létrehozásakor.

Amikor lenyomja az **F5** billentyűt az alkalmazás helyi futtatásához, a transpilation a gazdagép (a (z). exe) inicializálása előtt történik. 

Ha a Function alkalmazást az Azure-ban az **üzembe helyezés az alkalmazásban...** gomb használatával helyezi üzembe, a Azure functions-bővítmény először a JavaScript-fájlok éles környezetben való létrehozására kész buildjét hozza létre a géppel készített forrásfájlok közül.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A központi eszközök használata során többféleképpen is eltérhetnek a JavaScript-projekttől.

#### <a name="create-project"></a>Projekt létrehozása

Ha a Core Tools használatával szeretne létrehozni egy írógéppel Function app-projektet, a Function alkalmazás létrehozásakor meg kell adnia az írógéppel nyelvi beállítást. Ezt a következő módszerek egyikével teheti meg:

- Futtassa a `func init` parancsot, válassza a `node` elemet a nyelvi verem elemnél, majd válassza a `typescript` lehetőséget.

- Futtassa a következő parancsot: `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Helyi Futtatás

Ha a Function app-kódot helyileg szeretné futtatni a Core Tools használatával, használja a következő parancsokat a `func host start` helyett: 

```command
npm install
npm start
```

A `npm start` parancs egyenértékű a következő parancsokkal:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Közzététel az Azure platformon

Mielőtt a [`func azure functionapp publish`] parancsot használja az Azure-ba való üzembe helyezéshez, létrehoz egy, a JavaScript-fájlok gyártásra kész buildjét a géppel készített forrásfájlok közül. 

A következő parancsok a géppel készített projekt előkészítését és közzétételét teszik közzé a Core Tools használatával: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Ebben a parancsban cserélje le a `<APP_NAME>` értéket a Function alkalmazás nevével.

## <a name="considerations-for-javascript-functions"></a>A JavaScript-függvények szempontjai

Ha JavaScript-függvényekkel dolgozik, vegye figyelembe a következő részekben ismertetett szempontokat.

### <a name="choose-single-vcpu-app-service-plans"></a>VCPU App Service csomagok kiválasztása

Ha a App Service csomagot használó Function-alkalmazást hoz létre, azt javasoljuk, hogy egyetlen vCPU csomagot válasszon, nem pedig több vCPU rendelkező csomagot. Napjainkban a függvények a JavaScript-funkciókat hatékonyabban futtatják az vCPU virtuális gépeken, a nagyobb méretű virtuális gépek használata azonban nem eredményezi a várt teljesítményt. Ha szükséges, manuálisan is kibővítheti a további vCPU virtuálisgép-példányok hozzáadásával, vagy engedélyezheti az autoskálázást is. További információ: a [Példányszám manuális vagy automatikus skálázása](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Hidegindító

Azure Functions a kiszolgáló nélküli üzemeltetési modellben való fejlesztésekor a hideg indítás a valóság. A *hidegindító* kifejezés arra utal, hogy amikor a Function alkalmazás első alkalommal indul el egy adott időszak után, a folyamat hosszabb időt vesz igénybe. Ha a JavaScript-függvények nagy függőségi fákkal rendelkeznek, akkor a hideg indítás jelentős lehet. A hidegindító folyamat felgyorsításához [futtassa a függvényeket csomagfájlként](run-functions-from-deployment-package.md) , ha lehetséges. Számos üzembe helyezési módszer alapértelmezés szerint a Futtatás a csomag modelljével történik, de ha nagy mennyiségű hideg indul el, és nem fut így, ez a változás jelentős javulást nyújthat.

### <a name="connection-limits"></a>Kapcsolatok korlátai

Ha egy Azure Functions-alkalmazásban szolgáltatás-specifikus ügyfelet használ, ne hozzon létre új ügyfelet minden függvény meghívásával. Ehelyett hozzon létre egyetlen, statikus ügyfelet a globális hatókörben. További információ: [kapcsolatok kezelése Azure Functionsban](manage-connections.md).

### <a name="use-async-and-await"></a>@No__t-0 és `await` használata

A JavaScript-Azure Functions írásakor a `async` és a `await` kulcsszó használatával kell kódot írnia. A kód `async` és `await` helyett a visszahívások vagy a `.then`, valamint a `.catch` használata az ígéretekkel segít elkerülni a két gyakori problémát:
 - [A Node. js folyamat összeomlása](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)miatt fellépő nem kezelt kivételek eldobása, ami hatással van más függvények végrehajtására.
 - Váratlan viselkedés, például hiányzó naplók a Context. log naplófájlból, a nem megfelelően várt aszinkron hívások miatt.

Az alábbi példában a (z) `fs.readFile` aszinkron metódust egy hiba-első visszahívási függvény hívja meg második paramétereként. Ez a kód a fent említett problémákat is okozhatja. A megfelelő hatókörben nem kifejezetten bekövetkező kivétel összeomlott a teljes folyamaton (a probléma #1). A visszahívási függvény hatókörén kívüli @no__t – 0 hívása azt jelenti, hogy a függvény meghívása a fájl olvasása előtt megszűnik (probléma #2). Ebben a példában a `context.done()` túl korai eredményt hívja a hiányzó naplóbejegyzések `Data from file:` kezdetű bejegyzéseivel.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

A `async` és a `await` kulcsszó használata segít elkerülni mindkét hibát. A [(`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) Node. js segédprogrammal kapcsolja be a hiba-első visszahívási stílus függvényeit a várt függvényekbe.

Az alábbi példában a függvény végrehajtása során felmerülő kezeletlen kivételek csak a kivételt okozó egyéni hívás meghiúsulnak. A `await` kulcsszó azt jelenti, hogy csak a `readFile` befejezése után hajtja végre a következő `readFileAsync` műveletet. A `async` és a `await` esetében nem kell meghívnia a `context.done()` visszahívást.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    try {
        const data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Következő lépések

További információkért lásd a következőket:

+ [Azure Functions – ajánlott eljárások](functions-best-practices.md)
+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
+ [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)

["az Azure functionapp Publishing"]: functions-run-local.md#project-file-deployment
