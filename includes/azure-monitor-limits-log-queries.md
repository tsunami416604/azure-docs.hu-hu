---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072637"
---
### <a name="general-query-limits"></a>Általános lekérdezési korlátok

| Korlát | Leírás |
|:---|:---|
| Lekérdezés nyelve | Az Azure Monitor ugyanazt a [Kusto lekérdezési nyelvet](/azure/kusto/query/) használja, mint az Azure Data Explorer. Tekintse meg az [Azure Monitor naplózási lekérdezési nyelvi különbségek](../articles/azure-monitor/log-query/data-explorer-difference.md) a KQL nyelvi elemek nem támogatott az Azure Monitor. |
| Azure-régiók | A naplólekérdezések túlzott terhelést tapasztalhatnak, ha az adatok több Azure-régióban is átnyúlnak a Log Analytics-munkaterületeken. A részleteket a [Lekérdezési korlátok](../articles/azure-monitor/log-query/scope.md#query-limits) ban találja. |
| Erőforrásközi lekérdezések | Az Application Insights-erőforrások és a Log Analytics-munkaterületek maximális száma egyetlen, 100-ra korlátozott lekérdezésben.<br>A Nézettervező nem támogatja az erőforrásközi lekérdezést.<br>Az új scheduledQueryRules API támogatja az erőforrások közötti lekérdezést a naplóriasztásokban.<br>A részletekért lásd az [erőforrásközi lekérdezési korlátokat.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |

### <a name="user-query-throttling"></a>Felhasználói lekérdezés szabályozása
Az Azure Monitor számos szabályozási korlátot, hogy megvédje a felhasználók túlzott számú lekérdezések küldése. Az ilyen viselkedés potenciálisan túlterhelheti a rendszer háttérerőforrásait, és veszélyeztetheti a szolgáltatás válaszképességét. A következő korlátozások célja, hogy megvédjék az ügyfeleket a megszakításoktól, és biztosítsák a konzisztens szolgáltatási szintet. A felhasználó szabályozása és a korlátok úgy vannak kialakítva, hogy csak a szélsőséges használati forgatókönyv, és nem lehet releváns a tipikus használat.


| Measure | Korlát felhasználónként | Leírás |
|:---|:---|:---|
| Egyidejű lekérdezések | 5 | Ha már 5 lekérdezés fut a felhasználó számára, minden új lekérdezés egy felhasználónkénti egyidejűségi várólistába kerül. Amikor az egyik futó lekérdezés befejeződik, a következő lekérdezés lekéri a várólistából, és elindul. Ez nem tartalmazza a riasztási szabályok lekérdezéseit.
| Idő az egyidejűségi várólistában | 2,5 perc | Ha egy lekérdezés 2,5 percnél tovább ül a várólistában indítás nélkül, akkor a rendszer a 429-es kódú HTTP-hibaválaszt kap. |
| Összes lekérdezés az egyidejűségi várólistában | 40 | Ha a várólistában lévő lekérdezések száma eléri a 40-et, a további lekérdezéseket a 429-es HTTP-hibakóddal elutasítva utasítják el. Ez a szám az egyidejűleg futtatható 5 lekérdezésen kívül található. |
| Lekérdezési arány | 200 lekérdezés 30 másodpercenként | Ez az a teljes sebesség, amelyet egyetlen felhasználó küldhet el az összes munkaterületre.  Ez a korlát a vizualizációs részek, például az Azure-irányítópultok és a Log Analytics munkaterület-összefoglaló lap által kezdeményezett programozott lekérdezésekre vagy lekérdezésekre vonatkozik. |

- Optimalizálja a lekérdezéseket az [Azure Monitor naplólekérdezéseinek optimalizálása](../articles/azure-monitor/log-query/query-optimization.md)című részben leírtak szerint.
- Az irányítópultok és munkafüzetek több lekérdezést is tartalmazhatnak egyetlen nézetben, amelyek minden betöltéskor vagy frissítéskor lekérdezéssorozatot generálnak. Fontolja meg a felosztása őket több nézetek, amelyek terhelés igény szerint. 
- A Power BI-ban fontolja meg csak az összesített eredmények kibontását a nyers naplók helyett.