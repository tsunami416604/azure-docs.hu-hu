---
title: A naplólekérdezések első lépései az Azure Monitorban | Microsoft dokumentumok
description: Ez a cikk egy oktatóanyagot tartalmaz a naplólekérdezések írásának megkezdéséhez az Azure Monitorban.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: f56abe2bf6ccea1f55f9b3fe94b75016d449b46b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77670179"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>A naplólekérdezések első lépései az Azure Monitorban

> [!NOTE]
> Ezt a gyakorlatot a saját környezetében is elvégezheti, ha legalább egy virtuális gépről gyűjt adatokat. Ha nem, akkor használja [a Demo környezetben](https://portal.loganalytics.io/demo), amely magában foglalja a sok minta adatokat.

Ebben az oktatóanyagban megtudhatja, hogyan írhat naplólekérdezéseket az Azure Monitorban. Ez megtanít, hogyan kell:

- A lekérdezés szerkezetének ismertetése
- Lekérdezéseredményének rendezése
- Lekérdezési eredmények szűrése
- Időtartomány megadása
- Az eredményekben szerepeljen mezők kiválasztása
- Egyéni mezők definiálása és használata
- Összesített és csoportos eredmények

A Log Analytics Azure Portalon való használatáról az [Azure Monitor Log Analytics használatának első lépései](get-started-portal.md)című témakörben található.<br>
Az Azure Monitor naplólekérdezéseiről az [Azure Monitor naplólekérdezéseinek áttekintése című témakörben](log-query-overview.md)olvashat bővebben.

Kövesse együtt egy videó változata a bemutató az alábbi:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Új lekérdezés írása
A lekérdezések egy táblanévvel vagy a *keresési* paranccsal kezdődhetnek. A táblanévvel kell kezdeni, mivel egyértelmű hatókört határoz meg a lekérdezéshez, és javítja a lekérdezés teljesítményét és az eredmények relevanciáját.

> [!NOTE]
> Az Azure Monitor által használt Kusto-lekérdezési nyelv megkülönbözteti a kis- és nagybetűket. A nyelvi kulcsszavakat általában kisbetűvel kell írni. Ha táblák vagy oszlopok nevét használja egy lekérdezésben, ügyeljen arra, hogy a megfelelő kis- és nagybetűket használja, ahogy az a sémaablaktáblán látható.

### <a name="table-based-queries"></a>Táblaalapú lekérdezések
Az Azure Monitor a naplóadatokat táblákba rendezi, amelyek mindegyike több oszlopból áll. Az összes tábla és oszlop megjelenik a séma ablaktáblán a Log Analytics az Analytics portálon. Azonosítsa az Önt érdeklő táblát, majd tekintse meg az adatok at:

```Kusto
SecurityEvent
| take 10
```

A fent látható lekérdezés 10 eredményt ad vissza a *SecurityEvent* táblából, nem adott sorrendben. Ez egy nagyon gyakori módja annak, hogy egy pillantást egy asztalra, és megérteni annak szerkezetét és tartalmát. Vizsgáljuk meg, hogyan épül fel:

* A lekérdezés a *SecurityEvent* táblanévvel kezdődik - ez a rész határozza meg a lekérdezés hatókörét.
* A cső (|) karakter elválasztja a parancsokat, így a következő parancs bemenetén szereplő első kimenete. Tetszőleges számú vezetékes elemet adhat hozzá.
* A cső után a **take** parancs, amely a táblából tetszőleges számú tetszőleges rekordot ad vissza.

A lekérdezést még anélkül `| take 10` is futtathatnánk, hogy hozzátennénk - ez még mindig érvényes lenne, de akár 10 000 eredményt is visszaadhat.

### <a name="search-queries"></a>Keresési lekérdezések
A keresési lekérdezések kevésbé strukturáltak, és általában jobban megfelelnek azoknak a rekordoknak a keresésére, amelyek egy adott értéket tartalmaznak valamelyik oszlopukban:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Ez a lekérdezés a *SecurityEvent* táblában olyan rekordokat keres, amelyek a "Kriptográfia" kifejezést tartalmazzák. Ezek közül a rekordok közül 10 rekordot ad vissza és jelenít meg. Ha kihagyjuk `in (SecurityEvent)` az alkatrészt, és csak futunk, `search "Cryptographic"`a keresés az *összes* asztalon átmegy, ami hosszabb időt vesz igénybe és kevésbé hatékony.

> [!WARNING]
> A keresési lekérdezések általában lassabbak, mint a táblaalapú lekérdezések, mivel több adatot kell feldolgozniuk. 

## <a name="sort-and-top"></a>Rendezés és felül
Míg **a take** hasznos, hogy néhány rekordot, az eredmények kiválasztása és megjelenítése nem adott sorrendben. Rendezett nézet bekerüléséhez az előnyben részesített oszlop szerint **rendezheti** a következőket:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Amit tudna visszatér túl sok eredmények mégis és erő is fog némely idő. A fenti lekérdezés *a teljes* SecurityEvent táblát a TimeGenerated oszlop szerint rendezi. Az Analytics portál ezután korlátozza a megjelenítést, hogy csak 10 000 rekord jelenjen meg. Ez a megközelítés természetesen nem optimális.

A legjobb módja annak, hogy csak a legújabb 10 rekordot, hogy használja **top**, amely rendezi a teljes táblázatot a szerver oldalon, majd visszatér a felső rekordok:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

A csökkenő az alapértelmezett rendezési sorrend, ezért általában kihagyjuk a **desc argumentumot.** A kimenet így fog kinézni:

![Top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Hol: szűrés egy feltétellel
A szűrők a nevük alapján szűrik az adatokat egy adott feltétel szerint. Ez a leggyakoribb módja annak, hogy a lekérdezési eredményeket a releváns információkra korlátozza.

Ha szűrőt szeretne hozzáadni egy lekérdezéshez, használja azt a **helyoperátort,** amelyet egy vagy több feltétel követ. A következő lekérdezés például csak a *SecurityEvent* rekordokat adja vissza, amelyek _szintje_ _8:_

```Kusto
SecurityEvent
| where Level == 8
```

Szűrőfeltételek írásakor a következő kifejezéseket használhatja:

| Kifejezés | Leírás | Példa |
|:---|:---|:---|
| == | Egyenlőség ellenőrzése<br>(kis- és nagybetűk megkülönböztetése) | `Level == 8` |
| =~ | Egyenlőség ellenőrzése<br>(kis- és nagybetű nem érzékeny) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Egyenlőtlenség ellenőrzése<br>(mindkét kifejezés azonos) | `Level != 4` |
| *és,* *vagy* | Feltételek között kötelező| `Level == 16 or CommandLine != ""` |

Ha több feltétel szerint szeretne szűrni, használja **a következőket:**

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

vagy cső **többszörös, ahol** az elemek egymás után:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Az értékek különböző típusúak lehetnek, ezért előfordulhat, hogy a megfelelő típus összehasonlításához le kell adnia őket. A SecurityEvent *Level* oszlop például Karakterlánc típusú, ezért numerikus típusra ( *int* vagy *long)* kell vetni, mielőtt numerikus operátorokat használhatna rajta:`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Időtartomány megadása

### <a name="time-picker"></a>Időválasztó
Az időválasztó a Futtatás gomb mellett található, és azt jelzi, hogy csak az elmúlt 24 óra rekordjait kérdezzük le. Ez az összes lekérdezésre alkalmazott alapértelmezett időtartomány. Ha csak az elmúlt órából származó rekordokat szeretné leadni, válassza az _Utolsó óra_ lehetőséget, és futtassa újra a lekérdezést.

![Időválasztó](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Időszűrő a lekérdezésben
A saját időtartományt időszűrő hozzáadásával is megadhatja a lekérdezéshez. A legjobb, ha az időszűrőt közvetlenül a tábla neve után helyezi el: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

A fenti időszűrő `ago(30m)` azt jelenti, "30 perccel ezelőtt", így ez a lekérdezés csak az utolsó 30 perc rekordjait adja vissza. Egyéb időegységek közé tartozik a nap (2d), perc (25m) és másodperc (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt és kiterjesztés: oszlopok kijelölése és számítása
A **projekt** segítségével válassza ki az eredményekben szerepelni kívánt oszlopokat:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Az előző példa ezt a kimenetet hozza létre:

![Projekteredmények lekérdezése](media/get-started-queries/project.png)

A **projekt** segítségével oszlopokat is átnevezhet, és újakat definiálhat. A következő példa a következő projektet használja:

* Csak a *Számítógép* és *az Időgenerált* eredeti oszlopokat jelölje ki.
* Nevezze át a *Tevékenység* oszlopot *EventDetails*névre.
* Hozzon létre egy *Új oszlopot EventCode*néven. A **részkarakterláncok()** függvény csak a Tevékenység mező első négy karakterének bekésésére szolgál.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**a extend** az összes eredeti oszlopot megtartja az eredményhalmazban, és továbbioszlopokat határoz meg. A következő lekérdezés **kiterjesztése** segítségével adja hozzá az *EventCode* oszlopot. Ne feledje, hogy ez az oszlop nem jelenik meg a táblázat végén, amely esetben ki kell bontania a rekord részleteit a megtekintéshez.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Összegzés: sorcsoportok összesítése
Az **összegzés** segítségével egy vagy több oszlop szerint azonosíthatja a rekordcsoportokat, és összesítéseket alkalmazhat rájuk. Az **összegzés** leggyakoribb használata a *darabszám*, amely az egyes csoportokban lévő eredmények számát adja vissza.

A következő lekérdezés áttekinti az összes *Perf* rekordot az elmúlt órában, *objektumnév*szerint csoportosítja őket, és megszámolja az egyes csoportok rekordjait: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Néha érdemes a csoportokat több dimenzió szerint definiálni. Ezen értékek minden egyedi kombinációja külön csoportot határoz meg:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Egy másik gyakori felhasználás, hogy matematikai vagy statisztikai számításokat végeznek az egyes csoportokon. A következő például az egyes számítógépek átlagos *ellenértékét* számítja ki:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Sajnos a lekérdezés eredményei értelmetlenek, mivel különböző teljesítményszámlálókat kevertünk össze. Ahhoz, hogy ez több értelmes, meg kell számítani az átlag külön-külön minden kombinációja *CounterName* és *a számítógép:*

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Összegzés időoszlop szerint
Az eredmények csoportosítása időoszlopon vagy más folytonos értéken is alapulhat. Egyszerűen összegezve `by TimeGenerated` azonban hozna létre csoportokat minden egyes ezredmásodperc az időtartományban, mivel ezek egyedi értékek. 

Ha folyamatos értékek alapján szeretne csoportokat létrehozni, a legjobb, ha a tartományt kezelhető egységekre bontja a **raktárhely**használatával. A következő lekérdezés olyan *Perf* rekordokat elemez, amelyek egy adott számítógépen a szabad*memóriát (Rendelkezésre álló MBytes) mérik.* Kiszámítja az egyes 1 órás időszakok átlagos értékét az elmúlt 7 napban:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

A kimenet tisztábbá tétele érdekében idődiagramként kell megjeleníteni, amely a rendelkezésre álló memóriát jeleníti meg az idő múlásával:

![Lekérdezési memória idővel](media/get-started-queries/chart.png)



## <a name="next-steps"></a>További lépések

- További információ a karakterláncadatok naplólekérdezésben való használatáról [a Karakterláncok használata az Azure Monitor naplólekérdezéseiben.](string-operations.md)
- További információ az adatok összesítéséről az [Azure Monitor naplólekérdezéseispeciális összesítéseivel](advanced-aggregations.md)rendelkező naplólekérdezésekben.
- Megtudhatja, hogy miként illesztheti össze több táblából származó adatokat a Joins segítségével [az Azure Monitor naplólekérdezéseivel.](joins.md)
- A [KQL nyelvi referenciadokumentumának](/azure/kusto/query/)dokumentációja a kusto-lekérdezés teljes nyelvéről.
