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
ms.openlocfilehash: 9bc2bd50fda34b493463898d8ef47ed2c246b7d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65751148"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Feladat-visszavételt az Azure virtuális Gépekhez az Azure-régiók között

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul a vész-helyreállítási stratégiát kezelésével és irányításával replikálása, feladatátvétele és feladat-visszavételhez helyszíni gépek és Azure-beli virtuális gépek (VM).

Ez az oktatóanyag leírja, hogyan visszavételhez egy Azure virtuális Gépen. Miután a feladatátvétel, kell visszaadja a feladatokat az elsődleges régióban elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> 
> * Feladat-visszavételt a virtuális Gépet a másodlagos régióban.
> * A másodlagos régióba az elsődleges virtuális gép ismételt védelme.
> 
> [!NOTE]
> 
> Ebben az oktatóanyagban megismerheti, hogyan átadja a feladatokat a célrégióban, és térjen vissza a forrásrégióban a minimális testreszabások néhány virtuális gépet. További részletes útmutatásért tekintse át a [útmutató végigvezeti az Azure virtuális gépekhez](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Előkészületek

* Győződjön meg arról, hogy a virtuális gép állapota **feladatátvétel véglegesítve**.
* Ellenőrizze, hogy az elsődleges régióban érhető el, és tudja hozhat létre, és azt az új erőforrások eléréséhez.
* Győződjön meg arról, hogy ismételt védelem engedélyezve van.

## <a name="fail-back-to-the-primary-region"></a>Feladat-visszavétel az elsődleges régióba

Virtuális gépek ismételt védelme után is visszaadja a feladatokat az elsődleges régió igény szerint.

1. A tárolóban, válassza ki a **replikált elemek**, és válassza ki a virtuális Gépet, amely újra lett.

    ![Feladat-visszavétel az elsődleges](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Válassza ki **feladatátvételi teszt** hajtson végre egy tesztet a feladatátvétel az elsődleges régióba biztonsági.
4. A helyreállítási pont és a virtuális hálózat feladatátvételi teszthez, majd válassza ki és **OK**. A teszt virtuális gép létrehozása az elsődleges régióban tekintheti meg.
5. A feladatátvételi teszt sikeres befejezése után jelölje ki a **feladatátvételi teszt utáni karbantartás** karbantartása a forrásrégióban a teszt feladatátvételhez létrehozott erőforrásokat.
6. A **replikált elemek**, a virtuális Gépet, majd válassza ki és **feladatátvételi**.
7. A **feladatátvételi**, válasszon ki egy helyreállítási pontot átadja a feladatokat:
    - **(Alapértelmezett) legújabb**: A Site Recovery szolgáltatásban lévő összes adatot dolgoz fel, és a legalacsonyabb helyreállítási időkorlátot (RPO) biztosít.
    - **Legutóbb feldolgozott**: A rendszer visszaállítja a virtuális Gépet a Site Recovery által feldolgozott legutóbbi helyreállítási pontot.
    - **Egyéni**: Átadja a feladatokat egy adott helyreállítási pontra. Ez a lehetőség feladatátvételi teszt végrehajtásához hasznos.

8. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvétel továbbra is fennáll, még akkor is, ha a Leállítás meghiúsul. Vegye figyelembe, hogy a Site Recovery nem törölje a forrás a feladatátvételt követően.
9. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
10. A feladatátvétel befejezése után ellenőrizze a virtuális gép, való bejelentkezéssel. A helyreállítási pont igény szerint módosíthatók.
11. A feladatátvétel ellenőrzése után válassza ki a **véglegesíti a feladatátvételt**. A véglegesítés törli az összes rendelkezésre álló helyreállítási pontok. A módosítás a helyreállítási pont beállítás már nem érhető el.
12. A virtuális gép átadta a feladatait, meg kell jelennie, és részt a feladatátvételben.

    ![Az elsődleges és másodlagos régióban található virtuális gép](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> A vész-helyreállítási virtuális gépek leállítása és felszabadítása állapotban marad. Ez az elvárt, mivel a Site Recovery a Virtuálisgép-adatok, amely lehet hasznos, ha feladatátvétel az elsődleges kiszolgálóról a másodlagos régióba később. Nem kell fizetnie a felszabadított virtuális gépekhez, így azok kell tartani.

## <a name="next-steps"></a>További lépések

[További](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) az ismételt védelem flowról.
