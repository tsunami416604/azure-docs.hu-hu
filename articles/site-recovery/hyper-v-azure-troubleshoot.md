---
title: Hyper-V hibaelhárítása az Azure Site recoveryvel Azure-beli vészhelyreállításához |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure-bA az Azure Site Recovery használata Hyper-V vész helyreállítási hibáinak elhárítása
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 2f9c4c0b973efe26e6ece2235f2d0c7a6878ebef
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844991"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Hyper-V hibaelhárítása az Azure-replikáció és feladatátvétel

Ez a cikk ismerteti a gyakori kérdéseket, előfordulhat, hogy között replikálja a helyszíni Hyper-V virtuális gépek Azure-ba, amikor használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Védelmi hibák engedélyezése

Ha a Hyper-V virtuális gépek védelmének engedélyezésekor problémákat tapasztal, ellenőrizze az alábbi javaslatokat:

1. Ellenőrizze, hogy a Hyper-V-gazdagépek és virtuális gépek megfelelnek az összes [követelmények és előfeltételek](hyper-v-azure-support-matrix.md).
2. Ha a System Center Virtual Machine Manager (VMM) felhőkben található Hyper-V-kiszolgálókon, győződjön meg arról, hogy sikeresen előkészítette a [VMM-kiszolgáló](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Ellenőrizze, hogy a Hyper-V Virtuálisgép-kezelő szolgáltatás fut-e a Hyper-V-gazdagépek.
4. Ellenőrizze a problémákra, amelyek a Hyper-V-VMMS\Admin jelentkezzen be a virtuális gép jelennek meg. Ez a napló található **alkalmazások és szolgáltatásnaplók** > **Microsoft** > **Windows**.
5. A Vendég virtuális Gépen ellenőrizze, hogy a WMI engedélyezve van, és elérhető-e.
  - [Ismerje meg](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) Alapszintű ellenőrzés WMI.
  - [Hibaelhárítás](https://aka.ms/WMiTshooting) WMI.
  - [Hibaelhárítás ](https://technet.microsoft.com/library/ff406382.aspx#H22) WMI-parancsfájlok és a szolgáltatásokkal kapcsolatos problémák.
6. A Vendég virtuális Gépen győződjön meg arról, hogy fut-e a legújabb integrációs szolgáltatásokat.
    - [Ellenőrizze](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) , hogy rendelkezik-e a legújabb verzióra.
    - [Tartsa](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) integrációs szolgáltatások naprakész.
    
## <a name="replication-issues"></a>Replikációjával kapcsolatos problémák

Kezdeti és folyamatos replikálási hibáinak elhárítása a következőképpen:

1. Ellenőrizze, hogy futtatja a [legújabb verzió](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) a Site Recovery Services.
2. Győződjön meg arról, hogy a replikáció szünetel:
  - Ellenőrizze a virtuális gép működési állapota a Hyper-V Manager konzolon.
  - Ha fontos, kattintson a jobb gombbal a virtuális gép > **replikációs** > **replikálás állapotának megtekintése**.
  - Ha a replikáció szünetel, kattintson a **replikálás folytatása**.
3. Ellenőrizze, hogy a szükséges szolgáltatások futnak-e. Ha nem, indítsa újra őket.
    - Ha Hyper-V VMM nélkül replikál, ellenőrizze, hogy ezek a szolgáltatások futnak-e a Hyper-V-gazdagépen:
        - Virtuálisgép-kezelő szolgáltatás
        - A Microsoft Azure Recovery Services Agent szolgáltatás
        - A Microsoft Azure Site Recovery szolgáltatással
        - A WMI szolgáltató gazdaszolgáltatása
    - Ha a környezetben a VMM-mel replikál, ellenőrizze, hogy ezek a szolgáltatások futnak-e:
        - A Hyper-V gazdagépen ellenőrizze, hogy a Virtuálisgép-kezelő szolgáltatás, a Microsoft Azure Recovery Services Agent és a WMI szolgáltató szolgáltatás fut-e.
        - A VMM-kiszolgálón győződjön meg arról, hogy a System Center Virtual Machine Manager szolgáltatás fut-e.
4. Ellenőrizze a Hyper-V kiszolgáló és az Azure közötti kapcsolatot. Ellenőrizze a kapcsolatot, nyissa meg a Feladatkezelőt a Hyper-V-gazdagépen. Az a **teljesítmény** lapra, majd **nyissa meg az erőforrás-figyelő**. Az a **hálózati** lap > **hálózati tevékenységgel rendelkező folyamat**, ellenőrizze, hogy cbengine.exe aktívan küld nagy adatmennyiségek (MB).
5. Ellenőrizze, hogy ha a Hyper-V-gazdagépek csatlakozhat az Azure storage blob URL-címe. Ellenőrizze, hogy a gazdagépek is csatlakozni, válassza ki és ellenőrizze, hogy **cbengine.exe**. Nézet **TCP-kapcsolatok** , ellenőrizze a kapcsolatot a gazdagép és az Azure storage-blobba.
6. Az alább ismertetett, ellenőrizze a teljesítménnyel kapcsolatos problémák.
    
### <a name="performance-issues"></a>Teljesítménnyel kapcsolatos problémák

Hálózati sávszélesség-korlátozások hatással lehet a replikáció. Hibaelhárítás a következő:

1. [Ellenőrizze](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) sávszélesség vagy a környezetben korlátozások szabályozás esetén nem.
2. Futtassa a [Deployment Planner a profiler](hyper-v-deployment-planner-run.md).
3. A Profilkészítő futtatása, után hajtsa végre a [sávszélesség](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) és [tárolási](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) javaslatok.
4. Ellenőrizze [adatváltozása korlátozások](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Ha magas data churn egy virtuális gépen, tegye a következőket:
  - Ellenőrizze, hogy ha a virtuális Gépet újraszinkronizálásra van megjelölve.
  - Hajtsa végre a [ezeket a lépéseket](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) , a forgalom forrását.
  - Forgalom akkor fordulhat elő, amikor HRL naplófájlok meghaladja az 50 %-a szabad lemezterület. Ha a probléma, üzembe helyezése több tárhely az összes virtuális gép, amelyen a probléma akkor fordul elő.
  - Ellenőrizze, hogy a replikáció nincs leállítva. Ha igen, továbbra is a módosítások írása a hrl fájlt, ami növelheti a nagyobb méretű.
 

## <a name="critical-replication-state-issues"></a>Kritikus fontosságú replikáció állapotával kapcsolatos problémák megoldásáról

1. Replikálás állapotának ellenőrzéséhez csatlakozhat a helyszíni Hyper-V Manager konzolt, válassza ki a virtuális Gépet és állapotának ellenőrzéséhez.

    ![Replikáció állapota](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Kattintson a **replikálás állapotának megtekintése** a részletek megtekintéséhez:

    - Ha a replikáció szünetel, kattintson a jobb gombbal a virtuális gép > **replikációs** > **replikálás folytatása**.
    - Ha egy virtuális Gépet egy Hyper-V gazdagépen konfigurálni a Site Recoveryben ugyanazon fürt egy másik Hyper-V-gazdagépre, vagy egy különálló gépre telepíti át, a virtuális gép nem negatív. Csak ellenőrizze, hogy az új Hyper-V-gazdagép teljesíti az összes előfeltételt, és a Site Recoveryben van konfigurálva.

## <a name="app-consistent-snapshot-issues"></a>Alkalmazáskonzisztens pillanatkép kapcsolatos problémák

Az alkalmazáskonzisztens pillanatkép a virtuális Gépen található alkalmazásadatok időponthoz pillanatképet. Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális Gépet az alkalmazások konzisztens állapotban a pillanatkép készítésének időpontjában.  Ez a szakasz néhány gyakori probléma, előfordulhat, hogy részletesen.

### <a name="vss-failing-inside-the-vm"></a>Sikertelen volt a virtuális gép VSS

1. Ellenőrizze, hogy a legújabb integrációs szolgáltatások telepítve és fut-e.  Ellenőrizze, hogy ha egy frissítés érhető el a következő parancs futtatásával egy rendszergazda jogú PowerShell-parancssorba a Hyper-V-gazdagépen: **get-vm |} válassza a neve, állam, IntegrationServicesState**.
2. Ellenőrizze, hogy VSS-szolgáltatás fut, és megfelelő állapotú:
    - A szolgáltatások ellenőrzéséhez jelentkezzen be a Vendég virtuális Gépen. Ezután nyisson meg egy rendszergazdai parancssort, és ellenőrizze, hogy kifogástalan állapotban-e a VSS-író a következő parancsokat.
        - **Vssadmin list writers**
        - **Vssadmin list shadows**
        - **Vssadmin list-szolgáltatók**
    - A kimenet ellenőrzése. Ha az író hibás állapotban van, tegye a következőket:
        - Ellenőrizze a virtuális gépen a VSS-művelet hibákat az alkalmazások eseménynaplójában.
    - Próbálja ki ezeket a sikertelen író társított szolgáltatások újraindítása:
        - Kötet árnyékmásolata
         - Az Azure Site Recovery VSS Provider
    - Ezt követően, várjon néhány óra múlva megtekintheti, ha alkalmazáskonzisztens pillanatképek létrehozása sikerült.
    - Végső megoldásként próbálja ki, a virtuális gép újraindítása. Ez előfordulhat, hogy oldja meg a szolgáltatások, amelyek nem válaszoló állapotban van.
3. Ellenőrizze, hogy a virtuális gépen nem kell a dinamikus lemezeket. Ez az alkalmazáskonzisztens pillanatképek nem támogatott. Ellenőrizheti, a Lemezkezelés (diskmgmt.msc).

    ![Dinamikus lemez](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Ellenőrizze, hogy nem rendelkezik a virtuális Géphez csatolt iSCSI-lemez. Ez a funkció nem támogatott.
5. Ellenőrizze, hogy a Backup szolgáltatás engedélyezve van. Ellenőrizze, hogy van-e engedélyezve a **Hyper-V beállítások** > **integrációs szolgáltatások**.
6. Ellenőrizze, hogy nem lesznek ütközések VSS-pillanatképeket alkalmazásokkal. Ha több alkalmazás próbálja veszik a VSS-pillanatképet az azonos idő ütközés fordulhat elő. Például ha egy biztonsági mentési alkalmazás VSS-pillanatképet tart a Site Recovery által a replikációs szabályzat ütemezése pillanatképének elkészítéséhez.   
7. Ellenőrizze, hogy ha a virtuális gép egy nagy forgalom sebessége tapasztalja:
    - Napi adatváltozási sebesség mérhetik a Vendég virtuális gépek, Hyper-v rendszerű gazdagépen a teljesítményszámlálók segítségével. Az adatmódosítási arány mértékeket, engedélyezze a következő számlálót. Ezt az értéket 5-15 perc, a virtuális gép lemezeinek mintát Aggregrate beolvasni a virtuális gép forgalommal.
        - Kategória: "a Hyper-V virtuális tárolóeszköz"
        - A számláló: "írási bájt / mp"</br>
        - Ezen adatok lemorzsolódási rátához növeli vagy továbbra is magas szinten, attól függően, hogyan foglalt a virtuális gép vagy az alkalmazások vannak.
        - Az átlagos lemez adatváltozása 2 MB/s a Site Recovery standard szintű tárolóra vonatkozó. [További információ](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Emellett akkor is [ellenőrzése a storage skálázhatósági célértékét](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#scalability-targets-for-a-storage-account).
8. Futtassa a [Deployment Planner](hyper-v-deployment-planner-run.md).
9. Tekintse át a javaslatok [hálózati](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) és [tárolási](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>A Hyper-V gazdagépen belül sikertelen VSS

1. Ellenőrizze az Eseménynapló bejegyzéseit, a VSS-hibák és javaslatok:
    - A Hyper-V gazdakiszolgálón, nyissa meg a Hyper-V felügyeleti eseménynaplójában **Eseménynapló** > **alkalmazások és szolgáltatásnaplók** > **Microsoft**  >  **Windows** > **a Hyper-V** > **rendszergazdai**.
    - Győződjön meg arról, hogy vannak-e bármilyen alkalmazáskonzisztens pillanatkép hibákat jelző események.
    - Egy tipikus hiba: "a Hyper-V VSS pillanatkép beállítása"XYZ"virtuális gép létrehozása nem sikerült: A-író nem átmeneti hibát észlelt. A VSS-szolgáltatás újraindítása megoldhatja problémákat, ha a szolgáltatás nem válaszol."

2. A virtuális gép létrehozása a VSS-pillanatképet, ellenőrizze, hogy a Hyper-V integrációs szolgáltatások telepítve vannak-e a virtuális gépen, és, hogy engedélyezve van-e a biztonsági mentés (VSS) integrációs szolgáltatás.
    - Győződjön meg róla, hogy az Integration Services VSS-szolgáltatás/démonok a vendégen futnak, és a egy **OK** állapota.
    - Ellenőrizheti ezt a parancsot a Hyper-V gazdagépen egy emelt szintű PowerShell-munkamenetet **et-VMIntegrationService – VMName<VMName>-név VSS** ezeket az információkat is kaphat a Vendég virtuális Gépen való bejelentkezéssel. [További információk](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Győződjön meg arról, hogy a biztonsági mentés/VSS integrációs szolgáltatásokat a virtuális gép működik és kifogástalan állapotban vannak-e. Ha nem, indítsa újra ezeket a szolgáltatásokat, és a Hyper-V Kötet-árnyékmásolata kérelmező szolgáltatás a Hyper-V gazdakiszolgálón.

### <a name="common-errors"></a>Gyakori hibák

**Hibakód:** | **üzenet** | **Részletek**
--- | --- | ---
**0x800700EA** | "A Hyper-V VSS pillanatkép beállítása a virtuális gép létrehozása nem sikerült: több adat áll rendelkezésre. (0x800700EA). VSS-pillanatkép beállított-létrehozás sikertelen lehet, ha biztonsági mentési művelet folyamatban van.<br/><br/> Nem sikerült a virtuális gép replikációs művelet: több adat érhető el. " | Ellenőrizze, hogy a virtuális gép rendelkezik-e engedélyezve van a dinamikus lemezek. Ez a funkció nem támogatott.
**0x80070032** | "A Hyper-V Kötet árnyékmásolata másolási kérelmező nem sikerült csatlakozni a virtuális gép <. / VMname > mert a verziója nem egyezik meg a Hyper-V által várt verzió | Ellenőrizze, hogy ha a legújabb Windows-frissítések telepítése.<br/><br/> [Frissítés](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) a legújabb integrációs szolgáltatásokat.



## <a name="collect-replication-logs"></a>Naplók gyűjtése

Az összes Hyper-V replikáció eseményt naplózza a Hyper-V-VMMS\Admin naplóban található **alkalmazások és szolgáltatásnaplók** > **Microsoft** > **Windows**. Emellett engedélyezheti egy elemzési naplóját a Hyper-V virtuális gépek felügyeleti szolgáltatás a következő:

1. Győződjön meg arról, az elemzési és hibakeresési naplók tekinthetik meg az eseménynaplóban. Ahhoz, hogy a naplók az eseménynaplóban elérhető, kattintson a **nézet** > **elemzési és hibakeresési naplók megjelenítése.**. Megjelenik az elemzési naplóját **Hyper-V-VMMS**.
2. Az a **műveletek** ablaktáblán kattintson a **napló engedélyezése**. 

    ![Napló engedélyezése](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Miután engedélyezve van, az megjelenik a **Teljesítményfigyelő**, mint egy **esemény-nyomkövetési munkamenet** alatt **adatgyűjtő-csoportosítók**. 
4. Az összegyűjtött információk megtekintéséhez, állítsa le a nyomkövetési munkamenet a napló letiltása. Majd mentse a naplót, és nyissa meg újra az eseménynaplóban, vagy más eszközök segítségével átalakíthatja szükség szerint.


### <a name="event-log-locations"></a>Eseménynapló helyek

**Eseménynapló** | **Részletek** |
--- | ---
**Alkalmazások és a Service Logs/Microsoft/VirtualMachineManager/kiszolgálói/felügyeleti** (VMM-kiszolgáló) | A VMM hibáinak elhárítása naplók.
**Alkalmazások és a naplók vagy MicrosoftAzureRecoveryServices/replikációs szolgáltatás** (Hyper-V gazdagép) | A Microsoft Azure Recovery Services Agent hibáinak elhárítása naplók. 
**Alkalmazások és a Service Logs/Microsoft/Azure Site Recovery/szolgáltató/műveleti** (Hyper-V gazdagép)| A Microsoft Azure Site Recovery szolgáltatás hibáinak elhárítása naplók.
**Alkalmazások és Logs/Microsoft/Windows/Hyper-V-VMMS/Szolgáltatásadminisztrátor** (Hyper-V gazdagép) | Naplózza a Hyper-V virtuális gép felügyeleti problémák elhárításához.

### <a name="log-collection-for-advanced-troubleshooting"></a>Naplók gyűjtése speciális hibaelhárítás

Ezek az eszközök speciális hibaelhárítási segítséget:

-   A VMM-ben, hajtsa végre a Site Recovery napló gyűjtése használata a [támogatási diagnosztikai Platform (SDP) eszköz](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   A Hyper-V VMM nélkül [töltse le ezt az eszközt](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), és futtassa azt a Hyper-V gazdagépen a naplók gyűjtését.

