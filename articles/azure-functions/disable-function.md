---
title: Függvények letiltása a Azure Functionsban
description: Megtudhatja, hogyan tilthatja le és engedélyezheti a függvényeket a Azure Functionsban.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: bffb3136c77074ecd50e839fd7c73144ad910967
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970975"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Függvények letiltása a Azure Functionsban

Ez a cikk azt ismerteti, hogyan lehet letiltani egy függvényt a Azure Functionsban. A függvények *letiltásához* azt jelenti, hogy a futtatókörnyezet figyelmen kívül hagyja a függvényhez definiált automatikus triggert. Ezzel megakadályozhatja, hogy egy adott függvény a teljes Function app leállítása nélkül fusson.

A függvények letiltásának ajánlott módja a következő formátumú alkalmazás-beállítás használata: `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Az Alkalmazásbeállítások számos módon hozhatók létre és módosíthatók, például az [Azure CLI](/cli/azure/) használatával és a függvény **kezelés** lapjának [Azure Portal](https://portal.azure.com). 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

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

## <a name="use-the-portal"></a>A portál használata

Használhatja a Function **állapot** kapcsolót is a függvény **kezelés** lapján. A kapcsoló a `AzureWebJobs.<FUNCTION_NAME>.Disabled` alkalmazás beállításainak létrehozásával és törlésével működik.

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Egyéb módszerek

Míg az alkalmazás-beállítási módszer minden nyelvhez és az összes futásidejű verzióhoz ajánlott, számos más módon is letilthatja a függvényeket. Ezeket a metódusokat, amelyek nyelvtől és futtatókörnyezettől függően változnak, a visszamenőleges kompatibilitás érdekében tartanak fenn. 

### <a name="c-class-libraries"></a>C#osztály kódtárai

A Class Library függvényben a `Disable` attribútummal is megakadályozhatja a függvény aktiválását. Az attribútumot konstruktor paraméter nélkül is használhatja, ahogy az az alábbi példában is látható:

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

### <a name="functions-1x---scripting-languages"></a>Függvények 1. x – parancsfájlkezelési nyelvek

Az 1. x verzióban a *function. JSON* fájl `disabled` tulajdonságát is használhatja, hogy a futtatókörnyezet ne indítson el egy függvényt. Ez a metódus csak a parancsfájlok és a JavaScript C# programozási nyelveken működik. A `disabled` tulajdonság beállítható úgy, hogy `true` vagy egy alkalmazás-beállítás neve:

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
vagy 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

A második példában a függvény le van tiltva, ha van egy IS_DISABLED nevű alkalmazás-beállítás, és `true` vagy 1 értékre van beállítva.

Szerkesztheti a fájlt a Azure Portal, vagy használhatja a Function **állapot** kapcsolót a függvény **felügyelet** lapján. A portál kapcsoló a *function. JSON* fájl módosításával működik.

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Következő lépések

Ez a cikk az automatikus eseményindítók letiltását ismerteti. További információ az eseményindítókkal kapcsolatban: [triggerek és kötések](functions-triggers-bindings.md).
