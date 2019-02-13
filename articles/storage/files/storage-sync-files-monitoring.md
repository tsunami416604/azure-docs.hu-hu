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
ms.openlocfilehash: a14b0f2b01a0566a47cbcb02ee4315adcba9a90f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200802"
---
# <a name="monitor-azure-file-sync"></a>Az Azure File Sync monitorozása

Az Azure File Sync használatával fájlmegosztásainak a szervezet az Azure Files között, miközben gondoskodik a rugalmasságát, teljesítményét és kompatibilitását a helyszíni fájlkiszolgálók. Az Azure File Sync Windows Server az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. Helyileg, az adatok eléréséhez a Windows Serveren elérhető bármely protokollt használhatja, beleértve az SMB, NFS és FTPS. Tetszőleges számú gyorsítótárak világszerte igény szerint is rendelkezhet.

Ez a cikk ismerteti az Azure File Sync üzembe helyezés az Azure portal és a Windows Server használatával figyelése.

Jelenleg érhetők el az alábbi figyelési lehetőségek:

## <a name="azure-portal"></a>Azure Portal

Az Azure Portalon megtekintheti a regisztrált kiszolgáló állapot, a kiszolgálói végpont állapota (szinkronizálási állapot) és a metrikákat.

### <a name="storage-sync-service"></a>Társzinkronizálási szolgáltatás

Regisztrált kiszolgáló állapota, a kiszolgáló végpontonkénti állapotot és a mérőszámok megtekintéséhez nyissa meg a Storage Sync Service az Azure Portalon. Regisztrált kiszolgáló állapotának ellenőrzése a regisztrált kiszolgálók paneljén megtekinthető. A kiszolgáló végpont állapotának ellenőrzése a szinkronizálási csoportok paneljén megtekinthető.

Registered Server Health
- Ha a regisztrált kiszolgáló állapota Online, a kiszolgáló sikeresen kommunikálnak a szolgáltatással.
- Ha a regisztrált kiszolgáló állapota Offline jelenik meg, ellenőrizze, fut-e a Storage Sync figyelő (AzureStorageSyncMonitor.exe) folyamat a kiszolgálón. Ha a kiszolgáló egy tűzfal vagy proxy mögött, konfigurálja a tűzfal és proxy / [dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy).

Server Endpoint Health
- A kiszolgálói végpont állapota a portálon a szinkronizálási naplózott a telemetriai adatok eseménynaplójában a kiszolgálón (9102 és 9302-azonosító) alapul. A szinkronizálási munkamenet (például hiba megszakítva) egy átmeneti hiba miatt nem sikerül, ha a előfordulhat, hogy továbbra is megjelenítése kifogástalan állapotú, a portál szinkronizálási, mindaddig, amíg a jelenlegi szinkronizálási munkamenet, hogy így a folyamat (Event ID 9302 azt határozza meg, ha a fájlok vannak alkalmazva). További információ a következő dokumentációban tekintheti meg: [Rendszerállapot szinkronizálni](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [szinkronizálása folyamatban](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Ha a portálon láthatók onnan, és nincs folyamatban szinkronizálás szinkronizálási hibát, ellenőrizze a [hibaelhárítási dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) útmutatást.

Mérőszámok
- A következő metrikák teljesítményobjektumok a Storage Sync Service portálon:

  | Metrika neve | Leírás | Portál blade(s) | 
  |-|-|-|
  | Szinkronizált bájt | Átvitt adatok (feltöltési és letöltési) mérete | Szinkronizálási csoport, a kiszolgálói végpont |
  | A felhő rétegezési visszaírási | Idézni adatok mérete | Regisztrált kiszolgálók |
  | Nem szinkronizált fájlok | Fájlok, amelyek nem szinkronizálása száma | Kiszolgálói végpont |
  | Fájlok szinkronizálása | Fájlok száma (feltöltési és letöltési) átvitele | Szinkronizálási csoport, a kiszolgálói végpont |
  | Kiszolgálói szívverés | A kiszolgálótól kapott szívverések száma | Regisztrált kiszolgálók |

- További tudnivalókért lásd: [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor) szakaszban. 

  > [!Note]  
  > A Storage Sync Service portálon diagramnak egy időtartomány 24 óra. Különböző időtartományok vagy méretek megtekintéséhez használja az Azure Monitor.


### <a name="azure-monitor"></a>Azure Monitor

Használat [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) sync monitorozása, felhőbeli rétegezés és a kapcsolat a kiszolgálóval. Metrikák az Azure File Sync alapértelmezés szerint engedélyezve vannak, és az Azure Monitor küldött 15 percenként.

Az Azure Monitor az Azure File Sync metrikák megtekintéséhez válassza ki a Társzinkronizálási szolgáltatás erőforrástípus.

A következő metrikák az Azure File Sync az Azure monitorban érhetők el:

| Metrika neve | Leírás |
|-|-|
| Szinkronizált bájt | Átvitt adatok (feltöltési és letöltési) mérete.<br><br>Szervezeti egység: Bájt<br>Aggregation Type: Összeg<br>Alkalmazható dimenziók: Kiszolgálói végpont nevét, szinkronizálási irány, szinkronizálási csoport neve |
| A felhő rétegezési visszaírási | Idézni adatok mérete.<br><br>Szervezeti egység: Bájt<br>Aggregation Type: Összeg<br>A dimenzió alkalmazható: Kiszolgáló neve |
| Nem szinkronizált fájlok | Fájlok, amelyek nem szinkronizálása száma.<br><br>Szervezeti egység: Darabszám<br>Aggregation Type: Összeg<br>Alkalmazható dimenziók: Kiszolgálói végpont nevét, szinkronizálási irány, szinkronizálási csoport neve |
| Fájlok szinkronizálása | Fájlok száma (feltöltési és letöltési) át.<br><br>Szervezeti egység: Darabszám<br>Aggregation Type: Összeg<br>Alkalmazható dimenziók: Kiszolgálói végpont nevét, szinkronizálási irány, szinkronizálási csoport neve |
| Kiszolgálói szívverés | A kiszolgálótól kapott szívverések száma.<br><br>Szervezeti egység: Darabszám<br>Aggregation Type: Maximum<br>A dimenzió alkalmazható: Kiszolgáló neve |
| Szinkronizálási munkamenet eredménye | Munkamenet eredmény szinkronizálása (1 = sikeres szinkronizálási munkamenet; 0 = sikertelen szinkronizálási munkamenet)<br><br>Szervezeti egység: Darabszám<br>Az összesítés típusa: Maximum<br>Alkalmazható dimenziók: Kiszolgálói végpont nevét, szinkronizálási irány, szinkronizálási csoport neve |

## <a name="windows-server"></a>Windows Server

A Windows Serveren felhőbeli rétegezés, a regisztrált kiszolgáló megtekintheti és rendszerállapot szinkronizálni.

### <a name="event-logs"></a>Eseménynaplók

A kiszolgálón a telemetriai adatok Eseménynapló használatával figyelheti a regisztrált kiszolgáló, a szinkronizálási és a felhőbeli rétegezés állapotfigyelő. A telemetriai adatok Eseménynapló alkalmazások és az eseménynaplóban Services\Microsoft\FileSync\Agent alatt található.

Szinkronizálás állapota
- Eseményazonosító 9102 egy szinkronizálási munkamenet befejezése után a rendszer naplózza. Ez az esemény használandó határozza meg, ha a szinkronizálási munkamenetek sikeresen elvégezte-e (HResult = 0), és nincsenek-e cikk szinkronizálási hibák. További információ a következő dokumentációban tekintheti meg: [Rendszerállapot szinkronizálni](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [elemenkénti hibák](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Néha szinkronizálási munkamenetek teljes sikertelen, vagy egy nem nulla értékű PerItemErrorCount rendelkezik, de továbbra is meg a folyamat előrehaladását, az egyes fájlok szinkronizálása sikeresen megtörtént. Ez a alkalmazott mezőket (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount és AppliedSizeBytes), amely jelzi, hogy mekkora a munkamenet a következő látható. Ha a sor több olyan szinkronizálási munkamenetek nem működik, de egy növekvő érvényesítettek száma jelenik meg, majd adjon szinkronizálási idő, próbálja meg újra a támogatási jegy megnyitása előtt.

- 9302-es Azonosítójú esemény 5-10 percenként kerül, ha van egy aktív szinkronizálási munkamenet. Határozza meg, ha a jelenlegi szinkronizálási munkamenet lehetővé teszi a folyamatban használt ezt az eseményt (AppliedItemCount > 0). Szinkronizálási folyamat nem készít, végül hibával leáll a szinkronizálási munkamenet kell, és a egy Event ID 9102 lesz naplózva a hiba miatt. További információ a következő dokumentációban tekintheti meg: [Szinkronizálás folyamatban](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Registered Server Health
- Eseményazonosító 9301 Ez lehet 30 másodperc, ha a kiszolgáló lekérdezi a feladatok a szolgáltatás a rendszer naplózza. Ha a GetNextJob állapotú = 0, a kiszolgáló nem tud kommunikálni a szolgáltatás. Ha GetNextJob hibával befejeződött, ellenőrizze a [hibaelhárítási dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) útmutatást.

A felhő rétegezési állapota
- Egy kiszolgálói rétegezési tevékenység monitorozásához használja Event ID 9003, 9016 és 9029 a telemetriai adatok eseménynaplóban (alkalmazások és az eseménynaplóban Services\Microsoft\FileSync\Agent alatt található).

  - 9003-es Azonosítójú esemény a kiszolgálói végpont hibaeloszlás biztosít. Ha például összesített hibaszám, ErrorCode, stb. Vegye figyelembe, hogy egy eseményt a rendszer naplózza hibakód.
  - Eseményazonosító 9016 másolatkészítési eredményeket biztosít egy köteten. Például a szabad terület százalékos értéke, a fájlok száma mögé munkamenetben, több fájlt nem sikerült ghost stb.
  - Eseményazonosító 9029 kiszolgálói végpont másolatkészítési munkamenet információkat nyújt. Például a munkamenet, a fájlok száma a megkísérelt fájlok száma rétegzett a munkamenetben, a fájlok száma már rétegzett, stb.
  
- A kiszolgálói tevékenység visszaírási monitorozásához használja Event ID 9005, 9006, 9009 és 9059 a telemetriai adatok eseménynaplóban (alkalmazások és az eseménynaplóban Services\Microsoft\FileSync\Agent alatt található).

  - 9005-es Azonosítójú esemény a kiszolgálói végpont visszaírási megbízhatóságot biztosít. Például teljes egyedi fájlok elérni, egyedi fájlok száma és a sikertelen hozzáférés, stb.
  - Eseményazonosító 9006 visszaírási hibaeloszlás a kiszolgálói végpont biztosít. Például összes sikertelen kérelem ErrorCode, stb. Vegye figyelembe, hogy egy eseményt a rendszer naplózza hibakód.
  - Eseményazonosító 9009 kiszolgálói végpont visszaírási munkamenet információkat nyújt. Például DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, stb.
  - Eseményazonosító 9059 alkalmazás visszaírási terjesztési a kiszolgálói végpont biztosít. Ha például ShareId, alkalmazás neve és TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Teljesítményszámlálók

A kiszolgálón az Azure File Sync teljesítményszámlálók használatával figyeli a szinkronizálási tevékenység.

Azure File Sync teljesítményét szeretné megtekinteni számlálókat a kiszolgálón, indítsa el a Teljesítményfigyelő (Perfmon.exe), és a számlálók a AFS bájt, és a szinkronizálási műveletek AFS objektumok területen találhatók.

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
