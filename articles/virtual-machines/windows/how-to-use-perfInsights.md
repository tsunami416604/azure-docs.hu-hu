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
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>PerfInsights használata 

[PerfInsights](http://aka.ms/perfinsightsdownload) egy automatizált parancsfájl, amely a hasznos diagnosztikai adatokat gyűjt. Emellett i/o-magas terhelés terhelések fut, és megoldhatja a Windows Azure virtuális gép teljesítményproblémákat elemzési jelentéseket biztosít. Ez a virtuális gépek önálló parancsfájlként, vagy futtatható közvetlenül a portálról telepítésével [Azure teljesítmény Diagnostics Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md).

Ha a virtuális gépek teljesítményproblémákat tapasztal előtt lépjen kapcsolatba az ügyfélszolgálattal, futtassa ezt a parancsfájlt.

## <a name="supported-troubleshooting-scenarios"></a>Hibaelhárítási forgatókönyveket támogatja.

PerfInsights is összegyűjti és elemzi az adatokat több különböző. Az alábbiakban gyakori helyzetek foglalkozik.

### <a name="collect-basic-configuration"></a>Alapszintű konfigurációs gyűjtése 

Ebben a forgatókönyvben gyűjti a lemezkonfigurációt és más fontos információkat, beleértve:

-   Eseménynaplók

-   Az összes bejövő és kimenő kapcsolatok hálózati állapota

-   Hálózati és tűzfalbeállításokat konfigurációs beállításai

-   A rendszer a jelenleg futó összes alkalmazás feladatlista

-   A virtuális gép msinfo32 által létrehozott információs fájl

-   Microsoft SQL Server adatbázis-konfigurációs beállításokat (Ha a virtuális gép ki van jelölve egy SQL Servert futtató kiszolgáló)

-   Tárolási megbízhatóság számlálók

-   Fontos Windows-gyorsjavítások

-   Telepített fájlrendszerszűrő-illesztőprogramok

Ez a passzív gyűjteménye, amely nem befolyásolja a rendszer. 

>[!Note]
>Ebben a forgatókönyvben az alábbi esetek mindegyikében automatikusan tartalmazza.

### <a name="benchmarking"></a>Teljesítménymérésre

Ez a forgatókönyv futtatása a [Diskspd](https://github.com/Microsoft/diskspd) teljesítményteszt (iops-érték és MB/s) a virtuális géphez csatolt összes merevlemezén. 

> [!Note]
> Ez a forgatókönyv befolyásolhatja a rendszer, és egy éles rendszeren nem futtatható. Ha szükséges, futtassa a ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt teszt által okozott nagyobb munkaterhelést kedvezőtlen hatással lehet a virtuális gép teljesítményére.
>

### <a name="slow-vm-analysis"></a>Lassú VM elemzés 

Ez a forgatókönyv futtatása egy [teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) nyomkövetési a számlálók a Generalcounters.txt fájlban megadott használatával. Ha a virtuális gép az SQL Servert futtató kiszolgálónak, a teljesítmény-számláló nyomkövetési futása. A számlálókat, amelyek szerepelnek a Sqlcounters.txt fájl használatával ilyeneket, és diagnosztika teljesítményadatokat is tartalmazza.

### <a name="slow-vm-analysis-and-benchmarking"></a>Lassú VM elemzés és teljesítménymérésre

Ez a forgatókönyv futtatása egy [teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) nyomkövetési elé egy [Diskspd](https://github.com/Microsoft/diskspd) teljesítményteszt. 

> [!Note]
> Ez a forgatókönyv befolyásolhatja a rendszer, és egy éles rendszeren nem futtatható. Ha szükséges, futtassa a ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt teszt által okozott nagyobb munkaterhelést kedvezőtlen hatással lehet a virtuális gép teljesítményére.
>

### <a name="azure-files-analysis"></a>Az Azure fájlok elemzés 

Ebben a forgatókönyvben egy különleges teljesítmény számláló rögzítési együtt a hálózati nyomkövetés fut. A rögzítés minden a Server Message Block (SMB) ügyfél megosztások számlálókat tartalmaz. Az alábbiakban néhány kulcsfontosságú SMB ügyfél megosztás teljesítményszámlálók a rögzítés részét képező:

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

Egy egyéni lassú VM elemzés futtatásakor párhuzamos nyomkövetések választja. Hogy futtathassa őket minden (teljesítményszámláló, következő helyen, hálózati és StorPort) Ha azt szeretné. Nyomkövetés befejezése után az eszköz a Diskspd teljesítményteszt fut, ha az van kiválasztva. 

> [!Note]
> Ez a forgatókönyv befolyásolhatja a rendszer, és egy éles rendszeren nem futtatható. Ha szükséges, futtassa a ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt teszt által okozott nagyobb munkaterhelést kedvezőtlen hatással lehet a virtuális gép teljesítményére.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Milyen típusú adatokat a parancsfájl által összegyűjtött?

Windows virtuális gép, lemezt vagy tárolási készletek konfigurációt, teljesítményszámlálóival, információkat naplózza, és különböző nyomkövetéseket. A teljesítmény forgatókönyv módjától függ. Az alábbi táblázat részletesen:

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
| Teljesítmény-számláló nyomkövetési **     |                            |                                    |                          |                                |                      | Igen                  |
| SMB-számláló nyomkövetési **             |                            |                                    |                          |                                | Igen                  |                      |
| SQL Server számláló nyomkövetési **      |                            |                                    |                          |                                |                      | Igen                  |
| Nyomkövetési a következő helyen                      |                            |                                    |                          |                                |                      | Igen                  |
| StorPort-nyomkövetési                   |                            |                                    |                          |                                |                      | Igen                  |
| Hálózati nyomkövetés futtatása                    |                            |                                    |                          |                                | Igen                  | Igen                  |
| A Diskspd teljesítményteszt nyomkövetési ***      |                            | Igen                                |                          | Igen                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Teljesítmény-diagnosztikai nyomkövetési (*)

Az adatok gyűjtéséhez és folyamatos teljesítményt eseményadatokat a háttérben fut, egy szabályalapú motor. A következő szabályok jelenleg támogatottak:

- HighCpuUsage szabály: magas CPU-használat időszakok észleli, és a felső CPU-használat fogyasztók időszak során tartalmazza.
- HighDiskUsage szabály: a fizikai lemezek magas lemez időszakai észleli, és bemutatja a felső lemez használati fogyasztók ezen időszak alatt.
- HighResolutionDiskMetric szabály: látható IOPS, az átviteli sebesség és az i/o késés metrikák / minden fizikai lemez 50 milliszekundumban megadva. Ennek segítségével gyorsan azonosíthatja a sávszélesség-szabályozás időszakok lemez.
- HighMemoryUsage szabály: felső memóriaterület időszakai észleli, és bemutatja a felső memória használati fogyasztók ezen időszak alatt.

> [!NOTE] 
> Jelenleg a Windows, amely tartalmazza a .NET-keretrendszer 3.5-ös vagy későbbi verziói támogatottak.

### <a name="performance-counter-trace-"></a>Teljesítmény-számláló nyomkövetési (*)

Gyűjti a következő teljesítményszámlálókkal:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk és \LogicalDisk
- \Cache\Dirty lapok, \Cache\Lazy írási ürítések másodpercenkénti \Server\Pool lapozható, hibákat, és a lapozható \Server\Pool hibák
- Kijelölt számlálók alatt \Network felületet, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ Hálózati QoS Policy\Packets \Per hálózati illesztő kártya processzortevékenység és \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>SQL Server-példányok
- \SQL server: pufferkezelőnek, \SQLServer:Resource készlet statisztikák és \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, statisztikák
- \SQLServer:Access módszerek

#### <a name="for-azure-files"></a>Az Azure-fájlok
\SMB Ügyfélmegosztások

### <a name="diskspd-benchmark-trace-"></a>A Diskspd teljesítményteszt nyomkövetési (*)
Diskspd i/o-munkaterhelés tesztek ([írási] operációsrendszer-lemez és tárolókészlet meghajtók [olvasási/írási])

## <a name="run-the-perfinsights-script-on-your-vm"></a>Futtassa a PerfInsights parancsfájlt a virtuális gép

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Mi van szükségem a parancsfájl futtatása előtt? 

#### <a name="script-requirements"></a>Parancsfájl-követelmények

-  Ezt a parancsfájlt, amely rendelkezik a teljesítménycsökkenés oka a virtuális gépen kell futnia. 

-  A következő operációs rendszereket támogatja: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016; Windows 8.1 és Windows 10.

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>A virtuális gépek éles a parancsprogram futtatásakor lehetséges problémák

-  Minden összehasonlítási forgatókönyvet vagy a "Egyéni lassú VM analysis" forgatókönyvekben, amelyek a következő helyen vagy a Diskspd használatára van konfigurálva a parancsfájl kedvezőtlen hatással lehet a virtuális gép teljesítményére. Ezek a forgatókönyvek nem szabad termelési környezetben.

-  Bármely összehasonlítási esetek vagy az "Egyéni lassú VM analysis" forgatókönyvekben, amelyek a Diskspd használatára van konfigurálva győződjön meg arról, hogy nincs más háttértevékenység gátolja az i/o-munkaterhelések.

-  Alapértelmezés szerint a parancsfájl az ideiglenes tárolási meghajtó adatainak gyűjtéséről. Ha nyomkövetése engedélyezve van a hosszabb ideig marad, előfordulhat, hogy a gyűjtött adatok mennyisége megfelelő. Ez csökkentheti a ideiglenes lemezterület rendelkezésre állását, és így hatással lehet minden olyan alkalmazás, amely a meghajtó támaszkodik.

### <a name="how-do-i-run-perfinsights"></a>Hogyan futtathatok PerfInsights? 

Telepítse a virtuális gépen PerfInsights is futtathatja [Azure teljesítmény Diagnostics Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md). Akkor is futtatható önálló parancsfájlként. 

**Telepítse, PerfInsights futtassa az Azure-portálon**

A beállítással kapcsolatos további információkért lásd: [telepítése Azure teljesítmény Diagnostics Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md#install-the-extension).  

**Önálló módban PerfInsights parancsfájl futtatása**

A parancsfájl futtatásához a PerfInsights kövesse az alábbi lépéseket:


1. Töltse le [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. A PerfInsights.zip fájl feloldása. Ehhez kattintson a jobb gombbal a PerfInsights.zip fájlt, és válassza ki **tulajdonságok**. Az a **általános** lapon jelölje be **Unblock**, majd válassza ki **OK**. Ez biztosítja, hogy a parancsfájl futtatása nélkül más biztonsági kérdések.  

    ![Képernyőfelvétel a PerfInsights tulajdonság, oldja fel a kijelölt](media/how-to-use-perfInsights/unlock-file.png)

3.  Bontsa ki a tömörített PerfInsights.zip fájl az ideiglenes meghajtóba (alapértelmezés szerint ez az általában a D meghajtó). A tömörített fájlt tartalmaznia kell a következő fájlok és mappák:

    ![A zip-mappában található fájlokat képernyőképe](media/how-to-use-perfInsights/file-folder.png)

4.  Nyissa meg a Windows Powershellt rendszergazdaként, és futtassa a PerfInsights.ps1 parancsfájl.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Lehetséges, hogy az "y" annak megerősítéséhez, hogy a végrehajtási házirend módosításához adja meg.

    Az a **figyelje meg, és hozzájárul** párbeszédpanel, lehetősége van a Microsoft Support diagnosztikai információk megosztását. Beleegyezik kell is, a folytatáshoz a licencszerződés feltételeit. Adja meg a beállításokat, majd válassza ki **-parancsfájl futtatása**.

    ![Képernyőkép az értesítés és a hozzájárulásukat adják párbeszédpanel](media/how-to-use-perfInsights/disclaimer.png)

5.  Küldje el a nagybetűk, amelyet, amennyiben az rendelkezésre áll, a parancsfájl futtatásakor. Válassza ki **OK**.
    
    ![Támogatási azonosító párbeszédpanel képernyőképe](media/how-to-use-perfInsights/enter-support-number.png)

6.  Válassza ki az átmeneti tárolási meghajtót. A parancsfájl is automatikus észlelés a meghajtó betűjele. Ebben a szakaszban olyan probléma lép fel, ha a program kérheti válassza ki a meghajtót, (az alapértelmezett meghajtón D). Létrehozott naplók vannak tárolva, a napló\_gyűjtemény mappát. Adja meg, vagy fogadja el a meghajtóbetűjelet után válassza ki **OK**.

    ![Ideiglenes meghajtó párbeszédpanel képernyőképe](media/how-to-use-perfInsights/enter-drive.png)

7.  A hibaelhárítási választása a megadott listáról.

       ![Képernyőkép a hibaelhárítás forgatókönyvek listája](media/how-to-use-perfInsights/select-scenarios.png)

PerfInsights felhasználói felület nélkül is futtathatja. A következő parancsot futtatja a felhasználói felület nélkül forgatókönyv hibaelhárítási "lassú VM analysis". A rendszer kérni fogja, hogy ugyanazon jogi nyilatkozat és a 4. lépésben említett EULA hozzájárulás.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

Ha azt szeretné, hogy PerfInsights csendes módban való futásra, használja a **- AcceptDisclaimerAndShareDiagnostics** paraméter. Például a következő paranccsal:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Hogyan hibáinak elhárítása problémák a parancsprogram futtatása közben?

Ha a parancsfájl rendellenesen áll, térhet vissza konzisztens állapotú legyen a tisztítás kapcsoló együtt a parancsfájl futtatásával az alábbiak szerint:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Ha problémák fordulnak elő során az ideiglenes meghajtón automatikus észlelését, a program kérheti válassza ki a meghajtót, (az alapértelmezett meghajtón D).

![Ideiglenes meghajtó párbeszédpanel képernyőképe](media/how-to-use-perfInsights/enter-drive.png)

A parancsfájl a segédeszközök eltávolítja, és eltávolítja az ideiglenes mappát.

### <a name="troubleshoot-other-script-issues"></a>Más parancsfájl elhárítása 

Ha bármilyen probléma akkor fordulhat elő, ha a parancsfájl futtatása, nyomja le a Ctrl + C billentyűkombinációval megszakítási a parancsfájl. Parancsfájl hiba tapasztalható még több próbálkozást követően továbbra is, ha a parancsfájl hibakeresési módban használatával futtassa a "-Debug" paraméter indításakor.

A hiba akkor fordul elő, miután a PowerShell-konzol teljes kimenetének másolása HTML-, és elküldi a Microsoft Support ügynök segít a hibaelhárítás elősegítése érdekében.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>A parancsfájl futtatása "Egyéni lassú VM analysis" módban

Kiválasztásával a **egyéni lassú VM elemzés**, engedélyezheti a több nyomkövetések párhuzamosan (több nyomkövetések választáshoz használja a Shift billentyűt):

![Képernyőkép a forgatókönyvek listája](media/how-to-use-perfInsights/select-scenario.png)

A teljesítmény számláló nyomkövetési, a következő helyen nyomkövetési, a hálózati nyomkövetés vagy a Storport-nyomkövetési forgatókönyvek kiválasztásakor kövesse a következő párbeszédpanelen. Próbálja meg újra a teljesítményromlást előállítani, a nyomkövetések elindítása után.

A következő párbeszédpanel a nyomkövetés indítása:

![Képernyőfelvétel a Start teljesítmény számláló nyomkövetési párbeszédpanel](media/how-to-use-perfInsights/start-trace-message.png)

Leállítja a nyomkövetési adatokat, hogy a parancs második párbeszédpanelen.

![Képernyőkép az leállítása teljesítmény számláló nyomkövetési párbeszédpanel](media/how-to-use-perfInsights/stop-trace-message.png)
![Képernyőkép az leállítása az összes hívásláncainak párbeszédpanel](media/how-to-use-perfInsights/ok-trace-message.png)

A nyomkövetési adatokat vagy a műveletek befejezése után egy új fájl megtalálható-e D:\\napló\_gyűjtemény (vagy az ideiglenes meghajtón). A fájl neve **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip.** Ez a fájl küldhet a támogatási ügynök elemzés céljából.

## <a name="review-the-diagnostics-report"></a>Tekintse át a diagnosztikai jelentés

Belül a **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip** fájlt, egy HTML-jelentést, amely leírja, PerfInsights eredményei találja. Tekintse át a jelentést, bontsa ki a **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip** fájlt, és nyissa meg a **PerfInsights diagram jelentés.HTML** fájlt.

Válassza ki a **megállapítások** fülre.

![Képernyőkép a PerfInsights jelentés](media/how-to-use-perfInsights/findingtab.png)
![PerfInsights jelentés képernyőképe](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Kategóriába sorolt, kritikus megállapítások előforduló ismert problémákat, amelyek teljesítményproblémákat okozhatnak. Eredmények kategóriába sorolt, fontos jelentik nem optimális konfigurációkra nem feltétlenül teljesítményproblémákat okozhatnak. Kategóriába sorolt, tájékoztató megállapítások informatív utasítás csak olyan.

Tekintse át a javaslatok és az összes kritikus fontosságú és fontos megállapítások mutató hivatkozásokat. Tudnivalók arról, hogy azok hatással teljesítmény és gyakorlati tanácsokat teljesítményre optimalizált konfigurációk.

### <a name="storage-tab"></a>Tárolás lap

A **megállapítások** szakasz jelenít meg a különböző megállapítások és tárolással kapcsolatos javaslatokat.

A **lemez térkép** és **kötet térkép** szakaszok azt ismertetik, hogyan logikai kötetek és a fizikai lemezek kapcsolódnak egymáshoz.

A fizikai lemez perspektíva (lemez térkép) a táblázat összes logikai kötetet a lemezen futtató. A következő példában **PhysicalDrive2** több partícióra (J és H) létrehozott két logikai kötetek fut:

![Képernyőkép a lemez lap](media/how-to-use-perfInsights/disktab.png)

A kötet perspektíva (kötet térkép) a táblázatok minden logikai mennyiségi összes fizikai lemez. Figyelje meg, hogy a RAID/dinamikus lemez, előfordulhat, hogy több fizikai lemezére logikai kötet futtatja. A következő példában *C:\\csatlakoztatási* egy csatlakoztatási pontot tanúsítólemezként *SpannedDisk* fizikai lemezek 2 és 3:

![Képernyőkép a kötet lap](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL lap

Ha a cél virtuális gép bármely SQL Server-példányokat futtatja, megjelenik egy lap a nevű jelentésben **SQL**:

![Képernyőkép az SQL-lap](media/how-to-use-perfInsights/sqltab.png)

Ez a szakasz egy **megállapítások** fülre, és további lapon minden, a virtuális Gépen futó SQL Server-példánya.

A **megállapítások** lapon az összes SQL-listát tartalmaz kapcsolatos teljesítményproblémák található, valamint a javaslatokat.

A következő példában **PhysicalDrive0** (fut a C meghajtó) jelenik meg. Ennek az az oka mind a **modeldev** és **modellog** fájlok találhatók, a C meghajtó, és azok különböző típusú (adatok fájl és a tranzakciós napló, például rendre).

![Képernyőkép a naplóadatok](media/how-to-use-perfInsights/loginfo.png)

Az adott SQL Server-példányok lapokat tartalmaz egy általános szakaszt, amely a kijelölt példányhoz alapvető információkat jelenít meg. A lapok is tartalmaz további szakaszaiban speciális tudnivalókat – többek között a beállításait, a konfiguráció és a felhasználói beállítások.

### <a name="diagnostic-tab"></a>Diagnosztikai lap
A **diagnosztikai** lapon információt tartalmaz felső CPU, a lemez és a memória fogyasztók azon a számítógépen futó időtartama PerfInsights. Akkor is megismerheti kritikus javításokat, hogy a rendszer hiányzik, a feladatlistában és fontos rendszeresemények lehet-e. 

## <a name="references-to-the-external-tools-used"></a>A külső eszközök használt mutató hivatkozások

### <a name="diskspd"></a>A Diskspd

A Diskspd egy olyan tárolási terhelés generator és teljesítmény vizsgálati eszköz a Microsoft. További információkért lásd: [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Következő helyen

Következő helyen rögzíthesse a nyomkövetési adatokat a Windows Performance Toolkit a parancssori eszköz. További információkért lásd: [Windows Performance Toolkit – a következő helyen](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Következő lépések

Feltöltheti diagnosztikai naplók és jelentések a Microsoft Support további ellenőrzésre. Támogatási kérheti, hogy a kimenet a felderítését és elhárítását segítő PerfInsights által generált továbbítja.

Az alábbi képernyőfelvételen egy üzenetet jelenít meg hasonló mi akkor fordulhat elő:

![A Microsoft Support mintaüzenet képernyőképe](media/how-to-use-perfInsights/supportemail.png)

Kövesse az utasításokat a fájl adatátviteli munkaterület eléréséhez az üzenetben. A fokozott biztonság érdekében kell módosítani a jelszavát, az első használatkor.

Miután bejelentkezik, talál egy párbeszédpanel, töltse fel a **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip** PerfInsights által összegyűjtött fájl.
