---
title: Az Azure-beli virtuális gépek újbóli védelme az elsődleges régióban az Azure Site Recovery használatával | Microsoft dokumentumok
description: Bemutatja, hogyan lehet újra megvédeni az Azure-beli virtuális gépeket a feladatátvétel után, a másodlagos és az elsődleges régióban, az Azure Site Recovery használatával.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 73747b8331054cdc3bfa1f4073ccf2cdb62ab326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283242"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Az Azure-beli virtuális gépek feladatátvétel immár a feladatkezelő régióban

Ha [az](site-recovery-failover.md) Azure-beli virtuális gépek az egyik régióból a másikba az [Azure Site Recovery](site-recovery-overview.md)használatával, a virtuális gépek indítása a másodlagos régióban, nem **védett** állapotban. Ha vissza szeretné adni a virtuális gépeket az elsődleges régiónak, tegye a következő feladatokat:

1. A másodlagos régióban a virtuális gépek ismételt védelme, hogy azok az elsődleges régióba replikálódjanak.
1. Az újravédelem befejezése után, és a virtuális gépek replikálása, átveheti a másodlagos elsődleges régió.

## <a name="prerequisites"></a>Előfeltételek

- A virtuális gép feladatátvételaz elsődleges másodlagos régió kell elkövetni.
- Az elsődleges célhelynek elérhetőnek kell lennie, és képesnek kell lennie az adott régióban lévő erőforrások elérésére vagy létrehozására.

## <a name="reprotect-a-vm"></a>Virtuális gép újbóli védelme

1. A **Vault** > **replikált elemek ben**kattintson a jobb gombbal a feladatátvételi feladatátvételre, és válassza az **Újravédelem parancsot.** Az újravédelmi iránynak a másodlagostól az elsődlegesig kell megjelennie.

   ![Védelem újravédelme](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Tekintse át az erőforráscsoportot, a hálózatot, a tárhelyet és a rendelkezésre állási csoportokat. Ezt követően kattintson az **OK** gombra. Ha vannak újként megjelölt erőforrások, azok az újravédelmi folyamat részeként jönnek létre.
1. A reprotection feladat maggot a célhely a legfrissebb adatokkal. A feladat befejezése után a különbözeti replikáció megtörténik. Ezután átadhatja a feladatátvételt az elsődleges helyre. A testreszabási lehetőség segítségével kiválaszthatja a tárfiókot vagy a hálózatot, amelyet az újravédelem során használni kíván.

   ![Testreszabási beállítás](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Beállítások újravédelmének testreszabása

A célvirtuális gép következő tulajdonságait testreszabhatja az újravédelem során.

![Testreszabás](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Tulajdonság |Megjegyzések  |
|---------|---------|
|Cél erőforráscsoport | Módosítsa azt a célerőforrás-csoportot, amelyben a virtuális gép létrejön. Az újravédelem részeként a cél virtuális gép törlődik. Választhat egy új erőforráscsoportot, amely alatt a feladatátvétel után létre hozhatja a virtuális gép. |
|Virtuális hálózat megcélzása | A célhálózat nem módosítható az újravédelmi feladat során. A hálózat módosításához végezze el újra a hálózati hozzárendelést. |
|Céltároló (a másodlagos virtuális gép nem használ felügyelt lemezeket) | Módosíthatja a virtuális gép által a feladatátvétel után használt tárfiókot. |
|Kópia felügyelt lemezei (a másodlagos virtuális gép felügyelt lemezeket használ) | A Site Recovery replikáni kezelt lemezeket hoz létre az elsődleges régióban a másodlagos virtuális gép felügyelt lemezei tükrözéséhez. |
|Gyorsítótár | Megadhatja a replikáció során használandó gyorsítótár-tárfiókot. Alapértelmezés szerint egy új gyorsítótár-tárfiók jön létre, ha nem létezik. |
|Rendelkezésre állási csoport | Ha a másodlagos régióban lévő virtuális gép egy rendelkezésre állási csoport része, kiválaszthatja a cél virtuális gép rendelkezésre állási készletét az elsődleges régióban. Alapértelmezés szerint a Site Recovery megpróbálja megtalálni a meglévő rendelkezésre állási készlet az elsődleges régióban, és használja azt. A testreszabás során új rendelkezésre állási készletet adhat meg. |

### <a name="what-happens-during-reprotection"></a>Mi történik az újravédelem során?

Alapértelmezés szerint a következők következnek be:

1. A gyorsítótár-tárfiók jön létre a régióban, ahol a feladatátvételi virtuális gép fut.
1. Ha a céltárfiók (az elsődleges régióban az eredeti tárfiók) nem létezik, egy új jön létre. A hozzárendelt tárfiók neve a másodlagos virtuális gép által használt tárfiók `asr`neve, amely a.
1. Ha a virtuális gép felügyelt lemezeket használ, replikáni kezelt lemezek jönnek létre az elsődleges régióban a másodlagos virtuális gép lemezeiről replikált adatok tárolására.
1. Ha a cél rendelkezésre állási csoport nem létezik, egy új jön létre a feladat újravédelmi feladat részeként, ha szükséges. Ha testre szabta az újravédelmi beállításokat, akkor a program a kiválasztott készletet használja.

Amikor újravédelmi feladatot indít el, és a cél virtuális gép létezik, a következő történik:

1. A céloldali virtuális gép ki van kapcsolva, ha fut.
1. Ha a virtuális gép felügyelt lemezeket használ, az eredeti `-ASRReplica` lemez egy másolata jön létre utótaggal. Az eredeti lemezek törlődnek. A `-ASRReplica` másolatok replikációra szolgálnak.
1. Ha a virtuális gép nem felügyelt lemezeket használ, a célvirtuális gép adatlemezei leválnak, és replikációra használják őket. Az operációs rendszer lemezének egy példányát hoz létre, és csatolja a virtuális géphez. Az eredeti operációsrendszer-lemez levan választva, és replikációra használják.
1. A program csak a forráslemez és a céllemez közötti módosításokat szinkronizálja. A különbözetek kiszámítása a lemezek összehasonlításával, majd átvitt. Ellenőrizze az alábbi, hogy megtalálja a becsült időt, hogy teljes a reprotection.
1. A szinkronizálás befejezése után megkezdődik a különbözeti replikáció, és a replikációs házirendnek megfelelő helyreállítási pont jön létre.

Amikor újravédelmi feladatot indít el, és a célvirtuális gép és a lemezek nem léteznek, a következő történik:

1. Ha a virtuális gép felügyelt lemezeket használ, `-ASRReplica` a replikalemezek utótaggal jönnek létre. A `-ASRReplica` másolatok replikációra szolgálnak.
1. Ha a virtuális gép nem felügyelt lemezeket használ, a replikalemezek a céltárfiókban jönnek létre.
1. A rendszer a teljes lemezeket a feladatátvevő régióból az új célterületre másolja.
1. A szinkronizálás befejezése után megkezdődik a különbözeti replikáció, és a replikációs házirendnek megfelelő helyreállítási pont jön létre.

#### <a name="estimated-time-to-do-the-reprotection"></a>Becsült idő az újravédelemhez

A legtöbb esetben az Azure Site Recovery nem replikálja a teljes adatokat a forrásrégióba.
A következő feltételek határozzák meg, hogy mennyi adatot replikál a replikált:

1. Ha a forrás virtuális gép adatait törlik, sérült, vagy nem érhető el valamilyen okból, például egy erőforráscsoport módosítása/törlése, majd az újravédelem során a teljes kezdeti replikáció történik, mert nincs elérhető adat a forrásrégióban használható.
1. Ha a forrás virtuálisgép-adatok elérhetők, akkor csak a különbözetek számítási összehasonlításával mindkét lemezt, majd át. Ellenőrizze az alábbi táblázatot, hogy megkapja a becsült időt.

|Példa helyzet | Az újravédelemhez szükséges idő |
|---|---|
|A forrásrégió 1 virtuális gép1 TB-os szabványos lemezzel rendelkezik.<br/>Csak 127 GB-os adatokat használ a rendszer, és a lemez többi része üres.<br/>A lemez típusa szabványos 60 MiB/S átviteli fokkal.<br/>Nincs adatváltozás a feladatátvétel után.| Hozzávetőleges idő: 45 perc – 1,5 óra.<br/>Az újravédelem során a Site Recovery feltölti az összes olyan adat ellenőrző összegét, amely 127 GB/ 45 MB-ot, körülbelül 45 percet vesz igénybe.<br/>Néhány rezsi idő szükséges site recovery automatikus méretezés, körülbelül 20-30 perc.<br/>Nincs ki- és nyomozó. |
|A forrásrégió 1 virtuális gép1 TB-os szabványos lemezzel rendelkezik.<br/>A rendszer csak 127 GB-os adatokat használ, és a lemez többi része üres.<br/>A lemez típusa szabványos 60 MiB/S átviteli fokkal.<br/>A feladatátvétel után 45 GB-os adatváltozások.| Hozzávetőleges idő: 1 óra – 2 óra.<br/>Az újravédelem során a Site Recovery feltölti az összes olyan adat ellenőrző összegét, amely 127 GB/ 45 MB-ot, körülbelül 45 percet vesz igénybe.<br/>Átviteli idő a 45 GB/45 Mb/s, körülbelül 17 perces módosítások alkalmazásához.<br/>A kimenő forgalom díja 45 GB-os adatmódosítás, nem pedig az ellenőrzőösszeg esetében történik. |

Ha a virtuális gép újra védett, miután nem sikerült vissza az elsődleges régióba (azaz ha a virtuális gép újra védett az elsődleges régióból a VÉSZ-régióba), a cél virtuális gép és a kapcsolódó hálózati adapter(ek) törlődnek.

Ha a virtuális gép újra védett a VÉSZ-régióból az elsődleges régióba, nem töröljük az egyszeri elsődleges virtuális gép és a társított hálózati adapter(ek).

## <a name="next-steps"></a>További lépések

A virtuális gép védelme után kezdeményezheti a feladatátvételt. A feladatátvétel leállítja a virtuális gép a másodlagos régióban, és létrehozza és leállítja a virtuális gép az elsődleges régióban, rövid állásidő ebben a folyamatban. Azt javasoljuk, hogy válassza ki a megfelelő időpontot ehhez a folyamathoz, és futtasson egy teszt feladatátvételt, mielőtt teljes feladatátvételt szeretne az elsődleges helyre. [További információ](site-recovery-failover.md) az Azure Site Recovery feladatátvételről.
