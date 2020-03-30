---
title: A PerfInsights használata a Microsoft Azure-ban| Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogy miként háríthatja el a PerfInsights segítségével a Windows virtuális gépek teljesítményével kapcsolatos problémákat.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 783b479dd3e5f429516799d7d3ea82f363cac2ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250126"
---
# <a name="how-to-use-perfinsights"></a>A PerfInsights használata

[A PerfInsights](https://aka.ms/perfinsightsdownload) egy önsegítő diagnosztikai eszköz, amely összegyűjti és elemzi a diagnosztikai adatokat, és jelentést készít a Windows virtuális gépek azure-beli teljesítményproblémáinak elhárításához. A PerfInsights virtuális gépeken önálló eszközként futtatható, közvetlenül a portálról az [Azure virtuális gépek teljesítménydiagnosztika](performance-diagnostics.md)használatával vagy [az Azure Performance Diagnostics vm extension](performance-diagnostics-vm-extension.md)telepítésével.

Ha teljesítményproblémákat tapasztal a virtuális gépekkel kapcsolatban, mielőtt kapcsolatba lépne az ügyfélszolgálattal, futtassa ezt az eszközt.

## <a name="supported-troubleshooting-scenarios"></a>Támogatott hibaelhárítási forgatókönyvek

A PerfInsights többféle információt gyűjthet és elemezhet. A következő szakaszok a gyakori forgatókönyveket ismertetik.

### <a name="quick-performance-analysis"></a>Gyors teljesítményelemzés

Ez a forgatókönyv a lemezkonfigurációt és más fontos információkat gyűjti, többek között a következőket:

-   Eseménynaplók

-   Az összes bejövő és kimenő kapcsolat hálózati állapota

-   Hálózati és tűzfal-konfigurációs beállítások

-   Feladatlista a rendszeren jelenleg futó összes alkalmazáshoz

-   Microsoft SQL Server adatbázis-konfigurációs beállítások (ha a virtuális gép SQL Server rendszert futtató kiszolgálóként van azonosítva)

-   Tárolási megbízhatósági számlálók

-   Fontos Windows gyorsjavítások

-   Telepített szűrőillesztők

Ez egy passzív információgyűjtemény, amely nem befolyásolhatja a rendszert. 

>[!Note]
>Ez a forgatókönyv automatikusan bekerül az alábbi esetek mindegyikébe:

### <a name="benchmarking"></a>Teljesítményértékelés

Ebben a forgatókönyvben fut a [Diskspd](https://github.com/Microsoft/diskspd) benchmark-teszt (IOPS és MBPS) a virtuális géphez csatlakoztatott összes meghajtón. 

> [!Note]
> Ez a forgatókönyv hatással lehet a rendszerre, és nem kell futtatni egy élő termelési rendszer. Szükség esetén futtassa ezt a forgatókönyvet egy dedikált karbantartási ablakban a problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt által okozott megnövekedett munkaterhelés hátrányosan befolyásolhatja a virtuális gép teljesítményét.
>

### <a name="performance-analysis"></a>Teljesítményelemzés

Ebben a forgatókönyvben a RuleEngineConfig.json fájlban megadott számlálók használatával futtat egy teljesítményszámláló-nyomkövetést. [performance counter](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) Ha a virtuális gép SQL Server t futtató kiszolgálóként van azonosítva, a rendszer egy teljesítményszámláló-nyomkövetést futtat. Ezt a RuleEngineConfig.json fájlban található számlálók használatával teszi. Ez a forgatókönyv teljesítménydiagnosztikai adatokat is tartalmaz.

### <a name="azure-files-analysis"></a>Azure-fájlok elemzése

Ebben a forgatókönyvben egy speciális teljesítményszámláló-rögzítést és egy hálózati nyomkövetést futtat. A rögzítés tartalmazza az összes Kiszolgálói üzenetblokk (SMB) ügyfélmegosztási számlálót. Az alábbiakban néhány kulcsfontosságú SMB-ügyfél megosztási teljesítményszámlálót, amelyek a rögzítés részét képezik:

| **Típus**     | **SMB-ügyfélmegosztási számláló** |
|--------------|-------------------------------|
| IOPS         | Adatkérések/mp             |
|              | Olvasási kérelmek/mp             |
|              | Írási kérelmek/mp            |
| Késés      | Átlagos másodperc/Adatkérés         |
|              | Átlagos másodperc/Olvasás                 |
|              | Átlagos másodperc/írás                |
| Io-méret      | Átlagos bájt/adatkérés       |
|              | Átlagos bájt/olvasás               |
|              | Átlagos bájt/írás              |
| Teljesítmény   | Adatbájt/mp                |
|              | Olvasott bájt/mp                |
|              | Írt bájt/mp               |
| Processzor-várólista hossza | Olvasási várólista átlagos hossza        |
|              | Átlagos írási várólista hossza       |
|              | Adatvárólista átlagos hossza        |

### <a name="advanced-performance-analysis"></a>Speciális teljesítményelemzés

Speciális teljesítményelemzés futtatásakor a párhuzamosan futtatandó nyomkövetések lehetőséget. Ha szeretné, futtathatja őket (Teljesítményszámláló, Xperf, Hálózat és StorPort).  

> [!Note]
> Ez a forgatókönyv hatással lehet a rendszerre, és nem kell futtatni egy élő termelési rendszer. Szükség esetén futtassa ezt a forgatókönyvet egy dedikált karbantartási ablakban a problémák elkerülése érdekében. A nyomkövetési vagy teljesítményteszt által okozott megnövekedett munkaterhelés hátrányosan befolyásolhatja a virtuális gép teljesítményét.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Milyen adatokat gyűjt a PerfInsights?

A rendszer információkat gyűjt a Windows virtuális gépekről, a lemezekről vagy a tárolókészletek konfigurációjáról, a teljesítményszámlálókról, a naplókról és a különböző nyomkövetésekről. Ez a használt teljesítményforgatókönyvtől függ. A részleteket a következő táblázat tartalmazza:

|Összegyűjtött adatok                              |  |  | Teljesítménybeli forgatókönyvek |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Gyors teljesítményelemzés | Teljesítményértékelés | Teljesítményelemzés | Azure-fájlok elemzése | Speciális teljesítményelemzés |
| Információ az eseménynaplókból       | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Rendszerinformáció                | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Kötettérkép                        | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Lemezleképezés                          | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Feladatok futtatása                     | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tárolási megbízhatósági számlálók      | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tárolási adatok               | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Fsutil kimenet                     | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Illesztőprogram-adatok szűrése                | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Netstat kimenet                    | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Hálózati konfiguráció             | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Tűzfal-konfiguráció            | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| SQL Server-konfiguráció          | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Teljesítménydiagnosztika nyomkövetései *  | Igen                        | Igen                                | Igen                      | Igen                  | Igen                  |
| Teljesítményszámláló-nyomkövetés **      |                            |                                    | Igen                      |                      | Igen                  |
| SMB-számláló nyomkövetése **              |                            |                                    |                          | Igen                  |                      |
| SQL Server számlálójának nyomkövetése **       |                            |                                    | Igen                      |                      | Igen                  |
| Xperf nyomkövetés                       |                            |                                    |                          |                      | Igen                  |
| StorPort nyomkövetés                    |                            |                                    |                          |                      | Igen                  |
| Hálózati nyomkövetés                     |                            |                                    |                          | Igen                  | Igen                  |
| Diskspd benchmark nyomkövetés ***       |                            | Igen                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Teljesítménydiagnosztika-nyomkövetés (*)

A háttérben egy szabályalapú motort futtat az adatok gyűjtésére és a folyamatban lévő teljesítményproblémák diagnosztizálására. A következő szabályok jelenleg támogatottak:

- HighCpuUsage szabály: Észleli a magas CPU-használati időszakok, és megmutatja a felső CPU-használat fogyasztók ezekben az időszakokban.
- HighDiskUsage szabály: Észleli a magas lemezhasználati időszakokat a fizikai lemezeken, és megjeleníti a legnépszerűbb lemezhasználati fogyasztókat ezekben az időszakokban.
- HighResolutionDiskMetric szabály: IOPS, átviteli és I/O-késés metrikák 50 ezredmásodpercenként minden egyes fizikai lemez. Segít a lemezszabályozási időszakok gyors azonosításában.
- HighMemoryUsage szabály: Észleli a magas memóriahasználati időszakok, és megmutatja a felső memóriahasználat fogyasztók ezekben az időszakokban.

> [!NOTE] 
> Jelenleg a .

### <a name="performance-counter-trace-"></a>Teljesítményszámláló-nyomkövetés (**)

A következő teljesítményszámlálókat gyűjti:

- \Folyamat, \Processzor, \Memória, \Szál, \PhysicalDisk és \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Nem lapozható készlet, Hibák és \Server\Pool paged hibák
- A \Hálózati adapter, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Szegmensek, \TCPv6\Szegmensek, \Hálózati adapter, \WFPv4\Csomagok, \WFPv6\Csomagok, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \TCPv6\Connection\ \ Hálózati QoS-házirend\Csomagok, \Processzoronkénti hálózati csatolókártya-tevékenység és \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>SQL Server-példányok esetén
- \SQL Server:Buffer Manager, \SQLServer:Erőforráskészlet statisztikája és \SQLServer:SQL Statistics\
- \SQLServer:Zárolások, \SQLServer:Általános, Statisztika
- \SQLServer:Hozzáférési módszerek

#### <a name="for-azure-files"></a>Azure-fájlokhoz
\SMB ügyfélmegosztások

### <a name="diskspd-benchmark-trace-"></a>Diskspd teljesítménynyomkövetés (***)
Diskspd I/O számítási feladatok tesztjei (operációs rendszerlemez [írási] és készletmeghajtók [olvasás/írás])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>A PerfInsights eszköz futtatása a virtuális számítógépen

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Mit kell tudnom az eszköz futtatása előtt? 

#### <a name="tool-requirements"></a>A szerszámra vonatkozó követelmények

-  Ezt az eszközt a teljesítményproblémát okozó virtuális számítógépen kell futtatni. 

-  A következő operációs rendszerek támogatottak: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016; Windows 8.1 és Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Lehetséges problémák, ha az eszközt éles virtuális gépeken futtatja

-  Az xperf vagy diskspd használatára konfigurált "Speciális teljesítményelemzés" esetén az eszköz hátrányosan befolyásolhatja a virtuális gép teljesítményét. Ezeket a forgatókönyveket nem szabad éles környezetben futtatni.

-  A benchmarking forgatókönyv vagy a "Speciális teljesítményelemzés" forgatókönyv, amely a Diskspd használatára van konfigurálva, győződjön meg arról, hogy nincs más háttértevékenység zavarja az I/O számítási feladatok.

-  Alapértelmezés szerint az eszköz az ideiglenes tárolómeghajtót használja az adatok gyűjtésére. Ha a nyomkövetés hosszabb ideig engedélyezve marad, az összegyűjtött adatok mennyisége releváns lehet. Ez csökkentheti az ideiglenes lemezen rendelkezésre álló helyet, és ezért hatással lehet minden olyan alkalmazásra, amely ezen a meghajtón alapul.

### <a name="how-do-i-run-perfinsights"></a>Hogyan futtathatom a PerfInsights-ot? 

A PerfInsights virtuális gépen az [Azure Performance Diagnostics vm extension](performance-diagnostics-vm-extension.md)telepítésével futtatható. Önálló eszközként is futtathatja. 

**A PerfInsights telepítése és futtatása az Azure Portalról**

Erről a beállításról további információt az [Azure Performance Diagnostics vm extension telepítése című témakörben](performance-diagnostics-vm-extension.md#install-the-extension)talál.  

**PerfInsights futtatása önálló módban**

A PerfInsights eszköz futtatásához hajtsa végre az alábbi lépéseket:


1. Letöltés [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. A PerfInsights.zip fájl blokkolásának feloldása. Ehhez kattintson a jobb gombbal a PerfInsights.zip fájlra, és válassza **a Tulajdonságok parancsot.** Az **Általános** lapon válassza a **Tiltás feloldása**lehetőséget, majd az **OK**gombot. Ez biztosítja, hogy az eszköz további biztonsági kérések nélkül fut.  

    ![Képernyőkép a PerfInsights tulajdonságairól, kiemelve a Tiltás feloldása lehetőséggel](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Bontsa ki a tömörített PerfInsights.zip fájlt az ideiglenes meghajtóra (alapértelmezés szerint ez általában a D meghajtó). 

4.  Nyissa meg a Windows parancssort rendszergazdaként, majd futtassa a PerfInsights.exe programot az elérhető parancssori paraméterek megtekintéséhez.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![A PerfInsights parancssori kimenetének képernyőképe](media/how-to-use-perfInsights/pi-commandline.png)
    
    A PerfInsights-forgatókönyvek futtatásának alapvető szintaxisa a következő:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Az alábbi példában 5 mins-ig futtathat teljesítményelemzési forgatókönyvet:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    A következő példával 5 méterre futtathatja a speciális forgatókönyvet Xperf és Performance számlálónyomatokkal:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Az alábbi példában 5 napig futtathatja a teljesítményelemzési forgatókönyvet, és feltöltheti az eredményzip-fájlt a tárfiókba:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    A **/list** paranccsal megkeresheti az összes rendelkezésre álló forgatókönyvet és lehetőséget:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >A perfInsights egy forgatókönyv futtatása előtt felszólítja a felhasználót, hogy egyezzen bele a diagnosztikai információk megosztásába, és fogadja el a végfelhasználói licencszerződés elfogadását. Az **/AcceptDisclaimerAndShareDiagnostics** kapcsolóval kihagyhatja ezeket a utasításokat. 
    >
    >Ha aktív támogatási jegye van a Microsoftnál, és a PerfInsights futtatása a támogatási szakember kérésére, akivel dolgozik, győződjön meg arról, hogy megadja a támogatási jegy számát az **/sr** kapcsoló használatával.
    >
    >Alapértelmezés szerint a PerfInsights megpróbálja frissíteni magát a legújabb verzióra, ha elérhető. A **/SkipAutoUpdate** vagy **a /sau** paraméter kapcsolóval hagyja ki az automatikus frissítést.  
    >
    >Ha a **/d** időtartam-kapcsoló nincs megadva, a PerfInsights a vmslow, az azurefiles és a speciális forgatókönyvek futtatása közben repro-t kér a problémára. 

A nyomkövetések vagy műveletek befejezésekor egy új fájl jelenik meg ugyanabban a mappában, mint a PerfInsights. A fájl neve **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip.** Elküldheti ezt a fájlt a támogatási ügynöknek elemzésre, vagy megnyithatja a jelentést a zip fájlban, hogy áttekintse a megállapításokat és javaslatokat.

## <a name="review-the-diagnostics-report"></a>A diagnosztikai jelentés áttekintése

A **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** fájlban talál egy HTML-jelentést, amely részletezi a PerfInsights megállapításait. A jelentés áttekintéséhez **bontsa\_ki a PerformanceDiagnostics\_yyyy-MM-dd hh-mm-ss-fff.zip** fájlt, majd nyissa meg a **PerfInsights Report.html** fájlt.

Válassza az **Eredmények** lapot.

![Képernyőkép a PerfInsights jelentésről](media/how-to-use-perfInsights/pi-finding-tab.png)
![a PerfInsights-jelentésről](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> A magasként kategorizált megállapítások olyan ismert problémák, amelyek teljesítményproblémákat okozhatnak. A közepesként kategorizált megállapítások nem optimális konfigurációkat képviselnek, amelyek nem feltétlenül okoznak teljesítményproblémákat. Az alacsonyként kategorizált megállapítások csak tájékoztató jellegű megállapítások.

Tekintse át az ajánlásokat és a linkeket az összes magas és közepes megállapításokat. Ismerje meg, hogyan befolyásolhatják a teljesítményt, valamint a teljesítményre optimalizált konfigurációkra vonatkozó gyakorlati tanácsok.

### <a name="storage-tab"></a>Tárolás lap

A **Megállapítások** szakasz a tárolással kapcsolatos különböző megállapításokat és ajánlásokat jeleníti meg.

A **Lemeztérkép** és **a Kötettérkép** szakasz azt ismerteti, hogy a logikai kötetek és a fizikai lemezek hogyan kapcsolódnak egymáshoz.

A fizikai lemez perspektívájában (Lemezleképezés) a táblázat a lemezen futó összes logikai kötetet megjeleníti. A következő példában a **PhysicalDrive2** két, több partíción (J és H) létrehozott logikai kötetet futtat:

![Képernyőkép a lemezlapról](media/how-to-use-perfInsights/pi-disk-tab.png)

A kötet perspektívájában (Kötettérkép) a táblázatok az egyes logikai kötetek alatti összes fizikai lemezt jelenítik meg. Figyelje meg, hogy RAID/Dynamic lemezek esetén előfordulhat, hogy logikai kötetet több fizikai lemezen is futtat. A következő példában a *C:\\csatlakoztatás* *spannedDisk* néven konfigurált csatlakoztatási pont a 2- es és 3-as fizikai lemezeken:

![Képernyőkép a Hangerő lapról](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>SQL lap

Ha a célvirtuális gép SQL Server-példányokat üzemeltet, a jelentésben egy további lap jelenik meg, amelynek neve **SQL:**

![Képernyőkép az SQL lapról](media/how-to-use-perfInsights/pi-sql-tab.png)

Ez a szakasz tartalmazza **a megállapítások** lapot, és további lapok at az SQL Server-példányok a virtuális gép üzemeltetett.

Az **Eredmények** lap tartalmazza az SQL-lel kapcsolatos teljesítményproblémák listáját, valamint a javaslatokat.

A következő példában a (C meghajtót futtató) **PhysicalDrive0** jelenik meg. Ennek az az oka, hogy mind a **modeldev,** mind a **modellog** fájlok a C meghajtón találhatók, és különböző típusúak (például adatfájl és tranzakciós napló).

![Naplóadatok képernyőképe](media/how-to-use-perfInsights/pi-log-info.png)

Az SQL Server egyes példányainak lapjai egy általános szakaszt tartalmaznak, amely a kijelölt példányra vonatkozó alapvető információkat jeleníti meg. A lapok további szakaszokat is tartalmaznak a speciális információkhoz, beleértve a beállításokat, a konfigurációkat és a felhasználói beállításokat.

### <a name="diagnostic-tab"></a>Diagnosztikai lap
A **Diagnosztika** lap a PerfInsights futásának időtartama alatt a számítógép felső processzor-, lemez- és memória-fogyasztóiról tartalmaz információkat. Információkat találhat a rendszer hiányzó kritikus javításairól, a feladatlistáról és a fontos rendszereseményekről is. 

## <a name="references-to-the-external-tools-used"></a>Hivatkozások a használt külső eszközökre

### <a name="diskspd"></a>Diskspd

Diskspd egy tárolási terhelés generátor és teljesítmény teszt eszköz a Microsoft. További információ: [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf között

Az Xperf egy parancssori eszköz, amely a Windows teljesítményeszközkészletből rögzíti a nyomkövetéseket. További információt a [Windows teljesítményeszközkészlete – Xperf című témakörben talál.](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)

## <a name="next-steps"></a>További lépések

További ellenőrzés céljából diagnosztikai naplókat és jelentéseket tölthet fel a Microsoft támogatási szolgálatának. A támogatás kérheti, hogy továbbítsa a PerfInsights által létrehozott kimenetet a hibaelhárítási folyamat segítése érdekében.

A következő képernyőképen a kapotthoz hasonló üzenet jelenik meg:

![Képernyőkép a Microsoft támogatási szolgálatától származó mintaüzenetről](media/how-to-use-perfInsights/pi-support-email.png)

A fájlátviteli munkaterület eléréséhez kövesse az üzenetutasításait. A nagyobb biztonság érdekében az első használatkor meg kell változtatnia a jelszavát.

Miután bejelentkezett, talál egy párbeszédpanelt a PerfInsights által gyűjtött **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** fájl feltöltéséhez.

