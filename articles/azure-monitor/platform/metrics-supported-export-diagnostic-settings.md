---
title: Az Azure Monitor platformmetrikák exportálhatók a diagnosztikai beállításokkal
description: Az Azure Monitor segítségével az egyes erőforrástípusokhoz elérhető metrikák listája.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 6be8cb1b7e74301d16a1174f5ca2b774334dac3f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422111"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Az Azure Monitor platformmetrikák exportálhatók a diagnosztikai beállításokkal

Az Azure Monitor [platformmetrikák](data-platform-metrics.md) at alapértelmezés szerint konfiguráció nélkül biztosít. Számos lehetőséget kínál a platformmetrikák kal való interakcióra, beleértve a portálon való ábrázolásukat, a REST API-n keresztüli hozzáférésüket, vagy a PowerShell vagy a CLI használatával történő lekérdezésüket. Tekintse meg [a metrikák által támogatott](metrics-supported.md) a platform metrikák jelenleg elérhető az Azure Monitor konszolidált metrika-folyamat teljes listáját. A metrikák lekérdezéséhez és eléréséhez használja a [2018-01-01 api-verziót.](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) Más metrikák is elérhetők lehetnek a portálon, vagy örökölt API-k használatával.

A platform metrikák az Azure monitor folyamat más helyekre exportálhatja a két módon.
1. [Diagnosztikai beállítások használatával](diagnostic-settings.md) küldanek a Log Analytics, az Event Hubs vagy az Azure Storage.
2. A [REST API metrikák](https://docs.microsoft.com/rest/api/monitor/metrics/list) használata

Az Azure Monitor-háttérrendszer bonyolultsága miatt nem minden metrika exportálható diagnosztikai beállításokkal. Az alábbi táblázat felsorolja, hogy a diagnosztikai beállításokkal exportálható és nem is exportálható.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Változás a null-ok és a nulla értékek viselkedésére 
 
A diagnosztikai beállításokkal exportálható platformmetrikák hoz néhány metrika, amelynek az Azure Monitor értelmezi a "0s" a "Null". Ez némi zavart okozott a valós '0s' (az erőforrás által kibocsátott) és a "0s" (Nulls) között. Hamarosan változás következik be, és a diagnosztikai beállításokkal exportált platformmutatók nem exportálják a "0s"-t, kivéve, ha az alapul szolgáló erőforrás valóban kibocsátotta őket. A változást 2020. április 1-re tervezték, de a COVID-19 miatt irdúgy is elhalasztották. 

Ne feledje:

1.  Ha töröl egy erőforráscsoportot vagy egy adott erőforrást, az érintett erőforrások metrikaadatait a rendszer a továbbiakban nem küldi el a diagnosztikai beállítási exportálási céloknak. Ez azt, hogy a továbbiakban nem jelenik meg az Event Hubs, storage-fiókok és a Log Analytics-munkaterületek.
2.  Ez a javulás minden nyilvános és privát felhőben elérhető lesz.
3.  Ez a módosítás nem befolyásolja az alábbi élmények egyikének viselkedését sem: 
   - Diagnosztikai beállításokkal exportált platformerőforrás-naplók
   - Mérőszámok diagramja a Metrikakezelőben
   - Riasztás a platform mérőszámairól
 
## <a name="metrics-exportable-table"></a>Metrikák exportálható táblázat 

A táblázat a következő oszlopokat tartalmazza. 
- Exportálható a diagnosztikai beállításokon keresztül? 
- Null / 0 által végrehajtott 
- ResourceType 
- Metrika 
- MetricDisplayName (Metrikusmegjelenítésneve)
- Unit (Egység) 
- AggregationType (AggregationType)


> [!NOTE]
> Az alábbi táblázat alján lehet egy vízszintes görgetősáv. Ha úgy gondolja, hogy hiányoznak az adatok, ellenőrizze, hogy a görgetősáv teljesen balra van-e.  


| Exportálható a diagnosztikai beállításokon keresztül?  | Már bocsátanak ki NULL-okat |  ResourceType  |  Metrika  |  MetricDisplayName (Metrikusmegjelenítésneve)  |  Unit (Egység)  |  AggregationType (AggregationType) | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  CleanerCurrentPrice  |  Memória: Tisztább aktuális ár  |  Darabszám  |  Átlag | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  CleanerMemoryNem zsugorítható  |  Memória: A tisztább memória nem zsugorodik  |  Bájt  |  Átlag | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  CleanerMemoryZsugorodikható  |  Memória: Tisztább memória zsugorodik  |  Bájt  |  Átlag | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  CommandPoolBusyThreads  |  Szálak: Parancskészlet foglalt szálai  |  Darabszám  |  Átlag | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  CommandPoolIdleThreads  |  Szálak: Parancskészlet tétlen szálai  |  Darabszám  |  Átlag | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  CommandPoolJobQueueLength  |  Parancskészlet feladatvárólistájának hossza  |  Darabszám  |  Átlag | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  Jelenlegi kapcsolatok  |  Kapcsolat: Jelenlegi kapcsolatok  |  Darabszám  |  Átlag | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  CurrentUserSessions  |  Aktuális felhasználói munkamenetek  |  Darabszám  |  Átlag | 
| Igen****  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  LongParsingBusyThreads  |  Szálak: Hosszú elemzés foglalt szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  LongParsingIdleThreads  |  Szálak: Hosszú elemzés tétlen szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  LongParsingJobQueueLength  |  Szálak: Hosszú elemzési feladat várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  mashup_engine_memory_metric  |  M motor memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  mashup_engine_private_bytes_metric  |  M motor privát bájt  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  mashup_engine_qpu_metric  |  M Motor QPU  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  mashup_engine_virtual_bytes_metric  |  M motor virtuális bájtjai  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  memory_metric  |  Memory (Memória)  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  memory_thrashing_metric  |  Memóriaakadozás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  MemoryLimitHard memória  |  Memória: Memória korlát kemény  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  MemoryLimitHigh  |  Memória: Memória limit magas  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  MemoryLimitLow memória  |  Memória: Kevés memóriakorlát  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  MemoryLimitVertiPaq  |  Memória: Memóriakorlát VertiPaq  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  Memóriahasználat  |  Memória: Memóriahasználat  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  private_bytes_metric  |  Privát bájtok  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ProcessingPoolBusyIOJobThreads  |  Szálak: A készlet foglalt I/O-feladatszálainak feldolgozása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ProcessingPoolBusyNonIOThreads  |  Szálak: A készlet foglalt, nem I/O-szálai  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ProcessingPoolIdleIOJobThreads  |  Szálak: A készlet tétlen I/O-feladatszálainak feldolgozása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ProcessingPoolIdleNonIOThreads  |  Szálak: A készlet alapjárati nem I/O-szálai  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ProcessingPoolIOJobQueueLength  |  Szálak: A készlet I/O-feladatvárólistájának hosszának feldolgozása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ProcessingPoolJobQueueLength  |  Készletfeladat-várólista hosszának feldolgozása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  qpu_metric  |  QPU  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  QueryPoolBusyThreads  |  Lekérdezéskészlet foglalt szálai  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  QueryPoolIdleThreads  |  Szálak: Lekérdezéskészlet tétlen szálai  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  QueryPoolJobQueueLength  |  Szálak: Lekérdezéskészlet-feladat várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  Kvóta  |  Memória: Kvóta  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  Kvóta blokkolva  |  Memória: Kvóta letiltva  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  RowsconvertedperSec  |  Feldolgozás: Másodpercenként konvertált sorok  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  RowsreadperSec  |  Feldolgozás: Másodpercenként olvasott sorok  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  RowsWrittenPerSec  |  Feldolgozás: Másodpercenként írt sorok  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ShortParsingBusyThreads  |  Szálak: Rövid elemzés foglalt szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ShortParsingIdleThreads  |  Szálak: Rövid elemzési tétlen szálak  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  ShortParsingJobQueueLength  |  Szálak: Rövid elemzési feladat várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  SuccessfullConnectionsPerSec  |  Sikeres kapcsolatok másodpercenként  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  Összes kapcsolati hiba  |  Csatlakozási hibák összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  Összes kapcsolatkérése  |  Csatlakozási kérelmek összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  VertiPaqNonpaged között  |  Memória: VertiPaq Nonpaged  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  VertiPaqPaged  |  Memória: VertiPaq lapozgatott  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AnalysisServices/kiszolgálók  |  virtual_bytes_metric  |  Felhasznált virtuális memória jelenlegi mérete (bájt)  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Háttérvégpontidőtartama  |  A háttérrendszer-kérelmek időtartama  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ApiManagement/service  |  Kapacitás  |  Kapacitás  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Időtartam  |  Az átjárókérelmek teljes időtartama  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Eldobott EventHub-események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubElutasítottEsemények  |  Elutasított EventHub-események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Sikeres EventHub-események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubThrottledEsemények  |  Szabályozott EventHub-események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  EventHub-események idővel elvégzett eseményei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  EventHub-események mérete  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  EventHub-események összesen  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Sikertelen EventHub-események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Sikertelen kérések  |  Sikertelen átjárókérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Egyébkérések  |  Egyéb átjárókérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Sikeres kérések  |  Sikeres átjárókérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Átjárókérelmek összesen (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ApiManagement/service  |  Jogosulatlan kérések  |  Nem engedélyezett átjárókérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  AppCpuUsageSzázalék  |  Alkalmazás PROCESSZOR-használati százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  AppMemoryCommitted  |  Hozzárendelt alkalmazásmemória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  AppMemoryMax alkalmazás  |  Alkalmazásmemória max.  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  AppMemoryUsed  |  Használt alkalmazásmemória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  GCPauseTotalCount (GCPauseTotalCount)  |  Gc szünetszáma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  GCPauseTotalTime  |  Globális katalógus szüneteltetési teljes ideje  |  Ezredmásodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  MaxOldGenMemoryPoolPoolBytes  |  Maximálisan elérhető régi generációs adatméret  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  OldGenMemoryPoolBytes  |  Régi generációs adatok mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  OldGenPromotedBytes  |  Előléptetés régi generációs adatméretre  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  SystemCpuUsageSzázalék  |  Rendszerprocesszor-használat százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatHibaszám  |  Tomcat globális hiba  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatReceivedBytes (TomcatReceivedBytes)  |  Tomcat teljes fogadott bájt  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatRequestMaxTime  |  Tomcat-kérelem maximális ideje  |  Ezredmásodperc  |  Maximum | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatRequestTotalCount (TomcatRequestTotalCount)  |  Tomcat kérelem teljes száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatRequestTotalTime  |  Tomcat kérelem összes alkalommal  |  Ezredmásodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatResponseAvgTime  |  Tomcat kérelem átlagos ideje  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatSentBytes (TomcatSentBytes)  |  Tomcat összes elküldött bájt  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatSessionActiveCurrentCount  |  Tomcat munkamenet élő száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatSessionActiveMaxCount  |  Tomcat-munkamenet maximális aktív száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatSessionAliveMaxTime  |  Tomcat session max élő idő  |  Ezredmásodperc  |  Maximum | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatSessionCreatedCount  |  Tomcat munkamenet létrehozva száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatSessionExpiredCount  |  Tomcat-munkamenet lejárt száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  TomcatSessionRejectedCount  |  Tomcat munkamenet elutasított száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.AppPlatform/Tavasz  |  YoungGenPromotedBytes  |  Népszerűsítés fiatal generációs adatméretre  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Automation/automationAccounts  |  Teljes feladat  |  Összes feladat  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  A frissítési központi telepítési gép teljes futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  A frissítés telepítési futásai összesen  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  CoreCount (Magszáma)  |  Dedikált magok száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  LétrehozásNodeCount  |  Csomópontszám létrehozása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  IdleNode-számláló  |  Tétlen csomópontok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Feladat teljes eseményeinek törlése  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  FeladatDeleteStartEvent  |  Feladat indítási eseményeinek törlése  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Feladat teljes események letiltása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  FeladatDisableStartEvent  |  Feladat indítási eseményeinek letiltása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  FeladatStartEvent  |  Feladatindítási események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Feladat befejezése – teljes események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  FeladatTerminateStartEvent  |  Feladat indítási eseményeinek leállítása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  KilépéspoolNodeCount  |  Kilépés készletcsomópont-számlálóból  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount (LowPriorityCoreCount)  |  LowPriority magszámláló  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount (OfflineNodeCount)  |  Kapcsolat nélküli csomópontok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Készlet létrehozási események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  Készlettörléscompleteesemény  |  Készlet törlése teljes események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  KészletdeleteStartevent  |  Készlet törlése kezdőesemények  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Készlet átméretezése teljes események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Készlet átméretezése kezdési események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount (PreemptedNodeCount)  |  Preempted csomópontszám  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  ÚjraindításNodeCount  |  A csomópontszám újraindítása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount (ReimagingNodeCount)  |  Csomópontszám újraszámítása  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  RunningNodeCount között  |  Futó csomópontszám  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  StartingNodeCount között  |  Kezdő csomópontszám  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Sikertelen kezdő feladat csomópontszáma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Feladat-befejezési események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Feladat sikertelen események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Feladat indítási eseményei  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount (ÖsszeslowPriorityNodeCount)  |  Alacsony prioritású csomópontszám  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  TotalNodeCount (ÖsszesnodeCount)  |  Dedikált csomópontszám  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  Használhatatlan NodeCount  |  Használhatatlan csomópontszám  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount (Várakozás: StartForStartTaskNodeCount)  |  Várakozás a feladatcsomópont-számláló indítására  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Aktív magok  |  Aktív magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Aktív csomópontok  |  Aktív csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Alapjárati magok  |  Alapjárati magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Tétlen csomópontok  |  Tétlen csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Feladat elvégezve  |  Feladat elvégezve  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Elküldött feladat  |  Elküldött feladat  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Elhagyva magok  |  Elhagyva magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Csomópontok elhagyása  |  Csomópontok elhagyása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Preempted magok  |  Preempted magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Elődpontos csomópontok  |  Elődpontos csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Kvótakihasználtság százaléka  |  Kvótakihasználtság százaléka  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Magok összesen  |  Magok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Összes csomópont  |  Összes csomópont  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Használhatatlan magok  |  Használhatatlan magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.BatchAI/munkaterületek  |  Használhatatlan csomópontok  |  Használhatatlan csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  ConnectionAccepted (Kapcsolat elfogadva)  |  Elfogadott kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  Kapcsolat által kezelt  |  Kezelt kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagepercentageinDouble  |  PROCESSZOR-használat százaléka  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  Io olvasási bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBájtok  |  IO írási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit memóriahatár  |  Memóriakorlát  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  Memóriahasználat  |  Memóriahasználat  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  Memóriahasználatszázalékösszeg  |  Memóriahasználat százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  Függőtranzakciók  |  Függőben lévő tranzakciók  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  Feldolgozott blokkok  |  Feldolgozott blokkok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  Feldolgozott tranzakciók  |  Feldolgozott tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  Várólistán lévő tranzakciók  |  Várólistán lévő tranzakciók  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  Kéréssel elnyújtva  |  Kezelt kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Blockchain/blockchainMembers  |  StorageUsage (Tároláshasználata)  |  Tárhely használat  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-találatok  |  Gyorsítótár-találatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárhits0  |  Gyorsítótár-találatok (0. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-találatok1  |  Gyorsítótár-találatok (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-találatok2  |  Gyorsítótár-találatok (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-találatok3  |  Gyorsítótár-találatok (3. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-találatok4  |  Gyorsítótár-találatok (4. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-találatok5  |  Gyorsítótár-találatok (Shard 5)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótártalálat6  |  Gyorsítótár-találatok (6. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-találatok7  |  Gyorsítótár-találatok (Shard 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-találatok8  |  Gyorsítótár-találatok (8. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótártalálat9  |  Gyorsítótár-találatok (9. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárLátság  |  Gyorsítótár-késleltetés mikroszekundumok (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-tévesztések  |  Gyorsítótár-tévesztések  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses0  |  Gyorsítótár-tévesztések (0. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses1  |  Gyorsítótár-tévesztések (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses2  |  Gyorsítótár-tévesztések (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses3  |  Gyorsítótár-tévesztések (3. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses4  |  Gyorsítótár-tévesztések (4. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses5  |  Gyorsítótár-tévesztések (5. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses6  |  Gyorsítótár-tévesztések (6. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótár-misses7  |  Gyorsítótár-tévesztések (7. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses8  |  Gyorsítótár-tévesztések (8. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótármisses9  |  Gyorsítótár-tévesztések (9.sángold)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead  |  Gyorsítótár olvasása  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead0  |  Gyorsítótár olvasása (0. s.  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead1  |  Gyorsítótár olvasása (1. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead2  |  Gyorsítótár olvasása (2. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead3  |  Gyorsítótár olvasása (3. shard)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead4  |  Gyorsítótár olvasása (4. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead5  |  Gyorsítótár olvasása (5. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead6  |  Gyorsítótár olvasása (6. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead7  |  Gyorsítótár olvasása (7. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead8  |  Gyorsítótár olvasása (8. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheRead9  |  Gyorsítótár olvasása (9. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárírás  |  Gyorsítótár írása  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheWrite0  |  Gyorsítótár írása (0. shard)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheWrite1  |  Gyorsítótár írása (1. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  cacheWrite2  |  Gyorsítótár írása (2. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárÍrás3  |  Gyorsítótár írása (3. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárWrite4  |  Gyorsítótár írása (4. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárÍrás5  |  Gyorsítótár írása (5. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárÍrás6  |  Gyorsítótár írása (6. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárÍrás7  |  Gyorsítótár írása (Shard 7)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárÍrás8  |  Gyorsítótár írása (8. szegmens)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  gyorsítótárÍrás9  |  Gyorsítótár írása (Shard 9)  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients (connectedclients)  |  Csatlakozott ügyfelek  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients0  |  Csatlakoztatott ügyfelek (0. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients1  |  Csatlakoztatott ügyfelek (1. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients2  |  Csatlakoztatott ügyfelek (2. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients3  |  Csatlakoztatott ügyfelek (3. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients4  |  Csatlakoztatott ügyfelek (4. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients5  |  Csatlakoztatott ügyfelek (5. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients6  |  Csatlakoztatott ügyfelek (6. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients7  |  Csatlakoztatott ügyfelek (Shard 7)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients8  |  Csatlakoztatott ügyfelek (8. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  connectedclients9  |  Csatlakoztatott ügyfelek (9. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  Hibák  |  Hibák  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilakoltatott kulcsok  |  Kilakoltatott kulcsok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilaktatotik0  |  Kilakoltatott kulcsok (0. shard)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilaktatotik1  |  Kilakoltatott kulcsok (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilakoltatott kulcsok2  |  Kilakoltatott kulcsok (2. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilakoltatott kulcsok3  |  Kilakoltatott kulcsok (3. shard)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilaktatotik4  |  Kilakoltatott kulcsok (4. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilaktatotik5  |  Kilakoltatott kulcsok (5. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilaktatotik6  |  Kilakoltatott kulcsok (6. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilakoltattákkeys7  |  Kilakoltatott kulcsok (Shard 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilakoltatott kulcsok8  |  Kilakoltatott kulcsok (8. szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kilakoltatott kulcsok9  |  Kilakoltatott kulcsok (9. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok  |  Lejárt kulcsok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok0  |  Lejárt kulcsok (0. shard)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok1  |  Lejárt kulcsok (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok2  |  Lejárt kulcsok (2. shard)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok3  |  Lejárt kulcsok (3. shard)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok4  |  Lejárt kulcsok (4. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok5  |  Lejárt kulcsok (5. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok6  |  Lejárt kulcsok (Shard 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok7  |  Lejárt kulcsok (Shard 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok8  |  Lejárt kulcsok (8. shard)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  lejárt kulcsok9  |  Lejárt kulcsok (Shard 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getparancsok  |  Kap  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands0  |  Gets (Shard 0)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Shard 1)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands2  |  Gets (Shard 2)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands3  |  Gets (Shard 3)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands4  |  Gets (Shard 4)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands5  |  Gets (Shard 5)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands6  |  Gets (Shard 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands7  |  Gets (Shard 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands8  |  Gets (Shard 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  getcommands9  |  Gets (Shard 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond  |  Műveletek másodpercenként  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond0  |  Műveletek másodpercenként (0. sánda)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond1  |  Műveletek másodpercenként (1. sánda)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond2  |  Műveletek másodpercenként (2. sánda)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond3  |  Műveletek másodpercenként (3. shard)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond4  |  Műveletek másodpercenként (4. sánda)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond5  |  Műveletek másodpercenként (5. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond6  |  Műveletek másodpercenként (Shard 6)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond7  |  Műveletek másodpercenként (Shard 7)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond8  |  Műveletek másodpercenként (Shard 8)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  műveletekPerSecond9  |  Műveletek másodpercenként (Shard 9)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime  |  CPU  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (Shard 0)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime1  |  CPU (1. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime2  |  CPU (Shard 2)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime3  |  CPU (Szilánk 3)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime4  |  CPU (Shard 4)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime5  |  CPU (Shard 5)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime6  |  CPU (Shard 6)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime7  |  CPU (Shard 7)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime8  |  CPU (Shard 8)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  százalékProcessorTime9  |  CPU (Shard 9)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kiszolgálóbetöltése  |  Kiszolgáló betöltése  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad0  |  Kiszolgáló betöltése (0. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad1  |  Kiszolgáló betöltése (1. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad2  |  Kiszolgáló betöltése (2. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad3  |  Kiszolgáló betöltése (3. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad4  |  Kiszolgáló betöltése (4. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad5  |  Kiszolgáló betöltése (5. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad6  |  Kiszolgáló betöltése (6. shard)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad7  |  Kiszolgáló betöltése (Shard 7)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  serverLoad8  |  Kiszolgáló betöltése (8. szegmens)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  kiszolgálóLoad9  |  Kiszolgáló betöltése (Shard 9)  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok  |  Halmazok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setcommands0  |  Készletek (Shard 0)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok1  |  Készletek (Shard 1)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok2  |  Készletek (Shard 2)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok3  |  Készletek (Shard 3)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok4  |  Készletek (Shard 4)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok5  |  Készletek (Shard 5)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok6  |  Készletek (Shard 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok7  |  Készletek (Shard 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok8  |  Készletek (Shard 8)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  setparancsok9  |  Készletek (Shard 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancs feldolgozva  |  Összes művelet  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozása00  |  Összes művelet (0. sángold)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás1  |  Összes művelet (1. szegmens)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás2  |  Összes művelet (2. shard)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás3  |  Összes művelet (3. shard)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás4  |  Összes művelet (4. szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás5  |  Összes művelet (5. szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás6  |  Összes művelet (Shard 6)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás7  |  Összes művelet (Shard 7)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás8  |  Összes művelet (8. szilánk)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes parancsfeldolgozás9  |  Összes művelet (Shard 9)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes kulcs  |  Összes kulcs  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összeskulcs0  |  Összes kulcs (0. sángold)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összes kulcs1  |  Összes kulcs (1. szegmens)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összesenkeys2  |  Összes kulcs (2. szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összesenkeys3  |  Összes kulcs (3. sánda)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összesen4  |  Összes kulcs (4. szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összesen5  |  Összes kulcs (5. szilánk)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összesen6  |  Összes kulcs (Shard 6)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összesen7  |  Összes kulcs (Shard 7)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összesen8  |  Összes kulcs (Shard 8)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  összesenkeys9  |  Összes kulcs (Shard 9)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használt memória  |  Használt memória  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória0  |  Használt memória (0. shard)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória1  |  Használt memória (1. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória2  |  Használt memória (2. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória3  |  Használt memória (3. shard)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória4  |  Használt memória (4. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória5  |  Használt memória (5. szegmens)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória6  |  Használt memória (Shard 6)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória7  |  Használt memória (Shard 7)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória8  |  Használt memória (Shard 8)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemória9  |  Használt memória (Shard 9)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemóriaszázalék  |  Használt memória százaléka  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használt memoryRss  |  Használt memória RSS  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss0  |  Használt memória RSS (Shard 0)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss1  |  Használt memória RSS (Shard 1)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss2  |  Használt memória RSS (Shard 2)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss3  |  Használt memória RSS (Shard 3)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss4  |  Használt memória RSS (Shard 4)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss5  |  Használt memória RSS (Shard 5)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss6  |  Használt memória RSS (Shard 6)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss7  |  Használt memória RSS (Shard 7)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss8  |  Használt memória RSS (Shard 8)  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Cache/redis  |  használtmemoryRss9  |  Használt memória RSS (Shard 9)  |  Bájt  |  Maximum | 
| Nem  | Nem |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Lemez olvasási bájtjai/mp  |  Lemez olvasása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Lemezolvasási műveletek/mp  |  Lemezolvasási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Lemezírási bájtok/mp  |  Lemez írása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Lemezírási műveletek/mp  |  Lemezírási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Bejövő hálózat  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Kimenő hálózat  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicCompute/virtualMachines  |  Lemez olvasási bájtjai/mp  |  Lemez olvasása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/virtualMachines  |  Lemezolvasási műveletek/mp  |  Lemezolvasási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicCompute/virtualMachines  |  Lemezírási bájtok/mp  |  Lemez írása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/virtualMachines  |  Lemezírási műveletek/mp  |  Lemezírási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/virtualMachines  |  Bejövő hálózat  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/virtualMachines  |  Kimenő hálózat  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicCompute/virtualMachines  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts  |  Használtkapacitás  |  Használt kapacitás  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapacity kapacitás  |  Blob kapacitása  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount (BlobCount)  |  Blobok száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Konténerszám  |  Blob-tárolók száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Indexkapacitás  |  Index kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Fájlkapacitás  |  Fájl kapacitása  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Fájlszám  |  Fájlok száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Fájlmegosztások száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileSharequota fájlmegosztás  |  Fájlmegosztási kvóta mérete  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Fájlmegosztáspillanatkép száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Fájlmegosztássnapshotmérete  |  Fájlmegosztás pillanatképének mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Várólistakapacitás  |  Várólista kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Várólistaszáma  |  Várólista száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount (Üzenetsor)  |  Várólista-üzenetek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Táblakapacitás  |  Tábla kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Tábladarabszám  |  Táblaszáma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Táblaentitásszáma  |  Táblaentitások száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Blokkolt hívások  |  Blokkolt hívások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Karakterekbe képezve  |  Betanított karakterek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Lefordított karakterek  |  Lefordított karakterek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Ügyfélhibák  |  Ügyfélhibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Datain között  |  Adatok a  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  DataOut (Adatki- és a  |  Adatok ki  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Késés  |  Késés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Kiszolgálóhibák  |  Kiszolgálói hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  SpeechSessionDuration  |  Beszédmunkamenet időtartama  |  Másodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Sikeres hívások  |  Sikeres hívások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Összes hívás  |  Összes hívás  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Összes hiba  |  Összes hiba  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  TotalTokenCalls  |  Tokenhívások összesen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.CognitiveServices/fiókok  |  Összes tranzakció  |  Összes tranzakció  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Cpu-kreditek felhasznált  |  Cpu-kreditek felhasznált  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Fennmaradó CPU-kreditek  |  Fennmaradó CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez várakozási sorának mélysége  |  Adatlemez-várólista mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez olvasási bájtjai/mp  |  Adatlemez olvasási bájtjai/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez olvasási műveletei/mp  |  Adatlemez olvasási műveletei/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez írási bájt/mp  |  Adatlemez írási bájtjai/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Adatlemez írási műveletei/mp  |  Adatlemez írási műveletei/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezolvasási bájtok  |  Lemezolvasási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezolvasási műveletek/mp  |  Lemezolvasási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezírási bájtok  |  Lemezírási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezírási műveletek/mp  |  Lemezírási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Bejövő forgalomfolyamok  |  Bejövő forgalomfolyamok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Bejövő folyamatok maximális létrehozási aránya  |  Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Bejövő hálózat  |  Hálózat számlázható (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Hálózat összesen  |  Hálózat összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Kimenő hálózat  |  Hálózat számlázható (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Hálózat kiteljesedése összesen  |  Hálózat kiteljesedése összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  OS-lemez várakozási sorának mélysége  |  Az operációs rendszer lemezvárólistájának mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer lemezolvasási bájtjai/mp  |  Operációs rendszer lemezolvasási bájtjainak másodpercenkénti száma (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer lemezolvasási műveletei/mp  |  Operációs rendszer lemezolvasási műveleteinek másodpercenkénti száma (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer lemezének írása bájt/mp  |  Operációs rendszer lemezének írása másodpercenkénti bájtjai (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer lemezírási műveletei/mp  |  Operációs rendszer lemezírási műveleteinek/mp-je (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer lemezenként – QD  |  Operációs rendszer lemezének qd-ja (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Az operációs rendszer lemezenkénti olvasási bájt/mp  |  Operációs rendszer lemezolvasási bájtjai/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Az operációs rendszer lemezenkénti olvasási műveletei/mp  |  Operációs rendszer lemezolvasási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer lemezenként i.e.  |  Operációs rendszer lemezírási bájtjai/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Operációs rendszer lemezenkéntírási műveletek másodpercenkénti száma  |  Operációs rendszer lemezírási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Kimenő folyamatok  |  Kimenő folyamatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Kimenő folyamatok maximális létrehozási aránya  |  Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezenkénti qd  |  Adatlemez –qd (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezolvasási bájt/mp  |  Adatlemez olvasási bájt/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezolvasási műveletek/mp  |  Adatlemez olvasási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezenkénti írási bájt/mp  |  Adatlemez írási bájt/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Lemezírási műveletek/mp  |  Adatlemez írási műveletei/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Prémium szintű adatlemez gyorsítótárának olvasási találata  |  Prémium szintű adatlemez gyorsítótárának olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Prémium szintű adatlemez gyorsítótárolvasási tévesztése  |  Prémium szintű adatlemez gyorsítótárolvasási tévesztés (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Prémium szintű operációsrendszer-gyorsítótár olvasási találata  |  Prémium szintű operációsrendszer-gyorsítótár olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachines  |  Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése  |  Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Cpu-kreditek felhasznált  |  Cpu-kreditek felhasznált  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Fennmaradó CPU-kreditek  |  Fennmaradó CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez várakozási sorának mélysége  |  Adatlemez-várólista mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez olvasási bájtjai/mp  |  Adatlemez olvasási bájtjai/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez olvasási műveletei/mp  |  Adatlemez olvasási műveletei/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez írási bájt/mp  |  Adatlemez írási bájtjai/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Adatlemez írási műveletei/mp  |  Adatlemez írási műveletei/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezolvasási bájtok  |  Lemezolvasási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezolvasási műveletek/mp  |  Lemezolvasási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezírási bájtok  |  Lemezírási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezírási műveletek/mp  |  Lemezírási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Bejövő forgalomfolyamok  |  Bejövő forgalomfolyamok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Bejövő folyamatok maximális létrehozási aránya  |  Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Bejövő hálózat  |  Hálózat számlázható (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Hálózat összesen  |  Hálózat összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Kimenő hálózat  |  Hálózat számlázható (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Hálózat kiteljesedése összesen  |  Hálózat kiteljesedése összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  OS-lemez várakozási sorának mélysége  |  Az operációs rendszer lemezvárólistájának mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer lemezolvasási bájtjai/mp  |  Operációs rendszer lemezolvasási bájtjainak másodpercenkénti száma (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer lemezolvasási műveletei/mp  |  Operációs rendszer lemezolvasási műveleteinek másodpercenkénti száma (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer lemezének írása bájt/mp  |  Operációs rendszer lemezének írása másodpercenkénti bájtjai (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer lemezírási műveletei/mp  |  Operációs rendszer lemezírási műveleteinek/mp-je (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer lemezenként – QD  |  Operációs rendszer lemezének qd-ja (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Az operációs rendszer lemezenkénti olvasási bájt/mp  |  Operációs rendszer lemezolvasási bájtjai/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Az operációs rendszer lemezenkénti olvasási műveletei/mp  |  Operációs rendszer lemezolvasási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer lemezenként i.e.  |  Operációs rendszer lemezírási bájtjai/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Operációs rendszer lemezenkéntírási műveletek másodpercenkénti száma  |  Operációs rendszer lemezírási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Kimenő folyamatok  |  Kimenő folyamatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Kimenő folyamatok maximális létrehozási aránya  |  Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezenkénti qd  |  Adatlemez –qd (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezolvasási bájt/mp  |  Adatlemez olvasási bájt/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezolvasási műveletek/mp  |  Adatlemez olvasási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezenkénti írási bájt/mp  |  Adatlemez írási bájt/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Lemezírási műveletek/mp  |  Adatlemez írási műveletei/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Prémium szintű adatlemez gyorsítótárának olvasási találata  |  Prémium szintű adatlemez gyorsítótárának olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Prémium szintű adatlemez gyorsítótárolvasási tévesztése  |  Prémium szintű adatlemez gyorsítótárolvasási tévesztés (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Prémium szintű operációsrendszer-gyorsítótár olvasási találata  |  Prémium szintű operációsrendszer-gyorsítótár olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets  |  Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése  |  Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Cpu-kreditek felhasznált  |  Cpu-kreditek felhasznált  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fennmaradó CPU-kreditek  |  Fennmaradó CPU-kreditek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Adatlemez várakozási sorának mélysége  |  Adatlemez-várólista mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Adatlemez olvasási bájtjai/mp  |  Adatlemez olvasási bájtjai/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Adatlemez olvasási műveletei/mp  |  Adatlemez olvasási műveletei/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Adatlemez írási bájt/mp  |  Adatlemez írási bájtjai/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Adatlemez írási műveletei/mp  |  Adatlemez írási műveletei/mp (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezolvasási bájtok  |  Lemezolvasási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezolvasási műveletek/mp  |  Lemezolvasási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezírási bájtok  |  Lemezírási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezírási műveletek/mp  |  Lemezírási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bejövő forgalomfolyamok  |  Bejövő forgalomfolyamok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bejövő folyamatok maximális létrehozási aránya  |  Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bejövő hálózat  |  Hálózat számlázható (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Hálózat összesen  |  Hálózat összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Kimenő hálózat  |  Hálózat számlázható (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Hálózat kiteljesedése összesen  |  Hálózat kiteljesedése összesen  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS-lemez várakozási sorának mélysége  |  Az operációs rendszer lemezvárólistájának mélysége (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer lemezolvasási bájtjai/mp  |  Operációs rendszer lemezolvasási bájtjainak másodpercenkénti száma (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer lemezolvasási műveletei/mp  |  Operációs rendszer lemezolvasási műveleteinek másodpercenkénti száma (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer lemezének írása bájt/mp  |  Operációs rendszer lemezének írása másodpercenkénti bájtjai (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer lemezírási műveletei/mp  |  Operációs rendszer lemezírási műveleteinek/mp-je (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer lemezenként – QD  |  Operációs rendszer lemezének qd-ja (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Az operációs rendszer lemezenkénti olvasási bájt/mp  |  Operációs rendszer lemezolvasási bájtjai/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Az operációs rendszer lemezenkénti olvasási műveletei/mp  |  Operációs rendszer lemezolvasási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer lemezenként i.e.  |  Operációs rendszer lemezírási bájtjai/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operációs rendszer lemezenkéntírási műveletek másodpercenkénti száma  |  Operációs rendszer lemezírási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Kimenő folyamatok  |  Kimenő folyamatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Kimenő folyamatok maximális létrehozási aránya  |  Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezenkénti qd  |  Adatlemez –qd (elavult)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezolvasási bájt/mp  |  Adatlemez olvasási bájt/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezolvasási műveletek/mp  |  Adatlemez olvasási műveletei/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezenkénti írási bájt/mp  |  Adatlemez írási bájt/mp (elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lemezírási műveletek/mp  |  Adatlemez írási műveletei/mp(elavult)  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Prémium szintű adatlemez gyorsítótárának olvasási találata  |  Prémium szintű adatlemez gyorsítótárának olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Prémium szintű adatlemez gyorsítótárolvasási tévesztése  |  Prémium szintű adatlemez gyorsítótárolvasási tévesztés (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Prémium szintű operációsrendszer-gyorsítótár olvasási találata  |  Prémium szintű operációsrendszer-gyorsítótár olvasási találata (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése  |  Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése (előzetes verzió)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage (CpuUsage)  |  Processzorhasználat  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ContainerInstance/containerGroups  |  Memóriahasználat  |  Memóriahasználat  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Másodpercenként fogadott hálózati bájtok  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Másodpercenként küldött hálózati bájtok  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ContainerRegistry/regisztika  |  RunDuration  |  Futtatás időtartama  |  Ezredmásodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.ContainerRegistry/regisztika  |  SuccessfulPullCount  |  Sikeres lekéréses szám  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ContainerRegistry/regisztika  |  SuccessfulPushCount  |  Sikeres leküldéses szám  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ContainerRegistry/regisztika  |  TotalPullCount (Összes pullcount)  |  Teljes lekérések száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.ContainerRegistry/regisztika  |  ÖsszesenPushCount  |  Összes leküldéses szám  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Felügyelt fürtben rendelkezésre álló processzormagok teljes száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Felügyelt fürtben rendelkezésre álló memória teljes mennyisége  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Különböző csomóponti feltételek állapotai  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  A hüvelyek száma fázisonként  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Podok száma kész állapotban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Elérhető kapacitás  |  Rendelkezésre álló kapacitás  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Feltöltött felhőalapú bájtok (eszköz)  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Feltöltött felhőalapú bájtok (megosztás)  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput (Felhőolvasás - Átput  |  Felhőbeli letöltési átviteli  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Felhőbeli letöltési átviteli (megosztás)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput (Felhőfeltöltési átput)  |  Felhőbeli feltöltési átviteli  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Felhőbeli feltöltési átviteli -átmenő (megosztás)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryKitilizáció  |  Peremhálózati számítás – Memóriahasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorKitilizálás  |  Peremhálózati számítás – százalékos CPU  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Olvasási átviteli -átmenő (hálózat)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Írási átviteli -áteresztő (hálózat)  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Teljes kapacitás  |  Teljes kapacitás  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataFactory/datafactories  |  Sikertelen futtatások  |  Sikertelen futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns (Sikeres futtatások)  |  Sikeres futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  TevékenységÉrvénytelenítvefuttatások  |  A megszakított tevékenység futtatási mutatói  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  ActivityFailedRuns  |  A sikertelen tevékenység mérőszámokat futtat  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  ActivitySucceededRuns között  |  A sikeres tevékenység mérőszámait futtatja  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  FactorySizeIngbunits  |  Teljes gyárméret (GB egység)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  IntegrationRuntimeAvailableMemory  |  Integrációs futásidejű elérhető memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  IntegrationRuntimeAverageTaskPickupDelay  |  Integrációs futásidejű várólista időtartama  |  Másodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  IntegrationRuntimeCpuSzázalék  |  Integrációs futásidejű CPU-kihasználtság  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  IntegrationRuntimeQueueLength  |  Integrációs futásidejű várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  MaxallowedfactorySizeIngbunits  |  A gyár maximálisan megengedett mérete (GB egység)  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  MaxAllowedResourceCount (MaxAllowedResourceCount)  |  A megengedett entitások maximális száma  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  PipelineCancelledRuns  |  Megszakított folyamatfuttatási mérőszámok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  PipelineFailedRuns  |  Sikertelen folyamat futtatási metrikák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  PipelineSucceededRuns között  |  Sikeres folyamatfuttatási metrikák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  Erőforrásszáma  |  Entitások száma összesen  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  TriggerCancelledRuns  |  Megszakított eseményindító-futtatási mérőszámok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  TriggerFailedRuns  |  Sikertelen eseményindító futtatási mérőszámai  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataFactory/gyárak  |  Aktiválássucceededruns  |  Sikeres eseményindító-futtatási mérőszámok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeAnalytics/fiókok  |  JobAUEndedTörölve  |  Megszakított AU idő  |  Másodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeAnalytics/fiókok  |  JobAUEndedFailure  |  Sikertelen AU idő  |  Másodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeAnalytics/fiókok  |  JobAUEndedsiker  |  Sikeres AU idő  |  Másodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeAnalytics/fiókok  |  JobEndedTörölve  |  Törölt feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeAnalytics/fiókok  |  JobEndedFailure (Feladat– Hiba)  |  Sikertelen feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeAnalytics/fiókok  |  JobEndedSiker  |  Sikeres feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeStore/fiókok  |  DataRead (Olvasd el)  |  Adatok olvasása  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeStore/fiókok  |  Adatokírás  |  Írt adatok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeStore/fiókok  |  Olvasáskérések  |  Olvasási kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DataLakeStore/fiókok  |  TotalStorage (Összes tárhely) tárolása  |  Összes tárhely  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DataLakeStore/fiókok  |  WriteRequests (Íráskérések)  |  Írási kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  active_connections  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  backup_storage_used  |  Használt biztonsági másolat tárolása  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  connections_failed  |  Sikertelen kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  cpu_percent  |  PROCESSZOR százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  io_consumption_percent  |  IO százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  memory_percent  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  network_bytes_egress  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  network_bytes_ingress  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  seconds_behind_master  |  Replikációs késleltetés másodpercben  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  serverlog_storage_limit  |  Kiszolgálónapló tárolási korlátja  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  serverlog_storage_percent  |  Kiszolgálói napló tárolási százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  serverlog_storage_usage  |  Használt kiszolgálónapló-tároló  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  storage_limit  |  Tárolási korlát  |  Bájt  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  storage_percent  |  Tárolási százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMariaDB/kiszolgálók  |  storage_used  |  Használt tároló  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  active_connections  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  backup_storage_used  |  Használt biztonsági másolat tárolása  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  connections_failed  |  Sikertelen kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  cpu_percent  |  PROCESSZOR százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  io_consumption_percent  |  IO százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  memory_percent  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  network_bytes_egress  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  network_bytes_ingress  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  seconds_behind_master  |  Replikációs késleltetés másodpercben  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  serverlog_storage_limit  |  Kiszolgálónapló tárolási korlátja  |  Bájt  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  serverlog_storage_percent  |  Kiszolgálói napló tárolási százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  serverlog_storage_usage  |  Használt kiszolgálónapló-tároló  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  storage_limit  |  Tárolási korlát  |  Bájt  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  storage_percent  |  Tárolási százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DBforMySQL/kiszolgálók  |  storage_used  |  Használt tároló  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  active_connections  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  backup_storage_used  |  Használt biztonsági másolat tárolása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  connections_failed  |  Sikertelen kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  cpu_percent  |  PROCESSZOR százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  io_consumption_percent  |  IO százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  memory_percent  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  network_bytes_egress  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  network_bytes_ingress  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  pg_replica_log_delay_in_bytes  |  Maximális késés a replikák között  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  pg_replica_log_delay_in_seconds  |  Replika késés  |  Másodperc  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  serverlog_storage_limit  |  Kiszolgálónapló tárolási korlátja  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  serverlog_storage_percent  |  Kiszolgálói napló tárolási százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  serverlog_storage_usage  |  Használt kiszolgálónapló-tároló  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  storage_limit  |  Tárolási korlát  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  storage_percent  |  Tárolási százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/szerverek  |  storage_used  |  Használt tároló  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Aktív kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  PROCESSZOR százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/serversv2  |  iops  |  IOPS  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Tárolási százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Használt tároló  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/Fiók  |  digitaltwins.telemetry.csomópontok  |  Digitális Twins csomópont telemetriai helyőrző  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  C2D üzenetek elhagyva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  A C2D-üzenetek kézbesítése befejeződött  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Elutasított C2D-üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Sikertelen közvetlen metódus-meghívások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestMéret  |  A közvetlen metódusmetódusok kérésének mérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseMéret  |  A közvetlen metódusmetódusok válaszmérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Sikeres közvetlen metódus-meghívások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Sikertelen ikerolvasás háttérből  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  A háttérből beolvasások válaszmérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Sikeres ikerolvasás háttérből  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Sikertelen ikerfrissítések a háttérrendszerből  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Két frissítés mérete háttérrendszerről  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Sikeres ikerfrissítések a háttérrendszerből  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  C2D üzenetek lejárt (előzetes verzió)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  Konfigurációk  |  Konfigurációs mutatók  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Devices/IotHubs  |  connectedDeviceCount (connectedDeviceCount)  |  Csatlakoztatott eszközök (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Útválasztás: üzenetekbe/eseményekre kézbesített üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Útválasztás: az Event Hubba kézbesített üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Útválasztás: a Service Bus várólistájába kézbesített üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Útválasztás: a Service Bus topic szolgáltatásba küldött üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Útválasztás: a tárolóba kézbesített üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobok  |  Útválasztás: a tárolóba szállított blobok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Útválasztás: a tárolóba szállított adatok  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Útválasztás: üzenetek/események üzenetkéste  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Útválasztás: üzenetkésés az Eseményközpontszámára  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Útválasztás: üzenetkésa Service Bus-várólistához  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Útválasztás: üzenetkésés a Service Bus-témakörhöz  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Útválasztás: üzenet késése a tároláshoz  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.telemettry.egress.dropped  |  Útválasztás: eldobott telemetriai üzenetek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.telemettry.egress.fallback  |  Útválasztás: tartalékba küldött üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.telemettry.egress.invalid  |  Útválasztás: a telemetriai üzenetek nem kompatibilisek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.telemettry.egress.orphaned  |  Útválasztás: telemetriai üzenetek árva   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.telemettry.egress.success  |  Útválasztás: telemetriai üzenetek kézbesítve  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.telemettry.ingress.allProtocol  |  Telemetriai üzenet küldési kísérletei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.telemettry.ingress.sendThrottle  |  Szabályozási hibák száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Elküldött telemetriai üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Nem sikerült ikerbeolvasás az eszközökről  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.méret  |  Az eszközökről beolvasások válaszmérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Sikeres ikerolvasás eszközökről  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Nem sikerült ikerfrissítés az eszközökről  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Az eszközökről származó ikerfrissítések mérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Sikeres ikerfrissítések az eszközökről  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaHasznált  |  A felhasznált üzenetek száma összesen  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Az eszköz összes adathasználata  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Az eszköz teljes adathasználata (előzetes verzió)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Csatlakoztatott eszközök (elavult)   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Összes eszköz (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  EventGridDeliveres  |  Eseményrács-szállítások(előzetes verzió)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Eseményrács késése (előzetes verzió)  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Sikertelen feladatmegszakítások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Sikeres feladattörlések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.befejezett  |  Befejezett feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Metódusmeghívási feladatok sikertelen létrehozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  A metódus-meghívási feladatok sikeres létrehozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Két frissítési feladat sikertelen létrehozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Két frissítési feladat sikeres létrehozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Sikertelen feladatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Sikertelen hívások a feladatok listázásához  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Sikeres hívások a feladatok listázásához  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.query.failure  |  Sikertelen feladatlekérdezések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  jobs.query.success  |  Sikeres feladatlekérdezések  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Devices/IotHubs  |  totalDeviceCount (összeseszközszám)  |  Eszközök összesen (előzetes verzió)  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Sikertelen ikerlekérdezések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Ikerlekérdezések eredménymérete  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Sikeres ikerlekérdezések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/provisioningServices  |  Tanúsítványkísérletek  |  Igazolási kísérletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments -ek  |  Hozzárendelt eszközök  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Devices/provisioningServices  |  Regisztrációkísérletek  |  Regisztrációs kísérletek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage (Elérhető tárhely)  |  Rendelkezésre álló tár  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Cassandra kapcsolat lezárások  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestTöltések  |  Cassandra kérelmek díjak  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  CassandraKéri  |  Cassandra kérések  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  Adathasználat  |  Adathasználat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount (Dokumentumdarab)  |  Dokumentumok száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota (Dokumentumquota)  |  Dokumentumkvóta  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  Indexhasználat  |  Index használata  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  Metaadatkérések  |  Metaadat-kérelmek  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Mongo kérelem díj  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.DocumentDB/databaseAccounts  |  MongoKérések  |  Mongo kérések  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Mongo kérelem aránya  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Mongo törlési kérelem aránya  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsBeszúrás  |  Mongo kérelembehelyezési arány  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Mongo lekérdezési kérelmek aránya  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsFrissítés  |  Mongo frissítési kérelmek aránya  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  Kiépítettátviteli  |  Kiosztott átviteli sebesség  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  ReplikációLatency  |  P99 replikációs késés  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.DocumentDB/databaseAccounts  |  Szolgáltatáselérhetősége  |  A szolgáltatás elérhetősége  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Összes kérelem  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Összes kérelemegység  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EnterpriseKnowledgeGraph/szolgáltatások  |  Hibaszáma  |  Hibásak száma  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft.EnterpriseKnowledgeGraph/szolgáltatások  |  Sikerszáma  |  Sikeres műveletek száma  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft.EnterpriseKnowledgeGraph/szolgáltatások  |  SuccessLatency (Káttaláta)  |  Sikeres késés  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.EnterpriseKnowledgeGraph/szolgáltatások  |  Tranzakciószáma  |  Tranzakciók száma  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/tartományok  |  DeadLetteredCount (HalottletteredCount)  |  Kézbesített levéllel írt események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventGrid/tartományok  |  DeliveryAttemptFailCount  |  Sikertelen kézbesítési események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/tartományok  |  DeliverySuccessCount (DeliverySuccessCount)  |  Kézbesített események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventGrid/tartományok  |  DestinationProcessingDurationinms  |  Cél feldolgozási időtartama  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/tartományok  |  DroppedEventCount (Ejtési eseményszám)  |  Eldobott események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/tartományok  |  MatchedEventCount (Egyezésű eseményszám)  |  Egyező események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/tartományok  |  PublishFailCount  |  Sikertelen események közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/tartományok  |  PublishSuccessCount (Közzétételsikerszáma)  |  Közzétett események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/tartományok  |  KözzétételSikerLatencyInM-ek  |  Sikeres késés közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount (HalottletteredCount)  |  Kézbesített levéllel írt események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Sikertelen kézbesítési események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount (DeliverySuccessCount)  |  Kézbesített események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationinms  |  Cél feldolgozási időtartama  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount (Ejtési eseményszám)  |  Eldobott események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount (Egyezésű eseményszám)  |  Egyező események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/extensionTémakörök  |  PublishFailCount  |  Sikertelen események közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/extensionTémakörök  |  PublishSuccessCount (Közzétételsikerszáma)  |  Közzétett események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/extensionTémakörök  |  KözzétételSikerLatencyInM-ek  |  Sikeres késés közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/extensionTémakörök  |  Nem egyező eseményszám  |  Páratlan események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/témakörök  |  PublishFailCount  |  Sikertelen események közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/témakörök  |  PublishSuccessCount (Közzétételsikerszáma)  |  Közzétett események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/témakörök  |  KözzétételSikerLatencyInM-ek  |  Sikeres késés közzététele  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventGrid/témakörök  |  Nem egyező eseményszám  |  Páratlan események  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  Elérhető memória  |  Szabad memória  |  Százalék  |  Maximum | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  CaptureBacklog (Rögzítési backlog)  |  Elfog hátralék.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  Rögzítettbájt  |  Rögzített bájtok.  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  CapturedMessages (Rögzített üzenetek)  |  Rögzített üzenetek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  Kapcsolatok zárva  |  Kapcsolatok zárva.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  Kapcsolatok megnyitva  |  Kapcsolatok megnyitva.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.EventHub/clusters  |  Bejövő bájt  |  Bejövő bájtok.  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/clusters  |  Bejövő üzenetek  |  Bejövő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/clusters  |  Bejövő kérések  |  Bejövő kérések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/clusters  |  Kimenő bájtok  |  Kimenő bájtok.  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/clusters  |  Kimenő üzenetek  |  Kimenő üzenetek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  Kvótatúllépéshibák  |  A kvóta túllépte a hibákat.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  Kiszolgálóhibák  |  Kiszolgálói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  Sikeres kérések  |  Sikeres kérelmek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Szabályozott kérelmek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/clusters  |  Felhasználói hibák  |  Felhasználói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  ActiveConnections  |  ActiveConnections  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  CaptureBacklog (Rögzítési backlog)  |  Elfog hátralék.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  Rögzítettbájt  |  Rögzített bájtok.  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  CapturedMessages (Rögzített üzenetek)  |  Rögzített üzenetek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  Kapcsolatok zárva  |  Kapcsolatok zárva.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  Kapcsolatok megnyitva  |  Kapcsolatok megnyitva.  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHABL  |  Hátraléküzenetek archiválása (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHAMBS  |  Archiválási üzenet átviteli -átmenő (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHAMSGS között  |  Archiválási üzenetek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHINBÁJT  |  Bejövő bájtok (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHINMBS  |  Bejövő bájtok (elavult) (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHINMSGS  |  Bejövő üzenetek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHOUTBYTES (EHOUTBÁJT)  |  Kimenő bájtok (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHOUTMBS  |  Kimenő bájtok (elavult) (elavult)  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  EHOUTMSGS  |  Kimenő üzenetek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  FailreQ között  |  Sikertelen kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  Bejövő bájt  |  Bejövő bájtok.  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  Bejövő üzenetek  |  Bejövő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  Bejövő kérések  |  Bejövő kérések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  INMSGS  |  Bejövő üzenetek (elavult) (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  InreQs  |  Bejövő kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  INTERR  |  Belső kiszolgálói hibák (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  MISCERR  |  Egyéb hibák (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  Kimenő bájtok  |  Kimenő bájtok.  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  Kimenő üzenetek  |  Kimenő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  OUTMSGS  |  Kimenő üzenetek (elavult) (elavult)  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  Kvótatúllépéshibák  |  A kvóta túllépte a hibákat.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  Kiszolgálóhibák  |  Kiszolgálói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  Méret  |  Méret  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  Sikeres kérések  |  Sikeres kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  SUCCREQ között  |  Sikeres kérelmek (elavult)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.EventHub/névterek  |  SVRBSY között  |  Kiszolgáló foglaltsági hibái (elavult)  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  ThrottledRequests  |  Szabályozott kérelmek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.EventHub/névterek  |  Felhasználói hibák  |  Felhasználói hibák.  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.HDInsight/fürtök  |  KategorizáltGatewayRequests  |  Kategorizált átjárókérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.HDInsight/fürtök  |  GatewayRequests  |  Átjárókérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.HDInsight/fürtök  |  NumActiveWorkers  |  Aktív munkavállalók száma  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.HDInsight/fürtök  |  Méretezési kérések  |  Kérelmek méretezése  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Insights/AutoscaleSettings  |  Metrikus küszöbérték  |  Metrikaküszöbérték  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/AutoscaleSettings  |  Megfigyeltkapacitás  |  Megfigyelt kapacitás  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue érték  |  Megfigyelt metrikus érték  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsinitiated  |  Kezdeményezett műveletek méretezése  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  availabilityResults/availabilitySzázalék  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Insights/Components  |  availabilityResults/count  |  Rendelkezésre állási tesztek  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  availabilityEredmények/időtartam  |  Rendelkezésre állási teszt időtartama  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  Oldalbetöltési hálózat csatlakozási ideje  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  Ügyfél feldolgozási ideje  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  Válaszidő fogadása  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  Kérés ideje elküldése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  A böngészőoldal betöltési ideje  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.Insights/Components  |  függőségek/számlálás  |  Függőségi hívások  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  függőségek/időtartam  |  Függőség időtartama  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.Insights/Components  |  függőségek/sikertelen  |  Függőségi hívás hibái  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft.Insights/Components  |  kivételek/böngésző  |  Böngészőkivételek  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft.Insights/Components  |  kivételek/számlálás  |  Kivételek  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft.Insights/Components  |  kivételek/kiszolgáló  |  Kiszolgálói kivételek  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft.Insights/Components  |  oldalmegtekintések/számlálók  |  Oldalnézetek  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  oldalMegtekintések/időtartam  |  Oldalnézet betöltési ideje  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Kivétel aránya  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  Igénybe vehető memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  Folyamat PROCESSZOR  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  Io-sebesség feldolgozása  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Processzor idő  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Magánbájtok feldolgozása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  HTTP-kérelem végrehajtási ideje  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  HTTP-kérelmek az alkalmazásvárólistában  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  HTTP-kérelmek aránya  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Insights/Components  |  kérések/számlálás  |  Kiszolgálói kérelmek  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft.Insights/Components  |  kérések/időtartam  |  Szerverválaszidő  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.Insights/Components  |  kérések/sikertelen  |  Sikertelen kérelmek  |  Darabszám  |  Darabszám | 
| Nem  | Nem |  Microsoft.Insights/Components  |  kérések/díj  |  Kiszolgálókérési arány  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Insights/Components  |  nyomok/szám  |  Hívásláncok  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Szolgáltatás Api-találatai összesen  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft.KeyVault/vaults  |  ServiceApiLatency (SzervizapiLatency)  |  Általános szolgáltatásapi-késés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Szolgáltatás api-eredményeinek összesen  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  Gyorsítótárhasználata  |  Gyorsítótár kihasználtsága  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  ContinuousExportMaxLatenessMinutes  |  Folyamatos exportálási maximális késési perc  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  ContinuousExportNumOfRecordsExportált  |  Folyamatos exportálás - exportált rekordok száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  Folyamatos exportálásfüggő száma  |  Folyamatos exportálás függőben lévő száma  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  ContinuousExportResult  |  Folyamatos exportálás eredménye  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  CPU  |  CPU  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  EseményekProcessedForEventHubs  |  Feldolgozott események (esemény-/IoT-központok esetén)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  Exportálás Kihasználása  |  Kihasználtság exportálása  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  BetöltésLatencyInSeconds  |  Betöltési késés (másodpercben)  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  Betöltéseredménye  |  Lenyelési eredmény  |  Darabszám  |  Darabszám | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  LenyelésKitilizáció  |  Lenyelés kihasználtsága  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  BetöltésvolumeinMB  |  Betöltési kötet (MB-ban)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  KeepAlive (Életben tartás)  |  Életben maradni  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  QueryDuration (Lekérdezésidőtartama)  |  Lekérdezés időtartama  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  SteamingingestRequestRate  |  Betöltési kérelem sebességének streamelése  |  Darabszám  |  RaterequestsPerSecond (RaterequestsPerSzekszekszek | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  StreamingIngestDataRate  |  A betöltési adatok átviteli sebessége  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  StreamingIngestIdőtartam  |  Streamelési idő  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Kusto/Fürtök  |  StreamingIngestResults (StreamingestResults)  |  A betöltés i.  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency (MűveletÁtlika)  |  Művelet késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Végrehajtott műveletek  |  Végrehajtott műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Sikertelen műveletek  |  Sikertelen műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Kihagyott műveletek  |  Kihagyott műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Indított műveletek  |  Megkezdett műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Sikeres műveletek  |  Sikeres műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency (AkciósikerÁtkés)  |  Művelet sikeres késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEsemények  |  Művelet szabályozása események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Összekötőmemória használata az integrációs szolgáltatáskörnyezethez  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Összekötő processzor használata integrációs szolgáltatáskörnyezethez  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Munkafolyamat-memória használata az integrációs szolgáltatáskörnyezethez  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Munkafolyamat-processzor használata integrációs szolgáltatáskörnyezethez  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailureSzázalék  |  Futtatási hiba százaléka  |  Százalék  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  Futtatás késése  |  Késés futtatása  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Futtatások megszakítva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Futtatások befejezve  |  Futtatások befejezve  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  FuttatásFailed  |  Sikertelen futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Futtatások Started  |  Futtatások indítása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  FuttatásSikeres  |  Sikeres futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEsemények  |  Futás –Szabályozású események futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency (RunSuccessLatency)  |  Sikeres késés futtatása  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEsemények  |  Szabályozott események futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Tűz késleltetése   |  Másodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency (TriggerLatencia)  |  Eseményindító késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Eseményindítók befejezve  |  Befejezett eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  EseményindítókSikertelen  |  Sikertelen eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired (TriggersFired)  |  Kiváltott eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Előugró eseményindítók  |  Kihagyott eseményindítók  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Aktivált eseményindítók  |  Elindítva az eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  Eseményindítók Sikeres  |  Az eseményindítók sikeresek   |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency (TriggerSuccessLatency)  |  Sikeres késés aktiválása   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEsemények  |  Irányított események aktiválása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/munkafolyamatok  |  ActionLatency (MűveletÁtlika)  |  Művelet késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Végrehajtott műveletek  |  Végrehajtott műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Sikertelen műveletek  |  Sikertelen műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Kihagyott műveletek  |  Kihagyott műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Indított műveletek  |  Megkezdett műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Sikeres műveletek  |  Sikeres műveletek   |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/munkafolyamatok  |  ActionSuccessLatency (AkciósikerÁtkés)  |  Művelet sikeres késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  ActionThrottledEsemények  |  Művelet szabályozása események  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  BillableActionExecutions (Számlázhatóműveletvégrehajtásai)  |  Számlázható műveletek végrehajtása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  SzámlázhatóTriggerExecutions  |  Számlázható eseményindító-végrehajtások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  BillingUsageNativeOperation művelet  |  Natív műveletvégrehajtás számlázási használata  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  BillingUsageNativeOperation művelet  |  Natív műveletvégrehajtás számlázási használata  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  BillingUsageStandardConnector  |  Számlázási használat szabványos összekötő-végrehajtások esetén  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  BillingUsageStandardConnector  |  Számlázási használat szabványos összekötő-végrehajtások esetén  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  BillingUsageConsumptionConsumption  |  Számlázási használat a tárolási felhasználás végrehajtásaihez  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  BillingUsageConsumptionConsumption  |  Számlázási használat a tárolási felhasználás végrehajtásaihez  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  RunFailureSzázalék  |  Futtatási hiba százaléka  |  Százalék  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/munkafolyamatok  |  Futtatás késése  |  Késés futtatása  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  RunsCancelled  |  Futtatások megszakítva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Futtatások befejezve  |  Futtatások befejezve  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  FuttatásFailed  |  Sikertelen futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Futtatások Started  |  Futtatások indítása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  FuttatásSikeres  |  Sikeres futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  RunStartThrottledEsemények  |  Futás –Szabályozású események futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/munkafolyamatok  |  RunSuccessLatency (RunSuccessLatency)  |  Sikeres késés futtatása  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  RunThrottledEsemények  |  Szabályozott események futtatása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Összes számlázható végrehajtás  |  Számlázható kivégzések összesen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/munkafolyamatok  |  TriggerFireLatency  |  Tűz késleltetése   |  Másodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Logic/munkafolyamatok  |  TriggerLatency (TriggerLatencia)  |  Eseményindító késése   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Eseményindítók befejezve  |  Befejezett eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  EseményindítókSikertelen  |  Sikertelen eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  TriggersFired (TriggersFired)  |  Kiváltott eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Előugró eseményindítók  |  Kihagyott eseményindítók  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Aktivált eseményindítók  |  Elindítva az eseményindítók   |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  Eseményindítók Sikeres  |  Az eseményindítók sikeresek   |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Logic/munkafolyamatok  |  TriggerSuccessLatency (TriggerSuccessLatency)  |  Sikeres késés aktiválása   |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Logic/munkafolyamatok  |  TriggerThrottledEsemények  |  Irányított események aktiválása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Aktív magok  |  Aktív magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Aktív csomópontok  |  Aktív csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Befejezett futtatások  |  Befejezett futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Sikertelen futtatások  |  Sikertelen futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Alapjárati magok  |  Alapjárati magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Tétlen csomópontok  |  Tétlen csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Elhagyva magok  |  Elhagyva magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Csomópontok elhagyása  |  Csomópontok elhagyása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  A modell telepítése sikertelen  |  A modell telepítése sikertelen  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Modell üzembe helyezése elindítva  |  Modell üzembe helyezése elindítva  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  A modell telepítése sikerült  |  A modell telepítése sikerült  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  A modellregiszter nem sikerült  |  A modellregiszter nem sikerült  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Sikeres modellregisztika  |  Sikeres modellregisztika  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Preempted magok  |  Preempted magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Elődpontos csomópontok  |  Elődpontos csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Kvótakihasználtság százaléka  |  Kvótakihasználtság százaléka  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Elkezdett futtatások  |  Elkezdett futtatások  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Magok összesen  |  Magok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Összes csomópont  |  Összes csomópont  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Használhatatlan magok  |  Használhatatlan magok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.MachineLearningServices/munkaterületek  |  Használhatatlan csomópontok  |  Használhatatlan csomópontok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Maps/fiókok  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Maps/fiókok  |  Használat  |  Használat  |  Darabszám  |  Darabszám | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  Eszközszáma  |  Eszközdarabszáma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  Eszközkvóta  |  Eszközkvóta  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  AssetQuotaUsedSzázalék  |  Használt eszközkvóta százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount (Tartalomkulcs-házirendszáma)  |  Tartalomkulcs-házirendek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota (ContentKeyPolicyQuota)  |  Tartalomkulcs-házirendkvóta  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedSzázalék  |  A tartalomkulcs-házirend használt kvótája százalékban  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  StreamingPolicyCount (StreamPolicyCount)  |  Streamelési irányelvek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota (StreamingPolicyQuota)  |  Streamelési házirend kvóta  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedSzázalék  |  A streamelési szabályzat használt kvótája százalékban  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices/streamingVégpontok  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices/streamingVégpontok  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Media/mediaservices/streamingVégpontok  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres befejezés a késés végéig  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount (GCPauseTotalCount)  |  Gc szünetszáma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Globális katalógus szüneteltetési teljes ideje  |  Ezredmásodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolPoolBytes  |  Maximálisan elérhető régi generációs adatméret  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Régi generációs adatok mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Előléptetés régi generációs adatméretre  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsageszázalék  |  Szolgáltatás PROCESSZORának használati százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Hozzárendelt szolgáltatásmemória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Szolgáltatás memória max  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  Használt serviceMemory  |  Használt szolgáltatásmemória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsageSzázalék  |  Rendszerprocesszor-használat százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatHibaszám  |  Tomcat globális hiba  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes (TomcatReceivedBytes)  |  Tomcat teljes fogadott bájt  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tomcat-kérelem maximális ideje  |  Ezredmásodperc  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount (TomcatRequestTotalCount)  |  Tomcat kérelem teljes száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Tomcat kérelem összes alkalommal  |  Ezredmásodperc  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Tomcat kérelem átlagos ideje  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes (TomcatSentBytes)  |  Tomcat összes elküldött bájt  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Tomcat munkamenet élő száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Tomcat-munkamenet maximális aktív száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Tomcat session max élő idő  |  Ezredmásodperc  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Tomcat munkamenet létrehozva száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Tomcat-munkamenet lejárt száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Tomcat munkamenet elutasított száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Népszerűsítés fiatal generációs adatméretre  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.NetApp/netAppAccounts/capacityPools  |  Kötetkészletfelosztott  |  Használt kötetkészlet  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Kötetkészlet teljes logikai mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Átlagos olvasási átkés  |  Átlagos olvasási késleltetés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Átlagos íráskésleltetés  |  Átlagos írási késés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops között  |  Olvasás iops  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize (VolumeLogicalSize)  |  Kötet logikai mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize (VolumeSnapshotSize)  |  Kötet pillanatképének mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops (WriteIops)  |  Iops írása  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime alkalmazás  |  Alkalmazásátjáró teljes ideje  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Kérelmek percenként és egészséges gazdagépenként  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  HáttérendConnectTime  |  Háttérkapcsolati idő  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Háttér– első bájtválasz ideje  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Háttértartalék utolsó bájtválasz ideje  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  HáttérvégpontVálaszállapota  |  Háttérválaszok állapota  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  BlockedCount (BlockedCount)  |  Webalkalmazás-tűzfal blokkolt kérelmek szabályának elosztása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  BlockedReqCount között  |  A webalkalmazás tűzfalának blokkolt kérései száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  Fogadott bájt  |  Fogadott bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  Küldött bájt  |  Elküldött bájtok  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  Kapacitásegységek  |  Aktuális kapacitásegységek  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  Ügyfélrtt  |  Ügyfél RTT  |  Ezredmásodperc  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  Számítási egységek  |  Aktuális számítási egységek  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  Jelenlegi kapcsolatok  |  Jelenlegi kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  Sikertelen kérések  |  Sikertelen kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  EgészségeshostCount  |  Egészséges gazdagép száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  Egyezésű ekszáma  |  Webalkalmazás-tűzfal összes szabályának elosztása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  ResponseStatus (Válasz állapota)  |  Válasz állapota  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Network/applicationGateways  |  Teljesítmény  |  Teljesítmény  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  Ügyfél TLS protokollja  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Összes kérelem  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/applicationGateways  |  Nem megfelelőhostcount  |  Nem megfelelő gazdagépszám  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit alkalmazás  |  Alkalmazásszabályok találati száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/azurefirewalls  |  Feldolgozott adatok  |  Feldolgozott adatok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/azurefirewalls  |  Tűzfal-egészségügyi  |  Tűzfal állapota  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/azurefirewalls  |  HálózatiRuleHit  |  Hálózati szabályok találati száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/azurefirewalls  |  SNATPortKihasználtság  |  SNAT-port kihasználtsága  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/kapcsolatok  |  BitsInPerSz  |  BitsInPerSz  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/kapcsolatok  |  BitsOutPerSz  |  BitsOutPerSz  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/dnszones  |  QueryVolume (Lekérdezési kötet)  |  Lekérdezés imtora  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Network/dnszones  |  RecordSetCapacityKihasználtság  |  Rekordkészlet kapacitáskihasználtsága  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/dnszones  |  Rekordsor száma  |  Rekordkészlet száma  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/expressRouteCircuits  |  ArpElérhető  |  Arp elérhetősége  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRouteCircuits  |  BgpElérhető  |  BGP elérhetősége  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSz  |  BitsInPerSz  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSz  |  BitsOutPerSz  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSz  |  DroppedInBitsPerSecond  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerMásodik  |  DroppedOutBitsPerSecond  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSz  |  BitsInPerSz  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSz  |  BitsOutPerSz  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSz  |  CountPerSzekszekundum  |  Átlag | 
| Nem  | Nem |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSz  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRoutePorts  |  AdminÁllam  |  AdminÁllam  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRoutePorts  |  LineProtocol (LineProtocol)  |  LineProtocol (LineProtocol)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSz  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSz  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Háttér-állapot százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  Háttérvégpontkérése  |  Háttér-kérelem száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  HáttérendKérelem Latency  |  Háttér-kérelem késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  Számlázható válaszméret  |  Számlázható válasz mérete  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  Kérésszáma  |  Kérelmek száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  Kérésméretet  |  A kérelem mérete  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  Válaszméret  |  Válasz mérete  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  Összesen Késedelmiség  |  Teljes késés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Webalkalmazás tűzfalának kérelmek száma  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Network/loadBalancers  |  FelosztottSnatPorts  |  Lefoglalt SNAT-portok (előzetes verzió)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/loadBalancers  |  ByteCount (Bájtszáma)  |  Bájtszáma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/loadBalancers  |  DipElérhető  |  Állapotvizsgálat állapota  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/loadBalancers  |  Csomagszáma  |  Csomagok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/loadBalancers  |  SnatConnectionCount (SnatConnectionCount)  |  SNAT-kapcsolatok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/loadBalancers  |  SYNCount (SZINK)  |  SYN-szám  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Használt SNAT-portok (előzetes verzió)  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/loadBalancers  |  VipElérhetőség  |  Az adatelérési út elérhetősége  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/networkInterfaces  |  BájtBeRate  |  Fogadott bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Elküldött bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/networkInterfaces  |  CsomagokFogadott  |  Fogadott csomagok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/networkInterfaces  |  CsomagokSentRate  |  Elküldött csomagok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/networkWatchers/connectionMonitorok  |  Átlagosfordulók  |  Átlagos oda-vissza idő (ms)  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/networkWatchers/connectionMonitorok  |  ChecksFailedPercent  |  Sikertelen ellenőrzések (előnézet)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/networkWatchers/connectionMonitorok  |  MintavételekFailedPercent  |  Sikertelen % mintavételek  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/networkWatchers/connectionMonitorok  |  Körúttalmunkaidő-gyártók  |  Oda-vissza út (ms) (előzetes verzió)  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  ByteCount (Bájtszáma)  |  Bájtszáma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  Eldobott bájtokDDoS  |  Eldobott bejövő bájtok DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Bejövő továbbított DDoS-bájtok  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Bejövő ddos bájtok  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Bejövő SYN-csomagok a DDoS-kockázatcsökkentés elindításához  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Bejövő TCP-csomagok a DDoS-kockázatcsökkentés elindításához  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Bejövő UDP-csomagok a DDoS-kockázatcsökkentés elindításához  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  Ifunderddostámadás  |  A DDoS támadás vagy nem  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  Csomagszáma  |  Csomagok száma  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  DDoS-t eldobott bejövő csomagok  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Továbbított bejövő csomagok DDoS  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  Bejövő csomagok DDoS  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  SynCount (Szinka)  |  SYN-szám  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Eldobott bejövő TCP-bájtok DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Bejövő TCP-bájtok továbbított DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  Bejövő TCP-bájtok DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  DDoS-t eldobott bejövő TCP-csomagok  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Bejövő TCP-csomagok továbbított DDoS  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  Bejövő TCP-csomagok DDoS  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Kimenő UDP-bájtok eldobott DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Bejövő UDP-bájtok továbbított DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  Bejövő UDP-bájtok DDoS  |  BytesPerSecond  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  DDoS-t eldobott bejövő UDP-csomagok  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Bejövő UDP-csomagok továbbított DDoS  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Bejövő UDP-csomagok DDoS  |  CountPerSzekszekundum  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/publicIPAddresses  |  VipElérhetőség  |  Az adatelérési út elérhetősége  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Végpont állapota végpont szerint  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Végpont által visszaadott lekérdezések  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  Átlagos sávszélesség  |  Átjáró S2S-sávszélessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth (P2SBandwidth)  |  Átjáró P2S-sávszélessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectioncount között  |  P2S-kapcsolatok száma  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth (CsatornaÁtlagos sávszélessége)  |  Alagút sávszélessége  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Alagút kimenő hídbájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Bújtatási kimenő forgalom – TS eltérés a csomag ledobása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Bújtatási kimenő csomagok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Alagút be- és áttetsző bájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Bújtatás iTS eltéréscsomag-ledobása  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketsCsomagok  |  Bújtatási csomagok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworks  |  PingMeshAverageKörmenet-objektumok  |  A pingelések oda-vissza olásának ideje a virtuális géphez  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Sikertelen pingelések a virtuális géphez  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Bejövő  |  Bejövő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  bejövő.all.failedrequests  |  Minden sikertelen bejövő kérés  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  bejövő.all.requests  |  Minden bejövő kérés  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  bejövő.ütemezett  |  Elküldött ütemezett leküldéses értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  bejövő.ütemezett.mégse  |  Ütemezett leküldéses értesítések törölve  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  telepítés.all  |  Telepítéskezelési műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Telepítési műveletek törlése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Telepítési műveletek beszereznie  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  telepítés.patch  |  Javítás telepítési műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  telepítés.upsert  |  Telepítési műveletek létrehozása vagy frissítése  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Minden kimenő értesítés  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.allpns.badorexpiredchannel  |  Hibás vagy lejárt csatornahibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.allpns.channelerror  |  Csatornahibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.allpns.invalidpayload  |  Hasznos adatra van hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.allpns.pnserror  |  Külső értesítési rendszer hibái  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Sikeres értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.apns.badchannel  |  APNS hibás csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.apns.expiredchannel  |  APNS lejárt csatornahiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.apns.invalidcredentials  |  APNS engedélyezési hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.apns.invalidnotificationsize  |  Az APN-ek érvénytelen értesítésméret-hibája  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.apns.pnserror  |  APNS-hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Az APN-ek sikeres értesítései  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.authenticationerror  |  GCM hitelesítési hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.badchannel  |  GCM hibás csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.expiredchannel  |  GCM lejárt csatornahiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.invalidcredentials  |  GCM-engedélyezési hibák (érvénytelen hitelesítő adatok)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.invalidnotificationformat  |  A GCM érvénytelen értesítési formátuma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.invalidnotificationsize  |  A GCM érvénytelen értesítésméret-hibája  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.pnserror  |  GCM hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Sikeres GCM-értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.throttled  |  GCM szabályozott értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.gcm.wrongchannel  |  GCM rossz csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  MPNS-hitelesítési hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.mpns.badchannel  |  MPNS hibás csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnect  |  MPNS-csatorna leválasztva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  MPNS eldobott értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  MPNS érvénytelen hitelesítő adatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  MpN-ek érvénytelen értesítési formátuma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  MPNS-hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  MPNS sikeres értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.mpns.throttled  |  MPNS szabályozott értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.authenticationerror  |  WNS hitelesítési hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.badchannel  |  WNS hibás csatorna hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.channeldisconnect  |  WNS-csatorna leválasztva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.channelthrottled  |  WNS-csatorna fojtva  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.dropped  |  WNS eldobott értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.expiredchannel  |  WNS lejárt csatornahiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.invalidcredentials  |  WNS-engedélyezési hibák (érvénytelen hitelesítő adatok)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.invalidnotificationformat  |  Wns érvénytelen értesítési formátum  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.invalidnotificationsize  |  Wns érvénytelen értesítésméret-hiba  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.invalidtoken  |  WNS engedélyezési hibák (érvénytelen jogkivonat)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.pnserror  |  WNS hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.success  |  A WNS sikeres értesítései  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.throttled  |  WNS szabályozott értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.tokenproviderunreachable  |  WNS engedélyezési hibák (nem érhető el)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  kimenő.wns.wrongtoken  |  WNS engedélyezési hibák (hibás token)  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  regisztráció.all  |  Regisztrációs műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  regisztráció.create  |  Regisztrációs műveletek létrehozása  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  regisztráció.törlés  |  Regisztráció törlési műveletei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  regisztráció.get  |  Regisztráció olvasási műveletei  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  regisztráció.update  |  Regisztrációs frissítési műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  ütemezett.függőben  |  Függőben lévő ütemezett értesítések  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os rendelkezésre álló memória  |  Rendelkezésre álló memória %-a  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%- os rendelkezésre álló swap-terület  |  A rendelkezésre álló csereterület %-a  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%, használatban lévő lekötött bájtok  |  Használatban lévő lekötött bájtok %-a  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os DPC idő  |  DPC-idő %-os ideje  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_% Ingyenes Inodes  |  % szabad inodák  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_% szabad hely  |  Szabad terület %-os  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_% szabad hely  |  Szabad terület %-os  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%- os tétlen idő  |  Üresjáratban eltöltött időhányad (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os megszakítási idő  |  Megszakítási idő %-os ideje  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os IO várakozási idő  |  %-os IO várakozási idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_% Szép idő  |  % Szép idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os jogosultsági idő  |  Jogosultsági idő %-os százaléka  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os processzoridő  |  A processzor kihasználtsága (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os processzoridő  |  A processzor kihasználtsága (%)  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_% Használt Inodes  |  Használt inodák %-a  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os használt memória  |  Felhasznált memória %-a  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%- os használt terület  |  % használt terület  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_% Használt swap terület  |  % Használt csereterület  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_%-os felhasználói idő  |  %felhasználói idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Available MByte  |  Elérhető MBytes  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Available MByte memória  |  Elérhető MByte memória  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Available MByte swap  |  Elérhető MByteswap  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.  |  Átlagos lemezmásodperc/olvasás  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.  |  Átlagos lemezmásodperc/olvasás  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Avg. Lemez másodperc/átvitel  |  Átlagos lemezmásodperc/átvitel  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.  |  Átlagos lemez másodperc/írás  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.  |  Átlagos lemez másodperc/írás  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Bytes fogadott/mp  |  Másodpercenként fogadott bájtok száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Bytes elküldve/mp  |  Küldött bájtok/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Bytes összesen/mp  |  Összes bájt/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Current lemezvárólista hossza  |  Az aktuális lemezvárólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Disk olvasási bájt/mp  |  Lemez olvasási bájtjai/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Disk olvasás/mp  |  Lemezolvasás/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Disk olvasás/mp  |  Lemezolvasás/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Disk átvitelek/mp  |  Lemezátvitel/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Disk átvitelek/mp  |  Lemezátvitel/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Disk írási bájt/mp  |  Lemezírási bájtok/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Disk írások/mp  |  Lemezírás/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Disk írások/mp  |  Lemezírás/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Free megabájt  |  Ingyenes Megabájt  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Free megabájt  |  Ingyenes Megabájt  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Free fizikai memória  |  Szabad fizikai memória  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Free tér közterülete a lapozófájlokban  |  Szabad terület a lapozófájlokban  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Free virtuális memória  |  Ingyenes virtuális memória  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Logical lemezbájt/mp  |  Logikai lemezbájt/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Page olvasása/mp  |  Oldalolvasások/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Page írások/mp  |  Lapírások/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Pages/mp  |  Oldalak/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Pct kiváltságos idő  |  Pct privilegizált idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Pct felhasználói idő  |  Pct felhasználói idő  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Physical lemezbájt/mp  |  Fizikai lemezbájt/mp  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Processes  |  Folyamatok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Processor várólista hossza  |  Processzorvárólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Size lapozófájlokban tárolva  |  Lapozófájlokban tárolt méret  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Total bájt  |  Összes bájt  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Total fogadott bájtok  |  Fogadott bájtok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Total továbbított bájtok  |  Átküldött bájtok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Total ütközések  |  Ütközések összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Total fogadott csomagok  |  Fogadott csomagok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Total továbbított csomagok  |  Átküldött csomagok összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Total Rx hibák  |  Rx hibák összesen  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Total Tx hibák  |  Összes Tx hiba  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Uptime  |  Uptime  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Used MBytes swap tér  |  Használt MBytes swap tér  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Used memória kbyte  |  Használt memóriakbyte-ok  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Used memória MBytes  |  Használt memória MBytes  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Users  |  Felhasználók  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Average_Virtual megosztott memória  |  Virtuális megosztott memória  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Esemény  |  Esemény  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.OperationalInsights/munkaterületek  |  Szívverés  |  Szívverés  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.OperationalInsights/munkaterületek  |  Frissítés  |  Frissítés  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.PowerBIDedikált/kapacitások  |  memory_metric  |  Memory (Memória)  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.PowerBIDedikált/kapacitások  |  memory_thrashing_metric  |  Memóriaverés (adatkészletek)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.PowerBIDedikált/kapacitások  |  qpu_high_utilization_metric  |  QPU magas kihasználtság  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.PowerBIDedikált/kapacitások  |  QueryDuration (Lekérdezésidőtartama)  |  Lekérdezés időtartama (adatkészletek)  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.PowerBIDedikált/kapacitások  |  QueryPoolJobQueueLength  |  Lekérdezéskészlet feladatvárólista hossza (adatkészletek)  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  ActiveConnections  |  ActiveConnections  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  ActiveListeners  |  ActiveListeners  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Relay/névterek  |  Átvitt bájtok  |  Átvitt bájtok  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  Figyelőkapcsolatok-Ügyfélhiba  |  Figyelőkapcsolatok-Ügyfélhiba  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  Figyelőkapcsolatok-ServerHiba  |  Figyelőkapcsolatok-ServerHiba  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  ListenerConnections-sikeres  |  ListenerConnections-sikeres  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  Figyelőleválasztás  |  Figyelőleválasztás  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  SenderConnections-ClientError (Küldőkapcsolatok-ügyfélhiba)  |  SenderConnections-ClientError (Küldőkapcsolatok-ügyfélhiba)  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  SenderConnections-Sikeres  |  SenderConnections-Sikeres  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Relay/névterek  |  Feladóleválasztás  |  Feladóleválasztás  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Search/searchServices  |  KeresésLatency  |  Késés keresése  |  Másodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Keresési lekérdezések másodpercenként  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Search/searchServices  |  Szabályozott searchqueriesszázalékos  |  Szabályozott keresési lekérdezések százalékos aránya  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  ActiveConnections  |  ActiveConnections  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  ActiveMessages  |  A várólistában/témakörben lévő aktív üzenetek száma.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  Kapcsolatok zárva  |  Kapcsolatok zárva.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  Kapcsolatok megnyitva  |  Kapcsolatok megnyitva.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  CPUXN-ek  |  CPU (elavult)  |  Százalék  |  Maximum | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  DeadletteredÜzenetek  |  Kézbesítetlen levelek száma egy várólistában/témakörben.  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.ServiceBus/névterek  |  Bejövő üzenetek  |  Bejövő üzenetek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.ServiceBus/névterek  |  Bejövő kérések  |  Bejövő kérések  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  Üzenetek  |  A várólistában/témakörben lévő üzenetek száma.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  NamespaceCpuUsage  |  CPU  |  Százalék  |  Maximum | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  NamespaceMemoryUsage  |  Memóriahasználat  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.ServiceBus/névterek  |  Kimenő üzenetek  |  Kimenő üzenetek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  Ütemezett üzenetek  |  A várólistában/témakörben lévő ütemezett üzenetek száma.  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  Kiszolgálóhibák  |  Kiszolgálói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  Méret  |  Méret  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  Sikeres kérések  |  Sikeres kérelmek  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  ThrottledRequests  |  Szabályozott kérelmek.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  Felhasználói hibák  |  Felhasználói hibák.  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.ServiceBus/névterek  |  WSXN-ek  |  Memóriahasználat (elavult)  |  Százalék  |  Maximum | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  ActualCpu  |  ActualCpu  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  ActualMemory (Tényleges memória)  |  ActualMemory (Tényleges memória)  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  Felosztott cpu  |  Felosztott cpu  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  Lefoglalt memória  |  Lefoglalt memória  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  ApplicationStatus alkalmazásállapota  |  ApplicationStatus alkalmazásállapota  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  ContainerStatus (Konténerállapota)  |  ContainerStatus (Konténerállapota)  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  Cpukihasználtság  |  Cpukihasználtság  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  Memóriakihasználtság  |  Memóriakihasználtság  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  RestartCount (Újraindítási számláló)  |  RestartCount (Újraindítási számláló)  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  SzolgáltatásReplicaStatus  |  SzolgáltatásReplicaStatus  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.ServiceFabricMesh/alkalmazások  |  ServiceStatus (Szolgáltatásállapota)  |  ServiceStatus (Szolgáltatásállapota)  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.SignalRService/SignalR  |  Kapcsolatszáma  |  Kapcsolatok száma  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.SignalRService/SignalR  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.SignalRService/SignalR  |  MessageCount (Üzenetszám)  |  Üzenetek száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.SignalRService/SignalR  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.SignalRService/SignalR  |  Rendszerhibák  |  Rendszerhibák  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.SignalRService/SignalR  |  Felhasználói hibák  |  Felhasználói hibák  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Átlagos CPU-százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  I/o-bájtok olvasása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Írásban írt IO-bájtok  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/managedInstances  |  io_requests  |  Az IO-kérelmek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Fenntartott tárhely  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Használt tárhely  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Virtuális magok száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/kiszolgálók  |  database_dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/kiszolgálók  |  database_storage_used  |  Használt adatterület  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/kiszolgálók  |  dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/kiszolgálók  |  dtu_used  |  Használt DTU  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/kiszolgálók  |  storage_used  |  Használt adatterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Lefoglalt adatterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  Alkalmazás PROCESSZORa számlázva  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Alkalmazás processzorának százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Alkalmazásmemória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Tűzfal blokkolja  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Gyorsítótár találati százaléka  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Gyorsítótár által használt százalék  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Sikertelen kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Sikeres kapcsolatok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  cpu_limit  |  CPU-korlát  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  cpu_used  |  HASZNÁLT PROCESSZOR  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  Holtpont  |  Holtpontok  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU-korlát  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  dtu_used  |  Használt DTU  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU százalék  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  dwu_limit  |  DWU-korlát  |  Darabszám  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  dwu_used  |  Használt DWU  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Helyi tempdb százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Log IO százalék  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Memória százaléka  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Adat IO kihasználtsága (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Munkamenetek százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  storage  |  Használt adatterület  |  Bájt  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  storage_percent  |  Felhasznált adatterület százaléka  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Tempdb-adatfájl mérete kilobájt  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Tempdb naplófájl mérete Kilobájt  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Használt Tempdb százaléknapló  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Munkavállalók százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Memórián belüli OLTP-tároló százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Lefoglalt adatterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Kiosztott adatterület százalék  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  CPU-korlát  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  HASZNÁLT PROCESSZOR  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Lefoglalt adatterület  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  CPU-korlát  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  HASZNÁLT PROCESSZOR  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU használt  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Log IO százalék  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Adat IO kihasználtsága (%)  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Munkamenetek százalékos aránya  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Használt adatterület  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Munkavállalók százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  DTU-kihasználtság (%)  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU korlát  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU használt  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Log IO százalék  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Adat IO kihasználtsága (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Munkamenetek százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Adatok maximális mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Felhasznált adatterület százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Használt adatterület  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Tempdb-adatfájl mérete kilobájt  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Tempdb naplófájl mérete Kilobájt  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Használt Tempdb százaléknapló  |  Százalék  |  Maximum | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Munkavállalók százalékos aránya  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Memórián belüli OLTP-tároló százaléka  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts  |  Használtkapacitás  |  Használt kapacitás  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity kapacitás  |  Blob kapacitása  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount (BlobCount)  |  Blobok száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  Konténerszám  |  Blob-tárolók száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  Indexkapacitás  |  Index kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/blobServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  Fájlkapacitás  |  Fájl kapacitása  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  Fájlszám  |  Fájlok száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Fájlmegosztások száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  FileSharequota fájlmegosztás  |  Fájlmegosztási kvóta mérete  |  Bájt  |  Átlag | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Fájlmegosztás pillanatképének száma  |  Darabszám  |  Átlag | 
| Nem  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  Fájlmegosztássnapshotmérete  |  Fájlmegosztás pillanatképének mérete  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/fileServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  Várólistakapacitás  |  Várólista kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  Várólistaszáma  |  Várólista száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount (Üzenetsor)  |  Várólista-üzenetek száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/queueServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  Rendelkezésre állás  |  Rendelkezésre állás  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  Kimenő forgalom  |  Kimenő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  Bejövő forgalom  |  Bejövő forgalom  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency (SuccessE2ELatency)  |  Sikeres kérések végpontok közötti késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency (Sikereskiszolgálók átkának)  |  Sikeres kiszolgálói kérések késése  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  Táblakapacitás  |  Tábla kapacitása  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  Tábladarabszám  |  Táblaszáma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  Táblaentitásszáma  |  Táblaentitások száma  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.Storage/storageAccounts/tableServices  |  Tranzakciók  |  Tranzakciók  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  ClientIOPS  |  Összes ügyfél IOPS  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  Ügyfélkés-késés  |  Átlagos ügyfélkésés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  ClientLockIOPS  |  Ügyfélzárolási IOPS  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  ÜgyfélMetadataReadIOPS  |  Ügyfél metaadatok olvasási IOPS-ának olvasása  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  ÜgyfélMetadataWriteIOPS  |  Ügyfél metaadatok írási IOPS-a  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  ÜgyfélreadiOPS  |  Ügyfél olvasási IOPS  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  Ügyfélolvasásátviteli  |  Átlagos gyorsítótár olvasási átviteli  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  ÜgyfélwriteIOPS  |  Ügyfél írási IOPS  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  ClientWriteThroughput  |  Gyorsítótár írási átviteli értékátlaga  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetAsyncWriteThroughput  |  StorageTarget aszinkron írási átviteli  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetFillThroughput  |  StorageTarget kitöltési átviteli adatáta  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetHealth  |  Tárolási cél állapota  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetIOPS  |  Teljes storageTarget IOPS  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetLatency (TárolástargetLatency)  |  StorageTarget késés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetMetadataReadIOPS  |  StorageTarget metaadat-olvasási IOPS  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetMetadataWriteIOPS  |  StorageTarget metaadat-írási IOPS  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetReadAheadThroughput  |  StorageTarget előre olvasási átviteli  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetReadIOPS  |  StorageTarget olvasási IOPS  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetSyncWriteThroughput  |  StorageTarget szinkron írási átviteli  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetTotalReadThroughput  |  StorageTarget teljes olvasási átviteli-átmenő  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetTotalWriteThroughput  |  StorageTarget teljes írási átviteli-áteresztő  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  StorageTargetWriteIOPS  |  StorageTarget írási IOPS  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  Microsoft.StorageCache/gyorsítótárak  |  Uptime  |  Uptime  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Munkamenet-eredmény szinkronizálása  |  Darabszám  |  Átlag | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Szinkronizált bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Felhőrétegezés visszahívási méret alkalmazásonként  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Felhőrétegezés visszahívási mérete  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Felhőrétegezés visszahívása  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Felhőrétegezés visszahívási átviteli  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Kiszolgáló online állapota  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices  |  StorageSyncSessionperItem-hibák száma  |  A fájlok szinkronizálása nem  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices/registeredServers  |  Kiszolgálószívverés  |  Kiszolgáló online állapota  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Felhőrétegezés visszahívása  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Szinkronizált bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionperItem-hibák száma  |  A fájlok szinkronizálása nem  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Szinkronizált bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Szinkronizált fájlok  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  A fájlok szinkronizálása nem  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Sikertelen függvénykérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Függvényesemények  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutKérések  |  Függvénykérések  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  Konverziós hibák  |  Adatkonvertálási hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  Deszerializációhiba  |  Bemeneti deszerializálási hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Nem sorrendben események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEsemények  |  Korai bemeneti események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  Hibák  |  Futásidejű hibák  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Bemeneti eseménybájtjai  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents (InputEvents)  |  Bemeneti események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Eldugott bemeneti események  |  Darabszám  |  Maximum | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesperSecond  |  Fogadott bemeneti források  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEsemények  |  Késői bemeneti események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEsemények  |  Kimeneti események  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Vízjel késleltetése  |  Másodperc  |  Maximum | 
| **Igen**  | Nem |  Microsoft.StreamAnalytics/streamingjobs  |  Erőforrás-kihasználtság  |  SU % kihasználtság  |  Százalék  |  Maximum | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Lemezolvasási bájtok  |  Lemezolvasási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Lemezolvasási műveletek/mp  |  Lemezolvasási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Lemezírási bájtok  |  Lemezírási bájtok  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Lemezírási műveletek/mp  |  Lemezírási műveletek/mp  |  CountPerSzekszekundum  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Lemez olvasási bájtjai/mp  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Lemezolvasási késés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Lemezolvasási műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Lemezírási bájtok/mp  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Lemezírási késés  |  Ezredmásodperc  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Lemezírási műveletek  |  Darabszám  |  Összesen | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Aktív memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Memória biztosított  |  Memória megadva  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Használt memória  |  Használt memória  |  Bájt  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bejövő hálózat  |  Bejövő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Kimenő hálózat  |  Kimenő hálózat  |  Bájt  |  Összesen | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Hálózat bájt/mp-ben  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Hálózati kimenő bájtok/mp  |  BytesPerSecond  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Százalékos processzorhasználat  |  Százalékos processzorhasználat  |  Százalék  |  Átlag | 
| **Igen**  | Nem |  Microsoft.VMwareCloudSimple/virtualMachines  |  Százalékprocesszorready  |  Százalékos CPU ready  |  Ezredmásodperc  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Aktív kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Átlagos válaszidő  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Fogadott bájt  |  Adatok a  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Küldött bájt  |  Adatok ki  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Cpuszázalék  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Lemezvárólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx között  |  Http 2xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx között  |  Http 3xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx között  |  Http 4xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx között  |  Http Server hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength között  |  Http Várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Nagy app szolgáltatáscsomag-dolgozók  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Közepes alkalmazású szolgáltatáscsomag-dolgozók  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Memóriaszázalék  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Kis app osszes alkalmazáscsomag-dolgozók  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontends  |  Elülső végek összesen  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/workerPools  |  Cpuszázalék  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/workerPools  |  Memóriaszázalék  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/workerPools  |  Elérhető dolgozók  |  Elérhető dolgozók  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/workerPools  |  Dolgozók összesen  |  Összes dolgozó  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/hostingEnvironments/workerPools  |  Használt dolgozók  |  Használt munkavállalók  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  Fogadott bájt  |  Adatok a  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  Küldött bájt  |  Adatok ki  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  Cpuszázalék  |  Processzorhasználat (%)  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Lemezvárólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  HttpQueueLength között  |  Http Várólista hossza  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  Memóriaszázalék  |  Memória százaléka  |  Százalék  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpCloseVárakozás  |  TCP bezárása várakozás  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpLezárás  |  TCP zárása  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpLétrehozt  |  TCP létrehozva  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP Fin várjon 1  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP Fin Várjon 2  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP utolsó Ack  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpSynÉrkezett  |  TCP Syn fogadott  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  TCP Syn elküldve  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/serverfarms  |  TcpTimeVárakozás  |  TCP-idő várakozása  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Alkalmazáskapcsolatok  |  Kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  AverageMemoryWorkingSet  |  Átlagos memóriamunkakészlet  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  AverageResponseTime  |  Átlagos válaszidő  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Fogadott bájt  |  Adatok a  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Küldött bájt  |  Adatok ki  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  CpuTime  |  PROCESSZOR-idő  |  Másodperc  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  CurrentAssemblies között  |  Aktuális szerelvények  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  FunctionExecutionCount (FunctionExecutionCount)  |  Függvényvégrehajtási száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  FunctionExecutionUnits  |  Funkcióvégrehajtási egységek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Gen0Gyűjtemények  |  Gen 0 Szemétgyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Gen1Gyűjtemények  |  Gen 1 Szemétgyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Gen2Gyűjtemények  |  Gen 2 Szemétgyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Kezeli  |  Leírók száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Állapotellenőrző állapota  |  Állapot-ellenőrzés állapota  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http101  |  Http 101  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http2xx között  |  Http 2xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http3xx között  |  Http 3xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http401  |  Http 401  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http403  |  Http 403  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http404  |  Http 404  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http406  |  Http 406  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http4xx között  |  Http 4xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Http5xx között  |  Http Server hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  httpResponseTime  |  Válasz ideje  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  IoOtherBytesPerSz  |  Egyéb bájtok másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  IoOtherOperationsPerSecond  |  Io egyéb műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  IoReadBytesPerSz  |  Io olvasási bájt másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  IoReadOperationsPerSecond  |  Io olvasási műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  IoWriteBytesPerSecond  |  Io írási bájt másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  IoWriteOperationsPerSecond  |  I/O-írási műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  MemoryWorkingSet  |  Memóriamunkakészlet  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Privát bájt  |  Privát bájtok  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Kérelmekaz alkalmazásvárólistában  |  Kérelmek az alkalmazásvárólistában  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  Szálak  |  Szálak száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  TotalAppDomains (Összes alkalmazásdomain)  |  Összes alkalmazástartomány  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/webhelyek  |  TotalAppDomainsUnloaded  |  A kiürített összes alkalmazástartomány  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Alkalmazáskapcsolatok  |  Kapcsolatok  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  AverageMemoryWorkingSet  |  Átlagos memóriamunkakészlet  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  AverageResponseTime  |  Átlagos válaszidő  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Fogadott bájt  |  Adatok a  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Küldött bájt  |  Adatok ki  |  Bájt  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  CpuTime  |  PROCESSZOR-idő  |  Másodperc  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  CurrentAssemblies között  |  Aktuális szerelvények  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  FunctionExecutionCount (FunctionExecutionCount)  |  Függvényvégrehajtási száma  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  FunctionExecutionUnits  |  Funkcióvégrehajtási egységek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Gen0Gyűjtemények  |  Gen 0 Szemétgyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Gen1Gyűjtemények  |  Gen 1 Szemétgyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Gen2Gyűjtemények  |  Gen 2 Szemétgyűjtemények  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Kezeli  |  Leírók száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Állapotellenőrző állapota  |  Állapot-ellenőrzés állapota  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http101  |  Http 101  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http2xx között  |  Http 2xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http3xx között  |  Http 3xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http401  |  Http 401  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http403  |  Http 403  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http404  |  Http 404  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http406  |  Http 406  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http4xx között  |  Http 4xx  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Http5xx között  |  Http Server hibák  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  httpResponseTime  |  Válasz ideje  |  Másodperc  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  IoOtherBytesPerSz  |  Egyéb bájtok másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  IoOtherOperationsPerSecond  |  Io egyéb műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  IoReadBytesPerSz  |  Io olvasási bájt másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  IoReadOperationsPerSecond  |  Io olvasási műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  IoWriteBytesPerSecond  |  Io írási bájt másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  IoWriteOperationsPerSecond  |  I/O-írási műveletek másodpercenként  |  BytesPerSecond  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  MemoryWorkingSet  |  Memóriamunkakészlet  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Privát bájt  |  Privát bájtok  |  Bájt  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Kérelmek  |  Kérelmek  |  Darabszám  |  Összesen | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Kérelmekaz alkalmazásvárólistában  |  Kérelmek az alkalmazásvárólistában  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  Szálak  |  Szálak száma  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  TotalAppDomains (Összes alkalmazásdomain)  |  Összes alkalmazástartomány  |  Darabszám  |  Átlag | 
| **Igen**  | **Igen** |  Microsoft.Web/sites/slot  |  TotalAppDomainsUnloaded  |  A kiürített összes alkalmazástartomány  |  Darabszám  |  Átlag | 
