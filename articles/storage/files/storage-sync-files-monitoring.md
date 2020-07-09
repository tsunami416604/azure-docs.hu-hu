---
title: Figyelő Azure File Sync | Microsoft Docs
description: A Azure File Sync figyelése.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0232a0c6526d6dcdfec86dedec437c71e7e21080
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515199"
---
# <a name="monitor-azure-file-sync"></a>Az Azure File Sync monitorozása

A Azure File Sync segítségével központilag kezelheti a szervezete fájlmegosztást Azure Filesban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Tetszőleges számú gyorsítótárral rendelkezhet a világ minden tájáról.

Ez a cikk azt ismerteti, hogyan figyelheti Azure File Sync üzembe helyezését a Azure Monitor, a Storage Sync Service és a Windows Server használatával.

Jelenleg az alábbi figyelési lehetőségek állnak rendelkezésre.

## <a name="azure-monitor"></a>Azure Monitor

A [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) használatával megtekintheti a metrikákat, és konfigurálhatja a szinkronizálási, a felhő-és a kiszolgálói kapcsolatok riasztásait.  

### <a name="metrics"></a>Mérőszámok

A Azure File Sync metrikái alapértelmezés szerint engedélyezve vannak, és 15 percenként Azure Monitor küldik őket.

Azure Monitor Azure File Sync metrikáinak megtekintéséhez válassza ki a **Storage Sync Services** erőforrástípust.

Az Azure File Sync következő mérőszámai érhetők el Azure Monitorban:

| Metrika neve | Description |
|-|-|
| Szinkronizált bájtok száma | Az átvitt adatok mérete (feltöltés és letöltés).<br><br>Egység: bájtok<br>Összesítés típusa: Sum<br>Alkalmazható méretek: kiszolgálói végpont neve, szinkronizálás iránya, szinkronizálási csoport neve |
| Felhőbeli rétegek felidézése | A visszahívott adatmennyiség.<br><br>**Megjegyzés**: Ez a mérőszám a jövőben el lesz távolítva. A Felhőbeli rétegű visszahívás méretének mérőszámával figyelheti a meghívott adatok méretét.<br><br>Egység: bájtok<br>Összesítés típusa: Sum<br>Alkalmazható dimenzió: kiszolgálónév |
| Felhőbeli rétegek felidézésének mérete | A visszahívott adatmennyiség.<br><br>Egység: bájtok<br>Összesítés típusa: Sum<br>Alkalmazható dimenzió: kiszolgáló neve, szinkronizálási csoport neve |
| Felhőbeli rétegek felidézésének mérete alkalmazás szerint | Az alkalmazás által visszahívott adatmennyiség.<br><br>Egység: bájtok<br>Összesítés típusa: Sum<br>Alkalmazható dimenzió: alkalmazás neve, kiszolgálónév, szinkronizálási csoport neve |
| Felhőbeli rétegek felidézésének átviteli sebessége | Az visszahívási teljesítmény mérete.<br><br>Egység: bájtok<br>Összesítés típusa: Sum<br>Alkalmazható dimenzió: kiszolgáló neve, szinkronizálási csoport neve |
| Nem szinkronizált fájlok | A szinkronizálni nem kívánt fájlok száma.<br><br>Egység: darabszám<br>Összesítés típusa: Sum<br>Alkalmazható méretek: kiszolgálói végpont neve, szinkronizálás iránya, szinkronizálási csoport neve |
| Szinkronizált fájlok | Az átvitt fájlok száma (feltöltés és letöltés).<br><br>Egység: darabszám<br>Összesítés típusa: Sum<br>Alkalmazható méretek: kiszolgálói végpont neve, szinkronizálás iránya, szinkronizálási csoport neve |
| Kiszolgáló online állapota | A kiszolgálótól kapott szívverések száma.<br><br>Egység: darabszám<br>Összesítés típusa: maximum<br>Alkalmazható dimenzió: kiszolgálónév |
| Szinkronizálási munkamenet eredménye | Szinkronizálási munkamenet eredménye (1 = sikeres szinkronizálási munkamenet; 0 = sikertelen szinkronizálási munkamenet)<br><br>Egység: darabszám<br>Összesítési típusok: maximum<br>Alkalmazható méretek: kiszolgálói végpont neve, szinkronizálás iránya, szinkronizálási csoport neve |

### <a name="alerts"></a>Riasztások

Ha Azure Monitor riasztásokat szeretne konfigurálni, válassza ki a Storage Sync szolgáltatást, majd válassza ki a riasztáshoz használni kívánt [Azure file Sync metrikát](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) .  

A következő táblázat a riasztásra vonatkozó példákat és a riasztáshoz használandó megfelelő mérőszámot sorolja fel:

| Forgatókönyv | A riasztáshoz használandó metrika |
|-|-|
| Kiszolgálói végpont állapota a portálon = hiba | Szinkronizálási munkamenet eredménye |
| A fájlok nem szinkronizálhatók a kiszolgálóval vagy a Felhőbeli végponttal | Nem szinkronizált fájlok |
| A regisztrált kiszolgáló nem tud kommunikálni a Storage Sync szolgáltatással | Kiszolgáló online állapota |
| A Felhőbeli rétegek felidézésének mérete túllépte a 500GiB egy napon belül  | Felhőbeli rétegek felidézésének mérete |

Ha többet szeretne megtudni a Azure Monitor riasztások konfigurálásáról, tekintse meg [a Microsoft Azure riasztások áttekintése]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)című témakört.

## <a name="storage-sync-service"></a>Társzinkronizálási szolgáltatás

A regisztrált kiszolgáló állapotának, a kiszolgálói végpont állapotának és a mérőszámoknak a megtekintéséhez nyissa meg a Storage Sync szolgáltatást a Azure Portal. A regisztrált kiszolgáló állapota a **regisztrált kiszolgálók** panelen és a kiszolgálói végpont állapota lapon tekinthető meg a **szinkronizálási csoportok** panelen.

### <a name="registered-server-health"></a>Regisztrált kiszolgáló állapota

- Ha a **regisztrált kiszolgáló** állapota **online**, a kiszolgáló sikeresen kommunikál a szolgáltatással.
- Ha a **regisztrált kiszolgáló** állapota **kapcsolat nélküli üzemmódban jelenik meg**, ellenőrizze, hogy a kiszolgálón fut-e a Storage Sync monitor (AzureStorageSyncMonitor.exe) folyamata. Ha a kiszolgáló tűzfal vagy proxy mögött található, tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) a tűzfal és a proxy konfigurálásához.

### <a name="server-endpoint-health"></a>Kiszolgálói végpont állapota

- A portál kiszolgálói végpontjának állapota a kiszolgálón található telemetria-eseménynaplóban naplózott szinkronizálási eseményeken alapul (azonosító: 9102 és 9302). Ha egy szinkronizálási munkamenet átmeneti hiba miatt meghiúsul, például a hiba megszakadt, a szinkronizálás továbbra is Kifogástalan állapotba kerülhet a portálon, amíg az aktuális szinkronizálási munkamenet folyamatban van. A 9302-es AZONOSÍTÓJÚ esemény annak meghatározására szolgál, hogy a fájlok alkalmazása folyamatban van-e. További információ: az [állapot szinkronizálása](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) és a [szinkronizálási folyamat](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Ha a portál szinkronizálási hibát jelez, mert a szinkronizálás nem halad előre, tekintse meg a [hibaelhárítási dokumentációt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) .

### <a name="metric-charts"></a>Metrikai diagramok

- A következő metrikai diagramok láthatók a Storage Sync szolgáltatás portálon:

  | Metrika neve | Description | Lap neve |
  |-|-|-|
  | Szinkronizált bájtok száma | Átvitt adatok mérete (feltöltés és letöltés) | Szinkronizálási csoport, kiszolgálói végpont |
  | Felhőbeli rétegek felidézése | Visszahívott adatmennyiség | Regisztrált kiszolgálók |
  | Nem szinkronizált fájlok | A szinkronizálni nem kívánt fájlok száma | Kiszolgálói végpont |
  | Szinkronizált fájlok | Átvitt fájlok száma (feltöltés és letöltés) | Szinkronizálási csoport, kiszolgálói végpont |
  | Kiszolgáló online állapota | A kiszolgálótól kapott szívverések száma | Regisztrált kiszolgálók |

- További információ: [Azure monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > A Storage Sync szolgáltatás portáljának diagramjai 24 órás időintervallummal rendelkeznek. A különböző időtartományok vagy méretek megtekintéséhez használja a Azure Monitor.

## <a name="windows-server"></a>Windows Server

A Windows Serveren megtekintheti a felhő-előállítók, a regisztrált kiszolgálók és a szinkronizálás állapotát.

### <a name="event-logs"></a>Eseménynaplók

A kiszolgáló telemetria-eseménynaplójának használatával figyelheti a regisztrált kiszolgálókat, a szinkronizálást és a felhőalapú rétegek állapotát. Az telemetria Eseménynapló az *alkalmazások és a Services\Microsoft\FileSync\Agent*területen található Eseménynapló.

Szinkronizálás állapota:

- A 9102-es AZONOSÍTÓJÚ esemény a szinkronizálási munkamenet befejeződése után van naplózva. Ezzel az eseménnyel megállapíthatja, hogy a szinkronizálási munkamenetek sikeresek-e (**HResult = 0**), és hogy vannak-e az egyes elemek szinkronizálási hibái. További információ: [Sync Health](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) és [per-Item errors](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) dokumentáció.

  > [!Note]  
  > Időnként a szinkronizálási munkamenetek nem teljesek, vagy nem nulla PerItemErrorCount rendelkeznek. Azonban továbbra is előrehaladást végeznek, és néhány fájl szinkronizálása sikeresen megtörtént. Ezt az alkalmazott mezőkben láthatja, például AppliedFileCount, AppliedDirCount, AppliedTombstoneCount és AppliedSizeBytes. Ezek a mezők tájékoztatják, hogy a munkamenet mennyivel járt sikerrel. Ha úgy látja, hogy egy sorban több szinkronizálási munkamenet is meghibásodik, és egyre nagyobb mértékben vannak alkalmazva, a támogatási jegy megnyitása előtt adja meg a szinkronizálási időt.

- Az 9302-as AZONOSÍTÓJÚ esemény 5 – 10 percenként van naplózva, ha aktív szinkronizálási munkamenet van. Ezzel az eseménysel megállapíthatja, hogy az aktuális szinkronizálási munkamenet folyamatban van-e (**AppliedItemCount > 0**). Ha a szinkronizálás nem végez előrehaladást, a szinkronizálási munkamenetnek végül sikertelennek kell lennie, és a 9102-es AZONOSÍTÓJÚ esemény lesz naplózva a hibával. További információt a [szinkronizálási folyamat dokumentációjában](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)talál.

Regisztrált kiszolgáló állapota:

- Az 9301-as AZONOSÍTÓJÚ esemény 30 másodpercenként van naplózva, amikor egy kiszolgáló lekérdezi a szolgáltatást a feladatokhoz. Ha a GetNextJob a **status = 0**értékkel végződik, a kiszolgáló képes kommunikálni a szolgáltatással. Ha a GetNextJob hibával zárul, útmutatásért olvassa el a [hibaelhárítási dokumentációt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) .

Felhő-rétegek állapota:

- A-kiszolgálón a rétegek közötti tevékenységek figyeléséhez használja a 9003, 9016 és 9029 AZONOSÍTÓJÚ eseményt a telemetria eseménynaplójában, amely az *alkalmazások és Services\Microsoft\FileSync\Agent*területen található Eseménynapló.

  - A 9003-as AZONOSÍTÓJÚ esemény egy kiszolgálói végpont hibáinak eloszlását biztosítja. Például: összesített hibák száma és ErrorCode. A rendszer egy eseményt naplóz egy hibakód alapján.
  - A 9016-es azonosítójú esemény a kötetek szellemképes eredményeit biztosítja. Például: a szabad terület százalékos értéke, a munkamenetben felkészült fájlok száma, és a fájlok száma nem sikerült.
  - A 9029-as AZONOSÍTÓJÚ esemény a kiszolgálói végpontok szellemkép-információit biztosítja. Például: a munkamenetben megkísérelt fájlok száma, a munkamenetben leírtak száma és a már lépcsőzetesen megadott fájlok száma.
  
- A kiszolgálón található visszahívás-tevékenységek figyeléséhez használja a 9005, 9006, 9009 és 9059 azonosítójú eseményazonosítót a telemetria eseménynaplójában, amely Eseménynapló az *alkalmazások és a Services\Microsoft\FileSync\Agent*területen található.

  - Az 9005-as AZONOSÍTÓJÚ esemény egy kiszolgálói végpontra vonatkozó visszahívás megbízhatóságot biztosít. Például: összes elért egyedi fájl, valamint a sikertelen hozzáféréssel rendelkező egyedi fájlok teljes száma.
  - Az 9006-as AZONOSÍTÓJÚ esemény a kiszolgálói végpontok visszahívási hibáinak eloszlását biztosítja. Például: összes sikertelen kérelem és ErrorCode. A rendszer egy eseményt naplóz egy hibakód alapján.
  - Az 9009-as AZONOSÍTÓJÚ esemény egy kiszolgálói végpontra vonatkozó visszahívás-munkamenet-információkat biztosít. Például: DurationSeconds, CountFilesRecallSucceeded és CountFilesRecallFailed.
  - Az 9059-as AZONOSÍTÓJÚ esemény egy kiszolgálói végpont alkalmazás-visszahívási eloszlását biztosítja. Például: ShareId, alkalmazásnév és TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Teljesítményszámlálók

A szinkronizálási tevékenység figyeléséhez használja a kiszolgáló Azure File Sync teljesítményszámlálói.

A kiszolgáló Azure File Sync teljesítményszámlálók megtekintéséhez nyissa meg a Teljesítményfigyelőt (Perfmon.exe). A számlálók a **továbbított AFS-bájtok** és az **AFS-szinkronizációs** objektumok alatt találhatók.

A következő teljesítményszámlálók érhetők el Azure File Sync a Teljesítményfigyelőben:

| Teljesítmény Object\Counter neve | Description |
|-|-|
| AFS bájtok Transferred\Downloaded sebessége (bájt/s) | A másodpercenként letöltött bájtok száma. |
| AFS bájtok Transferred\Uploaded sebessége (bájt/s) | A másodpercenként feltöltött bájtok száma. |
| AFS bájtok Transferred\Total sebessége (bájt/s) | Bájtok másodpercenkénti száma (feltöltés és letöltés). |
| AFS Sync Operations\Downloaded-szinkronizálási fájlok/mp | A letöltött fájlok száma másodpercenként. |
| AFS Sync Operations\Uploaded-szinkronizálási fájlok/mp | A feltöltött fájlok száma másodpercenként. |
| AFS Sync Operations\Total szinkronizálási művelet/mp | A szinkronizált fájlok teljes száma (feltöltés és letöltés). |

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [A tűzfal és a proxy beállításainak megfontolása](storage-sync-files-firewall-and-proxy.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Azure-fájlok szinkronizálásának hibaelhárítása](storage-sync-files-troubleshoot.md)
- [Azure Files gyakori kérdések](storage-files-faq.md)
