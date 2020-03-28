---
title: Az Azure-beli virtuális gépek et az Azure Site Recovery szolgáltatással egy elsődleges régióba kell visszaadni.
description: Bemutatja, hogyan lehet az Azure-beli virtuális gépeket az elsődleges régióba visszaadni az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74091345"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Azure-beli virtuális gép visszavétele az Azure-régiók között

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul a vész-helyreállítási stratégia kezeléséhez és vezényléséhez a replikáció, a feladatátvétel és a feladat-visszavétel a helyszíni gépek és az Azure virtuális gépek (Virtuális gépek).

Ez az oktatóanyag ismerteti, hogyan lehet egy Azure virtuális gép visszavétele. Miután feladatátvételt, akkor vissza kell adnia az elsődleges régióba, ha elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> 
> * A másodlagos régióban a virtuális gép visszavétele.
> * Az elsődleges virtuális gép újbóli védelme a másodlagos régióban.
> 
> [!NOTE]
> 
> Ez az oktatóanyag segít néhány virtuális gép feladatátvételben egy célrégióban, és vissza a forrásrégióba minimális testreszabások. További részletes útmutatásért tekintse át az [Azure virtuális gépek útmutatóit.](https://docs.microsoft.com/azure/virtual-machines/windows/)

## <a name="before-you-start"></a>Előkészületek

* Győződjön meg arról, hogy a virtuális gép állapota **feladatátvétel véglegesítve.**
* Ellenőrizze, hogy az elsődleges régió elérhető-e, és hogy képes-e új erőforrásokat létrehozni és elérni benne.
* Győződjön meg arról, hogy az újravédelem engedélyezve van.

## <a name="fail-back-to-the-primary-region"></a>Feladat-visszavétel az elsődleges régióba

A virtuális gépek újbóli védelme után szükség szerint visszaléphet az elsődleges régióba.

1. A tárolóban válassza **a Replikált elemek**lehetőséget, majd válassza ki az újravédett virtuális gépét.

    ![Feladat-visszavétel az elsődleges](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. A **Replikált elemek csoportban**jelölje ki a virtuális gép, majd a **Feladatátvétel**lehetőséget.
3. A **Feladatátvétel**csoportban válassza ki azt a helyreállítási pontot, amelynek feladatátvételre van lehetőség:
    - **Legújabb (alapértelmezett)**: Feldolgozza a Site Recovery szolgáltatás összes adatát, és biztosítja a legalacsonyabb helyreállítási pont célkitűzést (RPO).
    - **Legutóbbi feldolgozott:** Visszaállítja a virtuális gép a legújabb helyreállítási pont, amely a Site Recovery által feldolgozott.
    - **Egyéni**: Átadja a következőt egy adott helyreállítási pontnak. Ez a lehetőség feladatátvételi teszt végrehajtásához hasznos.
4. Válassza a Számítógép leállítása a **feladatátvétel megkezdése előtt** lehetőséget, ha azt szeretné, hogy a Site Recovery megkísérelje a virtuális gépek leállítását a DR régióban a feladatátvétel elkísérése előtt. A feladatátvétel akkor is folytatódik, ha a leállítás sikertelen. 
5. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
6. A feladatátvétel befejezése után ellenőrizze a virtuális gép bejelentkezéssel. Szükség szerint módosíthatja a helyreállítási pontot.
7. Miután ellenőrizte a feladatátvételt, válassza **a Feladatátvétel véglegesítése**lehetőséget. A véglegesítés törli az összes rendelkezésre álló helyreállítási pontot. A változás-helyreállítási pont beállítás már nem érhető el.
8. A virtuális gép nek meg kell jelennie a feladatátvétel, és a feladat-visszavétel.

    ![Virtuális gép az elsődleges és másodlagos régiókban](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> A Site Recovery bővítmény 9.28.x.x verzióját futtató gépek esetén a 40 Site Recovery [összesítő összegző](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) frissítőcsomag megtisztítja a gépeket a másodlagos vész-helyreállítási régióban, miután a feladat-visszavétel befejeződött, és a virtuális gépek újra védettek. Nincs szükség a másodlagos régióban lévő virtuális gépek és hálózati adapterek manuális törlésére. Ha a rendszer teljesen letiltja a replikációt a hiba elhárítása után, a Site Recovery a virtuális gépek és a hálózati adapterek mellett törli a lemezeket a vész-helyreállítási régióban.

## <a name="next-steps"></a>További lépések

[További információ](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) az újravédelmi folyamatról.
