---
title: Függvények letiltása a Azure Functionsban
description: Megtudhatja, hogyan tilthatja le és engedélyezheti a függvényeket a Azure Functionsban.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: ee701e8df8faddef9bbdb16e7a1048c4dc2e40a5
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848739"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Függvények letiltása a Azure Functionsban

Ez a cikk azt ismerteti, hogyan lehet letiltani egy függvényt a Azure Functionsban. A függvények *letiltásához* azt jelenti, hogy a futtatókörnyezet figyelmen kívül hagyja a függvényhez definiált automatikus triggert. Ezzel megakadályozhatja, hogy egy adott függvény a teljes Function app leállítása nélkül fusson.

A függvények letiltásának ajánlott módja a következő formátumban: alkalmazás beállítása `AzureWebJobs.<FUNCTION_NAME>.Disabled` . Az Alkalmazásbeállítások számos módon hozhatók létre és módosíthatók, például az [Azure CLI](/cli/azure/) használatával és a függvény **kezelés** lapjának [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Ha letilt egy HTTP által aktivált függvényt a jelen cikkben ismertetett módszerekkel, a végpont továbbra is elérhető, ha a helyi számítógépen fut.  

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure CLI-ben a [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) paranccsal hozhatja létre és módosíthatja az alkalmazás beállításait. A következő parancs letiltja a nevű függvényt, `QueueTrigger` Ha létrehoz egy nevű alkalmazást a beállításhoz `AzureWebJobs.QueueTrigger.Disabled` `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

A függvény újbóli engedélyezéséhez futtassa újra a parancsot egy értékkel `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>A portál használata

Használhatja a funkció **Áttekintés** lapján található **Engedélyezés** és **Letiltás** gombokat is. Ezek a gombok az alkalmazás beállításainak létrehozásával és törlésével működnek `AzureWebJobs.<FUNCTION_NAME>.Disabled` .

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

> [!NOTE]  
> A portálon integrált tesztelési funkció figyelmen kívül hagyja a `Disabled` beállítást. Ez azt jelenti, hogy egy letiltott függvény továbbra is fut, amikor a portálon a **teszt** ablakból indult el. 

## <a name="other-methods"></a>Egyéb módszerek

Míg az alkalmazás-beállítási módszer minden nyelvhez és az összes futásidejű verzióhoz ajánlott, számos más módon is letilthatja a függvényeket. Ezeket a metódusokat, amelyek nyelvtől és futtatókörnyezettől függően változnak, a visszamenőleges kompatibilitás érdekében tartanak fenn. 

### <a name="c-class-libraries"></a>C# szintű kódtárak

A Class Library függvényben az attribútum segítségével is `Disable` megakadályozhatja a függvény aktiválását. Az attribútumot konstruktor paraméter nélkül is használhatja, ahogy az az alábbi példában is látható:

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
> Az `Disabled` attribútum az egyetlen módszer egy Class Library-függvény letiltására. A Class Library függvényhez tartozó generált *function. JSON* fájlt nem közvetlenül kell szerkeszteni. Ha szerkeszti ezt a fájlt, a tulajdonságra bármit is megadhat `disabled` .
>
> Ugyanez vonatkozik a **Function állapot** kapcsolóra a **kezelés** lapon, mivel a *function. JSON* fájl módosításával működik.
>
> Azt is vegye figyelembe, hogy a portálon előfordulhat, hogy a funkció le van tiltva, ha nem.

### <a name="functions-1x---scripting-languages"></a>Függvények 1. x – parancsfájlkezelési nyelvek

Az 1. x verzióban a `disabled` *function. JSON* fájl tulajdonságát is használhatja, hogy a futtatókörnyezet ne indítson el egy függvényt. Ez a metódus csak olyan programozási nyelveken működik, mint a C# parancsfájl és a JavaScript. A tulajdonság beállítható a következőre `disabled` `true` : vagy egy alkalmazás-beállítás neve:

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

A második példában a függvény le van tiltva, ha van egy IS_DISABLED nevű alkalmazás-beállítás, és a értéke `true` vagy 1.

>[!IMPORTANT]  
>A portál mostantól az Alkalmazásbeállítások használatával letiltja a v1. x függvényeket. Ha egy alkalmazás beállítása ütközik a function. JSON fájllal, hiba léphet fel. A hibák elkerülése érdekében távolítsa el a `disabled` tulajdonságot a function. JSON fájlból. 


## <a name="next-steps"></a>További lépések

Ez a cikk az automatikus eseményindítók letiltását ismerteti. További információ az eseményindítókkal kapcsolatban: [triggerek és kötések](functions-triggers-bindings.md).
