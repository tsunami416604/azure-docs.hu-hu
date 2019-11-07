---
title: Durable Functions-Azure-beli feladat-hubok
description: Megtudhatja, mi a Task hub a Azure Functions Durable Functions-bővítményében. Útmutató a feladatok hubok konfigurálásához.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: b42294fdcf60add8496116bd1f83bf64f54a5f63
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614723"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) feladat-hubok

A [Durable Functionsban](durable-functions-overview.md) található egyik *feladat* egy olyan Azure Storage-erőforrások logikai tárolója, amelyeket a rendszer az előkészítéshez használ. A Orchestrator és a Activity függvények csak akkor kommunikálhatnak egymással, ha ugyanahhoz a feladathoz tartozó hubhoz tartoznak.

Ha több Function-alkalmazás is osztozik egy Storage-fiókkal, minden egyes Function *-alkalmazást külön* feladat-központtal kell konfigurálni. A Storage-fiókok több feladatot tartalmazó hubokat is tartalmazhatnak. A következő diagram egy Function hub-t ábrázol a megosztott és a dedikált Storage-fiókokban.

![A megosztott és dedikált Storage-fiókokat bemutató diagram.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-erőforrások

A feladatok központja a következő tárolási erőforrásokból áll:

* Egy vagy több vezérlő-várólista.
* Egy munkaelem-várólista.
* Egy előzményi tábla.
* Egy példány tábla.
* Egy vagy több bérleti blobot tartalmazó tároló.
* Nagyméretű üzenet-adattartalomot tartalmazó tárolási tároló, ha van ilyen.

Az összes ilyen erőforrás automatikusan létrejön az alapértelmezett Azure Storage-fiókban, ha a Orchestrator, az entitás vagy a tevékenység funkció fut, vagy ütemezve van a futtatásra. A [teljesítmény-és méretezési](durable-functions-perf-and-scale.md) cikk elmagyarázza, hogyan használják ezeket az erőforrásokat.

## <a name="task-hub-names"></a>Task hub-nevek

A feladatok hubokat a *Host. JSON* fájlban deklarált név azonosítja, ahogy az az alábbi példában is látható:

### <a name="hostjson-functions-20"></a>Host. JSON (functions 2,0)

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

### <a name="hostjson-functions-1x"></a>Host. JSON (functions 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

A feladatok hubok az Alkalmazásbeállítások használatával is konfigurálhatók, ahogy az az alábbi `host.json` például fájlban látható:

### <a name="hostjson-functions-10"></a>Host. JSON (functions 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>Host. JSON (functions 2,0)

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

A feladat hub neve a `MyTaskHub` Alkalmazásbeállítások értékére lesz állítva. Az alábbi `local.settings.json` bemutatja, hogyan határozhatja meg a `MyTaskHub` beállítást `samplehubname`ként:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

A következő kód egy előre lefordított C# példa arra, hogy miként írhat egy olyan függvényt, amely a beállítási [ügyfél kötését](durable-functions-bindings.md#orchestration-client) használja egy olyan feladattal való együttműködéshez, amely alkalmazás-beállításként van konfigurálva:

### <a name="c"></a>C#

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
> Az előző C# példa a Durable functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="javascript"></a>JavaScript

A `function.json` fájljának Task hub tulajdonsága az alkalmazás beállításain keresztül van beállítva:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

A feladat-hub nevének betűvel kell kezdődnie, és csak betűkből és számokból állhat. Ha nincs megadva, a rendszer a következő táblázatban látható módon fogja használni az alapértelmezett feladat-hub nevet:

| Tartós bővítmény verziója | Alapértelmezett Task hub-név |
| - | - |
| 2. x | Az Azure-ban való üzembe helyezéskor a feladat hub neve a _Function alkalmazás_nevéből származik. Az Azure-on kívül futtatva az alapértelmezett feladat hub neve `TestHubName`. |
| 1. x | Az összes környezet alapértelmezett feladatának központi neve `DurableFunctionsHub`. |

A bővítmény-verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

> [!NOTE]
> A név az, ami megkülönbözteti az egyik feladatot a másiktól, ha egy megosztott Storage-fiókban több Task hub található. Ha több Function-alkalmazás osztozik egy megosztott Storage-fiókkal, explicit módon konfigurálnia kell a különböző neveket az egyes feladatok hubhoz a *Host. JSON* fájlokban. Ellenkező esetben a több függvényt használó alkalmazások versenyeznek egymással az üzeneteknél, ami nem definiált viselkedést eredményezhet, beleértve az előkészítést, amely váratlanul "ragadt" a `Pending` vagy `Running` állapotban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan kezelheti a hangszerelési verziószámozást](durable-functions-versioning.md)
