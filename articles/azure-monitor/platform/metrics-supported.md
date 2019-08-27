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
ms.openlocfilehash: dbbe5a667b8d467b416e4a4a571d8d3599ec45b6
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051820"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák Azure Monitor

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. Alább látható az Azure Monitor metrikus folyamatával jelenleg elérhető metrikák teljes listája. Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők. Az alábbi lista csak a konszolidált Azure Monitor metrikai folyamat használatával elérhető metrikákat tartalmazza. A metrikák lekérdezéséhez és eléréséhez használja a [2018-01-01 API-Version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Az Event hub "bejövő üzenetek" mérőszáma megvizsgálható és sorba állítható a várólista-szinten. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|memory_metric|Memory (Memória)|Bájt|Average|Memória. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|TotalConnectionRequests|Kapcsolatkérelmek teljes száma|Count|Average|A kapcsolatkérelmek teljes száma. Ezek az érkezések.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|Egység/s|Average|A sikeres kapcsolatok gyakorisága.|ServerResourceType|
|TotalConnectionFailures|Összes sikertelen Kapcsolatfelvétel|Count|Average|A sikertelen csatlakozási kísérletek teljes száma.|ServerResourceType|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Count|Average|A létrejött felhasználói munkamenetek aktuális száma.|ServerResourceType|
|QueryPoolBusyThreads|Lekérdezési készlet foglalt szálai|Count|Average|A lekérdezési szál készletében lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolJobQueueLength|Parancssori feladatok várólistájának hossza|Count|Average|A parancsfájl-készlet várólistájában lévő feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Feldolgozási készlet nyomtatási várólistájának hossza|Count|Average|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|ServerResourceType|
|Összege|Kapcsolat: Aktuális kapcsolatok|Count|Average|A létesített ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CleanerCurrentPrice|Memória: Tisztító aktuális díja|Count|Average|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: Tisztító memória zsugorodó|Bájt|Average|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Nem zsugorodó tisztító memória|Bájt|Average|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|ServerResourceType|
|MemoryUsage|Memória: Memóriahasználat|Bájt|Average|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memória által leképezett adatmennyiséggel, figyelmen kívül hagyva azokat a memóriát, amelyeket a xVelocity memórián belüli elemzési motorja (VertiPaq) leképezett vagy lefoglalt, és amely meghaladja a xVelocity-memória korlátját.|ServerResourceType|
|MemoryLimitHard|Memória: Memória korlátozása – kemény|Bájt|Average|Rögzített memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Memória: Memória korlátja magas|Bájt|Average|Magas memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Memória: Kevés a memória korlátja|Bájt|Average|Kevés a memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: Memória korlátja VertiPaq|Bájt|Average|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|Kvóta|Memória: Kvóta|Bájt|Average|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|ServerResourceType|
|QuotaBlocked|Memória: Kvóta letiltva|Count|Average|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq nem lapozható|Bájt|Average|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq lapozható|Bájt|Average|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|ServerResourceType|
|RowsReadPerSec|Feldolgozási Olvasott sorok száma másodpercenként|Egység/s|Average|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|ServerResourceType|
|RowsConvertedPerSec|Feldolgozási Másodpercenként konvertált sorok száma|Egység/s|Average|A sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsWrittenPerSec|Feldolgozási Másodpercenként írt sorok száma|Egység/s|Average|A sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|CommandPoolBusyThreads|Szálak A parancssori készlet foglalt szálai|Count|Average|A parancsfájl-készletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Szálak Parancs-készlet üresjárati szálai|Count|Average|Az üresjárati szálak száma a parancs szálának készletében.|ServerResourceType|
|LongParsingBusyThreads|Szálak A foglalt szálak hosszú elemzése|Count|Average|A foglalt szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingIdleThreads|Szálak Hosszú távú elemzés – üresjárati szálak|Count|Average|Az üresjárati szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingJobQueueLength|Szálak Hosszú elemzési feladatok várólistájának hossza|Count|Average|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Szálak Feldolgozási készlet foglalt I/O-feladatok szálai|Count|Average|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Szálak Feldolgozási készlet elfoglalatlan nem I/O-szálai|Count|Average|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Szálak Feldolgozási készlet I/O-feladatok várólistájának hossza|Count|Average|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Szálak Feldolgozási készlet – üresjárati I/O-feladatok szálai|Count|Average|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Szálak Feldolgozási készlet üresjáratban lévő nem I/O-szálai|Count|Average|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Szálak Lekérdezési készlet üresjárati szálai|Count|Average|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|QueryPoolJobQueueLength|Szálak Lekérdezési készlet feladatok várólistájának hossza|Count|Average|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak Foglalt szálak rövid elemzése|Count|Average|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Szálak Rövid elemzési üresjárati szálak|Count|Average|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak Rövid elemzési feladatok várólistájának hossza|Count|Average|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memória-Kiverés|Percent|Average|Memória átlagos kiverése.|ServerResourceType|
|mashup_engine_qpu_metric|M motor QPU|Count|Average|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Average|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Count|Összes|Az átjáróra vonatkozó kérelmek teljes száma egy adott időszakban. A problémák diagnosztizálásához különféle méretek is feldarabolható. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Átjárók összes kérelme|Count|Összes|Az átjáróra vonatkozó kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, ezért ajánlott az új `Requests` metrika használata. |Hely, állomásnév|
|SuccessfulRequests|Sikeres átjáró-kérelmek|Count|Összes|A sikeres átjáró-kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, ezért ajánlott az új `Requests` metrika használata.|Hely, állomásnév|
|UnauthorizedRequests|Nem engedélyezett átjáróra vonatkozó kérelmek|Count|Összes| Egy adott időszakban nem engedélyezett átjáró-kérelmek teljes száma. Ez a metrika elavult, ezért ajánlott az új `Requests` metrika használata.|Hely, állomásnév|
|FailedRequests|Sikertelen átjáró-kérelmek|Count|Összes|A sikertelen átjáró-kérelmek teljes száma egy adott időszakban. Ez a metrika elavult, ezért ajánlott az új `Requests` metrika használata.|Hely, állomásnév|
|OtherRequests|Egyéb átjáró-kérelmek|Count|Összes|Egy adott időszakban a sikeres, jogosulatlan vagy sikertelen kategóriákba nem tartozó átjáró-kérelmek teljes száma. Ez a metrika elavult, ezért ajánlott az új `Requests` metrika használata. |Hely, állomásnév|
|Duration|Az átjárók kéréseinek teljes időtartama|Ezredmásodperc|Average|Az az idő, amikor a API Management egy ügyféltől érkező kérést kap, és amikor az ügyfél válaszát adja vissza.|Hely, állomásnév|
|Kapacitás|Kapacitás|Percent|Average|Egy API Management-példány terhelésének jelzője a megalapozott döntések meghozatala érdekében, hogy a példány méretét nagyobb terhelésre kell-e méretezni.|Location|
|EventHubTotalEvents|Összes EventHub esemény|Count|Összes|Egy adott időszakban API Management EventHub eljuttatott események teljes száma.|Location|
|EventHubSuccessfulEvents|Sikeres EventHub események|Count|Összes|A sikeres EventHub események teljes száma egy adott időszakban.|Location|
|EventHubTotalFailedEvents|Sikertelen EventHub események|Count|Összes|A sikertelen EventHub események teljes száma egy adott időszakban.|Location|
|EventHubRejectedEvents|EventHub-események visszautasítva|Count|Összes|Az elutasított EventHub-események (helytelen konfiguráció vagy jogosulatlan) teljes száma egy adott időszakban.|Location|
|EventHubThrottledEvents|Szabályozott EventHub események|Count|Összes|Egy adott időszakban a szabályozott EventHub események teljes száma.|Location|
|EventHubTimedoutEvents|Időtúllépés a EventHub eseményeinél|Count|Összes|Egy adott időszakon belül a EventHub események teljes száma.|Location|
|EventHubDroppedEvents|Eldobott EventHub események|Count|Összes|Egy adott időszakban a várólista-méretre vonatkozó korlát miatt kihagyott események teljes száma.|Location|
|EventHubTotalBytesSent|EventHub-események mérete|Bájt|Összes|A EventHub-események teljes mérete bájtban egy adott időszakban.|Location|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok összesen|Count|Összes|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentRuns|Összes frissítés központi telepítési futtatása|Count|Összes|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Összes frissítés központi telepítési gép futtatása|Count|Összes|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált mag száma|Count|Összes|A Batch-fiókban lévő dedikált magok teljes száma|Nincs dimenzió|
|TotalNodeCount|Dedikált csomópontok száma|Count|Összes|A Batch-fiókban lévő dedikált csomópontok teljes száma|Nincs dimenzió|
|LowPriorityCoreCount|LowPriority mag száma|Count|Összes|Az alacsony prioritású magok teljes száma a Batch-fiókban|Nincs dimenzió|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Count|Összes|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|Nincs dimenzió|
|CreatingNodeCount|Csomópontok számának létrehozása|Count|Összes|Létrehozandó csomópontok száma|Nincs dimenzió|
|StartingNodeCount|Csomópontok számának indítása|Count|Összes|Kiinduló csomópontok száma|Nincs dimenzió|
|WaitingForStartTaskNodeCount|Várakozás a feladat-csomópontok számának megkezdésére|Count|Összes|Az indítási tevékenység befejezésére váró csomópontok száma|Nincs dimenzió|
|StartTaskFailedNodeCount|Indítási feladat sikertelen csomópontok száma|Count|Összes|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|Nincs dimenzió|
|IdleNodeCount|Tétlen csomópontok száma|Count|Összes|Üresjárati csomópontok száma|Nincs dimenzió|
|OfflineNodeCount|Offline csomópontok száma|Count|Összes|Offline csomópontok száma|Nincs dimenzió|
|RebootingNodeCount|Csomópontok számának újraindítása|Count|Összes|Újraindítási csomópontok száma|Nincs dimenzió|
|ReimagingNodeCount|Csomópontok rendszerképének alaphelyzetbe állítása|Count|Összes|Rendszerkép-csomópontok száma|Nincs dimenzió|
|RunningNodeCount|Csomópontok száma|Count|Összes|Futó csomópontok száma|Nincs dimenzió|
|LeavingPoolNodeCount|A készlet-csomópontok számának elhagyása|Count|Összes|A készletet elhagyó csomópontok száma|Nincs dimenzió|
|UnusableNodeCount|Nem használható csomópontok száma|Count|Összes|Használhatatlan csomópontok száma|Nincs dimenzió|
|PreemptedNodeCount|Előzik-csomópontok száma|Count|Összes|Előzik-csomópontok száma|Nincs dimenzió|
|TaskStartEvent|Tevékenységek indítási eseményei|Count|Összes|Az elindított feladatok teljes száma|Nincs dimenzió|
|TaskCompleteEvent|Feladat teljes eseményei|Count|Összes|A Befejezett feladatok teljes száma|Nincs dimenzió|
|TaskFailEvent|Feladat sikertelen eseményei|Count|Összes|A meghiúsult állapotban Befejezett feladatok teljes száma|Nincs dimenzió|
|PoolCreateEvent|Készlet-létrehozási események|Count|Összes|A létrehozott készletek teljes száma|Nincs dimenzió|
|PoolResizeStartEvent|Készlet átméretezésének indítási eseményei|Count|Összes|A készlet elindított újraméretezésének teljes száma|Nincs dimenzió|
|PoolResizeCompleteEvent|Készlet átméretezése – befejezett események|Count|Összes|A készlet összes újraméretezésének száma|Nincs dimenzió|
|PoolDeleteStartEvent|Készlet törlése – indítási események|Count|Összes|Az elindított készlet-törlések száma összesen|Nincs dimenzió|
|PoolDeleteCompleteEvent|Készlet törlése – befejezett események|Count|Összes|A befejezett készlet-törlések teljes száma|Nincs dimenzió|
|JobDeleteCompleteEvent|Feladatok törlése – befejezett események|Count|Összes|A sikeresen törölt feladatok teljes száma.|Nincs dimenzió|
|JobDeleteStartEvent|Feladatok törlésének indítási eseményei|Count|Összes|A törölni kívánt feladatok teljes száma.|Nincs dimenzió|
|JobDisableCompleteEvent|A feladatok letiltják a teljes eseményeket|Count|Összes|A sikeresen letiltott feladatok teljes száma.|Nincs dimenzió|
|JobDisableStartEvent|Feladatok letiltásának indítási eseményei|Count|Összes|A letiltani kívánt feladatok teljes száma.|Nincs dimenzió|
|JobStartEvent|Feladatok indítási eseményei|Count|Összes|A sikeresen elindított feladatok teljes száma.|Nincs dimenzió|
|JobTerminateCompleteEvent|A feladatokhoz tartozó befejezett események befejezése|Count|Összes|A sikeresen leállított feladatok teljes száma.|Nincs dimenzió|
|JobTerminateStartEvent|Feladatokból indított események leállítása|Count|Összes|A leállítani kívánt feladatok teljes száma.|Nincs dimenzió|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakoztatott ügyfélrendszerek|Count|Maximum||ShardId|
|totalcommandsprocessed|Műveletek összesen|Count|Összes||ShardId|
|cachehits|Gyorsítótárbeli találatok|Count|Összes||ShardId|
|cachemisses|Gyorsítótárbeli tévesztések|Count|Összes||ShardId|
|getcommands|Lekérdezések|Count|Összes||ShardId|
|setcommands|Beállítások|Count|Összes||ShardId|
|operationsPerSecond|Műveletek száma másodpercenként|Count|Maximum||ShardId|
|evictedkeys|Kizárt kulcsok|Count|Összes||ShardId|
|totalkeys|Kulcsok összesen|Count|Maximum||ShardId|
|expiredkeys|Lejárt kulcsok|Count|Összes||ShardId|
|usedmemory|Felhasznált memória|Bájt|Maximum||ShardId|
|usedmemorypercentage|Felhasznált memória százalékos aránya|Percent|Maximum||ShardId|
|usedmemoryRss|Felhasznált memória RSS-címe|Bájt|Maximum||ShardId|
|serverLoad|Kiszolgáló-terhelés|Percent|Maximum||ShardId|
|cacheWrite|Gyorsítótár-írás|BytesPerSecond|Maximum||ShardId|
|cacheRead|Gyorsítótár-olvasás|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Percent|Maximum||ShardId|
|cacheLatency|Gyorsítótár késési másodpercek (előzetes verzió)|Count|Average||ShardId, SampleType|
|hiba|Hibák|Count|Maximum||ShardId, ErrorType|
|connectedclients0|Csatlakoztatott ügyfelek (0. szegmens)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed0|Összes művelet (0. szegmens)|Count|Összes||Nincs dimenzió|
|cachehits0|Gyorsítótárbeli találatok (szegmens 0)|Count|Összes||Nincs dimenzió|
|cachemisses0|Gyorsítótár-kihagyás (0. szegmens)|Count|Összes||Nincs dimenzió|
|getcommands0|Beolvasás (0. szegmens)|Count|Összes||Nincs dimenzió|
|setcommands0|Készletek (0. szegmens)|Count|Összes||Nincs dimenzió|
|operationsPerSecond0|Művelet/másodperc (0. Szilánk)|Count|Maximum||Nincs dimenzió|
|evictedkeys0|Kizárt kulcsok (0. szegmens)|Count|Összes||Nincs dimenzió|
|totalkeys0|Összes kulcs (szilánk 0)|Count|Maximum||Nincs dimenzió|
|expiredkeys0|Lejárt kulcsok (szilánk 0)|Count|Összes||Nincs dimenzió|
|usedmemory0|Felhasznált memória (0. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss0|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad0|Kiszolgáló terhelése (0. szegmens)|Percent|Maximum||Nincs dimenzió|
|cacheWrite0|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead0|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime0|CPU (0. szegmens)|Percent|Maximum||Nincs dimenzió|
|connectedclients1|Csatlakoztatott ügyfelek (1. szegmens)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed1|Összes művelet (1. szegmens)|Count|Összes||Nincs dimenzió|
|cachehits1|Gyorsítótárbeli találatok (1. Szilánk)|Count|Összes||Nincs dimenzió|
|cachemisses1|Gyorsítótár-lemaradás (1. szegmens)|Count|Összes||Nincs dimenzió|
|getcommands1|Beolvasás (1. szegmens)|Count|Összes||Nincs dimenzió|
|setcommands1|Készletek (1. szegmens)|Count|Összes||Nincs dimenzió|
|operationsPerSecond1|Művelet/másodperc (1. szegmens)|Count|Maximum||Nincs dimenzió|
|evictedkeys1|Kizárt kulcsok (1. Szilánk)|Count|Összes||Nincs dimenzió|
|totalkeys1|Összes kulcs (1. szegmens)|Count|Maximum||Nincs dimenzió|
|expiredkeys1|Lejárt kulcsok (1. szegmens)|Count|Összes||Nincs dimenzió|
|usedmemory1|Felhasznált memória (1. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss1|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad1|Kiszolgáló terhelése (1. szegmens)|Percent|Maximum||Nincs dimenzió|
|cacheWrite1|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead1|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime1|CPU (1. Szilánk)|Percent|Maximum||Nincs dimenzió|
|connectedclients2|Csatlakoztatott ügyfelek (2. szegmens)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed2|Összes művelet (2. szegmens)|Count|Összes||Nincs dimenzió|
|cachehits2|Gyorsítótárbeli találatok (2. szegmens)|Count|Összes||Nincs dimenzió|
|cachemisses2|Gyorsítótár-lemaradás (2. szegmens)|Count|Összes||Nincs dimenzió|
|getcommands2|Beolvasás (2. szegmens)|Count|Összes||Nincs dimenzió|
|setcommands2|Készletek (2. szegmens)|Count|Összes||Nincs dimenzió|
|operationsPerSecond2|Művelet/másodperc (2. szegmens)|Count|Maximum||Nincs dimenzió|
|evictedkeys2|Kizárt kulcsok (2. szegmens)|Count|Összes||Nincs dimenzió|
|totalkeys2|Összes kulcs (2. szegmens)|Count|Maximum||Nincs dimenzió|
|expiredkeys2|Lejárt kulcsok (2. szegmens)|Count|Összes||Nincs dimenzió|
|usedmemory2|Felhasznált memória (2. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss2|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad2|Kiszolgáló betöltése (2. szegmens)|Percent|Maximum||Nincs dimenzió|
|cacheWrite2|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead2|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime2|CPU (2. szegmens)|Percent|Maximum||Nincs dimenzió|
|connectedclients3|Csatlakoztatott ügyfelek (3. Szilánk)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed3|Összes művelet (3. szegmens)|Count|Összes||Nincs dimenzió|
|cachehits3|Gyorsítótárbeli találatok (3. Szilánk)|Count|Összes||Nincs dimenzió|
|cachemisses3|Gyorsítótár-lemaradás (3. szegmens)|Count|Összes||Nincs dimenzió|
|getcommands3|Beolvasás (3. szegmens)|Count|Összes||Nincs dimenzió|
|setcommands3|Készletek (3. szegmens)|Count|Összes||Nincs dimenzió|
|operationsPerSecond3|Művelet/másodperc (3. szegmens)|Count|Maximum||Nincs dimenzió|
|evictedkeys3|Kizárt kulcsok (3. Szilánk)|Count|Összes||Nincs dimenzió|
|totalkeys3|Összes kulcs (3. Szilánk)|Count|Maximum||Nincs dimenzió|
|expiredkeys3|Lejárt kulcsok (3. Szilánk)|Count|Összes||Nincs dimenzió|
|usedmemory3|Felhasznált memória (3. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss3|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad3|Kiszolgáló terhelése (3. Szilánk)|Percent|Maximum||Nincs dimenzió|
|cacheWrite3|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead3|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime3|CPU (3. Szilánk)|Percent|Maximum||Nincs dimenzió|
|connectedclients4|Csatlakoztatott ügyfelek (4. Szilánk)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed4|Összes művelet (4. Szilánk)|Count|Összes||Nincs dimenzió|
|cachehits4|Gyorsítótár-találatok (4. Szilánk)|Count|Összes||Nincs dimenzió|
|cachemisses4|Gyorsítótár-lemaradás (4. szegmens)|Count|Összes||Nincs dimenzió|
|getcommands4|Beolvasás (4. Szilánk)|Count|Összes||Nincs dimenzió|
|setcommands4|Készletek (4. Szilánk)|Count|Összes||Nincs dimenzió|
|operationsPerSecond4|Művelet/másodperc (4. szegmens)|Count|Maximum||Nincs dimenzió|
|evictedkeys4|Kizárt kulcsok (4. Szilánk)|Count|Összes||Nincs dimenzió|
|totalkeys4|Összes kulcs (4. Szilánk)|Count|Maximum||Nincs dimenzió|
|expiredkeys4|Lejárt kulcsok (4. Szilánk)|Count|Összes||Nincs dimenzió|
|usedmemory4|Felhasznált memória (4. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss4|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|serverLoad4|Kiszolgáló terhelése (4. Szilánk)|Percent|Maximum||Nincs dimenzió|
|cacheWrite4|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead4|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime4|CPU (4. Szilánk)|Percent|Maximum||Nincs dimenzió|
|connectedclients5|Csatlakoztatott ügyfelek (5. Szilánk)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed5|Összes művelet (5. Szilánk)|Count|Összes||Nincs dimenzió|
|cachehits5|Gyorsítótárbeli találatok (szegmens 5)|Count|Összes||Nincs dimenzió|
|cachemisses5|Gyorsítótár-lemaradás (5. szegmens)|Count|Összes||Nincs dimenzió|
|getcommands5|Beolvasás (szegmens 5)|Count|Összes||Nincs dimenzió|
|setcommands5|Készletek (szilánk 5)|Count|Összes||Nincs dimenzió|
|operationsPerSecond5|Művelet/másodperc (5. szegmens)|Count|Maximum||Nincs dimenzió|
|evictedkeys5|Kizárt kulcsok (5. Szilánk)|Count|Összes||Nincs dimenzió|
|totalkeys5|Összes kulcs (5. szegmens)|Count|Maximum||Nincs dimenzió|
|expiredkeys5|Lejárt kulcsok (szilánk 5)|Count|Összes||Nincs dimenzió|
|usedmemory5|Felhasznált memória (5. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss5|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad5|Kiszolgáló terhelése (5. Szilánk)|Percent|Maximum||Nincs dimenzió|
|cacheWrite5|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead5|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime5|CPU (5. Szilánk)|Percent|Maximum||Nincs dimenzió|
|connectedclients6|Csatlakoztatott ügyfelek (6. szegmens)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed6|Összes művelet (szegmens 6)|Count|Összes||Nincs dimenzió|
|cachehits6|Gyorsítótárbeli találatok (szilánk 6)|Count|Összes||Nincs dimenzió|
|cachemisses6|Gyorsítótár-lemaradás (szegmens 6)|Count|Összes||Nincs dimenzió|
|getcommands6|Beolvasás (szilánk 6)|Count|Összes||Nincs dimenzió|
|setcommands6|Készletek (szilánk 6)|Count|Összes||Nincs dimenzió|
|operationsPerSecond6|Művelet/másodperc (szegmens 6)|Count|Maximum||Nincs dimenzió|
|evictedkeys6|Kizárt kulcsok (szegmens 6)|Count|Összes||Nincs dimenzió|
|totalkeys6|Összes kulcs (szilánk 6)|Count|Maximum||Nincs dimenzió|
|expiredkeys6|Lejárt kulcsok (szilánk 6)|Count|Összes||Nincs dimenzió|
|usedmemory6|Felhasznált memória (6. szegmens)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss6|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad6|Kiszolgáló betöltése (szegmens 6)|Percent|Maximum||Nincs dimenzió|
|cacheWrite6|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead6|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime6|CPU (szegmens 6)|Percent|Maximum||Nincs dimenzió|
|connectedclients7|Csatlakoztatott ügyfelek (7. szegmens)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed7|Összes művelet (7. szegmens)|Count|Összes||Nincs dimenzió|
|cachehits7|Gyorsítótárbeli találatok (szegmens 7)|Count|Összes||Nincs dimenzió|
|cachemisses7|Gyorsítótár-lemaradás (7. szegmens)|Count|Összes||Nincs dimenzió|
|getcommands7|Beolvasás (szegmens 7)|Count|Összes||Nincs dimenzió|
|setcommands7|Készletek (szegmens 7)|Count|Összes||Nincs dimenzió|
|operationsPerSecond7|Művelet/másodperc (7. szegmens)|Count|Maximum||Nincs dimenzió|
|evictedkeys7|Kizárt kulcsok (7. szegmens)|Count|Összes||Nincs dimenzió|
|totalkeys7|Összes kulcs (szegmens 7)|Count|Maximum||Nincs dimenzió|
|expiredkeys7|Lejárt kulcsok (7. szegmens)|Count|Összes||Nincs dimenzió|
|usedmemory7|Felhasznált memória (7. szegmens)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss7|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||Nincs dimenzió|
|serverLoad7|Kiszolgáló terhelése (7. szegmens)|Percent|Maximum||Nincs dimenzió|
|cacheWrite7|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead7|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime7|PROCESSZOR (7. szegmens)|Percent|Maximum||Nincs dimenzió|
|connectedclients8|Csatlakoztatott ügyfelek (10. szegmens)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed8|Összes művelet (szegmens 8)|Count|Összes||Nincs dimenzió|
|cachehits8|Gyorsítótárbeli találatok (szilánk 8)|Count|Összes||Nincs dimenzió|
|cachemisses8|Gyorsítótár-lemaradás (szegmens 8)|Count|Összes||Nincs dimenzió|
|getcommands8|Beolvasás (szilánk 8)|Count|Összes||Nincs dimenzió|
|setcommands8|Készletek (szilánk 8)|Count|Összes||Nincs dimenzió|
|operationsPerSecond8|Művelet/másodperc (szegmens 8)|Count|Maximum||Nincs dimenzió|
|evictedkeys8|Kizárt kulcsok (szilánk 8)|Count|Összes||Nincs dimenzió|
|totalkeys8|Összes kulcs (szilánk 8)|Count|Maximum||Nincs dimenzió|
|expiredkeys8|Lejárt kulcsok (szilánk 8)|Count|Összes||Nincs dimenzió|
|usedmemory8|Felhasznált memória (8. Szilánk)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss8|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||Nincs dimenzió|
|serverLoad8|Kiszolgáló betöltése (szegmens 8)|Percent|Maximum||Nincs dimenzió|
|cacheWrite8|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead8|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime8|CPU (szegmens 8)|Percent|Maximum||Nincs dimenzió|
|connectedclients9|Csatlakoztatott ügyfelek (10. szegmens)|Count|Maximum||Nincs dimenzió|
|totalcommandsprocessed9|Összes művelet (szegmens 9)|Count|Összes||Nincs dimenzió|
|cachehits9|Gyorsítótárbeli találatok (szilánk 9)|Count|Összes||Nincs dimenzió|
|cachemisses9|Gyorsítótár-lemaradás (szegmens 9)|Count|Összes||Nincs dimenzió|
|getcommands9|Beolvasás (szegmens 9)|Count|Összes||Nincs dimenzió|
|setcommands9|Készletek (szilánk 9)|Count|Összes||Nincs dimenzió|
|operationsPerSecond9|Művelet/másodperc (szegmens 9)|Count|Maximum||Nincs dimenzió|
|evictedkeys9|Kizárt kulcsok (szilánk 9)|Count|Összes||Nincs dimenzió|
|totalkeys9|Összes kulcs (szilánk 9)|Count|Maximum||Nincs dimenzió|
|expiredkeys9|Lejárt kulcsok (szilánk 9)|Count|Összes||Nincs dimenzió|
|usedmemory9|Felhasznált memória (10. szegmens)|Bájt|Maximum||Nincs dimenzió|
|usedmemoryRss9|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||Nincs dimenzió|
|serverLoad9|Kiszolgáló terhelése (szegmens 9)|Percent|Maximum||Nincs dimenzió|
|cacheWrite9|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||Nincs dimenzió|
|cacheRead9|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||Nincs dimenzió|
|percentProcessorTime9|CPU (szilánk 9)|Percent|Maximum||Nincs dimenzió|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Average|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|Nincs dimenzió|
|Lemez olvasási sebessége (bájt/s)|Lemezolvasás|BytesPerSecond|Average|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|Nincs dimenzió|
|Lemez írási sebessége (bájt/s)|Lemezírás|BytesPerSecond|Average|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|Nincs dimenzió|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|Egység/s|Average|Lemez olvasása IOPS.|Nincs dimenzió|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|Egység/s|Average|Lemez írása IOPS.|Nincs dimenzió|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Average|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Lemez olvasási sebessége (bájt/s)|Lemezolvasás|BytesPerSecond|Average|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|Lemezírás|BytesPerSecond|Average|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|Egység/s|Average|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|Egység/s|Average|Lemez írása IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalCalls|Összes hívás|Count|Összes|A hívások száma összesen|ApiName, OperationName, régió|
|SuccessfulCalls|Sikeres hívások|Count|Összes|A sikeres hívások száma.|ApiName, OperationName, régió|
|TotalErrors|Összes hiba|Count|Összes|A hibaüzenettel rendelkező hívások teljes száma (HTTP-válasz kódja 4xx vagy 5xx).|ApiName, OperationName, régió|
|BlockedCalls|Blokkolt hívások|Count|Összes|Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|ApiName, OperationName, régió|
|Kiszolgálóhibái|Kiszolgálóhibák|Count|Összes|A szolgáltatás belső hibája miatti hívások száma (HTTP-válasz kódja 5xx).|ApiName, OperationName, régió|
|ClientErrors|Ügyfélhibák|Count|Összes|Ügyféloldali hibával rendelkező hívások száma (HTTP-válasz kódja 4xx).|ApiName, OperationName, régió|
|DataIn|Bejövő adatforgalom|Bájt|Összes|A bejövő adat mérete bájtban megadva.|ApiName, OperationName, régió|
|DataOut|Kimenő adatforgalom|Bájt|Összes|A kimenő adat mérete bájtban kifejezve.|ApiName, OperationName, régió|
|Késés|Késés|Ezredmásodpercben|Average|Késés ezredmásodpercben.|ApiName, OperationName, régió|
|CharactersTranslated|Lefordított karakterek|Count|Összes|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTrained|Betanított karakterek|Count|Összes|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|SpeechSessionDuration|Beszédfelismerési munkamenet időtartama|Másodperc|Összes|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|TotalTransactions|Tranzakciók száma|Count|Összes|A tranzakciók száma összesen|Nincs dimenzió|
|TotalTokenCalls|Jogkivonathívások száma|Count|Összes|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Számlázható bejövő hálózati forgalom|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom)|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Számlázható kimenő hálózati forgalom|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren kimenő számlázható bájtok száma (kimenő forgalom)|Nincs dimenzió|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|Nincs dimenzió|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|A megfigyelési időszak során lemezre írt bájtok|Nincs dimenzió|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|Egység/s|Average|Lemezolvasási I/O-műveletek|Nincs dimenzió|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|Egység/s|Average|Lemezre írási I/O-műveletek|Nincs dimenzió|
|Fennmaradó processzorkreditek|Fennmaradó processzorkreditek|Count|Average|A feltört kreditek teljes száma|Nincs dimenzió|
|Felhasznált processzorkreditek|Felhasznált processzorkreditek|Count|Average|A virtuális gép által felhasznált kreditek teljes száma|Nincs dimenzió|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|Egység/s|Average|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Tárolóhely azonosítója|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|Egység/s|Average|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|Tárolóhely azonosítója|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|Egység/s|Average|IOPS beolvasása egyetlen lemezről a figyelési időszakban|Tárolóhely azonosítója|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|Egység/s|Average|IOPS írása egyetlen lemezről a figyelési időszakban|Tárolóhely azonosítója|
|Várólista lemezenkénti mélysége|Adatlemez QD (elavult)|Count|Average|Adatlemez várólistájának mélysége (vagy várólista hossza)|Tárolóhely azonosítója|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|Egység/s|Average|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincs dimenzió|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|Egység/s|Average|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincs dimenzió|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|Egység/s|Average|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|Egység/s|Average|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|Lemez várólistájának operációs rendszerenkénti mélysége|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Count|Average|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincs dimenzió|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|Egység/s|Average|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LOGIKAI EGYSÉG|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|Egység/s|Average|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LOGIKAI EGYSÉG|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|Egység/s|Average|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LOGIKAI EGYSÉG|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|Egység/s|Average|IOPS írása egyetlen lemezről a figyelési időszakban|LOGIKAI EGYSÉG|
|Adatlemez várólistájának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Count|Average|Adatlemez várólistájának mélysége (vagy várólista hossza)|LOGIKAI EGYSÉG|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|Egység/s|Average|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincs dimenzió|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|Egység/s|Average|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincs dimenzió|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|Egység/s|Average|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|Egység/s|Average|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|OS-lemez várakozási sorának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Count|Average|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincs dimenzió|
|Bejövő forgalomfolyamok|Bejövő folyamatok (előzetes verzió)|Count|Average|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincs dimenzió|
|Kimenő forgalomfolyamok|Kimenő folyamatok (előzetes verzió)|Count|Average|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincs dimenzió|
|Bejövő forgalomfolyamok maximális létrehozási gyakorisága|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|Egység/s|Average|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincs dimenzió|
|Kimenő forgalomfolyamok maximális létrehozási gyakorisága|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|Egység/s|Average|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincs dimenzió|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Percent|Average|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LOGIKAI EGYSÉG|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Percent|Average|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LOGIKAI EGYSÉG|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Percent|Average|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincs dimenzió|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Percent|Average|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincs dimenzió|
|Összes bejövő hálózati forgalom|Összes bejövő hálózati forgalom|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincs dimenzió|
|Összes kimenő hálózati forgalom|Összes kimenő hálózati forgalom|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincs dimenzió|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Hálózat bejövő adatforgalma|Számlázható bejövő hálózati forgalom|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázható bájtok száma (bejövő forgalom)|VMName|
|Hálózat kimenő adatforgalma|Számlázható kimenő hálózati forgalom|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren kimenő számlázható bájtok száma (kimenő forgalom)|VMName|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|Egység/s|Average|Lemezolvasási I/O-műveletek|VMName|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|Egység/s|Average|Lemezre írási I/O-műveletek|VMName|
|Fennmaradó processzorkreditek|Fennmaradó processzorkreditek|Count|Average|A feltört kreditek teljes száma|Nincs dimenzió|
|Felhasznált processzorkreditek|Felhasznált processzorkreditek|Count|Average|A virtuális gép által felhasznált kreditek teljes száma|Nincs dimenzió|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|Egység/s|Average|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Tárolóhely azonosítója|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|Egység/s|Average|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|Tárolóhely azonosítója|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|Egység/s|Average|IOPS beolvasása egyetlen lemezről a figyelési időszakban|Tárolóhely azonosítója|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|Egység/s|Average|IOPS írása egyetlen lemezről a figyelési időszakban|Tárolóhely azonosítója|
|Várólista lemezenkénti mélysége|Adatlemez QD (elavult)|Count|Average|Adatlemez várólistájának mélysége (vagy várólista hossza)|Tárolóhely azonosítója|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|Egység/s|Average|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincs dimenzió|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|Egység/s|Average|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincs dimenzió|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|Egység/s|Average|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|Egység/s|Average|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincs dimenzió|
|Lemez várólistájának operációs rendszerenkénti mélysége|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Count|Average|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincs dimenzió|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|Egység/s|Average|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|Egység/s|Average|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|Egység/s|Average|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|Egység/s|Average|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez várólistájának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Count|Average|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|Egység/s|Average|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|Egység/s|Average|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|Egység/s|Average|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|Egység/s|Average|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OS-lemez várakozási sorának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Count|Average|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|Bejövő forgalomfolyamok|Bejövő folyamatok (előzetes verzió)|Count|Average|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő forgalomfolyamok|Kimenő folyamatok (előzetes verzió)|Count|Average|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Bejövő forgalomfolyamok maximális létrehozási gyakorisága|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|Egység/s|Average|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő forgalomfolyamok maximális létrehozási gyakorisága|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|Egység/s|Average|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Percent|Average|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Percent|Average|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Percent|Average|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Percent|Average|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|
|Összes bejövő hálózati forgalom|Összes bejövő hálózati forgalom|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Összes kimenő hálózati forgalom|Összes kimenő hálózati forgalom|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|CPU-használat|Count|Average|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Memóriahasználat|Bájt|Average|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Bejövő hálózati forgalom (bájt/s)|Bájt|Average|A másodpercenként fogadott hálózati bájtok száma.|Nincs dimenzió|
|NetworkBytesTransmittedPerSecond|Kimenő hálózati forgalom (bájt/s)|Bájt|Average|A másodpercenként továbbított hálózati bájtok száma.|Nincs dimenzió|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalPullCount|Lekérések száma összesen|Count|Average|A lekérések száma összesen|Nincs dimenzió|
|SuccessfulPullCount|Sikeres lekérések száma|Count|Average|Sikeres Képkeresések száma|Nincs dimenzió|
|TotalPushCount|Leküldések száma összesen|Count|Average|Leküldések száma összesen|Nincs dimenzió|
|SuccessfulPushCount|Sikeres leküldések száma|Count|Average|Sikeres leküldések száma|Nincs dimenzió|
|RunDuration|Futtatás időtartama|Ezredmásodperc|Összes|Futtatás időtartama (ezredmásodpercben)|Nincs dimenzió|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Count|Összes|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Nincs dimenzió|
|kube_node_status_allocatable_memory_bytes|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Összes|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Nincs dimenzió|
|kube_pod_status_ready|A hüvelyek száma üzemkész állapotban|Count|Összes|A hüvelyek száma üzemkész állapotban|névtér, Pod|
|kube_node_status_condition|Különböző csomóponti feltételek állapota|Count|Összes|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|Hüvelyek száma fázis szerint|Count|Összes|Hüvelyek száma fázis szerint|fázis, névtér, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DCIApiCalls|API-hívások Customer Insights|Count|Összes||Nincs dimenzió|
|DCIMappingImportOperationSuccessfulLines|Az importálási művelet sikeres sorainak leképezése|Count|Összes||Nincs dimenzió|
|DCIMappingImportOperationFailedLines|Az importálási művelet sikertelen sorainak megfeleltetése|Count|Összes||Nincs dimenzió|
|DCIMappingImportOperationTotalLines|Az importálási művelet összes sorának leképezése|Count|Összes||Nincs dimenzió|
|DCIMappingImportOperationRuntimeInSeconds|Az importálási művelet futtatókörnyezetének leképezése másodpercben|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundProfileExportSucceeded|A kimenő profil exportálása sikerült|Count|Összes||Nincs dimenzió|
|DCIOutboundProfileExportFailed|A kimenő profil exportálása nem sikerült|Count|Összes||Nincs dimenzió|
|DCIOutboundProfileExportDuration|Kimenő profil exportálásának időtartama|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiExportSucceeded|A kimenő KPI exportálása sikerült|Count|Összes||Nincs dimenzió|
|DCIOutboundKpiExportFailed|A kimenő KPI exportálása nem sikerült|Count|Összes||Nincs dimenzió|
|DCIOutboundKpiExportDuration|Kimenő KPI-exportálás időtartama|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiExportStarted|Kimenő KPI-exportálás elindítva|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundKpiRecordCount|Kimenő KPI-rekordok száma|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundProfileExportCount|Kimenő profilok exportálásának száma|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportFailed|A kimenő kezdeti profil exportálása nem sikerült|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportSucceeded|A kimenő kezdeti profil exportálása sikerült|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialKpiExportFailed|A kimenő kezdeti KPI-exportálás nem sikerült|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialKpiExportSucceeded|A kimenő kezdeti KPI-exportálás sikerült|Másodperc|Összes||Nincs dimenzió|
|DCIOutboundInitialProfileExportDurationInSeconds|Kimenő kezdeti profil exportálásának időtartama másodpercben|Másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiFailed|A standard KPI ADla-feladata másodpercek alatt meghiúsult|Másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiTimeOut|ADla-feladatot a standard KPI-időkorlát másodpercben|Másodperc|Összes||Nincs dimenzió|
|AdlaJobForStandardKpiCompleted|A standard KPI ADla-feladata másodpercek alatt befejeződött|Másodperc|Összes||Nincs dimenzió|
|ImportASAValuesFailed|Nem sikerült importálni az ASA-értékeket|Count|Összes||Nincs dimenzió|
|ImportASAValuesSucceeded|Az ASA-értékek importálása sikerült|Count|Összes||Nincs dimenzió|
|DCIProfilesCount|Profil példányainak száma|Count|Vezetéknév||Nincs dimenzió|
|DCIInteractionsPerMonthCount|Interakciók havonta – darabszám|Count|Vezetéknév||Nincs dimenzió|
|DCIKpisCount|KPI-darabszám|Count|Vezetéknév||Nincs dimenzió|
|DCISegmentsCount|Szegmensek száma|Count|Vezetéknév||Nincs dimenzió|
|DCIPredictiveMatchPoliciesCount|Prediktív egyezések száma|Count|Vezetéknév||Nincs dimenzió|
|DCIPredictionsCount|Előrejelzési szám|Count|Vezetéknév||Nincs dimenzió|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|NICReadThroughput|Olvasási sebesség (hálózat)|BytesPerSecond|Average|Az eszközön lévő hálózati adapter olvasási átviteli sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|NICWriteThroughput|Írási átviteli sebesség (hálózat)|BytesPerSecond|Average|Az eszközön lévő hálózati adapter írási sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|CloudReadThroughputPerShare|Felhőbeli letöltési teljesítmény (megosztás)|BytesPerSecond|Average|Az Azure-ba való letöltési átviteli sebesség a jelentési időszak alatt.|Megosztás|
|CloudUploadThroughputPerShare|Felhőbeli feltöltési sebesség (megosztás)|BytesPerSecond|Average|Az Azure-ba való feltöltési átviteli sebesség a jelentési időszak során.|Megosztás|
|BytesUploadedToCloudPerShare|Felhőbeli feltöltött bájtok (megosztás)|Bájt|Average|A jelentési időszak során az Azure-ba feltöltött bájtok teljes száma.|Megosztás|
|TotalCapacity|Teljes kapacitás|Bájt|Average|Teljes kapacitás|Nincs dimenzió|
|Availablecapacity;)|Használható kapacitás|Bájt|Average|A rendelkezésre álló kapacitás bájtban a jelentési időszak alatt.|Nincs dimenzió|
|CloudUploadThroughput|Felhő feltöltési átviteli sebessége|BytesPerSecond|Average|A Felhőbeli feltöltési sebesség az Azure-ba a jelentési időszak alatt.|Nincs dimenzió|
|CloudReadThroughput|Felhőbeli letöltési sebesség|BytesPerSecond|Average|A felhő letöltési sebessége az Azure-ba a jelentési időszak alatt.|Nincs dimenzió|
|BytesUploadedToCloud|Feltöltött Felhőbeli bájtok (eszköz)|Bájt|Average|Az Azure-ba az eszközről a jelentési időszakban feltöltött bájtok teljes száma.|Nincs dimenzió|
|HyperVVirtualProcessorUtilization|Edge-számítás – százalékos CPU|Percent|Average|Processzorhasználat (%)|InstanceName|
|HyperVMemoryUtilization|Edge-számítás – memóriahasználat|Percent|Average|Felhasznált memória mennyisége|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Sikertelen futtatások|Count|Összes||pipelineName, activityName|
|SuccessfulRuns|Sikeres futtatások|Count|Összes||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Sikertelen folyamat-futtatási metrikák|Count|Összes||FailureType, név|
|PipelineSucceededRuns|A folyamat sikeresen futtatja a metrikákat|Count|Összes||FailureType, név|
|ActivityFailedRuns|Sikertelen tevékenység-futtatási metrikák|Count|Összes||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|A sikeres tevékenység metrikákat futtat|Count|Összes||ActivityType, PipelineName, FailureType, név|
|TriggerFailedRuns|Sikertelen trigger-futtatási metrikák|Count|Összes||Név, FailureType|
|TriggerSucceededRuns|A sikeres trigger metrikákat futtat|Count|Összes||Név, FailureType|
|IntegrationRuntimeCpuPercentage|Integration Runtime CPU-kihasználtsága|Percent|Average||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableMemory|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Average||IntegrationRuntimeName, csomópontnév|
|MaxAllowedResourceCount|Engedélyezett entitások maximális száma|Count|Maximum||Nincs dimenzió|
|MaxAllowedFactorySizeInGbUnits|Maximálisan engedélyezett gyári méret (GB egység)|Count|Maximum||Nincs dimenzió|
|ResourceCount|Entitások száma összesen|Count|Maximum||Nincs dimenzió|
|FactorySizeInGbUnits|Gyári méret összesen (GB egység)|Count|Maximum||Nincs dimenzió|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Count|Összes|A sikeres feladatok száma.|Nincs dimenzió|
|JobEndedFailure|Sikertelen feladatok|Count|Összes|Sikertelen feladatok száma.|Nincs dimenzió|
|JobEndedCancelled|Megszakított feladatok|Count|Összes|Megszakított feladatok száma.|Nincs dimenzió|
|JobAUEndedSuccess|Sikeres AU-idő|Másodperc|Összes|A sikeres feladatok teljes AU-ideje.|Nincs dimenzió|
|JobAUEndedFailure|Sikertelen AU-idő|Másodperc|Összes|A sikertelen feladatok összes AU-ideje.|Nincs dimenzió|
|JobAUEndedCancelled|Megszakított AU-idő|Másodperc|Összes|A megszakított feladatok összes AU-ideje.|Nincs dimenzió|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|Nincs dimenzió|
|DataWritten|Kiírt adatok|Bájt|Összes|A fiókba írt összes adatmennyiség.|Nincs dimenzió|
|DataRead|Olvasott adatok|Bájt|Összes|A fiókból beolvasott adatok teljes mennyisége.|Nincs dimenzió|
|WriteRequests|Írási kérelmek|Count|Összes|A fiókra vonatkozó adatírási kérelmek száma.|Nincs dimenzió|
|ReadRequests|Olvasási kérelmek|Count|Összes|A fióknak küldött adatolvasási kérelmek száma.|Nincs dimenzió|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Percent|Average|CPU-százalék|Nincs dimenzió|
|memory_percent|Memória százaléka|Percent|Average|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százalék|Percent|Average|IO-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Percent|Average|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tároló|Bájt|Average|Felhasznált tároló|Nincs dimenzió|
|storage_limit|Tárolási korlát|Bájt|Average|Tárolási korlát|Nincs dimenzió|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Percent|Average|Kiszolgáló naplójának tárolási százaléka|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Average|Kiszolgáló naplójának tárolója|Nincs dimenzió|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Average|Kiszolgáló naplójának tárolási korlátja|Nincs dimenzió|
|active_connections|Aktív kapcsolatok|Count|Average|Aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Count|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|seconds_behind_master|Replikálás késése másodpercben|Count|Average|Replikálás késése másodpercben|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Average|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Percent|Average|CPU-százalék|Nincs dimenzió|
|memory_percent|Memória százaléka|Percent|Average|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százalék|Percent|Average|IO-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Percent|Average|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tároló|Bájt|Average|Felhasznált tároló|Nincs dimenzió|
|storage_limit|Tárolási korlát|Bájt|Average|Tárolási korlát|Nincs dimenzió|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Percent|Average|Kiszolgáló naplójának tárolási százaléka|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Average|Kiszolgáló naplójának tárolója|Nincs dimenzió|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Average|Kiszolgáló naplójának tárolási korlátja|Nincs dimenzió|
|active_connections|Aktív kapcsolatok|Count|Average|Aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Count|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|seconds_behind_master|Replikálás késése másodpercben|Count|Average|Replikálás késése másodpercben|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Average|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Percent|Average|CPU-százalék|Nincs dimenzió|
|memory_percent|Memória százaléka|Percent|Average|Memória százaléka|Nincs dimenzió|
|io_consumption_percent|IO-százalék|Percent|Average|IO-százalék|Nincs dimenzió|
|storage_percent|Tárolási százalék|Percent|Average|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tároló|Bájt|Average|Felhasznált tároló|Nincs dimenzió|
|storage_limit|Tárolási korlát|Bájt|Average|Tárolási korlát|Nincs dimenzió|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Percent|Average|Kiszolgáló naplójának tárolási százaléka|Nincs dimenzió|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Average|Kiszolgáló naplójának tárolója|Nincs dimenzió|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Average|Kiszolgáló naplójának tárolási korlátja|Nincs dimenzió|
|active_connections|Aktív kapcsolatok|Count|Average|Aktív kapcsolatok|Nincs dimenzió|
|connections_failed|Sikertelen kapcsolatok|Count|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Average|Felhasznált biztonsági mentési tár|Nincs dimenzió|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|pg_replica_log_delay_in_seconds|Replika késése|Másodperc|Maximum|Replika késése másodpercben|Nincs dimenzió|
|pg_replica_log_delay_in_bytes|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|Nincs dimenzió|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Percent|Average|CPU-százalék|Nincs dimenzió|
|memory_percent|Memória százaléka|Percent|Average|Memória százaléka|Nincs dimenzió|
|IOPS|IOPS|Count|Average|I/o-műveletek másodpercenként|Nincs dimenzió|
|storage_percent|Tárolási százalék|Percent|Average|Tárolási százalék|Nincs dimenzió|
|storage_used|Felhasznált tároló|Bájt|Average|Felhasznált tároló|Nincs dimenzió|
|active_connections|Aktív kapcsolatok|Count|Average|Aktív kapcsolatok|Nincs dimenzió|
|network_bytes_egress|Hálózat kimenő adatforgalma|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|
|network_bytes_ingress|Hálózat bejövő adatforgalma|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|Nincs dimenzió|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetria üzenetek küldése|Count|Összes|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincs dimenzió|
|d2c.telemetry.ingress.success|Telemetria üzenetek elküldése|Count|Összes|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincs dimenzió|
|c2d.commands.egress.complete.success|A parancsok befejeződtek|Count|Összes|Az eszköz által sikeresen befejeződött a felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.abandon.success|Elhagyott parancsok|Count|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|c2d.commands.egress.reject.success|Elutasított parancsok|Count|Összes|Az eszköz által visszautasított felhőből az eszközre irányuló parancsok száma|Nincs dimenzió|
|Devices. totalDevices|Összes eszköz (elavult)|Count|Összes|Az IoT hub-ban regisztrált eszközök száma|Nincs dimenzió|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök (elavult) |Count|Összes|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincs dimenzió|
|d2c.telemetry.egress.success|Útválasztás: telemetria üzenetek kézbesítése|Count|Összes|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincs dimenzió|
|d2c.telemetry.egress.dropped|Útválasztás: telemetria üzenetek elvetve |Count|Összes|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincs dimenzió|
|d2c.telemetry.egress.orphaned|Útválasztás: árva telemetria üzenetek |Count|Összes|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincs dimenzió|
|d2c.telemetry.egress.invalid|Útválasztás: Inkompatibilis telemetria-üzenetek|Count|Összes|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincs dimenzió|
|d2c.telemetry.egress.fallback|Útválasztás: tartalékként továbbított üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincs dimenzió|
|d2c.endpoints.egress.eventHubs|Útválasztás: az Event hub számára továbbított üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincs dimenzió|
|d2c.endpoints.latency.eventHubs|Útválasztás: az Event hub üzenetének késése|Ezredmásodperc|Average|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusQueues|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusQueues|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodperc|Average|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincs dimenzió|
|d2c.endpoints.egress.serviceBusTopics|Útválasztás: Service Bus témakörbe küldött üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincs dimenzió|
|d2c.endpoints.latency.serviceBusTopics|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodperc|Average|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincs dimenzió|
|d2c.endpoints.egress.builtIn.events|Útválasztás: üzenetek/események küldésére küldött üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események). Ez a metrika csak akkor kezd működni, ha az https://aka.ms/iotrouting) útválasztás engedélyezve van (az IoT hub esetében.|Nincs dimenzió|
|d2c.endpoints.latency.builtIn.events|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodperc|Average|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események). Ez a metrika csak akkor kezd működni, ha az https://aka.ms/iotrouting) útválasztás engedélyezve van (az IoT hub esetében.|Nincs dimenzió|
|d2c.endpoints.egress.storage|Útválasztás: a tárolóba küldött üzenetek|Count|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincs dimenzió|
|d2c.endpoints.latency.storage|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodperc|Average|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincs dimenzió|
|d2c.endpoints.egress.storage.bytes|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összes|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincs dimenzió|
|d2c.endpoints.egress.storage.blobs|Útválasztás: tárolóba szállított Blobok|Count|Összes|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincs dimenzió|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Count|Összes|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát https://aka.ms/ioteventgrid) (.|Eredmény, EventType|
|EventGridLatency|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|d2c.twin.read.success|Sikeres dupla olvasások az eszközökről|Count|Összes|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|d2c.twin.read.failure|Sikertelen dupla olvasások az eszközökről|Count|Összes|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincs dimenzió|
|d2c.twin.read.size|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Average|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincs dimenzió|
|d2c.twin.update.success|Sikeres dupla frissítések az eszközökről|Count|Összes|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincs dimenzió|
|d2c.twin.update.failure|Sikertelen dupla frissítések az eszközökről|Count|Összes|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|d2c.twin.update.size|Az eszközökből származó kettős frissítések mérete|Bájt|Average|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincs dimenzió|
|c2d.methods.success|Közvetlen metódusok sikeres meghívása|Count|Összes|Az összes sikeres közvetlen metódus hívásának száma.|Nincs dimenzió|
|C2D. Methods. failure|Sikertelen közvetlen metódusok meghívása|Count|Összes|A sikertelen közvetlen metódusok összes hívásának száma.|Nincs dimenzió|
|c2d.methods.requestSize|A közvetlen metódus meghívásának mérete|Bájt|Average|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincs dimenzió|
|c2d.methods.responseSize|A közvetlen metódus-meghívások válaszának mérete|Bájt|Average|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincs dimenzió|
|c2d.twin.read.success|Sikeres dupla olvasások a háttérből|Count|Összes|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|c2d.twin.read.failure|Sikertelen dupla olvasások a háttérből|Count|Összes|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincs dimenzió|
|c2d.twin.read.size|Dupla olvasások válaszának mérete a háttérből|Bájt|Average|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincs dimenzió|
|c2d.twin.update.success|Sikeres dupla frissítések a háttérből|Count|Összes|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|c2d.twin.update.failure|Sikertelen dupla frissítések a háttérből|Count|Összes|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincs dimenzió|
|c2d.twin.update.size|Dupla frissítések mérete a háttérből|Bájt|Average|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincs dimenzió|
|twinQueries.success|Sikeres Twin-lekérdezések|Count|Összes|Az összes sikeres dupla lekérdezés száma.|Nincs dimenzió|
|twinQueries.failure|Sikertelen dupla lekérdezések|Count|Összes|Az összes sikertelen dupla lekérdezés száma.|Nincs dimenzió|
|twinQueries.resultSize|Dupla lekérdezések eredményének mérete|Bájt|Average|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincs dimenzió|
|jobs.createTwinUpdateJob.success|A kettős frissítési feladatok sikeres létrehozása|Count|Összes|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincs dimenzió|
|jobs.createTwinUpdateJob.failure|A kettős frissítési feladatok sikertelen létrehozása|Count|Összes|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.success|Metódus-Meghívási feladatok sikeres létrehozása|Count|Összes|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincs dimenzió|
|jobs.createDirectMethodJob.failure|Nem sikerült létrehozni a metódus Meghívási feladatait|Count|Összes|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincs dimenzió|
|jobs.listJobs.success|Sikeres hívások a feladatok listázásához|Count|Összes|A feladatok listázására irányuló sikeres hívások száma.|Nincs dimenzió|
|jobs.listJobs.failure|Sikertelen hívások a feladatok listázásához|Count|Összes|A feladatokat listázó sikertelen hívások száma.|Nincs dimenzió|
|jobs.cancelJob.success|Sikeres feladatok törlése|Count|Összes|A feladat megszakítására irányuló sikeres hívások száma.|Nincs dimenzió|
|jobs.cancelJob.failure|Sikertelen feladatok megszakítása|Count|Összes|A feladat megszakítására irányuló sikertelen hívások száma.|Nincs dimenzió|
|jobs.queryJobs.success|Sikeres feladatok lekérdezése|Count|Összes|A lekérdezési feladatok összes sikeres hívásának száma.|Nincs dimenzió|
|jobs.queryJobs.failure|Sikertelen feladatok lekérdezése|Count|Összes|A lekérdező feladatok összes sikertelen hívásának száma.|Nincs dimenzió|
|feladatok. kész|Befejezett feladatok|Count|Összes|Az összes befejezett feladat száma.|Nincs dimenzió|
|feladatok. sikertelen|Sikertelen feladatok|Count|Összes|Az összes sikertelen feladat száma.|Nincs dimenzió|
|d2c.telemetry.ingress.sendThrottle|Szabályozási hibák száma|Count|Összes|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincs dimenzió|
|dailyMessageQuotaUsed|A felhasznált üzenetek teljes száma|Count|Average|A jelenleg használt üzenetek teljes száma. Ez egy kumulatív érték, amely minden nap 00:00-kor, a nulla időpontra visszaáll.|Nincs dimenzió|
|deviceDataUsage|Az eszköz összes adatfelhasználása|Bájt|Összes|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincs dimenzió|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Count|Average|Az IoT hub-ban regisztrált eszközök száma|Nincs dimenzió|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Count|Average|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincs dimenzió|
|konfigurációk|Konfigurációs mérőszámok|Count|Összes|A konfigurációs műveletek metrikái|Nincs dimenzió|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Count|Összes|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Hozzárendelt eszközök|Count|Összes|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Igazolási kísérletek|Count|Összes|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AvailableStorage|Rendelkezésre álló tár|Bájt|Összes|5 perces részletességgel jelentett teljes rendelkezésre álló tárterület|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|Cassandra-kapcsolatok bezárása|Count|Összes|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|Régió, ClosureReason|
|CassandraRequestCharges|Cassandra-kérelmek díjai|Count|Összes|A Cassandra-kérelmekhez felhasznált RUs|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra-kérelmek|Count|Count|A végrehajtott Cassandra-kérelmek száma|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Adatforgalom|Bájt|Összes|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DocumentCount|Dokumentumok száma|Count|Összes|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|Dokumentum kvótája|Bájt|Összes|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|IndexUsage|Indexelés használata|Bájt|Összes|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|MetadataRequests|Metaadat-kérelmek|Count|Count|A metaadat-kérelmek száma. Cosmos DB az egyes fiókok rendszermetaadat-gyűjteményét kezeli, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, |
|MongoRequestCharge|Mongo-kérelem díja|Count|Összes|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequests|Mongo kérelmek|Count|Count|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|ProvisionedThroughput|Kiosztott átviteli kapacitás|Count|Maximum|Kiosztott átviteli kapacitás|DatabaseName, CollectionName|
|ReplicationLatency|P99 replikáció késése|Ezredmásodpercben|Average|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion, TargetRegion|
|ServiceAvailability|Szolgáltatás rendelkezésre állása|Percent|Average|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|Nincs dimenzió|
|TotalRequestUnits|Kérelmek összes egysége|Count|Összes|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType|
|TotalRequests|Összes kérelem|Count|Count|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Count|Összes|A témakörben közzétett összes esemény|Nincs dimenzió|
|PublishFailCount|Sikertelen események közzététele|Count|Összes|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Count|Összes|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincs dimenzió|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Count|Összes|Sikeres közzétételi késés (ezredmásodpercben)|Nincs dimenzió|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Egyeztetett események|Count|Összes|Az esemény-előfizetésnek megfelelő összes esemény|Nincs dimenzió|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Count|Összes|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Count|Összes|Az esemény-előfizetésbe küldött összes esemény|Nincs dimenzió|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodperc|Average|Cél feldolgozási időtartama (ezredmásodpercben)|Nincs dimenzió|
|DroppedEventCount|Eldobott események|Count|Összes|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Count|Összes|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Count|Összes|A témakörben közzétett összes esemény|Nincs dimenzió|
|PublishFailCount|Sikertelen események|Count|Összes|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Count|Összes|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincs dimenzió|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Count|Összes|Sikeres közzétételi késés (ezredmásodpercben)|Nincs dimenzió|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Count|Összes|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName |
|Kiszolgálóhibái|Kiszolgálói hibák.|Count|Összes|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName |
|UserErrors|Felhasználói hibák.|Count|Összes|A Microsoft. EventHub felhasználói hibái.|EntityName |
|QuotaExceededErrors|A kvóta túllépte a hibákat.|Count|Összes|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName |
|ThrottledRequests|Szabályozott kérelmek.|Count|Összes|A Microsoft. EventHub által szabályozott kérelmek.|EntityName |
|IncomingRequests|Bejövő kérések|Count|Összes|A Microsoft. EventHub bejövő kérései.|EntityName|
|IncomingMessages|Bejövő üzenetek|Count|Összes|A Microsoft. EventHub bejövő üzenetei.|EntityName|
|OutgoingMessages|Kimenő üzenetek|Count|Összes|A Microsoft. EventHub kimenő üzenetei.|EntityName|
|IncomingBytes|Bejövő bájtok.|Bájt|Összes|A Microsoft. EventHub bejövő bájtjai.|EntityName|
|OutgoingBytes|Kimenő bájtok.|Bájt|Összes|A Microsoft. EventHub kimenő bájtjai.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|A Microsoft. EventHub összes aktív kapcsolata.|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatok.|Count|Average|A Microsoft. EventHub megnyitott kapcsolatainak száma.|EntityName|
|ConnectionsClosed|A kapcsolatok lezárva.|Count|Average|A Microsoft. EventHub lezárt kapcsolatainak száma.|EntityName|
|CaptureBacklog|Várakozó fájlok rögzítése.|Count|Összes|A Microsoft. EventHub várakozó fájlok rögzítése.|EntityName|
|CapturedMessages|Rögzített üzenetek.|Count|Összes|A Microsoft. EventHub rögzített üzenetei.|EntityName|
|CapturedBytes|Rögzített bájtok száma.|Bájt|Összes|A Microsoft. EventHub rögzített bájtok száma.|EntityName|
|Size|Size|Bájt|Average|Egy EventHub mérete bájtban megadva.|EntityName|
|INREQS|Bejövő kérelmek (elavult)|Count|Összes|Egy névtér összes bejövő küldési kérelme (elavult)|Nincs dimenzió|
|SUCCREQ|Sikeres kérések (elavult)|Count|Összes|A névtér összes sikeres kérelme (elavult)|Nincs dimenzió|
|FAILREQ|Sikertelen kérelmek (elavult)|Count|Összes|Névtér összes sikertelen kérelme (elavult)|Nincs dimenzió|
|SVRBSY|Kiszolgáló által foglalt hibák (elavult)|Count|Összes|Egy névtér összes kiszolgáló által foglalt hibája (elavult)|Nincs dimenzió|
|INTERER|Belső kiszolgálói hibák (elavult)|Count|Összes|Névtér összes belső kiszolgálóhiba (elavult)|Nincs dimenzió|
|MISCERR|Egyéb hibák (elavult)|Count|Összes|Névtér összes sikertelen kérelme (elavult)|Nincs dimenzió|
|INMSGS|Bejövő üzenetek (elavult) (elavult)|Count|Összes|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)|Nincs dimenzió|
|EHINMSGS|Bejövő üzenetek (elavult)|Count|Összes|Névtér összes bejövő üzenete (elavult)|Nincs dimenzió|
|OUTMSGS|Kimenő üzenetek (elavult) (elavult)|Count|Összes|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)|Nincs dimenzió|
|EHOUTMSGS|Kimenő üzenetek (elavult)|Count|Összes|Névtér összes kimenő üzenete (elavult)|Nincs dimenzió|
|EHINMBS|Bejövő bájtok (elavult) (elavult)|Bájt|Összes|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)|Nincs dimenzió|
|EHINBYTES|Bejövő bájtok (elavult)|Bájt|Összes|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincs dimenzió|
|EHOUTMBS|Kimenő bájtok (elavult) (elavult)|Bájt|Összes|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)|Nincs dimenzió|
|EHOUTBYTES|Kimenő bájtok (elavult)|Bájt|Összes|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincs dimenzió|
|EHABL|Archivált várakozó üzenetek (elavult)|Count|Összes|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)|Nincs dimenzió|
|EHAMSGS|Archivált üzenetek (elavult)|Count|Összes|Event hub archivált üzenetek egy névtérben (elavult)|Nincs dimenzió|
|EHAMBS|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összes|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)|Nincs dimenzió|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előzetes verzió)|Count|Összes|A Microsoft. EventHub sikeres kérelmeinek száma. (Előzetes verzió)|Nincs dimenzió|
|Kiszolgálóhibái|Kiszolgálói hibák. (Előzetes verzió)|Count|Összes|Kiszolgálói hibák a Microsoft. EventHub esetében. (Előzetes verzió)|Nincs dimenzió|
|UserErrors|Felhasználói hibák. (Előzetes verzió)|Count|Összes|A Microsoft. EventHub felhasználói hibái. (Előzetes verzió)|Nincs dimenzió|
|QuotaExceededErrors|A kvóta túllépte a hibákat. (Előzetes verzió)|Count|Összes|A kvóta túllépte a Microsoft. EventHub hibáit. (Előzetes verzió)|Nincs dimenzió|
|ThrottledRequests|Szabályozott kérelmek. (Előzetes verzió)|Count|Összes|A Microsoft. EventHub által szabályozott kérelmek. (Előzetes verzió)|Nincs dimenzió|
|IncomingRequests|Bejövő kérések (előzetes verzió)|Count|Összes|A Microsoft. EventHub bejövő kérései. (Előzetes verzió)|Nincs dimenzió|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Count|Összes|A Microsoft. EventHub bejövő üzenetei. (Előzetes verzió)|Nincs dimenzió|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Count|Összes|A Microsoft. EventHub kimenő üzenetei. (Előzetes verzió)|Nincs dimenzió|
|IncomingBytes|Bejövő bájtok. (Előzetes verzió)|Bájt|Összes|A Microsoft. EventHub bejövő bájtjai. (Előzetes verzió)|Nincs dimenzió|
|OutgoingBytes|Kimenő bájtok. (Előzetes verzió)|Bájt|Összes|A Microsoft. EventHub kimenő bájtjai. (Előzetes verzió)|Nincs dimenzió|
|ActiveConnections|Aktív kapcsolatai (előzetes verzió)|Count|Average|A Microsoft. EventHub összes aktív kapcsolata. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsOpened|Megnyitott kapcsolatok. (Előzetes verzió)|Count|Average|A Microsoft. EventHub megnyitott kapcsolatainak száma. (Előzetes verzió)|Nincs dimenzió|
|ConnectionsClosed|A kapcsolatok lezárva. (Előzetes verzió)|Count|Average|A Microsoft. EventHub lezárt kapcsolatainak száma. (Előzetes verzió)|Nincs dimenzió|
|CaptureBacklog|Várakozó fájlok rögzítése. (Előzetes verzió)|Count|Összes|A Microsoft. EventHub várakozó fájlok rögzítése. (Előzetes verzió)|Nincs dimenzió|
|CapturedMessages|Rögzített üzenetek. (Előzetes verzió)|Count|Összes|A Microsoft. EventHub rögzített üzenetei. (Előzetes verzió)|Nincs dimenzió|
|CapturedBytes|Rögzített bájtok száma. (Előzetes verzió)|Bájt|Összes|A Microsoft. EventHub rögzített bájtok száma. (Előzetes verzió)|Nincs dimenzió|
|CPU|CPU (előzetes verzió)|Percent|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Role|
|AvailableMemory|Rendelkezésre álló memória (előzetes verzió)|Count|Maximum|Az Event hub-fürt számára rendelkezésre álló memória bájtban megadva|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjáróra vonatkozó kérelmek|Count|Összes|Az átjáróra vonatkozó kérelmek száma|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategorizált átjáró-kérelmek|Count|Összes|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Aktív feldolgozók száma|Count|Maximum|Aktív feldolgozók száma|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrikaérték|Count|Average|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|MetricThreshold|Metrika küszöbértéke|Count|Average|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Count|Average|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.|Nincs dimenzió|
|ScaleActionsInitiated|Elindított skálázási műveletek|Count|Összes|A skálázási művelet iránya.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Nyilvános előzetes verzió)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Rendelkezésre állás|Százalék|Average|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|Rendelkezésre állási tesztek|Count|Count|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Average|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Hálózati kapcsolat ideje lapbetöltéskor|Ezredmásodpercben|Average|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|Nincs dimenzió|
|browserTimings/processingDuration|Ügyfél feldolgozási ideje|Ezredmásodpercben|Average|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|Nincs dimenzió|
|browserTimings/receiveDuration|Válasz fogadási ideje|Ezredmásodpercben|Average|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|Nincs dimenzió|
|browserTimings/sendDuration|Kérelem küldési ideje|Ezredmásodpercben|Average|A hálózati kapcsolat és az első bájt fogadása közötti idő.|Nincs dimenzió|
|browserTimings/totalDuration|Böngésző lapbetöltési ideje|Ezredmásodpercben|Average|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|Nincs dimenzió|
|függőségek/darabszám|Függőségi hívások|Count|Count|Az alkalmazás által külső erőforrások felé indított hívások száma.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|függőségek/időtartam|Függőségi időtartam|Ezredmásodpercben|Average|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|függőségek/sikertelen|Függőségi hívások hibái|Count|Count|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|Oldalmegtekintések/darabszám|Lapmegtekintések|Count|Count|Lapok nézeteinek száma.|művelet/szintetikus|
|pageViews/duration|Lapmegtekintés betöltési ideje|Ezredmásodpercben|Average|Lapmegtekintés betöltési ideje|művelet/szintetikus|
|performanceCounters/requestExecutionTime|HTTP-kérés végrehajtási ideje|Ezredmásodpercben|Average|A legutóbbi kérelem végrehajtási ideje.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-kérések az alkalmazás sorában|Count|Average|Az alkalmazás-kérelmek várólistájának hossza|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-kérések gyakorisága|Egység/s|Average|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Kivételek gyakorisága|Egység/s|Average|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Folyamat átviteli sebessége|BytesPerSecond|Average|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Folyamat CPU|Percent|Average|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processzoridő|Percent|Average|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Elérhető memória|Bájt|Average|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Folyamat saját bájtjai|Bájt|Average|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|cloud/roleInstance|
|kérelmek/időtartam|Kiszolgáló válaszideje|Ezredmásodpercben|Average|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/darabszám|Kiszolgálói kérelmek|Count|Count|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|Sikertelen kérelmek|Count|Count|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések > = 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|Kiszolgálói kérelmek gyakorisága|Egység/s|Average|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kivételek/darabszám|Kivételek|Count|Count|Az összes nem kezelt kivétel összesített száma.|cloud/roleName, cloud/roleInstance, client/type|
|kivételek/böngésző|Böngészőkivételek|Count|Count|A böngészőben fellépő nem kezelt kivételek száma.|Nincs dimenzió|
|kivételek/kiszolgáló|Kiszolgálókivételek|Count|Count|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|felhő/roleName, felhő/roleInstance|
|nyomkövetés/darabszám|Hívásláncok|Count|Count|Híváslánc-dokumentumok száma|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Összes szolgáltatási API-találat|Count|Count|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|A szolgáltatási API teljes késése|Ezredmásodperc|Average|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Összes szolgáltatási API-eredmény|Count|Count|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CacheUtilization|Gyorsítótár kihasználtsága|Percent|Average|Kihasználtsági szint a fürt hatókörében|Nincsenek|
|QueryDuration|Lekérdezés időtartama|Ezredmásodperc|Average|Lekérdezések időtartama másodpercben|Lekérdezés állapota|
|IngestionUtilization|Betöltés kihasználtsága|Percent|Average|A használatban lévő betöltési tárolóhelyek aránya a fürtben|Nincsenek|
|KeepAlive|Életben tartása|Count|Average|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|Nincsenek|
|IngestionVolumeInMB|Betöltési mennyiség (MB)|Count|Összes|A betöltött adatmennyiség teljes mennyisége a fürtön (MB)|Adatbázis|
|IngestionLatencyInSeconds|Betöltési késleltetés (másodperc)|Másodperc|Average|A lenyelési idő a forrástól (például az üzenet a EventHub) a fürthöz másodpercek alatt|Nincsenek|
|EventProcessedForEventHubs|Feldolgozott események (Event Hubs)|Count|Összes|Az Event hub-ból való betöltéskor a fürt által feldolgozott események száma|Nincsenek|
|IngestionResult|Betöltés eredménye|Count|Count|Betöltési műveletek száma|State|
|CPU|CPU|Percent|Average|CPU-kihasználtsági szint|Nincsenek|
| ContinuousExportNumOfRecordsExported | A folyamatos exportálás során exportált rekordok száma | Count | Összes | Az exportálási művelet során írt összes tárolási összetevőhöz exportált rekordok száma  | Nincsenek |
| ExportUtilization | Exportálás kihasználtsága | Percent | Maximum | Exportálás kihasználtsága | Nincsenek |
| ContinuousExportPendingCount | Folyamatos exportálás függőben lévő darabszáma | Count | Maximum | A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma | Nincsenek |
| ContinuousExportMaxLatenessMinutes | Folyamatos exportálás maximális késői percben | Count | Maximum | Az összes függőben lévő és a végrehajtásra kész összes folyamatos exportálás percben megadott maximális ideje | Nincsenek |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Count|Count|API-hívások száma|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Count|Összes|Az elindított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCompleted|Befejezett futtatások|Count|Összes|A befejezett munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsSucceeded|Sikeres futtatások|Count|Összes|A sikeres munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsFailed|Sikertelen futtatások|Count|Összes|Sikertelen munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCancelled|Megszakított futtatások|Count|Összes|A megszakított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunLatency|Futtatások késése|Másodperc|Average|A befejezett munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunSuccessLatency|Sikeres futtatások késése|Másodperc|Average|A sikeres munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunThrottledEvents|Futtatások által elindított események|Count|Összes|A munkafolyamat-műveletek vagy az elindított események száma.|Nincs dimenzió|
|RunFailurePercentage|Futtatási hibák százalékos értéke|Percent|Összes|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincs dimenzió|
|ActionsStarted|Elindított műveletek |Count|Összes|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|Befejezett műveletek |Count|Összes|A befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|Sikeres műveletek |Count|Összes|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Sikertelen műveletek|Count|Összes|Nem sikerült a munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Count|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|Műveletek késése |Másodperc|Average|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|Sikeres műveletek késése |Másodperc|Average|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|Műveletek által elindított események|Count|Összes|A munkafolyamat-műveletek által szabályozott események száma..|Nincs dimenzió|
|TriggersStarted|Elindított triggerek |Count|Összes|Az elindított munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersCompleted|Befejezett triggerek |Count|Összes|A befejezett munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSucceeded|Sikeres triggerek |Count|Összes|A sikeres munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFailed|Sikertelen triggerek |Count|Összes|Sikertelen munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott triggerek|Count|Összes|A kihagyott munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFired|Aktivált triggerek |Count|Összes|A kilőtt munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggerLatency|Triggerek késése |Másodperc|Average|A befejezett munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerFireLatency|Triggerek aktiválásának késése |Másodperc|Average|Az aktivált munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerSuccessLatency|Sikeres triggerek késése |Másodperc|Average|A sikeres munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerThrottledEvents|Triggerek által elindított események|Count|Összes|A beállított munkafolyamat-triggerek száma.|Nincs dimenzió|
|BillableActionExecutions|Számlázható műveleti végrehajtások|Count|Összes|A számlázott munkafolyamat-műveletek végrehajtásának száma.|Nincs dimenzió|
|BillableTriggerExecutions|Számlázható triggerek végrehajtásai|Count|Összes|A számlázott munkafolyamat-trigger-végrehajtások száma.|Nincs dimenzió|
|TotalBillableExecutions|Összes számlázható végrehajtás|Count|Összes|A számlázandó munkafolyamat-végrehajtások száma.|Nincs dimenzió|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Count|Összes|A számlázott natív művelet-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Count|Összes|A számlázott szabványos összekötő-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Count|Összes|A számlázható tárterület-használat végrehajtásának száma.|Nincs dimenzió|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Count|Összes|A számlázott natív művelet-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Count|Összes|A számlázott szabványos összekötő-végrehajtások száma.|Nincs dimenzió|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Count|Összes|A számlázható tárterület-használat végrehajtásának száma.|Nincs dimenzió|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Count|Összes|Az elindított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCompleted|Befejezett futtatások|Count|Összes|A befejezett munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsSucceeded|Sikeres futtatások|Count|Összes|A sikeres munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsFailed|Sikertelen futtatások|Count|Összes|Sikertelen munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunsCancelled|Megszakított futtatások|Count|Összes|A megszakított munkafolyamat-futtatások száma.|Nincs dimenzió|
|RunLatency|Futtatások késése|Másodperc|Average|A befejezett munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunSuccessLatency|Sikeres futtatások késése|Másodperc|Average|A sikeres munkafolyamat-futtatások késése.|Nincs dimenzió|
|RunThrottledEvents|Futtatások által elindított események|Count|Összes|A munkafolyamat-műveletek vagy az elindított események száma.|Nincs dimenzió|
|RunStartThrottledEvents|Futtatás indítása által szabályozott események|Count|Összes|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincs dimenzió|
|RunFailurePercentage|Futtatási hibák százalékos értéke|Percent|Összes|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincs dimenzió|
|ActionsStarted|Elindított műveletek |Count|Összes|Az elindított munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsCompleted|Befejezett műveletek |Count|Összes|A befejezett munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSucceeded|Sikeres műveletek |Count|Összes|A sikeres munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsFailed|Sikertelen műveletek |Count|Összes|Nem sikerült a munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionsSkipped|Kihagyott műveletek |Count|Összes|A kihagyott munkafolyamat-műveletek száma.|Nincs dimenzió|
|ActionLatency|Műveletek késése |Másodperc|Average|A befejezett munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionSuccessLatency|Sikeres műveletek késése |Másodperc|Average|A sikeres munkafolyamat-műveletek késése.|Nincs dimenzió|
|ActionThrottledEvents|Műveletek által elindított események|Count|Összes|A munkafolyamat-műveletek által szabályozott események száma..|Nincs dimenzió|
|TriggersStarted|Elindított triggerek |Count|Összes|Az elindított munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersCompleted|Befejezett triggerek |Count|Összes|A befejezett munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSucceeded|Sikeres triggerek |Count|Összes|A sikeres munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFailed|Sikertelen triggerek |Count|Összes|Sikertelen munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersSkipped|Kihagyott triggerek|Count|Összes|A kihagyott munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggersFired|Aktivált triggerek |Count|Összes|A kilőtt munkafolyamat-eseményindítók száma.|Nincs dimenzió|
|TriggerLatency|Triggerek késése |Másodperc|Average|A befejezett munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerFireLatency|Triggerek aktiválásának késése |Másodperc|Average|Az aktivált munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerSuccessLatency|Sikeres triggerek késése |Másodperc|Average|A sikeres munkafolyamat-eseményindítók késése.|Nincs dimenzió|
|TriggerThrottledEvents|Triggerek által elindított események|Count|Összes|A beállított munkafolyamat-triggerek száma.|Nincs dimenzió|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Percent|Average|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|Nincs dimenzió|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Percent|Average|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|Nincs dimenzió|
|IntegrationServiceEnvironmentConnectorProcessorUsage|integrációs szolgáltatási környezet összekötő-processzorának használata|Percent|Average|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|Nincs dimenzió|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Összekötő memóriahasználat integrációs szolgáltatási környezet|Percent|Average|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|Nincs dimenzió|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Befejezett futtatások|Befejezett futtatások|Count|Összes|A munkaterülethez sikeresen befejezett futtatások száma|Forgatókönyv|
|Elindított futtatások|Elindított futtatások|Count|Összes|A munkaterülethez elindított futtatások száma|Forgatókönyv|
|Sikertelen futtatások|Sikertelen futtatások|Count|Összes|Sikertelen futtatások száma ehhez a munkaterülethez|Forgatókönyv|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Count|Count|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Average|Az API-k rendelkezésre állása|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageOtherLatency|Egyéb késések átlaga|ms/op|Average|Átlagos egyéb késés (nem olvasható vagy írható) ezredmásodpercben, művelet|Nincs dimenzió|
|AverageReadLatency|Olvasási késleltetés átlagos késése|ms/op|Average|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|Nincs dimenzió|
|AverageTotalLatency|Teljes késés átlaga|ms/op|Average|Átlagos teljes késés (ezredmásodpercben)/művelet|Nincs dimenzió|
|AverageWriteLatency|Írási késleltetés átlagos késése|ms/op|Average|Írási késleltetés átlagos száma ezredmásodpercben|Nincs dimenzió|
|FilesystemOtherOps|Fájlrendszer – egyéb Ops|Ops|Average|Fájlrendszer egyéb műveleteinek száma (nem olvasható vagy írható)|Nincs dimenzió|
|FilesystemReadOps|Fájlrendszer olvasási Ops|Ops|Average|A fájlrendszer olvasási műveleteinek száma|Nincs dimenzió|
|FilesystemTotalOps|Fájlrendszer összes Ops|Ops|Average|Az összes fájlrendszer-művelet összege|Nincs dimenzió|
|FilesystemWriteOps|Fájlrendszer írási Ops|Ops|Average|A fájlrendszer írási műveleteinek száma|Nincs dimenzió|
|IoBytesPerOtherOps|IO bájt/egyéb műveleti azonosító|bájt/op|Average|/Kimenő bájtok száma/egyéb műveletek (nem olvasható vagy írható)|Nincs dimenzió|
|IoBytesPerReadOps|IO bájt/olvasási Ops|bájt/op|Average|Bejövő/kimenő bájtok másodpercenkénti száma olvasási művelet esetén|Nincs dimenzió|
|IoBytesPerTotalOps|IO-bájt/op az összes műveletnél|bájt/op|Average|Az összes/kimenő bájtok műveletének összege|Nincs dimenzió|
|IoBytesPerWriteOps|IO bájt/írási műveleti egység|bájt/op|Average|Bejövő/kimenő bájtok száma írási művelettel|Nincs dimenzió|
|OtherIops|Egyéb IOPS|művelet/másodperc|Average|Egyéb/kimenő műveletek másodpercenként|Nincs dimenzió|
|OtherThroughput|Egyéb átviteli sebesség|MBps|Average|Egyéb átviteli sebesség (amely nem írható vagy írható) megabájt/másodpercben|Nincs dimenzió|
|ReadIops|IOPS olvasása|művelet/másodperc|Average|Olvasási/kimenő műveletek másodpercenként|Nincs dimenzió|
|ReadThroughput|Olvasási sebesség|MBps|Average|Olvasási sebesség (MB/s) másodpercenként|Nincs dimenzió|
|TotalIops|Összes IOPS|művelet/másodperc|Average|Az összes/kimenő művelet másodpercenkénti összege|Nincs dimenzió|
|TotalThroughput|Teljes átvitel|MBps|Average|Az összes átviteli sebesség (MB/s) összege másodpercenként|Nincs dimenzió|
|VolumeAllocatedSize|Kötet lefoglalt mérete|bájt|Average|A kötet lefoglalt mérete (nem a tényleges felhasznált bájtok)|Nincs dimenzió|
|VolumeLogicalSize|Kötet logikai mérete|bájt|Average|A kötet logikai mérete (felhasznált bájtok)|Nincs dimenzió|
|VolumeSnapshotSize|Kötet pillanatképének mérete|bájt|Average|A köteten található összes pillanatkép mérete|Nincs dimenzió|
|WriteIops|IOPS írása|művelet/másodperc|Average|Írási/kimenő műveletek másodpercenként|Nincs dimenzió|
|WriteThroughput|Írási sebesség|MBps|Average|Írási sebesség megabájtban (MB/s)|Nincs dimenzió|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Kötet-készlet lefoglalt mérete|bájt|Average|A készlet lefoglalt mérete (nem a tényleges felhasznált bájtok)|Nincs dimenzió|
|VolumePoolAllocatedUsed|Lefoglalt kötet-készlet|bájt|Average|A készlet lefoglalt felhasznált mérete|Nincs dimenzió|
|VolumePoolTotalLogicalSize|Kötet készletének teljes logikai mérete|bájt|Average|A készlethez tartozó összes kötet logikai méretének összege|Nincs dimenzió|
|VolumePoolTotalSnapshotSize|Kötet készletének teljes pillanatképének mérete|bájt|Average|A készlet összes pillanatképének összege|Nincs dimenzió|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Eljuttatott bájtok|Count|Összes|A hálózati adapter által eljuttatott bájtok száma|Nincs dimenzió|
|BytesReceivedRate|Fogadott bájtok száma|Count|Összes|A hálózati adapter által fogadott bájtok száma|Nincs dimenzió|
|PacketsSentRate|Küldött csomagok|Count|Összes|A hálózati adapter által küldött csomagok száma|Nincs dimenzió|
|PacketsReceivedRate|Fogadott csomagok|Count|Összes|A hálózati adapter által fogadott csomagok száma|Nincs dimenzió|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|Adatelérési út rendelkezésre állása|Count|Average|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress, FrontendPort|
|DipAvailability|Állapot mintavételi állapota|Count|Average|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Bájtok száma|Count|Összes|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Csomagok száma|Count|Összes|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN-szám|Count|Összes|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT-kapcsolatok száma|Count|Összes|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Lefoglalt SNAT portok (előzetes verzió)|Count|Összes|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Használt SNAT-portok (előzetes verzió)|Count|Összes|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezési kötet|Count|Összes|A DNS-zónák számára kiszolgált lekérdezések száma|Nincs dimenzió|
|RecordSetCount|Rekordok készletének száma|Count|Maximum|Rekordhalmazok száma egy DNS-zónában|Nincs dimenzió|
|RecordSetCapacityUtilization|Rekordazonosító kapacitásának kihasználtsága|Percent|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|Nincs dimenzió|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|Egység/s|Maximum|Bejövő csomagok DDoS|Nincs dimenzió|
|PacketsDroppedDDoS|Bejövő csomagok eldobott DDoS|Egység/s|Maximum|Bejövő csomagok eldobott DDoS|Nincs dimenzió|
|PacketsForwardedDDoS|Bejövő csomagok továbbított DDoS|Egység/s|Maximum|Bejövő csomagok továbbított DDoS|Nincs dimenzió|
|TCPPacketsInDDoS|Bejövő TCP-csomagok DDoS|Egység/s|Maximum|Bejövő TCP-csomagok DDoS|Nincs dimenzió|
|TCPPacketsDroppedDDoS|Bejövő TCP-csomagok eldobott DDoS|Egység/s|Maximum|Bejövő TCP-csomagok eldobott DDoS|Nincs dimenzió|
|TCPPacketsForwardedDDoS|Bejövő TCP-csomagok továbbított DDoS|Egység/s|Maximum|Bejövő TCP-csomagok továbbított DDoS|Nincs dimenzió|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|Egység/s|Maximum|Bejövő UDP-csomagok DDoS|Nincs dimenzió|
|UDPPacketsDroppedDDoS|Bejövő UDP-csomagok eldobott DDoS|Egység/s|Maximum|Bejövő UDP-csomagok eldobott DDoS|Nincs dimenzió|
|UDPPacketsForwardedDDoS|Bejövő UDP-csomagok továbbított DDoS|Egység/s|Maximum|Bejövő UDP-csomagok továbbított DDoS|Nincs dimenzió|
|BytesInDDoS|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|Nincs dimenzió|
|BytesDroppedDDoS|Bejövő bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő bájtok elvetve DDoS|Nincs dimenzió|
|BytesForwardedDDoS|Bejövő bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő bájtok továbbított DDoS|Nincs dimenzió|
|TCPBytesInDDoS|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|Nincs dimenzió|
|TCPBytesDroppedDDoS|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|Nincs dimenzió|
|TCPBytesForwardedDDoS|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|Nincs dimenzió|
|UDPBytesInDDoS|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|Nincs dimenzió|
|UDPBytesDroppedDDoS|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|Nincs dimenzió|
|UDPBytesForwardedDDoS|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|Nincs dimenzió|
|IfUnderDDoSAttack|DDoS-támadás alatt vagy nem|Count|Maximum|DDoS-támadás alatt vagy nem|Nincs dimenzió|
|DDoSTriggerTCPPackets|Bejövő TCP-csomagok a DDoS-mérséklés indításához|Egység/s|Maximum|Bejövő TCP-csomagok a DDoS-mérséklés indításához|Nincs dimenzió|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|Egység/s|Maximum|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|Nincs dimenzió|
|DDoSTriggerSYNPackets|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|Egység/s|Maximum|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|Nincs dimenzió|
|VipAvailability|Adatelérési út rendelkezésre állása|Count|Average|Átlagos IP-cím rendelkezésre állása időszakonként|Port|
|ByteCount|Bájtok száma|Count|Összes|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|PacketCount|Csomagok száma|Count|Összes|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|SynCount|SYN-szám|Count|Összes|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ApplicationRuleHit|Alkalmazás-szabályok találatok száma|Count|Összes|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|NetworkRuleHit|Hálózati szabályok találatok száma|Count|Összes|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljesítmény|Teljesítmény|BytesPerSecond|Összes|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|Nincs dimenzió|
|UnhealthyHostCount|Nem kifogástalan állapotú gazdagépek száma|Count|Average|Sérült háttérbeli gazdagépek száma|BackendSettingsPool|
|HealthyHostCount|Kifogástalan állapotú gazdagépek száma|Count|Average|Az egészséges háttérbeli gazdagépek száma|BackendSettingsPool|
|TotalRequests|Összes kérelem|Count|Összes|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|FailedRequests|Sikertelen kérelmek|Count|Összes|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|ResponseStatus|Válasz állapota|Count|Összes|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Összege|Aktuális kapcsolatok|Count|Összes|Application Gatewaysal létesített aktuális kapcsolatok száma|Nincs dimenzió|
|CapacityUnits|Aktuális kapacitási egységek|Count|Average|Felhasznált kapacitási egységek|Nincs dimenzió|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageBandwidth|Átjáró S2S sávszélessége|BytesPerSecond|Average|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincs dimenzió|
|P2SBandwidth|Átjáró P2S sávszélessége|BytesPerSecond|Average|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincs dimenzió|
|P2SConnectionCount|P2S-kapcsolatok száma|Count|Maximum|Átjáró pont – hely kapcsolatának száma|Protocol|
|TunnelAverageBandwidth|Bújtatási sávszélesség|BytesPerSecond|Average|Egy alagút átlagos sávszélessége (bájt/s)|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Alagút kimenő bájtjai|Bájt|Összes|Egy alagút kimenő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Alagutak bejövő bájtjai|Bájt|Összes|Alagút bejövő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagjai|Count|Összes|Alagút kimenő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Bújtatási bejövő csomagok|Count|Összes|Alagút bejövő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Alagút kimenő terminálszolgáltatási eltérési csomagjainak eldobása|Count|Összes|Kimenő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Az alagút bejövő HOZZÁFÉRÉSe nem egyezik a csomagok eldobásával|Count|Összes|Bejövő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Egység/s|Average|BITS ingressing Azure másodpercenként|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Egység/s|Average|BITS egressing Azure másodpercenként|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Egység/s|Average|BITS ingressing Azure másodpercenként|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|Egység/s|Average|BITS egressing Azure másodpercenként|Nincs dimenzió|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Egység/s|Average|BITS ingressing Azure másodpercenként|Nincs dimenzió|
|BitsOutPerSecond|BitsOutPerSecond|Egység/s|Average|BITS egressing Azure másodpercenként|Nincs dimenzió|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Visszaadott végponti lekérdezések|Count|Összes|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végponti állapot végpont szerint|Count|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ProbesFailedPercent|%-Os mintavétel sikertelen|Percent|Average|a kapcsolat figyelési mintavételének%-a meghiúsult|Nincs dimenzió|
|AverageRoundtripMs|Átl. Oda-és visszaút ideje (MS)|Ezredmásodpercben|Average|A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS)|Nincs dimenzió|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RequestCount|Kérelmek száma|Count|Összes|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Kérelem mérete|Bájt|Összes|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Válasz mérete|Bájt|Összes|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Háttérbeli kérelmek száma|Count|Összes|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Háttérbeli kérelmek késése|Ezredmásodpercben|Average|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Háttérszolgáltatás|
|TotalLatency|Teljes késés|Ezredmásodpercben|Average|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Háttér állapotának százalékos aránya|Percent|Average|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|WebApplicationFirewallRequestCount|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Count|Összes|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|regisztráció. All|Regisztrálási műveletek|Count|Összes|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |Nincs dimenzió|
|registration.create|Regisztráció-létrehozási műveletek|Count|Összes|Az összes sikeres regisztrációs létrehozás száma.|Nincs dimenzió|
|registration.update|Regisztráció-frissítési műveletek|Count|Összes|A sikeres regisztrációs frissítések száma.|Nincs dimenzió|
|registration.get|Regisztráció-olvasási műveletek|Count|Összes|A sikeres regisztrációs lekérdezések száma.|Nincs dimenzió|
|registration.delete|Regisztráció-törlési műveletek|Count|Összes|A regisztráció sikeres törléseinak száma.|Nincs dimenzió|
|bejövő|Bejövő üzenetek|Count|Összes|A sikeres küldési API-hívások száma. |Nincs dimenzió|
|bejövő. ütemezett|Elküldött ütemezett leküldéses értesítések|Count|Összes|Ütemezett leküldéses értesítések megszakítva|Nincs dimenzió|
|bejövő. ütemezett. Mégse|Ütemezett leküldéses értesítések megszakítva|Count|Összes|Ütemezett leküldéses értesítések megszakítva|Nincs dimenzió|
|ütemezett. függőben|Függőben lévő ütemezett értesítések|Count|Összes|Függőben lévő ütemezett értesítések|Nincs dimenzió|
|a telepítés. All|Telepítéskezelési műveletek|Count|Összes|Telepítéskezelési műveletek|Nincs dimenzió|
|telepítés. Get|Telepítéslekérdezési műveletek|Count|Összes|Telepítéslekérdezési műveletek|Nincs dimenzió|
|telepítési. upsert|Telepítés-létrehozási és -frissítési műveletek|Count|Összes|Telepítés-létrehozási és -frissítési műveletek|Nincs dimenzió|
|Installation. patch|Telepítésjavítási műveletek|Count|Összes|Telepítésjavítási műveletek|Nincs dimenzió|
|installation.delete|Telepítéstörlési műveletek|Count|Összes|Telepítéstörlési műveletek|Nincs dimenzió|
|outgoing.allpns.success|Sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|kimenő. allpns. invalidpayload|Terhelési hibák|Count|Összes|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|Nincs dimenzió|
|outgoing.allpns.pnserror|Külső értesítési rendszer hibái|Count|Összes|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|Nincs dimenzió|
|outgoing.allpns.channelerror|Csatornahibák|Count|Összes|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|Nincs dimenzió|
|outgoing.allpns.badorexpiredchannel|Rossz vagy lejárt csatorna által okozott hibák|Count|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|Nincs dimenzió|
|outgoing.wns.success|WNS – sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.wns.invalidcredentials|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Count|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|Nincs dimenzió|
|outgoing.wns.badchannel|WNS – rossz csatorna által okozott hiba|Count|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|Nincs dimenzió|
|outgoing.wns.expiredchannel|WNS – lejárt csatorna által okozott hiba|Count|Összes|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapot: 410 eltűnt).|Nincs dimenzió|
|kimenő. wns. szabályozva|WNS – szabályozott értesítések|Count|Összes|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapot: 406 nem elfogadható).|Nincs dimenzió|
|outgoing.wns.tokenproviderunreachable|WNS-hitelesítési hibák (nem érhető el)|Count|Összes|A Windows Live nem érhető el.|Nincs dimenzió|
|kimenő. wns. invalidtoken|WNS-hitelesítési hibák (érvénytelen token)|Count|Összes|A WNS számára megadott jogkivonat érvénytelen (WNS állapot: 401 nem engedélyezett).|Nincs dimenzió|
|outgoing.wns.wrongtoken|WNS-hitelesítési hibák (hibás token)|Count|Összes|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|Nincs dimenzió|
|kimenő. wns. invalidnotificationformat|WNS – érvénytelen értesítési formátum|Count|Összes|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|Nincs dimenzió|
|kimenő. wns. invalidnotificationsize|WNS – érvénytelen értesítésméret által okozott hiba|Count|Összes|Az értesítési tartalom túl nagy (WNS állapot: 413).|Nincs dimenzió|
|outgoing.wns.channelthrottled|WNS – csatorna szabályozva|Count|Összes|Az értesítés el lett dobva, mert a regisztrációban szereplő URI a regisztráció során szabályozott (WNS válasz fejléce: X-WNS-NotificationStatus:channelThrottled).|Nincs dimenzió|
|outgoing.wns.channeldisconnected|WNS – nincs kapcsolat a csatornával|Count|Összes|Az értesítés el lett dobva, mert a regisztrációban szereplő URI a regisztráció során szabályozott (WNS válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva.|Nincs dimenzió|
|kimenő. wns. Dropped|WNS – elvetett értesítések|Count|Összes|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincs dimenzió|
|outgoing.wns.pnserror|WNS-hibák|Count|Összes|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|Nincs dimenzió|
|outgoing.wns.authenticationerror|WNS – hitelesítési hibák|Count|Összes|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|Nincs dimenzió|
|outgoing.apns.success|APNS – sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.apns.invalidcredentials|APNS-hitelesítési hibák|Count|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.apns.badchannel|APNS – rossz csatorna által okozott hiba|Count|Összes|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS bináris protokoll állapotkód: 8. APNS HTTP protokoll állapotkód: 400 a következővel: "BadDeviceToken").|Nincs dimenzió|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Count|Összes|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|Nincs dimenzió|
|outgoing.apns.invalidnotificationsize|APNS – érvénytelen értesítésméret által okozott hiba|Count|Összes|Azon leküldések száma, amelyek sikertelenek voltak, mert túl nagy a hasznos adat (APNS bináris protokoll állapotkód: 7).|Nincs dimenzió|
|outgoing.apns.pnserror|APNS-hibák|Count|Összes|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.success|GCM – sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.gcm.invalidcredentials|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Count|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|kimenő. GCM. badchannel|GCM – rossz csatorna által okozott hiba|Count|Összes|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM eredmény: Érvénytelen regisztráció).|Nincs dimenzió|
|outgoing.gcm.expiredchannel|GCM – lejárt csatorna által okozott hiba|Count|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM eredmény: NotRegistered).|Nincs dimenzió|
|kimenő. GCM. szabályozva|GCM – szabályozott értesítések|Count|Összes|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM-állapotkód: 501-599 vagy eredmény: nem érhető el).|Nincs dimenzió|
|kimenő. GCM. invalidnotificationformat|GCM – érvénytelen értesítési formátum|Count|Összes|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|Nincs dimenzió|
|kimenő. GCM. invalidnotificationsize|GCM – érvénytelen értesítésméret által okozott hiba|Count|Összes|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|Nincs dimenzió|
|kimenő. GCM. wrongchannel|GCM – nem megfelelő csatorna által okozott hiba|Count|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM eredmény: InvalidPackageName).|Nincs dimenzió|
|outgoing.gcm.pnserror|GCM-hibák|Count|Összes|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.gcm.authenticationerror|GCM-hitelesítési hibák|Count|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM eredmény: MismatchedSenderId).|Nincs dimenzió|
|outgoing.mpns.success|MPNS – sikeres értesítések|Count|Összes|Az összes sikeres értesítés száma.|Nincs dimenzió|
|outgoing.mpns.invalidcredentials|MPNS – érvénytelen hitelesítő adatok|Count|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|outgoing.mpns.badchannel|MPNS – rossz csatorna által okozott hiba|Count|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|Nincs dimenzió|
|outgoing.mpns.throttled|MPNS – szabályozott értesítések|Count|Összes|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem elfogadható).|Nincs dimenzió|
|outgoing.mpns.invalidnotificationformat|MPNS – érvénytelen értesítési formátum|Count|Összes|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|Nincs dimenzió|
|outgoing.mpns.channeldisconnected|MPNS – nincs kapcsolat a csatornával|Count|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (a MPNS állapota: 412 nem található).|Nincs dimenzió|
|kimenő. mpns. Dropped|MPNS – elvetett értesítések|Count|Összes|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X – NotificationStatus: QueueFull vagy letiltva).|Nincs dimenzió|
|outgoing.mpns.pnserror|MPNS-hibák|Count|Összes|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincs dimenzió|
|outgoing.mpns.authenticationerror|MPNS – hitelesítési hibák|Count|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincs dimenzió|
|notificationhub. leküldések|Minden kimenő értesítés|Count|Összes|Az értesítési központ összes kimenő értesítése|Nincs dimenzió|
|incoming.all.requests|Minden bejövő kérelem|Count|Összes|Értesítési központ összes bejövő kérelme|Nincs dimenzió|
|bejövő. ALL. failedrequests|Minden sikertelen bejövő kérelem|Count|Összes|Értesítési központ bejövő sikertelen kéréseinek száma|Nincs dimenzió|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_%-os szabad inode|Szabad inode%-ban|Count|Average|Average_%-os szabad inode|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ szabad területe (%)|% Szabad terület|Count|Average|Average_ szabad területe (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-ban használt inode|Felhasznált inode%-ban|Count|Average|Average_%-ban használt inode|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% felhasznált terület|Foglalt hely %|Count|Average|Average_% felhasznált terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk olvasási sebesség (bájt/s)|Lemezolvasási sebesség (bájt/s)|Count|Average|Average_Disk olvasási sebesség (bájt/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Olvasási sebesség (Average_Disk/s)|Lemezolvasások/mp|Count|Average|Olvasási sebesség (Average_Disk/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk adatátvitel gyakorisága (mp)|Átvitel/mp|Count|Average|Average_Disk adatátvitel gyakorisága (mp)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk írási sebesség (bájt/s)|Lemezírási sebesség (bájt/s)|Count|Average|Average_Disk írási sebesség (bájt/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk írás gyakorisága (művelet/mp)|Lemezírások/mp|Count|Average|Average_Disk írás gyakorisága (művelet/mp)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Szabad hely MB-ban|Count|Average|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Logikai lemez bájt/mp|Count|Average|Average_Logical lemez sebessége (bájt/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os rendelkezésre álló memória|Rendelkezésre álló memória%-ban|Count|Average|Average_%-os rendelkezésre álló memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os rendelkezésre álló swap-terület|Rendelkezésre álló swap-terület (%)|Count|Average|Average_%-os rendelkezésre álló swap-terület|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-ban használt memória|Felhasznált memória (%)|Count|Average|Average_%-ban használt memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-ban használt lapozófájl|Felhasznált swap-terület%-ban|Count|Average|Average_%-ban használt lapozófájl|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available memória (MB)|Rendelkezésre álló memória|Count|Average|Average_Available memória (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available (MB) – swap|Rendelkezésre álló memória (MB)|Count|Average|Average_Available (MB) – swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Olvasási sebesség (Average_Page/s)|Olvasott lap/mp|Count|Average|Olvasási sebesség (Average_Page/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page írás gyakorisága (művelet/mp)|Írási idő/mp|Count|Average|Average_Page írás gyakorisága (művelet/mp)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Lap/mp|Count|Average|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MB-ban|Felhasznált memória (MB) – lapozófájl|Count|Average|Average_Used MB-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória MB-ban|Felhasznált memória (MB)|Count|Average|Average_Used memória MB-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Továbbított Average_Total-bájtok|Küldött bájtok száma összesen|Count|Average|Továbbított Average_Total-bájtok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Total-bájtok száma|Fogadott bájtok teljes száma|Count|Average|Fogadott Average_Total-bájtok száma|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bájtok|Összes bájt|Count|Average|Average_Total bájtok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Továbbított Average_Total-csomagok|Továbbított csomagok összesen|Count|Average|Továbbított Average_Total-csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Total-csomagok|Fogadott csomagok összesen|Count|Average|Fogadott Average_Total-csomagok|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-hibák|Rx-hibák összesen|Count|Average|Average_Total RX-hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-hibák|TX-hibák összesen|Count|Average|Average_Total TX-hibák|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total ütközések|Ütközések összesen|Count|Average|Average_Total ütközések|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Átl. Lemez mp/Olvasás|Count|Average|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/átvitel|Átl. Lemez mp/átvitel|Count|Average|Average_Avg. Lemez mp/átvitel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Átl. Lemez mp/írás|Count|Average|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Fizikai lemez sebessége (bájt/s)|Count|Average|Average_Physical lemez sebessége (bájt/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct-jogosultságú idő|PCT rendszerjogosultságú idő|Count|Average|Average_Pct-jogosultságú idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|PCT felhasználói idő|Count|Average|Average_Pct felhasználói idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Felhasznált memória (kilobájt)|Count|Average|Average_Used memória kilobájtban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Virtuális megosztott memória|Count|Average|Average_Virtual megosztott memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC idő|% DPC idő|Count|Average|Average_% DPC idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% üresjárati idő|Üresjárati idő%-ban|Count|Average|Average_% üresjárati idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os megszakítási ideje|Megszakítási idő%-ban|Count|Average|Average_%-os megszakítási ideje|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO várakozási idő|I/o várakozási idő%-ban|Count|Average|Average_% IO várakozási idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os szép idő|% Nice idő|Count|Average|Average_%-os szép idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|%-Os privilegizált idő|Count|Average|Average_%-os privilegizált idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os processzoridő|Processzoridő|Count|Average|Average_%-os processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Felhasználói idő%-ban|Count|Average|Average_%-os felhasználói idő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fizikai memória|Szabad fizikai memória|Count|Average|Average_Free fizikai memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free terület a Lapozófájlokban|Szabad terület a Lapozófájlokban|Count|Average|Average_Free terület a Lapozófájlokban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuális memória|Szabad virtuális memória|Count|Average|Average_Free virtuális memória|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Folyamatok|Count|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Lapozófájlokban tárolt méret|Count|Average|Lapozófájlokban tárolt Average_Size|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Hasznos üzemidő|Count|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Felhasználók|Count|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/Olvasás|Átl. Lemez mp/Olvasás|Count|Average|Average_Avg. Lemez mp/Olvasás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Lemez mp/írás|Átl. Lemez mp/írás|Count|Average|Average_Avg. Lemez mp/írás|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current-várólista hossza|Lemez aktuális várólistájának hossza|Count|Average|Average_Current-várólista hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Olvasási sebesség (Average_Disk/s)|Lemezolvasások/mp|Count|Average|Olvasási sebesség (Average_Disk/s)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk adatátvitel gyakorisága (mp)|Átvitel/mp|Count|Average|Average_Disk adatátvitel gyakorisága (mp)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk írás gyakorisága (művelet/mp)|Lemezírások/mp|Count|Average|Average_Disk írás gyakorisága (művelet/mp)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free (MB)|Szabad hely MB-ban|Count|Average|Average_Free (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ szabad területe (%)|% Szabad terület|Count|Average|Average_ szabad területe (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB-ban|Rendelkezésre álló memória|Count|Average|Average_Available MB-ban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Előjegyzett memória%-ban használatban|Count|Average|Average_%-os előjegyzett bájtok használatban|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Fogadott Average_Bytes/s|Fogadott bájtok/mp|Count|Average|Fogadott Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes elküldésének gyakorisága (mp)|Küldött bájtok/s|Count|Average|Average_Bytes elküldésének gyakorisága (mp)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Összes bájt/mp|Count|Average|Average_Bytes összesen/mp|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_%-os processzoridő|Processzoridő|Count|Average|Average_%-os processzoridő|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor-várólista hossza|Processzor-várólista hossza|Count|Average|Average_Processor-várólista hossza|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Szívverés|Szívverés|Count|Összes|Szívverés|Computer, OSType, Version, SourceComputerId|
|frissítés|frissítés|Count|Average|frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|
|Esemény|Esemény|Count|Average|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Lekérdezés időtartama|Ezredmásodperc|Average|DAX-lekérdezés időtartama az utolsó intervallumban|Nincs dimenzió|
|QueryPoolJobQueueLength|Szálak Lekérdezési készlet feladatok várólistájának hossza|Count|Average|A lekérdezési szál készletének várólistájában lévő feladatok száma.|Nincs dimenzió|
|qpu_high_utilization_metric|QPU magas kihasználtsága|Count|Összes|QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0|Nincs dimenzió|
|memory_metric|Memory (Memória)|Bájt|Average|Memória. 0-3 GB-os tartomány a1, 0-5 GB az a2-hez, 0-10 GB az a3-hoz, 0-25 GB a4-hez, 0-50 GB az A5-hez és az A6-os 0-100 GB-hoz|Nincs dimenzió|
|memory_thrashing_metric|Memória-Kiverés|Percent|Average|Memória átlagos kiverése.|Nincs dimenzió|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Összes|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Összes|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Összes|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Összes|A Microsoft. Relay sikeres feladói kapcsolatokra.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Összes|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Összes|A Microsoft. Relay feladói kapcsolatokra ServerError.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Összes|A Microsoft. Relay Figyelőkapcsolatokra összesen.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Összes|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|EntityName|
|ActiveConnections|ActiveConnections|Count|Összes|A Microsoft. Relay aktív kapcsolatai összesen.|EntityName|
|AktívFigyelők|AktívFigyelők|Count|Összes|A Microsoft. Relay ActiveListeners összesen.|EntityName|
|ÁtvittBájtok|ÁtvittBájtok|Count|Összes|A Microsoft. Relay BytesTransferred összesen.|EntityName|
|Figyelőkapcsolat-bontások|Figyelőkapcsolat-bontások|Count|Összes|A Microsoft. Relay bontásai összesen.|EntityName|
|Feladóikapcsolat-bontások|Feladóikapcsolat-bontások|Count|Összes|A Microsoft. Relay SenderDisconnects összesen.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|Másodperc|Average|Keresési szolgáltatás átlagos keresési késése|Nincs dimenzió|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|Egység/s|Average|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|Nincs dimenzió|
|ThrottledSearchQueriesPercentage|Szabályozott lekérdezések százalékos aránya|Percent|Average|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|Nincs dimenzió|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések (előzetes verzió)|Count|Összes|A névtér összes sikeres kérelme (előzetes verzió)|EntityName|
|Kiszolgálóhibái|Kiszolgálói hibák. (Előzetes verzió)|Count|Összes|Kiszolgálói hibák a Microsoft. ServiceBus esetében. (Előzetes verzió)|EntityName|
|UserErrors|Felhasználói hibák. (Előzetes verzió)|Count|Összes|A Microsoft. ServiceBus felhasználói hibái. (Előzetes verzió)|EntityName|
|ThrottledRequests|Szabályozott kérelmek. (Előzetes verzió)|Count|Összes|A Microsoft. ServiceBus által szabályozott kérelmek. (Előzetes verzió)|EntityName|
|IncomingRequests|Bejövő kérések (előzetes verzió)|Count|Összes|A Microsoft. ServiceBus bejövő kérései. (Előzetes verzió)|EntityName|
|IncomingMessages|Bejövő üzenetek (előzetes verzió)|Count|Összes|A Microsoft. ServiceBus bejövő üzenetei. (Előzetes verzió)|EntityName|
|OutgoingMessages|Kimenő üzenetek (előzetes verzió)|Count|Összes|A Microsoft. ServiceBus kimenő üzenetei. (Előzetes verzió)|EntityName|
|ActiveConnections|Aktív kapcsolatai (előzetes verzió)|Count|Összes|A Microsoft. ServiceBus összes aktív kapcsolata. (Előzetes verzió)|Nincs dimenzió|
|Size|Méret (előzetes verzió)|Bájt|Average|Várólista/témakör mérete bájtban. (Előzetes verzió)|EntityName|
|Üzenetek|Üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|Count|Average|Üzenetsor vagy témakör üzeneteinek száma. (Előzetes verzió)|EntityName|
|ActiveMessages|Üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|Count|Average|Üzenetsor vagy témakör aktív üzeneteinek száma. (Előzetes verzió)|EntityName|
|DeadletteredMessages|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. (Előzetes verzió)|Count|Average|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. (Előzetes verzió)|EntityName|
|ScheduledMessages|Az üzenetsor/témakör ütemezett üzeneteinek száma. (Előzetes verzió)|Count|Average|Az üzenetsor/témakör ütemezett üzeneteinek száma. (Előzetes verzió)|EntityName|
|CPUXNS|Processzorhasználat névterenként|Percent|Maximum|A Service Bus Premium névtér CPU-használati metrikája|Nincs dimenzió|
|WSXNS|Felhasznál memória mérete névterenként|Percent|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma|Nincs dimenzió|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|A tárolóhoz a millicores-ben lefoglalt processzor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bájt|Average|A tároló számára lefoglalt memória (MB)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Tényleges CPU-használat a millicores-ben|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bájt|Average|Tényleges memóriahasználat MB-ban|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Average|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Average|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|ServiceStatus|ServiceStatus|Count|Average|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Count|Average|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ConnectionCount|Kapcsolatok száma|Count|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|MessageCount|Üzenetek száma|Count|Összes|Az üzenetek teljes mennyisége.|Nincs dimenzió|
|InboundTraffic|Bejövő forgalom|Bájt|Összes|A szolgáltatás bejövő forgalma|Nincs dimenzió|
|OutboundTraffic|Kimenő forgalom|Bájt|Összes|A szolgáltatás kimenő forgalma|Nincs dimenzió|
|UserErrors|Felhasználói hibák|Percent|Maximum|A felhasználói hibák százalékos aránya|Nincs dimenzió|
|SystemErrors|Rendszerhibák|Percent|Maximum|A rendszerhibák százalékos aránya|Nincs dimenzió|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO százalékos aránya|Percent|Average|Adat IO százalékos aránya|Nincs dimenzió|
|log_write_percent|Naplózási IO százalékos aránya|Percent|Average|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|dtu_consumption_percent|DTU százalékos értéke|Percent|Average|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|storage|Használatban lévő adatterület|Bájt|Maximum|Az adatbázis teljes mérete. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|connection_successful|Sikeres kapcsolatok|Count|Összes|Sikeres kapcsolatok|Nincs dimenzió|
|connection_failed|Sikertelen kapcsolatok|Count|Összes|Sikertelen kapcsolatok|Nincs dimenzió|
|blocked_by_firewall|Tűzfal blokkolja|Count|Összes|Tűzfal blokkolja|Nincs dimenzió|
|holtpont|Holtpontok|Count|Összes|Holtpontok. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|storage_percent|Felhasznált adatterület százalékos aránya|Percent|Maximum|Az adatbázis mérete százalékban megadva. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|Nincs dimenzió|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Percent|Average|Memóriában tárolt OLTP-tárolási százalék. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|workers_percent|Munkavégzők százalékos aránya|Percent|Average|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|sessions_percent|Munkamenetek százalékos aránya|Percent|Average|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|
|dtu_limit|DTU korlátja|Count|Average|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|dtu_used|Használt DTU|Count|Average|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|cpu_limit|CPU-korlát|Count|Average|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|cpu_used|Felhasznált CPU|Count|Average|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincs dimenzió|
|dwu_limit|DWU korlátja|Count|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|dwu_consumption_percent|DWU százalékos aránya|Percent|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|dwu_used|Használt DWU|Count|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|dw_cpu_percent|A DW-csomópont szintjének CPU-aránya|Percent|Average|A DW-csomópont szintjének CPU-aránya|DwLogicalNodeId|
|dw_physical_data_read_percent|A DW-csomópont szintű adat IO-aránya|Percent|Average|A DW-csomópont szintű adat IO-aránya|DwLogicalNodeId|
|cache_hit_percent|Gyorsítótár találati százaléka|Percent|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|cache_used_percent|Gyorsítótár használt százaléka|Percent|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|local_tempdb_usage_percent|Helyi tempdb százalékos aránya|Percent|Average|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|Nincs dimenzió|
|app_cpu_billed|CPU alapján számlázott alkalmazás|Count|Összes|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincs dimenzió|
|app_cpu_percent|Alkalmazás CPU-aránya|Percent|Average|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincs dimenzió|
|app_memory_percent|Alkalmazás memóriájában használt százalék|Percent|Average|Az alkalmazás memóriájában használt százalék. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincs dimenzió|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Average|Lefoglalt adatterület. Az adattárházak esetében nem alkalmazható.|Nincs dimenzió|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Nincs dimenzió|
|physical_data_read_percent|Adat IO százalékos aránya|Percent|Average|Adat IO százalékos aránya|Nincs dimenzió|
|log_write_percent|Naplózási IO százalékos aránya|Percent|Average|Naplózási IO százalékos aránya|Nincs dimenzió|
|dtu_consumption_percent|DTU százalékos értéke|Percent|Average|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|storage_percent|Felhasznált adatterület százalékos aránya||Percent|Average|Tárolási százalék|Nincs dimenzió|
|workers_percent|Munkavégzők százalékos aránya|Percent|Average|Munkavégzők százalékos aránya|Nincs dimenzió|
|sessions_percent|Munkamenetek százalékos aránya|Percent|Average|Munkamenetek százalékos aránya|Nincs dimenzió|
|eDTU_limit|eDTU korlátja|Count|Average|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|storage_limit|Adatok maximális mérete|Bájt|Average|Tárolási korlát|Nincs dimenzió|
|eDTU_used|használt eDTU|Count|Average|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|storage_used|Használatban lévő adatterület|Bájt|Average|Felhasznált tároló|Nincs dimenzió|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Percent|Average|Memóriában tárolt OLTP tárolási százaléka|Nincs dimenzió|
|cpu_limit|CPU-korlát|Count|Average|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|cpu_used|Felhasznált CPU|Count|Average|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincs dimenzió|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Average|Lefoglalt adatterület|Nincs dimenzió|
|allocated_data_storage_percent|Lefoglalt adatterület százalékos aránya|Percent|Maximum|Lefoglalt adatterület százalékos aránya|Nincs dimenzió|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|virtual_core_count|Virtuális mag száma|Count|Average|Virtuális mag száma|Nincs dimenzió|
|avg_cpu_percent|Átlagos CPU-százalék|Percent|Average|Átlagos CPU-százalék|Nincs dimenzió|
|reserved_storage_mb|Tárterület fenntartva|Count|Average|Tárterület fenntartva|Nincs dimenzió|
|storage_space_used_mb|Felhasznált tárterület|Count|Average|Felhasznált tárterület|Nincs dimenzió|
|io_requests|IO-kérelmek száma|Count|Average|IO-kérelmek száma|Nincs dimenzió|
|io_bytes_read|I/o-bájtok olvasása|Bájt|Average|I/o-bájtok olvasása|Nincs dimenzió|
|io_bytes_written|I/o-bájtok írása|Bájt|Average|I/o-bájtok írása|Nincs dimenzió|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Használt kapacitás|Bájt|Átlag|A fiók felhasznált kapacitása|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|Blob-kapacitása|Bájt|Average|A tárfiók Blob Service-példánya által felhasznált tárterület mérete bájtban megadva.|BlobType, Tier|
|BlobCount|Blobok száma|Darabszám|Összes|A tárfiók Blob Service-példányában található blobok száma.|BlobType|       |BlobCount|Blobok száma|Count|Average|A tárfiók Blob Service-példányában található blobok száma.|BlobType, Tier|
|ContainerCount|Blobtárolók száma|Darabszám|Átlag|A tárfiók Blob Service-példányában található tárolók száma.|Nincs dimenzió|
|IndexCapacity|Indexkapacitás|Bájt|Average|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|File Storage kapacitása|Bájt|Average|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|FileCount|Fájlok száma|Count|Average|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Nincs dimenzió|
|FileShareCount|Fájlmegosztások száma|Count|Average|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Queue Storage kapacitása|Bájt|Átlag|A tárfiók Queue Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|QueueCount|Üzenetsorok száma|Darabszám|Átlag|A tárfiók Queue-szolgáltatás-példányában található üzenetsorok száma.|Nincs dimenzió|
|QueueMessageCount|Üzenetsorbeli üzenetek száma|Darabszám|Átlag|A tárfiók Queue Storage-szolgáltatás-példányában található üzenetsorbeli üzenetek hozzávetőleges száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Table Storage kapacitása|Bájt|Átlag|A tárfiók Table Storage-szolgáltatás-példánya által felhasznált tárterület mérete bájtban megadva.|Nincs dimenzió|
|TableCount|Táblák száma|Darabszám|Átlag|A tárfiók Table Storage-szolgáltatás-példányában található táblák száma.|Nincs dimenzió|
|TableEntityCount|Táblaentitások száma|Darabszám|Átlag|A tárfiók Table Storage-szolgáltatás-példányában található táblaentitások száma.|Nincs dimenzió|
|Tranzakciók|Tranzakciók|Darabszám|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérelmeket, valamint a hibák kéréseket tartalmazza. Különböző típusú válaszok számának használja a ResponseType dimenziót.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összes|A bejövő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a hálózati késés megadott AverageE2ELatency.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állási a TotalBillableRequests értékét és értékkel való osztásának alkalmazható kérések, ki nem várt hibák száma alapján számítjuk. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Szinkronizálási munkamenet eredménye|Count|Average|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Szinkronizált fájlok|Count|Összes|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Count|Összes|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összes|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Kiszolgáló online állapota|Count|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a regisztrált kiszolgáló sikeresen rögzített egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Felhőbeli rétegek felidézése|Bájt|Összes|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU százalékos kihasználtsága|Percent|Maximum|SU százalékos kihasználtsága|LogicalName, PartitionId|
|InputEvents|Bemeneti események|Count|Összes|Bemeneti események|LogicalName, PartitionId|
|InputEventBytes|Bemeneti esemény bájtokban|Bájt|Összes|Bemeneti esemény bájtokban|LogicalName, PartitionId|
|LateInputEvents|Késedelmes bemeneti események|Count|Összes|Késedelmes bemeneti események|LogicalName, PartitionId|
|OutputEvents|Kimeneti események|Count|Összes|Kimeneti események|LogicalName, PartitionId|
|ConversionErrors|Adatkonverziós hibák|Count|Összes|Adatkonverziós hibák|LogicalName, PartitionId|
|Hibák|Futásidejű hibák|Count|Összes|Futásidejű hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Üzemen kívüli események|Count|Összes|Üzemen kívüli események|LogicalName, PartitionId|
|AMLCalloutRequests|Függvénykérések|Count|Összes|Függvénykérések|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Sikertelen függvénykérések|Count|Összes|Sikertelen függvénykérések|LogicalName, PartitionId|
|AMLCalloutInputEvents|Függvényesemények|Count|Összes|Függvényesemények|LogicalName, PartitionId|
|DeserializationError|A deszerializálás bemeneti hibái|Count|Összes|A deszerializálás bemeneti hibái|LogicalName, PartitionId|
|EarlyInputEvents|Korai bemeneti események|Count|Összes|Korai bemeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Vízjel-késleltetés|Másodperc|Maximum|Vízjel-késleltetés|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Várakozó bemeneti események|Count|Maximum|Várakozó bemeneti események|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Fogadott bemeneti források|Count|Összes|Fogadott bemeneti források|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Count|Összes|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|Nincs dimenzió|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Count|Összes|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|Nincs dimenzió|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összes|Az összes eseményforrás által olvasott bájtok száma|Nincs dimenzió|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összes|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincs dimenzió|
|IngressStoredEvents|Bejövő tárolt események|Count|Összes|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincs dimenzió|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincs dimenzió|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Count|Average|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenet sorszáma közötti különbség|Nincs dimenzió|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Count|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincs dimenzió|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Count|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincs dimenzió|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Count|Összes|Az eseményforrás által olvasott üzenetek száma|Nincs dimenzió|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Count|Összes|Az eseményforrás által beolvasott érvénytelen üzenetek száma|Nincs dimenzió|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összes|Az eseményforrás által beolvasott bájtok száma|Nincs dimenzió|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összes|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincs dimenzió|
|IngressStoredEvents|Bejövő tárolt események|Count|Összes|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincs dimenzió|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincs dimenzió|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Count|Average|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenet sorszáma közötti különbség|Nincs dimenzió|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Count|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincs dimenzió|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Count|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincs dimenzió|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Average|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincs dimenzió|
|DiskWriteBytesPerSecond|Lemez írási sebessége (bájt/s)|BytesPerSecond|Average|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|Nincs dimenzió|
|Lemezről beolvasott bájtok|Lemezről beolvasott bájtok|Bájt|Összes|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincs dimenzió|
|Lemezre írt bájtok|Lemezre írt bájtok|Bájt|Összes|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|Nincs dimenzió|
|DiskReadOperations|Lemezes olvasási műveletek|Count|Összes|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincs dimenzió|
|DiskWriteOperations|Lemez írási műveletei|Count|Összes|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincs dimenzió|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|Egység/s|Average|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincs dimenzió|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|Egység/s|Average|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincs dimenzió|
|DiskReadLatency|Lemez olvasási késése|Ezredmásodperc|Average|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|Nincs dimenzió|
|DiskWriteLatency|Lemez írási késése|Ezredmásodperc|Average|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|Nincs dimenzió|
|NetworkInBytesPerSecond|Hálózat bájt/mp-ben|BytesPerSecond|Average|A fogadott forgalom átlagos hálózati átviteli sebessége.|Nincs dimenzió|
|NetworkOutBytesPerSecond|Hálózati kimenő bájtok/s|BytesPerSecond|Average|A továbbított forgalom átlagos hálózati átviteli sebessége.|Nincs dimenzió|
|Hálózat bejövő adatforgalma|Hálózat bejövő adatforgalma|Bájt|Összes|A fogadott forgalom összes hálózati átviteli sebessége.|Nincs dimenzió|
|Hálózat kimenő adatforgalma|Hálózat kimenő adatforgalma|Bájt|Összes|A továbbított forgalom összes hálózati átviteli sebessége.|Nincs dimenzió|
|MemoryUsed|Felhasznált memória|Bájt|Average|A virtuális gép által használt gépi memória mennyisége.|Nincs dimenzió|
|MemoryGranted|Megadott memória|Bájt|Average|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|Nincs dimenzió|
|MemoryActive|Memória aktív|Bájt|Average|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|Nincs dimenzió|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Average|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|Nincs dimenzió|
|PercentageCpuReady|CPU-készültség százalékos aránya|Ezredmásodperc|Összes|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|Nincs dimenzió|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Percent|Average|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Count|Average|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Count|Average|HTTP-várólista hossza|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (a függvények kivételével)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|Processzoridő|Másodperc|Összes|Processzoridő|Példány|
|Kérelmek|Kérelmek|Count|Összes|Kérelmek|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Count|Összes|HTTP 101|Példány|
|Http2xx|Http 2xx|Count|Összes|Http 2xx|Példány|
|Http3xx|HTTP 3xx|Count|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401|Count|Összes|HTTP 401|Példány|
|Http403|HTTP 403|Count|Összes|HTTP 403|Példány|
|Http404|Http 404|Count|Összes|Http 404|Példány|
|Http406|HTTP 406|Count|Összes|HTTP 406|Példány|
|Http4xx|Http 4xx|Count|Összes|Http 4xx|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Count|Összes|HTTP-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória-munkakészlet|Bájt|Average|Memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Average|Átlagos memória-munkakészlet|Példány|
|AverageResponseTime|Átlagos válaszidő|Másodperc|Average|Átlagos válaszidő|Példány|
|AppConnections|Kapcsolatok|Count|Average|Kapcsolatok|Példány|
|Leírók|Leírók száma|Count|Average|Leírók száma|Példány|
|Szálak|Szálak száma|Count|Average|Szálak száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Average|Saját [nem megosztható] memória (bájt)|Példány|
|IoReadBytesPerSecond|I/O – olvasás (bájt/másodperc)|BytesPerSecond|Összes|I/O – olvasás (bájt/másodperc)|Példány|
|IoWriteBytesPerSecond|I/O – írás (bájt/másodperc)|BytesPerSecond|Összes|I/O – írás (bájt/másodperc)|Példány|
|IoOtherBytesPerSecond|I/O – egyéb (bájt/másodperc)|BytesPerSecond|Összes|I/O – egyéb (bájt/másodperc)|Példány|
|IoReadOperationsPerSecond|I/O – olvasás (művelet/másodperc)|BytesPerSecond|Összes|I/O – olvasás (művelet/másodperc)|Példány|
|IoWriteOperationsPerSecond|I/O – írás (művelet/másodperc)|BytesPerSecond|Összes|I/O – írás (művelet/másodperc)|Példány|
|IoOtherOperationsPerSecond|I/O – egyéb (művelet/másodperc)|BytesPerSecond|Összes|I/O – egyéb (művelet/másodperc)|Példány|
|RequestsInApplicationQueue|Alkalmazás-várólistán lévő kérelmek|Count|Average|Alkalmazás-várólistán lévő kérelmek|Példány|
|CurrentAssemblies|Szerelvények pillanatnyi száma|Count|Average|Szerelvények pillanatnyi száma|Példány|
|TotalAppDomains|Alkalmazástartományok összesen|Count|Average|Alkalmazástartományok összesen|Példány|
|TotalAppDomainsUnloaded|Memóriából eltávolított alkalmazástartományok összesen|Count|Average|Memóriából eltávolított alkalmazástartományok összesen|Példány|
|Gen0Collections|0\. generációs szemétgyűjtések száma|Count|Összes|0\. generációs szemétgyűjtések száma|Példány|
|Gen1Collections|1\. generációs szemétgyűjtések száma|Count|Összes|1\. generációs szemétgyűjtések száma|Példány|
|Gen2Collections|2\. generációs szemétgyűjtések száma|Count|Összes|2\. generációs szemétgyűjtések száma|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (functions)

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Count|Összes|HTTP-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória-munkakészlet|Bájt|Average|Memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Average|Átlagos memória-munkakészlet|Példány|
|FunctionExecutionUnits|Függvény-végrehajtási egység|MB/ezredmásodperc|Összes|[Függvények végrehajtási egységei](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Count|Összes|Függvény végrehajtásainak száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Average|Saját [nem megosztható] memória (bájt)|Példány|
|IoReadBytesPerSecond|I/O – olvasás (bájt/másodperc)|BytesPerSecond|Összes|I/O – olvasás (bájt/másodperc)|Példány|
|IoWriteBytesPerSecond|I/O – írás (bájt/másodperc)|BytesPerSecond|Összes|I/O – írás (bájt/másodperc)|Példány|
|IoOtherBytesPerSecond|I/O – egyéb (bájt/másodperc)|BytesPerSecond|Összes|I/O – egyéb (bájt/másodperc)|Példány|
|IoReadOperationsPerSecond|I/O – olvasás (művelet/másodperc)|BytesPerSecond|Összes|I/O – olvasás (művelet/másodperc)|Példány|
|IoWriteOperationsPerSecond|I/O – írás (művelet/másodperc)|BytesPerSecond|Összes|I/O – írás (művelet/másodperc)|Példány|
|IoOtherOperationsPerSecond|I/O – egyéb (művelet/másodperc)|BytesPerSecond|Összes|I/O – egyéb (művelet/másodperc)|Példány|
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
|CpuTime|Processzoridő|Másodperc|Összes|Processzoridő|Példány|
|Kérelmek|Kérelmek|Count|Összes|Kérelmek|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Count|Összes|HTTP 101|Példány|
|Http2xx|Http 2xx|Count|Összes|Http 2xx|Példány|
|Http3xx|HTTP 3xx|Count|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401|Count|Összes|HTTP 401|Példány|
|Http403|HTTP 403|Count|Összes|HTTP 403|Példány|
|Http404|Http 404|Count|Összes|Http 404|Példány|
|Http406|HTTP 406|Count|Összes|HTTP 406|Példány|
|Http4xx|Http 4xx|Count|Összes|Http 4xx|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Count|Összes|HTTP-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória-munkakészlet|Bájt|Average|Memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memória-munkakészlet|Bájt|Average|Átlagos memória-munkakészlet|Példány|
|AverageResponseTime|Átlagos válaszidő|Másodperc|Average|Átlagos válaszidő|Példány|
|FunctionExecutionUnits|Függvény-végrehajtási egység|Count|Összes|Függvény-végrehajtási egység|Példány|
|FunctionExecutionCount|Függvény végrehajtásainak száma|Count|Összes|Függvény végrehajtásainak száma|Példány|
|AppConnections|Kapcsolatok|Count|Average|Kapcsolatok|Példány|
|Leírók|Leírók száma|Count|Average|Leírók száma|Példány|
|Szálak|Szálak száma|Count|Average|Szálak száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Average|Saját [nem megosztható] memória (bájt)|Példány|
|IoReadBytesPerSecond|I/O – olvasás (bájt/másodperc)|BytesPerSecond|Összes|I/O – olvasás (bájt/másodperc)|Példány|
|IoWriteBytesPerSecond|I/O – írás (bájt/másodperc)|BytesPerSecond|Összes|I/O – írás (bájt/másodperc)|Példány|
|IoOtherBytesPerSecond|I/O – egyéb (bájt/másodperc)|BytesPerSecond|Összes|I/O – egyéb (bájt/másodperc)|Példány|
|IoReadOperationsPerSecond|I/O – olvasás (művelet/másodperc)|BytesPerSecond|Összes|I/O – olvasás (művelet/másodperc)|Példány|
|IoWriteOperationsPerSecond|I/O – írás (művelet/másodperc)|BytesPerSecond|Összes|I/O – írás (művelet/másodperc)|Példány|
|IoOtherOperationsPerSecond|I/O – egyéb (művelet/másodperc)|BytesPerSecond|Összes|I/O – egyéb (művelet/másodperc)|Példány|
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
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|HTTP 101|Count|Összes|HTTP 101|Példány|
|Http2xx|Http 2xx|Count|Összes|Http 2xx|Példány|
|Http3xx|HTTP 3xx|Count|Összes|HTTP 3xx|Példány|
|Http401|HTTP 401|Count|Összes|HTTP 401|Példány|
|Http403|HTTP 403|Count|Összes|HTTP 403|Példány|
|Http404|Http 404|Count|Összes|Http 404|Példány|
|Http406|HTTP 406|Count|Összes|HTTP 406|Példány|
|Http4xx|Http 4xx|Count|Összes|Http 4xx|Példány|
|Http5xx|HTTP-kiszolgálói hibák|Count|Összes|HTTP-kiszolgálói hibák|Példány|
|AverageResponseTime|Átlagos válaszidő|Másodperc|Average|Átlagos válaszidő|Példány|
|CpuPercentage|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Percent|Average|Memóriahasználat (%)|Példány|
|DiskQueueLength|Lemezvárólista hossza|Count|Average|Lemezvárólista hossza|Példány|
|HttpQueueLength|HTTP-várólista hossza|Count|Average|HTTP-várólista hossza|Példány|
|ActiveRequests|Aktív kérelmek|Count|Összes|Aktív kérelmek|Példány|
|TotalFrontEnds|Előterek száma|Count|Average|Előterek száma|Nincs dimenzió|
|SmallAppServicePlanInstances|Kis méretű App Service-csomag feldolgozói|Count|Average|Kis méretű App Service-csomag feldolgozói|Nincs dimenzió|
|MediumAppServicePlanInstances|Közepes méretű App Service-csomag feldolgozói|Count|Average|Közepes méretű App Service-csomag feldolgozói|Nincs dimenzió|
|LargeAppServicePlanInstances|Nagy méretű App Service-csomag feldolgozói|Count|Average|Nagy méretű App Service-csomag feldolgozói|Nincs dimenzió|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenített neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WorkersTotal|Feldolgozók összesen|Count|Average|Feldolgozók összesen|Nincs dimenzió|
|WorkersAvailable|Rendelkezésre álló feldolgozók|Count|Average|Rendelkezésre álló feldolgozók|Nincs dimenzió|
|WorkersUsed|Használt feldolgozók|Count|Average|Használt feldolgozók|Nincs dimenzió|
|CpuPercentage|Processzorhasználat (%)|Percent|Average|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memóriahasználat (%)|Percent|Average|Memóriahasználat (%)|Példány|

## <a name="next-steps"></a>További lépések
* [Tudnivalók a Azure Monitor mérőszámokról](data-platform.md)
* [Riasztások létrehozása mérőszámokon](alerts-overview.md)
* [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](diagnostic-logs-overview.md)
