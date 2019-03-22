---
title: Átadja a feladatokat, és az ismételt védelem – Azure-beli virtuális gépek replikálása egy másodlagos Azure-régióba az Azure Site Recovery szolgáltatással vész-helyreállítási.
description: Megtudhatja, hogyan végezhet feladatátvételt és a vészhelyreállítás, az Azure Site Recovery szolgáltatással egy másodlagos Azure-régióba replikálja az Azure virtuális gépek ismételt védelme.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c19e554571927efa907350df1f1fbbd8ff491c42
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314756"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Feladatátadás és Azure virtuális gépek ismételt védelme a régiók között

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Ez az oktatóanyag leírja, hogyan végezhet feladatátvételt egy másodlagos Azure-régióba az Azure virtuális Gépekhez. Miután a feladatátvétel, a virtuális gép ismételt védelme. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure-beli virtuális gép feladatátvétele
> * A másodlagos Azure virtuális gép ismételt védelme, hogy az elsődleges régióba replikálódjon.

> [!NOTE]
> Ebben az oktatóanyagban az alapértelmezett beállításokat, és minimális testreszabási legegyszerűbb elérési útvonalát tartalmazza. Összetettebb esetekhez használhatja a "Hogyan To" a cikkek az Azure virtuális gépek.

## <a name="prerequisites"></a>Előfeltételek

- Mindenképp végezzen [vészhelyreállítási próbát](azure-to-azure-tutorial-dr-drill.md) annak ellenőrzésére, hogy minden a várt módon működik-e.
- Ellenőrizze a virtuális gép tulajdonságait a feladatátvételi teszt futtatása előtt. A virtuális gépnek meg kell felelnie az [Azure-követelményeknek](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Feladatátvétel futtatása a másodlagos régióba

1. A **Replikált elemek** területen válassza ki azt a virtuális gépet, amelyen feladatátvételt szeretne végezni > **Feladatátvétel**

   ![Feladatátvétel](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:

   * **Legújabb** (alapértelmezett): A Site Recovery szolgáltatásban lévő összes adatot dolgoz fel, és a legalacsonyabb helyreállítási időkorlátot (RPO) biztosít.
   * **Legutóbb feldolgozott**: A Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot a virtuális gép feladatátvételét.
   * **Egyéni**: Átadja a feladatokat egy adott helyreállítási pontra. Ez a lehetőség feladatátvételi teszt végrehajtásához hasznos.

3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a Site Recovery megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A Site Recovery nem üríti a forrás a feladatátvételt követően.

4. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

5. A feladatátvétel után a virtuális gépre való bejelentkezéssel ellenőrizze a virtuális gépet. Ha a virtuális gép egy másik helyreállítási pontjára szeretne ugrani, akkor a **Helyreállítási pont módosítása** lehetőséget használhatja.

6. Ha elégedett a feladatátviteli virtuális géppel, **véglegesítheti** a feladatátvételt.
   A véglegesítés törli a szolgáltatással elérhető összes helyreállítási pontot. Most már nem tudja majd módosítani, a helyreállítási pont.

## <a name="reprotect-the-secondary-vm"></a>A másodlagos virtuális gép ismételt védelme

A virtuális gép feladatátvétele után ismét meg kell védenie azt, hogy az visszareplikálódjon az elsődleges régióba.

1. Győződjön meg arról, hogy a virtuális gép a **Feladatátvétel véglegesítve** állapotban van, és ellenőrizze, hogy az elsődleges régió elérhető-e, és létre tud-e hozni új erőforrásokat az elsődleges régióban, valamint el tudja-e érni azokat.
2. A **Tároló** > **Replikált elemek** területen kattintson a jobb gombbal arra a virtuális gépre, amelyen feladatátvételt végzett, és válassza az **Ismételt védelem** parancsot.

   ![Kattintson a jobb gombbal az ismételt védelemhez](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Győződjön meg arról, hogy az elsődleges régióra, másodlagos védelem iránya már van kiválasztva.
3. Tekintse át az **Erőforráscsoport, a Hálózat, a Tárolás és a Rendelkezésre állási csoportok** adatait. Bármely megjelölt új erőforrások jönnek létre az ismételt védelmi művelet részeként.
4. Kattintson az **OK** gombra az ismételt védelmi feladat elindításához. Ez a feladat feltölti a célhelyet a legújabb adatokkal. Ezután replikálja az eltéréseket az elsődleges régióba. A virtuális gép most védett állapotban van.

## <a name="next-steps"></a>További lépések
- Ismételt védelme után [megtudhatja, hogyan](azure-to-azure-tutorial-failback.md) feladat-visszavételhez az elsődleges régióban elérhetővé válik.
- [További](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) az ismételt védelem flowról.
