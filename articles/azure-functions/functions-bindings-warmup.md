---
title: Az Azure Functions bemelegedési eseményindítója
description: Ismerje meg, hogyan használhatja a bemelegedési eseményindítót az Azure Functionsben.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure függvények, funkciók, eseményfeldolgozás, bemelegítés, hidegindítás, prémium, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167313"
---
# <a name="azure-functions-warm-up-trigger"></a>Az Azure Functions bemelegedési eseményindítója

Ez a cikk bemutatja, hogyan dolgozhat a bemelegedési eseményindító az Azure Functionsben. A bemelegedési eseményindító csak [a Prémium csomagban](functions-premium-plan.md)futó függvényalkalmazások esetében támogatott. A bemelegedési eseményindító meghívásra kerül, ha egy példány t afutó függvényalkalmazás méretezéséhez ad hozzá. A bemelegítési eseményindító segítségével előtöltheti az egyéni függőségeket a [melegítés előtti folyamat](./functions-premium-plan.md#pre-warmed-instances) során, hogy a függvények készen álljanak a kérelmek azonnali feldolgozására. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok - 2.x vagy újabb funkciók

A [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomag, **3.0.5-ös vagy újabb** verzió szükséges. A csomag forráskódja az [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub-tárházban található. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Eseményindító

A bemelegedési eseményindító lehetővé teszi, hogy definiáljon egy függvényt, amely egy új példányon fog futni, amikor hozzáadja a futó alkalmazáshoz. A bemelegítő függvény segítségével kapcsolatokat nyithat meg, függőségeket tölthet be, vagy bármely más egyéni logikát futtathat, mielőtt az alkalmazás megkezdi a forgalom fogadását. 

A bemelegedési eseményindító célja, hogy megosztott függőségeket hozzon létre, amelyeket az alkalmazás más függvényei fognak használni. [A megosztott függőségek példáit itt talál.](./manage-connections.md#client-code-examples)

Vegye figyelembe, hogy a bemelegedési eseményindító csak a horizontális felskálázási műveletek során van meghívva, újraindítások vagy más nem méretű indítások során nem. Győződjön meg arról, hogy a logika be tudja tölteni az összes szükséges függőséget a bemelegedési eseményindító használata nélkül. Lusta terhelés egy jó minta ennek eléréséhez.

## <a name="trigger---example"></a>Trigger - példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely minden új példányon futni fog, amikor hozzáadja az alkalmazáshoz. A visszatérési érték attribútum nem szükséges.


* A függvényt ```warmup``` meg kell nevezni (a kis- és nagybetűk et nem, és alkalmazásonként csak egy bemelegítő függvény lehet.
* Ha a bemelegedést .NET osztálykönyvtár-függvényként szeretné használni, győződjön meg arról, hogy csomaghivatkozással rendelkezik a **Microsoft.Azure.WebJobs.Extensions >= 3.0.5-re.**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


A helyőrző megjegyzések azt mutatják, hogy az alkalmazásban hol deklarálhatja és inicializálhatja a megosztott függőségeket. 
[A megosztott függőségekről itt olvashat bővebben.](./manage-connections.md#client-code-examples)

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)


A következő példa egy bemelegítő eseményindítót mutat be egy *function.json* fájlban, és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely minden új példányon futni fog, amikor hozzáadja az alkalmazáshoz.

A függvényt ```warmup``` meg kell nevezni (a kis- és nagybetűk et nem, és alkalmazásonként csak egy bemelegítő függvény lehet.

Itt a *function.json* fájl:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kódot, `HttpRequest`amely kötődik:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy bemelegítő eseményindítót mutat be egy *function.json* fájlban, és egy [JavaScript-függvényt,](functions-reference-node.md) amely minden új példányon futni fog, amikor hozzáadja az alkalmazáshoz.

A függvényt ```warmup``` meg kell nevezni (a kis- és nagybetűk et nem, és alkalmazásonként csak egy bemelegítő függvény lehet.

Itt a *function.json* fájl:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

A következő példa egy bemelegítő eseményindítót mutat be egy *function.json* fájlban, és egy [Python-függvényt,](functions-reference-python.md) amely minden új példányon futni fog, amikor hozzáadja az alkalmazáshoz.

A függvényt ```warmup``` meg kell nevezni (a kis- és nagybetűk et nem, és alkalmazásonként csak egy bemelegítő függvény lehet.

Itt a *function.json* fájl:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a Python kód:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

A következő példa egy bemelegítő eseményindítót mutat be, amely akkor fut, amikor minden új példány tava van az alkalmazáshoz.

A függvényt `warmup` meg kell nevezni (a kis- és nagybetűk et nem, és alkalmazásonként csak egy bemelegítő függvény lehet.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)az `WarmupTrigger` attribútum elérhető a függvény konfigurálásához.

# <a name="c"></a>[C #](#tab/csharp)

Ez a példa bemutatja, hogyan kell használni a [bemelegítő](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) attribútumot.

Vegye figyelembe, hogy ```Warmup``` a funkciót meg kell hívni, és alkalmazásonként csak egy bemelegítő funkció lehet.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Egy teljes példa, lásd az [eseményindító példa](#trigger---example).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A bemelegedési eseményindító nem támogatott Java attribútumként.

---

## <a name="trigger---configuration"></a>Trigger - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `WarmupTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
| **Típus** | n/a| Kötelező - a `warmupTrigger`beállításnak a beállítására kell. |
| **direction** | n/a| Kötelező - a `in`beállításnak a beállítására kell. |
| **név** | n/a| Kötelező – a függvénykódban használt változónév.|

## <a name="trigger---usage"></a>Trigger - használat

A bemelegedési aktivált függvény meghívásakor nem kap további információt.

## <a name="trigger---limits"></a>Trigger - korlátok

* A bemelegedési eseményindító csak a [Prémium csomagon](./functions-premium-plan.md)futó alkalmazások számára érhető el.
* A bemelegedési eseményindító csak a skálázási műveletek során van meghívva, újraindítások vagy más nem méretű indítások során nem. Győződjön meg arról, hogy a logika be tudja tölteni az összes szükséges függőséget a bemelegedési eseményindító használata nélkül. Lusta terhelés egy jó minta ennek eléréséhez.
* A bemelegedési eseményindító nem hívható meg, ha egy példány már fut.
* Függvényalkalmazásonként csak egy bemelegedési eseményindító függvény lehet.

## <a name="next-steps"></a>További lépések

[További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)
