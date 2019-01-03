---
title: Az Azure Time Series Insights-környezet skálázása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan skálázható az Azure Time Series Insights-környezetet. Az Azure portal segítségével hozzáadhatók vagy kivonhatók díjszabási Termékváltozat kapacitásokat.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: ee695798dc8a2a19d5cd3d94cbf43e0b58065f84
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556679"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>A Time Series Insights-környezet méretezése

Ez a cikk bemutatja, hogyan módosíthatja a környezet kapacitása a Time Series Insights-környezet az Azure portal használatával. A kapacitása a alkalmazni a bejövő forgalom, a tárolási kapacitás és a kiválasztott Termékváltozat kapcsolódó költségek szorzó. 

Az Azure portal használatával növelheti vagy csökkentheti a belül egy adott díjszabási Termékváltozat kapacitása. 

Azonban a tarifacsomag módosítása Termékváltozat nem engedélyezett. Például egy S1 Termékváltozat díjszabása környezet nem lehet konvertálni az S2 szintű, vagy fordítva. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 Termékváltozat telemetriabevitelt régiójának és kapacitás

| S1 Termékváltozat kapacitása | Bejövő forgalom | Maximális tárolási kapacitás
| --- | --- | --- |
| 1 | 1 GB (1 millió esemény) | 30 GB (30 millió esemény) / hó |
| 10 | 10 GB-os (10 millió esemény) | 300 GB (300 millió esemény) / hó |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 Termékváltozat telemetriabevitelt régiójának és kapacitás

| S2 Termékváltozat kapacitása | Bejövő forgalom | Maximális tárolási kapacitás
| --- | --- | --- |
| 1 | 10 GB-os (10 millió esemény) | 300 GB (300 millió esemény) / hó |
| 10 | 100 GB (100 millió esemény) | 3 TB (3 milliárd eseményig) / hó |

Kapacitások költségráfordításokkal egyenes arányban, méretezhető, így egy S1 Termékváltozat kapacitása 2 támogatja a 2 GB (2 millió) esemény napi bejövő forgalom és 60 GB (60 millió esemény) / hó.

## <a name="change-the-capacity-of-your-environment"></a>A környezet a kapacitás módosításához
1. Az Azure Portalon keresse meg és válassza ki a Time Series Insights-környezetet. 

2. A Time Series Insighs környezet menüben válassza **konfigurálása**.

   ![Configure.png](media/scale-your-environment/configure.png)

3. Módosítsa a **kapacitás** csúszkával válassza ki a kapacitás, amely megfelel a követelményeknek, a bejövő forgalom arányát és a tárolási kapacitást. Figyelje meg a **bejövő forgalom**, **tárolókapacitás**, és **becsült költség** dinamikusan frissülnek a megjelenítése, milyen hatású a változtatás. 

   ![Csúszka](media/scale-your-environment/slider.png)

   A kapacitás szorzó száma beírhatja azt is megteheti, a csúszka jobbra a szövegmezőben. 

4. Válassza ki **mentése** a környezet méretezése. A folyamatjelző jelenik meg, amíg a módosítás véglegesítve lesz, rövid ideig. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Győződjön meg arról, hogy az új kapacitást elegendő szabályozás elkerülése érdekében](time-series-insights-diagnose-and-solve-problems.md).
