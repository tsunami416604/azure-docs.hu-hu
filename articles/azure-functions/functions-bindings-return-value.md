---
title: Az Azure-függvény visszatérési értéke használatával
description: Ismerje meg, a visszatérési értékek kezelése az Azure Functions szolgáltatáshoz
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739678"
---
# <a name="using-the-azure-function-return-value"></a>Az Azure-függvény visszaadott értékének használata

Ez a cikk bemutatja, hogyan visszatérési értékek a munka egy függvényen belül.

Visszatérési értékkel nyelveken hozhasson létre függvény [kimeneti kötésének](./functions-triggers-bindings.md#binding-direction) , a visszaadott érték:

* C# osztálytár, a alkalmazni a kimeneti kötés attribútum a metódus visszatérési értéke.
* Más nyelven, állítsa be a `name` tulajdonság *function.json* való `$return`.

Ha több kimeneti kötést, a visszaadott értékének használata csak az egyik.

C# és a C#-szkript, adatokat küldeni a kimeneti kötés egyéb módjai a következők `out` paraméterek és [gyűjtő objektumok](functions-reference-csharp.md#writing-multiple-output-values).

Tekintse meg a nyelvspecifikus példa a visszaadott érték használatát:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C#-példa

Az alábbiakban C#-kódot, amely a visszaadott érték egy kimeneti kötést, aszinkron például követ használ:

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

## <a name="c-script-example"></a>C#-szkript példa

Itt van a kimeneti kötés a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Íme a C# szkriptkódot, aszinkron például követi:

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

## <a name="f-example"></a>F#Példa

Itt van a kimeneti kötés a *function.json* fájlt:

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

Itt van a kimeneti kötés a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

A JavaScript, a visszaadott érték kerül a második paraméter `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Python-példát

Itt van a kimeneti kötés a *function.json* fájlt:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
A Python-kód itt látható:

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
> [Az Azure Functions kötés hibáinak kezelése](./functions-bindings-errors.md)
