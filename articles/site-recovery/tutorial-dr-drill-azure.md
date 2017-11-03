---
title: "A vész-helyreállítási részletezéshez helyszíni gépek futtassa az Azure-bA az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "További tudnivalók a helyszíni vész-helyreállítási részletezési futtatja az Azure-ba, az Azure Site Recovery szolgáltatással"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ddd17921-68f4-41c7-ba4c-b767d36f1733
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 15e4487217ec21bb33380422640cb19dfcbcee39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Vészhelyreállítási próba végrehajtása az Azure-ba

Az oktatóanyag bemutatja, hogyan futtathat egy vész-helyreállítási részletezéshez helyszíni gépek az Azure-ba, használatával a feladatátvételi tesztet. A részletezés azt ellenőrzi, hogy a replikációs stratégiájának adatvesztés nélkül. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A teszt feladatátvételhez elkülönített hálózat beállítása
> * Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően
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
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve a hálózatot/alhálózatot, amelyben az Azure virtuális Gépen található után feladatátvétel és a hozzá rendelt IP-cím.
5. A **lemezek**, az operációs rendszer és az adatlemezek információkat tekintheti meg a virtuális Gépen.

## <a name="run-a-test-failover-for-a-single-vm"></a>Egy virtuális a feladatátvételi teszt futtatása

Feladatátvételi teszt futtatásakor a következő történik:

1. Egy Előfeltételek ellenőrzése során annak biztosítását, hogy a feladatátvétel szükséges feltételek teljesülnek.
2. Feladatátvételi dolgozza fel az adatokat, a, hogy az Azure virtuális gép hozhatók létre. Ha jelölje be a legutóbbi helyreállítási pontot, a helyreállítási pont az adataiból jön létre.
3. Egy Azure virtuális gép jön létre az előző lépésben feldolgozott adatokat használ.

Feladatátvételi teszt futtatása az alábbiak szerint:

1. A **beállítások** > **replikált elemek**, kattintson a virtuális gép > **+ feladatátvételi teszt**.

2. Válasszon ki egy helyreállítási pontot a a feladatátvételre használni:
    - **Legújabb feldolgozott** : a virtuális gép átadja a feladatokat a Site Recovery által feldolgozott legutóbbi helyreállítási pontot. Az időbélyeg jelenik meg. Ezzel a beállítással nem van feldolgozásával töltött idő adatokat, így egy alacsony RTO (helyreállítási idő célkitűzése) biztosít.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat az összes virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak. Az időbélyeg jelenik meg.
    - **Egyéni**: válassza ki a helyreállítási pontot.
3. A **feladatátvételi teszt**, jelölje ki a cél Azure hálózati mely Azure virtuális gépek csatlakoznak feladatátvételt követően.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Folyamatban van a virtuális gép tulajdonságainak megnyitásához kattintson követheti nyomon. Vagy kattintson a **feladatátvételi teszt** feladatot a tároló neve > **beállítások** > **feladatok** >
   **Site Recovery-feladatok**.
5. A feladatátvétel befejezése után a replika Azure virtuális gép megjelenik az Azure portálon > **virtuális gépek**. Ellenőrizze, hogy a virtuális gép a megfelelő méretűek, hogy csatlakozik-e a megfelelő hálózati-e és fut-e.
6. Most kell kapcsolódnia kell az Azure-ban a replikált virtuális gép.
7. A feladatátvételi teszt során létrehozott Azure virtuális gépek törléséhez kattintson **karbantartása a feladatátvételi teszt** a a helyreállítási terv. A **megjegyzések**, és a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez mentéséhez.

Bizonyos esetekben feladatátvételi igényel, amely körülbelül nyolc-tíz perc végezze el a további feldolgozást. Bizonyára észrevette, hogy már a feladatátvételi idő VMware Linux-gépek, a VMware virtuális gépek, amelyek nem rendelkeznek a DHCP szolgáltatás lehetővé teszi, hogy és a VMware virtuális gépek, amelyek nem rendelkeznek a következő rendszerindító illesztőprogramok tesztelése: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Helyszíni VMware virtuális gépek esetén a feladatátvétel és a feladat-visszavétel futtatása](tutorial-vmware-to-azure-failover-failback.md).
