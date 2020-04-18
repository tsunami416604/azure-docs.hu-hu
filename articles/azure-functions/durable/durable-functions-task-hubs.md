---
title: Feladatközpontok a tartós függvényekben – Azure
description: Ismerje meg, hogy mi a feladatközpont az Azure Functions tartós függvények bővítményében. További információ a feladatközpontok konfigurálásáról.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604618"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Feladatközpontok a tartós függvényekben (Azure Functions)

A tartós [függvények](durable-functions-overview.md) *feladatközpont* egy logikai tároló az Azure Storage-erőforrások, amelyek vezénylések használt. Az Orchestrator és a tevékenységfüggvények csak akkor kommunikálhatnak egymással, ha ugyanahhoz a feladatközponthoz tartoznak.

Ha több függvényalkalmazás osztozik egy tárfiókon, minden függvényalkalmazást külön feladatközpont-névvel *kell* konfigurálni. A tárfiók több feladatközpontot is tartalmazhat. Az alábbi ábra egy feladatközpontot mutat be függvényalkalmazásonként megosztott és dedikált tárfiókokban.

![Megosztott és dedikált tárfiókokat megjelenítő diagram.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-erőforrások

A feladatközpont a következő tárolási erőforrásokból áll:

* Egy vagy több vezérlősor.
* Egy munkatétel-várólista.
* Egy történelem tábla.
* Egy példány tábla.
* Egy tárolótároló, amely egy vagy több bérleti blobokat tartalmaz.
* Nagy üzenetrakományokat tartalmazó tárolótároló, ha van ilyen.

Ezek az erőforrások automatikusan létrejönnek az alapértelmezett Azure Storage-fiókban, amikor az orchestrator, az entitás vagy a tevékenység függvényei futnak, vagy az ütemezett futtatásra vannak ütemezve. A [Teljesítmény és méretezés](durable-functions-perf-and-scale.md) című cikk ismerteti, hogyan használják ezeket az erőforrásokat.

## <a name="task-hub-names"></a>Feladatközpont nevei

A feladatközpontokat a következő szabályoknak megfelelő név azonosítja:

* Csak alfanumerikus karaktereket tartalmaz
* Levéllel kezdődik
* Legalább 3 karakter hosszú, legfeljebb 45 karakter

A feladatközpont neve deklarálva van a *host.json* fájlban, ahogy az a következő példában látható:

### <a name="hostjson-functions-20"></a>host.json (Függvények 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (1.x függvények)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

A feladatközpontok az alkalmazásbeállításokkal is konfigurálhatók, `host.json` ahogy az a következő példafájlban látható:

### <a name="hostjson-functions-10"></a>host.json (Függvények 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Függvények 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

A feladatközpont neve az `MyTaskHub` alkalmazásbeállítás értékére lesz beállítva. Az `local.settings.json` alábbiakban bemutatjuk, `MyTaskHub` hogyan `samplehubname`kell meghatározni a beállítást:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

A következő kód bemutatja, hogyan kell írni egy függvényt, amely a [vezénylési ügyfélkötést](durable-functions-bindings.md#orchestration-client) használja az alkalmazásbeállításként konfigurált feladatközponttal való együttműködésre:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Az előző C# példa a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A fájlban lévő `function.json` feladatközpont-tulajdonság az Alkalmazásbeállításon keresztül van beállítva:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

A feladatközpont nevének betűvel kell kezdődnie, és csak betűkből és számokból állhat. Ha nincs megadva, a feladatközpont alapértelmezett neve az alábbi táblázatban látható módon lesz használva:

| Tartós bővítményverzió | Alapértelmezett feladatközpont neve |
| - | - |
| 2,x | Az Azure-ban üzembe helyezve a feladatközpont neve a _függvényalkalmazás_nevéből származik. Ha az Azure-on kívül fut, `TestHubName`az alapértelmezett feladatközpont neve . |
| 1,x | Az összes környezet alapértelmezett feladatközpont-neve a `DurableFunctionsHub`. |

A bővítményverziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

> [!NOTE]
> A név az, ami megkülönbözteti az egyik feladatközpontot a másiktól, ha egy megosztott tárfiókban több feladatközpont van. Ha több funkciós alkalmazással is rendelkezik, amelyek megosztott tárfiókot osztanak meg, explicit módon különböző neveket kell konfigurálnia a *host.json-fájlok* minden egyes feladatközpontjához. Ellenkező esetben a többfunkciós alkalmazások versenyeznek egymással az üzenetekért, ami nem definiált viselkedést eredményezhet, beleértve a vezényléseket, amelyek váratlanul "beragadtak" az `Pending` adott állapotban. `Running`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a vezénylési verziók kezeléséről](durable-functions-versioning.md)
