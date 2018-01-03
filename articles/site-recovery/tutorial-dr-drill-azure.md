---
title: "A vész-helyreállítási részletezéshez helyszíni gépek futtassa az Azure-bA az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "További tudnivalók a helyszíni vész-helyreállítási részletezési futtatja az Azure-ba, az Azure Site Recovery szolgáltatással"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: f7dc5e2df95a64685a8b70d25e839c371d4fc2de
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Vészhelyreállítási próba végrehajtása az Azure-ba

Az oktatóanyag bemutatja, hogyan futtathat egy vész-helyreállítási részletezéshez egy a helyi gép az Azure-ba, használja a feladatátvételi tesztet. A részletezés azt ellenőrzi, hogy a replikációs stratégiájának adatvesztés nélkül. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A teszt feladatátvételhez elkülönített hálózat beállítása
> * A feladatátvételt követően csatlakozni az Azure virtuális gép előkészítése
> * Egyetlen gép feladatátvételi teszt futtatása

Ez az a negyedik oktatóanyag egy sorozat. Ez az oktatóanyag feltételezi, hogy már végrehajtotta a feladatokat az előző oktatóanyagok a.

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. [Helyszíni VMware előkészítése](tutorial-prepare-on-premises-vmware.md)
3. [Vészhelyreállítás beállítása](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>Ellenőrizze a virtuális gép tulajdonságai

Feladatátvételi teszt futtatása előtt ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel-e az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. A **védett elemek**, kattintson a **replikált elemek** > virtuális gép.
2. Az a **replikált elemek** ablaktáblában nincs Virtuálisgép-adatok, állapotát, összegzését, és a legújabb elérhető helyreállítási pontok. Kattintson a **tulajdonságok** megtekintheti annak további részleteit.
3. A **számítás és hálózat**, módosíthatja a Azure nevét, az erőforráscsoport, a célméretet, [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md), és a felügyelt lemezbeállításokat.
   
      >[!NOTE]
      A feladat-visszavétel a helyszíni Hyper-V gépeket Azure virtuális gépek felügyelt lemezzel jelenleg nem támogatott. Csak akkor ajánlott a felügyelt lemezek beállítás feladatátvételi Ha szeretne áttelepíteni a helyszíni virtuális gépek Azure-ba, nélkül vissza sikertelenek lesznek.
   
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve a hálózatot/alhálózatot, amelyben az Azure virtuális Gépen található után feladatátvétel és a hozzá rendelt IP-cím.
5. A **lemezek**, az operációs rendszer és az adatlemezek információkat tekintheti meg a virtuális Gépen.

## <a name="run-a-test-failover-for-a-single-vm"></a>Egy virtuális a feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő történik:

1. Egy Előfeltételek ellenőrzése során annak biztosítását, hogy a feladatátvétel szükséges feltételek teljesülnek.
2. Feladatátvételi dolgozza fel az adatokat, a, hogy az Azure virtuális gép hozhatók létre. Ha jelölje be a legutóbbi helyreállítási pontot, a helyreállítási pont az adataiból jön létre.
3. Egy Azure virtuális gép jön létre az előző lépésben feldolgozott adatokat használ.

Feladatátvételi teszt futtatása az alábbiak szerint:

1. A **beállítások** > **replikált elemek**, kattintson a virtuális gép > **+ feladatátvételi teszt**.
2. Válassza ki a **legújabb feldolgozott** helyreállítási pont ebben az oktatóanyagban. Ez nem sikerül a virtuális gép a legutóbbi pontnak keresztül időben. Az időbélyeg jelenik meg. Ezzel a beállítással nem van feldolgozásával töltött idő adatokat, így egy alacsony RTO (helyreállítási idő célkitűzése) biztosít.
3. A **feladatátvételi teszt**, jelölje ki a cél Azure hálózati mely Azure virtuális gépek csatlakoznak feladatátvételt követően.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Folyamatban van a virtuális gép tulajdonságainak megnyitásához kattintson követheti nyomon. Vagy kattintson a **feladatátvételi teszt** feladatot a tároló neve > **beállítások** > **feladatok** >
   **Site Recovery-feladatok**.
5. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure portálon > **virtuális gépek**. Ellenőrizze, hogy a virtuális gép a megfelelő méretűek, hogy csatlakozik-e a megfelelő hálózati-e és fut-e.
6. Most kell kapcsolódnia kell az Azure-ban a replikált virtuális gép.
7. A feladatátvételi teszt során létrehozott Azure virtuális gépek törléséhez kattintson **karbantartása a feladatátvételi teszt** a virtuális Gépen. A **megjegyzések**, és a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez mentéséhez.

Bizonyos esetekben feladatátvételi igényel, amely körülbelül nyolc-tíz perc végezze el a további feldolgozást. Bizonyára észrevette, hogy már a feladatátvételi idő VMware Linux-gépek, a VMware virtuális gépek, amelyek nem rendelkeznek a DHCP szolgáltatás lehetővé teszi, hogy és a VMware virtuális gépek, amelyek nem rendelkeznek a következő rendszerindító illesztőprogramok tesztelése: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyszíni VMware virtuális gépek esetén a feladatátvétel és a feladat-visszavétel futtatása](tutorial-vmware-to-azure-failover-failback.md).
