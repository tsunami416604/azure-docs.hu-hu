---
title: Azure Monitor a platform metrikái exportálható diagnosztikai beállítások használatával
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 5d38786d3c7b852d3a9b65cd366eed68ebbb01e3
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152952"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor a platform metrikái exportálható diagnosztikai beállítások használatával

A Azure Monitor alapértelmezés szerint a [platform metrikáit](data-platform-metrics.md) biztosítja konfiguráció nélkül. Számos lehetőséget kínál a platform metrikáinak interakcióra, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. Lásd: [mérőszámok – támogatott](metrics-supported.md) a Azure monitor konszolidált metrikus folyamatával jelenleg elérhető platform-metrikák teljes listájához. A metrikák lekérdezéséhez és eléréséhez használja az [2018-01-01 API-Version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők.

Az Azure monitor folyamatának platform metrikáit kétféleképpen exportálhatja más helyekre.
1. [Diagnosztikai beállítások](diagnostic-settings.md) használata Log Analytics, Event Hubs vagy Azure Storage-ba történő küldéshez.
2. A [metrikák REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list) használata

A Azure Monitor-háttér bonyolult jellemzői miatt nem minden metrika exportálható a diagnosztikai beállítások használatával. Az alábbi táblázat felsorolja, hogy mely és nem exportálható a diagnosztikai beállítások használatával.

Exportálható diagnosztikai beállításokkal? | ResourceType | Metrika | MetricDisplayName | Unit (Egység) | AggregationType
|----|-----|------|----|----|-----|
Igen | Microsoft. AnalysisServices/kiszolgálók | CleanerCurrentPrice | Memória: tisztító – aktuális ár | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | CleanerMemoryNonshrinkable | Memória: nem csökkenthető a tisztább memória | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | CleanerMemoryShrinkable | Memória: tisztító memória csökkenthető | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | CommandPoolBusyThreads | Szálak: a parancssori készlet foglalt szálak | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | CommandPoolIdleThreads | Szálak: parancssori készlet üresjárati szálai | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | CommandPoolJobQueueLength | Parancssori feladatok várólistájának hossza | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | Összege | Kapcsolat: aktuális kapcsolatok | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | CurrentUserSessions | Aktuális felhasználói munkamenetek | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | LongParsingBusyThreads | Szálak: hosszú elemzés – foglalt szálak | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | LongParsingIdleThreads | Szálak: tartós folyamatok elemzése – üresjárati szálak | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | LongParsingJobQueueLength | Szálak: hosszú elemzési feladatok várólistájának hossza | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | mashup_engine_memory_metric | M motor memóriája | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | mashup_engine_private_bytes_metric | M motor saját bájtjai | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | mashup_engine_qpu_metric | M motor QPU | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | mashup_engine_virtual_bytes_metric | M motor virtuális bájtjai | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | memory_metric | Memória | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | memory_thrashing_metric | Memóriaakadozás | Százalék | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | MemoryLimitHard | Memória: rögzített memória | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | MemoryLimitHigh | Memória: magas a memória korlátozása | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | MemoryLimitLow | Memória: kevés a memória korlátja | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | MemoryLimitVertiPaq | Memória: VertiPaq korlátja | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | MemoryUsage | Memória: memóriahasználat | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | private_bytes_metric | Saját [nem megosztható] memória (bájt) | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ProcessingPoolBusyIOJobThreads | Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ProcessingPoolBusyNonIOThreads | Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ProcessingPoolIdleIOJobThreads | Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ProcessingPoolIdleNonIOThreads | Szálak: feldolgozási készlet üresjáratban nem I/O-szálai | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ProcessingPoolIOJobQueueLength | Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ProcessingPoolJobQueueLength | Feldolgozási készlet nyomtatási várólistájának hossza | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | qpu_metric | QPU | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | QueryPoolBusyThreads | Lekérdezési készlet foglalt szálai | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | QueryPoolIdleThreads | Szálak: lekérdezési készlet – üresjárati szálak | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | QueryPoolJobQueueLength | Szálak: lekérdezési készlet feladatok várólistájának hossza | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | Kvóta | Memória: kvóta | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | QuotaBlocked | Memória: blokkolt kvóta | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | RowsConvertedPerSec | Feldolgozás: másodpercenként konvertált sorok száma | CountPerSecond | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | RowsReadPerSec | Feldolgozás: másodpercenként beolvasott sorok száma | CountPerSecond | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | RowsWrittenPerSec | Feldolgozás: másodpercenként írt sorok száma | CountPerSecond | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ShortParsingBusyThreads | Szálak: rövid elemzés – foglalt szálak | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ShortParsingIdleThreads | Szálak: rövid elemzési üresjárati szálak | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | ShortParsingJobQueueLength | Szálak: rövid elemzési feladatok várólistájának hossza | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | SuccessfullConnectionsPerSec | Sikeres kapcsolatok másodpercenként | CountPerSecond | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | TotalConnectionFailures | Összes sikertelen Kapcsolatfelvétel | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | TotalConnectionRequests | Kapcsolatkérelmek teljes száma | Mennyiség | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | VertiPaqNonpaged | Memória: VertiPaq, nem lapozható | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | VertiPaqPaged | Memória: VertiPaq lapozható | Bájt | Átlag
Igen | Microsoft. AnalysisServices/kiszolgálók | virtual_bytes_metric | Felhasznált virtuális memória jelenlegi mérete (bájt) | Bájt | Átlag
Igen | Microsoft.ApiManagement/service | BackendDuration | A háttérbeli kérelmek időtartama | Ezredmásodperc | Átlag
Igen | Microsoft.ApiManagement/service | Kapacitás | Kapacitás | Százalék | Átlag
Igen | Microsoft.ApiManagement/service | Időtartam | Az átjárók kéréseinek teljes időtartama | Ezredmásodperc | Átlag
Igen | Microsoft.ApiManagement/service | EventHubDroppedEvents | Eldobott EventHub események | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | EventHubRejectedEvents | EventHub-események visszautasítva | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | Sikeres EventHub események | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | EventHubThrottledEvents | Szabályozott EventHub események | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | EventHubTimedoutEvents | Időtúllépés a EventHub eseményeinél | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | EventHubTotalBytesSent | EventHub-események mérete | Bájt | Összes
Igen | Microsoft.ApiManagement/service | EventHubTotalEvents | Összes EventHub esemény | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | Sikertelen EventHub események | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | FailedRequests | Sikertelen átjáró-kérelmek (elavult) | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | OtherRequests | Egyéb átjáró-kérelmek (elavult) | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | Kérelmek | Kérelmek | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | SuccessfulRequests | Sikeres átjáró-kérelmek (elavult) | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | TotalRequests | Összes átjáró kérelme (elavult) | Mennyiség | Összes
Igen | Microsoft.ApiManagement/service | UnauthorizedRequests | Nem engedélyezett átjáró-kérelmek (elavult) | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | AppCpuUsagePercentage | Alkalmazás CPU-kihasználtságának százalékos aránya | Százalék | Átlag
Igen | Microsoft. AppPlatform/Spring | AppMemoryCommitted | Az alkalmazáshoz hozzárendelt memória | Bájt | Átlag
Igen | Microsoft. AppPlatform/Spring | AppMemoryMax | Az alkalmazás memóriájának maximális száma | Bájt | Maximum
Igen | Microsoft. AppPlatform/Spring | AppMemoryUsed | Használt alkalmazás memóriája | Bájt | Átlag
Igen | Microsoft. AppPlatform/Spring | GCPauseTotalCount | GC-szüneteltetések száma | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | GCPauseTotalTime | GC felfüggesztésének teljes ideje | Ezredmásodperc | Összes
Igen | Microsoft. AppPlatform/Spring | MaxOldGenMemoryPoolBytes | A rendelkezésre álló régi generációs adatméret maximális száma | Bájt | Átlag
Igen | Microsoft. AppPlatform/Spring | OldGenMemoryPoolBytes | A régi generációs adatméret | Bájt | Átlag
Igen | Microsoft. AppPlatform/Spring | OldGenPromotedBytes | Előléptetés a régi generációs adatméretre | Bájt | Maximum
Igen | Microsoft. AppPlatform/Spring | SystemCpuUsagePercentage | CPU-használat százalékos aránya | Százalék | Átlag
Igen | Microsoft. AppPlatform/Spring | TomcatErrorCount | Tomcat globális hiba | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatReceivedBytes | Tomcat összesen fogadott bájtok | Bájt | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatRequestMaxTime | Tomcat-kérelem maximális ideje | Ezredmásodperc | Maximum
Igen | Microsoft. AppPlatform/Spring | TomcatRequestTotalCount | Tomcat-kérelem összesített száma | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatRequestTotalTime | Tomcat-kérelem teljes ideje | Ezredmásodperc | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatResponseAvgTime | Tomcat-kérelem átlagos ideje | Ezredmásodperc | Átlag
Igen | Microsoft. AppPlatform/Spring | TomcatSentBytes | Tomcat összesen eljuttatott bájtok száma | Bájt | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatSessionActiveCurrentCount | Tomcat-munkamenet élő száma | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatSessionActiveMaxCount | Tomcat-munkamenet maximális aktív száma | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatSessionAliveMaxTime | Tomcat-munkamenet maximális élettartama | Ezredmásodperc | Maximum
Igen | Microsoft. AppPlatform/Spring | TomcatSessionCreatedCount | Tomcat-munkamenet létrehozva szám | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatSessionExpiredCount | A Tomcat-munkamenet lejárt | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | TomcatSessionRejectedCount | A Tomcat-munkamenet elutasította a darabszámot | Mennyiség | Összes
Igen | Microsoft. AppPlatform/Spring | YoungGenPromotedBytes | Népszerűsítse a fiatal generáció adatméretét | Bájt | Maximum
Igen | Microsoft. Automation/automationAccounts | TotalJob | Feladatok összesen | Mennyiség | Összes
Igen | Microsoft. Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | Összes frissítés központi telepítési gép futtatása | Mennyiség | Összes
Igen | Microsoft. Automation/automationAccounts | TotalUpdateDeploymentRuns | Összes frissítés központi telepítési futtatása | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | CoreCount | Dedikált mag száma | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | CreatingNodeCount | Csomópontok számának létrehozása | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | IdleNodeCount | Tétlen csomópontok száma | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | Feladatok törlése – befejezett események | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | Feladatok törlésének indítási eseményei | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | A feladatok letiltják a teljes eseményeket | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | JobDisableStartEvent | Feladatok letiltásának indítási eseményei | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | JobStartEvent | Feladatok indítási eseményei | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | A feladatokhoz tartozó befejezett események befejezése | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | Feladatokból indított események leállítása | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | A készlet-csomópontok számának elhagyása | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | LowPriority mag száma | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | OfflineNodeCount | Offline csomópontok száma | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | PoolCreateEvent | Készlet-létrehozási események | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | Készlet törlése – befejezett események | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | Készlet törlése – indítási események | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | Készlet átméretezése – befejezett események | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | Készlet átméretezésének indítási eseményei | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | PreemptedNodeCount | Előzik-csomópontok száma | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | RebootingNodeCount | Csomópontok számának újraindítása | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | ReimagingNodeCount | Csomópontok rendszerképének alaphelyzetbe állítása | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | RunningNodeCount | Csomópontok száma | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | StartingNodeCount | Csomópontok számának indítása | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | Indítási feladat sikertelen csomópontok száma | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | TaskCompleteEvent | Feladat teljes eseményei | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | TaskFailEvent | Feladat sikertelen eseményei | Mennyiség | Összes
Igen | Microsoft.Batch/batchAccounts | TaskStartEvent | Tevékenységek indítási eseményei | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | Alacsony prioritású csomópontok száma | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | TotalNodeCount | Dedikált csomópontok száma | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | UnusableNodeCount | Nem használható csomópontok száma | Mennyiség | Összes
Nem | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | Várakozás a feladat-csomópontok számának megkezdésére | Mennyiség | Összes
Igen | Microsoft. BatchAI/munkaterületek | Aktív magok | Aktív magok | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Aktív csomópontok | Aktív csomópontok | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Üresjárati magok | Üresjárati magok | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Tétlen csomópontok | Tétlen csomópontok | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | A feladatok befejeződtek | A feladatok befejeződtek | Mennyiség | Összes
Igen | Microsoft. BatchAI/munkaterületek | Feladatok elküldve | Feladatok elküldve | Mennyiség | Összes
Igen | Microsoft. BatchAI/munkaterületek | Magok kihagyása | Magok kihagyása | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Csomópontok elhagyása | Csomópontok elhagyása | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Előzik magok | Előzik magok | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Előzik-csomópontok | Előzik-csomópontok | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Kvóta kihasználtsága (%) | Kvóta kihasználtsága (%) | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Magok összesen | Magok összesen | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Csomópontok összesen | Csomópontok összesen | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Használhatatlan magok | Használhatatlan magok | Mennyiség | Átlag
Igen | Microsoft. BatchAI/munkaterületek | Használhatatlan csomópontok | Használhatatlan csomópontok | Mennyiség | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | ConnectionAccepted | Elfogadott kapcsolatok | Mennyiség | Összes
Igen | Microsoft. Blockchain/blockchainMembers | ConnectionActive | Aktív kapcsolatok | Mennyiség | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | ConnectionHandled | Kezelt kapcsolatok | Mennyiség | Összes
Igen | Microsoft. Blockchain/blockchainMembers | CpuUsagePercentageInDouble | CPU-használat százaléka | Százalék | Maximum
Igen | Microsoft. Blockchain/blockchainMembers | IOReadBytes | IO olvasási bájtok | Bájt | Összes
Igen | Microsoft. Blockchain/blockchainMembers | IOWriteBytes | IO írási bájtjai | Bájt | Összes
Igen | Microsoft. Blockchain/blockchainMembers | MemoryLimit | Memória korlátja | Bájt | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | MemoryUsage | Memóriahasználat | Bájt | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | MemoryUsagePercentageInDouble | Memóriahasználat százaléka | Százalék | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | PendingTransactions | Függőben lévő tranzakciók | Mennyiség | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | ProcessedBlocks | Feldolgozott blokkok | Mennyiség | Összes
Igen | Microsoft. Blockchain/blockchainMembers | ProcessedTransactions | Feldolgozott tranzakciók | Mennyiség | Összes
Igen | Microsoft. Blockchain/blockchainMembers | QueuedTransactions | Várólistán lévő tranzakciók | Mennyiség | Átlag
Igen | Microsoft. Blockchain/blockchainMembers | RequestHandled | Kezelt kérelmek | Mennyiség | Összes
Igen | Microsoft. Blockchain/blockchainMembers | StorageUsage | Tárterület-használat | Bájt | Átlag
Igen | Microsoft. cache/Redis | cachehits | Gyorsítótár-találatok | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits0 | Gyorsítótárbeli találatok (szegmens 0) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits1 | Gyorsítótárbeli találatok (1. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits2 | Gyorsítótárbeli találatok (2. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits3 | Gyorsítótárbeli találatok (3. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits4 | Gyorsítótár-találatok (4. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits5 | Gyorsítótárbeli találatok (szegmens 5) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits6 | Gyorsítótárbeli találatok (szilánk 6) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits7 | Gyorsítótárbeli találatok (szegmens 7) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits8 | Gyorsítótárbeli találatok (szilánk 8) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachehits9 | Gyorsítótárbeli találatok (szilánk 9) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cacheLatency | Gyorsítótár késési másodpercek (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft. cache/Redis | cachemisses | Gyorsítótár-lemaradás | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses0 | Gyorsítótár-kihagyás (0. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses1 | Gyorsítótár-lemaradás (1. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses2 | Gyorsítótár-lemaradás (2. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses3 | Gyorsítótár-lemaradás (3. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses4 | Gyorsítótár-lemaradás (4. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses5 | Gyorsítótár-lemaradás (5. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses6 | Gyorsítótár-lemaradás (szegmens 6) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses7 | Gyorsítótár-lemaradás (7. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses8 | Gyorsítótár-lemaradás (szegmens 8) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cachemisses9 | Gyorsítótár-lemaradás (szegmens 9) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | cacheRead | Gyorsítótár-olvasás | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead0 | Gyorsítótár-olvasás (0. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead1 | Gyorsítótár olvasása (1. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead2 | Gyorsítótár olvasása (2. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead3 | Gyorsítótár olvasása (3. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead4 | Gyorsítótár olvasása (4. Szilánk) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead5 | Gyorsítótár olvasása (5. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead6 | Gyorsítótár olvasása (szegmens 6) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead7 | Gyorsítótár olvasása (szegmens 7) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead8 | Gyorsítótár olvasása (szilánk 8) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheRead9 | Gyorsítótár olvasása (szegmens 9) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite | Gyorsítótár írása | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite0 | Gyorsítótár-írás (0. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite1 | Gyorsítótár-írás (1. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite2 | Gyorsítótár-írás (2. szegmens) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite3 | Gyorsítótár-írás (3. Szilánk) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite4 | Gyorsítótár-írás (4. Szilánk) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite5 | Gyorsítótár-írás (5. Szilánk) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite6 | Gyorsítótár írása (szegmens 6) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite7 | Gyorsítótár-írás (szegmens 7) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite8 | Gyorsítótár-írás (szegmens 8) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | cacheWrite9 | Gyorsítótár-írás (szegmens 9) | BytesPerSecond | Maximum
Igen | Microsoft. cache/Redis | connectedclients | Csatlakozott ügyfelek | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients0 | Csatlakoztatott ügyfelek (0. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients1 | Csatlakoztatott ügyfelek (1. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients2 | Csatlakoztatott ügyfelek (2. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients3 | Csatlakoztatott ügyfelek (3. Szilánk) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients4 | Csatlakoztatott ügyfelek (4. Szilánk) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients5 | Csatlakoztatott ügyfelek (5. Szilánk) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients6 | Csatlakoztatott ügyfelek (6. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients7 | Csatlakoztatott ügyfelek (7. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients8 | Csatlakoztatott ügyfelek (10. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | connectedclients9 | Csatlakoztatott ügyfelek (10. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | Hibák | Hibák | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | evictedkeys | Kizárt kulcsok | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys0 | Kizárt kulcsok (0. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys1 | Kizárt kulcsok (1. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys2 | Kizárt kulcsok (2. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys3 | Kizárt kulcsok (3. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys4 | Kizárt kulcsok (4. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys5 | Kizárt kulcsok (5. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys6 | Kizárt kulcsok (szegmens 6) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys7 | Kizárt kulcsok (7. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys8 | Kizárt kulcsok (szilánk 8) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | evictedkeys9 | Kizárt kulcsok (szilánk 9) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys | Lejárt kulcsok | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys0 | Lejárt kulcsok (szilánk 0) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys1 | Lejárt kulcsok (1. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys2 | Lejárt kulcsok (2. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys3 | Lejárt kulcsok (3. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys4 | Lejárt kulcsok (4. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys5 | Lejárt kulcsok (szilánk 5) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys6 | Lejárt kulcsok (szilánk 6) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys7 | Lejárt kulcsok (7. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys8 | Lejárt kulcsok (szilánk 8) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | expiredkeys9 | Lejárt kulcsok (szilánk 9) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands | Kap | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands0 | Beolvasás (0. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands1 | Beolvasás (1. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands2 | Beolvasás (2. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands3 | Beolvasás (3. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands4 | Beolvasás (4. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands5 | Beolvasás (szegmens 5) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands6 | Beolvasás (szilánk 6) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands7 | Beolvasás (szegmens 7) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands8 | Beolvasás (szilánk 8) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | getcommands9 | Beolvasás (szegmens 9) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | operationsPerSecond | Műveletek száma másodpercenként | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond0 | Művelet/másodperc (0. Szilánk) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond1 | Művelet/másodperc (1. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond2 | Művelet/másodperc (2. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond3 | Művelet/másodperc (3. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond4 | Művelet/másodperc (4. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond5 | Művelet/másodperc (5. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond6 | Művelet/másodperc (szegmens 6) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond7 | Művelet/másodperc (7. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond8 | Művelet/másodperc (szegmens 8) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | operationsPerSecond9 | Művelet/másodperc (szegmens 9) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime | CPU | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime0 | CPU (0. szegmens) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime1 | CPU (1. Szilánk) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime2 | CPU (2. szegmens) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime3 | CPU (3. Szilánk) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime4 | CPU (4. Szilánk) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime5 | CPU (5. Szilánk) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime6 | CPU (szegmens 6) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime7 | PROCESSZOR (7. szegmens) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime8 | CPU (szegmens 8) | Százalék | Maximum
Igen | Microsoft. cache/Redis | percentProcessorTime9 | CPU (szilánk 9) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad | Kiszolgáló terhelése | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad0 | Kiszolgáló terhelése (0. szegmens) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad1 | Kiszolgáló terhelése (1. szegmens) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad2 | Kiszolgáló betöltése (2. szegmens) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad3 | Kiszolgáló terhelése (3. Szilánk) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad4 | Kiszolgáló terhelése (4. Szilánk) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad5 | Kiszolgáló terhelése (5. Szilánk) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad6 | Kiszolgáló betöltése (szegmens 6) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad7 | Kiszolgáló terhelése (7. szegmens) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad8 | Kiszolgáló betöltése (szegmens 8) | Százalék | Maximum
Igen | Microsoft. cache/Redis | serverLoad9 | Kiszolgáló terhelése (szegmens 9) | Százalék | Maximum
Igen | Microsoft. cache/Redis | setcommands | Készletek | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands0 | Készletek (0. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands1 | Készletek (1. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands2 | Készletek (2. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands3 | Készletek (3. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands4 | Készletek (4. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands5 | Készletek (szilánk 5) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands6 | Készletek (szilánk 6) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands7 | Készletek (szegmens 7) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands8 | Készletek (szilánk 8) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | setcommands9 | Készletek (szilánk 9) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed | Összes művelet | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed0 | Összes művelet (0. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed1 | Összes művelet (1. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed2 | Összes művelet (2. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed3 | Összes művelet (3. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed4 | Összes művelet (4. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed5 | Összes művelet (5. Szilánk) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed6 | Összes művelet (szegmens 6) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed7 | Összes művelet (7. szegmens) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed8 | Összes művelet (szegmens 8) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalcommandsprocessed9 | Összes művelet (szegmens 9) | Mennyiség | Összes
Igen | Microsoft. cache/Redis | totalkeys | Kulcsok összesen | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys0 | Összes kulcs (szilánk 0) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys1 | Összes kulcs (1. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys2 | Összes kulcs (2. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys3 | Összes kulcs (3. Szilánk) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys4 | Összes kulcs (4. Szilánk) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys5 | Összes kulcs (5. szegmens) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys6 | Összes kulcs (szilánk 6) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys7 | Összes kulcs (szegmens 7) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys8 | Összes kulcs (szilánk 8) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | totalkeys9 | Összes kulcs (szilánk 9) | Mennyiség | Maximum
Igen | Microsoft. cache/Redis | usedmemory | Felhasznált memória | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory0 | Felhasznált memória (0. Szilánk) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory1 | Felhasznált memória (1. Szilánk) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory2 | Felhasznált memória (2. Szilánk) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory3 | Felhasznált memória (3. Szilánk) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory4 | Felhasznált memória (4. Szilánk) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory5 | Felhasznált memória (5. Szilánk) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory6 | Felhasznált memória (6. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory7 | Felhasznált memória (7. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory8 | Felhasznált memória (8. Szilánk) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemory9 | Felhasznált memória (10. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemorypercentage | Felhasznált memória százalékos aránya | Százalék | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss | Felhasznált memória RSS-címe | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss0 | Használt memória RSS-je (0. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss1 | Használt memória RSS-je (1. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss2 | Használt memória RSS-je (2. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss3 | Használt memória RSS-je (3. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss4 | Használt memória RSS-je (4. Szilánk) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss5 | Használt memória RSS-je (5. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss6 | Használt memória RSS-je (3. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss7 | Használt memória RSS-je (7. szegmens) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss8 | Használt memória RSS-je (szegmens 8) | Bájt | Maximum
Igen | Microsoft. cache/Redis | usedmemoryRss9 | Használt memória RSS-je (szegmens 9) | Bájt | Maximum
Nem | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Lemez olvasási sebessége (bájt/s) | Lemez olvasása | BytesPerSecond | Átlag
Igen | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Nem | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Lemez írási sebessége (bájt/s) | Lemez írása | BytesPerSecond | Átlag
Igen | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Bejövő hálózat | Bejövő hálózat | Bájt | Összes
Igen | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Kimenő hálózat | Kimenő hálózat | Bájt | Összes
Igen | Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Nem | Microsoft. ClassicCompute/virtualMachines | Lemez olvasási sebessége (bájt/s) | Lemez olvasása | BytesPerSecond | Átlag
Igen | Microsoft. ClassicCompute/virtualMachines | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Nem | Microsoft. ClassicCompute/virtualMachines | Lemez írási sebessége (bájt/s) | Lemez írása | BytesPerSecond | Átlag
Igen | Microsoft. ClassicCompute/virtualMachines | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft. ClassicCompute/virtualMachines | Bejövő hálózat | Bejövő hálózat | Bájt | Összes
Igen | Microsoft. ClassicCompute/virtualMachines | Kimenő hálózat | Kimenő hálózat | Bájt | Összes
Igen | Microsoft. ClassicCompute/virtualMachines | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts | Elérhetőség | Elérhetőség | Százalék | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Igen | Microsoft. ClassicStorage/storageAccounts | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. ClassicStorage/storageAccounts | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts | Tranzakciók | Tranzakciók | Mennyiség | Összes
Nem | Microsoft. ClassicStorage/storageAccounts | UsedCapacity | Felhasznált kapacitás | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | Elérhetőség | Elérhetőség | Százalék | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/blobServices | BlobCapacity | Blob-kapacitása | Bájt | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/blobServices | BlobCount | Blobok száma | Mennyiség | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | ContainerCount | Blobtárolók száma | Mennyiség | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Nem | Microsoft. ClassicStorage/storageAccounts/blobServices | IndexCapacity | Indexkapacitás | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/blobServices | Tranzakciók | Tranzakciók | Mennyiség | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | Elérhetőség | Elérhetőség | Százalék | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileCapacity | Fájl kapacitása | Bájt | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileCount | Fájlok száma | Mennyiség | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareCount | Fájlmegosztás száma | Mennyiség | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareQuota | Fájlmegosztás kvótájának mérete | Bájt | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareSnapshotCount | Fájlmegosztás pillanatképének száma | Mennyiség | Átlag
Nem | Microsoft. ClassicStorage/storageAccounts/fileServices | FileShareSnapshotSize | Fájlmegosztás pillanatképének mérete | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/fileServices | Tranzakciók | Tranzakciók | Mennyiség | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | Elérhetőség | Elérhetőség | Százalék | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueCapacity | Várólista kapacitása | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueCount | Várólista száma | Mennyiség | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueMessageCount | Üzenetsor-üzenetek száma | Mennyiség | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/queueServices | Tranzakciók | Tranzakciók | Mennyiség | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | Elérhetőség | Elérhetőség | Százalék | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | TableCapacity | Tábla kapacitása | Bájt | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | TableCount | Táblák száma | Mennyiség | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | TableEntityCount | Tábla entitások száma | Mennyiség | Átlag
Igen | Microsoft. ClassicStorage/storageAccounts/tableServices | Tranzakciók | Tranzakciók | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | BlockedCalls | Blokkolt hívások | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | CharactersTrained | Betanított karakterek | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | CharactersTranslated | Lefordított karakterek | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | ClientErrors | Ügyfél-hibák | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | DataIn | Bejövő adatforgalom | Bájt | Összes
Igen | Microsoft. CognitiveServices/fiókok | DataOut | Kimenő adatforgalom | Bájt | Összes
Igen | Microsoft. CognitiveServices/fiókok | Késés | Késés | Ezredmásodpercben | Átlag
Igen | Microsoft. CognitiveServices/fiókok | Kiszolgálóhibái | Kiszolgálói hibák | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | SpeechSessionDuration | Beszédfelismerési munkamenet időtartama | másodperc | Összes
Igen | Microsoft. CognitiveServices/fiókok | SuccessfulCalls | Sikeres hívások | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | TotalCalls | Hívások összesen | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | TotalErrors | Összes hiba | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | TotalTokenCalls | Jogkivonat-hívások összesen | Mennyiség | Összes
Igen | Microsoft. CognitiveServices/fiókok | TotalTransactions | Tranzakciók összesen | Mennyiség | Összes
Igen | Microsoft.Compute/virtualMachines | Felhasznált CPU-kreditek | Felhasznált CPU-kreditek | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachines | Fennmaradó CPU-kreditek | Fennmaradó CPU-kreditek | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez várakozási sorának mélysége | Adatlemez-várólista mélysége (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez-olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez olvasási műveletei (művelet/s) | Adatlemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez-írási sebesség (bájt/s) | Adatlemez-írási sebesség (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Adatlemez írási műveletei (művelet/s) | Adatlemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Lemez olvasási bájtjai | Lemez olvasási bájtjai | Bájt | Összes
Igen | Microsoft.Compute/virtualMachines | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Lemez írási bájtjai | Lemez írási bájtjai | Bájt | Összes
Igen | Microsoft.Compute/virtualMachines | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Bejövő forgalomfolyamok | Bejövő forgalomfolyamok | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachines | Bejövő folyamatok maximális létrehozási aránya | Bejövő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Bejövő hálózat | Számlázandó hálózat (elavult) | Bájt | Összes
Igen | Microsoft.Compute/virtualMachines | Teljes hálózat | Teljes hálózat | Bájt | Összes
Igen | Microsoft.Compute/virtualMachines | Kimenő hálózat | Hálózati kimenő számlázandó (elavult) | Bájt | Összes
Igen | Microsoft.Compute/virtualMachines | Kimenő hálózat összesen | Kimenő hálózat összesen | Bájt | Összes
Igen | Microsoft.Compute/virtualMachines | OS-lemez várakozási sorának mélysége | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez olvasási művelete/s | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | OPERÁCIÓSRENDSZER-lemez írási műveletei/s | OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez QD | OPERÁCIÓSRENDSZER-lemez QD (elavult) | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez olvasási művelet/mp | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Operációs rendszer/lemez írási műveletei másodpercenként | OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Kimenő folyamatok | Kimenő folyamatok | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachines | Kimenő folyamatok maximális létrehozási aránya | Kimenő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez QD | Adatlemez QD (elavult) | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez olvasási művelet/mp | Adatlemez olvasási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez írási sebessége (bájt/s) | Adatlemez-írási sebesség (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | /Lemez írási műveletek másodpercenként | Adatlemez írási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachines | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachines | Prémium szintű adatlemez-gyorsítótár – olvasási találat | Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachines | Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás | Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachines | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachines | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Felhasznált CPU-kreditek | Felhasznált CPU-kreditek | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Fennmaradó CPU-kreditek | Fennmaradó CPU-kreditek | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez várakozási sorának mélysége | Adatlemez-várólista mélysége (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez-olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez olvasási műveletei (művelet/s) | Adatlemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez-írási sebesség (bájt/s) | Adatlemez-írási sebesség (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Adatlemez írási műveletei (művelet/s) | Adatlemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Lemez olvasási bájtjai | Lemez olvasási bájtjai | Bájt | Összes
Igen | Microsoft.Compute/virtualMachineScaleSets | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Lemez írási bájtjai | Lemez írási bájtjai | Bájt | Összes
Igen | Microsoft.Compute/virtualMachineScaleSets | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Bejövő forgalomfolyamok | Bejövő forgalomfolyamok | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Bejövő folyamatok maximális létrehozási aránya | Bejövő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Bejövő hálózat | Számlázandó hálózat (elavult) | Bájt | Összes
Igen | Microsoft.Compute/virtualMachineScaleSets | Teljes hálózat | Teljes hálózat | Bájt | Összes
Igen | Microsoft.Compute/virtualMachineScaleSets | Kimenő hálózat | Hálózati kimenő számlázandó (elavult) | Bájt | Összes
Igen | Microsoft.Compute/virtualMachineScaleSets | Kimenő hálózat összesen | Kimenő hálózat összesen | Bájt | Összes
Igen | Microsoft.Compute/virtualMachineScaleSets | OS-lemez várakozási sorának mélysége | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez olvasási művelete/s | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | OPERÁCIÓSRENDSZER-lemez írási műveletei/s | OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez QD | OPERÁCIÓSRENDSZER-lemez QD (elavult) | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez olvasási művelet/mp | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Operációs rendszer/lemez írási műveletei másodpercenként | OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Kimenő folyamatok | Kimenő folyamatok | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Kimenő folyamatok maximális létrehozási aránya | Kimenő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez QD | Adatlemez QD (elavult) | Mennyiség | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez olvasási művelet/mp | Adatlemez olvasási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez írási sebessége (bájt/s) | Adatlemez-írási sebesség (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | /Lemez írási műveletek másodpercenként | Adatlemez írási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Prémium szintű adatlemez-gyorsítótár – olvasási találat | Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás | Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft.Compute/virtualMachineScaleSets | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Felhasznált CPU-kreditek | Felhasznált CPU-kreditek | Mennyiség | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Fennmaradó CPU-kreditek | Fennmaradó CPU-kreditek | Mennyiség | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Adatlemez várakozási sorának mélysége | Adatlemez-várólista mélysége (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Adatlemez-olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Adatlemez olvasási műveletei (művelet/s) | Adatlemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Adatlemez-írási sebesség (bájt/s) | Adatlemez-írási sebesség (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Adatlemez írási műveletei (művelet/s) | Adatlemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Lemez olvasási bájtjai | Lemez olvasási bájtjai | Bájt | Összes
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Lemez írási bájtjai | Lemez írási bájtjai | Bájt | Összes
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Bejövő forgalomfolyamok | Bejövő forgalomfolyamok | Mennyiség | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Bejövő folyamatok maximális létrehozási aránya | Bejövő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Bejövő hálózat | Számlázandó hálózat (elavult) | Bájt | Összes
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Teljes hálózat | Teljes hálózat | Bájt | Összes
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Kimenő hálózat | Hálózati kimenő számlázandó (elavult) | Bájt | Összes
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Kimenő hálózat összesen | Kimenő hálózat összesen | Bájt | Összes
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | OS-lemez várakozási sorának mélysége | OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez olvasási művelete/s | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | OPERÁCIÓSRENDSZER-lemez írási műveletei/s | OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez QD | OPERÁCIÓSRENDSZER-lemez QD (elavult) | Mennyiség | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez olvasási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez olvasási művelet/mp | OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez írási sebessége (bájt/s) | OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Operációs rendszer/lemez írási műveletei másodpercenként | OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Kimenő folyamatok | Kimenő folyamatok | Mennyiség | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Kimenő folyamatok maximális létrehozási aránya | Kimenő folyamatok maximális létrehozási aránya (előzetes verzió) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | /Lemez QD | Adatlemez QD (elavult) | Mennyiség | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | /Lemez olvasási sebesség (bájt/s) | Adatlemez-olvasási sebesség (bájt/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | /Lemez olvasási művelet/mp | Adatlemez olvasási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | /Lemez írási sebessége (bájt/s) | Adatlemez-írási sebesség (bájt/s) (elavult) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | /Lemez írási műveletek másodpercenként | Adatlemez írási műveletei (művelet/mp) (elavult) | CountPerSecond | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Prémium szintű adatlemez-gyorsítótár – olvasási találat | Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás | Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata | Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió) | Százalék | Átlag
Igen | Microsoft. számítás/virtualMachineScaleSets/virtualMachines | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása | Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió) | Százalék | Átlag
Igen | Microsoft. ContainerInstance/containerGroups | CpuUsage | Processzorhasználat | Mennyiség | Átlag
Igen | Microsoft. ContainerInstance/containerGroups | MemoryUsage | Memóriahasználat | Bájt | Átlag
Igen | Microsoft. ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | Másodpercenként fogadott hálózati bájtok száma | Bájt | Átlag
Igen | Microsoft. ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | Másodpercenként továbbított hálózati bájtok száma | Bájt | Átlag
Igen | Microsoft. ContainerRegistry/nyilvántartók | RunDuration | Futtatás időtartama | Ezredmásodperc | Összes
Igen | Microsoft. ContainerRegistry/nyilvántartók | SuccessfulPullCount | Sikeres lekérések száma | Mennyiség | Átlag
Igen | Microsoft. ContainerRegistry/nyilvántartók | SuccessfulPushCount | Sikeres leküldések száma | Mennyiség | Átlag
Igen | Microsoft. ContainerRegistry/nyilvántartók | TotalPullCount | Lekérések száma összesen | Mennyiség | Átlag
Igen | Microsoft. ContainerRegistry/nyilvántartók | TotalPushCount | Leküldések száma összesen | Mennyiség | Átlag
Nem | Microsoft. Tárolószolgáltatás/managedClusters | kube_node_status_allocatable_cpu_cores | A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma | Mennyiség | Átlag
Nem | Microsoft. Tárolószolgáltatás/managedClusters | kube_node_status_allocatable_memory_bytes | A felügyelt fürtben rendelkezésre álló memória teljes mennyisége | Bájt | Átlag
Nem | Microsoft. Tárolószolgáltatás/managedClusters | kube_node_status_condition | Különböző csomóponti feltételek állapota | Mennyiség | Átlag
Nem | Microsoft. Tárolószolgáltatás/managedClusters | kube_pod_status_phase | Hüvelyek száma fázis szerint | Mennyiség | Átlag
Nem | Microsoft. Tárolószolgáltatás/managedClusters | kube_pod_status_ready | A hüvelyek száma üzemkész állapotban | Mennyiség | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | Availablecapacity;) | Rendelkezésre álló kapacitás | Bájt | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | Feltöltött Felhőbeli bájtok (eszköz) | Bájt | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | Felhőbeli feltöltött bájtok (megosztás) | Bájt | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | Felhőbeli letöltési sebesség | BytesPerSecond | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | Felhőbeli letöltési teljesítmény (megosztás) | BytesPerSecond | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | Felhőbeli feltöltési sebesség | BytesPerSecond | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | Felhőbeli feltöltési sebesség (megosztás) | BytesPerSecond | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Edge-számítás – memóriahasználat | Százalék | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Edge-számítás – százalékos CPU | Százalék | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | Olvasási sebesség (hálózat) | BytesPerSecond | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | Írási átviteli sebesség (hálózat) | BytesPerSecond | Átlag
Igen | Microsoft. DataBoxEdge/dataBoxEdgeDevices | TotalCapacity | Teljes kapacitás | Bájt | Átlag
Igen | Microsoft. DataFactory/datafactories | FailedRuns | Sikertelen futtatások | Mennyiség | Összes
Igen | Microsoft. DataFactory/datafactories | SuccessfulRuns | Sikeres futtatások | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | ActivityCancelledRuns | A megszakított tevékenység metrikákat futtat | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | ActivityFailedRuns | Sikertelen tevékenység-futtatási metrikák | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | ActivitySucceededRuns | A sikeres tevékenység metrikákat futtat | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | FactorySizeInGbUnits | Gyári méret összesen (GB egység) | Mennyiség | Maximum
Igen | Microsoft. DataFactory/gyárak | IntegrationRuntimeAvailableMemory | Az Integration Runtime rendelkezésre álló memóriája | Bájt | Átlag
Igen | Microsoft. DataFactory/gyárak | IntegrationRuntimeAverageTaskPickupDelay | Integration Runtime-várólista időtartama | másodperc | Átlag
Igen | Microsoft. DataFactory/gyárak | IntegrationRuntimeCpuPercentage | Integration Runtime CPU-kihasználtsága | Százalék | Átlag
Igen | Microsoft. DataFactory/gyárak | IntegrationRuntimeQueueLength | Integration Runtime-várólista hossza | Mennyiség | Átlag
Igen | Microsoft. DataFactory/gyárak | MaxAllowedFactorySizeInGbUnits | Maximálisan engedélyezett gyári méret (GB egység) | Mennyiség | Maximum
Igen | Microsoft. DataFactory/gyárak | MaxAllowedResourceCount | Engedélyezett entitások maximális száma | Mennyiség | Maximum
Igen | Microsoft. DataFactory/gyárak | PipelineCancelledRuns | Megszakított folyamat-futtatási metrikák | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | PipelineFailedRuns | Sikertelen folyamat-futtatási metrikák | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | PipelineSucceededRuns | A folyamat sikeresen futtatja a metrikákat | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | ResourceCount | Entitások száma összesen | Mennyiség | Maximum
Igen | Microsoft. DataFactory/gyárak | TriggerCancelledRuns | Megszakított trigger-futtatási metrikák | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | TriggerFailedRuns | Sikertelen trigger-futtatási metrikák | Mennyiség | Összes
Igen | Microsoft. DataFactory/gyárak | TriggerSucceededRuns | A sikeres trigger metrikákat futtat | Mennyiség | Összes
Igen | Microsoft. DataLakeAnalytics/fiókok | JobAUEndedCancelled | Megszakított AU-idő | másodperc | Összes
Igen | Microsoft. DataLakeAnalytics/fiókok | JobAUEndedFailure | Sikertelen AU-idő | másodperc | Összes
Igen | Microsoft. DataLakeAnalytics/fiókok | JobAUEndedSuccess | Sikeres AU-idő | másodperc | Összes
Igen | Microsoft. DataLakeAnalytics/fiókok | JobEndedCancelled | Megszakított feladatok | Mennyiség | Összes
Igen | Microsoft. DataLakeAnalytics/fiókok | JobEndedFailure | Sikertelen feladatok | Mennyiség | Összes
Igen | Microsoft. DataLakeAnalytics/fiókok | JobEndedSuccess | Sikeres feladatok | Mennyiség | Összes
Igen | Microsoft. Data Lake Store/fiókok | DataRead | Olvasott információk | Bájt | Összes
Igen | Microsoft. Data Lake Store/fiókok | DataWritten | Írt adatértékek | Bájt | Összes
Igen | Microsoft. Data Lake Store/fiókok | ReadRequests | Olvasási kérelmek | Mennyiség | Összes
Igen | Microsoft. Data Lake Store/fiókok | TotalStorage | Összes tárhely | Bájt | Maximum
Igen | Microsoft. Data Lake Store/fiókok | WriteRequests | Írási kérelmek | Mennyiség | Összes
Igen | Microsoft. DBforMariaDB/kiszolgálók | active_connections | Aktív kapcsolatok | Mennyiség | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | backup_storage_used | Felhasznált biztonsági mentési tár | Bájt | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | connections_failed | Sikertelen kapcsolatok | Mennyiség | Összes
Igen | Microsoft. DBforMariaDB/kiszolgálók | cpu_percent | CPU-százalék | Százalék | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | io_consumption_percent | IO-százalék | Százalék | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | memory_percent | Memória százaléka | Százalék | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | network_bytes_egress | Kimenő hálózat | Bájt | Összes
Igen | Microsoft. DBforMariaDB/kiszolgálók | network_bytes_ingress | Bejövő hálózat | Bájt | Összes
Igen | Microsoft. DBforMariaDB/kiszolgálók | seconds_behind_master | Replikálás késése másodpercben | Mennyiség | Maximum
Igen | Microsoft. DBforMariaDB/kiszolgálók | serverlog_storage_limit | Kiszolgáló naplójának tárolási korlátja | Bájt | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | serverlog_storage_percent | Kiszolgáló naplójának tárolási százaléka | Százalék | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | serverlog_storage_usage | Kiszolgáló naplójának tárolója | Bájt | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | storage_limit | Tárolási korlát | Bájt | Maximum
Igen | Microsoft. DBforMariaDB/kiszolgálók | storage_percent | Tárolási százalék | Százalék | Átlag
Igen | Microsoft. DBforMariaDB/kiszolgálók | storage_used | Felhasznált tárterület | Bájt | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | active_connections | Aktív kapcsolatok | Mennyiség | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | backup_storage_used | Felhasznált biztonsági mentési tár | Bájt | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | connections_failed | Sikertelen kapcsolatok | Mennyiség | Összes
Igen | Microsoft. DBforMySQL/kiszolgálók | cpu_percent | CPU-százalék | Százalék | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | io_consumption_percent | IO-százalék | Százalék | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | memory_percent | Memória százaléka | Százalék | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | network_bytes_egress | Kimenő hálózat | Bájt | Összes
Igen | Microsoft. DBforMySQL/kiszolgálók | network_bytes_ingress | Bejövő hálózat | Bájt | Összes
Igen | Microsoft. DBforMySQL/kiszolgálók | seconds_behind_master | Replikálás késése másodpercben | Mennyiség | Maximum
Igen | Microsoft. DBforMySQL/kiszolgálók | serverlog_storage_limit | Kiszolgáló naplójának tárolási korlátja | Bájt | Maximum
Igen | Microsoft. DBforMySQL/kiszolgálók | serverlog_storage_percent | Kiszolgáló naplójának tárolási százaléka | Százalék | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | serverlog_storage_usage | Kiszolgáló naplójának tárolója | Bájt | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | storage_limit | Tárolási korlát | Bájt | Maximum
Igen | Microsoft. DBforMySQL/kiszolgálók | storage_percent | Tárolási százalék | Százalék | Átlag
Igen | Microsoft. DBforMySQL/kiszolgálók | storage_used | Felhasznált tárterület | Bájt | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | active_connections | Aktív kapcsolatok | Mennyiség | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | backup_storage_used | Felhasznált biztonsági mentési tár | Bájt | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | connections_failed | Sikertelen kapcsolatok | Mennyiség | Összes
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | cpu_percent | CPU-százalék | Százalék | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | io_consumption_percent | IO-százalék | Százalék | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | memory_percent | Memória százaléka | Százalék | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | network_bytes_egress | Kimenő hálózat | Bájt | Összes
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | network_bytes_ingress | Bejövő hálózat | Bájt | Összes
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | pg_replica_log_delay_in_bytes | Replikák maximális késése | Bájt | Maximum
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | pg_replica_log_delay_in_seconds | Replika késése | másodperc | Maximum
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | serverlog_storage_limit | Kiszolgáló naplójának tárolási korlátja | Bájt | Maximum
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | serverlog_storage_percent | Kiszolgáló naplójának tárolási százaléka | Százalék | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | serverlog_storage_usage | Kiszolgáló naplójának tárolója | Bájt | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | storage_limit | Tárolási korlát | Bájt | Maximum
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | storage_percent | Tárolási százalék | Százalék | Átlag
Igen | Microsoft. DBforPostgreSQL/kiszolgálók | storage_used | Felhasznált tárterület | Bájt | Átlag
Igen | Microsoft. DBforPostgreSQL/serversv2 | active_connections | Aktív kapcsolatok | Mennyiség | Átlag
Igen | Microsoft. DBforPostgreSQL/serversv2 | cpu_percent | CPU-százalék | Százalék | Átlag
Igen | Microsoft. DBforPostgreSQL/serversv2 | IOPS | IO | Mennyiség | Átlag
Igen | Microsoft. DBforPostgreSQL/serversv2 | memory_percent | Memória százaléka | Százalék | Átlag
Igen | Microsoft. DBforPostgreSQL/serversv2 | network_bytes_egress | Kimenő hálózat | Bájt | Összes
Igen | Microsoft. DBforPostgreSQL/serversv2 | network_bytes_ingress | Bejövő hálózat | Bájt | Összes
Igen | Microsoft. DBforPostgreSQL/serversv2 | storage_percent | Tárolási százalék | Százalék | Átlag
Igen | Microsoft. DBforPostgreSQL/serversv2 | storage_used | Felhasznált tárterület | Bájt | Átlag
Igen | Microsoft. Devices/Account | digitaltwins. telemetria. csomópontok | Digitális Twins-csomópont telemetria helyőrzője | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. commands. kimenő. elhagyása. sikeres | C2D üzenetek elhagyva | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. commands. kimenő. Complete. success | C2D-üzenetek kézbesítésének befejezése | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. commands. kimenő. elutasítás. sikeres | C2D-üzenetek elutasítva | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. Methods. failure | Sikertelen közvetlen metódushívások | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. Methods. requestSize | Közvetlen metódushívások kérelmeinek mérete | Bájt | Átlag
Igen | Microsoft. Devices/IotHubs | C2D. Methods. responseSize | Közvetlen metódushívások válaszainak mérete | Bájt | Átlag
Igen | Microsoft. Devices/IotHubs | C2D. Methods. success | Közvetlen metódusok sikeres meghívása | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. Twin. Read. failure | Sikertelen ikerolvasások a háttérrendszerből | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. Twin. Read. size | Ikereszköz-olvasások válaszmérete a háttérrendszerből | Bájt | Átlag
Igen | Microsoft. Devices/IotHubs | C2D. Twin. Read. success | Sikeres dupla olvasások a háttérből | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. Twin. Update. failure | Sikertelen ikereszköz-frissítések a háttérrendszerből | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2D. Twin. Update. size | Dupla frissítések mérete a háttérből | Bájt | Átlag
Igen | Microsoft. Devices/IotHubs | C2D. Twin. Update. success | Sikeres ikereszköz-frissítések a háttérrendszerből | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | C2DMessagesExpired | C2D-üzenetek lejárt (előzetes verzió) | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | konfigurációk | Konfigurációs metrikák | Mennyiség | Összes
Nem | Microsoft. Devices/IotHubs | connectedDeviceCount | Csatlakoztatott eszközök (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. beépített. események | Útválasztás: üzenetek/események küldésére küldött üzenetek | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. eventHubs | Útválasztás: az Event hub számára továbbított üzenetek | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. serviceBusQueues | Útválasztás: Service Bus üzenetsor számára továbbított üzenetek | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. serviceBusTopics | Útválasztás: Service Bus témakörbe küldött üzenetek | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. Storage | Útválasztás: a tárolóba küldött üzenetek | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. Storage. Blobok | Útválasztás: tárba továbbított blobok | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. kimenő. Storage. Bytes | Útválasztás: tárba továbbított adatok | Bájt | Összes
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. késleltetés. beépített. események | Útválasztás: üzenetek/események üzenetének késése | Ezredmásodperc | Átlag
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. késleltetés. eventHubs | Útválasztás: Event Hub üzeneteinek késése | Ezredmásodperc | Átlag
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. késleltetés. serviceBusQueues | Útválasztás: Service Bus-üzenetsor üzeneteinek késése | Ezredmásodperc | Átlag
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. késleltetés. serviceBusTopics | Útválasztás: Service Bus témakör üzenetének késése | Ezredmásodperc | Átlag
Igen | Microsoft. Devices/IotHubs | D2C. endpoints. látencia. Storage | Útválasztás: üzenetek késése a tároláshoz | Ezredmásodperc | Átlag
Igen | Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. eldobott | Útválasztás: telemetria üzenetek elvetve  | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. tartalék | Útválasztás: tartalékként továbbított üzenetek | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. érvénytelen | Útválasztás: Inkompatibilis telemetria-üzenetek | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. árva | Útválasztás: árva telemetria üzenetek  | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. telemetria. kimenő. sikeres | Útválasztás: telemetria üzenetek kézbesítése | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. telemetria. beáramló. allProtocol | Telemetria üzenetek küldése | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. telemetria. beáramló. sendThrottle | Szabályozási hibák száma | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. telemetria. beáramló. sikeres | Elküldött telemetriai üzenetek | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. Twin. Read. failure | Sikertelen ikerolvasások az eszközökről | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. Twin. Read. size | Ikereszköz-olvasások válaszmérete az eszközökből | Bájt | Átlag
Igen | Microsoft. Devices/IotHubs | D2C. Twin. Read. success | Sikeres dupla olvasások az eszközökről | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. Twin. Update. failure | Sikertelen ikereszköz-frissítések az eszközökből | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | D2C. Twin. Update. size | Az eszközökből származó kettős frissítések mérete | Bájt | Átlag
Igen | Microsoft. Devices/IotHubs | D2C. Twin. Update. success | Sikeres dupla frissítések az eszközökről | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | dailyMessageQuotaUsed | A felhasznált üzenetek teljes száma | Mennyiség | Átlag
Igen | Microsoft. Devices/IotHubs | deviceDataUsage | Az eszköz összes adatfelhasználása | Bájt | Összes
Igen | Microsoft. Devices/IotHubs | deviceDataUsageV2 | Az eszköz összes adatfelhasználása (előzetes verzió) | Bájt | Összes
Igen | Microsoft. Devices/IotHubs | Devices. connectedDevices. allProtocol | Csatlakoztatott eszközök (elavult)  | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | Devices. totalDevices | Összes eszköz (elavult) | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | EventGridDeliveries | Event Grid kézbesítések (előzetes verzió) | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | EventGridLatency | Event Grid késés (előzetes verzió) | Ezredmásodperc | Átlag
Igen | Microsoft. Devices/IotHubs | feladatok. cancelJob. hiba | Sikertelen feladatlemondások | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. cancelJob. sikeres | Sikeres feladatok törlése | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. kész | Befejezett feladatok | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. createDirectMethodJob. hiba | Sikertelen metódusmeghívási feladat-létrehozások | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. createDirectMethodJob. sikeres | Metódus-Meghívási feladatok sikeres létrehozása | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. createTwinUpdateJob. hiba | Sikertelen ikereszköz-frissítési feladat-létrehozások | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. createTwinUpdateJob. sikeres | A kettős frissítési feladatok sikeres létrehozása | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. sikertelen | Sikertelen feladatok | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. listJobs. hiba | Sikertelen hívások a feladatok listázásához | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. listJobs. sikeres | Sikeres hívások a feladatok listázásához | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. queryJobs. hiba | Sikertelen feladatlekérdezések | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | feladatok. queryJobs. sikeres | Sikeres feladatok lekérdezése | Mennyiség | Összes
Nem | Microsoft. Devices/IotHubs | totalDeviceCount | Összes eszköz (előzetes verzió) | Mennyiség | Átlag
Igen | Microsoft. Devices/IotHubs | twinQueries. hiba | Sikertelen ikerlekérdezések | Mennyiség | Összes
Igen | Microsoft. Devices/IotHubs | twinQueries.resultSize | Dupla lekérdezések eredményének mérete | Bájt | Átlag
Igen | Microsoft. Devices/IotHubs | twinQueries. success | Sikeres Twin-lekérdezések | Mennyiség | Összes
Igen | Microsoft. Devices/provisioningServices | AttestationAttempts | Igazolási kísérletek | Mennyiség | Összes
Igen | Microsoft. Devices/provisioningServices | DeviceAssignments | Hozzárendelt eszközök | Mennyiség | Összes
Igen | Microsoft. Devices/provisioningServices | RegistrationAttempts | Regisztrációs kísérletek | Mennyiség | Összes
Nem | Microsoft. DocumentDB/databaseAccounts | AvailableStorage | Rendelkezésre álló tár | Bájt | Összes
Nem | Microsoft. DocumentDB/databaseAccounts | CassandraConnectionClosures | Cassandra-kapcsolatok bezárása | Mennyiség | Összes
Nem | Microsoft. DocumentDB/databaseAccounts | CassandraRequestCharges | Cassandra-kérelmek díjai | Mennyiség | Összes
Nem | Microsoft. DocumentDB/databaseAccounts | CassandraRequests | Cassandra-kérelmek | Mennyiség | Mennyiség
Nem | Microsoft. DocumentDB/databaseAccounts | DataUsage | Adathasználat | Bájt | Összes
Igen | Microsoft. DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | Mennyiség | Mennyiség
Nem | Microsoft. DocumentDB/databaseAccounts | DocumentCount | Dokumentumok száma | Mennyiség | Összes
Nem | Microsoft. DocumentDB/databaseAccounts | DocumentQuota | Dokumentum kvótája | Bájt | Összes
Nem | Microsoft. DocumentDB/databaseAccounts | IndexUsage | Indexelés használata | Bájt | Összes
Nem | Microsoft. DocumentDB/databaseAccounts | MetadataRequests | Metaadat-kérelmek | Mennyiség | Mennyiség
Igen | Microsoft. DocumentDB/databaseAccounts | MongoRequestCharge | Mongo-kérelem díja | Mennyiség | Összes
Igen | Microsoft. DocumentDB/databaseAccounts | MongoRequests | Mongo kérelmek | Mennyiség | Mennyiség
Nem | Microsoft. DocumentDB/databaseAccounts | MongoRequestsCount | Mongo kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft. DocumentDB/databaseAccounts | MongoRequestsDelete | Mongo-törlési kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft. DocumentDB/databaseAccounts | MongoRequestsInsert | Mongo-beszúrási kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft. DocumentDB/databaseAccounts | MongoRequestsQuery | Mongo-lekérdezési kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft. DocumentDB/databaseAccounts | MongoRequestsUpdate | Mongo-frissítési kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft. DocumentDB/databaseAccounts | ProvisionedThroughput | Kiosztott átviteli kapacitás | Mennyiség | Maximum
Igen | Microsoft. DocumentDB/databaseAccounts | ReplicationLatency | P99 replikáció késése | Ezredmásodpercben | Átlag
Nem | Microsoft. DocumentDB/databaseAccounts | ServiceAvailability | Szolgáltatás rendelkezésre állása | Százalék | Átlag
Igen | Microsoft. DocumentDB/databaseAccounts | TotalRequests | Összes kérelem | Mennyiség | Mennyiség
Igen | Microsoft. DocumentDB/databaseAccounts | TotalRequestUnits | Kérelmek összes egysége | Mennyiség | Összes
Nem | Microsoft. EnterpriseKnowledgeGraph/szolgáltatások | FailureCount | Hibásak száma | Mennyiség | Mennyiség
Nem | Microsoft. EnterpriseKnowledgeGraph/szolgáltatások | SuccessCount | Sikeres műveletek száma | Mennyiség | Mennyiség
Nem | Microsoft. EnterpriseKnowledgeGraph/szolgáltatások | SuccessLatency | Sikeres késés | Ezredmásodpercben | Átlag
Nem | Microsoft. EnterpriseKnowledgeGraph/szolgáltatások | TransactionCount | Tranzakciók száma | Mennyiség | Mennyiség
Igen | Microsoft. EventGrid/tartományok | DeadLetteredCount | Kézbesítetlen levelek eseményei | Mennyiség | Összes
Nem | Microsoft. EventGrid/tartományok | DeliveryAttemptFailCount | Sikertelen kézbesítések eseményei | Mennyiség | Összes
Igen | Microsoft. EventGrid/tartományok | DeliverySuccessCount | Kézbesítési események | Mennyiség | Összes
Nem | Microsoft. EventGrid/tartományok | DestinationProcessingDurationInMs | Cél feldolgozási időtartama | Ezredmásodperc | Átlag
Igen | Microsoft. EventGrid/tartományok | DroppedEventCount | Eldobott események | Mennyiség | Összes
Igen | Microsoft. EventGrid/tartományok | MatchedEventCount | Egyeztetett események | Mennyiség | Összes
Igen | Microsoft. EventGrid/tartományok | PublishFailCount | Sikertelen események közzététele | Mennyiség | Összes
Igen | Microsoft. EventGrid/tartományok | PublishSuccessCount | Közzétett események | Mennyiség | Összes
Igen | Microsoft. EventGrid/tartományok | PublishSuccessLatencyInMs | Sikeres közzétételi késés | Mennyiség | Összes
Igen | Microsoft. EventGrid/eventSubscriptions | DeadLetteredCount | Kézbesítetlen levelek eseményei | Mennyiség | Összes
Nem | Microsoft. EventGrid/eventSubscriptions | DeliveryAttemptFailCount | Sikertelen kézbesítések eseményei | Mennyiség | Összes
Igen | Microsoft. EventGrid/eventSubscriptions | DeliverySuccessCount | Kézbesítési események | Mennyiség | Összes
Nem | Microsoft. EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | Cél feldolgozási időtartama | Ezredmásodperc | Átlag
Igen | Microsoft. EventGrid/eventSubscriptions | DroppedEventCount | Eldobott események | Mennyiség | Összes
Igen | Microsoft. EventGrid/eventSubscriptions | MatchedEventCount | Egyeztetett események | Mennyiség | Összes
Igen | Microsoft. EventGrid/extensionTopics | PublishFailCount | Sikertelen események közzététele | Mennyiség | Összes
Igen | Microsoft. EventGrid/extensionTopics | PublishSuccessCount | Közzétett események | Mennyiség | Összes
Igen | Microsoft. EventGrid/extensionTopics | PublishSuccessLatencyInMs | Sikeres közzétételi késés | Mennyiség | Összes
Igen | Microsoft. EventGrid/extensionTopics | UnmatchedEventCount | Páratlan események | Mennyiség | Összes
Igen | Microsoft. EventGrid/témakörök | PublishFailCount | Sikertelen események közzététele | Mennyiség | Összes
Igen | Microsoft. EventGrid/témakörök | PublishSuccessCount | Közzétett események | Mennyiség | Összes
Igen | Microsoft. EventGrid/témakörök | PublishSuccessLatencyInMs | Sikeres közzétételi késés | Mennyiség | Összes
Igen | Microsoft. EventGrid/témakörök | UnmatchedEventCount | Páratlan események | Mennyiség | Összes
Nem | Microsoft. EventHub/fürtök | Aktív kapcsolatai | Aktív kapcsolatai | Mennyiség | Átlag
Nem | Microsoft. EventHub/fürtök | AvailableMemory | Szabad memória | Százalék | Maximum
Nem | Microsoft. EventHub/fürtök | CaptureBacklog | Várakozó fájlok rögzítése. | Mennyiség | Összes
Nem | Microsoft. EventHub/fürtök | CapturedBytes | Rögzített bájtok száma. | Bájt | Összes
Nem | Microsoft. EventHub/fürtök | CapturedMessages | Rögzített üzenetek. | Mennyiség | Összes
Nem | Microsoft. EventHub/fürtök | ConnectionsClosed | A kapcsolatok lezárva. | Mennyiség | Átlag
Nem | Microsoft. EventHub/fürtök | ConnectionsOpened | Megnyitott kapcsolatok. | Mennyiség | Átlag
Nem | Microsoft. EventHub/fürtök | CPU | CPU | Százalék | Maximum
Igen | Microsoft. EventHub/fürtök | IncomingBytes | Bejövő bájtok. | Bájt | Összes
Igen | Microsoft. EventHub/fürtök | IncomingMessages | Bejövő üzenetek | Mennyiség | Összes
Igen | Microsoft. EventHub/fürtök | IncomingRequests | Bejövő kérelmek | Mennyiség | Összes
Igen | Microsoft. EventHub/fürtök | OutgoingBytes | Kimenő bájtok. | Bájt | Összes
Igen | Microsoft. EventHub/fürtök | OutgoingMessages | Kimenő üzenetek | Mennyiség | Összes
Nem | Microsoft. EventHub/fürtök | QuotaExceededErrors | A kvóta túllépte a hibákat. | Mennyiség | Összes
Nem | Microsoft. EventHub/fürtök | Kiszolgálóhibái | Kiszolgálói hibák. | Mennyiség | Összes
Nem | Microsoft. EventHub/fürtök | SuccessfulRequests | Sikeres kérések | Mennyiség | Összes
Nem | Microsoft. EventHub/fürtök | ThrottledRequests | Szabályozott kérelmek. | Mennyiség | Összes
Nem | Microsoft. EventHub/fürtök | UserErrors | Felhasználói hibák. | Mennyiség | Összes
Nem | Microsoft. EventHub/névterek | Aktív kapcsolatai | Aktív kapcsolatai | Mennyiség | Átlag
Nem | Microsoft. EventHub/névterek | CaptureBacklog | Várakozó fájlok rögzítése. | Mennyiség | Összes
Nem | Microsoft. EventHub/névterek | CapturedBytes | Rögzített bájtok száma. | Bájt | Összes
Nem | Microsoft. EventHub/névterek | CapturedMessages | Rögzített üzenetek. | Mennyiség | Összes
Nem | Microsoft. EventHub/névterek | ConnectionsClosed | A kapcsolatok lezárva. | Mennyiség | Átlag
Nem | Microsoft. EventHub/névterek | ConnectionsOpened | Megnyitott kapcsolatok. | Mennyiség | Átlag
Igen | Microsoft. EventHub/névterek | EHABL | Archivált várakozó üzenetek (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | EHAMBS | Archivált üzenetek átviteli sebessége (elavult) | Bájt | Összes
Igen | Microsoft. EventHub/névterek | EHAMSGS | Archivált üzenetek (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | EHINBYTES | Bejövő bájtok (elavult) | Bájt | Összes
Igen | Microsoft. EventHub/névterek | EHINMBS | Bejövő bájtok (elavult) (elavult) | Bájt | Összes
Igen | Microsoft. EventHub/névterek | EHINMSGS | Bejövő üzenetek (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | EHOUTBYTES | Kimenő bájtok (elavult) | Bájt | Összes
Igen | Microsoft. EventHub/névterek | EHOUTMBS | Kimenő bájtok (elavult) (elavult) | Bájt | Összes
Igen | Microsoft. EventHub/névterek | EHOUTMSGS | Kimenő üzenetek (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | FAILREQ | Sikertelen kérelmek (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | IncomingBytes | Bejövő bájtok. | Bájt | Összes
Igen | Microsoft. EventHub/névterek | IncomingMessages | Bejövő üzenetek | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | IncomingRequests | Bejövő kérelmek | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | INMSGS | Bejövő üzenetek (elavult) (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | INREQS | Bejövő kérelmek (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | INTERer | Belső kiszolgálói hibák (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | MISCERR | Egyéb hibák (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | OutgoingBytes | Kimenő bájtok. | Bájt | Összes
Igen | Microsoft. EventHub/névterek | OutgoingMessages | Kimenő üzenetek | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | OUTMSGS | Kimenő üzenetek (elavult) (elavult) | Mennyiség | Összes
Nem | Microsoft. EventHub/névterek | QuotaExceededErrors | A kvóta túllépte a hibákat. | Mennyiség | Összes
Nem | Microsoft. EventHub/névterek | Kiszolgálóhibái | Kiszolgálói hibák. | Mennyiség | Összes
Nem | Microsoft. EventHub/névterek | Méret | Méret | Bájt | Átlag
Nem | Microsoft. EventHub/névterek | SuccessfulRequests | Sikeres kérések | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | SUCCREQ | Sikeres kérések (elavult) | Mennyiség | Összes
Igen | Microsoft. EventHub/névterek | SVRBSY | Kiszolgáló által foglalt hibák (elavult) | Mennyiség | Összes
Nem | Microsoft. EventHub/névterek | ThrottledRequests | Szabályozott kérelmek. | Mennyiség | Összes
Nem | Microsoft. EventHub/névterek | UserErrors | Felhasználói hibák. | Mennyiség | Összes
Igen | Microsoft. HDInsight/fürtök | CategorizedGatewayRequests | Kategorizált átjáró-kérelmek | Mennyiség | Összes
Igen | Microsoft. HDInsight/fürtök | GatewayRequests | Átjáróra vonatkozó kérelmek | Mennyiség | Összes
Igen | Microsoft. HDInsight/fürtök | NumActiveWorkers | Aktív feldolgozók száma | Mennyiség | Maximum
Igen | Microsoft. HDInsight/fürtök | ScalingRequests | Kérelmek méretezése | Mennyiség | Maximum
Igen | Microsoft. bepillantások/AutoscaleSettings | MetricThreshold | Metrika küszöbértéke | Mennyiség | Átlag
Igen | Microsoft. bepillantások/AutoscaleSettings | ObservedCapacity | Megfigyelt kapacitás | Mennyiség | Átlag
Igen | Microsoft. bepillantások/AutoscaleSettings | ObservedMetricValue | Megfigyelt metrika értéke | Mennyiség | Átlag
Igen | Microsoft. bepillantások/AutoscaleSettings | ScaleActionsInitiated | Elindított skálázási műveletek | Mennyiség | Összes
Igen | Microsoft. bepillantások/összetevők | availabilityResults/availabilityPercentage | Elérhetőség | Százalék | Átlag
Nem | Microsoft. bepillantások/összetevők | availabilityResults/darabszám | Rendelkezésre állási tesztek | Mennyiség | Mennyiség
Igen | Microsoft. bepillantások/összetevők | availabilityResults/időtartam | Rendelkezésre állási teszt időtartama | Ezredmásodpercben | Átlag
Igen | Microsoft. bepillantások/összetevők | browserTimings/networkDuration | Oldal betöltési hálózati kapcsolati ideje | Ezredmásodpercben | Átlag
Igen | Microsoft. bepillantások/összetevők | browserTimings/processingDuration | Ügyfél feldolgozási ideje | Ezredmásodpercben | Átlag
Igen | Microsoft. bepillantások/összetevők | browserTimings/receiveDuration | Válaszidő fogadása | Ezredmásodpercben | Átlag
Igen | Microsoft. bepillantások/összetevők | browserTimings/sendDuration | Kérelem küldési ideje | Ezredmásodpercben | Átlag
Igen | Microsoft. bepillantások/összetevők | browserTimings/totalDuration | Böngésző oldalának betöltési ideje | Ezredmásodpercben | Átlag
Nem | Microsoft. bepillantások/összetevők | függőségek/darabszám | Függőségi hívások | Mennyiség | Mennyiség
Igen | Microsoft. bepillantások/összetevők | függőségek/időtartam | Függőség időtartama | Ezredmásodpercben | Átlag
Nem | Microsoft. bepillantások/összetevők | függőségek/sikertelen | Függőségi hívások hibái | Mennyiség | Mennyiség
Nem | Microsoft. bepillantások/összetevők | kivételek/böngésző | Böngészőkivételek | Mennyiség | Mennyiség
Igen | Microsoft. bepillantások/összetevők | kivételek/darabszám | Kivételek | Mennyiség | Mennyiség
Nem | Microsoft. bepillantások/összetevők | kivételek/kiszolgáló | Kiszolgálói kivételek | Mennyiség | Mennyiség
Igen | Microsoft. bepillantások/összetevők | Oldalmegtekintések/darabszám | Lapok nézetei | Mennyiség | Mennyiség
Igen | Microsoft. bepillantások/összetevők | Oldalmegtekintések/időtartam | Oldal nézet betöltési ideje | Ezredmásodpercben | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/exceptionsPerSecond | Kivételek aránya | CountPerSecond | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/memoryAvailableBytes | Igénybe vehető memória | Bájt | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/processCpuPercentage | Processzor feldolgozása | Százalék | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/processIOBytesPerSecond | Folyamat i/o-sebessége | BytesPerSecond | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/processorCpuPercentage | Processzoridő | Százalék | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/processPrivateBytes | Saját bájtok feldolgozása | Bájt | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/requestExecutionTime | HTTP-kérelem végrehajtási ideje | Ezredmásodpercben | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/requestsInQueue | HTTP-kérelmek az alkalmazás-várólistában | Mennyiség | Átlag
Igen | Microsoft. bepillantások/összetevők | performanceCounters/requestsPerSecond | HTTP-kérelmek gyakorisága | CountPerSecond | Átlag
Nem | Microsoft. bepillantások/összetevők | kérelmek/darabszám | Kiszolgálói kérelmek | Mennyiség | Mennyiség
Igen | Microsoft. bepillantások/összetevők | kérelmek/időtartam | Kiszolgáló válaszideje | Ezredmásodpercben | Átlag
Nem | Microsoft. bepillantások/összetevők | kérelmek/sikertelen | Sikertelen kérelmek | Mennyiség | Mennyiség
Nem | Microsoft. bepillantások/összetevők | kérelmek/díjszabás | Kiszolgálói kérelmek gyakorisága | CountPerSecond | Átlag
Igen | Microsoft. bepillantások/összetevők | nyomkövetés/darabszám | Hívásláncok | Mennyiség | Mennyiség
Igen | Microsoft. kulcstartó/tárolók | ServiceApiHit | Összes szolgáltatási API-találat | Mennyiség | Mennyiség
Igen | Microsoft. kulcstartó/tárolók | ServiceApiLatency | Általános szolgáltatás API-késése | Ezredmásodperc | Átlag
Igen | Microsoft. kulcstartó/tárolók | ServiceApiResult | Összes szolgáltatás API-eredményei | Mennyiség | Mennyiség
Igen | Microsoft. Kusto/fürtök | CacheUtilization | Gyorsítótár kihasználtsága | Százalék | Átlag
Igen | Microsoft. Kusto/fürtök | ContinuousExportMaxLatenessMinutes | Folyamatos exportálás maximális késői percben | Mennyiség | Maximum
Igen | Microsoft. Kusto/fürtök | ContinuousExportNumOfRecordsExported | Folyamatos exportálás – számú exportált rekord | Mennyiség | Összes
Igen | Microsoft. Kusto/fürtök | ContinuousExportPendingCount | Folyamatos exportálás függőben lévő darabszáma | Mennyiség | Maximum
Igen | Microsoft. Kusto/fürtök | ContinuousExportResult | Folyamatos exportálás eredménye | Mennyiség | Mennyiség
Igen | Microsoft. Kusto/fürtök | CPU | CPU | Százalék | Átlag
Igen | Microsoft. Kusto/fürtök | EventsProcessedForEventHubs | Feldolgozott események (Event/IoT hubok esetében) | Mennyiség | Összes
Igen | Microsoft. Kusto/fürtök | ExportUtilization | Exportálás kihasználtsága | Százalék | Maximum
Igen | Microsoft. Kusto/fürtök | IngestionLatencyInSeconds | Betöltési késleltetés (másodperc) | másodperc | Átlag
Igen | Microsoft. Kusto/fürtök | IngestionResult | Betöltés eredménye | Mennyiség | Mennyiség
Igen | Microsoft. Kusto/fürtök | IngestionUtilization | Betöltés kihasználtsága | Százalék | Átlag
Igen | Microsoft. Kusto/fürtök | IngestionVolumeInMB | Betöltési mennyiség (MB) | Mennyiség | Összes
Igen | Microsoft. Kusto/fürtök | KeepAlive | Életben tartása | Mennyiség | Átlag
Igen | Microsoft. Kusto/fürtök | QueryDuration | Lekérdezés időtartama | Ezredmásodperc | Átlag
Igen | Microsoft. Kusto/fürtök | SteamingIngestRequestRate | Folyamatos átviteli kérelmek gyakorisága | Mennyiség | RateRequestsPerSecond
Igen | Microsoft. Kusto/fürtök | StreamingIngestDataRate | Adatforgalom adatátviteli sebessége | Mennyiség | Átlag
Igen | Microsoft. Kusto/fürtök | StreamingIngestDuration | Folyamatos átvitel időtartama | Ezredmásodperc | Átlag
Igen | Microsoft. Kusto/fürtök | StreamingIngestResults | Folyamatos átvitel eredménye | Mennyiség | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | ActionLatency | Művelet késése  | másodperc | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | ActionsCompleted | Befejezett műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | ActionsFailed | Sikertelen műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | ActionsSkipped | Kihagyott műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | ActionsStarted | Elindított műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | ActionsSucceeded | Sikeres műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | ActionSuccessLatency | Művelet sikerességi késése  | másodperc | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | ActionThrottledEvents | Művelet által szabályozott események | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | Összekötő memóriahasználat integrációs szolgáltatási környezet | Százalék | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | integrációs szolgáltatási környezet összekötő-processzorának használata | Százalék | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | integrációs szolgáltatási környezet munkafolyamat-memóriahasználat | Százalék | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | A integrációs szolgáltatási környezet munkafolyamat-processzorának használata | Százalék | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | RunFailurePercentage | Sikertelen futtatások százalékos aránya | Százalék | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | RunLatency | Késés futtatása | másodperc | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | RunsCancelled | Megszakított futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | RunsCompleted | Befejezett futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | RunsFailed | Sikertelen futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | RunsStarted | Elindított futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | RunsSucceeded | Sikeres futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | RunStartThrottledEvents | Indítási leszabályozású események futtatása | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | RunSuccessLatency | Sikeres Futtatás késése | másodperc | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | RunThrottledEvents | Szabályozott események futtatása | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggerFireLatency | Kiváltó tűz késése  | másodperc | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggerLatency | Trigger késése  | másodperc | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggersCompleted | Befejezett eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggersFailed | Sikertelen eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggersFired | Kilőtt eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggersSkipped | Kihagyott eseményindítók | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggersStarted | Elindított eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggersSucceeded | Sikeres eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggerSuccessLatency | Sikeres triggerek késése  | másodperc | Átlag
Igen | Microsoft. Logic/integrationServiceEnvironments | TriggerThrottledEvents | Kiváltott események indítása | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | ActionLatency | Művelet késése  | másodperc | Átlag
Igen | Microsoft. Logic/munkafolyamatok | ActionsCompleted | Befejezett műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | ActionsFailed | Sikertelen műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | ActionsSkipped | Kihagyott műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | ActionsStarted | Elindított műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | ActionsSucceeded | Sikeres műveletek  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | ActionSuccessLatency | Művelet sikerességi késése  | másodperc | Átlag
Igen | Microsoft. Logic/munkafolyamatok | ActionThrottledEvents | Művelet által szabályozott események | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | BillableActionExecutions | Számlázható műveletek végrehajtása | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | BillableTriggerExecutions | Számlázható trigger-végrehajtások | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | BillingUsageNativeOperation | A natív művelet végrehajtásához használt számlázási használat | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | BillingUsageNativeOperation | A natív művelet végrehajtásához használt számlázási használat | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | BillingUsageStandardConnector | A standard szintű összekötők végrehajtásának számlázási használata | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | BillingUsageStandardConnector | A standard szintű összekötők végrehajtásának számlázási használata | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | BillingUsageStorageConsumption | Számlázási használat a tárolási felhasználás végrehajtásához | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | BillingUsageStorageConsumption | Számlázási használat a tárolási felhasználás végrehajtásához | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | RunFailurePercentage | Sikertelen futtatások százalékos aránya | Százalék | Összes
Igen | Microsoft. Logic/munkafolyamatok | RunLatency | Késés futtatása | másodperc | Átlag
Igen | Microsoft. Logic/munkafolyamatok | RunsCancelled | Megszakított futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | RunsCompleted | Befejezett futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | RunsFailed | Sikertelen futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | RunsStarted | Elindított futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | RunsSucceeded | Sikeres futtatások | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | RunStartThrottledEvents | Indítási leszabályozású események futtatása | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | RunSuccessLatency | Sikeres Futtatás késése | másodperc | Átlag
Igen | Microsoft. Logic/munkafolyamatok | RunThrottledEvents | Szabályozott események futtatása | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | TotalBillableExecutions | Számlázandó végrehajtások összesen | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | TriggerFireLatency | Kiváltó tűz késése  | másodperc | Átlag
Igen | Microsoft. Logic/munkafolyamatok | TriggerLatency | Trigger késése  | másodperc | Átlag
Igen | Microsoft. Logic/munkafolyamatok | TriggersCompleted | Befejezett eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | TriggersFailed | Sikertelen eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | TriggersFired | Kilőtt eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | TriggersSkipped | Kihagyott eseményindítók | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | TriggersStarted | Elindított eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | TriggersSucceeded | Sikeres eseményindítók  | Mennyiség | Összes
Igen | Microsoft. Logic/munkafolyamatok | TriggerSuccessLatency | Sikeres triggerek késése  | másodperc | Átlag
Igen | Microsoft. Logic/munkafolyamatok | TriggerThrottledEvents | Kiváltott események indítása | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | Aktív magok | Aktív magok | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Aktív csomópontok | Aktív csomópontok | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Befejezett futtatások | Befejezett futtatások | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | Sikertelen futtatások | Sikertelen futtatások | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | Üresjárati magok | Üresjárati magok | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Tétlen csomópontok | Tétlen csomópontok | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Magok kihagyása | Magok kihagyása | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Csomópontok elhagyása | Csomópontok elhagyása | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Modell-üzembehelyezés sikertelen | Modell-üzembehelyezés sikertelen | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | Modell-üzembehelyezés elindítva | Modell-üzembehelyezés elindítva | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | Modell-üzembehelyezés sikerült | Modell-üzembehelyezés sikerült | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | A modell regisztrálása nem sikerült | A modell regisztrálása nem sikerült | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | A modell regisztrálása sikerült | A modell regisztrálása sikerült | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | Előzik magok | Előzik magok | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Előzik-csomópontok | Előzik-csomópontok | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Kvóta kihasználtsága (%) | Kvóta kihasználtsága (%) | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Elindított futtatások | Elindított futtatások | Mennyiség | Összes
Igen | Microsoft. MachineLearningServices/munkaterületek | Magok összesen | Magok összesen | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Csomópontok összesen | Csomópontok összesen | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Használhatatlan magok | Használhatatlan magok | Mennyiség | Átlag
Igen | Microsoft. MachineLearningServices/munkaterületek | Használhatatlan csomópontok | Használhatatlan csomópontok | Mennyiség | Átlag
Igen | Microsoft. Maps/fiókok | Elérhetőség | Elérhetőség | Százalék | Átlag
Nem | Microsoft. Maps/fiókok | Használat | Használat | Mennyiség | Mennyiség
Igen | Microsoft. Media/Mediaservices | AssetCount | Eszközök száma | Mennyiség | Átlag
Igen | Microsoft. Media/Mediaservices | AssetQuota | Eszköz kvótája | Mennyiség | Átlag
Igen | Microsoft. Media/Mediaservices | AssetQuotaUsedPercentage | Eszköz kvótájának kihasználtsága (%) | Százalék | Átlag
Igen | Microsoft. Media/Mediaservices | ContentKeyPolicyCount | Tartalmi kulcsokra vonatkozó szabályzatok száma | Mennyiség | Átlag
Igen | Microsoft. Media/Mediaservices | ContentKeyPolicyQuota | Tartalmi kulcs házirend-kvótája | Mennyiség | Átlag
Igen | Microsoft. Media/Mediaservices | ContentKeyPolicyQuotaUsedPercentage | Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya | Százalék | Átlag
Igen | Microsoft. Media/Mediaservices | StreamingPolicyCount | Folyamatos átviteli szabályzatok száma | Mennyiség | Átlag
Igen | Microsoft. Media/Mediaservices | StreamingPolicyQuota | Streaming Policy-kvóta | Mennyiség | Átlag
Igen | Microsoft. Media/Mediaservices | StreamingPolicyQuotaUsedPercentage | Adatfolyam-házirend kvótájának kihasználtsága (%) | Százalék | Átlag
Igen | Microsoft. Media/Mediaservices/streamingEndpoints | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Igen | Microsoft. Media/Mediaservices/streamingEndpoints | Kérelmek | Kérelmek | Mennyiség | Összes
Igen | Microsoft. Media/Mediaservices/streamingEndpoints | SuccessE2ELatency | A Befejezés végének késése | Ezredmásodperc | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | GCPauseTotalCount | GC-szüneteltetések száma | Mennyiség | Összes
Igen | Microsoft. Microservices4Spring/appClusters | GCPauseTotalTime | GC felfüggesztésének teljes ideje | Ezredmásodperc | Összes
Igen | Microsoft. Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | A rendelkezésre álló régi generációs adatméret maximális száma | Bájt | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | OldGenMemoryPoolBytes | A régi generációs adatméret | Bájt | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | OldGenPromotedBytes | Előléptetés a régi generációs adatméretre | Bájt | Maximum
Igen | Microsoft. Microservices4Spring/appClusters | ServiceCpuUsagePercentage | Szolgáltatás CPU-kihasználtságának százalékos aránya | Százalék | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | ServiceMemoryCommitted | Szolgáltatáshoz hozzárendelt memória | Bájt | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | ServiceMemoryMax | Szolgáltatás memóriájának maximális száma | Bájt | Maximum
Igen | Microsoft. Microservices4Spring/appClusters | ServiceMemoryUsed | Szolgáltatás felhasznált memóriája | Bájt | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | SystemCpuUsagePercentage | CPU-használat százalékos aránya | Százalék | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | TomcatErrorCount | Tomcat globális hiba | Mennyiség | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatReceivedBytes | Tomcat összesen fogadott bájtok | Bájt | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatRequestMaxTime | Tomcat-kérelem maximális ideje | Ezredmásodperc | Maximum
Igen | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalCount | Tomcat-kérelem összesített száma | Mennyiség | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalTime | Tomcat-kérelem teljes ideje | Ezredmásodperc | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatResponseAvgTime | Tomcat-kérelem átlagos ideje | Ezredmásodperc | Átlag
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSentBytes | Tomcat összesen eljuttatott bájtok száma | Bájt | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Tomcat-munkamenet élő száma | Mennyiség | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Tomcat-munkamenet maximális aktív száma | Mennyiség | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Tomcat-munkamenet maximális élettartama | Ezredmásodperc | Maximum
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionCreatedCount | Tomcat-munkamenet létrehozva szám | Mennyiség | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionExpiredCount | A Tomcat-munkamenet lejárt | Mennyiség | Összes
Igen | Microsoft. Microservices4Spring/appClusters | TomcatSessionRejectedCount | A Tomcat-munkamenet elutasította a darabszámot | Mennyiség | Összes
Igen | Microsoft. Microservices4Spring/appClusters | YoungGenPromotedBytes | Népszerűsítse a fiatal generáció adatméretét | Bájt | Maximum
Igen | Microsoft. NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | Lefoglalt kötet-készlet | Bájt | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | Kötet készletének teljes logikai mérete | Bájt | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | AverageReadLatency | Olvasási késleltetés átlagos késése | Ezredmásodpercben | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | AverageWriteLatency | Írási késleltetés átlagos késése | Ezredmásodpercben | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | ReadIops | IOPS olvasása | CountPerSecond | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | VolumeLogicalSize | Kötet logikai mérete | Bájt | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | VolumeSnapshotSize | Kötet pillanatképének mérete | Bájt | Átlag
Igen | Microsoft. NetApp/netAppAccounts/capacityPools/kötetek | WriteIops | IOPS írása | CountPerSecond | Átlag
Nem | Microsoft. Network/applicationGateways | ApplicationGatewayTotalTime | Application Gateway teljes idő | Ezredmásodpercben | Átlag
Nem | Microsoft. Network/applicationGateways | AvgRequestCountPerHealthyHost | Percenkénti kérelmek száma kifogástalan állapotú gazdagépen | Mennyiség | Átlag
Nem | Microsoft. Network/applicationGateways | BackendConnectTime | Háttérbeli kapcsolat ideje | Ezredmásodpercben | Átlag
Nem | Microsoft. Network/applicationGateways | BackendFirstByteResponseTime | Háttérbeli első bájt válaszideje | Ezredmásodpercben | Átlag
Nem | Microsoft. Network/applicationGateways | BackendLastByteResponseTime | Háttérbeli utolsó bájt válaszideje | Ezredmásodpercben | Átlag
Igen | Microsoft. Network/applicationGateways | BackendResponseStatus | Háttérbeli válasz állapota | Mennyiség | Összes
Igen | Microsoft. Network/applicationGateways | BlockedCount | Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása | Mennyiség | Összes
Igen | Microsoft. Network/applicationGateways | BlockedReqCount | Webalkalmazási tűzfal letiltott kérelmek száma | Mennyiség | Összes
Igen | Microsoft. Network/applicationGateways | BytesReceived | Fogadott bájtok száma | Bájt | Összes
Igen | Microsoft. Network/applicationGateways | BytesSent | Eljuttatott bájtok | Bájt | Összes
Nem | Microsoft. Network/applicationGateways | CapacityUnits | Aktuális kapacitási egységek | Mennyiség | Átlag
Nem | Microsoft. Network/applicationGateways | ClientRtt | Ügyfél-RTT | Ezredmásodpercben | Átlag
Nem | Microsoft. Network/applicationGateways | ComputeUnits | Aktuális számítási egységek | Mennyiség | Átlag
Igen | Microsoft. Network/applicationGateways | Összege | Aktuális kapcsolatok | Mennyiség | Összes
Igen | Microsoft. Network/applicationGateways | FailedRequests | Sikertelen kérelmek | Mennyiség | Összes
Igen | Microsoft. Network/applicationGateways | HealthyHostCount | Kifogástalan állapotú gazdagépek száma | Mennyiség | Átlag
Igen | Microsoft. Network/applicationGateways | MatchedCount | Webalkalmazási tűzfal teljes szabályának eloszlása | Mennyiség | Összes
Igen | Microsoft. Network/applicationGateways | ResponseStatus | Válasz állapota | Mennyiség | Összes
Nem | Microsoft. Network/applicationGateways | Adatforgalom | Adatforgalom | BytesPerSecond | Átlag
Igen | Microsoft. Network/applicationGateways | TlsProtocol | Ügyfél TLS protokoll | Mennyiség | Összes
Igen | Microsoft. Network/applicationGateways | TotalRequests | Összes kérelem | Mennyiség | Összes
Igen | Microsoft. Network/applicationGateways | UnhealthyHostCount | Nem kifogástalan állapotú gazdagépek száma | Mennyiség | Átlag
Igen | Microsoft. Network/azurefirewalls | ApplicationRuleHit | Alkalmazás-szabályok találatok száma | Mennyiség | Összes
Igen | Microsoft. Network/azurefirewalls | DataProcessed | Feldolgozott adatok | Bájt | Összes
Igen | Microsoft. Network/azurefirewalls | FirewallHealth | Tűzfal állapota | Százalék | Átlag
Igen | Microsoft. Network/azurefirewalls | NetworkRuleHit | Hálózati szabályok találatok száma | Mennyiség | Összes
Igen | Microsoft. Network/azurefirewalls | SNATPortUtilization | SNAT-portok kihasználtsága | Százalék | Átlag
Igen | Microsoft. Network/Connections | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/Connections | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/dnszones | QueryVolume | Lekérdezési kötet | Mennyiség | Összes
Nem | Microsoft. Network/dnszones | RecordSetCapacityUtilization | Rekordazonosító kapacitásának kihasználtsága | Százalék | Maximum
Igen | Microsoft. Network/dnszones | RecordSetCount | Rekordok készletének száma | Mennyiség | Maximum
Igen | Microsoft. Network/expressRouteCircuits | ArpAvailability | ARP rendelkezésre állása | Százalék | Átlag
Igen | Microsoft. Network/expressRouteCircuits | BgpAvailability | BGP rendelkezésre állása | Százalék | Átlag
Nem | Microsoft. Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/expressRouteCircuits/társak | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/expressRouteCircuits/társak | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Nem | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/expressRoutePorts | AdminState | AdminState | Mennyiség | Átlag
Igen | Microsoft. Network/expressRoutePorts | LineProtocol | LineProtocol | Mennyiség | Átlag
Igen | Microsoft. Network/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Átlag
Igen | Microsoft. Network/expressRoutePorts | RxLightLevel | RxLightLevel | Mennyiség | Átlag
Igen | Microsoft. Network/expressRoutePorts | TxLightLevel | TxLightLevel | Mennyiség | Átlag
Igen | Microsoft. Network/frontdoors | BackendHealthPercentage | Háttér állapotának százalékos aránya | Százalék | Átlag
Igen | Microsoft. Network/frontdoors | BackendRequestCount | Háttérbeli kérelmek száma | Mennyiség | Összes
Igen | Microsoft. Network/frontdoors | BackendRequestLatency | Háttérbeli kérelmek késése | Ezredmásodpercben | Átlag
Igen | Microsoft. Network/frontdoors | BillableResponseSize | Számlázható válasz mérete | Bájt | Összes
Igen | Microsoft. Network/frontdoors | RequestCount | Kérelmek száma | Mennyiség | Összes
Igen | Microsoft. Network/frontdoors | RequestSize | Kérelem mérete | Bájt | Összes
Igen | Microsoft. Network/frontdoors | ResponseSize | Válasz mérete | Bájt | Összes
Igen | Microsoft. Network/frontdoors | TotalLatency | Teljes késés | Ezredmásodpercben | Átlag
Igen | Microsoft. Network/frontdoors | WebApplicationFirewallRequestCount | Webalkalmazási tűzfalra vonatkozó kérelmek száma | Mennyiség | Összes
Nem | Microsoft.Network/loadBalancers | AllocatedSnatPorts | Lefoglalt SNAT portok (előzetes verzió) | Mennyiség | Összes
Igen | Microsoft.Network/loadBalancers | ByteCount | Bájtok száma | Mennyiség | Összes
Igen | Microsoft.Network/loadBalancers | DipAvailability | Állapot mintavételi állapota | Mennyiség | Átlag
Igen | Microsoft.Network/loadBalancers | PacketCount | Csomagok száma | Mennyiség | Összes
Igen | Microsoft.Network/loadBalancers | SnatConnectionCount | SNAT-kapcsolatok száma | Mennyiség | Összes
Igen | Microsoft.Network/loadBalancers | SYNCount | SYN-szám | Mennyiség | Összes
Nem | Microsoft.Network/loadBalancers | UsedSnatPorts | Használt SNAT-portok (előzetes verzió) | Mennyiség | Összes
Igen | Microsoft.Network/loadBalancers | VipAvailability | Adatelérési út rendelkezésre állása | Mennyiség | Átlag
Igen | Microsoft. Network/networkInterfaces | BytesReceivedRate | Fogadott bájtok száma | Bájt | Összes
Igen | Microsoft. Network/networkInterfaces | BytesSentRate | Eljuttatott bájtok | Bájt | Összes
Igen | Microsoft. Network/networkInterfaces | PacketsReceivedRate | Fogadott csomagok | Mennyiség | Összes
Igen | Microsoft. Network/networkInterfaces | PacketsSentRate | Küldött csomagok | Mennyiség | Összes
Igen | Microsoft. Network/networkWatchers/connectionMonitors | AverageRoundtripMs | Átlagos menetidő (MS) | Ezredmásodpercben | Átlag
Igen | Microsoft. Network/networkWatchers/connectionMonitors | ChecksFailedPercent | Sikertelen ellenőrzések százalékos aránya (előzetes verzió) | Százalék | Átlag
Igen | Microsoft. Network/networkWatchers/connectionMonitors | ProbesFailedPercent | %-Os mintavétel sikertelen | Százalék | Átlag
Igen | Microsoft. Network/networkWatchers/connectionMonitors | RoundTripTimeMs | Oda-és visszaút időpontja (MS) (előzetes verzió) | Ezredmásodpercben | Átlag
Igen | Microsoft.Network/publicIPAddresses | ByteCount | Bájtok száma | Mennyiség | Összes
Igen | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | Eldobott bejövő bájtok DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | Továbbított bejövő bájtok DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | BytesInDDoS | Bejövő bájtok DDoS | BytesPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | Bejövő SYN-csomagok a DDoS-elhárítás elindításához | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | Bejövő TCP-csomagok a DDoS-mérséklés indításához | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | Bejövő UDP-csomagok a DDoS-elhárítás elindításához | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | DDoS-támadás alatt vagy nem | Mennyiség | Maximum
Igen | Microsoft.Network/publicIPAddresses | PacketCount | Csomagok száma | Mennyiség | Összes
Igen | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | Eldobott bejövő csomagok DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | Továbbított bejövő csomagok DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | PacketsInDDoS | Bejövő csomagok DDoS | CountPerSecond | Maximum
Igen | Microsoft.Network/publicIPAddresses | SynCount | SYN-szám | Mennyiség | Összes
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
Igen | Microsoft.Network/publicIPAddresses | VipAvailability | Adatelérési út rendelkezésre állása | Mennyiség | Átlag
Igen | Microsoft. Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | Végponti állapot végpont szerint | Mennyiség | Maximum
Igen | Microsoft. Network/trafficManagerProfiles | QpsByEndpoint | Visszaadott végponti lekérdezések | Mennyiség | Összes
Igen | Microsoft. Network/virtualNetworkGateways | AverageBandwidth | Átjáró S2S sávszélessége | BytesPerSecond | Átlag
Igen | Microsoft. Network/virtualNetworkGateways | P2SBandwidth | Átjáró P2S sávszélessége | BytesPerSecond | Átlag
Igen | Microsoft. Network/virtualNetworkGateways | P2SConnectionCount | P2S-kapcsolatok száma | Mennyiség | Maximum
Igen | Microsoft. Network/virtualNetworkGateways | TunnelAverageBandwidth | Bújtatási sávszélesség | BytesPerSecond | Átlag
Igen | Microsoft. Network/virtualNetworkGateways | TunnelEgressBytes | Alagút kimenő bájtjai | Bájt | Összes
Igen | Microsoft. Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | Alagút kimenő terminálszolgáltatási eltérési csomagjainak eldobása | Mennyiség | Összes
Igen | Microsoft. Network/virtualNetworkGateways | TunnelEgressPackets | Alagút kimenő csomagjai | Mennyiség | Összes
Igen | Microsoft. Network/virtualNetworkGateways | TunnelIngressBytes | Alagutak bejövő bájtjai | Bájt | Összes
Igen | Microsoft. Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | Az alagút bejövő HOZZÁFÉRÉSe nem egyezik a csomagok eldobásával | Mennyiség | Összes
Igen | Microsoft. Network/virtualNetworkGateways | TunnelIngressPackets | Bújtatási bejövő csomagok | Mennyiség | Összes
Igen | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | A pingelések időpontjának kerekítése egy virtuális géphez | Ezredmásodpercben | Átlag
Igen | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | Sikertelen pingelések egy virtuális géphez | Százalék | Átlag
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő | Bejövő üzenetek | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ALL. failedrequests | Minden bejövő sikertelen kérelem | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ALL. requests | Minden bejövő kérelem | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ütemezett | Ütemezett leküldéses értesítések elküldve | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | bejövő. ütemezett. Mégse | Ütemezett leküldéses értesítések megszakítva | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | a telepítés. All | Telepítési felügyeleti műveletek | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | telepítés. Törlés | Telepítési műveletek törlése | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | telepítés. Get | Telepítési műveletek beolvasása | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | Installation. patch | Javítások telepítési műveletei | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | telepítési. upsert | Telepítési műveletek létrehozása vagy frissítése | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | notificationhub. leküldések | Minden kimenő értesítés | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. badorexpiredchannel | Rossz vagy lejárt csatorna-hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. channelerror | Csatorna hibái | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. invalidpayload | Hasznos adatok | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. pnserror | Külső értesítési rendszerhibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. allpns. sikeres | Sikeres értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. badchannel | APNS hibás csatorna hiba | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. expiredchannel | APNS lejárt csatorna hibája | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. invalidcredentials | APNS-hitelesítési hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. invalidnotificationsize | A APNS érvénytelen értesítési méretet észlelt | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. pnserror | APNS hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. apns. sikeres | APNS sikeres értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. authenticationerror | GCM-hitelesítési hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. badchannel | GCM hibás csatorna hiba | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. expiredchannel | GCM lejárt csatorna hibája | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. invalidcredentials | GCM-hitelesítési hibák (érvénytelen hitelesítő adatok) | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. invalidnotificationformat | GCM – érvénytelen értesítési formátum | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. invalidnotificationsize | A GCM érvénytelen értesítési méretet észlelt | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. pnserror | GCM hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. sikeres | GCM sikeres értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. szabályozva | GCM-szabályozású értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. GCM. wrongchannel | GCM rossz csatorna hibája | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. authenticationerror | MPNS-hitelesítési hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. badchannel | MPNS hibás csatorna hiba | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. channeldisconnected | MPNS csatorna leválasztva | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. Dropped | MPNS eldobott értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. invalidcredentials | MPNS – érvénytelen hitelesítő adatok | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. invalidnotificationformat | MPNS – érvénytelen értesítési formátum | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. pnserror | MPNS hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. sikeres | MPNS sikeres értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. mpns. szabályozva | MPNS-szabályozású értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. authenticationerror | WNS-hitelesítési hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. badchannel | WNS hibás csatorna hiba | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. channeldisconnected | WNS csatorna leválasztva | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. channelthrottled | WNS csatorna szabályozása | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. Dropped | WNS eldobott értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. expiredchannel | WNS lejárt csatorna hibája | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidcredentials | WNS-hitelesítési hibák (érvénytelen hitelesítő adatok) | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidnotificationformat | WNS – érvénytelen értesítési formátum | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidnotificationsize | A WNS érvénytelen értesítési méretet észlelt | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. invalidtoken | WNS-hitelesítési hibák (érvénytelen token) | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. pnserror | WNS hibák | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. sikeres | WNS sikeres értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. szabályozva | WNS-szabályozású értesítések | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. tokenproviderunreachable | WNS-hitelesítési hibák (nem érhető el) | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | kimenő. wns. wrongtoken | WNS-hitelesítési hibák (hibás token) | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. All | Regisztrációs műveletek | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. Create | Regisztráció-létrehozási műveletek | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. Delete | Regisztrációs törlési műveletek | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. Get | Regisztrálási olvasási műveletek | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | regisztráció. frissítés | Regisztrációs frissítési műveletek | Mennyiség | Összes
Igen | Microsoft. NotificationHubs/névterek/NotificationHubs | ütemezett. függőben | Függőben lévő ütemezett értesítések | Mennyiség | Összes
Igen | Microsoft. OperationalInsights/munkaterületek | Rendelkezésre álló memória Average_%-ban | Rendelkezésre álló memória%-ban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ rendelkezésre álló szabad hely a lapozófájlban | Rendelkezésre álló swap-terület (%) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_%-os előjegyzett bájtok használatban | Előjegyzett memória%-ban használatban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_% DPC idő | % DPC idő | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ szabad inode (%) | Szabad inode%-ban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ szabad terület (%) | Szabad terület százalékos aránya | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ szabad terület (%) | Szabad terület százalékos aránya | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ üresjárati idő%-ban | Üresjáratban eltöltött időhányad (%) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_%-os megszakítási idő | Megszakítási idő%-ban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_% IO várakozási idő | I/o várakozási idő%-ban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_%-os szép idő | % Nice idő | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_%-os privilegizált idő | %-Os privilegizált idő | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ processzoridő | A processzor kihasználtsága (%) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ processzoridő | A processzor kihasználtsága (%) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ használt inode | Felhasznált inode%-ban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Felhasznált memória Average_%-ban | Felhasznált memória (%) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ felhasznált terület%-ban | Felhasznált terület (%) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_ felhasznált lapozófájl-terület | Felhasznált swap-terület%-ban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_%-os felhasználói idő | Felhasználói idő%-ban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Available MB-ban | Rendelkezésre álló memória (MB) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Available MB memória | Rendelkezésre álló memória (MB) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Available MB-ban való swap | Rendelkezésre álló memória (MB) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz. | Átlagos írási idő (mp/olvasás) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz. | Átlagos írási idő (mp/olvasás) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Avg. teljesítmény mp/átvitelben | Átlagos műveleti idő (mp/átvitel) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz. | Átlagos írási idő (mp/írás) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz. | Átlagos írási idő (mp/írás) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Fogadott Average_Bytes/mp | Fogadott bájtok/s | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Küldési Average_Bytes másodpercenként | Küldési sebesség (bájt/s) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Bytes összesen/mp | Összes bájt/mp | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Current a lemez várólistájának hossza | Lemez aktuális várólistájának hossza | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Olvasási sebesség (bájt/s) Average_Disk | Lemezolvasás sebessége bájt/mp-ben | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Olvasási Average_Disk másodpercenként | Lemezolvasások/mp | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Olvasási Average_Disk másodpercenként | Lemezolvasások/mp | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Disk adatátvitel másodpercenként | Lemez átvitele másodpercenként | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Disk adatátvitel másodpercenként | Lemez átvitele másodpercenként | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Írási sebesség (bájt/s) Average_Disk | Lemezírás sebessége bájt/mp-ben | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Írás/mp Average_Disk | Lemezírások/mp | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Írás/mp Average_Disk | Lemezírások/mp | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Free megabájt | Szabad terület (MB) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Free megabájt | Szabad terület (MB) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Fizikai memória Average_Free | Szabad fizikai memória | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Free lemezterület a Lapozófájlokban | Szabad terület a Lapozófájlokban | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Free virtuális memória | Szabad virtuális memória | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Logical lemez sebessége (bájt/s) | Logikai lemez sebessége (bájt/s) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Olvasási Average_Page másodpercenként | Olvasott lap/mperc | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Írás/mp Average_Page | Írt lap/mperc | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Pages előállított/másodperc | Mozgatott lapok (lap/sec) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Pct emelt szintű idő | PCT rendszerjogosultságú idő | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Pct felhasználói idő | PCT felhasználói idő | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Physical lemez sebessége (bájt/s) | Fizikai lemez sebessége (bájt/s) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Processes | Folyamatok | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Processor várólista hossza | Processzor-várólista hossza | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Lapozófájlokban tárolt Average_Size | Lapozófájlokban tárolt méret | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Total bájtok | Bájtok összesen | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Fogadott Average_Total bájtok száma | Fogadott bájtok összesen | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Total továbbított bájtok | Továbbított bájtok összesen | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Ütközések Average_Total | Ütközések összesen | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Total fogadott csomagok | Fogadott csomagok összesen | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Total továbbított csomagok | Továbbított csomagok összesen | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Rx-hibák Average_Total | Rx-hibák összesen | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Total TX-hibák | TX-hibák összesen | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Uptime | Hasznos üzemidő | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Used MB-ban felcserélt terület | Felhasznált memória (MB) – lapozófájl | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Used memória kilobájtban | Felhasznált memória (kilobájt) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Used memória MB-ban | Felhasznált memória (MB) | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Users | Felhasználók | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Average_Virtual megosztott memória | Virtuális megosztott memória | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Esemény | Esemény | Mennyiség | Átlag
Igen | Microsoft. OperationalInsights/munkaterületek | Szívverés | Szívverés | Mennyiség | Összes
Igen | Microsoft. OperationalInsights/munkaterületek | Frissítés | Frissítés | Mennyiség | Átlag
Igen | Microsoft. PowerBIDedicated/kapacitások | memory_metric | Memória | Bájt | Átlag
Igen | Microsoft. PowerBIDedicated/kapacitások | memory_thrashing_metric | Memória-Kiverés (adathalmazok) | Százalék | Átlag
Igen | Microsoft. PowerBIDedicated/kapacitások | qpu_high_utilization_metric | QPU magas kihasználtság | Mennyiség | Összes
Igen | Microsoft. PowerBIDedicated/kapacitások | QueryDuration | Lekérdezés időtartama (adatkészletek) | Ezredmásodperc | Átlag
Igen | Microsoft. PowerBIDedicated/kapacitások | QueryPoolJobQueueLength | Lekérdezési készlet nyomtatási várólistájának hossza (adatkészletek) | Mennyiség | Átlag
Nem | Microsoft. Relay/névterek | Aktív kapcsolatai | Aktív kapcsolatai | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | ActiveListeners | ActiveListeners | Mennyiség | Összes
Igen | Microsoft. Relay/névterek | BytesTransferred | BytesTransferred | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Figyelőkapcsolatokra – vonatkozó ügyfélhibái | Figyelőkapcsolatokra – vonatkozó ügyfélhibái | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Figyelőkapcsolatokra – ServerError | Figyelőkapcsolatokra – ServerError | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Figyelőkapcsolatokra – sikeres | Figyelőkapcsolatokra – sikeres | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Figyelőkapcsolatokra – TotalRequests | Figyelőkapcsolatokra – TotalRequests | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Bontásai | Bontásai | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Feladói kapcsolatokra – vonatkozó ügyfélhibái | Feladói kapcsolatokra – vonatkozó ügyfélhibái | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Feladói kapcsolatokra – ServerError | Feladói kapcsolatokra – ServerError | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Feladói kapcsolatokra – sikeres | Feladói kapcsolatokra – sikeres | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | Feladói kapcsolatokra – TotalRequests | Feladói kapcsolatokra – TotalRequests | Mennyiség | Összes
Nem | Microsoft. Relay/névterek | SenderDisconnects | SenderDisconnects | Mennyiség | Összes
Igen | Microsoft. Search/searchServices | SearchLatency | Keresési késés | másodperc | Átlag
Igen | Microsoft. Search/searchServices | SearchQueriesPerSecond | Keresési lekérdezések másodpercenként | CountPerSecond | Átlag
Igen | Microsoft. Search/searchServices | ThrottledSearchQueriesPercentage | Szabályozott keresési lekérdezések százalékos aránya | Százalék | Átlag
Nem | Microsoft. ServiceBus/névterek | Aktív kapcsolatai | Aktív kapcsolatai | Mennyiség | Összes
Nem | Microsoft. ServiceBus/névterek | ActiveMessages | Üzenetsor vagy témakör aktív üzeneteinek száma. | Mennyiség | Átlag
Nem | Microsoft. ServiceBus/névterek | ConnectionsClosed | A kapcsolatok lezárva. | Mennyiség | Átlag
Nem | Microsoft. ServiceBus/névterek | ConnectionsOpened | Megnyitott kapcsolatok. | Mennyiség | Átlag
Nem | Microsoft. ServiceBus/névterek | CPUXNS | CPU (elavult) | Százalék | Maximum
Nem | Microsoft. ServiceBus/névterek | DeadletteredMessages | Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. | Mennyiség | Átlag
Igen | Microsoft. ServiceBus/névterek | IncomingMessages | Bejövő üzenetek | Mennyiség | Összes
Igen | Microsoft. ServiceBus/névterek | IncomingRequests | Bejövő kérelmek | Mennyiség | Összes
Nem | Microsoft. ServiceBus/névterek | Üzenetek | Üzenetsor vagy témakör üzeneteinek száma. | Mennyiség | Átlag
Nem | Microsoft. ServiceBus/névterek | NamespaceCpuUsage | CPU | Százalék | Maximum
Nem | Microsoft. ServiceBus/névterek | NamespaceMemoryUsage | Memóriahasználat | Százalék | Maximum
Igen | Microsoft. ServiceBus/névterek | OutgoingMessages | Kimenő üzenetek | Mennyiség | Összes
Nem | Microsoft. ServiceBus/névterek | ScheduledMessages | Az üzenetsor/témakör ütemezett üzeneteinek száma. | Mennyiség | Átlag
Nem | Microsoft. ServiceBus/névterek | Kiszolgálóhibái | Kiszolgálói hibák. | Mennyiség | Összes
Nem | Microsoft. ServiceBus/névterek | Méret | Méret | Bájt | Átlag
Nem | Microsoft. ServiceBus/névterek | SuccessfulRequests | Sikeres kérések | Mennyiség | Összes
Nem | Microsoft. ServiceBus/névterek | ThrottledRequests | Szabályozott kérelmek. | Mennyiség | Összes
Nem | Microsoft. ServiceBus/névterek | UserErrors | Felhasználói hibák. | Mennyiség | Összes
Nem | Microsoft. ServiceBus/névterek | WSXNS | Memóriahasználat (elavult) | Százalék | Maximum
Nem | Microsoft. ServiceFabricMesh/alkalmazások | ActualCpu | ActualCpu | Mennyiség | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | ActualMemory | ActualMemory | Bájt | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | AllocatedCpu | AllocatedCpu | Mennyiség | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | AllocatedMemory | AllocatedMemory | Bájt | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | ApplicationStatus | ApplicationStatus | Mennyiség | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | Tároló állapota: | Tároló állapota: | Mennyiség | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | CpuUtilization | CpuUtilization | Százalék | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | MemoryUtilization | MemoryUtilization | Százalék | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | RestartCount | RestartCount | Mennyiség | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | ServiceReplicaStatus | ServiceReplicaStatus | Mennyiség | Átlag
Nem | Microsoft. ServiceFabricMesh/alkalmazások | ServiceStatus | ServiceStatus | Mennyiség | Átlag
Igen | Microsoft. SignalRService/szignáló | ConnectionCount | Kapcsolatok száma | Mennyiség | Maximum
Igen | Microsoft. SignalRService/szignáló | InboundTraffic | Bejövő forgalom | Bájt | Összes
Igen | Microsoft. SignalRService/szignáló | MessageCount | Üzenetek száma | Mennyiség | Összes
Igen | Microsoft. SignalRService/szignáló | OutboundTraffic | Kimenő forgalom | Bájt | Összes
Igen | Microsoft. SignalRService/szignáló | SystemErrors | Rendszerhibák | Százalék | Maximum
Igen | Microsoft. SignalRService/szignáló | UserErrors | Felhasználói hibák | Százalék | Maximum
Igen | Microsoft. SQL/managedInstances | avg_cpu_percent | Átlagos CPU-százalék | Százalék | Átlag
Igen | Microsoft. SQL/managedInstances | io_bytes_read | I/o-bájtok olvasása | Bájt | Átlag
Igen | Microsoft. SQL/managedInstances | io_bytes_written | I/o-bájtok írása | Bájt | Átlag
Igen | Microsoft. SQL/managedInstances | io_requests | IO-kérelmek száma | Mennyiség | Átlag
Igen | Microsoft. SQL/managedInstances | reserved_storage_mb | Tárterület fenntartva | Mennyiség | Átlag
Igen | Microsoft. SQL/managedInstances | storage_space_used_mb | Felhasznált tárterület | Mennyiség | Átlag
Igen | Microsoft. SQL/managedInstances | virtual_core_count | Virtuális mag száma | Mennyiség | Átlag
Nem | Microsoft. SQL/kiszolgálók | database_dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Nem | Microsoft. SQL/kiszolgálók | database_storage_used | Felhasznált adatterület | Bájt | Átlag
Igen | Microsoft. SQL/kiszolgálók | dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók | dtu_used | Használt DTU | Mennyiség | Átlag
Igen | Microsoft. SQL/kiszolgálók | storage_used | Felhasznált adatterület | Bájt | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | allocated_data_storage | Lefoglalt adatterület | Bájt | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | app_cpu_billed | Az alkalmazás CPU-számlázása | Mennyiség | Összes
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | app_cpu_percent | Alkalmazás CPU-aránya | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | app_memory_percent | Az alkalmazás memóriájának százaléka | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | blocked_by_firewall | Tűzfal blokkolja | Mennyiség | Összes
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | cache_hit_percent | Gyorsítótár találati százaléka | Százalék | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | cache_used_percent | Gyorsítótár használt százaléka | Százalék | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | connection_failed | Sikertelen kapcsolatok | Mennyiség | Összes
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | connection_successful | Sikeres kapcsolatok | Mennyiség | Összes
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | cpu_limit | CPU-korlát | Mennyiség | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | cpu_percent | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | cpu_used | Felhasznált CPU | Mennyiség | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | holtpont | Holtpontok | Mennyiség | Összes
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | dtu_limit | DTU korlátja | Mennyiség | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | dtu_used | Használt DTU | Mennyiség | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | dwu_consumption_percent | DWU százalékos aránya | Százalék | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | dwu_limit | DWU korlátja | Mennyiség | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | dwu_used | Használt DWU | Mennyiség | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | local_tempdb_usage_percent | Helyi tempdb százalékos aránya | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | log_write_percent | Naplózási IO-százalék | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | memory_usage_percent | Memória százaléka | Százalék | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | physical_data_read_percent | Adat IO kihasználtsága (%) | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | sessions_percent | Munkamenetek százalékos aránya | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | tárterület | Felhasznált adatterület | Bájt | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | storage_percent | Felhasznált adatterület százalékos aránya | Százalék | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | tempdb_data_size | Tempdb adatfájl mérete (kilobájt) | Mennyiség | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | tempdb_log_size | Tempdb-naplófájl mérete (kilobájt) | Mennyiség | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | tempdb_log_used_percent | Tempdb százalékos naplója használatban | Százalék | Maximum
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | workers_percent | Munkavégzők százalékos aránya | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/adatbázisok | xtp_storage_percent | Memóriában tárolt OLTP tárolási százaléka | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | allocated_data_storage | Lefoglalt adatterület | Bájt | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | allocated_data_storage_percent | Lefoglalt adatterület százalékos aránya | Százalék | Maximum
Igen | Microsoft. SQL/kiszolgálók/elasticPools | cpu_limit | CPU-korlát | Mennyiség | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | cpu_percent | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | cpu_used | Felhasznált CPU | Mennyiség | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_allocated_data_storage | Lefoglalt adatterület | Bájt | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_cpu_limit | CPU-korlát | Mennyiség | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_cpu_percent | Processzorhasználat (%) | Százalék | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_cpu_used | Felhasznált CPU | Mennyiség | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_eDTU_used | használt eDTU | Mennyiség | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_log_write_percent | Naplózási IO-százalék | Százalék | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_physical_data_read_percent | Adat IO kihasználtsága (%) | Százalék | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_sessions_percent | Munkamenetek százalékos aránya | Százalék | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_storage_used | Felhasznált adatterület | Bájt | Átlag
Nem | Microsoft. SQL/kiszolgálók/elasticPools | database_workers_percent | Munkavégzők százalékos aránya | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | dtu_consumption_percent | DTU-kihasználtság (%) | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | eDTU_limit | eDTU korlátja | Mennyiség | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | eDTU_used | használt eDTU | Mennyiség | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | log_write_percent | Naplózási IO-százalék | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | physical_data_read_percent | Adat IO kihasználtsága (%) | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | sessions_percent | Munkamenetek százalékos aránya | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | storage_limit | Adatok maximális mérete | Bájt | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | storage_percent | Felhasznált adatterület százalékos aránya | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | storage_used | Felhasznált adatterület | Bájt | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | tempdb_data_size | Tempdb adatfájl mérete (kilobájt) | Mennyiség | Maximum
Igen | Microsoft. SQL/kiszolgálók/elasticPools | tempdb_log_size | Tempdb-naplófájl mérete (kilobájt) | Mennyiség | Maximum
Igen | Microsoft. SQL/kiszolgálók/elasticPools | tempdb_log_used_percent | Tempdb százalékos naplója használatban | Százalék | Maximum
Igen | Microsoft. SQL/kiszolgálók/elasticPools | workers_percent | Munkavégzők százalékos aránya | Százalék | Átlag
Igen | Microsoft. SQL/kiszolgálók/elasticPools | xtp_storage_percent | Memóriában tárolt OLTP tárolási százaléka | Százalék | Átlag
Igen | Microsoft.Storage/storageAccounts | Elérhetőség | Elérhetőség | Százalék | Átlag
Igen | Microsoft.Storage/storageAccounts | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Igen | Microsoft.Storage/storageAccounts | Belépő | Belépő | Bájt | Összes
Igen | Microsoft.Storage/storageAccounts | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft.Storage/storageAccounts | Tranzakciók | Tranzakciók | Mennyiség | Összes
Nem | Microsoft.Storage/storageAccounts | UsedCapacity | Felhasznált kapacitás | Bájt | Átlag
Igen | Microsoft. Storage/storageAccounts/blobServices | Elérhetőség | Elérhetőség | Százalék | Átlag
Nem | Microsoft. Storage/storageAccounts/blobServices | BlobCapacity | Blob-kapacitása | Bájt | Átlag
Nem | Microsoft. Storage/storageAccounts/blobServices | BlobCount | Blobok száma | Mennyiség | Átlag
Igen | Microsoft. Storage/storageAccounts/blobServices | ContainerCount | Blobtárolók száma | Mennyiség | Átlag
Igen | Microsoft. Storage/storageAccounts/blobServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Nem | Microsoft. Storage/storageAccounts/blobServices | IndexCapacity | Indexkapacitás | Bájt | Átlag
Igen | Microsoft. Storage/storageAccounts/blobServices | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. Storage/storageAccounts/blobServices | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. Storage/storageAccounts/blobServices | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. Storage/storageAccounts/blobServices | Tranzakciók | Tranzakciók | Mennyiség | Összes
Igen | Microsoft. Storage/storageAccounts/fileServices | Elérhetőség | Elérhetőség | Százalék | Átlag
Igen | Microsoft. Storage/storageAccounts/fileServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Nem | Microsoft. Storage/storageAccounts/fileServices | FileCapacity | Fájl kapacitása | Bájt | Átlag
Nem | Microsoft. Storage/storageAccounts/fileServices | FileCount | Fájlok száma | Mennyiség | Átlag
Nem | Microsoft. Storage/storageAccounts/fileServices | FileShareCount | Fájlmegosztás száma | Mennyiség | Átlag
Nem | Microsoft. Storage/storageAccounts/fileServices | FileShareQuota | Fájlmegosztás kvótájának mérete | Bájt | Átlag
Nem | Microsoft. Storage/storageAccounts/fileServices | FileShareSnapshotCount | Fájlmegosztás pillanatképének száma | Mennyiség | Átlag
Nem | Microsoft. Storage/storageAccounts/fileServices | FileShareSnapshotSize | Fájlmegosztás pillanatképének mérete | Bájt | Átlag
Igen | Microsoft. Storage/storageAccounts/fileServices | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. Storage/storageAccounts/fileServices | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. Storage/storageAccounts/fileServices | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. Storage/storageAccounts/fileServices | Tranzakciók | Tranzakciók | Mennyiség | Összes
Igen | Microsoft. Storage/storageAccounts/queueServices | Elérhetőség | Elérhetőség | Százalék | Átlag
Igen | Microsoft. Storage/storageAccounts/queueServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Igen | Microsoft. Storage/storageAccounts/queueServices | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. Storage/storageAccounts/queueServices | QueueCapacity | Várólista kapacitása | Bájt | Átlag
Igen | Microsoft. Storage/storageAccounts/queueServices | QueueCount | Várólista száma | Mennyiség | Átlag
Igen | Microsoft. Storage/storageAccounts/queueServices | QueueMessageCount | Üzenetsor-üzenetek száma | Mennyiség | Átlag
Igen | Microsoft. Storage/storageAccounts/queueServices | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. Storage/storageAccounts/queueServices | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. Storage/storageAccounts/queueServices | Tranzakciók | Tranzakciók | Mennyiség | Összes
Igen | Microsoft. Storage/storageAccounts/tableServices | Elérhetőség | Elérhetőség | Százalék | Átlag
Igen | Microsoft. Storage/storageAccounts/tableServices | Kimenő forgalom | Kimenő forgalom | Bájt | Összes
Igen | Microsoft. Storage/storageAccounts/tableServices | Belépő | Belépő | Bájt | Összes
Igen | Microsoft. Storage/storageAccounts/tableServices | SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag
Igen | Microsoft. Storage/storageAccounts/tableServices | SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag
Igen | Microsoft. Storage/storageAccounts/tableServices | TableCapacity | Tábla kapacitása | Bájt | Átlag
Igen | Microsoft. Storage/storageAccounts/tableServices | TableCount | Táblák száma | Mennyiség | Átlag
Igen | Microsoft. Storage/storageAccounts/tableServices | TableEntityCount | Tábla entitások száma | Mennyiség | Átlag
Igen | Microsoft. Storage/storageAccounts/tableServices | Tranzakciók | Tranzakciók | Mennyiség | Összes
Igen | Microsoft. StorageCache/gyorsítótárak | ClientIOPS | Ügyfél teljes IOPS | Mennyiség | Átlag
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
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetHealth | Tárolási cél állapota | Mennyiség | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetIOPS | Összes StorageTarget-IOPS | Mennyiség | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetLatency | StorageTarget késés | Ezredmásodperc | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetMetadataReadIOPS | StorageTarget-metaadatok IOPS olvasása | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetMetadataWriteIOPS | StorageTarget-metaadatok írási IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetReadAheadThroughput | StorageTarget – olvasási sebesség | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetReadIOPS | StorageTarget olvasási IOPS | CountPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetSyncWriteThroughput | StorageTarget szinkron írási sebessége | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetTotalReadThroughput | StorageTarget összesen olvasási átviteli sebesség | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetTotalWriteThroughput | StorageTarget összes írási sebessége | BytesPerSecond | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | StorageTargetWriteIOPS | StorageTarget írási IOPS | Mennyiség | Átlag
Igen | Microsoft. StorageCache/gyorsítótárak | Hasznos üzemidő | Hasznos üzemidő | Mennyiség | Átlag
Igen | Microsoft. storagesync/storageSyncServices | ServerSyncSessionResult | Szinkronizálási munkamenet eredménye | Mennyiség | Átlag
Igen | Microsoft. storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | Szinkronizált bájtok száma | Bájt | Összes
Igen | Microsoft. storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | Felhőbeli rétegek felidézésének mérete alkalmazás szerint | Bájt | Összes
Igen | Microsoft. storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | Felhőbeli rétegek felidézésének mérete | Bájt | Összes
Igen | Microsoft. storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | Felhőbeli rétegek felidézése | Bájt | Összes
Igen | Microsoft. storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | Felhőbeli rétegek felidézésének átviteli sebessége | BytesPerSecond | Átlag
Igen | Microsoft. storagesync/storageSyncServices | StorageSyncServerHeartbeat | Kiszolgáló online állapota | Mennyiség | Maximum
Igen | Microsoft. storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | Szinkronizált fájlok | Mennyiség | Összes
Igen | Microsoft. storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | Nem szinkronizált fájlok | Mennyiség | Összes
Igen | Microsoft. storagesync/storageSyncServices/registeredServers | ServerHeartbeat | Kiszolgáló online állapota | Mennyiség | Maximum
Igen | Microsoft. storagesync/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | Felhőbeli rétegek felidézése | Bájt | Összes
Igen | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | Szinkronizált bájtok száma | Bájt | Összes
Igen | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | Szinkronizált fájlok | Mennyiség | Összes
Igen | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | Nem szinkronizált fájlok | Mennyiség | Összes
Igen | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | Szinkronizált bájtok száma | Bájt | Összes
Igen | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | Szinkronizált fájlok | Mennyiség | Összes
Igen | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | Nem szinkronizált fájlok | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | Sikertelen függvénykérések | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | AMLCalloutInputEvents | Függvényesemények | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | AMLCalloutRequests | Függvénykérések | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | ConversionErrors | Adatátalakítási hibák | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | DeserializationError | Bemenet-deszerializálási hibák | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | Megrendelési események | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | EarlyInputEvents | Korai bemeneti események | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | Hibák | Futásidejű hibák | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | InputEventBytes | Bemeneti eseménybájtok | Bájt | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | InputEvents | Bemeneti események | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | Várakozó bemeneti események | Mennyiség | Maximum
Igen | Microsoft. StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | Fogadott bemeneti források | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | LateInputEvents | Késői bemeneti események | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | OutputEvents | Kimeneti események | Mennyiség | Összes
Igen | Microsoft. StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | Vízjel késleltetése | másodperc | Maximum
Igen | Microsoft. StreamAnalytics/streamingjobs | ResourceUtilization | SU% kihasználtsága | Százalék | Maximum
Igen | Microsoft. VMwareCloudSimple/virtualMachines | Lemez olvasási bájtjai | Lemez olvasási bájtjai | Bájt | Összes
Igen | Microsoft. VMwareCloudSimple/virtualMachines | Lemez olvasási művelete/mp | Lemez olvasási művelete/mp | CountPerSecond | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | Lemez írási bájtjai | Lemez írási bájtjai | Bájt | Összes
Igen | Microsoft. VMwareCloudSimple/virtualMachines | Lemez írási műveletei/mp | Lemez írási műveletei/mp | CountPerSecond | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Lemez olvasási sebessége (bájt/s) | BytesPerSecond | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadLatency | Lemez olvasási késése | Ezredmásodperc | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadOperations | Lemezes olvasási műveletek | Mennyiség | Összes
Igen | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Lemez írási sebessége (bájt/s) | BytesPerSecond | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteLatency | Lemez írási késése | Ezredmásodperc | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteOperations | Lemez írási műveletei | Mennyiség | Összes
Igen | Microsoft. VMwareCloudSimple/virtualMachines | MemoryActive | Memória aktív | Bájt | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | MemoryGranted | Megadott memória | Bájt | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | MemoryUsed | Felhasznált memória | Bájt | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | Bejövő hálózat | Bejövő hálózat | Bájt | Összes
Igen | Microsoft. VMwareCloudSimple/virtualMachines | Kimenő hálózat | Kimenő hálózat | Bájt | Összes
Igen | Microsoft. VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | Hálózat bájt/mp-ben | BytesPerSecond | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | Hálózati kimenő bájtok/s | BytesPerSecond | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag
Igen | Microsoft. VMwareCloudSimple/virtualMachines | PercentageCpuReady | CPU-készültség százalékos aránya | Ezredmásodperc | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | ActiveRequests | Aktív kérések | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | AverageResponseTime | Átlagos válaszidő | másodperc | Átlag
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | BytesReceived | Bejövő adatforgalom | Bájt | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | BytesSent | Kimenő adatforgalom | Bájt | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | CpuPercentage | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | DiskQueueLength | Lemezvezérlő-várólista hossza | Mennyiség | Átlag
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http2xx | Http-2xx | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http3xx | Http-3xx | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http401 | HTTP 401 | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http403 | Http 403 | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http404 | Http 404 | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http4xx | Http-4xx | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Http5xx | Http-kiszolgálói hibák | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | HttpQueueLength | Http-várólista hossza | Mennyiség | Átlag
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | Nagy App Service-csomag feldolgozói | Mennyiség | Átlag
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | Közepes App Service-csomag feldolgozói | Mennyiség | Átlag
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | MemoryPercentage | Memória százaléka | Százalék | Átlag
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | Kérelmek | Kérelmek | Mennyiség | Összes
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | Kisméretű App Service-csomag feldolgozói | Mennyiség | Átlag
Igen | Microsoft. Web/hostingEnvironments/multiRolePools | TotalFrontEnds | Összes előtér | Mennyiség | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | CpuPercentage | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | MemoryPercentage | Memória százaléka | Százalék | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | WorkersAvailable | Rendelkezésre álló munkavégzők | Mennyiség | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | WorkersTotal | Alkalmazottak összesen | Mennyiség | Átlag
Igen | Microsoft. Web/hostingEnvironments/workerPools | WorkersUsed | Használt feldolgozók | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | BytesReceived | Bejövő adatforgalom | Bájt | Összes
Igen | Microsoft.Web/serverfarms | BytesSent | Kimenő adatforgalom | Bájt | Összes
Igen | Microsoft.Web/serverfarms | CpuPercentage | Processzorhasználat (%) | Százalék | Átlag
Igen | Microsoft.Web/serverfarms | DiskQueueLength | Lemezvezérlő-várólista hossza | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | HttpQueueLength | Http-várólista hossza | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | MemoryPercentage | Memória százaléka | Százalék | Átlag
Igen | Microsoft.Web/serverfarms | TcpCloseWait | TCP-bezárási várakozás | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | TcpClosing | TCP-zárás | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | TcpEstablished | A TCP létrejött | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | TcpFinWait1 | TCP fin WAIT 1 | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | TcpFinWait2 | TCP fin WAIT 2 | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | TcpLastAck | TCP Last ACK | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | TcpSynReceived | TCP SYN kapott | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | TcpSynSent | Eljuttatott TCP SYN | Mennyiség | Átlag
Igen | Microsoft.Web/serverfarms | TcpTimeWait | TCP-idő várakozása | Mennyiség | Átlag
Igen | Microsoft.Web/sites | AppConnections | Connections (Kapcsolatok) | Mennyiség | Átlag
Igen | Microsoft.Web/sites | AverageMemoryWorkingSet | Memória átlagos munkakészlete | Bájt | Átlag
Igen | Microsoft.Web/sites | AverageResponseTime | Átlagos válaszidő | másodperc | Átlag
Igen | Microsoft.Web/sites | BytesReceived | Bejövő adatforgalom | Bájt | Összes
Igen | Microsoft.Web/sites | BytesSent | Kimenő adatforgalom | Bájt | Összes
Igen | Microsoft.Web/sites | CpuTime | CPU-idő | másodperc | Összes
Igen | Microsoft.Web/sites | CurrentAssemblies | Aktuális szerelvények | Mennyiség | Átlag
Igen | Microsoft.Web/sites | FunctionExecutionCount | Függvény végrehajtásának száma | Mennyiség | Összes
Igen | Microsoft.Web/sites | FunctionExecutionUnits | Függvények végrehajtási egységei | Mennyiség | Összes
Igen | Microsoft.Web/sites | Gen0Collections | 0\. generációs Garbage-gyűjtemények | Mennyiség | Összes
Igen | Microsoft.Web/sites | Gen1Collections | 1\. generációs Garbage gyűjtemények | Mennyiség | Összes
Igen | Microsoft.Web/sites | Gen2Collections | 2\. generációs Garbage gyűjtemények | Mennyiség | Összes
Igen | Microsoft.Web/sites | Kezeli | Leírók száma | Mennyiség | Átlag
Igen | Microsoft.Web/sites | HealthCheckStatus | Állapot-ellenőrzési állapot | Mennyiség | Átlag
Igen | Microsoft.Web/sites | Http101 | Http 101 | Mennyiség | Összes
Igen | Microsoft.Web/sites | Http2xx | Http-2xx | Mennyiség | Összes
Igen | Microsoft.Web/sites | Http3xx | Http-3xx | Mennyiség | Összes
Igen | Microsoft.Web/sites | Http401 | HTTP 401 | Mennyiség | Összes
Igen | Microsoft.Web/sites | Http403 | Http 403 | Mennyiség | Összes
Igen | Microsoft.Web/sites | Http404 | Http 404 | Mennyiség | Összes
Igen | Microsoft.Web/sites | Http406 | Http 406 | Mennyiség | Összes
Igen | Microsoft.Web/sites | Http4xx | Http-4xx | Mennyiség | Összes
Igen | Microsoft.Web/sites | Http5xx | Http-kiszolgálói hibák | Mennyiség | Összes
Igen | Microsoft.Web/sites | HttpResponseTime | Válaszidő | másodperc | Átlag
Igen | Microsoft.Web/sites | IoOtherBytesPerSecond | IO – egyéb bájtok másodpercenként | BytesPerSecond | Összes
Igen | Microsoft.Web/sites | IoOtherOperationsPerSecond | IO egyéb műveletek másodpercenként | BytesPerSecond | Összes
Igen | Microsoft.Web/sites | IoReadBytesPerSecond | IO olvasási bájtok másodpercenként | BytesPerSecond | Összes
Igen | Microsoft.Web/sites | IoReadOperationsPerSecond | I/o-olvasási műveletek másodpercenként | BytesPerSecond | Összes
Igen | Microsoft.Web/sites | IoWriteBytesPerSecond | IO írási bájtok másodpercenként | BytesPerSecond | Összes
Igen | Microsoft.Web/sites | IoWriteOperationsPerSecond | IO írási műveletek másodpercenként | BytesPerSecond | Összes
Igen | Microsoft.Web/sites | MemoryWorkingSet | A memória-munkakészlet | Bájt | Átlag
Igen | Microsoft.Web/sites | PrivateBytes | Saját [nem megosztható] memória (bájt) | Bájt | Átlag
Igen | Microsoft.Web/sites | Kérelmek | Kérelmek | Mennyiség | Összes
Igen | Microsoft.Web/sites | RequestsInApplicationQueue | Kérelmek az alkalmazás-várólistán | Mennyiség | Átlag
Igen | Microsoft.Web/sites | Témák | Szálak száma | Mennyiség | Átlag
Igen | Microsoft.Web/sites | TotalAppDomains | Alkalmazás összes tartománya | Mennyiség | Átlag
Igen | Microsoft.Web/sites | TotalAppDomainsUnloaded | Összes kitöltött alkalmazás-tartomány | Mennyiség | Átlag
Igen | Microsoft. Web/Sites/Slots | AppConnections | Connections (Kapcsolatok) | Mennyiség | Átlag
Igen | Microsoft. Web/Sites/Slots | AverageMemoryWorkingSet | Memória átlagos munkakészlete | Bájt | Átlag
Igen | Microsoft. Web/Sites/Slots | AverageResponseTime | Átlagos válaszidő | másodperc | Átlag
Igen | Microsoft. Web/Sites/Slots | BytesReceived | Bejövő adatforgalom | Bájt | Összes
Igen | Microsoft. Web/Sites/Slots | BytesSent | Kimenő adatforgalom | Bájt | Összes
Igen | Microsoft. Web/Sites/Slots | CpuTime | CPU-idő | másodperc | Összes
Igen | Microsoft. Web/Sites/Slots | CurrentAssemblies | Aktuális szerelvények | Mennyiség | Átlag
Igen | Microsoft. Web/Sites/Slots | FunctionExecutionCount | Függvény végrehajtásának száma | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | FunctionExecutionUnits | Függvények végrehajtási egységei | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Gen0Collections | 0\. generációs Garbage-gyűjtemények | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Gen1Collections | 1\. generációs Garbage gyűjtemények | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Gen2Collections | 2\. generációs Garbage gyűjtemények | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Kezeli | Leírók száma | Mennyiség | Átlag
Igen | Microsoft. Web/Sites/Slots | HealthCheckStatus | Állapot-ellenőrzési állapot | Mennyiség | Átlag
Igen | Microsoft. Web/Sites/Slots | Http101 | Http 101 | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Http2xx | Http-2xx | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Http3xx | Http-3xx | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Http401 | HTTP 401 | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Http403 | Http 403 | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Http404 | Http 404 | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Http406 | Http 406 | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Http4xx | Http-4xx | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | Http5xx | Http-kiszolgálói hibák | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | HttpResponseTime | Válaszidő | másodperc | Átlag
Igen | Microsoft. Web/Sites/Slots | IoOtherBytesPerSecond | IO – egyéb bájtok másodpercenként | BytesPerSecond | Összes
Igen | Microsoft. Web/Sites/Slots | IoOtherOperationsPerSecond | IO egyéb műveletek másodpercenként | BytesPerSecond | Összes
Igen | Microsoft. Web/Sites/Slots | IoReadBytesPerSecond | IO olvasási bájtok másodpercenként | BytesPerSecond | Összes
Igen | Microsoft. Web/Sites/Slots | IoReadOperationsPerSecond | I/o-olvasási műveletek másodpercenként | BytesPerSecond | Összes
Igen | Microsoft. Web/Sites/Slots | IoWriteBytesPerSecond | IO írási bájtok másodpercenként | BytesPerSecond | Összes
Igen | Microsoft. Web/Sites/Slots | IoWriteOperationsPerSecond | IO írási műveletek másodpercenként | BytesPerSecond | Összes
Igen | Microsoft. Web/Sites/Slots | MemoryWorkingSet | A memória-munkakészlet | Bájt | Átlag
Igen | Microsoft. Web/Sites/Slots | PrivateBytes | Saját [nem megosztható] memória (bájt) | Bájt | Átlag
Igen | Microsoft. Web/Sites/Slots | Kérelmek | Kérelmek | Mennyiség | Összes
Igen | Microsoft. Web/Sites/Slots | RequestsInApplicationQueue | Kérelmek az alkalmazás-várólistán | Mennyiség | Átlag
Igen | Microsoft. Web/Sites/Slots | Témák | Szálak száma | Mennyiség | Átlag
Igen | Microsoft. Web/Sites/Slots | TotalAppDomains | Alkalmazás összes tartománya | Mennyiség | Átlag
Igen | Microsoft. Web/Sites/Slots | TotalAppDomainsUnloaded | Összes kitöltött alkalmazás-tartomány | Mennyiség | Átlag
