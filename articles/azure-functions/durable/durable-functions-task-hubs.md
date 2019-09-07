---
title: Durable Functions-Azure-beli feladat-hubok
description: Megtudhatja, mi a Task hub a Azure Functions Durable Functions-bővítményében. Megtudhatja, hogyan konfigurálhatja a feladatok hubok konfigurálását.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: b0a58251530467d788710b0584b15715a207e20f
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734324"
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

Az összes ilyen erőforrás automatikusan létrejön az alapértelmezett Azure Storage-fiókban, ha a Orchestrator vagy a Activity functions futtatása vagy futtatása ütemezve van. A [teljesítmény-és méretezési](durable-functions-perf-and-scale.md) cikk elmagyarázza, hogyan használják ezeket az erőforrásokat.

## <a name="task-hub-names"></a>Task hub-nevek

A feladatok hubokat a *Host. JSON* fájlban deklarált név azonosítja, ahogy az az alábbi példában is látható:

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

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

A feladatok hubok az Alkalmazásbeállítások használatával is konfigurálhatók, ahogy az az alábbi *Host. JSON* példa fájljában látható:

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

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

A feladat hub neve az Alkalmazásbeállítás értékére `MyTaskHub` lesz állítva. Az alábbi `local.settings.json` útmutató bemutatja, hogyan határozhatja `MyTaskHub` meg a `samplehubname`beállítást a következő módon:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Íme egy előre lefordított C# példa arra, hogyan írhat egy olyan függvényt, amely egy [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) -t használ egy olyan feladattal való együttműködéshez, amely alkalmazás-beállításként van konfigurálva:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
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

### <a name="javascript"></a>JavaScript

A `function.json` fájl Task hub tulajdonsága az alkalmazás beállításain keresztül van beállítva:
```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

A feladat-hub nevének betűvel kell kezdődnie, és csak betűkből és számokból állhat. Ha nincs megadva, az alapértelmezett név a **DurableFunctionsHub**.

> [!NOTE]
> A név az, ami megkülönbözteti az egyik feladatot a másiktól, ha egy megosztott Storage-fiókban több Task hub található. Ha több Function-alkalmazás osztozik egy megosztott Storage-fiókkal, explicit módon konfigurálnia kell a különböző neveket az egyes feladatok hubhoz a *Host. JSON* fájlokban. Ellenkező esetben a több függvényt használó alkalmazások versenyeznek egymással az üzenetek esetében, ami nem definiált viselkedést eredményezhet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató a verziószámozás kezeléséhez](durable-functions-versioning.md)
