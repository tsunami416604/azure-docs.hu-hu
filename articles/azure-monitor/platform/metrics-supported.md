---
title: Azure Monitor támogatott metrikák erőforrástípus szerint
description: Az egyes erőforrástípusok számára elérhető metrikák listája Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 59df49d320b23686a3d053335ea2b95e98125b28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88135555"
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

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Igen|Memória: tisztító – aktuális ár|Darabszám|Átlag|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|ServerResourceType|
|CleanerMemoryNonshrinkable|Igen|Memória: nem csökkenthető a tisztább memória|Bájt|Átlag|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|ServerResourceType|
|CleanerMemoryShrinkable|Igen|Memória: tisztító memória csökkenthető|Bájt|Átlag|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|ServerResourceType|
|CommandPoolBusyThreads|Igen|Szálak: a parancssori készlet foglalt szálak|Darabszám|Átlag|A parancsfájl-készletben lévő foglalt szálak száma.|ServerResourceType|
|CommandPoolIdleThreads|Igen|Szálak: parancssori készlet üresjárati szálai|Darabszám|Átlag|Az üresjárati szálak száma a parancs szálának készletében.|ServerResourceType|
|CommandPoolJobQueueLength|Igen|Parancssori feladatok várólistájának hossza|Darabszám|Átlag|A parancsfájl-készlet várólistájában lévő feladatok száma.|ServerResourceType|
|Összege|Igen|Kapcsolat: aktuális kapcsolatok|Darabszám|Átlag|A létesített ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CurrentUserSessions|Igen|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrejött felhasználói munkamenetek aktuális száma.|ServerResourceType|
|LongParsingBusyThreads|Igen|Szálak: hosszú elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingIdleThreads|Igen|Szálak: tartós folyamatok elemzése – üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a hosszú elemzési szál készletében.|ServerResourceType|
|LongParsingJobQueueLength|Igen|Szálak: hosszú elemzési feladatok várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|mashup_engine_memory_metric|Igen|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_private_bytes_metric|Igen|M motor saját bájtjai|Bájt|Átlag|Saját bájtok használata az adategyesítési motor folyamatai alapján.|ServerResourceType|
|mashup_engine_qpu_metric|Igen|M motor QPU|Darabszám|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Igen|M motor virtuális bájtjai|Bájt|Átlag|Az adategyesítési motor folyamatai által használt virtuális bájtok használata.|ServerResourceType|
|memory_metric|Igen|Memória|Bájt|Átlag|Memory. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|ServerResourceType|
|memory_thrashing_metric|Igen|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|ServerResourceType|
|MemoryLimitHard|Igen|Memória: rögzített memória|Bájt|Átlag|Rögzített memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Igen|Memória: magas a memória korlátozása|Bájt|Átlag|Magas memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow|Igen|Memória: kevés a memória korlátja|Bájt|Átlag|Kevés a memória korlátja a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Igen|Memória: VertiPaq korlátja|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|ServerResourceType|
|MemoryUsage|Igen|Memória: memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memória által leképezett adatmennyiséggel, figyelmen kívül hagyva azokat a memóriát, amelyeket a xVelocity memórián belüli elemzési motorja (VertiPaq) leképezett vagy lefoglalt, és amely meghaladja a xVelocity-memória korlátját.|ServerResourceType|
|private_bytes_metric|Igen|Saját bájtok|Bájt|Átlag|Saját bájtok.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Igen|Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai|Darabszám|Átlag|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Igen|Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva|Darabszám|Átlag|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Igen|Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Igen|Szálak: feldolgozási készlet üresjáratban nem I/O-szálai|Darabszám|Átlag|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Igen|Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Igen|Feldolgozási készlet nyomtatási várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|ServerResourceType|
|qpu_metric|Igen|QPU|Darabszám|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|ServerResourceType|
|QueryPoolBusyThreads|Igen|Lekérdezési készlet foglalt szálai|Darabszám|Átlag|A lekérdezési szál készletében lévő foglalt szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Igen|Szálak: lekérdezési készlet – üresjárati szálak|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|ServerResourceType|
|QueryPoolJobQueueLength|Igen|Szálak: lekérdezési készlet feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|Kvóta|Igen|Memória: kvóta|Bájt|Átlag|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|ServerResourceType|
|QuotaBlocked|Igen|Memória: blokkolt kvóta|Darabszám|Átlag|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|ServerResourceType|
|RowsConvertedPerSec|Igen|Feldolgozás: másodpercenként konvertált sorok száma|CountPerSecond|Átlag|A sorok konvertálásának sebessége a feldolgozás során.|ServerResourceType|
|RowsReadPerSec|Igen|Feldolgozás: másodpercenként beolvasott sorok száma|CountPerSecond|Átlag|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|ServerResourceType|
|RowsWrittenPerSec|Igen|Feldolgozás: másodpercenként írt sorok száma|CountPerSecond|Átlag|A sorok írásának sebessége a feldolgozás során.|ServerResourceType|
|ShortParsingBusyThreads|Igen|Szálak: rövid elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingIdleThreads|Igen|Szálak: rövid elemzési üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|ServerResourceType|
|ShortParsingJobQueueLength|Igen|Szálak: rövid elemzési feladatok várólistájának hossza|Darabszám|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|ServerResourceType|
|SuccessfullConnectionsPerSec|Igen|Sikeres kapcsolatok másodpercenként|CountPerSecond|Átlag|A sikeres kapcsolatok gyakorisága.|ServerResourceType|
|TotalConnectionFailures|Igen|Összes sikertelen Kapcsolatfelvétel|Darabszám|Átlag|A sikertelen csatlakozási kísérletek teljes száma.|ServerResourceType|
|TotalConnectionRequests|Igen|Kapcsolatkérelmek teljes száma|Darabszám|Átlag|A kapcsolatkérelmek teljes száma. Ezek az érkezések.|ServerResourceType|
|VertiPaqNonpaged|Igen|Memória: VertiPaq, nem lapozható|Bájt|Átlag|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|ServerResourceType|
|VertiPaqPaged|Igen|Memória: VertiPaq lapozható|Bájt|Átlag|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|ServerResourceType|
|virtual_bytes_metric|Igen|Felhasznált virtuális memória jelenlegi mérete (bájt)|Bájt|Átlag|Virtuális bájtok.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BackendDuration|Igen|A háttérbeli kérelmek időtartama|Ezredmásodpercben|Átlag|A háttérbeli kérelmek időtartama (ezredmásodpercben)|Hely, állomásnév|
|Kapacitás|Igen|Kapacitás|Százalék|Átlag|A ApiManagement szolgáltatás kihasználtsági metrikája|Hely|
|Időtartam|Igen|Az átjárók kéréseinek teljes időtartama|Ezredmásodpercben|Átlag|Az átjáró kérelmek teljes időtartama ezredmásodpercben|Hely, állomásnév|
|EventHubDroppedEvents|Igen|Eldobott EventHub események|Darabszám|Összesen|A várólista-méretkorlát elérte miatt kihagyott események száma|Hely|
|EventHubRejectedEvents|Igen|EventHub-események visszautasítva|Darabszám|Összesen|Visszautasított EventHub-események száma (helytelen konfiguráció vagy jogosulatlan)|Hely|
|EventHubSuccessfulEvents|Igen|Sikeres EventHub események|Darabszám|Összesen|Sikeres EventHub események száma|Hely|
|EventHubThrottledEvents|Igen|Szabályozott EventHub események|Darabszám|Összesen|A szabályozott EventHub események száma|Hely|
|EventHubTimedoutEvents|Igen|Időtúllépés a EventHub eseményeinél|Darabszám|Összesen|Az időtúllépés miatti EventHub események száma|Hely|
|EventHubTotalBytesSent|Igen|EventHub-események mérete|Bájt|Összesen|EventHub-események teljes mérete bájtban|Hely|
|EventHubTotalEvents|Igen|Összes EventHub esemény|Darabszám|Összesen|A EventHub eljuttatott események száma|Hely|
|EventHubTotalFailedEvents|Igen|Sikertelen EventHub események|Darabszám|Összesen|Sikertelen EventHub események száma|Hely|
|FailedRequests|Igen|Sikertelen átjáró-kérelmek (elavult)|Darabszám|Összesen|Hibák száma az átjáró kéréseiben – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|NetworkConnectivity|Igen|Erőforrások hálózati kapcsolati állapota (előzetes verzió)|Darabszám|Átlag|A API Management szolgáltatástól függő erőforrástípusok hálózati kapcsolati állapota|Hely, ResourceType|
|OtherRequests|Igen|Egyéb átjáró-kérelmek (elavult)|Darabszám|Összesen|Egyéb átjáró-kérelmek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|Több dimenzióval rendelkező átjáró-kérelmek metrikái|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Igen|Sikeres átjáró-kérelmek (elavult)|Darabszám|Összesen|Sikeres átjáró-kérések száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzió helyett|Hely, állomásnév|
|TotalRequests|Igen|Összes átjáró kérelme (elavult)|Darabszám|Összesen|Átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|
|UnauthorizedRequests|Igen|Nem engedélyezett átjáró-kérelmek (elavult)|Darabszám|Összesen|Jogosulatlan átjárók kéréseinek száma – a többdimenziós kérelmek metrikájának használata GatewayResponseCodeCategory-dimenzióval|Hely, állomásnév|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Igen|HttpIncomingRequestCount|Darabszám|Darabszám|A bejövő HTTP-kérelmek teljes száma.|StatusCode, hitelesítés|
|HttpIncomingRequestDuration|Igen|HttpIncomingRequestDuration|Darabszám|Átlag|Egy HTTP-kérelem késése.|StatusCode, hitelesítés|
|ThrottledHttpRequestCount|Igen|ThrottledHttpRequestCount|Darabszám|Darabszám|Szabályozott HTTP-kérelmek.|Nincsenek méretek|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|JVM. GC. Live. Resize|Igen|JVM. GC. Live. Resize|Bájt|Átlag|Régi generációs memória-készlet mérete teljes GC után|Üzembe helyezés, AppName, Pod|
|JVM. GC. max. az adatméret|Igen|JVM. GC. max. az adatméret|Bájt|Átlag|A régi generációs memória maximális mérete|Üzembe helyezés, AppName, Pod|
|JVM. GC. Memory. lefoglalt|Igen|JVM. GC. Memory. lefoglalt|Bájt|Maximum|Növekszik a fiatal generációs memória-készlet méretének növekedésével, miután egy GC a következőre kerül|Üzembe helyezés, AppName, Pod|
|JVM. GC. Memory. előléptetve|Igen|JVM. GC. Memory. előléptetve|Bájt|Maximum|A régi generációs memória-készlet méretének pozitív növekedésének száma, mielőtt a GC a GC-re lenne|Üzembe helyezés, AppName, Pod|
|JVM. GC. pause. Total. Count|Igen|JVM. GC. pause. Total. Count|Darabszám|Összesen|GC-szüneteltetések száma|Üzembe helyezés, AppName, Pod|
|JVM. GC. pause. Total. Time|Igen|JVM. GC. pause. Total. Time|Ezredmásodpercben|Összesen|GC felfüggesztésének teljes ideje|Üzembe helyezés, AppName, Pod|
|JVM. Memory. véglegesített|Igen|JVM. Memory. véglegesített|Bájt|Átlag|JVM rendelt memória bájtban|Üzembe helyezés, AppName, Pod|
|JVM. Memory. max|Igen|JVM. Memory. max|Bájt|Maximum|A memória-kezeléshez használható memória maximális mennyisége bájtban|Üzembe helyezés, AppName, Pod|
|JVM. Memory. felhasználva|Igen|JVM. Memory. felhasználva|Bájt|Átlag|Az alkalmazás memóriája bájtban használatban|Üzembe helyezés, AppName, Pod|
|Process. CPU. használat|Igen|Process. CPU. használat|Százalék|Átlag|Alkalmazás JVM CPU-kihasználtsági aránya|Üzembe helyezés, AppName, Pod|
|System. CPU. használat|Igen|System. CPU. használat|Százalék|Átlag|A legújabb CPU-használat a teljes rendszerhez|Üzembe helyezés, AppName, Pod|
|tomcat. Global. error|Igen|tomcat. Global. error|Darabszám|Összesen|Tomcat globális hiba|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Received|Igen|tomcat. Global. Received|Bájt|Összesen|Tomcat összesen fogadott bájtok|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. AVG. Time|Igen|tomcat. Global. Request. AVG. Time|Ezredmásodpercben|Átlag|Tomcat-kérelem átlagos ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. max|Igen|tomcat. Global. Request. max|Ezredmásodpercben|Maximum|Tomcat-kérelem maximális ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. Total. Count|Igen|tomcat. Global. Request. Total. Count|Darabszám|Összesen|Tomcat-kérelem összesített száma|Üzembe helyezés, AppName, Pod|
|tomcat. Global. Request. Total. Time|Igen|tomcat. Global. Request. Total. Time|Ezredmásodpercben|Összesen|Tomcat-kérelem teljes ideje|Üzembe helyezés, AppName, Pod|
|tomcat. Global. elküldve|Igen|tomcat. Global. elküldve|Bájt|Összesen|Tomcat összesen eljuttatott bájtok száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Active. Current|Igen|tomcat. Sessions. Active. Current|Darabszám|Összesen|Tomcat-munkamenet aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Active. max|Igen|tomcat. Sessions. Active. max|Darabszám|Összesen|Tomcat-munkamenet maximális aktív száma|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. Alive. max|Igen|tomcat. Sessions. Alive. max|Ezredmásodpercben|Maximum|Tomcat-munkamenet maximális élettartama|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. created|Igen|tomcat. Sessions. created|Darabszám|Összesen|Tomcat-munkamenet létrehozva szám|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. lejárt|Igen|tomcat. Sessions. lejárt|Darabszám|Összesen|A Tomcat-munkamenet lejárt|Üzembe helyezés, AppName, Pod|
|tomcat. Sessions. elutasítva|Igen|tomcat. Sessions. elutasítva|Darabszám|Összesen|A Tomcat-munkamenet elutasította a darabszámot|Üzembe helyezés, AppName, Pod|
|tomcat.threads.config. max|Igen|tomcat.threads.config. max|Darabszám|Összesen|Tomcat-konfiguráció maximális szálának száma|Üzembe helyezés, AppName, Pod|
|tomcat. Threads. Current|Igen|tomcat. Threads. Current|Darabszám|Összesen|Tomcat jelenlegi szálak száma|Üzembe helyezés, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|TotalJob|Igen|Feladatok összesen|Darabszám|Összesen|A feladatok teljes száma|Runbook, állapot|
|TotalUpdateDeploymentMachineRuns|Igen|Összes frissítés központi telepítési gép futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének teljes futtatása a szoftverfrissítési központi telepítési gépen|SoftwareUpdateConfigurationName, állapot, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Igen|Összes frissítés központi telepítési futtatása|Darabszám|Összesen|Szoftverfrissítés központi telepítésének összes futtatása|SoftwareUpdateConfigurationName, állapot|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CoreCount|Nem|Dedikált mag száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált magok teljes száma|Nincsenek méretek|
|CreatingNodeCount|Nem|Csomópontok számának létrehozása|Darabszám|Összesen|Létrehozandó csomópontok száma|Nincsenek méretek|
|IdleNodeCount|Nem|Tétlen csomópontok száma|Darabszám|Összesen|Üresjárati csomópontok száma|Nincsenek méretek|
|JobDeleteCompleteEvent|Igen|Feladatok törlése – befejezett események|Darabszám|Összesen|A sikeresen törölt feladatok teljes száma.|jobId|
|JobDeleteStartEvent|Igen|Feladatok törlésének indítási eseményei|Darabszám|Összesen|A törölni kívánt feladatok teljes száma.|jobId|
|JobDisableCompleteEvent|Igen|A feladatok letiltják a teljes eseményeket|Darabszám|Összesen|A sikeresen letiltott feladatok teljes száma.|jobId|
|JobDisableStartEvent|Igen|Feladatok letiltásának indítási eseményei|Darabszám|Összesen|A letiltani kívánt feladatok teljes száma.|jobId|
|JobStartEvent|Igen|Feladatok indítási eseményei|Darabszám|Összesen|A sikeresen elindított feladatok teljes száma.|jobId|
|JobTerminateCompleteEvent|Igen|A feladatokhoz tartozó befejezett események befejezése|Darabszám|Összesen|A sikeresen leállított feladatok teljes száma.|jobId|
|JobTerminateStartEvent|Igen|Feladatokból indított események leállítása|Darabszám|Összesen|A leállítani kívánt feladatok teljes száma.|jobId|
|LeavingPoolNodeCount|Nem|A készlet-csomópontok számának elhagyása|Darabszám|Összesen|A készletet elhagyó csomópontok száma|Nincsenek méretek|
|LowPriorityCoreCount|Nem|LowPriority mag száma|Darabszám|Összesen|Az alacsony prioritású magok teljes száma a Batch-fiókban|Nincsenek méretek|
|OfflineNodeCount|Nem|Offline csomópontok száma|Darabszám|Összesen|Offline csomópontok száma|Nincsenek méretek|
|PoolCreateEvent|Igen|Készlet-létrehozási események|Darabszám|Összesen|A létrehozott készletek teljes száma|poolId|
|PoolDeleteCompleteEvent|Igen|Készlet törlése – befejezett események|Darabszám|Összesen|A befejezett készlet-törlések teljes száma|poolId|
|PoolDeleteStartEvent|Igen|Készlet törlése – indítási események|Darabszám|Összesen|Az elindított készlet-törlések száma összesen|poolId|
|PoolResizeCompleteEvent|Igen|Készlet átméretezése – befejezett események|Darabszám|Összesen|A készlet összes újraméretezésének száma|poolId|
|PoolResizeStartEvent|Igen|Készlet átméretezésének indítási eseményei|Darabszám|Összesen|A készlet elindított újraméretezésének teljes száma|poolId|
|PreemptedNodeCount|Nem|Előzik-csomópontok száma|Darabszám|Összesen|Előzik-csomópontok száma|Nincsenek méretek|
|RebootingNodeCount|Nem|Csomópontok számának újraindítása|Darabszám|Összesen|Újraindítási csomópontok száma|Nincsenek méretek|
|ReimagingNodeCount|Nem|Csomópontok rendszerképének alaphelyzetbe állítása|Darabszám|Összesen|Rendszerkép-csomópontok száma|Nincsenek méretek|
|RunningNodeCount|Nem|Csomópontok száma|Darabszám|Összesen|Futó csomópontok száma|Nincsenek méretek|
|StartingNodeCount|Nem|Csomópontok számának indítása|Darabszám|Összesen|Kiinduló csomópontok száma|Nincsenek méretek|
|StartTaskFailedNodeCount|Nem|Indítási feladat sikertelen csomópontok száma|Darabszám|Összesen|Azon csomópontok száma, amelyekben az indítási tevékenység meghiúsult|Nincsenek méretek|
|TaskCompleteEvent|Igen|Feladat teljes eseményei|Darabszám|Összesen|A Befejezett feladatok teljes száma|poolId, jobId|
|TaskFailEvent|Igen|Feladat sikertelen eseményei|Darabszám|Összesen|A meghiúsult állapotban Befejezett feladatok teljes száma|poolId, jobId|
|TaskStartEvent|Igen|Tevékenységek indítási eseményei|Darabszám|Összesen|Az elindított feladatok teljes száma|poolId, jobId|
|TotalLowPriorityNodeCount|Nem|Low-Priority csomópontok száma|Darabszám|Összesen|Az alacsony prioritású csomópontok teljes száma a Batch-fiókban|Nincsenek méretek|
|TotalNodeCount|Nem|Dedikált csomópontok száma|Darabszám|Összesen|A Batch-fiókban lévő dedikált csomópontok teljes száma|Nincsenek méretek|
|UnusableNodeCount|Nem|Nem használható csomópontok száma|Darabszám|Összesen|Használhatatlan csomópontok száma|Nincsenek méretek|
|WaitingForStartTaskNodeCount|Nem|Várakozás a feladat-csomópontok számának megkezdésére|Darabszám|Összesen|Az indítási tevékenység befejezésére váró csomópontok száma|Nincsenek méretek|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Igen|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Igen|Aktív csomópontok|Darabszám|Átlag|Futó csomópontok száma|Forgatókönyv, ClusterName|
|Üresjárati magok|Igen|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Igen|Tétlen csomópontok|Darabszám|Átlag|Üresjárati csomópontok száma|Forgatókönyv, ClusterName|
|A feladatok befejeződtek|Igen|A feladatok befejeződtek|Darabszám|Összesen|Befejezett feladatok száma|Forgatókönyv, ClusterName, ResultType|
|Feladatok elküldve|Igen|Feladatok elküldve|Darabszám|Összesen|Elküldött feladatok száma|Forgatókönyv, ClusterName|
|Magok kihagyása|Igen|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Igen|Csomópontok elhagyása|Darabszám|Átlag|Kilépő csomópontok száma|Forgatókönyv, ClusterName|
|Előzik magok|Igen|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Igen|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma|Forgatókönyv, ClusterName|
|Kvóta kihasználtsága (%)|Igen|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Magok összesen|Igen|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Csomópontok összesen|Igen|Csomópontok összesen|Darabszám|Átlag|Összes csomópont száma|Forgatókönyv, ClusterName|
|Használhatatlan magok|Igen|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Igen|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma|Forgatókönyv, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Igen|Szórásos feldolgozott darabszám|Darabszám|Átlag|A feldolgozott tranzakciók száma|Csomópont, csatorna, típus, állapot|
|ConnectionAccepted|Igen|Elfogadott kapcsolatok|Darabszám|Összesen|Elfogadott kapcsolatok|Csomópont|
|ConnectionActive|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Csomópont|
|ConnectionHandled|Igen|Kezelt kapcsolatok|Darabszám|Összesen|Kezelt kapcsolatok|Csomópont|
|ConsensusEtcdraftCommittedBlockNumber|Igen|Konszenzus Etcdraft véglegesített blokk száma|Darabszám|Átlag|A legutolsó blokkolt blokk blokkjának száma|Csomópont, csatorna|
|CpuUsagePercentageInDouble|Igen|CPU-használat százaléka|Százalék|Maximum|CPU-használat százaléka|Csomópont|
|EndorserEndorsementFailures|Igen|A jóváhagyói jóváhagyások hibái|Darabszám|Átlag|A sikertelen jóváhagyások száma.|Csomópont, csatorna, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|Igen|Gossip Leader-választási vezető|Darabszám|Összesen|Társ a vezető (1) vagy követője (0)|Csomópont, csatorna|
|GossipMembershipTotalPeersKnown|Igen|Pletykák tagságának összes ismert társa|Darabszám|Átlag|Összes ismert társ|Csomópont, csatorna|
|GossipStateHeight|Igen|Pletyka állapotának magassága|Darabszám|Átlag|Aktuális Főkönyv magassága|Csomópont, csatorna|
|IOReadBytes|Igen|IO olvasási bájtok|Bájt|Összesen|IO olvasási bájtok|Csomópont|
|IOWriteBytes|Igen|IO írási bájtjai|Bájt|Összesen|IO írási bájtjai|Csomópont|
|LedgerTransactionCount|Igen|Főkönyvi tranzakciók száma|Darabszám|Átlag|Feldolgozott tranzakciók száma|Csomópont, csatorna, transaction_type, chaincode, validation_code|
|MemoryLimit|Igen|Memória korlátja|Bájt|Átlag|Memória korlátja|Csomópont|
|MemoryUsage|Igen|Memóriahasználat|Bájt|Átlag|Memóriahasználat|Csomópont|
|MemoryUsagePercentageInDouble|Igen|Memóriahasználat százaléka|Százalék|Átlag|Memóriahasználat százaléka|Csomópont|
|PendingTransactions|Igen|Függőben lévő tranzakciók|Darabszám|Átlag|Függőben lévő tranzakciók|Csomópont|
|ProcessedBlocks|Igen|Feldolgozott blokkok|Darabszám|Összesen|Feldolgozott blokkok|Csomópont|
|ProcessedTransactions|Igen|Feldolgozott tranzakciók|Darabszám|Összesen|Feldolgozott tranzakciók|Csomópont|
|QueuedTransactions|Igen|Várólistán lévő tranzakciók|Darabszám|Átlag|Várólistán lévő tranzakciók|Csomópont|
|RequestHandled|Igen|Kezelt kérelmek|Darabszám|Összesen|Kezelt kérelmek|Csomópont|
|StorageUsage|Igen|Tárterület-használat|Bájt|Átlag|Tárterület-használat|Csomópont|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|cachehits|Igen|Gyorsítótár-találatok|Darabszám|Összesen||ShardId|
|cachehits0|Igen|Gyorsítótárbeli találatok (szegmens 0)|Darabszám|Összesen||Nincsenek méretek|
|cachehits1|Igen|Gyorsítótárbeli találatok (1. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits2|Igen|Gyorsítótárbeli találatok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachehits3|Igen|Gyorsítótárbeli találatok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits4|Igen|Gyorsítótár-találatok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|cachehits5|Igen|Gyorsítótárbeli találatok (szegmens 5)|Darabszám|Összesen||Nincsenek méretek|
|cachehits6|Igen|Gyorsítótárbeli találatok (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|cachehits7|Igen|Gyorsítótárbeli találatok (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|cachehits8|Igen|Gyorsítótárbeli találatok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|cachehits9|Igen|Gyorsítótárbeli találatok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|cacheLatency|Igen|Gyorsítótár késési másodpercek (előzetes verzió)|Darabszám|Átlag||ShardId|
|cachemisses|Igen|Gyorsítótár-tévesztések|Darabszám|Összesen||ShardId|
|cachemisses0|Igen|Gyorsítótár-kihagyás (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses1|Igen|Gyorsítótár-lemaradás (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses2|Igen|Gyorsítótár-lemaradás (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses3|Igen|Gyorsítótár-lemaradás (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses4|Igen|Gyorsítótár-lemaradás (4. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses5|Igen|Gyorsítótár-lemaradás (5. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses6|Igen|Gyorsítótár-lemaradás (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses7|Igen|Gyorsítótár-lemaradás (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses8|Igen|Gyorsítótár-lemaradás (szegmens 8)|Darabszám|Összesen||Nincsenek méretek|
|cachemisses9|Igen|Gyorsítótár-lemaradás (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|cachemissrate|Igen|Gyorsítótár-kihagyás aránya|Százalék|cachemissrate||ShardId|
|cacheRead|Igen|Gyorsítótár-olvasás|BytesPerSecond|Maximum||ShardId|
|cacheRead0|Igen|Gyorsítótár-olvasás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead1|Igen|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead2|Igen|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead3|Igen|Gyorsítótár olvasása (3. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead4|Igen|Gyorsítótár olvasása (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead5|Igen|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead6|Igen|Gyorsítótár olvasása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead7|Igen|Gyorsítótár olvasása (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead8|Igen|Gyorsítótár olvasása (szilánk 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheRead9|Igen|Gyorsítótár olvasása (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite|Igen|Gyorsítótár-írás|BytesPerSecond|Maximum||ShardId|
|cacheWrite0|Igen|Gyorsítótár-írás (0. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite1|Igen|Gyorsítótár-írás (1. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite2|Igen|Gyorsítótár-írás (2. szegmens)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite3|Igen|Gyorsítótár-írás (3. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite4|Igen|Gyorsítótár-írás (4. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite5|Igen|Gyorsítótár-írás (5. Szilánk)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite6|Igen|Gyorsítótár írása (szegmens 6)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite7|Igen|Gyorsítótár-írás (szegmens 7)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite8|Igen|Gyorsítótár-írás (szegmens 8)|BytesPerSecond|Maximum||Nincsenek méretek|
|cacheWrite9|Igen|Gyorsítótár-írás (szegmens 9)|BytesPerSecond|Maximum||Nincsenek méretek|
|connectedclients|Igen|Csatlakoztatott ügyfelek|Darabszám|Maximum||ShardId|
|connectedclients0|Igen|Csatlakoztatott ügyfelek (0. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients1|Igen|Csatlakoztatott ügyfelek (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients2|Igen|Csatlakoztatott ügyfelek (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients3|Igen|Csatlakoztatott ügyfelek (3. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients4|Igen|Csatlakoztatott ügyfelek (4. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients5|Igen|Csatlakoztatott ügyfelek (5. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients6|Igen|Csatlakoztatott ügyfelek (6. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients7|Igen|Csatlakoztatott ügyfelek (7. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients8|Igen|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|connectedclients9|Igen|Csatlakoztatott ügyfelek (10. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|Hibák|Igen|Hibák|Darabszám|Maximum||ShardId, ErrorType|
|evictedkeys|Igen|Kizárt kulcsok|Darabszám|Összesen||ShardId|
|evictedkeys0|Igen|Kizárt kulcsok (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys1|Igen|Kizárt kulcsok (1. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys2|Igen|Kizárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys3|Igen|Kizárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys4|Igen|Kizárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys5|Igen|Kizárt kulcsok (5. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys6|Igen|Kizárt kulcsok (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys7|Igen|Kizárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys8|Igen|Kizárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|evictedkeys9|Igen|Kizárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys|Igen|Lejárt kulcsok|Darabszám|Összesen||ShardId|
|expiredkeys0|Igen|Lejárt kulcsok (szilánk 0)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys1|Igen|Lejárt kulcsok (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys2|Igen|Lejárt kulcsok (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys3|Igen|Lejárt kulcsok (3. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys4|Igen|Lejárt kulcsok (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys5|Igen|Lejárt kulcsok (szilánk 5)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys6|Igen|Lejárt kulcsok (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys7|Igen|Lejárt kulcsok (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys8|Igen|Lejárt kulcsok (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|expiredkeys9|Igen|Lejárt kulcsok (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|getcommands|Igen|Lekérések|Darabszám|Összesen||ShardId|
|getcommands0|Igen|Beolvasás (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands1|Igen|Beolvasás (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands2|Igen|Beolvasás (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands3|Igen|Beolvasás (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|getcommands4|Igen|Beolvasás (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|getcommands5|Igen|Beolvasás (szegmens 5)|Darabszám|Összesen||Nincsenek méretek|
|getcommands6|Igen|Beolvasás (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|getcommands7|Igen|Beolvasás (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|getcommands8|Igen|Beolvasás (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|getcommands9|Igen|Beolvasás (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|operationsPerSecond|Igen|Műveletek száma másodpercenként|Darabszám|Maximum||ShardId|
|operationsPerSecond0|Igen|Művelet/másodperc (0. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond1|Igen|Művelet/másodperc (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond2|Igen|Művelet/másodperc (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond3|Igen|Művelet/másodperc (3. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond4|Igen|Művelet/másodperc (4. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond5|Igen|Művelet/másodperc (5. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond6|Igen|Művelet/másodperc (szegmens 6)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond7|Igen|Művelet/másodperc (7. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond8|Igen|Művelet/másodperc (szegmens 8)|Darabszám|Maximum||Nincsenek méretek|
|operationsPerSecond9|Igen|Művelet/másodperc (szegmens 9)|Darabszám|Maximum||Nincsenek méretek|
|percentProcessorTime|Igen|CPU|Százalék|Maximum||ShardId|
|percentProcessorTime0|Igen|CPU (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime1|Igen|CPU (1. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime2|Igen|CPU (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime3|Igen|CPU (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime4|Igen|CPU (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime5|Igen|CPU (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime6|Igen|CPU (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime7|Igen|PROCESSZOR (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime8|Igen|CPU (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|percentProcessorTime9|Igen|CPU (szilánk 9)|Százalék|Maximum||Nincsenek méretek|
|serverLoad|Igen|Kiszolgáló terhelése|Százalék|Maximum||ShardId|
|serverLoad0|Igen|Kiszolgáló terhelése (0. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad1|Igen|Kiszolgáló terhelése (1. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad2|Igen|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad3|Igen|Kiszolgáló terhelése (3. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad4|Igen|Kiszolgáló terhelése (4. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad5|Igen|Kiszolgáló terhelése (5. Szilánk)|Százalék|Maximum||Nincsenek méretek|
|serverLoad6|Igen|Kiszolgáló betöltése (szegmens 6)|Százalék|Maximum||Nincsenek méretek|
|serverLoad7|Igen|Kiszolgáló terhelése (7. szegmens)|Százalék|Maximum||Nincsenek méretek|
|serverLoad8|Igen|Kiszolgáló betöltése (szegmens 8)|Százalék|Maximum||Nincsenek méretek|
|serverLoad9|Igen|Kiszolgáló terhelése (szegmens 9)|Százalék|Maximum||Nincsenek méretek|
|setcommands|Igen|Készletek|Darabszám|Összesen||ShardId|
|setcommands0|Igen|Készletek (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands1|Igen|Készletek (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands2|Igen|Készletek (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands3|Igen|Készletek (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|setcommands4|Igen|Készletek (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|setcommands5|Igen|Készletek (szilánk 5)|Darabszám|Összesen||Nincsenek méretek|
|setcommands6|Igen|Készletek (szilánk 6)|Darabszám|Összesen||Nincsenek méretek|
|setcommands7|Igen|Készletek (szegmens 7)|Darabszám|Összesen||Nincsenek méretek|
|setcommands8|Igen|Készletek (szilánk 8)|Darabszám|Összesen||Nincsenek méretek|
|setcommands9|Igen|Készletek (szilánk 9)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed|Igen|Műveletek összesen|Darabszám|Összesen||ShardId|
|totalcommandsprocessed0|Igen|Összes művelet (0. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed1|Igen|Összes művelet (1. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed2|Igen|Összes művelet (2. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed3|Igen|Összes művelet (3. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed4|Igen|Összes művelet (4. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed5|Igen|Összes művelet (5. Szilánk)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed6|Igen|Összes művelet (szegmens 6)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed7|Igen|Összes művelet (7. szegmens)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed8|Igen|Összes művelet (szegmens 8)|Darabszám|Összesen||Nincsenek méretek|
|totalcommandsprocessed9|Igen|Összes művelet (szegmens 9)|Darabszám|Összesen||Nincsenek méretek|
|totalkeys|Igen|Kulcsok összesen|Darabszám|Maximum||ShardId|
|totalkeys0|Igen|Összes kulcs (szilánk 0)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys1|Igen|Összes kulcs (1. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys2|Igen|Összes kulcs (2. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys3|Igen|Összes kulcs (3. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys4|Igen|Összes kulcs (4. Szilánk)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys5|Igen|Összes kulcs (5. szegmens)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys6|Igen|Összes kulcs (szilánk 6)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys7|Igen|Összes kulcs (szegmens 7)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys8|Igen|Összes kulcs (szilánk 8)|Darabszám|Maximum||Nincsenek méretek|
|totalkeys9|Igen|Összes kulcs (szilánk 9)|Darabszám|Maximum||Nincsenek méretek|
|usedmemory|Igen|Felhasznált memória|Bájt|Maximum||ShardId|
|usedmemory0|Igen|Felhasznált memória (0. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory1|Igen|Felhasznált memória (1. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory2|Igen|Felhasznált memória (2. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory3|Igen|Felhasznált memória (3. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory4|Igen|Felhasznált memória (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory5|Igen|Felhasznált memória (5. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory6|Igen|Felhasznált memória (6. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemory7|Igen|Felhasznált memória (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemory8|Igen|Felhasznált memória (8. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemory9|Igen|Felhasznált memória (10. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemorypercentage|Igen|Felhasznált memória százalékos aránya|Százalék|Maximum||ShardId|
|usedmemoryRss|Igen|Felhasznált memória RSS-címe|Bájt|Maximum||ShardId|
|usedmemoryRss0|Igen|Használt memória RSS-je (0. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss1|Igen|Használt memória RSS-je (1. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss2|Igen|Használt memória RSS-je (2. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss3|Igen|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss4|Igen|Használt memória RSS-je (4. Szilánk)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss5|Igen|Használt memória RSS-je (5. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss6|Igen|Használt memória RSS-je (3. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss7|Igen|Használt memória RSS-je (7. szegmens)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss8|Igen|Használt memória RSS-je (szegmens 8)|Bájt|Maximum||Nincsenek méretek|
|usedmemoryRss9|Igen|Használt memória RSS-je (szegmens 9)|Bájt|Maximum||Nincsenek méretek|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Igen|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási sebessége (bájt/s)|Nem|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|RoleInstanceId|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|RoleInstanceId|
|Lemez írási sebessége (bájt/s)|Nem|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|RoleInstanceId|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|RoleInstanceId|
|Bejövő hálózat|Igen|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|RoleInstanceId|
|Kimenő hálózat|Igen|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|RoleInstanceId|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási sebessége (bájt/s)|Nem|Lemez olvasása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezről beolvasott bájtok átlagos száma.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS.|Nincsenek méretek|
|Lemez írási sebessége (bájt/s)|Nem|Lemez írása|BytesPerSecond|Átlag|A megfigyelési időszak során lemezre írt bájtok átlagos száma.|Nincsenek méretek|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS.|Nincsenek méretek|
|Bejövő hálózat|Igen|Bejövő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom).|Nincsenek méretek|
|Kimenő hálózat|Igen|Kimenő hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom).|Nincsenek méretek|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|Nincsenek méretek|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|UsedCapacity|Nem|Felhasznált kapacitás|Bájt|Átlag|Felhasznált fiókkapacitás|Nincsenek méretek|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|BlobCapacity|Nem|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Nem|Blobok száma|Darabszám|Átlag|A Storage-fiók Blob service található Blobok száma.|BlobType, szintű|
|ContainerCount|Igen|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiók Blob service lévő tárolók száma.|Nincsenek méretek|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|IndexCapacity|Nem|Index kapacitása|Bájt|Átlag|A ADLS Gen2 (hierarchikus) index által felhasznált tárterület mérete bájtban megadva.|Nincsenek méretek|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|FileCapacity|Nem|Fájl kapacitása|Bájt|Átlag|A Storage-fiók Fájlszolgáltatások által felhasznált tárterület mérete bájtban megadva.|Fájlmegosztás|
|FileCount|Nem|Fájlok száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|Nem|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiók Fájlszolgáltatások szolgáltatásában lévő fájlmegosztás száma.|Nincsenek méretek|
|FileShareQuota|Nem|Fájlmegosztás kvótájának mérete|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|FileShareSnapshotCount|Nem|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|Nem|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|QueueCapacity|Igen|Várólista kapacitása|Bájt|Átlag|A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|QueueCount|Igen|Várólista száma|Darabszám|Átlag|A Storage-fiók Queue szolgáltatás várólistájának száma.|Nincsenek méretek|
|QueueMessageCount|Igen|Üzenetsor-üzenetek száma|Darabszám|Átlag|A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma.|Nincsenek méretek|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatáshoz vagy a megadott API-művelethez tartozó sikeres kérések végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|TableCapacity|Igen|Tábla kapacitása|Bájt|Átlag|A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve.|Nincsenek méretek|
|TableCount|Igen|Táblák száma|Darabszám|Átlag|A Storage-fiók Table service található tábla száma.|Nincsenek méretek|
|TableEntityCount|Igen|Tábla entitások száma|Darabszám|Átlag|A Storage-fiók Table serviceban szereplő táblák entitások száma.|Nincsenek méretek|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BlockedCalls|Igen|Blokkolt hívások|Darabszám|Összesen|Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|ApiName, OperationName, régió|
|CharactersTrained|Igen|Betanított karakterek|Darabszám|Összesen|A betanított karakterek teljes száma.|ApiName, OperationName, régió|
|CharactersTranslated|Igen|Lefordított karakterek|Darabszám|Összesen|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|ApiName, OperationName, régió|
|ClientErrors|Igen|Ügyfél-hibák|Darabszám|Összesen|Ügyféloldali hibával rendelkező hívások száma (HTTP-válasz kódja 4xx).|ApiName, OperationName, régió|
|DataIn|Igen|A-ben tárolt adatértékek|Bájt|Összesen|A bejövő adat mérete bájtban megadva.|ApiName, OperationName, régió|
|DataOut|Igen|Kimenő adatvesztés|Bájt|Összesen|A kimenő adat mérete bájtban kifejezve.|ApiName, OperationName, régió|
|Késés|Igen|Késés|Ezredmásodpercben|Átlag|Késés ezredmásodpercben.|ApiName, OperationName, régió|
|ProcessedImages|Igen|Feldolgozott rendszerképek|Darabszám|Összesen| A képfeldolgozáshoz tartozó tranzakciók száma.|ApiName, FeatureName, UsageChannel, régió|
|Kiszolgálóhibái|Igen|Kiszolgálói hibák|Darabszám|Összesen|A szolgáltatás belső hibája miatti hívások száma (HTTP-válasz kódja 5xx).|ApiName, OperationName, régió|
|SpeechSessionDuration|Igen|Beszédfelismerési munkamenet időtartama|Másodperc|Összesen|A beszédfelismerési munkamenet teljes időtartama másodpercben.|ApiName, OperationName, régió|
|SuccessfulCalls|Igen|Sikeres hívások|Darabszám|Összesen|A sikeres hívások száma.|ApiName, OperationName, régió|
|TotalCalls|Igen|Hívások összesen|Darabszám|Összesen|A hívások száma összesen|ApiName, OperationName, régió|
|TotalErrors|Igen|Összes hiba|Darabszám|Összesen|A hibaüzenettel rendelkező hívások teljes száma (HTTP-válasz kódja 4xx vagy 5xx).|ApiName, OperationName, régió|
|TotalTokenCalls|Igen|Jogkivonat-hívások összesen|Darabszám|Összesen|A jogkivonat-hívások teljes száma.|ApiName, OperationName, régió|
|TotalTransactions|Igen|Tranzakciók összesen|Darabszám|Összesen|A tranzakciók száma összesen|Nincsenek méretek|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Igen|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Igen|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez várakozási sorának mélysége|Igen|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|Adatlemez-olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Igen|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez-írási sebesség (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez írási műveletei (művelet/s)|Igen|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Bejövő forgalomfolyamok|Igen|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Igen|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő hálózat|Igen|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|Nincsenek méretek|
|Teljes hálózat|Igen|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Igen|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|Nincsenek méretek|
|Kimenő hálózat összesen|Igen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|
|OS-lemez várakozási sorának mélysége|Igen|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Igen|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez QD|Igen|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez olvasási művelet/mp|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez írási műveletei másodpercenként|Igen|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Igen|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Igen|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|/Lemez QD|Igen|Adatlemez QD (elavult)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|/Lemez olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Igen|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási sebessége (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez írási műveletek másodpercenként|Igen|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Igen|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Igen|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Igen|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Igen|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez várakozási sorának mélysége|Igen|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN, VMName|
|Adatlemez-olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez olvasási műveletei (művelet/s)|Igen|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Adatlemez-írási sebesség (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN, VMName|
|Adatlemez írási műveletei (művelet/s)|Igen|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN, VMName|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|VMName|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|VMName|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|VMName|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|VMName|
|Bejövő forgalomfolyamok|Igen|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Igen|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|VMName|
|Bejövő hálózat|Igen|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|VMName|
|Teljes hálózat|Igen|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|VMName|
|Kimenő hálózat|Igen|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|VMName|
|Kimenő hálózat összesen|Igen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|VMName|
|OS-lemez várakozási sorának mélysége|Igen|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|VMName|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Igen|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|VMName|
|Operációs rendszer/lemez QD|Igen|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez olvasási művelet/mp|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez írási műveletei másodpercenként|Igen|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Igen|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Igen|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|VMName|
|/Lemez QD|Igen|Adatlemez QD (elavult)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|/Lemez olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Igen|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási sebessége (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez írási műveletek másodpercenként|Igen|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Igen|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN, VMName|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Igen|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN, VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|VMName|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. számítás/virtualMachineScaleSets/virtualMachines

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Felhasznált CPU-kreditek|Igen|Felhasznált CPU-kreditek|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|Nincsenek méretek|
|Fennmaradó CPU-kreditek|Igen|Fennmaradó CPU-kreditek|Darabszám|Átlag|A feltört kreditek teljes száma|Nincsenek méretek|
|Adatlemez várakozási sorának mélysége|Igen|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|LUN|
|Adatlemez-olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/mp) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|LUN|
|Adatlemez olvasási műveletei (művelet/s)|Igen|Adatlemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|LUN|
|Adatlemez-írási sebesség (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|LUN|
|Adatlemez írási műveletei (művelet/s)|Igen|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|LUN|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A figyelési időszak során lemezről beolvasott bájtok száma|Nincsenek méretek|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Lemez olvasása IOPS|Nincsenek méretek|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A megfigyelési időszak során lemezre írt bájtok|Nincsenek méretek|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Lemez írása IOPS|Nincsenek méretek|
|Bejövő forgalomfolyamok|Igen|Bejövő forgalomfolyamok|Darabszám|Átlag|A bejövő folyamatok a bejövő irány aktuális folyamatainak száma (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő folyamatok maximális létrehozási aránya|Igen|Bejövő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A bejövő folyamatok maximális létrehozásának aránya (a virtuális gépre irányuló forgalom)|Nincsenek méretek|
|Bejövő hálózat|Igen|Számlázandó hálózat (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott számlázandó bájtok száma (bejövő forgalom) (elavult)|Nincsenek méretek|
|Teljes hálózat|Igen|Teljes hálózat|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren fogadott bájtok száma (bejövő forgalom)|Nincsenek méretek|
|Kimenő hálózat|Igen|Hálózati kimenő számlázandó (elavult)|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren (kimenő forgalom) (elavult) lévő számlázandó bájtok száma|Nincsenek méretek|
|Kimenő hálózat összesen|Igen|Kimenő hálózat összesen|Bájt|Összesen|A virtuális gép (ek) által az összes hálózati adapteren Elküldött bájtok száma (kimenő forgalom)|Nincsenek méretek|
|OS-lemez várakozási sorának mélysége|Igen|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (előzetes verzió)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez olvasási művelete/s|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (előzetes verzió)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|OPERÁCIÓSRENDSZER-lemez írási műveletei/s|Igen|OPERÁCIÓSRENDSZER-lemez írási műveletei/mp (előzetes verzió)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez QD|Igen|OPERÁCIÓSRENDSZER-lemez QD (elavult)|Darabszám|Átlag|OPERÁCIÓSRENDSZER-lemez várólistájának mélysége (vagy várólista hossza)|Nincsenek méretek|
|Operációs rendszer/lemez olvasási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez olvasási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációs rendszer lemezének figyelési időtartama alatt egyetlen lemezről beolvasott bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez olvasási művelet/mp|Igen|OPERÁCIÓSRENDSZER-lemez olvasási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Operációs rendszer/lemez írási sebessége (bájt/s)|Igen|OPERÁCIÓSRENDSZER-lemez írási sebessége (bájt/s) (elavult)|CountPerSecond|Átlag|Az operációsrendszer-lemez figyelési időszaka során egy lemezre írt bájtok másodpercenkénti száma|Nincsenek méretek|
|Operációs rendszer/lemez írási műveletei másodpercenként|Igen|OPERÁCIÓSRENDSZER-lemez írási művelete/mp (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről az operációsrendszer-lemez figyelési időszakában|Nincsenek méretek|
|Kimenő folyamatok|Igen|Kimenő folyamatok|Darabszám|Átlag|A kimenő folyamatok a kimenő irányú aktuális folyamatok száma (a virtuális gépről érkező forgalom)|Nincsenek méretek|
|Kimenő folyamatok maximális létrehozási aránya|Igen|Kimenő folyamatok maximális létrehozási aránya|CountPerSecond|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépről kifelé haladó forgalom)|Nincsenek méretek|
|/Lemez QD|Igen|Adatlemez QD (elavult)|Darabszám|Átlag|Adatlemez várólistájának mélysége (vagy várólista hossza)|SlotId|
|/Lemez olvasási sebesség (bájt/s)|Igen|Adatlemez-olvasási sebesség (bájt/mp) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezről beolvasott bájtok másodpercenkénti száma|SlotId|
|/Lemez olvasási művelet/mp|Igen|Adatlemez olvasási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS beolvasása egyetlen lemezről a figyelési időszakban|SlotId|
|/Lemez írási sebessége (bájt/s)|Igen|Adatlemez-írási sebesség (bájt/s) (elavult)|CountPerSecond|Átlag|A figyelési időszak során egyetlen lemezre írt bájtok másodpercenkénti száma|SlotId|
|/Lemez írási műveletek másodpercenként|Igen|Adatlemez írási műveletei (művelet/mp) (elavult)|CountPerSecond|Átlag|IOPS írása egyetlen lemezről a figyelési időszakban|SlotId|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|Nincsenek méretek|
|Prémium szintű adatlemez-gyorsítótár – olvasási találat|Igen|Prémium szintű adatlemez-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási találat|LUN|
|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|Igen|Prémium szintű adatlemez-gyorsítótár olvasási lemaradása (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez-gyorsítótár – olvasási kihagyás|LUN|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási találata|Nincsenek méretek|
|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Igen|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása (előzetes verzió)|Százalék|Átlag|Prémium operációsrendszer-lemez gyorsítótárának olvasási kihagyása|Nincsenek méretek|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CpuUsage|Igen|CPU-használat|Darabszám|Átlag|CPU-használat a millicores összes magot illetően.|containerName|
|MemoryUsage|Igen|Memóriahasználat|Bájt|Átlag|A memória teljes kihasználtsága bájtban.|containerName|
|NetworkBytesReceivedPerSecond|Igen|Másodpercenként fogadott hálózati bájtok száma|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok száma.|Nincsenek méretek|
|NetworkBytesTransmittedPerSecond|Igen|Másodpercenként továbbított hálózati bájtok száma|Bájt|Átlag|A másodpercenként továbbított hálózati bájtok száma.|Nincsenek méretek|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Igen|AgentPool CPU-idő|Másodperc|Összesen|AgentPool CPU-idő másodpercben|Nincsenek méretek|
|RunDuration|Igen|Futtatás időtartama|Ezredmásodpercben|Összesen|Futtatás időtartama (ezredmásodpercben)|Nincsenek méretek|
|SuccessfulPullCount|Igen|Sikeres lekérések száma|Darabszám|Átlag|Sikeres Képkeresések száma|Nincsenek méretek|
|SuccessfulPushCount|Igen|Sikeres leküldések száma|Darabszám|Átlag|Sikeres leküldések száma|Nincsenek méretek|
|TotalPullCount|Igen|Lekérések száma összesen|Darabszám|Átlag|A lekérések száma összesen|Nincsenek méretek|
|TotalPushCount|Igen|Leküldések száma összesen|Darabszám|Átlag|Leküldések száma összesen|Nincsenek méretek|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Nem|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Darabszám|Átlag|A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma|Nincsenek méretek|
|kube_node_status_allocatable_memory_bytes|Nem|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Átlag|A felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Nincsenek méretek|
|kube_node_status_condition|Nem|Különböző csomóponti feltételek állapota|Darabszám|Átlag|Különböző csomóponti feltételek állapota|feltétel, állapot, status2, csomópont|
|kube_pod_status_phase|Nem|Hüvelyek száma fázis szerint|Darabszám|Átlag|Hüvelyek száma fázis szerint|fázis, névtér, Pod|
|kube_pod_status_ready|Nem|A hüvelyek száma üzemkész állapotban|Darabszám|Átlag|A hüvelyek száma üzemkész állapotban|névtér, Pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRequests|Igen|Sikertelen kérelmek|Darabszám|Összesen|A rendelkezésre álló naplók beolvasása egyéni erőforrás-szolgáltatók számára|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Igen|Sikeres kérelmek|Darabszám|Összesen|Az egyéni szolgáltató által kezdeményezett sikeres kérések|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Availablecapacity;)|Igen|Rendelkezésre álló kapacitás|Bájt|Átlag|A rendelkezésre álló kapacitás bájtban a jelentési időszak alatt.|Nincsenek méretek|
|BytesUploadedToCloud|Igen|Feltöltött Felhőbeli bájtok (eszköz)|Bájt|Átlag|Az Azure-ba az eszközről a jelentési időszakban feltöltött bájtok teljes száma.|Nincsenek méretek|
|BytesUploadedToCloudPerShare|Igen|Felhőbeli feltöltött bájtok (megosztás)|Bájt|Átlag|A jelentési időszak során az Azure-ba feltöltött bájtok teljes száma.|Megosztás|
|CloudReadThroughput|Igen|Felhőbeli letöltési sebesség|BytesPerSecond|Átlag|A felhő letöltési sebessége az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudReadThroughputPerShare|Igen|Felhőbeli letöltési teljesítmény (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való letöltési átviteli sebesség a jelentési időszak alatt.|Megosztás|
|CloudUploadThroughput|Igen|Felhőbeli feltöltési sebesség|BytesPerSecond|Átlag|A Felhőbeli feltöltési sebesség az Azure-ba a jelentési időszak alatt.|Nincsenek méretek|
|CloudUploadThroughputPerShare|Igen|Felhőbeli feltöltési sebesség (megosztás)|BytesPerSecond|Átlag|Az Azure-ba való feltöltési átviteli sebesség a jelentési időszak során.|Megosztás|
|HyperVMemoryUtilization|Igen|Edge-számítás – memóriahasználat|Százalék|Átlag|Használatban lévő RAM mennyisége|InstanceName|
|HyperVVirtualProcessorUtilization|Igen|Edge-számítás – százalékos CPU|Százalék|Átlag|CPU-használat százalékos aránya|InstanceName|
|NICReadThroughput|Igen|Olvasási sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter olvasási átviteli sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|NICWriteThroughput|Igen|Írási átviteli sebesség (hálózat)|BytesPerSecond|Átlag|Az eszközön lévő hálózati adapter írási sebessége az átjáró összes kötetének jelentési időszakában.|InstanceName|
|TotalCapacity|Igen|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|Nincsenek méretek|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|FailedRuns|Igen|Sikertelen futtatások|Darabszám|Összesen||pipelineName, activityName|
|SuccessfulRuns|Igen|Sikeres futtatások|Darabszám|Összesen||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Igen|A megszakított tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivityFailedRuns|Igen|Sikertelen tevékenység-futtatási metrikák|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|ActivitySucceededRuns|Igen|A sikeres tevékenység metrikákat futtat|Darabszám|Összesen||ActivityType, PipelineName, FailureType, név|
|FactorySizeInGbUnits|Igen|Gyári méret összesen (GB egység)|Darabszám|Maximum||Nincsenek méretek|
|IntegrationRuntimeAvailableMemory|Igen|Az Integration Runtime rendelkezésre álló memóriája|Bájt|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeAvailableNodeNumber|Igen|Integrációs modul elérhető csomópontok száma|Darabszám|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Igen|Integration Runtime-várólista időtartama|Másodperc|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Igen|Integration Runtime CPU-kihasználtsága|Százalék|Átlag||IntegrationRuntimeName, csomópontnév|
|IntegrationRuntimeQueueLength|Igen|Integration Runtime-várólista hossza|Darabszám|Átlag||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Igen|Maximálisan engedélyezett gyári méret (GB egység)|Darabszám|Maximum||Nincsenek méretek|
|MaxAllowedResourceCount|Igen|Engedélyezett entitások maximális száma|Darabszám|Maximum||Nincsenek méretek|
|PipelineCancelledRuns|Igen|Megszakított folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineFailedRuns|Igen|Sikertelen folyamat-futtatási metrikák|Darabszám|Összesen||FailureType, név|
|PipelineSucceededRuns|Igen|A folyamat sikeresen futtatja a metrikákat|Darabszám|Összesen||FailureType, név|
|ResourceCount|Igen|Entitások száma összesen|Darabszám|Maximum||Nincsenek méretek|
|TriggerCancelledRuns|Igen|Megszakított trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerFailedRuns|Igen|Sikertelen trigger-futtatási metrikák|Darabszám|Összesen||Név, FailureType|
|TriggerSucceededRuns|Igen|A sikeres trigger metrikákat futtat|Darabszám|Összesen||Név, FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DataRead|Igen|Olvasott információk|Bájt|Összesen|A fiókból beolvasott adatok teljes mennyisége.|Nincsenek méretek|
|DataWritten|Igen|Írt adatértékek|Bájt|Összesen|A fiókba írt összes adatmennyiség.|Nincsenek méretek|
|ReadRequests|Igen|Olvasási kérelmek|Darabszám|Összesen|A fióknak küldött adatolvasási kérelmek száma.|Nincsenek méretek|
|TotalStorage|Igen|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatmennyiség teljes mennyisége.|Nincsenek méretek|
|WriteRequests|Igen|Írási kérelmek|Darabszám|Összesen|A fiókra vonatkozó adatírási kérelmek száma.|Nincsenek méretek|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Igen|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Igen|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|seconds_behind_master|Igen|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Igen|Kiszolgáló naplójának tárolási korlátja|Bájt|Átlag|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Igen|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Igen|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Igen|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Igen|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Igen|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|seconds_behind_master|Igen|Replikálás késése másodpercben|Darabszám|Maximum|Replikálás késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Igen|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Igen|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Igen|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Igen|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|backup_storage_used|Igen|Felhasznált biztonsági mentési tár|Bájt|Átlag|Felhasznált biztonsági mentési tár|Nincsenek méretek|
|connections_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|io_consumption_percent|Igen|IO-százalék|Százalék|Átlag|IO-százalék|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|pg_replica_log_delay_in_bytes|Igen|Replikák maximális késése|Bájt|Maximum|A legkésleltetett replika bájtban kifejezett késése|Nincsenek méretek|
|pg_replica_log_delay_in_seconds|Igen|Replika késése|Másodperc|Maximum|Replika késése másodpercben|Nincsenek méretek|
|serverlog_storage_limit|Igen|Kiszolgáló naplójának tárolási korlátja|Bájt|Maximum|Kiszolgáló naplójának tárolási korlátja|Nincsenek méretek|
|serverlog_storage_percent|Igen|Kiszolgáló naplójának tárolási százaléka|Százalék|Átlag|Kiszolgáló naplójának tárolási százaléka|Nincsenek méretek|
|serverlog_storage_usage|Igen|Kiszolgáló naplójának tárolója|Bájt|Átlag|Kiszolgáló naplójának tárolója|Nincsenek méretek|
|storage_limit|Igen|Tárolási korlát|Bájt|Maximum|Tárolási korlát|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|IOPS|Igen|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_connections|Igen|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Nincsenek méretek|
|connections_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|connections_succeeded|Igen|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_percent|Igen|CPU-százalék|Százalék|Átlag|CPU-százalék|Nincsenek méretek|
|IOPS|Igen|IOPS|Darabszám|Átlag|I/o-műveletek másodpercenként|Nincsenek méretek|
|maximum_used_transactionIDs|Igen|Maximálisan használt tranzakciós azonosítók|Darabszám|Átlag|Maximálisan használt tranzakciós azonosítók|Nincsenek méretek|
|memory_percent|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Nincsenek méretek|
|network_bytes_egress|Igen|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|network_bytes_ingress|Igen|Bejövő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|Nincsenek méretek|
|storage_percent|Igen|Tárolási százalék|Százalék|Átlag|Tárolási százalék|Nincsenek méretek|
|storage_used|Igen|Felhasznált tárterület|Bájt|Átlag|Felhasznált tárterület|Nincsenek méretek|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. commands. kimenő. elhagyása. sikeres|Igen|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. Complete. success|Igen|C2D-üzenetek kézbesítésének befejezése|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|Nincsenek méretek|
|C2D. commands. kimenő. elutasítás. sikeres|Igen|C2D-üzenetek elutasítva|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|Nincsenek méretek|
|C2D. Methods. failure|Igen|Sikertelen közvetlen metódusok meghívása|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|Nincsenek méretek|
|C2D. Methods. requestSize|Igen|A közvetlen metódus meghívásának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus-kérelem átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. responseSize|Igen|A közvetlen metódus-meghívások válaszának mérete|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Methods. success|Igen|Közvetlen metódusok sikeres meghívása|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|Nincsenek méretek|
|C2D. Twin. Read. failure|Igen|Sikertelen dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Read. size|Igen|Dupla olvasások válaszának mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|C2D. Twin. Read. success|Igen|Sikeres dupla olvasások a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|C2D. Twin. Update. failure|Igen|Sikertelen dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2D. Twin. Update. size|Igen|Dupla frissítések mérete a háttérből|Bájt|Átlag|Az összes sikeres háttér-kezdeményező dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|C2D. Twin. Update. success|Igen|Sikeres dupla frissítések a háttérből|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|C2DMessagesExpired|Igen|C2D-üzenetek lejárt (előzetes verzió)|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|Nincsenek méretek|
|konfigurációk|Igen|Konfigurációs mérőszámok|Darabszám|Összesen|A konfigurációs műveletek metrikái|Nincsenek méretek|
|connectedDeviceCount|Nem|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. endpoints. kimenő. beépített. események|Igen|Útválasztás: üzenetek/események küldésére küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a beépített végpontnak (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. kimenő. eventHubs|Igen|Útválasztás: az Event hub számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket az Event hub-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusQueues|Igen|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. serviceBusTopics|Igen|Útválasztás: Service Bus témakörbe küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage|Igen|Útválasztás: a tárolóba küldött üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Blobok|Igen|Útválasztás: tárolóba szállított Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincsenek méretek|
|D2C. endpoints. kimenő. Storage. Bytes|Igen|Útválasztás: a tárolóba szállított adatmennyiség|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincsenek méretek|
|D2C. endpoints. késleltetés. beépített. események|Igen|Útválasztás: üzenetek/események üzenetének késése|Ezredmásodpercben|Átlag|A beérkező üzenetek átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria üzenetet a beépített végpontba (üzenetek/események).|Nincsenek méretek|
|D2C. endpoints. késleltetés. eventHubs|Igen|Útválasztás: az Event hub üzenetének késése|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub-végpontba.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusQueues|Igen|Útválasztás: Service Bus üzenetsor üzenet-késése|Ezredmásodpercben|Átlag|Egy Service Bus üzenetsor-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. késleltetés. serviceBusTopics|Igen|Útválasztás: Service Bus témakör üzenetének késése|Ezredmásodpercben|Átlag|Egy Service Bus témakör-végpontba beérkező üzenetek közötti átlagos késés (ezredmásodpercben) IoT Hub és telemetria.|Nincsenek méretek|
|D2C. endpoints. látencia. Storage|Igen|Útválasztás: üzenetek késése a tároláshoz|Ezredmásodpercben|Átlag|A IoT Hub és telemetria üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincsenek méretek|
|D2C. telemetria. kimenő. eldobott|Igen|Útválasztás: telemetria üzenetek elvetve |Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincsenek méretek|
|D2C. telemetria. kimenő. tartalék|Igen|Útválasztás: tartalékként továbbított üzenetek|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincsenek méretek|
|D2C. telemetria. kimenő. érvénytelen|Igen|Útválasztás: Inkompatibilis telemetria-üzenetek|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincsenek méretek|
|D2C. telemetria. kimenő. árva|Igen|Útválasztás: árva telemetria üzenetek |Darabszám|Összesen|A IoT Hub-útválasztás által elárvult üzenetek száma, mert nem feleltek meg az útválasztási szabályoknak (beleértve a tartalék szabályt is). |Nincsenek méretek|
|D2C. telemetria. kimenő. sikeres|Igen|Útválasztás: telemetria üzenetek kézbesítése|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincsenek méretek|
|D2C. telemetria. beáramló. allProtocol|Igen|Telemetria üzenetek küldése|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sendThrottle|Igen|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincsenek méretek|
|D2C. telemetria. beáramló. sikeres|Igen|Telemetria üzenetek elküldése|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincsenek méretek|
|D2C. Twin. Read. failure|Igen|Sikertelen dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincsenek méretek|
|D2C. Twin. Read. size|Igen|Az eszközökről érkező kettős olvasások válaszának mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás átlagos, minimális és maximális száma.|Nincsenek méretek|
|D2C. Twin. Read. success|Igen|Sikeres dupla olvasások az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincsenek méretek|
|D2C. Twin. Update. failure|Igen|Sikertelen dupla frissítések az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincsenek méretek|
|D2C. Twin. Update. size|Igen|Az eszközökből származó kettős frissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés átlagos, minimális és maximális mérete.|Nincsenek méretek|
|D2C. Twin. Update. success|Igen|Sikeres dupla frissítések az eszközökről|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincsenek méretek|
|dailyMessageQuotaUsed|Igen|A felhasznált üzenetek teljes száma|Darabszám|Maximum|A jelenleg használt összes üzenet száma|Nincsenek méretek|
|deviceDataUsage|Igen|Az eszköz összes adatfelhasználása|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|deviceDataUsageV2|Igen|Az eszköz összes adatfelhasználása (előzetes verzió)|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincsenek méretek|
|Devices. connectedDevices. allProtocol|Igen|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincsenek méretek|
|Devices. totalDevices|Igen|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|EventGridDeliveries|Igen|Event Grid kézbesítések (előzetes verzió)|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát ( https://aka.ms/ioteventgrid) .|Eredmény, EventType|
|EventGridLatency|Igen|Event Grid késés (előzetes verzió)|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType|
|feladatok. cancelJob. hiba|Igen|Sikertelen feladatok megszakítása|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|Nincsenek méretek|
|feladatok. cancelJob. sikeres|Igen|Sikeres feladatok törlése|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. kész|Igen|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. hiba|Igen|Nem sikerült létrehozni a metódus Meghívási feladatait|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createDirectMethodJob. sikeres|Igen|Metódus-Meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. hiba|Igen|A kettős frissítési feladatok sikertelen létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincsenek méretek|
|feladatok. createTwinUpdateJob. sikeres|Igen|A kettős frissítési feladatok sikeres létrehozása|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincsenek méretek|
|feladatok. sikertelen|Igen|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincsenek méretek|
|feladatok. listJobs. hiba|Igen|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|Nincsenek méretek|
|feladatok. listJobs. sikeres|Igen|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|Nincsenek méretek|
|feladatok. queryJobs. hiba|Igen|Sikertelen feladatok lekérdezése|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|Nincsenek méretek|
|feladatok. queryJobs. sikeres|Igen|Sikeres feladatok lekérdezése|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincsenek méretek|
|RoutingDataSizeInBytesDelivered|Igen|Továbbítási üzenetek mérete bájtban (előzetes verzió)|Bájt|Összesen|Az IoT hub által egy végpontra küldött üzenetek teljes mérete bájtban megadva. A Végpontneve és a EndpointType dimenzió használatával megtekintheti az üzenetek méretét a különböző végpontokra küldött bájtokban. A metrika értéke minden leküldött üzenetnél növekszik, beleértve azt is, hogy az üzenetet több végpontra is továbbítják-e, vagy ha az üzenet többször is ugyanarra a végpontra érkezik.|EndpointType, Végpontneve, RoutingSource|
|RoutingDeliveries|Igen|Útválasztási kézbesítések (előzetes verzió)|Darabszám|Összesen|Azon időpontok száma, IoT Hub az összes végpont felé irányuló üzenetek továbbítására történt kísérlet az Útválasztás használatával. A sikeres vagy sikertelen kísérletek számának megtekintéséhez használja az eredmény dimenziót. Ha szeretné megtekinteni a hiba okát (például érvénytelen, eldobott vagy árva), használja az FailureReasonCategory dimenziót. A Végpontneve és a EndpointType dimenzióval is megtudhatja, hogy hány üzenet lett továbbítva a különböző végpontoknak. A metrika értéke eggyel nő az egyes kézbesítési kísérleteknél, beleértve azt is, hogy az üzenetet több végpontra kézbesítik-e, vagy ha az üzenet többször is ugyanarra a végpontra van kézbesítve.|EndpointType, Végpontneve, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Igen|Útválasztási kézbesítés késése (előzetes verzió)|Ezredmásodpercben|Átlag|Az üzenet bejövő értékének átlagos késése (ezredmásodpercben), hogy IoT Hub és telemetria az üzeneteket a végpontba. A Végpontneve és a EndpointType méretek segítségével megismerheti a különböző végpontok késését.|EndpointType, Végpontneve, RoutingSource|
|totalDeviceCount|Nem|Összes eszköz (előzetes verzió)|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincsenek méretek|
|twinQueries. hiba|Igen|Sikertelen dupla lekérdezések|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|Nincsenek méretek|
|twinQueries.resultSize|Igen|Dupla lekérdezések eredményének mérete|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének átlagos, minimális és maximális mérete.|Nincsenek méretek|
|twinQueries. success|Igen|Sikeres Twin-lekérdezések|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|Nincsenek méretek|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AttestationAttempts|Igen|Igazolási kísérletek|Darabszám|Összesen|Az eszköz által megkísérelt tanúsítványok száma|ProvisioningServiceName, állapot, protokoll|
|DeviceAssignments|Igen|Hozzárendelt eszközök|Darabszám|Összesen|Az IoT hubhoz rendelt eszközök száma|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Igen|Regisztrációs kísérletek|Darabszám|Összesen|A megkísérelt eszközök regisztrációinak száma|ProvisioningServiceName, IotHubName, állapot|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AddRegion|Igen|Régió hozzáadva|Darabszám|Darabszám|Régió hozzáadva|Régió|
|AutoscaleMaxThroughput|Nem|Maximális átviteli sebesség|Darabszám|Maximum|Maximális átviteli sebesség|DatabaseName, CollectionName|
|AvailableStorage|Nem|elavult Rendelkezésre álló tár|Bájt|Összesen|A "rendelkezésre álló tár" el lesz távolítva a Azure Monitorról a 2020 szeptember végén. Cosmos DB a gyűjtemény tárterületének mérete mostantól korlátlan. Az egyetlen korlátozás az, hogy az egyes logikai partíciós kulcsok tárolási mérete 20 GB. A PartitionKeyStatistics a diagnosztikai naplóban engedélyezheti a felső partíciós kulcsok tárolási felhasználásának megismeréséhez. A Cosmos DB tárolási kvótával kapcsolatos további információkért tekintse meg ezt a dokumentációt https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Az elavulás után a fennmaradó riasztási szabályok az elavult metrika esetében még mindig le lesznek tiltva az elavult dátum után.|CollectionName, DatabaseName, régió|
|CassandraConnectionClosures|Nem|Cassandra-kapcsolatok bezárása|Darabszám|Összesen|A lezárt Cassandra-kapcsolatok száma 1 perces részletességgel|Régió, ClosureReason|
|CassandraConnectorAvgReplicationLatency|Nem|Cassandra-összekötő átlagos ReplicationLatency|Ezredmásodpercben|Átlag|Cassandra-összekötő átlagos ReplicationLatency|Nincsenek méretek|
|CassandraConnectorReplicationHealthStatus|Nem|Cassandra Connector replikációs állapotának állapota|Darabszám|Darabszám|Cassandra Connector replikációs állapotának állapota|NotStarted, ReplicationInProgress, hiba|
|CassandraKeyspaceCreate|Nem|Cassandra-terület létrehozva|Darabszám|Darabszám|Cassandra-terület létrehozva|ResourceName |
|CassandraKeyspaceDelete|Nem|Cassandra szóköz törölve|Darabszám|Darabszám|Cassandra szóköz törölve|ResourceName |
|CassandraKeyspaceThroughputUpdate|Nem|Cassandra Space-átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Space-átviteli sebesség frissítve|ResourceName |
|CassandraKeyspaceUpdate|Nem|Cassandra Space frissítve|Darabszám|Darabszám|Cassandra Space frissítve|ResourceName |
|CassandraRequestCharges|Nem|Cassandra-kérelmek díjai|Darabszám|Összesen|A Cassandra-kérelmekhez felhasznált RUs|DatabaseName, CollectionName, régió, OperationType, ResourceType|
|CassandraRequests|Nem|Cassandra-kérelmek|Darabszám|Darabszám|A végrehajtott Cassandra-kérelmek száma|DatabaseName, CollectionName, régió, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|Nem|Cassandra-tábla létrehozva|Darabszám|Darabszám|Cassandra-tábla létrehozva|ResourceName, ChildResourceName, |
|CassandraTableDelete|Nem|Cassandra-tábla törölve|Darabszám|Darabszám|Cassandra-tábla törölve|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|Nem|Cassandra Table átviteli sebesség frissítve|Darabszám|Darabszám|Cassandra Table átviteli sebesség frissítve|ResourceName, ChildResourceName, |
|CassandraTableUpdate|Nem|Cassandra-tábla frissítve|Darabszám|Darabszám|Cassandra-tábla frissítve|ResourceName, ChildResourceName, |
|CreateAccount|Igen|Fiók létrehozva|Darabszám|Darabszám|Fiók létrehozva|Nincsenek méretek|
|DataUsage|Nem|Adathasználat|Bájt|Összesen|Összesen 5 perces részletességgel jelentett adatok teljes kihasználtsága|CollectionName, DatabaseName, régió|
|DeleteAccount|Igen|Fiók törölve|Darabszám|Darabszám|Fiók törölve|Nincsenek méretek|
|DocumentCount|Nem|Dokumentumok száma|Darabszám|Összesen|5 perces részletességgel jelentett dokumentumok száma összesen|CollectionName, DatabaseName, régió|
|DocumentQuota|Nem|Dokumentum kvótája|Bájt|Összesen|Összesen 5 perces részletességgel jelentett tárolási kvóta|CollectionName, DatabaseName, régió|
|GremlinDatabaseCreate|Nem|Gremlin-adatbázis létrehozva|Darabszám|Darabszám|Gremlin-adatbázis létrehozva|ResourceName |
|GremlinDatabaseDelete|Nem|Gremlin-adatbázis törölve|Darabszám|Darabszám|Gremlin-adatbázis törölve|ResourceName |
|GremlinDatabaseThroughputUpdate|Nem|Gremlin adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin adatbázis átviteli sebessége frissítve|ResourceName |
|GremlinDatabaseUpdate|Nem|Gremlin-adatbázis frissítve|Darabszám|Darabszám|Gremlin-adatbázis frissítve|ResourceName |
|GremlinGraphCreate|Nem|Gremlin gráf létrehozva|Darabszám|Darabszám|Gremlin gráf létrehozva|ResourceName, ChildResourceName, |
|GremlinGraphDelete|Nem|Gremlin gráf törölve|Darabszám|Darabszám|Gremlin gráf törölve|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|Nem|Gremlin gráf átviteli sebessége frissítve|Darabszám|Darabszám|Gremlin gráf átviteli sebessége frissítve|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|Nem|Gremlin gráf frissítve|Darabszám|Darabszám|Gremlin gráf frissítve|ResourceName, ChildResourceName, |
|IndexUsage|Nem|Indexhasználat|Bájt|Összesen|Az indexelés teljes használata 5 perces részletességgel jelentett|CollectionName, DatabaseName, régió|
|MetadataRequests|Nem|Metaadat-kérelmek|Darabszám|Darabszám|A metaadat-kérelmek száma. Cosmos DB az egyes fiókok rendszermetaadat-gyűjteményét kezeli, amely lehetővé teszi a gyűjtemények, adatbázisok stb. és azok konfigurációinak számbavételét díjmentesen.|DatabaseName, CollectionName, régió, StatusCode, |
|MongoCollectionCreate|Nem|Mongo-gyűjtemény létrehozva|Darabszám|Darabszám|Mongo-gyűjtemény létrehozva|ResourceName, ChildResourceName, |
|MongoCollectionDelete|Nem|Mongo-gyűjtemény törölve|Darabszám|Darabszám|Mongo-gyűjtemény törölve|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|Nem|A Mongo-gyűjtési átviteli sebesség frissítve|Darabszám|Darabszám|A Mongo-gyűjtési átviteli sebesség frissítve|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|Nem|Mongo-gyűjtemény frissítve|Darabszám|Darabszám|Mongo-gyűjtemény frissítve|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|Nem|Mongo-adatbázis törölve|Darabszám|Darabszám|Mongo-adatbázis törölve|ResourceName |
|MongoDatabaseThroughputUpdate|Nem|Mongo adatbázis átviteli sebessége frissítve|Darabszám|Darabszám|Mongo adatbázis átviteli sebessége frissítve|ResourceName |
|MongoDBDatabaseCreate|Nem|Mongo-adatbázis létrehozva|Darabszám|Darabszám|Mongo-adatbázis létrehozva|ResourceName |
|MongoDBDatabaseUpdate|Nem|Mongo-adatbázis frissítve|Darabszám|Darabszám|Mongo-adatbázis frissítve|ResourceName |
|MongoRequestCharge|Igen|Mongo-kérelem díja|Darabszám|Összesen|Felhasznált Mongo-kérelmek egységei|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequests|Igen|Mongo kérelmek|Darabszám|Darabszám|A Mongo kérelmek száma|DatabaseName, CollectionName, régió, CommandName, ErrorCode, status|
|MongoRequestsCount|Nem|Mongo kérelmek gyakorisága|CountPerSecond|Átlag|Mongo kérelmek száma másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsDelete|Nem|Mongo-törlési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-törlési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsInsert|Nem|Mongo-beszúrási kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-beszúrási szám másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsQuery|Nem|Mongo-lekérdezési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo-lekérdezési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|MongoRequestsUpdate|Nem|Mongo-frissítési kérelmek gyakorisága|CountPerSecond|Átlag|Mongo frissítési kérelem másodpercenként|DatabaseName, CollectionName, régió, ErrorCode|
|NormalizedRUConsumption|Nem|Normalizált RU-felhasználás|Százalék|Maximum|Maximális RU-százalék/perc|CollectionName, DatabaseName, régió, PartitionKeyRangeId|
|ProvisionedThroughput|Nem|Kiosztott átviteli sebesség|Darabszám|Maximum|Kiosztott átviteli sebesség|DatabaseName, CollectionName|
|RegionFailover|Igen|A régió feladatátvétele megtörtént|Darabszám|Darabszám|A régió feladatátvétele megtörtént|Nincsenek méretek|
|RemoveRegion|Igen|Régió eltávolítva|Darabszám|Darabszám|Régió eltávolítva|Régió|
|ReplicationLatency|Igen|P99 replikáció késése|Ezredmásodpercben|Átlag|P99 replikációs késés a forrás-és a célcsoportok között a Geo-kompatibilis fiókhoz|SourceRegion, TargetRegion|
|ServerSideLatency|Nem|Kiszolgálóoldali késés|Ezredmásodpercben|Átlag|Kiszolgálóoldali késés|DatabaseName, CollectionName, régió, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Nem|Szolgáltatás rendelkezésre állása|Százalék|Átlag|A fiók a rendelkezésre állást egy óra, nap vagy hónap részletességgel kéri|Nincsenek méretek|
|SqlContainerCreate|Nem|SQL-tároló létrehozva|Darabszám|Darabszám|SQL-tároló létrehozva|ResourceName, ChildResourceName, |
|SqlContainerDelete|Nem|SQL-tároló törölve|Darabszám|Darabszám|SQL-tároló törölve|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|Nem|SQL-tároló átviteli sebessége frissítve|Darabszám|Darabszám|SQL-tároló átviteli sebessége frissítve|ResourceName, ChildResourceName, |
|SqlContainerUpdate|Nem|SQL-tároló frissítve|Darabszám|Darabszám|SQL-tároló frissítve|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|Nem|SQL-adatbázis létrehozva|Darabszám|Darabszám|SQL-adatbázis létrehozva|ResourceName |
|SqlDatabaseDelete|Nem|SQL-adatbázis törölve|Darabszám|Darabszám|SQL-adatbázis törölve|ResourceName |
|SqlDatabaseThroughputUpdate|Nem|Az SQL Database átviteli sebessége frissítve|Darabszám|Darabszám|Az SQL Database átviteli sebessége frissítve|ResourceName |
|SqlDatabaseUpdate|Nem|SQL-adatbázis frissítve|Darabszám|Darabszám|SQL-adatbázis frissítve|ResourceName |
|TableTableCreate|Nem|AzureTable tábla létrehozva|Darabszám|Darabszám|AzureTable tábla létrehozva|ResourceName |
|TableTableDelete|Nem|AzureTable-tábla törölve|Darabszám|Darabszám|AzureTable-tábla törölve|ResourceName |
|TableTableThroughputUpdate|Nem|AzureTable-tábla átviteli sebessége frissítve|Darabszám|Darabszám|AzureTable-tábla átviteli sebessége frissítve|ResourceName |
|TableTableUpdate|Nem|AzureTable tábla frissítve|Darabszám|Darabszám|AzureTable tábla frissítve|ResourceName |
|TotalRequests|Igen|Összes kérelem|Darabszám|Darabszám|Benyújtott kérelmek száma|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|TotalRequestUnits|Igen|Kérelmek összes egysége|Darabszám|Összesen|Felhasználható kérési egységek|DatabaseName, CollectionName, régió, StatusCode, OperationType, status|
|UpdateAccountKeys|Igen|A fiók kulcsai frissítve|Darabszám|Darabszám|A fiók kulcsai frissítve|KeyType|
|UpdateAccountNetworkSettings|Igen|A fiók hálózati beállításai frissítve|Darabszám|Darabszám|A fiók hálózati beállításai frissítve|Nincsenek méretek|
|UpdateAccountReplicationSettings|Igen|A fiók replikációs beállításainak frissítése megtörtént|Darabszám|Darabszám|A fiók replikációs beállításainak frissítése megtörtént|Nincsenek méretek|
|UpdateDiagnosticsSettings|Nem|Fiók diagnosztikai beállításainak frissítése|Darabszám|Darabszám|Fiók diagnosztikai beállításainak frissítése|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Nincsenek méretek|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincsenek méretek|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincsenek méretek|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/fürtök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|Nem|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|Nincsenek méretek|
|AvailableMemory|Nem|Szabad memória|Százalék|Maximum|Az Event hub-fürt rendelkezésre álló memóriája a teljes memória százalékában.|Szerepkör|
|CaptureBacklog|Nem|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|Nincsenek méretek|
|CapturedBytes|Nem|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|Nincsenek méretek|
|CapturedMessages|Nem|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|Nincsenek méretek|
|ConnectionsClosed|Nem|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|Nincsenek méretek|
|ConnectionsOpened|Nem|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|Nincsenek méretek|
|CPU|Nem|CPU|Százalék|Maximum|Az Event hub-fürt CPU-kihasználtsága százalékként|Szerepkör|
|IncomingBytes|Igen|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|Nincsenek méretek|
|IncomingMessages|Igen|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|Nincsenek méretek|
|IncomingRequests|Igen|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|Nincsenek méretek|
|OutgoingBytes|Igen|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|Nincsenek méretek|
|OutgoingMessages|Igen|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|Nincsenek méretek|
|QuotaExceededErrors|Nem|Kvótatúllépési hibák.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|Nincsenek méretek|
|Kiszolgálóhibái|Nem|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|Nincsenek méretek|
|Méret|Nem|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|Szerepkör|
|SuccessfulRequests|Nem|Sikeres kérelmek|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|Nincsenek méretek|
|ThrottledRequests|Nem|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|Nincsenek méretek|
|UserErrors|Nem|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|Nincsenek méretek|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|Nem|Aktív kapcsolatok|Darabszám|Átlag|A Microsoft. EventHub összes aktív kapcsolata.|Nincsenek méretek|
|CaptureBacklog|Nem|Várakozó fájlok rögzítése.|Darabszám|Összesen|A Microsoft. EventHub várakozó fájlok rögzítése.|EntityName|
|CapturedBytes|Nem|Rögzített bájtok száma.|Bájt|Összesen|A Microsoft. EventHub rögzített bájtok száma.|EntityName|
|CapturedMessages|Nem|Rögzített üzenetek.|Darabszám|Összesen|A Microsoft. EventHub rögzített üzenetei.|EntityName|
|ConnectionsClosed|Nem|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub lezárt kapcsolatainak száma.|EntityName|
|ConnectionsOpened|Nem|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. EventHub megnyitott kapcsolatainak száma.|EntityName|
|EHABL|Igen|Archivált várakozó üzenetek (elavult)|Darabszám|Összesen|Az Event hub archiválási üzenetei a várakozó névtérben (elavult)|Nincsenek méretek|
|EHAMBS|Igen|Archivált üzenetek átviteli sebessége (elavult)|Bájt|Összesen|Event hub archivált üzenetek átviteli sebessége egy névtérben (elavult)|Nincsenek méretek|
|EHAMSGS|Igen|Archivált üzenetek (elavult)|Darabszám|Összesen|Event hub archivált üzenetek egy névtérben (elavult)|Nincsenek méretek|
|EHINBYTES|Igen|Bejövő bájtok (elavult)|Bájt|Összesen|Az Event hub bejövő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincsenek méretek|
|EHINMBS|Igen|Bejövő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub bejövő üzeneteinek átviteli sebessége egy névtér esetében. Ez a metrika elavult. Ehelyett a bejövő bájtok metrikáját használja (elavult)|Nincsenek méretek|
|EHINMSGS|Igen|Bejövő üzenetek (elavult)|Darabszám|Összesen|Névtér összes bejövő üzenete (elavult)|Nincsenek méretek|
|EHOUTBYTES|Igen|Kimenő bájtok (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél (elavult)|Nincsenek méretek|
|EHOUTMBS|Igen|Kimenő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event hub kimenő üzenetének átviteli sebessége egy névtérnél. Ez a metrika elavult. Ehelyett használja a kimenő bájtok metrikáját (elavult)|Nincsenek méretek|
|EHOUTMSGS|Igen|Kimenő üzenetek (elavult)|Darabszám|Összesen|Névtér összes kimenő üzenete (elavult)|Nincsenek méretek|
|FAILREQ|Igen|Sikertelen kérelmek (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)|Nincsenek méretek|
|IncomingBytes|Igen|Bejövő bájtok.|Bájt|Összesen|A Microsoft. EventHub bejövő bájtjai.|EntityName|
|IncomingMessages|Igen|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. EventHub bejövő üzenetei.|EntityName|
|IncomingRequests|Igen|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. EventHub bejövő kérései.|EntityName|
|INMSGS|Igen|Bejövő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes bejövő üzenete. Ez a metrika elavult. Ehelyett használja a bejövő üzenetek metrikáját (elavult)|Nincsenek méretek|
|INREQS|Igen|Bejövő kérelmek (elavult)|Darabszám|Összesen|Egy névtér összes bejövő küldési kérelme (elavult)|Nincsenek méretek|
|INTERer|Igen|Belső kiszolgálói hibák (elavult)|Darabszám|Összesen|Névtér összes belső kiszolgálóhiba (elavult)|Nincsenek méretek|
|MISCERR|Igen|Egyéb hibák (elavult)|Darabszám|Összesen|Névtér összes sikertelen kérelme (elavult)|Nincsenek méretek|
|OutgoingBytes|Igen|Kimenő bájtok.|Bájt|Összesen|A Microsoft. EventHub kimenő bájtjai.|EntityName|
|OutgoingMessages|Igen|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. EventHub kimenő üzenetei.|EntityName|
|OUTMSGS|Igen|Kimenő üzenetek (elavult) (elavult)|Darabszám|Összesen|Egy névtér összes kimenő üzenete. Ez a metrika elavult. Ehelyett használja a kimenő üzenetek metrikáját (elavult)|Nincsenek méretek|
|QuotaExceededErrors|Nem|Kvótatúllépési hibák.|Darabszám|Összesen|A kvóta túllépte a Microsoft. EventHub hibáit.|EntityName |
|Kiszolgálóhibái|Nem|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. EventHub esetében.|EntityName |
|Méret|Nem|Méret|Bájt|Átlag|Egy EventHub mérete bájtban megadva.|EntityName|
|SuccessfulRequests|Nem|Sikeres kérelmek|Darabszám|Összesen|A Microsoft. EventHub sikeres kérelmeinek száma.|EntityName |
|SUCCREQ|Igen|Sikeres kérések (elavult)|Darabszám|Összesen|A névtér összes sikeres kérelme (elavult)|Nincsenek méretek|
|SVRBSY|Igen|Kiszolgáló által foglalt hibák (elavult)|Darabszám|Összesen|Egy névtér összes kiszolgáló által foglalt hibája (elavult)|Nincsenek méretek|
|ThrottledRequests|Nem|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. EventHub által szabályozott kérelmek.|EntityName |
|UserErrors|Nem|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. EventHub felhasználói hibái.|EntityName |


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/fürtök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Igen|Kategorizált átjáró-kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma kategóriánként (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Igen|Átjáróra vonatkozó kérelmek|Darabszám|Összesen|Az átjáróra vonatkozó kérelmek száma|HttpStatus|
|NumActiveWorkers|Igen|Aktív feldolgozók száma|Darabszám|Maximum|Aktív feldolgozók száma|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/AutoscaleSettings

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|MetricThreshold|Igen|Metrika küszöbértéke|Darabszám|Átlag|A konfigurált autoskálázási küszöbérték az autoscale futtatásakor.|MetricTriggerRule|
|ObservedCapacity|Igen|Megfigyelt kapacitás|Darabszám|Átlag|Az a kapacitás, amelyet a program a végrehajtáskor az autoskálázáshoz jelentett.|Nincsenek méretek|
|ObservedMetricValue|Igen|Megfigyelt metrika értéke|Darabszám|Átlag|Az az érték, amelyet az autoscale kiszámít a végrehajtáskor|MetricTriggerSource|
|ScaleActionsInitiated|Igen|Elindított skálázási műveletek|Darabszám|Összesen|A skálázási művelet iránya.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Igen|Rendelkezésre állás|Százalék|Átlag|Sikeresen befejezett rendelkezésre állási tesztek százaléka|availabilityResult/név, availabilityResult/hely|
|availabilityResults/darabszám|Nem|Rendelkezésre állási tesztek|Darabszám|Darabszám|Rendelkezésre állási tesztek száma|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|availabilityResults/időtartam|Igen|Rendelkezésre állási teszt időtartama|Ezredmásodpercben|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/név, availabilityResult/hely, availabilityResult/sikeres|
|browserTimings/networkDuration|Igen|Oldal betöltési hálózati kapcsolati ideje|Ezredmásodpercben|Átlag|A felhasználói kérés és a hálózati kapcsolat közötti idő. Magában foglalja a DNS-keresési és szállítási kapcsolatokat.|Nincsenek méretek|
|browserTimings/processingDuration|Igen|Ügyfél feldolgozási ideje|Ezredmásodpercben|Átlag|A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van.|Nincsenek méretek|
|browserTimings/receiveDuration|Igen|Válaszidő fogadása|Ezredmásodpercben|Átlag|Az első és az utolsó bájt közötti idő, vagy a kapcsolat megszakadása.|Nincsenek méretek|
|browserTimings/sendDuration|Igen|Kérelem küldési ideje|Ezredmásodpercben|Átlag|A hálózati kapcsolat és az első bájt fogadása közötti idő.|Nincsenek méretek|
|browserTimings/totalDuration|Igen|Böngésző oldalának betöltési ideje|Ezredmásodpercben|Átlag|A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik.|Nincsenek méretek|
|függőségek/darabszám|Nem|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított hívások száma.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/időtartam|Igen|Függőség időtartama|Ezredmásodpercben|Átlag|Az alkalmazás által külső erőforrások felé indított hívások időtartama.|függőség/típus, függőség/performanceBucket, függőség/sikeres, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|függőségek/sikertelen|Nem|Függőségi hívások hibái|Darabszám|Darabszám|Az alkalmazás által külső erőforrások felé indított sikertelen függőségi hívások száma.|függőség/típus, függőség/performanceBucket, függőség/cél, függőség/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kivételek/böngésző|Nem|Böngészőkivételek|Darabszám|Darabszám|A böngészőben fellépő nem kezelt kivételek száma.|felhő/roleName|
|kivételek/darabszám|Igen|Kivételek|Darabszám|Darabszám|Az összes nem kezelt kivétel összesített száma.|felhő/roleName, felhő/roleInstance, ügyfél/típus|
|kivételek/kiszolgáló|Nem|Kiszolgálói kivételek|Darabszám|Darabszám|A kiszolgálói alkalmazásban fellépő nem kezelt kivételek száma.|felhő/roleName, felhő/roleInstance|
|Oldalmegtekintések/darabszám|Igen|Lapok nézetei|Darabszám|Darabszám|Lapok nézeteinek száma.|művelet/szintetikus, felhő/roleName|
|Oldalmegtekintések/időtartam|Igen|Oldal nézet betöltési ideje|Ezredmásodpercben|Átlag|Oldal nézet betöltési ideje|művelet/szintetikus, felhő/roleName|
|performanceCounters/exceptionsPerSecond|Igen|Kivételek aránya|CountPerSecond|Átlag|A Windowsnak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET-kivételeket és a .NET-kivételekre konvertált nem kezelt kivételeket.|felhő/roleInstance|
|performanceCounters/memoryAvailableBytes|Igen|Igénybe vehető memória|Bájt|Átlag|A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer általi használatra.|felhő/roleInstance|
|performanceCounters/processCpuPercentage|Igen|Processzor feldolgozása|Százalék|Átlag|Az eltelt idő százalékos aránya, amelyet az összes feldolgozási szál használ a processzor által az utasítások végrehajtásához. Ez 0 és 100 között változhat. Ez a metrika a W3wp-folyamat teljesítményét mutatja.|felhő/roleInstance|
|performanceCounters/processIOBytesPerSecond|Igen|Folyamat i/o-sebessége|BytesPerSecond|Átlag|A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma.|felhő/roleInstance|
|performanceCounters/processorCpuPercentage|Igen|Processzoridő|Százalék|Átlag|Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ.|felhő/roleInstance|
|performanceCounters/processPrivateBytes|Igen|Saját bájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|felhő/roleInstance|
|performanceCounters/requestExecutionTime|Igen|HTTP-kérelem végrehajtási ideje|Ezredmásodpercben|Átlag|A legutóbbi kérelem végrehajtási ideje.|felhő/roleInstance|
|performanceCounters/requestsInQueue|Igen|HTTP-kérelmek az alkalmazás-várólistában|Darabszám|Átlag|Az alkalmazás-kérelmek várólistájának hossza|felhő/roleInstance|
|performanceCounters/requestsPerSecond|Igen|HTTP-kérelmek gyakorisága|CountPerSecond|Átlag|Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET.|felhő/roleInstance|
|kérelmek/darabszám|Nem|Kiszolgálói kérelmek|Darabszám|Darabszám|A befejezett HTTP-kérelmek száma.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/időtartam|Igen|Kiszolgáló válaszideje|Ezredmásodpercben|Átlag|HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|kérelmek/sikertelen|Nem|Sikertelen kérelmek|Darabszám|Darabszám|A sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérések >= 400, és nem egyenlő a 401-es válasz kóddal.|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, felhő/roleName|
|kérelmek/díjszabás|Nem|Kiszolgálói kérelmek gyakorisága|CountPerSecond|Átlag|A kiszolgálói kérelmek másodpercenkénti száma|kérelem/performanceBucket, kérelem/resultCode, művelet/szintetikus, felhő/roleInstance, kérelem/siker, felhő/roleName|
|nyomkövetés/darabszám|Igen|Hívásláncok|Darabszám|Darabszám|Nyomkövetési dokumentumok száma|nyomkövetés/severityLevel, művelet/szintetikus, felhő/roleName, felhő/roleInstance|

## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|C2D. Property. Read. failure|Igen|Nem sikerült beolvasni az eszköz tulajdonságát IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett sikertelen tulajdonságok összes olvasásának száma|Nincsenek méretek|
|C2D. Property. Read. success|Igen|Az eszköz tulajdonságainak olvasása IoT Central|Darabszám|Összesen|Az IoT Centralról kezdeményezett sikeres tulajdonságok száma|Nincsenek méretek|
|C2D. Property. Update. hiba|Igen|Nem sikerült frissíteni az eszköz tulajdonságait IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett sikertelen tulajdonságok frissítéseinek száma|Nincsenek méretek|
|C2D. Property. Update. success|Igen|Az eszköz tulajdonságainak frissítése IoT Central|Darabszám|Összesen|Az IoT Central által kezdeményezett összes sikeres tulajdonság-frissítés száma|Nincsenek méretek|
|connectedDeviceCount|Nem|Csatlakoztatott eszközök összesen|Darabszám|Átlag|IoT Centralhoz csatlakoztatott eszközök száma|Nincsenek méretek|
|D2C. Property. Read. failure|Igen|Nem sikerült beolvasni az eszköz tulajdonságait az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikertelen tulajdonságok összes olvasásának száma|Nincsenek méretek|
|D2C. Property. Read. success|Igen|Az eszköz tulajdonságainak olvasása az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikeres tulajdonságok összes olvasásának száma|Nincsenek méretek|
|D2C. Property. Update. hiba|Igen|Nem sikerült az eszköz tulajdonságainak frissítése az eszközökről|Darabszám|Összesen|Az eszközökről indított összes sikertelen tulajdonság-frissítés száma|Nincsenek méretek|
|D2C. Property. Update. success|Igen|Az eszköz tulajdonságainak frissítése az eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikeres tulajdonság-frissítés száma|Nincsenek méretek|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Tár teljes rendelkezésre állása|Százalék|Átlag|Tár kéréseinek rendelkezésre állása|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Nem|Tár teljes telítettsége|Százalék|Átlag|Tár kapacitásának használata|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Igen|Összes szolgáltatási API-találat|Darabszám|Darabszám|A Service API összes találatának száma|ActivityType, ActivityName|
|ServiceApiLatency|Igen|Általános szolgáltatás API-késése|Ezredmásodpercben|Átlag|A Service API-kérelmek teljes késése|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Igen|Összes szolgáltatás API-eredményei|Darabszám|Darabszám|A szolgáltatás API-eredményeinek teljes száma|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BatchBlobCount|Igen|Köteg Blobok száma|Darabszám|Átlag|Adatforrások száma egy összesített kötegben a betöltéshez.|Adatbázis|
|BatchDuration|Igen|Köteg időtartama|Másodperc|Átlag|A betöltési folyamat összesítési fázisának időtartama.|Adatbázis|
|BatchesProcessed|Igen|Feldolgozott kötegek|Darabszám|Átlag|A betöltéshez összesített kötegek száma. A Batch befejezésének oka: azt határozza meg, hogy a köteg elérte-e a kötegelt házirend által beállított időkorlátot, az adatméretet vagy a fájlok számát.|Adatbázis, SealReason|
|BatchSize|Igen|Köteg mérete|Bájt|Átlag|Tömörítetlen várt adatméret egy összesített kötegben a betöltéshez.|Adatbázis|
|CacheUtilization|Igen|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürt hatókörében|Nincsenek méretek|
|ContinuousExportMaxLatenessMinutes|Igen|Folyamatos exportálás maximális késői|Darabszám|Maximum|A fürt folyamatos exportálási feladatai által jelentett késői idő (percben)|Nincsenek méretek|
|ContinuousExportNumOfRecordsExported|Igen|Folyamatos exportálás – számú exportált rekord|Darabszám|Összesen|Az exportált rekordok száma az exportálási művelet során írt összes tárolási összetevő esetében|ContinuousExportName, adatbázis|
|ContinuousExportPendingCount|Igen|Folyamatos exportálás függőben lévő darabszáma|Darabszám|Maximum|A függőben lévő folyamatos exportálási feladatok végrehajtásra kész állapotának száma|Nincsenek méretek|
|ContinuousExportResult|Igen|Folyamatos exportálás eredménye|Darabszám|Darabszám|Azt jelzi, hogy a folyamatos exportálás sikeres vagy sikertelen volt-e|ContinuousExportName, eredmény, adatbázis|
|CPU|Igen|CPU|Százalék|Átlag|CPU-kihasználtsági szint|Nincsenek méretek|
|EventsProcessedForEventHubs|Igen|Feldolgozott események (Event/IoT hubok esetében)|Darabszám|Összesen|A fürt által az esemény/IoT Hub betöltéskor feldolgozott események száma|EventStatus|
|ExportUtilization|Igen|Exportálás kihasználtsága|Százalék|Maximum|Exportálás kihasználtsága|Nincsenek méretek|
|IngestionLatencyInSeconds|Igen|Betöltési késleltetés (másodperc)|Másodperc|Átlag|A lenyelési idő a forrástól (például az üzenet a EventHub) a fürthöz másodpercek alatt|Nincsenek méretek|
|IngestionResult|Igen|Betöltés eredménye|Darabszám|Darabszám|Betöltési műveletek száma|IngestionResultDetails|
|IngestionUtilization|Igen|Betöltés kihasználtsága|Százalék|Átlag|A használatban lévő betöltési tárolóhelyek aránya a fürtben|Nincsenek méretek|
|IngestionVolumeInMB|Igen|Betöltési mennyiség (MB)|Darabszám|Összesen|A betöltött adatmennyiség teljes mennyisége a fürtön (MB)|Nincsenek méretek|
|InstanceCount|Igen|Példányszám|Darabszám|Átlag|Példányok száma összesen|Nincsenek méretek|
|KeepAlive|Igen|Életben tartása|Darabszám|Átlag|A józan ész-ellenőrzési érték azt jelzi, hogy a fürt válaszol a lekérdezésekre|Nincsenek méretek|
|QueryDuration|Igen|Lekérdezés időtartama|Ezredmásodpercben|Átlag|Lekérdezések időtartama másodpercben|QueryStatus|
|SteamingIngestRequestRate|Igen|Streamelési betöltési kérelmek gyakorisága|Darabszám|RateRequestsPerSecond|Folyamatos átviteli kérelmek aránya (kérelmek/másodperc)|Nincsenek méretek|
|StreamingIngestDataRate|Igen|Streamelés adatbetöltési sebessége|Darabszám|Átlag|Adatforgalom adatátviteli sebessége (MB/s)|Nincsenek méretek|
|StreamingIngestDuration|Igen|Streamelés betöltési időtartama|Ezredmásodpercben|Átlag|Folyamatos átvitel időtartama (ezredmásodperc)|Nincsenek méretek|
|StreamingIngestResults|Igen|Streamelés betöltési eredménye|Darabszám|Átlag|Folyamatos átvitel eredménye|Eredmény|
|TotalNumberOfConcurrentQueries|Igen|Az egyidejű lekérdezések teljes száma|Darabszám|Összesen|Az egyidejű lekérdezések teljes száma|Nincsenek méretek|
|TotalNumberOfExtents|Igen|Egységek teljes száma|Darabszám|Összesen|Adategységek teljes száma|Nincsenek méretek|
|TotalNumberOfThrottledCommands|Igen|A szabályozott parancsok teljes száma|Darabszám|Összesen|A szabályozott parancsok teljes száma|CommandType|
|TotalNumberOfThrottledQueries|Igen|A szabályozott lekérdezések teljes száma|Darabszám|Összesen|A szabályozott lekérdezések teljes száma|Nincsenek méretek|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency|Igen|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionsCompleted|Igen|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Igen|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Igen|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsStarted|Igen|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Igen|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionSuccessLatency|Igen|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Igen|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Igen|Összekötő memóriahasználat integrációs szolgáltatási környezet|Százalék|Átlag|Összekötő memóriahasználat az integrációs szolgáltatási környezetben.|Nincsenek méretek|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Igen|integrációs szolgáltatási környezet összekötő-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet összekötő-processzorának használata.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Igen|integrációs szolgáltatási környezet munkafolyamat-memóriahasználat|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamati memóriahasználat.|Nincsenek méretek|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Igen|A integrációs szolgáltatási környezet munkafolyamat-processzorának használata|Százalék|Átlag|Az integrációs szolgáltatási környezet munkafolyamat-processzorának használata.|Nincsenek méretek|
|RunFailurePercentage|Igen|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|RunLatency|Igen|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunsCancelled|Igen|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Igen|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Igen|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsStarted|Igen|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Igen|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunStartThrottledEvents|Igen|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunSuccessLatency|Igen|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Igen|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|TriggerFireLatency|Igen|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerLatency|Igen|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggersCompleted|Igen|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Igen|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Igen|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Igen|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersStarted|Igen|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Igen|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerSuccessLatency|Igen|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Igen|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActionLatency|Igen|Művelet késése |Másodperc|Átlag|A befejezett munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionsCompleted|Igen|Befejezett műveletek |Darabszám|Összesen|A befejezett munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsFailed|Igen|Sikertelen műveletek |Darabszám|Összesen|Nem sikerült a munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSkipped|Igen|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsStarted|Igen|Elindított műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionsSucceeded|Igen|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|Nincsenek méretek|
|ActionSuccessLatency|Igen|Művelet sikerességi késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|Nincsenek méretek|
|ActionThrottledEvents|Igen|Művelet által szabályozott események|Darabszám|Összesen|A munkafolyamat-műveletek által szabályozott események száma..|Nincsenek méretek|
|BillableActionExecutions|Igen|Számlázható műveletek végrehajtása|Darabszám|Összesen|A számlázott munkafolyamat-műveletek végrehajtásának száma.|Nincsenek méretek|
|BillableTriggerExecutions|Igen|Számlázható trigger-végrehajtások|Darabszám|Összesen|A számlázott munkafolyamat-trigger-végrehajtások száma.|Nincsenek méretek|
|BillingUsageNativeOperation|Igen|A natív művelet végrehajtásához használt számlázási használat|Darabszám|Összesen|A számlázott natív művelet-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStandardConnector|Igen|A standard szintű összekötők végrehajtásának számlázási használata|Darabszám|Összesen|A számlázott szabványos összekötő-végrehajtások száma.|Nincsenek méretek|
|BillingUsageStorageConsumption|Igen|Számlázási használat a tárolási felhasználás végrehajtásához|Darabszám|Összesen|A számlázható tárterület-használat végrehajtásának száma.|Nincsenek méretek|
|RunFailurePercentage|Igen|Sikertelen futtatások százalékos aránya|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya meghiúsult.|Nincsenek méretek|
|RunLatency|Igen|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunsCancelled|Igen|Megszakított futtatások|Darabszám|Összesen|A megszakított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsCompleted|Igen|Befejezett futtatások|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsFailed|Igen|Sikertelen futtatások|Darabszám|Összesen|Sikertelen munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsStarted|Igen|Elindított futtatások|Darabszám|Összesen|Az elindított munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunsSucceeded|Igen|Sikeres futtatások|Darabszám|Összesen|A sikeres munkafolyamat-futtatások száma.|Nincsenek méretek|
|RunStartThrottledEvents|Igen|Indítási leszabályozású események futtatása|Darabszám|Összesen|A munkafolyamat futtatásának megkezdéséhez beállított események száma.|Nincsenek méretek|
|RunSuccessLatency|Igen|Sikeres Futtatás késése|Másodperc|Átlag|A sikeres munkafolyamat-futtatások késése.|Nincsenek méretek|
|RunThrottledEvents|Igen|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-műveletek vagy az elindított események száma.|Nincsenek méretek|
|TotalBillableExecutions|Igen|Számlázandó végrehajtások összesen|Darabszám|Összesen|A számlázandó munkafolyamat-végrehajtások száma.|Nincsenek méretek|
|TriggerFireLatency|Igen|Kiváltó tűz késése |Másodperc|Átlag|Az aktivált munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerLatency|Igen|Trigger késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggersCompleted|Igen|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFailed|Igen|Sikertelen eseményindítók |Darabszám|Összesen|Sikertelen munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersFired|Igen|Kilőtt eseményindítók |Darabszám|Összesen|A kilőtt munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSkipped|Igen|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersStarted|Igen|Elindított eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggersSucceeded|Igen|Sikeres eseményindítók |Darabszám|Összesen|A sikeres munkafolyamat-eseményindítók száma.|Nincsenek méretek|
|TriggerSuccessLatency|Igen|Sikeres triggerek késése |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|Nincsenek méretek|
|TriggerThrottledEvents|Igen|Kiváltott események indítása|Darabszám|Összesen|A beállított munkafolyamat-triggerek száma.|Nincsenek méretek|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív magok|Igen|Aktív magok|Darabszám|Átlag|Aktív magok száma|Forgatókönyv, ClusterName|
|Aktív csomópontok|Igen|Aktív csomópontok|Darabszám|Átlag|Acitve-csomópontok száma Ezek azok a csomópontok, amelyek aktívan futtatnak egy feladatot.|Forgatókönyv, ClusterName|
|A kért futtatások megszakítása|Igen|A kért futtatások megszakítása|Darabszám|Összesen|Azon futtatások száma, amelyek esetében a rendszer a megszakítást kérelmezte ehhez a munkaterülethez. A Count akkor frissül, ha a lemondási kérelem érkezett a futtatáshoz.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Megszakított futtatások|Igen|Megszakított futtatások|Darabszám|Összesen|A munkaterülethez megszakított futtatások száma. A rendszer a Futtatás sikeres megszakítása után frissíti a darabszámot.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Befejezett futtatások|Igen|Befejezett futtatások|Darabszám|Összesen|A munkaterületen sikeresen befejeződött a futtatások száma. A számláló a Futtatás befejeződése után frissül, és a rendszer a kimenetet gyűjtötte.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|CpuUtilization|Igen|CpuUtilization|Darabszám|Átlag|A memória kihasználtságának százalékos aránya egy CPU-csomóponton. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, ClusterName|
|Hibák|Igen|Hibák|Darabszám|Összesen|A munkaterületen futtatott hibák száma. A Count frissítése akkor történik meg, amikor a Futtatás hibát észlel.|Forgatókönyv|
|Sikertelen futtatások|Igen|Sikertelen futtatások|Darabszám|Összesen|A munkaterületen nem sikerült a futtatások száma. A Count a Futtatás meghiúsulása esetén frissül.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Futtatások véglegesítése|Igen|Futtatások véglegesítése|Darabszám|Összesen|A munkaterületre vonatkozó véglegesítési állapotba lépett futtatások száma. A Count akkor frissül, ha egy Futtatás befejeződött, de a kimeneti gyűjtemény még folyamatban van.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|GpuUtilization|Igen|GpuUtilization|Darabszám|Átlag|A memória kihasználtságának százalékos aránya egy GPU-csomóponton. A kihasználtságot egy perces időközönként kell jelenteni.|Forgatókönyv, runId, NodeId, DeviceId, ClusterName|
|Üresjárati magok|Igen|Üresjárati magok|Darabszám|Átlag|Üresjárati magok száma|Forgatókönyv, ClusterName|
|Tétlen csomópontok|Igen|Tétlen csomópontok|Darabszám|Átlag|Az üresjárati csomópontok száma. Az üresjárati csomópontok azok a csomópontok, amelyek nem futtatnak feladatokat, de az elérhetővé tehetik az új feladatot.|Forgatókönyv, ClusterName|
|Magok kihagyása|Igen|Magok kihagyása|Darabszám|Átlag|Kihagyott magok száma|Forgatókönyv, ClusterName|
|Csomópontok elhagyása|Igen|Csomópontok elhagyása|Darabszám|Átlag|A csomópontok elhagyásának száma. A csomópontok elhagyják azokat a csomópontokat, amelyek éppen befejezték a feladatok feldolgozását, és az inaktív állapotba kerülnek.|Forgatókönyv, ClusterName|
|Modell-üzembehelyezés sikertelen|Igen|Modell-üzembehelyezés sikertelen|Darabszám|Összesen|A munkaterületen sikertelen telepítési modellek száma|Forgatókönyv, StatusCode|
|Modell-üzembehelyezés elindítva|Igen|Modell-üzembehelyezés elindítva|Darabszám|Összesen|A munkaterületen elindított modellek központi telepítésének száma|Forgatókönyv|
|Modell-üzembehelyezés sikerült|Igen|Modell-üzembehelyezés sikerült|Darabszám|Összesen|A munkaterületen sikeres központi telepítési modellek száma|Forgatókönyv|
|A modell regisztrálása nem sikerült|Igen|A modell regisztrálása nem sikerült|Darabszám|Összesen|A munkaterületen meghiúsult modell-regisztrációk száma|Forgatókönyv, StatusCode|
|A modell regisztrálása sikerült|Igen|A modell regisztrálása sikerült|Darabszám|Összesen|A munkaterületen sikeres modell-regisztrációk száma|Forgatókönyv|
|Nem válaszoló futtatások|Igen|Nem válaszoló futtatások|Darabszám|Összesen|A munkaterületre nem válaszoló futtatások száma. A Count akkor frissül, ha a Futtatás nem válaszol.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Nem indult el a futtatások|Igen|Nem indult el a futtatások|Darabszám|Összesen|A nem elindított futtatások száma ehhez a munkaterülethez. A Count frissítése akkor történik meg, ha egy futtatási kérelem érkezik, de a futtatási adatok még nem lettek feltöltve. |Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Előzik magok|Igen|Előzik magok|Darabszám|Átlag|Előzik magok száma|Forgatókönyv, ClusterName|
|Előzik-csomópontok|Igen|Előzik-csomópontok|Darabszám|Átlag|Előzik-csomópontok száma Ezek a csomópontok az alacsony prioritású csomópontok, amelyek a rendelkezésre álló csomópont-készletből származnak.|Forgatókönyv, ClusterName|
|Futtatások előkészítése|Igen|Futtatások előkészítése|Darabszám|Összesen|A munkaterületre felkészülő futtatások száma. A Count akkor frissül, ha egy Futtatás előkészítési állapotba kerül, amíg a futtatási környezet előkészítése folyamatban van.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Kiépítési futtatások|Igen|Kiépítési futtatások|Darabszám|Összesen|A munkaterülethez kiépített futtatások száma. A Count frissítése akkor történik meg, ha egy Futtatás a számítási cél létrehozására vagy kiépítési célra vár.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Várólistán lévő futtatások|Igen|Várólistán lévő futtatások|Darabszám|Összesen|Azon futtatások száma, amelyek várólistára kerülnek ehhez a munkaterülethez. A Count akkor frissül, ha egy Futtatás várólistára kerül a számítási célra. Akkor fordulhat elő, ha a szükséges számítási csomópontok üzemkész állapotra várnak.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Kvóta kihasználtsága (%)|Igen|Kvóta kihasználtsága (%)|Darabszám|Átlag|A felhasznált kvóta százaléka|Forgatókönyv, ClusterName, VmFamilyName, VmPriority|
|Elindított futtatások|Igen|Elindított futtatások|Darabszám|Összesen|A munkaterületen futó futtatások száma. A Count akkor frissül, ha a Futtatás a szükséges erőforrásokon fut.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Futtatások indítása|Igen|Futtatások indítása|Darabszám|Összesen|A munkaterülethez elindított futtatások száma. A Count frissítése a futtatási és futtatási adatok (például a futtatási azonosító) létrehozási kérelme alapján történt.|Forgatókönyv, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Magok összesen|Igen|Magok összesen|Darabszám|Átlag|Magok teljes száma|Forgatókönyv, ClusterName|
|Csomópontok összesen|Igen|Csomópontok összesen|Darabszám|Átlag|A csomópontok száma összesen. Ez az összeg magában foglalja az aktív csomópontok, a tétlen csomópontok, a nem használható csomópontok, a Premepted csomópontok és a csomópontok elhagyását|Forgatókönyv, ClusterName|
|Használhatatlan magok|Igen|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Forgatókönyv, ClusterName|
|Használhatatlan csomópontok|Igen|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma Néhány feloldhatatlan probléma miatt nem használható csomópontok nem működőképesek. Az Azure újrahasznosítja ezeket a csomópontokat.|Forgatókönyv, ClusterName|
|Figyelmeztetések|Igen|Figyelmeztetések|Darabszám|Összesen|A futtatási figyelmeztetések száma ebben a munkaterületen. A darabszám akkor frissül, amikor egy Futtatás figyelmeztetést észlel.|Forgatókönyv|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/fiókok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|Az API-k rendelkezésre állása|ApiCategory, ApiName|
|Használat|Nem|Használat|Darabszám|Darabszám|API-hívások száma|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AssetCount|Igen|Eszközök száma|Darabszám|Átlag|Az aktuális Media Service-fiókban már létrehozott eszközök száma|Nincsenek méretek|
|AssetQuota|Igen|Eszköz kvótája|Darabszám|Átlag|Hány eszköz engedélyezett a jelenlegi Media Service-fiókhoz|Nincsenek méretek|
|AssetQuotaUsedPercentage|Igen|Eszköz kvótájának kihasználtsága (%)|Százalék|Átlag|Az eszköz által használt százalék a jelenlegi Media Service-fiókban|Nincsenek méretek|
|ContentKeyPolicyCount|Igen|Tartalmi kulcsokra vonatkozó szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejött a tartalmi kulcs házirendjeinek száma|Nincsenek méretek|
|ContentKeyPolicyQuota|Igen|Tartalmi kulcs házirend-kvótája|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó tartalmi kulcsokra vonatkozó házirendek száma|Nincsenek méretek|
|ContentKeyPolicyQuotaUsedPercentage|Igen|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|Százalék|Átlag|A tartalmi kulcsra vonatkozó házirend százaléka a jelenlegi Media Service-fiókban|Nincsenek méretek|
|StreamingPolicyCount|Igen|Folyamatos átviteli szabályzatok száma|Darabszám|Átlag|A jelenlegi Media Service-fiókban már létrejöttek a folyamatos átviteli házirendek|Nincsenek méretek|
|StreamingPolicyQuota|Igen|Streaming Policy-kvóta|Darabszám|Átlag|Az aktuális Media Service-fiókhoz tartozó folyamatos átviteli házirendek száma|Nincsenek méretek|
|StreamingPolicyQuotaUsedPercentage|Igen|Adatfolyam-házirend kvótájának kihasználtsága (%)|Százalék|Átlag|A streaming Policy használt százalék a jelenlegi Media Service-fiókban|Nincsenek méretek|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Mediaservices/streamingEndpoints

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatforgalom mennyisége bájtban kifejezve.|OutputFormat|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|Továbbítási végpontra irányuló kérelmek.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Igen|A Befejezés végének késése|Ezredmásodpercben|Átlag|A sikeres kérelmek átlagos késése ezredmásodpercben.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Igen|Készlet lefoglalt mérete|Bájt|Átlag|A készlet kiépített mérete|Nincsenek méretek|
|VolumePoolAllocatedUsed|Igen|A kötet méretéhez lefoglalt készlet|Bájt|Átlag|A készlet lefoglalt felhasznált mérete|Nincsenek méretek|
|VolumePoolTotalLogicalSize|Igen|Készlet felhasznált mérete|Bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|Nincsenek méretek|
|VolumePoolTotalSnapshotSize|Igen|A készlethez tartozó pillanatképek teljes mérete|Bájt|Átlag|A készletben lévő összes kötet pillanatkép-méretének összege|Nincsenek méretek|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/kötetek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AverageReadLatency|Igen|Olvasási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Olvasási késleltetés átlagos száma ezredmásodpercben/művelet|Nincsenek méretek|
|AverageWriteLatency|Igen|Írási késleltetés átlagos késése|Ezredmásodpercben|Átlag|Írási késleltetés átlagos száma ezredmásodpercben|Nincsenek méretek|
|CbsVolumeBackupActive|Igen|Kötet biztonsági mentésének aktív állapota|Darabszám|Átlag|A biztonsági mentés jelenleg fel van függesztve a kötetre.|Nincsenek méretek|
|CbsVolumeLogicalBackupBytes|Igen|Logikai bájtok biztonsági mentése|Bájt|Átlag|A kötetre vonatkozó, nem tömörített/nem titkosított bájtok biztonsági mentése Toatl.|Nincsenek méretek|
|CbsVolumeOperationComplete|Igen|Művelet állapota|Darabszám|Átlag|A legutóbbi biztonsági mentési/visszaállítási művelet sikeres volt.|Nincsenek méretek|
|CbsVolumeOperationTransferredBytes|Igen|Átvitt bájtok a művelethez|Bájt|Átlag|A legutóbbi biztonsági mentési/visszaállítási művelethez továbbított bájtok száma összesen.|Nincsenek méretek|
|CbsVolumeProtected|Igen|Kötet védett állapota|Darabszám|Átlag|A kötetet a Cloud Backup szolgáltatás védi.|Nincsenek méretek|
|ReadIops|Igen|IOPS olvasása|CountPerSecond|Átlag|Olvasási/kimenő műveletek másodpercenként|Nincsenek méretek|
|VolumeAllocatedSize|Igen|Kötet lefoglalt mérete|Bájt|Átlag|Kötet kiépített mérete|Nincsenek méretek|
|VolumeLogicalSize|Igen|Kötet felhasznált mérete|Bájt|Átlag|A kötet logikai mérete (felhasznált bájtok)|Nincsenek méretek|
|VolumeSnapshotSize|Igen|Kötet pillanatképének mérete|Bájt|Átlag|A köteten található összes pillanatkép mérete|Nincsenek méretek|
|WriteIops|Igen|IOPS írása|CountPerSecond|Átlag|Írási/kimenő műveletek másodpercenként|Nincsenek méretek|
|XregionReplicationHealthy|Igen|A kötet replikálási állapota Kifogástalan|Darabszám|Átlag|A kapcsolat feltétele, 1 vagy 0.|Nincsenek méretek|
|XregionReplicationLagTime|Igen|Kötet replikációs késési ideje|Másodperc|Átlag|Az az időtartam másodpercben, ameddig a tükrözött adatmennyiség a forrás mögött marad.|Nincsenek méretek|
|XregionReplicationLastTransferDuration|Igen|Kötet replikálásának utolsó átvitelének időtartama|Másodperc|Átlag|Az utolsó átvitel befejezéséhez szükséges idő másodpercben.|Nincsenek méretek|
|XregionReplicationLastTransferSize|Igen|Kötet replikálásának utolsó átvitelének mérete|Bájt|Átlag|Az utolsó átvitel részeként átvitt bájtok teljes száma.|Nincsenek méretek|
|XregionReplicationRelationshipProgress|Igen|Kötet replikálásának folyamata|Bájt|Átlag|Az aktuális adatátviteli művelethez továbbított adatok teljes mennyisége.|Nincsenek méretek|
|XregionReplicationRelationshipTransferring|Igen|A kötet-replikálás átadása|Darabszám|Átlag|Azt jelzi, hogy a kötet replikálásának állapota "átvitel".|Nincsenek méretek|
|XregionReplicationTotalTransferBytes|Igen|Kötet replikálásának teljes átvitele|Bájt|Átlag|A kapcsolathoz továbbított összesített bájtok száma.|Nincsenek méretek|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Nem|Application Gateway teljes idő|Ezredmásodpercben|Átlag|A kérelem feldolgozásának és a válasz elküldésekor elvégezhető átlagos idő. Ez az időszak átlaga, amikor a Application Gateway megkapja egy HTTP-kérelem első bájtját, amikor a válasz küldése művelet befejeződik. Fontos megjegyezni, hogy ez általában a Application Gateway feldolgozási időt, az időpontot, amikor a kérés és a válasz csomagjai a hálózaton keresztül utaznak, valamint a háttér-kiszolgáló válaszának időpontját.|Figyelő|
|AvgRequestCountPerHealthyHost|Nem|Percenkénti kérelmek száma kifogástalan állapotú gazdagépen|Darabszám|Átlag|Kérelmek átlagos száma percenként, a készletben lévő kifogástalan állapotú gazdagépek esetében|BackendSettingsPool|
|BackendConnectTime|Nem|Háttérbeli kapcsolat ideje|Ezredmásodpercben|Átlag|A háttér-kiszolgálóval létesített kapcsolatok létrehozásához töltött idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendFirstByteResponseTime|Nem|Háttérbeli első bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz fejléc első bájtjának fogadása között eltelt idő, a háttér-kiszolgáló feldolgozási idejének megközelítő időpontja|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendLastByteResponseTime|Nem|Háttérbeli utolsó bájt válaszideje|Ezredmásodpercben|Átlag|A háttérrendszer-kiszolgálóhoz való kapcsolódás megkezdése és a válasz törzse utolsó bájtjának fogadása között eltelt idő|Figyelő, BackendServer, Háttérkészletek, Backendhttpsetting értékre|
|BackendResponseStatus|Igen|Háttérbeli válasz állapota|Darabszám|Összesen|A háttérbeli tagok által létrehozott HTTP-válasz kódok száma. Ez nem tartalmazza a Application Gateway által létrehozott válasz-kódokat.|BackendServer, Háttérkészletek, Backendhttpsetting értékre, HttpStatusGroup|
|BlockedCount|Igen|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal letiltott kérelmek szabályának eloszlása|Szerepkörcsoportot, RuleId|
|BlockedReqCount|Igen|Webalkalmazási tűzfal letiltott kérelmek száma|Darabszám|Összesen|Webalkalmazási tűzfal letiltott kérelmek száma|Nincsenek méretek|
|BytesReceived|Igen|Fogadott bájtok száma|Bájt|Összesen|A Application Gateway által az ügyfelektől fogadott bájtok teljes száma|Figyelő|
|BytesSent|Igen|Eljuttatott bájtok|Bájt|Összesen|A Application Gateway által az ügyfeleknek eljuttatott bájtok teljes száma|Figyelő|
|CapacityUnits|Nem|Aktuális kapacitási egységek|Darabszám|Átlag|Felhasznált kapacitási egységek|Nincsenek méretek|
|ClientRtt|Nem|Ügyfél-RTT|Ezredmásodpercben|Átlag|Az ügyfelek és a Application Gateway közötti átlagos menetidő. Ez a metrika azt jelzi, hogy mennyi időt vesz igénybe a kapcsolatok és a visszaküldési visszaigazolás|Figyelő|
|ComputeUnits|Nem|Aktuális számítási egységek|Darabszám|Átlag|Felhasznált számítási egységek|Nincsenek méretek|
|CpuUtilization|Nem|Processzor kihasználtsága|Százalék|Átlag|A Application Gateway aktuális CPU-kihasználtsága|Nincsenek méretek|
|Összege|Igen|Aktuális kapcsolatok|Darabszám|Összesen|Application Gatewaysal létesített aktuális kapcsolatok száma|Nincsenek méretek|
|EstimatedBilledCapacityUnits|Nem|Becsült számlázott kapacitási egységek|Darabszám|Átlag|A felszámított becsült kapacitási egységek|Nincsenek méretek|
|FailedRequests|Igen|Sikertelen kérelmek|Darabszám|Összesen|A Application Gateway által kiszolgált sikertelen kérelmek száma|BackendSettingsPool|
|FixedBillableCapacityUnits|Nem|Fix számlázandó kapacitásegységek|Darabszám|Átlag|Minimálisan felszámított kapacitási egységek|Nincsenek méretek|
|HealthyHostCount|Igen|Kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Az egészséges háttérbeli gazdagépek száma|BackendSettingsPool|
|MatchedCount|Igen|Webalkalmazási tűzfal teljes szabályának eloszlása|Darabszám|Összesen|Webalkalmazási tűzfal – teljes szabály eloszlása a bejövő forgalomhoz|Szerepkörcsoportot, RuleId|
|NewConnectionsPerSecond|Nem|Új kapcsolatok másodpercenként|CountPerSecond|Átlag|Új kapcsolatok másodpercenként létesített Application Gateway|Nincsenek méretek|
|ResponseStatus|Igen|Válasz állapota|Darabszám|Összesen|Application Gateway által visszaadott http-válasz állapota|HttpStatusGroup|
|Teljesítmény|Nem|Teljesítmény|BytesPerSecond|Átlag|A Application Gateway által kiszolgált bájtok másodpercenkénti száma|Nincsenek méretek|
|TlsProtocol|Igen|Ügyfél TLS protokoll|Darabszám|Összesen|Azoknak a TLS-és nem TLS-kérelmeknek a száma, amelyek az ügyfél által kezdeményezett Application Gatewaykal létesített kapcsolatban állnak. A TLS protokoll terjesztésének megtekintéséhez szűrje a Dimension TLS protokollt.|Figyelő, TlsProtocol|
|TotalRequests|Igen|Összes kérelem|Darabszám|Összesen|A Application Gateway által kiszolgált sikeres kérések száma|BackendSettingsPool|
|UnhealthyHostCount|Igen|Nem kifogástalan állapotú gazdagépek száma|Darabszám|Átlag|Sérült háttérbeli gazdagépek száma|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Igen|Alkalmazás-szabályok találatok száma|Darabszám|Összesen|Az alkalmazások szabályainak száma|Állapot, ok, protokoll|
|DataProcessed|Igen|Feldolgozott adatok|Bájt|Összesen|A tűzfal által feldolgozott adatmennyiség teljes mennyisége|Nincsenek méretek|
|FirewallHealth|Igen|Tűzfal állapota|Százalék|Átlag|A tűzfal általános állapotát jelzi|Állapot, ok|
|NetworkRuleHit|Igen|Hálózati szabályok találatok száma|Darabszám|Összesen|A hálózati szabályok számának megtalálása|Állapot, ok, protokoll|
|SNATPortUtilization|Igen|SNAT-portok kihasználtsága|Százalék|Átlag|Jelenleg használatban lévő kimenő SNAT-portok százalékos aránya|Protokoll|
|Teljesítmény|Nem|Teljesítmény|BitsPerSecond|Átlag|A tűzfal által feldolgozott átviteli sebesség|Nincsenek méretek|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Igen|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|Igen|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek méretek|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|QueryVolume|Igen|Lekérdezési kötet|Darabszám|Összesen|A DNS-zónák számára kiszolgált lekérdezések száma|Nincsenek méretek|
|RecordSetCapacityUtilization|Nem|Rekordazonosító kapacitásának kihasználtsága|Százalék|Maximum|Egy DNS-zóna által használt rekordazonosító-kapacitás százaléka|Nincsenek méretek|
|RecordSetCount|Igen|Rekordok készletének száma|Darabszám|Maximum|Rekordhalmazok száma egy DNS-zónában|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ArpAvailability|Igen|ARP rendelkezésre állása|Százalék|Átlag|Az ARP és a MSEE közötti rendelkezésre állás az összes társ felé.|PeeringType, társ|
|BgpAvailability|Igen|BGP rendelkezésre állása|Százalék|Átlag|A BGP rendelkezésre állása a MSEE az összes társ felé.|PeeringType, társ|
|BitsInPerSecond|Nem|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeringType|
|BitsOutPerSecond|Nem|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeringType|
|GlobalReachBitsInPerSecond|Nem|GlobalReachBitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|Nem|GlobalReachBitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Nem|DroppedInBitsPerSecond|CountPerSecond|Átlag|Bejövő adatforgalom (bit/mp)|Nincsenek méretek|
|QosDropBitsOutPerSecond|Nem|DroppedOutBitsPerSecond|CountPerSecond|Átlag|Kimenő adatforgalom másodpercenkénti száma|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/társak

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Igen|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Nincsenek méretek|
|BitsOutPerSecond|Igen|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Nincsenek méretek|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Nem|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Kapcsolatnév|
|ErGatewayConnectionBitsOutPerSecond|Nem|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Kapcsolatnév|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AdminState|Igen|AdminState|Darabszám|Átlag|A port rendszergazdai állapota|Hivatkozás|
|LineProtocol|Igen|LineProtocol|Darabszám|Átlag|A port protokolljának állapota|Hivatkozás|
|PortBitsInPerSecond|Igen|BitsInPerSecond|CountPerSecond|Átlag|Az Azure-ra bejövő bitek száma másodpercenként|Hivatkozás|
|PortBitsOutPerSecond|Igen|BitsOutPerSecond|CountPerSecond|Átlag|Az Azure-ról kimenő bitek száma másodpercenként|Hivatkozás|
|RxLightLevel|Igen|RxLightLevel|Darabszám|Átlag|Rx-fény szintje dBm-ben|Hivatkozás, sáv|
|TxLightLevel|Igen|TxLightLevel|Darabszám|Átlag|TX-fény szintje dBm-ben|Hivatkozás, sáv|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Igen|Háttér állapotának százalékos aránya|Százalék|Átlag|A HTTP/S proxyról a háttérbe való sikeres állapot-mintavételek százalékos aránya|Háttér, Háttérkészletek|
|BackendRequestCount|Igen|Háttérbeli kérelmek száma|Darabszám|Összesen|A HTTP/S proxyról a háttérre küldött kérések száma|HttpStatus, HttpStatusGroup, háttérrendszer|
|BackendRequestLatency|Igen|Háttérbeli kérelmek késése|Ezredmásodpercben|Átlag|A HTTP/S proxy által a háttérbe való kérelem elküldésekor kiszámított idő, amíg a HTTP/S proxy nem kapta meg a háttér utolsó válaszának bájtját.|Háttérrendszer|
|BillableResponseSize|Igen|Számlázható válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött számlázandó bájtjainak (minimális 2KB) száma az ügyfeleknek.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Igen|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfél-kérelmek száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Igen|Kérelem mérete|Bájt|Összesen|Az ügyfelek által a HTTP/S proxynak küldött kérelmekként küldött bájtok száma|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Igen|Válasz mérete|Bájt|Összesen|A HTTP/S proxy válaszként küldött bájtok száma az ügyfeleknek|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Igen|Teljes késés|Ezredmásodpercben|Átlag|Az ügyfél által a http/S proxy által fogadott kérelemből kiszámított idő, amíg az ügyfél elismerte a HTTP/S proxy utolsó válaszának bájtját.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Igen|Webalkalmazási tűzfalra vonatkozó kérelmek száma|Darabszám|Összesen|A webalkalmazási tűzfal által feldolgozott ügyfelek kéréseinek száma|PolicyName, RuleName, művelet|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|Nem|Lefoglalt SNAT-portok|Darabszám|Átlag|Az időszakon belül lefoglalt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Igen|Bájtok száma|Darabszám|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|FrontendIPAddress, FrontendPort, irány|
|DipAvailability|Igen|Állapotadat-mintavétel|Darabszám|Átlag|Az állapot átlagos Load Balancer állapotának időtartama időszakonként|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Igen|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|SnatConnectionCount|Igen|SNAT-kapcsolatok száma|Darabszám|Összesen|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Igen|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress, FrontendPort, irány|
|UsedSnatPorts|Nem|Használt SNAT-portok|Darabszám|Átlag|Az időszakon belül használt SNAT-portok teljes száma|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Igen|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Az adatelérési út átlagos Load Balancer időtartamának időtartama|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Igen|Fogadott bájtok száma|Bájt|Összesen|A hálózati adapter által fogadott bájtok száma|Nincsenek méretek|
|BytesSentRate|Igen|Eljuttatott bájtok|Bájt|Összesen|A hálózati adapter által eljuttatott bájtok száma|Nincsenek méretek|
|PacketsReceivedRate|Igen|Fogadott csomagok|Darabszám|Összesen|A hálózati adapter által fogadott csomagok száma|Nincsenek méretek|
|PacketsSentRate|Igen|Küldött csomagok|Darabszám|Összesen|A hálózati adapter által küldött csomagok száma|Nincsenek méretek|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Igen|Átlagos menetidő (MS)|Ezredmásodpercben|Átlag|A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS)|Nincsenek méretek|
|ChecksFailedPercent|Igen|Sikertelen ellenőrzések százalékos aránya (előzetes verzió)|Százalék|Átlag|a kapcsolat figyelési ellenőrzése nem sikerült|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|ProbesFailedPercent|Igen|%-Os mintavétel sikertelen|Százalék|Átlag|a kapcsolat figyelési mintavételének%-a meghiúsult|Nincsenek méretek|
|RoundTripTimeMs|Igen|Round-Trip idő (MS) (előzetes verzió)|Ezredmásodpercben|Átlag|A kapcsolat figyelési ellenőrzésének időkorlátja ezredmásodpercben|SourceAddress, SourceName, Sourceresourceid azonosítónak, forrás típusa, protokoll, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ByteCount|Igen|Bájtok száma|Darabszám|Összesen|Az adott időszakon belül továbbított bájtok teljes száma|Port, irány|
|BytesDroppedDDoS|Igen|Bejövő bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő bájtok elvetve DDoS|Nincsenek méretek|
|BytesForwardedDDoS|Igen|Bejövő bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő bájtok továbbított DDoS|Nincsenek méretek|
|BytesInDDoS|Igen|Bejövő bájtok DDoS|BytesPerSecond|Maximum|Bejövő bájtok DDoS|Nincsenek méretek|
|DDoSTriggerSYNPackets|Igen|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő SYN-csomagok a DDoS-elhárítás elindításához|Nincsenek méretek|
|DDoSTriggerTCPPackets|Igen|Bejövő TCP-csomagok a DDoS-mérséklés indításához|CountPerSecond|Maximum|Bejövő TCP-csomagok a DDoS-mérséklés indításához|Nincsenek méretek|
|DDoSTriggerUDPPackets|Igen|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|CountPerSecond|Maximum|Bejövő UDP-csomagok a DDoS-elhárítás elindításához|Nincsenek méretek|
|IfUnderDDoSAttack|Igen|DDoS-támadás alatt vagy nem|Darabszám|Maximum|DDoS-támadás alatt vagy nem|Nincsenek méretek|
|PacketCount|Igen|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Port, irány|
|PacketsDroppedDDoS|Igen|Bejövő csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő csomagok eldobott DDoS|Nincsenek méretek|
|PacketsForwardedDDoS|Igen|Bejövő csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő csomagok továbbított DDoS|Nincsenek méretek|
|PacketsInDDoS|Igen|Bejövő csomagok DDoS|CountPerSecond|Maximum|Bejövő csomagok DDoS|Nincsenek méretek|
|SynCount|Igen|SYN-szám|Darabszám|Összesen|Az időszakon belül továbbított SYN-csomagok teljes száma|Port, irány|
|TCPBytesDroppedDDoS|Igen|Bejövő TCP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok elvetve DDoS|Nincsenek méretek|
|TCPBytesForwardedDDoS|Igen|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|Nincsenek méretek|
|TCPBytesInDDoS|Igen|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|Nincsenek méretek|
|TCPPacketsDroppedDDoS|Igen|Bejövő TCP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok eldobott DDoS|Nincsenek méretek|
|TCPPacketsForwardedDDoS|Igen|Bejövő TCP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok továbbított DDoS|Nincsenek méretek|
|TCPPacketsInDDoS|Igen|Bejövő TCP-csomagok DDoS|CountPerSecond|Maximum|Bejövő TCP-csomagok DDoS|Nincsenek méretek|
|UDPBytesDroppedDDoS|Igen|Bejövő UDP-bájtok elvetve DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok elvetve DDoS|Nincsenek méretek|
|UDPBytesForwardedDDoS|Igen|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|Nincsenek méretek|
|UDPBytesInDDoS|Igen|Bejövő UDP bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP bájtok DDoS|Nincsenek méretek|
|UDPPacketsDroppedDDoS|Igen|Bejövő UDP-csomagok eldobott DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok eldobott DDoS|Nincsenek méretek|
|UDPPacketsForwardedDDoS|Igen|Bejövő UDP-csomagok továbbított DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok továbbított DDoS|Nincsenek méretek|
|UDPPacketsInDDoS|Igen|Bejövő UDP-csomagok DDoS|CountPerSecond|Maximum|Bejövő UDP-csomagok DDoS|Nincsenek méretek|
|VipAvailability|Igen|Adatelérési út rendelkezésre állása|Darabszám|Átlag|Átlagos IP-cím rendelkezésre állása időszakonként|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Igen|Végponti állapot végpont szerint|Darabszám|Maximum|1 Ha a végpont mintavételi állapota "enabled" (engedélyezve), 0 más.|Végpontneve|
|QpsByEndpoint|Igen|Visszaadott végponti lekérdezések|Darabszám|Összesen|Az adott időkeretben a Traffic Manager-végpontok számának visszaadása|Végpontneve|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AverageBandwidth|Igen|Átjáró S2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SBandwidth|Igen|Átjáró P2S sávszélessége|BytesPerSecond|Átlag|Egy átjáró átlagos pont – hely sávszélessége bájt/másodpercben|Nincsenek méretek|
|P2SConnectionCount|Igen|P2S-kapcsolatok száma|Darabszám|Maximum|Az átjáró P2S-kapcsolatainak száma|Protokoll|
|TunnelAverageBandwidth|Igen|Alagút sávszélessége|BytesPerSecond|Átlag|Az alagút átlagos sávszélessége bájt/másodpercben|Kapcsolatnév, RemoteIP|
|TunnelEgressBytes|Igen|Alagút kimenő forgalma (bájt)|Bájt|Összesen|Az alagútról kimenő bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Igen|Alagút forgalomválasztó-eltérés miatti kimenőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelEgressPackets|Igen|Alagút kimenő forgalma (csomag)|Darabszám|Összesen|Az alagútról kimenő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressBytes|Igen|Alagút bejövő forgalma (bájt)|Bájt|Összesen|Az alagúthoz beérkező bájtok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Igen|Alagút forgalomválasztó-eltérés miatti bejövőcsomag-elvetése|Darabszám|Összesen|Az alagút forgalomválasztójának eltérése miatt elvetett bejövő csomagok száma|Kapcsolatnév, RemoteIP|
|TunnelIngressPackets|Igen|Bújtatási bejövő csomagok|Darabszám|Összesen|Az alagúthoz beérkező csomagok száma|Kapcsolatnév, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Igen|A pingelések időpontjának kerekítése egy virtuális géphez|Ezredmásodpercben|Átlag|A célként megadott virtuális gépre eljuttatott pingelések menetének időpontja|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Igen|Sikertelen pingelések egy virtuális géphez|Százalék|Átlag|A hibás pingelések száma a cél virtuális gép összes elküldéses pingelésének százalékában|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/névterek/NotificationHubs

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|bejövő|Igen|Bejövő üzenetek|Darabszám|Összesen|A sikeres küldési API-hívások száma. |Nincsenek méretek|
|bejövő. ALL. failedrequests|Igen|Minden bejövő sikertelen kérelem|Darabszám|Összesen|Értesítési központ bejövő sikertelen kéréseinek száma|Nincsenek méretek|
|bejövő. ALL. requests|Igen|Minden bejövő kérelem|Darabszám|Összesen|Értesítési központ összes bejövő kérelme|Nincsenek méretek|
|bejövő. ütemezett|Igen|Ütemezett leküldéses értesítések elküldve|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|bejövő. ütemezett. Mégse|Igen|Ütemezett leküldéses értesítések megszakítva|Darabszám|Összesen|Ütemezett leküldéses értesítések megszakítva|Nincsenek méretek|
|a telepítés. All|Igen|Telepítési felügyeleti műveletek|Darabszám|Összesen|Telepítési felügyeleti műveletek|Nincsenek méretek|
|telepítés. Törlés|Igen|Telepítési műveletek törlése|Darabszám|Összesen|Telepítési műveletek törlése|Nincsenek méretek|
|telepítés. Get|Igen|Telepítési műveletek beolvasása|Darabszám|Összesen|Telepítési műveletek beolvasása|Nincsenek méretek|
|Installation. patch|Igen|Javítások telepítési műveletei|Darabszám|Összesen|Javítások telepítési műveletei|Nincsenek méretek|
|telepítési. upsert|Igen|Telepítési műveletek létrehozása vagy frissítése|Darabszám|Összesen|Telepítési műveletek létrehozása vagy frissítése|Nincsenek méretek|
|notificationhub. leküldések|Igen|Minden kimenő értesítés|Darabszám|Összesen|Az értesítési központ összes kimenő értesítése|Nincsenek méretek|
|kimenő. allpns. badorexpiredchannel|Igen|Rossz vagy lejárt csatorna-hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő csatorna/jogkivonat/regisztrációban lejárt vagy érvénytelen.|Nincsenek méretek|
|kimenő. allpns. channelerror|Igen|Csatorna hibái|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna érvénytelen, és nem lett hozzárendelve a megfelelő alkalmazáshoz (szabályozott vagy lejárt).|Nincsenek méretek|
|kimenő. allpns. invalidpayload|Igen|Hasznos adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS hibás adattartalom-hibát adott vissza.|Nincsenek méretek|
|kimenő. allpns. pnserror|Igen|Külső értesítési rendszerhibák|Darabszám|Összesen|A sikertelen leküldések száma, mert hiba történt a PNS való kommunikáció során (a hitelesítési problémák kizárása).|Nincsenek méretek|
|kimenő. allpns. sikeres|Igen|Sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. apns. badchannel|Igen|APNS hibás csatorna hiba|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert a jogkivonat érvénytelen (APNS: 8).|Nincsenek méretek|
|kimenő. apns. expiredchannel|Igen|APNS lejárt csatorna hibája|Darabszám|Összesen|A APNS visszajelzési csatornája által érvénytelenített jogkivonat száma.|Nincsenek méretek|
|kimenő. apns. invalidcredentials|Igen|APNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. apns. invalidnotificationsize|Igen|A APNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (APNS-állapotkód: 7).|Nincsenek méretek|
|kimenő. apns. pnserror|Igen|APNS hibák|Darabszám|Összesen|A APNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. apns. sikeres|Igen|APNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. authenticationerror|Igen|GCM-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM result: MismatchedSenderId).|Nincsenek méretek|
|kimenő. GCM. badchannel|Igen|GCM hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban a regisztráció során nem ismerhető fel (GCM-eredmény: érvénytelen regisztráció).|Nincsenek méretek|
|kimenő. GCM. expiredchannel|Igen|GCM lejárt csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban lejárt (GCM-eredmény: NotRegistered).|Nincsenek méretek|
|kimenő. GCM. invalidcredentials|Igen|GCM-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. GCM. invalidnotificationformat|Igen|GCM – érvénytelen értesítési formátum|Darabszám|Összesen|Az adattartalom helytelen formázása miatt sikertelen leküldések száma (GCM eredmény: InvalidDataKey vagy InvalidTtl).|Nincsenek méretek|
|kimenő. GCM. invalidnotificationsize|Igen|A GCM érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az adattartalom túl nagy mérete miatt sikertelen leküldések száma (GCM eredmény: MessageTooBig).|Nincsenek méretek|
|kimenő. GCM. pnserror|Igen|GCM hibák|Darabszám|Összesen|A GCM szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. GCM. sikeres|Igen|GCM sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. GCM. szabályozva|Igen|GCM-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a GCM leszabályozza az alkalmazást (GCM: 501-599 vagy eredmény: nem érhető el).|Nincsenek méretek|
|kimenő. GCM. wrongchannel|Igen|GCM rossz csatorna hibája|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban nincs társítva a jelenlegi alkalmazáshoz (GCM-eredmény: InvalidPackageName).|Nincsenek méretek|
|kimenő. mpns. authenticationerror|Igen|MPNS-hitelesítési hibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. badchannel|Igen|MPNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (MPNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. mpns. channeldisconnected|Igen|MPNS csatorna leválasztva|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztráció regisztrációban szereplő URI megszakadt (MPNS-állapot: 412 nem található).|Nincsenek méretek|
|kimenő. mpns. Dropped|Igen|MPNS eldobott értesítések|Darabszám|Összesen|A MPNS által eldobott leküldések száma (MPNS-válasz fejléce: X-NotificationStatus: QueueFull vagy letiltva).|Nincsenek méretek|
|kimenő. mpns. invalidcredentials|Igen|MPNS – érvénytelen hitelesítő adatok|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|Nincsenek méretek|
|kimenő. mpns. invalidnotificationformat|Igen|MPNS – érvénytelen értesítési formátum|Darabszám|Összesen|Azon leküldések száma, amelyek sikertelenek voltak, mert az értesítés adattartalma túl nagy.|Nincsenek méretek|
|kimenő. mpns. pnserror|Igen|MPNS hibák|Darabszám|Összesen|A MPNS szolgáltatással kommunikáló hibák miatt sikertelen leküldések száma.|Nincsenek méretek|
|kimenő. mpns. sikeres|Igen|MPNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. mpns. szabályozva|Igen|MPNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a MPNS szabályozza az alkalmazást (WNS MPNS: 406 nem fogadható el).|Nincsenek méretek|
|kimenő. wns. authenticationerror|Igen|WNS-hitelesítési hibák|Darabszám|Összesen|Nem érkezik értesítés a Windows Live érvénytelen hitelesítő adatokkal vagy helytelen jogkivonattal kommunikáló hibák miatt.|Nincsenek méretek|
|kimenő. wns. badchannel|Igen|WNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban szereplő URI a regisztráció során nem ismerhető fel (WNS állapot: 404 nem található).|Nincsenek méretek|
|kimenő. wns. channeldisconnected|Igen|WNS csatorna leválasztva|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. channelthrottled|Igen|WNS csatorna szabályozása|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztrációban szereplő regisztrációban szereplő URI szabályozva van (WNS-válasz fejléce: X-WNS-NotificationStatus: channelThrottled).|Nincsenek méretek|
|kimenő. wns. Dropped|Igen|WNS eldobott értesítések|Darabszám|Összesen|Az értesítés el lett dobva, mert a regisztráció regisztrációban szereplő URI szabályozva van (X-WNS-NotificationStatus: kihagyva, de nem X-WNS-DeviceConnectionStatus: leválasztva).|Nincsenek méretek|
|kimenő. wns. expiredchannel|Igen|WNS lejárt csatorna hibája|Darabszám|Összesen|Az regisztrációban szereplő URI érvényességének lejárta miatt sikertelen leküldések száma (WNS állapota: 410 elveszett).|Nincsenek méretek|
|kimenő. wns. invalidcredentials|Igen|WNS-hitelesítési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A sikertelen leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|Nincsenek méretek|
|kimenő. wns. invalidnotificationformat|Igen|WNS – érvénytelen értesítési formátum|Darabszám|Összesen|Az értesítés formátuma érvénytelen (WNS állapot: 400). Vegye figyelembe, hogy a WNS nem utasítja el az összes érvénytelen hasznos adatot.|Nincsenek méretek|
|kimenő. wns. invalidnotificationsize|Igen|A WNS érvénytelen értesítési méretet észlelt|Darabszám|Összesen|Az értesítési tartalom túl nagy (WNS állapot: 413).|Nincsenek méretek|
|kimenő. wns. invalidtoken|Igen|WNS-hitelesítési hibák (érvénytelen token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvénytelen (WNS-állapot: 401 jogosulatlan).|Nincsenek méretek|
|kimenő. wns. pnserror|Igen|WNS hibák|Darabszám|Összesen|A WNS-vel folytatott kommunikáció hibája miatt nem érkezik értesítés.|Nincsenek méretek|
|kimenő. wns. sikeres|Igen|WNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|Nincsenek méretek|
|kimenő. wns. szabályozva|Igen|WNS-szabályozású értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a WNS szabályozza az alkalmazást (WNS állapota: 406, nem fogadható el).|Nincsenek méretek|
|kimenő. wns. tokenproviderunreachable|Igen|WNS-hitelesítési hibák (nem érhető el)|Darabszám|Összesen|A Windows Live nem érhető el.|Nincsenek méretek|
|kimenő. wns. wrongtoken|Igen|WNS-hitelesítési hibák (hibás token)|Darabszám|Összesen|A WNS számára megadott jogkivonat érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a regisztráció regisztrációban szereplő URI egy másik alkalmazáshoz van társítva. Győződjön meg arról, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van társítva, amelynek a hitelesítő adatai az értesítési központban vannak.|Nincsenek méretek|
|regisztráció. All|Igen|Regisztrációs műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs művelet (létrehozás, frissítések lekérdezése és törlése) száma. |Nincsenek méretek|
|regisztráció. Create|Igen|Regisztráció-létrehozási műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs létrehozás száma.|Nincsenek méretek|
|regisztráció. Delete|Igen|Regisztrációs törlési műveletek|Darabszám|Összesen|A regisztráció sikeres törléseinak száma.|Nincsenek méretek|
|regisztráció. Get|Igen|Regisztrálási olvasási műveletek|Darabszám|Összesen|A sikeres regisztrációs lekérdezések száma.|Nincsenek méretek|
|regisztráció. frissítés|Igen|Regisztrációs frissítési műveletek|Darabszám|Összesen|A sikeres regisztrációs frissítések száma.|Nincsenek méretek|
|ütemezett. függőben|Igen|Függőben lévő ütemezett értesítések|Darabszám|Összesen|Függőben lévő ütemezett értesítések|Nincsenek méretek|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/munkaterületek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre álló memória Average_%-ban|Igen|Rendelkezésre álló memória%-ban|Darabszám|Átlag|Rendelkezésre álló memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ rendelkezésre álló szabad hely a lapozófájlban|Igen|Rendelkezésre álló swap-terület (%)|Darabszám|Átlag|Average_ rendelkezésre álló szabad hely a lapozófájlban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os előjegyzett bájtok használatban|Igen|Előjegyzett memória%-ban használatban|Darabszám|Átlag|Average_%-os előjegyzett bájtok használatban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% DPC idő|Igen|% DPC idő|Darabszám|Átlag|Average_% DPC idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad inode (%)|Igen|Szabad inode%-ban|Darabszám|Átlag|Average_ szabad inode (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ szabad terület (%)|Igen|Szabad terület (%)|Darabszám|Átlag|Average_ szabad terület (%)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ üresjárati idő%-ban|Igen|Üresjáratban eltöltött időhányad (%)|Darabszám|Átlag|Average_ üresjárati idő%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os megszakítási idő|Igen|Megszakítási idő%-ban|Darabszám|Átlag|Average_%-os megszakítási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_% IO várakozási idő|Igen|I/o várakozási idő%-ban|Darabszám|Átlag|Average_% IO várakozási idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os szép idő|Igen|% Nice idő|Darabszám|Átlag|Average_%-os szép idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os privilegizált idő|Igen|%-Os privilegizált idő|Darabszám|Átlag|Average_%-os privilegizált idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ processzoridő|Igen|A processzor kihasználtsága (%)|Darabszám|Átlag|Average_ processzoridő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ használt inode|Igen|Felhasznált inode%-ban|Darabszám|Átlag|Average_ használt inode|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Felhasznált memória Average_%-ban|Igen|Felhasznált memória (%)|Darabszám|Átlag|Felhasznált memória Average_%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált terület%-ban|Igen|Felhasznált terület (%)|Darabszám|Átlag|Average_ felhasznált terület%-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_ felhasznált lapozófájl-terület|Igen|Felhasznált swap-terület%-ban|Darabszám|Átlag|Average_ felhasznált lapozófájl-terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_%-os felhasználói idő|Igen|Felhasználói idő%-ban|Darabszám|Átlag|Average_%-os felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban|Igen|Rendelkezésre álló memória (megabájt)|Darabszám|Átlag|Average_Available MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB memória|Igen|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Available MB-ban való swap|Igen|Rendelkezésre álló memória (MB)|Darabszám|Átlag|Average_Available MB-ban való swap|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Igen|Átlagos írási idő (mp/olvasás)|Darabszám|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. Lemez (mp/átvitel)|Igen|Átlagos műveleti idő (mp/átvitel)|Darabszám|Átlag|Average_Avg. Lemez (mp/átvitel)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Igen|Átlagos írási idő (mp/írás)|Darabszám|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Bytes/mp|Igen|Fogadott bájtok/s|Darabszám|Átlag|Fogadott Average_Bytes/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Küldési Average_Bytes másodpercenként|Igen|Küldési sebesség (bájt/s)|Darabszám|Átlag|Küldési Average_Bytes másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Bytes összesen/mp|Igen|Összes bájt/mp|Darabszám|Átlag|Average_Bytes összesen/mp|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Current a lemez várólistájának hossza|Igen|Lemez aktuális várólistájának hossza|Darabszám|Átlag|Average_Current a lemez várólistájának hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási sebesség (bájt/s) Average_Disk|Igen|Lemez olvasási sebessége (bájt/s)|Darabszám|Átlag|Olvasási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Disk másodpercenként|Igen|Olvasási sebesség (lemez/mp)|Darabszám|Átlag|Olvasási Average_Disk másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Disk adatátvitel másodpercenként|Igen|Lemez átvitele másodpercenként|Darabszám|Átlag|Average_Disk adatátvitel másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írási sebesség (bájt/s) Average_Disk|Igen|Lemez írási sebessége (bájt/s)|Darabszám|Átlag|Írási sebesség (bájt/s) Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Disk|Igen|Írási sebesség (írás/mp)|Darabszám|Átlag|Írás/mp Average_Disk|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free megabájt|Igen|Szabad terület (MB)|Darabszám|Átlag|Average_Free megabájt|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fizikai memória Average_Free|Igen|Szabad fizikai memória|Darabszám|Átlag|Fizikai memória Average_Free|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free lemezterület a Lapozófájlokban|Igen|Szabad terület a Lapozófájlokban|Darabszám|Átlag|Average_Free lemezterület a Lapozófájlokban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Free virtuális memória|Igen|Szabad virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Logical lemez sebessége (bájt/s)|Igen|Logikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Logical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Olvasási Average_Page másodpercenként|Igen|Olvasott lap/mp|Darabszám|Átlag|Olvasási Average_Page másodpercenként|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Írás/mp Average_Page|Igen|Írási idő/mp|Darabszám|Átlag|Írás/mp Average_Page|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pages előállított/másodperc|Igen|Lap/mp|Darabszám|Átlag|Average_Pages előállított/másodperc|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct emelt szintű idő|Igen|PCT rendszerjogosultságú idő|Darabszám|Átlag|Average_Pct emelt szintű idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Pct felhasználói idő|Igen|PCT felhasználói idő|Darabszám|Átlag|Average_Pct felhasználói idő|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Physical lemez sebessége (bájt/s)|Igen|Fizikai lemez sebessége (bájt/s)|Darabszám|Átlag|Average_Physical lemez sebessége (bájt/s)|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processes|Igen|Folyamatok|Darabszám|Átlag|Average_Processes|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Processor várólista hossza|Igen|Processzor-várólista hossza|Darabszám|Átlag|Average_Processor várólista hossza|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Lapozófájlokban tárolt Average_Size|Igen|Lapozófájlokban tárolt méret|Darabszám|Átlag|Lapozófájlokban tárolt Average_Size|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total bájtok|Igen|Bájtok összesen|Darabszám|Átlag|Average_Total bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Fogadott Average_Total bájtok száma|Igen|Fogadott bájtok összesen|Darabszám|Átlag|Fogadott Average_Total bájtok száma|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított bájtok|Igen|Továbbított bájtok összesen|Darabszám|Átlag|Average_Total továbbított bájtok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Ütközések Average_Total|Igen|Ütközések összesen|Darabszám|Átlag|Ütközések Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total fogadott csomagok|Igen|Fogadott csomagok összesen|Darabszám|Átlag|Average_Total fogadott csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total továbbított csomagok|Igen|Továbbított csomagok összesen|Darabszám|Átlag|Average_Total továbbított csomagok|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Rx-hibák Average_Total|Igen|Rx-hibák összesen|Darabszám|Átlag|Rx-hibák Average_Total|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Total TX-hibák|Igen|TX-hibák összesen|Darabszám|Átlag|Average_Total TX-hibák|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Uptime|Igen|Üzemidő|Darabszám|Átlag|Average_Uptime|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used MB-ban felcserélt terület|Igen|Felhasznált memória (MB) – lapozófájl|Darabszám|Átlag|Average_Used MB-ban felcserélt terület|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória kilobájtban|Igen|Felhasznált memória (kilobájt)|Darabszám|Átlag|Average_Used memória kilobájtban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Used memória MB-ban|Igen|Felhasznált memória (MB)|Darabszám|Átlag|Average_Used memória MB-ban|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Users|Igen|Felhasználók|Darabszám|Átlag|Average_Users|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Average_Virtual megosztott memória|Igen|Virtuális megosztott memória|Darabszám|Átlag|Average_Virtual megosztott memória|Számítógép, ObjectName, példánynév, CounterPath, SourceSystem|
|Esemény|Igen|Esemény|Darabszám|Átlag|Esemény|Forrás, eseménynapló, számítógép, EventCategory, EventLevel, EventLevelName, Napszállta|
|Szívverés|Igen|Szívverés|Darabszám|Összesen|Szívverés|Számítógép, OSType, verzió, SourceComputerId|
|Frissítés|Igen|Frissítés|Darabszám|Átlag|Frissítés|Számítógép, termék, besorolás, UpdateState, nem kötelező, jóváhagyott|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/társaik

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Igen|Kimenő forgalom aránya|BitsPerSecond|Átlag|Kimenő forgalom sebessége (bit/mp)|ConnectionId|
|IngressTrafficRate|Igen|Bejövő forgalom aránya|BitsPerSecond|Átlag|Bejövő forgalom sebessége bit/másodpercben|ConnectionId|
|SessionAvailabilityV4|Igen|Munkamenet rendelkezésre állása v4|Százalék|Átlag|A v4-munkamenet rendelkezésre állása|ConnectionId|
|SessionAvailabilityV6|Igen|Munkamenet rendelkezésre állása v6|Százalék|Átlag|A V6-munkamenet rendelkezésre állása|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PrefixLatency|Igen|Előtag-késés|Ezredmásodpercben|Átlag|Medián előtag késése|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|memory_metric|Igen|Memória|Bájt|Átlag|Memory. 0-3 GB-os tartomány a1, 0-5 GB az a2-hez, 0-10 GB az a3-hoz, 0-25 GB a4-hez, 0-50 GB az A5-hez és az A6-os 0-100 GB-hoz|Nincsenek méretek|
|memory_thrashing_metric|Igen|Memória-Kiverés (adathalmazok)|Százalék|Átlag|Memória átlagos kiverése.|Nincsenek méretek|
|qpu_high_utilization_metric|Igen|QPU magas kihasználtság|Darabszám|Összesen|QPU magas kihasználtsága az elmúlt percben, 1 a magas QPU-kihasználtság érdekében, máskülönben 0|Nincsenek méretek|
|QueryDuration|Igen|Lekérdezés időtartama (adatkészletek)|Ezredmásodpercben|Átlag|DAX-lekérdezés időtartama az utolsó intervallumban|Nincsenek méretek|
|QueryPoolJobQueueLength|Igen|Lekérdezési készlet nyomtatási várólistájának hossza (adatkészletek)|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|Nincsenek méretek|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|Nem|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft. Relay aktív kapcsolatai összesen.|EntityName|
|ActiveListeners|Nem|ActiveListeners|Darabszám|Összesen|A Microsoft. Relay ActiveListeners összesen.|EntityName|
|BytesTransferred|Igen|BytesTransferred|Darabszám|Összesen|A Microsoft. Relay BytesTransferred összesen.|EntityName|
|ListenerConnections-ClientError|Nem|ListenerConnections-ClientError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra vonatkozó ügyfélhibái.|EntityName |
|ListenerConnections-ServerError|Nem|ListenerConnections-ServerError|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra ServerError.|EntityName |
|ListenerConnections-Success|Nem|ListenerConnections-Success|Darabszám|Összesen|A Microsoft. Relay sikeres Figyelőkapcsolatokra.|EntityName |
|ListenerConnections-TotalRequests|Nem|ListenerConnections-TotalRequests|Darabszám|Összesen|A Microsoft. Relay Figyelőkapcsolatokra összesen.|EntityName|
|Bontásai|Nem|Bontásai|Darabszám|Összesen|A Microsoft. Relay bontásai összesen.|EntityName|
|SenderConnections-ClientError|Nem|SenderConnections-ClientError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra vonatkozó ügyfélhibái.|EntityName |
|SenderConnections-ServerError|Nem|SenderConnections-ServerError|Darabszám|Összesen|A Microsoft. Relay feladói kapcsolatokra ServerError.|EntityName |
|SenderConnections-Success|Nem|SenderConnections-Success|Darabszám|Összesen|A Microsoft. Relay sikeres feladói kapcsolatokra.|EntityName |
|SenderConnections-TotalRequests|Nem|SenderConnections-TotalRequests|Darabszám|Összesen|A Microsoft. Relay összes feladói kapcsolatokra-kérelme.|EntityName|
|SenderDisconnects|Nem|SenderDisconnects|Darabszám|Összesen|A Microsoft. Relay SenderDisconnects összesen.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|SearchLatency|Igen|Keresési késés|Másodperc|Átlag|Keresési szolgáltatás átlagos keresési késése|Nincsenek méretek|
|SearchQueriesPerSecond|Igen|Keresési lekérdezések másodpercenként|CountPerSecond|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatáshoz|Nincsenek méretek|
|ThrottledSearchQueriesPercentage|Igen|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatás számára szabályozott keresési lekérdezések százalékos aránya|Nincsenek méretek|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Aktív kapcsolatok|Nem|Aktív kapcsolatok|Darabszám|Összesen|A Microsoft. ServiceBus összes aktív kapcsolata.|Nincsenek méretek|
|ActiveMessages|Nem|Üzenetsor vagy témakör aktív üzeneteinek száma.|Darabszám|Átlag|Üzenetsor vagy témakör aktív üzeneteinek száma.|EntityName|
|ConnectionsClosed|Nem|Lezárt kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus lezárt kapcsolatainak száma.|EntityName|
|ConnectionsOpened|Nem|Megnyitott kapcsolatok.|Darabszám|Átlag|A Microsoft. ServiceBus megnyitott kapcsolatainak száma.|EntityName|
|CPUXNS|Nem|CPU (elavult)|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája. Ez a metrika depricated. Használja helyette a CPU-metrikát (NamespaceCpuUsage).|Nincsenek méretek|
|DeadletteredMessages|Nem|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|Darabszám|Átlag|Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma.|EntityName|
|IncomingMessages|Igen|Bejövő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő üzenetei.|EntityName|
|IncomingRequests|Igen|Bejövő kérelmek|Darabszám|Összesen|A Microsoft. ServiceBus bejövő kérései.|EntityName|
|Üzenetek|Nem|Üzenetsor/téma üzeneteinek száma.|Darabszám|Átlag|Üzenetsor/téma üzeneteinek száma.|EntityName|
|NamespaceCpuUsage|Nem|CPU|Százalék|Maximum|A Service Bus Premium névtér CPU-használati metrikája.|Nincsenek méretek|
|NamespaceMemoryUsage|Nem|Memóriahasználat|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma.|Nincsenek méretek|
|OutgoingMessages|Igen|Kimenő üzenetek|Darabszám|Összesen|A Microsoft. ServiceBus kimenő üzenetei.|EntityName|
|ScheduledMessages|Nem|Az üzenetsor/témakör ütemezett üzeneteinek száma.|Darabszám|Átlag|Az üzenetsor/témakör ütemezett üzeneteinek száma.|EntityName|
|Kiszolgálóhibái|Nem|Kiszolgálóhibák.|Darabszám|Összesen|Kiszolgálói hibák a Microsoft. ServiceBus esetében.|EntityName |
|Méret|Nem|Méret|Bájt|Átlag|Várólista/témakör mérete bájtban.|EntityName|
|SuccessfulRequests|Nem|Sikeres kérelmek|Darabszám|Összesen|Névtér összes sikeres kérelme|EntityName |
|ThrottledRequests|Nem|Szabályozott kérelmek.|Darabszám|Összesen|A Microsoft. ServiceBus által szabályozott kérelmek.|EntityName |
|UserErrors|Nem|Felhasználói hibák.|Darabszám|Összesen|A Microsoft. ServiceBus felhasználói hibái.|EntityName |
|WSXNS|Nem|Memóriahasználat (elavult)|Százalék|Maximum|A Service Bus Premium névtér memóriahasználat mérőszáma. Ez a metrika elavult. Használja helyette a memóriahasználat (NamespaceMemoryUsage) metrikáját.|Nincsenek méretek|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/alkalmazások

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActualCpu|Nem|ActualCpu|Darabszám|Átlag|Tényleges CPU-használat a Millon magokban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ActualMemory|Nem|ActualMemory|Bájt|Átlag|Tényleges memóriahasználat MB-ban|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedCpu|Nem|AllocatedCpu|Darabszám|Átlag|A tárolóhoz a Millet magokban lefoglalt CPU|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|AllocatedMemory|Nem|AllocatedMemory|Bájt|Átlag|A tároló számára lefoglalt memória (MB)|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|ApplicationStatus|Nem|ApplicationStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazás állapota|ApplicationName, állapot|
|Tároló állapota:|Nem|Tároló állapota:|Darabszám|Átlag|Service Fabric Mesh-alkalmazás tárolójának állapota|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName, állapot|
|CpuUtilization|Nem|CpuUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|MemoryUtilization|Nem|MemoryUtilization|Százalék|Átlag|A tároló PROCESSZORának kihasználtsága a AllocatedCpu százalékaként|ApplicationName, szolgáltatásnév, CodePackageName, ServiceReplicaName|
|RestartCount|Nem|RestartCount|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található tároló újraindításainak száma|ApplicationName, status, szolgáltatásnév, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|Nem|ServiceReplicaStatus|Darabszám|Átlag|Service Fabric Mesh alkalmazásban található szolgáltatási replika állapota|ApplicationName, status, szolgáltatásnév, ServiceReplicaName|
|ServiceStatus|Nem|ServiceStatus|Darabszám|Átlag|Service Fabric Mesh-alkalmazásban található szolgáltatás állapotának állapota|ApplicationName, állapot, szolgáltatásnév|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ConnectionCount|Igen|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolatok mennyisége.|Végpont|
|InboundTraffic|Igen|Bejövő forgalom|Bájt|Összesen|A szolgáltatás bejövő forgalma|Nincsenek méretek|
|MessageCount|Igen|Üzenetek száma|Darabszám|Összesen|Az üzenetek teljes mennyisége.|Nincsenek méretek|
|OutboundTraffic|Igen|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|Nincsenek méretek|
|SystemErrors|Igen|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|Nincsenek méretek|
|UserErrors|Igen|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|Nincsenek méretek|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Igen|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|Nincsenek méretek|
|io_bytes_read|Igen|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|Nincsenek méretek|
|io_bytes_written|Igen|I/o-bájtok írása|Bájt|Átlag|I/o-bájtok írása|Nincsenek méretek|
|io_requests|Igen|IO-kérelmek száma|Darabszám|Átlag|IO-kérelmek száma|Nincsenek méretek|
|reserved_storage_mb|Igen|Tárterület fenntartva|Darabszám|Átlag|Tárterület fenntartva|Nincsenek méretek|
|storage_space_used_mb|Igen|Felhasznált tárterület|Darabszám|Átlag|Felhasznált tárterület|Nincsenek méretek|
|virtual_core_count|Igen|Virtuális mag száma|Darabszám|Átlag|Virtuális mag száma|Nincsenek méretek|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|active_queries|Igen|Aktív lekérdezések|Darabszám|Összesen|Aktív lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|allocated_data_storage|Igen|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adattárolás. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|app_cpu_billed|Igen|Az alkalmazás CPU-számlázása|Darabszám|Összesen|Az alkalmazás CPU-számlázása. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_cpu_percent|Igen|Alkalmazás CPU-aránya|Százalék|Átlag|Alkalmazás CPU-aránya (%) A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|app_memory_percent|Igen|Az alkalmazás memóriájának százaléka|Százalék|Átlag|Az alkalmazás memóriájának százalékos aránya. A kiszolgáló nélküli adatbázisokra vonatkozik.|Nincsenek méretek|
|base_blob_size_bytes|Igen|Az alap blob tárterületének mérete|Bájt|Maximum|Az alap blob tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|Nincsenek méretek|
|blocked_by_firewall|Igen|Tűzfal blokkolja|Darabszám|Összesen|Tűzfal blokkolja|Nincsenek méretek|
|cache_hit_percent|Igen|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|cache_used_percent|Igen|Gyorsítótár használt százaléka|Százalék|Maximum|Gyorsítótárban használt százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|connection_failed|Igen|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|Nincsenek méretek|
|connection_successful|Igen|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|Nincsenek méretek|
|cpu_limit|Igen|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|cpu_percent|Igen|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Igen|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|holtpont|Igen|Holtpontok|Darabszám|Összesen|Holtpontok. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|diff_backup_size_bytes|Igen|Különbözeti biztonsági másolatok tárolási mérete|Bájt|Maximum|Kumulatív különbözeti biztonsági másolati tárterület mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|dtu_consumption_percent|Igen|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_limit|Igen|DTU korlátja|Darabszám|Átlag|DTU korlátja. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dtu_used|Igen|Használt DTU|Darabszám|Átlag|DTU használatban. A DTU-alapú adatbázisokra vonatkozik.|Nincsenek méretek|
|dw_backup_size_gb|Igen|Adattárolás mérete (GB)|Darabszám|Összesen|Az adattárolási méret az adatai és a tranzakciónapló méretétől áll. A metrika a számla "Storage" részébe kerül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dw_geosnapshot_size_gb|Igen|Vész-helyreállítási tárterület mérete (GB)|Darabszám|Összesen|A vész-helyreállítási tároló mérete "vész-helyreállítási tárolóként" jelenik meg a számlán. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dw_snapshot_size_gb|Igen|Pillanatkép-tároló mérete (GB)|Darabszám|Összesen|A pillanatképek tárolási mérete a pillanatképek által a felhasználó által meghatározott és az Automatikus visszaállítási pontok létrehozásához rögzített növekményes módosítások mérete. A metrika a számla "Storage" részébe kerül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_consumption_percent|Igen|DWU százalékos aránya|Százalék|Maximum|DWU százalék. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_limit|Igen|DWU korlátja|Darabszám|Maximum|DWU korlátja. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|dwu_used|Igen|Használt DWU|Darabszám|Maximum|DWU használatban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|full_backup_size_bytes|Igen|Teljes biztonsági mentési tár mérete|Bájt|Maximum|Összesített teljes biztonsági mentési tár mérete. A virtuális mag-alapú adatbázisokra vonatkozik. Nem alkalmazható nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|local_tempdb_usage_percent|Igen|Helyi tempdb százalékos aránya|Százalék|Átlag|Helyi tempdb százalékban. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|log_backup_size_bytes|Igen|Napló biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő napló biztonsági mentési tárterületének mérete. A virtuális mag-alapú és a nagy kapacitású adatbázisokra vonatkozik.|Nincsenek méretek|
|log_write_percent|Igen|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|memory_usage_percent|Igen|Memória százaléka|Százalék|Maximum|Memória százalékos aránya Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|physical_data_read_percent|Igen|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|queued_queries|Igen|Várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések az összes munkaterhelési csoporton belül. Csak az adattárházak esetében érvényes.|Nincsenek méretek|
|sessions_percent|Igen|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|snapshot_backup_size_bytes|Igen|Pillanatkép biztonsági mentési tárterületének mérete|Bájt|Maximum|Összesítő pillanatkép biztonsági mentési tárterületének mérete. A nagy kapacitású-adatbázisokra vonatkozik.|Nincsenek méretek|
|sqlserver_process_core_percent|Igen|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|sqlserver_process_memory_percent|Igen|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage|Igen|Felhasznált adatterület|Bájt|Maximum|Felhasznált adatterület. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|storage_percent|Igen|Felhasznált adatterület százalékos aránya|Százalék|Maximum|Az adatterület felhasznált százaléka százalékban. Nem alkalmazható adattárházak vagy nagy kapacitású-adatbázisokra.|Nincsenek méretek|
|tempdb_data_size|Igen|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|Tempdb az adatfájl mérete kilobájtban. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|tempdb_log_size|Igen|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|Tempdb-naplófájl mérete (kilobájt) Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|tempdb_log_used_percent|Igen|Tempdb százalékos naplója használatban|Százalék|Maximum|A tempdb százalékos naplója használatban van. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|wlg_active_queries|Igen|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|Aktív lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Igen|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Azok a lekérdezések, amelyek túllépték a munkaterhelés-csoport időtúllépését. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Igen|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások a teljes rendszerterhelési csoporthoz viszonyított százalékos aránya. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Igen|Munkaterhelés-csoport lefoglalása a Cap-erőforrás százaléka alapján|Százalék|Maximum|Az erőforrások megadott százalékos aránya a munkaterhelési csoportok meghatározott erőforrásaihoz viszonyítva. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Igen|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport számára engedélyezett erőforrások százalékos arányának korlátozása, figyelembe véve a más munkaterhelés-csoportok számára kiosztott tényleges minimális erőforrás-százalékos arányt. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Igen|Effektív minimális erőforrás százaléka|Százalék|Maximum|A munkaterhelés-csoport számára fenntartott és elkülönített erőforrások minimális százalékos aránya, a szolgáltatási szint minimumának figyelembevételével. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Igen|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Várólistán lévő lekérdezések a munkaterhelés csoporton belül. Csak az adattárházak esetében érvényes.|WorkloadGroupName, IsUserDefined|
|workers_percent|Igen|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|
|xtp_storage_percent|Igen|In-Memory OLTP-tárolási százalék|Százalék|Átlag|In-Memory OLTP a tárolási százalékot. Az adattárházak esetében nem alkalmazható.|Nincsenek méretek|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/kiszolgálók/elasticPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|allocated_data_storage|Igen|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|Nincsenek méretek|
|allocated_data_storage_percent|Igen|Lefoglalt adatterület százalékos aránya|Százalék|Maximum|Lefoglalt adatterület százalékos aránya|Nincsenek méretek|
|cpu_limit|Igen|CPU-korlát|Darabszám|Átlag|CPU-korlát. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|cpu_percent|Igen|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Nincsenek méretek|
|cpu_used|Igen|Felhasznált CPU|Darabszám|Átlag|A processzor használatban van. A virtuális mag-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|database_allocated_data_storage|Nem|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|DatabaseResourceId|
|database_cpu_limit|Nem|CPU-korlát|Darabszám|Átlag|CPU-korlát|DatabaseResourceId|
|database_cpu_percent|Nem|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|DatabaseResourceId|
|database_cpu_used|Nem|Felhasznált CPU|Darabszám|Átlag|Felhasznált CPU|DatabaseResourceId|
|database_dtu_consumption_percent|Nem|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId|
|database_eDTU_used|Nem|használt eDTU|Darabszám|Átlag|használt eDTU|DatabaseResourceId|
|database_log_write_percent|Nem|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|DatabaseResourceId|
|database_physical_data_read_percent|Nem|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|DatabaseResourceId|
|database_sessions_percent|Nem|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|DatabaseResourceId|
|database_storage_used|Nem|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|DatabaseResourceId|
|database_workers_percent|Nem|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|DatabaseResourceId|
|dtu_consumption_percent|Igen|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_limit|Igen|eDTU korlátja|Darabszám|Átlag|eDTU korlátja. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|eDTU_used|Igen|használt eDTU|Darabszám|Átlag|eDTU használatban. A DTU-alapú rugalmas készletekre vonatkozik.|Nincsenek méretek|
|log_write_percent|Igen|Naplózási IO-százalék|Százalék|Átlag|Naplózási IO-százalék|Nincsenek méretek|
|physical_data_read_percent|Igen|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|Nincsenek méretek|
|sessions_percent|Igen|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|Nincsenek méretek|
|sqlserver_process_core_percent|Igen|SQL Server Process Core százalék|Százalék|Maximum|CPU-használat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|Nincsenek méretek|
|sqlserver_process_memory_percent|Igen|SQL Server feldolgozási memória százalékos aránya|Százalék|Maximum|Memóriahasználat az SQL DB-folyamat százalékaként. A rugalmas készletekre vonatkozik.|Nincsenek méretek|
|storage_limit|Igen|Maximális adatméret|Bájt|Átlag|Maximális adatméret|Nincsenek méretek|
|storage_percent|Igen|Felhasznált adatterület százalékos aránya|Százalék|Átlag|Felhasznált adatterület százalékos aránya|Nincsenek méretek|
|storage_used|Igen|Felhasznált adatterület|Bájt|Átlag|Felhasznált adatterület|Nincsenek méretek|
|tempdb_data_size|Igen|Tempdb adatfájl mérete (kilobájt)|Darabszám|Maximum|Tempdb adatfájl mérete (kilobájt)|Nincsenek méretek|
|tempdb_log_size|Igen|Tempdb-naplófájl mérete (kilobájt)|Darabszám|Maximum|Tempdb-naplófájl mérete (kilobájt)|Nincsenek méretek|
|tempdb_log_used_percent|Igen|Tempdb százalékos naplója használatban|Százalék|Maximum|Tempdb százalékos naplója használatban|Nincsenek méretek|
|workers_percent|Igen|Munkavégzők százalékos aránya|Százalék|Átlag|Munkavégzők százalékos aránya|Nincsenek méretek|
|xtp_storage_percent|Igen|In-Memory OLTP-tárolási százalék|Százalék|Átlag|In-Memory OLTP-tárolási százalék|Nincsenek méretek|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|
|UsedCapacity|Nem|Felhasznált kapacitás|Bájt|Átlag|A Storage-fiók által használt tárterület mennyisége. Standard szintű tárfiókok esetében ez a blob, a tábla, a fájl és a várólista által használt kapacitás összege. A Premium Storage-fiókok és a blob Storage-fiókok esetében ugyanaz, mint a BlobCapacity vagy a FileCapacity.|Nincsenek méretek|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|BlobCapacity|Nem|BLOB kapacitása|Bájt|Átlag|A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve.|BlobType, szintű|
|BlobCount|Nem|Blobok száma|Darabszám|Átlag|A Storage-fiókban tárolt blob-objektumok száma.|BlobType, szintű|
|BlobProvisionedSize|Nem|BLOB kiépített mérete|Bájt|Átlag|A Storage-fiók Blob serviceban kiépített tárterület mérete bájtban megadva.|BlobType, szintű|
|ContainerCount|Igen|BLOB-tárolók száma|Darabszám|Átlag|A Storage-fiókban lévő tárolók száma.|Nincsenek méretek|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|IndexCapacity|Nem|Index kapacitása|Bájt|Átlag|A Azure Data Lake Storage Gen2 hierarchikus index által használt tárterület mennyisége.|Nincsenek méretek|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|FileCapacity|Nem|Fájl kapacitása|Bájt|Átlag|A Storage-fiók által használt file Storage mennyisége.|Fájlmegosztás|
|FileCount|Nem|Fájlok száma|Darabszám|Átlag|A Storage-fiókban található fájlok száma.|Fájlmegosztás|
|FileShareCapacityQuota|Nem|Fájlmegosztás kapacitásának kvótája|Bájt|Átlag|A Azure Files szolgáltatás által a bájtokban felhasználható tárterület felső korlátja.|Fájlmegosztás|
|FileShareCount|Nem|Fájlmegosztás száma|Darabszám|Átlag|A Storage-fiókban lévő fájlmegosztás száma.|Nincsenek méretek|
|FileShareProvisionedIOPS|Nem|Fájlmegosztási kiépített IOPS|Bájt|Átlag|A Premium file Storage-fiókban a prémium fájlmegosztás kiépített IOPS alapszáma. Ezt a számot a megosztási kapacitás kiosztott mérete (kvóta) alapján számítjuk ki.|Fájlmegosztás|
|FileShareSnapshotCount|Nem|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A Storage-fiók Files szolgáltatásában lévő megosztásban található Pillanatképek száma.|Fájlmegosztás|
|FileShareSnapshotSize|Nem|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a Storage-fiók Fájlszolgáltatások szolgáltatásában használt tárterület mérete bájtban megadva.|Fájlmegosztás|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés, fájlmegosztás|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés, fájlmegosztás|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|QueueCapacity|Igen|Várólista kapacitása|Bájt|Átlag|A Storage-fiók által használt üzenetsor-tároló mennyisége.|Nincsenek méretek|
|QueueCount|Igen|Várólista száma|Darabszám|Átlag|A Storage-fiókban lévő várólisták száma.|Nincsenek méretek|
|QueueMessageCount|Igen|Üzenetsor-üzenetek száma|Darabszám|Átlag|A nem lejárt üzenetsor-üzenetek száma a Storage-fiókban.|Nincsenek méretek|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Rendelkezésre állás|Igen|Rendelkezésre állás|Százalék|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|GeoType, ApiName, hitelesítés|
|Kimenő forgalom|Igen|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége. Ez a szám magában foglalja az Azure Storage külső ügyfelének, valamint az Azure-on belüli kimenő ügyfeleknek. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|GeoType, ApiName, hitelesítés|
|Bejövő forgalom|Igen|Bejövő forgalom|Bájt|Összesen|A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|GeoType, ApiName, hitelesítés|
|SuccessE2ELatency|Igen|Sikeres kérések végpontok közötti késése|Ezredmásodpercben|Átlag|A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|GeoType, ApiName, hitelesítés|
|SuccessServerLatency|Igen|Sikerességi kiszolgáló késése|Ezredmásodpercben|Átlag|Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|GeoType, ApiName, hitelesítés|
|TableCapacity|Igen|Tábla kapacitása|Bájt|Átlag|A Storage-fiók által használt Table Storage mennyisége.|Nincsenek méretek|
|TableCount|Igen|Táblák száma|Darabszám|Átlag|A Storage-fiókban lévő táblák száma.|Nincsenek méretek|
|TableEntityCount|Igen|Tábla entitások száma|Darabszám|Átlag|A Storage-fiókban lévő tábla entitások száma.|Nincsenek méretek|
|Tranzakciók|Igen|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. Használjon ResponseType dimenziót a különböző típusú válaszok számára.|ResponseType, GeoType, ApiName, hitelesítés|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Igen|Szinkronizálási munkamenet eredménye|Darabszám|Átlag|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a kiszolgálói végpont sikeresen befejezte a szinkronizálási munkamenetet a Felhőbeli végponttal|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Igen|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Igen|Felhőbeli rétegek felidézésének mérete alkalmazás szerint|Bájt|Összesen|Az alkalmazás által visszahívott adatmennyiség|SyncGroupName, kiszolgálónév, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Igen|Felhőbeli rétegek felidézésének mérete|Bájt|Összesen|Visszahívott adatmennyiség|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Igen|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Igen|Felhőbeli rétegek felidézésének átviteli sebessége|BytesPerSecond|Átlag|Adatvisszahívási sebesség mérete|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Igen|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Igen|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Igen|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Igen|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely minden alkalommal naplózza az 1 értéket, amikor a resigtered-kiszolgáló sikeresen megjegyez egy szívverést a Felhőbeli végponttal|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Igen|Felhőbeli rétegek felidézése|Bájt|Összesen|A kiszolgáló által visszaadott adatmennyiség teljes mérete|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Igen|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Igen|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Igen|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Igen|Szinkronizált bájtok száma|Bájt|Összesen|A szinkronizálási munkamenetek számára átvitt fájlok teljes mérete|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Igen|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Igen|Nem szinkronizált fájlok|Darabszám|Összesen|Nem sikerült szinkronizálni a fájlok számát|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Igen|Sikertelen függvények kérései|Darabszám|Összesen|Sikertelen függvények kérései|LogicalName, PartitionId|
|AMLCalloutInputEvents|Igen|Függvények eseményei|Darabszám|Összesen|Függvények eseményei|LogicalName, PartitionId|
|AMLCalloutRequests|Igen|Függvények kérései|Darabszám|Összesen|Függvények kérései|LogicalName, PartitionId|
|ConversionErrors|Igen|Adatátalakítási hibák|Darabszám|Összesen|Adatátalakítási hibák|LogicalName, PartitionId|
|DeserializationError|Igen|Bemeneti deszerializálási hibák|Darabszám|Összesen|Bemeneti deszerializálási hibák|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Igen|Megrendelési események|Darabszám|Összesen|Megrendelési események|LogicalName, PartitionId|
|EarlyInputEvents|Igen|Korai bemeneti események|Darabszám|Összesen|Korai bemeneti események|LogicalName, PartitionId|
|Hibák|Igen|Futásidejű hibák|Darabszám|Összesen|Futásidejű hibák|LogicalName, PartitionId|
|InputEventBytes|Igen|Bemeneti esemény bájtjai|Bájt|Összesen|Bemeneti esemény bájtjai|LogicalName, PartitionId|
|InputEvents|Igen|Bemeneti események|Darabszám|Összesen|Bemeneti események|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Igen|Várakozó bemeneti eseményei|Darabszám|Maximum|Várakozó bemeneti eseményei|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Igen|Fogadott bemeneti források|Darabszám|Összesen|Fogadott bemeneti források|LogicalName, PartitionId|
|LateInputEvents|Igen|Késői bemeneti események|Darabszám|Összesen|Késői bemeneti események|LogicalName, PartitionId|
|OutputEvents|Igen|Kimeneti események|Darabszám|Összesen|Kimeneti események|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Igen|Vízjel késleltetése|Másodperc|Maximum|Vízjel késleltetése|LogicalName, PartitionId|
|ResourceUtilization|Igen|SU% kihasználtsága|Százalék|Maximum|SU% kihasználtsága|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. szinapszis/munkaterületek

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|Nem|A tevékenység futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési tevékenységek száma|Eredmény, FailureType, tevékenység, ActivityType, folyamat|
|OrchestrationPipelineRunsEnded|Nem|A folyamat futtatása befejeződött|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési folyamat-futtatások száma|Eredmény, FailureType, folyamat|
|OrchestrationTriggersEnded|Nem|Befejezett eseményindítók|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított előkészítési eseményindítók száma|Eredmény, FailureType, trigger|
|SQLOnDemandLoginAttempts|Nem|Bejelentkezési kísérletek|Darabszám|Összesen|A sikerül vagy sikertelen bejelentkezési kísérletek száma|Eredmény|
|SQLOnDemandQueriesEnded|Nem|A lekérdezések véget ért|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított lekérdezések száma|Eredmény|
|SQLOnDemandQueryProcessedBytes|Nem|Feldolgozott adatok|Bájt|Összesen|Lekérdezések által feldolgozott adatmennyiség|Nincsenek méretek|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. szinapszis/munkaterületek/bigDataPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CoresCapacity|Nem|Magok kapacitása|Darabszám|Maximum|Magok kapacitása|Nincsenek méretek|
|MemoryCapacityGB|Nem|Memória kapacitása (GB)|Darabszám|Maximum|Memória kapacitása (GB)|Nincsenek méretek|
|SparkJobsEnded|Igen|Befejezett alkalmazások|Darabszám|Összesen|Befejezett alkalmazások száma|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. szinapszis/munkaterületek/sqlPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|Nem|Adaptív gyorsítótár-találatok százalékos aránya|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár találati százalékos metrikájának használatával határozhatja meg, hogy szeretné-e bővíteni a további kapacitást, vagy futtassa újra a számítási feladatokat a gyorsítótár hidrát|Nincsenek méretek|
|AdaptiveCacheUsedPercent|Nem|Adaptív gyorsítótár használt százaléka|Százalék|Maximum|Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár használt százalékos metrikájának használatával határozhatja meg, hogy a további kapacitást szeretné-e méretezni, vagy újra kell futtatnia a számítási feladatokat a gyorsítótár hidrát|Nincsenek méretek|
|Kapcsolatok|Igen|Kapcsolatok|Darabszám|Összesen|Az SQL-készletbe való teljes bejelentkezések száma|Eredmény|
|ConnectionsBlockedByFirewall|Nem|Tűzfal által letiltott kapcsolatok|Darabszám|Összesen|A tűzfalszabályok által letiltott kapcsolatok száma. Nyissa meg újra az SQL-készlethez tartozó hozzáférés-vezérlési szabályzatokat, és figyelje meg, hogy a kapcsolatok száma magas-e|Nincsenek méretek|
|DWULimit|Nem|DWU korlátja|Darabszám|Maximum|Az SQL-készlet szolgáltatási szintjének célkitűzése|Nincsenek méretek|
|DWUUsed|Nem|Használt DWU|Darabszám|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. DWU-korláttal mérve * DWU százalék|Nincsenek méretek|
|DWUUsedPercent|Nem|DWU használt százalék|Százalék|Maximum|Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. A CPU-százalék és az adatio-százalék közötti maximális érték alapján mérhető|Nincsenek méretek|
|LocalTempDBUsedPercent|Nem|Helyi tempdb használt százalék|Százalék|Maximum|Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva|Nincsenek méretek|
|MemoryUsedPercent|Nem|Felhasznált memória százalékos aránya|Százalék|Maximum|Memória kihasználtsága az SQL-készlet összes csomópontján|Nincsenek méretek|
|wlg_effective_min_resource_percent|Igen|Effektív minimális erőforrás százaléka|Százalék|Minimális|A tényleges minimális erőforrás-százalékos beállítás a szolgáltatási szint és a munkaterhelés-csoport beállításainak figyelembevételével engedélyezett. A tényleges min_percentage_resource magasabb szinten állítható be alacsonyabb szolgáltatási szinteken|IsUserDefined, WorkloadGroup|
|WLGActiveQueries|Nem|Munkaterhelési csoport aktív lekérdezései|Darabszám|Összesen|A munkaterhelés csoporton belüli aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik.|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|Nem|Munkaterhelés-csoport lekérdezési időtúllépései|Darabszám|Összesen|Túllépte az időkorlátot tartalmazó munkaterhelési csoport lekérdezéseit. A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának megkezdése után (nem tartalmazza a várakozási időt a zárolás vagy az erőforrás-várakozás miatt)|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Nem|Munkaterhelési csoport lefoglalása az erőforrás maximális százaléka alapján|Százalék|Maximum|Megjeleníti az erőforrások százalékos kiosztását a munkaterhelési csoport tényleges maximális erőforrásához viszonyítva. Ez a mérőszám a munkaterhelés-csoport hatékony kihasználtságát biztosítja|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|Nem|Munkaterhelés-csoport kiosztása rendszerszázalékkal|Százalék|Maximum|Az erőforrások százalékos kiosztása a teljes rendszerrel viszonyítva|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Igen|Érvényes Cap-erőforrás százaléka|Százalék|Maximum|A munkaterhelési csoport tényleges Cap-erőforrásának százaléka. Ha más munkaterhelés-csoportok min_percentage_resource > 0, a effective_cap_percentage_resource arányosan csökken|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|Nem|Munkaterhelési csoport várólistán lévő lekérdezések|Darabszám|Összesen|Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/Environments

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Igen|Bejövő fogadott bájtok száma|Bájt|Összesen|Az összes eseményforrás által olvasott bájtok száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|Igen|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedMessages|Igen|Bejövő fogadott üzenetek|Darabszám|Összesen|Az összes Event hub-vagy IoT hub-eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Igen|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Igen|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressStoredBytes|Igen|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Igen|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|WarmStorageMaxProperties|Igen|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Igen|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Igen|Bejövő fogadott bájtok száma|Bájt|Összesen|Az eseményforrás által beolvasott bájtok száma|Nincsenek méretek|
|IngressReceivedInvalidMessages|Igen|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az eseményforrás által beolvasott érvénytelen üzenetek száma|Nincsenek méretek|
|IngressReceivedMessages|Igen|Bejövő fogadott üzenetek|Darabszám|Összesen|Az eseményforrás által olvasott üzenetek száma|Nincsenek méretek|
|IngressReceivedMessagesCountLag|Igen|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|Nincsenek méretek|
|IngressReceivedMessagesTimeLag|Igen|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|Nincsenek méretek|
|IngressStoredBytes|Igen|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|Nincsenek méretek|
|IngressStoredEvents|Igen|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|Nincsenek méretek|
|WarmStorageMaxProperties|Igen|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|Nincsenek méretek|
|WarmStorageUsedProperties|Igen|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|Nincsenek méretek|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|Lemez olvasási bájtjai|Igen|Lemez olvasási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|Lemez olvasási művelete/mp|Igen|Lemez olvasási művelete/mp|CountPerSecond|Átlag|Az i/o-olvasási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|Lemez írási bájtjai|Igen|Lemez írási bájtjai|Bájt|Összesen|A lemez teljes átviteli sebessége a mintavételi időszakon keresztüli írási műveletek miatt.|Nincsenek méretek|
|Lemez írási műveletei/mp|Igen|Lemez írási műveletei/mp|CountPerSecond|Átlag|Az IO-írási műveletek átlagos száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskReadBytesPerSecond|Igen|Lemez olvasási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége a mintavételi időszakon belül beolvasási műveletek miatt.|Nincsenek méretek|
|DiskReadLatency|Igen|Lemez olvasási késése|Ezredmásodpercben|Átlag|Olvasási késés összesen Az eszköz és a kernel olvasási késésének összege.|Nincsenek méretek|
|DiskReadOperations|Igen|Lemezes olvasási műveletek|Darabszám|Összesen|Az i/o-olvasási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|DiskWriteBytesPerSecond|Igen|Lemez írási sebessége (bájt/s)|BytesPerSecond|Átlag|A lemez átlagos átviteli sebessége írási műveletek miatt a mintavételi időszakon belül.|Nincsenek méretek|
|DiskWriteLatency|Igen|Lemez írási késése|Ezredmásodpercben|Átlag|Írási késleltetés összesen Az eszköz és a kernel írási késésének összege.|Nincsenek méretek|
|DiskWriteOperations|Igen|Lemez írási műveletei|Darabszám|Összesen|Az i/o-írási műveletek száma az előző mintavételi időszakban. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|Nincsenek méretek|
|MemoryActive|Igen|Memória aktív|Bájt|Átlag|A virtuális gép által az elmúlt kis időszakban használt memória mennyisége. Ez a "true" (igaz) szám, amely szerint a virtuális gépnek mennyi memóriára van szüksége. Előfordulhat, hogy a használaton kívüli memóriát felcserélték, vagy a vendég teljesítményére nincs hatással.|Nincsenek méretek|
|MemoryGranted|Igen|Megadott memória|Bájt|Átlag|A gazdagép által a virtuális géphez megadott memória mennyisége. A rendszer mindaddig nem kap memóriát a gazdagép számára, amíg egyszer meg nem éri az adott időt, és a memóriában megadhatja a memóriát, ha a VMkernel szüksége van a memóriára.|Nincsenek méretek|
|MemoryUsed|Igen|Felhasznált memória|Bájt|Átlag|A virtuális gép által használt gépi memória mennyisége.|Nincsenek méretek|
|Bejövő hálózat|Igen|Bejövő hálózat|Bájt|Összesen|A fogadott forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|Kimenő hálózat|Igen|Kimenő hálózat|Bájt|Összesen|A továbbított forgalom összes hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkInBytesPerSecond|Igen|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|NetworkOutBytesPerSecond|Igen|Hálózati kimenő bájtok/s|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli sebessége.|Nincsenek méretek|
|Százalékos processzorhasználat|Igen|Százalékos processzorhasználat|Százalék|Átlag|A CPU kihasználtsága. Ezt az értéket a rendszer 100%-os jelentéssel jelenti, amely az összes processzor-magot jelképezi a rendszeren. Például egy négy Magos rendszer 50%-át használó 2 irányú virtuális gép teljesen két magot használ.|Nincsenek méretek|
|PercentageCpuReady|Igen|CPU-készültség százalékos aránya|Ezredmásodpercben|Összesen|A készenléti idő az az idő, ameddig a processzor (ok) a korábbi frissítési időközben elérhetővé válik.|Nincsenek méretek|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|ActiveRequests|Igen|Aktív kérések|Darabszám|Összesen|Aktív kérések|Példány|
|AverageResponseTime|Igen|Átlagos válaszidő|Másodperc|Átlag|Átlagos válaszidő|Példány|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CpuPercentage|Igen|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|DiskQueueLength|Igen|Lemezvezérlő-várólista hossza|Darabszám|Átlag|Lemezvezérlő-várólista hossza|Példány|
|Http101|Igen|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Igen|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Igen|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|Igen|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Igen|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Igen|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Igen|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Igen|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Igen|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|HttpQueueLength|Igen|Http-várólista hossza|Darabszám|Átlag|Http-várólista hossza|Példány|
|LargeAppServicePlanInstances|Igen|Nagy App Service-csomag feldolgozói|Darabszám|Átlag|Nagy App Service-csomag feldolgozói|Nincsenek méretek|
|MediumAppServicePlanInstances|Igen|Közepes App Service-csomag feldolgozói|Darabszám|Átlag|Közepes App Service-csomag feldolgozói|Nincsenek méretek|
|MemoryPercentage|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|SmallAppServicePlanInstances|Igen|Kisméretű App Service-csomag feldolgozói|Darabszám|Átlag|Kisméretű App Service-csomag feldolgozói|Nincsenek méretek|
|TotalFrontEnds|Igen|Összes előtér|Darabszám|Átlag|Összes előtér|Nincsenek méretek|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|CpuPercentage|Igen|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|MemoryPercentage|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|WorkersAvailable|Igen|Rendelkezésre álló munkavégzők|Darabszám|Átlag|Rendelkezésre álló munkavégzők|Nincsenek méretek|
|WorkersTotal|Igen|Alkalmazottak összesen|Darabszám|Átlag|Alkalmazottak összesen|Nincsenek méretek|
|WorkersUsed|Igen|Használt feldolgozók|Darabszám|Átlag|Használt feldolgozók|Nincsenek méretek|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/kiszolgálófarmok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CpuPercentage|Igen|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|DiskQueueLength|Igen|Lemezvezérlő-várólista hossza|Darabszám|Átlag|Lemezvezérlő-várólista hossza|Példány|
|HttpQueueLength|Igen|Http-várólista hossza|Darabszám|Átlag|Http-várólista hossza|Példány|
|MemoryPercentage|Igen|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|SocketInboundAll|Igen|SocketInboundAll|Darabszám|Átlag|SocketInboundAll|Példány|
|SocketLoopback|Igen|SocketLoopback|Darabszám|Átlag|SocketLoopback|Példány|
|SocketOutboundAll|Igen|SocketOutboundAll|Darabszám|Átlag|SocketOutboundAll|Példány|
|SocketOutboundEstablished|Igen|SocketOutboundEstablished|Darabszám|Átlag|SocketOutboundEstablished|Példány|
|SocketOutboundTimeWait|Igen|SocketOutboundTimeWait|Darabszám|Átlag|SocketOutboundTimeWait|Példány|
|TcpCloseWait|Igen|TCP-bezárási várakozás|Darabszám|Átlag|TCP-bezárási várakozás|Példány|
|TcpClosing|Igen|TCP-zárás|Darabszám|Átlag|TCP-zárás|Példány|
|TcpEstablished|Igen|A TCP létrejött|Darabszám|Átlag|A TCP létrejött|Példány|
|TcpFinWait1|Igen|TCP fin WAIT 1|Darabszám|Átlag|TCP fin WAIT 1|Példány|
|TcpFinWait2|Igen|TCP fin WAIT 2|Darabszám|Átlag|TCP fin WAIT 2|Példány|
|TcpLastAck|Igen|TCP Last ACK|Darabszám|Átlag|TCP Last ACK|Példány|
|TcpSynReceived|Igen|TCP SYN kapott|Darabszám|Átlag|TCP SYN kapott|Példány|
|TcpSynSent|Igen|Eljuttatott TCP SYN|Darabszám|Átlag|Eljuttatott TCP SYN|Példány|
|TcpTimeWait|Igen|TCP-idő várakozása|Darabszám|Átlag|TCP-idő várakozása|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (a függvények kivételével) 

> [!NOTE]
> A **fájlrendszer használata** egy új metrika, amely globálisan zajlik, és nem számítunk fel adatokat, kivéve, ha Ön rendelkezik a privát előzetes verzióra vonatkozó engedélyezési listával.

> [!IMPORTANT]
> Az **átlagos válaszidő** a metrikák összesítésével való összekeveredés elkerülése érdekében elavulttá válik. A **válaszidő** használata csereként.

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections|Igen|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|AverageMemoryWorkingSet|Igen|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Igen|Átlagos válaszidő **(elavult)**|Másodperc|Átlag|Átlagos válaszidő|Példány|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CpuTime|Igen|CPU-idő|Másodperc|Összesen|CPU-idő|Példány|
|CurrentAssemblies|Igen|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|FileSystemUsage|Igen|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|Nincsenek méretek|
|Gen0Collections|Igen|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|Igen|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|Igen|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|Kezeli|Igen|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
|HealthCheckStatus|Igen|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http101|Igen|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Igen|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|Igen|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Igen|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Igen|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Igen|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Igen|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Igen|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|HttpResponseTime|Igen|Válaszidő|Másodperc|Átlag|Válaszidő|Példány|
|IoOtherBytesPerSecond|Igen|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoOtherOperationsPerSecond|Igen|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|IoReadBytesPerSecond|Igen|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Igen|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteBytesPerSecond|Igen|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoWriteOperationsPerSecond|Igen|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|MemoryWorkingSet|Igen|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|PrivateBytes|Igen|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|RequestsInApplicationQueue|Igen|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|Szálak|Igen|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|TotalAppDomains|Igen|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Igen|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (functions)

> [!NOTE]
> A **fájlrendszer használata** egy új metrika, amely globálisan zajlik, és nem számítunk fel adatokat, kivéve, ha Ön rendelkezik a privát előzetes verzióra vonatkozó engedélyezési listával.

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|Igen|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CurrentAssemblies|Igen|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|FileSystemUsage|Igen|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|Nincsenek méretek|
|FunctionExecutionCount|Igen|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|FunctionExecutionUnits|Igen|Függvények végrehajtási egységei|Darabszám|Összesen|[Függvények végrehajtási egységei](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|Gen0Collections|Igen|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|Igen|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|Igen|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|HealthCheckStatus|Igen|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http5xx|Igen|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|IoOtherBytesPerSecond|Igen|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoOtherOperationsPerSecond|Igen|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|IoReadBytesPerSecond|Igen|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Igen|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteBytesPerSecond|Igen|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoWriteOperationsPerSecond|Igen|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|MemoryWorkingSet|Igen|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|PrivateBytes|Igen|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|RequestsInApplicationQueue|Igen|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|TotalAppDomains|Igen|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Igen|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|AppConnections|Igen|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|AverageMemoryWorkingSet|Igen|Memória átlagos munkakészlete|Bájt|Átlag|Memória átlagos munkakészlete|Példány|
|AverageResponseTime|Igen|Átlagos válaszidő|Másodperc|Átlag|Átlagos válaszidő|Példány|
|BytesReceived|Igen|A-ben tárolt adatértékek|Bájt|Összesen|A-ben tárolt adatértékek|Példány|
|BytesSent|Igen|Kimenő adatvesztés|Bájt|Összesen|Kimenő adatvesztés|Példány|
|CpuTime|Igen|CPU-idő|Másodperc|Összesen|CPU-idő|Példány|
|CurrentAssemblies|Igen|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|FileSystemUsage|Igen|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|Nincsenek méretek|
|FunctionExecutionCount|Igen|Függvény végrehajtásának száma|Darabszám|Összesen|Függvény végrehajtásának száma|Példány|
|FunctionExecutionUnits|Igen|Függvények végrehajtási egységei|Darabszám|Összesen|Függvények végrehajtási egységei|Példány|
|Gen0Collections|Igen|0. generációs Garbage-gyűjtemények|Darabszám|Összesen|0. generációs Garbage-gyűjtemények|Példány|
|Gen1Collections|Igen|1. generációs Garbage gyűjtemények|Darabszám|Összesen|1. generációs Garbage gyűjtemények|Példány|
|Gen2Collections|Igen|2. generációs Garbage gyűjtemények|Darabszám|Összesen|2. generációs Garbage gyűjtemények|Példány|
|Kezeli|Igen|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
|HealthCheckStatus|Igen|Állapot-ellenőrzési állapot|Darabszám|Átlag|Állapot-ellenőrzési állapot|Példány|
|Http101|Igen|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx|Igen|Http-2xx|Darabszám|Összesen|Http-2xx|Példány|
|Http3xx|Igen|Http-3xx|Darabszám|Összesen|Http-3xx|Példány|
|Http401|Igen|HTTP 401|Darabszám|Összesen|HTTP 401|Példány|
|Http403|Igen|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Igen|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Igen|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx|Igen|Http-4xx|Darabszám|Összesen|Http-4xx|Példány|
|Http5xx|Igen|Http-kiszolgálói hibák|Darabszám|Összesen|Http-kiszolgálói hibák|Példány|
|HttpResponseTime|Igen|Válaszidő|Másodperc|Átlag|Válaszidő|Példány|
|IoOtherBytesPerSecond|Igen|IO – egyéb bájtok másodpercenként|BytesPerSecond|Összesen|IO – egyéb bájtok másodpercenként|Példány|
|IoOtherOperationsPerSecond|Igen|IO egyéb műveletek másodpercenként|BytesPerSecond|Összesen|IO egyéb műveletek másodpercenként|Példány|
|IoReadBytesPerSecond|Igen|IO olvasási bájtok másodpercenként|BytesPerSecond|Összesen|IO olvasási bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Igen|I/o-olvasási műveletek másodpercenként|BytesPerSecond|Összesen|I/o-olvasási műveletek másodpercenként|Példány|
|IoWriteBytesPerSecond|Igen|IO írási bájtok másodpercenként|BytesPerSecond|Összesen|IO írási bájtok másodpercenként|Példány|
|IoWriteOperationsPerSecond|Igen|IO írási műveletek másodpercenként|BytesPerSecond|Összesen|IO írási műveletek másodpercenként|Példány|
|MemoryWorkingSet|Igen|Memória munkakészlete|Bájt|Átlag|Memória munkakészlete|Példány|
|PrivateBytes|Igen|Saját bájtok|Bájt|Átlag|Saját bájtok|Példány|
|Kérelmek|Igen|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|RequestsInApplicationQueue|Igen|Kérelmek az alkalmazás-várólistán|Darabszám|Átlag|Kérelmek az alkalmazás-várólistán|Példány|
|Szálak|Igen|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|TotalAppDomains|Igen|Alkalmazás összes tartománya|Darabszám|Átlag|Alkalmazás összes tartománya|Példány|
|TotalAppDomainsUnloaded|Igen|Összes kitöltött alkalmazás-tartomány|Darabszám|Átlag|Összes kitöltött alkalmazás-tartomány|Példány|


## <a name="next-steps"></a>Következő lépések
* [Tudnivalók a Azure Monitor mérőszámokról](data-platform.md)
* [Riasztások létrehozása metrikákhoz](alerts-overview.md)
* [Metrikák exportálása a Storage-ba, az Event hub-ba vagy a Log Analyticsba](platform-logs-overview.md)
