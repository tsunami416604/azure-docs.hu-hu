---
title: HTTP API-k tartós függvényekben – Azure-függvények
description: Ismerje meg, hogyan valósíthatja meg a HTTP API-kat az Azure Functions durable functions bővítményében.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278167"
---
# <a name="http-api-reference"></a>HTTP API-hivatkozás

A Durable Functions bővítmény beépített HTTP API-kkészletét teszi elérhetővé, amelyek en [vezénylési](durable-functions-types-features-overview.md#orchestrator-functions)feladatok, [entitások](durable-functions-types-features-overview.md#entity-functions)és feladatközpontok felügyeleti [feladatainak végrehajtására](durable-functions-task-hubs.md)használhatók . Ezek a HTTP API-k olyan bővíthetőségi webhookok, amelyek et az Azure Functions gazdagép engedélyezi, de közvetlenül a Durable Functions bővítmény kezeli.

A bővítmény által megvalósított összes HTTP API-hoz a következő paraméterekre van szükség. Az összes paraméter adattípusa `string`.

| Paraméter        | Paraméter típusa  | Leírás |
|------------------|-----------------|-------------|
| **`taskHub`**    | Lekérdezési sztring    | A [feladatközpont](durable-functions-task-hubs.md)neve . Ha nincs megadva, a program az aktuális függvényalkalmazás feladatközpont-nevét feltételezi. |
| **`connection`** | Lekérdezési sztring    | A tárfiók kapcsolati karakterláncának **neve.** Ha nincs megadva, a függvényalkalmazás alapértelmezett kapcsolati karakterláncát feltételezi a rendszer. |
| **`systemKey`**  | Lekérdezési sztring    | Az API meghívásához szükséges engedélyezési kulcs. |

`systemKey`az Azure Functions gazdagép által automatikusan létrehozott engedélyezési kulcs. Kifejezetten hozzáférést biztosít a Tartós feladat bővítmény API-khoz, és ugyanúgy kezelhető, mint más [engedélyezési kulcsok.](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API) Létrehozhat olyan URL-eket, amelyek `taskHub` `connection`a `systemKey` megfelelő , és lekérdezési karakterlánc-értékeket tartalmaznak [vezénylési ügyfélkötési](durable-functions-bindings.md#orchestration-client) API-k használatával, például a `CreateCheckStatusResponse` .NET és `CreateHttpManagementPayload` API-k, illetve a `createCheckStatusResponse` JavaScript-ben lévő API-k `createHttpManagementPayload` használatával.

A következő néhány szakasz a bővítmény által támogatott speciális HTTP API-kat ismerteti, és példákat tartalmaz a használatukra.

## <a name="start-orchestration"></a>Vezénylés indítása

Elindítja a megadott orchestrator függvény új példányának végrehajtó ját.

### <a name="request"></a>Kérés

A Functions runtime 1.x verziója esetén a kérelem formázása a következő (több sor jelenik meg az egyértelműség érdekében):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

A Functions runtime 2.x verziójában az URL-formátum ugyanazokat a paramétereket használja, de kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétereket:

| Mező              | Paraméter típusa  | Leírás |
|--------------------|-----------------|-------------|
| **`functionName`** | URL-cím             | Az elindítandó orchestrator függvény neve. |
| **`instanceId`**   | URL-cím             | Nem kötelező megadni. A vezénylési példány azonosítója. Ha nincs megadva, az orchestrator függvény egy véletlenszerű példányazonosítóval indul. |
| **`{content}`**    | Tartalom kérése | Választható. A JSON-formátumú orchestrator függvény bemenet. |

### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (Elfogadva)**: A megadott orchestrator függvény futásának megkezdésére volt ütemezve. A `Location` válaszfejléc tartalmaz egy URL-címet a vezénylési állapot lekérdezéséhez.
* **HTTP 400 (Hibás kérés)**: A megadott orchestrator függvény nem létezik, a megadott példányazonosító érvénytelen, vagy a kérelem tartalma érvénytelen JSON.

A következő egy példakérelem, `RestartVMs` amely elindítja az orchestrator függvényt, és tartalmazza a JSON-objektum hasznos adatát:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

A **HTTP 202-es** esetek válaszhasznos terhelése egy JSON-objektum a következő mezőkkel:

| Mező                       | Leírás                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |A vezénylési példány azonosítója. |
| **`statusQueryGetUri`**     |A vezénylési példány állapot-URL-címe. |
| **`sendEventPostUri`**      |A vezénylési példány "raise event" URL-címe. |
| **`terminatePostUri`**      |A vezénylési példány "terminate" URL-címe. |
| **`purgeHistoryDeleteUri`** |A vezénylési példány "törlési előzmények" URL-címe. |
| **`rewindPostUri`**         |(előzetes verzió) A vezénylési példány "visszatekerési" URL-címe. |

Az összes mező adattípusa `string`.

Íme egy példa válasz hasznos adat egy `abc123` vezénylési példány azonosítójaként (olvashatósági formátumban):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

A HTTP-válasz célja, hogy kompatibilis legyen a *szavazásfogyasztói mintával.* A következő említésre méltó válaszfejléceket is tartalmazza:

* **Hely**: Az állapotvégpont URL-címe. Ez az URL-cím `statusQueryGetUri` ugyanazt az értéket tartalmazza, mint a mező.
* **Újrapróbálkozás után:** A lekérdezési műveletek közötti várakozási idő másodperceinek száma. Az alapértelmezett érték `10`.

Az aszinkron HTTP-lekérdezési mintával kapcsolatos további információkért tekintse meg a [HTTP aszinkron művelet követési](durable-functions-http-features.md#async-operation-tracking) dokumentációját.

## <a name="get-instance-status"></a>Példány állapotának beírása

Egy adott vezénylési példány állapotát.

### <a name="request"></a>Kérés

A Functions runtime 1.x verziója esetén a kérelem formázása a következő (több sor jelenik meg az egyértelműség érdekében):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

A Functions runtime 2.x verziójában az URL-formátum ugyanazokat a paramétereket használja, de kissé eltérő előtaggal:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétereket:

| Mező                   | Paraméter típusa  | Leírás |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL-cím             | A vezénylési példány azonosítója. |
| **`showInput`**         | Lekérdezési sztring    | Nem kötelező megadni. Ha a `false`beállítás, a függvény bemenet nem fog szerepelni a válasz hasznos adat.|
| **`showHistory`**       | Lekérdezési sztring    | Nem kötelező megadni. Ha a `true`beállítás, a vezénylési végrehajtási előzmények szerepelni fog a válasz hasznos adat.|
| **`showHistoryOutput`** | Lekérdezési sztring    | Nem kötelező megadni. Ha a `true`beállítás, a függvény kimenetek szerepelni fog nak a vezénylési végrehajtási előzmények.|
| **`createdTimeFrom`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegen vagy azt követően létrehozott visszaadott példányok listáját.|
| **`createdTimeTo`**     | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegen vagy azt megelőzően létrehozott visszaadott példányok listáját.|
| **`runtimeStatus`**     | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a visszaadott példányok listáját a futásidejű állapotuk alapján. A lehetséges futásidejű állapotértékek listáját a [Példányok lekérdezése](durable-functions-instance-management.md) című cikkben találja. |

### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 200 (OK)**: A megadott példány befejezett állapotban van.
* **HTTP 202 (Elfogadva)**: A megadott példány folyamatban van.
* **HTTP 400 (Hibás kérelem):** A megadott példány meghibásodott vagy megszakadt.
* **HTTP 404 (Nem található)**: A megadott példány nem létezik, vagy nem indult el.
* **HTTP 500 (belső kiszolgálóhiba)**: A megadott példány nem kezelt kivétellel nem sikerült.

A **HTTP 200 és HTTP 202** esetek válaszhasznos a következő mezőket követő JSON-objektum: **HTTP 202**

| Mező                 | Adattípus | Leírás |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | sztring    | A példány futásidejű állapota. Az értékek a következők: *Futás*, *Függőben,* *Sikertelen*, *Megszakítva*, *Megszakított*, *Befejezett*. |
| **`input`**           | JSON      | A példány inicializálásához használt JSON-adatok. Ez a `null` mező `showInput` akkor jelenik `false`meg, ha a lekérdezési karakterlánc paramétere a.|
| **`customStatus`**    | JSON      | Az egyéni vezénylési állapothoz használt JSON-adatok. Ez a `null` mező be van állítva, ha nincs beállítva. |
| **`output`**          | JSON      | A példány JSON-kimenete. Ez a `null` mező akkor jelenik meg, ha a példány nem befejezett állapotban van. |
| **`createdTime`**     | sztring    | A példány létrehozásának időpontja. ISO 8601 bővített jelölés. |
| **`lastUpdatedTime`** | sztring    | Az az időpont, amikor a példány utoljára megmaradt. ISO 8601 bővített jelölés. |
| **`historyEvents`**   | JSON      | A vezénylési végrehajtási előzményeket tartalmazó JSON-tömb. Ez a `null` mező `showHistory` nem az, `true`ha a lekérdezési karakterlánc paramétere . |

Íme egy példa válasz hasznos adat, beleértve a vezénylési végrehajtási előzmények és a tevékenység kimenetek (formázott olvashatóság):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

A **HTTP 202** válasz tartalmaz egy **Helyválasz** fejlécet `statusQueryGetUri` is, amely ugyanarra az URL-címre hivatkozik, mint a korábban említett mező.

## <a name="get-all-instances-status"></a>Az összes példány állapotának beírása

Az összes példány állapotát is lekérdezheti, `instanceId` ha eltávolítja a "Példányállapot bekérése" kérelemből. Ebben az esetben az alapvető paraméterek megegyeznek a "Példány állapotának beírása" paraméterekkel. A lekérdezési karakterlánc-paraméterek szűréshez szintén támogatottak.

Egy dolog megjegyezni, `connection` `code` hogy és nem kötelező. Ha névtelen hitelesítés van a függvényben, akkor `code` nem szükséges.
Ha nem szeretne az AzureWebJobsStorage alkalmazás beállításában definiálttól eltérő másik tárolási kapcsolati karakterláncot használni, akkor nyugodtan figyelmen kívül hagyhatja a kapcsolatlekérdezési karakterlánc paramétert.

### <a name="request"></a>Kérés

A Functions runtime 1.x verziója esetén a kérelem formázása a következő (több sor jelenik meg az egyértelműség érdekében):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

A Functions runtime 2.x verziójában az URL-formátum ugyanazokat a paramétereket használja, de kissé eltérő előtaggal:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétereket:

| Mező                   | Paraméter típusa  | Leírás |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL-cím             | A vezénylési példány azonosítója. |
| **`showInput`**         | Lekérdezési sztring    | Nem kötelező megadni. Ha a `false`beállítás, a függvény bemenet nem fog szerepelni a válasz hasznos adat.|
| **`showHistory`**       | Lekérdezési sztring    | Nem kötelező megadni. Ha a `true`beállítás, a vezénylési végrehajtási előzmények szerepelni fog a válasz hasznos adat.|
| **`showHistoryOutput`** | Lekérdezési sztring    | Nem kötelező megadni. Ha a `true`beállítás, a függvény kimenetek szerepelni fog nak a vezénylési végrehajtási előzmények.|
| **`createdTimeFrom`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegen vagy azt követően létrehozott visszaadott példányok listáját.|
| **`createdTimeTo`**     | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegen vagy azt megelőzően létrehozott visszaadott példányok listáját.|
| **`runtimeStatus`**     | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a visszaadott példányok listáját a futásidejű állapotuk alapján. A lehetséges futásidejű állapotértékek listáját a [Példányok lekérdezése](durable-functions-instance-management.md) című cikkben találja. |
| **`top`**               | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, korlátozza a lekérdezés által visszaadott példányok számát. |

### <a name="response"></a>Válasz

Íme egy példa a válasz hasznos terhelésére, beleértve a vezénylési állapotot (olvashatóság szempontjából formázva):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Ez a művelet nagyon költséges lehet az Azure Storage I/O szempontjából, ha sok sor van a Példányok táblában. További részletek a példány tábla megtalálható a [teljesítmény és a méretezés a tartós funkciók (Azure Functions) dokumentációban.](durable-functions-perf-and-scale.md#instances-table)
>

Ha több eredmény létezik, a válaszfejlécben egy folytatási jogkivonat kerül vissza.  A fejléc neve `x-ms-continuation-token`.

Ha a következő kérelemfejlécben adja meg a folytatási token értékét, az eredmények következő oldalát kaphatja meg. A kérelemfejléc neve is `x-ms-continuation-token`.

## <a name="purge-single-instance-history"></a>Egypéldányos előzmények kiürítése

Törli egy adott vezénylési példány előzményeit és kapcsolódó összetevőit.

### <a name="request"></a>Kérés

A Functions runtime 1.x verziója esetén a kérelem formázása a következő (több sor jelenik meg az egyértelműség érdekében):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

A Functions runtime 2.x verziójában az URL-formátum ugyanazokat a paramétereket használja, de kissé eltérő előtaggal:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétereket:

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | A vezénylési példány azonosítója. |

### <a name="response"></a>Válasz

A következő HTTP-állapotkódértékek adhatók vissza.

* **HTTP 200 (OK)**: A példányelőzmények törlése sikeresen megtörtént.
* **HTTP 404 (Nem található)**: A megadott példány nem létezik.

A **HTTP 200-as** eset válaszhasznos terhelése egy JSON-objektum a következő mezővel:

| Mező                  | Adattípus | Leírás |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | egész szám   | A törölt példányok száma. Az egypéldányos esetnél ennek `1`az értéknek mindig . |

Íme egy példa válasz hasznos teher (formázott olvashatóság):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Több példányelőzmény törlése

A feladatközponton belüli több példány előzményeit és kapcsolódó összetevőit `{instanceId}` is törölheti, ha eltávolítja a "Egypéldányos előzmények kiürítése" kérelemből. A példányelőzmények szelektív kiürítéséhez használja ugyanazokat a szűrőket, amelyeket a "Minden példány állapotának lekérése" kérelemben ismertetett.

### <a name="request"></a>Kérés

A Functions runtime 1.x verziója esetén a kérelem formázása a következő (több sor jelenik meg az egyértelműség érdekében):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

A Functions runtime 2.x verziójában az URL-formátum ugyanazokat a paramétereket használja, de kissé eltérő előtaggal:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétereket:

| Mező                 | Paraméter típusa  | Leírás |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Lekérdezési sztring    | Szűri a megadott ISO8601 időbélyegen vagy azt követően létrehozott törölt példányok listáját.|
| **`createdTimeTo`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyegen vagy azt megelőzően létrehozott törölt példányok listáját.|
| **`runtimeStatus`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a törölt példányok listáját a futásidejű állapotuk alapján. A lehetséges futásidejű állapotértékek listáját a [Példányok lekérdezése](durable-functions-instance-management.md) című cikkben találja. |

> [!NOTE]
> Ez a művelet nagyon költséges lehet az Azure Storage I/O szempontjából, ha sok sor van a példányokban és/vagy az előzmények táblákban. Ezek a táblák további részletek a [teljesítmény és a méretezés a tartós funkciók (Azure Functions) dokumentációban](durable-functions-perf-and-scale.md#instances-table) található.

### <a name="response"></a>Válasz

A következő HTTP-állapotkódértékek adhatók vissza.

* **HTTP 200 (OK)**: A példányelőzmények törlése sikeresen megtörtént.
* **HTTP 404 (Nem található)**: Nem található olyan példány, amely megfelelne a szűrőkifejezésnek.

A **HTTP 200-as** eset válaszhasznos terhelése egy JSON-objektum a következő mezővel:

| Mező                   | Adattípus | Leírás |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | egész szám   | A törölt példányok száma. |

Íme egy példa válasz hasznos teher (formázott olvashatóság):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Esemény emelése

Eseményértesítő üzenetet küld egy futó vezénylési példánynak.

### <a name="request"></a>Kérés

A Functions runtime 1.x verziója esetén a kérelem formázása a következő (több sor jelenik meg az egyértelműség érdekében):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

A Functions runtime 2.x verziójában az URL-formátum ugyanazokat a paramétereket használja, de kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétereket:

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | A vezénylési példány azonosítója. |
| **`eventName`**   | URL-cím             | Annak az eseménynek a neve, amelyre a célvezenition példány vár. |
| **`{content}`**   | Tartalom kérése | A JSON-formátumú esemény hasznos adat. |

### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (Elfogadva)**: A kiváltott esemény feldolgozásra elfogadott.
* **HTTP 400 (Hibás kérés)**: A `application/json` kérelem tartalma nem vagy érvénytelen JSON volt.
* **HTTP 404 (Nem található)**: A megadott példány nem található.
* **HTTP 410 (Gone)**: A megadott példány befejeződött vagy nem sikerült, és nem tudja feldolgozni a felvetett eseményeket.

Íme egy példakérés, amely a `"incr"` JSON-karakterláncot egy műveletnek nevezett eseményre váró példánynak **küldi:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Az API-ra adott válaszok nem tartalmaznak tartalmat.

## <a name="terminate-instance"></a>Példány leállítása

Leállít egy futó vezénylési példányt.

### <a name="request"></a>Kérés

A Functions runtime 1.x verziója esetén a kérelem formázása a következő (több sor jelenik meg az egyértelműség érdekében):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

A Functions runtime 2.x verziójában az URL-formátum ugyanazokat a paramétereket használja, de kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétert.

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | A vezénylési példány azonosítója. |
| **`reason`**      | Lekérdezési sztring    | Választható. A vezénylési példány megszüntetésének oka. |

### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (Elfogadva)**: A rendszer elfogadta a megszakítási kérelmet feldolgozásra.
* **HTTP 404 (Nem található)**: A megadott példány nem található.
* **HTTP 410 (Gone)**: A megadott példány befejeződött vagy nem sikerült.

Íme egy példa kérés, amely leállítja a futó példányt, és meghatározza a **hibás**okát:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-ra adott válaszok nem tartalmaznak tartalmat.

## <a name="rewind-instance-preview"></a>Példány visszatekerése (előzetes verzió)

Visszaállít egy sikertelen vezénylési példányt futó állapotba a legutóbbi sikertelen műveletek visszajátszásával.

### <a name="request"></a>Kérés

A Functions runtime 1.x verziója esetén a kérelem formázása a következő (több sor jelenik meg az egyértelműség érdekében):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

A Functions runtime 2.x verziójában az URL-formátum ugyanazokat a paramétereket használja, de kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétert.

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | A vezénylési példány azonosítója. |
| **`reason`**      | Lekérdezési sztring    | Választható. A vezénylési példány visszatekerésének oka. |

### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (Elfogadva)**: A visszatekerési kérelem feldolgozásra elfogadásra került.
* **HTTP 404 (Nem található)**: A megadott példány nem található.
* **HTTP 410 (Gone)**: A megadott példány befejeződött vagy megszakadt.

Íme egy példa kérés, amely visszatekeri a hibás példányt, és meghatározza a **rögzített**oka:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-ra adott válaszok nem tartalmaznak tartalmat.

## <a name="signal-entity"></a>Jel entitás

Egyirányú műveletüzenetet küld [egy tartós entitásnak.](durable-functions-types-features-overview.md#entity-functions) Ha az entitás nem létezik, akkor automatikusan létrejön.

> [!NOTE]
> A tartós entitások a Durable Functions 2.0-s verziótól kezdve érhetők el.

### <a name="request"></a>Kérés

A HTTP-kérelem formázása a következőképpen történik (az egyértelműség érdekében több sor jelenik meg):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétereket:

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL-cím             | Az entitás neve (típusa). |
| **`entityKey`**   | URL-cím             | Az entitás kulcsa (egyedi azonosítója). |
| **`op`**          | Lekérdezési sztring    | Választható. A meghívni a felhasználó által definiált művelet neve. |
| **`{content}`**   | Tartalom kérése | A JSON-formátumú esemény hasznos adat. |

Íme egy példakérés, amely egy felhasználó által definiált `Counter` "Hozzáadás" üzenetet küld egy nevű entitásnak. `steps` Az üzenet tartalma az `5`érték . Ha az entitás még nem létezik, akkor ez a kérés hozza létre:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Alapértelmezés szerint [a .NET osztályalapú entitások](durable-functions-dotnet-entities.md#defining-entity-classes) `op` nál, `delete` amely megadja az entitás állapotát. Ha azonban az entitás `delete`definiál egy nevű műveletet, akkor a rendszer a felhasználó által definiált műveletet hívja meg.

### <a name="response"></a>Válasz

Ez a művelet több lehetséges választ is kapott:

* **HTTP 202 (Elfogadva)**: A jelművelet aszinkron feldolgozásra lett elfogadva.
* **HTTP 400 (Hibás kérés)**: A `application/json`kérelem tartalma nem volt érvénytelen, nem `entityKey` érvényes JSON, vagy érvénytelen értékkel rendelkezett.
* **HTTP 404 (Nem található)** `entityName` : A megadott nem található.

A sikeres HTTP-kérelem nem tartalmaz tartalmat a válaszban. A sikertelen HTTP-kérelmek JSON-formátumú hibainformációkat tartalmazhatnak a válasz tartalomában.

## <a name="get-entity"></a>Entitás bekéselése

Leadja a megadott entitás állapotát.

### <a name="request"></a>Kérés

A HTTP-kérelem formázása a következőképpen történik (az egyértelműség érdekében több sor jelenik meg):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Válasz

Ennek a műveletnek két lehetséges válasza van:

* **HTTP 200 (OK)**: A megadott entitás létezik.
* **HTTP 404 (Nem található)**: A megadott entitás nem található.

A sikeres válasz az entitás JSON-szerializált állapotát tartalmazza annak tartalmaként.

### <a name="example"></a>Példa
A következő példa HTTP-kérelem lekéri egy meglévő `Counter` entitás állapotát: `steps`

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Ha `Counter` az entitás egyszerűen több, egy `currentValue` mezőbe mentett lépést tartalmaz, a válasz tartalma a következőképpen nézhet ki (olvashatóság szempontjából formázva):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Listaentitások

Több entitást is lekérdezhet az entitás neve vagy az utolsó művelet dátuma szerint.

### <a name="request"></a>Kérés

A HTTP-kérelem formázása a következőképpen történik (az egyértelműség érdekében több sor jelenik meg):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Az API kérelemparaméterei tartalmazzák a korábban említett alapértelmezett készletet, valamint a következő egyedi paramétereket:

| Mező                       | Paraméter típusa  | Leírás |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL-cím             | Választható. Ha meg van adva, a visszaküldött entitások listáját az entitásneve (kis- és nagybetűk nem megkülönböztető) alapján szűri. |
| **`fetchState`**            | Lekérdezési sztring    | Nem kötelező megadni. Ha a `true`beállítás, az entitásállapota szerepelni fog a válasz hasznos adatában. |
| **`lastOperationTimeFrom`** | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyeg után műveleteket feldolgozó visszaadott entitások listáját. |
| **`lastOperationTimeTo`**   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, szűri a megadott ISO8601 időbélyeg előtt felfeldolgozott műveleteket feldolgozó visszaadott entitások listáját. |
| **`top`**                   | Lekérdezési sztring    | Nem kötelező megadni. Ha meg van adva, korlátozza a lekérdezés által visszaadott entitások számát. |


### <a name="response"></a>Válasz

A sikeres HTTP 200 válasz egy JSON-szerializált entitástömböt és adott esetben az egyes entitások állapotát tartalmazza.

Alapértelmezés szerint a művelet az első 100 olyan entitást adja vissza, amely megfelel a lekérdezési feltételeknek. A hívó megadhat egy lekérdezési karakterlánc `top` paraméterértékét, hogy az eredmények maximális számát adja vissza. Ha több eredmény létezik a visszaadotton túl, a válaszfejlécben is visszaadja a folytatási jogkivonatot. A fejléc neve `x-ms-continuation-token`.

Ha a következő kérelemfejlécben adja meg a folytatási token értékét, az eredmények következő oldalát kaphatja meg. A kérelemfejléc neve is `x-ms-continuation-token`.

### <a name="example---list-all-entities"></a>Példa - az összes entitás felsorolása

A következő példa http-kérelem felsorolja az összes entitást a feladatközpontban:

```http
GET /runtime/webhooks/durabletask/entities
```

A json válasz a következőképpen nézhet ki (az olvashatóság érdekében formázva):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Példa – entitások listájának szűrése

A következő példa HTTP-kérelem csak az `counter` első két típusú entitást sorolja fel, és lekéri az állapotukat is:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

A json válasz a következőképpen nézhet ki (az olvashatóság érdekében formázva):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan használhatja az Application Insights segítségével a tartós függvények figyelésére](durable-functions-diagnostics.md)