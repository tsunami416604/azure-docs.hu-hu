---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ec825bb59840784ca45e8a38caa664c19883fb32
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263326"
---
|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|routePrefix|api-t|Az útvonal előtagja, amely az összes útvonalra vonatkozik. Az alapértelmezett előtag eltávolításához használjon üres karakterláncot. |
|maxOutstandingRequests|200<sup>*</sup>|A függőben lévő kérések maximális száma, amelyek egy adott időpontban vannak tárolva. Ez a korlát olyan kérelmeket tartalmaz, amelyek várólistára kerülnek, de nem indult el, valamint folyamatban van a végrehajtás. Az ezen a korláton túli bejövő kérelmek elutasítása egy 429 "túl elfoglalt" választ tartalmaz. Ez lehetővé teszi, hogy a hívók időalapú újrapróbálkozási stratégiákat alkalmazzanak, és segítséget nyújt a kérelmek maximális késésének szabályozásához is. Ez csak a parancsfájl-gazdagép végrehajtási útvonalán belüli üzenetsor-kezelőt vezérli. Más várólisták, például a ASP.NET kérelmek várólistája továbbra is érvényben marad, és ezt a beállítást nem érinti. <sup>*</sup> Az 1. x verzió alapértelmezett értéke nem kötött (`-1`). A 2. x verzió alapértelmezett értéke egy felhasználási csomagban 200. Egy dedikált csomagban található 2. x verzió alapértelmezett értéke nem kötött (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|A párhuzamosan végrehajtandó http-függvények maximális száma. Ez lehetővé teszi a párhuzamosság szabályozását, ami segíthet az erőforrások kihasználtságának kezelésében. Előfordulhat például, hogy olyan http-függvénnyel rendelkezik, amely sok rendszererőforrást (memóriát/processzort/szoftvercsatornát) használ, így problémát okoz, ha a párhuzamosság túl magas. Vagy lehet, hogy olyan függvényt használ, amely a kimenő kéréseket egy harmadik féltől származó szolgáltatásnak teszi elérhetővé, és a hívásoknak korlátozott arányban kell lenniük. Ezekben az esetekben a szabályozás alkalmazása segíthet. <sup>*</sup> Az 1. x verzió alapértelmezett értéke nem kötött (`-1`). A 2. x verzió alapértelmezett értéke egy felhasználási csomagban 100. Egy dedikált csomagban található 2. x verzió alapértelmezett értéke nem kötött (`-1`).|
|dynamicThrottlesEnabled|igaz<sup>*</sup>|Ha engedélyezve van, ez a beállítás hatására a kérelmek feldolgozási folyamata rendszeres időközönként ellenőrzi a rendszerteljesítmény-számlálókat, például a kapcsolatok/szálak/folyamatok/memória/CPU/etc értéket. ha ezek a számlálók egy beépített magas küszöbértéken (80%) vannak, a rendszer a kérelmeket a 429 "túl elfoglalt" válaszként utasítja el, amíg a számláló (k) be nem fejeződik a <sup>*</sup> Az 1. x verzió alapértelmezett értéke hamis. A 2. x verzió alapértelmezett értéke a használati tervben igaz. A 2. x verzió alapértelmezett értéke egy dedikált csomagban hamis.|
