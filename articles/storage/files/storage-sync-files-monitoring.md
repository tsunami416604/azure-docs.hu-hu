---
title: Az Azure File Sync monitorozása |} A Microsoft Docs
description: Az Azure File Sync figyelésének módjáról.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 4ae17249903f317e7a75a3e6bc7c03292021c96a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534633"
---
# <a name="monitor-azure-file-sync"></a>Az Azure File Sync monitorozása

Az Azure File Sync használatával fájlmegosztásainak a szervezet az Azure Files között, miközben gondoskodik a rugalmasságát, teljesítményét és kompatibilitását a helyszíni fájlkiszolgálók. Az Azure File Sync Windows Server az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. Helyileg, az adatok eléréséhez a Windows Serveren elérhető bármely protokollt használhatja, beleértve az SMB, NFS és FTPS. Tetszőleges számú gyorsítótárak világszerte igény szerint is rendelkezhet.

Ez a cikk ismerteti az Azure File Sync üzembe helyezésének figyelése az Azure portal és a Windows Server használatával.

Az alábbi figyelési lehetőségek érhetők el jelenleg.

## <a name="azure-portal"></a>Azure Portal

Az Azure Portalon megtekintheti a regisztrált kiszolgáló állapot, a kiszolgálói végpont állapota (szinkronizálási állapot) és a metrikákat.

### <a name="storage-sync-service"></a>Társzinkronizálási szolgáltatás

Regisztrált kiszolgáló állapota, a kiszolgáló végpontonkénti állapotot és a mérőszámok megtekintéséhez nyissa meg a Storage Sync Service az Azure Portalon. A regisztrált kiszolgáló állapotának megtekintéséhez a **regisztrált kiszolgálókat** panel és a kiszolgáló, a végpontonkénti állapotot a **csoportok szinkronizálása** panelen.

Regisztrált kiszolgáló állapota:

- Ha a **regisztrált kiszolgáló** állapota **Online**, a kiszolgáló sikeresen kommunikálnak a szolgáltatással.
- Ha a **regisztrált kiszolgáló** állapota **megjelenik Offline**, ellenőrizze, hogy fut-e a Storage Sync figyelő (AzureStorageSyncMonitor.exe) folyamat a kiszolgálón. Ha a kiszolgáló egy tűzfal vagy proxy mögött van, tekintse meg [Ez a cikk](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) a tűzfal és proxy konfigurálása.

Kiszolgálói végpont állapota:

- A kiszolgálói végpont állapota a portálon a szinkronizálási naplózott a telemetriai adatok eseménynaplójában a kiszolgálón (9102 és 9302-azonosító) alapul. Ha a szinkronizálási munkamenet egy átmeneti hiba miatt nem sikerül, például hiba meg lett szakítva, szinkronizálási továbbra is jelenhet meg kifogástalan állapotú a portálon mindaddig, amíg a jelenlegi szinkronizálási munkamenet lehetővé teszi a folyamatban. 9302-es Azonosítójú esemény segítségével határozza meg, ha a fájlok vannak folyamatban van a alkalmazni. További információkért lásd: [rendszerállapot szinkronizálni](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) és [szinkronizálása folyamatban](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Ha a portál megjeleníti a szinkronizálási hiba, mert a szinkronizálási folyamat nem készít, tekintse meg a [hibaelhárítási dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) útmutatást.

Metrikák:

- A következő metrikák teljesítményobjektumok a Storage Sync Service portálon:

  | Metrika neve | Leírás | Név panel |
  |-|-|-|
  | Szinkronizált bájt | Átvitt adatok (feltöltési és letöltési) mérete | Szinkronizálási csoport, a kiszolgálói végpont |
  | A felhő rétegezési visszaírási | Idézni adatok mérete | Regisztrált kiszolgálók |
  | Nem szinkronizált fájlok | Fájlok, amelyek nem szinkronizálása száma | Kiszolgálói végpont |
  | Fájlok szinkronizálása | Fájlok száma (feltöltési és letöltési) átvitele | Szinkronizálási csoport, a kiszolgálói végpont |
  | Kiszolgáló online állapotát | A kiszolgálótól kapott szívverések száma | Regisztrált kiszolgálók |

- További tudnivalókért lásd: [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > A Storage Sync Service portálon diagramnak egy időtartomány 24 óra. Különböző időtartományok vagy méretek megtekintéséhez használja az Azure Monitor.

### <a name="azure-monitor"></a>Azure Monitor

Használat [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) sync monitorozása, felhőbeli rétegezés és a kapcsolat a kiszolgálóval. Metrikák az Azure File Sync alapértelmezés szerint engedélyezve vannak, és az Azure Monitor küldött 15 percenként.

Az Azure Monitor az Azure File Sync metrikák megtekintéséhez jelölje ki a **Társzinkronizálási szolgáltatás** erőforrástípus.

A következő metrikák az Azure File Sync az Azure monitorban érhetők el:

| Metrika neve | Leírás |
|-|-|
| Szinkronizált bájt | Átvitt adatok (feltöltési és letöltési) mérete.<br><br>Szervezeti egység: Bájt<br>Aggregation Type: Összeg<br>Alkalmazható dimenziók: Kiszolgálói végpont nevét, szinkronizálási irány, szinkronizálási csoport neve |
| A felhő rétegezési visszaírási | Idézni adatok mérete.<br><br>Szervezeti egység: Bájt<br>Aggregation Type: Összeg<br>A dimenzió alkalmazható: Kiszolgáló neve |
| Nem szinkronizált fájlok | Fájlok, amelyek nem szinkronizálása száma.<br><br>Szervezeti egység: Darabszám<br>Aggregation Type: Összeg<br>Alkalmazható dimenziók: Kiszolgálói végpont nevét, szinkronizálási irány, szinkronizálási csoport neve |
| Fájlok szinkronizálása | Fájlok száma (feltöltési és letöltési) át.<br><br>Szervezeti egység: Darabszám<br>Aggregation Type: Összeg<br>Alkalmazható dimenziók: Kiszolgálói végpont nevét, szinkronizálási irány, szinkronizálási csoport neve |
| Kiszolgáló online állapotát | A kiszolgálótól kapott szívverések száma.<br><br>Szervezeti egység: Darabszám<br>Aggregation Type: Maximum<br>A dimenzió alkalmazható: Kiszolgáló neve |
| Szinkronizálási munkamenet eredménye | Munkamenet eredmény szinkronizálása (1 = sikeres szinkronizálási munkamenet; 0 = sikertelen szinkronizálási munkamenet)<br><br>Szervezeti egység: Darabszám<br>Az összesítés típusa: Maximum<br>Alkalmazható dimenziók: Kiszolgálói végpont nevét, szinkronizálási irány, szinkronizálási csoport neve |

## <a name="windows-server"></a>Windows Server

A Windows Server felhőbeli rétegezés, a regisztrált kiszolgáló megtekintheti és rendszerállapot szinkronizálni.

### <a name="event-logs"></a>Eseménynaplók

A kiszolgálón a telemetriai adatok Eseménynapló használatával figyelheti a regisztrált kiszolgáló, a szinkronizálási és a felhőbeli rétegezés állapotfigyelő. A telemetriai adatok eseménynaplójában található az Eseménynapló alapján *alkalmazásokat és Services\Microsoft\FileSync\Agent*.

Szinkronizálás állapota:

- Eseményazonosító 9102 egy szinkronizálási munkamenet befejezése után a rendszer naplózza. Használja ezt az eseményt annak meghatározására, hogy ha a szinkronizálási munkamenetek sikeres (**HResult = 0**), és nincsenek-e cikk szinkronizálási hibák. További információkért lásd: a [rendszerállapot szinkronizálni](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) és [elemenkénti hibák](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) dokumentációját.

  > [!Note]  
  > Néha szinkronizálási munkamenetek teljes sikertelen, vagy egy nem nulla értékű PerItemErrorCount rendelkezik. Azonban továbbra is élnek előrefelé görgetést, és egyes fájlok szinkronizálása sikeresen megtörtént. Ez a alkalmazott mezőket, például a AppliedFileCount, AppliedDirCount, AppliedTombstoneCount és AppliedSizeBytes látható. Ezek a mezők jelzi, hogy mekkora a munkamenet sikeresen befejeződött. Ha több szinkronizálási munkamenetek teendőt olyan sorok esetén sikertelen látja, és a egy növekvő érvényesítettek száma rendelkeznek, adjon a szinkronizálási idő, és próbálkozzon újra egy támogatási jegy megnyitása előtt.

- 9302-es Azonosítójú esemény 5-10 percenként kerül, ha van egy aktív szinkronizálási munkamenet. Ez az esemény segítségével határozza meg, ha a szinkronizálási munkamenet jelenlegi, hogy így folyamatban (**AppliedItemCount > 0**). Ha a szinkronizálási folyamat nem készít, végül hibával leáll a szinkronizálási munkamenet kell, és egy Event ID 9102 lesz naplózva a hiba miatt. További információkért lásd: a [szinkronizálása folyamatban dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Regisztrált kiszolgáló állapota:

- Eseményazonosító 9301 Ez lehet 30 másodperc, ha a kiszolgáló lekérdezi a feladatok a szolgáltatás a rendszer naplózza. Ha GetNextJob befejezi a **állapot = 0**, a kiszolgáló nem tud kommunikálni a szolgáltatás. Ha GetNextJob hibával ér véget, ellenőrizze a [hibaelhárítási dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) útmutatást.

A felhő rétegezési állapota:

- Egy kiszolgálói rétegezési tevékenység monitorozásához használja a Event ID 9003, 9016 és a Telemetriai eseménynaplójában, eseménynaplójában alatt található 9029 *alkalmazásokat és Services\Microsoft\FileSync\Agent*.

  - 9003-es Azonosítójú esemény a kiszolgálói végpont hibaeloszlás biztosít. Példa: Összesített hibaszám és hibakód. Hibakód: egy eseményt a rendszer naplózza.
  - Eseményazonosító 9016 másolatkészítési eredményeket biztosít egy köteten. Példa: Szabad hely %-os, a munkamenetben mögé fájlok száma, amely több fájlt nem sikerült a ghost.
  - Eseményazonosító 9029 kiszolgálói végpont másolatkészítési munkamenet információkat nyújt. Példa: A munkamenet rétegzett fájlok a munkamenetet, a fájlok száma a megkísérelt száma, és már rétegzett fájlok száma.
  
- A kiszolgálói tevékenység visszaírási monitorozásához használja a Event ID 9005, 9006, 9009 és a Telemetriai eseménynaplójában, eseménynaplójában alatt található 9059 *alkalmazásokat és Services\Microsoft\FileSync\Agent*.

  - 9005-es Azonosítójú esemény a kiszolgálói végpont visszaírási megbízhatóságot biztosít. Példa: Egyedi fájlok összesen használt és teljes egyedi fájlok sikertelen.
  - Eseményazonosító 9006 visszaírási hibaeloszlás a kiszolgálói végpont biztosít. Példa: Összes sikertelen kérelem és hibakód. Hibakód: egy eseményt a rendszer naplózza.
  - Eseményazonosító 9009 kiszolgálói végpont visszaírási munkamenet információkat nyújt. Példa: DurationSeconds, CountFilesRecallSucceeded, and CountFilesRecallFailed.
  - Eseményazonosító 9059 alkalmazás visszaírási terjesztési a kiszolgálói végpont biztosít. Példa: ShareId, alkalmazás neve és TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Teljesítményszámlálók

A kiszolgálón az Azure File Sync teljesítményszámlálók használatával figyeli a szinkronizálási tevékenység.

A kiszolgálón az Azure File Sync teljesítményszámlálók megtekintéséhez nyissa meg a Teljesítményfigyelőt (Perfmon.exe). A számlálók alatt található a **AFS bájt továbbított** és **AFS szinkronizálási műveletek** objektumokat.

Az Azure File Sync a következő számlálókat a Teljesítményfigyelő érhetők el:

| Teljesítmény Object\Counter neve | Leírás |
|-|-|
| AFS bájt Transferred\Downloaded bájt/mp | Letöltött bájtok száma másodpercenként. |
| AFS bájt Transferred\Uploaded bájt/mp | Másodpercenként feltöltött bájtok száma. |
| AFS bájt Transferred\Total bájt/mp | Összes bájt / másodperc (feltöltési és letöltési). |
| AFS szinkronizálási Operations\Downloaded szinkronizálási fájlok/mp | A másodpercenként letöltött fájlok száma. |
| AFS szinkronizálási Operations\Uploaded szinkronizálási fájlok/mp | Másodpercenként feltöltött fájlok száma. |
| AFS szinkronizálási Operations\Total szinkronizálási fájl művelet/mp | Fájlok szinkronizálása (feltöltési és letöltési) teljes száma. |

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Vegye figyelembe a tűzfal és proxy-beállítások](storage-sync-files-firewall-and-proxy.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)
- [Az Azure Files – gyakori kérdések](storage-files-faq.md)
