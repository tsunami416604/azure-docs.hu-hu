---
title: Visszatérési érték használata egy Azure-függvényből
description: Ismerje meg az Azure Functions visszatérési értékeinek kezelését
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74480569"
---
# <a name="using-the-azure-function-return-value"></a>Az Azure Függvény visszatérési értékének használata

Ez a cikk bemutatja, hogyan működnek a visszatérési értékek egy függvényen belül.

A visszatérési értékkel rendelkező nyelveken a [függvénykimeneti kötést](./functions-triggers-bindings.md#binding-direction) a visszatérési értékhez kötheti:

* Egy C# osztálytárban alkalmazza a kimeneti kötés attribútumot a metódus visszatérési értékére.
* Java-ban alkalmazza a kimeneti kötési jegyzetet a függvénymetódusra.
* Más nyelveken állítsa `name` a tulajdonságot a `$return` *function.json-ban* .

Ha több kimeneti kötés van, csak az egyikhez használja a visszatérési értéket.

A C# és A C# parancsfájlban az adatok `out` kimeneti kötésre küldésének alternatív módjai a paraméterek és [a gyűjtőobjektumok.](functions-reference-csharp.md#writing-multiple-output-values)

# <a name="c"></a>[C #](#tab/csharp)

A következőket a C# kód, amely egy kimeneti kötés visszatérési értékét használja, majd egy aszinkron példa:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A output kötésa a *function.json* fájlban:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Itt a C# parancsfájlkód, amelyet egy aszinkron példa követ:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F #](#tab/fsharp)

A output kötésa a *function.json* fájlban:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Itt az F# kód:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

A output kötésa a *function.json* fájlban:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

JavaScript ben a visszatérési érték a `context.done`következő második paraméterbe kerül:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="python"></a>[Python](#tab/python)

A output kötésa a *function.json* fájlban:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Itt a Python kód:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Az alábbiakban a kimeneti kötés hez visszaadott értéket használó Java-kódot használja:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Functions kötési hibáinak kezelése](./functions-bindings-errors.md)
