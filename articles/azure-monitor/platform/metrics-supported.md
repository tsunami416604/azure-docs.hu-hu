---
title: Az Azure Monitor által támogatott metrikák erőforrástípus szerint
description: Az Azure Monitor segítségével az egyes erőforrástípusokhoz elérhető metrikák listája.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 80e38893d764f35511793d8b31f596dcbf16dd42
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991892"
---
# <a name="supported-metrics-with-azure-monitor"></a>Támogatott metrikák az Azure Monitorsegítségével

> [!NOTE]
> Ez a lista nagyrészt automatikusan generált az Azure Monitor metrikák REST API-t. A listán a Githubon keresztül végzett módosítások figyelmeztetés nélkül átírhatók. Az állandó frissítések elkészítésével kapcsolatos részletekért forduljon a cikk szerzőjéhez.

Az Azure Monitor számos lehetőséget kínál a metrikák kal való interakcióra, beleértve a portálon való ábrázolásukat, a REST API-n keresztüli hozzáférésüket, vagy a PowerShell vagy a CLI használatával történő lekérdezésüket. 

Ez a cikk az Azure Monitor összesített metrika-folyamatában jelenleg elérhető összes platform (azaz automatikusan gyűjtött) metrikák teljes listája. A listát utoljára 2020. március 27-én aktualizálták. Előfordulhat, hogy az ezen időpont után módosított vagy hozzáadott mérőszámok nem jelennek meg. A metrikák listájának programozási célú lekérdezéséhez és eléréséhez használja a [2018-01-01 api-verziót](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

Más metrikák is elérhetők lehetnek a portálon, vagy örökölt API-k használatával. Az Azure virtuális gépeken, a Szolgáltatásfabricben és a Felhőszolgáltatásokban futó vendég operációs rendszer (vendég operációs rendszer) metrikák **nem** szerepelnek itt. Ezeket az operációs rendszeren vagy annak részeként futó egy vagy több ügynökön keresztül kell gyűjteni. Az ügynök metrikáit elküldheti a platform metrikái adatbázisába az [egyéni metrikák](metrics-custom-overview.md) API használatával, amelyek jelenleg nyilvános előzetes verzióban vannak. Ezután feltérképezheti, riasztást adhat, és egyéb módon használhatja a vendég operációs rendszer metrikákat, például a platformmérőket. További információ: [Monitoring Agents Overview](agents-overview.md).    

A metrikák névtér szerint vannak rendezve. A szolgáltatások és a hozzájuk tartozó névterek listáját az [Azure-szolgáltatások erőforrás-szolgáltatói](../../azure-resource-manager/management/azure-services-resource-providers.md)című témakörben találja. 

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
> A diagnosztikai beállításokkal exportálható platformmutatók listáját [ebben a cikkben](metrics-supported-export-diagnostic-settings.md)találja.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/kiszolgálók

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|qpu_metric|QPU|Darabszám|Átlag|QPU. Tartomány 0-100 S1, 0-200 S2 és 0-400 S4 esetén|ServerResourceType|
|memory_metric|Memory (Memória)|Bájt|Átlag|Memory. 0–25 GB-os tartomány S1 esetén, 0–50 GB S2 esetén és 0–100 GB S4 esetén|ServerResourceType|
|private_bytes_metric|Privát bájtok|Bájt|Átlag|Privát bájt.|ServerResourceType|
|virtual_bytes_metric|Felhasznált virtuális memória jelenlegi mérete (bájt)|Bájt|Átlag|Virtuális bájtok.|ServerResourceType|
|Összes kapcsolatkérése|Csatlakozási kérelmek összesen|Darabszám|Átlag|Összes csatlakozási kérelem. Ezek érkezések.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sikeres kapcsolatok másodpercenként|CountPerSzekszekundum|Átlag|A sikeres kapcsolatbefejezések aránya.|ServerResourceType|
|Összes kapcsolati hiba|Csatlakozási hibák összesen|Darabszám|Átlag|Összes sikertelen csatlakozási kísérlet.|ServerResourceType|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrehozott felhasználói munkamenetek aktuális száma.|ServerResourceType|
|QueryPoolBusyThreads|Lekérdezéskészlet foglalt szálai|Darabszám|Átlag|Foglalt szálak száma a lekérdezési szálkészletben.|ServerResourceType|
|CommandPoolJobQueueLength|Parancskészlet feladatvárólistájának hossza|Darabszám|Átlag|A parancsszálkészlet várólistájában lévő feladatok száma.|ServerResourceType|
|ProcessingPoolJobQueueLength|Készletfeladat-várólista hosszának feldolgozása|Darabszám|Átlag|A feldolgozószál-készlet várólistájában lévő nem I/O-feladatok száma.|ServerResourceType|
|Jelenlegi kapcsolatok|Kapcsolat: Jelenlegi kapcsolatok|Darabszám|Átlag|A létrehozott ügyfélkapcsolatok aktuális száma.|ServerResourceType|
|CleanerCurrentPrice|Memória: Tisztább aktuális ár|Darabszám|Átlag|A memória aktuális ára , $/byte/time, normalizálva 1000-re.|ServerResourceType|
|CleanerMemoryZsugorodikható|Memória: Tisztább memória zsugorodik|Bájt|Átlag|A memória mennyisége bájtban, a háttértisztító általi tisztításfüggvényében.|ServerResourceType|
|CleanerMemoryNem zsugorítható|Memória: A tisztább memória nem zsugorodik|Bájt|Átlag|A memória mennyisége bájtban, nem függ a háttértisztító tisztítását.|ServerResourceType|
|Memóriahasználat|Memória: Memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználata a tisztább memória árának kiszámításához használt módon. Egyenlő a counter Process\PrivateBytes plus a memória-leképezett adatok méretével, figyelmen kívül hagyva minden olyan memóriát, amelyet az xVelocity in-memory analytics engine (VertiPaq) az xVelocity motor memórialimitjét meghaladóan leképezett vagy lefoglalt.|ServerResourceType|
|MemoryLimitHard memória|Memória: Memória korlát kemény|Bájt|Átlag|Szigorú memóriakorlát a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitHigh|Memória: Memória limit magas|Bájt|Átlag|Magas memóriakorlát, konfigurációs fájlból.|ServerResourceType|
|MemoryLimitLow memória|Memória: Kevés memóriakorlát|Bájt|Átlag|Kevés a memória, a konfigurációs fájlból.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: Memóriakorlát VertiPaq|Bájt|Átlag|Memóriakorlát, konfigurációs fájlból.|ServerResourceType|
|Kvóta|Memória: Kvóta|Bájt|Átlag|Aktuális memóriakvóta bájtban. A memóriakvótát memóriatámogatásnak vagy memóriafoglalásnak is nevezik.|ServerResourceType|
|Kvóta blokkolva|Memória: Kvóta letiltva|Darabszám|Átlag|A többi memóriakvótáfelszabadításig letiltott kvótakérelmek jelenlegi száma.|ServerResourceType|
|VertiPaqNonpaged között|Memória: VertiPaq Nonpaged|Bájt|Átlag|A munkakészletben zárolt memóriabájtokat a memóriában lévő motor számára.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq lapozgatott|Bájt|Átlag|A memórián belüli adatokhoz használt lapozható memória bájtjai.|ServerResourceType|
|RowsreadperSec|Feldolgozás: Másodpercenként olvasott sorok|CountPerSzekszekundum|Átlag|Az összes relációs adatbázisból beolvasott sorok aránya.|ServerResourceType|
|RowsconvertedperSec|Feldolgozás: Másodpercenként konvertált sorok|CountPerSzekszekundum|Átlag|A feldolgozás során konvertált sorok aránya.|ServerResourceType|
|RowsWrittenPerSec|Feldolgozás: Másodpercenként írt sorok|CountPerSzekszekundum|Átlag|A feldolgozás során írt sorok aránya.|ServerResourceType|
|CommandPoolBusyThreads|Szálak: Parancskészlet foglalt szálai|Darabszám|Átlag|Foglalt szálak száma a parancsszálkészletben.|ServerResourceType|
|CommandPoolIdleThreads|Szálak: Parancskészlet tétlen szálai|Darabszám|Átlag|Az alapjárati szálak száma a parancsszálkészletben.|ServerResourceType|
|LongParsingBusyThreads|Szálak: Hosszú elemzés foglalt szálak|Darabszám|Átlag|Foglalt szálak száma a hosszú elemzési szálkészletben.|ServerResourceType|
|LongParsingIdleThreads|Szálak: Hosszú elemzés tétlen szálak|Darabszám|Átlag|A hosszú elemzési szálkészletben lévő alapjárati szálak száma.|ServerResourceType|
|LongParsingJobQueueLength|Szálak: Hosszú elemzési feladat várólista hossza|Darabszám|Átlag|A hosszú elemzési szálkészlet várólistájában lévő feladatok száma.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Szálak: A készlet foglalt I/O-feladatszálainak feldolgozása|Darabszám|Átlag|I/O-feladatokat futtató szálak száma a feldolgozási szálkészletben.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Szálak: A készlet foglalt, nem I/O-szálai|Darabszám|Átlag|Nem I/O-feladatokat futtató szálak száma a feldolgozószál-készletben.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Szálak: A készlet I/O-feladatvárólistájának hosszának feldolgozása|Darabszám|Átlag|A feldolgozószál-készlet várólistájában lévő I/O-feladatok száma.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Szálak: A készlet tétlen I/O-feladatszálainak feldolgozása|Darabszám|Átlag|A feldolgozási szálkészletben lévő I/O-feladatok tétlen szálainak száma.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Szálak: A készlet alapjárati nem I/O-szálai|Darabszám|Átlag|A nem I/O-feladatoknak szánt feldolgozószál-készletben lévő tétlen szálak száma.|ServerResourceType|
|QueryPoolIdleThreads|Szálak: Lekérdezéskészlet tétlen szálai|Darabszám|Átlag|A feldolgozási szálkészletben lévő I/O-feladatok tétlen szálainak száma.|ServerResourceType|
|QueryPoolJobQueueLength|Szálak: Lekérdezéskészlet feladatvárólistája lengt|Darabszám|Átlag|A lekérdezési szálkészlet várólistájában lévő feladatok száma.|ServerResourceType|
|ShortParsingBusyThreads|Szálak: Rövid elemzés foglalt szálak|Darabszám|Átlag|Foglalt szálak száma a rövid elemzési szálkészletben.|ServerResourceType|
|ShortParsingIdleThreads|Szálak: Rövid elemzési tétlen szálak|Darabszám|Átlag|A rövid elemzési szálkészletben lévő alapjárati szálak száma.|ServerResourceType|
|ShortParsingJobQueueLength|Szálak: Rövid elemzési feladat várólista hossza|Darabszám|Átlag|A rövid elemzési szálkészlet várólistájában lévő feladatok száma.|ServerResourceType|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Átlagos memória verés.|ServerResourceType|
|mashup_engine_qpu_metric|M Motor QPU|Darabszám|Átlag|QPU-használat mashup motorfolyamatok által|ServerResourceType|
|mashup_engine_memory_metric|M motor memória|Bájt|Átlag|Memóriahasználat mashup motorfolyamatok által|ServerResourceType|
|mashup_engine_private_bytes_metric|M motor privát bájt|Bájt|Átlag|Privát bájthasználat a mashup motor folyamatai által.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M motor virtuális bájtjai|Bájt|Átlag|Virtuális bájthasználat mashup motorfolyamatokkal.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalRequests|Átjárókérelmek összesen (elavult)|Darabszám|Összesen|Átjárókérelmek száma – Többdimenziós kérelemmetrika használata a GatewayResponseCodeCategory dimenzióval|Hely,Állomásnév|
|Sikeres kérések|Sikeres átjárókérelmek (elavult)|Darabszám|Összesen|A sikeres átjárókérelmek száma – Többdimenziós kérelemmetrika használata a GatewayResponseCodeCategory dimenzióval|Hely,Állomásnév|
|Jogosulatlan kérések|Nem engedélyezett átjárókérelmek (elavult)|Darabszám|Összesen|Nem engedélyezett átjárókérelmek száma – Többdimenziós kérelemmetrika használata a GatewayResponseCodeCategory dimenzióval|Hely,Állomásnév|
|Sikertelen kérések|Sikertelen átjárókérelmek (elavult)|Darabszám|Összesen|Az átjárókérelmek hibáinak száma – Többdimenziós kérelemmetrika használata a GatewayResponseCodeCategory dimenzióval|Hely,Állomásnév|
|Egyébkérések|Egyéb átjárókérelmek (elavult)|Darabszám|Összesen|Egyéb átjárókérelmek száma – Többdimenziós kérelemmetrika használata a GatewayResponseCodeCategory dimenzióval|Hely,Állomásnév|
|Időtartam|Az átjárókérelmek teljes időtartama|Ezredmásodperc|Átlag|Az átjárókérelmek teljes időtartama ezredmásodpercben|Hely,Állomásnév|
|Háttérvégpontidőtartama|A háttérrendszer-kérelmek időtartama|Ezredmásodperc|Átlag|A háttérrendszer-kérelmek időtartama ezredmásodpercben|Hely,Állomásnév|
|Kapacitás|Kapacitás|Százalék|Átlag|Az ApiManagement szolgáltatás kihasználtsági metrikája|Hely|
|EventHubTotalEvents|EventHub-események összesen|Darabszám|Összesen|Az EventHubra küldött események száma|Hely|
|EventHubSuccessfulEvents|Sikeres EventHub-események|Darabszám|Összesen|Sikeres EventHub-események száma|Hely|
|EventHubTotalFailedEvents|Sikertelen EventHub-események|Darabszám|Összesen|Sikertelen EventHub-események száma|Hely|
|EventHubElutasítottEsemények|Elutasított EventHub-események|Darabszám|Összesen|Elutasított EventHub-események száma (hibás konfiguráció vagy jogosulatlan)|Hely|
|EventHubThrottledEsemények|Szabályozott EventHub-események|Darabszám|Összesen|Szabályozott EventHub-események száma|Hely|
|EventHubTimedoutEvents|EventHub-események idővel elvégzett eseményei|Darabszám|Összesen|EventHub-események időtúltöltött száma|Hely|
|EventHubDroppedEvents|Eldobott EventHub-események|Darabszám|Összesen|A várólista méretkorlátjának elérése miatt kihagyott események száma|Hely|
|EventHubTotalBytesSent|EventHub-események mérete|Bájt|Összesen|Az EventHub-események teljes mérete bájtban|Hely|
|Kérelmek|Kérelmek|Darabszám|Összesen|Átjárókérelem-metrikák több dimenzióval|Hely,Állomásnév,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory|
|Hálózati kapcsolat|Az erőforrások hálózati kapcsolatállapota (előzetes verzió)|Darabszám|Összesen|Az API Management szolgáltatás függő erőforrástípusainak hálózati kapcsolata|Hely,Erőforrástípus|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Darabszám|Darabszám|A bejövő http-kérések teljes száma.|StatusCode (Állapotkód)|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Darabszám|Átlag|Késés http-kérelem esetén.|StatusCode (Állapotkód)|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Tavasz

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SystemCpuUsageSzázalék|Rendszerprocesszor-használat százaléka|Százalék|Átlag|A legutóbbi cpu használat az egész rendszer|Alkalmazásnév,Pod|
|AppCpuUsageSzázalék|Alkalmazás PROCESSZOR-használati százaléka|Százalék|Átlag|App JVM CPU-használat százaléka|Alkalmazásnév,Pod|
|AppMemoryCommitted|Hozzárendelt alkalmazásmemória|Bájt|Átlag|A JVM-hez rendelt memória bájtban|Alkalmazásnév,Pod|
|AppMemoryUsed|Használt alkalmazásmemória|Bájt|Átlag|Alkalmazásmemória bájtban használva|Alkalmazásnév,Pod|
|AppMemoryMax alkalmazás|Alkalmazásmemória max.|Bájt|Maximum|A memóriakezeléshez használható maximális memóriamennyiség bájtban|Alkalmazásnév,Pod|
|MaxOldGenMemoryPoolPoolBytes|Maximálisan elérhető régi generációs adatméret|Bájt|Átlag|A régi generációs memóriakészlet maximális mérete|Alkalmazásnév,Pod|
|OldGenMemoryPoolBytes|Régi generációs adatok mérete|Bájt|Átlag|A régi generációs memóriakészlet mérete a teljes globális katalógus után|Alkalmazásnév,Pod|
|OldGenPromotedBytes|Előléptetés régi generációs adatméretre|Bájt|Maximum|A gc és a GC utáni régi generációs memóriakészlet méretének pozitív növekedésének száma|Alkalmazásnév,Pod|
|YoungGenPromotedBytes|Népszerűsítés fiatal generációs adatméretre|Bájt|Maximum|A fiatal generációs memóriakészlet méretének növekedéséhez az egyik GLOBÁLIS|Alkalmazásnév,Pod|
|GCPauseTotalCount (GCPauseTotalCount)|Gc szünetszáma|Darabszám|Összesen|Gc szünetszáma|Alkalmazásnév,Pod|
|GCPauseTotalTime|Globális katalógus szüneteltetési teljes ideje|Ezredmásodperc|Összesen|Globális katalógus szüneteltetési teljes ideje|Alkalmazásnév,Pod|
|TomcatSentBytes (TomcatSentBytes)|Tomcat összes elküldött bájt|Bájt|Összesen|Tomcat összes elküldött bájt|Alkalmazásnév,Pod|
|TomcatReceivedBytes (TomcatReceivedBytes)|Tomcat teljes fogadott bájt|Bájt|Összesen|Tomcat teljes fogadott bájt|Alkalmazásnév,Pod|
|TomcatRequestTotalTime|Tomcat kérelem összes alkalommal|Ezredmásodperc|Összesen|Tomcat kérelem összes alkalommal|Alkalmazásnév,Pod|
|TomcatRequestTotalCount (TomcatRequestTotalCount)|Tomcat kérelem teljes száma|Darabszám|Összesen|Tomcat kérelem teljes száma|Alkalmazásnév,Pod|
|TomcatResponseAvgTime|Tomcat kérelem átlagos ideje|Ezredmásodperc|Átlag|Tomcat kérelem átlagos ideje|Alkalmazásnév,Pod|
|TomcatRequestMaxTime|Tomcat-kérelem maximális ideje|Ezredmásodperc|Maximum|Tomcat-kérelem maximális ideje|Alkalmazásnév,Pod|
|TomcatHibaszám|Tomcat globális hiba|Darabszám|Összesen|Tomcat globális hiba|Alkalmazásnév,Pod|
|TomcatSessionActiveMaxCount|Tomcat-munkamenet maximális aktív száma|Darabszám|Összesen|Tomcat-munkamenet maximális aktív száma|Alkalmazásnév,Pod|
|TomcatSessionAliveMaxTime|Tomcat session max élő idő|Ezredmásodperc|Maximum|Tomcat session max élő idő|Alkalmazásnév,Pod|
|TomcatSessionActiveCurrentCount|Tomcat munkamenet élő száma|Darabszám|Összesen|Tomcat munkamenet élő száma|Alkalmazásnév,Pod|
|TomcatSessionCreatedCount|Tomcat munkamenet létrehozva száma|Darabszám|Összesen|Tomcat munkamenet létrehozva száma|Alkalmazásnév,Pod|
|TomcatSessionExpiredCount|Tomcat-munkamenet lejárt száma|Darabszám|Összesen|Tomcat-munkamenet lejárt száma|Alkalmazásnév,Pod|
|TomcatSessionRejectedCount|Tomcat munkamenet elutasított száma|Darabszám|Összesen|Tomcat munkamenet elutasított száma|Alkalmazásnév,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljes feladat|Összes feladat|Darabszám|Összesen|A munkahelyek teljes száma|Runbook,Állapot|
|TotalUpdateDeploymentRuns|A frissítés telepítési futásai összesen|Darabszám|Összesen|A szoftverfrissítések telepítésének teljes futtatása|SoftwareUpdateConfigurationName,Állapot|
|TotalUpdateDeploymentMachineRuns|A frissítési központi telepítési gép teljes futtatása|Darabszám|Összesen|A szoftverfrissítés-telepítőgép teljes futtatása egy szoftverfrissítési központi telepítésen|SoftwareUpdateConfigurationName,Állapot,TargetComputer,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CoreCount (Magszáma)|Dedikált magok száma|Darabszám|Összesen|A kötegszámla dedikált magjainak teljes száma|None|
|TotalNodeCount (ÖsszesnodeCount)|Dedikált csomópontszám|Darabszám|Összesen|A kötegszámla kijelölt csomópontjainak teljes száma|None|
|LowPriorityCoreCount (LowPriorityCoreCount)|LowPriority magszámláló|Darabszám|Összesen|Az alacsony prioritású magok teljes száma a kötegfiókban|None|
|TotalLowPriorityNodeCount (ÖsszeslowPriorityNodeCount)|Alacsony prioritású csomópontszám|Darabszám|Összesen|A kötegszámla alacsony prioritású csomópontjainak teljes száma|None|
|LétrehozásNodeCount|Csomópontszám létrehozása|Darabszám|Összesen|Létrehozás alatt álló csomópontok száma|None|
|StartingNodeCount között|Kezdő csomópontszám|Darabszám|Összesen|Induló csomópontok száma|None|
|WaitingForStartTaskNodeCount (Várakozás: StartForStartTaskNodeCount)|Várakozás a feladatcsomópont-számláló indítására|Darabszám|Összesen|A kezdési feladat befejezésére váró csomópontok száma|None|
|StartTaskFailedNodeCount|Sikertelen kezdő feladat csomópontszáma|Darabszám|Összesen|Azon csomópontok száma, amelyeknél a feladat indítása sikertelen volt|None|
|IdleNode-számláló|Tétlen csomópontok száma|Darabszám|Összesen|Tétlen csomópontok száma|None|
|OfflineNodeCount (OfflineNodeCount)|Kapcsolat nélküli csomópontok száma|Darabszám|Összesen|Kapcsolat nélküli csomópontok száma|None|
|ÚjraindításNodeCount|A csomópontszám újraindítása|Darabszám|Összesen|Újraindító csomópontok száma|None|
|ReimagingNodeCount (ReimagingNodeCount)|Csomópontszám újraszámítása|Darabszám|Összesen|Újraképző csomópontok száma|None|
|RunningNodeCount között|Futó csomópontszám|Darabszám|Összesen|Futó csomópontok száma|None|
|KilépéspoolNodeCount|Kilépés készletcsomópont-számlálóból|Darabszám|Összesen|A készletből kilépő csomópontok száma|None|
|Használhatatlan NodeCount|Használhatatlan csomópontszám|Darabszám|Összesen|Használhatatlan csomópontok száma|None|
|PreemptedNodeCount (PreemptedNodeCount)|Preempted csomópontszám|Darabszám|Összesen|Preempted csomópontok száma|None|
|TaskStartEvent|Feladat indítási eseményei|Darabszám|Összesen|Az elindított tevékenységek száma összesen|poolId,jobId|
|TaskCompleteEvent|Feladat-befejezési események|Darabszám|Összesen|A befejezett feladatok száma összesen|poolId,jobId|
|TaskFailEvent|Feladat sikertelen események|Darabszám|Összesen|A sikertelen állapotban elvégzett feladatok száma összesen|poolId,jobId|
|PoolCreateEvent|Készlet létrehozási események|Darabszám|Összesen|A létrehozott készletek teljes száma|poolId|
|PoolResizeStartEvent|Készlet átméretezése kezdési események|Darabszám|Összesen|Az elindított készletátméretek teljes száma|poolId|
|PoolResizeCompleteEvent|Készlet átméretezése teljes események|Darabszám|Összesen|A befejezett készletátméretek teljes száma|poolId|
|KészletdeleteStartevent|Készlet törlése kezdőesemények|Darabszám|Összesen|Az elindított készlettörlések teljes száma|poolId|
|Készlettörléscompleteesemény|Készlet törlése teljes események|Darabszám|Összesen|A befejezett készlettörlések teljes száma|poolId|
|JobDeleteCompleteEvent|Feladat teljes eseményeinek törlése|Darabszám|Összesen|A sikeresen törölt feladatok száma összesen.|jobId|
|FeladatDeleteStartEvent|Feladat indítási eseményeinek törlése|Darabszám|Összesen|A törlésre kért feladatok teljes száma.|jobId|
|JobDisableCompleteEvent|Feladat teljes események letiltása|Darabszám|Összesen|A sikeresen letiltott feladatok száma összesen.|jobId|
|FeladatDisableStartEvent|Feladat indítási eseményeinek letiltása|Darabszám|Összesen|A letiltásra kért feladatok teljes száma.|jobId|
|FeladatStartEvent|Feladatindítási események|Darabszám|Összesen|A sikeresen elindított feladatok száma összesen.|jobId|
|JobTerminateCompleteEvent|Feladat befejezése – teljes események|Darabszám|Összesen|A sikeresen leállított feladatok száma összesen.|jobId|
|FeladatTerminateStartEvent|Feladat indítási eseményeinek leállítása|Darabszám|Összesen|A megszüntetendő feladatok teljes száma.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Elküldött feladat|Elküldött feladat|Darabszám|Összesen|Beküldött állások száma|Eset,Fürtnév|
|Feladat elvégezve|Feladat elvégezve|Darabszám|Összesen|Befejezett feladatok száma|Eset,Fürtnév,ResultType|
|Összes csomópont|Összes csomópont|Darabszám|Átlag|Összes csomópont száma|Eset,Fürtnév|
|Aktív csomópontok|Aktív csomópontok|Darabszám|Átlag|Futó csomópontok száma|Eset,Fürtnév|
|Tétlen csomópontok|Tétlen csomópontok|Darabszám|Átlag|Tétlen csomópontok száma|Eset,Fürtnév|
|Használhatatlan csomópontok|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma|Eset,Fürtnév|
|Elődpontos csomópontok|Elődpontos csomópontok|Darabszám|Átlag|Preempted csomópontok száma|Eset,Fürtnév|
|Csomópontok elhagyása|Csomópontok elhagyása|Darabszám|Átlag|A kilépő csomópontok száma|Eset,Fürtnév|
|Magok összesen|Magok összesen|Darabszám|Átlag|Az összes mag száma|Eset,Fürtnév|
|Aktív magok|Aktív magok|Darabszám|Átlag|Aktív magok száma|Eset,Fürtnév|
|Alapjárati magok|Alapjárati magok|Darabszám|Átlag|Tétlen magok száma|Eset,Fürtnév|
|Használhatatlan magok|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Eset,Fürtnév|
|Preempted magok|Preempted magok|Darabszám|Átlag|Preempted magok száma|Eset,Fürtnév|
|Elhagyva magok|Elhagyva magok|Darabszám|Átlag|A kilépő magok száma|Eset,Fürtnév|
|Kvótakihasználtság százaléka|Kvótakihasználtság százaléka|Darabszám|Átlag|A felhasznált kvóta százaléka|Eset,Fürtnév,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsagepercentageinDouble|PROCESSZOR-használat százaléka|Százalék|Maximum|PROCESSZOR-használat százaléka|Csomópont|
|Memóriahasználat|Memóriahasználat|Bájt|Átlag|Memóriahasználat|Csomópont|
|MemoryLimit memóriahatár|Memóriakorlát|Bájt|Átlag|Memóriakorlát|Csomópont|
|Memóriahasználatszázalékösszeg|Memóriahasználat százaléka|Százalék|Átlag|Memóriahasználat százaléka|Csomópont|
|StorageUsage (Tároláshasználata)|Tárhely használat|Bájt|Átlag|Tárhely használat|Csomópont|
|IOReadBytes|Io olvasási bájtjai|Bájt|Összesen|Io olvasási bájtjai|Csomópont|
|IOWriteBájtok|IO írási bájtok|Bájt|Összesen|IO írási bájtok|Csomópont|
|ConnectionAccepted (Kapcsolat elfogadva)|Elfogadott kapcsolatok|Darabszám|Összesen|Elfogadott kapcsolatok|Csomópont|
|Kapcsolat által kezelt|Kezelt kapcsolatok|Darabszám|Összesen|Kezelt kapcsolatok|Csomópont|
|ConnectionActive|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|Csomópont|
|Kéréssel elnyújtva|Kezelt kérelmek|Darabszám|Összesen|Kezelt kérelmek|Csomópont|
|Feldolgozott blokkok|Feldolgozott blokkok|Darabszám|Összesen|Feldolgozott blokkok|Csomópont|
|Feldolgozott tranzakciók|Feldolgozott tranzakciók|Darabszám|Összesen|Feldolgozott tranzakciók|Csomópont|
|Függőtranzakciók|Függőben lévő tranzakciók|Darabszám|Átlag|Függőben lévő tranzakciók|Csomópont|
|Várólistán lévő tranzakciók|Várólistán lévő tranzakciók|Darabszám|Átlag|Várólistán lévő tranzakciók|Csomópont|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedclients (connectedclients)|Csatlakozott ügyfelek|Darabszám|Maximum||Szilánk|
|összes parancs feldolgozva|Összes művelet|Darabszám|Összesen||Szilánk|
|gyorsítótár-találatok|Gyorsítótár-találatok|Darabszám|Összesen||Szilánk|
|gyorsítótár-tévesztések|Gyorsítótár-tévesztések|Darabszám|Összesen||Szilánk|
|gyorsítótár-missrate|Gyorsítótár-tévesztési arány|Százalék|gyorsítótár-missrate||Szilánk|
|getparancsok|Kap|Darabszám|Összesen||Szilánk|
|setparancsok|Halmazok|Darabszám|Összesen||Szilánk|
|műveletekPerSecond|Műveletek másodpercenként|Darabszám|Maximum||Szilánk|
|kilakoltatott kulcsok|Kilakoltatott kulcsok|Darabszám|Összesen||Szilánk|
|összes kulcs|Összes kulcs|Darabszám|Maximum||Szilánk|
|lejárt kulcsok|Lejárt kulcsok|Darabszám|Összesen||Szilánk|
|használt memória|Használt memória|Bájt|Maximum||Szilánk|
|használtmemóriaszázalék|Használt memória százaléka|Százalék|Maximum||Szilánk|
|használt memoryRss|Használt memória RSS|Bájt|Maximum||Szilánk|
|kiszolgálóbetöltése|Kiszolgáló betöltése|Százalék|Maximum||Szilánk|
|gyorsítótárírás|Gyorsítótár írása|BytesPerSecond|Maximum||Szilánk|
|cacheRead|Gyorsítótár olvasása|BytesPerSecond|Maximum||Szilánk|
|százalékProcessorTime|CPU|Százalék|Maximum||Szilánk|
|gyorsítótárLátság|Gyorsítótár-késleltetés mikroszekundumok (előzetes verzió)|Darabszám|Átlag||Szilánk|
|Hibák|Hibák|Darabszám|Maximum||ShardId,Hibatípus|
|connectedclients0|Csatlakoztatott ügyfelek (0. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozása00|Összes művelet (0. sángold)|Darabszám|Összesen||None|
|gyorsítótárhits0|Gyorsítótár-találatok (0. szegmens)|Darabszám|Összesen||None|
|gyorsítótármisses0|Gyorsítótár-tévesztések (0. szegmens)|Darabszám|Összesen||None|
|getcommands0|Gets (Shard 0)|Darabszám|Összesen||None|
|setcommands0|Készletek (Shard 0)|Darabszám|Összesen||None|
|műveletekPerSecond0|Műveletek másodpercenként (0. sánda)|Darabszám|Maximum||None|
|kilaktatotik0|Kilakoltatott kulcsok (0. shard)|Darabszám|Összesen||None|
|összeskulcs0|Összes kulcs (0. sángold)|Darabszám|Maximum||None|
|lejárt kulcsok0|Lejárt kulcsok (0. shard)|Darabszám|Összesen||None|
|használtmemória0|Használt memória (0. shard)|Bájt|Maximum||None|
|használtmemoryRss0|Használt memória RSS (Shard 0)|Bájt|Maximum||None|
|serverLoad0|Kiszolgáló betöltése (0. szegmens)|Százalék|Maximum||None|
|cacheWrite0|Gyorsítótár írása (0. shard)|BytesPerSecond|Maximum||None|
|cacheRead0|Gyorsítótár olvasása (0. s.|BytesPerSecond|Maximum||None|
|percentProcessorTime0|CPU (Shard 0)|Százalék|Maximum||None|
|connectedclients1|Csatlakoztatott ügyfelek (1. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozás1|Összes művelet (1. szegmens)|Darabszám|Összesen||None|
|gyorsítótár-találatok1|Gyorsítótár-találatok (1. szegmens)|Darabszám|Összesen||None|
|gyorsítótármisses1|Gyorsítótár-tévesztések (1. szegmens)|Darabszám|Összesen||None|
|getcommands1|Gets (Shard 1)|Darabszám|Összesen||None|
|setparancsok1|Készletek (Shard 1)|Darabszám|Összesen||None|
|műveletekPerSecond1|Műveletek másodpercenként (1. sánda)|Darabszám|Maximum||None|
|kilaktatotik1|Kilakoltatott kulcsok (1. szegmens)|Darabszám|Összesen||None|
|összes kulcs1|Összes kulcs (1. szegmens)|Darabszám|Maximum||None|
|lejárt kulcsok1|Lejárt kulcsok (1. szegmens)|Darabszám|Összesen||None|
|használtmemória1|Használt memória (1. szegmens)|Bájt|Maximum||None|
|használtmemoryRss1|Használt memória RSS (Shard 1)|Bájt|Maximum||None|
|serverLoad1|Kiszolgáló betöltése (1. szegmens)|Százalék|Maximum||None|
|cacheWrite1|Gyorsítótár írása (1. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead1|Gyorsítótár olvasása (1. szegmens)|BytesPerSecond|Maximum||None|
|százalékProcessorTime1|CPU (1. szegmens)|Százalék|Maximum||None|
|connectedclients2|Csatlakoztatott ügyfelek (2. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozás2|Összes művelet (2. shard)|Darabszám|Összesen||None|
|gyorsítótár-találatok2|Gyorsítótár-találatok (2. szegmens)|Darabszám|Összesen||None|
|gyorsítótármisses2|Gyorsítótár-tévesztések (2. szegmens)|Darabszám|Összesen||None|
|getcommands2|Gets (Shard 2)|Darabszám|Összesen||None|
|setparancsok2|Készletek (Shard 2)|Darabszám|Összesen||None|
|műveletekPerSecond2|Műveletek másodpercenként (2. sánda)|Darabszám|Maximum||None|
|kilakoltatott kulcsok2|Kilakoltatott kulcsok (2. szegmens)|Darabszám|Összesen||None|
|összesenkeys2|Összes kulcs (2. szilánk)|Darabszám|Maximum||None|
|lejárt kulcsok2|Lejárt kulcsok (2. shard)|Darabszám|Összesen||None|
|használtmemória2|Használt memória (2. szegmens)|Bájt|Maximum||None|
|használtmemoryRss2|Használt memória RSS (Shard 2)|Bájt|Maximum||None|
|serverLoad2|Kiszolgáló betöltése (2. szegmens)|Százalék|Maximum||None|
|cacheWrite2|Gyorsítótár írása (2. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead2|Gyorsítótár olvasása (2. szegmens)|BytesPerSecond|Maximum||None|
|százalékProcessorTime2|CPU (Shard 2)|Százalék|Maximum||None|
|connectedclients3|Csatlakoztatott ügyfelek (3. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozás3|Összes művelet (3. shard)|Darabszám|Összesen||None|
|gyorsítótár-találatok3|Gyorsítótár-találatok (3. szegmens)|Darabszám|Összesen||None|
|gyorsítótármisses3|Gyorsítótár-tévesztések (3. szegmens)|Darabszám|Összesen||None|
|getcommands3|Gets (Shard 3)|Darabszám|Összesen||None|
|setparancsok3|Készletek (Shard 3)|Darabszám|Összesen||None|
|műveletekPerSecond3|Műveletek másodpercenként (3. shard)|Darabszám|Maximum||None|
|kilakoltatott kulcsok3|Kilakoltatott kulcsok (3. shard)|Darabszám|Összesen||None|
|összesenkeys3|Összes kulcs (3. sánda)|Darabszám|Maximum||None|
|lejárt kulcsok3|Lejárt kulcsok (3. shard)|Darabszám|Összesen||None|
|használtmemória3|Használt memória (3. shard)|Bájt|Maximum||None|
|használtmemoryRss3|Használt memória RSS (Shard 3)|Bájt|Maximum||None|
|serverLoad3|Kiszolgáló betöltése (3. szegmens)|Százalék|Maximum||None|
|gyorsítótárÍrás3|Gyorsítótár írása (3. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead3|Gyorsítótár olvasása (3. shard)|BytesPerSecond|Maximum||None|
|százalékProcessorTime3|CPU (Szilánk 3)|Százalék|Maximum||None|
|connectedclients4|Csatlakoztatott ügyfelek (4. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozás4|Összes művelet (4. szilánk)|Darabszám|Összesen||None|
|gyorsítótár-találatok4|Gyorsítótár-találatok (4. szegmens)|Darabszám|Összesen||None|
|gyorsítótármisses4|Gyorsítótár-tévesztések (4. szegmens)|Darabszám|Összesen||None|
|getcommands4|Gets (Shard 4)|Darabszám|Összesen||None|
|setparancsok4|Készletek (Shard 4)|Darabszám|Összesen||None|
|műveletekPerSecond4|Műveletek másodpercenként (4. sánda)|Darabszám|Maximum||None|
|kilaktatotik4|Kilakoltatott kulcsok (4. szegmens)|Darabszám|Összesen||None|
|összesen4|Összes kulcs (4. szilánk)|Darabszám|Maximum||None|
|lejárt kulcsok4|Lejárt kulcsok (4. szegmens)|Darabszám|Összesen||None|
|használtmemória4|Használt memória (4. szegmens)|Bájt|Maximum||None|
|használtmemoryRss4|Használt memória RSS (Shard 4)|Bájt|Maximum||None|
|serverLoad4|Kiszolgáló betöltése (4. szegmens)|Százalék|Maximum||None|
|gyorsítótárWrite4|Gyorsítótár írása (4. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead4|Gyorsítótár olvasása (4. szegmens)|BytesPerSecond|Maximum||None|
|százalékProcessorTime4|CPU (Shard 4)|Százalék|Maximum||None|
|connectedclients5|Csatlakoztatott ügyfelek (5. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozás5|Összes művelet (5. szilánk)|Darabszám|Összesen||None|
|gyorsítótár-találatok5|Gyorsítótár-találatok (Shard 5)|Darabszám|Összesen||None|
|gyorsítótármisses5|Gyorsítótár-tévesztések (5. szegmens)|Darabszám|Összesen||None|
|getcommands5|Gets (Shard 5)|Darabszám|Összesen||None|
|setparancsok5|Készletek (Shard 5)|Darabszám|Összesen||None|
|műveletekPerSecond5|Műveletek másodpercenként (5. szegmens)|Darabszám|Maximum||None|
|kilaktatotik5|Kilakoltatott kulcsok (5. szegmens)|Darabszám|Összesen||None|
|összesen5|Összes kulcs (5. szilánk)|Darabszám|Maximum||None|
|lejárt kulcsok5|Lejárt kulcsok (5. szegmens)|Darabszám|Összesen||None|
|használtmemória5|Használt memória (5. szegmens)|Bájt|Maximum||None|
|használtmemoryRss5|Használt memória RSS (Shard 5)|Bájt|Maximum||None|
|serverLoad5|Kiszolgáló betöltése (5. szegmens)|Százalék|Maximum||None|
|gyorsítótárÍrás5|Gyorsítótár írása (5. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead5|Gyorsítótár olvasása (5. szegmens)|BytesPerSecond|Maximum||None|
|százalékProcessorTime5|CPU (Shard 5)|Százalék|Maximum||None|
|connectedclients6|Csatlakoztatott ügyfelek (6. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozás6|Összes művelet (Shard 6)|Darabszám|Összesen||None|
|gyorsítótártalálat6|Gyorsítótár-találatok (6. szegmens)|Darabszám|Összesen||None|
|gyorsítótármisses6|Gyorsítótár-tévesztések (6. szegmens)|Darabszám|Összesen||None|
|getcommands6|Gets (Shard 6)|Darabszám|Összesen||None|
|setparancsok6|Készletek (Shard 6)|Darabszám|Összesen||None|
|műveletekPerSecond6|Műveletek másodpercenként (Shard 6)|Darabszám|Maximum||None|
|kilaktatotik6|Kilakoltatott kulcsok (6. szegmens)|Darabszám|Összesen||None|
|összesen6|Összes kulcs (Shard 6)|Darabszám|Maximum||None|
|lejárt kulcsok6|Lejárt kulcsok (Shard 6)|Darabszám|Összesen||None|
|használtmemória6|Használt memória (Shard 6)|Bájt|Maximum||None|
|használtmemoryRss6|Használt memória RSS (Shard 6)|Bájt|Maximum||None|
|serverLoad6|Kiszolgáló betöltése (6. shard)|Százalék|Maximum||None|
|gyorsítótárÍrás6|Gyorsítótár írása (6. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead6|Gyorsítótár olvasása (6. szegmens)|BytesPerSecond|Maximum||None|
|százalékProcessorTime6|CPU (Shard 6)|Százalék|Maximum||None|
|connectedclients7|Csatlakoztatott ügyfelek (Shard 7)|Darabszám|Maximum||None|
|összes parancsfeldolgozás7|Összes művelet (Shard 7)|Darabszám|Összesen||None|
|gyorsítótár-találatok7|Gyorsítótár-találatok (Shard 7)|Darabszám|Összesen||None|
|gyorsítótár-misses7|Gyorsítótár-tévesztések (7. szegmens)|Darabszám|Összesen||None|
|getcommands7|Gets (Shard 7)|Darabszám|Összesen||None|
|setparancsok7|Készletek (Shard 7)|Darabszám|Összesen||None|
|műveletekPerSecond7|Műveletek másodpercenként (Shard 7)|Darabszám|Maximum||None|
|kilakoltattákkeys7|Kilakoltatott kulcsok (Shard 7)|Darabszám|Összesen||None|
|összesen7|Összes kulcs (Shard 7)|Darabszám|Maximum||None|
|lejárt kulcsok7|Lejárt kulcsok (Shard 7)|Darabszám|Összesen||None|
|használtmemória7|Használt memória (Shard 7)|Bájt|Maximum||None|
|használtmemoryRss7|Használt memória RSS (Shard 7)|Bájt|Maximum||None|
|serverLoad7|Kiszolgáló betöltése (Shard 7)|Százalék|Maximum||None|
|gyorsítótárÍrás7|Gyorsítótár írása (Shard 7)|BytesPerSecond|Maximum||None|
|cacheRead7|Gyorsítótár olvasása (7. szegmens)|BytesPerSecond|Maximum||None|
|százalékProcessorTime7|CPU (Shard 7)|Százalék|Maximum||None|
|connectedclients8|Csatlakoztatott ügyfelek (8. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozás8|Összes művelet (8. szilánk)|Darabszám|Összesen||None|
|gyorsítótár-találatok8|Gyorsítótár-találatok (8. szegmens)|Darabszám|Összesen||None|
|gyorsítótármisses8|Gyorsítótár-tévesztések (8. szegmens)|Darabszám|Összesen||None|
|getcommands8|Gets (Shard 8)|Darabszám|Összesen||None|
|setparancsok8|Készletek (Shard 8)|Darabszám|Összesen||None|
|műveletekPerSecond8|Műveletek másodpercenként (Shard 8)|Darabszám|Maximum||None|
|kilakoltatott kulcsok8|Kilakoltatott kulcsok (8. szilánk)|Darabszám|Összesen||None|
|összesen8|Összes kulcs (Shard 8)|Darabszám|Maximum||None|
|lejárt kulcsok8|Lejárt kulcsok (8. shard)|Darabszám|Összesen||None|
|használtmemória8|Használt memória (Shard 8)|Bájt|Maximum||None|
|használtmemoryRss8|Használt memória RSS (Shard 8)|Bájt|Maximum||None|
|serverLoad8|Kiszolgáló betöltése (8. szegmens)|Százalék|Maximum||None|
|gyorsítótárÍrás8|Gyorsítótár írása (8. szegmens)|BytesPerSecond|Maximum||None|
|cacheRead8|Gyorsítótár olvasása (8. szegmens)|BytesPerSecond|Maximum||None|
|százalékProcessorTime8|CPU (Shard 8)|Százalék|Maximum||None|
|connectedclients9|Csatlakoztatott ügyfelek (9. szegmens)|Darabszám|Maximum||None|
|összes parancsfeldolgozás9|Összes művelet (Shard 9)|Darabszám|Összesen||None|
|gyorsítótártalálat9|Gyorsítótár-találatok (9. szegmens)|Darabszám|Összesen||None|
|gyorsítótármisses9|Gyorsítótár-tévesztések (9.sángold)|Darabszám|Összesen||None|
|getcommands9|Gets (Shard 9)|Darabszám|Összesen||None|
|setparancsok9|Készletek (Shard 9)|Darabszám|Összesen||None|
|műveletekPerSecond9|Műveletek másodpercenként (Shard 9)|Darabszám|Maximum||None|
|kilakoltatott kulcsok9|Kilakoltatott kulcsok (9. szegmens)|Darabszám|Összesen||None|
|összesenkeys9|Összes kulcs (Shard 9)|Darabszám|Maximum||None|
|lejárt kulcsok9|Lejárt kulcsok (Shard 9)|Darabszám|Összesen||None|
|használtmemória9|Használt memória (Shard 9)|Bájt|Maximum||None|
|használtmemoryRss9|Használt memória RSS (Shard 9)|Bájt|Maximum||None|
|kiszolgálóLoad9|Kiszolgáló betöltése (Shard 9)|Százalék|Maximum||None|
|gyorsítótárÍrás9|Gyorsítótár írása (Shard 9)|BytesPerSecond|Maximum||None|
|cacheRead9|Gyorsítótár olvasása (9. szegmens)|BytesPerSecond|Maximum||None|
|százalékProcessorTime9|CPU (Shard 9)|Százalék|Maximum||None|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Webalkalmazás tűzfalának kérelmek száma|Darabszám|Összesen|A webalkalmazás-tűzfal által feldolgozott ügyfélkérelmek száma|Házirendnév,Szabálynév,Művelet|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|None|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A virtuális gép(ek) által az összes hálózati csatolón fogadott bájtok száma (bejövő forgalom).|None|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A virtuális gép(ek) (kimenő forgalom) által az összes hálózati csatolón kimenő bájtok száma.|None|
|Lemez olvasási bájtjai/mp|Lemez olvasása|BytesPerSecond|Átlag|A lemezről a figyelési időszak alatt beolvasott átlagos bájtok.|None|
|Lemezírási bájtok/mp|Lemez írása|BytesPerSecond|Átlag|A lemezre írt átlagos bájtok a figyelési időszak alatt.|None|
|Lemezolvasási műveletek/mp|Lemezolvasási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezolvasási IOPS.|None|
|Lemezírási műveletek/mp|Lemezírási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezírási IOPS.|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya.|RoleInstanceId azonosító|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A virtuális gép(ek) által az összes hálózati csatolón fogadott bájtok száma (bejövő forgalom).|RoleInstanceId azonosító|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A virtuális gép(ek) (kimenő forgalom) által az összes hálózati csatolón kimenő bájtok száma.|RoleInstanceId azonosító|
|Lemez olvasási bájtjai/mp|Lemez olvasása|BytesPerSecond|Átlag|A lemezről a figyelési időszak alatt beolvasott átlagos bájtok.|RoleInstanceId azonosító|
|Lemezírási bájtok/mp|Lemez írása|BytesPerSecond|Átlag|A lemezre írt átlagos bájtok a figyelési időszak alatt.|RoleInstanceId azonosító|
|Lemezolvasási műveletek/mp|Lemezolvasási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezolvasási IOPS.|RoleInstanceId azonosító|
|Lemezírási műveletek/mp|Lemezírási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezírási IOPS.|RoleInstanceId azonosító|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használtkapacitás|Használt kapacitás|Bájt|Átlag|Használt számla kapacitása|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|Geotípus,ApiName,Hitelesítés|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolószolgáltatásvagy a megadott API-művelet sikeres kéréseinek végpontok utáni késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity kapacitás|Blob kapacitása|Bájt|Átlag|A tárfiók Blob szolgáltatása által használt tárterület bájtban használt tárterület mennyisége.|BlobType,Szint|
|BlobCount (BlobCount)|Blobok száma|Darabszám|Átlag|A blob száma a tárfiók Blob szolgáltatásában.|BlobType,Szint|
|Konténerszám|Blob-tárolók száma|Darabszám|Átlag|A tárolók száma a tárfiók Blob szolgáltatásában.|None|
|Indexkapacitás|Index kapacitása|Bájt|Átlag|Az ADLS Gen2 (hierarchikus) index által használt tárterület bájtban megadott mennyisége.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|Geotípus,ApiName,Hitelesítés|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolószolgáltatásvagy a megadott API-művelet sikeres kéréseinek végpontok utáni késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Táblakapacitás|Tábla kapacitása|Bájt|Átlag|A tárfiók Table szolgáltatása által használt tárterület által használt tárterület bájtban felhasznált tárterület mennyisége.|None|
|Tábladarabszám|Táblaszáma|Darabszám|Átlag|A tárfiók Table szolgáltatástáblájának száma.|None|
|Táblaentitásszáma|Táblaentitások száma|Darabszám|Átlag|A tárfiók Table szolgáltatásában lévő táblaentitások száma.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|Geotípus,ApiName,Hitelesítés|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolószolgáltatásvagy a megadott API-művelet sikeres kéréseinek végpontok utáni késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Fájlkapacitás|Fájl kapacitása|Bájt|Átlag|A tárfiók fájlszolgáltatása által használt tárterület bájtban használt tárterület mennyisége.|Fájlmegosztás|
|Fájlszám|Fájlok száma|Darabszám|Átlag|A tárfiók Fájlszolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|Fájlmegosztások száma|Darabszám|Átlag|A tárfiók Fájlszolgáltatásában lévő fájlmegosztások száma.|None|
|FileShareSnapshotCount|Fájlmegosztáspillanatkép száma|Darabszám|Átlag|A tárfiók Fájlszolgáltatásában a megosztáson jelen lévő pillanatképek száma.|Fájlmegosztás|
|Fájlmegosztássnapshotmérete|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a tárfiók fájlszolgáltatásában bájtokban használt tárképek által használt tárterület mennyisége.|Fájlmegosztás|
|FileSharequota fájlmegosztás|Fájlmegosztási kvóta mérete|Bájt|Átlag|Az Azure Files Service által bájtban használható tárterület összegének felső határa.|Fájlmegosztás|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés,Fájlmegosztás|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolószolgáltatásvagy a megadott API-művelet sikeres kéréseinek végpontok utáni késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Várólistakapacitás|Várólista kapacitása|Bájt|Átlag|A tárfiók várólistaszolgáltatása által használt tárház készletei által használt tárterület bájtban.|None|
|Várólistaszáma|Várólista száma|Darabszám|Átlag|A tárfiók várólista-szolgáltatásában lévő várólista száma.|None|
|QueueMessageCount (Üzenetsor)|Várólista-üzenetek száma|Darabszám|Átlag|A tárfiók várólista-szolgáltatásában lévő várólista-üzenetek hozzávetőleges száma.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt késés ezredmásodpercben. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést.|Geotípus,ApiName,Hitelesítés|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A tárolószolgáltatásvagy a megadott API-művelet sikeres kéréseinek végpontok utáni késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/fiókok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Összes hívás|Összes hívás|Darabszám|Összesen|A hívások száma összesen.|Apiname,Műveletnév,Terület|
|Sikeres hívások|Sikeres hívások|Darabszám|Összesen|A sikeres hívások száma.|Apiname,Műveletnév,Terület|
|Összes hiba|Összes hiba|Darabszám|Összesen|A hibaválaszt megválaszoló hívások teljes száma (HTTP-válaszkód: 4xx vagy 5xx).|Apiname,Műveletnév,Terület|
|Blokkolt hívások|Blokkolt hívások|Darabszám|Összesen|A túllépett hívások száma vagy a kvótakorlát.|Apiname,Műveletnév,Terület|
|Kiszolgálóhibák|Kiszolgálói hibák|Darabszám|Összesen|A szolgáltatás belső hibával rendelkező hívások száma (HTTP-válaszkód 5xx).|Apiname,Műveletnév,Terület|
|Ügyfélhibák|Ügyfélhibák|Darabszám|Összesen|Ügyféloldali hibával rendelkező hívások száma (HTTP-válaszkód 4xx).|Apiname,Műveletnév,Terület|
|Datain között|Adatok a|Bájt|Összesen|Bejövő adatok mérete bájtban.|Apiname,Műveletnév,Terület|
|DataOut (Adatki- és a|Adatok ki|Bájt|Összesen|Kimenő adatok mérete bájtban.|Apiname,Műveletnév,Terület|
|Késés|Késés|Ezredmásodperc|Átlag|Késés ezredmásodpercben.|Apiname,Műveletnév,Terület|
|TotalTokenCalls|Tokenhívások összesen|Darabszám|Összesen|A tokenhívások teljes száma.|Apiname,Műveletnév,Terület|
|Lefordított karakterek|Lefordított karakterek|Darabszám|Összesen|A bejövő szöveges kérelemben szereplő karakterek teljes száma.|Apiname,Műveletnév,Terület|
|Karakterekbe képezve|Betanított karakterek|Darabszám|Összesen|Betanított karakterek teljes száma.|Apiname,Műveletnév,Terület|
|SpeechSessionDuration|Beszédmunkamenet időtartama|Másodperc|Összesen|A beszédszakasz teljes időtartama másodpercben.|Apiname,Műveletnév,Terület|
|Összes tranzakció|Összes tranzakció|Darabszám|Összesen|Tranzakciók teljes száma.|None|
|Feldolgozott képek|Feldolgozott képek|Darabszám|Összesen| A képfeldolgozáshoz felelős tranzakciók száma.|Apiname,szolgáltatásnév,csatorna,régió|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|None|
|Bejövő hálózat|Hálózat számlázható (elavult)|Bájt|Összesen|A virtuális gép(ek) (bejövő forgalom) által az összes hálózati csatolón fogadott számlázható bájtok száma (elavult)|None|
|Kimenő hálózat|Hálózat számlázható (elavult)|Bájt|Összesen|A virtuális gép(ek) (kimenő forgalom) által az összes hálózati csatolón kiszámlázott bájtok száma (elavult)|None|
|Lemezolvasási bájtok|Lemezolvasási bájtok|Bájt|Összesen|Lemezről beolvasott bájtok a figyelési időszak alatt|None|
|Lemezírási bájtok|Lemezírási bájtok|Bájt|Összesen|Lemezre írt bájtok a figyelési időszak alatt|None|
|Lemezolvasási műveletek/mp|Lemezolvasási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezolvasási IOPS|None|
|Lemezírási műveletek/mp|Lemezírási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezírási IOPS|None|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Darabszám|Átlag|A felszakításra rendelkezésre álló kreditek teljes száma|None|
|Cpu-kreditek felhasznált|Cpu-kreditek felhasznált|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|Lemezolvasási bájt/mp|Adatlemez olvasási bájt/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/sec a figyelési időszak alatt|SlotId között|
|Lemezenkénti írási bájt/mp|Adatlemez írási bájt/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása a figyelési időszak alatt|SlotId között|
|Lemezolvasási műveletek/mp|Adatlemez olvasási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről a figyelési időszak alatt|SlotId között|
|Lemezírási műveletek/mp|Adatlemez írási [műveletei/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről a figyelési időszak alatt|SlotId között|
|Lemezenkénti qd|Adatlemez –QD [(elavult)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|Adatlemez-várólista mélysége(vagy várólista hossza)|SlotId között|
|Az operációs rendszer lemezenkénti olvasási bájt/mp|Operációs rendszer lemezolvasási [bájtjai/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/mp-es olvasása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenként i.e.|Operációs rendszer lemezírási [bájtjai/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Az operációs rendszer lemezenkénti olvasási műveletei/mp|Operációs rendszer lemezolvasási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenkéntírási műveletek másodpercenkénti száma|Operációs rendszer lemezírási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenként – QD|Operációs rendszer lemezének [qd-ja (elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|Az operációs rendszer lemezvárólistájának mélysége(vagy a várólista hossza)|None|
|Adatlemez olvasási bájtjai/mp|Adatlemez olvasási bájtjai/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/sec a figyelési időszak alatt|Lun|
|Adatlemez írási bájt/mp|Adatlemez írási bájtjai/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása a figyelési időszak alatt|Lun|
|Adatlemez olvasási műveletei/mp|Adatlemez olvasási műveletei/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről a figyelési időszak alatt|Lun|
|Adatlemez írási műveletei/mp|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről a figyelési időszak alatt|Lun|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez-várólista mélysége(vagy várólista hossza)|Lun|
|Operációs rendszer lemezolvasási bájtjai/mp|Operációs rendszer lemezolvasási bájtjainak másodpercenkénti száma (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/mp-es olvasása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezének írása bájt/mp|Operációs rendszer lemezének írása másodpercenkénti bájtjai (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezolvasási műveletei/mp|Operációs rendszer lemezolvasási műveleteinek másodpercenkénti száma (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezírási műveletei/mp|Operációs rendszer lemezírási műveleteinek/mp-je (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|OS-lemez várakozási sorának mélysége|Az operációs rendszer lemezvárólistájának mélysége (előzetes verzió)|Darabszám|Átlag|Az operációs rendszer lemezvárólistájának mélysége(vagy a várólista hossza)|None|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Darabszám|Átlag|Bejövő folyamatok száma a bejövő irányban (a virtuális gépbe haladó forgalom)|None|
|Kimenő folyamatok|Kimenő folyamatok|Darabszám|Átlag|Kimenő folyamatok a kimenő irányban lévő áramok száma (a virtuális gépből kimenő forgalom)|None|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya|CountPerSzekszekundum|Átlag|A bejövő forgalom maximális létrehozási sebessége (a virtuális gépbe érkező forgalom)|None|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya|CountPerSzekszekundum|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépből kimenő forgalom)|None|
|Prémium szintű adatlemez gyorsítótárának olvasási találata|Prémium szintű adatlemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási találata|Lun|
|Prémium szintű adatlemez gyorsítótárolvasási tévesztése|Prémium szintű adatlemez gyorsítótárolvasási tévesztés (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárolvasási tévesztése|Lun|
|Prémium szintű operációsrendszer-gyorsítótár olvasási találata|Prémium szintű operációsrendszer-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű operációsrendszer-gyorsítótár olvasási találata|None|
|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése (előzetes verzió)|Százalék|Átlag|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése|None|
|Hálózat összesen|Hálózat összesen|Bájt|Összesen|A virtuális gép(ek) által az összes hálózati csatolón fogadott bájtok száma (bejövő forgalom)|None|
|Hálózat kiteljesedése összesen|Hálózat kiteljesedése összesen|Bájt|Összesen|A virtuális gép(ek) (kimenő forgalom) által az összes hálózati csatolón kimenő bájtok száma|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|VMName|
|Bejövő hálózat|Hálózat számlázható (elavult)|Bájt|Összesen|A virtuális gép(ek) (bejövő forgalom) által az összes hálózati csatolón fogadott számlázható bájtok száma (elavult)|VMName|
|Kimenő hálózat|Hálózat számlázható (elavult)|Bájt|Összesen|A virtuális gép(ek) (kimenő forgalom) által az összes hálózati csatolón kiszámlázott bájtok száma (elavult)|VMName|
|Lemezolvasási bájtok|Lemezolvasási bájtok|Bájt|Összesen|Lemezről beolvasott bájtok a figyelési időszak alatt|VMName|
|Lemezírási bájtok|Lemezírási bájtok|Bájt|Összesen|Lemezre írt bájtok a figyelési időszak alatt|VMName|
|Lemezolvasási műveletek/mp|Lemezolvasási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezolvasási IOPS|VMName|
|Lemezírási műveletek/mp|Lemezírási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezírási IOPS|VMName|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Darabszám|Átlag|A felszakításra rendelkezésre álló kreditek teljes száma|None|
|Cpu-kreditek felhasznált|Cpu-kreditek felhasznált|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|Lemezolvasási bájt/mp|Adatlemez olvasási bájt/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/sec a figyelési időszak alatt|SlotId között|
|Lemezenkénti írási bájt/mp|Adatlemez írási bájt/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása a figyelési időszak alatt|SlotId között|
|Lemezolvasási műveletek/mp|Adatlemez olvasási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről a figyelési időszak alatt|SlotId között|
|Lemezírási műveletek/mp|Adatlemez írási [műveletei/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről a figyelési időszak alatt|SlotId között|
|Lemezenkénti qd|Adatlemez –qd [(elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|Adatlemez-várólista mélysége(vagy várólista hossza)|SlotId között|
|Az operációs rendszer lemezenkénti olvasási bájt/mp|Operációs rendszer lemezolvasási [bájtjai/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/mp-es olvasása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenként i.e.|Operációs rendszer lemezírási [bájtjai/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Az operációs rendszer lemezenkénti olvasási műveletei/mp|Operációs rendszer lemezolvasási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenkéntírási műveletek másodpercenkénti száma|Operációs rendszer lemezírási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenként – QD|Operációs rendszer lemezének [qd-ja (elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|Az operációs rendszer lemezvárólistájának mélysége(vagy a várólista hossza)|None|
|Adatlemez olvasási bájtjai/mp|Adatlemez olvasási bájtjai/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/sec a figyelési időszak alatt|Névn.|
|Adatlemez írási bájt/mp|Adatlemez írási bájtjai/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása a figyelési időszak alatt|Névn.|
|Adatlemez olvasási műveletei/mp|Adatlemez olvasási műveletei/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről a figyelési időszak alatt|Névn.|
|Adatlemez írási műveletei/mp|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről a figyelési időszak alatt|Névn.|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez-várólista mélysége(vagy várólista hossza)|Névn.|
|Operációs rendszer lemezolvasási bájtjai/mp|Operációs rendszer lemezolvasási bájtjainak másodpercenkénti száma (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/mp-es olvasása az operációs rendszer lemezének figyelési időszaka alatt|VMName|
|Operációs rendszer lemezének írása bájt/mp|Operációs rendszer lemezének írása másodpercenkénti bájtjai (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása az operációs rendszer lemezének figyelési időszaka alatt|VMName|
|Operációs rendszer lemezolvasási műveletei/mp|Operációs rendszer lemezolvasási műveleteinek másodpercenkénti száma (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|VMName|
|Operációs rendszer lemezírási műveletei/mp|Operációs rendszer lemezírási műveleteinek/mp-je (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|VMName|
|OS-lemez várakozási sorának mélysége|Az operációs rendszer lemezvárólistájának mélysége (előzetes verzió)|Darabszám|Átlag|Az operációs rendszer lemezvárólistájának mélysége(vagy a várólista hossza)|VMName|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Darabszám|Átlag|Bejövő folyamatok száma a bejövő irányban (a virtuális gépbe haladó forgalom)|VMName|
|Kimenő folyamatok|Kimenő folyamatok|Darabszám|Átlag|Kimenő folyamatok a kimenő irányban lévő áramok száma (a virtuális gépből kimenő forgalom)|VMName|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya|CountPerSzekszekundum|Átlag|A bejövő forgalom maximális létrehozási sebessége (a virtuális gépbe érkező forgalom)|VMName|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya|CountPerSzekszekundum|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépből kimenő forgalom)|VMName|
|Prémium szintű adatlemez gyorsítótárának olvasási találata|Prémium szintű adatlemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási találata|Névn.|
|Prémium szintű adatlemez gyorsítótárolvasási tévesztése|Prémium szintű adatlemez gyorsítótárolvasási tévesztés (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárolvasási tévesztése|Névn.|
|Prémium szintű operációsrendszer-gyorsítótár olvasási találata|Prémium szintű operációsrendszer-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű operációsrendszer-gyorsítótár olvasási találata|VMName|
|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése (előzetes verzió)|Százalék|Átlag|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése|VMName|
|Hálózat összesen|Hálózat összesen|Bájt|Összesen|A virtuális gép(ek) által az összes hálózati csatolón fogadott bájtok száma (bejövő forgalom)|VMName|
|Hálózat kiteljesedése összesen|Hálózat kiteljesedése összesen|Bájt|Összesen|A virtuális gép(ek) (kimenő forgalom) által az összes hálózati csatolón kimenő bájtok száma|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A virtuális gép(ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya|None|
|Bejövő hálózat|Hálózat számlázható (elavult)|Bájt|Összesen|A virtuális gép(ek) (bejövő forgalom) által az összes hálózati csatolón fogadott számlázható bájtok száma (elavult)|None|
|Kimenő hálózat|Hálózat számlázható (elavult)|Bájt|Összesen|A virtuális gép(ek) (kimenő forgalom) által az összes hálózati csatolón kiszámlázott bájtok száma (elavult)|None|
|Lemezolvasási bájtok|Lemezolvasási bájtok|Bájt|Összesen|Lemezről beolvasott bájtok a figyelési időszak alatt|None|
|Lemezírási bájtok|Lemezírási bájtok|Bájt|Összesen|Lemezre írt bájtok a figyelési időszak alatt|None|
|Lemezolvasási műveletek/mp|Lemezolvasási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezolvasási IOPS|None|
|Lemezírási műveletek/mp|Lemezírási műveletek/mp|CountPerSzekszekundum|Átlag|Lemezírási IOPS|None|
|Fennmaradó CPU-kreditek|Fennmaradó CPU-kreditek|Darabszám|Átlag|A felszakításra rendelkezésre álló kreditek teljes száma|None|
|Cpu-kreditek felhasznált|Cpu-kreditek felhasznált|Darabszám|Átlag|A virtuális gép által felhasznált kreditek teljes száma|None|
|Lemezolvasási bájt/mp|Adatlemez olvasási bájt/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/sec a figyelési időszak alatt|SlotId között|
|Lemezenkénti írási bájt/mp|Adatlemez írási bájt/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása a figyelési időszak alatt|SlotId között|
|Lemezolvasási műveletek/mp|Adatlemez olvasási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről a figyelési időszak alatt|SlotId között|
|Lemezírási műveletek/mp|Adatlemez írási [műveletei/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről a figyelési időszak alatt|SlotId között|
|Lemezenkénti qd|Adatlemez –qd [(elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|Adatlemez-várólista mélysége(vagy várólista hossza)|SlotId között|
|Az operációs rendszer lemezenkénti olvasási bájt/mp|Operációs rendszer lemezolvasási [bájtjai/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/mp-es olvasása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenként i.e.|Operációs rendszer lemezírási [bájtjai/mp(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Az operációs rendszer lemezenkénti olvasási műveletei/mp|Operációs rendszer lemezolvasási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenkéntírási műveletek másodpercenkénti száma|Operációs rendszer lemezírási műveletei/mp [(elavult)](portal-disk-metrics-deprecation.md)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezenként – QD|Operációs rendszer lemezének [qd-ja (elavult)](portal-disk-metrics-deprecation.md)|Darabszám|Átlag|Az operációs rendszer lemezvárólistájának mélysége(vagy a várólista hossza)|None|
|Adatlemez olvasási bájtjai/mp|Adatlemez olvasási bájtjai/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/sec a figyelési időszak alatt|Lun|
|Adatlemez írási bájt/mp|Adatlemez írási bájtjai/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása a figyelési időszak alatt|Lun|
|Adatlemez olvasási műveletei/mp|Adatlemez olvasási műveletei/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről a figyelési időszak alatt|Lun|
|Adatlemez írási műveletei/mp|Adatlemez írási műveletei/mp (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről a figyelési időszak alatt|Lun|
|Adatlemez várakozási sorának mélysége|Adatlemez-várólista mélysége (előzetes verzió)|Darabszám|Átlag|Adatlemez-várólista mélysége(vagy várólista hossza)|Lun|
|Operációs rendszer lemezolvasási bájtjai/mp|Operációs rendszer lemezolvasási bájtjainak másodpercenkénti száma (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezről beolvasott bájtok/mp-es olvasása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezének írása bájt/mp|Operációs rendszer lemezének írása másodpercenkénti bájtjai (előzetes verzió)|CountPerSzekszekundum|Átlag|Egyetlen lemezre írt bájtok/mp-es írása az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezolvasási műveletei/mp|Operációs rendszer lemezolvasási műveleteinek másodpercenkénti száma (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS olvasása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|Operációs rendszer lemezírási műveletei/mp|Operációs rendszer lemezírási műveleteinek/mp-je (előzetes verzió)|CountPerSzekszekundum|Átlag|IOPS írása egyetlen lemezről az operációs rendszer lemezének figyelési időszaka alatt|None|
|OS-lemez várakozási sorának mélysége|Az operációs rendszer lemezvárólistájának mélysége (előzetes verzió)|Darabszám|Átlag|Az operációs rendszer lemezvárólistájának mélysége(vagy a várólista hossza)|None|
|Bejövő forgalomfolyamok|Bejövő forgalomfolyamok|Darabszám|Átlag|Bejövő folyamatok száma a bejövő irányban (a virtuális gépbe haladó forgalom)|None|
|Kimenő folyamatok|Kimenő folyamatok|Darabszám|Átlag|Kimenő folyamatok a kimenő irányban lévő áramok száma (a virtuális gépből kimenő forgalom)|None|
|Bejövő folyamatok maximális létrehozási aránya|Bejövő folyamatok maximális létrehozási aránya|CountPerSzekszekundum|Átlag|A bejövő forgalom maximális létrehozási sebessége (a virtuális gépbe érkező forgalom)|None|
|Kimenő folyamatok maximális létrehozási aránya|Kimenő folyamatok maximális létrehozási aránya|CountPerSzekszekundum|Átlag|A kimenő folyamatok maximális létrehozási sebessége (a virtuális gépből kimenő forgalom)|None|
|Prémium szintű adatlemez gyorsítótárának olvasási találata|Prémium szintű adatlemez gyorsítótárának olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárának olvasási találata|Lun|
|Prémium szintű adatlemez gyorsítótárolvasási tévesztése|Prémium szintű adatlemez gyorsítótárolvasási tévesztés (előzetes verzió)|Százalék|Átlag|Prémium szintű adatlemez gyorsítótárolvasási tévesztése|Lun|
|Prémium szintű operációsrendszer-gyorsítótár olvasási találata|Prémium szintű operációsrendszer-gyorsítótár olvasási találata (előzetes verzió)|Százalék|Átlag|Prémium szintű operációsrendszer-gyorsítótár olvasási találata|None|
|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése (előzetes verzió)|Százalék|Átlag|Prémium szintű operációsrendszer-lemezgyorsítótár olvasási tévesztése|None|
|Hálózat összesen|Hálózat összesen|Bájt|Összesen|A virtuális gép(ek) által az összes hálózati csatolón fogadott bájtok száma (bejövő forgalom)|None|
|Hálózat kiteljesedése összesen|Hálózat kiteljesedése összesen|Bájt|Összesen|A virtuális gép(ek) (kimenő forgalom) által az összes hálózati csatolón kimenő bájtok száma|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuUsage (CpuUsage)|Processzorhasználat|Darabszám|Átlag|A processzorhasználat minden magon millicore-ban.|containerName (tárolóneve)|
|Memóriahasználat|Memóriahasználat|Bájt|Átlag|Teljes memóriahasználat bájtban.|containerName (tárolóneve)|
|NetworkBytesReceivedPerSecond|Másodpercenként fogadott hálózati bájtok|Bájt|Átlag|A másodpercenként fogadott hálózati bájtok.|None|
|NetworkBytesTransmittedPerSecond|Másodpercenként küldött hálózati bájtok|Bájt|Átlag|A másodpercenként küldött hálózati bájtok.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/regisztika

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalPullCount (Összes pullcount)|Teljes lekérések száma|Darabszám|Átlag|Képlekérések száma összesen|None|
|SuccessfulPullCount|Sikeres lekéréses szám|Darabszám|Átlag|Sikeres képlekérések száma|None|
|ÖsszesenPushCount|Összes leküldéses szám|Darabszám|Átlag|Képleküldések száma összesen|None|
|SuccessfulPushCount|Sikeres leküldéses szám|Darabszám|Átlag|Sikeres lemezleküldések száma|None|
|RunDuration|Futtatás időtartama|Ezredmásodperc|Összesen|Futtatás időtartama ezredmásodpercben|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Felügyelt fürtben rendelkezésre álló processzormagok teljes száma|Darabszám|Átlag|Felügyelt fürtben rendelkezésre álló processzormagok teljes száma|None|
|kube_node_status_allocatable_memory_bytes|Felügyelt fürtben rendelkezésre álló memória teljes mennyisége|Bájt|Átlag|Felügyelt fürtben rendelkezésre álló memória teljes mennyisége|None|
|kube_pod_status_ready|Podok száma kész állapotban|Darabszám|Átlag|Podok száma kész állapotban|névtér,pod|
|kube_node_status_condition|Különböző csomóponti feltételek állapotai|Darabszám|Átlag|Különböző csomóponti feltételek állapotai|feltétel,állapot,állapot2,csomópont|
|kube_pod_status_phase|A hüvelyek száma fázisonként|Darabszám|Átlag|A hüvelyek száma fázisonként|fázis,névtér,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Sikeres kérések|Sikeres kérelmek|Darabszám|Összesen|Az egyéni szolgáltató sikeres kérései|HttpMetódus,CallPath,StatusCode|
|Sikertelen kérések|Sikertelen kérelmek|Darabszám|Összesen|Az egyéni erőforrás-szolgáltatók számára rendelkezésre álló naplók beszerzése|HttpMetódus,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|NICReadThroughput|Olvasási átviteli -átmenő (hálózat)|BytesPerSecond|Átlag|Az eszköz hálózati illesztőjének olvasási átviteli hatása a jelentési időszakban az átjáró összes kötetére vonatkozóan.|Példánynév|
|NICWriteThroughput|Írási átviteli -áteresztő (hálózat)|BytesPerSecond|Átlag|A hálózati adapter írási átviteli fazekadusa az eszközön a jelentési időszakban az átjáró összes kötetére vonatkozóan.|Példánynév|
|CloudReadThroughputPerShare|Felhőbeli letöltési átviteli (megosztás)|BytesPerSecond|Átlag|A letöltési átviteli-átahelyezett azure-ba egy megosztása a jelentéskészítési időszak alatt.|Megosztás|
|CloudUploadThroughputPerShare|Felhőbeli feltöltési átviteli -átmenő (megosztás)|BytesPerSecond|Átlag|A feltöltési átviteli-átaz Azure-ba egy megosztása a jelentéskészítési időszak alatt.|Megosztás|
|BytesUploadedToCloudPerShare|Feltöltött felhőalapú bájtok (megosztás)|Bájt|Átlag|Az Azure-ba a jelentéskészítési időszak alatt feltöltött bájtok teljes száma.|Megosztás|
|Teljes kapacitás|Teljes kapacitás|Bájt|Átlag|Teljes kapacitás|None|
|Elérhető kapacitás|Rendelkezésre álló kapacitás|Bájt|Átlag|A jelentési időszak alatt bájtban rendelkezésre álló kapacitás.|None|
|CloudUploadThroughput (Felhőfeltöltési átput)|Felhőbeli feltöltési átviteli|BytesPerSecond|Átlag|A felhőfeltöltési átviteli-átmenő az Azure-ba a jelentéskészítési időszak alatt.|None|
|CloudReadThroughput (Felhőolvasás - Átput|Felhőbeli letöltési átviteli|BytesPerSecond|Átlag|A felhőletöltési átviteli-idő az Azure-ba a jelentéskészítési időszak alatt.|None|
|BytesUploadedToCloud|Feltöltött felhőalapú bájtok (eszköz)|Bájt|Átlag|Az Azure-ból a jelentéskészítési időszak alatt feltöltött bájtok teljes száma.|None|
|HyperVVirtualProcessorKitilizálás|Peremhálózati számítás – százalékos CPU|Százalék|Átlag|Processzor használatának százalékos százaléka|Példánynév|
|HyperVMemoryKitilizáció|Peremhálózati számítás – Memóriahasználat|Százalék|Átlag|A használatban lévő RAM mennyisége|Példánynév|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Eszköz felosztása osztályozás szerint|Eszköz felosztása osztályozás szerint|Darabszám|Összesen|A meghatározott besorolással rendelkező eszközök számát jelzi, azaz az adott címkével vannak besorolva.|Besorolás,Forrás|
|Eszközfelosztástárolótípusa|Eszköz elosztása tárolási típus szerint|Darabszám|Összesen|Egy adott tárolási típussal rendelkező eszközök számát jelzi.|StorageType típusú|
|NumberOfAssetsWithClassifications|Legalább egy besorolással rendelkező eszközök száma|Darabszám|Átlag|A legalább egy címkebesorolással rendelkező eszközök számát jelzi.|None|
|VizsgálatTörölve|Vizsgálat megszakítva|Darabszám|Összesen|A megszakított vizsgálatok számát jelzi.|None|
|Vizsgálat befejezve|Vizsgálat befejezve|Darabszám|Összesen|A sikeresen végrehajtott vizsgálatok számát jelzi.|None|
|Sikertelen vizsgálat|A vizsgálat nem sikerült|Darabszám|Összesen|A sikertelen beolvasások számát jelzi.|None|
|ScanTimeTaken|A beszkavaltöltött idő|Másodperc|Összesen|A teljes bevizsgálati időt jelzi másodpercben.|None|
|CatalogActiveUsers|Napi aktív felhasználók|Darabszám|Összesen|Aktív felhasználók száma naponta|None|
|Katalógushasználata|Használat elosztása művelet szerint|Darabszám|Összesen|Adja meg, hogy a felhasználó hány műveletet végez a katalógusban, azaz az Access, a Keresés, a Szószedet.|Művelet|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Sikertelen futtatások|Sikertelen futtatások|Darabszám|Összesen||pipelineName,activityName|
|SuccessfulRuns (Sikeres futtatások)|Sikeres futtatások|Darabszám|Összesen||pipelineName,activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/gyárak

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PipelineFailedRuns|Sikertelen folyamat futtatási metrikák|Darabszám|Összesen||FailureType,Név|
|PipelineSucceededRuns között|Sikeres folyamatfuttatási metrikák|Darabszám|Összesen||FailureType,Név|
|PipelineCancelledRuns|Megszakított folyamatfuttatási mérőszámok|Darabszám|Összesen||FailureType,Név|
|ActivityFailedRuns|A sikertelen tevékenység mérőszámokat futtat|Darabszám|Összesen||ActivityType,Csővezetéknév,Hibatípus,Név|
|ActivitySucceededRuns között|A sikeres tevékenység mérőszámait futtatja|Darabszám|Összesen||ActivityType,Csővezetéknév,Hibatípus,Név|
|TevékenységÉrvénytelenítvefuttatások|A megszakított tevékenység futtatási mutatói|Darabszám|Összesen||ActivityType,Csővezetéknév,Hibatípus,Név|
|TriggerFailedRuns|Sikertelen eseményindító futtatási mérőszámai|Darabszám|Összesen||Név,FailureType|
|Aktiválássucceededruns|Sikeres eseményindító-futtatási mérőszámok|Darabszám|Összesen||Név,FailureType|
|TriggerCancelledRuns|Megszakított eseményindító-futtatási mérőszámok|Darabszám|Összesen||Név,FailureType|
|IntegrationRuntimeCpuSzázalék|Integrációs futásidejű CPU-kihasználtság|Százalék|Átlag||IntegrationRuntimeName,NodeName|
|IntegrationRuntimeAvailableMemory|Integrációs futásidejű elérhető memória|Bájt|Átlag||IntegrationRuntimeName,NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Integrációs futásidejű várólista időtartama|Másodperc|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Integrációs futásidejű várólista hossza|Darabszám|Átlag||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Integrációs futásidejű elérhető csomópontszám|Darabszám|Átlag||IntegrationRuntimeName|
|MaxAllowedResourceCount (MaxAllowedResourceCount)|A megengedett entitások maximális száma|Darabszám|Maximum||None|
|MaxallowedfactorySizeIngbunits|A gyár maximálisan megengedett mérete (GB egység)|Darabszám|Maximum||None|
|Erőforrásszáma|Entitások száma összesen|Darabszám|Maximum||None|
|FactorySizeIngbunits|Teljes gyárméret (GB egység)|Darabszám|Maximum||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/fiókok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|JobEndedSiker|Sikeres feladatok|Darabszám|Összesen|Sikeres feladatok száma.|None|
|JobEndedFailure (Feladat– Hiba)|Sikertelen feladatok|Darabszám|Összesen|Sikertelen feladatok száma.|None|
|JobEndedTörölve|Törölt feladatok|Darabszám|Összesen|A törölt feladatok száma.|None|
|JobAUEndedsiker|Sikeres AU idő|Másodperc|Összesen|A sikeres feladatok hoz a teljes AU idő.|None|
|JobAUEndedFailure|Sikertelen AU idő|Másodperc|Összesen|A sikertelen feladatok összes AU ideje.|None|
|JobAUEndedTörölve|Megszakított AU idő|Másodperc|Összesen|A törölt feladatok összes AU ideje.|None|
|Feladatszínpad|Állások a színpadon|Darabszám|Összesen|A feladatok száma az egyes fázisokban.|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/fiókok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|TotalStorage (Összes tárhely) tárolása|Összes tárhely|Bájt|Maximum|A fiókban tárolt adatok teljes mennyisége.|None|
|Adatokírás|Írt adatok|Bájt|Összesen|A számlára írt adatok teljes mennyisége.|None|
|DataRead (Olvasd el)|Adatok olvasása|Bájt|Összesen|A fiókból beolvasott adatok teljes mennyisége.|None|
|WriteRequests (Íráskérések)|Írási kérelmek|Darabszám|Összesen|A fiókhoz érkező adatírási kérelmek száma.|None|
|Olvasáskérések|Olvasási kérelmek|Darabszám|Összesen|A fiókhoz intézett adatok olvasási kérelmeinek száma.|None|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/fiókok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ShareCount (ShareCount)|Elküldött megosztások|Darabszám|Maximum|A számlán küldött részvények száma|Megosztásneve|
|ShareSubscriptionCount|Kapott részvények|Darabszám|Maximum|A számlán kapott részvények száma|ShareSubscriptionName|
|Sikeres megosztások|Elküldött megosztási sikeres pillanatképek|Darabszám|Darabszám|A fiókban az elküldött megosztás sikeres pillanatképeinek száma|None|
|Sikertelen megosztásszinkronizálások|Sikertelen megosztási pillanatképek elküldése|Darabszám|Darabszám|Az elküldött megosztás sikertelen pillanatképeinek száma a fiókban|None|
|SucceededShareSubscriptionSynchrizations|Fogadott megosztás sikeres pillanatképei|Darabszám|Darabszám|A fogadott megosztás sikeres pillanatképeinek száma a fiókban|None|
|SikertelenShareSubscription-szinkronizálások|Sikertelen megosztáspillanatképek fogadása|Darabszám|Darabszám|Sikertelen pillanatképek fogadásának száma a fiókban|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/kiszolgálók

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|PROCESSZOR százalék|Százalék|Átlag|PROCESSZOR százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|io_consumption_percent|IO százalék|Százalék|Átlag|IO százalék|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Használt tároló|Bájt|Átlag|Használt tároló|None|
|storage_limit|Tárolási korlát|Bájt|Maximum|Tárolási korlát|None|
|serverlog_storage_percent|Kiszolgálói napló tárolási százaléka|Százalék|Átlag|Kiszolgálói napló tárolási százaléka|None|
|serverlog_storage_usage|Használt kiszolgálónapló-tároló|Bájt|Átlag|Használt kiszolgálónapló-tároló|None|
|serverlog_storage_limit|Kiszolgálónapló tárolási korlátja|Bájt|Átlag|Kiszolgálónapló tárolási korlátja|None|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|seconds_behind_master|Replikációs késleltetés másodpercben|Darabszám|Maximum|Replikációs késleltetés másodpercben|None|
|backup_storage_used|Használt biztonsági másolat tárolása|Bájt|Átlag|Használt biztonsági másolat tárolása|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózat az aktív kapcsolatok között|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/kiszolgálók

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|PROCESSZOR százalék|Százalék|Átlag|PROCESSZOR százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|io_consumption_percent|IO százalék|Százalék|Átlag|IO százalék|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Használt tároló|Bájt|Átlag|Használt tároló|None|
|storage_limit|Tárolási korlát|Bájt|Maximum|Tárolási korlát|None|
|serverlog_storage_percent|Kiszolgálói napló tárolási százaléka|Százalék|Átlag|Kiszolgálói napló tárolási százaléka|None|
|serverlog_storage_usage|Használt kiszolgálónapló-tároló|Bájt|Átlag|Használt kiszolgálónapló-tároló|None|
|serverlog_storage_limit|Kiszolgálónapló tárolási korlátja|Bájt|Maximum|Kiszolgálónapló tárolási korlátja|None|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|seconds_behind_master|Replikációs késleltetés másodpercben|Darabszám|Maximum|Replikációs késleltetés másodpercben|None|
|backup_storage_used|Használt biztonsági másolat tárolása|Bájt|Átlag|Használt biztonsági másolat tárolása|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózat az aktív kapcsolatok között|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/szerverek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|PROCESSZOR százalék|Százalék|Átlag|PROCESSZOR százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|io_consumption_percent|IO százalék|Százalék|Átlag|IO százalék|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Használt tároló|Bájt|Átlag|Használt tároló|None|
|storage_limit|Tárolási korlát|Bájt|Maximum|Tárolási korlát|None|
|serverlog_storage_percent|Kiszolgálói napló tárolási százaléka|Százalék|Átlag|Kiszolgálói napló tárolási százaléka|None|
|serverlog_storage_usage|Használt kiszolgálónapló-tároló|Bájt|Átlag|Használt kiszolgálónapló-tároló|None|
|serverlog_storage_limit|Kiszolgálónapló tárolási korlátja|Bájt|Maximum|Kiszolgálónapló tárolási korlátja|None|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|backup_storage_used|Használt biztonsági másolat tárolása|Bájt|Átlag|Használt biztonsági másolat tárolása|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózat az aktív kapcsolatok között|None|
|pg_replica_log_delay_in_seconds|Replika késés|Másodperc|Maximum|Replika késleltetés másodpercben|None|
|pg_replica_log_delay_in_bytes|Maximális késés a replikák között|Bájt|Maximum|A legelmaradóbb replika bájtjainak késése|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|PROCESSZOR százalék|Százalék|Átlag|PROCESSZOR százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|iops|IOPS|Darabszám|Átlag|Io műveletek másodpercenként|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Használt tároló|Bájt|Átlag|Használt tároló|None|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózat az aktív kapcsolatok között|None|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|PROCESSZOR százalék|Százalék|Átlag|PROCESSZOR százalék|None|
|memory_percent|Memória százaléka|Százalék|Átlag|Memória százaléka|None|
|iops|IOPS|Darabszám|Átlag|Io műveletek másodpercenként|None|
|storage_percent|Tárolási százalék|Százalék|Átlag|Tárolási százalék|None|
|storage_used|Használt tároló|Bájt|Átlag|Használt tároló|None|
|active_connections|Aktív kapcsolatok|Darabszám|Átlag|Aktív kapcsolatok|None|
|network_bytes_egress|Kimenő hálózat|Bájt|Összesen|Hálózati kapcsolat az aktív kapcsolatok között|None|
|network_bytes_ingress|Bejövő hálózat|Bájt|Összesen|Hálózat az aktív kapcsolatok között|None|
|connections_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|connections_succeeded|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|None|
|maximum_used_transactionIDs|Maximálisan használt tranzakcióazonosítók|Darabszám|Átlag|Maximálisan használt tranzakcióazonosítók|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|d2c.telemettry.ingress.allProtocol|Telemetriai üzenet küldési kísérletei|Darabszám|Összesen|Az IoT-központba küldeni próbált eszközről felhőbe irányuló telemetriai üzenetek száma|None|
|d2c.telemetry.ingress.success|Elküldött telemetriai üzenetek|Darabszám|Összesen|Az IoT hubra küldött eszközök ről a felhőbe irányuló telemetriai üzenetek száma|None|
|c2d.commands.egress.complete.success|A C2D-üzenetek kézbesítése befejeződött|Darabszám|Összesen|Az eszköz által sikeresen végrehajtott felhőből eszközre irányuló üzenetkézbesítések száma|None|
|c2d.commands.egress.abandon.success|C2D üzenetek elhagyva|Darabszám|Összesen|Az eszköz által elhagyott, felhőről eszközre irányuló üzenetek száma|None|
|c2d.commands.egress.reject.success|Elutasított C2D-üzenetek|Darabszám|Összesen|Az eszköz által elutasított, felhőről eszközre irányuló üzenetek száma|None|
|C2DMessagesExpired|C2D üzenetek lejárt (előzetes verzió)|Darabszám|Összesen|Az eszközről az eszközre irányuló lejárt üzenetek száma|None|
|devices.totalDevices|Összes eszköz (elavult)|Darabszám|Összesen|Az IoT hubhoz regisztrált eszközök száma|None|
|devices.connectedDevices.allProtocol|Csatlakoztatott eszközök (elavult) |Darabszám|Összesen|Az IoT hubhoz csatlakoztatott eszközök száma|None|
|d2c.telemettry.egress.success|Útválasztás: telemetriai üzenetek kézbesítve|Darabszám|Összesen|Az üzenetek sikeres kézbesítésének száma az IoT Hub útválasztáshasználatával az összes végpontra. Ha egy üzenet több végpontra van irányítva, ez az érték minden sikeres kézbesítésesetén eggyel nő. Ha egy üzenetet többször kézbesítenek ugyanarra a végpontra, ez az érték minden sikeres kézbesítésesetén eggyel nő.|None|
|d2c.telemettry.egress.dropped|Útválasztás: eldobott telemetriai üzenetek |Darabszám|Összesen|Az üzenetek zsákutca miatti eldobásának száma az IoT Hub útválasztása. Ez az érték nem számít a tartalék útvonalra kézbesített üzeneteknek, mivel az eldobott üzenetek nem kézbesítve vannak.|None|
|d2c.telemettry.egress.orphaned|Útválasztás: telemetriai üzenetek árva |Darabszám|Összesen|Az Üzenetek árván maradásának száma az IoT Hub-útválasztás miatt, mert nem felelnek meg semmilyen útválasztási szabálynak (beleértve a tartalék szabályt is). |None|
|d2c.telemettry.egress.invalid|Útválasztás: a telemetriai üzenetek nem kompatibilisek|Darabszám|Összesen|Az IoT Hub-útválasztás végpontdal való inkompatibilitás miatt nem tudott üzeneteket kézbesíteni. Ez az érték nem tartalmazza az újrapróbálkozásokat.|None|
|d2c.telemettry.egress.fallback|Útválasztás: tartalékba küldött üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás a tartalék útvonalhoz társított végpontra kézbesített üzenetek száma.|None|
|d2c.endpoints.egress.eventHubs|Útválasztás: az Event Hubba kézbesített üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen kézbesített üzeneteket Event Hub végpontok száma.|None|
|d2c.endpoints.latency.eventHubs|Útválasztás: üzenetkésés az Eseményközpontszámára|Ezredmásodperc|Átlag|Az IoT Hubba irányuló üzenetek és az Event Hub-végpontba való üzenet-be- és üzenet-be- és üzenetbe- és üzenetbe- és üzenetküldés közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.serviceBusQueues|Útválasztás: a Service Bus várólistájába kézbesített üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen kézbesített üzeneteket Service Bus várólista végpontok száma.|None|
|d2c.endpoints.latency.serviceBusQueues|Útválasztás: üzenetkésa Service Bus-várólistához|Ezredmásodperc|Átlag|Az IoT Hubba irányuló üzenet-be- és telemetriai üzenetek egy Service Bus-várólista-végpontba való be- és telemetriai üzenet közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.serviceBusTopics|Útválasztás: a Service Bus topic szolgáltatásba küldött üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen kézbesített üzeneteket Service Bus témakör végpontok száma.|None|
|d2c.endpoints.latency.serviceBusTopics|Útválasztás: üzenetkésés a Service Bus-témakörhöz|Ezredmásodperc|Átlag|Az IoT Hubba küldött üzenet és a telemetriai üzenetek egy Service Bus-témakör végpontjába való be- és telemetriai üzenet közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.builtIn.events|Útválasztás: üzenetekbe/eseményekre kézbesített üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen kézbesített üzeneteket a beépített végpont (üzenetek/események) száma.|None|
|d2c.endpoints.latency.builtIn.events|Útválasztás: üzenetek/események üzenetkéste|Ezredmásodperc|Átlag|Az IoT Hubba küldött üzenet és a telemetriai üzenetek beépített végpontba (üzenetek/események) való be- és telemetriai üzenetek közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.storage|Útválasztás: a tárolóba kézbesített üzenetek|Darabszám|Összesen|Az IoT Hub útválasztás sikeresen szállított üzeneteket a tárolási végpontok száma.|None|
|d2c.endpoints.latency.storage|Útválasztás: üzenet késése a tároláshoz|Ezredmásodperc|Átlag|Az IoT Hubba küldött üzenet és a telemetriai üzenetek tárolási végpontba való be- és telemetriai üzenete közötti átlagos késés (ezredmásodperc).|None|
|d2c.endpoints.egress.storage.bytes|Útválasztás: a tárolóba szállított adatok|Bájt|Összesen|A tárolási végpontokhoz kézbesített adatok (bájt) adatok mennyisége (bájt).|None|
|d2c.endpoints.egress.storage.blobok|Útválasztás: a tárolóba szállított blobok|Darabszám|Összesen|Az IoT Hub-útválasztás blobok tárolási végpontok kézbesítésének száma.|None|
|EventGridDeliveres|Eseményrács-szállítások (előzetes verzió)|Darabszám|Összesen|Az Event Gridben közzétett IoT Hub-események száma. Használja az Eredmény dimenziót a sikeres és sikertelen kérelmek számához. Az EventType dimenzió azhttps://aka.ms/ioteventgrid)esemény típusát ( .|ResourceId,Eredmény,EventType|
|EventGridLatency|Eseményrács késése (előzetes verzió)|Ezredmásodperc|Átlag|Az iot hub esemény létrehozásának időpontjától az esemény eseménynaptárban való közzétételének időpontjáig eltelt átlagos késés (ezredmásodperc). Ez a szám az összes eseménytípus átlaga. Az EventType dimenzió val egy adott eseménytípus késésének megtekintéséhez.|ResourceId,EventType|
|Útválasztási szállítások|Szállítások útválasztása (előzetes verzió)|Ezredmásodperc|Összesen|Az IoT Hub által az összes végpontnak útválasztás használatával történő kézbesítése. A sikeres vagy sikertelen kísérletek számának megtekintéséhez használja az Eredmény dimenziót. A hiba okának megtekintéséhez, például érvénytelen, eldobott vagy árva, használja a FailureReasonCategory dimenziót. Az EndpointName és az EndpointType dimenziók segítségével is megismerheti, hogy hány üzenet érkezett a különböző végpontokra. A metrika értéke minden kézbesítési kísérlet esetén eggyel nő, például ha az üzenet több végpontra kerül, vagy ha az üzenet többször is ugyanarra a végpontra kerül.|ResourceId,EndpointType,EndpointName,FailureReasonCategory,Result,RoutingSource|
|RoutingDeliveryLatency|Útválasztási kézbesítési késés (előzetes verzió)|Ezredmásodperc|Átlag|Az IoT Hubba küldött üzenet és a telemetriai üzenetek végpontba való be- és telemetriai üzenete közötti átlagos késés (ezredmásodperc). Az EndpointName és az EndpointType dimenziók segítségével megismerheti a különböző végpontok késését.|ResourceId,EndpointType,EndpointName,RoutingSource|
|d2c.twin.read.success|Sikeres ikerolvasás eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett ikerolvasás száma.|None|
|d2c.twin.read.failure|Nem sikerült ikerbeolvasás az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett ikerolvasás száma.|None|
|d2c.twin.read.méret|Az eszközökről beolvasások válaszmérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett ikerolvasás átlaga, percés max.|None|
|d2c.twin.update.success|Sikeres ikerfrissítések az eszközökről|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett ikerfrissítés száma.|None|
|d2c.twin.update.failure|Nem sikerült ikerfrissítés az eszközökről|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett ikerfrissítés száma.|None|
|d2c.twin.update.size|Az eszközökről származó ikerfrissítések mérete|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett ikerfrissítés átlagos, min és maximális mérete.|None|
|c2d.methods.success|Sikeres közvetlen metódus-meghívások|Darabszám|Összesen|Az összes sikeres közvetlen metódushívás száma.|None|
|c2d.methods.failure|Sikertelen közvetlen metódus-meghívások|Darabszám|Összesen|A sikertelen közvetlen metódushívások száma.|None|
|c2d.methods.requestMéret|A közvetlen metódusmetódusok kérésének mérete|Bájt|Átlag|Az összes sikeres közvetlen metóduskérés átlaga, perce és max.|None|
|c2d.methods.responseMéret|A közvetlen metódusmetódusok válaszmérete|Bájt|Átlag|Az összes sikeres közvetlen metódusválasz átlaga, percés max.|None|
|c2d.twin.read.success|Sikeres ikerolvasás háttérből|Darabszám|Összesen|Az összes sikeres háttér-kezdeményezett ikerolvasás oka.|None|
|c2d.twin.read.failure|Sikertelen ikerolvasás háttérből|Darabszám|Összesen|Az összes sikertelen háttér-kezdeményezett ikerolvasások száma.|None|
|c2d.twin.read.size|A háttérből beolvasások válaszmérete|Bájt|Átlag|Az összes sikeres háttér-kezdeményezett ikerolvasás átlaga, min és max.|None|
|c2d.twin.update.success|Sikeres ikerfrissítések a háttérrendszerből|Darabszám|Összesen|Az összes sikeres háttér-kezdeményezett ikerfrissítés száma.|None|
|c2d.twin.update.failure|Sikertelen ikerfrissítések a háttérrendszerből|Darabszám|Összesen|A rendszer az összes sikertelen háttér-kezdeményezett ikerfrissítés számát.|None|
|c2d.twin.update.size|Két frissítés mérete háttérrendszerről|Bájt|Átlag|Az összes sikeres háttér-kezdeményezett ikerfrissítés átlagos, min és maximális mérete.|None|
|twinQueries.success|Sikeres ikerlekérdezések|Darabszám|Összesen|Az összes sikeres ikerlekérdezés száma.|None|
|twinQueries.failure|Sikertelen ikerlekérdezések|Darabszám|Összesen|A sikertelen ikerlekérdezések száma.|None|
|twinQueries.resultSize|Ikerlekérdezések eredménymérete|Bájt|Átlag|Az összes sikeres ikerlekérdezés eredményméretének átlagos, min és max.|None|
|jobs.createTwinUpdateJob.success|Két frissítési feladat sikeres létrehozása|Darabszám|Összesen|Az ikerfrissítési feladatok sikeres létrehozásának száma.|None|
|jobs.createTwinUpdateJob.failure|Két frissítési feladat sikertelen létrehozása|Darabszám|Összesen|Az összes sikertelen ikerfrissítési feladat létrehozása.|None|
|jobs.createDirectMethodJob.success|A metódus-meghívási feladatok sikeres létrehozása|Darabszám|Összesen|A közvetlen metódusok meghívási feladatainak sikeres létrehozásának száma.|None|
|jobs.createDirectMethodJob.failure|Metódusmeghívási feladatok sikertelen létrehozása|Darabszám|Összesen|A közvetlen metódus-meghívási feladatok összes sikertelen létrehozásának száma.|None|
|jobs.listJobs.success|Sikeres hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázásához sikeres hívások száma.|None|
|jobs.listJobs.failure|Sikertelen hívások a feladatok listázásához|Darabszám|Összesen|A feladatok listázásához sikertelen hívások száma.|None|
|jobs.cancelJob.success|Sikeres feladattörlések|Darabszám|Összesen|A feladat megszakításához sikeres hívások száma.|None|
|jobs.cancelJob.failure|Sikertelen feladatmegszakítások|Darabszám|Összesen|A feladat megszakításához sikertelen hívások száma.|None|
|jobs.query.success|Sikeres feladatlekérdezések|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|None|
|jobs.query.failure|Sikertelen feladatlekérdezések|Darabszám|Összesen|A lekérdezési feladatok összes sikertelen hívásának száma.|None|
|jobs.befejezett|Befejezett feladatok|Darabszám|Összesen|Az összes befejezett feladat száma.|None|
|jobs.failed|Sikertelen feladatok|Darabszám|Összesen|Az összes sikertelen feladat száma.|None|
|d2c.telemettry.ingress.sendThrottle|Szabályozási hibák száma|Darabszám|Összesen|Az eszköz átviteli fojtószelepei miatti szabályozási hibák száma|None|
|dailyMessageQuotaHasznált|A felhasznált üzenetek száma összesen|Darabszám|Átlag|A ma használt összes üzenet száma|None|
|deviceDataUsage|Az eszköz összes adathasználata|Bájt|Összesen|Az IotHubhoz csatlakoztatott eszközökre és eszközökről átvitt bájtok|None|
|deviceDataUsageV2|Az eszköz teljes adathasználata (előzetes verzió)|Bájt|Összesen|Az IotHubhoz csatlakoztatott eszközökre és eszközökről átvitt bájtok|None|
|totalDeviceCount (összeseszközszám)|Eszközök összesen (előzetes verzió)|Darabszám|Átlag|Az IoT hubhoz regisztrált eszközök száma|None|
|connectedDeviceCount (connectedDeviceCount)|Csatlakoztatott eszközök (előzetes verzió)|Darabszám|Átlag|Az IoT hubhoz csatlakoztatott eszközök száma|None|
|Konfigurációk|Konfigurációs mutatók|Darabszám|Összesen|A konfigurációs műveletek metrikája|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Regisztrációkísérletek|Regisztrációs kísérletek|Darabszám|Összesen|A megkísérelt eszközregisztrációk száma|provisioningServiceName,IotHubName,Állapot|
|DeviceAssignments -ek|Hozzárendelt eszközök|Darabszám|Összesen|Az IoT-központhoz rendelt eszközök száma|provisioningServiceName,IotHubName|
|Tanúsítványkísérletek|Igazolási kísérletek|Darabszám|Összesen|A megkísérelt eszköztanúsítványok száma|ProvisioningServiceName,Állapot,Protokoll|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|AddRegion (Régió hozzáadása)|Régió hozzáadva|Darabszám|Darabszám|Régió hozzáadva|Régió|
|AvailableStorage (Elérhető tárhely)|Rendelkezésre álló tár|Bájt|Összesen|Az 5 perces részletességgel jelentett teljes rendelkezésre álló tárterület|Gyűjteménynév,Adatbázisnév,Terület|
|CassandraConnectionClosures|Cassandra kapcsolat lezárások|Darabszám|Összesen|A lezárt Cassandra-kapcsolatok száma, 1 perces részletességgel|APIType,Régió,LezárásOk|
|CassandraKeyspaceTörlés|Cassandra Keyspace törölve|Darabszám|Darabszám|Cassandra Keyspace törölve|Erőforrásnév,apikind,apikindresourcetype,OperationType|
|CassandraKeyspaceThroughputFrissítés|Cassandra Keyspace átviteli nyomás frissítve|Darabszám|Darabszám|Cassandra Keyspace átviteli nyomás frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceFrissítés|Cassandra Keyspace frissítve|Darabszám|Darabszám|Cassandra Keyspace frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraRequestTöltések|Cassandra kérelmek díjak|Darabszám|Összesen|A Cassandra által benyújtott kérelmekhez felhasznált|APIType,Adatbázisnév,Gyűjteménynév,Terület,Művelettípus,Erőforrástípus|
|CassandraKéri|Cassandra kérések|Darabszám|Darabszám|Cassandra-kérelmek száma|APIType,Adatbázisnév,Gyűjteménynév,Terület,Művelettípus,Erőforrástípus,Hibakód|
|CassandraTableDelete|Cassandra tábla törölve|Darabszám|Darabszám|Cassandra tábla törölve|Erőforrásnév,ChildResourceName,apikind,apikindresourcetype,OperationType|
|CassandraTableThroughputFrissítés|Cassandra tábla átviteli átputja frissítve|Darabszám|Darabszám|Cassandra tábla átviteli átputja frissítve|Erőforrásnév,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraTableUpdate|Cassandra tábla frissítve|Darabszám|Darabszám|Cassandra tábla frissítve|Erőforrásnév,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Fiók létrehozása|Fiók létrehozva|Darabszám|Darabszám|Fiók létrehozva|None|
|Adathasználat|Adathasználat|Bájt|Összesen|Az 5 perces részletességgel jelentett teljes adathasználat|Gyűjteménynév,Adatbázisnév,Terület|
|Törlési fiók|Fiók törölve|Darabszám|Darabszám|Fiók törölve|None|
|DocumentCount (Dokumentumdarab)|Dokumentumok száma|Darabszám|Összesen|Az 5 perces részletességgel jelentett dokumentumok teljes száma|Gyűjteménynév,Adatbázisnév,Terület|
|DocumentQuota (Dokumentumquota)|Dokumentumkvóta|Bájt|Összesen|Az 5 perces részletességgel jelentett teljes tárolási kvóta|Gyűjteménynév,Adatbázisnév,Terület|
|GremlinDatabaseTörlés|Gremlin adatbázis törölve|Darabszám|Darabszám|Gremlin adatbázis törölve|Erőforrásnév,apikind,apikindresourcetype,OperationType|
|GremlinDatabaseThroughputFrissítés|Gremlin adatbázis átviteli áteresztőáltal frissítve|Darabszám|Darabszám|Gremlin adatbázis átviteli áteresztőáltal frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabaseFrissítés|Gremlin adatbázis frissítve|Darabszám|Darabszám|Gremlin adatbázis frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphTörlés|Gremlin graph törölve|Darabszám|Darabszám|Gremlin graph törölve|Erőforrásnév,ChildResourceName,apikind,apikindresourcetype,OperationType|
|GremlinGraphThroughputFrissítés|Gremlin Graph átviteli áteresztőáltal frissítve|Darabszám|Darabszám|Gremlin Graph átviteli áteresztőáltal frissítve|Erőforrásnév,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphFrissítés|Gremlin Graph frissítve|Darabszám|Darabszám|Gremlin Graph frissítve|Erőforrásnév,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Indexhasználat|Index használata|Bájt|Összesen|Az 5 perces részletességgel jelentett teljes indexhasználat|Gyűjteménynév,Adatbázisnév,Terület|
|Metaadatkérések|Metaadat-kérelmek|Darabszám|Darabszám|Metaadat-kérelmek száma. A Cosmos DB rendszermetaadat-gyűjtést tart fenn minden egyes fiókhoz, amely lehetővé teszi a gyűjtemények, adatbázisok stb.|Adatbázisnév,Gyűjteménynév,Régió,Állapotkód,Szerepkör|
|MongoCollectionDelete|Mongo Gyűjtemény törölve|Darabszám|Darabszám|Mongo Gyűjtemény törölve|Erőforrásnév,ChildResourceName,apikind,apikindresourcetype,OperationType|
|MongoCollectionThroughputUpdate|Mongo gyűjtemény átviteli áttörve frissítve|Darabszám|Darabszám|Mongo gyűjtemény átviteli áttörve frissítve|Erőforrásnév,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoCollectionUpdate|Mongo Gyűjtemény Frissítve|Darabszám|Darabszám|Mongo Gyűjtemény Frissítve|Erőforrásnév,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDBDatabaseUpdate|Mongo adatbázis frissítve|Darabszám|Darabszám|Mongo adatbázis frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseDelete|Mongo adatbázis törölve|Darabszám|Darabszám|Mongo adatbázis törölve|Erőforrásnév,apikind,apikindresourcetype,OperationType|
|MongoDatabaseThroughputUpdate|Mongo adatbázis átviteli átállása frissítve|Darabszám|Darabszám|Mongo adatbázis átviteli átállása frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequestCharge|Mongo kérelem díj|Darabszám|Összesen|Mongo kérelem egységek elfogyasztott|Adatbázisnév,Gyűjteménynév,Terület,Parancsnév,Hibakód,Állapot|
|MongoKérések|Mongo kérések|Darabszám|Darabszám|Mongo kérelmek száma|Adatbázisnév,Gyűjteménynév,Terület,Parancsnév,Hibakód,Állapot|
|MongoRequestsCount|Mongo kérelem aránya|CountPerSzekszekundum|Átlag|Mongo kérelem száma másodpercenként|Adatbázisnév,Gyűjteménynév,Terület,Parancsnév,Hibakód|
|MongoRequestsDelete|Mongo törlési kérelem aránya|CountPerSzekszekundum|Átlag|Mongo Delete kérés másodpercenként|Adatbázisnév,Gyűjteménynév,Terület,Parancsnév,Hibakód|
|MongoRequestsBeszúrás|Mongo kérelembehelyezési arány|CountPerSzekszekundum|Átlag|Mongo beszúrási szám másodpercenként|Adatbázisnév,Gyűjteménynév,Terület,Parancsnév,Hibakód|
|MongoRequestsQuery|Mongo lekérdezési kérelmek aránya|CountPerSzekszekundum|Átlag|Mongo lekérdezési kérelem másodpercenként|Adatbázisnév,Gyűjteménynév,Terület,Parancsnév,Hibakód|
|MongoRequestsFrissítés|Mongo frissítési kérelmek aránya|CountPerSzekszekundum|Átlag|Mongo frissítési kérelem másodpercenként|Adatbázisnév,Gyűjteménynév,Terület,Parancsnév,Hibakód|
|NormalizáltRUFogyasztás|Normalizált VT-felhasználás|Százalék|Maximum|Maximális RU-fogyasztási százalék percenként|Gyűjteménynév,Adatbázisnév,Terület|
|Kiépítettátviteli|Kiosztott átviteli sebesség|Darabszám|Maximum|Kiosztott átviteli sebesség|Adatbázisnév,Gyűjteménynév|
|RégióFailover|A régió feladatátvétele|Darabszám|Darabszám|A régió feladatátvétele|None|
|Régió eltávolítása|Terület eltávolítva|Darabszám|Darabszám|Terület eltávolítva|Régió|
|ReplikációLatency|P99 replikációs késés|Ezredmásodperc|Átlag|P99 replikációs késés a földrajzi alapú fiók forrás- és célrégiói között|Forrásrégió,Célterület|
|ServerSideLatency (KiszolgálósideLatency)|Kiszolgálóoldali késés|Ezredmásodperc|Átlag|Kiszolgálóoldali késés|Adatbázisnév,Gyűjteménynév,Terület,Kapcsolatmód,Művelettípus,PublicAPIType|
|Szolgáltatáselérhetősége|A szolgáltatás elérhetősége|Százalék|Átlag|A fiók kérelmek elérhetősége egy óra, nap vagy hónap részletessége|None|
|SqlContainerDelete|Sql tároló törölve|Darabszám|Darabszám|Sql tároló törölve|Erőforrásnév,ChildResourceName,apikind,apikindresourcetype,OperationType|
|SqlContainerThroughputUpdate|Sql tároló átviteli átputja frissítve|Darabszám|Darabszám|Sql tároló átviteli átputja frissítve|Erőforrásnév,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlContainerUpdate|Sql tároló frissítve|Darabszám|Darabszám|Sql tároló frissítve|Erőforrásnév,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseDelete|Sql adatbázis törölve|Darabszám|Darabszám|Sql adatbázis törölve|Erőforrásnév,apikind,apikindresourcetype,OperationType|
|SqlDatabaseThroughputUpdate|Sql-adatbázis átviteli átputja frissítve|Darabszám|Darabszám|Sql-adatbázis átviteli átputja frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseUpdate|Sql-adatbázis frissítve|Darabszám|Darabszám|Sql-adatbázis frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableDelete táblatörlése|AzureTable tábla törölve|Darabszám|Darabszám|AzureTable tábla törölve|Erőforrásnév,apikind,apikindresourcetype,OperationType|
|TableTableThroughputUpdate|AzureTable tábla átviteli átputa frissítve|Darabszám|Darabszám|AzureTable tábla átviteli átputa frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Táblafrissítés|AzureTable tábla frissítve|Darabszám|Darabszám|AzureTable tábla frissítve|Erőforrásnév,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TotalRequestUnits|Összes kérelemegység|Darabszám|Összesen|Felhasznált egységek igénylése|Adatbázisnév,Gyűjteménynév,Régió,Állapotkód,Művelettípus,Állapot|
|TotalRequests|Összes kérelem|Darabszám|Darabszám|A benyújtott kérelmek száma|Adatbázisnév,Gyűjteménynév,Régió,Állapotkód,Művelettípus,Állapot|
|UpdateAccountKeys|Fiókkulcsok frissítve|Darabszám|Darabszám|Fiókkulcsok frissítve|Kulcstípus|
|UpdateAccountNetworkSettings|Fiókhálózati beállítások frissítve|Darabszám|Darabszám|Fiókhálózati beállítások frissítve|None|
|UpdateAccountReplicationSettings|A fiókreplikáció beállításai frissítve|Darabszám|Darabszám|A fiókreplikáció beállításai frissítve|None|
|UpdateDiagnosticsSettings|Fiókdiagnosztikai beállítások frissítve|Darabszám|Darabszám|Fiókdiagnosztikai beállítások frissítve|DiagnosticSettingsName,ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/szolgáltatások

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Tranzakciószáma|Tranzakciók száma|Darabszám|Darabszám|Tranzakciók száma összesen|Tranzakciószáma|
|Sikerszáma|Sikeres műveletek száma|Darabszám|Darabszám|Sikeres tranzakciók száma|Sikerszáma|
|Hibaszáma|Hibásak száma|Darabszám|Darabszám|Sikertelen tranzakciók száma|Hibaszáma|
|SuccessLatency (Káttaláta)|Sikeres késés|Ezredmásodperc|Átlag|Sikeres tranzakciók késése|Sikerszáma|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/tartományok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount (Közzétételsikerszáma)|Közzétett események|Darabszám|Összesen|A témakörben közzétett események összesen|Témakör|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|Az összes eseményt nem sikerült közzétenni ebben a témakörben|Témakör,Hibatípus,Hiba|
|KözzétételSikerLatencyInM-ek|Sikeres késés közzététele|Ezredmásodperc|Összesen|Sikeres késés közzététele ezredmásodpercben|None|
|MatchedEventCount (Egyezésű eseményszám)|Egyező események|Darabszám|Összesen|Az esemény-előfizetéshez illeszkedő összes esemény|Témakör,EventSubscriptionName,DomainEventsubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény nem sikerült az esemény-előfizetéshez|Témakör,EventSubscriptionName,DomainEventsubscriptionName,Hiba,Hibatípus|
|DeliverySuccessCount (DeliverySuccessCount)|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített események összesen|Témakör,EventSubscriptionName,DomainEventsubscriptionName|
|DestinationProcessingDurationinms|Cél feldolgozási időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|Témakör,EventSubscriptionName,DomainEventsubscriptionName|
|DroppedEventCount (Ejtési eseményszám)|Eldobott események|Darabszám|Összesen|Az esemény-előfizetéshez tartozó összes eldobott esemény|Témakör,EventSubscriptionName,DomainEventsubscriptionName,DropReason|
|DeadLetteredCount (HalottletteredCount)|Kézbesített levéllel írt események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő kézbesítésre kész ként írt események összesen|Témakör,EventSubscriptionName,DomainEventsubscriptionName,ZsákutcareasonReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/témakörök

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount (Közzétételsikerszáma)|Közzétett események|Darabszám|Összesen|A témakörben közzétett események összesen|None|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|Az összes eseményt nem sikerült közzétenni ebben a témakörben|Hibatípus,Hiba|
|Nem egyező eseményszám|Páratlan események|Darabszám|Összesen|A témakörhöz tartozó esemény-előfizetések egyikének sem megfelelő összes esemény|None|
|KözzétételSikerLatencyInM-ek|Sikeres késés közzététele|Ezredmásodperc|Összesen|Sikeres késés közzététele ezredmásodpercben|None|
|MatchedEventCount (Egyezésű eseményszám)|Egyező események|Darabszám|Összesen|Az esemény-előfizetéshez illeszkedő összes esemény|EventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény nem sikerült az esemény-előfizetéshez|Hiba,Hibatípus,EventSubscriptionName|
|DeliverySuccessCount (DeliverySuccessCount)|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített események összesen|EventSubscriptionName|
|DestinationProcessingDurationinms|Cél feldolgozási időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|EventSubscriptionName|
|DroppedEventCount (Ejtési eseményszám)|Eldobott események|Darabszám|Összesen|Az esemény-előfizetéshez tartozó összes eldobott esemény|DropReason,EventSubscriptionName|
|DeadLetteredCount (HalottletteredCount)|Kézbesített levéllel írt események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő kézbesítésre kész ként írt események összesen|Kézbesítési levélok címere,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount (Közzétételsikerszáma)|Közzétett események|Darabszám|Összesen|A témakörben közzétett események összesen|None|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|Az összes eseményt nem sikerült közzétenni ebben a témakörben|Hibatípus,Hiba|
|Nem egyező eseményszám|Páratlan események|Darabszám|Összesen|A témakörhöz tartozó esemény-előfizetések egyikének sem megfelelő összes esemény|None|
|KözzétételSikerLatencyInM-ek|Sikeres késés közzététele|Ezredmásodperc|Összesen|Sikeres késés közzététele ezredmásodpercben|None|
|MatchedEventCount (Egyezésű eseményszám)|Egyező események|Darabszám|Összesen|Az esemény-előfizetéshez illeszkedő összes esemény|EventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény nem sikerült az esemény-előfizetéshez|Hiba,Hibatípus,EventSubscriptionName|
|DeliverySuccessCount (DeliverySuccessCount)|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített események összesen|EventSubscriptionName|
|DestinationProcessingDurationinms|Cél feldolgozási időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|EventSubscriptionName|
|DroppedEventCount (Ejtési eseményszám)|Eldobott események|Darabszám|Összesen|Az esemény-előfizetéshez tartozó összes eldobott esemény|DropReason,EventSubscriptionName|
|DeadLetteredCount (HalottletteredCount)|Kézbesített levéllel írt események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő kézbesítésre kész ként írt események összesen|Kézbesítési levélok címere,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount (Egyezésű eseményszám)|Egyező események|Darabszám|Összesen|Az esemény-előfizetéshez illeszkedő összes esemény|None|
|DeliveryAttemptFailCount|Sikertelen kézbesítési események|Darabszám|Összesen|Az összes esemény nem sikerült az esemény-előfizetéshez|Hiba,Hibatípus|
|DeliverySuccessCount (DeliverySuccessCount)|Kézbesített események|Darabszám|Összesen|Az esemény-előfizetéshez kézbesített események összesen|None|
|DestinationProcessingDurationinms|Cél feldolgozási időtartama|Ezredmásodperc|Átlag|Célfeldolgozás időtartama ezredmásodpercben|None|
|DroppedEventCount (Ejtési eseményszám)|Eldobott események|Darabszám|Összesen|Az esemény-előfizetéshez tartozó összes eldobott esemény|DropReason (DropReason)|
|DeadLetteredCount (HalottletteredCount)|Kézbesített levéllel írt események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő kézbesítésre kész ként írt események összesen|DeadLetterReason (Kézbesítési levélok)|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTémakörök

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount (Közzétételsikerszáma)|Közzétett események|Darabszám|Összesen|A témakörben közzétett események összesen|None|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|Az összes eseményt nem sikerült közzétenni ebben a témakörben|Hibatípus,Hiba|
|Nem egyező eseményszám|Páratlan események|Darabszám|Összesen|A témakörhöz tartozó esemény-előfizetések egyikének sem megfelelő összes esemény|None|
|KözzétételSikerLatencyInM-ek|Sikeres késés közzététele|Ezredmásodperc|Összesen|Sikeres késés közzététele ezredmásodpercben|None|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/névterek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Sikeres kérések|Sikeres kérelmek|Darabszám|Összesen|Sikeres kérelmek a Microsoft.EventHub számára.|Entitásnév,Műveleteredmény|
|Kiszolgálóhibák|Kiszolgálói hibák.|Darabszám|Összesen|A Microsoft.EventHub kiszolgálóhibái.|Entitásnév,Műveleteredmény|
|Felhasználói hibák|Felhasználói hibák.|Darabszám|Összesen|Felhasználói hibák a Microsoft.EventHub on.|Entitásnév,Műveleteredmény|
|Kvótatúllépéshibák|A kvóta túllépte a hibákat.|Darabszám|Összesen|Kvóta túllépte a Microsoft.EventHub hibáit.|Entitásnév,Műveleteredmény|
|ThrottledRequests|Szabályozott kérelmek.|Darabszám|Összesen|Szabályozott kérelmek a Microsoft.EventHub számára.|Entitásnév,Műveleteredmény|
|Bejövő kérések|Bejövő kérések|Darabszám|Összesen|Bejövő kérelmek a Microsoft.EventHub számára.|Entitásnév|
|Bejövő üzenetek|Bejövő üzenetek|Darabszám|Összesen|Bejövő üzenetek a Microsoft.EventHub hoz.|Entitásnév|
|Kimenő üzenetek|Kimenő üzenetek|Darabszám|Összesen|Kimenő üzenetek a Microsoft.EventHub hoz.|Entitásnév|
|Bejövő bájt|Bejövő bájtok.|Bájt|Összesen|Bejövő bájtok a Microsoft.EventHub hoz.|Entitásnév|
|Kimenő bájtok|Kimenő bájtok.|Bájt|Összesen|Kimenő bájt a Microsoft.EventHub hoz.|Entitásnév|
|ActiveConnections|ActiveConnections|Darabszám|Átlag|A Microsoft.EventHub összes aktív kapcsolata.|None|
|Kapcsolatok megnyitva|Kapcsolatok megnyitva.|Darabszám|Átlag|A Microsoft.EventHub számára megnyitott kapcsolatok.|Entitásnév|
|Kapcsolatok zárva|Kapcsolatok zárva.|Darabszám|Átlag|A Microsoft.EventHub számára lezárt kapcsolatok.|Entitásnév|
|CaptureBacklog (Rögzítési backlog)|Elfog hátralék.|Darabszám|Összesen|Rögzítse a Backlog-ot a Microsoft.EventHub számára.|Entitásnév|
|CapturedMessages (Rögzített üzenetek)|Rögzített üzenetek.|Darabszám|Összesen|Rögzített üzenetek a Microsoft.EventHub hoz.|Entitásnév|
|Rögzítettbájt|Rögzített bájtok.|Bájt|Összesen|Rögzített bájtok a Microsoft.EventHub hoz.|Entitásnév|
|Méret|Méret|Bájt|Átlag|EventHub mérete bájtban.|Entitásnév|
|InreQs|Bejövő kérelmek (elavult)|Darabszám|Összesen|Névtérbe érkező bejövő küldési kérelmek összesen (elavult)|None|
|SUCCREQ között|Sikeres kérelmek (elavult)|Darabszám|Összesen|Névtérre vonatkozó sikeres kérelmek összesen (elavult)|None|
|FailreQ között|Sikertelen kérelmek (elavult)|Darabszám|Összesen|Névtérre vonatkozó sikertelen kérelmek összesen (elavult)|None|
|SVRBSY között|Kiszolgáló foglaltsági hibái (elavult)|Darabszám|Összesen|Névtér kiszolgálófoglalt sági hibáinak összesen (elavult)|None|
|INTERR|Belső kiszolgálói hibák (elavult)|Darabszám|Összesen|Névtér belső kiszolgálóhibáinak összesen (elavult)|None|
|MISCERR|Egyéb hibák (elavult)|Darabszám|Összesen|Névtérre vonatkozó sikertelen kérelmek összesen (elavult)|None|
|INMSGS|Bejövő üzenetek (elavult) (elavult)|Darabszám|Összesen|Névtérbe érkező üzenetek összesen. Ez a metrika elavult. Használja inkább a Bejövő üzenetek mérőszámot (elavult)|None|
|EHINMSGS|Bejövő üzenetek (elavult)|Darabszám|Összesen|Névtérbe érkező üzenetek összesen (elavult)|None|
|OUTMSGS|Kimenő üzenetek (elavult) (elavult)|Darabszám|Összesen|Névtér kimenő üzeneteinek összesen. Ez a metrika elavult. Használja inkább a Kimenő üzenetek mutatót (elavult)|None|
|EHOUTMSGS|Kimenő üzenetek (elavult)|Darabszám|Összesen|Névtér kimenő üzenetének összesen (elavult)|None|
|EHINMBS|Bejövő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event Hub bejövő üzenetátviteli fazonja egy névtérhez. Ez a metrika elavult. Használja inkább a Bejövő bájtok metrikát (elavult)|None|
|EHINBÁJT|Bejövő bájtok (elavult)|Bájt|Összesen|Az Event Hub bejövő üzenetátviteli igénye névtérhez (elavult)|None|
|EHOUTMBS|Kimenő bájtok (elavult) (elavult)|Bájt|Összesen|Az Event Hub kimenő üzenetátviteli címe egy névtérhez. Ez a metrika elavult. Használja inkább a Kimenő bájtok metrikát (elavult)|None|
|EHOUTBYTES (EHOUTBÁJT)|Kimenő bájtok (elavult)|Bájt|Összesen|Az Event Hub kimenő üzenetátviteli igénye névtérhez (elavult)|None|
|EHABL|Hátraléküzenetek archiválása (elavult)|Darabszám|Összesen|Az Event Hub archiválási üzenetei egy névtér hátralékában (elavult)|None|
|EHAMSGS között|Archiválási üzenetek (elavult)|Darabszám|Összesen|Az Event Hub archivált üzenetei egy névtérben (elavult)|None|
|EHAMBS|Archiválási üzenet átviteli -átmenő (elavult)|Bájt|Összesen|Az Event Hub archivált üzenetátviteli igénye névtérben (elavult)|None|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Sikeres kérések|Sikeres kérelmek|Darabszám|Összesen|Sikeres kérelmek a Microsoft.EventHub számára.|OperationResult műveleteredménye|
|Kiszolgálóhibák|Kiszolgálói hibák.|Darabszám|Összesen|A Microsoft.EventHub kiszolgálóhibái.|OperationResult műveleteredménye|
|Felhasználói hibák|Felhasználói hibák.|Darabszám|Összesen|Felhasználói hibák a Microsoft.EventHub on.|OperationResult műveleteredménye|
|Kvótatúllépéshibák|A kvóta túllépte a hibákat.|Darabszám|Összesen|Kvóta túllépte a Microsoft.EventHub hibáit.|OperationResult műveleteredménye|
|ThrottledRequests|Szabályozott kérelmek.|Darabszám|Összesen|Szabályozott kérelmek a Microsoft.EventHub számára.|OperationResult műveleteredménye|
|Bejövő kérések|Bejövő kérések|Darabszám|Összesen|Bejövő kérelmek a Microsoft.EventHub számára.|None|
|Bejövő üzenetek|Bejövő üzenetek|Darabszám|Összesen|Bejövő üzenetek a Microsoft.EventHub hoz.|None|
|Kimenő üzenetek|Kimenő üzenetek|Darabszám|Összesen|Kimenő üzenetek a Microsoft.EventHub hoz.|None|
|Bejövő bájt|Bejövő bájtok.|Bájt|Összesen|Bejövő bájtok a Microsoft.EventHub hoz.|None|
|Kimenő bájtok|Kimenő bájtok.|Bájt|Összesen|Kimenő bájt a Microsoft.EventHub hoz.|None|
|ActiveConnections|ActiveConnections|Darabszám|Átlag|A Microsoft.EventHub összes aktív kapcsolata.|None|
|Kapcsolatok megnyitva|Kapcsolatok megnyitva.|Darabszám|Átlag|A Microsoft.EventHub számára megnyitott kapcsolatok.|None|
|Kapcsolatok zárva|Kapcsolatok zárva.|Darabszám|Átlag|A Microsoft.EventHub számára lezárt kapcsolatok.|None|
|CaptureBacklog (Rögzítési backlog)|Elfog hátralék.|Darabszám|Összesen|Rögzítse a Backlog-ot a Microsoft.EventHub számára.|None|
|CapturedMessages (Rögzített üzenetek)|Rögzített üzenetek.|Darabszám|Összesen|Rögzített üzenetek a Microsoft.EventHub hoz.|None|
|Rögzítettbájt|Rögzített bájtok.|Bájt|Összesen|Rögzített bájtok a Microsoft.EventHub hoz.|None|
|CPU|CPU|Százalék|Maximum|Az Eseményközpont-fürt processzorának kihasználtsága százalékban|Szerepkör|
|Elérhető memória|Szabad memória|Százalék|Maximum|Az Event Hub Cluster számára rendelkezésre álló memória a teljes memória százalékában.|Szerepkör|
|Méret|EventHub mérete bájtban.|Bájt|Átlag|EventHub mérete bájtban.|Szerepkör|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/fürtök

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|GatewayRequests|Átjárókérelmek|Darabszám|Összesen|Átjárókérelmek száma|httpállapota|
|KategorizáltGatewayRequests|Kategorizált átjárókérelmek|Darabszám|Összesen|Átjárókérelmek száma kategóriák szerint (1xx/2xx/3xx/4xx/5xx)|httpállapota|
|NumActiveWorkers|Aktív munkavállalók száma|Darabszám|Maximum|Aktív munkavállalók száma|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ObservedMetricValue érték|Megfigyelt metrikus érték|Darabszám|Átlag|Az automatikus skálázás által kiszámított érték végrehajtáskor|MetricTriggerForrás|
|Metrikus küszöbérték|Metrikaküszöbérték|Darabszám|Átlag|A konfigurált automatikus skálázási küszöbértéket az automatikus skálázás futtatásakor.|MetricTriggerRule (MetricTriggerRule)|
|Megfigyeltkapacitás|Megfigyelt kapacitás|Darabszám|Átlag|Az automatikus skálázásnak jelentett kapacitás, amikor végrehajtotta.|None|
|ScaleActionsinitiated|Kezdeményezett műveletek méretezése|Darabszám|Összesen|A méretezési művelet iránya.|Méretezésirányirány|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|availabilityResults/availabilitySzázalék|Rendelkezésre állás|Százalék|Átlag|A sikeresen befejezett rendelkezésre állási tesztek százalékos aránya|availabilityResult/name,availabilityEredmény/hely|
|availabilityResults/count|Rendelkezésre állási tesztek|Darabszám|Darabszám|Rendelkezésre állási tesztek száma|availabilityResult/name,availabilityEredmény/hely,elérhetőségEredmény/siker|
|availabilityEredmények/időtartam|Rendelkezésre állási teszt időtartama|Ezredmásodperc|Átlag|Rendelkezésre állási teszt időtartama|availabilityResult/name,availabilityEredmény/hely,elérhetőségEredmény/siker|
|browserTimings/networkDuration|Oldalbetöltési hálózat csatlakozási ideje|Ezredmásodperc|Átlag|A felhasználói kérelem és a hálózati kapcsolat között töltött idő. Tartalmazza a DNS-keresési és átviteli kapcsolatot.|None|
|browserTimings/processingDuration|Ügyfél feldolgozási ideje|Ezredmásodperc|Átlag|A dokumentum utolsó bájtjának fogadása és a DOM betöltése között töltött idő. Előfordulhat, hogy az Async-kérelmek feldolgozása még folyamatban van.|None|
|browserTimings/receiveDuration|Válaszidő fogadása|Ezredmásodperc|Átlag|Az első és az utolsó bájt között vagy a kapcsolat lekapcsolásáig töltött idő.|None|
|browserTimings/sendDuration|Kérés ideje elküldése|Ezredmásodperc|Átlag|A hálózati kapcsolat és az első bájt fogadása között töltött idő.|None|
|browserTimings/totalDuration|A böngészőoldal betöltési ideje|Ezredmásodperc|Átlag|A felhasználói kérelemtől a DOM-ig, a stíluslapoktól, a szkriptektől és a képektől kezdve betöltődnek.|None|
|függőségek/számlálás|Függőségi hívások|Darabszám|Darabszám|Az alkalmazás által a külső erőforrásokhoz kezdeményezett hívások száma.|függőség/típus,függőség/teljesítményBucket,függőség/siker,függőség/cél,függőség/eredményKód,művelet/szintetikus,felhő/roleInstance,felhő/szerepkörnév|
|függőségek/időtartam|Függőség időtartama|Ezredmásodperc|Átlag|Az alkalmazás által a külső erőforrásokhoz intézett hívások időtartama.|függőség/típus,függőség/teljesítményBucket,függőség/siker,függőség/cél,függőség/eredményKód,művelet/szintetikus,felhő/roleInstance,felhő/szerepkörnév|
|függőségek/sikertelen|Függőségi hívás hibái|Darabszám|Darabszám|Az alkalmazás által külső erőforrásokhoz kezdeményezett sikertelen függőségi hívások száma.|függőség/típus,függőség/teljesítményBucket,függőség/siker,függőség/cél,függőség/eredményKód,művelet/szintetikus,felhő/roleInstance,felhő/szerepkörnév|
|oldalmegtekintések/számlálók|Oldalnézetek|Darabszám|Darabszám|Az oldalmegtekintések száma.|művelet/szintetikus,felhő/roleName|
|oldalMegtekintések/időtartam|Oldalnézet betöltési ideje|Ezredmásodperc|Átlag|Oldalnézet betöltési ideje|művelet/szintetikus,felhő/roleName|
|performanceCounters/requestExecutionTime|HTTP-kérelem végrehajtási ideje|Ezredmásodperc|Átlag|A legutóbbi kérelem végrehajtási ideje.|felhő/roleInstance|
|performanceCounters/requestsInQueue|HTTP-kérelmek az alkalmazásvárólistában|Darabszám|Átlag|Az alkalmazáskérelem-várólista hossza.|felhő/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-kérelmek aránya|CountPerSzekszekundum|Átlag|Az alkalmazáshoz érkező összes kérelem másodpercenkénti aránya ASP.NET.Rate of all requests to the application per second from ASP.NET.|felhő/roleInstance|
|performanceCounters/exceptionsPerSecond|Kivétel aránya|CountPerSzekszekundum|Átlag|Az ablakoknak jelentett kezelt és nem kezelt kivételek száma, beleértve a .NET kivételeket és a .NET kivételekké konvertált nem felügyelt kivételeket.|felhő/roleInstance|
|performanceCounters/processIOBytesPerSecond|Io-sebesség feldolgozása|BytesPerSecond|Átlag|Fájlokba, hálózatba és eszközökbe másodpercenként imitálta és írt bájtok száma.|felhő/roleInstance|
|performanceCounters/processCpuPercentage|Folyamat PROCESSZOR|Százalék|Átlag|Az eltelt idő azon százaléka, amelyet az összes folyamatszál a processzor segítségével az utasítások végrehajtására használt. Ez 0 és 100 között változhat. Ez a mérőszám csak a w3wp-folyamat teljesítményét jelzi.|felhő/roleInstance|
|performanceCounters/processorCpuPercentage|Processzor idő|Százalék|Átlag|A processzor által nem tétlen szálakban töltött idő hány százaléka.|felhő/roleInstance|
|performanceCounters/memoryAvailableBytes|Igénybe vehető memória|Bájt|Átlag|A fizikai memória azonnal rendelkezésre áll egy folyamathoz való hozzárendeléshez vagy rendszerhasználatra.|felhő/roleInstance|
|performanceCounters/processPrivateBytes|Magánbájtok feldolgozása|Bájt|Átlag|A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve.|felhő/roleInstance|
|kérések/időtartam|Szerverválaszidő|Ezredmásodperc|Átlag|A HTTP-kérelem fogadása és a válasz elküldésének befejezése között töltött idő.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|kérések/számlálás|Kiszolgálói kérelmek|Darabszám|Darabszám|A teljesített HTTP-kérelmek száma.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|kérések/sikertelen|Sikertelen kérelmek|Darabszám|Darabszám|Sikertelenként megjelölt HTTP-kérelmek száma. A legtöbb esetben ezek a kérelmek egy válaszkód >= 400 és nem egyenlő 401.|request/performanceBucket,request/resultCode,request/success,operation/synthetic,cloud/roleInstance,cloud/roleName|
|kérések/díj|Kiszolgálókérési arány|CountPerSzekszekundum|Átlag|A kiszolgálókérelmek másodpercenkénti aránya|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|kivételek/számlálás|Kivételek|Darabszám|Darabszám|Az összes nem felnemfogott kivétel összesített száma.|felhő/roleName,felhő/roleInstance,ügyfél/típus|
|kivételek/böngésző|Böngészőkivételek|Darabszám|Darabszám|A böngészőben felnem fogott kivételek száma.|ügyfél/isServer,felhő/roleName|
|kivételek/kiszolgáló|Kiszolgálói kivételek|Darabszám|Darabszám|A kiszolgálóalkalmazásban felnem fogott kivételek száma.|ügyfél/isServer,felhő/roleName,cloud/roleInstance|
|nyomok/szám|Hívásláncok|Darabszám|Darabszám|Nyomkövetési dokumentumok száma|trace/severityLevel,operation/synthetic,cloud/roleName,cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|connectedDeviceCount (connectedDeviceCount)|Csatlakoztatott eszközök összesen|Darabszám|Átlag|Az IoT Centralhoz csatlakoztatott eszközök száma|None|
|c2d.property.read.success|Sikeres eszköztulajdonság-olvasás az IoT-központból|Darabszám|Összesen|Az IoT Central-ból kezdeményezett összes sikeres tulajdonságolvasás száma|None|
|c2d.property.read.failure|Sikertelen eszköztulajdonság-olvasás az IoT-központból|Darabszám|Összesen|Az IoT Centraltól kezdeményezett összes sikertelen tulajdonságolvasás oka|None|
|d2c.property.read.success|Sikeres eszköztulajdonság-olvasás eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikeres tulajdonságolvasás száma|None|
|d2c.property.read.failure|Az eszközökről beolvasás sikertelen eszköztulajdonság-olvasás|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikertelen tulajdonságolvasás oka|None|
|c2d.property.update.success|Sikeres eszköztulajdonság-frissítések az IoT Centraltól|Darabszám|Összesen|Az IoT Centraltól kezdeményezett összes sikeres tulajdonságfrissítés száma|None|
|c2d.property.update.failure|Sikertelen eszköztulajdonság-frissítések az IoT Centraltól|Darabszám|Összesen|Az IoT Centraltól kezdeményezett sikertelen tulajdonságfrissítések száma|None|
|d2c.property.update.success|Sikeres eszköztulajdonság-frissítések eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett összes sikeres tulajdonságfrissítés száma|None|
|d2c.property.update.failure|Sikertelen eszköztulajdonság-frissítések eszközökről|Darabszám|Összesen|Az eszközökről kezdeményezett sikertelen tulajdonságfrissítések száma|None|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServiceApiHit|Szolgáltatás Api-találatai összesen|Darabszám|Darabszám|Az összes szolgáltatás API-lekérésének száma|Tevékenységtípus,Tevékenységnév|
|ServiceApiLatency (SzervizapiLatency)|Általános szolgáltatásapi-késés|Ezredmásodperc|Átlag|A szolgáltatás API-kéréseinek teljes késése|ActivityType,Tevékenységnév,StatusCode,StatusCodeClass|
|ServiceApiResult|Szolgáltatás api-eredményeinek összesen|Darabszám|Darabszám|Az összes szolgáltatásapi-eredmények száma|ActivityType,Tevékenységnév,StatusCode,StatusCodeClass|
|TelítettségCipődoboz|Teljes Vault telítettség|Százalék|Átlag|Használt tárolókapacitás|ActivityType,ActivityName,TransactionType|
|Rendelkezésre állás|Teljes tártároló elérhetősége|Százalék|Átlag|A tároló kérések elérhetősége|ActivityType,Tevékenységnév,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Fürtök

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Gyorsítótárhasználata|Gyorsítótár kihasználtsága|Százalék|Átlag|Kihasználtsági szint a fürthatókörben|None|
|QueryDuration (Lekérdezésidőtartama)|Lekérdezés időtartama|Ezredmásodperc|Átlag|Lekérdezések időtartama másodpercben|Lekérdezésállapota|
|LenyelésKitilizáció|Lenyelés kihasználtsága|Százalék|Átlag|A fürtben használt betöltési tárolóhelyek aránya|None|
|KeepAlive (Életben tartás)|Életben maradni|Darabszám|Átlag|A józanság ellenőrzése azt jelzi, hogy a fürt válaszol a lekérdezésekre|None|
|BetöltésvolumeinMB|Betöltési kötet (MB-ban)|Darabszám|Összesen|A fürtbe bevitt adatok teljes mennyisége (MB-ban)|Adatbázis|
|BetöltésLatencyInSeconds|Betöltési késés (másodpercben)|Másodperc|Átlag|Betöltési idő a forrástól (pl. az üzenet az EventHubon van) másodpercek alatt a fürthöz|None|
|EseményekProcessedForEventHubs|Feldolgozott események (esemény-/IoT-központok esetén)|Darabszám|Összesen|A fürt által az Event/IoT Hubból történő betöltéskor feldolgozott események száma|EventStatus|
|Betöltéseredménye|Lenyelési eredmény|Darabszám|Darabszám|Lenyelési műveletek száma|Betöltéseredményrészletei|
|CPU|CPU|Százalék|Átlag|CPU-kihasználtsági szint|None|
|ContinuousExportNumOfRecordsExportált|Folyamatos exportálás – az exportált rekordok száma|Darabszám|Összesen|Az exportálási művelet során írt összes tárolási műtermékhez exportált és kilőtt rekordok száma|ContinuousExportName,Adatbázis|
|Exportálás Kihasználása|Kihasználtság exportálása|Százalék|Maximum|Exportálás kihasználtsága|None|
|Folyamatos exportálásfüggő száma|Folyamatos exportálás függőben lévő száma|Darabszám|Maximum|A végrehajtásra kész függőben lévő folyamatos exportálási feladatok száma|None|
|ContinuousExportMaxLatenessMinutes|Folyamatos exportálási maximális késés|Darabszám|Maximum|A fürt folyamatos exportálási feladatai által jelentett késés (percben)|None|
|ContinuousExportResult|Folyamatos exportálás eredménye|Darabszám|Darabszám|Azt jelzi, hogy a folyamatos exportálás sikeres vagy sikertelen volt-e.|ContinuousExportName,Eredmény,Adatbázis|
|StreamingIngestIdőtartam|Streamelési idő|Ezredmásodperc|Átlag|Streamelési időtartam ezredmásodpercben|None|
|StreamingIngestDataRate|A betöltési adatok átviteli sebessége|Darabszám|Átlag|A betöltési adatsebesség streamelése (MB másodpercenként)|None|
|SteamingingestRequestRate|Betöltési kérelem sebességének streamelése|Darabszám|RaterequestsPerSecond (RaterequestsPerSzekszekszek|A betöltési kérelmek sebességének streamelése (kérelmek másodpercenként)|None|
|StreamingIngestResults (StreamingestResults)|A betöltés i.|Darabszám|Átlag|A betöltési eredmény streamelése|Eredmény|
|TotalNumberOfConcurrentQueries|Az egyidejű lekérdezések teljes száma|Darabszám|Összesen|Az egyidejű lekérdezések teljes száma|None|
|TotalNumberOfThrottledQueries|A szabályozott lekérdezések teljes száma|Darabszám|Összesen|A szabályozott lekérdezések teljes száma|None|
|TotalNumberOfThrottledParancsok|A szabályozott parancsok száma összesen|Darabszám|Összesen|A szabályozott parancsok száma összesen|CommandType (CommandType)|
|TotalNumberOfExtents|A terjedések száma összesen|Darabszám|Összesen|Az adatterjedések teljes száma|None|
|InstanceCount (Példányszám)|Példányszám|Darabszám|Átlag|Példányok teljes száma|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/munkafolyamatok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Futtatások Started|Futtatások indítása|Darabszám|Összesen|A munkafolyamat-futtatások száma.|None|
|Futtatások befejezve|Futtatások befejezve|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|None|
|FuttatásSikeres|Sikeres futtatások|Darabszám|Összesen|A munkafolyamat-futtatások száma sikerült.|None|
|FuttatásFailed|Sikertelen futtatások|Darabszám|Összesen|A munkafolyamat-futtatások száma nem sikerült.|None|
|RunsCancelled|Futtatások megszakítva|Darabszám|Összesen|Megszakított munkafolyamatok száma.|None|
|Futtatás késése|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|None|
|RunSuccessLatency (RunSuccessLatency)|Sikeres késés futtatása|Másodperc|Átlag|A sikeres munkafolyamat késése fut.|None|
|RunThrottledEsemények|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-művelet vagy az eseményindító kontróeseményeinek száma.|None|
|RunStartThrottledEsemények|Futás –Szabályozású események futtatása|Darabszám|Összesen|A munkafolyamat-futtatási indítási események száma.|None|
|RunFailureSzázalék|Futtatási hiba százaléka|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya nem sikerült.|None|
|Indított műveletek|Megkezdett műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|None|
|Végrehajtott műveletek|Végrehajtott műveletek |Darabszám|Összesen|A végrehajtott munkafolyamat-műveletek száma.|None|
|Sikeres műveletek|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|None|
|Sikertelen műveletek|Sikertelen műveletek |Darabszám|Összesen|A sikertelen munkafolyamat-műveletek száma.|None|
|Kihagyott műveletek|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|None|
|ActionLatency (MűveletÁtlika)|Művelet késése |Másodperc|Átlag|Befejezett munkafolyamat-műveletek késése.|None|
|ActionSuccessLatency (AkciósikerÁtkés)|Művelet sikeres késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|None|
|ActionThrottledEsemények|Művelet szabályozása események|Darabszám|Összesen|A munkafolyamat-művelet et szabályozta az események száma..|None|
|Aktivált eseményindítók|Elindítva az eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|None|
|Eseményindítók befejezve|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|None|
|Eseményindítók Sikeres|Az eseményindítók sikeresek |Darabszám|Összesen|A munkafolyamat-eseményindítók száma sikerült.|None|
|EseményindítókSikertelen|Sikertelen eseményindítók |Darabszám|Összesen|A sikertelen munkafolyamat-eseményindítók száma.|None|
|Előugró eseményindítók|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|None|
|TriggersFired (TriggersFired)|Kiváltott eseményindítók |Darabszám|Összesen|Az aktivált munkafolyamat-eseményindítók száma.|None|
|TriggerLatency (TriggerLatencia)|Eseményindító késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|None|
|TriggerFireLatency|Tűz késleltetése |Másodperc|Átlag|Az elindított munkafolyamat-eseményindítók késése.|None|
|TriggerSuccessLatency (TriggerSuccessLatency)|Sikeres késés aktiválása |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|None|
|TriggerThrottledEsemények|Irányított események aktiválása|Darabszám|Összesen|A munkafolyamat-eseményindítók száma szabályozott események.|None|
|BillableActionExecutions (Számlázhatóműveletvégrehajtásai)|Számlázható műveletek végrehajtása|Darabszám|Összesen|A számlázásra kerülő munkafolyamat-műveletek végrehajtásai száma.|None|
|SzámlázhatóTriggerExecutions|Számlázható eseményindító-végrehajtások|Darabszám|Összesen|A számlázást végző munkafolyamat-eseményindítók végrehajtásának száma.|None|
|Összes számlázható végrehajtás|Számlázható kivégzések összesen|Darabszám|Összesen|A számlázott munkafolyamat-végrehajtások száma.|None|
|BillingUsageNativeOperation művelet|Natív műveletvégrehajtás számlázási használata|Darabszám|Összesen|A számlázott natív műveletvégrehajtások száma.|None|
|BillingUsageStandardConnector|Számlázási használat szabványos összekötő-végrehajtások esetén|Darabszám|Összesen|A számlázott szabványos összekötő-végrehajtások száma.|None|
|BillingUsageConsumptionConsumption|Számlázási használat a tárolási felhasználás végrehajtásaihez|Darabszám|Összesen|A számlázott tárfelhasználás-végrehajtások száma.|None|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Futtatások Started|Futtatások indítása|Darabszám|Összesen|A munkafolyamat-futtatások száma.|None|
|Futtatások befejezve|Futtatások befejezve|Darabszám|Összesen|A befejezett munkafolyamat-futtatások száma.|None|
|FuttatásSikeres|Sikeres futtatások|Darabszám|Összesen|A munkafolyamat-futtatások száma sikerült.|None|
|FuttatásFailed|Sikertelen futtatások|Darabszám|Összesen|A munkafolyamat-futtatások száma nem sikerült.|None|
|RunsCancelled|Futtatások megszakítva|Darabszám|Összesen|Megszakított munkafolyamatok száma.|None|
|Futtatás késése|Késés futtatása|Másodperc|Átlag|A befejezett munkafolyamat-futtatások késése.|None|
|RunSuccessLatency (RunSuccessLatency)|Sikeres késés futtatása|Másodperc|Átlag|A sikeres munkafolyamat késése fut.|None|
|RunThrottledEsemények|Szabályozott események futtatása|Darabszám|Összesen|A munkafolyamat-művelet vagy az eseményindító kontróeseményeinek száma.|None|
|RunStartThrottledEsemények|Futás –Szabályozású események futtatása|Darabszám|Összesen|A munkafolyamat-futtatási indítási események száma.|None|
|RunFailureSzázalék|Futtatási hiba százaléka|Százalék|Összesen|A munkafolyamat-futtatások százalékos aránya nem sikerült.|None|
|Indított műveletek|Megkezdett műveletek |Darabszám|Összesen|Az elindított munkafolyamat-műveletek száma.|None|
|Végrehajtott műveletek|Végrehajtott műveletek |Darabszám|Összesen|A végrehajtott munkafolyamat-műveletek száma.|None|
|Sikeres műveletek|Sikeres műveletek |Darabszám|Összesen|A sikeres munkafolyamat-műveletek száma.|None|
|Sikertelen műveletek|Sikertelen műveletek |Darabszám|Összesen|A sikertelen munkafolyamat-műveletek száma.|None|
|Kihagyott műveletek|Kihagyott műveletek |Darabszám|Összesen|A kihagyott munkafolyamat-műveletek száma.|None|
|ActionLatency (MűveletÁtlika)|Művelet késése |Másodperc|Átlag|Befejezett munkafolyamat-műveletek késése.|None|
|ActionSuccessLatency (AkciósikerÁtkés)|Művelet sikeres késése |Másodperc|Átlag|A sikeres munkafolyamat-műveletek késése.|None|
|ActionThrottledEsemények|Művelet szabályozása események|Darabszám|Összesen|A munkafolyamat-művelet et szabályozta az események száma..|None|
|Aktivált eseményindítók|Elindítva az eseményindítók |Darabszám|Összesen|Az elindított munkafolyamat-eseményindítók száma.|None|
|Eseményindítók befejezve|Befejezett eseményindítók |Darabszám|Összesen|A befejezett munkafolyamat-eseményindítók száma.|None|
|Eseményindítók Sikeres|Az eseményindítók sikeresek |Darabszám|Összesen|A munkafolyamat-eseményindítók száma sikerült.|None|
|EseményindítókSikertelen|Sikertelen eseményindítók |Darabszám|Összesen|A sikertelen munkafolyamat-eseményindítók száma.|None|
|Előugró eseményindítók|Kihagyott eseményindítók|Darabszám|Összesen|A kihagyott munkafolyamat-eseményindítók száma.|None|
|TriggersFired (TriggersFired)|Kiváltott eseményindítók |Darabszám|Összesen|Az aktivált munkafolyamat-eseményindítók száma.|None|
|TriggerLatency (TriggerLatencia)|Eseményindító késése |Másodperc|Átlag|A befejezett munkafolyamat-eseményindítók késése.|None|
|TriggerFireLatency|Tűz késleltetése |Másodperc|Átlag|Az elindított munkafolyamat-eseményindítók késése.|None|
|TriggerSuccessLatency (TriggerSuccessLatency)|Sikeres késés aktiválása |Másodperc|Átlag|A sikeres munkafolyamat-eseményindítók késése.|None|
|TriggerThrottledEsemények|Irányított események aktiválása|Darabszám|Összesen|A munkafolyamat-eseményindítók száma szabályozott események.|None|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Munkafolyamat-processzor használata integrációs szolgáltatáskörnyezethez|Százalék|Átlag|Munkafolyamat-processzor használata integrációs szolgáltatási környezethez.|None|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Munkafolyamat-memória használata az integrációs szolgáltatáskörnyezethez|Százalék|Átlag|Munkafolyamat-memória használat az integrációs szolgáltatási környezethez.|None|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Összekötő processzor használata integrációs szolgáltatáskörnyezethez|Százalék|Átlag|Összekötő processzor használata integrációs szolgáltatási környezetben.|None|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Összekötőmemória használata az integrációs szolgáltatáskörnyezethez|Százalék|Átlag|Összekötőmemória használata az integrációs szolgáltatás környezetéhez.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/munkaterületek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Megszakított futtatások|Megszakított futtatások|Darabszám|Összesen|A munkaterülethez megszakított futtatások száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Kért futtatások megszakítása|Kért futtatások megszakítása|Darabszám|Összesen|Azon futtatások száma, amelyekben a munkaterülethez megszakítást kértek|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Befejezett futtatások|Befejezett futtatások|Darabszám|Összesen|A munkaterület sikeresen befejezett futtatásainak száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Sikertelen futtatások|Sikertelen futtatások|Darabszám|Összesen|A munkaterület sikertelen futásainak száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Futások véglegesítése|Futások véglegesítése|Darabszám|Összesen|A munkaterület véglegesítési állapotába lépett futtatások száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Nem válaszol fut|Nem válaszol fut|Darabszám|Összesen|A munkaterületre nem válaszoló futtatások száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Nem indított futtatások|Nem indított futtatások|Darabszám|Összesen|A munkaterület indítása in not started állapotban lévő futtatások száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Futások előkészítése|Futások előkészítése|Darabszám|Összesen|A munkaterületre készülő futtatások száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Kiépítési futtatások|Kiépítési futtatások|Darabszám|Összesen|A munkaterülethez kiépített futtatások száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Várólistán lévő futtatások|Várólistán lévő futtatások|Darabszám|Összesen|A munkaterület várólistára helyezett futásainak száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Elkezdett futtatások|Elkezdett futtatások|Darabszám|Összesen|A munkaterülethez megkezdett futtatások száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Kezdő futtatások|Kezdő futtatások|Darabszám|Összesen|A munkaterülethez megkezdett futtatások száma|Eset,RunType,Közzétett folyamatkörnyezetazonosító,Számítási típus,PipelineStepType|
|Hibák|Hibák|Darabszám|Összesen|Futtatási hibák száma ezen a munkaterületen|Forgatókönyv|
|Figyelmeztetések|Figyelmeztetések|Darabszám|Összesen|A futtatási figyelmeztetések száma ebben a munkaterületen|Forgatókönyv|
|Sikeres modellregisztika|Sikeres modellregisztika|Darabszám|Összesen|Az ebben a munkaterületen sikeres modellregisztrációk száma|Forgatókönyv|
|A modellregiszter nem sikerült|A modellregiszter nem sikerült|Darabszám|Összesen|A munkaterületen meghibásodott modellregisztrációk száma|Eset,StatusCode|
|Modell üzembe helyezése elindítva|Modell üzembe helyezése elindítva|Darabszám|Összesen|Az ebben a munkaterületen elindított modelltelepítések száma|Forgatókönyv|
|A modell telepítése sikerült|A modell telepítése sikerült|Darabszám|Összesen|Az ebben a munkaterületen sikeres modelltelepítések száma|Forgatókönyv|
|A modell telepítése sikertelen|A modell telepítése sikertelen|Darabszám|Összesen|A munkaterületen meghibásodott modelltelepítések száma|Eset,StatusCode|
|Összes csomópont|Összes csomópont|Darabszám|Átlag|Az összes csomópont száma. Ez az összeg tartalmaz néhány aktív csomópontot, idle csomópontokat, használhatatlan csomópontokat, előre megadott csomópontokat, Csomópontok elhagyását|Eset,Fürtnév|
|Aktív csomópontok|Aktív csomópontok|Darabszám|Átlag|Acitve csomópontok száma. Ezek azok a csomópontok, amelyek aktívan futtatnak egy feladatot.|Eset,Fürtnév|
|Tétlen csomópontok|Tétlen csomópontok|Darabszám|Átlag|Az alacsony annektcsomópontok száma. Az idle csomópontok azok a csomópontok, amelyek nem futtatnak semmilyen feladatot, de el fogadhatnak új feladatot, ha rendelkezésre állnak.|Eset,Fürtnév|
|Használhatatlan csomópontok|Használhatatlan csomópontok|Darabszám|Átlag|Használhatatlan csomópontok száma. A használhatatlan csomópontok nem működnek valamilyen megoldhatatlan probléma miatt. Az Azure újrahasznosítja ezeket a csomópontokat.|Eset,Fürtnév|
|Elődpontos csomópontok|Elődpontos csomópontok|Darabszám|Átlag|Preempted csomópontok száma. Ezek a csomópontok az alacsony prioritású csomópontok, amelyek elvesznek a rendelkezésre álló csomópontkészlet.|Eset,Fürtnév|
|Csomópontok elhagyása|Csomópontok elhagyása|Darabszám|Átlag|A kilépő csomópontok száma. A csomópontok elhagyása azok a csomópontok, amelyek éppen befejezte a feladat feldolgozását, és tétlen állapotba kerülnek.|Eset,Fürtnév|
|Magok összesen|Magok összesen|Darabszám|Átlag|Az összes mag száma|Eset,Fürtnév|
|Aktív magok|Aktív magok|Darabszám|Átlag|Aktív magok száma|Eset,Fürtnév|
|Alapjárati magok|Alapjárati magok|Darabszám|Átlag|Tétlen magok száma|Eset,Fürtnév|
|Használhatatlan magok|Használhatatlan magok|Darabszám|Átlag|Használhatatlan magok száma|Eset,Fürtnév|
|Preempted magok|Preempted magok|Darabszám|Átlag|Preempted magok száma|Eset,Fürtnév|
|Elhagyva magok|Elhagyva magok|Darabszám|Átlag|A kilépő magok száma|Eset,Fürtnév|
|Kvótakihasználtság százaléka|Kvótakihasználtság százaléka|Darabszám|Átlag|A felhasznált kvóta százaléka|Eset,Fürtnév,VmFamilyName,VmPriority|
|Cpukihasználtság|Cpukihasználtság|Darabszám|Átlag|CPU (előzetes verzió)|Eset,runId,NodeId,CreatedTime|
|GpuKiás|GpuKiás|Darabszám|Átlag|GPU (előzetes verzió)|Eset,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/fiókok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használat|Használat|Darabszám|Darabszám|AZ API-hívások száma|Apicategory,ApiName,ResultType,ResponseCode|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|Az API-k elérhetősége|Apicategory,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingVégpontok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő forgalom adatainak mennyisége bájtban.|Kimenetformátum|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres befejezés a késés végéig|Ezredmásodperc|Átlag|A sikeres kérelmek átlagos késése ezredmásodpercben.|Kimenetformátum|
|Kérelmek|Kérelmek|Darabszám|Összesen|A streamelési végpontra irányuló kérelmek.|Outputformat,HttpStatusCode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Eszközkvóta|Eszközkvóta|Darabszám|Átlag|Hány eszköz engedélyezett az aktuális médiaszolgáltatás-fiókhoz?|None|
|Eszközszáma|Eszközdarabszáma|Darabszám|Átlag|Hány eszköz van már létrehozva az aktuális médiaszolgáltatás-fiókban|None|
|AssetQuotaUsedSzázalék|Használt eszközkvóta százalék|Százalék|Átlag|Eszköz használt százaléka az aktuális médiaszolgáltatás-fiókban|None|
|ContentKeyPolicyQuota (ContentKeyPolicyQuota)|Tartalomkulcs-házirendkvóta|Darabszám|Átlag|Hány tartalomkulcs-vezérlő engedélyezett az aktuális médiaszolgáltatás-fiókhoz?|None|
|ContentKeyPolicyCount (Tartalomkulcs-házirendszáma)|Tartalomkulcs-házirendek száma|Darabszám|Átlag|Hány tartalomkulcs-házirend van már létrehozva az aktuális médiaszolgáltatás-fiókban|None|
|ContentKeyPolicyQuotaUsedSzázalék|A tartalomkulcs-házirend használt kvótája százalékban|Százalék|Átlag|Az aktuális médiaszolgáltatás-fiókban használt tartalomkulcs-házirend százaléka|None|
|StreamingPolicyQuota (StreamingPolicyQuota)|Streamelési házirend kvóta|Darabszám|Átlag|Hány streamelési házirend engedélyezett az aktuális médiaszolgáltatás-fiókhoz?|None|
|StreamingPolicyCount (StreamPolicyCount)|Streamelési irányelvek száma|Darabszám|Átlag|Hány streamelési házirend van már létrehozva az aktuális médiaszolgáltatás-fiókban?|None|
|StreamingPolicyQuotaUsedSzázalék|A streamelési szabályzat használt kvótája százalékban|Százalék|Átlag|Az aktuális médiaszolgáltatás-fiókban használt streamelési házirend százaléka|None|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|EszközökKonvertálva|Átváltott eszközök|Darabszám|Összesen|Az átváltott eszközök teljes száma|AppId,ResourceId,SDKVersion|
|ActiveRenderingSessions|Aktív renderelési munkamenetek|Darabszám|Összesen|Az aktív renderelési munkamenetek teljes száma|AppId,ResourceId,SessionType,SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Átlagos olvasási átkés|Átlagos olvasási késleltetés|Ezredmásodperc|Átlag|Átlagos olvasási késleltetés műveletenként ezredmásodpercben|None|
|Átlagos íráskésleltetés|Átlagos írási késés|Ezredmásodperc|Átlag|Átlagos írási késés műveletenként ezredmásodpercben|None|
|VolumeLogicalSize (VolumeLogicalSize)|Felhasznált kötet mérete|Bájt|Átlag|A kötet logikai mérete (használt bájt)|None|
|VolumeSnapshotSize (VolumeSnapshotSize)|Kötet pillanatképének mérete|Bájt|Átlag|A kötet összes pillanatképének mérete|None|
|ReadIops között|Olvasás iops|CountPerSzekszekundum|Átlag|Olvasás be/ki műveletek másodpercenként|None|
|WriteIops (WriteIops)|Iops írása|CountPerSzekszekundum|Átlag|Írásbe/ki műveletek másodpercenként|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kötetkészletfelosztott|A kötetmérethez rendelt készlet|Bájt|Átlag|A készlet felosztott használt mérete|None|
|VolumePoolTotalLogicalSize|Készlet felhasznált mérete|Bájt|Átlag|A készlethez tartozó összes kötet logikai méretének összege|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BytesSentRate|Elküldött bájtok|Bájt|Összesen|A hálózati csatoló által küldött bájtok száma|None|
|BájtBeRate|Fogadott bájtok|Bájt|Összesen|A hálózati adapter fogadott bájtok száma|None|
|CsomagokSentRate|Elküldött csomagok|Darabszám|Összesen|A hálózati illesztő által küldött csomagok száma|None|
|CsomagokFogadott|Fogadott csomagok|Darabszám|Összesen|A hálózati adapter által fogadott csomagok száma|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|VipElérhetőség|Az adatelérési út elérhetősége|Darabszám|Átlag|Átlagos terheléselosztó adatútvonal-elérhetősége időtartamonként|ElőtérIP-cím,FrontendPort|
|DipElérhető|Állapotvizsgálat állapota|Darabszám|Átlag|Átlagos terheléselosztó állapotminta állapota időtartamonként|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount (Bájtszáma)|Bájtszáma|Darabszám|Összesen|Az időszakon belül továbbított bájtok teljes száma|FrontendIPAddress,FrontendPort,Irány|
|Csomagszáma|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|FrontendIPAddress,FrontendPort,Irány|
|SYNCount (SZINK)|SYN-szám|Darabszám|Összesen|Az időtartamon belül továbbított SYN-csomagok teljes száma|FrontendIPAddress,FrontendPort,Irány|
|SnatConnectionCount (SnatConnectionCount)|SNAT-kapcsolatok száma|Darabszám|Összesen|Az időszakon belül létrehozott új SNAT-kapcsolatok teljes száma|ElőtérIP-cím,HáttérvégpontIP-cím,ConnectionState|
|FelosztottSnatPorts|Lefoglalt SNAT-portok (előzetes verzió)|Darabszám|Összesen|Az időszakon belül lefoglalt SNAT-portok teljes száma|ElőtérIP-cím,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|UsedSnatPorts|Használt SNAT-portok (előzetes verzió)|Darabszám|Összesen|Az időtartamon belül felhasznált SNAT-portok teljes száma|ElőtérIP-cím,BackendIPAddress,ProtocolType,IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume (Lekérdezési kötet)|Lekérdezés imtora|Darabszám|Összesen|DNS-zónában kiszolgált lekérdezések száma|None|
|Rekordsor száma|Rekordkészlet száma|Darabszám|Maximum|Rekordkészletek száma egy DNS-zónában|None|
|RecordSetCapacityKihasználtság|Rekordkészlet kapacitáskihasználtsága|Százalék|Maximum|A DNS-zóna által használt rekordkészlet-kapacitás százaléka|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PacketsInDDoS|Bejövő csomagok DDoS|CountPerSzekszekundum|Maximum|Bejövő csomagok DDoS|None|
|PacketsDroppedDDoS|DDoS-t eldobott bejövő csomagok|CountPerSzekszekundum|Maximum|DDoS-t eldobott bejövő csomagok|None|
|PacketsForwardedDDoS|Továbbított bejövő csomagok DDoS|CountPerSzekszekundum|Maximum|Továbbított bejövő csomagok DDoS|None|
|TCPPacketsInDDoS|Bejövő TCP-csomagok DDoS|CountPerSzekszekundum|Maximum|Bejövő TCP-csomagok DDoS|None|
|TCPPacketsDroppedDDoS|DDoS-t eldobott bejövő TCP-csomagok|CountPerSzekszekundum|Maximum|DDoS-t eldobott bejövő TCP-csomagok|None|
|TCPPacketsForwardedDDoS|Bejövő TCP-csomagok továbbított DDoS|CountPerSzekszekundum|Maximum|Bejövő TCP-csomagok továbbított DDoS|None|
|UDPPacketsInDDoS|Bejövő UDP-csomagok DDoS|CountPerSzekszekundum|Maximum|Bejövő UDP-csomagok DDoS|None|
|UDPPacketsDroppedDDoS|DDoS-t eldobott bejövő UDP-csomagok|CountPerSzekszekundum|Maximum|DDoS-t eldobott bejövő UDP-csomagok|None|
|UDPPacketsForwardedDDoS|Bejövő UDP-csomagok továbbított DDoS|CountPerSzekszekundum|Maximum|Bejövő UDP-csomagok továbbított DDoS|None|
|BytesInDDoS|Bejövő ddos bájtok|BytesPerSecond|Maximum|Bejövő ddos bájtok|None|
|Eldobott bájtokDDoS|Eldobott bejövő bájtok DDoS|BytesPerSecond|Maximum|Eldobott bejövő bájtok DDoS|None|
|BytesForwardedDDoS|Bejövő továbbított DDoS-bájtok|BytesPerSecond|Maximum|Bejövő továbbított DDoS-bájtok|None|
|TCPBytesInDDoS|Bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok DDoS|None|
|TCPBytesDroppedDDoS|Eldobott bejövő TCP-bájtok DDoS|BytesPerSecond|Maximum|Eldobott bejövő TCP-bájtok DDoS|None|
|TCPBytesForwardedDDoS|Bejövő TCP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő TCP-bájtok továbbított DDoS|None|
|UDPBytesInDDoS|Bejövő UDP-bájtok DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok DDoS|None|
|UDPBytesDroppedDDoS|Kimenő UDP-bájtok eldobott DDoS|BytesPerSecond|Maximum|Kimenő UDP-bájtok eldobott DDoS|None|
|UDPBytesForwardedDDoS|Bejövő UDP-bájtok továbbított DDoS|BytesPerSecond|Maximum|Bejövő UDP-bájtok továbbított DDoS|None|
|Ifunderddostámadás|A DDoS támadás vagy nem|Darabszám|Maximum|A DDoS támadás vagy nem|None|
|DDoSTriggerTCPPackets|Bejövő TCP-csomagok a DDoS-kockázatcsökkentés elindításához|CountPerSzekszekundum|Maximum|Bejövő TCP-csomagok a DDoS-kockázatcsökkentés elindításához|None|
|DDoSTriggerUDPPackets|Bejövő UDP-csomagok a DDoS-kockázatcsökkentés elindításához|CountPerSzekszekundum|Maximum|Bejövő UDP-csomagok a DDoS-kockázatcsökkentés elindításához|None|
|DDoSTriggerSYNPackets|Bejövő SYN-csomagok a DDoS-kockázatcsökkentés elindításához|CountPerSzekszekundum|Maximum|Bejövő SYN-csomagok a DDoS-kockázatcsökkentés elindításához|None|
|VipElérhetőség|Az adatelérési út elérhetősége|Darabszám|Átlag|Az IP-cím átlagos elérhetősége időtartamonként|Port|
|ByteCount (Bájtszáma)|Bájtszáma|Darabszám|Összesen|Az időszakon belül továbbított bájtok teljes száma|Port,Irány|
|Csomagszáma|Csomagok száma|Darabszám|Összesen|Az időszakon belül továbbított csomagok teljes száma|Port,Irány|
|SynCount (Szinka)|SYN-szám|Darabszám|Összesen|Az időtartamon belül továbbított SYN-csomagok teljes száma|Port,Irány|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PingMeshAverageKörmenet-objektumok|A pingelések oda-vissza olásának ideje a virtuális géphez|Ezredmásodperc|Átlag|A célvirtuális gépnek küldött pingek oda-vissza útja|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Sikertelen pingelések a virtuális géphez|Százalék|Átlag|A sikertelen pingek száma a célvirtuális gép összes elküldött pingjeszámára|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ApplicationRuleHit alkalmazás|Alkalmazásszabályok találati száma|Darabszám|Összesen|Az alkalmazásszabályok találatának száma|Állapot,Ok,Protokoll|
|HálózatiRuleHit|Hálózati szabályok találati száma|Darabszám|Összesen|A hálózati szabályok találatának száma|Állapot,Ok,Protokoll|
|Tűzfal-egészségügyi|Tűzfal állapota|Százalék|Átlag|Tűzfal állapota|Állapot,Ok|
|Feldolgozott adatok|Feldolgozott adatok|Bájt|Összesen|A tűzfal által feldolgozott adatok teljes mennyisége|None|
|SNATPortKihasználtság|SNAT-port kihasználtsága|Százalék|Átlag|SNAT-port kihasználtsága|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Teljesítmény|Teljesítmény|BytesPerSecond|Átlag|Az Application Gateway által kiszolgált bájtok száma másodpercenként|None|
|Nem megfelelőhostcount|Nem megfelelő gazdagépszám|Darabszám|Átlag|A nem megfelelő állapotú háttérállomások száma|Háttérvégpont-beállításkészlet|
|EgészségeshostCount|Egészséges gazdagép száma|Darabszám|Átlag|Kifogástalan háttér-gazdagépek száma|Háttérvégpont-beállításkészlet|
|TotalRequests|Összes kérelem|Darabszám|Összesen|Az Application Gateway által kézbesített sikeres kérelmek száma|Háttérvégpont-beállításkészlet|
|AvgRequestCountPerHealthyHost|Kérelmek percenként és egészséges gazdagépenként|Darabszám|Átlag|Átlagos kérelmek száma percenként egy kifogástalan háttér-gazdagép egy készletben|Háttérvégpont-beállításkészlet|
|Sikertelen kérések|Sikertelen kérelmek|Darabszám|Összesen|Az Application Gateway által kézbesített sikertelen kérelmek száma|Háttérvégpont-beállításkészlet|
|ResponseStatus (Válasz állapota)|Válasz állapota|Darabszám|Összesen|Az Application Gateway által visszaadott http-válaszállapot|httpstatuscsoport|
|Jelenlegi kapcsolatok|Jelenlegi kapcsolatok|Darabszám|Összesen|Az Application Gateway-rel létrehozott aktuális kapcsolatok száma|None|
|NewConnectionsPerSecond|Új kapcsolatok másodpercenként|CountPerSzekszekundum|Átlag|Az Application Gateway alkalmazásátjáróval létrehozott új kapcsolatok másodpercenként|None|
|Cpukihasználtság|PROCESSZOR-kihasználtság|Százalék|Átlag|Az alkalmazásátjáró aktuális processzorkihasználtsága|None|
|Kapacitásegységek|Aktuális kapacitásegységek|Darabszám|Átlag|Felhasznált kapacitásegységek|None|
|FixedBillableCapacityUnits|Rögzített számlázható kapacitásegységek|Darabszám|Átlag|A felszámított minimális kapacitásegységek|None|
|Becsültszámlázott kapacitásegységek|Becsült számlázott kapacitásegységek|Darabszám|Átlag|A felszámítandó becsült kapacitásegységek|None|
|Számítási egységek|Aktuális számítási egységek|Darabszám|Átlag|Felhasznált számítási egységek|None|
|HáttérvégpontVálaszállapota|Háttérválaszok állapota|Darabszám|Összesen|A háttértagok által létrehozott HTTP-válaszkódok száma. Ez nem tartalmazza az Application Gateway által létrehozott válaszkódokat.|Háttérvégpont-kiszolgáló,háttérsor,háttérrendszerHttpSetting,HttpStatusGroup|
|TlsProtocol|Ügyfél TLS protokollja|Darabszám|Összesen|Az alkalmazásátjáróval kapcsolatot létrehozó ügyfél által kezdeményezett TLS- és nem TLS-kérelmek száma. A TLS protokoll eloszlásának megtekintéséhez szűrje a TLS protokoll dimenziója szerint.|Figyelő,TlsProtocol|
|Küldött bájt|Elküldött bájtok|Bájt|Összesen|Az Alkalmazásátjáró által az ügyfeleknek küldött bájtok teljes száma|Figyelő|
|Fogadott bájt|Fogadott bájtok|Bájt|Összesen|Az Alkalmazásátjáró által az ügyfelektől fogadott bájtok teljes száma|Figyelő|
|Ügyfélrtt|Ügyfél RTT|Ezredmásodperc|Átlag|Az ügyfelek és az Application Gateway közötti átlagos oda-vissza út. Ez a mérőszám azt jelzi, hogy mennyi ideig tart a kapcsolatok létrehozása és a nyugtázás visszaküldése|Figyelő|
|ApplicationGatewayTotalTime alkalmazás|Alkalmazásátjáró teljes ideje|Ezredmásodperc|Átlag|A kérelem feldolgozásához és válaszának elküldéséhez szükséges átlagos idő. Ezt a rendszer a HTTP-kérelem első bájtjának fogadása időpontjától addig az időpontig számítja ki, amikor a válaszküldési művelet befejeződik. Fontos megjegyezni, hogy ez általában magában foglalja az Application Gateway feldolgozási idő, az idő, hogy a kérelem és a válasz csomagok utaznak a hálózaton keresztül, és az idő a háttérkiszolgáló vette, hogy válaszoljon.|Figyelő|
|HáttérendConnectTime|Háttérkapcsolati idő|Ezredmásodperc|Átlag|A háttérkiszolgálóval való kapcsolat létesítésével töltött idő|Figyelő,BackendServer,BackendPool,HáttérrendszerhttpSetting|
|BackendFirstByteResponseTime|Háttér– első bájtválasz ideje|Ezredmásodperc|Átlag|A háttérkiszolgálóval létesített kapcsolat létrehozásának és a válaszfejléc első bájtjának fogadása és a háttérkiszolgáló feldolgozási idejének közelítése között eltelt idő|Figyelő,BackendServer,BackendPool,HáttérrendszerhttpSetting|
|BackendLastByteResponseTime|Háttértartalék utolsó bájtválasz ideje|Ezredmásodperc|Átlag|A háttérkiszolgálóval létesítés és a választörzs utolsó bájtjának fogadása között eltelt idő|Figyelő,BackendServer,BackendPool,HáttérrendszerhttpSetting|
|Egyezésű ekszáma|Webalkalmazás-tűzfal v1 teljes szabályelosztása|Darabszám|Összesen|Webalkalmazás-tűzfal v1 teljes szabályelosztása a bejövő forgalomhoz|Szabálycsoport,Szabályazonosító|
|BlockedCount (BlockedCount)|Webalkalmazás-tűzfal v1 letiltott kérelmek szabályának elosztása|Darabszám|Összesen|Webalkalmazás-tűzfal v1 blokkolt kérelmek szabály ának terjesztése|Szabálycsoport,Szabályazonosító|
|BlockedReqCount között|Webalkalmazás-tűzfal v1 blokkolt kérelmek száma|Darabszám|Összesen|Webalkalmazás-tűzfal v1 blokkolt kérelmek száma|None|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Átlagos sávszélesség|Átjáró S2S-sávszélessége|BytesPerSecond|Átlag|Átjáró átlagos helyek közötti sávszélessége bájt/másodpercben|None|
|P2SBandwidth (P2SBandwidth)|Átjáró P2S-sávszélessége|BytesPerSecond|Átlag|Átjáró átlagos pont-hely sávszélessége bájt/másodpercben|None|
|P2SConnectioncount között|P2S-kapcsolatok száma|Darabszám|Maximum|Átjáró pont-hely közötti kapcsolatszáma|Protocol (Protokoll)|
|TunnelAverageBandwidth (CsatornaÁtlagos sávszélessége)|Alagút sávszélessége|BytesPerSecond|Átlag|Alagút átlagos sávszélessége bájt/másodpercben|Kapcsolatnév,Távoli IP|
|TunnelEgressBytes|Alagút kimenő hídbájtjai|Bájt|Összesen|Alagút kimenő bájtjai|Kapcsolatnév,Távoli IP|
|TunnelIngressBytes|Alagút be- és áttetsző bájtjai|Bájt|Összesen|Alagút bejövő bájtjai|Kapcsolatnév,Távoli IP|
|TunnelEgressPackets|Bújtatási kimenő csomagok|Darabszám|Összesen|Kimenő csomagok száma egy alagútban|Kapcsolatnév,Távoli IP|
|TunnelIngressPacketsCsomagok|Bújtatási csomagok|Darabszám|Összesen|Alagút bejövő csomagszáma|Kapcsolatnév,Távoli IP|
|TunnelEgressPacketDropTSMismatch|Bújtatási kimenő forgalom – TS eltérés a csomag ledobása|Darabszám|Összesen|Kimenő csomag-ejtési szám az alagút forgalomválasztójának eltéréseiből|Kapcsolatnév,Távoli IP|
|TunnelIngressPacketDropTSMismatch|Bújtatás iTS eltéréscsomag-ledobása|Darabszám|Összesen|Bejövő csomagejtési szám az alagút forgalomválasztójának eltérései ből|Kapcsolatnév,Távoli IP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Darabszám|Átlag|Rx fényszint dBm-ben|Kapcsolat,Sáv|
|TxLightLevel|TxLightLevel|Darabszám|Átlag|Tx fényszint dBm-ben|Kapcsolat,Sáv|
|AdminÁllam|AdminÁllam|Darabszám|Átlag|A port rendszergazdai állapota|Hivatkozás|
|LineProtocol (LineProtocol)|LineProtocol (LineProtocol)|Darabszám|Átlag|A port vonalprotokoll-állapota|Hivatkozás|
|PortBitsInPerSecond|BitsInPerSz|CountPerSzekszekundum|Átlag|Bitek be- ésmásodpercenként|Hivatkozás|
|PortBitsOutPerSecond|BitsOutPerSz|CountPerSzekszekundum|Átlag|Bitek az Azure másodpercenkénti kijutása|Hivatkozás|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSz|BitsInPerSz|CountPerSzekszekundum|Átlag|Bitek be- ésmásodpercenként|Társviszony-létesítéstípusa|
|BitsOutPerSz|BitsOutPerSz|CountPerSzekszekundum|Átlag|Bitek az Azure másodpercenkénti kijutása|Társviszony-létesítéstípusa|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSzekszekundum|Átlag|Bitek be- ésmásodpercenként|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSzekszekundum|Átlag|Bitek az Azure másodpercenkénti kijutása|PeeredCircuitSKey|
|BgpElérhető|BGP elérhetősége|Százalék|Átlag|BGP Elérhetőség az MSEE-től minden társ felé.|Társviszony-létesítéstípusa,Társ|
|ArpElérhető|Arp elérhetősége|Százalék|Átlag|ARP Elérhetőség az MSEE-től az összes társ felé.|Társviszony-létesítéstípusa,Társ|
|QosDropBitsInPerSz|DroppedInBitsPerSecond|CountPerSzekszekundum|Átlag|Másodpercenként eldobott adatbe- és be- és az adatok be- és visszaesése|None|
|QosDropBitsOutPerMásodik|DroppedOutBitsPerSecond|CountPerSzekszekundum|Átlag|Másodpercenként eldobott kimenő adatbitek|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSz|BitsInPerSz|CountPerSzekszekundum|Átlag|Bitek be- ésmásodpercenként|None|
|BitsOutPerSz|BitsOutPerSz|CountPerSzekszekundum|Átlag|Bitek az Azure másodpercenkénti kijutása|None|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/kapcsolatok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BitsInPerSz|BitsInPerSz|CountPerSzekszekundum|Átlag|Bitek be- ésmásodpercenként|None|
|BitsOutPerSz|BitsOutPerSz|CountPerSzekszekundum|Átlag|Bitek az Azure másodpercenkénti kijutása|None|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSz|CountPerSzekszekundum|Átlag|Bitek be- ésmásodpercenként|Kapcsolatneve|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSz|CountPerSzekszekundum|Átlag|Bitek az Azure másodpercenkénti kijutása|Kapcsolatneve|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QpsByEndpoint|Végpont által visszaadott lekérdezések|Darabszám|Összesen|A Traffic Manager-végpont adott időkereten belül való visszaadásának száma|Végpontneve|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Végpont állapota végpont szerint|Darabszám|Maximum|1, ha a végpont mintavételi állapota "Engedélyezve", ellenkező esetben 0.|Végpontneve|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitorok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MintavételekFailedPercent|Sikertelen % mintavételek|Százalék|Átlag|A kapcsolatfigyelő szondák %-a nem sikerült|None|
|Átlagosfordulók|Átlagos oda-vissza idő (ms)|Ezredmásodperc|Átlag|A forrás és a cél között küldött kapcsolatfigyelő szondák átlagos oda-vissza ideje (ms)|None|
|ChecksFailedPercent|Sikertelen ellenőrzések (előnézet)|Százalék|Átlag|A kapcsolatfigyelési ellenőrzések %-a sikertelen volt|Forráscím,Forrásnév,Forráserőforrás-azonosító,Forrástípus,Protokoll,Célcím,Célnév,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|Körúttalmunkaidő-gyártók|Oda-vissza út (ms) (előzetes verzió)|Ezredmásodperc|Átlag|Oda-vissza út ezredmásodpercben a kapcsolatfigyelés ellenőrzéséhez|Forráscím,Forrásnév,Forráserőforrás-azonosító,Forrástípus,Protokoll,Célcím,Célnév,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérésszáma|Kérelmek száma|Darabszám|Összesen|A HTTP/S proxy által kiszolgált ügyfélkérelmek száma|httpstatus,httpstatusgroup,ügyfélrégió,ügyfélország|
|Kérésméretet|A kérelem mérete|Bájt|Összesen|Az ügyfelek től a HTTP/S-proxynak kérésként küldött bájtok száma|httpstatus,httpstatusgroup,ügyfélrégió,ügyfélország|
|Válaszméret|Válasz mérete|Bájt|Összesen|A HTTP/S proxytól az ügyfeleknek válaszként küldött bájtok száma|httpstatus,httpstatusgroup,ügyfélrégió,ügyfélország|
|Számlázható válaszméret|Számlázható válasz mérete|Bájt|Összesen|A HTTP/S-proxy által az ügyfeleknek válaszként küldött számlázható bájtok száma (kérésenként legalább 2 KB).|httpstatus,httpstatusgroup,ügyfélrégió,ügyfélország|
|Háttérvégpontkérése|Háttér-kérelem száma|Darabszám|Összesen|A HTTP/S proxyból a háttérrendszereknek küldött kérelmek száma|HttpStatus,HttpStatusGroup,Háttérrendszer|
|HáttérendKérelem Latency|Háttér-kérelem késése|Ezredmásodperc|Átlag|A HTTP/S-proxy által a háttérrendszernek küldött kérelem számításának időpontja mindaddig, amíg a HTTP/S proxy meg nem kapta az utolsó válaszbájtot a háttérrendszertől.|Háttér|
|Összesen Késedelmiség|Teljes késés|Ezredmásodperc|Átlag|A HTTP/S-proxy által az ügyfélkérés fogadásának időpontjától számított idő, amíg az ügyfél vissza nem nyugtázta a HTTP/S-proxy utolsó válaszbájtot|httpstatus,httpstatusgroup,ügyfélrégió,ügyfélország|
|BackendHealthPercentage|Háttér-állapot százalékos aránya|Százalék|Átlag|A HTTP/S-proxyés a háttérrendszerek sikeres állapotminta százalékos aránya|Háttér-, háttérsor-készlet|
|WebApplicationFirewallRequestCount|Webalkalmazás tűzfalának kérelmek száma|Darabszám|Összesen|A webalkalmazás-tűzfal által feldolgozott ügyfélkérelmek száma|Házirendnév,Szabálynév,Művelet|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryVolume (Lekérdezési kötet)|Lekérdezés imtora|Darabszám|Összesen|A privát DNS-zónához megjelenített lekérdezések száma|None|
|Rekordsor száma|Rekordkészlet száma|Darabszám|Maximum|Rekordkészletek száma egy privát DNS-zónában|None|
|RecordSetCapacityKihasználtság|Rekordkészlet kapacitáskihasználtsága|Százalék|Maximum|A rekordkészlet-kapacitás nak a magánDNS-zóna által használt százaléka|None|
|VirtualNetworkLinkCount (VirtualNetworkLinkCount)|Virtuális hálózati kapcsolatok száma|Darabszám|Maximum|Magán DNS-zónához kapcsolt virtuális hálózatok száma|None|
|VirtualNetworkLinkKapacitásKihasználás|Virtuális hálózati kapcsolat kapacitáskihasználása|Százalék|Maximum|A virtuális hálózati kapcsolat kapacitásának a magándns-zóna által használt százaléka|None|
|VirtualNetworkWithRegistrationLinkCount|Virtuális hálózati regisztrációs kapcsolatok száma|Darabszám|Maximum|Magán DNS-zónához kapcsolt virtuális hálózatok száma automatikus regisztráció engedélyezve|None|
|VirtualNetworkWithRegistrationCapacityKihasználás|Virtuális hálózati regisztrációs kapcsolat kapacitáskihasználása|Százalék|Maximum|A virtuális hálózati kapcsolat százaléka a magánDNS-zóna által használt automatikus regisztrációs kapacitással|None|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|regisztráció.all|Regisztrációs műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs művelet száma (a létrehozások frissítik a lekérdezéseket és a törléseket). |None|
|regisztráció.create|Regisztrációs műveletek létrehozása|Darabszám|Összesen|Az összes sikeres regisztrációs létrehozás száma.|None|
|regisztráció.update|Regisztrációs frissítési műveletek|Darabszám|Összesen|Az összes sikeres regisztrációs frissítés száma.|None|
|regisztráció.get|Regisztráció olvasási műveletei|Darabszám|Összesen|Az összes sikeres regisztrációs lekérdezés száma.|None|
|regisztráció.törlés|Regisztráció törlési műveletei|Darabszám|Összesen|Az összes sikeres regisztrációs törlés száma.|None|
|Bejövő|Bejövő üzenetek|Darabszám|Összesen|Az összes sikeres küldési API-hívás száma. |None|
|bejövő.ütemezett|Elküldött ütemezett leküldéses értesítések|Darabszám|Összesen|Ütemezett leküldéses értesítések törölve|None|
|bejövő.ütemezett.mégse|Ütemezett leküldéses értesítések törölve|Darabszám|Összesen|Ütemezett leküldéses értesítések törölve|None|
|ütemezett.függőben|Függőben lévő ütemezett értesítések|Darabszám|Összesen|Függőben lévő ütemezett értesítések|None|
|telepítés.all|Telepítéskezelési műveletek|Darabszám|Összesen|Telepítéskezelési műveletek|None|
|installation.get|Telepítési műveletek beszereznie|Darabszám|Összesen|Telepítési műveletek beszereznie|None|
|telepítés.upsert|Telepítési műveletek létrehozása vagy frissítése|Darabszám|Összesen|Telepítési műveletek létrehozása vagy frissítése|None|
|telepítés.patch|Javítás telepítési műveletek|Darabszám|Összesen|Javítás telepítési műveletek|None|
|installation.delete|Telepítési műveletek törlése|Darabszám|Összesen|Telepítési műveletek törlése|None|
|outgoing.allpns.success|Sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő.allpns.invalidpayload|Hasznos adatra van hiba|Darabszám|Összesen|A nem sikerült leküldések száma, mert a PNS hibás hasznos adathibát adott vissza.|None|
|kimenő.allpns.pnserror|Külső értesítési rendszer hibái|Darabszám|Összesen|A pns-sel való kommunikáció miatt sikertelen leküldések száma (a hitelesítési problémák kizárása).|None|
|kimenő.allpns.channelerror|Csatornahibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna érvénytelen volt, nincs társítva a megfelelő alkalmazásszabályozással vagy lejárt.|None|
|kimenő.allpns.badorexpiredchannel|Hibás vagy lejárt csatornahibák|Darabszám|Összesen|A sikertelen leküldések száma, mert a csatorna/token/registrationId a regisztrációban lejárt vagy érvénytelen.|None|
|kimenő.wns.success|A WNS sikeres értesítései|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő.wns.invalidcredentials|WNS-engedélyezési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A nem sikerült leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva. (A Windows Live nem ismeri fel a hitelesítő adatokat).|None|
|kimenő.wns.badchannel|WNS hibás csatorna hiba|Darabszám|Összesen|A nem sikerült leküldések száma, mert a channelURI a regisztráció nem ismerhető fel (WNS állapot: 404 nem található).|None|
|kimenő.wns.expiredchannel|WNS lejárt csatornahiba|Darabszám|Összesen|A ChannelURI lejárta miatt sikertelen leküldések száma (WNS-állapot: 410 Gone).|None|
|kimenő.wns.throttled|WNS szabályozott értesítések|Darabszám|Összesen|A nem sikerült leküldések száma, mert a WNS szabályozza ezt az alkalmazást (WNS állapot: 406 Nem elfogadható).|None|
|kimenő.wns.tokenproviderunreachable|WNS engedélyezési hibák (nem érhető el)|Darabszám|Összesen|A Windows Live nem érhető el.|None|
|kimenő.wns.invalidtoken|WNS engedélyezési hibák (érvénytelen jogkivonat)|Darabszám|Összesen|A WNS-nek megadott token érvénytelen (WNS állapot: 401 Jogosulatlan).|None|
|kimenő.wns.wrongtoken|WNS engedélyezési hibák (hibás token)|Darabszám|Összesen|A WNS-nek biztosított token érvényes, de egy másik alkalmazáshoz (WNS állapot: 403 Tiltott). Ez akkor fordulhat elő, ha a ChannelURI a regisztrációban egy másik alkalmazáshoz van társítva. Ellenőrizze, hogy az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz van-e társítva, amelynek hitelesítő adatai az értesítési központban találhatók.|None|
|kimenő.wns.invalidnotificationformat|Wns érvénytelen értesítési formátum|Darabszám|Összesen|Az értesítés formátuma érvénytelen (WNS-állapot: 400). Ne feledje, hogy a WNS nem utasítja el az összes érvénytelen rakományt.|None|
|kimenő.wns.invalidnotificationsize|Wns érvénytelen értesítésméret-hiba|Darabszám|Összesen|Az értesítés imázsa túl nagy (WNS állapot: 413).|None|
|kimenő.wns.channelthrottled|WNS-csatorna fojtva|Darabszám|Összesen|Az értesítés megszakadt, mert a ChannelURI a regisztrációban szabályozott (WNS válasz fejléc: X-WNS-NotificationStatus:channelThrottled).|None|
|kimenő.wns.channeldisconnect|WNS-csatorna leválasztva|Darabszám|Összesen|Az értesítés megszakadt, mert a channelURI a regisztrációszabályozott (WNS válasz fejléc: X-WNS-DeviceConnectionStatus: leválasztva).|None|
|kimenő.wns.dropped|WNS eldobott értesítések|Darabszám|Összesen|Az értesítés megszakadt, mert a channelURI a regisztrációban van szabályozva (X-WNS-NotificationStatus: ellett, de nem X-WNS-DeviceConnectionStatus: leválasztva).|None|
|kimenő.wns.pnserror|WNS hibák|Darabszám|Összesen|Az értesítés nem kézbesítve a WNS-sel való kommunikáció hibái miatt.|None|
|kimenő.wns.authenticationerror|WNS hitelesítési hibák|Darabszám|Összesen|Az értesítés nem kézbesítve, mert a Windows Live érvénytelen hitelesítő adatokkal vagy hibás jogkivonattal való kommunikáció során nem történt meg.|None|
|outgoing.apns.success|Az APN-ek sikeres értesítései|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő.apns.invalidcredentials|APNS engedélyezési hibák|Darabszám|Összesen|A nem sikerült leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő.apns.badchannel|APNS hibás csatorna hiba|Darabszám|Összesen|A jogkivonat érvénytelen (APNS-állapotkód: 8) miatt sikertelen leküldések száma.|None|
|kimenő.apns.expiredchannel|APNS lejárt csatornahiba|Darabszám|Összesen|Az APNS-visszajelzési csatorna által érvénytelenített jogkivonatok száma.|None|
|kimenő.apns.invalidnotificationsize|Az APN-ek érvénytelen értesítésméret-hibája|Darabszám|Összesen|A nem sikerült leküldések száma, mert a hasznos adat túl nagy volt (APNS állapotkód: 7).|None|
|kimenő.apns.pnserror|APNS-hibák|Darabszám|Összesen|Az APNS-sel való kommunikáció hibái miatt meghiúsult leküldések száma.|None|
|outgoing.gcm.success|Sikeres GCM-értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|kimenő.gcm.invalidcredentials|GCM-engedélyezési hibák (érvénytelen hitelesítő adatok)|Darabszám|Összesen|A nem sikerült leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő.gcm.badchannel|GCM hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációregisztrációs azonosítónem lett felismerve (GCM eredmény: Érvénytelen regisztráció).|None|
|kimenő.gcm.expiredchannel|GCM lejárt csatornahiba|Darabszám|Összesen|A regisztrációregisztrációi azonosító lejárta miatt sikertelen leküldések száma (GCM-eredmény: NotRegistered).|None|
|kimenő.gcm.throttled|GCM szabályozott értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert a GCM szabályozta az alkalmazást (GCM állapotkód: 501-599 vagy eredmény:Nem érhető el).|None|
|kimenő.gcm.invalidnotificationformat|A GCM érvénytelen értesítési formátuma|Darabszám|Összesen|A nem sikerült leküldések száma, mert a hasznos adat nem volt megfelelően formázva (GCM eredmény: InvalidDataKey vagy InvalidTtl).|None|
|kimenő.gcm.invalidnotificationsize|A GCM érvénytelen értesítésméret-hibája|Darabszám|Összesen|A nem sikerült leküldések száma, mert a hasznos adat túl nagy volt (GCM eredmény: MessageTooBig).|None|
|kimenő.gcm.wrongchannel|GCM rossz csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációregisztrációs azonosító nincs társítva az aktuális alkalmazáshoz (GCM-eredmény: InvalidPackageName).|None|
|kimenő.gcm.pnserror|GCM hibák|Darabszám|Összesen|A GCM-mel való kommunikáció hibái miatt meghiúsult leküldések száma.|None|
|kimenő.gcm.authenticationerror|GCM hitelesítési hibák|Darabszám|Összesen|A nem sikerült leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat a hitelesítő adatok le vannak tiltva, vagy a SenderId nincs megfelelően konfigurálva az alkalmazásban (GCM eredmény: Nem egyezőSenderId).|None|
|outgoing.mpns.success|MPNS sikeres értesítések|Darabszám|Összesen|Az összes sikeres értesítés száma.|None|
|outgoing.mpns.invalidcredentials|MPNS érvénytelen hitelesítő adatok|Darabszám|Összesen|A nem sikerült leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|kimenő.mpns.badchannel|MPNS hibás csatorna hiba|Darabszám|Összesen|A sikertelen leküldések száma, mert a regisztrációban a ChannelURI nem ismerhető fel (MPNS-állapot: 404 nem található).|None|
|kimenő.mpns.throttled|MPNS szabályozott értesítések|Darabszám|Összesen|A sikertelen leküldések száma, mert az MPNS szabályozza ezt az alkalmazást (WNS MPNS: 406 Nem elfogadható).|None|
|outgoing.mpns.invalidnotificationformat|MpN-ek érvénytelen értesítési formátuma|Darabszám|Összesen|A sikertelen leküldések száma, mert az értesítés hasznos terhelése túl nagy volt.|None|
|outgoing.mpns.channeldisconnect|MPNS-csatorna leválasztva|Darabszám|Összesen|A sikertelen leküldések száma, mert a ChannelURI a regisztrációban megszakadt (MPNS-állapot: 412 nem található).|None|
|outgoing.mpns.dropped|MPNS eldobott értesítések|Darabszám|Összesen|Az MPNS által eldobott leküldések száma (MPNS-válaszfejléc: X-NotificationStatus: QueueFull vagy Letiltott).|None|
|outgoing.mpns.pnserror|MPNS-hibák|Darabszám|Összesen|Az MPNS-sel való kommunikáció hibái miatt meghiúsult leküldések száma.|None|
|outgoing.mpns.authenticationerror|MPNS-hitelesítési hibák|Darabszám|Összesen|A nem sikerült leküldések száma, mert a PNS nem fogadta el a megadott hitelesítő adatokat, vagy a hitelesítő adatok le vannak tiltva.|None|
|notificationhub.pushes|Minden kimenő értesítés|Darabszám|Összesen|Az értesítési központ összes kimenő értesítése|None|
|bejövő.all.requests|Minden bejövő kérés|Darabszám|Összesen|Értesítési központ bejövő kérelmeinek összesen|None|
|bejövő.all.failedrequests|Minden sikertelen bejövő kérés|Darabszám|Összesen|Értesítési központ bejövő sikertelen kérelmeinek összesen|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/munkaterületek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Average_% Ingyenes Inodes|% szabad inodák|Darabszám|Átlag|Average_% Ingyenes Inodes|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_% szabad hely|Szabad terület %-os|Darabszám|Átlag|Average_% szabad hely|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_% Használt Inodes|Használt inodák %-a|Darabszám|Átlag|Average_% Használt Inodes|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%- os használt terület|% használt terület|Darabszám|Átlag|Average_%- os használt terület|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Disk olvasási bájt/mp|Lemez olvasási bájtjai/mp|Darabszám|Átlag|Average_Disk olvasási bájt/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Disk olvasás/mp|Lemezolvasás/mp|Darabszám|Átlag|Average_Disk olvasás/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Disk átvitelek/mp|Lemezátvitel/mp|Darabszám|Átlag|Average_Disk átvitelek/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Disk írási bájt/mp|Lemezírási bájtok/mp|Darabszám|Átlag|Average_Disk írási bájt/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Disk írások/mp|Lemezírás/mp|Darabszám|Átlag|Average_Disk írások/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Free megabájt|Ingyenes Megabájt|Darabszám|Átlag|Average_Free megabájt|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Logical lemezbájt/mp|Logikai lemezbájt/mp|Darabszám|Átlag|Average_Logical lemezbájt/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%-os rendelkezésre álló memória|Rendelkezésre álló memória %-a|Darabszám|Átlag|Average_%-os rendelkezésre álló memória|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%- os rendelkezésre álló swap-terület|A rendelkezésre álló csereterület %-a|Darabszám|Átlag|Average_%- os rendelkezésre álló swap-terület|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%-os használt memória|Felhasznált memória %-a|Darabszám|Átlag|Average_%-os használt memória|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_% Használt swap terület|% Használt csereterület|Darabszám|Átlag|Average_% Használt swap terület|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Available MByte memória|Elérhető MByte memória|Darabszám|Átlag|Average_Available MByte memória|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Available MByte swap|Elérhető MByteswap|Darabszám|Átlag|Average_Available MByte swap|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Page olvasása/mp|Oldalolvasások/mp|Darabszám|Átlag|Average_Page olvasása/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Page írások/mp|Lapírások/mp|Darabszám|Átlag|Average_Page írások/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Pages/mp|Oldalak/mp|Darabszám|Átlag|Average_Pages/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Used MBytes swap tér|Használt MBytes swap tér|Darabszám|Átlag|Average_Used MBytes swap tér|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Used memória MBytes|Használt memória MBytes|Darabszám|Átlag|Average_Used memória MBytes|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Total továbbított bájtok|Átküldött bájtok összesen|Darabszám|Átlag|Average_Total továbbított bájtok|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Total fogadott bájtok|Fogadott bájtok összesen|Darabszám|Átlag|Average_Total fogadott bájtok|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Total bájt|Összes bájt|Darabszám|Átlag|Average_Total bájt|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Total továbbított csomagok|Átküldött csomagok összesen|Darabszám|Átlag|Average_Total továbbított csomagok|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Total fogadott csomagok|Fogadott csomagok összesen|Darabszám|Átlag|Average_Total fogadott csomagok|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Total Rx hibák|Rx hibák összesen|Darabszám|Átlag|Average_Total Rx hibák|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Total Tx hibák|Összes Tx hiba|Darabszám|Átlag|Average_Total Tx hibák|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Total ütközések|Ütközések összesen|Darabszám|Átlag|Average_Total ütközések|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Átlagos lemezmásodperc/olvasás|Darabszám|Átlag|Average_Avg. olvasási idő (mp/olvasás) teljesítményszámlálóhoz.|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Avg. Lemez másodperc/átvitel|Átlagos lemezmásodperc/átvitel|Darabszám|Átlag|Average_Avg. Lemez másodperc/átvitel|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Átlagos lemez másodperc/írás|Darabszám|Átlag|Average_Avg. írási idő (mp/írás) teljesítményszámlálóhoz.|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Physical lemezbájt/mp|Fizikai lemezbájt/mp|Darabszám|Átlag|Average_Physical lemezbájt/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Pct kiváltságos idő|Pct privilegizált idő|Darabszám|Átlag|Average_Pct kiváltságos idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Pct felhasználói idő|Pct felhasználói idő|Darabszám|Átlag|Average_Pct felhasználói idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Used memória kbyte|Használt memóriakbyte-ok|Darabszám|Átlag|Average_Used memória kbyte|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Virtual megosztott memória|Virtuális megosztott memória|Darabszám|Átlag|Average_Virtual megosztott memória|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%-os DPC idő|DPC-idő %-os ideje|Darabszám|Átlag|Average_%-os DPC idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%- os tétlen idő|Üresjáratban eltöltött időhányad (%)|Darabszám|Átlag|Average_%- os tétlen idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%-os megszakítási idő|Megszakítási idő %-os ideje|Darabszám|Átlag|Average_%-os megszakítási idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%-os IO várakozási idő|%-os IO várakozási idő|Darabszám|Átlag|Average_%-os IO várakozási idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_% Szép idő|% Szép idő|Darabszám|Átlag|Average_% Szép idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%-os jogosultsági idő|Jogosultsági idő %-os százaléka|Darabszám|Átlag|Average_%-os jogosultsági idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%-os processzoridő|A processzor kihasználtsága (%)|Darabszám|Átlag|Average_%-os processzoridő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%-os felhasználói idő|%felhasználói idő|Darabszám|Átlag|Average_%-os felhasználói idő|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Free fizikai memória|Szabad fizikai memória|Darabszám|Átlag|Average_Free fizikai memória|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Free tér közterülete a lapozófájlokban|Szabad terület a lapozófájlokban|Darabszám|Átlag|Average_Free tér közterülete a lapozófájlokban|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Free virtuális memória|Ingyenes virtuális memória|Darabszám|Átlag|Average_Free virtuális memória|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Processes|Folyamatok|Darabszám|Átlag|Average_Processes|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Size lapozófájlokban tárolva|Lapozófájlokban tárolt méret|Darabszám|Átlag|Average_Size lapozófájlokban tárolva|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Uptime|Uptime|Darabszám|Átlag|Average_Uptime|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Users|Felhasználók|Darabszám|Átlag|Average_Users|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Current lemezvárólista hossza|Az aktuális lemezvárólista hossza|Darabszám|Átlag|Average_Current lemezvárólista hossza|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Available MByte|Elérhető MBytes|Darabszám|Átlag|Average_Available MByte|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_%, használatban lévő lekötött bájtok|Használatban lévő lekötött bájtok %-a|Darabszám|Átlag|Average_%, használatban lévő lekötött bájtok|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Bytes fogadott/mp|Másodpercenként fogadott bájtok száma|Darabszám|Átlag|Average_Bytes fogadott/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Bytes elküldve/mp|Küldött bájtok/mp|Darabszám|Átlag|Average_Bytes elküldve/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Bytes összesen/mp|Összes bájt/mp|Darabszám|Átlag|Average_Bytes összesen/mp|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Average_Processor várólista hossza|Processzorvárólista hossza|Darabszám|Átlag|Average_Processor várólista hossza|Számítógép,Objektumnév,Példánynév,CounterPath,Forrásrendszer|
|Szívverés|Szívverés|Darabszám|Összesen|Szívverés|Számítógép,OSType,Verzió,Forrásszámítógépazonosító|
|Frissítés|Frissítés|Darabszám|Átlag|Frissítés|Számítógép,Termék,Besorolás,UpdateState,Választható,Jóváhagyott|
|Esemény|Esemény|Darabszám|Átlag|Esemény|Forrás,Eseménynapló,Számítógép,Eseménykategória,Eseményszint,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Társviszony-létesítési/társviszony-létesítési szolgáltatások

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ElőtagLátencia|Előtag késése|Ezredmásodperc|Átlag|Az előtag medián késése|Előtagneve|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Társviszony-létesítési/társviszony-létesítési

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Munkamenet-elérhetőségV4|Munkamenet rendelkezésre állása V4|Százalék|Átlag|A V4-munkamenet elérhetősége|ConnectionId azonosító|
|Munkamenet-elérhetőségV6|Munkamenet rendelkezésre állása V6|Százalék|Átlag|A V6-os munkamenet elérhetősége|ConnectionId azonosító|
|IngressTrafficRate (BeressTrafficRate)|Be- és forgalom mértéke|BitsPerSz|Átlag|Bejövő forgalom sebessége bit/másodpercben|ConnectionId azonosító|
|EgressTrafficRate|Kimenő forgalom aránya|BitsPerSz|Átlag|Kimenő forgalom sebessége bit/másodpercben|ConnectionId azonosító|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedikált/kapacitások

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|QueryDuration (Lekérdezésidőtartama)|Lekérdezés időtartama|Ezredmásodperc|Átlag|A DAX-lekérdezés időtartama az utolsó intervallumban|Nincsenek dimenziók|
|QueryPoolJobQueueLength|Szálak: Lekérdezéskészlet-feladat várólista hossza|Darabszám|Átlag|A lekérdezési szálkészlet várólistájában lévő feladatok száma.|Nincsenek dimenziók|
|qpu_high_utilization_metric|QPU magas kihasználtság|Darabszám|Összesen|QPU nagy kihasználtság az utolsó pillanatban, 1 A magas QPU kihasználtság, Egyébként 0|Nincsenek dimenziók|
|memory_metric|Memory (Memória)|Bájt|Átlag|Memory. Tartomány 0-3 GB A1, 0-5 GB A2, 0-10 GB A3, 0-25 GB A4, 0-50 GB A5 és 0-100 GB A6 esetén|Nincsenek dimenziók|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Átlagos memória verés.|Nincsenek dimenziók|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/fiókok

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Eszköz felosztása osztályozás szerint|Eszköz felosztása osztályozás szerint|Darabszám|Összesen|A meghatározott besorolással rendelkező eszközök számát jelzi, azaz az adott címkével vannak besorolva.|Besorolás,Forrás,Erőforrásazonosító|
|Eszközfelosztástárolótípusa|Eszköz elosztása tárolási típus szerint|Darabszám|Összesen|Egy adott tárolási típussal rendelkező eszközök számát jelzi.|StorageType,ResourceId|
|CatalogActiveUsers|Napi aktív felhasználók|Darabszám|Összesen|Aktív felhasználók száma naponta|ResourceId|
|Katalógushasználata|Használat elosztása művelet szerint|Darabszám|Összesen|Adja meg, hogy a felhasználó hány műveletet végez a katalógusban, azaz az Access, a Keresés, a Szószedet.|Művelet,Erőforrásazonosító|
|NumberOfAssetsWithClassifications|Legalább egy besorolással rendelkező eszközök száma|Darabszám|Átlag|A legalább egy címkebesorolással rendelkező eszközök számát jelzi.|ResourceId|
|VizsgálatTörölve|Vizsgálat megszakítva|Darabszám|Összesen|A megszakított vizsgálatok számát jelzi.|ResourceId|
|Vizsgálat befejezve|Vizsgálat befejezve|Darabszám|Összesen|A sikeresen végrehajtott vizsgálatok számát jelzi.|ResourceId|
|Sikertelen vizsgálat|A vizsgálat nem sikerült|Darabszám|Összesen|A sikertelen beolvasások számát jelzi.|ResourceId|
|ScanTimeTaken|A beszkavaltöltött idő|Másodperc|Összesen|A teljes bevizsgálati időt jelzi másodpercben.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/névterek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ListenerConnections-sikeres|ListenerConnections-sikeres|Darabszám|Összesen|Sikeres figyelőkapcsolatok a Microsoft.Relay.|Entitásnév,Műveleteredmény|
|Figyelőkapcsolatok-Ügyfélhiba|Figyelőkapcsolatok-Ügyfélhiba|Darabszám|Összesen|ClientError a Microsoft.Relay figyelőkapcsolatain.|Entitásnév,Műveleteredmény|
|Figyelőkapcsolatok-ServerHiba|Figyelőkapcsolatok-ServerHiba|Darabszám|Összesen|ServerError a Microsoft.Relay figyelőkapcsolatain.|Entitásnév,Műveleteredmény|
|SenderConnections-Sikeres|SenderConnections-Sikeres|Darabszám|Összesen|Sikeres SenderConnections for Microsoft.Relay.|Entitásnév,Műveleteredmény|
|SenderConnections-ClientError (Küldőkapcsolatok-ügyfélhiba)|SenderConnections-ClientError (Küldőkapcsolatok-ügyfélhiba)|Darabszám|Összesen|Ügyfélhiba a Microsoft.Relay senderconnections szolgáltatásán.|Entitásnév,Műveleteredmény|
|SenderConnections-ServerError|SenderConnections-ServerError|Darabszám|Összesen|ServerError a SenderConnections for Microsoft.Relay oldalon.|Entitásnév,Műveleteredmény|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Darabszám|Összesen|A Microsoft.Relay összes figyelőkapcsolata.|Entitásnév|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Darabszám|Összesen|A Microsoft.Relay összes SenderConnections-kérelme.|Entitásnév|
|ActiveConnections|ActiveConnections|Darabszám|Összesen|A Microsoft.Relay összes ActiveConnections szolgáltatása.|Entitásnév|
|ActiveListeners|ActiveListeners|Darabszám|Összesen|ActiveListeners teljes microsoft.relay.|Entitásnév|
|Átvitt bájtok|Átvitt bájtok|Darabszám|Összesen|A Microsoft.Relay összes átvitt bájtja.|Entitásnév|
|Figyelőleválasztás|Figyelőleválasztás|Darabszám|Összesen|A Microsoft.Relay összes figyelőleválasztása.|Entitásnév|
|Feladóleválasztás|Feladóleválasztás|Darabszám|Összesen|A Microsoft.Relay összes SenderDisconnect-je.|Entitásnév|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|KeresésLatency|Késés keresése|Másodperc|Átlag|A keresési szolgáltatás átlagos keresési késleltetése|None|
|SearchQueriesPerSecond|Keresési lekérdezések másodpercenként|CountPerSzekszekundum|Átlag|Keresési lekérdezések másodpercenként a keresési szolgáltatás|None|
|Szabályozott searchqueriesszázalékos|Szabályozott keresési lekérdezések százalékos aránya|Százalék|Átlag|A keresési szolgáltatáshoz szabályozott keresési lekérdezések százalékos aránya|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/névterek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Sikeres kérések|Sikeres kérelmek|Darabszám|Összesen|Névtérre vonatkozó sikeres kérelmek összesen|Entitásnév,Műveleteredmény|
|Kiszolgálóhibák|Kiszolgálói hibák.|Darabszám|Összesen|A Microsoft.ServiceBus kiszolgálóhibái.|Entitásnév,Műveleteredmény|
|Felhasználói hibák|Felhasználói hibák.|Darabszám|Összesen|Felhasználói hibák a Microsoft.ServiceBus szolgáltatásban.|Entitásnév,Műveleteredmény|
|ThrottledRequests|Szabályozott kérelmek.|Darabszám|Összesen|Szabályozással kapcsolatos kérelmek a Microsoft.ServiceBus számára.|Entitásnév,Műveleteredmény|
|Bejövő kérések|Bejövő kérések|Darabszám|Összesen|Bejövő kérelmek a Microsoft.ServiceBus számára.|Entitásnév|
|Bejövő üzenetek|Bejövő üzenetek|Darabszám|Összesen|Bejövő üzenetek a Microsoft.ServiceBus programhoz.|Entitásnév|
|Kimenő üzenetek|Kimenő üzenetek|Darabszám|Összesen|Kimenő üzenetek a Microsoft.ServiceBus programhoz.|Entitásnév|
|ActiveConnections|ActiveConnections|Darabszám|Összesen|A Microsoft.ServiceBus összes aktív kapcsolata.|None|
|Kapcsolatok megnyitva|Kapcsolatok megnyitva.|Darabszám|Átlag|A Microsoft.ServiceBus számára megnyitott kapcsolatok.|Entitásnév|
|Kapcsolatok zárva|Kapcsolatok zárva.|Darabszám|Átlag|A Microsoft.ServiceBus számára megszakadt kapcsolatok.|Entitásnév|
|Méret|Méret|Bájt|Átlag|Várólista/témakör mérete bájtban.|Entitásnév|
|Üzenetek|A várólistában/témakörben lévő üzenetek száma.|Darabszám|Átlag|A várólistában/témakörben lévő üzenetek száma.|Entitásnév|
|ActiveMessages|A várólistában/témakörben lévő aktív üzenetek száma.|Darabszám|Átlag|A várólistában/témakörben lévő aktív üzenetek száma.|Entitásnév|
|DeadletteredÜzenetek|Kézbesítetlen levelek száma egy várólistában/témakörben.|Darabszám|Átlag|Kézbesítetlen levelek száma egy várólistában/témakörben.|Entitásnév|
|Ütemezett üzenetek|A várólistában/témakörben lévő ütemezett üzenetek száma.|Darabszám|Átlag|A várólistában/témakörben lévő ütemezett üzenetek száma.|Entitásnév|
|NamespaceCpuUsage|CPU|Százalék|Maximum|Szolgáltatásbusz prémium névtér CPU-használati metrika.|Replika|
|NamespaceMemoryUsage|Memóriahasználat|Százalék|Maximum|Szolgáltatásbusz prémium névtér memória használati metrikája.|Replika|
|CPUXN-ek|CPU (elavult)|Százalék|Maximum|Szolgáltatásbusz prémium névtér CPU-használati metrika. Ez a mérőszám depricated. Használja inkább a CPU-metrikát (NamespaceCpuUsage).|Replika|
|WSXN-ek|Memóriahasználat (elavult)|Százalék|Maximum|Szolgáltatásbusz prémium névtér memória használati metrikája. Ez a metrika elavult. Használja inkább a Memóriahasználat (NamespaceMemoryUsage) metrikát.|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/alkalmazások

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Felosztott cpu|Felosztott cpu|Darabszám|Átlag|A tárolóhoz rendelt processzor milli magban|Alkalmazásnév,Szolgáltatásnév,CodePackageName,ServiceReplicaName|
|Lefoglalt memória|Lefoglalt memória|Bájt|Átlag|A tárolószámára lefoglalt memória MB-ban|Alkalmazásnév,Szolgáltatásnév,CodePackageName,ServiceReplicaName|
|ActualCpu|ActualCpu|Darabszám|Átlag|Tényleges CPU-használat milli magottartalmazóban|Alkalmazásnév,Szolgáltatásnév,CodePackageName,ServiceReplicaName|
|ActualMemory (Tényleges memória)|ActualMemory (Tényleges memória)|Bájt|Átlag|Tényleges memóriahasználat MB-ban|Alkalmazásnév,Szolgáltatásnév,CodePackageName,ServiceReplicaName|
|Cpukihasználtság|Cpukihasználtság|Százalék|Átlag|A tároló processzorának kihasználtsága a AllocatedCpu százalékában|Alkalmazásnév,Szolgáltatásnév,CodePackageName,ServiceReplicaName|
|Memóriakihasználtság|Memóriakihasználtság|Százalék|Átlag|A tároló processzorának kihasználtsága a AllocatedCpu százalékában|Alkalmazásnév,Szolgáltatásnév,CodePackageName,ServiceReplicaName|
|ApplicationStatus alkalmazásállapota|ApplicationStatus alkalmazásállapota|Darabszám|Átlag|A Service Fabric Mesh alkalmazás állapota|Alkalmazásnév,Állapot|
|ServiceStatus (Szolgáltatásállapota)|ServiceStatus (Szolgáltatásállapota)|Darabszám|Átlag|Szolgáltatás állapotának állapota a Service Fabric Mesh alkalmazásban|Alkalmazásnév,Állapot,Szolgáltatásnév|
|SzolgáltatásReplicaStatus|SzolgáltatásReplicaStatus|Darabszám|Átlag|Szolgáltatáskópia állapota a Service Fabric Mesh alkalmazásban|Alkalmazásnév,Állapot,Szolgáltatásnév,ServiceReplicaName|
|ContainerStatus (Konténerállapota)|ContainerStatus (Konténerállapota)|Darabszám|Átlag|A tároló állapota a Service Fabric Mesh alkalmazásban|Alkalmazásnév,Szolgáltatásnév,CodePackageName,ServiceReplicaName,Állapot|
|RestartCount (Újraindítási számláló)|RestartCount (Újraindítási számláló)|Darabszám|Átlag|Tároló újrakezdési száma a Service Fabric Mesh alkalmazásban|Alkalmazásnév,Állapot,Szolgáltatásnév,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kapcsolatszáma|Kapcsolatok száma|Darabszám|Maximum|A felhasználói kapcsolat mennyisége.|Végpont|
|MessageCount (Üzenetszám)|Üzenetek száma|Darabszám|Összesen|Az üzenetek teljes mennyisége.|None|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő szolgáltatásforgalom|None|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A szolgáltatás kimenő forgalma|None|
|Felhasználói hibák|Felhasználói hibák|Százalék|Maximum|A felhasználói hibák százalékos aránya|None|
|Rendszerhibák|Rendszerhibák|Százalék|Maximum|A rendszerhibák százalékos aránya|None|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|None|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|None|
|log_write_percent|Log IO százalék|Százalék|Átlag|Log IO százalék. Adatraktárakra nem vonatkozik.|None|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. DTU-alapú adatbázisokra vonatkozik.|None|
|storage|Használt adatterület|Bájt|Maximum|Használt adatterület. Adatraktárakra nem vonatkozik.|None|
|connection_successful|Sikeres kapcsolatok|Darabszám|Összesen|Sikeres kapcsolatok|None|
|connection_failed|Sikertelen kapcsolatok|Darabszám|Összesen|Sikertelen kapcsolatok|None|
|blocked_by_firewall|Tűzfal blokkolja|Darabszám|Összesen|Tűzfal blokkolja|None|
|Holtpont|Holtpontok|Darabszám|Összesen|Patthelyzet. Adatraktárakra nem vonatkozik.|None|
|storage_percent|Felhasznált adatterület százaléka|Százalék|Maximum|A felhasznált adatterület százaléka. Adatraktárakra vagy nagy kapacitású adatbázisokra nem alkalmazható.|None|
|xtp_storage_percent|Memórián belüli OLTP-tároló százaléka|Százalék|Átlag|Memórián belüli OLTP-tároló százaléka. Adatraktárakra nem vonatkozik.|None|
|workers_percent|Munkavállalók százalékos aránya|Százalék|Átlag|A munkavállalók százalékos aránya. Adatraktárakra nem vonatkozik.|None|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százaléka. Adatraktárakra nem vonatkozik.|None|
|dtu_limit|DTU-korlát|Darabszám|Átlag|DTU-korlát. DTU-alapú adatbázisokra vonatkozik.|None|
|dtu_used|Használt DTU|Darabszám|Átlag|DTU használt. DTU-alapú adatbázisokra vonatkozik.|None|
|cpu_limit|CPU-korlát|Darabszám|Átlag|CPU-korlát. Virtuálismag-alapú adatbázisokra vonatkozik.|None|
|cpu_used|HASZNÁLT PROCESSZOR|Darabszám|Átlag|CPU használt. Virtuálismag-alapú adatbázisokra vonatkozik.|None|
|dwu_limit|DWU-korlát|Darabszám|Maximum|DWU limit. Csak adatraktárakra vonatkozik.|None|
|dwu_consumption_percent|DWU százalék|Százalék|Maximum|DWU százalék. Csak adatraktárakra vonatkozik.|None|
|dwu_used|Használt DWU|Darabszám|Maximum|Használt DWU. Csak adatraktárakra vonatkozik.|None|
|cache_hit_percent|Gyorsítótár találati százaléka|Százalék|Maximum|Gyorsítótár találati százaléka. Csak adatraktárakra vonatkozik.|None|
|cache_used_percent|Gyorsítótár által használt százalék|Százalék|Maximum|Gyorsítótár használt százaléka. Csak adatraktárakra vonatkozik.|None|
|sqlserver_process_core_percent<sup>1.</sup> |SQL Server folyamat alapszázaléka|Százalék|Maximum|Az SQL Server folyamat processzorhasználati százaléka az operációs rendszer szerint mérve.|None|
|<sup>1.</sup> sqlserver_process_memory_percent |SQL Server folyamatmemória százaléka|Százalék|Maximum|Az SQL Server folyamat memóriahasználati százaléka az operációs rendszer szerint mérve.|None|
|<sup>tempdb_data_size 2.</sup> |Tempdb-adatfájl mérete kilobájt|Darabszám|Maximum|Tempdb adatfájl mérete kilobájt.|None|
|<sup>tempdb_log_size 2.</sup> |Tempdb naplófájl mérete Kilobájt|Darabszám|Maximum|Tempdb naplófájl mérete kilobájt.|None|
|<sup>tempdb_log_used_percent 2.</sup> |Használt Tempdb százaléknapló|Százalék|Maximum|Tempdb százaléknapló használatban.|None|
|local_tempdb_usage_percent|Helyi tempdb százalék|Százalék|Átlag|Helyi tempdb százalék. Csak adatraktárakra vonatkozik.|None|
|app_cpu_billed|Alkalmazás PROCESSZORa számlázva|Darabszám|Összesen|Az alkalmazás cpu-ja számlázva. Kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|app_cpu_percent|Alkalmazás processzorának százaléka|Százalék|Átlag|Alkalmazás processzorának százaléka. Kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|app_memory_percent|Alkalmazásmemória százaléka|Százalék|Átlag|Alkalmazásmemória százaléka. Kiszolgáló nélküli adatbázisokra vonatkozik.|None|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adattárolás. Adatraktárakra nem vonatkozik.|None|
|memory_usage_percent|Memória százaléka|Százalék|Maximum|Memória százalék. Csak adatraktárakra vonatkozik.|None|
|dw_backup_size_gb|Adattároló mérete|Darabszám|Összesen|Az adattárolási méret az adatok méretéből és a tranzakciós naplóból áll. A mérőszám beleszámít a számla "Tárolás" részébe. Csak adatraktárakra vonatkozik.|None|
|dw_snapshot_size_gb|Pillanatkép-tároló mérete|Darabszám|Összesen|Pillanatkép-tároló mérete a pillanatképek által rögzített növekményes módosítások mérete a felhasználó által definiált és automatikus visszaállítási pontok létrehozásához. A mérőszám beleszámít a számla "Tárolás" részébe. Csak adatraktárakra vonatkozik.|None|
|dw_geosnapshot_size_gb|Vész-helyreállítási tároló mérete|Darabszám|Összesen|Vész-helyreállítási tárolási mérete tükröződik a "vész-helyreállítási tároló" a számlán. Csak adatraktárakra vonatkozik.|None|
|wlg_allocation_relative_to_system_percent|Számítási feladatok csoportjának lefoglalása rendszerszázalék szerint|Százalék|Maximum|Az erőforrások hozzárendelt százaléka a teljes rendszerhez képest munkaterhelési csoportonként. Csak adatraktárakra vonatkozik.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Számítási feladatok csoport lefoglalása erőforrásszázalék szerint|Százalék|Maximum|Az erőforrások hozzárendelt százaléka a megadott terhelési erőforrásokhoz képest munkaterhelési csoportonként. Csak adatraktárakra vonatkozik.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Számítási feladatok csoport aktív lekérdezései|Darabszám|Összesen|Aktív lekérdezések a munkaterheléscsoporton belül. Csak adatraktárakra vonatkozik.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Munkaterhelés-csoport várólistán lévő lekérdezései|Darabszám|Összesen|Várólistára helyezett lekérdezések a munkaterhelési csoporton belül. Csak adatraktárakra vonatkozik.|WorkloadGroupName,IsUserDefined|
|active_queries|Aktív lekérdezések|Darabszám|Összesen|Aktív lekérdezések az összes számítási feladat csoportban. Csak adatraktárakra vonatkozik.|None|
|queued_queries|Várakozással várakozó lekérdezések|Darabszám|Összesen|Várólistára helyezett lekérdezések az összes munkaterhelési csoportban. Csak adatraktárakra vonatkozik.|None|
|wlg_active_queries_timeouts|Számítási feladatok csoport lekérdezési időmegválaszolásai|Darabszám|Összesen|A számítási feladatok csoport számára időkimenőt tartalmazó lekérdezések. Csak adatraktárakra vonatkozik.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Effektív min erőforrás százalék|Százalék|Maximum|A számítási feladatok csoportjához lefoglalt és elkülönített erőforrások minimális százaléka, figyelembe véve a minimális szolgáltatási szintet. Csak adatraktárakra vonatkozik.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Effektív erőforrás-korlát százaléka|Százalék|Maximum|A számítási feladatok csoport számára engedélyezett erőforrások százalékos arányának szigorú korlátozása, figyelembe véve a más munkaterhelés-csoportok számára lefoglalt effektív erőforrás-százalékot. Csak adatraktárakra vonatkozik.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Teljes biztonsági mentési tárolóméret|Bájt|Maximum|Összesített teljes biztonsági mentési tárterület mérete. Virtuálismag-alapú adatbázisokra vonatkozik. Nem alkalmazható a nagy kapacitású adatbázisok.|None|
|diff_backup_size_bytes|Különbözeti biztonsági mentési tárolóméret|Bájt|Maximum|Összesített különbözeti biztonsági mentési tároló mérete. Virtuálismag-alapú adatbázisokra vonatkozik. Nem alkalmazható a nagy kapacitású adatbázisok.|None|
|log_backup_size_bytes|Biztonsági másolat tárolási méretének naplózása|Bájt|Maximum|Összesített naplóbiztonsági mentési tárolómérete. Virtuálismag-alapú és nagy kapacitású adatbázisok vonatkozik.|None|
|snapshot_backup_size_bytes|Pillanatkép biztonsági mentési tárolómérete|Bájt|Maximum|Összesített pillanatkép biztonsági mentési tároló mérete. Nagykapacitású adatbázisokra vonatkozik.|None|
|base_blob_size_bytes|Alapblob-tároló mérete|Bájt|Maximum|Alapblob-tároló mérete. Nagykapacitású adatbázisokra vonatkozik.|None|

<sup>1</sup> Ez a metrika a virtuális mag 2 virtuálismaggal és magasabb magokkal, illetve 200 DTU-val és magasabb magokkal rendelkező adatbázisokhoz érhető el. 

<sup>2</sup> Ez a metrika a virtuális mag 2 virtuálismaggal és magasabb magokkal, illetve 200 DTU-val és magasabb magokkal rendelkező adatbázisokhoz érhető el. Ez a metrika jelenleg nem érhető el a nagy kapacitású adatbázisok vagy adatraktárak.

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|None|
|database_cpu_percent|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|DatabaseResourceId azonosító|
|physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|None|
|database_physical_data_read_percent|Adat IO kihasználtsága (%)|Százalék|Átlag|Adat IO kihasználtsága (%)|DatabaseResourceId azonosító|
|log_write_percent|Log IO százalék|Százalék|Átlag|Log IO százalék|None|
|database_log_write_percent|Log IO százalék|Százalék|Átlag|Log IO százalék|DatabaseResourceId azonosító|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU százalék. A DTU-alapú rugalmas készletekre vonatkozik.|None|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId azonosító|
|storage_percent|Felhasznált adatterület százaléka|Százalék|Átlag|Felhasznált adatterület százaléka|None|
|workers_percent|Munkavállalók százalékos aránya|Százalék|Átlag|Munkavállalók százalékos aránya|None|
|database_workers_percent|Munkavállalók százalékos aránya|Százalék|Átlag|Munkavállalók százalékos aránya|DatabaseResourceId azonosító|
|sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|None|
|database_sessions_percent|Munkamenetek százalékos aránya|Százalék|Átlag|Munkamenetek százalékos aránya|DatabaseResourceId azonosító|
|eDTU_limit|eDTU korlát|Darabszám|Átlag|eDTU korlátot. A DTU-alapú rugalmas készletekre vonatkozik.|None|
|storage_limit|Adatok maximális mérete|Bájt|Átlag|Adatok maximális mérete|None|
|eDTU_used|eDTU használt|Darabszám|Átlag|eDTU-t használ. A DTU-alapú rugalmas készletekre vonatkozik.|None|
|database_eDTU_used|eDTU használt|Darabszám|Átlag|eDTU használt|DatabaseResourceId azonosító|
|storage_used|Használt adatterület|Bájt|Átlag|Használt adatterület|None|
|database_storage_used|Használt adatterület|Bájt|Átlag|Használt adatterület|DatabaseResourceId azonosító|
|xtp_storage_percent|Memórián belüli OLTP-tároló százaléka|Százalék|Átlag|Memórián belüli OLTP-tároló százaléka|None|
|cpu_limit|CPU-korlát|Darabszám|Átlag|CPU-korlát. Virtuálismag-alapú rugalmas készletek vonatkozik.|None|
|database_cpu_limit|CPU-korlát|Darabszám|Átlag|CPU-korlát|DatabaseResourceId azonosító|
|cpu_used|HASZNÁLT PROCESSZOR|Darabszám|Átlag|CPU használt. Virtuálismag-alapú rugalmas készletek vonatkozik.|None|
|database_cpu_used|HASZNÁLT PROCESSZOR|Darabszám|Átlag|HASZNÁLT PROCESSZOR|DatabaseResourceId azonosító|
|sqlserver_process_core_percent<sup>1.</sup>|SQL Server folyamat alapszázaléka|Százalék|Maximum|Az SQL Server folyamat processzorhasználati százaléka az operációs rendszer szerint mérve. Rugalmas készletekre vonatkozik. |None|
|<sup>1.</sup> sqlserver_process_memory_percent|SQL Server folyamatmemória százaléka|Százalék|Maximum|Az SQL Server folyamat memóriahasználati százaléka az operációs rendszer szerint mérve. Rugalmas készletekre vonatkozik. |None|
|<sup>tempdb_data_size 2.</sup>|Tempdb-adatfájl mérete kilobájt|Darabszám|Maximum|Tempdb adatfájl mérete kilobájt.|None|
|<sup>tempdb_log_size 2.</sup>|Tempdb naplófájl mérete Kilobájt|Darabszám|Maximum|Tempdb naplófájl mérete kilobájt. |None|
|<sup>tempdb_log_used_percent 2.</sup>|Használt Tempdb százaléknapló|Százalék|Maximum|Tempdb százaléknapló használatban.|None|
|allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|None|
|database_allocated_data_storage|Lefoglalt adatterület|Bájt|Átlag|Lefoglalt adatterület|DatabaseResourceId azonosító|
|allocated_data_storage_percent|Kiosztott adatterület százalék|Százalék|Maximum|Kiosztott adatterület százalék|None|

<sup>1</sup> Ez a metrika a virtuális mag 2 virtuálismaggal és magasabb magokkal, illetve 200 DTU-val és magasabb magokkal rendelkező adatbázisokhoz érhető el. 

<sup>2</sup> Ez a metrika a virtuális mag 2 virtuálismaggal és magasabb magokkal, illetve 200 DTU-val és magasabb magokkal rendelkező adatbázisokhoz érhető el. Ez a metrika jelenleg nem érhető el a nagy kapacitású adatbázisok.


## <a name="microsoftsqlservers"></a>Microsoft.Sql/kiszolgálók

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-kihasználtság (%)|Százalék|Átlag|DTU-kihasználtság (%)|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Használt adatterület|Bájt|Átlag|Használt adatterület|ElasticPoolResourceId|
|database_storage_used|Használt adatterület|Bájt|Átlag|Használt adatterület|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Használt DTU|Darabszám|Átlag|Használt DTU|DatabaseResourceId azonosító|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|virtual_core_count|Virtuális magok száma|Darabszám|Átlag|Virtuális magok száma|None|
|avg_cpu_percent|Átlagos CPU-százalék|Százalék|Átlag|Átlagos CPU-százalék|None|
|reserved_storage_mb|Fenntartott tárhely|Darabszám|Átlag|Fenntartott tárhely|None|
|storage_space_used_mb|Használt tárhely|Darabszám|Átlag|Használt tárhely|None|
|io_requests|Az IO-kérelmek száma|Darabszám|Átlag|Az IO-kérelmek száma|None|
|io_bytes_read|I/o-bájtok olvasása|Bájt|Átlag|I/o-bájtok olvasása|None|
|io_bytes_written|Írásban írt IO-bájtok|Bájt|Átlag|Írásban írt IO-bájtok|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Használtkapacitás|Használt kapacitás|Bájt|Átlag|Használt számla kapacitása|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|Geotípus,ApiName,Hitelesítés|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A storage-szolgáltatásvagy a megadott API-művelet sikeres kérelmek átlagos végpont-végponti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|BlobCapacity kapacitás|Blob kapacitása|Bájt|Átlag|A tárfiók Blob szolgáltatása által használt tárterület bájtban használt tárterület mennyisége.|BlobType,Szint|
|BlobCount (BlobCount)|Blobok száma|Darabszám|Átlag|A blob száma a tárfiók Blob szolgáltatásában.|BlobType,Szint|
|Konténerszám|Blob-tárolók száma|Darabszám|Átlag|A tárolók száma a tárfiók Blob szolgáltatásában.|None|
|Indexkapacitás|Index kapacitása|Bájt|Átlag|Az ADLS Gen2 (hierarchikus) index által használt tárterület bájtban megadott mennyisége.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|Geotípus,ApiName,Hitelesítés|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A storage-szolgáltatásvagy a megadott API-művelet sikeres kérelmek átlagos végpont-végponti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Táblakapacitás|Tábla kapacitása|Bájt|Átlag|A tárfiók Table szolgáltatása által használt tárterület által használt tárterület bájtban felhasznált tárterület mennyisége.|None|
|Tábladarabszám|Táblaszáma|Darabszám|Átlag|A tárfiók Table szolgáltatástáblájának száma.|None|
|Táblaentitásszáma|Táblaentitások száma|Darabszám|Átlag|A tárfiók Table szolgáltatásában lévő táblaentitások száma.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|Geotípus,ApiName,Hitelesítés|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A storage-szolgáltatásvagy a megadott API-művelet sikeres kérelmek átlagos végpont-végponti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Fájlkapacitás|Fájl kapacitása|Bájt|Átlag|A tárfiók fájlszolgáltatása által használt tárterület bájtban használt tárterület mennyisége.|Fájlmegosztás|
|Fájlszám|Fájlok száma|Darabszám|Átlag|A tárfiók Fájlszolgáltatásában lévő fájl száma.|Fájlmegosztás|
|FileShareCount|Fájlmegosztások száma|Darabszám|Átlag|A tárfiók Fájlszolgáltatásában lévő fájlmegosztások száma.|None|
|FileShareSnapshotCount|Fájlmegosztás pillanatképének száma|Darabszám|Átlag|A tárfiók Fájlszolgáltatásában a megosztáson jelen lévő pillanatképek száma.|Fájlmegosztás|
|Fájlmegosztássnapshotmérete|Fájlmegosztás pillanatképének mérete|Bájt|Átlag|A pillanatképek által a tárfiók fájlszolgáltatásában bájtokban használt tárképek által használt tárterület mennyisége.|Fájlmegosztás|
|FileSharequota fájlmegosztás|Fájlmegosztási kvóta mérete|Bájt|Átlag|Az Azure Files Service által bájtban használható tárterület összegének felső határa.|Fájlmegosztás|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés,Fájlmegosztás|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A storage-szolgáltatásvagy a megadott API-művelet sikeres kérelmek átlagos végpont-végponti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés,Fájlmegosztás|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Várólistakapacitás|Várólista kapacitása|Bájt|Átlag|A tárfiók várólistaszolgáltatása által használt tárház készletei által használt tárterület bájtban.|None|
|Várólistaszáma|Várólista száma|Darabszám|Átlag|A tárfiók várólista-szolgáltatásában lévő várólista száma.|None|
|QueueMessageCount (Üzenetsor)|Várólista-üzenetek száma|Darabszám|Átlag|A tárfiók várólista-szolgáltatásában lévő várólista-üzenetek hozzávetőleges száma.|None|
|Tranzakciók|Tranzakciók|Darabszám|Összesen|Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és a sikertelen, valamint a hibát eredményező kérelmeket is magában foglalja. A ResponseType dimenziót a különböző típusú válaszok számához használja.|ResponseType,GeoType,ApiName,Hitelesítés|
|Bejövő forgalom|Bejövő forgalom|Bájt|Összesen|A bejövő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja.|Geotípus,ApiName,Hitelesítés|
|Kimenő forgalom|Kimenő forgalom|Bájt|Összesen|A kimenő adatok mennyisége bájtban. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat.|Geotípus,ApiName,Hitelesítés|
|SuccessServerLatency (Sikereskiszolgálók átkának)|Sikeres kiszolgálói kérések késése|Ezredmásodperc|Átlag|Az Azure Storage által a sikeres kérelem feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza az AverageE2ELatency paraméterben megadott hálózati késést.|Geotípus,ApiName,Hitelesítés|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres kérések végpontok közötti késése|Ezredmásodperc|Átlag|A storage-szolgáltatásvagy a megadott API-művelet sikeres kérelmek átlagos végpont-végponti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt.|Geotípus,ApiName,Hitelesítés|
|Rendelkezésre állás|Rendelkezésre állás|Százalék|Átlag|A rendelkezésre állás i a storage szolgáltatás vagy a megadott API-művelet rendelkezésre állásának százalékos aránya. A Rendelkezésre állás a TotalBillableRequests érték és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik.|Geotípus,ApiName,Hitelesítés|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/gyorsítótárak

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ClientIOPS|Összes ügyfél IOPS|Darabszám|Átlag|A gyorsítótár által feldolgozott ügyfélfájlok műveleteinek aránya.|None|
|Ügyfélkés-késés|Átlagos ügyfélkésés|Ezredmásodperc|Átlag|Az ügyfélfájl-műveletek átlagos késése a tárológyorsítótárba.|None|
|ÜgyfélreadiOPS|Ügyfél olvasási IOPS|CountPerSzekszekundum|Átlag|Ügyfél olvasási műveletek másodpercenként.|None|
|Ügyfélolvasásátviteli|Átlagos gyorsítótár olvasási átviteli|BytesPerSecond|Átlag|Az ügyfél olvassa az adatátviteli sebességet.|None|
|ÜgyfélwriteIOPS|Ügyfél írási IOPS|CountPerSzekszekundum|Átlag|Ügyfél írási műveletek másodpercenként.|None|
|ClientWriteThroughput|Gyorsítótár írási átviteli értékátlaga|BytesPerSecond|Átlag|Ügyfél írási adatátviteli sebessége.|None|
|ÜgyfélMetadataReadIOPS|Ügyfél metaadatok olvasási IOPS-ának olvasása|CountPerSzekszekundum|Átlag|A gyorsítótárba küldött olyan ügyfélfájl-műveletek aránya, az adatolvasások kivételével, amelyek nem módosítják az állandó állapotot.|None|
|ÜgyfélMetadataWriteIOPS|Ügyfél metaadatok írási IOPS-a|CountPerSzekszekundum|Átlag|A gyorsítótárba küldött ügyfélfájl-műveletek aránya az adatok írása nélkül, amelyek módosítják az állandó állapotot.|None|
|ClientLockIOPS|Ügyfélzárolási IOPS|CountPerSzekszekundum|Átlag|Ügyfélfájl-zárolási műveletek másodpercenként.|None|
|StorageTargetHealth|Tárolási cél állapota|Darabszám|Átlag|A gyorsítótár és a tárolási célok közötti kapcsolati teszt logikai eredményei.|None|
|Uptime|Uptime|Darabszám|Átlag|A gyorsítótár és a figyelőrendszer közötti kapcsolatteszt logikai eredményei.|None|
|StorageTargetIOPS|Teljes storageTarget IOPS|Darabszám|Átlag|A gyorsítótár által egy adott StorageTarget nek küldött összes fájlművelet aránya.|StorageTarget (Tároló– Cél|
|StorageTargetWriteIOPS|StorageTarget írási IOPS|Darabszám|Átlag|A gyorsítótár által egy adott StorageTarget nek küldött fájlírási műveletek aránya.|StorageTarget (Tároló– Cél|
|StorageTargetAsyncWriteThroughput|StorageTarget aszinkron írási átviteli|BytesPerSecond|Átlag|A gyorsítótár aszinkron módon történő írási sebessége egy adott StorageTarget számára. Ezek opportunista írások, amelyek nem okoznak az ügyfelek blokkolására.|StorageTarget (Tároló– Cél|
|StorageTargetSyncWriteThroughput|StorageTarget szinkron írási átviteli|BytesPerSecond|Átlag|A gyorsítótár által szinkronizáltan írt adatok egy adott StorageTarget. Ezek olyan írások, amelyek miatt az ügyfelek blokkolják.|StorageTarget (Tároló– Cél|
|StorageTargetTotalWriteThroughput|StorageTarget teljes írási átviteli-áteresztő|BytesPerSecond|Átlag|A gyorsítótár által egy adott StorageTarget beírásának teljes sebessége.|StorageTarget (Tároló– Cél|
|StorageTargetLatency (TárolástargetLatency)|StorageTarget késés|Ezredmásodperc|Átlag|A gyorsítótár által a partricular is storagetarget-nek küldött összes fájlművelet átlagos oda-vissza késése.|StorageTarget (Tároló– Cél|
|StorageTargetMetadataReadIOPS|StorageTarget metaadat-olvasási IOPS|CountPerSzekszekundum|Átlag|A gyorsítótár által egy adott StorageTarget nek küldött állandó állapotot nem módosító fájlműveletek aránya, kivéve az olvasási műveletet.|StorageTarget (Tároló– Cél|
|StorageTargetMetadataWriteIOPS|StorageTarget metaadat-írási IOPS|CountPerSzekszekundum|Átlag|Az állandó állapotot módosító és a gyorsítótár által egy adott StorageTarget nek küldött írási műveletet tartalmazó fájlműveletek aránya.|StorageTarget (Tároló– Cél|
|StorageTargetReadIOPS|StorageTarget olvasási IOPS|CountPerSzekszekundum|Átlag|A gyorsítótár által egy adott StorageTarget nek küldött fájlolvasási műveletek aránya.|StorageTarget (Tároló– Cél|
|StorageTargetReadAheadThroughput|StorageTarget előre olvasási átviteli|BytesPerSecond|Átlag|A gyorsítótár opportunista módon beolvassa az adatokat a StorageTarget.|StorageTarget (Tároló– Cél|
|StorageTargetFillThroughput|StorageTarget kitöltési átviteli adatáta|BytesPerSecond|Átlag|A gyorsítótár által a StorageTarget ből beolvasandó adatok beolvasásának sebessége a gyorsítótár-tévesztés kezeléséhez.|StorageTarget (Tároló– Cél|
|StorageTargetTotalReadThroughput|StorageTarget teljes olvasási átviteli-átmenő|BytesPerSecond|Átlag|A gyorsítótár által egy adott StorageTarget ből beolvasott adatok teljes sebessége.|StorageTarget (Tároló– Cél|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Munkamenet-eredmény szinkronizálása|Darabszám|Átlag|Metrika, amely 1-es értéket naplóz minden alkalommal, amikor a kiszolgálóvégpont sikeresen befejezi a szinkronizálási munkamenetet a felhővégpontdal|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSessionperItem-hibák száma|A fájlok szinkronizálása nem|Darabszám|Összesen|A fájlok száma nem sikerült szinkronizálni|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Szinkronizált bájtok|Bájt|Összesen|A szinkronizálási munkamenetek teljes fájlmérete|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely naplózza az 1 értéket minden alkalommal, amikor a resigtered szerver sikeresen rögzíti a szívverést a Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Felhőrétegezés visszahívása|Bájt|Összesen|A kiszolgáló által visszahívott adatok teljes mérete|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Felhőrétegezés visszahívási mérete|Bájt|Összesen|Visszahívott adatok mérete|SyncGroupName,Kiszolgálónév|
|StorageSyncRecallThroughputBytesPerSecond|Felhőrétegezés visszahívási átviteli|BytesPerSecond|Átlag|Az adat-visszaírási átviteli tömeg mérete|SyncGroupName,Kiszolgálónév|
|StorageSyncRecalledNetworkBytesByApplication|Felhőrétegezés visszahívási méret alkalmazásonként|Bájt|Összesen|Az alkalmazás által visszahívott adatok mérete|SyncGroupName,Kiszolgálónév,Alkalmazásnév|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionperItem-hibák száma|A fájlok szinkronizálása nem|Darabszám|Összesen|A fájlok száma nem sikerült szinkronizálni|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Szinkronizált bájtok|Bájt|Összesen|A szinkronizálási munkamenetek teljes fájlmérete|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Szinkronizált fájlok|Darabszám|Összesen|Szinkronizált fájlok száma|Kiszolgálóvégpontneve,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|A fájlok szinkronizálása nem|Darabszám|Összesen|A fájlok száma nem sikerült szinkronizálni|Kiszolgálóvégpontneve,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Szinkronizált bájtok|Bájt|Összesen|A szinkronizálási munkamenetek teljes fájlmérete|Kiszolgálóvégpontneve,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kiszolgálószívverés|Kiszolgáló online állapota|Darabszám|Maximum|Metrika, amely naplózza az 1 értéket minden alkalommal, amikor a resigtered szerver sikeresen rögzíti a szívverést a Cloud Endpoint|ServerResourceId,Kiszolgálónév|
|ServerRecallIOTotalSizeBytes|Felhőrétegezés visszahívása|Bájt|Összesen|A kiszolgáló által visszahívott adatok teljes mérete|ServerResourceId,Kiszolgálónév|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Erőforrás-kihasználtság|SU % kihasználtság|Százalék|Maximum|SU % kihasználtság|Logikai név,PartitionId|
|InputEvents (InputEvents)|Bemeneti események|Darabszám|Összesen|Bemeneti események|Logikai név,PartitionId|
|InputEventBytes|Bemeneti eseménybájtjai|Bájt|Összesen|Bemeneti eseménybájtjai|Logikai név,PartitionId|
|LateInputEsemények|Késői bemeneti események|Darabszám|Összesen|Késői bemeneti események|Logikai név,PartitionId|
|OutputEsemények|Kimeneti események|Darabszám|Összesen|Kimeneti események|Logikai név,PartitionId|
|Konverziós hibák|Adatkonvertálási hibák|Darabszám|Összesen|Adatkonvertálási hibák|Logikai név,PartitionId|
|Hibák|Futásidejű hibák|Darabszám|Összesen|Futásidejű hibák|Logikai név,PartitionId|
|DroppedOrAdjustedEvents|Nem sorrendben események|Darabszám|Összesen|Nem sorrendben események|Logikai név,PartitionId|
|AMLCalloutKérések|Függvénykérések|Darabszám|Összesen|Függvénykérések|Logikai név,PartitionId|
|AMLCalloutFailedRequests|Sikertelen függvénykérelmek|Darabszám|Összesen|Sikertelen függvénykérelmek|Logikai név,PartitionId|
|AMLCalloutInputEvents|Függvényesemények|Darabszám|Összesen|Függvényesemények|Logikai név,PartitionId|
|Deszerializációhiba|Bemeneti deszerializálási hibák|Darabszám|Összesen|Bemeneti deszerializálási hibák|Logikai név,PartitionId|
|EarlyInputEsemények|Korai bemeneti események|Darabszám|Összesen|Korai bemeneti események|Logikai név,PartitionId|
|OutputWatermarkDelaySeconds|Vízjel késleltetése|Másodperc|Maximum|Vízjel késleltetése|Logikai név,PartitionId|
|InputEventsSourcesBacklogged|Eldugott bemeneti események|Darabszám|Maximum|Eldugott bemeneti események|Logikai név,PartitionId|
|InputEventsSourcesperSecond|Fogadott bemeneti források|Darabszám|Összesen|Fogadott bemeneti források|Logikai név,PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/munkaterületek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|OrchestrationPipelineSEnded|A folyamat futtatása véget ért|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított vezénylési folyamatfuttatások száma|Eredmény,FailureType,Folyamat|
|OrchestrationActivityRunsEnded|A tevékenységfuttatások véget értek|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított vezénylési tevékenységek száma|Eredmény,Hibatípus,Tevékenység,Tevékenységtípus,Folyamat|
|OrchestrationTriggersEnded|Az eseményindítók véget értek|Darabszám|Összesen|Sikeres, sikertelen vagy megszakított vezénylési eseményindítók száma|Eredmény,FailureType,Eseményindító|
|SQLOnDemandLoginAttempts|Bejelentkezési kísérletek|Darabszám|Összesen|A sikeres vagy sikertelen bejelentkezési kísérletek száma|Eredmény|
|SQLOnDemandQueriesEnded|A lekérdezések lezárultak|Darabszám|Összesen|A sikeres, sikertelen vagy megszakított lekérdezések száma|Eredmény|
|SQLOnDemandQueryProcessedBytes|Feldolgozott adatok|Bájt|Összesen|Lekérdezések által feldolgozott adatok mennyisége|None|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|SparkJobsEnded|Lekötött alkalmazások|Darabszám|Összesen|A megszakított alkalmazások száma|Feladattípus,Feladateredmény|
|MagokKapacitás|Magkapacitás|Darabszám|Maximum|Magkapacitás|None|
|Memóriakapacitásgb|Memóriakapacitás (GB)|Darabszám|Maximum|Memóriakapacitás (GB)|None|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DWULimit|DWU-korlát|Darabszám|Maximum|Az SQL-készlet szolgáltatási szintű célkitűzése|None|
|DWUHasznált|Használt DWU|Darabszám|Maximum|A használat magas szintű megjelenítését jelöli az SQL-készletben. DWU-határértékszerint mérve * DWU százalék|None|
|DWUUSEDPercent|DWU használt százalék|Százalék|Maximum|A használat magas szintű megjelenítését jelöli az SQL-készletben. A CPU-százalék és az adat I/-százalék közötti maximális érték figyelembevételével mérve|None|
|Kapcsolatokblokkolástűzfallal|Tűzfal által blokkolt kapcsolatok|Darabszám|Összesen|A tűzfalszabályok által blokkolt kapcsolatok száma. Az SQL-készlet hozzáférés-vezérlési házirendjeinek újbóli meglátogatása és a kapcsolatok figyelése, ha a számláló magas|None|
|AdaptiveCacheHitPercent|Adaptív gyorsítótár találati százaléka|Százalék|Maximum|Azt méri, hogy a munkaterhelések mennyire jól használja az adaptív gyorsítótárat. Ezzel a metrikával a gyorsítótár leolvasási százalékos metrikája határozza meg, hogy a méretezés további kapacitás vagy újrafut számítási feladatok hidratálni a gyorsítótárat|None|
|AdaptiveCacheUsedPercent|Adaptív gyorsítótár használt százaléka|Százalék|Maximum|Azt méri, hogy a munkaterhelések mennyire jól használja az adaptív gyorsítótárat. Használja ezt a metrikát a gyorsítótár által használt százalékos metrikával annak meghatározásához, hogy további kapacitást méretez-e, vagy újrafuttassa a számítási feladatokat a gyorsítótár hidratálásához.|None|
|LocalTempDBUsedPercent|Helyi tempdb használt százalék|Százalék|Maximum|Helyi tempdb-használat az összes számítási csomóponton – az értékek ötpercenként kerülnek kibocsátásra|None|
|Használt memóriaPercent|Felhasznált memória százaléka|Százalék|Maximum|Memóriakihasználtság az SQL-készlet összes csomópontja között|None|
|Kapcsolatok|Kapcsolatok|Darabszám|Összesen|Az SQL-készletbe való összes bejelentkezés száma|Eredmény|
|WLGActiveQueries|Számítási feladatok csoport aktív lekérdezései|Darabszám|Összesen|A számítási feladatok csoporton belüli aktív lekérdezések. A szűretlen és felbontatlan mérőszám használata a rendszeren futó összes aktív lekérdezést megjeleníti.|IsUserDefined,WorkloadGroup|
|WLGActiveQueriesIdő-outok|Számítási feladatok csoport lekérdezési időmegválaszolásai|Darabszám|Összesen|Az időkimenő terhelést okozó munkaterhelés-csoport lekérdezései. Ez a mérőszám által jelentett lekérdezési időtúllépések csak akkor jelennek meg, ha a lekérdezés végrehajtása megkezdődött (nem tartalmazza a zárolás vagy az erőforrás-várakozás miatti várakozási időt)|IsUserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Számítási feladatok csoportjának lefoglalása rendszerszázalék szerint|Százalék|Maximum|Az erőforrások nak a teljes rendszerhez viszonyított százalékos elosztása|IsUserDefined,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Számítási feladatok csoportjának lefoglalása maximális erőforrásszázalék szerint|Százalék|Maximum|Az erőforrások számítási terhelésenkénti tényleges korláthoz viszonyított százalékos allokációját jeleníti meg. Ez a metrika biztosítja a számítási feladatok csoport hatékony kihasználását|IsUserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Effektív erőforrás-korlát százaléka|Százalék|Maximum|A számítási feladatok csoport tényleges erőforrás-százaléka. Ha vannak más számítási min_percentage_resource > 0-val rendelkező munkaterhelési csoportok, a effective_cap_percentage_resource arányosan csökken|IsUserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Effektív min erőforrás százalék|Százalék|Minimális|A szolgáltatásszint és a munkaterhelési csoport beállításainak figyelembevételével engedélyezett tényleges min erőforrás-százalékos beállítás. A hatékony min_percentage_resource magasabbra állítható az alacsonyabb szolgáltatási szinteken|IsUserDefined,WorkloadGroup|
|WLGQueuedQueries|Munkaterhelés-csoport várólistán lévő lekérdezései|Darabszám|Összesen|A maximális egyidejűségi korlát elérése után várólistára helyezett kérelmek összesített száma|IsUserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/környezetek

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Fogadott üzenetek be- és beérkezések|Darabszám|Összesen|Az összes eseményközpontból vagy IoT-központ eseményforrásából olvasott üzenetek száma|None|
|Bejövő üzenetekfogadásaérvénytelenüzenetek|Bejövő üzenetek bejövő üzenetek|Darabszám|Összesen|Az összes eseményközpontból vagy IoT-központ eseményforrásából beolvasott érvénytelen üzenetek száma|None|
|Be- és be- és beérkezésérkezett|Fogadott bájtok beérkezése|Bájt|Összesen|Az összes eseményforrásból beolvasott bájtok száma|None|
|BeressStoredBytes|Tárolt be- és betolatás|Bájt|Összesen|A sikeresen feldolgozott és lekérdezésre elérhető események teljes mérete|None|
|IngressStoredEsemények|Be- és betolatott események|Darabszám|Összesen|A sikeresen feldolgozott és lekérdezéshez elérhető összeolvasztott események száma|None|
|IngressReceivedMessagesTimeLag|Be- és beérkező üzenetek időbeli eltolódása|Másodperc|Maximum|Az üzenet eseményforrásban való várólistán töltött ideje és a bejövő forgalomban történő feldolgozása közötti különbség|None|
|IngressReceivedMessagesCountLag|Be- és beérkező üzenetek száma lag|Darabszám|Átlag|Az eseményforrás-partíción utoljára várólistán lévő üzenetek sorszáma és a bejövő forgalomban feldolgozott üzenetek sorszáma közötti különbség|None|
|WarmStorageMaxProperties|Meleg tároló max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 Termékváltozat számára engedélyezett tulajdonságok maximális száma és a Warm Store által a PAYG Termékváltozathoz engedélyezett tulajdonságok maximális száma|None|
|WarmStorageUsedProperties|A használt meleg tároló tulajdonságai |Darabszám|Maximum|A környezet által az S1/S2 Termékváltozathoz használt tulajdonságok száma és a Warm Store által a PAYG Termékváltozathoz használt tulajdonságok száma|None|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|IngressReceivedMessages|Fogadott üzenetek be- és beérkezések|Darabszám|Összesen|Az eseményforrásból olvasott üzenetek száma|None|
|Bejövő üzenetekfogadásaérvénytelenüzenetek|Bejövő üzenetek bejövő üzenetek|Darabszám|Összesen|Az eseményforrásból beolvasott érvénytelen üzenetek száma|None|
|Be- és be- és beérkezésérkezett|Fogadott bájtok beérkezése|Bájt|Összesen|Az eseményforrásból beolvasott bájtok száma|None|
|BeressStoredBytes|Tárolt be- és betolatás|Bájt|Összesen|A sikeresen feldolgozott és lekérdezésre elérhető események teljes mérete|None|
|IngressStoredEsemények|Be- és betolatott események|Darabszám|Összesen|A sikeresen feldolgozott és lekérdezéshez elérhető összeolvasztott események száma|None|
|IngressReceivedMessagesTimeLag|Be- és beérkező üzenetek időbeli eltolódása|Másodperc|Maximum|Az üzenet eseményforrásban való várólistán töltött ideje és a bejövő forgalomban történő feldolgozása közötti különbség|None|
|IngressReceivedMessagesCountLag|Be- és beérkező üzenetek száma lag|Darabszám|Átlag|Az eseményforrás-partíción utoljára várólistán lévő üzenetek sorszáma és a bejövő forgalomban feldolgozott üzenetek sorszáma közötti különbség|None|
|WarmStorageMaxProperties|Meleg tároló max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 Termékváltozat számára engedélyezett tulajdonságok maximális száma és a Warm Store által a PAYG Termékváltozathoz engedélyezett tulajdonságok maximális száma|None|
|WarmStorageUsedProperties|A használt meleg tároló tulajdonságai |Darabszám|Maximum|A környezet által az S1/S2 Termékváltozathoz használt tulajdonságok száma és a Warm Store által a PAYG Termékváltozathoz használt tulajdonságok száma|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Lemez olvasási bájtjai/mp|BytesPerSecond|Átlag|Átlagos lemezátviteli rendszer a mintaidőszak olvasási műveletei miatt.|None|
|DiskWriteBytesPerSecond|Lemezírási bájtok/mp|BytesPerSecond|Átlag|Átlagos lemezátviteli rendszer a mintaidőszak alatt végzett írási műveletek miatt.|None|
|Lemezolvasási bájtok|Lemezolvasási bájtok|Bájt|Összesen|A mintaidőszak olvasási műveleteinek köszönhetően a lemez teljes átviteli-átaputája.|None|
|Lemezírási bájtok|Lemezírási bájtok|Bájt|Összesen|A mintaidőszak írási műveleteinek köszönhetően a lemez teljes átviteli terhelése.|None|
|DiskReadOperations|Lemezolvasási műveletek|Darabszám|Összesen|Az előző mintavételi időszakban végzett i/o-olvasási műveletek száma. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|DiskWriteOperations|Lemezírási műveletek|Darabszám|Összesen|Az előző mintavételi időszakban végzett IO-írási műveletek száma. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|Lemezolvasási műveletek/mp|Lemezolvasási műveletek/mp|CountPerSzekszekundum|Átlag|Az előző mintavételi időszakban az IO olvasási műveletek átlagos száma. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|Lemezírási műveletek/mp|Lemezírási műveletek/mp|CountPerSzekszekundum|Átlag|Az előző mintavételi időszakban az IO írási műveletek átlagos száma. Vegye figyelembe, hogy ezek a műveletek változó méretűek lehetnek.|None|
|DiskReadLatency|Lemezolvasási késés|Ezredmásodperc|Átlag|Teljes olvasási késleltetés. Az eszköz és a kernel olvasási késleltetéseinek összege.|None|
|DiskWriteLatency|Lemezírási késés|Ezredmásodperc|Átlag|Teljes írási késés. Az eszköz és a kernel írási késéseinek összege.|None|
|NetworkInBytesPerSecond|Hálózat bájt/mp-ben|BytesPerSecond|Átlag|A fogadott forgalom átlagos hálózati átviteli forgalma.|None|
|NetworkOutBytesPerSecond|Hálózati kimenő bájtok/mp|BytesPerSecond|Átlag|A továbbított forgalom átlagos hálózati átviteli forgalma.|None|
|Bejövő hálózat|Bejövő hálózat|Bájt|Összesen|A fogadott forgalom teljes hálózati átviteli forgalma.|None|
|Kimenő hálózat|Kimenő hálózat|Bájt|Összesen|A továbbított forgalom teljes hálózati átviteli forgalma.|None|
|Használt memória|Használt memória|Bájt|Átlag|A virtuális gép által használt gépmemória mennyisége.|None|
|Memória biztosított|Memória megadva|Bájt|Átlag|A gazdagép által a virtuális gépnek biztosított memória mennyisége. A memória nem adható meg a gazdagépnek, amíg egyszer meg nem érinti, és a megadott memóriát fel lehet cserélni vagy lufiba lehet dobni, ha a virtuális géprendszernek szüksége van a memóriára.|None|
|MemoryActive|Aktív memória|Bájt|Átlag|A virtuális gép által az elmúlt kis időablakban használt memória mennyisége. Ez az "igaz" száma, hogy mennyi memóriát a virtuális gép jelenleg szüksége van. További, a fel nem használt memória lehet cserélni, vagy ballonos hatással nincs a vendég teljesítményét.|None|
|Százalékos processzorhasználat|Százalékos processzorhasználat|Százalék|Átlag|A PROCESSZOR-kihasználtság. Ez az érték a rendszer 100%-os értékével jelenik meg a rendszer összes processzormagjával. Például egy kétutas virtuális gép, amely egy négymagos rendszer 50%-át használja, teljes mértékben két magot használ.|None|
|Százalékprocesszorready|Százalékos CPU ready|Ezredmásodperc|Összesen|A kész idő az az idő, amely arra vár, hogy a PROCESSZOR(ok) elérhetővé váljon az elmúlt frissítési időközben.|None|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Cpuszázalék|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|Memóriaszázalék|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvárólista hossza|Darabszám|Átlag|Lemezvárólista hossza|Példány|
|HttpQueueLength között|Http Várólista hossza|Darabszám|Átlag|Http Várólista hossza|Példány|
|Fogadott bájt|Adatok a|Bájt|Összesen|Adatok a|Példány|
|Küldött bájt|Adatok ki|Bájt|Összesen|Adatok ki|Példány|
|TcpSynSent|TCP Syn elküldve|Darabszám|Átlag|TCP Syn elküldve|Példány|
|TcpSynÉrkezett|TCP Syn fogadott|Darabszám|Átlag|TCP Syn fogadott|Példány|
|TcpLétrehozt|TCP létrehozva|Darabszám|Átlag|TCP létrehozva|Példány|
|TcpFinWait1|TCP Fin várjon 1|Darabszám|Átlag|TCP Fin várjon 1|Példány|
|TcpFinWait2|TCP Fin Várjon 2|Darabszám|Átlag|TCP Fin Várjon 2|Példány|
|TcpLezárás|TCP zárása|Darabszám|Átlag|TCP zárása|Példány|
|TcpCloseVárakozás|TCP bezárása várakozás|Darabszám|Átlag|TCP bezárása várakozás|Példány|
|TcpLastAck|TCP utolsó Ack|Darabszám|Átlag|TCP utolsó Ack|Példány|
|TcpTimeVárakozás|TCP-idő várakozása|Darabszám|Átlag|TCP-idő várakozása|Példány|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (a funkciók kivételével) 

> [!NOTE]
> **A fájlrendszer használata** egy új mérőszám, amelyet globálisan vezetnek be, csak akkor várható adat, ha a privát előzetes verzióhoz engedélyezési listán szerepel.

> [!IMPORTANT]
> **Az átlagos válaszidő** elavult, hogy elkerülje a metrikaösszesítésekkel való összetévesztést. A **válaszidő** helyett használja.

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|PROCESSZOR-idő|Másodperc|Összesen|PROCESSZOR-idő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|Fogadott bájt|Adatok a|Bájt|Összesen|Adatok a|Példány|
|Küldött bájt|Adatok ki|Bájt|Összesen|Adatok ki|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx között|Http 2xx|Darabszám|Összesen|Http 2xx|Példány|
|Http3xx között|Http 3xx|Darabszám|Összesen|Http 3xx|Példány|
|Http401|Http 401|Darabszám|Összesen|Http 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx között|Http 4xx|Darabszám|Összesen|Http 4xx|Példány|
|Http5xx között|Http Server hibák|Darabszám|Összesen|Http Server hibák|Példány|
|MemoryWorkingSet|Memóriamunkakészlet|Bájt|Átlag|Memóriamunkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memóriamunkakészlet|Bájt|Átlag|Átlagos memóriamunkakészlet|Példány|
|Válaszidő|Válasz ideje|Másodperc|Összesen|Válasz ideje|Példány|
|AverageResponseTime|Átlagos válaszidő (elavult)|Másodperc|Átlag|Átlagos válaszidő|Példány|
|Alkalmazáskapcsolatok|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|Kezeli|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
|Szálak|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|Privát bájt|Privát bájtok|Bájt|Átlag|Privát bájtok|Példány|
|IoReadBytesPerSz|Io olvasási bájt másodpercenként|BytesPerSecond|Összesen|Io olvasási bájt másodpercenként|Példány|
|IoWriteBytesPerSecond|Io írási bájt másodpercenként|BytesPerSecond|Összesen|Io írási bájt másodpercenként|Példány|
|IoOtherBytesPerSz|Egyéb bájtok másodpercenként|BytesPerSecond|Összesen|Egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Io olvasási műveletek másodpercenként|BytesPerSecond|Összesen|Io olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|I/O-írási műveletek másodpercenként|BytesPerSecond|Összesen|I/O-írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|Io egyéb műveletek másodpercenként|BytesPerSecond|Összesen|Io egyéb műveletek másodpercenként|Példány|
|Kérelmekaz alkalmazásvárólistában|Kérelmek az alkalmazásvárólistában|Darabszám|Átlag|Kérelmek az alkalmazásvárólistában|Példány|
|CurrentAssemblies között|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains (Összes alkalmazásdomain)|Összes alkalmazástartomány|Darabszám|Átlag|Összes alkalmazástartomány|Példány|
|TotalAppDomainsUnloaded|A kiürített összes alkalmazástartomány|Darabszám|Átlag|A kiürített összes alkalmazástartomány|Példány|
|Gen0Gyűjtemények|Gen 0 Szemétgyűjtemények|Darabszám|Összesen|Gen 0 Szemétgyűjtemények|Példány|
|Gen1Gyűjtemények|Gen 1 Szemétgyűjtemények|Darabszám|Összesen|Gen 1 Szemétgyűjtemények|Példány|
|Gen2Gyűjtemények|Gen 2 Szemétgyűjtemények|Darabszám|Összesen|Gen 2 Szemétgyűjtemények|Példány|
|Állapotellenőrző állapota|Állapot-ellenőrzés állapota|Darabszám|Átlag|Állapot-ellenőrzés állapota|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/webhelyek (funkciók)

> [!NOTE]
> **A fájlrendszer használata** egy új mérőszám, amelyet globálisan vezetnek be, csak akkor várható adat, ha a privát előzetes verzióhoz engedélyezési listán szerepel.

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Fogadott bájt|Adatok a|Bájt|Összesen|Adatok a|Példány|
|Küldött bájt|Adatok ki|Bájt|Összesen|Adatok ki|Példány|
|Http5xx között|Http Server hibák|Darabszám|Összesen|Http Server hibák|Példány|
|MemoryWorkingSet|Memóriamunkakészlet|Bájt|Átlag|Memóriamunkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memóriamunkakészlet|Bájt|Átlag|Átlagos memóriamunkakészlet|Példány|
|FunctionExecutionUnits|Funkcióvégrehajtási egységek|MB / Ezredmásodpercek|Összesen|[Funkcióvégrehajtási egységek](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Példány|
|FunctionExecutionCount (FunctionExecutionCount)|Függvényvégrehajtási száma|Darabszám|Összesen|Függvényvégrehajtási száma|Példány|
|Privát bájt|Privát bájtok|Bájt|Átlag|Privát bájtok|Példány|
|IoReadBytesPerSz|Io olvasási bájt másodpercenként|BytesPerSecond|Összesen|Io olvasási bájt másodpercenként|Példány|
|IoWriteBytesPerSecond|Io írási bájt másodpercenként|BytesPerSecond|Összesen|Io írási bájt másodpercenként|Példány|
|IoOtherBytesPerSz|Egyéb bájtok másodpercenként|BytesPerSecond|Összesen|Egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Io olvasási műveletek másodpercenként|BytesPerSecond|Összesen|Io olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|I/O-írási műveletek másodpercenként|BytesPerSecond|Összesen|I/O-írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|Io egyéb műveletek másodpercenként|BytesPerSecond|Összesen|Io egyéb műveletek másodpercenként|Példány|
|Kérelmekaz alkalmazásvárólistában|Kérelmek az alkalmazásvárólistában|Darabszám|Átlag|Kérelmek az alkalmazásvárólistában|Példány|
|CurrentAssemblies között|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains (Összes alkalmazásdomain)|Összes alkalmazástartomány|Darabszám|Átlag|Összes alkalmazástartomány|Példány|
|TotalAppDomainsUnloaded|A kiürített összes alkalmazástartomány|Darabszám|Átlag|A kiürített összes alkalmazástartomány|Példány|
|Gen0Gyűjtemények|Gen 0 Szemétgyűjtemények|Darabszám|Összesen|Gen 0 Szemétgyűjtemények|Példány|
|Gen1Gyűjtemények|Gen 1 Szemétgyűjtemények|Darabszám|Összesen|Gen 1 Szemétgyűjtemények|Példány|
|Gen2Gyűjtemények|Gen 2 Szemétgyűjtemények|Darabszám|Összesen|Gen 2 Szemétgyűjtemények|Példány|
|Állapotellenőrző állapota|Állapot-ellenőrzés állapota|Darabszám|Átlag|Állapot-ellenőrzés állapota|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slot

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|CpuTime|PROCESSZOR-idő|Másodperc|Összesen|PROCESSZOR-idő|Példány|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|Fogadott bájt|Adatok a|Bájt|Összesen|Adatok a|Példány|
|Küldött bájt|Adatok ki|Bájt|Összesen|Adatok ki|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx között|Http 2xx|Darabszám|Összesen|Http 2xx|Példány|
|Http3xx között|Http 3xx|Darabszám|Összesen|Http 3xx|Példány|
|Http401|Http 401|Darabszám|Összesen|Http 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx között|Http 4xx|Darabszám|Összesen|Http 4xx|Példány|
|Http5xx között|Http Server hibák|Darabszám|Összesen|Http Server hibák|Példány|
|MemoryWorkingSet|Memóriamunkakészlet|Bájt|Átlag|Memóriamunkakészlet|Példány|
|AverageMemoryWorkingSet|Átlagos memóriamunkakészlet|Bájt|Átlag|Átlagos memóriamunkakészlet|Példány|
|AverageResponseTime|Átlagos válaszidő|Másodperc|Átlag|Átlagos válaszidő|Példány|
|httpResponseTime|Válasz ideje|Másodperc|Átlag|Válasz ideje|Példány|
|FunctionExecutionUnits|Funkcióvégrehajtási egységek|Darabszám|Összesen|Funkcióvégrehajtási egységek|Példány|
|FunctionExecutionCount (FunctionExecutionCount)|Függvényvégrehajtási száma|Darabszám|Összesen|Függvényvégrehajtási száma|Példány|
|Alkalmazáskapcsolatok|Kapcsolatok|Darabszám|Átlag|Kapcsolatok|Példány|
|Kezeli|Leírók száma|Darabszám|Átlag|Leírók száma|Példány|
|Szálak|Szálak száma|Darabszám|Átlag|Szálak száma|Példány|
|Privát bájt|Privát bájtok|Bájt|Átlag|Privát bájtok|Példány|
|IoReadBytesPerSz|Io olvasási bájt másodpercenként|BytesPerSecond|Összesen|Io olvasási bájt másodpercenként|Példány|
|IoWriteBytesPerSecond|Io írási bájt másodpercenként|BytesPerSecond|Összesen|Io írási bájt másodpercenként|Példány|
|IoOtherBytesPerSz|Egyéb bájtok másodpercenként|BytesPerSecond|Összesen|Egyéb bájtok másodpercenként|Példány|
|IoReadOperationsPerSecond|Io olvasási műveletek másodpercenként|BytesPerSecond|Összesen|Io olvasási műveletek másodpercenként|Példány|
|IoWriteOperationsPerSecond|I/O-írási műveletek másodpercenként|BytesPerSecond|Összesen|I/O-írási műveletek másodpercenként|Példány|
|IoOtherOperationsPerSecond|Io egyéb műveletek másodpercenként|BytesPerSecond|Összesen|Io egyéb műveletek másodpercenként|Példány|
|Kérelmekaz alkalmazásvárólistában|Kérelmek az alkalmazásvárólistában|Darabszám|Átlag|Kérelmek az alkalmazásvárólistában|Példány|
|CurrentAssemblies között|Aktuális szerelvények|Darabszám|Átlag|Aktuális szerelvények|Példány|
|TotalAppDomains (Összes alkalmazásdomain)|Összes alkalmazástartomány|Darabszám|Átlag|Összes alkalmazástartomány|Példány|
|TotalAppDomainsUnloaded|A kiürített összes alkalmazástartomány|Darabszám|Átlag|A kiürített összes alkalmazástartomány|Példány|
|Gen0Gyűjtemények|Gen 0 Szemétgyűjtemények|Darabszám|Összesen|Gen 0 Szemétgyűjtemények|Példány|
|Gen1Gyűjtemények|Gen 1 Szemétgyűjtemények|Darabszám|Összesen|Gen 1 Szemétgyűjtemények|Példány|
|Gen2Gyűjtemények|Gen 2 Szemétgyűjtemények|Darabszám|Összesen|Gen 2 Szemétgyűjtemények|Példány|
|Állapotellenőrző állapota|Állapot-ellenőrzés állapota|Darabszám|Átlag|Állapot-ellenőrzés állapota|Példány|
|FileSystemUsage|Fájlrendszer használata|Bájt|Átlag|Fájlrendszer használata|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Kérelmek|Kérelmek|Darabszám|Összesen|Kérelmek|Példány|
|Fogadott bájt|Adatok a|Bájt|Összesen|Adatok a|Példány|
|Küldött bájt|Adatok ki|Bájt|Összesen|Adatok ki|Példány|
|Http101|Http 101|Darabszám|Összesen|Http 101|Példány|
|Http2xx között|Http 2xx|Darabszám|Összesen|Http 2xx|Példány|
|Http3xx között|Http 3xx|Darabszám|Összesen|Http 3xx|Példány|
|Http401|Http 401|Darabszám|Összesen|Http 401|Példány|
|Http403|Http 403|Darabszám|Összesen|Http 403|Példány|
|Http404|Http 404|Darabszám|Összesen|Http 404|Példány|
|Http406|Http 406|Darabszám|Összesen|Http 406|Példány|
|Http4xx között|Http 4xx|Darabszám|Összesen|Http 4xx|Példány|
|Http5xx között|Http Server hibák|Darabszám|Összesen|Http Server hibák|Példány|
|AverageResponseTime|Átlagos válaszidő|Másodperc|Átlag|Átlagos válaszidő|Példány|
|Cpuszázalék|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|Memóriaszázalék|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
|DiskQueueLength|Lemezvárólista hossza|Darabszám|Átlag|Lemezvárólista hossza|Példány|
|HttpQueueLength között|Http Várólista hossza|Darabszám|Átlag|Http Várólista hossza|Példány|
|ActiveRequests|Aktív kérelmek|Darabszám|Összesen|Aktív kérelmek|Példány|
|TotalFrontends|Elülső végek összesen|Darabszám|Átlag|Elülső végek összesen|None|
|SmallAppServicePlanInstances|Kis app osszes alkalmazáscsomag-dolgozók|Darabszám|Átlag|Kis app osszes alkalmazáscsomag-dolgozók|None|
|MediumAppServicePlanInstances|Közepes alkalmazású szolgáltatáscsomag-dolgozók|Darabszám|Átlag|Közepes alkalmazású szolgáltatáscsomag-dolgozók|None|
|LargeAppServicePlanInstances|Nagy app szolgáltatáscsomag-dolgozók|Darabszám|Átlag|Nagy app szolgáltatáscsomag-dolgozók|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metrikus megjelenítendő név|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|Dolgozók összesen|Összes dolgozó|Darabszám|Átlag|Összes dolgozó|None|
|Elérhető dolgozók|Elérhető dolgozók|Darabszám|Átlag|Elérhető dolgozók|None|
|Használt dolgozók|Használt munkavállalók|Darabszám|Átlag|Használt munkavállalók|None|
|Cpuszázalék|Processzorhasználat (%)|Százalék|Átlag|Processzorhasználat (%)|Példány|
|Memóriaszázalék|Memória százaléka|Százalék|Átlag|Memória százaléka|Példány|
## <a name="next-steps"></a>További lépések
* [A metrikákról az Azure Monitorban olvashat](data-platform.md)
* [Riasztások létrehozása metrikákhoz](alerts-overview.md)
* [Mérőszámok exportálása tárolásra, eseményközpontra vagy logelemzésre](platform-logs-overview.md)

