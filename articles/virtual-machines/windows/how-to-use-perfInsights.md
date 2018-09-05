---
title: A PerfInsights használata a Microsoft Azure-ban |} A Microsoft Docs
description: A PerfInsights használata Windows VM teljesítménnyel kapcsolatos hibák elhárítására megtanulni.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 1f3a24cebe5061f7e3ca3897692b068531780431
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43668121"
---
# <a name="how-to-use-perfinsights"></a>A PerfInsights használata

[A PerfInsights](http://aka.ms/perfinsightsdownload) önsegítő diagnosztikai eszköz, amely gyűjti és elemzi a diagnosztikai adatokat, és jelentést készít az Azure-beli Windows virtuális gép teljesítmény kapcsolatos problémák elhárítása érdekében. A PerfInsights futtatni a virtuális gépek önálló eszközként, vagy közvetlenül a portálon telepítésével [Azure teljesítménydiagnosztikai Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md).

Ha teljesítményproblémákat okozhat a virtuális gépeket, mielőtt kapcsolatba lépne a támogatási tapasztalja, futtassa ezt az eszközt.

## <a name="supported-troubleshooting-scenarios"></a>Támogatott hibaelhárítási forgatókönyvek

A PerfInsights összegyűjtheti, és elemezheti a különféle információkat. Az alábbiakban gyakori forgatókönyveket fednek le.

### <a name="quick-performance-analysis"></a>Gyors teljesítmény elemzése

Ebben a forgatókönyvben gyűjti a lemezkonfiguráció és egyéb fontos információkat, beleértve:

-   Eseménynaplók

-   Az összes bejövő és kimenő kapcsolatok hálózati állapota

-   Hálózati és tűzfalbeállításokat konfigurációs beállításai

-   A rendszer a jelenleg futó összes alkalmazás feladatlista

-   A Microsoft SQL Server adatbázis konfigurációs beállításai (Ha a virtuális gép ki van jelölve egy SQL Servert futtató kiszolgáló)

-   Tárolási megbízhatóság számlálók

-   Fontos Windows-gyorsjavítások

-   Telepített fájlrendszerszűrő-illesztőprogramok

Ez a passzív gyűjtemény, amely nem lehet hatással a rendszer. 

>[!Note]
>Ebben a forgatókönyvben az alábbi esetekben minden automatikusan tartalmazza:

### <a name="benchmarking"></a>A teljesítménytesztek

Ebben a forgatókönyvben futtatja a [Diskspd](https://github.com/Microsoft/diskspd) teljesítményteszt (IOPS és MB/s) az összes olyan meghajtón, a virtuális géphez csatolt. 

> [!Note]
> Ebben a forgatókönyvben befolyásolhatja a rendszer, és a egy éles rendszeren nem futtatható. Ha szükséges, futtassa az ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. Egy nyomkövetési vagy a teljesítményteszt teszt által okozott nagyobb munkaterhelést hátrányosan befolyásolhatja a virtuális gép teljesítményét.
>

### <a name="performance-analysis"></a>Teljesítményelemzés

Ebben a forgatókönyvben fut egy [teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) nyomkövetési a számlálók a RuleEngineConfig.json fájlban megadott használatával. Ha a virtuális gép, amelyen fut az SQL Server-kiszolgálóként, futtatja a teljesítmény a számláló a nyomkövetés. Így a számlálók a RuleEngineConfig.json fájlban található használatával hajtja végre. Ebben a forgatókönyvben a diagnosztikai teljesítményadatokat is tartalmaz.

### <a name="azure-files-analysis"></a>Az Azure Files-elemzés

Ebben a forgatókönyvben egy speciális teljesítmény számláló rögzítési együtt a hálózati nyomkövetés futtatja. A rögzítési minden a Server Message Block (SMB) ügyfél megosztások számlálókat tartalmaz. Az alábbiakban néhány főbb SMB ügyfél megosztás teljesítményszámlálók, amelyek részei a rögzítést:

| **Típus**     | **Az SMB-ügyfél megosztások számláló** |
|--------------|-------------------------------|
| IO         | Adatok vonatkozó kérelmek/másodperc             |
|              | Olvasási kéréseinek száma másodpercenként             |
|              | Írási kéréseinek száma másodpercenként            |
| Késés      | Átlagos műveleti idő (mp) / kérelem         |
|              | Átlagos mp/Olvasás                 |
|              | Átlagos mp/írás                |
| I/o-mérete      | Átl. Bájt/kérelem       |
|              | Átl. Olvasott bájtok /               |
|              | Átl. Bájt/írás              |
| Teljesítmény   | Adatok bájt/mp                |
|              | Olvasási bájt/mp                |
|              | Írási bájt/mp               |
| Várólista hossza | Átl. Olvasási várólistájának hossza        |
|              | Átl. Írás a várólista hossza       |
|              | Átl. Adatok várólistájának hossza        |

### <a name="advanced-performance-analysis"></a>Speciális teljesítményelemzése

Egy speciális Teljesítményelemzés futtatásakor a nyomkövetések párhuzamos választja. Ha azt szeretné, futtathatja őket az összes (teljesítményszámláló, következő helyen, hálózati és StorPort).  

> [!Note]
> Ebben a forgatókönyvben befolyásolhatja a rendszer, és a egy éles rendszeren nem futtatható. Ha szükséges, futtassa az ebben a forgatókönyvben egy dedikált karbantartási időszakban esetleges problémák elkerülése érdekében. Egy nyomkövetési vagy a teljesítményteszt teszt által okozott nagyobb munkaterhelést hátrányosan befolyásolhatja a virtuális gép teljesítményét.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Milyen típusú információkat gyűjti a PerfInsights?

Windows virtuális gép, a lemezek vagy a tárolási készletek konfigurációt, a teljesítményszámlálók, információkat naplózza, és különböző nyomkövetéseket. Attól függ, a teljesítmény forgatókönyvet használja. Az alábbi táblázat részletesen:

|Összegyűjtött adatok                              |  |  | Teljesítmény-forgatókönyvek |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Gyors teljesítmény elemzése | A teljesítménytesztek | Teljesítményelemzés | Az Azure Files-elemzés | Speciális teljesítményelemzése |
| Eseménynaplók adatait       | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Rendszerinformáció                | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Kötet térkép                        | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Lemez térkép                          | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Futó tevékenységek                     | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tárolási megbízhatóság számlálók      | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Szolgáltatás adatai               | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Fsutil kimenet                     | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Szűrő-illesztőprogram adatai                | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Netstat parancs kimenete                    | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Hálózati konfiguráció             | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tűzfal-konfiguráció            | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| SQL Server-konfiguráció          | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Teljesítmény-diagnosztikai nyomkövetés *  | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Teljesítmény-számláló nyomkövetési **      |                            |                                    | Igen                      |                      | Igen                  |
| Az SMB-számláló nyomkövetési **              |                            |                                    |                          | Igen                  |                      |
| Az SQL Server számláló nyomkövetési **       |                            |                                    | Igen                      |                      | Igen                  |
| A következő helyen nyomkövetési                       |                            |                                    |                          |                      | Igen                  |
| StorPort-nyomkövetés                    |                            |                                    |                          |                      | Igen                  |
| Hálózati nyomkövetés                     |                            |                                    |                          | Igen                  | Igen                  |
| A Diskspd referenciaalap nyomkövetési x       |                            | Igen                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Teljesítménybeli problémák diagnosztizálása a nyomkövetési (*)

Az adatok gyűjtéséhez és a folyamatban lévő teljesítménybeli problémák diagnosztizálása a háttérben fut, egy szabályalapú motor. A következő szabályok jelenleg támogatja:

- HighCpuUsage szabály: magas CPU-használat időszakok észleli, és a felső CPU-használat fogyasztók megjelenítése az érintett időszakokban.
- HighDiskUsage szabály: időszakai méretű fizikai lemezen észleli, és bemutatja az első lemez használati fogyasztók az érintett időszakokban.
- HighResolutionDiskMetric szabály: Megjeleníti az egyes fizikai lemezek 50 ezredmásodperc IOPS, az átviteli sebesség és az i/o várakozási ideje metrika. Segít gyorsan azonosítani az időszakok szabályozás lemez.
- HighMemoryUsage szabály: magas memória időszakai észleli, és bemutatja a felső memória használati fogyasztók az érintett időszakokban.

> [!NOTE] 
> Jelenleg a Windows-verziókat, amelyek tartalmazzák a .NET-keretrendszer 4.5-ös vagy újabb verziói támogatottak.

### <a name="performance-counter-trace-"></a>Teljesítmény-számláló nyomkövetési (*)

Gyűjti a következő teljesítményszámlálókkal:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk és \LogicalDisk
- Lapok, \Cache\Lazy írási kiürítéseinek száma/s, \Server\Pool \Cache\Dirty lapozható, és a lapozható \Server\Pool hibák
- Kijelölt számlálók alatt \Network \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, felületén \ Hálózati QoS Policy\Packets \Per hálózati adapter kártya processzortevékenység és \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Az SQL Server-példányok
- \SQL server: Buffer Manager \SQLServer:Resource készlet statisztikák és \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, statisztika
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Az Azure Files szolgáltatáshoz
\SMB Ügyfélmegosztások

### <a name="diskspd-benchmark-trace-"></a>A Diskspd referenciaalap nyomkövetési (*)
A Diskspd-i/o terhelési tesztek (operációsrendszer-lemez [írási] és [olvasási/írási] készlet meghajtók)

## <a name="run-the-perfinsights-tool-on-your-vm"></a>A PerfInsights eszköz futtatásához a virtuális Gépen

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Mit kell tudni szeretnék az eszköz futtatása előtt? 

#### <a name="tool-requirements"></a>Eszköz követelményei

-  Ezzel az eszközzel kell futtatni a virtuális gépen, amely rendelkezik a teljesítménnyel kapcsolatos problémák. 

-  A következő operációs rendszerek támogatottak: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016; Windows 8.1 és Windows 10-es.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Lehetséges problémákat, amikor az eszközt futtatja az éles virtuális gépek

-  Teljesítménymérési forgatókönyv vagy a "Komplex Teljesítményelemzés" forgatókönyvet, amely a következő helyen vagy a Diskspd használatára van konfigurálva az eszköz kedvezőtlen hatással lehet a teljesítmény, a virtuális gép. Ezek a forgatókönyvek nem éles környezetben fog futni.

-  Teljesítménymérési forgatókönyv vagy a "Komplex Teljesítményelemzés" forgatókönyvet, amely a Diskspd használatára van konfigurálva győződjön meg arról, hogy nincs más háttérben futó tevékenység rontja az I/O munkaterheléstől.

-  Alapértelmezés szerint az eszköz az ideiglenes meghajtó használja a adatainak gyűjtéséről. Ha a nyomkövetés engedélyezve van a hosszabb ideig marad, az összegyűjtött adatok mennyisége hasznosak lehetnek. Csökkentheti az ideiglenes lemezen található rendelkezésre állását, és így hatással lehet a bármilyen alkalmazás, amely a meghajtó támaszkodik.

### <a name="how-do-i-run-perfinsights"></a>Hogyan működik a PerfInsights? 

Futtathatja a PerfInsights virtuális gépen történő telepítésével [Azure teljesítménydiagnosztikai Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md). Is futtathatja azt egy önálló eszközként. 

**Telepítse és futtassa a PerfInsights az Azure Portalról**

Ezzel a beállítással kapcsolatos további információkért lásd: [telepítése Azure teljesítménydiagnosztikai Virtuálisgép-bővítmény](performance-diagnostics-vm-extension.md#install-the-extension).  

**A PerfInsights futtatása a önálló módban**

A PerfInsights eszköz futtatásához kövesse az alábbi lépéseket:


1. Töltse le [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. A PerfInsights.zip fájl tiltásának feloldásához. Ehhez kattintson a jobb gombbal a PerfInsights.zip fájlt, és válassza ki **tulajdonságok**. Az a **általános** lapon jelölje be **feloldása**, majd válassza ki **OK**. Ez biztosítja, hogy az eszköz fut-e minden olyan további biztonsági kéri nélkül.  

    ![Képernyőkép a PerfInsights tulajdonságok, kiemelt feloldása](media/how-to-use-perfInsights/unlock-file.png)

3.  Bontsa ki a tömörített PerfInsights.zip be az ideiglenes meghajtó (alapértelmezés szerint ez a általában a D meghajtó). 

4.  Nyissa meg a Windows-parancssort rendszergazdaként, és futtassa a PerfInsights.exe a rendelkezésre álló parancssori paraméterek megjelenítéséhez.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Képernyőkép a PerfInsights commandline kimeneti](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    Az alapszintű futtató PerfInsights forgatókönyvek szintaxisa:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Használhatja az alábbi példában 5 perc teljesítményét elemző forgatókönyv futtatása:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Az alábbi példa használatával futtassa a következő helyen és a teljesítmény-számláló nyomkövetéseinek 5 perc a speciális forgatókönyvhöz:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Használhatja az alábbi példa futtatása teljesítményét elemző forgatókönyv 5 perc, és az eredmény zip-fájl feltöltése a storage-fiókhoz:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Kereshet a rendelkezésre álló forgatókönyveket és a beállítások használatával a **/listában** parancsot:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Egy forgatókönyvben futó, mielőtt a PerfInsights kéri a felhasználót, hogy fogadja el a diagnosztika adatok megosztása, és fogadja el a végfelhasználói LICENCSZERZŐDÉST. Használat **/AcceptDisclaimerAndShareDiagnostics** arra, hogy hagyja ki ezeket az utasításokat. 
    >
    >Ha egy aktív támogatási jegyet a Microsoft és a támogatási szakember dolgozik, a kérelmenként futó PerfInsights, ügyeljen arra, hogy adja meg a támogatási jegy száma a a **/sr** lehetőséget.
    >
    >Alapértelmezés szerint a PerfInsights megkísérli frissítése magát a legújabb verzióra, ha elérhető. Használat **/SkipAutoUpdate** vagy **/sau** paraméter kihagyása az automatikus frissítés.  
    >
    >Ha az időtartam kapcsoló **/d** nincs megadva, a PerfInsights figyelmezteti, hogy Reprodukálja a problémát vmslow, az Azure filesba – és a speciális forgatókönyvek futtatása során. 

A nyomkövetések vagy a műveletek végezhető el, amikor új fájlt a PerfInsights ugyanabban a mappában jelenik meg. A fájl neve **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-mm-fff.zip.** Ez a fájl küldhet a támogatási szakembere elemzés céljából, vagy nyissa meg a jelentést a zip-fájlt, tekintse át az eredményeket és meglátásokat bemutató javaslatok belül.

## <a name="review-the-diagnostics-report"></a>Tekintse át a diagnosztikai jelentést

Belül a **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-mm-fff.zip** fájlt, egy HTML-jelentést, amely leírja a PerfInsights eredményeit, találja. Tekintse át a jelentést, bontsa ki a **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-mm-fff.zip** fájlt, és nyissa meg a **PerfInsights diagram jelentés.HTML** fájlt.

Válassza ki a **megállapításokat** fülre.

![Képernyőkép a PerfInsights jelentés](media/how-to-use-perfInsights/findingtab.png)
![PerfInsights jelentés képernyőképe](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Kategóriába sorolt magas megállapításokat ismert problémák, amelyek teljesítményproblémákat okozhatnak. Eredmények kategorizált, közepes méretű képviseli, amely nem feltétlenül okoznak teljesítményproblémákat nem optimális konfigurációkat. Alsó kategóriája megállapításokat informatív utasítások csak olyan.

Tekintse át a javaslatokat, valamint az összes magas és közepes megállapításokat mutató hivatkozásokat. Ismerje meg, hogyan azok befolyásolhatja a teljesítményt, és emellett teljesítményre optimalizált konfigurációk esetén ajánlott eljárásokkal kapcsolatos.

### <a name="storage-tab"></a>Tároló lap

A **megállapításokat** szakasz különféle megállapításokat és tárolással kapcsolatos javaslatokat jelenít meg.

A **lemez térkép** és **kötet térkép** szakaszok ismertetik, hogyan logikai kötetek és a fizikai lemezek kapcsolódnak egymáshoz.

A fizikai lemez szempontból (lemez térkép) a táblázat összes logikai kötetet a lemezen futtató. A következő példában **PhysicalDrive2** két, több partíción (J és H) létrehozott logikai kötetek fut:

![Lemezek lap képernyőképe](media/how-to-use-perfInsights/disktab.png)

A kötet szempontból (kötet térkép) a táblázatokban a fizikai lemezek az egyes logikai hangereje. Figyelje meg, hogy RAID/dinamikus lemez esetében előfordulhat, hogy futtatja egy logikai kötet több fizikai lemezen. A következő példában *C:\\csatlakoztatási* csatlakoztatási pont beállítás értéke *SpannedDisk* fizikai lemezen, 2. és 3:

![Képernyőkép a kötet lap](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL lap

Ha a cél virtuális Gépen üzemelteti az minden olyan SQL Server-példányokat, megjelenik egy lap a jelentésben, nevű **SQL**:

![Képernyőkép az SQL lap](media/how-to-use-perfInsights/sqltab.png)

Ez a szakasz egy **megállapításokat** lapon és a további lapon az egyes virtuális gépen futó SQL Server-példányokat.

A **megállapításokat** lapon megtalálhatja az összes SQL kapcsolódó található, a javaslatok és teljesítményproblémák.

A következő példában **PhysicalDrive0** (fut a C meghajtó) jelenik meg. Ennek az az oka is a **modeldev** és **modellog** a C: meghajtón található fájlokat, és azok különböző típusú (például az adatok fájl és a tranzakciós napló jelölik).

![Képernyőkép a naplóadatok](media/how-to-use-perfInsights/loginfo.png)

Olyan specifikus példányai, az SQL Server-lapokat tartalmaz egy általános szakaszt, amely megjeleníti a kiválasztott példány alapvető adatait. A lapokon további szakaszaiban olvashat speciális információk, beállítások, a konfigurációk és a felhasználói beállítások is tartalmaznak.

### <a name="diagnostic-tab"></a>Diagnosztikai lap
A **diagnosztikai** lapon információt tartalmaz a Processzor, lemez és a memória felső fogyasztók azon a számítógépen futó időtartama a PerfInsights. Is annak kritikus javításokat kapcsolatos információkat, hogy a rendszer hiányzik, a feladatlistában és fontos eseményeket lehet-e. 

## <a name="references-to-the-external-tools-used"></a>A külső eszközök használt mutató hivatkozások

### <a name="diskspd"></a>A Diskspd

A Diskspd egy olyan tárolási terhelés készítő és a teljesítmény vizsgálati eszköz a Microsoft. További információkért lásd: [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Következő helyen

Következő helyen egy olyan parancssori eszköz rögzíthesse a hívásláncokat, a a Windows-teljesítmény eszközkészletéből. További információkért lásd: [Windows Performance Toolkit – a következő helyen](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>További lépések

Feltöltheti-diagnosztikai naplók és jelentések Microsoft Support további ellenőrzésre. Támogatási kérheti, hogy a PerfInsights, amelyek segítik a hibaelhárítást a által létrehozott kimeneti költött.

A következő képernyőképen látható üzenetet hasonlóan mi kaphat:

![A Microsoft Support mintaüzenet képernyőképe](media/how-to-use-perfInsights/supportemail.png)

Kövesse az utasításokat a fájl átvitele munkaterület eléréséhez az üzenetben. A fokozott biztonság érdekében a jelszót az első használatkor módosítania kell.

Miután bejelentkezett, egy párbeszédpanel a feltöltendő megtalálja a **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-mm-fff.zip** PerfInsights által gyűjtött fájl.
