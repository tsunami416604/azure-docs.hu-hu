---
title: Adatok betöltési idő jelentkezzen be az Azure Monitor |} A Microsoft Docs
description: Ismerteti a különböző tényező befolyásolja az Azure Monitor log-adatok gyűjtése a késés.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: ba9a0ab775e062f21a058b537e289fe3ea2b40bb
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000046"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Naplózási adatok betöltési idő, az Azure monitorban
Az Azure Monitor egy nagy méretű szolgáltatás, amely több ezer ügyfelünk egyre bővülő ütemben havonta terabájtnyi adatot küldő szolgál. Vannak gyakran kérdések mennyi időt vesz igénybe a naplózási adatokat a begyűjtésük után lesz elérhető. Ez a cikk ismerteti a különböző tényező befolyásolja, a késés.

## <a name="typical-latency"></a>Átlagos késés
Késés a létrehozott adatok a figyelt rendszeren és az idő, hogy előre elemezhetők az Azure Monitor hivatkozik. A Teljesítménynapló-adatok betöltését jellemző várakozási 2 és 5 perc között van. A megadott késése az adott adatok számos tényezőtől alábbi leírásban függően változhat.


## <a name="factors-affecting-latency"></a>Késés befolyásoló tényezők
A teljes feldolgozási idő egy adott adatkészlet is kell bontani a következő magas szintű területeken. 

- Az ügynök idő – Fedezze fel az esemény, gyűjteni, és majd küldje el az Azure Monitor Adatbetöltési pont, egy naplórekord ideje. A legtöbb esetben ez a folyamat egy ügynök kezeli.
- Folyamat idő – az idő a betöltési folyamat naplórekord feldolgozásához. Ez magában foglalja az esemény tulajdonságainak elemzés és a lehetséges hozzáadni számított információkat.
- Indexelési idő – egy naplórekord Azure Monitor big data store-ba, hogy az idő.

A különböző késést, a folyamat részletei az alábbiakban tekintheti át.

### <a name="agent-collection-latency"></a>Az ügynök gyűjtemény késés
Az ügynökök és felügyeleti megoldásokat különböző stratégiák segítségével adatokat gyűjteni a virtuális gép, amelyek befolyásolhatják a várakozási. Néhány konkrét példa a következők:

- Windows-események, a syslog-események és a teljesítmény-mérőszámokat a rendszer azonnal gyűjti. Linuxos teljesítményszámlálókkal, 30 másodperces időközönként kérdezi le azt.
- IIS-naplók és egyéni naplók összegyűjtése után időbélyegzőik változik. Az IIS-naplók, ez befolyásolja a [átvitel ütemezése az IIS-kiszolgálón konfigurált](data-sources-iis-logs.md). 
- Active Directory replikáció-megoldás a kiértékelés öt naponta, hajt végre, amíg az Active Directory Assessment megoldás hajt végre az Active Directory infrastruktúrával heti értékelése. Az ügynök összegyűjti ezeket a naplókat, csak ha értékelés befejeződött.

### <a name="agent-upload-frequency"></a>Az ügynök feltöltési gyakoriságának
Annak érdekében, hogy a Log Analytics-ügynököket az egyszerűsített, az ügynök naplóinak puffereli, és rendszeres időközönként feltölti őket az Azure Monitor. Töltse fel gyakorisága 30 másodperc és az adatok típusától függően 2 perc közé esik. A legtöbb adatfeltöltés 1 perc alatt. Hálózati körülmények hátrányosan befolyásolhatják a késést az adatok az Azure Monitor Adatbetöltési pont eléréséhez.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Azure-Tevékenységnaplók, diagnosztikai naplók és mérőszámok
Az Azure data ad hozzá a Log Analytics Adatbetöltési pont feldolgozási címen elérhető legyen, további időt:

- Diagnosztikai naplók adatait az Azure-szolgáltatás függően a 2-15 percet igénybe vehet. Tekintse meg a [az alábbi lekérdezés](#checking-ingestion-time) megvizsgálhatja a késés, a környezetben
- Az Azure platform metrikák kell küldeni a Log Analytics Adatbetöltési pont 3 percet igénybe vehet.
- Tevékenységnapló adatainak kell küldeni a Log Analytics Adatbetöltési pont körülbelül 10 – 15 percig fog tartani.

Adatbetöltési pont érhető el, miután adatok lekérdezése elérhető további 2 – 5 percet vesz igénybe.

### <a name="management-solutions-collection"></a>Felügyeleti megoldások gyűjtemény
Egyes megoldások ne gyűjtsön adataikat az ügynök, és előfordulhat, hogy a módszert egy gyűjtemény, amely további késleltetést. Egyes megoldások adatgyűjtést rendszeres időközönként közel valós idejű gyűjtemény kísérlet nélkül. Példák a következők:

- Az Office 365-megoldás tevékenységeket tartalmazó naplók az Office 365 felügyeleti tevékenység API, amely jelenleg nem biztosít semmilyen közel valós idejű késési garancia használatával kérdezi le.
- Windows Analytics-megoldások (például frissítési megfelelőség szempontjából) adatait gyűjti a megoldás a napi gyakorisággal.

Tekintse meg az egyes megoldások annak gyűjtési gyakoriságát dokumentációját.

### <a name="pipeline-process-time"></a>Pipeline-feldolgozási idő
Ha a rekordok naplózása az Azure Monitor folyamat be vannak töltve, íródtak egy ideiglenes tárolóra annak biztosítása érdekében a bérlők elszigetelésére, és győződjön meg arról, hogy az adatok nem vesznek el. Ez a folyamat általában 5 – 15 másodperc hozzáadja. Egyes felügyeleti megoldások megvalósítása nehezebb algoritmusokat összesített adatokat, és hozzájut, az adatok a streamelés a. Például a hálózati teljesítmény figyelése összesíti a bejövő adatok keresztül 3 perces időközönként, hatékonyan hozzáadása a 3 perces késéssel. Egy másik folyamat, amely hozzáadja a késés az a, amely kezeli az egyéni naplókat. Bizonyos esetekben ez a folyamat néhány percet a késés előfordulhat, hogy hozzá-fájlokból az ügynök által gyűjtött naplók.

### <a name="new-custom-data-types-provisioning"></a>Kiépítés új egyéni adattípusok
Az egyéni adatokat egy új típusú létrehozásakor egy [egyéni napló](data-sources-custom-logs.md) vagy a [adatgyűjtő API](data-collector-api.md), a rendszer létrehoz egy dedikált storage-tárolóba. Ez az egy egyszeri terhelést, amely csak az első megjelenítési módja, ez az adattípus megy végbe.

### <a name="surge-protection"></a>Védelem
Azure monitor a legmagasabb prioritást, hogy gondoskodjon arról, hogy nincsenek felhasználói adatok elvesznek, így a rendszer az adatok megnövekedésénél beépített védelemmel rendelkezik. Ez magában foglalja a pufferek ellenőrizze, hogy még terhelés környezetéről, a rendszer fog tartani működik. Normál terhelés alatt ezek a vezérlők hozzáadása a kevesebb mint egy percet, de a feltételek és a hibák, lehetőség van felvenni jelentős mennyiségű időt, miközben biztosítja az adatok biztonságos-e.

### <a name="indexing-time"></a>Indexelési idő
Nincs közötti analytics és a fejlett keresési képességek figyelésekor az adatokhoz való azonnali hozzáférés biztosítása minden big data platform beépített egyensúlyt. Az Azure Monitor lehetővé teszi nagy teljesítményű lekérdezések futtatása több milliárd rekordon, és néhány másodpercen belül eredményeinek beolvasása. Ez lehetséges legyen, mivel az infrastruktúra során az Adatbetöltési jelentősen alakítja át az adatokat, és tárolja azokat az egyedi kompakt struktúrák. A rendszer puffereli az adatokat, amíg elegendő az, hogy nem áll rendelkezésre ezen szerkezetek létrehozásához. Ez a keresési eredmények között megjelenik a naplórekord előtt kell elvégezni.

Ez a folyamat jelenleg körülbelül 5 percet vesz igénybe esetén adatok mennyisége alacsony, de kevesebb idő magasabb adatok alapján. Ez a megszokottnál counterintuitive, de ez a folyamat lehetővé teszi, hogy a késés nagy mennyiségű éles számítási feladatokra optimalizálása.



## <a name="checking-ingestion-time"></a>Betöltési idő ellenőrzése
Betöltési idő eltérőek lehetnek a különböző körülmények között különböző erőforrásokat. Napló lekérdezések segítségével azonosíthatja a környezet konkrét viselkedését.

### <a name="ingestion-latency-delays"></a>Adatbetöltési késés késések
Egy adott rekord késését eredményét összehasonlításával mérni tudja az [ingestion_time()](/azure/kusto/query/ingestiontimefunction) függvény a _TimeGenerated_ mező. Ezeket az adatokat a különböző összesítések használható található Adatbetöltési késés működését. Vizsgálja meg az egyes PERCENTILIS visszaadása a betöltési időt nyerhet a nagy mennyiségű adat. 

Például a következő lekérdezés bemutatják, hogy mely számítógépek volt a legnagyobb feldolgozási idő az aktuális nap során: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
Ha szeretne részletes elemzést egy időszakon belül egy adott számítógép betöltési időt, használja a következő lekérdezés az adatok egy Graph ábrázoló: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
Számítógép betöltési idő megjelenítése az ország, találhatók az IP-címüket alapuló használja a következő lekérdezést: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
Az ügynök származó különböző adattípusok különböző Adatbetöltési késés idő, előfordulhat, így az előző lekérdezésekhez más típusú is használható. Vizsgálja meg a különböző Azure-szolgáltatások betöltési ideje használja a következő lekérdezést: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Erőforrások, amelyek nem válaszol 
Bizonyos esetekben egy erőforrást sikerült nem küld adatokat. Szeretné megtudni, ha egy erőforrás adatokat küld-e, tekintse meg a legutóbbi rekord, amely segítségével azonosítható a standard _TimeGenerated_ mező.  

Használja a _szívverés_ táblázat, amelyben ellenőrizni egy virtuális gép rendelkezésre állását, mivel szívverést az ügynök által küldött percenként egyszer. Használja a következő lekérdezés az aktív számítógépek, amelyek még nem jelentettek szívverés nemrég listázásához: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>További lépések
* Olvassa el a [szolgáltatói szerződést (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) Azure figyelő.

