---
title: Hyper-V vészhelyreállítás hibáinak elhárítása az Azure Site Recovery szolgáltatással
description: A Hyper-V és az Azure replikációval kapcsolatos vész-helyreállítási problémák elhárításának ismertetése az Azure Site Recovery használatával
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 0a3e5c922009353e4ba9ccab12cf70ea2b5992da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961478"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>A Hyper-V-ről az Azure-ba végzett replikáció és feladatátvétel hibaelhárítása

Ez a cikk ismerteti azokat a gyakori problémákat, amelyek a helyszíni Hyper-V virtuális gépek Azure-ba replikálásakor az Azure Site Recovery használatával [találkozhatnak.](site-recovery-overview.md)

## <a name="enable-protection-issues"></a>Védelmi problémák engedélyezése

Ha problémákat tapasztal a Hyper-V virtuális gépek védelmének engedélyezésekor, ellenőrizze az alábbi javaslatokat:

1. Ellenőrizze, hogy a Hyper-V-állomások és a virtuális gépek megfelelnek-e az összes [követelménynek és előfeltételnek.](hyper-v-azure-support-matrix.md)
2. Ha a Hyper-V kiszolgálók a System Center Virtual Machine Manager (VMM) felhőkben találhatók, ellenőrizze, hogy előkészítette-e a [VMM-kiszolgálót.](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional)
3. Ellenőrizze, hogy a Hyper-V virtuálisgép-kezelő szolgáltatás fut-e a Hyper-V gazdagépeken.
4. Ellenőrizze, hogy a problémák jelennek meg a Hyper-V-VMMS\Admin bejelentkezés a virtuális gépre. Ez a napló a**Microsoft** > **Windows**alkalmazások és **szolgáltatások naplóiban** > található.
5. A vendég virtuális gép, ellenőrizze, hogy a WMI engedélyezve van és elérhető.
   - [További információ az](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) alapvető WMI-tesztelésről.
   - [Hibaelhárítás](https://aka.ms/WMiTshooting) Wmi.
   - A WMI-parancsfájlokkal és -szolgáltatásokkal kapcsolatos problémák [elhárítása.](https://technet.microsoft.com/library/ff406382.aspx#H22)
6. A vendég virtuális gép győződjön meg arról, hogy az integrációs szolgáltatások legújabb verziója fut.
    - [Ellenőrizze,](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) hogy a legújabb verzióval rendelkezik-e.
    - [Megtartás](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integrációs szolgáltatások naprakész.
    
## <a name="replication-issues"></a>Replikációs problémák

A kezdeti és a folyamatban lévő replikációval kapcsolatos problémákat a következő módon háríthatja el:

1. Győződjön meg arról, hogy a Site Recovery szolgáltatások [legújabb verzióját](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) futtatja.
2. Ellenőrizze, hogy a replikáció szünetel-e:
   - Ellenőrizze a virtuális gép állapotát a Hyper-V Manager konzolon.
   - Ha kritikus fontosságú, kattintson a jobb gombbal a virtuális gép > **replikációs** > **nézet replikációs állapotának állapotán.**
   - Ha a replikáció szünetel, kattintson **a Replikáció folytatása gombra.**
3. Ellenőrizze, hogy futnak-e a szükséges szolgáltatások. Ha nem, indítsa újra őket.
    - Ha a Hyper-V-t VMM nélkül replikálja, ellenőrizze, hogy ezek a szolgáltatások futnak-e a Hyper-V gazdagépen:
        - Virtuálisgép-kezelő szolgáltatás
        - Microsoft Azure Recovery Services-ügynök szolgáltatás
        - Microsoft Azure Site Recovery szolgáltatás
        - WMI Provider Host szolgáltatás
    - Ha a környezetben replikálja a VMM-et, ellenőrizze, hogy futnak-e ezek a szolgáltatások:
        - A Hyper-V gazdagépen ellenőrizze, hogy a Virtuálisgép-kezelő szolgáltatás, a Microsoft Azure Recovery Services Agent és a WMI-szolgáltató gazdaszolgáltatásfut-e.
        - A VMM-kiszolgálón győződjön meg arról, hogy a System Center virtuálisgép-kezelő szolgáltatás fut.
4. Ellenőrizze a Hyper-V-kiszolgáló és az Azure közti kapcsolatot. A kapcsolat ellenőrzéséhez nyissa meg a Feladatkezelőt a Hyper V állomáson. A **Teljesítmény** lapon kattintson az **Erőforrásfigyelő megnyitása gombra.** A **Hálózat** lapon > **Folyamat a hálózati tevékenységgel**lapon ellenőrizze, hogy a cbengine.exe aktívan küld-e nagy mennyiségű adatot.
5. Ellenőrizze, hogy a Hyper-V-állomások csatlakozhatnak-e az Azure storage blob URL-címéhez. Annak ellenőrzéséhez, hogy a gazdagépek csatlakozhatnak-e, jelölje ki és ellenőrizze **a cbengine.exe programot.** **TCP-kapcsolatok** megtekintéséhez a gazdagép és az Azure storage blob közötti kapcsolat ellenőrzéséhez.
6. Ellenőrizze a teljesítménnyel kapcsolatos problémákat az alábbiakszerint.
    
### <a name="performance-issues"></a>Teljesítményproblémák

A hálózati sávszélesség korlátozásai hatással lehetnek a replikációra. A következő problémák elhárítása:

1. [Ellenőrizze,](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) hogy vannak-e sávszélesség- vagy sávszélesség-szabályozási korlátozások a környezetben.
2. Futtassa a [Központi telepítéstervező profilozót.](hyper-v-deployment-planner-run.md)
3. A profilozó futtatása után kövesse a [sávszélesség-](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) és [tárolási](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) javaslatokat.
4. Ellenőrizze [az adatforgalmi forgalom korlátozásait.](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits) Ha magas adatforgalom jelenik meg egy virtuális gépen, tegye a következőket:
   - Ellenőrizze, hogy a virtuális gép van-e megjelölve az újraszinkronizáláshoz.
   - Kövesse [az alábbi lépéseket](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) a lemorzsolódás forrásának vizsgálatához.
   - Lemorzsolódás akkor fordulhat elő, ha a HRL naplófájlok meghaladják a rendelkezésre álló lemezterület 50%-át. Ha ez a probléma, több tárhelyet biztosít az összes virtuális gép, amelyen a probléma jelentkezik.
   - Ellenőrizze, hogy a replikáció nincs-e szüneteltetve. Ha ez így van, akkor továbbra is írásban a változásokat a hrl fájlt, amely hozzájárulhat a megnövekedett mérete.
 

## <a name="critical-replication-state-issues"></a>Kritikus replikációs állapotmal kapcsolatos problémák

1. A replikáció állapotának ellenőrzéséhez csatlakozzon a helyszíni Hyper-V Manager konzolhoz, válassza ki a virtuális gép, és ellenőrizze az állapotát.

    ![Replikáció állapota](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. A részletek megtekintéséhez kattintson a **Replikáció állapotának megtekintése** elemre:

    - Ha a replikáció szünetel, kattintson a jobb gombbal a virtuális gép > **replikáció folytatása** > **replikációs replikációra.**
    - Ha egy virtuális gép egy Hyper-V gazdagép konfigurálva site recovery áttelepíti egy másik Hyper-V gazdagép ugyanabban a fürtben, vagy egy önálló gép, a virtuális gép replikációja nem érinti. Csak ellenőrizze, hogy az új Hyper-V állomás megfelel-e az összes előfeltételnek, és a Site Recovery konfigurálva van.

## <a name="app-consistent-snapshot-issues"></a>Alkalmazáskonzisztens pillanatkép-problémák

Az alkalmazáskonzisztens pillanatkép a virtuális gépen belüli alkalmazásadatok időbeli pillanatképe. Kötet árnyékmásolata szolgáltatás (VSS) biztosítja, hogy a virtuális gép alkalmazások konzisztens állapotban, amikor a pillanatkép készül.  Ez a szakasz néhány gyakori problémát részletez.

### <a name="vss-failing-inside-the-vm"></a>VSS sikertelen ül a virtuális gépbelsejében

1. Ellenőrizze, hogy az integrációs szolgáltatások legújabb verziója telepítve van-e és fut-e.  Ellenőrizze, hogy elérhető-e frissítés a következő parancs futtatásával egy emelt szintű PowerShell-parancsból a Hyper-V gazdagépen: **get-vm | select Name, State, IntegrationServicesState**.
2. Ellenőrizze, hogy a VSS-szolgáltatások futnak-e és kifogástalan állapotúak-e:
   - A szolgáltatások ellenőrzéséhez jelentkezzen be a vendég virtuális gép. Ezután nyisson meg egy rendszergazdai parancssort, és futtassa a következő parancsokat annak ellenőrzéséhez, hogy az összes VSS-író kifogástalan állapotú-e.
       - **Vssadmin lista írók**
       - **Vssadmin lista árnyékok**
       - **Vssadmin listaszolgáltatók**
   - Ellenőrizze a kimenetet. Ha az írók hibás állapotban vannak, tegye a következőket:
       - Ellenőrizze az alkalmazás eseménynaplóját a vm vss működési hibák.
   - Próbálja meg újraindítani ezeket a hibás íróhoz társított szolgáltatásokat:
     - Kötet árnyékmásolata
       - Azure Site Recovery VSS-szolgáltató
   - Ezt követően várjon néhány órát, hogy lássa, sikeresen létrejöttek-e az alkalmazáskonzisztens pillanatképek.
   - Végső megoldásként próbálja meg újraindítani a virtuális gépet. Ez feloldhatja a nem válaszoló állapotban lévő szolgáltatásokat.
3. Ellenőrizze, hogy nem rendelkezik-e dinamikus lemezekkel a virtuális gépben. THis nem támogatja az alkalmazás-konzisztens pillanatképek. A Lemezkezelés (diskmgmt.msc) részben található.

    ![Dinamikus lemez](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Ellenőrizze, hogy nincs-e iSCSI-lemez csatlakoztatva a virtuális géphez. Ez a funkció nem támogatott.
5. Ellenőrizze, hogy a Biztonsági mentés szolgáltatás engedélyezve van-e. Ellenőrizze, hogy engedélyezve **van-e a Hyper-V beállítások** > **integrációs szolgáltatásaiban.**
6. Győződjön meg arról, hogy nincsenek ütközések a VSS-pillanatképeket levevő alkalmazásokkal. Ha több alkalmazás próbál VSS-pillanatképek et egyszerre ütközések fordulhatnak elő. Ha például egy biztonsági mentési alkalmazás VSS-pillanatképeket készít, amikor a replikációs házirend úgy ütemezi a Site Recovery-t, hogy pillanatképet készítsen.   
7. Ellenőrizze, hogy a virtuális gép magas lemorzsolódási arányt tapasztal-e:
    - A hyper-V gazdagépen teljesítményszámlálók használatával mérheti a vendég virtuális gépek napi adatváltozási sebességét. Az adatváltozási sebesség méréséhez engedélyezze a következő számlálót. Ennek az értéknek a mintáját 5–15 percig a virtuális gép lemezein kell összesíteni a virtuális gép lemorzsolódásának lekérnie.
        - Kategória: "Hyper-V virtuális tárolóeszköz"
        - Számláló: "Írj bájt / Sec"</br>
        - Ez az adatváltozási sebesség növekedni fog, vagy magas szinten marad, attól függően, hogy mennyire elfoglalt a virtuális gép vagy az alkalmazások.
        - Az átlagos forráslemez adatváltozása 2 MB/s a Site Recovery szabványos tárolásához. [További információ](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Ezenkívül [ellenőrizheti a tárolási méretezhetőségi célokat](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)is.
8. Futtassa a [Központi telepítéstervezőt](hyper-v-deployment-planner-run.md).
9. Tekintse át a [hálózati](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) és [tárolási](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)javaslatokat.


### <a name="vss-failing-inside-the-hyper-v-host"></a>A VSS sikertelen ül a Hyper-V gazdagépen belül

1. Ellenőrizze az eseménynaplókban a VSS-hibákat és -javaslatokat:
    - A Hyper-V gazdakiszolgálón nyissa meg a Hyper-V felügyeleti eseménynaplót az **Eseménynapló** > **alkalmazások és szolgáltatások naplóiban,** > a**Microsoft** > **Windows** > **Hyper-V** > **Admin**alkalmazásban.
    - Ellenőrizze, hogy vannak-e olyan események, amelyek alkalmazáskonzisztens pillanatkép-hibákat jeleznek.
    - Egy tipikus hiba: "A Hyper-V nem tudta létrehozni a VSS pillanatkép-készletet az "XYZ" virtuális géphez: Az író nem átmeneti hibát észlelt. A VSS-szolgáltatás újraindítása megoldhatja a problémákat, ha a szolgáltatás nem válaszol."

2. VsS-pillanatképek létrehozásához a virtuális gép, ellenőrizze, hogy a Hyper-V integrációs szolgáltatások telepítve vannak a virtuális gépre, és hogy a biztonsági mentési (VSS) integrációs szolgáltatás engedélyezve van.
    - Győződjön meg arról, hogy az integrációs szolgáltatások VSS szolgáltatás/démonok futnak a vendég, és **egy OK** állapotban vannak.
    - Ezt a Get-VMIntegrationService -VMName VMName>-Name VSS nevű rendszergazdai jogosultságú PowerShell-munkamenetből is ellenőrizheti a **Get-VMIntegrationService -VMName VMName\<>-Name VSS.** [További információ](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Győződjön meg arról, hogy a biztonsági mentés/VSS integrációs szolgáltatások a virtuális gép futnak, és kifogástalan állapotban vannak. Ha nem, indítsa újra ezeket a szolgáltatásokat, és a Hyper-V kötet árnyékmásolata kérvényező szolgáltatás a Hyper-V gazdakiszolgálón.

### <a name="common-errors"></a>Gyakori hibák

**Hibakód** | **Üzenetet** | **Részletek**
--- | --- | ---
**0x800700EA** | "A Hyper-V nem tudta létrehozni a VSS pillanatkép-készletet a virtuális géphez: További adatok érhetők el. (0x800700EA). VSS pillanatkép készlet létrehozása sikertelen lehet, ha a biztonsági mentési művelet folyamatban van.<br/><br/> A virtuális gép replikációs művelete nem sikerült: További adatok érhetők el." | Ellenőrizze, hogy a virtuális gép rendelkezik-e dinamikus lemez engedélyezve. Ez a funkció nem támogatott.
**0x80070032** | "A Hyper-V kötet árnyékmásolata kérelmező nem tudott csatlakozni a <./VMname>, mert a verzió nem felel meg a Hyper-V által várt verziónak | Ellenőrizze, hogy telepítve vannak-e a legújabb Windows-frissítések.<br/><br/> [Frissítsen](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) az integrációs szolgáltatások legújabb verziójára.



## <a name="collect-replication-logs"></a>Replikációs naplók gyűjtése

Az összes Hyper-V replikációs esemény a**Microsoft** > **Windows**alkalmazások és **szolgáltatások naplóiban** > található Hyper-V-VMMS\Admin naplóban kerül naplózásra. Ezenkívül engedélyezheti az analitikus naplót a Hyper-V virtuálisgép-kezelő szolgáltatáshoz, az alábbiak szerint:

1. Az Analitikus és a Hibakeresési naplók láthatóvá tehetők az Eseménynaplóban. A naplók elérhetővé tenni az Eseménynaplóban kattintson az Analitikus megjelenítése és a Hibakeresési naplók **megtekintése** > **parancsra.** Az analitikus napló a **Hyper-V-VMMS alatt**jelenik meg.
2. A **Műveletek** ablaktáblán kattintson a **Napló engedélyezése gombra.** 

    ![Napló engedélyezése](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Miután engedélyezve van, a **Teljesítményfigyelőben** **eseménykövetési munkamenetként** jelenik meg **az adatgyűjtő-csoportosítók**alatt. 
4. Az összegyűjtött adatok megtekintéséhez állítsa le a nyomkövetési munkamenetet a napló letiltásával. Ezután mentse a naplót, és nyissa meg újra az Eseménynaplóban, vagy használjon más eszközöket a konvertálásához, ha szükséges.


### <a name="event-log-locations"></a>Eseménynapló helyei

**Eseménynapló** | **Részletek** |
--- | ---
**Alkalmazások és szerviznaplók/Microsoft/VirtualMachineManager/Server/Admin** (VMM-kiszolgáló) | VMM-problémák elhárítására irányuló naplók.
**Alkalmazások és szolgáltatásnaplók/MicrosoftAzureRecoveryServices/Replication** (Hyper-V állomás) | Naplók a Microsoft Azure Recovery Services Agent hibáinak elhárításához. 
**Alkalmazások és szolgáltatásnaplók/Microsoft/Azure site recovery/provider/operational** (Hyper-V host)| Naplók a Microsoft Azure Site Recovery Service hibáinak elhárításához.
**Alkalmazások és szerviznaplók/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V állomás) | Naplók a Hyper-V VM felügyeleti problémáinak elhárításához.

### <a name="log-collection-for-advanced-troubleshooting"></a>Naplógyűjtés a speciális hibaelhárításhoz

Ezek az eszközök segíthetnek a speciális hibaelhárításban:

-   A VMM esetében hajtsa végre a Webhely-helyreállítási naplógyűjteményt a [Support Diagnostics Platform (SDP) eszközzel.](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)
-   A VMM nélküli Hyper-V esetében [töltse le ezt az eszközt](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), és futtassa a Hyper-V gazdagépen a naplók összegyűjtéséhez.

