---
title: Az Azure-beli virtuális gépeket az Azure Site Recovery szolgáltatással rendelkező elsődleges régióba adja vissza.
description: Ismerteti, hogyan lehet az Azure-beli virtuális gépeket az elsődleges régióba Azure Site Recovery szolgáltatással visszaadni.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f73d20c19e8fc26c553490772f5374e8a88a77b2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289306"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Azure-beli virtuális gép feladatátvétele Azure-régiók között

A [Azure site Recovery](site-recovery-overview.md) szolgáltatás a helyi gépek és az Azure-beli virtuális gépek (VM-EK) replikációjának, feladatátvételének és feladat-visszavételének kezelésével és koordinálásával járul hozzá a vész-helyreállítási stratégiához.

Ez az oktatóanyag egy Azure-beli virtuális gép feladatátvételét ismerteti. A feladatátvételt követően vissza kell térnie az elsődleges régióra, ha az elérhető. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> 
> * Adja vissza a virtuális gépet a másodlagos régióban.
> * Állítsa vissza újra az elsődleges virtuális gépet a másodlagos régióba.
> 
> [!NOTE]
> 
> Ez az oktatóanyag segítséget nyújt néhány virtuális gép átadásához egy adott régióban, és a minimális testreszabásokkal visszatérhet a forrás-régióhoz. További részletes utasításokért tekintse át az Azure-beli [virtuális gépek útmutatóit](../virtual-machines/windows/index.yml).

## <a name="before-you-start"></a>Előkészületek

* Győződjön meg arról, hogy a virtuális gép állapota **feladatátvételsel véglegesítve**van.
* Győződjön meg arról, hogy az elsődleges régió elérhető, és hogy új erőforrásokat tud létrehozni és elérni.
* Győződjön meg arról, hogy az ismételt védelem engedélyezve van.

## <a name="fail-back-to-the-primary-region"></a>Feladat-visszavétel az elsődleges régióba

A virtuális gépek újravédése után szükség szerint visszatérhet az elsődleges régióhoz.

1. A tárolóban válassza a **replikált elemek**lehetőséget, majd válassza ki az ÚJRAVÉDETT virtuális gépet.

    ![Feladat-visszavétel elsődlegesre](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. A **replikált elemek**területen válassza ki a virtuális gépet, majd válassza a **feladatátvétel**lehetőséget.
3. A **feladatátvétel**területen válasszon ki egy helyreállítási pontot a feladatátvételhez:
    - **Legutóbbi (alapértelmezett)**: feldolgozza a site Recovery szolgáltatásban lévő összes értéket, és a legalacsonyabb helyreállítási időpontot (RPO) biztosítja.
    - **Legutóbb feldolgozott**: visszaállít egy virtuális gépet a site Recovery által feldolgozott legutóbbi helyreállítási pontra.
    - **Egyéni**: a feladatátvétel egy adott helyreállítási pontra történik. Ez a lehetőség feladatátvételi teszt végrehajtásához hasznos.
4. Válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget, ha azt szeretné, hogy a site Recovery megkísérelje a virtuális gépek leállítását a Dr régióban a feladatátvétel elindítása előtt. A feladatátvétel akkor is folytatódik, ha a Leállítás sikertelen. 
5. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
6. A feladatátvétel befejeződése után ellenőrizze a virtuális gépet a bejelentkezéshez. Igény szerint módosíthatja a helyreállítási pontot.
7. A feladatátvétel ellenőrzése után válassza **a feladatátvétel véglegesítés**lehetőséget. A véglegesítési művelet törli az összes rendelkezésre álló helyreállítási pontot. A helyreállítási pont módosítása lehetőség már nem érhető el.
8. A virtuális gépnek a feladatátvételt és a feladatátvételt vissza kell mutatnia.

    ![Virtuális gép elsődleges és másodlagos régiókban](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> A felügyelt lemezeket használó gépek esetében, valamint a Site Recovery bővítmény 9.28. x. x verziójának futtatása a 40-es [kumulatív frissítéssel](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) site Recovery a másodlagos vész-helyreállítási régióban lévő gépek tisztítása, a feladat-visszavétel befejezése és a virtuális gépek ismételt védelme után. A másodlagos régióban nem szükséges manuálisan törölni a virtuális gépeket és a hálózati adaptereket. Vegye figyelembe, hogy a nem felügyelt lemezekkel rendelkező virtuális gépek nem törlődnek. Ha a feladatátvételt követően teljesen letiltja a replikálást, Site Recovery a virtuális gépek és hálózati adapterek mellett a vész-helyreállítási régióban lévő lemezeket is törli.

## <a name="next-steps"></a>További lépések

[További](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) információ az ismételt védelem folyamatáról.
