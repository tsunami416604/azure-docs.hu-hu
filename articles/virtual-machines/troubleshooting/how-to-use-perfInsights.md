---
title: A PerfInsights használata a Microsoft Azureban | Microsoft Docs
description: Megtudhatja, hogyan használható a PerfInsights a Windowsos virtuális gépek teljesítményével kapcsolatos problémák elhárításához.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: eace9c196ebd9f71b7a6f2bac7a59f581e6f313f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090463"
---
# <a name="how-to-use-perfinsights"></a>A PerfInsights használata

A [PerfInsights](https://aka.ms/perfinsightsdownload) egy önsegítő diagnosztikai eszköz, amely összegyűjti és elemzi a diagnosztikai adatokat, és jelentést nyújt a Windows rendszerű virtuális gépek teljesítményével kapcsolatos problémák megoldásához az Azure-ban. A PerfInsights virtuális gépeken is futtathatók önálló eszközként, közvetlenül a portálról az [Azure Virtual Machines teljesítmény-diagnosztika](performance-diagnostics.md)használatával vagy az [Azure Performance Diagnostics](performance-diagnostics-vm-extension.md)virtuálisgép-bővítményének telepítésével.

Ha teljesítménnyel kapcsolatos problémákat tapasztal a virtuális gépekkel kapcsolatban, akkor a támogatáshoz való kapcsolatfelvétel előtt futtassa ezt az eszközt.

## <a name="supported-troubleshooting-scenarios"></a>Támogatott hibaelhárítási forgatókönyvek

A PerfInsights több fajta információt gyűjthet és elemez. A következő szakaszban a gyakori forgatókönyvek jelennek meg.

### <a name="quick-performance-analysis"></a>Gyors teljesítmény elemzése

Ez a forgatókönyv a lemez konfigurációját és egyéb fontos információkat gyűjt, beleértve a következőket:

-   Eseménynaplók

-   Az összes bejövő és kimenő kapcsolat hálózati állapota

-   Hálózati és tűzfal konfigurációs beállításai

-   A rendszeren jelenleg futó összes alkalmazás feladatlistája

-   Adatbázis-konfigurációs beállítások Microsoft SQL Server (ha a virtuális gép SQL Server-t futtató kiszolgálóként van azonosítva)

-   Tárolási megbízhatósági számlálók

-   Fontos Windows-gyorsjavítások

-   Telepített szűrő-illesztőprogramok

Ez az információ passzív gyűjteménye, amely nem érinti a rendszerét. 

>[!Note]
>Ezt a forgatókönyvet a következő esetekben automatikusan tartalmazza a rendszer:

### <a name="benchmarking"></a>Teljesítménymérésre

Ez a forgatókönyv a [Diskspd](https://github.com/Microsoft/diskspd) teljesítményteszt-tesztet (IOPS és MBPS) futtatja a virtuális géphez csatolt összes meghajtón. 

> [!Note]
> Ez a forgatókönyv hatással lehet a rendszerre, és nem futtatható éles üzemi rendszeren. Ha szükséges, futtassa ezt a forgatókönyvet egy dedikált karbantartási ablakban a problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt-tesztek által okozott megnövekedett munkaterhelés negatív hatással lehet a virtuális gép teljesítményére.
>

### <a name="performance-analysis"></a>Teljesítmény elemzése

Ez a forgatókönyv a [teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) nyomkövetését a RuleEngineConfig. JSON fájlban megadott számlálók használatával futtatja. Ha a virtuális gép a SQL Server rendszert futtató kiszolgálóként van azonosítva, a teljesítményszámláló-nyomkövetés fut. Ezt a RuleEngineConfig. JSON fájlban található számlálók használatával végezheti el. Ez a forgatókönyv a teljesítmény-diagnosztikai adatait is tartalmazza.

### <a name="azure-files-analysis"></a>Azure Files elemzése

Ez a forgatókönyv egy speciális teljesítményszámláló-rögzítést futtat egy hálózati nyomkövetéssel együtt. A rögzítés magában foglalja az összes SMB-ügyfél megosztási számlálóját. Az alábbiakban néhány, a rögzítés részét képező SMB-ügyfél-megosztási teljesítményszámlálók találhatók:

| **Típus**     | **SMB-ügyfél megosztási számlálója** |
|--------------|-------------------------------|
| IOPS         | Adatkérések másodpercenkénti száma             |
|              | Olvasási kérelmek/másodperc             |
|              | Írási kérelmek/másodperc            |
| Késés      | Átlagos/mp/adatkérés         |
|              | Átlagos/olvasási sebesség (mp)                 |
|              | Átlagos/írási sebesség (mp)                |
| IO-méret      | Átl. Bájt/adat kérése       |
|              | Átl. Bájt/olvasás               |
|              | Átl. Bájt/írás              |
| Teljesítmény   | Adatmennyiség (bájt/s)                |
|              | Olvasott bájt/mp                |
|              | Írt bájt/mp               |
| Várólista hossza | Átl. Olvasási várólista hossza        |
|              | Átl. Írási várólista hossza       |
|              | Átl. Adatvárólista hossza        |

### <a name="advanced-performance-analysis"></a>Fejlett teljesítmény-elemzés

Ha speciális teljesítmény-elemzést futtat, a Nyomkövetések lehetőség kiválasztásával párhuzamosan futtathatók. Ha szeretné, futtathatja az összeset (teljesítményszámláló, XPerf, hálózat és StorPort).  

> [!Note]
> Ez a forgatókönyv hatással lehet a rendszerre, és nem futtatható éles üzemi rendszeren. Ha szükséges, futtassa ezt a forgatókönyvet egy dedikált karbantartási ablakban a problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt-tesztek által okozott megnövekedett munkaterhelés negatív hatással lehet a virtuális gép teljesítményére.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Milyen típusú információkat gyűjt a PerfInsights?

A Windows rendszerű virtuális gépekre, lemezekre vagy tárolási készletek konfigurálására, a teljesítményszámlálók, a naplók és a különböző nyomkövetési adatok gyűjtésére vonatkozó információk. Ez a használt teljesítménnyel kapcsolatos forgatókönyvtől függ. A következő táblázat a részleteket tartalmazza:

|Összegyűjtött adatok                              |  |  | Teljesítménnyel kapcsolatos forgatókönyvek |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Gyors teljesítmény elemzése | Teljesítménymérésre | Teljesítmény elemzése | Azure Files elemzése | Fejlett teljesítmény-elemzés |
| Az eseménynaplók adatai       | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Rendszerinformáció                | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Kötet térképe                        | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Lemezes Térkép                          | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Futó tevékenységek                     | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tárolási megbízhatósági számlálók      | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Szolgáltatás adatai               | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Fsutil kimenet                     | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Illesztőprogram-adatok szűrése                | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Netstat kimenet                    | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Hálózati konfiguráció             | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tűzfal konfigurációja            | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| SQL Server konfiguráció          | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Teljesítmény-diagnosztika nyomkövetése *  | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Teljesítményszámláló nyomkövetése * *      |                            |                                    | Igen                      |                      | Igen                  |
| SMB-számláló nyomkövetése * *              |                            |                                    |                          | Igen                  |                      |
| SQL Server számláló nyomkövetése * *       |                            |                                    | Igen                      |                      | Igen                  |
| XPerf nyomkövetés                       |                            |                                    |                          |                      | Igen                  |
| StorPort-nyomkövetés                    |                            |                                    |                          |                      | Igen                  |
| Hálózati nyomkövetés                     |                            |                                    |                          | Igen                  | Igen                  |
| Diskspd teljesítményteszt nyomkövetése * * *       |                            | Igen                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Teljesítmény-diagnosztika nyomkövetése (*)

A háttérben futtat egy szabály-alapú motort az adatok gyűjtéséhez és a folyamatban lévő teljesítménnyel kapcsolatos problémák diagnosztizálásához. Jelenleg a következő szabályok támogatottak:

- HighCpuUsage szabály: Észleli a PROCESSZORok magas kihasználtsági időszakait, és megjeleníti az adott időszakokban a legfelső szintű CPU-használati felhasználókat.
- HighDiskUsage szabály: Észleli a nagy kihasználtságú lemezeket a fizikai lemezeken, és megjeleníti a legfelső szintű felhasználási felhasználókat az adott időszakokban.
- HighResolutionDiskMetric szabály: Az egyes fizikai lemezek IOPS, átviteli sebességét és I/O-késleltetési metrikáit jeleníti meg 50 ezredmásodpercben. Segít a lemez-szabályozási időszakok gyors azonosításában.
- HighMemoryUsage szabály: Észleli a nagy memória-használati időszakokat, és megjeleníti az adott időszakokban a legfelső szintű használatú felhasználókat.

> [!NOTE] 
> Jelenleg a .NET-keretrendszer 4,5-es vagy újabb verzióit tartalmazó Windows-verziók támogatottak.

### <a name="performance-counter-trace-"></a>Teljesítményszámláló nyomkövetése (* *)

A következő teljesítményszámlálók összegyűjtése:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk és \Logikai lemez
- \Cache\Dirty-lapok, \Cache\Lazy-írási ürítések/mp, \Server\Pool-nem lapozható, hibák és \Server\Pool lapozható hibák
- A \ hálózati felület, a \IPv4\Datagrams, a \IPv6\Datagrams, a \TCPv4\Segments, a \TCPv6\Segments, a \ hálózati-adapter, a \WFPv4\Packets, a \WFPv6\Packets, a \UDPv4\Datagrams, a \UDPv6\Datagrams, a \TCPv4\Connection, a \TCPv6\Connection, \ Hálózati QoS-Policy\Packets, \Per processzor hálózati kártya tevékenység és \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>SQL Server példányok esetén
- \SQL-kiszolgáló: puffer-kezelő, \SQLServer: erőforráskészlet-statisztika és \SQLServer: SQL-statisztika \
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Azure Files
\SMB-ügyfelek megosztása

### <a name="diskspd-benchmark-trace-"></a>Diskspd teljesítményteszt nyomkövetése (* * *)
Diskspd I/O-munkaterhelési tesztek (operációsrendszer-lemez [írás] és készlet-meghajtók [olvasás/írás])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Futtassa a PerfInsights eszközt a virtuális gépen

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Mit kell tudni az eszköz futtatása előtt? 

#### <a name="tool-requirements"></a>Eszközre vonatkozó követelmények

-  Ezt az eszközt a teljesítménnyel kapcsolatos problémát biztosító virtuális gépen kell futtatni. 

-  A következő operációs rendszerek támogatottak: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016; Windows 8,1 és Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Lehetséges problémák az eszköz éles virtuális gépeken való futtatásakor

-  A benchmarking forgatókönyvhöz vagy a XPerf vagy Diskspd használatára konfigurált "Advanced Performance Analysis" forgatókönyv esetén az eszköz negatív hatással lehet a virtuális gép teljesítményére. Ezeket a forgatókönyveket nem szabad éles környezetben futtatni.

-  A benchmarking forgatókönyvhöz vagy a Diskspd használatára konfigurált "Advanced Performance Analysis" forgatókönyvhöz győződjön meg arról, hogy egyetlen más háttérbeli tevékenység sem zavarja az I/O-munkaterhelést.

-  Alapértelmezés szerint az eszköz az ideiglenes tárolót használja az adatok gyűjtésére. Ha hosszabb ideig van engedélyezve a nyomkövetés, a gyűjtött adatok mennyisége is releváns lehet. Ezzel csökkentheti az ideiglenes lemez tárterületének rendelkezésre állását, és így hatással lehet a meghajtón alapuló alkalmazásokra.

### <a name="how-do-i-run-perfinsights"></a>Hogyan futtatni a PerfInsights-t? 

Az [Azure Performance Diagnostics](performance-diagnostics-vm-extension.md)virtuálisgép-bővítményének telepítésével virtuális gépen is futtathat PerfInsights. Önálló eszközként is futtatható. 

**A PerfInsights telepítése és futtatása a Azure Portal**

További információ erről a lehetőségről: az [Azure Performance Diagnostics virtuálisgép-bővítmény telepítése](performance-diagnostics-vm-extension.md#install-the-extension).  

**PerfInsights futtatása önálló módban**

A PerfInsights eszköz futtatásához kövesse az alábbi lépéseket:


1. Töltse le a [PerfInsights. zip fájlt](https://aka.ms/perfinsightsdownload).

2. Oldja fel a PerfInsights. zip fájlt. Ehhez kattintson a jobb gombbal a PerfInsights. zip fájlra, és válassza a **Tulajdonságok**lehetőséget. Az **általános** lapon válassza a **Tiltás feloldása**elemet, majd kattintson **az OK gombra**. Ez biztosítja, hogy az eszköz további biztonsági kérések nélkül fusson.  

    ![Képernyőkép a PerfInsights tulajdonságairól, Kiemelt feloldással](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Bontsa ki a tömörített PerfInsights. zip fájlt az ideiglenes meghajtóra (alapértelmezés szerint ez általában a D meghajtó). 

4.  Nyissa meg a Windows-parancssort rendszergazdaként, majd futtassa a PerfInsights. exe fájlt, és tekintse meg az elérhető parancssori paramétereket.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Képernyőkép a PerfInsights parancssori kimenetről](media/how-to-use-perfInsights/pi-commandline.png)
    
    A PerfInsights-forgatókönyvek futtatásának alapvető szintaxisa a következő:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Az alábbi példát követve futtathat teljesítmény-elemzési forgatókönyvet 5 percen keresztül:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Az alábbi példát követve futtathatja a speciális forgatókönyvet a XPerf és a teljesítményszámláló nyomkövetésével 5 percen keresztül:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Az alábbi példát követve futtathat teljesítmény-elemzési forgatókönyvet 5 percen keresztül, és feltöltheti az eredményként szolgáló zip-fájlt a Storage-fiókba:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Az összes rendelkezésre álló forgatókönyvet és beállítást a **/List** parancs használatával tekintheti meg:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Egy forgatókönyv futtatása előtt a PerfInsights felszólítja a felhasználót, hogy fogadja el a diagnosztikai adatok megosztását, és fogadja el a végfelhasználói licencszerződést. Használja az **/AcceptDisclaimerAndShareDiagnostics** kapcsolót az ilyen kérések kihagyásához. 
    >
    >Ha aktív támogatási jegyet használ a Microsofttal, és az PerfInsights-t futtatja a támogatási szakember kérelmére, akkor ügyeljen arra, hogy a támogatási jegy számát a **/SR** beállítással adja meg.
    >
    >Alapértelmezés szerint a PerfInsights megpróbálja frissíteni magát a legújabb verzióra, ha elérhető. Az automatikus frissítés kihagyása az **/SkipAutoUpdate** vagy a **/Sau** paraméterrel.  
    >
    >Ha nincs megadva az időtartam kapcsoló **/d** értéke, a PerfInsights felszólítja, hogy Reprodukálási a problémát a vmslow, a azurefiles és a speciális forgatókönyvek futtatásakor. 

A Nyomkövetések vagy műveletek befejezésekor az új fájl ugyanabban a mappában jelenik meg, mint a PerfInsights. A fájl neve **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-SS-fff. zip.** Ezt a fájlt elküldheti a támogatási ügynöknek elemzésre, vagy megnyithatja a jelentést a zip-fájlban a megállapítások és javaslatok áttekintéséhez.

## <a name="review-the-diagnostics-report"></a>A diagnosztikai jelentés áttekintése

A **\_PerformanceDiagnostics éééé-hh-nn\_hh-mm-SS-fff. zip** fájlon belül egy HTML-jelentést talál, amely a PerfInsights eredményeit részletezi. A jelentés áttekintéséhez bontsa ki **a\_PerformanceDiagnostics éééé-hh-\_nn hh-mm-SS-fff. zip** fájlt, majd nyissa meg a **PerfInsights report. html** fájlt.

Válassza az **eredmények** lapot.

![Képernyőkép a PerfInsights jelentésről](media/how-to-use-perfInsights/pi-finding-tab.png)
![a PerfInsights-jelentésről](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> A magas kategóriába sorolt megállapítások olyan ismert problémák, amelyek teljesítménnyel kapcsolatos problémákat okozhatnak. A közepesként kategorizált eredmények olyan nem optimális konfigurációkat jelentenek, amelyek nem szükségszerűen okoznak teljesítménnyel kapcsolatos problémákat. Az alacsonyként kategorizált eredmények csak tájékoztató utasítások.

Tekintse át az ajánlásokat és a hivatkozásokat az összes magas és közepes eredményhez. Ismerje meg, hogy miként befolyásolhatják a teljesítményt és az ajánlott eljárásokat a teljesítményre optimalizált konfigurációk esetében.

### <a name="storage-tab"></a>Tároló lap

Az **eredmények** szakasz a tárolással kapcsolatos különböző eredményeket és javaslatokat jeleníti meg.

A **Disk Map** és a **Volume Map** szakasz ismerteti, hogyan kapcsolódnak egymáshoz a logikai kötetek és a fizikai lemezek.

A fizikai lemez perspektívájában (Disk map) a táblázatban a lemezen futó összes logikai kötet látható. A következő példában a **PhysicalDrive2** két, több partíción létrehozott logikai kötetet futtat (J és H):

![A lemez lap képernyőképe](media/how-to-use-perfInsights/pi-disk-tab.png)

A kötet perspektívájában (Volume map) a táblázatok az egyes logikai kötetek összes fizikai lemezét megjelenítik. Figyelje meg, hogy a RAID/dinamikus lemezek esetében több fizikai lemezen is futtathat logikai köteteket. A következő példában a *C:\\Mount* egy csatlakoztatási pont, amely a 2. és 3. fizikai lemezeken *SpannedDisk* van konfigurálva:

![Képernyőfelvétel a Volume lapról](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>SQL lap

Ha a célként megadott virtuális gép bármely SQL Server példányt futtat, a jelentés egy további lapja jelenik meg, az **SQL**neve:

![Az SQL Tab képernyőképe](media/how-to-use-perfInsights/pi-sql-tab.png)

Ez a szakasz egy **ténymegállapítás** lapot tartalmaz, valamint a virtuális gépen futó SQL Server-példányok további lapjait.

Az **eredmények** lap tartalmazza az összes talált SQL-teljesítménnyel kapcsolatos problémát, valamint a javaslatokat.

A következő példában a **PhysicalDrive0** (a C meghajtó futtatása) üzenet jelenik meg. Ennek az az oka, hogy mind a **modeldev** , mind a **Modellog** fájl a C meghajtón található, és különböző típusúak (például adatfájlok és tranzakciónaplók).

![A naplózási információk képernyőképe](media/how-to-use-perfInsights/pi-log-info.png)

A SQL Server adott példányaihoz tartozó lapok egy általános szakaszt tartalmaznak, amely a kiválasztott példánnyal kapcsolatos alapvető adatokat jeleníti meg. A lapok további részeket is tartalmaznak a speciális információkhoz, beleértve a beállításokat, a konfigurációkat és a felhasználói beállításokat.

### <a name="diagnostic-tab"></a>Diagnosztika lap
A **diagnosztika** lapon a PerfInsights futtatásának időtartamára vonatkozó információk találhatók a számítógép felső CPU-, lemez-és memória-fogyasztóiról. A rendszerből esetlegesen hiányzó kritikus javításokkal, a feladatlistával és a fontos rendszereseményekkel kapcsolatos információk is megtalálhatók. 

## <a name="references-to-the-external-tools-used"></a>A használt külső eszközökre mutató hivatkozások

### <a name="diskspd"></a>Diskspd

A Diskspd egy Storage Load Generator és Performance test eszköz a Microsofttól. További információ: [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

A XPerf egy parancssori eszköz, amely a Windows Performance Toolkit nyomkövetéseit rögzíti. További információ: [Windows Performance Toolkit – XPerf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>További lépések

A diagnosztikai naplókat és jelentéseket feltöltheti Microsoft ügyfélszolgálata további áttekintés céljából. A támogatás kérheti, hogy továbbítsa a PerfInsights által generált kimenetet, hogy segítséget nyújtson a hibaelhárítási folyamathoz.

Az alábbi képernyőképen egy, a következőhöz hasonló üzenet látható:

![Képernyőkép a Microsoft ügyfélszolgálataról](media/how-to-use-perfInsights/pi-support-email.png)

Az üzenetben található utasításokat követve férhet hozzá a fájlátviteli munkaterülethez. A további biztonság érdekében meg kell változtatnia a jelszavát az első használatkor.

A bejelentkezést követően egy párbeszédpanel jelenik meg, amely feltölti a PerfInsights által gyűjtött **\_PerformanceDiagnostics éééé-hh\_-nn hh-mm-SS-fff. zip** fájlt.

