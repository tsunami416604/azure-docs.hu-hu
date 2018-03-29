---
title: Az Azure figyelő metrika - erőforrás típusonkénti támogatott metrikák |} Microsoft Docs
description: Minden erőforrás típusból Azure megfigyelővel metrikák listája.
author: anirudhcavale
manager: ashwink
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: ancav
ms.openlocfilehash: fc7a4062af9e73383af46f0c36eb22710ce9b6e1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure-figyelő támogatott metrikák
Azure figyelő többféleképpen is kommunikál a metrikákat, többek között a diagramkészítési őket a portálon, a hozzájuk férni a REST API-n keresztül, vagy a őket lekérdezése a PowerShell vagy a parancssori felület. Alatt érhető el teljes listáját és az összes metrikák jelenleg Azure figyelő metrika folyamat.

> [!NOTE]
> Más metrikákkal portálon vagy az örökölt API-k használatával érhetők el. Ez a lista csak voltak elérhetők a összevont Azure-figyelő metrika kimenetátirányításának segítségével metrikák tartalmazza. A lekérdezés és eléréséhez metrikák többdimenziósak kérjük, használja a [2018-01-01 api-verzió](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Darabszám|Átlag|QPU. Tartomány 0 – 100 s1, 0-200 S2 és 0 – 400 S4|ServerResourceType|
|memory_metric|Memory (Memória)|Bájt|Átlag|A memória. S1 0-25 GB-ot, S2 0-50 GB-ot és 0 – 100 GB-ot S4|ServerResourceType|
|TotalConnectionRequests|Teljes csatlakozási kérések|Darabszám|Átlag|Teljes csatlakozási kérések. Ezek a érkezők.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenkénti száma|Egység/s|Átlag|Sikeres csatlakozás befejezésekhez aránya.|ServerResourceType|
|TotalConnectionFailures|Teljes kapcsolódási hibák|Darabszám|Átlag|Összes sikertelen csatlakozási kísérletek.|ServerResourceType|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Darabszám|Átlag|Létrehozott felhasználói munkamenetek jelenlegi száma.|ServerResourceType|
|QueryPoolBusyThreads|Lekérdezés készlet foglalt szálak|Darabszám|Átlag|A lekérdezés szálkészlet foglalt szálak számát.|ServerResourceType|
|CommandPoolJobQueueLength|A parancs készlet feladat várólistájának hossza|Darabszám|Átlag|A parancs szálkészlet sorban lévő feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Feldolgozási készlet feladat várólistájának hossza|Darabszám|Átlag|A várólistában a feldolgozási szálkészletben lévő nem-I/O-feladatok száma.|ServerResourceType|
|CurrentConnections|Kapcsolat: A jelenlegi kapcsolatok száma|Darabszám|Átlag|Ügyfél fennálló kapcsolatok aktuális száma.|ServerResourceType|
|CleanerCurrentPrice|Memória: Tisztító aktuális árlista|Darabszám|Átlag|Aktuális árlista memória $/ bájt/idő normalizálva 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: Zsugoríthatósági tisztító memória|Bájt|Átlag|Memória mérete, a háttérben tisztító kiürítése függvényében mennyisége.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Tisztító memória nonshrinkable|Bájt|Átlag|Memória mérete, nem vonatkoznak a háttérben tisztító kiürítése mennyisége.|ServerResourceType|
|MemoryUsage|Memória: Memória használata|Bájt|Átlag|A kiszolgálói folyamat, tisztító memória ár kiszámításakor felhasznált memória mennyisége Process\PrivateBytes plus memórialeképezési adatok figyelmen kívül hagyása minden leképezve, vagy a xVelocity memórián belüli analytics motor (VertiPaq) meghaladja a memóriakorlátot xVelocity motor által lefoglalt memória méretét a teljesítményszámláló értéke.|ServerResourceType|
|MemoryLimitHard|Memória: Memóriakorlátot rögzített|Bájt|Átlag|Rögzített memóriakorlátot, konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Memória: Memória korlátozása magas|Bájt|Átlag|Magas memóriakorlátot, konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Memória: Memória korlátja alacsony|Bájt|Átlag|Kevés a memória a határt, a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: Memória korlátja VertiPaq|Bájt|Átlag|A memória maximális mennyiségét, a konfigurációs fájlból.|ServerResourceType|
|Kvóta|Memória: kvóta|Bájt|Átlag|Aktuális memóriakvótáját, bájtban. Memóriakvótáját memória grant vagy memória foglalás is nevezik.|ServerResourceType|
|QuotaBlocked|Memória: Kvóta letiltva|Darabszám|Átlag|Le vannak tiltva, amíg más memória kvóták felszabadítását kvóta kérelmek jelenlegi száma.|ServerResourceType|
|VertiPaqNonpaged|Memória: a nem lapozható VertiPaq|Bájt|Átlag|A memórián belüli motor általi használatra munkakészletében bájt memóriát zárolva van.|ServerResourceType|
|VertiPaqPaged|Memória: A VertiPaq lapozható|Bájt|Átlag|A memóriában levő használt lapozható memória bájt.|ServerResourceType|
|RowsReadPerSec|Feldolgozási: Sor olvasása másodpercenkénti száma|Egység/s|Átlag|Sorok aránya az összes relációs adatbázisok olvasni.|ServerResourceType|
|RowsConvertedPerSec|Feldolgozási: Sorok konvertálni másodpercenkénti száma|Egység/s|Átlag|Sorok aránya alakítja a feldolgozás során.|ServerResourceType|
|RowsWrittenPerSec|Feldolgozási: Másodpercenként írt sorok|Egység/s|Átlag|Feldolgozás során írt sorok száma.|ServerResourceType|
|CommandPoolBusyThreads|Szálak: Parancs készlet foglalt szálak|Darabszám|Átlag|A parancs szálkészlet foglalt szálak számát.|ServerResourceType|
|CommandPoolIdleThreads|Szálak: Parancs készlet üresjárati szálak|Darabszám|Átlag|A parancs szálkészlet üresjárati szálak számát.|ServerResourceType|
|LongParsingBusyThreads|Szálak: Hosszú foglalt szálak elemzése|Darabszám|Átlag|A hosszú elemzési szálkészlet foglalt szálak számát.|ServerResourceType|
|LongParsingIdleThreads|Szálak: Hosszú üresjárati szálak elemzése|Darabszám|Átlag|A hosszú elemzési szálkészlet üresjárati szálak számát.|ServerResourceType|
|LongParsingJobQueueLength|Szálak: Hosszú elemzése feladat várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szálkészlet sorban lévő feladatok száma.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Szálak: Készlet foglalt i/o-feladat szálak feldolgozása|Darabszám|Átlag|I/o-feladatok futtatása a feldolgozási szálkészletben lévő szálak száma.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Szálak: Készlet foglalt nem-I/O-szálak feldolgozása|Darabszám|Átlag|Nem-I/O-feladatok futtatása a feldolgozási szálkészletben lévő szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Szálak: Feldolgozása készlet i/o-feldolgozás várólistájának hossza|Darabszám|Átlag|A várólistában a feldolgozási szálkészletben lévő i/o-feladatok száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Szálak: Készlet üresjárati i/o-feladat szálak feldolgozása|Darabszám|Átlag|A feldolgozási szálkészletben lévő i/o-feladatok üresjárati szálak száma.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Szálak: Készlet üresjárati nem-I/O-szálak feldolgozása|Darabszám|Átlag|Nem-I/O-feladatok hozzárendelve a feldolgozási szálkészletben üresjárati szálak számát.|ServerResourceType|
|QueryPoolIdleThreads|Szálak: Lekérdezés készlet üresjárati szálak|Darabszám|Átlag|A feldolgozási szálkészletben lévő i/o-feladatok üresjárati szálak száma.|ServerResourceType|
|QueryPoolJobQueueLength|Szálak: Lekérdezés készlet feladat várólista lengt|Darabszám|Átlag|A lekérdezés szálkészlet sorban lévő feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak: Rövid foglalt szálak elemzése|Darabszám|Átlag|A rövid elemzési szálkészlet foglalt szálak számát.|ServerResourceType|
|ShortParsingIdleThreads|Szálak: Rövid üresjárati szálak elemzése|Darabszám|Átlag|A rövid elemzési szálkészlet üresjárati szálak számát.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak: Rövid elemzése feladat várólistájának hossza|Darabszám|Átlag|A rövid elemzési szálkészlet sorban lévő feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memória Akadozások|Százalék|Átlag|Átlagos memória thrashing.|ServerResourceType|
|mashup_engine_qpu_metric|M-motor QPU|Darabszám|Átlag|Adategyesítési motor folyamatok QPU kihasználtsága|ServerResourceType|
|mashup_engine_memory_metric|M-motor memória|Bájt|Átlag|Adategyesítési motor folyamatok memóriahasználata|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Teljes átjáró kérelmek|Darabszám|Összes|Átjáró kérelmek száma|Location, Hostname|
|SuccessfulRequests|Átjáró sikeres kérelmek|Darabszám|Összes|Átjáró sikeres kérelmek száma|Location, Hostname|
|UnauthorizedRequests|Jogosulatlan átjáró kérelmek|Darabszám|Összes|Jogosulatlan átjáró kérelmek száma|Location, Hostname|
|FailedRequests|Hibás átjáró kérelmek|Darabszám|Összes|Az átjáró kérelmekben hibák száma|Location, Hostname|
|OtherRequests|Más átjáró kérelmek|Darabszám|Összes|Más átjáró kérelmek száma|Location, Hostname|
|Időtartam|Átjáró kérelmek teljes időtartama|Ezredmásodperc|Átlag|Teljes időtartam az átjáró kérelmek ezredmásodpercben|Location, Hostname|
|Kapacitás|Kapacitás (előzetes verzió)|Százalék|Maximum|Kihasználtság metrika ApiManagement szolgáltatás|Hely|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok teljes száma|Darabszám|Összes|A feladatok teljes száma|RunbookName, állapota|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált magok száma|Darabszám|Összes|A batch-fiók dedikált magok száma összesen|Nincs dimenzió|
|TotalNodeCount|Dedikált csomópontok száma|Darabszám|Összes|A batch-fiók dedikált csomópontja teljes száma|Nincs dimenzió|
|LowPriorityCoreCount|LowPriority magok száma|Darabszám|Összes|A batch-fiókhoz az alacsony prioritású magok száma összesen|Nincs dimenzió|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Darabszám|Összes|Alacsony prioritású csomópontok a batch-fiók teljes száma|Nincs dimenzió|
|CreatingNodeCount|Csomópontok száma létrehozása|Darabszám|Összes|A létrehozandó csomópontok száma|Nincs dimenzió|
|StartingNodeCount|Kezdő csomópontok száma|Darabszám|Összes|Kezdési csomópontok száma|Nincs dimenzió|
|WaitingForStartTaskNodeCount|Várakozás a kezdő tevékenység csomópontok száma|Darabszám|Összes|Várakozás a feladat indítása befejezéséhez a csomópontok száma|Nincs dimenzió|
|StartTaskFailedNodeCount|Kezdő tevékenység nem sikerült a csomópontok száma|Darabszám|Összes|Ha a feladat indítása nem csomópontok száma|Nincs dimenzió|
|IdleNodeCount|Üresjárati csomópontok száma|Darabszám|Összes|Üresjárati csomópontok száma|Nincs dimenzió|
|OfflineNodeCount|Kapcsolat nélküli csomópontok száma|Darabszám|Összes|Kapcsolat nélküli csomópontok száma|Nincs dimenzió|
|RebootingNodeCount|Rendszer újraindítása a csomópontok száma|Darabszám|Összes|Újraindítás a csomópontok száma|Nincs dimenzió|
|ReimagingNodeCount|Különösen csomópontok száma|Darabszám|Összes|Különösen csomópontok száma|Nincs dimenzió|
|RunningNodeCount|Futó csomópontok száma|Darabszám|Összes|Futó csomópontok száma|Nincs dimenzió|
|LeavingPoolNodeCount|Kilépés a készlet csomópontok száma|Darabszám|Összes|A készlet elhagyása csomópontok száma|Nincs dimenzió|
|UnusableNodeCount|Használhatatlanná csomópontok száma|Darabszám|Összes|Használhatatlanná csomópontok száma|Nincs dimenzió|
|PreemptedNodeCount|Csomópont Count részesítés|Darabszám|Összes|A leállított csomópontok száma|Nincs dimenzió|
|TaskStartEvent|A feladat kezdési események|Darabszám|Összes|Kezdték feladatok száma összesen|Nincs dimenzió|
|TaskCompleteEvent|A feladat befejezése események|Darabszám|Összes|A befejezett feladatok száma összesen|Nincs dimenzió|
|TaskFailEvent|A feladat sikertelen események|Darabszám|Összes|Hibás állapotban befejeződött feladatok száma összesen|Nincs dimenzió|
|PoolCreateEvent|Alkalmazáskészlet létrehozása események|Darabszám|Összes|Létrehozott gyűjtők száma összesen|Nincs dimenzió|
|PoolResizeStartEvent|Készlet átméretezési Start események|Darabszám|Összes|Készlet átméretezi, amely kezdték száma összesen|Nincs dimenzió|
|PoolResizeCompleteEvent|Készlet átméretezési teljes események|Darabszám|Összes|Készlet átméretezi elvégző száma összesen|Nincs dimenzió|
|PoolDeleteStartEvent|Kezdő események készlet törlése|Darabszám|Összes|Teljes száma, amelyek kezdték készlet törlése|Nincs dimenzió|
|PoolDeleteCompleteEvent|Alkalmazáskészlet teljes események törlése|Darabszám|Összes|A befejezett készlet törlése száma összesen|Nincs dimenzió|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakoztatott ügyfélszámítógépek|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed|Műveletek összesen|Darabszám|Összes||Nincs dimenzió|
|cachehits|Gyorsítótárbeli találatok|Darabszám|Összes||Nincs dimenzió|
|cachemisses|Gyorsítótárbeli tévesztések|Darabszám|Összes||Nincs dimenzió|
|getcommands|Lekérdezések|Darabszám|Összes||Nincs dimenzió|
|setcommands|Beállítások|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond|Műveletek száma másodpercenként|Darabszám|Összes||Nincs dimenzió|
|evictedkeys|Kizárt kulcsok|Darabszám|Összes||Nincs dimenzió|
|totalkeys|Kulcsok összesen|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys|Lejárt kulcsok|Darabszám|Összes||Nincs dimenzió|
|usedmemory|Felhasznált memória|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss|Használt memória RSS|Bájt|Maximum||Nincs dimenzió|
|serverLoad|Kiszolgáló-terhelés|Százalék|Maximum||Nincs dimenzió|
|cacheWrite|Gyorsítótár-írás|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead|Gyorsítótár-olvasás|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime|CPU|Százalék|Maximum||Nincs dimenzió|
|connectedclients0|Csatlakozott ügyfelek (szilánkok 0)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed0|Összes művelet (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|cachehits0|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|cachemisses0|A gyorsítótárbeli (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|getcommands0|Lekérdezi a (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|setcommands0|Beállítása (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond0|Műveletek száma másodpercenként (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys0|Eltávolított kulcsok (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|totalkeys0|Összes kulcs (szilánkok 0)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys0|Lejárt kulcsokkal (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|usedmemory0|Használt memória (szilánkok 0)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss0|Használt memória RSS (szilánkok 0)|Bájt|Maximum||Nincs dimenzió|
|serverLoad0|Kiszolgáló terhelését (szilánkok 0)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite0|Gyorsítótár-írási (szilánkok 0)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead0|Gyorsítótár olvasható (szilánkok 0)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime0|Processzor (szilánkok 0)|Százalék|Maximum||Nincs dimenzió|
|connectedclients1|Csatlakozott ügyfelek (szilánkok 1)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed1|Összes művelet (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|cachehits1|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|cachemisses1|A gyorsítótárbeli (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|getcommands1|(Szilánkok 1) beolvasása|Darabszám|Összes||Nincs dimenzió|
|setcommands1|(1 a Shard) beállítása|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond1|Műveletek száma másodpercenként (1 a Shard)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys1|Eltávolított kulcsok (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|totalkeys1|Összes kulcs (szilánkok 1)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys1|Lejárt kulcsokkal (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|usedmemory1|Használt memória (szilánkok 1)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss1|Használt memória RSS (szilánkok 1)|Bájt|Maximum||Nincs dimenzió|
|serverLoad1|Kiszolgáló terhelését (szilánkok 1)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite1|Gyorsítótár-írási (szilánkok 1)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead1|Gyorsítótár olvasható (szilánkok 1)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime1|Processzor (szilánkok 1)|Százalék|Maximum||Nincs dimenzió|
|connectedclients2|Csatlakozott ügyfelek (szilánkok 2)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed2|Összes művelet (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|cachehits2|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|cachemisses2|Cache Misses (Shard 2)|Darabszám|Összes||Nincs dimenzió|
|getcommands2|Lekérdezi a (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|setcommands2|Beállítása (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond2|Műveletek száma másodpercenként (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys2|Eltávolított kulcsok (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|totalkeys2|Összes kulcs (szilánkok 2)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys2|Lejárt kulcsokkal (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|usedmemory2|Használt memória (szilánkok 2)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss2|Használt memória RSS (szilánkok 2)|Bájt|Maximum||Nincs dimenzió|
|serverLoad2|Kiszolgáló terhelését (szilánkok 2)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite2|Gyorsítótár-írási (szilánkok 2)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead2|Gyorsítótár olvasható (szilánkok 2)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime2|Processzor (szilánkok 2)|Százalék|Maximum||Nincs dimenzió|
|connectedclients3|Csatlakozott ügyfelek (szilánkok 3)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed3|Összes művelet (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|cachehits3|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|cachemisses3|A gyorsítótárbeli (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|getcommands3|Lekérdezi a (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|setcommands3|(3 szilánkok) beállítása|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond3|Műveletek száma másodpercenként (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys3|Eltávolított kulcsok (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|totalkeys3|Összes kulcs (szilánkok 3)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys3|Lejárt kulcsokkal (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|usedmemory3|Használt memória (szilánkok 3)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss3|Használt memória RSS (szilánkok 3)|Bájt|Maximum||Nincs dimenzió|
|serverLoad3|Kiszolgáló terhelését (szilánkok 3)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite3|Gyorsítótár-írási (szilánkok 3)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead3|Gyorsítótár olvasható (szilánkok 3)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime3|Processzor (szilánkok 3)|Százalék|Maximum||Nincs dimenzió|
|connectedclients4|Csatlakozott ügyfelek (szilánkok 4)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed4|Összes művelet (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|cachehits4|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|cachemisses4|A gyorsítótárbeli (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|getcommands4|Lekérdezi a (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|setcommands4|(4 szilánkok) beállítása|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond4|Műveletek száma másodpercenként (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys4|Eltávolított kulcsok (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|totalkeys4|Összes kulcs (szilánkok 4)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys4|Lejárt kulcsokkal (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|usedmemory4|Használt memória (szilánkok 4)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss4|Használt memória RSS (szilánkok 4)|Bájt|Maximum||Nincs dimenzió|
|serverLoad4|Kiszolgáló terhelését (szilánkok 4)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite4|Gyorsítótár-írási (szilánkok 4)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead4|Gyorsítótár olvasható (szilánkok 4)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime4|Processzor (szilánkok 4)|Százalék|Maximum||Nincs dimenzió|
|connectedclients5|Csatlakozott ügyfelek (szilánkok 5)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed5|Összes művelet (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|cachehits5|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|cachemisses5|A gyorsítótárbeli (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|getcommands5|Lekérdezi a (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|setcommands5|Beállítása (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond5|Műveletek száma másodpercenként (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys5|Eltávolított kulcsok (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|totalkeys5|Összes kulcs (szilánkok 5)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys5|Lejárt kulcsokkal (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|usedmemory5|Használt memória (szilánkok 5)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss5|Használt memória RSS (szilánkok 5)|Bájt|Maximum||Nincs dimenzió|
|serverLoad5|Kiszolgáló terhelését (szilánkok 5)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite5|Gyorsítótár-írási (szilánkok 5)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead5|Gyorsítótár olvasható (szilánkok 5)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime5|Processzor (szilánkok 5)|Százalék|Maximum||Nincs dimenzió|
|connectedclients6|Csatlakozott ügyfelek (szilánkok 6)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed6|Összes művelet (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|cachehits6|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|cachemisses6|A gyorsítótárbeli (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|getcommands6|Lekérdezi a (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|setcommands6|Beállítása (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond6|Műveletek száma másodpercenként (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys6|Eltávolított kulcsok (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|totalkeys6|Összes kulcs (szilánkok 6)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys6|Lejárt kulcsokkal (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|usedmemory6|Használt memória (szilánkok 6)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss6|RSS (szilánkok 6) a használt memória|Bájt|Maximum||Nincs dimenzió|
|serverLoad6|Kiszolgáló terhelését (szilánkok 6)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite6|Gyorsítótár-írási (szilánkok 6)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead6|Gyorsítótár olvasható (szilánkok 6)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime6|Processzor (szilánkok 6)|Százalék|Maximum||Nincs dimenzió|
|connectedclients7|Csatlakozott ügyfelek (szilánkok 7)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed7|Összes művelet (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|cachehits7|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|cachemisses7|A gyorsítótárbeli (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|getcommands7|Lekérdezi a (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|setcommands7|Beállítása (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond7|Műveletek száma másodpercenként (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys7|Eltávolított kulcsok (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|totalkeys7|Összes kulcs (szilánkok 7)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys7|Lejárt kulcsokkal (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|usedmemory7|Használt memória (szilánkok 7)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss7|Használt memória RSS (szilánkok 7)|Bájt|Maximum||Nincs dimenzió|
|serverLoad7|Kiszolgáló terhelését (szilánkok 7)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite7|Gyorsítótár-írási (szilánkok 7)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead7|Gyorsítótár olvasható (szilánkok 7)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime7|Processzor (szilánkok 7)|Százalék|Maximum||Nincs dimenzió|
|connectedclients8|Csatlakozott ügyfelek (szilánkok 8)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed8|Összes művelet (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|cachehits8|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|cachemisses8|A gyorsítótárbeli (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|getcommands8|Lekérdezi a (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|setcommands8|Beállítása (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond8|Műveletek száma másodpercenként (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys8|Eltávolított kulcsok (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|totalkeys8|Összes kulcs (szilánkok 8)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys8|Lejárt kulcsokkal (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|usedmemory8|Használt memória (szilánkok 8)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss8|Használt memória RSS (szilánkok 8)|Bájt|Maximum||Nincs dimenzió|
|serverLoad8|Kiszolgáló terhelését (szilánkok 8)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite8|Gyorsítótár-írási (szilánkok 8)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead8|Gyorsítótár olvasható (szilánkok 8)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime8|Processzor (szilánkok 8)|Százalék|Maximum||Nincs dimenzió|
|connectedclients9|Csatlakozott ügyfelek (szilánkok 9)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed9|Összes művelet (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|cachehits9|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|cachemisses9|A gyorsítótárbeli (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|getcommands9|Lekérdezi a (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|setcommands9|Beállítása (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|operationsPerSecond9|Műveletek száma másodpercenként (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys9|Eltávolított kulcsok (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|totalkeys9|Összes kulcs (szilánkok 9)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys9|Lejárt kulcsokkal (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|usedmemory9|Használt memória (szilánkok 9)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss9|Használt memória RSS (szilánkok 9)|Bájt|Maximum||Nincs dimenzió|
|serverLoad9|Kiszolgáló terhelését (szilánkok 9)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite9|Gyorsítótár-írási (szilánkok 9)|Bájt/s|Maximum||Nincs dimenzió|
|cacheRead9|Gyorsítótár olvasható (szilánkok 9)|Bájt/s|Maximum||Nincs dimenzió|
|percentProcessorTime9|Processzor (szilánkok 9)|Százalék|Maximum||Nincs dimenzió|

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
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A virtuális gép(ek) által az összes hálózati adapteren elküldött bájtok száma (kimenő forgalom).|Nincs dimenzió|
|Lemezolvasási sebesség (bájt/s)|Lemezolvasás|Bájt/s|Átlag|A monitorozási időszakban lemezről beolvasott bájtok átlagos száma.|Nincs dimenzió|
|Lemezírási sebesség (bájt/s)|Lemezírás|Bájt/s|Átlag|A monitorozási időszakban lemezre írt bájtok átlagos száma.|Nincs dimenzió|
|Lemezolvasási művelet/s|Lemezolvasási művelet/s|Egység/s|Átlag|Lemezolvasási I/O-műveletek.|Nincs dimenzió|
|Lemezre írási művelet/s|Lemezre írási művelet/s|Egység/s|Átlag|Lemezre írási I/O-műveletek.|Nincs dimenzió|

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
|Késés|Késés|MilliSeconds|Átlag|A késés másodpercben.|Nincs dimenzió|
|CharactersTranslated|Lefordított karakterek|Darabszám|Összes|A bejövő szöveges kérelem karakterszáma.|Nincs dimenzió|
|SpeechSessionDuration|Beszédfelismerési munkamenet időtartama|másodperc|Összes|A beszédfelismerési munkamenet teljes időtartama.|Nincs dimenzió|
|TotalTransactions|Teljes tranzakció|Darabszám|Összes|Tranzakciók száma összesen|Nincs dimenzió|

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

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|CPU-használat|Darabszám|Átlag|CPU-használat a millicores az összes mag.|containerName|
|MemoryUsage|Memóriahasználat|Bájt|Átlag|Teljes memóriahasználatát bájt.|containerName|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DCIApiCalls|Ügyfél Insights API-hívások|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationSuccessfulLines|Leképezési importálási művelet sikeres sorok|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationFailedLines|Sorok leképezése az importálási művelet sikertelen volt.|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationTotalLines|Leképezési importálási művelet teljes sorok|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationRuntimeInSeconds|Leképezési importálási művelet futásidejű másodpercben.|másodperc|Összes||Nincs dimenzió|
|DCIOutboundProfileExportSucceeded|Kimenő profil exportálása sikeres volt.|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundProfileExportFailed|Nem sikerült kimenő profil exportálása|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundProfileExportDuration|Kimenő profil exportálása időtartama|másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiExportSucceeded|Kimenő Kpi exportálása sikeres volt.|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundKpiExportFailed|Kimenő Kpi az exportálás sikertelen.|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundKpiExportDuration|Kimenő Kpi exportálási időtartama|másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiExportStarted|Kimenő Kpi Exportálás indítása|másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiRecordCount|Kimenő Kpi rekordszám|másodperc|Összes||Nincs dimenzió|
|DCIOutboundProfileExportCount|Kimenő profil exportálása száma|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportFailed|Nem sikerült kimenő kezdeti profil exportálása|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportSucceeded|Kimenő kezdeti profil exportálása sikeres volt.|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialKpiExportFailed|Kimenő kezdeti Kpi az exportálás sikertelen.|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialKpiExportSucceeded|Kimenő kezdeti Kpi exportálása sikerült|másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportDurationInSeconds|Kimenő kezdeti profil exportálása időtartama másodpercben|másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiFailed|Nem sikerült másodpercben szabványos Kpi Adla feladata|másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiTimeOut|Adla feladat szabványos Kpi időkorlát másodpercben|másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiCompleted|Standard Kpi másodperc alatt befejeződött Adla feladata|másodperc|Összes||Nincs dimenzió|
|ImportASAValuesFailed|Importálás ASA értékek száma nem sikerült.|Darabszám|Összes||Nincs dimenzió|
|ImportASAValuesSucceeded|Importálás ASA értékek száma sikeres volt.|Darabszám|Összes||Nincs dimenzió|
|DCIProfilesCount|Profil példányok száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIInteractionsPerMonthCount|Kapcsolati hónap száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIKpisCount|KPI száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCISegmentsCount|Szegmens száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIPredictiveMatchPoliciesCount|A prediktív egyezés száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIPredictionsCount|Előrejelzés száma|Darabszám|Vezetéknév||Nincs dimenzió|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Sikertelen futtatások|Darabszám|Összes||pipelineName, activityName, windowEnd, windowStart |
|SuccessfulRuns|Sikeres futtatások|Darabszám|Összes||pipelineName, activityName, windowEnd, windowStart |

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Nem sikerült a folyamat fut metrikák|Darabszám|Összes||FailureType, neve|
|PipelineSucceededRuns|Sikerült a folyamat fut metrikák|Darabszám|Összes||FailureType, neve|
|ActivityFailedRuns|Nem sikerült a tevékenységi futtatása metrikák|Darabszám|Összes||Az ActivityType, PipelineName, FailureType, neve|
|ActivitySucceededRuns|Sikeres volt a tevékenység futtatása metrikák|Darabszám|Összes||Az ActivityType, PipelineName, FailureType, neve|
|TriggerFailedRuns|Nem sikerült az eseményindító futtatása metrikák|Darabszám|Összes||Name, FailureType|
|TriggerSucceededRuns|Sikeres volt a trigger futtatása metrikák|Darabszám|Összes||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Integrációs futásidejű CPU-használat|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableMemory|Integrációs futásidejű rendelkezésre álló memória|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Darabszám|Összes|Sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedFailure|A sikertelen feladatok|Darabszám|Összes|A sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedCancelled|Megszakított feladatok|Darabszám|Összes|Megszakított feladatok száma.|Nincs dimenzió|
|JobAUEndedSuccess|Sikeres AU idő|másodperc|Összes|Sikertelen feladatok teljes AU ideje.|Nincs dimenzió|
|JobAUEndedFailure|Nem sikerült AU idő|másodperc|Összes|A sikertelen feladatok teljes AU ideje.|Nincs dimenzió|
|JobAUEndedCancelled|Megszakított AU idő|másodperc|Összes|Megszakított feladatok teljes AU ideje.|Nincs dimenzió|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Teljes tárterület|Bájt|Maximum|A fiókban tárolt adatok teljes mennyisége.|Nincs dimenzió|
|DataWritten|Kiírt adatok|Bájt|Összes|Teljes adatmennyiség ír a fiókba.|Nincs dimenzió|
|DataRead|Olvasott adatok|Bájt|Összes|Az adatok olvasása-fiókból.|Nincs dimenzió|
|WriteRequests|Írási kérelmeket szolgál|Darabszám|Összes|Adatok száma írási kérelmeket szolgál ahhoz a fiókhoz.|Nincs dimenzió|
|ReadRequests|Olvasási kérések|Darabszám|Összes|Adatok száma az olvasási kérésekre ahhoz a fiókhoz.|Nincs dimenzió|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzor százalékban|Százalék|Átlag|Processzor százalékban|Nincs dimenzió|
|compute_limit|Számítási egység korlátot|Darabszám|Átlag|Számítási egység korlátot|Nincs dimenzió|
|compute_consumption_percent|Számítási egység százalékos aránya|Százalék|Átlag|Számítási egység százalékos aránya|Nincs dimenzió|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százaléka|Százalék|Átlag|IO-százaléka|Nincs dimenzió|
|storage_percent|Tárolási százalékos aránya|Százalék|Átlag|Tárolási százalékos aránya|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Átlag|Tárolási kapacitása|Nincs dimenzió|
|active_connections|Aktív kapcsolatok száma|Darabszám|Átlag|Aktív kapcsolatok száma|Nincs dimenzió|
|connections_failed|Nem sikerült kapcsolatok száma|Darabszám|Átlag|Nem sikerült kapcsolatok száma|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzor százalékban|Százalék|Átlag|Processzor százalékban|Nincs dimenzió|
|compute_limit|Számítási egység korlátot|Darabszám|Átlag|Számítási egység korlátot|Nincs dimenzió|
|compute_consumption_percent|Számítási egység százalékos aránya|Százalék|Átlag|Számítási egység százalékos aránya|Nincs dimenzió|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százaléka|Százalék|Átlag|IO-százaléka|Nincs dimenzió|
|storage_percent|Tárolási százalékos aránya|Százalék|Átlag|Tárolási százalékos aránya|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Átlag|Tárolási kapacitása|Nincs dimenzió|
|active_connections|Aktív kapcsolatok száma|Darabszám|Átlag|Aktív kapcsolatok száma|Nincs dimenzió|
|connections_failed|Nem sikerült kapcsolatok száma|Darabszám|Átlag|Nem sikerült kapcsolatok száma|Nincs dimenzió|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetria üzenet küldési kísérlet|Darabszám|Összes|Az IoT hub küldendő próbált eszköz-felhő telemetriai üzenetek száma|Nincs dimenzió|
|d2c.telemetry.ingress.success|Küldött telemetriai üzenetek|Darabszám|Összes|Sikerült elküldeni az IoT hub eszköz-felhő telemetriai üzenetek száma|Nincs dimenzió|
|c2d.commands.egress.complete.success|A parancsok befejeződött|Darabszám|Összes|Az eszköz által végrehajtott felhő eszközre parancsok száma|Nincs dimenzió|
|c2d.commands.egress.abandon.success|Elhagyott parancsok|Darabszám|Összes|Az eszköz által félbe hagyott felhő eszközre parancsok száma|Nincs dimenzió|
|c2d.commands.egress.reject.success|Elutasított parancsok|Darabszám|Összes|Az eszköz által elutasított felhő eszközre parancsok száma|Nincs dimenzió|
|devices.totalDevices|Eszközök teljes száma|Darabszám|Összes|Az IoT hub regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök|Darabszám|Összes|Az IoT hub csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.success|Telemetria kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt végpontok (összesen)|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Az eldobott üzenetek|Darabszám|Összes|Dobva, mert a kézbesítési végpont lett elhalt üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.orphaned|Árva üzenetek|Darabszám|Összes|A nem megfelelő bármely útvonalakat, beleértve a tartalék útvonal üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.invalid|Érvénytelen üzenet|Darabszám|Összes|A végpont alkalmazással való inkompatibilitás miatt nem kézbesített üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Tartalék feltételnek megfelelő üzenetek|Darabszám|Összes|A tartalék végpont üzenetek száma|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Az Event Hubs végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt az Event Hubs végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Az Event Hubs végpontok üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között az Eseményközpont végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Service Bus-üzenetsorba végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-üzenetsorba végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|A Service Bus-üzenetsorba végpontokhoz üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-üzenetsorba végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Service Bus-témakörbe végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-témakörbe végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|A Service Bus-témakörbe végpontokhoz üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-témakörbe végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|A beépített végpont (üzenet/események) kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt a beépített végpont (üzenet/esemény)|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Üzenet várakozási ideje a beépített végpont (üzenet/esemény)|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között történő (üzenet/esemény), beépített végpont ezredmásodpercben |Nincs dimenzió|
|d2c.endpoints.egress.storage|Tárolási végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt tárolási végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.storage|Üzenet késés tárolási végpontok|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a tárolási végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.storage.bytes|Adatok tárhelyen|Bájt|Összes|Adatok tárolási végpontok írt bájtok száma|Nincs dimenzió|
|d2c.endpoints.egress.storage.blobs|Blobok tárhelyen|Darabszám|Összes|BLOB storage végpontok írt száma|Nincs dimenzió|
|d2c.twin.read.success|Sikeres iker olvassa be az eszközökön|Darabszám|Összes|Az összes sikeres a két eszköz által kezdeményezett olvasások száma.|Nincs dimenzió|
|d2c.twin.read.failure|Nem sikerült a kettős olvasások eszközökről|Darabszám|Összes|Teljes számát a két eszköz által kezdeményezett olvasás sikertelen volt.|Nincs dimenzió|
|d2c.twin.read.size|A két válasz mérete olvassa be az eszközökön|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres iker olvasási műveletek.|Nincs dimenzió|
|d2c.twin.update.success|Sikeres iker frissítések eszközökről|Darabszám|Összes|A két eszköz által kezdeményezett összes sikeres frissítések száma.|Nincs dimenzió|
|d2c.twin.update.failure|Nem sikerült a két frissítések eszközökről|Darabszám|Összes|A teljes számát a két eszköz által kezdeményezett frissítések nem sikerült.|Nincs dimenzió|
|d2c.twin.update.size|A két frissítések eszközökről mérete|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres iker frissítések.|Nincs dimenzió|
|c2d.methods.success|Sikeres közvetlen módszer meghívásához|Darabszám|Összes|Az összes sikeres közvetlen módszer hívások száma.|Nincs dimenzió|
|c2d.methods.failure|Nem sikerült a közvetlen módszer meghívásához|Darabszám|Összes|A teljes számát a közvetlen metódushívások nem sikerült.|Nincs dimenzió|
|c2d.methods.requestSize|A közvetlen módszer meghívásához kérelem mérete|Bájt|Átlag|Az átlagos, a minimális és a közvetlen módszer minden sikeres kérelmek maximális.|Nincs dimenzió|
|c2d.methods.responseSize|Válasz mérete közvetlen módszer meghívásához|Bájt|Átlag|Az átlagos, a minimális és a maximális minden sikeres közvetlen módszer válaszok.|Nincs dimenzió|
|c2d.twin.read.success|Sikeres iker háttérből olvassa be|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett iker olvasási műveletek számát.|Nincs dimenzió|
|c2d.twin.read.failure|Sikertelen a két háttérből olvassa be|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett olvasások nem sikerült.|Nincs dimenzió|
|c2d.twin.read.size|A válasz méretének iker olvasások háttérből|Bájt|Átlag|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett iker olvasási műveletek.|Nincs dimenzió|
|c2d.twin.update.success|Sikeres iker frissítések háttérből|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett iker frissítések száma.|Nincs dimenzió|
|c2d.twin.update.failure|Sikertelen a két frissítések háttérből|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett frissítések nem sikerült.|Nincs dimenzió|
|c2d.twin.update.size|A két frissítések háttérből mérete|Bájt|Átlag|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett iker frissítések.|Nincs dimenzió|
|twinQueries.success|Sikeres iker lekérdezések|Darabszám|Összes|Minden sikeres iker lekérdezések száma.|Nincs dimenzió|
|twinQueries.failure|Sikertelen a két lekérdezések|Darabszám|Összes|Összes sikertelen iker lekérdezések száma.|Nincs dimenzió|
|twinQueries.resultSize|A két lekérdezések eredmény méretének|Bájt|Átlag|Az átlagos, a minimális és a maximális az eredmény méretének minden sikeres iker lekérdezések.|Nincs dimenzió|
|jobs.createTwinUpdateJob.success|A sikeres és a két frissítési feladatok|Darabszám|Összes|Minden sikeres létrehozása két frissítési feladatok számát.|Nincs dimenzió|
|jobs.createTwinUpdateJob.failure|Sikertelen és a két frissítési feladatok|Darabszám|Összes|A két frissítési feladatok során létrehozására tett sikertelen kísérlet száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.success|A metódus meghívása feladatok sikeres létrehozása|Darabszám|Összes|Minden sikeres létrehozása közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.failure|A metódus meghívása feladatok sikertelen számát|Darabszám|Összes|A közvetlen metódus meghívása feladatok során létrehozására tett sikertelen kísérlet száma.|Nincs dimenzió|
|jobs.listJobs.success|Lista feladatok sikeres hívások|Darabszám|Összes|Az összes sikeres indított hívások száma a lista feladatok.|Nincs dimenzió|
|jobs.listJobs.failure|Nem sikerült a lista feladatok hívások|Darabszám|Összes|A feladatok lista összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.cancelJob.success|Megszakított feladatok sikeres|Darabszám|Összes|Az összes sikeres indított hívások száma megszakítja a feladatot.|Nincs dimenzió|
|jobs.cancelJob.failure|Sikertelen feladat sikertelen|Darabszám|Összes|A visszavonni a feladatot az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.queryJobs.success|Feladat sikeres lekérdezések|Darabszám|Összes|A lekérdezés feladatok minden sikeres hívások száma.|Nincs dimenzió|
|jobs.queryJobs.failure|Sikertelen feladat-lekérdezések|Darabszám|Összes|A lekérdezés feladatok összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.completed|Befejezett feladatok|Darabszám|Összes|Az összes befejezett feladatok száma.|Nincs dimenzió|
|jobs.failed|A sikertelen feladatok|Darabszám|Összes|Összes sikertelen feladatok száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Sávszélesség-szabályozási hibák száma|Darabszám|Összes|Azelőtt gyorsítja fel az eszköz átviteli miatt szabályozás hibák száma|Nincs dimenzió|
|dailyMessageQuotaUsed|Használt üzenetek teljes száma|Darabszám|Átlag|Jelenleg használt teljes üzenetek száma. Ez az összesített érték, amely nulla értékre állítását: 00:00 UTC minden nap.|Nincs dimenzió|
|deviceDataUsage|Teljes devicedata kihasználtsága|Darabszám|Összes|Átvitt adatok és az egyik eszközön sem csatlakozik az IOT hubbal|Nincs dimenzió|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Darabszám|Összes|Kísérlet történt az eszköz-regisztrációinak száma|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Hozzárendelt eszközök|Darabszám|Összes|Az IoT-központ rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Állapotigazolási kísérletek|Darabszám|Összes|Próbált eszköz tanúsítványok száma|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|kért használat gyakorisága|Százalék|Átlag|kért használat gyakorisága|Nincs dimenzió|

## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|tenantHub.requestedUsageRate|kért használat gyakorisága|Százalék|Átlag|kért használat gyakorisága|Nincs dimenzió|
|deviceDataUsage|Teljes devicedata kihasználtsága|Darabszám|Összes|Átvitt adatok és az egyik eszközön sem csatlakozik az IOT hubbal|Nincs dimenzió|
|d2c.telemetry.ingress.allProtocol|Telemetria üzenet küldési kísérlet|Darabszám|Összes|Az IoT hub küldendő próbált eszköz-felhő telemetriai üzenetek száma|Nincs dimenzió|
|d2c.telemetry.ingress.success|Küldött telemetriai üzenetek|Darabszám|Összes|Sikerült elküldeni az IoT hub eszköz-felhő telemetriai üzenetek száma|Nincs dimenzió|
|c2d.commands.egress.complete.success|A parancsok befejeződött|Darabszám|Összes|Az eszköz által végrehajtott felhő eszközre parancsok száma|Nincs dimenzió|
|c2d.commands.egress.abandon.success|Elhagyott parancsok|Darabszám|Összes|Az eszköz által félbe hagyott felhő eszközre parancsok száma|Nincs dimenzió|
|c2d.commands.egress.reject.success|Elutasított parancsok|Darabszám|Összes|Az eszköz által elutasított felhő eszközre parancsok száma|Nincs dimenzió|
|devices.totalDevices|Eszközök teljes száma|Darabszám|Összes|Az IoT hub regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök|Darabszám|Összes|Az IoT hub csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.success|Telemetria kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt végpontok (összesen)|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Az eldobott üzenetek|Darabszám|Összes|Dobva, mert a kézbesítési végpont lett elhalt üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.orphaned|Árva üzenetek|Darabszám|Összes|A nem megfelelő bármely útvonalakat, beleértve a tartalék útvonal üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.invalid|Érvénytelen üzenet|Darabszám|Összes|A végpont alkalmazással való inkompatibilitás miatt nem kézbesített üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Tartalék feltételnek megfelelő üzenetek|Darabszám|Összes|A tartalék végpont üzenetek száma|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Az Event Hubs végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt az Event Hubs végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Az Event Hubs végpontok üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között az Eseményközpont végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Service Bus-üzenetsorba végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-üzenetsorba végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|A Service Bus-üzenetsorba végpontokhoz üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-üzenetsorba végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Service Bus-témakörbe végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-témakörbe végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|A Service Bus-témakörbe végpontokhoz üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-témakörbe végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|A beépített végpont (üzenet/események) kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt a beépített végpont (üzenet/esemény)|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Üzenet várakozási ideje a beépített végpont (üzenet/esemény)|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között történő (üzenet/esemény), beépített végpont ezredmásodpercben |Nincs dimenzió|
|d2c.endpoints.egress.storage|Tárolási végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt tárolási végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.storage|Üzenet késés tárolási végpontok|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a tárolási végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.storage.bytes|Adatok tárhelyen|Bájt|Összes|Adatok tárolási végpontok írt bájtok száma|Nincs dimenzió|
|d2c.endpoints.egress.storage.blobs|Blobok tárhelyen|Darabszám|Összes|BLOB storage végpontok írt száma|Nincs dimenzió|
|d2c.twin.read.success|Sikeres iker olvassa be az eszközökön|Darabszám|Összes|Az összes sikeres a két eszköz által kezdeményezett olvasások száma.|Nincs dimenzió|
|d2c.twin.read.failure|Nem sikerült a kettős olvasások eszközökről|Darabszám|Összes|Teljes számát a két eszköz által kezdeményezett olvasás sikertelen volt.|Nincs dimenzió|
|d2c.twin.read.size|A két válasz mérete olvassa be az eszközökön|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres iker olvasási műveletek.|Nincs dimenzió|
|d2c.twin.update.success|Sikeres iker frissítések eszközökről|Darabszám|Összes|A két eszköz által kezdeményezett összes sikeres frissítések száma.|Nincs dimenzió|
|d2c.twin.update.failure|Nem sikerült a két frissítések eszközökről|Darabszám|Összes|A teljes számát a két eszköz által kezdeményezett frissítések nem sikerült.|Nincs dimenzió|
|d2c.twin.update.size|A két frissítések eszközökről mérete|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres iker frissítések.|Nincs dimenzió|
|c2d.methods.success|Sikeres közvetlen módszer meghívásához|Darabszám|Összes|Az összes sikeres közvetlen módszer hívások száma.|Nincs dimenzió|
|c2d.methods.failure|Nem sikerült a közvetlen módszer meghívásához|Darabszám|Összes|A teljes számát a közvetlen metódushívások nem sikerült.|Nincs dimenzió|
|c2d.methods.requestSize|A közvetlen módszer meghívásához kérelem mérete|Bájt|Átlag|Az átlagos, a minimális és a közvetlen módszer minden sikeres kérelmek maximális.|Nincs dimenzió|
|c2d.methods.responseSize|Válasz mérete közvetlen módszer meghívásához|Bájt|Átlag|Az átlagos, a minimális és a maximális minden sikeres közvetlen módszer válaszok.|Nincs dimenzió|
|c2d.twin.read.success|Sikeres iker háttérből olvassa be|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett iker olvasási műveletek számát.|Nincs dimenzió|
|c2d.twin.read.failure|Sikertelen a két háttérből olvassa be|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett olvasások nem sikerült.|Nincs dimenzió|
|c2d.twin.read.size|A válasz méretének iker olvasások háttérből|Bájt|Átlag|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett iker olvasási műveletek.|Nincs dimenzió|
|c2d.twin.update.success|Sikeres iker frissítések háttérből|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett iker frissítések száma.|Nincs dimenzió|
|c2d.twin.update.failure|Sikertelen a két frissítések háttérből|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett frissítések nem sikerült.|Nincs dimenzió|
|c2d.twin.update.size|A két frissítések háttérből mérete|Bájt|Átlag|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett iker frissítések.|Nincs dimenzió|
|twinQueries.success|Sikeres iker lekérdezések|Darabszám|Összes|Minden sikeres iker lekérdezések száma.|Nincs dimenzió|
|twinQueries.failure|Sikertelen a két lekérdezések|Darabszám|Összes|Összes sikertelen iker lekérdezések száma.|Nincs dimenzió|
|twinQueries.resultSize|A két lekérdezések eredmény méretének|Bájt|Átlag|Az átlagos, a minimális és a maximális az eredmény méretének minden sikeres iker lekérdezések.|Nincs dimenzió|
|jobs.createTwinUpdateJob.success|A sikeres és a két frissítési feladatok|Darabszám|Összes|Minden sikeres létrehozása két frissítési feladatok számát.|Nincs dimenzió|
|jobs.createTwinUpdateJob.failure|Sikertelen és a két frissítési feladatok|Darabszám|Összes|A két frissítési feladatok során létrehozására tett sikertelen kísérlet száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.success|A metódus meghívása feladatok sikeres létrehozása|Darabszám|Összes|Minden sikeres létrehozása közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.failure|A metódus meghívása feladatok sikertelen számát|Darabszám|Összes|A közvetlen metódus meghívása feladatok során létrehozására tett sikertelen kísérlet száma.|Nincs dimenzió|
|jobs.listJobs.success|Lista feladatok sikeres hívások|Darabszám|Összes|Az összes sikeres indított hívások száma a lista feladatok.|Nincs dimenzió|
|jobs.listJobs.failure|Nem sikerült a lista feladatok hívások|Darabszám|Összes|A feladatok lista összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.cancelJob.success|Megszakított feladatok sikeres|Darabszám|Összes|Az összes sikeres indított hívások száma megszakítja a feladatot.|Nincs dimenzió|
|jobs.cancelJob.failure|Sikertelen feladat sikertelen|Darabszám|Összes|A visszavonni a feladatot az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.queryJobs.success|Feladat sikeres lekérdezések|Darabszám|Összes|A lekérdezés feladatok minden sikeres hívások száma.|Nincs dimenzió|
|jobs.queryJobs.failure|Sikertelen feladat-lekérdezések|Darabszám|Összes|A lekérdezés feladatok összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.completed|Befejezett feladatok|Darabszám|Összes|Az összes befejezett feladatok száma.|Nincs dimenzió|
|jobs.failed|A sikertelen feladatok|Darabszám|Összes|Összes sikertelen feladatok száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Sávszélesség-szabályozási hibák száma|Darabszám|Összes|Azelőtt gyorsítja fel az eszköz átviteli miatt szabályozás hibák száma|Nincs dimenzió|
|dailyMessageQuotaUsed|Használt üzenetek teljes száma|Darabszám|Átlag|Jelenleg használt teljes üzenetek száma. Ez az összesített érték, amely nulla értékre állítását: 00:00 UTC minden nap.|Nincs dimenzió|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MetadataRequests|Metaadat-kérelmeket|Darabszám|Darabszám|Metaadat-kérelmek száma. Cosmos DB megőrzi a rendszer metaadat-gyűjtemény minden olyan fiókhoz, amely lehetővé teszi, hogy a gyűjtemények, adatbázisok stb számbavétele, és azok a konfigurációk ingyenesen elérhető.|GlobalDatabaseAccountName, DatabaseName, CollectionName, régió, StatusCode|
|MongoRequestCharge|Mongo kérelem kell fizetni|Darabszám|Összes|Felhasznált mongo kérelemegység|GlobalDatabaseAccountName, DatabaseName, CollectionName, régió, CommandName, hibakód|
|MongoRequests|Mongo kérelmek|Darabszám|Darabszám|Mongo kérelmek száma|GlobalDatabaseAccountName, DatabaseName, CollectionName, régió, CommandName, hibakód|
|TotalRequestUnits|Teljes kérelemegység|Darabszám|Összes|Egységek felhasznált kérése|GlobalDatabaseAccountName, DatabaseName, CollectionName, régió, StatusCode|
|TotalRequests|Összes kérelem|Darabszám|Darabszám|Kérelmek száma|GlobalDatabaseAccountName, DatabaseName, CollectionName, régió, StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub sikeres kérelmeinek száma. (Előzetes verzió)|entityName|
|ServerErrors|Kiszolgálóhibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kiszolgálóhibáinak száma. (Előzetes verzió)|entityName|
|UserErrors|Felhasználói hibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub felhasználói hibáinak száma. (Előzetes verzió)|entityName|
|QuotaExceededErrors|Kvótatúllépési hibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kvótatúllépési hibáinak száma. (Előzetes verzió)|entityName|
|ThrottledRequests|Szabályozott kérelmek száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub szabályozott kérelmeinek száma. (Előzetes verzió)|entityName|
|IncomingRequests|A bejövő kérések (előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub bejövő kérelmeinek száma. (Előzetes verzió)|entityName|
|IncomingMessages|A bejövő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub bejövő üzeneteinek száma. (Előzetes verzió)|entityName|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub kimenő üzeneteinek száma. (Előzetes verzió)|entityName|
|IncomingBytes|Bejövő bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub bejövő bájtjainak száma. (Előzetes verzió)|entityName|
|OutgoingBytes|Kimenő bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub kimenő bájtjainak száma. (Előzetes verzió)|entityName|
|AktívKapcsolatok|ActiveConnections (előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub aktív kapcsolatainak száma összesen. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatok száma. (Előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub megnyitott kapcsolatainak száma. (Előzetes verzió)|entityName|
|ConnectionsClosed|Lezárt kapcsolatok száma. (Előzetes verzió)|Darabszám|Átlag|A Microsoft.EventHub lezárt kapcsolatainak száma. (Előzetes verzió)|entityName|
|CaptureBacklog|Hátralék rögzítése. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub hátralékának rögzítése. (Előzetes verzió)|entityName|
|CapturedMessages|Rögzített üzenetek száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.EventHub rögzített üzeneteinek száma. (Előzetes verzió)|entityName|
|CapturedBytes|Rögzített bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft.EventHub rögzített bájtjainak száma. (Előzetes verzió)|entityName|
|Méret|Méret (előzetes verzió)|Bájt|Átlag|Az EventHub bájtban kifejezett mérete. (Előzetes verzió)|entityName|
|INREQS|Bejövő kérések|Darabszám|Összes|Egy névtér bejövő küldési kérelmei összesen|Nincs dimenzió|
|SUCCREQ|Sikeres kérések|Darabszám|Összes|A névtér összes sikeres kérelme|Nincs dimenzió|
|FAILREQ|Sikertelen kérések|Darabszám|Összes|A névtérhez kapcsolódó összes sikertelen kérelem|Nincs dimenzió|
|SVRBSY|Kiszolgáló foglaltsága miatti hibák|Darabszám|Összes|A névtér összes, kiszolgáló foglaltsága miatti hibája|Nincs dimenzió|
|INTERR|Belső kiszolgálóhibák|Darabszám|Összes|A névtér összes belső kiszolgálóhibája|Nincs dimenzió|
|MISCERR|Egyéb hibák|Darabszám|Összes|A névtérhez kapcsolódó összes sikertelen kérelem|Nincs dimenzió|
|INMSGS|Bejövő üzenetek (elavult)|Darabszám|Összes|Teljes bejövő üzenetek névtér. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrika|Nincs dimenzió|
|EHINMSGS|Bejövő üzenetek|Darabszám|Összes|A névtér összes bejövő üzenete|Nincs dimenzió|
|OUTMSGS|Kimenő üzenetek (elavult)|Darabszám|Összes|A teljes kimenő üzenetek névtér. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrika|Nincs dimenzió|
|EHOUTMSGS|Kimenő üzenetek|Darabszám|Összes|A névtér összes kimenő üzenete|Nincs dimenzió|
|EHINMBS|Bejövő bájtok (elavult)|Bájt|Összes|Event Hub bejövő üzenet átviteli névtér. Ez a metrika elavult. Ehelyett használja a bejövő bájtok metrika|Nincs dimenzió|
|EHINBYTES|Bejövő bájtok száma|Bájt|Összes|Az eseményközpont adott névtérhez kapcsolódó bejövő üzeneteinek átviteli sebessége|Nincs dimenzió|
|EHOUTMBS|Kimenő bájtok (elavult)|Bájt|Összes|Event Hub kimenő üzenet átviteli névtér. Ez a metrika elavult. Ehelyett használja a kimenő bájt metrika|Nincs dimenzió|
|EHOUTBYTES|Kimenő bájtok száma|Bájt|Összes|Az eseményközpont adott névtérhez kapcsolódó kimenő üzeneteinek átviteli sebessége|Nincs dimenzió|
|EHABL|Archivált várakozó üzenetek|Darabszám|Összes|Az eseményközpont adott névtérhez kapcsolódó archivált várakozó üzenetei|Nincs dimenzió|
|EHAMSGS|Üzenetek archiválása|Darabszám|Összes|Az eseményközpont adott névtéren belüli archivált üzenetei|Nincs dimenzió|
|EHAMBS|Archivált üzenetek átviteli sebessége|Bájt|Összes|Az eseményközpont adott névtéren belüli archivált üzeneteinek átviteli sebessége|Nincs dimenzió|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjáró kérelmek|Darabszám|Összes|Átjáró kérelmek száma|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategorizált átjáró kérelmek|Darabszám|Összes|Kategóriák (1xx/2xx/3xx/4xx/5xx) átjáró kérések száma|ClusterDnsName, HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrikaérték|Darabszám|Átlag|Az automatikus skálázás által a végrehajtásakor kiszámított érték|MetricTriggerSource|
|MetricThreshold|Metrika küszöbértéke|Darabszám|Átlag|Az automatikus skálázás futtatásakor konfigurált automatikus skálázási küszöbérték.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Darabszám|Átlag|Az automatikus skálázás számára annak végrehajtásakor jelentett kapacitás.|Nincs dimenzió|
|ScaleActionsInitiated|Elindított skálázási műveletek|Darabszám|Összes|A skálázási művelet iránya.|ScaleDirection|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Összes szolgáltatási API-találat|Darabszám|Darabszám|Szolgáltatási API-találatok teljes száma|Az ActivityType, ActivityName|
|ServiceApiLatency|A szolgáltatási API teljes késése|Ezredmásodperc|Átlag|A szolgáltatási API-kérelmek teljes késése|Az ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Összes szolgáltatási API-eredmény|Darabszám|Darabszám|A szolgáltatási API-eredmények teljes száma|Az ActivityType, ActivityName, StatusCode|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Késés|Késés|Ezredmásodperc|Átlag|Az API-hívások időtartama|OperationName, OperationResult|

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

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|VIP rendelkezésre állása|Darabszám|Átlag|Rendelkezésre állási VIP-végpontok mintavételi eredmények alapján|VipAddress, VipPort|
|DipAvailability|DIP rendelkezésre állása|Darabszám|Átlag|Rendelkezésre állási, a DIP-végpontok mintavételi eredmények alapján|ProtocolType, DipPort, vipaddress értéke, VipPort, DipAddress|
|ByteCount|Bájtok száma|Darabszám|Összes|Időtartamon belül átvitt bájtok teljes száma|VipAddress, VipPort, Direction|
|PacketCount|Csomagok száma|Darabszám|Összes|Időtartamon belül küldött csomagok teljes száma|VipAddress, VipPort, Direction|
|SYNCount|Szinkronizálás a mi száma|Darabszám|Összes|Továbbított időtartamon belül SZIN csomagok száma összesen|VipAddress, VipPort, Direction|
|SnatConnectionCount|SNAT kapcsolatok száma|Darabszám|Összes|Időtartamon belül létrehozott új SNAT kapcsolatok száma|Vipaddress értéke, DipAddress, ConnectionState|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezés kötet|Darabszám|Összes|A DNS-zónák kiszolgált lekérdezések száma|Nincs dimenzió|
|RecordSetCount|Set rekordszám|Darabszám|Maximum|A DNS-zóna rekordhalmazok száma|Nincs dimenzió|
|RecordSetCapacityUtilization|A rekordhalmaz kapacitáskihasználás|Százalék|Maximum|A DNS-zónák alkalmazott rekordhalmaz kapacitás százaléka|Nincs dimenzió|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|Egység/s|Maximum|Bejövő csomagok DDoS|Nincs dimenzió|
|PacketsDroppedDDoS|Eldobott DDoS bejövő csomagok|Egység/s|Maximum|Eldobott DDoS bejövő csomagok|Nincs dimenzió|
|PacketsForwardedDDoS|DDoS továbbított bejövő csomagok|Egység/s|Maximum|DDoS továbbított bejövő csomagok|Nincs dimenzió|
|TCPPacketsInDDoS|A bejövő TCP-csomagok DDoS|Egység/s|Maximum|A bejövő TCP-csomagok DDoS|Nincs dimenzió|
|TCPPacketsDroppedDDoS|Bejövő TCP-csomagok eldobott DDoS|Egység/s|Maximum|Bejövő TCP-csomagok eldobott DDoS|Nincs dimenzió|
|TCPPacketsForwardedDDoS|DDoS továbbított bejövő TCP-csomagok|Egység/s|Maximum|DDoS továbbított bejövő TCP-csomagok|Nincs dimenzió|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|Egység/s|Maximum|Bejövő UDP-csomagok DDoS|Nincs dimenzió|
|UDPPacketsDroppedDDoS|Bejövő UDP-csomagok eldobott DDoS|Egység/s|Maximum|Bejövő UDP-csomagok eldobott DDoS|Nincs dimenzió|
|UDPPacketsForwardedDDoS|DDoS továbbított bejövő UDP-csomagok|Egység/s|Maximum|DDoS továbbított bejövő UDP-csomagok|Nincs dimenzió|
|BytesInDDoS|Bejövő bájtok DDoS|Bájt/s|Maximum|Bejövő bájtok DDoS|Nincs dimenzió|
|BytesDroppedDDoS|Bejövő bájtok eldobott DDoS|Bájt/s|Maximum|Bejövő bájtok eldobott DDoS|Nincs dimenzió|
|BytesForwardedDDoS|DDoS továbbított bejövő bájtok|Bájt/s|Maximum|DDoS továbbított bejövő bájtok|Nincs dimenzió|
|TCPBytesInDDoS|Bejövő TCP bájtok DDoS|Bájt/s|Maximum|Bejövő TCP bájtok DDoS|Nincs dimenzió|
|TCPBytesDroppedDDoS|Bejövő TCP-bájt eldobott DDoS|Bájt/s|Maximum|Bejövő TCP-bájt eldobott DDoS|Nincs dimenzió|
|TCPBytesForwardedDDoS|DDoS továbbított bejövő TCP bájt|Bájt/s|Maximum|DDoS továbbított bejövő TCP bájt|Nincs dimenzió|
|UDPBytesInDDoS|Bejövő UDP bájtok DDoS|Bájt/s|Maximum|Bejövő UDP bájtok DDoS|Nincs dimenzió|
|UDPBytesDroppedDDoS|Bejövő UDP bájt eldobott DDoS|Bájt/s|Maximum|Bejövő UDP bájt eldobott DDoS|Nincs dimenzió|
|UDPBytesForwardedDDoS|DDoS továbbított bejövő UDP bájt|Bájt/s|Maximum|DDoS továbbított bejövő UDP bájt|Nincs dimenzió|
|IfUnderDDoSAttack|DDoS alatt támadásokkal vagy sem|Darabszám|Maximum|DDoS alatt támadásokkal vagy sem|Nincs dimenzió|
|DDoSTriggerTCPPackets|A bejövő TCP-csomagok DDoS megoldás elindítása|Egység/s|Maximum|A bejövő TCP-csomagok DDoS megoldás elindítása|Nincs dimenzió|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok DDoS megoldás elindítása|Egység/s|Maximum|Bejövő UDP-csomagok DDoS megoldás elindítása|Nincs dimenzió|
|DDoSTriggerSYNPackets|A bejövő SZIN csomagok DDoS megoldás elindítása|Egység/s|Maximum|A bejövő SZIN csomagok DDoS megoldás elindítása|Nincs dimenzió|
|VipAvailability|Rendelkezésre állás|Darabszám|Átlag|IP-cím-átlagos rendelkezésre állás időn belül|Port|
|ByteCount|Bájtok száma|Darabszám|Összes|Időtartamon belül átvitt bájtok teljes száma|Port, Direction|
|PacketCount|Csomagok száma|Darabszám|Összes|Időtartamon belül küldött csomagok teljes száma|Port, Direction|
|SynCount|Szinkronizálás a mi száma|Darabszám|Összes|Továbbított időtartamon belül SZIN csomagok száma összesen|Port, Direction|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljesítmény|Teljesítmény|Bájt/s|Összes|Az Alkalmazásátjáró rendelkezik kiszolgált másodpercenként bájtok száma|Nincs dimenzió|
|UnhealthyHostCount|A nem megfelelő gazdagép száma|Darabszám|Átlag|A nem megfelelő háttér gazdagépek száma|BackendSettingsPool|
|HealthyHostCount|Kifogástalan állapotú gazdagép száma|Darabszám|Átlag|Kifogástalan háttér gazdagépek száma|BackendSettingsPool|
|TotalRequests|Összes kérelem|Darabszám|Összes|Alkalmazásátjáró rendelkezik szolgáltatott sikeres kérelmek száma|BackendSettingsPool|
|FailedRequests|Sikertelen kérések|Darabszám|Összes|Alkalmazásátjáró szolgáltatott rendelkezik a sikertelen kérelmek száma|BackendSettingsPool|
|ResponseStatus|Válaszállapot|Darabszám|Összes|Alkalmazásátjáró által visszaadott HTTP-válasz állapota|HttpStatusGroup|
|CurrentConnections|Jelenlegi kapcsolatok száma|Darabszám|Összes|Alkalmazásátjáró létesített jelenlegi kapcsolatok száma|Nincs dimenzió|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Bújtatás sávszélesség|Bájt/s|Átlag|Egy alagút bájt / s átlagos sávszélesség|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bújtatás kilépő bájt|Bájt|Összes|Kimenő bájtok egy alagút|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bújtatás érkező bájtok|Bájt|Összes|Bejövő bájtok egy alagút|ConnectionName, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagok|Darabszám|Összes|Egy alagút kimenő csomagok száma|ConnectionName, RemoteIP|
|TunnelIngressPackets|Bújtatás érkező csomagok|Darabszám|Összes|Egy alagút bejövő csomagok száma|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Bújtatás kilépő TS eltérés csomagjai|Darabszám|Összes|A alagút forgalmi választó nem egyeznek a kimenő csomagok eldobási száma|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Bújtatás érkező TS eltérés csomagjai|Darabszám|Összes|A forgalom választó nem egyeznek a alagút bejövő csomagok eldobási száma|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Egység/s|Átlag|A BITS ingressing Azure / másodperc|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|Egység/s|Átlag|A BITS Azure egressing / másodperc|Nincs dimenzió|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Lekérdezések által visszaadott végpont|Darabszám|Összes|Szám, ahányszor a Traffic Manager-végpont adott vissza az adott időkereten belül|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végpont végpont állapota|Darabszám|Maximum|Ha a végpontok mintavételi állapota "engedélyezve" 1, egyébként pedig 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ProbesFailedPercent|Nem sikerült % mintavételt|Százalék|Átlag|a figyelés mintavételt kapcsolat % nem sikerült|Nincs dimenzió|
|AverageRoundtripMs|Avg. Körbejárási idő (ms)|MilliSeconds|Átlag|Átlagos hálózati oda-vissza idő (ms) a forrás és cél között küldött mintavételt figyelési kapcsolathoz|Nincs dimenzió|

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
|outgoing.wns.badchannel|WNS – rossz csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő csatorna-URI-azonosító ismeretlen (WNS-állapot: 404 Nem található).|Nincs dimenzió|
|outgoing.wns.expiredchannel|WNS – lejárt csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a csatorna-URI-azonosító lejárt (WNS-állapot: 410 Megszűnt).|Nincs dimenzió|
|outgoing.wns.throttled|WNS – szabályozott értesítések|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a WNS szabályozza az alkalmazást. (WNS-állapot: 406 Nem megfelelő).|Nincs dimenzió|
|outgoing.wns.tokenproviderunreachable|WNS – hitelesítési hibák (nem érhető el)|Darabszám|Összes|A Windows Live szolgáltatás nem érhető el.|Nincs dimenzió|
|outgoing.wns.invalidtoken|WNS – hitelesítési hibák (érvénytelen jogkivonat)|Darabszám|Összes|A WNS szolgáltatásnak megadott jogkivonat érvénytelen (WNS-állapot: 401 A hozzáférés megtagadva).|Nincs dimenzió|
|outgoing.wns.wrongtoken|WNS – hitelesítési hibák (nem megfelelő jogkivonat)|Darabszám|Összes|A wns-ből a megadott jogkivonat nem érvényes, de egy másik alkalmazással (WNS állapota: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztrációhoz ChannelURI társítva egy másik alkalmazás. Ellenőrizze, hogy az ügyfélalkalmazás ugyanazt az alkalmazást, amelynek hitelesítő adatait az értesítési központban vannak társítva.|Nincs dimenzió|
|outgoing.wns.invalidnotificationformat|WNS – érvénytelen értesítési formátum|Darabszám|Összes|Az értesítési formátuma érvénytelen (WNS állapota: 400). Vegye figyelembe, hogy a wns-ből nem utasíthatja el minden érvénytelen Payload van jelen.|Nincs dimenzió|
|outgoing.wns.invalidnotificationsize|WNS – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az értesítési tartalom túl nagy (WNS-állapot: 413).|Nincs dimenzió|
|outgoing.wns.channelthrottled|WNS – csatorna szabályozva|Darabszám|Összes|A rendszer elvetette az értesítést, mert a regisztrációban szereplő URI-azonosítójú csatorna szabályozott (WNS-válaszfejléc: X-WNS-NotificationStatus:channelThrottled).|Nincs dimenzió|
|outgoing.wns.channeldisconnected|WNS – nincs kapcsolat a csatornával|Darabszám|Összes|A rendszer elvetette az értesítést, mert a regisztrációban szereplő URI-azonosítójú csatorna szabályozott (WNS-válaszfejléc: X-WNS-DeviceConnectionStatus: disconnected).|Nincs dimenzió|
|outgoing.wns.dropped|WNS – elvetett értesítések|Darabszám|Összes|A rendszer elvetette az értesítést, mert a regisztrációban szereplő URI-azonosítójú csatorna szabályozott (X-WNS-NotificationStatus: dropped, de nem X-WNS-DeviceConnectionStatus: disconnected).|Nincs dimenzió|
|outgoing.wns.pnserror|WNS-hibák|Darabszám|Összes|Az értesítés nem lett kézbesítve, mert hiba történt a WNS szolgáltatással való kommunikáció közben.|Nincs dimenzió|
|outgoing.wns.authenticationerror|WNS – hitelesítési hibák|Darabszám|Összes|Az értesítés nem lett kézbesítve, mert hiba történt a Windows Live szolgáltatással való kommunikáció közben; érvénytelenek a hitelesítő adatok vagy nem megfelelő a jogkivonat.|Nincs dimenzió|
|outgoing.apns.success|APNS – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.apns.invalidcredentials|APNS-hitelesítési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.apns.badchannel|APNS – rossz csatorna által okozott hiba|Darabszám|Összes|A jogkivonat érvénytelensége miatt sikertelen leküldések száma (APNS-állapotkód: 8).|Nincs dimenzió|
|outgoing.apns.expiredchannel|APNS – lejárt csatorna által okozott hiba|Darabszám|Összes|Az APNS visszajelzési csatornája által érvénytelenített jogkivonatok száma.|Nincs dimenzió|
|outgoing.apns.invalidnotificationsize|APNS – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (APNS-állapotkód: 7).|Nincs dimenzió|
|outgoing.apns.pnserror|APNS-hibák|Darabszám|Összes|Az APNS szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.success|GCM – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.gcm.invalidcredentials|GCM – hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.gcm.badchannel|GCM – rossz csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő regisztrációs azonosító ismeretlen (GCM-eredmény: Invalid Registration).|Nincs dimenzió|
|outgoing.gcm.expiredchannel|GCM – lejárt csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő regisztrációs azonosító lejárt (GCM-eredmény: NotRegistered).|Nincs dimenzió|
|outgoing.gcm.throttled|GCM – szabályozott értesítések|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a GCM szabályozta az alkalmazást. (GCM-állapotkód: 501-599 vagy GCM-eredmény: Unavailable).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationformat|GCM – érvénytelen értesítési formátum|Darabszám|Összes|Az adattartalom helytelen formátuma miatt sikertelen leküldések száma (GCM-eredmény: InvalidDataKey vagy InvalidTtl).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationsize|GCM – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM-eredmény: MessageTooBig).|Nincs dimenzió|
|outgoing.gcm.wrongchannel|GCM – nem megfelelő csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő regisztrációs azonosító nem a jelenlegi alkalmazáshoz van hozzárendelve (GCM-eredmény: InvalidPackageName).|Nincs dimenzió|
|outgoing.gcm.pnserror|GCM-hibák|Darabszám|Összes|A GCM szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.authenticationerror|GCM-hitelesítési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva, vagy hogy a küldő azonosítója nincs megfelelően konfigurálva az alkalmazásban (GCM-eredmény: MismatchedSenderId).|Nincs dimenzió|
|outgoing.mpns.success|MPNS – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.mpns.invalidcredentials|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.mpns.badchannel|MPNS – rossz csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő csatorna-URI-azonosító ismeretlen (MPNS-állapot: 404 Nem található).|Nincs dimenzió|
|outgoing.mpns.throttled|MPNS – szabályozott értesítések|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy az MPNS szabályozza az alkalmazást. (WNS MPNS: 406 Nem megfelelő).|Nincs dimenzió|
|outgoing.mpns.invalidnotificationformat|MPNS – érvénytelen értesítési formátum|Darabszám|Összes|Az értesítési tartalom túl nagy mérete miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.mpns.channeldisconnected|MPNS – nincs kapcsolat a csatornával|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő URI-azonosítójú csatornával megszakadt a kapcsolat (MPNS-állapot: 412 Nem található).|Nincs dimenzió|
|outgoing.mpns.dropped|MPNS – elvetett értesítések|Darabszám|Összes|A MPNS által elvetett leküldések száma (MPNS-válaszfejléc: X-NotificationStatus: QueueFull vagy Suppressed).|Nincs dimenzió|
|outgoing.mpns.pnserror|MPNS-hibák|Darabszám|Összes|Az MPNS szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.mpns.authenticationerror|MPNS – hitelesítési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|notificationhub.pushes|Minden kimenő értesítés|Darabszám|Összes|Az értesítési központ minden kimenő értesítése|Nincs dimenzió|
|incoming.all.requests|Minden bejövő kérelem|Darabszám|Összes|Egy értesítési központ összes bejövő kérelme|Nincs dimenzió|
|incoming.all.failedrequests|Minden sikertelen bejövő kérelem|Darabszám|Összes|Egy értesítési központ összes sikertelen bejövő kérelme|Nincs dimenzió|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Lekérdezés időtartama|Ezredmásodperc|Átlag|Utolsó intervallum időtartama DAX-lekérdezés|Nincs dimenzió|
|QueryPoolJobQueueLength|Szálak: Lekérdezés készlet feladat várólistájának hossza|Darabszám|Átlag|A lekérdezés szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces
(Nyilvános előzetes verzió)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_ % szabad Inode-OK|Average_ % szabad Inode-OK|Darabszám|Átlag|Average_ % szabad Inode-OK|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % szabad terület|Average_ % szabad terület|Darabszám|Átlag|Average_ % szabad terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt Inode-OK Average_ %|Foglalt Inode-OK Average_ %|Darabszám|Átlag|Foglalt Inode-OK Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt hely Average_ %|Foglalt hely Average_ %|Darabszám|Átlag|Foglalt hely Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk olvasott bájt/mp|Average_Disk olvasott bájt/mp|Darabszám|Átlag|Average_Disk olvasott bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lemezolvasások/mp|Average_Disk lemezolvasások/mp|Darabszám|Átlag|Average_Disk lemezolvasások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk átvitel/mp|Average_Disk átvitel/mp|Darabszám|Átlag|Average_Disk átvitel/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk írt bájt/mp|Average_Disk írt bájt/mp|Darabszám|Átlag|Average_Disk írt bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk/mp|Average_Disk/mp|Darabszám|Átlag|Average_Disk/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free mérete (MB)|Average_Free mérete (MB)|Darabszám|Átlag|Average_Free mérete (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical bájt/mp|Average_Logical bájt/mp|Darabszám|Átlag|Average_Logical bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ rendelkezésre álló memória %|Average_ rendelkezésre álló memória %|Darabszám|Átlag|Average_ rendelkezésre álló memória %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ rendelkezésre álló Lapozóterület %|Average_ rendelkezésre álló Lapozóterület %|Darabszám|Átlag|Average_ rendelkezésre álló Lapozóterület %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt memória Average_ %|Foglalt memória Average_ %|Darabszám|Átlag|Foglalt memória Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Foglalt Lapozóterület Average_ %|Foglalt Lapozóterület Average_ %|Darabszám|Átlag|Foglalt Lapozóterület Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available memória MB-ban|Average_Available memória MB-ban|Darabszám|Átlag|Average_Available memória MB-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available álló lapozófájl-kapacitás|Average_Available álló lapozófájl-kapacitás|Darabszám|Átlag|Average_Available álló lapozófájl-kapacitás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page lemezolvasások/mp|Average_Page lemezolvasások/mp|Darabszám|Átlag|Average_Page lemezolvasások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page/mp|Average_Page/mp|Darabszám|Átlag|Average_Page/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Average_Pages/sec|Darabszám|Átlag|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Memória (MB) Lapozóterület Average_Used|Memória (MB) Lapozóterület Average_Used|Darabszám|Átlag|Memória (MB) Lapozóterület Average_Used|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória (MB)|Average_Used memória (MB)|Darabszám|Átlag|Average_Used memória (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Küldött bájtok Average_Total|Küldött bájtok Average_Total|Darabszám|Átlag|Küldött bájtok Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott bájtok Average_Total|Fogadott bájtok Average_Total|Darabszám|Átlag|Fogadott bájtok Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bájt|Average_Total bájt|Darabszám|Átlag|Average_Total bájt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total csomagok|Average_Total csomagok|Darabszám|Átlag|Average_Total csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Total csomagok|Fogadott Average_Total csomagok|Darabszám|Átlag|Fogadott Average_Total csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total a Rx hibák|Average_Total a Rx hibák|Darabszám|Átlag|Average_Total a Rx hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx hibák|Average_Total Tx hibák|Darabszám|Átlag|Average_Total Tx hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total ütközések|Average_Total ütközések|Darabszám|Átlag|Average_Total ütközések|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Average_Avg. Lemez mp/Olvasás|Darabszám|Átlag|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/átvitel|Average_Avg. Lemez mp/átvitel|Darabszám|Átlag|Average_Avg. Lemez mp/átvitel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Average_Avg. Lemez mp/írás|Darabszám|Átlag|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical bájt/mp|Average_Physical bájt/mp|Darabszám|Átlag|Average_Physical bájt/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct védett módú használatának aránya|Average_Pct védett módú használatának aránya|Darabszám|Átlag|Average_Pct védett módú használatának aránya|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|Average_Pct felhasználói idő|Darabszám|Átlag|Average_Pct felhasználói idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória KB|Average_Used memória KB|Darabszám|Átlag|Average_Used memória KB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Average_Virtual megosztott memória|Darabszám|Átlag|Average_Virtual megosztott memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ DPC idő %|Average_ DPC idő %|Darabszám|Átlag|Average_ DPC idő %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % üresjárati idő|Average_ % üresjárati idő|Darabszám|Átlag|Average_ % üresjárati idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Megszakítási idő Average_ %|Megszakítási idő Average_ %|Darabszám|Átlag|Megszakítási idő Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % IO várakozási idő|Average_ % IO várakozási idő|Darabszám|Átlag|Average_ % IO várakozási idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ futtatásával töltött idő %|Average_ futtatásával töltött idő %|Darabszám|Átlag|Average_ futtatásával töltött idő %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % védett módú használatának aránya|Average_ % védett módú használatának aránya|Darabszám|Átlag|Average_ % védett módú használatának aránya|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ a processzor kihasználtsága (%)|Average_ a processzor kihasználtsága (%)|Darabszám|Átlag|Average_ a processzor kihasználtsága (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Felhasználói idő Average_ %|Felhasználói idő Average_ %|Darabszám|Átlag|Felhasználói idő Average_ %|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fizikai memória|Average_Free fizikai memória|Darabszám|Átlag|Average_Free fizikai memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free hely a Lapozófájlokban|Average_Free hely a Lapozófájlokban|Darabszám|Átlag|Average_Free hely a Lapozófájlokban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuális memória|Average_Free virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Average_Processes|Darabszám|Átlag|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Lapozófájlokban tárolt Average_Size|Darabszám|Átlag|Lapozófájlokban tárolt Average_Size|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Average_Uptime|Darabszám|Átlag|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Average_Users|Darabszám|Átlag|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Average_Avg. Lemez mp/Olvasás|Darabszám|Átlag|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Average_Avg. Lemez mp/írás|Darabszám|Átlag|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Lemezvárólista hossza|Average_Current Lemezvárólista hossza|Darabszám|Átlag|Average_Current Lemezvárólista hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lemezolvasások/mp|Average_Disk lemezolvasások/mp|Darabszám|Átlag|Average_Disk lemezolvasások/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk átvitel/mp|Average_Disk átvitel/mp|Darabszám|Átlag|Average_Disk átvitel/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk/mp|Average_Disk/mp|Darabszám|Átlag|Average_Disk/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free mérete (MB)|Average_Free mérete (MB)|Darabszám|Átlag|Average_Free mérete (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % szabad terület|Average_ % szabad terület|Darabszám|Átlag|Average_ % szabad terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available memória (MB)|Average_Available memória (MB)|Darabszám|Átlag|Average_Available memória (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % előjegyzett memória|Average_ % előjegyzett memória|Darabszám|Átlag|Average_ % előjegyzett memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Average_Bytes Received/sec|Darabszám|Átlag|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Average_Bytes Sent/sec|Darabszám|Átlag|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes száma/s|Average_Bytes száma/s|Darabszám|Átlag|Average_Bytes száma/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ a processzor kihasználtsága (%)|Average_ a processzor kihasználtsága (%)|Darabszám|Átlag|Average_ a processzor kihasználtsága (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor várólistájának hossza|Average_Processor várólistájának hossza|Darabszám|Átlag|Average_Processor várólistájának hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Szívverés|Szívverés|Darabszám|Átlag|Szívverés|Computer, OSType, Version, SourceComputerId|
|Frissítés|Frissítés|Darabszám|Átlag|Frissítés|A számítógép, a termék, a besorolás, a UpdateState, nem kötelező, jóváhagyott|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Figyelőkapcsolatok-Siker|Figyelőkapcsolatok-Siker|Darabszám|Összes|A Microsoft.Relay sikeresen létrejött figyelőkapcsolatai.|entityName|
|Figyelőkapcsolatok-Ügyfélhiba|Figyelőkapcsolatok-Ügyfélhiba|Darabszám|Összes|A Microsoft.Relay figyelőkapcsolatokra vonatkozó ügyfélhibái.|entityName|
|Figyelőkapcsolatok-Kiszolgálóhiba|Figyelőkapcsolatok-Kiszolgálóhiba|Darabszám|Összes|A Microsoft.Relay figyelőkapcsolatokra vonatkozó kiszolgálóhibái.|entityName|
|FeladóiKapcsolatok-Siker|FeladóiKapcsolatok-Siker|Darabszám|Összes|A Microsoft.Relay sikeresen létrejött feladói kapcsolatai.|entityName|
|FeladóiKapcsolatok-Ügyfélhiba|FeladóiKapcsolatok-Ügyfélhiba|Darabszám|Összes|A Microsoft.Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|entityName|
|FeladóiKapcsolatok-Kiszolgálóhiba|FeladóiKapcsolatok-Kiszolgálóhiba|Darabszám|Összes|A Microsoft.Relay feladói kapcsolatokra vonatkozó kiszolgálóhibái.|entityName|
|Figyelőkapcsolatok-KérelmekÖsszesen|Figyelőkapcsolatok-KérelmekÖsszesen|Darabszám|Összes|A Microsoft.Relay figyelőkapcsolatai összesen.|entityName|
|FeladóiKapcsolatok-KérelmekÖsszesen|FeladóiKapcsolatok-KérelmekÖsszesen|Darabszám|Összes|A Microsoft.Relay feladói kapcsolatai összesen.|entityName|
|AktívKapcsolatok|AktívKapcsolatok|Darabszám|Összes|A Microsoft.Relay aktív kapcsolatai összesen.|entityName|
|AktívFigyelők|AktívFigyelők|Darabszám|Összes|A Microsoft.Relay aktív figyelői összesen.|entityName|
|ÁtvittBájtok|ÁtvittBájtok|Darabszám|Összes|A Microsoft.Relay által átvitt bájtok összesen.|entityName|
|Figyelőkapcsolat-bontások|Figyelőkapcsolat-bontások|Darabszám|Összes|A Microsoft.Relay figyelőkapcsolat-bontásai összesen.|entityName|
|Feladóikapcsolat-bontások|Feladóikapcsolat-bontások|Darabszám|Összes|A Microsoft.Relay feladóikapcsolat-bontásai összesen.|entityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|másodperc|Átlag|A keresési szolgáltatás átlagos keresési késés|Nincs dimenzió|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|Egység/s|Átlag|A keresési szolgáltatás másodpercenként keresési lekérdezések|Nincs dimenzió|
|ThrottledSearchQueriesPercentage|Szabályozott lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás volt halmozódni keresési lekérdezések aránya|Nincs dimenzió|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előzetes verzió)|Darabszám|Összes|A sikeres kérelmek teljes névtér (előzetes verzió)|EntityName, |
|ServerErrors|Kiszolgálóhibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus kiszolgálóhibáinak száma. (Előzetes verzió)|EntityName, |
|UserErrors|Felhasználói hibák száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus felhasználói hibáinak száma. (Előzetes verzió)|EntityName, |
|ThrottledRequests|Szabályozott kérelmek száma. (Előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus szabályozott kérelmeinek száma. (Előzetes verzió)|EntityName, |
|IncomingRequests|A bejövő kérések (előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus bejövő kérelmeinek száma. (Előzetes verzió)|entityName|
|IncomingMessages|A bejövő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus bejövő üzeneteinek száma. (Előzetes verzió)|entityName|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus kimenő üzeneteinek száma. (Előzetes verzió)|entityName|
|AktívKapcsolatok|ActiveConnections (előzetes verzió)|Darabszám|Összes|A Microsoft.ServiceBus aktív kapcsolatainak száma összesen. (Előzetes verzió)|Nincs dimenzió|
|Méret|Méret (előzetes verzió)|Bájt|Átlag|Egy várólista/témakör bájtban kifejezett mérete. (Előzetes verzió)|entityName|
|Üzenetek|A várólista témakör lévő üzenetek száma. (Előzetes verzió)|Darabszám|Átlag|A várólista témakör lévő üzenetek száma. (Előzetes verzió)|entityName|
|ActiveMessages|A várólista témakör lévő aktív üzenetek száma. (Előzetes verzió)|Darabszám|Átlag|A várólista témakör lévő aktív üzenetek száma. (Előzetes verzió)|entityName|
|CPUXNS|Processzorhasználat névterenként|Százalék|Maximum|Prémium szintű Service Bus-névtér CPU-használati metrikája|Nincs dimenzió|
|WSXNS|Felhasznál memória mérete névterenként|Százalék|Maximum|Prémium szintű Service Bus-névtér memóriahasználati metrikája|Nincs dimenzió|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincs dimenzió|
|log_write_percent|Napló IO százalékos aránya|Százalék|Átlag|Napló IO százalékos aránya|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|Nincs dimenzió|
|tárterület|Adatbázis teljes mérete|Bájt|Maximum|Adatbázis teljes mérete|Nincs dimenzió|
|connection_successful|Sikeres kapcsolatok|Darabszám|Összes|Sikeres kapcsolatok|Nincs dimenzió|
|connection_failed|Nem sikerült kapcsolatok|Darabszám|Összes|Nem sikerült kapcsolatok|Nincs dimenzió|
|blocked_by_firewall|Tiltsa le tűzfal|Darabszám|Összes|Tiltsa le tűzfal|Nincs dimenzió|
|Holtpont|Holtpont|Darabszám|Összes|Holtpont|Nincs dimenzió|
|storage_percent|Adatbázis méretének kihasználtsága|Százalék|Maximum|Adatbázis méretének kihasználtsága|Nincs dimenzió|
|xtp_storage_percent|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Százalék|Átlag|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Százalék|Átlag|Feldolgozók százalékos aránya|Nincs dimenzió|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincs dimenzió|
|dtu_limit|DTU korlátot|Darabszám|Átlag|DTU korlátot|Nincs dimenzió|
|dtu_used|Felhasznált DTU|Darabszám|Átlag|Felhasznált DTU|Nincs dimenzió|
|dwu_limit|DWU korlátot|Darabszám|Maximum|DWU korlátot|Nincs dimenzió|
|dwu_consumption_percent|DWU százalékos aránya|Százalék|Maximum|DWU százalékos aránya|Nincs dimenzió|
|dwu_used|A DWU használt|Darabszám|Maximum|A DWU használt|Nincs dimenzió|
|dw_cpu_percent|DW csomópont szintjén processzor|Százalék|Átlag|DW csomópont szintjén processzor|DwLogicalNodeId|
|dw_physical_data_read_percent|DW csomópont adat IO százalékos értéke|Százalék|Átlag|DW csomópont adat IO százalékos értéke|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincs dimenzió|
|log_write_percent|Napló IO százalékos aránya|Százalék|Átlag|Napló IO százalékos aránya|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|Nincs dimenzió|
|storage_percent|Tárolási százalékos aránya|Százalék|Átlag|Tárolási százalékos aránya|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Százalék|Átlag|Feldolgozók százalékos aránya|Nincs dimenzió|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincs dimenzió|
|eDTU_limit|eDTU korlátot|Darabszám|Átlag|eDTU korlátot|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Átlag|Tárolási kapacitása|Nincs dimenzió|
|eDTU_used|felhasznált edtu-ra|Darabszám|Átlag|felhasznált edtu-ra|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|xtp_storage_percent|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Százalék|Átlag|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Nincs dimenzió|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|ElasticPoolResourceId|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Használt kapacitás|Bájt|Átlag|A fiók felhasznált kapacitása|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, ApiName|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|Blob-kapacitása|Bájt|Összes|A tárfiók Blob Service-példánya által felhasznált tárterület mérete bájtban megadva.|BlobType|
|BlobCount|Blobok száma|Darabszám|Összes|A tárfiók Blob Service-példányában található blobok száma.|BlobType|
|ContainerCount|Blobtárolók száma|Darabszám|Átlag|A tárfiók Blob Service-példányában található tárolók száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, ApiName|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Table Storage kapacitása|Bájt|Átlag|A tárfiók Table Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|TableCount|Táblák száma|Darabszám|Átlag|A tárfiók Table Storage-szolgáltatás-példányában található táblák száma.|Nincs dimenzió|
|TableEntityCount|Táblaentitások száma|Darabszám|Átlag|A tárfiók Table Storage-szolgáltatás-példányában található táblaentitások száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, ApiName|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Queue Storage kapacitása|Bájt|Átlag|A tárfiók Queue Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|QueueCount|Üzenetsorok száma|Darabszám|Átlag|A tárfiók Queue-szolgáltatás-példányában található üzenetsorok száma.|Nincs dimenzió|
|QueueMessageCount|Üzenetsorbeli üzenetek száma|Darabszám|Átlag|A tárfiók Queue Storage-szolgáltatás-példányában található üzenetsorbeli üzenetek hozzávetőleges száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, ApiName|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|File Storage kapacitása|Bájt|Átlag|A tárfiók File Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|FileCount|Fájlok száma|Darabszám|Átlag|A tárfiók File Storage-szolgáltatás-példányában található fájlok száma.|Nincs dimenzió|
|FileShareCount|Fájlmegosztások száma|Darabszám|Átlag|A tárfiók File Storage-szolgáltatás-példányában található fájlmegosztások száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, ApiName|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU százalékos kihasználtsága|Százalék|Maximum|SU százalékos kihasználtsága|Nincs dimenzió|
|InputEvents|Bemeneti események|Darabszám|Összes|Bemeneti események|Nincs dimenzió|
|InputEventBytes|Bemeneti esemény bájtokban|Bájt|Összes|Bemeneti esemény bájtokban|Nincs dimenzió|
|LateInputEvents|Késedelmes bemeneti események|Darabszám|Összes|Késedelmes bemeneti események|Nincs dimenzió|
|OutputEvents|Kimeneti események|Darabszám|Összes|Kimeneti események|Nincs dimenzió|
|ConversionErrors|Adatkonverziós hibák|Darabszám|Összes|Adatkonverziós hibák|Nincs dimenzió|
|Hibák|Futásidejű hibák|Darabszám|Összes|Futásidejű hibák|Nincs dimenzió|
|DroppedOrAdjustedEvents|Üzemen kívüli események|Darabszám|Összes|Üzemen kívüli események|Nincs dimenzió|
|AMLCalloutRequests|Függvénykérések|Darabszám|Összes|Függvénykérések|Nincs dimenzió|
|AMLCalloutFailedRequests|Sikertelen függvénykérések|Darabszám|Összes|Sikertelen függvénykérések|Nincs dimenzió|
|AMLCalloutInputEvents|Függvényesemények|Darabszám|Összes|Függvényesemények|Nincs dimenzió|
|DeserializationError|A deszerializálás bemeneti hibái|Darabszám|Összes|A deszerializálás bemeneti hibái|Nincs dimenzió|
|EarlyInputEvents|Események, amelyeknél az alkalmazásbeli pontos idő korábbi, mint az érkezési idejük.|Darabszám|Összes|Események, amelyeknél az alkalmazásbeli pontos idő korábbi, mint az érkezési idejük.|Nincs dimenzió|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Százalék|Átlag|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Darabszám|Átlag|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Darabszám|Átlag|HTTP-várólista hossza|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (funkciókat kivéve)

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

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funkciók)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Darabszám|Összes|HTTP-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória-munkakészlet|Bájt|Átlag|Memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Átlag|Átlagos memória-munkakészlet|Példány|
|FunctionExecutionUnits|Függvény-végrehajtási egység|Darabszám|Összes|Függvény-végrehajtási egység|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Darabszám|Összes|Függvény végrehajtásainak száma|Példány|

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
|WorkersUsed|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|Nincs dimenzió|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Százalék|Átlag|Memóriahasználat (%)|Példány|

## <a name="next-steps"></a>További lépések
* [Olvassa el a Azure figyelő metrikák](monitoring-overview-metrics.md)
* [Hozzon létre a riasztások mérőszámokat](insights-receive-alert-notifications.md)
* [Tárolás, az Event Hubs vagy Naplóelemzési metrikák exportálása](monitoring-overview-of-diagnostic-logs.md)
