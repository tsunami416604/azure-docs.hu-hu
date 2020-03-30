---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471693"
---
Egyelőre csak a prémium szintű SSD-k engedélyezhetik a megosztott lemezeket. A szolgáltatást támogató lemezméretek P15 és nagyobbak. A különböző lemezméretek `maxShares` eltérő korláttal rendelkezhetnek, `maxShares` amelyet az érték beállításakor nem lehet túllépni.

Minden lemezhez megadhat `maxShares` egy értéket, amely a lemez egyidejű megosztását letudó csomópontok maximális számát jelöli. Ha például egy kétcsomópontos feladatátvevő fürt beállítását tervezi, állítsa be a programot. `maxShares=2` A maximális érték egy felső határ. A csomópontok csatlakozhatnak a fürthöz, illetve kihagyhatnak (csatlakoztathatják vagy leválaszthatják a `maxShares` lemezt), feltéve, hogy a csomópontok száma alacsonyabb a megadott értéknél.

> [!NOTE]
> Az `maxShares` érték csak akkor állítható be vagy szerkeszthető, ha a lemez le van választva az összes csomópontról.

Az alábbi táblázat a lemezméret `maxShares` szerint megengedett maximális értékeket mutatja be:

|Lemezméretek  |maxShares limit  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

A lemez IOPS- és sávszélesség-korlátait `maxShares` nem befolyásolja az érték. A P15-lemez maximális IOPS-a például 1100, függetlenül attól, hogy maxShares = 1 vagy maxShares > 1.