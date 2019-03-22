---
title: Azure virtuális gépek replikálása egy másodlagos Azure-régióba az Azure Site Recovery szolgáltatással vész-helyreállítási sikertelen.
description: Ismerje meg, hogyan végezhet az Azure virtuális gépek az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c8ce05e644ad556542314b17151b808586734824
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315317"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Nem sikerült az Azure virtuális gépek Azure-régiók között

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul a vész-helyreállítási stratégiát kezelésével és irányításával replikálása, feladatátvétele és vissza a helyszíni gépek és Azure-beli virtuális gépek (VM) sikertelen.

Ez az oktatóanyag leírja, hogyan visszavételhez egy Azure virtuális Gépen. A feladatátvétel után visszaadja a feladatokat az elsődleges régióra, amint az elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> 
> * Feladat-visszavételt az Azure virtuális Gépen a másodlagos régióban.
> * Ismételt védelem az elsődleges Azure virtuális Gépen, a másodlagos régióba.
> 
> [!NOTE]
> 
> Ebben az oktatóanyagban megismerheti, hogyan átadja a feladatokat a célrégióban, és térjen vissza a forrásrégióban a minimális testreszabások néhány virtuális gépet. További részletes útmutatásért tekintse át a "Hogyan To" a cikkek az Azure virtuális gépek.

## <a name="before-you-start"></a>Előkészületek

> * Győződjön meg arról, hogy a virtuális gép szerepel, a **feladatátvétel véglegesítve** állapota.
> * Ellenőrizze, hogy az elsődleges régióban érhető el, és Ön hozhat létre, és azt az új erőforrások eléréséhez.
> * Győződjön meg arról, hogy ismételt védelem engedélyezve van.

## <a name="fail-back-to-the-primary-region"></a>Feladat-visszavétel az elsődleges régióba

Után a virtuális gépek a védelem visszaállítása után, is a feladatokat az elsődleges régióba igény szerint.

1. A tárolóban, kattintson a **replikált elemek** , és válassza ki újra a védett virtuális gép.

    ![Feladat-visszavétel az elsődleges](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Kattintson a **feladatátvételi teszt** hajtson végre egy tesztet a feladatátvétel az elsődleges régióba biztonsági.
4. Válassza ki a helyreállítási pont és a virtuális hálózat feladatátvételi teszthez, és kattintson a **OK**. A teszt virtuális gép létrehozása az elsődleges régióban tekintheti meg.
5. Feladatátvételi teszt sikeres befejeződése után kattintson a **feladatátvételi teszt utáni karbantartás** karbantartása a forrásrégióban a teszt feladatátvételhez létrehozott erőforrásokat.
6. A **replikált elemek**, válassza ki a virtuális Gépet, és kattintson a **feladatátvételi**.
7. A **feladatátvételi**, válasszon egy helyreállítási pontot a feladatátvétel.
    - **(Alapértelmezett) legújabb**: A Site Recovery szolgáltatásban lévő összes adatot dolgoz fel, és a legalacsonyabb helyreállítási időkorlátot (RPO) biztosít.
    - **Legutóbb feldolgozott**: A rendszer visszaállítja a virtuális Gépet a Site Recovery által feldolgozott legutóbbi helyreállítási pontot.
    - **Egyéni**: Átadja a feladatokat egy adott helyreállítási pontra. Ez a lehetőség feladatátvételi teszt végrehajtásához hasznos.

8. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a Site Recovery megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. Vegye figyelembe, hogy a Site Recovery nem törölje a forrás a feladatátvételt követően.
9. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
10. A feladatátvétel után ellenőrizze a virtuális gép, való bejelentkezéssel. A helyreállítási pont igény szerint módosíthatók.
11. A feladatátvétel ellenőrzése után kattintson a **véglegesíti a feladatátvételt**. A véglegesítés törli az összes rendelkezésre álló helyreállítási pontok. A módosítás a helyreállítási pont beállítás már nem érhető el.
12. A virtuális gép átadta a feladatait, meg kell jelennie, és részt a feladatátvételben.

    ![Az elsődleges és másodlagos régióban található virtuális gép](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> A vész-helyreállítási virtuális gépek a leállítási állapot felszabadítása marad. Ez az elvárt, mivel a Site Recovery a Virtuálisgép-adatok, amely lehet hasznos, ha feladatátvétel az elsődleges kiszolgálóról a másodlagos régióba később. Nem kell fizetnie a felszabadított virtuális gépekhez, így azok kell tartani.

## <a name="next-steps"></a>További lépések

[További](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) az ismételt védelem flowról.
