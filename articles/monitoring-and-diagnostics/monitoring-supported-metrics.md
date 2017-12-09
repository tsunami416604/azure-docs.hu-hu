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
ms.openlocfilehash: d66c6760cd2414e377d9c0cf55835a21b4bc5051
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure-figyelő támogatott metrikák
Azure figyelő többféleképpen is kommunikál a metrikákat, többek között a diagramkészítési őket a portálon, a hozzájuk férni a REST API-n keresztül, vagy a őket lekérdezése a PowerShell vagy a parancssori felület. Alatt érhető el teljes listáját és az összes metrikák jelenleg Azure figyelő metrika folyamat.

> [!NOTE]
> Más metrikákkal portálon vagy az örökölt API-k használatával érhetők el. Ez a lista csak voltak elérhetők a összevont Azure-figyelő metrika kimenetátirányításának segítségével metrikák tartalmazza. A lekérdezés és eléréséhez metrikák többdimenziósak kérjük, használja a [2017-05-01. dátumú előnézeti api-verzió](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Darabszám|Átlag|QPU. Tartomány 0 – 100 s1, 0-200 S2 és 0 – 400 S4|Nincs dimenzió|
|memory_metric|Memory (Memória)|Bájt|Átlag|A memória. S1 0-25 GB-ot, S2 0-50 GB-ot és 0 – 100 GB-ot S4|Nincs dimenzió|
|TotalConnectionRequests|Teljes csatlakozási kérések|Darabszám|Átlag|Teljes csatlakozási kérések. Ezek a érkezők.|Nincs dimenzió|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenkénti száma|Egység/s|Átlag|Sikeres csatlakozás befejezésekhez aránya.|Nincs dimenzió|
|TotalConnectionFailures|Teljes kapcsolódási hibák|Darabszám|Átlag|Összes sikertelen csatlakozási kísérletek.|Nincs dimenzió|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Darabszám|Átlag|Létrehozott felhasználói munkamenetek jelenlegi száma.|Nincs dimenzió|
|QueryPoolBusyThreads|Lekérdezés készlet foglalt szálak|Darabszám|Átlag|A lekérdezés szálkészlet foglalt szálak számát.|Nincs dimenzió|
|CommandPoolJobQueueLength|A parancs készlet feladat várólistájának hossza|Darabszám|Átlag|A parancs szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|
|ProcessingPoolJobQueueLength|Feldolgozási készlet feladat várólistájának hossza|Darabszám|Átlag|A várólistában a feldolgozási szálkészletben lévő nem-I/O-feladatok száma.|Nincs dimenzió|
|CurrentConnections|Kapcsolat: A jelenlegi kapcsolatok száma|Darabszám|Átlag|Ügyfél fennálló kapcsolatok aktuális száma.|Nincs dimenzió|
|CleanerCurrentPrice|Memória: Tisztító aktuális árlista|Darabszám|Átlag|Aktuális árlista memória $/ bájt/idő normalizálva 1000.|Nincs dimenzió|
|CleanerMemoryShrinkable|Memória: Zsugoríthatósági tisztító memória|Bájt|Átlag|Memória mérete, a háttérben tisztító kiürítése függvényében mennyisége.|Nincs dimenzió|
|CleanerMemoryNonshrinkable|Memória: Tisztító memória nonshrinkable|Bájt|Átlag|Memória mérete, nem vonatkoznak a háttérben tisztító kiürítése mennyisége.|Nincs dimenzió|
|MemoryUsage|Memória: Memória használata|Bájt|Átlag|A kiszolgálói folyamat, tisztító memória ár kiszámításakor felhasznált memória mennyisége Process\PrivateBytes plus memórialeképezési adatok figyelmen kívül hagyása minden leképezve, vagy a xVelocity memórián belüli analytics motor (VertiPaq) meghaladja a memóriakorlátot xVelocity motor által lefoglalt memória méretét a teljesítményszámláló értéke.|Nincs dimenzió|
|MemoryLimitHard|Memória: Memóriakorlátot rögzített|Bájt|Átlag|Rögzített memóriakorlátot, konfigurációs fájlból.|Nincs dimenzió|
|MemoryLimitHigh|Memória: Memória korlátozása magas|Bájt|Átlag|Magas memóriakorlátot, konfigurációs fájlból.|Nincs dimenzió|
|MemoryLimitLow|Memória: Memória korlátja alacsony|Bájt|Átlag|Kevés a memória a határt, a konfigurációs fájlból.|Nincs dimenzió|
|MemoryLimitVertiPaq|Memória: Memória korlátja VertiPaq|Bájt|Átlag|A memória maximális mennyiségét, a konfigurációs fájlból.|Nincs dimenzió|
|Kvóta|Memória: kvóta|Bájt|Átlag|Aktuális memóriakvótáját, bájtban. Memóriakvótáját memória grant vagy memória foglalás is nevezik.|Nincs dimenzió|
|QuotaBlocked|Memória: Kvóta letiltva|Darabszám|Átlag|Le vannak tiltva, amíg más memória kvóták felszabadítását kvóta kérelmek jelenlegi száma.|Nincs dimenzió|
|VertiPaqNonpaged|Memória: a nem lapozható VertiPaq|Bájt|Átlag|A memórián belüli motor általi használatra munkakészletében bájt memóriát zárolva van.|Nincs dimenzió|
|VertiPaqPaged|Memória: A VertiPaq lapozható|Bájt|Átlag|A memóriában levő használt lapozható memória bájt.|Nincs dimenzió|
|RowsReadPerSec|Feldolgozási: Sor olvasása másodpercenkénti száma|Egység/s|Átlag|Sorok aránya az összes relációs adatbázisok olvasni.|Nincs dimenzió|
|RowsConvertedPerSec|Feldolgozási: Sorok konvertálni másodpercenkénti száma|Egység/s|Átlag|Sorok aránya alakítja a feldolgozás során.|Nincs dimenzió|
|RowsWrittenPerSec|Feldolgozási: Másodpercenként írt sorok|Egység/s|Átlag|Feldolgozás során írt sorok száma.|Nincs dimenzió|
|CommandPoolBusyThreads|Szálak: Parancs készlet foglalt szálak|Darabszám|Átlag|A parancs szálkészlet foglalt szálak számát.|Nincs dimenzió|
|CommandPoolIdleThreads|Szálak: Parancs készlet üresjárati szálak|Darabszám|Átlag|A parancs szálkészlet üresjárati szálak számát.|Nincs dimenzió|
|LongParsingBusyThreads|Szálak: Hosszú foglalt szálak elemzése|Darabszám|Átlag|A hosszú elemzési szálkészlet foglalt szálak számát.|Nincs dimenzió|
|LongParsingIdleThreads|Szálak: Hosszú üresjárati szálak elemzése|Darabszám|Átlag|A hosszú elemzési szálkészlet üresjárati szálak számát.|Nincs dimenzió|
|LongParsingJobQueueLength|Szálak: Hosszú elemzése feladat várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|
|ProcessingPoolBusyIOJobThreads|Szálak: Készlet foglalt i/o-feladat szálak feldolgozása|Darabszám|Átlag|I/o-feladatok futtatása a feldolgozási szálkészletben lévő szálak száma.|Nincs dimenzió|
|ProcessingPoolBusyNonIOThreads|Szálak: Készlet foglalt nem-I/O-szálak feldolgozása|Darabszám|Átlag|Nem-I/O-feladatok futtatása a feldolgozási szálkészletben lévő szálak száma.|Nincs dimenzió|
|ProcessingPoolIOJobQueueLength|Szálak: Feldolgozása készlet i/o-feldolgozás várólistájának hossza|Darabszám|Átlag|A várólistában a feldolgozási szálkészletben lévő i/o-feladatok száma.|Nincs dimenzió|
|ProcessingPoolIdleIOJobThreads|Szálak: Készlet üresjárati i/o-feladat szálak feldolgozása|Darabszám|Átlag|A feldolgozási szálkészletben lévő i/o-feladatok üresjárati szálak száma.|Nincs dimenzió|
|ProcessingPoolIdleNonIOThreads|Szálak: Készlet üresjárati nem-I/O-szálak feldolgozása|Darabszám|Átlag|Nem-I/O-feladatok hozzárendelve a feldolgozási szálkészletben üresjárati szálak számát.|Nincs dimenzió|
|QueryPoolIdleThreads|Szálak: Lekérdezés készlet üresjárati szálak|Darabszám|Átlag|A feldolgozási szálkészletben lévő i/o-feladatok üresjárati szálak száma.|Nincs dimenzió|
|QueryPoolJobQueueLength|Szálak: Lekérdezés készlet feladat várólista lengt|Darabszám|Átlag|A lekérdezés szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|
|ShortParsingBusyThreads|Szálak: Rövid foglalt szálak elemzése|Darabszám|Átlag|A rövid elemzési szálkészlet foglalt szálak számát.|Nincs dimenzió|
|ShortParsingIdleThreads|Szálak: Rövid üresjárati szálak elemzése|Darabszám|Átlag|A rövid elemzési szálkészlet üresjárati szálak számát.|Nincs dimenzió|
|ShortParsingJobQueueLength|Szálak: Rövid elemzése feladat várólistájának hossza|Darabszám|Átlag|A rövid elemzési szálkészlet sorban lévő feladatok száma.|Nincs dimenzió|
|memory_thrashing_metric|Memória Akadozások|Százalék|Átlag|Átlagos memória thrashing.|Nincs dimenzió|
|mashup_engine_qpu_metric|M-motor QPU|Darabszám|Átlag|Adategyesítési motor folyamatok QPU kihasználtsága|Nincs dimenzió|
|mashup_engine_memory_metric|M-motor memória|Bájt|Átlag|Adategyesítési motor folyamatok memóriahasználata|Nincs dimenzió|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Teljes átjáró kérelmek|Darabszám|Összes|Átjáró kérelmek száma|Hely, állomásnév|
|SuccessfulRequests|Átjáró sikeres kérelmek|Darabszám|Összes|Átjáró sikeres kérelmek száma|Hely, állomásnév|
|UnauthorizedRequests|Jogosulatlan átjáró kérelmek|Darabszám|Összes|Jogosulatlan átjáró kérelmek száma|Hely, állomásnév|
|FailedRequests|Hibás átjáró kérelmek|Darabszám|Összes|Az átjáró kérelmekben hibák száma|Hely, állomásnév|
|OtherRequests|Más átjáró kérelmek|Darabszám|Összes|Más átjáró kérelmek száma|Hely, állomásnév|
|Időtartam|Átjáró kérelmek teljes időtartama|Ezredmásodperc|Átlag|Teljes időtartam az átjáró kérelmek ezredmásodpercben|Hely, állomásnév|
|Kapacitás|Kapacitás (előzetes verzió)|Százalék|Maximum|Kihasználtság metrika ApiManagement szolgáltatás|Hely|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok teljes száma|Darabszám|Összes|A feladatok teljes száma|Nincs dimenzió|

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
|setcommands|Beállítása|Darabszám|Összes||Nincs dimenzió|
|evictedkeys|Kizárt kulcsok|Darabszám|Összes||Nincs dimenzió|
|totalkeys|Kulcsok összesen|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys|Lejárt kulcsok|Darabszám|Összes||Nincs dimenzió|
|usedmemory|Felhasznált memória|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss|Használt memória RSS|Bájt|Maximum||Nincs dimenzió|
|serverLoad|Kiszolgáló-terhelés|Százalék|Maximum||Nincs dimenzió|
|cacheWrite|Gyorsítótár-írás|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead|Gyorsítótár-olvasás|BytesPerSecond|Maximum||Nincs dimenzió|
|PercentProcessorTime|CPU|Százalék|Maximum||Nincs dimenzió|
|connectedclients0|Csatlakozott ügyfelek (szilánkok 0)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed0|Összes művelet (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|cachehits0|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|cachemisses0|A gyorsítótárbeli (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|getcommands0|Lekérdezi a (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|setcommands0|Beállítása (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys0|Eltávolított kulcsok (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|totalkeys0|Összes kulcs (szilánkok 0)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys0|Lejárt kulcsokkal (szilánkok 0)|Darabszám|Összes||Nincs dimenzió|
|usedmemory0|Használt memória (szilánkok 0)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss0|Használt memória RSS (szilánkok 0)|Bájt|Maximum||Nincs dimenzió|
|serverLoad0|Kiszolgáló terhelését (szilánkok 0)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite0|Gyorsítótár-írási (szilánkok 0)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead0|Gyorsítótár olvasható (szilánkok 0)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime0|Processzor (szilánkok 0)|Százalék|Maximum||Nincs dimenzió|
|connectedclients1|Csatlakozott ügyfelek (szilánkok 1)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed1|Összes művelet (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|cachehits1|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|cachemisses1|A gyorsítótárbeli (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|getcommands1|(Szilánkok 1) beolvasása|Darabszám|Összes||Nincs dimenzió|
|setcommands1|(1 a Shard) beállítása|Darabszám|Összes||Nincs dimenzió|
|evictedkeys1|Eltávolított kulcsok (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|totalkeys1|Összes kulcs (szilánkok 1)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys1|Lejárt kulcsokkal (szilánkok 1)|Darabszám|Összes||Nincs dimenzió|
|usedmemory1|Használt memória (szilánkok 1)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss1|Használt memória RSS (szilánkok 1)|Bájt|Maximum||Nincs dimenzió|
|serverLoad1|Kiszolgáló terhelését (szilánkok 1)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite1|Gyorsítótár-írási (szilánkok 1)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead1|Gyorsítótár olvasható (szilánkok 1)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime1|Processzor (szilánkok 1)|Százalék|Maximum||Nincs dimenzió|
|connectedclients2|Csatlakozott ügyfelek (szilánkok 2)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed2|Összes művelet (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|cachehits2|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|cachemisses2|A gyorsítótárbeli (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|getcommands2|Lekérdezi a (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|setcommands2|Beállítása (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys2|Eltávolított kulcsok (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|totalkeys2|Összes kulcs (szilánkok 2)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys2|Lejárt kulcsokkal (szilánkok 2)|Darabszám|Összes||Nincs dimenzió|
|usedmemory2|Használt memória (szilánkok 2)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss2|Használt memória RSS (szilánkok 2)|Bájt|Maximum||Nincs dimenzió|
|serverLoad2|Kiszolgáló terhelését (szilánkok 2)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite2|Gyorsítótár-írási (szilánkok 2)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead2|Gyorsítótár olvasható (szilánkok 2)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime2|Processzor (szilánkok 2)|Százalék|Maximum||Nincs dimenzió|
|connectedclients3|Csatlakozott ügyfelek (szilánkok 3)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed3|Összes művelet (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|cachehits3|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|cachemisses3|A gyorsítótárbeli (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|getcommands3|Lekérdezi a (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|setcommands3|(3 szilánkok) beállítása|Darabszám|Összes||Nincs dimenzió|
|evictedkeys3|Eltávolított kulcsok (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|totalkeys3|Összes kulcs (szilánkok 3)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys3|Lejárt kulcsokkal (szilánkok 3)|Darabszám|Összes||Nincs dimenzió|
|usedmemory3|Használt memória (szilánkok 3)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss3|Használt memória RSS (szilánkok 3)|Bájt|Maximum||Nincs dimenzió|
|serverLoad3|Kiszolgáló terhelését (szilánkok 3)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite3|Gyorsítótár-írási (szilánkok 3)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead3|Gyorsítótár olvasható (szilánkok 3)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime3|Processzor (szilánkok 3)|Százalék|Maximum||Nincs dimenzió|
|connectedclients4|Csatlakozott ügyfelek (szilánkok 4)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed4|Összes művelet (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|cachehits4|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|cachemisses4|A gyorsítótárbeli (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|getcommands4|Lekérdezi a (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|setcommands4|(4 szilánkok) beállítása|Darabszám|Összes||Nincs dimenzió|
|evictedkeys4|Eltávolított kulcsok (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|totalkeys4|Összes kulcs (szilánkok 4)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys4|Lejárt kulcsokkal (szilánkok 4)|Darabszám|Összes||Nincs dimenzió|
|usedmemory4|Használt memória (szilánkok 4)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss4|Használt memória RSS (szilánkok 4)|Bájt|Maximum||Nincs dimenzió|
|serverLoad4|Kiszolgáló terhelését (szilánkok 4)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite4|Gyorsítótár-írási (szilánkok 4)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead4|Gyorsítótár olvasható (szilánkok 4)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime4|Processzor (szilánkok 4)|Százalék|Maximum||Nincs dimenzió|
|connectedclients5|Csatlakozott ügyfelek (szilánkok 5)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed5|Összes művelet (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|cachehits5|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|cachemisses5|A gyorsítótárbeli (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|getcommands5|Lekérdezi a (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|setcommands5|Beállítása (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys5|Eltávolított kulcsok (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|totalkeys5|Összes kulcs (szilánkok 5)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys5|Lejárt kulcsokkal (szilánkok 5)|Darabszám|Összes||Nincs dimenzió|
|usedmemory5|Használt memória (szilánkok 5)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss5|Használt memória RSS (szilánkok 5)|Bájt|Maximum||Nincs dimenzió|
|serverLoad5|Kiszolgáló terhelését (szilánkok 5)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite5|Gyorsítótár-írási (szilánkok 5)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead5|Gyorsítótár olvasható (szilánkok 5)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime5|Processzor (szilánkok 5)|Százalék|Maximum||Nincs dimenzió|
|connectedclients6|Csatlakozott ügyfelek (szilánkok 6)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed6|Összes művelet (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|cachehits6|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|cachemisses6|A gyorsítótárbeli (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|getcommands6|Lekérdezi a (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|setcommands6|Beállítása (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys6|Eltávolított kulcsok (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|totalkeys6|Összes kulcs (szilánkok 6)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys6|Lejárt kulcsokkal (szilánkok 6)|Darabszám|Összes||Nincs dimenzió|
|usedmemory6|Használt memória (szilánkok 6)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss6|RSS (szilánkok 6) a használt memória|Bájt|Maximum||Nincs dimenzió|
|serverLoad6|Kiszolgáló terhelését (szilánkok 6)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite6|Gyorsítótár-írási (szilánkok 6)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead6|Gyorsítótár olvasható (szilánkok 6)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime6|Processzor (szilánkok 6)|Százalék|Maximum||Nincs dimenzió|
|connectedclients7|Csatlakozott ügyfelek (szilánkok 7)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed7|Összes művelet (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|cachehits7|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|cachemisses7|A gyorsítótárbeli (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|getcommands7|Lekérdezi a (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|setcommands7|Beállítása (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys7|Eltávolított kulcsok (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|totalkeys7|Összes kulcs (szilánkok 7)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys7|Lejárt kulcsokkal (szilánkok 7)|Darabszám|Összes||Nincs dimenzió|
|usedmemory7|Használt memória (szilánkok 7)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss7|Használt memória RSS (szilánkok 7)|Bájt|Maximum||Nincs dimenzió|
|serverLoad7|Kiszolgáló terhelését (szilánkok 7)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite7|Gyorsítótár-írási (szilánkok 7)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead7|Gyorsítótár olvasható (szilánkok 7)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime7|Processzor (szilánkok 7)|Százalék|Maximum||Nincs dimenzió|
|connectedclients8|Csatlakozott ügyfelek (szilánkok 8)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed8|Összes művelet (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|cachehits8|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|cachemisses8|A gyorsítótárbeli (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|getcommands8|Lekérdezi a (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|setcommands8|Beállítása (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys8|Eltávolított kulcsok (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|totalkeys8|Összes kulcs (szilánkok 8)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys8|Lejárt kulcsokkal (szilánkok 8)|Darabszám|Összes||Nincs dimenzió|
|usedmemory8|Használt memória (szilánkok 8)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss8|Használt memória RSS (szilánkok 8)|Bájt|Maximum||Nincs dimenzió|
|serverLoad8|Kiszolgáló terhelését (szilánkok 8)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite8|Gyorsítótár-írási (szilánkok 8)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead8|Gyorsítótár olvasható (szilánkok 8)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime8|Processzor (szilánkok 8)|Százalék|Maximum||Nincs dimenzió|
|connectedclients9|Csatlakozott ügyfelek (szilánkok 9)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed9|Összes művelet (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|cachehits9|Találatot eredményező gyorsítótárbeli kereséseinek (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|cachemisses9|A gyorsítótárbeli (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|getcommands9|Lekérdezi a (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|setcommands9|Beállítása (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|evictedkeys9|Eltávolított kulcsok (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|totalkeys9|Összes kulcs (szilánkok 9)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys9|Lejárt kulcsokkal (szilánkok 9)|Darabszám|Összes||Nincs dimenzió|
|usedmemory9|Használt memória (szilánkok 9)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss9|Használt memória RSS (szilánkok 9)|Bájt|Maximum||Nincs dimenzió|
|serverLoad9|Kiszolgáló terhelését (szilánkok 9)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite9|Gyorsítótár-írási (szilánkok 9)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead9|Gyorsítótár olvasható (szilánkok 9)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime9|Processzor (szilánkok 9)|Százalék|Maximum||Nincs dimenzió|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A százalékos aránya, amelyek jelenleg használják a virtuális gépek lefoglalt számítási egység.|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|Minden hálózati interfészen a virtuális gépek (bejövő forgalom) által fogadott bájtok száma.|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|Időtúllépés a által a virtuális gépek (kimenő forgalmat) hálózati adaptereken bájtok száma.|Nincs dimenzió|
|Lemez sebessége olvasott bájt/mp|Lemez olvasása|BytesPerSecond|Átlag|Átlagos bájtok beolvasása lemezről időszak ellenőrzése során.|Nincs dimenzió|
|Lemez írási bájtok/s|Lemez írási|BytesPerSecond|Átlag|Átlagos bájt időszak ellenőrzése során lemezre írni.|Nincs dimenzió|
|Lemezolvasási művelet/s|Lemezolvasási művelet/s|Egység/s|Átlag|Lemez olvasási iops-érték.|Nincs dimenzió|
|Lemezre írási művelet/s|Lemezre írási művelet/s|Egység/s|Átlag|Lemez írási iops-érték.|Nincs dimenzió|

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
|CharactersTranslated|A lefordított karakter|Darabszám|Összes|Szöveg bejövő kérelemben szereplő karakterek száma.|Nincs dimenzió|
|SpeechSessionDuration|Beszéd munkamenet időtartama|másodperc|Összes|Teljes időtartam másodpercben beszéd-munkamenet.|Nincs dimenzió|

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
|Fennmaradó CPU kreditek|Fennmaradó CPU kreditek|Darabszám|Átlag|Elérhető kapacitásnövelés kreditek száma összesen|Nincs dimenzió|
|Felhasznált CPU kreditek|Felhasznált CPU kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|

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
|Fennmaradó CPU kreditek|Fennmaradó CPU kreditek|Darabszám|Átlag|Elérhető kapacitásnövelés kreditek száma összesen|Nincs dimenzió|
|Felhasznált CPU kreditek|Felhasznált CPU kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|

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
|Fennmaradó CPU kreditek|Fennmaradó CPU kreditek|Darabszám|Átlag|Elérhető kapacitásnövelés kreditek száma összesen|Nincs dimenzió|
|Felhasznált CPU kreditek|Felhasznált CPU kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek száma összesen|Nincs dimenzió|

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
|DCIProfilesCount|Profil példányok száma|Darabszám|Utolsó||Nincs dimenzió|
|DCIInteractionsPerMonthCount|Kapcsolati hónap száma|Darabszám|Utolsó||Nincs dimenzió|
|DCIKpisCount|KPI száma|Darabszám|Utolsó||Nincs dimenzió|
|DCISegmentsCount|Szegmens száma|Darabszám|Utolsó||Nincs dimenzió|
|DCIPredictiveMatchPoliciesCount|A prediktív egyezés száma|Darabszám|Utolsó||Nincs dimenzió|
|DCIPredictionsCount|Előrejelzés száma|Darabszám|Utolsó||Nincs dimenzió|

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
|DataWritten|Az adatok írása|Bájt|Összes|Teljes adatmennyiség ír a fiókba.|Nincs dimenzió|
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
|d2c.telemetry.ingress.SUCCESS|Küldött telemetriai üzenetek|Darabszám|Összes|Sikerült elküldeni az IoT hub eszköz-felhő telemetriai üzenetek száma|Nincs dimenzió|
|c2d.commands.egress.Complete.SUCCESS|A parancsok befejeződött|Darabszám|Összes|Az eszköz által végrehajtott felhő eszközre parancsok száma|Nincs dimenzió|
|c2d.commands.egress.Abandon.SUCCESS|Elhagyott parancsok|Darabszám|Összes|Az eszköz által félbe hagyott felhő eszközre parancsok száma|Nincs dimenzió|
|c2d.commands.egress.Reject.SUCCESS|Elutasított parancsok|Darabszám|Összes|Az eszköz által elutasított felhő eszközre parancsok száma|Nincs dimenzió|
|devices.totalDevices|Eszközök teljes száma|Darabszám|Összes|Az IoT hub regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök|Darabszám|Összes|Az IoT hub csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.SUCCESS|Telemetria kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt végpontok (összesen)|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Az eldobott üzenetek|Darabszám|Összes|Dobva, mert a kézbesítési végpont lett elhalt üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.Orphaned|Árva üzenetek|Darabszám|Összes|A nem megfelelő bármely útvonalakat, beleértve a tartalék útvonal üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.Invalid|Érvénytelen üzenet|Darabszám|Összes|A végpont alkalmazással való inkompatibilitás miatt nem kézbesített üzenetek száma|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Tartalék feltételnek megfelelő üzenetek|Darabszám|Összes|A tartalék végpont üzenetek száma|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Az Event Hubs végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt az Event Hubs végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Az Event Hubs végpontok üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között az Eseményközpont végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Service Bus-üzenetsorba végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-üzenetsorba végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|A Service Bus-üzenetsorba végpontokhoz üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-üzenetsorba végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Service Bus-témakörbe végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt Service Bus-témakörbe végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|A Service Bus-témakörbe végpontokhoz üzenet késés|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a Service Bus-témakörbe végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|A beépített végpont (üzenet/események) kézbesített üzenetek|Darabszám|Összes|Ennyiszer üzenetek írása sikeres volt a beépített végpont (üzenet/esemény)|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Üzenet várakozási ideje a beépített végpont (üzenet/esemény)|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között történő (üzenet/esemény), beépített végpont ezredmásodpercben |Nincs dimenzió|
|d2c.endpoints.egress.Storage|Tárolási végpontok kézbesített üzenetek|Darabszám|Összes|Üzenetek írása sikeres volt tárolási végpontok száma|Nincs dimenzió|
|d2c.endpoints.latency.Storage|Üzenet késés tárolási végpontok|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár az IoT hubhoz üzenet érkező és az üzenet érkező között a tárolási végpont, ezredmásodpercben|Nincs dimenzió|
|d2c.endpoints.egress.Storage.bytes|Adatok tárhelyen|Bájt|Összes|Adatok tárolási végpontok írt bájtok száma|Nincs dimenzió|
|d2c.endpoints.egress.Storage.blobs|Blobok tárhelyen|Darabszám|Összes|BLOB storage végpontok írt száma|Nincs dimenzió|
|d2c.Twin.Read.SUCCESS|Sikeres iker olvassa be az eszközökön|Darabszám|Összes|Az összes sikeres a két eszköz által kezdeményezett olvasások száma.|Nincs dimenzió|
|d2c.Twin.Read.failure|Nem sikerült a kettős olvasások eszközökről|Darabszám|Összes|Teljes számát a két eszköz által kezdeményezett olvasás sikertelen volt.|Nincs dimenzió|
|d2c.Twin.Read.size|A két válasz mérete olvassa be az eszközökön|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális az összes sikeres iker olvasási műveletek.|Nincs dimenzió|
|d2c.Twin.Update.SUCCESS|Sikeres iker frissítések eszközökről|Darabszám|Összes|A két eszköz által kezdeményezett összes sikeres frissítések száma.|Nincs dimenzió|
|d2c.Twin.Update.failure|Nem sikerült a két frissítések eszközökről|Darabszám|Összes|A teljes számát a két eszköz által kezdeményezett frissítések nem sikerült.|Nincs dimenzió|
|d2c.Twin.Update.size|A két frissítések eszközökről mérete|Bájt|Átlag|Az eszköz által kezdeményezett átlagos, minimális és maximális méretét az összes sikeres iker frissítések.|Nincs dimenzió|
|c2d.methods.SUCCESS|Sikeres közvetlen módszer meghívásához|Darabszám|Összes|Az összes sikeres közvetlen módszer hívások száma.|Nincs dimenzió|
|c2d.methods.failure|Nem sikerült a közvetlen módszer meghívásához|Darabszám|Összes|A teljes számát a közvetlen metódushívások nem sikerült.|Nincs dimenzió|
|c2d.methods.requestSize|A közvetlen módszer meghívásához kérelem mérete|Bájt|Átlag|Az átlagos, a minimális és a közvetlen módszer minden sikeres kérelmek maximális.|Nincs dimenzió|
|c2d.methods.responseSize|Válasz mérete közvetlen módszer meghívásához|Bájt|Átlag|Az átlagos, a minimális és a maximális minden sikeres közvetlen módszer válaszok.|Nincs dimenzió|
|c2d.Twin.Read.SUCCESS|Sikeres iker háttérből olvassa be|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett iker olvasási műveletek számát.|Nincs dimenzió|
|c2d.Twin.Read.failure|Sikertelen a két háttérből olvassa be|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett olvasások nem sikerült.|Nincs dimenzió|
|c2d.Twin.Read.size|A válasz méretének iker olvasások háttérből|Bájt|Átlag|Az átlagos, minimális és maximális az összes sikeres back-end által kezdeményezett iker olvasási műveletek.|Nincs dimenzió|
|c2d.Twin.Update.SUCCESS|Sikeres iker frissítések háttérből|Darabszám|Összes|Az összes sikeres back-end által kezdeményezett iker frissítések száma.|Nincs dimenzió|
|c2d.Twin.Update.failure|Sikertelen a két frissítések háttérből|Darabszám|Összes|Teljes számát a két back-end által kezdeményezett frissítések nem sikerült.|Nincs dimenzió|
|c2d.Twin.Update.size|A két frissítések háttérből mérete|Bájt|Átlag|Az átlagos, minimális és maximális méretét az összes sikeres back-end által kezdeményezett iker frissítések.|Nincs dimenzió|
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
|jobs.Completed|Befejezett feladatok|Darabszám|Összes|Az összes befejezett feladatok száma.|Nincs dimenzió|
|jobs.Failed|A sikertelen feladatok|Darabszám|Összes|Összes sikertelen feladatok száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Sávszélesség-szabályozási hibák száma|Darabszám|Összes|Azelőtt gyorsítja fel az eszköz átviteli miatt szabályozás hibák száma|Nincs dimenzió|
|dailyMessageQuotaUsed|Használt üzenetek teljes száma|Darabszám|Átlag|Jelenleg használt teljes üzenetek száma. Ez az összesített érték, amely nulla értékre állítását: 00:00 UTC minden nap.|Nincs dimenzió|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Darabszám|Összes|Kísérlet történt az eszköz-regisztrációinak száma|ProvisioningServiceName, IotHubName, állapota|
|DeviceAssignments|Hozzárendelt eszközök|Darabszám|Összes|Az IoT-központ rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Állapotigazolási kísérletek|Darabszám|Összes|Próbált eszköz tanúsítványok száma|ProvisioningServiceName, állapotát, a protokoll|

## <a name="microsoftdocumentdbdatabaseaccounts-cosmosdb"></a>Microsoft.DocumentDB/databaseAccounts (CosmosDB)
|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Összes kérelem|Darabszám|Darabszám|Kérelmek száma|DatabaseAccount, CollectionName, DatabaseName, régió, StatusCode|
|MongoRequests|Mongo kérelmek|Darabszám|Darabszám|Mongo kérelmek száma|DatabaseAccount, CollectionName, DatabaseName, régió, ErrorCode, CommandName|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|INREQS|Bejövő küldési kérelmek|Darabszám|Összes|Teljes bejövő névtér kérelem elküldése|Nincs dimenzió|
|SUCCREQ|Sikeres kérések|Darabszám|Összes|A névtér összes sikeres kérelme|Nincs dimenzió|
|FAILREQ|Sikertelen kérések|Darabszám|Összes|A névtérhez kapcsolódó összes sikertelen kérelem|Nincs dimenzió|
|SVRBSY|Kiszolgáló foglaltsága miatti hibák|Darabszám|Összes|A névtér összes, kiszolgáló foglaltsága miatti hibája|Nincs dimenzió|
|INTERR|Belső kiszolgálóhibák|Darabszám|Összes|A névtér összes belső kiszolgálóhibája|Nincs dimenzió|
|MISCERR|Egyéb hibák|Darabszám|Összes|A névtérhez kapcsolódó összes sikertelen kérelem|Nincs dimenzió|
|INMSGS|(Elavult a) bejövő üzenetek|Darabszám|Összes|Teljes bejövő üzenetek névtér. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrika|Nincs dimenzió|
|EHINMSGS|Bejövő üzenetek|Darabszám|Összes|A névtér összes bejövő üzenete|Nincs dimenzió|
|OUTMSGS|(Elavult) kimenő üzenetek|Darabszám|Összes|A teljes kimenő üzenetek névtér. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrika|Nincs dimenzió|
|EHOUTMSGS|Kimenő üzenetek|Darabszám|Összes|A névtér összes kimenő üzenete|Nincs dimenzió|
|EHINMBS|Bejövő bájtok (elavult)|Bájt|Összes|Event Hub bejövő üzenet átviteli névtér. Ez a metrika elavult. Ehelyett használja a bejövő bájtok metrika|Nincs dimenzió|
|EHINBYTES|Bejövő bájtok|Bájt|Összes|Az eseményközpont adott névtérhez kapcsolódó bejövő üzeneteinek átviteli sebessége|Nincs dimenzió|
|EHOUTMBS|Kimenő bájtok (elavult)|Bájt|Összes|Event Hub kimenő üzenet átviteli névtér. Ez a metrika elavult. Ehelyett használja a kimenő bájt metrika|Nincs dimenzió|
|EHOUTBYTES|Kimenő bájtok|Bájt|Összes|Az eseményközpont adott névtérhez kapcsolódó kimenő üzeneteinek átviteli sebessége|Nincs dimenzió|
|EHABL|Archivált várakozó üzenetek|Darabszám|Összes|Az eseményközpont adott névtérhez kapcsolódó archivált várakozó üzenetei|Nincs dimenzió|
|EHAMSGS|Üzenetek archiválása|Darabszám|Összes|Az eseményközpont adott névtéren belüli archivált üzenetei|Nincs dimenzió|
|EHAMBS|Archivált üzenetek átviteli sebessége|Bájt|Összes|Az eseményközpont adott névtéren belüli archivált üzeneteinek átviteli sebessége|Nincs dimenzió|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrika értékét|Darabszám|Átlag|Az automatikus skálázás végrehajtásakor által kiszámított érték|MetricTriggerSource|
|MetricThreshold|Metrika küszöbérték|Darabszám|Átlag|A megadott automatikus skálázási küszöbértéket, automatikus skálázás futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Darabszám|Átlag|A kapacitás jelentett automatikus skálázás végrehajtásakor azt.|Nincs dimenzió|
|ScaleActionsInitiated|Skálázási műveletet kezdeményezett|Darabszám|Összes|A méretezési művelet irányát.|ScaleDirection|

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
|RunThrottledEvents|Futtatások által elindított események|Darabszám|Összes|Munkafolyamat-művelet vagy az eseményindító száma halmozódni események.|Nincs dimenzió|
|RunFailurePercentage|Futtatási hibák százalékos értéke|Százalék|Összes|Sikertelen munkafolyamat-futtatások százalékos értéke.|Nincs dimenzió|
|ActionsStarted|Elindított műveletek |Darabszám|Összes|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|Befejezett műveletek |Darabszám|Összes|A befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|Sikeres műveletek |Darabszám|Összes|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Sikertelen műveletek|Darabszám|Összes|A sikertelen munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Darabszám|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|Műveletek késése |másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|Sikeres műveletek késése |másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|Műveletek által elindított események|Darabszám|Összes|A munkafolyamat-műveletek által elindított események száma.|Nincs dimenzió|
|TriggersStarted|Eseményindítók indítása |Darabszám|Összes|Munkafolyamat-eseményindítók indítása száma.|Nincs dimenzió|
|TriggersCompleted|Eseményindítók befejeződött |Darabszám|Összes|Befejezett munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSucceeded|Eseményindítók sikeres volt. |Darabszám|Összes|Munkafolyamat-eseményindítók száma sikeresen befejeződött.|Nincs dimenzió|
|TriggersFailed|Nem sikerült az eseményindítók |Darabszám|Összes|Nem sikerült munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott eseményindítók|Darabszám|Összes|Munkafolyamat-eseményindítók száma kihagyva.|Nincs dimenzió|
|TriggersFired|Eseményindítók következik |Darabszám|Összes|Munkafolyamat-eseményindítók száma következik be.|Nincs dimenzió|
|TriggerLatency|Eseményindító késés |másodperc|Átlag|Befejezett munkafolyamat eseményindítók késését.|Nincs dimenzió|
|TriggerFireLatency|Eseményindító tűz késés |másodperc|Átlag|Tiltás késése a munkafolyamat-eseményindítók következik be.|Nincs dimenzió|
|TriggerSuccessLatency|Eseményindító sikeres késés |másodperc|Átlag|Tiltás késése a munkafolyamat-eseményindítók sikeresen befejeződött.|Nincs dimenzió|
|TriggerThrottledEvents|Eseményindító Halmozódni események|Darabszám|Összes|Munkafolyamat eseményindító száma halmozódni események.|Nincs dimenzió|
|BillableActionExecutions|Számlázható műveleti végrehajtások|Darabszám|Összes|Számlázandó munkafolyamat-műveleti végrehajtások száma.|Nincs dimenzió|
|BillableTriggerExecutions|Számlázható eseményindító végrehajtások|Darabszám|Összes|Munkafolyamat eseményindító végrehajtások első számlázva száma.|Nincs dimenzió|
|TotalBillableExecutions|Összes számlázható végrehajtás|Darabszám|Összes|Számlázandó munkafolyamat-végrehajtások száma.|Nincs dimenzió|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|VIP rendelkezésre állása|Darabszám|Átlag|Rendelkezésre állási VIP-végpontok mintavételi eredmények alapján|Vipaddress értéke, VipPort|
|DipAvailability|DIP rendelkezésre állása|Darabszám|Átlag|Rendelkezésre állási, a DIP-végpontok mintavételi eredmények alapján|ProtocolType, DipPort, vipaddress értéke, VipPort, DipAddress|
|ByteCount|Bájtok száma|Darabszám|Összes|Időtartamon belül átvitt bájtok teljes száma|Vipaddress értéke, VipPort, iránya|
|PacketCount|Csomagok száma|Darabszám|Összes|Időtartamon belül küldött csomagok teljes száma|Vipaddress értéke, VipPort, iránya|
|SYNCount|Szinkronizálás a mi száma|Darabszám|Összes|Továbbított időtartamon belül SZIN csomagok száma összesen|Vipaddress értéke, VipPort, iránya|
|SnatConnectionCount|SNAT kapcsolatok száma|Darabszám|Összes|Időtartamon belül létrehozott új SNAT kapcsolatok száma|Vipaddress értéke, DipAddress, ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|Egység/s|Átlag|Bejövő csomagok DDoS|Nincs dimenzió|
|PacketsDroppedDDoS|Eldobott DDoS bejövő csomagok|Egység/s|Átlag|Eldobott DDoS bejövő csomagok|Nincs dimenzió|
|PacketsForwardedDDoS|DDoS továbbított bejövő csomagok|Egység/s|Átlag|DDoS továbbított bejövő csomagok|Nincs dimenzió|
|TCPPacketsInDDoS|A bejövő TCP-csomagok DDoS|Egység/s|Átlag|A bejövő TCP-csomagok DDoS|Nincs dimenzió|
|TCPPacketsDroppedDDoS|Bejövő TCP-csomagok eldobott DDoS|Egység/s|Átlag|Bejövő TCP-csomagok eldobott DDoS|Nincs dimenzió|
|TCPPacketsForwardedDDoS|DDoS továbbított bejövő TCP-csomagok|Egység/s|Átlag|DDoS továbbított bejövő TCP-csomagok|Nincs dimenzió|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|Egység/s|Átlag|Bejövő UDP-csomagok DDoS|Nincs dimenzió|
|UDPPacketsDroppedDDoS|Bejövő UDP-csomagok eldobott DDoS|Egység/s|Átlag|Bejövő UDP-csomagok eldobott DDoS|Nincs dimenzió|
|UDPPacketsForwardedDDoS|DDoS továbbított bejövő UDP-csomagok|Egység/s|Átlag|DDoS továbbított bejövő UDP-csomagok|Nincs dimenzió|
|BytesInDDoS|Bejövő bájtok DDoS|BytesPerSecond|Átlag|Bejövő bájtok DDoS|Nincs dimenzió|
|BytesDroppedDDoS|Bejövő bájtok eldobott DDoS|BytesPerSecond|Átlag|Bejövő bájtok eldobott DDoS|Nincs dimenzió|
|BytesForwardedDDoS|DDoS továbbított bejövő bájtok|BytesPerSecond|Átlag|DDoS továbbított bejövő bájtok|Nincs dimenzió|
|TCPBytesInDDoS|Bejövő TCP bájtok DDoS|BytesPerSecond|Átlag|Bejövő TCP bájtok DDoS|Nincs dimenzió|
|TCPBytesDroppedDDoS|Bejövő TCP-bájt eldobott DDoS|BytesPerSecond|Átlag|Bejövő TCP-bájt eldobott DDoS|Nincs dimenzió|
|TCPBytesForwardedDDoS|DDoS továbbított bejövő TCP bájt|BytesPerSecond|Átlag|DDoS továbbított bejövő TCP bájt|Nincs dimenzió|
|UDPBytesInDDoS|Bejövő UDP bájtok DDoS|BytesPerSecond|Átlag|Bejövő UDP bájtok DDoS|Nincs dimenzió|
|UDPBytesDroppedDDoS|Bejövő UDP bájt eldobott DDoS|BytesPerSecond|Átlag|Bejövő UDP bájt eldobott DDoS|Nincs dimenzió|
|UDPBytesForwardedDDoS|DDoS továbbított bejövő UDP bájt|BytesPerSecond|Átlag|DDoS továbbított bejövő UDP bájt|Nincs dimenzió|
|IfUnderDDoSAttack|DDoS alatt támadásokkal vagy sem|Darabszám|Átlag|DDoS alatt támadásokkal vagy sem|Nincs dimenzió|
|DDoSTriggerTCPPackets|A bejövő TCP-csomagok DDoS megoldás elindítása|Egység/s|Átlag|A bejövő TCP-csomagok DDoS megoldás elindítása|Nincs dimenzió|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok DDoS megoldás elindítása|Egység/s|Átlag|Bejövő UDP-csomagok DDoS megoldás elindítása|Nincs dimenzió|
|VipAvailability|Rendelkezésre állás|Darabszám|Átlag|IP-cím-átlagos rendelkezésre állás időn belül|Port|
|ByteCount|Bájtok száma|Darabszám|Összes|Időtartamon belül átvitt bájtok teljes száma|Port, iránya|
|PacketCount|Csomagok száma|Darabszám|Összes|Időtartamon belül küldött csomagok teljes száma|Port, iránya|
|SynCount|Szinkronizálás a mi száma|Darabszám|Összes|Továbbított időtartamon belül SZIN csomagok száma összesen|Port, iránya|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljesítmény|Teljesítmény|BytesPerSecond|Átlag|Az Alkalmazásátjáró rendelkezik kiszolgált másodpercenként bájtok száma|Nincs dimenzió|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Bújtatás sávszélesség|BytesPerSecond|Átlag|Egy alagút bájt / s átlagos sávszélesség|Kapcsolat neve, RemoteIP|
|TunnelPeakBandwidth|Bújtatás maximális sávszélesség|BytesPerSecond|Átlag|Maximális sávszélesség a alagút bájt / s|Kapcsolat neve, RemoteIP|
|TunnelEgressBytes|Bújtatás kilépő bájt|Bájt|Átlag|Kimenő bájtok öt percenként egy alagút|Kapcsolat neve, RemoteIP|
|TunnelIngressBytes|Bújtatás érkező bájtok|Bájt|Átlag|Bejövő bájtok öt percenként egy alagút|Kapcsolat neve, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagok|Darabszám|Átlag|Egy alagút öt percenként kimenő csomagok száma|Kapcsolat neve, RemoteIP|
|TunnelIngressPackets|Bújtatás érkező csomagok|Darabszám|Átlag|Egy alagút öt percenként bejövő csomagok száma|Kapcsolat neve, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Bújtatás kilépő TS eltérés csomagjai|Darabszám|Átlag|Kimenő forgalom választó eltérés öt percenként alagutat a csomagjai|Kapcsolat neve, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Bújtatás érkező TS eltérés csomagjai|Darabszám|Átlag|Bejövő forgalom választó eltérés öt percenként alagutat a csomagjai|Kapcsolat neve, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesIn|BytesIn|Darabszám|Összes|Bájtok ingressing Azure|Nincs dimenzió|
|BytesOut|BytesOut|Darabszám|Összes|Egressing Azure bájt|Nincs dimenzió|
|BitsInPerSecond|BitsInPerSecond|Egység/s|Átlag|A BITS ingressing Azure / másodperc|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|Egység/s|Átlag|A BITS Azure egressing / másodperc|Nincs dimenzió|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Lekérdezések által visszaadott végpont|Darabszám|Összes|Szám, ahányszor a Traffic Manager-végpont adott vissza az adott időkereten belül|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végpont végpont állapota|Darabszám|Maximum|Ha a végpontok mintavételi állapota "engedélyezve" 1, egyébként pedig 0.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|registration.all|Regisztrálási műveletek|Darabszám|Összes|Az összes sikeres regisztrációkra vonatkozó művelet (létrehozás, frissítés, lekérdezés és törlés) száma. |Nincs dimenzió|
|registration.Create|Regisztráció-létrehozási műveletek|Darabszám|Összes|Az összes sikeres regisztráció-létrehozás száma.|Nincs dimenzió|
|registration.Update|Regisztráció-frissítési műveletek|Darabszám|Összes|Az összes sikeres regisztrációfrissítés száma.|Nincs dimenzió|
|registration.Get|Regisztráció-olvasási műveletek|Darabszám|Összes|Az összes sikeres regisztráció-lekérdezés száma.|Nincs dimenzió|
|registration.delete|Regisztráció-törlési műveletek|Darabszám|Összes|Az összes sikeres regisztrációtörlés száma.|Nincs dimenzió|
|bejövő|Bejövő üzenetek|Darabszám|Összes|Az összes sikeres API-hívásküldés száma. |Nincs dimenzió|
|Incoming.Scheduled|Elküldött ütemezett leküldéses értesítések|Darabszám|Összes|Megszakított ütemezett leküldéses értesítések|Nincs dimenzió|
|Incoming.Scheduled.Cancel|Megszakított ütemezett leküldéses értesítések|Darabszám|Összes|Megszakított ütemezett leküldéses értesítések|Nincs dimenzió|
|Scheduled.Pending|Függőben lévő ütemezett értesítések|Darabszám|Összes|Függőben lévő ütemezett értesítések|Nincs dimenzió|
|Installation.all|Telepítéskezelési műveletek|Darabszám|Összes|Telepítéskezelési műveletek|Nincs dimenzió|
|Installation.Get|Telepítéslekérdezési műveletek|Darabszám|Összes|Telepítéslekérdezési műveletek|Nincs dimenzió|
|Installation.upsert|Telepítés-létrehozási és -frissítési műveletek|Darabszám|Összes|Telepítés-létrehozási és -frissítési műveletek|Nincs dimenzió|
|Installation.Patch|Telepítésjavítási műveletek|Darabszám|Összes|Telepítésjavítási műveletek|Nincs dimenzió|
|Installation.delete|Telepítéstörlési műveletek|Darabszám|Összes|Telepítéstörlési műveletek|Nincs dimenzió|
|outgoing.allpns.SUCCESS|Sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.allpns.invalidpayload|Terhelési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS helytelen adattartalomra vonatkozó hibát adott vissza.|Nincs dimenzió|
|outgoing.allpns.pnserror|Külső értesítési rendszer hibái|Darabszám|Összes|A PNS szolgáltatással való kommunikáció közben történt (hitelesítési problémákon kívüli) hiba miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.allpns.channelerror|Csatornahibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a csatorna érvénytelen volt, nem a megfelelő alkalmazáshoz volt hozzárendelve, szabályozott volt vagy lejárt.|Nincs dimenzió|
|outgoing.allpns.badorexpiredchannel|Rossz vagy lejárt csatorna által okozott hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő csatorna, jogkivonat vagy regisztrációs azonosító lejárt vagy érvénytelen volt.|Nincs dimenzió|
|outgoing.wns.SUCCESS|WNS – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
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
|outgoing.apns.SUCCESS|APNS – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.apns.invalidcredentials|APNS-hitelesítési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.apns.badchannel|APNS – rossz csatorna által okozott hiba|Darabszám|Összes|A jogkivonat érvénytelensége miatt sikertelen leküldések száma (APNS-állapotkód: 8).|Nincs dimenzió|
|outgoing.apns.expiredchannel|APNS – lejárt csatorna által okozott hiba|Darabszám|Összes|Az APNS visszajelzési csatornája által érvénytelenített jogkivonatok száma.|Nincs dimenzió|
|outgoing.apns.invalidnotificationsize|APNS – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (APNS-állapotkód: 7).|Nincs dimenzió|
|outgoing.apns.pnserror|APNS-hibák|Darabszám|Összes|Az APNS szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.SUCCESS|GCM – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.gcm.invalidcredentials|GCM – hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.gcm.badchannel|GCM – rossz csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő regisztrációs azonosító ismeretlen (GCM-eredmény: Invalid Registration).|Nincs dimenzió|
|outgoing.gcm.expiredchannel|GCM – lejárt csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő regisztrációs azonosító lejárt (GCM-eredmény: NotRegistered).|Nincs dimenzió|
|outgoing.gcm.throttled|GCM – szabályozott értesítések|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a GCM szabályozta az alkalmazást. (GCM-állapotkód: 501-599 vagy GCM-eredmény: Unavailable).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationformat|GCM – érvénytelen értesítési formátum|Darabszám|Összes|Az adattartalom helytelen formátuma miatt sikertelen leküldések száma (GCM-eredmény: InvalidDataKey vagy InvalidTtl).|Nincs dimenzió|
|outgoing.gcm.invalidnotificationsize|GCM – érvénytelen értesítésméret által okozott hiba|Darabszám|Összes|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM-eredmény: MessageTooBig).|Nincs dimenzió|
|outgoing.gcm.wrongchannel|GCM – nem megfelelő csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő regisztrációs azonosító nem a jelenlegi alkalmazáshoz van hozzárendelve (GCM-eredmény: InvalidPackageName).|Nincs dimenzió|
|outgoing.gcm.pnserror|GCM-hibák|Darabszám|Összes|A GCM szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.authenticationerror|GCM-hitelesítési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva, vagy hogy a küldő azonosítója nincs megfelelően konfigurálva az alkalmazásban (GCM-eredmény: MismatchedSenderId).|Nincs dimenzió|
|outgoing.mpns.SUCCESS|MPNS – sikeres értesítések|Darabszám|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.mpns.invalidcredentials|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.mpns.badchannel|MPNS – rossz csatorna által okozott hiba|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő csatorna-URI-azonosító ismeretlen (MPNS-állapot: 404 Nem található).|Nincs dimenzió|
|outgoing.mpns.throttled|MPNS – szabályozott értesítések|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy az MPNS szabályozza az alkalmazást. (WNS MPNS: 406 Nem megfelelő).|Nincs dimenzió|
|outgoing.mpns.invalidnotificationformat|MPNS – érvénytelen értesítési formátum|Darabszám|Összes|Az értesítési tartalom túl nagy mérete miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.mpns.channeldisconnected|MPNS – nincs kapcsolat a csatornával|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a regisztrációban szereplő URI-azonosítójú csatornával megszakadt a kapcsolat (MPNS-állapot: 412 Nem található).|Nincs dimenzió|
|outgoing.mpns.dropped|MPNS – elvetett értesítések|Darabszám|Összes|A MPNS által elvetett leküldések száma (MPNS-válaszfejléc: X-NotificationStatus: QueueFull vagy Suppressed).|Nincs dimenzió|
|outgoing.mpns.pnserror|MPNS-hibák|Darabszám|Összes|Az MPNS szolgáltatással való kommunikáció közben történt hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.mpns.authenticationerror|MPNS – hitelesítési hibák|Darabszám|Összes|Az amiatt sikertelen leküldések száma, hogy a PNS nem fogadta el a megadott hitelesítő adatokat vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|notificationhub.pushes|Minden kimenő értesítések|Darabszám|Összes|Az értesítési központnak minden kimenő értesítések|Nincs dimenzió|
|Incoming.all.Requests|Az összes bejövő kérelmek|Darabszám|Összes|Bejövő kérelmek teljes száma a értesítési központ|Nincs dimenzió|
|Incoming.all.failedrequests|Minden bejövő sikertelen kérelmek|Darabszám|Összes|Teljes bejövő sikertelen kérelmek egy értesítési központ|Nincs dimenzió|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|másodperc|Átlag|A keresési szolgáltatás átlagos keresési késés|Nincs dimenzió|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|Egység/s|Átlag|A keresési szolgáltatás másodpercenként keresési lekérdezések|Nincs dimenzió|
|ThrottledSearchQueriesPercentage|Szabályozott lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás volt halmozódni keresési lekérdezések aránya|Nincs dimenzió|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
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
|dw_cpu_percent|DW csomópont szintjén processzor|Százalék|Átlag|DW csomópont szintjén processzor|dw_logical_node_id|
|dw_physical_data_read_percent|DW csomópont adat IO százalékos értéke|Százalék|Átlag|DW csomópont adat IO százalékos értéke|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincs dimenzió|
|database_cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|DatabaseResourceId|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincs dimenzió|
|database_physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|DatabaseResourceId|
|log_write_percent|Napló IO százalékos aránya|Százalék|Átlag|Napló IO százalékos aránya|Nincs dimenzió|
|database_log_write_percent|Napló IO százalékos aránya|Százalék|Átlag|Napló IO százalékos aránya|DatabaseResourceId|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|Nincs dimenzió|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId|
|storage_percent|Tárolási százalékos aránya|Százalék|Átlag|Tárolási százalékos aránya|Nincs dimenzió|
|workers_percent|Feldolgozók százalékos aránya|Százalék|Átlag|Feldolgozók százalékos aránya|Nincs dimenzió|
|database_workers_percent|Feldolgozók százalékos aránya|Százalék|Átlag|Feldolgozók százalékos aránya|DatabaseResourceId|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincs dimenzió|
|database_sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|DatabaseResourceId|
|eDTU_limit|eDTU korlátot|Darabszám|Átlag|eDTU korlátot|Nincs dimenzió|
|storage_limit|Tárolási kapacitása|Bájt|Átlag|Tárolási kapacitása|Nincs dimenzió|
|eDTU_used|felhasznált edtu-ra|Darabszám|Átlag|felhasznált edtu-ra|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|database_storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|DatabaseResourceId|
|xtp_storage_percent|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Százalék|Átlag|A memórián belüli online Tranzakciófeldolgozási tárolási százaléka|Nincs dimenzió|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId, ElasticPoolResourceId|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|ElasticPoolResourceId|
|database_storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|DatabaseResourceId, ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Használható kapacitás|Bájt|Átlag|A fiókot használja a kapacitás|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|A BLOB-kapacitás|Bájt|Átlag|A tárfiók a Blob szolgáltatás bájtban által használt tárolási mennyisége.|BlobType|
|Blobok száma|A BLOB száma|Darabszám|Átlag|A tárfiók a Blob szolgáltatás Blob számát.|BlobType|
|ContainerCount|A BLOB-tároló száma|Darabszám|Átlag|A tárfiók a Blob szolgáltatás tárolók száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Tábla Kapacitás|Bájt|Átlag|A tárfiók Table szolgáltatás bájtban által használt tárolási mennyisége.|Nincs dimenzió|
|TableCount|Tábla száma|Darabszám|Átlag|A tárfiók Table szolgáltatás táblájában száma.|Nincs dimenzió|
|TableEntityCount|Tábla entitások száma|Darabszám|Átlag|A tárfiók Table szolgáltatás a táblaentitásokat száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Várólista kapacitás|Bájt|Átlag|A tárfiók Queue szolgáltatás bájtban által használt tárolási mennyisége.|Nincs dimenzió|
|QueueCount|Várólista száma|Darabszám|Átlag|A tárfiók Queue szolgáltatás sorból száma.|Nincs dimenzió|
|QueueMessageCount|Várólista-üzenetek száma|Darabszám|Átlag|A tárfiók Queue szolgáltatás várólista üzeneteinek hozzávetőleges számát.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|Fájl kapacitás|Bájt|Átlag|A tárfiók bájtban szolgáltatás által használt tároló mennyisége.|Nincs dimenzió|
|Filecount;/%totalfilecount|Fájlok száma|Darabszám|Átlag|A tárfiók szolgáltatása a fájl száma.|Nincs dimenzió|
|FileShareCount|Fájlmegosztás|Darabszám|Átlag|Hány fájl osztja meg a tárfiók fájl szolgáltatásban.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|A társzolgáltatás vagy a megadott API-művelet felé intézett kérések száma. Ez a szám sikeres és sikertelen kérelmeket, valamint a hibák előállított kérelem tartalmazza. A választ más típusú számú ResponseType dimenziót használhat.|ResponseType, GeoType, APINÉV|
|Belépő|Belépő|Bájt|Összes|Az adatmennyiség érkező, bájtban. Ez a szám egy külső ügyféltől érkező tartalmazza az Azure Storage, valamint a bejövő adatok Azure-ban.|GeoType, APINÉV|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|Az adatmennyiség kilépő, bájtban. Ez a szám kilépő külső ügyfélről az Azure Storage, valamint az Azure virtuális tartalmazza. Ez a szám emiatt nem tükrözi számlázható kimenő forgalom.|GeoType, APINÉV|
|SuccessServerLatency|Sikeres kiszolgáló késleltetése|Ezredmásodperc|Átlag|Átlagosan ilyen késéssel jár által az Azure Storage segítségével dolgozza fel a kérelem sikeres, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, APINÉV|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A társzolgáltatás vagy a megadott API-művelet ezredmásodpercben a sikeres kérelmek átlagos végpontok közötti késését. Ezt az értéket tartalmazza a szükséges feldolgozási ideje az Azure Storage olvasni a kérelmet, küldés és a választ kap belül.|GeoType, APINÉV|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A százalékos aránya a társzolgáltatás vagy a megadott API-művelet rendelkezésre állása. Rendelkezésre állási TotalBillableRequests értéke, és azt vonatkozó kérés, beleértve azokat is, váratlan hibák előállított számát elosztjuk számítja ki. Váratlan hibákat eredményez romlik a rendelkezésre állás a társzolgáltatás vagy a megadott API-művelet.|GeoType, APINÉV|

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

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Százalék|Átlag|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Darabszám|Összes|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Darabszám|Összes|HTTP-várólista hossza|Példány|
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
|FunctionExecutionUnits|Függvény-végrehajtási egység|Darabszám|Átlag|Függvény-végrehajtási egység|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Darabszám|Átlag|Függvény végrehajtásainak száma|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funkciók)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Darabszám|Összes|HTTP-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória-munkakészlet|Bájt|Átlag|Memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Átlag|Átlagos memória-munkakészlet|Példány|
|FunctionExecutionUnits|Függvény-végrehajtási egység|Darabszám|Átlag|Függvény-végrehajtási egység|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Darabszám|Átlag|Függvény végrehajtásainak száma|Példány|

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
|FunctionExecutionUnits|Függvény-végrehajtási egység|Darabszám|Átlag|Függvény-végrehajtási egység|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Darabszám|Átlag|Függvény végrehajtásainak száma|Példány|

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
|DiskQueueLength|Lemezvárólista hossza|Darabszám|Összes|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Darabszám|Összes|HTTP-várólista hossza|Példány|
|ActiveRequests|Aktív kérelmek|Darabszám|Összes|Aktív kérelmek|Példány|
|TotalFrontEnds|Előterek száma|Darabszám|Átlag|Előterek száma|Példány|
|SmallAppServicePlanInstances|Kis méretű App Service-csomag feldolgozói|Darabszám|Átlag|Kis méretű App Service-csomag feldolgozói|Példány|
|MediumAppServicePlanInstances|Közepes méretű App Service-csomag feldolgozói|Darabszám|Átlag|Közepes méretű App Service-csomag feldolgozói|Példány|
|LargeAppServicePlanInstances|Nagy méretű App Service-csomag feldolgozói|Darabszám|Átlag|Nagy méretű App Service-csomag feldolgozói|Példány|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Feldolgozók|Feldolgozók összesen|Darabszám|Átlag|Feldolgozók összesen|Példány|
|WorkersAvailable|Rendelkezésre álló feldolgozók|Darabszám|Átlag|Rendelkezésre álló feldolgozók|Példány|
|WorkersUsed|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|Példány|

## <a name="next-steps"></a>Következő lépések
* [Olvassa el a Azure figyelő metrikák](monitoring-overview-metrics.md)
* [Hozzon létre a riasztások mérőszámokat](insights-receive-alert-notifications.md)
* [Tárolás, az Event Hubs vagy Naplóelemzési metrikák exportálása](monitoring-overview-of-diagnostic-logs.md)
