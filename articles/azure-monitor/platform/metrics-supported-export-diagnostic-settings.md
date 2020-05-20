---
title: Azure Monitor a platform metrikái exportálható diagnosztikai beállítások használatával
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 91fc2c4525ee622064520b0098087d54158bbe9e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680689"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor a platform metrikái exportálható diagnosztikai beállítások használatával

A Azure Monitor alapértelmezés szerint a [platform metrikáit](data-platform-metrics.md) biztosítja konfiguráció nélkül. Számos lehetőséget kínál a platform metrikáinak interakcióra, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. Lásd: [mérőszámok – támogatott](metrics-supported.md) a Azure monitor konszolidált metrikus folyamatával jelenleg elérhető platform-metrikák teljes listájához. A metrikák lekérdezéséhez és eléréséhez használja az [2018-01-01 API-Version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők.

Az Azure monitor folyamatának platform metrikáit kétféleképpen exportálhatja más helyekre.
1. [Diagnosztikai beállítások](diagnostic-settings.md) használata Log Analytics, Event Hubs vagy Azure Storage-ba történő küldéshez.
2. A [metrikák REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list) használata

A Azure Monitor-háttér bonyolult jellemzői miatt nem minden metrika exportálható a diagnosztikai beállítások használatával. Az alábbi táblázat felsorolja, hogy mely és nem exportálható a diagnosztikai beállítások használatával.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Váltás a NULLák és a nulla értékek viselkedésére 
 
A diagnosztikai beállítások használatával exportálható platform-metrikák esetében van néhány mérőszám, amelynek Azure Monitor a "0" értéket "NULL" értékre értelmezi. Ez némi zavart okozott a Real "0s" (erőforrás által kibocsátott) és az "0s" (nulls) értelmezése között. Hamarosan változik a változás, és a diagnosztikai beállításokon keresztül exportált platform-metrikák többé nem exportálják a "0s"-et, kivéve, ha az alapul szolgáló erőforrás valóban kiadta azokat. 

> [!CAUTION]
> A fent ismertetett viselkedés változása a 2020. június 1-jén történik.

Ne feledje:

1.  Ha töröl egy erőforráscsoportot vagy egy adott erőforrást, a rendszer nem küldi el az érintett erőforrásokból származó metrikai adatokat a diagnosztikai beállítások exportálási célhelyére. Ez azt eredményezi, hogy többé nem fog megjelenni a Event Hubs, a Storage-fiókokban és a Log Analytics-munkaterületeken.
2.  Ez a fejlesztés minden nyilvános és privát felhőben elérhető lesz.
3.  Ez a változás nem befolyásolja a következő tapasztalatok valamelyikének viselkedését: 
   - A platform erőforrás-naplói a diagnosztikai beállítások használatával lettek exportálva
   - Metrikák ábrázolási Metrikaböngésző
   - A platform metrikáinak riasztása
 
## <a name="metrics-exportable-table"></a>Metrika exportálható táblázat 

A tábla a következő oszlopokat tartalmazza. 
- Exportálható diagnosztikai beállításokkal? 
- NULL/0 eredményezte 
- ResourceType 
- Metric 
- MetricDisplayName
- Unit (Egység) 
- AggregationType


> [!NOTE]
> Az alábbi táblázat a lenti vízszintes görgetősávot tartalmazhatja. Ha úgy véli, hogy hiányzik az információ, ellenőrizze, hogy a görgetősáv a bal oldalon van-e.  


| Exportálható diagnosztikai beállításokkal?  | Már NULLák is vannak kibocsátva |  ResourceType  |  Metric  |  MetricDisplayName  |  Unit (Egység)  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  CleanerCurrentPrice  |  Memória: tisztító – aktuális ár  |  Darabszám  |  Átlag | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  CleanerMemoryNonshrinkable  |  Memória: nem csökkenthető a tisztább memória  |  Bájt  |  Átlag | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  CleanerMemoryShrinkable  |  Memória: tisztító memória csökkenthető  |  Bájt  |  Átlag | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  CommandPoolBusyThreads  |  Szálak: a parancssori készlet foglalt szálak  |  Darabszám  |  Átlag | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  CommandPoolIdleThreads  |  Szálak: parancssori készlet üresjárati szálai  |  Darabszám  |  Átlag | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  CommandPoolJobQueueLength  |  Parancssori feladatok várólistájának hossza  |  Darabszám  |  Átlag | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  Összege  |  Kapcsolat: aktuális kapcsolatok  |  Darabszám  |  Átlag | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  CurrentUserSessions  |  Aktuális felhasználói munkamenetek  |  Darabszám  |  Átlag | 
| Igen * * * *  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  LongParsingBusyThreads  |  Szálak: hosszú elemzés – foglalt szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  LongParsingIdleThreads  |  Szálak: tartós folyamatok elemzése – üresjárati szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  LongParsingJobQueueLength  |  Szálak: hosszú elemzési feladatok várólistájának hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  mashup_engine_memory_metric  |  M motor memóriája  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  mashup_engine_private_bytes_metric  |  M motor saját bájtjai  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  mashup_engine_qpu_metric  |  M motor QPU  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  mashup_engine_virtual_bytes_metric  |  M motor virtuális bájtjai  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  memory_metric  |  Memory (Memória)  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  memory_thrashing_metric  |  Memóriaakadozás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  MemoryLimitHard  |  Memória: rögzített memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  MemoryLimitHigh  |  Memória: magas a memória korlátozása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  MemoryLimitLow  |  Memória: kevés a memória korlátja  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  MemoryLimitVertiPaq  |  Memória: VertiPaq korlátja  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  MemoryUsage  |  Memória: memóriahasználat  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  private_bytes_metric  |  Saját bájtok  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ProcessingPoolBusyIOJobThreads  |  Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ProcessingPoolBusyNonIOThreads  |  Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ProcessingPoolIdleIOJobThreads  |  Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ProcessingPoolIdleNonIOThreads  |  Szálak: feldolgozási készlet üresjáratban nem I/O-szálai  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ProcessingPoolIOJobQueueLength  |  Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ProcessingPoolJobQueueLength  |  Feldolgozási készlet nyomtatási várólistájának hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  qpu_metric  |  QPU  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  QueryPoolBusyThreads  |  Lekérdezési készlet foglalt szálai  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  QueryPoolIdleThreads  |  Szálak: lekérdezési készlet – üresjárati szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  QueryPoolJobQueueLength  |  Szálak: lekérdezési készlet feladatok várólistájának hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  Kvóta  |  Memória: kvóta  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  QuotaBlocked  |  Memória: blokkolt kvóta  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  RowsConvertedPerSec  |  Feldolgozás: másodpercenként konvertált sorok száma  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  RowsReadPerSec  |  Feldolgozás: másodpercenként beolvasott sorok száma  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  RowsWrittenPerSec  |  Feldolgozás: másodpercenként írt sorok száma  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ShortParsingBusyThreads  |  Szálak: rövid elemzés – foglalt szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ShortParsingIdleThreads  |  Szálak: rövid elemzési üresjárati szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  ShortParsingJobQueueLength  |  Szálak: rövid elemzési feladatok várólistájának hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  SuccessfullConnectionsPerSec  |  Sikeres kapcsolatok másodpercenként  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  TotalConnectionFailures  |  Összes sikertelen Kapcsolatfelvétel  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  TotalConnectionRequests  |  Kapcsolatkérelmek teljes száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  VertiPaqNonpaged  |  Memória: VertiPaq, nem lapozható  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  VertiPaqPaged  |  Memória: VertiPaq lapozható  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AnalysisServices/kiszolgálók  |  virtual_bytes_metric  |  Felhasznált virtuális memória jelenlegi mérete (bájt)  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  BackendDuration  |  A háttérbeli kérelmek időtartama  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ApiManagement/service  |  Kapacitás  |  Kapacitás  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Időtartam  |  Az átjárók kéréseinek teljes időtartama  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Eldobott EventHub események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  EventHub-események visszautasítva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Sikeres EventHub események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Szabályozott EventHub események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Időtúllépés a EventHub eseményeinél  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  EventHub-események mérete  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Összes EventHub esemény  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Sikertelen EventHub események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Sikertelen átjáró-kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Egyéb átjáró-kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Sikeres átjáró-kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Összes átjáró kérelme (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Nem engedélyezett átjáró-kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  AppCpuUsagePercentage  |  Alkalmazás CPU-kihasználtságának százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  AppMemoryCommitted  |  Az alkalmazáshoz hozzárendelt memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  AppMemoryMax  |  Az alkalmazás memóriájának maximális száma  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  AppMemoryUsed  |  Használt alkalmazás memóriája  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  GCPauseTotalCount  |  GC-szüneteltetések száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  GCPauseTotalTime  |  GC felfüggesztésének teljes ideje  |  Ezredmásodpercben  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  MaxOldGenMemoryPoolBytes  |  A rendelkezésre álló régi generációs adatméret maximális száma  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  OldGenMemoryPoolBytes  |  A régi generációs adatméret  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  OldGenPromotedBytes  |  Előléptetés a régi generációs adatméretre  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  SystemCpuUsagePercentage  |  CPU-használat százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatErrorCount  |  Tomcat globális hiba  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatReceivedBytes  |  Tomcat összesen fogadott bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatRequestMaxTime  |  Tomcat-kérelem maximális ideje  |  Ezredmásodpercben  |  Maximum | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatRequestTotalCount  |  Tomcat-kérelem összesített száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatRequestTotalTime  |  Tomcat-kérelem teljes ideje  |  Ezredmásodpercben  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatResponseAvgTime  |  Tomcat-kérelem átlagos ideje  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatSentBytes  |  Tomcat összesen eljuttatott bájtok száma  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatSessionActiveCurrentCount  |  Tomcat-munkamenet élő száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatSessionActiveMaxCount  |  Tomcat-munkamenet maximális aktív száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatSessionAliveMaxTime  |  Tomcat-munkamenet maximális élettartama  |  Ezredmásodpercben  |  Maximum | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatSessionCreatedCount  |  Tomcat-munkamenet létrehozva szám  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatSessionExpiredCount  |  A Tomcat-munkamenet lejárt  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  TomcatSessionRejectedCount  |  A Tomcat-munkamenet elutasította a darabszámot  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. AppPlatform/Spring  |  YoungGenPromotedBytes  |  Népszerűsítse a fiatal generáció adatméretét  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Automation/automationAccounts  |  TotalJob  |  Feladatok összesen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Összes frissítés központi telepítési gép futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Összes frissítés központi telepítési futtatása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  CoreCount  |  Dedikált mag száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  CreatingNodeCount  |  Csomópontok számának létrehozása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  IdleNodeCount  |  Tétlen csomópontok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  JobDeleteCompleteEvent  |  Feladatok törlése – befejezett események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  JobDeleteStartEvent  |  Feladatok törlésének indítási eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  JobDisableCompleteEvent  |  A feladatok letiltják a teljes eseményeket  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  JobDisableStartEvent  |  Feladatok letiltásának indítási eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  JobStartEvent  |  Feladatok indítási eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  JobTerminateCompleteEvent  |  A feladatokhoz tartozó befejezett események befejezése  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  JobTerminateStartEvent  |  Feladatokból indított események leállítása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  LeavingPoolNodeCount  |  A készlet-csomópontok számának elhagyása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority mag száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  OfflineNodeCount  |  Offline csomópontok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  PoolCreateEvent  |  Készlet-létrehozási események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Készlet törlése – befejezett események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  PoolDeleteStartEvent  |  Készlet törlése – indítási események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  PoolResizeCompleteEvent  |  Készlet átméretezése – befejezett események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  PoolResizeStartEvent  |  Készlet átméretezésének indítási eseményei  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  PreemptedNodeCount  |  Előzik-csomópontok száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  RebootingNodeCount  |  Csomópontok számának újraindítása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  ReimagingNodeCount  |  Csomópontok rendszerképének alaphelyzetbe állítása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  RunningNodeCount  |  Csomópontok száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  StartingNodeCount  |  Csomópontok számának indítása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  StartTaskFailedNodeCount  |  Indítási feladat sikertelen csomópontok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  TaskCompleteEvent  |  Feladat teljes eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  TaskFailEvent  |  Feladat sikertelen eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. batch/batchAccounts  |  TaskStartEvent  |  Tevékenységek indítási eseményei  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Alacsony prioritású csomópontok száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  TotalNodeCount  |  Dedikált csomópontok száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  UnusableNodeCount  |  Nem használható csomópontok száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Várakozás a feladat-csomópontok számának megkezdésére  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Aktív magok  |  Aktív magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Aktív csomópontok  |  Aktív csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Üresjárati magok  |  Üresjárati magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Tétlen csomópontok  |  Tétlen csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  A feladatok befejeződtek  |  A feladatok befejeződtek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Feladatok elküldve  |  Feladatok elküldve  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Magok kihagyása  |  Magok kihagyása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Csomópontok elhagyása  |  Csomópontok elhagyása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Előzik magok  |  Előzik magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Előzik-csomópontok  |  Előzik-csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Kvóta kihasználtsága (%)  |  Kvóta kihasználtsága (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Magok összesen  |  Magok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Csomópontok összesen  |  Csomópontok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Használhatatlan magok  |  Használhatatlan magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. BatchAI/munkaterületek  |  Használhatatlan csomópontok  |  Használhatatlan csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  ConnectionAccepted  |  Elfogadott kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  ConnectionActive  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  ConnectionHandled  |  Kezelt kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  CPU-használat százaléka  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  IOReadBytes  |  IO olvasási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  IOWriteBytes  |  IO írási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  MemoryLimit  |  Memória korlátja  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  MemoryUsage  |  Memóriahasználat  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Memóriahasználat százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  PendingTransactions  |  Függőben lévő tranzakciók  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  ProcessedBlocks  |  Feldolgozott blokkok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  ProcessedTransactions  |  Feldolgozott tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  QueuedTransactions  |  Várólistán lévő tranzakciók  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  RequestHandled  |  Kezelt kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Blockchain/blockchainMembers  |  StorageUsage  |  Tárterület-használat  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits  |  Gyorsítótár-találatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits0  |  Gyorsítótárbeli találatok (szegmens 0)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits1  |  Gyorsítótárbeli találatok (1. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits2  |  Gyorsítótárbeli találatok (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits3  |  Gyorsítótárbeli találatok (3. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits4  |  Gyorsítótár-találatok (4. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits5  |  Gyorsítótárbeli találatok (szegmens 5)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits6  |  Gyorsítótárbeli találatok (szilánk 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits7  |  Gyorsítótárbeli találatok (szegmens 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits8  |  Gyorsítótárbeli találatok (szilánk 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachehits9  |  Gyorsítótárbeli találatok (szilánk 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheLatency  |  Gyorsítótár késési másodpercek (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses  |  Gyorsítótár-tévesztések  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses0  |  Gyorsítótár-kihagyás (0. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses1  |  Gyorsítótár-lemaradás (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses2  |  Gyorsítótár-lemaradás (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses3  |  Gyorsítótár-lemaradás (3. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses4  |  Gyorsítótár-lemaradás (4. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses5  |  Gyorsítótár-lemaradás (5. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses6  |  Gyorsítótár-lemaradás (szegmens 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses7  |  Gyorsítótár-lemaradás (7. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses8  |  Gyorsítótár-lemaradás (szegmens 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cachemisses9  |  Gyorsítótár-lemaradás (szegmens 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead  |  Gyorsítótár-olvasás  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead0  |  Gyorsítótár-olvasás (0. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead1  |  Gyorsítótár olvasása (1. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead2  |  Gyorsítótár olvasása (2. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead3  |  Gyorsítótár olvasása (3. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead4  |  Gyorsítótár olvasása (4. Szilánk)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead5  |  Gyorsítótár olvasása (5. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead6  |  Gyorsítótár olvasása (szegmens 6)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead7  |  Gyorsítótár olvasása (szegmens 7)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead8  |  Gyorsítótár olvasása (szilánk 8)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheRead9  |  Gyorsítótár olvasása (szegmens 9)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite  |  Gyorsítótár-írás  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite0  |  Gyorsítótár-írás (0. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite1  |  Gyorsítótár-írás (1. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite2  |  Gyorsítótár-írás (2. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite3  |  Gyorsítótár-írás (3. Szilánk)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite4  |  Gyorsítótár-írás (4. Szilánk)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite5  |  Gyorsítótár-írás (5. Szilánk)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite6  |  Gyorsítótár írása (szegmens 6)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite7  |  Gyorsítótár-írás (szegmens 7)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite8  |  Gyorsítótár-írás (szegmens 8)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  cacheWrite9  |  Gyorsítótár-írás (szegmens 9)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients  |  Csatlakoztatott ügyfelek  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients0  |  Csatlakoztatott ügyfelek (0. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients1  |  Csatlakoztatott ügyfelek (1. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients2  |  Csatlakoztatott ügyfelek (2. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients3  |  Csatlakoztatott ügyfelek (3. Szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients4  |  Csatlakoztatott ügyfelek (4. Szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients5  |  Csatlakoztatott ügyfelek (5. Szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients6  |  Csatlakoztatott ügyfelek (6. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients7  |  Csatlakoztatott ügyfelek (7. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients8  |  Csatlakoztatott ügyfelek (10. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  connectedclients9  |  Csatlakoztatott ügyfelek (10. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  Hibák  |  Hibák  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys  |  Kizárt kulcsok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys0  |  Kizárt kulcsok (0. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys1  |  Kizárt kulcsok (1. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys2  |  Kizárt kulcsok (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys3  |  Kizárt kulcsok (3. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys4  |  Kizárt kulcsok (4. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys5  |  Kizárt kulcsok (5. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys6  |  Kizárt kulcsok (szegmens 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys7  |  Kizárt kulcsok (7. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys8  |  Kizárt kulcsok (szilánk 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  evictedkeys9  |  Kizárt kulcsok (szilánk 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys  |  Lejárt kulcsok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys0  |  Lejárt kulcsok (szilánk 0)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys1  |  Lejárt kulcsok (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys2  |  Lejárt kulcsok (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys3  |  Lejárt kulcsok (3. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys4  |  Lejárt kulcsok (4. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys5  |  Lejárt kulcsok (szilánk 5)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys6  |  Lejárt kulcsok (szilánk 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys7  |  Lejárt kulcsok (7. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys8  |  Lejárt kulcsok (szilánk 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  expiredkeys9  |  Lejárt kulcsok (szilánk 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands  |  Lekérések  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands0  |  Beolvasás (0. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands1  |  Beolvasás (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands2  |  Beolvasás (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands3  |  Beolvasás (3. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands4  |  Beolvasás (4. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands5  |  Beolvasás (szegmens 5)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands6  |  Beolvasás (szilánk 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands7  |  Beolvasás (szegmens 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands8  |  Beolvasás (szilánk 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  getcommands9  |  Beolvasás (szegmens 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond  |  Műveletek száma másodpercenként  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond0  |  Művelet/másodperc (0. Szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond1  |  Művelet/másodperc (1. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond2  |  Művelet/másodperc (2. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond3  |  Művelet/másodperc (3. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond4  |  Művelet/másodperc (4. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond5  |  Művelet/másodperc (5. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond6  |  Művelet/másodperc (szegmens 6)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond7  |  Művelet/másodperc (7. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond8  |  Művelet/másodperc (szegmens 8)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  operationsPerSecond9  |  Művelet/másodperc (szegmens 9)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime  |  CPU  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime0  |  CPU (0. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime1  |  CPU (1. Szilánk)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime2  |  CPU (2. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime3  |  CPU (3. Szilánk)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime4  |  CPU (4. Szilánk)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime5  |  CPU (5. Szilánk)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime6  |  CPU (szegmens 6)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime7  |  PROCESSZOR (7. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime8  |  CPU (szegmens 8)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  percentProcessorTime9  |  CPU (szilánk 9)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad  |  Kiszolgáló terhelése  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad0  |  Kiszolgáló terhelése (0. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad1  |  Kiszolgáló terhelése (1. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad2  |  Kiszolgáló betöltése (2. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad3  |  Kiszolgáló terhelése (3. Szilánk)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad4  |  Kiszolgáló terhelése (4. Szilánk)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad5  |  Kiszolgáló terhelése (5. Szilánk)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad6  |  Kiszolgáló betöltése (szegmens 6)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad7  |  Kiszolgáló terhelése (7. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad8  |  Kiszolgáló betöltése (szegmens 8)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  serverLoad9  |  Kiszolgáló terhelése (szegmens 9)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands  |  Készletek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands0  |  Készletek (0. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands1  |  Készletek (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands2  |  Készletek (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands3  |  Készletek (3. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands4  |  Készletek (4. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands5  |  Készletek (szilánk 5)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands6  |  Készletek (szilánk 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands7  |  Készletek (szegmens 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands8  |  Készletek (szilánk 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  setcommands9  |  Készletek (szilánk 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed  |  Műveletek összesen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed0  |  Összes művelet (0. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed1  |  Összes művelet (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed2  |  Összes művelet (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed3  |  Összes művelet (3. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed4  |  Összes művelet (4. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed5  |  Összes művelet (5. Szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed6  |  Összes művelet (szegmens 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed7  |  Összes művelet (7. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed8  |  Összes művelet (szegmens 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalcommandsprocessed9  |  Összes művelet (szegmens 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys  |  Kulcsok összesen  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys0  |  Összes kulcs (szilánk 0)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys1  |  Összes kulcs (1. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys2  |  Összes kulcs (2. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys3  |  Összes kulcs (3. Szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys4  |  Összes kulcs (4. Szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys5  |  Összes kulcs (5. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys6  |  Összes kulcs (szilánk 6)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys7  |  Összes kulcs (szegmens 7)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys8  |  Összes kulcs (szilánk 8)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  totalkeys9  |  Összes kulcs (szilánk 9)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory  |  Felhasznált memória  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory0  |  Felhasznált memória (0. Szilánk)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory1  |  Felhasznált memória (1. Szilánk)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory2  |  Felhasznált memória (2. Szilánk)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory3  |  Felhasznált memória (3. Szilánk)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory4  |  Felhasznált memória (4. Szilánk)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory5  |  Felhasznált memória (5. Szilánk)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory6  |  Felhasznált memória (6. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory7  |  Felhasznált memória (7. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory8  |  Felhasznált memória (8. Szilánk)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemory9  |  Felhasznált memória (10. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemorypercentage  |  Felhasznált memória százalékos aránya  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss  |  Felhasznált memória RSS-címe  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss0  |  Használt memória RSS-je (0. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss1  |  Használt memória RSS-je (1. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss2  |  Használt memória RSS-je (2. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss3  |  Használt memória RSS-je (3. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss4  |  Használt memória RSS-je (4. Szilánk)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss5  |  Használt memória RSS-je (5. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss6  |  Használt memória RSS-je (3. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss7  |  Használt memória RSS-je (7. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss8  |  Használt memória RSS-je (szegmens 8)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. cache/Redis  |  usedmemoryRss9  |  Használt memória RSS-je (szegmens 9)  |  Bájt  |  Maximum | 
| Nem  | Nem |  Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök  |  Lemez olvasási sebessége (bájt/s)  |  Lemez olvasása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök  |  Lemez olvasási művelete/mp  |  Lemez olvasási művelete/mp  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök  |  Lemez írási sebessége (bájt/s)  |  Lemez írása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök  |  Lemez írási műveletei/mp  |  Lemez írási műveletei/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök  |  Bejövő hálózat  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök  |  Kimenő hálózat  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicCompute/virtualMachines  |  Lemez olvasási sebessége (bájt/s)  |  Lemez olvasása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/virtualMachines  |  Lemez olvasási művelete/mp  |  Lemez olvasási művelete/mp  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicCompute/virtualMachines  |  Lemez írási sebessége (bájt/s)  |  Lemez írása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/virtualMachines  |  Lemez írási műveletei/mp  |  Lemez írási műveletei/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/virtualMachines  |  Bejövő hálózat  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/virtualMachines  |  Kimenő hálózat  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicCompute/virtualMachines  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts  |  UsedCapacity  |  Felhasznált kapacitás  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  BLOB kapacitása  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Blobok száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  BLOB-tárolók száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Index kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  Fájl kapacitása  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Fájlok száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Fájlmegosztás száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Fájlmegosztás kvótájának mérete  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Fájlmegosztás pillanatképének száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Fájlmegosztás pillanatképének mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Várólista kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Várólista száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Üzenetsor-üzenetek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Tábla kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Táblák száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Tábla entitások száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  BlockedCalls  |  Blokkolt hívások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  CharactersTrained  |  Betanított karakterek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  CharactersTranslated  |  Lefordított karakterek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  ClientErrors  |  Ügyfél-hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  DataIn  |  A-ben tárolt adatértékek  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  DataOut  |  Kimenő adatvesztés  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  Késés  |  Késés  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  Kiszolgálóhibái  |  Kiszolgálói hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  SpeechSessionDuration  |  Beszédfelismerési munkamenet időtartama  |  Másodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  SuccessfulCalls  |  Sikeres hívások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  TotalCalls  |  Hívások összesen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  TotalErrors  |  Összes hiba  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  TotalTokenCalls  |  Jogkivonat-hívások összesen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. CognitiveServices/fiókok  |  TotalTransactions  |  Tranzakciók összesen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Felhasznált CPU-kreditek  |  Felhasznált CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Fennmaradó CPU-kreditek  |  Fennmaradó CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez várakozási sorának mélysége  |  Adatlemez-várólista mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez-olvasási sebesség (bájt/s)  |  Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez olvasási műveletei (művelet/s)  |  Adatlemez olvasási művelete/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez-írási sebesség (bájt/s)  |  Adatlemez-írási sebesség (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez írási műveletei (művelet/s)  |  Adatlemez írási műveletei/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemez olvasási bájtjai  |  Lemez olvasási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemez olvasási művelete/mp  |  Lemez olvasási művelete/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemez írási bájtjai  |  Lemez írási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemez írási műveletei/mp  |  Lemez írási műveletei/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Bejövő forgalomfolyamok  |  Bejövő forgalomfolyamok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Bejövő folyamatok maximális létrehozási aránya  |  Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Bejövő hálózat  |  Számlázandó hálózat (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Teljes hálózat  |  Teljes hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Kimenő hálózat  |  Hálózati kimenő számlázandó (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Kimenő hálózat összesen  |  Kimenő hálózat összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  OS-lemez várakozási sorának mélysége  |  OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/s  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  OPERÁCIÓSRENDSZER-lemez írási műveletei/s  |  OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer/lemez QD  |  OPERÁCIÓSRENDSZER-lemez QD (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer/lemez olvasási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer/lemez olvasási művelet/mp  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer/lemez írási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer/lemez írási műveletei másodpercenként  |  OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Kimenő folyamatok  |  Kimenő folyamatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Kimenő folyamatok maximális létrehozási aránya  |  Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  /Lemez QD  |  Adatlemez QD (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  /Lemez olvasási sebesség (bájt/s)  |  Adatlemez-olvasási sebesség (bájt/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  /Lemez olvasási művelet/mp  |  Adatlemez olvasási műveletei (művelet/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  /Lemez írási sebessége (bájt/s)  |  Adatlemez-írási sebesség (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  /Lemez írási műveletek másodpercenként  |  Adatlemez írási műveletei (művelet/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Prémium szintű adatlemez-gyorsítótár – olvasási találat  |  Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás  |  Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási találata  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Felhasznált CPU-kreditek  |  Felhasznált CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Fennmaradó CPU-kreditek  |  Fennmaradó CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez várakozási sorának mélysége  |  Adatlemez-várólista mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez-olvasási sebesség (bájt/s)  |  Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez olvasási műveletei (művelet/s)  |  Adatlemez olvasási művelete/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez-írási sebesség (bájt/s)  |  Adatlemez-írási sebesség (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez írási műveletei (művelet/s)  |  Adatlemez írási műveletei/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemez olvasási bájtjai  |  Lemez olvasási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemez olvasási művelete/mp  |  Lemez olvasási művelete/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemez írási bájtjai  |  Lemez írási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemez írási műveletei/mp  |  Lemez írási műveletei/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Bejövő forgalomfolyamok  |  Bejövő forgalomfolyamok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Bejövő folyamatok maximális létrehozási aránya  |  Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Bejövő hálózat  |  Számlázandó hálózat (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Teljes hálózat  |  Teljes hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Kimenő hálózat  |  Hálózati kimenő számlázandó (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Kimenő hálózat összesen  |  Kimenő hálózat összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  OS-lemez várakozási sorának mélysége  |  OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/s  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  OPERÁCIÓSRENDSZER-lemez írási műveletei/s  |  OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer/lemez QD  |  OPERÁCIÓSRENDSZER-lemez QD (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer/lemez olvasási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer/lemez olvasási művelet/mp  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer/lemez írási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer/lemez írási műveletei másodpercenként  |  OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Kimenő folyamatok  |  Kimenő folyamatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Kimenő folyamatok maximális létrehozási aránya  |  Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  /Lemez QD  |  Adatlemez QD (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  /Lemez olvasási sebesség (bájt/s)  |  Adatlemez-olvasási sebesség (bájt/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  /Lemez olvasási művelet/mp  |  Adatlemez olvasási műveletei (művelet/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  /Lemez írási sebessége (bájt/s)  |  Adatlemez-írási sebesség (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  /Lemez írási műveletek másodpercenként  |  Adatlemez írási műveletei (művelet/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Prémium szintű adatlemez-gyorsítótár – olvasási találat  |  Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás  |  Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási találata  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Felhasznált CPU-kreditek  |  Felhasznált CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Fennmaradó CPU-kreditek  |  Fennmaradó CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Adatlemez várakozási sorának mélysége  |  Adatlemez-várólista mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Adatlemez-olvasási sebesség (bájt/s)  |  Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Adatlemez olvasási műveletei (művelet/s)  |  Adatlemez olvasási művelete/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Adatlemez-írási sebesség (bájt/s)  |  Adatlemez-írási sebesség (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Adatlemez írási műveletei (művelet/s)  |  Adatlemez írási műveletei/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Lemez olvasási bájtjai  |  Lemez olvasási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Lemez olvasási művelete/mp  |  Lemez olvasási művelete/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Lemez írási bájtjai  |  Lemez írási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Lemez írási műveletei/mp  |  Lemez írási műveletei/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Bejövő forgalomfolyamok  |  Bejövő forgalomfolyamok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Bejövő folyamatok maximális létrehozási aránya  |  Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Bejövő hálózat  |  Számlázandó hálózat (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Teljes hálózat  |  Teljes hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Kimenő hálózat  |  Hálózati kimenő számlázandó (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Kimenő hálózat összesen  |  Kimenő hálózat összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  OS-lemez várakozási sorának mélysége  |  OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/s  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  OPERÁCIÓSRENDSZER-lemez írási műveletei/s  |  OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer/lemez QD  |  OPERÁCIÓSRENDSZER-lemez QD (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer/lemez olvasási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer/lemez olvasási művelet/mp  |  OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer/lemez írási sebessége (bájt/s)  |  OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer/lemez írási műveletei másodpercenként  |  OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Kimenő folyamatok  |  Kimenő folyamatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Kimenő folyamatok maximális létrehozási aránya  |  Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  /Lemez QD  |  Adatlemez QD (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  /Lemez olvasási sebesség (bájt/s)  |  Adatlemez-olvasási sebesség (bájt/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  /Lemez olvasási művelet/mp  |  Adatlemez olvasási műveletei (művelet/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  /Lemez írási sebessége (bájt/s)  |  Adatlemez-írási sebesség (bájt/s) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  /Lemez írási műveletek másodpercenként  |  Adatlemez írási műveletei (művelet/mp) (elavult)  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Prémium szintű adatlemez-gyorsítótár – olvasási találat  |  Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás  |  Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási találata  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. számítás/virtualMachineScaleSets/virtualMachines  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása  |  Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ContainerInstance/containerGroups  |  CpuUsage  |  Processzorhasználat  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ContainerInstance/containerGroups  |  MemoryUsage  |  Memóriahasználat  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Másodpercenként fogadott hálózati bájtok száma  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Másodpercenként továbbított hálózati bájtok száma  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ContainerRegistry/nyilvántartók  |  RunDuration  |  Futtatás időtartama  |  Ezredmásodpercben  |  Összesen | 
| **Igen**  | Nem |  Microsoft. ContainerRegistry/nyilvántartók  |  SuccessfulPullCount  |  Sikeres lekérések száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ContainerRegistry/nyilvántartók  |  SuccessfulPushCount  |  Sikeres leküldések száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ContainerRegistry/nyilvántartók  |  TotalPullCount  |  Lekérések száma összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. ContainerRegistry/nyilvántartók  |  TotalPushCount  |  Leküldések száma összesen  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Tárolószolgáltatás/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Tárolószolgáltatás/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  A felügyelt fürtben rendelkezésre álló memória teljes mennyisége  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. Tárolószolgáltatás/managedClusters  |  kube_node_status_condition  |  Különböző csomóponti feltételek állapota  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Tárolószolgáltatás/managedClusters  |  kube_pod_status_phase  |  Hüvelyek száma fázis szerint  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Tárolószolgáltatás/managedClusters  |  kube_pod_status_ready  |  A hüvelyek száma üzemkész állapotban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  Availablecapacity;)  |  Rendelkezésre álló kapacitás  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Feltöltött Felhőbeli bájtok (eszköz)  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Felhőbeli feltöltött bájtok (megosztás)  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Felhőbeli letöltési sebesség  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Felhőbeli letöltési teljesítmény (megosztás)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Felhőbeli feltöltési sebesség  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Felhőbeli feltöltési sebesség (megosztás)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Edge-számítás – memóriahasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Edge-számítás – százalékos CPU  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Olvasási sebesség (hálózat)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Írási átviteli sebesség (hálózat)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  TotalCapacity  |  Teljes kapacitás  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataFactory/datafactories  |  FailedRuns  |  Sikertelen futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/datafactories  |  SuccessfulRuns  |  Sikeres futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  ActivityCancelledRuns  |  A megszakított tevékenység metrikákat futtat  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  ActivityFailedRuns  |  Sikertelen tevékenység-futtatási metrikák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  ActivitySucceededRuns  |  A sikeres tevékenység metrikákat futtat  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  FactorySizeInGbUnits  |  Gyári méret összesen (GB egység)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  IntegrationRuntimeAvailableMemory  |  Az Integration Runtime rendelkezésre álló memóriája  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  IntegrationRuntimeAverageTaskPickupDelay  |  Integration Runtime-várólista időtartama  |  Másodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  IntegrationRuntimeCpuPercentage  |  Integration Runtime CPU-kihasználtsága  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  IntegrationRuntimeQueueLength  |  Integration Runtime-várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  MaxAllowedFactorySizeInGbUnits  |  Maximálisan engedélyezett gyári méret (GB egység)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  MaxAllowedResourceCount  |  Engedélyezett entitások maximális száma  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  PipelineCancelledRuns  |  Megszakított folyamat-futtatási metrikák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  PipelineFailedRuns  |  Sikertelen folyamat-futtatási metrikák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  PipelineSucceededRuns  |  A folyamat sikeresen futtatja a metrikákat  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  ResourceCount  |  Entitások száma összesen  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  TriggerCancelledRuns  |  Megszakított trigger-futtatási metrikák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  TriggerFailedRuns  |  Sikertelen trigger-futtatási metrikák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataFactory/gyárak  |  TriggerSucceededRuns  |  A sikeres trigger metrikákat futtat  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataLakeAnalytics/fiókok  |  JobAUEndedCancelled  |  Megszakított AU-idő  |  Másodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataLakeAnalytics/fiókok  |  JobAUEndedFailure  |  Sikertelen AU-idő  |  Másodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataLakeAnalytics/fiókok  |  JobAUEndedSuccess  |  Sikeres AU-idő  |  Másodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataLakeAnalytics/fiókok  |  JobEndedCancelled  |  Megszakított feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataLakeAnalytics/fiókok  |  JobEndedFailure  |  Sikertelen feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DataLakeAnalytics/fiókok  |  JobEndedSuccess  |  Sikeres feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Data Lake Store/fiókok  |  DataRead  |  Olvasott információk  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Data Lake Store/fiókok  |  DataWritten  |  Írt adatértékek  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Data Lake Store/fiókok  |  ReadRequests  |  Olvasási kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Data Lake Store/fiókok  |  TotalStorage  |  Összes tárhely  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Data Lake Store/fiókok  |  WriteRequests  |  Írási kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  active_connections  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  backup_storage_used  |  Felhasznált biztonsági mentési tár  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  connections_failed  |  Sikertelen kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  cpu_percent  |  CPU-százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  io_consumption_percent  |  IO-százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  memory_percent  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  network_bytes_egress  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  network_bytes_ingress  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  seconds_behind_master  |  Replikálás késése másodpercben  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  serverlog_storage_limit  |  Kiszolgáló naplójának tárolási korlátja  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  serverlog_storage_percent  |  Kiszolgáló naplójának tárolási százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  serverlog_storage_usage  |  Kiszolgáló naplójának tárolója  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  storage_limit  |  Tárolási korlát  |  Bájt  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  storage_percent  |  Tárolási százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMariaDB/kiszolgálók  |  storage_used  |  Felhasznált tárterület  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  active_connections  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  backup_storage_used  |  Felhasznált biztonsági mentési tár  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  connections_failed  |  Sikertelen kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  cpu_percent  |  CPU-százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  io_consumption_percent  |  IO-százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  memory_percent  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  network_bytes_egress  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  network_bytes_ingress  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  seconds_behind_master  |  Replikálás késése másodpercben  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  serverlog_storage_limit  |  Kiszolgáló naplójának tárolási korlátja  |  Bájt  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  serverlog_storage_percent  |  Kiszolgáló naplójának tárolási százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  serverlog_storage_usage  |  Kiszolgáló naplójának tárolója  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  storage_limit  |  Tárolási korlát  |  Bájt  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  storage_percent  |  Tárolási százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DBforMySQL/kiszolgálók  |  storage_used  |  Felhasznált tárterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  active_connections  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  backup_storage_used  |  Felhasznált biztonsági mentési tár  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  connections_failed  |  Sikertelen kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  cpu_percent  |  CPU-százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  io_consumption_percent  |  IO-százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  memory_percent  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  network_bytes_egress  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  network_bytes_ingress  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  pg_replica_log_delay_in_bytes  |  Replikák maximális késése  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  pg_replica_log_delay_in_seconds  |  Replika késése  |  Másodperc  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  serverlog_storage_limit  |  Kiszolgáló naplójának tárolási korlátja  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  serverlog_storage_percent  |  Kiszolgáló naplójának tárolási százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  serverlog_storage_usage  |  Kiszolgáló naplójának tárolója  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  storage_limit  |  Tárolási korlát  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  storage_percent  |  Tárolási százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/kiszolgálók  |  storage_used  |  Felhasznált tárterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/serversv2  |  active_connections  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/serversv2  |  cpu_percent  |  CPU-százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/serversv2  |  IOPS  |  IOPS  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/serversv2  |  memory_percent  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/serversv2  |  storage_percent  |  Tárolási százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. DBforPostgreSQL/serversv2  |  storage_used  |  Felhasznált tárterület  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/Account  |  digitaltwins. telemetria. csomópontok  |  Digitális Twins-csomópont telemetria helyőrzője  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. commands. kimenő. elhagyása. sikeres  |  C2D üzenetek elhagyva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. commands. kimenő. Complete. success  |  C2D-üzenetek kézbesítésének befejezése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. commands. kimenő. elutasítás. sikeres  |  C2D-üzenetek elutasítva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Methods. failure  |  Sikertelen közvetlen metódusok meghívása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Methods. requestSize  |  A közvetlen metódus meghívásának mérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Methods. responseSize  |  A közvetlen metódus-meghívások válaszának mérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Methods. success  |  Közvetlen metódusok sikeres meghívása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Twin. Read. failure  |  Sikertelen dupla olvasások a háttérből  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Twin. Read. size  |  Dupla olvasások válaszának mérete a háttérből  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Twin. Read. success  |  Sikeres dupla olvasások a háttérből  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Twin. Update. failure  |  Sikertelen dupla frissítések a háttérből  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Twin. Update. size  |  Dupla frissítések mérete a háttérből  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2D. Twin. Update. success  |  Sikeres dupla frissítések a háttérből  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  C2DMessagesExpired  |  C2D-üzenetek lejárt (előzetes verzió)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  konfigurációk  |  Konfigurációs mérőszámok  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Devices/IotHubs  |  connectedDeviceCount  |  Csatlakoztatott eszközök (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. kimenő. beépített. események  |  Útválasztás: üzenetek/események küldésére küldött üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. kimenő. eventHubs  |  Útválasztás: az Event hub számára továbbított üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. kimenő. serviceBusQueues  |  Útválasztás: Service Bus üzenetsor számára továbbított üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. kimenő. serviceBusTopics  |  Útválasztás: Service Bus témakörbe küldött üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. kimenő. Storage  |  Útválasztás: a tárolóba küldött üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. kimenő. Storage. Blobok  |  Útválasztás: tárolóba szállított Blobok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. kimenő. Storage. Bytes  |  Útválasztás: a tárolóba szállított adatmennyiség  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. késleltetés. beépített. események  |  Útválasztás: üzenetek/események üzenetének késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. késleltetés. eventHubs  |  Útválasztás: az Event hub üzenetének késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. késleltetés. serviceBusQueues  |  Útválasztás: Service Bus üzenetsor üzenet-késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. késleltetés. serviceBusTopics  |  Útválasztás: Service Bus témakör üzenetének késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. látencia. Storage  |  Útválasztás: üzenetek késése a tároláshoz  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. telemetria. kimenő. eldobott  |  Útválasztás: telemetria üzenetek elvetve   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. telemetria. kimenő. tartalék  |  Útválasztás: tartalékként továbbított üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. telemetria. kimenő. érvénytelen  |  Útválasztás: Inkompatibilis telemetria-üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. telemetria. kimenő. árva  |  Útválasztás: árva telemetria üzenetek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. telemetria. kimenő. sikeres  |  Útválasztás: telemetria üzenetek kézbesítése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. telemetria. beáramló. allProtocol  |  Telemetria üzenetek küldése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. telemetria. beáramló. sendThrottle  |  Szabályozási hibák száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. telemetria. beáramló. sikeres  |  Telemetria üzenetek elküldése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. Twin. Read. failure  |  Sikertelen dupla olvasások az eszközökről  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. Twin. Read. size  |  Az eszközökről érkező kettős olvasások válaszának mérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. Twin. Read. success  |  Sikeres dupla olvasások az eszközökről  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. Twin. Update. failure  |  Sikertelen dupla frissítések az eszközökről  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. Twin. Update. size  |  Az eszközökből származó kettős frissítések mérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  D2C. Twin. Update. success  |  Sikeres dupla frissítések az eszközökről  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  dailyMessageQuotaUsed  |  A felhasznált üzenetek teljes száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  deviceDataUsage  |  Az eszköz összes adatfelhasználása  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  deviceDataUsageV2  |  Az eszköz összes adatfelhasználása (előzetes verzió)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  Devices. connectedDevices. allProtocol  |  Csatlakoztatott eszközök (elavult)   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  Devices. totalDevices  |  Összes eszköz (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  EventGridDeliveries  |  Event Grid kézbesítések (előzetes verzió)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  EventGridLatency  |  Event Grid késés (előzetes verzió)  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. cancelJob. hiba  |  Sikertelen feladatok megszakítása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. cancelJob. sikeres  |  Sikeres feladatok törlése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. kész  |  Befejezett feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. createDirectMethodJob. hiba  |  Nem sikerült létrehozni a metódus Meghívási feladatait  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. createDirectMethodJob. sikeres  |  Metódus-Meghívási feladatok sikeres létrehozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. createTwinUpdateJob. hiba  |  A kettős frissítési feladatok sikertelen létrehozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. createTwinUpdateJob. sikeres  |  A kettős frissítési feladatok sikeres létrehozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. sikertelen  |  Sikertelen feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. listJobs. hiba  |  Sikertelen hívások a feladatok listázásához  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. listJobs. sikeres  |  Sikeres hívások a feladatok listázásához  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. queryJobs. hiba  |  Sikertelen feladatok lekérdezése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  feladatok. queryJobs. sikeres  |  Sikeres feladatok lekérdezése  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Devices/IotHubs  |  totalDeviceCount  |  Összes eszköz (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  twinQueries. hiba  |  Sikertelen dupla lekérdezések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  twinQueries.resultSize  |  Dupla lekérdezések eredményének mérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Devices/IotHubs  |  twinQueries. success  |  Sikeres Twin-lekérdezések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/provisioningServices  |  AttestationAttempts  |  Igazolási kísérletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/provisioningServices  |  DeviceAssignments  |  Hozzárendelt eszközök  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Devices/provisioningServices  |  RegistrationAttempts  |  Regisztrációs kísérletek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  AvailableStorage  |  Rendelkezésre álló tárhely  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Cassandra-kapcsolatok bezárása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Cassandra-kérelmek díjai  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  CassandraRequests  |  Cassandra-kérelmek  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  DataUsage  |  Adathasználat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  DocumentCount  |  Dokumentumok száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  DocumentQuota  |  Dokumentum kvótája  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  IndexUsage  |  Indexhasználat  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  MetadataRequests  |  Metaadat-kérelmek  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Mongo-kérelem díja  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequests  |  Mongo kérelmek  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Mongo kérelmek gyakorisága  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Mongo-törlési kérelmek gyakorisága  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Mongo-beszúrási kérelmek gyakorisága  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Mongo-lekérdezési kérelmek gyakorisága  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Mongo-frissítési kérelmek gyakorisága  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Kiosztott átviteli sebesség  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  ReplicationLatency  |  P99 replikáció késése  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. DocumentDB/databaseAccounts  |  ServiceAvailability  |  Szolgáltatás rendelkezésre állása  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. DocumentDB/databaseAccounts  |  TotalRequests  |  Összes kérelem  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft. DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Kérelmek összes egysége  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EnterpriseKnowledgeGraph/szolgáltatások  |  FailureCount  |  Hibák száma  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft. EnterpriseKnowledgeGraph/szolgáltatások  |  SuccessCount  |  Sikeresek száma  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft. EnterpriseKnowledgeGraph/szolgáltatások  |  SuccessLatency  |  Sikeres késés  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. EnterpriseKnowledgeGraph/szolgáltatások  |  TransactionCount  |  Tranzakciók száma  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/tartományok  |  DeadLetteredCount  |  Kézbesítetlen levelek eseményei  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventGrid/tartományok  |  DeliveryAttemptFailCount  |  Sikertelen kézbesítések eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/tartományok  |  DeliverySuccessCount  |  Kézbesítési események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventGrid/tartományok  |  DestinationProcessingDurationInMs  |  Cél feldolgozási időtartama  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/tartományok  |  DroppedEventCount  |  Eldobott események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/tartományok  |  MatchedEventCount  |  Egyeztetett események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/tartományok  |  PublishFailCount  |  Sikertelen események közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/tartományok  |  PublishSuccessCount  |  Közzétett események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/tartományok  |  PublishSuccessLatencyInMs  |  Sikeres közzétételi késés  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Kézbesítetlen levelek eseményei  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Sikertelen kézbesítések eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Kézbesítési események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Cél feldolgozási időtartama  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/eventSubscriptions  |  DroppedEventCount  |  Eldobott események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/eventSubscriptions  |  MatchedEventCount  |  Egyeztetett események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/extensionTopics  |  PublishFailCount  |  Sikertelen események közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessCount  |  Közzétett események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Sikeres közzétételi késés  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/extensionTopics  |  UnmatchedEventCount  |  Páratlan események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/témakörök  |  PublishFailCount  |  Sikertelen események közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/témakörök  |  PublishSuccessCount  |  Közzétett események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/témakörök  |  PublishSuccessLatencyInMs  |  Sikeres közzétételi késés  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventGrid/témakörök  |  UnmatchedEventCount  |  Páratlan események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  Aktív kapcsolatai  |  Aktív kapcsolatai  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  AvailableMemory  |  Szabad memória  |  Százalék  |  Maximum | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  CaptureBacklog  |  Várakozó fájlok rögzítése.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  CapturedBytes  |  Rögzített bájtok száma.  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  CapturedMessages  |  Rögzített üzenetek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  ConnectionsClosed  |  A kapcsolatok lezárva.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  ConnectionsOpened  |  Megnyitott kapcsolatok.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  CPU  |  CPU  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. EventHub/fürtök  |  IncomingBytes  |  Bejövő bájtok.  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/fürtök  |  IncomingMessages  |  Bejövő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/fürtök  |  IncomingRequests  |  Bejövő kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/fürtök  |  OutgoingBytes  |  Kimenő bájtok.  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/fürtök  |  OutgoingMessages  |  Kimenő üzenetek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  QuotaExceededErrors  |  A kvóta túllépte a hibákat.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  Kiszolgálóhibái  |  Kiszolgálói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  SuccessfulRequests  |  Sikeres kérések  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  ThrottledRequests  |  Szabályozott kérelmek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/fürtök  |  UserErrors  |  Felhasználói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  Aktív kapcsolatai  |  Aktív kapcsolatai  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  CaptureBacklog  |  Várakozó fájlok rögzítése.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  CapturedBytes  |  Rögzített bájtok száma.  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  CapturedMessages  |  Rögzített üzenetek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  ConnectionsClosed  |  A kapcsolatok lezárva.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  ConnectionsOpened  |  Megnyitott kapcsolatok.  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHABL  |  Archivált várakozó üzenetek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHAMBS  |  Archivált üzenetek átviteli sebessége (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHAMSGS  |  Archivált üzenetek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHINBYTES  |  Bejövő bájtok (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHINMBS  |  Bejövő bájtok (elavult) (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHINMSGS  |  Bejövő üzenetek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHOUTBYTES  |  Kimenő bájtok (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHOUTMBS  |  Kimenő bájtok (elavult) (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  EHOUTMSGS  |  Kimenő üzenetek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  FAILREQ  |  Sikertelen kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  IncomingBytes  |  Bejövő bájtok.  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  IncomingMessages  |  Bejövő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  IncomingRequests  |  Bejövő kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  INMSGS  |  Bejövő üzenetek (elavult) (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  INREQS  |  Bejövő kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  INTERer  |  Belső kiszolgálói hibák (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  MISCERR  |  Egyéb hibák (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  OutgoingBytes  |  Kimenő bájtok.  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  OutgoingMessages  |  Kimenő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  OUTMSGS  |  Kimenő üzenetek (elavult) (elavult)  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  QuotaExceededErrors  |  A kvóta túllépte a hibákat.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  Kiszolgálóhibái  |  Kiszolgálói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  Méret  |  Méret  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  SuccessfulRequests  |  Sikeres kérések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  SUCCREQ  |  Sikeres kérések (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. EventHub/névterek  |  SVRBSY  |  Kiszolgáló által foglalt hibák (elavult)  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  ThrottledRequests  |  Szabályozott kérelmek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. EventHub/névterek  |  UserErrors  |  Felhasználói hibák.  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. HDInsight/fürtök  |  CategorizedGatewayRequests  |  Kategorizált átjáró-kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. HDInsight/fürtök  |  GatewayRequests  |  Átjáróra vonatkozó kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. HDInsight/fürtök  |  NumActiveWorkers  |  Aktív feldolgozók száma  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. HDInsight/fürtök  |  ScalingRequests  |  Kérelmek méretezése  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. bepillantások/AutoscaleSettings  |  MetricThreshold  |  Metrika küszöbértéke  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/AutoscaleSettings  |  ObservedCapacity  |  Megfigyelt kapacitás  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/AutoscaleSettings  |  ObservedMetricValue  |  Megfigyelt metrika értéke  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. bepillantások/AutoscaleSettings  |  ScaleActionsInitiated  |  Elindított skálázási műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  availabilityResults/availabilityPercentage  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. bepillantások/összetevők  |  availabilityResults/darabszám  |  Rendelkezésre állási tesztek  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  availabilityResults/időtartam  |  Rendelkezésre állási teszt időtartama  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  browserTimings/networkDuration  |  Oldal betöltési hálózati kapcsolati ideje  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  browserTimings/processingDuration  |  Ügyfél feldolgozási ideje  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  browserTimings/receiveDuration  |  Válaszidő fogadása  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  browserTimings/sendDuration  |  Kérelem küldési ideje  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  browserTimings/totalDuration  |  Böngésző oldalának betöltési ideje  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. bepillantások/összetevők  |  függőségek/darabszám  |  Függőségi hívások  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  függőségek/időtartam  |  Függőség időtartama  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. bepillantások/összetevők  |  függőségek/sikertelen  |  Függőségi hívások hibái  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft. bepillantások/összetevők  |  kivételek/böngésző  |  Böngészőkivételek  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft. bepillantások/összetevők  |  kivételek/darabszám  |  Kivételek  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft. bepillantások/összetevők  |  kivételek/kiszolgáló  |  Kiszolgálói kivételek  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft. bepillantások/összetevők  |  Oldalmegtekintések/darabszám  |  Lapok nézetei  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  Oldalmegtekintések/időtartam  |  Oldal nézet betöltési ideje  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. bepillantások/összetevők  |  performanceCounters/exceptionsPerSecond  |  Kivételek aránya  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  performanceCounters/memoryAvailableBytes  |  Igénybe vehető memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  performanceCounters/processCpuPercentage  |  Processzor feldolgozása  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  performanceCounters/processIOBytesPerSecond  |  Folyamat i/o-sebessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  performanceCounters/processorCpuPercentage  |  Processzoridő  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  performanceCounters/processPrivateBytes  |  Saját bájtok feldolgozása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  performanceCounters/requestExecutionTime  |  HTTP-kérelem végrehajtási ideje  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. bepillantások/összetevők  |  performanceCounters/requestsInQueue  |  HTTP-kérelmek az alkalmazás-várólistában  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. bepillantások/összetevők  |  performanceCounters/requestsPerSecond  |  HTTP-kérelmek gyakorisága  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. bepillantások/összetevők  |  kérelmek/darabszám  |  Kiszolgálói kérelmek  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft. bepillantások/összetevők  |  kérelmek/időtartam  |  Kiszolgáló válaszideje  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. bepillantások/összetevők  |  kérelmek/sikertelen  |  Sikertelen kérelmek  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft. bepillantások/összetevők  |  kérelmek/díjszabás  |  Kiszolgálói kérelmek gyakorisága  |  CountPerSecond  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. bepillantások/összetevők  |  nyomkövetés/darabszám  |  Hívásláncok  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft. kulcstartó/tárolók  |  ServiceApiHit  |  Összes szolgáltatási API-találat  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft. kulcstartó/tárolók  |  ServiceApiLatency  |  Általános szolgáltatás API-késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. kulcstartó/tárolók  |  ServiceApiResult  |  Összes szolgáltatás API-eredményei  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  CacheUtilization  |  Gyorsítótár kihasználtsága  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  ContinuousExportMaxLatenessMinutes  |  Folyamatos exportálás maximális késői percben  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  ContinuousExportNumOfRecordsExported  |  Folyamatos exportálás – számú exportált rekord  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  ContinuousExportPendingCount  |  Folyamatos exportálás függőben lévő darabszáma  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  ContinuousExportResult  |  Folyamatos exportálás eredménye  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  CPU  |  CPU  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  EventsProcessedForEventHubs  |  Feldolgozott események (Event/IoT hubok esetében)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  ExportUtilization  |  Exportálás kihasználtsága  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  IngestionLatencyInSeconds  |  Betöltési késleltetés (másodperc)  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  IngestionResult  |  Betöltés eredménye  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  IngestionUtilization  |  Betöltés kihasználtsága  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  IngestionVolumeInMB  |  Betöltési mennyiség (MB)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  KeepAlive  |  Életben tartása  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  QueryDuration  |  Lekérdezés időtartama  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  SteamingIngestRequestRate  |  Folyamatos átviteli kérelmek gyakorisága  |  Darabszám  |  RateRequestsPerSecond | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  StreamingIngestDataRate  |  Adatforgalom adatátviteli sebessége  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  StreamingIngestDuration  |  Folyamatos átvitel időtartama  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Kusto/fürtök  |  StreamingIngestResults  |  Folyamatos átvitel eredménye  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  ActionLatency  |  Művelet késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Befejezett műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsFailed  |  Sikertelen műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Kihagyott műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsStarted  |  Elindított műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Sikeres műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Művelet sikerességi késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Művelet által szabályozott események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Összekötő memóriahasználat integrációs szolgáltatási környezet  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  integrációs szolgáltatási környezet összekötő-processzorának használata  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  integrációs szolgáltatási környezet munkafolyamat-memóriahasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  A integrációs szolgáltatási környezet munkafolyamat-processzorának használata  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Sikertelen futtatások százalékos aránya  |  Százalék  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  RunLatency  |  Késés futtatása  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsCancelled  |  Megszakított futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsCompleted  |  Befejezett futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsFailed  |  Sikertelen futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsStarted  |  Elindított futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Sikeres futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Indítási leszabályozású események futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Sikeres Futtatás késése  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Szabályozott események futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Kiváltó tűz késése   |  Másodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerLatency  |  Trigger késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Befejezett eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersFailed  |  Sikertelen eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersFired  |  Kilőtt eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Kihagyott eseményindítók  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersStarted  |  Elindított eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Sikeres eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Sikeres triggerek késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Kiváltott események indítása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/munkafolyamatok  |  ActionLatency  |  Művelet késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  ActionsCompleted  |  Befejezett műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  ActionsFailed  |  Sikertelen műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  ActionsSkipped  |  Kihagyott műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  ActionsStarted  |  Elindított műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  ActionsSucceeded  |  Sikeres műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/munkafolyamatok  |  ActionSuccessLatency  |  Művelet sikerességi késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  ActionThrottledEvents  |  Művelet által szabályozott események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  BillableActionExecutions  |  Számlázható műveletek végrehajtása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  BillableTriggerExecutions  |  Számlázható trigger-végrehajtások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  BillingUsageNativeOperation  |  A natív művelet végrehajtásához használt számlázási használat  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  BillingUsageNativeOperation  |  A natív művelet végrehajtásához használt számlázási használat  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  BillingUsageStandardConnector  |  A standard szintű összekötők végrehajtásának számlázási használata  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  BillingUsageStandardConnector  |  A standard szintű összekötők végrehajtásának számlázási használata  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  BillingUsageStorageConsumption  |  Számlázási használat a tárolási felhasználás végrehajtásához  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  BillingUsageStorageConsumption  |  Számlázási használat a tárolási felhasználás végrehajtásához  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  RunFailurePercentage  |  Sikertelen futtatások százalékos aránya  |  Százalék  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/munkafolyamatok  |  RunLatency  |  Késés futtatása  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  RunsCancelled  |  Megszakított futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  RunsCompleted  |  Befejezett futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  RunsFailed  |  Sikertelen futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  RunsStarted  |  Elindított futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  RunsSucceeded  |  Sikeres futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  RunStartThrottledEvents  |  Indítási leszabályozású események futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/munkafolyamatok  |  RunSuccessLatency  |  Sikeres Futtatás késése  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  RunThrottledEvents  |  Szabályozott események futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  TotalBillableExecutions  |  Számlázandó végrehajtások összesen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/munkafolyamatok  |  TriggerFireLatency  |  Kiváltó tűz késése   |  Másodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Logic/munkafolyamatok  |  TriggerLatency  |  Trigger késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  TriggersCompleted  |  Befejezett eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  TriggersFailed  |  Sikertelen eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  TriggersFired  |  Kilőtt eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  TriggersSkipped  |  Kihagyott eseményindítók  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  TriggersStarted  |  Elindított eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  TriggersSucceeded  |  Sikeres eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Logic/munkafolyamatok  |  TriggerSuccessLatency  |  Sikeres triggerek késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Logic/munkafolyamatok  |  TriggerThrottledEvents  |  Kiváltott események indítása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Aktív magok  |  Aktív magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Aktív csomópontok  |  Aktív csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Befejezett futtatások  |  Befejezett futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Sikertelen futtatások  |  Sikertelen futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Üresjárati magok  |  Üresjárati magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Tétlen csomópontok  |  Tétlen csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Magok kihagyása  |  Magok kihagyása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Csomópontok elhagyása  |  Csomópontok elhagyása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Modell-üzembehelyezés sikertelen  |  Modell-üzembehelyezés sikertelen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Modell-üzembehelyezés elindítva  |  Modell-üzembehelyezés elindítva  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Modell-üzembehelyezés sikerült  |  Modell-üzembehelyezés sikerült  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  A modell regisztrálása nem sikerült  |  A modell regisztrálása nem sikerült  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  A modell regisztrálása sikerült  |  A modell regisztrálása sikerült  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Előzik magok  |  Előzik magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Előzik-csomópontok  |  Előzik-csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Kvóta kihasználtsága (%)  |  Kvóta kihasználtsága (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Elindított futtatások  |  Elindított futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Magok összesen  |  Magok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Csomópontok összesen  |  Csomópontok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Használhatatlan magok  |  Használhatatlan magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. MachineLearningServices/munkaterületek  |  Használhatatlan csomópontok  |  Használhatatlan csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Maps/fiókok  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. Maps/fiókok  |  Használat  |  Használat  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  AssetCount  |  Eszközök száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  AssetQuota  |  Eszköz kvótája  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  AssetQuotaUsedPercentage  |  Eszköz kvótájának kihasználtsága (%)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  ContentKeyPolicyCount  |  Tartalmi kulcsokra vonatkozó szabályzatok száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  ContentKeyPolicyQuota  |  Tartalmi kulcs házirend-kvótája  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  StreamingPolicyCount  |  Folyamatos átviteli szabályzatok száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  StreamingPolicyQuota  |  Streaming Policy-kvóta  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Adatfolyam-házirend kvótájának kihasználtsága (%)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices/streamingEndpoints  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices/streamingEndpoints  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Media/Mediaservices/streamingEndpoints  |  SuccessE2ELatency  |  A Befejezés végének késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalCount  |  GC-szüneteltetések száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalTime  |  GC felfüggesztésének teljes ideje  |  Ezredmásodpercben  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  A rendelkezésre álló régi generációs adatméret maximális száma  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  A régi generációs adatméret  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Előléptetés a régi generációs adatméretre  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Szolgáltatás CPU-kihasználtságának százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Szolgáltatáshoz hozzárendelt memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Szolgáltatás memóriájának maximális száma  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Szolgáltatás felhasznált memóriája  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  CPU-használat százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatErrorCount  |  Tomcat globális hiba  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Tomcat összesen fogadott bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tomcat-kérelem maximális ideje  |  Ezredmásodpercben  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Tomcat-kérelem összesített száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Tomcat-kérelem teljes ideje  |  Ezredmásodpercben  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Tomcat-kérelem átlagos ideje  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatSentBytes  |  Tomcat összesen eljuttatott bájtok száma  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Tomcat-munkamenet élő száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Tomcat-munkamenet maximális aktív száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Tomcat-munkamenet maximális élettartama  |  Ezredmásodpercben  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Tomcat-munkamenet létrehozva szám  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  A Tomcat-munkamenet lejárt  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  A Tomcat-munkamenet elutasította a darabszámot  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Népszerűsítse a fiatal generáció adatméretét  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Lefoglalt kötet-készlet  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Kötet készletének teljes logikai mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. NetApp/netAppAccounts/capacityPools/kötetek  |  AverageReadLatency  |  Olvasási késleltetés átlagos késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. NetApp/netAppAccounts/capacityPools/kötetek  |  AverageWriteLatency  |  Írási késleltetés átlagos késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. NetApp/netAppAccounts/capacityPools/kötetek  |  ReadIops  |  IOPS olvasása  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. NetApp/netAppAccounts/capacityPools/kötetek  |  VolumeLogicalSize  |  Kötet logikai mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. NetApp/netAppAccounts/capacityPools/kötetek  |  VolumeSnapshotSize  |  Kötet pillanatképének mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. NetApp/netAppAccounts/capacityPools/kötetek  |  WriteIops  |  IOPS írása  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Application Gateway teljes idő  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Percenkénti kérelmek száma kifogástalan állapotú gazdagépen  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  BackendConnectTime  |  Háttérbeli kapcsolat ideje  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  BackendFirstByteResponseTime  |  Háttérbeli első bájt válaszideje  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  BackendLastByteResponseTime  |  Háttérbeli utolsó bájt válaszideje  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  BackendResponseStatus  |  Háttérbeli válasz állapota  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  BlockedCount  |  Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  BlockedReqCount  |  Webalkalmazási tűzfal letiltott kérelmek száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  BytesReceived  |  Fogadott bájtok száma  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  BytesSent  |  Eljuttatott bájtok  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  CapacityUnits  |  Aktuális kapacitási egységek  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  ClientRtt  |  Ügyfél-RTT  |  Ezredmásodpercben  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  ComputeUnits  |  Aktuális számítási egységek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  Összege  |  Aktuális kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  FailedRequests  |  Sikertelen kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  HealthyHostCount  |  Kifogástalan állapotú gazdagépek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  MatchedCount  |  Webalkalmazási tűzfal teljes szabályának eloszlása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  ResponseStatus  |  Válasz állapota  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Network/applicationGateways  |  Teljesítmény  |  Teljesítmény  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  TlsProtocol  |  Ügyfél TLS protokoll  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  TotalRequests  |  Összes kérelem  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/applicationGateways  |  UnhealthyHostCount  |  Nem kifogástalan állapotú gazdagépek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/azurefirewalls  |  ApplicationRuleHit  |  Alkalmazás-szabályok találatok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/azurefirewalls  |  DataProcessed  |  Feldolgozott adatfeldolgozás  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/azurefirewalls  |  FirewallHealth  |  Tűzfal állapota  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/azurefirewalls  |  NetworkRuleHit  |  Hálózati szabályok találatok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/azurefirewalls  |  SNATPortUtilization  |  SNAT-portok kihasználtsága  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/Connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/Connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/dnszones  |  QueryVolume  |  Lekérdezési kötet  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Network/dnszones  |  RecordSetCapacityUtilization  |  Rekordazonosító kapacitásának kihasználtsága  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/dnszones  |  RecordSetCount  |  Rekordok készletének száma  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/expressRouteCircuits  |  ArpAvailability  |  ARP rendelkezésre állása  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRouteCircuits  |  BgpAvailability  |  BGP rendelkezésre állása  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRouteCircuits/társak  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRouteCircuits/társak  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Átlag | 
| Nem  | Nem |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRoutePorts  |  AdminState  |  AdminState  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  BackendHealthPercentage  |  Háttér állapotának százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  BackendRequestCount  |  Háttérbeli kérelmek száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  BackendRequestLatency  |  Háttérbeli kérelmek késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  BillableResponseSize  |  Számlázható válasz mérete  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  RequestCount  |  Kérelmek száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  RequestSize  |  Kérelem mérete  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  ResponseSize  |  Válasz mérete  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  TotalLatency  |  Teljes késés  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Webalkalmazási tűzfalra vonatkozó kérelmek száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Network/loadBalancers  |  AllocatedSnatPorts  |  Lefoglalt SNAT portok (előzetes verzió)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/loadBalancers  |  ByteCount  |  Bájtok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/loadBalancers  |  DipAvailability  |  Állapot mintavételi állapota  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/loadBalancers  |  PacketCount  |  Csomagok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/loadBalancers  |  SnatConnectionCount  |  SNAT-kapcsolatok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/loadBalancers  |  SYNCount  |  SYN-szám  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Network/loadBalancers  |  UsedSnatPorts  |  Használt SNAT-portok (előzetes verzió)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/loadBalancers  |  VipAvailability  |  Adatelérési út rendelkezésre állása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/networkInterfaces  |  BytesReceivedRate  |  Fogadott bájtok száma  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/networkInterfaces  |  BytesSentRate  |  Eljuttatott bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/networkInterfaces  |  PacketsReceivedRate  |  Fogadott csomagok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/networkInterfaces  |  PacketsSentRate  |  Küldött csomagok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Átlagos menetidő (MS)  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Sikertelen ellenőrzések százalékos aránya (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  %-Os mintavétel sikertelen  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Oda-és visszaút időpontja (MS) (előzetes verzió)  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  ByteCount  |  Bájtok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  BytesDroppedDDoS  |  Bejövő bájtok elvetve DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  BytesForwardedDDoS  |  Bejövő bájtok továbbított DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  BytesInDDoS  |  Bejövő bájtok DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  DDoSTriggerSYNPackets  |  Bejövő SYN-csomagok a DDoS-elhárítás elindításához  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  DDoSTriggerTCPPackets  |  Bejövő TCP-csomagok a DDoS-mérséklés indításához  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  DDoSTriggerUDPPackets  |  Bejövő UDP-csomagok a DDoS-elhárítás elindításához  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  IfUnderDDoSAttack  |  DDoS-támadás alatt vagy nem  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  PacketCount  |  Csomagok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  PacketsDroppedDDoS  |  Bejövő csomagok eldobott DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  PacketsForwardedDDoS  |  Bejövő csomagok továbbított DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  PacketsInDDoS  |  Bejövő csomagok DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  SynCount  |  SYN-szám  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  TCPBytesDroppedDDoS  |  Bejövő TCP-bájtok elvetve DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  TCPBytesForwardedDDoS  |  Bejövő TCP-bájtok továbbított DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  TCPBytesInDDoS  |  Bejövő TCP-bájtok DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  TCPPacketsDroppedDDoS  |  Bejövő TCP-csomagok eldobott DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  TCPPacketsForwardedDDoS  |  Bejövő TCP-csomagok továbbított DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  TCPPacketsInDDoS  |  Bejövő TCP-csomagok DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  UDPBytesDroppedDDoS  |  Bejövő UDP-bájtok elvetve DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  UDPBytesForwardedDDoS  |  Bejövő UDP-bájtok továbbított DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  UDPBytesInDDoS  |  Bejövő UDP bájtok DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  UDPPacketsDroppedDDoS  |  Bejövő UDP-csomagok eldobott DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  UDPPacketsForwardedDDoS  |  Bejövő UDP-csomagok továbbított DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  UDPPacketsInDDoS  |  Bejövő UDP-csomagok DDoS  |  CountPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/nyilvános IP  |  VipAvailability  |  Adatelérési út rendelkezésre állása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Végponti állapot végpont szerint  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. Network/trafficManagerProfiles  |  QpsByEndpoint  |  Visszaadott végponti lekérdezések  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  AverageBandwidth  |  Átjáró S2S sávszélessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  P2SBandwidth  |  Átjáró P2S sávszélessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  P2SConnectionCount  |  P2S-kapcsolatok száma  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Bújtatási sávszélesség  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Alagút kimenő bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Alagút kimenő terminálszolgáltatási eltérési csomagjainak eldobása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Alagút kimenő csomagjai  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Alagutak bejövő bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Az alagút bejövő HOZZÁFÉRÉSe nem egyezik a csomagok eldobásával  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Bújtatási bejövő csomagok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  A pingelések időpontjának kerekítése egy virtuális géphez  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Sikertelen pingelések egy virtuális géphez  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  bejövő  |  Bejövő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  bejövő. ALL. failedrequests  |  Minden bejövő sikertelen kérelem  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  bejövő. ALL. requests  |  Minden bejövő kérelem  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  bejövő. ütemezett  |  Ütemezett leküldéses értesítések elküldve  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  bejövő. ütemezett. Mégse  |  Ütemezett leküldéses értesítések megszakítva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  a telepítés. All  |  Telepítési felügyeleti műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  telepítés. Törlés  |  Telepítési műveletek törlése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  telepítés. Get  |  Telepítési műveletek beolvasása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  Installation. patch  |  Javítások telepítési műveletei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  telepítési. upsert  |  Telepítési műveletek létrehozása vagy frissítése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  notificationhub. leküldések  |  Minden kimenő értesítés  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. allpns. badorexpiredchannel  |  Rossz vagy lejárt csatorna-hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. allpns. channelerror  |  Csatorna hibái  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. allpns. invalidpayload  |  Hasznos adatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. allpns. pnserror  |  Külső értesítési rendszerhibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. allpns. sikeres  |  Sikeres értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. apns. badchannel  |  APNS hibás csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. apns. expiredchannel  |  APNS lejárt csatorna hibája  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. apns. invalidcredentials  |  APNS-hitelesítési hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. apns. invalidnotificationsize  |  A APNS érvénytelen értesítési méretet észlelt  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. apns. pnserror  |  APNS hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. apns. sikeres  |  APNS sikeres értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. authenticationerror  |  GCM-hitelesítési hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. badchannel  |  GCM hibás csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. expiredchannel  |  GCM lejárt csatorna hibája  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. invalidcredentials  |  GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. invalidnotificationformat  |  GCM – érvénytelen értesítési formátum  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. invalidnotificationsize  |  A GCM érvénytelen értesítési méretet észlelt  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. pnserror  |  GCM hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. sikeres  |  GCM sikeres értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. szabályozva  |  GCM-szabályozású értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. GCM. wrongchannel  |  GCM rossz csatorna hibája  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. authenticationerror  |  MPNS-hitelesítési hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. badchannel  |  MPNS hibás csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. channeldisconnected  |  MPNS csatorna leválasztva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. Dropped  |  MPNS eldobott értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. invalidcredentials  |  MPNS – érvénytelen hitelesítő adatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. invalidnotificationformat  |  MPNS – érvénytelen értesítési formátum  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. pnserror  |  MPNS hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. sikeres  |  MPNS sikeres értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. mpns. szabályozva  |  MPNS-szabályozású értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. authenticationerror  |  WNS-hitelesítési hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. badchannel  |  WNS hibás csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. channeldisconnected  |  WNS csatorna leválasztva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. channelthrottled  |  WNS csatorna szabályozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. Dropped  |  WNS eldobott értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. expiredchannel  |  WNS lejárt csatorna hibája  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. invalidcredentials  |  WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. invalidnotificationformat  |  WNS – érvénytelen értesítési formátum  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. invalidnotificationsize  |  A WNS érvénytelen értesítési méretet észlelt  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. invalidtoken  |  WNS-hitelesítési hibák (érvénytelen token)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. pnserror  |  WNS hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. sikeres  |  WNS sikeres értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. szabályozva  |  WNS-szabályozású értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. tokenproviderunreachable  |  WNS-hitelesítési hibák (nem érhető el)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  kimenő. wns. wrongtoken  |  WNS-hitelesítési hibák (hibás token)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  regisztráció. All  |  Regisztrációs műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  regisztráció. Create  |  Regisztráció-létrehozási műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  regisztráció. Delete  |  Regisztrációs törlési műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  regisztráció. Get  |  Regisztrálási olvasási műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  regisztráció. frissítés  |  Regisztrációs frissítési műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. NotificationHubs/névterek/NotificationHubs  |  ütemezett. függőben  |  Függőben lévő ütemezett értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Rendelkezésre álló memória Average_%-ban  |  Rendelkezésre álló memória%-ban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ rendelkezésre álló szabad hely a lapozófájlban  |  Rendelkezésre álló swap-terület (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_%-os előjegyzett bájtok használatban  |  Előjegyzett memória%-ban használatban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_% DPC idő  |  % DPC idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ szabad inode (%)  |  Szabad inode%-ban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ szabad terület (%)  |  Szabad terület (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ szabad terület (%)  |  Szabad terület (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ üresjárati idő%-ban  |  Üresjáratban eltöltött időhányad (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_%-os megszakítási idő  |  Megszakítási idő%-ban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_% IO várakozási idő  |  I/o várakozási idő%-ban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_%-os szép idő  |  % Nice idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_%-os privilegizált idő  |  %-Os privilegizált idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ processzoridő  |  A processzor kihasználtsága (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ processzoridő  |  A processzor kihasználtsága (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ használt inode  |  Felhasznált inode%-ban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Felhasznált memória Average_%-ban  |  Felhasznált memória (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ felhasznált terület%-ban  |  Felhasznált terület (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_ felhasznált lapozófájl-terület  |  Felhasznált swap-terület%-ban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_%-os felhasználói idő  |  Felhasználói idő%-ban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Available MB-ban  |  Rendelkezésre álló memória (MB)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Available MB memória  |  Rendelkezésre álló memória (MB)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Available MB-ban való swap  |  Rendelkezésre álló memória (MB)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.  |  Átlagos írási idő (mp/olvasás)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.  |  Átlagos írási idő (mp/olvasás)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Avg. Lemez (mp/átvitel)  |  Átlagos műveleti idő (mp/átvitel)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.  |  Átlagos írási idő (mp/írás)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.  |  Átlagos írási idő (mp/írás)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Fogadott Average_Bytes/mp  |  Fogadott bájtok/s  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Küldési Average_Bytes másodpercenként  |  Küldési sebesség (bájt/s)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Bytes összesen/mp  |  Összes bájt/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Current a lemez várólistájának hossza  |  Lemez aktuális várólistájának hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Olvasási sebesség (bájt/s) Average_Disk  |  Lemez olvasási sebessége (bájt/s)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Olvasási Average_Disk másodpercenként  |  Olvasási sebesség (lemez/mp)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Olvasási Average_Disk másodpercenként  |  Olvasási sebesség (lemez/mp)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Disk adatátvitel másodpercenként  |  Lemez átvitele másodpercenként  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Disk adatátvitel másodpercenként  |  Lemez átvitele másodpercenként  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Írási sebesség (bájt/s) Average_Disk  |  Lemez írási sebessége (bájt/s)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Írás/mp Average_Disk  |  Írási sebesség (írás/mp)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Írás/mp Average_Disk  |  Írási sebesség (írás/mp)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Free megabájt  |  Szabad terület (MB)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Free megabájt  |  Szabad terület (MB)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Fizikai memória Average_Free  |  Szabad fizikai memória  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Free lemezterület a Lapozófájlokban  |  Szabad terület a Lapozófájlokban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Free virtuális memória  |  Szabad virtuális memória  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Logical lemez sebessége (bájt/s)  |  Logikai lemez sebessége (bájt/s)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Olvasási Average_Page másodpercenként  |  Olvasott lap/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Írás/mp Average_Page  |  Írási idő/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Pages előállított/másodperc  |  Lap/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Pct emelt szintű idő  |  PCT rendszerjogosultságú idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Pct felhasználói idő  |  PCT felhasználói idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Physical lemez sebessége (bájt/s)  |  Fizikai lemez sebessége (bájt/s)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Processes  |  Folyamatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Processor várólista hossza  |  Processzor-várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Lapozófájlokban tárolt Average_Size  |  Lapozófájlokban tárolt méret  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Total bájtok  |  Bájtok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Fogadott Average_Total bájtok száma  |  Fogadott bájtok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Total továbbított bájtok  |  Továbbított bájtok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Ütközések Average_Total  |  Ütközések összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Total fogadott csomagok  |  Fogadott csomagok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Total továbbított csomagok  |  Továbbított csomagok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Rx-hibák Average_Total  |  Rx-hibák összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Total TX-hibák  |  TX-hibák összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Uptime  |  Üzemidő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Used MB-ban felcserélt terület  |  Felhasznált memória (MB) – lapozófájl  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Used memória kilobájtban  |  Felhasznált memória (kilobájt)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Used memória MB-ban  |  Felhasznált memória (MB)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Users  |  Felhasználók  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Average_Virtual megosztott memória  |  Virtuális megosztott memória  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Esemény  |  Esemény  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. OperationalInsights/munkaterületek  |  Szívverés  |  Szívverés  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. OperationalInsights/munkaterületek  |  Frissítés  |  Frissítés  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. PowerBIDedicated/kapacitások  |  memory_metric  |  Memory (Memória)  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. PowerBIDedicated/kapacitások  |  memory_thrashing_metric  |  Memória-Kiverés (adathalmazok)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. PowerBIDedicated/kapacitások  |  qpu_high_utilization_metric  |  QPU magas kihasználtság  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. PowerBIDedicated/kapacitások  |  QueryDuration  |  Lekérdezés időtartama (adatkészletek)  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. PowerBIDedicated/kapacitások  |  QueryPoolJobQueueLength  |  Lekérdezési készlet nyomtatási várólistájának hossza (adatkészletek)  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Aktív kapcsolatai  |  Aktív kapcsolatai  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  ActiveListeners  |  ActiveListeners  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Relay/névterek  |  BytesTransferred  |  BytesTransferred  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Figyelőkapcsolatokra – vonatkozó ügyfélhibái  |  Figyelőkapcsolatokra – vonatkozó ügyfélhibái  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Figyelőkapcsolatokra – ServerError  |  Figyelőkapcsolatokra – ServerError  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Figyelőkapcsolatokra – sikeres  |  Figyelőkapcsolatokra – sikeres  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Figyelőkapcsolatokra – TotalRequests  |  Figyelőkapcsolatokra – TotalRequests  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Bontásai  |  Bontásai  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Feladói kapcsolatokra – vonatkozó ügyfélhibái  |  Feladói kapcsolatokra – vonatkozó ügyfélhibái  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Feladói kapcsolatokra – ServerError  |  Feladói kapcsolatokra – ServerError  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Feladói kapcsolatokra – sikeres  |  Feladói kapcsolatokra – sikeres  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  Feladói kapcsolatokra – TotalRequests  |  Feladói kapcsolatokra – TotalRequests  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Relay/névterek  |  SenderDisconnects  |  SenderDisconnects  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Search/searchServices  |  SearchLatency  |  Keresési késés  |  Másodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Search/searchServices  |  SearchQueriesPerSecond  |  Keresési lekérdezések másodpercenként  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Szabályozott keresési lekérdezések százalékos aránya  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  Aktív kapcsolatai  |  Aktív kapcsolatai  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  ActiveMessages  |  Üzenetsor vagy témakör aktív üzeneteinek száma.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  ConnectionsClosed  |  A kapcsolatok lezárva.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  ConnectionsOpened  |  Megnyitott kapcsolatok.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  CPUXNS  |  CPU (elavult)  |  Százalék  |  Maximum | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  DeadletteredMessages  |  Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. ServiceBus/névterek  |  IncomingMessages  |  Bejövő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. ServiceBus/névterek  |  IncomingRequests  |  Bejövő kérelmek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  Üzenetek  |  Üzenetsor vagy témakör üzeneteinek száma.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  NamespaceCpuUsage  |  CPU  |  Százalék  |  Maximum | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  NamespaceMemoryUsage  |  Memóriahasználat  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. ServiceBus/névterek  |  OutgoingMessages  |  Kimenő üzenetek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  ScheduledMessages  |  Az üzenetsor/témakör ütemezett üzeneteinek száma.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  Kiszolgálóhibái  |  Kiszolgálói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  Méret  |  Méret  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  SuccessfulRequests  |  Sikeres kérések  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  ThrottledRequests  |  Szabályozott kérelmek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  UserErrors  |  Felhasználói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. ServiceBus/névterek  |  WSXNS  |  Memóriahasználat (elavult)  |  Százalék  |  Maximum | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  ActualCpu  |  ActualCpu  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  ActualMemory  |  ActualMemory  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  AllocatedCpu  |  AllocatedCpu  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  AllocatedMemory  |  AllocatedMemory  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  ApplicationStatus  |  ApplicationStatus  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  Tároló állapota:  |  Tároló állapota:  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  CpuUtilization  |  CpuUtilization  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  MemoryUtilization  |  MemoryUtilization  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  RestartCount  |  RestartCount  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. ServiceFabricMesh/alkalmazások  |  ServiceStatus  |  ServiceStatus  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SignalRService/szignáló  |  ConnectionCount  |  Kapcsolatok száma  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. SignalRService/szignáló  |  InboundTraffic  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. SignalRService/szignáló  |  MessageCount  |  Üzenetek száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. SignalRService/szignáló  |  OutboundTraffic  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. SignalRService/szignáló  |  SystemErrors  |  Rendszerhibák  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. SignalRService/szignáló  |  UserErrors  |  Felhasználói hibák  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. SQL/managedInstances  |  avg_cpu_percent  |  Átlagos CPU-százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/managedInstances  |  io_bytes_read  |  I/o-bájtok olvasása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/managedInstances  |  io_bytes_written  |  I/o-bájtok írása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/managedInstances  |  io_requests  |  IO-kérelmek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/managedInstances  |  reserved_storage_mb  |  Tárterület fenntartva  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/managedInstances  |  storage_space_used_mb  |  Felhasznált tárterület  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/managedInstances  |  virtual_core_count  |  Virtuális mag száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók  |  database_dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók  |  database_storage_used  |  Felhasznált adatterület  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók  |  dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók  |  dtu_used  |  Használt DTU  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók  |  storage_used  |  Felhasznált adatterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  allocated_data_storage  |  Lefoglalt adatterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  app_cpu_billed  |  Az alkalmazás CPU-számlázása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  app_cpu_percent  |  Alkalmazás CPU-aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  app_memory_percent  |  Az alkalmazás memóriájának százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  blocked_by_firewall  |  Tűzfal blokkolja  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  cache_hit_percent  |  Gyorsítótár találati százaléka  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  cache_used_percent  |  Gyorsítótár használt százaléka  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  connection_failed  |  Sikertelen kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  connection_successful  |  Sikeres kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  cpu_limit  |  CPU-korlát  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  cpu_percent  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  cpu_used  |  Felhasznált CPU  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  holtpont  |  Holtpontok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  dtu_limit  |  DTU korlátja  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  dtu_used  |  Használt DTU  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  dwu_consumption_percent  |  DWU százalékos aránya  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  dwu_limit  |  DWU korlátja  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  dwu_used  |  Használt DWU  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  local_tempdb_usage_percent  |  Helyi tempdb százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  log_write_percent  |  Naplózási IO-százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  memory_usage_percent  |  Memória százaléka  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  physical_data_read_percent  |  Adat IO kihasználtsága (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  sessions_percent  |  Munkamenetek százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  storage  |  Felhasznált adatterület  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  storage_percent  |  Felhasznált adatterület százalékos aránya  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  tempdb_data_size  |  Tempdb adatfájl mérete (kilobájt)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  tempdb_log_size  |  Tempdb-naplófájl mérete (kilobájt)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/adatbázisok  |  tempdb_log_used_percent  |  Tempdb százalékos naplója használatban  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  workers_percent  |  Munkavégzők százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/adatbázisok  |  xtp_storage_percent  |  Memóriában tárolt OLTP tárolási százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  allocated_data_storage  |  Lefoglalt adatterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  allocated_data_storage_percent  |  Lefoglalt adatterület százalékos aránya  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  cpu_limit  |  CPU-korlát  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/elasticPools  |  cpu_percent  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  cpu_used  |  Felhasznált CPU  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_allocated_data_storage  |  Lefoglalt adatterület  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_cpu_limit  |  CPU-korlát  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_cpu_percent  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_cpu_used  |  Felhasznált CPU  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_eDTU_used  |  használt eDTU  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_log_write_percent  |  Naplózási IO-százalék  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_physical_data_read_percent  |  Adat IO kihasználtsága (%)  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_sessions_percent  |  Munkamenetek százalékos aránya  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_storage_used  |  Felhasznált adatterület  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  database_workers_percent  |  Munkavégzők százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/elasticPools  |  dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  eDTU_limit  |  eDTU korlátja  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/elasticPools  |  eDTU_used  |  használt eDTU  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/elasticPools  |  log_write_percent  |  Naplózási IO-százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/elasticPools  |  physical_data_read_percent  |  Adat IO kihasználtsága (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/elasticPools  |  sessions_percent  |  Munkamenetek százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  storage_limit  |  Az adatmaximális méret  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  storage_percent  |  Felhasznált adatterület százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  storage_used  |  Felhasznált adatterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  tempdb_data_size  |  Tempdb adatfájl mérete (kilobájt)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  tempdb_log_size  |  Tempdb-naplófájl mérete (kilobájt)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. SQL/kiszolgálók/elasticPools  |  tempdb_log_used_percent  |  Tempdb százalékos naplója használatban  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/elasticPools  |  workers_percent  |  Munkavégzők százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. SQL/kiszolgálók/elasticPools  |  xtp_storage_percent  |  Memóriában tárolt OLTP tárolási százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts  |  UsedCapacity  |  Felhasznált kapacitás  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  BlobCapacity  |  BLOB kapacitása  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  BlobCount  |  Blobok száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  ContainerCount  |  BLOB-tárolók száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  IndexCapacity  |  Index kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/blobServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  FileCapacity  |  Fájl kapacitása  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  FileCount  |  Fájlok száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  FileShareCount  |  Fájlmegosztás száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  FileShareQuota  |  Fájlmegosztás kvótájának mérete  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Fájlmegosztás pillanatképének száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Fájlmegosztás pillanatképének mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/fileServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  QueueCapacity  |  Várólista kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  QueueCount  |  Várólista száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Üzenetsor-üzenetek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/queueServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  TableCapacity  |  Tábla kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  TableCount  |  Táblák száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  TableEntityCount  |  Tábla entitások száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. Storage/storageAccounts/tableServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientIOPS  |  Ügyfél teljes IOPS  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientLatency  |  Ügyfél átlagos késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientLockIOPS  |  Ügyfél-zárolási IOPS  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientMetadataReadIOPS  |  Ügyfél metaadatainak olvasása IOPS  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientMetadataWriteIOPS  |  Ügyfél metaadatainak írási IOPS  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientReadIOPS  |  Ügyfél olvasási IOPS  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientReadThroughput  |  Gyorsítótár átlagos olvasási átviteli sebessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientWriteIOPS  |  Ügyfél írási IOPS  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  ClientWriteThroughput  |  Gyorsítótár átlagos írási sebessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetAsyncWriteThroughput  |  StorageTarget aszinkron írási átviteli sebesség  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetFillThroughput  |  StorageTarget-kitöltési sebesség  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetHealth  |  Tárolási cél állapota  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetIOPS  |  Összes StorageTarget-IOPS  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetLatency  |  StorageTarget késés  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetMetadataReadIOPS  |  StorageTarget-metaadatok IOPS olvasása  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetMetadataWriteIOPS  |  StorageTarget-metaadatok írási IOPS  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetReadAheadThroughput  |  StorageTarget – olvasási sebesség  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetReadIOPS  |  StorageTarget olvasási IOPS  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetSyncWriteThroughput  |  StorageTarget szinkron írási sebessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetTotalReadThroughput  |  StorageTarget összesen olvasási átviteli sebesség  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetTotalWriteThroughput  |  StorageTarget összes írási sebessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  StorageTargetWriteIOPS  |  StorageTarget írási IOPS  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. StorageCache/gyorsítótárak  |  Üzemidő  |  Üzemidő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Szinkronizálási munkamenet eredménye  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Szinkronizált bájtok száma  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Felhőbeli rétegek felidézésének mérete alkalmazás szerint  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Felhőbeli rétegek felidézésének mérete  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Felhőbeli rétegek felidézése  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Felhőbeli rétegek felidézésének átviteli sebessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Kiszolgáló online állapota  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Nem szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  Kiszolgáló online állapota  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Felhőbeli rétegek felidézése  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Szinkronizált bájtok száma  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Nem szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Szinkronizált bájtok száma  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Nem szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Sikertelen függvények kérései  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Függvények eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Függvények kérései  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  ConversionErrors  |  Adatátalakítási hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  DeserializationError  |  Bemeneti deszerializálási hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Megrendelési események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Korai bemeneti események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  Hibák  |  Futásidejű hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventBytes  |  Bemeneti esemény bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  InputEvents  |  Bemeneti események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Várakozó bemeneti eseményei  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Fogadott bemeneti források  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  LateInputEvents  |  Késői bemeneti események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  OutputEvents  |  Kimeneti események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Vízjel késleltetése  |  Másodperc  |  Maximum | 
| **Igen**  | Nem |  Microsoft. StreamAnalytics/streamingjobs  |  ResourceUtilization  |  SU% kihasználtsága  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  Lemez olvasási bájtjai  |  Lemez olvasási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  Lemez olvasási művelete/mp  |  Lemez olvasási művelete/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  Lemez írási bájtjai  |  Lemez írási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  Lemez írási műveletei/mp  |  Lemez írási műveletei/mp  |  CountPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Lemez olvasási sebessége (bájt/s)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Lemez olvasási késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Lemezes olvasási műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Lemez írási sebessége (bájt/s)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Lemez írási késése  |  Ezredmásodpercben  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Lemez írási műveletei  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Memória aktív  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Megadott memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Felhasznált memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  Bejövő hálózat  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  Kimenő hálózat  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Hálózat bájt/mp-ben  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Hálózati kimenő bájtok/s  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft. VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  CPU-készültség százalékos aránya  |  Ezredmásodpercben  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Aktív kérések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Átlagos válaszidő  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  A-ben tárolt adatértékek  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Kimenő adatvesztés  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Lemezvezérlő-várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http-2xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http-3xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http-4xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Http-kiszolgálói hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Http-várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Nagy App Service-csomag feldolgozói  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Közepes App Service-csomag feldolgozói  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Kisméretű App Service-csomag feldolgozói  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Összes előtér  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Rendelkezésre álló munkavégzők  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Alkalmazottak összesen  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Használt feldolgozók  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  BytesReceived  |  A-ben tárolt adatértékek  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  BytesSent  |  Kimenő adatvesztés  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  CpuPercentage  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  DiskQueueLength  |  Lemezvezérlő-várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  HttpQueueLength  |  Http-várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  MemoryPercentage  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpCloseWait  |  TCP-bezárási várakozás  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpClosing  |  TCP-zárás  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpEstablished  |  A TCP létrejött  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpFinWait1  |  TCP fin WAIT 1  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpFinWait2  |  TCP fin WAIT 2  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpLastAck  |  TCP Last ACK  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpSynReceived  |  TCP SYN kapott  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpSynSent  |  Eljuttatott TCP SYN  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/kiszolgálófarmok  |  TcpTimeWait  |  TCP-idő várakozása  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  AppConnections  |  Kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  AverageMemoryWorkingSet  |  Memória átlagos munkakészlete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  AverageResponseTime  |  Átlagos válaszidő  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  BytesReceived  |  A-ben tárolt adatértékek  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  BytesSent  |  Kimenő adatvesztés  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  CpuTime  |  CPU-idő  |  Másodperc  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  CurrentAssemblies  |  Aktuális szerelvények  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  FunctionExecutionCount  |  Függvény végrehajtásának száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  FunctionExecutionUnits  |  Függvények végrehajtási egységei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Gen0Collections  |  0. generációs Garbage-gyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Gen1Collections  |  1. generációs Garbage gyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Gen2Collections  |  2. generációs Garbage gyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Kezeli  |  Leírók száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  HealthCheckStatus  |  Állapot-ellenőrzési állapot  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http101  |  Http 101  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http2xx  |  Http-2xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http3xx  |  Http-3xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http401  |  HTTP 401  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http403  |  Http 403  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http404  |  Http 404  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http406  |  Http 406  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http4xx  |  Http-4xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Http5xx  |  Http-kiszolgálói hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  HttpResponseTime  |  Válaszidő  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  IoOtherBytesPerSecond  |  IO – egyéb bájtok másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  IoOtherOperationsPerSecond  |  IO egyéb műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  IoReadBytesPerSecond  |  IO olvasási bájtok másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  IoReadOperationsPerSecond  |  I/o-olvasási műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  IoWriteBytesPerSecond  |  IO írási bájtok másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  IoWriteOperationsPerSecond  |  IO írási műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  MemoryWorkingSet  |  Memória munkakészlete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  PrivateBytes  |  Saját bájtok  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  RequestsInApplicationQueue  |  Kérelmek az alkalmazás-várólistán  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  Szálak  |  Szálak száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  TotalAppDomains  |  Alkalmazás összes tartománya  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites  |  TotalAppDomainsUnloaded  |  Összes kitöltött alkalmazás-tartomány  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  AppConnections  |  Kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  AverageMemoryWorkingSet  |  Memória átlagos munkakészlete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  AverageResponseTime  |  Átlagos válaszidő  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  BytesReceived  |  A-ben tárolt adatértékek  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  BytesSent  |  Kimenő adatvesztés  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  CpuTime  |  CPU-idő  |  Másodperc  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  CurrentAssemblies  |  Aktuális szerelvények  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  FunctionExecutionCount  |  Függvény végrehajtásának száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  FunctionExecutionUnits  |  Függvények végrehajtási egységei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Gen0Collections  |  0. generációs Garbage-gyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Gen1Collections  |  1. generációs Garbage gyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Gen2Collections  |  2. generációs Garbage gyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Kezeli  |  Leírók száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  HealthCheckStatus  |  Állapot-ellenőrzési állapot  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http101  |  Http 101  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http2xx  |  Http-2xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http3xx  |  Http-3xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http401  |  HTTP 401  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http403  |  Http 403  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http404  |  Http 404  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http406  |  Http 406  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http4xx  |  Http-4xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Http5xx  |  Http-kiszolgálói hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  HttpResponseTime  |  Válaszidő  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  IoOtherBytesPerSecond  |  IO – egyéb bájtok másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  IoOtherOperationsPerSecond  |  IO egyéb műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  IoReadBytesPerSecond  |  IO olvasási bájtok másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  IoReadOperationsPerSecond  |  I/o-olvasási műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  IoWriteBytesPerSecond  |  IO írási bájtok másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  IoWriteOperationsPerSecond  |  IO írási műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  MemoryWorkingSet  |  Memória munkakészlete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  PrivateBytes  |  Saját bájtok  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  RequestsInApplicationQueue  |  Kérelmek az alkalmazás-várólistán  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  Szálak  |  Szálak száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  TotalAppDomains  |  Alkalmazás összes tartománya  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft. Web/Sites/Slots  |  TotalAppDomainsUnloaded  |  Összes kitöltött alkalmazás-tartomány  |  Darabszám  |  Átlag | 
