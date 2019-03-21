---
title: A replikáció konfigurálása a közvetlen tárolóhelyek (S2d) virtuális gépek az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk ismerteti a konfigurálása az S2D-t, hogy egy Azure-régióból a másikba a Site Recovery használatával a virtuális gépek replikációját.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 6c639d4503b170660abed5767e3571c8a2bf24b9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112772"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>Az Azure virtuális gépek replikálása egy másik Azure-régióba közvetlen tárolás használatával

Ez a cikk bemutatja, hogyan vészhelyreállítása az Azure virtuális gépeken futó közvetlen tárolóhelyek engedélyezése.

>[!NOTE]
>Csak összeomlás-konzisztens helyreállítási pontok storage spaces közvetlen fürtök esetében támogatottak.
>

## <a name="introduction"></a>Bevezetés 
[A közvetlen tárolóhelyek (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) van egy szoftveresen definiált tárolást, amely lehetővé teszi a létrehozása [vendégfürtök](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) az Azure-ban.  Egy vendégfürt Microsoft Azure-ban egy feladatátvevő fürt IaaS virtuális gépek áll. Lehetővé teszi a üzemeltetett virtuális gépek számítási feladatait átadja a feladatokat jövedelmezőbb munkát tesznek lehetővé a magasabb rendelkezésre állási SLA alkalmazásokhoz, mint egy Azure virtuális Gépen is biztosít a vendégfürtök között. Ez hasznos forgatókönyvekben, ahol egy kritikus fontosságú alkalmazást üzemeltető VM, például az SQL vagy a méretezési csoport kibővíthető fájlkiszolgálók stb.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Katasztrófa utáni helyreállítás az Azure Virtual Machines használatával a közvetlen tárolóhelyek
Egy tipikus forgatókönyv szükség lehet a virtuális gépek vendégfürt az Azure-ban az alkalmazás, például a méretezési csoport a kibővíthető fájlkiszolgálók nagyobb rugalmasság. Ez az alkalmazás magasabb rendelkezésre állást biztosíthat, miközben, ezeket az alkalmazásokat bármelyik régióban szolgáltatói hiba a Site Recovery használatával védeni szeretné. A Site Recovery egy adott régióban található, egy másik Azure-régióba replikálja az adatokat, és a fürt egy feladatátvételi esemény a vészhelyreállítási régióban lévő kimenetei.

Alábbi diagram bemutatja a képi formában jeleníti meg két tároló használata Azure virtuális gépek feladatátvevő fürtre a közvetlen tárolóhelyek szolgáltatással.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Windows feladatátvevő fürt két Azure-beli virtuális gépek és virtuális gépenként van két vagy több adatlemezt.
- S2D szinkronizálja az adatokat lemezen lévő adatokat, és megadja a szinkronizált tároló-tárolókészletként.
- A tárolókészlet mutat be, mint a feladatátvevő fürt fürt megosztott kötete (CSV).
- A feladatátvevő fürt az adatmeghajtók a CSV-t használja.

**Vészhelyreállítási szempontok**

1. Amikor állítja be az [felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) a fürt folyamatosan tanúsító vész-helyreállítási régióban.
2. Ha a feladatátvételt a virtuális gépek a Vészhelyreállítási régióban, amely eltér a forrásrégióban lévő alhálózathoz fog majd IP-címet kell lehet módosítani a feladatátvételt követően.  IP-címét kell használnia az ASR a fürt módosítása [helyreállítási terv szkriptet.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Mintaparancsfájl](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) végrehajtja a parancsot használja az egyéni szkriptek futtatására szolgáló bővítmény virtuális gépen 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>A Site Recovery engedélyezi az S2D-fürt:

1. Belül a recovery services-tároló, kattintson a "+ replikálása"
1. Választhatja ki a fürt összes csomópontját, és azokat az része egy [több virtuális gépre kiterjedő konzisztencia csoport](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Válassza ki a replikációs házirend az alkalmazáskonzisztencia ki * (csak az összeomlási konzisztencia támogatás érhető el)
1. A replikáció engedélyezése

   ![storagespacesdirect védelme](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Nyissa meg a replikált elemek, és láthatja, hogy a virtuális gép állapotát. 
3. A virtuális gépek első védettek, és több virtuális gépre kiterjedő konzisztencia csoport részeként is látható.

   ![storagespacesdirect védelme](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv támogatja az alkalmazás-előkészítés különböző rétegek egy többrétegű alkalmazást, a feladatátvétel alatt. Alkalmazás-előkészítés segít fenntartani a konzisztenciát alkalmazás. Amikor létrehoz egy helyreállítási terv többszintű webalkalmazások, teljes körű lépéseket ismertetett [helyreállítási terv létrehozása a Site Recovery használatával](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok felvétele

1.  A helyreállítási terv létrehozásához adja meg a virtuális gépeket.
2.  Kattintson a "Testreszabás" gombra a virtuális gépek csoportosítása. Alapértelmezés szerint minden virtuális gép a csoport "% 1" részét képezik.


### <a name="add-scripts-to-the-recovery-plan"></a>A helyreállítási tervbe szkriptek hozzáadása
Az alkalmazások megfelelő működéséhez szükség lehet néhány művelet az Azure-beli virtuális gépeken ehhez a feladatátvétel után, vagy tesztcélú feladatátvétel alatt. Egyes feladatátvétel utáni műveletek automatizálható. Például Itt azt terheléselosztó csatolni, és módosítása a fürt IP-címe.


### <a name="failover-of-the-virtual-machines"></a>A virtuális gépek feladatátvétele 
A használatával feladatátvételt kell mind a csomópontok a virtuális gépek a [ASR-helyreállítási terv](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![storagespacesdirect védelme](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
1.  Az Azure Portalon válassza ki a helyreállítási tárban.
2.  Válassza ki a létrehozott helyreállítási tervet.
3.  Kattintson a **Feladatátvétel tesztelése** elemre.
4.  A teszt feladatátvételi folyamat indításához válassza ki a helyreállítási pont és az Azure virtuális hálózat.
5.  A másodlagos környezet esetén hajtsa végre az ellenőrzések.
6.  Ha ellenőrzés befejeződött, tisztítsa meg a feladatátvételi környezet válassza **feladatátvételi teszt utáni karbantartás**.

További információkért lásd: [az Azure-bA a Site Recovery feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1.  Az Azure Portalon válassza ki a helyreállítási tárban.
2.  Válassza ki a helyreállítási tervet, amelyet létrehozott SAP-alkalmazások.
3.  Válassza a **Feladatátvétel** lehetőséget.
4.  A feladatátvételi folyamat indításához válassza ki a helyreállítási pontot.

További információkért lásd: [feladatátvétel a Site Recoveryben](site-recovery-failover.md).
## <a name="next-steps"></a>További lépések

[További](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) feladat-visszavétel futtatása.
