---
title: "Understanding riasztásait az Azure Naplóelemzés |} Microsoft Docs"
description: "Naplóelemzési riasztások határozza meg az OMS-adattárban lévő fontos adatokat és is proaktív értesítést küldenek, problémák vagy meghívása műveletek kijavításának őket.  Ez a cikk ismerteti a különböző riasztási szabályok és hogyan vannak definiálva."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2017
ms.author: bwren
ms.openlocfilehash: a0897113660f764cb23239b066bc93c479a9a553
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="understanding-alerts-in-log-analytics"></a>A Naplóelemzési riasztások ismertetése

Log Analytics riasztások határozza meg a Naplóelemzési tárházban fontos adatokat.  Ez a cikk részletesen ismerteti, hogyan riasztási szabályok Naplóelemzési munka, és különböző típusú riasztási szabályok közötti különbségeket ismerteti.

A riasztási szabályok létrehozásának folyamatán tekintse meg a következő cikkeket:

- Riasztási szabályok létrehozására [Azure-portálon](log-analytics-alerts-creating.md)
- Riasztási szabályok létrehozására [Resource Manager-sablon](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Riasztási szabályok létrehozására [REST API-n](log-analytics-api-alerts.md)


## <a name="alert-rules"></a>Riasztási szabályok

A riasztási szabályok, amelyek automatikusan futnak a napló keresések rendszeres időközönként riasztások jönnek létre.  Ha a napló keresés eredményeit az adott feltételeknek megfelelő, egy riasztás rekord jön létre.  A szabály úgy automatikusan futtatja egy vagy több műveletek proaktív értesítést küldenek, a figyelmeztetés vagy meg kíván hívni egy másik folyamat.  Különböző típusú riasztási szabályok az elemzés végrehajtásához használja a másik programot.

![Log Analytics-riasztások](media/log-analytics-alerts/overview.png)

A riasztási szabályok határozzák meg a következő adatokat:

- **Naplófájl-keresési**.  A lekérdezés, amely futtatja a minden alkalommal, amikor a riasztási szabály következik be.  Ez a lekérdezés által visszaadott rekordok segítségével határozza meg, hogy riasztás jöjjön létre.
- **Időablak**.  Adja meg a lekérdezés időintervallumát.  A lekérdezés visszaadja csak azt jelzi, hogy az aktuális időponthoz képest ebben a tartományban jöttek létre.  Ez bármilyen 5 perc és 24 óra közötti érték lehet. Például ha a időszak 60 percre van beállítva, és a lekérdezés futtatása, 1:15 előtti, csak a 12:15 előtti és 1:15 előtti között létrejövő rekordok ad vissza.
- **Gyakoriság**.  Meghatározza, hogy milyen gyakran kell futtatni a lekérdezést. Bármely érték 5 perc és 24 óra közötti lehet. A időszak kisebbnek vagy azzal egyenlőnek kell lennie.  Ha az érték nagyobb, mint az időszak, majd azzal kockáztatja alatt nem talált rekordokat.<br>Vegye figyelembe például egy olyan időkeretet, 30 perc és 60 perc gyakorisága.  Ha a lekérdezés futtatása, 1:00, 12:30 és 1:00 PM rekordok adja vissza.  A következő szeretné futtatni a lekérdezést ideje 2:00 amikor meghaladná a 1:30 és 2:00 között rögzíti.  1:00 és 1:30 között létrejövő rekordok volna soha nem értékelhető ki.
- **Küszöbérték**.  A naplófájl-keresési eredmények kiértékelése annak meghatározásához, hogy riasztást kell létrehozni.  A küszöbérték nem azonos a különböző típusú riasztási szabályok.

A Naplóelemzési minden riasztási szabály a két típus egyike.  Ezek a típusok leírását a következő szakaszok részletesen.

- **[Találatok száma](#number-of-results-alert-rules)**. Egyetlen riasztás jön létre, amikor a szám a napló keresés által visszaadott rekordok meghaladja a megadott értéket.
- **[Metrika mérési](#metric-measurement-alert-rules)**.  A megadott küszöbértéket meghaladó értékek napló keresés eredményeit az egyes objektumok létre riasztást.

Riasztási szabály típusa közötti különbségek a következők:

- **Találatok száma** riasztási szabály mindig létrehoz egy riasztás rövid **metrika mérési** riasztási szabály a minden objektumon meghaladja a küszöbértéket, riasztást hoz létre.
- **Találatok száma** riasztási szabályok riasztást hoznak létre, amikor a küszöbérték elérése esetén egy alkalommal. **Metrika mérési** riasztási szabályok riasztást hozhat létre, a küszöbérték túllépésekor bizonyos számú alkalommal keresztül egy adott időintervallumban.

## <a name="number-of-results-alert-rules"></a>Eredmények riasztási szabályok száma
**Találatok száma** riasztási szabályok egyetlen riasztás létrehozása, ha a keresési lekérdezés által visszaadott rekordok száma meghaladja a küszöbértéket.

### <a name="threshold"></a>Küszöbérték
A küszöbérték egy **eredmények száma** riasztási szabály egyszerűen nagyobb vagy kisebb, mint egy adott értéket.  Ha a napló keresés által visszaadott rekordok számát a feltételeknek, egy riasztás jön létre.

### <a name="scenarios"></a>Forgatókönyvek

#### <a name="events"></a>Események
Riasztási szabály az ilyen típusú ideális munkavégzés az eseményekkel, például a Windows eseménynaplóiban keresse meg, a Syslog, és az egyéni naplózza.  Érdemes lehet a riasztás létrehozása, ha egy adott hibaesemény jön létre, vagy ha több hibaesemények belül egy adott időkerete jönnek létre.

Riasztást küld, egyetlen esemény, az eredmények csak meg 0-nál nagyobbnak, és mind a gyakoriság időszak, 5 perc.  A lekérdezés futó minden 5 perc és egy egyszeri esemény, a lekérdezés futtatása legutóbbi indítása óta létrehozott ellenőrzése.  Hosszabb gyakorisága miatt késhet az esemény gyűjtött és a riasztás létrehozása között eltelő idő.

Egyes alkalmazások jelentkezhetnek be, amely nem feltétlenül riasztás alkalmi hiba.  Az alkalmazás például újra a folyamatot a hibaesemény létrehozott és a következő alkalommal majd sikeres.  Ebben az esetben nem érdemes lehet a riasztás létrehozása, kivéve, ha több esemény egy adott időpontban időszakban jönnek létre.  

Bizonyos esetekben érdemes lehet egy esemény hiányában hozzon létre egy riasztást.  Például egy folyamat esetleg naplózása rendszeres annak jelzésére, hogy megfelelően működik-e.  Ha egy ilyen eseményt belül egy adott időkerete nem jelentkeznek, majd riasztást kell létrehozni.  Ebben az esetben akkor értékre kell állítania a küszöbérték **1-nél kevesebb**.

#### <a name="performance-alerts"></a>Teljesítményével kapcsolatos riasztások
[Teljesítményadatok](log-analytics-data-sources-performance-counters.md) rögzíti a hasonló események OMS-tárházban tárolja.  Ha azt szeretné, ha egy teljesítményszámláló eléri az adott riasztást küld, majd a küszöbérték szerepelnie kell a lekérdezésben.

Ha szeretné a processzor futtatásakor riasztás például több mint 90 %, használhatja a következő lekérdezést a küszöbértékkel a riasztási szabály **0-nál nagyobb**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

    

Ha riasztást küld, ha a processzor átlagosan több mint 90 %-át egy adott időtartomány, használhatja a következő lekérdezést a küszöbértékkel a riasztási szabály **0-nál nagyobb**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | where CounterValue>90 | summarize avg(CounterValue) by Computer

    
>[!NOTE]
> Ha a munkaterületet van még nincsenek frissítve az a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd a fenti lekérdezések megváltozna a ez utóbbi használatával a következő a [parancs mérésére](log-analytics-search-reference.md#commands):`Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90`
> `Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90`


## <a name="metric-measurement-alert-rules"></a>Metrika mérési riasztási szabályok

>[!NOTE]
> Riasztási szabályok metrika mérési jelenleg nyilvános előzetes verziójához.

**Metrika mérési** riasztási szabályok létrehozása minden objektum egy riasztást a megadott küszöbértéket meghaladó érték lekérdezésben.  A következő különböző különbségeket rendelkeznek **eredmények száma** riasztási szabályok.

#### <a name="log-search"></a>Naplókeresés
Használhatja a lekérdezés egy **eredmények száma** riasztás szabályok vonatkoznak konkrét követelmények a lekérdezés egy metrika mérési riasztási szabály.  Tartalmaznia kell egy [parancs mérésére](log-analytics-search-reference.md#commands) egy adott mező az eredmények csoportosításához. Ez a parancs a következő elemeket kell tartalmaznia.

- **Összesítő függvény**.  Meghatározza a számítás, amely történik, és potenciálisan egy numerikus összesítendő mező.  Például **count()** visszatér a rekordok számát a lekérdezésben **avg(CounterValue)** arra az időtartamra, lesz a ellenértéknek mező átlagának visszaadása.
- **Csoport mező**.  Az összesített érték egy rekord jön létre minden egyes példányánál ebben a mezőben, és a riasztás is generálható minden.  Például, ha az egyes számítógépek riasztás létrehozása, használhatja **számítógépenként**.   
- **Időköz**.  Az időtartam alatt, amelyben az adatokat összesített értéket határoz meg.  Például, ha a megadott **5minutes**, létrehozott egy rekordot az a csoportmező, 5 perces időközönként a riasztás megadott időszak alatt összesített értéket minden egyes példányánál.

#### <a name="threshold"></a>Küszöbérték
Riasztási szabályok metrika mérési küszöbértéke összesítő érték és a behatolások határozzák meg.  A naplófájl-keresési bármely adatpont meghaladja ezt az értéket, ha a jövőben éri figyelembe.  Ha megszegése az összes objektum az eredmények száma meghaladja a megadott értéket, majd riasztást hoz létre, hogy az objektum.

#### <a name="example"></a>Példa
Fontolja meg egy olyan forgatókönyvet, ahol keresett riasztást, ha a számítógép számát processzorhasználat 90 %-os háromszor több mint 30 perc.  Riasztási szabály okozna a következő részletekkel.  

**Lekérdezés:** telj |} ahol ObjectName == "Processzor" és a CounterName == "kihasználtsága (%) |} AggregatedValue összefoglalója bin (TimeGenerated, 5 m), a számítógép által avg(CounterValue) =<br>
**Időablak:** 30 perc<br>
**Riasztási gyakoriságot:** 5 perc<br>
**Értéket összesítő:** 90-nél több<br>
**Eseményindító riasztás alapján:** összesen feltöri nagyobb, mint 2<br>

A lekérdezés 5 perces időközönként hozna létre az egyes számítógépek átlagos értékét.  Ez a lekérdezés az előző 30 perc át 5 percenként összegyűjtött adatok kell futnak.  Mintaadatokat három számítógépek alább láthatók.

![A minta lekérdezés eredményei](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Ebben a példában külön riasztások létrehozott KSZLG02 és srv03 mivel azok megszegése a 90 %-os küszöbértéket 3-szor a időszak alatt.  Ha a **eseményindító riasztás alapján:** értékről **folyamatos** riasztást volna létre csak a srv03 óta 3 egymást követő minták küszöbértéke megszegése azt.

## <a name="alert-records"></a>Riasztási rekordok
A Naplóelemzési riasztási szabályok által létrehozott riasztás rekordok rendelkezik egy **típus** a **riasztás** és egy **SourceSystem** a **OMS**.  A Tulajdonságok rendelkeznek, az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |*Riasztás* |
| SourceSystem |*OMS* |
| *Objektum*  | [Metrika mérési riasztások](#metric-measurement-alert-rules) egy tulajdonság az a csoportmező fog rendelkezni.  Például ha a naplófájl-keresési csoportjait a számítógép, az értesítési rekord rendelkezik annak a számítógépnek a nevét számítógép mezővel értékeként.
| AlertName |A riasztás nevét. |
| AlertSeverity |A riasztás súlyossági szintje. |
| LinkToSearchResults |A lekérdezésből, amely a riasztás létrehozása a rekordok visszaadó Naplóelemzési napló keresési kapcsolódik. |
| Lekérdezés |A futtatott lekérdezés szövegét. |
| QueryExecutionEndTime |A lekérdezés időintervallumát végét. |
| QueryExecutionStartTime |A lekérdezés időintervallumát elindítása. |
| ThresholdOperator | A riasztási szabály által használt operátor. |
| ThresholdValue | A riasztási szabály által használt érték. |
| TimeGenerated |A riasztás létrehozásának dátuma és időpontja. |

Más típusú által létrehozott riasztás rekordok léteznek a [riasztás felügyeleti megoldás](log-analytics-solution-alert-management.md) és [Power BI exportálja](log-analytics-powerbi.md).  Ezek minden rendelkeznek egy **típus** a **riasztási** alapján megkülönböztetett, de azok **SourceSystem**.


## <a name="next-steps"></a>További lépések
* Telepítse a [Riasztáskezelési megoldás](log-analytics-solution-alert-management.md) Naplóelemzési begyűjti a System Center Operations Manager riasztásokat együtt létrehozott riasztások elemzéséhez.
* Tudjon meg többet az [keresések jelentkezzen](log-analytics-log-searches.md) , amely riasztást generál.
* A forgatókönyv a [konfigurálása egy webhook](log-analytics-alerts-webhooks.md) a riasztási szabályt.  
* Ismerje meg, hogyan írhat [az Azure Automation runbookjai](https://azure.microsoft.com/documentation/services/automation) riasztások által azonosított problémák megoldásáról.
