---
title: Helyszíni gépek migrálása az Azure-ba az Azure Site Recoveryvel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan migrálhatók a helyszíni gépek az Azure-ba az Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fc15db91b8f4cc6dbdecd0e7321abdbf81744f08
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357980"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Helyszíni gépek áttelepítése az Azure-ba


Ez a cikk ismerteti a helyszíni gépek áttelepítése az Azure-ba, használja a [Azure Site Recovery](site-recovery-overview.md). A Site Recovery általánosan, kezeléséhez és a helyszíni gépek és Azure virtuális gépek vészhelyreállításának szolgál. Azonban azt is használható az áttelepítéshez. Áttelepítési ugyanazokat a lépéseket használja, mint a vész-helyreállítási, egy kivétellel. Az áttelepítés az utolsó lépés gépek feladatátvétele a helyszíni helyről. Vész-helyreállítási eltérően nem visszaadja a feladatokat a helyszíni áttelepítési forgatókönyvekben.


Az oktatóanyag bemutatja, hogyan migrálhatja a helyszíni virtuális gépeket és a fizikai kiszolgálókat az Azure-ba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Állítsa be a forrás és cél környezetet az áttelepítésre
> * Replikációs szabályzat beállítása
> * A replikáció engedélyezése
> * A várnak megfelelő működés ellenőrzése egy áttelepítési teszt futtatásával
> * Azure-ba irányuló egyszeri feladatátvételi teszt futtatása


> [!TIP]
> Az Azure Migrate szolgáltatás most kipróbálhatják egy új, ügynök nélküli kivételfigyelés élményt áttelepítése VMware virtuális gépekhez az Azure-bA. [További](https://aka.ms/migrateVMs-signup).


## <a name="before-you-start"></a>Előkészületek

Vegye figyelembe, hogy a paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.


## <a name="prepare-azure-and-on-premises"></a>Az Azure és helyszíni előkészítése

1. Az Azure előkészítése leírtak szerint [Ez a cikk](tutorial-prepare-azure.md). Bár ez a cikk ismerteti a vész-helyreállítási előkészítő lépések, a lépések is érvényesek az áttelepítéshez.
2. Helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md)- vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-kiszolgálók előkészítése. Ha a fizikai gépek migráláshoz, nem kell előkészületeket végeznie. Csak ellenőrizze a [támogatási mátrix](vmware-physical-azure-support-matrix.md).


## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és hová.
1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az Erőforrás menüben kattintson a **Site Recovery** > **Az infrastruktúra előkészítése** > **Védelmi cél** lehetőségre.
3. A **Védelmi cél** ablakban válassza ki, hogy mit szeretne migrálni.
    - **VMware**: Válassza ki **az Azure-bA** > **Igen, a VMWare vSphere Hipervizorral**.
    - **Fizikai gép**: Válassza ki **az Azure-bA** > **nem virtualizált/egyéb**.
    - **Hyper-V**: Válassza ki **az Azure-bA** > **Igen, a Hyper-V-vel**. Ha a Hyper-V virtuális gépeket egy VMM kezeli, válassza az **Igen** lehetőséget.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | Állítsa be a [forráskörnyezet](vmware-azure-set-up-source.md), és állítsa be a [konfigurációs kiszolgáló](vmware-azure-deploy-configuration-server.md).
Fizikai gép | [Állítsa be a](physical-azure-set-up-source.md) a forrás-környezet és a konfigurációs kiszolgálón.
Hyper-V | Állítsa be a [forráskörnyezet](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Állítsa be a [forráskörnyezet](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) a System Center VMM-mel telepített Hyper-V.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a Resource Manager-alapú üzemi modell beállítást.
3. A Site Recovery az Azure-erőforrások ellenőrzi.
    - Ha VMware virtuális gépek vagy fizikai kiszolgálók migráláshoz, a Site Recovery, amelyben az Azure virtuális gépek kerülnek a feladatátvételt követően felálló Azure-hálózat rendelkezik ellenőrzi.
    - Hyper-V virtuális gépek migráláshoz, ha a Site Recovery egy kompatibilis Azure storage-fiók és a hálózati ellenőrzi.
4. Ha a System Center VMM által felügyelt Hyper-V virtuális gépek migráláshoz, állítsa be a [hálózatleképezés](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | Állítsa be a [replikációs házirend](vmware-azure-set-up-replication.md) VMware virtuális gépekhez.
Fizikai gép | Állítsa be a [replikációs házirend](physical-azure-disaster-recovery.md#create-a-replication-policy) fizikai gépek számára.
Hyper-V | Állítsa be a [replikációs házirendhez](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Állítsa be a [replikációs házirend](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) a System Center VMM-mel telepített Hyper-V.

## <a name="enable-replication"></a>A replikáció engedélyezése

**Forgatókönyv** | **Részletek**
--- | --- 
VMware | [Engedélyezze a replikációt](vmware-azure-enable-replication.md) a VMware virtuális gépek számára.
Fizikai gép | [Engedélyezze a replikációt](physical-azure-disaster-recovery.md#enable-replication) fizikai gépek számára.
Hyper-V | [A replikáció engedélyezése](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Engedélyezze a replikációt](hyper-v-vmm-azure-tutorial.md#enable-replication) a System Center VMM-mel telepített Hyper-V.


## <a name="run-a-test-migration"></a>Migrálási teszt futtatása

A [rest failover](tutorial-dr-drill-azure.md) parancs Azure-ban történő futtatásával győződjön meg arról, hogy minden a vártnak megfelelően működik-e.


## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Futtasson egy feladatátvételt a migrálni kívánt gépen.

1. A **Beállítások** > **Replikált elemek** területen kattintson a gépre > **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Válassza a legutóbbi helyreállítási pontot.
3. Ehhez a forgatókönyvhöz nem kell figyelembe venni a titkosítási kulcs beállítását.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery a feladatátvitel indítása előtt megkísérli leállítani a virtuális gépeket. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
5. Ellenőrizze, hogy az Azure-beli virtuális gép a várt módon jelenik-e meg az Azure-ban.
6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Migrálás befejezése** parancsra. Ez a következőket teszi:

   - Befejezi a migrálási folyamatot, a helyszíni virtuális gép replikálását, és leállítja a virtuális gép Site Recovery-számlázását.
   - Ezzel a lépéssel törli azokat a replikációs adatokat. Azzal nem törli az áttelepített virtuális gépeket.

     ![Az áttelepítés befejezése](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: Virtuális gép replikációja leáll a feladatátvétel indítása előtt. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Bizonyára észrevette, hogy hosszabb a feladatátvételi teszt ideje a fizikai kiszolgálókon, a VMware Linux gépeken, a nem DHCP-vel kiosztott IP-című VMware virtuális gépeken, valamint a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Áttelepítés után

Miután befejeződött a gépek áttelepítése az Azure-ba, még meg kell tennie néhány lépést.

Egyes lépések automatikusan is végrehajthatók az áttelepítési folyamat részeként a [helyreállítási tervek](site-recovery-runbook-automation.md) beépített automatizálási szkript funkciójával   


### <a name="post-migration-steps-in-azure"></a>Áttelepítést követő lépések az Azure-ban

- Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása). 
- Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
- Az [Azure virtuálisgép-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) kezeli a virtuális gépek kommunikációját az Azure-hálóvezérlővel. Erre egyes Azure-szolgáltatások, például az Azure Backup, a Site Recovery és az Azure Security esetében van szükség.
    - VMware-alapú gépek és fizikai kiszolgálók áttelepítése esetében a mobilitási szolgáltatástelepítő telepíti az elérhető Azure virtuálisgép-ügynököt a Windows-rendszerű gépekre. Linux-rendszerű virtuális gépek esetében azt javasoljuk, hogy feladatátvétel után telepítse az ügynököt.
    - Azure virtuális gépek másodlagos régióba való áttelepítésekor még az áttelepítés előtt ki kell építeni az Azure virtuálisgép-ügynököt a virtuális gépen.
    - Hyper-V-alapú virtuális gépek Azure-ba való áttelepítése esetén az Azure virtuálisgép-ügynököt az áttelepítés után telepítse az Azure virtuális gépen.
- Manuálisan távolítson el minden Site Recovery-szolgáltatót/ügynököt a virtuális gépről. Ha VMware virtuális gépek vagy fizikai kiszolgálók áttelepítése, távolítsa el a mobilitási szolgáltatás a virtuális gépről.
- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információk]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információk](azure-to-azure-quickstart.md).
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

Ebben az oktatóanyagban helyszíni virtuális gépeket migrált Azure-beli virtuális gépekbe. Most

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása](azure-to-azure-replicate-after-migration.md) egy másodlagos Azure-régióba az Azure-beli virtuális gépek.

  
