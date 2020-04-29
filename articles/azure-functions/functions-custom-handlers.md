---
title: Azure Functions egyéni kezelők (előzetes verzió)
description: Megtudhatja, hogyan használhatja a Azure Functionst bármilyen nyelvi vagy futtatókörnyezeti verzióval.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479253"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions egyéni kezelők (előzetes verzió)

Minden functions alkalmazást egy nyelvspecifikus kezelő hajt végre. Habár a Azure Functions alapértelmezés szerint számos [nyelvi kezelőt](./supported-languages.md) támogat, vannak olyan esetek, amikor további ellenőrzésre van szükség az alkalmazás végrehajtási környezetében. Az egyéni kezelők ezt a további vezérlést adják meg.

Az egyéni kezelők olyan egyszerű webkiszolgálók, amelyek eseményeket fogadnak a functions gazdagépről. Minden olyan nyelv, amely támogatja a HTTP primitíveket, egyéni kezelőt valósíthat meg.

Az egyéni kezelők a legmegfelelőbbek olyan helyzetekben, amikor a következőket kívánja használni:

- Functions-alkalmazás implementálása a hivatalosan támogatott nyelveken túli nyelveken
- Functions-alkalmazás implementálása nyelvi verzióban vagy futtatókörnyezetben alapértelmezés szerint nem támogatott
- Az alkalmazás végrehajtási környezetének részletes szabályozása

Az egyéni kezelők esetében az összes [eseményindító és bemeneti és kimeneti kötés](./functions-triggers-bindings.md) támogatott a [bővítmények](./functions-bindings-register.md)használatával.

## <a name="overview"></a>Áttekintés

Az alábbi ábrán a functions gazdagép és egy egyéni kezelőként megvalósított webkiszolgáló közötti kapcsolat látható.

![Azure Functions egyéni kezelő – áttekintés](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Az események a functions gazdagépnek küldenek kérelmet. Az esemény vagy egy nyers HTTP-adattartalom (a kötések nélküli HTTP által aktivált függvények esetén), vagy egy olyan hasznos adat, amely a függvény bemeneti kötési adatait tárolja.
- A functions gazdagép ezt követően a kérést a webkiszolgálónak küldi el a [kérelem hasznos](#request-payload)adatainak kibocsátásával.
- A webkiszolgáló végrehajtja az egyes függvényeket, és visszaküldi a functions gazdagépnek a [Válasz adattartalmát](#response-payload) .
- A functions gazdagép a választ a célként megadott kimeneti kötési adattartalomként adja meg.

Az egyéni kezelőként megvalósított Azure Functions alkalmazásoknak néhány konvenciónak megfelelően kell konfigurálniuk a *Host. JSON* és a *function. JSON* fájlokat.

## <a name="application-structure"></a>Alkalmazás szerkezete

Az egyéni kezelők megvalósításához a következő szempontokat kell figyelembe vennie az alkalmazásban:

- Egy *Host. JSON* fájl az alkalmazás gyökérkönyvtárában
- *Function. JSON* fájl minden függvényhez (a függvény nevével megegyező mappában)
- Egy webkiszolgálót futtató parancs, parancsfájl vagy végrehajtható fájl

Az alábbi ábrán látható, hogyan jelennek meg a fájlok a "Order" nevű függvény fájlrendszerében.

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuration

Az alkalmazás a *Host. JSON* fájl használatával van konfigurálva. Ez a fájl közli a functions-gazdagépgel, hogy hová kell elküldeni a kérelmeket, ha a HTTP-események feldolgozására képes webkiszolgálóra mutat.

Az egyéni kezelőt a *Host. JSON* fájl konfigurálásával határozzák meg, amely a webkiszolgáló `httpWorker` szakaszon keresztüli futtatásának részleteit tartalmazza.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

A `httpWorker` szakasz a által meghatározott célra mutat `defaultExecutablePath`. A végrehajtási cél lehet egy parancs, végrehajtható fájl vagy fájl, amelyben a webkiszolgáló implementálva van.

A megírt alkalmazások `defaultExecutablePath` esetében a parancsfájl nyelvi futtatókörnyezetére mutat, és `defaultWorkerPath` a parancsfájl helyére mutat. Az alábbi példa bemutatja, hogyan van konfigurálva egy JavaScript-alkalmazás a Node. js-ben egyéni kezelőként.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Argumentumokat a `arguments` tömb használatával is át lehet adni:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Számos hibakeresési beállításhoz argumentumok szükségesek. További részletekért tekintse meg a [hibakeresés](#debugging) szakaszt.

> [!NOTE]
> A *Host. JSON* fájlnak a futó webkiszolgálóként azonos szinten kell lennie a címtár-struktúrában. Előfordulhat, hogy egyes nyelvek és eszközlánccal alapértelmezés szerint nem helyezik el ezt a fájlt az alkalmazás gyökerébe.

#### <a name="bindings-support"></a>Kötések támogatása

A standard triggerek és a bemeneti és kimeneti kötések a *Host. JSON* fájlban lévő [kiterjesztési csomagokra](./functions-bindings-register.md) hivatkozva érhetők el.

### <a name="function-metadata"></a>Függvény metaadatainak

Egyéni kezelővel való használat esetén a *function. JSON* tartalma nem különbözik a függvények más környezetekben való definiálásának módjától. Az egyetlen követelmény, hogy a *function. JSON* fájloknak egy nevű mappában kell lenniük, hogy egyezzenek a függvény nevével.

### <a name="request-payload"></a>Kérelem tartalma

A tiszta HTTP-függvények kérelme a nyers HTTP-kérelem hasznos adattartalma. A Pure HTTP függvények olyan függvényként vannak definiálva, amely nem rendelkezik bemeneti vagy kimeneti kötésekkel, és amelyek HTTP-választ adnak vissza.

Bármilyen más típusú függvény, amely tartalmazza a bemenetet, a kimeneti kötéseket vagy a HTTP-n kívüli eseményindítót, egyéni kérelem-adattartalommal rendelkezik.

A következő kód egy példaként szolgáló kérelem hasznos adatait jelöli. Az adattartalom tartalmaz egy JSON-struktúrát két `Data` taggal `Metadata`: és.

A `Data` tag olyan kulcsokat tartalmaz, amelyek megfelelnek a bemeneti és a trigger neveknek a *function. JSON* fájl kötések tömbében meghatározottak szerint.

A `Metadata` tag [az eseményforrás által generált metaadatokat](./functions-bindings-expressions-patterns.md#trigger-metadata)tartalmaz.

A következő *function. JSON* fájlban megadott kötések miatt:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

A rendszer az ehhez a példához hasonló kérelem-adattartalmat ad vissza:

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>Válasz hasznos adat

Az egyezmény szerint a függvények válaszai kulcs/érték párokként vannak formázva. A támogatott kulcsok a következők:

| <nobr>Hasznos adatok kulcsa</nobr>   | Adattípus | Megjegyzések                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | A `bindings` *function. JSON* fájl tömbje által meghatározott válasz értékeket tartalmazza.<br /><br />Ha például egy függvény egy "blob" nevű blob Storage kimeneti kötéssel van konfigurálva, akkor `Outputs` a egy nevű `blob`kulcsot tartalmaz, amely a blob értékére van állítva. |
| `Logs`        | tömb     | Az üzenetek megjelennek a függvények hívási naplóiban.<br /><br />Az Azure-ban futtatott üzenetek Application Insightsban jelennek meg. |
| `ReturnValue` | sztring    | Arra szolgál, hogy a *függvény. JSON* fájljában a `$return` kimenet beállításakor választ adjon. |

Tekintse [meg a minta hasznos](#bindings-implementation)adatokat bemutató példát.

## <a name="examples"></a>Példák

Az egyéni kezelők bármilyen nyelven telepíthetők, amely támogatja a HTTP-eseményeket. Habár Azure Functions [teljes mértékben támogatja a JavaScriptet és a Node. js](./functions-reference-node.md)-t, az alábbi példák bemutatják, hogyan implementálhat egy egyéni kezelőt a Node. js-ben a JavaScript használatával az útmutatás céljából.

> [!TIP]
> Az egyéni kezelők más nyelveken való megvalósításának megismerése közben az itt látható Node. js-alapú példák akkor is hasznosak lehetnek, ha a Node. js nem támogatott verziójában szeretné futtatni a functions alkalmazást.

## <a name="http-only-function"></a>Csak HTTP-függvény

Az alábbi példa bemutatja, hogyan konfigurálhat egy HTTP által aktivált függvényt további kötések és kimenetek nélkül. Az ebben a példában megvalósított forgatókönyv egy nevű `http` függvényt tartalmaz, `GET` amely `POST` a vagy a paramétert fogadja.

A következő kódrészlet azt mutatja be, hogyan áll a függvényre irányuló kérés.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Megvalósítás

A *http*nevű mappában a *function. JSON* fájl konfigurálja a http által aktivált függvényt.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

A függvény a (z) `GET` és `POST` a kérelmek fogadására van konfigurálva, és az eredmény értékét egy `res`nevű argumentummal kell megadnia.

Az alkalmazás gyökerében a *Host. JSON* fájl a Node. js futtatására van konfigurálva, és rámutat a `server.js` fájlra.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

A fájlkiszolgáló *. js* fájl egy webkiszolgálót és egy http-függvényt valósít meg.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

Ebben a példában az expressz használatával webkiszolgálót hozhat létre a HTTP-események kezeléséhez, és a a `FUNCTIONS_HTTPWORKER_PORT`-on keresztüli kérelmek figyelésére van beállítva.

A függvény a következő elérési úton van `/hello`definiálva:. `GET`a kérések kezelése egy egyszerű JSON-objektum visszaadása `POST` révén történik, és a kérések a `req.body`kérések törzséhez is hozzáférnek a használatával.

A Order függvény útvonala itt van `/hello` , és nem `/api/hello` , mert a functions gazdagép a kérést az egyéni kezelőhöz rendeli.

>[!NOTE]
>A `FUNCTIONS_HTTPWORKER_PORT` nem a függvény meghívásához használt nyilvános port. A functions gazdagép ezt a portot használja az egyéni kezelő meghívásához.

## <a name="function-with-bindings"></a>Függvény kötésekkel

Az ebben a példában megvalósított forgatókönyv egy nevű `order` függvényt tartalmaz, `POST` amely a termék megrendelését jelképező hasznos adatokat fogad el. Mivel a függvény elküld egy megrendelést, létrejön egy Queue Storage üzenet, és a rendszer egy HTTP-választ ad vissza.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Megvalósítás

A *sorrend*nevű mappában a *function. JSON* fájl konfigurálja a http által aktivált függvényt.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

Ez a függvény olyan http- [triggert használó függvényként](./functions-bindings-http-webhook-trigger.md) van meghatározva, amely [http-választ](./functions-bindings-http-webhook-output.md) ad vissza, és egy [üzenetsor-tárolási](./functions-bindings-storage-queue-output.md) üzenetet küld.

Az alkalmazás gyökerében a *Host. JSON* fájl a Node. js futtatására van konfigurálva, és rámutat a `server.js` fájlra.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

A fájlkiszolgáló *. js* fájl egy webkiszolgálót és egy http-függvényt valósít meg.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

Ebben a példában az expressz használatával webkiszolgálót hozhat létre a HTTP-események kezeléséhez, és a a `FUNCTIONS_HTTPWORKER_PORT`-on keresztüli kérelmek figyelésére van beállítva.

A függvény a következő elérési úton van `/order` definiálva:.  A Order függvény útvonala itt van `/order` , és nem `/api/order` , mert a functions gazdagép a kérést az egyéni kezelőhöz rendeli.

Mivel `POST` a rendszer a kérelmeket elküldi erre a függvénybe, az adat néhány ponton elérhetővé válnak:

- A kérelem törzse a-on keresztül érhető el`req.body`
- A függvénynek közzétett adat a következőn keresztül érhető el`req.body.Data.req.Body`

A függvény válasza egy kulcs/érték párokba van formázva, `Outputs` ahol a tag olyan JSON-értéket tartalmaz, amelyben a kulcsok megfelelnek a *function. JSON* fájlban meghatározott kimeneteknek.

Ha a `message` kérelemben szereplő üzenettel egyenlő értéket ad meg, és `res` a várt http-válaszra van állítva, akkor ez a függvény egy üzenetet küld Queue Storage és egy http-választ ad vissza.

## <a name="debugging"></a>Hibakeresés

A functions egyéni kezelő alkalmazás hibakereséséhez hozzá kell adnia a nyelv és a futtatókörnyezet megfelelő argumentumait a hibakeresés engedélyezéséhez.

Egy Node. js-alkalmazás hibakereséséhez például a `--inspect` jelzőt a *Host. JSON* fájlban lévő argumentumként adja át a rendszer.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> A hibakeresési konfiguráció a *Host. JSON* fájl részét képezi, ami azt jelenti, hogy előfordulhat, hogy el kell távolítania a néhány argumentumot az éles környezetbe való üzembe helyezés előtt.

Ezzel a konfigurációval elindíthatja a függvény gazdagépének folyamatát a következő parancs használatával:

```bash
func host start
```

A folyamat elindítása után a hibakereső és a találati töréspontok is csatolhatók.

### <a name="visual-studio-code"></a>Visual Studio Code

A következő példa egy minta-konfiguráció, amely bemutatja, hogyan állíthatja be a *Launch. JSON* fájlt az alkalmazás a Visual Studio Code debuggerhez való összekapcsolásához.

Ez a példa a Node. js-re vonatkozik, ezért előfordulhat, hogy módosítania kell ezt a példát más nyelvekre vagy futtatókörnyezetekre.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Telepítése

Az egyéni kezelők szinte minden Azure Functions üzemeltetési lehetőséghez üzembe helyezhetők (lásd a [korlátozásokat](#restrictions)). Ha a kezelő egyéni függőségeket (például nyelvi futtatókörnyezetet) igényel, előfordulhat, hogy [Egyéni tárolót](./functions-create-function-linux-custom-image.md)kell használnia.

## <a name="restrictions"></a>Korlátozások

- A Linux-fogyasztási csomagok nem támogatják az egyéni kezelőket.
- A webkiszolgálónak 60 másodpercen belül el kell indulnia.

## <a name="samples"></a>Példák

Tekintse át az [Egyéni kezelő a GitHub](https://github.com/Azure-Samples/functions-custom-handlers) -tárházat, amely példákat mutat be a függvények különböző nyelveken való megvalósítására.
