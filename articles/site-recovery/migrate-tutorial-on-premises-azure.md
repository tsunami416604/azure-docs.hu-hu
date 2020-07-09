---
title: Helyszíni gépek migrálása Azure Site Recovery
description: Ez a cikk bemutatja, hogyan migrálhatók a helyszíni gépek az Azure-ba az Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ccf83bacedb667e52e9865b6d451641faa0ac414
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131186"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Helyszíni gépek áttelepítése az Azure-ba


Ez a cikk bemutatja, hogyan telepítheti át a helyszíni gépeket az Azure-ba a [Azure site Recovery](site-recovery-overview.md)használatával. 

> [!TIP]
> A Azure Site Recovery szolgáltatás helyett a Azure Migrate használatával kell áttelepítenie a helyszíni gépeket az Azure-ba. [További információk](../migrate/migrate-services-overview.md).


Az oktatóanyag bemutatja, hogyan migrálhatja a helyszíni virtuális gépeket és a fizikai kiszolgálókat az Azure-ba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A forrás-és a célként megadott környezet beállítása az áttelepítéshez
> * Replikációs szabályzat beállítása
> * A replikáció engedélyezése
> * A várnak megfelelő működés ellenőrzése egy áttelepítési teszt futtatásával
> * Azure-ba irányuló egyszeri feladatátvételi teszt futtatása


> [!TIP]
> Most már áttelepítheti a helyszíni kiszolgálókat az Azure-ba a Azure Migrate szolgáltatás használatával. [További információ](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Előkészületek

A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.


## <a name="prepare-azure-and-on-premises"></a>Az Azure és a helyszíni előkészítése

1. Készítse elő az Azure-t a [jelen cikkben](tutorial-prepare-azure.md)leírtak szerint. Bár ez a cikk a vész-helyreállítás előkészítési lépéseit ismerteti, a lépések a Migrálás esetében is érvényesek.
2. Helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md)- vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-kiszolgálók előkészítése. Ha fizikai gépeket telepít át, nem kell előkészítenie semmit. Csak ellenőrizze a [támogatási mátrixot](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és hová.
1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az erőforrás menüben kattintson site Recovery az **Site Recovery**  >  **infrastruktúra előkészítése**  >  **védelmi cél**elemre.
3. A **Védelmi cél** ablakban válassza ki, hogy mit szeretne migrálni.
    - **VMware**: Válassza az **Azure-ba** > **Igen, a következővel: VMWare vSphere hipervizorral** lehetőséget.
    - **Fizikai gép**: Válassza az **Azure-ba** > **Nem virtualizált/egyéb** lehetőséget.
    - **Hyper-V**: Válassz az **Azure-ba** > **Igen, a következővel: Hyper-V** lehetőséget. Ha a Hyper-V virtuális gépeket egy VMM kezeli, válassza az **Igen** lehetőséget.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | Állítsa be a [forrás-környezetet](vmware-azure-set-up-source.md), és állítsa be a [konfigurációs kiszolgálót](vmware-azure-deploy-configuration-server.md).
Fizikai gép | [Állítsa be](physical-azure-set-up-source.md) a forrás-és a konfigurációs kiszolgálót.
Hyper-V | A [forrás környezet](hyper-v-azure-tutorial.md#set-up-the-source-environment) beállítása<br/><br/> Állítsa be a System Center VMM üzembe helyezett Hyper-V- [forrás környezetét](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) .

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **infrastruktúra előkészítése**  >  **cél**elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a Resource Manager-alapú üzemi modell beállítást.
3. Site Recovery ellenőrzi az Azure-erőforrásokat.
    - Ha VMware virtuális gépeket vagy fizikai kiszolgálókat telepít át, Site Recovery ellenőrzi, hogy van-e olyan Azure-hálózat, amelyben az Azure-beli virtuális gépek a feladatátvétel után jönnek létre.
    - Ha a Hyper-V virtuális gépek áttelepítését végzi, Site Recovery ellenőrzi, hogy rendelkezik-e kompatibilis Azure Storage-fiókkal és-hálózattal.
4. Ha a System Center VMM által felügyelt Hyper-V virtuális gépeket telepíti át, állítsa be a [hálózati leképezést](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | Hozzon létre egy [replikációs házirendet](vmware-azure-set-up-replication.md) a VMWare virtuális gépekhez.
Fizikai gép | Állítsa be a [replikációs szabályzatot](physical-azure-disaster-recovery.md#create-a-replication-policy) a fizikai gépekhez.
Hyper-V | [Replikációs házirend](hyper-v-azure-tutorial.md#set-up-a-replication-policy) beállítása<br/><br/> Hozzon létre egy [replikációs házirendet](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) a System Center VMM üzembe helyezett Hyper-V-hez.

## <a name="enable-replication"></a>A replikáció engedélyezése

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | [Engedélyezze a replikációt](vmware-azure-enable-replication.md) a VMWare virtuális gépek számára.
Fizikai gép | A fizikai gépek [replikálásának engedélyezése](physical-azure-disaster-recovery.md#enable-replication) .
Hyper-V | [A replikáció engedélyezése](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> A System Center VMM üzembe helyezett Hyper-V [replikálásának engedélyezése](hyper-v-vmm-azure-tutorial.md#enable-replication) .


## <a name="run-a-test-migration"></a>Migrálási teszt futtatása

A [rest failover](tutorial-dr-drill-azure.md) parancs Azure-ban történő futtatásával győződjön meg arról, hogy minden a vártnak megfelelően működik-e.


## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Futtasson egy feladatátvételt a migrálni kívánt gépen.

1. A **Beállítások**  >  **replikált elemek** elemnél kattintson a gép > **feladatátvétel**elemre.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Válassza a legutóbbi helyreállítási pontot.
3. Ehhez a forgatókönyvhöz nem kell figyelembe venni a titkosítási kulcs beállítását.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery a feladatátvitel indítása előtt megkísérli leállítani a virtuális gépeket. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamat a **feladatok** lapon követhető.
5. Ellenőrizze, hogy az Azure-beli virtuális gép a várt módon jelenik-e meg az Azure-ban.
6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Migrálás befejezése** parancsra. Ez a következő műveleteket végzi el:

   - Befejezi az áttelepítési folyamatot, leállítja a helyszíni virtuális gép replikálását, és leállítja Site Recovery a virtuális gép számlázását.
   - Ezzel a lépéssel megtisztítja a replikációs adatvédelmet. Nem törli az áttelepített virtuális gépeket.

     ![Az áttelepítés befejezése](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Észreveheti, hogy a feladatátvételi idő a fizikai kiszolgálók, a VMware Linux-gépek, a VMware virtuális gépek, amelyeken nincs engedélyezve a DHCP-szolgáltatás, valamint a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépek: storvsc, VMBus, storflt, Intelide, ATAPI.

## <a name="after-migration"></a>Áttelepítés után

Miután befejeződött a gépek áttelepítése az Azure-ba, még meg kell tennie néhány lépést.

Egyes lépések automatikusan is végrehajthatók az áttelepítési folyamat részeként a [helyreállítási tervek](site-recovery-runbook-automation.md) beépített automatizálási szkript funkciójával   


### <a name="post-migration-steps-in-azure"></a>Áttelepítést követő lépések az Azure-ban

- Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása). 
- Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
- Az [Azure virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) kezeli a virtuális gépek kommunikációját az Azure-hálóvezérlővel. Erre egyes Azure-szolgáltatások, például az Azure Backup, a Site Recovery és az Azure Security esetében van szükség.
    - VMware-alapú gépek és fizikai kiszolgálók áttelepítése esetében a mobilitási szolgáltatástelepítő telepíti az elérhető Azure virtuálisgép-ügynököt a Windows-rendszerű gépekre. Linux-rendszerű virtuális gépek esetében azt javasoljuk, hogy feladatátvétel után telepítse az ügynököt.
    - Ha Azure-beli virtuális gépeket telepít át egy másodlagos régióba, az áttelepítés előtt az Azure virtuálisgép-ügynököt a virtuális gépen kell kiépíteni.
    - Ha a Hyper-V virtuális gépeket az Azure-ba telepíti át, az áttelepítés után telepítse az Azure virtuálisgép-ügynököt az Azure-beli virtuális gépre.
- Manuálisan távolítson el minden Site Recovery-szolgáltatót/ügynököt a virtuális gépről. Ha VMware virtuális gépeket vagy fizikai kiszolgálókat telepít át, távolítsa el a mobilitási szolgáltatást a virtuális gépről.
- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információk](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információk](azure-to-azure-quickstart.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a beérkező forgalom hozzáférését az Azure Security Center [igény szerinti felügyelet](../security-center/security-center-just-in-time.md) funkciójával
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/security-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/ ) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.

### <a name="post-migration-steps-on-premises"></a>Áttelepítést követő helyszíni lépések

- Helyezze át az alkalmazás-forgalmat az áttelepített Azure virtuálisgép-példányon futó alkalmazásra.
- Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
- Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
- Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével.




## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban helyszíni virtuális gépeket migrált Azure-beli virtuális gépekbe. Now

> [!div class="nextstepaction"]
> [Állítsa be](azure-to-azure-replicate-after-migration.md) a vész-helyreállítást egy másodlagos Azure-régióba az Azure-beli virtuális gépek számára.

  
