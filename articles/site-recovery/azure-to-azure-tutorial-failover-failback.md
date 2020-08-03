---
title: Átadja az Azure-beli virtuális gépek feladatátvételét a másodlagos Azure-régióba a Azure Site Recovery szolgáltatással való vész-helyreállítás érdekében.
description: Ismerje meg, hogyan végezheti el a feladatátvételt a másodlagos Azure-régióba replikált Azure-beli virtuális gépek átadásával és ismételt védelemmel a Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8d38aa513b0829c2626fcd4a92c40faabff1f83e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502392"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Azure-beli virtuális gépek feladatátvétele és újbóli ellátása régiók között

Ez az oktatóanyag azt ismerteti, hogyan lehet átadni egy Azure-beli virtuális gépet (VM) egy másodlagos Azure-régióhoz a [Azure site Recovery](site-recovery-overview.md) szolgáltatással. A feladatátvételt követően újra kell védetté tenni a virtuális gépet. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure-beli virtuális gép feladatátvétele
> * Tegye újra védetté a másodlagos Azure-beli virtuális gépet, hogy az az elsődleges régióba replikálódjon.

> [!NOTE]
> Ez az oktatóanyag a legegyszerűbb útvonalat tartalmazza alapértelmezett beállításokkal és minimális testreszabással. Összetettebb forgatókönyvek esetén használja az Azure-beli virtuális gépekhez tartozó "útmutató" című cikket.


## <a name="prerequisites"></a>Előfeltételek

- Mielőtt elkezdené, tekintse át a feladatátvételsel kapcsolatos [gyakori kérdéseket](site-recovery-faq.md#failover) .
- Mindenképp végezzen [vészhelyreállítási próbát](azure-to-azure-tutorial-dr-drill.md) annak ellenőrzésére, hogy minden a várt módon működik-e.
- Ellenőrizze a virtuális gép tulajdonságait a feladatátvételi teszt futtatása előtt. A virtuális gépnek meg kell felelnie az [Azure-követelményeknek](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Feladatátvétel futtatása a másodlagos régióba

1. A **Replikált elemek** területen válassza ki azt a virtuális gépet, amelyen feladatátvételt szeretne végezni > **Feladatátvétel**

   ![A virtuális gép feladatátvételi beállításait bemutató képernyőkép.](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:

   * **Legutóbbi** (alapértelmezett): feldolgozza a site Recovery szolgáltatásban lévő összes értéket, és a legalacsonyabb helyreállítási időpontot (RPO) biztosítja.
   * **Legutóbb feldolgozott**: a virtuális gépet a site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontra visszaállíthatja.
   * **Egyéni**: a feladatátvétel egy adott helyreállítási pontra történik. Ez a lehetőség feladatátvételi teszt végrehajtásához hasznos.

3. Válassza a **gép leállítása a feladatátvétel** megkezdése előtt lehetőséget, ha azt szeretné, hogy a feladatátvétel elindítása előtt a site Recovery megkísérelje leállítani a forrás virtuális gépek leállítását. A Leállítás segít biztosítani az adatvesztést. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. Site Recovery a feladatátvétel után nem törli a forrást.

4. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

5. A feladatátvétel után a virtuális gépre való bejelentkezéssel ellenőrizze a virtuális gépet. Ha a virtuális gép egy másik helyreállítási pontjára szeretne ugrani, akkor a **Helyreállítási pont módosítása** lehetőséget használhatja.

6. Ha elégedett a feladatátviteli virtuális géppel, **véglegesítheti** a feladatátvételt.
   A véglegesítés törli a szolgáltatással elérhető összes helyreállítási pontot. Most már nem lehet módosítani a helyreállítási pontot.

> [!NOTE]
> Ha feladatátvételt végez egy olyan virtuális gépen, amelyhez a virtuális gép replikálásának engedélyezése után ad hozzá egy lemezt, a replikációs pontok megjelenítik a helyreállításhoz elérhető lemezeket. Ha például egy virtuális gép egyetlen lemezzel rendelkezik, és egy újat ad hozzá, a lemez hozzáadása előtt létrehozott replikációs pontok azt mutatják, hogy a replikációs pont "2 lemezből álló 1".

![A feladatátvételt egy hozzáadott lemezzel ábrázoló képernyőkép.](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>A másodlagos virtuális gép ismételt védelme

A virtuális gép feladatátvétele után ismét meg kell védenie azt, hogy az visszareplikálódjon az elsődleges régióba.

1. Győződjön meg arról, hogy a virtuális gép a **Feladatátvétel véglegesítve** állapotban van, és ellenőrizze, hogy az elsődleges régió elérhető-e, és létre tud-e hozni új erőforrásokat az elsődleges régióban, valamint el tudja-e érni azokat.
2. A **tárolóban**  >  **replikált elemek**területen kattintson a jobb gombbal arra a virtuális gépre, amelyen a feladatátvétel történt, majd válassza az **ismételt védelem**lehetőséget.

   ![Képernyőkép a virtuális gép újbóli védelmének beállításáról.](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Ellenőrizze, hogy a védelem iránya (másodlagos – elsődleges régió) már ki van-e választva.
3. Tekintse át az **Erőforráscsoport, a Hálózat, a Tárolás és a Rendelkezésre állási csoportok** adatait. Az újként megjelölt erőforrások az ismételt védelem művelet részeként jönnek létre.
4. Kattintson az **OK** gombra az ismételt védelmi feladat elindításához. Ez a feladat feltölti a célhelyet a legújabb adatokkal. Ezután replikálja az eltéréseket az elsődleges régióba. A virtuális gép most védett állapotban van.

## <a name="next-steps"></a>További lépések
- Az ismételt védelem után [megtudhatja, hogyan](azure-to-azure-tutorial-failback.md) térhet vissza az elsődleges régióhoz, ha elérhető.
- [További](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) információ az ismételt védelem folyamatáról.
