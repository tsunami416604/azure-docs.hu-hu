---
title: Helyszíni gépek áttelepítése az Azure Site Recovery szolgáltatással
description: Ez a cikk bemutatja, hogyan migrálhatók a helyszíni gépek az Azure-ba az Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b978190776aee3c89d3beadde76d20c4327b012f
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388916"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Helyszíni gépek áttelepítése az Azure-ba


Ez a cikk ismerteti, hogyan telepítheti át a helyszíni gépeket az Azure-ba az [Azure Site Recovery](site-recovery-overview.md)használatával. 

> [!TIP]
> Most már használja az Azure Migrate-t a helyszíni gépek azure-ba való áttelepítéséhez az Azure Site Recovery szolgáltatás helyett. [További információ](../migrate/migrate-services-overview.md).


Az oktatóanyag bemutatja, hogyan migrálhatja a helyszíni virtuális gépeket és a fizikai kiszolgálókat az Azure-ba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az áttelepítés forrás- és célkörnyezetének beállítása
> * Replikációs szabályzat beállítása
> * A replikáció engedélyezése
> * A várnak megfelelő működés ellenőrzése egy áttelepítési teszt futtatásával
> * Azure-ba irányuló egyszeri feladatátvételi teszt futtatása


> [!TIP]
> Most már áttelepítheti a helyszíni kiszolgálókat az Azure-ba az Azure Migrate szolgáltatás használatával. [További információ](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Előkészületek

A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.


## <a name="prepare-azure-and-on-premises"></a>Az Azure és a helyszíni létesítmények előkészítése

1. Készítse elő az Azure-t [a cikkben](tutorial-prepare-azure.md)leírtak szerint. Bár ez a cikk a vész-helyreállítás helyreállítási lépések előkészítése, a lépések is érvényesek az áttelepítés.
2. Helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md)- vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-kiszolgálók előkészítése. Ha fizikai gépeket telepít át, nem kell semmit sem készítenie. Csak ellenőrizze a [támogatási mátrix](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és hová.
1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az Erőforrás menüben kattintson a **Helyhelyreállítása** >  > **infrastruktúra-védelem előkészítése parancsra.****Prepare Infrastructure**
3. A **Védelmi cél** ablakban válassza ki, hogy mit szeretne migrálni.
    - **VMware**: Válassza az **Azure-ba** > **Igen, a következővel: VMWare vSphere hipervizorral** lehetőséget.
    - **Fizikai gép**: Válassza az **Azure-ba** > **Nem virtualizált/egyéb** lehetőséget.
    - **Hyper-V**: Válassz az **Azure-ba** > **Igen, a következővel: Hyper-V** lehetőséget. Ha a Hyper-V virtuális gépeket egy VMM kezeli, válassza az **Igen** lehetőséget.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | Állítsa be a [forráskörnyezetet](vmware-azure-set-up-source.md), és állítsa be a [konfigurációs kiszolgálót](vmware-azure-deploy-configuration-server.md).
Fizikai gép | [Állítsa be](physical-azure-set-up-source.md) a forráskörnyezetet és a konfigurációs kiszolgálót.
Hyper-V | A [forráskörnyezet](hyper-v-azure-tutorial.md#set-up-the-source-environment) beállítása<br/><br/> Állítsa be a [Forráskörnyezet](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) hyper-V telepített System Center VMM.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson **az Infrastruktúra-cél** > **Target**előkészítése gombra, és válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a Resource Manager-alapú üzemi modell beállítást.
3. A Site Recovery ellenőrzi az Azure-erőforrásokat.
    - Ha virtuális gépvirtuális gépeit vagy fizikai kiszolgálóit telepíti át, a Site Recovery ellenőrzi, hogy rendelkezik-e olyan Azure-hálózattal, amelyben az Azure virtuális gépek a feladatátvétel után létrejönnek.
    - Ha hyper-v virtuális gépeket telepít át, a Site Recovery ellenőrzi, hogy rendelkezik-e kompatibilis Azure-tárfiókkal és -hálózattal.
4. Ha a System Center VMM által kezelt Hyper-V virtuális gépeket telepíti át, állítsa be a [hálózati leképezést.](hyper-v-vmm-azure-tutorial.md#configure-network-mapping)

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | Hozzon létre egy [replikációs házirendet](vmware-azure-set-up-replication.md) a VMware virtuális gépekhez.
Fizikai gép | Állítson be [replikációs házirendet](physical-azure-disaster-recovery.md#create-a-replication-policy) a fizikai gépekhez.
Hyper-V | [Replikációs házirend](hyper-v-azure-tutorial.md#set-up-a-replication-policy) beállítása<br/><br/> A System Center VMM-mel telepített Hyper-V [replikációs házirendjének](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) beállítása.

## <a name="enable-replication"></a>A replikáció engedélyezése

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | A VMware virtuális gépek [replikációjának engedélyezése.](vmware-azure-enable-replication.md)
Fizikai gép | [Engedélyezze](physical-azure-disaster-recovery.md#enable-replication) a replikációt a fizikai gépeken.
Hyper-V | [Replikáció engedélyezése](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Engedélyezze](hyper-v-vmm-azure-tutorial.md#enable-replication) a replikációt a System Center VMM rendszerrel telepített Hyper-V számára.


## <a name="run-a-test-migration"></a>Migrálási teszt futtatása

A [rest failover](tutorial-dr-drill-azure.md) parancs Azure-ban történő futtatásával győződjön meg arról, hogy minden a vártnak megfelelően működik-e.


## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Futtasson egy feladatátvételt a migrálni kívánt gépen.

1. A**Replikált elemek** **beállításai** > párbeszédpanelen kattintson a **feladatátvétel**> gépre.
2. A **Feladatátvétel** csoportban válasszon egy **helyreállítási pontot,** amelynek feladatátvételre van. Válassza a legutóbbi helyreállítási pontot.
3. Ehhez a forgatókönyvhöz nem kell figyelembe venni a titkosítási kulcs beállítását.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery a feladatátvitel indítása előtt megkísérli leállítani a virtuális gépeket. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a Feladatok lapon **követheti.**
5. Ellenőrizze, hogy az Azure-beli virtuális gép a várt módon jelenik-e meg az Azure-ban.
6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Migrálás befejezése** parancsra. Ez a következőket teszi:

   - Befejezi az áttelepítési folyamatot, leállítja a helyszíni virtuális gép replikációját, és leállítja a virtuális gép Site Recovery számlázását.
   - Ez a lépés törli a replikációs adatokat. Nem törli az áttelepített virtuális gépeket.

     ![Az áttelepítés befejezése](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Előfordulhat, hogy a fizikai kiszolgálók, a VMware Linux-gépek, a DHCP-szolgáltatással nem rendelkező VMware virtuális gépek és a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépek hosszabb tesztelési idővel rendelkeznek: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Áttelepítés után

Miután befejeződött a gépek áttelepítése az Azure-ba, még meg kell tennie néhány lépést.

Egyes lépések automatikusan is végrehajthatók az áttelepítési folyamat részeként a [helyreállítási tervek](site-recovery-runbook-automation.md) beépített automatizálási szkript funkciójával   


### <a name="post-migration-steps-in-azure"></a>Áttelepítést követő lépések az Azure-ban

- Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása). 
- Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
- Az [Azure virtuálisgép-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) kezeli a virtuális gépek kommunikációját az Azure-hálóvezérlővel. Erre egyes Azure-szolgáltatások, például az Azure Backup, a Site Recovery és az Azure Security esetében van szükség.
    - VMware-alapú gépek és fizikai kiszolgálók áttelepítése esetében a mobilitási szolgáltatástelepítő telepíti az elérhető Azure virtuálisgép-ügynököt a Windows-rendszerű gépekre. Linux-rendszerű virtuális gépek esetében azt javasoljuk, hogy feladatátvétel után telepítse az ügynököt.
    - Ha az Azure virtuális gépek egy másodlagos régióba, az Azure virtuálisgép-ügynök ki kell építeni a virtuális gép az áttelepítés előtt.
    - Ha a Hyper-V virtuális gépek et az Azure-ba, telepítse az Azure virtuálisgép-ügynök az Azure virtuális gép az áttelepítés után.
- Manuálisan távolítson el minden Site Recovery-szolgáltatót/ügynököt a virtuális gépről. VMware virtuális gépek vagy fizikai kiszolgálók áttelepítése esetén távolítsa el a Mobilitás szolgáltatást a virtuális gépről.
- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](azure-to-azure-quickstart.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a beérkező forgalom hozzáférését az Azure Security Center [igény szerinti felügyelet]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) funkciójával
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/ ) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.

### <a name="post-migration-steps-on-premises"></a>Áttelepítést követő helyszíni lépések

- Helyezze át az alkalmazás-forgalmat az áttelepített Azure virtuálisgép-példányon futó alkalmazásra.
- Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
- Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
- Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével.




## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban helyszíni virtuális gépeket migrált Azure-beli virtuális gépekbe. Now

> [!div class="nextstepaction"]
> Állítsa be a [vész-helyreállítási](azure-to-azure-replicate-after-migration.md) egy másodlagos Azure-régióban az Azure virtuális gépek.

  
