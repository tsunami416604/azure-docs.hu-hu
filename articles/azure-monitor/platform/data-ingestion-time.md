---
title: Adatbetöltési idő naplózása az Azure Monitorban | Microsoft dokumentumok
description: Bemutatja a különböző tényezőket, amelyek befolyásolják a késést az Azure Monitor naplóadatainak gyűjtése során.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 99d5594dd3ebe3750cb0a09ea803065e2aeb5ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666637"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Naplóadatok feldolgozási ideje az Azure Monitorban
Az Azure Monitor egy nagy méretű adatszolgáltatás, amely több ezer ügyfelet szolgál ki, akik havonta több terabájtnyi adatot küldenek növekvő ütemben. Gyakran vannak kérdések arról, hogy a naplóadatok az adatgyűjtés után milyen ideig érhetők el. Ez a cikk ismerteti a különböző tényezők, amelyek befolyásolják ezt a késést.

## <a name="typical-latency"></a>Tipikus késleltetés
Késés utal, hogy az adatok létre jönnek a figyelt rendszeren, és az idő, hogy elérhetővé válik az Azure Monitor elemzésre. A naplóadatok betöltésének tipikus késése 2 és 5 perc között van. Az adott adatok specifikus késése az alábbiakban ismertetett tényezőktől függően változhat.


## <a name="factors-affecting-latency"></a>Késést befolyásoló tényezők
Egy adott adathalmaz teljes betöltési ideje a következő magas szintű területekre bontható. 

- Ügynök ideje – Az idő egy esemény felderítéséhez, összegyűjtéséhez, majd küldje el az Azure Monitor betöltési pont, mint egy naplórekord. A legtöbb esetben ezt a folyamatot egy ügynök kezeli.
- Folyamatidő – Az az idő, amely alatt a feldolgozási folyamat feldolgozza a naplóbejegyzést. Ez magában foglalja az esemény tulajdonságainak elemzését és a számított adatok potenciális hozzáadását.
- Indexelési idő – A naplórekord Azure Monitor big data tárolóba való betöltésére fordított idő.

A folyamat során bevezetett különböző késés részletei az alábbiakban olvashatók.

### <a name="agent-collection-latency"></a>Az ügynökgyűjtemény késése
Az ügynökök és a felügyeleti megoldások különböző stratégiákat használnak a virtuális gépekről történő adatgyűjtéshez, ami hatással lehet a késésre. Néhány konkrét példa a következők:

- A Windows-események, a syslog-események és a teljesítménymutatók gyűjtése azonnal történik. A Linux teljesítményszámlálók lekérdezése 30 másodperces időközönként történik.
- Az IIS-naplók és az egyéni naplók az időbélyeg ük változása után kerülnek összegyűjtésre. Az IIS-naplók esetében ezt befolyásolja az [IIS-ben konfigurált átütemezés.](data-sources-iis-logs.md) 
- Az Active Directory replikációs megoldás ötnaponta végzi el az értékelést, míg az Active Directory-felmérési megoldás heti rendszerességgel értékeli az Active Directory-infrastruktúrát. Az ügynök csak akkor gyűjti ezeket a naplókat, ha az értékelés befejeződött.

### <a name="agent-upload-frequency"></a>Az ügynök feltöltési gyakorisága
Annak érdekében, hogy a Log Analytics-ügynök könnyű, az ügynök pufferek naplók és rendszeres időközönként feltölti őket az Azure Monitor. A feltöltés gyakorisága az adatok típusától függően 30 másodperc és 2 perc között változik. A legtöbb adat feltöltése 1 perc alatt történik. A hálózati feltételek negatívan befolyásolhatják ezeknek az adatoknak a késését az Azure Monitor betöltési pontjának eléréséhez.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Azure-tevékenységnaplók, erőforrásnaplók és metrikák
Az Azure-adatok további időt adnak a Log Analytics feldolgozási pontján való elérhetővé váláshoz:

- Az erőforrásnaplókból származó adatok az Azure szolgáltatástól függően 2–15 percet vesznek igénybe. Tekintse meg az [alábbi lekérdezést,](#checking-ingestion-time) hogy megvizsgálja ezt a késést a környezetben
- Az Azure platform metrikák 3 percet vesz igénybe a Log Analytics betöltési pont.
- A tevékenységnapló adatai körülbelül 10–15 percet vesznek igénybe a Log Analytics betöltési pontjára való elküldéshez.

Egyszer elérhető -on betöltés pont, adat fog további 2-5 jegyzőkönyv -hoz lenni elérhető részére kérdezés.

### <a name="management-solutions-collection"></a>Felügyeleti megoldások gyűjteménye
Egyes megoldások nem gyűjtik az adataikat egy ügynöktől, és további késést bevezető adatgyűjtési módszert használhatnak. Egyes megoldások rendszeres időközönként gyűjtenek adatokat anélkül, hogy közel valós idejű adatgyűjtést kísérelne meg. Konkrét példák a következők:

- Az Office 365-megoldás lekérdezi a tevékenységnaplókat az Office 365 Felügyeleti tevékenység API használatával, amely jelenleg nem nyújt közel valós idejű késési garanciát.
- A Megoldás naponta gyűjti a Windows Analytics-megoldásokat (Megfelelőség frissítése) a megoldás.

A gyűjtésgyakoriságának meghatározásához tekintse meg az egyes megoldások dokumentációját.

### <a name="pipeline-process-time"></a>Folyamat-feldolgozási idő
Miután a naplórekordok at az Azure Monitor-folyamatba (a _TimeReceived tulajdonságban azonosított) a [rendszer](log-standard-properties.md#_timereceived) ideiglenes tárolóba írja őket, hogy biztosítsa a bérlők elkülönítését, és győződjön meg arról, hogy az adatok nem vesznek el. Ez a folyamat általában hozzáad 5-15 másodpercet. Egyes felügyeleti megoldások nehezebb algoritmusokat valósítanak meg az adatok összesítéséhez és az adatok streamelése kori elemzési adatok levezetéséhez. A hálózati teljesítményfigyelés például 3 perces időközönként összesíti a bejövő adatokat, így gyakorlatilag 3 perces késést ad hozzá. Egy másik folyamat, amely hozzáadja a késést az egyéni naplókat kezelő folyamat. Bizonyos esetekben ez a folyamat néhány perc késést adhat az ügynök által a fájlokból gyűjtött naplókhoz.

### <a name="new-custom-data-types-provisioning"></a>Új egyéni adattípusok kiépítése
Ha egy új típusú egyéni adatok jönnek létre egy [egyéni napló](data-sources-custom-logs.md) vagy az [adatgyűjtő API,a](data-collector-api.md)rendszer létrehoz egy dedikált tárolótároló. Ez egy egyszeri többletterhelés, amely csak az adattípus első megjelenésekor fordul elő.

### <a name="surge-protection"></a>Túlfeszültség elleni védelem
Az Azure Monitor legfontosabb prioritása annak biztosítása, hogy ne vesszenek el az ügyféladatok, így a rendszer beépített adatvédelmet biztosít az adathullámok számára. Ez magában foglalja a pufferek annak biztosítása érdekében, hogy még hatalmas terhelés alatt, a rendszer folyamatosan működik. Normál terhelés esetén ezek a vezérlők kevesebb mint egy percet adnak hozzá, de szélsőséges körülmények és hibák esetén jelentős időt adhatnak hozzá, miközben biztosítják az adatok biztonságát.

### <a name="indexing-time"></a>Indexelési idő
Minden big data-platformhoz beépített egyensúly áll rendelkezésre az elemzésés a speciális keresési képességek között, szemben az adatokhoz való azonnali hozzáféréssel. Az Azure Monitor lehetővé teszi, hogy hatékony lekérdezéseket futtasson több milliárd rekordon, és néhány másodpercen belül eredményeket érjen el. Ez azért lehetséges, mert az infrastruktúra a betöltés során drasztikusan átalakítja az adatokat, és egyedi kompakt szerkezetekben tárolja azokat. A rendszer addig puffereli az adatokat, amíg elegendő rendelkezésükre nem áll ezeknek a struktúráknak a létrehozásához. Ezt ki kell tölteni, mielőtt a naplórekord megjelenik a keresési eredmények között.

Ez a folyamat jelenleg körülbelül 5 percet vesz igénybe, ha kevés az adatmennyiség, de kisebb idő magasabb adatátviteli sebesség mellett. Ez úgy tűnik, counterintuitive, de ez a folyamat lehetővé teszi a késés optimalizálása a nagy mennyiségű termelési számítási feladatok.



## <a name="checking-ingestion-time"></a>Betöltési idő ellenőrzése
A betöltési idő eltérő lehet a különböző erőforrások esetében, különböző körülmények között. A naplólekérdezések segítségével azonosíthatja a környezet adott viselkedését. Az alábbi táblázat bemutatja, hogyan határozhatja meg a különböző időpontokban egy rekord létrehozása és küldése az Azure Monitor.

| Lépés | Tulajdonság vagy függvény | Megjegyzések |
|:---|:---|:---|
| Adatforrásban létrehozott rekord | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>Ha az adatforrás nem állítja be ezt az értéket, akkor a _TimeReceived. |
| Az Azure Monitor betöltési végpontja által fogadott rekord | [_TimeReceived](log-standard-properties.md#_timereceived) | |
| A munkaterületen tárolt és lekérdezésekhez elérhető rekord | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Benyelési késés ek
Egy adott rekord késésének mérése a [ingestion_time()](/azure/kusto/query/ingestiontimefunction) függvény és a _TimeGenerated_ tulajdonság eredményének összehasonlításával. Ezek az adatok különböző összesítésekkel használhatók a betöltési késés működésének megkereséséhez. Vizsgálja meg a betöltési idő néhány percentilisét, hogy nagy mennyiségű adatot megkaphassa. 

A következő lekérdezés például megmutatja, hogy mely számítógépeknek volt a legmagasabb a betöltési ideje az előző 8 órában: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Az előző percentilis ellenőrzések jó a késés általános tendenciáinak megtalálásához. A késés rövid távú csúcsértékének azonosítása, a maximális (`max()`) használata hatékonyabb lehet.

Ha egy adott számítógép betöltési idejét szeretné egy adott időszakban lefúrni, használja a következő lekérdezést, amely az elmúlt nap adatait is megjeleníti egy grafikonon: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
A következő lekérdezéssel megjelenítheti a számítógép betöltési idejét azon ország/régió szerint, amelyben az IP-címük alapján találhatók: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Az ügynöktől származó különböző adattípusok eltérő betöltési késési idővel rendelkezhetnek, így az előző lekérdezések más típusokkal is használhatók. A következő lekérdezés segítségével vizsgálja meg a különböző Azure-szolgáltatások betöltési idejét: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Nem válaszoló erőforrások 
Bizonyos esetekben egy erőforrás leállíthatja az adatok küldését. Annak megértéséhez, hogy egy erőforrás küld-e adatokat vagy sem, tekintse meg a legutóbbi rekordját, amelyet a szabványos _TimeGenerated_ mező azonosíthat.  

A _Szívverés_ tábla segítségével ellenőrizze a virtuális gép rendelkezésre állását, mivel az ügynök percenként egyszer szívverést küld. Az alábbi lekérdezéssel sorolja fel azokat az aktív számítógépeket, amelyek nem jelentettek szívverést a közelmúltban: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>További lépések
* Olvassa el az Azure Monitor [szolgáltatásiszint-szerződését (SLA).](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/)

