---
title: "Az Azure figyelő metrika - erőforrás típusonkénti támogatott metrikák |} Microsoft Docs"
description: "Minden erőforrás típusból Azure megfigyelővel metrikák listája."
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: ancav
ms.openlocfilehash: 05830547a5b8a24a59571edf6dd44d101b660189
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure-figyelő támogatott metrikák
Azure figyelő többféleképpen is kommunikál a metrikákat, többek között a diagramkészítési őket a portálon, a hozzájuk férni a REST API-n keresztül, vagy a őket lekérdezése a PowerShell vagy a parancssori felület. Alatt érhető el teljes listáját és az összes metrikák jelenleg Azure figyelő metrika folyamat.

> [!NOTE]
> Más metrikákkal portálon vagy az örökölt API-k használatával érhetők el. Ez a lista csak voltak elérhetők a összevont Azure-figyelő metrika kimenetátirányításának segítségével metrikák tartalmazza. A lekérdezés és eléréséhez metrikák többdimenziósak kérjük, használja a [2017-05-01. dátumú előnézeti api-verzió](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Darabszám|Átlagos|QPU. Tartomány 0 – 100 s1, 0-200 S2 és 0 – 400 S4|Nincs dimenzió|
|memory_metric|Memory (Memória)|Bájtok|Átlagos|A memória. S1 0-25 GB-ot, S2 0-50 GB-ot és 0 – 100 GB-ot S4|Nincs dimenzió|
|TotalConnectionRequests|Teljes csatlakozási kérések|Mennyiség|Átlagos|Teljes csatlakozási kérések. Ezek a érkezők.|Nincs dimenzió|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenkénti száma|CountPerSecond|Átlagos|Sikeres csatlakozás befejezésekhez aránya.|Nincs dimenzió|
|TotalConnectionFailures|Teljes kapcsolódási hibák|Darabszám|Átlagos|Összes sikertelen csatlakozási kísérletek.|Nincs dimenzió|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Mennyiség|Átlagos|Létrehozott felhasználói munkamenetek jelenlegi száma.|Nincs dimenzió|
|QueryPoolBusyThreads|Lekérdezés készlet foglalt szálak|Darabszám|Átlagos|A lekérdezés szálkészlet foglalt szálak számát.|Nincs dimenzió|
|CommandPoolJobQueueLength|A parancs készlet feladat várólistájának hossza|Darabszám|Átlagos|A parancs szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|
|ProcessingPoolJobQueueLength|Feldolgozási készlet feladat várólistájának hossza|Darabszám|Átlagos|A várólistában a feldolgozási szálkészletben lévő nem-I/O-feladatok száma.|Nincs dimenzió|
|CurrentConnections|Kapcsolat: A jelenlegi kapcsolatok száma|Darabszám|Átlagos|Ügyfél fennálló kapcsolatok aktuális száma.|Nincs dimenzió|
|CleanerCurrentPrice|Memória: Tisztító aktuális árlista|Darabszám|Átlagos|Aktuális árlista memória $/ bájt/idő normalizálva 1000.|Nincs dimenzió|
|CleanerMemoryShrinkable|Memória: Zsugoríthatósági tisztító memória|Bájtok|Átlagos|Memória mérete, a háttérben tisztító kiürítése függvényében mennyisége.|Nincs dimenzió|
|CleanerMemoryNonshrinkable|Memória: Tisztító memória nonshrinkable|Bájtok|Átlagos|Memória mérete, nem vonatkoznak a háttérben tisztító kiürítése mennyisége.|Nincs dimenzió|
|MemoryUsage|Memória: Memória használata|Bájtok|Átlagos|A kiszolgálói folyamat, tisztító memória ár kiszámításakor felhasznált memória mennyisége Process\PrivateBytes plus memórialeképezési adatok figyelmen kívül hagyása minden leképezve, vagy a xVelocity memórián belüli analytics motor (VertiPaq) meghaladja a memóriakorlátot xVelocity motor által lefoglalt memória méretét a teljesítményszámláló értéke.|Nincs dimenzió|
|MemoryLimitHard|Memória: Memóriakorlátot rögzített|Bájtok|Átlagos|Rögzített memóriakorlátot, konfigurációs fájlból.|Nincs dimenzió|
|MemoryLimitHigh|Memória: Memória korlátozása magas|Bájtok|Átlagos|Magas memóriakorlátot, konfigurációs fájlból.|Nincs dimenzió|
|MemoryLimitLow|Memória: Memória korlátja alacsony|Bájtok|Átlagos|Kevés a memória a határt, a konfigurációs fájlból.|Nincs dimenzió|
|MemoryLimitVertiPaq|Memória: Memória korlátja VertiPaq|Bájtok|Átlagos|A memória maximális mennyiségét, a konfigurációs fájlból.|Nincs dimenzió|
|Kvóta|Memória: kvóta|Bájtok|Átlagos|Aktuális memóriakvótáját, bájtban. Memóriakvótáját memória grant vagy memória foglalás is nevezik.|Nincs dimenzió|
|QuotaBlocked|Memória: Kvóta letiltva|Mennyiség|Átlagos|Le vannak tiltva, amíg más memória kvóták felszabadítását kvóta kérelmek jelenlegi száma.|Nincs dimenzió|
|VertiPaqNonpaged|Memória: a nem lapozható VertiPaq|Bájtok|Átlagos|A memórián belüli motor általi használatra munkakészletében bájt memóriát zárolva van.|Nincs dimenzió|
|VertiPaqPaged|Memória: A VertiPaq lapozható|Bájtok|Átlagos|A memóriában levő használt lapozható memória bájt.|Nincs dimenzió|
|RowsReadPerSec|Feldolgozási: Sor olvasása másodpercenkénti száma|CountPerSecond|Átlagos|Sorok aránya az összes relációs adatbázisok olvasni.|Nincs dimenzió|
|RowsConvertedPerSec|Feldolgozási: Sorok konvertálni másodpercenkénti száma|CountPerSecond|Átlagos|Sorok aránya alakítja a feldolgozás során.|Nincs dimenzió|
|RowsWrittenPerSec|Feldolgozási: Másodpercenként írt sorok|CountPerSecond|Átlagos|Feldolgozás során írt sorok száma.|Nincs dimenzió|
|CommandPoolBusyThreads|Szálak: Parancs készlet foglalt szálak|Darabszám|Átlagos|A parancs szálkészlet foglalt szálak számát.|Nincs dimenzió|
|CommandPoolIdleThreads|Szálak: Parancs készlet üresjárati szálak|Mennyiség|Átlagos|A parancs szálkészlet üresjárati szálak számát.|Nincs dimenzió|
|LongParsingBusyThreads|Szálak: Hosszú foglalt szálak elemzése|Darabszám|Átlagos|A hosszú elemzési szálkészlet foglalt szálak számát.|Nincs dimenzió|
|LongParsingIdleThreads|Szálak: Hosszú üresjárati szálak elemzése|Darabszám|Átlagos|A hosszú elemzési szálkészlet üresjárati szálak számát.|Nincs dimenzió|
|LongParsingJobQueueLength|Szálak: Hosszú elemzése feladat várólistájának hossza|Darabszám|Átlagos|A hosszú elemzési szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|
|ProcessingPoolBusyIOJobThreads|Szálak: Készlet foglalt i/o-feladat szálak feldolgozása|Darabszám|Átlagos|I/o-feladatok futtatása a feldolgozási szálkészletben lévő szálak száma.|Nincs dimenzió|
|ProcessingPoolBusyNonIOThreads|Szálak: Készlet foglalt nem-I/O-szálak feldolgozása|Mennyiség|Átlagos|Nem-I/O-feladatok futtatása a feldolgozási szálkészletben lévő szálak száma.|Nincs dimenzió|
|ProcessingPoolIOJobQueueLength|Szálak: Feldolgozása készlet i/o-feldolgozás várólistájának hossza|Darabszám|Átlagos|A várólistában a feldolgozási szálkészletben lévő i/o-feladatok száma.|Nincs dimenzió|
|ProcessingPoolIdleIOJobThreads|Szálak: Készlet üresjárati i/o-feladat szálak feldolgozása|Darabszám|Átlagos|A feldolgozási szálkészletben lévő i/o-feladatok üresjárati szálak száma.|Nincs dimenzió|
|ProcessingPoolIdleNonIOThreads|Szálak: Készlet üresjárati nem-I/O-szálak feldolgozása|Darabszám|Átlagos|Nem-I/O-feladatok hozzárendelve a feldolgozási szálkészletben üresjárati szálak számát.|Nincs dimenzió|
|QueryPoolIdleThreads|Szálak: Lekérdezés készlet üresjárati szálak|Darabszám|Átlagos|A feldolgozási szálkészletben lévő i/o-feladatok üresjárati szálak száma.|Nincs dimenzió|
|QueryPoolJobQueueLength|Szálak: Lekérdezés készlet feladat várólista lengt|Mennyiség|Átlagos|A lekérdezés szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|
|ShortParsingBusyThreads|Szálak: Rövid foglalt szálak elemzése|Mennyiség|Átlagos|A rövid elemzési szálkészlet foglalt szálak számát.|Nincs dimenzió|
|ShortParsingIdleThreads|Szálak: Rövid üresjárati szálak elemzése|Darabszám|Átlagos|A rövid elemzési szálkészlet üresjárati szálak számát.|Nincs dimenzió|
|ShortParsingJobQueueLength|Szálak: Rövid elemzése feladat várólistájának hossza|Mennyiség|Átlagos|A rövid elemzési szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|
|memory_thrashing_metric|Memória Akadozások|Százalék|Átlagos|Átlagos memória thrashing.|Nincs dimenzió|
|mashup_engine_qpu_metric|M-motor QPU|Darabszám|Átlagos|Adategyesítési motor folyamatok QPU kihasználtsága|Nincs dimenzió|
|mashup_engine_memory_metric|M-motor memória|Bájtok|Átlagos|Adategyesítési motor folyamatok memóriahasználata|Nincs dimenzió|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Teljes átjáró kérelmek|Darabszám|Összes|Átjáró kérelmek száma|Hely, állomásnév|
|SuccessfulRequests|Átjáró sikeres kérelmek|Mennyiség|Összes|Átjáró sikeres kérelmek száma|Hely, állomásnév|
|UnauthorizedRequests|Jogosulatlan átjáró kérelmek|Mennyiség|Összes|Jogosulatlan átjáró kérelmek száma|Hely, állomásnév|
|FailedRequests|Hibás átjáró kérelmek|Darabszám|Összes|Az átjáró kérelmekben hibák száma|Hely, állomásnév|
|OtherRequests|Más átjáró kérelmek|Mennyiség|Összes|Más átjáró kérelmek száma|Hely, állomásnév|
|Időtartam|Átjáró kérelmek teljes időtartama|ideje ezredmásodpercben|Átlagos|Teljes időtartam az átjáró kérelmek ezredmásodpercben|Hely, állomásnév|
|Kapacitás|Kapacitás (előzetes verzió)|Százalék|Maximális|Kihasználtság metrika ApiManagement szolgáltatás|Hely|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok teljes száma|Darabszám|Összes|A feladatok teljes száma|Nincs dimenzió|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált magok száma|Mennyiség|Összes|A batch-fiók dedikált magok száma összesen|Nincs dimenzió|
|TotalNodeCount|Dedikált csomópontok száma|Mennyiség|Összes|A batch-fiók dedikált csomópontja teljes száma|Nincs dimenzió|
|LowPriorityCoreCount|LowPriority magok száma|Darabszám|Összes|A batch-fiókhoz az alacsony prioritású magok száma összesen|Nincs dimenzió|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Darabszám|Összes|Alacsony prioritású csomópontok a batch-fiók teljes száma|Nincs dimenzió|
|CreatingNodeCount|Csomópontok száma létrehozása|Darabszám|Összes|A létrehozandó csomópontok száma|Nincs dimenzió|
|StartingNodeCount|Kezdő csomópontok száma|Darabszám|Összes|Kezdési csomópontok száma|Nincs dimenzió|
|WaitingForStartTaskNodeCount|Várakozás a kezdő tevékenység csomópontok száma|Mennyiség|Összes|Várakozás a feladat indítása befejezéséhez a csomópontok száma|Nincs dimenzió|
|StartTaskFailedNodeCount|Kezdő tevékenység nem sikerült a csomópontok száma|Mennyiség|Összes|Ha a feladat indítása nem csomópontok száma|Nincs dimenzió|
|IdleNodeCount|Üresjárati csomópontok száma|Mennyiség|Összes|Üresjárati csomópontok száma|Nincs dimenzió|
|OfflineNodeCount|Kapcsolat nélküli csomópontok száma|Mennyiség|Összes|Kapcsolat nélküli csomópontok száma|Nincs dimenzió|
|RebootingNodeCount|Rendszer újraindítása a csomópontok száma|Darabszám|Összes|Újraindítás a csomópontok száma|Nincs dimenzió|
|ReimagingNodeCount|Különösen csomópontok száma|Mennyiség|Összes|Különösen csomópontok száma|Nincs dimenzió|
|RunningNodeCount|Futó csomópontok száma|Darabszám|Összes|Futó csomópontok száma|Nincs dimenzió|
|LeavingPoolNodeCount|Kilépés a készlet csomópontok száma|Darabszám|Összes|A készlet elhagyása csomópontok száma|Nincs dimenzió|
|UnusableNodeCount|Használhatatlanná csomópontok száma|Darabszám|Összes|Használhatatlanná csomópontok száma|Nincs dimenzió|
|PreemptedNodeCount|Csomópont Count részesítés|Darabszám|Összes|A leállított csomópontok száma|Nincs dimenzió|
|TaskStartEvent|A feladat kezdési események|Darabszám|Összes|Kezdték feladatok száma összesen|Nincs dimenzió|
|TaskCompleteEvent|A feladat befejezése események|Mennyiség|Összes|A befejezett feladatok száma összesen|Nincs dimenzió|
|TaskFailEvent|A feladat sikertelen események|Darabszám|Összes|Hibás állapotban befejeződött feladatok száma összesen|Nincs dimenzió|
|PoolCreateEvent|Alkalmazáskészlet létrehozása események|Darabszám|Összes|Létrehozott gyűjtők száma összesen|Nincs dimenzió|
|PoolResizeStartEvent|Készlet átméretezési Start események|Darabszám|Összes|Készlet átméretezi, amely kezdték száma összesen|Nincs dimenzió|
|PoolResizeCompleteEvent|Készlet átméretezési teljes események|Mennyiség|Összes|Készlet átméretezi elvégző száma összesen|Nincs dimenzió|
|PoolDeleteStartEvent|Kezdő események készlet törlése|Darabszám|Összes|Teljes száma, amelyek kezdték készlet törlése|Nincs dimenzió|
|PoolDeleteCompleteEvent|Alkalmazáskészlet teljes események törlése|Darabszám|Összes|A befejezett készlet törlése száma összesen|Nincs dimenzió|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakozott ügyfelek|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed|Összes művelet|Mennyiség|Összes||Nincs dimenzió|
|cachehits|Találatot eredményező gyorsítótárbeli kereséseinek|Darabszám|Összes||Nincs dimenzió|
|cachemisses|Gyorsítótár-tévesztései|Mennyiség|Összes||Nincs dimenzió|
|getcommands|Beolvasása|Mennyiség|Összes||Nincs dimenzió|
|setcommands|Beállítása|Mennyiség|Összes||Nincs dimenzió|
|evictedkeys|Eltávolított kulcsok|Darabszám|Összes||Nincs dimenzió|
|totalkeys|Összes kulcs|Darabszám|Maximális||Nincs dimenzió|
|expiredkeys|Lejárt kulcsok|Mennyiség|Összes||Nincs dimenzió|
|usedmemory|Használt memória|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss|Használt memória RSS|Bájtok|Maximális||Nincs dimenzió|
|serverLoad|A kiszolgálóterhelés|Százalék|Maximális||Nincs dimenzió|
|cacheWrite|Gyorsítótár-írási|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead|Gyorsítótár olvasása|BytesPerSecond|Maximális||Nincs dimenzió|
|PercentProcessorTime|CPU|Százalék|Maximális||Nincs dimenzió|
|connectedclients0|Csatlakozott ügyfelek (szilánkok 0)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed0|Összes művelet (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|cachehits0|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|cachemisses0|A gyorsítótárbeli (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|getcommands0|Lekérdezi a (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|setcommands0|Beállítása (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys0|Eltávolított kulcsok (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|totalkeys0|Összes kulcs (szilánkok 0)|Darabszám|Maximális||Nincs dimenzió|
|expiredkeys0|Lejárt kulcsokkal (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|usedmemory0|Használt memória (szilánkok 0)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss0|Használt memória RSS (szilánkok 0)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad0|Kiszolgáló terhelését (szilánkok 0)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite0|Gyorsítótár-írási (szilánkok 0)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead0|Gyorsítótár olvasható (szilánkok 0)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime0|Processzor (szilánkok 0)|Százalék|Maximális||Nincs dimenzió|
|connectedclients1|Csatlakozott ügyfelek (szilánkok 1)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed1|Összes művelet (szilánkok 1)|Mennyiség|Összes||Nincs dimenzió|
|cachehits1|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 1)|Mennyiség|Összes||Nincs dimenzió|
|cachemisses1|A gyorsítótárbeli (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|getcommands1|(Szilánkok 1) beolvasása|Darabszám|Összes||Nincs dimenzió|
|setcommands1|(1 a Shard) beállítása|Mennyiség|Összes||Nincs dimenzió|
|evictedkeys1|Eltávolított kulcsok (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|totalkeys1|Összes kulcs (szilánkok 1)|Mennyiség|Maximális||Nincs dimenzió|
|expiredkeys1|Lejárt kulcsokkal (szilánkok 1)|Mennyiség|Összes||Nincs dimenzió|
|usedmemory1|Használt memória (szilánkok 1)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss1|Használt memória RSS (szilánkok 1)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad1|Kiszolgáló terhelését (szilánkok 1)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite1|Gyorsítótár-írási (szilánkok 1)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead1|Gyorsítótár olvasható (szilánkok 1)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime1|Processzor (szilánkok 1)|Százalék|Maximális||Nincs dimenzió|
|connectedclients2|Csatlakozott ügyfelek (szilánkok 2)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed2|Összes művelet (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|cachehits2|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|cachemisses2|A gyorsítótárbeli (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|getcommands2|Lekérdezi a (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|setcommands2|Beállítása (szilánkok 2)|Mennyiség|Összes||Nincs dimenzió|
|evictedkeys2|Eltávolított kulcsok (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|totalkeys2|Összes kulcs (szilánkok 2)|Darabszám|Maximális||Nincs dimenzió|
|expiredkeys2|Lejárt kulcsokkal (szilánkok 2)|Mennyiség|Összes||Nincs dimenzió|
|usedmemory2|Használt memória (szilánkok 2)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss2|Használt memória RSS (szilánkok 2)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad2|Kiszolgáló terhelését (szilánkok 2)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite2|Gyorsítótár-írási (szilánkok 2)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead2|Gyorsítótár olvasható (szilánkok 2)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime2|Processzor (szilánkok 2)|Százalék|Maximális||Nincs dimenzió|
|connectedclients3|Csatlakozott ügyfelek (szilánkok 3)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed3|Összes művelet (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|cachehits3|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|cachemisses3|A gyorsítótárbeli (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|getcommands3|Lekérdezi a (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|setcommands3|(3 szilánkok) beállítása|Darabszám|Összes||Nincs dimenzió|
|evictedkeys3|Eltávolított kulcsok (szilánkok 3)|Mennyiség|Összes||Nincs dimenzió|
|totalkeys3|Összes kulcs (szilánkok 3)|Mennyiség|Maximális||Nincs dimenzió|
|expiredkeys3|Lejárt kulcsokkal (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|usedmemory3|Használt memória (szilánkok 3)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss3|Használt memória RSS (szilánkok 3)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad3|Kiszolgáló terhelését (szilánkok 3)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite3|Gyorsítótár-írási (szilánkok 3)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead3|Gyorsítótár olvasható (szilánkok 3)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime3|Processzor (szilánkok 3)|Százalék|Maximális||Nincs dimenzió|
|connectedclients4|Csatlakozott ügyfelek (szilánkok 4)|Mennyiség|Maximális||Nincs dimenzió|
|totalcommandsprocessed4|Összes művelet (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|cachehits4|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|cachemisses4|A gyorsítótárbeli (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|getcommands4|Lekérdezi a (szilánkok 4)|Mennyiség|Összes||Nincs dimenzió|
|setcommands4|(4 szilánkok) beállítása|Darabszám|Összes||Nincs dimenzió|
|evictedkeys4|Eltávolított kulcsok (szilánkok 4)|Mennyiség|Összes||Nincs dimenzió|
|totalkeys4|Összes kulcs (szilánkok 4)|Mennyiség|Maximális||Nincs dimenzió|
|expiredkeys4|Lejárt kulcsokkal (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|usedmemory4|Használt memória (szilánkok 4)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss4|Használt memória RSS (szilánkok 4)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad4|Kiszolgáló terhelését (szilánkok 4)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite4|Gyorsítótár-írási (szilánkok 4)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead4|Gyorsítótár olvasható (szilánkok 4)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime4|Processzor (szilánkok 4)|Százalék|Maximális||Nincs dimenzió|
|connectedclients5|Csatlakozott ügyfelek (szilánkok 5)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed5|Összes művelet (szilánkok 5)|Mennyiség|Összes||Nincs dimenzió|
|cachehits5|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|cachemisses5|A gyorsítótárbeli (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|getcommands5|Lekérdezi a (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|setcommands5|Beállítása (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys5|Eltávolított kulcsok (szilánkok 5)|Mennyiség|Összes||Nincs dimenzió|
|totalkeys5|Összes kulcs (szilánkok 5)|Darabszám|Maximális||Nincs dimenzió|
|expiredkeys5|Lejárt kulcsokkal (szilánkok 5)|Mennyiség|Összes||Nincs dimenzió|
|usedmemory5|Használt memória (szilánkok 5)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss5|Használt memória RSS (szilánkok 5)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad5|Kiszolgáló terhelését (szilánkok 5)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite5|Gyorsítótár-írási (szilánkok 5)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead5|Gyorsítótár olvasható (szilánkok 5)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime5|Processzor (szilánkok 5)|Százalék|Maximális||Nincs dimenzió|
|connectedclients6|Csatlakozott ügyfelek (szilánkok 6)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed6|Összes művelet (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|cachehits6|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 6)|Mennyiség|Összes||Nincs dimenzió|
|cachemisses6|A gyorsítótárbeli (szilánkok 6)|Mennyiség|Összes||Nincs dimenzió|
|getcommands6|Lekérdezi a (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|setcommands6|Beállítása (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys6|Eltávolított kulcsok (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|totalkeys6|Összes kulcs (szilánkok 6)|Darabszám|Maximális||Nincs dimenzió|
|expiredkeys6|Lejárt kulcsokkal (szilánkok 6)|Mennyiség|Összes||Nincs dimenzió|
|usedmemory6|Használt memória (szilánkok 6)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss6|RSS (szilánkok 6) a használt memória|Bájtok|Maximális||Nincs dimenzió|
|serverLoad6|Kiszolgáló terhelését (szilánkok 6)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite6|Gyorsítótár-írási (szilánkok 6)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead6|Gyorsítótár olvasható (szilánkok 6)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime6|Processzor (szilánkok 6)|Százalék|Maximális||Nincs dimenzió|
|connectedclients7|Csatlakozott ügyfelek (szilánkok 7)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed7|Összes művelet (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|cachehits7|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|cachemisses7|A gyorsítótárbeli (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|getcommands7|Lekérdezi a (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|setcommands7|Beállítása (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys7|Eltávolított kulcsok (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|totalkeys7|Összes kulcs (szilánkok 7)|Mennyiség|Maximális||Nincs dimenzió|
|expiredkeys7|Lejárt kulcsokkal (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|usedmemory7|Használt memória (szilánkok 7)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss7|Használt memória RSS (szilánkok 7)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad7|Kiszolgáló terhelését (szilánkok 7)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite7|Gyorsítótár-írási (szilánkok 7)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead7|Gyorsítótár olvasható (szilánkok 7)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime7|Processzor (szilánkok 7)|Százalék|Maximális||Nincs dimenzió|
|connectedclients8|Csatlakozott ügyfelek (szilánkok 8)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed8|Összes művelet (szilánkok 8)|Mennyiség|Összes||Nincs dimenzió|
|cachehits8|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|cachemisses8|A gyorsítótárbeli (szilánkok 8)|Mennyiség|Összes||Nincs dimenzió|
|getcommands8|Lekérdezi a (szilánkok 8)|Mennyiség|Összes||Nincs dimenzió|
|setcommands8|Beállítása (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys8|Eltávolított kulcsok (szilánkok 8)|Mennyiség|Összes||Nincs dimenzió|
|totalkeys8|Összes kulcs (szilánkok 8)|Mennyiség|Maximális||Nincs dimenzió|
|expiredkeys8|Lejárt kulcsokkal (szilánkok 8)|Mennyiség|Összes||Nincs dimenzió|
|usedmemory8|Használt memória (szilánkok 8)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss8|Használt memória RSS (szilánkok 8)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad8|Kiszolgáló terhelését (szilánkok 8)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite8|Gyorsítótár-írási (szilánkok 8)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead8|Gyorsítótár olvasható (szilánkok 8)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime8|Processzor (szilánkok 8)|Százalék|Maximális||Nincs dimenzió|
|connectedclients9|Csatlakozott ügyfelek (szilánkok 9)|Darabszám|Maximális||Nincs dimenzió|
|totalcommandsprocessed9|Összes művelet (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|cachehits9|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|cachemisses9|A gyorsítótárbeli (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|getcommands9|Lekérdezi a (szilánkok 9)|Mennyiség|Összes||Nincs dimenzió|
|setcommands9|Beállítása (szilánkok 9)|Mennyiség|Összes||Nincs dimenzió|
|evictedkeys9|Eltávolított kulcsok (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|totalkeys9|Összes kulcs (szilánkok 9)|Mennyiség|Maximális||Nincs dimenzió|
|expiredkeys9|Lejárt kulcsokkal (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|usedmemory9|Használt memória (szilánkok 9)|Bájtok|Maximális||Nincs dimenzió|
|usedmemoryRss9|Használt memória RSS (szilánkok 9)|Bájtok|Maximális||Nincs dimenzió|
|serverLoad9|Kiszolgáló terhelését (szilánkok 9)|Százalék|Maximális||Nincs dimenzió|
|cacheWrite9|Gyorsítótár-írási (szilánkok 9)|BytesPerSecond|Maximális||Nincs dimenzió|
|cacheRead9|Gyorsítótár olvasható (szilánkok 9)|BytesPerSecond|Maximális||Nincs dimenzió|
|percentProcessorTime9|Processzor (szilánkok 9)|Százalék|Maximális||Nincs dimenzió|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Processzor-százalékos aránya|Processzor-százalékos aránya|Százalék|Átlagos|A százalékos aránya, amelyek jelenleg használják a virtuális gépek lefoglalt számítási egység.|Nincs dimenzió|
|A hálózati|A hálózati|Bájtok|Összes|Minden hálózati interfészen a virtuális gépek (bejövő forgalom) által fogadott bájtok száma.|Nincs dimenzió|
|Kimenő hálózati|Kimenő hálózati|Bájtok|Összes|Időtúllépés a által a virtuális gépek (kimenő forgalmat) hálózati adaptereken bájtok száma.|Nincs dimenzió|
|Lemez sebessége olvasott bájt/mp|Lemez olvasása|BytesPerSecond|Átlagos|Átlagos bájtok beolvasása lemezről időszak ellenőrzése során.|Nincs dimenzió|
|Lemez írási bájtok/s|Lemez írási|BytesPerSecond|Átlagos|Átlagos bájt időszak ellenőrzése során lemezre írni.|Nincs dimenzió|
|Lemezolvasási műveletek másodpercenkénti száma|Lemezolvasási műveletek másodpercenkénti száma|CountPerSecond|Átlagos|Lemez olvasási iops-érték.|Nincs dimenzió|
|Lemez írási művelet/mp|Lemez írási művelet/mp|CountPerSecond|Átlagos|Lemez írási iops-érték.|Nincs dimenzió|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalCalls|Az összes hívás|Darabszám|Összes|Hívások száma összesen.|Nincs dimenzió|
|SuccessfulCalls|Sikeres hívás|Darabszám|Összes|Sikeres hívások száma.|Nincs dimenzió|
|TotalErrors|Az összes hiba száma|Mennyiség|Összes|Hibaüzenetet (HTTP-válasz kód 4xx vagy 5xx) végződő hívások száma.|Nincs dimenzió|
|BlockedCalls|Letiltott hívások|Mennyiség|Összes|Adott túllépése sebessége vagy kvótakorlátot hívások száma.|Nincs dimenzió|
|ServerErrors|Kiszolgáló hibák|Darabszám|Összes|Belső szolgáltatáshiba (HTTP válasz kód 5xx) végződő hívások száma.|Nincs dimenzió|
|ClientErrors|Ügyfél-hibák|Darabszám|Összes|Ügyfél kiszolgálóoldali hiba (HTTP válasz kód 4xx) végződő hívások száma.|Nincs dimenzió|
|DataIn|Az adatok|Bájtok|Összes|A bejövő adatok (bájt) mérete.|Nincs dimenzió|
|DataOut|Kimenő adatforgalmat|Bájtok|Összes|Bájtban kimenő adatok mérete.|Nincs dimenzió|
|Késés|Késés|Ideje ezredmásodpercben|Átlagos|Késés ezredmásodpercben.|Nincs dimenzió|
|CharactersTranslated|A lefordított karakter|Darabszám|Összes|Szöveg bejövő kérelemben szereplő karakterek száma.|Nincs dimenzió|
|SpeechSessionDuration|Beszéd munkamenet időtartama|másodperc|Összes|Teljes időtartam másodpercben beszéd-munkamenet.|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Processzor-százalékos aránya|Processzor-százalékos aránya|Százalék|Átlagos|A százalékos aránya, amelyek jelenleg használják a virtuális gépek lefoglalt számítási egység|Nincs dimenzió|
|A hálózati|A hálózati|Bájtok|Összes|Minden hálózati interfészen a virtuális gépek (bejövő forgalom) által fogadott bájtok száma|Nincs dimenzió|
|Kimenő hálózati|Kimenő hálózati|Bájtok|Összes|Időtúllépés a által a virtuális gépek (kimenő forgalmat) hálózati adaptereken bájtok száma|Nincs dimenzió|
|Lemez olvasott bájtok|Lemez olvasott bájtok|Bájtok|Összes|Az összes bájt beolvasása a lemezről figyelése időszak során|Nincs dimenzió|
|Lemez írt bájtok száma|Lemez írt bájtok száma|Bájtok|Összes|Figyelési időszak során lemezre írni, bájtok száma összesen|Nincs dimenzió|
|Lemezolvasási műveletek másodpercenkénti száma|Lemezolvasási műveletek másodpercenkénti száma|CountPerSecond|Átlagos|Lemez olvasási IOPS|Nincs dimenzió|
|Lemez írási művelet/mp|Lemez írási művelet/mp|CountPerSecond|Átlagos|Lemez írási IOPS|Nincs dimenzió|
|Fennmaradó CPU kreditek|Fennmaradó CPU kreditek|Darabszám|Átlagos|Elérhető kapacitásnövelés kreditek száma összesen|Nincs dimenzió|
|Felhasznált CPU kreditek|Felhasznált CPU kreditek|Darabszám|Átlagos|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Processzor-százalékos aránya|Processzor-százalékos aránya|Százalék|Átlagos|A százalékos aránya, amelyek jelenleg használják a virtuális gépek lefoglalt számítási egység|Nincs dimenzió|
|A hálózati|A hálózati|Bájtok|Összes|Minden hálózati interfészen a virtuális gépek (bejövő forgalom) által fogadott bájtok száma|Nincs dimenzió|
|Kimenő hálózati|Kimenő hálózati|Bájtok|Összes|Időtúllépés a által a virtuális gépek (kimenő forgalmat) hálózati adaptereken bájtok száma|Nincs dimenzió|
|Lemez olvasott bájtok|Lemez olvasott bájtok|Bájtok|Összes|Az összes bájt beolvasása a lemezről figyelése időszak során|Nincs dimenzió|
|Lemez írt bájtok száma|Lemez írt bájtok száma|Bájtok|Összes|Figyelési időszak során lemezre írni, bájtok száma összesen|Nincs dimenzió|
|Lemezolvasási műveletek másodpercenkénti száma|Lemezolvasási műveletek másodpercenkénti száma|CountPerSecond|Átlagos|Lemez olvasási IOPS|Nincs dimenzió|
|Lemez írási művelet/mp|Lemez írási művelet/mp|CountPerSecond|Átlagos|Lemez írási IOPS|Nincs dimenzió|
|Fennmaradó CPU kreditek|Fennmaradó CPU kreditek|Darabszám|Átlagos|Elérhető kapacitásnövelés kreditek száma összesen|Nincs dimenzió|
|Felhasznált CPU kreditek|Felhasznált CPU kreditek|Darabszám|Átlagos|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Processzor-százalékos aránya|Processzor-százalékos aránya|Százalék|Átlagos|A százalékos aránya, amelyek jelenleg használják a virtuális gépek lefoglalt számítási egység|Nincs dimenzió|
|A hálózati|A hálózati|Bájtok|Összes|Minden hálózati interfészen a virtuális gépek (bejövő forgalom) által fogadott bájtok száma|Nincs dimenzió|
|Kimenő hálózati|Kimenő hálózati|Bájtok|Összes|Időtúllépés a által a virtuális gépek (kimenő forgalmat) hálózati adaptereken bájtok száma|Nincs dimenzió|
|Lemez olvasott bájtok|Lemez olvasott bájtok|Bájtok|Összes|Az összes bájt beolvasása a lemezről figyelése időszak során|Nincs dimenzió|
|Lemez írt bájtok száma|Lemez írt bájtok száma|Bájtok|Összes|Figyelési időszak során lemezre írni, bájtok száma összesen|Nincs dimenzió|
|Lemezolvasási műveletek másodpercenkénti száma|Lemezolvasási műveletek másodpercenkénti száma|CountPerSecond|Átlagos|Lemez olvasási IOPS|Nincs dimenzió|
|Lemez írási művelet/mp|Lemez írási művelet/mp|CountPerSecond|Átlagos|Lemez írási IOPS|Nincs dimenzió|
|Fennmaradó CPU kreditek|Fennmaradó CPU kreditek|Mennyiség|Átlagos|Elérhető kapacitásnövelés kreditek száma összesen|Nincs dimenzió|
|Felhasznált CPU kreditek|Felhasznált CPU kreditek|Darabszám|Átlagos|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DCIApiCalls|Ügyfél Insights API-hívások|Mennyiség|Összes||Nincs dimenzió|
|DCIMappingImportOperationSuccessfulLines|Leképezési importálási művelet sikeres sorok|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationFailedLines|Sorok leképezése az importálási művelet sikertelen volt.|Mennyiség|Összes||Nincs dimenzió|
|DCIMappingImportOperationTotalLines|Leképezési importálási művelet teljes sorok|Darabszám|Összes||Nincs dimenzió|
|DCIMappingImportOperationRuntimeInSeconds|Leképezési importálási művelet futásidejű másodpercben.|másodperc|Összes||Nincs dimenzió|
|DCIOutboundProfileExportSucceeded|Kimenő profil exportálása sikeres volt.|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundProfileExportFailed|Nem sikerült kimenő profil exportálása|Darabszám|Összes||Nincs dimenzió|
|DCIOutboundProfileExportDuration|Kimenő profil exportálása időtartama|másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiExportSucceeded|Kimenő Kpi exportálása sikeres volt.|Mennyiség|Összes||Nincs dimenzió|
|DCIOutboundKpiExportFailed|Kimenő Kpi az exportálás sikertelen.|Mennyiség|Összes||Nincs dimenzió|
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
|ImportASAValuesSucceeded|Importálás ASA értékek száma sikeres volt.|Mennyiség|Összes||Nincs dimenzió|
|DCIProfilesCount|Profil példányok száma|Mennyiség|utolsó||Nincs dimenzió|
|DCIInteractionsPerMonthCount|Kapcsolati hónap száma|Darabszám|utolsó||Nincs dimenzió|
|DCIKpisCount|KPI száma|Mennyiség|utolsó||Nincs dimenzió|
|DCISegmentsCount|Szegmens száma|Darabszám|utolsó||Nincs dimenzió|
|DCIPredictiveMatchPoliciesCount|A prediktív egyezés száma|Darabszám|utolsó||Nincs dimenzió|
|DCIPredictionsCount|Előrejelzés száma|Darabszám|utolsó||Nincs dimenzió|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Darabszám|Összes|Sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedFailure|A sikertelen feladatok|Mennyiség|Összes|A sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedCancelled|Megszakított feladatok|Darabszám|Összes|Megszakított feladatok száma.|Nincs dimenzió|
|JobAUEndedSuccess|Sikeres AU idő|másodperc|Összes|Sikertelen feladatok teljes AU ideje.|Nincs dimenzió|
|JobAUEndedFailure|Nem sikerült AU idő|másodperc|Összes|A sikertelen feladatok teljes AU ideje.|Nincs dimenzió|
|JobAUEndedCancelled|Megszakított AU idő|másodperc|Összes|Megszakított feladatok teljes AU ideje.|Nincs dimenzió|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Teljes tárterület|Bájtok|Maximális|A fiókban tárolt adatok teljes mennyisége.|Nincs dimenzió|
|DataWritten|Az adatok írása|Bájtok|Összes|Teljes adatmennyiség ír a fiókba.|Nincs dimenzió|
|DataRead|Adatok olvasása|Bájtok|Összes|Az adatok olvasása-fiókból.|Nincs dimenzió|
|WriteRequests|Írási kérelmeket szolgál|Darabszám|Összes|Adatok száma írási kérelmeket szolgál ahhoz a fiókhoz.|Nincs dimenzió|
|ReadRequests|Olvasási kérések|Darabszám|Összes|Adatok száma az olvasási kérésekre ahhoz a fiókhoz.|Nincs dimenzió|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzor százalékban|Százalék|Átlagos|Processzor százalékban|Nincs dimenzió|
|compute_limit|Számítási egység korlátot|Darabszám|Átlagos|Számítási egység korlátot|Nincs dimenzió|
|compute_consumption_percent|Számítási egység százalékos aránya|Százalék|Átlagos|Számítási egység százalékos aránya|Nincs dimenzió|
|memory_percent|Memória százaléka|Százalék|Átlagos|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százaléka|Százalék|Átlagos|IO-százaléka|Nincs dimenzió|
|storage_percent|Tárolási százalékos aránya|Százalék|Átlagos|Tárolási százalékos aránya|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájtok|Átlagos|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájtok|Átlagos|Tárolási kapacitása|Nincs dimenzió|
|active_connections|Aktív kapcsolatok száma|Darabszám|Átlagos|Aktív kapcsolatok száma|Nincs dimenzió|
|connections_failed|Nem sikerült kapcsolatok száma|Mennyiség|Átlagos|Nem sikerült kapcsolatok száma|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzor százalékban|Százalék|Átlagos|Processzor százalékban|Nincs dimenzió|
|compute_limit|Számítási egység korlátot|Mennyiség|Átlagos|Számítási egység korlátot|Nincs dimenzió|
|compute_consumption_percent|Számítási egység százalékos aránya|Százalék|Átlagos|Számítási egység százalékos aránya|Nincs dimenzió|
|memory_percent|Memória százaléka|Százalék|Átlagos|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százaléka|Százalék|Átlagos|IO-százaléka|Nincs dimenzió|
|storage_percent|Tárolási százalékos aránya|Százalék|Átlagos|Tárolási százalékos aránya|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájtok|Átlagos|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájtok|Átlagos|Tárolási kapacitása|Nincs dimenzió|
|active_connections|Aktív kapcsolatok száma|Darabszám|Átlagos|Aktív kapcsolatok száma|Nincs dimenzió|
|connections_failed|Nem sikerült kapcsolatok száma|Darabszám|Átlagos|Nem sikerült kapcsolatok száma|Nincs dimenzió|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetria üzenet küldési kísérlet|Darabszám|Összes|Az IoT hub küldendő próbált eszköz-felhő telemetriai üzenetek száma|Nincs dimenzió|
|d2c.telemetry.ingress.SUCCESS|Küldött telemetriai üzenetek|Darabszám|Összes|Sikerült elküldeni az IoT hub eszköz-felhő telemetriai üzenetek száma|Nincs dimenzió|
|c2d.commands.egress.Complete.SUCCESS|A parancsok befejeződött|Darabszám|Összes|Az eszköz által végrehajtott felhő eszközre parancsok száma|Nincs dimenzió|
|c2d.commands.egress.Abandon.SUCCESS|Elhagyott parancsok|Darabszám|Összes|Az eszköz által félbe hagyott felhő eszközre parancsok száma|Nincs dimenzió|
|c2d.commands.egress.Reject.SUCCESS|Elutasított parancsok|Darabszám|Összes|Az eszköz által elutasított felhő eszközre parancsok száma|Nincs dimenzió|
|devices.totalDevices|Eszközök teljes száma|Darabszám|Összes|Az IoT hub regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök|Mennyiség|Összes|Az IoT hub csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.SUCCESS|Telemetria kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt végpontok (összesen)|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Az eldobott üzenetek|Darabszám|Összes|Dobva, mert a kézbesítési végpont lett elhalt üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.Orphaned|Árva üzenetek|Darabszám|Összes|A nem megfelelő bármely útvonalakat, beleértve a tartalék útvonal üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.Invalid|Érvénytelen üzenet|Darabszám|Összes|A végpont alkalmazással való inkompatibilitás miatt nem kézbesített üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Tartalék feltételnek megfelelő üzenetek|Darabszám|Összes|A tartalék végpont üzenetek száma|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Az Event Hubs végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt az Event Hubs végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Az Event Hubs végpontok üzenet késés|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között az Eseményközpont végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Service Bus-üzenetsorba végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-üzenetsorba végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|A Service Bus-üzenetsorba végpontokhoz üzenet késés|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-üzenetsorba végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Service Bus-témakörbe végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-témakörbe végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|A Service Bus-témakörbe végpontokhoz üzenet késés|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-témakörbe végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|A beépített végpont (üzenet/események) kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt a beépített végpont (üzenet/esemény)|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Üzenet várakozási ideje a beépített végpont (üzenet/esemény)|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között történő (üzenet/esemény), beépített végpont ezredmásodpercben |Nincs dimenzió|
|d2c.endpoints.egress.Storage|Tárolási végpontok kézbesített üzenetek|Mennyiség|Összes|Üzenetek írása sikeres volt tárolási végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.Storage|Üzenet késés tárolási végpontok|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a tárolási végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.Storage.bytes|Adatok tárhelyen|Bájtok|Összes|Adatok tárolási végpontok írt bájtok száma|Nincs dimenzió|
|d2c.endpoints.egress.Storage.blobs|Blobok tárhelyen|Mennyiség|Összes|BLOB storage végpontok írt száma|Nincs dimenzió|
|d2c.Twin.Read.SUCCESS|Sikeres iker olvassa be az eszközökön|Darabszám|Összes|Az összes sikeres a két eszköz által kezdeményezett olvasások száma.|Nincs dimenzió|
|d2c.Twin.Read.failure|Nem sikerült a kettős olvasások eszközökről|Darabszám|Összes|Teljes számát a két eszköz által kezdeményezett olvasás sikertelen volt.|Nincs dimenzió|
|d2c.Twin.Read.size|A két válasz mérete olvassa be az eszközökön|Bájtok|Átlagos|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres iker olvasási műveletek.|Nincs dimenzió|
|d2c.Twin.Update.SUCCESS|Sikeres iker frissítések eszközökről|Mennyiség|Összes|A két eszköz által kezdeményezett összes sikeres frissítések száma.|Nincs dimenzió|
|d2c.Twin.Update.failure|Nem sikerült a két frissítések eszközökről|Darabszám|Összes|A teljes számát a két eszköz által kezdeményezett frissítések nem sikerült.|Nincs dimenzió|
|d2c.Twin.Update.size|A két frissítések eszközökről mérete|Bájtok|Átlagos|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres iker frissítések.|Nincs dimenzió|
|c2d.methods.SUCCESS|Sikeres közvetlen módszer meghívásához|Darabszám|Összes|Az összes sikeres közvetlen módszer hívások száma.|Nincs dimenzió|
|c2d.methods.failure|Nem sikerült a közvetlen módszer meghívásához|Darabszám|Összes|A teljes számát a közvetlen metódushívások nem sikerült.|Nincs dimenzió|
|c2d.methods.requestSize|A közvetlen módszer meghívásához kérelem mérete|Bájtok|Átlagos|Az átlagos, a minimális és a közvetlen módszer minden sikeres kérelmek maximális.|Nincs dimenzió|
|c2d.methods.responseSize|Válasz mérete közvetlen módszer meghívásához|Bájtok|Átlagos|Az átlagos, a minimális és a maximális minden sikeres közvetlen módszer válaszok.|Nincs dimenzió|
|c2d.Twin.Read.SUCCESS|Sikeres iker háttérből olvassa be|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett iker olvasási műveletek számát.|Nincs dimenzió|
|c2d.Twin.Read.failure|Sikertelen a két háttérből olvassa be|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett olvasások nem sikerült.|Nincs dimenzió|
|c2d.Twin.Read.size|A válasz méretének iker olvasások háttérből|Bájtok|Átlagos|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett iker olvasási műveletek.|Nincs dimenzió|
|c2d.Twin.Update.SUCCESS|Sikeres iker frissítések háttérből|Mennyiség|Összes|Az összes sikeres back-end által kezdeményezett iker frissítések száma.|Nincs dimenzió|
|c2d.Twin.Update.failure|Sikertelen a két frissítések háttérből|Mennyiség|Összes|Teljes számát a két back-end által kezdeményezett frissítések nem sikerült.|Nincs dimenzió|
|c2d.Twin.Update.size|A két frissítések háttérből mérete|Bájtok|Átlagos|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett iker frissítések.|Nincs dimenzió|
|twinQueries.success|Sikeres iker lekérdezések|Darabszám|Összes|Minden sikeres iker lekérdezések száma.|Nincs dimenzió|
|twinQueries.failure|Sikertelen a két lekérdezések|Darabszám|Összes|Összes sikertelen iker lekérdezések száma.|Nincs dimenzió|
|twinQueries.resultSize|A két lekérdezések eredmény méretének|Bájtok|Átlagos|Az átlagos, a minimális és a maximális az eredmény méretének minden sikeres iker lekérdezések.|Nincs dimenzió|
|jobs.createTwinUpdateJob.success|A sikeres és a két frissítési feladatok|Mennyiség|Összes|Minden sikeres létrehozása két frissítési feladatok számát.|Nincs dimenzió|
|jobs.createTwinUpdateJob.failure|Sikertelen és a két frissítési feladatok|Darabszám|Összes|A két frissítési feladatok során létrehozására tett sikertelen kísérlet száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.success|A metódus meghívása feladatok sikeres létrehozása|Darabszám|Összes|Minden sikeres létrehozása közvetlen metódus meghívása feladatok száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.failure|A metódus meghívása feladatok sikertelen számát|Darabszám|Összes|A közvetlen metódus meghívása feladatok során létrehozására tett sikertelen kísérlet száma.|Nincs dimenzió|
|jobs.listJobs.success|Lista feladatok sikeres hívások|Mennyiség|Összes|Az összes sikeres indított hívások száma a lista feladatok.|Nincs dimenzió|
|jobs.listJobs.failure|Nem sikerült a lista feladatok hívások|Darabszám|Összes|A feladatok lista összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.cancelJob.success|Megszakított feladatok sikeres|Darabszám|Összes|Az összes sikeres indított hívások száma megszakítja a feladatot.|Nincs dimenzió|
|jobs.cancelJob.failure|Sikertelen feladat sikertelen|Darabszám|Összes|A visszavonni a feladatot az összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.queryJobs.success|Feladat sikeres lekérdezések|Darabszám|Összes|A lekérdezés feladatok minden sikeres hívások száma.|Nincs dimenzió|
|jobs.queryJobs.failure|Sikertelen feladat-lekérdezések|Darabszám|Összes|A lekérdezés feladatok összes sikertelen hívások száma.|Nincs dimenzió|
|jobs.Completed|Befejezett feladatokhoz|Darabszám|Összes|Az összes befejezett feladatok száma.|Nincs dimenzió|
|jobs.Failed|A sikertelen feladatok|Darabszám|Összes|Összes sikertelen feladatok száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Sávszélesség-szabályozási hibák száma|Mennyiség|Összes|Azelőtt gyorsítja fel az eszköz átviteli miatt szabályozás hibák száma|Nincs dimenzió|
|dailyMessageQuotaUsed|Használt üzenetek teljes száma|Darabszám|Átlagos|Jelenleg használt teljes üzenetek száma|Nincs dimenzió|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Darabszám|Összes|Kísérlet történt az eszköz-regisztrációinak száma|ProvisioningServiceName, IotHubName, állapota|
|DeviceAssignments|Hozzárendelt eszközök|Darabszám|Összes|Az IoT-központ rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Állapotigazolási kísérletek|Darabszám|Összes|Próbált eszköz tanúsítványok száma|ProvisioningServiceName, állapotát, a protokoll|

## <a name="microsoftdocumentdbdatabaseaccounts-cosmosdb"></a>Microsoft.DocumentDB/databaseAccounts (CosmosDB)
|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Kérelmek teljes száma|Darabszám|Mennyiség|Kérelmek száma|DatabaseAccount, CollectionName, DatabaseName, régió, StatusCode|
|MongoRequests|Mongo kérelmek|Darabszám|Mennyiség|Mongo kérelmek száma|DatabaseAccount, CollectionName, DatabaseName, régió, ErrorCode, CommandName|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|INREQS|Bejövő küldési kérelmek|Darabszám|Összes|Teljes bejövő névtér kérelem elküldése|Nincs dimenzió|
|SUCCREQ|Sikeres kérelmei|Darabszám|Összes|A sikeres kérelmek teljes névtér|Nincs dimenzió|
|FAILREQ|Sikertelen kérelmek|Darabszám|Összes|A sikertelen kérelmek teljes névtér|Nincs dimenzió|
|SVRBSY|Kiszolgáló elfoglalt hibák|Mennyiség|Összes|Névtér a teljes kiszolgáló foglalt hibák|Nincs dimenzió|
|INTERR|Belső kiszolgálóhibákat|Darabszám|Összes|Teljes belső kiszolgálóhibákat névtér|Nincs dimenzió|
|MISCERR|Egyéb hibák|Darabszám|Összes|A sikertelen kérelmek teljes névtér|Nincs dimenzió|
|INMSGS|(Elavult a) bejövő üzenetek|Darabszám|Összes|Teljes bejövő üzenetek névtér. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrika|Nincs dimenzió|
|EHINMSGS|A bejövő üzenetek|Darabszám|Összes|Egy névtér teljes bejövő üzenete|Nincs dimenzió|
|OUTMSGS|(Elavult) kimenő üzenetek|Darabszám|Összes|A teljes kimenő üzenetek névtér. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrika|Nincs dimenzió|
|EHOUTMSGS|Kimenő üzenetek|Darabszám|Összes|Teljes kimenő üzenetek névtér|Nincs dimenzió|
|EHINMBS|Bejövő bájtok (elavult)|Bájtok|Összes|Event Hub bejövő üzenet átviteli névtér. Ez a metrika elavult. Ehelyett használja a bejövő bájtok metrika|Nincs dimenzió|
|EHINBYTES|Bejövő bájtok|Bájtok|Összes|Event Hub bejövő üzenet átviteli névtér|Nincs dimenzió|
|EHOUTMBS|Kimenő bájtok (elavult)|Bájtok|Összes|Event Hub kimenő üzenet átviteli névtér. Ez a metrika elavult. Ehelyett használja a kimenő bájt metrika|Nincs dimenzió|
|EHOUTBYTES|Kimenő bájtok|Bájtok|Összes|Event Hub kimenő üzenet átviteli névtér|Nincs dimenzió|
|EHABL|Archív várakozó üzenetek|Darabszám|Összes|Hub archív eseményüzeneteket a névtér várakozó fájlok|Nincs dimenzió|
|EHAMSGS|Üzenetek archiválása|Darabszám|Összes|Az Event Hubs archivált névtérben levő üzenetek|Nincs dimenzió|
|EHAMBS|Archív üzenet átviteli sebesség|Bájtok|Összes|Az Event Hubs archivált üzenet átviteli névtérben|Nincs dimenzió|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrika értékét|Darabszám|Átlagos|Az automatikus skálázás végrehajtásakor által kiszámított érték|MetricTriggerSource|
|MetricThreshold|Metrika küszöbérték|Darabszám|Átlagos|A megadott automatikus skálázási küszöbértéket, automatikus skálázás futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Darabszám|Átlagos|A kapacitás jelentett automatikus skálázás végrehajtásakor azt.|Nincs dimenzió|
|ScaleActionsInitiated|Skálázási műveletet kezdeményezett|Mennyiség|Összes|A méretezési művelet irányát.|ScaleDirection|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Futtatása megkezdődött|Darabszám|Összes|Az elindított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCompleted|Futtatása befejeződött|Darabszám|Összes|A befejezett munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsSucceeded|Futtatása sikeres volt.|Mennyiség|Összes|A sikeres munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsFailed|Nem sikerült fut.|Darabszám|Összes|A sikertelen munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCancelled|Megszakítva fut.|Mennyiség|Összes|A megszakított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunLatency|Futtassa a késés|másodperc|Átlagos|A befejezett munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunSuccessLatency|Futtassa a sikeres késés|másodperc|Átlagos|A sikeres munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunThrottledEvents|Futtassa a szabályozottan halmozott események|Mennyiség|Összes|A munkafolyamat-műveletek vagy -eseményindítók által elindított események száma.|Nincs dimenzió|
|RunFailurePercentage|Futtassa a hiba százalékos aránya|Százalék|Összes|Százalékos aránya a munkafolyamat futtatása sikertelen.|Nincs dimenzió|
|ActionsStarted|A műveletek indítása |Mennyiség|Összes|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|A műveletek befejezése |Mennyiség|Összes|A befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|A művelet sikeres volt. |Mennyiség|Összes|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Nem sikerült műveletek|Darabszám|Összes|A sikertelen munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Darabszám|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|A művelet késés |másodperc|Átlagos|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|A művelet sikeres késés |másodperc|Átlagos|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|A művelet Halmozódni események|Mennyiség|Összes|A munkafolyamat-műveletek által elindított események száma.|Nincs dimenzió|
|TriggersStarted|Eseményindítók indítása |Darabszám|Összes|Az elindított munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersCompleted|Eseményindítók befejeződött |Mennyiség|Összes|A befejezett munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSucceeded|Eseményindítók sikeres volt. |Darabszám|Összes|A sikeres munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFailed|Nem sikerült az eseményindítók |Mennyiség|Összes|A sikertelen munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott eseményindítók|Darabszám|Összes|A kihagyott munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFired|Eseményindítók következik |Mennyiség|Összes|Az aktivált munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggerLatency|Eseményindító késés |másodperc|Átlagos|A befejezett munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerFireLatency|Eseményindító tűz késés |másodperc|Átlagos|Az aktivált munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerSuccessLatency|Eseményindító sikeres késés |másodperc|Átlagos|A sikeres munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerThrottledEvents|Eseményindító Halmozódni események|Mennyiség|Összes|A munkafolyamat-eseményindítók által elindított események száma.|Nincs dimenzió|
|BillableActionExecutions|Számlázható művelet végrehajtások|Darabszám|Összes|Munkafolyamat-művelet végrehajtások első számlázva száma.|Nincs dimenzió|
|BillableTriggerExecutions|Számlázható eseményindító végrehajtások|Darabszám|Összes|Munkafolyamat eseményindító végrehajtások első számlázva száma.|Nincs dimenzió|
|TotalBillableExecutions|Teljes számlázható végrehajtások|Darabszám|Összes|Első számlázva munkafolyamat végrehajtások száma.|Nincs dimenzió|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|VIP rendelkezésre állása|Darabszám|Átlagos|Rendelkezésre állási VIP-végpontok mintavételi eredmények alapján|Vipaddress értéke, VipPort|
|DipAvailability|DIP rendelkezésre állása|Mennyiség|Átlagos|Rendelkezésre állási, a DIP-végpontok mintavételi eredmények alapján|ProtocolType, DipPort, vipaddress értéke, VipPort, DipAddress|
|ByteCount|Bájtok száma|Darabszám|Összes|Időtartamon belül átvitt bájtok teljes száma|Vipaddress értéke, VipPort, iránya|
|PacketCount|Csomagok száma|Mennyiség|Összes|Időtartamon belül küldött csomagok teljes száma|Vipaddress értéke, VipPort, iránya|
|SYNCount|Szinkronizálás a mi száma|Mennyiség|Összes|Továbbított időtartamon belül SZIN csomagok száma összesen|Vipaddress értéke, VipPort, iránya|
|SnatConnectionCount|SNAT kapcsolatok száma|Mennyiség|Összes|Időtartamon belül létrehozott új SNAT kapcsolatok száma|Vipaddress értéke, DipAddress, ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|CountPerSecond|Átlagos|Bejövő csomagok DDoS|Nincs dimenzió|
|PacketsDroppedDDoS|Eldobott DDoS bejövő csomagok|CountPerSecond|Átlagos|Eldobott DDoS bejövő csomagok|Nincs dimenzió|
|PacketsForwardedDDoS|DDoS továbbított bejövő csomagok|CountPerSecond|Átlagos|DDoS továbbított bejövő csomagok|Nincs dimenzió|
|TCPPacketsInDDoS|A bejövő TCP-csomagok DDoS|CountPerSecond|Átlagos|A bejövő TCP-csomagok DDoS|Nincs dimenzió|
|TCPPacketsDroppedDDoS|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Átlagos|Bejövő TCP-csomagok eldobott DDoS|Nincs dimenzió|
|TCPPacketsForwardedDDoS|DDoS továbbított bejövő TCP-csomagok|CountPerSecond|Átlagos|DDoS továbbított bejövő TCP-csomagok|Nincs dimenzió|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|CountPerSecond|Átlagos|Bejövő UDP-csomagok DDoS|Nincs dimenzió|
|UDPPacketsDroppedDDoS|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Átlagos|Bejövő UDP-csomagok eldobott DDoS|Nincs dimenzió|
|UDPPacketsForwardedDDoS|DDoS továbbított bejövő UDP-csomagok|CountPerSecond|Átlagos|DDoS továbbított bejövő UDP-csomagok|Nincs dimenzió|
|BytesInDDoS|Bejövő bájtok DDoS|BytesPerSecond|Átlagos|Bejövő bájtok DDoS|Nincs dimenzió|
|BytesDroppedDDoS|Bejövő bájtok eldobott DDoS|BytesPerSecond|Átlagos|Bejövő bájtok eldobott DDoS|Nincs dimenzió|
|BytesForwardedDDoS|DDoS továbbított bejövő bájtok|BytesPerSecond|Átlagos|DDoS továbbított bejövő bájtok|Nincs dimenzió|
|TCPBytesInDDoS|Bejövő TCP bájtok DDoS|BytesPerSecond|Átlagos|Bejövő TCP bájtok DDoS|Nincs dimenzió|
|TCPBytesDroppedDDoS|Bejövő TCP-bájt eldobott DDoS|BytesPerSecond|Átlagos|Bejövő TCP-bájt eldobott DDoS|Nincs dimenzió|
|TCPBytesForwardedDDoS|DDoS továbbított bejövő TCP bájt|BytesPerSecond|Átlagos|DDoS továbbított bejövő TCP bájt|Nincs dimenzió|
|UDPBytesInDDoS|Bejövő UDP bájtok DDoS|BytesPerSecond|Átlagos|Bejövő UDP bájtok DDoS|Nincs dimenzió|
|UDPBytesDroppedDDoS|Bejövő UDP bájt eldobott DDoS|BytesPerSecond|Átlagos|Bejövő UDP bájt eldobott DDoS|Nincs dimenzió|
|UDPBytesForwardedDDoS|DDoS továbbított bejövő UDP bájt|BytesPerSecond|Átlagos|DDoS továbbított bejövő UDP bájt|Nincs dimenzió|
|IfUnderDDoSAttack|DDoS alatt támadásokkal vagy sem|Darabszám|Átlagos|DDoS alatt támadásokkal vagy sem|Nincs dimenzió|
|DDoSTriggerTCPPackets|A bejövő TCP-csomagok DDoS megoldás elindítása|CountPerSecond|Átlagos|A bejövő TCP-csomagok DDoS megoldás elindítása|Nincs dimenzió|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok DDoS megoldás elindítása|CountPerSecond|Átlagos|Bejövő UDP-csomagok DDoS megoldás elindítása|Nincs dimenzió|
|VipAvailability|Rendelkezésre állás|Mennyiség|Átlagos|IP-cím-átlagos rendelkezésre állás időn belül|Port|
|ByteCount|Bájtok száma|Darabszám|Összes|Időtartamon belül átvitt bájtok teljes száma|Port, iránya|
|PacketCount|Csomagok száma|Darabszám|Összes|Időtartamon belül küldött csomagok teljes száma|Port, iránya|
|SynCount|Szinkronizálás a mi száma|Darabszám|Összes|Továbbított időtartamon belül SZIN csomagok száma összesen|Port, iránya|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljesítmény|Teljesítmény|BytesPerSecond|Átlagos|Az Alkalmazásátjáró rendelkezik kiszolgált másodpercenként bájtok száma|Nincs dimenzió|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Bújtatás sávszélesség|BytesPerSecond|Átlagos|Egy alagút bájt / s átlagos sávszélesség|Kapcsolat neve, RemoteIP|
|TunnelPeakBandwidth|Bújtatás maximális sávszélesség|BytesPerSecond|Átlagos|Maximális sávszélesség a alagút bájt / s|Kapcsolat neve, RemoteIP|
|TunnelEgressBytes|Bújtatás kilépő bájt|Bájtok|Átlagos|Kimenő bájtok öt percenként egy alagút|Kapcsolat neve, RemoteIP|
|TunnelIngressBytes|Bújtatás érkező bájtok|Bájtok|Átlagos|Bejövő bájtok öt percenként egy alagút|Kapcsolat neve, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagok|Darabszám|Átlagos|Egy alagút öt percenként kimenő csomagok száma|Kapcsolat neve, RemoteIP|
|TunnelIngressPackets|Bújtatás érkező csomagok|Darabszám|Átlagos|Egy alagút öt percenként bejövő csomagok száma|Kapcsolat neve, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Bújtatás kilépő TS eltérés csomagjai|Darabszám|Átlagos|Kimenő forgalom választó eltérés öt percenként alagutat a csomagjai|Kapcsolat neve, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Bújtatás érkező TS eltérés csomagjai|Darabszám|Átlagos|Bejövő forgalom választó eltérés öt percenként alagutat a csomagjai|Kapcsolat neve, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesIn|BytesIn|Darabszám|Összes|Bájtok ingressing Azure|Nincs dimenzió|
|BytesOut|BytesOut|Mennyiség|Összes|Egressing Azure bájt|Nincs dimenzió|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlagos|A BITS ingressing Azure / másodperc|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlagos|A BITS Azure egressing / másodperc|Nincs dimenzió|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Lekérdezések által visszaadott végpont|Mennyiség|Összes|Szám, ahányszor a Traffic Manager-végpont adott vissza az adott időkereten belül|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végpont végpont állapota|Mennyiség|Maximális|Ha a végpontok mintavételi állapota "engedélyezve" 1, egyébként pedig 0.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|registration.all|Regisztrációs műveletek|Darabszám|Összes|Minden sikeres regisztráció műveletet (a létrehozott frissítések lekérdezések és a törlések) száma. |Nincs dimenzió|
|registration.Create|Regisztrációs műveletek létrehozása|Mennyiség|Összes|Minden sikeres regisztráció és a száma.|Nincs dimenzió|
|registration.Update|Regisztráció a frissítési műveletek|Mennyiség|Összes|Minden sikeres regisztráció frissítések száma.|Nincs dimenzió|
|registration.Get|Regisztrációs olvasási műveletek|Darabszám|Összes|Minden sikeres regisztráció lekérdezések száma.|Nincs dimenzió|
|registration.delete|Regisztrációs Delete művelet|Mennyiség|Összes|Minden sikeres regisztráció törlési száma.|Nincs dimenzió|
|bejövő|A bejövő üzenetek|Mennyiség|Összes|Az összes sikeres számát az API-hívásokban küldése. |Nincs dimenzió|
|Incoming.Scheduled|Ütemezett leküldéses értesítések küldése|Darabszám|Összes|Ütemezett leküldéses értesítések megszakítva|Nincs dimenzió|
|Incoming.Scheduled.Cancel|Ütemezett leküldéses értesítések megszakítva|Darabszám|Összes|Ütemezett leküldéses értesítések megszakítva|Nincs dimenzió|
|Scheduled.Pending|Függőben lévő ütemezett értesítések|Darabszám|Összes|Függőben lévő ütemezett értesítések|Nincs dimenzió|
|Installation.all|Telepítési műveletek|Mennyiség|Összes|Telepítési műveletek|Nincs dimenzió|
|Installation.Get|Telepítési műveletek beolvasása|Mennyiség|Összes|Telepítési műveletek beolvasása|Nincs dimenzió|
|Installation.upsert|Telepítés-létrehozási és -frissítési műveletek|Darabszám|Összes|Telepítés-létrehozási és -frissítési műveletek|Nincs dimenzió|
|Installation.Patch|Javítás telepítési műveletek|Mennyiség|Összes|Javítás telepítési műveletek|Nincs dimenzió|
|Installation.delete|Telepítési műveletek törlése|Darabszám|Összes|Telepítési műveletek törlése|Nincs dimenzió|
|outgoing.allpns.SUCCESS|Sikeres értesítések|Darabszám|Összes|Minden sikeres értesítések száma.|Nincs dimenzió|
|outgoing.allpns.invalidpayload|Hasznos hibák|Mennyiség|Összes|Leküldéses értesítések, amelyek nem sikerült, mert a pns-sel rossz hasznos hibát adott vissza a száma.|Nincs dimenzió|
|outgoing.allpns.pnserror|Külső értesítési rendszerhibák|Darabszám|Összes|A leküldéses értesítések, amelyek miatt sikertelen volt egy probléma merült fel a pns-sel (nem tartalmazza a hitelesítési problémákat az) száma.|Nincs dimenzió|
|outgoing.allpns.channelerror|Csatorna által okozott hibák|Darabszám|Összes|A száma, amelyek nem sikerült, mert a csatorna érvénytelen nincs társítva a megfelelő alkalmazás szabályozva, vagy lejárt a leküldéses értesítések.|Nincs dimenzió|
|outgoing.allpns.badorexpiredchannel|Rossz vagy lejárt csatorna által okozott hibák|Darabszám|Összes|A száma leküldéses értesítések, amelyek nem sikerült, mert a csatorna/token/registrationId a regisztrációhoz lejárt vagy érvénytelen.|Nincs dimenzió|
|outgoing.wns.SUCCESS|WNS sikeres értesítések|Mennyiség|Összes|Minden sikeres értesítések száma.|Nincs dimenzió|
|outgoing.wns.invalidcredentials|WNS engedélyezési hibái (érvénytelen hitelesítő adatok)|Darabszám|Összes|A száma leküldéses értesítések, amelyek nem sikerült, mert a pns-sel nem fogadta el a megadott hitelesítő adatok, vagy a hitelesítő adatok le vannak tiltva. (Windows Live nem ismeri fel a hitelesítő adatok).|Nincs dimenzió|
|outgoing.wns.badchannel|WNS hibás csatorna hiba|Darabszám|Összes|A száma, amelyek nem sikerült, mert a rendszer nem ismerte fel a regisztrációhoz ChannelURI leküldéses értesítések (WNS állapota: 404 nem található).|Nincs dimenzió|
|outgoing.wns.expiredchannel|WNS lejárt csatorna hiba|Darabszám|Összes|A száma, amelyek nem sikerült, mert lejárt a ChannelURI leküldéses értesítések (WNS állapota: 410 Gone).|Nincs dimenzió|
|outgoing.wns.throttled|WNS Halmozódni értesítések|Darabszám|Összes|A száma, amelyek nem sikerült, mert ez az alkalmazás a WNS szabályozás leküldéses értesítések (WNS állapota: 406 nem elfogadható).|Nincs dimenzió|
|outgoing.wns.tokenproviderunreachable|WNS engedélyezési hibái (nem érhető el)|Mennyiség|Összes|Windows Live nem érhető el.|Nincs dimenzió|
|outgoing.wns.invalidtoken|WNS engedélyezési hibái (lexikális elem érvénytelen)|Mennyiség|Összes|Érvénytelen a jogkivonat segítségével WNS (WNS állapota: 401 nem engedélyezett).|Nincs dimenzió|
|outgoing.wns.wrongtoken|WNS engedélyezési hibái (helytelen Token)|Mennyiség|Összes|A wns-ből a megadott jogkivonat nem érvényes, de egy másik alkalmazással (WNS állapota: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztrációhoz ChannelURI társítva egy másik alkalmazás. Ellenőrizze, hogy az ügyfélalkalmazás ugyanazt az alkalmazást, amelynek hitelesítő adatait az értesítési központban vannak társítva.|Nincs dimenzió|
|outgoing.wns.invalidnotificationformat|WNS érvénytelen értesítés formátuma|Mennyiség|Összes|Az értesítési formátuma érvénytelen (WNS állapota: 400). Vegye figyelembe, hogy a wns-ből nem utasíthatja el minden érvénytelen Payload van jelen.|Nincs dimenzió|
|outgoing.wns.invalidnotificationsize|WNS – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az értesítési tartalom mérete túl nagy (WNS állapota: 413).|Nincs dimenzió|
|outgoing.wns.channelthrottled|WNS csatorna Halmozódni|Darabszám|Összes|Az értesítés el lett dobva, mert a regisztrációhoz ChannelURI szabályozva van (WNS válaszfejlécet: X-WNS-NotificationStatus:channelThrottled).|Nincs dimenzió|
|outgoing.wns.channeldisconnected|WNS csatorna kapcsolata megszakadt|Darabszám|Összes|Az értesítés el lett dobva, mert a regisztrációhoz ChannelURI szabályozva van (WNS válaszfejlécet: X-WNS-DeviceConnectionStatus: leválasztva).|Nincs dimenzió|
|outgoing.wns.dropped|WNS eldobott értesítések|Darabszám|Összes|Az értesítés el lett dobva, mert a regisztrációhoz ChannelURI szabályozva van (X-WNS-NotificationStatus: az eldobott, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincs dimenzió|
|outgoing.wns.pnserror|WNS hibák|Mennyiség|Összes|Értesítés a WNS-sel való kommunikáció során hibák miatt nem érkezik.|Nincs dimenzió|
|outgoing.wns.authenticationerror|WNS hitelesítési hibák|Darabszám|Összes|Az értesítés kézbesítése nem kommunikál a Windows Live érvénytelen hitelesítő adatokat vagy nem megfelelő jogkivonatot hibák miatt.|Nincs dimenzió|
|outgoing.apns.SUCCESS|APNS sikeres értesítések|Darabszám|Összes|Minden sikeres értesítések száma.|Nincs dimenzió|
|outgoing.apns.invalidcredentials|APNS engedélyezési hibái|Darabszám|Összes|A száma leküldéses értesítések, amelyek nem sikerült, mert a pns-sel nem fogadta el a megadott hitelesítő adatok, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.apns.badchannel|APNS hibás csatorna hiba|Mennyiség|Összes|A száma, amelyek nem sikerült, mert a token érvénytelen leküldéses értesítések (APN-állapotkód: 8).|Nincs dimenzió|
|outgoing.apns.expiredchannel|APNS lejárt csatorna hiba|Darabszám|Összes|A jogkivonatot, amely a APNS visszajelzés csatornánként érvénytelenítve lett száma.|Nincs dimenzió|
|outgoing.apns.invalidnotificationsize|APNS – érvénytelen értesítésméret által okozott hiba|Mennyiség|Összes|A száma, amelyek nem sikerült, mert a tartalom túl nagy volt leküldéses értesítések (APN-állapotkód: 7).|Nincs dimenzió|
|outgoing.apns.pnserror|APNS-hibák|Darabszám|Összes|A száma, amelyek kommunikálni az APN szolgáltatás hibái miatt nem sikerült leküldéses értesítések.|Nincs dimenzió|
|outgoing.gcm.SUCCESS|Sikeres GCM-értesítések|Darabszám|Összes|Minden sikeres értesítések száma.|Nincs dimenzió|
|outgoing.gcm.invalidcredentials|GCM engedélyezési hibái (érvénytelen hitelesítő adatok)|Darabszám|Összes|A száma leküldéses értesítések, amelyek nem sikerült, mert a pns-sel nem fogadta el a megadott hitelesítő adatok, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.gcm.badchannel|GCM hibás csatorna hiba|Darabszám|Összes|A száma, amelyek nem sikerült, mert a rendszer nem ismerte fel a regisztrációhoz registrationId leküldéses értesítések (GCM eredmény: Érvénytelen regisztrációs).|Nincs dimenzió|
|outgoing.gcm.expiredchannel|GCM lejárt csatorna hiba|Darabszám|Összes|A száma, amelyek nem sikerült, mert lejárt a regisztrációs a registrationId leküldéses értesítések (GCM eredmény: NotRegistered).|Nincs dimenzió|
|outgoing.gcm.throttled|GCM Halmozódni értesítések|Darabszám|Összes|A száma, amelyek nem sikerült, mert a GCM szabályozva az alkalmazás leküldéses értesítések (GCM-állapotkód: 501-599 vagy eredmény: nem érhető el).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationformat|GCM érvénytelen értesítés formátuma|Darabszám|Összes|A száma, amelyek nem sikerült, mert a tartalom nem megfelelő formátumú leküldéses értesítések (GCM eredmény: InvalidDataKey vagy InvalidTtl).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationsize|GCM – érvénytelen értesítésméret által okozott hiba|Mennyiség|Összes|A száma, amelyek nem sikerült, mert a tartalom túl nagy volt leküldéses értesítések (GCM eredmény: MessageTooBig).|Nincs dimenzió|
|outgoing.gcm.wrongchannel|GCM rossz csatorna hiba|Mennyiség|Összes|A leküldéses értesítések, amelyek nem sikerült, mert nincs társítva a jelenlegi alkalmazás a regisztrációhoz registrationId száma (GCM eredmény: InvalidPackageName).|Nincs dimenzió|
|outgoing.gcm.pnserror|GCM hibák|Darabszám|Összes|A száma, amelyek a GCM-mel való kommunikációhoz hibák miatt nem sikerült leküldéses értesítések.|Nincs dimenzió|
|outgoing.gcm.authenticationerror|GCM hitelesítési hibák|Darabszám|Összes|A leküldéses értesítések, amelyek nem sikerült, mert a pns-sel nem fogadta el a megadott hitelesítő adatokat a hitelesítő adatok száma le vannak tiltva, vagy az alkalmazás nincs megfelelően beállítva a SenderId (GCM eredmény: MismatchedSenderId).|Nincs dimenzió|
|outgoing.mpns.SUCCESS|MPNS sikeres értesítések|Mennyiség|Összes|Minden sikeres értesítések száma.|Nincs dimenzió|
|outgoing.mpns.invalidcredentials|MPNS érvénytelen hitelesítő adatok|Darabszám|Összes|A száma leküldéses értesítések, amelyek nem sikerült, mert a pns-sel nem fogadta el a megadott hitelesítő adatok, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.mpns.badchannel|MPNS hibás csatorna hiba|Darabszám|Összes|A száma, amelyek nem sikerült, mert a rendszer nem ismerte fel a regisztrációhoz ChannelURI leküldéses értesítések (MPNS állapota: 404 nem található).|Nincs dimenzió|
|outgoing.mpns.throttled|MPNS Halmozódni értesítések|Mennyiség|Összes|A száma, amely nem sikerült, mert az alkalmazás a MPNS szabályozás leküldéses értesítések (WNS MPNS: 406 nem elfogadható).|Nincs dimenzió|
|outgoing.mpns.invalidnotificationformat|MPNS érvénytelen értesítés formátuma|Mennyiség|Összes|A szám a leküldéses értesítések, melyeknél nem sikerült, mert az értesítési tartalom túl nagy volt.|Nincs dimenzió|
|outgoing.mpns.channeldisconnected|MPNS csatorna kapcsolata megszakadt|Darabszám|Összes|A száma, amelyek nem sikerült, mert meg lett szakítva a regisztrációhoz ChannelURI, leküldéses értesítések (MPNS állapot: nem található 412).|Nincs dimenzió|
|outgoing.mpns.dropped|MPNS eldobott értesítések|Mennyiség|Összes|A leküldéses értesítések MPNS által eldobott száma (MPNS válaszfejlécet: X-NotificationStatus: QueueFull vagy Suppressed).|Nincs dimenzió|
|outgoing.mpns.pnserror|MPNS-hibák|Mennyiség|Összes|A száma, amelyek MPNS folytatott kommunikáció hibák miatt nem sikerült leküldéses értesítések.|Nincs dimenzió|
|outgoing.mpns.authenticationerror|MPNS hitelesítési hibák|Mennyiség|Összes|A száma leküldéses értesítések, amelyek nem sikerült, mert a pns-sel nem fogadta el a megadott hitelesítő adatok, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|notificationhub.pushes|Minden kimenő értesítések|Darabszám|Összes|Az értesítési központnak minden kimenő értesítések|Nincs dimenzió|
|Incoming.all.Requests|Az összes bejövő kérelmek|Darabszám|Összes|Bejövő kérelmek teljes száma a értesítési központ|Nincs dimenzió|
|Incoming.all.failedrequests|Minden bejövő sikertelen kérelmek|Mennyiség|Összes|Teljes bejövő sikertelen kérelmek egy értesítési központ|Nincs dimenzió|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|másodperc|Átlagos|A keresési szolgáltatás átlagos keresési késés|Nincs dimenzió|
|SearchQueriesPerSecond|Keresési lekérdezések száma másodpercenként|CountPerSecond|Átlagos|A keresési szolgáltatás másodpercenként keresési lekérdezések|Nincs dimenzió|
|ThrottledSearchQueriesPercentage|Szabályozottan halmozott keresési lekérdezések százalékos aránya|Százalék|Átlagos|A keresési szolgáltatás volt halmozódni keresési lekérdezések aránya|Nincs dimenzió|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CPUXNS|Egy névtér CPU-használat|Százalék|Maximális|Service bus prémium névtér CPU kihasználtsága metrika|Nincs dimenzió|
|WSXNS|Felhasznál memória mérete névterenként|Százalék|Maximális|Service bus prémium névtér memória kihasználtsága metrika|Nincs dimenzió|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlagos|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlagos|Adat IO kihasználtsága (%)|Nincs dimenzió|
|log_write_percent|Napló IO százalékos aránya|Százalék|Átlagos|Napló IO százalékos aránya|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlagos|DTU-kihasználtság (%)|Nincs dimenzió|
|Tárolás|Adatbázis teljes mérete|Bájtok|Maximális|Adatbázis teljes mérete|Nincs dimenzió|
|connection_successful|Sikeres kapcsolatok|Darabszám|Összes|Sikeres kapcsolatok|Nincs dimenzió|
|connection_failed|Nem sikerült kapcsolatok|Mennyiség|Összes|Nem sikerült kapcsolatok|Nincs dimenzió|
|blocked_by_firewall|Tiltsa le tűzfal|Darabszám|Összes|Tiltsa le tűzfal|Nincs dimenzió|
|Holtpont|Holtpont|Darabszám|Összes|Holtpont|Nincs dimenzió|
|storage_percent|Adatbázis méretének kihasználtsága|Százalék|Maximális|Adatbázis méretének kihasználtsága|Nincs dimenzió|
|xtp_storage_percent|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Százalék|Átlagos|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Százalék|Átlagos|Feldolgozók százalékos aránya|Nincs dimenzió|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlagos|Munkamenetek százalékos aránya|Nincs dimenzió|
|dtu_limit|DTU korlátot|Mennyiség|Átlagos|DTU korlátot|Nincs dimenzió|
|dtu_used|Felhasznált DTU|Darabszám|Átlagos|Felhasznált DTU|Nincs dimenzió|
|dwu_limit|DWU korlátot|Mennyiség|Maximális|DWU korlátot|Nincs dimenzió|
|dwu_consumption_percent|DWU százalékos aránya|Százalék|Maximális|DWU százalékos aránya|Nincs dimenzió|
|dwu_used|A DWU használt|Darabszám|Maximális|A DWU használt|Nincs dimenzió|
|dw_cpu_percent|DW csomópont szintjén processzor|Százalék|Átlagos|DW csomópont szintjén processzor|dw_logical_node_id|
|dw_physical_data_read_percent|DW csomópont adat IO százalékos értéke|Százalék|Átlagos|DW csomópont adat IO százalékos értéke|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlagos|Processzorhasználat (%)|Nincs dimenzió|
|database_cpu_percent|Processzorhasználat (%)|Százalék|Átlagos|Processzorhasználat (%)|DatabaseResourceId|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlagos|Adat IO kihasználtsága (%)|Nincs dimenzió|
|database_physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlagos|Adat IO kihasználtsága (%)|DatabaseResourceId|
|log_write_percent|Napló IO százalékos aránya|Százalék|Átlagos|Napló IO százalékos aránya|Nincs dimenzió|
|database_log_write_percent|Napló IO százalékos aránya|Százalék|Átlagos|Napló IO százalékos aránya|DatabaseResourceId|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlagos|DTU-kihasználtság (%)|Nincs dimenzió|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlagos|DTU-kihasználtság (%)|DatabaseResourceId|
|storage_percent|Tárolási százalékos aránya|Százalék|Átlagos|Tárolási százalékos aránya|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Százalék|Átlagos|Feldolgozók százalékos aránya|Nincs dimenzió|
|database_workers_percent|Feldolgozók százalékos aránya|Százalék|Átlagos|Feldolgozók százalékos aránya|DatabaseResourceId|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlagos|Munkamenetek százalékos aránya|Nincs dimenzió|
|database_sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlagos|Munkamenetek százalékos aránya|DatabaseResourceId|
|eDTU_limit|eDTU korlátot|Darabszám|Átlagos|eDTU korlátot|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájtok|Átlagos|Tárolási kapacitása|Nincs dimenzió|
|eDTU_used|felhasznált edtu-ra|Mennyiség|Átlagos|felhasznált edtu-ra|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájtok|Átlagos|Felhasznált tárterület|Nincs dimenzió|
|database_storage_used|Felhasznált tárterület|Bájtok|Átlagos|Felhasznált tárterület|DatabaseResourceId|
|xtp_storage_percent|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Százalék|Átlagos|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Nincs dimenzió|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlagos|DTU-kihasználtság (%)|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlagos|DTU-kihasználtság (%)|DatabaseResourceId, ElasticPoolResourceId|
|storage_used|Felhasznált tárterület|Bájtok|Átlagos|Felhasznált tárterület|ElasticPoolResourceId|
|database_storage_used|Felhasznált tárterület|Bájtok|Átlagos|Felhasznált tárterület|DatabaseResourceId, ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Használható kapacitás|Bájtok|Átlagos|A fiókot használja a kapacitás|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájtok|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájtok|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|ideje ezredmásodpercben|Átlagos|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlagos|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|A BLOB-kapacitás|Bájtok|Átlagos|A tárfiók a Blob szolgáltatás bájtban által használt tárolási mennyisége.|BlobType|
|Blobok száma|A BLOB száma|Darabszám|Átlagos|A tárfiók a Blob szolgáltatás Blob számát.|BlobType|
|ContainerCount|A BLOB-tároló száma|Mennyiség|Átlagos|A tárfiók a Blob szolgáltatás tárolók száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájtok|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájtok|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|ideje ezredmásodpercben|Átlagos|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlagos|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Tábla Kapacitás|Bájtok|Átlagos|A tárfiók Table szolgáltatás bájtban által használt tárolási mennyisége.|Nincs dimenzió|
|TableCount|Tábla száma|Darabszám|Átlagos|A tárfiók Table szolgáltatás táblájában száma.|Nincs dimenzió|
|TableEntityCount|Tábla entitások száma|Mennyiség|Átlagos|A tárfiók Table szolgáltatás a táblaentitásokat száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájtok|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájtok|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|ideje ezredmásodpercben|Átlagos|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlagos|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Várólista kapacitás|Bájtok|Átlagos|A tárfiók Queue szolgáltatás bájtban által használt tárolási mennyisége.|Nincs dimenzió|
|QueueCount|Várólista száma|Darabszám|Átlagos|A tárfiók Queue szolgáltatás sorból száma.|Nincs dimenzió|
|QueueMessageCount|Várólista-üzenetek száma|Mennyiség|Átlagos|A tárfiók Queue szolgáltatás várólista üzeneteinek hozzávetőleges számát.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájtok|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájtok|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|ideje ezredmásodpercben|Átlagos|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlagos|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|Fájl kapacitás|Bájtok|Átlagos|A tárfiók bájtban szolgáltatás által használt tároló mennyisége.|Nincs dimenzió|
|Filecount;/%totalfilecount|Fájlok száma|Darabszám|Átlagos|A tárfiók szolgáltatása a fájl száma.|Nincs dimenzió|
|FileShareCount|Fájlmegosztás|Mennyiség|Átlagos|Hány fájl osztja meg a tárfiók fájl szolgáltatásban.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájtok|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájtok|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|ideje ezredmásodpercben|Átlagos|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|ideje ezredmásodpercben|Átlagos|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlagos|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU kihasználtsága (%)|Százalék|Maximális|SU kihasználtsága (%)|Nincs dimenzió|
|InputEvents|A bemeneti események|Mennyiség|Összes|A bemeneti események|Nincs dimenzió|
|InputEventBytes|A bemeneti esemény bájt|Bájtok|Összes|A bemeneti esemény bájt|Nincs dimenzió|
|LateInputEvents|A későn érkező bemeneti események|Mennyiség|Összes|A későn érkező bemeneti események|Nincs dimenzió|
|OutputEvents|A kimeneti eseményekben|Mennyiség|Összes|A kimeneti eseményekben|Nincs dimenzió|
|ConversionErrors|Adatok konvertálási hibák|Darabszám|Összes|Adatok konvertálási hibák|Nincs dimenzió|
|Hibák|Futásidejű hibák|Darabszám|Összes|Futásidejű hibák|Nincs dimenzió|
|DroppedOrAdjustedEvents|Üzemen kívüli események|Darabszám|Összes|Üzemen kívüli események|Nincs dimenzió|
|AMLCalloutRequests|Függvény kérelmek|Darabszám|Összes|Függvény kérelmek|Nincs dimenzió|
|AMLCalloutFailedRequests|Sikertelen függvény kérelmek|Darabszám|Összes|Sikertelen függvény kérelmek|Nincs dimenzió|
|AMLCalloutInputEvents|Függvény események|Darabszám|Összes|Függvény események|Nincs dimenzió|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlagos|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Százalék|Átlagos|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Mennyiség|Összes|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Mennyiség|Összes|HTTP-várólista hossza|Példány|
|BytesReceived|Az adatok|Bájtok|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalmat|Bájtok|Összes|Kimenő adatforgalmat|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (funkciókat kivéve)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összes|CPU-idő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összes|Kérelmek|Példány|
|BytesReceived|Az adatok|Bájtok|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalmat|Bájtok|Összes|Kimenő adatforgalmat|Példány|
|Http101|HTTP 101|Mennyiség|Összes|HTTP 101|Példány|
|Http2xx|HTTP 2xx|Darabszám|Összes|HTTP 2xx|Példány|
|Http3xx|HTTP 3xx|Mennyiség|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401-es|Darabszám|Összes|HTTP 401-es|Példány|
|Http403|A HTTP 403|Darabszám|Összes|A HTTP 403|Példány|
|Http404|HTTP 404-es|Darabszám|Összes|HTTP 404-es|Példány|
|Http406|406 http|Mennyiség|Összes|406 http|Példány|
|Http4xx|HTTP 4xx|Darabszám|Összes|HTTP 4xx|Példány|
|Http5xx|HTTP-kiszolgáló hibák|Mennyiség|Összes|HTTP-kiszolgáló hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájtok|Átlagos|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Munkakészlet átlagos memória|Bájtok|Átlagos|Munkakészlet átlagos memória|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlagos|Átlagos válaszidő|Példány|
|FunctionExecutionUnits|Függvény végrehajtása egység|Darabszám|Átlagos|Függvény végrehajtása egység|Példány|
|FunctionExecutionCount|Függvény végrehajtási száma|Darabszám|Átlagos|Függvény végrehajtási száma|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funkciók)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|Az adatok|Bájtok|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalmat|Bájtok|Összes|Kimenő adatforgalmat|Példány|
|Http5xx|HTTP-kiszolgáló hibák|Darabszám|Összes|HTTP-kiszolgáló hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájtok|Átlagos|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Munkakészlet átlagos memória|Bájtok|Átlagos|Munkakészlet átlagos memória|Példány|
|FunctionExecutionUnits|Függvény végrehajtása egység|Darabszám|Átlagos|Függvény végrehajtása egység|Példány|
|FunctionExecutionCount|Függvény végrehajtási száma|Mennyiség|Átlagos|Függvény végrehajtási száma|Példány|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összes|CPU-idő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összes|Kérelmek|Példány|
|BytesReceived|Az adatok|Bájtok|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalmat|Bájtok|Összes|Kimenő adatforgalmat|Példány|
|Http101|HTTP 101|Darabszám|Összes|HTTP 101|Példány|
|Http2xx|HTTP 2xx|Darabszám|Összes|HTTP 2xx|Példány|
|Http3xx|HTTP 3xx|Mennyiség|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401-es|Mennyiség|Összes|HTTP 401-es|Példány|
|Http403|A HTTP 403|Darabszám|Összes|A HTTP 403|Példány|
|Http404|HTTP 404-es|Darabszám|Összes|HTTP 404-es|Példány|
|Http406|406 http|Darabszám|Összes|406 http|Példány|
|Http4xx|HTTP 4xx|Mennyiség|Összes|HTTP 4xx|Példány|
|Http5xx|HTTP-kiszolgáló hibák|Darabszám|Összes|HTTP-kiszolgáló hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájtok|Átlagos|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Munkakészlet átlagos memória|Bájtok|Átlagos|Munkakészlet átlagos memória|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlagos|Átlagos válaszidő|Példány|
|FunctionExecutionUnits|Függvény végrehajtása egység|Darabszám|Átlagos|Függvény végrehajtása egység|Példány|
|FunctionExecutionCount|Függvény végrehajtási száma|Darabszám|Átlagos|Függvény végrehajtási száma|Példány|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|Az adatok|Bájtok|Összes|Az adatok|Példány|
|BytesSent|Kimenő adatforgalmat|Bájtok|Összes|Kimenő adatforgalmat|Példány|
|Http101|HTTP 101|Darabszám|Összes|HTTP 101|Példány|
|Http2xx|HTTP 2xx|Darabszám|Összes|HTTP 2xx|Példány|
|Http3xx|HTTP 3xx|Darabszám|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401-es|Mennyiség|Összes|HTTP 401-es|Példány|
|Http403|A HTTP 403|Darabszám|Összes|A HTTP 403|Példány|
|Http404|HTTP 404-es|Mennyiség|Összes|HTTP 404-es|Példány|
|Http406|406 http|Mennyiség|Összes|406 http|Példány|
|Http4xx|HTTP 4xx|Darabszám|Összes|HTTP 4xx|Példány|
|Http5xx|HTTP-kiszolgáló hibák|Darabszám|Összes|HTTP-kiszolgáló hibák|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlagos|Átlagos válaszidő|Példány|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlagos|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Százalék|Átlagos|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Darabszám|Összes|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Darabszám|Összes|HTTP-várólista hossza|Példány|
|ActiveRequests|Aktív kérések|Darabszám|Összes|Aktív kérések|Példány|
|TotalFrontEnds|Teljes előtér-webkiszolgálóinak|Darabszám|Átlagos|Teljes előtér-webkiszolgálóinak|Példány|
|SmallAppServicePlanInstances|Kisméretű App Service-csomag munkavállalók|Darabszám|Átlagos|Kisméretű App Service-csomag munkavállalók|Példány|
|MediumAppServicePlanInstances|Közepes méretű App Service-csomag munkavállalók|Darabszám|Átlagos|Közepes méretű App Service-csomag munkavállalók|Példány|
|LargeAppServicePlanInstances|Nagyméretű App Service-csomag munkavállalók|Darabszám|Átlagos|Nagyméretű App Service-csomag munkavállalók|Példány|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Feldolgozók|Teljes munkavállalók|Mennyiség|Átlagos|Teljes munkavállalók|Példány|
|WorkersAvailable|Rendelkezésre álló munkavállalók|Darabszám|Átlagos|Rendelkezésre álló munkavállalók|Példány|
|WorkersUsed|Használt feldolgozók|Darabszám|Átlagos|Használt feldolgozók|Példány|

## <a name="next-steps"></a>Következő lépések
* [Olvassa el a Azure figyelő metrikák](monitoring-overview-metrics.md)
* [Hozzon létre a riasztások mérőszámokat](insights-receive-alert-notifications.md)
* [Tárolás, az Event Hubs vagy Naplóelemzési metrikák exportálása](monitoring-overview-of-diagnostic-logs.md)
