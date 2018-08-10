---
title: Az Azure Functions függvények letiltása
description: Ismerje meg, hogyan letiltása és engedélyezése az Azure Functions függvények 1.x és a 2.x.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: tdykstra
ms.openlocfilehash: be30a97c9c94031ea526385326c220fbf0d316b7
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006442"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Az Azure Functions függvények letiltása

Ez a cikk bemutatja, hogyan tilthatja le a függvény az Azure Functions szolgáltatásban. A *letiltása* függvény azt jelenti, hogy a futtatókörnyezet figyelmen kívül hagyhatja az automatikus eseményindító, amely a függvény van definiálva. Benne módja attól függ, a futtatókörnyezet-verzió és a programozási nyelvek:

* Functions 1.x
  * Programozási nyelvek
  * C#-osztálykódtárakat
* Functions 2.x
  * Az összes nyelvet egyik módja
  * Választható módon a C#-osztálykódtárakat

## <a name="functions-1x---scripting-languages"></a>1.x - parancsnyelven függvények

Programozási nyelvek például C#-szkript és a JavaScript, használja a `disabled` tulajdonságát a *function.json* fájlt ossza meg a futtatókörnyezet nem az, hogy elindítsa a függvényt. Ez a tulajdonság beállítható `true` vagy egy alkalmazás-beállítás neve:

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

A második példában a funkció le van tiltva az Alkalmazásbeállítás, amely IS_DISABLED neve és értéke esetén `true` vagy 1.

Szerkesztheti a fájl a az Azure Portalon vagy a **függvény állapota** váltson a függvény **kezelés** fülre. A portál kapcsoló módosításával működik a *function.json* fájlt.

![Switch függvénynek állapota](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>1.x – C#-osztálykódtárakat függvények

A Functions 1.x osztálytár használja egy `Disable` megakadályozza, hogy a függvény aktiválása attribútum. Az attribútum a konstruktor paraméter nélkül használhatja az alábbi példában látható módon:

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

Az attribútum a konstruktor paraméter nélkül, hogy újrafordítottuk, és ismételt üzembe helyezése a projekt módosítása a függvény letiltott állapotban van szükség. Rugalmasabb a attribútumot használjanak, hogy tartalmazzák a konstruktorparamétert, amely egy logikai alkalmazás beállítás hivatkozik a következő példában látható módon:

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

Ez a módszer lehetővé teszi engedélyezheti vagy letilthatja a függvény az Alkalmazásbeállítás módosítása újrafordítás vagy újbóli üzembe helyezése nélkül. A függvényalkalmazás újraindításához egy alkalmazás-beállítás módosítása okozza, így a Letiltva állapot módosítása közvetlenül elismerten.

> [!IMPORTANT]
> A `Disabled` attribútum az egyetlen módja egy osztály függvénye letiltása. A generált *function.json* fájl, amely a egy osztály függvénye nem célja, hogy közvetlenül szerkeszthetők. Ha a fájl szerkesztéséhez bárhogy a `disabled` tulajdonság nem lesz hatása.
>
> Ugyanez vonatkozik a a **állapot működéséhez** váltani a **kezelés** lapon, mivel a működés módosításával a *function.json* fájl.
>
> Továbbá vegye figyelembe, hogy a portál jelezni a funkció le van tiltva, ha nem, előfordulhat, hogy.



## <a name="functions-2x---all-languages"></a>Függvények 2.x - minden nyelv

A függvények 2.x függvény letiltja az alkalmazás-beállítás használatával. Például letilthatja a függvény neve `QueueTrigger`, létrehozhat egy nevű Alkalmazásbeállítás `AzureWebJobs.QueueTrigger.Disabled`, és állítsa be `true`. A funkció engedélyezéséhez állítsa az alkalmazás beállítást `false`. Is használhatja a **függvény állapota** váltson a függvény **kezelés** fülre. A kapcsoló létrehozása és törlése úgy működik a `AzureWebJobs.<functionname>.Disabled` alkalmazásbeállítást.

![Switch függvénynek állapota](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>A 2.x - C#-osztálykódtárakat függvények

A Functions 2.x osztálytár azt javasoljuk, hogy a működik módszert az összes nyelvet használja. Ha szeretné, akkor is, de [részeként funkcióinak 1.x használata a Disable-attribútum](#functions-1x---c-class-libraries).

## <a name="next-steps"></a>További lépések

Ez a cikk az automatikus eseményindítók letiltása. Eseményindítók kapcsolatos további információkért lásd: [eseményindítók és kötések](functions-triggers-bindings.md).
