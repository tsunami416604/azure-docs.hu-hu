---
title: 1Az adathasználat elemzése a Log Analyticsben | Microsoft Docs
description: A Log Analytics Használat és becsült költségek irányítópultja segítségével kiszámíthatja, hogy mennyi adatot küld a rendszer a Log Analyticsnek, és meghatározhatja a váratlan növekedést okozó tényezőket.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.openlocfilehash: e709f1bed10ee1f7d45bea18b8bdf2c6653be6a6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433981"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Az adathasználat elemzése a Log Analyticsben

> [!NOTE]
> Ez a cikk ismerteti az adathasználat elemzése a Log Analytics.  További kapcsolódó információt a következő cikkekben talál.
> - [Költségek kezelése a Log Analytics és az adatmennyiség szabályozásával](manage-cost-storage.md) azt ismerteti, hogyan tarthatja a költségeket az Adatmegőrzés időtartama módosításával.
> - [Használat és becsült költségek figyelése](../../azure-monitor/platform/usage-estimated-costs.md) ismerteti, hogyan lehet megtekinteni a használati és becsült költségek figyelési funkciók eltérő díjszabási modelleket a több Azure-ban. Emellett bemutatja, hogyan lehet módosítani a díjszabási modellt.

## <a name="understand-usage"></a>Használatelemzés

Használat **Log Analytics-használat és becsült költségek** segítségével áttekintheti és elemezheti az adathasználatot. Az egyes megoldások által összegyűjtött adatok mennyiségét jeleníti meg, mennyi adatot alatt marad, és a költségek becslése betöltött adatok és adatmegőrzés minden csomagban foglalt adatmennyiségen felüli mennyisége alapján.

![Használat és becsült költségek](media/data-usage/usage-estimated-cost-dashboard-01.png)<br>

Ismerje meg részletesen az adatokat, kattintson a felső ikonra vagy a diagramokat, jobb a **használat és becsült költségek** lap. Most már dolgozhat a lekérdezés további részletek, amely a használat.  

![Naplók megtekintése](media/data-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>A vártnál magasabb szintű használatot okozó hibák elhárítása
A magasabb szintű használatot a következők okozhatják:
- A vártnál több adatot küld a rendszer a Log Analytics számára
- Több csomópont, mint a várt adatokat küldő a Log Analytics vagy az egyes csomópontok a szokásosnál több adatot küldenek

Vessünk egy pillantást, hogy hogyan tudjuk többet is megtudhat ezek mindegyikét. 

> [!NOTE]
> Egyes mezőit a használati adattípusú, miközben továbbra is a séma már elavult, és azok értékei már fel van töltve. Ezek a **számítógép** valamint Adatbetöltési kapcsolódó mezőket (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** és **AverageProcessingTimeMs**.
> Lekérdezni egy számítógépen a feldolgozott adatok mennyisége az új módon lentebb. 

### <a name="data-volume"></a>Adatmennyiség 
Az a **használat és becsült költségek** lapon a *adatbetöltés megoldásonként* küldött adatok teljes mennyiségét, és mekkora küld a rendszer egyes megoldások által látható diagramon. Ez lehetővé teszi, hogy határozza meg a trendeket, például hogy a teljes adathasználat (vagy egy adott megoldás használatának) nő, állandó vagy csökken van hátra. Ennek létrehozásához használt lekérdezés

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Vegye figyelembe, hogy a záradék "ahol IsBillable = true" szűri ki az egyes megoldások, amelynek nem jár Adatbetöltési adattípusok. 

Lásd: adatok trendjeit adott adattípusok, például ha az IIS-naplók miatt az adatok tanulmánya szeretné tovább, részletesebben is megtekintheti a:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Adatokat küldő csomópontok

Az elmúlt hónapban adatokat küldő számítógépek (csomópontok) számát tudni használata

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

Megtekintheti a **mérete** számlázható események betöltött számítógépenként, használja a `_BilledSize` -tulajdonsággal, amely biztosítja a mérete (bájt):

`union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last `

Ezen lekérdezések használatához takarékosan, különböző adatok adatok typres keresésekre költséges végrehajtásához. Ez a lekérdezés a lekérdezés ezt a használati adatok típusa a régi módja váltja fel. 

Megtekintheti a **száma** számítógép betöltött események használata

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

A számítógép betöltött számlázható események száma használja 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Ha meg szeretné tekinteni a számlázható adattípusok darabszáma adatot küldenek, adott számítógéphez, használja:

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

Ezután részletesen áttekintjük az adatforrás egy adott típusú, Íme néhány hasznos példa a lekérdezésekre:

+ **Biztonsági** megoldás
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Naplókezelési** megoldás
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** adattípus
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Esemény** adattípus
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Rendszernapló** adattípus
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** adattípus
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Tippek az adatmennyiség csökkentése

Néhány javaslatot a gyűjtött naplók mennyiségét csökkenti a következők:

| A nagy adatmennyiség forrása | Az adatmennyiség csökkentésének módja |
| -------------------------- | ------------------------- |
| Biztonsági események            | Válassza a [gyakori vagy minimális biztonsági események](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) lehetőséget <br> Módosítsa a biztonsági naplózási szabályzatot, hogy csak a szükséges eseményeket gyűjtse be. Tekintse át a következőkhöz való eseménygyűjtés szükségességét: <br> - [szűrőplatform naplózása](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [beállításjegyzék naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [fájlrendszer naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [kernelobjektum naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [leírókezelés naplózása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – Cserélhető tároló naplózása |
| Teljesítményszámlálók       | Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md): <br> – Csökkentse a gyűjtés gyakoriságát <br> – Csökkentse a teljesítményszámlálók számát |
| Eseménynaplók                 | Módosítsa az [eseménynaplók konfigurációját](data-sources-windows-events.md): <br> – Csökkentse a gyűjtött eseménynaplók számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* szintű eseményeket |
| Rendszernapló                     | Módosítsa a [rendszernapló konfigurációját](data-sources-syslog.md): <br> – Csökkentse a gyűjtésben részt vevő létesítmények számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* vagy *Hibakeresés* szintű eseményeket |
| AzureDiagnostics           | Az erőforrásnapló-gyűjtés módosítása a következőre: <br> – Csökkentse a Log Analytics számára naplókat küldő erőforrások számát <br> – Csak a szükséges naplókat gyűjtse |
| Megoldásadatok olyan számítógépekről, amelyeknek nincs szükségük a megoldásra | A [megoldáscélzási](../../azure-monitor/insights/solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat. |

### <a name="getting-node-counts"></a>Első csomópont hibás 

Ha a "Száma csomópontonként (OMS)" tarifacsomag, akkor számítunk fel a csomópontok és megoldások száma alapján használja, a Insights számát, valamint a tábla, amelynek, számolunk fel az Analytics-csomópontok megjelennek a a **használat és becsült költségek**lapot.  

Tekintse meg a különböző biztonsági csomópontok számát, a lekérdezés használhatja:

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Különböző automatizálási csomópontok számának megtekintéséhez használja a lekérdezés:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Riasztás létrehozása, amikor az adatgyűjtés szintje a vártnál magasabb
Ez a szakasz ismerteti, hogyan hozhat létre riasztást, ha:
- Az adatmennyiség meghalad egy megadott mennyiséget.
- Az adatmennyiség várhatóan meghalad egy megadott mennyiséget.

Az Azure-riasztások támogatják a keresési lekérdezéseket támogató [naplóriasztásokat](../../azure-monitor/platform/alerts-unified-log.md). 

A következő lekérdezés akkor ad vissza eredményt, ha több mint 100 GB adat lett összegyűjtve az elmúlt 24 órában:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

A következő lekérdezés egy egyszerű képlettel előrejelzi, mikor fog a rendszer egy nap alatt több mint 100 GB adatot küldeni: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Ha más adatmennyiségre szeretne riasztást beállítani, módosítsa a lekérdezésekben a 100 értéket arra a GB mennyiségre, amely esetén riasztást szeretne kapni.

Az [új naplózási riasztás létrehozásával kapcsolatos](../../azure-monitor/platform/alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha az adatgyűjtés szintje a vártnál magasabb.

Az első lekérdezéshez tartozó riasztás létrehozásakor – amikor több mint 100 GB adat lett összegyűjtve 24 órán belül, állítsa be a következőket:  

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - A **Jel neve** legyen **Egyéni naplókeresés**
   - A **Keresési lekérdezés** legyen a következő: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - A **Riasztási logika** **alapja** legyen az *eredmények száma*, a **Feltétel** pedig legyen *nagyobb mint* a következő **küszöbérték** : *0*
   - Az **Időszak** értékét állítsa *1440* percre, a **Riasztási időköz** pedig legyen *60* perc, mivel a használati adatok csak óránként frissülnek.
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - A **Név** legyen *Több mint 100 GB adatmennyiség 24 órán belül*
   - A **Súlyosság** legyen *Figyelmeztetés*

Megadhat egy meglévő [műveletcsoportot](../../azure-monitor/platform/action-groups.md), illetve létrehozhat egy újat, hogy értesítést kapjon, amikor egy naplóriasztás megfelel a feltételeknek.

A második lekérdezéshez tartozó riasztás létrehozásakor – amikor több mint 100 GB adat összegyűjtése várható 24 órán belül, állítsa be a következőket:

- A **riasztási feltétel megadásával** határozza meg a célerőforrásként használt Log Analytics-munkaterületet.
- A **Riasztási feltételek** résznél az alábbiakat adja meg:
   - A **Jel neve** legyen **Egyéni naplókeresés**
   - A **Keresési lekérdezés** legyen a következő: `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - A **Riasztási logika** **alapja** legyen az *eredmények száma*, a **Feltétel** pedig legyen *nagyobb mint* a következő **küszöbérték** : *0*
   - Az **Időszak** értékét állítsa *180* percre, a **Riasztási időköz** pedig legyen *60* perc, mivel a használati adatok csak óránként frissülnek.
- **Határozza meg a riasztás részleteit** az alábbiak megadásával:
   - A **Név** legyen *Több mint 100 GB várható adatmennyiség 24 órán belül*
   - A **Súlyosság** legyen *Figyelmeztetés*

Megadhat egy meglévő [műveletcsoportot](../../azure-monitor/platform/action-groups.md), illetve létrehozhat egy újat, hogy értesítést kapjon, amikor egy naplóriasztás megfelel a feltételeknek.

Riasztás fogadásakor kövesse a következő szakaszban leírt lépéseket a vártnál magasabb szintű használatot okozó hibák elhárításához.

## <a name="next-steps"></a>További lépések
* A keresési nyelv használatával kapcsolatban tekintse meg a [Log Analytics naplókeresési funkciójával](../log-query/log-query-overview.md) kapcsolatos cikket. A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
* Az [új naplózási riasztás létrehozásával kapcsolatos](../../azure-monitor/platform/alerts-metric.md) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel.
* A [megoldáscélzási](../insights/solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat.
* Hatékony biztonságiesemény-gyűjtési szabályzat konfigurálásához tekintse meg az [Azure Security Center szűrési szabályzataival](../../security-center/security-center-enable-data-collection.md) foglalkozó cikket.
* Módosítsa a [teljesítményszámlálók konfigurációját](data-sources-performance-counters.md).
* Az eseménygyűjtési beállítások módosításához tekintse meg az [eseménynaplók konfigurációját](data-sources-windows-events.md) leíró szakaszt.
* A rendszernapló-gyűjtési beállítások módosításához tekintse meg a [rendszernaplók konfigurációját](data-sources-syslog.md) leíró szakaszt.
