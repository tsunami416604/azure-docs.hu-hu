---
title: Azure Monitor a platform metrikái exportálható diagnosztikai beállítások használatával
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: fb11bf402ec671a46c191be0d8958c6a8a2c963d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134960"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor a platform metrikái exportálható diagnosztikai beállítások használatával

A Azure Monitor alapértelmezés szerint a [platform metrikáit](data-platform-metrics.md) biztosítja konfiguráció nélkül. Számos lehetőséget kínál a platform metrikáinak interakcióra, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. Lásd: [mérőszámok – támogatott](metrics-supported.md) a Azure monitor konszolidált metrikus folyamatával jelenleg elérhető platform-metrikák teljes listájához. A metrikák lekérdezéséhez és eléréséhez használja az [2018-01-01 API-Version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők.

Az Azure monitor folyamatának platform metrikáit kétféleképpen exportálhatja más helyekre.
1. [Diagnosztikai beállítások](diagnostic-settings.md) használata Log Analytics, Event Hubs vagy Azure Storage-ba történő küldéshez.
2. A [metrikák REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list) használata

A Azure Monitor-háttér bonyolult jellemzői miatt nem minden metrika exportálható a diagnosztikai beállítások használatával. Az alábbi táblázat felsorolja, hogy mely és nem exportálható a diagnosztikai beállítások használatával.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Váltás a NULLák és a nulla értékek viselkedésére 
 
A diagnosztikai beállítások használatával exportálható platform-metrikák esetében van néhány mérőszám, amelynek Azure Monitor a "0" értéket "NULL" értékre értelmezi. Ez némi zavart okozott a Real "0s" (erőforrás által kibocsátott) és az "0s" (nulls) értelmezése között. A diagnosztikai beállításokon keresztül exportált **, 2020-** es platform-mérőszámok már nem exportálják a "0s"-et, kivéve, ha az alapul szolgáló erőforrás valóban kibocsátotta azokat. Ne feledje:

1.  Ha töröl egy erőforráscsoportot vagy egy adott erőforrást, a rendszer nem küldi el az érintett erőforrásokból származó metrikai adatokat a diagnosztikai beállítások exportálási célhelyére. Ez azt eredményezi, hogy többé nem fog megjelenni a Event Hubs, a Storage-fiókokban és a Log Analytics-munkaterületeken.
2.  Ez a fejlesztés minden nyilvános és privát felhőben elérhető lesz.
3.  Ez a változás nem befolyásolja a következő tapasztalatok valamelyikének viselkedését: 
-   A platform erőforrás-naplói a diagnosztikai beállítások használatával lettek exportálva
-   Metrikák ábrázolási Metrikaböngésző
-   A platform metrikáinak riasztása
 
## <a name="metrics-exportable-table"></a>Metrika exportálható táblázat 

A tábla a következő oszlopokat tartalmazza. 
- Exportálható diagnosztikai beállításokkal? 
- ResourceType 
- Metrika 
- MetricDisplayName
- Unit (Egység) 
- AggregationType


> [!NOTE]
> Az alábbi táblázat a lenti vízszintes görgetősávot tartalmazhatja. Ha úgy véli, hogy hiányzik az információ, ellenőrizze, hogy a görgetősáv a bal oldalon van-e.  


Exportálható diagnosztikai beállításokkal? | ResourceType | Metrika | MetricDisplayName | Unit (Egység) | AggregationType
|----|-----|------|----|----|-----|
Igen | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | Memória: tisztító – aktuális ár | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | Memória: nem csökkenthető a tisztább memória | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | Memória: tisztító memória csökkenthető | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | Szálak: a parancssori készlet foglalt szálak | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | Szálak: parancssori készlet üresjárati szálai | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | Parancssori feladatok várólistájának hossza | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | Összege | Kapcsolat: aktuális kapcsolatok | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | CurrentUserSessions | Aktuális felhasználói munkamenetek | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | Szálak: hosszú elemzés – foglalt szálak | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | Szálak: tartós folyamatok elemzése – üresjárati szálak | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | Szálak: hosszú elemzési feladatok várólistájának hossza | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | M motor memóriája | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | M motor saját bájtjai | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | M motor QPU | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | M motor virtuális bájtjai | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | memory_metric | Memory (Memória) | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | memory_thrashing_metric | Memória-Kiverés | Százalék | Átlag
Igen | Microsoft.AnalysisServices/servers | MemoryLimitHard | Memória: rögzített memória | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | MemoryLimitHigh | Memória: magas a memória korlátozása | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | MemoryLimitLow | Memória: kevés a memória korlátja | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | Memória: VertiPaq korlátja | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | MemoryUsage | Memória: memóriahasználat | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | private_bytes_metric | Saját [nem megosztható] memória (bájt) | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | Szálak: feldolgozási készlet üresjáratban nem I/O-szálai | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | Feldolgozási készlet nyomtatási várólistájának hossza | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | qpu_metric | QPU | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | Lekérdezési készlet foglalt szálai | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | Szálak: lekérdezési készlet – üresjárati szálak | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | Szálak: lekérdezési készlet feladatok várólistájának hossza | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | Kvóta | Memória: kvóta | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | QuotaBlocked | Memória: blokkolt kvóta | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | Feldolgozás: másodpercenként konvertált sorok száma | CountPerSecond | Átlag
Igen | Microsoft.AnalysisServices/servers | RowsReadPerSec | Feldolgozás: másodpercenként beolvasott sorok száma | CountPerSecond | Átlag
Igen | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | Feldolgozás: másodpercenként írt sorok száma | CountPerSecond | Átlag
Igen | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | Szálak: rövid elemzés – foglalt szálak | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | Szálak: rövid elemzési üresjárati szálak | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | Szálak: rövid elemzési feladatok várólistájának hossza | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | Sikeres kapcsolatok másodpercenként | CountPerSecond | Átlag
Igen | Microsoft.AnalysisServices/servers | TotalConnectionFailures | Összes sikertelen Kapcsolatfelvétel | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | TotalConnectionRequests | Kapcsolatkérelmek teljes száma | Darabszám | Átlag
Igen | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | Memória: VertiPaq, nem lapozható | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | VertiPaqPaged | Memória: VertiPaq lapozható | Bájt | Átlag
Igen | Microsoft.AnalysisServices/servers | virtual_bytes_metric | Virtuális bájtok | Bájt | Átlag
Igen | Microsoft.ApiManagement/service | BackendDuration | A háttérbeli kérelmek időtartama | Ezredmásodperc | Átlag
Igen | Microsoft.ApiManagement/service | Kapacitás | Kapacitás | Százalék | Átlag
Igen | Microsoft.ApiManagement/service | Időtartam | Az átjárók kéréseinek teljes időtartama | Ezredmásodperc | Átlag
Igen | Microsoft.ApiManagement/service | EventHubDroppedEvents | Eldobott EventHub események | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | EventHubRejectedEvents | EventHub-események visszautasítva | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | Sikeres EventHub események | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | EventHubThrottledEvents | Szabályozott EventHub események | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | EventHubTimedoutEvents | Időtúllépés a EventHub eseményeinél | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | EventHubTotalBytesSent | EventHub-események mérete | Bájt | Összesen
Igen | Microsoft.ApiManagement/service | EventHubTotalEvents | Összes EventHub esemény | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | Sikertelen EventHub események | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | FailedRequests | Sikertelen átjáró-kérelmek (elavult) | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | OtherRequests | Egyéb átjáró-kérelmek (elavult) | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | Kérelmek | Kérelmek | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | SuccessfulRequests | Sikeres átjáró-kérelmek (elavult) | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | TotalRequests | Összes átjáró kérelme (elavult) | Darabszám | Összesen
Igen | Microsoft.ApiManagement/service | UnauthorizedRequests | Nem engedélyezett átjáró-kérelmek (elavult) | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | AppCpuUsagePercentage | Alkalmazás CPU-kihasználtságának százalékos aránya | Százalék | Átlag
Igen | Microsoft. AppPlatform/Spring | AppMemoryCommitted | Az alkalmazáshoz hozzárendelt memória | Bájt | Átlag
Igen | Microsoft. AppPlatform/Spring | AppMemoryMax | Az alkalmazás memóriájának maximális száma | Bájt | Maximum
Igen | Microsoft. AppPlatform/Spring | AppMemoryUsed | Használt alkalmazás memóriája | Bájt | Átlag
Igen | Microsoft. AppPlatform/Spring | GCPauseTotalCount | GC-szüneteltetések száma | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | GCPauseTotalTime | GC felfüggesztésének teljes ideje | Ezredmásodperc | Összesen
Igen | Microsoft. AppPlatform/Spring | MaxOldGenMemoryPoolBytes | A rendelkezésre álló régi generációs adatméret maximális száma | Bájt | Átlag
Igen | Microsoft. AppPlatform/Spring | OldGenMemoryPoolBytes | A régi generációs adatméret | Bájt | Átlag
Igen | Microsoft. AppPlatform/Spring | OldGenPromotedBytes | Előléptetés a régi generációs adatméretre | Bájt | Maximum
Igen | Microsoft. AppPlatform/Spring | SystemCpuUsagePercentage | CPU-használat százalékos aránya | Százalék | Átlag
Igen | Microsoft. AppPlatform/Spring | TomcatErrorCount | Tomcat globális hiba | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatReceivedBytes | Tomcat összesen fogadott bájtok | Bájt | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatRequestMaxTime | Tomcat-kérelem maximális ideje | Ezredmásodperc | Maximum
Igen | Microsoft. AppPlatform/Spring | TomcatRequestTotalCount | Tomcat-kérelem összesített száma | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatRequestTotalTime | Tomcat-kérelem teljes ideje | Ezredmásodperc | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatResponseAvgTime | Tomcat-kérelem átlagos ideje | Ezredmásodperc | Átlag
Igen | Microsoft. AppPlatform/Spring | TomcatSentBytes | Tomcat összesen eljuttatott bájtok száma | Bájt | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatSessionActiveCurrentCount | Tomcat-munkamenet élő száma | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatSessionActiveMaxCount | Tomcat-munkamenet maximális aktív száma | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatSessionAliveMaxTime | Tomcat-munkamenet maximális élettartama | Ezredmásodperc | Maximum
Igen | Microsoft. AppPlatform/Spring | TomcatSessionCreatedCount | Tomcat-munkamenet létrehozva szám | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatSessionExpiredCount | A Tomcat-munkamenet lejárt | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | TomcatSessionRejectedCount | A Tomcat-munkamenet elutasította a darabszámot | Darabszám | Összesen
Igen | Microsoft. AppPlatform/Spring | YoungGenPromotedBytes | Népszerűsítse a fiatal generáció adatméretét | Bájt | Maximum
Igen | Microsoft.Automation/automationAccounts | TotalJob | Feladatok összesen | Darabszám | Összesen
Igen | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | Összes frissítés központi telepítési gép futtatása | Darabszám | Összesen
Igen | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentRuns | Összes frissítés központi telepítési futtatása | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | CoreCount | Dedikált mag száma | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | CreatingNodeCount | Csomópontok számának létrehozása | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | IdleNodeCount | Tétlen csomópontok száma | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | Feladatok törlése – befejezett események | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | Feladatok törlésének indítási eseményei | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | A feladatok letiltják a teljes eseményeket | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | JobDisableStartEvent | Feladatok letiltásának indítási eseményei | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | JobStartEvent | Feladatok indítási eseményei | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | A feladatokhoz tartozó befejezett események befejezése | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | Feladatokból indított események leállítása | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | A készlet-csomópontok számának elhagyása | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | LowPriority mag száma | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | OfflineNodeCount | Offline csomópontok száma | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | PoolCreateEvent | Készlet-létrehozási események | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | Készlet törlése – befejezett események | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | Készlet törlése – indítási események | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | Készlet átméretezése – befejezett események | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | Készlet átméretezésének indítási eseményei | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | PreemptedNodeCount | Előzik-csomópontok száma | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | RebootingNodeCount | Csomópontok számának újraindítása | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | ReimagingNodeCount | Csomópontok rendszerképének alaphelyzetbe állítása | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | RunningNodeCount | Csomópontok száma | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | StartingNodeCount | Csomópontok számának indítása | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | Indítási feladat sikertelen csomópontok száma | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | TaskCompleteEvent | Feladat teljes eseményei | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | TaskFailEvent | Feladat sikertelen eseményei | Darabszám | Összesen
Igen | Microsoft.Batch/batchAccounts | TaskStartEvent | Tevékenységek indítási eseményei | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | Alacsony prioritású csomópontok száma | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | TotalNodeCount | Dedikált csomópontok száma | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | UnusableNodeCount | Nem használható csomópontok száma | Darabszám | Összesen
Nem | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | Várakozás a feladat-csomópontok számának megkezdésére | Darabszám | Összesen
Igen | Microsoft. BatchAI/munkaterületek | Aktív magok | Aktív magok | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Aktív csomópontok | Aktív csomópontok | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Üresjárati magok | Üresjárati magok | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Tétlen csomópontok | Tétlen csomópontok | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | A feladatok befejeződtek | A feladatok befejeződtek | Darabszám | Összesen
Igen | Microsoft. BatchAI/munkaterületek | Feladatok elküldve | Feladatok elküldve | Darabszám | Összesen
Igen | Microsoft. BatchAI/munkaterületek | Magok kihagyása | Magok kihagyása | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Csomópontok elhagyása | Csomópontok elhagyása | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Előzik magok | Előzik magok | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Előzik-csomópontok | Előzik-csomópontok | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Kvóta kihasználtsága (%) | Kvóta kihasználtsága (%) | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Magok összesen | Magok összesen | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Csomópontok összesen | Csomópontok összesen | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Használhatatlan magok | Használhatatlan magok | Darabszám | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Használhatatlan csomópontok | Használhatatlan csomópontok | Darabszám | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | ConnectionAccepted | Elfogadott kapcsolatok | Darabszám | Összesen
Igen | Microsoft. Blockchain/blockchainMembers | ConnectionActive | Aktív kapcsolatok | Darabszám | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | ConnectionHandled | Kezelt kapcsolatok | Darabszám | Összesen
Igen | Microsoft. Blockchain/blockchainMembers | CpuUsagePercentageInDouble | CPU-használat százaléka | Százalék | Maximum
Igen | Microsoft. Blockchain/blockchainMembers | IOReadBytes | IO olvasási bájtok | Bájt | Összesen
Igen | Microsoft. Blockchain/blockchainMembers | IOWriteBytes | IO írási bájtjai | Bájt | Összesen
Igen | Microsoft. Blockchain/blockchainMembers | Memóriakorlát | Memória korlátja | Bájt | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | MemoryUsage | Memóriahasználat | Bájt | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | MemoryUsagePercentageInDouble | Memóriahasználat százaléka | Százalék | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | PendingTransactions | Függőben lévő tranzakciók | Darabszám | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | ProcessedBlocks | Feldolgozott blokkok | Darabszám | Összesen
Igen | Microsoft. Blockchain/blockchainMembers | ProcessedTransactions | Feldolgozott tranzakciók | Darabszám | Összesen
Igen | Microsoft. Blockchain/blockchainMembers | QueuedTransactions | Várólistán lévő tranzakciók | Darabszám | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | RequestHandled | Kezelt kérelmek | Darabszám | Összesen
Igen | Microsoft. Blockchain/blockchainMembers | StorageUsage | Tárterület-használat | Bájt | Átlag
Igen | Microsoft.Cache/redis | cachehits | Gyorsítótár-találatok | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits0 | Gyorsítótárbeli találatok (szegmens 0) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits1 | Gyorsítótárbeli találatok (1. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits2 | Gyorsítótárbeli találatok (2. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits3 | Gyorsítótárbeli találatok (3. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits4 | Gyorsítótár-találatok (4. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits5 | Gyorsítótárbeli találatok (szegmens 5) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits6 | Gyorsítótárbeli találatok (szilánk 6) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits7 | Gyorsítótárbeli találatok (szegmens 7) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits8 | Gyorsítótárbeli találatok (szilánk 8) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachehits9 | Gyorsítótárbeli találatok (szilánk 9) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cacheLatency | Gyorsítótár késési másodpercek (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Cache/redis | cachemisses | Gyorsítótár-lemaradás | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses0 | Gyorsítótár-kihagyás (0. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses1 | Gyorsítótár-lemaradás (1. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses2 | Gyorsítótár-lemaradás (2. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses3 | Gyorsítótár-lemaradás (3. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses4 | Gyorsítótár-lemaradás (4. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses5 | Gyorsítótár-lemaradás (5. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses6 | Gyorsítótár-lemaradás (szegmens 6) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses7 | Gyorsítótár-lemaradás (7. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses8 | Gyorsítótár-lemaradás (szegmens 8) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cachemisses9 | Gyorsítótár-lemaradás (szegmens 9) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | cacheRead | Gyorsítótár-olvasás | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead0 | Gyorsítótár-olvasás (0. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead1 | Gyorsítótár olvasása (1. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead2 | Gyorsítótár olvasása (2. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead3 | Gyorsítótár olvasása (3. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead4 | Gyorsítótár olvasása (4. Szilánk) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead5 | Gyorsítótár olvasása (5. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead6 | Gyorsítótár olvasása (szegmens 6) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead7 | Gyorsítótár olvasása (szegmens 7) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead8 | Gyorsítótár olvasása (szilánk 8) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheRead9 | Gyorsítótár olvasása (szegmens 9) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite | Gyorsítótár írása | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite0 | Gyorsítótár-írás (0. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite1 | Gyorsítótár-írás (1. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite2 | Gyorsítótár-írás (2. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite3 | Gyorsítótár-írás (3. Szilánk) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite4 | Gyorsítótár-írás (4. Szilánk) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite5 | Gyorsítótár-írás (5. Szilánk) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite6 | Gyorsítótár írása (szegmens 6) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite7 | Gyorsítótár-írás (szegmens 7) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite8 | Gyorsítótár-írás (szegmens 8) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | cacheWrite9 | Gyorsítótár-írás (szegmens 9) | BytesPerSecond | Maximum
Igen | Microsoft.Cache/redis | connectedclients | Csatlakozott ügyfelek | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients0 | Csatlakoztatott ügyfelek (0. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients1 | Csatlakoztatott ügyfelek (1. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients2 | Csatlakoztatott ügyfelek (2. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients3 | Csatlakoztatott ügyfelek (3. Szilánk) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients4 | Csatlakoztatott ügyfelek (4. Szilánk) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients5 | Csatlakoztatott ügyfelek (5. Szilánk) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients6 | Csatlakoztatott ügyfelek (6. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients7 | Csatlakoztatott ügyfelek (7. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients8 | Csatlakoztatott ügyfelek (10. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | connectedclients9 | Csatlakoztatott ügyfelek (10. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | Hibák | Hibák | Darabszám | Maximum
Igen | Microsoft.Cache/redis | evictedkeys | Kizárt kulcsok | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys0 | Kizárt kulcsok (0. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys1 | Kizárt kulcsok (1. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys2 | Kizárt kulcsok (2. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys3 | Kizárt kulcsok (3. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys4 | Kizárt kulcsok (4. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys5 | Kizárt kulcsok (5. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys6 | Kizárt kulcsok (szegmens 6) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys7 | Kizárt kulcsok (7. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys8 | Kizárt kulcsok (szilánk 8) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | evictedkeys9 | Kizárt kulcsok (szilánk 9) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys | Lejárt kulcsok | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys0 | Lejárt kulcsok (szilánk 0) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys1 | Lejárt kulcsok (1. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys2 | Lejárt kulcsok (2. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys3 | Lejárt kulcsok (3. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys4 | Lejárt kulcsok (4. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys5 | Lejárt kulcsok (szilánk 5) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys6 | Lejárt kulcsok (szilánk 6) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys7 | Lejárt kulcsok (7. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys8 | Lejárt kulcsok (szilánk 8) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | expiredkeys9 | Lejárt kulcsok (szilánk 9) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands | Kap | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands0 | Beolvasás (0. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands1 | Beolvasás (1. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands2 | Beolvasás (2. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands3 | Beolvasás (3. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands4 | Beolvasás (4. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands5 | Beolvasás (szegmens 5) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands6 | Beolvasás (szilánk 6) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands7 | Beolvasás (szegmens 7) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands8 | Beolvasás (szilánk 8) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | getcommands9 | Beolvasás (szegmens 9) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | operationsPerSecond | Műveletek száma másodpercenként | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond0 | Művelet/másodperc (0. Szilánk) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond1 | Művelet/másodperc (1. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond2 | Művelet/másodperc (2. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond3 | Művelet/másodperc (3. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond4 | Művelet/másodperc (4. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond5 | Művelet/másodperc (5. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond6 | Művelet/másodperc (szegmens 6) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond7 | Művelet/másodperc (7. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond8 | Művelet/másodperc (szegmens 8) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | operationsPerSecond9 | Művelet/másodperc (szegmens 9) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime | CPU | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime0 | CPU (0. szegmens) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime1 | CPU (1. Szilánk) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime2 | CPU (2. szegmens) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime3 | CPU (3. Szilánk) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime4 | CPU (4. Szilánk) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime5 | CPU (5. Szilánk) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime6 | CPU (szegmens 6) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime7 | PROCESSZOR (7. szegmens) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime8 | CPU (szegmens 8) | Százalék | Maximum
Igen | Microsoft.Cache/redis | percentProcessorTime9 | CPU (szilánk 9) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad | Kiszolgáló terhelése | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad0 | Kiszolgáló terhelése (0. szegmens) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad1 | Kiszolgáló terhelése (1. szegmens) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad2 | Kiszolgáló betöltése (2. szegmens) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad3 | Kiszolgáló terhelése (3. Szilánk) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad4 | Kiszolgáló terhelése (4. Szilánk) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad5 | Kiszolgáló terhelése (5. Szilánk) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad6 | Kiszolgáló betöltése (szegmens 6) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad7 | Kiszolgáló terhelése (7. szegmens) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad8 | Kiszolgáló betöltése (szegmens 8) | Százalék | Maximum
Igen | Microsoft.Cache/redis | serverLoad9 | Kiszolgáló terhelése (szegmens 9) | Százalék | Maximum
Igen | Microsoft.Cache/redis | setcommands | Beállítja | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands0 | Készletek (0. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands1 | Készletek (1. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands2 | Készletek (2. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands3 | Készletek (3. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands4 | Készletek (4. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands5 | Készletek (szilánk 5) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands6 | Készletek (szilánk 6) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands7 | Készletek (szegmens 7) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands8 | Készletek (szilánk 8) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | setcommands9 | Készletek (szilánk 9) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed | Összes művelet | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed0 | Összes művelet (0. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed1 | Összes művelet (1. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed2 | Összes művelet (2. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed3 | Összes művelet (3. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed4 | Összes művelet (4. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed5 | Összes művelet (5. Szilánk) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed6 | Összes művelet (szegmens 6) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed7 | Összes művelet (7. szegmens) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed8 | Összes művelet (szegmens 8) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalcommandsprocessed9 | Összes művelet (szegmens 9) | Darabszám | Összesen
Igen | Microsoft.Cache/redis | totalkeys | Kulcsok összesen | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys0 | Összes kulcs (szilánk 0) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys1 | Összes kulcs (1. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys2 | Összes kulcs (2. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys3 | Összes kulcs (3. Szilánk) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys4 | Összes kulcs (4. Szilánk) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys5 | Összes kulcs (5. szegmens) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys6 | Összes kulcs (szilánk 6) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys7 | Összes kulcs (szegmens 7) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys8 | Összes kulcs (szilánk 8) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | totalkeys9 | Összes kulcs (szilánk 9) | Darabszám | Maximum
Igen | Microsoft.Cache/redis | usedmemory | Felhasznált memória | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory0 | Felhasznált memória (0. Szilánk) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory1 | Felhasznált memória (1. Szilánk) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory2 | Felhasznált memória (2. Szilánk) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory3 | Felhasznált memória (3. Szilánk) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory4 | Felhasznált memória (4. Szilánk) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory5 | Felhasznált memória (5. Szilánk) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory6 | Felhasznált memória (6. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory7 | Felhasznált memória (7. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory8 | Felhasznált memória (8. Szilánk) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemory9 | Felhasznált memória (10. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemorypercentage | Felhasznált memória százalékos aránya | Százalék | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss | Felhasznált memória RSS-címe | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss0 | Használt memória RSS-je (0. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss1 | Használt memória RSS-je (1. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss2 | Használt memória RSS-je (2. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss3 | Használt memória RSS-je (3. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss4 | Használt memória RSS-je (4. Szilánk) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss5 | Használt memória RSS-je (5. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss6 | Használt memória RSS-je (3. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss7 | Használt memória RSS-je (7. szegmens) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss8 | Használt memória RSS-je (szegmens 8) | Bájt | Maximum
Igen | Microsoft.Cache/redis | usedmemoryRss9 | Használt memória RSS-je (szegmens 9) | Bájt | Maximum
Nem | Microsoft.ClassicCompute/domainNames/slots/roles | Lemez olvasási sebessége (bájt/s) | Lemez olvasása | BytesPerSecond | Átlag
Igen | Microsoft.ClassicCompute/domainNames/slots/roles | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Nem | Microsoft.ClassicCompute/domainNames/slots/roles | Lemez írási sebessége (bájt/s) | Lemez írása | BytesPerSecond | Átlag
Igen | Microsoft.ClassicCompute/domainNames/slots/roles | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft.ClassicCompute/domainNames/slots/roles | Bejövő hálózat | Bejövő hálózat | Bájt | Összesen
Igen | Microsoft.ClassicCompute/domainNames/slots/roles | Kimenő hálózat | Kimenő hálózat | Bájt | Összesen
Igen | Microsoft.ClassicCompute/domainNames/slots/roles | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Nem | Microsoft.ClassicCompute/virtualMachines | Lemez olvasási sebessége (bájt/s) | Lemez olvasása | BytesPerSecond | Átlag
Igen | Microsoft.ClassicCompute/virtualMachines | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Nem | Microsoft.ClassicCompute/virtualMachines | Lemez írási sebessége (bájt/s) | Lemez írása | BytesPerSecond | Átlag
Igen | Microsoft.ClassicCompute/virtualMachines | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft.ClassicCompute/virtualMachines | Bejövő hálózat | Bejövő hálózat | Bájt | Összesen
Igen | Microsoft.ClassicCompute/virtualMachines | Kimenő hálózat | Kimenő hálózat | Bájt | Összesen
Igen | Microsoft.ClassicCompute/virtualMachines | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft.ClassicStorage/storageAccounts | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Igen | Microsoft.ClassicStorage/storageAccounts | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Igen | Microsoft.ClassicStorage/storageAccounts | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft.ClassicStorage/storageAccounts | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft.ClassicStorage/storageAccounts | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft.ClassicStorage/storageAccounts | Tranzakciók | Tranzakciók | Darabszám | Összesen
Nem | Microsoft.ClassicStorage/storageAccounts | UsedCapacity | Használt kapacitás | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/blobServices | BlobCapacity | Blob-kapacitása | Bájt | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/blobServices | BlobCount | Blobok száma | Darabszám | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | ContainerCount | Blobtárolók száma | Darabszám | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Nem | Microsoft. ClassicStorage/storageAccounts/blobServices | IndexCapacity | Index kapacitása | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | Tranzakciók | Tranzakciók | Darabszám | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileCapacity | Fájl kapacitása | Bájt | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileCount | Fájlok száma | Darabszám | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareCount | Fájlmegosztás száma | Darabszám | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareQuota | Fájlmegosztás kvótájának mérete | Bájt | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareSnapshotCount | Fájlmegosztás pillanatképének száma | Darabszám | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareSnapshotSize | Fájlmegosztás pillanatképének mérete | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | Tranzakciók | Tranzakciók | Darabszám | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueCapacity | Queue Storage kapacitása | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueCount | Üzenetsorok száma | Darabszám | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueMessageCount | Üzenetsorbeli üzenetek száma | Darabszám | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | Tranzakciók | Tranzakciók | Darabszám | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | TableCapacity | Table Storage kapacitása | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | TableCount | Táblák száma | Darabszám | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | TableEntityCount | Táblaentitások száma | Darabszám | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | Tranzakciók | Tranzakciók | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | BlockedCalls | Blokkolt hívások | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | CharactersTrained | Betanított karakterek | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | CharactersTranslated | Lefordított karakterek | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | ClientErrors | Ügyfél-hibák | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | DataIn | A-ben tárolt adatértékek | Bájt | Összesen
Igen | Microsoft.CognitiveServices/accounts | DataOut | Kimenő adatvesztés | Bájt | Összesen
Igen | Microsoft.CognitiveServices/accounts | Késés | Késés | Ezredmásodpercben | Átlag
Igen | Microsoft.CognitiveServices/accounts | Kiszolgálóhibái | Kiszolgálói hibák | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | Beszédfelismerési munkamenet időtartama | Másodperc | Összesen
Igen | Microsoft.CognitiveServices/accounts | SuccessfulCalls | Sikeres hívások | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | TotalCalls | Hívások összesen | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | TotalErrors | Összes hiba | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | TotalTokenCalls | Jogkivonat-hívások összesen | Darabszám | Összesen
Igen | Microsoft.CognitiveServices/accounts | TotalTransactions | Tranzakciók összesen | Darabszám | Összesen
Igen | Microsoft.Compute/virtualMachines | Felhasznált CPU-kreditek | Felhasznált CPU-kreditek | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachines | Fennmaradó CPU-kreditek | Fennmaradó CPU-kreditek | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez várólistájának mélysége | Adatlemez-várólista mélysége (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez-olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez olvasási műveletei (művelet/s) | Adatlemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez-írási sebesség (bájt/s) | Adatlemez-írási sebesség (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez írási műveletei (művelet/s) | Adatlemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Lemez olvasási bájtjai | Lemez olvasási bájtjai | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachines | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Lemez írási bájtjai | Lemez írási bájtjai | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachines | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Bejövő folyamatok | Bejövő folyamatok | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachines | Bejövő folyamatok maximális létrehozási aránya | Bejövő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Bejövő hálózat | Számlázandó hálózat (elavult) | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachines | Teljes hálózat | Teljes hálózat | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachines | Kimenő hálózat | Hálózati kimenő számlázandó (elavult) | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachines | Kimenő hálózat összesen | Kimenő hálózat összesen | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez olvasási művelete/s | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez írási műveletei/s | OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez QD | OPERÁCIÓSRENDSZER-lemez QD (elavult) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez olvasási művelet/mp | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez írási műveletei másodpercenként | OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Kimenő folyamatok | Kimenő folyamatok | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachines | Kimenő folyamatok maximális létrehozási aránya | Kimenő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez QD | Adatlemez QD (elavult) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez olvasási művelet/mp | Adatlemez olvasási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez írási sebessége (bájt/s) | Adatlemez-írási sebesség (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez írási műveletek másodpercenként | Adatlemez írási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachines | Prémium szintű adatlemez-gyorsítótár – olvasási találat | Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachines | Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás | Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachines | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachines | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Felhasznált CPU-kreditek | Felhasznált CPU-kreditek | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Fennmaradó CPU-kreditek | Fennmaradó CPU-kreditek | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez várólistájának mélysége | Adatlemez-várólista mélysége (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez-olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez olvasási műveletei (művelet/s) | Adatlemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez-írási sebesség (bájt/s) | Adatlemez-írási sebesség (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez írási műveletei (művelet/s) | Adatlemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Lemez olvasási bájtjai | Lemez olvasási bájtjai | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Lemez írási bájtjai | Lemez írási bájtjai | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Bejövő folyamatok | Bejövő folyamatok | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Bejövő folyamatok maximális létrehozási aránya | Bejövő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Bejövő hálózat | Számlázandó hálózat (elavult) | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets | Teljes hálózat | Teljes hálózat | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets | Kimenő hálózat | Hálózati kimenő számlázandó (elavult) | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets | Kimenő hálózat összesen | Kimenő hálózat összesen | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez olvasási művelete/s | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez írási műveletei/s | OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez QD | OPERÁCIÓSRENDSZER-lemez QD (elavult) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez olvasási művelet/mp | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez írási műveletei másodpercenként | OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Kimenő folyamatok | Kimenő folyamatok | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Kimenő folyamatok maximális létrehozási aránya | Kimenő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez QD | Adatlemez QD (elavult) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez olvasási művelet/mp | Adatlemez olvasási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez írási sebessége (bájt/s) | Adatlemez-írási sebesség (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez írási műveletek másodpercenként | Adatlemez írási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Prémium szintű adatlemez-gyorsítótár – olvasási találat | Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás | Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Felhasznált CPU-kreditek | Felhasznált CPU-kreditek | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Fennmaradó CPU-kreditek | Fennmaradó CPU-kreditek | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Adatlemez várólistájának mélysége | Adatlemez-várólista mélysége (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Adatlemez-olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Adatlemez olvasási műveletei (művelet/s) | Adatlemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Adatlemez-írási sebesség (bájt/s) | Adatlemez-írási sebesség (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Adatlemez írási műveletei (művelet/s) | Adatlemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lemez olvasási bájtjai | Lemez olvasási bájtjai | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lemez írási bájtjai | Lemez írási bájtjai | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bejövő folyamatok | Bejövő folyamatok | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bejövő folyamatok maximális létrehozási aránya | Bejövő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bejövő hálózat | Számlázandó hálózat (elavult) | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Teljes hálózat | Teljes hálózat | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Kimenő hálózat | Hálózati kimenő számlázandó (elavult) | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Kimenő hálózat összesen | Kimenő hálózat összesen | Bájt | Összesen
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez olvasási művelete/s | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez írási műveletei/s | OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez QD | OPERÁCIÓSRENDSZER-lemez QD (elavult) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez olvasási művelet/mp | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez írási műveletei másodpercenként | OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Kimenő folyamatok | Kimenő folyamatok | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Kimenő folyamatok maximális létrehozási aránya | Kimenő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | /Lemez QD | Adatlemez QD (elavult) | Darabszám | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | /Lemez olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | /Lemez olvasási művelet/mp | Adatlemez olvasási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | /Lemez írási sebessége (bájt/s) | Adatlemez-írási sebesség (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | /Lemez írási műveletek másodpercenként | Adatlemez írási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Prémium szintű adatlemez-gyorsítótár – olvasási találat | Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás | Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.ContainerInstance/containerGroups | CpuUsage | CPU-használat | Darabszám | Átlag
Igen | Microsoft.ContainerInstance/containerGroups | MemoryUsage | Memóriahasználat | Bájt | Átlag
Igen | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | Másodpercenként fogadott hálózati bájtok száma | Bájt | Átlag
Igen | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | Másodpercenként továbbított hálózati bájtok száma | Bájt | Átlag
Igen | Microsoft.ContainerRegistry/registries | RunDuration | Futtatás időtartama | Ezredmásodperc | Összesen
Igen | Microsoft.ContainerRegistry/registries | SuccessfulPullCount | Sikeres lekérések száma | Darabszám | Átlag
Igen | Microsoft.ContainerRegistry/registries | SuccessfulPushCount | Sikeres leküldések száma | Darabszám | Átlag
Igen | Microsoft.ContainerRegistry/registries | TotalPullCount | Lekérések száma összesen | Darabszám | Átlag
Igen | Microsoft.ContainerRegistry/registries | TotalPushCount | Leküldések száma összesen | Darabszám | Átlag
Nem | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma | Darabszám | Átlag
Nem | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | A felügyelt fürtben rendelkezésre álló memória teljes mennyisége | Bájt | Átlag
Nem | Microsoft.ContainerService/managedClusters | kube_node_status_condition | Különböző csomóponti feltételek állapota | Darabszám | Átlag
Nem | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | Hüvelyek száma fázis szerint | Darabszám | Átlag
Nem | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | A hüvelyek száma üzemkész állapotban | Darabszám | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | Availablecapacity;) | Rendelkezésre álló kapacitás | Bájt | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | Feltöltött Felhőbeli bájtok (eszköz) | Bájt | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | Felhőbeli feltöltött bájtok (megosztás) | Bájt | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | Felhőbeli letöltési sebesség | BytesPerSecond | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | Felhőbeli letöltési teljesítmény (megosztás) | BytesPerSecond | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | Felhőbeli feltöltési sebesség | BytesPerSecond | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | Felhőbeli feltöltési sebesség (megosztás) | BytesPerSecond | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Edge-számítás – memóriahasználat | Százalék | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Edge-számítás – százalékos CPU | Százalék | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | Olvasási sebesség (hálózat) | BytesPerSecond | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | Írási átviteli sebesség (hálózat) | BytesPerSecond | Átlag
Igen | Microsoft.DataBoxEdge/dataBoxEdgeDevices | TotalCapacity | Teljes kapacitás | Bájt | Átlag
Igen | Microsoft.DataFactory/datafactories | FailedRuns | Sikertelen futtatások | Darabszám | Összesen
Igen | Microsoft.DataFactory/datafactories | SuccessfulRuns | Sikeres futtatások | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | ActivityCancelledRuns | A megszakított tevékenység metrikákat futtat | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | ActivityFailedRuns | Sikertelen tevékenység-futtatási metrikák | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | ActivitySucceededRuns | A sikeres tevékenység metrikákat futtat | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | FactorySizeInGbUnits | Gyári méret összesen (GB egység) | Darabszám | Maximum
Igen | Microsoft.DataFactory/factories | IntegrationRuntimeAvailableMemory | Az Integration Runtime rendelkezésre álló memóriája | Bájt | Átlag
Igen | Microsoft.DataFactory/factories | IntegrationRuntimeAverageTaskPickupDelay | Integration Runtime-várólista időtartama | Másodperc | Átlag
Igen | Microsoft.DataFactory/factories | IntegrationRuntimeCpuPercentage | Integration Runtime CPU-kihasználtsága | Százalék | Átlag
Igen | Microsoft.DataFactory/factories | IntegrationRuntimeQueueLength | Integration Runtime-várólista hossza | Darabszám | Átlag
Igen | Microsoft.DataFactory/factories | MaxAllowedFactorySizeInGbUnits | Maximálisan engedélyezett gyári méret (GB egység) | Darabszám | Maximum
Igen | Microsoft.DataFactory/factories | MaxAllowedResourceCount | Engedélyezett entitások maximális száma | Darabszám | Maximum
Igen | Microsoft.DataFactory/factories | PipelineCancelledRuns | Megszakított folyamat-futtatási metrikák | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | PipelineFailedRuns | Sikertelen folyamat-futtatási metrikák | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | PipelineSucceededRuns | A folyamat sikeresen futtatja a metrikákat | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | ResourceCount | Entitások száma összesen | Darabszám | Maximum
Igen | Microsoft.DataFactory/factories | TriggerCancelledRuns | Megszakított trigger-futtatási metrikák | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | TriggerFailedRuns | Sikertelen trigger-futtatási metrikák | Darabszám | Összesen
Igen | Microsoft.DataFactory/factories | TriggerSucceededRuns | A sikeres trigger metrikákat futtat | Darabszám | Összesen
Igen | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | Megszakított AU-idő | Másodperc | Összesen
Igen | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | Sikertelen AU-idő | Másodperc | Összesen
Igen | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | Sikeres AU-idő | Másodperc | Összesen
Igen | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | Megszakított feladatok | Darabszám | Összesen
Igen | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | Sikertelen feladatok | Darabszám | Összesen
Igen | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | Sikeres feladatok | Darabszám | Összesen
Igen | Microsoft.DataLakeStore/accounts | DataRead | Olvasott információk | Bájt | Összesen
Igen | Microsoft.DataLakeStore/accounts | DataWritten | Írt adatértékek | Bájt | Összesen
Igen | Microsoft.DataLakeStore/accounts | ReadRequests | Olvasási kérelmek | Darabszám | Összesen
Igen | Microsoft.DataLakeStore/accounts | TotalStorage | Teljes tárterület | Bájt | Maximum
Igen | Microsoft.DataLakeStore/accounts | WriteRequests | Írási kérelmek | Darabszám | Összesen
Igen | Microsoft.DBforMariaDB/servers | active_connections | Aktív kapcsolatok | Darabszám | Átlag
Igen | Microsoft.DBforMariaDB/servers | backup_storage_used | Felhasznált biztonsági mentési tár | Bájt | Átlag
Igen | Microsoft.DBforMariaDB/servers | connections_failed | Sikertelen kapcsolatok | Darabszám | Összesen
Igen | Microsoft.DBforMariaDB/servers | cpu_percent | CPU-százalék | Százalék | Átlag
Igen | Microsoft.DBforMariaDB/servers | io_consumption_percent | IO-százalék | Százalék | Átlag
Igen | Microsoft.DBforMariaDB/servers | memory_percent | Memória százaléka | Százalék | Átlag
Igen | Microsoft.DBforMariaDB/servers | network_bytes_egress | Kimenő hálózat | Bájt | Összesen
Igen | Microsoft.DBforMariaDB/servers | network_bytes_ingress | Bejövő hálózat | Bájt | Összesen
Igen | Microsoft.DBforMariaDB/servers | seconds_behind_master | Replikálás késése másodpercben | Darabszám | Maximum
Igen | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | Kiszolgáló naplójának tárolási korlátja | Bájt | Átlag
Igen | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | Kiszolgáló naplójának tárolási százaléka | Százalék | Átlag
Igen | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | Kiszolgáló naplójának tárolója | Bájt | Átlag
Igen | Microsoft.DBforMariaDB/servers | storage_limit | Tárolási korlát | Bájt | Maximum
Igen | Microsoft.DBforMariaDB/servers | storage_percent | Tárolási százalék | Százalék | Átlag
Igen | Microsoft.DBforMariaDB/servers | storage_used | Felhasznált tárterület | Bájt | Átlag
Igen | Microsoft.DBforMySQL/servers | active_connections | Aktív kapcsolatok | Darabszám | Átlag
Igen | Microsoft.DBforMySQL/servers | backup_storage_used | Felhasznált biztonsági mentési tár | Bájt | Átlag
Igen | Microsoft.DBforMySQL/servers | connections_failed | Sikertelen kapcsolatok | Darabszám | Összesen
Igen | Microsoft.DBforMySQL/servers | cpu_percent | CPU-százalék | Százalék | Átlag
Igen | Microsoft.DBforMySQL/servers | io_consumption_percent | IO-százalék | Százalék | Átlag
Igen | Microsoft.DBforMySQL/servers | memory_percent | Memória százaléka | Százalék | Átlag
Igen | Microsoft.DBforMySQL/servers | network_bytes_egress | Kimenő hálózat | Bájt | Összesen
Igen | Microsoft.DBforMySQL/servers | network_bytes_ingress | Bejövő hálózat | Bájt | Összesen
Igen | Microsoft.DBforMySQL/servers | seconds_behind_master | Replikálás késése másodpercben | Darabszám | Maximum
Igen | Microsoft.DBforMySQL/servers | serverlog_storage_limit | Kiszolgáló naplójának tárolási korlátja | Bájt | Maximum
Igen | Microsoft.DBforMySQL/servers | serverlog_storage_percent | Kiszolgáló naplójának tárolási százaléka | Százalék | Átlag
Igen | Microsoft.DBforMySQL/servers | serverlog_storage_usage | Kiszolgáló naplójának tárolója | Bájt | Átlag
Igen | Microsoft.DBforMySQL/servers | storage_limit | Tárolási korlát | Bájt | Maximum
Igen | Microsoft.DBforMySQL/servers | storage_percent | Tárolási százalék | Százalék | Átlag
Igen | Microsoft.DBforMySQL/servers | storage_used | Felhasznált tárterület | Bájt | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | active_connections | Aktív kapcsolatok | Darabszám | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | backup_storage_used | Felhasznált biztonsági mentési tár | Bájt | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | connections_failed | Sikertelen kapcsolatok | Darabszám | Összesen
Igen | Microsoft.DBforPostgreSQL/servers | cpu_percent | CPU-százalék | Százalék | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | IO-százalék | Százalék | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | memory_percent | Memória százaléka | Százalék | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | Kimenő hálózat | Bájt | Összesen
Igen | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | Bejövő hálózat | Bájt | Összesen
Igen | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | Replikák maximális késése | Bájt | Maximum
Igen | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | Replika késése | Másodperc | Maximum
Igen | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | Kiszolgáló naplójának tárolási korlátja | Bájt | Maximum
Igen | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | Kiszolgáló naplójának tárolási százaléka | Százalék | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | Kiszolgáló naplójának tárolója | Bájt | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | storage_limit | Tárolási korlát | Bájt | Maximum
Igen | Microsoft.DBforPostgreSQL/servers | storage_percent | Tárolási százalék | Százalék | Átlag
Igen | Microsoft.DBforPostgreSQL/servers | storage_used | Felhasznált tárterület | Bájt | Átlag
Igen | Microsoft.DBforPostgreSQL/serversv2 | active_connections | Aktív kapcsolatok | Darabszám | Átlag
Igen | Microsoft.DBforPostgreSQL/serversv2 | cpu_percent | CPU-százalék | Százalék | Átlag
Igen | Microsoft.DBforPostgreSQL/serversv2 | IOPS | IOPS | Darabszám | Átlag
Igen | Microsoft.DBforPostgreSQL/serversv2 | memory_percent | Memória százaléka | Százalék | Átlag
Igen | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_egress | Kimenő hálózat | Bájt | Összesen
Igen | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_ingress | Bejövő hálózat | Bájt | Összesen
Igen | Microsoft.DBforPostgreSQL/serversv2 | storage_percent | Tárolási százalék | Százalék | Átlag
Igen | Microsoft.DBforPostgreSQL/serversv2 | storage_used | Felhasznált tárterület | Bájt | Átlag
Igen | Microsoft. Devices/Account | digitaltwins. telemetria. csomópontok | Digitális Twins-csomópont telemetria helyőrzője | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | C2D üzenetek elhagyva | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | C2D-üzenetek kézbesítésének befejezése | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | C2D-üzenetek elutasítva | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | C2D. Methods. failure | Sikertelen közvetlen metódusok meghívása | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | c2d.methods.requestSize | A közvetlen metódus meghívásának mérete | Bájt | Átlag
Igen | Microsoft.Devices/IotHubs | c2d.methods.responseSize | A közvetlen metódus-meghívások válaszának mérete | Bájt | Átlag
Igen | Microsoft.Devices/IotHubs | c2d.methods.success | Közvetlen metódusok sikeres meghívása | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | c2d.twin.read.failure | Sikertelen dupla olvasások a háttérből | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | c2d.twin.read.size | Dupla olvasások válaszának mérete a háttérből | Bájt | Átlag
Igen | Microsoft.Devices/IotHubs | c2d.twin.read.success | Sikeres dupla olvasások a háttérből | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | c2d.twin.update.failure | Sikertelen dupla frissítések a háttérből | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | c2d.twin.update.size | Dupla frissítések mérete a háttérből | Bájt | Átlag
Igen | Microsoft.Devices/IotHubs | c2d.twin.update.success | Sikeres dupla frissítések a háttérből | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | C2DMessagesExpired | C2D-üzenetek lejárt (előzetes verzió) | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | konfigurációk | Konfigurációs mérőszámok | Darabszám | Összesen
Nem | Microsoft.Devices/IotHubs | connectedDeviceCount | Csatlakoztatott eszközök (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | Útválasztás: üzenetek/események küldésére küldött üzenetek | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | Útválasztás: az Event hub számára továbbított üzenetek | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | Útválasztás: Service Bus üzenetsor számára továbbított üzenetek | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | Útválasztás: Service Bus témakörbe küldött üzenetek | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | Útválasztás: a tárolóba küldött üzenetek | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | Útválasztás: tárolóba szállított Blobok | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | Útválasztás: a tárolóba szállított adatmennyiség | Bájt | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | Útválasztás: üzenetek/események üzenetének késése | Ezredmásodperc | Átlag
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | Útválasztás: az Event hub üzenetének késése | Ezredmásodperc | Átlag
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | Útválasztás: Service Bus üzenetsor üzenet-késése | Ezredmásodperc | Átlag
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | Útválasztás: Service Bus témakör üzenetének késése | Ezredmásodperc | Átlag
Igen | Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage | Útválasztás: üzenetek késése a tároláshoz | Ezredmásodperc | Átlag
Igen | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | Útválasztás: telemetria üzenetek elvetve  | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | Útválasztás: tartalékként továbbított üzenetek | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | Útválasztás: Inkompatibilis telemetria-üzenetek | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | Útválasztás: árva telemetria üzenetek  | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | Útválasztás: telemetria üzenetek kézbesítése | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | Telemetria üzenetek küldése | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | Szabályozási hibák száma | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | Telemetria üzenetek elküldése | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.twin.read.failure | Sikertelen dupla olvasások az eszközökről | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.twin.read.size | Az eszközökről érkező kettős olvasások válaszának mérete | Bájt | Átlag
Igen | Microsoft.Devices/IotHubs | d2c.twin.read.success | Sikeres dupla olvasások az eszközökről | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.twin.update.failure | Sikertelen dupla frissítések az eszközökről | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | d2c.twin.update.size | Az eszközökből származó kettős frissítések mérete | Bájt | Átlag
Igen | Microsoft.Devices/IotHubs | d2c.twin.update.success | Sikeres dupla frissítések az eszközökről | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | A felhasznált üzenetek teljes száma | Darabszám | Átlag
Igen | Microsoft.Devices/IotHubs | deviceDataUsage | Az eszköz összes adatfelhasználása | Bájt | Összesen
Igen | Microsoft.Devices/IotHubs | deviceDataUsageV2 | Az eszköz összes adatfelhasználása (előzetes verzió) | Bájt | Összesen
Igen | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | Csatlakoztatott eszközök (elavult)  | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | Devices. totalDevices | Összes eszköz (elavult) | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | EventGridDeliveries | Event Grid kézbesítések (előzetes verzió) | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | EventGridLatency | Event Grid késés (előzetes verzió) | Ezredmásodperc | Átlag
Igen | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | Sikertelen feladatok megszakítása | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.cancelJob.success | Sikeres feladatok törlése | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | feladatok. kész | Befejezett feladatok | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | Nem sikerült létrehozni a metódus Meghívási feladatait | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | Metódus-Meghívási feladatok sikeres létrehozása | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | A kettős frissítési feladatok sikertelen létrehozása | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | A kettős frissítési feladatok sikeres létrehozása | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | feladatok. sikertelen | Sikertelen feladatok | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.listJobs.failure | Sikertelen hívások a feladatok listázásához | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.listJobs.success | Sikeres hívások a feladatok listázásához | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | Sikertelen feladatok lekérdezése | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | jobs.queryJobs.success | Sikeres feladatok lekérdezése | Darabszám | Összesen
Nem | Microsoft.Devices/IotHubs | totalDeviceCount | Összes eszköz (előzetes verzió) | Darabszám | Átlag
Igen | Microsoft.Devices/IotHubs | twinQueries.failure | Sikertelen dupla lekérdezések | Darabszám | Összesen
Igen | Microsoft.Devices/IotHubs | twinQueries.resultSize | Dupla lekérdezések eredményének mérete | Bájt | Átlag
Igen | Microsoft.Devices/IotHubs | twinQueries.success | Sikeres Twin-lekérdezések | Darabszám | Összesen
Igen | Microsoft.Devices/provisioningServices | AttestationAttempts | Igazolási kísérletek | Darabszám | Összesen
Igen | Microsoft.Devices/provisioningServices | DeviceAssignments | Hozzárendelt eszközök | Darabszám | Összesen
Igen | Microsoft.Devices/provisioningServices | RegistrationAttempts | Regisztrációs kísérletek | Darabszám | Összesen
Nem | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | Rendelkezésre álló tár | Bájt | Összesen
Nem | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Cassandra-kapcsolatok bezárása | Darabszám | Összesen
Nem | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Cassandra-kérelmek díjai | Darabszám | Összesen
Nem | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Cassandra-kérelmek | Darabszám | Darabszám
Nem | Microsoft.DocumentDB/databaseAccounts | DataUsage | Adathasználat | Bájt | Összesen
Igen | Microsoft.DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | Darabszám | Darabszám
Nem | Microsoft.DocumentDB/databaseAccounts | DocumentCount | Dokumentumok száma | Darabszám | Összesen
Nem | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | Dokumentum kvótája | Bájt | Összesen
Nem | Microsoft.DocumentDB/databaseAccounts | IndexUsage | Indexelés használata | Bájt | Összesen
Nem | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | Metaadat-kérelmek | Darabszám | Darabszám
Igen | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | Mongo-kérelem díja | Darabszám | Összesen
Igen | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Mongo kérelmek | Darabszám | Darabszám
Nem | Microsoft.DocumentDB/databaseAccounts | MongoRequestsCount | Mongo kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft.DocumentDB/databaseAccounts | MongoRequestsDelete | Mongo-törlési kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft.DocumentDB/databaseAccounts | MongoRequestsInsert | Mongo-beszúrási kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft.DocumentDB/databaseAccounts | MongoRequestsQuery | Mongo-lekérdezési kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft.DocumentDB/databaseAccounts | MongoRequestsUpdate | Mongo-frissítési kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | Kiosztott átviteli sebesség | Darabszám | Maximum
Igen | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | P99 replikáció késése | Ezredmásodpercben | Átlag
Nem | Microsoft.DocumentDB/databaseAccounts | ServiceAvailability | Szolgáltatás rendelkezésre állása | Százalék | Átlag
Igen | Microsoft.DocumentDB/databaseAccounts | TotalRequests | Összes kérelem | Darabszám | Darabszám
Igen | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | Kérelmek összes egysége | Darabszám | Összesen
Nem | Microsoft. EnterpriseKnowledgeGraph/szolgáltatások | FailureCount | Hibák száma | Darabszám | Darabszám
Nem | Microsoft. EnterpriseKnowledgeGraph/szolgáltatások | SuccessCount | Sikeres műveletek száma | Darabszám | Darabszám
Nem | Microsoft. EnterpriseKnowledgeGraph/szolgáltatások | SuccessLatency | Sikeres késés | Ezredmásodpercben | Átlag
Nem | Microsoft. EnterpriseKnowledgeGraph/szolgáltatások | TransactionCount | Tranzakciók száma | Darabszám | Darabszám
Igen | Microsoft.EventGrid/domains | DeadLetteredCount | Kézbesítetlen levelek eseményei | Darabszám | Összesen
Nem | Microsoft.EventGrid/domains | DeliveryAttemptFailCount | Sikertelen kézbesítések eseményei | Darabszám | Összesen
Igen | Microsoft.EventGrid/domains | DeliverySuccessCount | Kézbesítési események | Darabszám | Összesen
Nem | Microsoft.EventGrid/domains | DestinationProcessingDurationInMs | Cél feldolgozási időtartama | Ezredmásodperc | Átlag
Igen | Microsoft.EventGrid/domains | DroppedEventCount | Eldobott események | Darabszám | Összesen
Igen | Microsoft.EventGrid/domains | MatchedEventCount | Egyeztetett események | Darabszám | Összesen
Igen | Microsoft.EventGrid/domains | PublishFailCount | Sikertelen események közzététele | Darabszám | Összesen
Igen | Microsoft.EventGrid/domains | PublishSuccessCount | Közzétett események | Darabszám | Összesen
Igen | Microsoft.EventGrid/domains | PublishSuccessLatencyInMs | Sikeres közzétételi késés | Darabszám | Összesen
Igen | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | Kézbesítetlen levelek eseményei | Darabszám | Összesen
Nem | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | Sikertelen kézbesítések eseményei | Darabszám | Összesen
Igen | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | Kézbesítési események | Darabszám | Összesen
Nem | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | Cél feldolgozási időtartama | Ezredmásodperc | Átlag
Igen | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | Eldobott események | Darabszám | Összesen
Igen | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | Egyeztetett események | Darabszám | Összesen
Igen | Microsoft.EventGrid/extensionTopics | PublishFailCount | Sikertelen események közzététele | Darabszám | Összesen
Igen | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | Közzétett események | Darabszám | Összesen
Igen | Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs | Sikeres közzétételi késés | Darabszám | Összesen
Igen | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | Páratlan események | Darabszám | Összesen
Igen | Microsoft.EventGrid/topics | PublishFailCount | Sikertelen események közzététele | Darabszám | Összesen
Igen | Microsoft.EventGrid/topics | PublishSuccessCount | Közzétett események | Darabszám | Összesen
Igen | Microsoft.EventGrid/topics | PublishSuccessLatencyInMs | Sikeres közzétételi késés | Darabszám | Összesen
Igen | Microsoft.EventGrid/topics | UnmatchedEventCount | Páratlan események | Darabszám | Összesen
Nem | Microsoft.EventHub/clusters | ActiveConnections | ActiveConnections | Darabszám | Átlag
Nem | Microsoft.EventHub/clusters | AvailableMemory | Szabad memória | Százalék | Maximum
Nem | Microsoft.EventHub/clusters | CaptureBacklog | Várakozó fájlok rögzítése. | Darabszám | Összesen
Nem | Microsoft.EventHub/clusters | CapturedBytes | Rögzített bájtok száma. | Bájt | Összesen
Nem | Microsoft.EventHub/clusters | CapturedMessages | Rögzített üzenetek. | Darabszám | Összesen
Nem | Microsoft.EventHub/clusters | ConnectionsClosed | A kapcsolatok lezárva. | Darabszám | Átlag
Nem | Microsoft.EventHub/clusters | ConnectionsOpened | Megnyitott kapcsolatok. | Darabszám | Átlag
Nem | Microsoft.EventHub/clusters | CPU | CPU | Százalék | Maximum
Igen | Microsoft.EventHub/clusters | IncomingBytes | Bejövő bájtok. | Bájt | Összesen
Igen | Microsoft.EventHub/clusters | IncomingMessages | Bejövő üzenetek | Darabszám | Összesen
Igen | Microsoft.EventHub/clusters | IncomingRequests | Bejövő kérelmek | Darabszám | Összesen
Igen | Microsoft.EventHub/clusters | OutgoingBytes | Kimenő bájtok. | Bájt | Összesen
Igen | Microsoft.EventHub/clusters | OutgoingMessages | Kimenő üzenetek | Darabszám | Összesen
Nem | Microsoft.EventHub/clusters | QuotaExceededErrors | A kvóta túllépte a hibákat. | Darabszám | Összesen
Nem | Microsoft.EventHub/clusters | Kiszolgálóhibái | Kiszolgálói hibák. | Darabszám | Összesen
Nem | Microsoft.EventHub/clusters | SuccessfulRequests | Sikeres kérések | Darabszám | Összesen
Nem | Microsoft.EventHub/clusters | ThrottledRequests | Szabályozott kérelmek. | Darabszám | Összesen
Nem | Microsoft.EventHub/clusters | UserErrors | Felhasználói hibák. | Darabszám | Összesen
Nem | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | Darabszám | Átlag
Nem | Microsoft.EventHub/namespaces | CaptureBacklog | Várakozó fájlok rögzítése. | Darabszám | Összesen
Nem | Microsoft.EventHub/namespaces | CapturedBytes | Rögzített bájtok száma. | Bájt | Összesen
Nem | Microsoft.EventHub/namespaces | CapturedMessages | Rögzített üzenetek. | Darabszám | Összesen
Nem | Microsoft.EventHub/namespaces | ConnectionsClosed | A kapcsolatok lezárva. | Darabszám | Átlag
Nem | Microsoft.EventHub/namespaces | ConnectionsOpened | Megnyitott kapcsolatok. | Darabszám | Átlag
Igen | Microsoft.EventHub/namespaces | EHABL | Archivált várakozó üzenetek (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | EHAMBS | Archivált üzenetek átviteli sebessége (elavult) | Bájt | Összesen
Igen | Microsoft.EventHub/namespaces | EHAMSGS | Archivált üzenetek (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | EHINBYTES | Bejövő bájtok (elavult) | Bájt | Összesen
Igen | Microsoft.EventHub/namespaces | EHINMBS | Bejövő bájtok (elavult) (elavult) | Bájt | Összesen
Igen | Microsoft.EventHub/namespaces | EHINMSGS | Bejövő üzenetek (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | EHOUTBYTES | Kimenő bájtok (elavult) | Bájt | Összesen
Igen | Microsoft.EventHub/namespaces | EHOUTMBS | Kimenő bájtok (elavult) (elavult) | Bájt | Összesen
Igen | Microsoft.EventHub/namespaces | EHOUTMSGS | Kimenő üzenetek (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | FAILREQ | Sikertelen kérelmek (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | IncomingBytes | Bejövő bájtok. | Bájt | Összesen
Igen | Microsoft.EventHub/namespaces | IncomingMessages | Bejövő üzenetek | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | IncomingRequests | Bejövő kérelmek | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | INMSGS | Bejövő üzenetek (elavult) (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | INREQS | Bejövő kérelmek (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | INTERer | Belső kiszolgálói hibák (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | MISCERR | Egyéb hibák (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | OutgoingBytes | Kimenő bájtok. | Bájt | Összesen
Igen | Microsoft.EventHub/namespaces | OutgoingMessages | Kimenő üzenetek | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | OUTMSGS | Kimenő üzenetek (elavult) (elavult) | Darabszám | Összesen
Nem | Microsoft.EventHub/namespaces | QuotaExceededErrors | A kvóta túllépte a hibákat. | Darabszám | Összesen
Nem | Microsoft.EventHub/namespaces | Kiszolgálóhibái | Kiszolgálói hibák. | Darabszám | Összesen
Nem | Microsoft.EventHub/namespaces | Méret | Méret | Bájt | Átlag
Nem | Microsoft.EventHub/namespaces | SuccessfulRequests | Sikeres kérések | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | SUCCREQ | Sikeres kérések (elavult) | Darabszám | Összesen
Igen | Microsoft.EventHub/namespaces | SVRBSY | Kiszolgáló által foglalt hibák (elavult) | Darabszám | Összesen
Nem | Microsoft.EventHub/namespaces | ThrottledRequests | Szabályozott kérelmek. | Darabszám | Összesen
Nem | Microsoft.EventHub/namespaces | UserErrors | Felhasználói hibák. | Darabszám | Összesen
Igen | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | Kategorizált átjáró-kérelmek | Darabszám | Összesen
Igen | Microsoft.HDInsight/clusters | GatewayRequests | Átjáróra vonatkozó kérelmek | Darabszám | Összesen
Igen | Microsoft.HDInsight/clusters | NumActiveWorkers | Aktív feldolgozók száma | Darabszám | Maximum
Igen | Microsoft.HDInsight/clusters | ScalingRequests | Kérelmek méretezése | Darabszám | Maximum
Igen | Microsoft.Insights/AutoscaleSettings | MetricThreshold | Metrika küszöbértéke | Darabszám | Átlag
Igen | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | Megfigyelt kapacitás | Darabszám | Átlag
Igen | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | Megfigyelt metrika értéke | Darabszám | Átlag
Igen | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | Elindított skálázási műveletek | Darabszám | Összesen
Igen | Microsoft.Insights/Components | availabilityResults/availabilityPercentage | Rendelkezésre állás | Százalék | Átlag
Nem | Microsoft.Insights/Components | availabilityResults/darabszám | Rendelkezésre állási tesztek | Darabszám | Darabszám
Igen | Microsoft.Insights/Components | availabilityResults/időtartam | Rendelkezésre állási teszt időtartama | Ezredmásodpercben | Átlag
Igen | Microsoft.Insights/Components | browserTimings/networkDuration | Oldal betöltési hálózati kapcsolati ideje | Ezredmásodpercben | Átlag
Igen | Microsoft.Insights/Components | browserTimings/processingDuration | Ügyfél feldolgozási ideje | Ezredmásodpercben | Átlag
Igen | Microsoft.Insights/Components | browserTimings/receiveDuration | Válaszidő fogadása | Ezredmásodpercben | Átlag
Igen | Microsoft.Insights/Components | browserTimings/sendDuration | Kérelem küldési ideje | Ezredmásodpercben | Átlag
Igen | Microsoft.Insights/Components | browserTimings/totalDuration | Böngésző oldalának betöltési ideje | Ezredmásodpercben | Átlag
Nem | Microsoft.Insights/Components | függőségek/darabszám | Függőségi hívások | Darabszám | Darabszám
Igen | Microsoft.Insights/Components | függőségek/időtartam | Függőség időtartama | Ezredmásodpercben | Átlag
Nem | Microsoft.Insights/Components | függőségek/sikertelen | Függőségi hívások hibái | Darabszám | Darabszám
Nem | Microsoft.Insights/Components | kivételek/böngésző | Böngészőkivételek | Darabszám | Darabszám
Igen | Microsoft.Insights/Components | kivételek/darabszám | Kivételek | Darabszám | Darabszám
Nem | Microsoft.Insights/Components | kivételek/kiszolgáló | Kiszolgálói kivételek | Darabszám | Darabszám
Igen | Microsoft.Insights/Components | Oldalmegtekintések/darabszám | Lapok nézetei | Darabszám | Darabszám
Igen | Microsoft.Insights/Components | pageViews/duration | Oldal nézet betöltési ideje | Ezredmásodpercben | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond | Kivételek aránya | CountPerSecond | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/memoryAvailableBytes | Rendelkezésre álló memória | Bájt | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/processCpuPercentage | Processzor feldolgozása | Százalék | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/processIOBytesPerSecond | Folyamat i/o-sebessége | BytesPerSecond | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/processorCpuPercentage | Processzoridő | Százalék | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/processPrivateBytes | Saját bájtok feldolgozása | Bájt | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/requestExecutionTime | HTTP-kérelem végrehajtási ideje | Ezredmásodpercben | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/requestsInQueue | HTTP-kérelmek az alkalmazás-várólistában | Darabszám | Átlag
Igen | Microsoft.Insights/Components | performanceCounters/requestsPerSecond | HTTP-kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft.Insights/Components | kérelmek/darabszám | Kiszolgálói kérelmek | Darabszám | Darabszám
Igen | Microsoft.Insights/Components | kérelmek/időtartam | Kiszolgáló válaszideje | Ezredmásodpercben | Átlag
Nem | Microsoft.Insights/Components | kérelmek/sikertelen | Sikertelen kérelmek | Darabszám | Darabszám
Nem | Microsoft.Insights/Components | kérelmek/díjszabás | Kiszolgálói kérelmek gyakorisága | CountPerSecond | Átlag
Igen | Microsoft.Insights/Components | nyomkövetés/darabszám | Nyomok | Darabszám | Darabszám
Igen | Microsoft.KeyVault/vaults | ServiceApiHit | Összes szolgáltatási API-találat | Darabszám | Darabszám
Igen | Microsoft.KeyVault/vaults | ServiceApiLatency | Általános szolgáltatás API-késése | Ezredmásodperc | Átlag
Igen | Microsoft.KeyVault/vaults | ServiceApiResult | Összes szolgáltatás API-eredményei | Darabszám | Darabszám
Igen | Microsoft.Kusto/Clusters | CacheUtilization | Gyorsítótár kihasználtsága | Százalék | Átlag
Igen | Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes | Folyamatos exportálás maximális késői percben | Darabszám | Maximum
Igen | Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported | Folyamatos exportálás – számú exportált rekord | Darabszám | Összesen
Igen | Microsoft.Kusto/Clusters | ContinuousExportPendingCount | Folyamatos exportálás függőben lévő darabszáma | Darabszám | Maximum
Igen | Microsoft.Kusto/Clusters | ContinuousExportResult | Folyamatos exportálás eredménye | Darabszám | Darabszám
Igen | Microsoft.Kusto/Clusters | CPU | CPU | Százalék | Átlag
Igen | Microsoft.Kusto/Clusters | EventsProcessedForEventHubs | Feldolgozott események (Event/IoT hubok esetében) | Darabszám | Összesen
Igen | Microsoft.Kusto/Clusters | ExportUtilization | Exportálás kihasználtsága | Százalék | Maximum
Igen | Microsoft.Kusto/Clusters | IngestionLatencyInSeconds | Betöltési késleltetés (másodperc) | Másodperc | Átlag
Igen | Microsoft.Kusto/Clusters | IngestionResult | Betöltés eredménye | Darabszám | Darabszám
Igen | Microsoft.Kusto/Clusters | IngestionUtilization | Betöltés kihasználtsága | Százalék | Átlag
Igen | Microsoft.Kusto/Clusters | IngestionVolumeInMB | Betöltési mennyiség (MB) | Darabszám | Összesen
Igen | Microsoft.Kusto/Clusters | KeepAlive | Életben tartása | Darabszám | Átlag
Igen | Microsoft.Kusto/Clusters | QueryDuration | Lekérdezés időtartama | Ezredmásodperc | Átlag
Igen | Microsoft.Kusto/Clusters | SteamingIngestRequestRate | Folyamatos átviteli kérelmek gyakorisága | Darabszám | RateRequestsPerSecond
Igen | Microsoft.Kusto/Clusters | StreamingIngestDataRate | Adatforgalom adatátviteli sebessége | Darabszám | Átlag
Igen | Microsoft.Kusto/Clusters | StreamingIngestDuration | Folyamatos átvitel időtartama | Ezredmásodperc | Átlag
Igen | Microsoft.Kusto/Clusters | StreamingIngestResults | Folyamatos átvitel eredménye | Darabszám | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | ActionLatency | Művelet késése  | Másodperc | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted | Befejezett műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | ActionsFailed | Sikertelen műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped | Kihagyott műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | ActionsStarted | Elindított műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded | Sikeres műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | ActionSuccessLatency | Művelet sikerességi késése  | Másodperc | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents | Művelet által szabályozott események | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | Összekötő memóriahasználat integrációs szolgáltatási környezet | Százalék | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | integrációs szolgáltatási környezet összekötő-processzorának használata | Százalék | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | integrációs szolgáltatási környezet munkafolyamat-memóriahasználat | Százalék | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | A integrációs szolgáltatási környezet munkafolyamat-processzorának használata | Százalék | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage | Sikertelen futtatások százalékos aránya | Százalék | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | RunLatency | Késés futtatása | Másodperc | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | RunsCancelled | Megszakított futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | RunsCompleted | Befejezett futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | RunsFailed | Sikertelen futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | RunsStarted | Elindított futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded | Sikeres futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents | Indítási leszabályozású események futtatása | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | RunSuccessLatency | Sikeres Futtatás késése | Másodperc | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents | Szabályozott események futtatása | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggerFireLatency | Kiváltó tűz késése  | Másodperc | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggerLatency | Trigger késése  | Másodperc | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted | Befejezett eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggersFailed | Sikertelen eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggersFired | Kilőtt eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped | Kihagyott eseményindítók | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggersStarted | Elindított eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded | Sikeres eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggerSuccessLatency | Sikeres triggerek késése  | Másodperc | Átlag
Igen | Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents | Kiváltott események indítása | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | ActionLatency | Művelet késése  | Másodperc | Átlag
Igen | Microsoft.Logic/workflows | ActionsCompleted | Befejezett műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | ActionsFailed | Sikertelen műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | ActionsSkipped | Kihagyott műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | ActionsStarted | Elindított műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | ActionsSucceeded | Sikeres műveletek  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | ActionSuccessLatency | Művelet sikerességi késése  | Másodperc | Átlag
Igen | Microsoft.Logic/workflows | ActionThrottledEvents | Művelet által szabályozott események | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | BillableActionExecutions | Számlázható műveletek végrehajtása | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | BillableTriggerExecutions | Számlázható trigger-végrehajtások | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | BillingUsageNativeOperation | A natív művelet végrehajtásához használt számlázási használat | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | BillingUsageNativeOperation | A natív művelet végrehajtásához használt számlázási használat | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | BillingUsageStandardConnector | A standard szintű összekötők végrehajtásának számlázási használata | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | BillingUsageStandardConnector | A standard szintű összekötők végrehajtásának számlázási használata | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Számlázási használat a tárolási felhasználás végrehajtásához | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Számlázási használat a tárolási felhasználás végrehajtásához | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | RunFailurePercentage | Sikertelen futtatások százalékos aránya | Százalék | Összesen
Igen | Microsoft.Logic/workflows | RunLatency | Késés futtatása | Másodperc | Átlag
Igen | Microsoft.Logic/workflows | RunsCancelled | Megszakított futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | RunsCompleted | Befejezett futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | RunsFailed | Sikertelen futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | RunsStarted | Elindított futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | RunsSucceeded | Sikeres futtatások | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | RunStartThrottledEvents | Indítási leszabályozású események futtatása | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | RunSuccessLatency | Sikeres Futtatás késése | Másodperc | Átlag
Igen | Microsoft.Logic/workflows | RunThrottledEvents | Szabályozott események futtatása | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | TotalBillableExecutions | Számlázandó végrehajtások összesen | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | TriggerFireLatency | Kiváltó tűz késése  | Másodperc | Átlag
Igen | Microsoft.Logic/workflows | TriggerLatency | Trigger késése  | Másodperc | Átlag
Igen | Microsoft.Logic/workflows | TriggersCompleted | Befejezett eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | TriggersFailed | Sikertelen eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | TriggersFired | Kilőtt eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | TriggersSkipped | Kihagyott eseményindítók | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | TriggersStarted | Elindított eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | TriggersSucceeded | Sikeres eseményindítók  | Darabszám | Összesen
Igen | Microsoft.Logic/workflows | TriggerSuccessLatency | Sikeres triggerek késése  | Másodperc | Átlag
Igen | Microsoft.Logic/workflows | TriggerThrottledEvents | Kiváltott események indítása | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | Aktív magok | Aktív magok | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Aktív csomópontok | Aktív csomópontok | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Befejezett futtatások | Befejezett futtatások | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | Sikertelen futtatások | Sikertelen futtatások | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | Üresjárati magok | Üresjárati magok | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Tétlen csomópontok | Tétlen csomópontok | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Magok kihagyása | Magok kihagyása | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Csomópontok elhagyása | Csomópontok elhagyása | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Modell-üzembehelyezés sikertelen | Modell-üzembehelyezés sikertelen | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | Modell-üzembehelyezés elindítva | Modell-üzembehelyezés elindítva | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | Modell-üzembehelyezés sikerült | Modell-üzembehelyezés sikerült | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | A modell regisztrálása nem sikerült | A modell regisztrálása nem sikerült | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | A modell regisztrálása sikerült | A modell regisztrálása sikerült | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | Előzik magok | Előzik magok | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Előzik-csomópontok | Előzik-csomópontok | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Kvóta kihasználtsága (%) | Kvóta kihasználtsága (%) | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Elindított futtatások | Elindított futtatások | Darabszám | Összesen
Igen | Microsoft. MachineLearningServices/munkaterületek | Magok összesen | Magok összesen | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Csomópontok összesen | Csomópontok összesen | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Használhatatlan magok | Használhatatlan magok | Darabszám | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Használhatatlan csomópontok | Használhatatlan csomópontok | Darabszám | Átlag
Igen | Microsoft. Maps/fiókok | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Nem | Microsoft. Maps/fiókok | Használat | Használat | Darabszám | Darabszám
Igen | Microsoft. Media/Mediaservices | AssetCount | Eszközök száma | Darabszám | Átlag
Igen | Microsoft. Media/Mediaservices | AssetQuota | Eszköz kvótája | Darabszám | Átlag
Igen | Microsoft. Media/Mediaservices | AssetQuotaUsedPercentage | Eszköz kvótájának kihasználtsága (%) | Százalék | Átlag
Igen | Microsoft. Media/Mediaservices | ContentKeyPolicyCount | Tartalmi kulcsokra vonatkozó szabályzatok száma | Darabszám | Átlag
Igen | Microsoft. Media/Mediaservices | ContentKeyPolicyQuota | Tartalmi kulcs házirend-kvótája | Darabszám | Átlag
Igen | Microsoft. Media/Mediaservices | ContentKeyPolicyQuotaUsedPercentage | Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya | Százalék | Átlag
Igen | Microsoft. Media/Mediaservices | StreamingPolicyCount | Folyamatos átviteli szabályzatok száma | Darabszám | Átlag
Igen | Microsoft. Media/Mediaservices | StreamingPolicyQuota | Streaming Policy-kvóta | Darabszám | Átlag
Igen | Microsoft. Media/Mediaservices | StreamingPolicyQuotaUsedPercentage | Adatfolyam-házirend kvótájának kihasználtsága (%) | Százalék | Átlag
Igen | Microsoft. Media/Mediaservices/streamingEndpoints | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Igen | Microsoft. Media/Mediaservices/streamingEndpoints | Kérelmek | Kérelmek | Darabszám | Összesen
Igen | Microsoft. Media/Mediaservices/streamingEndpoints | SuccessE2ELatency | A Befejezés végének késése | Ezredmásodperc | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | GCPauseTotalCount | GC-szüneteltetések száma | Darabszám | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | GCPauseTotalTime | GC felfüggesztésének teljes ideje | Ezredmásodperc | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | A rendelkezésre álló régi generációs adatméret maximális száma | Bájt | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | OldGenMemoryPoolBytes | A régi generációs adatméret | Bájt | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | OldGenPromotedBytes | Előléptetés a régi generációs adatméretre | Bájt | Maximum
Igen | Microsoft. Microservices4Spring/appClusters | ServiceCpuUsagePercentage | Szolgáltatás CPU-kihasználtságának százalékos aránya | Százalék | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | ServiceMemoryCommitted | Szolgáltatáshoz hozzárendelt memória | Bájt | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | ServiceMemoryMax | Szolgáltatás memóriájának maximális száma | Bájt | Maximum
Igen | Microsoft. Microservices4Spring/appClusters | ServiceMemoryUsed | Szolgáltatás felhasznált memóriája | Bájt | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | SystemCpuUsagePercentage | CPU-használat százalékos aránya | Százalék | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | TomcatErrorCount | Tomcat globális hiba | Darabszám | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatReceivedBytes | Tomcat összesen fogadott bájtok | Bájt | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatRequestMaxTime | Tomcat-kérelem maximális ideje | Ezredmásodperc | Maximum
Igen | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalCount | Tomcat-kérelem összesített száma | Darabszám | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalTime | Tomcat-kérelem teljes ideje | Ezredmásodperc | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatResponseAvgTime | Tomcat-kérelem átlagos ideje | Ezredmásodperc | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSentBytes | Tomcat összesen eljuttatott bájtok száma | Bájt | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Tomcat-munkamenet élő száma | Darabszám | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Tomcat-munkamenet maximális aktív száma | Darabszám | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Tomcat-munkamenet maximális élettartama | Ezredmásodperc | Maximum
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionCreatedCount | Tomcat-munkamenet létrehozva szám | Darabszám | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionExpiredCount | A Tomcat-munkamenet lejárt | Darabszám | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionRejectedCount | A Tomcat-munkamenet elutasította a darabszámot | Darabszám | Összesen
Igen | Microsoft. Microservices4Spring/appClusters | YoungGenPromotedBytes | Népszerűsítse a fiatal generáció adatméretét | Bájt | Maximum
Igen | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | Lefoglalt kötet-készlet | Bájt | Átlag
Igen | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | Kötet készletének teljes logikai mérete | Bájt | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | AverageReadLatency | Olvasási késleltetés átlagos késése | Ezredmásodpercben | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | AverageWriteLatency | Írási késleltetés átlagos késése | Ezredmásodpercben | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | ReadIops | IOPS olvasása | CountPerSecond | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | VolumeLogicalSize | Kötet logikai mérete | Bájt | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | VolumeSnapshotSize | Kötet pillanatképének mérete | Bájt | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | WriteIops | IOPS írása | CountPerSecond | Átlag
Nem | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | Application Gateway teljes idő | Ezredmásodpercben | Átlag
Nem | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | Percenkénti kérelmek száma kifogástalan állapotú gazdagépen | Darabszám | Átlag
Nem | Microsoft.Network/applicationGateways | BackendConnectTime | Háttérbeli kapcsolat ideje | Ezredmásodpercben | Átlag
Nem | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | Háttérbeli első bájt válaszideje | Ezredmásodpercben | Átlag
Nem | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | Háttérbeli utolsó bájt válaszideje | Ezredmásodpercben | Átlag
Igen | Microsoft.Network/applicationGateways | BackendResponseStatus | Háttérbeli válasz állapota | Darabszám | Összesen
Igen | Microsoft.Network/applicationGateways | BlockedCount | Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása | Darabszám | Összesen
Igen | Microsoft.Network/applicationGateways | BlockedReqCount | Webalkalmazási tűzfal letiltott kérelmek száma | Darabszám | Összesen
Igen | Microsoft.Network/applicationGateways | BytesReceived | Fogadott bájtok száma | Bájt | Összesen
Igen | Microsoft.Network/applicationGateways | BytesSent | Eljuttatott bájtok | Bájt | Összesen
Nem | Microsoft.Network/applicationGateways | CapacityUnits | Aktuális kapacitási egységek | Darabszám | Átlag
Nem | Microsoft.Network/applicationGateways | ClientRtt | Ügyfél-RTT | Ezredmásodpercben | Átlag
Nem | Microsoft.Network/applicationGateways | ComputeUnits | Aktuális számítási egységek | Darabszám | Átlag
Igen | Microsoft.Network/applicationGateways | Összege | Aktuális kapcsolatok | Darabszám | Összesen
Igen | Microsoft.Network/applicationGateways | FailedRequests | Sikertelen kérelmek | Darabszám | Összesen
Igen | Microsoft.Network/applicationGateways | HealthyHostCount | Kifogástalan állapotú gazdagépek száma | Darabszám | Átlag
Igen | Microsoft.Network/applicationGateways | MatchedCount | Webalkalmazási tűzfal teljes szabályának eloszlása | Darabszám | Összesen
Igen | Microsoft.Network/applicationGateways | ResponseStatus | Válasz állapota | Darabszám | Összesen
Nem | Microsoft.Network/applicationGateways | Átviteli sebesség | Átviteli sebesség | BytesPerSecond | Átlag
Igen | Microsoft.Network/applicationGateways | TlsProtocol | Ügyfél TLS protokoll | Darabszám | Összesen
Igen | Microsoft.Network/applicationGateways | TotalRequests | Összes kérelem | Darabszám | Összesen
Igen | Microsoft.Network/applicationGateways | UnhealthyHostCount | Nem kifogástalan állapotú gazdagépek száma | Darabszám | Átlag
Igen | Microsoft. Network/azurefirewalls | ApplicationRuleHit | Alkalmazás-szabályok találatok száma | Darabszám | Összesen
Igen | Microsoft. Network/azurefirewalls | DataProcessed | Feldolgozott adatfeldolgozás | Bájt | Összesen
Igen | Microsoft. Network/azurefirewalls | FirewallHealth | Tűzfal állapota | Százalék | Átlag
Igen | Microsoft. Network/azurefirewalls | NetworkRuleHit | Hálózati szabályok találatok száma | Darabszám | Összesen
Igen | Microsoft. Network/azurefirewalls | SNATPortUtilization | SNAT-portok kihasználtsága | Százalék | Átlag
Igen | Microsoft. Network/Connections | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/Connections | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Igen | Microsoft.Network/dnszones | QueryVolume | Lekérdezési kötet | Darabszám | Összesen
Nem | Microsoft.Network/dnszones | RecordSetCapacityUtilization | Rekordazonosító kapacitásának kihasználtsága | Százalék | Maximum
Igen | Microsoft.Network/dnszones | RecordSetCount | Rekordok készletének száma | Darabszám | Maximum
Igen | Microsoft.Network/expressRouteCircuits | ArpAvailability | ARP rendelkezésre állása | Százalék | Átlag
Igen | Microsoft.Network/expressRouteCircuits | BgpAvailability | BGP rendelkezésre állása | Százalék | Átlag
Nem | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Nem | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Nem | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | CountPerSecond | Átlag
Nem | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | CountPerSecond | Átlag
Nem | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | CountPerSecond | Átlag
Nem | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | CountPerSecond | Átlag
Igen | Microsoft.Network/expressRouteCircuits/peerings | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Igen | Microsoft.Network/expressRouteCircuits/peerings | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/expressRoutePorts | AdminState | AdminState | Darabszám | Átlag
Igen | Microsoft. Network/expressRoutePorts | LineProtocol | LineProtocol | Darabszám | Átlag
Igen | Microsoft. Network/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/expressRoutePorts | RxLightLevel | RxLightLevel | Darabszám | Átlag
Igen | Microsoft. Network/expressRoutePorts | TxLightLevel | TxLightLevel | Darabszám | Átlag
Igen | Microsoft. Network/frontdoors | BackendHealthPercentage | Háttér állapotának százalékos aránya | Százalék | Átlag
Igen | Microsoft. Network/frontdoors | BackendRequestCount | Háttérbeli kérelmek száma | Darabszám | Összesen
Igen | Microsoft. Network/frontdoors | BackendRequestLatency | Háttérbeli kérelmek késése | Ezredmásodpercben | Átlag
Igen | Microsoft. Network/frontdoors | BillableResponseSize | Számlázható válasz mérete | Bájt | Összesen
Igen | Microsoft. Network/frontdoors | RequestCount | Kérelmek száma | Darabszám | Összesen
Igen | Microsoft. Network/frontdoors | RequestSize | Kérelem mérete | Bájt | Összesen
Igen | Microsoft. Network/frontdoors | ResponseSize | Válasz mérete | Bájt | Összesen
Igen | Microsoft. Network/frontdoors | TotalLatency | Teljes késés | Ezredmásodpercben | Átlag
Igen | Microsoft. Network/frontdoors | WebApplicationFirewallRequestCount | Webalkalmazási tűzfalra vonatkozó kérelmek száma | Darabszám | Összesen
Nem | Microsoft.Network/loadBalancers | AllocatedSnatPorts | Lefoglalt SNAT portok (előzetes verzió) | Darabszám | Összesen
Igen | Microsoft.Network/loadBalancers | ByteCount | Bájtok száma | Darabszám | Összesen
Igen | Microsoft.Network/loadBalancers | DipAvailability | Állapot mintavételi állapota | Darabszám | Átlag
Igen | Microsoft.Network/loadBalancers | PacketCount | Csomagok száma | Darabszám | Összesen
Igen | Microsoft.Network/loadBalancers | SnatConnectionCount | SNAT-kapcsolatok száma | Darabszám | Összesen
Igen | Microsoft.Network/loadBalancers | SYNCount | SYN-szám | Darabszám | Összesen
Nem | Microsoft.Network/loadBalancers | UsedSnatPorts | Használt SNAT-portok (előzetes verzió) | Darabszám | Összesen
Igen | Microsoft.Network/loadBalancers | VipAvailability | Adatelérési út rendelkezésre állása | Darabszám | Átlag
Igen | Microsoft.Network/networkInterfaces | BytesReceivedRate | Fogadott bájtok száma | Bájt | Összesen
Igen | Microsoft.Network/networkInterfaces | BytesSentRate | Eljuttatott bájtok | Bájt | Összesen
Igen | Microsoft.Network/networkInterfaces | PacketsReceivedRate | Fogadott csomagok | Darabszám | Összesen
Igen | Microsoft.Network/networkInterfaces | PacketsSentRate | Küldött csomagok | Darabszám | Összesen
Igen | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | Átlagos menetidő (MS) | Ezredmásodpercben | Átlag
Igen | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | Sikertelen ellenőrzések százalékos aránya (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | %-Os mintavétel sikertelen | Százalék | Átlag
Igen | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | Oda-és visszaút időpontja (MS) (előzetes verzió) | Ezredmásodpercben | Átlag
Igen | Microsoft.Network/publicIPAddresses | ByteCount | Bájtok száma | Darabszám | Összesen
Igen | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | Bejövő bájtok elvetve DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | Bejövő bájtok továbbított DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | BytesInDDoS | Bejövő bájtok DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | Bejövő SYN-csomagok a DDoS-elhárítás elindításához | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | Bejövő TCP-csomagok a DDoS-mérséklés indításához | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | Bejövő UDP-csomagok a DDoS-elhárítás elindításához | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | DDoS-támadás alatt vagy nem | Darabszám | Maximum
Igen | Microsoft.Network/publicIPAddresses | PacketCount | Csomagok száma | Darabszám | Összesen
Igen | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | Bejövő csomagok eldobott DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | Bejövő csomagok továbbított DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | PacketsInDDoS | Bejövő csomagok DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | SynCount | SYN-szám | Darabszám | Összesen
Igen | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | Bejövő TCP-bájtok elvetve DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | Bejövő TCP-bájtok továbbított DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | Bejövő TCP-bájtok DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | Bejövő TCP-csomagok eldobott DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | Bejövő TCP-csomagok továbbított DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | Bejövő TCP-csomagok DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | Bejövő UDP-bájtok elvetve DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | Bejövő UDP-bájtok továbbított DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | Bejövő UDP bájtok DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | Bejövő UDP-csomagok eldobott DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | Bejövő UDP-csomagok továbbított DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | Bejövő UDP-csomagok DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | VipAvailability | Adatelérési út rendelkezésre állása | Darabszám | Átlag
Igen | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | Végponti állapot végpont szerint | Darabszám | Maximum
Igen | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | Visszaadott végponti lekérdezések | Darabszám | Összesen
Igen | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | Átjáró S2S sávszélessége | BytesPerSecond | Átlag
Igen | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | Átjáró P2S sávszélessége | BytesPerSecond | Átlag
Igen | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | P2S-kapcsolatok száma | Darabszám | Maximum
Igen | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | Bújtatási sávszélesség | BytesPerSecond | Átlag
Igen | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | Alagút kimenő bájtjai | Bájt | Összesen
Igen | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | Alagút kimenő terminálszolgáltatási eltérési csomagjainak eldobása | Darabszám | Összesen
Igen | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | Alagút kimenő csomagjai | Darabszám | Összesen
Igen | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | Alagutak bejövő bájtjai | Bájt | Összesen
Igen | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | Az alagút bejövő HOZZÁFÉRÉSe nem egyezik a csomagok eldobásával | Darabszám | Összesen
Igen | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | Bújtatási bejövő csomagok | Darabszám | Összesen
Igen | Microsoft. Network/virtualNetworks | PingMeshAverageRoundtripMs | A pingelések időpontjának kerekítése egy virtuális géphez | Ezredmásodpercben | Átlag
Igen | Microsoft. Network/virtualNetworks | PingMeshProbesFailedPercent | Sikertelen pingelések egy virtuális géphez | Százalék | Átlag
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő | Bejövő üzenetek | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ALL. failedrequests | Minden bejövő sikertelen kérelem | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | incoming.all.requests | Minden bejövő kérelem | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ütemezett | Ütemezett leküldéses értesítések elküldve | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ütemezett. Mégse | Ütemezett leküldéses értesítések megszakítva | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | a telepítés. All | Telepítési felügyeleti műveletek | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | installation.delete | Telepítési műveletek törlése | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | telepítés. Get | Telepítési műveletek beolvasása | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | Installation. patch | Javítások telepítési műveletei | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | telepítési. upsert | Telepítési műveletek létrehozása vagy frissítése | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | notificationhub. leküldések | Minden kimenő értesítés | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.allpns.badorexpiredchannel | Rossz vagy lejárt csatorna-hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.allpns.channelerror | Csatorna hibái | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. invalidpayload | Hasznos adatok | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.allpns.pnserror | Külső értesítési rendszerhibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.allpns.success | Sikeres értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.apns.badchannel | APNS hibás csatorna hiba | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.apns.expiredchannel | APNS Expired Channel Error | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.apns.invalidcredentials | APNS-hitelesítési hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.apns.invalidnotificationsize | A APNS érvénytelen értesítési méretet észlelt | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.apns.pnserror | APNS hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.apns.success | APNS sikeres értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.gcm.authenticationerror | GCM-hitelesítési hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. badchannel | GCM hibás csatorna hiba | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.gcm.expiredchannel | GCM lejárt csatorna hibája | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.gcm.invalidcredentials | GCM-hitelesítési hibák (érvénytelen hitelesítő adatok) | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. invalidnotificationformat | GCM – érvénytelen értesítési formátum | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. invalidnotificationsize | A GCM érvénytelen értesítési méretet észlelt | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.gcm.pnserror | GCM hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.gcm.success | GCM sikeres értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. szabályozva | GCM-szabályozású értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. wrongchannel | GCM rossz csatorna hibája | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.mpns.authenticationerror | MPNS-hitelesítési hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.mpns.badchannel | MPNS hibás csatorna hiba | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.mpns.channeldisconnected | MPNS csatorna leválasztva | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. Dropped | MPNS eldobott értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.mpns.invalidcredentials | MPNS – érvénytelen hitelesítő adatok | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.mpns.invalidnotificationformat | MPNS – érvénytelen értesítési formátum | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.mpns.pnserror | MPNS hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.mpns.success | MPNS sikeres értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.mpns.throttled | MPNS-szabályozású értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.authenticationerror | WNS-hitelesítési hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.badchannel | WNS hibás csatorna hiba | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.channeldisconnected | WNS csatorna leválasztva | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.channelthrottled | WNS csatorna szabályozása | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. Dropped | WNS eldobott értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.expiredchannel | WNS lejárt csatorna hibája | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.invalidcredentials | WNS-hitelesítési hibák (érvénytelen hitelesítő adatok) | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidnotificationformat | WNS – érvénytelen értesítési formátum | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidnotificationsize | A WNS érvénytelen értesítési méretet észlelt | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidtoken | WNS-hitelesítési hibák (érvénytelen token) | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.pnserror | WNS hibák | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.success | WNS sikeres értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. szabályozva | WNS-szabályozású értesítések | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.tokenproviderunreachable | WNS-hitelesítési hibák (nem érhető el) | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | outgoing.wns.wrongtoken | WNS-hitelesítési hibák (hibás token) | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. All | Regisztrációs műveletek | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | registration.create | Regisztráció-létrehozási műveletek | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | registration.delete | Regisztrációs törlési műveletek | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | registration.get | Regisztrálási olvasási műveletek | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | registration.update | Regisztrációs frissítési műveletek | Darabszám | Összesen
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | ütemezett. függőben | Függőben lévő ütemezett értesítések | Darabszám | Összesen
Igen | Microsoft.OperationalInsights/workspaces | Rendelkezésre álló memória Average_%-ban | Rendelkezésre álló memória%-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ rendelkezésre álló szabad hely a lapozófájlban | Rendelkezésre álló swap-terület (%) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_%-os előjegyzett bájtok használatban | Előjegyzett memória%-ban használatban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_% DPC idő | % DPC idő | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ szabad inode (%) | Szabad inode%-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ szabad terület (%) | % Szabad terület | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ szabad terület (%) | % Szabad terület | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ üresjárati idő%-ban | Üresjárati idő%-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_%-os megszakítási idő | Megszakítási idő%-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_% IO várakozási idő | I/o várakozási idő%-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_%-os szép idő | % Nice idő | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_%-os privilegizált idő | %-Os privilegizált idő | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ processzoridő | Processzoridő | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ processzoridő | Processzoridő | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ használt inode | Felhasznált inode%-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Felhasznált memória Average_%-ban | Felhasznált memória (%) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ felhasznált terület%-ban | Foglalt hely % | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_ felhasznált lapozófájl-terület | Felhasznált swap-terület%-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_%-os felhasználói idő | Felhasználói idő%-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Available MB-ban | Rendelkezésre álló memória | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Available MB memória | Rendelkezésre álló memória | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Available MB-ban való swap | Rendelkezésre álló memória (MB) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Avg. Lemez mp/Olvasás | Átlagos írási idő (mp/olvasás) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Avg. Lemez mp/Olvasás | Átlagos írási idő (mp/olvasás) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Avg. Lemez mp/átvitel | Átlagos műveleti idő (mp/átvitel) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Avg. Lemez mp/írás | Átlagos írási idő (mp/írás) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Avg. Lemez mp/írás | Átlagos írási idő (mp/írás) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Fogadott Average_Bytes/mp | Fogadott bájtok/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Küldési Average_Bytes másodpercenként | Küldött bájtok/s | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Bytes összesen/mp | Összes bájt/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Current a lemez várólistájának hossza | Lemez aktuális várólistájának hossza | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Olvasási sebesség (bájt/s) Average_Disk | Lemezolvasási sebesség (bájt/s) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Olvasási Average_Disk másodpercenként | Lemezolvasások/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Olvasási Average_Disk másodpercenként | Lemezolvasások/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Disk adatátvitel másodpercenként | Átvitel/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Disk adatátvitel másodpercenként | Átvitel/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Írási sebesség (bájt/s) Average_Disk | Lemezírási sebesség (bájt/s) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Írás/mp Average_Disk | Lemezírások/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Írás/mp Average_Disk | Lemezírások/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Free megabájt | Szabad hely MB-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Free megabájt | Szabad hely MB-ban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Fizikai memória Average_Free | Szabad fizikai memória | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Free lemezterület a Lapozófájlokban | Szabad terület a Lapozófájlokban | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Free virtuális memória | Szabad virtuális memória | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Logical lemez sebessége (bájt/s) | Logikai lemez bájt/mp | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Olvasási Average_Page másodpercenként | Olvasott lap/mperc | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Írás/mp Average_Page | Írt lap/mperc | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Pages/sec | Mozgatott lapok (lap/sec) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Pct emelt szintű idő | PCT rendszerjogosultságú idő | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Pct felhasználói idő | PCT felhasználói idő | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Physical lemez sebessége (bájt/s) | Fizikai lemez sebessége (bájt/s) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Processes | Folyamatok | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Processor várólista hossza | Processzor-várólista hossza | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Lapozófájlokban tárolt Average_Size | Lapozófájlokban tárolt méret | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Total bájtok | Bájtok összesen | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Fogadott Average_Total bájtok száma | Fogadott bájtok teljes száma | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Total továbbított bájtok | Küldött bájtok száma összesen | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Ütközések Average_Total | Ütközések összesen | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Total fogadott csomagok | Fogadott csomagok összesen | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Total továbbított csomagok | Továbbított csomagok összesen | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Rx-hibák Average_Total | Rx-hibák összesen | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Total TX-hibák | TX-hibák összesen | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Uptime | Hasznos üzemidő | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Used MB-ban felcserélt terület | Felhasznált memória (MB) – lapozófájl | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Used memória kilobájtban | Felhasznált memória (kilobájt) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Used memória MB-ban | Felhasznált memória (MB) | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Users | Felhasználók | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Average_Virtual megosztott memória | Virtuális megosztott memória | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Esemény | Esemény | Darabszám | Átlag
Igen | Microsoft.OperationalInsights/workspaces | Szívverés | Szívverés | Darabszám | Összesen
Igen | Microsoft.OperationalInsights/workspaces | Frissítés | Frissítés | Darabszám | Átlag
Igen | Microsoft.PowerBIDedicated/capacities | memory_metric | Memory (Memória) | Bájt | Átlag
Igen | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | Memória-Kiverés (adathalmazok) | Százalék | Átlag
Igen | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | QPU magas kihasználtsága | Darabszám | Összesen
Igen | Microsoft.PowerBIDedicated/capacities | QueryDuration | Lekérdezés időtartama (adatkészletek) | Ezredmásodperc | Átlag
Igen | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | Lekérdezési készlet nyomtatási várólistájának hossza (adatkészletek) | Darabszám | Átlag
Nem | Microsoft.Relay/namespaces | ActiveConnections | ActiveConnections | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | ActiveListeners | ActiveListeners | Darabszám | Összesen
Igen | Microsoft.Relay/namespaces | BytesTransferred | BytesTransferred | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | ListenerConnections-ClientError | ListenerConnections-ClientError | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | ListenerConnections-ServerError | ListenerConnections-ServerError | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | ListenerConnections-Success | ListenerConnections-Success | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | Bontásai | Bontásai | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | SenderConnections-ClientError | SenderConnections-ClientError | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | SenderConnections-ServerError | SenderConnections-ServerError | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | SenderConnections-Success | SenderConnections-Success | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | SenderConnections-TotalRequests | SenderConnections-TotalRequests | Darabszám | Összesen
Nem | Microsoft.Relay/namespaces | SenderDisconnects | SenderDisconnects | Darabszám | Összesen
Igen | Microsoft.Search/searchServices | SearchLatency | Keresési késés | Másodperc | Átlag
Igen | Microsoft.Search/searchServices | SearchQueriesPerSecond | Keresési lekérdezések másodpercenként | CountPerSecond | Átlag
Igen | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | Szabályozott keresési lekérdezések százalékos aránya | Százalék | Átlag
Nem | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | Darabszám | Összesen
Nem | Microsoft.ServiceBus/namespaces | ActiveMessages | Üzenetsor vagy témakör aktív üzeneteinek száma. | Darabszám | Átlag
Nem | Microsoft.ServiceBus/namespaces | ConnectionsClosed | A kapcsolatok lezárva. | Darabszám | Átlag
Nem | Microsoft.ServiceBus/namespaces | ConnectionsOpened | Megnyitott kapcsolatok. | Darabszám | Átlag
Nem | Microsoft.ServiceBus/namespaces | CPUXNS | CPU (elavult) | Százalék | Maximum
Nem | Microsoft.ServiceBus/namespaces | DeadletteredMessages | Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. | Darabszám | Átlag
Igen | Microsoft.ServiceBus/namespaces | IncomingMessages | Bejövő üzenetek | Darabszám | Összesen
Igen | Microsoft.ServiceBus/namespaces | IncomingRequests | Bejövő kérelmek | Darabszám | Összesen
Nem | Microsoft.ServiceBus/namespaces | Üzenetek | Üzenetsor vagy témakör üzeneteinek száma. | Darabszám | Átlag
Nem | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | CPU | Százalék | Maximum
Nem | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | Memóriahasználat | Százalék | Maximum
Igen | Microsoft.ServiceBus/namespaces | OutgoingMessages | Kimenő üzenetek | Darabszám | Összesen
Nem | Microsoft.ServiceBus/namespaces | ScheduledMessages | Az üzenetsor/témakör ütemezett üzeneteinek száma. | Darabszám | Átlag
Nem | Microsoft.ServiceBus/namespaces | Kiszolgálóhibái | Kiszolgálói hibák. | Darabszám | Összesen
Nem | Microsoft.ServiceBus/namespaces | Méret | Méret | Bájt | Átlag
Nem | Microsoft.ServiceBus/namespaces | SuccessfulRequests | Sikeres kérések | Darabszám | Összesen
Nem | Microsoft.ServiceBus/namespaces | ThrottledRequests | Szabályozott kérelmek. | Darabszám | Összesen
Nem | Microsoft.ServiceBus/namespaces | UserErrors | Felhasználói hibák. | Darabszám | Összesen
Nem | Microsoft.ServiceBus/namespaces | WSXNS | Memóriahasználat (elavult) | Százalék | Maximum
Nem | Microsoft.ServiceFabricMesh/applications | ActualCpu | ActualCpu | Darabszám | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | ActualMemory | ActualMemory | Bájt | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | AllocatedCpu | AllocatedCpu | Darabszám | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | AllocatedMemory | AllocatedMemory | Bájt | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | ApplicationStatus | ApplicationStatus | Darabszám | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | ContainerStatus | ContainerStatus | Darabszám | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | CpuUtilization | CpuUtilization | Százalék | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | MemoryUtilization | MemoryUtilization | Százalék | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | RestartCount | RestartCount | Darabszám | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | ServiceReplicaStatus | ServiceReplicaStatus | Darabszám | Átlag
Nem | Microsoft.ServiceFabricMesh/applications | ServiceStatus | ServiceStatus | Darabszám | Átlag
Igen | Microsoft.SignalRService/SignalR | ConnectionCount | Kapcsolatok száma | Darabszám | Maximum
Igen | Microsoft.SignalRService/SignalR | InboundTraffic | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft.SignalRService/SignalR | MessageCount | Üzenetek száma | Darabszám | Összesen
Igen | Microsoft.SignalRService/SignalR | OutboundTraffic | Kimenő forgalom | Bájt | Összesen
Igen | Microsoft.SignalRService/SignalR | SystemErrors | Rendszerhibák | Százalék | Maximum
Igen | Microsoft.SignalRService/SignalR | UserErrors | Felhasználói hibák | Százalék | Maximum
Igen | Microsoft.Sql/managedInstances | avg_cpu_percent | Átlagos CPU-százalék | Százalék | Átlag
Igen | Microsoft.Sql/managedInstances | io_bytes_read | I/o-bájtok olvasása | Bájt | Átlag
Igen | Microsoft.Sql/managedInstances | io_bytes_written | I/o-bájtok írása | Bájt | Átlag
Igen | Microsoft.Sql/managedInstances | io_requests | IO-kérelmek száma | Darabszám | Átlag
Igen | Microsoft.Sql/managedInstances | reserved_storage_mb | Tárterület fenntartva | Darabszám | Átlag
Igen | Microsoft.Sql/managedInstances | storage_space_used_mb | Felhasznált tárterület | Darabszám | Átlag
Igen | Microsoft.Sql/managedInstances | virtual_core_count | Virtuális mag száma | Darabszám | Átlag
Nem | Microsoft.Sql/servers | database_dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Nem | Microsoft.Sql/servers | database_storage_used | Felhasznált adatterület | Bájt | Átlag
Igen | Microsoft.Sql/servers | dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Igen | Microsoft.Sql/servers | dtu_used | Használt DTU | Darabszám | Átlag
Igen | Microsoft.Sql/servers | storage_used | Felhasznált adatterület | Bájt | Átlag
Igen | Microsoft.Sql/servers/databases | allocated_data_storage | Lefoglalt adatterület | Bájt | Átlag
Igen | Microsoft.Sql/servers/databases | app_cpu_billed | Az alkalmazás CPU-számlázása | Darabszám | Összesen
Igen | Microsoft.Sql/servers/databases | app_cpu_percent | Alkalmazás CPU-aránya | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | app_memory_percent | Az alkalmazás memóriájának százaléka | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | blocked_by_firewall | Tűzfal blokkolja | Darabszám | Összesen
Igen | Microsoft.Sql/servers/databases | cache_hit_percent | Gyorsítótár találati százaléka | Százalék | Maximum
Igen | Microsoft.Sql/servers/databases | cache_used_percent | Gyorsítótár használt százaléka | Százalék | Maximum
Igen | Microsoft.Sql/servers/databases | connection_failed | Sikertelen kapcsolatok | Darabszám | Összesen
Igen | Microsoft.Sql/servers/databases | connection_successful | Sikeres kapcsolatok | Darabszám | Összesen
Igen | Microsoft.Sql/servers/databases | cpu_limit | CPU-korlát | Darabszám | Átlag
Igen | Microsoft.Sql/servers/databases | cpu_percent | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | cpu_used | Felhasznált CPU | Darabszám | Átlag
Igen | Microsoft.Sql/servers/databases | holtpont | Holtpontok | Darabszám | Összesen
Igen | Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | dtu_limit | DTU korlátja | Darabszám | Átlag
Igen | Microsoft.Sql/servers/databases | dtu_used | Használt DTU | Darabszám | Átlag
Igen | Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU százalékos aránya | Százalék | Maximum
Igen | Microsoft.Sql/servers/databases | dwu_limit | DWU korlátja | Darabszám | Maximum
Igen | Microsoft.Sql/servers/databases | dwu_used | Használt DWU | Darabszám | Maximum
Igen | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | Helyi tempdb százalékos aránya | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | log_write_percent | Naplózási IO-százalék | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | memory_usage_percent | Memória százaléka | Százalék | Maximum
Igen | Microsoft.Sql/servers/databases | physical_data_read_percent | Adat IO kihasználtsága (%) | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | sessions_percent | Munkamenetek százalékos aránya | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | Storage | Felhasznált adatterület | Bájt | Maximum
Igen | Microsoft.Sql/servers/databases | storage_percent | Felhasznált adatterület százalékos aránya | Százalék | Maximum
Igen | Microsoft.Sql/servers/databases | tempdb_data_size | Tempdb adatfájl mérete (kilobájt) | Darabszám | Maximum
Igen | Microsoft.Sql/servers/databases | tempdb_log_size | Tempdb-naplófájl mérete (kilobájt) | Darabszám | Maximum
Igen | Microsoft.Sql/servers/databases | tempdb_log_used_percent | Tempdb százalékos naplója használatban | Százalék | Maximum
Igen | Microsoft.Sql/servers/databases | workers_percent | Munkavégzők százalékos aránya | Százalék | Átlag
Igen | Microsoft.Sql/servers/databases | xtp_storage_percent | Memóriában tárolt OLTP tárolási százaléka | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | allocated_data_storage | Lefoglalt adatterület | Bájt | Átlag
Igen | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | Lefoglalt adatterület százalékos aránya | Százalék | Maximum
Igen | Microsoft.Sql/servers/elasticPools | cpu_limit | CPU-korlát | Darabszám | Átlag
Igen | Microsoft.Sql/servers/elasticPools | cpu_percent | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | cpu_used | Felhasznált CPU | Darabszám | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | Lefoglalt adatterület | Bájt | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_cpu_limit | CPU-korlát | Darabszám | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_cpu_percent | Processzorhasználat (%) | Százalék | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_cpu_used | Felhasznált CPU | Darabszám | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_eDTU_used | használt eDTU | Darabszám | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_log_write_percent | Naplózási IO-százalék | Százalék | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | Adat IO kihasználtsága (%) | Százalék | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_sessions_percent | Munkamenetek százalékos aránya | Százalék | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_storage_used | Felhasznált adatterület | Bájt | Átlag
Nem | Microsoft.Sql/servers/elasticPools | database_workers_percent | Munkavégzők százalékos aránya | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | eDTU_limit | eDTU korlátja | Darabszám | Átlag
Igen | Microsoft.Sql/servers/elasticPools | eDTU_used | használt eDTU | Darabszám | Átlag
Igen | Microsoft.Sql/servers/elasticPools | log_write_percent | Naplózási IO-százalék | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Adat IO kihasználtsága (%) | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | sessions_percent | Munkamenetek százalékos aránya | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | storage_limit | Az adatmaximális méret | Bájt | Átlag
Igen | Microsoft.Sql/servers/elasticPools | storage_percent | Felhasznált adatterület százalékos aránya | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | storage_used | Felhasznált adatterület | Bájt | Átlag
Igen | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Tempdb adatfájl mérete (kilobájt) | Darabszám | Maximum
Igen | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Tempdb-naplófájl mérete (kilobájt) | Darabszám | Maximum
Igen | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | Tempdb százalékos naplója használatban | Százalék | Maximum
Igen | Microsoft.Sql/servers/elasticPools | workers_percent | Munkavégzők százalékos aránya | Százalék | Átlag
Igen | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Memóriában tárolt OLTP tárolási százaléka | Százalék | Átlag
Igen | Microsoft.Storage/storageAccounts | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Igen | Microsoft.Storage/storageAccounts | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Igen | Microsoft.Storage/storageAccounts | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft.Storage/storageAccounts | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts | Tranzakciók | Tranzakciók | Darabszám | Összesen
Nem | Microsoft.Storage/storageAccounts | UsedCapacity | Használt kapacitás | Bájt | Átlag
Igen | Microsoft.Storage/storageAccounts/blobServices | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Nem | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Blob-kapacitása | Bájt | Átlag
Nem | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Blobok száma | Darabszám | Átlag
Igen | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Blobtárolók száma | Darabszám | Átlag
Igen | Microsoft.Storage/storageAccounts/blobServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Nem | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | Index kapacitása | Bájt | Átlag
Igen | Microsoft.Storage/storageAccounts/blobServices | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts/blobServices | Tranzakciók | Tranzakciók | Darabszám | Összesen
Igen | Microsoft.Storage/storageAccounts/fileServices | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Igen | Microsoft.Storage/storageAccounts/fileServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Nem | Microsoft.Storage/storageAccounts/fileServices | FileCapacity | Fájl kapacitása | Bájt | Átlag
Nem | Microsoft.Storage/storageAccounts/fileServices | FileCount | Fájlok száma | Darabszám | Átlag
Nem | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | Fájlmegosztás száma | Darabszám | Átlag
Nem | Microsoft.Storage/storageAccounts/fileServices | FileShareQuota | Fájlmegosztás kvótájának mérete | Bájt | Átlag
Nem | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotCount | Fájlmegosztás pillanatképének száma | Darabszám | Átlag
Nem | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotSize | Fájlmegosztás pillanatképének mérete | Bájt | Átlag
Igen | Microsoft.Storage/storageAccounts/fileServices | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts/fileServices | Tranzakciók | Tranzakciók | Darabszám | Összesen
Igen | Microsoft.Storage/storageAccounts/queueServices | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Igen | Microsoft.Storage/storageAccounts/queueServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Igen | Microsoft.Storage/storageAccounts/queueServices | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | Queue Storage kapacitása | Bájt | Átlag
Igen | Microsoft.Storage/storageAccounts/queueServices | QueueCount | Üzenetsorok száma | Darabszám | Átlag
Igen | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | Üzenetsorbeli üzenetek száma | Darabszám | Átlag
Igen | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts/queueServices | Tranzakciók | Tranzakciók | Darabszám | Összesen
Igen | Microsoft.Storage/storageAccounts/tableServices | Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag
Igen | Microsoft.Storage/storageAccounts/tableServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összesen
Igen | Microsoft.Storage/storageAccounts/tableServices | Bejövő forgalom | Bejövő forgalom | Bájt | Összesen
Igen | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | Table Storage kapacitása | Bájt | Átlag
Igen | Microsoft.Storage/storageAccounts/tableServices | TableCount | Táblák száma | Darabszám | Átlag
Igen | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | Táblaentitások száma | Darabszám | Átlag
Igen | Microsoft.Storage/storageAccounts/tableServices | Tranzakciók | Tranzakciók | Darabszám | Összesen
Igen | Microsoft. StorageCache/gyorsítótárak | ClientIOPS | Ügyfél teljes IOPS | Darabszám | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | ClientLatency | Ügyfél átlagos késése | Ezredmásodperc | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | ClientLockIOPS | Ügyfél-zárolási IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | ClientMetadataReadIOPS | Ügyfél metaadatainak olvasása IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | ClientMetadataWriteIOPS | Ügyfél metaadatainak írási IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | ClientReadIOPS | Ügyfél olvasási IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | ClientReadThroughput | Gyorsítótár átlagos olvasási átviteli sebessége | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | ClientWriteIOPS | Ügyfél írási IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | ClientWriteThroughput | Gyorsítótár átlagos írási sebessége | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetAsyncWriteThroughput | StorageTarget aszinkron írási átviteli sebesség | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetFillThroughput | StorageTarget-kitöltési sebesség | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetHealth | Tárolási cél állapota | Darabszám | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetIOPS | Összes StorageTarget-IOPS | Darabszám | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetLatency | StorageTarget késés | Ezredmásodperc | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetMetadataReadIOPS | StorageTarget-metaadatok IOPS olvasása | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetMetadataWriteIOPS | StorageTarget-metaadatok írási IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetReadAheadThroughput | StorageTarget – olvasási sebesség | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetReadIOPS | StorageTarget olvasási IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetSyncWriteThroughput | StorageTarget szinkron írási sebessége | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetTotalReadThroughput | StorageTarget összesen olvasási átviteli sebesség | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetTotalWriteThroughput | StorageTarget összes írási sebessége | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetWriteIOPS | StorageTarget írási IOPS | Darabszám | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | Hasznos üzemidő | Hasznos üzemidő | Darabszám | Átlag
Igen | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | Szinkronizálási munkamenet eredménye | Darabszám | Átlag
Igen | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | Szinkronizált bájtok száma | Bájt | Összesen
Igen | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | Felhőbeli rétegek felidézésének mérete alkalmazás szerint | Bájt | Összesen
Igen | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | Felhőbeli rétegek felidézésének mérete | Bájt | Összesen
Igen | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | Felhőbeli rétegek felidézése | Bájt | Összesen
Igen | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | Felhőbeli rétegek felidézésének átviteli sebessége | BytesPerSecond | Átlag
Igen | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | Kiszolgáló online állapota | Darabszám | Maximum
Igen | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | Szinkronizált fájlok | Darabszám | Összesen
Igen | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | Nem szinkronizált fájlok | Darabszám | Összesen
Igen | Microsoft. storagesync/storageSyncServices/registeredServers | ServerHeartbeat | Kiszolgáló online állapota | Darabszám | Maximum
Igen | Microsoft. storagesync/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | Felhőbeli rétegek felidézése | Bájt | Összesen
Igen | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | Szinkronizált bájtok száma | Bájt | Összesen
Igen | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | Szinkronizált fájlok | Darabszám | Összesen
Igen | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | Nem szinkronizált fájlok | Darabszám | Összesen
Igen | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | Szinkronizált bájtok száma | Bájt | Összesen
Igen | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | Szinkronizált fájlok | Darabszám | Összesen
Igen | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | Nem szinkronizált fájlok | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | Sikertelen függvénykérések | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | Függvényesemények | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | Függvénykérések | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | Adatkonverziós hibák | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | A deszerializálás bemeneti hibái | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | Megrendelési események | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | Korai bemeneti események | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | Hibák | Futásidejű hibák | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | Bemeneti esemény bájtokban | Bájt | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | InputEvents | Bemeneti események | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | Várakozó bemeneti események | Darabszám | Maximum
Igen | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | Fogadott bemeneti források | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | Késedelmes bemeneti események | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | Kimeneti események | Darabszám | Összesen
Igen | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | Vízjel-késleltetés | Másodperc | Maximum
Igen | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | SU százalékos kihasználtsága | Százalék | Maximum
Igen | Microsoft.VMwareCloudSimple/virtualMachines | Lemez olvasási bájtjai | Lemez olvasási bájtjai | Bájt | Összesen
Igen | Microsoft.VMwareCloudSimple/virtualMachines | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | Lemez írási bájtjai | Lemez írási bájtjai | Bájt | Összesen
Igen | Microsoft.VMwareCloudSimple/virtualMachines | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Lemez olvasási sebessége (bájt/s) | BytesPerSecond | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadLatency | Lemez olvasási késése | Ezredmásodperc | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadOperations | Lemezes olvasási műveletek | Darabszám | Összesen
Igen | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Lemez írási sebessége (bájt/s) | BytesPerSecond | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteLatency | Lemez írási késése | Ezredmásodperc | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteOperations | Lemez írási műveletei | Darabszám | Összesen
Igen | Microsoft.VMwareCloudSimple/virtualMachines | MemoryActive | Memória aktív | Bájt | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | MemoryGranted | Megadott memória | Bájt | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | MemoryUsed | Felhasznált memória | Bájt | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | Bejövő hálózat | Bejövő hálózat | Bájt | Összesen
Igen | Microsoft.VMwareCloudSimple/virtualMachines | Kimenő hálózat | Kimenő hálózat | Bájt | Összesen
Igen | Microsoft.VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | Hálózat bájt/mp-ben | BytesPerSecond | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | Hálózati kimenő bájtok/s | BytesPerSecond | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft.VMwareCloudSimple/virtualMachines | PercentageCpuReady | CPU-készültség százalékos aránya | Ezredmásodperc | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | Aktív kérések | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | Átlagos válaszidő | Másodperc | Átlag
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | A-ben tárolt adatértékek | Bájt | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | Kimenő adatvesztés | Bájt | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | Lemezvezérlő-várólista hossza | Darabszám | Átlag
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | Http 2xx | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | Http-3xx | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | HTTP 401 | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | Http 403 | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | Http 404 | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | Http 4xx | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Http-kiszolgálói hibák | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Http-várólista hossza | Darabszám | Átlag
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | Nagy App Service-csomag feldolgozói | Darabszám | Átlag
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | Közepes App Service-csomag feldolgozói | Darabszám | Átlag
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | Memória százaléka | Százalék | Átlag
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | Kérelmek | Kérelmek | Darabszám | Összesen
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | Kisméretű App Service-csomag feldolgozói | Darabszám | Átlag
Igen | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | Összes előtér | Darabszám | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | CpuPercentage | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | MemoryPercentage | Memória százaléka | Százalék | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | WorkersAvailable | Rendelkezésre álló munkavégzők | Darabszám | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | WorkersTotal | Alkalmazottak összesen | Darabszám | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | WorkersUsed | Használt feldolgozók | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | BytesReceived | A-ben tárolt adatértékek | Bájt | Összesen
Igen | Microsoft.Web/serverfarms | BytesSent | Kimenő adatvesztés | Bájt | Összesen
Igen | Microsoft.Web/serverfarms | CpuPercentage | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft.Web/serverfarms | DiskQueueLength | Lemezvezérlő-várólista hossza | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | HttpQueueLength | Http-várólista hossza | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | MemoryPercentage | Memória százaléka | Százalék | Átlag
Igen | Microsoft.Web/serverfarms | TcpCloseWait | TCP-bezárási várakozás | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | TcpClosing | TCP-zárás | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | TcpEstablished | A TCP létrejött | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | TcpFinWait1 | TCP fin WAIT 1 | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | TcpFinWait2 | TCP fin WAIT 2 | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | TcpLastAck | TCP Last ACK | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | TcpSynReceived | TCP SYN kapott | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | TcpSynSent | Eljuttatott TCP SYN | Darabszám | Átlag
Igen | Microsoft.Web/serverfarms | TcpTimeWait | TCP-idő várakozása | Darabszám | Átlag
Igen | Microsoft.Web/sites | AppConnections | Kapcsolatok | Darabszám | Átlag
Igen | Microsoft.Web/sites | AverageMemoryWorkingSet | Memória átlagos munkakészlete | Bájt | Átlag
Igen | Microsoft.Web/sites | AverageResponseTime | Átlagos válaszidő | Másodperc | Átlag
Igen | Microsoft.Web/sites | BytesReceived | A-ben tárolt adatértékek | Bájt | Összesen
Igen | Microsoft.Web/sites | BytesSent | Kimenő adatvesztés | Bájt | Összesen
Igen | Microsoft.Web/sites | CpuTime | CPU-idő | Másodperc | Összesen
Igen | Microsoft.Web/sites | CurrentAssemblies | Aktuális szerelvények | Darabszám | Átlag
Igen | Microsoft.Web/sites | FunctionExecutionCount | Függvény végrehajtásának száma | Darabszám | Összesen
Igen | Microsoft.Web/sites | FunctionExecutionUnits | Függvények végrehajtási egységei | Darabszám | Összesen
Igen | Microsoft.Web/sites | Gen0Collections | 0\. generációs Garbage-gyűjtemények | Darabszám | Összesen
Igen | Microsoft.Web/sites | Gen1Collections | 1\. generációs Garbage gyűjtemények | Darabszám | Összesen
Igen | Microsoft.Web/sites | Gen2Collections | 2\. generációs Garbage gyűjtemények | Darabszám | Összesen
Igen | Microsoft.Web/sites | Kezeli | Leírók száma | Darabszám | Átlag
Igen | Microsoft.Web/sites | HealthCheckStatus | Állapot-ellenőrzési állapot | Darabszám | Átlag
Igen | Microsoft.Web/sites | Http101 | Http 101 | Darabszám | Összesen
Igen | Microsoft.Web/sites | Http2xx | Http 2xx | Darabszám | Összesen
Igen | Microsoft.Web/sites | Http3xx | Http-3xx | Darabszám | Összesen
Igen | Microsoft.Web/sites | Http401 | HTTP 401 | Darabszám | Összesen
Igen | Microsoft.Web/sites | Http403 | Http 403 | Darabszám | Összesen
Igen | Microsoft.Web/sites | Http404 | Http 404 | Darabszám | Összesen
Igen | Microsoft.Web/sites | Http406 | Http 406 | Darabszám | Összesen
Igen | Microsoft.Web/sites | Http4xx | Http 4xx | Darabszám | Összesen
Igen | Microsoft.Web/sites | Http5xx | Http-kiszolgálói hibák | Darabszám | Összesen
Igen | Microsoft.Web/sites | HttpResponseTime | Válaszidő | Másodperc | Átlag
Igen | Microsoft.Web/sites | IoOtherBytesPerSecond | IO – egyéb bájtok másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites | IoOtherOperationsPerSecond | IO egyéb műveletek másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites | IoReadBytesPerSecond | IO olvasási bájtok másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites | IoReadOperationsPerSecond | I/o-olvasási műveletek másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites | IoWriteBytesPerSecond | IO írási bájtok másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites | IoWriteOperationsPerSecond | IO írási műveletek másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites | MemoryWorkingSet | Memória munkakészlete | Bájt | Átlag
Igen | Microsoft.Web/sites | PrivateBytes | Saját [nem megosztható] memória (bájt) | Bájt | Átlag
Igen | Microsoft.Web/sites | Kérelmek | Kérelmek | Darabszám | Összesen
Igen | Microsoft.Web/sites | RequestsInApplicationQueue | Kérelmek az alkalmazás-várólistán | Darabszám | Átlag
Igen | Microsoft.Web/sites | Szálak | Szálak száma | Darabszám | Átlag
Igen | Microsoft.Web/sites | TotalAppDomains | Alkalmazás összes tartománya | Darabszám | Átlag
Igen | Microsoft.Web/sites | TotalAppDomainsUnloaded | Összes kitöltött alkalmazás-tartomány | Darabszám | Átlag
Igen | Microsoft.Web/sites/slots | AppConnections | Kapcsolatok | Darabszám | Átlag
Igen | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | Memória átlagos munkakészlete | Bájt | Átlag
Igen | Microsoft.Web/sites/slots | AverageResponseTime | Átlagos válaszidő | Másodperc | Átlag
Igen | Microsoft.Web/sites/slots | BytesReceived | A-ben tárolt adatértékek | Bájt | Összesen
Igen | Microsoft.Web/sites/slots | BytesSent | Kimenő adatvesztés | Bájt | Összesen
Igen | Microsoft.Web/sites/slots | CpuTime | CPU-idő | Másodperc | Összesen
Igen | Microsoft.Web/sites/slots | CurrentAssemblies | Aktuális szerelvények | Darabszám | Átlag
Igen | Microsoft.Web/sites/slots | FunctionExecutionCount | Függvény végrehajtásának száma | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | FunctionExecutionUnits | Függvények végrehajtási egységei | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Gen0Collections | 0\. generációs Garbage-gyűjtemények | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Gen1Collections | 1\. generációs Garbage gyűjtemények | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Gen2Collections | 2\. generációs Garbage gyűjtemények | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Kezeli | Leírók száma | Darabszám | Átlag
Igen | Microsoft.Web/sites/slots | HealthCheckStatus | Állapot-ellenőrzési állapot | Darabszám | Átlag
Igen | Microsoft.Web/sites/slots | Http101 | Http 101 | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Http2xx | Http 2xx | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Http3xx | Http-3xx | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Http401 | HTTP 401 | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Http403 | Http 403 | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Http404 | Http 404 | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Http406 | Http 406 | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Http4xx | Http 4xx | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | Http5xx | Http-kiszolgálói hibák | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | HttpResponseTime | Válaszidő | Másodperc | Átlag
Igen | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | IO – egyéb bájtok másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | IO egyéb műveletek másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites/slots | IoReadBytesPerSecond | IO olvasási bájtok másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | I/o-olvasási műveletek másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | IO írási bájtok másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | IO írási műveletek másodpercenként | BytesPerSecond | Összesen
Igen | Microsoft.Web/sites/slots | MemoryWorkingSet | Memória munkakészlete | Bájt | Átlag
Igen | Microsoft.Web/sites/slots | PrivateBytes | Saját [nem megosztható] memória (bájt) | Bájt | Átlag
Igen | Microsoft.Web/sites/slots | Kérelmek | Kérelmek | Darabszám | Összesen
Igen | Microsoft.Web/sites/slots | RequestsInApplicationQueue | Kérelmek az alkalmazás-várólistán | Darabszám | Átlag
Igen | Microsoft.Web/sites/slots | Szálak | Szálak száma | Darabszám | Átlag
Igen | Microsoft.Web/sites/slots | TotalAppDomains | Alkalmazás összes tartománya | Darabszám | Átlag
Igen | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | Összes kitöltött alkalmazás-tartomány | Darabszám | Átlag
