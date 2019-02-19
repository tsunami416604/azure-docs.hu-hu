---
title: Az Azure Monitor támogatott mérőszámok erőforrástípus szerint
description: A metrikák elérhető az Azure Monitor az egyes erőforrástípusok listája.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/14/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 8ee900554371644f374e4aeed51f1eeb0c18569e
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408867"
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
|qpu_metric|QPU|Darabszám|Átlag|QPU. Tartomány 0 és 100 s1, 0 – 200 – S2 és 0 – 400 – S4|ServerResourceType|
|memory_metric|Memory (Memória)|Bájt|Átlag|A memória. Tartomány 0 – 25 GB – S1, 0-50 GB – S2 és 0 – 100 GB – S4|ServerResourceType|
|TotalConnectionRequests|Csatlakozási kérelmek teljes száma|Darabszám|Átlag|Csatlakozási kérelmek teljes száma. Ezek a beérkező kérelmek.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|Egység/s|Átlag|A sikeres kapcsolat befejezésekből sebessége.|ServerResourceType|
|TotalConnectionFailures|Csatlakozási hibák teljes száma|Darabszám|Átlag|A sikertelen csatlakozási kísérletek összesen.|ServerResourceType|
|CurrentUserSessions|Jelenlegi felhasználói munkamenetek|Darabszám|Átlag|A létrehozott felhasználói munkamenetek aktuális száma.|ServerResourceType|
|QueryPoolBusyThreads|Lekérdezési készlet foglalt szálai|Darabszám|Átlag|A lekérdezési szálkészletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolJobQueueLength|A parancs feladat-várólistájának hossza|Darabszám|Átlag|Az a parancs szálkészlet üzenetsorában található feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Feldolgozási készlet feladat-várólistájának hossza|Darabszám|Átlag|Az a feldolgozási szálkészlet üzenetsorában lévő nem I/o feladatok száma.|ServerResourceType|
|CurrentConnections|Kapcsolat: Jelenlegi kapcsolatok száma|Darabszám|Átlag|A létrehozott ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CleanerCurrentPrice|Memória: Tisztító – aktuális ár|Darabszám|Átlag|Aktuális ára $/ bájt/idő, a memória, 1000-re normalizálva.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: Tisztító – zsugorítható memória|Bájt|Átlag|Memória (bájt) vonatkoznak a háttérben futó tisztító kiürítése mennyisége.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Tisztább memória – nem zsugorítható|Bájt|Átlag|Memória (bájt) nem vonatkoznak a háttérben futó tisztító kiürítése mennyisége.|ServerResourceType|
|MemoryUsage|Memória: Memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat tisztító memória ár kiszámításakor memóriahasználat. Számláló Process\PrivateBytes plusz a memória által leképezett adatok figyelmen kívül hagyva amely leképezett vagy lefoglalt által az xVelocity memóriabeli elemzési motor (VertiPaq) az xVelocity motor memóriakorlátját mérete megegyezik.|ServerResourceType|
|MemoryLimitHard|Memória: Szigorú Memóriakorlát|Bájt|Átlag|Szigorú Memóriakorlát a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Memória: Felső Memóriakorlát|Bájt|Átlag|Felső Memóriakorlát a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Memória: Alsó Memóriakorlát|Bájt|Átlag|Alsó Memóriakorlát a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: VertiPaq-Memóriakorlát|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|Kvóta|Memória: Kvóta|Bájt|Átlag|Aktuális memóriakvóta bájtban. A memóriakvóta memória grant vagy a memória foglalás is nevezik.|ServerResourceType|
|QuotaBlocked|Memória: Blokkolt kvóta|Darabszám|Átlag|Aktuális száma, amelyek le vannak tiltva, amíg a további Memóriakvóták felszabadítását.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq Nonpaged|Bájt|Átlag|Bájt memóriát a memóriabeli motor általi használatra munkakészletében lévő zárolva van.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq Paged|Bájt|Átlag|Lapozható memória, a memóriában lévő adatok bájt.|ServerResourceType|
|RowsReadPerSec|Feldolgozás: Másodpercenként beolvasott sorok száma|Egység/s|Átlag|Sorok beolvasásának sebessége az összes relációs adatbázisból.|ServerResourceType|
|RowsConvertedPerSec|Feldolgozás: Másodpercenként konvertált sorok|Egység/s|Átlag|Sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsWrittenPerSec|Feldolgozás: Másodpercenként írt sorok|Egység/s|Átlag|Sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|CommandPoolBusyThreads|Szálak: A parancs parancskészlet – foglalt szálak|Darabszám|Átlag|A parancs szálkészletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Szálak: A parancs készlet – üresjárati szálak|Darabszám|Átlag|A parancsszálkészletben az üresjárati szálak száma.|ServerResourceType|
|LongParsingBusyThreads|Szálak: Tartós folyamatok elemzési foglalt szálak|Darabszám|Átlag|A tartós folyamatok elemzési szálkészletben lévő foglalt szálak száma.|ServerResourceType|
|LongParsingIdleThreads|Szálak: Tartós folyamatok elemzési üresjárati szálak|Darabszám|Átlag|A tartós folyamatok elemzési szálkészletében az üresjárati szálak száma.|ServerResourceType|
|LongParsingJobQueueLength|Szálak: Tartós folyamatok elemzési feladat üzenetsorának hossza|Darabszám|Átlag|A tartós folyamatok elemzési szálkészlet üzenetsorában található feladatok száma.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Szálak: Készlet foglalt i/o-feladat szálak feldolgozása|Darabszám|Átlag|A feldolgozási szálkészletben I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Szálak: Feldolgozási készlet a nem I/o feladat foglalt szálai|Darabszám|Átlag|A feldolgozási szálkészletben nem I/o feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Szálak: Feldolgozási készlet – I/O feladat üzenetsorának hossza|Darabszám|Átlag|Az a feldolgozási szálkészlet üzenetsorában található I/O feladatok száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Szálak: Feldolgozási készlet – üresjárati i/o feladat szálak|Darabszám|Átlag|A feldolgozási szálkészletben I/O feladatok üresjárati szálak száma.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Szálak: Feldolgozási készlet a nem I/o feladat üresjárati szálai|Darabszám|Átlag|A feldolgozási szálkészletben nem I/o feladatok számára kijelölt üresjárati szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Szálak: Lekérdezési készlet – üresjárati szálak|Darabszám|Átlag|A feldolgozási szálkészletben I/O feladatok üresjárati szálak száma.|ServerResourceType|
|QueryPoolJobQueueLength|Szálak: Lekérdezési készlet feladat-várólistájának hossza|Darabszám|Átlag|Az a lekérdezési szálkészlet üzenetsorában található feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak: Rövid elemzés a foglalt szálak|Darabszám|Átlag|A rövid elemzési szálkészletben lévő foglalt szálak száma.|ServerResourceType|
|ShortParsingIdleThreads|Szálak: Rövid elemzési üresjárati szálak|Darabszám|Átlag|A rövid elemzési szálkészletben az üresjárati szálak száma.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak: Rövid elemzési feladat üzenetsorának hossza|Darabszám|Átlag|A a rövid elemzési szálkészlet üzenetsorában található feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Átlagos memóriaakadozás.|ServerResourceType|
|mashup_engine_qpu_metric|M motor qpu-ja|Darabszám|Átlag|Az adategyesítési motor folyamatainak QPU használatáról|ServerResourceType|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Átlag|Az adategyesítési motor folyamatainak memóriafelhasználása|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Összes Átjárókérés|Darabszám|Összes|Átjáró-kérelmek száma|Hely, gazdagépnév|
|SuccessfulRequests|Sikeres Átjárókérések|Darabszám|Összes|Átjáró sikeres kérelmek száma|Hely, gazdagépnév|
|UnauthorizedRequests|Jogosulatlan Átjárókérések|Darabszám|Összes|Jogosulatlan átjárókérések száma|Hely, gazdagépnév|
|FailedRequests|Sikertelen Átjárókérések|Darabszám|Összes|Sikertelen átjárókérések a száma|Hely, gazdagépnév|
|OtherRequests|Egyéb Átjárókérések|Darabszám|Összes|Egyéb átjárókérések száma|Hely, gazdagépnév|
|Időtartam|Teljes időtartama Átjárókérések|Ezredmásodperc|Átlag|Teljes időtartam, Átjárókérések ezredmásodpercben|Hely, gazdagépnév|
|Kapacitás|Kapacitás|Százalék|Átlag|Az ApiManagement szolgáltatás Utilization metrika|Hely|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok száma összesen|Darabszám|Összes|A feladatok teljes száma|A Runbook állapota|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált magok száma|Darabszám|Összes|A batch-fiókban lévő dedikált magok teljes száma|Nincs dimenzió|
|TotalNodeCount|Dedikált csomópontok száma|Darabszám|Összes|A batch-fiókban lévő dedikált csomópontok száma összesen|Nincs dimenzió|
|LowPriorityCoreCount|LowPriority magok száma|Darabszám|Összes|A batch-fiókban az alacsony prioritású magok teljes száma|Nincs dimenzió|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Darabszám|Összes|A batch-fiókban az alacsony prioritású csomópontok száma összesen|Nincs dimenzió|
|CreatingNodeCount|Csomópontok száma létrehozása|Darabszám|Összes|A létrehozott csomópontok száma|Nincs dimenzió|
|StartingNodeCount|Csomópontok száma indítása|Darabszám|Összes|A kezdési csomópontok száma|Nincs dimenzió|
|WaitingForStartTaskNodeCount|Várakozás a kezdő tevékenység csomópontok száma|Darabszám|Összes|A Start feladat befejezésére való várakozás csomópontok száma|Nincs dimenzió|
|StartTaskFailedNodeCount|Kezdő tevékenység sikertelen volt a csomópontok száma|Darabszám|Összes|Ahol a feladat indítása sikertelen volt a csomópontok száma|Nincs dimenzió|
|IdleNodeCount|Inaktív csomópontok száma|Darabszám|Összes|Tétlen csomópontok száma|Nincs dimenzió|
|OfflineNodeCount|A kapcsolat nélküli csomópontok száma|Darabszám|Összes|A kapcsolat nélküli csomópontok száma|Nincs dimenzió|
|RebootingNodeCount|Rendszer újraindítása a csomópontok száma|Darabszám|Összes|Rendszer újraindítása a csomópontok száma|Nincs dimenzió|
|ReimagingNodeCount|Csomópontok száma rendszerképének alaphelyzetbe állítása|Darabszám|Összes|Csomópont rendszerképének alaphelyzetbe állítása száma|Nincs dimenzió|
|RunningNodeCount|Futó csomópontok száma|Darabszám|Összes|Futó csomópontok száma|Nincs dimenzió|
|LeavingPoolNodeCount|Csomópontok száma készlet elhagyása|Darabszám|Összes|A csomópontok a készlet elhagyása|Nincs dimenzió|
|UnusableNodeCount|Használhatatlan csomópontok száma|Darabszám|Összes|Használhatatlan csomópontok száma|Nincs dimenzió|
|PreemptedNodeCount|A leállított csomópontok száma|Darabszám|Összes|A leállított csomópontok száma|Nincs dimenzió|
|TaskStartEvent|Tevékenység indítása esemény|Darabszám|Összes|Megkezdte az feladatok száma összesen|Nincs dimenzió|
|TaskCompleteEvent|Tevékenység kész esemény|Darabszám|Összes|A befejezett feladatok száma összesen|Nincs dimenzió|
|TaskFailEvent|Tevékenység meghiúsult esemény|Darabszám|Összes|A sikertelen állapotú befejezett feladatok száma összesen|Nincs dimenzió|
|PoolCreateEvent|Készlet létrehozása esemény|Darabszám|Összes|Létrehozott gyűjtők száma összesen|Nincs dimenzió|
|PoolResizeStartEvent|Készlet átméretezésének indítása események|Darabszám|Összes|Készlet átméretezi, amelyek elindultak száma összesen|Nincs dimenzió|
|PoolResizeCompleteEvent|Készlet átméretezése kész események|Darabszám|Összes|Befejezett készlet átméretezi száma összesen|Nincs dimenzió|
|PoolDeleteStartEvent|Készlet törlésének indítása események|Darabszám|Összes|Teljes száma, amelyek elindultak készlet törlése|Nincs dimenzió|
|PoolDeleteCompleteEvent|Készlet törlése kész események|Darabszám|Összes|Befejezett készlet törlések száma összesen|Nincs dimenzió|
|JobDeleteCompleteEvent|Feladat befejezése események törlése|Darabszám|Összes|Sikeresen törölt feladatok teljes száma.|Nincs dimenzió|
|JobDeleteStartEvent|Feladat kezdő-események törlése|Darabszám|Összes|A törlendő kérő feladatok teljes száma.|Nincs dimenzió|
|JobDisableCompleteEvent|Feladat letiltása befejeződött esemény|Darabszám|Összes|Sikerült letiltani feladatok teljes száma.|Nincs dimenzió|
|JobDisableStartEvent|Feladat letiltása indítási események|Darabszám|Összes|Kért, le kell tiltani feladatok teljes száma.|Nincs dimenzió|
|JobStartEvent|Feladat indítása esemény|Darabszám|Összes|Sikeresen elindult feladatok teljes száma.|Nincs dimenzió|
|JobTerminateCompleteEvent|Feladat leállítása befejeződött esemény|Darabszám|Összes|Sikerült leállítani feladatok teljes száma.|Nincs dimenzió|
|JobTerminateStartEvent|Feladat leállítása indítása esemény|Darabszám|Összes|Kért, megszakítandó feladatok teljes száma.|Nincs dimenzió|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakoztatott ügyfélrendszerek|Darabszám|Maximum||ShardId|
|totalcommandsprocessed|Műveletek összesen|Darabszám|Összes||ShardId|
|cachehits|Gyorsítótárbeli találatok|Darabszám|Összes||ShardId|
|cachemisses|Gyorsítótárbeli tévesztések|Darabszám|Összes||ShardId|
|getcommands|Lekérdezések|Darabszám|Összes||ShardId|
|setcommands|Beállítások|Darabszám|Összes||ShardId|
|operationsPerSecond|Műveletek száma másodpercenként|Darabszám|Maximum||ShardId|
|evictedkeys|Kizárt kulcsok|Darabszám|Összes||ShardId|
|totalkeys|Kulcsok összesen|Darabszám|Maximum||ShardId|
|expiredkeys|Lejárt kulcsok|Darabszám|Összes||ShardId|
|usedmemory|Felhasznált memória|Bájt|Maximum||ShardId|
|usedmemorypercentage|Használt memória százalékos aránya|Százalék|Maximum||ShardId|
|usedmemoryRss|Használt memória RSS|Bájt|Maximum||ShardId|
|serverLoad|Kiszolgáló-terhelés|Százalék|Maximum||ShardId|
|cacheWrite|Gyorsítótár-írás|Bájt/s|Maximum||ShardId|
|cacheRead|Gyorsítótár-olvasás|Bájt/s|Maximum||ShardId|
|percentProcessorTime|CPU|Százalék|Maximum||ShardId|
|cacheLatency|Gyorsítótár késés mikroszekundum (előzetes verzió)|Darabszám|Átlag||ShardId, SampleType|
|Hibák|Hibák|Darabszám|Maximum||ShardId, ErrorType|
|connectedclients0|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 0)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed0|Műveletek összesen (horizontálisan Skálázáson 0)|Darabszám|Összes||Nincs dimenzió|
|cachehits0|Cache Hits (Shard 0)|Darabszám|Összes||Nincs dimenzió|
|cachemisses0|Cache Misses (Shard 0)|Darabszám|Összes||Nincs dimenzió|
|getcommands0|Lekérdezi a (horizontálisan Skálázáson 0)|Darabszám|Összes||Nincs dimenzió|
|setcommands0|Csoportok (horizontálisan Skálázáson 0)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond0|Műveletek száma másodpercenként (horizontálisan Skálázáson 0)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys0|Kizárt kulcsok (horizontálisan Skálázáson 0)|Darabszám|Összes||Nincs dimenzió|
|totalkeys0|Kulcsok összesen (horizontálisan Skálázáson 0)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys0|Lejárt kulcsok (horizontálisan Skálázáson 0)|Darabszám|Összes||Nincs dimenzió|
|usedmemory0|Használt memória (horizontálisan Skálázáson 0)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss0|Használt memória RSS (horizontálisan Skálázáson 0)|Bájt|Maximum||Nincs dimenzió|
|serverLoad0|Kiszolgáló terhelése (horizontálisan Skálázáson 0)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite0|Gyorsítótár-írás (horizontálisan Skálázáson 0)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead0|Gyorsítótár-olvasás (horizontálisan Skálázáson 0)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime0|Processzor (horizontálisan Skálázáson 0)|Százalék|Maximum||Nincs dimenzió|
|connectedclients1|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 1)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed1|Műveletek összesen (horizontálisan Skálázáson 1)|Darabszám|Összes||Nincs dimenzió|
|cachehits1|Cache Hits (Shard 1)|Darabszám|Összes||Nincs dimenzió|
|cachemisses1|Gyorsítótárbeli (horizontálisan Skálázáson 1)|Darabszám|Összes||Nincs dimenzió|
|getcommands1|(Horizontálisan Skálázáson 1) beolvasása|Darabszám|Összes||Nincs dimenzió|
|setcommands1|Csoportok (horizontálisan Skálázáson 1)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond1|Műveletek száma másodpercenként (horizontálisan Skálázáson 1)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys1|Kizárt kulcsok (horizontálisan Skálázáson 1)|Darabszám|Összes||Nincs dimenzió|
|totalkeys1|Kulcsok összesen (horizontálisan Skálázáson 1)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys1|Lejárt kulcsok (horizontálisan Skálázáson 1)|Darabszám|Összes||Nincs dimenzió|
|usedmemory1|Használt memória (horizontálisan Skálázáson 1)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss1|Használt memória RSS (horizontálisan Skálázáson 1)|Bájt|Maximum||Nincs dimenzió|
|serverLoad1|Kiszolgáló terhelése (horizontálisan Skálázáson 1)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite1|Gyorsítótár-írás (horizontálisan Skálázáson 1)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead1|Gyorsítótár-olvasás (horizontálisan Skálázáson 1)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime1|Processzor (horizontálisan Skálázáson 1)|Százalék|Maximum||Nincs dimenzió|
|connectedclients2|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 2)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed2|Műveletek összesen (horizontálisan Skálázáson 2)|Darabszám|Összes||Nincs dimenzió|
|cachehits2|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 2)|Darabszám|Összes||Nincs dimenzió|
|cachemisses2|Gyorsítótárbeli (horizontálisan Skálázáson 2)|Darabszám|Összes||Nincs dimenzió|
|getcommands2|(2 horizontálisan Skálázáson) beolvasása|Darabszám|Összes||Nincs dimenzió|
|setcommands2|Csoportok (horizontálisan Skálázáson 2)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond2|Műveletek száma másodpercenként (horizontálisan Skálázáson 2)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys2|Kizárt kulcsok (horizontálisan Skálázáson 2)|Darabszám|Összes||Nincs dimenzió|
|totalkeys2|Kulcsok összesen (horizontálisan Skálázáson 2)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys2|Lejárt kulcsok (horizontálisan Skálázáson 2)|Darabszám|Összes||Nincs dimenzió|
|usedmemory2|Használt memória (horizontálisan Skálázáson 2)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss2|Használt memória RSS (horizontálisan Skálázáson 2)|Bájt|Maximum||Nincs dimenzió|
|serverLoad2|Kiszolgáló terhelése (horizontálisan Skálázáson 2)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite2|Gyorsítótár-írás (horizontálisan Skálázáson 2)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead2|Gyorsítótár-olvasás (horizontálisan Skálázáson 2)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime2|Processzor (horizontálisan Skálázáson 2)|Százalék|Maximum||Nincs dimenzió|
|connectedclients3|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 3)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed3|Műveletek összesen (horizontálisan Skálázáson 3)|Darabszám|Összes||Nincs dimenzió|
|cachehits3|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 3)|Darabszám|Összes||Nincs dimenzió|
|cachemisses3|Gyorsítótárbeli (horizontálisan Skálázáson 3)|Darabszám|Összes||Nincs dimenzió|
|getcommands3|(3 horizontálisan Skálázáson) beolvasása|Darabszám|Összes||Nincs dimenzió|
|setcommands3|Csoportok (horizontálisan Skálázáson 3)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond3|Műveletek száma másodpercenként (horizontálisan Skálázáson 3)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys3|Kizárt kulcsok (horizontálisan Skálázáson 3)|Darabszám|Összes||Nincs dimenzió|
|totalkeys3|Kulcsok összesen (horizontálisan Skálázáson 3)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys3|Lejárt kulcsok (horizontálisan Skálázáson 3)|Darabszám|Összes||Nincs dimenzió|
|usedmemory3|Használt memória (horizontálisan Skálázáson 3)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss3|Használt memória RSS (horizontálisan Skálázáson 3)|Bájt|Maximum||Nincs dimenzió|
|serverLoad3|Kiszolgáló terhelése (horizontálisan Skálázáson 3)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite3|Gyorsítótár-írás (horizontálisan Skálázáson 3)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead3|Gyorsítótár-olvasás (horizontálisan Skálázáson 3)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime3|Processzor (horizontálisan Skálázáson 3)|Százalék|Maximum||Nincs dimenzió|
|connectedclients4|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 4)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed4|Műveletek összesen (horizontálisan Skálázáson 4)|Darabszám|Összes||Nincs dimenzió|
|cachehits4|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 4)|Darabszám|Összes||Nincs dimenzió|
|cachemisses4|Gyorsítótárbeli (horizontálisan Skálázáson 4)|Darabszám|Összes||Nincs dimenzió|
|getcommands4|(4 horizontálisan Skálázáson) beolvasása|Darabszám|Összes||Nincs dimenzió|
|setcommands4|Csoportok (horizontálisan Skálázáson 4)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond4|Műveletek száma másodpercenként (horizontálisan Skálázáson 4)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys4|Kizárt kulcsok (horizontálisan Skálázáson 4)|Darabszám|Összes||Nincs dimenzió|
|totalkeys4|Kulcsok összesen (horizontálisan Skálázáson 4)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys4|Lejárt kulcsok (horizontálisan Skálázáson 4)|Darabszám|Összes||Nincs dimenzió|
|usedmemory4|Használt memória (horizontálisan Skálázáson 4)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss4|Használt memória RSS (horizontálisan Skálázáson 4)|Bájt|Maximum||Nincs dimenzió|
|serverLoad4|Kiszolgáló terhelése (horizontálisan Skálázáson 4)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite4|Gyorsítótár-írás (horizontálisan Skálázáson 4)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead4|Gyorsítótár-olvasás (horizontálisan Skálázáson 4)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime4|Processzor (horizontálisan Skálázáson 4)|Százalék|Maximum||Nincs dimenzió|
|connectedclients5|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 5)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed5|Műveletek összesen (horizontálisan Skálázáson 5)|Darabszám|Összes||Nincs dimenzió|
|cachehits5|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 5)|Darabszám|Összes||Nincs dimenzió|
|cachemisses5|Gyorsítótárbeli (horizontálisan Skálázáson 5)|Darabszám|Összes||Nincs dimenzió|
|getcommands5|Lekérdezi a (horizontálisan Skálázáson 5)|Darabszám|Összes||Nincs dimenzió|
|setcommands5|Csoportok (horizontálisan Skálázáson 5)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond5|Műveletek száma másodpercenként (horizontálisan Skálázáson 5)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys5|Kizárt kulcsok (horizontálisan Skálázáson 5)|Darabszám|Összes||Nincs dimenzió|
|totalkeys5|Kulcsok összesen (horizontálisan Skálázáson 5)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys5|Lejárt kulcsok (horizontálisan Skálázáson 5)|Darabszám|Összes||Nincs dimenzió|
|usedmemory5|Használt memória (horizontálisan Skálázáson 5)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss5|Használt memória RSS (horizontálisan Skálázáson 5)|Bájt|Maximum||Nincs dimenzió|
|serverLoad5|Kiszolgáló terhelése (horizontálisan Skálázáson 5)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite5|Gyorsítótár-írás (horizontálisan Skálázáson 5)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead5|Gyorsítótár-olvasás (horizontálisan Skálázáson 5)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime5|Processzor (horizontálisan Skálázáson 5)|Százalék|Maximum||Nincs dimenzió|
|connectedclients6|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 6)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed6|Műveletek összesen (horizontálisan Skálázáson 6)|Darabszám|Összes||Nincs dimenzió|
|cachehits6|Cache Hits (Shard 6)|Darabszám|Összes||Nincs dimenzió|
|cachemisses6|Gyorsítótárbeli (horizontálisan Skálázáson 6)|Darabszám|Összes||Nincs dimenzió|
|getcommands6|Lekérdezi a (horizontálisan Skálázáson 6)|Darabszám|Összes||Nincs dimenzió|
|setcommands6|Csoportok (horizontálisan Skálázáson 6)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond6|Műveletek száma másodpercenként (horizontálisan Skálázáson 6)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys6|Kizárt kulcsok (horizontálisan Skálázáson 6)|Darabszám|Összes||Nincs dimenzió|
|totalkeys6|Kulcsok összesen (horizontálisan Skálázáson 6)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys6|Lejárt kulcsok (horizontálisan Skálázáson 6)|Darabszám|Összes||Nincs dimenzió|
|usedmemory6|Használt memória (horizontálisan Skálázáson 6)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss6|Használt memória RSS (horizontálisan Skálázáson 6)|Bájt|Maximum||Nincs dimenzió|
|serverLoad6|Kiszolgáló terhelése (horizontálisan Skálázáson 6)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite6|Gyorsítótár-írás (horizontálisan Skálázáson 6)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead6|Gyorsítótár-olvasás (horizontálisan Skálázáson 6)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime6|Processzor (horizontálisan Skálázáson 6)|Százalék|Maximum||Nincs dimenzió|
|connectedclients7|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 7)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed7|Műveletek összesen (horizontálisan Skálázáson 7)|Darabszám|Összes||Nincs dimenzió|
|cachehits7|Találatot eredményező gyorsítótárbeli kereséseinek (horizontálisan Skálázáson 7)|Darabszám|Összes||Nincs dimenzió|
|cachemisses7|Gyorsítótárbeli (horizontálisan Skálázáson 7)|Darabszám|Összes||Nincs dimenzió|
|getcommands7|Lekérdezi a (horizontálisan Skálázáson 7)|Darabszám|Összes||Nincs dimenzió|
|setcommands7|Csoportok (horizontálisan Skálázáson 7)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond7|Műveletek száma másodpercenként (horizontálisan Skálázáson 7)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys7|Kizárt kulcsok (horizontálisan Skálázáson 7)|Darabszám|Összes||Nincs dimenzió|
|totalkeys7|Kulcsok összesen (horizontálisan Skálázáson 7)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys7|Lejárt kulcsok (horizontálisan Skálázáson 7)|Darabszám|Összes||Nincs dimenzió|
|usedmemory7|Használt memória (horizontálisan Skálázáson 7)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss7|Használt memória RSS (horizontálisan Skálázáson 7)|Bájt|Maximum||Nincs dimenzió|
|serverLoad7|Kiszolgáló terhelése (horizontálisan Skálázáson 7)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite7|Gyorsítótár-írás (horizontálisan Skálázáson 7)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead7|Gyorsítótár-olvasás (horizontálisan Skálázáson 7)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime7|Processzor (horizontálisan Skálázáson 7)|Százalék|Maximum||Nincs dimenzió|
|connectedclients8|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 8)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed8|Műveletek összesen (horizontálisan Skálázáson 8)|Darabszám|Összes||Nincs dimenzió|
|cachehits8|Cache Hits (Shard 8)|Darabszám|Összes||Nincs dimenzió|
|cachemisses8|Gyorsítótárbeli (horizontálisan Skálázáson 8)|Darabszám|Összes||Nincs dimenzió|
|getcommands8|Lekérdezi a (horizontálisan Skálázáson 8)|Darabszám|Összes||Nincs dimenzió|
|setcommands8|Csoportok (horizontálisan Skálázáson 8)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond8|Műveletek száma másodpercenként (horizontálisan Skálázáson 8)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys8|Kizárt kulcsok (horizontálisan Skálázáson 8)|Darabszám|Összes||Nincs dimenzió|
|totalkeys8|Kulcsok összesen (horizontálisan Skálázáson 8)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys8|Lejárt kulcsok (horizontálisan Skálázáson 8)|Darabszám|Összes||Nincs dimenzió|
|usedmemory8|Használt memória (horizontálisan Skálázáson 8)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss8|Használt memória RSS (horizontálisan Skálázáson 8)|Bájt|Maximum||Nincs dimenzió|
|serverLoad8|Kiszolgáló terhelése (horizontálisan Skálázáson 8)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite8|Gyorsítótár-írás (horizontálisan Skálázáson 8)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead8|Gyorsítótár-olvasás (horizontálisan Skálázáson 8)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime8|Processzor (horizontálisan Skálázáson 8)|Százalék|Maximum||Nincs dimenzió|
|connectedclients9|Csatlakoztatott ügyfelek (horizontálisan Skálázáson 9)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed9|Műveletek összesen (horizontálisan Skálázáson 9)|Darabszám|Összes||Nincs dimenzió|
|cachehits9|Cache Hits (Shard 9)|Darabszám|Összes||Nincs dimenzió|
|cachemisses9|Gyorsítótárbeli (horizontálisan Skálázáson 9)|Darabszám|Összes||Nincs dimenzió|
|getcommands9|Lekérdezi a (horizontálisan Skálázáson 9)|Darabszám|Összes||Nincs dimenzió|
|setcommands9|Csoportok (horizontálisan Skálázáson 9)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond9|Műveletek száma másodpercenként (horizontálisan Skálázáson 9)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys9|Kizárt kulcsok (horizontálisan Skálázáson 9)|Darabszám|Összes||Nincs dimenzió|
|totalkeys9|Kulcsok összesen (horizontálisan Skálázáson 9)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys9|Lejárt kulcsok (horizontálisan Skálázáson 9)|Darabszám|Összes||Nincs dimenzió|
|usedmemory9|Használt memória (horizontálisan Skálázáson 9)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss9|Használt memória RSS (horizontálisan Skálázáson 9)|Bájt|Maximum||Nincs dimenzió|
|serverLoad9|Kiszolgáló terhelése (horizontálisan Skálázáson 9)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite9|Gyorsítótár-írás (horizontálisan Skálázáson 9)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead9|Gyorsítótár-olvasás (horizontálisan Skálázáson 9)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime9|Processzor (horizontálisan Skálázáson 9)|Százalék|Maximum||Nincs dimenzió|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren elküldött bájtok száma (kimenő forgalom).|Nincs dimenzió|
|Lemezolvasási sebesség (bájt/s)|Lemezolvasás|Bájt/s|Átlag|A monitorozási időszakban lemezről beolvasott bájtok átlagos száma.|Nincs dimenzió|
|Lemezírási sebesség (bájt/s)|Lemezírás|Bájt/s|Átlag|A monitorozási időszakban lemezre írt bájtok átlagos száma.|Nincs dimenzió|
|Lemezolvasási művelet/s|Lemezolvasási művelet/s|Egység/s|Átlag|Lemezolvasási I/O-műveletek.|Nincs dimenzió|
|Lemezre írási művelet/s|Lemezre írási művelet/s|Egység/s|Átlag|Lemezre írási I/O-műveletek.|Nincs dimenzió|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Szerepkörpéldány azonosítója|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Szerepkörpéldány azonosítója|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren elküldött bájtok száma (kimenő forgalom).|Szerepkörpéldány azonosítója|
|Lemezolvasási sebesség (bájt/s)|Lemezolvasás|Bájt/s|Átlag|A monitorozási időszakban lemezről beolvasott bájtok átlagos száma.|Szerepkörpéldány azonosítója|
|Lemezírási sebesség (bájt/s)|Lemezírás|Bájt/s|Átlag|A monitorozási időszakban lemezre írt bájtok átlagos száma.|Szerepkörpéldány azonosítója|
|Lemezolvasási művelet/s|Lemezolvasási művelet/s|Egység/s|Átlag|Lemezolvasási I/O-műveletek.|Szerepkörpéldány azonosítója|
|Lemezre írási művelet/s|Lemezre írási művelet/s|Egység/s|Átlag|Lemezre írási I/O-műveletek.|Szerepkörpéldány azonosítója|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalCalls|Összes hívás|Darabszám|Összes|A hívások teljes száma.|Nincs dimenzió|
|SuccessfulCalls|Sikeres hívások|Darabszám|Összes|A sikeres hívások száma.|Nincs dimenzió|
|TotalErrors|Összes hiba|Darabszám|Összes|A hibaválaszt generáló hívások teljes száma (HTTP-válaszkód: 4xx vagy 5xx).|Nincs dimenzió|
|BlockedCalls|Blokkolt hívások|Darabszám|Összes|A sebesség- vagy kvótakorlátot átlépő hívások száma.|Nincs dimenzió|
|ServerErrors|Kiszolgálóhibák|Darabszám|Összes|A belső szolgáltatási hibába ütköző hívások száma (HTTP-válaszkód: 5xx).|Nincs dimenzió|
|ClientErrors|Ügyfélhibák|Darabszám|Összes|Az ügyféloldali hibába ütköző hívások száma (HTTP-válaszkód: 4xx).|Nincs dimenzió|
|DataIn|Bejövő adatforgalom|Bájt|Összes|A bejövő adatmennyiség bájtban.|Nincs dimenzió|
|DataOut|Kimenő adatforgalom|Bájt|Összes|A kimenő adatmennyiség bájtban.|Nincs dimenzió|
|Késés|Késés|Ideje ezredmásodpercben|Átlag|A késés másodpercben.|Nincs dimenzió|
|CharactersTranslated|Lefordított karakterek|Darabszám|Összes|A bejövő szöveges kérelem karakterszáma.|Nincs dimenzió|
|SpeechSessionDuration|Beszédfelismerési munkamenet időtartama|másodperc|Összes|A beszédfelismerési munkamenet teljes időtartama.|Nincs dimenzió|
|TotalTransactions|Tranzakciók száma|Darabszám|Összes|A tranzakciók száma.|Nincs dimenzió|
|TotalTokenCalls|Jogkivonathívások száma|Darabszám|Összes|A jogkivonathívások száma.|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren elküldött bájtok száma (kimenő forgalom)|Nincs dimenzió|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma összesen|Nincs dimenzió|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|A figyelési időszak során lemezre írt bájtok száma összesen|Nincs dimenzió|
|Lemezolvasási művelet/s|Lemezolvasási művelet/s|Egység/s|Átlag|Lemezolvasási I/O-műveletek|Nincs dimenzió|
|Lemezre írási művelet/s|Lemezre írási művelet/s|Egység/s|Átlag|Lemezre írási I/O-műveletek|Nincs dimenzió|
|Fennmaradó processzorkreditek|Fennmaradó processzorkreditek|Darabszám|Átlag|Adatlökethez rendelkezésre álló kreditek száma összesen|Nincs dimenzió|
|Felhasznált processzorkreditek|Felhasznált processzorkreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|
|Lemezenkénti olvasási sebesség (bájt/mp)|Adatlemez-olvasási sebesség (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Figyelési időszak során egyetlen lemezről beolvasott összes bájt/másodperc|Tárolóhely azonosítója|
|Lemezenkénti írási sebesség (bájt/mp)|Adatlemez-írási sebesség (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Összes bájt/mp a figyelési időszak során egyetlen lemezre írt|Tárolóhely azonosítója|
|Lemezenkénti olvasások száma (művelet/s)|Adatlemez-olvasások száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték a figyelési időszak során egyetlen lemezről olvasás során.|Tárolóhely azonosítója|
|Lemezenkénti írások száma (művelet/s)|Adatlemez-írások száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték a figyelési időszak során egyetlen lemezre írás során.|Tárolóhely azonosítója|
|Várólista lemezenkénti mélysége|Adatlemez QD (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy hossza)|Tárolóhely azonosítója|
|Lemezolvasás operációs rendszerenkénti sebessége (bájt/s)|Operációsrendszer-lemez olvasásának sebessége (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Figyelési operációsrendszer-lemez időszak során egyetlen lemezről beolvasott összes bájt/másodperc|Nincs dimenzió|
|Lemezírás operációs rendszerenkénti sebessége (bájt/s)|Operációsrendszer-lemez írásának sebessége (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Összes bájt/mp figyelési operációsrendszer-lemez időszak során egyetlen lemezre írt|Nincs dimenzió|
|Lemezolvasások operációs rendszerenkénti száma (művelet/s)|Operációsrendszer-lemez olvasásainak száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték olvasásakor egyetlen lemez a monitorozási időszakban operációsrendszer-lemez|Nincs dimenzió|
|Lemezírások operációs rendszerenkénti száma (művelet/s)|Operációsrendszer-lemez írásainak száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes IOPS írásakor egyetlen a monitorozási időszakban operációsrendszer-lemez|Nincs dimenzió|
|Lemez várólistájának operációs rendszerenkénti mélysége|Operációsrendszer-lemez várólistájának Mélysége (előzetes verzió)|Darabszám|Átlag|Az operációsrendszer-lemez várólistájának mélysége (vagy hossza)|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren elküldött bájtok száma (kimenő forgalom)|Nincs dimenzió|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma összesen|Nincs dimenzió|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|A figyelési időszak során lemezre írt bájtok száma összesen|Nincs dimenzió|
|Lemezolvasási művelet/s|Lemezolvasási művelet/s|Egység/s|Átlag|Lemezolvasási I/O-műveletek|Nincs dimenzió|
|Lemezre írási művelet/s|Lemezre írási művelet/s|Egység/s|Átlag|Lemezre írási I/O-műveletek|Nincs dimenzió|
|Fennmaradó processzorkreditek|Fennmaradó processzorkreditek|Darabszám|Átlag|Adatlökethez rendelkezésre álló kreditek száma összesen|Nincs dimenzió|
|Felhasznált processzorkreditek|Felhasznált processzorkreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|
|Lemezenkénti olvasási sebesség (bájt/mp)|Adatlemez-olvasási sebesség (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Figyelési időszak során egyetlen lemezről beolvasott összes bájt/másodperc|Tárolóhely azonosítója|
|Lemezenkénti írási sebesség (bájt/mp)|Adatlemez-írási sebesség (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Összes bájt/mp a figyelési időszak során egyetlen lemezre írt|Tárolóhely azonosítója|
|Lemezenkénti olvasások száma (művelet/s)|Adatlemez-olvasások száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték a figyelési időszak során egyetlen lemezről olvasás során.|Tárolóhely azonosítója|
|Lemezenkénti írások száma (művelet/s)|Adatlemez-írások száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték a figyelési időszak során egyetlen lemezre írás során.|Tárolóhely azonosítója|
|Várólista lemezenkénti mélysége|Adatlemez QD (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy hossza)|Tárolóhely azonosítója|
|Lemezolvasás operációs rendszerenkénti sebessége (bájt/s)|Operációsrendszer-lemez olvasásának sebessége bájt/mp|Egység/s|Átlag|Figyelési operációsrendszer-lemez időszak során egyetlen lemezről beolvasott összes bájt/másodperc|Nincs dimenzió|
|Lemezírás operációs rendszerenkénti sebessége (bájt/s)|Operációsrendszer-lemez írásának sebessége (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Összes bájt/mp figyelési operációsrendszer-lemez időszak során egyetlen lemezre írt|Nincs dimenzió|
|Lemezolvasások operációs rendszerenkénti száma (művelet/s)|Operációsrendszer-lemez olvasásainak száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték olvasásakor egyetlen lemez a monitorozási időszakban operációsrendszer-lemez|Nincs dimenzió|
|Lemezírások operációs rendszerenkénti száma (művelet/s)|Operációsrendszer-lemez írásainak száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes IOPS írásakor egyetlen a monitorozási időszakban operációsrendszer-lemez|Nincs dimenzió|
|Lemez várólistájának operációs rendszerenkénti mélysége|Operációsrendszer-lemez várólistájának Mélysége (előzetes verzió)|Darabszám|Átlag|Az operációsrendszer-lemez várólistájának mélysége (vagy hossza)|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren elküldött bájtok száma (kimenő forgalom)|Nincs dimenzió|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma összesen|Nincs dimenzió|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|A figyelési időszak során lemezre írt bájtok száma összesen|Nincs dimenzió|
|Lemezolvasási művelet/s|Lemezolvasási művelet/s|Egység/s|Átlag|Lemezolvasási I/O-műveletek|Nincs dimenzió|
|Lemezre írási művelet/s|Lemezre írási művelet/s|Egység/s|Átlag|Lemezre írási I/O-műveletek|Nincs dimenzió|
|Fennmaradó processzorkreditek|Fennmaradó processzorkreditek|Darabszám|Átlag|Adatlökethez rendelkezésre álló kreditek száma összesen|Nincs dimenzió|
|Felhasznált processzorkreditek|Felhasznált processzorkreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|
|Lemezenkénti olvasási sebesség (bájt/mp)|Adatlemez-olvasási sebesség (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Figyelési időszak során egyetlen lemezről beolvasott összes bájt/másodperc|Tárolóhely azonosítója|
|Lemezenkénti írási sebesség (bájt/mp)|Adatlemez-írási sebesség (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Összes bájt/mp a figyelési időszak során egyetlen lemezre írt|Tárolóhely azonosítója|
|Lemezenkénti olvasások száma (művelet/s)|Adatlemez-olvasások száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték a figyelési időszak során egyetlen lemezről olvasás során.|Tárolóhely azonosítója|
|Lemezenkénti írások száma (művelet/s)|Adatlemez-írások száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték a figyelési időszak során egyetlen lemezre írás során.|Tárolóhely azonosítója|
|Várólista lemezenkénti mélysége|Adatlemez QD (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy hossza)|Tárolóhely azonosítója|
|Lemezolvasás operációs rendszerenkénti sebessége (bájt/s)|Operációsrendszer-lemez olvasásának sebessége (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Figyelési operációsrendszer-lemez időszak során egyetlen lemezről beolvasott összes bájt/másodperc|Nincs dimenzió|
|Lemezírás operációs rendszerenkénti sebessége (bájt/s)|Operációsrendszer-lemez írásának sebessége (bájt/s) (Előzetes verzió)|Egység/s|Átlag|Összes bájt/mp figyelési operációsrendszer-lemez időszak során egyetlen lemezre írt|Nincs dimenzió|
|Lemezolvasások operációs rendszerenkénti száma (művelet/s)|Operációsrendszer-lemez olvasásainak száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes iops-érték olvasásakor egyetlen lemez a monitorozási időszakban operációsrendszer-lemez|Nincs dimenzió|
|Lemezírások operációs rendszerenkénti száma (művelet/s)|Operációsrendszer-lemez írásainak száma (művelet/s) (Előzetes verzió)|Egység/s|Átlag|Teljes IOPS írásakor egyetlen a monitorozási időszakban operációsrendszer-lemez|Nincs dimenzió|
|Lemez várólistájának operációs rendszerenkénti mélysége|Operációsrendszer-lemez várólistájának Mélysége (előzetes verzió)|Darabszám|Átlag|Az operációsrendszer-lemez várólistájának mélysége (vagy hossza)|Nincs dimenzió|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|CPU-használat|Darabszám|Átlag|Processzorhasználat az összes magon millicore-ban.|containerName|
|MemoryUsage|Memóriahasználat|Bájt|Átlag|A teljes memóriahasználat bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Bejövő hálózati forgalom (bájt/s)|Bájt|Átlag|A hálózatban másodpercenként fogadott bájtok száma.|Nincs dimenzió|
|NetworkBytesTransmittedPerSecond|Kimenő hálózati forgalom (bájt/s)|Bájt|Átlag|A hálózatban másodpercenként továbbított bájtok száma.|Nincs dimenzió|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Egy felügyelt fürt rendelkezésre álló Processzormagok száma összesen|Darabszám|Összes|Egy felügyelt fürt rendelkezésre álló Processzormagok száma összesen|Nincs dimenzió|
|kube_node_status_allocatable_memory_bytes|Egy felügyelt fürt rendelkezésre álló memória teljes mennyisége|Bájt|Összes|Egy felügyelt fürt rendelkezésre álló memória teljes mennyisége|Nincs dimenzió|
|kube_pod_status_ready|Kész állapotú podok száma|Darabszám|Összes|Kész állapotú podok száma|névtér, -pod|
|kube_node_status_condition|A különböző csomópont-feltételek állapota|Darabszám|Összes|A különböző csomópont-feltételek állapota|a feltétel, állapot, csomópont|
|kube_pod_status_phase|A fázis a podok számát|Darabszám|Összes|A fázis a podok számát|fázis, a névteret, a pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API-hívások|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationSuccessfulLines|Leképezés importálási művelet sikeres sorok|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationFailedLines|Sorok leképezése az importálási művelet sikertelen volt.|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationTotalLines|Leképezés importálási művelet teljes sorok|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationRuntimeInSeconds|Leképezés importálási művelet futásidejű másodpercek alatt|másodperc|Összes||Nincs dimenzió|
|DCIOutboundProfileExportSucceeded|Kimenő profil exportálása sikerült|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundProfileExportFailed|Kimenő profil exportálása nem sikerült|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundProfileExportDuration|Kimenő profil exportálása időtartama|másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundKpiExportFailed|Kimenő Kpi exportálása nem sikerült|Darabszám|Összes||Nincs dimenzió|
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
|ImportASAValuesFailed|Importálás ASA értékek száma nem sikerült|Darabszám|Összes||Nincs dimenzió|
|ImportASAValuesSucceeded|Importálás ASA értékek száma sikeres volt|Darabszám|Összes||Nincs dimenzió|
|DCIProfilesCount|Profil példányok száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIInteractionsPerMonthCount|Interakciók hónap száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIKpisCount|KPI száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCISegmentsCount|Szegmensek száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIPredictiveMatchPoliciesCount|Prediktív egyezés száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIPredictionsCount|Prediction Count|Darabszám|Vezetéknév||Nincs dimenzió|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Sikertelen futtatások|Darabszám|Összes||pipelineName, activityName, windowEnd, windowStart|
|SuccessfulRuns|Sikeres futtatások|Darabszám|Összes||pipelineName, activityName, windowEnd, windowStart|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Nem sikerült a folyamat futtatása metrikák|Darabszám|Összes||FailureType, neve|
|PipelineSucceededRuns|Sikeres futtatások metrikák folyamat|Darabszám|Összes||FailureType, neve|
|ActivityFailedRuns|Nem sikerült a tevékenységi futtatások metrikák|Darabszám|Összes||Tevékenységtípus, PipelineName, FailureType, neve|
|ActivitySucceededRuns|Sikeres futtatások metrikák tevékenység|Darabszám|Összes||Tevékenységtípus, PipelineName, FailureType, neve|
|TriggerFailedRuns|Nem sikerült az eseményindító-futtatások metrikák|Darabszám|Összes||Név, FailureType|
|TriggerSucceededRuns|Eseményindító-futtatások metrikák sikerült|Darabszám|Összes||Név, FailureType|
|IntegrationRuntimeCpuPercentage|Integrációs modul CPU-kihasználtság|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableMemory|Integrációs modul rendelkezésre álló memória|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Darabszám|Összes|Sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedFailure|Sikertelen feladatok|Darabszám|Összes|Sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedCancelled|Megszakított feladatok|Darabszám|Összes|Megszakított feladatok száma.|Nincs dimenzió|
|JobAUEndedSuccess|Sikeres AU-idő|másodperc|Összes|Sikeres feladatokra fordított AU időt.|Nincs dimenzió|
|JobAUEndedFailure|Sikertelen AU-idő|másodperc|Összes|Sikertelen feladatok teljes AU ideje.|Nincs dimenzió|
|JobAUEndedCancelled|Megszakított AU-idő|másodperc|Összes|Megszakított feladatok teljes AU ideje.|Nincs dimenzió|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatok teljes mennyisége.|Nincs dimenzió|
|DataWritten|Kiírt adatok|Bájt|Összes|Teljes adatmennyiséget ír a fiókba.|Nincs dimenzió|
|DataRead|Olvasott adatok|Bájt|Összes|Összes adat mennyisége a fiók olvasni.|Nincs dimenzió|
|WriteRequests|Írási kérelmek|Darabszám|Összes|Az adatok száma a fiók írási kérelmeket.|Nincs dimenzió|
|ReadRequests|Olvasási kérelmek|Darabszám|Összes|Az adatok száma olvasási kérelmek ahhoz a fiókhoz.|Nincs dimenzió|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Százalék|Átlag|Százalékos Processzorhasználat|Nincs dimenzió|
|memory_percent|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|Nincs dimenzió|
|io_consumption_percent|I/o-százalék|Százalék|Átlag|I/o-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Átlag|Tárolási kapacitása|Nincs dimenzió|
|serverlog_storage_percent|Server Log storage százalékban|Százalék|Átlag|Server Log storage százalékban|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Átlag|Kiszolgálói naplók tárolására használt|Nincs dimenzió|
|serverlog_storage_limit|Log storage maximális|Bájt|Átlag|Log storage maximális|Nincs dimenzió|
|active_connections|Az aktív kapcsolatok|Darabszám|Átlag|Az aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Összes|Kimenő hálózati forgalom között az aktív kapcsolatok|Nincs dimenzió|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Összes|Hálózati az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Százalék|Átlag|Százalékos Processzorhasználat|Nincs dimenzió|
|memory_percent|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|Nincs dimenzió|
|io_consumption_percent|I/o-százalék|Százalék|Átlag|I/o-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Átlag|Tárolási kapacitása|Nincs dimenzió|
|serverlog_storage_percent|Server Log storage százalékban|Százalék|Átlag|Server Log storage százalékban|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Átlag|Kiszolgálói naplók tárolására használt|Nincs dimenzió|
|serverlog_storage_limit|Log storage maximális|Bájt|Átlag|Log storage maximális|Nincs dimenzió|
|active_connections|Az aktív kapcsolatok|Darabszám|Átlag|Az aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|seconds_behind_master|Replikációs késés másodpercben|Darabszám|Átlag|Replikációs késés másodpercben|Nincs dimenzió|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Összes|Kimenő hálózati forgalom között az aktív kapcsolatok|Nincs dimenzió|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Összes|Hálózati az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Százalékos Processzorhasználat|Százalék|Átlag|Százalékos Processzorhasználat|Nincs dimenzió|
|memory_percent|Memória százalékos aránya|Százalék|Átlag|Memória százalékos aránya|Nincs dimenzió|
|io_consumption_percent|I/o-százalék|Százalék|Átlag|I/o-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Átlag|Tárolási kapacitása|Nincs dimenzió|
|serverlog_storage_percent|Server Log storage százalékban|Százalék|Átlag|Server Log storage százalékban|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgálói naplók tárolására használt|Bájt|Átlag|Kiszolgálói naplók tárolására használt|Nincs dimenzió|
|serverlog_storage_limit|Log storage maximális|Bájt|Átlag|Log storage maximális|Nincs dimenzió|
|active_connections|Az aktív kapcsolatok|Darabszám|Átlag|Az aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Összes|Kimenő hálózati forgalom között az aktív kapcsolatok|Nincs dimenzió|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Összes|Hálózati az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetriai üzenetet küld kísérletek|Darabszám|Összes|Küldését az IoT hub próbált eszköz – felhő telemetriát üzenetek száma|Nincs dimenzió|
|d2c.telemetry.ingress.success|Telemetriai üzeneteket küldi|Darabszám|Összes|Sikerült elküldeni az IoT hub eszköz – felhő telemetriát üzenetek száma|Nincs dimenzió|
|c2d.commands.egress.complete.success|A parancsok befejeződött|Darabszám|Összes|Az eszköz által végrehajtott művelet felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.abandon.success|Elhagyott parancsok|Darabszám|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.reject.success|Elutasított parancsok|Darabszám|Összes|Az eszköz által elutasított felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|devices.totalDevices|(Elavult az) összes eszköz|Darabszám|Összes|Az IoT hubban regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|(Elavult) csatlakoztatott eszközök |Darabszám|Összes|Az IoT hubhoz csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.success|Útválasztási: telemetriai üzenetet|Darabszám|Összes|Többször is sikeresen kézbesíti az üzeneteket az IoT Hub útválasztás használatával az összes végpontok száma. Ha egy üzenet van irányítva több végpontot, ez az érték minden egyes sikeres kézbesítés eggyel nő. Egy üzenet szállítják egyazon végpont több alkalommal, ha ez az érték minden egyes sikeres kézbesítés eggyel nő.|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Útválasztási: telemetriai üzeneteket eldobása |Darabszám|Összes|Hányszor üzenetek kézbesíthetetlen végpontok miatt útválasztási IoT Hub által el lettek dobva. Ez az érték nem számít üzenetet tartalék útvonal, az eldobott üzenetek jelenjenek meg nem létezik.|Nincs dimenzió|
|d2c.telemetry.egress.orphaned|Útválasztási: telemetriai üzeneteket árva |Darabszám|Összes|Hányszor üzenetek által az IoT Hub-útválasztás árva is, mert (beleértve a tartalék szabály) minden olyan útválasztási szabályok nem egyeznek. |Nincs dimenzió|
|d2c.telemetry.egress.invalid|Útválasztási: telemetria inkompatibilis üzenetek|Darabszám|Összes|Hányszor útválasztás az IoT Hub kézbesíti az üzeneteket egy alkalmazással a végponthoz való inkompatibilitás miatt nem sikerült. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Útválasztási: kézbesíti az üzeneteket tartalék|Darabszám|Összes|Hányszor útválasztás az IoT Hub-üzenetek a végponthoz társított a tartalék útvonal i.|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Útválasztási: kézbesíti az üzeneteket az Eseményközpont|Darabszám|Összes|Hányszor sikeresen útválasztás az IoT Hub Event Hub-végpontok üzenetek érkeznek.|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Útválasztási: Event Hub üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a bejövő üzenet be egy Eseményközpont-végpontra.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Útválasztási: üzenetek kézbesítése Service Bus-üzenetsorba helyezése|Darabszám|Összes|Hányszor sikeresen útválasztás az IoT Hub-üzenetek i a Service Bus üzenetsor-végpontokra irányuló.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|Útválasztási: Service Bus-üzenetsor üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy Service Bus-üzenetsor végpont.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Útválasztási: üzenetek kézbesítése Service Bus-témakörbe|Darabszám|Összes|Hányszor sikeresen útválasztás az IoT Hub-i üzenetek a Service Bus-témakör végpontokat.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|Útválasztási: Service Bus-témakör üzenet késése|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy Service Bus-témakör végpont.|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|Útválasztási: kézbesíti az üzeneteket üzenetek/események|Darabszám|Összes|Hányszor az IoT Hub sikeresen útválasztási üzenetek érkeznek a beépített végpont (üzenetek/események).|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Útválasztási: üzenet közel valós idejű üzenetek/események|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő forgalom, a beépített végpont (üzenetek/események).|Nincs dimenzió|
|d2c.endpoints.egress.storage|Útválasztási: kézbesíti az üzeneteket storage|Darabszám|Összes|Többször az IoT Hub sikeresen útválasztási üzenetek érkeznek tárolási végpontok száma.|Nincs dimenzió|
|d2c.endpoints.latency.storage|Útválasztási: üzenet késése storage|Ezredmásodperc|Átlag|Átlagos késése (MS) között az IoT hub üzenet bejövő és a telemetriai üzenetet bejövő be egy storage-végponthoz.|Nincs dimenzió|
|d2c.endpoints.egress.storage.bytes|Útválasztási: az adatokat kézbesíti storage|Bájt|Összes|Adatok (bájt) mennyisége az IoT Hub útválasztás kézbesíti a tárolási végpontok.|Nincs dimenzió|
|d2c.endpoints.egress.storage.blobs|Útválasztási: blobok kézbesíteni storage|Darabszám|Összes|The number of times IoT Hub routing delivered blobs to storage endpoints.|Nincs dimenzió|
|d2c.twin.read.success|A sikeres ikerírási eszközökről|Darabszám|Összes|Az összes sikeres eszköz által kezdeményezett ikerírási száma.|Nincs dimenzió|
|d2c.twin.read.failure|Nem sikerült az iker írási eszközökről|Darabszám|Összes|A teljes számát a eszköz által kezdeményezett ikerírási nem sikerült.|Nincs dimenzió|
|d2c.twin.read.size|Válasz mérete ikerírási eszközökről|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres, a páros olvasási.|Nincs dimenzió|
|d2c.twin.update.success|Eszközök sikeres ikereszköz-frissítések|Darabszám|Összes|Az összes sikeres eszköz által kezdeményezett ikereszköz-frissítések száma.|Nincs dimenzió|
|d2c.twin.update.failure|Nem sikerült az eszköz az ikereszköz-frissítések|Darabszám|Összes|A szám az összes eszköz által kezdeményezett ikereszköz-frissítések nem sikerült.|Nincs dimenzió|
|d2c.twin.update.size|Ikereszköz-frissítések eszközökről mérete|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres ikereszköz frissítéseket.|Nincs dimenzió|
|c2d.methods.success|A sikeres közvetlen metódus meghívásához|Darabszám|Összes|Közvetlen metódus az összes sikeres hívások száma.|Nincs dimenzió|
|c2d.methods.failure|Nem sikerült a közvetlen metódus meghívásához|Darabszám|Összes|A teljes számát nem sikerült a közvetlen metódust hívja.|Nincs dimenzió|
|c2d.methods.requestSize|Kérés mérete közvetlen metódus meghívásához|Bájt|Átlag|Az átlagos, minimális és közvetlen metódus az összes sikeres kérelmek maximális száma.|Nincs dimenzió|
|c2d.methods.responseSize|Közvetlen megpróbálkozni a válasz mérete|Bájt|Átlag|Az átlagos, minimális és a közvetlen metódus az összes sikeres válaszok maximális száma.|Nincs dimenzió|
|c2d.twin.read.success|A sikeres ikerírási háttérrendszerből|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-olvasások száma.|Nincs dimenzió|
|c2d.twin.read.failure|Nem sikerült a páros olvasási háttérrendszerből|Darabszám|Összes|A teljes számát a back-end által kezdeményezett ikerírási nem sikerült.|Nincs dimenzió|
|c2d.twin.read.size|Válasz mérete ikerírási háttérrendszerből|Bájt|Átlag|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett a páros olvasási.|Nincs dimenzió|
|c2d.twin.update.success|Háttér sikeres ikereszköz-frissítések|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett ikereszköz-frissítések száma.|Nincs dimenzió|
|c2d.twin.update.failure|Háttér sikertelen ikereszköz-frissítések|Darabszám|Összes|A teljes számát a back-end által kezdeményezett ikereszköz-frissítések nem sikerült.|Nincs dimenzió|
|c2d.twin.update.size|A háttérben ikereszköz-frissítések mérete|Bájt|Átlag|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett ikereszköz frissítéseket.|Nincs dimenzió|
|twinQueries.success|A sikeres ikereszköz-lekérdezések|Darabszám|Összes|Az összes sikeres ikereszköz-lekérdezések száma.|Nincs dimenzió|
|twinQueries.failure|Sikertelen ikereszköz-lekérdezések|Darabszám|Összes|Összes sikertelen ikereszköz-lekérdezések száma.|Nincs dimenzió|
|twinQueries.resultSize|Ikereszköz-lekérdezések eredmény mérete|Bájt|Átlag|Az átlagos, minimális és az összes sikeres ikereszköz-lekérdezés eredményének mérete legfeljebb.|Nincs dimenzió|
|jobs.createTwinUpdateJob.success|Ikereszköz-frissítési feladatok sikeres létrehozás|Darabszám|Összes|Az összes sikeres létrehozás ikereszköz-frissítési feladatok száma.|Nincs dimenzió|
|jobs.createTwinUpdateJob.failure|Sikertelen létrehozás ikereszköz-frissítési feladatok|Darabszám|Összes|Minden létrehozására tett sikertelen ikereszköz frissítési feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.success|Metódus meghívása feladatok sikeres létrehozás|Darabszám|Összes|Az összes sikeres létrehozás közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.failure|Metódus meghívása feladatok sikertelen létrehozás|Darabszám|Összes|Összes sikertelen létrehozása a közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.listJobs.success|Listázhatók a feladatok sikeres hívások|Darabszám|Összes|Az összes sikeres hívások listázhatók a feladatok száma.|Nincs dimenzió|
|jobs.listJobs.failure|Sikertelen hívások listázhatók a feladatok|Darabszám|Összes|Az összes sikertelen hívás listázhatók a feladatok száma.|Nincs dimenzió|
|jobs.cancelJob.success|Megszakított feladatok sikeres|Darabszám|Összes|Megszakítja a feladatot az összes sikeres hívások száma.|Nincs dimenzió|
|jobs.cancelJob.failure|Sikertelen feladat általi hóközi lemondás|Darabszám|Összes|Megszakítja a feladatot az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.queryJobs.success|Feladat sikeres lekérdezések|Darabszám|Összes|Lekérdezés projektekhez az összes sikeres hívások száma.|Nincs dimenzió|
|jobs.queryJobs.failure|Sikertelen feladat lekérdezések|Darabszám|Összes|Lekérdezés projektekhez az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.completed|Befejezett feladatok|Darabszám|Összes|Az összes befejezett feladatok száma.|Nincs dimenzió|
|jobs.Failed|Sikertelen feladatok|Darabszám|Összes|Összes sikertelen feladatok száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Szabályozási hibák száma|Darabszám|Összes|Eszköz átviteli miatt szabályozási hibák számának szabályozza|Nincs dimenzió|
|dailyMessageQuotaUsed|Használt üzenetek teljes száma|Darabszám|Átlag|Ma használt teljes üzenetek száma. Ez az összesített érték, amely lenullázódik: 00:00 (UTC) minden nap.|Nincs dimenzió|
|deviceDataUsage|Teljes devicedata használat (elavult)|Bájt|Összes|És bármilyen eszközről csatlakozik az IotHub átvitt bájtok száma|Nincs dimenzió|
|deviceDataUsageV2|Eszköz teljes adathasználat (előzetes verzió)|Bájt|Összes|És bármilyen eszközről csatlakozik az IotHub átvitt bájtok száma|Nincs dimenzió|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Darabszám|Átlag|Az IoT hubban regisztrált eszközök száma|Nincs dimenzió|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hubhoz csatlakoztatott eszközök száma|Nincs dimenzió|
|Konfigurációk|Konfiguráció-metrikák|Darabszám|Összes|A konfigurálási műveletek metrikái|Nincs dimenzió|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Darabszám|Összes|Kísérlet történt eszközregisztrációk száma|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Hozzárendelt eszközök|Darabszám|Összes|Egy IoT hubra hozzárendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Igazolási kísérletek|Darabszám|Összes|Próbált eszköz tanúsítványok száma|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

### <a name="request-metrics"></a>Kérelem-metrikák

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók| Idő granularitással| Örökölt metrika leképezés | Használat |
|---|---|---|---|---|---| ---| ---| ---|
| TotalRequests |   Összes kérelem| Darabszám   | Darabszám | Kérelmek száma|  DatabaseName, CollectionName, Region, StatusCode|   Összes |   TotalRequests, Http 2xx, 3xx Http, a Http 400-as, a Http 401-es, belső kiszolgálóhiba szolgáltatás nem érhető el, a kérelmek szabályozva, másodpercenként átlagosan |    Kérések száma állapot kódja, gyűjtemény jelenleg egy perces pontossággal figyelésére használható. A kérelmek másodpercenkénti átlagos száma az összesítés használatát perc, és nullával való osztás 60. |
| MetadataRequests |    A metaadat-kérelmek   |Darabszám| Darabszám   | A metaadat-kérelmek száma. Az Azure Cosmos DB tárolja a rendszer metaadat-gyűjtemény minden fiókhoz, amely lehetővé teszi, hogy számba venni a gyűjteményeket, adatbázisok és egyéb, és a konfigurációját, ingyenesen.    | DatabaseName, CollectionName, Region, StatusCode| Összes|  |A metaadat-kérelmek miatt szabályozások figyelésére használható.|
| MongoRequests |   Mongo-kérelmek| Darabszám | Darabszám|  Mongo-kérelmek száma   | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Összes |Mongo-lekérdezési kérelmek arányának, a Mongo-frissítési kérés arány, a Mongo kérelmek arányának törléséhez Mongo beszúrása a kérések aránya, Mongo száma kérelmek gyakorisága|   Mongo-kérelem hibák nyomon követésére, a tanúsítványalgoritmusok parancsonként írja be. |


### <a name="request-unit-metrics"></a>Kérelem egységek metrikáit

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók| Idő granularitással| Örökölt metrika leképezés | Használat |
|---|---|---|---|---|---| ---| ---| ---|
| MongoRequestCharge|   Mongo-kérelem díja |  Darabszám   |Összes  |Felhasznált Kérelemegységek mongo|  DatabaseName, CollectionName, Region, CommandName, ErrorCode|   Összes |Mongo-lekérdezés kérelem díjmentes, a Mongo frissítési kérelem díjmentes, a Mongo törlése kérelem díja, Mongo beszúrása kérelem számolunk fel, Mongo száma kérelem díja| Egy perc alatt Mongo erőforrás RUs figyelésére használható.|
| TotalRequestUnits |Teljes kérelemegység|   Darabszám|  Összes|  Felhasznált egységek kérése| DatabaseName, CollectionName, Region, StatusCode    |Összes|   TotalRequestUnits|  Jelenleg egy perces pontossággal teljes RU-használat figyelésére használható. Átlagos RU másodpercenként felhasznált perc teljes összesítést használ, és nullával való osztás 60.|
| ProvisionedThroughput |Kiosztott átviteli kapacitás|    Darabszám|  Maximum |Kiosztott átviteli sebességet gyűjtemény részletessége|  DatabaseName, CollectionName|   5M| |   Kiosztott átviteli sebesség gyűjteményenként figyelésére használható.|

### <a name="storage-metrics"></a>Storage-mérőszámok

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók| Idő granularitással| Örökölt metrika leképezés | Használat |
|---|---|---|---|---|---| ---| ---| ---|
| AvailableStorage| Rendelkezésre álló tár   |Bájt| Összes|  5 perces részletesség régiónként, jelentett összes elérhető tárhely|   DatabaseName, CollectionName, régió|   5M| Rendelkezésre álló tár|   Figyelhető a rendelkezésre álló tár kapacitása (csak a rögzített tároló gyűjtemények alkalmazható) minimális részletessége 5 percnek kell lennie.| 
| DataUsage |Adatforgalom |Bájt| Összes   |5 perces részletesség régiónként, jelentett használati adatok összesen|    DatabaseName, CollectionName, régió|   5M  |Adatok mérete  | Gyűjtemény és a régió teljes adathasználat figyelésére használható, minimális részletessége 5 percnek kell lennie.|
| IndexUsage|   Index használat|    Bájt|  Összes   |5 perces részletesség régiónként, jelentett teljes Index használata|    DatabaseName, CollectionName, régió|   5M| Index mérete| Gyűjtemény és a régió teljes adathasználat figyelésére használható, minimális részletessége 5 percnek kell lennie. |
| DocumentQuota|    A dokumentum kvóta| Bájt|  Összes|  Teljes tárolási kvótája régiónként 5 perces részletesség jelenteni. A f vonatkozik| DatabaseName, CollectionName, régió|   5M  |Tárkapacitás|  Gyűjtemény és a régió teljes kvóta figyelésére használható, minimális részletessége 5 percnek kell lennie.|
| DocumentCount|    Dokumentumok száma| Darabszám   |Összes  |5 perces részletesség régiónként, jelentett Totaldocument száma|  DatabaseName, CollectionName, régió|   5M  |Dokumentumok száma|Dokumentumok száma, gyűjtemény és a régió figyelésére használható, minimális részletessége 5 percnek kell lennie.|

### <a name="latency-metrics"></a>Késés metrikák

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók| Idő granularitással| Használat |
|---|---|---|---|---|---| ---| ---| ---|
| ReplicationLatency    | Replikáció késése|  Ideje ezredmásodpercben|   Minimum,Maximum,Average | Fiók geo-kompatibilis a forrás- és régiók közötti P99 replikáció késése| SourceRegion, TargetRegion| Összes | Egy georeplikált fiók bármely két régió között P99 replikációs késésétől figyelésére használható. |

### <a name="availability-metrics"></a>Rendelkezésre állási metrikák

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók| Idő granularitással| Örökölt metrika leképezés | Használat |
|---|---|---|---|---|---| ---| ---| ---|
| ServiceAvailability   | Szolgáltatás rendelkezésre állása| Százalék |Minimális, maximális|   Egy órás részletességgel fiók kérelmek rendelkezésre állásáról|  |   1H  | Szolgáltatás rendelkezésre állása  | Ez az összes sikeres kérelme százalékát. Rendszerhiba miatt kell végrehajtani, ha az állapotkód értéke 410, tekinthető a kérelem 500 vagy 503-as használható a fiókját órás részletességgel rendelkezésre állásának figyeléséhez. |

### <a name="cassandra-api-metrics"></a>Cassandra API-metrikák

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók| Idő granularitással| Használat |
|---|---|---|---|---|---| ---| ---| ---|
| CassandraRequests | Cassandra Requests |  Darabszám|  Darabszám|  Cassandra API-kérelmek száma|  DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType|   Összes| Jelenleg egy perces pontossággal Cassandra kérelmek figyelésére használható. A kérelmek másodpercenkénti átlagos száma az összesítés használatát perc, és nullával való osztás 60.|
| CassandraRequestCharges|  Cassandra-kérelem díjak| Darabszám|   Sum, Min, Max, Avg| Cassandra API-kérések által felhasznált kérelemegységek|   DatabaseName, CollectionName, Region, OperationType, ResourceType|  Összes| Kérelemegység / perc a Cassandra API-fiók által használt figyelésére használható.|
| CassandraConnectionClosures   | Cassandra-kapcsolat szünnapok |Darabszám| Darabszám   |Lezárt Cassandra-kapcsolatok száma|    ClosureReason, régió|  Összes | Az ügyfélszámítógépek és az Azure Cosmos DB Cassandra API közötti kapcsolatok figyelésére használható.|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összes|Ez a témakör közzétett összes esemény|Nincs dimenzió|
|PublishFailCount|Sikertelen események|Darabszám|Összes|Nem sikerült közzétenni a témakör összes esemény|ErrorType, Error|
|UnmatchedEventCount|Nem egyező események|Darabszám|Összes|Összes esemény nem egyeznek az esemény-előfizetések ebben a témakörben|Nincs dimenzió|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Egyeztetett események|Darabszám|Összes|Az Eseményelőfizetés egyezteti összes esemény|Nincs dimenzió|
|DeliveryAttemptFailCount|Nem sikerült kézbesíteni események|Darabszám|Összes|Nem sikerült továbbítani az esemény-előfizetés az összes esemény|Hiba történt, ErrorType|
|DeliverySuccessCount|A szállított események|Darabszám|Összes|Az Eseményelőfizetés szállított összes esemény|Nincs dimenzió|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartam|Ezredmásodperc|Átlag|Cél feldolgozási idő ezredmásodpercben|Nincs dimenzió|
|DroppedEventCount|Az eldobott események|Darabszám|Összes|Az Eseményelőfizetés a megfelelő teljes eldobott események|Nincs dimenzió|
|DeadLetteredCount|Feldolgozatlan események|Darabszám|Összes|Az Eseményelőfizetés a megfelelő teljes feldolgozatlan események|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összes|Ez a témakör közzétett összes esemény|Nincs dimenzió|
|PublishFailCount|Sikertelen események|Darabszám|Összes|Nem sikerült közzétenni a témakör összes esemény|ErrorType, Error|
|UnmatchedEventCount|Nem egyező események|Darabszám|Összes|Összes esemény nem egyeznek az esemény-előfizetések ebben a témakörben|Nincs dimenzió|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előnézet)|Darabszám|Összes|A Microsoft.EventHub sikeres kérelmeinek száma. (Előzetes verzió)|EntityName|
|ServerErrors|Kiszolgálóhibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kiszolgálóhibáinak száma. (Előzetes verzió)|EntityName|
|UserErrors|Felhasználói hibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub felhasználói hibáinak száma. (Előzetes verzió)|EntityName|
|QuotaExceededErrors|Kvótatúllépési hibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kvótatúllépési hibáinak száma. (Előzetes verzió)|EntityName|
|ThrottledRequests|Szabályozott kérelmek száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub szabályozott kérelmeinek száma. (Előzetes verzió)|EntityName|
|IncomingRequests|A bejövő kérések (előnézet)|Darabszám|Összes|A Microsoft.EventHub bejövő kérelmeinek száma. (Előzetes verzió)|EntityName|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub bejövő üzeneteinek száma. (Előzetes verzió)|EntityName|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kimenő üzeneteinek száma. (Előzetes verzió)|EntityName|
|IncomingBytes|Bejövő bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub bejövő bájtjainak száma. (Előzetes verzió)|EntityName|
|OutgoingBytes|Kimenő bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub kimenő bájtjainak száma. (Előzetes verzió)|EntityName|
|AktívKapcsolatok|Aktív kapcsolatai (előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub aktív kapcsolatainak száma összesen. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatok száma. (Előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub megnyitott kapcsolatainak száma. (Előzetes verzió)|EntityName|
|ConnectionsClosed|Lezárt kapcsolatok száma. (Előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub lezárt kapcsolatainak száma. (Előzetes verzió)|EntityName|
|CaptureBacklog|Hátralék rögzítése. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub hátralékának rögzítése. (Előzetes verzió)|EntityName|
|CapturedMessages|Rögzített üzenetek száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub rögzített üzeneteinek száma. (Előzetes verzió)|EntityName|
|CapturedBytes|Rögzített bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub rögzített bájtjainak száma. (Előzetes verzió)|EntityName|
|Méret|Méret (előzetes verzió)|Bájt|Átlag|Az eseményközpont mérete (bájt). (Előzetes verzió)|EntityName|
|INREQS|Bejövő kérések|Darabszám|Összes|Egy névtér bejövő küldési kérelmei összesen|Nincs dimenzió|
|SUCCREQ|Sikeres kérések|Darabszám|Összes|A névtér összes sikeres kérelme|Nincs dimenzió|
|FAILREQ|Sikertelen kérelmek|Darabszám|Összes|A névtérhez kapcsolódó összes sikertelen kérelem|Nincs dimenzió|
|SVRBSY|Kiszolgáló foglaltsága miatti hibák|Darabszám|Összes|A névtér összes, kiszolgáló foglaltsága miatti hibája|Nincs dimenzió|
|INTERR|Belső kiszolgálóhibák|Darabszám|Összes|A névtér összes belső kiszolgálóhibája|Nincs dimenzió|
|MISCERR|Egyéb hibák|Darabszám|Összes|A névtérhez kapcsolódó összes sikertelen kérelem|Nincs dimenzió|
|INMSGS|Bejövő üzenetek (elavult)|Darabszám|Összes|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Használja inkább a bejövő üzenetek metrikát|Nincs dimenzió|
|EHINMSGS|Bejövő üzenetek|Darabszám|Összes|A névtér összes bejövő üzenete|Nincs dimenzió|
|OUTMSGS|Kimenő üzenetek (elavult)|Darabszám|Összes|Kimenő üzenetek névtér száma. Ez a metrika elavult. Használja inkább a kimenő üzenetek metrikát|Nincs dimenzió|
|EHOUTMSGS|Kimenő üzenetek|Darabszám|Összes|A névtér összes kimenő üzenete|Nincs dimenzió|
|EHINMBS|Bejövő bájtok (elavult)|Bájt|Összes|Event Hub bejövő üzeneteinek átviteli sebessége a névtér. Ez a metrika elavult. Használja inkább a bejövő bájtok metrikát|Nincs dimenzió|
|EHINBYTES|Bejövő bájtok száma|Bájt|Összes|Az eseményközpont adott névtérhez kapcsolódó bejövő üzeneteinek átviteli sebessége|Nincs dimenzió|
|EHOUTMBS|Kimenő bájtok (elavult)|Bájt|Összes|Event Hub kimenő üzeneteinek átviteli sebessége a névtér. Ez a metrika elavult. Használja inkább a kimenő bájtok metrikát|Nincs dimenzió|
|EHOUTBYTES|Kimenő bájtok száma|Bájt|Összes|Az eseményközpont adott névtérhez kapcsolódó kimenő üzeneteinek átviteli sebessége|Nincs dimenzió|
|EHABL|Archivált várakozó üzenetek|Darabszám|Összes|Az eseményközpont adott névtérhez kapcsolódó archivált várakozó üzenetei|Nincs dimenzió|
|EHAMSGS|Üzenetek archiválása|Darabszám|Összes|Az eseményközpont adott névtéren belüli archivált üzenetei|Nincs dimenzió|
|EHAMBS|Archivált üzenetek átviteli sebessége|Bájt|Összes|Az eseményközpont adott névtéren belüli archivált üzeneteinek átviteli sebessége|Nincs dimenzió|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előnézet)|Darabszám|Összes|A Microsoft.EventHub sikeres kérelmeinek száma. (Előzetes verzió)|Nincs dimenzió|
|ServerErrors|Kiszolgálóhibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kiszolgálóhibáinak száma. (Előzetes verzió)|Nincs dimenzió|
|UserErrors|Felhasználói hibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub felhasználói hibáinak száma. (Előzetes verzió)|Nincs dimenzió|
|QuotaExceededErrors|Kvótatúllépési hibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kvótatúllépési hibáinak száma. (Előzetes verzió)|Nincs dimenzió|
|ThrottledRequests|Szabályozott kérelmek száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub szabályozott kérelmeinek száma. (Előzetes verzió)|Nincs dimenzió|
|IncomingRequests|A bejövő kérések (előnézet)|Darabszám|Összes|A Microsoft.EventHub bejövő kérelmeinek száma. (Előzetes verzió)|Nincs dimenzió|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub bejövő üzeneteinek száma. (Előzetes verzió)|Nincs dimenzió|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kimenő üzeneteinek száma. (Előzetes verzió)|Nincs dimenzió|
|IncomingBytes|Bejövő bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub bejövő bájtjainak száma. (Előzetes verzió)|Nincs dimenzió|
|OutgoingBytes|Kimenő bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub kimenő bájtjainak száma. (Előzetes verzió)|Nincs dimenzió|
|AktívKapcsolatok|Aktív kapcsolatai (előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub aktív kapcsolatainak száma összesen. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatok száma. (Előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub megnyitott kapcsolatainak száma. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsClosed|Lezárt kapcsolatok száma. (Előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub lezárt kapcsolatainak száma. (Előzetes verzió)|Nincs dimenzió|
|CaptureBacklog|Hátralék rögzítése. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub hátralékának rögzítése. (Előzetes verzió)|Nincs dimenzió|
|CapturedMessages|Rögzített üzenetek száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub rögzített üzeneteinek száma. (Előzetes verzió)|Nincs dimenzió|
|CapturedBytes|Rögzített bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub rögzített bájtjainak száma. (Előzetes verzió)|Nincs dimenzió|
|CPU|Processzor (előzetes verzió)|Százalék|Maximum|Az Event Hub-fürt CPU-kihasználtsága (%)|Szerepkör|
|AvailableMemory|Rendelkezésre álló memória (előzetes verzió)|Darabszám|Maximum|Az Event Hub-fürt számára rendelkezésre álló memória (bájt)|Szerepkör|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjárókérések|Darabszám|Összes|Átjáró-kérelmek száma|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategorizált Átjárókérések|Darabszám|Összes|Átjáró-kérelmek (1xx/2xx vagy 3xx/4xx vagy 5xx) kategóriák szerint|ClusterDnsName, HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrikaérték|Darabszám|Átlag|Az automatikus skálázás által a végrehajtásakor kiszámított érték|MetricTriggerSource|
|MetricThreshold|Metrika küszöbértéke|Darabszám|Átlag|Az automatikus skálázás futtatásakor konfigurált automatikus skálázási küszöbérték.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Darabszám|Átlag|Az automatikus skálázás számára annak végrehajtásakor jelentett kapacitás.|Nincs dimenzió|
|ScaleActionsInitiated|Elindított skálázási műveletek|Darabszám|Összes|A skálázási művelet iránya.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components
(Nyilvános előzetes verzió)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/duration|Tesztek időtartama|Ideje ezredmásodpercben|Átlag|Tesztek időtartama|availabilityResult/name, availabilityResult /-helyet, availabilityResult/sikeres|
|billingMeters/telemetryCount|Adatpontok száma|Darabszám|Összes|Az Application Insights-erőforrásnak elküldött adatpontok száma. Ezen metrika feldolgozása akár két órás késéssel történik.|billing/telemetryItemType, billing/telemetryItemSource|
|billingMeters/telemetrySize|Adatpontok mennyisége|Bájt|Összes|Az Application Insights-erőforrás felé küldött adatok mennyisége. Ezen metrika feldolgozása akár két órás késéssel történik.|billing/telemetryItemType, billing/telemetryItemSource|
|browserTimings/networkDuration|Hálózati kapcsolat ideje lapbetöltéskor|Ideje ezredmásodpercben|Átlag|A felhasználói kérelem és a hálózati kapcsolat között eltelt idő. Magában foglalja a DNS-keresési és -átviteli kapcsolat.|Nincs dimenzió|
|browserTimings/processingDuration|Ügyfél feldolgozási ideje|Ideje ezredmásodpercben|Átlag|Az utolsó bájtig eltelt egy dokumentum fogadása, mindaddig, amíg a DOM betöltése között eltelt idő. Aszinkron kérelmek feldolgozása még folyamatban lehet is.|Nincs dimenzió|
|browserTimings/receiveDuration|Válasz fogadási ideje|Ideje ezredmásodpercben|Átlag|Az első és az utolsó bájt között, vagy a kapcsolat bontásáig eltelt idő.|Nincs dimenzió|
|browserTimings/sendDuration|Kérelem küldési ideje|Ideje ezredmásodpercben|Átlag|A hálózati kapcsolódástól az első bájt érkezéséig eltelt idő.|Nincs dimenzió|
|browserTimings/totalDuration|Böngésző lapbetöltési ideje|Ideje ezredmásodpercben|Átlag|A felhasználói kérelemtől a DOM, a stíluslapok, a szkriptek és a képek betöltéséig eltelt idő.|Nincs dimenzió|
|függőségek és száma|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított hívások száma.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|függőségek/időtartama|Függőségi időtartam|Ideje ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|függőségek/nem sikerült|Függőségi hibák|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Lapmegtekintések|Darabszám|Darabszám|Lapmegtekintések száma.|a művelet/szintetikus|
|pageViews/duration|Lapmegtekintés betöltési ideje|Ideje ezredmásodpercben|Átlag|Lapmegtekintés betöltési ideje|a művelet/szintetikus|
|performanceCounters/requestExecutionTime|HTTP-kérés végrehajtási ideje|Ideje ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-kérések az alkalmazás sorában|Darabszám|Átlag|Az alkalmazás kérelem-várólistájának hossza.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-kérések gyakorisága|Egység/s|Átlag|Az ASP.NET által az alkalmazásnak küldött összes kérelem száma másodpercenként.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Kivételek gyakorisága|Egység/s|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekké konvertált nem kezelt kivételeket is.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Folyamat átviteli sebessége|Bájt/s|Átlag|Olvasási-írási műveletek fájlokon, hálózaton és eszközökön összesen (bájt/mp).|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Folyamat CPU|Százalék|Átlag|Az, hogy az összes folyamat szálak eltelt idő százalékában használták a processzort utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a w3wp folyamat teljesítményét jelzi.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processzoridő|Százalék|Átlag|A processzor nem üresjárati szálak futtatásával töltött százalékos időhányada.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Elérhető memória|Bájt|Átlag|Folyamatok vagy rendszerfeladatok számára azonnal kiosztható fizikai memória mennysége.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Folyamat saját bájtjai|Bájt|Átlag|A kizárólag a megfigyelt alkalmazás folyamataihoz hozzárendelt memória mennyisége.|cloud/roleInstance|
|kérések/időtartama|Kiszolgáló válaszideje|Ideje ezredmásodpercben|Átlag|Egy HTTP-kérelem fogadása és a válasz küldésének befejezése között eltelt idő.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|kérelmek és száma|Kiszolgálói kérelmek|Darabszám|Darabszám|Befejezett HTTP-kérelmek száma.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|sikertelen kérések /|Sikertelen kérelmek|Darabszám|Darabszám|Nem sikerült megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérelmek válaszkód > = 400-as és a 401-es nem egyenlő.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|kivételek és száma|Kivételek|Darabszám|Összes|A nem kezelt kivételek száma összesen.|cloud/roleName, cloud/roleInstance, client/type|
|kivételek és böngésző|Böngészőkivételek|Darabszám|Összes|A böngészőben fellépő nem kezelt kivételek száma.|Nincs dimenzió|
|kivételek és a kiszolgáló|Kiszolgálókivételek|Darabszám|Összes|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|cloud/roleName, cloud/roleInstance|
|nyomkövetési és száma|Hívásláncok száma|Darabszám|Összes|Híváslánc-dokumentumok száma|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Összes szolgáltatási API-találat|Darabszám|Darabszám|Szolgáltatási API-találatok teljes száma|Tevékenységtípus, ActivityName|
|ServiceApiLatency|A szolgáltatási API teljes késése|Ezredmásodperc|Átlag|A szolgáltatási API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Összes szolgáltatási API-eredmény|Darabszám|Darabszám|A szolgáltatási API-eredmények teljes száma|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|Gyorsítótár-kihasználtság|Százalék|Átlag|A fürt hatókörében kihasználtsága|Nincs dimenzió|
|QueryDuration|Lekérdezések időtartama|Ezredmásodperc|Átlag|A lekérdezések időtartama másodpercben|QueryStatus|
|IngestionsLoadFactor|Adatbetöltési kihasználtsága|Százalék|Átlag|A fürtben használt adatfeldolgozási pontok aránya|Nincs dimenzió|
|IsEngineAnsweringQuery|Életben tartási|Darabszám|Átlag|Megerősítést jelölőnégyzet azt jelzi, hogy a fürt lekérdezéseire|Nincs dimenzió|
|IngestCommandOriginalSizeInMb|Adatbetöltési kötet (megabájtban)|Darabszám|Összes|A fürthöz (megabájtban) a feldolgozott adatok teljes mennyisége|Nincs dimenzió|
|EventAgeSeconds|Adatbetöltési késés (másodpercben)|másodperc|Átlag|A betöltési idő másodpercben a fürthöz a forrás (pl. üzenet jelenik meg az EventHub)|Nincs dimenzió|
|EventReceivedFromEventHub|(Az Event Hubs) feldolgozott események|Darabszám|Összes|Ha az Event Hubs fürtjét a fürt által feldolgozott események száma|Nincs dimenzió|
|IngestionResult|Adatbetöltési eredménye|Darabszám|Darabszám|Az Adatbetöltési műveletek száma|IngestionResultDetails|
|EngineCPU|CPU|Százalék|Átlag|CPU-kihasználtsága|Nincs dimenzió|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Darabszám|Darabszám|Száma az API-hívások|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Darabszám|Összes|Az elindított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCompleted|Befejezett futtatások|Darabszám|Összes|A befejezett munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsSucceeded|Sikeres futtatások|Darabszám|Összes|A sikeres munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsFailed|Sikertelen futtatások|Darabszám|Összes|A sikertelen munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCancelled|Megszakított futtatások|Darabszám|Összes|A megszakított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunLatency|Futtatások késése|másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunSuccessLatency|Sikeres futtatások késése|másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunThrottledEvents|Futtatások által elindított események|Darabszám|Összes|A munkafolyamat-műveletek vagy -triggerek által elindított események száma.|Nincs dimenzió|
|RunFailurePercentage|Futtatási hibák százalékos értéke|Százalék|Összes|Sikertelen munkafolyamat-futtatások százalékos értéke.|Nincs dimenzió|
|ActionsStarted|Elindított műveletek |Darabszám|Összes|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|Befejezett műveletek |Darabszám|Összes|A befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|Sikeres műveletek |Darabszám|Összes|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Sikertelen műveletek|Darabszám|Összes|A sikertelen munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Darabszám|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|Műveletek késése |másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|Sikeres műveletek késése |másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|Műveletek által elindított események|Darabszám|Összes|A munkafolyamat-műveletek által elindított események száma.|Nincs dimenzió|
|TriggersStarted|Elindított triggerek |Darabszám|Összes|Az elindított munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersCompleted|Befejezett triggerek |Darabszám|Összes|A befejezett munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersSucceeded|Sikeres triggerek |Darabszám|Összes|A sikeres munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersFailed|Sikertelen triggerek |Darabszám|Összes|A sikertelen munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott triggerek|Darabszám|Összes|A kihagyott munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggersFired|Aktivált triggerek |Darabszám|Összes|Az aktivált munkafolyamat-triggerek száma.|Nincs dimenzió|
|TriggerLatency|Triggerek késése |másodperc|Átlag|A befejezett munkafolyamat-triggerek késése.|Nincs dimenzió|
|TriggerFireLatency|Triggerek aktiválásának késése |másodperc|Átlag|Az aktivált munkafolyamat-trigger késése.|Nincs dimenzió|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Átlag|A sikeres munkafolyamat-triggerek késése.|Nincs dimenzió|
|TriggerThrottledEvents|Triggerek által elindított események|Darabszám|Összes|A munkafolyamat-triggerek által elindított események száma.|Nincs dimenzió|
|BillableActionExecutions|Számlázható műveleti végrehajtások|Darabszám|Összes|Számlázandó munkafolyamat-műveleti végrehajtások száma.|Nincs dimenzió|
|BillableTriggerExecutions|Számlázható triggerek végrehajtásai|Darabszám|Összes|Számlázandó munkafolyamati triggerek végrehajtásainak száma.|Nincs dimenzió|
|TotalBillableExecutions|Összes számlázható végrehajtás|Darabszám|Összes|Számlázandó munkafolyamat-végrehajtások száma.|Nincs dimenzió|
|BillingUsageNativeOperation|Natív műveletek végrehajtásához kapcsolódó használat számlázása|Darabszám|Összes|A számlázandó natív műveletvégrehajtások száma.|Nincs dimenzió|
|BillingUsageStandardConnector|Normál összekötők végrehajtásához kapcsolódó használat számlázása|Darabszám|Összes|A számlázandó normál összekötő-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStorageConsumption|Tárterület-felhasználás végrehajtásához kapcsolódó használat számlázása|Darabszám|Összes|A számlázandó tárterületfelhasználás-végrehajtások száma.|Nincs dimenzió|
|BillingUsageNativeOperation|Natív műveletek végrehajtásához kapcsolódó használat számlázása|Darabszám|Összes|A számlázandó natív műveletvégrehajtások száma.|Nincs dimenzió|
|BillingUsageStandardConnector|Normál összekötők végrehajtásához kapcsolódó használat számlázása|Darabszám|Összes|A számlázandó normál összekötő-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStorageConsumption|Tárterület-felhasználás végrehajtásához kapcsolódó használat számlázása|Darabszám|Összes|A számlázandó tárterületfelhasználás-végrehajtások száma.|Nincs dimenzió|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageOtherLatency|Átlagos más késés|ms/op|Átlag|Átlagos (vagyis nem olvasási vagy írási) más késés (MS) / művelet|Nincs dimenzió|
|AverageReadLatency|Átlagos olvasási késés|ms/op|Átlag|Átlagos olvasási késés (MS) / művelet|Nincs dimenzió|
|AverageTotalLatency|Átlagos teljes késés|ms/op|Átlag|Átlagos teljes késés (MS) / művelet|Nincs dimenzió|
|AverageWriteLatency|Az átlagos írási késés|ms/op|Átlag|Az átlagos írási késés (MS) / művelet|Nincs dimenzió|
|FilesystemOtherOps|Fájlrendszer végzett egyéb művelet|Az OPS|Átlag|Fájlrendszer számos egyéb műveletek (vagyis nem Olvasás vagy írás)|Nincs dimenzió|
|FilesystemReadOps|Fájlrendszer olvasása|Az OPS|Átlag|Fájlrendszer számú olvasási műveletek|Nincs dimenzió|
|FilesystemTotalOps|Az ops teljes fájlrendszer|Az OPS|Átlag|Az összes fájlrendszerműveletek összege|Nincs dimenzió|
|FilesystemWriteOps|Fájlrendszer írási művelet|Az OPS|Átlag|Fájlrendszer írási műveletek száma|Nincs dimenzió|
|IoBytesPerOtherOps|I/o-bájtok száma végzett egyéb művelet|bytes/op|Átlag|Szám, a/kimenő bájtok száma egyéb műveletek (vagyis nem Olvasás vagy írás)|Nincs dimenzió|
|IoBytesPerReadOps|I/o-bájtok száma olvasási művelet|bytes/op|Átlag|Bejövő és kimenő bájtok száma olvasási műveletek száma|Nincs dimenzió|
|IoBytesPerTotalOps|I/o-bájtok száma op összes művelet között|bytes/op|Átlag|Az összes/kimenő bájtok művelet összeg|Nincs dimenzió|
|IoBytesPerWriteOps|I/o-bájtok száma írási művelet|bytes/op|Átlag|A szám a/kimenő bájtok száma írási művelet|Nincs dimenzió|
|OtherIops|Más iops|műveletek/mp|Átlag|Egyéb be/ki művelet / másodperc|Nincs dimenzió|
|OtherThroughput|Más átviteli sebesség|MB/s|Átlag|Más átviteli sebesség (vagyis nem olvasási vagy írási) másodpercenként (MB)|Nincs dimenzió|
|ReadIops|Olvasási iops-érték|műveletek/mp|Átlag|Olvassa el a bejövő és kimenő műveletek száma másodpercenként|Nincs dimenzió|
|ReadThroughput|Olvasás átviteli sebesség|MB/s|Átlag|Olvassa el az átviteli sebesség (MB) másodpercenként|Nincs dimenzió|
|TotalIops|Teljes iops|műveletek/mp|Átlag|Összege az összes be/ki műveletek száma másodpercenként|Nincs dimenzió|
|TotalThroughput|Teljes átvitel|MB/s|Átlag|Az összes átviteli sebesség (MB) másodpercenként összege|Nincs dimenzió|
|VolumeAllocatedSize|Kötet lefoglalt méret|bájt|Átlag|Lefoglalt kötet mérete határozza meg (nem a tényleges használt bájt)|Nincs dimenzió|
|VolumeLogicalSize|A kötet logikai mérete|bájt|Átlag|A kötet (felhasznált memória) logikai mérete|Nincs dimenzió|
|VolumeSnapshotSize|Kötet-pillanatképek mérete|bájt|Átlag|Az összes pillanatképek a kötet mérete|Nincs dimenzió|
|WriteIops|Az írási iops|műveletek/mp|Átlag|Írja be/ki műveletek száma másodpercenként|Nincs dimenzió|
|WriteThroughput|Átviteli sebesség írása|MB/s|Átlag|Átviteli sebesség írási / másodperc (MB)|Nincs dimenzió|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Kötet lefoglalt készlet mérete|bájt|Átlag|Lefoglalt méret a készlet (nem a tényleges használt bájt)|Nincs dimenzió|
|VolumePoolAllocatedUsed|Lefoglalt kötet készlet használt|bájt|Átlag|A készlet lefoglalt felhasznált mérete|Nincs dimenzió|
|VolumePoolTotalLogicalSize|Kötet készlet teljes logikai mérete|bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|Nincs dimenzió|
|VolumePoolTotalSnapshotSize|Kötet készlet teljes pillanatkép mérete|bájt|Átlag|A készlet összes pillanatképet összege|Nincs dimenzió|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Küldött bájtok|Darabszám|Összes|A hálózati adapter küldött bájtok száma|Nincs dimenzió|
|BytesReceivedRate|Fogadott bájtok száma|Darabszám|Összes|A hálózati adapter fogadott bájtok száma|Nincs dimenzió|
|PacketsSentRate|Küldött csomagok|Darabszám|Összes|A hálózati adapter küldött csomagok száma|Nincs dimenzió|
|PacketsReceivedRate|Fogadott csomagok|Darabszám|Összes|A hálózati adapter kapott csomagok száma|Nincs dimenzió|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|Az adatok elérési útja elérhetősége|Darabszám|Átlag|Átlagos időtartam / Load Balancer adatok elérési útja rendelkezésre állását|FrontendIPAddress, FrontendPort|
|DipAvailability|Állapot-mintavétel|Darabszám|Átlag|Átlagos Load Balancer mintavételi állapot szerint időtartam|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Bájtok száma|Darabszám|Összes|Időtartamon belül, átvitt bájtok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Csomagok száma|Darabszám|Összes|Időtartamon belül küldött csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Szinkronizálás a mi száma|Darabszám|Összes|Továbbított időtartamon belül SZIN csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT Connection Count|Darabszám|Összes|Új időtartamon belül létrehozott SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Lefoglalt SNAT portok (előzetes verzió)|Darabszám|Összes|Lefoglalt időtartamon belül, SNAT portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Használt SNAT portok (előzetes verzió)|Darabszám|Összes|Időtartamon belül használt SNAT portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezés kötet|Darabszám|Összes|A DNS-zónák kiszolgált lekérdezések száma|Nincs dimenzió|
|RecordSetCount|Rekord beállítása száma|Darabszám|Maximum|A DNS-zóna rekordhalmazok száma|Nincs dimenzió|
|RecordSetCapacityUtilization|Record Set Capacity Utilization|Százalék|Maximum|DNS-zóna az egyes rekordhalmaz-kapacitás százaléka|Nincs dimenzió|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|Egység/s|Maximum|Bejövő csomagok DDoS|Nincs dimenzió|
|PacketsDroppedDDoS|Bejövő miatt eldobott csomagok DDoS|Egység/s|Maximum|Bejövő miatt eldobott csomagok DDoS|Nincs dimenzió|
|PacketsForwardedDDoS|DDoS továbbított bejövő csomagok|Egység/s|Maximum|DDoS továbbított bejövő csomagok|Nincs dimenzió|
|TCPPacketsInDDoS|Bejövő TCP-csomagok DDoS|Egység/s|Maximum|Bejövő TCP-csomagok DDoS|Nincs dimenzió|
|TCPPacketsDroppedDDoS|A bejövő TCP-miatt eldobott csomagok DDoS|Egység/s|Maximum|A bejövő TCP-miatt eldobott csomagok DDoS|Nincs dimenzió|
|TCPPacketsForwardedDDoS|DDoS továbbított bejövő TCP-csomagok|Egység/s|Maximum|DDoS továbbított bejövő TCP-csomagok|Nincs dimenzió|
|UDPPacketsInDDoS|Bejövő DDoS elleni UDP-csomagok|Egység/s|Maximum|Bejövő DDoS elleni UDP-csomagok|Nincs dimenzió|
|UDPPacketsDroppedDDoS|UDP-bejövő csomagok eldobott DDoS|Egység/s|Maximum|UDP-bejövő csomagok eldobott DDoS|Nincs dimenzió|
|UDPPacketsForwardedDDoS|Bejövő továbbított DDoS elleni UDP-csomagok|Egység/s|Maximum|Bejövő továbbított DDoS elleni UDP-csomagok|Nincs dimenzió|
|BytesInDDoS|Bejövő bájtok DDoS|Bájt/s|Maximum|Bejövő bájtok DDoS|Nincs dimenzió|
|BytesDroppedDDoS|Bejövő bájtok eldobott DDoS|Bájt/s|Maximum|Bejövő bájtok eldobott DDoS|Nincs dimenzió|
|BytesForwardedDDoS|DDoS továbbított bejövő bájtok|Bájt/s|Maximum|DDoS továbbított bejövő bájtok|Nincs dimenzió|
|TCPBytesInDDoS|Bejövő TCP-bájtok DDoS|Bájt/s|Maximum|Bejövő TCP-bájtok DDoS|Nincs dimenzió|
|TCPBytesDroppedDDoS|A bejövő TCP-bájtok eldobott DDoS|Bájt/s|Maximum|A bejövő TCP-bájtok eldobott DDoS|Nincs dimenzió|
|TCPBytesForwardedDDoS|DDoS továbbított bejövő TCP bájt|Bájt/s|Maximum|DDoS továbbított bejövő TCP bájt|Nincs dimenzió|
|UDPBytesInDDoS|Bejövő DDoS elleni UDP bájtok|Bájt/s|Maximum|Bejövő DDoS elleni UDP bájtok|Nincs dimenzió|
|UDPBytesDroppedDDoS|Bejövő UDP bájt eldobott DDoS|Bájt/s|Maximum|Bejövő UDP bájt eldobott DDoS|Nincs dimenzió|
|UDPBytesForwardedDDoS|Bejövő, továbbítja a DDoS elleni UDP bájt|Bájt/s|Maximum|Bejövő, továbbítja a DDoS elleni UDP bájt|Nincs dimenzió|
|IfUnderDDoSAttack|A DDoS elleni támadás vagy sem|Darabszám|Maximum|A DDoS elleni támadás vagy sem|Nincs dimenzió|
|DDoSTriggerTCPPackets|DDoS elleni védelem aktiválásához bejövő TCP-csomagok|Egység/s|Maximum|DDoS elleni védelem aktiválásához bejövő TCP-csomagok|Nincs dimenzió|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagokat a DDoS elleni védelem aktiválásához|Egység/s|Maximum|Bejövő UDP-csomagokat a DDoS elleni védelem aktiválásához|Nincs dimenzió|
|DDoSTriggerSYNPackets|Bejövő szinkronizálás a mi csomagok DDoS elleni védelem aktiválásához|Egység/s|Maximum|Bejövő szinkronizálás a mi csomagok DDoS elleni védelem aktiválásához|Nincs dimenzió|
|VipAvailability|Az adatok elérési útja elérhetősége|Darabszám|Átlag|Átlagos időtartam / IP-cím elérhetősége|Port|
|ByteCount|Bájtok száma|Darabszám|Összes|Időtartamon belül, átvitt bájtok teljes száma|Port, iránya|
|PacketCount|Csomagok száma|Darabszám|Összes|Időtartamon belül küldött csomagok teljes száma|Port, iránya|
|SynCount|Szinkronizálás a mi száma|Darabszám|Összes|Továbbított időtartamon belül SZIN csomagok teljes száma|Port, iránya|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljesítmény|Teljesítmény|Bájt/s|Összes|Az Application Gateway szolgálta másodpercenként bájtok száma|Nincs dimenzió|
|UnhealthyHostCount|Nem kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Nem megfelelő állapotú háttérrendszer gazdagépek száma|BackendSettingsPool|
|HealthyHostCount|Kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Megfelelően működő háttér gazdagépek száma|BackendSettingsPool|
|TotalRequests|Összes kérelem|Darabszám|Összes|Az Application Gateway szolgálta sikeres kérelmek száma|BackendSettingsPool|
|FailedRequests|Sikertelen kérelmek|Darabszám|Összes|Az Application Gateway szolgálta sikertelen kérelmek száma|BackendSettingsPool|
|ResponseStatus|Válasz állapota|Darabszám|Összes|Az Application Gateway által visszaadott HTTP-válasz állapota|HttpStatusGroup|
|CurrentConnections|Jelenlegi kapcsolatok száma|Darabszám|Összes|Az Application Gatewayen a jelenlegi kapcsolatok száma|Nincs dimenzió|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageBandwidth|Átjáró S2S sávszélesség|Bájt/s|Átlag|Átlagos site-to-site sávszélesség egy átjáró bájt / másodpercben|Nincs dimenzió|
|P2SBandwidth|Átjáró P2S sávszélesség|Bájt/s|Átlag|Átlagos pont – hely sávszélesség egy átjáró bájt / másodpercben|Nincs dimenzió|
|P2SConnectionCount|P2S-kapcsolatok száma|Darabszám|Maximum|Az átjáró pont – hely kapcsolat száma|Protokoll|
|TunnelAverageBandwidth|Alagút sávszélessége|Bájt/s|Átlag|Átlagos sávszélesség-alagút bájt / másodpercben|ConnectionName, RemoteIP|
|TunnelEgressBytes|Alagút kimenő bájtok|Bájt|Összes|Kimenő bájtok-alagút|ConnectionName, RemoteIP|
|TunnelIngressBytes|Alagút bejövő bájtok|Bájt|Összes|Bejövő bájtok-alagút|ConnectionName, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagok|Darabszám|Összes|Egy alagutat a kimenő csomagok száma|ConnectionName, RemoteIP|
|TunnelIngressPackets|Alagút bejövő csomagok|Darabszám|Összes|Egy alagutat a bejövő csomag száma|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Alagút kimenő TS eltérés Csomagvesztéseinek|Darabszám|Összes|A forgalom választó eltérő egy alagutat a kimenő csomag eldobási száma|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Alagút bejövő TS eltérés Csomagvesztéseinek|Darabszám|Összes|A forgalom választó eltérő egy alagutat a bejövő csomag eldobási száma|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Egység/s|Átlag|A BITS ingressing Azure / másodperc|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|Egység/s|Átlag|A BITS Azure egressing / másodperc|Nincs dimenzió|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Egység/s|Átlag|A BITS ingressing Azure / másodperc|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|Egység/s|Átlag|A BITS Azure egressing / másodperc|Nincs dimenzió|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Egység/s|Átlag|A BITS ingressing Azure / másodperc|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|Egység/s|Átlag|A BITS Azure egressing / másodperc|Nincs dimenzió|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Lekérdezések által visszaadott végpont|Darabszám|Összes|Traffic Manager végpont adott vissza a megadott időkereten belül hányszor|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végpont végpont állapota|Darabszám|Maximum|Ha a végpontok mintavételi állapotának "engedélyezve" 1, 0 egyéb.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ProbesFailedPercent|Nem sikerült a(z) % mintavételek|Százalék|Átlag|a figyelés mintavételek kapcsolat % sikertelen|Nincs dimenzió|
|AverageRoundtripMs|Átl. Üzenetváltási idő (ms)|Ideje ezredmásodpercben|Átlag|Átlagos hálózati üzenetváltási idő (ms) figyelési mintavételek elküldve a forrás és cél közötti kapcsolat|Nincs dimenzió|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RequestCount|A kérelmek száma|Darabszám|Összes|A HTTP-vagy Https-proxy által kiszolgált ügyfélkérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Kérés mérete|Bájt|Összes|A HTTP-vagy Https-proxyval ügyfelektől érkező kérések küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Válasz mérete|Bájt|Összes|HTTP-vagy Https-proxy által az ügyfelek, válaszok küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Háttér-kérések száma|Darabszám|Összes|A HTTP-vagy Https-proxy háttérkomponenseinek küldött kérelmek száma|Válaszállapotok összege HTTP, HttpStatusGroup, háttér|
|BackendRequestLatency|Háttérbeli késleltetése|Ideje ezredmásodpercben|Átlag|Amikor a kérést küld a HTTP-vagy Https-proxy által a háttérrendszer mindaddig, amíg a HTTP-vagy Https-proxy kapott válasz utolsó bájtját a háttérbeli számított idő|Háttérszolgáltatás|
|TotalLatency|Teljes késés|Ideje ezredmásodpercben|Átlag|Ha az ügyfél kérést kapott a HTTP-vagy Https-proxy mindaddig, amíg az ügyfél arra vonatkozik, a HTTP-vagy Https-proxy a válasz utolsó bájtját számított idő|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Háttérkiszolgáló állapotadatainak százalékos aránya|Százalék|Átlag|Sikeres állapotjelentés aránya mintavételek a HTTP-vagy Https-proxy által háttérkomponenseinek|Háttér, Háttérkészletek|
|WebApplicationFirewallRequestCount|Web Application Firewall kérések száma|Darabszám|Összes|A webalkalmazási tűzfal által feldolgozott ügyfél-kérelmek száma|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|registration.all|Regisztrálási műveletek|Darabszám|Összes|Az összes sikeres regisztrációkra vonatkozó művelet (létrehozás, frissítés, lekérdezés és törlés) száma. |Nincs dimenzió|
|registration.create|Regisztráció-létrehozási műveletek|Darabszám|Összes|Az összes sikeres regisztráció-létrehozás száma.|Nincs dimenzió|
|registration.update|Regisztráció-frissítési műveletek|Darabszám|Összes|Az összes sikeres regisztrációfrissítés száma.|Nincs dimenzió|
|registration.get|Regisztráció-olvasási műveletek|Darabszám|Összes|Az összes sikeres regisztráció-lekérdezés száma.|Nincs dimenzió|
|registration.delete|Regisztráció-törlési műveletek|Darabszám|Összes|Az összes sikeres regisztrációtörlés száma.|Nincs dimenzió|
|bejövő|Bejövő üzenetek|Darabszám|Összes|Az összes sikeres API-hívásküldés száma. |Nincs dimenzió|
|Incoming.Scheduled|Elküldött ütemezett leküldéses értesítések|Darabszám|Összes|Megszakított ütemezett leküldéses értesítések|Nincs dimenzió|
|Incoming.Scheduled.Cancel|Megszakított ütemezett leküldéses értesítések|Darabszám|Összes|Megszakított ütemezett leküldéses értesítések|Nincs dimenzió|
|Scheduled.Pending|Függőben lévő ütemezett értesítések|Darabszám|Összes|Függőben lévő ütemezett értesítések|Nincs dimenzió|
|Installation.all|Telepítéskezelési műveletek|Darabszám|Összes|Telepítéskezelési műveletek|Nincs dimenzió|
|installation.get|Telepítéslekérdezési műveletek|Darabszám|Összes|Telepítéslekérdezési műveletek|Nincs dimenzió|
|Installation.upsert|Telepítés-létrehozási és -frissítési műveletek|Darabszám|Összes|Telepítés-létrehozási és -frissítési műveletek|Nincs dimenzió|
|Installation.Patch|Telepítésjavítási műveletek|Darabszám|Összes|Telepítésjavítási műveletek|Nincs dimenzió|
|installation.delete|Telepítéstörlési műveletek|Darabszám|Összes|Telepítéstörlési műveletek|Nincs dimenzió|
|outgoing.allpns.success|Sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.allpns.invalidpayload|Terhelési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS helytelen adattartalomra vonatkozó hibát adott vissza.|Nincs dimenzió|
|outgoing.allpns.pnserror|Külső értesítési rendszer hibái|Darabszám|Összes|A PNS szolgáltatással való kommunikáció közben történt (hitelesítési problémákon kívüli) hiba miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.allpns.channelerror|Csatornahibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a csatorna érvénytelen volt, nem a megfelelő alkalmazáshoz volt hozzárendelve, szabályozott volt vagy lejárt.|Nincs dimenzió|
|outgoing.allpns.badorexpiredchannel|Rossz vagy lejárt csatorna által okozott hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő csatorna, jogkivonat vagy regisztrációs azonosító lejárt vagy érvénytelen volt.|Nincs dimenzió|
|outgoing.wns.success|WNS – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.wns.invalidcredentials|WNS – hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva. (Windows Live nem ismeri fel a hitelesítő adatok).|Nincs dimenzió|
|outgoing.wns.badchannel|WNS – rossz csatorna által okozott hiba|Darabszám|Összes|A leküldések száma nem sikerült, mert a regisztrációban ismeretlen (WNS-állapot: 404 nem található).|Nincs dimenzió|
|outgoing.wns.expiredchannel|WNS – lejárt csatorna által okozott hiba|Darabszám|Összes|A leküldések száma nem sikerült, mert a regisztrációban lejárt (WNS-állapot: 410-es Gone).|Nincs dimenzió|
|outgoing.wns.throttled|WNS – szabályozott értesítések|Darabszám|Összes|A leküldések száma nem sikerült, mert a WNS szabályozza az alkalmazást (WNS-állapot: 406 nem megfelelő).|Nincs dimenzió|
|outgoing.wns.tokenproviderunreachable|WNS – hitelesítési hibák (nem érhető el)|Darabszám|Összes|A Windows Live szolgáltatás nem érhető el.|Nincs dimenzió|
|outgoing.wns.invalidtoken|WNS – hitelesítési hibák (érvénytelen jogkivonat)|Darabszám|Összes|Wns megadott jogkivonat érvénytelen (WNS-állapot: 401-es nem engedélyezett).|Nincs dimenzió|
|outgoing.wns.wrongtoken|WNS – hitelesítési hibák (nem megfelelő jogkivonat)|Darabszám|Összes|Wns megadott jogkivonat érvényes, de egy másik alkalmazás (WNS-állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztrációban rendelve egy másik alkalmazás. Ellenőrizze, hogy az ügyfélalkalmazás ugyanazt az alkalmazást, amelynek hitelesítő adatait az értesítési központban vannak társítva.|Nincs dimenzió|
|outgoing.wns.invalidnotificationformat|WNS – érvénytelen értesítési formátum|Darabszám|Összes|Az értesítés formátuma érvénytelen (WNS-állapot: 400). Vegye figyelembe, hogy WNS nem utasít el minden érvénytelen adattartalmat.|Nincs dimenzió|
|outgoing.wns.invalidnotificationsize|WNS – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az értesítési tartalom mérete túl nagy (WNS-állapot: 413).|Nincs dimenzió|
|outgoing.wns.channelthrottled|WNS – csatorna szabályozva|Darabszám|Összes|Az értesítés el lett dobva, mert a regisztrációban szabályozott (WNS-válaszfejléc: X-WNS-NotificationStatus:channelThrottled).|Nincs dimenzió|
|outgoing.wns.channeldisconnected|WNS – nincs kapcsolat a csatornával|Darabszám|Összes|Az értesítés el lett dobva, mert a regisztrációban szabályozott (WNS-válaszfejléc: X-WNS-DeviceConnectionStatus: disconnected).|Nincs dimenzió|
|outgoing.wns.dropped|WNS – elvetett értesítések|Darabszám|Összes|A rendszer elvetette az értesítést, mert a regisztrációban szereplő URI-azonosítójú csatorna szabályozott (X-WNS-NotificationStatus: dropped, de nem X-WNS-DeviceConnectionStatus: disconnected).|Nincs dimenzió|
|outgoing.wns.pnserror|WNS-hibák|Darabszám|Összes|Az értesítés nem lett kézbesítve, mert hiba történt a WNS szolgáltatással való kommunikáció közben.|Nincs dimenzió|
|outgoing.wns.authenticationerror|WNS – hitelesítési hibák|Darabszám|Összes|Az értesítés nem lett kézbesítve, mert hiba történt a Windows Live szolgáltatással való kommunikáció közben; érvénytelenek a hitelesítő adatok vagy nem megfelelő a jogkivonat.|Nincs dimenzió|
|outgoing.apns.success|APNS – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.apns.invalidcredentials|APNS-hitelesítési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.apns.badchannel|APNS – rossz csatorna által okozott hiba|Darabszám|Összes|A leküldések száma nem sikerült, mert a jogkivonat érvénytelen (APNS-állapotkód: 8).|Nincs dimenzió|
|outgoing.apns.expiredchannel|APNS – lejárt csatorna által okozott hiba|Darabszám|Összes|Az APNS visszajelzési csatornája által érvénytelenített jogkivonatok száma.|Nincs dimenzió|
|outgoing.apns.invalidnotificationsize|APNS – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az adattartalom túl nagy a sikertelen leküldések száma, (APNS-állapotkód: 7).|Nincs dimenzió|
|outgoing.apns.pnserror|APNS-hibák|Darabszám|Összes|Az APNS szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.success|GCM – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.gcm.invalidcredentials|GCM – hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.gcm.badchannel|GCM – rossz csatorna által okozott hiba|Darabszám|Összes|A leküldések száma nem sikerült, mert a regisztrációban szereplő regisztrációs azonosító ismeretlen (GCM-eredmény: Érvénytelen regisztrációs).|Nincs dimenzió|
|outgoing.gcm.expiredchannel|GCM – lejárt csatorna által okozott hiba|Darabszám|Összes|Leküldések száma, amely nem sikerült, mert a regisztrációban szereplő regisztrációs azonosító lejárt (GCM-eredmény: NotRegistered).|Nincs dimenzió|
|outgoing.gcm.throttled|GCM – szabályozott értesítések|Darabszám|Összes|A leküldések száma nem sikerült, mert a GCM szabályozta az alkalmazást (GCM-állapotkód: 501-599 vagy eredmény: nem érhető el).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationformat|GCM – érvénytelen értesítési formátum|Darabszám|Összes|Leküldések száma, hogy nem sikerült, mert a tartalom formázása nem megfelelő (GCM-eredmény: InvalidDataKey vagy InvalidTtl).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationsize|GCM – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az adattartalom túl nagy a sikertelen leküldések száma, (GCM-eredmény: MessageTooBig).|Nincs dimenzió|
|outgoing.gcm.wrongchannel|GCM – nem megfelelő csatorna által okozott hiba|Darabszám|Összes|Nem sikerült, mert a regisztrációban szereplő regisztrációs azonosító nem a jelenlegi alkalmazáshoz kapcsolódó leküldések száma (GCM-eredmény: InvalidPackageName).|Nincs dimenzió|
|outgoing.gcm.pnserror|GCM-hibák|Darabszám|Összes|A GCM szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.authenticationerror|GCM-hitelesítési hibák|Darabszám|Összes|A PNS nem fogadta el a megadott hitelesítő adatokat a hitelesítő adatok miatt sikertelen leküldések száma le vannak tiltva, vagy hogy a nem megfelelően van konfigurálva az alkalmazásban (GCM-eredmény: MismatchedSenderId).|Nincs dimenzió|
|outgoing.mpns.success|MPNS – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.mpns.invalidcredentials|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.mpns.badchannel|MPNS – rossz csatorna által okozott hiba|Darabszám|Összes|A leküldések száma nem sikerült, mert a regisztrációban ismeretlen (MPNS-állapot: 404 nem található).|Nincs dimenzió|
|outgoing.mpns.throttled|MPNS – szabályozott értesítések|Darabszám|Összes|A leküldések száma nem sikerült, mert az MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem megfelelő).|Nincs dimenzió|
|outgoing.mpns.invalidnotificationformat|MPNS – érvénytelen értesítési formátum|Darabszám|Összes|Az értesítési tartalom túl nagy mérete miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.mpns.channeldisconnected|MPNS – nincs kapcsolat a csatornával|Darabszám|Összes|A leküldések száma nem sikerült, mert a regisztrációban le lett választva (MPNS-állapot: 412 nem található).|Nincs dimenzió|
|outgoing.mpns.dropped|MPNS – elvetett értesítések|Darabszám|Összes|A MPNS által elvetett leküldések száma (MPNS-válaszfejléc: X-NotificationStatus: QueueFull vagy letiltott).|Nincs dimenzió|
|outgoing.mpns.pnserror|MPNS-hibák|Darabszám|Összes|Az MPNS szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.mpns.authenticationerror|MPNS – hitelesítési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|notificationhub.pushes|Minden kimenő értesítés|Darabszám|Összes|Az értesítési központ minden kimenő értesítése|Nincs dimenzió|
|incoming.all.requests|Minden bejövő kérelem|Darabszám|Összes|Egy értesítési központ összes bejövő kérelme|Nincs dimenzió|
|Incoming.all.failedrequests|Minden sikertelen bejövő kérelem|Darabszám|Összes|Egy értesítési központ összes sikertelen bejövő kérelme|Nincs dimenzió|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_ % szabad Inode-OK|Szabad Inode-OK|Darabszám|Átlag|Average_ % szabad Inode-OK|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % szabad terület|% Szabad terület|Darabszám|Átlag|Average_ % szabad terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt Inode-OK Average_ %|Foglalt Inode-OK %|Darabszám|Átlag|Foglalt Inode-OK Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt hely Average_ %|Foglalt hely %|Darabszám|Átlag|Foglalt hely Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk olvasott bájt/mp|Lemezolvasási sebesség (bájt/s)|Darabszám|Átlag|Average_Disk olvasott bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk/mp|Lemezolvasások/mp|Darabszám|Átlag|Average_Disk/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk átvitel/mp|Átvitel/mp|Darabszám|Átlag|Average_Disk átvitel/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Zapsané Bajty/s|Lemezírási sebesség (bájt/s)|Darabszám|Átlag|Average_Disk Zapsané Bajty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Lemezírások/mp|Lemezírások/mp|Darabszám|Átlag|Average_Disk Lemezírások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Szabad hely MB-ban|Darabszám|Átlag|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical bájt/mp|Logikai lemez bájt/mp|Darabszám|Átlag|Average_Logical bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ rendelkezésre álló memória %|Rendelkezésre álló memória %|Darabszám|Átlag|Average_ rendelkezésre álló memória %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ rendelkezésre álló Lapozóterület %|Rendelkezésre álló Lapozóterület %|Darabszám|Átlag|Average_ rendelkezésre álló Lapozóterület %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt memória Average_ %|Foglalt memória %|Darabszám|Átlag|Foglalt memória Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt Lapozóterület Average_ %|Foglalt Lapozóterület %|Darabszám|Átlag|Foglalt Lapozóterület Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available álló memória|Rendelkezésre álló memória|Darabszám|Átlag|Average_Available álló memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available lapozási memória|Rendelkezésre álló Lapozóterület|Darabszám|Átlag|Average_Available lapozási memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page/mp|Olvasott lap/mp|Darabszám|Átlag|Average_Page/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Lemezírások/mp|Írt lap/mp|Darabszám|Átlag|Average_Page Lemezírások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Lap/mp|Darabszám|Átlag|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used álló Lapozóterület|Használt lapozási memória terület|Darabszám|Átlag|Average_Used álló Lapozóterület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória (MB)|Használt memória (MB)|Darabszám|Átlag|Average_Used memória (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total átvitt bájtok|Küldött bájtok száma összesen|Darabszám|Átlag|Average_Total átvitt bájtok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total fogadott bájtok száma|Fogadott bájtok teljes száma|Darabszám|Átlag|Average_Total fogadott bájtok száma|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bájtok|Összes bájt|Darabszám|Átlag|Average_Total bájtok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Átvitt Average_Total csomagok|Az összes csomag továbbított adatok köre|Darabszám|Átlag|Átvitt Average_Total csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Total csomag|Összes fogadott csomag|Darabszám|Átlag|Fogadott Average_Total csomag|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx hibák|Teljes Rx-hibák|Darabszám|Átlag|Average_Total Rx hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx hibák|Teljes Tx-hibák|Darabszám|Átlag|Average_Total Tx hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total ütközések|Teljes ütközések|Darabszám|Átlag|Average_Total ütközések|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Átl. Lemez mp/Olvasás|Darabszám|Átlag|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/átvitel|Átl. Lemez mp/átvitel|Darabszám|Átlag|Average_Avg. Lemez mp/átvitel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Átl. Lemez mp/írás|Darabszám|Átlag|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical bájt/mp|Fizikai lemez bájt/mp|Darabszám|Átlag|Average_Physical bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Védett módú Average_Pct|A PCT kiemelt idő|Darabszám|Átlag|Védett módú Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Felhasználói idő Average_Pct|A PCT felhasználói idő|Darabszám|Átlag|Felhasználói idő Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória KB|Használt memória mérete kilobájtban|Darabszám|Átlag|Average_Used memória KB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|A megosztott memória Average_Virtual|A megosztott virtuális memória|Darabszám|Átlag|A megosztott memória Average_Virtual|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ DPC idő %|DPC idő %|Darabszám|Átlag|Average_ DPC idő %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ inaktivitási idő %|Inaktivitási idő %|Darabszám|Átlag|Average_ inaktivitási idő %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ megszakítási idő %|Megszakítási idő %|Darabszám|Átlag|Average_ megszakítási idő %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % IO várakozási idő|A(z) % IO várakozási idő|Darabszám|Átlag|Average_ % IO várakozási idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ %-ban a processzoron idő|Idő %-ban a processzoron|Darabszám|Átlag|Average_ %-ban a processzoron idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Védett módú Average_ %|Védett módú használat % idő|Darabszám|Átlag|Védett módú Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ processzoridő|Processzoridő|Darabszám|Átlag|Average_ processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Felhasználói idő Average_ %|Felhasználói idő %|Darabszám|Átlag|Felhasználói idő Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fizikai memória|Szabad fizikai memória|Darabszám|Átlag|Average_Free fizikai memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free hely a Lapozófájlokban|Szabad hely a Lapozófájlokban|Darabszám|Átlag|Average_Free hely a Lapozófájlokban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuális memória|Szabad virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Folyamatok|Darabszám|Átlag|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|A Lapozófájlokban tárolt Average_Size|A Lapozófájlokban tárolt méret|Darabszám|Átlag|A Lapozófájlokban tárolt Average_Size|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Hasznos üzemidő|Darabszám|Átlag|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Felhasználók|Darabszám|Átlag|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Átl. Lemez mp/Olvasás|Darabszám|Átlag|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Átl. Lemez mp/írás|Darabszám|Átlag|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Lemezvárólista hossza|Lemezvárólista jelenlegi hossza|Darabszám|Átlag|Average_Current Lemezvárólista hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk/mp|Lemezolvasások/mp|Darabszám|Átlag|Average_Disk/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk átvitel/mp|Átvitel/mp|Darabszám|Átlag|Average_Disk átvitel/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Lemezírások/mp|Lemezírások/mp|Darabszám|Átlag|Average_Disk Lemezírások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Szabad hely MB-ban|Darabszám|Átlag|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % szabad terület|% Szabad terület|Darabszám|Átlag|Average_ % szabad terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available (MB)|Rendelkezésre álló memória|Darabszám|Átlag|Average_Available (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ véglegesített % bájt használatban|Előjegyzett kihasználtsága (%)|Darabszám|Átlag|Average_ véglegesített % bájt használatban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Fogadott bájtok/mp|Darabszám|Átlag|Fogadott Average_Bytes/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes küldött/mp|Küldött bájtok/s|Darabszám|Átlag|Average_Bytes küldött/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes összes/mp|Összes bájt/mp|Darabszám|Átlag|Average_Bytes összes/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ processzoridő|Processzoridő|Darabszám|Átlag|Average_ processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor várólistájának hossza|Processzor-várólista hossza|Darabszám|Átlag|Average_Processor várólistájának hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Szívverés|Szívverés|Darabszám|Összes|Szívverés|Computer, OSType, Version, SourceComputerId|
|Frissítés|Frissítés|Darabszám|Átlag|Frissítés|Számítógép, termék, a besorolás, a UpdateState, nem kötelező, jóváhagyott|
|Esemény|Esemény|Darabszám|Átlag|Esemény|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Lekérdezések időtartama|Ezredmásodperc|Átlag|DAX-lekérdezés időtartama az elmúlt időköz|Nincs dimenzió|
|QueryPoolJobQueueLength|Szálak: Lekérdezési készlet feladat-várólistájának hossza|Darabszám|Átlag|Az a lekérdezési szálkészlet üzenetsorában található feladatok száma.|Nincs dimenzió|
|qpu_high_utilization_metric|QPU magas kihasználtság|Darabszám|Összes|QPU magas kihasználtság az elmúlt percben, 1 QPU magas kihasználtság, különben 0|Nincs dimenzió|
|memory_metric|Memory (Memória)|Bájt|Átlag|A memória. Tartomány 0 – 3 GB az a1-es, 0 – 5 GB az a2-es, 0 – 10 GB az A3, a4-es 0 – 25 GB, a5 0-50 GB-os és 0 – 100 GB-A6|Nincs dimenzió|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Átlagos memóriaakadozás.|Nincs dimenzió|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Figyelőkapcsolatok-Siker|Figyelőkapcsolatok-Siker|Darabszám|Összes|A Microsoft.Relay sikeresen létrejött figyelőkapcsolatai.|EntityName|
|Figyelőkapcsolatok-Ügyfélhiba|Figyelőkapcsolatok-Ügyfélhiba|Darabszám|Összes|A Microsoft.Relay figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName|
|Figyelőkapcsolatok-Kiszolgálóhiba|Figyelőkapcsolatok-Kiszolgálóhiba|Darabszám|Összes|A Microsoft.Relay figyelőkapcsolatokra vonatkozó kiszolgálóhibái.|EntityName|
|FeladóiKapcsolatok-Siker|FeladóiKapcsolatok-Siker|Darabszám|Összes|A Microsoft.Relay sikeresen létrejött feladói kapcsolatai.|EntityName|
|FeladóiKapcsolatok-Ügyfélhiba|FeladóiKapcsolatok-Ügyfélhiba|Darabszám|Összes|A Microsoft.Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName|
|FeladóiKapcsolatok-Kiszolgálóhiba|FeladóiKapcsolatok-Kiszolgálóhiba|Darabszám|Összes|A Microsoft.Relay feladói kapcsolatokra vonatkozó kiszolgálóhibái.|EntityName|
|Figyelőkapcsolatok-KérelmekÖsszesen|Figyelőkapcsolatok-KérelmekÖsszesen|Darabszám|Összes|A Microsoft.Relay figyelőkapcsolatai összesen.|EntityName|
|FeladóiKapcsolatok-KérelmekÖsszesen|FeladóiKapcsolatok-KérelmekÖsszesen|Darabszám|Összes|A Microsoft.Relay feladói kapcsolatai összesen.|EntityName|
|AktívKapcsolatok|AktívKapcsolatok|Darabszám|Összes|A Microsoft.Relay aktív kapcsolatai összesen.|EntityName|
|AktívFigyelők|AktívFigyelők|Darabszám|Összes|A Microsoft.Relay aktív figyelői összesen.|EntityName|
|ÁtvittBájtok|ÁtvittBájtok|Darabszám|Összes|A Microsoft.Relay által átvitt bájtok összesen.|EntityName|
|Figyelőkapcsolat-bontások|Figyelőkapcsolat-bontások|Darabszám|Összes|A Microsoft.Relay figyelőkapcsolat-bontásai összesen.|EntityName|
|Feladóikapcsolat-bontások|Feladóikapcsolat-bontások|Darabszám|Összes|A Microsoft.Relay feladóikapcsolat-bontásai összesen.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|másodperc|Átlag|A keresési szolgáltatás átlagos keresési késés|Nincs dimenzió|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|Egység/s|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatás|Nincs dimenzió|
|ThrottledSearchQueriesPercentage|Szabályozott lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás szabályozva lettek keresési lekérdezések aránya|Nincs dimenzió|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előnézet)|Darabszám|Összes|(Előzetes verzió) a névtér összes sikeres kérelme|EntityName|
|ServerErrors|Kiszolgálóhibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus kiszolgálóhibáinak száma. (Előzetes verzió)|EntityName|
|UserErrors|Felhasználói hibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus felhasználói hibáinak száma. (Előzetes verzió)|EntityName|
|ThrottledRequests|Szabályozott kérelmek száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus szabályozott kérelmeinek száma. (Előzetes verzió)|EntityName|
|IncomingRequests|A bejövő kérések (előnézet)|Darabszám|Összes|A Microsoft.ServiceBus bejövő kérelmeinek száma. (Előzetes verzió)|EntityName|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus bejövő üzeneteinek száma. (Előzetes verzió)|EntityName|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus kimenő üzeneteinek száma. (Előzetes verzió)|EntityName|
|AktívKapcsolatok|Aktív kapcsolatai (előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus aktív kapcsolatainak száma összesen. (Előzetes verzió)|Nincs dimenzió|
|Méret|Méret (előzetes verzió)|Bájt|Átlag|Az üzenetsor vagy témakör mérete (bájt). (Előzetes verzió)|EntityName|
|Üzenetek|Az üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|Darabszám|Átlag|Az üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|EntityName|
|ActiveMessages|Az üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|Darabszám|Átlag|Az üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|EntityName|
|CPUXNS|Processzorhasználat névterenként|Százalék|Maximum|Prémium szintű Service Bus-névtér CPU-használati metrikája|Nincs dimenzió|
|WSXNS|Felhasznál memória mérete névterenként|Százalék|Maximum|Prémium szintű Service Bus-névtér memóriahasználati metrikája|Nincs dimenzió|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ConnectionCount|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolat mennyisége.|Végpont|
|MessageCount|Üzenetek száma|Darabszám|Összes|Üzenetek teljes mennyisége.|Nincs dimenzió|
|InboundTraffic|Bejövő forgalom|Bájt|Összes|A szolgáltatás a bejövő adatforgalom|Nincs dimenzió|
|OutboundTraffic|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom a szolgáltatás|Nincs dimenzió|
|UserErrors|Felhasználói hibák|Százalék|Maximum|A felhasználói hibáinak aránya|Nincs dimenzió|
|SystemErrors|Rendszerhibák|Százalék|Maximum|A rendszer hibák százaléka|Nincs dimenzió|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincs dimenzió|
|log_write_percent|Napló i/o-százaléka|Százalék|Átlag|Napló i/o-százaléka|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|Nincs dimenzió|
|tárterület|Adatbázis teljes mérete|Bájt|Maximum|Adatbázis teljes mérete|Nincs dimenzió|
|connection_successful|Sikeres kapcsolatok|Darabszám|Összes|Sikeres kapcsolatok|Nincs dimenzió|
|connection_failed|Sikertelen kapcsolatok|Darabszám|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|blocked_by_firewall|Tűzfal által blokkolva|Darabszám|Összes|Tűzfal által blokkolva|Nincs dimenzió|
|Holtpont|Holtpontok|Darabszám|Összes|Holtpontok|Nincs dimenzió|
|storage_percent|Adatbázis méretének kihasználtsága|Százalék|Maximum|Adatbázis méretének kihasználtsága|Nincs dimenzió|
|xtp_storage_percent|Memóriabeli OLTP storage százalék|Százalék|Átlag|Memóriabeli OLTP storage százalék|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Százalék|Átlag|Feldolgozók százalékos aránya|Nincs dimenzió|
|sessions_percent|Munkamenetek százaléka|Százalék|Átlag|Munkamenetek százaléka|Nincs dimenzió|
|dtu_limit|DTU-korlát|Darabszám|Átlag|DTU-korlát|Nincs dimenzió|
|dtu_used|Használt dtu-k|Darabszám|Átlag|Használt dtu-k|Nincs dimenzió|
|dwu_limit|DWU-korlát|Darabszám|Maximum|DWU-korlát|Nincs dimenzió|
|dwu_consumption_percent|Százalékos DWU|Százalék|Maximum|Százalékos DWU|Nincs dimenzió|
|dwu_used|Alkalmazott DWU|Darabszám|Maximum|Alkalmazott DWU|Nincs dimenzió|
|dw_cpu_percent|DW csomópont szintjén Processzorhasználat (%)|Százalék|Átlag|DW csomópont szintjén Processzorhasználat (%)|DwLogicalNodeId|
|dw_physical_data_read_percent|DW csomópont adat IO százalékos értéke|Százalék|Átlag|DW csomópont adat IO százalékos értéke|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincs dimenzió|
|log_write_percent|Napló i/o-százaléka|Százalék|Átlag|Napló i/o-százaléka|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|Nincs dimenzió|
|storage_percent|Tárolási százalékos aránya|Százalék|Átlag|Tárolási százalékos aránya|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Százalék|Átlag|Feldolgozók százalékos aránya|Nincs dimenzió|
|sessions_percent|Munkamenetek százaléka|Százalék|Átlag|Munkamenetek százaléka|Nincs dimenzió|
|eDTU_limit|az eDTU-korlát|Darabszám|Átlag|az eDTU-korlát|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Átlag|Tárolási kapacitása|Nincs dimenzió|
|eDTU_used|használt edtu-k|Darabszám|Átlag|használt edtu-k|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|xtp_storage_percent|Memóriabeli OLTP storage százalék|Százalék|Átlag|Memóriabeli OLTP storage százalék|Nincs dimenzió|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|virtual_core_count|Virtuális magok száma|Darabszám|Átlag|Virtuális magok száma|Nincs dimenzió|
|avg_cpu_percent|Átlagos Processzorhasználat (%)|Százalék|Átlag|Átlagos Processzorhasználat (%)|Nincs dimenzió|
|reserved_storage_mb|Lefoglalt lemezterület|Darabszám|Átlag|Lefoglalt lemezterület|Nincs dimenzió|
|storage_space_used_mb|Felhasznált lemezterület|Darabszám|Átlag|Felhasznált lemezterület|Nincs dimenzió|
|io_requests|I/o-kérelmek száma|Darabszám|Átlag|I/o-kérelmek száma|Nincs dimenzió|
|io_bytes_read|Olvasási i/o-bájtok|Bájt|Átlag|Olvasási i/o-bájtok|Nincs dimenzió|
|io_bytes_written|I/o írt bájtok száma|Bájt|Átlag|I/o írt bájtok száma|Nincs dimenzió|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Használt kapacitás|Bájt|Összes|A fiók felhasznált kapacitása|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|Blob-kapacitása|Bájt|Összes|A tárfiók Blob Service-példánya által felhasznált tárterület mérete bájtban megadva.|BlobType|
|BlobCount|Blobok száma|Darabszám|Összes|A tárfiók Blob Service-példányában található blobok száma.|BlobType|
|ContainerCount|Blobtárolók száma|Darabszám|Átlag|A tárfiók Blob Service-példányában található tárolók száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, Authentication|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, Authentication|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, Authentication|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|File Storage kapacitása|Bájt|Átlag|A tárfiók File Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|FileCount|Fájlok száma|Darabszám|Átlag|A tárfiók File Storage-szolgáltatás-példányában található fájlok száma.|Nincs dimenzió|
|FileShareCount|Fájlmegosztások száma|Darabszám|Átlag|A tárfiók File Storage-szolgáltatás-példányában található fájlmegosztások száma.|Nincs dimenzió|
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

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU százalékos kihasználtsága|Százalék|Maximum|SU százalékos kihasználtsága|Nincs dimenzió|
|InputEvents|Bemeneti események|Darabszám|Összes|Bemeneti események|Nincs dimenzió|
|InputEventBytes|Bemeneti esemény bájtokban|Bájt|Összes|Bemeneti esemény bájtokban|Nincs dimenzió|
|LateInputEvents|Késedelmes bemeneti események|Darabszám|Összes|Késedelmes bemeneti események|Nincs dimenzió|
|Outputevents értéke|Kimeneti események|Darabszám|Összes|Kimeneti események|Nincs dimenzió|
|ConversionErrors|Adatkonverziós hibák|Darabszám|Összes|Adatkonverziós hibák|Nincs dimenzió|
|Hibák|Futásidejű hibák|Darabszám|Összes|Futásidejű hibák|Nincs dimenzió|
|DroppedOrAdjustedEvents|Üzemen kívüli események|Darabszám|Összes|Üzemen kívüli események|Nincs dimenzió|
|AMLCalloutRequests|Függvénykérések|Darabszám|Összes|Függvénykérések|Nincs dimenzió|
|AMLCalloutFailedRequests|Sikertelen függvénykérések|Darabszám|Összes|Sikertelen függvénykérések|Nincs dimenzió|
|AMLCalloutInputEvents|Függvényesemények|Darabszám|Összes|Függvényesemények|Nincs dimenzió|
|DeserializationError|A deszerializálás bemeneti hibái|Darabszám|Összes|A deszerializálás bemeneti hibái|Nincs dimenzió|
|EarlyInputEvents|Korai bemeneti események|Darabszám|Összes|Korai bemeneti események|Nincs dimenzió|
|OutputWatermarkDelaySeconds|Vízjel-késleltetés|másodperc|Maximum|Vízjel-késleltetés|Nincs dimenzió|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő üzenetek fogadása|Darabszám|Összes|Az összes eseményközpontot vagy IoT hubot eseményforrások olvasni az üzenetek száma|Nincs dimenzió|
|IngressReceivedInvalidMessages|Bejövő forgalom érvénytelen üzenet érkezett|Darabszám|Összes|Az összes eseményközpontot vagy IoT hubot eseményforrások olvasni érvénytelen üzenetek száma|Nincs dimenzió|
|IngressReceivedBytes|Bejövő bájtokat fogadott|Bájt|Összes|Minden esemény forrásból beolvasott bájtok száma|Nincs dimenzió|
|IngressStoredBytes|Bejövő bájtok tárolt|Bájt|Összes|Sikeresen feldolgozott és lekérdezhető események teljes mérete|Nincs dimenzió|
|IngressStoredEvents|Bejövő forgalom tárolt események|Darabszám|Összes|Sikeresen feldolgozott és lekérdezhető egybesimított események száma|Nincs dimenzió|
|IngressReceivedMessagesTimeLag|Bejövő forgalom a fogadott üzenetek idő elteltével|másodperc|Maximum|A, amely az üzenet az eseményforrás a várólistán lévő és az idő a bejövő forgalom feldolgozása közötti különbség|Nincs dimenzió|
|IngressReceivedMessagesCountLag|Bejövő forgalom a fogadott üzenetek száma késés|Darabszám|Átlag|Utolsó várólistán lévő üzenetek sorszáma közötti különbség az adatforrás a bejövő forgalom feldolgozott üzenet partíció- és feladatütemezési száma|Nincs dimenzió|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő üzenetek fogadása|Darabszám|Összes|Az eseményforrás olvasni üzenetek száma|Nincs dimenzió|
|IngressReceivedInvalidMessages|Bejövő forgalom érvénytelen üzenet érkezett|Darabszám|Összes|Az eseményforrás olvasni érvénytelen üzenetek száma|Nincs dimenzió|
|IngressReceivedBytes|Bejövő bájtokat fogadott|Bájt|Összes|Az esemény forrásból beolvasott bájtok száma|Nincs dimenzió|
|IngressStoredBytes|Bejövő bájtok tárolt|Bájt|Összes|Sikeresen feldolgozott és lekérdezhető események teljes mérete|Nincs dimenzió|
|IngressStoredEvents|Bejövő forgalom tárolt események|Darabszám|Összes|Sikeresen feldolgozott és lekérdezhető egybesimított események száma|Nincs dimenzió|
|IngressReceivedMessagesTimeLag|Bejövő forgalom a fogadott üzenetek idő elteltével|másodperc|Maximum|A, amely az üzenet az eseményforrás a várólistán lévő és az idő a bejövő forgalom feldolgozása közötti különbség|Nincs dimenzió|
|IngressReceivedMessagesCountLag|Bejövő forgalom a fogadott üzenetek száma késés|Darabszám|Átlag|Utolsó várólistán lévő üzenetek sorszáma közötti különbség az adatforrás a bejövő forgalom feldolgozott üzenet partíció- és feladatütemezési száma|Nincs dimenzió|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Százalék|Átlag|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Darabszám|Átlag|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Darabszám|Átlag|HTTP-várólista hossza|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (kivéve a functions)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|Processzoridő|másodperc|Összes|Processzoridő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összes|Kérelmek|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Darabszám|Összes|HTTP 101|Példány|
|Http2xx|HTTP 2xx|Darabszám|Összes|HTTP 2xx|Példány|
|Http3xx|HTTP 3xx|Darabszám|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401|Darabszám|Összes|HTTP 401|Példány|
|Http403|HTTP 403|Darabszám|Összes|HTTP 403|Példány|
|Http404|HTTP 404|Darabszám|Összes|HTTP 404|Példány|
|Http406|HTTP 406|Darabszám|Összes|HTTP 406|Példány|
|Http4xx|Http 4xx|Darabszám|Összes|Http 4xx|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Darabszám|Összes|HTTP-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória-munkakészlet|Bájt|Átlag|Memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Átlag|Átlagos memória-munkakészlet|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|AppConnections|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|Leírók|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
|Szálak|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Átlag|Saját [nem megosztható] memória (bájt)|Példány|
|IoReadBytesPerSecond|I/O – olvasás (bájt/másodperc)|Bájt/s|Összes|I/O – olvasás (bájt/másodperc)|Példány|
|IoWriteBytesPerSecond|I/O – írás (bájt/másodperc)|Bájt/s|Összes|I/O – írás (bájt/másodperc)|Példány|
|IoOtherBytesPerSecond|I/O – egyéb (bájt/másodperc)|Bájt/s|Összes|I/O – egyéb (bájt/másodperc)|Példány|
|IoReadOperationsPerSecond|I/O – olvasás (művelet/másodperc)|Bájt/s|Összes|I/O – olvasás (művelet/másodperc)|Példány|
|IoWriteOperationsPerSecond|I/O – írás (művelet/másodperc)|Bájt/s|Összes|I/O – írás (művelet/másodperc)|Példány|
|IoOtherOperationsPerSecond|I/O – egyéb (művelet/másodperc)|Bájt/s|Összes|I/O – egyéb (művelet/másodperc)|Példány|
|RequestsInApplicationQueue|Alkalmazás-várólistán lévő kérelmek|Darabszám|Átlag|Alkalmazás-várólistán lévő kérelmek|Példány|
|CurrentAssemblies|Szerelvények pillanatnyi száma|Darabszám|Átlag|Szerelvények pillanatnyi száma|Példány|
|TotalAppDomains|Alkalmazástartományok összesen|Darabszám|Átlag|Alkalmazástartományok összesen|Példány|
|TotalAppDomainsUnloaded|Memóriából eltávolított alkalmazástartományok összesen|Darabszám|Átlag|Memóriából eltávolított alkalmazástartományok összesen|Példány|
|Gen0Collections|0. generációs szemétgyűjtések száma|Darabszám|Összes|0. generációs szemétgyűjtések száma|Példány|
|Gen1Collections|1. generációs szemétgyűjtések száma|Darabszám|Összes|1. generációs szemétgyűjtések száma|Példány|
|Gen2Collections|2. generációs szemétgyűjtések száma|Darabszám|Összes|2. generációs szemétgyűjtések száma|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Darabszám|Összes|HTTP-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória-munkakészlet|Bájt|Átlag|Memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Átlag|Átlagos memória-munkakészlet|Példány|
|FunctionExecutionUnits|Függvény-végrehajtási egység|Darabszám|Összes|Függvény-végrehajtási egység|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Darabszám|Összes|Függvény végrehajtásainak száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Átlag|Saját [nem megosztható] memória (bájt)|Példány|
|IoReadBytesPerSecond|I/O – olvasás (bájt/másodperc)|Bájt/s|Összes|I/O – olvasás (bájt/másodperc)|Példány|
|IoWriteBytesPerSecond|I/O – írás (bájt/másodperc)|Bájt/s|Összes|I/O – írás (bájt/másodperc)|Példány|
|IoOtherBytesPerSecond|I/O – egyéb (bájt/másodperc)|Bájt/s|Összes|I/O – egyéb (bájt/másodperc)|Példány|
|IoReadOperationsPerSecond|I/O – olvasás (művelet/másodperc)|Bájt/s|Összes|I/O – olvasás (művelet/másodperc)|Példány|
|IoWriteOperationsPerSecond|I/O – írás (művelet/másodperc)|Bájt/s|Összes|I/O – írás (művelet/másodperc)|Példány|
|IoOtherOperationsPerSecond|I/O – egyéb (művelet/másodperc)|Bájt/s|Összes|I/O – egyéb (művelet/másodperc)|Példány|
|RequestsInApplicationQueue|Alkalmazás-várólistán lévő kérelmek|Darabszám|Átlag|Alkalmazás-várólistán lévő kérelmek|Példány|
|CurrentAssemblies|Szerelvények pillanatnyi száma|Darabszám|Átlag|Szerelvények pillanatnyi száma|Példány|
|TotalAppDomains|Alkalmazástartományok összesen|Darabszám|Átlag|Alkalmazástartományok összesen|Példány|
|TotalAppDomainsUnloaded|Memóriából eltávolított alkalmazástartományok összesen|Darabszám|Átlag|Memóriából eltávolított alkalmazástartományok összesen|Példány|
|Gen0Collections|0. generációs szemétgyűjtések száma|Darabszám|Összes|0. generációs szemétgyűjtések száma|Példány|
|Gen1Collections|1. generációs szemétgyűjtések száma|Darabszám|Összes|1. generációs szemétgyűjtések száma|Példány|
|Gen2Collections|2. generációs szemétgyűjtések száma|Darabszám|Összes|2. generációs szemétgyűjtések száma|Példány|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|Processzoridő|másodperc|Összes|Processzoridő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összes|Kérelmek|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Darabszám|Összes|HTTP 101|Példány|
|Http2xx|HTTP 2xx|Darabszám|Összes|HTTP 2xx|Példány|
|Http3xx|HTTP 3xx|Darabszám|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401|Darabszám|Összes|HTTP 401|Példány|
|Http403|HTTP 403|Darabszám|Összes|HTTP 403|Példány|
|Http404|HTTP 404|Darabszám|Összes|HTTP 404|Példány|
|Http406|HTTP 406|Darabszám|Összes|HTTP 406|Példány|
|Http4xx|Http 4xx|Darabszám|Összes|Http 4xx|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Darabszám|Összes|HTTP-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória-munkakészlet|Bájt|Átlag|Memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Átlag|Átlagos memória-munkakészlet|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|FunctionExecutionUnits|Függvény-végrehajtási egység|Darabszám|Összes|Függvény-végrehajtási egység|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Darabszám|Összes|Függvény végrehajtásainak száma|Példány|
|AppConnections|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|Leírók|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
|Szálak|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Átlag|Saját [nem megosztható] memória (bájt)|Példány|
|IoReadBytesPerSecond|I/O – olvasás (bájt/másodperc)|Bájt/s|Összes|I/O – olvasás (bájt/másodperc)|Példány|
|IoWriteBytesPerSecond|I/O – írás (bájt/másodperc)|Bájt/s|Összes|I/O – írás (bájt/másodperc)|Példány|
|IoOtherBytesPerSecond|I/O – egyéb (bájt/másodperc)|Bájt/s|Összes|I/O – egyéb (bájt/másodperc)|Példány|
|IoReadOperationsPerSecond|I/O – olvasás (művelet/másodperc)|Bájt/s|Összes|I/O – olvasás (művelet/másodperc)|Példány|
|IoWriteOperationsPerSecond|I/O – írás (művelet/másodperc)|Bájt/s|Összes|I/O – írás (művelet/másodperc)|Példány|
|IoOtherOperationsPerSecond|I/O – egyéb (művelet/másodperc)|Bájt/s|Összes|I/O – egyéb (művelet/másodperc)|Példány|
|RequestsInApplicationQueue|Alkalmazás-várólistán lévő kérelmek|Darabszám|Átlag|Alkalmazás-várólistán lévő kérelmek|Példány|
|CurrentAssemblies|Szerelvények pillanatnyi száma|Darabszám|Átlag|Szerelvények pillanatnyi száma|Példány|
|TotalAppDomains|Alkalmazástartományok összesen|Darabszám|Átlag|Alkalmazástartományok összesen|Példány|
|TotalAppDomainsUnloaded|Memóriából eltávolított alkalmazástartományok összesen|Darabszám|Átlag|Memóriából eltávolított alkalmazástartományok összesen|Példány|
|Gen0Collections|0. generációs szemétgyűjtések száma|Darabszám|Összes|0. generációs szemétgyűjtések száma|Példány|
|Gen1Collections|1. generációs szemétgyűjtések száma|Darabszám|Összes|1. generációs szemétgyűjtések száma|Példány|
|Gen2Collections|2. generációs szemétgyűjtések száma|Darabszám|Összes|2. generációs szemétgyűjtések száma|Példány|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Darabszám|Összes|Kérelmek|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Darabszám|Összes|HTTP 101|Példány|
|Http2xx|HTTP 2xx|Darabszám|Összes|HTTP 2xx|Példány|
|Http3xx|HTTP 3xx|Darabszám|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401|Darabszám|Összes|HTTP 401|Példány|
|Http403|HTTP 403|Darabszám|Összes|HTTP 403|Példány|
|Http404|HTTP 404|Darabszám|Összes|HTTP 404|Példány|
|Http406|HTTP 406|Darabszám|Összes|HTTP 406|Példány|
|Http4xx|Http 4xx|Darabszám|Összes|Http 4xx|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Darabszám|Összes|HTTP-kiszolgálói hibák|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Százalék|Átlag|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Darabszám|Átlag|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Darabszám|Átlag|HTTP-várólista hossza|Példány|
|ActiveRequests|Aktív kérelmek|Darabszám|Összes|Aktív kérelmek|Példány|
|TotalFrontEnds|Előterek száma|Darabszám|Átlag|Előterek száma|Nincs dimenzió|
|SmallAppServicePlanInstances|Kis méretű App Service-csomag feldolgozói|Darabszám|Átlag|Kis méretű App Service-csomag feldolgozói|Nincs dimenzió|
|MediumAppServicePlanInstances|Közepes méretű App Service-csomag feldolgozói|Darabszám|Átlag|Közepes méretű App Service-csomag feldolgozói|Nincs dimenzió|
|LargeAppServicePlanInstances|Nagy méretű App Service-csomag feldolgozói|Darabszám|Átlag|Nagy méretű App Service-csomag feldolgozói|Nincs dimenzió|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Feldolgozók|Feldolgozók összesen|Darabszám|Átlag|Feldolgozók összesen|Nincs dimenzió|
|WorkersAvailable|Rendelkezésre álló feldolgozók|Darabszám|Átlag|Rendelkezésre álló feldolgozók|Nincs dimenzió|
|Napon használt|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|Nincs dimenzió|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Százalék|Átlag|Memóriahasználat (%)|Példány|

## <a name="next-steps"></a>További lépések
* [További információ a metrikák az Azure monitorban](../../azure-monitor/platform/data-collection.md)
* [Riasztások létrehozása metrikákhoz](../../azure-monitor/platform/alerts-overview.md)
* [A storage, az Event Hubs vagy a Log Analytics metrikák exportálása](../../azure-monitor/platform/diagnostic-logs-overview.md)

