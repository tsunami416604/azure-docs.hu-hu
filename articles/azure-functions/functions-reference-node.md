---
title: JavaScript fejlesztői útmutató az Azure Functionshez
description: Ismerje meg, hogyan fejleszthet funkciókat javascript használatával.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: 345df8e1ea88caa6f8dbe941245c1f989c3e81c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276828"
---
# <a name="azure-functions-javascript-developer-guide"></a>Az Azure Functions JavaScript fejlesztői útmutatója

Ez az útmutató az Azure Functions JavaScript-alapú írásának bonyolultságáról tartalmaz információkat.

A JavaScript függvény egy `function` exportált, amely akkor hajtvégre, amikor aktiválódik ([az eseményindítók a function.json fájlban vannak konfigurálva).](functions-triggers-bindings.md) Az első argumentum, amely `context` minden függvénynek átadott egy objektum, amely kötési adatok fogadására és küldésére, naplózásra és a futásidejű kommunikációra szolgál.

Ez a cikk feltételezi, hogy már elolvasta az [Azure Functions fejlesztői hivatkozását.](functions-reference.md) Az első függvény létrehozásához a Functions rövid útmutatót a [Visual Studio Code használatával](functions-create-first-function-vs-code.md) vagy a [portálon](functions-create-first-azure-function.md)hozza létre.

Ez a cikk a [TypeScript alkalmazásfejlesztést](#typescript)is támogatja.

## <a name="folder-structure"></a>Mappastruktúra

A JavaScript-projektekhez szükséges mappastruktúra a következőképpen néz ki. Ez az alapértelmezett lehet változtatni. További információt az alábbi [scriptFile](#using-scriptfile) szakaszban talál.

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

A projekt gyökerében van egy megosztott [host.json](functions-host-json.md) fájl, amely a függvényalkalmazás konfigurálásához használható. Minden függvény saját kódfájllal (.js) és kötési konfigurációs fájllal (function.json) rendelkezik. A `function.json`szülőkönyvtár neve mindig a függvény neve.

A Functions futásidejű [2.x-es verziójában](functions-versions.md) szükséges kötési `extensions.csproj` kiterjesztések a fájlban `bin` vannak definiálva, a mappában lévő tényleges könyvtárfájlokkal együtt. Helyi fejlesztés esetén kötelező kiterjesztéseket kell [regisztrálnia.](./functions-bindings-register.md#extension-bundles) Amikor funkciókat fejleszt az Azure Portalon, ez a regisztráció az Ön számára történik.

## <a name="exporting-a-function"></a>Függvény exportálása

A JavaScript függvényeket [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) a [`exports`](https://nodejs.org/api/modules.html#modules_exports)(vagy ) keresztül kell exportálni. Az exportált függvénynek egy JavaScript-függvénynek kell lennie, amely az aktiváláskor hajtható végre.

Alapértelmezés szerint a Functions futásidejű megkeresi a függvényt a alkalmazásban, `index.js`ahol `index.js` ugyanaz a szülőkönyvtár van, mint a megfelelő `function.json`. Az alapértelmezett esetben az exportált függvény legyen az egyetlen exportálás `run` a `index`fájlból, vagy az exportálás neve vagy . A függvény fájlhelyének és exportálási nevének konfigurálásához olvassa el [alább a függvény belépési pontjának konfigurálását.](functions-reference-node.md#configure-function-entry-point)

Az exportált függvény végrehajtása során számos argumentumot továbbít. Az első érv, ami `context` kell, mindig egy tárgy. Ha a funkció szinkron (nem ad vissza egy ígéret), `context` akkor át `context.done` kell adnia az objektumot, mivel a hívás szükséges a helyes használathoz.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Aszinkron függvény exportálása
Ha a [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarációvagy egyszerű JavaScript-ígéretek használata a Functions runtime 2.x-es verziójában, nem kell explicit módon hívnia a [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) [`context.done`](#contextdone-method) visszahívást annak jelzésére, hogy a funkció befejeződött. A függvény akkor fejeződik be, amikor az exportált aszinkron függvény/Ígéret befejeződik. Az 1.x-es verziót célzó függvények [`context.done`](#contextdone-method) esetében a kód végrehajtása után is meg kell hívnia.

A következő példa egy egyszerű függvény, amely naplózza, hogy az aktivált, és azonnal befejezi a végrehajtást.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Aszinkron függvény exportálásakor egy kimeneti kötést is `return` konfigurálhat az érték hez. Ez akkor ajánlott, ha csak egy kimeneti kötéssel rendelkezik.

Kimenet hozzárendeléséhez `return`a használatával `name` módosítsa `$return` `function.json`a tulajdonságot a alkalmazásban.

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
A JavaScript ben [a kötések](functions-triggers-bindings.md) egy függvény function.jsonjában vannak konfigurálva és definiálva. A függvények számos módon kommunikálnak a kötésekkel.

### <a name="inputs"></a>Bemenetek
A bemenetkét kategóriába sorolhatók az Azure Functionsben: az egyik az eseményindító bemenet, a másik pedig a további bemenet. Az eseményindítók és más `direction === "in"`bemeneti kötések (kötések) egy függvény által háromféleképpen olvashatók:
 - **_[Ajánlott]_ Ahogy paraméterek et adott át a függvénynek.** A függvény ugyanabban a sorrendben kerül át a függvénybe, mint a *function.json.* A `name` *function.json* ban definiált tulajdonságnak nem kell egyeznie a paraméter nevével, bár meg kell.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **A [`context.bindings`](#contextbindings-property) tárgy tagjaiként.** Minden tagot a `name` *function.json*ban definiált tulajdonság nevez el.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **A JavaScript-objektumot [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) használó bemenetekként.** Ez lényegében megegyezik a bemenetek paraméterekként való átadásával, de lehetővé teszi a bemenetek dinamikus kezelését.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Kimenetek
A kimenetek `direction === "out"`(kötések) egy függvény által többféleképpen írhatók. A `name` *function.json* ban meghatározott kötés tulajdonsága minden esetben megegyezik a függvényben írt objektumtag nevével. 

A kimeneti kötésekhez az alábbi módokon rendelhet adatokat (ne kombinálja ezeket a módszereket):

- **_[Több kimenethez ajánlott]_ Visszaadott egy tárgyat.** Ha aszinkron/ígéret-visszaad függvényt használ, visszaadhat egy objektumot hozzárendelt kimeneti adatokkal. Az alábbi példában a kimeneti kötések neve "httpResponse" és "queueOutput" a *function.json*.

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

  Ha szinkron függvényt használ, ezt az objektumot [`context.done`](#contextdone-method) a használatával is visszaadhatja (lásd a példát).
- **_[Egy kimenethez ajánlott]_ Érték visszaadása közvetlenül és a $return kötési név használatával.** Ez csak az async/Promise visszatérő függvényekesetén működik. Példa az [aszinkron függvény exportálására.](#exporting-an-async-function) 
- **Értékek hozzárendelése `context.bindings` ** Az értékeket közvetlenül a context.bindings-hez rendelheti.

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

A bemeneti kötés adattípusának meghatározásához használja a `dataType` tulajdonságot a kötési definícióban. Ha például egy HTTP-kérelem tartalmát bináris formátumban `binary`szeretné olvasni, használja a következő típust:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

A `dataType` következő `binary`lehetőségek `stream`közül `string`választhat: , , és.

## <a name="context-object"></a>környezet objektum
A futásidejű `context` egy objektum ot használ az adatok nak a függvénybe és a függvényből történő átvezetésére, valamint a futásidejű kommunikáció lehetővé teszi. A környezeti objektum használható a kötésekből származó adatok olvasására és `context.done` beállítására, naplók írására és a visszahívás használatára, ha az exportált függvény szinkron.

Az `context` objektum mindig egy függvény első paramétere. Be kell vonni, mert fontos `context.done` módszerekkel rendelkezik, mint például és `context.log`. Az objektumot tetszés szerint elnevezheti (például `ctx` vagy `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings tulajdonság

```js
context.bindings
```

Egy elnevezett objektumot ad vissza, amely kötési adatok olvasására vagy hozzárendelésére szolgál. A bemeneti és eseményindító kötési `context.bindings`adatok a tulajdonságok olvasásával érhetők el a programban. A kimenetkötési adatok úgy rendelhetők hozzá, hogy adatokat`context.bindings`

Például a function.json fájlban található következő kötésdefiníciók lehetővé `context.bindings.myInput` teszik a várólista tartalmának elérését, és a kimenetek hozzárendelését a várólistához a használatával. `context.bindings.myOutput`

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

Megadhatja a kimeneti kötési `context.done` adatokat `context.binding` az objektum helyett a módszerrel (lásd alább).

### <a name="contextbindingdata-property"></a>context.bindingData tulajdonság

```js
context.bindingData
```

Egy elnevezett objektumot ad vissza, amely`invocationId`eseményindító `sys.utcNow` `sys.randGuid`metaadatait és függvénymeghívási adatait ( , `sys.methodName`, , . ) tartalmazza. Az eseményindítók metaadatainak példáját lásd ebben az [eseményközpontokban.](functions-bindings-event-hubs-trigger.md)

### <a name="contextdone-method"></a>context.done metódus

```js
context.done([err],[propertyBag])
```

Lehetővé teszi, hogy a futásidejű tudja, hogy a kód befejeződött. Amikor a függvény [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) a deklarációt `context.done()`használja, nem kell használnia a ot. A `context.done` visszahívás implicit módon van hívva. Az Async függvények a 8-as vagy újabb verzióban érhetők el, amelyhez a Functions futásidő 2.x verziója szükséges.

Ha a függvény nem aszinkron függvény, **hívjon fel,** `context.done` hogy tájékoztassa a futásidejűt a funkció befejezéséről. A végrehajtás idővel kimarad, ha hiányzik.

A `context.done` módszer lehetővé teszi, hogy egy felhasználó által definiált hibát visszaadjon a futásidőnek és egy kimeneti kötési adatokat tartalmazó JSON-objektumot. Az `context.done` `context.bindings` objektumon beállított objektumok felülírásához átadott tulajdonságok.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log metódus  

```js
context.log(message)
```

Lehetővé teszi, hogy az alapértelmezett nyomkövetési szinten írjon a streamelési függvény naplóiba. A `context.log`, további naplózási módszerek állnak rendelkezésre, amelyek lehetővé teszik a függvénynaplók írását más nyomkövetési szinteken:


| Módszer                 | Leírás                                |
| ---------------------- | ------------------------------------------ |
| **error(_üzenet_)**   | Hibaszint-naplózásra vagy alacsonyabb szintre ír.   |
| **figyelmeztetés(_üzenet_)**    | A figyelmeztetési szint naplózására vagy az alacsonyabb szintre ír. |
| **info(_üzenet_)**    | Az információszintű naplózásba vagy az alacsonyabb szintbe ír.    |
| **verbose(_üzenet_)** | Részletes szintű naplózásba ír.           |

A következő példa naplót ír a figyelmeztetési nyomkövetés szintjén:

```javascript
context.log.warn("Something has happened."); 
```

Beállíthatja a host.json fájlban való [naplózás nyomkövetési küszöbértékét.](#configure-the-trace-level-for-console-logging) A naplók írásával kapcsolatos további információkért lásd az alábbi [nyomkövetési kimenetek írását.](#writing-trace-output-to-the-console)

Olvassa el [az Azure Functions figyelését,](functions-monitoring.md) ha többet szeretne megtudni a függvénynaplók megtekintéséről és lekérdezéséről.

## <a name="writing-trace-output-to-the-console"></a>Nyomkövetési kimenet írása a konzolra 

A Függvényekben a `context.log` módszerek segítségével nyomkövetési kimenetet írhat a konzolra. A Functions v2.x-ben `console.log` a függvényalkalmazás szintjén rögzítik a nyomkövetési kimeneteket. Ez azt jelenti, `console.log` hogy a kimenetek nem kötődnek egy adott függvény meghívásához, és nem jelennek meg egy adott függvény naplóiban. Ezek azonban az Application Insights propagálása. A Functions v1.x-ben `console.log` nem lehet írni a konzolra.

Híváskor `context.log()`az üzenet az alapértelmezett nyomkövetési szinten, az _információkövetési_ szinten jelenik meg a konzolon. A következő kód az info nyomkövetés szintjén ír a konzolra:

```javascript
context.log({hello: 'world'});  
```

Ez a kód egyenértékű a fenti kóddal:

```javascript
context.log.info({hello: 'world'});  
```

Ez a kód hibaszinten írja a konzolra:

```javascript
context.log.error("An error has occurred.");  
```

Mivel a _hiba_ a legmagasabb nyomkövetési szint, ez a nyomkövetés minden nyomkövetési szinten a kimenetre kerül, amíg a naplózás engedélyezve van.

Minden `context.log` módszer támogatja ugyanazt a paraméterformátumot, amelyet a Node.js [util.format metódus](https://nodejs.org/api/util.html#util_util_format_format)támogat. Vegye figyelembe a következő kódot, amely az alapértelmezett nyomkövetési szint használatával írja a függvénynaplókat:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Ugyanazt a kódot a következő formátumban is írhatja:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>A nyomkövetési szint konfigurálása a konzolnaplózáshoz

Az 1.x függvények segítségével meghatározhatja a konzolra írás küszöbértékének nyomkövetési szintjét, így egyszerűen szabályozhatja, hogy a függvényből hogyan írják a nyomkövetéseket a konzolra. A konzolra írt összes nyomkövetés küszöbértékének `tracing.consoleLevel` beállításához használja a host.json fájlban lévő tulajdonságot. Ez a beállítás a függvényalkalmazás összes függvényére vonatkozik. A következő példa a részletes naplózás engedélyezéséhez állítja be a nyomkövetési küszöbértéket:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

A **consoleLevel** értékei a `context.log` metódusok nevének felelnek meg. A konzolra történő összes nyomkövetési naplózás letiltásához állítsa ki a **consoleLevel** _kapcsolót._ További információ: [host.json reference](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP-eseményindítók és kötések

A HTTP- és webhook-eseményindítók és a HTTP-kimeneti kötések kérés- és válaszobjektumokat használnak a HTTP-üzenetküldés hez.  

### <a name="request-object"></a>Kérelem objektum

A `context.req` (kérés) objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás                                                    |
| ------------- | -------------------------------------------------------------- |
| _Szervezet_        | A kérelem törzsét tartalmazó objektum.               |
| _Fejlécek_     | A kérelemfejléceket tartalmazó objektum.                   |
| _Módszer_      | A kérelem HTTP-metódusa.                                |
| _originalUrl_ | A kérelem URL-címe.                                        |
| _params (params)_      | A kérelem útválasztási paramétereit tartalmazó objektum. |
| _Lekérdezés_       | A lekérdezési paramétereket tartalmazó objektum.                  |
| _rawBody (nyers test)_     | Az üzenet törzse karakterláncként.                           |


### <a name="response-object"></a>Válaszobjektum

A `context.res` (válasz) objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság  | Leírás                                               |
| --------- | --------------------------------------------------------- |
| _Szervezet_    | A válasz törzsét tartalmazó objektum.         |
| _Fejlécek_ | A válaszfejléceket tartalmazó objektum.             |
| _isRaw_   | Azt jelzi, hogy a program kihagyja a formázást a válaszban.    |
| _Állapot_  | A válasz HTTP-állapotkódja.                     |
| _Süti_ | Http cookie-objektumok tömbje, amelyek a válaszban vannak beállítva. A `name`HTTP cookie-objektumnak van egy , `value` `maxAge` , `sameSite`és más cookie-tulajdonsága, például vagy . |

### <a name="accessing-the-request-and-response"></a>A kérés és a válasz elérése 

Http-eseményindítókkal végzett munka során a HTTP-kérelem- és válaszobjektumokat többféleképpen is elérheti:

+ **Az `req` `res` objektum ból `context` és tulajdonságaiból.** Ily módon a hagyományos mintával http-adatokat érhet el a környezeti objektumból, ahelyett, hogy a teljes `context.bindings.name` mintát kellene használnia. A következő példa bemutatja, `res` hogyan `context`érheti el a `req` és az objektumokat a következőben:

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **A megnevezett bemeneti és kimeneti kötésekből.** Ily módon a HTTP-eseményindító és a kötések ugyanúgy működnek, mint bármely más kötés. A következő példa a válaszobjektumot `response` egy elnevezett kötés használatával állítja be: 

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
+ **_[Csak válasz]_ A `context.res.send(body?: any)`hívással .** Http-válasz jön létre `body` a bemeneti válasz törzseként. `context.done()`implicit módon hívják.

+ **_[Csak válasz]_ A `context.done()`hívással .** A HTTP-kötés egy speciális típusa a `context.done()` metódusnak adott választ adja vissza. A következő HTTP-kimeneti `$return` kötés egy kimeneti paramétert határoz meg:

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

## <a name="scaling-and-concurrency"></a>Méretezés és egyidejűség

Alapértelmezés szerint az Azure Functions automatikusan figyeli az alkalmazás terhelését, és szükség szerint további állomáspéldányokat hoz létre a Node.js számára. A függvények beépített (nem felhasználó által konfigurálható) küszöbértékeket használ a különböző eseményindító-típusokhoz, hogy eldöntsék, mikor vegyenek fel példányokat, például az üzenetek korát és a QueueTrigger várólistaméretét. További információ: [How the Consumption and Premium plans is work.](functions-scale.md#how-the-consumption-and-premium-plans-work)

Ez a skálázási viselkedés számos Node.js alkalmazás számára elegendő. A CPU-hoz kötött alkalmazások esetében tovább javíthatja a teljesítményt több nyelvi munkavégző folyamat használatával.

Alapértelmezés szerint minden Functions állomáspéldány egyetlen nyelvi munkavégző folyamattal rendelkezik. A alkalmazásbeállítás [FUNCTIONS_WORKER_PROCESS_COUNTsal](functions-app-settings.md#functions_worker_process_count) növelheti a gazdagépenkénti munkavégző folyamatok számát (legfeljebb 10). Az Azure Functions ezután megpróbálja egyenletesen elosztani az egyidejű függvénymeghívásokat ezek között a dolgozók között. 

A FUNCTIONS_WORKER_PROCESS_COUNT minden állomásra vonatkozik, amelyet a Functions létrehoz, amikor az alkalmazást az igényeknek megfelelően skálázhatja. 

## <a name="node-version"></a>Csomópont verziója

Az alábbi táblázat a Functions futásidejű ek aktuálisan támogatott Node.js verzióit mutatja be operációs rendszer szerint:

| Függvények verziója | Csomópont-verzió (Windows) | Csomópontverzió (Linux) |
|---|---| --- |
| 1,x | 6.11.2 (a futásidő zárolva) | n/a |
| 2,x  | ~8<br/>~10 (ajánlott)<br/>~12<sup>*</sup> | ~8 (ajánlott)<br/>~10  |
| 3.x | ~10<br/>~12 (ajánlott)  | ~10<br/>~12 (ajánlott) |

<sup>*</sup>A ~12 csomópont jelenleg a Functions futásidejű 2.x verzióján engedélyezett. A legjobb teljesítmény érdekében azonban azt javasoljuk, hogy a Functions 3.x-es verzióját használja ~12 csomóponttal. 

A futásidejű aktuális verzióját a fenti alkalmazásbeállítás ellenőrzésével vagy `process.version` bármely funkcióból történő nyomtatással láthatja. A WEBSITE_NODE_DEFAULT_VERSION [alkalmazásbeállítás](functions-how-to-use-azure-function-app-settings.md#settings) sal a támogatott LTS-verzióra, `~10`például a verzióra.

## <a name="dependency-management"></a>Függőségkezelés
Annak érdekében, hogy a közösségi könyvtárak a JavaScript-kód, amint az az alábbi példában látható, győződjön meg arról, hogy minden függőség telepítve van a függvényalkalmazás az Azure-ban.

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
> Meg kell `package.json` határoznia egy fájlt a függvényalkalmazás gyökerében. A fájl definiálása lehetővé teszi, hogy az alkalmazás összes funkciója ugyanazt a gyorsítótárazott csomagot ossza meg, ami a legjobb teljesítményt nyújtja. Ha verzióütközés merül fel, feloldhatja `package.json` azt egy adott függvény mappájában lévő fájl hozzáadásával.  

A függvényalkalmazások forrásvezérlőből történő telepítésekor a tárházban lévő bármely `package.json` fájl a központi telepítés során elindít egy `npm install` fájlt a mappájában. De a portálon vagy a CLI-n keresztül történő üzembe helyezéskor manuálisan kell telepítenie a csomagokat.

Kétféleképpen telepíthet csomagokat a Függvényalkalmazásba: 

### <a name="deploying-with-dependencies"></a>Üzembe helyezés függőségekkel
1. Telepítse az összes szükséges csomagot helyileg a futtatásával. `npm install`

2. Telepítse a kódot, és `node_modules` győződjön meg arról, hogy a mappa szerepel a központi telepítésben. 


### <a name="using-kudu"></a>A Kudu használata
1. Nyissa meg a következőt: `https://<function_app_name>.scm.azurewebsites.net`.

2. Kattintson **a Debug Console** > **CMD gombra.**

3. Nyissa `D:\home\site\wwwroot`meg a t, majd húzza a package.json fájlt a lap felső felében található **wwwroot** mappába.  
    Más módon is feltölthet fájlokat a függvényalkalmazásba. További információt a [Függvényalkalmazás-fájlok frissítése](functions-reference.md#fileupdate)című témakörben talál. 

4. A package.json fájl feltöltése `npm install` után futtassa a parancsot a **Kudu távoli végrehajtási konzolon.**  
    Ez a művelet letölti a package.json fájlban jelzett csomagokat, és újraindítja a függvényalkalmazást.

## <a name="environment-variables"></a>Környezeti változók

A Funkciók alkalmazásban [az alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatáskapcsolati karakterláncok környezeti változókként jelennek meg a végrehajtás során. Ezeket a beállításokat a `process.env`használatával érheti el, `context.log()` amint `AzureWebJobsStorage` az `WEBSITE_SITE_NAME` itt a második és a harmadik hívásban látható, ahol naplózjuk a környezeti változókat:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ha helyileg fut, a program a [local.settings.json](functions-run-local.md#local-settings-file) projektfájlból olvassa be az alkalmazásbeállításokat.

## <a name="configure-function-entry-point"></a>Függvény belépési pontjának konfigurálása

A `function.json` `scriptFile` tulajdonságok, és `entryPoint` az exportált függvény helyének és nevének konfigurálására használható. Ezek a tulajdonságok fontosak lehetnek, ha a JavaScript transpiled.

### <a name="using-scriptfile"></a>Az `scriptFile` használata

Alapértelmezés szerint a Program JavaScript-függvényt hajt végre a program `index.js` `function.json`ból, amely nek ugyanaz a szülőkönyvtára van, mint a megfelelőnek.

`scriptFile`a következő példához hasonló mappastruktúra levételére használható:

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

A `function.json` `myNodeFunction` for tartalmaznia kell egy `scriptFile` tulajdonságot, amely a fájlra mutat, és az exportált függvény fut.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Az `entryPoint` használata

A `scriptFile` (vagy `index.js`) függvényt a `module.exports` megtaláláshoz és a futtatáshoz a funkció használatával kell exportálni. Alapértelmezés szerint az aktiváláskor végrehajtható függvény az egyetlen exportálás a fájlból, az exportálás neve `run`vagy az exportálás neve. `index`

Ez a következő `entryPoint` `function.json`példában látható módon konfigurálható a alkalmazásban:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

A Functions v2.x,amely `this` támogatja a paramétert a felhasználói funkciók, a függvénykód ezután lehet, mint a következő példában:

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

Ebben a példában fontos megjegyezni, hogy bár egy objektum exportálása folyamatban van, nincs garancia a végrehajtások közötti állapot megőrzésére.

## <a name="local-debugging"></a>Helyi hibakeresés

A paraméterrel `--inspect` való indításkor a Node.js folyamat figyel egy hibakereső ügyfelet a megadott porton. Az Azure Functions 2.x-ben megadhatja a kód ot futtató Node.js folyamatba átadandó argumentumokat a környezeti változó vagy az alkalmazásbeállítás hozzáadásával. `languageWorkers:node:arguments = <args>` 

A helyi hibakereséshez `"languageWorkers:node:arguments": "--inspect=5858"` `Values` adja hozzá a [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) fájl alá, és csatoljon egy hibakeresőt az 5858-as porthoz.

A VS-kód használatával történő hibakeresés során a `--inspect` paraméter automatikusan hozzáadódik a `port` projekt launch.json fájljának értékével.

Az 1.x verzióban a beállítás `languageWorkers:node:arguments` nem fog működni. A hibakeresési port az [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) Azure Functions Core Tools paraméterével választható ki.

## <a name="typescript"></a>TypeScript

A Functions futásidejű 2.x-es verziójának megcélzásakor az [Azure Functions for Visual Studio Code](functions-create-first-function-vs-code.md) és az Azure Functions Core [Tools](functions-run-local.md) egyaránt lehetővé teszi, hogy függvényalkalmazásokat hozzon létre egy TypeScript függvényalkalmazás-projekteket támogató sablon használatával. A sablon `package.json` olyan `tsconfig.json` fájlokat hoz létre és vetít ki fájlokat, amelyek megkönnyítik a JavaScript-függvények áthalmozását, futtatását és közzétételét typescript kódból ezekkel az eszközökkel.

A létrehozott `.funcignore` fájl jelzi, hogy mely fájlok at zárják ki, amikor egy projektet közzétesznek az Azure-ban.  

A TypeScript-fájlok (.ts) javascript fájlokká (.js) kerülnek a `dist` kimeneti könyvtárba. A TypeScript-sablonok a `function.json` `dist` [ `scriptFile` paraméterrel](#using-scriptfile) jelzik a megfelelő .js fájl helyét a mappában. A kimeneti helyet a sablon `outDir` állítja `tsconfig.json` be a fájlban lévő paraméter használatával. Ha módosítja ezt a beállítást vagy a mappa nevét, a futásidejű nem találja a futtatandó kódot.

> [!NOTE]
> A TypeScript kísérleti támogatása a Functions futásidejű 1.x verziója. A kísérleti verzió transpiles TypeScript fájlokat JavaScript fájlokat, amikor a függvény meghívása. A 2.x-es verzióban ezt a kísérleti támogatást leváltotta az eszközvezérelt módszer, amely a gazdagép inicializálása előtt és a telepítési folyamat során transzpilálást végez.

A TypeScript-projektből történő helyi fejlesztés és üzembe helyezés módja a fejlesztői eszköztől függ.

### <a name="visual-studio-code"></a>Visual Studio Code

Az [Azure Functions for Visual Studio Kód](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) bővítmény lehetővé teszi a függvények fejlesztését typescript használatával. A Core Tools az Azure Functions bővítmény követelménye.

Ha TypeScript függvényalkalmazást szeretne létrehozni a `TypeScript` Visual Studio-kódban, válassza ki a nyelvet a függvényalkalmazás létrehozásakor.

Amikor az **F5** billentyű lenyomásával helyileg futtatja az alkalmazást, a transpilálás a gazdagép (func.exe) inicializálása előtt történik. 

Amikor a függvényalkalmazást az Azure-ba telepíti a **Deploy to function alkalmazás használatával...** gomb, az Azure Functions bővítmény először létrehoz egy éles használatra kész JavaScript-fájlokat a TypeScript-forrásfájlokból.

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

A Core Tools használata során számos módon különbözik egy TypeScript-projekt a JavaScript-projektektől.

#### <a name="create-project"></a>Projekt létrehozása

Ha TypeScript függvényalkalmazás-projektet szeretne létrehozni a Core Tools eszközzel, a függvényalkalmazás létrehozásakor meg kell adnia a TypeScript nyelv beállítást. Ezt az alábbi módokon teheti meg:

- Futtassa `func init` a `node` parancsot, jelölje ki `typescript`a nyelvi veremként, majd válassza a lehetőséget.

- Futtassa a következő parancsot: `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Helyi futtatás

Ha a függvényalkalmazás kódját helyileg szeretné futtatni a `func host start`Core Tools eszközökkel, használja a következő parancsokat a következő kulccsal: 

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

Mielőtt a [`func azure functionapp publish`] paranccsal üzembe helyezne az Azure-ba, hozzon létre egy éles használatra kész JavaScript-fájlokat a TypeScript forrásfájlokból. 

A következő parancsok előkészítik és közzéteszik a TypeScript-projektet a Core Tools segítségével: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Ebben a parancsban cserélje le `<APP_NAME>` a függvényalkalmazás nevét.

## <a name="considerations-for-javascript-functions"></a>JavaScript-függvényekkel kapcsolatos szempontok

Ha JavaScript-függvényekkel dolgozik, vegye figyelembe a következő szakaszokban található szempontokat.

### <a name="choose-single-vcpu-app-service-plans"></a>Válassza ki az egyvCPU-s App Service-csomagokat

Ha olyan függvényalkalmazást hoz létre, amely az App Service-csomagot használja, azt javasoljuk, hogy a több vCPU-val rendelkező csomag helyett egy egyvCPU-csomagot válasszon. Ma a Functions hatékonyabban futtatja a JavaScript-függvényeket egyvirtuálisos cpu-s virtuális gépeken, és a nagyobb virtuális gépek használata nem eredményezi a várt teljesítményjavulást. Szükség esetén manuálisan is felskálázhatja további egyvCPU virtuálisgép-példányok hozzáadásával, vagy engedélyezheti az automatikus skálázást. További információt a [Példányszám manuális vagy automatikus méretezése című témakörben talál.](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json)

### <a name="cold-start"></a>Hidegindítás

Az Azure Functions kiszolgáló nélküli üzemeltetési modellben történő fejlesztése során a hidegindítások valóságnak valóak. *A hidegindítás* arra utal, hogy amikor a függvényalkalmazás először elindul egy inaktív időszak után, az indítás hosszabb időt vesz igénybe. A JavaScript-függvények esetében különösen nagy függőségi fákkal a hidegindítás jelentős lehet. A hidegindítási folyamat felgyorsítása érdekében ha lehetséges, [futtassa a funkciókat csomagfájlként.](run-functions-from-deployment-package.md) Számos telepítési módszer alapértelmezés szerint a csomagmodellből való futtatást használja, de ha nagy hidegindítást tapasztal, és nem így fut, ez a változás jelentős javulást kínálhat.

### <a name="connection-limits"></a>Csatlakozási korlátok

Ha egy Azure Functions-alkalmazásban szolgáltatásspecifikus ügyfelet használ, ne hozzon létre új ügyfelet minden függvénymeghívással. Ehelyett hozzon létre egy, statikus ügyfelet a globális hatókörben. További információt a Kapcsolatok kezelése az Azure Functions ben című [témakörben talál.](manage-connections.md)

### <a name="use-async-and-await"></a>Használja `async` és`await`

Amikor Az Azure Functions javascriptben írásakor `await` kódot kell írnia a és a `async` kulcsszavak használatával. A kód `async` `await` írása visszahívások `.then` `.catch` vagy ígéretek helyett segít elkerülni két gyakori problémát:
 - A [Node.js folyamatot összeomló,](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)fel nem fogott kivételek kidobása, amelyek hatással lehetnek más függvények végrehajtására.
 - Nem várt viselkedés, például a context.log naplóból hiányzó naplók, amelyeket nem megfelelően várt aszinkron hívások okoznak.

Az alábbi példában az aszinkron `fs.readFile` metódus meghívása egy hiba-első visszahívási függvény, mint a második paraméter. Ez a kód okozza a fent említett mindkét kérdést. Egy kivétel, amely nem kifejezetten fogott a megfelelő hatókör összeomlott a teljes folyamat (kérdés #1). A `context.done()` visszahívási függvény hatókörén kívüli hívás azt jelenti, hogy a függvény meghívása befejeződhet a fájl olvasása előtt (probléma #2). Ebben a példában a túl korai `context.done()` hívás `Data from file:`a hiányzó naplóbejegyzéseket eredményezi, amelyek a programmal kezdődnek.

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

A `async` kulcsszavak `await` és a kulcsszavak használata segít elkerülni mindkét hibát. A Node.js segédprogrammal [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) a hiba-első visszahívási stílusú függvényeket a várt függvényekké alakíthatja.

Az alábbi példában a függvény végrehajtása során nem kezelt kivételek csak a kivételt kiemelő egyéni meghívássikertelenek. A `await` kulcsszó azt `readFileAsync` jelenti, `readFile` hogy a következő lépések csak a végrehajtás után fejeződnek be. A `async` `await`és a lehetőséggel nem kell `context.done()` hívnia a visszahívást.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

+ [Azure Functions – ajánlott eljárások](functions-best-practices.md)
+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
+ [Az Azure Functions aktiválódik és kötéseket köt](functions-triggers-bindings.md)

["func azure functionapp közzé"]: functions-run-local.md#project-file-deployment
