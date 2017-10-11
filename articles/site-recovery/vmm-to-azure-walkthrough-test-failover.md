---
title: "A Hyper-V replikáció (a System Center VMM-mel) feladatátvételi teszt futtatása az Azure-bA |} Microsoft Docs"
description: "A feladatátvételi teszt futtatásához a Hyper-V virtuális gépek VMM-felhőkben replikálása Azure-bA az Azure Site Recovery szolgáltatással lépéseket foglalja össze."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b562a23-7ba7-48ee-905d-c22b4b5d6466
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: 4688fc4bc74a9e0e04487cfbe965006070fd9a7b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="step-11-run-a-test-failover-for-hyper-v-replication-with-vmm-to-azure"></a>11. lépés: A Hyper-V replikáció (a VMM-mel) feladatátvételi teszt futtatása az Azure-bA

Miután [engedélyezze a Hyper-V virtuális gépek replikációját](vmm-to-azure-walkthrough-enable-replication.md), ez a cikk feladatátvételi teszt futtatása a helyszíni Hyper-V virtuális gépek Azure-ba, a System Center Virtual Machine Manager (VMM) felhőkben felügyelt használata használatával a [Azure A helyreállítási hely](site-recovery-overview.md) szolgáltatás az Azure portálon.

Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Előkészületek

Javasoljuk, hogy ellenőrizze a virtuális gép tulajdonságait, és hajtsa végre a módosításokat feladatátvételi teszt futtatása előtt kell. Érheti el a virtuális gép tulajdonságok **replikált elemek**. A **Essentials** panel gépek beállítások és állapotával kapcsolatos adatokat jeleníti meg.

## <a name="managed-disk-considerations"></a>Felügyelt lemezzel kapcsolatos megfontolások

[Által kezelt lemezeken](../virtual-machines/windows/managed-disks-overview.md) egyszerűbbé Lemezkezelés Azure virtuális gépek, a virtuális gépek lemezei társított storage-fiókok kezelése. 

- Felügyelt lemezek létrehozni és csatolni a virtuális gép csak akkor, ha a feladatátvételt az Azure-bA. Ha engedélyezi a védelmet, storage-fiókok replikálja az adatokat a helyszíni virtuális gépek.
- Felügyelt lemezek csak a Resource manager üzembe helyezési modellben rel központilag telepített virtuális gépek hozható létre.
- A feladat-visszavétel az Azure-ból a helyszíni Hyper-V környezet jelenleg nem támogatott gépek felügyelt lemezzel. Csak akkor kell beállítania **az kezelt lemezek** való **Igen** Ha végzett áttelepítés csak (feladatátvételt az Azure feladat-visszavétel nélkül)
- Ez a beállítás engedélyezése esetén csak a rendelkezésre állási beállítja tartalmazó erőforráscsoportokat **az kezelt lemezek** engedélyezve választható ki. Felügyelt lemezzel rendelkező virtuális gépek rendelkezésre állási készletek kell **az kezelt lemezek** beállítása **Igen**. Ha a beállítás nincs engedélyezve a virtuális gépeket, majd csak lévő rendelkezésre állási csoportokra erőforráscsoportok nélkül engedélyezve felügyelt lemezek választható ki. [További információk](../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).
- - Ha a replikáláshoz használni kívánt tárfiókot a Storage szolgáltatás titkosítási titkosított, kezelt lemezek nem hozható létre, a feladatátvétel során. Ebben az esetben vagy nem felügyelt lemezek használatának engedélyezése vagy tiltsa le a virtuális gép védelmét, és engedélyezi, hogy a storage-fiók, amely nem rendelkezik engedélyezhető a titkosítás használata. [További információk](../virtual-machines/windows/managed-disks-overview.md#managed-disks-and-encryption).

 
## <a name="network-considerations"></a>Hálózati kapcsolatos szempontok
    
- Beállíthatja, hogy a feladatátvételt követően az Azure virtuális Géphez használandó cél IP-címet. Ha nem ad meg címet, a gép, amelynek a feladatait átadja, a DHCP-t fogja használni. Ha olyan címet ad meg, amely nem használható feladatátadásra, a feladatátvételi művelet sikertelen lesz. A cél IP-címe feladatátvételi tesztre is használható, amennyiben a cím elérhető a feladatátvételi teszt hálózatában.
- A hálózati adapterek számát a cél virtuális gépek mérete határozza meg, a következők szerint:
    - Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
    - Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot fogja használni.
    - Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a cél csupán egy adaptert támogat, akkor a célgépen is csak egy adapter fog működni.     
- Ha a virtuális gépen több hálózati adapter működik, azok mindegyike ugyanahhoz a hálózathoz fog csatlakozni.
- Ha a virtuális gépnek több hálózati adaptert, majd az elsőt a listán látható lesz a *alapértelmezett* az Azure virtuális gép hálózati adapteréhez.


## <a name="view-and-manage-vm-settings"></a>Megtekintheti és kezelheti a virtuális gép beállításait

Azt javasoljuk, hogy ellenőrizze a gép tulajdonságai között a forrás, a feladatátvétel futtatása előtt.

1. A **védett elemek**, kattintson a **replikált elemek**, és kattintson a virtuális Gépet.

    ![A replikáció engedélyezése](./media/vmm-to-azure-walkthrough-test-failover/test-failover1.png)
2. Az a **replikált elemek** ablaktábláján egy virtuális gép állapotát, és a legújabb elérhető helyreállítási pontok összegzése látható. Kattintson a **tulajdonságok** megtekintheti annak további részleteit.

    ![A replikáció engedélyezése](./media/vmm-to-azure-walkthrough-test-failover/test-failover2.png)
3. A **számítás és hálózat**, akkor is:
    - Módosítsa az Azure virtuális gép nevét. A nevet meg kell felelnie [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Adja meg a feladatátvételt követően [erőforráscsoport].
    - Adja meg a célméretet az Azure virtuális Géphez
    - Válasszon egy [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md).
    - Adja meg, hogy használja-e [által kezelt lemezeken](#managed-disk-considerations). Válassza ki **Igen**, ha azt szeretné, hogy a géphez az áttelepítési Azure felügyelt lemezek csatolni.
    - Megtekintése vagy módosítása a hálózati beállításokat, beleértve a hálózatot/alhálózatot, amelyben az Azure virtuális Gépen található után feladatátvétel és a hozzá rendelt IP-cím.

    ![A replikáció engedélyezése](./media/vmm-to-azure-walkthrough-test-failover/test-failover4.png)
4. A **lemezek**, az operációs rendszer és az adatlemezek információkat tekintheti meg a virtuális Gépen.


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Most győződjön meg arról, hogy a feladatátvételi teszt futtatása minden a várt módon működik.

- Ha csatlakozni az Azure virtuális gépeken, feladatátvételt követően RDP segítségével szeretne [csatlakozás előkészítéséhez](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - A teljes teszteléshez szüksége lesz az Active Directory és a DNS egy másolatára a tesztkörnyezetében. [További információk](site-recovery-active-directory.md#test-failover-considerations).
 - Teljes információ a feladatátvételi teszt [Ez a cikk](site-recovery-test-failover-to-azure.md) cikk.
 
 Most feladatátvételt:

1. Az egyetlen számítógépen, feladatátvételt **replikált elemek**, kattintson a virtuális gép > **+ feladatátvételi teszt** ikonra.
2. Helyreállítási terv feladatátadásához a **Helyreállítási tervek** menüben kattintson a jobb gombbal a kívánt tervre, majd válassza a **Feladatátvételi teszt** lehetőséget. Helyreállítási terv létrehozásához [kövesse ezeket az utasításokat](site-recovery-create-recovery-plans.md).
3. A **feladatátvételi teszt**, válassza ki a feladatátvételt követően csatlakoznak az Azure virtuális gépek mely Azure-beli hálózatot.
4. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Folyamatban van a virtuális gép tulajdonságainak megnyitásához, vagy a kattintva követheti nyomon a **feladatátvételi teszt** feladatot a tároló neve > **feladatok** > **Site Recovery-feladatok**.
5. A feladatátvétel befejezését követően a replika Azure-gépnek meg kell jelennie az Azure Portal > **Virtuális gépek** részében. Ellenőrizze, hogy a virtuális gép mérete megfelelő-e, hogy a gép a megfelelő hálózathoz csatlakozik-e, illetve, hogy fut-e.
6. Ha elvégezte a feladatátvételt követő csatlakozáshoz szükséges előkészületeket, most tudnia kellene csatlakozni az Azure-beli virtuális géphez.
7. Befejezése után kattintson a **karbantartása a feladatátvételi teszt** a a helyreállítási terv. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket. Ezzel törli a feladatátvételi teszt során létrehozott virtuális gépeket.



## <a name="next-steps"></a>Következő lépések

- [További](site-recovery-failover.md) kapcsolatos különféle a feladatátvételeket, és futtatásukhoz.
- [További információ a feladat-visszavétel](site-recovery-failback-from-azure-to-hyper-v.md), a feladat-visszavételt és az Azure virtuális gépek replikálása vissza az elsődleges helyszíni VMM-felhőben.

