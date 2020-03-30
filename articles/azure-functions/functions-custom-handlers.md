---
title: Az Azure Functions egyéni kezelői (előzetes verzió)
description: Ismerje meg, hogyan használhatja az Azure Functions bármely nyelvi vagy futásidejű verziót.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479253"
---
# <a name="azure-functions-custom-handlers-preview"></a>Az Azure Functions egyéni kezelői (előzetes verzió)

Minden Functions alkalmazást egy nyelvspecifikus kezelő hajt végre. Bár az Azure Functions alapértelmezés szerint számos [nyelvi kezelőt](./supported-languages.md) támogat, előfordulhat, hogy további szabályozást szeretne az alkalmazásvégrehajtási környezet felett. Az egyéni kezelők ezt a további vezérlőt biztosítják.

Az egyéni kezelők olyan könnyű webkiszolgálók, amelyek eseményeket fogadnak a Functions gazdagéptől. A HTTP primitíveket támogató nyelvek egyéni kezelőt valósíthatnak meg.

Az egyéni kezelők a legalkalmasabbak olyan helyzetekre, amikor a következőket szeretné:

- Functions alkalmazás megvalósítása a hivatalosan támogatott nyelveken túli nyelven
- Functions alkalmazás megvalósítása olyan nyelvi verzióban vagy futásidőben, amelyet alapértelmezés szerint nem támogatnak
- Részletes vezérléssel az alkalmazásvégrehajtási környezet felett

Az egyéni kezelők, az összes [eseményindítók és a bemeneti és kimeneti kötések](./functions-triggers-bindings.md) támogatja [a kiterjesztés kötegek.](./functions-bindings-register.md)

## <a name="overview"></a>Áttekintés

Az alábbi ábra a Functions állomás és az egyéni kezelőként megvalósított webkiszolgáló közötti kapcsolatot mutatja be.

![Az Azure Functions egyéni kezelőjének áttekintése](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Az események a Functions-állomásnak küldött kérelmet váltanak ki. Az esemény egy nyers HTTP-hasznos adat (a HTTP-aktivált függvények kötések nélkül), vagy egy hasznos, amely rendelkezik a bemeneti kötési adatokat a függvény.
- A Functions állomás ezután a kérést a webkiszolgálóhoz adja meg egy [kérelem hasznos adatának](#request-payload)kibocsátásával.
- A webkiszolgáló végrehajtja az egyes függvényt, és visszaküldi a függvények függvényének [a válasz hasznos adatát.](#response-payload)
- A Functions gazdagép a választ kimeneti kötési hasznos adatként nevezi meg a célhoz.

Az egyéni kezelőként megvalósított Azure Functions-alkalmazásnak néhány konvenció szerint konfigurálnia kell a *host.json* és *a function.json* fájlokat.

## <a name="application-structure"></a>Alkalmazási struktúra

Egyéni kezelő megvalósításához az alkalmazás következő szempontokra van szükség:

- *Host.json* fájl az alkalmazás gyökerében
- *Function.json* fájl minden függvényhez (a függvény nevének megfelelő mappán belül)
- Webkiszolgálót futtató parancs, parancsfájl vagy végrehajtható fájl

Az alábbi ábra bemutatja, hogyan néznek ki ezek a fájlok a fájlrendszerben egy "sorrend" nevű függvényhez.

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Konfiguráció

Az alkalmazás a *host.json* fájlon keresztül van konfigurálva. Ez a fájl megmondja a Functions állomásnak, hogy hová küldjön kéréseket, ha egy HTTP-események feldolgozására képes webkiszolgálóra mutat.

Az egyéni kezelő a *host.json* fájl konfigurálásával határozható meg, amely `httpWorker` részletezi, hogyan kell futtatni a webkiszolgálót a szakaszon keresztül.

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

A `httpWorker` szakasz a. `defaultExecutablePath`által meghatározott célra mutat. A végrehajtási cél lehet parancs, végrehajtható fájl vagy fájl, ahol a webkiszolgáló tvalósítja meg.

Parancsfájlalapú alkalmazások `defaultExecutablePath` esetén a parancsfájl nyelvének futásidejére mutat, és `defaultWorkerPath` a parancsfájl helyére. A következő példa bemutatja, hogyan van konfigurálva egy JavaScript-alkalmazás a Node.js-ben egyéni kezelőként.

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

Az argumentumokat a `arguments` tömb használatával is átadhatja:

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

Az argumentumok számos hibakeresési beállításhoz szükségesek. További részleteket a [Hibakeresés](#debugging) című részben ismer.

> [!NOTE]
> A *host.json* fájlnak a könyvtárstruktúrában a futó webkiszolgálóval azonos szinten kell lennie. Előfordulhat, hogy egyes nyelvek és eszközláncok alapértelmezés szerint nem helyezik el ezt a fájlt az alkalmazás gyökérhelyére.

#### <a name="bindings-support"></a>Kötések támogatása

Szabványos eseményindítók mellett bemeneti és kimeneti kötések érhetők el hivatkozva [kiterjesztés kötegek](./functions-bindings-register.md) a *host.json* fájlban.

### <a name="function-metadata"></a>Függvény metaadatai

Ha egyéni kezelővel használja, a *function.json* tartalom nem különbözik attól, ahogyan bármely más környezetben definiálna egy függvényt. Az egyetlen követelmény az, hogy *a function.json* fájloknak a függvény nevének megfelelő nevű mappában kell lenniük.

### <a name="request-payload"></a>Hasznos adat kérése

A kérelem hasznos a tiszta HTTP-függvények a nyers HTTP-kérelem hasznos adat. A tiszta HTTP-függvények bemeneti vagy kimeneti kötések nélküli függvényekként vannak definiálva, amelyek HTTP-választ adnak vissza.

Bármely más típusú függvény, amely bemeneti, kimeneti kötéseket tartalmaz, vagy http-től eltérő eseményforráson keresztül aktiválódik, egyéni kérelemhasznos adattal rendelkezik.

A következő kód egy mintakérelem hasznos adatát jelöli. A hasznos teher egy JSON struktúrát tartalmaz, amelynek két tagja van: `Data` és `Metadata`.

A `Data` tag olyan kulcsokat tartalmaz, amelyek megfelelnek a bemeneti és eseményindítóneveknek a *function.json* fájlban lévő kötések tömbjében meghatározottak szerint.

A `Metadata` tag [tartalmazza az eseményforrásból létrehozott metaadatokat.](./functions-bindings-expressions-patterns.md#trigger-metadata)

A következő *function.json* fájlban definiált kötések alapján:

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

A kérelem hasznos adata ehhez a példához hasonló értéket ad vissza:

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

### <a name="response-payload"></a>Válasz hasznos adata

A függvényválaszok konvenció szerint kulcs-/értékpárokként vannak formázva. A támogatott kulcsok a következők:

| <nobr>Hasznos kulcs</nobr>   | Adattípus | Megjegyzések                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | A *function.json* `bindings` fájl tömbje által meghatározott válaszértékeket tartalmazza.<br /><br />Például ha egy függvény "blob" nevű blob tárolókimeneti `Outputs` kötéssel `blob`van konfigurálva, majd tartalmaz egy nevű kulcsot, amely a blob értékére van állítva. |
| `Logs`        | tömb     | Az üzenetek megjelennek a Functions meghívási naplóiban.<br /><br />Amikor az Azure-ban fut, az üzenetek megjelennek az Application Insightsban. |
| `ReturnValue` | sztring    | Válasz ként szolgál, ha egy `$return` kimenet a *function.json* fájlban van konfigurálva. |

Tekintse meg a [példa egy minta hasznos teher](#bindings-implementation).

## <a name="examples"></a>Példák

Az egyéni kezelők bármely olyan nyelven implementálhatók, amely támogatja a HTTP-eseményeket. Bár az Azure Functions teljes mértékben támogatja a [JavaScript és a Node.js,](./functions-reference-node.md)a következő példák bemutatják, hogyan valósítható meg egy egyéni kezelő JavaScript használatával node.js utasítás céljából.

> [!TIP]
> Bár az itt látható Node.js-alapú példák hasznosak lehetnek, ha a Node.js nem támogatott verziójában szeretné futtatni a Functions alkalmazást.

## <a name="http-only-function"></a>CSAK HTTP-FÜGGVÉNY

A következő példa bemutatja, hogyan konfigurálhat egy HTTP-aktivált függvényt további kötések és kimenetek nélkül. Ebben a példában megvalósított `http` forgatókönyv egy `GET` vagy `POST` .

A következő kódrészlet azt mutatja be, hogy a függvényhez való kérelem hogyan van összeállva.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Megvalósítás

A *function.json* fájl egy *http-aktivált*függvényt konfigurál.

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

A függvény úgy van `GET` `POST` beállítva, hogy fogadja mind a `res`kéréseket, és az eredmény értékét egy argumentum nak nevezett argumentumon keresztül adja meg.

Az alkalmazás gyökerében a *host.json* fájl úgy van beállítva, `server.js` hogy futtassa a Node.js fájlt, és irányítsa a fájlt.

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

A *server.js* fájl webkiszolgálót és HTTP-függvényt valósít meg.

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

Ebben a példában az Express webkiszolgálót hoz létre a HTTP-események `FUNCTIONS_HTTPWORKER_PORT`kezelésére, és úgy van beállítva, hogy a kéréseket a segítségével figyelje.

A függvény a elérési útján `/hello`van definiálva. `GET`a kéréseket egy egyszerű JSON-objektum `POST` visszaküldésével kezelik, `req.body`és a kérelmek hozzáférhetnek a kéréstörzshöz a rendszeren keresztül.

A rendelési függvény útvonala itt van, `/hello` és nem `/api/hello` azért, mert a Functions állomás proxya a kérelmet az egyéni kezelő.

>[!NOTE]
>A `FUNCTIONS_HTTPWORKER_PORT` nem a függvény hívásához használt nyilvános néző port. Ezt a portot a Functions állomás használja az egyéni kezelő hívására.

## <a name="function-with-bindings"></a>Funkció kötésekkel

Ebben a példában megvalósított `order` forgatókönyv egy `POST` nevű függvényt tartalmaz, amely elfogadja a termékrendelést képviselő hasznos adattal. A függvényrendelés feladásakor létrejön egy várólista-tároló üzenet, és http-válasz jön vissza.

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

A *function.json* fájl egy *sorrendnevű mappában*konfigurálja a HTTP által aktivált függvényt.

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

Ez a függvény [HTTP-alapú függvényként](./functions-bindings-http-webhook-trigger.md) van definiálva, amely [HTTP-választ](./functions-bindings-http-webhook-output.md) ad vissza, és [várólista-tárolási](./functions-bindings-storage-queue-output.md) üzenetet ad ki.

Az alkalmazás gyökerében a *host.json* fájl úgy van beállítva, `server.js` hogy futtassa a Node.js fájlt, és irányítsa a fájlt.

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

A *server.js* fájl webkiszolgálót és HTTP-függvényt valósít meg.

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

Ebben a példában az Express webkiszolgálót hoz létre a HTTP-események `FUNCTIONS_HTTPWORKER_PORT`kezelésére, és úgy van beállítva, hogy a kéréseket a segítségével figyelje.

A függvény a elérési útján `/order` van definiálva.  A rendelési függvény útvonala itt van, `/order` és nem `/api/order` azért, mert a Functions állomás proxya a kérelmet az egyéni kezelő.

A `POST` függvénynek küldött kérelmek során az adatok néhány ponton keresztül jelennek meg:

- A kérés törzse elérhető a`req.body`
- A funkcióba küldött adatok a`req.body.Data.req.Body`

A függvény válasza egy kulcs/érték párba van `Outputs` formázva, ahol a tag jSON-értékkel rendelkezik, ahol a kulcsok megegyeznek a *function.json* fájlban meghatározott kimenetekkel.

Ha `message` a kérelemből érkező üzenettel és `res` a várt HTTP-válaszsal egyenlő beállítást állít be, ez a függvény üzenetet küld a várólista-tárolónak, és HTTP-választ ad vissza.

## <a name="debugging"></a>Hibakeresés

A Functions egyéni kezelőalkalmazás hibakereséséhez a hibakeresés engedélyezéséhez hozzá kell adnia a nyelvnek és a futásidejűnek megfelelő argumentumokat.

Például egy Node.js alkalmazás hibakereséséhez a `--inspect` jelző argumentumként kerül átadásra a *host.json* fájlban.

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
> A hibakeresési konfiguráció a *host.json* fájl része, ami azt jelenti, hogy előfordulhat, hogy el kell távolítania néhány argumentumot az éles környezetben való üzembe helyezés előtt.

Ezzel a konfigurációval a függvény gazdafolyamatát a következő paranccsal indíthatja el:

```bash
func host start
```

A folyamat indítása után csatolhat egy hibakeresőt, és leléphet a töréspontokhoz.

### <a name="visual-studio-code"></a>Visual Studio Code

A következő példa egy mintakonfiguráció, amely bemutatja, hogyan állíthatja be a *launch.json* fájlt, hogy csatlakoztassa az alkalmazást a Visual Studio kód hibakereső.

Ez a példa a Node.js, így előfordulhat, hogy módosítania kell ezt a példát más nyelveken vagy futásidőkben.

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

Az egyéni kezelő szinte minden Azure Functions üzemeltetési lehetőséghez telepíthető (lásd a [korlátozásokat).](#restrictions) Ha a kezelő egyéni függőségeket (például nyelvi futásidejűt) igényel, előfordulhat, hogy egyéni tárolót kell [használnia.](./functions-create-function-linux-custom-image.md)

## <a name="restrictions"></a>Korlátozások

- Az egyéni kezelők nem támogatottak a Linux-felhasználási tervekben.
- A webszervernek 60 másodpercen belül el kell indulnia.

## <a name="samples"></a>Példák

Tekintse meg az [egyéni kezelő minták GitHub-tárházban](https://github.com/Azure-Samples/functions-custom-handlers) példákat, hogyan valósítható meg függvények különböző nyelveken.
