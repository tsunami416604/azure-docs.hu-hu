---
title: Hyper-V hibaelhárítása az Azure Site Recovery Azure replikációs |} Microsoft Docs
description: Ismerteti, hogyan az Azure Site Recovery segítségével Azure replikáció és a Hyper-V problémák elhárítása
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 04/09/2018
ms.author: rayne
ms.openlocfilehash: 8b16f495a21ff0b3da415390877777650a9fbb82
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048203"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Hyper-V Azure replikációs és feladatátvételi hibaelhárítása

Ez a cikk ismerteti a gyakori problémákat, amikor replikálása a helyszíni Hyper-V virtuális gépek Azure-ba, esetleg előforduló használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Adatvédelmi problémák engedélyezése

Ha problémát tapasztal, ha engedélyezi a Hyper-V virtuális gépek védelmét, ellenőrizze a következőket:

1. Ellenőrizze, hogy a Hyper-V-gazdagépek és virtuális gépek felel meg az összes [követelmények és előfeltételek](hyper-v-azure-support-matrix.md).
2. Ha Hyper-V kiszolgálók találhatók a System Center Virtual Machine Manager (VMM) egy felhőben, győződjön meg arról, hogy előkészítése után a [VMM-kiszolgáló](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Ellenőrizze, hogy a Hyper-V Virtuálisgép-kezelő szolgáltatás fut a Hyper-V-gazdagépek.
4. Ellenőrizze a problémákra, amelyek a virtuális Gépet a Hyper-V-VMMS\Admin bejelentkezés jelennek meg. Ez a napló található **alkalmazási és Szolgáltatásnaplójában** > **Microsoft** > **Windows**.
5. A Vendég virtuális Gépen ellenőrizze, hogy a WMI engedélyezve van és elérhető.
  - [További tudnivalók](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) alapszintű WMI-ellenőrzés.
  - [Hibaelhárítás](https://aka.ms/WMiTshooting) WMI.
  - [Hibaelhárítás ](https://technet.microsoft.com/library/ff406382.aspx#H22) WMI-parancsfájlok és a szolgáltatások problémáit.
5. A Vendég virtuális Gépen ellenőrizze, hogy fut-e a legújabb integrációs szolgáltatásokat.
    - [Ellenőrizze](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) , hogy rendelkezik-e a legújabb verzióra.
    - [Tartsa](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) legújabb integrációs szolgáltatásokat.
    
## <a name="replication-issues"></a>Replikálási problémák megoldásához

Elhárítása kezdeti és folyamatos replikálási az alábbiak szerint:

1. Győződjön meg arról, hogy futtatja a [legújabb verziójára](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) a Site Recovery services.
2. Győződjön meg arról, hogy a replikáció szünetel:
  - Ellenőrizze a virtuális gép állapotát állapotát a Hyper-V Manager konzolon.
  - Ha kritikus, kattintson a jobb gombbal a virtuális gép > **replikációs** > **replikálás állapotának megtekintése**.
  - Ha a replikáció szünetel, kattintson a **replikálás folytatása**.
3. Ellenőrizze, hogy a szükséges szolgáltatások futnak-e. Ha nem, indítsa újra őket.
    - Ha Hyper-V nélkül a VMM-bA replikál, ellenőrizze, hogy ezek a szolgáltatások futnak-e a Hyper-V-állomáson:
        - Virtuálisgép-kezelő szolgáltatás
        - A Microsoft Azure Recovery Services Agent szolgáltatás
        - A Microsoft Azure Site Recovery szolgáltatásban
        - WMI-szolgáltató Host szolgáltatás
    - Ha a környezetben a VMM-mel replikál, ellenőrizze, hogy ezek a szolgáltatások futnak:
        - A Hyper-V gazdagép ellenőrizze, hogy a Virtuálisgép-kezelő szolgáltatás, a Microsoft Azure Recovery Services Agent és a WMI szolgáltató gazdaszolgáltatás futnak.
        - A VMM-kiszolgálón gondoskodjon arról, hogy a System Center Virtual Machine Manager szolgáltatás fut-e.
4. Ellenőrizze a Hyper-V kiszolgáló és az Azure közötti kapcsolatot. Ehhez nyissa meg a Feladatkezelőt a Hyper-V gazdagépen. Az a **teljesítmény** lapra, majd **nyissa meg az erőforrás-figyelő**. Az a **hálózati** lapon > **Processess hálózati tevékenységet**, ellenőrizze, hogy cbengine.exe aktívan küldésekor nagy adatkötetek (MB).
5. Ellenőrizze, hogy ha a Hyper-V-gazdagépek képesek-e csatlakozni az Azure storage blob URL-CÍMÉT. Ehhez az szükséges, válassza ki, és ellenőrizze **cbengine.exe**. Nézet **TCP-kapcsolatok** a gazdagép és az Azure storage-blob ellenőrzésére.
6. Az alább ismertetett teljesítménnyel kapcsolatos problémák ellenőrzése
    
### <a name="performance-issues"></a>Teljesítménnyel kapcsolatos problémák

Hálózati sávszélességgel kapcsolatos korlátozásai hatással lehet a replikáció. Problémamegoldás az alábbiak szerint:

1. [Ellenőrizze](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) Ha sávszélesség vagy a sávszélesség-szabályozás megkötések a környezetben.
2. Futtassa a [telepítési Planner Profilkészítő](hyper-v-deployment-planner-run.md).
3. Miután a Profilkészítő, kövesse a [sávszélesség](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) és [tárolási](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) javaslatok.
4. Ellenőrizze [adatok kavarog korlátozások](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Ha a virtuális gép kavarog magas adatok megtekintéséhez tegye a következőket:
  - Ellenőrizze, hogy ha a virtuális Gépet az újraszinkronizálás van megjelölve.
  - Hajtsa végre a [ezeket a lépéseket](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) vizsgálja meg a forgalom forrása.
  - Forgalom HRL naplófájlok meghaladja az 50 %-a szabad lemezterület a esetén fordul elő. Ha ez a probléma, létre további tárhely az összes virtuális gépet, amelyen a probléma akkor fordul elő.
  - Ellenőrizze, hogy a replikáció nem szünetel. Ha igen, azt továbbra is fennáll, a módosítások a hrl fájl, ami növelheti a megnövekedett méretének írása.
 

## <a name="critical-replication-state-issues"></a>Kritikus fontosságú replikációt az állapotproblémáit

1. Replikálás állapotának ellenőrzéséhez a helyszíni Hyper-V Manager konzol csatlakozni, válassza ki a virtuális gép és állapotának ellenőrzése.

    ![Replikáció állapota](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Kattintson a **replikálás állapotának megtekintése** a részletek megtekintéséhez:

    - Ha a replikáció szünetel, kattintson a jobb gombbal a virtuális gép > **replikációs** > **replikálás folytatása**.
    - Ha egy virtuális Gépet egy Hyper-V gazdagépen a Site Recovery szolgáltatásban konfigurált ugyanabban a fürtben lévő másik Hyper-V gazdagépet, vagy egy önálló gépet áttelepíti, a virtuális gép replikációjának nincs hatással. Csak ellenőrizze, hogy az új Hyper-V gazdagép teljesíti az összes előfeltételt, és van konfigurálva, a Site Recovery szolgáltatásban.

## <a name="app-consistent-snapshot-issues"></a>Alkalmazáskonzisztens pillanatkép problémák

Az alkalmazáskonzisztens pillanatkép a virtuális Gépen belül az alkalmazásadatok pont időponthoz kötött pillanatképet. Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális alkalmazások konzisztens állapotban a pillanatkép készítésének időpontjában.  Ez a szakasz részletesen során esetlegesen jelentkező gyakori problémákat.

### <a name="vss-failing-inside-the-vm"></a>Sikertelen a virtuális Gépen belül VSS

1. Ellenőrizze, hogy a legújabb integrációs szolgáltatások telepítve és fut-e.  Ellenőrizze, hogy egy frissítés érhető el a következő parancs futtatásával egy rendszergazda jogú PowerShell-parancssorból a Hyper-V állomáson: **get-vm |} válassza ki a nevét, állapotát, IntegrationServicesState**.
2. Ellenőrizze, hogy az VSS-szolgáltatás fut, és megfelelő:
    - Ehhez az szükséges, bejelentkezik a Vendég virtuális Gépen. Nyisson meg egy rendszergazdai parancssort, majd futtassa a következő parancsokat, hogy a VSS-író megfelelő.
        - **Vssadmin lista íróhoz.**
        - **Vssadmin lista árnyékok**
        - **Vssadmin lista szolgáltatójától**
    - Ellenőrizze a kimenetet. Ha az író hibás állapotban van, tegye a következőket:
        - Ellenőrizze az alkalmazások eseménynaplójában keresse meg a virtuális Gépen, a VSS-művelet hibákat.
    - Indítsa újra a sikertelen író társított szolgáltatásokról:
        - Kötet árnyékmásolata
         - Az Azure Site Recovery VSS-szolgáltató
    - Ezt követően, várja meg néhány óra múlva megjelenítéséhez, ha alkalmazáskonzisztens pillanatképek létrehozása sikeresen befejeződött.
    - Próbálja végső esetben végezze el a virtuális gép újraindul. Ez lehet, hogy oldja meg a nem válaszoló állapotban lévő szolgáltatásokat.
3. Ellenőrizze, hogy a virtuális gép nem rendelkezik a dinamikus lemezek. Ez az alkalmazáskonzisztens pillanatképek nem támogatott. A Lemezkezelés (diskmgmt.msc) ellenőrizheti.

    ![Dinamikus lemez](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Ellenőrizze, hogy ne legyen csatolva a virtuális gép iSCSI-lemez. Ez a funkció nem támogatott.
5. Ellenőrizze, hogy a biztonsági mentési szolgáltatás engedélyezve van-e. Győződjön meg arról, ez a **Hyper-V beállítások** > **integrációs szolgáltatások**.
6. Ellenőrizze, hogy nincsenek ütközések véve a VSS-pillanatképeket alkalmazásokkal. Ha több alkalmazás VSS-pillanatképeket érvénybe az azonos idő ütközések, akkor fordulhat elő. Ha például egy biztonsági mentési alkalmazás VSS-pillanatképeket tart a Site Recovery ütemezése a replikációs házirend pillanatképének elkészítéséhez.   
7. Ellenőrizze, hogy ha a virtuális gép olyan nagy forgalom sebessége tapasztalja:
    - A napi adatváltozási sebesség lehet mérni a Vendég virtuális gépen teljesítményszámlálók segítségével a Hyper-V gazdagépen. Ehhez engedélyezze a következő számlálót. Ez az érték 5-15 perc, a virtuális gép lemezeinek mintát Aggregrate beolvasni a virtuális gép forgalom.
        - Kategória: "a Hyper-V virtuális tárolóeszköz"
        - A számláló: "írási bájt / mp"</br>
        - Az adatok változási sebessége növeli vagy továbbra is magas szinten, attól függően, hogy hogyan foglalt a virtuális gép vagy az alkalmazások vannak.
        - Az átlagos forrás lemez adatforgalommal szabványos Site Recovery-tároló 2 MB/s. [További információ](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Ezen kívül is [ellenőrizze a tárolási méretezhetőségi célok](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#scalability-targets-for-a-storage-account).
8. Futtassa a [telepítési Planner](hyper-v-deployment-planner-run.md).
9. Tekintse át a javaslatok [hálózati](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) és [tárolási](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>A Hyper-V gazdagépen belül sikertelen VSS

1. Ellenőrizze az Eseménynapló bejegyzéseit, VSS-hibák és javaslatokat:
    - A Hyper-V gazdagép-kiszolgálón nyissa meg a Hyper-V rendszergazda eseménynaplóba **Eseménynapló** > **alkalmazási és Szolgáltatásnaplójában** > **Microsoft**  >  **Windows** > **Hyper-V** > **Admin**.
    - Győződjön meg arról, hogy vannak-e bármilyen alkalmazáskonzisztens pillanatkép hibákat jelző események.
    - Egy tipikus hiba: "a Hyper-V nem sikerült létrehozni a virtuális gép"XYZ"VSS pillanatképkészlet: az író nem átmeneti hibát észlelt. A VSS-szolgáltatás újraindítása megoldhatja problémákat, ha a szolgáltatás nem válaszol."

2. VSS-pillanatképeket létrehozni a virtuális géphez, ellenőrizze, hogy a Hyper-V integrációs szolgáltatások telepítve vannak-e a virtuális Gépre, és, hogy a biztonsági mentés (VSS) integrációs szolgáltatás engedélyezve van-e.
    - Győződjön meg arról, hogy az integrációs szolgáltatások VSS szolgáltatás/démonok a vendégen futnak, és egy **OK** állapotát.
    - Egy emelt szintű PowerShell-munkamenetben a Hyper-V gazdagépen a paranccsal ellenőrizheti ezt **et-VMIntegrationService - VMName<VMName>-név VSS** is ezt az információt kaphat a Vendég virtuális Gépen való bejelentkezés. [További információk](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Győződjön meg arról, hogy a biztonsági mentés/VSS integrációs szolgáltatásokat a virtuális Gépen fut, és kifogástalan állapotban vannak-e. Ha nem, indítsa újra ezeket a szolgáltatásokat és és a Hyper-V Kötet árnyékmásolata kérelmező szolgáltatást a Hyper-V gazdagép-kiszolgálón.

### <a name="common-errors"></a>Gyakori hibák

**Hibakód:** | **üzenet** | **Részletek**
--- | --- | ---
**0x800700EA** | "A Hyper-V VSS pillanatképkészlet a virtuális gép létrehozása nem sikerült: több adat. (0x800700EA). VSS-pillanatkép beállított-létrehozás sikertelen lehet, ha a biztonsági mentési művelet van folyamatban.<br/><br/> Nem sikerült a virtuális gép replikációs művelet: több adat. " | Ellenőrizze, hogy a virtuális gép rendelkezik-e dinamikus lemez engedélyezve van. Ez a funkció nem támogatott.
**0x80070032** | "Hyper-V Kötet árnyékmásolata másolási kérelmező nem lehet kapcsolódni a virtuális gép <. / VMname >, mert a verziója nem egyezik meg a Hyper-V által várt verzió | A legújabb Windows-frissítések telepítésének ellenőrzése.<br/><br/> [Frissítés](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services.md#keep-integration-services-up-to-date) az integrációs szolgáltatásokat a legújabb verzióra.



## <a name="collect-replication-logs"></a>Naplók gyűjtése

A Hyper-V-VMMS\Admin naplóban található összes Hyper-V replikáció esemény naplózása **alkalmazási és Szolgáltatásnaplójában** > **Microsoft** > **Windows**. Emellett engedélyezheti az elemzési naplóját a Hyper-V Virtuálisgép-kezelési szolgáltatás, az alábbiak szerint:

1. Ellenőrizze az elemzési és hibakeresési naplók az Eseménynapló használatával megtekinthető. Ehhez az Eseménynapló **nézet** > **elemzési és hibakeresési naplók megjelenítése.**. Megjelenik az analitikus napló **Hyper-V-VMMS**.
2. Az a **műveletek** ablaktáblán kattintson a **napló engedélyezése**. 

    ![Napló engedélyezése](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Az engedélyezése után megjelenik **Teljesítményfigyelő**, mint egy **eseménykövető munkamenet** alatt **adatgyűjtő-csoportosítók**. 
4. Az összegyűjtött információk megtekintéséhez állítsa le a napló letiltásával a nyomkövetési munkamenet. Majd mentse a naplót, és nyissa meg ismét az eseménynaplóban, vagy más eszközök segítségével szükség szerint konvertálja.


### <a name="event-log-locations"></a>Eseménynapló helyek

**Eseménynapló** | **Részletek** |
--- | ---
**Alkalmazások és a szolgáltatás Logs/Microsoft/VirtualMachineManager/kiszolgáló/Admin** (VMM-kiszolgáló) | Naplózza a VMM problémák hibaelhárítását.
**Alkalmazások és a naplók vagy MicrosoftAzureRecoveryServices/replikációs szolgáltatás** (Hyper-V gazdagép) | Naplózza a Microsoft Azure Recovery Services Agent problémák hibaelhárítását. 
**Alkalmazások és a szolgáltatás Logs/Microsoft/Azure Site Recovery/szolgáltató/műveleti** (Hyper-V gazdagép)| Naplózza a Microsoft Azure Site Recovery szolgáltatás problémák hibaelhárítását.
**Alkalmazások és a Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V gazdagép) | Naplózza a Hyper-V Virtuálisgép-felügyeleti problémák elhárításához.

### <a name="log-collection-for-advanced-troubleshooting"></a>Speciális hibaelhárítás naplógyűjtést

Ezek az eszközök is segítséget nyújt a speciális hibaelhárítás:

-   A VMM Szolgáltatáshoz, hajtsa végre a Site Recovery napló gyűjtemény használja a [támogatási diagnosztikai Platform (SDP) eszköz](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   A Hyper-V nélkül, a VMM [a Letöltés](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), és futtassa a Hyper-V gazdagépen a naplók gyűjtésére.

