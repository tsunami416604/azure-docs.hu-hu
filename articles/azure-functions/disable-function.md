---
title: Függvények letiltása a Azure Functionsban
description: Megtudhatja, hogyan tilthatja le és engedélyezheti a függvényeket az 1. x és 2. x Azure Functionsban.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 498bb8c0f1e7bb674605d4a98f0be0f3e0b9a7c9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650498"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Függvények letiltása a Azure Functionsban

Ez a cikk azt ismerteti, hogyan lehet letiltani egy függvényt a Azure Functionsban. A függvények letiltásához azt jelenti, hogy a futtatókörnyezet figyelmen kívül hagyja a függvényhez definiált automatikus triggert. Ennek módja a futásidejű verziótól és a programozási nyelvtől függ:

* 2\. x függvények:
  * Az egyik módszer az összes nyelvhez
  * Az C# osztály-kódtárak választható módja
* Függvények 1. x:
  * Programozási nyelvek
  * C#osztály kódtárai

## <a name="functions-2x---all-languages"></a>Functions 2. x – minden nyelv

A 2. x függvényben letilthatja a függvényt, ha a formátumot `AzureWebJobs.<FUNCTION_NAME>.Disabled`egy alkalmazási beállítással letiltja. Az Alkalmazásbeállítások számos módon hozhatók létre és módosíthatók, például az [Azure CLI](/cli/azure/) használatával és a függvény **kezelés** lapjának [Azure Portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) -ben a paranccsal hozhatja létre és módosíthatja az alkalmazás beállításait. A következő parancs letiltja a nevű `QueueTrigger` függvényt, ha létrehoz egy nevű `AzureWebJobs.QueueTrigger.Disabled` alkalmazást a `true`beállításhoz. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

A függvény újbóli engedélyezéséhez futtassa újra a parancsot egy értékkel `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portál

Használhatja a Function **állapot** kapcsolót is a függvény **kezelés** lapján. A kapcsoló az `AzureWebJobs.<FUNCTION_NAME>.Disabled` alkalmazás beállításainak létrehozásával és törlésével működik.

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2. x C# -Class könyvtárak

A functions 2. x osztályú függvénytárban azt javasoljuk, hogy az összes nyelvre vonatkozó módszert használja. Ha azonban szeretné, használhatja [a Disable attribútumot az 1. x függvényeknél](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Függvények 1. x – parancsfájlkezelési nyelvek

A parancsfájlok, például a C# parancsfájlok és a JavaScriptek esetében használja `disabled` a *function. JSON* fájl tulajdonságát, hogy a futtatókörnyezet ne indítson el egy függvényt. Ez a tulajdonság a következőre `true` állítható be: vagy egy alkalmazás-beállítás neve:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
or 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

A második példában a függvény le van tiltva, ha van egy IS_DISABLED nevű alkalmazás-beállítás, és `true` a értéke vagy 1.

Szerkesztheti a fájlt a Azure Portal, vagy használhatja a Function **állapot** kapcsolót a függvény **felügyelet** lapján. A portál kapcsoló a *function. JSON* fájl módosításával működik.

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Függvények 1. x C# osztályú kódtárak

A függvények 1. x osztályának könyvtárában egy `Disable` attribútum használatával megakadályozható, hogy egy függvény aktiválható legyen. Az attribútumot konstruktor paraméter nélkül is használhatja, ahogy az az alábbi példában is látható:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

A konstruktor paraméter nélküli attribútumhoz újra kell fordítania a projektet, és újra kell telepítenie a projekt letiltott állapotának módosításához. Az attribútum használatának rugalmasabb módja, ha egy olyan konstruktor paramétert tartalmaz, amely egy logikai alkalmazás beállítására hivatkozik, ahogy az alábbi példában is látható:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Ezzel a módszerrel engedélyezheti és letilthatja a függvényt úgy, hogy az újrafordítás vagy az újbóli telepítés nélkül megváltoztatja az alkalmazás beállítását. Az Alkalmazásbeállítások módosítása a Function alkalmazás újraindítását eredményezi, így a letiltott állapot változása azonnal felismerhető.

> [!IMPORTANT]
> Az `Disabled` attribútum az egyetlen módszer egy Class Library-függvény letiltására. A Class Library függvényhez tartozó generált *function. JSON* fájlt nem közvetlenül kell szerkeszteni. Ha szerkeszti ezt a fájlt, a `disabled` tulajdonságra bármit is megadhat.
>
> Ugyanez vonatkozik a **Function állapot** kapcsolóra a **kezelés** lapon, mivel a *function. JSON* fájl módosításával működik.
>
> Azt is vegye figyelembe, hogy a portálon előfordulhat, hogy a funkció le van tiltva, ha nem.

## <a name="next-steps"></a>További lépések

Ez a cikk az automatikus eseményindítók letiltását ismerteti. További információ az eseményindítókkal kapcsolatban: [triggerek és kötések](functions-triggers-bindings.md).
