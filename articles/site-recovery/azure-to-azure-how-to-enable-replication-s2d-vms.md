---
title: Közvetlen tárterülettel rendelkező Azure-beli virtuális gépek replikálása az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan replikálhatja a közvetlen tárterülettel működő Azure-beli virtuális gépeket az Azure Site Recovery használatával.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 9f394fa8d618c97d74a47ff6e42a002f177cf7d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973664"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>A tárolóhelyeket közvetlenül egy másik régióba futtató Azure-virtuális gépek replikálása

Ez a cikk ismerteti, hogyan engedélyezheti a közvetlen tárolóhelyeket futtató Azure-beli virtuális gépek vészutáni helyreállítását.

>[!NOTE]
>Csak összeomlás konzisztens helyreállítási pontok támogatottak a közvetlen fürtök tárolóhelyeken.
>

[Közvetlen tárolóhelyek (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) a szoftver által definiált tároló, amely lehetővé teszi, hogy [vendégfürtök az](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) Azure-ban.  A Microsoft Azure vendégfürtje iaaS virtuális gépekből álló feladatátvevő fürt. Lehetővé teszi a üzemeltetett virtuálisgép-számítási feladatok feladatátvételt a vendégfürtök között, így az alkalmazások számára magasabb rendelkezésre állású SLA érhető el, mint amit egyetlen Azure virtuális gép biztosíthat. Ez akkor hasznos, ha a virtuális gép kritikus alkalmazást, például SQL-t vagy kibővített fájlkiszolgálót üzemeltet.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Katasztrófa utáni helyreállítás közvetlen tárolóhelyekkel

Egy tipikus forgatókönyv előfordulhat, hogy a virtuális gépek vendégfürt az Azure-ban az alkalmazás nagyobb rugalmasságát, például a kibővített fájlkiszolgáló. Bár ez magasabb rendelkezésre állást biztosít az alkalmazás számára, szeretné megvédeni ezeket az alkalmazásokat a Site Recovery használatával bármely régiószintű hiba esetén. A Site Recovery replikálja az adatokat az egyik régióból egy másik Azure-régióba, és feladatátvétel esetén hozza létre a fürtet a vész-helyreállítási régióban.

Az alábbi ábrán egy kétcsomópontos Azure Virtuálisgép feladatátvételi fürt közvetlen tárolóhelyek használatával látható.

![tárolóterekközvetlenül](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Két Azure virtuális gép egy Windows feladatátvevő fürtben, és minden virtuális gép két vagy több adatlemezzel rendelkezik.
- Az S2D szinkronizálja az adatlemezen lévő adatokat, és a szinkronizált tárolókészletet tárolókészletként jelenik meg.
- A tárolókészlet fürt megosztott kötetként (CSV) jelenik meg a feladatátvevő fürtszámára.
- A feladatátvevő fürt a CSV-t használja az adatmeghajtókhoz.

**Vész-helyreállítási szempontok**

1. Amikor [a fürt felhőtanúja](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) beállításakor a vész-helyreállítási régióban őrizheti meg a tanúsító állapotot.
2. Ha a vész-erőforrás-régió alhálózatának a virtuális gépek et szeretné átvenni, amely eltér a forrásrégiótól, akkor a fürt IP-címét a feladatátvétel után módosítani kell.  A fürt IP-címének módosításához a Site Recovery helyreállítási terv parancsfájlját kell [használnia.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Mintaparancsfájl](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) a virtuális gépen belüli parancs futtatásához egyéni parancsfájlkiterjesztéssel 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Hely-helyreállítás engedélyezése S2D fürthöz:

1. A helyreállítási szolgáltatások tárolójában kattintson a "+replicate" gombra
1. Jelölje ki a fürt összes csomópontját, és tegye őket egy [többvirtuális gép konzisztenciacsoportjának részévé](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Válassza ki a replikációs házirendet alkalmazáskonzisztenciával kikapcsolva* (csak összeomlás-konzisztencia-támogatás érhető el)
1. A replikáció engedélyezése

   ![storagespacesdirect védelem](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Lépjen a replikált elemekre, és láthatja a virtuális gép állapotát is.
3. Mindkét virtuális gépek egyre védett, és a több virtuális gép konzisztenciacsoport részeként is megjelennek.

   ![storagespacesdirect védelem](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv támogatja a különböző szintek szekvenálása egy többrétegű alkalmazás feladatátvétel során. A szekvenálás segít fenntartani az alkalmazások konzisztenciáját. Amikor helyreállítási tervet hoz létre egy többrétegű webalkalmazáshoz, hajtsa végre a Helyreállítási terv létrehozása a Site Recovery használatával című részben [ismertetett](site-recovery-create-recovery-plans.md)lépéseket.

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása feladatátvételi csoportokhoz

1.  Hozzon létre egy helyreállítási tervet a virtuális gépek hozzáadásával.
2.  A virtuális gépek csoportosításához kattintson a "Testreszabás" gombra. Alapértelmezés szerint minden virtuális gép az "1.


### <a name="add-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez
Ahhoz, hogy az alkalmazások megfelelően működjenek, előfordulhat, hogy bizonyos műveleteket kell végeznie az Azure virtuális gépeken a feladatátvétel után vagy egy teszt feladatátvétel során. A feladatátvételt követő egyes műveletek automatizálhatók. Itt például terheléselosztót csatlakoztatunk, és megváltoztatjuk a fürt IP-címét.


### <a name="failover-of-the-virtual-machines"></a>A virtuális gépek feladatátvétele 
A virtuális gépek mindkét csomópontjának át kell vennie a feladatátvételt a hely-helyreállítási [helyreállítási terv](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) használatával 

![storagespacesdirect védelem](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
1.  Az Azure Portalon válassza ki a Recovery Services-tároló.
2.  Válassza ki a létrehozott helyreállítási tervet.
3.  Kattintson a **Feladatátvétel tesztelése** elemre.
4.  A teszt feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot és az Azure virtuális hálózatot.
5.  Ha a másodlagos környezet ben, végezze el az ellenőrzéseket.
6.  Ha az ellenőrzések befejeződtek, a feladatátvételi környezet tisztításához válassza a **Karbantartás teszt feladatátvétellehetőséget.**

További információ: [Feladatátvétel tesztelése az Azure-ban a Site Recovery.for](site-recovery-test-failover-to-azure.md)more information, see Test failover to Azure in Site Recovery .

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1.  Az Azure Portalon válassza ki a Recovery Services-tároló.
2.  Válassza ki az SAP-alkalmazásokhoz létrehozott helyreállítási tervet.
3.  Válassza a **Feladatátvétel** lehetőséget.
4.  A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információ: [Feladatátvétel a Site Recovery alkalmazásban.](site-recovery-failover.md)
## <a name="next-steps"></a>További lépések

[További információ](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) a feladat-visszavétel ek futtatásáról.
