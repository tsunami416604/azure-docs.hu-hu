---
title: Visszatérési érték használata egy Azure-függvényből
description: További információ a Azure Functions visszatérési értékeinek kezeléséről
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 1ea7ec0444ba80d3494afba77ad9d7fdabd5f982
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086406"
---
# <a name="using-the-azure-function-return-value"></a>Az Azure Function Return értékének használata

Ez a cikk azt ismerteti, hogyan működnek a visszatérési értékek egy függvényen belül.

A visszatérési értékkel rendelkező nyelveken a függvény [kimeneti kötését](./functions-triggers-bindings.md#binding-direction) a visszatérési értékhez kötheti:

* Egy C# osztály-függvénytárban alkalmazza a kimeneti kötési attribútumot a metódus visszatérési értékére.
* Más nyelveken állítsa a `name` *Function* `$return`. JSON tulajdonságot a következőre:.

Ha több kimeneti kötés is van, akkor a visszatérési értéket csak az egyikre használja.

A C# és C# a parancsfájlban az adatok `out` kimeneti kötésbe való küldésének alternatív módjai a paraméterek és a [gyűjtő objektumok](functions-reference-csharp.md#writing-multiple-output-values).

Tekintse meg a visszatérési érték használatát bemutató nyelvspecifikus példát:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C#például

Az alábbi C# kód egy kimeneti kötés visszatérési értékét használja, amelyet egy aszinkron példa követ:

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

## <a name="c-script-example"></a>C#parancsfájl – példa

Itt látható a *function. JSON* fájlban található kimeneti kötés:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Itt látható a C# szkript kódja, amelyet egy aszinkron példa követ:

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

## <a name="f-example"></a>F#például

Itt látható a *function. JSON* fájlban található kimeneti kötés:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Íme a F# kódot:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="javascript-example"></a>JavaScript-példa

Itt látható a *function. JSON* fájlban található kimeneti kötés:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

A JavaScriptben a visszatérési érték a második paraméterben a `context.done`következő:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Python-példa

Itt látható a *function. JSON* fájlban található kimeneti kötés:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Itt látható a Python-kód:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions kötési hibák kezelése](./functions-bindings-errors.md)
