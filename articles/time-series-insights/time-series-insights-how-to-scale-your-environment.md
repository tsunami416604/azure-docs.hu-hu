---
title: "Az Azure idő adatsorozat Insights környezet méretezése |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan adhat az Azure idő adatsorozat Insights környezet skálázása"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: ba6bd1ab05bb7e24dd1bc307218e7a772fbde601
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>A idő adatsorozat Insights környezet méretezése

Ez az oktatóanyag bemutatja, hogyan adhat a idő adatsorozat Insights környezet skálázása.

> [!NOTE]
> Termékváltozat-típusok között felskálázott nem engedélyezett. Egy S1 Sku tartalmazó környezet nem alakítható át olyan S2 környezetet.

## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 SKU érkező sebességét és a kapacitások

| S1 Termékváltozat-kapacitás | Érkező arány | Tárolókészlet teljes tárhelykapacitását
| --- | --- | --- |
| 1 | 1 GB (1 millió esemény) | Havonta 30 GB (30 millió esemény) |
| 10 | 10 GB-os (10 millió esemény) | Havonta 300 GB (300 millió esemény) |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU érkező sebességét és a kapacitások

| S2 Termékváltozat-kapacitás | Érkező arány | Tárolókészlet teljes tárhelykapacitását
| --- | --- | --- |
| 1 | 10 GB-os (10 millió esemény) | Havonta 300 GB (300 millió esemény) |
| 10 | 100 GB (100 millió esemény) | Havonta 3 TB (3 milliárd esemény) |

Kapacitások lineárisan lehessen méretezni, így a S1 sku 2 kapacitással rendelkező támogatja a 2 GB (2 millió) esemény / nap érkező sebessége és 60 GB (60 millió esemény).

## <a name="changing-the-capacity-of-your-environment"></a>A kapacitás, a környezet módosítása

1. Az Azure-portálon válassza ki a környezetben, amelynek meg szeretné változtatni kapacitása.
1. A beállítások konfigurálásához kattintson.
1. A kapacitás csúszka segítségével válassza ki a, amely megfelel az érkező sebesség és a tárolási kapacitás.

## <a name="next-steps"></a>Következő lépések

* Győződjön meg arról, hogy az új kapacitása elegendő, hogy megakadályozza a sávszélesség-szabályozás. További részletekért lásd: a *a környezetében előfordulhat, hogy lehet első halmozódni* szakasz [Itt](time-series-insights-diagnose-and-solve-problems.md).