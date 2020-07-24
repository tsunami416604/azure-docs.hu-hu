---
title: Azure Monitor támogatott metrikák erőforrástípus szerint
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 81e2abc1b4fd0c540b08f96e6b34c16fca3319f1
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132016"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák Azure Monitor

> [!NOTE]
> A lista nagy mértékben automatikusan létrejön a Azure Monitor metrikák REST API. A listán a GitHubon keresztül végrehajtott módosítások figyelmeztetés nélkül is megírhatók. A végleges frissítések végrehajtásával kapcsolatban a cikk szerzője nyújt tájékoztatást.

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy a PowerShell vagy a parancssori felület használatával történő lekérdezésre. 

Ez a cikk a Azure Monitor konszolidált metrikus folyamatával jelenleg elérhető összes platform (azaz automatikusan összegyűjtött) mérőszámok teljes listáját tartalmazza. A lista 2020. március 27-én Utoljára frissítve. Az ezen dátum után módosított vagy hozzáadott mérőszámok nem jelennek meg alább. A metrikák listájának programozott módon történő lekérdezéséhez és eléréséhez használja a [2018-01-01 API-Version](/rest/api/monitor/metricdefinitions). A listán nem szereplő egyéb mérőszámok a portálon vagy a régi API-k használatával is elérhetők.

A metrikák az erőforrás-szolgáltatók és az erőforrástípus szerint vannak rendezve. A szolgáltatások listáját és az azokhoz tartozó erőforrás-szolgáltatókat az Azure- [szolgáltatások erőforrás-szolgáltatói](../../azure-resource-manager/management/azure-services-resource-providers.md)című témakörben tekintheti meg. 

## <a name="exporting-platform-metrics-to-other-locations"></a>Platform metrikáinak exportálása más helyszínekre

Az Azure monitor folyamatának platform metrikáit kétféleképpen exportálhatja más helyekre.
1. A [metrikák REST API](/rest/api/monitor/metrics/list) használata
2. [Diagnosztikai beállítások](diagnostic-settings.md) használata a platform metrikáinak átirányításához 
    - Azure Storage
    - Naplók Azure Monitor (és így Log Analytics)
    - Event hubok, amelyekkel a nem a Microsofttól származó rendszerekhez juthat 

A diagnosztikai beállítások használata a metrikák átirányításának legegyszerűbb módja, de bizonyos korlátozások vannak: 

- **Néhány nem exportálható** – az összes metrika exportálható a REST API használatával, néhányat azonban nem lehet a diagnosztikai beállítások használatával exportálni a Azure monitor háttérbeli munkafolyamatok miatt. Az alábbi táblázatokban szereplő *diagnosztikai beállításokkal exportálható* oszlop, amely így exportálható.  

- **Többdimenziós mérőszámok** – a többdimenziós metrikák más helyekre való küldése jelenleg nem támogatott a diagnosztikai beállítások használatával. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel. *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.

## <a name="guest-os-and-host-os-metrics"></a>Vendég operációs rendszer és a gazdagép operációs rendszerének mérőszámai

> [!WARNING]
> Az Azure Virtual Machines, Service Fabric és Cloud Services operációs rendszert futtató vendég operációs rendszer mérőszámai itt **nem** szerepelnek. A vendég operációs rendszer metrikáit egy vagy több olyan ügynökön keresztül kell gyűjteni, amely a vendég operációs rendszer részeként fut vagy sem.  A vendég operációs rendszer metrikái olyan teljesítményszámlálók, amelyek a vendég CPU-százalékos vagy memóriahasználat nyomon követésére szolgálnak, amelyek közül mindkettő gyakran használatos automatikus méretezéshez vagy riasztáshoz. 
>
> **A gazdagép operációs rendszer metrikái elérhetők és alább láthatók.** Ezek nem azonosak. A gazdagép operációs rendszer metrikái a vendég operációs rendszer munkamenetét működtető Hyper-V-munkamenethez kapcsolódnak. 

> [!TIP]
> Az ajánlott eljárás az [Azure Diagnostics-bővítmény](diagnostics-extension-overview.md) használata és konfigurálása, hogy a vendég operációs rendszer teljesítményének mérőszámait ugyanarra a Azure monitor metrikus adatbázisba küldje el, ahol a platform metrikái vannak tárolva. A bővítmény a vendég operációs rendszer metrikáit az [Egyéni metrikák](metrics-custom-overview.md) API-n keresztül irányítja. Ezután diagramot, riasztást és egyéb módon használhatja a vendég operációs rendszer metrikáit, például a platform metrikáit. Emellett a Log Analytics ügynökkel is elküldheti a vendég operációs rendszer metrikáit Azure Monitor naplókba/Log Analytics. A mérőszámokat nem metrikus adatokkal kombinálva is lekérdezheti. 

További információ: [monitoring Agents – áttekintés](agents-overview.md).    

## <a name="table-formatting"></a>Táblázat formázása

> [!IMPORTANT] 
> Ez a legújabb frissítés egy új oszlopot hoz létre, és átrendezi a mérőszámokat ABC-ként. A hozzáadási információ azt jelenti, hogy az alábbi táblázatok a böngészőablak szélessége alapján vízszintes görgetősávtal rendelkezhetnek az alsó sarokban. Ha úgy véli, hogy hiányoznak az adatok, a görgetősávon láthatja a teljes táblázatot.


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Memória: tisztító – aktuális ár|Darabszám|Átlag|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|ServerResourceType|
|CleanerMemoryNonshrinkable|Yes|Memória: nem csökkenthető a tisztább memória|Bájt|Átlag|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|ServerResourceType|
|CleanerMemoryShrinkable|Yes|Memória: tisztító memória csökkenthető|Bájt|Átlag|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Szálak: a parancssori készlet foglalt szálak|Darabszám|Átlag|A parancsfájl-készletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Szálak: parancssori készlet üresjárati szálai|Darabszám|Átlag|Az üresjárati szálak száma a parancs szálának készletében.|ServerResourceType|
|CommandPoolJobQueueLength|Yes|Parancssori feladatok várólistájának hossza|Darabszám|Átlag|A parancsfájl-készlet várólistájában lévő feladatok száma.|ServerResourceType|
|Összege|Yes|Kapcsolat: aktuális kapcsolatok|Darabszám|Átlag|A létesített ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CurrentUserSessions|Yes|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrejött felhasználói munkamenetek aktuális száma.|ServerResourceType|
|LongParsingBusyThreads|Yes|Szálak: hosszú elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingIdleThreads|Yes|Szálak: tartós folyamatok elemzése – üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Szálak: hosszú elemzési feladatok várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|mashup_engine_memory_metric|Yes|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|M motor saját bájtjai|Bájt|Átlag|Saját bájtok használata az adategyesítési motor folyamatai alapján.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|M motor QPU|Darabszám|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|M motor virtuális bájtjai|Bájt|Átlag|Az adategyesítési motor folyamatai által használt virtuális bájtok használata.|ServerResourceType|
|memory_metric|Yes|Memória|Bájt|Átlag|Memory. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|memory_thrashing_metric|Yes|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|ServerResourceType|
|MemoryLimitHard|Yes|Memória: rögzített memória|Bájt|Átlag|Rögzített memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Yes|Memória: magas a memória korlátozása|Bájt|Átlag|Magas memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Yes|Memória: kevés a memória korlátja|Bájt|Átlag|Kevés a memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Memória: VertiPaq korlátja|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|MemoryUsage|Yes|Memória: memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memória által leképezett adatmennyiséggel, figyelmen kívül hagyva azokat a memóriát, amelyeket a xVelocity memórián belüli elemzési motorja (VertiPaq) leképezett vagy lefoglalt, és amely meghaladja a xVelocity-memória korlátját.|ServerResourceType|
|private_bytes_metric|Yes|Saját bájtok|Bájt|Átlag|Saját bájtok.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Yes|Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai|Darabszám|Átlag|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Yes|Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva|Darabszám|Átlag|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Yes|Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Yes|Szálak: feldolgozási készlet üresjáratban nem I/O-szálai|Darabszám|Átlag|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Yes|Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Yes|Feldolgozási készlet nyomtatási várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|ServerResourceType|
|qpu_metric|Yes|QPU|Darabszám|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|QueryPoolBusyThreads|Yes|Lekérdezési készlet foglalt szálai|Darabszám|Átlag|A lekérdezési szál készletében lévő foglalt szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Szálak: lekérdezési készlet – üresjárati szálak|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Szálak: lekérdezési készlet feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|Kvóta|Yes|Memória: kvóta|Bájt|Átlag|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|ServerResourceType|
|QuotaBlocked|Yes|Memória: blokkolt kvóta|Darabszám|Átlag|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|ServerResourceType|
|RowsConvertedPerSec|Yes|Feldolgozás: másodpercenként konvertált sorok száma|CountPerSecond|Átlag|A sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsReadPerSec|Yes|Feldolgozás: másodpercenként beolvasott sorok száma|CountPerSecond|Átlag|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|ServerResourceType|
|RowsWrittenPerSec|Yes|Feldolgozás: másodpercenként írt sorok száma|CountPerSecond|Átlag|A sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Szálak: rövid elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Szálak: rövid elemzési üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Szálak: rövid elemzési feladatok várólistájának hossza|Darabszám|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Sikeres kapcsolatok másodpercenként|CountPerSecond|Átlag|A sikeres kapcsolatok gyakorisága.|ServerResourceType|
|TotalConnectionFailures|Yes|Összes sikertelen Kapcsolatfelvétel|Darabszám|Átlag|A sikertelen csatlakozási kísérletek teljes száma.|ServerResourceType|
|TotalConnectionRequests|Yes|Kapcsolatkérelmek teljes száma|Darabszám|Átlag|A kapcsolatkérelmek teljes száma. Ezek az érkezések.|ServerResourceType|
|VertiPaqNonpaged|Yes|Memória: VertiPaq, nem lapozható|Bájt|Átlag|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|ServerResourceType|
|VertiPaqPaged|Yes|Memória: VertiPaq lapozható|Bájt|Átlag|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|ServerResourceType|
|virtual_bytes_metric|Yes|Felhasznált virtuális memória jelenlegi mérete (bájt)|Bájt|Átlag|Virtuális bájtok.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|A háttérbeli kérelmek időtartama|Ezredmásodpercben|Átlag|A háttérbeli kérelmek időtartama (ezredmásodpercben)|Hely, állomásnév|
|Kapacitás|Yes|Kapacitás|Százalék|Átlag|A ApiManagement szolgáltatás kihasználtsági metrikája|Hely|
|Időtartam|Yes|Az átjárók kéréseinek teljes időtartama|Ezredmásodpercben|Átlag|Az átjáró kérelmek teljes időtartama ezredmásodpercben|Hely, állomásnév|
|EventHubDroppedEvents|Yes|Eldobott EventHub események|Darabszám|Összesen|A várólista-méretkorlát elérte miatt kihagyott események száma|Hely|
|EventHubRejectedEvents|Yes|EventHub-események visszautasítva|Darabszám|Összesen|Visszautasított EventHub-események száma (helytelen konfiguráció vagy jogosulatlan)|Hely|
|EventHubSuccessfulEvents|Yes|Sikeres EventHub események|Darabszám|Összesen|Sikeres EventHub események száma|Hely|
|EventHubThrottledEvents|Yes|Szabályozott EventHub események|Darabszám|Összesen|A szabályozott EventHub események száma|Hely|
|EventHubTimedoutEvents|Yes|Időtúllépés a EventHub eseményeinél|Darabszám|Összesen|Az időtúllépés miatti EventHub események száma|Hely|
|EventHubTotalBytesSent|Yes|EventHub-események mérete|Bájt|Összesen|EventHub-események teljes mérete bájtban|Hely|
|EventHubTotalEvents|Yes|Összes EventHub esemény|Darabszám|Összesen|A EventHub eljuttatott események száma|Hely|
|EventHubTotalFailedEvents|Yes|Sikertelen EventHub események|Darabszám|Összesen|Sikertelen EventHub események száma|Hely|
|FailedRequests|Yes|Sikertelen átjáró-kérelmek (elavult)|Darabszám|Összesen|Hibák száma az átjáró kéréseiben – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|NetworkConnectivity|Yes|Erőforrások hálózati kapcsolati állapota (előzetes verzió)|Darabszám|Átlag|A API Management szolgáltatástól függő erőforrástípusok hálózati kapcsolati állapota|Hely, ResourceType|
|OtherRequests|Yes|Egyéb átjáró-kérelmek (elavult)|Darabszám|Összesen|Egyéb átjáró-kérelmek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Több dimenzióval rendelkező átjáró-kérelmek metrikái|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Sikeres átjáró-kérelmek (elavult)|Darabszám|Összesen|Sikeres átjáró-kérések száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzió helyett|Hely, állomásnév|
|TotalRequests|Yes|Összes átjáró kérelme (elavult)|Darabszám|Összesen|Átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|UnauthorizedRequests|Yes|Nem engedélyezett átjáró-kérelmek (elavult)|Darabszám|Összesen|Jogosulatlan átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Darabszám|Darabszám|A bejövő HTTP-kérelmek teljes száma.|StatusCode, hitelesítés|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Darabszám|Átlag|Egy HTTP-kérelem késése.|StatusCode, hitelesítés|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Darabszám|Darabszám|Szabályozott HTTP-kérelmek.|Nincsenek méretek|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|JVM. GC. Live. Resize|Yes|JVM. GC. Live. Resize|Bájt|Átlag|Régi generációs memória-készlet mérete teljes GC után|Üzembe helyezés, AppName, Pod|
|JVM. GC. max. az adatméret|Yes|JVM. GC. max. az adatméret|Bájt|Átlag|A régi generációs memória maximális mérete|Üzembe helyezés, AppName, Pod|
|JVM. GC. Memory. lefoglalt|Yes|JVM. GC. Memory. lefoglalt|Bájt|Maximum|Növekszik a fiatal generációs memória-készlet méretének növekedésével, miután egy GC a következőre kerül|Üzembe helyezés, AppName, Pod|
|JVM. GC. Memory. előléptetve|Yes|JVM. GC. Memory. előléptetve|Bájt|Maximum|A régi generációs memória-készlet méretének pozitív növekedésének száma, mielőtt a GC a GC-re lenne|Üzembe helyezés, AppName, Pod|
|JVM. GC. pause. Total. Count|Yes|JVM. GC. pause. Total. Count|Darabszám|Összesen|GC-szüneteltetések száma|Üzembe helyezés, AppName, Pod|
|JVM. GC. pause. Total. Time|Yes|JVM. GC. pause. Total. Time|Ezredmásodpercben|Összesen|GC felfüggesztésének teljes ideje|Üzembe helyezés, AppName, Pod|
|JVM. Memory. véglegesített|Yes|JVM. Memory. véglegesített|Bájt|Átlag|JVM rendelt memória bájtban|Üzembe helyezés, AppName, Pod|
|JVM. Memory. max|Yes|JVM. Memory. max|Bájt|Maximum|A memória-kezeléshez használható memória maximális mennyisége bájtban|Üzembe helyezés, AppName, Pod|
|JVM. Memory. felhasználva|Yes|JVM. Memory. felhasználva|Bájt|Átlag|Az alkalmazás memóriája bájtban használatban|Üzembe helyezés, AppName, Pod|
|Process. CPU. használat|Yes|Process. CPU. használat|Százalék|Átlag|Alkalmazás JVM CPU-kihasználtsági aránya|Üzembe helyezés, AppName, Pod|
|System. CPU. használat|Yes|System. CPU. használat|Százalék|Átlag|A legújabb CPU-használat a teljes rendszerhez|Üzembe helyezés, AppName, Pod|
|tomcat. Global. error|Yes|tomcat. Global. error|Darabszám|Összesen|Tomcat globális hiba|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Received|Yes|tomcat. Global. Received|Bájt|Összesen|Tomcat összesen fogadott bájtok|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. AVG. Time|Yes|tomcat. Global. Request. AVG. Time|Ezredmásodpercben|Átlag|Tomcat-kérelem átlagos ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. max|Yes|tomcat. Global. Request. max|Ezredmásodpercben|Maximum|Tomcat-kérelem maximális ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. Total. Count|Yes|tomcat. Global. Request. Total. Count|Darabszám|Összesen|Tomcat-kérelem összesített száma|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. Total. Time|Yes|tomcat. Global. Request. Total. Time|Ezredmásodpercben|Összesen|Tomcat-kérelem teljes ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. elküldve|Yes|tomcat. Global. elküldve|Bájt|Összesen|Tomcat összesen eljuttatott bájtok száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Active. Current|Yes|tomcat. Sessions. Active. Current|Darabszám|Összesen|Tomcat-munkamenet aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Active. max|Yes|tomcat. Sessions. Active. max|Darabszám|Összesen|Tomcat-munkamenet maximális aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Alive. max|Yes|tomcat. Sessions. Alive. max|Ezredmásodpercben|Maximum|Tomcat-munkamenet maximális élettartama|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. created|Yes|tomcat. Sessions. created|Darabszám|Összesen|Tomcat-munkamenet létrehozva szám|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. lejárt|Yes|tomcat. Sessions. lejárt|Darabszám|Összesen|A Tomcat-munkamenet lejárt|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. elutasítva|Yes|tomcat. Sessions. elutasítva|Darabszám|Összesen|A Tomcat-munkamenet elutasította a darabszámot|Üzembe helyezés, AppName, Pod|
|tomcat.threads.config. max|Yes|tomcat.threads.config. max|Darabszám|Összesen|Tomcat-konfiguráció maximális szálának száma|Üzembe helyezés, AppName, Pod|
|tomcat. Threads. Current|Yes|tomcat. Threads. Current|Darabszám|Összesen|Tomcat jelenlegi szálak száma|Üzembe helyezés, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Feladatok összesen|Darabszám|Összesen|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentMachineRuns|Yes|Összes frissítés központi telepítési gép futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Összes frissítés központi telepítési futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, állapot|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CoreCount|No|Dedikált mag száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált magok teljes száma|Nincsenek méretek|
|CreatingNodeCount|No|Csomópontok számának létrehozása|Darabszám|Összesen|Létrehozandó csomópontok száma|Nincsenek méretek|
|IdleNodeCount|No|Tétlen csomópontok száma|Darabszám|Összesen|Üresjárati csomópontok száma|Nincsenek méretek|
|JobDeleteCompleteEvent|Yes|Feladatok törlése – befejezett események|Darabszám|Összesen|A sikeresen törölt feladatok teljes száma.|jobId|
|JobDeleteStartEvent|Yes|Feladatok törlésének indítási eseményei|Darabszám|Összesen|A törölni kívánt feladatok teljes száma.|jobId|
|JobDisableCompleteEvent|Yes|A feladatok letiltják a teljes eseményeket|Darabszám|Összesen|A sikeresen letiltott feladatok teljes száma.|jobId|
|JobDisableStartEvent|Yes|Feladatok letiltásának indítási eseményei|Darabszám|Összesen|A letiltani kívánt feladatok teljes száma.|jobId|
|JobStartEvent|Yes|Feladatok indítási eseményei|Darabszám|Összesen|A sikeresen elindított feladatok teljes száma.|jobId|
|JobTerminateCompleteEvent|Yes|A feladatokhoz tartozó befejezett események befejezése|Darabszám|Összesen|A sikeresen leállított feladatok teljes száma.|jobId|
|JobTerminateStartEvent|Yes|Feladatokból indított események leállítása|Darabszám|Összesen|A leállítani kívánt feladatok teljes száma.|jobId|
|LeavingPoolNodeCount|No|A készlet-csomópontok számának elhagyása|Darabszám|Összesen|A készletet elhagyó csomópontok száma|Nincsenek méretek|
|LowPriorityCoreCount|No|LowPriority mag száma|Darabszám|Összesen|Az alacsony prioritású magok teljes száma a Batch-fiókban|Nincsenek méretek|
|OfflineNodeCount|No|Offline csomópontok száma|Darabszám|Összesen|Offline csomópontok száma|Nincsenek méretek|
|PoolCreateEvent|Yes|Készlet-létrehozási események|Darabszám|Összesen|A létrehozott készletek teljes száma|poolId|
|PoolDeleteCompleteEvent|Yes|Készlet törlése – befejezett események|Darabszám|Összesen|A befejezett készlet-törlések teljes száma|poolId|
|PoolDeleteStartEvent|Yes|Készlet törlése – indítási események|Darabszám|Összesen|Az elindított készlet-törlések száma összesen|poolId|
|PoolResizeCompleteEvent|Yes|Készlet átméretezése – befejezett események|Darabszám|Összesen|A készlet összes újraméretezésének száma|poolId|
|PoolResizeStartEvent|Yes|Készlet átméretezésének indítási eseményei|Darabszám|Összesen|A készlet elindított újraméretezésének teljes száma|poolId|
|PreemptedNodeCount|No|Előzik-csomópontok száma|Darabszám|Összesen|Előzik-csomópontok száma|Nincsenek méretek|
|RebootingNodeCount|No|Csomópontok számának újraindítása|Darabszám|Összesen|Újraindítási csomópontok száma|Nincsenek méretek|
|ReimagingNodeCount|No|Csomópontok rendszerképének alaphelyzetbe állítása|Darabszám|Összesen|Rendszerkép-csomópontok száma|Nincsenek méretek|
|RunningNodeCount|No|Csomópontok száma|Darabszám|Összesen|Futó csomópontok száma|Nincsenek méretek|
|StartingNodeCount|No|Csomópontok számának indítása|Darabszám|Összesen|Kiinduló csomópontok száma|Nincsenek méretek|
|StartTaskFailedNodeCount|No|Indítási feladat sikertelen csomópontok száma|Darabszám|Összesen|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|Nincsenek méretek|
|TaskCompleteEvent|Yes|Feladat teljes eseményei|Darabszám|Összesen|A Befejezett feladatok teljes száma|poolId, jobId|
|TaskFailEvent|Yes|Feladat sikertelen eseményei|Darabszám|Összesen|A meghiúsult állapotban Befejezett feladatok teljes száma|poolId, jobId|
|TaskStartEvent|Yes|Tevékenységek indítási eseményei|Darabszám|Összesen|Az elindított feladatok teljes száma|poolId, jobId|
|TotalLowPriorityNodeCount|No|Alacsony prioritású csomópontok száma|Darabszám|Összesen|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|Nincsenek méretek|
|TotalNodeCount|No|Dedikált csomópontok száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált csomópontok teljes száma|Nincsenek méretek|
|UnusableNodeCount|No|Nem használható csomópontok száma|Darabszám|Összesen|Használhatatlan csomópontok száma|Nincsenek méretek|
|WaitingForStartTaskNodeCount|No|Várakozás a feladat-csomópontok számának megkezdésére|Darabszám|Összesen|Az indítási tevékenység befejezésére váró csomópontok száma|Nincsenek méretek|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Yes|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Yes|Aktív csomópontok|Darabszám|Átlag|Futó csomópontok száma|Forgatókönyv, ClusterName|
|Üresjárati magok|Yes|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Yes|Tétlen csomópontok|Darabszám|Átlag|Üresjárati csomópontok száma|Forgatókönyv, ClusterName|
|A feladatok befejeződtek|Yes|A feladatok befejeződtek|Darabszám|Összesen|Befejezett feladatok száma|Forgatókönyv, ClusterName, ResultType|
|Feladatok elküldve|Yes|Feladatok elküldve|Darabszám|Összesen|Elküldött feladatok száma|Forgatókönyv, ClusterName|
|Magok kihagyása|Yes|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Yes|Csomópontok elhagyása|Darabszám|Átlag|Kilépő csomópontok száma|Forgatókönyv, ClusterName|
|Előzik magok|Yes|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Yes|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtsága (%)|Yes|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Magok összesen|Yes|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Csomópontok összesen|Yes|Csomópontok összesen|Darabszám|Átlag|Összes csomópont száma|Forgatókönyv, ClusterName|
|Használhatatlan magok|Yes|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Yes|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma|Forgatókönyv, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|Szórásos feldolgozott darabszám|Darabszám|Átlag|A feldolgozott tranzakciók száma|Csomópont, csatorna, típus, állapot|
|ConnectionAccepted|Yes|Elfogadott kapcsolatok|Darabszám|Összesen|Elfogadott kapcsolatok|Csomópont|
|ConnectionActive|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Csomópont|
|ConnectionHandled|Yes|Kezelt kapcsolatok|Darabszám|Összesen|Kezelt kapcsolatok|Csomópont|
|ConsensusEtcdraftCommittedBlockNumber|Yes|Konszenzus Etcdraft véglegesített blokk száma|Darabszám|Átlag|A legutolsó blokkolt blokk blokkjának száma|Csomópont, csatorna|
|CpuUsagePercentageInDouble|Yes|CPU-használat százaléka|Százalék|Maximum|CPU-használat százaléka|Csomópont|
|EndorserEndorsementFailures|Yes|A jóváhagyói jóváhagyások hibái|Darabszám|Átlag|A sikertelen jóváhagyások száma.|Csomópont, csatorna, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|Yes|Gossip Leader-választási vezető|Darabszám|Összesen|Társ a vezető (1) vagy követője (0)|Csomópont, csatorna|
|GossipMembershipTotalPeersKnown|Yes|Pletykák tagságának összes ismert társa|Darabszám|Átlag|Összes ismert társ|Csomópont, csatorna|
|GossipStateHeight|Yes|Pletyka állapotának magassága|Darabszám|Átlag|Aktuális Főkönyv magassága|Csomópont, csatorna|
|IOReadBytes|Yes|IO olvasási bájtok|Bájt|Összesen|IO olvasási bájtok|Csomópont|
|IOWriteBytes|Yes|IO írási bájtjai|Bájt|Összesen|IO írási bájtjai|Csomópont|
|LedgerTransactionCount|Yes|Főkönyvi tranzakciók száma|Darabszám|Átlag|Feldolgozott tranzakciók száma|Csomópont, csatorna, transaction_type, chaincode, validation_code|
|MemoryLimit|Yes|Memória korlátja|Bájt|Átlag|Memória korlátja|Csomópont|
|MemoryUsage|Yes|Memóriahasználat|Bájt|Átlag|Memóriahasználat|Csomópont|
|MemoryUsagePercentageInDouble|Yes|Memóriahasználat százaléka|Százalék|Átlag|Memóriahasználat százaléka|Csomópont|
|PendingTransactions|Yes|Függőben lévő tranzakciók|Darabszám|Átlag|Függőben lévő tranzakciók|Csomópont|
|ProcessedBlocks|Yes|Feldolgozott blokkok|Darabszám|Összesen|Feldolgozott blokkok|Csomópont|
|ProcessedTransactions|Yes|Feldolgozott tranzakciók|Darabszám|Összesen|Feldolgozott tranzakciók|Csomópont|
|QueuedTransactions|Yes|Várólistán lévő tranzakciók|Darabszám|Átlag|Várólistán lévő tranzakciók|Csomópont|
|RequestHandled|Yes|Kezelt kérelmek|Darabszám|Összesen|Kezelt kérelmek|Csomópont|
|StorageUsage|Yes|Tárterület-használat|Bájt|Átlag|Tárterület-használat|Csomópont|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|cachehits|Yes|Gyorsítótár-találatok|Darabszám|Összesen||ShardId|
|cachehits0|Yes|Gyorsítótárbeli találatok (szegmens 0)|Darabszám|Összesen||Nincsenek méretek|
|cachehits1|Yes|Gyorsítótárbeli találatok (1. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits2|Yes|Gyorsítótárbeli találatok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachehits3|Yes|Gyorsítótárbeli találatok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits4|Yes|Gyorsítótár-találatok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits5|Yes|Gyorsítótárbeli találatok (szegmens 5)|Darabszám|Összesen||Nincsenek méretek|
|cachehits6|Yes|Gyorsítótárbeli találatok (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|cachehits7|Yes|Gyorsítótárbeli találatok (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|cachehits8|Yes|Gyorsítótárbeli találatok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|cachehits9|Yes|Gyorsítótárbeli találatok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|cacheLatency|Yes|Gyorsítótár késési másodpercek (előzetes verzió)|Darabszám|Átlag||ShardId|
|cachemisses|Yes|Gyorsítótár-tévesztések|Darabszám|Összesen||ShardId|
|cachemisses0|Yes|Gyorsítótár-kihagyás (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses1|Yes|Gyorsítótár-lemaradás (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses2|Yes|Gyorsítótár-lemaradás (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses3|Yes|Gyorsítótár-lemaradás (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses4|Yes|Gyorsítótár-lemaradás (4. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses5|Yes|Gyorsítótár-lemaradás (5. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses6|Yes|Gyorsítótár-lemaradás (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses7|Yes|Gyorsítótár-lemaradás (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses8|Yes|Gyorsítótár-lemaradás (szegmens 8)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses9|Yes|Gyorsítótár-lemaradás (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|cachemissrate|Yes|Gyorsítótár-kihagyás aránya|Százalék|cachemissrate||ShardId|
|cacheRead|Yes|Gyorsítótár-olvasás|BytesPerSecond|Maximum||ShardId|
|cacheRead0|Yes|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead1|Yes|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead2|Yes|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead3|Yes|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead4|Yes|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead5|Yes|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead6|Yes|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead7|Yes|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead8|Yes|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead9|Yes|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite|Yes|Gyorsítótár-írás|BytesPerSecond|Maximum||ShardId|
|cacheWrite0|Yes|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite1|Yes|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite2|Yes|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite3|Yes|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite4|Yes|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite5|Yes|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite6|Yes|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite7|Yes|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite8|Yes|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite9|Yes|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|connectedclients|Yes|Csatlakoztatott ügyfelek|Darabszám|Maximum||ShardId|
|connectedclients0|Yes|Csatlakoztatott ügyfelek (0. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients1|Yes|Csatlakoztatott ügyfelek (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients2|Yes|Csatlakoztatott ügyfelek (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients3|Yes|Csatlakoztatott ügyfelek (3. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients4|Yes|Csatlakoztatott ügyfelek (4. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients5|Yes|Csatlakoztatott ügyfelek (5. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients6|Yes|Csatlakoztatott ügyfelek (6. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients7|Yes|Csatlakoztatott ügyfelek (7. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients8|Yes|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients9|Yes|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|Hibák|Yes|Hibák|Darabszám|Maximum||ShardId, ErrorType|
|evictedkeys|Yes|Kizárt kulcsok|Darabszám|Összesen||ShardId|
|evictedkeys0|Yes|Kizárt kulcsok (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys1|Yes|Kizárt kulcsok (1. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys2|Yes|Kizárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys3|Yes|Kizárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys4|Yes|Kizárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys5|Yes|Kizárt kulcsok (5. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys6|Yes|Kizárt kulcsok (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys7|Yes|Kizárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys8|Yes|Kizárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys9|Yes|Kizárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys|Yes|Lejárt kulcsok|Darabszám|Összesen||ShardId|
|expiredkeys0|Yes|Lejárt kulcsok (szilánk 0)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys1|Yes|Lejárt kulcsok (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys2|Yes|Lejárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys3|Yes|Lejárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys4|Yes|Lejárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys5|Yes|Lejárt kulcsok (szilánk 5)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys6|Yes|Lejárt kulcsok (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys7|Yes|Lejárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys8|Yes|Lejárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys9|Yes|Lejárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|getcommands|Yes|Lekérések|Darabszám|Összesen||ShardId|
|getcommands0|Yes|Beolvasás (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands1|Yes|Beolvasás (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands2|Yes|Beolvasás (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands3|Yes|Beolvasás (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands4|Yes|Beolvasás (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|getcommands5|Yes|Beolvasás (szegmens 5)|Darabszám|Összesen||Nincsenek méretek|
|getcommands6|Yes|Beolvasás (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|getcommands7|Yes|Beolvasás (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|getcommands8|Yes|Beolvasás (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|getcommands9|Yes|Beolvasás (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|operationsPerSecond|Yes|Műveletek száma másodpercenként|Darabszám|Maximum||ShardId|
|operationsPerSecond0|Yes|Művelet/másodperc (0. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond1|Yes|Művelet/másodperc (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond2|Yes|Művelet/másodperc (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond3|Yes|Művelet/másodperc (3. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond4|Yes|Művelet/másodperc (4. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond5|Yes|Művelet/másodperc (5. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond6|Yes|Művelet/másodperc (szegmens 6)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond7|Yes|Művelet/másodperc (7. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond8|Yes|Művelet/másodperc (szegmens 8)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond9|Yes|Művelet/másodperc (szegmens 9)|Darabszám|Maximum||Nincsenek méretek|
|percentProcessorTime|Yes|CPU|Százalék|Maximum||ShardId|
|percentProcessorTime0|Yes|CPU (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime1|Yes|CPU (1. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime2|Yes|CPU (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime3|Yes|CPU (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime4|Yes|CPU (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime5|Yes|CPU (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime6|Yes|CPU (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime7|Yes|PROCESSZOR (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime8|Yes|CPU (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime9|Yes|CPU (szilánk 9)|Százalék|Maximum||Nincsenek méretek|
|serverLoad|Yes|Kiszolgáló terhelése|Százalék|Maximum||ShardId|
|serverLoad0|Yes|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad1|Yes|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad2|Yes|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad3|Yes|Kiszolgáló terhelése (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad4|Yes|Kiszolgáló terhelése (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad5|Yes|Kiszolgáló terhelése (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad6|Yes|Kiszolgáló betöltése (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|serverLoad7|Yes|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad8|Yes|Kiszolgáló betöltése (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|serverLoad9|Yes|Kiszolgáló terhelése (szegmens 9)|Százalék|Maximum||Nincsenek méretek|
|setcommands|Yes|Készletek|Darabszám|Összesen||ShardId|
|setcommands0|Yes|Készletek (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands1|Yes|Készletek (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands2|Yes|Készletek (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands3|Yes|Készletek (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands4|Yes|Készletek (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|setcommands5|Yes|Készletek (szilánk 5)|Darabszám|Összesen||Nincsenek méretek|
|setcommands6|Yes|Készletek (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|setcommands7|Yes|Készletek (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|setcommands8|Yes|Készletek (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|setcommands9|Yes|Készletek (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed|Yes|Műveletek összesen|Darabszám|Összesen||ShardId|
|totalcommandsprocessed0|Yes|Összes művelet (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed1|Yes|Összes művelet (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed2|Yes|Összes művelet (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed3|Yes|Összes művelet (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed4|Yes|Összes művelet (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed5|Yes|Összes művelet (5. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed6|Yes|Összes művelet (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed7|Yes|Összes művelet (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed8|Yes|Összes művelet (szegmens 8)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed9|Yes|Összes művelet (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|totalkeys|Yes|Kulcsok összesen|Darabszám|Maximum||ShardId|
|totalkeys0|Yes|Összes kulcs (szilánk 0)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys1|Yes|Összes kulcs (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys2|Yes|Összes kulcs (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys3|Yes|Összes kulcs (3. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys4|Yes|Összes kulcs (4. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys5|Yes|Összes kulcs (5. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys6|Yes|Összes kulcs (szilánk 6)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys7|Yes|Összes kulcs (szegmens 7)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys8|Yes|Összes kulcs (szilánk 8)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys9|Yes|Összes kulcs (szilánk 9)|Darabszám|Maximum||Nincsenek méretek|
|usedmemory|Yes|Felhasznált memória|Bájt|Maximum||ShardId|
|usedmemory0|Yes|Felhasznált memória (0. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory1|Yes|Felhasznált memória (1. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory2|Yes|Felhasznált memória (2. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory3|Yes|Felhasznált memória (3. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory4|Yes|Felhasznált memória (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory5|Yes|Felhasznált memória (5. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory6|Yes|Felhasznált memória (6. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemory7|Yes|Felhasznált memória (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemory8|Yes|Felhasznált memória (8. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory9|Yes|Felhasznált memória (10. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemorypercentage|Yes|Felhasznált memória százalékos aránya|Százalék|Maximum||ShardId|
|usedmemoryRss|Yes|Felhasznált memória RSS-címe|Bájt|Maximum||ShardId|
|usedmemoryRss0|Yes|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss1|Yes|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss2|Yes|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss3|Yes|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss4|Yes|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss5|Yes|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss6|Yes|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss7|Yes|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss8|Yes|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss9|Yes|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||Nincsenek méretek|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási sebessége (bájt/s)|No|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|No|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|RoleInstanceId|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási sebessége (bájt/s)|No|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|Nincsenek méretek|
|Lemez írási sebessége (bájt/s)|No|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|Nincsenek méretek|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|Nincsenek méretek|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincsenek méretek|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|Nincsenek méretek|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincsenek méretek|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|UsedCapacity|No|Felhasznált kapacitás|Bájt|Átlag|Felhasznált fiókkapacitás|Nincsenek méretek|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|BlobCapacity|No|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|No|Blobok száma|Darabszám|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|Yes|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiók Blob service lévő tárolók száma.|Nincsenek méretek|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|IndexCapacity|No|Index kapacitása|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|Nincsenek méretek|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|FileCapacity|No|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileCount|No|Fájlok száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|No|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|Nincsenek méretek|
|FileShareQuota|No|Fájlmegosztás kvótájának mérete|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|FileShareSnapshotCount|No|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|No|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|QueueCapacity|Yes|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|QueueCount|Yes|Várólista száma|Darabszám|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|Nincsenek méretek|
|QueueMessageCount|Yes|Üzenetsor-üzenetek száma|Darabszám|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|Nincsenek méretek|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|TableCapacity|Yes|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|TableCount|Yes|Táblák száma|Darabszám|Átlag|A Storage-fiók Table service található tábla száma.|Nincsenek méretek|
|TableEntityCount|Yes|Tábla entitások száma|Darabszám|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|Nincsenek méretek|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Blokkolt hívások|Darabszám|Összesen|Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|ApiName, OperationName, régió|
|CharactersTrained|Yes|Betanított karakterek|Darabszám|Összesen|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTranslated|Yes|Lefordított karakterek|Darabszám|Összesen|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|ClientErrors|Yes|Ügyfél-hibák|Darabszám|Összesen|Ügyféloldali hibával rendelkező hívások száma (HTTP-válasz kódja 4xx).|ApiName, OperationName, régió|
|DataIn|Yes|A-ben tárolt adatértékek|Bájt|Összesen|A bejövő adat mérete bájtban megadva.|ApiName, OperationName, régió|
|DataOut|Yes|Kimenő adatvesztés|Bájt|Összesen|A kimenő adat mérete bájtban kifejezve.|ApiName, OperationName, régió|
|Késés|Yes|Késés|Ezredmásodpercben|Átlag|Késés ezredmásodpercben.|ApiName, OperationName, régió|
|ProcessedImages|Yes|Feldolgozott rendszerképek|Darabszám|Összesen| A képfeldolgozáshoz tartozó tranzakciók száma.|ApiName, FeatureName, UsageChannel, régió|
|Kiszolgálóhibái|Yes|Kiszolgálói hibák|Darabszám|Összesen|A szolgáltatás belső hibája miatti hívások száma (HTTP-válasz kódja 5xx).|ApiName, OperationName, régió|
|SpeechSessionDuration|Yes|Beszédfelismerési munkamenet időtartama|Másodperc|Összesen|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|SuccessfulCalls|Yes|Sikeres hívások|Darabszám|Összesen|A sikeres hívások száma.|ApiName, OperationName, régió|
|TotalCalls|Yes|Hívások összesen|Darabszám|Összesen|A hívások száma összesen|ApiName, OperationName, régió|
|TotalErrors|Yes|Összes hiba|Darabszám|Összesen|A hibaüzenettel rendelkező hívások teljes száma (HTTP-válasz kódja 4xx vagy 5xx).|ApiName, OperationName, régió|
|TotalTokenCalls|Yes|Jogkivonat-hívások összesen|Darabszám|Összesen|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|
|TotalTransactions|Yes|Tranzakciók összesen|Darabszám|Összesen|A tranzakciók száma összesen|Nincsenek méretek|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|Adatlemez-olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Yes|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez-írási sebesség (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez írási műveletei (művelet/s)|Yes|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Yes|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő hálózat|Yes|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|Nincsenek méretek|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Yes|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|Nincsenek méretek|
|Kimenő hálózat összesen|Yes|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|
|OS-lemez várakozási sorának mélysége|Yes|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Yes|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez QD|Yes|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez olvasási művelet/mp|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez írási műveletei másodpercenként|Yes|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Yes|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|/Lemez QD|Yes|Adatlemez QD (elavult)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|/Lemez olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Yes|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási sebessége (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez írási műveletek másodpercenként|Yes|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Yes|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Yes|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|Adatlemez-olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Yes|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Yes|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|VMName|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|VMName|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Yes|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő hálózat|Yes|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|VMName|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat|Yes|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|VMName|
|Kimenő hálózat összesen|Yes|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|
|OS-lemez várakozási sorának mélysége|Yes|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Yes|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|Operációs rendszer/lemez QD|Yes|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez olvasási művelet/mp|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez írási műveletei másodpercenként|Yes|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Yes|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|/Lemez QD|Yes|Adatlemez QD (elavult)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|/Lemez olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Yes|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási sebessége (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez írási műveletek másodpercenként|Yes|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Yes|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Yes|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. számítás/virtualMachineScaleSets/virtualMachines

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Yes|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Yes|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez várakozási sorának mélysége|Yes|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|Adatlemez-olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Yes|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez-írási sebesség (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez írási műveletei (művelet/s)|Yes|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Bejövő forgalomfolyamok|Yes|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Yes|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő hálózat|Yes|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|Nincsenek méretek|
|Teljes hálózat|Yes|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Yes|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|Nincsenek méretek|
|Kimenő hálózat összesen|Yes|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|
|OS-lemez várakozási sorának mélysége|Yes|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Yes|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez QD|Yes|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez olvasási művelet/mp|Yes|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez írási sebessége (bájt/s)|Yes|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez írási műveletei másodpercenként|Yes|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Yes|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Yes|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|/Lemez QD|Yes|Adatlemez QD (elavult)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|/Lemez olvasási sebesség (bájt/s)|Yes|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Yes|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási sebessége (bájt/s)|Yes|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez írási műveletek másodpercenként|Yes|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Yes|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Yes|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Yes|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|CPU-használat|Darabszám|Átlag|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Yes|Memóriahasználat|Bájt|Átlag|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Yes|Másodpercenként fogadott hálózati bájtok száma|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|Nincsenek méretek|
|NetworkBytesTransmittedPerSecond|Yes|Másodpercenként továbbított hálózati bájtok száma|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|Nincsenek méretek|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|AgentPool CPU-idő|Másodperc|Összesen|AgentPool CPU-idő másodpercben|Nincsenek méretek|
|RunDuration|Yes|Futtatás időtartama|Ezredmásodpercben|Összesen|Futtatás időtartama (ezredmásodpercben)|Nincsenek méretek|
|SuccessfulPullCount|Yes|Sikeres lekérések száma|Darabszám|Átlag|Sikeres Képkeresések száma|Nincsenek méretek|
|SuccessfulPushCount|Yes|Sikeres leküldések száma|Darabszám|Átlag|Sikeres leküldések száma|Nincsenek méretek|
|TotalPullCount|Yes|Lekérések száma összesen|Darabszám|Átlag|A lekérések száma összesen|Nincsenek méretek|
|TotalPushCount|Yes|Leküldések száma összesen|Darabszám|Átlag|Leküldések száma összesen|Nincsenek méretek|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|No|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Darabszám|Átlag|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Nincsenek méretek|
|kube_node_status_allocatable_memory_bytes|No|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Átlag|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Nincsenek méretek|
|kube_node_status_condition|No|Különböző csomóponti feltételek állapota|Darabszám|Átlag|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|No|Hüvelyek száma fázis szerint|Darabszám|Átlag|Hüvelyek száma fázis szerint|fázis, névtér, Pod|
|kube_pod_status_ready|No|A hüvelyek száma üzemkész állapotban|Darabszám|Átlag|A hüvelyek száma üzemkész állapotban|névtér, Pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Sikertelen kérelmek|Darabszám|Összesen|A rendelkezésre álló naplók beolvasása egyéni erőforrás-szolgáltatók számára|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Sikeres kérelmek|Darabszám|Összesen|Az egyéni szolgáltató által kezdeményezett sikeres kérések|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Availablecapacity;)|Yes|Rendelkezésre álló kapacitás|Bájt|Átlag|A rendelkezésre álló kapacitás bájtban a jelentési időszak alatt.|Nincsenek méretek|
|BytesUploadedToCloud|Yes|Feltöltött Felhőbeli bájtok (eszköz)|Bájt|Átlag|Az Azure-ba az eszközről a jelentési időszakban feltöltött bájtok teljes száma.|Nincsenek méretek|
|BytesUploadedToCloudPerShare|Yes|Felhőbeli feltöltött bájtok (megosztás)|Bájt|Átlag|A jelentési időszak során az Azure-ba feltöltött bájtok teljes száma.|Megosztás|
|CloudReadThroughput|Yes|Felhőbeli letöltési sebesség|BytesPerSecond|Átlag|A felhő letöltési sebessége az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudReadThroughputPerShare|Yes|Felhőbeli letöltési teljesítmény (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való letöltési átviteli sebesség a jelentési időszak alatt.|Megosztás|
|CloudUploadThroughput|Yes|Felhőbeli feltöltési sebesség|BytesPerSecond|Átlag|A Felhőbeli feltöltési sebesség az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudUploadThroughputPerShare|Yes|Felhőbeli feltöltési sebesség (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való feltöltési átviteli sebesség a jelentési időszak során.|Megosztás|
|HyperVMemoryUtilization|Yes|Edge-számítás – memóriahasználat|Százalék|Átlag|Használatban lévő RAM mennyisége|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Edge-számítás – százalékos CPU|Százalék|Átlag|CPU-használat százalékos aránya|InstanceName|
|NICReadThroughput|Yes|Olvasási sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter olvasási átviteli sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|NICWriteThroughput|Yes|Írási átviteli sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter írási sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|TotalCapacity|Yes|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|Nincsenek méretek|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRuns|Yes|Sikertelen futtatások|Darabszám|Összesen||pipelineName, activityName|
|SuccessfulRuns|Yes|Sikeres futtatások|Darabszám|Összesen||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|A megszakított tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivityFailedRuns|Yes|Sikertelen tevékenység-futtatási metrikák|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|Yes|A sikeres tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|FactorySizeInGbUnits|Yes|Gyári méret összesen (GB egység)|Darabszám|Maximum||Nincsenek méretek|
|IntegrationRuntimeAvailableMemory|Yes|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableNodeNumber|Yes|Integrációs modul elérhető csomópontok száma|Darabszám|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Integration Runtime-várólista időtartama|Másodperc|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Yes|Integration Runtime CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeQueueLength|Yes|Integration Runtime-várólista hossza|Darabszám|Átlag||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Maximálisan engedélyezett gyári méret (GB egység)|Darabszám|Maximum||Nincsenek méretek|
|MaxAllowedResourceCount|Yes|Engedélyezett entitások maximális száma|Darabszám|Maximum||Nincsenek méretek|
|PipelineCancelledRuns|Yes|Megszakított folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineFailedRuns|Yes|Sikertelen folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineSucceededRuns|Yes|A folyamat sikeresen futtatja a metrikákat|Darabszám|Összesen||FailureType, név|
|ResourceCount|Yes|Entitások száma összesen|Darabszám|Maximum||Nincsenek méretek|
|TriggerCancelledRuns|Yes|Megszakított trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerFailedRuns|Yes|Sikertelen trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerSucceededRuns|Yes|A sikeres trigger metrikákat futtat|Darabszám|Összesen||Név, FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DataRead|Yes|Olvasott információk|Bájt|Összesen|A fiókból beolvasott adatok teljes mennyisége.|Nincsenek méretek|
|DataWritten|Yes|Írt adatértékek|Bájt|Összesen|A fiókba írt összes adatmennyiség.|Nincsenek méretek|
|ReadRequests|Yes|Olvasási kérelmek|Darabszám|Összesen|A fióknak küldött adatolvasási kérelmek száma.|Nincsenek méretek|
|TotalStorage|Yes|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|Nincsenek méretek|
|WriteRequests|Yes|Írási kérelmek|Darabszám|Összesen|A fiókra vonatkozó adatírási kérelmek száma.|Nincsenek méretek|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Yes|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|seconds_behind_master|Yes|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Yes|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Yes|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Yes|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Yes|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Yes|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|seconds_behind_master|Yes|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Yes|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Yes|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Yes|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Yes|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Yes|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Yes|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|pg_replica_log_delay_in_bytes|Yes|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|Nincsenek méretek|
|pg_replica_log_delay_in_seconds|Yes|Replika késése|Másodperc|Maximum|Replika késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Yes|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Yes|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Yes|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Yes|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|IOPS|Yes|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|connections_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|connections_succeeded|Yes|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_percent|Yes|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|IOPS|Yes|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|maximum_used_transactionIDs|Yes|Maximálisan használt tranzakciós azonosítók|Darabszám|Átlag|Maximálisan használt tranzakciós azonosítók|Nincsenek méretek|
|memory_percent|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Yes|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Yes|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|storage_percent|Yes|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Yes|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. commands. kimenő. elhagyása. sikeres|Yes|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. Complete. success|Yes|C2D-üzenetek kézbesítésének befejezése|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. elutasítás. sikeres|Yes|C2D-üzenetek elutasítva|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. Methods. failure|Yes|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|Nincsenek méretek|
|C2D. Methods. requestSize|Yes|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. responseSize|Yes|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. success|Yes|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|Nincsenek méretek|
|C2D. Twin. Read. failure|Yes|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Read. size|Yes|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Twin. Read. success|Yes|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Update. failure|Yes|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2D. Twin. Update. size|Yes|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|C2D. Twin. Update. success|Yes|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2DMessagesExpired|Yes|C2D-üzenetek lejárt (előzetes verzió)|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|Nincsenek méretek|
|konfigurációk|Yes|Konfigurációs mérőszámok|Darabszám|Összesen|A konfigurációs műveletek metrikái|Nincsenek méretek|
|connectedDeviceCount|No|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. endpoints. kimenő. beépített. események|Yes|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. kimenő. eventHubs|Yes|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusQueues|Yes|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusTopics|Yes|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage|Yes|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Blobok|Yes|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Bytes|Yes|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincsenek méretek|
|D2C. endpoints. késleltetés. beépített. események|Yes|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. késleltetés. eventHubs|Yes|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusQueues|Yes|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusTopics|Yes|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. látencia. Storage|Yes|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincsenek méretek|
|D2C. telemetria. kimenő. eldobott|Yes|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincsenek méretek|
|D2C. telemetria. kimenő. tartalék|Yes|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincsenek méretek|
|D2C. telemetria. kimenő. érvénytelen|Yes|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincsenek méretek|
|D2C. telemetria. kimenő. árva|Yes|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincsenek méretek|
|D2C. telemetria. kimenő. sikeres|Yes|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincsenek méretek|
|D2C. telemetria. beáramló. allProtocol|Yes|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sendThrottle|Yes|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sikeres|Yes|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincsenek méretek|
|D2C. Twin. Read. failure|Yes|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincsenek méretek|
|D2C. Twin. Read. size|Yes|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|D2C. Twin. Read. success|Yes|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|D2C. Twin. Update. failure|Yes|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|D2C. Twin. Update. size|Yes|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|D2C. Twin. Update. success|Yes|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincsenek méretek|
|dailyMessageQuotaUsed|Yes|A felhasznált üzenetek teljes száma|Darabszám|Maximum|A jelenleg használt összes üzenet száma|Nincsenek méretek|
|deviceDataUsage|Yes|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|deviceDataUsageV2|Yes|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|Devices. connectedDevices. allProtocol|Yes|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|Devices. totalDevices|Yes|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|EventGridDeliveries|Yes|Event Grid kézbesítések (előzetes verzió)|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát ( https://aka.ms/ioteventgrid) .|Eredmény, EventType|
|EventGridLatency|Yes|Event Grid késés (előzetes verzió)|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|feladatok. cancelJob. hiba|Yes|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|Nincsenek méretek|
|feladatok. cancelJob. sikeres|Yes|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. kész|Yes|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. hiba|Yes|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. sikeres|Yes|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. hiba|Yes|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. sikeres|Yes|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. sikertelen|Yes|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincsenek méretek|
|feladatok. listJobs. hiba|Yes|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|Nincsenek méretek|
|feladatok. listJobs. sikeres|Yes|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. queryJobs. hiba|Yes|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|Nincsenek méretek|
|feladatok. queryJobs. sikeres|Yes|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek méretek|
|RoutingDataSizeInBytesDelivered|Yes|Továbbítási üzenetek mérete bájtban (előzetes verzió)|Bájt|Összesen|Az IoT hub által egy végpontra küldött üzenetek teljes mérete bájtban megadva. A Végpontneve és a EndpointType dimenzió használatával megtekintheti az üzenetek méretét a különböző végpontokra küldött bájtokban. A metrika értéke minden leküldött üzenetnél növekszik, beleértve azt is, hogy az üzenetet több végpontra is továbbítják-e, vagy ha az üzenet többször is ugyanarra a végpontra érkezik.|EndpointType, Végpontneve, RoutingSource|
|RoutingDeliveries|Yes|Útválasztási kézbesítések (előzetes verzió)|Darabszám|Összesen|Azon időpontok száma, IoT Hub az összes végpont felé irányuló üzenetek továbbítására történt kísérlet az Útválasztás használatával. A sikeres vagy sikertelen kísérletek számának megtekintéséhez használja az eredmény dimenziót. Ha szeretné megtekinteni a hiba okát (például érvénytelen, eldobott vagy árva), használja az FailureReasonCategory dimenziót. A Végpontneve és a EndpointType dimenzióval is megtudhatja, hogy hány üzenet lett továbbítva a különböző végpontoknak. A metrika értéke eggyel nő az egyes kézbesítési kísérleteknél, beleértve azt is, hogy az üzenetet több végpontra kézbesítik-e, vagy ha az üzenet többször is ugyanarra a végpontra van kézbesítve.|EndpointType, Végpontneve, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Yes|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodpercben|Átlag|Az üzenet bejövő értékének átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria az üzeneteket a végpontba. A Végpontneve és a EndpointType méretek segítségével megismerheti a különböző végpontok késését.|EndpointType, Végpontneve, RoutingSource|
|totalDeviceCount|No|Összes eszköz (előzetes verzió)|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|twinQueries. hiba|Yes|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|Nincsenek méretek|
|twinQueries.resultSize|Yes|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincsenek méretek|
|twinQueries. success|Yes|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|Nincsenek méretek|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Igazolási kísérletek|Darabszám|Összesen|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, állapot, protokoll|
|DeviceAssignments|Yes|Hozzárendelt eszközök|Darabszám|Összesen|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Regisztrációs kísérletek|Darabszám|Összesen|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, állapot|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Régió hozzáadva|Darabszám|Darabszám|Régió hozzáadva|Régió|
|AutoscaleMaxThroughput|No|Maximális átviteli sebesség|Darabszám|Maximum|Maximális átviteli sebesség|DatabaseName, CollectionName|
|AvailableStorage|No|elavult Rendelkezésre álló tár|Bájt|Összesen|A "rendelkezésre álló tár" el lesz távolítva a Azure Monitorról a 2020 szeptember végén. Cosmos DB a gyűjtemény tárterületének mérete mostantól korlátlan. Az egyetlen korlátozás az, hogy az egyes logikai partíciós kulcsok tárolási mérete 20 GB. A PartitionKeyStatistics a diagnosztikai naplóban engedélyezheti a felső partíciós kulcsok tárolási felhasználásának megismeréséhez. A Cosmos DB tárolási kvótával kapcsolatos további információkért tekintse meg ezt a dokumentációt https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Az elavulás után a fennmaradó riasztási szabályok az elavult metrika esetében még mindig le lesznek tiltva az elavult dátum után.|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|No|Cassandra-kapcsolatok bezárása|Darabszám|Összesen|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|Régió, ClosureReason|
|CassandraConnectorAvgReplicationLatency|No|Cassandra-összekötő átlagos ReplicationLatency|Ezredmásodpercben|Átlag|Cassandra-összekötő átlagos ReplicationLatency|Nincsenek méretek|
|CassandraConnectorReplicationHealthStatus|No|Cassandra Connector replikációs állapotának állapota|Darabszám|Darabszám|Cassandra Connector replikációs állapotának állapota|NotStarted, ReplicationInProgress, hiba|
|CassandraKeyspaceCreate|No|Cassandra-terület létrehozva|Darabszám|Darabszám|Cassandra-terület létrehozva|ResourceName |
|CassandraKeyspaceDelete|No|Cassandra szóköz törölve|Darabszám|Darabszám|Cassandra szóköz törölve|ResourceName |
|CassandraKeyspaceThroughputUpdate|No|Cassandra Space-átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Space-átviteli sebesség frissítve|ResourceName |
|CassandraKeyspaceUpdate|No|Cassandra Space frissítve|Darabszám|Darabszám|Cassandra Space frissítve|ResourceName |
|CassandraRequestCharges|No|Cassandra-kérelmek díjai|Darabszám|Összesen|A Cassandra-kérelmekhez felhasznált RUs|DatabaseName, CollectionName, régió, OperationType, ResourceType|
|CassandraRequests|No|Cassandra-kérelmek|Darabszám|Darabszám|A végrehajtott Cassandra-kérelmek száma|DatabaseName, CollectionName, régió, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Cassandra-tábla létrehozva|Darabszám|Darabszám|Cassandra-tábla létrehozva|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Cassandra-tábla törölve|Darabszám|Darabszám|Cassandra-tábla törölve|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Cassandra Table átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Table átviteli sebesség frissítve|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Cassandra-tábla frissítve|Darabszám|Darabszám|Cassandra-tábla frissítve|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Fiók létrehozva|Darabszám|Darabszám|Fiók létrehozva|Nincsenek méretek|
|DataUsage|No|Adathasználat|Bájt|Összesen|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DeleteAccount|Yes|Fiók törölve|Darabszám|Darabszám|Fiók törölve|Nincsenek méretek|
|DocumentCount|No|Dokumentumok száma|Darabszám|Összesen|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|No|Dokumentum kvótája|Bájt|Összesen|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|GremlinDatabaseCreate|No|Gremlin-adatbázis létrehozva|Darabszám|Darabszám|Gremlin-adatbázis létrehozva|ResourceName |
|GremlinDatabaseDelete|No|Gremlin-adatbázis törölve|Darabszám|Darabszám|Gremlin-adatbázis törölve|ResourceName |
|GremlinDatabaseThroughputUpdate|No|Gremlin adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin adatbázis átviteli sebessége frissítve|ResourceName |
|GremlinDatabaseUpdate|No|Gremlin-adatbázis frissítve|Darabszám|Darabszám|Gremlin-adatbázis frissítve|ResourceName |
|GremlinGraphCreate|No|Gremlin gráf létrehozva|Darabszám|Darabszám|Gremlin gráf létrehozva|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin gráf törölve|Darabszám|Darabszám|Gremlin gráf törölve|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Gremlin gráf átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin gráf átviteli sebessége frissítve|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Gremlin gráf frissítve|Darabszám|Darabszám|Gremlin gráf frissítve|ResourceName, ChildResourceName, |
|IndexUsage|No|Indexhasználat|Bájt|Összesen|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|MetadataRequests|No|Metaadat-kérelmek|Darabszám|Darabszám|A metaadat-kérelmek száma. Cosmos DB az egyes fiókok rendszermetaadat-gyűjteményét kezeli, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, |
|MongoCollectionCreate|No|Mongo-gyűjtemény létrehozva|Darabszám|Darabszám|Mongo-gyűjtemény létrehozva|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Mongo-gyűjtemény törölve|Darabszám|Darabszám|Mongo-gyűjtemény törölve|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|A Mongo-gyűjtési átviteli sebesség frissítve|Darabszám|Darabszám|A Mongo-gyűjtési átviteli sebesség frissítve|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Mongo-gyűjtemény frissítve|Darabszám|Darabszám|Mongo-gyűjtemény frissítve|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Mongo-adatbázis törölve|Darabszám|Darabszám|Mongo-adatbázis törölve|ResourceName |
|MongoDatabaseThroughputUpdate|No|Mongo adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Mongo adatbázis átviteli sebessége frissítve|ResourceName |
|MongoDBDatabaseCreate|No|Mongo-adatbázis létrehozva|Darabszám|Darabszám|Mongo-adatbázis létrehozva|ResourceName |
|MongoDBDatabaseUpdate|No|Mongo-adatbázis frissítve|Darabszám|Darabszám|Mongo-adatbázis frissítve|ResourceName |
|MongoRequestCharge|Yes|Mongo-kérelem díja|Darabszám|Összesen|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequests|Yes|Mongo kérelmek|Darabszám|Darabszám|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequestsCount|No|Mongo kérelmek gyakorisága|CountPerSecond|Átlag|Mongo kérelmek száma másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsDelete|No|Mongo-törlési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-törlési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsInsert|No|Mongo-beszúrási kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-beszúrási szám másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsQuery|No|Mongo-lekérdezési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-lekérdezési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsUpdate|No|Mongo-frissítési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo frissítési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|NormalizedRUConsumption|No|Normalizált RU-felhasználás|Százalék|Maximum|Maximális RU-százalék/perc|CollectionName, DatabaseName, régió, PartitionKeyRangeId|
|ProvisionedThroughput|No|Kiosztott átviteli sebesség|Darabszám|Maximum|Kiosztott átviteli sebesség|DatabaseName, CollectionName|
|RegionFailover|Yes|A régió feladatátvétele megtörtént|Darabszám|Darabszám|A régió feladatátvétele megtörtént|Nincsenek méretek|
|RemoveRegion|Yes|Régió eltávolítva|Darabszám|Darabszám|Régió eltávolítva|Régió|
|ReplicationLatency|Yes|P99 replikáció késése|Ezredmásodpercben|Átlag|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion, TargetRegion|
|ServerSideLatency|No|Kiszolgálóoldali késés|Ezredmásodpercben|Átlag|Kiszolgálóoldali késés|DatabaseName, CollectionName, régió, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Szolgáltatás rendelkezésre állása|Százalék|Átlag|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|Nincsenek méretek|
|SqlContainerCreate|No|SQL-tároló létrehozva|Darabszám|Darabszám|SQL-tároló létrehozva|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|SQL-tároló törölve|Darabszám|Darabszám|SQL-tároló törölve|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|SQL-tároló átviteli sebessége frissítve|Darabszám|Darabszám|SQL-tároló átviteli sebessége frissítve|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|SQL-tároló frissítve|Darabszám|Darabszám|SQL-tároló frissítve|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|SQL-adatbázis létrehozva|Darabszám|Darabszám|SQL-adatbázis létrehozva|ResourceName |
|SqlDatabaseDelete|No|SQL-adatbázis törölve|Darabszám|Darabszám|SQL-adatbázis törölve|ResourceName |
|SqlDatabaseThroughputUpdate|No|Az SQL Database átviteli sebessége frissítve|Darabszám|Darabszám|Az SQL Database átviteli sebessége frissítve|ResourceName |
|SqlDatabaseUpdate|No|SQL-adatbázis frissítve|Darabszám|Darabszám|SQL-adatbázis frissítve|ResourceName |
|TableTableCreate|No|AzureTable tábla létrehozva|Darabszám|Darabszám|AzureTable tábla létrehozva|ResourceName |
|TableTableDelete|No|AzureTable-tábla törölve|Darabszám|Darabszám|AzureTable-tábla törölve|ResourceName |
|TableTableThroughputUpdate|No|AzureTable-tábla átviteli sebessége frissítve|Darabszám|Darabszám|AzureTable-tábla átviteli sebessége frissítve|ResourceName |
|TableTableUpdate|No|AzureTable tábla frissítve|Darabszám|Darabszám|AzureTable tábla frissítve|ResourceName |
|TotalRequests|Yes|Összes kérelem|Darabszám|Darabszám|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|TotalRequestUnits|Yes|Kérelmek összes egysége|Darabszám|Összesen|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|UpdateAccountKeys|Yes|A fiók kulcsai frissítve|Darabszám|Darabszám|A fiók kulcsai frissítve|KeyType|
|UpdateAccountNetworkSettings|Yes|A fiók hálózati beállításai frissítve|Darabszám|Darabszám|A fiók hálózati beállításai frissítve|Nincsenek méretek|
|UpdateAccountReplicationSettings|Yes|A fiók replikációs beállításainak frissítése megtörtént|Darabszám|Darabszám|A fiók replikációs beállításainak frissítése megtörtént|Nincsenek méretek|
|UpdateDiagnosticsSettings|No|Fiók diagnosztikai beállításainak frissítése|Darabszám|Darabszám|Fiók diagnosztikai beállításainak frissítése|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Nincsenek méretek|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincsenek méretek|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincsenek méretek|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/fürtök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|Nincsenek méretek|
|AvailableMemory|No|Szabad memória|Százalék|Maximum|Az Event hub-fürt rendelkezésre álló memóriája a teljes memória százalékában.|Szerepkör|
|CaptureBacklog|No|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|Nincsenek méretek|
|CapturedBytes|No|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|Nincsenek méretek|
|CapturedMessages|No|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|Nincsenek méretek|
|ConnectionsClosed|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|Nincsenek méretek|
|ConnectionsOpened|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|Nincsenek méretek|
|CPU|No|CPU|Százalék|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Szerepkör|
|IncomingBytes|Yes|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|Nincsenek méretek|
|IncomingMessages|Yes|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|Nincsenek méretek|
|IncomingRequests|Yes|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|Nincsenek méretek|
|OutgoingBytes|Yes|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|Nincsenek méretek|
|OutgoingMessages|Yes|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|Nincsenek méretek|
|QuotaExceededErrors|No|Kvótatúllépési hibák.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|Nincsenek méretek|
|Kiszolgálóhibái|No|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|Nincsenek méretek|
|Méret|No|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|Szerepkör|
|SuccessfulRequests|No|Sikeres kérelmek|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|Nincsenek méretek|
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|Nincsenek méretek|
|UserErrors|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|Nincsenek méretek|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|Nincsenek méretek|
|CaptureBacklog|No|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|EntityName|
|CapturedBytes|No|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|EntityName|
|CapturedMessages|No|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|EntityName|
|ConnectionsClosed|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|EntityName|
|ConnectionsOpened|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|EntityName|
|EHABL|Yes|Archivált várakozó üzenetek (elavult)|Darabszám|Összesen|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)|Nincsenek méretek|
|EHAMBS|Yes|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összesen|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)|Nincsenek méretek|
|EHAMSGS|Yes|Archivált üzenetek (elavult)|Darabszám|Összesen|Event hub archivált üzenetek egy névtérben (elavult)|Nincsenek méretek|
|EHINBYTES|Yes|Bejövő bájtok (elavult)|Bájt|Összesen|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincsenek méretek|
|EHINMBS|Yes|Bejövő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)|Nincsenek méretek|
|EHINMSGS|Yes|Bejövő üzenetek (elavult)|Darabszám|Összesen|Névtér összes bejövő üzenete (elavult)|Nincsenek méretek|
|EHOUTBYTES|Yes|Kimenő bájtok (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincsenek méretek|
|EHOUTMBS|Yes|Kimenő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)|Nincsenek méretek|
|EHOUTMSGS|Yes|Kimenő üzenetek (elavult)|Darabszám|Összesen|Névtér összes kimenő üzenete (elavult)|Nincsenek méretek|
|FAILREQ|Yes|Sikertelen kérelmek (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)|Nincsenek méretek|
|IncomingBytes|Yes|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|EntityName|
|IncomingMessages|Yes|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|EntityName|
|IncomingRequests|Yes|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|EntityName|
|INMSGS|Yes|Bejövő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)|Nincsenek méretek|
|INREQS|Yes|Bejövő kérelmek (elavult)|Darabszám|Összesen|Egy névtér összes bejövő küldési kérelme (elavult)|Nincsenek méretek|
|INTERer|Yes|Belső kiszolgálói hibák (elavult)|Darabszám|Összesen|Névtér összes belső kiszolgálóhiba (elavult)|Nincsenek méretek|
|MISCERR|Yes|Egyéb hibák (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)|Nincsenek méretek|
|OutgoingBytes|Yes|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|EntityName|
|OutgoingMessages|Yes|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|EntityName|
|OUTMSGS|Yes|Kimenő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)|Nincsenek méretek|
|QuotaExceededErrors|No|Kvótatúllépési hibák.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName |
|Kiszolgálóhibái|No|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName |
|Méret|No|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|EntityName|
|SuccessfulRequests|No|Sikeres kérelmek|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName |
|SUCCREQ|Yes|Sikeres kérések (elavult)|Darabszám|Összesen|A névtér összes sikeres kérelme (elavult)|Nincsenek méretek|
|SVRBSY|Yes|Kiszolgáló által foglalt hibák (elavult)|Darabszám|Összesen|Egy névtér összes kiszolgáló által foglalt hibája (elavult)|Nincsenek méretek|
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|EntityName |
|UserErrors|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|EntityName |


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/fürtök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Yes|Kategorizált átjáró-kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Átjáróra vonatkozó kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma|HttpStatus|
|NumActiveWorkers|Yes|Aktív feldolgozók száma|Darabszám|Maximum|Aktív feldolgozók száma|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/AutoscaleSettings

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Metrika küszöbértéke|Darabszám|Átlag|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Yes|Megfigyelt kapacitás|Darabszám|Átlag|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.|Nincsenek méretek|
|ObservedMetricValue|Yes|Megfigyelt metrika értéke|Darabszám|Átlag|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Elindított skálázási műveletek|Darabszám|Összesen|A skálázási művelet iránya.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Rendelkezésre állás|Százalék|Átlag|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|No|Rendelkezésre állási tesztek|Darabszám|Darabszám|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Yes|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Yes|Oldal betöltési hálózati kapcsolati ideje|Ezredmásodpercben|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|Nincsenek méretek|
|browserTimings/processingDuration|Yes|Ügyfél feldolgozási ideje|Ezredmásodpercben|Átlag|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|Nincsenek méretek|
|browserTimings/receiveDuration|Yes|Válaszidő fogadása|Ezredmásodpercben|Átlag|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|Nincsenek méretek|
|browserTimings/sendDuration|Yes|Kérelem küldési ideje|Ezredmásodpercben|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|Nincsenek méretek|
|browserTimings/totalDuration|Yes|Böngésző oldalának betöltési ideje|Ezredmásodpercben|Átlag|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|Nincsenek méretek|
|függőségek/darabszám|No|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/időtartam|Yes|Függőség időtartama|Ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/sikertelen|No|Függőségi hívások hibái|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|függőség/típus, függőség/performanceBucket, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kivételek/böngésző|No|Böngészőkivételek|Darabszám|Darabszám|A böngészőben fellépő nem kezelt kivételek száma.|felhő/roleName|
|kivételek/darabszám|Yes|Kivételek|Darabszám|Darabszám|Az összes nem kezelt kivétel összesített száma.|felhő/roleName, felhő/roleInstance, ügyfél/típus|
|kivételek/kiszolgáló|No|Kiszolgálói kivételek|Darabszám|Darabszám|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|felhő/roleName, felhő/roleInstance|
|Oldalmegtekintések/darabszám|Yes|Lapok nézetei|Darabszám|Darabszám|Lapok nézeteinek száma.|művelet/szintetikus, felhő/roleName|
|Oldalmegtekintések/időtartam|Yes|Oldal nézet betöltési ideje|Ezredmásodpercben|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus, felhő/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Kivételek aránya|CountPerSecond|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|felhő/roleInstance|
|performanceCounters/memoryAvailableBytes|Yes|Igénybe vehető memória|Bájt|Átlag|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|felhő/roleInstance|
|performanceCounters/processCpuPercentage|Yes|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|felhő/roleInstance|
|performanceCounters/processIOBytesPerSecond|Yes|Folyamat i/o-sebessége|BytesPerSecond|Átlag|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|felhő/roleInstance|
|performanceCounters/processorCpuPercentage|Yes|Processzoridő|Százalék|Átlag|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|felhő/roleInstance|
|performanceCounters/processPrivateBytes|Yes|Saját bájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|felhő/roleInstance|
|performanceCounters/requestExecutionTime|Yes|HTTP-kérelem végrehajtási ideje|Ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|felhő/roleInstance|
|performanceCounters/requestsInQueue|Yes|HTTP-kérelmek az alkalmazás-várólistában|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájának hossza|felhő/roleInstance|
|performanceCounters/requestsPerSecond|Yes|HTTP-kérelmek gyakorisága|CountPerSecond|Átlag|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|felhő/roleInstance|
|kérelmek/darabszám|No|Kiszolgálói kérelmek|Darabszám|Darabszám|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/időtartam|Yes|Kiszolgáló válaszideje|Ezredmásodpercben|Átlag|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|No|Sikertelen kérelmek|Darabszám|Darabszám|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések >= 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|No|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Átlag|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|nyomkövetés/darabszám|Yes|Hívásláncok|Darabszám|Darabszám|Nyomkövetési dokumentumok száma|nyomkövetés/severityLevel, művelet/szintetikus, felhő/roleName, felhő/roleInstance|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Tár teljes rendelkezésre állása|Százalék|Átlag|Tár kéréseinek rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Tár teljes telítettsége|Százalék|Átlag|Tár kapacitásának használata|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Összes szolgáltatási API-találat|Darabszám|Darabszám|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Yes|Általános szolgáltatás API-késése|Ezredmásodpercben|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Összes szolgáltatás API-eredményei|Darabszám|Darabszám|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Köteg Blobok száma|Darabszám|Átlag|Adatforrások száma egy összesített kötegben a betöltéshez.|Adatbázis|
|BatchDuration|Yes|Köteg időtartama|Másodperc|Átlag|A betöltési folyamat összesítési fázisának időtartama.|Adatbázis|
|BatchesProcessed|Yes|Feldolgozott kötegek|Darabszám|Átlag|A betöltéshez összesített kötegek száma. A Batch befejezésének oka: azt határozza meg, hogy a köteg elérte-e a kötegelt házirend által beállított időkorlátot, az adatméretet vagy a fájlok számát.|Adatbázis, SealReason|
|BatchSize|Yes|Köteg mérete|Bájt|Átlag|Tömörítetlen várt adatméret egy összesített kötegben a betöltéshez.|Adatbázis|
|CacheUtilization|Yes|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|Nincsenek méretek|
|ContinuousExportMaxLatenessMinutes|Yes|Folyamatos exportálás maximális késői|Darabszám|Maximum|A fürt folyamatos exportálási feladatai által jelentett késői idő (percben)|Nincsenek méretek|
|ContinuousExportNumOfRecordsExported|Yes|Folyamatos exportálás – számú exportált rekord|Darabszám|Összesen|Az exportált rekordok száma az exportálási művelet során írt összes tárolási összetevő esetében|ContinuousExportName, adatbázis|
|ContinuousExportPendingCount|Yes|Folyamatos exportálás függőben lévő darabszáma|Darabszám|Maximum|A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma|Nincsenek méretek|
|ContinuousExportResult|Yes|Folyamatos exportálás eredménye|Darabszám|Darabszám|Azt jelzi, hogy a folyamatos exportálás sikeres vagy sikertelen volt-e|ContinuousExportName, eredmény, adatbázis|
|CPU|Yes|CPU|Százalék|Átlag|CPU-kihasználtsági szint|Nincsenek méretek|
|EventsProcessedForEventHubs|Yes|Feldolgozott események (Event/IoT hubok esetében)|Darabszám|Összesen|A fürt által az esemény/IoT Hub betöltéskor feldolgozott események száma|EventStatus|
|ExportUtilization|Yes|Exportálás kihasználtsága|Százalék|Maximum|Exportálás kihasználtsága|Nincsenek méretek|
|IngestionLatencyInSeconds|Yes|Betöltési késleltetés (másodperc)|Másodperc|Átlag|A lenyelési idő a forrástól (például az üzenet a EventHub) a fürthöz másodpercek alatt|Nincsenek méretek|
|IngestionResult|Yes|Betöltés eredménye|Darabszám|Darabszám|Betöltési műveletek száma|IngestionResultDetails|
|IngestionUtilization|Yes|Betöltés kihasználtsága|Százalék|Átlag|A használatban lévő betöltési tárolóhelyek aránya a fürtben|Nincsenek méretek|
|IngestionVolumeInMB|Yes|Betöltési mennyiség (MB)|Darabszám|Összesen|A betöltött adatmennyiség teljes mennyisége a fürtön (MB)|Nincsenek méretek|
|InstanceCount|Yes|Példányszám|Darabszám|Átlag|Példányok száma összesen|Nincsenek méretek|
|KeepAlive|Yes|Életben tartása|Darabszám|Átlag|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|Nincsenek méretek|
|QueryDuration|Yes|Lekérdezés időtartama|Ezredmásodpercben|Átlag|Lekérdezések időtartama másodpercben|QueryStatus|
|SteamingIngestRequestRate|Yes|Folyamatos átviteli kérelmek gyakorisága|Darabszám|RateRequestsPerSecond|Folyamatos átviteli kérelmek aránya (kérelmek/másodperc)|Nincsenek méretek|
|StreamingIngestDataRate|Yes|Adatforgalom adatátviteli sebessége|Darabszám|Átlag|Adatforgalom adatátviteli sebessége (MB/s)|Nincsenek méretek|
|StreamingIngestDuration|Yes|Folyamatos átvitel időtartama|Ezredmásodpercben|Átlag|Folyamatos átvitel időtartama (ezredmásodperc)|Nincsenek méretek|
|StreamingIngestResults|Yes|Folyamatos átvitel eredménye|Darabszám|Átlag|Folyamatos átvitel eredménye|Eredmény|
|TotalNumberOfConcurrentQueries|Yes|Az egyidejű lekérdezések teljes száma|Darabszám|Összesen|Az egyidejű lekérdezések teljes száma|Nincsenek méretek|
|TotalNumberOfExtents|Yes|Egységek teljes száma|Darabszám|Összesen|Adategységek teljes száma|Nincsenek méretek|
|TotalNumberOfThrottledCommands|Yes|A szabályozott parancsok teljes száma|Darabszám|Összesen|A szabályozott parancsok teljes száma|CommandType|
|TotalNumberOfThrottledQueries|Yes|A szabályozott lekérdezések teljes száma|Darabszám|Összesen|A szabályozott lekérdezések teljes száma|Nincsenek méretek|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionsCompleted|Yes|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Yes|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Yes|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsStarted|Yes|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Yes|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionSuccessLatency|Yes|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Yes|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Összekötő memóriahasználat integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|integrációs szolgáltatási környezet összekötő-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|Nincsenek méretek|
|RunFailurePercentage|Yes|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|RunLatency|Yes|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunsCancelled|Yes|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Yes|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Yes|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsStarted|Yes|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Yes|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunStartThrottledEvents|Yes|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunSuccessLatency|Yes|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Yes|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|TriggerFireLatency|Yes|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerLatency|Yes|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggersCompleted|Yes|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Yes|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Yes|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Yes|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersStarted|Yes|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Yes|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerSuccessLatency|Yes|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Yes|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionsCompleted|Yes|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Yes|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Yes|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsStarted|Yes|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Yes|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionSuccessLatency|Yes|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Yes|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|BillableActionExecutions|Yes|Számlázható műveletek végrehajtása|Darabszám|Összesen|A számlázott munkafolyamat-műveletek végrehajtásának száma.|Nincsenek méretek|
|BillableTriggerExecutions|Yes|Számlázható trigger-végrehajtások|Darabszám|Összesen|A számlázott munkafolyamat-trigger-végrehajtások száma.|Nincsenek méretek|
|BillingUsageNativeOperation|Yes|A natív művelet végrehajtásához használt számlázási használat|Darabszám|Összesen|A számlázott natív művelet-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStandardConnector|Yes|A standard szintű összekötők végrehajtásának számlázási használata|Darabszám|Összesen|A számlázott szabványos összekötő-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStorageConsumption|Yes|Számlázási használat a tárolási felhasználás végrehajtásához|Darabszám|Összesen|A számlázható tárterület-használat végrehajtásának száma.|Nincsenek méretek|
|RunFailurePercentage|Yes|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|RunLatency|Yes|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunsCancelled|Yes|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Yes|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Yes|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsStarted|Yes|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Yes|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunStartThrottledEvents|Yes|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunSuccessLatency|Yes|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Yes|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|TotalBillableExecutions|Yes|Számlázandó végrehajtások összesen|Darabszám|Összesen|A számlázandó munkafolyamat-végrehajtások száma.|Nincsenek méretek|
|TriggerFireLatency|Yes|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerLatency|Yes|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggersCompleted|Yes|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Yes|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Yes|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Yes|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersStarted|Yes|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Yes|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerSuccessLatency|Yes|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Yes|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Yes|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Yes|Aktív csomópontok|Darabszám|Átlag|Acitve-csomópontok száma Ezek azok a csomópontok, amelyek aktívan futtatnak egy feladatot.|Forgatókönyv, ClusterName|
|A kért futtatások megszakítása|Yes|A kért futtatások megszakítása|Darabszám|Összesen|Azon futtatások száma, amelyek esetében a rendszer a megszakítást kérelmezte ehhez a munkaterülethez. A Count akkor frissül, ha a lemondási kérelem érkezett a futtatáshoz.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Megszakított futtatások|Yes|Megszakított futtatások|Darabszám|Összesen|A munkaterülethez megszakított futtatások száma. A rendszer a Futtatás sikeres megszakítása után frissíti a darabszámot.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Befejezett futtatások|Yes|Befejezett futtatások|Darabszám|Összesen|A munkaterületen sikeresen befejeződött a futtatások száma. A számláló a Futtatás befejeződése után frissül, és a rendszer a kimenetet gyűjtötte.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|CpuUtilization|Yes|CpuUtilization|Darabszám|Átlag|A memória kihasználtságának százalékos aránya egy CPU-csomóponton. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, ClusterName|
|Hibák|Yes|Hibák|Darabszám|Összesen|A munkaterületen futtatott hibák száma. A Count frissítése akkor történik meg, amikor a Futtatás hibát észlel.|Forgatókönyv|
|Sikertelen futtatások|Yes|Sikertelen futtatások|Darabszám|Összesen|A munkaterületen nem sikerült a futtatások száma. A Count a Futtatás meghiúsulása esetén frissül.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Futtatások véglegesítése|Yes|Futtatások véglegesítése|Darabszám|Összesen|A munkaterületre vonatkozó véglegesítési állapotba lépett futtatások száma. A Count akkor frissül, ha egy Futtatás befejeződött, de a kimeneti gyűjtemény még folyamatban van.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|GpuUtilization|Yes|GpuUtilization|Darabszám|Átlag|A memória kihasználtságának százalékos aránya egy GPU-csomóponton. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, DeviceId, ClusterName|
|Üresjárati magok|Yes|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Yes|Tétlen csomópontok|Darabszám|Átlag|Az üresjárati csomópontok száma. Az üresjárati csomópontok azok a csomópontok, amelyek nem futtatnak feladatokat, de az elérhetővé tehetik az új feladatot.|Forgatókönyv, ClusterName|
|Magok kihagyása|Yes|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Yes|Csomópontok elhagyása|Darabszám|Átlag|A csomópontok elhagyásának száma. A csomópontok elhagyják azokat a csomópontokat, amelyek éppen befejezték a feladatok feldolgozását, és az inaktív állapotba kerülnek.|Forgatókönyv, ClusterName|
|Modell-üzembehelyezés sikertelen|Yes|Modell-üzembehelyezés sikertelen|Darabszám|Összesen|A munkaterületen sikertelen telepítési modellek száma|Forgatókönyv, StatusCode|
|Modell-üzembehelyezés elindítva|Yes|Modell-üzembehelyezés elindítva|Darabszám|Összesen|A munkaterületen elindított modellek központi telepítésének száma|Forgatókönyv|
|Modell-üzembehelyezés sikerült|Yes|Modell-üzembehelyezés sikerült|Darabszám|Összesen|A munkaterületen sikeres központi telepítési modellek száma|Forgatókönyv|
|A modell regisztrálása nem sikerült|Yes|A modell regisztrálása nem sikerült|Darabszám|Összesen|A munkaterületen meghiúsult modell-regisztrációk száma|Forgatókönyv, StatusCode|
|A modell regisztrálása sikerült|Yes|A modell regisztrálása sikerült|Darabszám|Összesen|A munkaterületen sikeres modell-regisztrációk száma|Forgatókönyv|
|Nem válaszoló futtatások|Yes|Nem válaszoló futtatások|Darabszám|Összesen|A munkaterületre nem válaszoló futtatások száma. A Count akkor frissül, ha a Futtatás nem válaszol.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Nem indult el a futtatások|Yes|Nem indult el a futtatások|Darabszám|Összesen|A nem elindított futtatások száma ehhez a munkaterülethez. A Count frissítése akkor történik meg, ha egy futtatási kérelem érkezik, de a futtatási adatok még nem lettek feltöltve. |Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Előzik magok|Yes|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Yes|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma Ezek a csomópontok az alacsony prioritású csomópontok, amelyek a rendelkezésre álló csomópont-készletből származnak.|Forgatókönyv, ClusterName|
|Futtatások előkészítése|Yes|Futtatások előkészítése|Darabszám|Összesen|A munkaterületre felkészülő futtatások száma. A Count akkor frissül, ha egy Futtatás előkészítési állapotba kerül, amíg a futtatási környezet előkészítése folyamatban van.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Kiépítési futtatások|Yes|Kiépítési futtatások|Darabszám|Összesen|A munkaterülethez kiépített futtatások száma. A Count frissítése akkor történik meg, ha egy Futtatás a számítási cél létrehozására vagy kiépítési célra vár.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Várólistán lévő futtatások|Yes|Várólistán lévő futtatások|Darabszám|Összesen|Azon futtatások száma, amelyek várólistára kerülnek ehhez a munkaterülethez. A Count akkor frissül, ha egy Futtatás várólistára kerül a számítási célra. Akkor fordulhat elő, ha a szükséges számítási csomópontok üzemkész állapotra várnak.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Kvóta kihasználtsága (%)|Yes|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Elindított futtatások|Yes|Elindított futtatások|Darabszám|Összesen|A munkaterületen futó futtatások száma. A Count akkor frissül, ha a Futtatás a szükséges erőforrásokon fut.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Futtatások indítása|Yes|Futtatások indítása|Darabszám|Összesen|A munkaterülethez elindított futtatások száma. A Count frissítése a futtatási és futtatási adatok (például a futtatási azonosító) létrehozási kérelme alapján történt.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Magok összesen|Yes|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Csomópontok összesen|Yes|Csomópontok összesen|Darabszám|Átlag|A csomópontok száma összesen. Ez az összeg magában foglalja az aktív csomópontok, a tétlen csomópontok, a nem használható csomópontok, a Premepted csomópontok és a csomópontok elhagyását|Forgatókönyv, ClusterName|
|Használhatatlan magok|Yes|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Yes|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma Néhány feloldhatatlan probléma miatt nem használható csomópontok nem működőképesek. Az Azure újrahasznosítja ezeket a csomópontokat.|Forgatókönyv, ClusterName|
|Figyelmeztetések|Yes|Figyelmeztetések|Darabszám|Összesen|A futtatási figyelmeztetések száma ebben a munkaterületen. A darabszám akkor frissül, amikor egy Futtatás figyelmeztetést észlel.|Forgatókönyv|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|
|Használat|No|Használat|Darabszám|Darabszám|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Eszközök száma|Darabszám|Átlag|Az aktuális Media Service-fiókban már létrehozott eszközök száma|Nincsenek méretek|
|AssetQuota|Yes|Eszköz kvótája|Darabszám|Átlag|Hány eszköz engedélyezett a jelenlegi Media Service-fiókhoz|Nincsenek méretek|
|AssetQuotaUsedPercentage|Yes|Eszköz kvótájának kihasználtsága (%)|Százalék|Átlag|Az eszköz által használt százalék a jelenlegi Media Service-fiókban|Nincsenek méretek|
|ContentKeyPolicyCount|Yes|Tartalmi kulcsokra vonatkozó szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejött a tartalmi kulcs házirendjeinek száma|Nincsenek méretek|
|ContentKeyPolicyQuota|Yes|Tartalmi kulcs házirend-kvótája|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó tartalmi kulcsokra vonatkozó házirendek száma|Nincsenek méretek|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|Százalék|Átlag|A tartalmi kulcsra vonatkozó házirend százaléka a jelenlegi Media Service-fiókban|Nincsenek méretek|
|StreamingPolicyCount|Yes|Folyamatos átviteli szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejöttek a folyamatos átviteli házirendek|Nincsenek méretek|
|StreamingPolicyQuota|Yes|Streaming Policy-kvóta|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó folyamatos átviteli házirendek száma|Nincsenek méretek|
|StreamingPolicyQuotaUsedPercentage|Yes|Adatfolyam-házirend kvótájának kihasználtsága (%)|Százalék|Átlag|A streaming Policy használt százalék a jelenlegi Media Service-fiókban|Nincsenek méretek|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Mediaservices/streamingEndpoints

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve.|OutputFormat|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Továbbítási végpontra irányuló kérelmek.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|A Befejezés végének késése|Ezredmásodpercben|Átlag|A sikeres kérelmek átlagos késése ezredmásodpercben.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Készlet lefoglalt mérete|Bájt|Átlag|A készlet kiépített mérete|Nincsenek méretek|
|VolumePoolAllocatedUsed|Yes|A kötet méretéhez lefoglalt készlet|Bájt|Átlag|A készlet lefoglalt felhasznált mérete|Nincsenek méretek|
|VolumePoolTotalLogicalSize|Yes|Készlet felhasznált mérete|Bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|Nincsenek méretek|
|VolumePoolTotalSnapshotSize|Yes|A készlethez tartozó pillanatképek teljes mérete|Bájt|Átlag|A készletben lévő összes kötet pillanatkép-méretének összege|Nincsenek méretek|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/kötetek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Olvasási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|Nincsenek méretek|
|AverageWriteLatency|Yes|Írási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Írási késleltetés átlagos száma ezredmásodpercben|Nincsenek méretek|
|CbsVolumeBackupActive|Yes|Kötet biztonsági mentésének aktív állapota|Darabszám|Átlag|A biztonsági mentés jelenleg fel van függesztve a kötetre.|Nincsenek méretek|
|CbsVolumeLogicalBackupBytes|Yes|Logikai bájtok biztonsági mentése|Bájt|Átlag|A kötetre vonatkozó, nem tömörített/nem titkosított bájtok biztonsági mentése Toatl.|Nincsenek méretek|
|CbsVolumeOperationComplete|Yes|Művelet állapota|Darabszám|Átlag|A legutóbbi biztonsági mentési/visszaállítási művelet sikeres volt.|Nincsenek méretek|
|CbsVolumeOperationTransferredBytes|Yes|Átvitt bájtok a művelethez|Bájt|Átlag|A legutóbbi biztonsági mentési/visszaállítási művelethez továbbított bájtok száma összesen.|Nincsenek méretek|
|CbsVolumeProtected|Yes|Kötet védett állapota|Darabszám|Átlag|A kötetet a Cloud Backup szolgáltatás védi.|Nincsenek méretek|
|ReadIops|Yes|IOPS olvasása|CountPerSecond|Átlag|Olvasási/kimenő műveletek másodpercenként|Nincsenek méretek|
|VolumeAllocatedSize|Yes|Kötet lefoglalt mérete|Bájt|Átlag|Kötet kiépített mérete|Nincsenek méretek|
|VolumeLogicalSize|Yes|Kötet felhasznált mérete|Bájt|Átlag|A kötet logikai mérete (felhasznált bájtok)|Nincsenek méretek|
|VolumeSnapshotSize|Yes|Kötet pillanatképének mérete|Bájt|Átlag|A köteten található összes pillanatkép mérete|Nincsenek méretek|
|WriteIops|Yes|IOPS írása|CountPerSecond|Átlag|Írási/kimenő műveletek másodpercenként|Nincsenek méretek|
|XregionReplicationHealthy|Yes|A kötet replikálási állapota Kifogástalan|Darabszám|Átlag|A kapcsolat feltétele, 1 vagy 0.|Nincsenek méretek|
|XregionReplicationLagTime|Yes|Kötet replikációs késési ideje|Másodperc|Átlag|Az az időtartam másodpercben, ameddig a tükrözött adatmennyiség a forrás mögött marad.|Nincsenek méretek|
|XregionReplicationLastTransferDuration|Yes|Kötet replikálásának utolsó átvitelének időtartama|Másodperc|Átlag|Az utolsó átvitel befejezéséhez szükséges idő másodpercben.|Nincsenek méretek|
|XregionReplicationLastTransferSize|Yes|Kötet replikálásának utolsó átvitelének mérete|Bájt|Átlag|Az utolsó átvitel részeként átvitt bájtok teljes száma.|Nincsenek méretek|
|XregionReplicationRelationshipProgress|Yes|Kötet replikálásának folyamata|Bájt|Átlag|Az aktuális adatátviteli művelethez továbbított adatok teljes mennyisége.|Nincsenek méretek|
|XregionReplicationRelationshipTransferring|Yes|A kötet-replikálás átadása|Darabszám|Átlag|Azt jelzi, hogy a kötet replikálásának állapota "átvitel".|Nincsenek méretek|
|XregionReplicationTotalTransferBytes|Yes|Kötet replikálásának teljes átvitele|Bájt|Átlag|A kapcsolathoz továbbított összesített bájtok száma.|Nincsenek méretek|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway teljes idő|Ezredmásodpercben|Átlag|A kérelem feldolgozásának és a válasz elküldésekor elvégezhető átlagos idő. Ez az időszak átlaga, amikor a Application Gateway megkapja egy HTTP-kérelem első bájtját, amikor a válasz küldése művelet befejeződik. Fontos megjegyezni, hogy ez általában a Application Gateway feldolgozási időt, az időpontot, amikor a kérés és a válasz csomagjai a hálózaton keresztül utaznak, valamint a háttér-kiszolgáló válaszának időpontját.|Figyelő|
|AvgRequestCountPerHealthyHost|No|Percenkénti kérelmek száma kifogástalan állapotú gazdagépen|Darabszám|Átlag|Kérelmek átlagos száma percenként, a készletben lévő kifogástalan állapotú gazdagépek esetében|BackendSettingsPool|
|BackendConnectTime|No|Háttérbeli kapcsolat ideje|Ezredmásodpercben|Átlag|A háttér-kiszolgálóval létesített kapcsolatok létrehozásához töltött idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendFirstByteResponseTime|No|Háttérbeli első bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz fejléc első bájtjának fogadása között eltelt idő, a háttér-kiszolgáló feldolgozási idejének megközelítő időpontja|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendLastByteResponseTime|No|Háttérbeli utolsó bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz törzse utolsó bájtjának fogadása között eltelt idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendResponseStatus|Yes|Háttérbeli válasz állapota|Darabszám|Összesen|A háttérbeli tagok által létrehozott HTTP-válasz kódok száma. Ez nem tartalmazza a Application Gateway által létrehozott válasz-kódokat.|BackendServer, Háttérkészletek, Backendhttpsetting értékre, HttpStatusGroup|
|BlockedCount|Yes|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Szerepkörcsoportot, RuleId|
|BlockedReqCount|Yes|Webalkalmazási tűzfal letiltott kérelmek száma|Darabszám|Összesen|Webalkalmazási tűzfal letiltott kérelmek száma|Nincsenek méretek|
|BytesReceived|Yes|Fogadott bájtok száma|Bájt|Összesen|A Application Gateway által az ügyfelektől fogadott bájtok teljes száma|Figyelő|
|BytesSent|Yes|Eljuttatott bájtok|Bájt|Összesen|A Application Gateway által az ügyfeleknek eljuttatott bájtok teljes száma|Figyelő|
|CapacityUnits|No|Aktuális kapacitási egységek|Darabszám|Átlag|Felhasznált kapacitási egységek|Nincsenek méretek|
|ClientRtt|No|Ügyfél-RTT|Ezredmásodpercben|Átlag|Az ügyfelek és a Application Gateway közötti átlagos menetidő. Ez a metrika azt jelzi, hogy mennyi időt vesz igénybe a kapcsolatok és a visszaküldési visszaigazolás|Figyelő|
|ComputeUnits|No|Aktuális számítási egységek|Darabszám|Átlag|Felhasznált számítási egységek|Nincsenek méretek|
|CpuUtilization|No|Processzor kihasználtsága|Százalék|Átlag|A Application Gateway aktuális CPU-kihasználtsága|Nincsenek méretek|
|Összege|Yes|Aktuális kapcsolatok|Darabszám|Összesen|Application Gatewaysal létesített aktuális kapcsolatok száma|Nincsenek méretek|
|EstimatedBilledCapacityUnits|No|Becsült számlázott kapacitási egységek|Darabszám|Átlag|A felszámított becsült kapacitási egységek|Nincsenek méretek|
|FailedRequests|Yes|Sikertelen kérelmek|Darabszám|Összesen|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Fix számlázandó kapacitásegységek|Darabszám|Átlag|Minimálisan felszámított kapacitási egységek|Nincsenek méretek|
|HealthyHostCount|Yes|Kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Az egészséges háttérbeli gazdagépek száma|BackendSettingsPool|
|MatchedCount|Yes|Webalkalmazási tűzfal teljes szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal – teljes szabály eloszlása a bejövő forgalomhoz|Szerepkörcsoportot, RuleId|
|NewConnectionsPerSecond|No|Új kapcsolatok másodpercenként|CountPerSecond|Átlag|Új kapcsolatok másodpercenként létesített Application Gateway|Nincsenek méretek|
|ResponseStatus|Yes|Válasz állapota|Darabszám|Összesen|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Átviteli sebesség|No|Átviteli sebesség|BytesPerSecond|Átlag|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|Nincsenek méretek|
|TlsProtocol|Yes|Ügyfél TLS protokoll|Darabszám|Összesen|Azoknak a TLS-és nem TLS-kérelmeknek a száma, amelyek az ügyfél által kezdeményezett Application Gatewaykal létesített kapcsolatban állnak. A TLS protokoll terjesztésének megtekintéséhez szűrje a Dimension TLS protokollt.|Figyelő, TlsProtocol|
|TotalRequests|Yes|Összes kérelem|Darabszám|Összesen|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|UnhealthyHostCount|Yes|Nem kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Sérült háttérbeli gazdagépek száma|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Alkalmazás-szabályok találatok száma|Darabszám|Összesen|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|DataProcessed|Yes|Feldolgozott adatfeldolgozás|Bájt|Összesen|A tűzfal által feldolgozott adatmennyiség teljes mennyisége|Nincsenek méretek|
|FirewallHealth|Yes|Tűzfal állapota|Százalék|Átlag|A tűzfal általános állapotát jelzi|Állapot, ok|
|NetworkRuleHit|Yes|Hálózati szabályok találatok száma|Darabszám|Összesen|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|
|SNATPortUtilization|Yes|SNAT-portok kihasználtsága|Százalék|Átlag|Jelenleg használatban lévő kimenő SNAT-portok százalékos aránya|Protokoll|
|Átviteli sebesség|No|Átviteli sebesség|BitsPerSecond|Átlag|A tűzfal által feldolgozott átviteli sebesség|Nincsenek méretek|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|Yes|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek méretek|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Lekérdezési kötet|Darabszám|Összesen|A DNS-zónák számára kiszolgált lekérdezések száma|Nincsenek méretek|
|RecordSetCapacityUtilization|No|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|Nincsenek méretek|
|RecordSetCount|Yes|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy DNS-zónában|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|ARP rendelkezésre állása|Százalék|Átlag|Az ARP és a MSEE közötti rendelkezésre állás az összes társ felé.|PeeringType, társ|
|BgpAvailability|Yes|BGP rendelkezésre állása|Százalék|Átlag|A BGP rendelkezésre állása a MSEE az összes társ felé.|PeeringType, társ|
|BitsInPerSecond|No|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeringType|
|BitsOutPerSecond|No|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeringType|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeredCircuitSKey|
|QosDropBitsInPerSecond|No|DroppedInBitsPerSecond|CountPerSecond|Átlag|Bejövő adatforgalom (bit/mp)|Nincsenek méretek|
|QosDropBitsOutPerSecond|No|DroppedOutBitsPerSecond|CountPerSecond|Átlag|Kimenő adatforgalom másodpercenkénti száma|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/társak

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|Yes|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Kapcsolatnév|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Kapcsolatnév|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Darabszám|Átlag|A port rendszergazdai állapota|Hivatkozás|
|LineProtocol|Yes|LineProtocol|Darabszám|Átlag|A port protokolljának állapota|Hivatkozás|
|PortBitsInPerSecond|Yes|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Hivatkozás|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Hivatkozás|
|RxLightLevel|Yes|RxLightLevel|Darabszám|Átlag|Rx-fény szintje dBm-ben|Hivatkozás, sáv|
|TxLightLevel|Yes|TxLightLevel|Darabszám|Átlag|TX-fény szintje dBm-ben|Hivatkozás, sáv|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Háttér állapotának százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|BackendRequestCount|Yes|Háttérbeli kérelmek száma|Darabszám|Összesen|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Yes|Háttérbeli kérelmek késése|Ezredmásodpercben|Átlag|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Háttérrendszer|
|BillableResponseSize|Yes|Számlázható válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött számlázandó bájtjainak (minimális 2KB) száma az ügyfeleknek.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Yes|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|Kérelem mérete|Bájt|Összesen|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Yes|Válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Lefoglalt SNAT-portok|Darabszám|Átlag|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Yes|Bájtok száma|Darabszám|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, irány|
|DipAvailability|Yes|Állapotadat-mintavétel|Darabszám|Átlag|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Yes|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SnatConnectionCount|Yes|SNAT-kapcsolatok száma|Darabszám|Összesen|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|UsedSnatPorts|No|Használt SNAT-portok|Darabszám|Átlag|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Yes|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Fogadott bájtok száma|Bájt|Összesen|A hálózati adapter által fogadott bájtok száma|Nincsenek méretek|
|BytesSentRate|Yes|Eljuttatott bájtok|Bájt|Összesen|A hálózati adapter által eljuttatott bájtok száma|Nincsenek méretek|
|PacketsReceivedRate|Yes|Fogadott csomagok|Darabszám|Összesen|A hálózati adapter által fogadott csomagok száma|Nincsenek méretek|
|PacketsSentRate|Yes|Küldött csomagok|Darabszám|Összesen|A hálózati adapter által küldött csomagok száma|Nincsenek méretek|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Átlagos menetidő (MS)|Ezredmásodpercben|Átlag|A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS)|Nincsenek méretek|
|ChecksFailedPercent|Yes|Sikertelen ellenőrzések százalékos aránya (előzetes verzió)|Százalék|Átlag|a kapcsolat figyelési ellenőrzése nem sikerült|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|ProbesFailedPercent|Yes|%-Os mintavétel sikertelen|Százalék|Átlag|a kapcsolat figyelési mintavételének%-a meghiúsult|Nincsenek méretek|
|RoundTripTimeMs|Yes|Oda-és visszaút időpontja (MS) (előzetes verzió)|Ezredmásodpercben|Átlag|A kapcsolat figyelési ellenőrzésének időkorlátja ezredmásodpercben|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bájtok száma|Darabszám|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|BytesDroppedDDoS|Yes|Bejövő bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő bájtok elvetve DDoS|Nincsenek méretek|
|BytesForwardedDDoS|Yes|Bejövő bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő bájtok továbbított DDoS|Nincsenek méretek|
|BytesInDDoS|Yes|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|Nincsenek méretek|
|DDoSTriggerSYNPackets|Yes|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|Nincsenek méretek|
|DDoSTriggerTCPPackets|Yes|Bejövő TCP-csomagok a DDoS-mérséklés indításához|CountPerSecond|Maximum|Bejövő TCP-csomagok a DDoS-mérséklés indításához|Nincsenek méretek|
|DDoSTriggerUDPPackets|Yes|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|Nincsenek méretek|
|IfUnderDDoSAttack|Yes|DDoS-támadás alatt vagy nem|Darabszám|Maximum|DDoS-támadás alatt vagy nem|Nincsenek méretek|
|PacketCount|Yes|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|PacketsDroppedDDoS|Yes|Bejövő csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő csomagok eldobott DDoS|Nincsenek méretek|
|PacketsForwardedDDoS|Yes|Bejövő csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő csomagok továbbított DDoS|Nincsenek méretek|
|PacketsInDDoS|Yes|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|Nincsenek méretek|
|SynCount|Yes|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|
|TCPBytesDroppedDDoS|Yes|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|Nincsenek méretek|
|TCPBytesForwardedDDoS|Yes|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|Nincsenek méretek|
|TCPBytesInDDoS|Yes|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|Nincsenek méretek|
|TCPPacketsDroppedDDoS|Yes|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok eldobott DDoS|Nincsenek méretek|
|TCPPacketsForwardedDDoS|Yes|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok továbbított DDoS|Nincsenek méretek|
|TCPPacketsInDDoS|Yes|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|Nincsenek méretek|
|UDPBytesDroppedDDoS|Yes|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|Nincsenek méretek|
|UDPBytesForwardedDDoS|Yes|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|Nincsenek méretek|
|UDPBytesInDDoS|Yes|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|Nincsenek méretek|
|UDPPacketsDroppedDDoS|Yes|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok eldobott DDoS|Nincsenek méretek|
|UDPPacketsForwardedDDoS|Yes|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok továbbított DDoS|Nincsenek méretek|
|UDPPacketsInDDoS|Yes|Bejövő UDP-csomagok DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok DDoS|Nincsenek méretek|
|VipAvailability|Yes|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Átlagos IP-cím rendelkezésre állása időszakonként|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Végponti állapot végpont szerint|Darabszám|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|
|QpsByEndpoint|Yes|Visszaadott végponti lekérdezések|Darabszám|Összesen|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SBandwidth|Yes|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SConnectionCount|Yes|P2S-kapcsolatok száma|Darabszám|Maximum|Az átjáró P2S-kapcsolatainak száma|Protokoll|
|TunnelAverageBandwidth|Yes|Alagút sávszélessége|BytesPerSecond|Átlag|Az alagút átlagos sávszélessége bájt/másodpercben|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Yes|Alagút kimenő forgalma (bájt)|Bájt|Összesen|Az alagútról kimenő bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti kimenőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Yes|Alagút kimenő forgalma (csomag)|Darabszám|Összesen|Az alagútról kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Yes|Alagút bejövő forgalma (bájt)|Bájt|Összesen|Az alagúthoz beérkező bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Alagút forgalomválasztó-eltérés miatti bejövőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett bejövő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Yes|Bújtatási bejövő csomagok|Darabszám|Összesen|Az alagúthoz beérkező csomagok száma|Kapcsolatnév, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|A pingelések időpontjának kerekítése egy virtuális géphez|Ezredmásodpercben|Átlag|A célként megadott virtuális gépre eljuttatott pingelések menetének időpontja|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Sikertelen pingelések egy virtuális géphez|Százalék|Átlag|A hibás pingelések száma a cél virtuális gép összes elküldéses pingelésének százalékában|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|bejövő|Yes|Bejövő üzenetek|Darabszám|Összesen|A sikeres küldési API-hívások száma. |Nincsenek méretek|
|bejövő. ALL. failedrequests|Yes|Minden bejövő sikertelen kérelem|Darabszám|Összesen|Értesítési központ bejövő sikertelen kéréseinek száma|Nincsenek méretek|
|bejövő. ALL. requests|Yes|Minden bejövő kérelem|Darabszám|Összesen|Értesítési központ összes bejövő kérelme|Nincsenek méretek|
|bejövő. ütemezett|Yes|Ütemezett leküldéses értesítések elküldve|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|bejövő. ütemezett. Mégse|Yes|Ütemezett leküldéses értesítések megszakítva|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|a telepítés. All|Yes|Telepítési felügyeleti műveletek|Darabszám|Összesen|Telepítési felügyeleti műveletek|Nincsenek méretek|
|telepítés. Törlés|Yes|Telepítési műveletek törlése|Darabszám|Összesen|Telepítési műveletek törlése|Nincsenek méretek|
|telepítés. Get|Yes|Telepítési műveletek beolvasása|Darabszám|Összesen|Telepítési műveletek beolvasása|Nincsenek méretek|
|Installation. patch|Yes|Javítások telepítési műveletei|Darabszám|Összesen|Javítások telepítési műveletei|Nincsenek méretek|
|telepítési. upsert|Yes|Telepítési műveletek létrehozása vagy frissítése|Darabszám|Összesen|Telepítési műveletek létrehozása vagy frissítése|Nincsenek méretek|
|notificationhub. leküldések|Yes|Minden kimenő értesítés|Darabszám|Összesen|Az értesítési központ összes kimenő értesítése|Nincsenek méretek|
|kimenő. allpns. badorexpiredchannel|Yes|Rossz vagy lejárt csatorna-hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|Nincsenek méretek|
|kimenő. allpns. channelerror|Yes|Csatorna hibái|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|Nincsenek méretek|
|kimenő. allpns. invalidpayload|Yes|Hasznos adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|Nincsenek méretek|
|kimenő. allpns. pnserror|Yes|Külső értesítési rendszerhibák|Darabszám|Összesen|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|Nincsenek méretek|
|kimenő. allpns. sikeres|Yes|Sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. apns. badchannel|Yes|APNS hibás csatorna hiba|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS: 8).|Nincsenek méretek|
|kimenő. apns. expiredchannel|Yes|APNS lejárt csatorna hibája|Darabszám|Összesen|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|Nincsenek méretek|
|kimenő. apns. invalidcredentials|Yes|APNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. apns. invalidnotificationsize|Yes|A APNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (APNS-állapotkód: 7).|Nincsenek méretek|
|kimenő. apns. pnserror|Yes|APNS hibák|Darabszám|Összesen|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. apns. sikeres|Yes|APNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. authenticationerror|Yes|GCM-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM result: MismatchedSenderId).|Nincsenek méretek|
|kimenő. GCM. badchannel|Yes|GCM hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM-eredmény: érvénytelen regisztráció).|Nincsenek méretek|
|kimenő. GCM. expiredchannel|Yes|GCM lejárt csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM-eredmény: NotRegistered).|Nincsenek méretek|
|kimenő. GCM. invalidcredentials|Yes|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. GCM. invalidnotificationformat|Yes|GCM – érvénytelen értesítési formátum|Darabszám|Összesen|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|Nincsenek méretek|
|kimenő. GCM. invalidnotificationsize|Yes|A GCM érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|Nincsenek méretek|
|kimenő. GCM. pnserror|Yes|GCM hibák|Darabszám|Összesen|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. GCM. sikeres|Yes|GCM sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. szabályozva|Yes|GCM-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM: 501-599 vagy eredmény: nem érhető el).|Nincsenek méretek|
|kimenő. GCM. wrongchannel|Yes|GCM rossz csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM-eredmény: InvalidPackageName).|Nincsenek méretek|
|kimenő. mpns. authenticationerror|Yes|MPNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. badchannel|Yes|MPNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. mpns. channeldisconnected|Yes|MPNS csatorna leválasztva|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (MPNS-állapot: 412 nem található).|Nincsenek méretek|
|kimenő. mpns. Dropped|Yes|MPNS eldobott értesítések|Darabszám|Összesen|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X-NotificationStatus: QueueFull vagy letiltva).|Nincsenek méretek|
|kimenő. mpns. invalidcredentials|Yes|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. invalidnotificationformat|Yes|MPNS – érvénytelen értesítési formátum|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|Nincsenek méretek|
|kimenő. mpns. pnserror|Yes|MPNS hibák|Darabszám|Összesen|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. mpns. sikeres|Yes|MPNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. mpns. szabályozva|Yes|MPNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem fogadható el).|Nincsenek méretek|
|kimenő. wns. authenticationerror|Yes|WNS-hitelesítési hibák|Darabszám|Összesen|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|Nincsenek méretek|
|kimenő. wns. badchannel|Yes|WNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. wns. channeldisconnected|Yes|WNS csatorna leválasztva|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. channelthrottled|Yes|WNS csatorna szabályozása|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-NotificationStatus: channelThrottled).|Nincsenek méretek|
|kimenő. wns. Dropped|Yes|WNS eldobott értesítések|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. expiredchannel|Yes|WNS lejárt csatorna hibája|Darabszám|Összesen|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapota: 410 elveszett).|Nincsenek méretek|
|kimenő. wns. invalidcredentials|Yes|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|Nincsenek méretek|
|kimenő. wns. invalidnotificationformat|Yes|WNS – érvénytelen értesítési formátum|Darabszám|Összesen|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|Nincsenek méretek|
|kimenő. wns. invalidnotificationsize|Yes|A WNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az értesítési tartalom túl nagy (WNS állapot: 413).|Nincsenek méretek|
|kimenő. wns. invalidtoken|Yes|WNS-hitelesítési hibák (érvénytelen token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvénytelen (WNS-állapot: 401 jogosulatlan).|Nincsenek méretek|
|kimenő. wns. pnserror|Yes|WNS hibák|Darabszám|Összesen|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|Nincsenek méretek|
|kimenő. wns. sikeres|Yes|WNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. wns. szabályozva|Yes|WNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapota: 406, nem fogadható el).|Nincsenek méretek|
|kimenő. wns. tokenproviderunreachable|Yes|WNS-hitelesítési hibák (nem érhető el)|Darabszám|Összesen|A Windows Live nem érhető el.|Nincsenek méretek|
|kimenő. wns. wrongtoken|Yes|WNS-hitelesítési hibák (hibás token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|Nincsenek méretek|
|regisztráció. All|Yes|Regisztrációs műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |Nincsenek méretek|
|regisztráció. Create|Yes|Regisztráció-létrehozási műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs létrehozás száma.|Nincsenek méretek|
|regisztráció. Delete|Yes|Regisztrációs törlési műveletek|Darabszám|Összesen|A regisztráció sikeres törléseinak száma.|Nincsenek méretek|
|regisztráció. Get|Yes|Regisztrálási olvasási műveletek|Darabszám|Összesen|A sikeres regisztrációs lekérdezések száma.|Nincsenek méretek|
|regisztráció. frissítés|Yes|Regisztrációs frissítési műveletek|Darabszám|Összesen|A sikeres regisztrációs frissítések száma.|Nincsenek méretek|
|ütemezett. függőben|Yes|Függőben lévő ütemezett értesítések|Darabszám|Összesen|Függőben lévő ütemezett értesítések|Nincsenek méretek|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre álló memória Average_%-ban|Yes|Rendelkezésre álló memória%-ban|Darabszám|Átlag|Rendelkezésre álló memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ rendelkezésre álló szabad hely a lapozófájlban|Yes|Rendelkezésre álló swap-terület (%)|Darabszám|Átlag|Average_ rendelkezésre álló szabad hely a lapozófájlban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Yes|Előjegyzett memória%-ban használatban|Darabszám|Átlag|Average_%-os előjegyzett bájtok használatban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% DPC idő|Yes|% DPC idő|Darabszám|Átlag|Average_% DPC idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad inode (%)|Yes|Szabad inode%-ban|Darabszám|Átlag|Average_ szabad inode (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Yes|Szabad terület (%)|Darabszám|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ üresjárati idő%-ban|Yes|Üresjáratban eltöltött időhányad (%)|Darabszám|Átlag|Average_ üresjárati idő%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Yes|Megszakítási idő%-ban|Darabszám|Átlag|Average_%-os megszakítási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% IO várakozási idő|Yes|I/o várakozási idő%-ban|Darabszám|Átlag|Average_% IO várakozási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os szép idő|Yes|% Nice idő|Darabszám|Átlag|Average_%-os szép idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|Yes|%-Os privilegizált idő|Darabszám|Átlag|Average_%-os privilegizált idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|Yes|A processzor kihasználtsága (%)|Darabszám|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ használt inode|Yes|Felhasznált inode%-ban|Darabszám|Átlag|Average_ használt inode|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Felhasznált memória Average_%-ban|Yes|Felhasznált memória (%)|Darabszám|Átlag|Felhasznált memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált terület%-ban|Yes|Felhasznált terület (%)|Darabszám|Átlag|Average_ felhasznált terület%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált lapozófájl-terület|Yes|Felhasznált swap-terület%-ban|Darabszám|Átlag|Average_ felhasznált lapozófájl-terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Yes|Felhasználói idő%-ban|Darabszám|Átlag|Average_%-os felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban|Yes|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB memória|Yes|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban való swap|Yes|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB-ban való swap|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Yes|Átlagos írási idő (mp/olvasás)|Darabszám|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Lemez (mp/átvitel)|Yes|Átlagos műveleti idő (mp/átvitel)|Darabszám|Átlag|Average_Avg. Lemez (mp/átvitel)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Yes|Átlagos írási idő (mp/írás)|Darabszám|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Yes|Fogadott bájtok/s|Darabszám|Átlag|Fogadott Average_Bytes/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Küldési Average_Bytes másodpercenként|Yes|Küldési sebesség (bájt/s)|Darabszám|Átlag|Küldési Average_Bytes másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Yes|Összes bájt/mp|Darabszám|Átlag|Average_Bytes összesen/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Current a lemez várólistájának hossza|Yes|Lemez aktuális várólistájának hossza|Darabszám|Átlag|Average_Current a lemez várólistájának hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási sebesség (bájt/s) Average_Disk|Yes|Lemez olvasási sebessége (bájt/s)|Darabszám|Átlag|Olvasási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Yes|Olvasási sebesség (lemez/mp)|Darabszám|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Yes|Lemez átvitele másodpercenként|Darabszám|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írási sebesség (bájt/s) Average_Disk|Yes|Lemez írási sebessége (bájt/s)|Darabszám|Átlag|Írási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Yes|Írási sebesség (írás/mp)|Darabszám|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Yes|Szabad terület (MB)|Darabszám|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fizikai memória Average_Free|Yes|Szabad fizikai memória|Darabszám|Átlag|Fizikai memória Average_Free|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free lemezterület a Lapozófájlokban|Yes|Szabad terület a Lapozófájlokban|Darabszám|Átlag|Average_Free lemezterület a Lapozófájlokban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free virtuális memória|Yes|Szabad virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Yes|Logikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Logical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Page másodpercenként|Yes|Olvasott lap/mp|Darabszám|Átlag|Olvasási Average_Page másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Page|Yes|Írási idő/mp|Darabszám|Átlag|Írás/mp Average_Page|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pages előállított/másodperc|Yes|Lap/mp|Darabszám|Átlag|Average_Pages előállított/másodperc|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|Yes|PCT rendszerjogosultságú idő|Darabszám|Átlag|Average_Pct emelt szintű idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|Yes|PCT felhasználói idő|Darabszám|Átlag|Average_Pct felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Yes|Fizikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Physical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processes|Yes|Folyamatok|Darabszám|Átlag|Average_Processes|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Yes|Processzor-várólista hossza|Darabszám|Átlag|Average_Processor várólista hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Yes|Lapozófájlokban tárolt méret|Darabszám|Átlag|Lapozófájlokban tárolt Average_Size|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total bájtok|Yes|Bájtok összesen|Darabszám|Átlag|Average_Total bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Total bájtok száma|Yes|Fogadott bájtok összesen|Darabszám|Átlag|Fogadott Average_Total bájtok száma|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított bájtok|Yes|Továbbított bájtok összesen|Darabszám|Átlag|Average_Total továbbított bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Ütközések Average_Total|Yes|Ütközések összesen|Darabszám|Átlag|Ütközések Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total fogadott csomagok|Yes|Fogadott csomagok összesen|Darabszám|Átlag|Average_Total fogadott csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított csomagok|Yes|Továbbított csomagok összesen|Darabszám|Átlag|Average_Total továbbított csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rx-hibák Average_Total|Yes|Rx-hibák összesen|Darabszám|Átlag|Rx-hibák Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total TX-hibák|Yes|TX-hibák összesen|Darabszám|Átlag|Average_Total TX-hibák|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Uptime|Yes|Üzemidő|Darabszám|Átlag|Average_Uptime|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used MB-ban felcserélt terület|Yes|Felhasznált memória (MB) – lapozófájl|Darabszám|Átlag|Average_Used MB-ban felcserélt terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Yes|Felhasznált memória (kilobájt)|Darabszám|Átlag|Average_Used memória kilobájtban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória MB-ban|Yes|Felhasznált memória (MB)|Darabszám|Átlag|Average_Used memória MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Users|Yes|Felhasználók|Darabszám|Átlag|Average_Users|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Yes|Virtuális megosztott memória|Darabszám|Átlag|Average_Virtual megosztott memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Esemény|Yes|Esemény|Darabszám|Átlag|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|
|Szívverés|Yes|Szívverés|Darabszám|Összesen|Szívverés|Számítógép, OSType, verzió, SourceComputerId|
|Frissítés|Yes|Frissítés|Darabszám|Átlag|Frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/társaik

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Kimenő forgalom aránya|BitsPerSecond|Átlag|Kimenő forgalom sebessége (bit/mp)|ConnectionId|
|IngressTrafficRate|Yes|Bejövő forgalom aránya|BitsPerSecond|Átlag|Bejövő forgalom sebessége bit/másodpercben|ConnectionId|
|SessionAvailabilityV4|Yes|Munkamenet rendelkezésre állása v4|Százalék|Átlag|A v4-munkamenet rendelkezésre állása|ConnectionId|
|SessionAvailabilityV6|Yes|Munkamenet rendelkezésre állása v6|Százalék|Átlag|A V6-munkamenet rendelkezésre állása|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Előtag-késés|Ezredmásodpercben|Átlag|Medián előtag késése|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Memória|Bájt|Átlag|Memory. 0-3 GB-os tartomány a1, 0-5 GB az a2-hez, 0-10 GB az a3-hoz, 0-25 GB a4-hez, 0-50 GB az A5-hez és az A6-os 0-100 GB-hoz|Nincsenek méretek|
|memory_thrashing_metric|Yes|Memória-Kiverés (adathalmazok)|Százalék|Átlag|Memória átlagos kiverése.|Nincsenek méretek|
|qpu_high_utilization_metric|Yes|QPU magas kihasználtság|Darabszám|Összesen|QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0|Nincsenek méretek|
|QueryDuration|Yes|Lekérdezés időtartama (adatkészletek)|Ezredmásodpercben|Átlag|DAX-lekérdezés időtartama az utolsó intervallumban|Nincsenek méretek|
|QueryPoolJobQueueLength|Yes|Lekérdezési készlet nyomtatási várólistájának hossza (adatkészletek)|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|Nincsenek méretek|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft. Relay aktív kapcsolatai összesen.|EntityName|
|ActiveListeners|No|ActiveListeners|Darabszám|Összesen|A Microsoft. Relay ActiveListeners összesen.|EntityName|
|BytesTransferred|Yes|BytesTransferred|Darabszám|Összesen|A Microsoft. Relay BytesTransferred összesen.|EntityName|
|Figyelőkapcsolatokra – vonatkozó ügyfélhibái|No|Figyelőkapcsolatokra – vonatkozó ügyfélhibái|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName |
|Figyelőkapcsolatokra – ServerError|No|Figyelőkapcsolatokra – ServerError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|EntityName |
|Figyelőkapcsolatokra – sikeres|No|Figyelőkapcsolatokra – sikeres|Darabszám|Összesen|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|EntityName |
|Figyelőkapcsolatokra – TotalRequests|No|Figyelőkapcsolatokra – TotalRequests|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra összesen.|EntityName|
|Bontásai|No|Bontásai|Darabszám|Összesen|A Microsoft. Relay bontásai összesen.|EntityName|
|Feladói kapcsolatokra – vonatkozó ügyfélhibái|No|Feladói kapcsolatokra – vonatkozó ügyfélhibái|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName |
|Feladói kapcsolatokra – ServerError|No|Feladói kapcsolatokra – ServerError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra ServerError.|EntityName |
|Feladói kapcsolatokra – sikeres|No|Feladói kapcsolatokra – sikeres|Darabszám|Összesen|A Microsoft. Relay sikeres feladói kapcsolatokra.|EntityName |
|Feladói kapcsolatokra – TotalRequests|No|Feladói kapcsolatokra – TotalRequests|Darabszám|Összesen|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|EntityName|
|SenderDisconnects|No|SenderDisconnects|Darabszám|Összesen|A Microsoft. Relay SenderDisconnects összesen.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Keresési késés|Másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|Nincsenek méretek|
|SearchQueriesPerSecond|Yes|Keresési lekérdezések másodpercenként|CountPerSecond|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|Nincsenek méretek|
|ThrottledSearchQueriesPercentage|Yes|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|Nincsenek méretek|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|No|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft. ServiceBus összes aktív kapcsolata.|Nincsenek méretek|
|ActiveMessages|No|Üzenetsor vagy témakör aktív üzeneteinek száma.|Darabszám|Átlag|Üzenetsor vagy témakör aktív üzeneteinek száma.|EntityName|
|ConnectionsClosed|No|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus lezárt kapcsolatainak száma.|EntityName|
|ConnectionsOpened|No|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus megnyitott kapcsolatainak száma.|EntityName|
|CPUXNS|No|CPU (elavult)|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája. Ez a metrika depricated. Használja helyette a CPU-metrikát (NamespaceCpuUsage).|Nincsenek méretek|
|DeadletteredMessages|No|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|Darabszám|Átlag|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|EntityName|
|IncomingMessages|Yes|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő üzenetei.|EntityName|
|IncomingRequests|Yes|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő kérései.|EntityName|
|Üzenetek|No|Üzenetsor/téma üzeneteinek száma.|Darabszám|Átlag|Üzenetsor/téma üzeneteinek száma.|EntityName|
|NamespaceCpuUsage|No|CPU|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája.|Nincsenek méretek|
|NamespaceMemoryUsage|No|Memóriahasználat|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma.|Nincsenek méretek|
|OutgoingMessages|Yes|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus kimenő üzenetei.|EntityName|
|ScheduledMessages|No|Az üzenetsor/témakör ütemezett üzeneteinek száma.|Darabszám|Átlag|Az üzenetsor/témakör ütemezett üzeneteinek száma.|EntityName|
|Kiszolgálóhibái|No|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. ServiceBus esetében.|EntityName |
|Méret|No|Méret|Bájt|Átlag|Várólista/témakör mérete bájtban.|EntityName|
|SuccessfulRequests|No|Sikeres kérelmek|Darabszám|Összesen|Névtér összes sikeres kérelme|EntityName |
|ThrottledRequests|No|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. ServiceBus által szabályozott kérelmek.|EntityName |
|UserErrors|No|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. ServiceBus felhasználói hibái.|EntityName |
|WSXNS|No|Memóriahasználat (elavult)|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma. Ez a metrika elavult. Használja helyette a memóriahasználat (NamespaceMemoryUsage) metrikáját.|Nincsenek méretek|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/alkalmazások

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Darabszám|Átlag|Tényleges CPU-használat a Millon magokban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedCpu|No|AllocatedCpu|Darabszám|Átlag|A tárolóhoz a Millet magokban lefoglalt CPU|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedMemory|No|AllocatedMemory|Bájt|Átlag|A tároló számára lefoglalt memória (MB)|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|Tároló állapota:|No|Tároló állapota:|Darabszám|Átlag|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName, állapot|
|CpuUtilization|No|CpuUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|RestartCount|No|RestartCount|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, status, szolgáltatásnév, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, status, szolgáltatásnév, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, állapot, szolgáltatásnév|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|InboundTraffic|Yes|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|Nincsenek méretek|
|MessageCount|Yes|Üzenetek száma|Darabszám|Összesen|Az üzenetek teljes mennyisége.|Nincsenek méretek|
|OutboundTraffic|Yes|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|Nincsenek méretek|
|SystemErrors|Yes|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|Nincsenek méretek|
|UserErrors|Yes|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|Nincsenek méretek|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|Nincsenek méretek|
|io_bytes_read|Yes|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|Nincsenek méretek|
|io_bytes_written|Yes|I/o-bájtok írása|Bájt|Átlag|I/o-bájtok írása|Nincsenek méretek|
|io_requests|Yes|IO-kérelmek száma|Darabszám|Átlag|IO-kérelmek száma|Nincsenek méretek|
|reserved_storage_mb|Yes|Tárterület fenntartva|Darabszám|Átlag|Tárterület fenntartva|Nincsenek méretek|
|storage_space_used_mb|Yes|Felhasznált tárterület|Darabszám|Átlag|Felhasznált tárterület|Nincsenek méretek|
|virtual_core_count|Yes|Virtuális mag száma|Darabszám|Átlag|Virtuális mag száma|Nincsenek méretek|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|active_queries|Yes|Aktív lekérdezések|Darabszám|Összesen|Aktív lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|allocated_data_storage|Yes|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adattárolás. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|app_cpu_billed|Yes|Az alkalmazás CPU-számlázása|Darabszám|Összesen|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_cpu_percent|Yes|Alkalmazás CPU-aránya|Százalék|Átlag|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_memory_percent|Yes|Az alkalmazás memóriájának százaléka|Százalék|Átlag|Az alkalmazás memóriájának százalékos aránya. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|base_blob_size_bytes|Yes|Az alap blob tárterületének mérete|Bájt|Maximum|Az alap blob tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|Nincsenek méretek|
|blocked_by_firewall|Yes|Tűzfal blokkolja|Darabszám|Összesen|Tűzfal blokkolja|Nincsenek méretek|
|cache_hit_percent|Yes|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|cache_used_percent|Yes|Gyorsítótár használt százaléka|Százalék|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|connection_failed|Yes|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|connection_successful|Yes|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_limit|Yes|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|cpu_percent|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Yes|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|holtpont|Yes|Holtpontok|Darabszám|Összesen|Holtpontok. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|diff_backup_size_bytes|Yes|Különbözeti biztonsági másolatok tárolási mérete|Bájt|Maximum|Kumulatív különbözeti biztonsági másolati tárterület mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|dtu_consumption_percent|Yes|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_limit|Yes|DTU korlátja|Darabszám|Átlag|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_used|Yes|Használt DTU|Darabszám|Átlag|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dw_backup_size_gb|Yes|Adattárolás mérete (GB)|Darabszám|Összesen|Az adattárolási méret az adatai és a tranzakciónapló méretétől áll. A metrika a számla "Storage" részébe kerül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dw_geosnapshot_size_gb|Yes|Vész-helyreállítási tárterület mérete (GB)|Darabszám|Összesen|A vész-helyreállítási tároló mérete "vész-helyreállítási tárolóként" jelenik meg a számlán. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dw_snapshot_size_gb|Yes|Pillanatkép-tároló mérete (GB)|Darabszám|Összesen|A pillanatképek tárolási mérete a pillanatképek által a felhasználó által meghatározott és az Automatikus visszaállítási pontok létrehozásához rögzített növekményes módosítások mérete. A metrika a számla "Storage" részébe kerül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_consumption_percent|Yes|DWU százalékos aránya|Százalék|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_limit|Yes|DWU korlátja|Darabszám|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_used|Yes|Használt DWU|Darabszám|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|full_backup_size_bytes|Yes|Teljes biztonsági mentési tár mérete|Bájt|Maximum|Összesített teljes biztonsági mentési tár mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|local_tempdb_usage_percent|Yes|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|log_backup_size_bytes|Yes|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő napló biztonsági mentési tárterületének mérete. A virtuális mag-alapú és a nagy kapacitású adatbázisokra vonatkozik.|Nincsenek méretek|
|log_write_percent|Yes|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|memory_usage_percent|Yes|Memória százaléka|Százalék|Maximum|Memória százalékos aránya Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|physical_data_read_percent|Yes|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|queued_queries|Yes|Várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|sessions_percent|Yes|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|snapshot_backup_size_bytes|Yes|Pillanatkép biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő pillanatkép biztonsági mentési tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|Nincsenek méretek|
|sqlserver_process_core_percent|Yes|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|sqlserver_process_memory_percent|Yes|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage|Yes|Felhasznált adatterület|Bájt|Maximum|Felhasznált adatterület. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage_percent|Yes|Felhasznált adatterület százalékos aránya|Százalék|Maximum|Az adatterület felhasznált százaléka százalékban. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|tempdb_data_size|Yes|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|Tempdb az adatfájl mérete kilobájtban. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|tempdb_log_size|Yes|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|Tempdb-naplófájl mérete (kilobájt) Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|tempdb_log_used_percent|Yes|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|wlg_active_queries|Yes|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|Aktív lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Azok a lekérdezések, amelyek túllépték a munkaterhelés-csoport időtúllépését. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások a teljes rendszerterhelési csoporthoz viszonyított százalékos aránya. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Munkaterhelés-csoport lefoglalása a Cap-erőforrás százaléka alapján|Százalék|Maximum|Az erőforrások megadott százalékos aránya a munkaterhelési csoportok meghatározott erőforrásaihoz viszonyítva. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport számára engedélyezett erőforrások százalékos arányának korlátozása, figyelembe véve a más munkaterhelés-csoportok számára kiosztott tényleges minimális erőforrás-százalékos arányt. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Effektív minimális erőforrás százaléka|Százalék|Maximum|A munkaterhelés-csoport számára fenntartott és elkülönített erőforrások minimális százalékos aránya, a szolgáltatási szint minimumának figyelembevételével. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|xtp_storage_percent|Yes|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP-tárolási százalék. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/kiszolgálók/elasticPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|Nincsenek méretek|
|allocated_data_storage_percent|Yes|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|Nincsenek méretek|
|cpu_limit|Yes|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|cpu_percent|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Yes|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|database_allocated_data_storage|No|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|DatabaseResourceId|
|database_cpu_limit|No|CPU-korlát|Darabszám|Átlag|CPU-korlát|DatabaseResourceId|
|database_cpu_percent|No|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|DatabaseResourceId|
|database_cpu_used|No|Felhasznált CPU|Darabszám|Átlag|Felhasznált CPU|DatabaseResourceId|
|database_dtu_consumption_percent|No|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId|
|database_eDTU_used|No|használt eDTU|Darabszám|Átlag|használt eDTU|DatabaseResourceId|
|database_log_write_percent|No|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|DatabaseResourceId|
|database_physical_data_read_percent|No|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|DatabaseResourceId|
|database_sessions_percent|No|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|DatabaseResourceId|
|database_storage_used|No|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId|
|database_workers_percent|No|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|DatabaseResourceId|
|dtu_consumption_percent|Yes|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_limit|Yes|eDTU korlátja|Darabszám|Átlag|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_used|Yes|használt eDTU|Darabszám|Átlag|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|log_write_percent|Yes|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|Nincsenek méretek|
|physical_data_read_percent|Yes|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|sessions_percent|Yes|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincsenek méretek|
|sqlserver_process_core_percent|Yes|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|Nincsenek méretek|
|sqlserver_process_memory_percent|Yes|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|Nincsenek méretek|
|storage_limit|Yes|Az adatmaximális méret|Bájt|Átlag|Az adatmaximális méret|Nincsenek méretek|
|storage_percent|Yes|Felhasznált adatterület százalékos aránya|Százalék|Átlag|Felhasznált adatterület százalékos aránya|Nincsenek méretek|
|storage_used|Yes|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|Nincsenek méretek|
|tempdb_data_size|Yes|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|Tempdb adatfájl mérete (kilobájt)|Nincsenek méretek|
|tempdb_log_size|Yes|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|Tempdb-naplófájl mérete (kilobájt)|Nincsenek méretek|
|tempdb_log_used_percent|Yes|Tempdb százalékos naplója használatban|Százalék|Maximum|Tempdb százalékos naplója használatban|Nincsenek méretek|
|workers_percent|Yes|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|Nincsenek méretek|
|xtp_storage_percent|Yes|Memóriában tárolt OLTP tárolási százaléka|Százalék|Átlag|Memóriában tárolt OLTP tárolási százaléka|Nincsenek méretek|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|UsedCapacity|No|Felhasznált kapacitás|Bájt|Átlag|A Storage-fiók által használt tárterület mennyisége. Standard szintű tárfiókok esetében ez a blob, a tábla, a fájl és a várólista által használt kapacitás összege. A Premium Storage-fiókok és a blob Storage-fiókok esetében ugyanaz, mint a BlobCapacity vagy a FileCapacity.|Nincsenek méretek|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|BlobCapacity|No|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|No|Blobok száma|Darabszám|Átlag|A Storage-fiókban tárolt blob-objektumok száma.|BlobType, szintű|
|BlobProvisionedSize|No|BLOB kiépített mérete|Bájt|Átlag|A Storage-fiók Blob serviceban kiépített tárterület mérete bájtban megadva.|BlobType, szintű|
|ContainerCount|Yes|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiókban lévő tárolók száma.|Nincsenek méretek|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|IndexCapacity|No|Index kapacitása|Bájt|Átlag|A Azure Data Lake Storage Gen2 hierarchikus index által használt tárterület mennyisége.|Nincsenek méretek|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|FileCapacity|No|Fájl kapacitása|Bájt|Átlag|A Storage-fiók által használt file Storage mennyisége.|Fájlmegosztás|
|FileCount|No|Fájlok száma|Darabszám|Átlag|A Storage-fiókban található fájlok száma.|Fájlmegosztás|
|FileShareCapacityQuota|No|Fájlmegosztás kapacitásának kvótája|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|FileShareCount|No|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiókban lévő fájlmegosztás száma.|Nincsenek méretek|
|FileShareProvisionedIOPS|No|Fájlmegosztási kiépített IOPS|Bájt|Átlag|A Premium file Storage-fiókban a prémium fájlmegosztás kiépített IOPS alapszáma. Ezt a számot a megosztási kapacitás kiosztott mérete (kvóta) alapján számítjuk ki.|Fájlmegosztás|
|FileShareSnapshotCount|No|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|No|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|QueueCapacity|Yes|Várólista kapacitása|Bájt|Átlag|A Storage-fiók által használt üzenetsor-tároló mennyisége.|Nincsenek méretek|
|QueueCount|Yes|Várólista száma|Darabszám|Átlag|A Storage-fiókban lévő várólisták száma.|Nincsenek méretek|
|QueueMessageCount|Yes|Üzenetsor-üzenetek száma|Darabszám|Átlag|A nem lejárt üzenetsor-üzenetek száma a Storage-fiókban.|Nincsenek méretek|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Yes|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Yes|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Yes|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Yes|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Yes|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|TableCapacity|Yes|Tábla kapacitása|Bájt|Átlag|A Storage-fiók által használt Table Storage mennyisége.|Nincsenek méretek|
|TableCount|Yes|Táblák száma|Darabszám|Átlag|A Storage-fiókban lévő táblák száma.|Nincsenek méretek|
|TableEntityCount|Yes|Tábla entitások száma|Darabszám|Átlag|A Storage-fiókban lévő tábla entitások száma.|Nincsenek méretek|
|Tranzakciók|Yes|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Szinkronizálási munkamenet eredménye|Darabszám|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Felhőbeli rétegek felidézésének mérete alkalmazás szerint|Bájt|Összesen|Az alkalmazás által visszahívott adatmennyiség|SyncGroupName, kiszolgálónév, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Felhőbeli rétegek felidézésének mérete|Bájt|Összesen|Visszahívott adatmennyiség|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Yes|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Felhőbeli rétegek felidézésének átviteli sebessége|BytesPerSecond|Átlag|Adatvisszahívási sebesség mérete|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Yes|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Yes|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Yes|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Sikertelen függvények kérései|Darabszám|Összesen|Sikertelen függvények kérései|LogicalName, PartitionId|
|AMLCalloutInputEvents|Yes|Függvények eseményei|Darabszám|Összesen|Függvények eseményei|LogicalName, PartitionId|
|AMLCalloutRequests|Yes|Függvények kérései|Darabszám|Összesen|Függvények kérései|LogicalName, PartitionId|
|ConversionErrors|Yes|Adatátalakítási hibák|Darabszám|Összesen|Adatátalakítási hibák|LogicalName, PartitionId|
|DeserializationError|Yes|Bemeneti deszerializálási hibák|Darabszám|Összesen|Bemeneti deszerializálási hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Yes|Megrendelési események|Darabszám|Összesen|Megrendelési események|LogicalName, PartitionId|
|EarlyInputEvents|Yes|Korai bemeneti események|Darabszám|Összesen|Korai bemeneti események|LogicalName, PartitionId|
|Hibák|Yes|Futásidejű hibák|Darabszám|Összesen|Futásidejű hibák|LogicalName, PartitionId|
|InputEventBytes|Yes|Bemeneti esemény bájtjai|Bájt|Összesen|Bemeneti esemény bájtjai|LogicalName, PartitionId|
|InputEvents|Yes|Bemeneti események|Darabszám|Összesen|Bemeneti események|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Yes|Várakozó bemeneti eseményei|Darabszám|Maximum|Várakozó bemeneti eseményei|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Yes|Fogadott bemeneti források|Darabszám|Összesen|Fogadott bemeneti források|LogicalName, PartitionId|
|LateInputEvents|Yes|Késői bemeneti események|Darabszám|Összesen|Késői bemeneti események|LogicalName, PartitionId|
|OutputEvents|Yes|Kimeneti események|Darabszám|Összesen|Kimeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Vízjel késleltetése|Másodperc|Maximum|Vízjel késleltetése|LogicalName, PartitionId|
|ResourceUtilization|Yes|SU% kihasználtsága|Százalék|Maximum|SU% kihasználtsága|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. szinapszis/munkaterületek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|No|A tevékenység futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési tevékenységek száma|Eredmény, FailureType, tevékenység, ActivityType, folyamat|
|OrchestrationPipelineRunsEnded|No|A folyamat futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési folyamat-futtatások száma|Eredmény, FailureType, folyamat|
|OrchestrationTriggersEnded|No|Befejezett eseményindítók|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési eseményindítók száma|Eredmény, FailureType, trigger|
|SQLOnDemandLoginAttempts|No|Bejelentkezési kísérletek|Darabszám|Összesen|A sikerül vagy sikertelen bejelentkezési kísérletek száma|Eredmény|
|SQLOnDemandQueriesEnded|No|A lekérdezések véget ért|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított lekérdezések száma|Eredmény|
|SQLOnDemandQueryProcessedBytes|No|Feldolgozott adatfeldolgozás|Bájt|Összesen|Lekérdezések által feldolgozott adatmennyiség|Nincsenek méretek|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. szinapszis/munkaterületek/bigDataPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CoresCapacity|No|Magok kapacitása|Darabszám|Maximum|Magok kapacitása|Nincsenek méretek|
|MemoryCapacityGB|No|Memória kapacitása (GB)|Darabszám|Maximum|Memória kapacitása (GB)|Nincsenek méretek|
|SparkJobsEnded|Yes|Befejezett alkalmazások|Darabszám|Összesen|Befejezett alkalmazások száma|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. szinapszis/munkaterületek/sqlPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|No|Adaptív gyorsítótár-találatok százalékos aránya|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár találati százalékos metrikájának használatával határozhatja meg, hogy szeretné-e bővíteni a további kapacitást, vagy futtassa újra a számítási feladatokat a gyorsítótár hidrát|Nincsenek méretek|
|AdaptiveCacheUsedPercent|No|Adaptív gyorsítótár használt százaléka|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár használt százalékos metrikájának használatával határozhatja meg, hogy a további kapacitást szeretné-e méretezni, vagy újra kell futtatnia a számítási feladatokat a gyorsítótár hidrát|Nincsenek méretek|
|Kapcsolatok|Yes|Kapcsolatok|Darabszám|Összesen|Az SQL-készletbe való teljes bejelentkezések száma|Eredmény|
|ConnectionsBlockedByFirewall|No|Tűzfal által letiltott kapcsolatok|Darabszám|Összesen|A tűzfalszabályok által letiltott kapcsolatok száma. Nyissa meg újra az SQL-készlethez tartozó hozzáférés-vezérlési szabályzatokat, és figyelje meg, hogy a kapcsolatok száma magas-e|Nincsenek méretek|
|DWULimit|No|DWU korlátja|Darabszám|Maximum|Az SQL-készlet szolgáltatási szintjének célkitűzése|Nincsenek méretek|
|DWUUsed|No|Használt DWU|Darabszám|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. DWU-korláttal mérve * DWU százalék|Nincsenek méretek|
|DWUUsedPercent|No|DWU használt százalék|Százalék|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. A CPU-százalék és az adatio-százalék közötti maximális érték alapján mérhető|Nincsenek méretek|
|LocalTempDBUsedPercent|No|Helyi tempdb használt százalék|Százalék|Maximum|Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva|Nincsenek méretek|
|MemoryUsedPercent|No|Felhasznált memória százalékos aránya|Százalék|Maximum|Memória kihasználtsága az SQL-készlet összes csomópontján|Nincsenek méretek|
|wlg_effective_min_resource_percent|Yes|Effektív minimális erőforrás százaléka|Százalék|Minimális|A tényleges minimális erőforrás-százalékos beállítás a szolgáltatási szint és a munkaterhelés-csoport beállításainak figyelembevételével engedélyezett. A tényleges min_percentage_resource magasabb szinten állítható be alacsonyabb szolgáltatási szinteken|IsUserDefined, WorkloadGroup|
|WLGActiveQueries|No|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|A munkaterhelés csoporton belüli aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Túllépte az időkorlátot tartalmazó munkaterhelési csoport lekérdezéseit. A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának megkezdése után (nem tartalmazza a várakozási időt a zárolás vagy az erőforrás-várakozás miatt)|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|No|Munkaterhelési csoport lefoglalása az erőforrás maximális százaléka alapján|Százalék|Maximum|Megjeleníti az erőforrások százalékos kiosztását a munkaterhelési csoport tényleges maximális erőforrásához viszonyítva. Ez a mérőszám a munkaterhelés-csoport hatékony kihasználtságát biztosítja|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások százalékos kiosztása a teljes rendszerrel viszonyítva|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Yes|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport tényleges Cap-erőforrásának százaléka. Ha más munkaterhelés-csoportok min_percentage_resource > 0, a effective_cap_percentage_resource arányosan csökken|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az összes eseményforrás által olvasott bájtok száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|Yes|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedMessages|Yes|Bejövő fogadott üzenetek|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Yes|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Yes|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressStoredBytes|Yes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Yes|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|WarmStorageMaxProperties|Yes|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Yes|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az eseményforrás által beolvasott bájtok száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|Yes|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedMessages|Yes|Bejövő fogadott üzenetek|Darabszám|Összesen|Az eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Yes|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Yes|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressStoredBytes|Yes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Yes|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|WarmStorageMaxProperties|Yes|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Yes|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtjai|Yes|Lemez olvasási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Yes|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|Lemez írási bájtjai|Yes|Lemez írási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|Nincsenek méretek|
|Lemez írási műveletei/mp|Yes|Lemez írási műveletei/mp|CountPerSecond|Átlag|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskReadBytesPerSecond|Yes|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|DiskReadLatency|Yes|Lemez olvasási késése|Ezredmásodpercben|Átlag|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|Nincsenek méretek|
|DiskReadOperations|Yes|Lemezes olvasási műveletek|Darabszám|Összesen|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskWriteBytesPerSecond|Yes|Lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|Nincsenek méretek|
|DiskWriteLatency|Yes|Lemez írási késése|Ezredmásodpercben|Átlag|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|Nincsenek méretek|
|DiskWriteOperations|Yes|Lemez írási műveletei|Darabszám|Összesen|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|MemoryActive|Yes|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|Nincsenek méretek|
|MemoryGranted|Yes|Megadott memória|Bájt|Átlag|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|Nincsenek méretek|
|MemoryUsed|Yes|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt gépi memória mennyisége.|Nincsenek méretek|
|Bejövő hálózat|Yes|Bejövő hálózat|Bájt|Összesen|A fogadott forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|Kimenő hálózat|Yes|Kimenő hálózat|Bájt|Összesen|A továbbított forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkInBytesPerSecond|Yes|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkOutBytesPerSecond|Yes|Hálózati kimenő bájtok/s|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|Százalékos processzorhasználat|Yes|Százalékos processzorhasználat|Százalék|Átlag|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|Nincsenek méretek|
|PercentageCpuReady|Yes|CPU-készültség százalékos aránya|Ezredmásodpercben|Összesen|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|Nincsenek méretek|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Aktív kérések|Darabszám|Összesen|Aktív kérések|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő|Másodperc|Átlag|Átlagos válaszidő|Példány|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CpuPercentage|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|DiskQueueLength|Yes|Lemezvezérlő-várólista hossza|Darabszám|Átlag|Lemezvezérlő-várólista hossza|Példány|
|Http101|Yes|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Yes|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Yes|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Yes|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Yes|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Yes|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Yes|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Yes|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|HttpQueueLength|Yes|Http-várólista hossza|Darabszám|Átlag|Http-várólista hossza|Példány|
|LargeAppServicePlanInstances|Yes|Nagy App Service-csomag feldolgozói|Darabszám|Átlag|Nagy App Service-csomag feldolgozói|Nincsenek méretek|
|MediumAppServicePlanInstances|Yes|Közepes App Service-csomag feldolgozói|Darabszám|Átlag|Közepes App Service-csomag feldolgozói|Nincsenek méretek|
|MemoryPercentage|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|SmallAppServicePlanInstances|Yes|Kisméretű App Service-csomag feldolgozói|Darabszám|Átlag|Kisméretű App Service-csomag feldolgozói|Nincsenek méretek|
|TotalFrontEnds|Yes|Összes előtér|Darabszám|Átlag|Összes előtér|Nincsenek méretek|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|WorkersAvailable|Yes|Rendelkezésre álló munkavégzők|Darabszám|Átlag|Rendelkezésre álló munkavégzők|Nincsenek méretek|
|WorkersTotal|Yes|Alkalmazottak összesen|Darabszám|Átlag|Alkalmazottak összesen|Nincsenek méretek|
|WorkersUsed|Yes|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|Nincsenek méretek|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/kiszolgálófarmok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CpuPercentage|Yes|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|DiskQueueLength|Yes|Lemezvezérlő-várólista hossza|Darabszám|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Yes|Http-várólista hossza|Darabszám|Átlag|Http-várólista hossza|Példány|
|MemoryPercentage|Yes|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|SocketInboundAll|Yes|SocketInboundAll|Darabszám|Átlag|SocketInboundAll|Példány|
|SocketLoopback|Yes|SocketLoopback|Darabszám|Átlag|SocketLoopback|Példány|
|SocketOutboundAll|Yes|SocketOutboundAll|Darabszám|Átlag|SocketOutboundAll|Példány|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Darabszám|Átlag|SocketOutboundEstablished|Példány|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Darabszám|Átlag|SocketOutboundTimeWait|Példány|
|TcpCloseWait|Yes|TCP-bezárási várakozás|Darabszám|Átlag|TCP-bezárási várakozás|Példány|
|TcpClosing|Yes|TCP-zárás|Darabszám|Átlag|TCP-zárás|Példány|
|TcpEstablished|Yes|A TCP létrejött|Darabszám|Átlag|A TCP létrejött|Példány|
|TcpFinWait1|Yes|TCP fin WAIT 1|Darabszám|Átlag|TCP fin WAIT 1|Példány|
|TcpFinWait2|Yes|TCP fin WAIT 2|Darabszám|Átlag|TCP fin WAIT 2|Példány|
|TcpLastAck|Yes|TCP Last ACK|Darabszám|Átlag|TCP Last ACK|Példány|
|TcpSynReceived|Yes|TCP SYN kapott|Darabszám|Átlag|TCP SYN kapott|Példány|
|TcpSynSent|Yes|Eljuttatott TCP SYN|Darabszám|Átlag|Eljuttatott TCP SYN|Példány|
|TcpTimeWait|Yes|TCP-idő várakozása|Darabszám|Átlag|TCP-idő várakozása|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (a függvények kivételével) 

> [!NOTE]
> A **fájlrendszer használata** egy új metrika, amely globálisan zajlik, és nem számítunk fel adatokat, kivéve, ha Ön rendelkezik a privát előzetes verzióra vonatkozó engedélyezési listával.

> [!IMPORTANT]
> Az **átlagos válaszidő** a metrikák összesítésével való összekeveredés elkerülése érdekében elavulttá válik. A **válaszidő** használata csereként.

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|AverageMemoryWorkingSet|Yes|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő **(elavult)**|Másodperc|Átlag|Átlagos válaszidő|Példány|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CpuTime|Yes|CPU-idő|Másodperc|Összesen|CPU-idő|Példány|
|CurrentAssemblies|Yes|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|FileSystemUsage|Yes|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|Nincsenek méretek|
|Gen0Collections|Yes|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|Yes|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|Yes|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|Kezeli|Yes|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
|HealthCheckStatus|Yes|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http101|Yes|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Yes|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Yes|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Yes|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Yes|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Yes|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Yes|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|HttpResponseTime|Yes|Válaszidő|Másodperc|Átlag|Válaszidő|Példány|
|IoOtherBytesPerSecond|Yes|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoOtherOperationsPerSecond|Yes|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|IoReadBytesPerSecond|Yes|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Yes|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteBytesPerSecond|Yes|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoWriteOperationsPerSecond|Yes|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|MemoryWorkingSet|Yes|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|PrivateBytes|Yes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|RequestsInApplicationQueue|Yes|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|Szálak|Yes|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|TotalAppDomains|Yes|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Yes|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (functions)

> [!NOTE]
> A **fájlrendszer használata** egy új metrika, amely globálisan zajlik, és nem számítunk fel adatokat, kivéve, ha Ön rendelkezik a privát előzetes verzióra vonatkozó engedélyezési listával.

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|Yes|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CurrentAssemblies|Yes|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|FileSystemUsage|Yes|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|Nincsenek méretek|
|FunctionExecutionCount|Yes|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|FunctionExecutionUnits|Yes|Függvények végrehajtási egységei|Darabszám|Összesen|[Függvények végrehajtási egységei](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|Gen0Collections|Yes|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|Yes|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|Yes|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|HealthCheckStatus|Yes|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http5xx|Yes|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|IoOtherBytesPerSecond|Yes|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoOtherOperationsPerSecond|Yes|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|IoReadBytesPerSecond|Yes|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Yes|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteBytesPerSecond|Yes|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoWriteOperationsPerSecond|Yes|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|MemoryWorkingSet|Yes|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|PrivateBytes|Yes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|RequestsInApplicationQueue|Yes|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|TotalAppDomains|Yes|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Yes|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|AverageMemoryWorkingSet|Yes|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Yes|Átlagos válaszidő|Másodperc|Átlag|Átlagos válaszidő|Példány|
|BytesReceived|Yes|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Yes|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CpuTime|Yes|CPU-idő|Másodperc|Összesen|CPU-idő|Példány|
|CurrentAssemblies|Yes|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|FileSystemUsage|Yes|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|Nincsenek méretek|
|FunctionExecutionCount|Yes|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|FunctionExecutionUnits|Yes|Függvények végrehajtási egységei|Darabszám|Összesen|Függvények végrehajtási egységei|Példány|
|Gen0Collections|Yes|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|Yes|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|Yes|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|Kezeli|Yes|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
|HealthCheckStatus|Yes|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http101|Yes|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Yes|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Yes|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|Yes|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Yes|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Yes|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Yes|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Yes|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Yes|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|HttpResponseTime|Yes|Válaszidő|Másodperc|Átlag|Válaszidő|Példány|
|IoOtherBytesPerSecond|Yes|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoOtherOperationsPerSecond|Yes|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|IoReadBytesPerSecond|Yes|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Yes|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteBytesPerSecond|Yes|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoWriteOperationsPerSecond|Yes|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|MemoryWorkingSet|Yes|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|PrivateBytes|Yes|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|Kérelmek|Yes|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|RequestsInApplicationQueue|Yes|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|Szálak|Yes|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|TotalAppDomains|Yes|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Yes|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|


## <a name="next-steps"></a>További lépések
* [Tudnivalók a Azure Monitor mérőszámokról](data-platform.md)
* [Riasztások létrehozása metrikákhoz](alerts-overview.md)
* [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](platform-logs-overview.md)
