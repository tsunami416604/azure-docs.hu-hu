---
title: A függvények letiltása az Azure Functionsben
description: Ismerje meg, hogyan tilthatja le és engedélyezheti a függvényeket az Azure Functionsben.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: fb8edf635856078655b8640ba0e1723fdd5e8a5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116143"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>A függvények letiltása az Azure Functionsben

Ez a cikk bemutatja, hogyan tilthatja le a függvényeket az Azure Functionsben. Egy függvény *letiltása* azt jelenti, hogy a futásidejű figyelmen kívül hagyja a függvényhez definiált automatikus eseményindítót. Ez lehetővé teszi, hogy megakadályozza egy adott függvény futtatását a teljes függvényalkalmazás leállítása nélkül.

A függvények letiltásának ajánlott módja a formátumban `AzureWebJobs.<FUNCTION_NAME>.Disabled`lévő alkalmazásbeállítás használata. Ezt az alkalmazásbeállítást számos módon hozhatja létre és módosíthatja, többek között az [Azure CLI](/cli/azure/) használatával és az [Azure Portalon](https://portal.azure.com)a függvény **Kezelés** lapján. 

> [!NOTE]  
> Ha a jelen cikkben ismertetett módszerekkel letilt egy HTTP-aktivált függvényt, a végpont továbbra is elérhető lehet a helyi számítógépen való futtatáskor.  

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure CLI-ben [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) a paranccsal hozza létre és módosítsa az alkalmazásbeállítást. A következő parancs letiltja a nevű `QueueTrigger` `AzureWebJobs.QueueTrigger.Disabled` függvényt `true`egy alkalmazásbeállítás létrehozásával, amely a -ra állítja be. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

A függvény újbóli engedélyezéséhez futtassa újra `false`ugyanazt a parancsot .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>A portál használata

A függvény **Kezelés** lapján is használhatja a **Függvényállapot** kapcsolót. A kapcsoló az `AzureWebJobs.<FUNCTION_NAME>.Disabled` alkalmazásbeállítás létrehozásával és törlésével működik.

![Függvényállapot-kapcsoló](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Egyéb módszerek

Bár az alkalmazásbeállítási módszer minden nyelvhez és az összes futásidejű verzióhoz ajánlott, a függvények letiltása számos más módon is elérhető. Ezek a módszerek, amelyek nyelvtől és futásidejű verziótól függően változnak, a visszamenőleges kompatibilitás érdekében megmaradnak. 

### <a name="c-class-libraries"></a>C# osztálykönyvtárak

Az osztálytár-függvényekben az `Disable` attribútum segítségével megakadályozhatja a függvény aktiválását. Az attribútumkonstruktor paraméter nélkül is használható, ahogy az a következő példában látható:

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

A konstruktorparaméter nélküli attribútum megköveteli a projekt újrafordítását és újratelepítését a függvény letiltott állapotának módosításához. Az attribútum használatának rugalmasabb módja egy logikai alkalmazásbeállításra hivatkozó konstruktorparaméter felvétele, ahogy az a következő példában látható:

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

Ezzel a módszerrel engedélyezheti és letilthatja a funkciót az alkalmazásbeállítás módosításával, újrafordítás vagy újratelepítés nélkül. Egy alkalmazásbeállítás módosítása a függvényalkalmazás újraindítását eredményezi, így a rendszer azonnal felismeri a letiltott állapotváltozást.

> [!IMPORTANT]
> Az `Disabled` attribútum az egyetlen módja az osztálykönyvtár-függvények letiltásának. Az osztálytár-függvényhez létrehozott *function.json* fájl nem szerkeszthető közvetlenül. Ha módosítja a fájlt, bármit `disabled` is tesz a tulajdonsággal, annak nincs hatása.
>
> Ugyanez vonatkozik a **Kezelés** lap **Függvény állapot** kapcsolójára is, mivel a *function.json* fájl módosításával működik.
>
> Vegye figyelembe azt is, hogy a portál jelezheti, hogy a függvény le van tiltva, ha nem.

### <a name="functions-1x---scripting-languages"></a>Funkciók 1.x - script nyelvek

Az 1.x-es verzióban `disabled` a *function.json* fájl tulajdonságával is megmondhatja a futásidőnek, hogy ne indítson el egy függvényt. Ez a módszer csak parancsfájlnyelvek, például C# parancsfájl és JavaScript esetén működik. A `disabled` tulajdonság beállítható `true` egy alkalmazásbeállítás nevére vagy nevére:

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

A második példában a függvény le van tiltva, ha van `true` egy IS_DISABLED nevű alkalmazásbeállítás, amely 1-re van állítva.

Szerkesztheti a fájlt az Azure Portalon, vagy használhatja a függvény **kezelés** lapján a **Függvényállapot-kapcsolót.** A portálkapcsoló a *function.json* fájl módosításával működik.

![Függvényállapot-kapcsoló](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>További lépések

Ez a cikk az automatikus eseményindítók letiltásáról szól. Az eseményindítókról az [Eseményindítók és kötések](functions-triggers-bindings.md)című témakörben talál további információt.
