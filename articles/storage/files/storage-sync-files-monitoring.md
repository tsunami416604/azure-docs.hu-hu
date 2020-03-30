---
title: Az Azure-fájlszinkronizálás figyelése | Microsoft dokumentumok
description: Az Azure File Sync figyelése.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699291"
---
# <a name="monitor-azure-file-sync"></a>Az Azure File Sync monitorozása

Az Azure File Sync használatával központosíthatja a szervezet fájlmegosztásait az Azure Files ban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Server kiszolgálón elérhető bármely protokoll thasználhat az adatok helyi eléréséhez, beleértve az SMB, az NFS és az FTPS protokollt. Annyi gyorsítótára lehet, amennyire szüksége van szerte a világon.

Ez a cikk ismerteti, hogyan figyelheti az Azure File Sync központi telepítését az Azure Monitor, a Storage Sync Service és a Windows Server használatával.

A következő figyelési lehetőségek jelenleg rendelkezésre állnak.

## <a name="azure-monitor"></a>Azure Monitor

Az [Azure Monitor segítségével](https://docs.microsoft.com/azure/azure-monitor/overview) megtekintheti a metrikákat, és riasztásokat konfigurálhat a szinkronizáláshoz, a felhőrétegezéshez és a kiszolgálói kapcsolathoz.  

### <a name="metrics"></a>Mérőszámok

Az Azure File Sync metrikák alapértelmezés szerint engedélyezve vannak, és 15 percenként kerülnek elküldésre az Azure Monitornak.

Az Azure File Sync metrikák megtekintéséhez az Azure Monitor, válassza ki a **Storage Sync Services** erőforrás-típus.

Az Azure File Sync következő metrikák érhetők el az Azure Monitorban:

| Metrika neve | Leírás |
|-|-|
| Szinkronizált bájtok | Az átvitt adatok mérete (feltöltés és letöltés).<br><br>Egység: Bájt<br>Összesítés típusa: Összeg<br>Alkalmazandó dimenziók: Kiszolgálóvégpont neve, Szinkronizálás iránya, Csoportnév szinkronizálása |
| Felhőrétegezés visszahívása | A visszahívott adatok mérete.<br><br>**Megjegyzés:** Ez a mérőszám a jövőben eltávolításra kerül. A felhőrétegezés imátlekérési méret metrika segítségével figyelheti a visszahívott adatok méretét.<br><br>Egység: Bájt<br>Összesítés típusa: Összeg<br>Alkalmazandó dimenzió: Kiszolgáló neve |
| Felhőrétegezés visszahívási mérete | A visszahívott adatok mérete.<br><br>Egység: Bájt<br>Összesítés típusa: Összeg<br>Alkalmazandó dimenzió: Kiszolgálónév, Szinkronizálási csoport neve |
| Felhőrétegezés visszahívási méret alkalmazásonként | Az alkalmazás által visszahívott adatok mérete.<br><br>Egység: Bájt<br>Összesítés típusa: Összeg<br>Alkalmazandó dimenzió: Alkalmazás név, kiszolgálónév, szinkronizálási csoport neve |
| Felhőrétegezés visszahívási átviteli | Az adat-visszaírási átviteli rendszer mérete.<br><br>Egység: Bájt<br>Összesítés típusa: Összeg<br>Alkalmazandó dimenzió: Kiszolgálónév, Szinkronizálási csoport neve |
| A fájlok szinkronizálása nem | A nem szinkronizálható fájlok száma.<br><br>Egység: Darabszám<br>Összesítés típusa: Összeg<br>Alkalmazandó dimenziók: Kiszolgálóvégpont neve, Szinkronizálás iránya, Csoportnév szinkronizálása |
| Szinkronizált fájlok | Az átvitt fájlok száma (feltöltés és letöltés).<br><br>Egység: Darabszám<br>Összesítés típusa: Összeg<br>Alkalmazandó dimenziók: Kiszolgálóvégpont neve, Szinkronizálás iránya, Csoportnév szinkronizálása |
| Kiszolgáló online állapota | A kiszolgálótól kapott szívverések száma.<br><br>Egység: Darabszám<br>Összesítés típusa: Maximum<br>Alkalmazandó dimenzió: Kiszolgáló neve |
| Munkamenet-eredmény szinkronizálása | Szinkronizálási munkamenet eredménye (1=sikeres szinkronizálási munkamenet; 0=sikertelen szinkronizálási munkamenet)<br><br>Egység: Darabszám<br>Összesítési típusok: Maximum<br>Alkalmazandó dimenziók: Kiszolgálóvégpont neve, Szinkronizálás iránya, Csoportnév szinkronizálása |

### <a name="alerts"></a>Riasztások

Riasztások konfigurálásához az Azure Monitorban, válassza ki a Storage Sync Service, majd válassza ki az [Azure File Sync metrika](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) a riasztáshoz használható.  

Az alábbi táblázat néhány figyelni et és a riasztáshoz használandó megfelelő metrikát sorol fel:

| Forgatókönyv | Riasztáshoz használandó mérőszám |
|-|-|
| Kiszolgálóvégpont állapota a portálon = Hiba | Munkamenet-eredmény szinkronizálása |
| A fájlok szinkronizálása nem sikerült kiszolgálóval vagy felhőbeli végpontdal | A fájlok szinkronizálása nem |
| A regisztrált kiszolgáló nem tud kommunikálni a Storage Sync Szolgáltatással | Kiszolgáló online állapota |
| A felhőrétegezés visszahívási mérete egy nap alatt meghaladta az 500GiB-t  | Felhőrétegezés visszahívási mérete |

Ha többet szeretne tudni a riasztások Azure Monitorban történő konfigurálásáról, [olvassa el a Riasztások áttekintése a Microsoft Azure-ban című témakört.]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)

## <a name="storage-sync-service"></a>Társzinkronizálási szolgáltatás

A regisztrált kiszolgálóállapot, a kiszolgálóvégpont állapota és a metrikák megtekintéséhez nyissa meg a Storage Sync Service az Azure Portalon. A regisztrált kiszolgáló állapota a **Regisztrált kiszolgálók** panelen és a kiszolgálóvégpont állapotában, a **Szinkronizálási csoportok** panelen tekinthető meg.

### <a name="registered-server-health"></a>Regisztrált kiszolgáló állapota

- Ha a **regisztrált kiszolgáló** állapota **Online,** a kiszolgáló sikeresen kommunikál a szolgáltatással.
- Ha a **regisztrált kiszolgáló** állapota **offline állapotban van,** ellenőrizze, hogy a tárolószinkronizálási figyelő (AzureStorageSyncMonitor.exe) folyamat fut-e a kiszolgálón. Ha a kiszolgáló tűzfal vagy proxy mögött van, olvassa el ezt a [cikket](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) a tűzfal és a proxy konfigurálásához.

### <a name="server-endpoint-health"></a>Kiszolgálóvégpont állapota

- A kiszolgálóvégpont állapota a portálon a kiszolgálón a telemetriai eseménynaplóban (9102-es és 9302-es azonosító) naplózott szinkronizálási eseményeken alapul. Ha egy szinkronizálási munkamenet átmeneti hiba miatt sikertelen, például a hiba megszakítva, a szinkronizálás továbbra is kifogástalan állapotúnak tűnhet a portálon mindaddig, amíg az aktuális szinkronizálási munkamenet folyamatban van. A 9302-es eseményazonosító alapján állapítható meg, hogy a program fájlokat alkalmaz-e. További információt a [Szinkronizálás állapotáról](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) és a szinkronizálás előrehaladásáról című [témakörben talál.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)
- Ha a portál szinkronizálási hibát jelenít meg, mert a szinkronizálás nem halad, útmutatást a [hibaelhárítási dokumentációban](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) talál.

### <a name="metric-charts"></a>Metrikus diagramok

- A storage sync service portálon a következő metrikadiagramok láthatók:

  | Metrika neve | Leírás | Penge neve |
  |-|-|-|
  | Szinkronizált bájtok | Az átvitt adatok mérete (feltöltés és letöltés) | Szinkronizálási csoport, Kiszolgálóvégpont |
  | Felhőrétegezés visszahívása | Visszahívott adatok mérete | Regisztrált kiszolgálók |
  | A fájlok szinkronizálása nem | A nem szinkronizálható fájlok száma | Kiszolgálóvégpont |
  | Szinkronizált fájlok | Az átvitt fájlok száma (feltöltés és letöltés) | Szinkronizálási csoport, Kiszolgálóvégpont |
  | Kiszolgáló online állapota | A kiszolgálótól kapott szívverések száma | Regisztrált kiszolgálók |

- További információ: [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > A Storage Sync Service portál diagramjainak időtartománya 24 óra. A különböző időtartományok vagy dimenziók megtekintéséhez használja az Azure Monitor.

## <a name="windows-server"></a>Windows Server

A Windows Server rendszerben megtekintheti a felhőrétegezést, a regisztrált kiszolgálót és a szinkronizálás állapotát.

### <a name="event-logs"></a>Eseménynaplók

A telemetriai eseménynapló segítségével figyelheti a regisztrált kiszolgáló, szinkronizálás és felhőrétegezés állapotát. A Telemetriai eseménynapló az Eseménynaplóban található az *Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában.*

Szinkronizálás állapota:

- A 9102-es azonosítójú esemény naplózása a szinkronizálási munkamenet befejezése után történik. Ezzel az eseménnyel megállapíthatja, hogy a szinkronizálási munkamenetek sikeresek-e (**HResult = 0**), és hogy vannak-e tételenkénti szinkronizálási hibák. További információt a [szinkronizálás állapotáról](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) és [a termékenkénti hibák dokumentációjában](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) talál.

  > [!Note]  
  > Előfordulhat, hogy a szinkronizálási munkamenetek összességében sikertelenek, vagy nem nulla PerItemErrorCount-mal rendelkeznek. Azonban továbbra is előre haladnak, és néhány fájl szinkronizálása sikeresen megtörtént. Ezt az Alkalmazott mezőkben láthatja, például az AppliedFileCount, az AppliedDirCount, az AppliedTombstoneCount és az AppliedSizeBytes mezőkben. Ezek a mezők megmutatják, hogy a munkamenet mekkora része sikerült. Ha azt látja, hogy több szinkronizálási munkamenet sikertelen egy sorban, és egyre több alkalmazott számmal rendelkeznek, adjon időt a szinkronizálásra, hogy újra próbálkozzon, mielőtt megnyitna egy támogatási jegyet.

- A 9302-es azonosítójú eseményt 5–10 percenként naplózza a rendszer, ha aktív szinkronizálási munkamenet van. Ezzel az eseménnyel határozhatja meg, hogy az aktuális szinkronizálási munkamenet folyamatban van-e (**AppliedItemCount > 0**). Ha a szinkronizálás nem halad előre, a szinkronizálási munkamenet nek végül sikertelennek kell lennie, és a hibával a rendszer a 9102-es azonosítójú eseményt naplózza. További információt a [szinkronizálási folyamat dokumentációjában](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)talál.

Regisztrált kiszolgáló állapota:

- A 9301-es azonosítójú esemény 30 másodpercenként kerül naplózásra, amikor egy kiszolgáló lekérdezi a szolgáltatást a feladatokért. Ha a GetNextJob **állapota = 0,** a kiszolgáló képes kommunikálni a szolgáltatással. Ha a GetNextJob egy hibával fejeződik be, a [hibaelhárítási dokumentációban](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) talál útmutatást.

Felhőrétegezés állapota:

- A kiszolgálón végzett rétegezési tevékenység figyeléséhez használja a 9003-as, 9016-os és 9029-es eseményazonosítójú eseményt a Telemetriai eseménynaplóban, amely az Eseménynaplóban található az *Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában.*

  - A 9003-as eseményazonosító hibaeloszlást biztosít a kiszolgáló végpontja számára. Például: Összes hibaszám és Hibakód. Hibakódonként egy esemény kerül naplózásra.
  - A 9016-os eseményesemény szellemkép-jelentéssel jár egy kötethez. Például: A szabad terület százaléka, a munkamenetben szellemként lévő fájlok száma és a fájlok száma nem sikerült.
  - A 9029-es eseményazonosító szellemkép-munkamenet-információkat biztosít a kiszolgáló végpontjának. Például: A munkamenetben megkísérelt fájlok száma, a munkamenetben rétegzett fájlok száma és a már rétegzett fájlok száma.
  
- A kiszolgálón végzett visszahívási tevékenység figyeléséhez használja a 9005-ös, 9006-os, 9009-es és 9059-es eseményazonosítójú eseményt a Telemetriai eseménynaplóban, amely az Eseménynaplóban, *az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában*található.

  - A 9005-ös eseményazonosító biztosítja a kiszolgálóvégpont visszahívási megbízhatóságát. Például: Az elért egyedi fájlok összesen és a sikertelen hozzáféréssel rendelkező egyedi fájlok összesítése.
  - A 9006-os eseményazonosító visszahívási hibaeloszlást biztosít a kiszolgáló végpontja számára. Például: Összes sikertelen kérelmek, és ErrorCode. Hibakódonként egy esemény kerül naplózásra.
  - A 9009-es eseményazonosító a kiszolgáló végpontjának visszahívási munkamenet-információit tartalmazza. Például: DurationSeconds, CountFilesRecallSucceeded és CountFilesRecallFailed.
  - A 9059-es eseményazonosító alkalmazás-visszahívási terjesztést biztosít a kiszolgáló végpontja számára. Például: ShareId, Alkalmazásnév és TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Teljesítményszámlálók

Használja az Azure File Sync teljesítményszámlálók a kiszolgálón a szinkronizálási tevékenység figyeléséhez.

Az Azure File Sync teljesítményszámlálóinak megtekintéséhez nyissa meg a Teljesítményfigyelőt (Perfmon.exe). A számlálók az **Átvitt AFS-bájtok** és az **AFS-szinkronizálási műveletek** objektumok alatt találhatók.

Az Azure File Sync következő teljesítményszámlálói érhetők el a Teljesítményfigyelőben:

| Teljesítményobjektum\Számláló neve | Leírás |
|-|-|
| Átvitt AFS-bájtok\Letöltött bájt/mp | A másodpercenként letöltött bájtok száma. |
| Átvitt AFS-bájtok\Feltöltött bájt/mp | A másodpercenként feltöltött bájtok száma. |
| Átvitt AFS-bájtok\Teljes bájt/mp | Összes bájt másodpercenként (feltöltés és letöltés). |
| AFS-szinkronizálási műveletek\Letöltött szinkronizálási fájlok/mp | A letöltött fájlok száma másodpercenként. |
| AFS-szinkronizálási műveletek\Feltöltött szinkronizálási fájlok/mp | A másodpercenként feltöltött fájlok száma. |
| AFS-szinkronizálási műveletek\Összes szinkronizálási fájlművelet/mp | A szinkronizált fájlok teljes száma (feltöltés és letöltés). |

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Fontolja meg a tűzfal- és proxybeállításokat](storage-sync-files-firewall-and-proxy.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)
- [Az Azure Files gyakori kérdései](storage-files-faq.md)
