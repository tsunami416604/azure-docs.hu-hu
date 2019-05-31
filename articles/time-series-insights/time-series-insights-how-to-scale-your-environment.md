---
title: Az Azure Time Series Insights-környezet skálázása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan skálázható az Azure Time Series Insights-környezetet. Az Azure portal segítségével hozzáadhatók vagy kivonhatók díjszabási Termékváltozat kapacitásokat.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: be06fd5a6f05d750e6ca9801a6004f7180a12d5c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238994"
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

1. A Time Series Insights-környezethez a menüben válassza ki a **konfigurálása**.

   [![configure.png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Módosítsa a **kapacitás** csúszkával válassza ki a kapacitás, amely megfelel a követelményeknek, a bejövő forgalom arányát és a tárolási kapacitást. Figyelje meg a **bejövő forgalom**, **tárolókapacitás**, és **becsült költség** dinamikusan frissülnek a megjelenítése, milyen hatású a változtatás.

   [![Csúszka](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   A kapacitás szorzó száma beírhatja azt is megteheti, a csúszka jobbra a szövegmezőben.

1. Válassza ki **mentése** a környezet méretezése. A folyamatjelző jelenik meg, amíg a módosítás véglegesítve lesz, rövid ideig.

## <a name="next-steps"></a>További lépések

- Győződjön meg arról, hogy az új kapacitást [elegendő szabályozás elkerülése érdekében](time-series-insights-diagnose-and-solve-problems.md).