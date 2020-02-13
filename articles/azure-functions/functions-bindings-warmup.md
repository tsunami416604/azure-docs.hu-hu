---
title: Azure Functions bemelegedési trigger
description: Megtudhatja, hogyan használhatja a bemelegedési triggert Azure Functionsban.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: Azure functions, functions, Event Processing, bemelegedési, Cold Start, Premium, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167313"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions bemelegítő trigger

Ez a cikk azt ismerteti, hogyan használható a bemelegedési trigger a Azure Functionsban. A bemelegedési trigger csak [prémium](functions-premium-plan.md)szintű csomagban futó Function apps esetén támogatott. A rendszer meghívja a bemelegedési eseményindítót, amikor egy példány bekerül egy futó Function-alkalmazás méretezésére. A bemelegedési művelettel előre betöltheti az egyéni függőségeket az [előmelegítési folyamat](./functions-premium-plan.md#pre-warmed-instances) során, így a függvények azonnal elkezdhetik a kérelmek feldolgozását. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-csomag **3.0.5 vagy újabb** verziója szükséges. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) tárházban található. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Eseményindító

A bemelegedési trigger lehetővé teszi, hogy Definiáljon egy függvényt, amely egy új példányon fog futni, amikor bekerül a futó alkalmazásba. A bemelegedési függvény használatával megnyithatja a kapcsolatokat, betöltheti a függőségeket, vagy bármilyen más egyéni logikát futtathat, mielőtt az alkalmazás megkapja a forgalmat. 

A bemelegedési trigger olyan megosztott függőségek létrehozására szolgál, amelyeket az alkalmazás egyéb funkciói fognak használni. [Tekintse meg a megosztott függőségek példáit](./manage-connections.md#client-code-examples).

Vegye figyelembe, hogy a bemelegedési trigger csak a kibővített műveletekben, az újraindítások vagy más nem léptékű indítások során hívható meg. A bemelegedési trigger használata nélkül gondoskodnia kell arról, hogy a logikája az összes szükséges függőséget be tudja tölteni. A lusta betöltés jó példa ennek megvalósítására.

## <a name="trigger---example"></a>Az eseményindító – példa

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi példa egy [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely minden egyes új példányon futni fog, amikor az alkalmazáshoz kerül. Nincs szükség visszatérési érték attribútumra.


* A függvénynek ```warmup``` (kis-és nagybetűk megkülönböztetése) nevűnek kell lennie, és egy alkalmazásban csak egy bemelegedési függvény lehet.
* Ha a bemelegedési .NET Class Library-függvényt szeretné használni, győződjön meg arról, hogy a csomagra hivatkozik a **Microsoft. Azure. webjobs. extensions > = 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


A helyőrző megjegyzések megmutatják, hogy az alkalmazás hol deklarálja és inicializálja a megosztott függőségeket. 
[További információk a megosztott függőségekről itt](./manage-connections.md#client-code-examples).

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
# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)


Az alábbi példa egy bemelegedési triggert mutat be egy *function. JSON* fájlban, valamint egy [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely minden egyes új példányon futni fog, amikor az alkalmazáshoz kerül.

A függvénynek ```warmup``` (kis-és nagybetűk megkülönböztetése) nevűnek kell lennie, és egy alkalmazásban csak egy bemelegedési függvény lehet.

Itt látható a *function. JSON* fájl:

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

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Az alábbi C# parancsfájl-kód a `HttpRequest`hoz kötődik:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy bemelegedési triggert mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely minden egyes új példányon futni fog, amikor az alkalmazáshoz kerül.

A függvénynek ```warmup``` (kis-és nagybetűk megkülönböztetése) nevűnek kell lennie, és egy alkalmazásban csak egy bemelegedési függvény lehet.

Itt látható a *function. JSON* fájl:

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

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A következő JavaScript-kódot:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa egy bemelegedési triggert mutat be egy *function. JSON* fájlban és egy [Python-függvényben](functions-reference-python.md) , amely minden egyes új példányon futni fog, amikor az alkalmazáshoz kerül.

A függvénynek ```warmup``` (kis-és nagybetűk megkülönböztetése) nevűnek kell lennie, és egy alkalmazásban csak egy bemelegedési függvény lehet.

Itt látható a *function. JSON* fájl:

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

A [konfigurációs](#trigger---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

Az alábbi példa egy bemelegedési triggert mutat be, amely akkor fut le, amikor az egyes új példányok bekerülnek az alkalmazásba.

A függvénynek `warmup` (kis-és nagybetűk megkülönböztetése) nevűnek kell lennie, és egy alkalmazásban csak egy bemelegedési függvény lehet.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Eseményindító - attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)a függvény konfigurálásához a `WarmupTrigger` attribútum érhető el.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ez a példa a [bemelegedési](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) attribútum használatát mutatja be.

Vegye figyelembe, hogy a függvényt ```Warmup``` kell meghívni, és egy alkalmazásban csak egy bemelegedési függvény lehet.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Teljes példaként tekintse meg az [trigger példáját](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A bemelegedési trigger nem támogatott a Javaban attribútumként.

---

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `WarmupTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **type** | n/a| Kötelező – `warmupTrigger`értékre kell állítani. |
| **direction** | n/a| Kötelező – `in`értékre kell állítani. |
| **név** | n/a| Kötelező – a függvény kódjában használt változó neve.|

## <a name="trigger---usage"></a>Eseményindító - használat

A meghívásakor a bemelegedési által aktivált függvények nem kapnak további információt.

## <a name="trigger---limits"></a>Trigger – korlátok

* A bemelegedési trigger csak a [prémium csomagon](./functions-premium-plan.md)futó alkalmazások számára érhető el.
* A bemelegedési trigger csak vertikális Felskálázási műveletek során hívható meg, az újraindítások vagy más nem léptékű indítások során. A bemelegedési trigger használata nélkül gondoskodnia kell arról, hogy a logikája az összes szükséges függőséget be tudja tölteni. A lusta betöltés jó példa ennek megvalósítására.
* A bemelegedési eseményindító nem hívható meg, ha egy példány már fut.
* Function App-alkalmazásban csak egy bemelegedési trigger függvény adható meg.

## <a name="next-steps"></a>Következő lépések

[További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
