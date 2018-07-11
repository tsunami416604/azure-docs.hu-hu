---
title: Ismételt védelem sikertelen volt az Azure virtuális gépen az elsődleges Azure régióba az Azure Site Recoveryvel |} A Microsoft Docs
description: Ismerteti, hogyan lehet ismételt védelme az Azure-beli virtuális gépek egy másodlagos régióban egy elsődleges régióban, az Azure Site Recovery a feladatátvétel után.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 9759e209f15622d70aaa833a993234863ac1053c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918866"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Ismételt védelem sikertelen volt az Azure virtuális gépen az elsődleges régióba


Ha Ön [átadja a feladatokat](site-recovery-failover.md) Azure virtuális gépek egyik régióból egy másik használatával [Azure Site Recovery](site-recovery-overview.md), a virtuális gépek rendszerindítási a másodlagos régióban, nem védett állapotban. Ha visszavétel a virtuális gépek az elsődleges régióra, tegye a következőt szeretné:

- A másodlagos régió virtuális gépek ismételt védelme, hogy megkezdődhessen replikálni az elsődleges régióba. 
- Miután ismételt védelem befejezése és a virtuális gépeket replikál, akkor a feladatokat őket a másodlagos elsődleges régió felé.

> [!WARNING]
> Ha [áttelepített](migrate-overview.md#what-do-we-mean-by-migration) gépek az elsődleges kiszolgálóról a másodlagos régióba, a virtuális gép áthelyezése egy másik erőforráscsoportot, vagy az Azure virtuális gép törölve, nem a virtuális gép ismételt védelme, és feladat-visszavételt azt.


## <a name="prerequisites"></a>Előfeltételek
1. A virtuális gép feladatátvétel az elsődleges másodlagos régióba véglegesíteni kell lennie.
2. A cél elsődleges helyének elérhetőnek kell lennie, és meg kell tudni eléréséhez, vagy hozzon létre erőforrásokat az adott régióban.

## <a name="reprotect-a-vm"></a>A virtuális gép ismételt védelme

1. A **tároló** > **replikált elemek**, kattintson a jobb gombbal a feladatátvételen átesett virtuális gép, és válassza ki **ismételt védelem**. Az ismételt védelem irányát az elsődleges, másodlagos kell megjelennie. 

  ![Az ismételt védelem](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Tekintse át az erőforrás csoport, hálózati, tárolási és rendelkezésre állási csoportokat. Ezután kattintson az **OK** gombra. Ha vannak megjelölve az új erőforrásokat, akkor jönnek létre az ismételt védelem folyamat részeként.
3. Az ismételt védelem feladat feltölti a célhelyet, a legújabb adatokkal. Amely befejezését követően a változásreplikálás kerül sor. Ezt követően is átadja a feladatokat az elsődleges hely. Kiválaszthatja a tárfiók, vagy a hálózati során használni kívánt ismételt védelme, a Testreszabás beállítás használatával.

  ![A beállítás testreszabása](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Az ismételt védelem beállításainak testreszabása

Testre szabhatja a következő tulajdonságokat a cél VMe ismételt védelem során.

![Testreszabás](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Tulajdonság |Megjegyzések  |
|---------|---------|
|Céloldali erőforráscsoport     | Módosítsa a céloldali erőforráscsoport, amelyben a virtuális gép létrejött. Ismételt védelem részeként a cél virtuális gép törlődik. Választhat egy új erőforráscsoportot, amelybe a feladatátvételt követően a virtuális gép létrehozásához.        |
|Cél virtuális hálózattal     | A célhálózat nem módosítható a védelem-újrabeállítás során. Ha módosítani szeretné a hálózaton, ismételje meg a hálózatleképezést.         |
|Cél tárterület (a másodlagos virtuális gép nem használ felügyelt lemezeket)     | A storage-fiók, amely a virtuális gép a feladatátvételt követően módosíthatja.         |
|Replikált felügyelt lemezek (a másodlagos virtuális gép felügyelt lemezeket használ.)    | A Site Recovery replika felügyelt lemezeket az elsődleges régióból történő tükrözésének a másodlagos virtuális gép felügyelt lemezeket hoz létre.         | 
|Cache Storage     | Megadhatja a replikálás során használandó gyorsítótárfiókot. Alapértelmezés szerint egy új gyorsítótárfiók van hozhatók létre, ha még nem létezik.         |
|Rendelkezésre állási csoport     |Ha a másodlagos régió virtuális gép egy rendelkezésre állási csoport része, választhat egy rendelkezésre állási csoportot a céloldali virtuális Gépet az elsődleges régióba. Alapértelmezés szerint a Site Recovery megpróbálja megkeresni a meglévő rendelkezésre állási az elsődleges régióban, és használja azt. Testreszabás megadhat egy új rendelkezésre állási csoportot.         |


### <a name="what-happens-during-reprotection"></a>Mi történik az ismételt védelem alatt?

Alapértelmezés szerint a következő történik:

1. A gyorsítótárfiók az elsődleges régióban jön létre.
2. Ha a célként megadott tárfiók (az eredeti storage-fiók az elsődleges régióba) nem létezik, a rendszer létrehoz egy új. A hozzárendelt tárfiók nevét az "asr" utótaggal másodlagos virtuális gép által használt tárfiók neve.
3. Ha a virtuális gép felügyelt lemezeket használ, a replikált felügyelt lemez jön létre az elsődleges régióban, a másodlagos virtuális gép lemezeinek replikált adatok tárolásához. 
4. Ha a cél rendelkezésre állási csoport nem létezik, egy új az ismételt védelmi feladat részeként létrejön, ha szükséges. Ha az ismételt védelem beállításainak testre szabott, a kiválasztott készlet szolgál.

Ha az ismételt védelmi feladat, és a cél virtuális gép létezik indít el, az alábbiak történnek:

1. A szükséges összetevők védelem-újrabeállítás során jönnek létre. Ha már léteznek, hogy újra felhasználhatók.
2. A virtuális gép ki van kapcsolva, ha céloldalon fut.
3. A célként megadott oldal Virtuálisgép-lemez másolja a program a Site Recovery egy tárolóba egy kezdőblob.
4. A virtuális gép céloldalon majd törlődik.
5. A jelenlegi forrást használja a kezdőérték-blobnak (másodlagos) virtuális gép replikálása oldalán. Ez biztosítja, hogy csak a változásokat a rendszer replikálja.
6. A forrás lemez és a kezdőérték-blobnak között jelentős változásokat szinkronizálja a rendszer. Ez eltarthat egy ideig.
7. Az ismételt védelmi feladat befejezése után a változásreplikálás kezdődik, és létrehoz egy helyreállítási pontot a replikációs szabályzat megfelelően.
8. Az ismételt védelmi feladat sikeresen lezajlott, miután a virtuális gépek védett állapotba kerül.

## <a name="next-steps"></a>További lépések

Miután a virtuális gép védett, feladatátvevő is kezdeményezhető. A feladatátvétel leállítja a virtuális Gépet a másodlagos régióban és hoz létre, és az elsődleges régióban, néhány kisebb üzemkimaradás indul el a virtuális gép. Ennek megfelelően válassza ki egy időpontot, és, hogy a feladatátvételi teszt futtatásához javasoljuk, de az elsődleges hely teljes feladatátvételt kezdeményez. [További](site-recovery-failover.md) feladatátvételi kapcsolatban.

