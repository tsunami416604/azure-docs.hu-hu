---
title: Az Azure Monitor támogatott mérőszámok erőforrástípus szerint
description: A metrikák elérhető az Azure Monitor az egyes erőforrástípusok listája.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 70f6e26d423781ba53865304a3fe8440fb120a7a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705174"
---
# <a name="supported-metrics-with-azure-monitor"></a>Az Azure monitorban támogatott mérőszámok

Az Azure Monitor használatával kommunikálhat a metrikák, beleértve a diagramkészítési őket a portálon, a hozzájuk férni a REST API-n keresztül vagy a lekérdezési őket több módot nyújt a PowerShell vagy parancssori felület használatával. Alább érhető el minden metrika teljes listáját jelenleg az Azure Monitor metrika folyamattal. Más metrikák elérhető a portálon vagy az örökölt API-k használatával lehet. Az alábbi listán csak a metrikák elérhető az Azure Monitor konszolidált metrika folyamat használatával tartalmazza. És -lekérdezés számára, ezek a metrikák, használja a [2018-01-01-es api-verzió](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Eseményközpont "Bejövő üzenetek" metrikája fel és ábrázolható egy üzenetsoronként. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Tartomány 0 és 100 s1, 0 – 200 – S2 és 0 – 400 – S4|ServerResourceType|
|memory_metric|Memory (Memória)|Bájt|Average|A memória. Tartomány 0 – 25 GB – S1, 0-50 GB – S2 és 0 – 100 GB – S4|ServerResourceType|
|TotalConnectionRequests|Csatlakozási kérelmek teljes száma|Count|Average|Csatlakozási kérelmek teljes száma. Ezek a beérkező kérelmek.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|CountPerSecond|Average|A sikeres kapcsolat befejezésekből sebessége.|ServerResourceType|
|TotalConnectionFailures|Csatlakozási hibák teljes száma|Count|Average|A sikertelen csatlakozási kísérletek összesen.|ServerResourceType|
|CurrentUserSessions|Jelenlegi felhasználói munkamenetek|Count|Average|A létrehozott felhasználói munkamenetek aktuális száma.|ServerResourceType|
|QueryPoolBusyThreads|Lekérdezési készlet foglalt szálai|Count|Average|A lekérdezési szálkészletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolJobQueueLength|A parancs feladat-várólistájának hossza|Count|Average|Az a parancs szálkészlet üzenetsorában található feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Feldolgozási készlet feladat-várólistájának hossza|Count|Average|Az a feldolgozási szálkészlet üzenetsorában lévő nem I/o feladatok száma.|ServerResourceType|
|CurrentConnections|Kapcsolat: Jelenlegi kapcsolatok száma|Count|Average|A létrehozott ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CleanerCurrentPrice|Memória: Tisztító – aktuális ár|Count|Average|Aktuális ára $/ bájt/idő, a memória, 1000-re normalizálva.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: Tisztító – zsugorítható memória|Bájt|Average|Memória (bájt) vonatkoznak a háttérben futó tisztító kiürítése mennyisége.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Tisztább memória – nem zsugorítható|Bájt|Average|Memória (bájt) nem vonatkoznak a háttérben futó tisztító kiürítése mennyisége.|ServerResourceType|
|MemoryUsage|Memória: Memóriahasználat|Bájt|Average|A kiszolgálói folyamat tisztító memória ár kiszámításakor memóriahasználat. Számláló Process\PrivateBytes plusz a memória által leképezett adatok figyelmen kívül hagyva amely leképezett vagy lefoglalt által az xVelocity memóriabeli elemzési motor (VertiPaq) az xVelocity motor memóriakorlátját mérete megegyezik.|ServerResourceType|
|MemoryLimitHard|Memória: Szigorú Memóriakorlát|Bájt|Average|Szigorú Memóriakorlát a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Memória: Felső Memóriakorlát|Bájt|Average|Felső Memóriakorlát a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Memória: Alsó Memóriakorlát|Bájt|Average|Alsó Memóriakorlát a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: VertiPaq-Memóriakorlát|Bájt|Average|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|Kvóta|Memória: Kvóta|Bájt|Average|Aktuális memóriakvóta bájtban. A memóriakvóta memória grant vagy a memória foglalás is nevezik.|ServerResourceType|
|QuotaBlocked|Memória: Blokkolt kvóta|Count|Average|Aktuális száma, amelyek le vannak tiltva, amíg a további Memóriakvóták felszabadítását.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq Nonpaged|Bájt|Average|Bájt memóriát a memóriabeli motor általi használatra munkakészletében lévő zárolva van.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq Paged|Bájt|Average|Lapozható memória, a memóriában lévő adatok bájt.|ServerResourceType|
|RowsReadPerSec|Feldolgozás: Másodpercenként beolvasott sorok száma|CountPerSecond|Average|Sorok beolvasásának sebessége az összes relációs adatbázisból.|ServerResourceType|
|RowsConvertedPerSec|Feldolgozás: Másodpercenként konvertált sorok|CountPerSecond|Average|Sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsWrittenPerSec|Feldolgozás: Másodpercenként írt sorok|CountPerSecond|Average|Sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|CommandPoolBusyThreads|Szálak: A parancs parancskészlet – foglalt szálak|Count|Average|A parancs szálkészletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Szálak: A parancs készlet – üresjárati szálak|Count|Average|A parancsszálkészletben az üresjárati szálak száma.|ServerResourceType|
|LongParsingBusyThreads|Szálak: Tartós folyamatok elemzési foglalt szálak|Count|Average|A tartós folyamatok elemzési szálkészletben lévő foglalt szálak száma.|ServerResourceType|
|LongParsingIdleThreads|Szálak: Tartós folyamatok elemzési üresjárati szálak|Count|Average|A tartós folyamatok elemzési szálkészletében az üresjárati szálak száma.|ServerResourceType|
|LongParsingJobQueueLength|Szálak: Tartós folyamatok elemzési feladat üzenetsorának hossza|Count|Average|A tartós folyamatok elemzési szálkészlet üzenetsorában található feladatok száma.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Szálak: Készlet foglalt i/o-feladat szálak feldolgozása|Count|Average|A feldolgozási szálkészletben I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Szálak: Feldolgozási készlet a nem I/o feladat foglalt szálai|Count|Average|A feldolgozási szálkészletben nem I/o feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Szálak: Feldolgozási készlet – I/O feladat üzenetsorának hossza|Count|Average|Az a feldolgozási szálkészlet üzenetsorában található I/O feladatok száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Szálak: Feldolgozási készlet – üresjárati i/o feladat szálak|Count|Average|A feldolgozási szálkészletben I/O feladatok üresjárati szálak száma.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Szálak: Feldolgozási készlet a nem I/o feladat üresjárati szálai|Count|Average|A feldolgozási szálkészletben nem I/o feladatok számára kijelölt üresjárati szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Szálak: Lekérdezési készlet – üresjárati szálak|Count|Average|A feldolgozási szálkészletben I/O feladatok üresjárati szálak száma.|ServerResourceType|
|QueryPoolJobQueueLength|Szálak: Lekérdezési készlet feladat-várólistájának hossza|Count|Average|Az a lekérdezési szálkészlet üzenetsorában található feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak: Rövid elemzés a foglalt szálak|Count|Average|A rövid elemzési szálkészletben lévő foglalt szálak száma.|ServerResourceType|
|ShortParsingIdleThreads|Szálak: Rövid elemzési üresjárati szálak|Count|Average|A rövid elemzési szálkészletben az üresjárati szálak száma.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak: Rövid elemzési feladat üzenetsorának hossza|Count|Average|A a rövid elemzési szálkészlet üzenetsorában található feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memóriaakadozás|Percent|Average|Átlagos memóriaakadozás.|ServerResourceType|
|mashup_engine_qpu_metric|M motor qpu-ja|Count|Average|Az adategyesítési motor folyamatainak QPU használatáról|ServerResourceType|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Average|Az adategyesítési motor folyamatainak memóriafelhasználása|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Összes Átjárókérés|Count|Összes|Átjáró-kérelmek száma|Hely, gazdagépnév|
|SuccessfulRequests|Sikeres Átjárókérések|Count|Összes|Átjáró sikeres kérelmek száma|Hely, gazdagépnév|
|UnauthorizedRequests|Jogosulatlan Átjárókérések|Count|Összes|Jogosulatlan átjárókérések száma|Hely, gazdagépnév|
|FailedRequests|Sikertelen Átjárókérések|Count|Összes|Sikertelen átjárókérések a száma|Hely, gazdagépnév|
|OtherRequests|Egyéb Átjárókérések|Count|Összes|Egyéb átjárókérések száma|Hely, gazdagépnév|
|Duration|Teljes időtartama Átjárókérések|Ezredmásodperc|Average|Teljes időtartam, Átjárókérések ezredmásodpercben|Hely, gazdagépnév|
|Kapacitás|Kapacitás|Percent|Average|Az ApiManagement szolgáltatás Utilization metrika|Location|
|EventHubTotalEvents|Teljes EventHub-események|Count|Összes|Az EventHub küldött események száma|Location|
|EventHubSuccessfulEvents|A sikeres EventHub-események|Count|Összes|A sikeres EventHub események száma|Location|
|EventHubTotalFailedEvents|Nem sikerült az EventHub-események|Count|Összes|Nem sikerült az EventHub-események száma|Location|
|EventHubRejectedEvents|Elutasított EventHub-események|Count|Összes|Elutasított EventHub események száma (konfigurációja érvénytelen vagy nem engedélyezett)|Location|
|EventHubThrottledEvents|Az EventHub által elindított események|Count|Összes|Az EventHub által elindított események száma|Location|
|EventHubTimedoutEvents|Az EventHub-események túllépte az időkorlátot|Count|Összes|Száma túllépte az időkorlátot az EventHub-események|Location|
|EventHubDroppedEvents|Az eldobott EventHub-események|Count|Összes|Események száma elérte a várólista maximális mérete miatt kihagyva|Location|
|EventHubTotalBytesSent|Az EventHub-események|Bájt|Összes|Az EventHub-események (bájt)|Location|
|Kérelmek|Kérelmek|Count|Összes|Átjárókérések|Hely, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok száma összesen|Count|Összes|A feladatok teljes száma|A Runbook állapota|
|TotalUpdateDeploymentRuns|Teljes frissítés központi telepítésének futtatásai|Count|Összes|Összes Szoftverfrissítés központi telepítésének futtatása|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Teljes frissítés üzembe helyezési gép futtatások|Count|Összes|Összes frissítés üzembe helyezési gép fut, a szoftverfrissítések központi telepítésének futtatása|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált magok száma|Count|Összes|A batch-fiókban lévő dedikált magok teljes száma|Nincs dimenzió|
|TotalNodeCount|Dedikált csomópontok száma|Count|Összes|A batch-fiókban lévő dedikált csomópontok száma összesen|Nincs dimenzió|
|LowPriorityCoreCount|LowPriority magok száma|Count|Összes|A batch-fiókban az alacsony prioritású magok teljes száma|Nincs dimenzió|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Count|Összes|A batch-fiókban az alacsony prioritású csomópontok száma összesen|Nincs dimenzió|
|CreatingNodeCount|Csomópontok száma létrehozása|Count|Összes|A létrehozott csomópontok száma|Nincs dimenzió|
|StartingNodeCount|Csomópontok száma indítása|Count|Összes|A kezdési csomópontok száma|Nincs dimenzió|
|WaitingForStartTaskNodeCount|Várakozás a kezdő tevékenység csomópontok száma|Count|Összes|A Start feladat befejezésére való várakozás csomópontok száma|Nincs dimenzió|
|StartTaskFailedNodeCount|Kezdő tevékenység sikertelen volt a csomópontok száma|Count|Összes|Ahol a feladat indítása sikertelen volt a csomópontok száma|Nincs dimenzió|
|IdleNodeCount|Inaktív csomópontok száma|Count|Összes|Tétlen csomópontok száma|Nincs dimenzió|
|OfflineNodeCount|A kapcsolat nélküli csomópontok száma|Count|Összes|A kapcsolat nélküli csomópontok száma|Nincs dimenzió|
|RebootingNodeCount|Rendszer újraindítása a csomópontok száma|Count|Összes|Rendszer újraindítása a csomópontok száma|Nincs dimenzió|
|ReimagingNodeCount|Csomópontok száma rendszerképének alaphelyzetbe állítása|Count|Összes|Csomópont rendszerképének alaphelyzetbe állítása száma|Nincs dimenzió|
|RunningNodeCount|Futó csomópontok száma|Count|Összes|Futó csomópontok száma|Nincs dimenzió|
|LeavingPoolNodeCount|Csomópontok száma készlet elhagyása|Count|Összes|A csomópontok a készlet elhagyása|Nincs dimenzió|
|UnusableNodeCount|Használhatatlan csomópontok száma|Count|Összes|Használhatatlan csomópontok száma|Nincs dimenzió|
|PreemptedNodeCount|A leállított csomópontok száma|Count|Összes|A leállított csomópontok száma|Nincs dimenzió|
|TaskStartEvent|Tevékenység indítása esemény|Count|Összes|Megkezdte az feladatok száma összesen|Nincs dimenzió|
|TaskCompleteEvent|Tevékenység kész esemény|Count|Összes|A befejezett feladatok száma összesen|Nincs dimenzió|
|TaskFailEvent|Tevékenység meghiúsult esemény|Count|Összes|A sikertelen állapotú befejezett feladatok száma összesen|Nincs dimenzió|
|PoolCreateEvent|Készlet létrehozása esemény|Count|Összes|Létrehozott gyűjtők száma összesen|Nincs dimenzió|
|PoolResizeStartEvent|Készlet átméretezésének indítása események|Count|Összes|Készlet átméretezi, amelyek elindultak száma összesen|Nincs dimenzió|
|PoolResizeCompleteEvent|Készlet átméretezése kész események|Count|Összes|Befejezett készlet átméretezi száma összesen|Nincs dimenzió|
|PoolDeleteStartEvent|Készlet törlésének indítása események|Count|Összes|Teljes száma, amelyek elindultak készlet törlése|Nincs dimenzió|
|PoolDeleteCompleteEvent|Készlet törlése kész események|Count|Összes|Befejezett készlet törlések száma összesen|Nincs dimenzió|
|JobDeleteCompleteEvent|Feladat befejezése események törlése|Count|Összes|Sikeresen törölt feladatok teljes száma.|Nincs dimenzió|
|JobDeleteStartEvent|Feladat kezdő-események törlése|Count|Összes|A törlendő kérő feladatok teljes száma.|Nincs dimenzió|
|JobDisableCompleteEvent|Feladat letiltása befejeződött esemény|Count|Összes|Sikerült letiltani feladatok teljes száma.|Nincs dimenzió|
|JobDisableStartEvent|Feladat letiltása indítási események|Count|Összes|Kért, le kell tiltani feladatok teljes száma.|Nincs dimenzió|
|JobStartEvent|Feladat indítása esemény|Count|Összes|Sikeresen elindult feladatok teljes száma.|Nincs dimenzió|
|JobTerminateCompleteEvent|Feladat leállítása befejeződött esemény|Count|Összes|Sikerült leállítani feladatok teljes száma.|Nincs dimenzió|
|JobTerminateStartEvent|Feladat leállítása indítása esemény|Count|Összes|Kért, megszakítandó feladatok teljes száma.|Nincs dimenzió|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakozott ügyfelek|Count|Maximum||ShardId|
|totalcommandsprocessed|Műveletek összesen|Count|Összes||ShardId|
|cachehits|Gyorsítótár-találatok|Count|Összes||ShardId|
|cachemisses|Gyorsítótár-tévesztések|Count|Összes||ShardId|
|getcommands|Beolvasása|Count|Összes||ShardId|
|setcommands|Csoportok|Count|Összes||ShardId|
|operationsPerSecond|Műveletek száma másodpercenként|Count|Maximum||ShardId|
|evictedkeys|Kizárt kulcsok|Count|Összes||ShardId|
|totalkeys|Kulcsok összesen|Count|Maximum||ShardId|
|expiredkeys|Lejárt kulcsok|Count|Összes||ShardId|
|usedmemory|Használt memória|Bájt|Maximum||ShardId|
|usedmemorypercentage|Használt memória százalékos aránya|Percent|Maximum||ShardId|
|usedmemoryRss|Használt memória RSS|Bájt|Maximum||ShardId|
|serverLoad|Kiszolgáló terhelése|Percent|Maximum||ShardId|
|cacheWrite|Gyorsítótár-írás|BytesPerSecond|Maximum||ShardId|
|cacheRead|Gyorsítótár-olvasás|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Percent|Maximum||ShardId|
|cacheLatency|Gyorsítótár késés mikroszekundum (előzetes verzió)|Count|Average||ShardId, SampleType|
|Hibák|Hibák|Count|Maximum||ShardId, ErrorType|
|connectedclients0|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 0)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed0|Műveletek összesen (horizontálisan Skálázáson 0)|Count|Összes||Nincs dimenzió|
|cachehits0|Cache Hits (Shard 0)|Count|Összes||Nincs dimenzió|
|cachemisses0|Cache Misses (Shard 0)|Count|Összes||Nincs dimenzió|
|getcommands0|Lekérdezi a (horizontálisan Skálázáson 0)|Count|Összes||Nincs dimenzió|
|setcommands0|Csoportok (horizontálisan Skálázáson 0)|Count|Összes||Nincs dimenzió|
|operationsPerSecond0|Műveletek száma másodpercenként (horizontálisan Skálázáson 0)|Count|Maximum||Nincs dimenzió|
|evictedkeys0|Kizárt kulcsok (horizontálisan Skálázáson 0)|Count|Összes||Nincs dimenzió|
|totalkeys0|Kulcsok összesen (horizontálisan Skálázáson 0)|Count|Maximum||Nincs dimenzió|
|expiredkeys0|Lejárt kulcsok (horizontálisan Skálázáson 0)|Count|Összes||Nincs dimenzió|
|usedmemory0|Használt memória (horizontálisan Skálázáson 0)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss0|Használt memória RSS (horizontálisan Skálázáson 0)|Bájt|Maximum||Nincs dimenzió|
|serverLoad0|Kiszolgáló terhelése (horizontálisan Skálázáson 0)|Percent|Maximum||Nincs dimenzió|
|cacheWrite0|Gyorsítótár-írás (horizontálisan Skálázáson 0)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead0|Gyorsítótár-olvasás (horizontálisan Skálázáson 0)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime0|Processzor (horizontálisan Skálázáson 0)|Percent|Maximum||Nincs dimenzió|
|connectedclients1|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 1)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed1|Műveletek összesen (horizontálisan Skálázáson 1)|Count|Összes||Nincs dimenzió|
|cachehits1|Cache Hits (Shard 1)|Count|Összes||Nincs dimenzió|
|cachemisses1|Gyorsítótárbeli (horizontálisan Skálázáson 1)|Count|Összes||Nincs dimenzió|
|getcommands1|(Horizontálisan Skálázáson 1) beolvasása|Count|Összes||Nincs dimenzió|
|setcommands1|Csoportok (horizontálisan Skálázáson 1)|Count|Összes||Nincs dimenzió|
|operationsPerSecond1|Műveletek száma másodpercenként (horizontálisan Skálázáson 1)|Count|Maximum||Nincs dimenzió|
|evictedkeys1|Kizárt kulcsok (horizontálisan Skálázáson 1)|Count|Összes||Nincs dimenzió|
|totalkeys1|Kulcsok összesen (horizontálisan Skálázáson 1)|Count|Maximum||Nincs dimenzió|
|expiredkeys1|Lejárt kulcsok (horizontálisan Skálázáson 1)|Count|Összes||Nincs dimenzió|
|usedmemory1|Használt memória (horizontálisan Skálázáson 1)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss1|Használt memória RSS (horizontálisan Skálázáson 1)|Bájt|Maximum||Nincs dimenzió|
|serverLoad1|Kiszolgáló terhelése (horizontálisan Skálázáson 1)|Percent|Maximum||Nincs dimenzió|
|cacheWrite1|Gyorsítótár-írás (horizontálisan Skálázáson 1)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead1|Gyorsítótár-olvasás (horizontálisan Skálázáson 1)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime1|Processzor (horizontálisan Skálázáson 1)|Percent|Maximum||Nincs dimenzió|
|connectedclients2|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 2)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed2|Műveletek összesen (horizontálisan Skálázáson 2)|Count|Összes||Nincs dimenzió|
|cachehits2|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 2)|Count|Összes||Nincs dimenzió|
|cachemisses2|Gyorsítótárbeli (horizontálisan Skálázáson 2)|Count|Összes||Nincs dimenzió|
|getcommands2|(2 horizontálisan Skálázáson) beolvasása|Count|Összes||Nincs dimenzió|
|setcommands2|Csoportok (horizontálisan Skálázáson 2)|Count|Összes||Nincs dimenzió|
|operationsPerSecond2|Műveletek száma másodpercenként (horizontálisan Skálázáson 2)|Count|Maximum||Nincs dimenzió|
|evictedkeys2|Kizárt kulcsok (horizontálisan Skálázáson 2)|Count|Összes||Nincs dimenzió|
|totalkeys2|Kulcsok összesen (horizontálisan Skálázáson 2)|Count|Maximum||Nincs dimenzió|
|expiredkeys2|Lejárt kulcsok (horizontálisan Skálázáson 2)|Count|Összes||Nincs dimenzió|
|usedmemory2|Használt memória (horizontálisan Skálázáson 2)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss2|Használt memória RSS (horizontálisan Skálázáson 2)|Bájt|Maximum||Nincs dimenzió|
|serverLoad2|Kiszolgáló terhelése (horizontálisan Skálázáson 2)|Percent|Maximum||Nincs dimenzió|
|cacheWrite2|Gyorsítótár-írás (horizontálisan Skálázáson 2)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead2|Gyorsítótár-olvasás (horizontálisan Skálázáson 2)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime2|Processzor (horizontálisan Skálázáson 2)|Percent|Maximum||Nincs dimenzió|
|connectedclients3|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 3)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed3|Műveletek összesen (horizontálisan Skálázáson 3)|Count|Összes||Nincs dimenzió|
|cachehits3|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 3)|Count|Összes||Nincs dimenzió|
|cachemisses3|Gyorsítótárbeli (horizontálisan Skálázáson 3)|Count|Összes||Nincs dimenzió|
|getcommands3|(3 horizontálisan Skálázáson) beolvasása|Count|Összes||Nincs dimenzió|
|setcommands3|Csoportok (horizontálisan Skálázáson 3)|Count|Összes||Nincs dimenzió|
|operationsPerSecond3|Műveletek száma másodpercenként (horizontálisan Skálázáson 3)|Count|Maximum||Nincs dimenzió|
|evictedkeys3|Kizárt kulcsok (horizontálisan Skálázáson 3)|Count|Összes||Nincs dimenzió|
|totalkeys3|Kulcsok összesen (horizontálisan Skálázáson 3)|Count|Maximum||Nincs dimenzió|
|expiredkeys3|Lejárt kulcsok (horizontálisan Skálázáson 3)|Count|Összes||Nincs dimenzió|
|usedmemory3|Használt memória (horizontálisan Skálázáson 3)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss3|Használt memória RSS (horizontálisan Skálázáson 3)|Bájt|Maximum||Nincs dimenzió|
|serverLoad3|Kiszolgáló terhelése (horizontálisan Skálázáson 3)|Percent|Maximum||Nincs dimenzió|
|cacheWrite3|Gyorsítótár-írás (horizontálisan Skálázáson 3)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead3|Gyorsítótár-olvasás (horizontálisan Skálázáson 3)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime3|Processzor (horizontálisan Skálázáson 3)|Percent|Maximum||Nincs dimenzió|
|connectedclients4|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 4)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed4|Műveletek összesen (horizontálisan Skálázáson 4)|Count|Összes||Nincs dimenzió|
|cachehits4|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 4)|Count|Összes||Nincs dimenzió|
|cachemisses4|Gyorsítótárbeli (horizontálisan Skálázáson 4)|Count|Összes||Nincs dimenzió|
|getcommands4|(4 horizontálisan Skálázáson) beolvasása|Count|Összes||Nincs dimenzió|
|setcommands4|Csoportok (horizontálisan Skálázáson 4)|Count|Összes||Nincs dimenzió|
|operationsPerSecond4|Műveletek száma másodpercenként (horizontálisan Skálázáson 4)|Count|Maximum||Nincs dimenzió|
|evictedkeys4|Kizárt kulcsok (horizontálisan Skálázáson 4)|Count|Összes||Nincs dimenzió|
|totalkeys4|Kulcsok összesen (horizontálisan Skálázáson 4)|Count|Maximum||Nincs dimenzió|
|expiredkeys4|Lejárt kulcsok (horizontálisan Skálázáson 4)|Count|Összes||Nincs dimenzió|
|usedmemory4|Használt memória (horizontálisan Skálázáson 4)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss4|Használt memória RSS (horizontálisan Skálázáson 4)|Bájt|Maximum||Nincs dimenzió|
|serverLoad4|Kiszolgáló terhelése (horizontálisan Skálázáson 4)|Percent|Maximum||Nincs dimenzió|
|cacheWrite4|Gyorsítótár-írás (horizontálisan Skálázáson 4)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead4|Gyorsítótár-olvasás (horizontálisan Skálázáson 4)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime4|Processzor (horizontálisan Skálázáson 4)|Percent|Maximum||Nincs dimenzió|
|connectedclients5|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 5)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed5|Műveletek összesen (horizontálisan Skálázáson 5)|Count|Összes||Nincs dimenzió|
|cachehits5|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 5)|Count|Összes||Nincs dimenzió|
|cachemisses5|Gyorsítótárbeli (horizontálisan Skálázáson 5)|Count|Összes||Nincs dimenzió|
|getcommands5|Lekérdezi a (horizontálisan Skálázáson 5)|Count|Összes||Nincs dimenzió|
|setcommands5|Csoportok (horizontálisan Skálázáson 5)|Count|Összes||Nincs dimenzió|
|operationsPerSecond5|Műveletek száma másodpercenként (horizontálisan Skálázáson 5)|Count|Maximum||Nincs dimenzió|
|evictedkeys5|Kizárt kulcsok (horizontálisan Skálázáson 5)|Count|Összes||Nincs dimenzió|
|totalkeys5|Kulcsok összesen (horizontálisan Skálázáson 5)|Count|Maximum||Nincs dimenzió|
|expiredkeys5|Lejárt kulcsok (horizontálisan Skálázáson 5)|Count|Összes||Nincs dimenzió|
|usedmemory5|Használt memória (horizontálisan Skálázáson 5)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss5|Használt memória RSS (horizontálisan Skálázáson 5)|Bájt|Maximum||Nincs dimenzió|
|serverLoad5|Kiszolgáló terhelése (horizontálisan Skálázáson 5)|Percent|Maximum||Nincs dimenzió|
|cacheWrite5|Gyorsítótár-írás (horizontálisan Skálázáson 5)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead5|Gyorsítótár-olvasás (horizontálisan Skálázáson 5)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime5|Processzor (horizontálisan Skálázáson 5)|Percent|Maximum||Nincs dimenzió|
|connectedclients6|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 6)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed6|Műveletek összesen (horizontálisan Skálázáson 6)|Count|Összes||Nincs dimenzió|
|cachehits6|Cache Hits (Shard 6)|Count|Összes||Nincs dimenzió|
|cachemisses6|Gyorsítótárbeli (horizontálisan Skálázáson 6)|Count|Összes||Nincs dimenzió|
|getcommands6|Lekérdezi a (horizontálisan Skálázáson 6)|Count|Összes||Nincs dimenzió|
|setcommands6|Csoportok (horizontálisan Skálázáson 6)|Count|Összes||Nincs dimenzió|
|operationsPerSecond6|Műveletek száma másodpercenként (horizontálisan Skálázáson 6)|Count|Maximum||Nincs dimenzió|
|evictedkeys6|Kizárt kulcsok (horizontálisan Skálázáson 6)|Count|Összes||Nincs dimenzió|
|totalkeys6|Kulcsok összesen (horizontálisan Skálázáson 6)|Count|Maximum||Nincs dimenzió|
|expiredkeys6|Lejárt kulcsok (horizontálisan Skálázáson 6)|Count|Összes||Nincs dimenzió|
|usedmemory6|Használt memória (horizontálisan Skálázáson 6)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss6|Használt memória RSS (horizontálisan Skálázáson 6)|Bájt|Maximum||Nincs dimenzió|
|serverLoad6|Kiszolgáló terhelése (horizontálisan Skálázáson 6)|Percent|Maximum||Nincs dimenzió|
|cacheWrite6|Gyorsítótár-írás (horizontálisan Skálázáson 6)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead6|Gyorsítótár-olvasás (horizontálisan Skálázáson 6)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime6|Processzor (horizontálisan Skálázáson 6)|Percent|Maximum||Nincs dimenzió|
|connectedclients7|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 7)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed7|Műveletek összesen (horizontálisan Skálázáson 7)|Count|Összes||Nincs dimenzió|
|cachehits7|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 7)|Count|Összes||Nincs dimenzió|
|cachemisses7|Gyorsítótárbeli (horizontálisan Skálázáson 7)|Count|Összes||Nincs dimenzió|
|getcommands7|Lekérdezi a (horizontálisan Skálázáson 7)|Count|Összes||Nincs dimenzió|
|setcommands7|Csoportok (horizontálisan Skálázáson 7)|Count|Összes||Nincs dimenzió|
|operationsPerSecond7|Műveletek száma másodpercenként (horizontálisan Skálázáson 7)|Count|Maximum||Nincs dimenzió|
|evictedkeys7|Kizárt kulcsok (horizontálisan Skálázáson 7)|Count|Összes||Nincs dimenzió|
|totalkeys7|Kulcsok összesen (horizontálisan Skálázáson 7)|Count|Maximum||Nincs dimenzió|
|expiredkeys7|Lejárt kulcsok (horizontálisan Skálázáson 7)|Count|Összes||Nincs dimenzió|
|usedmemory7|Használt memória (horizontálisan Skálázáson 7)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss7|Használt memória RSS (horizontálisan Skálázáson 7)|Bájt|Maximum||Nincs dimenzió|
|serverLoad7|Kiszolgáló terhelése (horizontálisan Skálázáson 7)|Percent|Maximum||Nincs dimenzió|
|cacheWrite7|Gyorsítótár-írás (horizontálisan Skálázáson 7)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead7|Gyorsítótár-olvasás (horizontálisan Skálázáson 7)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime7|Processzor (horizontálisan Skálázáson 7)|Percent|Maximum||Nincs dimenzió|
|connectedclients8|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 8)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed8|Műveletek összesen (horizontálisan Skálázáson 8)|Count|Összes||Nincs dimenzió|
|cachehits8|Cache Hits (Shard 8)|Count|Összes||Nincs dimenzió|
|cachemisses8|Gyorsítótárbeli (horizontálisan Skálázáson 8)|Count|Összes||Nincs dimenzió|
|getcommands8|Lekérdezi a (horizontálisan Skálázáson 8)|Count|Összes||Nincs dimenzió|
|setcommands8|Csoportok (horizontálisan Skálázáson 8)|Count|Összes||Nincs dimenzió|
|operationsPerSecond8|Műveletek száma másodpercenként (horizontálisan Skálázáson 8)|Count|Maximum||Nincs dimenzió|
|evictedkeys8|Kizárt kulcsok (horizontálisan Skálázáson 8)|Count|Összes||Nincs dimenzió|
|totalkeys8|Kulcsok összesen (horizontálisan Skálázáson 8)|Count|Maximum||Nincs dimenzió|
|expiredkeys8|Lejárt kulcsok (horizontálisan Skálázáson 8)|Count|Összes||Nincs dimenzió|
|usedmemory8|Használt memória (horizontálisan Skálázáson 8)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss8|Használt memória RSS (horizontálisan Skálázáson 8)|Bájt|Maximum||Nincs dimenzió|
|serverLoad8|Kiszolgáló terhelése (horizontálisan Skálázáson 8)|Percent|Maximum||Nincs dimenzió|
|cacheWrite8|Gyorsítótár-írás (horizontálisan Skálázáson 8)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead8|Gyorsítótár-olvasás (horizontálisan Skálázáson 8)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime8|Processzor (horizontálisan Skálázáson 8)|Percent|Maximum||Nincs dimenzió|
|connectedclients9|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 9)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed9|Műveletek összesen (horizontálisan Skálázáson 9)|Count|Összes||Nincs dimenzió|
|cachehits9|Cache Hits (Shard 9)|Count|Összes||Nincs dimenzió|
|cachemisses9|Gyorsítótárbeli (horizontálisan Skálázáson 9)|Count|Összes||Nincs dimenzió|
|getcommands9|Lekérdezi a (horizontálisan Skálázáson 9)|Count|Összes||Nincs dimenzió|
|setcommands9|Csoportok (horizontálisan Skálázáson 9)|Count|Összes||Nincs dimenzió|
|operationsPerSecond9|Műveletek száma másodpercenként (horizontálisan Skálázáson 9)|Count|Maximum||Nincs dimenzió|
|evictedkeys9|Kizárt kulcsok (horizontálisan Skálázáson 9)|Count|Összes||Nincs dimenzió|
|totalkeys9|Kulcsok összesen (horizontálisan Skálázáson 9)|Count|Maximum||Nincs dimenzió|
|expiredkeys9|Lejárt kulcsok (horizontálisan Skálázáson 9)|Count|Összes||Nincs dimenzió|
|usedmemory9|Használt memória (horizontálisan Skálázáson 9)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss9|Használt memória RSS (horizontálisan Skálázáson 9)|Bájt|Maximum||Nincs dimenzió|
|serverLoad9|Kiszolgáló terhelése (horizontálisan Skálázáson 9)|Percent|Maximum||Nincs dimenzió|
|cacheWrite9|Gyorsítótár-írás (horizontálisan Skálázáson 9)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead9|Gyorsítótár-olvasás (horizontálisan Skálázáson 9)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime9|Processzor (horizontálisan Skálázáson 9)|Percent|Maximum||Nincs dimenzió|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Average|Jelenleg a virtuális gép(ek) által használt lefoglalt számítási egységek százalékos értéke.|Nincs dimenzió|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|A virtuális gép (bejövő forgalom) által az összes hálózati adapteren fogadott bájtok száma.|Nincs dimenzió|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|A hálózati adapteren elküldött összes (kimenő forgalom) virtuális gép(ek) által bájtok száma.|Nincs dimenzió|
|Lemez sebessége olvasott bájt/mp|Lemezolvasás|BytesPerSecond|Average|A figyelési időszak során lemezről beolvasott bájtok átlagos száma.|Nincs dimenzió|
|Lemez írási bájt/mp-ben|Lemezírás|BytesPerSecond|Average|A figyelési időszak során lemezre írt átlagos bájtok száma.|Nincs dimenzió|
|Lemezolvasási művelet/mp|Lemezolvasási művelet/mp|CountPerSecond|Average|Lemez olvasási iops-t.|Nincs dimenzió|
|Lemezre írási művelet/mp|Lemezre írási művelet/mp|CountPerSecond|Average|Lemezre írási iops-t.|Nincs dimenzió|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Average|Jelenleg a virtuális gép(ek) által használt lefoglalt számítási egységek százalékos értéke.|RoleInstanceId|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|A virtuális gép (bejövő forgalom) által az összes hálózati adapteren fogadott bájtok száma.|RoleInstanceId|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|A hálózati adapteren elküldött összes (kimenő forgalom) virtuális gép(ek) által bájtok száma.|RoleInstanceId|
|Lemez sebessége olvasott bájt/mp|Lemezolvasás|BytesPerSecond|Average|A figyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez írási bájt/mp-ben|Lemezírás|BytesPerSecond|Average|A figyelési időszak során lemezre írt átlagos bájtok száma.|RoleInstanceId|
|Lemezolvasási művelet/mp|Lemezolvasási művelet/mp|CountPerSecond|Average|Lemez olvasási iops-t.|RoleInstanceId|
|Lemezre írási művelet/mp|Lemezre írási művelet/mp|CountPerSecond|Average|Lemezre írási iops-t.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalCalls|Összes hívás|Count|Összes|Hívások teljes száma.|ApiName, OperationName, régió|
|SuccessfulCalls|Sikeres hívások|Count|Összes|Sikeres hívások száma.|ApiName, OperationName, régió|
|TotalErrors|Az összes hiba|Count|Összes|A hibaválaszt (HTTP-válaszkód: 4xx vagy 5xx) hívások teljes száma.|ApiName, OperationName, régió|
|BlockedCalls|Blokkolt hívások|Count|Összes|Hívások száma, hogy túllépte a sebesség- vagy magkvótájának határértékét.|ApiName, OperationName, régió|
|ServerErrors|Kiszolgálóhibák|Count|Összes|Belső szolgáltatáshiba (HTTP válaszkód: 5xx)-hívások száma.|ApiName, OperationName, régió|
|ClientErrors|Ügyfél-hibák|Count|Összes|Az ügyféloldali hibába (HTTP válaszkód: 4xx) hívások száma.|ApiName, OperationName, régió|
|DataIn|Az adatok|Bájt|Összes|Bejövő adatmennyiség bájtban.|ApiName, OperationName, régió|
|DataOut|Kimenő adatforgalom|Bájt|Összes|Kimenő adatmennyiség bájtban.|ApiName, OperationName, régió|
|Késés|Késés|Ideje ezredmásodpercben|Average|Késés (MS).|ApiName, OperationName, régió|
|CharactersTranslated|Lefordított karakterek|Count|Összes|A bejövő szöveges kérelem karakterek száma.|ApiName, OperationName, régió|
|CharactersTrained|Betanított karakter|Count|Összes|Betanított karakterből állhat.|ApiName, OperationName, régió|
|SpeechSessionDuration|Beszédfelismerési munkamenet időtartama|másodperc|Összes|A beszédfelismerési munkamenet teljes időtartama.|ApiName, OperationName, régió|
|TotalTransactions|Teljes tranzakció|Count|Összes|Tranzakciók teljes számával.|Nincs dimenzió|
|TotalTokenCalls|Az összes jogkivonat hívás|Count|Összes|Token hívások teljes száma.|ApiName, OperationName, régió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincs dimenzió|
|Bejövő hálózat|A számlázandó hálózati|Bájt|Összes|Számlázható, (bejövő forgalom) virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma.|Nincs dimenzió|
|Kimenő hálózat|A számlázható kimenő hálózati|Bájt|Összes|A hálózati adapteren elküldött összes virtuális gép (kimenő forgalom) alapján számlázható bájtok száma|Nincs dimenzió|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|Nincs dimenzió|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|A figyelési időszak során lemezre írt bájtok száma|Nincs dimenzió|
|Lemezolvasási művelet/mp|Lemezolvasási művelet/mp|CountPerSecond|Average|Lemez olvasási iops-érték|Nincs dimenzió|
|Lemezre írási művelet/mp|Lemezre írási művelet/mp|CountPerSecond|Average|Lemezre írási iops-érték|Nincs dimenzió|
|Fennmaradó Processzorkreditek|Fennmaradó Processzorkreditek|Count|Average|Adatlökethez rendelkezésre álló kreditek száma összesen|Nincs dimenzió|
|Felhasznált Processzorkreditek|Felhasznált Processzorkreditek|Count|Average|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|
|Lemezenkénti olvasási bájt/mp|Adatlemez-olvasási bájt/mp (elavult)|CountPerSecond|Average|Figyelési időszak során egyetlen lemezről beolvasott bájtok/s|Helyet|
|Minden lemez Zapsané Bajty/s|Adatok lemezre írási bájt/mp (elavult)|CountPerSecond|Average|A figyelési időszak során egyetlen lemezre írt bájtok/s|Helyet|
|Lemezenkénti olvasási művelet/mp|Adatlemez-olvasási művelet/mp (elavult)|CountPerSecond|Average|A figyelési időszak során egyetlen lemezről olvasási iops-érték|Helyet|
|Lemezre írási műveletek másodpercenkénti száma|Adatok lemezre írási művelet/mp (elavult)|CountPerSecond|Average|Írási iops-t egyetlen, a figyelési időszak során|Helyet|
|Várólista lemezenkénti Mélysége|Adatlemez QD (elavult)|Count|Average|Adatok adatlemez várólistájának mélysége (vagy hossza)|Helyet|
|Operációs Rendszerenkénti lemez olvasási bájt/mp|Operációsrendszer-lemez olvasásának sebessége bájt/mp (elavult)|CountPerSecond|Average|Figyelési operációsrendszer-lemez időszak során egyetlen lemezről beolvasott bájtok/s|Nincs dimenzió|
|Lemezírás operációs Rendszerenkénti sebessége bájt/mp|Operációsrendszer-lemez Zapsané Bajty/s (elavult)|CountPerSecond|Average|Figyelési operációsrendszer-lemez időszak során egyetlen lemezre írt bájtok/s|Nincs dimenzió|
|Operációsrendszer-lemez olvasási műveletek másodpercenkénti száma|Operációsrendszer-lemez olvasási művelet/mp (elavult)|CountPerSecond|Average|A figyelési operációsrendszer-lemez időszak során egyetlen lemezről olvasási iops-érték|Nincs dimenzió|
|Lemezírás operációs Rendszerenkénti művelet/mp|Operációsrendszer-lemez írási művelet/mp (elavult)|CountPerSecond|Average|Egyetlen, a monitorozási időszakban operációsrendszer-lemez írási IOPS|Nincs dimenzió|
|Operációs Rendszerenkénti Mélysége|Az operációs rendszer lemez várólistájának Mélysége (elavult)|Count|Average|Operációsrendszer-lemez várólistájának mélysége (vagy hossza)|Nincs dimenzió|
|Adatlemez-olvasási bájt/mp|Adatlemez-olvasási bájt/mp (előzetes verzió)|CountPerSecond|Average|Figyelési időszak során egyetlen lemezről beolvasott bájtok/s|LOGIKAI EGYSÉG|
|Adatok lemez Zapsané Bajty/s|Adatok lemez Zapsané Bajty/s (előzetes verzió)|CountPerSecond|Average|A figyelési időszak során egyetlen lemezre írt bájtok/s|LOGIKAI EGYSÉG|
|Adatlemez-olvasási művelet/mp|Adatlemez-olvasási művelet/s (előzetes verzió)|CountPerSecond|Average|A figyelési időszak során egyetlen lemezről olvasási iops-érték|LOGIKAI EGYSÉG|
|Adatok lemezre írási művelet/mp|Adatok lemezre írási művelet/s (előzetes verzió)|CountPerSecond|Average|Írási iops-t egyetlen, a figyelési időszak során|LOGIKAI EGYSÉG|
|Adatok adatlemez várólistájának mélysége|Adatok adatlemez várólistájának mélysége (előzetes verzió)|Count|Average|Adatok adatlemez várólistájának mélysége (vagy hossza)|LOGIKAI EGYSÉG|
|Operációsrendszer-lemez olvasásának sebessége bájt/mp|Operációsrendszer-lemez olvasásának sebessége bájt/mp (előzetes verzió)|CountPerSecond|Average|Figyelési operációsrendszer-lemez időszak során egyetlen lemezről beolvasott bájtok/s|Nincs dimenzió|
|Operációsrendszer-lemez Zapsané Bajty/s|Operációsrendszer-lemez Zapsané Bajty/s (előzetes verzió)|CountPerSecond|Average|Figyelési operációsrendszer-lemez időszak során egyetlen lemezre írt bájtok/s|Nincs dimenzió|
|Operációsrendszer-lemez olvasási művelet/mp|Operációsrendszer-lemez olvasási művelet/s (előzetes verzió)|CountPerSecond|Average|A figyelési operációsrendszer-lemez időszak során egyetlen lemezről olvasási iops-érték|Nincs dimenzió|
|Operációsrendszer-lemez írási művelet/mp|Operációsrendszer-lemez írási művelet/s (előzetes verzió)|CountPerSecond|Average|Egyetlen, a monitorozási időszakban operációsrendszer-lemez írási IOPS|Nincs dimenzió|
|Operációsrendszer-lemez várólistájának mélysége|Operációsrendszer-lemez várólistájának mélysége (előzetes verzió)|Count|Average|Operációsrendszer-lemez várólistájának mélysége (vagy hossza)|Nincs dimenzió|
|Bejövő forgalom|Bejövő forgalom (előzetes verzió)|Count|Average|Bejövő forgalom száma a jelenlegi forgalom a bejövő irányban (a forgalom belép a virtuális gép)|Nincs dimenzió|
|Kimenő forgalom|Kimenő forgalom (előzetes verzió)|Count|Average|Kimenő forgalom szám aktuális flow a kimenő irányban (a forgalom a virtuális gép)|Nincs dimenzió|
|Bejövő forgalom maximális létrehozásának sebessége|Bejövő forgalom maximális gyakorisága (előzetes verzió)|CountPerSecond|Average|Bejövő forgalom (a forgalom belép a virtuális gép) a maximális gyakorisága|Nincs dimenzió|
|Kimenő forgalom maximális létrehozásának sebessége|Kimenő forgalom maximális gyakorisága (előzetes verzió)|CountPerSecond|Average|A kimenő forgalom (a forgalom a virtuális gép) maximális létrehozási aránya|Nincs dimenzió|
|Prémium szintű olvasható adatok lemez Taggyorsítótárának találati aránya|Prémium szintű adatok lemezolvasási gyorsítótár találati (előzetes verzió)|Percent|Average|Prémium szintű olvasható adatok lemez Taggyorsítótárának találati aránya|LOGIKAI EGYSÉG|
|Prémium szintű lemez gyorsítótár olvasási tévesztési|Prémium szintű lemez gyorsítótár olvasási tévesztési (előzetes verzió)|Percent|Average|Prémium szintű lemez gyorsítótár olvasási tévesztési|LOGIKAI EGYSÉG|
|Kattintson a prémium szintű operációsrendszer-lemez gyorsítótár-Olvasás|Prémium szintű operációsrendszer-lemez gyorsítótár-olvasás találati (előzetes verzió)|Percent|Average|Kattintson a prémium szintű operációsrendszer-lemez gyorsítótár-Olvasás|Nincs dimenzió|
|Premium OS Disk Cache Read Miss|Prémium szintű operációs rendszer lemez gyorsítótár olvasási tévesztés (előzetes verzió)|Percent|Average|Premium OS Disk Cache Read Miss|Nincs dimenzió|
|Hálózati összesen|Hálózati összesen|Bájt|Összes|(Bejövő forgalom) virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma.|Nincs dimenzió|
|Hálózati ki összesen|Hálózati ki összesen|Bájt|Összes|A hálózati adapteren elküldött összes (kimenő forgalom) virtuális gép(ek) által bájtok száma|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Bejövő hálózat|A számlázandó hálózati|Bájt|Összes|Számlázható, (bejövő forgalom) virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma.|VMName|
|Kimenő hálózat|A számlázható kimenő hálózati|Bájt|Összes|A hálózati adapteren elküldött összes virtuális gép (kimenő forgalom) alapján számlázható bájtok száma|VMName|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|A figyelési időszak során lemezre írt bájtok száma|VMName|
|Lemezolvasási művelet/mp|Lemezolvasási művelet/mp|CountPerSecond|Average|Lemez olvasási iops-érték|VMName|
|Lemezre írási művelet/mp|Lemezre írási művelet/mp|CountPerSecond|Average|Lemezre írási iops-érték|VMName|
|Fennmaradó Processzorkreditek|Fennmaradó Processzorkreditek|Count|Average|Adatlökethez rendelkezésre álló kreditek száma összesen|Nincs dimenzió|
|Felhasznált Processzorkreditek|Felhasznált Processzorkreditek|Count|Average|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|
|Lemezenkénti olvasási bájt/mp|Adatlemez-olvasási bájt/mp (elavult)|CountPerSecond|Average|Figyelési időszak során egyetlen lemezről beolvasott bájtok/s|Helyet|
|Minden lemez Zapsané Bajty/s|Adatok lemezre írási bájt/mp (elavult)|CountPerSecond|Average|A figyelési időszak során egyetlen lemezre írt bájtok/s|Helyet|
|Lemezenkénti olvasási művelet/mp|Adatlemez-olvasási művelet/mp (elavult)|CountPerSecond|Average|A figyelési időszak során egyetlen lemezről olvasási iops-érték|Helyet|
|Lemezre írási műveletek másodpercenkénti száma|Adatok lemezre írási művelet/mp (elavult)|CountPerSecond|Average|Írási iops-t egyetlen, a figyelési időszak során|Helyet|
|Várólista lemezenkénti Mélysége|Adatlemez QD (elavult)|Count|Average|Adatok adatlemez várólistájának mélysége (vagy hossza)|Helyet|
|Operációs Rendszerenkénti lemez olvasási bájt/mp|Operációsrendszer-lemez olvasásának sebessége bájt/mp (elavult)|CountPerSecond|Average|Figyelési operációsrendszer-lemez időszak során egyetlen lemezről beolvasott bájtok/s|Nincs dimenzió|
|Lemezírás operációs Rendszerenkénti sebessége bájt/mp|Operációsrendszer-lemez Zapsané Bajty/s (elavult)|CountPerSecond|Average|Figyelési operációsrendszer-lemez időszak során egyetlen lemezre írt bájtok/s|Nincs dimenzió|
|Operációsrendszer-lemez olvasási műveletek másodpercenkénti száma|Operációsrendszer-lemez olvasási művelet/mp (elavult)|CountPerSecond|Average|A figyelési operációsrendszer-lemez időszak során egyetlen lemezről olvasási iops-érték|Nincs dimenzió|
|Lemezírás operációs Rendszerenkénti művelet/mp|Operációsrendszer-lemez írási művelet/mp (elavult)|CountPerSecond|Average|Egyetlen, a monitorozási időszakban operációsrendszer-lemez írási IOPS|Nincs dimenzió|
|Operációs Rendszerenkénti Mélysége|Az operációs rendszer lemez várólistájának Mélysége (elavult)|Count|Average|Operációsrendszer-lemez várólistájának mélysége (vagy hossza)|Nincs dimenzió|
|Adatlemez-olvasási bájt/mp|Adatlemez-olvasási bájt/mp (előzetes verzió)|CountPerSecond|Average|Figyelési időszak során egyetlen lemezről beolvasott bájtok/s|Logikai egység, VMName|
|Adatok lemez Zapsané Bajty/s|Adatok lemez Zapsané Bajty/s (előzetes verzió)|CountPerSecond|Average|A figyelési időszak során egyetlen lemezre írt bájtok/s|Logikai egység, VMName|
|Adatlemez-olvasási művelet/mp|Adatlemez-olvasási művelet/s (előzetes verzió)|CountPerSecond|Average|A figyelési időszak során egyetlen lemezről olvasási iops-érték|Logikai egység, VMName|
|Adatok lemezre írási művelet/mp|Adatok lemezre írási művelet/s (előzetes verzió)|CountPerSecond|Average|Írási iops-t egyetlen, a figyelési időszak során|Logikai egység, VMName|
|Adatok adatlemez várólistájának mélysége|Adatok adatlemez várólistájának mélysége (előzetes verzió)|Count|Average|Adatok adatlemez várólistájának mélysége (vagy hossza)|Logikai egység, VMName|
|Operációsrendszer-lemez olvasásának sebessége bájt/mp|Operációsrendszer-lemez olvasásának sebessége bájt/mp (előzetes verzió)|CountPerSecond|Average|Figyelési operációsrendszer-lemez időszak során egyetlen lemezről beolvasott bájtok/s|VMName|
|Operációsrendszer-lemez Zapsané Bajty/s|Operációsrendszer-lemez Zapsané Bajty/s (előzetes verzió)|CountPerSecond|Average|Figyelési operációsrendszer-lemez időszak során egyetlen lemezre írt bájtok/s|VMName|
|Operációsrendszer-lemez olvasási művelet/mp|Operációsrendszer-lemez olvasási művelet/s (előzetes verzió)|CountPerSecond|Average|A figyelési operációsrendszer-lemez időszak során egyetlen lemezről olvasási iops-érték|VMName|
|Operációsrendszer-lemez írási művelet/mp|Operációsrendszer-lemez írási művelet/s (előzetes verzió)|CountPerSecond|Average|Egyetlen, a monitorozási időszakban operációsrendszer-lemez írási IOPS|VMName|
|Operációsrendszer-lemez várólistájának mélysége|Operációsrendszer-lemez várólistájának mélysége (előzetes verzió)|Count|Average|Operációsrendszer-lemez várólistájának mélysége (vagy hossza)|VMName|
|Bejövő forgalom|Bejövő forgalom (előzetes verzió)|Count|Average|Bejövő forgalom száma a jelenlegi forgalom a bejövő irányban (a forgalom belép a virtuális gép)|VMName|
|Kimenő forgalom|Kimenő forgalom (előzetes verzió)|Count|Average|Kimenő forgalom szám aktuális flow a kimenő irányban (a forgalom a virtuális gép)|VMName|
|Bejövő forgalom maximális létrehozásának sebessége|Bejövő forgalom maximális gyakorisága (előzetes verzió)|CountPerSecond|Average|Bejövő forgalom (a forgalom belép a virtuális gép) a maximális gyakorisága|VMName|
|Kimenő forgalom maximális létrehozásának sebessége|Kimenő forgalom maximális gyakorisága (előzetes verzió)|CountPerSecond|Average|A kimenő forgalom (a forgalom a virtuális gép) maximális létrehozási aránya|VMName|
|Prémium szintű olvasható adatok lemez Taggyorsítótárának találati aránya|Prémium szintű adatok lemezolvasási gyorsítótár találati (előzetes verzió)|Percent|Average|Prémium szintű olvasható adatok lemez Taggyorsítótárának találati aránya|Logikai egység, VMName|
|Prémium szintű lemez gyorsítótár olvasási tévesztési|Prémium szintű lemez gyorsítótár olvasási tévesztési (előzetes verzió)|Percent|Average|Prémium szintű lemez gyorsítótár olvasási tévesztési|Logikai egység, VMName|
|Kattintson a prémium szintű operációsrendszer-lemez gyorsítótár-Olvasás|Prémium szintű operációsrendszer-lemez gyorsítótár-olvasás találati (előzetes verzió)|Percent|Average|Kattintson a prémium szintű operációsrendszer-lemez gyorsítótár-Olvasás|VMName|
|Premium OS Disk Cache Read Miss|Prémium szintű operációs rendszer lemez gyorsítótár olvasási tévesztés (előzetes verzió)|Percent|Average|Premium OS Disk Cache Read Miss|VMName|
|Hálózati összesen|Hálózati összesen|Bájt|Összes|(Bejövő forgalom) virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma.|VMName|
|Hálózati ki összesen|Hálózati ki összesen|Bájt|Összes|A hálózati adapteren elküldött összes (kimenő forgalom) virtuális gép(ek) által bájtok száma|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|CPU-használat|Count|Average|Processzorhasználat az összes magon millicore-ban.|containerName|
|MemoryUsage|Memóriahasználat|Bájt|Average|Teljes memóriahasználat bájtban.|containerName|
|NetworkBytesReceivedPerSecond|A másodpercenként fogadott hálózati bájtok|Bájt|Average|A másodpercenként fogadott hálózati bájtok.|Nincs dimenzió|
|NetworkBytesTransmittedPerSecond|Másodpercenként továbbított hálózati bájtok|Bájt|Average|A másodpercenként továbbított hálózati bájtok.|Nincs dimenzió|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalPullCount|A lekéréses teljes száma|Count|Average|Összesen lekéri a rendszerképet száma|Nincs dimenzió|
|SuccessfulPullCount|A sikeres lekéréses száma|Count|Average|A sikeres kép lekéri száma|Nincs dimenzió|
|TotalPushCount|Leküldéses teljes száma|Count|Average|Leküldéses értesítések lemezkép száma összesen|Nincs dimenzió|
|SuccessfulPushCount|A sikeres Ügyfélleküldéses száma|Count|Average|A sikeres kép leküldések száma|Nincs dimenzió|
|RunDuration|Futtatás időtartama|Ezredmásodperc|Összes|Futtatás időtartama (MS)|Nincs dimenzió|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Egy felügyelt fürt rendelkezésre álló Processzormagok száma összesen|Count|Összes|Egy felügyelt fürt rendelkezésre álló Processzormagok száma összesen|Nincs dimenzió|
|kube_node_status_allocatable_memory_bytes|Egy felügyelt fürt rendelkezésre álló memória teljes mennyisége|Bájt|Összes|Egy felügyelt fürt rendelkezésre álló memória teljes mennyisége|Nincs dimenzió|
|kube_pod_status_ready|Kész állapotú podok száma|Count|Összes|Kész állapotú podok száma|névtér, -pod|
|kube_node_status_condition|A különböző csomópont-feltételek állapota|Count|Összes|A különböző csomópont-feltételek állapota|feltétel, az állapot, a állapota 2, a csomópont|
|kube_pod_status_phase|A fázis a podok számát|Count|Összes|A fázis a podok számát|fázis, a névteret, a pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API-hívások|Count|Összes||Nincs dimenzió|
|DCIMappingImportOperationSuccessfulLines|Leképezés importálási művelet sikeres sorok|Count|Összes||Nincs dimenzió|
|DCIMappingImportOperationFailedLines|Sorok leképezése az importálási művelet sikertelen volt.|Count|Összes||Nincs dimenzió|
|DCIMappingImportOperationTotalLines|Leképezés importálási művelet teljes sorok|Count|Összes||Nincs dimenzió|
|DCIMappingImportOperationRuntimeInSeconds|Leképezés importálási művelet futásidejű másodpercek alatt|másodperc|Összes||Nincs dimenzió|
|DCIOutboundProfileExportSucceeded|Kimenő profil exportálása sikerült|Count|Összes||Nincs dimenzió|
|DCIOutboundProfileExportFailed|Kimenő profil exportálása nem sikerült|Count|Összes||Nincs dimenzió|
|DCIOutboundProfileExportDuration|Kimenő profil exportálása időtartama|másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Count|Összes||Nincs dimenzió|
|DCIOutboundKpiExportFailed|Kimenő Kpi exportálása nem sikerült|Count|Összes||Nincs dimenzió|
|DCIOutboundKpiExportDuration|Kimenő Kpi exportálási időtartama|másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiExportStarted|Kimenő Kpi-exportálás elindítva|másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|másodperc|Összes||Nincs dimenzió|
|DCIOutboundProfileExportCount|Kimenő profil exportálása száma|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportFailed|Nem sikerült kezdeti kimenő profil exportálása|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportSucceeded|Kezdeti kimenő profil exportálása sikerült|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialKpiExportFailed|Kimenő kezdeti Kpi exportálása nem sikerült|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialKpiExportSucceeded|Kimenő kezdeti Kpi exportálása sikerült|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportDurationInSeconds|Kezdeti kimenő profil exportálása időtartama (másodpercben)|másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiFailed|Adla feladat Standard KPI másodpercben sikertelen|másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|másodperc|Összes||Nincs dimenzió|
|ImportASAValuesFailed|Importálás ASA értékek száma nem sikerült|Count|Összes||Nincs dimenzió|
|ImportASAValuesSucceeded|Importálás ASA értékek száma sikeres volt|Count|Összes||Nincs dimenzió|
|DCIProfilesCount|Profil példányok száma|Count|Vezetéknév||Nincs dimenzió|
|DCIInteractionsPerMonthCount|Interakciók hónap száma|Count|Vezetéknév||Nincs dimenzió|
|DCIKpisCount|KPI száma|Count|Vezetéknév||Nincs dimenzió|
|DCISegmentsCount|Szegmensek száma|Count|Vezetéknév||Nincs dimenzió|
|DCIPredictiveMatchPoliciesCount|Prediktív egyezés száma|Count|Vezetéknév||Nincs dimenzió|
|DCIPredictionsCount|Prediction Count|Count|Vezetéknév||Nincs dimenzió|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|NICReadThroughput|Olvasás átviteli sebesség (hálózat)|BytesPerSecond|Average|Az olvasás átviteli sebességét a hálózati adapter a jelentéskészítési időszakban az átjáró összes kötet az eszközön.|Példánynév|
|NICWriteThroughput|Írás az átviteli sebesség (hálózat)|BytesPerSecond|Average|A lemezírás teljesítménye, a hálózati adapter a jelentéskészítési időszakban az átjáró összes kötet az eszközön.|Példánynév|
|CloudReadThroughputPerShare|Felhőalapú letöltési átviteli sebesség (megosztás)|BytesPerSecond|Average|A letöltési átviteli az Azure-bA a jelentési időszak során megosztásból.|Megosztás|
|CloudUploadThroughputPerShare|Felhőbeli feltöltési sebessége (megosztás)|BytesPerSecond|Average|A feltöltési sebesség az Azure-bA a jelentési időszak során megosztásból.|Megosztás|
|BytesUploadedToCloudPerShare|Felhőalapú bájt feltöltött (megosztás)|Bájt|Average|A bájtok száma, amelyek megosztásból feltöltése az Azure-ba, a jelentési időszak során.|Megosztás|
|Teljes kapacitás|Teljes kapacitás|Bájt|Average|Teljes kapacitás|Nincs dimenzió|
|AvailableCapacity|Használható kapacitás|Bájt|Average|A rendelkezésre álló kapacitás (bájt) a jelentési időszak során.|Nincs dimenzió|
|CloudUploadThroughput|Felhőbeli feltöltési sebessége|BytesPerSecond|Average|A felhő feltöltési sebesség az Azure-bA a jelentési időszak során.|Nincs dimenzió|
|CloudReadThroughput|Felhőalapú letöltési átviteli sebesség|BytesPerSecond|Average|A felhő letöltési átviteli sebesség az Azure-bA a jelentési időszak során.|Nincs dimenzió|
|BytesUploadedToCloud|Felhőalapú bájt feltöltött (eszköz)|Bájt|Average|A bájtok száma, amely az eszközről feltöltése az Azure-ba, a jelentési időszak során.|Nincs dimenzió|
|HyperVVirtualProcessorUtilization|Edge-Compute - százalékos Processzorhasználat|Percent|Average|Százalékos processzorhasználat|Példánynév|
|HyperVMemoryUtilization|Él Compute - memória használata|Percent|Average|Használatban lévő RAM mennyisége|Példánynév|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Sikertelen futtatások|Count|Összes||pipelineName, activityName|
|SuccessfulRuns|Sikeres futtatások|Count|Összes||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Nem sikerült a folyamat futtatása metrikák|Count|Összes||FailureType, neve|
|PipelineSucceededRuns|Sikeres futtatások metrikák folyamat|Count|Összes||FailureType, neve|
|ActivityFailedRuns|Nem sikerült a tevékenységi futtatások metrikák|Count|Összes||Tevékenységtípus, PipelineName, FailureType, neve|
|ActivitySucceededRuns|Sikeres futtatások metrikák tevékenység|Count|Összes||Tevékenységtípus, PipelineName, FailureType, neve|
|TriggerFailedRuns|Nem sikerült az eseményindító-futtatások metrikák|Count|Összes||Név, FailureType|
|TriggerSucceededRuns|Eseményindító-futtatások metrikák sikerült|Count|Összes||Név, FailureType|
|IntegrationRuntimeCpuPercentage|Integrációs modul CPU-kihasználtság|Percent|Average||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableMemory|Integrációs modul rendelkezésre álló memória|Bájt|Average||IntegrationRuntimeName, csomópontnév|
|MaxAllowedResourceCount|Maximális engedélyezett entitások száma|Count|Maximum||Nincs dimenzió|
|MaxAllowedFactorySizeInGbUnits|Maximális engedélyezett feldolgozó mérete (GB-os egység)|Count|Maximum||Nincs dimenzió|
|ResourceCount|Teljes entitások száma|Count|Maximum||Nincs dimenzió|
|FactorySizeInGbUnits|Teljes feldolgozó mérete (GB-os egység)|Count|Maximum||Nincs dimenzió|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Count|Összes|Sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedFailure|Sikertelen feladatok|Count|Összes|Sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedCancelled|A megszakított feladatok|Count|Összes|A megszakított feladatok száma.|Nincs dimenzió|
|JobAUEndedSuccess|Sikeres AU-idő|másodperc|Összes|Sikeres feladatokra fordított AU időt.|Nincs dimenzió|
|JobAUEndedFailure|Sikertelen AU-idő|másodperc|Összes|Sikertelen feladatok teljes AU ideje.|Nincs dimenzió|
|JobAUEndedCancelled|Megszakított AU-idő|másodperc|Összes|A megszakított feladatok teljes AU ideje.|Nincs dimenzió|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatok teljes mennyisége.|Nincs dimenzió|
|DataWritten|Kiírt adatok|Bájt|Összes|Teljes adatmennyiséget ír a fiókba.|Nincs dimenzió|
|DataRead|Adatok olvasása|Bájt|Összes|Összes adat mennyisége a fiók olvasni.|Nincs dimenzió|
|WriteRequests|Írási kérelmek|Count|Összes|Az adatok száma a fiók írási kérelmeket.|Nincs dimenzió|
|ReadRequests|Olvasási kérelmek|Count|Összes|Az adatok száma olvasási kérelmek ahhoz a fiókhoz.|Nincs dimenzió|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Percent|Average|Százalékos Processzorhasználat|Nincs dimenzió|
|memory_percent|Memória százalékos aránya|Percent|Average|Memória százalékos aránya|Nincs dimenzió|
|io_consumption_percent|I/o-százalék|Percent|Average|I/o-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Percent|Average|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Average|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Average|Tárolási kapacitása|Nincs dimenzió|
|serverlog_storage_percent|Server Log storage százalékban|Percent|Average|Server Log storage százalékban|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Average|Kiszolgálói naplók tárolására használt|Nincs dimenzió|
|serverlog_storage_limit|Log storage maximális|Bájt|Average|Log storage maximális|Nincs dimenzió|
|active_connections|Az aktív kapcsolatok|Count|Average|Az aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Count|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|seconds_behind_master|Replikációs késés másodpercben|Count|Average|Replikációs késés másodpercben|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Average|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Kimenő hálózati forgalom között az aktív kapcsolatok|Nincs dimenzió|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Percent|Average|Százalékos Processzorhasználat|Nincs dimenzió|
|memory_percent|Memória százalékos aránya|Percent|Average|Memória százalékos aránya|Nincs dimenzió|
|io_consumption_percent|I/o-százalék|Percent|Average|I/o-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Percent|Average|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Average|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Average|Tárolási kapacitása|Nincs dimenzió|
|serverlog_storage_percent|Server Log storage százalékban|Percent|Average|Server Log storage százalékban|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Average|Kiszolgálói naplók tárolására használt|Nincs dimenzió|
|serverlog_storage_limit|Log storage maximális|Bájt|Average|Log storage maximális|Nincs dimenzió|
|active_connections|Az aktív kapcsolatok|Count|Average|Az aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Count|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|seconds_behind_master|Replikációs késés másodpercben|Count|Average|Replikációs késés másodpercben|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Average|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Kimenő hálózati forgalom között az aktív kapcsolatok|Nincs dimenzió|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Percent|Average|Százalékos Processzorhasználat|Nincs dimenzió|
|memory_percent|Memória százalékos aránya|Percent|Average|Memória százalékos aránya|Nincs dimenzió|
|io_consumption_percent|I/o-százalék|Percent|Average|I/o-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Percent|Average|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Average|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Average|Tárolási kapacitása|Nincs dimenzió|
|serverlog_storage_percent|Server Log storage százalékban|Percent|Average|Server Log storage százalékban|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Average|Kiszolgálói naplók tárolására használt|Nincs dimenzió|
|serverlog_storage_limit|Log storage maximális|Bájt|Average|Log storage maximális|Nincs dimenzió|
|active_connections|Az aktív kapcsolatok|Count|Average|Az aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Count|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Average|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Kimenő hálózati forgalom között az aktív kapcsolatok|Nincs dimenzió|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati az aktív kapcsolatok között|Nincs dimenzió|
|pg_replica_log_delay_in_seconds|Replika késés|másodperc|Maximum|Replika késés másodpercben|Nincs dimenzió|
|pg_replica_log_delay_in_bytes|Maximális késés replika között|Bájt|Maximum|A legtöbb elmaradt replika bájtban Lag|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Percent|Average|Százalékos Processzorhasználat|Nincs dimenzió|
|memory_percent|Memória százalékos aránya|Percent|Average|Memória százalékos aránya|Nincs dimenzió|
|iops-érték|IOPS|Count|Average|I/o-műveletek száma másodpercenként|Nincs dimenzió|
|storage_percent|Tárolási százalék|Percent|Average|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Average|Felhasznált tárterület|Nincs dimenzió|
|active_connections|Az aktív kapcsolatok|Count|Average|Az aktív kapcsolatok|Nincs dimenzió|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Kimenő hálózati forgalom között az aktív kapcsolatok|Nincs dimenzió|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetriai üzenetet küld kísérletek|Count|Összes|Küldését az IoT hub próbált eszköz – felhő telemetriát üzenetek száma|Nincs dimenzió|
|d2c.telemetry.ingress.success|Telemetriai üzeneteket küldi|Count|Összes|Sikerült elküldeni az IoT hub eszköz – felhő telemetriát üzenetek száma|Nincs dimenzió|
|c2d.commands.egress.complete.success|A parancsok befejeződött|Count|Összes|Az eszköz által végrehajtott művelet felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.abandon.success|Elhagyott parancsok|Count|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.reject.success|Elutasított parancsok|Count|Összes|Az eszköz által elutasított felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|devices.totalDevices|(Elavult az) összes eszköz|Count|Összes|Az IoT hubban regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|(Elavult) csatlakoztatott eszközök |Count|Összes|Az IoT hubhoz csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.success|Útválasztási: telemetriai üzenetet|Count|Összes|Többször is sikeresen kézbesíti az üzeneteket az IoT Hub útválasztás használatával az összes végpontok száma. Ha egy üzenet van irányítva több végpontot, ez az érték minden egyes sikeres kézbesítés eggyel nő. Egy üzenet szállítják egyazon végpont több alkalommal, ha ez az érték minden egyes sikeres kézbesítés eggyel nő.|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Útválasztási: telemetriai üzeneteket eldobása |Count|Összes|Hányszor üzenetek kézbesíthetetlen végpontok miatt útválasztási IoT Hub által el lettek dobva. Ez az érték nem számít üzenetet tartalék útvonal, az eldobott üzenetek jelenjenek meg nem létezik.|Nincs dimenzió|
|d2c.telemetry.egress.orphaned|Útválasztási: telemetriai üzeneteket árva |Count|Összes|Hányszor üzenetek által az IoT Hub-útválasztás árva is, mert (beleértve a tartalék szabály) minden olyan útválasztási szabályok nem egyeznek. |Nincs dimenzió|
|d2c.telemetry.egress.invalid|Útválasztási: telemetria inkompatibilis üzenetek|Count|Összes|Hányszor útválasztás az IoT Hub kézbesíti az üzeneteket egy alkalmazással a végponthoz való inkompatibilitás miatt nem sikerült. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Útválasztási: kézbesíti az üzeneteket tartalék|Count|Összes|Hányszor útválasztás az IoT Hub-üzenetek a végponthoz társított a tartalék útvonal i.|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Útválasztási: kézbesíti az üzeneteket az Eseményközpont|Count|Összes|Hányszor sikeresen útválasztás az IoT Hub Event Hub-végpontok üzenetek érkeznek.|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Útválasztási: Event Hub üzenet késése|Ezredmásodperc|Average|Átlagos késése (MS) között az IoT hub üzenet bejövő és a bejövő üzenet be egy Eseményközpont-végpontra.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Útválasztási: üzenetek kézbesítése Service Bus-üzenetsorba helyezése|Count|Összes|Hányszor sikeresen útválasztás az IoT Hub-üzenetek i a Service Bus üzenetsor-végpontokra irányuló.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|Útválasztási: Service Bus-üzenetsor üzenet késése|Ezredmásodperc|Average|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy Service Bus-üzenetsor végpont.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Útválasztási: üzenetek kézbesítése Service Bus-témakörbe|Count|Összes|Hányszor sikeresen útválasztás az IoT Hub-i üzenetek a Service Bus-témakör végpontokat.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|Útválasztási: Service Bus-témakör üzenet késése|Ezredmásodperc|Average|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy Service Bus-témakör végpont.|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|Útválasztási: kézbesíti az üzeneteket üzenetek/események|Count|Összes|Hányszor az IoT Hub sikeresen útválasztási üzenetek érkeznek a beépített végpont (üzenetek/események). Ez a metrika csak megkezdi a munkát, ha engedélyezve van az Útválasztás (https://aka.ms/iotrouting) az IoT hub.|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Útválasztási: üzenet közel valós idejű üzenetek/események|Ezredmásodperc|Average|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő forgalom, a beépített végpont (üzenetek/események). Ez a metrika csak megkezdi a munkát, ha engedélyezve van az Útválasztás (https://aka.ms/iotrouting) az IoT hub.|Nincs dimenzió|
|d2c.endpoints.egress.storage|Útválasztási: kézbesíti az üzeneteket storage|Count|Összes|Többször az IoT Hub sikeresen útválasztási üzenetek érkeznek tárolási végpontok száma.|Nincs dimenzió|
|d2c.endpoints.latency.storage|Útválasztási: üzenet késése storage|Ezredmásodperc|Average|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy storage-végponthoz.|Nincs dimenzió|
|d2c.endpoints.egress.storage.bytes|Útválasztási: az adatokat kézbesíti storage|Bájt|Összes|Adatok (bájt) mennyisége az IoT Hub útválasztás kézbesíti a tárolási végpontok.|Nincs dimenzió|
|d2c.endpoints.egress.storage.blobs|Útválasztási: blobok kézbesíteni storage|Count|Összes|The number of times IoT Hub routing delivered blobs to storage endpoints.|Nincs dimenzió|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Count|Összes|Az IoT Hub-események száma az Event Gridbe közzé. Az eredmény dimenzió használata a sikeres és sikertelen kérések száma. Esemény típusa dimenzió jeleníti meg az esemény típusát (https://aka.ms/ioteventgrid).|Eredmény, eseménytípus|
|EventGridLatency|Átlagos késése (ezredmásodpercben), amikor az Iot Hub event jött létre amikor az esemény közzé lett téve az Event Gridbe. Ez a szám az összes eseménytípust közötti átlagos. Az esemény típusa dimenzió segítségével megtekintheti az esemény egy adott típusú késését.|EventType|
|d2c.twin.read.success|A sikeres ikerírási eszközökről|Count|Összes|Az összes sikeres eszköz által kezdeményezett ikerírási száma.|Nincs dimenzió|
|d2c.twin.read.failure|Nem sikerült az iker írási eszközökről|Count|Összes|A teljes számát a eszköz által kezdeményezett ikerírási nem sikerült.|Nincs dimenzió|
|d2c.twin.read.size|Válasz mérete ikerírási eszközökről|Bájt|Average|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres, a páros olvasási.|Nincs dimenzió|
|d2c.twin.update.success|Eszközök sikeres ikereszköz-frissítések|Count|Összes|Az összes sikeres eszköz által kezdeményezett ikereszköz-frissítések száma.|Nincs dimenzió|
|d2c.twin.update.failure|Nem sikerült az eszköz az ikereszköz-frissítések|Count|Összes|A szám az összes eszköz által kezdeményezett ikereszköz-frissítések nem sikerült.|Nincs dimenzió|
|d2c.twin.update.size|Ikereszköz-frissítések eszközökről mérete|Bájt|Average|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres ikereszköz frissítéseket.|Nincs dimenzió|
|c2d.methods.success|A sikeres közvetlen metódus meghívásához|Count|Összes|Közvetlen metódus az összes sikeres hívások száma.|Nincs dimenzió|
|c2d.methods.failure|Nem sikerült a közvetlen metódus meghívásához|Count|Összes|A teljes számát nem sikerült a közvetlen metódust hívja.|Nincs dimenzió|
|c2d.methods.requestSize|Kérés mérete közvetlen metódus meghívásához|Bájt|Average|Az átlagos, minimális és közvetlen metódus az összes sikeres kérelmek maximális száma.|Nincs dimenzió|
|c2d.methods.responseSize|Közvetlen megpróbálkozni a válasz mérete|Bájt|Average|Az átlagos, minimális és a közvetlen metódus az összes sikeres válaszok maximális száma.|Nincs dimenzió|
|c2d.twin.read.success|A sikeres ikerírási háttérrendszerből|Count|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-olvasások száma.|Nincs dimenzió|
|c2d.twin.read.failure|Nem sikerült a páros olvasási háttérrendszerből|Count|Összes|A teljes számát a back-end által kezdeményezett ikerírási nem sikerült.|Nincs dimenzió|
|c2d.twin.read.size|Válasz mérete ikerírási háttérrendszerből|Bájt|Average|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett a páros olvasási.|Nincs dimenzió|
|c2d.twin.update.success|Háttér sikeres ikereszköz-frissítések|Count|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-frissítések száma.|Nincs dimenzió|
|c2d.twin.update.failure|Háttér sikertelen ikereszköz-frissítések|Count|Összes|A teljes számát a back-end által kezdeményezett ikereszköz-frissítések nem sikerült.|Nincs dimenzió|
|c2d.twin.update.size|A háttérben ikereszköz-frissítések mérete|Bájt|Average|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett ikereszköz frissítéseket.|Nincs dimenzió|
|twinQueries.success|A sikeres ikereszköz-lekérdezések|Count|Összes|Az összes sikeres ikereszköz-lekérdezések száma.|Nincs dimenzió|
|twinQueries.failure|Sikertelen ikereszköz-lekérdezések|Count|Összes|Összes sikertelen ikereszköz-lekérdezések száma.|Nincs dimenzió|
|twinQueries.resultSize|Ikereszköz-lekérdezések eredmény mérete|Bájt|Average|Az átlagos, minimális és az összes sikeres ikereszköz-lekérdezés eredményének mérete legfeljebb.|Nincs dimenzió|
|jobs.createTwinUpdateJob.success|Ikereszköz-frissítési feladatok sikeres létrehozás|Count|Összes|Az összes sikeres létrehozás ikereszköz-frissítési feladatok száma.|Nincs dimenzió|
|jobs.createTwinUpdateJob.failure|Sikertelen létrehozás ikereszköz-frissítési feladatok|Count|Összes|Minden létrehozására tett sikertelen ikereszköz frissítési feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.success|Metódus meghívása feladatok sikeres létrehozás|Count|Összes|Az összes sikeres létrehozás közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.failure|Metódus meghívása feladatok sikertelen létrehozás|Count|Összes|Összes sikertelen létrehozása a közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.listJobs.success|Listázhatók a feladatok sikeres hívások|Count|Összes|Az összes sikeres hívások listázhatók a feladatok száma.|Nincs dimenzió|
|jobs.listJobs.failure|Sikertelen hívások listázhatók a feladatok|Count|Összes|Az összes sikertelen hívás listázhatók a feladatok száma.|Nincs dimenzió|
|jobs.cancelJob.success|Megszakított feladatok sikeres|Count|Összes|Megszakítja a feladatot az összes sikeres hívások száma.|Nincs dimenzió|
|jobs.cancelJob.failure|Sikertelen feladat általi hóközi lemondás|Count|Összes|Megszakítja a feladatot az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.queryJobs.success|Feladat sikeres lekérdezések|Count|Összes|Lekérdezés projektekhez az összes sikeres hívások száma.|Nincs dimenzió|
|jobs.queryJobs.failure|Sikertelen feladat lekérdezések|Count|Összes|Lekérdezés projektekhez az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.completed|Befejezett feladatok|Count|Összes|Az összes befejezett feladatok száma.|Nincs dimenzió|
|jobs.Failed|Sikertelen feladatok|Count|Összes|Összes sikertelen feladatok száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Szabályozási hibák száma|Count|Összes|Eszköz átviteli miatt szabályozási hibák számának szabályozza|Nincs dimenzió|
|dailyMessageQuotaUsed|Használt üzenetek teljes száma|Count|Average|Ma használt teljes üzenetek száma. Ez az összesített érték, amely lenullázódik: 00:00 (UTC) minden nap.|Nincs dimenzió|
|deviceDataUsage|Eszköz teljes adathasználat|Bájt|Összes|És bármilyen eszközről csatlakozik az IotHub átvitt bájtok száma|Nincs dimenzió|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Count|Average|Az IoT hubban regisztrált eszközök száma|Nincs dimenzió|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Count|Average|Az IoT hubhoz csatlakoztatott eszközök száma|Nincs dimenzió|
|Konfigurációk|Konfiguráció-metrikák|Count|Összes|A konfigurálási műveletek metrikái|Nincs dimenzió|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Count|Összes|Kísérlet történt eszközregisztrációk száma|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Hozzárendelt eszközök|Count|Összes|Egy IoT hubra hozzárendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Igazolási kísérletek|Count|Összes|Próbált eszköz tanúsítványok száma|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AvailableStorage|Rendelkezésre álló tár|Bájt|Összes|5 perces részletesség, jelentett összes rendelkezésre álló tár|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|Cassandra-kapcsolat szünnapok|Count|Összes|Lezárt, egy 1 perces részletesség jelenteni Cassandra-kapcsolatok száma|Régió, ClosureReason|
|CassandraRequestCharges|Cassandra-kérelem díjak|Count|Összes|Cassandra-kérelmek felhasznált Kérelemegységek|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra Requests|Count|Count|Cassandra-kérelmek száma|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Adathasználat|Bájt|Összes|Teljes adathasználat jelentett 5 perces Részletesség:|CollectionName, DatabaseName, régió|
|DocumentCount|Dokumentumok száma|Count|Összes|5 perces részletesség jelenteni teljes számát|CollectionName, DatabaseName, régió|
|DocumentQuota|A dokumentum kvóta|Bájt|Összes|5 perces részletesség jelenteni teljes tárolási kvóta|CollectionName, DatabaseName, régió|
|IndexUsage|Index használat|Bájt|Összes|Teljes index használati jelentett 5 perces Részletesség:|CollectionName, DatabaseName, régió|
|MetadataRequests|A metaadat-kérelmek|Count|Count|A metaadat-kérelmek száma. A cosmos DB tárolja a rendszer metaadat-gyűjtemény minden fiókhoz, amely lehetővé teszi, hogy számba venni a gyűjteményeket, adatbázisok és egyéb, és a konfigurációját, ingyenesen.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Mongo-kérelem díja|Count|Összes|Felhasznált Kérelemegységek mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo-kérelmek|Count|Count|Mongo-kérelmek száma|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Kiosztott átviteli kapacitás|Count|Maximum|Kiosztott átviteli kapacitás|DatabaseName, CollectionName|
|ReplicationLatency|P99 Replikáció késése|Ideje ezredmásodpercben|Average|Fiók geo-kompatibilis a forrás- és régiók közötti P99 replikáció késése|SourceRegion, TargetRegion|
|ServiceAvailability|Szolgáltatás rendelkezésre állása|Percent|Average|Fiók kérelmek óránként, napi vagy havi granularitási rendelkezésre állásáról|Nincs dimenzió|
|TotalRequestUnits|Teljes kérelemegység|Count|Összes|Felhasznált egységek kérése|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Összes kérelem|Count|Count|Kérelmek száma|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Count|Összes|Ez a témakör közzétett összes esemény|Nincs dimenzió|
|PublishFailCount|Sikertelen események közzététele|Count|Összes|Nem sikerült közzétenni a témakör összes esemény|ErrorType, Error|
|UnmatchedEventCount|Nem egyező események|Count|Összes|Összes esemény nem egyeznek az esemény-előfizetések ebben a témakörben|Nincs dimenzió|
|PublishSuccessLatencyInMs|A közzététel sikeres kérések késése|Count|Összes|Közzététel sikeres kérések késése (MS)|Nincs dimenzió|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Egyeztetett események|Count|Összes|Az Eseményelőfizetés egyezteti összes esemény|Nincs dimenzió|
|DeliveryAttemptFailCount|Nem sikerült kézbesíteni események|Count|Összes|Nem sikerült továbbítani az esemény-előfizetés az összes esemény|Hiba történt, ErrorType|
|DeliverySuccessCount|A szállított események|Count|Összes|Az Eseményelőfizetés szállított összes esemény|Nincs dimenzió|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartam|Ezredmásodperc|Average|Cél feldolgozási idő ezredmásodpercben|Nincs dimenzió|
|DroppedEventCount|Az eldobott események|Count|Összes|Az Eseményelőfizetés a megfelelő teljes eldobott események|DropReason|
|DeadLetteredCount|Feldolgozatlan események|Count|Összes|Az Eseményelőfizetés a megfelelő teljes feldolgozatlan események|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Count|Összes|Ez a témakör közzétett összes esemény|Nincs dimenzió|
|PublishFailCount|Sikertelen események|Count|Összes|Nem sikerült közzétenni a témakör összes esemény|ErrorType, Error|
|UnmatchedEventCount|Nem egyező események|Count|Összes|Összes esemény nem egyeznek az esemény-előfizetések ebben a témakörben|Nincs dimenzió|
|PublishSuccessLatencyInMs|A közzététel sikeres kérések késése|Count|Összes|Közzététel sikeres kérések késése (MS)|Nincs dimenzió|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérelmek|Count|Összes|A Microsoft.eventhub sikeres kérelmeinek száma.|EntityName, |
|ServerErrors|Kiszolgálóhibáinak száma.|Count|Összes|A Microsoft.eventhub Kiszolgálóhibáinak száma.|EntityName, |
|UserErrors|Felhasználói hibáinak száma.|Count|Összes|A Microsoft.eventhub felhasználói hibáinak.|EntityName, |
|QuotaExceededErrors|Kvótatúllépési hibák száma.|Count|Összes|Kvótatúllépési hibák száma a Microsoft.eventhub.|EntityName, |
|ThrottledRequests|Szabályozott kérelmeinek száma.|Count|Összes|A Microsoft.eventhub szabályozott kérelmeinek száma.|EntityName, |
|IncomingRequests|Bejövő kérelmek|Count|Összes|A Microsoft.eventhub bejövő kérelmeinek száma.|EntityName|
|IncomingMessages|Bejövő üzenetek|Count|Összes|A Microsoft.eventhub bejövő üzeneteinek száma.|EntityName|
|OutgoingMessages|Kimenő üzenetek|Count|Összes|A Microsoft.eventhub kimenő üzeneteinek száma.|EntityName|
|IncomingBytes|Bejövő bájtok száma.|Bájt|Összes|A Microsoft.eventhub bejövő bájtjainak száma.|EntityName|
|OutgoingBytes|Kimenő bájtok száma.|Bájt|Összes|A Microsoft.eventhub kimenő bájtjainak száma.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Aktív kapcsolatainak száma összesen a Microsoft.eventhub.|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatainak száma.|Count|Average|A Microsoft.eventhub megnyitott kapcsolatainak száma.|EntityName|
|ConnectionsClosed|Lezárt kapcsolatok száma.|Count|Average|A Microsoft.eventhub lezárt kapcsolatainak száma.|EntityName|
|CaptureBacklog|Hátralék rögzítése.|Count|Összes|A Microsoft.eventhub hátralékának rögzítése.|EntityName|
|CapturedMessages|Rögzített üzenetek száma.|Count|Összes|A Microsoft.eventhub rögzített üzeneteinek száma.|EntityName|
|CapturedBytes|Rögzített bájtok száma.|Bájt|Összes|A Microsoft.eventhub rögzített bájtjainak száma.|EntityName|
|Size|Size|Bájt|Average|Az Eseményközpont bájt méretű.|EntityName|
|INREQS|(Elavult) a bejövő kérelmek|Count|Összes|Összes bejövő küldési kérelme (elavult) a névtér|Nincs dimenzió|
|SUCCREQ|Sikeres kérések (elavult)|Count|Összes|(Elavult) a névtér összes sikeres kérelme|Nincs dimenzió|
|FAILREQ|(Elavult) a sikertelen kérelmek|Count|Összes|(Elavult) a névtér összes sikertelen kérelem|Nincs dimenzió|
|SVRBSY|Kiszolgáló foglaltsága miatti hibák (elavult)|Count|Összes|(Elavult) a névtér a teljes kiszolgáló foglaltsága miatti hibák|Nincs dimenzió|
|INTERR|(Elavult) belső kiszolgálóhibák|Count|Összes|(Elavult) névtér összes belső kiszolgálóhibák|Nincs dimenzió|
|MISCERR|Más hibák (elavult)|Count|Összes|(Elavult) a névtér összes sikertelen kérelem|Nincs dimenzió|
|INMSGS|(Elavult) a bejövő üzenetek (elavult)|Count|Összes|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Használja inkább a bejövő üzenetek metrikát (elavult)|Nincs dimenzió|
|EHINMSGS|A bejövő üzenetek (elavult)|Count|Összes|(Elavult) a névtér összes bejövő üzenete|Nincs dimenzió|
|OUTMSGS|(Elavult) a kimenő üzenetek (elavult)|Count|Összes|Kimenő üzenetek névtér száma. Ez a metrika elavult. Használja inkább a kimenő üzenetek metrikát (elavult)|Nincs dimenzió|
|EHOUTMSGS|Kimenő üzenetek (elavult)|Count|Összes|Kimenő üzenetek (elavult) a névtér száma|Nincs dimenzió|
|EHINMBS|Bejövő bájtok (elavult) (elavult)|Bájt|Összes|Event Hub bejövő üzeneteinek átviteli sebessége a névtér. Ez a metrika elavult. Használja inkább a bejövő bájtok metrikát (elavult)|Nincs dimenzió|
|EHINBYTES|Bejövő bájtok (elavult)|Bájt|Összes|Event Hub bejövő üzeneteinek átviteli sebessége a névtér (elavult)|Nincs dimenzió|
|EHOUTMBS|Kimenő bájtok (elavult) (elavult)|Bájt|Összes|Event Hub kimenő üzeneteinek átviteli sebessége a névtér. Ez a metrika elavult. Használja inkább a kimenő bájtok metrikát (elavult)|Nincs dimenzió|
|EHOUTBYTES|Kimenő bájtok (elavult)|Bájt|Összes|Event Hub kimenő üzeneteinek átviteli sebessége a névtér (elavult)|Nincs dimenzió|
|EHABL|Archivált várakozó üzenetek (elavult)|Count|Összes|Event Hub archivált várakozó (elavult) a névtér üzenetei|Nincs dimenzió|
|EHAMSGS|Archivált üzenetek (elavult)|Count|Összes|Az Eseményközpont archivált üzenetek (elavult) névtérben található|Nincs dimenzió|
|EHAMBS|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összes|Az Eseményközpont archivált üzeneteinek átviteli sebessége a névtérben (elavult)|Nincs dimenzió|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előnézet)|Count|Összes|A Microsoft.eventhub sikeres kérelmeinek száma. (Előzetes verzió)|Nincs dimenzió|
|ServerErrors|Kiszolgálóhibáinak száma. (Előzetes verzió)|Count|Összes|A Microsoft.eventhub Kiszolgálóhibáinak száma. (Előzetes verzió)|Nincs dimenzió|
|UserErrors|Felhasználói hibáinak száma. (Előzetes verzió)|Count|Összes|A Microsoft.eventhub felhasználói hibáinak. (Előzetes verzió)|Nincs dimenzió|
|QuotaExceededErrors|Kvótatúllépési hibák száma. (Előzetes verzió)|Count|Összes|Kvótatúllépési hibák száma a Microsoft.eventhub. (Előzetes verzió)|Nincs dimenzió|
|ThrottledRequests|Szabályozott kérelmeinek száma. (Előzetes verzió)|Count|Összes|A Microsoft.eventhub szabályozott kérelmeinek száma. (Előzetes verzió)|Nincs dimenzió|
|IncomingRequests|A bejövő kérések (előnézet)|Count|Összes|A Microsoft.eventhub bejövő kérelmeinek száma. (Előzetes verzió)|Nincs dimenzió|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Count|Összes|A Microsoft.eventhub bejövő üzeneteinek száma. (Előzetes verzió)|Nincs dimenzió|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Count|Összes|A Microsoft.eventhub kimenő üzeneteinek száma. (Előzetes verzió)|Nincs dimenzió|
|IncomingBytes|Bejövő bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.eventhub bejövő bájtjainak száma. (Előzetes verzió)|Nincs dimenzió|
|OutgoingBytes|Kimenő bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.eventhub kimenő bájtjainak száma. (Előzetes verzió)|Nincs dimenzió|
|ActiveConnections|Aktív kapcsolatai (előzetes verzió)|Count|Average|Aktív kapcsolatainak száma összesen a Microsoft.eventhub. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatainak száma. (Előzetes verzió)|Count|Average|A Microsoft.eventhub megnyitott kapcsolatainak száma. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsClosed|Lezárt kapcsolatok száma. (Előzetes verzió)|Count|Average|A Microsoft.eventhub lezárt kapcsolatainak száma. (Előzetes verzió)|Nincs dimenzió|
|CaptureBacklog|Hátralék rögzítése. (Előzetes verzió)|Count|Összes|A Microsoft.eventhub hátralékának rögzítése. (Előzetes verzió)|Nincs dimenzió|
|CapturedMessages|Rögzített üzenetek száma. (Előzetes verzió)|Count|Összes|A Microsoft.eventhub rögzített üzeneteinek száma. (Előzetes verzió)|Nincs dimenzió|
|CapturedBytes|Rögzített bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.eventhub rögzített bájtjainak száma. (Előzetes verzió)|Nincs dimenzió|
|CPU|Processzor (előzetes verzió)|Percent|Maximum|CPU-felhasználást az Event Hub-fürt százalékos|Role|
|AvailableMemory|Rendelkezésre álló memória (előzetes verzió)|Count|Maximum|Rendelkezésre álló memória (bájt) az Event Hub-fürt esetében|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjárókérések|Count|Összes|Átjáró-kérelmek száma|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategorizált Átjárókérések|Count|Összes|Átjáró-kérelmek (1xx/2xx vagy 3xx/4xx vagy 5xx) kategóriák szerint|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Aktív feldolgozók száma|Count|Maximum|Aktív feldolgozók száma|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrikaérték|Count|Average|Az automatikus skálázás végrehajtásakor által kiszámított érték|MetricTriggerSource|
|MetricThreshold|Metrika küszöbértéke|Count|Average|Automatikus skálázás futtatásakor konfigurált automatikus skálázási küszöbérték.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Count|Average|Automatikus jelentett kapacitás, annak végrehajtásakor.|Nincs dimenzió|
|ScaleActionsInitiated|Elindított skálázási műveletek|Count|Összes|A skálázási művelet iránya.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Nyilvános előzetes verzió)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Rendelkezésre állás|Százalék|Average|A sikeresen befejezett rendelkezésre állási tesztek százalékos aránya|availabilityResult/name, availabilityResult/hely|
|availabilityResults és száma|Rendelkezésre állási tesztek|Count|Count|Rendelkezésre állási tesztek száma|availabilityResult/name, availabilityResult /-helyet, availabilityResult/sikeres|
|availabilityResults/duration|Rendelkezésre állási teszt futtatásának időtartama|Ideje ezredmásodpercben|Average|Rendelkezésre állási teszt futtatásának időtartama|availabilityResult/name, availabilityResult /-helyet, availabilityResult/sikeres|
|browserTimings/networkDuration|Oldal a hálózati kapcsolat ideje lapbetöltéskor|Ideje ezredmásodpercben|Average|A felhasználói kérelem és a hálózati kapcsolat között eltelt idő. Magában foglalja a DNS-keresési és -átviteli kapcsolat.|Nincs dimenzió|
|browserTimings/processingDuration|Ügyfél feldolgozási ideje|Ideje ezredmásodpercben|Average|Az utolsó bájtig eltelt egy dokumentum fogadása, mindaddig, amíg a DOM betöltése között eltelt idő. Aszinkron kérelmek feldolgozása még folyamatban lehet is.|Nincs dimenzió|
|browserTimings/receiveDuration|Válasz fogadási ideje|Ideje ezredmásodpercben|Average|Az első és utolsó bájt között, vagy amíg a leválasztás ideje.|Nincs dimenzió|
|browserTimings/sendDuration|Kérelem küldési ideje|Ideje ezredmásodpercben|Average|A hálózati kapcsolat és az első bájtig eltelt fogadása között eltelt idő.|Nincs dimenzió|
|browserTimings/totalDuration|Böngészőlap-betöltési ideje|Ideje ezredmásodpercben|Average|A felhasználói kérelemtől a DOM, a stíluslapok, szkriptek és a képek idő töltődnek be.|Nincs dimenzió|
|függőségek és száma|Függőségi hívások|Count|Count|Az alkalmazás által külső erőforrások indított hívások száma.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|függőségek/időtartama|Függőségi időtartam|Ideje ezredmásodpercben|Average|Az alkalmazás által külső erőforrások indított hívások időtartama.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|függőségek/nem sikerült|Függőségi hívás hibák|Count|Count|Az alkalmazás által külső erőforrások indított sikertelen függőségi hívások száma.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Lapmegtekintések|Count|Count|Lapmegtekintések száma.|a művelet/szintetikus|
|pageViews/duration|Lapmegtekintés betöltési ideje|Ideje ezredmásodpercben|Average|Lapmegtekintés betöltési ideje|a művelet/szintetikus|
|performanceCounters/requestExecutionTime|HTTP-kérés végrehajtási ideje|Ideje ezredmásodpercben|Average|A legutóbbi kérelem végrehajtási ideje.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-kérések az alkalmazás-várólistán|Count|Average|Az alkalmazás kérelem-várólistájának hossza.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-kérések gyakorisága|CountPerSecond|Average|Az ASP.NET által az alkalmazáshoz, másodpercenként küldött összes kérelem száma.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|kivételek sebessége|CountPerSecond|Average|Többek között a .NET-kivételeket és a .NET-kivételekké konvertált nem kezelt kivételek a Windowsnak jelentett kezelt és nem kezelt kivételek száma.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Folyamat átviteli sebessége|BytesPerSecond|Average|Összes bájt / másodperc olvasási-írási műveletek fájlokon, hálózaton és eszközökön.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Folyamat CPU|Percent|Average|Az, hogy az összes folyamat szálak eltelt idő százalékában használták a processzort utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a w3wp folyamat teljesítményét jelzi.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processzoridő|Percent|Average|A processzor nem üresjárati szálak futtatásával töltött idő aránya.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Rendelkezésre álló memória|Bájt|Average|Fizikai memória azonnal kiosztható vagy a rendszer általi használatra.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Folyamat saját bájtjai|Bájt|Average|Folyamataihoz hozzárendelt memória a megfigyelt alkalmazás.|cloud/roleInstance|
|kérések/időtartama|Kiszolgáló válaszideje|Ideje ezredmásodpercben|Average|Egy HTTP-kérelem fogadása és a válasz küldésének befejezése között eltelt idő.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|kérelmek és száma|Kiszolgálói kérelmek|Count|Count|HTTP-kérelmek száma befejeződött.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|sikertelen kérések /|Sikertelen kérelmek|Count|Count|Nem sikerült megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérelmek válaszkód > = 400-as és a 401-es nem egyenlő.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|kérések/arány|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Average|Kiszolgálói kérelmek másodpercenkénti száma|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|kivételek és száma|Kivételek|Count|Count|Minden nem kezelt kivételek száma.|cloud/roleName, cloud/roleInstance, client/type|
|kivételek és böngésző|Böngészőkivételek|Count|Count|A böngészőben fellépő nem kezelt kivételek száma.|Nincs dimenzió|
|kivételek és a kiszolgáló|Kiszolgálókivételek|Count|Count|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|cloud/roleName, cloud/roleInstance|
|nyomkövetési és száma|nyomok|Count|Count|Híváslánc-dokumentumok száma|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Összes szolgáltatási Api-találat|Count|Count|Teljes száma a szolgáltatás api-találat|Tevékenységtípus, ActivityName|
|ServiceApiLatency|Szolgáltatási Api teljes késése|Ezredmásodperc|Average|Api-kérelmek teljes késése szolgáltatás|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Összes szolgáltatási Api-eredmények|Count|Count|Teljes száma szolgáltatási api-eredmények|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CacheUtilization|Gyorsítótár-kihasználtság|Percent|Average|A fürt hatókörében kihasználtsága|Nincsenek|
|QueryDuration|Lekérdezések időtartama|Ezredmásodperc|Average|A lekérdezések időtartama másodpercben|Lekérdezés állapota|
|IngestionUtilization|Adatbetöltési kihasználtsága|Percent|Average|A fürtben használt adatfeldolgozási pontok aránya|Nincsenek|
|KeepAlive|Életben tartási|Count|Average|Megerősítést jelölőnégyzet azt jelzi, hogy a fürt lekérdezéseire|None|
|IngestionVolumeInMB|Adatbetöltési kötet (megabájtban)|Count|Összes|A fürthöz (megabájtban) a feldolgozott adatok teljes mennyisége|Adatbázis|
|IngestionLatencyInSeconds|Adatbetöltési késés (másodpercben)|másodperc|Average|A betöltési idő másodpercben a fürthöz a forrás (pl. üzenet jelenik meg az EventHub)|None|
|EventProcessedForEventHubs|(Az Event Hubs) feldolgozott események|Count|Összes|Ha az Event Hubs fürtjét a fürt által feldolgozott események száma|Nincsenek|
|IngestionResult|Adatbetöltési eredménye|Count|Count|Az Adatbetöltési műveletek száma|Állapot|
|CPU|CPU|Percent|Average|CPU-kihasználtsága|Nincsenek|
| ContinuousExportNumOfRecordsExported | A folyamatos Exportálás az exportált rekordok száma | Count | Összes | Minden tárolási összetevő írt az exportálás során az exportált rekordok száma  | Nincsenek |
| ExportUtilization | Exportálás kihasználtsága | Percent | Maximum | Exportálás kihasználtsága | Nincsenek |
| ContinuousExportPendingCount | Függőben lévő száma a folyamatos exportálás | Count | Maximum | Függőben lévő folyamatos hány a készen áll a végrehajtási feladatok exportálása | None |
| ContinuousExportMaxLatenessMinutes | A folyamatos exportálás maximális késedelmesség perc | Count | Maximum | A maximális idő az összes folyamatos export amely függőben lévő és a végrehajtásra kész | Nincsenek |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Count|Count|Száma az API-hívások|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Count|Összes|Elindított futtatások a munkafolyamat száma.|Nincs dimenzió|
|RunsCompleted|Befejezett futtatások|Count|Összes|Befejezett futtatások a munkafolyamat száma.|Nincs dimenzió|
|RunsSucceeded|Sikeres futtatások|Count|Összes|Sikeres futtatások a munkafolyamat száma.|Nincs dimenzió|
|RunsFailed|Sikertelen futtatások|Count|Összes|Sikertelen futtatások munkafolyamat száma.|Nincs dimenzió|
|RunsCancelled|Megszakított futtatások|Count|Összes|Megszakított futtatások a munkafolyamat száma.|Nincs dimenzió|
|RunLatency|Futtatások késése|másodperc|Average|Futtatások késése a befejezett munkafolyamat.|Nincs dimenzió|
|RunSuccessLatency|Sikeres futtatások késése|másodperc|Average|Futtatások késése a sikeres munkafolyamat.|Nincs dimenzió|
|RunThrottledEvents|Futtatások által elindított események|Count|Összes|Által elindított események száma a munkafolyamat-művelet vagy trigger.|Nincs dimenzió|
|RunFailurePercentage|Futtatási hibák százalékos értéke|Percent|Összes|Sikertelen futtatások aránya munkafolyamat.|Nincs dimenzió|
|ActionsStarted|Elindított műveletek |Count|Összes|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|Befejezett műveletek |Count|Összes|Befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|Sikeres műveletek |Count|Összes|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Sikertelen műveletek|Count|Összes|Sikertelen munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Count|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|Műveletek késése |másodperc|Average|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|Sikeres műveletek késése |másodperc|Average|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|Műveletek által elindított események|Count|Összes|Által elindított események száma a munkafolyamat-műveletek...|Nincs dimenzió|
|TriggersStarted|Elindított triggerek |Count|Összes|Az elindított munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersCompleted|Befejezett triggerek |Count|Összes|Befejezett munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersSucceeded|Sikeres triggerek |Count|Összes|A sikeres munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersFailed|Sikertelen triggerek |Count|Összes|Sikertelen munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott triggerek|Count|Összes|A kihagyott munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersFired|Aktivált triggerek |Count|Összes|Az aktivált munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggerLatency|Triggerek késése |másodperc|Average|A befejezett munkafolyamat-triggerek késése.|Nincs dimenzió|
|TriggerFireLatency|Triggerek aktiválásának késése |másodperc|Average|Az aktivált munkafolyamat-triggerek késése.|Nincs dimenzió|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Average|A sikeres munkafolyamat-triggerek késése.|Nincs dimenzió|
|TriggerThrottledEvents|Triggerek által elindított események|Count|Összes|Szám a munkafolyamat-triggerek által elindított események.|Nincs dimenzió|
|BillableActionExecutions|Számlázható műveleti végrehajtások|Count|Összes|Számlázandó munkafolyamat-műveleti végrehajtások száma.|Nincs dimenzió|
|BillableTriggerExecutions|Számlázható triggerek végrehajtásai|Count|Összes|Számlázandó munkafolyamat-trigger végrehajtásainak számát.|Nincs dimenzió|
|TotalBillableExecutions|Összes számlázható végrehajtás|Count|Összes|Számlázandó munkafolyamat-végrehajtások száma.|Nincs dimenzió|
|BillingUsageNativeOperation|Felhasználás számlázásának a natív művelet-végrehajtások|Count|Összes|Natív művelet végrehajtásainak számát.|Nincs dimenzió|
|BillingUsageStandardConnector|Standard összekötő végrehajtásai számlázás használata|Count|Összes|Standard összekötő végrehajtásainak számát.|Nincs dimenzió|
|BillingUsageStorageConsumption|A Storage használati végrehajtások számlázási használati|Count|Összes|Storage használati végrehajtásainak számát.|Nincs dimenzió|
|BillingUsageNativeOperation|Felhasználás számlázásának a natív művelet-végrehajtások|Count|Összes|Natív művelet végrehajtásainak számát.|Nincs dimenzió|
|BillingUsageStandardConnector|Standard összekötő végrehajtásai számlázás használata|Count|Összes|Standard összekötő végrehajtásainak számát.|Nincs dimenzió|
|BillingUsageStorageConsumption|A Storage használati végrehajtások számlázási használati|Count|Összes|Storage használati végrehajtásainak számát.|Nincs dimenzió|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Count|Összes|Elindított futtatások a munkafolyamat száma.|Nincs dimenzió|
|RunsCompleted|Befejezett futtatások|Count|Összes|Befejezett futtatások a munkafolyamat száma.|Nincs dimenzió|
|RunsSucceeded|Sikeres futtatások|Count|Összes|Sikeres futtatások a munkafolyamat száma.|Nincs dimenzió|
|RunsFailed|Sikertelen futtatások|Count|Összes|Sikertelen futtatások munkafolyamat száma.|Nincs dimenzió|
|RunsCancelled|Megszakított futtatások|Count|Összes|Megszakított futtatások a munkafolyamat száma.|Nincs dimenzió|
|RunLatency|Futtatások késése|másodperc|Average|Futtatások késése a befejezett munkafolyamat.|Nincs dimenzió|
|RunSuccessLatency|Sikeres futtatások késése|másodperc|Average|Futtatások késése a sikeres munkafolyamat.|Nincs dimenzió|
|RunThrottledEvents|Futtatások által elindított események|Count|Összes|Által elindított események száma a munkafolyamat-művelet vagy trigger.|Nincs dimenzió|
|RunStartThrottledEvents|Futtatás kezdő által elindított események|Count|Összes|Által elindított események munkafolyamat-Futtatás kezdési száma.|Nincs dimenzió|
|RunFailurePercentage|Futtatási hibák százalékos értéke|Percent|Összes|Sikertelen futtatások aránya munkafolyamat.|Nincs dimenzió|
|ActionsStarted|Elindított műveletek |Count|Összes|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|Befejezett műveletek |Count|Összes|Befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|Sikeres műveletek |Count|Összes|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Sikertelen műveletek |Count|Összes|Sikertelen munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Count|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|Műveletek késése |másodperc|Average|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|Sikeres műveletek késése |másodperc|Average|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|Műveletek által elindított események|Count|Összes|Által elindított események száma a munkafolyamat-műveletek...|Nincs dimenzió|
|TriggersStarted|Elindított triggerek |Count|Összes|Az elindított munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersCompleted|Befejezett triggerek |Count|Összes|Befejezett munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersSucceeded|Sikeres triggerek |Count|Összes|A sikeres munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersFailed|Sikertelen triggerek |Count|Összes|Sikertelen munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott triggerek|Count|Összes|A kihagyott munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersFired|Aktivált triggerek |Count|Összes|Az aktivált munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggerLatency|Triggerek késése |másodperc|Average|A befejezett munkafolyamat-triggerek késése.|Nincs dimenzió|
|TriggerFireLatency|Triggerek aktiválásának késése |másodperc|Average|Az aktivált munkafolyamat-triggerek késése.|Nincs dimenzió|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Average|A sikeres munkafolyamat-triggerek késése.|Nincs dimenzió|
|TriggerThrottledEvents|Triggerek által elindított események|Count|Összes|Szám a munkafolyamat-triggerek által elindított események.|Nincs dimenzió|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|A munkafolyamat processzor kihasználtsága integrációs Service-környezetben|Percent|Average|Processzor kihasználtsága munkafolyamat integrációs service-környezetben.|Nincs dimenzió|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|A munkafolyamat memóriahasználat integrációs Service-környezetben|Percent|Average|A memóriahasználat a munkafolyamat integrációs service-környezetben.|Nincs dimenzió|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Összekötő processzor kihasználtsága integrációs Service-környezetben|Percent|Average|Összekötő processzor kihasználtsága integrációs service-környezetben.|Nincs dimenzió|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Összekötő memóriahasználat integrációs Service-környezetben|Percent|Average|Összekötő memóriahasználat integrációs service-környezetben.|Nincs dimenzió|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Befejezett futtatások|Befejezett futtatások|Count|Összes|A munkaterülethez tartozó sikeresen befejezett futtatások száma|Forgatókönyv|
|Elindított futtatások|Elindított futtatások|Count|Összes|Ehhez a munkaterülethez elindított futtatások száma|Forgatókönyv|
|Sikertelen futtatások|Sikertelen futtatások|Count|Összes|Ehhez a munkaterülethez sikertelen futtatások száma|Forgatókönyv|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Count|Count|Száma az API-hívások|ApiCategory, ApiName, ResultType, ResponseCode|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Average|Az API-k rendelkezésre állása|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageOtherLatency|Átlagos más késés|ms/op|Average|Átlagos (vagyis nem olvasási vagy írási) más késés (MS) / művelet|Nincs dimenzió|
|AverageReadLatency|Átlagos olvasási késés|ms/op|Average|Átlagos olvasási késés (MS) / művelet|Nincs dimenzió|
|AverageTotalLatency|Átlagos teljes késés|ms/op|Average|Átlagos teljes késés (MS) / művelet|Nincs dimenzió|
|AverageWriteLatency|Az átlagos írási késés|ms/op|Average|Az átlagos írási késés (MS) / művelet|Nincs dimenzió|
|FilesystemOtherOps|Fájlrendszer végzett egyéb művelet|Az OPS|Average|Fájlrendszer számos egyéb műveletek (vagyis nem Olvasás vagy írás)|Nincs dimenzió|
|FilesystemReadOps|Fájlrendszer olvasása|Az OPS|Average|Fájlrendszer számú olvasási műveletek|Nincs dimenzió|
|FilesystemTotalOps|Az ops teljes fájlrendszer|Az OPS|Average|Az összes fájlrendszerműveletek összege|Nincs dimenzió|
|FilesystemWriteOps|Fájlrendszer írási művelet|Az OPS|Average|Fájlrendszer írási műveletek száma|Nincs dimenzió|
|IoBytesPerOtherOps|I/o-bájtok száma végzett egyéb művelet|bytes/op|Average|Szám, a/kimenő bájtok száma egyéb műveletek (vagyis nem Olvasás vagy írás)|Nincs dimenzió|
|IoBytesPerReadOps|I/o-bájtok száma olvasási művelet|bytes/op|Average|Bejövő és kimenő bájtok száma olvasási műveletek száma|Nincs dimenzió|
|IoBytesPerTotalOps|I/o-bájtok száma op összes művelet között|bytes/op|Average|Az összes/kimenő bájtok művelet összeg|Nincs dimenzió|
|IoBytesPerWriteOps|I/o-bájtok száma írási művelet|bytes/op|Average|A szám a/kimenő bájtok száma írási művelet|Nincs dimenzió|
|OtherIops|Más iops|műveletek/mp|Average|Egyéb be/ki művelet / másodperc|Nincs dimenzió|
|OtherThroughput|Más átviteli sebesség|MB/s|Average|Más átviteli sebesség (vagyis nem olvasási vagy írási) másodpercenként (MB)|Nincs dimenzió|
|ReadIops|Olvasási iops-érték|műveletek/mp|Average|Olvassa el a bejövő és kimenő műveletek száma másodpercenként|Nincs dimenzió|
|ReadThroughput|Olvasás átviteli sebesség|MB/s|Average|Olvassa el az átviteli sebesség (MB) másodpercenként|Nincs dimenzió|
|TotalIops|Teljes iops|műveletek/mp|Average|Összege az összes be/ki műveletek száma másodpercenként|Nincs dimenzió|
|TotalThroughput|Teljes átviteli sebesség|MB/s|Average|Az összes átviteli sebesség (MB) másodpercenként összege|Nincs dimenzió|
|VolumeAllocatedSize|Kötet lefoglalt méret|bájt|Average|Lefoglalt kötet mérete határozza meg (nem a tényleges használt bájt)|Nincs dimenzió|
|VolumeLogicalSize|A kötet logikai mérete|bájt|Average|A kötet (felhasznált memória) logikai mérete|Nincs dimenzió|
|VolumeSnapshotSize|Kötet-pillanatképek mérete|bájt|Average|Az összes pillanatképek a kötet mérete|Nincs dimenzió|
|WriteIops|Az írási iops|műveletek/mp|Average|Írja be/ki műveletek száma másodpercenként|Nincs dimenzió|
|WriteThroughput|Átviteli sebesség írása|MB/s|Average|Átviteli sebesség írási / másodperc (MB)|Nincs dimenzió|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Kötet lefoglalt készlet mérete|bájt|Average|Lefoglalt méret a készlet (nem a tényleges használt bájt)|Nincs dimenzió|
|VolumePoolAllocatedUsed|Lefoglalt kötet készlet használt|bájt|Average|A készlet lefoglalt felhasznált mérete|Nincs dimenzió|
|VolumePoolTotalLogicalSize|Kötet készlet teljes logikai mérete|bájt|Average|A készlethez tartozó összes kötet logikai méretének összege|Nincs dimenzió|
|VolumePoolTotalSnapshotSize|Kötet készlet teljes pillanatkép mérete|bájt|Average|A készlet összes pillanatképet összege|Nincs dimenzió|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Küldött bájtok|Count|Összes|A hálózati adapter küldött bájtok száma|Nincs dimenzió|
|BytesReceivedRate|Fogadott bájtok száma|Count|Összes|A hálózati adapter fogadott bájtok száma|Nincs dimenzió|
|PacketsSentRate|Küldött csomagok|Count|Összes|A hálózati adapter küldött csomagok száma|Nincs dimenzió|
|PacketsReceivedRate|Fogadott csomagok|Count|Összes|A hálózati adapter kapott csomagok száma|Nincs dimenzió|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|Az adatok elérési útja elérhetősége|Count|Average|Átlagos időtartam / Load Balancer adatok elérési útja rendelkezésre állását|FrontendIPAddress, FrontendPort|
|DipAvailability|Állapot-mintavétel|Count|Average|Átlagos Load Balancer mintavételi állapot szerint időtartam|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Bájtok száma|Count|Összes|Időtartamon belül, átvitt bájtok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Csomagok száma|Count|Összes|Időtartamon belül küldött csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Szinkronizálás a mi száma|Count|Összes|Továbbított időtartamon belül SZIN csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT Connection Count|Count|Összes|Új időtartamon belül létrehozott SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Lefoglalt SNAT portok (előzetes verzió)|Count|Összes|Lefoglalt időtartamon belül, SNAT portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Használt SNAT portok (előzetes verzió)|Count|Összes|Időtartamon belül használt SNAT portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezés kötet|Count|Összes|A DNS-zónák kiszolgált lekérdezések száma|Nincs dimenzió|
|RecordSetCount|Rekord beállítása száma|Count|Maximum|A DNS-zóna rekordhalmazok száma|Nincs dimenzió|
|RecordSetCapacityUtilization|Record Set Capacity Utilization|Percent|Maximum|DNS-zóna az egyes rekordhalmaz-kapacitás százaléka|Nincs dimenzió|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|Nincs dimenzió|
|PacketsDroppedDDoS|Bejövő miatt eldobott csomagok DDoS|CountPerSecond|Maximum|Bejövő miatt eldobott csomagok DDoS|Nincs dimenzió|
|PacketsForwardedDDoS|DDoS továbbított bejövő csomagok|CountPerSecond|Maximum|DDoS továbbított bejövő csomagok|Nincs dimenzió|
|TCPPacketsInDDoS|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|Nincs dimenzió|
|TCPPacketsDroppedDDoS|A bejövő TCP-miatt eldobott csomagok DDoS|CountPerSecond|Maximum|A bejövő TCP-miatt eldobott csomagok DDoS|Nincs dimenzió|
|TCPPacketsForwardedDDoS|DDoS továbbított bejövő TCP-csomagok|CountPerSecond|Maximum|DDoS továbbított bejövő TCP-csomagok|Nincs dimenzió|
|UDPPacketsInDDoS|Bejövő DDoS elleni UDP-csomagok|CountPerSecond|Maximum|Bejövő DDoS elleni UDP-csomagok|Nincs dimenzió|
|UDPPacketsDroppedDDoS|UDP-bejövő csomagok eldobott DDoS|CountPerSecond|Maximum|UDP-bejövő csomagok eldobott DDoS|Nincs dimenzió|
|UDPPacketsForwardedDDoS|Bejövő továbbított DDoS elleni UDP-csomagok|CountPerSecond|Maximum|Bejövő továbbított DDoS elleni UDP-csomagok|Nincs dimenzió|
|BytesInDDoS|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|Nincs dimenzió|
|BytesDroppedDDoS|Bejövő bájtok eldobott DDoS|BytesPerSecond|Maximum|Bejövő bájtok eldobott DDoS|Nincs dimenzió|
|BytesForwardedDDoS|DDoS továbbított bejövő bájtok|BytesPerSecond|Maximum|DDoS továbbított bejövő bájtok|Nincs dimenzió|
|TCPBytesInDDoS|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|Nincs dimenzió|
|TCPBytesDroppedDDoS|A bejövő TCP-bájtok eldobott DDoS|BytesPerSecond|Maximum|A bejövő TCP-bájtok eldobott DDoS|Nincs dimenzió|
|TCPBytesForwardedDDoS|DDoS továbbított bejövő TCP bájt|BytesPerSecond|Maximum|DDoS továbbított bejövő TCP bájt|Nincs dimenzió|
|UDPBytesInDDoS|Bejövő DDoS elleni UDP bájtok|BytesPerSecond|Maximum|Bejövő DDoS elleni UDP bájtok|Nincs dimenzió|
|UDPBytesDroppedDDoS|Bejövő UDP bájt eldobott DDoS|BytesPerSecond|Maximum|Bejövő UDP bájt eldobott DDoS|Nincs dimenzió|
|UDPBytesForwardedDDoS|Bejövő, továbbítja a DDoS elleni UDP bájt|BytesPerSecond|Maximum|Bejövő, továbbítja a DDoS elleni UDP bájt|Nincs dimenzió|
|IfUnderDDoSAttack|A DDoS elleni támadás vagy sem|Count|Maximum|A DDoS elleni támadás vagy sem|Nincs dimenzió|
|DDoSTriggerTCPPackets|DDoS elleni védelem aktiválásához bejövő TCP-csomagok|CountPerSecond|Maximum|DDoS elleni védelem aktiválásához bejövő TCP-csomagok|Nincs dimenzió|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagokat a DDoS elleni védelem aktiválásához|CountPerSecond|Maximum|Bejövő UDP-csomagokat a DDoS elleni védelem aktiválásához|Nincs dimenzió|
|DDoSTriggerSYNPackets|Bejövő szinkronizálás a mi csomagok DDoS elleni védelem aktiválásához|CountPerSecond|Maximum|Bejövő szinkronizálás a mi csomagok DDoS elleni védelem aktiválásához|Nincs dimenzió|
|VipAvailability|Az adatok elérési útja elérhetősége|Count|Average|Átlagos időtartam / IP-cím elérhetősége|Port|
|ByteCount|Bájtok száma|Count|Összes|Időtartamon belül, átvitt bájtok teljes száma|Port, iránya|
|PacketCount|Csomagok száma|Count|Összes|Időtartamon belül küldött csomagok teljes száma|Port, iránya|
|SynCount|Szinkronizálás a mi száma|Count|Összes|Továbbított időtartamon belül SZIN csomagok teljes száma|Port, iránya|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ApplicationRuleHit|Alkalmazás-szabályok találatszáma|Count|Összes|Elérte a kérelem szabályok száma|Állapot, ezért protokoll|
|NetworkRuleHit|Hálózati szabályok találatok száma|Count|Összes|Elérte a hálózati szabályok száma|Állapot, ezért protokoll|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljesítmény|Teljesítmény|BytesPerSecond|Összes|Az Application Gateway szolgálta másodpercenként bájtok száma|Nincs dimenzió|
|UnhealthyHostCount|Nem kifogástalan állapotú gazdagépek száma|Count|Average|Nem megfelelő állapotú háttérrendszer gazdagépek száma|BackendSettingsPool|
|HealthyHostCount|Kifogástalan állapotú gazdagépek száma|Count|Average|Megfelelően működő háttér gazdagépek száma|BackendSettingsPool|
|TotalRequests|Összes kérelem|Count|Összes|Az Application Gateway szolgálta sikeres kérelmek száma|BackendSettingsPool|
|FailedRequests|Sikertelen kérelmek|Count|Összes|Az Application Gateway szolgálta sikertelen kérelmek száma|BackendSettingsPool|
|ResponseStatus|Válasz állapota|Count|Összes|Az Application Gateway által visszaadott HTTP-válasz állapota|HttpStatusGroup|
|CurrentConnections|Jelenlegi kapcsolatok száma|Count|Összes|Az Application Gatewayen a jelenlegi kapcsolatok száma|Nincs dimenzió|
|CapacityUnits|Aktuális kapacitás egységek|Count|Average|Felhasznált kapacitás egységek|Nincs dimenzió|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageBandwidth|Átjáró S2S sávszélesség|BytesPerSecond|Average|Átlagos site-to-site sávszélesség egy átjáró bájt / másodpercben|Nincs dimenzió|
|P2SBandwidth|Átjáró P2S sávszélesség|BytesPerSecond|Average|Átlagos pont – hely sávszélesség egy átjáró bájt / másodpercben|Nincs dimenzió|
|P2SConnectionCount|P2S-kapcsolatok száma|Count|Maximum|Az átjáró pont – hely kapcsolat száma|Protocol|
|TunnelAverageBandwidth|Alagút sávszélessége|BytesPerSecond|Average|Átlagos sávszélesség-alagút bájt / másodpercben|ConnectionName, RemoteIP|
|TunnelEgressBytes|Alagút kimenő bájtok|Bájt|Összes|Kimenő bájtok-alagút|ConnectionName, RemoteIP|
|TunnelIngressBytes|Alagút bejövő bájtok|Bájt|Összes|Bejövő bájtok-alagút|ConnectionName, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagok|Count|Összes|Egy alagutat a kimenő csomagok száma|ConnectionName, RemoteIP|
|TunnelIngressPackets|Alagút bejövő csomagok|Count|Összes|Egy alagutat a bejövő csomag száma|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Alagút kimenő TS eltérés Csomagvesztéseinek|Count|Összes|A forgalom választó eltérő egy alagutat a kimenő csomag eldobási száma|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Alagút bejövő TS eltérés Csomagvesztéseinek|Count|Összes|A forgalom választó eltérő egy alagutat a bejövő csomag eldobási száma|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|A BITS ingressing Azure / másodperc|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|A BITS Azure egressing / másodperc|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|A BITS ingressing Azure / másodperc|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|A BITS Azure egressing / másodperc|Nincs dimenzió|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|A BITS ingressing Azure / másodperc|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|A BITS Azure egressing / másodperc|Nincs dimenzió|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Lekérdezések által visszaadott végpont|Count|Összes|Traffic Manager végpont adott vissza a megadott időkereten belül hányszor|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végpont végpont állapota|Count|Maximum|Ha a végpontok mintavételi állapotának "engedélyezve" 1, 0 egyéb.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ProbesFailedPercent|Nem sikerült a(z) % mintavételek|Percent|Average|a figyelés mintavételek kapcsolat % sikertelen|Nincs dimenzió|
|AverageRoundtripMs|Átl. Üzenetváltási idő (ms)|Ideje ezredmásodpercben|Average|Átlagos hálózati üzenetváltási idő (ms) figyelési mintavételek elküldve a forrás és cél közötti kapcsolat|Nincs dimenzió|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RequestCount|Kérelmek száma|Count|Összes|A HTTP-vagy Https-proxy által kiszolgált ügyfélkérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Kérés mérete|Bájt|Összes|A HTTP-vagy Https-proxyval ügyfelektől érkező kérések küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Válasz mérete|Bájt|Összes|HTTP-vagy Https-proxy által az ügyfelek, válaszok küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Háttér-kérések száma|Count|Összes|A HTTP-vagy Https-proxy háttérkomponenseinek küldött kérelmek száma|Válaszállapotok összege HTTP, HttpStatusGroup, háttér|
|BackendRequestLatency|Háttérbeli késleltetése|Ideje ezredmásodpercben|Average|Amikor a kérést küld a HTTP-vagy Https-proxy által a háttérrendszer mindaddig, amíg a HTTP-vagy Https-proxy kapott válasz utolsó bájtját a háttérbeli számított idő|Háttér|
|TotalLatency|Teljes késés|Ideje ezredmásodpercben|Average|Ha az ügyfél kérést kapott a HTTP-vagy Https-proxy mindaddig, amíg az ügyfél arra vonatkozik, a HTTP-vagy Https-proxy a válasz utolsó bájtját számított idő|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Háttérkiszolgáló állapotadatainak százalékos aránya|Percent|Average|Sikeres állapotjelentés aránya mintavételek a HTTP-vagy Https-proxy által háttérkomponenseinek|Háttér, Háttérkészletek|
|WebApplicationFirewallRequestCount|Web Application Firewall kérések száma|Count|Összes|A webalkalmazási tűzfal által feldolgozott ügyfél-kérelmek száma|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|registration.all|Regisztráció-műveletek|Count|Összes|Az összes sikeres regisztrációkra vonatkozó művelet (létrehozás, frissítés, lekérdezés és törlés) száma. |Nincs dimenzió|
|registration.create|Regisztráció-létrehozási műveletek|Count|Összes|Az összes sikeres regisztráció-létrehozás száma.|Nincs dimenzió|
|registration.update|Regisztráció-frissítési műveletek|Count|Összes|Összes sikeres regisztrációfrissítés száma.|Nincs dimenzió|
|registration.get|Regisztráció-olvasási műveletek|Count|Összes|Az összes sikeres regisztráció-lekérdezés száma.|Nincs dimenzió|
|registration.delete|Regisztráció-törlési műveletek|Count|Összes|Összes sikeres regisztrációtörlés száma.|Nincs dimenzió|
|bejövő|Bejövő üzenetek|Count|Összes|A száma, az összes sikeres API-hívások küldése. |Nincs dimenzió|
|Incoming.Scheduled|Elküldött ütemezett leküldéses értesítések|Count|Összes|Ütemezett leküldéses értesítések meg lett szakítva|Nincs dimenzió|
|Incoming.Scheduled.Cancel|Ütemezett leküldéses értesítések meg lett szakítva|Count|Összes|Ütemezett leküldéses értesítések meg lett szakítva|Nincs dimenzió|
|Scheduled.Pending|Függőben lévő ütemezett értesítések|Count|Összes|Függőben lévő ütemezett értesítések|Nincs dimenzió|
|Installation.all|Telepítéskezelési műveletek|Count|Összes|Telepítéskezelési műveletek|Nincs dimenzió|
|installation.get|Telepítéslekérdezési műveletek|Count|Összes|Telepítéslekérdezési műveletek|Nincs dimenzió|
|Installation.upsert|Telepítési műveletek létrehozása vagy módosítása|Count|Összes|Telepítési műveletek létrehozása vagy módosítása|Nincs dimenzió|
|Installation.Patch|Telepítésjavítási műveletek|Count|Összes|Telepítésjavítási műveletek|Nincs dimenzió|
|installation.delete|Telepítéstörlési műveletek|Count|Összes|Telepítéstörlési műveletek|Nincs dimenzió|
|outgoing.allpns.success|Sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.allpns.invalidpayload|Terhelési hibák|Count|Összes|A PNS helytelen adattartalomra hibát adott vissza miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.allpns.pnserror|Külső értesítési rendszer hibái|Count|Összes|Hiba történt (hitelesítési problémákon) a pns-sel való kommunikáció során miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.allpns.channelerror|Csatorna által okozott hibák|Count|Összes|Leküldések száma, hogy nem sikerült, mert a csatorna érvénytelen volt nem szabályozott, vagy lejárt a megfelelő alkalmazáshoz társított.|Nincs dimenzió|
|outgoing.allpns.badorexpiredchannel|Rossz vagy lejárt csatorna által okozott hibák|Count|Összes|Leküldések száma, amely nem sikerült, mert a csatorna/token/regisztrációban szereplő regisztrációs azonosító a regisztráció lejárt vagy érvénytelen volt.|Nincs dimenzió|
|outgoing.wns.success|Wns – sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.wns.invalidcredentials|Wns – hitelesítési hibák (érvénytelen hitelesítő adatok)|Count|Összes|Leküldések száma, hogy nem sikerült, mert a PNS nem fogadta el a megadott hitelesítő adatokkal, vagy a hitelesítő adatok le vannak tiltva. (Windows Live nem ismeri fel a hitelesítő adatok).|Nincs dimenzió|
|outgoing.wns.badchannel|Wns – rossz csatorna által okozott hiba|Count|Összes|A leküldések száma nem sikerült, mert a regisztrációban ismeretlen (WNS-állapot: 404 nem található).|Nincs dimenzió|
|outgoing.wns.expiredchannel|WNS Expired Channel Error|Count|Összes|A leküldések száma nem sikerült, mert a regisztrációban lejárt (WNS-állapot: 410-es Gone).|Nincs dimenzió|
|outgoing.wns.throttled|Wns – szabályozott értesítések|Count|Összes|A leküldések száma nem sikerült, mert a WNS szabályozza az alkalmazást (WNS-állapot: 406 nem megfelelő).|Nincs dimenzió|
|outgoing.wns.tokenproviderunreachable|Wns – hitelesítési hibák (nem érhető el)|Count|Összes|Windows Live szolgáltatás nem érhető el.|Nincs dimenzió|
|outgoing.wns.invalidtoken|Wns – hitelesítési hibák (érvénytelen jogkivonat)|Count|Összes|Wns megadott jogkivonat érvénytelen (WNS-állapot: 401-es nem engedélyezett).|Nincs dimenzió|
|outgoing.wns.wrongtoken|Wns – hitelesítési hibák (nem megfelelő jogkivonat)|Count|Összes|Wns megadott jogkivonat érvényes, de egy másik alkalmazás (WNS-állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztrációban rendelve egy másik alkalmazás. Ellenőrizze, hogy az ügyfélalkalmazás ugyanazt az alkalmazást, amelynek hitelesítő adatait az értesítési központban vannak társítva.|Nincs dimenzió|
|outgoing.wns.invalidnotificationformat|Wns – érvénytelen értesítési formátum|Count|Összes|Az értesítés formátuma érvénytelen (WNS-állapot: 400). Vegye figyelembe, hogy WNS nem utasít el minden érvénytelen adattartalmat.|Nincs dimenzió|
|outgoing.wns.invalidnotificationsize|Wns – érvénytelen Értesítésméret által okozott hiba|Count|Összes|Az értesítési tartalom mérete túl nagy (WNS-állapot: 413).|Nincs dimenzió|
|outgoing.wns.channelthrottled|Wns – csatorna szabályozva|Count|Összes|Az értesítés el lett dobva, mert a regisztrációban szabályozott (WNS-válaszfejléc: X-WNS-NotificationStatus:channelThrottled).|Nincs dimenzió|
|outgoing.wns.channeldisconnected|Wns – kapcsolat a csatornával|Count|Összes|Az értesítés el lett dobva, mert a regisztrációban szabályozott (WNS-válaszfejléc: X-WNS-DeviceConnectionStatus: disconnected).|Nincs dimenzió|
|outgoing.wns.dropped|Wns – elvetett értesítések|Count|Összes|Az értesítés el lett dobva, mert a regisztrációban szabályozott (X-WNS-NotificationStatus: dropped, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincs dimenzió|
|outgoing.wns.pnserror|Wns – hibák|Count|Összes|Az értesítés nem lett kézbesítve, mert a WNS-sel való kommunikáció közben hibák.|Nincs dimenzió|
|outgoing.wns.authenticationerror|Wns – hitelesítési hibák|Count|Összes|Az értesítés nem lett kézbesítve, mert érvénytelen hitelesítő adatok Windows Live, vagy nem megfelelő a jogkivonat való kommunikáció során hibák.|Nincs dimenzió|
|outgoing.apns.success|Apns – sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.apns.invalidcredentials|APNS-hitelesítési hibák|Count|Összes|Leküldések száma, hogy nem sikerült, mert a PNS nem fogadta el a megadott hitelesítő adatokkal, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.apns.badchannel|APNS – rossz csatorna hiba|Count|Összes|A leküldések száma nem sikerült, mert a jogkivonat érvénytelen (APNS-állapotkód: 8).|Nincs dimenzió|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Count|Összes|Az APNS visszajelzési csatornája által érvénytelenített jogkivonatok száma.|Nincs dimenzió|
|outgoing.apns.invalidnotificationsize|APNS érvénytelen Értesítésméret által okozott hiba|Count|Összes|Az adattartalom túl nagy a sikertelen leküldések száma, (APNS-állapotkód: 7).|Nincs dimenzió|
|outgoing.apns.pnserror|APNS-hibák|Count|Összes|Leküldések száma, az apns-sel kommunikáló hibák miatt sikertelen.|Nincs dimenzió|
|outgoing.gcm.success|Gcm – sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.gcm.invalidcredentials|Gcm – hitelesítési hibák (érvénytelen hitelesítő adatok)|Count|Összes|Leküldések száma, hogy nem sikerült, mert a PNS nem fogadta el a megadott hitelesítő adatokkal, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.gcm.badchannel|Gcm – rossz csatorna által okozott hiba|Count|Összes|A leküldések száma nem sikerült, mert a regisztrációban szereplő regisztrációs azonosító ismeretlen (GCM-eredmény: Érvénytelen regisztrációs).|Nincs dimenzió|
|outgoing.gcm.expiredchannel|GCM Expired Channel Error|Count|Összes|Leküldések száma, amely nem sikerült, mert a regisztrációban szereplő regisztrációs azonosító lejárt (GCM-eredmény: NotRegistered).|Nincs dimenzió|
|outgoing.gcm.throttled|Gcm – szabályozott értesítések|Count|Összes|A leküldések száma nem sikerült, mert a GCM szabályozta az alkalmazást (GCM-állapotkód: 501-599 vagy eredmény: nem érhető el).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationformat|Gcm – érvénytelen értesítési formátum|Count|Összes|Leküldések száma, hogy nem sikerült, mert a tartalom formázása nem megfelelő (GCM-eredmény: InvalidDataKey vagy InvalidTtl).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationsize|Gcm – érvénytelen Értesítésméret által okozott hiba|Count|Összes|Az adattartalom túl nagy a sikertelen leküldések száma, (GCM-eredmény: MessageTooBig).|Nincs dimenzió|
|outgoing.gcm.wrongchannel|Gcm – rossz csatorna által okozott hiba|Count|Összes|Nem sikerült, mert a regisztrációban szereplő regisztrációs azonosító nem a jelenlegi alkalmazáshoz kapcsolódó leküldések száma (GCM-eredmény: InvalidPackageName).|Nincs dimenzió|
|outgoing.gcm.pnserror|GCM-hibák|Count|Összes|A leküldések száma GCM való kommunikáció során hibák miatt sikertelen.|Nincs dimenzió|
|outgoing.gcm.authenticationerror|Gcm – hitelesítési hibák|Count|Összes|A PNS nem fogadta el a megadott hitelesítő adatokat a hitelesítő adatok miatt sikertelen leküldések száma le vannak tiltva, vagy hogy a nem megfelelően van konfigurálva az alkalmazásban (GCM-eredmény: MismatchedSenderId).|Nincs dimenzió|
|outgoing.mpns.success|Mpns – sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.mpns.invalidcredentials|MPNS Invalid Credentials|Count|Összes|Leküldések száma, hogy nem sikerült, mert a PNS nem fogadta el a megadott hitelesítő adatokkal, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.mpns.badchannel|Mpns – rossz csatorna által okozott hiba|Count|Összes|A leküldések száma nem sikerült, mert a regisztrációban ismeretlen (MPNS-állapot: 404 nem található).|Nincs dimenzió|
|outgoing.mpns.throttled|Mpns – szabályozott értesítések|Count|Összes|A leküldések száma nem sikerült, mert az MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem megfelelő).|Nincs dimenzió|
|outgoing.mpns.invalidnotificationformat|Mpns – érvénytelen értesítési formátum|Count|Összes|Leküldések száma, hogy nem sikerült, mert az értesítés adattartalom túl nagy.|Nincs dimenzió|
|outgoing.mpns.channeldisconnected|Mpns – kapcsolat a csatornával|Count|Összes|A leküldések száma nem sikerült, mert a regisztrációban le lett választva (MPNS-állapot: 412 nem található).|Nincs dimenzió|
|outgoing.mpns.dropped|Mpns – elvetett értesítések|Count|Összes|A MPNS által elvetett leküldések száma (MPNS-válaszfejléc: X-NotificationStatus: QueueFull vagy letiltott).|Nincs dimenzió|
|outgoing.mpns.pnserror|MPNS-hibák|Count|Összes|A leküldések száma MPNS való kommunikáció során hibák miatt sikertelen.|Nincs dimenzió|
|outgoing.mpns.authenticationerror|Mpns – hitelesítési hibák|Count|Összes|Leküldések száma, hogy nem sikerült, mert a PNS nem fogadta el a megadott hitelesítő adatokkal, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|notificationhub.pushes|Minden kimenő értesítés|Count|Összes|Az értesítési központ minden kimenő értesítése|Nincs dimenzió|
|incoming.all.requests|Minden bejövő kérelem|Count|Összes|Egy értesítési központ összes bejövő kérelme|Nincs dimenzió|
|Incoming.all.failedrequests|Minden sikertelen bejövő kérelem|Count|Összes|Egy értesítési központ összes bejövő sikertelen kérelem|Nincs dimenzió|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_ % szabad Inode-OK|Szabad Inode-OK|Count|Average|Average_ % szabad Inode-OK|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % szabad terület|% Szabad terület|Count|Average|Average_ % szabad terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt Inode-OK Average_ %|Foglalt Inode-OK %|Count|Average|Foglalt Inode-OK Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt hely Average_ %|Foglalt hely %|Count|Average|Foglalt hely Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk olvasott bájt/mp|Lemezolvasási sebesség (bájt/s)|Count|Average|Average_Disk olvasott bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk/mp|Lemezolvasások/mp|Count|Average|Average_Disk/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk átvitel/mp|Átvitel/mp|Count|Average|Average_Disk átvitel/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Zapsané Bajty/s|Lemezírási sebesség (bájt/s)|Count|Average|Average_Disk Zapsané Bajty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Lemezírások/mp|Lemezírások/mp|Count|Average|Average_Disk Lemezírások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Szabad hely MB-ban|Count|Average|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical bájt/mp|Logikai lemez bájt/mp|Count|Average|Average_Logical bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ rendelkezésre álló memória %|Rendelkezésre álló memória %|Count|Average|Average_ rendelkezésre álló memória %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ rendelkezésre álló Lapozóterület %|Rendelkezésre álló Lapozóterület %|Count|Average|Average_ rendelkezésre álló Lapozóterület %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt memória Average_ %|Foglalt memória %|Count|Average|Foglalt memória Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt Lapozóterület Average_ %|Foglalt Lapozóterület %|Count|Average|Foglalt Lapozóterület Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available álló memória|Rendelkezésre álló memória|Count|Average|Average_Available álló memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available lapozási memória|Rendelkezésre álló Lapozóterület|Count|Average|Average_Available lapozási memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page/mp|Olvasott lap/mp|Count|Average|Average_Page/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Lemezírások/mp|Írt lap/mp|Count|Average|Average_Page Lemezírások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Lap/mp|Count|Average|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used álló Lapozóterület|Használt lapozási memória terület|Count|Average|Average_Used álló Lapozóterület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória (MB)|Használt memória (MB)|Count|Average|Average_Used memória (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total átvitt bájtok|Küldött bájtok száma összesen|Count|Average|Average_Total átvitt bájtok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total fogadott bájtok száma|Fogadott bájtok teljes száma|Count|Average|Average_Total fogadott bájtok száma|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bájtok|Összes bájt|Count|Average|Average_Total bájtok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Átvitt Average_Total csomagok|Az összes csomag továbbított adatok köre|Count|Average|Átvitt Average_Total csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Total csomag|Összes fogadott csomag|Count|Average|Fogadott Average_Total csomag|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx hibák|Teljes Rx-hibák|Count|Average|Average_Total Rx hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx hibák|Teljes Tx-hibák|Count|Average|Average_Total Tx hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total ütközések|Teljes ütközések|Count|Average|Average_Total ütközések|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Átl. Lemez mp/Olvasás|Count|Average|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/átvitel|Átl. Lemez mp/átvitel|Count|Average|Average_Avg. Lemez mp/átvitel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Átl. Lemez mp/írás|Count|Average|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical bájt/mp|Fizikai lemez bájt/mp|Count|Average|Average_Physical bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Védett módú Average_Pct|A PCT kiemelt idő|Count|Average|Védett módú Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Felhasználói idő Average_Pct|A PCT felhasználói idő|Count|Average|Felhasználói idő Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória KB|Használt memória mérete kilobájtban|Count|Average|Average_Used memória KB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|A megosztott memória Average_Virtual|A megosztott virtuális memória|Count|Average|A megosztott memória Average_Virtual|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ DPC idő %|DPC idő %|Count|Average|Average_ DPC idő %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ inaktivitási idő %|Inaktivitási idő %|Count|Average|Average_ inaktivitási idő %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ megszakítási idő %|Megszakítási idő %|Count|Average|Average_ megszakítási idő %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % IO várakozási idő|A(z) % IO várakozási idő|Count|Average|Average_ % IO várakozási idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ %-ban a processzoron idő|Idő %-ban a processzoron|Count|Average|Average_ %-ban a processzoron idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Védett módú Average_ %|Védett módú használat % idő|Count|Average|Védett módú Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ processzoridő|Processzoridő|Count|Average|Average_ processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Felhasználói idő Average_ %|Felhasználói idő %|Count|Average|Felhasználói idő Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fizikai memória|Szabad fizikai memória|Count|Average|Average_Free fizikai memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free hely a Lapozófájlokban|Szabad hely a Lapozófájlokban|Count|Average|Average_Free hely a Lapozófájlokban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuális memória|Szabad virtuális memória|Count|Average|Average_Free virtuális memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Folyamatok|Count|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|A Lapozófájlokban tárolt Average_Size|A Lapozófájlokban tárolt méret|Count|Average|A Lapozófájlokban tárolt Average_Size|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Hasznos üzemidő|Count|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Felhasználók|Count|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Átl. Lemez mp/Olvasás|Count|Average|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Átl. Lemez mp/írás|Count|Average|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Lemezvárólista hossza|Lemezvárólista jelenlegi hossza|Count|Average|Average_Current Lemezvárólista hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk/mp|Lemezolvasások/mp|Count|Average|Average_Disk/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk átvitel/mp|Átvitel/mp|Count|Average|Average_Disk átvitel/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Lemezírások/mp|Lemezírások/mp|Count|Average|Average_Disk Lemezírások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Szabad hely MB-ban|Count|Average|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % szabad terület|% Szabad terület|Count|Average|Average_ % szabad terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available (MB)|Rendelkezésre álló memória|Count|Average|Average_Available (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ véglegesített % bájt használatban|Előjegyzett kihasználtsága (%)|Count|Average|Average_ véglegesített % bájt használatban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Fogadott bájtok/mp|Count|Average|Fogadott Average_Bytes/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes küldött/mp|Küldött bájtok/s|Count|Average|Average_Bytes küldött/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes összes/mp|Összes bájt/mp|Count|Average|Average_Bytes összes/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ processzoridő|Processzoridő|Count|Average|Average_ processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor várólistájának hossza|Processzor-várólista hossza|Count|Average|Average_Processor várólistájának hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Szívverés|Szívverés|Count|Összes|Szívverés|Computer, OSType, Version, SourceComputerId|
|frissítés|frissítés|Count|Average|frissítés|Számítógép, termék, a besorolás, a UpdateState, nem kötelező, jóváhagyott|
|Esemény|Esemény|Count|Average|Esemény|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Lekérdezések időtartama|Ezredmásodperc|Average|DAX-lekérdezés időtartama az elmúlt időköz|Nincs dimenzió|
|QueryPoolJobQueueLength|Szálak: Lekérdezési készlet feladat-várólistájának hossza|Count|Average|Az a lekérdezési szálkészlet üzenetsorában található feladatok száma.|Nincs dimenzió|
|qpu_high_utilization_metric|QPU magas kihasználtság|Count|Összes|QPU magas kihasználtság az elmúlt percben, 1 QPU magas kihasználtság, különben 0|Nincs dimenzió|
|memory_metric|Memory (Memória)|Bájt|Average|A memória. Tartomány 0 – 3 GB az a1-es, 0 – 5 GB az a2-es, 0 – 10 GB az A3, a4-es 0 – 25 GB, a5 0-50 GB-os és 0 – 100 GB-A6|Nincs dimenzió|
|memory_thrashing_metric|Memóriaakadozás|Percent|Average|Átlagos memóriaakadozás.|Nincs dimenzió|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Összes|A Microsoft.Relay sikeresen létrejött Figyelőkapcsolatai.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Összes|A Microsoft.Relay Figyelőkapcsolatokra vonatkozó Ügyfélhibái.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Összes|A Microsoft.Relay Figyelőkapcsolatokra vonatkozó Kiszolgálóhibái.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Összes|A sikeres Microsoft.Relay feladói kapcsolatokra vonatkozó.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Összes|A Microsoft.Relay feladói kapcsolatokra vonatkozó Ügyfélhibái.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Összes|A Microsoft.Relay feladói kapcsolatokra vonatkozó Kiszolgálóhibái.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Összes|A Microsoft.Relay Figyelőkapcsolatai összesen.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Összes|Feladói kapcsolatai összesen a Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Összes|Microsoft.Relay aktív kapcsolatai összesen.|EntityName|
|Aktív figyelői|Aktív figyelői|Count|Összes|Microsoft.Relay aktív figyelői összesen.|EntityName|
|Által átvitt bájtok|Által átvitt bájtok|Count|Összes|A Microsoft.Relay által átvitt bájtok összesen.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Összes|A Microsoft.Relay figyelőkapcsolat.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Összes|A Microsoft.Relay Feladóikapcsolat.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|másodperc|Average|A keresési szolgáltatás átlagos keresési késés|Nincs dimenzió|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|CountPerSecond|Average|Keresési lekérdezések másodpercenként a keresési szolgáltatás|Nincs dimenzió|
|ThrottledSearchQueriesPercentage|Szabályozott lekérdezések százalékos aránya|Percent|Average|A keresési szolgáltatás szabályozva lettek keresési lekérdezések aránya|Nincs dimenzió|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előnézet)|Count|Összes|(Előzetes verzió) a névtér összes sikeres kérelme|EntityName|
|ServerErrors|Kiszolgálóhibáinak száma. (Előzetes verzió)|Count|Összes|A Microsoft.servicebus Kiszolgálóhibáinak száma. (Előzetes verzió)|EntityName|
|UserErrors|Felhasználói hibáinak száma. (Előzetes verzió)|Count|Összes|A Microsoft.servicebus felhasználói hibáinak. (Előzetes verzió)|EntityName|
|ThrottledRequests|Szabályozott kérelmeinek száma. (Előzetes verzió)|Count|Összes|A Microsoft.servicebus szabályozott kérelmeinek száma. (Előzetes verzió)|EntityName|
|IncomingRequests|A bejövő kérések (előnézet)|Count|Összes|A Microsoft.servicebus bejövő kérelmeinek száma. (Előzetes verzió)|EntityName|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Count|Összes|A Microsoft.servicebus bejövő üzeneteinek száma. (Előzetes verzió)|EntityName|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Count|Összes|A Microsoft.servicebus kimenő üzeneteinek száma. (Előzetes verzió)|EntityName|
|ActiveConnections|Aktív kapcsolatai (előzetes verzió)|Count|Összes|Aktív kapcsolatainak száma összesen a Microsoft.servicebus. (Előzetes verzió)|Nincs dimenzió|
|Size|Méret (előzetes verzió)|Bájt|Average|Bájt az üzenetsor vagy témakör mérete. (Előzetes verzió)|EntityName|
|Üzenetek|Az üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|Count|Average|Az üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|EntityName|
|ActiveMessages|Az üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|Count|Average|Az üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|EntityName|
|DeadletteredMessages|Az üzenetsor vagy témakör kézbesítetlen lettered üzeneteinek száma. (Előzetes verzió)|Count|Average|Az üzenetsor vagy témakör kézbesítetlen lettered üzeneteinek száma. (Előzetes verzió)|EntityName|
|ScheduledMessages|Ütemezett, az üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|Count|Average|Ütemezett, az üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|EntityName|
|CPUXNS|Processzorhasználat névterenként|Percent|Maximum|Service bus prémium szintű névtér CPU-használati metrikája|Nincs dimenzió|
|WSXNS|Memória mérete névterenként|Percent|Maximum|Service bus prémium szintű névtér memóriahasználati metrikája|Nincs dimenzió|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|Ez a tároló millicore-ban hozzárendelt processzor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bájt|Average|Ez a tároló (MB) számára lefoglalt memória|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Tényleges processzorhasználat millicore-ban|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bájt|Average|Tényleges memóriahasználat (MB)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Average|Ez a tároló AllocatedCpu százalékát CPU hasznosítása|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Average|Ez a tároló AllocatedCpu százalékát CPU hasznosítása|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Service Fabric-háló alkalmazás állapota|ApplicationName, állapota|
|ServiceStatus|ServiceStatus|Count|Average|Egy szolgáltatás a Service Fabric-háló alkalmazás állapotának megtekintése|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|A Service Fabric-háló application szolgáltatás replika állapota|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|A Service Fabric-háló alkalmazásban tároló állapota|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Count|Average|Újraindítások száma egy adott tároló Service Fabric-háló alkalmazásban|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ConnectionCount|Kapcsolatok száma|Count|Maximum|A felhasználói kapcsolat mennyisége.|Végpont|
|MessageCount|Üzenetek száma|Count|Összes|Üzenetek teljes mennyisége.|Nincs dimenzió|
|InboundTraffic|Bejövő forgalom|Bájt|Összes|A szolgáltatás a bejövő adatforgalom|Nincs dimenzió|
|OutboundTraffic|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom a szolgáltatás|Nincs dimenzió|
|UserErrors|Felhasználói hibák|Percent|Maximum|A felhasználói hibáinak aránya|Nincs dimenzió|
|SystemErrors|Rendszerhibák|Percent|Maximum|A rendszer hibák százaléka|Nincs dimenzió|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Percent|Average|Adat IO kihasználtsága (%)|Nincs dimenzió|
|log_write_percent|Napló i/o-százaléka|Percent|Average|Napló i/o-százaléka|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Percent|Average|DTU-kihasználtság (%)|Nincs dimenzió|
|tárterület|Adatok felhasznált lemezterület|Bájt|Maximum|Adatbázis teljes mérete|Nincs dimenzió|
|connection_successful|Sikeres kapcsolatok|Count|Összes|Sikeres kapcsolatok|Nincs dimenzió|
|connection_failed|Sikertelen kapcsolatok|Count|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|blocked_by_firewall|Tűzfal által blokkolva|Count|Összes|Tűzfal által blokkolva|Nincs dimenzió|
|Holtpont|Holtpontok|Count|Összes|Holtpontok|Nincs dimenzió|
|storage_percent|Adatok felhasznált lemezterület-százalék|Percent|Maximum|Adatbázis méretének kihasználtsága|Nincs dimenzió|
|xtp_storage_percent|Memóriabeli OLTP storage százalék|Percent|Average|Memóriabeli OLTP storage százalék|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Percent|Average|Feldolgozók százalékos aránya|Nincs dimenzió|
|sessions_percent|Munkamenetek százaléka|Percent|Average|Munkamenetek százaléka|Nincs dimenzió|
|dtu_limit|DTU-korlát|Count|Average|DTU-korlát|Nincs dimenzió|
|dtu_used|Használt dtu-k|Count|Average|Használt dtu-k|Nincs dimenzió|
|cpu_limit|CPU-korlát|Count|Average|CPU-korlát|Nincs dimenzió|
|cpu_used|CPU-kihasználtság|Count|Average|CPU-kihasználtság|Nincs dimenzió|
|dwu_limit|DWU-korlát|Count|Maximum|DWU-korlát|Nincs dimenzió|
|dwu_consumption_percent|Százalékos DWU|Percent|Maximum|Százalékos DWU|Nincs dimenzió|
|dwu_used|Alkalmazott DWU|Count|Maximum|Alkalmazott DWU|Nincs dimenzió|
|dw_cpu_percent|DW csomópont szintjén Processzorhasználat (%)|Percent|Average|DW csomópont szintjén Processzorhasználat (%)|DwLogicalNodeId|
|dw_physical_data_read_percent|DW csomópont adat IO százalékos értéke|Percent|Average|DW csomópont adat IO százalékos értéke|DwLogicalNodeId|
    |cache_hit_percent|Gyorsítótár találati százaléka|Percent|Maximum|Gyorsítótár találati százaléka|Nincs dimenzió|
|cache_used_percent|Felhasznált gyorsítótár százalékos aránya|Percent|Maximum|Felhasznált gyorsítótár százalékos aránya|Nincs dimenzió|
|local_tempdb_usage_percent|Helyi tempdb százalékos aránya|Percent|Average|Helyi tempdb százalékos aránya|Nincs dimenzió|
|app_cpu_billed|Alkalmazás CPU, a számlázás|Count|Összes|Alkalmazás CPU, a számlázás|Nincs dimenzió|
|app_cpu_percent|Alkalmazás CPU-százaléka|Percent|Average|Alkalmazás CPU-százaléka|Nincs dimenzió|
|app_memory_percent|Alkalmazás a használt memória százalékos aránya|Percent|Average|Alkalmazás a használt memória százalékos aránya|Nincs dimenzió|
|allocated_data_storage|Adatok lefoglalt terület|Bájt|Average|Adatok lefoglalt terület|Nincs dimenzió|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Percent|Average|Adat IO kihasználtsága (%)|Nincs dimenzió|
|log_write_percent|Napló i/o-százaléka|Percent|Average|Napló i/o-százaléka|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Percent|Average|DTU-kihasználtság (%)|Nincs dimenzió|
|storage_percent|Adatok felhasznált lemezterület-százalék||Percent|Average|Tárolási százalékos aránya|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Percent|Average|Feldolgozók százalékos aránya|Nincs dimenzió|
|sessions_percent|Munkamenetek százaléka|Percent|Average|Munkamenetek százaléka|Nincs dimenzió|
|eDTU_limit|az eDTU-korlát|Count|Average|az eDTU-korlát|Nincs dimenzió|
|storage_limit|Adatok maximális mérete|Bájt|Average|Tárolási kapacitása|Nincs dimenzió|
|eDTU_used|használt edtu-k|Count|Average|használt edtu-k|Nincs dimenzió|
|storage_used|Adatok felhasznált lemezterület|Bájt|Average|Felhasznált tárterület|Nincs dimenzió|
|xtp_storage_percent|Memóriabeli OLTP storage százalék|Percent|Average|Memóriabeli OLTP storage százalék|Nincs dimenzió|
|cpu_limit|CPU-korlát|Count|Average|CPU-korlát|Nincs dimenzió|
|cpu_used|CPU-kihasználtság|Count|Average|CPU-kihasználtság|Nincs dimenzió|
|allocated_data_storage|Adatok lefoglalt terület|Bájt|Average|Adatok lefoglalt terület|Nincs dimenzió|
|allocated_data_storage_percent|Adatok lefoglalt terület százaléka|Percent|Maximum|Adatok lefoglalt terület százaléka|Nincs dimenzió|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|virtual_core_count|Virtuális magok száma|Count|Average|Virtuális magok száma|Nincs dimenzió|
|avg_cpu_percent|Átlagos Processzorhasználat (%)|Percent|Average|Átlagos Processzorhasználat (%)|Nincs dimenzió|
|reserved_storage_mb|Lefoglalt lemezterület|Count|Average|Lefoglalt lemezterület|Nincs dimenzió|
|storage_space_used_mb|Felhasznált lemezterület|Count|Average|Felhasznált lemezterület|Nincs dimenzió|
|io_requests|I/o-kérelmek száma|Count|Average|I/o-kérelmek száma|Nincs dimenzió|
|io_bytes_read|Olvasási i/o-bájtok|Bájt|Average|Olvasási i/o-bájtok|Nincs dimenzió|
|io_bytes_written|I/o írt bájtok száma|Bájt|Average|I/o írt bájtok száma|Nincs dimenzió|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Használt kapacitás|Bájt|Átlag|A fiók felhasznált kapacitása|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|Blob-kapacitása|Bájt|Average|A tárfiók Blob Service-példánya által felhasznált tárterület mérete bájtban megadva.|BlobType, Tier|
|BlobCount|Blobok száma|Darabszám|Összes|A tárfiók Blob Service-példányában található blobok száma.|BlobType|       |BlobCount|Blobok száma|Count|Average|A tárfiók Blob Service-példányában található blobok száma.|BlobType, Tier|
|ContainerCount|Blobtárolók száma|Darabszám|Átlag|A tárfiók Blob Service-példányában található tárolók száma.|Nincs dimenzió|
|IndexCapacity|Index kapacitás|Bájt|Average|Az ADLS Gen2-bájtok száma (hierarchikus) Index által felhasznált tárterület mérete.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|File Storage kapacitása|Bájt|Average|A tárfiók File Storage-szolgáltatás (bájt) által felhasznált tárterület mennyisége.|Nincs dimenzió|
|FileCount|Fájlok száma|Count|Average|A tárfiók File Storage-szolgáltatás található fájlok száma.|Nincs dimenzió|
|FileShareCount|Fájlmegosztás|Count|Average|A tárfiók File Storage-szolgáltatás fájlmegosztások száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Queue Storage kapacitása|Bájt|Átlag|A tárfiók Queue Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|QueueCount|Üzenetsorok száma|Darabszám|Átlag|A tárfiók Queue-szolgáltatás-példányában található üzenetsorok száma.|Nincs dimenzió|
|QueueMessageCount|Üzenetsorbeli üzenetek száma|Darabszám|Átlag|A tárfiók Queue Storage-szolgáltatás-példányában található üzenetsorbeli üzenetek hozzávetőleges száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Table Storage kapacitása|Bájt|Átlag|A tárfiók Table Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|TableCount|Táblák száma|Darabszám|Átlag|A tárfiók Table Storage-szolgáltatás-példányában található táblák száma.|Nincs dimenzió|
|TableEntityCount|Táblaentitások száma|Darabszám|Átlag|A tárfiók Table Storage-szolgáltatás-példányában található táblaentitások száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Szinkronizálási munkamenet eredménye|Count|Average|A metrika, hogy minden 1 értékkel a naplók sikeres idő-e a kiszolgálói végpont befejeződik egy szinkronizálási munkamenetet a Felhőbeli végpont|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Fájlok szinkronizálása|Count|Összes|Száma a fájlok szinkronizálása|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Nem szinkronizálja a fájlokat|Count|Összes|Nem sikerült szinkronizálni a fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Szinkronizált bájt|Bájt|Összes|Szinkronizálási munkamenet során átvitt fájlok összesített mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Kiszolgáló Online állapotát|Count|Maximum|A metrika, hogy minden 1 értékkel a naplók sikeres idő-e a regisztrált kiszolgáló rögzíti a Felhőbeli végpont a szívverés|ServerName|
|StorageSyncRecallIOTotalSizeBytes|A felhő rétegezési visszaírási|Bájt|Összes|A kiszolgáló idézni adatok teljes mérete|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU százalékos kihasználtsága|Percent|Maximum|SU százalékos kihasználtsága|LogicalName, PartitionId|
|InputEvents|Bemeneti események|Count|Összes|Bemeneti események|LogicalName, PartitionId|
|InputEventBytes|Bemeneti esemény bájtokban|Bájt|Összes|Bemeneti esemény bájtokban|LogicalName, PartitionId|
|LateInputEvents|Késedelmes bemeneti események|Count|Összes|Késedelmes bemeneti események|LogicalName, PartitionId|
|Outputevents értéke|Kimeneti események|Count|Összes|Kimeneti események|LogicalName, PartitionId|
|ConversionErrors|Adatkonverziós hibák|Count|Összes|Adatkonverziós hibák|LogicalName, PartitionId|
|Hibák|Futásidejű hibák|Count|Összes|Futásidejű hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Üzemen kívüli események|Count|Összes|Üzemen kívüli események|LogicalName, PartitionId|
|AMLCalloutRequests|Függvénykérések|Count|Összes|Függvénykérések|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Sikertelen függvénykérések|Count|Összes|Sikertelen függvénykérések|LogicalName, PartitionId|
|AMLCalloutInputEvents|Függvényesemények|Count|Összes|Függvényesemények|LogicalName, PartitionId|
|DeserializationError|A deszerializálás bemeneti hibái|Count|Összes|A deszerializálás bemeneti hibái|LogicalName, PartitionId|
|EarlyInputEvents|Korai bemeneti események|Count|Összes|Korai bemeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Vízjel-késleltetés|másodperc|Maximum|Vízjel-késleltetés|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Várakozó bemeneti események|Count|Maximum|Várakozó bemeneti események|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Fogadott bemeneti források|Count|Összes|Fogadott bemeneti források|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő üzenetek fogadása|Count|Összes|Az összes eseményközpontot vagy IoT hubot eseményforrások olvasni az üzenetek száma|Nincs dimenzió|
|IngressReceivedInvalidMessages|Bejövő forgalom érvénytelen üzenet érkezett|Count|Összes|Az összes eseményközpontot vagy IoT hubot eseményforrások olvasni érvénytelen üzenetek száma|Nincs dimenzió|
|IngressReceivedBytes|Bejövő bájtokat fogadott|Bájt|Összes|Minden esemény forrásból beolvasott bájtok száma|Nincs dimenzió|
|IngressStoredBytes|Bejövő bájtok tárolt|Bájt|Összes|Sikeresen feldolgozott és lekérdezhető események teljes mérete|Nincs dimenzió|
|IngressStoredEvents|Bejövő forgalom tárolt események|Count|Összes|Sikeresen feldolgozott és lekérdezhető egybesimított események száma|Nincs dimenzió|
|IngressReceivedMessagesTimeLag|Bejövő forgalom a fogadott üzenetek idő elteltével|másodperc|Maximum|A, amely az üzenet az eseményforrás a várólistán lévő és az idő a bejövő forgalom feldolgozása közötti különbség|Nincs dimenzió|
|IngressReceivedMessagesCountLag|Bejövő forgalom a fogadott üzenetek száma késés|Count|Average|Utolsó várólistán lévő üzenetek sorszáma közötti különbség az adatforrás a bejövő forgalom feldolgozott üzenet partíció- és feladatütemezési száma|Nincs dimenzió|
|WarmStorageMaxProperties|Meleg Tárolótulajdonságok maximális száma|Count|Maximum|S1 vagy S2 Termékváltozat és a Tulajdonságok PAYG termékváltozat meleg Store által engedélyezett maximális száma a környezet által engedélyezett maximális számát használt tulajdonságok|Nincs dimenzió|
|WarmStorageUsedProperties|Meleg tárolására használt tulajdonságai |Count|Maximum|S1 vagy S2 Termékváltozat és a Használatalapú termékváltozat meleg Store által használt tulajdonságok száma a környezet által használt tulajdonságok száma|Nincs dimenzió|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő üzenetek fogadása|Count|Összes|Az eseményforrás olvasni üzenetek száma|Nincs dimenzió|
|IngressReceivedInvalidMessages|Bejövő forgalom érvénytelen üzenet érkezett|Count|Összes|Az eseményforrás olvasni érvénytelen üzenetek száma|Nincs dimenzió|
|IngressReceivedBytes|Bejövő bájtokat fogadott|Bájt|Összes|Az esemény forrásból beolvasott bájtok száma|Nincs dimenzió|
|IngressStoredBytes|Bejövő bájtok tárolt|Bájt|Összes|Sikeresen feldolgozott és lekérdezhető események teljes mérete|Nincs dimenzió|
|IngressStoredEvents|Bejövő forgalom tárolt események|Count|Összes|Sikeresen feldolgozott és lekérdezhető egybesimított események száma|Nincs dimenzió|
|IngressReceivedMessagesTimeLag|Bejövő forgalom a fogadott üzenetek idő elteltével|másodperc|Maximum|A, amely az üzenet az eseményforrás a várólistán lévő és az idő a bejövő forgalom feldolgozása közötti különbség|Nincs dimenzió|
|IngressReceivedMessagesCountLag|Bejövő forgalom a fogadott üzenetek száma késés|Count|Average|Utolsó várólistán lévő üzenetek sorszáma közötti különbség az adatforrás a bejövő forgalom feldolgozott üzenet partíció- és feladatütemezési száma|Nincs dimenzió|
|WarmStorageMaxProperties|Meleg Tárolótulajdonságok maximális száma|Count|Maximum|S1 vagy S2 Termékváltozat és a Tulajdonságok PAYG termékváltozat meleg Store által engedélyezett maximális száma a környezet által engedélyezett maximális számát használt tulajdonságok|Nincs dimenzió|
|WarmStorageUsedProperties|Meleg tárolására használt tulajdonságai |Count|Maximum|S1 vagy S2 Termékváltozat és a Használatalapú termékváltozat meleg Store által használt tulajdonságok száma a környezet által használt tulajdonságok száma|Nincs dimenzió|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Lemez sebessége olvasott bájt/mp|BytesPerSecond|Average|Átlagos adatátviteli sebességet mintavételi időszak alatt az olvasási műveletek miatt.|Nincs dimenzió|
|DiskWriteBytesPerSecond|Lemez írási bájt/mp-ben|BytesPerSecond|Average|Átlagos adatátviteli sebességet mintavételi időszak alatt az írási műveletek miatt.|Nincs dimenzió|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|Lemez teljes átviteli sebesség a minta időszakban olvasási műveletek miatt.|Nincs dimenzió|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|Lemez teljes átviteli sebesség mintavételi időszak alatt az írási műveletek miatt.|Nincs dimenzió|
|DiskReadOperations|Lemez olvasási művelet|Count|Összes|I/o száma az előző példa időszakban olvasási műveleteket. Előfordulhat, hogy ezeket a műveleteket változó méretű.|Nincs dimenzió|
|DiskWriteOperations|Lemezre írási műveletek|Count|Összes|I/o száma az előző példa időszakban írási műveletek. Előfordulhat, hogy ezeket a műveleteket változó méretű.|Nincs dimenzió|
|Lemezolvasási művelet/mp|Lemezolvasási művelet/mp|CountPerSecond|Average|I/o átlagos száma az előző példa időszakban olvasási műveleteket. Előfordulhat, hogy ezeket a műveleteket változó méretű.|Nincs dimenzió|
|Lemezre írási művelet/mp|Lemezre írási művelet/mp|CountPerSecond|Average|I/o átlagos száma az előző példa időszakban írási műveletek. Előfordulhat, hogy ezeket a műveleteket változó méretű.|Nincs dimenzió|
|DiskReadLatency|Lemez olvasási késése|Ezredmásodperc|Average|Teljes olvasási késés. Az eszköz és a kernel összege olvasási késéseket.|Nincs dimenzió|
|DiskWriteLatency|Lemez írási késése|Ezredmásodperc|Average|Összesített írási késése. Az eszköz és a kernel összege írási késése.|Nincs dimenzió|
|NetworkInBytesPerSecond|A bájtok/s hálózat|BytesPerSecond|Average|Átlagos hálózati átviteli sebesség a kapott forgalmat.|Nincs dimenzió|
|NetworkOutBytesPerSecond|Hálózati ki (bájt/mp)|BytesPerSecond|Average|Átlagos hálózati átviteli sebesség továbbított forgalmat.|Nincs dimenzió|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|Teljes hálózati átviteli sebesség a kapott forgalmat.|Nincs dimenzió|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|Teljes hálózati átviteli sebesség továbbított forgalmat.|Nincs dimenzió|
|MemoryUsed|Használt memória|Bájt|Average|Használja a virtuális gép memória mennyiségét.|Nincs dimenzió|
|MemoryGranted|Biztosított memória|Bájt|Average|Meg lett adva a virtuális gép a gazdagép által memória mennyiségét. Memória nem rendelkezik a gazdagéphez, amíg egy alkalommal van csinált semmit, és a biztosított memória előfordulhat, hogy a lapozó vagy ballooned nyelvet a lenti listában, ha a VMkernel van szüksége, a memória.|Nincs dimenzió|
|MemoryActive|Memória aktív|Bájt|Average|Az elmúlt kis időtartam a virtuális gép által használt memória mennyisége. Ez a "true" száma mennyi memóriát a virtuális gép jelenleg van szükség. További, a fel nem használt memória, a lapozó vagy ballooned befolyásolása nélkül a Vendég-teljesítményre.|Nincs dimenzió|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Average|A CPU-kihasználtság. Ezt az értéket a rendszer az összes processzormag jelölő 100 %-os jelentett. Tegyük fel tükrözés 2 virtuális gép használatával 50 %-a egy 4 magos rendszer teljesen két magot használ.|Nincs dimenzió|
|PercentageCpuReady|Százalékos Processzorhasználat kész|Ezredmásodperc|Összes|Kész idő az az idő Várakozás CPU(s) a korábbi frissítési időköz az elérhető legyen a költségek.|Nincs dimenzió|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Percent|Average|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Count|Average|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Count|Average|HTTP-várólista hossza|Példány|
|BytesReceived|Az adatok|Bájt|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (kivéve a functions)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összes|CPU-idő|Példány|
|Kérelmek|Kérelmek|Count|Összes|Kérelmek|Példány|
|BytesReceived|Az adatok|Bájt|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Count|Összes|HTTP 101|Példány|
|Http2xx|Http 2xx|Count|Összes|Http 2xx|Példány|
|Http3xx|Http 3xx|Count|Összes|Http 3xx|Példány|
|Http401|Http 401|Count|Összes|Http 401|Példány|
|Http403|A HTTP 403-as|Count|Összes|A HTTP 403-as|Példány|
|Http404|Http 404|Count|Összes|Http 404|Példány|
|Http406|Http 406|Count|Összes|Http 406|Példány|
|Http4xx|Http 4xx|Count|Összes|Http 4xx|Példány|
|Http5xx|HTTP Kiszolgálóhibák|Count|Összes|HTTP Kiszolgálóhibák|Példány|
|MemoryWorkingSet|Memória-Munkakészlet|Bájt|Average|Memória-Munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Average|Átlagos memória-munkakészlet|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Average|Átlagos válaszidő|Példány|
|AppConnections|Kapcsolatok|Count|Average|Kapcsolatok|Példány|
|Kezeli a|Az ablakleírók száma|Count|Average|Az ablakleírók száma|Példány|
|Szálak|Szálak száma|Count|Average|Szálak száma|Példány|
|PrivateBytes|A saját memória|Bájt|Average|A saját memória|Példány|
|IoReadBytesPerSecond|I/o-olvasási bájt / másodperc|BytesPerSecond|Összes|I/o-olvasási bájt / másodperc|Példány|
|IoWriteBytesPerSecond|I/o-írási bájt / másodperc|BytesPerSecond|Összes|I/o-írási bájt / másodperc|Példány|
|IoOtherBytesPerSecond|I/o más bájtok másodpercenként|BytesPerSecond|Összes|I/o más bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek száma másodpercenként|BytesPerSecond|Összes|I/o-olvasási műveletek száma másodpercenként|Példány|
|IoWriteOperationsPerSecond|I/o-írási műveletek száma másodpercenként|BytesPerSecond|Összes|I/o-írási műveletek száma másodpercenként|Példány|
|IoOtherOperationsPerSecond|I/o egyéb műveletek száma másodpercenként|BytesPerSecond|Összes|I/o egyéb műveletek száma másodpercenként|Példány|
|RequestsInApplicationQueue|Alkalmazás-várólistán lévő kérelmek|Count|Average|Alkalmazás-várólistán lévő kérelmek|Példány|
|CurrentAssemblies|Szerelvények pillanatnyi száma|Count|Average|Szerelvények pillanatnyi száma|Példány|
|TotalAppDomains|Alkalmazástartományok összesen|Count|Average|Alkalmazástartományok összesen|Példány|
|TotalAppDomainsUnloaded|Memóriából eltávolított alkalmazástartományok összesen|Count|Average|Memóriából eltávolított alkalmazástartományok összesen|Példány|
|Gen0Collections|0\. generációs szemétgyűjtések száma|Count|Összes|0\. generációs szemétgyűjtések száma|Példány|
|Gen1Collections|1\. generációs szemétgyűjtések száma|Count|Összes|1\. generációs szemétgyűjtések száma|Példány|
|Gen2Collections|2\. generációs szemétgyűjtések száma|Count|Összes|2\. generációs szemétgyűjtések száma|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|Az adatok|Bájt|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http5xx|HTTP Kiszolgálóhibák|Count|Összes|HTTP Kiszolgálóhibák|Példány|
|MemoryWorkingSet|Memória-Munkakészlet|Bájt|Average|Memória-Munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Average|Átlagos memória-munkakészlet|Példány|
|FunctionExecutionUnits|Függvény-végrehajtási egység|MB / idő ezredmásodpercben|Összes|[Függvény-végrehajtási egység](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Count|Összes|Függvény végrehajtásainak száma|Példány|
|PrivateBytes|A saját memória|Bájt|Average|A saját memória|Példány|
|IoReadBytesPerSecond|I/o-olvasási bájt / másodperc|BytesPerSecond|Összes|I/o-olvasási bájt / másodperc|Példány|
|IoWriteBytesPerSecond|I/o-írási bájt / másodperc|BytesPerSecond|Összes|I/o-írási bájt / másodperc|Példány|
|IoOtherBytesPerSecond|I/o más bájtok másodpercenként|BytesPerSecond|Összes|I/o más bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek száma másodpercenként|BytesPerSecond|Összes|I/o-olvasási műveletek száma másodpercenként|Példány|
|IoWriteOperationsPerSecond|I/o-írási műveletek száma másodpercenként|BytesPerSecond|Összes|I/o-írási műveletek száma másodpercenként|Példány|
|IoOtherOperationsPerSecond|I/o egyéb műveletek száma másodpercenként|BytesPerSecond|Összes|I/o egyéb műveletek száma másodpercenként|Példány|
|RequestsInApplicationQueue|Alkalmazás-várólistán lévő kérelmek|Count|Average|Alkalmazás-várólistán lévő kérelmek|Példány|
|CurrentAssemblies|Szerelvények pillanatnyi száma|Count|Average|Szerelvények pillanatnyi száma|Példány|
|TotalAppDomains|Alkalmazástartományok összesen|Count|Average|Alkalmazástartományok összesen|Példány|
|TotalAppDomainsUnloaded|Memóriából eltávolított alkalmazástartományok összesen|Count|Average|Memóriából eltávolított alkalmazástartományok összesen|Példány|
|Gen0Collections|0\. generációs szemétgyűjtések száma|Count|Összes|0\. generációs szemétgyűjtések száma|Példány|
|Gen1Collections|1\. generációs szemétgyűjtések száma|Count|Összes|1\. generációs szemétgyűjtések száma|Példány|
|Gen2Collections|2\. generációs szemétgyűjtések száma|Count|Összes|2\. generációs szemétgyűjtések száma|Példány|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összes|CPU-idő|Példány|
|Kérelmek|Kérelmek|Count|Összes|Kérelmek|Példány|
|BytesReceived|Az adatok|Bájt|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Count|Összes|HTTP 101|Példány|
|Http2xx|Http 2xx|Count|Összes|Http 2xx|Példány|
|Http3xx|Http 3xx|Count|Összes|Http 3xx|Példány|
|Http401|Http 401|Count|Összes|Http 401|Példány|
|Http403|A HTTP 403-as|Count|Összes|A HTTP 403-as|Példány|
|Http404|Http 404|Count|Összes|Http 404|Példány|
|Http406|Http 406|Count|Összes|Http 406|Példány|
|Http4xx|Http 4xx|Count|Összes|Http 4xx|Példány|
|Http5xx|HTTP Kiszolgálóhibák|Count|Összes|HTTP Kiszolgálóhibák|Példány|
|MemoryWorkingSet|Memória-Munkakészlet|Bájt|Average|Memória-Munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Average|Átlagos memória-munkakészlet|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Average|Átlagos válaszidő|Példány|
|FunctionExecutionUnits|Függvény-végrehajtási egység|Count|Összes|Függvény-végrehajtási egység|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Count|Összes|Függvény végrehajtásainak száma|Példány|
|AppConnections|Kapcsolatok|Count|Average|Kapcsolatok|Példány|
|Kezeli a|Az ablakleírók száma|Count|Average|Az ablakleírók száma|Példány|
|Szálak|Szálak száma|Count|Average|Szálak száma|Példány|
|PrivateBytes|A saját memória|Bájt|Average|A saját memória|Példány|
|IoReadBytesPerSecond|I/o-olvasási bájt / másodperc|BytesPerSecond|Összes|I/o-olvasási bájt / másodperc|Példány|
|IoWriteBytesPerSecond|I/o-írási bájt / másodperc|BytesPerSecond|Összes|I/o-írási bájt / másodperc|Példány|
|IoOtherBytesPerSecond|I/o más bájtok másodpercenként|BytesPerSecond|Összes|I/o más bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek száma másodpercenként|BytesPerSecond|Összes|I/o-olvasási műveletek száma másodpercenként|Példány|
|IoWriteOperationsPerSecond|I/o-írási műveletek száma másodpercenként|BytesPerSecond|Összes|I/o-írási műveletek száma másodpercenként|Példány|
|IoOtherOperationsPerSecond|I/o egyéb műveletek száma másodpercenként|BytesPerSecond|Összes|I/o egyéb műveletek száma másodpercenként|Példány|
|RequestsInApplicationQueue|Alkalmazás-várólistán lévő kérelmek|Count|Average|Alkalmazás-várólistán lévő kérelmek|Példány|
|CurrentAssemblies|Szerelvények pillanatnyi száma|Count|Average|Szerelvények pillanatnyi száma|Példány|
|TotalAppDomains|Alkalmazástartományok összesen|Count|Average|Alkalmazástartományok összesen|Példány|
|TotalAppDomainsUnloaded|Memóriából eltávolított alkalmazástartományok összesen|Count|Average|Memóriából eltávolított alkalmazástartományok összesen|Példány|
|Gen0Collections|0\. generációs szemétgyűjtések száma|Count|Összes|0\. generációs szemétgyűjtések száma|Példány|
|Gen1Collections|1\. generációs szemétgyűjtések száma|Count|Összes|1\. generációs szemétgyűjtések száma|Példány|
|Gen2Collections|2\. generációs szemétgyűjtések száma|Count|Összes|2\. generációs szemétgyűjtések száma|Példány|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Count|Összes|Kérelmek|Példány|
|BytesReceived|Az adatok|Bájt|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Count|Összes|HTTP 101|Példány|
|Http2xx|Http 2xx|Count|Összes|Http 2xx|Példány|
|Http3xx|Http 3xx|Count|Összes|Http 3xx|Példány|
|Http401|Http 401|Count|Összes|Http 401|Példány|
|Http403|A HTTP 403-as|Count|Összes|A HTTP 403-as|Példány|
|Http404|Http 404|Count|Összes|Http 404|Példány|
|Http406|Http 406|Count|Összes|Http 406|Példány|
|Http4xx|Http 4xx|Count|Összes|Http 4xx|Példány|
|Http5xx|HTTP Kiszolgálóhibák|Count|Összes|HTTP Kiszolgálóhibák|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Average|Átlagos válaszidő|Példány|
|CpuPercentage|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Percent|Average|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Count|Average|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Count|Average|HTTP-várólista hossza|Példány|
|ActiveRequests|Aktív kérések|Count|Összes|Aktív kérések|Példány|
|TotalFrontEnds|Előterek száma|Count|Average|Előterek száma|Nincs dimenzió|
|SmallAppServicePlanInstances|Kis méretű App Service-csomag feldolgozói|Count|Average|Kis méretű App Service-csomag feldolgozói|Nincs dimenzió|
|MediumAppServicePlanInstances|Közepes méretű App Service-csomag feldolgozói|Count|Average|Közepes méretű App Service-csomag feldolgozói|Nincs dimenzió|
|LargeAppServicePlanInstances|Nagy méretű App Service-csomag feldolgozói|Count|Average|Nagy méretű App Service-csomag feldolgozói|Nincs dimenzió|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Feldolgozók|Feldolgozók összesen|Count|Average|Feldolgozók összesen|Nincs dimenzió|
|WorkersAvailable|Rendelkezésre álló feldolgozók|Count|Average|Rendelkezésre álló feldolgozók|Nincs dimenzió|
|Napon használt|Használt feldolgozók|Count|Average|Használt feldolgozók|Nincs dimenzió|
|CpuPercentage|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Percent|Average|Memóriahasználat (%)|Példány|

## <a name="next-steps"></a>További lépések
* [További információ a metrikák az Azure monitorban](data-platform.md)
* [Riasztások létrehozása metrikákhoz](alerts-overview.md)
* [A storage, az Event Hubs vagy a Log Analytics metrikák exportálása](diagnostic-logs-overview.md)
