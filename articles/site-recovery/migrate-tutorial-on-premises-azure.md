---
title: Helyszíni gépek migrálása az Azure-ba az Azure Site Recoveryvel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan migrálhatók a helyszíni gépek az Azure-ba az Azure Site Recovery használatával.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1df09a885d6c636ff6bd4bcbec03d27ff7b44ff9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836984"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Helyszíni gépek áttelepítése az Azure-ba

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás helyszíni gépek és Azure-beli virtuális gépek vészhelyreállításának az üzleti folytonosság biztosítása és vészhelyreállítás (BCDR) céljából való kezelésére és vezénylésére használható, valamint a helyi gépek az Azure-ba történő áttelepítésének kezelésére.


Az oktatóanyag bemutatja, hogyan migrálhatja a helyszíni virtuális gépeket és a fizikai kiszolgálókat az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Replikációs cél kiválasztása
> * Forrás- és célkörnyezet beállítása
> * Replikációs szabályzat beállítása
> * A replikáció engedélyezése
> * A várnak megfelelő működés ellenőrzése egy áttelepítési teszt futtatásával
> * Azure-ba irányuló egyszeri feladatátvételi teszt futtatása

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. Helyszíni [VMware](vmware-azure-tutorial-prepare-on-premises.md)- vagy [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-kiszolgálók előkészítése.

Mielőtt elkezdené, érdemes áttekinteni a [VMware](vmware-azure-architecture.md) vagy a [Hyper-V](hyper-v-azure-architecture.md) architektúrát az esetleges vészhelyreállítás megkönnyítése érdekében.


## <a name="prerequisites"></a>Előfeltételek

A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson az **Erőforrás létrehozása** > **Figyelés + felügyelet** > **Backup és Site Recovery** lehetőségre.
3. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton**, majd a **Létrehozás** gombra.

   ![Új tároló](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

Az új tároló megjelenik az **Irányítópult** **Minden erőforrás** részében, illetve a központi **Recovery Services-tárolók** oldalon.



## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és hová.
1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az Erőforrás menüben kattintson a **Site Recovery** > **Az infrastruktúra előkészítése** > **Védelmi cél** lehetőségre.
3. A **Védelmi cél** ablakban válassza ki, hogy mit szeretne migrálni.
    - **VMware**: Válassza az **Azure-ba** > **Igen, a következővel: VMWare vSphere hipervizorral** lehetőséget.
    - **Fizikai gép**: Válassza az **Azure-ba** > **Nem virtualizált/egyéb** lehetőséget.
    - **Hyper-V**: Válassz az **Azure-ba** > **Igen, a következővel: Hyper-V** lehetőséget. Ha a Hyper-V virtuális gépeket egy VMM kezeli, válassza az **Igen** lehetőséget.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

- [Állítsa be](vmware-azure-tutorial.md#set-up-the-source-environment) a forráskörnyezetet a VMware virtuális gépekhez.
- [Állítsa be](physical-azure-disaster-recovery.md#set-up-the-source-environment) a forráskörnyezetet a fizikai kiszolgálókhoz.
- [Állítsa be](hyper-v-azure-tutorial.md#set-up-the-source-environment) a forráskörnyezetet a Hyper-V virtuális gépekhez.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a Resource Manager-alapú üzemi modell beállítást.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

- [Állítsa be a replikációs szabályzatot](vmware-azure-tutorial.md#create-a-replication-policy) a VMware virtuális gépek számára.
- [Állítsa be a replikációs szabályzatot](physical-azure-disaster-recovery.md#create-a-replication-policy) a fizikai kiszolgálók számára.
- [Állítsa be a replikációs szabályzatot](hyper-v-azure-tutorial.md#set-up-a-replication-policy) a Hyper-V virtuális gépek számára.


## <a name="enable-replication"></a>A replikáció engedélyezése

- [Engedélyezze a replikációt](vmware-azure-tutorial.md#enable-replication) a VMware virtuális gépek számára.
- [Engedélyezze a replikációt](physical-azure-disaster-recovery.md#enable-replication) a fizikai kiszolgálók számára.
- Engedélyezze a Hyper-V virtuális gépek replikációját [VMM-mel](hyper-v-vmm-azure-tutorial.md#enable-replication) vagy [VMM nélkül](hyper-v-azure-tutorial.md#enable-replication).


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

    - Befejezi a migrálási folyamatot, az AWS virtuális gép replikálását, és leállítja a virtuális gép Site Recovery-számlázását.
    - Ezzel a lépéssel törli azokat a replikációs adatokat. Azzal nem törli az áttelepített virtuális gépeket.

    ![Az áttelepítés befejezése](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Bizonyára észrevette, hogy hosszabb a feladatátvételi teszt ideje a fizikai kiszolgálókon, a VMware Linux gépeken, a nem DHCP-vel kiosztott IP-című VMware virtuális gépeken, valamint a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Áttelepítés után

Miután befejeződött a gépek áttelepítése az Azure-ba, még meg kell tennie néhány lépést.

Egyes lépések automatikusan is végrehajthatók az áttelepítési folyamat részeként a [helyreállítási tervek]( https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation) beépített automatizálási szkript funkciójával   


### <a name="post-migration-steps-in-azure"></a>Áttelepítést követő lépések az Azure-ban

- Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása). 
- Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
- Az [Azure virtuálisgép-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) kezeli a virtuális gépek kommunikációját az Azure-hálóvezérlővel. Erre egyes Azure-szolgáltatások, például az Azure Backup, a Site Recovery és az Azure Security esetében van szükség.
    - VMware-alapú gépek és fizikai kiszolgálók áttelepítése esetében a mobilitási szolgáltatástelepítő telepíti az elérhető Azure virtuálisgép-ügynököt a Windows-rendszerű gépekre. Linux-rendszerű virtuális gépek esetében azt javasoljuk, hogy feladatátvétel után telepítse az ügynököt. a
    - Azure virtuális gépek másodlagos régióba való áttelepítésekor még az áttelepítés előtt ki kell építeni az Azure virtuálisgép-ügynököt a virtuális gépen.
    - Hyper-V-alapú virtuális gépek Azure-ba való áttelepítése esetén az Azure virtuálisgép-ügynököt az áttelepítés után telepítse az Azure virtuális gépen.
- Manuálisan távolítson el minden Site Recovery-szolgáltatót/ügynököt a virtuális gépről. VMware-alapú virtuális gépek vagy fizikai kiszolgálók áttelepítése esetén [távolítsa el a Mobilitási szolgáltatást][vmware-azure-install-mobility-service.md#uninstall-mobility-service-on-a-windows-server-computer] az Azure virtuális gépről.
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

Ebben az oktatóanyagban helyszíni virtuális gépeket migrált Azure-beli virtuális gépekbe. Most már készen áll az Azure virtuális gépek másodlagos régióba történő [vészhelyreállításának beállítására](azure-to-azure-replicate-after-migration.md).

  
