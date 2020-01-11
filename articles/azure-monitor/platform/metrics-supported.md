---
title: Azure Monitor támogatott metrikák erőforrástípus szerint
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 67e2675dbf65daf929407a437447f5d977c7a6c3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75750405"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák Azure Monitor

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. Alább látható az Azure Monitor metrikus folyamatával jelenleg elérhető metrikák teljes listája. Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők. Az alábbi lista csak a konszolidált Azure Monitor metrikai folyamat használatával elérhető metrikákat tartalmazza. A metrikák lekérdezéséhez és eléréséhez használja a [2018-01-01 API-Version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
> A diagnosztikai beállítások használatával exportálható platform-metrikák listáját [ebben a cikkben](metrics-supported-export-diagnostic-settings.md)találja.




## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Mennyiség|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|memory_metric|Memória|Bájt|Átlag|Memory. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|private_bytes_metric|Saját [nem megosztható] memória (bájt)|Bájt|Átlag|Saját bájtok.|ServerResourceType|
|virtual_bytes_metric|Felhasznált virtuális memória jelenlegi mérete (bájt)|Bájt|Átlag|Virtuális bájtok.|ServerResourceType|
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
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet – feladatok várólistájának hossza|Mennyiség|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak: rövid elemzés – foglalt szálak|Mennyiség|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Szálak: rövid elemzési üresjárati szálak|Mennyiség|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak: rövid elemzési feladatok várólistájának hossza|Mennyiség|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|ServerResourceType|
|mashup_engine_qpu_metric|M motor QPU|Mennyiség|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor saját bájtjai|Bájt|Átlag|Saját bájtok használata az adategyesítési motor folyamatai alapján.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M motor virtuális bájtjai|Bájt|Átlag|Az adategyesítési motor folyamatai által használt virtuális bájtok használata.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Összes átjáró kérelme (elavult)|Mennyiség|Összes|Átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|SuccessfulRequests|Sikeres átjáró-kérelmek (elavult)|Mennyiség|Összes|Sikeres átjáró-kérések száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzió helyett|Hely, állomásnév|
|UnauthorizedRequests|Nem engedélyezett átjáró-kérelmek (elavult)|Mennyiség|Összes|Jogosulatlan átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|FailedRequests|Sikertelen átjáró-kérelmek (elavult)|Mennyiség|Összes|Hibák száma az átjáró kéréseiben – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|OtherRequests|Egyéb átjáró-kérelmek (elavult)|Mennyiség|Összes|Egyéb átjáró-kérelmek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|Időtartam|Az átjárók kéréseinek teljes időtartama|Ezredmásodperc|Átlag|Az átjáró kérelmek teljes időtartama ezredmásodpercben|Hely, állomásnév|
|BackendDuration|A háttérbeli kérelmek időtartama|Ezredmásodperc|Átlag|A háttérbeli kérelmek időtartama (ezredmásodpercben)|Hely, állomásnév|
|Kapacitás|Kapacitás|Százalék|Átlag|A ApiManagement szolgáltatás kihasználtsági metrikája|Földrajzi egység|
|EventHubTotalEvents|Összes EventHub esemény|Mennyiség|Összes|A EventHub eljuttatott események száma|Földrajzi egység|
|EventHubSuccessfulEvents|Sikeres EventHub események|Mennyiség|Összes|Sikeres EventHub események száma|Földrajzi egység|
|EventHubTotalFailedEvents|Sikertelen EventHub események|Mennyiség|Összes|Sikertelen EventHub események száma|Földrajzi egység|
|EventHubRejectedEvents|EventHub-események visszautasítva|Mennyiség|Összes|Visszautasított EventHub-események száma (helytelen konfiguráció vagy jogosulatlan)|Földrajzi egység|
|EventHubThrottledEvents|Szabályozott EventHub események|Mennyiség|Összes|A szabályozott EventHub események száma|Földrajzi egység|
|EventHubTimedoutEvents|Időtúllépés a EventHub eseményeinél|Mennyiség|Összes|Az időtúllépés miatti EventHub események száma|Földrajzi egység|
|EventHubDroppedEvents|Eldobott EventHub események|Mennyiség|Összes|A várólista-méretkorlát elérte miatt kihagyott események száma|Földrajzi egység|
|EventHubTotalBytesSent|EventHub-események mérete|Bájt|Összes|EventHub-események teljes mérete bájtban|Földrajzi egység|
|Kérelmek|Kérelmek|Mennyiség|Összes|Több dimenzióval rendelkező átjáró-kérelmek metrikái|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Mennyiség|Mennyiség|A bejövő HTTP-kérelmek teljes száma.|None|
|FailedHttpRequestCount|FailedHttpRequestCount|Mennyiség|Mennyiség|Sikertelen HTTP-kérelmek.|None|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Mennyiség|Átlag|Egy HTTP-kérelem késése.|None|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|CPU-használat százalékos aránya|Százalék|Átlag|A legújabb CPU-használat a teljes rendszerhez|AppName, Pod|
|AppCpuUsagePercentage|Alkalmazás CPU-kihasználtságának százalékos aránya|Százalék|Átlag|Alkalmazás JVM CPU-kihasználtsági aránya|AppName, Pod|
|AppMemoryCommitted|Az alkalmazáshoz hozzárendelt memória|Bájt|Átlag|JVM rendelt memória bájtban|AppName, Pod|
|AppMemoryUsed|Használt alkalmazás memóriája|Bájt|Átlag|Az alkalmazás memóriája bájtban használatban|AppName, Pod|
|AppMemoryMax|Az alkalmazás memóriájának maximális száma|Bájt|Maximum|A memória-kezeléshez használható memória maximális mennyisége bájtban|AppName, Pod|
|MaxOldGenMemoryPoolBytes|A rendelkezésre álló régi generációs adatméret maximális száma|Bájt|Átlag|A régi generációs memória maximális mérete|AppName, Pod|
|OldGenMemoryPoolBytes|A régi generációs adatméret|Bájt|Átlag|Régi generációs memória-készlet mérete teljes GC után|AppName, Pod|
|OldGenPromotedBytes|Előléptetés a régi generációs adatméretre|Bájt|Maximum|A régi generációs memória-készlet méretének pozitív növekedésének száma, mielőtt a GC a GC-re lenne|AppName, Pod|
|YoungGenPromotedBytes|Népszerűsítse a fiatal generáció adatméretét|Bájt|Maximum|Növekszik a fiatal generációs memória-készlet méretének növekedésével, miután egy GC a következőre kerül|AppName, Pod|
|GCPauseTotalCount|GC-szüneteltetések száma|Mennyiség|Összes|GC-szüneteltetések száma|AppName, Pod|
|GCPauseTotalTime|GC felfüggesztésének teljes ideje|Ezredmásodperc|Összes|GC felfüggesztésének teljes ideje|AppName, Pod|
|TomcatSentBytes|Tomcat összesen eljuttatott bájtok száma|Bájt|Összes|Tomcat összesen eljuttatott bájtok száma|AppName, Pod|
|TomcatReceivedBytes|Tomcat összesen fogadott bájtok|Bájt|Összes|Tomcat összesen fogadott bájtok|AppName, Pod|
|TomcatRequestTotalTime|Tomcat-kérelem teljes ideje|Ezredmásodperc|Összes|Tomcat-kérelem teljes ideje|AppName, Pod|
|TomcatRequestTotalCount|Tomcat-kérelem összesített száma|Mennyiség|Összes|Tomcat-kérelem összesített száma|AppName, Pod|
|TomcatResponseAvgTime|Tomcat-kérelem átlagos ideje|Ezredmásodperc|Átlag|Tomcat-kérelem átlagos ideje|AppName, Pod|
|TomcatRequestMaxTime|Tomcat-kérelem maximális ideje|Ezredmásodperc|Maximum|Tomcat-kérelem maximális ideje|AppName, Pod|
|TomcatErrorCount|Tomcat globális hiba|Mennyiség|Összes|Tomcat globális hiba|AppName, Pod|
|TomcatSessionActiveMaxCount|Tomcat-munkamenet maximális aktív száma|Mennyiség|Összes|Tomcat-munkamenet maximális aktív száma|AppName, Pod|
|TomcatSessionAliveMaxTime|Tomcat-munkamenet maximális élettartama|Ezredmásodperc|Maximum|Tomcat-munkamenet maximális élettartama|AppName, Pod|
|TomcatSessionActiveCurrentCount|Tomcat-munkamenet élő száma|Mennyiség|Összes|Tomcat-munkamenet élő száma|AppName, Pod|
|TomcatSessionCreatedCount|Tomcat-munkamenet létrehozva szám|Mennyiség|Összes|Tomcat-munkamenet létrehozva szám|AppName, Pod|
|TomcatSessionExpiredCount|A Tomcat-munkamenet lejárt|Mennyiség|Összes|A Tomcat-munkamenet lejárt|AppName, Pod|
|TomcatSessionRejectedCount|A Tomcat-munkamenet elutasította a darabszámot|Mennyiség|Összes|A Tomcat-munkamenet elutasította a darabszámot|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok összesen|Mennyiség|Összes|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentRuns|Összes frissítés központi telepítési futtatása|Mennyiség|Összes|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, állapot|
|TotalUpdateDeploymentMachineRuns|Összes frissítés központi telepítési gép futtatása|Mennyiség|Összes|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált mag száma|Mennyiség|Összes|A Batch-fiókban lévő dedikált magok teljes száma|None|
|TotalNodeCount|Dedikált csomópontok száma|Mennyiség|Összes|A Batch-fiókban lévő dedikált csomópontok teljes száma|None|
|LowPriorityCoreCount|LowPriority mag száma|Mennyiség|Összes|Az alacsony prioritású magok teljes száma a Batch-fiókban|None|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Mennyiség|Összes|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|None|
|CreatingNodeCount|Csomópontok számának létrehozása|Mennyiség|Összes|Létrehozandó csomópontok száma|None|
|StartingNodeCount|Csomópontok számának indítása|Mennyiség|Összes|Kiinduló csomópontok száma|None|
|WaitingForStartTaskNodeCount|Várakozás a feladat-csomópontok számának megkezdésére|Mennyiség|Összes|Az indítási tevékenység befejezésére váró csomópontok száma|None|
|StartTaskFailedNodeCount|Indítási feladat sikertelen csomópontok száma|Mennyiség|Összes|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|None|
|IdleNodeCount|Tétlen csomópontok száma|Mennyiség|Összes|Üresjárati csomópontok száma|None|
|OfflineNodeCount|Offline csomópontok száma|Mennyiség|Összes|Offline csomópontok száma|None|
|RebootingNodeCount|Csomópontok számának újraindítása|Mennyiség|Összes|Újraindítási csomópontok száma|None|
|ReimagingNodeCount|Csomópontok rendszerképének alaphelyzetbe állítása|Mennyiség|Összes|Rendszerkép-csomópontok száma|None|
|RunningNodeCount|Csomópontok száma|Mennyiség|Összes|Futó csomópontok száma|None|
|LeavingPoolNodeCount|A készlet-csomópontok számának elhagyása|Mennyiség|Összes|A készletet elhagyó csomópontok száma|None|
|UnusableNodeCount|Nem használható csomópontok száma|Mennyiség|Összes|Használhatatlan csomópontok száma|None|
|PreemptedNodeCount|Előzik-csomópontok száma|Mennyiség|Összes|Előzik-csomópontok száma|None|
|TaskStartEvent|Tevékenységek indítási eseményei|Mennyiség|Összes|Az elindított feladatok teljes száma|None|
|TaskCompleteEvent|Feladat teljes eseményei|Mennyiség|Összes|A Befejezett feladatok teljes száma|None|
|TaskFailEvent|Feladat sikertelen eseményei|Mennyiség|Összes|A meghiúsult állapotban Befejezett feladatok teljes száma|None|
|PoolCreateEvent|Készlet-létrehozási események|Mennyiség|Összes|A létrehozott készletek teljes száma|None|
|PoolResizeStartEvent|Készlet átméretezésének indítási eseményei|Mennyiség|Összes|A készlet elindított újraméretezésének teljes száma|None|
|PoolResizeCompleteEvent|Készlet átméretezése – befejezett események|Mennyiség|Összes|A készlet összes újraméretezésének száma|None|
|PoolDeleteStartEvent|Készlet törlése – indítási események|Mennyiség|Összes|Az elindított készlet-törlések száma összesen|None|
|PoolDeleteCompleteEvent|Készlet törlése – befejezett események|Mennyiség|Összes|A befejezett készlet-törlések teljes száma|None|
|JobDeleteCompleteEvent|Feladatok törlése – befejezett események|Mennyiség|Összes|A sikeresen törölt feladatok teljes száma.|None|
|JobDeleteStartEvent|Feladatok törlésének indítási eseményei|Mennyiség|Összes|A törölni kívánt feladatok teljes száma.|None|
|JobDisableCompleteEvent|A feladatok letiltják a teljes eseményeket|Mennyiség|Összes|A sikeresen letiltott feladatok teljes száma.|None|
|JobDisableStartEvent|Feladatok letiltásának indítási eseményei|Mennyiség|Összes|A letiltani kívánt feladatok teljes száma.|None|
|JobStartEvent|Feladatok indítási eseményei|Mennyiség|Összes|A sikeresen elindított feladatok teljes száma.|None|
|JobTerminateCompleteEvent|A feladatokhoz tartozó befejezett események befejezése|Mennyiség|Összes|A sikeresen leállított feladatok teljes száma.|None|
|JobTerminateStartEvent|Feladatokból indított események leállítása|Mennyiség|Összes|A leállítani kívánt feladatok teljes száma.|None|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Feladatok elküldve|Feladatok elküldve|Mennyiség|Összes|Elküldött feladatok száma|Forgatókönyv, ClusterName|
|A feladatok befejeződtek|A feladatok befejeződtek|Mennyiség|Összes|Befejezett feladatok száma|Forgatókönyv, ClusterName, ResultType|
|Csomópontok összesen|Csomópontok összesen|Mennyiség|Átlag|Összes csomópont száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Aktív csomópontok|Mennyiség|Átlag|Futó csomópontok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Tétlen csomópontok|Mennyiség|Átlag|Üresjárati csomópontok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Használhatatlan csomópontok|Mennyiség|Átlag|Használhatatlan csomópontok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Előzik-csomópontok|Mennyiség|Átlag|Előzik-csomópontok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Csomópontok elhagyása|Mennyiség|Átlag|Kilépő csomópontok száma|Forgatókönyv, ClusterName|
|Magok összesen|Magok összesen|Mennyiség|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Aktív magok|Aktív magok|Mennyiség|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Üresjárati magok|Üresjárati magok|Mennyiség|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Használhatatlan magok|Használhatatlan magok|Mennyiség|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Előzik magok|Előzik magok|Mennyiség|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Magok kihagyása|Magok kihagyása|Mennyiség|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtsága (%)|Kvóta kihasználtsága (%)|Mennyiség|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|CPU-használat százaléka|Százalék|Maximum|CPU-használat százaléka|Csomópont|
|MemoryUsage|Memóriahasználat|Bájt|Átlag|Memóriahasználat|Csomópont|
|MemoryLimit|Memória korlátja|Bájt|Átlag|Memória korlátja|Csomópont|
|MemoryUsagePercentageInDouble|Memóriahasználat százaléka|Százalék|Átlag|Memóriahasználat százaléka|Csomópont|
|StorageUsage|Tárterület-használat|Bájt|Átlag|Tárterület-használat|Csomópont|
|IOReadBytes|IO olvasási bájtok|Bájt|Összes|IO olvasási bájtok|Csomópont|
|IOWriteBytes|IO írási bájtjai|Bájt|Összes|IO írási bájtjai|Csomópont|
|ConnectionAccepted|Elfogadott kapcsolatok|Mennyiség|Összes|Elfogadott kapcsolatok|Csomópont|
|ConnectionHandled|Kezelt kapcsolatok|Mennyiség|Összes|Kezelt kapcsolatok|Csomópont|
|ConnectionActive|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|Csomópont|
|RequestHandled|Kezelt kérelmek|Mennyiség|Összes|Kezelt kérelmek|Csomópont|
|ProcessedBlocks|Feldolgozott blokkok|Mennyiség|Összes|Feldolgozott blokkok|Csomópont|
|ProcessedTransactions|Feldolgozott tranzakciók|Mennyiség|Összes|Feldolgozott tranzakciók|Csomópont|
|PendingTransactions|Függőben lévő tranzakciók|Mennyiség|Átlag|Függőben lévő tranzakciók|Csomópont|
|QueuedTransactions|Várólistán lévő tranzakciók|Mennyiség|Átlag|Várólistán lévő tranzakciók|Csomópont|



## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakozott ügyfelek|Mennyiség|Maximum||ShardId|
|totalcommandsprocessed|Összes művelet|Mennyiség|Összes||ShardId|
|cachehits|Gyorsítótár-találatok|Mennyiség|Összes||ShardId|
|cachemisses|Gyorsítótár-lemaradás|Mennyiség|Összes||ShardId|
|cachemissrate|Gyorsítótár-kihagyás aránya|Százalék|cachemissrate||ShardId|
|getcommands|Kap|Mennyiség|Összes||ShardId|
|setcommands|Készletek|Mennyiség|Összes||ShardId|
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
|cacheLatency|Gyorsítótár késési másodpercek (előzetes verzió)|Mennyiség|Átlag||ShardId|
|Hibák|Hibák|Mennyiség|Maximum||ShardId, ErrorType|
|connectedclients0|Csatlakoztatott ügyfelek (0. szegmens)|Mennyiség|Maximum||None|
|totalcommandsprocessed0|Összes művelet (0. szegmens)|Mennyiség|Összes||None|
|cachehits0|Gyorsítótárbeli találatok (szegmens 0)|Mennyiség|Összes||None|
|cachemisses0|Gyorsítótár-kihagyás (0. szegmens)|Mennyiség|Összes||None|
|getcommands0|Beolvasás (0. szegmens)|Mennyiség|Összes||None|
|setcommands0|Készletek (0. szegmens)|Mennyiség|Összes||None|
|operationsPerSecond0|Művelet/másodperc (0. Szilánk)|Mennyiség|Maximum||None|
|evictedkeys0|Kizárt kulcsok (0. szegmens)|Mennyiség|Összes||None|
|totalkeys0|Összes kulcs (szilánk 0)|Mennyiség|Maximum||None|
|expiredkeys0|Lejárt kulcsok (szilánk 0)|Mennyiség|Összes||None|
|usedmemory0|Felhasznált memória (0. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss0|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||None|
|serverLoad0|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||None|
|cacheWrite0|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead0|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime0|CPU (0. szegmens)|Százalék|Maximum||None|
|connectedclients1|Csatlakoztatott ügyfelek (1. szegmens)|Mennyiség|Maximum||None|
|totalcommandsprocessed1|Összes művelet (1. szegmens)|Mennyiség|Összes||None|
|cachehits1|Gyorsítótárbeli találatok (1. Szilánk)|Mennyiség|Összes||None|
|cachemisses1|Gyorsítótár-lemaradás (1. szegmens)|Mennyiség|Összes||None|
|getcommands1|Beolvasás (1. szegmens)|Mennyiség|Összes||None|
|setcommands1|Készletek (1. szegmens)|Mennyiség|Összes||None|
|operationsPerSecond1|Művelet/másodperc (1. szegmens)|Mennyiség|Maximum||None|
|evictedkeys1|Kizárt kulcsok (1. Szilánk)|Mennyiség|Összes||None|
|totalkeys1|Összes kulcs (1. szegmens)|Mennyiség|Maximum||None|
|expiredkeys1|Lejárt kulcsok (1. szegmens)|Mennyiség|Összes||None|
|usedmemory1|Felhasznált memória (1. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss1|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||None|
|serverLoad1|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||None|
|cacheWrite1|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead1|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime1|CPU (1. Szilánk)|Százalék|Maximum||None|
|connectedclients2|Csatlakoztatott ügyfelek (2. szegmens)|Mennyiség|Maximum||None|
|totalcommandsprocessed2|Összes művelet (2. szegmens)|Mennyiség|Összes||None|
|cachehits2|Gyorsítótárbeli találatok (2. szegmens)|Mennyiség|Összes||None|
|cachemisses2|Gyorsítótár-lemaradás (2. szegmens)|Mennyiség|Összes||None|
|getcommands2|Beolvasás (2. szegmens)|Mennyiség|Összes||None|
|setcommands2|Készletek (2. szegmens)|Mennyiség|Összes||None|
|operationsPerSecond2|Művelet/másodperc (2. szegmens)|Mennyiség|Maximum||None|
|evictedkeys2|Kizárt kulcsok (2. szegmens)|Mennyiség|Összes||None|
|totalkeys2|Összes kulcs (2. szegmens)|Mennyiség|Maximum||None|
|expiredkeys2|Lejárt kulcsok (2. szegmens)|Mennyiség|Összes||None|
|usedmemory2|Felhasznált memória (2. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss2|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||None|
|serverLoad2|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||None|
|cacheWrite2|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead2|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime2|CPU (2. szegmens)|Százalék|Maximum||None|
|connectedclients3|Csatlakoztatott ügyfelek (3. Szilánk)|Mennyiség|Maximum||None|
|totalcommandsprocessed3|Összes művelet (3. szegmens)|Mennyiség|Összes||None|
|cachehits3|Gyorsítótárbeli találatok (3. Szilánk)|Mennyiség|Összes||None|
|cachemisses3|Gyorsítótár-lemaradás (3. szegmens)|Mennyiség|Összes||None|
|getcommands3|Beolvasás (3. szegmens)|Mennyiség|Összes||None|
|setcommands3|Készletek (3. szegmens)|Mennyiség|Összes||None|
|operationsPerSecond3|Művelet/másodperc (3. szegmens)|Mennyiség|Maximum||None|
|evictedkeys3|Kizárt kulcsok (3. Szilánk)|Mennyiség|Összes||None|
|totalkeys3|Összes kulcs (3. Szilánk)|Mennyiség|Maximum||None|
|expiredkeys3|Lejárt kulcsok (3. Szilánk)|Mennyiség|Összes||None|
|usedmemory3|Felhasznált memória (3. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss3|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||None|
|serverLoad3|Kiszolgáló terhelése (3. Szilánk)|Százalék|Maximum||None|
|cacheWrite3|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||None|
|cacheRead3|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime3|CPU (3. Szilánk)|Százalék|Maximum||None|
|connectedclients4|Csatlakoztatott ügyfelek (4. Szilánk)|Mennyiség|Maximum||None|
|totalcommandsprocessed4|Összes művelet (4. Szilánk)|Mennyiség|Összes||None|
|cachehits4|Gyorsítótár-találatok (4. Szilánk)|Mennyiség|Összes||None|
|cachemisses4|Gyorsítótár-lemaradás (4. szegmens)|Mennyiség|Összes||None|
|getcommands4|Beolvasás (4. Szilánk)|Mennyiség|Összes||None|
|setcommands4|Készletek (4. Szilánk)|Mennyiség|Összes||None|
|operationsPerSecond4|Művelet/másodperc (4. szegmens)|Mennyiség|Maximum||None|
|evictedkeys4|Kizárt kulcsok (4. Szilánk)|Mennyiség|Összes||None|
|totalkeys4|Összes kulcs (4. Szilánk)|Mennyiség|Maximum||None|
|expiredkeys4|Lejárt kulcsok (4. Szilánk)|Mennyiség|Összes||None|
|usedmemory4|Felhasznált memória (4. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss4|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||None|
|serverLoad4|Kiszolgáló terhelése (4. Szilánk)|Százalék|Maximum||None|
|cacheWrite4|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||None|
|cacheRead4|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||None|
|percentProcessorTime4|CPU (4. Szilánk)|Százalék|Maximum||None|
|connectedclients5|Csatlakoztatott ügyfelek (5. Szilánk)|Mennyiség|Maximum||None|
|totalcommandsprocessed5|Összes művelet (5. Szilánk)|Mennyiség|Összes||None|
|cachehits5|Gyorsítótárbeli találatok (szegmens 5)|Mennyiség|Összes||None|
|cachemisses5|Gyorsítótár-lemaradás (5. szegmens)|Mennyiség|Összes||None|
|getcommands5|Beolvasás (szegmens 5)|Mennyiség|Összes||None|
|setcommands5|Készletek (szilánk 5)|Mennyiség|Összes||None|
|operationsPerSecond5|Művelet/másodperc (5. szegmens)|Mennyiség|Maximum||None|
|evictedkeys5|Kizárt kulcsok (5. Szilánk)|Mennyiség|Összes||None|
|totalkeys5|Összes kulcs (5. szegmens)|Mennyiség|Maximum||None|
|expiredkeys5|Lejárt kulcsok (szilánk 5)|Mennyiség|Összes||None|
|usedmemory5|Felhasznált memória (5. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss5|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||None|
|serverLoad5|Kiszolgáló terhelése (5. Szilánk)|Százalék|Maximum||None|
|cacheWrite5|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||None|
|cacheRead5|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime5|CPU (5. Szilánk)|Százalék|Maximum||None|
|connectedclients6|Csatlakoztatott ügyfelek (6. szegmens)|Mennyiség|Maximum||None|
|totalcommandsprocessed6|Összes művelet (szegmens 6)|Mennyiség|Összes||None|
|cachehits6|Gyorsítótárbeli találatok (szilánk 6)|Mennyiség|Összes||None|
|cachemisses6|Gyorsítótár-lemaradás (szegmens 6)|Mennyiség|Összes||None|
|getcommands6|Beolvasás (szilánk 6)|Mennyiség|Összes||None|
|setcommands6|Készletek (szilánk 6)|Mennyiség|Összes||None|
|operationsPerSecond6|Művelet/másodperc (szegmens 6)|Mennyiség|Maximum||None|
|evictedkeys6|Kizárt kulcsok (szegmens 6)|Mennyiség|Összes||None|
|totalkeys6|Összes kulcs (szilánk 6)|Mennyiség|Maximum||None|
|expiredkeys6|Lejárt kulcsok (szilánk 6)|Mennyiség|Összes||None|
|usedmemory6|Felhasznált memória (6. szegmens)|Bájt|Maximum||None|
|usedmemoryRss6|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||None|
|serverLoad6|Kiszolgáló betöltése (szegmens 6)|Százalék|Maximum||None|
|cacheWrite6|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||None|
|cacheRead6|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||None|
|percentProcessorTime6|CPU (szegmens 6)|Százalék|Maximum||None|
|connectedclients7|Csatlakoztatott ügyfelek (7. szegmens)|Mennyiség|Maximum||None|
|totalcommandsprocessed7|Összes művelet (7. szegmens)|Mennyiség|Összes||None|
|cachehits7|Gyorsítótárbeli találatok (szegmens 7)|Mennyiség|Összes||None|
|cachemisses7|Gyorsítótár-lemaradás (7. szegmens)|Mennyiség|Összes||None|
|getcommands7|Beolvasás (szegmens 7)|Mennyiség|Összes||None|
|setcommands7|Készletek (szegmens 7)|Mennyiség|Összes||None|
|operationsPerSecond7|Művelet/másodperc (7. szegmens)|Mennyiség|Maximum||None|
|evictedkeys7|Kizárt kulcsok (7. szegmens)|Mennyiség|Összes||None|
|totalkeys7|Összes kulcs (szegmens 7)|Mennyiség|Maximum||None|
|expiredkeys7|Lejárt kulcsok (7. szegmens)|Mennyiség|Összes||None|
|usedmemory7|Felhasznált memória (7. szegmens)|Bájt|Maximum||None|
|usedmemoryRss7|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||None|
|serverLoad7|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||None|
|cacheWrite7|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||None|
|cacheRead7|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||None|
|percentProcessorTime7|PROCESSZOR (7. szegmens)|Százalék|Maximum||None|
|connectedclients8|Csatlakoztatott ügyfelek (10. szegmens)|Mennyiség|Maximum||None|
|totalcommandsprocessed8|Összes művelet (szegmens 8)|Mennyiség|Összes||None|
|cachehits8|Gyorsítótárbeli találatok (szilánk 8)|Mennyiség|Összes||None|
|cachemisses8|Gyorsítótár-lemaradás (szegmens 8)|Mennyiség|Összes||None|
|getcommands8|Beolvasás (szilánk 8)|Mennyiség|Összes||None|
|setcommands8|Készletek (szilánk 8)|Mennyiség|Összes||None|
|operationsPerSecond8|Művelet/másodperc (szegmens 8)|Mennyiség|Maximum||None|
|evictedkeys8|Kizárt kulcsok (szilánk 8)|Mennyiség|Összes||None|
|totalkeys8|Összes kulcs (szilánk 8)|Mennyiség|Maximum||None|
|expiredkeys8|Lejárt kulcsok (szilánk 8)|Mennyiség|Összes||None|
|usedmemory8|Felhasznált memória (8. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss8|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||None|
|serverLoad8|Kiszolgáló betöltése (szegmens 8)|Százalék|Maximum||None|
|cacheWrite8|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||None|
|cacheRead8|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||None|
|percentProcessorTime8|CPU (szegmens 8)|Százalék|Maximum||None|
|connectedclients9|Csatlakoztatott ügyfelek (10. szegmens)|Mennyiség|Maximum||None|
|totalcommandsprocessed9|Összes művelet (szegmens 9)|Mennyiség|Összes||None|
|cachehits9|Gyorsítótárbeli találatok (szilánk 9)|Mennyiség|Összes||None|
|cachemisses9|Gyorsítótár-lemaradás (szegmens 9)|Mennyiség|Összes||None|
|getcommands9|Beolvasás (szegmens 9)|Mennyiség|Összes||None|
|setcommands9|Készletek (szilánk 9)|Mennyiség|Összes||None|
|operationsPerSecond9|Művelet/másodperc (szegmens 9)|Mennyiség|Maximum||None|
|evictedkeys9|Kizárt kulcsok (szilánk 9)|Mennyiség|Összes||None|
|totalkeys9|Összes kulcs (szilánk 9)|Mennyiség|Maximum||None|
|expiredkeys9|Lejárt kulcsok (szilánk 9)|Mennyiség|Összes||None|
|usedmemory9|Felhasznált memória (10. szegmens)|Bájt|Maximum||None|
|usedmemoryRss9|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||None|
|serverLoad9|Kiszolgáló terhelése (szegmens 9)|Százalék|Maximum||None|
|cacheWrite9|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||None|
|cacheRead9|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||None|
|percentProcessorTime9|CPU (szilánk 9)|Százalék|Maximum||None|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Mennyiség|Összes|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|None|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|None|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|None|
|Lemez olvasási sebessége (bájt/s)|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|None|
|Lemez írási sebessége (bájt/s)|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|None|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|None|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Lemez olvasási sebessége (bájt/s)|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Felhasznált kapacitás|Bájt|Átlag|Fiókhoz használt kapacitás|None|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|Blob-kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Blobok száma|Mennyiség|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|Blobtárolók száma|Mennyiség|Átlag|A Storage-fiók Blob service lévő tárolók száma.|None|
|IndexCapacity|Indexkapacitás|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|None|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|None|
|TableCount|Táblák száma|Mennyiség|Átlag|A Storage-fiók Table service található tábla száma.|None|
|TableEntityCount|Tábla entitások száma|Mennyiség|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|None|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileCount|Fájlok száma|Mennyiség|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|Fájlmegosztás száma|Mennyiség|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|None|
|FileShareSnapshotCount|Fájlmegosztás pillanatképének száma|Mennyiség|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileShareQuota|Fájlmegosztás kvótájának mérete|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|None|
|QueueCount|Várólista száma|Mennyiség|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|None|
|QueueMessageCount|Üzenetsor-üzenetek száma|Mennyiség|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|None|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalCalls|Hívások összesen|Mennyiség|Összes|A hívások száma összesen|ApiName, OperationName, régió|
|SuccessfulCalls|Sikeres hívások|Mennyiség|Összes|A sikeres hívások száma.|ApiName, OperationName, régió|
|TotalErrors|Összes hiba|Mennyiség|Összes|A hibaüzenettel rendelkező hívások teljes száma (HTTP-válasz kódja 4xx vagy 5xx).|ApiName, OperationName, régió|
|BlockedCalls|Blokkolt hívások|Mennyiség|Összes|Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|ApiName, OperationName, régió|
|Kiszolgálóhibái|Kiszolgálói hibák|Mennyiség|Összes|A szolgáltatás belső hibája miatti hívások száma (HTTP-válasz kódja 5xx).|ApiName, OperationName, régió|
|ClientErrors|Ügyfél-hibák|Mennyiség|Összes|Ügyféloldali hibával rendelkező hívások száma (HTTP-válasz kódja 4xx).|ApiName, OperationName, régió|
|DataIn|Bejövő adatforgalom|Bájt|Összes|A bejövő adat mérete bájtban megadva.|ApiName, OperationName, régió|
|DataOut|Kimenő adatforgalom|Bájt|Összes|A kimenő adat mérete bájtban kifejezve.|ApiName, OperationName, régió|
|Késés|Késés|Ezredmásodpercben|Átlag|Késés ezredmásodpercben.|ApiName, OperationName, régió|
|CharactersTranslated|Lefordított karakterek|Mennyiség|Összes|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTrained|Betanított karakterek|Mennyiség|Összes|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|SpeechSessionDuration|Beszédfelismerési munkamenet időtartama|másodperc|Összes|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|TotalTransactions|Tranzakciók összesen|Mennyiség|Összes|A tranzakciók száma összesen|None|
|TotalTokenCalls|Jogkivonat-hívások összesen|Mennyiség|Összes|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|None|
|Bejövő hálózat|Számlázandó hálózat (elavult)|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|None|
|Kimenő hálózat|Hálózati kimenő számlázandó (elavult)|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|None|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|None|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összes|A megfigyelési időszak során lemezre írt bájtok|None|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|None|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|None|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Mennyiség|Átlag|A feltört kreditek teljes száma|None|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Mennyiség|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD (elavult)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LOGIKAI EGYSÉG|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LOGIKAI EGYSÉG|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LOGIKAI EGYSÉG|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LOGIKAI EGYSÉG|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LOGIKAI EGYSÉG|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|OS-lemez várakozási sorának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Mennyiség|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|None|
|Kimenő folyamatok|Kimenő folyamatok|Mennyiség|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|None|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|None|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|None|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LOGIKAI EGYSÉG|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LOGIKAI EGYSÉG|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|None|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|None|
|Teljes hálózat|Teljes hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|None|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Bejövő hálózat|Számlázandó hálózat (elavult)|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|VMName|
|Kimenő hálózat|Hálózati kimenő számlázandó (elavult)|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|VMName|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összes|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|VMName|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|VMName|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Mennyiség|Átlag|A feltört kreditek teljes száma|None|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Mennyiség|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD (elavult)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OS-lemez várakozási sorának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Mennyiség|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő folyamatok|Kimenő folyamatok|Mennyiség|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|
|Teljes hálózat|Teljes hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. számítás/virtualMachineScaleSets/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|None|
|Bejövő hálózat|Számlázandó hálózat (elavult)|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|None|
|Kimenő hálózat|Hálózati kimenő számlázandó (elavult)|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|None|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összes|A figyelési időszak során lemezről beolvasott bájtok száma|None|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összes|A megfigyelési időszak során lemezre írt bájtok|None|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|None|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|None|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Mennyiség|Átlag|A feltört kreditek teljes száma|None|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Mennyiség|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD (elavult)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LOGIKAI EGYSÉG|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LOGIKAI EGYSÉG|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LOGIKAI EGYSÉG|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LOGIKAI EGYSÉG|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Mennyiség|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LOGIKAI EGYSÉG|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|OS-lemez várakozási sorának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Mennyiség|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Mennyiség|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|None|
|Kimenő folyamatok|Kimenő folyamatok|Mennyiség|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|None|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|None|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya (előzetes verzió)|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|None|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LOGIKAI EGYSÉG|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LOGIKAI EGYSÉG|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|None|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|None|
|Teljes hálózat|Teljes hálózat|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|None|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összes|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|Processzorhasználat|Mennyiség|Átlag|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Memóriahasználat|Bájt|Átlag|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Másodpercenként fogadott hálózati bájtok száma|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|None|
|NetworkBytesTransmittedPerSecond|Másodpercenként továbbított hálózati bájtok száma|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalPullCount|Lekérések száma összesen|Mennyiség|Átlag|A lekérések száma összesen|None|
|SuccessfulPullCount|Sikeres lekérések száma|Mennyiség|Átlag|Sikeres Képkeresések száma|None|
|TotalPushCount|Leküldések száma összesen|Mennyiség|Átlag|Leküldések száma összesen|None|
|SuccessfulPushCount|Sikeres leküldések száma|Mennyiség|Átlag|Sikeres leküldések száma|None|
|RunDuration|Futtatás időtartama|Ezredmásodperc|Összes|Futtatás időtartama (ezredmásodpercben)|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Mennyiség|Átlag|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|None|
|kube_node_status_allocatable_memory_bytes|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Átlag|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|None|
|kube_pod_status_ready|A hüvelyek száma üzemkész állapotban|Mennyiség|Átlag|A hüvelyek száma üzemkész állapotban|névtér, Pod|
|kube_node_status_condition|Különböző csomóponti feltételek állapota|Mennyiség|Átlag|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|Hüvelyek száma fázis szerint|Mennyiség|Átlag|Hüvelyek száma fázis szerint|fázis, névtér, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfullRequests|Sikeres kérések|Mennyiség|Összes|Az egyéni szolgáltató által kezdeményezett sikeres kérések|HttpMethod, CallPath, StatusCode|
|FailedRequests|Sikertelen kérelmek|Mennyiség|Összes|A rendelkezésre álló naplók beolvasása egyéni erőforrás-szolgáltatók számára|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|NICReadThroughput|Olvasási sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter olvasási átviteli sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|NICWriteThroughput|Írási átviteli sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter írási sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|CloudReadThroughputPerShare|Felhőbeli letöltési teljesítmény (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való letöltési átviteli sebesség a jelentési időszak alatt.|Megosztás|
|CloudUploadThroughputPerShare|Felhőbeli feltöltési sebesség (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való feltöltési átviteli sebesség a jelentési időszak során.|Megosztás|
|BytesUploadedToCloudPerShare|Felhőbeli feltöltött bájtok (megosztás)|Bájt|Átlag|A jelentési időszak során az Azure-ba feltöltött bájtok teljes száma.|Megosztás|
|TotalCapacity|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|None|
|Availablecapacity;)|Rendelkezésre álló kapacitás|Bájt|Átlag|A rendelkezésre álló kapacitás bájtban a jelentési időszak alatt.|None|
|CloudUploadThroughput|Felhőbeli feltöltési sebesség|BytesPerSecond|Átlag|A Felhőbeli feltöltési sebesség az Azure-ba a jelentési időszak alatt.|None|
|CloudReadThroughput|Felhőbeli letöltési sebesség|BytesPerSecond|Átlag|A felhő letöltési sebessége az Azure-ba a jelentési időszak alatt.|None|
|BytesUploadedToCloud|Feltöltött Felhőbeli bájtok (eszköz)|Bájt|Átlag|Az Azure-ba az eszközről a jelentési időszakban feltöltött bájtok teljes száma.|None|
|HyperVVirtualProcessorUtilization|Edge-számítás – százalékos CPU|Százalék|Átlag|CPU-használat százalékos aránya|InstanceName|
|HyperVMemoryUtilization|Edge-számítás – memóriahasználat|Százalék|Átlag|Használatban lévő RAM mennyisége|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Sikertelen futtatások|Mennyiség|Összes||pipelineName, activityName|
|SuccessfulRuns|Sikeres futtatások|Mennyiség|Összes||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Sikertelen folyamat-futtatási metrikák|Mennyiség|Összes||FailureType, név|
|PipelineSucceededRuns|A folyamat sikeresen futtatja a metrikákat|Mennyiség|Összes||FailureType, név|
|PipelineCancelledRuns|Megszakított folyamat-futtatási metrikák|Mennyiség|Összes||FailureType, név|
|ActivityFailedRuns|Sikertelen tevékenység-futtatási metrikák|Mennyiség|Összes||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|A sikeres tevékenység metrikákat futtat|Mennyiség|Összes||ActivityType, PipelineName, FailureType, név|
|ActivityCancelledRuns|A megszakított tevékenység metrikákat futtat|Mennyiség|Összes||ActivityType, PipelineName, FailureType, név|
|TriggerFailedRuns|Sikertelen trigger-futtatási metrikák|Mennyiség|Összes||Név, FailureType|
|TriggerSucceededRuns|A sikeres trigger metrikákat futtat|Mennyiség|Összes||Név, FailureType|
|TriggerCancelledRuns|Megszakított trigger-futtatási metrikák|Mennyiség|Összes||Név, FailureType|
|IntegrationRuntimeCpuPercentage|Integration Runtime CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableMemory|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAverageTaskPickupDelay|Integration Runtime-várólista időtartama|másodperc|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Integration Runtime-várólista hossza|Mennyiség|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Integrációs modul elérhető csomópontok száma|Mennyiség|Átlag||IntegrationRuntimeName|
|MaxAllowedResourceCount|Engedélyezett entitások maximális száma|Mennyiség|Maximum||None|
|MaxAllowedFactorySizeInGbUnits|Maximálisan engedélyezett gyári méret (GB egység)|Mennyiség|Maximum||None|
|ResourceCount|Entitások száma összesen|Mennyiség|Maximum||None|
|FactorySizeInGbUnits|Gyári méret összesen (GB egység)|Mennyiség|Maximum||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Mennyiség|Összes|A sikeres feladatok száma.|None|
|JobEndedFailure|Sikertelen feladatok|Mennyiség|Összes|Sikertelen feladatok száma.|None|
|JobEndedCancelled|Megszakított feladatok|Mennyiség|Összes|Megszakított feladatok száma.|None|
|JobAUEndedSuccess|Sikeres AU-idő|másodperc|Összes|A sikeres feladatok teljes AU-ideje.|None|
|JobAUEndedFailure|Sikertelen AU-idő|másodperc|Összes|A sikertelen feladatok összes AU-ideje.|None|
|JobAUEndedCancelled|Megszakított AU-idő|másodperc|Összes|A megszakított feladatok teljes AU-ideje.|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|None|
|DataWritten|Írt adatértékek|Bájt|Összes|A fiókba írt összes adatmennyiség.|None|
|DataRead|Olvasott információk|Bájt|Összes|A fiókból beolvasott adatok teljes mennyisége.|None|
|WriteRequests|Írási kérelmek|Mennyiség|Összes|A fiókra vonatkozó adatírási kérelmek száma.|None|
|ReadRequests|Olvasási kérelmek|Mennyiség|Összes|A fióknak küldött adatolvasási kérelmek száma.|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|None|
|storage_limit|Tárolási korlát|Bájt|Maximum|Tárolási korlát|None|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|None|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|None|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|None|
|active_connections|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|None|
|seconds_behind_master|Replikálás késése másodpercben|Mennyiség|Maximum|Replikálás késése másodpercben|None|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|None|
|storage_limit|Tárolási korlát|Bájt|Maximum|Tárolási korlát|None|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|None|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|None|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|None|
|active_connections|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|None|
|seconds_behind_master|Replikálás késése másodpercben|Mennyiség|Maximum|Replikálás késése másodpercben|None|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|io_consumption_percent|IO-százalék|Százalék|Átlag|IO-százalék|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|None|
|storage_limit|Tárolási korlát|Bájt|Maximum|Tárolási korlát|None|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|None|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|None|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|None|
|active_connections|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|None|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|None|
|pg_replica_log_delay_in_seconds|Replika késése|másodperc|Maximum|Replika késése másodpercben|None|
|pg_replica_log_delay_in_bytes|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|IOPS|IO|Mennyiség|Átlag|I/o-műveletek másodpercenként|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|None|
|active_connections|Aktív kapcsolatok|Mennyiség|Átlag|Aktív kapcsolatok|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összes|Hálózati kapcsolat az aktív kapcsolatok között|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|D2C. telemetria. beáramló. allProtocol|Telemetria üzenetek küldése|Mennyiség|Összes|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|None|
|D2C. telemetria. beáramló. sikeres|Elküldött telemetriai üzenetek|Mennyiség|Összes|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|None|
|C2D. commands. kimenő. Complete. success|C2D-üzenetek kézbesítésének befejezése|Mennyiség|Összes|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|None|
|C2D. commands. kimenő. elhagyása. sikeres|C2D üzenetek elhagyva|Mennyiség|Összes|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|None|
|C2D. commands. kimenő. elutasítás. sikeres|C2D-üzenetek elutasítva|Mennyiség|Összes|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|None|
|C2DMessagesExpired|C2D-üzenetek lejárt (előzetes verzió)|Mennyiség|Összes|A felhőből az eszközre irányuló lejárt üzenetek száma|None|
|Devices. totalDevices|Összes eszköz (elavult)|Mennyiség|Összes|Az IoT hub-ban regisztrált eszközök száma|None|
|Devices. connectedDevices. allProtocol|Csatlakoztatott eszközök (elavult) |Mennyiség|Összes|Az IoT hub-hoz csatlakoztatott eszközök száma|None|
|D2C. telemetria. kimenő. sikeres|Útválasztás: telemetria üzenetek kézbesítése|Mennyiség|Összes|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|None|
|D2C. telemetria. kimenő. eldobott|Útválasztás: telemetria üzenetek elvetve |Mennyiség|Összes|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|None|
|D2C. telemetria. kimenő. árva|Útválasztás: árva telemetria üzenetek |Mennyiség|Összes|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |None|
|D2C. telemetria. kimenő. érvénytelen|Útválasztás: Inkompatibilis telemetria-üzenetek|Mennyiség|Összes|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|None|
|D2C. telemetria. kimenő. tartalék|Útválasztás: tartalékként továbbított üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|None|
|D2C. endpoints. kimenő. eventHubs|Útválasztás: az Event hub számára továbbított üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|None|
|D2C. endpoints. késleltetés. eventHubs|Útválasztás: Event Hub üzeneteinek késése|Ezredmásodperc|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|None|
|D2C. endpoints. kimenő. serviceBusQueues|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|None|
|D2C. endpoints. késleltetés. serviceBusQueues|Útválasztás: Service Bus-üzenetsor üzeneteinek késése|Ezredmásodperc|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|None|
|D2C. endpoints. kimenő. serviceBusTopics|Útválasztás: Service Bus témakörbe küldött üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|None|
|D2C. endpoints. késleltetés. serviceBusTopics|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodperc|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|None|
|D2C. endpoints. kimenő. beépített. események|Útválasztás: üzenetek/események küldésére küldött üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események).|None|
|D2C. endpoints. késleltetés. beépített. események|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodperc|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események).|None|
|D2C. endpoints. kimenő. Storage|Útválasztás: a tárolóba küldött üzenetek|Mennyiség|Összes|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|None|
|D2C. endpoints. látencia. Storage|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodperc|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|None|
|D2C. endpoints. kimenő. Storage. Bytes|Útválasztás: tárba továbbított adatok|Bájt|Összes|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|None|
|D2C. endpoints. kimenő. Storage. Blobok|Útválasztás: tárba továbbított blobok|Mennyiség|Összes|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|None|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Mennyiség|Összes|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát (https://aka.ms/ioteventgrid).|ResourceId, eredmény, EventType|
|EventGridLatency|Event Grid késés (előzetes verzió)|Ezredmásodperc|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|ResourceId, EventType|
|D2C. Twin. Read. success|Sikeres dupla olvasások az eszközökről|Mennyiség|Összes|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|None|
|D2C. Twin. Read. failure|Sikertelen ikerolvasások az eszközökről|Mennyiség|Összes|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|None|
|D2C. Twin. Read. size|Ikereszköz-olvasások válaszmérete az eszközökből|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|None|
|D2C. Twin. Update. success|Sikeres dupla frissítések az eszközökről|Mennyiség|Összes|Az eszköz által kezdeményezett két frissítés sikeres száma.|None|
|D2C. Twin. Update. failure|Sikertelen ikereszköz-frissítések az eszközökből|Mennyiség|Összes|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|None|
|D2C. Twin. Update. size|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|None|
|C2D. Methods. success|Közvetlen metódusok sikeres meghívása|Mennyiség|Összes|Az összes sikeres közvetlen metódus hívásának száma.|None|
|C2D. Methods. failure|Sikertelen közvetlen metódushívások|Mennyiség|Összes|A sikertelen közvetlen metódusok összes hívásának száma.|None|
|C2D. Methods. requestSize|Közvetlen metódushívások kérelmeinek mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|None|
|C2D. Methods. responseSize|Közvetlen metódushívások válaszainak mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|None|
|C2D. Twin. Read. success|Sikeres dupla olvasások a háttérből|Mennyiség|Összes|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|None|
|C2D. Twin. Read. failure|Sikertelen ikerolvasások a háttérrendszerből|Mennyiség|Összes|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|None|
|C2D. Twin. Read. size|Ikereszköz-olvasások válaszmérete a háttérrendszerből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|None|
|C2D. Twin. Update. success|Sikeres ikereszköz-frissítések a háttérrendszerből|Mennyiség|Összes|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|None|
|C2D. Twin. Update. failure|Sikertelen ikereszköz-frissítések a háttérrendszerből|Mennyiség|Összes|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|None|
|C2D. Twin. Update. size|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|None|
|twinQueries. success|Sikeres Twin-lekérdezések|Mennyiség|Összes|Az összes sikeres dupla lekérdezés száma.|None|
|twinQueries. hiba|Sikertelen ikerlekérdezések|Mennyiség|Összes|Az összes sikertelen dupla lekérdezés száma.|None|
|twinQueries.resultSize|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|None|
|feladatok. createTwinUpdateJob. sikeres|A kettős frissítési feladatok sikeres létrehozása|Mennyiség|Összes|A kettős frissítési feladatok sikeres létrehozásának száma.|None|
|feladatok. createTwinUpdateJob. hiba|Sikertelen ikereszköz-frissítési feladat-létrehozások|Mennyiség|Összes|A kettős frissítési feladatok sikertelen létrehozásának száma.|None|
|feladatok. createDirectMethodJob. sikeres|Metódus-Meghívási feladatok sikeres létrehozása|Mennyiség|Összes|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|None|
|feladatok. createDirectMethodJob. hiba|Sikertelen metódusmeghívási feladat-létrehozások|Mennyiség|Összes|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|None|
|feladatok. listJobs. sikeres|Sikeres hívások a feladatok listázásához|Mennyiség|Összes|A feladatok listázására irányuló sikeres hívások száma.|None|
|feladatok. listJobs. hiba|Sikertelen hívások a feladatok listázásához|Mennyiség|Összes|A feladatokat listázó sikertelen hívások száma.|None|
|feladatok. cancelJob. sikeres|Sikeres feladatok törlése|Mennyiség|Összes|A feladat megszakítására irányuló sikeres hívások száma.|None|
|feladatok. cancelJob. hiba|Sikertelen feladatlemondások|Mennyiség|Összes|A feladat megszakítására irányuló sikertelen hívások száma.|None|
|feladatok. queryJobs. sikeres|Sikeres feladatok lekérdezése|Mennyiség|Összes|A lekérdezési feladatok összes sikeres hívásának száma.|None|
|feladatok. queryJobs. hiba|Sikertelen feladatlekérdezések|Mennyiség|Összes|A lekérdező feladatok összes sikertelen hívásának száma.|None|
|feladatok. kész|Befejezett feladatok|Mennyiség|Összes|Az összes befejezett feladat száma.|None|
|feladatok. sikertelen|Sikertelen feladatok|Mennyiség|Összes|Az összes sikertelen feladat száma.|None|
|D2C. telemetria. beáramló. sendThrottle|Szabályozási hibák száma|Mennyiség|Összes|Az eszköz átviteli sebessége miatti szabályozási hibák száma|None|
|dailyMessageQuotaUsed|A felhasznált üzenetek teljes száma|Mennyiség|Átlag|A jelenleg használt összes üzenet száma|None|
|deviceDataUsage|Az eszköz összes adatfelhasználása|Bájt|Összes|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|None|
|deviceDataUsageV2|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összes|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|None|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Mennyiség|Átlag|Az IoT hub-ban regisztrált eszközök száma|None|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Mennyiség|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|None|
|konfigurációk|Konfigurációs metrikák|Mennyiség|Összes|A konfigurációs műveletek metrikái|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Mennyiség|Összes|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, állapot|
|DeviceAssignments|Hozzárendelt eszközök|Mennyiség|Összes|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Igazolási kísérletek|Mennyiség|Összes|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, állapot, protokoll|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AddRegion|Régió hozzáadva|Mennyiség|Mennyiség|Régió hozzáadva|Region (Régió)|
|AvailableStorage|Rendelkezésre álló tár|Bájt|Összes|5 perces részletességgel jelentett teljes rendelkezésre álló tárterület|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|Cassandra-kapcsolatok bezárása|Mennyiség|Összes|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|APIType, régió, ClosureReason|
|CassandraRequestCharges|Cassandra-kérelmek díjai|Mennyiség|Összes|A Cassandra-kérelmekhez felhasznált RUs|APIType, DatabaseName, CollectionName, régió, OperationType, ResourceType|
|CassandraRequests|Cassandra-kérelmek|Mennyiség|Mennyiség|A végrehajtott Cassandra-kérelmek száma|APIType, DatabaseName, CollectionName, region, OperationType, ResourceType, ErrorCode|
|CreateAccount|Fiók létrehozva|Mennyiség|Mennyiség|Fiók létrehozva|None|
|DataUsage|Adathasználat|Bájt|Összes|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DeleteAccount|Fiók törölve|Mennyiség|Mennyiség|Fiók törölve|None|
|DocumentCount|Dokumentumok száma|Mennyiség|Összes|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|Dokumentum kvótája|Bájt|Összes|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|IndexUsage|Indexelés használata|Bájt|Összes|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|MetadataRequests|Metaadat-kérelmek|Mennyiség|Mennyiség|A metaadat-kérelmek száma. Cosmos DB az egyes fiókok rendszermetaadat-gyűjteményét kezeli, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, szerepkör|
|MongoRequestCharge|Mongo-kérelem díja|Mennyiség|Összes|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequests|Mongo kérelmek|Mennyiség|Mennyiség|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsCount|Mongo kérelmek gyakorisága|CountPerSecond|Átlag|Mongo kérelmek száma másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo-törlési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-törlési kérelem másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsInsert|Mongo-beszúrási kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-beszúrási szám másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsQuery|Mongo-lekérdezési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-lekérdezési kérelem másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsUpdate|Mongo-frissítési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo frissítési kérelem másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|ProvisionedThroughput|Kiosztott átviteli kapacitás|Mennyiség|Maximum|Kiosztott átviteli kapacitás|DatabaseName, CollectionName|
|RegionFailover|A régió feladatátvétele megtörtént|Mennyiség|Mennyiség|A régió feladatátvétele megtörtént|None|
|RemoveRegion|Régió eltávolítva|Mennyiség|Mennyiség|Régió eltávolítva|Region (Régió)|
|ReplicationLatency|P99 replikáció késése|Ezredmásodpercben|Átlag|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion,TargetRegion|
|ServiceAvailability|Szolgáltatás rendelkezésre állása|Százalék|Átlag|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|None|
|TotalRequestUnits|Kérelmek összes egysége|Mennyiség|Összes|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType|
|TotalRequests|Összes kérelem|Mennyiség|Mennyiség|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType|
|UpdateAccountKeys|A fiók kulcsai frissítve|Mennyiség|Mennyiség|A fiók kulcsai frissítve|KeyType|
|UpdateAccountNetworkSettings|A fiók hálózati beállításai frissítve|Mennyiség|Mennyiség|A fiók hálózati beállításai frissítve|None|
|UpdateAccountReplicationSettings|A fiók replikációs beállításainak frissítése megtörtént|Mennyiség|Mennyiség|A fiók replikációs beállításainak frissítése megtörtént|None|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/szolgáltatások

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TransactionCount|Tranzakciók száma|Mennyiség|Mennyiség|Tranzakciók száma összesen|TransactionCount|
|SuccessCount|Sikeres műveletek száma|Mennyiség|Mennyiség|Sikeres tranzakciók száma|SuccessCount|
|FailureCount|Hibásak száma|Mennyiség|Mennyiség|Sikertelen tranzakciók száma|FailureCount|
|SuccessLatency|Sikeres késés|Ezredmásodpercben|Átlag|A sikeres tranzakciók késése|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Mennyiség|Összes|A témakörben közzétett összes esemény|Témakör|
|PublishFailCount|Sikertelen események közzététele|Mennyiség|Összes|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodperc|Összes|Sikeres közzétételi késés (ezredmásodpercben)|None|
|MatchedEventCount|Egyeztetett események|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Mennyiség|Összes|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Mennyiség|Összes|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodperc|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eldobott események|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Mennyiség|Összes|A témakörben közzétett összes esemény|None|
|PublishFailCount|Sikertelen események közzététele|Mennyiség|Összes|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Mennyiség|Összes|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|None|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodperc|Összes|Sikeres közzétételi késés (ezredmásodpercben)|None|
|MatchedEventCount|Egyeztetett események|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Mennyiség|Összes|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Kézbesítési események|Mennyiség|Összes|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodperc|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Eldobott események|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason,EventSubscriptionName|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Egyeztetett események|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő összes esemény|None|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Mennyiség|Összes|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Mennyiség|Összes|Az esemény-előfizetésbe küldött összes esemény|None|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodperc|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|None|
|DroppedEventCount|Eldobott események|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Mennyiség|Összes|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Mennyiség|Összes|A témakörben közzétett összes esemény|None|
|PublishFailCount|Sikertelen események közzététele|Mennyiség|Összes|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Mennyiség|Összes|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|None|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodperc|Összes|Sikeres közzétételi késés (ezredmásodpercben)|None|



## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Mennyiség|Összes|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName, Operationresult tevékenységen|
|Kiszolgálóhibái|Kiszolgálói hibák.|Mennyiség|Összes|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName, Operationresult tevékenységen|
|UserErrors|Felhasználói hibák.|Mennyiség|Összes|A Microsoft. EventHub felhasználói hibái.|EntityName, Operationresult tevékenységen|
|QuotaExceededErrors|A kvóta túllépte a hibákat.|Mennyiség|Összes|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName, Operationresult tevékenységen|
|ThrottledRequests|Szabályozott kérelmek.|Mennyiség|Összes|A Microsoft. EventHub által szabályozott kérelmek.|EntityName, Operationresult tevékenységen|
|IncomingRequests|Bejövő kérelmek|Mennyiség|Összes|A Microsoft. EventHub bejövő kérései.|EntityName|
|IncomingMessages|Bejövő üzenetek|Mennyiség|Összes|A Microsoft. EventHub bejövő üzenetei.|EntityName|
|OutgoingMessages|Kimenő üzenetek|Mennyiség|Összes|A Microsoft. EventHub kimenő üzenetei.|EntityName|
|IncomingBytes|Bejövő bájtok.|Bájt|Összes|A Microsoft. EventHub bejövő bájtjai.|EntityName|
|OutgoingBytes|Kimenő bájtok.|Bájt|Összes|A Microsoft. EventHub kimenő bájtjai.|EntityName|
|Aktív kapcsolatai|Aktív kapcsolatai|Mennyiség|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|None|
|ConnectionsOpened|Megnyitott kapcsolatok.|Mennyiség|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|EntityName|
|ConnectionsClosed|A kapcsolatok lezárva.|Mennyiség|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|EntityName|
|CaptureBacklog|Várakozó fájlok rögzítése.|Mennyiség|Összes|A Microsoft. EventHub várakozó fájlok rögzítése.|EntityName|
|CapturedMessages|Rögzített üzenetek.|Mennyiség|Összes|A Microsoft. EventHub rögzített üzenetei.|EntityName|
|CapturedBytes|Rögzített bájtok száma.|Bájt|Összes|A Microsoft. EventHub rögzített bájtok száma.|EntityName|
|Méret|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|EntityName|
|INREQS|Bejövő kérelmek (elavult)|Mennyiség|Összes|Egy névtér összes bejövő küldési kérelme (elavult)|None|
|SUCCREQ|Sikeres kérések (elavult)|Mennyiség|Összes|A névtér összes sikeres kérelme (elavult)|None|
|FAILREQ|Sikertelen kérelmek (elavult)|Mennyiség|Összes|Névtér összes sikertelen kérelme (elavult)|None|
|SVRBSY|Kiszolgáló által foglalt hibák (elavult)|Mennyiség|Összes|Egy névtér összes kiszolgáló által foglalt hibája (elavult)|None|
|INTERer|Belső kiszolgálói hibák (elavult)|Mennyiség|Összes|Névtér összes belső kiszolgálóhiba (elavult)|None|
|MISCERR|Egyéb hibák (elavult)|Mennyiség|Összes|Névtér összes sikertelen kérelme (elavult)|None|
|INMSGS|Bejövő üzenetek (elavult) (elavult)|Mennyiség|Összes|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)|None|
|EHINMSGS|Bejövő üzenetek (elavult)|Mennyiség|Összes|Névtér összes bejövő üzenete (elavult)|None|
|OUTMSGS|Kimenő üzenetek (elavult) (elavult)|Mennyiség|Összes|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)|None|
|EHOUTMSGS|Kimenő üzenetek (elavult)|Mennyiség|Összes|Névtér összes kimenő üzenete (elavult)|None|
|EHINMBS|Bejövő bájtok (elavult) (elavult)|Bájt|Összes|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)|None|
|EHINBYTES|Bejövő bájtok (elavult)|Bájt|Összes|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)|None|
|EHOUTMBS|Kimenő bájtok (elavult) (elavult)|Bájt|Összes|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)|None|
|EHOUTBYTES|Kimenő bájtok (elavult)|Bájt|Összes|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)|None|
|EHABL|Archivált várakozó üzenetek (elavult)|Mennyiség|Összes|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)|None|
|EHAMSGS|Archivált üzenetek (elavult)|Mennyiség|Összes|Event hub archivált üzenetek egy névtérben (elavult)|None|
|EHAMBS|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összes|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)|None|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Mennyiség|Összes|A Microsoft. EventHub sikeres kérelmeinek száma.|Operationresult tevékenységen|
|Kiszolgálóhibái|Kiszolgálói hibák.|Mennyiség|Összes|Kiszolgálói hibák a Microsoft. EventHub esetében.|Operationresult tevékenységen|
|UserErrors|Felhasználói hibák.|Mennyiség|Összes|A Microsoft. EventHub felhasználói hibái.|Operationresult tevékenységen|
|QuotaExceededErrors|A kvóta túllépte a hibákat.|Mennyiség|Összes|A kvóta túllépte a Microsoft. EventHub hibáit.|Operationresult tevékenységen|
|ThrottledRequests|Szabályozott kérelmek.|Mennyiség|Összes|A Microsoft. EventHub által szabályozott kérelmek.|Operationresult tevékenységen|
|IncomingRequests|Bejövő kérelmek|Mennyiség|Összes|A Microsoft. EventHub bejövő kérései.|None|
|IncomingMessages|Bejövő üzenetek|Mennyiség|Összes|A Microsoft. EventHub bejövő üzenetei.|None|
|OutgoingMessages|Kimenő üzenetek|Mennyiség|Összes|A Microsoft. EventHub kimenő üzenetei.|None|
|IncomingBytes|Bejövő bájtok.|Bájt|Összes|A Microsoft. EventHub bejövő bájtjai.|None|
|OutgoingBytes|Kimenő bájtok.|Bájt|Összes|A Microsoft. EventHub kimenő bájtjai.|None|
|Aktív kapcsolatai|Aktív kapcsolatai|Mennyiség|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|None|
|ConnectionsOpened|Megnyitott kapcsolatok.|Mennyiség|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|None|
|ConnectionsClosed|A kapcsolatok lezárva.|Mennyiség|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|None|
|CaptureBacklog|Várakozó fájlok rögzítése.|Mennyiség|Összes|A Microsoft. EventHub várakozó fájlok rögzítése.|None|
|CapturedMessages|Rögzített üzenetek.|Mennyiség|Összes|A Microsoft. EventHub rögzített üzenetei.|None|
|CapturedBytes|Rögzített bájtok száma.|Bájt|Összes|A Microsoft. EventHub rögzített bájtok száma.|None|
|CPU|CPU|Százalék|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Szerepkör|
|AvailableMemory|Szabad memória|Százalék|Maximum|Az Event hub-fürt rendelkezésre álló memóriája a teljes memória százalékában.|Szerepkör|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjáróra vonatkozó kérelmek|Mennyiség|Összes|Az átjáróra vonatkozó kérelmek száma|HttpStatus|
|CategorizedGatewayRequests|Kategorizált átjáró-kérelmek|Mennyiség|Összes|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Aktív feldolgozók száma|Mennyiség|Maximum|Aktív feldolgozók száma|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/AutoscaleSettings

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrika értéke|Mennyiség|Átlag|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|MetricThreshold|Metrika küszöbértéke|Mennyiség|Átlag|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Mennyiség|Átlag|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.|None|
|ScaleActionsInitiated|Elindított skálázási műveletek|Mennyiség|Összes|A skálázási művelet iránya.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Elérhetőség|Százalék|Átlag|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|Rendelkezésre állási tesztek|Mennyiség|Mennyiség|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Oldal betöltési hálózati kapcsolati ideje|Ezredmásodpercben|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|None|
|browserTimings/processingDuration|Ügyfél feldolgozási ideje|Ezredmásodpercben|Átlag|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|None|
|browserTimings/receiveDuration|Válaszidő fogadása|Ezredmásodpercben|Átlag|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|None|
|browserTimings/sendDuration|Kérelem küldési ideje|Ezredmásodpercben|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|None|
|browserTimings/totalDuration|Böngésző oldalának betöltési ideje|Ezredmásodpercben|Átlag|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|None|
|függőségek/darabszám|Függőségi hívások|Mennyiség|Mennyiség|Az alkalmazás által külső erőforrások felé indított hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/időtartam|Függőség időtartama|Ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/sikertelen|Függőségi hívások hibái|Mennyiség|Mennyiség|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|Oldalmegtekintések/darabszám|Lapok nézetei|Mennyiség|Mennyiség|Lapok nézeteinek száma.|művelet/szintetikus, felhő/roleName|
|Oldalmegtekintések/időtartam|Oldal nézet betöltési ideje|Ezredmásodpercben|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus, felhő/roleName|
|performanceCounters/requestExecutionTime|HTTP-kérelem végrehajtási ideje|Ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|felhő/roleInstance|
|performanceCounters/requestsInQueue|HTTP-kérelmek az alkalmazás-várólistában|Mennyiség|Átlag|Az alkalmazás-kérelmek várólistájának hossza|felhő/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-kérelmek gyakorisága|CountPerSecond|Átlag|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|felhő/roleInstance|
|performanceCounters/exceptionsPerSecond|Kivételek aránya|CountPerSecond|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|felhő/roleInstance|
|performanceCounters/processIOBytesPerSecond|Folyamat i/o-sebessége|BytesPerSecond|Átlag|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|felhő/roleInstance|
|performanceCounters/processCpuPercentage|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|felhő/roleInstance|
|performanceCounters/processorCpuPercentage|Processzoridő|Százalék|Átlag|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|felhő/roleInstance|
|performanceCounters/memoryAvailableBytes|Igénybe vehető memória|Bájt|Átlag|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|felhő/roleInstance|
|performanceCounters/processPrivateBytes|Saját bájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|felhő/roleInstance|
|kérelmek/időtartam|Kiszolgáló válaszideje|Ezredmásodpercben|Átlag|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/darabszám|Kiszolgálói kérelmek|Mennyiség|Mennyiség|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|Sikertelen kérelmek|Mennyiség|Mennyiség|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések > = 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, kérelem/siker, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Átlag|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kivételek/darabszám|Kivételek|Mennyiség|Mennyiség|Az összes nem kezelt kivétel összesített száma.|felhő/roleName, felhő/roleInstance, ügyfél/típus|
|kivételek/böngésző|Böngészőkivételek|Mennyiség|Mennyiség|A böngészőben fellépő nem kezelt kivételek száma.|ügyfél/isServer, felhő/roleName|
|kivételek/kiszolgáló|Kiszolgálói kivételek|Mennyiség|Mennyiség|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|ügyfél/isServer, felhő/roleName, felhő/roleInstance|
|nyomkövetés/darabszám|Hívásláncok|Mennyiség|Mennyiség|Nyomkövetési dokumentumok száma|nyomkövetés/severityLevel, művelet/szintetikus, felhő/roleName, felhő/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Összes szolgáltatási API-találat|Mennyiség|Mennyiség|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Általános szolgáltatás API-késése|Ezredmásodperc|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Összes szolgáltatás API-eredményei|Mennyiség|Mennyiség|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Tár teljes telítettsége|Százalék|Átlag|Tár kapacitásának használata|ActivityType, ActivityName, TransactionType|
|Elérhetőség|Tár teljes rendelkezésre állása|Százalék|Átlag|Tár kéréseinek rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CacheUtilization|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|None|
|QueryDuration|Lekérdezés időtartama|Ezredmásodperc|Átlag|Lekérdezések időtartama másodpercben|QueryStatus|
|IngestionUtilization|Betöltés kihasználtsága|Százalék|Átlag|A használatban lévő betöltési tárolóhelyek aránya a fürtben|None|
|KeepAlive|Életben tartása|Mennyiség|Átlag|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|None|
|IngestionVolumeInMB|Betöltési mennyiség (MB)|Mennyiség|Összes|A betöltött adatmennyiség teljes mennyisége a fürtön (MB)|Adatbázis|
|IngestionLatencyInSeconds|Betöltési késleltetés (másodperc)|másodperc|Átlag|A lenyelési idő a forrástól (például az üzenet a EventHub) a fürthöz másodpercek alatt|None|
|EventsProcessedForEventHubs|Feldolgozott események (Event/IoT hubok esetében)|Mennyiség|Összes|A fürt által az esemény/IoT Hub betöltéskor feldolgozott események száma|EventStatus|
|IngestionResult|Betöltés eredménye|Mennyiség|Mennyiség|Betöltési műveletek száma|IngestionResultDetails|
|CPU|CPU|Százalék|Átlag|CPU-kihasználtsági szint|None|
|ContinuousExportNumOfRecordsExported|Folyamatos exportálás – számú exportált rekord|Mennyiség|Összes|Az exportált rekordok száma az exportálási művelet során írt összes tárolási összetevő esetében|None|
|ExportUtilization|Exportálás kihasználtsága|Százalék|Maximum|Exportálás kihasználtsága|None|
|ContinuousExportPendingCount|Folyamatos exportálás függőben lévő darabszáma|Mennyiség|Maximum|A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma|None|
|ContinuousExportMaxLatenessMinutes|Folyamatos exportálás maximális késői percben|Mennyiség|Maximum|Az összes függőben lévő folyamatos exportálás és a végrehajtásra készen álló maximális idő percben|None|
|ContinuousExportResult|Folyamatos exportálás eredménye|Mennyiség|Mennyiség|Azt jelzi, hogy a folyamatos exportálás sikeres vagy sikertelen volt-e|ContinuousExportName, eredmény, adatbázis|
|StreamingIngestDuration|Folyamatos átvitel időtartama|Ezredmásodperc|Átlag|Folyamatos átvitel időtartama (ezredmásodperc)|None|
|StreamingIngestDataRate|Adatforgalom adatátviteli sebessége|Mennyiség|Átlag|Adatforgalom adatátviteli sebessége (MB/s)|None|
|SteamingIngestRequestRate|Folyamatos átviteli kérelmek gyakorisága|Mennyiség|RateRequestsPerSecond|Folyamatos átviteli kérelmek aránya (kérelmek/másodperc)|None|
|StreamingIngestResults|Folyamatos átvitel eredménye|Mennyiség|Átlag|Folyamatos átvitel eredménye|Eredmény|
|TotalNumberOfConcurrentQueries|Az egyidejű lekérdezések teljes száma|Mennyiség|Összes|Az egyidejű lekérdezések teljes száma|None|
|TotalNumberOfThrottledQueries|A szabályozott lekérdezések teljes száma|Mennyiség|Összes|A szabályozott lekérdezések teljes száma|None|
|TotalNumberOfThrottledCommands|A szabályozott parancsok teljes száma|Mennyiség|Összes|A szabályozott parancsok teljes száma|CommandType|
|TotalNumberOfExtents|Egységek teljes száma|Mennyiség|Összes|Adategységek teljes száma|None|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Mennyiség|Összes|Az elindított munkafolyamat-futtatások száma.|None|
|RunsCompleted|Befejezett futtatások|Mennyiség|Összes|A befejezett munkafolyamat-futtatások száma.|None|
|RunsSucceeded|Sikeres futtatások|Mennyiség|Összes|A sikeres munkafolyamat-futtatások száma.|None|
|RunsFailed|Sikertelen futtatások|Mennyiség|Összes|Sikertelen munkafolyamat-futtatások száma.|None|
|RunsCancelled|Megszakított futtatások|Mennyiség|Összes|A megszakított munkafolyamat-futtatások száma.|None|
|RunLatency|Késés futtatása|másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|None|
|RunSuccessLatency|Sikeres Futtatás késése|másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|None|
|RunThrottledEvents|Szabályozott események futtatása|Mennyiség|Összes|A munkafolyamat-műveletek vagy az elindított események száma.|None|
|RunStartThrottledEvents|Indítási leszabályozású események futtatása|Mennyiség|Összes|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|None|
|RunFailurePercentage|Sikertelen futtatások százalékos aránya|Százalék|Összes|A munkafolyamat-futtatások százalékos aránya meghiúsult.|None|
|ActionsStarted|Elindított műveletek |Mennyiség|Összes|Az elindított munkafolyamat-műveletek száma.|None|
|ActionsCompleted|Befejezett műveletek |Mennyiség|Összes|A befejezett munkafolyamat-műveletek száma.|None|
|ActionsSucceeded|Sikeres műveletek |Mennyiség|Összes|A sikeres munkafolyamat-műveletek száma.|None|
|ActionsFailed|Sikertelen műveletek |Mennyiség|Összes|Nem sikerült a munkafolyamat-műveletek száma.|None|
|ActionsSkipped|Kihagyott műveletek |Mennyiség|Összes|A kihagyott munkafolyamat-műveletek száma.|None|
|ActionLatency|Művelet késése |másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|None|
|ActionSuccessLatency|Művelet sikerességi késése |másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|None|
|ActionThrottledEvents|Művelet által szabályozott események|Mennyiség|Összes|A munkafolyamat-műveletek által szabályozott események száma..|None|
|TriggersStarted|Elindított eseményindítók |Mennyiség|Összes|Az elindított munkafolyamat-eseményindítók száma.|None|
|TriggersCompleted|Befejezett eseményindítók |Mennyiség|Összes|A befejezett munkafolyamat-eseményindítók száma.|None|
|TriggersSucceeded|Sikeres eseményindítók |Mennyiség|Összes|A sikeres munkafolyamat-eseményindítók száma.|None|
|TriggersFailed|Sikertelen eseményindítók |Mennyiség|Összes|Sikertelen munkafolyamat-eseményindítók száma.|None|
|TriggersSkipped|Kihagyott eseményindítók|Mennyiség|Összes|A kihagyott munkafolyamat-eseményindítók száma.|None|
|TriggersFired|Kilőtt eseményindítók |Mennyiség|Összes|A kilőtt munkafolyamat-eseményindítók száma.|None|
|TriggerLatency|Trigger késése |másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|None|
|TriggerFireLatency|Kiváltó tűz késése |másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|None|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|None|
|TriggerThrottledEvents|Kiváltott események indítása|Mennyiség|Összes|A beállított munkafolyamat-triggerek száma.|None|
|BillableActionExecutions|Számlázható műveletek végrehajtása|Mennyiség|Összes|A számlázott munkafolyamat-műveletek végrehajtásának száma.|None|
|BillableTriggerExecutions|Számlázható trigger-végrehajtások|Mennyiség|Összes|A számlázott munkafolyamat-trigger-végrehajtások száma.|None|
|TotalBillableExecutions|Számlázandó végrehajtások összesen|Mennyiség|Összes|A számlázandó munkafolyamat-végrehajtások száma.|None|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Mennyiség|Összes|A számlázott natív művelet-végrehajtások száma.|None|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Mennyiség|Összes|A számlázott szabványos összekötő-végrehajtások száma.|None|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Mennyiség|Összes|A számlázható tárterület-használat végrehajtásának száma.|None|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Mennyiség|Összes|A számlázott natív művelet-végrehajtások száma.|None|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Mennyiség|Összes|A számlázott szabványos összekötő-végrehajtások száma.|None|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Mennyiség|Összes|A számlázható tárterület-használat végrehajtásának száma.|None|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Mennyiség|Összes|Az elindított munkafolyamat-futtatások száma.|None|
|RunsCompleted|Befejezett futtatások|Mennyiség|Összes|A befejezett munkafolyamat-futtatások száma.|None|
|RunsSucceeded|Sikeres futtatások|Mennyiség|Összes|A sikeres munkafolyamat-futtatások száma.|None|
|RunsFailed|Sikertelen futtatások|Mennyiség|Összes|Sikertelen munkafolyamat-futtatások száma.|None|
|RunsCancelled|Megszakított futtatások|Mennyiség|Összes|A megszakított munkafolyamat-futtatások száma.|None|
|RunLatency|Késés futtatása|másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|None|
|RunSuccessLatency|Sikeres Futtatás késése|másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|None|
|RunThrottledEvents|Szabályozott események futtatása|Mennyiség|Összes|A munkafolyamat-műveletek vagy az elindított események száma.|None|
|RunStartThrottledEvents|Indítási leszabályozású események futtatása|Mennyiség|Összes|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|None|
|RunFailurePercentage|Sikertelen futtatások százalékos aránya|Százalék|Összes|A munkafolyamat-futtatások százalékos aránya meghiúsult.|None|
|ActionsStarted|Elindított műveletek |Mennyiség|Összes|Az elindított munkafolyamat-műveletek száma.|None|
|ActionsCompleted|Befejezett műveletek |Mennyiség|Összes|A befejezett munkafolyamat-műveletek száma.|None|
|ActionsSucceeded|Sikeres műveletek |Mennyiség|Összes|A sikeres munkafolyamat-műveletek száma.|None|
|ActionsFailed|Sikertelen műveletek |Mennyiség|Összes|Nem sikerült a munkafolyamat-műveletek száma.|None|
|ActionsSkipped|Kihagyott műveletek |Mennyiség|Összes|A kihagyott munkafolyamat-műveletek száma.|None|
|ActionLatency|Művelet késése |másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|None|
|ActionSuccessLatency|Művelet sikerességi késése |másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|None|
|ActionThrottledEvents|Művelet által szabályozott események|Mennyiség|Összes|A munkafolyamat-műveletek által szabályozott események száma..|None|
|TriggersStarted|Elindított eseményindítók |Mennyiség|Összes|Az elindított munkafolyamat-eseményindítók száma.|None|
|TriggersCompleted|Befejezett eseményindítók |Mennyiség|Összes|A befejezett munkafolyamat-eseményindítók száma.|None|
|TriggersSucceeded|Sikeres eseményindítók |Mennyiség|Összes|A sikeres munkafolyamat-eseményindítók száma.|None|
|TriggersFailed|Sikertelen eseményindítók |Mennyiség|Összes|Sikertelen munkafolyamat-eseményindítók száma.|None|
|TriggersSkipped|Kihagyott eseményindítók|Mennyiség|Összes|A kihagyott munkafolyamat-eseményindítók száma.|None|
|TriggersFired|Kilőtt eseményindítók |Mennyiség|Összes|A kilőtt munkafolyamat-eseményindítók száma.|None|
|TriggerLatency|Trigger késése |másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|None|
|TriggerFireLatency|Kiváltó tűz késése |másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|None|
|TriggerSuccessLatency|Sikeres triggerek késése |másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|None|
|TriggerThrottledEvents|Kiváltott események indítása|Mennyiség|Összes|A beállított munkafolyamat-triggerek száma.|None|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|None|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|None|
|IntegrationServiceEnvironmentConnectorProcessorUsage|integrációs szolgáltatási környezet összekötő-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|None|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Összekötő memóriahasználat integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Befejezett futtatások|Befejezett futtatások|Mennyiség|Összes|A munkaterülethez sikeresen befejezett futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Elindított futtatások|Elindított futtatások|Mennyiség|Összes|A munkaterülethez elindított futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Sikertelen futtatások|Sikertelen futtatások|Mennyiség|Összes|Sikertelen futtatások száma ehhez a munkaterülethez|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|A modell regisztrálása sikerült|A modell regisztrálása sikerült|Mennyiség|Összes|A munkaterületen sikeres modell-regisztrációk száma|Alkalmazási helyzet|
|A modell regisztrálása nem sikerült|A modell regisztrálása nem sikerült|Mennyiség|Összes|A munkaterületen meghiúsult modell-regisztrációk száma|Forgatókönyv, StatusCode|
|Modell-üzembehelyezés elindítva|Modell-üzembehelyezés elindítva|Mennyiség|Összes|A munkaterületen elindított modellek központi telepítésének száma|Alkalmazási helyzet|
|Modell-üzembehelyezés sikerült|Modell-üzembehelyezés sikerült|Mennyiség|Összes|A munkaterületen sikeres központi telepítési modellek száma|Alkalmazási helyzet|
|Modell-üzembehelyezés sikertelen|Modell-üzembehelyezés sikertelen|Mennyiség|Összes|A munkaterületen sikertelen telepítési modellek száma|Forgatókönyv, StatusCode|
|Csomópontok összesen|Csomópontok összesen|Mennyiség|Átlag|A csomópontok száma összesen. Ez az összeg magában foglalja az aktív csomópontok, a tétlen csomópontok, a nem használható csomópontok, a Premepted csomópontok és a csomópontok elhagyását|Forgatókönyv, ClusterName|
|Aktív csomópontok|Aktív csomópontok|Mennyiség|Átlag|Acitve-csomópontok száma Ezek azok a csomópontok, amelyek aktívan futtatnak egy feladatot.|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Tétlen csomópontok|Mennyiség|Átlag|Az üresjárati csomópontok száma. Az üresjárati csomópontok azok a csomópontok, amelyek nem futtatnak feladatokat, de az elérhetővé tehetik az új feladatot.|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Használhatatlan csomópontok|Mennyiség|Átlag|Használhatatlan csomópontok száma Néhány feloldhatatlan probléma miatt nem használható csomópontok nem működőképesek. Az Azure újrahasznosítja ezeket a csomópontokat.|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Előzik-csomópontok|Mennyiség|Átlag|Előzik-csomópontok száma Ezek a csomópontok az alacsony prioritású csomópontok, amelyek a rendelkezésre álló csomópont-készletből származnak.|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Csomópontok elhagyása|Mennyiség|Átlag|A csomópontok elhagyásának száma. A csomópontok elhagyják azokat a csomópontokat, amelyek éppen befejezték a feladatok feldolgozását, és az inaktív állapotba kerülnek.|Forgatókönyv, ClusterName|
|Magok összesen|Magok összesen|Mennyiség|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Aktív magok|Aktív magok|Mennyiség|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Üresjárati magok|Üresjárati magok|Mennyiség|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Használhatatlan magok|Használhatatlan magok|Mennyiség|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Előzik magok|Előzik magok|Mennyiség|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Magok kihagyása|Magok kihagyása|Mennyiség|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtsága (%)|Kvóta kihasználtsága (%)|Mennyiség|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Mennyiség|Átlag|CPU (előzetes verzió)|Forgatókönyv, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Mennyiség|Átlag|GPU (előzetes verzió)|Forgatókönyv, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Mennyiség|Mennyiség|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|
|Elérhetőség|Elérhetőség|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Mediaservices/streamingEndpoints

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve.|OutputFormat|
|SuccessE2ELatency|A Befejezés végének késése|Ezredmásodperc|Átlag|A sikeres kérelmek átlagos késése ezredmásodpercben.|OutputFormat|
|Kérelmek|Kérelmek|Mennyiség|Összes|Továbbítási végpontra irányuló kérelmek.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AssetQuota|Eszköz kvótája|Mennyiség|Átlag|Hány eszköz engedélyezett a jelenlegi Media Service-fiókhoz|None|
|AssetCount|Eszközök száma|Mennyiség|Átlag|Az aktuális Media Service-fiókban már létrehozott eszközök száma|None|
|AssetQuotaUsedPercentage|Eszköz kvótájának kihasználtsága (%)|Százalék|Átlag|Az eszköz által használt százalék a jelenlegi Media Service-fiókban|None|
|ContentKeyPolicyQuota|Tartalmi kulcs házirend-kvótája|Mennyiség|Átlag|Az aktuális Media Service-fiókhoz tartozó tartalmi kulcsokra vonatkozó házirendek száma|None|
|ContentKeyPolicyCount|Tartalmi kulcsokra vonatkozó szabályzatok száma|Mennyiség|Átlag|A jelenlegi Media Service-fiókban már létrejött a tartalmi kulcs házirendjeinek száma|None|
|ContentKeyPolicyQuotaUsedPercentage|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|Százalék|Átlag|A tartalmi kulcsra vonatkozó házirend százaléka a jelenlegi Media Service-fiókban|None|
|StreamingPolicyQuota|Streaming Policy-kvóta|Mennyiség|Átlag|Az aktuális Media Service-fiókhoz tartozó folyamatos átviteli házirendek száma|None|
|StreamingPolicyCount|Folyamatos átviteli szabályzatok száma|Mennyiség|Átlag|A jelenlegi Media Service-fiókban már létrejöttek a folyamatos átviteli házirendek|None|
|StreamingPolicyQuotaUsedPercentage|Adatfolyam-házirend kvótájának kihasználtsága (%)|Százalék|Átlag|A streaming Policy használt százalék a jelenlegi Media Service-fiókban|None|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/kötetek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageReadLatency|Olvasási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|None|
|AverageWriteLatency|Írási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Írási késleltetés átlagos száma ezredmásodpercben|None|
|VolumeLogicalSize|Kötet logikai mérete|Bájt|Átlag|A kötet logikai mérete (felhasznált bájtok)|None|
|VolumeSnapshotSize|Kötet pillanatképének mérete|Bájt|Átlag|A köteten található összes pillanatkép mérete|None|
|ReadIops|IOPS olvasása|CountPerSecond|Átlag|Olvasási/kimenő műveletek másodpercenként|None|
|WriteIops|IOPS írása|CountPerSecond|Átlag|Írási/kimenő műveletek másodpercenként|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Lefoglalt kötet-készlet|Bájt|Átlag|A készlet lefoglalt felhasznált mérete|None|
|VolumePoolTotalLogicalSize|Kötet készletének teljes logikai mérete|Bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Eljuttatott bájtok|Bájt|Összes|A hálózati adapter által eljuttatott bájtok száma|None|
|BytesReceivedRate|Fogadott bájtok száma|Bájt|Összes|A hálózati adapter által fogadott bájtok száma|None|
|PacketsSentRate|Küldött csomagok|Mennyiség|Összes|A hálózati adapter által küldött csomagok száma|None|
|PacketsReceivedRate|Fogadott csomagok|Mennyiség|Összes|A hálózati adapter által fogadott csomagok száma|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|Adatelérési út rendelkezésre állása|Mennyiség|Átlag|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress,FrontendPort|
|DipAvailability|Állapot mintavételi állapota|Mennyiség|Átlag|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Bájtok száma|Mennyiség|Összes|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, irány|
|PacketCount|Csomagok száma|Mennyiség|Összes|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SYNCount|SYN-szám|Mennyiség|Összes|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SnatConnectionCount|SNAT-kapcsolatok száma|Mennyiség|Összes|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Lefoglalt SNAT portok (előzetes verzió)|Mennyiség|Összes|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Használt SNAT-portok (előzetes verzió)|Mennyiség|Összes|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezési kötet|Mennyiség|Összes|A DNS-zónák számára kiszolgált lekérdezések száma|None|
|RecordSetCount|Rekordok készletének száma|Mennyiség|Maximum|Rekordhalmazok száma egy DNS-zónában|None|
|RecordSetCapacityUtilization|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|None|
|PacketsDroppedDDoS|Eldobott bejövő csomagok DDoS|CountPerSecond|Maximum|Eldobott bejövő csomagok DDoS|None|
|PacketsForwardedDDoS|Továbbított bejövő csomagok DDoS|CountPerSecond|Maximum|Továbbított bejövő csomagok DDoS|None|
|TCPPacketsInDDoS|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|None|
|TCPPacketsDroppedDDoS|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok eldobott DDoS|None|
|TCPPacketsForwardedDDoS|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok továbbított DDoS|None|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok DDoS|None|
|UDPPacketsDroppedDDoS|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok eldobott DDoS|None|
|UDPPacketsForwardedDDoS|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok továbbított DDoS|None|
|BytesInDDoS|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|None|
|BytesDroppedDDoS|Eldobott bejövő bájtok DDoS|BytesPerSecond|Maximum|Eldobott bejövő bájtok DDoS|None|
|BytesForwardedDDoS|Továbbított bejövő bájtok DDoS|BytesPerSecond|Maximum|Továbbított bejövő bájtok DDoS|None|
|TCPBytesInDDoS|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|None|
|TCPBytesDroppedDDoS|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|None|
|TCPBytesForwardedDDoS|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|None|
|UDPBytesInDDoS|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|None|
|UDPBytesDroppedDDoS|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|None|
|UDPBytesForwardedDDoS|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|None|
|IfUnderDDoSAttack|DDoS-támadás alatt vagy nem|Mennyiség|Maximum|DDoS-támadás alatt vagy nem|None|
|DDoSTriggerTCPPackets|Bejövő TCP-csomagok a DDoS-mérséklés indításához|CountPerSecond|Maximum|Bejövő TCP-csomagok a DDoS-mérséklés indításához|None|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|None|
|DDoSTriggerSYNPackets|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|None|
|VipAvailability|Adatelérési út rendelkezésre állása|Mennyiség|Átlag|Átlagos IP-cím rendelkezésre állása időszakonként|Port|
|ByteCount|Bájtok száma|Mennyiség|Összes|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|PacketCount|Csomagok száma|Mennyiség|Összes|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|SynCount|SYN-szám|Mennyiség|Összes|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|A pingelések időpontjának kerekítése egy virtuális géphez|Ezredmásodpercben|Átlag|A célként megadott virtuális gépre eljuttatott pingelések menetének időpontja|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Sikertelen pingelések egy virtuális géphez|Százalék|Átlag|A hibás pingelések száma a cél virtuális gép összes elküldéses pingelésének százalékában|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ApplicationRuleHit|Alkalmazás-szabályok találatok száma|Mennyiség|Összes|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|NetworkRuleHit|Hálózati szabályok találatok száma|Mennyiség|Összes|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|
|FirewallHealth|Tűzfal állapota|Százalék|Átlag|Tűzfal állapota|Állapot, ok|
|DataProcessed|Feldolgozott adatok|Bájt|Összes|A tűzfal által feldolgozott adatmennyiség teljes mennyisége|None|
|SNATPortUtilization|SNAT-portok kihasználtsága|Százalék|Átlag|SNAT-portok kihasználtsága|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Adatforgalom|Adatforgalom|BytesPerSecond|Átlag|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|None|
|UnhealthyHostCount|Nem kifogástalan állapotú gazdagépek száma|Mennyiség|Átlag|Sérült háttérbeli gazdagépek száma|BackendSettingsPool|
|HealthyHostCount|Kifogástalan állapotú gazdagépek száma|Mennyiség|Átlag|Az egészséges háttérbeli gazdagépek száma|BackendSettingsPool|
|TotalRequests|Összes kérelem|Mennyiség|Összes|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Percenkénti kérelmek száma kifogástalan állapotú gazdagépen|Mennyiség|Átlag|Kérelmek átlagos száma percenként, a készletben lévő kifogástalan állapotú gazdagépek esetében|BackendSettingsPool|
|FailedRequests|Sikertelen kérelmek|Mennyiség|Összes|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|ResponseStatus|Válasz állapota|Mennyiség|Összes|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Összege|Aktuális kapcsolatok|Mennyiség|Összes|Application Gatewaysal létesített aktuális kapcsolatok száma|None|
|CpuUtilization|CPU-kihasználtság|Százalék|Átlag|A Application Gateway aktuális CPU-kihasználtsága|None|
|CapacityUnits|Aktuális kapacitási egységek|Mennyiség|Átlag|Felhasznált kapacitási egységek|None|
|ComputeUnits|Aktuális számítási egységek|Mennyiség|Átlag|Felhasznált számítási egységek|None|
|BackendResponseStatus|Háttérbeli válasz állapota|Mennyiség|Összes|A háttérbeli tagok által létrehozott HTTP-válasz kódok száma. Ez nem tartalmazza a Application Gateway által létrehozott válasz-kódokat.|BackendServer, Háttérkészletek, Backendhttpsetting értékre, HttpStatusGroup|
|TlsProtocol|Ügyfél TLS protokoll|Mennyiség|Összes|Azoknak a TLS-és nem TLS-kérelmeknek a száma, amelyek az ügyfél által kezdeményezett Application Gatewaykal létesített kapcsolatban állnak. A TLS protokoll terjesztésének megtekintéséhez szűrje a Dimension TLS protokollt.|Figyelő, TlsProtocol|
|BytesSent|Eljuttatott bájtok|Bájt|Összes|A Application Gateway által az ügyfeleknek eljuttatott bájtok teljes száma|Figyelő|
|BytesReceived|Fogadott bájtok száma|Bájt|Összes|A Application Gateway által az ügyfelektől fogadott bájtok teljes száma|Figyelő|
|ClientRtt|Ügyfél-RTT|Ezredmásodpercben|Átlag|Az ügyfelek és a Application Gateway közötti átlagos menetidő. Ez a metrika azt jelzi, hogy mennyi időt vesz igénybe a kapcsolatok és a visszaküldési visszaigazolás|Figyelő|
|ApplicationGatewayTotalTime|Application Gateway teljes idő|Ezredmásodpercben|Átlag|A kérelem feldolgozásának és a válasz elküldésekor elvégezhető átlagos idő. Ez az időszak átlaga, amikor a Application Gateway megkapja egy HTTP-kérelem első bájtját, amikor a válasz küldése művelet befejeződik. Fontos megjegyezni, hogy ez általában a Application Gateway feldolgozási időt, az időpontot, amikor a kérés és a válasz csomagjai a hálózaton keresztül utaznak, valamint a háttér-kiszolgáló válaszának időpontját.|Figyelő|
|BackendConnectTime|Háttérbeli kapcsolat ideje|Ezredmásodpercben|Átlag|A háttér-kiszolgálóval létesített kapcsolatok létrehozásához töltött idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendFirstByteResponseTime|Háttérbeli első bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz fejléc első bájtjának fogadása között eltelt idő, a háttér-kiszolgáló feldolgozási idejének megközelítő időpontja|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendLastByteResponseTime|Háttérbeli utolsó bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz törzse utolsó bájtjának fogadása között eltelt idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|MatchedCount|Webalkalmazási tűzfal teljes szabályának eloszlása|Mennyiség|Összes|Webalkalmazási tűzfal – teljes szabály eloszlása a bejövő forgalomhoz|Szerepkörcsoportot, RuleId|
|BlockedCount|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Mennyiség|Összes|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Szerepkörcsoportot, RuleId|
|BlockedReqCount|Webalkalmazási tűzfal letiltott kérelmek száma|Mennyiség|Összes|Webalkalmazási tűzfal letiltott kérelmek száma|None|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageBandwidth|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|None|
|P2SBandwidth|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|None|
|P2SConnectionCount|P2S-kapcsolatok száma|Mennyiség|Maximum|Átjáró pont – hely kapcsolatának száma|Protocol (Protokoll)|
|TunnelAverageBandwidth|Bújtatási sávszélesség|BytesPerSecond|Átlag|Egy alagút átlagos sávszélessége (bájt/s)|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Alagút kimenő bájtjai|Bájt|Összes|Egy alagút kimenő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Alagutak bejövő bájtjai|Bájt|Összes|Alagút bejövő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagjai|Mennyiség|Összes|Alagút kimenő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Bújtatási bejövő csomagok|Mennyiség|Összes|Alagút bejövő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Alagút kimenő terminálszolgáltatási eltérési csomagjainak eldobása|Mennyiség|Összes|Kimenő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Az alagút bejövő HOZZÁFÉRÉSe nem egyezik a csomagok eldobásával|Mennyiség|Összes|Bejövő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Mennyiség|Átlag|Rx-fény szintje dBm-ben|Hivatkozás, sáv|
|TxLightLevel|TxLightLevel|Mennyiség|Átlag|TX-fény szintje dBm-ben|Hivatkozás, sáv|
|AdminState|AdminState|Mennyiség|Átlag|A port rendszergazdai állapota|Hivatkozás|
|LineProtocol|LineProtocol|Mennyiség|Átlag|A port protokolljának állapota|Hivatkozás|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|Hivatkozás|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|Hivatkozás|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|PeeredCircuitSKey|
|BgpAvailability|BGP rendelkezésre állása|Százalék|Átlag|A BGP rendelkezésre állása a MSEE az összes társ felé.|PeeringType, társ|
|ArpAvailability|ARP rendelkezésre állása|Százalék|Átlag|Az ARP és a MSEE közötti rendelkezésre állás az összes társ felé.|PeeringType, társ|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Átlag|Bejövő adatforgalom (bit/mp)|None|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Átlag|Kimenő adatforgalom másodpercenkénti száma|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/társak

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|None|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|None|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|None|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|None|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Átlag|BITS ingressing Azure másodpercenként|Kapcsolatnév|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Átlag|BITS egressing Azure másodpercenként|Kapcsolatnév|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Visszaadott végponti lekérdezések|Mennyiség|Összes|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végponti állapot végpont szerint|Mennyiség|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ProbesFailedPercent|%-Os mintavétel sikertelen|Százalék|Átlag|a kapcsolat figyelési mintavételének%-a meghiúsult|None|
|AverageRoundtripMs|Átlagos menetidő (MS)|Ezredmásodpercben|Átlag|A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS)|None|
|ChecksFailedPercent|Sikertelen ellenőrzések százalékos aránya (előzetes verzió)|Százalék|Átlag|a kapcsolat figyelési ellenőrzése nem sikerült|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Oda-és visszaút időpontja (MS) (előzetes verzió)|Ezredmásodpercben|Átlag|A kapcsolat figyelési ellenőrzésének időkorlátja ezredmásodpercben|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RequestCount|Kérelmek száma|Mennyiség|Összes|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|RequestSize|Kérelem mérete|Bájt|Összes|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|ResponseSize|Válasz mérete|Bájt|Összes|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BillableResponseSize|Számlázható válasz mérete|Bájt|Összes|A HTTP/S proxy válaszként küldött számlázandó bájtjainak (minimális 2KB) száma az ügyfeleknek.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendRequestCount|Háttérbeli kérelmek száma|Mennyiség|Összes|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Háttérbeli kérelmek késése|Ezredmásodpercben|Átlag|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Háttérszolgáltatás|
|TotalLatency|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Háttér állapotának százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|WebApplicationFirewallRequestCount|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Mennyiség|Összes|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|regisztráció. All|Regisztrációs műveletek|Mennyiség|Összes|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |None|
|regisztráció. Create|Regisztráció-létrehozási műveletek|Mennyiség|Összes|Az összes sikeres regisztrációs létrehozás száma.|None|
|regisztráció. frissítés|Regisztrációs frissítési műveletek|Mennyiség|Összes|A sikeres regisztrációs frissítések száma.|None|
|regisztráció. Get|Regisztrálási olvasási műveletek|Mennyiség|Összes|A sikeres regisztrációs lekérdezések száma.|None|
|regisztráció. Delete|Regisztrációs törlési műveletek|Mennyiség|Összes|A regisztráció sikeres törléseinak száma.|None|
|bejövő|Bejövő üzenetek|Mennyiség|Összes|A sikeres küldési API-hívások száma. |None|
|bejövő. ütemezett|Ütemezett leküldéses értesítések elküldve|Mennyiség|Összes|Ütemezett leküldéses értesítések megszakítva|None|
|bejövő. ütemezett. Mégse|Ütemezett leküldéses értesítések megszakítva|Mennyiség|Összes|Ütemezett leküldéses értesítések megszakítva|None|
|ütemezett. függőben|Függőben lévő ütemezett értesítések|Mennyiség|Összes|Függőben lévő ütemezett értesítések|None|
|a telepítés. All|Telepítési felügyeleti műveletek|Mennyiség|Összes|Telepítési felügyeleti műveletek|None|
|telepítés. Get|Telepítési műveletek beolvasása|Mennyiség|Összes|Telepítési műveletek beolvasása|None|
|telepítési. upsert|Telepítési műveletek létrehozása vagy frissítése|Mennyiség|Összes|Telepítési műveletek létrehozása vagy frissítése|None|
|Installation. patch|Javítások telepítési műveletei|Mennyiség|Összes|Javítások telepítési műveletei|None|
|telepítés. Törlés|Telepítési műveletek törlése|Mennyiség|Összes|Telepítési műveletek törlése|None|
|kimenő. allpns. sikeres|Sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|None|
|kimenő. allpns. invalidpayload|Hasznos adatok|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|None|
|kimenő. allpns. pnserror|Külső értesítési rendszerhibák|Mennyiség|Összes|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|None|
|kimenő. allpns. channelerror|Csatorna hibái|Mennyiség|Összes|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|None|
|kimenő. allpns. badorexpiredchannel|Rossz vagy lejárt csatorna-hibák|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|None|
|kimenő. wns. sikeres|WNS sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|None|
|kimenő. wns. invalidcredentials|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|None|
|kimenő. wns. badchannel|WNS hibás csatorna hiba|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|None|
|kimenő. wns. expiredchannel|WNS lejárt csatorna hibája|Mennyiség|Összes|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapota: 410 elveszett).|None|
|kimenő. wns. szabályozva|WNS-szabályozású értesítések|Mennyiség|Összes|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapota: 406, nem fogadható el).|None|
|kimenő. wns. tokenproviderunreachable|WNS-hitelesítési hibák (nem érhető el)|Mennyiség|Összes|A Windows Live nem érhető el.|None|
|kimenő. wns. invalidtoken|WNS-hitelesítési hibák (érvénytelen token)|Mennyiség|Összes|A WNS számára megadott jogkivonat érvénytelen (WNS-állapot: 401 jogosulatlan).|None|
|kimenő. wns. wrongtoken|WNS-hitelesítési hibák (hibás token)|Mennyiség|Összes|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|None|
|kimenő. wns. invalidnotificationformat|WNS – érvénytelen értesítési formátum|Mennyiség|Összes|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|None|
|kimenő. wns. invalidnotificationsize|A WNS érvénytelen értesítési méretet észlelt|Mennyiség|Összes|Az értesítési tartalom túl nagy (WNS állapot: 413).|None|
|kimenő. wns. channelthrottled|WNS csatorna szabályozása|Mennyiség|Összes|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-NotificationStatus: channelThrottled).|None|
|kimenő. wns. channeldisconnected|WNS csatorna leválasztva|Mennyiség|Összes|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva).|None|
|kimenő. wns. Dropped|WNS eldobott értesítések|Mennyiség|Összes|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|None|
|kimenő. wns. pnserror|WNS hibák|Mennyiség|Összes|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|None|
|kimenő. wns. authenticationerror|WNS-hitelesítési hibák|Mennyiség|Összes|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|None|
|kimenő. apns. sikeres|APNS sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|None|
|kimenő. apns. invalidcredentials|APNS-hitelesítési hibák|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő. apns. badchannel|APNS hibás csatorna hiba|Mennyiség|Összes|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS: 8).|None|
|kimenő. apns. expiredchannel|APNS lejárt csatorna hibája|Mennyiség|Összes|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|None|
|kimenő. apns. invalidnotificationsize|A APNS érvénytelen értesítési méretet észlelt|Mennyiség|Összes|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (APNS-állapotkód: 7).|None|
|kimenő. apns. pnserror|APNS hibák|Mennyiség|Összes|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|None|
|kimenő. GCM. sikeres|GCM sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|None|
|kimenő. GCM. invalidcredentials|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő. GCM. badchannel|GCM hibás csatorna hiba|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM-eredmény: érvénytelen regisztráció).|None|
|kimenő. GCM. expiredchannel|GCM lejárt csatorna hibája|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM-eredmény: NotRegistered).|None|
|kimenő. GCM. szabályozva|GCM-szabályozású értesítések|Mennyiség|Összes|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM: 501-599 vagy eredmény: nem érhető el).|None|
|kimenő. GCM. invalidnotificationformat|GCM – érvénytelen értesítési formátum|Mennyiség|Összes|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|None|
|kimenő. GCM. invalidnotificationsize|A GCM érvénytelen értesítési méretet észlelt|Mennyiség|Összes|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|None|
|kimenő. GCM. wrongchannel|GCM rossz csatorna hibája|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM-eredmény: InvalidPackageName).|None|
|kimenő. GCM. pnserror|GCM hibák|Mennyiség|Összes|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|None|
|kimenő. GCM. authenticationerror|GCM-hitelesítési hibák|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM result: MismatchedSenderId).|None|
|kimenő. mpns. sikeres|MPNS sikeres értesítések|Mennyiség|Összes|Az összes sikeres értesítés száma.|None|
|kimenő. mpns. invalidcredentials|MPNS – érvénytelen hitelesítő adatok|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő. mpns. badchannel|MPNS hibás csatorna hiba|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|None|
|kimenő. mpns. szabályozva|MPNS-szabályozású értesítések|Mennyiség|Összes|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem fogadható el).|None|
|kimenő. mpns. invalidnotificationformat|MPNS – érvénytelen értesítési formátum|Mennyiség|Összes|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|None|
|kimenő. mpns. channeldisconnected|MPNS csatorna leválasztva|Mennyiség|Összes|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (MPNS-állapot: 412 nem található).|None|
|kimenő. mpns. Dropped|MPNS eldobott értesítések|Mennyiség|Összes|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X-NotificationStatus: QueueFull vagy letiltva).|None|
|kimenő. mpns. pnserror|MPNS hibák|Mennyiség|Összes|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|None|
|kimenő. mpns. authenticationerror|MPNS-hitelesítési hibák|Mennyiség|Összes|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|notificationhub. leküldések|Minden kimenő értesítés|Mennyiség|Összes|Az értesítési központ összes kimenő értesítése|None|
|bejövő. ALL. requests|Minden bejövő kérelem|Mennyiség|Összes|Értesítési központ összes bejövő kérelme|None|
|bejövő. ALL. failedrequests|Minden bejövő sikertelen kérelem|Mennyiség|Összes|Értesítési központ bejövő sikertelen kéréseinek száma|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_ szabad inode (%)|Szabad inode%-ban|Mennyiség|Átlag|Average_ szabad inode (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Szabad terület százalékos aránya|Mennyiség|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ használt inode|Felhasznált inode%-ban|Mennyiség|Átlag|Average_ használt inode|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált terület%-ban|Felhasznált terület (%)|Mennyiség|Átlag|Average_ felhasznált terület%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási sebesség (bájt/s) Average_Disk|Lemezolvasás sebessége bájt/mp-ben|Mennyiség|Átlag|Olvasási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Lemezolvasások/mp|Mennyiség|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Lemez átvitele másodpercenként|Mennyiség|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írási sebesség (bájt/s) Average_Disk|Lemezírás sebessége bájt/mp-ben|Mennyiség|Átlag|Írási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Lemezírások/mp|Mennyiség|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Szabad terület (MB)|Mennyiség|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Logikai lemez sebessége (bájt/s)|Mennyiség|Átlag|Average_Logical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rendelkezésre álló memória Average_%-ban|Rendelkezésre álló memória%-ban|Mennyiség|Átlag|Rendelkezésre álló memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ rendelkezésre álló szabad hely a lapozófájlban|Rendelkezésre álló swap-terület (%)|Mennyiség|Átlag|Average_ rendelkezésre álló szabad hely a lapozófájlban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Felhasznált memória Average_%-ban|Felhasznált memória (%)|Mennyiség|Átlag|Felhasznált memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált lapozófájl-terület|Felhasznált swap-terület%-ban|Mennyiség|Átlag|Average_ felhasznált lapozófájl-terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB memória|Rendelkezésre álló memória (MB)|Mennyiség|Átlag|Average_Available MB memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban való swap|Rendelkezésre álló memória (MB)|Mennyiség|Átlag|Average_Available MB-ban való swap|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Page másodpercenként|Olvasott lap/mperc|Mennyiség|Átlag|Olvasási Average_Page másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Page|Írt lap/mperc|Mennyiség|Átlag|Írás/mp Average_Page|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pages előállított/másodperc|Mozgatott lapok (lap/sec)|Mennyiség|Átlag|Average_Pages előállított/másodperc|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
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
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Átlagos írási idő (mp/olvasás)|Mennyiség|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. teljesítmény mp/átvitelben|Átlagos műveleti idő (mp/átvitel)|Mennyiség|Átlag|Average_Avg. teljesítmény mp/átvitelben|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Átlagos írási idő (mp/írás)|Mennyiség|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Fizikai lemez sebessége (bájt/s)|Mennyiség|Átlag|Average_Physical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|PCT rendszerjogosultságú idő|Mennyiség|Átlag|Average_Pct emelt szintű idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|PCT felhasználói idő|Mennyiség|Átlag|Average_Pct felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Felhasznált memória (kilobájt)|Mennyiség|Átlag|Average_Used memória kilobájtban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Virtuális megosztott memória|Mennyiség|Átlag|Average_Virtual megosztott memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% DPC idő|% DPC idő|Mennyiség|Átlag|Average_% DPC idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ üresjárati idő%-ban|Üresjáratban eltöltött időhányad (%)|Mennyiség|Átlag|Average_ üresjárati idő%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Megszakítási idő%-ban|Mennyiség|Átlag|Average_%-os megszakítási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% IO várakozási idő|I/o várakozási idő%-ban|Mennyiség|Átlag|Average_% IO várakozási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os szép idő|% Nice idő|Mennyiség|Átlag|Average_%-os szép idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|%-Os privilegizált idő|Mennyiség|Átlag|Average_%-os privilegizált idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|A processzor kihasználtsága (%)|Mennyiség|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Felhasználói idő%-ban|Mennyiség|Átlag|Average_%-os felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fizikai memória Average_Free|Szabad fizikai memória|Mennyiség|Átlag|Fizikai memória Average_Free|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free lemezterület a Lapozófájlokban|Szabad terület a Lapozófájlokban|Mennyiség|Átlag|Average_Free lemezterület a Lapozófájlokban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free virtuális memória|Szabad virtuális memória|Mennyiség|Átlag|Average_Free virtuális memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processes|Folyamatok|Mennyiség|Átlag|Average_Processes|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Lapozófájlokban tárolt méret|Mennyiség|Átlag|Lapozófájlokban tárolt Average_Size|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Uptime|Hasznos üzemidő|Mennyiség|Átlag|Average_Uptime|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Users|Felhasználók|Mennyiség|Átlag|Average_Users|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Átlagos írási idő (mp/olvasás)|Mennyiség|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Átlagos írási idő (mp/írás)|Mennyiség|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Current a lemez várólistájának hossza|Lemez aktuális várólistájának hossza|Mennyiség|Átlag|Average_Current a lemez várólistájának hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Lemezolvasások/mp|Mennyiség|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Lemez átvitele másodpercenként|Mennyiség|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Lemezírások/mp|Mennyiség|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Szabad terület (MB)|Mennyiség|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Szabad terület százalékos aránya|Mennyiség|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban|Rendelkezésre álló memória (MB)|Mennyiség|Átlag|Average_Available MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Előjegyzett memória%-ban használatban|Mennyiség|Átlag|Average_%-os előjegyzett bájtok használatban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Fogadott bájtok/s|Mennyiség|Átlag|Fogadott Average_Bytes/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Küldési Average_Bytes másodpercenként|Küldési sebesség (bájt/s)|Mennyiség|Átlag|Küldési Average_Bytes másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Összes bájt/mp|Mennyiség|Átlag|Average_Bytes összesen/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|A processzor kihasználtsága (%)|Mennyiség|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Processzor-várólista hossza|Mennyiség|Átlag|Average_Processor várólista hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Szívverés|Szívverés|Mennyiség|Összes|Szívverés|Számítógép, OSType, verzió, SourceComputerId|
|Frissítés|Frissítés|Mennyiség|Átlag|Frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|
|Esemény|Esemény|Mennyiség|Átlag|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PrefixLatency|Előtag-késés|Ezredmásodperc|Átlag|Medián előtag késése|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/társaik

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Munkamenet rendelkezésre állása v4|Százalék|Átlag|A v4-munkamenet rendelkezésre állása|connectionId|
|SessionAvailabilityV6|Munkamenet rendelkezésre állása v6|Százalék|Átlag|A V6-munkamenet rendelkezésre állása|connectionId|
|IngressTrafficRate|Bejövő forgalom aránya|BitsPerSecond|Átlag|Bejövő forgalom sebessége bit/másodpercben|connectionId|
|EgressTrafficRate|Kimenő forgalom aránya|BitsPerSecond|Átlag|Kimenő forgalom sebessége (bit/mp)|connectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Lekérdezés időtartama|Ezredmásodperc|Átlag|DAX-lekérdezés időtartama az utolsó intervallumban|Nincsenek méretek|
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet feladatok várólistájának hossza|Mennyiség|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|Nincsenek méretek|
|qpu_high_utilization_metric|QPU magas kihasználtság|Mennyiség|Összes|QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0|Nincsenek méretek|
|memory_metric|Memória|Bájt|Átlag|Memory. 0-3 GB-os tartomány a1, 0-5 GB az a2-hez, 0-10 GB az a3-hoz, 0-25 GB a4-hez, 0-50 GB az A5-hez és az A6-os 0-100 GB-hoz|Nincsenek méretek|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|Nincsenek méretek|



## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Figyelőkapcsolatokra – sikeres|Figyelőkapcsolatokra – sikeres|Mennyiség|Összes|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|EntityName, Operationresult tevékenységen|
|Figyelőkapcsolatokra – vonatkozó ügyfélhibái|Figyelőkapcsolatokra – vonatkozó ügyfélhibái|Mennyiség|Összes|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName, Operationresult tevékenységen|
|Figyelőkapcsolatokra – ServerError|Figyelőkapcsolatokra – ServerError|Mennyiség|Összes|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|EntityName, Operationresult tevékenységen|
|Feladói kapcsolatokra – sikeres|Feladói kapcsolatokra – sikeres|Mennyiség|Összes|A Microsoft. Relay sikeres feladói kapcsolatokra.|EntityName, Operationresult tevékenységen|
|Feladói kapcsolatokra – vonatkozó ügyfélhibái|Feladói kapcsolatokra – vonatkozó ügyfélhibái|Mennyiség|Összes|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName, Operationresult tevékenységen|
|Feladói kapcsolatokra – ServerError|Feladói kapcsolatokra – ServerError|Mennyiség|Összes|A Microsoft. Relay feladói kapcsolatokra ServerError.|EntityName, Operationresult tevékenységen|
|Figyelőkapcsolatokra – TotalRequests|Figyelőkapcsolatokra – TotalRequests|Mennyiség|Összes|A Microsoft. Relay Figyelőkapcsolatokra összesen.|EntityName|
|Feladói kapcsolatokra – TotalRequests|Feladói kapcsolatokra – TotalRequests|Mennyiség|Összes|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|EntityName|
|Aktív kapcsolatai|Aktív kapcsolatai|Mennyiség|Összes|A Microsoft. Relay aktív kapcsolatai összesen.|EntityName|
|ActiveListeners|ActiveListeners|Mennyiség|Összes|A Microsoft. Relay ActiveListeners összesen.|EntityName|
|BytesTransferred|BytesTransferred|Mennyiség|Összes|A Microsoft. Relay BytesTransferred összesen.|EntityName|
|Bontásai|Bontásai|Mennyiség|Összes|A Microsoft. Relay bontásai összesen.|EntityName|
|SenderDisconnects|SenderDisconnects|Mennyiség|Összes|A Microsoft. Relay SenderDisconnects összesen.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|None|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|CountPerSecond|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|None|
|ThrottledSearchQueriesPercentage|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Mennyiség|Összes|Névtér összes sikeres kérelme|EntityName, Operationresult tevékenységen|
|Kiszolgálóhibái|Kiszolgálói hibák.|Mennyiség|Összes|Kiszolgálói hibák a Microsoft. ServiceBus esetében.|EntityName, Operationresult tevékenységen|
|UserErrors|Felhasználói hibák.|Mennyiség|Összes|A Microsoft. ServiceBus felhasználói hibái.|EntityName, Operationresult tevékenységen|
|ThrottledRequests|Szabályozott kérelmek.|Mennyiség|Összes|A Microsoft. ServiceBus által szabályozott kérelmek.|EntityName, Operationresult tevékenységen|
|IncomingRequests|Bejövő kérelmek|Mennyiség|Összes|A Microsoft. ServiceBus bejövő kérései.|EntityName|
|IncomingMessages|Bejövő üzenetek|Mennyiség|Összes|A Microsoft. ServiceBus bejövő üzenetei.|EntityName|
|OutgoingMessages|Kimenő üzenetek|Mennyiség|Összes|A Microsoft. ServiceBus kimenő üzenetei.|EntityName|
|Aktív kapcsolatai|Aktív kapcsolatai|Mennyiség|Összes|A Microsoft. ServiceBus összes aktív kapcsolata.|None|
|ConnectionsOpened|Megnyitott kapcsolatok.|Mennyiség|Átlag|A Microsoft. ServiceBus megnyitott kapcsolatainak száma.|EntityName|
|ConnectionsClosed|A kapcsolatok lezárva.|Mennyiség|Átlag|A Microsoft. ServiceBus lezárt kapcsolatainak száma.|EntityName|
|Méret|Méret|Bájt|Átlag|Várólista/témakör mérete bájtban.|EntityName|
|Üzenetek|Üzenetsor vagy témakör üzeneteinek száma.|Mennyiség|Átlag|Üzenetsor vagy témakör üzeneteinek száma.|EntityName|
|ActiveMessages|Üzenetsor vagy témakör aktív üzeneteinek száma.|Mennyiség|Átlag|Üzenetsor vagy témakör aktív üzeneteinek száma.|EntityName|
|DeadletteredMessages|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|Mennyiség|Átlag|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|EntityName|
|ScheduledMessages|Az üzenetsor/témakör ütemezett üzeneteinek száma.|Mennyiség|Átlag|Az üzenetsor/témakör ütemezett üzeneteinek száma.|EntityName|
|NamespaceCpuUsage|CPU|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája.|Replika|
|NamespaceMemoryUsage|Memóriahasználat|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma.|Replika|
|CPUXNS|CPU (elavult)|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája. Ez a metrika depricated. Használja helyette a CPU-metrikát (NamespaceCpuUsage).|Replika|
|WSXNS|Memóriahasználat (elavult)|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma. Ez a metrika elavult. Használja helyette a memóriahasználat (NamespaceMemoryUsage) metrikáját.|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/alkalmazások

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Mennyiség|Átlag|A tárolóhoz a Millet magokban lefoglalt CPU|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bájt|Átlag|A tároló számára lefoglalt memória (MB)|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Mennyiség|Átlag|Tényleges CPU-használat a Millon magokban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Mennyiség|Átlag|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|ServiceStatus|ServiceStatus|Mennyiség|Átlag|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, állapot, szolgáltatásnév|
|ServiceReplicaStatus|ServiceReplicaStatus|Mennyiség|Átlag|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, status, szolgáltatásnév, ServiceReplicaName|
|Tároló állapota:|Tároló állapota:|Mennyiség|Átlag|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName, állapot|
|RestartCount|RestartCount|Mennyiség|Átlag|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, status, szolgáltatásnév, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ConnectionCount|Kapcsolatok száma|Mennyiség|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|MessageCount|Üzenetek száma|Mennyiség|Összes|Az üzenetek teljes mennyisége.|None|
|InboundTraffic|Bejövő forgalom|Bájt|Összes|A szolgáltatás bejövő forgalma|None|
|OutboundTraffic|Kimenő forgalom|Bájt|Összes|A szolgáltatás kimenő forgalma|None|
|UserErrors|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|None|
|SystemErrors|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|None|





## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|None|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|None|
|log_write_percent|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|None|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|None|
|tárterület|Felhasznált adatterület|Bájt|Maximum|Felhasznált adatterület. Az adattárházak esetében nem alkalmazható.|None|
|connection_successful|Sikeres kapcsolatok|Mennyiség|Összes|Sikeres kapcsolatok|None|
|connection_failed|Sikertelen kapcsolatok|Mennyiség|Összes|Sikertelen kapcsolatok|None|
|blocked_by_firewall|Tűzfal blokkolja|Mennyiség|Összes|Tűzfal blokkolja|None|
|holtpont|Holtpontok|Mennyiség|Összes|Holtpontok. Az adattárházak esetében nem alkalmazható.|None|
|storage_percent|Felhasznált adatterület százalékos aránya|Százalék|Maximum|Az adatterület felhasznált százaléka százalékban. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|None|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP-tárolási százalék. Az adattárházak esetében nem alkalmazható.|None|
|workers_percent|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|None|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|None|
|dtu_limit|DTU korlátja|Mennyiség|Átlag|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|None|
|dtu_used|Használt DTU|Mennyiség|Átlag|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|None|
|cpu_limit|CPU-korlát|Mennyiség|Átlag|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|None|
|cpu_used|Felhasznált CPU|Mennyiség|Átlag|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|None|
|dwu_limit|DWU korlátja|Mennyiség|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|None|
|dwu_consumption_percent|DWU százalékos aránya|Százalék|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|None|
|dwu_used|Használt DWU|Mennyiség|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|None|
|cache_hit_percent|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|None|
|cache_used_percent|Gyorsítótár használt százaléka|Százalék|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|None|
|sqlserver_process_core_percent|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|None|
|sqlserver_process_memory_percent|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|None|
|tempdb_data_size|Tempdb adatfájl mérete (kilobájt)|Mennyiség|Maximum|Tempdb az adatfájl mérete kilobájtban. Az adattárházak esetében nem alkalmazható.|None|
|tempdb_log_size|Tempdb-naplófájl mérete (kilobájt)|Mennyiség|Maximum|Tempdb-naplófájl mérete (kilobájt) Az adattárházak esetében nem alkalmazható.|None|
|tempdb_log_used_percent|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van. Az adattárházak esetében nem alkalmazható.|None|
|local_tempdb_usage_percent|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|None|
|app_cpu_billed|Az alkalmazás CPU-számlázása|Mennyiség|Összes|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|app_cpu_percent|Alkalmazás CPU-aránya|Százalék|Átlag|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|app_memory_percent|Az alkalmazás memóriájának százaléka|Százalék|Átlag|Az alkalmazás memóriájának százalékos aránya. A kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adattárolás. Az adattárházak esetében nem alkalmazható.|None|
|memory_usage_percent|Memória százaléka|Százalék|Maximum|Memória százalékos aránya Csak az adattárházak esetében érvényes.|None|
|full_backup_size_bytes|Teljes biztonsági mentési tár mérete|Bájt|Maximum|Összesített teljes biztonsági mentési tár mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|None|
|diff_backup_size_bytes|Különbözeti biztonsági másolatok tárolási mérete|Bájt|Maximum|Kumulatív különbözeti biztonsági másolati tárterület mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|None|
|log_backup_size_bytes|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő napló biztonsági mentési tárterületének mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|None|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/kiszolgálók/elasticPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|None|
|database_cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|DatabaseResourceId|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|None|
|database_physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|DatabaseResourceId|
|log_write_percent|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|None|
|database_log_write_percent|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|DatabaseResourceId|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|None|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId|
|storage_percent|Felhasznált adatterület százalékos aránya|Százalék|Átlag|Felhasznált adatterület százalékos aránya|None|
|workers_percent|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|None|
|database_workers_percent|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|DatabaseResourceId|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|None|
|database_sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|DatabaseResourceId|
|eDTU_limit|eDTU korlátja|Mennyiség|Átlag|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|None|
|storage_limit|Adatok maximális mérete|Bájt|Átlag|Adatok maximális mérete|None|
|eDTU_used|használt eDTU|Mennyiség|Átlag|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|None|
|database_eDTU_used|használt eDTU|Mennyiség|Átlag|használt eDTU|DatabaseResourceId|
|storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|None|
|database_storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP tárolási százaléka|None|
|cpu_limit|CPU-korlát|Mennyiség|Átlag|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|None|
|database_cpu_limit|CPU-korlát|Mennyiség|Átlag|CPU-korlát|DatabaseResourceId|
|cpu_used|Felhasznált CPU|Mennyiség|Átlag|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|None|
|database_cpu_used|Felhasznált CPU|Mennyiség|Átlag|Felhasznált CPU|DatabaseResourceId|
|sqlserver_process_core_percent|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|None|
|sqlserver_process_memory_percent|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|None|
|tempdb_data_size|Tempdb adatfájl mérete (kilobájt)|Mennyiség|Maximum|Tempdb adatfájl mérete (kilobájt)|None|
|tempdb_log_size|Tempdb-naplófájl mérete (kilobájt)|Mennyiség|Maximum|Tempdb-naplófájl mérete (kilobájt)|None|
|tempdb_log_used_percent|Tempdb százalékos naplója használatban|Százalék|Maximum|Tempdb százalékos naplója használatban|None|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|None|
|database_allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|DatabaseResourceId|
|allocated_data_storage_percent|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|None|

## <a name="microsoftsqlservers"></a>Microsoft. SQL/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|ElasticPoolResourceId|
|database_storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Használt DTU|Mennyiség|Átlag|Használt DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|virtual_core_count|Virtuális mag száma|Mennyiség|Átlag|Virtuális mag száma|None|
|avg_cpu_percent|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|None|
|reserved_storage_mb|Tárterület fenntartva|Mennyiség|Átlag|Tárterület fenntartva|None|
|storage_space_used_mb|Felhasznált tárterület|Mennyiség|Átlag|Felhasznált tárterület|None|
|io_requests|IO-kérelmek száma|Mennyiség|Átlag|IO-kérelmek száma|None|
|io_bytes_read|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|None|
|io_bytes_written|I/o-bájtok írása|Bájt|Átlag|I/o-bájtok írása|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Felhasznált kapacitás|Bájt|Átlag|Fiókhoz használt kapacitás|None|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|Blob-kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Blobok száma|Mennyiség|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|Blobtárolók száma|Mennyiség|Átlag|A Storage-fiók Blob service lévő tárolók száma.|None|
|IndexCapacity|Indexkapacitás|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|None|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|None|
|TableCount|Táblák száma|Mennyiség|Átlag|A Storage-fiók Table service található tábla száma.|None|
|TableEntityCount|Tábla entitások száma|Mennyiség|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|None|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileCount|Fájlok száma|Mennyiség|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|Fájlmegosztás száma|Mennyiség|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|None|
|FileShareSnapshotCount|Fájlmegosztás pillanatképének száma|Mennyiség|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileShareQuota|Fájlmegosztás kvótájának mérete|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|None|
|QueueCount|Várólista száma|Mennyiség|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|None|
|QueueMessageCount|Üzenetsor-üzenetek száma|Mennyiség|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|None|
|Tranzakciók|Tranzakciók|Mennyiség|Összes|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Belépő|Belépő|Bájt|Összes|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összes|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikerességi kiszolgáló késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres E2E késés|Ezredmásodperc|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Elérhetőség|Elérhetőség|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/gyorsítótárak

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ClientIOPS|Ügyfél teljes IOPS|Mennyiség|Átlag|A gyorsítótár által feldolgozott ügyféloldali fájlok műveleteinek aránya.|None|
|ClientLatency|Ügyfél átlagos késése|Ezredmásodperc|Átlag|Az ügyfél-fájl műveleteinek átlagos késése a tárolási gyorsítótárban.|None|
|ClientReadIOPS|Ügyfél olvasási IOPS|CountPerSecond|Átlag|Az ügyfél olvasási műveletei másodpercenként.|None|
|ClientReadThroughput|Gyorsítótár átlagos olvasási átviteli sebessége|BytesPerSecond|Átlag|Az ügyfél olvasási adatátviteli sebessége.|None|
|ClientWriteIOPS|Ügyfél írási IOPS|CountPerSecond|Átlag|Az ügyfél írási műveletei másodpercenként.|None|
|ClientWriteThroughput|Gyorsítótár átlagos írási sebessége|BytesPerSecond|Átlag|Az ügyfél írási adatátviteli sebessége.|None|
|ClientMetadataReadIOPS|Ügyfél metaadatainak olvasása IOPS|CountPerSecond|Átlag|Az adatolvasások kivételével a gyorsítótárba elküldett ügyféloldali műveletek sebessége, amely nem módosítja az állandó állapotot.|None|
|ClientMetadataWriteIOPS|Ügyfél metaadatainak írási IOPS|CountPerSecond|Átlag|A gyorsítótárba küldendő, az adatírásokat nem tartalmazó, az állandó állapotot módosító ügyfél-fájl műveleteinek aránya.|None|
|ClientLockIOPS|Ügyfél-zárolási IOPS|CountPerSecond|Átlag|Az ügyfél fájljának zárolási műveletei másodpercenként.|None|
|StorageTargetHealth|Tárolási cél állapota|Mennyiség|Átlag|A kapcsolódási teszt logikai eredményei a gyorsítótár és a tárolási célok között.|None|
|Hasznos üzemidő|Hasznos üzemidő|Mennyiség|Átlag|A kapcsolódási teszt logikai eredményei a gyorsítótár és a figyelési rendszer között.|None|
|StorageTargetIOPS|Összes StorageTarget-IOPS|Mennyiség|Átlag|Az összes fájl műveletének sebessége, amelyet a gyorsítótár küld egy adott StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget írási IOPS|Mennyiség|Átlag|A fájl írási műveleteinek sebessége, amelyet a gyorsítótár egy adott StorageTarget küld.|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget aszinkron írási átviteli sebesség|BytesPerSecond|Átlag|A gyorsítótárnak az adatokat egy adott StorageTarget való aszinkron módon írási sebessége. Ezek olyan alkalmi írások, amelyek nem okozzák az ügyfelek blokkolását.|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget szinkron írási sebessége|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár szinkron módon ír adatokat egy adott StorageTarget. Ezek az írások, amelyek az ügyfelek blokkolását okozzák.|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget összes írási sebessége|BytesPerSecond|Átlag|Az a teljes sebesség, ameddig a gyorsítótár adatokat ír egy adott StorageTarget.|StorageTarget|
|StorageTargetLatency|StorageTarget késés|Ezredmásodperc|Átlag|A gyorsítótár által a partricular-StorageTarget küldött összes fájl műveletének átlagos ciklikus késése.|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget-metaadatok IOPS olvasása|CountPerSecond|Átlag|A nem állandó állapotot módosító, valamint az olvasási művelet kihagyása után a gyorsítótár egy adott StorageTarget küldi az adatokat.|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget-metaadatok írási IOPS|CountPerSecond|Átlag|Az állandó állapotot módosító és az írási műveletet nem tartalmazó, a gyorsítótár által egy adott StorageTarget küldött fájl-műveletek aránya.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget olvasási IOPS|CountPerSecond|Átlag|A fájl olvasási műveleteinek sebessége, amelyet a gyorsítótár egy adott StorageTarget küld.|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget – olvasási sebesség|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár a StorageTarget beolvassa az adatokat.|StorageTarget|
|StorageTargetFillThroughput|StorageTarget-kitöltési sebesség|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár beolvassa az adatokat a StorageTarget a gyorsítótár-kihagyás kezelésére.|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget összesen olvasási átviteli sebesség|BytesPerSecond|Átlag|Az a teljes sebesség, ameddig a gyorsítótár adatokat olvas egy adott StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Szinkronizálási munkamenet eredménye|Mennyiség|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Szinkronizált fájlok|Mennyiség|Összes|Szinkronizált fájlok száma|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Mennyiség|Összes|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összes|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Kiszolgáló online állapota|Mennyiség|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Felhőbeli rétegek felidézése|Bájt|Összes|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Felhőbeli rétegek felidézésének mérete|Bájt|Összes|Visszahívott adatmennyiség|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Felhőbeli rétegek felidézésének átviteli sebessége|BytesPerSecond|Átlag|Adatvisszahívási sebesség mérete|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Felhőbeli rétegek felidézésének mérete alkalmazás szerint|Bájt|Összes|Az alkalmazás által visszahívott adatmennyiség|SyncGroupName, kiszolgálónév, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Szinkronizált fájlok|Mennyiség|Összes|Szinkronizált fájlok száma|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Mennyiség|Összes|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összes|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Szinkronizált fájlok|Mennyiség|Összes|Szinkronizált fájlok száma|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Mennyiség|Összes|Nem sikerült szinkronizálni a fájlok számát|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összes|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerHeartbeat|Kiszolgáló online állapota|Mennyiség|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Felhőbeli rétegek felidézése|Bájt|Összes|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU% kihasználtsága|Százalék|Maximum|SU% kihasználtsága|LogicalName, PartitionId|
|InputEvents|Bemeneti események|Mennyiség|Összes|Bemeneti események|LogicalName, PartitionId|
|InputEventBytes|Bemeneti eseménybájtok|Bájt|Összes|Bemeneti eseménybájtok|LogicalName, PartitionId|
|LateInputEvents|Késői bemeneti események|Mennyiség|Összes|Késői bemeneti események|LogicalName, PartitionId|
|OutputEvents|Kimeneti események|Mennyiség|Összes|Kimeneti események|LogicalName, PartitionId|
|ConversionErrors|Adatátalakítási hibák|Mennyiség|Összes|Adatátalakítási hibák|LogicalName, PartitionId|
|Hibák|Futásidejű hibák|Mennyiség|Összes|Futásidejű hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Megrendelési események|Mennyiség|Összes|Megrendelési események|LogicalName, PartitionId|
|AMLCalloutRequests|Függvénykérések|Mennyiség|Összes|Függvénykérések|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Sikertelen függvénykérések|Mennyiség|Összes|Sikertelen függvénykérések|LogicalName, PartitionId|
|AMLCalloutInputEvents|Függvényesemények|Mennyiség|Összes|Függvényesemények|LogicalName, PartitionId|
|DeserializationError|Bemenet-deszerializálási hibák|Mennyiség|Összes|Bemenet-deszerializálási hibák|LogicalName, PartitionId|
|EarlyInputEvents|Korai bemeneti események|Mennyiség|Összes|Korai bemeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Vízjel késleltetése|másodperc|Maximum|Vízjel késleltetése|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Várakozó bemeneti események|Mennyiség|Maximum|Várakozó bemeneti események|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Fogadott bemeneti források|Mennyiség|Összes|Fogadott bemeneti források|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Mennyiség|Összes|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|None|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Mennyiség|Összes|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|None|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összes|Az összes eseményforrás által olvasott bájtok száma|None|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összes|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|None|
|IngressStoredEvents|Bejövő tárolt események|Mennyiség|Összes|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|None|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|None|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Mennyiség|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|None|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Mennyiség|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|None|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Mennyiség|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|None|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Mennyiség|Összes|Az eseményforrás által olvasott üzenetek száma|None|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Mennyiség|Összes|Az eseményforrás által beolvasott érvénytelen üzenetek száma|None|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összes|Az eseményforrás által beolvasott bájtok száma|None|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összes|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|None|
|IngressStoredEvents|Bejövő tárolt események|Mennyiség|Összes|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|None|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|None|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Mennyiség|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|None|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Mennyiség|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|None|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Mennyiség|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|None|
|DiskWriteBytesPerSecond|Lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|None|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összes|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|None|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összes|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|None|
|DiskReadOperations|Lemezes olvasási műveletek|Mennyiség|Összes|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|DiskWriteOperations|Lemez írási műveletei|Mennyiség|Összes|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|DiskReadLatency|Lemez olvasási késése|Ezredmásodperc|Átlag|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|None|
|DiskWriteLatency|Lemez írási késése|Ezredmásodperc|Átlag|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|None|
|NetworkInBytesPerSecond|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli sebessége.|None|
|NetworkOutBytesPerSecond|Hálózati kimenő bájtok/s|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli sebessége.|None|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összes|A fogadott forgalom összes hálózati átviteli sebessége.|None|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összes|A továbbított forgalom összes hálózati átviteli sebessége.|None|
|MemoryUsed|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt gépi memória mennyisége.|None|
|MemoryGranted|Megadott memória|Bájt|Átlag|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|None|
|MemoryActive|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|None|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|None|
|PercentageCpuReady|CPU-készültség százalékos aránya|Ezredmásodperc|Összes|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|None|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvezérlő-várólista hossza|Mennyiség|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Http-várólista hossza|Mennyiség|Átlag|Http-várólista hossza|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|TcpSynSent|Eljuttatott TCP SYN|Mennyiség|Átlag|Eljuttatott TCP SYN|Példány|
|TcpSynReceived|TCP SYN kapott|Mennyiség|Átlag|TCP SYN kapott|Példány|
|TcpEstablished|A TCP létrejött|Mennyiség|Átlag|A TCP létrejött|Példány|
|TcpFinWait1|TCP fin WAIT 1|Mennyiség|Átlag|TCP fin WAIT 1|Példány|
|TcpFinWait2|TCP fin WAIT 2|Mennyiség|Átlag|TCP fin WAIT 2|Példány|
|TcpClosing|TCP-zárás|Mennyiség|Átlag|TCP-zárás|Példány|
|TcpCloseWait|TCP-bezárási várakozás|Mennyiség|Átlag|TCP-bezárási várakozás|Példány|
|TcpLastAck|TCP Last ACK|Mennyiség|Átlag|TCP Last ACK|Példány|
|TcpTimeWait|TCP-idő várakozása|Mennyiség|Átlag|TCP-idő várakozása|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (a függvények kivételével)

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összes|CPU-idő|Példány|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|Http 101|Mennyiség|Összes|Http 101|Példány|
|Http2xx|Http-2xx|Mennyiség|Összes|Http-2xx|Példány|
|Http3xx|Http-3xx|Mennyiség|Összes|Http-3xx|Példány|
|Http401|HTTP 401|Mennyiség|Összes|HTTP 401|Példány|
|Http403|Http 403|Mennyiség|Összes|Http 403|Példány|
|Http404|Http 404|Mennyiség|Összes|Http 404|Példány|
|Http406|Http 406|Mennyiség|Összes|Http 406|Példány|
|Http4xx|Http-4xx|Mennyiség|Összes|Http-4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Mennyiség|Összes|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|A memória-munkakészlet|Bájt|Átlag|A memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|AppConnections|Connections (Kapcsolatok)|Mennyiség|Átlag|Connections (Kapcsolatok)|Példány|
|Kezeli|Leírók száma|Mennyiség|Átlag|Leírók száma|Példány|
|Témák|Szálak száma|Mennyiség|Átlag|Szálak száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Átlag|Saját [nem megosztható] memória (bájt)|Példány|
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
|HealthCheckStatus|Állapot-ellenőrzési állapot|Mennyiség|Átlag|Állapot-ellenőrzési állapot|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (functions)

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http5xx|Http-kiszolgálói hibák|Mennyiség|Összes|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|A memória-munkakészlet|Bájt|Átlag|A memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|FunctionExecutionUnits|Függvények végrehajtási egységei|MB/ezredmásodperc|Összes|[Függvények végrehajtási egységei](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|FunctionExecutionCount|Függvény végrehajtásának száma|Mennyiség|Összes|Függvény végrehajtásának száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Átlag|Saját [nem megosztható] memória (bájt)|Példány|
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
|HealthCheckStatus|Állapot-ellenőrzési állapot|Mennyiség|Átlag|Állapot-ellenőrzési állapot|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|másodperc|Összes|CPU-idő|Példány|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
|Http101|Http 101|Mennyiség|Összes|Http 101|Példány|
|Http2xx|Http-2xx|Mennyiség|Összes|Http-2xx|Példány|
|Http3xx|Http-3xx|Mennyiség|Összes|Http-3xx|Példány|
|Http401|HTTP 401|Mennyiség|Összes|HTTP 401|Példány|
|Http403|Http 403|Mennyiség|Összes|Http 403|Példány|
|Http404|Http 404|Mennyiség|Összes|Http 404|Példány|
|Http406|Http 406|Mennyiség|Összes|Http 406|Példány|
|Http4xx|Http-4xx|Mennyiség|Összes|Http-4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Mennyiség|Összes|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|A memória-munkakészlet|Bájt|Átlag|A memória-munkakészlet|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Átlagos válaszidő|másodperc|Átlag|Átlagos válaszidő|Példány|
|HttpResponseTime|Válaszidő|másodperc|Átlag|Válaszidő|Példány|
|FunctionExecutionUnits|Függvények végrehajtási egységei|Mennyiség|Összes|Függvények végrehajtási egységei|Példány|
|FunctionExecutionCount|Függvény végrehajtásának száma|Mennyiség|Összes|Függvény végrehajtásának száma|Példány|
|AppConnections|Connections (Kapcsolatok)|Mennyiség|Átlag|Connections (Kapcsolatok)|Példány|
|Kezeli|Leírók száma|Mennyiség|Átlag|Leírók száma|Példány|
|Témák|Szálak száma|Mennyiség|Átlag|Szálak száma|Példány|
|PrivateBytes|Saját [nem megosztható] memória (bájt)|Bájt|Átlag|Saját [nem megosztható] memória (bájt)|Példány|
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
|HealthCheckStatus|Állapot-ellenőrzési állapot|Mennyiség|Átlag|Állapot-ellenőrzési állapot|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Mennyiség|Összes|Kérelmek|Példány|
|BytesReceived|Bejövő adatforgalom|Bájt|Összes|Bejövő adatforgalom|Példány|
|BytesSent|Kimenő adatforgalom|Bájt|Összes|Kimenő adatforgalom|Példány|
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
|TotalFrontEnds|Összes előtér|Mennyiség|Átlag|Összes előtér|None|
|SmallAppServicePlanInstances|Kisméretű App Service-csomag feldolgozói|Mennyiség|Átlag|Kisméretű App Service-csomag feldolgozói|None|
|MediumAppServicePlanInstances|Közepes App Service-csomag feldolgozói|Mennyiség|Átlag|Közepes App Service-csomag feldolgozói|None|
|LargeAppServicePlanInstances|Nagy App Service-csomag feldolgozói|Mennyiség|Átlag|Nagy App Service-csomag feldolgozói|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WorkersTotal|Alkalmazottak összesen|Mennyiség|Átlag|Alkalmazottak összesen|None|
|WorkersAvailable|Rendelkezésre álló munkavégzők|Mennyiség|Átlag|Rendelkezésre álló munkavégzők|None|
|WorkersUsed|Használt feldolgozók|Mennyiség|Átlag|Használt feldolgozók|None|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
## <a name="next-steps"></a>Következő lépések
* [Tudnivalók a Azure Monitor mérőszámokról](data-platform.md)
* [Riasztások létrehozása mérőszámokon](alerts-overview.md)
* [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](platform-logs-overview.md)
