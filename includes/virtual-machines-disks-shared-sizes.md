---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008339"
---
Egyelőre csak az ultralemezek és a prémium szintű SSD-k engedélyezhetik a megosztott lemezeket. A különböző lemezméretek `maxShares` eltérő korláttal rendelkezhetnek, `maxShares` amelyet az érték beállításakor nem lehet túllépni. Prémium szintű SSD-k esetén a lemezeik megosztását támogató lemezméretek P15 és nagyobbak.

Minden lemezhez megadhat `maxShares` egy értéket, amely a lemez egyidejű megosztását letudó csomópontok maximális számát jelöli. Ha például egy kétcsomópontos feladatátvevő fürt beállítását tervezi, állítsa be a programot. `maxShares=2` A maximális érték egy felső határ. A csomópontok csatlakozhatnak a fürthöz, illetve kihagyhatnak (csatlakoztathatják vagy leválaszthatják a `maxShares` lemezt), feltéve, hogy a csomópontok száma alacsonyabb a megadott értéknél.

> [!NOTE]
> Az `maxShares` érték csak akkor állítható be vagy szerkeszthető, ha a lemez le van választva az összes csomópontról.

### <a name="premium-ssd-ranges"></a>Prémium SSD tartományok

Az alábbi táblázat a prémium `maxShares` szintű lemezméretek megengedett maximális értékeit mutatja be:

|Lemezméretek  |maxShares limit  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

A lemez IOPS- és sávszélesség-korlátait `maxShares` nem befolyásolja az érték. A P15-lemez maximális IOPS-a például 1100, függetlenül attól, hogy maxShares = 1 vagy maxShares > 1.

### <a name="ultra-disk-ranges"></a>Ultra lemeztartományok

A `maxShares` minimális érték 1, `maxShares` míg a maximális érték 5. Az ultralemezeken nincsenek méretkorlátozások, bármilyen méretű ultralemez `maxShares`bármilyen értéket használhat, akár a maximális értékkel együtt.