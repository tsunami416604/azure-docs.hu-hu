---
title: Regionális redundancia és feladatátvétel az Azure HPC-gyorsítótárral
description: Az Azure HPC-gyorsítótárral való vész-helyreállítási feladatátvételi képességek biztosításának technikái
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4eb203915c8fedbef6af0e5a3bc14eff1835a92b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982165"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Több gyorsítótár használata a regionális feladatátvételi helyreállításhoz

Minden Egyes Azure HPC-gyorsítótár-példány egy adott előfizetésen belül és egy régióban fut. Ez azt jelenti, hogy a gyorsítótár-munkafolyamat esetleg megszakadhat, ha a régió teljes kimaradás.

Ez a cikk egy stratégiát ismerteti a munkamegszakítás kockázatának csökkentésére egy második régió használatával a gyorsítótár feladatátvételhez.

A kulcs a több régióból elérhető háttértároló használata. Ez a tárterület lehet egy helyszíni NAS-rendszer megfelelő DNS-támogatással, vagy az Azure Blob storage, amely a gyorsítótártól egy másik régióban található.

Ahogy a munkafolyamat folytatódik az elsődleges régióban, az adatok a régión kívüli hosszú távú tárolóba kerülnek. Ha a gyorsítótár-régió elérhetetlenné válik, létrehozhat egy duplikált Azure HPC-gyorsítótár-példányt egy másodlagos régióban, csatlakozhat ugyanahhoz a tárolóhoz, és folytathatja a munkát az új gyorsítótárból.

## <a name="planning-for-regional-failover"></a>A regionális feladatátvétel tervezése

A lehetséges feladatátvételre előkészített gyorsítótár beállításához kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a háttértároló elérhető egy második régióban.
1. Az elsődleges gyorsítótár-példány létrehozásának tervezésekor a második régióban is elő kell készítenie a telepítési folyamat replikálására. Tartalmazza a következő elemeket:

   1. Virtuális hálózat és alhálózat szerkezete
   1. Gyorsítótár kapacitása
   1. A tárolási cél adatok, nevek és névtérelérési utak
   1. Az ügyfélgépek részletei, ha ugyanabban a régióban találhatók, mint a gyorsítótár
   1. Mount parancs a gyorsítótár-ügyfelek általi használatra

   > [!NOTE]
   > Az Azure HPC-gyorsítótár programozott módon hozható létre, akár egy [Azure Resource Manager-sablonon](../azure-resource-manager/templates/overview.md) keresztül, akár az API közvetlen elérésével. A részletekért forduljon az Azure HPC cache csapatához.

## <a name="failover-example"></a>Például feladatátvételi példa

Például képzelje el, hogy meg szeretné keresni az Azure HPC-gyorsítótárat az Azure USA keleti régiójában. A helyszíni adatközpontban tárolt adatokhoz fog hozzáférni.

Az USA nyugati régiójában lévő gyorsítótárat feladatátvételi biztonsági másolatként használhatja.

A gyorsítótár létrehozásakor az USA keleti részén készítsen elő egy második gyorsítótárat az USA nyugati részén 2. Parancsfájlok vagy sablonok segítségével automatizálhatja ezt a készítményt.

Abban az esetben, ha az USA keleti régiójában az egész régióra kiterjedő hiba történik, hozza létre az USA nyugati régiójában készített gyorsítótárat.

A gyorsítótár létrehozása után adjon hozzá olyan tárolási célokat, amelyek ugyanarra a helyszíni adattárakra mutatnak, és ugyanazokat az összesített névtérútvonalakat használja, mint a régi gyorsítótár tárolási tárolói.

Ha az eredeti ügyfelek érintettek, hozzon létre új ügyfeleket az USA nyugati régiójában az új gyorsítótárral való használatra.

Minden ügyfélnek csatlakoztatnia kell az új gyorsítótárat, még akkor is, ha az ügyfeleket nem érintette a régió kimaradása. Az új gyorsítótár különböző csatlakoztatási címekkel rendelkezik, mint a régi.

## <a name="learn-more"></a>Részletek

Az Azure-alkalmazásarchitektúra útmutató további információt tartalmaz [arról, hogyan lehet helyreállítani egy régiószintű szolgáltatáskimaradás.](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
