---
title: Függvények letiltása a Azure Functionsban
description: Megtudhatja, hogyan tilthatja le és engedélyezheti a függvényeket a Azure Functionsban.
ms.topic: conceptual
ms.date: 04/08/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 4d93f728103aabdd1bd5557033a8bd36ffac2d42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91661023"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Függvények letiltása a Azure Functionsban

Ez a cikk azt ismerteti, hogyan lehet letiltani egy függvényt a Azure Functionsban. A függvények *letiltásához* azt jelenti, hogy a futtatókörnyezet figyelmen kívül hagyja a függvényhez definiált automatikus triggert. Ezzel megakadályozhatja, hogy egy adott függvény a teljes Function app leállítása nélkül fusson.

A függvények letiltásának ajánlott módja a következő formátumban beállított alkalmazás beállítása: `AzureWebJobs.<FUNCTION_NAME>.Disabled` `true` . Az Alkalmazásbeállítások számos módon hozhatók létre és módosíthatók, például az [Azure CLI](/cli/azure/) használatával és a függvény **kezelés** lapjának [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Ha letilt egy HTTP által aktivált függvényt a jelen cikkben ismertetett módszerekkel, a végpont továbbra is elérhető, ha a helyi számítógépen fut.  

## <a name="use-the-azure-cli"></a>Az Azure CLI használata

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

Használhatja a funkció **Áttekintés** lapján található **Engedélyezés** és **Letiltás** gombokat is. Ezek a gombok az Alkalmazásbeállítások értékének módosításával működnek `AzureWebJobs.<FUNCTION_NAME>.Disabled` . Ez a függvény-specifikus beállítás az első letiltásakor jön létre.

![Függvény állapotának kapcsolója](media/disable-function/function-state-switch.png)

> [!NOTE]  
> A portálon integrált tesztelési funkció figyelmen kívül hagyja a `Disabled` beállítást. Ez azt jelenti, hogy egy letiltott függvény továbbra is fut, amikor a portálon a **teszt** ablakból indult el. 

## <a name="localsettingsjson"></a>local.settings.json

A függvények a helyileg futtatott módon is letilthatók. Ha le szeretne tiltani egy nevű függvényt `HttpExample` , adjon hozzá egy bejegyzést a local.settings.jsfájljában található értékek gyűjteményéhez, a következőképpen:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

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
> Az `Disabled` attribútum az egyetlen módszer egy Class Library-függvény letiltására. A (z) "class Library" függvényhez létrehozott *function.js* nem lehet közvetlenül szerkeszteni. Ha szerkeszti ezt a fájlt, a tulajdonságra bármit is megadhat `disabled` .
>
> Ugyanez vonatkozik a **Function állapot** kapcsolóra a **kezelés** lapon, mivel az a fájl *function.js* módosításával működik.
>
> Azt is vegye figyelembe, hogy a portálon előfordulhat, hogy a funkció le van tiltva, ha nem.

### <a name="functions-1x---scripting-languages"></a>Függvények 1. x – parancsfájlkezelési nyelvek

Az 1. x verzióban a `disabled` fájl *function.js* tulajdonságával is megadhatja, hogy a futtatókörnyezet ne indítson el egy függvényt. Ez a metódus csak olyan programozási nyelveken működik, mint a C# parancsfájl és a JavaScript. A tulajdonság beállítható a következőre `disabled` `true` : vagy egy alkalmazás-beállítás neve:

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
>A portál mostantól az Alkalmazásbeállítások használatával letiltja a v1. x függvényeket. Ha egy alkalmazás beállítása ütközik a fájl function.jsával, hiba léphet fel. A hibák elkerülése érdekében távolítsa el a `disabled` tulajdonságot a fájl function.jsból. 


## <a name="next-steps"></a>Következő lépések

Ez a cikk az automatikus eseményindítók letiltását ismerteti. További információ az eseményindítókkal kapcsolatban: [triggerek és kötések](functions-triggers-bindings.md).
