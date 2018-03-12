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
ms.openlocfilehash: ee8334cbe9256b7a5ecd5e96afa2f15d6389afa8
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-use-perfinsights"></a>PerfInsights használata 

[PerfInsights](http://aka.ms/perfinsightsdownload) önsegítő diagnosztikai eszköz, amely gyűjti és elemzi a diagnosztikai adatok és készít egy jelentést, a Windows Azure virtuális gép teljesítményproblémákat hibaelhárítás elősegítése érdekében. PerfInsights futtatható virtuális gépek önálló eszközként, illetve közvetlenül a portál telepítésével [Azure teljesítmény Diagnostics Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md).

Ha a virtuális gépek teljesítményproblémákat tapasztal előtt lépjen kapcsolatba az ügyfélszolgálattal, az eszköz futtatásához.

## <a name="supported-troubleshooting-scenarios"></a>Hibaelhárítási forgatókönyveket támogatja.

PerfInsights is összegyűjti és elemzi az adatokat több különböző. Az alábbiakban gyakori helyzetek foglalkozik.

### <a name="collect-basic-configuration"></a>Alapszintű konfigurációs gyűjtése 

Ebben a forgatókönyvben gyűjti a lemezkonfigurációt és más fontos információkat, beleértve:

-   Eseménynaplók

-   Az összes bejövő és kimenő kapcsolatok hálózati állapota

-   Hálózati és tűzfalbeállításokat konfigurációs beállításai

-   A rendszer a jelenleg futó összes alkalmazás feladatlista

-   Microsoft SQL Server adatbázis-konfigurációs beállításokat (Ha a virtuális gép ki van jelölve egy SQL Servert futtató kiszolgáló)

-   Tárolási megbízhatóság számlálók

-   Fontos Windows-gyorsjavítások

-   Telepített fájlrendszerszűrő-illesztőprogramok

Ez a passzív gyűjteménye, amely nem befolyásolja a rendszer. 

>[!Note]
>Ebben a forgatókönyvben automatikusan megtalálható minden, az alábbi esetekben:

### <a name="benchmarking"></a>Benchmarking

Ez a forgatókönyv futtatása a [Diskspd](https://github.com/Microsoft/diskspd) teljesítményteszt (iops-érték és MB/s) a virtuális géphez csatolt összes merevlemezén. 

> [!Note]
> Ez a forgatókönyv befolyásolhatja a rendszer, és egy éles rendszeren nem futtatható. Ha szükséges, futtassa a ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt teszt által okozott nagyobb munkaterhelést kedvezőtlen hatással lehet a virtuális gép teljesítményére.
>

### <a name="slow-vm-analysis"></a>Lassú VM elemzés 

Ez a forgatókönyv futtatása egy [teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) nyomkövetési a számlálók a RuleEngineConfig.json fájlban megadott használatával. Ha a virtuális gép ki van jelölve egy SQL Servert futtató kiszolgáló, a teljesítmény-számláló nyomkövetési fut. Igen, a számlálókat, amelyek szerepelnek a RuleEngineConfig.json fájl használatával. Ez az eset tartalmazza a teljesítmény diagnosztikai adatokat is.

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
| IO-méret      | Avg. Bájtok/kérelem       |
|              | Avg. Bájtos, Olvasás               |
|              | Avg. Bájt írása              |
| Teljesítmény   | Adatok bájtok/s                |
|              | Olvasott bájt/mp                |
|              | Írási bájtok/s               |
| Várólista hossza | Avg. Olvasási várólistájának hossza        |
|              | Avg. Írni a várólista hossza       |
|              | Avg. Várólista hossza        |

### <a name="custom-slow-vm-analysis"></a>Egyéni lassú VM elemzés 

Egy egyéni lassú VM elemzés futtatásakor párhuzamos nyomkövetések választja. Ha azt szeretné, hogy futtathassa őket az összes (teljesítményszámláló, következő helyen, hálózati és StorPort).  

> [!Note]
> Ez a forgatókönyv befolyásolhatja a rendszer, és egy éles rendszeren nem futtatható. Ha szükséges, futtassa a ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt teszt által okozott nagyobb munkaterhelést kedvezőtlen hatással lehet a virtuális gép teljesítményére.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Milyen típusú adatokat gyűjtött PerfInsights?

Windows virtuális gép, lemezt vagy tárolási készletek konfigurációt, teljesítményszámlálóival, információkat naplózza, és különböző nyomkövetéseket. A teljesítmény forgatókönyv módjától függ. Az alábbi táblázat részletesen:

|Összegyűjtött adatok                              |  |  | Teljesítmény-forgatókönyvek |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Alapszintű konfigurációs gyűjtése | Benchmarking | Lassú VM elemzés | Az Azure fájlok elemzés | Egyéni lassú VM elemzés |
| Eseménynapló információk       | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Rendszerinformáció                | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Kötet térkép                        | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Lemez térkép                          | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Futó tevékenységek                     | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tárolási megbízhatóság számlálók      | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Szolgáltatás adatai               | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Fsutil kimeneti                     | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Szűrő-illesztőprogram adatai                | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Netstat kimeneti                    | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Hálózati konfiguráció             | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tűzfal-konfiguráció            | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| SQL Server configuration          | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Teljesítmény diagnosztikai nyomkövetési *  | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Teljesítmény-számláló nyomkövetési **      |                            |                                    | Igen                      |                      | Igen                  |
| SMB-számláló nyomkövetési **              |                            |                                    |                          | Igen                  |                      |
| SQL Server számláló nyomkövetési **       |                            |                                    | Igen                      |                      | Igen                  |
| Nyomkövetési a következő helyen                       |                            |                                    |                          |                      | Igen                  |
| StorPort-nyomkövetési                    |                            |                                    |                          |                      | Igen                  |
| Hálózati nyomkövetés futtatása                     |                            |                                    |                          | Igen                  | Igen                  |
| A Diskspd teljesítményteszt nyomkövetési x       |                            | Igen                                |                          |                      |                      |
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
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Az Azure-fájlok
\SMB Ügyfélmegosztások

### <a name="diskspd-benchmark-trace-"></a>A Diskspd teljesítményteszt nyomkövetési (*)
Diskspd i/o-munkaterhelés tesztek ([írási] operációsrendszer-lemez és tárolókészlet meghajtók [olvasási/írási])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Futtassa a PerfInsights eszközt a virtuális gép

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Mit kell előtt I futtassa az eszközt? 

#### <a name="tool-requirements"></a>Eszköz követelmények

-  Ez az eszköz a virtuális Gépen, amelyen a teljesítménycsökkenés oka kell futtatni. 

-  A következő operációs rendszereket támogatja: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016; Windows 8.1 és Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>A virtuális gépek éles az eszköz futtatásakor lehetséges problémák

-  Az összehasonlítási forgatókönyv vagy a "Egyéni lassú VM analysis" forgatókönyvekben, amelyek a következő helyen vagy a Diskspd használatára van konfigurálva az eszköz kedvezőtlen hatással lehet a virtuális gép teljesítményére. Ezek a forgatókönyvek nem éles környezetben kell futtatni.

-  Az összehasonlítási forgatókönyv vagy a "Custom lassú VM analysis" forgatókönyvekben, amelyek a Diskspd használatára van konfigurálva győződjön meg arról, hogy nincs más háttértevékenység gátolja az i/o-munkaterhelések.

-  Alapértelmezés szerint az eszköz használja az ideiglenes tárolási meghajtó adatainak gyűjtéséről. Ha nyomkövetése engedélyezve van a hosszabb ideig marad, előfordulhat, hogy a gyűjtött adatok mennyisége megfelelő. Ez csökkentheti a ideiglenes lemezterület rendelkezésre állását, és így hatással lehet minden olyan alkalmazás, amely a meghajtó támaszkodik.

### <a name="how-do-i-run-perfinsights"></a>Hogyan futtathatok PerfInsights? 

Telepítse a virtuális gépen PerfInsights is futtathatja [Azure teljesítmény Diagnostics Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md). Akkor is futtatható önálló eszközként. 

**Telepítse, PerfInsights futtassa az Azure-portálon**

A beállítással kapcsolatos további információkért lásd: [telepítése Azure teljesítmény Diagnostics Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md#install-the-extension).  

**Futtassa a PerfInsights önálló módban**

Ha a PerfInsights eszközt, kövesse az alábbi lépéseket:


1. Töltse le [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. A PerfInsights.zip fájl feloldása. Ehhez kattintson a jobb gombbal a PerfInsights.zip fájlt, és válassza ki **tulajdonságok**. Az a **általános** lapon jelölje be **Unblock**, majd válassza ki **OK**. Ez biztosítja, hogy az eszköz fut-e bármilyen további biztonsági kérdések nélkül.  

    ![Képernyőfelvétel a PerfInsights tulajdonság, oldja fel a kijelölt](media/how-to-use-perfInsights/unlock-file.png)

3.  Bontsa ki a tömörített PerfInsights.zip fájl az ideiglenes meghajtóba (alapértelmezés szerint ez az általában a D meghajtó). 

4.  Nyissa meg a Windows parancssort rendszergazdaként, és futtassa a PerfInsights.exe a rendelkezésre álló parancssori paraméterek megjelenítéséhez.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Képernyőfelvétel a PerfInsights commandline kimeneti](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    Az alapszintű futtató PerfInsights forgatókönyvek szintaxisa a következő:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Használhatja az alábbi példában lassú VM-forgatókönyvet az 5 perc futtatásához:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Az alábbi példa használatával futtassa a következő helyen és a teljesítmény számláló megjelenik az 5 perc az egyéni forgatókönyv:
    
    ```
    PerfInsights /run custom xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Megtekintheti a rendelkezésre álló forgatókönyvek és a beállítások segítségével a **/list** parancs:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Mielőtt futtatná az olyan forgatókönyvekben, PerfInsights felszólítja a felhasználót elfogadja a diagnosztikai információkat, és elfogadja a végfelhasználói LICENCSZERZŐDÉST. Használjon **/AcceptDisclaimerAndShareDiagnostics** kapcsolóval kihagyhatja ezeket az utasításokat. 
    >
    >Ha egy aktív támogatási jegy, a Microsoft és a támogatási szakember dolgozunk a kérelmenként futó PerfInsights, ügyeljen arra, hogy adja meg a támogatási jegy szám használatával a **/sr** lehetőséget.
    >
    >Alapértelmezés szerint a PerfInsights próbálkozik, a legújabb verzióra történő frissítése magát, ha elérhető. Használjon **/SkipAutoUpdate** vagy **/sau** automatikus frissítés kihagyását paraméter.  
    >
    >Ha a duration kapcsoló **/d** nincs megadva, PerfInsights figyelmezteti, hogy Reprodukálja a hibát vmslow, azurefiles és egyéni esetek futtatása során. 

A nyomkövetési adatokat vagy a műveletek befejezése után egy új fájl megtalálható-e D:\\napló\_gyűjtemény (vagy az ideiglenes meghajtón). A fájl neve **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip.** Ezt a fájlt küldeni a támogatási ügynök elemzés, vagy nyissa meg a jelentés megállapítások és javaslatok tekintse át a zip-fájlt.

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

### <a name="sql-tab"></a>SQL tab

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

### <a name="diskspd"></a>Diskspd

A Diskspd egy olyan tárolási terhelés generator és teljesítmény vizsgálati eszköz a Microsoft. További információkért lásd: [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Következő helyen rögzíthesse a nyomkövetési adatokat a Windows Performance Toolkit a parancssori eszköz. További információkért lásd: [Windows Performance Toolkit – a következő helyen](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>További lépések

Feltöltheti diagnosztikai naplók és jelentések a Microsoft Support további ellenőrzésre. Támogatási kérheti, hogy a kimenet a felderítését és elhárítását segítő PerfInsights által generált továbbítja.

Az alábbi képernyőfelvételen egy üzenetet jelenít meg hasonló mi akkor fordulhat elő:

![A Microsoft Support mintaüzenet képernyőképe](media/how-to-use-perfInsights/supportemail.png)

Kövesse az utasításokat a fájl adatátviteli munkaterület eléréséhez az üzenetben. A fokozott biztonság érdekében kell módosítani a jelszavát, az első használatkor.

Miután bejelentkezik, talál egy párbeszédpanel, töltse fel a **CollectedData\_éééé-hh-nn\_hh\_mm\_ss.zip** PerfInsights által összegyűjtött fájl.
