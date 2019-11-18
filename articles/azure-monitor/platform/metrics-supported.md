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
ms.openlocfilehash: 58c0db1f8200a757c95d7f66f9d821968e7e1839
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121122"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák Azure Monitor

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. Alább látható az Azure Monitor metrikus folyamatával jelenleg elérhető metrikák teljes listája. Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők. Az alábbi lista csak a konszolidált Azure Monitor metrikai folyamat használatával elérhető metrikákat tartalmazza. A metrikák lekérdezéséhez és eléréséhez használja a [2018-01-01 API-Version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Darabszám|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|memory_metric|Memória|Bájt|Átlag|Memória. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|private_bytes_metric|Saját bájtok |Bájt|Átlag|A memória teljes mennyisége a Analysis Services motor folyamata és az adategyesítési tároló folyamatai lefoglalva, nem beleértve a más folyamatokkal megosztott memóriát.|ServerResourceType|
|virtual_bytes_metric|Virtuális bájtok |Bájt|Átlag|A Analysis Services motor és az adategyesítési tároló folyamatai által használt virtuális címtartomány jelenlegi mérete.|ServerResourceType|
|TotalConnectionRequests|Kapcsolatkérelmek teljes száma|Darabszám|Átlag|A kapcsolatkérelmek teljes száma. Ezek az érkezések.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|CountPerSecond|Átlag|A sikeres kapcsolatok gyakorisága.|ServerResourceType|
|TotalConnectionFailures|Összes sikertelen Kapcsolatfelvétel|Darabszám|Átlag|A sikertelen csatlakozási kísérletek teljes száma.|ServerResourceType|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrejött felhasználói munkamenetek aktuális száma.|ServerResourceType|
|QueryPoolBusyThreads|Lekérdezési készlet foglalt szálai|Darabszám|Átlag|A lekérdezési szál készletében lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolJobQueueLength|Parancssori feladatok várólistájának hossza|Darabszám|Átlag|A parancsfájl-készlet várólistájában lévő feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Feldolgozási készlet nyomtatási várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|ServerResourceType|
|Összege|Kapcsolat: aktuális kapcsolatok|Darabszám|Átlag|A létesített ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CleanerCurrentPrice|Memória: tisztító – aktuális ár|Darabszám|Átlag|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: tisztító memória csökkenthető|Bájt|Átlag|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: nem csökkenthető a tisztább memória|Bájt|Átlag|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|ServerResourceType|
|MemoryUsage|Memória: memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memória által leképezett adatmennyiséggel, figyelmen kívül hagyva azokat a memóriát, amelyeket a xVelocity memórián belüli elemzési motorja (VertiPaq) leképezett vagy lefoglalt, és amely meghaladja a xVelocity-memória korlátját.|ServerResourceType|
|MemoryLimitHard|Memória: rögzített memória|Bájt|Átlag|Rögzített memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Memória: magas a memória korlátozása|Bájt|Átlag|Magas memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Memória: kevés a memória korlátja|Bájt|Átlag|Kevés a memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: VertiPaq korlátja|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|Kvóta|Memória: kvóta|Bájt|Átlag|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|ServerResourceType|
|QuotaBlocked|Memória: blokkolt kvóta|Darabszám|Átlag|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq, nem lapozható|Bájt|Átlag|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq lapozható|Bájt|Átlag|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|ServerResourceType|
|RowsReadPerSec|Feldolgozás: másodpercenként beolvasott sorok száma|CountPerSecond|Átlag|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|ServerResourceType|
|RowsConvertedPerSec|Feldolgozás: másodpercenként konvertált sorok száma|CountPerSecond|Átlag|A sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsWrittenPerSec|Feldolgozás: másodpercenként írt sorok száma|CountPerSecond|Átlag|A sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|CommandPoolBusyThreads|Szálak: a parancssori készlet foglalt szálak|Darabszám|Átlag|A parancsfájl-készletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Szálak: parancssori készlet üresjárati szálai|Darabszám|Átlag|Az üresjárati szálak száma a parancs szálának készletében.|ServerResourceType|
|LongParsingBusyThreads|Szálak: hosszú elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingIdleThreads|Szálak: tartós folyamatok elemzése – üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingJobQueueLength|Szálak: hosszú elemzési feladatok várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai|Darabszám|Átlag|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva|Darabszám|Átlag|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Szálak: feldolgozási készlet üresjáratban nem I/O-szálai|Darabszám|Átlag|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Szálak: lekérdezési készlet – üresjárati szálak|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak: rövid elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Szálak: rövid elemzési üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak: rövid elemzési feladatok várólistájának hossza|Darabszám|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memória-Kiverés|Százalék|Átlag|Memória átlagos kiverése.|ServerResourceType|
|mashup_engine_qpu_metric|M motor QPU|Darabszám|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor saját bájtjai |Bájt|Átlag|A memória-adategyesítési tároló folyamatainak teljes mennyisége kiosztott, és nem tartalmazza a más folyamatokkal megosztott memóriát.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M motor virtuális bájtjai |Bájt|Átlag|A virtuális címtartomány összemashup-tároló folyamatainak jelenlegi mérete a.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek teljes száma egy adott időszakban. A problémák diagnosztizálásához különféle méretek is feldarabolható. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Átjárók összes kérelme|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát. |Hely, állomásnév|
|SuccessfulRequests|Sikeres átjáró-kérelmek|Darabszám|Összesen|A sikeres átjáró-kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát.|Hely, állomásnév|
|UnauthorizedRequests|Nem engedélyezett átjáróra vonatkozó kérelmek|Darabszám|Összesen| Egy adott időszakban nem engedélyezett átjáró-kérelmek teljes száma. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát.|Hely, állomásnév|
|FailedRequests|Sikertelen átjáró-kérelmek|Darabszám|Összesen|A sikertelen átjáró-kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát.|Hely, állomásnév|
|OtherRequests|Egyéb átjáró-kérelmek|Darabszám|Összesen|Egy adott időszakban a sikeres, jogosulatlan vagy sikertelen kategóriákba nem tartozó átjáró-kérelmek teljes száma. Ez a metrika elavult, javasoljuk, hogy használja az új `Requests` metrikát. |Hely, állomásnév|
|Időtartam|Az átjárók kéréseinek teljes időtartama|Ezredmásodperc|Átlag|Az az idő, amikor a API Management egy ügyféltől érkező kérést kap, és amikor az ügyfél válaszát adja vissza.|Hely, állomásnév|
|Kapacitás|Kapacitás|Százalék|Átlag|Egy API Management-példány terhelésének jelzője a megalapozott döntések meghozatala érdekében, hogy a példány méretét nagyobb terhelésre kell-e méretezni.|Földrajzi egység|
|EventHubTotalEvents|Összes EventHub esemény|Darabszám|Összesen|Egy adott időszakban API Management EventHub eljuttatott események teljes száma.|Földrajzi egység|
|EventHubSuccessfulEvents|Sikeres EventHub események|Darabszám|Összesen|A sikeres EventHub események teljes száma egy adott időszakban.|Földrajzi egység|
|EventHubTotalFailedEvents|Sikertelen EventHub események|Darabszám|Összesen|A sikertelen EventHub események teljes száma egy adott időszakban.|Földrajzi egység|
|EventHubRejectedEvents|EventHub-események visszautasítva|Darabszám|Összesen|Az elutasított EventHub-események (helytelen konfiguráció vagy jogosulatlan) teljes száma egy adott időszakban.|Földrajzi egység|
|EventHubThrottledEvents|Szabályozott EventHub események|Darabszám|Összesen|Egy adott időszakban a szabályozott EventHub események teljes száma.|Földrajzi egység|
|EventHubTimedoutEvents|Időtúllépés a EventHub eseményeinél|Darabszám|Összesen|Egy adott időszakon belül a EventHub események teljes száma.|Földrajzi egység|
|EventHubDroppedEvents|Eldobott EventHub események|Darabszám|Összesen|Egy adott időszakban a várólista-méretre vonatkozó korlát miatt kihagyott események teljes száma.|Földrajzi egység|
|EventHubTotalBytesSent|EventHub-események mérete|Bájt|Összesen|A EventHub-események teljes mérete bájtban egy adott időszakban.|Földrajzi egység|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok összesen|Darabszám|Összesen|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentRuns|Összes frissítés központi telepítési futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Összes frissítés központi telepítési gép futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált mag száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált magok teljes száma|Nincs dimenzió|
|TotalNodeCount|Dedikált csomópontok száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált csomópontok teljes száma|Nincs dimenzió|
|LowPriorityCoreCount|LowPriority mag száma|Darabszám|Összesen|Az alacsony prioritású magok teljes száma a Batch-fiókban|Nincs dimenzió|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Darabszám|Összesen|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|Nincs dimenzió|
|CreatingNodeCount|Csomópontok számának létrehozása|Darabszám|Összesen|Létrehozandó csomópontok száma|Nincs dimenzió|
|StartingNodeCount|Csomópontok számának indítása|Darabszám|Összesen|Kiinduló csomópontok száma|Nincs dimenzió|
|WaitingForStartTaskNodeCount|Várakozás a feladat-csomópontok számának megkezdésére|Darabszám|Összesen|Az indítási tevékenység befejezésére váró csomópontok száma|Nincs dimenzió|
|StartTaskFailedNodeCount|Indítási feladat sikertelen csomópontok száma|Darabszám|Összesen|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|Nincs dimenzió|
|IdleNodeCount|Tétlen csomópontok száma|Darabszám|Összesen|Üresjárati csomópontok száma|Nincs dimenzió|
|OfflineNodeCount|Offline csomópontok száma|Darabszám|Összesen|Offline csomópontok száma|Nincs dimenzió|
|RebootingNodeCount|Csomópontok számának újraindítása|Darabszám|Összesen|Újraindítási csomópontok száma|Nincs dimenzió|
|ReimagingNodeCount|Csomópontok rendszerképének alaphelyzetbe állítása|Darabszám|Összesen|Rendszerkép-csomópontok száma|Nincs dimenzió|
|RunningNodeCount|Csomópontok száma|Darabszám|Összesen|Futó csomópontok száma|Nincs dimenzió|
|LeavingPoolNodeCount|A készlet-csomópontok számának elhagyása|Darabszám|Összesen|A készletet elhagyó csomópontok száma|Nincs dimenzió|
|UnusableNodeCount|Nem használható csomópontok száma|Darabszám|Összesen|Használhatatlan csomópontok száma|Nincs dimenzió|
|PreemptedNodeCount|Előzik-csomópontok száma|Darabszám|Összesen|Előzik-csomópontok száma|Nincs dimenzió|
|TaskStartEvent|Tevékenységek indítási eseményei|Darabszám|Összesen|Az elindított feladatok teljes száma|Nincs dimenzió|
|TaskCompleteEvent|Feladat teljes eseményei|Darabszám|Összesen|A Befejezett feladatok teljes száma|Nincs dimenzió|
|TaskFailEvent|Feladat sikertelen eseményei|Darabszám|Összesen|A meghiúsult állapotban Befejezett feladatok teljes száma|Nincs dimenzió|
|PoolCreateEvent|Készlet-létrehozási események|Darabszám|Összesen|A létrehozott készletek teljes száma|Nincs dimenzió|
|PoolResizeStartEvent|Készlet átméretezésének indítási eseményei|Darabszám|Összesen|A készlet elindított újraméretezésének teljes száma|Nincs dimenzió|
|PoolResizeCompleteEvent|Készlet átméretezése – befejezett események|Darabszám|Összesen|A készlet összes újraméretezésének száma|Nincs dimenzió|
|PoolDeleteStartEvent|Készlet törlése – indítási események|Darabszám|Összesen|Az elindított készlet-törlések száma összesen|Nincs dimenzió|
|PoolDeleteCompleteEvent|Készlet törlése – befejezett események|Darabszám|Összesen|A befejezett készlet-törlések teljes száma|Nincs dimenzió|
|JobDeleteCompleteEvent|Feladatok törlése – befejezett események|Darabszám|Összesen|A sikeresen törölt feladatok teljes száma.|Nincs dimenzió|
|JobDeleteStartEvent|Feladatok törlésének indítási eseményei|Darabszám|Összesen|A törölni kívánt feladatok teljes száma.|Nincs dimenzió|
|JobDisableCompleteEvent|A feladatok letiltják a teljes eseményeket|Darabszám|Összesen|A sikeresen letiltott feladatok teljes száma.|Nincs dimenzió|
|JobDisableStartEvent|Feladatok letiltásának indítási eseményei|Darabszám|Összesen|A letiltani kívánt feladatok teljes száma.|Nincs dimenzió|
|JobStartEvent|Feladatok indítási eseményei|Darabszám|Összesen|A sikeresen elindított feladatok teljes száma.|Nincs dimenzió|
|JobTerminateCompleteEvent|A feladatokhoz tartozó befejezett események befejezése|Darabszám|Összesen|A sikeresen leállított feladatok teljes száma.|Nincs dimenzió|
|JobTerminateStartEvent|Feladatokból indított események leállítása|Darabszám|Összesen|A leállítani kívánt feladatok teljes száma.|Nincs dimenzió|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakoztatott ügyfelek|Darabszám|Maximum||ShardId|
|totalcommandsprocessed|Összes művelet|Darabszám|Összesen||ShardId|
|cachehits|Gyorsítótár-találatok|Darabszám|Összesen||ShardId|
|cachemisses|Gyorsítótár-lemaradás|Darabszám|Összesen||ShardId|
|getcommands|Kap|Darabszám|Összesen||ShardId|
|setcommands|Beállítja|Darabszám|Összesen||ShardId|
|operationsPerSecond|Műveletek száma másodpercenként|Darabszám|Maximum||ShardId|
|evictedkeys|Kizárt kulcsok|Darabszám|Összesen||ShardId|
|totalkeys|Kulcsok összesen|Darabszám|Maximum||ShardId|
|expiredkeys|Lejárt kulcsok|Darabszám|Összesen||ShardId|
|usedmemory|Felhasznált memória|Bájt|Maximum||ShardId|
|usedmemorypercentage|Felhasznált memória százalékos aránya|Százalék|Maximum||ShardId|
|usedmemoryRss|Felhasznált memória RSS-címe|Bájt|Maximum||ShardId|
|serverLoad|Kiszolgáló terhelése|Százalék|Maximum||ShardId|
|cacheWrite|Gyorsítótár írása|BytesPerSecond|Maximum||ShardId|
|cacheRead|Gyorsítótár-olvasás|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Százalék|Maximum||ShardId|
|cacheLatency|Gyorsítótár késési másodpercek (előzetes verzió)|Darabszám|Átlag||ShardId, SampleType|
|Hibák|Hibák|Darabszám|Maximum||ShardId, ErrorType|
|connectedclients0|Csatlakoztatott ügyfelek (0. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed0|Összes művelet (0. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|cachehits0|Gyorsítótárbeli találatok (szegmens 0)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses0|Gyorsítótár-kihagyás (0. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|getcommands0|Beolvasás (0. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|setcommands0|Készletek (0. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond0|Művelet/másodperc (0. Szilánk)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys0|Kizárt kulcsok (0. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys0|Összes kulcs (szilánk 0)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys0|Lejárt kulcsok (szilánk 0)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory0|Felhasznált memória (0. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss0|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad0|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite0|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead0|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime0|CPU (0. szegmens)|Százalék|Maximum||Nincs dimenzió|
|connectedclients1|Csatlakoztatott ügyfelek (1. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed1|Összes művelet (1. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|cachehits1|Gyorsítótárbeli találatok (1. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses1|Gyorsítótár-lemaradás (1. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|getcommands1|Beolvasás (1. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|setcommands1|Készletek (1. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond1|Művelet/másodperc (1. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys1|Kizárt kulcsok (1. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys1|Összes kulcs (1. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys1|Lejárt kulcsok (1. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory1|Felhasznált memória (1. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss1|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad1|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite1|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead1|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime1|CPU (1. Szilánk)|Százalék|Maximum||Nincs dimenzió|
|connectedclients2|Csatlakoztatott ügyfelek (2. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed2|Összes művelet (2. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|cachehits2|Gyorsítótárbeli találatok (2. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses2|Gyorsítótár-lemaradás (2. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|getcommands2|Beolvasás (2. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|setcommands2|Készletek (2. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond2|Művelet/másodperc (2. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys2|Kizárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys2|Összes kulcs (2. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys2|Lejárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory2|Felhasznált memória (2. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss2|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad2|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite2|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead2|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime2|CPU (2. szegmens)|Százalék|Maximum||Nincs dimenzió|
|connectedclients3|Csatlakoztatott ügyfelek (3. Szilánk)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed3|Összes művelet (3. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|cachehits3|Gyorsítótárbeli találatok (3. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses3|Gyorsítótár-lemaradás (3. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|getcommands3|Beolvasás (3. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|setcommands3|Készletek (3. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond3|Művelet/másodperc (3. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys3|Kizárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys3|Összes kulcs (3. Szilánk)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys3|Lejárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory3|Felhasznált memória (3. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss3|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad3|Kiszolgáló terhelése (3. Szilánk)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite3|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead3|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime3|CPU (3. Szilánk)|Százalék|Maximum||Nincs dimenzió|
|connectedclients4|Csatlakoztatott ügyfelek (4. Szilánk)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed4|Összes művelet (4. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|cachehits4|Gyorsítótár-találatok (4. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses4|Gyorsítótár-lemaradás (4. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|getcommands4|Beolvasás (4. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|setcommands4|Készletek (4. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond4|Művelet/másodperc (4. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys4|Kizárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys4|Összes kulcs (4. Szilánk)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys4|Lejárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory4|Felhasznált memória (4. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss4|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|serverLoad4|Kiszolgáló terhelése (4. Szilánk)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite4|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead4|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime4|CPU (4. Szilánk)|Százalék|Maximum||Nincs dimenzió|
|connectedclients5|Csatlakoztatott ügyfelek (5. Szilánk)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed5|Összes művelet (5. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|cachehits5|Gyorsítótárbeli találatok (szegmens 5)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses5|Gyorsítótár-lemaradás (5. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|getcommands5|Beolvasás (szegmens 5)|Darabszám|Összesen||Nincs dimenzió|
|setcommands5|Készletek (szilánk 5)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond5|Művelet/másodperc (5. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys5|Kizárt kulcsok (5. Szilánk)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys5|Összes kulcs (5. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys5|Lejárt kulcsok (szilánk 5)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory5|Felhasznált memória (5. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss5|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad5|Kiszolgáló terhelése (5. Szilánk)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite5|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead5|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime5|CPU (5. Szilánk)|Százalék|Maximum||Nincs dimenzió|
|connectedclients6|Csatlakoztatott ügyfelek (6. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed6|Összes művelet (szegmens 6)|Darabszám|Összesen||Nincs dimenzió|
|cachehits6|Gyorsítótárbeli találatok (szilánk 6)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses6|Gyorsítótár-lemaradás (szegmens 6)|Darabszám|Összesen||Nincs dimenzió|
|getcommands6|Beolvasás (szilánk 6)|Darabszám|Összesen||Nincs dimenzió|
|setcommands6|Készletek (szilánk 6)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond6|Művelet/másodperc (szegmens 6)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys6|Kizárt kulcsok (szegmens 6)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys6|Összes kulcs (szilánk 6)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys6|Lejárt kulcsok (szilánk 6)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory6|Felhasznált memória (6. szegmens)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss6|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad6|Kiszolgáló betöltése (szegmens 6)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite6|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead6|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime6|CPU (szegmens 6)|Százalék|Maximum||Nincs dimenzió|
|connectedclients7|Csatlakoztatott ügyfelek (7. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed7|Összes művelet (7. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|cachehits7|Gyorsítótárbeli találatok (szegmens 7)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses7|Gyorsítótár-lemaradás (7. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|getcommands7|Beolvasás (szegmens 7)|Darabszám|Összesen||Nincs dimenzió|
|setcommands7|Készletek (szegmens 7)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond7|Művelet/másodperc (7. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys7|Kizárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys7|Összes kulcs (szegmens 7)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys7|Lejárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory7|Felhasznált memória (7. szegmens)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss7|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad7|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite7|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead7|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime7|PROCESSZOR (7. szegmens)|Százalék|Maximum||Nincs dimenzió|
|connectedclients8|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed8|Összes művelet (szegmens 8)|Darabszám|Összesen||Nincs dimenzió|
|cachehits8|Gyorsítótárbeli találatok (szilánk 8)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses8|Gyorsítótár-lemaradás (szegmens 8)|Darabszám|Összesen||Nincs dimenzió|
|getcommands8|Beolvasás (szilánk 8)|Darabszám|Összesen||Nincs dimenzió|
|setcommands8|Készletek (szilánk 8)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond8|Művelet/másodperc (szegmens 8)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys8|Kizárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys8|Összes kulcs (szilánk 8)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys8|Lejárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory8|Felhasznált memória (8. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss8|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||Nincs dimenzió|
|serverLoad8|Kiszolgáló betöltése (szegmens 8)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite8|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead8|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime8|CPU (szegmens 8)|Százalék|Maximum||Nincs dimenzió|
|connectedclients9|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincs dimenzió|
|totalcommandsprocessed9|Összes művelet (szegmens 9)|Darabszám|Összesen||Nincs dimenzió|
|cachehits9|Gyorsítótárbeli találatok (szilánk 9)|Darabszám|Összesen||Nincs dimenzió|
|cachemisses9|Gyorsítótár-lemaradás (szegmens 9)|Darabszám|Összesen||Nincs dimenzió|
|getcommands9|Beolvasás (szegmens 9)|Darabszám|Összesen||Nincs dimenzió|
|setcommands9|Készletek (szilánk 9)|Darabszám|Összesen||Nincs dimenzió|
|operationsPerSecond9|Művelet/másodperc (szegmens 9)|Darabszám|Maximum||Nincs dimenzió|
|evictedkeys9|Kizárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincs dimenzió|
|totalkeys9|Összes kulcs (szilánk 9)|Darabszám|Maximum||Nincs dimenzió|
|expiredkeys9|Lejárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincs dimenzió|
|usedmemory9|Felhasznált memória (10. szegmens)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss9|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||Nincs dimenzió|
|serverLoad9|Kiszolgáló terhelése (szegmens 9)|Százalék|Maximum||Nincs dimenzió|
|cacheWrite9|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead9|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime9|CPU (szilánk 9)|Százalék|Maximum||Nincs dimenzió|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincs dimenzió|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincs dimenzió|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|Nincs dimenzió|
|Lemez olvasási sebessége (bájt/s)|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|Nincs dimenzió|
|Lemez írási sebessége (bájt/s)|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|Nincs dimenzió|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|Nincs dimenzió|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|Nincs dimenzió|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Lemez olvasási sebessége (bájt/s)|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalCalls|Hívások összesen|Darabszám|Összesen|A hívások száma összesen|ApiName, OperationName, régió|
|SuccessfulCalls|Sikeres hívások|Darabszám|Összesen|A sikeres hívások száma.|ApiName, OperationName, régió|
|TotalErrors|Összes hiba|Darabszám|Összesen|A hibaüzenettel rendelkező hívások teljes száma (HTTP-válasz kódja 4xx vagy 5xx).|ApiName, OperationName, régió|
|BlockedCalls|Blokkolt hívások|Darabszám|Összesen|Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|ApiName, OperationName, régió|
|Kiszolgálóhibái|Kiszolgálói hibák|Darabszám|Összesen|A szolgáltatás belső hibája miatti hívások száma (HTTP-válasz kódja 5xx).|ApiName, OperationName, régió|
|ClientErrors|Ügyfél-hibák|Darabszám|Összesen|Ügyféloldali hibával rendelkező hívások száma (HTTP-válasz kódja 4xx).|ApiName, OperationName, régió|
|DataIn|A-ben tárolt adatértékek|Bájt|Összesen|A bejövő adat mérete bájtban megadva.|ApiName, OperationName, régió|
|DataOut|Kimenő adatvesztés|Bájt|Összesen|A kimenő adat mérete bájtban kifejezve.|ApiName, OperationName, régió|
|Késés|Késés|Ezredmásodpercben|Átlag|Késés ezredmásodpercben.|ApiName, OperationName, régió|
|CharactersTranslated|Lefordított karakterek|Darabszám|Összesen|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTrained|Betanított karakterek|Darabszám|Összesen|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|SpeechSessionDuration|Beszédfelismerési munkamenet időtartama|másodperc|Összesen|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|TotalTransactions|Tranzakciók összesen|Darabszám|Összesen|A tranzakciók száma összesen|Nincs dimenzió|
|TotalTokenCalls|Jogkivonat-hívások összesen|Darabszám|Összesen|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincs dimenzió|
|Bejövő hálózat|Számlázandó hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom)|Nincs dimenzió|
|Kimenő hálózat|Hálózati kimenő számlázandó|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren kimenő számlázható bájtok száma (kimenő forgalom)|Nincs dimenzió|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincs dimenzió|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincs dimenzió|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincs dimenzió|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincs dimenzió|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincs dimenzió|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincs dimenzió|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD (elavult)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincs dimenzió|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincs dimenzió|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincs dimenzió|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LOGIKAI EGYSÉG|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LOGIKAI EGYSÉG|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LOGIKAI EGYSÉG|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LOGIKAI EGYSÉG|
|Adatlemez várólistájának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LOGIKAI EGYSÉG|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincs dimenzió|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincs dimenzió|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincs dimenzió|
|Bejövő folyamatok|Bejövő folyamatok (előzetes verzió)|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincs dimenzió|
|Kimenő folyamatok|Kimenő folyamatok (előzetes verzió)|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincs dimenzió|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincs dimenzió|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincs dimenzió|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LOGIKAI EGYSÉG|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LOGIKAI EGYSÉG|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincs dimenzió|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincs dimenzió|
|Teljes hálózat|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincs dimenzió|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Bejövő hálózat|Számlázandó hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat|Hálózati kimenő számlázandó|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren kimenő számlázható bájtok száma (kimenő forgalom)|VMName|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|VMName|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|VMName|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincs dimenzió|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincs dimenzió|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD (elavult)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincs dimenzió|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincs dimenzió|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincs dimenzió|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez várólistájának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|Bejövő folyamatok|Bejövő folyamatok (előzetes verzió)|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő folyamatok|Kimenő folyamatok (előzetes verzió)|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|
|Teljes hálózat|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|CPU-használat|Darabszám|Átlag|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Memóriahasználat|Bájt|Átlag|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Másodpercenként fogadott hálózati bájtok száma|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|Nincs dimenzió|
|NetworkBytesTransmittedPerSecond|Másodpercenként továbbított hálózati bájtok száma|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|Nincs dimenzió|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalPullCount|Lekérések száma összesen|Darabszám|Átlag|A lekérések száma összesen|Nincs dimenzió|
|SuccessfulPullCount|Sikeres lekérések száma|Darabszám|Átlag|Sikeres Képkeresések száma|Nincs dimenzió|
|TotalPushCount|Leküldések száma összesen|Darabszám|Átlag|Leküldések száma összesen|Nincs dimenzió|
|SuccessfulPushCount|Sikeres leküldések száma|Darabszám|Átlag|Sikeres leküldések száma|Nincs dimenzió|
|RunDuration|Futtatás időtartama|Ezredmásodperc|Összesen|Futtatás időtartama (ezredmásodpercben)|Nincs dimenzió|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Darabszám|Összesen|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Nincs dimenzió|
|kube_node_status_allocatable_memory_bytes|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Összesen|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Nincs dimenzió|
|kube_pod_status_ready|A hüvelyek száma üzemkész állapotban|Darabszám|Összesen|A hüvelyek száma üzemkész állapotban|névtér, Pod|
|kube_node_status_condition|Különböző csomóponti feltételek állapota|Darabszám|Összesen|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|Hüvelyek száma fázis szerint|Darabszám|Összesen|Hüvelyek száma fázis szerint|fázis, névtér, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DCIApiCalls|API-hívások Customer Insights|Darabszám|Összesen||Nincs dimenzió|
|DCIMappingImportOperationSuccessfulLines|Az importálási művelet sikeres sorainak leképezése|Darabszám|Összesen||Nincs dimenzió|
|DCIMappingImportOperationFailedLines|Az importálási művelet sikertelen sorainak megfeleltetése|Darabszám|Összesen||Nincs dimenzió|
|DCIMappingImportOperationTotalLines|Az importálási művelet összes sorának leképezése|Darabszám|Összesen||Nincs dimenzió|
|DCIMappingImportOperationRuntimeInSeconds|Az importálási művelet futtatókörnyezetének leképezése másodpercben|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundProfileExportSucceeded|A kimenő profil exportálása sikerült|Darabszám|Összesen||Nincs dimenzió|
|DCIOutboundProfileExportFailed|A kimenő profil exportálása nem sikerült|Darabszám|Összesen||Nincs dimenzió|
|DCIOutboundProfileExportDuration|Kimenő profil exportálásának időtartama|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundKpiExportSucceeded|A kimenő KPI exportálása sikerült|Darabszám|Összesen||Nincs dimenzió|
|DCIOutboundKpiExportFailed|A kimenő KPI exportálása nem sikerült|Darabszám|Összesen||Nincs dimenzió|
|DCIOutboundKpiExportDuration|Kimenő KPI-exportálás időtartama|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundKpiExportStarted|Kimenő KPI-exportálás elindítva|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundKpiRecordCount|Kimenő KPI-rekordok száma|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundProfileExportCount|Kimenő profilok exportálásának száma|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundInitialProfileExportFailed|A kimenő kezdeti profil exportálása nem sikerült|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundInitialProfileExportSucceeded|A kimenő kezdeti profil exportálása sikerült|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundInitialKpiExportFailed|A kimenő kezdeti KPI-exportálás nem sikerült|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundInitialKpiExportSucceeded|A kimenő kezdeti KPI-exportálás sikerült|másodperc|Összesen||Nincs dimenzió|
|DCIOutboundInitialProfileExportDurationInSeconds|Kimenő kezdeti profil exportálásának időtartama másodpercben|másodperc|Összesen||Nincs dimenzió|
|AdlaJobForStandardKpiFailed|A standard KPI ADla-feladata másodpercek alatt meghiúsult|másodperc|Összesen||Nincs dimenzió|
|AdlaJobForStandardKpiTimeOut|ADla-feladatot a standard KPI-időkorlát másodpercben|másodperc|Összesen||Nincs dimenzió|
|AdlaJobForStandardKpiCompleted|A standard KPI ADla-feladata másodpercek alatt befejeződött|másodperc|Összesen||Nincs dimenzió|
|ImportASAValuesFailed|Nem sikerült importálni az ASA-értékeket|Darabszám|Összesen||Nincs dimenzió|
|ImportASAValuesSucceeded|Az ASA-értékek importálása sikerült|Darabszám|Összesen||Nincs dimenzió|
|DCIProfilesCount|Profil példányainak száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIInteractionsPerMonthCount|Interakciók havonta – darabszám|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIKpisCount|KPI-darabszám|Darabszám|Vezetéknév||Nincs dimenzió|
|DCISegmentsCount|Szegmensek száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIPredictiveMatchPoliciesCount|Prediktív egyezések száma|Darabszám|Vezetéknév||Nincs dimenzió|
|DCIPredictionsCount|Előrejelzési szám|Darabszám|Vezetéknév||Nincs dimenzió|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|NICReadThroughput|Olvasási sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter olvasási átviteli sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|NICWriteThroughput|Írási átviteli sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter írási sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|CloudReadThroughputPerShare|Felhőbeli letöltési teljesítmény (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való letöltési átviteli sebesség a jelentési időszak alatt.|Megosztás|
|CloudUploadThroughputPerShare|Felhőbeli feltöltési sebesség (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való feltöltési átviteli sebesség a jelentési időszak során.|Megosztás|
|BytesUploadedToCloudPerShare|Felhőbeli feltöltött bájtok (megosztás)|Bájt|Átlag|A jelentési időszak során az Azure-ba feltöltött bájtok teljes száma.|Megosztás|
|TotalCapacity|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|Nincs dimenzió|
|Availablecapacity;)|Rendelkezésre álló kapacitás|Bájt|Átlag|A rendelkezésre álló kapacitás bájtban a jelentési időszak alatt.|Nincs dimenzió|
|CloudUploadThroughput|Felhőbeli feltöltési sebesség|BytesPerSecond|Átlag|A Felhőbeli feltöltési sebesség az Azure-ba a jelentési időszak alatt.|Nincs dimenzió|
|CloudReadThroughput|Felhőbeli letöltési sebesség|BytesPerSecond|Átlag|A felhő letöltési sebessége az Azure-ba a jelentési időszak alatt.|Nincs dimenzió|
|BytesUploadedToCloud|Feltöltött Felhőbeli bájtok (eszköz)|Bájt|Átlag|Az Azure-ba az eszközről a jelentési időszakban feltöltött bájtok teljes száma.|Nincs dimenzió|
|HyperVVirtualProcessorUtilization|Edge-számítás – százalékos CPU|Százalék|Átlag|CPU-használat százalékos aránya|InstanceName|
|HyperVMemoryUtilization|Edge-számítás – memóriahasználat|Százalék|Átlag|Használatban lévő RAM mennyisége|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Sikertelen futtatások|Darabszám|Összesen||pipelineName, activityName|
|SuccessfulRuns|Sikeres futtatások|Darabszám|Összesen||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Sikertelen folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineSucceededRuns|A folyamat sikeresen futtatja a metrikákat|Darabszám|Összesen||FailureType, név|
|ActivityFailedRuns|Sikertelen tevékenység-futtatási metrikák|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|A sikeres tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|TriggerFailedRuns|Sikertelen trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerSucceededRuns|A sikeres trigger metrikákat futtat|Darabszám|Összesen||Név, FailureType|
|IntegrationRuntimeCpuPercentage|Integration Runtime CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableMemory|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|
|MaxAllowedResourceCount|Engedélyezett entitások maximális száma|Darabszám|Maximum||Nincs dimenzió|
|MaxAllowedFactorySizeInGbUnits|Maximálisan engedélyezett gyári méret (GB egység)|Darabszám|Maximum||Nincs dimenzió|
|ResourceCount|Entitások száma összesen|Darabszám|Maximum||Nincs dimenzió|
|FactorySizeInGbUnits|Gyári méret összesen (GB egység)|Darabszám|Maximum||Nincs dimenzió|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Darabszám|Összesen|A sikeres feladatok száma.|Nincs dimenzió|
|JobEndedFailure|Sikertelen feladatok|Darabszám|Összesen|Sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedCancelled|Megszakított feladatok|Darabszám|Összesen|Megszakított feladatok száma.|Nincs dimenzió|
|JobAUEndedSuccess|Sikeres AU-idő|másodperc|Összesen|A sikeres feladatok teljes AU-ideje.|Nincs dimenzió|
|JobAUEndedFailure|Sikertelen AU-idő|másodperc|Összesen|A sikertelen feladatok összes AU-ideje.|Nincs dimenzió|
|JobAUEndedCancelled|Megszakított AU-idő|másodperc|Összesen|A megszakított feladatok összes AU-ideje.|Nincs dimenzió|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|Nincs dimenzió|
|DataWritten|Írt adatértékek|Bájt|Összesen|A fiókba írt összes adatmennyiség.|Nincs dimenzió|
|DataRead|Olvasott információk|Bájt|Összesen|A fiókból beolvasott adatok teljes mennyisége.|Nincs dimenzió|
|WriteRequests|Írási kérelmek|Darabszám|Összesen|A fiókra vonatkozó adatírási kérelmek száma.|Nincs dimenzió|
|ReadRequests|Olvasási kérelmek|Darabszám|Összesen|A fióknak küldött adatolvasási kérelmek száma.|Nincs dimenzió|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincs dimenzió|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási korlát|Bájt|Átlag|Tárolási korlát|Nincs dimenzió|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincs dimenzió|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|Nincs dimenzió|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincs dimenzió|
|seconds_behind_master|Replikálás késése másodpercben|Darabszám|Átlag|Replikálás késése másodpercben|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincs dimenzió|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási korlát|Bájt|Átlag|Tárolási korlát|Nincs dimenzió|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincs dimenzió|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|Nincs dimenzió|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincs dimenzió|
|seconds_behind_master|Replikálás késése másodpercben|Darabszám|Átlag|Replikálás késése másodpercben|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincs dimenzió|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|storage_limit|Tárolási korlát|Bájt|Átlag|Tárolási korlát|Nincs dimenzió|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincs dimenzió|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|Nincs dimenzió|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|pg_replica_log_delay_in_seconds|Replika késése|másodperc|Maximum|Replika késése másodpercben|Nincs dimenzió|
|pg_replica_log_delay_in_bytes|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincs dimenzió|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincs dimenzió|
|IOPS|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincs dimenzió|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincs dimenzió|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincs dimenzió|
|d2c.telemetry.ingress.success|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincs dimenzió|
|c2d.commands.egress.complete.success|A parancsok befejeződtek|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.abandon.success|Elhagyott parancsok|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.reject.success|Elutasított parancsok|Darabszám|Összesen|Az eszköz által visszautasított felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|Devices. totalDevices|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.success|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincs dimenzió|
|d2c.telemetry.egress.orphaned|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincs dimenzió|
|d2c.telemetry.egress.invalid|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Útválasztás: az Event hub üzenetének késése|Ezredmásodperc|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodperc|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodperc|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események). Ez a metrika csak akkor kezd működni, ha engedélyezve van az Útválasztás (https://aka.ms/iotrouting) az IoT hub esetében.|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodperc|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események). Ez a metrika csak akkor kezd működni, ha engedélyezve van az Útválasztás (https://aka.ms/iotrouting) az IoT hub esetében.|Nincs dimenzió|
|d2c.endpoints.egress.storage|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincs dimenzió|
|d2c.endpoints.latency.storage|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodperc|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincs dimenzió|
|d2c.endpoints.egress.storage.bytes|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincs dimenzió|
|d2c.endpoints.egress.storage.blobs|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincs dimenzió|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát (https://aka.ms/ioteventgrid).|Eredmény, EventType|
|EventGridLatency|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|d2c.twin.read.success|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|d2c.twin.read.failure|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincs dimenzió|
|d2c.twin.read.size|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincs dimenzió|
|d2c.twin.update.success|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincs dimenzió|
|d2c.twin.update.failure|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|d2c.twin.update.size|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincs dimenzió|
|c2d.methods.success|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|Nincs dimenzió|
|C2D. Methods. failure|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|Nincs dimenzió|
|c2d.methods.requestSize|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincs dimenzió|
|c2d.methods.responseSize|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincs dimenzió|
|c2d.twin.read.success|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|c2d.twin.read.failure|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|c2d.twin.read.size|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincs dimenzió|
|c2d.twin.update.success|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|c2d.twin.update.failure|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|c2d.twin.update.size|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincs dimenzió|
|twinQueries.success|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|Nincs dimenzió|
|twinQueries.failure|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|Nincs dimenzió|
|twinQueries.resultSize|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincs dimenzió|
|jobs.createTwinUpdateJob.success|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincs dimenzió|
|jobs.createTwinUpdateJob.failure|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.success|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.failure|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincs dimenzió|
|jobs.listJobs.success|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|Nincs dimenzió|
|jobs.listJobs.failure|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|Nincs dimenzió|
|jobs.cancelJob.success|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|Nincs dimenzió|
|jobs.cancelJob.failure|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|Nincs dimenzió|
|jobs.queryJobs.success|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincs dimenzió|
|jobs.queryJobs.failure|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|Nincs dimenzió|
|feladatok. kész|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincs dimenzió|
|feladatok. sikertelen|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincs dimenzió|
|dailyMessageQuotaUsed|A felhasznált üzenetek teljes száma|Darabszám|Átlag|A jelenleg használt üzenetek teljes száma. Ez egy kumulatív érték, amely minden nap 00:00-kor, a nulla időpontra visszaáll.|Nincs dimenzió|
|deviceDataUsage|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincs dimenzió|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincs dimenzió|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincs dimenzió|
|konfigurációk|Konfigurációs mérőszámok|Darabszám|Összesen|A konfigurációs műveletek metrikái|Nincs dimenzió|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Darabszám|Összesen|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Hozzárendelt eszközök|Darabszám|Összesen|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Igazolási kísérletek|Darabszám|Összesen|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AvailableStorage|Rendelkezésre álló tár|Bájt|Összesen|5 perces részletességgel jelentett teljes rendelkezésre álló tárterület|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|Cassandra-kapcsolatok bezárása|Darabszám|Összesen|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|Régió, ClosureReason|
|CassandraRequestCharges|Cassandra-kérelmek díjai|Darabszám|Összesen|A Cassandra-kérelmekhez felhasznált RUs|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra-kérelmek|Darabszám|Darabszám|A végrehajtott Cassandra-kérelmek száma|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Adathasználat|Bájt|Összesen|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DocumentCount|Dokumentumok száma|Darabszám|Összesen|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|Dokumentum kvótája|Bájt|Összesen|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|IndexUsage|Indexelés használata|Bájt|Összesen|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|MetadataRequests|Metaadat-kérelmek|Darabszám|Darabszám|A metaadat-kérelmek száma. Cosmos DB az egyes fiókokhoz rendszermetaadat-gyűjteményt tart fenn, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, |
|MongoRequestCharge|Mongo-kérelem díja|Darabszám|Összesen|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequests|Mongo kérelmek|Darabszám|Darabszám|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|ProvisionedThroughput|Kiosztott átviteli sebesség|Darabszám|Maximum|Kiosztott átviteli sebesség|DatabaseName, CollectionName|
|ReplicationLatency|P99 replikáció késése|Ezredmásodpercben|Átlag|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion, TargetRegion|
|ServiceAvailability|Szolgáltatás rendelkezésre állása|Százalék|Átlag|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|Nincs dimenzió|
|TotalRequestUnits|Kérelmek összes egysége|Darabszám|Összesen|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType|
|TotalRequests|Összes kérelem|Darabszám|Darabszám|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincs dimenzió|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincs dimenzió|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Darabszám|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincs dimenzió|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincs dimenzió|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Nincs dimenzió|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodperc|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincs dimenzió|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincs dimenzió|
|PublishFailCount|Sikertelen események|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincs dimenzió|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Darabszám|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincs dimenzió|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName |
|Kiszolgálóhibái|Kiszolgálói hibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName |
|UserErrors|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|EntityName |
|QuotaExceededErrors|A kvóta túllépte a hibákat.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName |
|ThrottledRequests|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|EntityName |
|IncomingRequests|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|EntityName|
|IncomingMessages|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|EntityName|
|OutgoingMessages|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|EntityName|
|IncomingBytes|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|EntityName|
|OutgoingBytes|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|EntityName|
|ActiveConnections|ActiveConnections|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|EntityName|
|ConnectionsClosed|A kapcsolatok lezárva.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|EntityName|
|CaptureBacklog|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|EntityName|
|CapturedMessages|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|EntityName|
|CapturedBytes|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|EntityName|
|Méret|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|EntityName|
|INREQS|Bejövő kérelmek (elavult)|Darabszám|Összesen|Egy névtér összes bejövő küldési kérelme (elavult)|Nincs dimenzió|
|SUCCREQ|Sikeres kérések (elavult)|Darabszám|Összesen|A névtér összes sikeres kérelme (elavult)|Nincs dimenzió|
|FAILREQ|Sikertelen kérelmek (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)|Nincs dimenzió|
|SVRBSY|Kiszolgáló által foglalt hibák (elavult)|Darabszám|Összesen|Egy névtér összes kiszolgáló által foglalt hibája (elavult)|Nincs dimenzió|
|INTERer|Belső kiszolgálói hibák (elavult)|Darabszám|Összesen|Névtér összes belső kiszolgálóhiba (elavult)|Nincs dimenzió|
|MISCERR|Egyéb hibák (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)|Nincs dimenzió|
|INMSGS|Bejövő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)|Nincs dimenzió|
|EHINMSGS|Bejövő üzenetek (elavult)|Darabszám|Összesen|Névtér összes bejövő üzenete (elavult)|Nincs dimenzió|
|OUTMSGS|Kimenő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)|Nincs dimenzió|
|EHOUTMSGS|Kimenő üzenetek (elavult)|Darabszám|Összesen|Névtér összes kimenő üzenete (elavult)|Nincs dimenzió|
|EHINMBS|Bejövő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)|Nincs dimenzió|
|EHINBYTES|Bejövő bájtok (elavult)|Bájt|Összesen|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincs dimenzió|
|EHOUTMBS|Kimenő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)|Nincs dimenzió|
|EHOUTBYTES|Kimenő bájtok (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincs dimenzió|
|EHABL|Archivált várakozó üzenetek (elavult)|Darabszám|Összesen|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)|Nincs dimenzió|
|EHAMSGS|Archivált üzenetek (elavult)|Darabszám|Összesen|Event hub archivált üzenetek egy névtérben (elavult)|Nincs dimenzió|
|EHAMBS|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összesen|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)|Nincs dimenzió|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előzetes verzió)|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma. (Előzetes verzió)|Nincs dimenzió|
|Kiszolgálóhibái|Kiszolgálói hibák. (Előzetes verzió)|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében. (Előzetes verzió)|Nincs dimenzió|
|UserErrors|Felhasználói hibák. (Előzetes verzió)|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái. (Előzetes verzió)|Nincs dimenzió|
|QuotaExceededErrors|A kvóta túllépte a hibákat. (Előzetes verzió)|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit. (Előzetes verzió)|Nincs dimenzió|
|ThrottledRequests|Szabályozott kérelmek. (Előzetes verzió)|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek. (Előzetes verzió)|Nincs dimenzió|
|IncomingRequests|Bejövő kérések (előzetes verzió)|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései. (Előzetes verzió)|Nincs dimenzió|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei. (Előzetes verzió)|Nincs dimenzió|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei. (Előzetes verzió)|Nincs dimenzió|
|IncomingBytes|Bejövő bájtok. (Előzetes verzió)|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai. (Előzetes verzió)|Nincs dimenzió|
|OutgoingBytes|Kimenő bájtok. (Előzetes verzió)|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai. (Előzetes verzió)|Nincs dimenzió|
|ActiveConnections|Aktív kapcsolatai (előzetes verzió)|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatok. (Előzetes verzió)|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsClosed|A kapcsolatok lezárva. (Előzetes verzió)|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma. (Előzetes verzió)|Nincs dimenzió|
|CaptureBacklog|Várakozó fájlok rögzítése. (Előzetes verzió)|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése. (Előzetes verzió)|Nincs dimenzió|
|CapturedMessages|Rögzített üzenetek. (Előzetes verzió)|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei. (Előzetes verzió)|Nincs dimenzió|
|CapturedBytes|Rögzített bájtok száma. (Előzetes verzió)|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma. (Előzetes verzió)|Nincs dimenzió|
|CPU|CPU (előzetes verzió)|Százalék|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Szerepkör|
|AvailableMemory|Rendelkezésre álló memória (előzetes verzió)|Darabszám|Maximum|Az Event hub-fürt számára rendelkezésre álló memória bájtban megadva|Szerepkör|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjáróra vonatkozó kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategorizált átjáró-kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Aktív feldolgozók száma|Darabszám|Maximum|Aktív feldolgozók száma|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrika értéke|Darabszám|Átlag|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|MetricThreshold|Metrika küszöbértéke|Darabszám|Átlag|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Darabszám|Átlag|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.|Nincs dimenzió|
|ScaleActionsInitiated|Elindított skálázási műveletek|Darabszám|Összesen|A skálázási művelet iránya.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Nyilvános előzetes verzió)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Rendelkezésre állás|Százalék|Átlag|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|Rendelkezésre állási tesztek|Darabszám|Darabszám|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Oldal betöltési hálózati kapcsolati ideje|Ezredmásodpercben|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|Nincs dimenzió|
|browserTimings/processingDuration|Ügyfél feldolgozási ideje|Ezredmásodpercben|Átlag|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|Nincs dimenzió|
|browserTimings/receiveDuration|Válaszidő fogadása|Ezredmásodpercben|Átlag|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|Nincs dimenzió|
|browserTimings/sendDuration|Kérelem küldési ideje|Ezredmásodpercben|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|Nincs dimenzió|
|browserTimings/totalDuration|Böngésző oldalának betöltési ideje|Ezredmásodpercben|Átlag|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|Nincs dimenzió|
|függőségek/darabszám|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított hívások száma.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|függőségek/időtartam|Függőség időtartama|Ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|függőségek/sikertelen|Függőségi hívások hibái|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|Oldalmegtekintések/darabszám|Lapok nézetei|Darabszám|Darabszám|Lapok nézeteinek száma.|művelet/szintetikus|
|pageViews/duration|Oldal nézet betöltési ideje|Ezredmásodpercben|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus|
|performanceCounters/requestExecutionTime|HTTP-kérelem végrehajtási ideje|Ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-kérelmek az alkalmazás-várólistában|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájának hossza|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-kérelmek gyakorisága|CountPerSecond|Átlag|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Kivételek aránya|CountPerSecond|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Folyamat i/o-sebessége|BytesPerSecond|Átlag|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processzoridő|Százalék|Átlag|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Rendelkezésre álló memória|Bájt|Átlag|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Saját bájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|cloud/roleInstance|
|kérelmek/időtartam|Kiszolgáló válaszideje|Ezredmásodpercben|Átlag|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/darabszám|Kiszolgálói kérelmek|Darabszám|Darabszám|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|Sikertelen kérelmek|Darabszám|Darabszám|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések > = 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Átlag|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kivételek/darabszám|Kivételek|Darabszám|Darabszám|Az összes nem kezelt kivétel összesített száma.|cloud/roleName, cloud/roleInstance, client/type|
|kivételek/böngésző|Böngészőkivételek|Darabszám|Darabszám|A böngészőben fellépő nem kezelt kivételek száma.|Nincs dimenzió|
|kivételek/kiszolgáló|Kiszolgálói kivételek|Darabszám|Darabszám|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|felhő/roleName, felhő/roleInstance|
|nyomkövetés/darabszám|Nyomok|Darabszám|Darabszám|Nyomkövetési dokumentumok száma|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Összes szolgáltatási API-találat|Darabszám|Darabszám|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Általános szolgáltatás API-késése|Ezredmásodperc|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Összes szolgáltatás API-eredményei|Darabszám|Darabszám|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CacheUtilization|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|Nincs|
|QueryDuration|Lekérdezés időtartama|Ezredmásodperc|Átlag|Lekérdezések időtartama másodpercben|Lekérdezés állapota|
|IngestionUtilization|Betöltés kihasználtsága|Százalék|Átlag|A használatban lévő betöltési tárolóhelyek aránya a fürtben|Nincs|
|KeepAlive|Életben tartása|Darabszám|Átlag|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|Nincs|
|IngestionVolumeInMB|Betöltési mennyiség (MB)|Darabszám|Összesen|A betöltött adatmennyiség teljes mennyisége a fürtön (MB)|Adatbázis|
|IngestionLatencyInSeconds|Betöltési késleltetés (másodperc)|másodperc|Átlag|A lenyelési idő a forrástól (például az üzenet a EventHub) a fürthöz másodpercek alatt|Nincs|
|EventProcessedForEventHubs|Feldolgozott események (Event Hubs)|Darabszám|Összesen|Az Event hub-ból való betöltéskor a fürt által feldolgozott események száma|Nincs|
|IngestionResult|Betöltés eredménye|Darabszám|Darabszám|Betöltési műveletek száma|status|
|CPU|CPU|Százalék|Átlag|CPU-kihasználtsági szint|Nincs|
| ContinuousExportNumOfRecordsExported | A folyamatos exportálás során exportált rekordok száma | Darabszám | Összesen | Az exportálási művelet során írt összes tárolási összetevőhöz exportált rekordok száma  | Nincs |
| ExportUtilization | Exportálás kihasználtsága | Százalék | Maximum | Exportálás kihasználtsága | Nincs |
| ContinuousExportPendingCount | Folyamatos exportálás függőben lévő darabszáma | Darabszám | Maximum | A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma | Nincs |
| ContinuousExportMaxLatenessMinutes | Folyamatos exportálás maximális késői percben | Darabszám | Maximum | Az összes függőben lévő és a végrehajtásra kész összes folyamatos exportálás percben megadott maximális ideje | Nincs |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Darabszám|Darabszám|API-hívások száma|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCompleted|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsSucceeded|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsFailed|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCancelled|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunLatency|Késés futtatása|másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunSuccessLatency|Sikeres Futtatás késése|másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunThrottledEvents|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincs dimenzió|
|RunFailurePercentage|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincs dimenzió|
|ActionsStarted|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Sikertelen műveletek|Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|Művelet késése |másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|Művelet sikerességi késése |másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincs dimenzió|
|TriggersStarted|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersCompleted|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSucceeded|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFailed|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFired|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggerLatency|Trigger késése |másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerFireLatency|Kiváltó tűz késése |másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerThrottledEvents|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincs dimenzió|
|BillableActionExecutions|Számlázható műveletek végrehajtása|Darabszám|Összesen|A számlázott munkafolyamat-műveletek végrehajtásának száma.|Nincs dimenzió|
|BillableTriggerExecutions|Számlázható trigger-végrehajtások|Darabszám|Összesen|A számlázott munkafolyamat-trigger-végrehajtások száma.|Nincs dimenzió|
|TotalBillableExecutions|Számlázandó végrehajtások összesen|Darabszám|Összesen|A számlázandó munkafolyamat-végrehajtások száma.|Nincs dimenzió|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Darabszám|Összesen|A számlázott natív művelet-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Darabszám|Összesen|A számlázott szabványos összekötő-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Darabszám|Összesen|A számlázható tárterület-használat végrehajtásának száma.|Nincs dimenzió|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Darabszám|Összesen|A számlázott natív művelet-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Darabszám|Összesen|A számlázott szabványos összekötő-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Darabszám|Összesen|A számlázható tárterület-használat végrehajtásának száma.|Nincs dimenzió|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCompleted|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsSucceeded|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsFailed|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCancelled|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunLatency|Késés futtatása|másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunSuccessLatency|Sikeres Futtatás késése|másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunThrottledEvents|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincs dimenzió|
|RunStartThrottledEvents|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincs dimenzió|
|RunFailurePercentage|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincs dimenzió|
|ActionsStarted|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|Művelet késése |másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|Művelet sikerességi késése |másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincs dimenzió|
|TriggersStarted|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersCompleted|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSucceeded|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFailed|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFired|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggerLatency|Trigger késése |másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerFireLatency|Kiváltó tűz késése |másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerThrottledEvents|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincs dimenzió|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|Nincs dimenzió|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|Nincs dimenzió|
|IntegrationServiceEnvironmentConnectorProcessorUsage|integrációs szolgáltatási környezet összekötő-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|Nincs dimenzió|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Összekötő memóriahasználat integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|Nincs dimenzió|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Befejezett futtatások|Befejezett futtatások|Darabszám|Összesen|A munkaterülethez sikeresen befejezett futtatások száma|Alkalmazási helyzet|
|Elindított futtatások|Elindított futtatások|Darabszám|Összesen|A munkaterülethez elindított futtatások száma|Alkalmazási helyzet|
|Sikertelen futtatások|Sikertelen futtatások|Darabszám|Összesen|Sikertelen futtatások száma ehhez a munkaterülethez|Alkalmazási helyzet|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Darabszám|Darabszám|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageOtherLatency|Egyéb késések átlaga|ms/op|Átlag|Átlagos egyéb késés (nem olvasható vagy írható) ezredmásodpercben, művelet|Nincs dimenzió|
|AverageReadLatency|Olvasási késleltetés átlagos késése|ms/op|Átlag|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|Nincs dimenzió|
|AverageTotalLatency|Teljes késés átlaga|ms/op|Átlag|Átlagos teljes késés (ezredmásodpercben)/művelet|Nincs dimenzió|
|AverageWriteLatency|Írási késleltetés átlagos késése|ms/op|Átlag|Írási késleltetés átlagos száma ezredmásodpercben|Nincs dimenzió|
|FilesystemOtherOps|Fájlrendszer – egyéb Ops|Ops|Átlag|Fájlrendszer egyéb műveleteinek száma (nem olvasható vagy írható)|Nincs dimenzió|
|FilesystemReadOps|Fájlrendszer olvasási Ops|Ops|Átlag|A fájlrendszer olvasási műveleteinek száma|Nincs dimenzió|
|FilesystemTotalOps|Fájlrendszer összes Ops|Ops|Átlag|Az összes fájlrendszer-művelet összege|Nincs dimenzió|
|FilesystemWriteOps|Fájlrendszer írási Ops|Ops|Átlag|A fájlrendszer írási műveleteinek száma|Nincs dimenzió|
|IoBytesPerOtherOps|IO bájt/egyéb műveleti azonosító|bájt/op|Átlag|/Kimenő bájtok száma/egyéb műveletek (nem olvasható vagy írható)|Nincs dimenzió|
|IoBytesPerReadOps|IO bájt/olvasási Ops|bájt/op|Átlag|Bejövő/kimenő bájtok másodpercenkénti száma olvasási művelet esetén|Nincs dimenzió|
|IoBytesPerTotalOps|IO-bájt/op az összes műveletnél|bájt/op|Átlag|Az összes/kimenő bájtok műveletének összege|Nincs dimenzió|
|IoBytesPerWriteOps|IO bájt/írási műveleti egység|bájt/op|Átlag|Bejövő/kimenő bájtok száma írási művelettel|Nincs dimenzió|
|OtherIops|Egyéb IOPS|művelet/másodperc|Átlag|Egyéb/kimenő műveletek másodpercenként|Nincs dimenzió|
|OtherThroughput|Egyéb átviteli sebesség|MBps|Átlag|Egyéb átviteli sebesség (amely nem írható vagy írható) megabájt/másodpercben|Nincs dimenzió|
|ReadIops|IOPS olvasása|művelet/másodperc|Átlag|Olvasási/kimenő műveletek másodpercenként|Nincs dimenzió|
|ReadThroughput|Olvasási sebesség|MBps|Átlag|Olvasási sebesség (MB/s) másodpercenként|Nincs dimenzió|
|TotalIops|Összes IOPS|művelet/másodperc|Átlag|Az összes/kimenő művelet másodpercenkénti összege|Nincs dimenzió|
|TotalThroughput|Teljes átviteli sebesség|MBps|Átlag|Az összes átviteli sebesség (MB/s) összege másodpercenként|Nincs dimenzió|
|VolumeAllocatedSize|Kötet lefoglalt mérete|bájt|Átlag|A kötet lefoglalt mérete (nem a tényleges felhasznált bájtok)|Nincs dimenzió|
|VolumeLogicalSize|Kötet logikai mérete|bájt|Átlag|A kötet logikai mérete (felhasznált bájtok)|Nincs dimenzió|
|VolumeSnapshotSize|Kötet pillanatképének mérete|bájt|Átlag|A köteten található összes pillanatkép mérete|Nincs dimenzió|
|WriteIops|IOPS írása|művelet/másodperc|Átlag|Írási/kimenő műveletek másodpercenként|Nincs dimenzió|
|WriteThroughput|Írási sebesség|MBps|Átlag|Írási sebesség megabájtban (MB/s)|Nincs dimenzió|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Kötet-készlet lefoglalt mérete|bájt|Átlag|A készlet lefoglalt mérete (nem a tényleges felhasznált bájtok)|Nincs dimenzió|
|VolumePoolAllocatedUsed|Lefoglalt kötet-készlet|bájt|Átlag|A készlet lefoglalt felhasznált mérete|Nincs dimenzió|
|VolumePoolTotalLogicalSize|Kötet készletének teljes logikai mérete|bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|Nincs dimenzió|
|VolumePoolTotalSnapshotSize|Kötet készletének teljes pillanatképének mérete|bájt|Átlag|A készlet összes pillanatképének összege|Nincs dimenzió|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Eljuttatott bájtok|Darabszám|Összesen|A hálózati adapter által eljuttatott bájtok száma|Nincs dimenzió|
|BytesReceivedRate|Fogadott bájtok száma|Darabszám|Összesen|A hálózati adapter által fogadott bájtok száma|Nincs dimenzió|
|PacketsSentRate|Küldött csomagok|Darabszám|Összesen|A hálózati adapter által küldött csomagok száma|Nincs dimenzió|
|PacketsReceivedRate|Fogadott csomagok|Darabszám|Összesen|A hálózati adapter által fogadott csomagok száma|Nincs dimenzió|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress, FrontendPort|
|DipAvailability|Állapot mintavételi állapota|Darabszám|Átlag|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Bájtok száma|Darabszám|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT-kapcsolatok száma|Darabszám|Összesen|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Lefoglalt SNAT portok (előzetes verzió)|Darabszám|Összesen|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Használt SNAT-portok (előzetes verzió)|Darabszám|Összesen|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezési kötet|Darabszám|Összesen|A DNS-zónák számára kiszolgált lekérdezések száma|Nincs dimenzió|
|RecordSetCount|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy DNS-zónában|Nincs dimenzió|
|RecordSetCapacityUtilization|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|Nincs dimenzió|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|Nincs dimenzió|
|PacketsDroppedDDoS|Bejövő csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő csomagok eldobott DDoS|Nincs dimenzió|
|PacketsForwardedDDoS|Bejövő csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő csomagok továbbított DDoS|Nincs dimenzió|
|TCPPacketsInDDoS|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|Nincs dimenzió|
|TCPPacketsDroppedDDoS|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok eldobott DDoS|Nincs dimenzió|
|TCPPacketsForwardedDDoS|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok továbbított DDoS|Nincs dimenzió|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok DDoS|Nincs dimenzió|
|UDPPacketsDroppedDDoS|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok eldobott DDoS|Nincs dimenzió|
|UDPPacketsForwardedDDoS|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok továbbított DDoS|Nincs dimenzió|
|BytesInDDoS|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|Nincs dimenzió|
|BytesDroppedDDoS|Bejövő bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő bájtok elvetve DDoS|Nincs dimenzió|
|BytesForwardedDDoS|Bejövő bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő bájtok továbbított DDoS|Nincs dimenzió|
|TCPBytesInDDoS|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|Nincs dimenzió|
|TCPBytesDroppedDDoS|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|Nincs dimenzió|
|TCPBytesForwardedDDoS|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|Nincs dimenzió|
|UDPBytesInDDoS|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|Nincs dimenzió|
|UDPBytesDroppedDDoS|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|Nincs dimenzió|
|UDPBytesForwardedDDoS|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|Nincs dimenzió|
|IfUnderDDoSAttack|DDoS-támadás alatt vagy nem|Darabszám|Maximum|DDoS-támadás alatt vagy nem|Nincs dimenzió|
|DDoSTriggerTCPPackets|Bejövő TCP-csomagok a DDoS-mérséklés indításához|CountPerSecond|Maximum|Bejövő TCP-csomagok a DDoS-mérséklés indításához|Nincs dimenzió|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|Nincs dimenzió|
|DDoSTriggerSYNPackets|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|Nincs dimenzió|
|VipAvailability|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Átlagos IP-cím rendelkezésre állása időszakonként|Port|
|ByteCount|Bájtok száma|Darabszám|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|PacketCount|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|SynCount|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ApplicationRuleHit|Alkalmazás-szabályok találatok száma|Darabszám|Összesen|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|NetworkRuleHit|Hálózati szabályok találatok száma|Darabszám|Összesen|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljesítmény|Teljesítmény|BytesPerSecond|Összesen|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|Nincs dimenzió|
|UnhealthyHostCount|Nem kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Sérült háttérbeli gazdagépek száma|BackendSettingsPool|
|HealthyHostCount|Kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Az egészséges háttérbeli gazdagépek száma|BackendSettingsPool|
|TotalRequests|Összes kérelem|Darabszám|Összesen|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|FailedRequests|Sikertelen kérelmek|Darabszám|Összesen|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|ResponseStatus|Válasz állapota|Darabszám|Összesen|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Összege|Aktuális kapcsolatok|Darabszám|Összesen|Application Gatewaysal létesített aktuális kapcsolatok száma|Nincs dimenzió|
|CapacityUnits|Aktuális kapacitási egységek|Darabszám|Átlag|Felhasznált kapacitási egységek|Nincs dimenzió|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageBandwidth|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincs dimenzió|
|P2SBandwidth|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincs dimenzió|
|P2SConnectionCount|P2S-kapcsolatok száma|Darabszám|Maximum|Átjáró pont – hely kapcsolatának száma|Protokoll|
|TunnelAverageBandwidth|Bújtatási sávszélesség|BytesPerSecond|Átlag|Egy alagút átlagos sávszélessége (bájt/s)|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Alagút kimenő bájtjai|Bájt|Összesen|Egy alagút kimenő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Alagutak bejövő bájtjai|Bájt|Összesen|Alagút bejövő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagjai|Darabszám|Összesen|Alagút kimenő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Bújtatási bejövő csomagok|Darabszám|Összesen|Alagút bejövő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Alagút kimenő terminálszolgáltatási eltérési csomagjainak eldobása|Darabszám|Összesen|Kimenő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Az alagút bejövő HOZZÁFÉRÉSe nem egyezik a csomagok eldobásával|Darabszám|Összesen|Bejövő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|Nincs dimenzió|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|Nincs dimenzió|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Visszaadott végponti lekérdezések|Darabszám|Összesen|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végponti állapot végpont szerint|Darabszám|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ProbesFailedPercent|%-Os mintavétel sikertelen|Százalék|Átlag|a kapcsolat figyelési mintavételének%-a meghiúsult|Nincs dimenzió|
|AverageRoundtripMs|Átlagos menetidő (MS)|Ezredmásodpercben|Átlag|A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS)|Nincs dimenzió|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RequestCount|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Kérelem mérete|Bájt|Összesen|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Háttérbeli kérelmek száma|Darabszám|Összesen|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Háttérbeli kérelmek késése|Ezredmásodpercben|Átlag|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Backend|
|TotalLatency|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Háttér állapotának százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|WebApplicationFirewallRequestCount|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|regisztráció. All|Regisztrációs műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |Nincs dimenzió|
|registration.create|Regisztráció-létrehozási műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs létrehozás száma.|Nincs dimenzió|
|registration.update|Regisztrációs frissítési műveletek|Darabszám|Összesen|A sikeres regisztrációs frissítések száma.|Nincs dimenzió|
|registration.get|Regisztrálási olvasási műveletek|Darabszám|Összesen|A sikeres regisztrációs lekérdezések száma.|Nincs dimenzió|
|registration.delete|Regisztrációs törlési műveletek|Darabszám|Összesen|A regisztráció sikeres törléseinak száma.|Nincs dimenzió|
|bejövő|Bejövő üzenetek|Darabszám|Összesen|A sikeres küldési API-hívások száma. |Nincs dimenzió|
|bejövő. ütemezett|Ütemezett leküldéses értesítések elküldve|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincs dimenzió|
|bejövő. ütemezett. Mégse|Ütemezett leküldéses értesítések megszakítva|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincs dimenzió|
|ütemezett. függőben|Függőben lévő ütemezett értesítések|Darabszám|Összesen|Függőben lévő ütemezett értesítések|Nincs dimenzió|
|a telepítés. All|Telepítési felügyeleti műveletek|Darabszám|Összesen|Telepítési felügyeleti műveletek|Nincs dimenzió|
|telepítés. Get|Telepítési műveletek beolvasása|Darabszám|Összesen|Telepítési műveletek beolvasása|Nincs dimenzió|
|telepítési. upsert|Telepítési műveletek létrehozása vagy frissítése|Darabszám|Összesen|Telepítési műveletek létrehozása vagy frissítése|Nincs dimenzió|
|Installation. patch|Javítások telepítési műveletei|Darabszám|Összesen|Javítások telepítési műveletei|Nincs dimenzió|
|installation.delete|Telepítési műveletek törlése|Darabszám|Összesen|Telepítési műveletek törlése|Nincs dimenzió|
|outgoing.allpns.success|Sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincs dimenzió|
|kimenő. allpns. invalidpayload|Hasznos adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|Nincs dimenzió|
|outgoing.allpns.pnserror|Külső értesítési rendszerhibák|Darabszám|Összesen|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|Nincs dimenzió|
|outgoing.allpns.channelerror|Csatorna hibái|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|Nincs dimenzió|
|outgoing.allpns.badorexpiredchannel|Rossz vagy lejárt csatorna-hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|Nincs dimenzió|
|outgoing.wns.success|WNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.wns.invalidcredentials|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|Nincs dimenzió|
|outgoing.wns.badchannel|WNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|Nincs dimenzió|
|outgoing.wns.expiredchannel|WNS lejárt csatorna hibája|Darabszám|Összesen|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapota: 410 elveszett).|Nincs dimenzió|
|kimenő. wns. szabályozva|WNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapota: 406, nem fogadható el).|Nincs dimenzió|
|outgoing.wns.tokenproviderunreachable|WNS-hitelesítési hibák (nem érhető el)|Darabszám|Összesen|A Windows Live nem érhető el.|Nincs dimenzió|
|kimenő. wns. invalidtoken|WNS-hitelesítési hibák (érvénytelen token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvénytelen (WNS-állapot: 401 jogosulatlan).|Nincs dimenzió|
|outgoing.wns.wrongtoken|WNS-hitelesítési hibák (hibás token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|Nincs dimenzió|
|kimenő. wns. invalidnotificationformat|WNS – érvénytelen értesítési formátum|Darabszám|Összesen|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|Nincs dimenzió|
|kimenő. wns. invalidnotificationsize|A WNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az értesítési tartalom túl nagy (WNS állapot: 413).|Nincs dimenzió|
|outgoing.wns.channelthrottled|WNS csatorna szabályozása|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-NotificationStatus: channelThrottled).|Nincs dimenzió|
|outgoing.wns.channeldisconnected|WNS csatorna leválasztva|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva).|Nincs dimenzió|
|kimenő. wns. Dropped|WNS eldobott értesítések|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincs dimenzió|
|outgoing.wns.pnserror|WNS hibák|Darabszám|Összesen|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|Nincs dimenzió|
|outgoing.wns.authenticationerror|WNS-hitelesítési hibák|Darabszám|Összesen|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|Nincs dimenzió|
|outgoing.apns.success|APNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.apns.invalidcredentials|APNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.apns.badchannel|APNS hibás csatorna hiba|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS bináris protokoll állapotkód: 8. APNS HTTP protokoll állapotkód: 400 "BadDeviceToken").|Nincs dimenzió|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Darabszám|Összesen|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|Nincs dimenzió|
|outgoing.apns.invalidnotificationsize|A APNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert túl nagy a hasznos adat (APNS bináris protokoll állapotkód: 7).|Nincs dimenzió|
|outgoing.apns.pnserror|APNS hibák|Darabszám|Összesen|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.success|GCM sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.gcm.invalidcredentials|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|kimenő. GCM. badchannel|GCM hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM-eredmény: érvénytelen regisztráció).|Nincs dimenzió|
|outgoing.gcm.expiredchannel|GCM lejárt csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM-eredmény: NotRegistered).|Nincs dimenzió|
|kimenő. GCM. szabályozva|GCM-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM: 501-599 vagy eredmény: nem érhető el).|Nincs dimenzió|
|kimenő. GCM. invalidnotificationformat|GCM – érvénytelen értesítési formátum|Darabszám|Összesen|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|Nincs dimenzió|
|kimenő. GCM. invalidnotificationsize|A GCM érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|Nincs dimenzió|
|kimenő. GCM. wrongchannel|GCM rossz csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM-eredmény: InvalidPackageName).|Nincs dimenzió|
|outgoing.gcm.pnserror|GCM hibák|Darabszám|Összesen|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.authenticationerror|GCM-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM result: MismatchedSenderId).|Nincs dimenzió|
|outgoing.mpns.success|MPNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.mpns.invalidcredentials|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.mpns.badchannel|MPNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|Nincs dimenzió|
|outgoing.mpns.throttled|MPNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem fogadható el).|Nincs dimenzió|
|outgoing.mpns.invalidnotificationformat|MPNS – érvénytelen értesítési formátum|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|Nincs dimenzió|
|outgoing.mpns.channeldisconnected|MPNS csatorna leválasztva|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (MPNS-állapot: 412 nem található).|Nincs dimenzió|
|kimenő. mpns. Dropped|MPNS eldobott értesítések|Darabszám|Összesen|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X-NotificationStatus: QueueFull vagy letiltva).|Nincs dimenzió|
|outgoing.mpns.pnserror|MPNS hibák|Darabszám|Összesen|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.mpns.authenticationerror|MPNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|notificationhub. leküldések|Minden kimenő értesítés|Darabszám|Összesen|Az értesítési központ összes kimenő értesítése|Nincs dimenzió|
|incoming.all.requests|Minden bejövő kérelem|Darabszám|Összesen|Értesítési központ összes bejövő kérelme|Nincs dimenzió|
|bejövő. ALL. failedrequests|Minden bejövő sikertelen kérelem|Darabszám|Összesen|Értesítési központ bejövő sikertelen kéréseinek száma|Nincs dimenzió|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_ szabad inode (%)|Szabad inode%-ban|Darabszám|Átlag|Average_ szabad inode (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ szabad terület (%)|% Szabad terület|Darabszám|Átlag|Average_ szabad terület (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ használt inode|Felhasznált inode%-ban|Darabszám|Átlag|Average_ használt inode|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ felhasznált terület%-ban|Foglalt hely %|Darabszám|Átlag|Average_ felhasznált terület%-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Olvasási sebesség (bájt/s) Average_Disk|Lemezolvasási sebesség (bájt/s)|Darabszám|Átlag|Olvasási sebesség (bájt/s) Average_Disk|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Lemezolvasások/mp|Darabszám|Átlag|Olvasási Average_Disk másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Átvitel/mp|Darabszám|Átlag|Average_Disk adatátvitel másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Írási sebesség (bájt/s) Average_Disk|Lemezírási sebesség (bájt/s)|Darabszám|Átlag|Írási sebesség (bájt/s) Average_Disk|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Lemezírások/mp|Darabszám|Átlag|Írás/mp Average_Disk|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabájt|Szabad hely MB-ban|Darabszám|Átlag|Average_Free megabájt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Logikai lemez bájt/mp|Darabszám|Átlag|Average_Logical lemez sebessége (bájt/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Rendelkezésre álló memória Average_%-ban|Rendelkezésre álló memória%-ban|Darabszám|Átlag|Rendelkezésre álló memória Average_%-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ rendelkezésre álló szabad hely a lapozófájlban|Rendelkezésre álló swap-terület (%)|Darabszám|Átlag|Average_ rendelkezésre álló szabad hely a lapozófájlban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Felhasznált memória Average_%-ban|Felhasznált memória (%)|Darabszám|Átlag|Felhasznált memória Average_%-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ felhasznált lapozófájl-terület|Felhasznált swap-terület%-ban|Darabszám|Átlag|Average_ felhasznált lapozófájl-terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB memória|Rendelkezésre álló memória|Darabszám|Átlag|Average_Available MB memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB-ban való swap|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB-ban való swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Olvasási Average_Page másodpercenként|Olvasott lap/mp|Darabszám|Átlag|Olvasási Average_Page másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Írás/mp Average_Page|Írási idő/mp|Darabszám|Átlag|Írás/mp Average_Page|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Lap/mp|Darabszám|Átlag|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MB-ban felcserélt terület|Felhasznált memória (MB) – lapozófájl|Darabszám|Átlag|Average_Used MB-ban felcserélt terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória MB-ban|Felhasznált memória (MB)|Darabszám|Átlag|Average_Used memória MB-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total továbbított bájtok|Küldött bájtok száma összesen|Darabszám|Átlag|Average_Total továbbított bájtok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Total bájtok száma|Fogadott bájtok teljes száma|Darabszám|Átlag|Fogadott Average_Total bájtok száma|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bájtok|Bájtok összesen|Darabszám|Átlag|Average_Total bájtok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total továbbított csomagok|Továbbított csomagok összesen|Darabszám|Átlag|Average_Total továbbított csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total fogadott csomagok|Fogadott csomagok összesen|Darabszám|Átlag|Average_Total fogadott csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Rx-hibák Average_Total|Rx-hibák összesen|Darabszám|Átlag|Rx-hibák Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-hibák|TX-hibák összesen|Darabszám|Átlag|Average_Total TX-hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Ütközések Average_Total|Ütközések összesen|Darabszám|Átlag|Ütközések Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Átlagos írási idő (mp/olvasás)|Darabszám|Átlag|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/átvitel|Átlagos műveleti idő (mp/átvitel)|Darabszám|Átlag|Average_Avg. Lemez mp/átvitel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Átlagos írási idő (mp/írás)|Darabszám|Átlag|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Fizikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Physical lemez sebessége (bájt/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|PCT rendszerjogosultságú idő|Darabszám|Átlag|Average_Pct emelt szintű idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|PCT felhasználói idő|Darabszám|Átlag|Average_Pct felhasználói idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Felhasznált memória (kilobájt)|Darabszám|Átlag|Average_Used memória kilobájtban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Virtuális megosztott memória|Darabszám|Átlag|Average_Virtual megosztott memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC idő|% DPC idő|Darabszám|Átlag|Average_% DPC idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ üresjárati idő%-ban|Üresjárati idő%-ban|Darabszám|Átlag|Average_ üresjárati idő%-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Megszakítási idő%-ban|Darabszám|Átlag|Average_%-os megszakítási idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO várakozási idő|I/o várakozási idő%-ban|Darabszám|Átlag|Average_% IO várakozási idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os szép idő|% Nice idő|Darabszám|Átlag|Average_%-os szép idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|%-Os privilegizált idő|Darabszám|Átlag|Average_%-os privilegizált idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ processzoridő|Processzoridő|Darabszám|Átlag|Average_ processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Felhasználói idő%-ban|Darabszám|Átlag|Average_%-os felhasználói idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fizikai memória Average_Free|Szabad fizikai memória|Darabszám|Átlag|Fizikai memória Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free lemezterület a Lapozófájlokban|Szabad terület a Lapozófájlokban|Darabszám|Átlag|Average_Free lemezterület a Lapozófájlokban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuális memória|Szabad virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Folyamatok|Darabszám|Átlag|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Lapozófájlokban tárolt méret|Darabszám|Átlag|Lapozófájlokban tárolt Average_Size|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Hasznos üzemidő|Darabszám|Átlag|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Felhasználók|Darabszám|Átlag|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Átlagos írási idő (mp/olvasás)|Darabszám|Átlag|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Átlagos írási idő (mp/írás)|Darabszám|Átlag|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current a lemez várólistájának hossza|Lemez aktuális várólistájának hossza|Darabszám|Átlag|Average_Current a lemez várólistájának hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Lemezolvasások/mp|Darabszám|Átlag|Olvasási Average_Disk másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Átvitel/mp|Darabszám|Átlag|Average_Disk adatátvitel másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Lemezírások/mp|Darabszám|Átlag|Írás/mp Average_Disk|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabájt|Szabad hely MB-ban|Darabszám|Átlag|Average_Free megabájt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ szabad terület (%)|% Szabad terület|Darabszám|Átlag|Average_ szabad terület (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB-ban|Rendelkezésre álló memória|Darabszám|Átlag|Average_Available MB-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Előjegyzett memória%-ban használatban|Darabszám|Átlag|Average_%-os előjegyzett bájtok használatban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Fogadott bájtok/mp|Darabszám|Átlag|Fogadott Average_Bytes/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Küldési Average_Bytes másodpercenként|Küldött bájtok/s|Darabszám|Átlag|Küldési Average_Bytes másodpercenként|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Összes bájt/mp|Darabszám|Átlag|Average_Bytes összesen/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ processzoridő|Processzoridő|Darabszám|Átlag|Average_ processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Processzor-várólista hossza|Darabszám|Átlag|Average_Processor várólista hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Szívverés|Szívverés|Darabszám|Összesen|Szívverés|Computer, OSType, Version, SourceComputerId|
|Frissítés|Frissítés|Darabszám|Átlag|Frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|
|Esemény|Esemény|Darabszám|Átlag|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Lekérdezés időtartama|Ezredmásodperc|Átlag|DAX-lekérdezés időtartama az utolsó intervallumban|Nincs dimenzió|
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|Nincs dimenzió|
|qpu_high_utilization_metric|QPU magas kihasználtsága|Darabszám|Összesen|QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0|Nincs dimenzió|
|memory_metric|Memória|Bájt|Átlag|Memória. 0-3 GB-os tartomány a1, 0-5 GB az a2-hez, 0-10 GB az a3-hoz, 0-25 GB a4-hez, 0-50 GB az A5-hez és az A6-os 0-100 GB-hoz|Nincs dimenzió|
|memory_thrashing_metric|Memória-Kiverés|Százalék|Átlag|Memória átlagos kiverése.|Nincs dimenzió|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Darabszám|Összesen|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Darabszám|Összesen|A Microsoft. Relay sikeres feladói kapcsolatokra.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra ServerError.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra összesen.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Darabszám|Összesen|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|EntityName|
|ActiveConnections|ActiveConnections|Darabszám|Összesen|A Microsoft. Relay aktív kapcsolatai összesen.|EntityName|
|ActiveListeners|ActiveListeners|Darabszám|Összesen|A Microsoft. Relay ActiveListeners összesen.|EntityName|
|BytesTransferred|BytesTransferred|Darabszám|Összesen|A Microsoft. Relay BytesTransferred összesen.|EntityName|
|Bontásai|Bontásai|Darabszám|Összesen|A Microsoft. Relay bontásai összesen.|EntityName|
|SenderDisconnects|SenderDisconnects|Darabszám|Összesen|A Microsoft. Relay SenderDisconnects összesen.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|Nincs dimenzió|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|CountPerSecond|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|Nincs dimenzió|
|ThrottledSearchQueriesPercentage|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|Nincs dimenzió|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előzetes verzió)|Darabszám|Összesen|A névtér összes sikeres kérelme (előzetes verzió)|EntityName|
|Kiszolgálóhibái|Kiszolgálói hibák. (Előzetes verzió)|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. ServiceBus esetében. (Előzetes verzió)|EntityName|
|UserErrors|Felhasználói hibák. (Előzetes verzió)|Darabszám|Összesen|A Microsoft. ServiceBus felhasználói hibái. (Előzetes verzió)|EntityName|
|ThrottledRequests|Szabályozott kérelmek. (Előzetes verzió)|Darabszám|Összesen|A Microsoft. ServiceBus által szabályozott kérelmek. (Előzetes verzió)|EntityName|
|IncomingRequests|Bejövő kérések (előzetes verzió)|Darabszám|Összesen|A Microsoft. ServiceBus bejövő kérései. (Előzetes verzió)|EntityName|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Darabszám|Összesen|A Microsoft. ServiceBus bejövő üzenetei. (Előzetes verzió)|EntityName|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Darabszám|Összesen|A Microsoft. ServiceBus kimenő üzenetei. (Előzetes verzió)|EntityName|
|ActiveConnections|Aktív kapcsolatai (előzetes verzió)|Darabszám|Összesen|A Microsoft. ServiceBus összes aktív kapcsolata. (Előzetes verzió)|Nincs dimenzió|
|Méret|Méret (előzetes verzió)|Bájt|Átlag|Várólista/témakör mérete bájtban. (Előzetes verzió)|EntityName|
|Üzenetek|Üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|Darabszám|Átlag|Üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|EntityName|
|ActiveMessages|Üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|Darabszám|Átlag|Üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|EntityName|
|DeadletteredMessages|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. (Előzetes verzió)|Darabszám|Átlag|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. (Előzetes verzió)|EntityName|
|ScheduledMessages|Az üzenetsor/témakör ütemezett üzeneteinek száma. (Előzetes verzió)|Darabszám|Átlag|Az üzenetsor/témakör ütemezett üzeneteinek száma. (Előzetes verzió)|EntityName|
|CPUXNS|CPU-használat/névtér|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája|Nincs dimenzió|
|WSXNS|Memória méretének használata névtérben|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma|Nincs dimenzió|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Darabszám|Átlag|A tárolóhoz a millicores-ben lefoglalt processzor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bájt|Átlag|A tároló számára lefoglalt memória (MB)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Darabszám|Átlag|Tényleges CPU-használat a millicores-ben|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|ServiceStatus|ServiceStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ConnectionCount|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|MessageCount|Üzenetek száma|Darabszám|Összesen|Az üzenetek teljes mennyisége.|Nincs dimenzió|
|InboundTraffic|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|Nincs dimenzió|
|OutboundTraffic|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|Nincs dimenzió|
|UserErrors|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|Nincs dimenzió|
|SystemErrors|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|Nincs dimenzió|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|app_cpu_billed|Az alkalmazás CPU-számlázása|Darabszám|Összesen|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincs dimenzió|
|app_cpu_percent|Alkalmazás CPU-aránya|Százalék|Átlag|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincs dimenzió|
|app_memory_percent|Alkalmazás memóriájában használt százalék|Százalék|Átlag|Az alkalmazás memóriájában használt százalék. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincs dimenzió|
|blocked_by_firewall|Tűzfal blokkolja|Darabszám|Összesen|Tűzfal blokkolja|Nincs dimenzió|
|cache_hit_percent|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|cache_used_percent|Gyorsítótár használt százaléka|Százalék|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|connection_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincs dimenzió|
|connection_successful|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincs dimenzió|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincs dimenzió|
|cpu_limit|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|cpu_used|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|holtpont|Holtpontok|Darabszám|Összesen|Holtpontok. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|diff_backup_size_bytes|Különbözeti biztonsági másolatok tárolási mérete|Bájt|Maximum|Kumulatív különbözeti biztonsági másolati tárterület mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincs dimenzió|
|dtu_limit|DTU korlátja|Darabszám|Átlag|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|dtu_used|Használt DTU|Darabszám|Átlag|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|dw_cpu_percent|A DW-csomópont szintjének CPU-aránya|Százalék|Átlag|A DW-csomópont szintjének CPU-aránya|DwLogicalNodeId|
|dw_physical_data_read_percent|A DW-csomópont szintű adat IO-aránya|Százalék|Átlag|A DW-csomópont szintű adat IO-aránya|DwLogicalNodeId|
|dwu_consumption_percent|DWU százalékos aránya|Százalék|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|dwu_limit|DWU korlátja|Darabszám|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|dwu_used|Használt DWU|Darabszám|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|full_backup_size_bytes|Teljes biztonsági mentési tár mérete|Bájt|Maximum|Összesített teljes biztonsági mentési tár mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincs dimenzió|
|local_tempdb_usage_percent|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|log_backup_size_bytes|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő napló biztonsági mentési tárterületének mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincs dimenzió|
|log_write_percent|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincs dimenzió|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|sqlserver_process_core_percent|SQL Server Process Core százalék|Százalék|Maximum|Ez a metrika helyőrző, és nem töltődik fel jelenleg.|Nincs dimenzió|
|sqlserver_process_memory_percent|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Ez a metrika helyőrző, és nem töltődik fel jelenleg.|Nincs dimenzió|
|tárterület|Felhasznált adatterület|Bájt|Maximum|Az adatbázis teljes mérete. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|storage_percent|Felhasznált adatterület százalékos aránya|Százalék|Maximum|Az adatbázis mérete százalékban megadva. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|Nincs dimenzió|
|tempdb_data_size|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|Tempdb az adatfájl mérete kilobájtban. Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincs dimenzió|
|tempdb_log_size|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|Tempdb-naplófájl mérete (kilobájt) Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincs dimenzió|
|tempdb_log_used_percent|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van. Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincs dimenzió|
|workers_percent|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP-tárolási százalék. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|Nincs dimenzió|
|allocated_data_storage_percent|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|Nincs dimenzió|
|cpu_limit|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincs dimenzió|
|cpu_used|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|eDTU_limit|eDTU korlátja|Darabszám|Átlag|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|eDTU_used|használt eDTU|Darabszám|Átlag|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|log_write_percent|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|Nincs dimenzió|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincs dimenzió|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincs dimenzió|
|storage_limit|Az adatmaximális méret|Bájt|Átlag|Tárolási korlát|Nincs dimenzió|
|storage_percent|Felhasznált adatterület százalékos aránya||Százalék|Átlag|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált tárterület|Nincs dimenzió|
|sqlserver_process_core_percent|SQL Server Process Core százalék|Százalék|Maximum|Ez a metrika helyőrző, és nem töltődik fel jelenleg.|Nincs dimenzió|
|sqlserver_process_memory_percent|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Ez a metrika helyőrző, és nem töltődik fel jelenleg.|Nincs dimenzió|
|tempdb_data_size|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|Tempdb az adatfájl mérete kilobájtban. Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincs dimenzió|
|tempdb_log_size|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|Tempdb-naplófájl mérete (kilobájt) Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincs dimenzió|
|tempdb_log_used_percent|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van. Az adattárházak esetében nem alkalmazható. Ez a metrika a virtuális mag beszerzési modellel vagy a 100 DTU, illetve a DTU-alapú vásárlási modellekhez magasabb szintű adatbázisok számára lesz elérhető.|Nincs dimenzió|
|workers_percent|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|Nincs dimenzió|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP tárolási százaléka|Nincs dimenzió|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|avg_cpu_percent|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|Nincs dimenzió|
|io_bytes_read|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|Nincs dimenzió|
|io_requests|IO-kérelmek száma|Darabszám|Átlag|IO-kérelmek száma|Nincs dimenzió|
|io_bytes_written|I/o-bájtok írása|Bájt|Átlag|I/o-bájtok írása|Nincs dimenzió|
|reserved_storage_mb|Tárterület fenntartva|Darabszám|Átlag|Tárterület fenntartva|Nincs dimenzió|
|storage_space_used_mb|Felhasznált tárterület|Darabszám|Átlag|Felhasznált tárterület|Nincs dimenzió|
|virtual_core_count|Virtuális mag száma|Darabszám|Átlag|Virtuális mag száma|Nincs dimenzió|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Használt kapacitás|Bájt|Átlag|A fiók felhasznált kapacitása|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|Blob-kapacitása|Bájt|Átlag|A tárfiók Blob Service-példánya által felhasznált tárterület mérete bájtban megadva.|BlobType, Tier|
|BlobCount|Blobok száma|Darabszám|Összesen|A tárfiók Blob Service-példányában található blobok száma.|BlobType|       |BlobCount|Blobok száma|Darabszám|Átlag|A tárfiók Blob Service-példányában található blobok száma.|BlobType, Tier|
|ContainerCount|Blobtárolók száma|Darabszám|Átlag|A tárfiók Blob Service-példányában található tárolók száma.|Nincs dimenzió|
|IndexCapacity|Index kapacitása|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|FileCount|Fájlok száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Nincs dimenzió|
|FileShareCount|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Queue Storage kapacitása|Bájt|Átlag|A tárfiók Queue Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|QueueCount|Üzenetsorok száma|Darabszám|Átlag|A tárfiók Queue-szolgáltatás-példányában található üzenetsorok száma.|Nincs dimenzió|
|QueueMessageCount|Üzenetsorbeli üzenetek száma|Darabszám|Átlag|A tárfiók Queue Storage-szolgáltatás-példányában található üzenetsorbeli üzenetek hozzávetőleges száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Table Storage kapacitása|Bájt|Átlag|A tárfiók Table Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|TableCount|Táblák száma|Darabszám|Átlag|A tárfiók Table Storage-szolgáltatás-példányában található táblák száma.|Nincs dimenzió|
|TableEntityCount|Táblaentitások száma|Darabszám|Átlag|A tárfiók Table Storage-szolgáltatás-példányában található táblaentitások száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Szinkronizálási munkamenet eredménye|Darabszám|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a regisztrált kiszolgáló sikeresen rögzített egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU százalékos kihasználtsága|Százalék|Maximum|SU százalékos kihasználtsága|LogicalName, PartitionId|
|InputEvents|Bemeneti események|Darabszám|Összesen|Bemeneti események|LogicalName, PartitionId|
|InputEventBytes|Bemeneti esemény bájtokban|Bájt|Összesen|Bemeneti esemény bájtokban|LogicalName, PartitionId|
|LateInputEvents|Késedelmes bemeneti események|Darabszám|Összesen|Késedelmes bemeneti események|LogicalName, PartitionId|
|OutputEvents|Kimeneti események|Darabszám|Összesen|Kimeneti események|LogicalName, PartitionId|
|ConversionErrors|Adatkonverziós hibák|Darabszám|Összesen|Adatkonverziós hibák|LogicalName, PartitionId|
|Hibák|Futásidejű hibák|Darabszám|Összesen|Futásidejű hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Megrendelési események|Darabszám|Összesen|Megrendelési események|LogicalName, PartitionId|
|AMLCalloutRequests|Függvénykérések|Darabszám|Összesen|Függvénykérések|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Sikertelen függvénykérések|Darabszám|Összesen|Sikertelen függvénykérések|LogicalName, PartitionId|
|AMLCalloutInputEvents|Függvényesemények|Darabszám|Összesen|Függvényesemények|LogicalName, PartitionId|
|DeserializationError|A deszerializálás bemeneti hibái|Darabszám|Összesen|A deszerializálás bemeneti hibái|LogicalName, PartitionId|
|EarlyInputEvents|Korai bemeneti események|Darabszám|Összesen|Korai bemeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Vízjel-késleltetés|másodperc|Maximum|Vízjel-késleltetés|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Várakozó bemeneti események|Darabszám|Maximum|Várakozó bemeneti események|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Fogadott bemeneti források|Darabszám|Összesen|Fogadott bemeneti források|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|Nincs dimenzió|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|Nincs dimenzió|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az összes eseményforrás által olvasott bájtok száma|Nincs dimenzió|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincs dimenzió|
|IngressStoredEvents|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincs dimenzió|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincs dimenzió|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenet sorszáma közötti különbség|Nincs dimenzió|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincs dimenzió|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincs dimenzió|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Darabszám|Összesen|Az eseményforrás által olvasott üzenetek száma|Nincs dimenzió|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az eseményforrás által beolvasott érvénytelen üzenetek száma|Nincs dimenzió|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az eseményforrás által beolvasott bájtok száma|Nincs dimenzió|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincs dimenzió|
|IngressStoredEvents|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincs dimenzió|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincs dimenzió|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenet sorszáma közötti különbség|Nincs dimenzió|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincs dimenzió|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincs dimenzió|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincs dimenzió|
|DiskWriteBytesPerSecond|Lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|Nincs dimenzió|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincs dimenzió|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|Nincs dimenzió|
|DiskReadOperations|Lemezes olvasási műveletek|Darabszám|Összesen|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincs dimenzió|
|DiskWriteOperations|Lemez írási műveletei|Darabszám|Összesen|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincs dimenzió|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincs dimenzió|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincs dimenzió|
|DiskReadLatency|Lemez olvasási késése|Ezredmásodperc|Átlag|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|Nincs dimenzió|
|DiskWriteLatency|Lemez írási késése|Ezredmásodperc|Átlag|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|Nincs dimenzió|
|NetworkInBytesPerSecond|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli sebessége.|Nincs dimenzió|
|NetworkOutBytesPerSecond|Hálózati kimenő bájtok/s|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli sebessége.|Nincs dimenzió|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A fogadott forgalom összes hálózati átviteli sebessége.|Nincs dimenzió|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A továbbított forgalom összes hálózati átviteli sebessége.|Nincs dimenzió|
|MemoryUsed|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt gépi memória mennyisége.|Nincs dimenzió|
|MemoryGranted|Megadott memória|Bájt|Átlag|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|Nincs dimenzió|
|MemoryActive|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|Nincs dimenzió|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|Nincs dimenzió|
|PercentageCpuReady|CPU-készültség százalékos aránya|Ezredmásodperc|Összesen|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|Nincs dimenzió|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvezérlő-várólista hossza|Darabszám|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Http-várólista hossza|Darabszám|Átlag|Http-várólista hossza|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (a függvények kivételével)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összesen|CPU-idő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Http 2xx|Darabszám|Összesen|Http 2xx|Példány|
|Http3xx|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Http 4xx|Darabszám|Összesen|Http 4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|AppConnections|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|Kezeli|Kezelők száma|Darabszám|Átlag|Kezelők száma|Példány|
|Szálak|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|PrivateBytes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|IoReadBytesPerSecond|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoWriteBytesPerSecond|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoOtherBytesPerSecond|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|RequestsInApplicationQueue|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|CurrentAssemblies|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|
|Gen0Collections|0\. generációs Garbage-gyűjtemények|Darabszám|Összesen|0\. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1\. generációs Garbage gyűjtemények|Darabszám|Összesen|1\. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2\. generációs Garbage gyűjtemények|Darabszám|Összesen|2\. generációs Garbage gyűjtemények|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (functions)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|Http5xx|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|FunctionExecutionUnits|Függvények végrehajtási egységei|MB/ezredmásodperc|Összesen|[Függvények végrehajtási egységei](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|FunctionExecutionCount|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|PrivateBytes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|IoReadBytesPerSecond|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoWriteBytesPerSecond|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoOtherBytesPerSecond|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|RequestsInApplicationQueue|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|CurrentAssemblies|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|
|Gen0Collections|0\. generációs Garbage-gyűjtemények|Darabszám|Összesen|0\. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1\. generációs Garbage gyűjtemények|Darabszám|Összesen|1\. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2\. generációs Garbage gyűjtemények|Darabszám|Összesen|2\. generációs Garbage gyűjtemények|Példány|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összesen|CPU-idő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Http 2xx|Darabszám|Összesen|Http 2xx|Példány|
|Http3xx|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Http 4xx|Darabszám|Összesen|Http 4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|FunctionExecutionUnits|Függvények végrehajtási egységei|Darabszám|Összesen|Függvények végrehajtási egységei|Példány|
|FunctionExecutionCount|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|AppConnections|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|Kezeli|Kezelők száma|Darabszám|Átlag|Kezelők száma|Példány|
|Szálak|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|PrivateBytes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|IoReadBytesPerSecond|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoWriteBytesPerSecond|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoOtherBytesPerSecond|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|RequestsInApplicationQueue|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|CurrentAssemblies|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|
|Gen0Collections|0\. generációs Garbage-gyűjtemények|Darabszám|Összesen|0\. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1\. generációs Garbage gyűjtemények|Darabszám|Összesen|1\. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2\. generációs Garbage gyűjtemények|Darabszám|Összesen|2\. generációs Garbage gyűjtemények|Példány|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Http 2xx|Darabszám|Összesen|Http 2xx|Példány|
|Http3xx|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Http 4xx|Darabszám|Összesen|Http 4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvezérlő-várólista hossza|Darabszám|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Http-várólista hossza|Darabszám|Átlag|Http-várólista hossza|Példány|
|ActiveRequests|Aktív kérések|Darabszám|Összesen|Aktív kérések|Példány|
|TotalFrontEnds|Összes előtér|Darabszám|Átlag|Összes előtér|Nincs dimenzió|
|SmallAppServicePlanInstances|Kisméretű App Service-csomag feldolgozói|Darabszám|Átlag|Kisméretű App Service-csomag feldolgozói|Nincs dimenzió|
|MediumAppServicePlanInstances|Közepes App Service-csomag feldolgozói|Darabszám|Átlag|Közepes App Service-csomag feldolgozói|Nincs dimenzió|
|LargeAppServicePlanInstances|Nagy App Service-csomag feldolgozói|Darabszám|Átlag|Nagy App Service-csomag feldolgozói|Nincs dimenzió|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WorkersTotal|Alkalmazottak összesen|Darabszám|Átlag|Alkalmazottak összesen|Nincs dimenzió|
|WorkersAvailable|Rendelkezésre álló munkavégzők|Darabszám|Átlag|Rendelkezésre álló munkavégzők|Nincs dimenzió|
|WorkersUsed|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|Nincs dimenzió|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|

## <a name="next-steps"></a>További lépések
* [Tudnivalók a Azure Monitor mérőszámokról](data-platform.md)
* [Riasztások létrehozása mérőszámokon](alerts-overview.md)
* [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](resource-logs-overview.md)
