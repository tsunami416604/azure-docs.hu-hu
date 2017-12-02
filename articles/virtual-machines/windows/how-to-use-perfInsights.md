---
title: "PerfInsights használata a Microsoft Azure |} Microsoft Docs"
description: "Tanulja meg PerfInsights használata Windows virtuális gép teljesítményét problémák megoldásához."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: bb4c21456643532df040df4fcd5f4fa1a4f48d2c
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-perfinsights"></a>PerfInsights használata 

[PerfInsights](http://aka.ms/perfinsightsdownload) egy automatizált parancsfájl, amely hasznos diagnosztikai adatokat gyűjt, i/o-magas terhelés terhelések fut, és egy elemző jelentés segítségével a Windows virtuális gép teljesítményét kapcsolatos problémák elhárítása a Microsoft Azure biztosít. Ez a virtuális gépek önálló parancsfájlként vagy futtatható közvetlenül a portálról telepítésével [Azure teljesítmény Diagnostics Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md).

Azt javasoljuk, hogy futtassa ezt a parancsfájlt egy támogatási jegy a Microsofttal a virtuális gépek teljesítményproblémái megnyitása előtt.

## <a name="supported-troubleshooting-scenarios"></a>Hibaelhárítási forgatókönyveket támogatja.

PerfInsights is összegyűjti és elemzi az adatokat több különböző, egyedi forgatókönyvek szerint vannak csoportosítva.

### <a name="collect-basic-configuration"></a>Alapszintű konfigurációs gyűjtése 

Ebben a forgatókönyvben a lemezkonfigurációt és egyéb fontos információkat, beleértve a következőket gyűjti:

-   Eseménynaplók

-   Az összes bejövő és kimenő kapcsolatok hálózati állapota

-   Hálózati és tűzfalbeállításokat konfigurációs beállításai

-   A rendszer a jelenleg futó összes alkalmazás feladatlista

-   A virtuális gép (VM) msinfo32 által létrehozott információs fájl

-   Microsoft SQL Server adatbázis-konfigurációs beállításokat (Ha a virtuális gép ki van jelölve egy SQL Servert futtató kiszolgáló)

-   Tárolási megbízhatóság számlálók

-   Fontos Windows-gyorsjavítások

-   Telepített fájlrendszerszűrő-illesztőprogramok

Ez a passzív gyűjteménye, amely nem befolyásolja a rendszer. 

>[!Note]
>Ebben a forgatókönyvben az alábbi esetek mindegyikében automatikusan tartalmazza.

### <a name="benchmarking"></a>Teljesítménymérésre

Ez a forgatókönyv futtatása a [diskspd](https://github.com/Microsoft/diskspd) teljesítményteszt (iops-érték és MB/s) a virtuális géphez csatolt összes merevlemezén. 

> [!Note]
> Ez a forgatókönyv befolyásolhatja a rendszer, és egy éles rendszeren nem futtatható. Ha szükséges, futtassa a ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt teszt által okozott nagyobb munkaterhelést ronthatja a teljesítményt, a virtuális gép.
>

### <a name="slow-vm-analysis"></a>Lassú VM elemzés 

Ez a forgatókönyv futtatása egy [teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) nyomkövetési a számlálók a Generalcounters.txt fájlban megadott használatával. Ha a virtuális gép az SQL Servert futtató kiszolgálónak, a teljesítmény-számláló nyomkövetési fut a számlálókat, amelyek szerepelnek a Sqlcounters.txt fájl használatával. Teljesítmény diagnosztikai adatokat is tartalmaz.

### <a name="slow-vm-analysis-and-benchmarking"></a>Lassú VM elemzés és teljesítménymérésre

Ez a forgatókönyv futtatása egy [teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) nyomkövetési elé egy [diskspd](https://github.com/Microsoft/diskspd) teljesítményteszt. 

> [!Note]
> Ez a forgatókönyv befolyásolhatja a rendszer, és egy éles rendszeren nem futtatható. Ha szükséges, futtassa a ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt teszt által okozott nagyobb munkaterhelést ronthatja a teljesítményt, a virtuális gép.
>

### <a name="azure-files-analysis"></a>Az Azure fájlok elemzés 

Ebben a forgatókönyvben egy különleges teljesítmény számláló rögzítési együtt a hálózati nyomkövetés fut. A rögzítés a "SMB-ügyfél megosztások" minden számlálókat tartalmaz. Az alábbiakban néhány kulcsfontosságú SMB ügyfél megosztás teljesítményszámlálók a rögzítés részét képező:

| **Típus**     | **SMB-ügyfél megosztások számláló** |
|--------------|-------------------------------|
| IO         | Kérelmek/másodperc             |
|              | Olvassa el a kérelmek/másodperc             |
|              | Írási kérelmek/másodperc            |
| Késés      | Kérelemfeldolgozás mp/adatok         |
|              | Átlagos olvasási idő                 |
|              | Átlagos mp/írás                |
| IO-méret      | Átlagos Bájtok/kérelem       |
|              | Átlagos Bájtos, Olvasás               |
|              | Átlagos Bájt írása              |
| Teljesítmény   | Adatok bájtok/s                |
|              | Olvasott bájt/mp                |
|              | Írási bájtok/s               |
| Várólista hossza | Átlagos Olvasási várólistájának hossza        |
|              | Átlagos Írni a várólista hossza       |
|              | Átlagos Várólista hossza        |

### <a name="custom-slow-vm-analysis"></a>Egyéni lassú VM elemzés 

Egy egyéni lassú VM elemzés futtatásakor meg (teljesítményszámláló, következő helyen, hálózati, storport) összes adat a párhuzamosan futó, attól függően, hány különböző nyomkövetések van kiválasztva. Nyomkövetés befejezése után az eszköz a diskspd teljesítményteszt fut, ha az van kiválasztva. 

> [!Note]
> Ez a forgatókönyv befolyásolhatja a rendszer, és egy éles rendszeren nem futtatható. Ha szükséges, futtassa a ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt teszt által okozott nagyobb munkaterhelést ronthatja a teljesítményt, a virtuális gép.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Milyen típusú adatokat a parancsfájl által összegyűjtött?

Windows virtuális gép, lemezt vagy tárolási készletek konfigurációt, teljesítményszámlálóival, információkat naplózza, és különböző nyomkövetési adatokat gyűjtött attól függően, hogy a teljesítmény forgatókönyvet használja:

|Összegyűjtött adatok                              |  |  | Teljesítmény-forgatókönyvek |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Alapszintű konfigurációs gyűjtése | Teljesítménymérésre | Lassú VM elemzés | Lassú VM elemzés és teljesítménymérésre | Az Azure fájlok elemzés | Egyéni lassú VM elemzés |
| Eseménynapló információk      | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Rendszerinformáció               | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Kötet térkép                       | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Lemez térkép                         | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Az éppen futó feladatok                    | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Tárolási megbízhatóság számlálók     | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Szolgáltatás adatai              | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Fsutil kimeneti                    | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Szűrő-illesztőprogram adatai               | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Netstat kimeneti                   | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Hálózati konfiguráció            | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Tűzfal-konfiguráció           | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| SQL Server-konfigurációs         | Igen                        | Igen                                | Igen                      | Igen                            | Igen                  | Igen                  |
| Teljesítmény diagnosztikai nyomkövetési * | Igen                        | Igen                                | Igen                      |                                | Igen                  | Igen                  |
| Teljesítményszámláló nyomkövetési **     |                            |                                    |                          |                                |                      | Igen                  |
| SMB-számláló nyomkövetési **             |                            |                                    |                          |                                | Igen                  |                      |
| SQL Server számláló nyomkövetési **      |                            |                                    |                          |                                |                      | Igen                  |
| Nyomkövetési a következő helyen                      |                            |                                    |                          |                                |                      | Igen                  |
| StorPort-nyomkövetési                   |                            |                                    |                          |                                |                      | Igen                  |
| Hálózati nyomkövetés futtatása                    |                            |                                    |                          |                                | Igen                  | Igen                  |
| A Diskspd teljesítményteszt nyomkövetési x      |                            | Igen                                |                          | Igen                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Teljesítmény-diagnosztikai nyomkövetési (*)

Az adatok gyűjtéséhez és folyamatos teljesítményt eseményadatokat a háttérben fut, egy szabályalapú motor. A következő szabályok jelenleg támogatottak:

- HighCpuUsage szabály: magas CPU-használat időszakok észleli, és a felső CPU-használat fogyasztók időszak során tartalmazza.
- HighDiskUsage szabály: fizikai lemezek magas lemez időszakai észleli, és bemutatja a felső lemez használati fogyasztók ezen időszak alatt.
- HighResolutionDiskMetric szabály: látható IOPS, az átviteli sebesség és az I/O várakozási ideje metrikák / minden fizikai lemez 50 milliszekundumban megadva. Ennek segítségével gyorsan azonosíthatja a sávszélesség-szabályozás időszakok lemez.
- HighMemoryUsage szabály: felső memóriaterület időszakai észleli, és bemutatja a felső memória használati fogyasztók ezen időszak alatt.

> [!NOTE] 
> Jelenleg a Windows, amely tartalmazza a .NET-keretrendszer 3.5-ös vagy későbbi verziói támogatottak.

### <a name="performance-counter-trace-"></a>Teljesítmény-számláló nyomkövetési (*)

Gyűjti a következő teljesítményszámlálókkal:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk, \LogicalDisk
- \Cache\Dirty lapok, \Cache\Lazy írási ürítések másodpercenkénti \Server\Pool lapozható, hibák, a lapozható \Server\Pool hibák
- Kijelölt számlálók \Network felületet, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, a hálózati illesztő kártya processzortevékenység, \Microsoft Winsock BSP \Per

#### <a name="for-sql-server-instances"></a>SQL Server-példányok
- \SQL server: pufferkezelőnek, \SQLServer:Resource készlet statisztikák, \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, statisztikák
- \SQLServer:Access módszerek

#### <a name="for-azure-files"></a>Az Azure-fájlok
\SMB Ügyfélmegosztások

### <a name="diskspd-benchmark-trace-"></a>A Diskspd teljesítményteszt nyomkövetési (*)
A Diskspd IO munkaterhelés tesztek [operációsrendszer-lemez (írás) és készlet meghajtók (olvasás/írás)]

## <a name="run-the-perfinsights-on-your-vm"></a>Futtassa a PerfInsights a virtuális gépen

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Mi van szükségem a parancsfájl futtatása előtt? 

**Parancsfájl-követelmények**

1.  Ezt a parancsfájlt, amely rendelkezik a teljesítménycsökkenés oka a virtuális gépen kell futnia. 

2.  Támogatottak a következő operációs rendszer: Windows Server 2008 R2, 2012, 2012 R2-ben 2016; Windows 8.1 és Windows 10.

**Lehetséges problémák, a virtuális gépek éles a parancsfájl futtatásakor:**

1.  Ha bármely Benchmarking esetek vagy az "Egyéni lassú VM analysis" forgatókönyvet, amely a következő helyen vagy a DiskSpd használatára van konfigurálva, a parancsfájl kedvezőtlen hatással lehet a virtuális gép teljesítményére. Ezek a forgatókönyvek futtatása nélkül a CSS mérnöke felügyelete éles környezetben nem ajánlott.

2.  Ha bármely Benchmarking esetek vagy az "Egyéni lassú VM analysis"-forgatókönyvekben, amelyek a DiskSpd használatára van konfigurálva, győződjön meg arról, hogy nincs más háttértevékenység zavarja a i/o-munkaterhelés a tesztelt lemezeken.

3.  Alapértelmezés szerint a parancsfájl az ideiglenes tárolási meghajtó adatainak gyűjtéséről. Ha nyomkövetése engedélyezve van a hosszabb ideig marad, előfordulhat, hogy a gyűjtött adatok mennyisége megfelelő. Ezzel csökkenthető a lemezen a ideiglenes, ezért érintő bármilyen alkalmazás, amely a meghajtó támaszkodik rendelkezésre állását.

### <a name="how-do-i-run-perfinsights"></a>Hogyan futtathatok PerfInsights? 

Telepítse a virtuális gépen PerfInsights is futtathatja [Azure teljesítmény Diagnostics Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md) vagy önálló parancsfájlként futtatáshoz. 

**Telepítse, PerfInsights futtassa az Azure-portálon**

PerfInsights most futtatható Azure teljesítmény diagnosztika bővítmény nevű Virtuálisgép-bővítmény használatával. További információkért lásd: [telepítése Azure teljesítmény diagnosztikai bővítmény](performance-diagnostics-vm-extension.md#install-the-extension).  

**Önálló módban PerfInsights parancsfájl futtatása**

A parancsfájl futtatásához a PerfInsights kövesse az alábbi lépéseket:


1. Töltse le [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. A PerfInsights.zip fájl feloldása. Ehhez kattintson a jobb gombbal a PerfInsights.zip fájlt, jelölje be **tulajdonságok**. A a **általános** lapon jelölje be **Unblock** majd **OK**. Ezzel biztosítható, hogy a parancsfájl futtatása nélkül más biztonsági kérdések.  

    ![A zip-fájl zárolásának feloldása](media/how-to-use-perfInsights/unlock-file.png)

3.  Bontsa ki a tömörített PerfInsights.zip a ideiglenes meghajtóba (alapértelmezés szerint ez általában a D meghajtó). A tömörített fájlt tartalmaznia kell a következő fájlok és mappák:

    ![a zip-mappában található fájlokat](media/how-to-use-perfInsights/file-folder.png)

4.  Nyissa meg a Windows Powershellt rendszergazdaként, és futtassa a PerfInsights.ps1 parancsfájl.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Lehetséges, hogy be az "y", ha annak megerősítéséhez, hogy a végrehajtási házirend módosításához kéri.

    A jogi nyilatkozat párbeszédpanelen lehetősége van a Microsoft Support diagnosztikai információk megoszthatók. Beleegyezik kell is, a folytatáshoz a licencszerződés feltételeit. Adja meg a beállításokat, és kattintson a **-parancsfájl futtatása**.

    ![Jogi nyilatkozat mezőbe](media/how-to-use-perfInsights/disclaimer.png)

5.  Küldje el a nagybetűk, amelyet, amennyiben az rendelkezésre áll, ha a parancsfájl futtatása (Ez a a statisztika). Kattintson a **OK**.
    
    ![Adja meg a támogatási azonosító](media/how-to-use-perfInsights/enter-support-number.png)

6.  Válassza ki az átmeneti tárolási meghajtót. A parancsfájl is automatikus észlelés a meghajtó betűjele. Ebben a szakaszban olyan probléma lép fel, ha a program kérheti válassza ki a meghajtót, (az alapértelmezett meghajtón D). Létrehozott naplók itt tárolják a napló\_gyűjtemény mappát. Adja meg, vagy fogadja el a meghajtóbetűjelet, után kattintson **OK**.

    ![Adja meg a meghajtó](media/how-to-use-perfInsights/enter-drive.png)

7.  A hibaelhárítási választása a megadott listáról.

       ![Válassza ki a támogatási példák](media/how-to-use-perfInsights/select-scenarios.png)

8.  PerfInsights felhasználói felület nélkül is futtathatja.

    A következő parancs futtatása a felhasználói felület kérdés nélkül forgatókönyv hibaelhárítási "lassú VM analysis", vagy az adatok rögzítéséhez 30 másodpercig. A rendszer kérni fogja, hogy ugyanazon jogi nyilatkozat és a 4. lépésben említett EULA hozzájárulás.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    Ha azt szeretné, hogy PerfInsights csendes módban való futásra, használja a **- AcceptDisclaimerAndShareDiagnostics** paraméter. Például a következő paranccsal:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Hogyan hibáinak elhárítása problémák a parancsprogram futtatása közben?

Ha a parancsfájl rendellenesen áll, törölheti is inkonzisztens állapotba a parancsfájl futtatásával együtt a - karbantartási kapcsolóval az alábbiak szerint:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Ha problémák fordulnak elő során az ideiglenes meghajtón automatikus észlelését, a program kérheti válassza ki a meghajtót, (az alapértelmezett meghajtón D).

![Adja meg meghajtó](media/how-to-use-perfInsights/enter-drive.png)

A parancsfájl a segédeszközök eltávolítja, és eltávolítja az ideiglenes mappát.

### <a name="troubleshooting-other-script-issues"></a>Más parancsfájl problémák elhárítása 

Ha bármilyen probléma akkor fordulhat elő, ha a parancsfájl futtatása, nyomja le a Ctrl + C feleslegesen zavarni a parancsfájl végrehajtása. Eltávolítja az ideiglenes objektumokat, a "Nem várt leállás után törlése" című szakaszában talál.

Parancsfájl hiba tapasztalható még több próbálkozást követően továbbra is, ha azt javasoljuk, hogy a parancsfájl futtatása "hibakeresési módban" használatával az "-Debug" paraméter indításakor.

A hiba akkor fordul elő, miután a PowerShell-konzol teljes kimenetének másolása HTML-, és elküldi a Microsoft Support ügynök segít a hibaelhárítás elősegítése érdekében.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>A parancsfájl futtatása egyéni lassú VM elemzés módban

Kiválasztásával a **egyéni lassú VM elemzés**, engedélyezheti a párhuzamos (használja a Shift billentyű többszörös kijelöléssel) több nyomkövetési adatokat:

![Válassza ki a forgatókönyvek](media/how-to-use-perfInsights/select-scenario.png)

Kiválasztásakor a teljesítmény számláló nyomkövetési, a következő helyen nyomkövetési, a hálózati nyomkövetés vagy a Storport-nyomkövetési forgatókönyvek a párbeszédpanel utasításait, és próbálja meg újra a teljesítményromlást előállítani, a nyomkövetések elindítása után.

A következő párbeszédpanelen lehetővé teszi a nyomkövetés indításához:

![Start-nyomkövetési](media/how-to-use-perfInsights/start-trace-message.png)

Leállítja a nyomkövetési adatokat, hogy a parancs második párbeszédpanelen.

![nyomkövetés leállítása](media/how-to-use-perfInsights/stop-trace-message.png)
![nyomkövetés leállítása](media/how-to-use-perfInsights/ok-trace-message.png)

A nyomkövetési adatokat vagy a műveletek befejezése után egy új fájl jön létre a D:\\napló\_gyűjtemény (vagy az ideiglenes meghajtón) nevű **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip.** Ez a fájl küldhet a támogatási ügynök elemzés céljából.

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>Tekintse át a PerfInsights által létrehozott diagnosztikai jelentés

Belül a **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip fájl** , amely PerfInsights által generált, egy HTML-jelentést, amely leírja, PerfInsights eredményei található. Tekintse át a jelentést, bontsa ki a **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip** fájlt, és nyissa meg a **PerfInsights diagram jelentés.HTML** fájlt.

Válassza ki a **megállapítások** fülre.

![Keresés lap](media/how-to-use-perfInsights/findingtab.png)
![megállapítások](media/how-to-use-perfInsights/findings.PNG)

**Megjegyzések**

-   Kategóriába sorolt, kritikus megállapítások előforduló ismert problémákat, amelyek teljesítményproblémákat okozhatnak.

-   Eredmények kategóriába sorolt, fontos jelentik nem optimális konfigurációkra nem feltétlenül teljesítményproblémákat okozhatnak.

-   Kategóriába sorolt, tájékoztató megállapítások informatív utasítás csak olyan.

Tekintse át a javaslatok és az összes kritikus fontosságú és fontos megállapítások a eredményeit, és hogyan azok befolyásolhatják a teljesítményt és ajánlott eljárások a teljesítményre optimalizált konfigurációk kapcsolatos részletes adatok mutató hivatkozásokat.

### <a name="storage-tab"></a>Tárolás lap

A **megállapítások** szakasz jelenít meg a különböző megállapítások és tárolással kapcsolatos javaslatokat.

A **DiskMap** és **VolumeMap** szakaszok ismertetik a kettős perspektíva hogyan logikai kötetek és a fizikai lemezek kapcsolódnak egymáshoz.

A fizikai lemez perspektíva (DiskMap) a táblázat összes logikai kötetet a lemezen futtató. A következő példában PhysicalDrive2 több partícióra (J és H) létrehozott két logikai kötetek fut:

![az adatok lapon](media/how-to-use-perfInsights/disktab.png)

A kötet perspektíva (*VolumeMap*), a táblázatok bemutatják az egyes logikai kötet összes fizikai lemez. Figyelje meg, hogy a RAID/dinamikus lemez, futtassa az logikai kötet több fizikai lemezek esetén. A következő mintában *C:\\csatlakoztatási* konfigurálva, a csatlakozási pont *SpannedDisk* a PhysicalDisks \#2 és \#3:

![kötet lap](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL lap

Ha a cél virtuális gép bármely SQL Server-példányokat futtatja, megjelenik egy lap nevű jelentésben **SQL**:

![SQL lap](media/how-to-use-perfInsights/sqltab.png)

Ez a szakasz tartalmaz egy "Megállapítások" lapon és a további sub lapon minden, a virtuális Gépen futó SQL Server-példánya.

A "Megállapítások" fülre az SQL listáját tartalmazza kapcsolatos teljesítményproblémák található, valamint a javaslatokat.

A következő példában *PhysicalDrive0* (fut a C meghajtó) jelenik meg, mivel mind a *modeldev* és *modellog* fájlok találhatók, a C meghajtó, és azok különböző típusú (például adatfájl és a tranzakciós napló rendre):

![LogInfo](media/how-to-use-perfInsights/loginfo.png)

Az SQL Server példányspecifikus lapokat tartalmaz egy általános szakaszt, amely a kijelölt példányhoz alapvető információkat jelenít meg, és további szakaszaiban speciális tudnivalókat – többek között a beállításait, a konfiguráció és a felhasználói beállítások.

### <a name="diagnostic-tab"></a>Diagnosztikai lap
Diagnosztikai lap felső CPU kapcsolatos információkat tartalmaz, a lemez- és memória fogyasztók PerfInsights időtartama lapján futtassa. Megtalálhat más hasznos információk, például a kritikus javítások, hogy a rendszer hiányoznak, feladatlista és fontos rendszer események. 

## <a name="references-to-the-external-tools-used"></a>A külső eszközök használt mutató hivatkozások

### <a name="diskspd"></a>A Diskspd

A DISKSPD egy olyan tárolási terhelés generator és teljesítmény vizsgálati eszköz a a Windows és Windows Server és Cloud kiszolgálói infrastruktúra mérnöki csapat. További információkért lásd: [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Következő helyen

Következő helyen egy olyan parancssori eszköz a Windows-teljesítmény eszközök Kit nyomok rögzítését.

További információkért lásd: [Windows Performance Toolkit – a következő helyen](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Következő lépések

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>Diagnosztikai naplók és jelentések feltöltése a Microsoft Support további ellenőrzésre

Használatakor a Microsoft Support csapattal, a lehetséges, hogy kérelmet továbbítja a kimenetet, hogy segítse a hibaelhárítást PerfInsights által generált.

A támogatási ügynök DTM munkaterület hozza létre, és e-mailt, amely tartalmaz egy hivatkozást fog kapni a [DTM portal (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm) egyedi felhasználói azonosító és jelszó.

Az üzenet fog érkezni a **CTS automatikus diagnosztika szolgáltatás** (ctsadiag@microsoft.com).

![Az üzenet minta](media/how-to-use-perfInsights/supportemail.png)

A fokozott biztonság érdekében meg kell az első használatkor a jelszó módosítása.

DTM jelentkezik be, miután feltöltése párbeszédpanel megtalálja a **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip** PerfInsights által összegyűjtött fájl.
