---
title: Azure Monitor támogatott metrikák erőforrástípus szerint
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: ba3535679f37916a18aae5fe7dbe4e9114cea695
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664913"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák Azure Monitor

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. Alább látható az Azure Monitor metrikus folyamatával jelenleg elérhető metrikák teljes listája. Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők. Az alábbi lista csak a konszolidált Azure Monitor metrikai folyamat használatával elérhető metrikákat tartalmazza. A metrikák lekérdezéséhez és eléréséhez használja a [2018-01-01 API-Version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Mennyiség|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|memory_metric|Memória|Bájt|Átlag|Memória. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|private_bytes_metric|Saját bájtok |Bájt|Átlag|A memória teljes mennyisége a Analysis Services motor folyamata és az adategyesítési tároló folyamatai lefoglalva, nem beleértve a más folyamatokkal megosztott memóriát.|ServerResourceType|
|virtual_bytes_metric|Virtuális bájtok |Bájt|Átlag|A Analysis Services motor és az adategyesítési tároló folyamatai által használt virtuális címtartomány jelenlegi mérete.|ServerResourceType|
|TotalConnectionRequests|Kapcsolatkérelmek teljes száma|Mennyiség|Átlag|A kapcsolatkérelmek teljes száma. Ezek az érkezések.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|CountPerSecond|Átlag|A sikeres kapcsolatok gyakorisága.|ServerResourceType|
|TotalConnectionFailures|Összes sikertelen Kapcsolatfelvétel|Mennyiség|Átlag|A sikertelen csatlakozási kísérletek teljes száma.|ServerResourceType|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Mennyiség|Átlag|A létrejött felhasználói munkamenetek aktuális száma.|ServerResourceType|
|QueryPoolBusyThreads|Lekérdezési készlet foglalt szálai|Mennyiség|Átlag|A lekérdezési szál készletében lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolJobQueueLength|Parancssori feladatok várólistájának hossza|Mennyiség|Átlag|A parancsfájl-készlet várólistájában lévő feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Feldolgozási készlet nyomtatási várólistájának hossza|Mennyiség|Átlag|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|ServerResourceType|
|Összege|Kapcsolat: aktuális kapcsolatok|Mennyiség|Átlag|A létesített ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CleanerCurrentPrice|Memória: tisztító – aktuális ár|Mennyiség|Átlag|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: tisztító memória csökkenthető|Bájt|Átlag|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: nem csökkenthető a tisztább memória|Bájt|Átlag|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|ServerResourceType|
|MemoryUsage|Memória: memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memória által leképezett adatmennyiséggel, figyelmen kívül hagyva azokat a memóriát, amelyeket a xVelocity memórián belüli elemzési motorja (VertiPaq) leképezett vagy lefoglalt, és amely meghaladja a xVelocity-memória korlátját.|ServerResourceType|
|MemoryLimitHard|Memória: rögzített memória|Bájt|Átlag|Rögzített memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Memória: magas a memória korlátozása|Bájt|Átlag|Magas memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Memória: kevés a memória korlátja|Bájt|Átlag|Kevés a memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: VertiPaq korlátja|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|Kvóta|Memória: kvóta|Bájt|Átlag|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|ServerResourceType|
|QuotaBlocked|Memória: blokkolt kvóta|Mennyiség|Átlag|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq, nem lapozható|Bájt|Átlag|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq lapozható|Bájt|Átlag|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|ServerResourceType|
|RowsReadPerSec|Feldolgozás: másodpercenként beolvasott sorok száma|CountPerSecond|Átlag|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|ServerResourceType|
|RowsConvertedPerSec|Feldolgozás: másodpercenként konvertált sorok száma|CountPerSecond|Átlag|A sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsWrittenPerSec|Feldolgozás: másodpercenként írt sorok száma|CountPerSecond|Átlag|A sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|CommandPoolBusyThreads|Szálak: a parancssori készlet foglalt szálak|Mennyiség|Átlag|A parancsfájl-készletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Szálak: parancssori készlet üresjárati szálai|Mennyiség|Átlag|Az üresjárati szálak száma a parancs szálának készletében.|ServerResourceType|
|LongParsingBusyThreads|Szálak: hosszú elemzés – foglalt szálak|Mennyiség|Átlag|A foglalt szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingIdleThreads|Szálak: tartós folyamatok elemzése – üresjárati szálak|Mennyiség|Átlag|Az üresjárati szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingJobQueueLength|Szálak: hosszú elemzési feladatok várólistájának hossza|Mennyiség|Átlag|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai|Mennyiség|Átlag|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva|Mennyiség|Átlag|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza|Mennyiség|Átlag|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai|Mennyiség|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Szálak: feldolgozási készlet üresjáratban nem I/O-szálai|Mennyiség|Átlag|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Szálak: lekérdezési készlet – üresjárati szálak|Mennyiség|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet feladatok várólistájának hossza|Mennyiség|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak: rövid elemzés – foglalt szálak|Mennyiség|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Szálak: rövid elemzési üresjárati szálak|Mennyiség|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak: rövid elemzési feladatok várólistájának hossza|Mennyiség|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memória-Kiverés|Százalék|Átlag|Memória átlagos kiverése.|ServerResourceType|
|mashup_engine_qpu_metric|M motor QPU|Mennyiség|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor saját bájtjai |Bájt|Átlag|A memória-adategyesítési tároló folyamatainak teljes mennyisége kiosztott, és nem tartalmazza a más folyamatokkal megosztott memóriát.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M motor virtuális bájtjai |Bájt|Átlag|A virtuális címtartomány összemashup-tároló folyamatainak jelenlegi mérete a.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Mennyiség|Összes|Az átjáróra vonatkozó kérelmek teljes száma egy adott időszakban. A problémák diagnosztizálásához különféle méretek is feldarabolható. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Átjárók összes kérelme|Mennyiség|Összes|Az átjáróra vonatkozó kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát. |Hely, állomásnév|
|SuccessfulRequests|Sikeres átjáró-kérelmek|Mennyiség|Összes|A sikeres átjáró-kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát.|Hely, állomásnév|
|UnauthorizedRequests|Nem engedélyezett átjáróra vonatkozó kérelmek|Mennyiség|Összes| Egy adott időszakban nem engedélyezett átjáró-kérelmek teljes száma. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát.|Hely, állomásnév|
|FailedRequests|Sikertelen átjáró-kérelmek|Mennyiség|Összes|A sikertelen átjáró-kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát.|Hely, állomásnév|
|OtherRequests|Egyéb átjáró-kérelmek|Mennyiség|Összes|Egy adott időszakban a sikeres, jogosulatlan vagy sikertelen kategóriákba nem tartozó átjáró-kérelmek teljes száma. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát. |Hely, állomásnév|
|Időtartam|Az átjárók kéréseinek teljes időtartama|Ezredmásodpercben|Átlag|Az az idő, amikor a API Management egy ügyféltől érkező kérést kap, és amikor az ügyfél válaszát adja vissza.|Hely, állomásnév|
|Kapacitás|Kapacitás|Százalék|Átlag|Egy API Management-példány terhelésének jelzője a megalapozott döntések meghozatala érdekében, hogy a példány méretét nagyobb terhelésre kell-e méretezni.|Földrajzi egység|
|EventHubTotalEvents|Összes EventHub esemény|Mennyiség|Összes|Egy adott időszakban API Management EventHub eljuttatott események teljes száma.|Földrajzi egység|
|EventHubSuccessfulEvents|Sikeres EventHub események|Mennyiség|Összes|A sikeres EventHub események teljes száma egy adott időszakban.|Földrajzi egység|
|EventHubTotalFailedEvents|Sikertelen EventHub események|Mennyiség|Összes|A sikertelen EventHub események teljes száma egy adott időszakban.|Földrajzi egység|
|EventHubRejectedEvents|EventHub-események visszautasítva|Mennyiség|Összes|Az elutasított EventHub-események (helytelen konfiguráció vagy jogosulatlan) teljes száma egy adott időszakban.|Földrajzi egység|
|EventHubThrottledEvents|Szabályozott EventHub események|Mennyiség|Összes|Egy adott időszakban a szabályozott EventHub események teljes száma.|Földrajzi egység|
|EventHubTimedoutEvents|Időtúllépés a EventHub eseményeinél|Mennyiség|Összes|Egy adott időszakon belül a EventHub események teljes száma.|Földrajzi egység|
|EventHubDroppedEvents|Eldobott EventHub események|Mennyiség|Összes|Egy adott időszakban a várólista-méretre vonatkozó korlát miatt kihagyott események teljes száma.|Földrajzi egység|
|EventHubTotalBytesSent|EventHub-események mérete|Bájt|Összes|A EventHub-események teljes mérete bájtban egy adott időszakban.|Földrajzi egység|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok összesen|Mennyiség|Összes|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentRuns|Összes frissítés központi telepítési futtatása|Mennyiség|Összes|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, állapot|
|TotalUpdateDeploymentMachineRuns|Összes frissítés központi telepítési gép futtatása|Mennyiség|Összes|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft. batch/batchAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált mag száma|Mennyiség|Összes|A Batch-fiókban lévő dedikált magok teljes száma|Nincsenek méretek|
|TotalNodeCount|Dedikált csomópontok száma|Mennyiség|Összes|A Batch-fiókban lévő dedikált csomópontok teljes száma|Nincsenek méretek|
|LowPriorityCoreCount|LowPriority mag száma|Mennyiség|Összes|Az alacsony prioritású magok teljes száma a Batch-fiókban|Nincsenek méretek|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Mennyiség|Összes|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|Nincsenek méretek|
|CreatingNodeCount|Csomópontok számának létrehozása|Mennyiség|Összes|Létrehozandó csomópontok száma|Nincsenek méretek|
|StartingNodeCount|Csomópontok számának indítása|Mennyiség|Összes|Kiinduló csomópontok száma|Nincsenek méretek|
|WaitingForStartTaskNodeCount|Várakozás a feladat-csomópontok számának megkezdésére|Mennyiség|Összes|Az indítási tevékenység befejezésére váró csomópontok száma|Nincsenek méretek|
|StartTaskFailedNodeCount|Indítási feladat sikertelen csomópontok száma|Mennyiség|Összes|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|Nincsenek méretek|
|IdleNodeCount|Tétlen csomópontok száma|Mennyiség|Összes|Üresjárati csomópontok száma|Nincsenek méretek|
|OfflineNodeCount|Offline csomópontok száma|Mennyiség|Összes|Offline csomópontok száma|Nincsenek méretek|
|RebootingNodeCount|Csomópontok számának újraindítása|Mennyiség|Összes|Újraindítási csomópontok száma|Nincsenek méretek|
|ReimagingNodeCount|Csomópontok rendszerképének alaphelyzetbe állítása|Mennyiség|Összes|Rendszerkép-csomópontok száma|Nincsenek méretek|
|RunningNodeCount|Csomópontok száma|Mennyiség|Összes|Futó csomópontok száma|Nincsenek méretek|
|LeavingPoolNodeCount|A készlet-csomópontok számának elhagyása|Mennyiség|Összes|A készletet elhagyó csomópontok száma|Nincsenek méretek|
|UnusableNodeCount|Nem használható csomópontok száma|Mennyiség|Összes|Használhatatlan csomópontok száma|Nincsenek méretek|
|PreemptedNodeCount|Előzik-csomópontok száma|Mennyiség|Összes|Előzik-csomópontok száma|Nincsenek méretek|
|TaskStartEvent|Tevékenységek indítási eseményei|Mennyiség|Összes|Az elindított feladatok teljes száma|Nincsenek méretek|
|TaskCompleteEvent|Feladat teljes eseményei|Mennyiség|Összes|A Befejezett feladatok teljes száma|Nincsenek méretek|
|TaskFailEvent|Feladat sikertelen eseményei|Mennyiség|Összes|A meghiúsult állapotban Befejezett feladatok teljes száma|Nincsenek méretek|
|PoolCreateEvent|Készlet-létrehozási események|Mennyiség|Összes|A létrehozott készletek teljes száma|Nincsenek méretek|
|PoolResizeStartEvent|Készlet átméretezésének indítási eseményei|Mennyiség|Összes|A készlet elindított újraméretezésének teljes száma|Nincsenek méretek|
|PoolResizeCompleteEvent|Készlet átméretezése – befejezett események|Mennyiség|Összes|A készlet összes újraméretezésének száma|Nincsenek méretek|
|PoolDeleteStartEvent|Készlet törlése – indítási események|Mennyiség|Összes|Az elindított készlet-törlések száma összesen|Nincsenek méretek|
|PoolDeleteCompleteEvent|Készlet törlése – befejezett események|Mennyiség|Összes|A befejezett készlet-törlések teljes száma|Nincsenek méretek|
|JobDeleteCompleteEvent|Feladatok törlése – befejezett események|Mennyiség|Összes|A sikeresen törölt feladatok teljes száma.|Nincsenek méretek|
|JobDeleteStartEvent|Feladatok törlésének indítási eseményei|Mennyiség|Összes|A törölni kívánt feladatok teljes száma.|Nincsenek méretek|
|JobDisableCompleteEvent|A feladatok letiltják a teljes eseményeket|Mennyiség|Összes|A sikeresen letiltott feladatok teljes száma.|Nincsenek méretek|
|JobDisableStartEvent|Feladatok letiltásának indítási eseményei|Mennyiség|Összes|A letiltani kívánt feladatok teljes száma.|Nincsenek méretek|
|JobStartEvent|Feladatok indítási eseményei|Mennyiség|Összes|A sikeresen elindított feladatok teljes száma.|Nincsenek méretek|
|JobTerminateCompleteEvent|A feladatokhoz tartozó befejezett események befejezése|Mennyiség|Összes|A sikeresen leállított feladatok teljes száma.|Nincsenek méretek|
|JobTerminateStartEvent|Feladatokból indított események leállítása|Mennyiség|Összes|A leállítani kívánt feladatok teljes száma.|Nincsenek méretek|

## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakoztatott ügyfelek|Mennyiség|Maximum||ShardId|
|totalcommandsprocessed|Összes művelet|Mennyiség|Összes||ShardId|
|cachehits|Gyorsítótár-találatok|Mennyiség|Összes||ShardId|
|cachemisses|Gyorsítótár-lemaradás|Mennyiség|Összes||ShardId|
|getcommands|Kap|Mennyiség|Összes||ShardId|
|setcommands|Beállítja|Mennyiség|Összes||ShardId|
|operationsPerSecond|Műveletek száma másodpercenként|Mennyiség|Maximum||ShardId|
|evictedkeys|Kizárt kulcsok|Mennyiség|Összes||ShardId|
|totalkeys|Kulcsok összesen|Mennyiség|Maximum||ShardId|
|expiredkeys|Lejárt kulcsok|Mennyiség|Összes||ShardId|
|usedmemory|Felhasznált memória|Bájt|Maximum||ShardId|
|usedmemorypercentage|Felhasznált memória százalékos aránya|Százalék|Maximum||ShardId|
|usedmemoryRss|Felhasznált memória RSS-címe|Bájt|Maximum||ShardId|
|serverLoad|Kiszolgáló terhelése|Százalék|Maximum||ShardId|
|cacheWrite|Gyorsítótár írása|BytesPerSecond|Maximum||ShardId|
|cacheRead|Gyorsítótár-olvasás|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Százalék|Maximum||ShardId|
|cacheLatency|Gyorsítótár késési másodpercek (előzetes verzió)|Mennyiség|Átlag||ShardId, SampleType|
|Hibák|Hibák|Mennyiség|Maximum||ShardId, ErrorType|
|connectedclients0|Csatlakoztatott ügyfelek (0. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed0|Összes művelet (0. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|cachehits0|Gyorsítótárbeli találatok (szegmens 0)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses0|Gyorsítótár-kihagyás (0. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|getcommands0|Beolvasás (0. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|setcommands0|Készletek (0. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond0|Művelet/másodperc (0. Szilánk)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys0|Kizárt kulcsok (0. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys0|Összes kulcs (szilánk 0)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys0|Lejárt kulcsok (szilánk 0)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory0|Felhasznált memória (0. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss0|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||Nincsenek méretek|
|serverLoad0|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite0|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead0|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime0|CPU (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|connectedclients1|Csatlakoztatott ügyfelek (1. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed1|Összes művelet (1. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|cachehits1|Gyorsítótárbeli találatok (1. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses1|Gyorsítótár-lemaradás (1. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|getcommands1|Beolvasás (1. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|setcommands1|Készletek (1. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond1|Művelet/másodperc (1. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys1|Kizárt kulcsok (1. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys1|Összes kulcs (1. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys1|Lejárt kulcsok (1. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory1|Felhasznált memória (1. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss1|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||Nincsenek méretek|
|serverLoad1|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite1|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead1|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime1|CPU (1. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|connectedclients2|Csatlakoztatott ügyfelek (2. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed2|Összes művelet (2. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|cachehits2|Gyorsítótárbeli találatok (2. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses2|Gyorsítótár-lemaradás (2. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|getcommands2|Beolvasás (2. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|setcommands2|Készletek (2. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond2|Művelet/másodperc (2. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys2|Kizárt kulcsok (2. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys2|Összes kulcs (2. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys2|Lejárt kulcsok (2. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory2|Felhasznált memória (2. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss2|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||Nincsenek méretek|
|serverLoad2|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite2|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead2|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime2|CPU (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|connectedclients3|Csatlakoztatott ügyfelek (3. Szilánk)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed3|Összes művelet (3. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|cachehits3|Gyorsítótárbeli találatok (3. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses3|Gyorsítótár-lemaradás (3. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|getcommands3|Beolvasás (3. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|setcommands3|Készletek (3. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond3|Művelet/másodperc (3. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys3|Kizárt kulcsok (3. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys3|Összes kulcs (3. Szilánk)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys3|Lejárt kulcsok (3. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory3|Felhasznált memória (3. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss3|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|serverLoad3|Kiszolgáló terhelése (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite3|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead3|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime3|CPU (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|connectedclients4|Csatlakoztatott ügyfelek (4. Szilánk)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed4|Összes művelet (4. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|cachehits4|Gyorsítótár-találatok (4. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses4|Gyorsítótár-lemaradás (4. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|getcommands4|Beolvasás (4. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|setcommands4|Készletek (4. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond4|Művelet/másodperc (4. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys4|Kizárt kulcsok (4. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys4|Összes kulcs (4. Szilánk)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys4|Lejárt kulcsok (4. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory4|Felhasznált memória (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss4|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|serverLoad4|Kiszolgáló terhelése (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite4|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead4|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime4|CPU (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|connectedclients5|Csatlakoztatott ügyfelek (5. Szilánk)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed5|Összes művelet (5. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|cachehits5|Gyorsítótárbeli találatok (szegmens 5)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses5|Gyorsítótár-lemaradás (5. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|getcommands5|Beolvasás (szegmens 5)|Mennyiség|Összes||Nincsenek méretek|
|setcommands5|Készletek (szilánk 5)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond5|Művelet/másodperc (5. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys5|Kizárt kulcsok (5. Szilánk)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys5|Összes kulcs (5. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys5|Lejárt kulcsok (szilánk 5)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory5|Felhasznált memória (5. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss5|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||Nincsenek méretek|
|serverLoad5|Kiszolgáló terhelése (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite5|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead5|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime5|CPU (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|connectedclients6|Csatlakoztatott ügyfelek (6. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed6|Összes művelet (szegmens 6)|Mennyiség|Összes||Nincsenek méretek|
|cachehits6|Gyorsítótárbeli találatok (szilánk 6)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses6|Gyorsítótár-lemaradás (szegmens 6)|Mennyiség|Összes||Nincsenek méretek|
|getcommands6|Beolvasás (szilánk 6)|Mennyiség|Összes||Nincsenek méretek|
|setcommands6|Készletek (szilánk 6)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond6|Művelet/másodperc (szegmens 6)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys6|Kizárt kulcsok (szegmens 6)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys6|Összes kulcs (szilánk 6)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys6|Lejárt kulcsok (szilánk 6)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory6|Felhasznált memória (6. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss6|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|serverLoad6|Kiszolgáló betöltése (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite6|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead6|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime6|CPU (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|connectedclients7|Csatlakoztatott ügyfelek (7. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed7|Összes művelet (7. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|cachehits7|Gyorsítótárbeli találatok (szegmens 7)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses7|Gyorsítótár-lemaradás (7. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|getcommands7|Beolvasás (szegmens 7)|Mennyiség|Összes||Nincsenek méretek|
|setcommands7|Készletek (szegmens 7)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond7|Művelet/másodperc (7. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys7|Kizárt kulcsok (7. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys7|Összes kulcs (szegmens 7)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys7|Lejárt kulcsok (7. szegmens)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory7|Felhasznált memória (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss7|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|serverLoad7|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite7|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead7|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime7|PROCESSZOR (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|connectedclients8|Csatlakoztatott ügyfelek (10. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed8|Összes művelet (szegmens 8)|Mennyiség|Összes||Nincsenek méretek|
|cachehits8|Gyorsítótárbeli találatok (szilánk 8)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses8|Gyorsítótár-lemaradás (szegmens 8)|Mennyiség|Összes||Nincsenek méretek|
|getcommands8|Beolvasás (szilánk 8)|Mennyiség|Összes||Nincsenek méretek|
|setcommands8|Készletek (szilánk 8)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond8|Művelet/másodperc (szegmens 8)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys8|Kizárt kulcsok (szilánk 8)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys8|Összes kulcs (szilánk 8)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys8|Lejárt kulcsok (szilánk 8)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory8|Felhasznált memória (8. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss8|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||Nincsenek méretek|
|serverLoad8|Kiszolgáló betöltése (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite8|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead8|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime8|CPU (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|connectedclients9|Csatlakoztatott ügyfelek (10. szegmens)|Mennyiség|Maximum||Nincsenek méretek|
|totalcommandsprocessed9|Összes művelet (szegmens 9)|Mennyiség|Összes||Nincsenek méretek|
|cachehits9|Gyorsítótárbeli találatok (szilánk 9)|Mennyiség|Összes||Nincsenek méretek|
|cachemisses9|Gyorsítótár-lemaradás (szegmens 9)|Mennyiség|Összes||Nincsenek méretek|
|getcommands9|Beolvasás (szegmens 9)|Mennyiség|Összes||Nincsenek méretek|
|setcommands9|Készletek (szilánk 9)|Mennyiség|Összes||Nincsenek méretek|
|operationsPerSecond9|Művelet/másodperc (szegmens 9)|Mennyiség|Maximum||Nincsenek méretek|
|evictedkeys9|Kizárt kulcsok (szilánk 9)|Mennyiség|Összes||Nincsenek méretek|
|totalkeys9|Összes kulcs (szilánk 9)|Mennyiség|Maximum||Nincsenek méretek|
|expiredkeys9|Lejárt kulcsok (szilánk 9)|Mennyiség|Összes||Nincsenek méretek|
|usedmemory9|Felhasznált memória (10. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss9|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||Nincsenek méretek|
|serverLoad9|Kiszolgáló terhelése (szegmens 9)|Százalék|Maximum||Nincsenek méretek|
|cacheWrite9|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead9|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|percentProcessorTime9|CPU (szilánk 9)|Százalék|Maximum||Nincsenek méretek|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincsenek méretek|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincsenek méretek|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|Nincsenek méretek|
|Lemez olvasási sebessége (bájt/s)|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|Nincsenek méretek|
|Lemez írási sebessége (bájt/s)|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|Nincsenek méretek|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|Nincsenek méretek|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Lemez olvasási sebessége (bájt/s)|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalCalls|Hívások összesen|Mennyiség|Összes|A hívások száma összesen|ApiName, OperationName, régió|
|SuccessfulCalls|Sikeres hívások|Mennyiség|Összes|A sikeres hívások száma.|ApiName, OperationName, régió|
|TotalErrors|Összes hiba|Mennyiség|Összes|A hibaüzenettel rendelkező hívások teljes száma (HTTP-válasz kódja 4xx vagy 5xx).|ApiName, OperationName, régió|
|BlockedCalls|Blokkolt hívások|Mennyiség|Összes|Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|ApiName, OperationName, régió|
|Kiszolgálóhibái|Kiszolgálói hibák|Mennyiség|Összes|A szolgáltatás belső hibája miatti hívások száma (HTTP-válasz kódja 5xx).|ApiName, OperationName, régió|
|ClientErrors|Ügyfél-hibák|Mennyiség|Összes|Ügyféloldali hibával rendelkező hívások száma (HTTP-válasz kódja 4xx).|ApiName, OperationName, régió|
|DataIn|A-ben tárolt adatértékek|Bájt|Összes|A bejövő adat mérete bájtban megadva.|ApiName, OperationName, régió|
|DataOut|Kimenő adatvesztés|Bájt|Összes|A kimenő adat mérete bájtban kifejezve.|ApiName, OperationName, régió|
|Késés|Késés|Ezredmásodpercben|Átlag|Késés ezredmásodpercben.|ApiName, OperationName, régió|
|CharactersTranslated|Lefordított karakterek|Mennyiség|Összes|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTrained|Betanított karakterek|Mennyiség|Összes|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|SpeechSessionDuration|Beszédfelismerési munkamenet időtartama|másodperc|Összes|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|TotalTransactions|Tranzakciók összesen|Mennyiség|Összes|A tranzakciók száma összesen|Nincsenek méretek|
|TotalTokenCalls|Jogkivonat-hívások összesen|Mennyiség|Összes|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Bejövő hálózat|Számlázandó hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Hálózati kimenő számlázandó|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren kimenő számlázható bájtok száma (kimenő forgalom)|Nincsenek méretek|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összes|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Mennyiség|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Mennyiség|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD (elavult)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez várólistájának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Bejövő folyamatok|Bejövő folyamatok (előzetes verzió)|Mennyiség|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Kimenő folyamatok|Kimenő folyamatok (előzetes verzió)|Mennyiség|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|
|Teljes hálózat|Teljes hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Bejövő hálózat|Számlázandó hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat|Hálózati kimenő számlázandó|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren kimenő számlázható bájtok száma (kimenő forgalom)|VMName|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összes|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|VMName|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|VMName|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Mennyiség|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Mennyiség|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD (elavult)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez várólistájának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|Bejövő folyamatok|Bejövő folyamatok (előzetes verzió)|Mennyiség|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő folyamatok|Kimenő folyamatok (előzetes verzió)|Mennyiség|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|
|Teljes hálózat|Teljes hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|CPU-használat|Mennyiség|Átlag|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Memóriahasználat|Bájt|Átlag|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Másodpercenként fogadott hálózati bájtok száma|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|Nincsenek méretek|
|NetworkBytesTransmittedPerSecond|Másodpercenként továbbított hálózati bájtok száma|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|Nincsenek méretek|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalPullCount|Lekérések száma összesen|Mennyiség|Átlag|A lekérések száma összesen|Nincsenek méretek|
|SuccessfulPullCount|Sikeres lekérések száma|Mennyiség|Átlag|Sikeres Képkeresések száma|Nincsenek méretek|
|TotalPushCount|Leküldések száma összesen|Mennyiség|Átlag|Leküldések száma összesen|Nincsenek méretek|
|SuccessfulPushCount|Sikeres leküldések száma|Mennyiség|Átlag|Sikeres leküldések száma|Nincsenek méretek|
|RunDuration|Futtatás időtartama|Ezredmásodpercben|Összes|Futtatás időtartama (ezredmásodpercben)|Nincsenek méretek|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Mennyiség|Összes|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Nincsenek méretek|
|kube_node_status_allocatable_memory_bytes|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Összes|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Nincsenek méretek|
|kube_pod_status_ready|A hüvelyek száma üzemkész állapotban|Mennyiség|Összes|A hüvelyek száma üzemkész állapotban|névtér, Pod|
|kube_node_status_condition|Különböző csomóponti feltételek állapota|Mennyiség|Összes|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|Hüvelyek száma fázis szerint|Mennyiség|Összes|Hüvelyek száma fázis szerint|fázis, névtér, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft. CustomerInsights/hubok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DCIApiCalls|API-hívások Customer Insights|Mennyiség|Összes||Nincsenek méretek|
|DCIMappingImportOperationSuccessfulLines|Az importálási művelet sikeres sorainak leképezése|Mennyiség|Összes||Nincsenek méretek|
|DCIMappingImportOperationFailedLines|Az importálási művelet sikertelen sorainak megfeleltetése|Mennyiség|Összes||Nincsenek méretek|
|DCIMappingImportOperationTotalLines|Az importálási művelet összes sorának leképezése|Mennyiség|Összes||Nincsenek méretek|
|DCIMappingImportOperationRuntimeInSeconds|Az importálási művelet futtatókörnyezetének leképezése másodpercben|másodperc|Összes||Nincsenek méretek|
|DCIOutboundProfileExportSucceeded|A kimenő profil exportálása sikerült|Mennyiség|Összes||Nincsenek méretek|
|DCIOutboundProfileExportFailed|A kimenő profil exportálása nem sikerült|Mennyiség|Összes||Nincsenek méretek|
|DCIOutboundProfileExportDuration|Kimenő profil exportálásának időtartama|másodperc|Összes||Nincsenek méretek|
|DCIOutboundKpiExportSucceeded|A kimenő KPI exportálása sikerült|Mennyiség|Összes||Nincsenek méretek|
|DCIOutboundKpiExportFailed|A kimenő KPI exportálása nem sikerült|Mennyiség|Összes||Nincsenek méretek|
|DCIOutboundKpiExportDuration|Kimenő KPI-exportálás időtartama|másodperc|Összes||Nincsenek méretek|
|DCIOutboundKpiExportStarted|Kimenő KPI-exportálás elindítva|másodperc|Összes||Nincsenek méretek|
|DCIOutboundKpiRecordCount|Kimenő KPI-rekordok száma|másodperc|Összes||Nincsenek méretek|
|DCIOutboundProfileExportCount|Kimenő profilok exportálásának száma|másodperc|Összes||Nincsenek méretek|
|DCIOutboundInitialProfileExportFailed|A kimenő kezdeti profil exportálása nem sikerült|másodperc|Összes||Nincsenek méretek|
|DCIOutboundInitialProfileExportSucceeded|A kimenő kezdeti profil exportálása sikerült|másodperc|Összes||Nincsenek méretek|
|DCIOutboundInitialKpiExportFailed|A kimenő kezdeti KPI-exportálás nem sikerült|másodperc|Összes||Nincsenek méretek|
|DCIOutboundInitialKpiExportSucceeded|A kimenő kezdeti KPI-exportálás sikerült|másodperc|Összes||Nincsenek méretek|
|DCIOutboundInitialProfileExportDurationInSeconds|Kimenő kezdeti profil exportálásának időtartama másodpercben|másodperc|Összes||Nincsenek méretek|
|AdlaJobForStandardKpiFailed|A standard KPI ADla-feladata másodpercek alatt meghiúsult|másodperc|Összes||Nincsenek méretek|
|AdlaJobForStandardKpiTimeOut|ADla-feladatot a standard KPI-időkorlát másodpercben|másodperc|Összes||Nincsenek méretek|
|AdlaJobForStandardKpiCompleted|A standard KPI ADla-feladata másodpercek alatt befejeződött|másodperc|Összes||Nincsenek méretek|
|ImportASAValuesFailed|Nem sikerült importálni az ASA-értékeket|Mennyiség|Összes||Nincsenek méretek|
|ImportASAValuesSucceeded|Az ASA-értékek importálása sikerült|Mennyiség|Összes||Nincsenek méretek|
|DCIProfilesCount|Profil példányainak száma|Mennyiség|utolsó||Nincsenek méretek|
|DCIInteractionsPerMonthCount|Interakciók havonta – darabszám|Mennyiség|utolsó||Nincsenek méretek|
|DCIKpisCount|KPI-darabszám|Mennyiség|utolsó||Nincsenek méretek|
|DCISegmentsCount|Szegmensek száma|Mennyiség|utolsó||Nincsenek méretek|
|DCIPredictiveMatchPoliciesCount|Prediktív egyezések száma|Mennyiség|utolsó||Nincsenek méretek|
|DCIPredictionsCount|Előrejelzési szám|Mennyiség|utolsó||Nincsenek méretek|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|NICReadThroughput|Olvasási sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter olvasási átviteli sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|NICWriteThroughput|Írási átviteli sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter írási sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|CloudReadThroughputPerShare|Felhőbeli letöltési teljesítmény (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való letöltési átviteli sebesség a jelentési időszak alatt.|Megosztás|
|CloudUploadThroughputPerShare|Felhőbeli feltöltési sebesség (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való feltöltési átviteli sebesség a jelentési időszak során.|Megosztás|
|BytesUploadedToCloudPerShare|Felhőbeli feltöltött bájtok (megosztás)|Bájt|Átlag|A jelentési időszak során az Azure-ba feltöltött bájtok teljes száma.|Megosztás|
|TotalCapacity|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|Nincsenek méretek|
|Availablecapacity;)|Rendelkezésre álló kapacitás|Bájt|Átlag|A rendelkezésre álló kapacitás bájtban a jelentési időszak alatt.|Nincsenek méretek|
|CloudUploadThroughput|Felhőbeli feltöltési sebesség|BytesPerSecond|Átlag|A Felhőbeli feltöltési sebesség az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudReadThroughput|Felhőbeli letöltési sebesség|BytesPerSecond|Átlag|A felhő letöltési sebessége az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|BytesUploadedToCloud|Feltöltött Felhőbeli bájtok (eszköz)|Bájt|Átlag|Az Azure-ba az eszközről a jelentési időszakban feltöltött bájtok teljes száma.|Nincsenek méretek|
|HyperVVirtualProcessorUtilization|Edge-számítás – százalékos CPU|Százalék|Átlag|CPU-használat százalékos aránya|InstanceName|
|HyperVMemoryUtilization|Edge-számítás – memóriahasználat|Százalék|Átlag|Használatban lévő RAM mennyisége|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Sikertelen futtatások|Mennyiség|Összes||pipelineName, activityName|
|SuccessfulRuns|Sikeres futtatások|Mennyiség|Összes||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Sikertelen folyamat-futtatási metrikák|Mennyiség|Összes||FailureType, név|
|PipelineSucceededRuns|A folyamat sikeresen futtatja a metrikákat|Mennyiség|Összes||FailureType, név|
|ActivityFailedRuns|Sikertelen tevékenység-futtatási metrikák|Mennyiség|Összes||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|A sikeres tevékenység metrikákat futtat|Mennyiség|Összes||ActivityType, PipelineName, FailureType, név|
|TriggerFailedRuns|Sikertelen trigger-futtatási metrikák|Mennyiség|Összes||Név, FailureType|
|TriggerSucceededRuns|A sikeres trigger metrikákat futtat|Mennyiség|Összes||Név, FailureType|
|IntegrationRuntimeCpuPercentage|Integration Runtime CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableMemory|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|
|MaxAllowedResourceCount|Engedélyezett entitások maximális száma|Mennyiség|Maximum||Nincsenek méretek|
|MaxAllowedFactorySizeInGbUnits|Maximálisan engedélyezett gyári méret (GB egység)|Mennyiség|Maximum||Nincsenek méretek|
|ResourceCount|Entitások száma összesen|Mennyiség|Maximum||Nincsenek méretek|
|FactorySizeInGbUnits|Gyári méret összesen (GB egység)|Mennyiség|Maximum||Nincsenek méretek|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Mennyiség|Összes|A sikeres feladatok száma.|Nincsenek méretek|
|JobEndedFailure|Sikertelen feladatok|Mennyiség|Összes|Sikertelen feladatok száma.|Nincsenek méretek|
|JobEndedCancelled|Megszakított feladatok|Mennyiség|Összes|Megszakított feladatok száma.|Nincsenek méretek|
|JobAUEndedSuccess|Sikeres AU-idő|másodperc|Összes|A sikeres feladatok teljes AU-ideje.|Nincsenek méretek|
|JobAUEndedFailure|Sikertelen AU-idő|másodperc|Összes|A sikertelen feladatok összes AU-ideje.|Nincsenek méretek|
|JobAUEndedCancelled|Megszakított AU-idő|másodperc|Összes|A megszakított feladatok összes AU-ideje.|Nincsenek méretek|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|Nincsenek méretek|
|DataWritten|Írt adatértékek|Bájt|Összes|A fiókba írt összes adatmennyiség.|Nincsenek méretek|
|DataRead|Olvasott információk|Bájt|Összes|A fiókból beolvasott adatok teljes mennyisége.|Nincsenek méretek|
|WriteRequests|Írási kérelmek|Mennyiség|Összes|A fiókra vonatkozó adatírási kérelmek száma.|Nincsenek méretek|
|ReadRequests|Olvasási kérelmek|Mennyiség|Összes|A fióknak küldött adatolvasási kérelmek száma.|Nincsenek méretek|

## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|
|storage_limit|Tárolási korlát|Bájt|Átlag|Tárolási korlát|Nincsenek méretek|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|active_connections|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|Nincsenek méretek|
|seconds_behind_master|Replikálás késése másodpercben|Mennyiség|Átlag|Replikálás késése másodpercben|Nincsenek méretek|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|
|storage_limit|Tárolási korlát|Bájt|Átlag|Tárolási korlát|Nincsenek méretek|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|active_connections|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|Nincsenek méretek|
|seconds_behind_master|Replikálás késése másodpercben|Mennyiség|Átlag|Replikálás késése másodpercben|Nincsenek méretek|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|
|storage_limit|Tárolási korlát|Bájt|Átlag|Tárolási korlát|Nincsenek méretek|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|active_connections|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|Nincsenek méretek|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|pg_replica_log_delay_in_seconds|Replika késése|másodperc|Maximum|Replika késése másodpercben|Nincsenek méretek|
|pg_replica_log_delay_in_bytes|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|Nincsenek méretek|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|IOPS|IO|Mennyiség|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|
|active_connections|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|

## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|D2C. telemetria. beáramló. allProtocol|Telemetria üzenetek küldése|Mennyiség|Összes|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sikeres|Telemetria üzenetek elküldése|Mennyiség|Összes|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincsenek méretek|
|C2D. commands. kimenő. Complete. success|A parancsok befejeződtek|Mennyiség|Összes|Az eszköz által sikeresen befejeződött a felhőből az eszközre irányuló parancsok száma|Nincsenek méretek|
|C2D. commands. kimenő. elhagyása. sikeres|Elhagyott parancsok|Mennyiség|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|Nincsenek méretek|
|C2D. commands. kimenő. elutasítás. sikeres|Elutasított parancsok|Mennyiség|Összes|Az eszköz által visszautasított felhőből az eszközre irányuló parancsok száma|Nincsenek méretek|
|Devices. totalDevices|Összes eszköz (elavult)|Mennyiség|Összes|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|Devices. connectedDevices. allProtocol|Csatlakoztatott eszközök (elavult) |Mennyiség|Összes|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. telemetria. kimenő. sikeres|Útválasztás: telemetria üzenetek kézbesítése|Mennyiség|Összes|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincsenek méretek|
|D2C. telemetria. kimenő. eldobott|Útválasztás: telemetria üzenetek elvetve |Mennyiség|Összes|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincsenek méretek|
|D2C. telemetria. kimenő. árva|Útválasztás: árva telemetria üzenetek |Mennyiség|Összes|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincsenek méretek|
|D2C. telemetria. kimenő. érvénytelen|Útválasztás: Inkompatibilis telemetria-üzenetek|Mennyiség|Összes|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincsenek méretek|
|D2C. telemetria. kimenő. tartalék|Útválasztás: tartalékként továbbított üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincsenek méretek|
|D2C. endpoints. kimenő. eventHubs|Útválasztás: az Event hub számára továbbított üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. késleltetés. eventHubs|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusQueues|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusQueues|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusTopics|Útválasztás: Service Bus témakörbe küldött üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusTopics|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. kimenő. beépített. események|Útválasztás: üzenetek/események küldésére küldött üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események). Ez a metrika csak akkor kezd működni, ha engedélyezve van az Útválasztás (https://aka.ms/iotrouting) az IoT hub esetében.|Nincsenek méretek|
|D2C. endpoints. késleltetés. beépített. események|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események). Ez a metrika csak akkor kezd működni, ha engedélyezve van az Útválasztás (https://aka.ms/iotrouting) az IoT hub esetében.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage|Útválasztás: a tárolóba küldött üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincsenek méretek|
|D2C. endpoints. látencia. Storage|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Bytes|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összes|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Blobok|Útválasztás: tárolóba szállított Blobok|Mennyiség|Összes|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincsenek méretek|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Mennyiség|Összes|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát (https://aka.ms/ioteventgrid).|Eredmény, EventType|
|EventGridLatency|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|D2C. Twin. Read. success|Sikeres dupla olvasások az eszközökről|Mennyiség|Összes|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|D2C. Twin. Read. failure|Sikertelen dupla olvasások az eszközökről|Mennyiség|Összes|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincsenek méretek|
|D2C. Twin. Read. size|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|D2C. Twin. Update. success|Sikeres dupla frissítések az eszközökről|Mennyiség|Összes|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincsenek méretek|
|D2C. Twin. Update. failure|Sikertelen dupla frissítések az eszközökről|Mennyiség|Összes|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|D2C. Twin. Update. size|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|C2D. Methods. success|Közvetlen metódusok sikeres meghívása|Mennyiség|Összes|Az összes sikeres közvetlen metódus hívásának száma.|Nincsenek méretek|
|C2D. Methods. failure|Sikertelen közvetlen metódusok meghívása|Mennyiség|Összes|A sikertelen közvetlen metódusok összes hívásának száma.|Nincsenek méretek|
|C2D. Methods. requestSize|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. responseSize|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Twin. Read. success|Sikeres dupla olvasások a háttérből|Mennyiség|Összes|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Read. failure|Sikertelen dupla olvasások a háttérből|Mennyiség|Összes|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Read. size|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Twin. Update. success|Sikeres dupla frissítések a háttérből|Mennyiség|Összes|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2D. Twin. Update. failure|Sikertelen dupla frissítések a háttérből|Mennyiség|Összes|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2D. Twin. Update. size|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|twinQueries. success|Sikeres Twin-lekérdezések|Mennyiség|Összes|Az összes sikeres dupla lekérdezés száma.|Nincsenek méretek|
|twinQueries. hiba|Sikertelen dupla lekérdezések|Mennyiség|Összes|Az összes sikertelen dupla lekérdezés száma.|Nincsenek méretek|
|twinQueries.resultSize|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. sikeres|A kettős frissítési feladatok sikeres létrehozása|Mennyiség|Összes|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. hiba|A kettős frissítési feladatok sikertelen létrehozása|Mennyiség|Összes|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. sikeres|Metódus-Meghívási feladatok sikeres létrehozása|Mennyiség|Összes|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. hiba|Nem sikerült létrehozni a metódus Meghívási feladatait|Mennyiség|Összes|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. listJobs. sikeres|Sikeres hívások a feladatok listázásához|Mennyiség|Összes|A feladatok listázására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. listJobs. hiba|Sikertelen hívások a feladatok listázásához|Mennyiség|Összes|A feladatokat listázó sikertelen hívások száma.|Nincsenek méretek|
|feladatok. cancelJob. sikeres|Sikeres feladatok törlése|Mennyiség|Összes|A feladat megszakítására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. cancelJob. hiba|Sikertelen feladatok megszakítása|Mennyiség|Összes|A feladat megszakítására irányuló sikertelen hívások száma.|Nincsenek méretek|
|feladatok. queryJobs. sikeres|Sikeres feladatok lekérdezése|Mennyiség|Összes|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek méretek|
|feladatok. queryJobs. hiba|Sikertelen feladatok lekérdezése|Mennyiség|Összes|A lekérdező feladatok összes sikertelen hívásának száma.|Nincsenek méretek|
|feladatok. kész|Befejezett feladatok|Mennyiség|Összes|Az összes befejezett feladat száma.|Nincsenek méretek|
|feladatok. sikertelen|Sikertelen feladatok|Mennyiség|Összes|Az összes sikertelen feladat száma.|Nincsenek méretek|
|D2C. telemetria. beáramló. sendThrottle|Szabályozási hibák száma|Mennyiség|Összes|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincsenek méretek|
|dailyMessageQuotaUsed|A felhasznált üzenetek teljes száma|Mennyiség|Átlag|A jelenleg használt üzenetek teljes száma. Ez egy kumulatív érték, amely minden nap 00:00-kor, a nulla időpontra visszaáll.|Nincsenek méretek|
|deviceDataUsage|Az eszköz összes adatfelhasználása|Bájt|Összes|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Mennyiség|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Mennyiség|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|konfigurációk|Konfigurációs mérőszámok|Mennyiség|Összes|A konfigurációs műveletek metrikái|Nincsenek méretek|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Mennyiség|Összes|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, állapot|
|DeviceAssignments|Hozzárendelt eszközök|Mennyiség|Összes|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Igazolási kísérletek|Mennyiség|Összes|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, állapot, protokoll|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AvailableStorage|Rendelkezésre álló tár|Bájt|Összes|5 perces részletességgel jelentett teljes rendelkezésre álló tárterület|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|Cassandra-kapcsolatok bezárása|Mennyiség|Összes|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|Régió, ClosureReason|
|CassandraRequestCharges|Cassandra-kérelmek díjai|Mennyiség|Összes|A Cassandra-kérelmekhez felhasznált RUs|DatabaseName, CollectionName, régió, OperationType, ResourceType|
|CassandraRequests|Cassandra-kérelmek|Mennyiség|Mennyiség|A végrehajtott Cassandra-kérelmek száma|DatabaseName, CollectionName, régió, OperationType, ResourceType, ErrorCode|
|DataUsage|Adathasználat|Bájt|Összes|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DocumentCount|Dokumentumok száma|Mennyiség|Összes|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|Dokumentum kvótája|Bájt|Összes|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|IndexUsage|Indexelés használata|Bájt|Összes|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|MetadataRequests|Metaadat-kérelmek|Mennyiség|Mennyiség|A metaadat-kérelmek száma. Cosmos DB az egyes fiókokhoz rendszermetaadat-gyűjteményt tart fenn, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, |
|MongoRequestCharge|Mongo-kérelem díja|Mennyiség|Összes|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequests|Mongo kérelmek|Mennyiség|Mennyiség|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|ProvisionedThroughput|Kiosztott átviteli kapacitás|Mennyiség|Maximum|Kiosztott átviteli kapacitás|DatabaseName, CollectionName|
|ReplicationLatency|P99 replikáció késése|Ezredmásodpercben|Átlag|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion, TargetRegion|
|ServiceAvailability|Szolgáltatás rendelkezésre állása|Százalék|Átlag|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|Nincsenek méretek|
|TotalRequestUnits|Kérelmek összes egysége|Mennyiség|Összes|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType|
|TotalRequests|Összes kérelem|Mennyiség|Mennyiség|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Mennyiség|Összes|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishFailCount|Sikertelen események közzététele|Mennyiség|Összes|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Mennyiség|Összes|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Mennyiség|Összes|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Egyeztetett események|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő összes esemény|Nincsenek méretek|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Mennyiség|Összes|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Mennyiség|Összes|Az esemény-előfizetésbe küldött összes esemény|Nincsenek méretek|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincsenek méretek|
|DroppedEventCount|Eldobott események|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Mennyiség|Összes|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishFailCount|Sikertelen események|Mennyiség|Összes|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Mennyiség|Összes|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Mennyiség|Összes|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|

## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Mennyiség|Összes|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName |
|Kiszolgálóhibái|Kiszolgálói hibák.|Mennyiség|Összes|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName |
|UserErrors|Felhasználói hibák.|Mennyiség|Összes|A Microsoft. EventHub felhasználói hibái.|EntityName |
|QuotaExceededErrors|A kvóta túllépte a hibákat.|Mennyiség|Összes|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName |
|ThrottledRequests|Szabályozott kérelmek.|Mennyiség|Összes|A Microsoft. EventHub által szabályozott kérelmek.|EntityName |
|IncomingRequests|Bejövő kérelmek|Mennyiség|Összes|A Microsoft. EventHub bejövő kérései.|entityName|
|IncomingMessages|Bejövő üzenetek|Mennyiség|Összes|A Microsoft. EventHub bejövő üzenetei.|entityName|
|OutgoingMessages|Kimenő üzenetek|Mennyiség|Összes|A Microsoft. EventHub kimenő üzenetei.|entityName|
|IncomingBytes|Bejövő bájtok.|Bájt|Összes|A Microsoft. EventHub bejövő bájtjai.|entityName|
|OutgoingBytes|Kimenő bájtok.|Bájt|Összes|A Microsoft. EventHub kimenő bájtjai.|entityName|
|Aktív kapcsolatai|Aktív kapcsolatai|Mennyiség|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|Nincsenek méretek|
|ConnectionsOpened|Megnyitott kapcsolatok.|Mennyiség|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|entityName|
|ConnectionsClosed|A kapcsolatok lezárva.|Mennyiség|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|entityName|
|CaptureBacklog|Várakozó fájlok rögzítése.|Mennyiség|Összes|A Microsoft. EventHub várakozó fájlok rögzítése.|entityName|
|CapturedMessages|Rögzített üzenetek.|Mennyiség|Összes|A Microsoft. EventHub rögzített üzenetei.|entityName|
|CapturedBytes|Rögzített bájtok száma.|Bájt|Összes|A Microsoft. EventHub rögzített bájtok száma.|entityName|
|Méret|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|entityName|
|INREQS|Bejövő kérelmek (elavult)|Mennyiség|Összes|Egy névtér összes bejövő küldési kérelme (elavult)|Nincsenek méretek|
|SUCCREQ|Sikeres kérések (elavult)|Mennyiség|Összes|A névtér összes sikeres kérelme (elavult)|Nincsenek méretek|
|FAILREQ|Sikertelen kérelmek (elavult)|Mennyiség|Összes|Névtér összes sikertelen kérelme (elavult)|Nincsenek méretek|
|SVRBSY|Kiszolgáló által foglalt hibák (elavult)|Mennyiség|Összes|Egy névtér összes kiszolgáló által foglalt hibája (elavult)|Nincsenek méretek|
|INTERer|Belső kiszolgálói hibák (elavult)|Mennyiség|Összes|Névtér összes belső kiszolgálóhiba (elavult)|Nincsenek méretek|
|MISCERR|Egyéb hibák (elavult)|Mennyiség|Összes|Névtér összes sikertelen kérelme (elavult)|Nincsenek méretek|
|INMSGS|Bejövő üzenetek (elavult) (elavult)|Mennyiség|Összes|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)|Nincsenek méretek|
|EHINMSGS|Bejövő üzenetek (elavult)|Mennyiség|Összes|Névtér összes bejövő üzenete (elavult)|Nincsenek méretek|
|OUTMSGS|Kimenő üzenetek (elavult) (elavult)|Mennyiség|Összes|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)|Nincsenek méretek|
|EHOUTMSGS|Kimenő üzenetek (elavult)|Mennyiség|Összes|Névtér összes kimenő üzenete (elavult)|Nincsenek méretek|
|EHINMBS|Bejövő bájtok (elavult) (elavult)|Bájt|Összes|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)|Nincsenek méretek|
|EHINBYTES|Bejövő bájtok (elavult)|Bájt|Összes|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincsenek méretek|
|EHOUTMBS|Kimenő bájtok (elavult) (elavult)|Bájt|Összes|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)|Nincsenek méretek|
|EHOUTBYTES|Kimenő bájtok (elavult)|Bájt|Összes|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincsenek méretek|
|EHABL|Archivált várakozó üzenetek (elavult)|Mennyiség|Összes|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)|Nincsenek méretek|
|EHAMSGS|Archivált üzenetek (elavult)|Mennyiség|Összes|Event hub archivált üzenetek egy névtérben (elavult)|Nincsenek méretek|
|EHAMBS|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összes|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)|Nincsenek méretek|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előzetes verzió)|Mennyiség|Összes|A Microsoft. EventHub sikeres kérelmeinek száma. (Előzetes verzió)|Nincsenek méretek|
|Kiszolgálóhibái|Kiszolgálói hibák. (Előzetes verzió)|Mennyiség|Összes|Kiszolgálói hibák a Microsoft. EventHub esetében. (Előzetes verzió)|Nincsenek méretek|
|UserErrors|Felhasználói hibák. (Előzetes verzió)|Mennyiség|Összes|A Microsoft. EventHub felhasználói hibái. (Előzetes verzió)|Nincsenek méretek|
|QuotaExceededErrors|A kvóta túllépte a hibákat. (Előzetes verzió)|Mennyiség|Összes|A kvóta túllépte a Microsoft. EventHub hibáit. (Előzetes verzió)|Nincsenek méretek|
|ThrottledRequests|Szabályozott kérelmek. (Előzetes verzió)|Mennyiség|Összes|A Microsoft. EventHub által szabályozott kérelmek. (Előzetes verzió)|Nincsenek méretek|
|IncomingRequests|Bejövő kérések (előzetes verzió)|Mennyiség|Összes|A Microsoft. EventHub bejövő kérései. (Előzetes verzió)|Nincsenek méretek|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Mennyiség|Összes|A Microsoft. EventHub bejövő üzenetei. (Előzetes verzió)|Nincsenek méretek|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Mennyiség|Összes|A Microsoft. EventHub kimenő üzenetei. (Előzetes verzió)|Nincsenek méretek|
|IncomingBytes|Bejövő bájtok. (Előzetes verzió)|Bájt|Összes|A Microsoft. EventHub bejövő bájtjai. (Előzetes verzió)|Nincsenek méretek|
|OutgoingBytes|Kimenő bájtok. (Előzetes verzió)|Bájt|Összes|A Microsoft. EventHub kimenő bájtjai. (Előzetes verzió)|Nincsenek méretek|
|Aktív kapcsolatai|Aktív kapcsolatai (előzetes verzió)|Mennyiség|Átlag|A Microsoft. EventHub összes aktív kapcsolata. (Előzetes verzió)|Nincsenek méretek|
|ConnectionsOpened|Megnyitott kapcsolatok. (Előzetes verzió)|Mennyiség|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma. (Előzetes verzió)|Nincsenek méretek|
|ConnectionsClosed|A kapcsolatok lezárva. (Előzetes verzió)|Mennyiség|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma. (Előzetes verzió)|Nincsenek méretek|
|CaptureBacklog|Várakozó fájlok rögzítése. (Előzetes verzió)|Mennyiség|Összes|A Microsoft. EventHub várakozó fájlok rögzítése. (Előzetes verzió)|Nincsenek méretek|
|CapturedMessages|Rögzített üzenetek. (Előzetes verzió)|Mennyiség|Összes|A Microsoft. EventHub rögzített üzenetei. (Előzetes verzió)|Nincsenek méretek|
|CapturedBytes|Rögzített bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft. EventHub rögzített bájtok száma. (Előzetes verzió)|Nincsenek méretek|
|CPU|CPU (előzetes verzió)|Százalék|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Szerepkör|
|AvailableMemory|Rendelkezésre álló memória (előzetes verzió)|Mennyiség|Maximum|Az Event hub-fürt számára rendelkezésre álló memória bájtban megadva|Szerepkör|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjáróra vonatkozó kérelmek|Mennyiség|Összes|Az átjáróra vonatkozó kérelmek száma|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategorizált átjáró-kérelmek|Mennyiség|Összes|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Aktív feldolgozók száma|Mennyiség|Maximum|Aktív feldolgozók száma|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/AutoscaleSettings

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrika értéke|Mennyiség|Átlag|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|MetricThreshold|Metrika küszöbértéke|Mennyiség|Átlag|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Mennyiség|Átlag|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.|Nincsenek méretek|
|ScaleActionsInitiated|Elindított skálázási műveletek|Mennyiség|Összes|A skálázási művelet iránya.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

(Nyilvános előzetes verzió)

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Elérhetőség|Százalék|Átlag|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|Rendelkezésre állási tesztek|Mennyiség|Mennyiség|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Oldal betöltési hálózati kapcsolati ideje|Ezredmásodpercben|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|Nincsenek méretek|
|browserTimings/processingDuration|Ügyfél feldolgozási ideje|Ezredmásodpercben|Átlag|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|Nincsenek méretek|
|browserTimings/receiveDuration|Válaszidő fogadása|Ezredmásodpercben|Átlag|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|Nincsenek méretek|
|browserTimings/sendDuration|Kérelem küldési ideje|Ezredmásodpercben|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|Nincsenek méretek|
|browserTimings/totalDuration|Böngésző oldalának betöltési ideje|Ezredmásodpercben|Átlag|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|Nincsenek méretek|
|függőségek/darabszám|Függőségi hívások|Mennyiség|Mennyiség|Az alkalmazás által külső erőforrások felé indított hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/időtartam|Függőség időtartama|Ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|függőség/típus, függőség/performanceBucket, függőség/sikeres, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/sikertelen|Függőségi hívások hibái|Mennyiség|Mennyiség|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|függőség/típus, függőség/performanceBucket, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|Oldalmegtekintések/darabszám|Lapok nézetei|Mennyiség|Mennyiség|Lapok nézeteinek száma.|művelet/szintetikus|
|Oldalmegtekintések/időtartam|Oldal nézet betöltési ideje|Ezredmásodpercben|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus|
|performanceCounters/requestExecutionTime|HTTP-kérelem végrehajtási ideje|Ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|felhő/roleInstance|
|performanceCounters/requestsInQueue|HTTP-kérelmek az alkalmazás-várólistában|Mennyiség|Átlag|Az alkalmazás-kérelmek várólistájának hossza|felhő/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-kérelmek gyakorisága|CountPerSecond|Átlag|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|felhő/roleInstance|
|performanceCounters/exceptionsPerSecond|Kivételek aránya|CountPerSecond|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|felhő/roleInstance|
|performanceCounters/processIOBytesPerSecond|Folyamat i/o-sebessége|BytesPerSecond|Átlag|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|felhő/roleInstance|
|performanceCounters/processCpuPercentage|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|felhő/roleInstance|
|performanceCounters/processorCpuPercentage|Processzoridő|Százalék|Átlag|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|felhő/roleInstance|
|performanceCounters/memoryAvailableBytes|Rendelkezésre álló memória|Bájt|Átlag|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|felhő/roleInstance|
|performanceCounters/processPrivateBytes|Saját bájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|felhő/roleInstance|
|kérelmek/időtartam|Kiszolgáló válaszideje|Ezredmásodpercben|Átlag|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/darabszám|Kiszolgálói kérelmek|Mennyiség|Mennyiség|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|Sikertelen kérelmek|Mennyiség|Mennyiség|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések > = 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Átlag|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kivételek/darabszám|Kivételek|Mennyiség|Mennyiség|Az összes nem kezelt kivétel összesített száma.|felhő/roleName, felhő/roleInstance, ügyfél/típus|
|kivételek/böngésző|Böngészőkivételek|Mennyiség|Mennyiség|A böngészőben fellépő nem kezelt kivételek száma.|Nincsenek méretek|
|kivételek/kiszolgáló|Kiszolgálói kivételek|Mennyiség|Mennyiség|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|felhő/roleName, felhő/roleInstance|
|nyomkövetés/darabszám|Nyomok|Mennyiség|Mennyiség|Nyomkövetési dokumentumok száma|nyomkövetés/severityLevel, művelet/szintetikus, felhő/roleName, felhő/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Összes szolgáltatási API-találat|Mennyiség|Mennyiség|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Általános szolgáltatás API-késése|Ezredmásodpercben|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Összes szolgáltatás API-eredményei|Mennyiség|Mennyiség|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CacheUtilization|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|None|
|QueryDuration|Lekérdezés időtartama|Ezredmásodpercben|Átlag|Lekérdezések időtartama másodpercben|Lekérdezés állapota|
|IngestionUtilization|Betöltés kihasználtsága|Százalék|Átlag|A használatban lévő betöltési tárolóhelyek aránya a fürtben|None|
|KeepAlive|Életben tartása|Mennyiség|Átlag|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|None|
|IngestionVolumeInMB|Betöltési mennyiség (MB)|Mennyiség|Összes|A betöltött adatmennyiség teljes mennyisége a fürtön (MB)|Adatbázis|
|IngestionLatencyInSeconds|Betöltési késleltetés (másodperc)|másodperc|Átlag|A lenyelési idő a forrástól (például az üzenet a EventHub) a fürthöz másodpercek alatt|None|
|EventProcessedForEventHubs|Feldolgozott események (Event Hubs)|Mennyiség|Összes|Az Event hub-ból való betöltéskor a fürt által feldolgozott események száma|None|
|IngestionResult|Betöltés eredménye|Mennyiség|Mennyiség|Betöltési műveletek száma|Állapot|
|CPU|CPU|Százalék|Átlag|CPU-kihasználtsági szint|None|
| ContinuousExportNumOfRecordsExported | A folyamatos exportálás során exportált rekordok száma | Mennyiség | Összes | Az exportálási művelet során írt összes tárolási összetevőhöz exportált rekordok száma  | None |
| ExportUtilization | Exportálás kihasználtsága | Százalék | Maximum | Exportálás kihasználtsága | None |
| ContinuousExportPendingCount | Folyamatos exportálás függőben lévő darabszáma | Mennyiség | Maximum | A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma | None |
| ContinuousExportMaxLatenessMinutes | Folyamatos exportálás maximális késői percben | Mennyiség | Maximum | Az összes függőben lévő és a végrehajtásra kész összes folyamatos exportálás percben megadott maximális ideje | None |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft. LocationBasedServices/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Mennyiség|Mennyiség|API-hívások száma|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Mennyiség|Összes|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Befejezett futtatások|Mennyiség|Összes|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Sikeres futtatások|Mennyiség|Összes|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Sikertelen futtatások|Mennyiség|Összes|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCancelled|Megszakított futtatások|Mennyiség|Összes|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunLatency|Késés futtatása|másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunSuccessLatency|Sikeres Futtatás késése|másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Szabályozott események futtatása|Mennyiség|Összes|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|RunFailurePercentage|Sikertelen futtatások százalékos aránya|Százalék|Összes|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|ActionsStarted|Elindított műveletek |Mennyiség|Összes|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsCompleted|Befejezett műveletek |Mennyiség|Összes|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Sikeres műveletek |Mennyiség|Összes|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Sikertelen műveletek|Mennyiség|Összes|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Kihagyott műveletek |Mennyiség|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionLatency|Művelet késése |másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionSuccessLatency|Művelet sikerességi késése |másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Művelet által szabályozott események|Mennyiség|Összes|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|TriggersStarted|Elindított eseményindítók |Mennyiség|Összes|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersCompleted|Befejezett eseményindítók |Mennyiség|Összes|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Sikeres eseményindítók |Mennyiség|Összes|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Sikertelen eseményindítók |Mennyiség|Összes|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Kihagyott eseményindítók|Mennyiség|Összes|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Kilőtt eseményindítók |Mennyiség|Összes|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerLatency|Trigger késése |másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerFireLatency|Kiváltó tűz késése |másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Kiváltott események indítása|Mennyiség|Összes|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|
|BillableActionExecutions|Számlázható műveletek végrehajtása|Mennyiség|Összes|A számlázott munkafolyamat-műveletek végrehajtásának száma.|Nincsenek méretek|
|BillableTriggerExecutions|Számlázható trigger-végrehajtások|Mennyiség|Összes|A számlázott munkafolyamat-trigger-végrehajtások száma.|Nincsenek méretek|
|TotalBillableExecutions|Számlázandó végrehajtások összesen|Mennyiség|Összes|A számlázandó munkafolyamat-végrehajtások száma.|Nincsenek méretek|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Mennyiség|Összes|A számlázott natív művelet-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Mennyiség|Összes|A számlázott szabványos összekötő-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Mennyiség|Összes|A számlázható tárterület-használat végrehajtásának száma.|Nincsenek méretek|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Mennyiség|Összes|A számlázott natív művelet-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Mennyiség|Összes|A számlázott szabványos összekötő-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Mennyiség|Összes|A számlázható tárterület-használat végrehajtásának száma.|Nincsenek méretek|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Mennyiség|Összes|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Befejezett futtatások|Mennyiség|Összes|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Sikeres futtatások|Mennyiség|Összes|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Sikertelen futtatások|Mennyiség|Összes|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCancelled|Megszakított futtatások|Mennyiség|Összes|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunLatency|Késés futtatása|másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunSuccessLatency|Sikeres Futtatás késése|másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Szabályozott események futtatása|Mennyiség|Összes|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|RunStartThrottledEvents|Indítási leszabályozású események futtatása|Mennyiség|Összes|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunFailurePercentage|Sikertelen futtatások százalékos aránya|Százalék|Összes|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|ActionsStarted|Elindított műveletek |Mennyiség|Összes|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsCompleted|Befejezett műveletek |Mennyiség|Összes|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Sikeres műveletek |Mennyiség|Összes|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Sikertelen műveletek |Mennyiség|Összes|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Kihagyott műveletek |Mennyiség|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionLatency|Művelet késése |másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionSuccessLatency|Művelet sikerességi késése |másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Művelet által szabályozott események|Mennyiség|Összes|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|TriggersStarted|Elindított eseményindítók |Mennyiség|Összes|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersCompleted|Befejezett eseményindítók |Mennyiség|Összes|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Sikeres eseményindítók |Mennyiség|Összes|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Sikertelen eseményindítók |Mennyiség|Összes|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Kihagyott eseményindítók|Mennyiség|Összes|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Kilőtt eseményindítók |Mennyiség|Összes|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerLatency|Trigger késése |másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerFireLatency|Kiváltó tűz késése |másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Kiváltott események indítása|Mennyiség|Összes|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorProcessorUsage|integrációs szolgáltatási környezet összekötő-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Összekötő memóriahasználat integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|Nincsenek méretek|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Befejezett futtatások|Befejezett futtatások|Mennyiség|Összes|A munkaterülethez sikeresen befejezett futtatások száma|Alkalmazási helyzet|
|Elindított futtatások|Elindított futtatások|Mennyiség|Összes|A munkaterülethez elindított futtatások száma|Alkalmazási helyzet|
|Sikertelen futtatások|Sikertelen futtatások|Mennyiség|Összes|Sikertelen futtatások száma ehhez a munkaterülethez|Alkalmazási helyzet|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Mennyiség|Mennyiség|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|
|Elérhetőség|Elérhetőség|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/kötetek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageOtherLatency|Egyéb késések átlaga|MS/op|Átlag|Átlagos egyéb késés (nem olvasható vagy írható) ezredmásodpercben, művelet|Nincsenek méretek|
|AverageReadLatency|Olvasási késleltetés átlagos késése|MS/op|Átlag|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|Nincsenek méretek|
|AverageTotalLatency|Teljes késés átlaga|MS/op|Átlag|Átlagos teljes késés (ezredmásodpercben)/művelet|Nincsenek méretek|
|AverageWriteLatency|Írási késleltetés átlagos késése|MS/op|Átlag|Írási késleltetés átlagos száma ezredmásodpercben|Nincsenek méretek|
|FilesystemOtherOps|Fájlrendszer – egyéb Ops|Ops|Átlag|Fájlrendszer egyéb műveleteinek száma (nem olvasható vagy írható)|Nincsenek méretek|
|FilesystemReadOps|Fájlrendszer olvasási Ops|Ops|Átlag|A fájlrendszer olvasási műveleteinek száma|Nincsenek méretek|
|FilesystemTotalOps|Fájlrendszer összes Ops|Ops|Átlag|Az összes fájlrendszer-művelet összege|Nincsenek méretek|
|FilesystemWriteOps|Fájlrendszer írási Ops|Ops|Átlag|A fájlrendszer írási műveleteinek száma|Nincsenek méretek|
|IoBytesPerOtherOps|IO bájt/egyéb műveleti azonosító|bájt/op|Átlag|/Kimenő bájtok száma/egyéb műveletek (nem olvasható vagy írható)|Nincsenek méretek|
|IoBytesPerReadOps|IO bájt/olvasási Ops|bájt/op|Átlag|Bejövő/kimenő bájtok másodpercenkénti száma olvasási művelet esetén|Nincsenek méretek|
|IoBytesPerTotalOps|IO-bájt/op az összes műveletnél|bájt/op|Átlag|Az összes/kimenő bájtok műveletének összege|Nincsenek méretek|
|IoBytesPerWriteOps|IO bájt/írási műveleti egység|bájt/op|Átlag|Bejövő/kimenő bájtok száma írási művelettel|Nincsenek méretek|
|OtherIops|Egyéb IOPS|művelet/másodperc|Átlag|Egyéb/kimenő műveletek másodpercenként|Nincsenek méretek|
|OtherThroughput|Egyéb átviteli sebesség|MBps|Átlag|Egyéb átviteli sebesség (amely nem írható vagy írható) megabájt/másodpercben|Nincsenek méretek|
|ReadIops|IOPS olvasása|művelet/másodperc|Átlag|Olvasási/kimenő műveletek másodpercenként|Nincsenek méretek|
|ReadThroughput|Olvasási sebesség|MBps|Átlag|Olvasási sebesség (MB/s) másodpercenként|Nincsenek méretek|
|TotalIops|Összes IOPS|művelet/másodperc|Átlag|Az összes/kimenő művelet másodpercenkénti összege|Nincsenek méretek|
|TotalThroughput|Teljes átviteli sebesség|MBps|Átlag|Az összes átviteli sebesség (MB/s) összege másodpercenként|Nincsenek méretek|
|VolumeAllocatedSize|Kötet lefoglalt mérete|bájt|Átlag|A kötet lefoglalt mérete (nem a tényleges felhasznált bájtok)|Nincsenek méretek|
|VolumeLogicalSize|Kötet logikai mérete|bájt|Átlag|A kötet logikai mérete (felhasznált bájtok)|Nincsenek méretek|
|VolumeSnapshotSize|Kötet pillanatképének mérete|bájt|Átlag|A köteten található összes pillanatkép mérete|Nincsenek méretek|
|WriteIops|IOPS írása|művelet/másodperc|Átlag|Írási/kimenő műveletek másodpercenként|Nincsenek méretek|
|WriteThroughput|Írási sebesség|MBps|Átlag|Írási sebesség megabájtban (MB/s)|Nincsenek méretek|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Kötet-készlet lefoglalt mérete|bájt|Átlag|A készlet lefoglalt mérete (nem a tényleges felhasznált bájtok)|Nincsenek méretek|
|VolumePoolAllocatedUsed|Lefoglalt kötet-készlet|bájt|Átlag|A készlet lefoglalt felhasznált mérete|Nincsenek méretek|
|VolumePoolTotalLogicalSize|Kötet készletének teljes logikai mérete|bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|Nincsenek méretek|
|VolumePoolTotalSnapshotSize|Kötet készletének teljes pillanatképének mérete|bájt|Átlag|A készlet összes pillanatképének összege|Nincsenek méretek|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Eljuttatott bájtok|Mennyiség|Összes|A hálózati adapter által eljuttatott bájtok száma|Nincsenek méretek|
|BytesReceivedRate|Fogadott bájtok száma|Mennyiség|Összes|A hálózati adapter által fogadott bájtok száma|Nincsenek méretek|
|PacketsSentRate|Küldött csomagok|Mennyiség|Összes|A hálózati adapter által küldött csomagok száma|Nincsenek méretek|
|PacketsReceivedRate|Fogadott csomagok|Mennyiség|Összes|A hálózati adapter által fogadott csomagok száma|Nincsenek méretek|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|Adatelérési út rendelkezésre állása|Mennyiség|Átlag|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress, FrontendPort|
|DipAvailability|Állapot mintavételi állapota|Mennyiség|Átlag|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Bájtok száma|Mennyiség|Összes|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, irány|
|PacketCount|Csomagok száma|Mennyiség|Összes|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SYNCount|SYN-szám|Mennyiség|Összes|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SnatConnectionCount|SNAT-kapcsolatok száma|Mennyiség|Összes|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Lefoglalt SNAT portok (előzetes verzió)|Mennyiség|Összes|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Használt SNAT-portok (előzetes verzió)|Mennyiség|Összes|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezési kötet|Mennyiség|Összes|A DNS-zónák számára kiszolgált lekérdezések száma|Nincsenek méretek|
|RecordSetCount|Rekordok készletének száma|Mennyiség|Maximum|Rekordhalmazok száma egy DNS-zónában|Nincsenek méretek|
|RecordSetCapacityUtilization|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|Nincsenek méretek|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|Nincsenek méretek|
|PacketsDroppedDDoS|Bejövő csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő csomagok eldobott DDoS|Nincsenek méretek|
|PacketsForwardedDDoS|Bejövő csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő csomagok továbbított DDoS|Nincsenek méretek|
|TCPPacketsInDDoS|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|Nincsenek méretek|
|TCPPacketsDroppedDDoS|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok eldobott DDoS|Nincsenek méretek|
|TCPPacketsForwardedDDoS|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok továbbított DDoS|Nincsenek méretek|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok DDoS|Nincsenek méretek|
|UDPPacketsDroppedDDoS|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok eldobott DDoS|Nincsenek méretek|
|UDPPacketsForwardedDDoS|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok továbbított DDoS|Nincsenek méretek|
|BytesInDDoS|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|Nincsenek méretek|
|BytesDroppedDDoS|Bejövő bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő bájtok elvetve DDoS|Nincsenek méretek|
|BytesForwardedDDoS|Bejövő bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő bájtok továbbított DDoS|Nincsenek méretek|
|TCPBytesInDDoS|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|Nincsenek méretek|
|TCPBytesDroppedDDoS|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|Nincsenek méretek|
|TCPBytesForwardedDDoS|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|Nincsenek méretek|
|UDPBytesInDDoS|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|Nincsenek méretek|
|UDPBytesDroppedDDoS|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|Nincsenek méretek|
|UDPBytesForwardedDDoS|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|Nincsenek méretek|
|IfUnderDDoSAttack|DDoS-támadás alatt vagy nem|Mennyiség|Maximum|DDoS-támadás alatt vagy nem|Nincsenek méretek|
|DDoSTriggerTCPPackets|Bejövő TCP-csomagok a DDoS-mérséklés indításához|CountPerSecond|Maximum|Bejövő TCP-csomagok a DDoS-mérséklés indításához|Nincsenek méretek|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|Nincsenek méretek|
|DDoSTriggerSYNPackets|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|Nincsenek méretek|
|VipAvailability|Adatelérési út rendelkezésre állása|Mennyiség|Átlag|Átlagos IP-cím rendelkezésre állása időszakonként|Port|
|ByteCount|Bájtok száma|Mennyiség|Összes|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|PacketCount|Csomagok száma|Mennyiség|Összes|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|SynCount|SYN-szám|Mennyiség|Összes|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ApplicationRuleHit|Alkalmazás-szabályok találatok száma|Mennyiség|Összes|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|NetworkRuleHit|Hálózati szabályok találatok száma|Mennyiség|Összes|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|
|DataProcessed|Feldolgozott adatok|Bájt|Összes|A tűzfalon áthaladó adatmennyiség|Nincsenek méretek|
|FirewallHealthState|Tűzfal állapota|Százalék|Átlag|A tűzfal állapotát jelzi.|Állapot, ok|
|SNATPortUtilization|SNAT-portok kihasználtsága|Százalék|Átlag|A tűzfal által használt SNAT-portok százalékos aránya|Nincsenek méretek|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Adatforgalom|Adatforgalom|BytesPerSecond|Összes|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|Nincsenek méretek|
|UnhealthyHostCount|Nem kifogástalan állapotú gazdagépek száma|Mennyiség|Átlag|Sérült háttérbeli gazdagépek száma|BackendSettingsPool|
|HealthyHostCount|Kifogástalan állapotú gazdagépek száma|Mennyiség|Átlag|Az egészséges háttérbeli gazdagépek száma|BackendSettingsPool|
|TotalRequests|Összes kérelem|Mennyiség|Összes|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|FailedRequests|Sikertelen kérelmek|Mennyiség|Összes|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|ResponseStatus|Válasz állapota|Mennyiség|Összes|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Összege|Aktuális kapcsolatok|Mennyiség|Összes|Application Gatewaysal létesített aktuális kapcsolatok száma|Nincsenek méretek|
|CapacityUnits|Aktuális kapacitási egységek|Mennyiség|Átlag|Felhasznált kapacitási egységek|Nincsenek méretek|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageBandwidth|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SBandwidth|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SConnectionCount|P2S-kapcsolatok száma|Mennyiség|Maximum|Átjáró pont – hely kapcsolatának száma|Protocol (Protokoll)|
|TunnelAverageBandwidth|Bújtatási sávszélesség|BytesPerSecond|Átlag|Egy alagút átlagos sávszélessége (bájt/s)|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Alagút kimenő bájtjai|Bájt|Összes|Egy alagút kimenő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Alagutak bejövő bájtjai|Bájt|Összes|Alagút bejövő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagjai|Mennyiség|Összes|Alagút kimenő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Bújtatási bejövő csomagok|Mennyiség|Összes|Alagút bejövő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Alagút kimenő terminálszolgáltatási eltérési csomagjainak eldobása|Mennyiség|Összes|Kimenő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Az alagút bejövő HOZZÁFÉRÉSe nem egyezik a csomagok eldobásával|Mennyiség|Összes|Bejövő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/társak

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|Nincsenek méretek|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|Nincsenek méretek|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Visszaadott végponti lekérdezések|Mennyiség|Összes|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végponti állapot végpont szerint|Mennyiség|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ProbesFailedPercent|%-Os mintavétel sikertelen|Százalék|Átlag|a kapcsolat figyelési mintavételének%-a meghiúsult|Nincsenek méretek|
|AverageRoundtripMs|Átlagos menetidő (MS)|Ezredmásodpercben|Átlag|A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS)|Nincsenek méretek|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|requestCount|Kérelmek száma|Mennyiség|Összes|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Kérelem mérete|Bájt|Összes|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Válasz mérete|Bájt|Összes|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Háttérbeli kérelmek száma|Mennyiség|Összes|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Háttérbeli kérelmek késése|Ezredmásodpercben|Átlag|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Backend|
|TotalLatency|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Háttér állapotának százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|WebApplicationFirewallRequestCount|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Mennyiség|Összes|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|regisztráció. All|Regisztrációs műveletek|Mennyiség|Összes|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |Nincsenek méretek|
|regisztráció. Create|Regisztráció-létrehozási műveletek|Mennyiség|Összes|Az összes sikeres regisztrációs létrehozás száma.|Nincsenek méretek|
|regisztráció. frissítés|Regisztrációs frissítési műveletek|Mennyiség|Összes|A sikeres regisztrációs frissítések száma.|Nincsenek méretek|
|regisztráció. Get|Regisztrálási olvasási műveletek|Mennyiség|Összes|A sikeres regisztrációs lekérdezések száma.|Nincsenek méretek|
|regisztráció. Delete|Regisztrációs törlési műveletek|Mennyiség|Összes|A regisztráció sikeres törléseinak száma.|Nincsenek méretek|
|bejövő|Bejövő üzenetek|Mennyiség|Összes|A sikeres küldési API-hívások száma. |Nincsenek méretek|
|bejövő. ütemezett|Ütemezett leküldéses értesítések elküldve|Mennyiség|Összes|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|bejövő. ütemezett. Mégse|Ütemezett leküldéses értesítések megszakítva|Mennyiség|Összes|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|ütemezett. függőben|Függőben lévő ütemezett értesítések|Mennyiség|Összes|Függőben lévő ütemezett értesítések|Nincsenek méretek|
|a telepítés. All|Telepítési felügyeleti műveletek|Mennyiség|Összes|Telepítési felügyeleti műveletek|Nincsenek méretek|
|telepítés. Get|Telepítési műveletek beolvasása|Mennyiség|Összes|Telepítési műveletek beolvasása|Nincsenek méretek|
|telepítési. upsert|Telepítési műveletek létrehozása vagy frissítése|Mennyiség|Összes|Telepítési műveletek létrehozása vagy frissítése|Nincsenek méretek|
|Installation. patch|Javítások telepítési műveletei|Mennyiség|Összes|Javítások telepítési műveletei|Nincsenek méretek|
|telepítés. Törlés|Telepítési műveletek törlése|Mennyiség|Összes|Telepítési műveletek törlése|Nincsenek méretek|
|kimenő. allpns. sikeres|Sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. allpns. invalidpayload|Hasznos adatok|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|Nincsenek méretek|
|kimenő. allpns. pnserror|Külső értesítési rendszerhibák|Mennyiség|Összes|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|Nincsenek méretek|
|kimenő. allpns. channelerror|Csatorna hibái|Mennyiség|Összes|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|Nincsenek méretek|
|kimenő. allpns. badorexpiredchannel|Rossz vagy lejárt csatorna-hibák|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|Nincsenek méretek|
|kimenő. wns. sikeres|WNS sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. wns. invalidcredentials|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|Nincsenek méretek|
|kimenő. wns. badchannel|WNS hibás csatorna hiba|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. wns. expiredchannel|WNS lejárt csatorna hibája|Mennyiség|Összes|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapota: 410 elveszett).|Nincsenek méretek|
|kimenő. wns. szabályozva|WNS-szabályozású értesítések|Mennyiség|Összes|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapota: 406, nem fogadható el).|Nincsenek méretek|
|kimenő. wns. tokenproviderunreachable|WNS-hitelesítési hibák (nem érhető el)|Mennyiség|Összes|A Windows Live nem érhető el.|Nincsenek méretek|
|kimenő. wns. invalidtoken|WNS-hitelesítési hibák (érvénytelen token)|Mennyiség|Összes|A WNS számára megadott jogkivonat érvénytelen (WNS-állapot: 401 jogosulatlan).|Nincsenek méretek|
|kimenő. wns. wrongtoken|WNS-hitelesítési hibák (hibás token)|Mennyiség|Összes|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|Nincsenek méretek|
|kimenő. wns. invalidnotificationformat|WNS – érvénytelen értesítési formátum|Mennyiség|Összes|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|Nincsenek méretek|
|kimenő. wns. invalidnotificationsize|A WNS érvénytelen értesítési méretet észlelt|Mennyiség|Összes|Az értesítési tartalom túl nagy (WNS állapot: 413).|Nincsenek méretek|
|kimenő. wns. channelthrottled|WNS csatorna szabályozása|Mennyiség|Összes|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-NotificationStatus: channelThrottled).|Nincsenek méretek|
|kimenő. wns. channeldisconnected|WNS csatorna leválasztva|Mennyiség|Összes|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. Dropped|WNS eldobott értesítések|Mennyiség|Összes|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. pnserror|WNS hibák|Mennyiség|Összes|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|Nincsenek méretek|
|kimenő. wns. authenticationerror|WNS-hitelesítési hibák|Mennyiség|Összes|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|Nincsenek méretek|
|kimenő. apns. sikeres|APNS sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. apns. invalidcredentials|APNS-hitelesítési hibák|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. apns. badchannel|APNS hibás csatorna hiba|Mennyiség|Összes|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS bináris protokoll állapotkód: 8. APNS HTTP protokoll állapotkód: 400 "BadDeviceToken").|Nincsenek méretek|
|kimenő. apns. expiredchannel|APNS lejárt csatorna hibája|Mennyiség|Összes|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|Nincsenek méretek|
|kimenő. apns. invalidnotificationsize|A APNS érvénytelen értesítési méretet észlelt|Mennyiség|Összes|Azon leküldések száma, amelyek sikertelenek voltak, mert túl nagy a hasznos adat (APNS bináris protokoll állapotkód: 7).|Nincsenek méretek|
|kimenő. apns. pnserror|APNS hibák|Mennyiség|Összes|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. GCM. sikeres|GCM sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. invalidcredentials|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. GCM. badchannel|GCM hibás csatorna hiba|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM-eredmény: érvénytelen regisztráció).|Nincsenek méretek|
|kimenő. GCM. expiredchannel|GCM lejárt csatorna hibája|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM-eredmény: NotRegistered).|Nincsenek méretek|
|kimenő. GCM. szabályozva|GCM-szabályozású értesítések|Mennyiség|Összes|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM: 501-599 vagy eredmény: nem érhető el).|Nincsenek méretek|
|kimenő. GCM. invalidnotificationformat|GCM – érvénytelen értesítési formátum|Mennyiség|Összes|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|Nincsenek méretek|
|kimenő. GCM. invalidnotificationsize|A GCM érvénytelen értesítési méretet észlelt|Mennyiség|Összes|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|Nincsenek méretek|
|kimenő. GCM. wrongchannel|GCM rossz csatorna hibája|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM-eredmény: InvalidPackageName).|Nincsenek méretek|
|kimenő. GCM. pnserror|GCM hibák|Mennyiség|Összes|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. GCM. authenticationerror|GCM-hitelesítési hibák|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM result: MismatchedSenderId).|Nincsenek méretek|
|kimenő. mpns. sikeres|MPNS sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. mpns. invalidcredentials|MPNS – érvénytelen hitelesítő adatok|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. badchannel|MPNS hibás csatorna hiba|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. mpns. szabályozva|MPNS-szabályozású értesítések|Mennyiség|Összes|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem fogadható el).|Nincsenek méretek|
|kimenő. mpns. invalidnotificationformat|MPNS – érvénytelen értesítési formátum|Mennyiség|Összes|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|Nincsenek méretek|
|kimenő. mpns. channeldisconnected|MPNS csatorna leválasztva|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (MPNS-állapot: 412 nem található).|Nincsenek méretek|
|kimenő. mpns. Dropped|MPNS eldobott értesítések|Mennyiség|Összes|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X-NotificationStatus: QueueFull vagy letiltva).|Nincsenek méretek|
|kimenő. mpns. pnserror|MPNS hibák|Mennyiség|Összes|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. mpns. authenticationerror|MPNS-hitelesítési hibák|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|notificationhub. leküldések|Minden kimenő értesítés|Mennyiség|Összes|Az értesítési központ összes kimenő értesítése|Nincsenek méretek|
|bejövő. ALL. requests|Minden bejövő kérelem|Mennyiség|Összes|Értesítési központ összes bejövő kérelme|Nincsenek méretek|
|bejövő. ALL. failedrequests|Minden bejövő sikertelen kérelem|Mennyiség|Összes|Értesítési központ bejövő sikertelen kéréseinek száma|Nincsenek méretek|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_ szabad inode (%)|Szabad inode%-ban|Mennyiség|Átlag|Average_ szabad inode (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Szabad terület (%)|Mennyiség|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ használt inode|Felhasznált inode%-ban|Mennyiség|Átlag|Average_ használt inode|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált terület%-ban|Felhasznált terület (%)|Mennyiség|Átlag|Average_ felhasznált terület%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási sebesség (bájt/s) Average_Disk|Lemez olvasási sebessége (bájt/s)|Mennyiség|Átlag|Olvasási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Olvasási sebesség (lemez/mp)|Mennyiség|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Lemez átvitele másodpercenként|Mennyiség|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írási sebesség (bájt/s) Average_Disk|Lemez írási sebessége (bájt/s)|Mennyiség|Átlag|Írási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Írási sebesség (írás/mp)|Mennyiség|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Szabad terület (MB)|Mennyiség|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Logikai lemez sebessége (bájt/s)|Mennyiség|Átlag|Average_Logical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rendelkezésre álló memória Average_%-ban|Rendelkezésre álló memória%-ban|Mennyiség|Átlag|Rendelkezésre álló memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ rendelkezésre álló szabad hely a lapozófájlban|Rendelkezésre álló swap-terület (%)|Mennyiség|Átlag|Average_ rendelkezésre álló szabad hely a lapozófájlban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Felhasznált memória Average_%-ban|Felhasznált memória (%)|Mennyiség|Átlag|Felhasznált memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált lapozófájl-terület|Felhasznált swap-terület%-ban|Mennyiség|Átlag|Average_ felhasznált lapozófájl-terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB memória|Rendelkezésre álló memória (MB)|Mennyiség|Átlag|Average_Available MB memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban való swap|Rendelkezésre álló memória (MB)|Mennyiség|Átlag|Average_Available MB-ban való swap|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Page másodpercenként|Olvasott lap/mp|Mennyiség|Átlag|Olvasási Average_Page másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Page|Írási idő/mp|Mennyiség|Átlag|Írás/mp Average_Page|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pages előállított/másodperc|Lap/mp|Mennyiség|Átlag|Average_Pages előállított/másodperc|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used MB-ban felcserélt terület|Felhasznált memória (MB) – lapozófájl|Mennyiség|Átlag|Average_Used MB-ban felcserélt terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória MB-ban|Felhasznált memória (MB)|Mennyiség|Átlag|Average_Used memória MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított bájtok|Továbbított bájtok összesen|Mennyiség|Átlag|Average_Total továbbított bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Total bájtok száma|Fogadott bájtok összesen|Mennyiség|Átlag|Fogadott Average_Total bájtok száma|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total bájtok|Bájtok összesen|Mennyiség|Átlag|Average_Total bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított csomagok|Továbbított csomagok összesen|Mennyiség|Átlag|Average_Total továbbított csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total fogadott csomagok|Fogadott csomagok összesen|Mennyiség|Átlag|Average_Total fogadott csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rx-hibák Average_Total|Rx-hibák összesen|Mennyiség|Átlag|Rx-hibák Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total TX-hibák|TX-hibák összesen|Mennyiség|Átlag|Average_Total TX-hibák|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Ütközések Average_Total|Ütközések összesen|Mennyiség|Átlag|Ütközések Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Írási idő (mp/olvasás)|Átlagos írási idő (mp/olvasás)|Mennyiség|Átlag|Average_Avg. Írási idő (mp/olvasás)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Lemez (mp/átvitel)|Átlagos műveleti idő (mp/átvitel)|Mennyiség|Átlag|Average_Avg. Lemez (mp/átvitel)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Írási idő (mp/írás)|Átlagos írási idő (mp/írás)|Mennyiség|Átlag|Average_Avg. Írási idő (mp/írás)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Fizikai lemez sebessége (bájt/s)|Mennyiség|Átlag|Average_Physical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|PCT rendszerjogosultságú idő|Mennyiség|Átlag|Average_Pct emelt szintű idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|PCT felhasználói idő|Mennyiség|Átlag|Average_Pct felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Felhasznált memória (kilobájt)|Mennyiség|Átlag|Average_Used memória kilobájtban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Virtuális megosztott memória|Mennyiség|Átlag|Average_Virtual megosztott memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% DPC idő|% DPC idő|Mennyiség|Átlag|Average_% DPC idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ üresjárati idő%-ban|Üresjárati idő%-ban|Mennyiség|Átlag|Average_ üresjárati idő%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Megszakítási idő%-ban|Mennyiség|Átlag|Average_%-os megszakítási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% IO várakozási idő|I/o várakozási idő%-ban|Mennyiség|Átlag|Average_% IO várakozási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os szép idő|% Nice idő|Mennyiség|Átlag|Average_%-os szép idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|%-Os privilegizált idő|Mennyiség|Átlag|Average_%-os privilegizált idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|Processzoridő (%)|Mennyiség|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Felhasználói idő%-ban|Mennyiség|Átlag|Average_%-os felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fizikai memória Average_Free|Szabad fizikai memória|Mennyiség|Átlag|Fizikai memória Average_Free|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free lemezterület a Lapozófájlokban|Szabad terület a Lapozófájlokban|Mennyiség|Átlag|Average_Free lemezterület a Lapozófájlokban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free virtuális memória|Szabad virtuális memória|Mennyiség|Átlag|Average_Free virtuális memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processes|Folyamatok|Mennyiség|Átlag|Average_Processes|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Lapozófájlokban tárolt méret|Mennyiség|Átlag|Lapozófájlokban tárolt Average_Size|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Uptime|Üzemidő|Mennyiség|Átlag|Average_Uptime|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Users|Felhasználók|Mennyiség|Átlag|Average_Users|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Írási idő (mp/olvasás)|Átlagos írási idő (mp/olvasás)|Mennyiség|Átlag|Average_Avg. Írási idő (mp/olvasás)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Írási idő (mp/írás)|Átlagos írási idő (mp/írás)|Mennyiség|Átlag|Average_Avg. Írási idő (mp/írás)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Current a lemez várólistájának hossza|Lemez aktuális várólistájának hossza|Mennyiség|Átlag|Average_Current a lemez várólistájának hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Olvasási sebesség (lemez/mp)|Mennyiség|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Lemez átvitele másodpercenként|Mennyiség|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Írási sebesség (írás/mp)|Mennyiség|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Szabad terület (MB)|Mennyiség|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Szabad terület (%)|Mennyiség|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban|Rendelkezésre álló memória (MB)|Mennyiség|Átlag|Average_Available MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Előjegyzett memória%-ban használatban|Mennyiség|Átlag|Average_%-os előjegyzett bájtok használatban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Fogadott bájtok/s|Mennyiség|Átlag|Fogadott Average_Bytes/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Küldési Average_Bytes másodpercenként|Küldési sebesség (bájt/s)|Mennyiség|Átlag|Küldési Average_Bytes másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Összes bájt/mp|Mennyiség|Átlag|Average_Bytes összesen/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|Processzoridő (%)|Mennyiség|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Processzor-várólista hossza|Mennyiség|Átlag|Average_Processor várólista hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Szívverés|Szívverés|Mennyiség|Összes|Szívverés|Számítógép, OSType, verzió, SourceComputerId|
|Frissítés|Frissítés|Mennyiség|Átlag|Frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|
|Esemény|Esemény|Mennyiség|Átlag|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Lekérdezés időtartama|Ezredmásodpercben|Átlag|DAX-lekérdezés időtartama az utolsó intervallumban|Nincsenek méretek|
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet feladatok várólistájának hossza|Mennyiség|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|Nincsenek méretek|
|qpu_high_utilization_metric|QPU magas kihasználtsága|Mennyiség|Összes|QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0|Nincsenek méretek|
|memory_metric|Memória|Bájt|Átlag|Memória. 0-3 GB-os tartomány a1, 0-5 GB az a2-hez, 0-10 GB az a3-hoz, 0-25 GB a4-hez, 0-50 GB az A5-hez és az A6-os 0-100 GB-hoz|Nincsenek méretek|
|memory_thrashing_metric|Memória-Kiverés|Százalék|Átlag|Memória átlagos kiverése.|Nincsenek méretek|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Figyelőkapcsolatokra – sikeres|Figyelőkapcsolatokra – sikeres|Mennyiség|Összes|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|entityName|
|Figyelőkapcsolatokra – vonatkozó ügyfélhibái|Figyelőkapcsolatokra – vonatkozó ügyfélhibái|Mennyiség|Összes|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|entityName|
|Figyelőkapcsolatokra – ServerError|Figyelőkapcsolatokra – ServerError|Mennyiség|Összes|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|entityName|
|Feladói kapcsolatokra – sikeres|Feladói kapcsolatokra – sikeres|Mennyiség|Összes|A Microsoft. Relay sikeres feladói kapcsolatokra.|entityName|
|Feladói kapcsolatokra – vonatkozó ügyfélhibái|Feladói kapcsolatokra – vonatkozó ügyfélhibái|Mennyiség|Összes|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|entityName|
|Feladói kapcsolatokra – ServerError|Feladói kapcsolatokra – ServerError|Mennyiség|Összes|A Microsoft. Relay feladói kapcsolatokra ServerError.|entityName|
|Figyelőkapcsolatokra – TotalRequests|Figyelőkapcsolatokra – TotalRequests|Mennyiség|Összes|A Microsoft. Relay Figyelőkapcsolatokra összesen.|entityName|
|Feladói kapcsolatokra – TotalRequests|Feladói kapcsolatokra – TotalRequests|Mennyiség|Összes|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|entityName|
|Aktív kapcsolatai|Aktív kapcsolatai|Mennyiség|Összes|A Microsoft. Relay aktív kapcsolatai összesen.|entityName|
|ActiveListeners|ActiveListeners|Mennyiség|Összes|A Microsoft. Relay ActiveListeners összesen.|entityName|
|BytesTransferred|BytesTransferred|Mennyiség|Összes|A Microsoft. Relay BytesTransferred összesen.|entityName|
|Bontásai|Bontásai|Mennyiség|Összes|A Microsoft. Relay bontásai összesen.|entityName|
|SenderDisconnects|SenderDisconnects|Mennyiség|Összes|A Microsoft. Relay SenderDisconnects összesen.|entityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|Nincsenek méretek|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|CountPerSecond|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|Nincsenek méretek|
|ThrottledSearchQueriesPercentage|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|Nincsenek méretek|

## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előzetes verzió)|Mennyiség|Összes|A névtér összes sikeres kérelme (előzetes verzió)|entityName|
|Kiszolgálóhibái|Kiszolgálói hibák. (Előzetes verzió)|Mennyiség|Összes|Kiszolgálói hibák a Microsoft. ServiceBus esetében. (Előzetes verzió)|entityName|
|UserErrors|Felhasználói hibák. (Előzetes verzió)|Mennyiség|Összes|A Microsoft. ServiceBus felhasználói hibái. (Előzetes verzió)|entityName|
|ThrottledRequests|Szabályozott kérelmek. (Előzetes verzió)|Mennyiség|Összes|A Microsoft. ServiceBus által szabályozott kérelmek. (Előzetes verzió)|entityName|
|IncomingRequests|Bejövő kérések (előzetes verzió)|Mennyiség|Összes|A Microsoft. ServiceBus bejövő kérései. (Előzetes verzió)|entityName|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Mennyiség|Összes|A Microsoft. ServiceBus bejövő üzenetei. (Előzetes verzió)|entityName|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Mennyiség|Összes|A Microsoft. ServiceBus kimenő üzenetei. (Előzetes verzió)|entityName|
|Aktív kapcsolatai|Aktív kapcsolatai (előzetes verzió)|Mennyiség|Összes|A Microsoft. ServiceBus összes aktív kapcsolata. (Előzetes verzió)|Nincsenek méretek|
|Méret|Méret (előzetes verzió)|Bájt|Átlag|Várólista/témakör mérete bájtban. (Előzetes verzió)|entityName|
|Üzenetek|Üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|Mennyiség|Átlag|Üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|entityName|
|ActiveMessages|Üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|Mennyiség|Átlag|Üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|entityName|
|DeadletteredMessages|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. (Előzetes verzió)|Mennyiség|Átlag|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. (Előzetes verzió)|entityName|
|ScheduledMessages|Az üzenetsor/témakör ütemezett üzeneteinek száma. (Előzetes verzió)|Mennyiség|Átlag|Az üzenetsor/témakör ütemezett üzeneteinek száma. (Előzetes verzió)|entityName|
|CPUXNS|CPU-használat/névtér|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája|Nincsenek méretek|
|WSXNS|Memória méretének használata névtérben|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma|Nincsenek méretek|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/alkalmazások

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Mennyiség|Átlag|A tárolóhoz a millicores-ben lefoglalt processzor|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bájt|Átlag|A tároló számára lefoglalt memória (MB)|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Mennyiség|Átlag|Tényleges CPU-használat a millicores-ben|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Mennyiség|Átlag|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|ServiceStatus|ServiceStatus|Mennyiség|Átlag|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, állapot, szolgáltatásnév|
|ServiceReplicaStatus|ServiceReplicaStatus|Mennyiség|Átlag|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, status, szolgáltatásnév, ServiceReplicaName|
|Tároló állapota:|Tároló állapota:|Mennyiség|Átlag|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName, állapot|
|RestartCount|RestartCount|Mennyiség|Átlag|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, status, szolgáltatásnév, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ConnectionCount|Kapcsolatok száma|Mennyiség|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|MessageCount|Üzenetek száma|Mennyiség|Összes|Az üzenetek teljes mennyisége.|Nincsenek méretek|
|InboundTraffic|Bejövő forgalom|Bájt|Összes|A szolgáltatás bejövő forgalma|Nincsenek méretek|
|OutboundTraffic|Kimenő forgalom|Bájt|Összes|A szolgáltatás kimenő forgalma|Nincsenek méretek|
|UserErrors|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|Nincsenek méretek|
|SystemErrors|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|Nincsenek méretek|

## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|app_cpu_billed|Az alkalmazás CPU-számlázása|Mennyiség|Összes|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_cpu_percent|Alkalmazás CPU-aránya|Százalék|Átlag|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_memory_percent|Alkalmazás memóriájában használt százalék|Százalék|Átlag|Az alkalmazás memóriájában használt százalék. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|blocked_by_firewall|Tűzfal blokkolja|Mennyiség|Összes|Tűzfal blokkolja|Nincsenek méretek|
|cache_hit_percent|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|cache_used_percent|Gyorsítótár használt százaléka|Százalék|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|connection_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|Nincsenek méretek|
|connection_successful|Sikeres kapcsolatok|Mennyiség|Összes|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_limit|CPU-korlát|Mennyiség|Átlag|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|cpu_used|Felhasznált CPU|Mennyiség|Átlag|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|holtpont|Holtpontok|Mennyiség|Összes|Holtpontok. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|diff_backup_size_bytes|Különbözeti biztonsági másolatok tárolási mérete|Bájt|Maximum|Kumulatív különbözeti biztonsági másolati tárterület mérete. Általános célú és üzletileg kritikus adatbázisokra vonatkozik. Jelenleg nem vonatkozik a példány-adatbázisok kezelésére.|Nincsenek méretek|
|dtu_limit|DTU korlátja|Mennyiség|Átlag|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_used|Használt DTU|Mennyiség|Átlag|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dw_cpu_percent|A DW-csomópont szintjének CPU-aránya|Százalék|Átlag|A DW-csomópont szintjének CPU-aránya|DwLogicalNodeId|
|dw_physical_data_read_percent|A DW-csomópont szintű adat IO-aránya|Százalék|Átlag|A DW-csomópont szintű adat IO-aránya|DwLogicalNodeId|
|dwu_consumption_percent|DWU százalékos aránya|Százalék|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_limit|DWU korlátja|Mennyiség|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_used|Használt DWU|Mennyiség|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|full_backup_size_bytes|Teljes biztonsági mentési tár mérete|Bájt|Maximum|Összesített teljes biztonsági mentési tár mérete. Általános célú és üzletileg kritikus adatbázisokra vonatkozik. Jelenleg nem vonatkozik a példány-adatbázisok kezelésére.|Nincsenek méretek|
|local_tempdb_usage_percent|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|log_backup_size_bytes|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő napló biztonsági mentési tárterületének mérete. Általános célú és üzletileg kritikus adatbázisokra vonatkozik. Jelenleg nem vonatkozik a példány-adatbázisok kezelésére.|Nincsenek méretek|
|log_write_percent|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|sqlserver_process_core_percent|SQL Server Process Core százalék|Százalék|Maximum|Ez a metrika helyőrző, és nem töltődik fel jelenleg.|Nincsenek méretek|
|sqlserver_process_memory_percent|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Ez a metrika helyőrző, és nem töltődik fel jelenleg.|Nincsenek méretek|
|tárterület|Felhasznált adatterület|Bájt|Maximum|Az adatbázis teljes mérete. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage_percent|Felhasznált adatterület százalékos aránya|Százalék|Maximum|Az adatbázis mérete százalékban megadva. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|tempdb_data_size|Tempdb adatfájl mérete (kilobájt)|Mennyiség|Maximum|Tempdb az adatfájl mérete kilobájtban. Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincsenek méretek|
|tempdb_log_size|Tempdb-naplófájl mérete (kilobájt)|Mennyiség|Maximum|Tempdb-naplófájl mérete (kilobájt) Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincsenek méretek|
|tempdb_log_used_percent|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van. Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincsenek méretek|
|workers_percent|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP-tárolási százalék. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/kiszolgálók/elasticPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|Nincsenek méretek|
|allocated_data_storage_percent|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|Nincsenek méretek|
|cpu_limit|CPU-korlát|Mennyiség|Átlag|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Felhasznált CPU|Mennyiség|Átlag|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_limit|eDTU korlátja|Mennyiség|Átlag|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_used|használt eDTU|Mennyiség|Átlag|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|log_write_percent|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|Nincsenek méretek|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincsenek méretek|
|storage_limit|Az adatmaximális méret|Bájt|Átlag|Tárolási korlát|Nincsenek méretek|
|storage_percent|Felhasznált adatterület százalékos aránya||Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|
|sqlserver_process_core_percent|SQL Server Process Core százalék|Százalék|Maximum|Ez a metrika helyőrző, és nem töltődik fel jelenleg.|Nincsenek méretek|
|sqlserver_process_memory_percent|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Ez a metrika helyőrző, és nem töltődik fel jelenleg.|Nincsenek méretek|
|tempdb_data_size|Tempdb adatfájl mérete (kilobájt)|Mennyiség|Maximum|Tempdb az adatfájl mérete kilobájtban. Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincsenek méretek|
|tempdb_log_size|Tempdb-naplófájl mérete (kilobájt)|Mennyiség|Maximum|Tempdb-naplófájl mérete (kilobájt) Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincsenek méretek|
|tempdb_log_used_percent|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van. Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincsenek méretek|
|workers_percent|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|Nincsenek méretek|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP tárolási százaléka|Nincsenek méretek|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|avg_cpu_percent|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|Nincsenek méretek|
|io_bytes_read|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|Nincsenek méretek|
|io_requests|IO-kérelmek száma|Mennyiség|Átlag|IO-kérelmek száma|Nincsenek méretek|
|io_bytes_written|I/o-bájtok írása|Bájt|Átlag|I/o-bájtok írása|Nincsenek méretek|
|reserved_storage_mb|Tárterület fenntartva|Mennyiség|Átlag|Tárterület fenntartva|Nincsenek méretek|
|storage_space_used_mb|Felhasznált tárterület|Mennyiség|Átlag|Felhasznált tárterület|Nincsenek méretek|
|virtual_core_count|Virtuális mag száma|Mennyiség|Átlag|Virtuális mag száma|Nincsenek méretek|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Felhasznált kapacitás|Bájt|Átlag|Fiókhoz használt kapacitás|Nincsenek méretek|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Blobok száma|Mennyiség|Összes|A Storage-fiók Blob service található Blobok száma.|BlobType|       |BlobCount|Blobok száma|Mennyiség|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|BLOB-tárolók száma|Mennyiség|Átlag|A Storage-fiók Blob service lévő tárolók száma.|Nincsenek méretek|
|IndexCapacity|Index kapacitása|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|Nincsenek méretek|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Nincsenek méretek|
|FileCount|Fájlok száma|Mennyiség|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Nincsenek méretek|
|FileShareCount|Fájlmegosztás száma|Mennyiség|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|Nincsenek méretek|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|QueueCount|Várólista száma|Mennyiség|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|Nincsenek méretek|
|QueueMessageCount|Üzenetsor-üzenetek száma|Mennyiség|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|Nincsenek méretek|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|TableCount|Táblák száma|Mennyiség|Átlag|A Storage-fiók Table service található tábla száma.|Nincsenek méretek|
|TableEntityCount|Tábla entitások száma|Mennyiség|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|Nincsenek méretek|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Szinkronizálási munkamenet eredménye|Mennyiség|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Szinkronizált fájlok|Mennyiség|Összes|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Mennyiség|Összes|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összes|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Kiszolgáló online állapota|Mennyiség|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a regisztrált kiszolgáló sikeresen rögzített egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Felhőbeli rétegek felidézése|Bájt|Összes|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU% kihasználtsága|Százalék|Maximum|SU% kihasználtsága|LogicalName, PartitionId|
|InputEvents|Bemeneti események|Mennyiség|Összes|Bemeneti események|LogicalName, PartitionId|
|InputEventBytes|Bemeneti esemény bájtjai|Bájt|Összes|Bemeneti esemény bájtjai|LogicalName, PartitionId|
|LateInputEvents|Késői bemeneti események|Mennyiség|Összes|Késői bemeneti események|LogicalName, PartitionId|
|OutputEvents|Kimeneti események|Mennyiség|Összes|Kimeneti események|LogicalName, PartitionId|
|ConversionErrors|Adatátalakítási hibák|Mennyiség|Összes|Adatátalakítási hibák|LogicalName, PartitionId|
|Hibák|Futásidejű hibák|Mennyiség|Összes|Futásidejű hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Megrendelési események|Mennyiség|Összes|Megrendelési események|LogicalName, PartitionId|
|AMLCalloutRequests|Függvények kérései|Mennyiség|Összes|Függvények kérései|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Sikertelen függvények kérései|Mennyiség|Összes|Sikertelen függvények kérései|LogicalName, PartitionId|
|AMLCalloutInputEvents|Függvények eseményei|Mennyiség|Összes|Függvények eseményei|LogicalName, PartitionId|
|DeserializationError|Bemeneti deszerializálási hibák|Mennyiség|Összes|Bemeneti deszerializálási hibák|LogicalName, PartitionId|
|EarlyInputEvents|Korai bemeneti események|Mennyiség|Összes|Korai bemeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Vízjel késleltetése|másodperc|Maximum|Vízjel késleltetése|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Várakozó bemeneti eseményei|Mennyiség|Maximum|Várakozó bemeneti eseményei|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Fogadott bemeneti források|Mennyiség|Összes|Fogadott bemeneti források|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Mennyiség|Összes|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Mennyiség|Összes|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összes|Az összes eseményforrás által olvasott bájtok száma|Nincsenek méretek|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összes|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Bejövő tárolt események|Mennyiség|Összes|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Mennyiség|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenet sorszáma közötti különbség|Nincsenek méretek|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Mennyiség|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Mennyiség|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Mennyiség|Összes|Az eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Mennyiség|Összes|Az eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összes|Az eseményforrás által beolvasott bájtok száma|Nincsenek méretek|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összes|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Bejövő tárolt események|Mennyiség|Összes|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Mennyiség|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenet sorszáma közötti különbség|Nincsenek méretek|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Mennyiség|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Mennyiség|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|DiskWriteBytesPerSecond|Lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|Nincsenek méretek|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összes|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összes|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|Nincsenek méretek|
|DiskReadOperations|Lemezes olvasási műveletek|Mennyiség|Összes|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskWriteOperations|Lemez írási műveletei|Mennyiség|Összes|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskReadLatency|Lemez olvasási késése|Ezredmásodpercben|Átlag|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|Nincsenek méretek|
|DiskWriteLatency|Lemez írási késése|Ezredmásodpercben|Átlag|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|Nincsenek méretek|
|NetworkInBytesPerSecond|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkOutBytesPerSecond|Hálózati kimenő bájtok/s|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|A fogadott forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|A továbbított forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|MemoryUsed|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt gépi memória mennyisége.|Nincsenek méretek|
|MemoryGranted|Megadott memória|Bájt|Átlag|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|Nincsenek méretek|
|MemoryActive|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|Nincsenek méretek|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|Nincsenek méretek|
|PercentageCpuReady|CPU-készültség százalékos aránya|Ezredmásodpercben|Összes|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|Nincsenek méretek|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvezérlő-várólista hossza|Mennyiség|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Http-várólista hossza|Mennyiség|Átlag|Http-várólista hossza|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összes|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összes|Kimenő adatvesztés|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (a függvények kivételével)

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összes|CPU-idő|Példány|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összes|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összes|Kimenő adatvesztés|Példány|
|Http101|Http 101|Mennyiség|Összes|Http 101|Példány|
|Http2xx|Http-2xx|Mennyiség|Összes|Http-2xx|Példány|
|Http3xx|Http-3xx|Mennyiség|Összes|Http-3xx|Példány|
|Http401|HTTP 401|Mennyiség|Összes|HTTP 401|Példány|
|Http403|Http 403|Mennyiség|Összes|Http 403|Példány|
|Http404|Http 404|Mennyiség|Összes|Http 404|Példány|
|Http406|Http 406|Mennyiség|Összes|Http 406|Példány|
|Http4xx|Http-4xx|Mennyiség|Összes|Http-4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Mennyiség|Összes|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|AppConnections|Connections (Kapcsolatok)|Mennyiség|Átlag|Connections (Kapcsolatok)|Példány|
|Kezeli|Kezelők száma|Mennyiség|Átlag|Kezelők száma|Példány|
|Szálak|Szálak száma|Mennyiség|Átlag|Szálak száma|Példány|
|PrivateBytes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|IoReadBytesPerSecond|IO olvasási bájtok másodpercenként|BytesPerSecond|Összes|IO olvasási bájtok másodpercenként|Példány|
|IoWriteBytesPerSecond|IO írási bájtok másodpercenként|BytesPerSecond|Összes|IO írási bájtok másodpercenként|Példány|
|IoOtherBytesPerSecond|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összes|IO – egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összes|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|IO írási műveletek másodpercenként|BytesPerSecond|Összes|IO írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|IO egyéb műveletek másodpercenként|BytesPerSecond|Összes|IO egyéb műveletek másodpercenként|Példány|
|RequestsInApplicationQueue|Kérelmek az alkalmazás-várólistán|Mennyiség|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|CurrentAssemblies|Aktuális szerelvények|Mennyiség|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains|Alkalmazás összes tartománya|Mennyiség|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Összes kitöltött alkalmazás-tartomány|Mennyiség|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|
|Gen0Collections|0\. generációs Garbage-gyűjtemények|Mennyiség|Összes|0\. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1\. generációs Garbage gyűjtemények|Mennyiség|Összes|1\. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2\. generációs Garbage gyűjtemények|Mennyiség|Összes|2\. generációs Garbage gyűjtemények|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (functions)

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összes|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összes|Kimenő adatvesztés|Példány|
|Http5xx|Http-kiszolgálói hibák|Mennyiség|Összes|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|FunctionExecutionUnits|Függvények végrehajtási egységei|MB/ezredmásodperc|Összes|[Függvények végrehajtási egységei](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|FunctionExecutionCount|Függvény végrehajtásának száma|Mennyiség|Összes|Függvény végrehajtásának száma|Példány|
|PrivateBytes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|IoReadBytesPerSecond|IO olvasási bájtok másodpercenként|BytesPerSecond|Összes|IO olvasási bájtok másodpercenként|Példány|
|IoWriteBytesPerSecond|IO írási bájtok másodpercenként|BytesPerSecond|Összes|IO írási bájtok másodpercenként|Példány|
|IoOtherBytesPerSecond|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összes|IO – egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összes|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|IO írási műveletek másodpercenként|BytesPerSecond|Összes|IO írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|IO egyéb műveletek másodpercenként|BytesPerSecond|Összes|IO egyéb műveletek másodpercenként|Példány|
|RequestsInApplicationQueue|Kérelmek az alkalmazás-várólistán|Mennyiség|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|CurrentAssemblies|Aktuális szerelvények|Mennyiség|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains|Alkalmazás összes tartománya|Mennyiség|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Összes kitöltött alkalmazás-tartomány|Mennyiség|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|
|Gen0Collections|0\. generációs Garbage-gyűjtemények|Mennyiség|Összes|0\. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1\. generációs Garbage gyűjtemények|Mennyiség|Összes|1\. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2\. generációs Garbage gyűjtemények|Mennyiség|Összes|2\. generációs Garbage gyűjtemények|Példány|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összes|CPU-idő|Példány|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összes|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összes|Kimenő adatvesztés|Példány|
|Http101|Http 101|Mennyiség|Összes|Http 101|Példány|
|Http2xx|Http-2xx|Mennyiség|Összes|Http-2xx|Példány|
|Http3xx|Http-3xx|Mennyiség|Összes|Http-3xx|Példány|
|Http401|HTTP 401|Mennyiség|Összes|HTTP 401|Példány|
|Http403|Http 403|Mennyiség|Összes|Http 403|Példány|
|Http404|Http 404|Mennyiség|Összes|Http 404|Példány|
|Http406|Http 406|Mennyiség|Összes|Http 406|Példány|
|Http4xx|Http-4xx|Mennyiség|Összes|Http-4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Mennyiség|Összes|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|FunctionExecutionUnits|Függvények végrehajtási egységei|Mennyiség|Összes|Függvények végrehajtási egységei|Példány|
|FunctionExecutionCount|Függvény végrehajtásának száma|Mennyiség|Összes|Függvény végrehajtásának száma|Példány|
|AppConnections|Connections (Kapcsolatok)|Mennyiség|Átlag|Connections (Kapcsolatok)|Példány|
|Kezeli|Kezelők száma|Mennyiség|Átlag|Kezelők száma|Példány|
|Szálak|Szálak száma|Mennyiség|Átlag|Szálak száma|Példány|
|PrivateBytes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|IoReadBytesPerSecond|IO olvasási bájtok másodpercenként|BytesPerSecond|Összes|IO olvasási bájtok másodpercenként|Példány|
|IoWriteBytesPerSecond|IO írási bájtok másodpercenként|BytesPerSecond|Összes|IO írási bájtok másodpercenként|Példány|
|IoOtherBytesPerSecond|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összes|IO – egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összes|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|IO írási műveletek másodpercenként|BytesPerSecond|Összes|IO írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|IO egyéb műveletek másodpercenként|BytesPerSecond|Összes|IO egyéb műveletek másodpercenként|Példány|
|RequestsInApplicationQueue|Kérelmek az alkalmazás-várólistán|Mennyiség|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|CurrentAssemblies|Aktuális szerelvények|Mennyiség|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains|Alkalmazás összes tartománya|Mennyiség|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Összes kitöltött alkalmazás-tartomány|Mennyiség|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|
|Gen0Collections|0\. generációs Garbage-gyűjtemények|Mennyiség|Összes|0\. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1\. generációs Garbage gyűjtemények|Mennyiség|Összes|1\. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2\. generációs Garbage gyűjtemények|Mennyiség|Összes|2\. generációs Garbage gyűjtemények|Példány|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összes|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összes|Kimenő adatvesztés|Példány|
|Http101|Http 101|Mennyiség|Összes|Http 101|Példány|
|Http2xx|Http-2xx|Mennyiség|Összes|Http-2xx|Példány|
|Http3xx|Http-3xx|Mennyiség|Összes|Http-3xx|Példány|
|Http401|HTTP 401|Mennyiség|Összes|HTTP 401|Példány|
|Http403|Http 403|Mennyiség|Összes|Http 403|Példány|
|Http404|Http 404|Mennyiség|Összes|Http 404|Példány|
|Http406|Http 406|Mennyiség|Összes|Http 406|Példány|
|Http4xx|Http-4xx|Mennyiség|Összes|Http-4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Mennyiség|Összes|Http-kiszolgálói hibák|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvezérlő-várólista hossza|Mennyiség|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Http-várólista hossza|Mennyiség|Átlag|Http-várólista hossza|Példány|
|ActiveRequests|Aktív kérések|Mennyiség|Összes|Aktív kérések|Példány|
|TotalFrontEnds|Összes előtér|Mennyiség|Átlag|Összes előtér|Nincsenek méretek|
|SmallAppServicePlanInstances|Kisméretű App Service-csomag feldolgozói|Mennyiség|Átlag|Kisméretű App Service-csomag feldolgozói|Nincsenek méretek|
|MediumAppServicePlanInstances|Közepes App Service-csomag feldolgozói|Mennyiség|Átlag|Közepes App Service-csomag feldolgozói|Nincsenek méretek|
|LargeAppServicePlanInstances|Nagy App Service-csomag feldolgozói|Mennyiség|Átlag|Nagy App Service-csomag feldolgozói|Nincsenek méretek|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Összesítés típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WorkersTotal|Alkalmazottak összesen|Mennyiség|Átlag|Alkalmazottak összesen|Nincsenek méretek|
|WorkersAvailable|Rendelkezésre álló munkavégzők|Mennyiség|Átlag|Rendelkezésre álló munkavégzők|Nincsenek méretek|
|WorkersUsed|Használt feldolgozók|Mennyiség|Átlag|Használt feldolgozók|Nincsenek méretek|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|

## <a name="next-steps"></a>Következő lépések
* [Tudnivalók a Azure Monitor mérőszámokról](data-platform.md)
* [Riasztások létrehozása mérőszámokon](alerts-overview.md)
* [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](resource-logs-overview.md)
