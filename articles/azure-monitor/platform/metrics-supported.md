---
title: Azure Monitor támogatott metrikák erőforrástípus szerint
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: f2e3c03ba599128cc4552f64637ebd63efcb4578
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128450"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák Azure Monitor

> [!NOTE]
> A lista nagy mértékben automatikusan létrejön a Azure Monitor metrikák REST API. A listán a githubon keresztül végrehajtott módosítások figyelmeztetés nélkül is megírhatók. A végleges frissítések végrehajtásával kapcsolatban a cikk szerzője nyújt tájékoztatást.

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. 

Ez a cikk a Azure Monitor konszolidált metrikus folyamatával jelenleg elérhető összes platform (azaz automatikusan összegyűjtött) mérőszámok teljes listáját tartalmazza. A lista 2020. március 27-én Utoljára frissítve. Az ezen dátum után módosított vagy hozzáadott mérőszámok nem jelennek meg alább. A metrikák listájának programozott módon történő lekérdezéséhez és eléréséhez használja a [2018-01-01 API-Version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

Más mérőszámok a portálon vagy a régi API-k használatával is elérhetők. Az Azure Virtual Machines, Service Fabric és Cloud Services operációs rendszert futtató vendég operációs rendszer mérőszámai itt **nem** szerepelnek. Ezeket egy vagy több olyan ügynökön keresztül kell gyűjteni, amely az operációs rendszer részeként vagy rendszeren fut. Az ügynök metrikáit a platform metrikai adatbázisába is elküldheti az [Egyéni metrikák](metrics-custom-overview.md) API használatával, amely jelenleg nyilvános előzetes verzióban érhető el. Ezután diagramot, riasztást és egyéb módon használhatja a vendég operációs rendszer metrikáit, például a platform metrikáit. További információ: [monitoring Agents – áttekintés](agents-overview.md).    

A metrikákat a névtér rendezi. A szolgáltatások és a hozzájuk tartozó névterek listáját lásd: [erőforrás-szolgáltatók az Azure-szolgáltatásokhoz](../../azure-resource-manager/management/azure-services-resource-providers.md). 

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
> A diagnosztikai beállítások használatával exportálható platform-metrikák listáját [ebben a cikkben](metrics-supported-export-diagnostic-settings.md)találja.


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Darabszám|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|memory_metric|Memory (Memória)|Bájt|Átlag|Memory. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|private_bytes_metric|Saját bájtok|Bájt|Átlag|Saját bájtok.|ServerResourceType|
|virtual_bytes_metric|Felhasznált virtuális memória jelenlegi mérete (bájt)|Bájt|Átlag|Virtuális bájtok.|ServerResourceType|
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
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet – feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak: rövid elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Szálak: rövid elemzési üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak: rövid elemzési feladatok várólistájának hossza|Darabszám|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|ServerResourceType|
|mashup_engine_qpu_metric|M motor QPU|Darabszám|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor saját bájtjai|Bájt|Átlag|Saját bájtok használata az adategyesítési motor folyamatai alapján.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M motor virtuális bájtjai|Bájt|Átlag|Az adategyesítési motor folyamatai által használt virtuális bájtok használata.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Összes átjáró kérelme (elavult)|Darabszám|Összesen|Átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|SuccessfulRequests|Sikeres átjáró-kérelmek (elavult)|Darabszám|Összesen|Sikeres átjáró-kérések száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzió helyett|Hely, állomásnév|
|UnauthorizedRequests|Nem engedélyezett átjáró-kérelmek (elavult)|Darabszám|Összesen|Jogosulatlan átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|FailedRequests|Sikertelen átjáró-kérelmek (elavult)|Darabszám|Összesen|Hibák száma az átjáró kéréseiben – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|OtherRequests|Egyéb átjáró-kérelmek (elavult)|Darabszám|Összesen|Egyéb átjáró-kérelmek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|Időtartam|Az átjárók kéréseinek teljes időtartama|Ezredmásodpercben|Átlag|Az átjáró kérelmek teljes időtartama ezredmásodpercben|Hely, állomásnév|
|BackendDuration|A háttérbeli kérelmek időtartama|Ezredmásodpercben|Átlag|A háttérbeli kérelmek időtartama (ezredmásodpercben)|Hely, állomásnév|
|Kapacitás|Kapacitás|Százalék|Átlag|A ApiManagement szolgáltatás kihasználtsági metrikája|Hely|
|EventHubTotalEvents|Összes EventHub esemény|Darabszám|Összesen|A EventHub eljuttatott események száma|Hely|
|EventHubSuccessfulEvents|Sikeres EventHub események|Darabszám|Összesen|Sikeres EventHub események száma|Hely|
|EventHubTotalFailedEvents|Sikertelen EventHub események|Darabszám|Összesen|Sikertelen EventHub események száma|Hely|
|EventHubRejectedEvents|EventHub-események visszautasítva|Darabszám|Összesen|Visszautasított EventHub-események száma (helytelen konfiguráció vagy jogosulatlan)|Hely|
|EventHubThrottledEvents|Szabályozott EventHub események|Darabszám|Összesen|A szabályozott EventHub események száma|Hely|
|EventHubTimedoutEvents|Időtúllépés a EventHub eseményeinél|Darabszám|Összesen|Az időtúllépés miatti EventHub események száma|Hely|
|EventHubDroppedEvents|Eldobott EventHub események|Darabszám|Összesen|A várólista-méretkorlát elérte miatt kihagyott események száma|Hely|
|EventHubTotalBytesSent|EventHub-események mérete|Bájt|Összesen|EventHub-események teljes mérete bájtban|Hely|
|Kérelmek|Kérelmek|Darabszám|Összesen|Több dimenzióval rendelkező átjáró-kérelmek metrikái|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|NetworkConnectivity|Erőforrások hálózati kapcsolati állapota (előzetes verzió)|Darabszám|Összesen|A API Management szolgáltatástól függő erőforrástípusok hálózati kapcsolati állapota|Hely, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Darabszám|Darabszám|A bejövő HTTP-kérelmek teljes száma.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Darabszám|Átlag|Egy HTTP-kérelem késése.|StatusCode|


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
|GCPauseTotalCount|GC-szüneteltetések száma|Darabszám|Összesen|GC-szüneteltetések száma|AppName, Pod|
|GCPauseTotalTime|GC felfüggesztésének teljes ideje|Ezredmásodpercben|Összesen|GC felfüggesztésének teljes ideje|AppName, Pod|
|TomcatSentBytes|Tomcat összesen eljuttatott bájtok száma|Bájt|Összesen|Tomcat összesen eljuttatott bájtok száma|AppName, Pod|
|TomcatReceivedBytes|Tomcat összesen fogadott bájtok|Bájt|Összesen|Tomcat összesen fogadott bájtok|AppName, Pod|
|TomcatRequestTotalTime|Tomcat-kérelem teljes ideje|Ezredmásodpercben|Összesen|Tomcat-kérelem teljes ideje|AppName, Pod|
|TomcatRequestTotalCount|Tomcat-kérelem összesített száma|Darabszám|Összesen|Tomcat-kérelem összesített száma|AppName, Pod|
|TomcatResponseAvgTime|Tomcat-kérelem átlagos ideje|Ezredmásodpercben|Átlag|Tomcat-kérelem átlagos ideje|AppName, Pod|
|TomcatRequestMaxTime|Tomcat-kérelem maximális ideje|Ezredmásodpercben|Maximum|Tomcat-kérelem maximális ideje|AppName, Pod|
|TomcatErrorCount|Tomcat globális hiba|Darabszám|Összesen|Tomcat globális hiba|AppName, Pod|
|TomcatSessionActiveMaxCount|Tomcat-munkamenet maximális aktív száma|Darabszám|Összesen|Tomcat-munkamenet maximális aktív száma|AppName, Pod|
|TomcatSessionAliveMaxTime|Tomcat-munkamenet maximális élettartama|Ezredmásodpercben|Maximum|Tomcat-munkamenet maximális élettartama|AppName, Pod|
|TomcatSessionActiveCurrentCount|Tomcat-munkamenet élő száma|Darabszám|Összesen|Tomcat-munkamenet élő száma|AppName, Pod|
|TomcatSessionCreatedCount|Tomcat-munkamenet létrehozva szám|Darabszám|Összesen|Tomcat-munkamenet létrehozva szám|AppName, Pod|
|TomcatSessionExpiredCount|A Tomcat-munkamenet lejárt|Darabszám|Összesen|A Tomcat-munkamenet lejárt|AppName, Pod|
|TomcatSessionRejectedCount|A Tomcat-munkamenet elutasította a darabszámot|Darabszám|Összesen|A Tomcat-munkamenet elutasította a darabszámot|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalJob|Feladatok összesen|Darabszám|Összesen|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentRuns|Összes frissítés központi telepítési futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, állapot|
|TotalUpdateDeploymentMachineRuns|Összes frissítés központi telepítési gép futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft. batch/batchAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount|Dedikált mag száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált magok teljes száma|None|
|TotalNodeCount|Dedikált csomópontok száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált csomópontok teljes száma|None|
|LowPriorityCoreCount|LowPriority mag száma|Darabszám|Összesen|Az alacsony prioritású magok teljes száma a Batch-fiókban|None|
|TotalLowPriorityNodeCount|Alacsony prioritású csomópontok száma|Darabszám|Összesen|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|None|
|CreatingNodeCount|Csomópontok számának létrehozása|Darabszám|Összesen|Létrehozandó csomópontok száma|None|
|StartingNodeCount|Csomópontok számának indítása|Darabszám|Összesen|Kiinduló csomópontok száma|None|
|WaitingForStartTaskNodeCount|Várakozás a feladat-csomópontok számának megkezdésére|Darabszám|Összesen|Az indítási tevékenység befejezésére váró csomópontok száma|None|
|StartTaskFailedNodeCount|Indítási feladat sikertelen csomópontok száma|Darabszám|Összesen|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|None|
|IdleNodeCount|Tétlen csomópontok száma|Darabszám|Összesen|Üresjárati csomópontok száma|None|
|OfflineNodeCount|Offline csomópontok száma|Darabszám|Összesen|Offline csomópontok száma|None|
|RebootingNodeCount|Csomópontok számának újraindítása|Darabszám|Összesen|Újraindítási csomópontok száma|None|
|ReimagingNodeCount|Csomópontok rendszerképének alaphelyzetbe állítása|Darabszám|Összesen|Rendszerkép-csomópontok száma|None|
|RunningNodeCount|Csomópontok száma|Darabszám|Összesen|Futó csomópontok száma|None|
|LeavingPoolNodeCount|A készlet-csomópontok számának elhagyása|Darabszám|Összesen|A készletet elhagyó csomópontok száma|None|
|UnusableNodeCount|Nem használható csomópontok száma|Darabszám|Összesen|Használhatatlan csomópontok száma|None|
|PreemptedNodeCount|Előzik-csomópontok száma|Darabszám|Összesen|Előzik-csomópontok száma|None|
|TaskStartEvent|Tevékenységek indítási eseményei|Darabszám|Összesen|Az elindított feladatok teljes száma|poolId, jobId|
|TaskCompleteEvent|Feladat teljes eseményei|Darabszám|Összesen|A Befejezett feladatok teljes száma|poolId, jobId|
|TaskFailEvent|Feladat sikertelen eseményei|Darabszám|Összesen|A meghiúsult állapotban Befejezett feladatok teljes száma|poolId, jobId|
|PoolCreateEvent|Készlet-létrehozási események|Darabszám|Összesen|A létrehozott készletek teljes száma|poolId|
|PoolResizeStartEvent|Készlet átméretezésének indítási eseményei|Darabszám|Összesen|A készlet elindított újraméretezésének teljes száma|poolId|
|PoolResizeCompleteEvent|Készlet átméretezése – befejezett események|Darabszám|Összesen|A készlet összes újraméretezésének száma|poolId|
|PoolDeleteStartEvent|Készlet törlése – indítási események|Darabszám|Összesen|Az elindított készlet-törlések száma összesen|poolId|
|PoolDeleteCompleteEvent|Készlet törlése – befejezett események|Darabszám|Összesen|A befejezett készlet-törlések teljes száma|poolId|
|JobDeleteCompleteEvent|Feladatok törlése – befejezett események|Darabszám|Összesen|A sikeresen törölt feladatok teljes száma.|jobId|
|JobDeleteStartEvent|Feladatok törlésének indítási eseményei|Darabszám|Összesen|A törölni kívánt feladatok teljes száma.|jobId|
|JobDisableCompleteEvent|A feladatok letiltják a teljes eseményeket|Darabszám|Összesen|A sikeresen letiltott feladatok teljes száma.|jobId|
|JobDisableStartEvent|Feladatok letiltásának indítási eseményei|Darabszám|Összesen|A letiltani kívánt feladatok teljes száma.|jobId|
|JobStartEvent|Feladatok indítási eseményei|Darabszám|Összesen|A sikeresen elindított feladatok teljes száma.|jobId|
|JobTerminateCompleteEvent|A feladatokhoz tartozó befejezett események befejezése|Darabszám|Összesen|A sikeresen leállított feladatok teljes száma.|jobId|
|JobTerminateStartEvent|Feladatokból indított események leállítása|Darabszám|Összesen|A leállítani kívánt feladatok teljes száma.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Feladatok elküldve|Feladatok elküldve|Darabszám|Összesen|Elküldött feladatok száma|Forgatókönyv, ClusterName|
|A feladatok befejeződtek|A feladatok befejeződtek|Darabszám|Összesen|Befejezett feladatok száma|Forgatókönyv, ClusterName, ResultType|
|Csomópontok összesen|Csomópontok összesen|Darabszám|Átlag|Összes csomópont száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Aktív csomópontok|Darabszám|Átlag|Futó csomópontok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Tétlen csomópontok|Darabszám|Átlag|Üresjárati csomópontok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Csomópontok elhagyása|Darabszám|Átlag|Kilépő csomópontok száma|Forgatókönyv, ClusterName|
|Magok összesen|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Aktív magok|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Üresjárati magok|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Használhatatlan magok|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Előzik magok|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Magok kihagyása|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtsága (%)|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|CPU-használat százaléka|Százalék|Maximum|CPU-használat százaléka|Csomópont|
|MemoryUsage|Memóriahasználat|Bájt|Átlag|Memóriahasználat|Csomópont|
|MemoryLimit|Memória korlátja|Bájt|Átlag|Memória korlátja|Csomópont|
|MemoryUsagePercentageInDouble|Memóriahasználat százaléka|Százalék|Átlag|Memóriahasználat százaléka|Csomópont|
|StorageUsage|Tárterület-használat|Bájt|Átlag|Tárterület-használat|Csomópont|
|IOReadBytes|IO olvasási bájtok|Bájt|Összesen|IO olvasási bájtok|Csomópont|
|IOWriteBytes|IO írási bájtjai|Bájt|Összesen|IO írási bájtjai|Csomópont|
|ConnectionAccepted|Elfogadott kapcsolatok|Darabszám|Összesen|Elfogadott kapcsolatok|Csomópont|
|ConnectionHandled|Kezelt kapcsolatok|Darabszám|Összesen|Kezelt kapcsolatok|Csomópont|
|ConnectionActive|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Csomópont|
|RequestHandled|Kezelt kérelmek|Darabszám|Összesen|Kezelt kérelmek|Csomópont|
|ProcessedBlocks|Feldolgozott blokkok|Darabszám|Összesen|Feldolgozott blokkok|Csomópont|
|ProcessedTransactions|Feldolgozott tranzakciók|Darabszám|Összesen|Feldolgozott tranzakciók|Csomópont|
|PendingTransactions|Függőben lévő tranzakciók|Darabszám|Átlag|Függőben lévő tranzakciók|Csomópont|
|QueuedTransactions|Várólistán lévő tranzakciók|Darabszám|Átlag|Várólistán lévő tranzakciók|Csomópont|



## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients|Csatlakozott ügyfelek|Darabszám|Maximum||ShardId|
|totalcommandsprocessed|Összes művelet|Darabszám|Összesen||ShardId|
|cachehits|Gyorsítótár-találatok|Darabszám|Összesen||ShardId|
|cachemisses|Gyorsítótár-lemaradás|Darabszám|Összesen||ShardId|
|cachemissrate|Gyorsítótár-kihagyás aránya|Százalék|cachemissrate||ShardId|
|getcommands|Kap|Darabszám|Összesen||ShardId|
|setcommands|Halmazok|Darabszám|Összesen||ShardId|
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
|cacheLatency|Gyorsítótár késési másodpercek (előzetes verzió)|Darabszám|Átlag||ShardId|
|Hibák|Hibák|Darabszám|Maximum||ShardId, ErrorType|
|connectedclients0|Csatlakoztatott ügyfelek (0. szegmens)|Darabszám|Maximum||None|
|totalcommandsprocessed0|Összes művelet (0. szegmens)|Darabszám|Összesen||None|
|cachehits0|Gyorsítótárbeli találatok (szegmens 0)|Darabszám|Összesen||None|
|cachemisses0|Gyorsítótár-kihagyás (0. szegmens)|Darabszám|Összesen||None|
|getcommands0|Beolvasás (0. szegmens)|Darabszám|Összesen||None|
|setcommands0|Készletek (0. szegmens)|Darabszám|Összesen||None|
|operationsPerSecond0|Művelet/másodperc (0. Szilánk)|Darabszám|Maximum||None|
|evictedkeys0|Kizárt kulcsok (0. szegmens)|Darabszám|Összesen||None|
|totalkeys0|Összes kulcs (szilánk 0)|Darabszám|Maximum||None|
|expiredkeys0|Lejárt kulcsok (szilánk 0)|Darabszám|Összesen||None|
|usedmemory0|Felhasznált memória (0. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss0|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||None|
|serverLoad0|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||None|
|cacheWrite0|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead0|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime0|CPU (0. szegmens)|Százalék|Maximum||None|
|connectedclients1|Csatlakoztatott ügyfelek (1. szegmens)|Darabszám|Maximum||None|
|totalcommandsprocessed1|Összes művelet (1. szegmens)|Darabszám|Összesen||None|
|cachehits1|Gyorsítótárbeli találatok (1. Szilánk)|Darabszám|Összesen||None|
|cachemisses1|Gyorsítótár-lemaradás (1. szegmens)|Darabszám|Összesen||None|
|getcommands1|Beolvasás (1. szegmens)|Darabszám|Összesen||None|
|setcommands1|Készletek (1. szegmens)|Darabszám|Összesen||None|
|operationsPerSecond1|Művelet/másodperc (1. szegmens)|Darabszám|Maximum||None|
|evictedkeys1|Kizárt kulcsok (1. Szilánk)|Darabszám|Összesen||None|
|totalkeys1|Összes kulcs (1. szegmens)|Darabszám|Maximum||None|
|expiredkeys1|Lejárt kulcsok (1. szegmens)|Darabszám|Összesen||None|
|usedmemory1|Felhasznált memória (1. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss1|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||None|
|serverLoad1|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||None|
|cacheWrite1|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead1|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime1|CPU (1. Szilánk)|Százalék|Maximum||None|
|connectedclients2|Csatlakoztatott ügyfelek (2. szegmens)|Darabszám|Maximum||None|
|totalcommandsprocessed2|Összes művelet (2. szegmens)|Darabszám|Összesen||None|
|cachehits2|Gyorsítótárbeli találatok (2. szegmens)|Darabszám|Összesen||None|
|cachemisses2|Gyorsítótár-lemaradás (2. szegmens)|Darabszám|Összesen||None|
|getcommands2|Beolvasás (2. szegmens)|Darabszám|Összesen||None|
|setcommands2|Készletek (2. szegmens)|Darabszám|Összesen||None|
|operationsPerSecond2|Művelet/másodperc (2. szegmens)|Darabszám|Maximum||None|
|evictedkeys2|Kizárt kulcsok (2. szegmens)|Darabszám|Összesen||None|
|totalkeys2|Összes kulcs (2. szegmens)|Darabszám|Maximum||None|
|expiredkeys2|Lejárt kulcsok (2. szegmens)|Darabszám|Összesen||None|
|usedmemory2|Felhasznált memória (2. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss2|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||None|
|serverLoad2|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||None|
|cacheWrite2|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead2|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime2|CPU (2. szegmens)|Százalék|Maximum||None|
|connectedclients3|Csatlakoztatott ügyfelek (3. Szilánk)|Darabszám|Maximum||None|
|totalcommandsprocessed3|Összes művelet (3. szegmens)|Darabszám|Összesen||None|
|cachehits3|Gyorsítótárbeli találatok (3. Szilánk)|Darabszám|Összesen||None|
|cachemisses3|Gyorsítótár-lemaradás (3. szegmens)|Darabszám|Összesen||None|
|getcommands3|Beolvasás (3. szegmens)|Darabszám|Összesen||None|
|setcommands3|Készletek (3. szegmens)|Darabszám|Összesen||None|
|operationsPerSecond3|Művelet/másodperc (3. szegmens)|Darabszám|Maximum||None|
|evictedkeys3|Kizárt kulcsok (3. Szilánk)|Darabszám|Összesen||None|
|totalkeys3|Összes kulcs (3. Szilánk)|Darabszám|Maximum||None|
|expiredkeys3|Lejárt kulcsok (3. Szilánk)|Darabszám|Összesen||None|
|usedmemory3|Felhasznált memória (3. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss3|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||None|
|serverLoad3|Kiszolgáló terhelése (3. Szilánk)|Százalék|Maximum||None|
|cacheWrite3|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||None|
|cacheRead3|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime3|CPU (3. Szilánk)|Százalék|Maximum||None|
|connectedclients4|Csatlakoztatott ügyfelek (4. Szilánk)|Darabszám|Maximum||None|
|totalcommandsprocessed4|Összes művelet (4. Szilánk)|Darabszám|Összesen||None|
|cachehits4|Gyorsítótár-találatok (4. Szilánk)|Darabszám|Összesen||None|
|cachemisses4|Gyorsítótár-lemaradás (4. szegmens)|Darabszám|Összesen||None|
|getcommands4|Beolvasás (4. Szilánk)|Darabszám|Összesen||None|
|setcommands4|Készletek (4. Szilánk)|Darabszám|Összesen||None|
|operationsPerSecond4|Művelet/másodperc (4. szegmens)|Darabszám|Maximum||None|
|evictedkeys4|Kizárt kulcsok (4. Szilánk)|Darabszám|Összesen||None|
|totalkeys4|Összes kulcs (4. Szilánk)|Darabszám|Maximum||None|
|expiredkeys4|Lejárt kulcsok (4. Szilánk)|Darabszám|Összesen||None|
|usedmemory4|Felhasznált memória (4. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss4|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||None|
|serverLoad4|Kiszolgáló terhelése (4. Szilánk)|Százalék|Maximum||None|
|cacheWrite4|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||None|
|cacheRead4|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||None|
|percentProcessorTime4|CPU (4. Szilánk)|Százalék|Maximum||None|
|connectedclients5|Csatlakoztatott ügyfelek (5. Szilánk)|Darabszám|Maximum||None|
|totalcommandsprocessed5|Összes művelet (5. Szilánk)|Darabszám|Összesen||None|
|cachehits5|Gyorsítótárbeli találatok (szegmens 5)|Darabszám|Összesen||None|
|cachemisses5|Gyorsítótár-lemaradás (5. szegmens)|Darabszám|Összesen||None|
|getcommands5|Beolvasás (szegmens 5)|Darabszám|Összesen||None|
|setcommands5|Készletek (szilánk 5)|Darabszám|Összesen||None|
|operationsPerSecond5|Művelet/másodperc (5. szegmens)|Darabszám|Maximum||None|
|evictedkeys5|Kizárt kulcsok (5. Szilánk)|Darabszám|Összesen||None|
|totalkeys5|Összes kulcs (5. szegmens)|Darabszám|Maximum||None|
|expiredkeys5|Lejárt kulcsok (szilánk 5)|Darabszám|Összesen||None|
|usedmemory5|Felhasznált memória (5. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss5|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||None|
|serverLoad5|Kiszolgáló terhelése (5. Szilánk)|Százalék|Maximum||None|
|cacheWrite5|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||None|
|cacheRead5|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||None|
|percentProcessorTime5|CPU (5. Szilánk)|Százalék|Maximum||None|
|connectedclients6|Csatlakoztatott ügyfelek (6. szegmens)|Darabszám|Maximum||None|
|totalcommandsprocessed6|Összes művelet (szegmens 6)|Darabszám|Összesen||None|
|cachehits6|Gyorsítótárbeli találatok (szilánk 6)|Darabszám|Összesen||None|
|cachemisses6|Gyorsítótár-lemaradás (szegmens 6)|Darabszám|Összesen||None|
|getcommands6|Beolvasás (szilánk 6)|Darabszám|Összesen||None|
|setcommands6|Készletek (szilánk 6)|Darabszám|Összesen||None|
|operationsPerSecond6|Művelet/másodperc (szegmens 6)|Darabszám|Maximum||None|
|evictedkeys6|Kizárt kulcsok (szegmens 6)|Darabszám|Összesen||None|
|totalkeys6|Összes kulcs (szilánk 6)|Darabszám|Maximum||None|
|expiredkeys6|Lejárt kulcsok (szilánk 6)|Darabszám|Összesen||None|
|usedmemory6|Felhasznált memória (6. szegmens)|Bájt|Maximum||None|
|usedmemoryRss6|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||None|
|serverLoad6|Kiszolgáló betöltése (szegmens 6)|Százalék|Maximum||None|
|cacheWrite6|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||None|
|cacheRead6|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||None|
|percentProcessorTime6|CPU (szegmens 6)|Százalék|Maximum||None|
|connectedclients7|Csatlakoztatott ügyfelek (7. szegmens)|Darabszám|Maximum||None|
|totalcommandsprocessed7|Összes művelet (7. szegmens)|Darabszám|Összesen||None|
|cachehits7|Gyorsítótárbeli találatok (szegmens 7)|Darabszám|Összesen||None|
|cachemisses7|Gyorsítótár-lemaradás (7. szegmens)|Darabszám|Összesen||None|
|getcommands7|Beolvasás (szegmens 7)|Darabszám|Összesen||None|
|setcommands7|Készletek (szegmens 7)|Darabszám|Összesen||None|
|operationsPerSecond7|Művelet/másodperc (7. szegmens)|Darabszám|Maximum||None|
|evictedkeys7|Kizárt kulcsok (7. szegmens)|Darabszám|Összesen||None|
|totalkeys7|Összes kulcs (szegmens 7)|Darabszám|Maximum||None|
|expiredkeys7|Lejárt kulcsok (7. szegmens)|Darabszám|Összesen||None|
|usedmemory7|Felhasznált memória (7. szegmens)|Bájt|Maximum||None|
|usedmemoryRss7|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||None|
|serverLoad7|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||None|
|cacheWrite7|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||None|
|cacheRead7|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||None|
|percentProcessorTime7|PROCESSZOR (7. szegmens)|Százalék|Maximum||None|
|connectedclients8|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||None|
|totalcommandsprocessed8|Összes művelet (szegmens 8)|Darabszám|Összesen||None|
|cachehits8|Gyorsítótárbeli találatok (szilánk 8)|Darabszám|Összesen||None|
|cachemisses8|Gyorsítótár-lemaradás (szegmens 8)|Darabszám|Összesen||None|
|getcommands8|Beolvasás (szilánk 8)|Darabszám|Összesen||None|
|setcommands8|Készletek (szilánk 8)|Darabszám|Összesen||None|
|operationsPerSecond8|Művelet/másodperc (szegmens 8)|Darabszám|Maximum||None|
|evictedkeys8|Kizárt kulcsok (szilánk 8)|Darabszám|Összesen||None|
|totalkeys8|Összes kulcs (szilánk 8)|Darabszám|Maximum||None|
|expiredkeys8|Lejárt kulcsok (szilánk 8)|Darabszám|Összesen||None|
|usedmemory8|Felhasznált memória (8. Szilánk)|Bájt|Maximum||None|
|usedmemoryRss8|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||None|
|serverLoad8|Kiszolgáló betöltése (szegmens 8)|Százalék|Maximum||None|
|cacheWrite8|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||None|
|cacheRead8|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||None|
|percentProcessorTime8|CPU (szegmens 8)|Százalék|Maximum||None|
|connectedclients9|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||None|
|totalcommandsprocessed9|Összes művelet (szegmens 9)|Darabszám|Összesen||None|
|cachehits9|Gyorsítótárbeli találatok (szilánk 9)|Darabszám|Összesen||None|
|cachemisses9|Gyorsítótár-lemaradás (szegmens 9)|Darabszám|Összesen||None|
|getcommands9|Beolvasás (szegmens 9)|Darabszám|Összesen||None|
|setcommands9|Készletek (szilánk 9)|Darabszám|Összesen||None|
|operationsPerSecond9|Művelet/másodperc (szegmens 9)|Darabszám|Maximum||None|
|evictedkeys9|Kizárt kulcsok (szilánk 9)|Darabszám|Összesen||None|
|totalkeys9|Összes kulcs (szilánk 9)|Darabszám|Maximum||None|
|expiredkeys9|Lejárt kulcsok (szilánk 9)|Darabszám|Összesen||None|
|usedmemory9|Felhasznált memória (10. szegmens)|Bájt|Maximum||None|
|usedmemoryRss9|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||None|
|serverLoad9|Kiszolgáló terhelése (szegmens 9)|Százalék|Maximum||None|
|cacheWrite9|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||None|
|cacheRead9|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||None|
|percentProcessorTime9|CPU (szilánk 9)|Százalék|Maximum||None|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|None|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|None|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|None|
|Lemez olvasási sebessége (bájt/s)|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|None|
|Lemez írási sebessége (bájt/s)|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|None|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|None|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Lemez olvasási sebessége (bájt/s)|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Felhasznált kapacitás|Bájt|Átlag|Fiókhoz használt kapacitás|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Blobok száma|Darabszám|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiók Blob service lévő tárolók száma.|None|
|IndexCapacity|Index kapacitása|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|None|
|TableCount|Táblák száma|Darabszám|Átlag|A Storage-fiók Table service található tábla száma.|None|
|TableEntityCount|Tábla entitások száma|Darabszám|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileCount|Fájlok száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|None|
|FileShareSnapshotCount|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileShareQuota|Fájlmegosztás kvótájának mérete|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|None|
|QueueCount|Várólista száma|Darabszám|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|None|
|QueueMessageCount|Üzenetsor-üzenetek száma|Darabszám|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
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
|TotalTokenCalls|Jogkivonat-hívások összesen|Darabszám|Összesen|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|
|CharactersTranslated|Lefordított karakterek|Darabszám|Összesen|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTrained|Betanított karakterek|Darabszám|Összesen|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|SpeechSessionDuration|Beszédfelismerési munkamenet időtartama|Másodperc|Összesen|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|TotalTransactions|Tranzakciók összesen|Darabszám|Összesen|A tranzakciók száma összesen|None|
|ProcessedImages|Feldolgozott rendszerképek|Darabszám|Összesen| A képfeldolgozáshoz tartozó tranzakciók száma.|ApiName, FeatureName, csatorna, régió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|None|
|Bejövő hálózat|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|None|
|Kimenő hálózat|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|None|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|None|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|None|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|None|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|None|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|None|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD [(elavult)](portal-disk-metrics-deprecation.md)] (Portal-Disk-Metrics-deprecation.MD)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD [(elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|OS-lemez várakozási sorának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|None|
|Kimenő folyamatok|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|None|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|None|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|None|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|None|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|None|
|Teljes hálózat|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|None|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Bejövő hálózat|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|VMName|
|Kimenő hálózat|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|VMName|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|VMName|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|VMName|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|None|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD [(elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD [(elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OS-lemez várakozási sorának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő folyamatok|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|
|Teljes hálózat|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. számítás/virtualMachineScaleSets/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|None|
|Bejövő hálózat|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|None|
|Kimenő hálózat|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|None|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|None|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|None|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|None|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|None|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|None|
|Felhasznált CPU-kreditek|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|/Lemez olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez írási sebessége (bájt/s)|Adatlemez-írási sebesség (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Adatlemez olvasási műveletei (művelet/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási műveletek másodpercenként|Adatlemez írási műveletei (művelet/mp [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez QD|Adatlemez QD [(elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s [) (elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|Operációs rendszer/lemez olvasási művelet/mp|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez írási műveletei másodpercenként|OPERÁCIÓSRENDSZER-lemez írási művelete/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|Operációs rendszer/lemez QD|OPERÁCIÓSRENDSZER-lemez QD [(elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Adatlemez-olvasási sebesség (bájt/s)|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez-írási sebesség (bájt/s)|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez írási műveletei (művelet/s)|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|None|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|None|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|None|
|OS-lemez várakozási sorának mélysége|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|None|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|None|
|Kimenő folyamatok|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|None|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|None|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|None|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|None|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|None|
|Teljes hálózat|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|None|
|Kimenő hálózat összesen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage|Processzorhasználat|Darabszám|Átlag|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Memóriahasználat|Bájt|Átlag|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Másodpercenként fogadott hálózati bájtok száma|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|None|
|NetworkBytesTransmittedPerSecond|Másodpercenként továbbított hálózati bájtok száma|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalPullCount|Lekérések száma összesen|Darabszám|Átlag|A lekérések száma összesen|None|
|SuccessfulPullCount|Sikeres lekérések száma|Darabszám|Átlag|Sikeres Képkeresések száma|None|
|TotalPushCount|Leküldések száma összesen|Darabszám|Átlag|Leküldések száma összesen|None|
|SuccessfulPushCount|Sikeres leküldések száma|Darabszám|Átlag|Sikeres leküldések száma|None|
|RunDuration|Futtatás időtartama|Ezredmásodpercben|Összesen|Futtatás időtartama (ezredmásodpercben)|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Darabszám|Átlag|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|None|
|kube_node_status_allocatable_memory_bytes|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Átlag|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|None|
|kube_pod_status_ready|A hüvelyek száma üzemkész állapotban|Darabszám|Átlag|A hüvelyek száma üzemkész állapotban|névtér, Pod|
|kube_node_status_condition|Különböző csomóponti feltételek állapota|Darabszám|Átlag|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|Hüvelyek száma fázis szerint|Darabszám|Átlag|Hüvelyek száma fázis szerint|fázis, névtér, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfullRequests|Sikeres kérések|Darabszám|Összesen|Az egyéni szolgáltató által kezdeményezett sikeres kérések|HttpMethod, CallPath, StatusCode|
|FailedRequests|Sikertelen kérelmek|Darabszám|Összesen|A rendelkezésre álló naplók beolvasása egyéni erőforrás-szolgáltatók számára|HttpMethod, CallPath, StatusCode|

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


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft. DataCatalog/datacatalogs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Eszközök megoszlása besorolás szerint|Darabszám|Összesen|Azt jelzi, hogy hány eszköz van hozzárendelve bizonyos besorolással, azaz hogy az adott címkével vannak besorolva.|Besorolás, forrás|
|AssetDistributionByStorageType|Eszközök eloszlása tárolási típus szerint|Darabszám|Összesen|Meghatározza az adott tárolási típussal rendelkező eszközök számát.|StorageType|
|NumberOfAssetsWithClassifications|Legalább egy besorolású eszközök száma|Darabszám|Átlag|Meghatározza a legalább egy címkével rendelkező eszközök számát.|None|
|ScanCancelled|Vizsgálat megszakítva|Darabszám|Összesen|A megszakított ellenőrzések számát jelzi.|None|
|ScanCompleted|A vizsgálat befejeződött|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma sikeresen befejeződött.|None|
|ScanFailed|Sikertelen vizsgálat|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma nem sikerült.|None|
|ScanTimeTaken|Vizsgálat ideje elvégezve|Másodperc|Összesen|A teljes vizsgálati időt jelzi másodpercben.|None|
|CatalogActiveUsers|Napi aktív felhasználók|Darabszám|Összesen|Napi aktív felhasználók száma|None|
|CatalogUsage|Használat eloszlása művelet szerint|Darabszám|Összesen|Adja meg, hogy a felhasználó hány műveletet végez a katalógusban, pl.: hozzáférés, keresés, szószedet.|Művelet|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FailedRuns|Sikertelen futtatások|Darabszám|Összesen||pipelineName, activityName|
|SuccessfulRuns|Sikeres futtatások|Darabszám|Összesen||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Sikertelen folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineSucceededRuns|A folyamat sikeresen futtatja a metrikákat|Darabszám|Összesen||FailureType, név|
|PipelineCancelledRuns|Megszakított folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|ActivityFailedRuns|Sikertelen tevékenység-futtatási metrikák|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|A sikeres tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivityCancelledRuns|A megszakított tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|TriggerFailedRuns|Sikertelen trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerSucceededRuns|A sikeres trigger metrikákat futtat|Darabszám|Összesen||Név, FailureType|
|TriggerCancelledRuns|Megszakított trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|IntegrationRuntimeCpuPercentage|Integration Runtime CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableMemory|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAverageTaskPickupDelay|Integration Runtime-várólista időtartama|Másodperc|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Integration Runtime-várólista hossza|Darabszám|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Integrációs modul elérhető csomópontok száma|Darabszám|Átlag||IntegrationRuntimeName|
|MaxAllowedResourceCount|Engedélyezett entitások maximális száma|Darabszám|Maximum||None|
|MaxAllowedFactorySizeInGbUnits|Maximálisan engedélyezett gyári méret (GB egység)|Darabszám|Maximum||None|
|ResourceCount|Entitások száma összesen|Darabszám|Maximum||None|
|FactorySizeInGbUnits|Gyári méret összesen (GB egység)|Darabszám|Maximum||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSuccess|Sikeres feladatok|Darabszám|Összesen|A sikeres feladatok száma.|None|
|JobEndedFailure|Sikertelen feladatok|Darabszám|Összesen|Sikertelen feladatok száma.|None|
|JobEndedCancelled|Megszakított feladatok|Darabszám|Összesen|Megszakított feladatok száma.|None|
|JobAUEndedSuccess|Sikeres AU-idő|Másodperc|Összesen|A sikeres feladatok teljes AU-ideje.|None|
|JobAUEndedFailure|Sikertelen AU-idő|Másodperc|Összesen|A sikertelen feladatok összes AU-ideje.|None|
|JobAUEndedCancelled|Megszakított AU-idő|Másodperc|Összesen|A megszakított feladatok teljes AU-ideje.|None|
|JobStage|Feladatok a fázisban|Darabszám|Összesen|A feladatok száma az egyes fázisokban.|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|None|
|DataWritten|Írt adatértékek|Bájt|Összesen|A fiókba írt összes adatmennyiség.|None|
|DataRead|Olvasott információk|Bájt|Összesen|A fiókból beolvasott adatok teljes mennyisége.|None|
|WriteRequests|Írási kérelmek|Darabszám|Összesen|A fiókra vonatkozó adatírási kérelmek száma.|None|
|ReadRequests|Olvasási kérelmek|Darabszám|Összesen|A fióknak küldött adatolvasási kérelmek száma.|None|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ShareCount|Eljuttatott megosztások|Darabszám|Maximum|A fiókban eljuttatott megosztások száma|ShareName|
|ShareSubscriptionCount|Fogadott megosztások|Darabszám|Maximum|A fiókban fogadott megosztások száma|ShareSubscriptionName|
|SucceededShareSynchronizations|Az eljuttatott megosztás pillanatképei sikeresen megtörténtek|Darabszám|Darabszám|A fiókban sikeresen elvégezte a sikeres megosztási Pillanatképek számát|None|
|FailedShareSynchronizations|Nem sikerült elküldeni a megosztást pillanatképeket|Darabszám|Darabszám|A fiókban sikertelenül továbbított megosztási Pillanatképek száma|None|
|SucceededShareSubscriptionSynchronizations|A kapott megosztás sikeres pillanatképei|Darabszám|Darabszám|A fogadott megosztás sikeres pillanatképei a fiókban|None|
|FailedShareSubscriptionSynchronizations|Fogadott megosztás sikertelen Pillanatképek|Darabszám|Darabszám|A fogadott megosztások száma sikertelen Pillanatképek a fiókban|None|


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
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|seconds_behind_master|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|None|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|


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
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|seconds_behind_master|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|None|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|


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
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|pg_replica_log_delay_in_seconds|Replika késése|Másodperc|Maximum|Replika késése másodpercben|None|
|pg_replica_log_delay_in_bytes|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|IOPS|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|None|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|Átlag|CPU-százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|IOPS|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|None|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|connections_succeeded|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|None|
|maximum_used_transactionIDs|Maximálisan használt tranzakciós azonosítók|Darabszám|Átlag|Maximálisan használt tranzakciós azonosítók|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|D2C. telemetria. beáramló. allProtocol|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|None|
|D2C. telemetria. beáramló. sikeres|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|None|
|C2D. commands. kimenő. Complete. success|C2D-üzenetek kézbesítésének befejezése|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|None|
|C2D. commands. kimenő. elhagyása. sikeres|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|None|
|C2D. commands. kimenő. elutasítás. sikeres|C2D-üzenetek elutasítva|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|None|
|C2DMessagesExpired|C2D-üzenetek lejárt (előzetes verzió)|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|None|
|Devices. totalDevices|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|None|
|Devices. connectedDevices. allProtocol|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|None|
|D2C. telemetria. kimenő. sikeres|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|None|
|D2C. telemetria. kimenő. eldobott|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|None|
|D2C. telemetria. kimenő. árva|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |None|
|D2C. telemetria. kimenő. érvénytelen|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|None|
|D2C. telemetria. kimenő. tartalék|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|None|
|D2C. endpoints. kimenő. eventHubs|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|None|
|D2C. endpoints. késleltetés. eventHubs|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|None|
|D2C. endpoints. kimenő. serviceBusQueues|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|None|
|D2C. endpoints. késleltetés. serviceBusQueues|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|None|
|D2C. endpoints. kimenő. serviceBusTopics|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|None|
|D2C. endpoints. késleltetés. serviceBusTopics|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|None|
|D2C. endpoints. kimenő. beépített. események|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események).|None|
|D2C. endpoints. késleltetés. beépített. események|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események).|None|
|D2C. endpoints. kimenő. Storage|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|None|
|D2C. endpoints. látencia. Storage|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|None|
|D2C. endpoints. kimenő. Storage. Bytes|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|None|
|D2C. endpoints. kimenő. Storage. Blobok|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|None|
|EventGridDeliveries|Event Grid kézbesítések (előzetes verzió)|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusáthttps://aka.ms/ioteventgrid)(.|ResourceId, eredmény, EventType|
|EventGridLatency|Event Grid késés (előzetes verzió)|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|ResourceId, EventType|
|RoutingDeliveries|Útválasztási kézbesítések (előzetes verzió)|Ezredmásodpercben|Összesen|Azon időpontok száma, IoT Hub az összes végpont felé irányuló üzenetek továbbítására történt kísérlet az Útválasztás használatával. A sikeres vagy sikertelen kísérletek számának megtekintéséhez használja az eredmény dimenziót. Ha szeretné megtekinteni a hiba okát (például érvénytelen, eldobott vagy árva), használja az FailureReasonCategory dimenziót. A Végpontneve és a EndpointType dimenzióval is megtudhatja, hogy hány üzenet lett továbbítva a különböző végpontoknak. A metrika értéke eggyel nő az egyes kézbesítési kísérleteknél, beleértve azt is, hogy az üzenetet több végpontra kézbesítik-e, vagy ha az üzenet többször is ugyanarra a végpontra van kézbesítve.|ResourceId, EndpointType, Végpontneve, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodpercben|Átlag|Az üzenet bejövő értékének átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria az üzeneteket a végpontba. A Végpontneve és a EndpointType méretek segítségével megismerheti a különböző végpontok késését.|ResourceId, EndpointType, Végpontneve, RoutingSource|
|D2C. Twin. Read. success|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|None|
|D2C. Twin. Read. failure|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|None|
|D2C. Twin. Read. size|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|None|
|D2C. Twin. Update. success|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|None|
|D2C. Twin. Update. failure|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|None|
|D2C. Twin. Update. size|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|None|
|C2D. Methods. success|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|None|
|C2D. Methods. failure|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|None|
|C2D. Methods. requestSize|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|None|
|C2D. Methods. responseSize|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|None|
|C2D. Twin. Read. success|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|None|
|C2D. Twin. Read. failure|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|None|
|C2D. Twin. Read. size|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|None|
|C2D. Twin. Update. success|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|None|
|C2D. Twin. Update. failure|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|None|
|C2D. Twin. Update. size|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|None|
|twinQueries. success|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|None|
|twinQueries. hiba|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|None|
|twinQueries.resultSize|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|None|
|feladatok. createTwinUpdateJob. sikeres|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|None|
|feladatok. createTwinUpdateJob. hiba|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|None|
|feladatok. createDirectMethodJob. sikeres|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|None|
|feladatok. createDirectMethodJob. hiba|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|None|
|feladatok. listJobs. sikeres|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|None|
|feladatok. listJobs. hiba|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|None|
|feladatok. cancelJob. sikeres|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|None|
|feladatok. cancelJob. hiba|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|None|
|feladatok. queryJobs. sikeres|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|None|
|feladatok. queryJobs. hiba|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|None|
|feladatok. kész|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|None|
|feladatok. sikertelen|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|None|
|D2C. telemetria. beáramló. sendThrottle|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|None|
|dailyMessageQuotaUsed|A felhasznált üzenetek teljes száma|Darabszám|Átlag|A jelenleg használt összes üzenet száma|None|
|deviceDataUsage|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|None|
|deviceDataUsageV2|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|None|
|totalDeviceCount|Összes eszköz (előzetes verzió)|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|None|
|connectedDeviceCount|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|None|
|konfigurációk|Konfigurációs mérőszámok|Darabszám|Összesen|A konfigurációs műveletek metrikái|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RegistrationAttempts|Regisztrációs kísérletek|Darabszám|Összesen|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, állapot|
|DeviceAssignments|Hozzárendelt eszközök|Darabszám|Összesen|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Igazolási kísérletek|Darabszám|Összesen|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, állapot, protokoll|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AddRegion|Régió hozzáadva|Darabszám|Darabszám|Régió hozzáadva|Régió|
|AvailableStorage|Rendelkezésre álló tár|Bájt|Összesen|5 perces részletességgel jelentett teljes rendelkezésre álló tárterület|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|Cassandra-kapcsolatok bezárása|Darabszám|Összesen|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|APIType, régió, ClosureReason|
|CassandraKeyspaceDelete|Cassandra szóköz törölve|Darabszám|Darabszám|Cassandra szóköz törölve|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra Space-átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Space-átviteli sebesség frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra Space frissítve|Darabszám|Darabszám|Cassandra Space frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Cassandra-kérelmek díjai|Darabszám|Összesen|A Cassandra-kérelmekhez felhasznált RUs|APIType, DatabaseName, CollectionName, régió, OperationType, ResourceType|
|CassandraRequests|Cassandra-kérelmek|Darabszám|Darabszám|A végrehajtott Cassandra-kérelmek száma|APIType, DatabaseName, CollectionName, region, OperationType, ResourceType, ErrorCode|
|CassandraTableDelete|Cassandra-tábla törölve|Darabszám|Darabszám|Cassandra-tábla törölve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Cassandra Table átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Table átviteli sebesség frissítve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Cassandra-tábla frissítve|Darabszám|Darabszám|Cassandra-tábla frissítve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CreateAccount|Fiók létrehozva|Darabszám|Darabszám|Fiók létrehozva|None|
|DataUsage|Adathasználat|Bájt|Összesen|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DeleteAccount|Fiók törölve|Darabszám|Darabszám|Fiók törölve|None|
|DocumentCount|Dokumentumok száma|Darabszám|Összesen|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|Dokumentum kvótája|Bájt|Összesen|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|GremlinDatabaseDelete|Gremlin-adatbázis törölve|Darabszám|Darabszám|Gremlin-adatbázis törölve|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|Gremlin adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin adatbázis átviteli sebessége frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Gremlin-adatbázis frissítve|Darabszám|Darabszám|Gremlin-adatbázis frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Gremlin gráf törölve|Darabszám|Darabszám|Gremlin gráf törölve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Gremlin gráf átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin gráf átviteli sebessége frissítve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Gremlin gráf frissítve|Darabszám|Darabszám|Gremlin gráf frissítve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Indexelés használata|Bájt|Összesen|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|MetadataRequests|Metaadat-kérelmek|Darabszám|Darabszám|A metaadat-kérelmek száma. Cosmos DB az egyes fiókok rendszermetaadat-gyűjteményét kezeli, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, szerepkör|
|MongoCollectionDelete|Mongo-gyűjtemény törölve|Darabszám|Darabszám|Mongo-gyűjtemény törölve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|A Mongo-gyűjtési átviteli sebesség frissítve|Darabszám|Darabszám|A Mongo-gyűjtési átviteli sebesség frissítve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Mongo-gyűjtemény frissítve|Darabszám|Darabszám|Mongo-gyűjtemény frissítve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Mongo-adatbázis frissítve|Darabszám|Darabszám|Mongo-adatbázis frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Mongo-adatbázis törölve|Darabszám|Darabszám|Mongo-adatbázis törölve|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|Mongo adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Mongo adatbázis átviteli sebessége frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|Mongo-kérelem díja|Darabszám|Összesen|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequests|Mongo kérelmek|Darabszám|Darabszám|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequestsCount|Mongo kérelmek gyakorisága|CountPerSecond|Átlag|Mongo kérelmek száma másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo-törlési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-törlési kérelem másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsInsert|Mongo-beszúrási kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-beszúrási szám másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsQuery|Mongo-lekérdezési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-lekérdezési kérelem másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|MongoRequestsUpdate|Mongo-frissítési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo frissítési kérelem másodpercenként|DatabaseName, CollectionName, régió, CommandName, ErrorCode|
|NormalizedRUConsumption|Normalizált RU-felhasználás|Százalék|Maximum|Maximális RU-százalék/perc|CollectionName, DatabaseName, régió|
|ProvisionedThroughput|Kiosztott átviteli sebesség|Darabszám|Maximum|Kiosztott átviteli sebesség|DatabaseName, CollectionName|
|RegionFailover|A régió feladatátvétele megtörtént|Darabszám|Darabszám|A régió feladatátvétele megtörtént|None|
|RemoveRegion|Régió eltávolítva|Darabszám|Darabszám|Régió eltávolítva|Régió|
|ReplicationLatency|P99 replikáció késése|Ezredmásodpercben|Átlag|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion,TargetRegion|
|ServerSideLatency|Kiszolgálóoldali késés|Ezredmásodpercben|Átlag|Kiszolgálóoldali késés|DatabaseName, CollectionName, régió, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Szolgáltatás rendelkezésre állása|Százalék|Átlag|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|None|
|SqlContainerDelete|SQL-tároló törölve|Darabszám|Darabszám|SQL-tároló törölve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|SQL-tároló átviteli sebessége frissítve|Darabszám|Darabszám|SQL-tároló átviteli sebessége frissítve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|SQL-tároló frissítve|Darabszám|Darabszám|SQL-tároló frissítve|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|SQL-adatbázis törölve|Darabszám|Darabszám|SQL-adatbázis törölve|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|Az SQL Database átviteli sebessége frissítve|Darabszám|Darabszám|Az SQL Database átviteli sebessége frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|SQL-adatbázis frissítve|Darabszám|Darabszám|SQL-adatbázis frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|AzureTable-tábla törölve|Darabszám|Darabszám|AzureTable-tábla törölve|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|AzureTable-tábla átviteli sebessége frissítve|Darabszám|Darabszám|AzureTable-tábla átviteli sebessége frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|AzureTable tábla frissítve|Darabszám|Darabszám|AzureTable tábla frissítve|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Kérelmek összes egysége|Darabszám|Összesen|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|TotalRequests|Összes kérelem|Darabszám|Darabszám|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|UpdateAccountKeys|A fiók kulcsai frissítve|Darabszám|Darabszám|A fiók kulcsai frissítve|KeyType|
|UpdateAccountNetworkSettings|A fiók hálózati beállításai frissítve|Darabszám|Darabszám|A fiók hálózati beállításai frissítve|None|
|UpdateAccountReplicationSettings|A fiók replikációs beállításainak frissítése megtörtént|Darabszám|Darabszám|A fiók replikációs beállításainak frissítése megtörtént|None|
|UpdateDiagnosticsSettings|Fiók diagnosztikai beállításainak frissítése|Darabszám|Darabszám|Fiók diagnosztikai beállításainak frissítése|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/szolgáltatások

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TransactionCount|Tranzakciók száma|Darabszám|Darabszám|Tranzakciók száma összesen|TransactionCount|
|SuccessCount|Sikeres műveletek száma|Darabszám|Darabszám|Sikeres tranzakciók száma|SuccessCount|
|FailureCount|Hibásak száma|Darabszám|Darabszám|Sikertelen tranzakciók száma|FailureCount|
|SuccessLatency|Sikeres késés|Ezredmásodpercben|Átlag|A sikeres tranzakciók késése|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|None|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|None|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|None|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|None|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason,EventSubscriptionName|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|None|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|None|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|None|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason,EventSubscriptionName|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|None|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|None|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|None|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|None|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|None|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|None|




## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName, Operationresult tevékenységen|
|Kiszolgálóhibái|Kiszolgálói hibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName, Operationresult tevékenységen|
|UserErrors|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|EntityName, Operationresult tevékenységen|
|QuotaExceededErrors|A kvóta túllépte a hibákat.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName, Operationresult tevékenységen|
|ThrottledRequests|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|EntityName, Operationresult tevékenységen|
|IncomingRequests|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|EntityName|
|IncomingMessages|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|EntityName|
|OutgoingMessages|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|EntityName|
|IncomingBytes|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|EntityName|
|OutgoingBytes|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|EntityName|
|Aktív kapcsolatai|Aktív kapcsolatai|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|None|
|ConnectionsOpened|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|EntityName|
|ConnectionsClosed|A kapcsolatok lezárva.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|EntityName|
|CaptureBacklog|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|EntityName|
|CapturedMessages|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|EntityName|
|CapturedBytes|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|EntityName|
|Méret|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|EntityName|
|INREQS|Bejövő kérelmek (elavult)|Darabszám|Összesen|Egy névtér összes bejövő küldési kérelme (elavult)|None|
|SUCCREQ|Sikeres kérések (elavult)|Darabszám|Összesen|A névtér összes sikeres kérelme (elavult)|None|
|FAILREQ|Sikertelen kérelmek (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)|None|
|SVRBSY|Kiszolgáló által foglalt hibák (elavult)|Darabszám|Összesen|Egy névtér összes kiszolgáló által foglalt hibája (elavult)|None|
|INTERer|Belső kiszolgálói hibák (elavult)|Darabszám|Összesen|Névtér összes belső kiszolgálóhiba (elavult)|None|
|MISCERR|Egyéb hibák (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)|None|
|INMSGS|Bejövő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)|None|
|EHINMSGS|Bejövő üzenetek (elavult)|Darabszám|Összesen|Névtér összes bejövő üzenete (elavult)|None|
|OUTMSGS|Kimenő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)|None|
|EHOUTMSGS|Kimenő üzenetek (elavult)|Darabszám|Összesen|Névtér összes kimenő üzenete (elavult)|None|
|EHINMBS|Bejövő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)|None|
|EHINBYTES|Bejövő bájtok (elavult)|Bájt|Összesen|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)|None|
|EHOUTMBS|Kimenő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)|None|
|EHOUTBYTES|Kimenő bájtok (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)|None|
|EHABL|Archivált várakozó üzenetek (elavult)|Darabszám|Összesen|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)|None|
|EHAMSGS|Archivált üzenetek (elavult)|Darabszám|Összesen|Event hub archivált üzenetek egy névtérben (elavult)|None|
|EHAMBS|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összesen|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)|None|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|Operationresult tevékenységen|
|Kiszolgálóhibái|Kiszolgálói hibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|Operationresult tevékenységen|
|UserErrors|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|Operationresult tevékenységen|
|QuotaExceededErrors|A kvóta túllépte a hibákat.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|Operationresult tevékenységen|
|ThrottledRequests|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|Operationresult tevékenységen|
|IncomingRequests|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|None|
|IncomingMessages|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|None|
|OutgoingMessages|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|None|
|IncomingBytes|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|None|
|OutgoingBytes|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|None|
|Aktív kapcsolatai|Aktív kapcsolatai|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|None|
|ConnectionsOpened|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|None|
|ConnectionsClosed|A kapcsolatok lezárva.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|None|
|CaptureBacklog|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|None|
|CapturedMessages|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|None|
|CapturedBytes|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|None|
|CPU|CPU|Százalék|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Szerepkör|
|AvailableMemory|Szabad memória|Százalék|Maximum|Az Event hub-fürt rendelkezésre álló memóriája a teljes memória százalékában.|Szerepkör|
|Méret|Egy EventHub mérete bájtban megadva.|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|Szerepkör|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjáróra vonatkozó kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma|HttpStatus|
|CategorizedGatewayRequests|Kategorizált átjáró-kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Aktív feldolgozók száma|Darabszám|Maximum|Aktív feldolgozók száma|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/AutoscaleSettings

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue|Megfigyelt metrika értéke|Darabszám|Átlag|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|MetricThreshold|Metrika küszöbértéke|Darabszám|Átlag|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Megfigyelt kapacitás|Darabszám|Átlag|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.|None|
|ScaleActionsInitiated|Elindított skálázási műveletek|Darabszám|Összesen|A skálázási művelet iránya.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Rendelkezésre állás|Százalék|Átlag|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|Rendelkezésre állási tesztek|Darabszám|Darabszám|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Oldal betöltési hálózati kapcsolati ideje|Ezredmásodpercben|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|None|
|browserTimings/processingDuration|Ügyfél feldolgozási ideje|Ezredmásodpercben|Átlag|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|None|
|browserTimings/receiveDuration|Válaszidő fogadása|Ezredmásodpercben|Átlag|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|None|
|browserTimings/sendDuration|Kérelem küldési ideje|Ezredmásodpercben|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|None|
|browserTimings/totalDuration|Böngésző oldalának betöltési ideje|Ezredmásodpercben|Átlag|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|None|
|függőségek/darabszám|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/időtartam|Függőség időtartama|Ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/sikertelen|Függőségi hívások hibái|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|Oldalmegtekintések/darabszám|Lapok nézetei|Darabszám|Darabszám|Lapok nézeteinek száma.|művelet/szintetikus, felhő/roleName|
|Oldalmegtekintések/időtartam|Oldal nézet betöltési ideje|Ezredmásodpercben|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus, felhő/roleName|
|performanceCounters/requestExecutionTime|HTTP-kérelem végrehajtási ideje|Ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|felhő/roleInstance|
|performanceCounters/requestsInQueue|HTTP-kérelmek az alkalmazás-várólistában|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájának hossza|felhő/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-kérelmek gyakorisága|CountPerSecond|Átlag|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|felhő/roleInstance|
|performanceCounters/exceptionsPerSecond|Kivételek aránya|CountPerSecond|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|felhő/roleInstance|
|performanceCounters/processIOBytesPerSecond|Folyamat i/o-sebessége|BytesPerSecond|Átlag|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|felhő/roleInstance|
|performanceCounters/processCpuPercentage|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|felhő/roleInstance|
|performanceCounters/processorCpuPercentage|Processzoridő|Százalék|Átlag|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|felhő/roleInstance|
|performanceCounters/memoryAvailableBytes|Igénybe vehető memória|Bájt|Átlag|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|felhő/roleInstance|
|performanceCounters/processPrivateBytes|Saját bájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|felhő/roleInstance|
|kérelmek/időtartam|Kiszolgáló válaszideje|Ezredmásodpercben|Átlag|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/darabszám|Kiszolgálói kérelmek|Darabszám|Darabszám|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|Sikertelen kérelmek|Darabszám|Darabszám|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések >= 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, kérelem/siker, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Átlag|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kivételek/darabszám|Kivételek|Darabszám|Darabszám|Az összes nem kezelt kivétel összesített száma.|felhő/roleName, felhő/roleInstance, ügyfél/típus|
|kivételek/böngésző|Böngészőkivételek|Darabszám|Darabszám|A böngészőben fellépő nem kezelt kivételek száma.|ügyfél/isServer, felhő/roleName|
|kivételek/kiszolgáló|Kiszolgálói kivételek|Darabszám|Darabszám|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|ügyfél/isServer, felhő/roleName, felhő/roleInstance|
|nyomkövetés/darabszám|Hívásláncok|Darabszám|Darabszám|Nyomkövetési dokumentumok száma|nyomkövetés/severityLevel, művelet/szintetikus, felhő/roleName, felhő/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedDeviceCount|Csatlakoztatott eszközök összesen|Darabszám|Átlag|IoT Centralhoz csatlakoztatott eszközök száma|None|
|C2D. Property. Read. success|Az eszköz tulajdonságainak olvasása IoT Central|Darabszám|Összesen|Az IoT Centralról kezdeményezett sikeres tulajdonságok száma|None|
|C2D. Property. Read. failure|Nem sikerült beolvasni az eszköz tulajdonságát IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett sikertelen tulajdonságok összes olvasásának száma|None|
|D2C. Property. Read. success|Az eszköz tulajdonságainak olvasása az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikeres tulajdonságok összes olvasásának száma|None|
|D2C. Property. Read. failure|Nem sikerült beolvasni az eszköz tulajdonságait az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikertelen tulajdonságok összes olvasásának száma|None|
|C2D. Property. Update. success|Az eszköz tulajdonságainak frissítése IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett összes sikeres tulajdonság-frissítés száma|None|
|C2D. Property. Update. hiba|Nem sikerült frissíteni az eszköz tulajdonságait IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett sikertelen tulajdonságok frissítéseinek száma|None|
|D2C. Property. Update. success|Az eszköz tulajdonságainak frissítése az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikeres tulajdonság-frissítés száma|None|
|D2C. Property. Update. hiba|Nem sikerült az eszköz tulajdonságainak frissítése az eszközökről|Darabszám|Összesen|Az eszközökről indított összes sikertelen tulajdonság-frissítés száma|None|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Összes szolgáltatási API-találat|Darabszám|Darabszám|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Általános szolgáltatás API-késése|Ezredmásodpercben|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Összes szolgáltatás API-eredményei|Darabszám|Darabszám|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Tár teljes telítettsége|Százalék|Átlag|Tár kapacitásának használata|ActivityType, ActivityName, TransactionType|
|Rendelkezésre állás|Tár teljes rendelkezésre állása|Százalék|Átlag|Tár kéréseinek rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CacheUtilization|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|None|
|QueryDuration|Lekérdezés időtartama|Ezredmásodpercben|Átlag|Lekérdezések időtartama másodpercben|QueryStatus|
|IngestionUtilization|Betöltés kihasználtsága|Százalék|Átlag|A használatban lévő betöltési tárolóhelyek aránya a fürtben|None|
|KeepAlive|Életben tartása|Darabszám|Átlag|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|None|
|IngestionVolumeInMB|Betöltési mennyiség (MB)|Darabszám|Összesen|A betöltött adatmennyiség teljes mennyisége a fürtön (MB)|Adatbázis|
|IngestionLatencyInSeconds|Betöltési késleltetés (másodperc)|Másodperc|Átlag|A lenyelési idő a forrástól (például az üzenet a EventHub) a fürthöz másodpercek alatt|None|
|EventsProcessedForEventHubs|Feldolgozott események (Event/IoT hubok esetében)|Darabszám|Összesen|A fürt által az esemény/IoT Hub betöltéskor feldolgozott események száma|EventStatus|
|IngestionResult|Betöltés eredménye|Darabszám|Darabszám|Betöltési műveletek száma|IngestionResultDetails|
|CPU|CPU|Százalék|Átlag|CPU-kihasználtsági szint|None|
|ContinuousExportNumOfRecordsExported|Folyamatos exportálás – számú exportált rekord|Darabszám|Összesen|Az exportált rekordok száma az exportálási művelet során írt összes tárolási összetevő esetében|ContinuousExportName, adatbázis|
|ExportUtilization|Exportálás kihasználtsága|Százalék|Maximum|Exportálás kihasználtsága|None|
|ContinuousExportPendingCount|Folyamatos exportálás függőben lévő darabszáma|Darabszám|Maximum|A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma|None|
|ContinuousExportMaxLatenessMinutes|Folyamatos exportálás maximális késői|Darabszám|Maximum|A fürt folyamatos exportálási feladatai által jelentett késői idő (percben)|None|
|ContinuousExportResult|Folyamatos exportálás eredménye|Darabszám|Darabszám|Azt jelzi, hogy a folyamatos exportálás sikeres vagy sikertelen volt-e|ContinuousExportName, eredmény, adatbázis|
|StreamingIngestDuration|Folyamatos átvitel időtartama|Ezredmásodpercben|Átlag|Folyamatos átvitel időtartama (ezredmásodperc)|None|
|StreamingIngestDataRate|Adatforgalom adatátviteli sebessége|Darabszám|Átlag|Adatforgalom adatátviteli sebessége (MB/s)|None|
|SteamingIngestRequestRate|Folyamatos átviteli kérelmek gyakorisága|Darabszám|RateRequestsPerSecond|Folyamatos átviteli kérelmek aránya (kérelmek/másodperc)|None|
|StreamingIngestResults|Folyamatos átvitel eredménye|Darabszám|Átlag|Folyamatos átvitel eredménye|Eredmény|
|TotalNumberOfConcurrentQueries|Az egyidejű lekérdezések teljes száma|Darabszám|Összesen|Az egyidejű lekérdezések teljes száma|None|
|TotalNumberOfThrottledQueries|A szabályozott lekérdezések teljes száma|Darabszám|Összesen|A szabályozott lekérdezések teljes száma|None|
|TotalNumberOfThrottledCommands|A szabályozott parancsok teljes száma|Darabszám|Összesen|A szabályozott parancsok teljes száma|CommandType|
|TotalNumberOfExtents|Egységek teljes száma|Darabszám|Összesen|Adategységek teljes száma|None|
|InstanceCount|Példányszám|Darabszám|Átlag|Példányok száma összesen|None|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|None|
|RunsCompleted|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|None|
|RunsSucceeded|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|None|
|RunsFailed|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|None|
|RunsCancelled|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|None|
|RunLatency|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|None|
|RunSuccessLatency|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|None|
|RunThrottledEvents|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|None|
|RunStartThrottledEvents|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|None|
|RunFailurePercentage|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|None|
|ActionsStarted|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|None|
|ActionsCompleted|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|None|
|ActionsSucceeded|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|None|
|ActionsFailed|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|None|
|ActionsSkipped|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|None|
|ActionLatency|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|None|
|ActionSuccessLatency|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|None|
|ActionThrottledEvents|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|None|
|TriggersStarted|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|None|
|TriggersCompleted|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|None|
|TriggersSucceeded|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|None|
|TriggersFailed|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|None|
|TriggersSkipped|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|None|
|TriggersFired|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|None|
|TriggerLatency|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|None|
|TriggerFireLatency|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|None|
|TriggerSuccessLatency|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|None|
|TriggerThrottledEvents|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|None|
|BillableActionExecutions|Számlázható műveletek végrehajtása|Darabszám|Összesen|A számlázott munkafolyamat-műveletek végrehajtásának száma.|None|
|BillableTriggerExecutions|Számlázható trigger-végrehajtások|Darabszám|Összesen|A számlázott munkafolyamat-trigger-végrehajtások száma.|None|
|TotalBillableExecutions|Számlázandó végrehajtások összesen|Darabszám|Összesen|A számlázandó munkafolyamat-végrehajtások száma.|None|
|BillingUsageNativeOperation|A natív művelet végrehajtásához használt számlázási használat|Darabszám|Összesen|A számlázott natív művelet-végrehajtások száma.|None|
|BillingUsageStandardConnector|A standard szintű összekötők végrehajtásának számlázási használata|Darabszám|Összesen|A számlázott szabványos összekötő-végrehajtások száma.|None|
|BillingUsageStorageConsumption|Számlázási használat a tárolási felhasználás végrehajtásához|Darabszám|Összesen|A számlázható tárterület-használat végrehajtásának száma.|None|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RunsStarted|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|None|
|RunsCompleted|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|None|
|RunsSucceeded|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|None|
|RunsFailed|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|None|
|RunsCancelled|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|None|
|RunLatency|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|None|
|RunSuccessLatency|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|None|
|RunThrottledEvents|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|None|
|RunStartThrottledEvents|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|None|
|RunFailurePercentage|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|None|
|ActionsStarted|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|None|
|ActionsCompleted|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|None|
|ActionsSucceeded|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|None|
|ActionsFailed|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|None|
|ActionsSkipped|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|None|
|ActionLatency|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|None|
|ActionSuccessLatency|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|None|
|ActionThrottledEvents|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|None|
|TriggersStarted|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|None|
|TriggersCompleted|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|None|
|TriggersSucceeded|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|None|
|TriggersFailed|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|None|
|TriggersSkipped|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|None|
|TriggersFired|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|None|
|TriggerLatency|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|None|
|TriggerFireLatency|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|None|
|TriggerSuccessLatency|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|None|
|TriggerThrottledEvents|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|None|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|None|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|None|
|IntegrationServiceEnvironmentConnectorProcessorUsage|integrációs szolgáltatási környezet összekötő-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|None|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Összekötő memóriahasználat integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Megszakított futtatások|Megszakított futtatások|Darabszám|Összesen|A munkaterülethez megszakított futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|A kért futtatások megszakítása|A kért futtatások megszakítása|Darabszám|Összesen|A munkaterületre vonatkozó megszakítást kérő futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Befejezett futtatások|Befejezett futtatások|Darabszám|Összesen|A munkaterülethez sikeresen befejezett futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Sikertelen futtatások|Sikertelen futtatások|Darabszám|Összesen|Sikertelen futtatások száma ehhez a munkaterülethez|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Futtatások véglegesítése|Futtatások véglegesítése|Darabszám|Összesen|A munkaterülethez tartozó, véglegesítés alatt álló futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Nem válaszoló futtatások|Nem válaszoló futtatások|Darabszám|Összesen|A munkaterületre nem válaszoló futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Nem indult el a futtatások|Nem indult el a futtatások|Darabszám|Összesen|A munkaterülethez nem elindított futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Futtatások előkészítése|Futtatások előkészítése|Darabszám|Összesen|A munkaterületre előkészítés alatt álló futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Kiépítési futtatások|Kiépítési futtatások|Darabszám|Összesen|A munkaterülethez kiépített futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Várólistán lévő futtatások|Várólistán lévő futtatások|Darabszám|Összesen|A munkaterülethez várólistára helyezett futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Elindított futtatások|Elindított futtatások|Darabszám|Összesen|A munkaterülethez elindított futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Futtatások indítása|Futtatások indítása|Darabszám|Összesen|A munkaterülethez elindított futtatások száma|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Hibák|Hibák|Darabszám|Összesen|A munkaterületen futtatott hibák száma|Forgatókönyv|
|Figyelmeztetések|Figyelmeztetések|Darabszám|Összesen|A futtatási figyelmeztetések száma ebben a munkaterületen|Forgatókönyv|
|A modell regisztrálása sikerült|A modell regisztrálása sikerült|Darabszám|Összesen|A munkaterületen sikeres modell-regisztrációk száma|Forgatókönyv|
|A modell regisztrálása nem sikerült|A modell regisztrálása nem sikerült|Darabszám|Összesen|A munkaterületen meghiúsult modell-regisztrációk száma|Forgatókönyv, StatusCode|
|Modell-üzembehelyezés elindítva|Modell-üzembehelyezés elindítva|Darabszám|Összesen|A munkaterületen elindított modellek központi telepítésének száma|Forgatókönyv|
|Modell-üzembehelyezés sikerült|Modell-üzembehelyezés sikerült|Darabszám|Összesen|A munkaterületen sikeres központi telepítési modellek száma|Forgatókönyv|
|Modell-üzembehelyezés sikertelen|Modell-üzembehelyezés sikertelen|Darabszám|Összesen|A munkaterületen sikertelen telepítési modellek száma|Forgatókönyv, StatusCode|
|Csomópontok összesen|Csomópontok összesen|Darabszám|Átlag|A csomópontok száma összesen. Ez az összeg magában foglalja az aktív csomópontok, a tétlen csomópontok, a nem használható csomópontok, a Premepted csomópontok és a csomópontok elhagyását|Forgatókönyv, ClusterName|
|Aktív csomópontok|Aktív csomópontok|Darabszám|Átlag|Acitve-csomópontok száma Ezek azok a csomópontok, amelyek aktívan futtatnak egy feladatot.|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Tétlen csomópontok|Darabszám|Átlag|Az üresjárati csomópontok száma. Az üresjárati csomópontok azok a csomópontok, amelyek nem futtatnak feladatokat, de az elérhetővé tehetik az új feladatot.|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma Néhány feloldhatatlan probléma miatt nem használható csomópontok nem működőképesek. Az Azure újrahasznosítja ezeket a csomópontokat.|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma Ezek a csomópontok az alacsony prioritású csomópontok, amelyek a rendelkezésre álló csomópont-készletből származnak.|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Csomópontok elhagyása|Darabszám|Átlag|A csomópontok elhagyásának száma. A csomópontok elhagyják azokat a csomópontokat, amelyek éppen befejezték a feladatok feldolgozását, és az inaktív állapotba kerülnek.|Forgatókönyv, ClusterName|
|Magok összesen|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Aktív magok|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Üresjárati magok|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Használhatatlan magok|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Előzik magok|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Magok kihagyása|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtsága (%)|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Darabszám|Átlag|CPU (előzetes verzió)|Forgatókönyv, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Darabszám|Átlag|GPU (előzetes verzió)|Forgatókönyv, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Darabszám|Darabszám|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Mediaservices/streamingEndpoints

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve.|OutputFormat|
|SuccessE2ELatency|A Befejezés végének késése|Ezredmásodpercben|Átlag|A sikeres kérelmek átlagos késése ezredmásodpercben.|OutputFormat|
|Kérelmek|Kérelmek|Darabszám|Összesen|Továbbítási végpontra irányuló kérelmek.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AssetQuota|Eszköz kvótája|Darabszám|Átlag|Hány eszköz engedélyezett a jelenlegi Media Service-fiókhoz|None|
|AssetCount|Eszközök száma|Darabszám|Átlag|Az aktuális Media Service-fiókban már létrehozott eszközök száma|None|
|AssetQuotaUsedPercentage|Eszköz kvótájának kihasználtsága (%)|Százalék|Átlag|Az eszköz által használt százalék a jelenlegi Media Service-fiókban|None|
|ContentKeyPolicyQuota|Tartalmi kulcs házirend-kvótája|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó tartalmi kulcsokra vonatkozó házirendek száma|None|
|ContentKeyPolicyCount|Tartalmi kulcsokra vonatkozó szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejött a tartalmi kulcs házirendjeinek száma|None|
|ContentKeyPolicyQuotaUsedPercentage|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|Százalék|Átlag|A tartalmi kulcsra vonatkozó házirend százaléka a jelenlegi Media Service-fiókban|None|
|StreamingPolicyQuota|Streaming Policy-kvóta|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó folyamatos átviteli házirendek száma|None|
|StreamingPolicyCount|Folyamatos átviteli szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejöttek a folyamatos átviteli házirendek|None|
|StreamingPolicyQuotaUsedPercentage|Adatfolyam-házirend kvótájának kihasználtsága (%)|Százalék|Átlag|A streaming Policy használt százalék a jelenlegi Media Service-fiókban|None|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AssetsConverted|Átalakított eszközök|Darabszám|Összesen|Átalakított eszközök teljes száma|AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Aktív renderelési munkamenetek|Darabszám|Összesen|Aktív renderelési munkamenetek száma összesen|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/kötetek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageReadLatency|Olvasási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|None|
|AverageWriteLatency|Írási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Írási késleltetés átlagos száma ezredmásodpercben|None|
|VolumeLogicalSize|Kötet felhasznált mérete|Bájt|Átlag|A kötet logikai mérete (felhasznált bájtok)|None|
|VolumeSnapshotSize|Kötet pillanatképének mérete|Bájt|Átlag|A köteten található összes pillanatkép mérete|None|
|ReadIops|IOPS olvasása|CountPerSecond|Átlag|Olvasási/kimenő műveletek másodpercenként|None|
|WriteIops|IOPS írása|CountPerSecond|Átlag|Írási/kimenő műveletek másodpercenként|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|A kötet méretéhez lefoglalt készlet|Bájt|Átlag|A készlet lefoglalt felhasznált mérete|None|
|VolumePoolTotalLogicalSize|Készlet felhasznált mérete|Bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Eljuttatott bájtok|Bájt|Összesen|A hálózati adapter által eljuttatott bájtok száma|None|
|BytesReceivedRate|Fogadott bájtok száma|Bájt|Összesen|A hálózati adapter által fogadott bájtok száma|None|
|PacketsSentRate|Küldött csomagok|Darabszám|Összesen|A hálózati adapter által küldött csomagok száma|None|
|PacketsReceivedRate|Fogadott csomagok|Darabszám|Összesen|A hálózati adapter által fogadott csomagok száma|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipAvailability|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress,FrontendPort|
|DipAvailability|Állapot mintavételi állapota|Darabszám|Átlag|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Bájtok száma|Darabszám|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, irány|
|PacketCount|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SYNCount|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SnatConnectionCount|SNAT-kapcsolatok száma|Darabszám|Összesen|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Lefoglalt SNAT portok (előzetes verzió)|Darabszám|Összesen|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Használt SNAT-portok (előzetes verzió)|Darabszám|Összesen|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezési kötet|Darabszám|Összesen|A DNS-zónák számára kiszolgált lekérdezések száma|None|
|RecordSetCount|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy DNS-zónában|None|
|RecordSetCapacityUtilization|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|None|
|PacketsDroppedDDoS|Bejövő csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő csomagok eldobott DDoS|None|
|PacketsForwardedDDoS|Bejövő csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő csomagok továbbított DDoS|None|
|TCPPacketsInDDoS|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|None|
|TCPPacketsDroppedDDoS|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok eldobott DDoS|None|
|TCPPacketsForwardedDDoS|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok továbbított DDoS|None|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok DDoS|None|
|UDPPacketsDroppedDDoS|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok eldobott DDoS|None|
|UDPPacketsForwardedDDoS|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok továbbított DDoS|None|
|BytesInDDoS|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|None|
|BytesDroppedDDoS|Bejövő bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő bájtok elvetve DDoS|None|
|BytesForwardedDDoS|Bejövő bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő bájtok továbbított DDoS|None|
|TCPBytesInDDoS|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|None|
|TCPBytesDroppedDDoS|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|None|
|TCPBytesForwardedDDoS|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|None|
|UDPBytesInDDoS|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|None|
|UDPBytesDroppedDDoS|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|None|
|UDPBytesForwardedDDoS|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|None|
|IfUnderDDoSAttack|DDoS-támadás alatt vagy nem|Darabszám|Maximum|DDoS-támadás alatt vagy nem|None|
|DDoSTriggerTCPPackets|Bejövő TCP-csomagok a DDoS-mérséklés indításához|CountPerSecond|Maximum|Bejövő TCP-csomagok a DDoS-mérséklés indításához|None|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|None|
|DDoSTriggerSYNPackets|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|None|
|VipAvailability|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Átlagos IP-cím rendelkezésre állása időszakonként|Port|
|ByteCount|Bájtok száma|Darabszám|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|PacketCount|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|SynCount|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|A pingelések időpontjának kerekítése egy virtuális géphez|Ezredmásodpercben|Átlag|A célként megadott virtuális gépre eljuttatott pingelések menetének időpontja|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Sikertelen pingelések egy virtuális géphez|Százalék|Átlag|A hibás pingelések száma a cél virtuális gép összes elküldéses pingelésének százalékában|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ApplicationRuleHit|Alkalmazás-szabályok találatok száma|Darabszám|Összesen|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|NetworkRuleHit|Hálózati szabályok találatok száma|Darabszám|Összesen|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|
|FirewallHealth|Tűzfal állapota|Százalék|Átlag|Tűzfal állapota|Állapot, ok|
|DataProcessed|Feldolgozott adatfeldolgozás|Bájt|Összesen|A tűzfal által feldolgozott adatmennyiség teljes mennyisége|None|
|SNATPortUtilization|SNAT-portok kihasználtsága|Százalék|Átlag|SNAT-portok kihasználtsága|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Átviteli sebesség|Átviteli sebesség|BytesPerSecond|Átlag|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|None|
|UnhealthyHostCount|Nem kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Sérült háttérbeli gazdagépek száma|BackendSettingsPool|
|HealthyHostCount|Kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Az egészséges háttérbeli gazdagépek száma|BackendSettingsPool|
|TotalRequests|Összes kérelem|Darabszám|Összesen|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Percenkénti kérelmek száma kifogástalan állapotú gazdagépen|Darabszám|Átlag|Kérelmek átlagos száma percenként, a készletben lévő kifogástalan állapotú gazdagépek esetében|BackendSettingsPool|
|FailedRequests|Sikertelen kérelmek|Darabszám|Összesen|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|ResponseStatus|Válasz állapota|Darabszám|Összesen|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Összege|Aktuális kapcsolatok|Darabszám|Összesen|Application Gatewaysal létesített aktuális kapcsolatok száma|None|
|NewConnectionsPerSecond|Új kapcsolatok másodpercenként|CountPerSecond|Átlag|Új kapcsolatok másodpercenként létesített Application Gateway|None|
|CpuUtilization|Processzor kihasználtsága|Százalék|Átlag|A Application Gateway aktuális CPU-kihasználtsága|None|
|CapacityUnits|Aktuális kapacitási egységek|Darabszám|Átlag|Felhasznált kapacitási egységek|None|
|FixedBillableCapacityUnits|Fix számlázandó kapacitásegységek|Darabszám|Átlag|Minimálisan felszámított kapacitási egységek|None|
|EstimatedBilledCapacityUnits|Becsült számlázott kapacitási egységek|Darabszám|Átlag|A felszámított becsült kapacitási egységek|None|
|ComputeUnits|Aktuális számítási egységek|Darabszám|Átlag|Felhasznált számítási egységek|None|
|BackendResponseStatus|Háttérbeli válasz állapota|Darabszám|Összesen|A háttérbeli tagok által létrehozott HTTP-válasz kódok száma. Ez nem tartalmazza a Application Gateway által létrehozott válasz-kódokat.|BackendServer, Háttérkészletek, Backendhttpsetting értékre, HttpStatusGroup|
|TlsProtocol|Ügyfél TLS protokoll|Darabszám|Összesen|Azoknak a TLS-és nem TLS-kérelmeknek a száma, amelyek az ügyfél által kezdeményezett Application Gatewaykal létesített kapcsolatban állnak. A TLS protokoll terjesztésének megtekintéséhez szűrje a Dimension TLS protokollt.|Figyelő, TlsProtocol|
|BytesSent|Eljuttatott bájtok|Bájt|Összesen|A Application Gateway által az ügyfeleknek eljuttatott bájtok teljes száma|Figyelő|
|BytesReceived|Fogadott bájtok száma|Bájt|Összesen|A Application Gateway által az ügyfelektől fogadott bájtok teljes száma|Figyelő|
|ClientRtt|Ügyfél-RTT|Ezredmásodpercben|Átlag|Az ügyfelek és a Application Gateway közötti átlagos menetidő. Ez a metrika azt jelzi, hogy mennyi időt vesz igénybe a kapcsolatok és a visszaküldési visszaigazolás|Figyelő|
|ApplicationGatewayTotalTime|Application Gateway teljes idő|Ezredmásodpercben|Átlag|A kérelem feldolgozásának és a válasz elküldésekor elvégezhető átlagos idő. Ez az időszak átlaga, amikor a Application Gateway megkapja egy HTTP-kérelem első bájtját, amikor a válasz küldése művelet befejeződik. Fontos megjegyezni, hogy ez általában a Application Gateway feldolgozási időt, az időpontot, amikor a kérés és a válasz csomagjai a hálózaton keresztül utaznak, valamint a háttér-kiszolgáló válaszának időpontját.|Figyelő|
|BackendConnectTime|Háttérbeli kapcsolat ideje|Ezredmásodpercben|Átlag|A háttér-kiszolgálóval létesített kapcsolatok létrehozásához töltött idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendFirstByteResponseTime|Háttérbeli első bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz fejléc első bájtjának fogadása között eltelt idő, a háttér-kiszolgáló feldolgozási idejének megközelítő időpontja|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendLastByteResponseTime|Háttérbeli utolsó bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz törzse utolsó bájtjának fogadása között eltelt idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|MatchedCount|Webalkalmazási tűzfal v1 teljes szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal v1 – a bejövő forgalomra vonatkozó összes szabály eloszlása|Szerepkörcsoportot, RuleId|
|BlockedCount|Webalkalmazási tűzfal v1 letiltott kérelmek szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal v1 letiltott kérelmek szabályának eloszlása|Szerepkörcsoportot, RuleId|
|BlockedReqCount|Webalkalmazási tűzfal v1 – letiltott kérelmek száma|Darabszám|Összesen|Webalkalmazási tűzfal v1 – letiltott kérelmek száma|None|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AverageBandwidth|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|None|
|P2SBandwidth|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|None|
|P2SConnectionCount|P2S-kapcsolatok száma|Darabszám|Maximum|Átjáró pont – hely kapcsolatának száma|Protocol (Protokoll)|
|TunnelAverageBandwidth|Bújtatási sávszélesség|BytesPerSecond|Átlag|Egy alagút átlagos sávszélessége (bájt/s)|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Alagút kimenő bájtjai|Bájt|Összesen|Egy alagút kimenő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Alagutak bejövő bájtjai|Bájt|Összesen|Alagút bejövő bájtjai|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Alagút kimenő csomagjai|Darabszám|Összesen|Alagút kimenő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Bújtatási bejövő csomagok|Darabszám|Összesen|Alagút bejövő csomagjainak száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Alagút kimenő terminálszolgáltatási eltérési csomagjainak eldobása|Darabszám|Összesen|Kimenő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Az alagút bejövő HOZZÁFÉRÉSe nem egyezik a csomagok eldobásával|Darabszám|Összesen|Bejövő csomagok eldobásának száma a forgalmi választótól eltérő alagút esetén|Kapcsolatnév, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Darabszám|Átlag|Rx-fény szintje dBm-ben|Hivatkozás, sáv|
|TxLightLevel|TxLightLevel|Darabszám|Átlag|TX-fény szintje dBm-ben|Hivatkozás, sáv|
|AdminState|AdminState|Darabszám|Átlag|A port rendszergazdai állapota|Hivatkozás|
|LineProtocol|LineProtocol|Darabszám|Átlag|A port protokolljának állapota|Hivatkozás|
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
|QpsByEndpoint|Visszaadott végponti lekérdezések|Darabszám|Összesen|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végponti állapot végpont szerint|Darabszám|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|



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
|RequestCount|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|RequestSize|Kérelem mérete|Bájt|Összesen|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|ResponseSize|Válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BillableResponseSize|Számlázható válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött számlázandó bájtjainak (minimális 2KB) száma az ügyfeleknek.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendRequestCount|Háttérbeli kérelmek száma|Darabszám|Összesen|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Háttérbeli kérelmek késése|Ezredmásodpercben|Átlag|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Háttérrendszer|
|TotalLatency|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Háttér állapotának százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|WebApplicationFirewallRequestCount|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume|Lekérdezési kötet|Darabszám|Összesen|Egy saját DNS zónában kiszolgált lekérdezések száma|None|
|RecordSetCount|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy saját DNS zónában|None|
|RecordSetCapacityUtilization|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy saját DNS zóna által használt rekordazonosító-kapacitás százalékos aránya|None|
|VirtualNetworkLinkCount|Virtual Network kapcsolatok száma|Darabszám|Maximum|saját DNS zónához csatolt virtuális hálózatok száma|None|
|VirtualNetworkLinkCapacityUtilization|Virtual Network kapcsolat kapacitásának kihasználtsága|Százalék|Maximum|saját DNS zóna által használt Virtual Network kapcsolat kapacitásának százaléka|None|
|VirtualNetworkWithRegistrationLinkCount|Virtual Network regisztrációs kapcsolatok száma|Darabszám|Maximum|Egy saját DNS zónához kapcsolt virtuális hálózatok száma, amelyeken engedélyezve van az automatikus regisztráció|None|
|VirtualNetworkWithRegistrationCapacityUtilization|Virtual Network regisztrációs kapcsolat kapacitásának kihasználtsága|Százalék|Maximum|Virtual Network-hivatkozás százaléka, amely egy saját DNS zóna által használt automatikus regisztrációs kapacitással rendelkezik|None|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|regisztráció. All|Regisztrációs műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |None|
|regisztráció. Create|Regisztráció-létrehozási műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs létrehozás száma.|None|
|regisztráció. frissítés|Regisztrációs frissítési műveletek|Darabszám|Összesen|A sikeres regisztrációs frissítések száma.|None|
|regisztráció. Get|Regisztrálási olvasási műveletek|Darabszám|Összesen|A sikeres regisztrációs lekérdezések száma.|None|
|regisztráció. Delete|Regisztrációs törlési műveletek|Darabszám|Összesen|A regisztráció sikeres törléseinak száma.|None|
|bejövő|Bejövő üzenetek|Darabszám|Összesen|A sikeres küldési API-hívások száma. |None|
|bejövő. ütemezett|Ütemezett leküldéses értesítések elküldve|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|None|
|bejövő. ütemezett. Mégse|Ütemezett leküldéses értesítések megszakítva|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|None|
|ütemezett. függőben|Függőben lévő ütemezett értesítések|Darabszám|Összesen|Függőben lévő ütemezett értesítések|None|
|a telepítés. All|Telepítési felügyeleti műveletek|Darabszám|Összesen|Telepítési felügyeleti műveletek|None|
|telepítés. Get|Telepítési műveletek beolvasása|Darabszám|Összesen|Telepítési műveletek beolvasása|None|
|telepítési. upsert|Telepítési műveletek létrehozása vagy frissítése|Darabszám|Összesen|Telepítési műveletek létrehozása vagy frissítése|None|
|Installation. patch|Javítások telepítési műveletei|Darabszám|Összesen|Javítások telepítési műveletei|None|
|telepítés. Törlés|Telepítési műveletek törlése|Darabszám|Összesen|Telepítési műveletek törlése|None|
|kimenő. allpns. sikeres|Sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő. allpns. invalidpayload|Hasznos adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|None|
|kimenő. allpns. pnserror|Külső értesítési rendszerhibák|Darabszám|Összesen|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|None|
|kimenő. allpns. channelerror|Csatorna hibái|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|None|
|kimenő. allpns. badorexpiredchannel|Rossz vagy lejárt csatorna-hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|None|
|kimenő. wns. sikeres|WNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő. wns. invalidcredentials|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|None|
|kimenő. wns. badchannel|WNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|None|
|kimenő. wns. expiredchannel|WNS lejárt csatorna hibája|Darabszám|Összesen|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapota: 410 elveszett).|None|
|kimenő. wns. szabályozva|WNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapota: 406, nem fogadható el).|None|
|kimenő. wns. tokenproviderunreachable|WNS-hitelesítési hibák (nem érhető el)|Darabszám|Összesen|A Windows Live nem érhető el.|None|
|kimenő. wns. invalidtoken|WNS-hitelesítési hibák (érvénytelen token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvénytelen (WNS-állapot: 401 jogosulatlan).|None|
|kimenő. wns. wrongtoken|WNS-hitelesítési hibák (hibás token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|None|
|kimenő. wns. invalidnotificationformat|WNS – érvénytelen értesítési formátum|Darabszám|Összesen|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|None|
|kimenő. wns. invalidnotificationsize|A WNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az értesítési tartalom túl nagy (WNS állapot: 413).|None|
|kimenő. wns. channelthrottled|WNS csatorna szabályozása|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-NotificationStatus: channelThrottled).|None|
|kimenő. wns. channeldisconnected|WNS csatorna leválasztva|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva).|None|
|kimenő. wns. Dropped|WNS eldobott értesítések|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|None|
|kimenő. wns. pnserror|WNS hibák|Darabszám|Összesen|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|None|
|kimenő. wns. authenticationerror|WNS-hitelesítési hibák|Darabszám|Összesen|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|None|
|kimenő. apns. sikeres|APNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő. apns. invalidcredentials|APNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő. apns. badchannel|APNS hibás csatorna hiba|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS: 8).|None|
|kimenő. apns. expiredchannel|APNS lejárt csatorna hibája|Darabszám|Összesen|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|None|
|kimenő. apns. invalidnotificationsize|A APNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (APNS-állapotkód: 7).|None|
|kimenő. apns. pnserror|APNS hibák|Darabszám|Összesen|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|None|
|kimenő. GCM. sikeres|GCM sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő. GCM. invalidcredentials|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő. GCM. badchannel|GCM hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM-eredmény: érvénytelen regisztráció).|None|
|kimenő. GCM. expiredchannel|GCM lejárt csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM-eredmény: NotRegistered).|None|
|kimenő. GCM. szabályozva|GCM-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM: 501-599 vagy eredmény: nem érhető el).|None|
|kimenő. GCM. invalidnotificationformat|GCM – érvénytelen értesítési formátum|Darabszám|Összesen|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|None|
|kimenő. GCM. invalidnotificationsize|A GCM érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|None|
|kimenő. GCM. wrongchannel|GCM rossz csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM-eredmény: InvalidPackageName).|None|
|kimenő. GCM. pnserror|GCM hibák|Darabszám|Összesen|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|None|
|kimenő. GCM. authenticationerror|GCM-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM result: MismatchedSenderId).|None|
|kimenő. mpns. sikeres|MPNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő. mpns. invalidcredentials|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő. mpns. badchannel|MPNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|None|
|kimenő. mpns. szabályozva|MPNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem fogadható el).|None|
|kimenő. mpns. invalidnotificationformat|MPNS – érvénytelen értesítési formátum|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|None|
|kimenő. mpns. channeldisconnected|MPNS csatorna leválasztva|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (MPNS-állapot: 412 nem található).|None|
|kimenő. mpns. Dropped|MPNS eldobott értesítések|Darabszám|Összesen|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X-NotificationStatus: QueueFull vagy letiltva).|None|
|kimenő. mpns. pnserror|MPNS hibák|Darabszám|Összesen|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|None|
|kimenő. mpns. authenticationerror|MPNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|notificationhub. leküldések|Minden kimenő értesítés|Darabszám|Összesen|Az értesítési központ összes kimenő értesítése|None|
|bejövő. ALL. requests|Minden bejövő kérelem|Darabszám|Összesen|Értesítési központ összes bejövő kérelme|None|
|bejövő. ALL. failedrequests|Minden bejövő sikertelen kérelem|Darabszám|Összesen|Értesítési központ bejövő sikertelen kéréseinek száma|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_ szabad inode (%)|Szabad inode%-ban|Darabszám|Átlag|Average_ szabad inode (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Szabad terület (%)|Darabszám|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ használt inode|Felhasznált inode%-ban|Darabszám|Átlag|Average_ használt inode|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált terület%-ban|Felhasznált terület (%)|Darabszám|Átlag|Average_ felhasznált terület%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási sebesség (bájt/s) Average_Disk|Lemez olvasási sebessége (bájt/s)|Darabszám|Átlag|Olvasási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Olvasási sebesség (lemez/mp)|Darabszám|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Lemez átvitele másodpercenként|Darabszám|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írási sebesség (bájt/s) Average_Disk|Lemez írási sebessége (bájt/s)|Darabszám|Átlag|Írási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Írási sebesség (írás/mp)|Darabszám|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Szabad terület (MB)|Darabszám|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Logikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Logical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rendelkezésre álló memória Average_%-ban|Rendelkezésre álló memória%-ban|Darabszám|Átlag|Rendelkezésre álló memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ rendelkezésre álló szabad hely a lapozófájlban|Rendelkezésre álló swap-terület (%)|Darabszám|Átlag|Average_ rendelkezésre álló szabad hely a lapozófájlban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Felhasznált memória Average_%-ban|Felhasznált memória (%)|Darabszám|Átlag|Felhasznált memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált lapozófájl-terület|Felhasznált swap-terület%-ban|Darabszám|Átlag|Average_ felhasznált lapozófájl-terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB memória|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban való swap|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB-ban való swap|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Page másodpercenként|Olvasott lap/mp|Darabszám|Átlag|Olvasási Average_Page másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Page|Írási idő/mp|Darabszám|Átlag|Írás/mp Average_Page|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pages előállított/másodperc|Lap/mp|Darabszám|Átlag|Average_Pages előállított/másodperc|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used MB-ban felcserélt terület|Felhasznált memória (MB) – lapozófájl|Darabszám|Átlag|Average_Used MB-ban felcserélt terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória MB-ban|Felhasznált memória (MB)|Darabszám|Átlag|Average_Used memória MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított bájtok|Továbbított bájtok összesen|Darabszám|Átlag|Average_Total továbbított bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Total bájtok száma|Fogadott bájtok összesen|Darabszám|Átlag|Fogadott Average_Total bájtok száma|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total bájtok|Bájtok összesen|Darabszám|Átlag|Average_Total bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított csomagok|Továbbított csomagok összesen|Darabszám|Átlag|Average_Total továbbított csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total fogadott csomagok|Fogadott csomagok összesen|Darabszám|Átlag|Average_Total fogadott csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rx-hibák Average_Total|Rx-hibák összesen|Darabszám|Átlag|Rx-hibák Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total TX-hibák|TX-hibák összesen|Darabszám|Átlag|Average_Total TX-hibák|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Ütközések Average_Total|Ütközések összesen|Darabszám|Átlag|Ütközések Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Átlagos írási idő (mp/olvasás)|Darabszám|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Lemez (mp/átvitel)|Átlagos műveleti idő (mp/átvitel)|Darabszám|Átlag|Average_Avg. Lemez (mp/átvitel)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Átlagos írási idő (mp/írás)|Darabszám|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Fizikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Physical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|PCT rendszerjogosultságú idő|Darabszám|Átlag|Average_Pct emelt szintű idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|PCT felhasználói idő|Darabszám|Átlag|Average_Pct felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Felhasznált memória (kilobájt)|Darabszám|Átlag|Average_Used memória kilobájtban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Virtuális megosztott memória|Darabszám|Átlag|Average_Virtual megosztott memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% DPC idő|% DPC idő|Darabszám|Átlag|Average_% DPC idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ üresjárati idő%-ban|Üresjáratban eltöltött időhányad (%)|Darabszám|Átlag|Average_ üresjárati idő%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Megszakítási idő%-ban|Darabszám|Átlag|Average_%-os megszakítási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% IO várakozási idő|I/o várakozási idő%-ban|Darabszám|Átlag|Average_% IO várakozási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os szép idő|% Nice idő|Darabszám|Átlag|Average_%-os szép idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|%-Os privilegizált idő|Darabszám|Átlag|Average_%-os privilegizált idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|A processzor kihasználtsága (%)|Darabszám|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Felhasználói idő%-ban|Darabszám|Átlag|Average_%-os felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fizikai memória Average_Free|Szabad fizikai memória|Darabszám|Átlag|Fizikai memória Average_Free|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free lemezterület a Lapozófájlokban|Szabad terület a Lapozófájlokban|Darabszám|Átlag|Average_Free lemezterület a Lapozófájlokban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free virtuális memória|Szabad virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processes|Folyamatok|Darabszám|Átlag|Average_Processes|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Lapozófájlokban tárolt méret|Darabszám|Átlag|Lapozófájlokban tárolt Average_Size|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Uptime|Üzemidő|Darabszám|Átlag|Average_Uptime|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Users|Felhasználók|Darabszám|Átlag|Average_Users|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Current a lemez várólistájának hossza|Lemez aktuális várólistájának hossza|Darabszám|Átlag|Average_Current a lemez várólistájának hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Előjegyzett memória%-ban használatban|Darabszám|Átlag|Average_%-os előjegyzett bájtok használatban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Fogadott bájtok/s|Darabszám|Átlag|Fogadott Average_Bytes/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Küldési Average_Bytes másodpercenként|Küldési sebesség (bájt/s)|Darabszám|Átlag|Küldési Average_Bytes másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Összes bájt/mp|Darabszám|Átlag|Average_Bytes összesen/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Processzor-várólista hossza|Darabszám|Átlag|Average_Processor várólista hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Szívverés|Szívverés|Darabszám|Összesen|Szívverés|Számítógép, OSType, verzió, SourceComputerId|
|Frissítés|Frissítés|Darabszám|Átlag|Frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|
|Esemény|Esemény|Darabszám|Átlag|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PrefixLatency|Előtag-késés|Ezredmásodpercben|Átlag|Medián előtag késése|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/társaik

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Munkamenet rendelkezésre állása v4|Százalék|Átlag|A v4-munkamenet rendelkezésre állása|ConnectionId|
|SessionAvailabilityV6|Munkamenet rendelkezésre állása v6|Százalék|Átlag|A V6-munkamenet rendelkezésre állása|ConnectionId|
|IngressTrafficRate|Bejövő forgalom aránya|BitsPerSecond|Átlag|Bejövő forgalom sebessége bit/másodpercben|ConnectionId|
|EgressTrafficRate|Kimenő forgalom aránya|BitsPerSecond|Átlag|Kimenő forgalom sebessége (bit/mp)|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration|Lekérdezés időtartama|Ezredmásodpercben|Átlag|DAX-lekérdezés időtartama az utolsó intervallumban|Nincsenek méretek|
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|Nincsenek méretek|
|qpu_high_utilization_metric|QPU magas kihasználtság|Darabszám|Összesen|QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0|Nincsenek méretek|
|memory_metric|Memory (Memória)|Bájt|Átlag|Memory. 0-3 GB-os tartomány a1, 0-5 GB az a2-hez, 0-10 GB az a3-hoz, 0-25 GB a4-hez, 0-50 GB az A5-hez és az A6-os 0-100 GB-hoz|Nincsenek méretek|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|Nincsenek méretek|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/fiókok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Eszközök megoszlása besorolás szerint|Darabszám|Összesen|Azt jelzi, hogy hány eszköz van hozzárendelve bizonyos besorolással, azaz hogy az adott címkével vannak besorolva.|Besorolás, forrás, ResourceId|
|AssetDistributionByStorageType|Eszközök eloszlása tárolási típus szerint|Darabszám|Összesen|Meghatározza az adott tárolási típussal rendelkező eszközök számát.|StorageType, ResourceId|
|CatalogActiveUsers|Napi aktív felhasználók|Darabszám|Összesen|Napi aktív felhasználók száma|ResourceId|
|CatalogUsage|Használat eloszlása művelet szerint|Darabszám|Összesen|Adja meg, hogy a felhasználó hány műveletet végez a katalógusban, pl.: hozzáférés, keresés, szószedet.|Művelet, ResourceId|
|NumberOfAssetsWithClassifications|Legalább egy besorolású eszközök száma|Darabszám|Átlag|Meghatározza a legalább egy címkével rendelkező eszközök számát.|ResourceId|
|ScanCancelled|Vizsgálat megszakítva|Darabszám|Összesen|A megszakított ellenőrzések számát jelzi.|ResourceId|
|ScanCompleted|A vizsgálat befejeződött|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma sikeresen befejeződött.|ResourceId|
|ScanFailed|Sikertelen vizsgálat|Darabszám|Összesen|Azt jelzi, hogy a vizsgálatok száma nem sikerült.|ResourceId|
|ScanTimeTaken|Vizsgálat ideje elvégezve|Másodperc|Összesen|A teljes vizsgálati időt jelzi másodpercben.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Figyelőkapcsolatokra – sikeres|Figyelőkapcsolatokra – sikeres|Darabszám|Összesen|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|EntityName, Operationresult tevékenységen|
|Figyelőkapcsolatokra – vonatkozó ügyfélhibái|Figyelőkapcsolatokra – vonatkozó ügyfélhibái|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName, Operationresult tevékenységen|
|Figyelőkapcsolatokra – ServerError|Figyelőkapcsolatokra – ServerError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|EntityName, Operationresult tevékenységen|
|Feladói kapcsolatokra – sikeres|Feladói kapcsolatokra – sikeres|Darabszám|Összesen|A Microsoft. Relay sikeres feladói kapcsolatokra.|EntityName, Operationresult tevékenységen|
|Feladói kapcsolatokra – vonatkozó ügyfélhibái|Feladói kapcsolatokra – vonatkozó ügyfélhibái|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName, Operationresult tevékenységen|
|Feladói kapcsolatokra – ServerError|Feladói kapcsolatokra – ServerError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra ServerError.|EntityName, Operationresult tevékenységen|
|Figyelőkapcsolatokra – TotalRequests|Figyelőkapcsolatokra – TotalRequests|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra összesen.|EntityName|
|Feladói kapcsolatokra – TotalRequests|Feladói kapcsolatokra – TotalRequests|Darabszám|Összesen|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|EntityName|
|Aktív kapcsolatai|Aktív kapcsolatai|Darabszám|Összesen|A Microsoft. Relay aktív kapcsolatai összesen.|EntityName|
|ActiveListeners|ActiveListeners|Darabszám|Összesen|A Microsoft. Relay ActiveListeners összesen.|EntityName|
|BytesTransferred|BytesTransferred|Darabszám|Összesen|A Microsoft. Relay BytesTransferred összesen.|EntityName|
|Bontásai|Bontásai|Darabszám|Összesen|A Microsoft. Relay bontásai összesen.|EntityName|
|SenderDisconnects|SenderDisconnects|Darabszám|Összesen|A Microsoft. Relay SenderDisconnects összesen.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SearchLatency|Keresési késés|Másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|None|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|CountPerSecond|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|None|
|ThrottledSearchQueriesPercentage|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SuccessfulRequests|Sikeres kérések|Darabszám|Összesen|Névtér összes sikeres kérelme|EntityName, Operationresult tevékenységen|
|Kiszolgálóhibái|Kiszolgálói hibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. ServiceBus esetében.|EntityName, Operationresult tevékenységen|
|UserErrors|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. ServiceBus felhasználói hibái.|EntityName, Operationresult tevékenységen|
|ThrottledRequests|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. ServiceBus által szabályozott kérelmek.|EntityName, Operationresult tevékenységen|
|IncomingRequests|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő kérései.|EntityName|
|IncomingMessages|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő üzenetei.|EntityName|
|OutgoingMessages|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus kimenő üzenetei.|EntityName|
|Aktív kapcsolatai|Aktív kapcsolatai|Darabszám|Összesen|A Microsoft. ServiceBus összes aktív kapcsolata.|None|
|ConnectionsOpened|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus megnyitott kapcsolatainak száma.|EntityName|
|ConnectionsClosed|A kapcsolatok lezárva.|Darabszám|Átlag|A Microsoft. ServiceBus lezárt kapcsolatainak száma.|EntityName|
|Méret|Méret|Bájt|Átlag|Várólista/témakör mérete bájtban.|EntityName|
|Üzenetek|Üzenetsor vagy témakör üzeneteinek száma.|Darabszám|Átlag|Üzenetsor vagy témakör üzeneteinek száma.|EntityName|
|ActiveMessages|Üzenetsor vagy témakör aktív üzeneteinek száma.|Darabszám|Átlag|Üzenetsor vagy témakör aktív üzeneteinek száma.|EntityName|
|DeadletteredMessages|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|Darabszám|Átlag|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|EntityName|
|ScheduledMessages|Az üzenetsor/témakör ütemezett üzeneteinek száma.|Darabszám|Átlag|Az üzenetsor/témakör ütemezett üzeneteinek száma.|EntityName|
|NamespaceCpuUsage|CPU|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája.|Replika|
|NamespaceMemoryUsage|Memóriahasználat|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma.|Replika|
|CPUXNS|CPU (elavult)|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája. Ez a metrika depricated. Használja helyette a CPU-metrikát (NamespaceCpuUsage).|Replika|
|WSXNS|Memóriahasználat (elavult)|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma. Ez a metrika elavult. Használja helyette a memóriahasználat (NamespaceMemoryUsage) metrikáját.|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/alkalmazások

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Darabszám|Átlag|A tárolóhoz a Millet magokban lefoglalt CPU|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bájt|Átlag|A tároló számára lefoglalt memória (MB)|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Darabszám|Átlag|Tényleges CPU-használat a Millon magokban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|ServiceStatus|ServiceStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, állapot, szolgáltatásnév|
|ServiceReplicaStatus|ServiceReplicaStatus|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, status, szolgáltatásnév, ServiceReplicaName|
|Tároló állapota:|Tároló állapota:|Darabszám|Átlag|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName, állapot|
|RestartCount|RestartCount|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, status, szolgáltatásnév, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ConnectionCount|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|MessageCount|Üzenetek száma|Darabszám|Összesen|Az üzenetek teljes mennyisége.|None|
|InboundTraffic|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|None|
|OutboundTraffic|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|None|
|UserErrors|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|None|
|SystemErrors|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|None|



## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|None|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|None|
|log_write_percent|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|None|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|None|
|storage|Felhasznált adatterület|Bájt|Maximum|Felhasznált adatterület. Az adattárházak esetében nem alkalmazható.|None|
|connection_successful|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|None|
|connection_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|blocked_by_firewall|Tűzfal blokkolja|Darabszám|Összesen|Tűzfal blokkolja|None|
|holtpont|Holtpontok|Darabszám|Összesen|Holtpontok. Az adattárházak esetében nem alkalmazható.|None|
|storage_percent|Felhasznált adatterület százalékos aránya|Százalék|Maximum|Az adatterület felhasznált százaléka százalékban. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|None|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP-tárolási százalék. Az adattárházak esetében nem alkalmazható.|None|
|workers_percent|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|None|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|None|
|dtu_limit|DTU korlátja|Darabszám|Átlag|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|None|
|dtu_used|Használt DTU|Darabszám|Átlag|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|None|
|cpu_limit|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|None|
|cpu_used|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|None|
|dwu_limit|DWU korlátja|Darabszám|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|None|
|dwu_consumption_percent|DWU százalékos aránya|Százalék|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|None|
|dwu_used|Használt DWU|Darabszám|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|None|
|cache_hit_percent|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|None|
|cache_used_percent|Gyorsítótár használt százaléka|Százalék|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|None|
|sqlserver_process_core_percent<sup>1</sup> |SQL Server Process Core százalék|Százalék|Maximum|A SQL Server folyamat CPU-kihasználtságának százalékos értéke az operációs rendszer által mért érték alapján.|None|
|sqlserver_process_memory_percent<sup>1</sup> |SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|A SQL Serveri folyamat memóriahasználat százalékát az operációs rendszer méri.|None|
|tempdb_data_size<sup>2</sup> |Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|Tempdb az adatfájl mérete kilobájtban.|None|
|tempdb_log_size<sup>2</sup> |Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|Tempdb-naplófájl mérete (kilobájt)|None|
|tempdb_log_used_percent<sup>2</sup> |Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van.|None|
|local_tempdb_usage_percent|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|None|
|app_cpu_billed|Az alkalmazás CPU-számlázása|Darabszám|Összesen|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|app_cpu_percent|Alkalmazás CPU-aránya|Százalék|Átlag|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|app_memory_percent|Az alkalmazás memóriájának százaléka|Százalék|Átlag|Az alkalmazás memóriájának százalékos aránya. A kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adattárolás. Az adattárházak esetében nem alkalmazható.|None|
|memory_usage_percent|Memória százaléka|Százalék|Maximum|Memória százalékos aránya Csak az adattárházak esetében érvényes.|None|
|dw_backup_size_gb|Adattárolás mérete|Darabszám|Összesen|Az adattárolási méret az adatai és a tranzakciónapló méretétől áll. A metrika a számla "Storage" részébe kerül. Csak az adattárházak esetében érvényes.|None|
|dw_snapshot_size_gb|Pillanatkép-tároló mérete|Darabszám|Összesen|A pillanatképek tárolási mérete a pillanatképek által a felhasználó által meghatározott és az Automatikus visszaállítási pontok létrehozásához rögzített növekményes módosítások mérete. A metrika a számla "Storage" részébe kerül. Csak az adattárházak esetében érvényes.|None|
|dw_geosnapshot_size_gb|Vész-helyreállítási tároló mérete|Darabszám|Összesen|A vész-helyreállítási tároló mérete "vész-helyreállítási tárolóként" jelenik meg a számlán. Csak az adattárházak esetében érvényes.|None|
|wlg_allocation_relative_to_system_percent|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások a teljes rendszerterhelési csoporthoz viszonyított százalékos aránya. Csak az adattárházak esetében érvényes.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Munkaterhelés-csoport lefoglalása a Cap-erőforrás százaléka alapján|Százalék|Maximum|Az erőforrások megadott százalékos aránya a munkaterhelési csoportok meghatározott erőforrásaihoz viszonyítva. Csak az adattárházak esetében érvényes.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|Aktív lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName,IsUserDefined|
|active_queries|Aktív lekérdezések|Darabszám|Összesen|Aktív lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|None|
|queued_queries|Várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|None|
|wlg_active_queries_timeouts|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Azok a lekérdezések, amelyek túllépték a munkaterhelés-csoport időtúllépését. Csak az adattárházak esetében érvényes.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Effektív minimális erőforrás százaléka|Százalék|Maximum|A munkaterhelés-csoport számára fenntartott és elkülönített erőforrások minimális százalékos aránya, a szolgáltatási szint minimumának figyelembevételével. Csak az adattárházak esetében érvényes.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport számára engedélyezett erőforrások százalékos arányának korlátozása, figyelembe véve a más munkaterhelés-csoportok számára kiosztott tényleges minimális erőforrás-százalékos arányt. Csak az adattárházak esetében érvényes.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Teljes biztonsági mentési tár mérete|Bájt|Maximum|Összesített teljes biztonsági mentési tár mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|None|
|diff_backup_size_bytes|Különbözeti biztonsági másolatok tárolási mérete|Bájt|Maximum|Kumulatív különbözeti biztonsági másolati tárterület mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|None|
|log_backup_size_bytes|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő napló biztonsági mentési tárterületének mérete. A virtuális mag-alapú és a nagy kapacitású adatbázisokra vonatkozik.|None|
|snapshot_backup_size_bytes|Pillanatkép biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő pillanatkép biztonsági mentési tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|None|
|base_blob_size_bytes|Az alap blob tárterületének mérete|Bájt|Maximum|Az alap blob tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|None|

<sup>1</sup> ez a metrika a virtuális mag beszerzési modellt használó adatbázisok számára érhető el, 2 virtuális mag és magasabb, vagy 200 DTU, valamint a DTU-alapú vásárlási modellek esetében magasabb. 

<sup>2</sup> ez a metrika a virtuális mag beszerzési modellt használó adatbázisok számára érhető el, 2 virtuális mag és magasabb, vagy 200 DTU és magasabb DTU-alapú vásárlási modellekhez. Ez a metrika jelenleg nem érhető el nagy kapacitású-adatbázisokhoz vagy adatraktárokhoz.

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
|eDTU_limit|eDTU korlátja|Darabszám|Átlag|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|None|
|storage_limit|Az adatmaximális méret|Bájt|Átlag|Az adatmaximális méret|None|
|eDTU_used|használt eDTU|Darabszám|Átlag|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|None|
|database_eDTU_used|használt eDTU|Darabszám|Átlag|használt eDTU|DatabaseResourceId|
|storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|None|
|database_storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId|
|xtp_storage_percent|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP tárolási százaléka|None|
|cpu_limit|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|None|
|database_cpu_limit|CPU-korlát|Darabszám|Átlag|CPU-korlát|DatabaseResourceId|
|cpu_used|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|None|
|database_cpu_used|Felhasznált CPU|Darabszám|Átlag|Felhasznált CPU|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|SQL Server Process Core százalék|Százalék|Maximum|A SQL Server folyamat CPU-kihasználtságának százalékos értéke az operációs rendszer által mért érték alapján. A rugalmas készletekre vonatkozik. |None|
|sqlserver_process_memory_percent<sup>1</sup>|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|A SQL Serveri folyamat memóriahasználat százalékát az operációs rendszer méri. A rugalmas készletekre vonatkozik. |None|
|tempdb_data_size<sup>2</sup>|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|Tempdb az adatfájl mérete kilobájtban.|None|
|tempdb_log_size<sup>2</sup>|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|Tempdb-naplófájl mérete (kilobájt) |None|
|tempdb_log_used_percent<sup>2</sup>|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van.|None|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|None|
|database_allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|DatabaseResourceId|
|allocated_data_storage_percent|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|None|

<sup>1</sup> ez a metrika a virtuális mag beszerzési modellt használó adatbázisok számára érhető el, 2 virtuális mag és magasabb, vagy 200 DTU, valamint a DTU-alapú vásárlási modellek esetében magasabb. 

<sup>2</sup> ez a metrika a virtuális mag beszerzési modellt használó adatbázisok számára érhető el, 2 virtuális mag és magasabb, vagy 200 DTU és magasabb DTU-alapú vásárlási modellekhez. Ez a metrika jelenleg nem érhető el nagy kapacitású-adatbázisokhoz.


## <a name="microsoftsqlservers"></a>Microsoft. SQL/kiszolgálók

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|ElasticPoolResourceId|
|database_storage_used|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Használt DTU|Darabszám|Átlag|Használt DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|virtual_core_count|Virtuális mag száma|Darabszám|Átlag|Virtuális mag száma|None|
|avg_cpu_percent|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|None|
|reserved_storage_mb|Tárterület fenntartva|Darabszám|Átlag|Tárterület fenntartva|None|
|storage_space_used_mb|Felhasznált tárterület|Darabszám|Átlag|Felhasznált tárterület|None|
|io_requests|IO-kérelmek száma|Darabszám|Átlag|IO-kérelmek száma|None|
|io_bytes_read|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|None|
|io_bytes_written|I/o-bájtok írása|Bájt|Átlag|I/o-bájtok írása|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|UsedCapacity|Felhasznált kapacitás|Bájt|Átlag|Fiókhoz használt kapacitás|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Blobok száma|Darabszám|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiók Blob service lévő tárolók száma.|None|
|IndexCapacity|Index kapacitása|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TableCapacity|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|None|
|TableCount|Táblák száma|Darabszám|Átlag|A Storage-fiók Table service található tábla száma.|None|
|TableEntityCount|Tábla entitások száma|Darabszám|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|FileCapacity|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileCount|Fájlok száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|None|
|FileShareSnapshotCount|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileShareQuota|Fájlmegosztás kvótájának mérete|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueueCapacity|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|None|
|QueueCount|Várólista száma|Darabszám|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|None|
|QueueMessageCount|Üzenetsor-üzenetek száma|Darabszám|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Sikeres kiszolgálói kérések késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/gyorsítótárak

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ClientIOPS|Ügyfél teljes IOPS|Darabszám|Átlag|A gyorsítótár által feldolgozott ügyféloldali fájlok műveleteinek aránya.|None|
|ClientLatency|Ügyfél átlagos késése|Ezredmásodpercben|Átlag|Az ügyfél-fájl műveleteinek átlagos késése a tárolási gyorsítótárban.|None|
|ClientReadIOPS|Ügyfél olvasási IOPS|CountPerSecond|Átlag|Az ügyfél olvasási műveletei másodpercenként.|None|
|ClientReadThroughput|Gyorsítótár átlagos olvasási átviteli sebessége|BytesPerSecond|Átlag|Az ügyfél olvasási adatátviteli sebessége.|None|
|ClientWriteIOPS|Ügyfél írási IOPS|CountPerSecond|Átlag|Az ügyfél írási műveletei másodpercenként.|None|
|ClientWriteThroughput|Gyorsítótár átlagos írási sebessége|BytesPerSecond|Átlag|Az ügyfél írási adatátviteli sebessége.|None|
|ClientMetadataReadIOPS|Ügyfél metaadatainak olvasása IOPS|CountPerSecond|Átlag|Az adatolvasások kivételével a gyorsítótárba elküldett ügyféloldali műveletek sebessége, amely nem módosítja az állandó állapotot.|None|
|ClientMetadataWriteIOPS|Ügyfél metaadatainak írási IOPS|CountPerSecond|Átlag|A gyorsítótárba küldendő, az adatírásokat nem tartalmazó, az állandó állapotot módosító ügyfél-fájl műveleteinek aránya.|None|
|ClientLockIOPS|Ügyfél-zárolási IOPS|CountPerSecond|Átlag|Az ügyfél fájljának zárolási műveletei másodpercenként.|None|
|StorageTargetHealth|Tárolási cél állapota|Darabszám|Átlag|A kapcsolódási teszt logikai eredményei a gyorsítótár és a tárolási célok között.|None|
|Üzemidő|Üzemidő|Darabszám|Átlag|A kapcsolódási teszt logikai eredményei a gyorsítótár és a figyelési rendszer között.|None|
|StorageTargetIOPS|Összes StorageTarget-IOPS|Darabszám|Átlag|Az összes fájl műveletének sebessége, amelyet a gyorsítótár küld egy adott StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget írási IOPS|Darabszám|Átlag|A fájl írási műveleteinek sebessége, amelyet a gyorsítótár egy adott StorageTarget küld.|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget aszinkron írási átviteli sebesség|BytesPerSecond|Átlag|A gyorsítótárnak az adatokat egy adott StorageTarget való aszinkron módon írási sebessége. Ezek olyan alkalmi írások, amelyek nem okozzák az ügyfelek blokkolását.|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget szinkron írási sebessége|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár szinkron módon ír adatokat egy adott StorageTarget. Ezek az írások, amelyek az ügyfelek blokkolását okozzák.|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget összes írási sebessége|BytesPerSecond|Átlag|Az a teljes sebesség, ameddig a gyorsítótár adatokat ír egy adott StorageTarget.|StorageTarget|
|StorageTargetLatency|StorageTarget késés|Ezredmásodpercben|Átlag|A gyorsítótár által a partricular-StorageTarget küldött összes fájl műveletének átlagos ciklikus késése.|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget-metaadatok IOPS olvasása|CountPerSecond|Átlag|A nem állandó állapotot módosító, valamint az olvasási művelet kihagyása után a gyorsítótár egy adott StorageTarget küldi az adatokat.|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget-metaadatok írási IOPS|CountPerSecond|Átlag|Az állandó állapotot módosító és az írási műveletet nem tartalmazó, a gyorsítótár által egy adott StorageTarget küldött fájl-műveletek aránya.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget olvasási IOPS|CountPerSecond|Átlag|A fájl olvasási műveleteinek sebessége, amelyet a gyorsítótár egy adott StorageTarget küld.|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget – olvasási sebesség|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár a StorageTarget beolvassa az adatokat.|StorageTarget|
|StorageTargetFillThroughput|StorageTarget-kitöltési sebesség|BytesPerSecond|Átlag|Az a sebesség, ameddig a gyorsítótár beolvassa az adatokat a StorageTarget a gyorsítótár-kihagyás kezelésére.|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget összesen olvasási átviteli sebesség|BytesPerSecond|Átlag|Az a teljes sebesség, ameddig a gyorsítótár adatokat olvas egy adott StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Szinkronizálási munkamenet eredménye|Darabszám|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Felhőbeli rétegek felidézésének mérete|Bájt|Összesen|Visszahívott adatmennyiség|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Felhőbeli rétegek felidézésének átviteli sebessége|BytesPerSecond|Átlag|Adatvisszahívási sebesség mérete|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Felhőbeli rétegek felidézésének mérete alkalmazás szerint|Bájt|Összesen|Az alkalmazás által visszahívott adatmennyiség|SyncGroupName, kiszolgálónév, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerHeartbeat|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ResourceUtilization|SU% kihasználtsága|Százalék|Maximum|SU% kihasználtsága|LogicalName, PartitionId|
|InputEvents|Bemeneti események|Darabszám|Összesen|Bemeneti események|LogicalName, PartitionId|
|InputEventBytes|Bemeneti esemény bájtjai|Bájt|Összesen|Bemeneti esemény bájtjai|LogicalName, PartitionId|
|LateInputEvents|Késői bemeneti események|Darabszám|Összesen|Késői bemeneti események|LogicalName, PartitionId|
|OutputEvents|Kimeneti események|Darabszám|Összesen|Kimeneti események|LogicalName, PartitionId|
|ConversionErrors|Adatátalakítási hibák|Darabszám|Összesen|Adatátalakítási hibák|LogicalName, PartitionId|
|Hibák|Futásidejű hibák|Darabszám|Összesen|Futásidejű hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Megrendelési események|Darabszám|Összesen|Megrendelési események|LogicalName, PartitionId|
|AMLCalloutRequests|Függvények kérései|Darabszám|Összesen|Függvények kérései|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Sikertelen függvények kérései|Darabszám|Összesen|Sikertelen függvények kérései|LogicalName, PartitionId|
|AMLCalloutInputEvents|Függvények eseményei|Darabszám|Összesen|Függvények eseményei|LogicalName, PartitionId|
|DeserializationError|Bemeneti deszerializálási hibák|Darabszám|Összesen|Bemeneti deszerializálási hibák|LogicalName, PartitionId|
|EarlyInputEvents|Korai bemeneti események|Darabszám|Összesen|Korai bemeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Vízjel késleltetése|Másodperc|Maximum|Vízjel késleltetése|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Várakozó bemeneti eseményei|Darabszám|Maximum|Várakozó bemeneti eseményei|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Fogadott bemeneti források|Darabszám|Összesen|Fogadott bemeneti források|LogicalName, PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft. szinapszis/munkaterületek

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|A folyamat futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési folyamat-futtatások száma|Eredmény, FailureType, folyamat|
|OrchestrationActivityRunsEnded|A tevékenység futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési tevékenységek száma|Eredmény, FailureType, tevékenység, ActivityType, folyamat|
|OrchestrationTriggersEnded|Befejezett eseményindítók|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési eseményindítók száma|Eredmény, FailureType, trigger|
|SQLOnDemandLoginAttempts|Bejelentkezési kísérletek|Darabszám|Összesen|A sikerül vagy sikertelen bejelentkezési kísérletek száma|Eredmény|
|SQLOnDemandQueriesEnded|A lekérdezések véget ért|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított lekérdezések száma|Eredmény|
|SQLOnDemandQueryProcessedBytes|Feldolgozott adatfeldolgozás|Bájt|Összesen|Lekérdezések által feldolgozott adatmennyiség|None|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. szinapszis/munkaterületek/bigDataPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SparkJobsEnded|Befejezett alkalmazások|Darabszám|Összesen|Befejezett alkalmazások száma|JobType, JobResult|
|CoresCapacity|Magok kapacitása|Darabszám|Maximum|Magok kapacitása|None|
|MemoryCapacityGB|Memória kapacitása (GB)|Darabszám|Maximum|Memória kapacitása (GB)|None|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. szinapszis/munkaterületek/sqlPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DWULimit|DWU korlátja|Darabszám|Maximum|Az SQL-készlet szolgáltatási szintjének célkitűzése|None|
|DWUUsed|Használt DWU|Darabszám|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. DWU-korláttal mérve * DWU százalék|None|
|DWUUsedPercent|DWU használt százalék|Százalék|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. A CPU-százalék és az adatio-százalék közötti maximális érték alapján mérhető|None|
|ConnectionsBlockedByFirewall|Tűzfal által letiltott kapcsolatok|Darabszám|Összesen|A tűzfalszabályok által letiltott kapcsolatok száma. Nyissa meg újra az SQL-készlethez tartozó hozzáférés-vezérlési szabályzatokat, és figyelje meg, hogy a kapcsolatok száma magas-e|None|
|AdaptiveCacheHitPercent|Adaptív gyorsítótár-találatok százalékos aránya|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár találati százalékos metrikájának használatával határozhatja meg, hogy szeretné-e bővíteni a további kapacitást, vagy futtassa újra a számítási feladatokat a gyorsítótár hidrát|None|
|AdaptiveCacheUsedPercent|Adaptív gyorsítótár használt százaléka|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár használt százalékos metrikájának használatával határozhatja meg, hogy a további kapacitást szeretné-e méretezni, vagy újra kell futtatnia a számítási feladatokat a gyorsítótár hidrát|None|
|LocalTempDBUsedPercent|Helyi tempdb használt százalék|Százalék|Maximum|Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva|None|
|MemoryUsedPercent|Felhasznált memória százalékos aránya|Százalék|Maximum|Memória kihasználtsága az SQL-készlet összes csomópontján|None|
|Kapcsolatok|Kapcsolatok|Darabszám|Összesen|Az SQL-készletbe való teljes bejelentkezések száma|Eredmény|
|WLGActiveQueries|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|A munkaterhelés csoporton belüli aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|IsUserDefined,WorkloadGroup|
|WLGActiveQueriesTimeouts|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Túllépte az időkorlátot tartalmazó munkaterhelési csoport lekérdezéseit. A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának megkezdése után (nem tartalmazza a várakozási időt a zárolás vagy az erőforrás-várakozás miatt)|IsUserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások százalékos kiosztása a teljes rendszerrel viszonyítva|IsUserDefined,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Munkaterhelési csoport lefoglalása az erőforrás maximális százaléka alapján|Százalék|Maximum|Megjeleníti az erőforrások százalékos kiosztását a munkaterhelési csoport tényleges maximális erőforrásához viszonyítva. Ez a mérőszám a munkaterhelés-csoport hatékony kihasználtságát biztosítja|IsUserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport tényleges Cap-erőforrásának százaléka. Ha más munkaterhelés-csoportok min_percentage_resource > 0, a effective_cap_percentage_resource arányosan csökken|IsUserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Effektív minimális erőforrás százaléka|Százalék|Minimális|A tényleges minimális erőforrás-százalékos beállítás a szolgáltatási szint és a munkaterhelés-csoport beállításainak figyelembevételével engedélyezett. A tényleges min_percentage_resource magasabb szinten állítható be alacsonyabb szolgáltatási szinteken|IsUserDefined,WorkloadGroup|
|WLGQueuedQueries|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát|IsUserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|None|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|None|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az összes eseményforrás által olvasott bájtok száma|None|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|None|
|IngressStoredEvents|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|None|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|None|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|None|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|None|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|None|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Darabszám|Összesen|Az eseményforrás által olvasott üzenetek száma|None|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az eseményforrás által beolvasott érvénytelen üzenetek száma|None|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az eseményforrás által beolvasott bájtok száma|None|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|None|
|IngressStoredEvents|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|None|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|None|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|None|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|None|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|None|
|DiskWriteBytesPerSecond|Lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|None|
|Lemez olvasási bájtjai|Lemez olvasási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|None|
|Lemez írási bájtjai|Lemez írási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|None|
|DiskReadOperations|Lemezes olvasási műveletek|Darabszám|Összesen|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|DiskWriteOperations|Lemez írási műveletei|Darabszám|Összesen|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|Lemez olvasási művelete/mp|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|Lemez írási műveletei/mp|Lemez írási műveletei/mp|CountPerSecond|Átlag|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|DiskReadLatency|Lemez olvasási késése|Ezredmásodpercben|Átlag|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|None|
|DiskWriteLatency|Lemez írási késése|Ezredmásodpercben|Átlag|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|None|
|NetworkInBytesPerSecond|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli sebessége.|None|
|NetworkOutBytesPerSecond|Hálózati kimenő bájtok/s|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli sebessége.|None|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A fogadott forgalom összes hálózati átviteli sebessége.|None|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A továbbított forgalom összes hálózati átviteli sebessége.|None|
|MemoryUsed|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt gépi memória mennyisége.|None|
|MemoryGranted|Megadott memória|Bájt|Átlag|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|None|
|MemoryActive|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|None|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|None|
|PercentageCpuReady|CPU-készültség százalékos aránya|Ezredmásodpercben|Összesen|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|None|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/kiszolgálófarmok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvezérlő-várólista hossza|Darabszám|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Http-várólista hossza|Darabszám|Átlag|Http-várólista hossza|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|TcpSynSent|Eljuttatott TCP SYN|Darabszám|Átlag|Eljuttatott TCP SYN|Példány|
|TcpSynReceived|TCP SYN kapott|Darabszám|Átlag|TCP SYN kapott|Példány|
|TcpEstablished|A TCP létrejött|Darabszám|Átlag|A TCP létrejött|Példány|
|TcpFinWait1|TCP fin WAIT 1|Darabszám|Átlag|TCP fin WAIT 1|Példány|
|TcpFinWait2|TCP fin WAIT 2|Darabszám|Átlag|TCP fin WAIT 2|Példány|
|TcpClosing|TCP-zárás|Darabszám|Átlag|TCP-zárás|Példány|
|TcpCloseWait|TCP-bezárási várakozás|Darabszám|Átlag|TCP-bezárási várakozás|Példány|
|TcpLastAck|TCP Last ACK|Darabszám|Átlag|TCP Last ACK|Példány|
|TcpTimeWait|TCP-idő várakozása|Darabszám|Átlag|TCP-idő várakozása|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (a függvények kivételével) 

> [!NOTE]
> A **fájlrendszer használata** egy új metrika, amely globálisan zajlik, és nem számítunk fel adatokat, kivéve, ha Ön rendelkezik a privát előzetes verzióra vonatkozó engedélyezési listával.

> [!IMPORTANT]
> Az **átlagos válaszidő** a metrikák összesítésével való összekeveredés elkerülése érdekében elavulttá válik. A **válaszidő** használata csereként.

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|Másodperc|Összesen|CPU-idő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|HttpResponseTime|Válaszidő|Másodperc|Összesen|Válaszidő|Példány|
|AverageResponseTime|Átlagos válaszidő (elavult)|Másodperc|Átlag|Átlagos válaszidő|Példány|
|AppConnections|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|Kezeli|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
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
|Gen0Collections|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|HealthCheckStatus|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (functions)

> [!NOTE]
> A **fájlrendszer használata** egy új metrika, amely globálisan zajlik, és nem számítunk fel adatokat, kivéve, ha Ön rendelkezik a privát előzetes verzióra vonatkozó engedélyezési listával.

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
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
|Gen0Collections|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|HealthCheckStatus|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|CPU-idő|Másodperc|Összesen|CPU-idő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|MemoryWorkingSet|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|AverageMemoryWorkingSet|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Átlagos válaszidő|Másodperc|Átlag|Átlagos válaszidő|Példány|
|HttpResponseTime|Válaszidő|Másodperc|Átlag|Válaszidő|Példány|
|FunctionExecutionUnits|Függvények végrehajtási egységei|Darabszám|Összesen|Függvények végrehajtási egységei|Példány|
|FunctionExecutionCount|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|AppConnections|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|Kezeli|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
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
|Gen0Collections|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|HealthCheckStatus|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|BytesReceived|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|AverageResponseTime|Átlagos válaszidő|Másodperc|Átlag|Átlagos válaszidő|Példány|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvezérlő-várólista hossza|Darabszám|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Http-várólista hossza|Darabszám|Átlag|Http-várólista hossza|Példány|
|ActiveRequests|Aktív kérések|Darabszám|Összesen|Aktív kérések|Példány|
|TotalFrontEnds|Összes előtér|Darabszám|Átlag|Összes előtér|None|
|SmallAppServicePlanInstances|Kisméretű App Service-csomag feldolgozói|Darabszám|Átlag|Kisméretű App Service-csomag feldolgozói|None|
|MediumAppServicePlanInstances|Közepes App Service-csomag feldolgozói|Darabszám|Átlag|Közepes App Service-csomag feldolgozói|None|
|LargeAppServicePlanInstances|Nagy App Service-csomag feldolgozói|Darabszám|Átlag|Nagy App Service-csomag feldolgozói|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WorkersTotal|Alkalmazottak összesen|Darabszám|Átlag|Alkalmazottak összesen|None|
|WorkersAvailable|Rendelkezésre álló munkavégzők|Darabszám|Átlag|Rendelkezésre álló munkavégzők|None|
|WorkersUsed|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|None|
|CpuPercentage|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
## <a name="next-steps"></a>További lépések
* [Tudnivalók a Azure Monitor mérőszámokról](data-platform.md)
* [Riasztások létrehozása metrikákhoz](alerts-overview.md)
* [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](platform-logs-overview.md)

