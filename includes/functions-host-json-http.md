---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 5abefd55aa06f53bc3b437b29a2582b3e2239a65
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131621"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|routePrefix|api|Az útválasztási előtagot, amely az összes útvonal vonatkozik. Üres karakterlánc segítségével távolítsa el az alapértelmezett előtag. |
|maxOutstandingRequests|200*|Egy adott időpontban tartott szálankénti függőben lévő kérelmek maximális száma. Ez a korlátozás kérelmek várólistára kerülnek, de nem kezdték végrehajtása, valamint bármely, a folyamat-végrehajtást tartalmaz. Minden olyan bejövő kérések át a korlátot 429 "Túlságosan elfoglalt" választ a rendszer elutasítja. Amely lehetővé teszi, hogy a hívók időalapú újrapróbálkozási stratégiák használatát tervezni, és vezérelheti a kérések maximális késéseket is segít. Ez csak vezérli, üzenetsor-kezelési, amely a gazdagép szkript végrehajtási útvonalát belül történik. Más várólisták, például az ASP.NET-kérelmek várólistája továbbra is lesz érvényben, és ez a beállítás nem befolyásolja. * Az alapértelmezett verzió 1.x korlátlan streameken működő. Az alapértelmezett verzió használatalapú 2.x 200. Az alapértelmezett verzió 2.x egy dedikált csomag korlátlan streameken működő.|
|maxConcurrentRequests|100 *|Http-függvények, amelyek végrehajtható párhuzamosan maximális számát. Ez lehetővé teszi a vezérlő egyidejűséget, amelyek segíthetnek az erőforrás-felhasználás kezelése. Előfordulhat például, hogy egy http-függvény, amely sok rendszererőforrást (memória / / processzorfoglalatok) használja úgy, hogy ha egyidejűség túl magas problémákat okoz. Vagy előfordulhat, hogy egy függvényt, amely lehetővé teszi a kimenő kérelmeket egy külső szolgáltatás, és ezeket a hívások sebessége korlátozott kell. Ezekben az esetekben alkalmazása egy késleltetési ide segítségével. * Az alapértelmezett verzió 1.x korlátlan streameken működő. Az alapértelmezett használatalapú 2.x verzió pedig a 100. Az alapértelmezett verzió 2.x egy dedikált csomag korlátlan streameken működő.|
|dynamicThrottlesEnabled|true*|Ha engedélyezve van, ez a beállítás megadja a kérelemfeldolgozási folyamatba, rendszeres időközönként ellenőrizze a rendszerteljesítményt számlálók, például a kapcsolatokat/szálak/folyamatok/memória/cpu/stb., és ha ezek a számlálók bármelyike egy beépített magas meghaladja a küszöbértéket (80 %), a kérelmek lesz-e. elutasított 429 "Túlságosan elfoglalt" választ, mindaddig, amíg a számláló térjen vissza a normális üzemi szint. * Az alapértelmezett verzió 1.x értéke hamis. Az alapértelmezett verzió használatalapú 2.x értéke igaz. Az alapértelmezett verzió egy dedikált csomagban 2.x értéke hamis.|
