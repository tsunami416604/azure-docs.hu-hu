---
title: Feladatátvétel, és az Azure-beli virtuális gépek replikálása egy másodlagos Azure-régióba vész-helyreállítási az Azure Site Recovery szolgáltatással.
description: Ismerje meg, hogyan feladatátvétel, és újra védi az Azure-beli virtuális gépek replikált egy másodlagos Azure-régióban a vész-helyreállítási, az Azure Site Recovery szolgáltatás.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68782595"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Feladatátvétel és az Azure-beli virtuális gépek régiók közötti újbóli védelme

Ez az oktatóanyag ismerteti, hogyan lehet feladatátvételegy Azure virtuális gép (VM) egy másodlagos Azure-régióban az [Azure Site Recovery](site-recovery-overview.md) szolgáltatással. Miután feladatátvételt, újra védi a virtuális gép. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure-beli virtuális gép feladatátvétele
> * A másodlagos Azure-virtuális gép ismételt védelme, hogy replikálja az elsődleges régióba.

> [!NOTE]
> Ez az oktatóanyag a legegyszerűbb elérési utat tartalmazza az alapértelmezett beállításokkal és a minimális testreszabással. Összetettebb forgatókönyvek esetén használja az Azure-beli virtuális gépek "Hogyan" című cikkeit.


## <a name="prerequisites"></a>Előfeltételek

- Mielőtt elkezdené, tekintse át a feladatátvételre [kapcsolatos gyakori kérdéseket.](site-recovery-faq.md#failover)
- Mindenképp végezzen [vészhelyreállítási próbát](azure-to-azure-tutorial-dr-drill.md) annak ellenőrzésére, hogy minden a várt módon működik-e.
- Ellenőrizze a virtuális gép tulajdonságait a feladatátvételi teszt futtatása előtt. A virtuális gépnek meg kell felelnie az [Azure-követelményeknek](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Feladatátvétel futtatása a másodlagos régióba

1. A **Replikált elemek** területen válassza ki azt a virtuális gépet, amelyen feladatátvételt szeretne végezni > **Feladatátvétel**

   ![Feladatátvétel](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:

   * **Legújabb** (alapértelmezett): Feldolgozza a Site Recovery szolgáltatás összes adatát, és biztosítja a legalacsonyabb helyreállítási pont célkitűzést (RPO).
   * **Legutóbbi feldolgozott:** Visszaállítja a virtuális gépet a Site Recovery szolgáltatás által feldolgozott legújabb helyreállítási pontra.
   * **Egyéni**: Átadja a következőt egy adott helyreállítási pontnak. Ez a lehetőség feladatátvételi teszt végrehajtásához hasznos.

3. Válassza **a Számítógép leállítása a feladatátvétel megkezdése előtt** lehetőséget, ha azt szeretné, hogy a Site Recovery a feladatátvétel elírása előtt megkísérli a forrásvirtuális gépek leállítását. A leállítás segít az adatvesztés számának biztosításában. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A Site Recovery nem törli a forrást a feladatátvétel után.

4. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

5. A feladatátvétel után a virtuális gépre való bejelentkezéssel ellenőrizze a virtuális gépet. Ha a virtuális gép egy másik helyreállítási pontjára szeretne ugrani, akkor a **Helyreállítási pont módosítása** lehetőséget használhatja.

6. Ha elégedett a feladatátviteli virtuális géppel, **véglegesítheti** a feladatátvételt.
   A véglegesítés törli a szolgáltatással elérhető összes helyreállítási pontot. Most már nem tudja módosítani a helyreállítási pontot.

> [!NOTE]
> Ha feladatátvételt kap egy virtuális gép, amelyhez lemezt ad hozzá, miután engedélyezte a virtuális gép replikációját, a replikációs pontok a helyreállításhoz rendelkezésre álló lemezeket jelenítik meg. Ha például egy virtuális gép egyetlen lemezzel rendelkezik, és újat ad hozzá, a lemez hozzáadása előtt létrehozott replikációs pontok azt mutatják, hogy a replikációs pont "1 a 2 lemezből" áll.

![Feladatátvétel hozzáadott lemezzel](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>A másodlagos virtuális gép ismételt védelme

A virtuális gép feladatátvétele után ismét meg kell védenie azt, hogy az visszareplikálódjon az elsődleges régióba.

1. Győződjön meg arról, hogy a virtuális gép a **Feladatátvétel véglegesítve** állapotban van, és ellenőrizze, hogy az elsődleges régió elérhető-e, és létre tud-e hozni új erőforrásokat az elsődleges régióban, valamint el tudja-e érni azokat.
2. A **Vault** > **replikált elemekben**kattintson a jobb gombbal a feladatátvételre jogosult virtuális gépre, majd válassza az **Újravédelem parancsot.**

   ![Kattintson a jobb gombbal az ismételt védelemhez](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Ellenőrizze, hogy az elsődleges régióhoz képest másodlagos védelmi irány már be van-e jelölve.
3. Tekintse át az **Erőforráscsoport, a Hálózat, a Tárolás és a Rendelkezésre állási csoportok** adatait. Az újként megjelölt erőforrások az újravédelmi művelet részeként jönnek létre.
4. Kattintson az **OK** gombra az ismételt védelmi feladat elindításához. Ez a feladat feltölti a célhelyet a legújabb adatokkal. Ezután replikálja az eltéréseket az elsődleges régióba. A virtuális gép most védett állapotban van.

## <a name="next-steps"></a>További lépések
- Az újbóli védelem után [megtudhatja, hogyan lehet](azure-to-azure-tutorial-failback.md) visszaadni az elsődleges régióba, ha elérhető.
- [További információ](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) az újravédelmi folyamatról.
