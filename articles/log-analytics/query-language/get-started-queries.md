---
title: Lekérdezések az Azure Log Analytics használatának első lépései |} A Microsoft Docs
description: Ez a cikk egy oktatóanyag, az első lépésekhez írása lekérdezések a Log Analyticsben.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 71d50a55d9c584b61a1412bb03a03ad99f1bb96c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39634646"
---
# <a name="get-started-with-queries-in-log-analytics"></a>A Log Analytics lekérdezések használatának első lépései


> [!NOTE]
> Hajtsa végre [az Analytics-portál – első lépések](get-started-analytics-portal.md) Ez az oktatóanyag elvégzése előtt.


Ebben az oktatóanyagban elsajátíthatja az Azure Log Analytics-lekérdezések írása. Mely arról szól, hogyan való:

- A lekérdezések struktúra ismertetése
- Lekérdezési eredmények rendezése
- Szűrő lekérdezés eredményei
- Adjon meg egy időtartományt
- Válassza ki a mezőket a keresési eredmények között szerepeljen
- Definiálja és egyéni mezők
- Összesítő és csoportosítási eredmények


## <a name="writing-a-new-query"></a>Új lekérdezés írása
Lekérdezések megkezdheti a következők egyikével táblanév vagy a *keresési* parancsot. A tábla nevét, mert a lekérdezés törlése hatókör határozza meg, és javítja a lekérdezési teljesítmény és az eredmények relevancia alapján végzett kell kezdődnie.

> [!NOTE]
> Az Azure Log Analytics lekérdezési nyelve, kis-és nagybetűket. Vezérlőnyelvi kulcsszavak általában nyelven írták kisbetűs. Táblák vagy oszlopok nevei a lekérdezésben használatakor ügyeljen arra, hogy a megfelelő kis-és nagybetűhasználattal a séma ablaktáblán látható módon.

### <a name="table-based-queries"></a>Tábla-alapú lekérdezések futtatása
Az Azure Log Analytics rendezik az adatokat táblákban, minden egyes tevődik össze több oszlopot. A séma ablaktáblán az analitika portálon minden táblák és oszlopok jelennek meg. Határozza meg, hogy érdeklő, és ezután tekintse meg az adatok egy kis, tábla:

```OQL
SecurityEvent
| take 10
```

A fenti lekérdezés 10 eredményét adja vissza a *SecurityEvent* táblához nincs konkrét sorrendje. Ez nagyon gyakori módja egy tábla egy termékkereslet és megérteni a felépítéséről és tartalmáról. Most vizsgálja meg, hogyan épül:

* A lekérdezés a táblanév előtaggal kezdődik *SecurityEvent* – Ez a rész határozza meg a lekérdezés hatókörét.
* A függőleges vonal (|) karakterrel elválasztó parancsok, így az elsőt a bemenetben a következő parancs kimenetét. Tetszőleges számú védőeszközön elemeket adhat hozzá.
* A következő függőleges vonal van a **igénybe** parancsot, amely egy tetszőleges rekordok adott számát adja vissza a táblából.

Hogy ténylegesen futhat a lekérdezés hozzáadása nélkül is `| take 10` –, amelyek továbbra is lenne érvényes, de sikerült legfeljebb 10 000 eredményeket.

### <a name="search-queries"></a>Keresési lekérdezések
Keresési lekérdezések a következők: kisebb strukturált, és általában több olyan bármely, az oszlopok egy adott értéket tartalmazó rekordok keresése:

```OQL
search in (SecurityEvent) "Cryptographic"
| take 10
```

Ez a lekérdezés átvizsgálja a *SecurityEvent* rekordok a "Titkosítási" kifejezést tartalmazó tábla. Ezeknek a rekordoknak 10 rekordot fog visszaadott és jelenik meg. Ha kihagyja azt a `in (SecurityEvent)` futtatásának részeként, és csak `search "Cryptographic"`, a keresés halad *összes* táblát is, amelyeket szeretne hosszabb időt vesz igénybe, és kevésbé hatékony.

> [!NOTE]
> Alapértelmezésben a időtartománya _elmúlt 24 órában_ van beállítva. Egy másik tartományt használja az időválasztó (melletti a *lépjen* gomb), vagy adja hozzá egy kifejezett idő dátumtartomány-szűrőt ad a lekérdezéshez.

## <a name="sort-and-top"></a>Rendezés és a leggyakoribb
Miközben **igénybe** van hasznos néhány rögzíti, az eredmények kiválasztva, és nem adott sorrendben jelennek meg. Egy rendezett nézet lekéréséhez sikerült **rendezési** az előnyben részesített oszlop szerint:

```
SecurityEvent   
| sort by TimeGenerated desc
```

Amely túl sok eredményt, ha vissza, és időbe is telhet. A fenti lekérdezés rendezi *a teljes* SecurityEvent tábla a TimeGenerated oszlop szerint. Az Analytics-portál majd korlátozza csak 10 000 rekordot megjelenítése. Ez a megközelítés természetesen nem áll optimális.

Csak a legújabb 10 rekord lekérése a legjobb módszer az, hogy használja **felső**, amely a kiszolgálói oldalon az egész tábla rendezi, és a felső rekordjait adja majd vissza:

```OQL
SecurityEvent
| top 10 by TimeGenerated
```

Csökkenő sorrendbe az alapértelmezett rendezési sorrend, így általában kihagyja a **desc** argumentum. A kimenet a következőképpen jelenik meg:

![Első 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Ahol: szűrési feltétel
Szűrők, a nevük, aszinkronitást szűrje az adatokat egy adott feltétel. Ez a leggyakoribb módja vonatkozó információkat a lekérdezési eredmények korlátozására.

Szűrő hozzáadása egy lekérdezés, használja a **ahol** operátor egy vagy több feltételt követ. Például a következő lekérdezés visszaadja az csak *SecurityEvent* rekordokat, ahol _szint_ egyenlő _8_:

```OQL
SecurityEvent
| where Level == 8
```

Szűrési feltételek írásakor, használhatja az alábbi kifejezések:

| Kifejezés | Leírás | Példa |
|:---|:---|:---|
| == | Egyenlőség ellenőrzése<br>(kis-és nagybetűket) | `Level == 8` |
| =~ | Egyenlőség ellenőrzése<br>(kis-és nagybetűket) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| ! =, <> | Egyenlótlenség ellenőrzése<br>(a mindkét kifejezés azonosak) | `Level != 4` |
| *és*, *vagy* | Szükséges feltételek között| `Level == 16 or CommandLine != ""` |

Szűrés több feltétel alapján, hogy használhatja **és**:

```OQL
SecurityEvent
| where Level == 8 and EventID == 4672
```

vagy több kanálu **ahol** elemek egy egymás után:

```OQL
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Értékek lehetnek a különböző típusú, hogy a leadott őket a megfelelő típusú összehasonlítás végrehajtásához szükség lehet. Például SecurityEvent *szint* sloupec je typu karakterlánc, így kell alakítania azt egy numerikus típus, például *int* vagy *hosszú*, a numerikus operátorok használata előtt: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Adjon meg egy időtartományt

### <a name="time-picker"></a>Időpontválasztó
A bal felső sarokban, ami azt jelzi, hogy csak az elmúlt 24 órában a rekordok lekérdezésekor a időválasztó van. Ez az összes lekérdezés alkalmazott alapértelmezett időintervallumát. Az elmúlt egy órában csak a rekordok lekéréséhez válassza _utolsó óra_ , és futtassa újra a lekérdezést.

![Időválasztó](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>A lekérdezés Időszűrő
A saját időtartomány megadhatók a lekérdezést ad hozzá egy Időszűrő is. Célszerű elhelyezni az Időszűrő közvetlenül után a táblázat neve: 

```OQL
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Az a fenti Időszűrő `ago(30m)` "30 perccel ezelőtt" azt jelenti, hogy ez a lekérdezés csak vissza rekordok az elmúlt 30 percben. Egyéb időegységben tartalmaznia nap (2d), perc (25m) és a másodperc (10 egység).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt és bővítés: válassza ki, és a számítási oszlopok
Használat **projekt** a keresési eredmények között szerepeljen az egyes oszlopok kiválasztásához:

```OQL
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Az előző példában állít elő, ez a kimenet:

![Log Analytics projekt eredmények](media/get-started-queries/project.png)

Is **projekt** oszlopok átnevezése, illetve újakat megadása. A következő példában projekt tegye a következőket:

* Csak válassza ki a *számítógép* és *TimeGenerated* eredeti oszlopot.
* Nevezze át a *tevékenység* oszlop *EventDetails*.
* Hozzon létre egy olyan új oszlop neve *EventCode*. A **substring()** függvény csak az első négy karaktert beszerezni a tevékenység mező szolgál.


```OQL
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**kiterjesztheti** összes eredeti oszlopot tartja az eredményhalmaz és újak határozza meg. Az alábbi lekérdezés használ **kiterjesztése** hozzáadása egy *konvertálásának* oszlopot, amely honosított TimeGenerated értéket tartalmaz.

```OQL
SecurityEvent
| top 10 by TimeGenerated
| extend localtime = TimeGenerated-8h
```

## <a name="summarize-aggregate-groups-of-rows"></a>Összefoglalásképpen: összesített sorcsoportra
Használat **összefoglalója** azonosíthatja a csoportok a rekordok, egy vagy több oszlop szerint, és összesítések vonatkoznak rájuk. A leggyakoribb használja az operációs rendszer **összefoglalója** van *száma*, amely eredmények számát adja vissza az egyes csoportokban.

A következő lekérdezés az összes felülvizsgálati *Teljesítményoptimalizált* az elmúlt órában származó rekordokat csoportok szerint *ObjectName*, és az egyes csoportokban a rekordok száma: 
```OQL
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Néha logikus csoportjait több szempontok alapján határozzák meg. Ezeket az értékeket minden egyéni kombinációja külön csoportot határozza meg:

```OQL
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Egy másik általában az egyes csoportok matematikai és statisztikai számításokhoz. Például az a következő kiszámítja az átlagos *AVG* az egyes számítógépekhez:

```OQL
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Sajnos ez a lekérdezés eredményeit, mivel azt összekeverni különböző teljesítményszámlálók, összegnek nincs valós jelentése. Ahhoz, hogy ez jobban leírja, külön-külön az egyes kombinációja átlagos számítható ki *CounterName* és *számítógép*:

```OQL
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Összegzés szempontja-idő típusú oszlop
Eredmények csoportosítása is alapulhat-idő típusú oszlop, vagy egy másik folyamatos értéket. Egyszerűen összefoglalójához `by TimeGenerated` azonban lenne csoportok létrehozása a minden egyetlen ezredmásodperces az időtartományban, hiszen ezek egyedi értékeket. 

Folyamatos értékek alapján csoportok létrehozásához, célszerű a tartomány megszüntetése használatával kezelhető egységekbe **bin**. A következő lekérdezés elemzi *Teljesítményoptimalizált* rekordokat, amelyek a szabad memória (*rendelkezésre álló memória*) egy adott számítógépen. Mindig kiszámítja az átlagos érték minden egyes időtartam Ha 1 óra, az elmúlt 2 napban:

```OQL
Perf 
| where TimeGenerated > ago(2d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize count() by bin(TimeGenerated, 1h)
```

Ahhoz, hogy világosabb kimeneti, választja egy idő-diagramon megjeleníthető a rendelkezésre álló memória megjelenítő idővel:

![Log Analytics memória időbeli alakulása](media/get-started-queries/chart.png)



## <a name="next-steps"></a>További lépések

- Ismerje meg [keresési lekérdezések írása](search-queries.md)