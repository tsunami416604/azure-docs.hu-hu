---
title: Függvények letiltása a Azure Functionsban
description: Megtudhatja, hogyan tilthatja le és engedélyezheti a függvényeket az 1. x és 2. x Azure Functionsban.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7968580fcaa40575571a41f067fa74fbdc0a3a34
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233042"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Függvények letiltása a Azure Functionsban

Ez a cikk azt ismerteti, hogyan lehet letiltani egy függvényt a Azure Functionsban. A függvények *letiltásához* azt jelenti, hogy a futtatókörnyezet figyelmen kívül hagyja a függvényhez definiált automatikus triggert. Ennek módja a futásidejű verziótól és a programozási nyelvtől függ:

* 2\. x függvények:
  * Az egyik módszer az összes nyelvhez
  * Az C# osztály-kódtárak választható módja
* Függvények 1. x:
  * Programozási nyelvek
  * C#osztály kódtárai

## <a name="functions-2x---all-languages"></a>Functions 2. x – minden nyelv

A 2. x függvényben letilthatja a függvényt az alkalmazás formátumának `AzureWebJobs.<FUNCTION_NAME>.Disabled`formátumban való használatával. Az Alkalmazásbeállítások számos módon hozhatók létre és módosíthatók, például az [Azure CLI](/cli/azure/) használatával és a függvény **kezelés** lapjának [Azure Portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben a [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) paranccsal hozhatja létre és módosíthatja az alkalmazás beállításait. A következő parancs letilt egy `QueueTrigger` nevű függvényt egy `AzureWebJobs.QueueTrigger.Disabled` nevű alkalmazás-beállítás létrehozásával `true`re. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

A függvény újbóli engedélyezéséhez futtassa újra ugyanazt a parancsot `false`értékkel.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portál

Használhatja a Function **állapot** kapcsolót is a függvény **kezelés** lapján. A kapcsoló a `AzureWebJobs.<FUNCTION_NAME>.Disabled` alkalmazás beállításainak létrehozásával és törlésével működik.

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2. x C# -Class könyvtárak

A functions 2. x osztályú függvénytárban azt javasoljuk, hogy az összes nyelvre vonatkozó módszert használja. Ha azonban szeretné, használhatja [a Disable attribútumot az 1. x függvényeknél](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Függvények 1. x – parancsfájlkezelési nyelvek

Parancsfájlok, például C# parancsfájlok és JavaScript esetén a *function. JSON* fájl `disabled` tulajdonságával adja meg, hogy a futtatókörnyezet ne indítson el egy függvényt. Ez a tulajdonság beállítható úgy, hogy `true` vagy egy alkalmazás-beállítás nevét:

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

A második példában a függvény le van tiltva, ha van egy IS_DISABLED nevű alkalmazás-beállítás, és `true` vagy 1 értékre van beállítva.

Szerkesztheti a fájlt a Azure Portal, vagy használhatja a Function **állapot** kapcsolót a függvény **felügyelet** lapján. A portál kapcsoló a *function. JSON* fájl módosításával működik.

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Függvények 1. x C# osztályú kódtárak

A függvények 1. x osztályú függvénytárában egy `Disable` attribútum használatával megakadályozható, hogy egy függvény aktiválható legyen. Az attribútumot konstruktor paraméter nélkül is használhatja, ahogy az az alábbi példában is látható:

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
> A `Disabled` attribútum a Class Library-függvények letiltásának egyetlen módja. A Class Library függvényhez tartozó generált *function. JSON* fájlt nem közvetlenül kell szerkeszteni. Ha szerkeszti ezt a fájlt, a `disabled` tulajdonsággal megjelenő bármit nem fog befolyásolni.
>
> Ugyanez vonatkozik a **Function állapot** kapcsolóra a **kezelés** lapon, mivel a *function. JSON* fájl módosításával működik.
>
> Azt is vegye figyelembe, hogy a portálon előfordulhat, hogy a funkció le van tiltva, ha nem.

## <a name="next-steps"></a>További lépések

Ez a cikk az automatikus eseményindítók letiltását ismerteti. További információ az eseményindítókkal kapcsolatban: [triggerek és kötések](functions-triggers-bindings.md).
