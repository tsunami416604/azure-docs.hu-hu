---
title: Understanding riasztásait az Azure Naplóelemzés |} Microsoft Docs
description: Naplóelemzési riasztások határozza meg az OMS-adattárban lévő fontos adatokat és is proaktív értesítést küldenek, problémák vagy meghívása műveletek kijavításának őket.  Ez a cikk ismerteti a különböző riasztási szabályok és hogyan vannak definiálva.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: cf1842c6abbbfd767184d8f480a5f3a5fd654ed0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32178670"
---
# <a name="understanding-alerts-in-log-analytics"></a>A Naplóelemzési riasztások ismertetése

> [!NOTE]
> A Naplóelemzési riasztások [az Azure kiterjesztendő](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A cikkben szereplő információkat továbbra is használhatók az Azure portálon, hogy a Naplóelemzési kereséssel riasztás részleteinek meghatározásához.

A Log Analytics-riasztások azonosítják a Log Analytics-adattárban található fontos információkat. Ez a cikk ismerteti a tervezési szempontokat sorát kell elvégezni a gyűjtemény gyakoriság az éppen lekérdezett, véletlenszerű késleltetése oka valószínűleg a hálózati késés vagy feldolgozási kapacitás és az adatok véglegesítése naplóba adatfeldolgozást az adatok alapján A naplóelemzési munkaterület. Részletesen ismerteti, hogyan riasztási szabályok használatát Naplóelemzési munkahelyi és a különböző típusú riasztási szabályok közötti különbségeket ismerteti.

A riasztási szabályok létrehozásának folyamatán tekintse meg a következő cikkeket:

- Riasztási szabályok létrehozására [Azure-portálon](log-analytics-alerts-creating.md)
- Riasztási szabályok létrehozására [Resource Manager-sablon](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Riasztási szabályok létrehozására [REST API-n](log-analytics-api-alerts.md)

## <a name="considerations"></a>Megfontolandó szempontok

Információk a adatainak gyűjtési gyakoriságát, a különböző megoldások és adattípus érhetők el a [az gyűjtemény adatait](log-analytics-add-solutions.md#data-collection-details) megoldások áttekintése cikk. Amint ez a cikk, gyűjtési gyakoriságát szerint ritkán előforduló mint hét naponta történő lehet *értesítésre kattinthat*. Fontos megérteni, és vegye figyelembe a adatainak gyűjtési gyakoriságát riasztás beállítása előtt. 

- A gyűjtemény gyakorisága határozza meg, milyen gyakran az OMS-ügynököt adatokat küld a Naplóelemzési. Például gyűjtési gyakoriságát 10 percet, és nincs más késések fordulnak elő a rendszerben, ha majd az átvitt adatok időbélyeggel lehet, hogy bárhonnan közötti nulla és a régi előtt a tárházba való felvétel alatt 10 perc és kereshető a Naplóelemzési.

- Is elindítható a riasztást, mielőtt az adatokat kell írni a tárház úgy, hogy az elérhető történő lekérdezéskor. A fent leírt késleltetése miatt a gyűjtemény gyakoriság értéke nem ugyanaz, mint a lekérdezések érhetők el az adatok időpontja. Például előfordulhat, hogy az adatok gyűjthetők pontosan minden 10 percig, amíg az adatok nem áll rendelkezésre az adattárház szabálytalan időközönként. Hypothetically nulla, 10 és 20 perces időközönként összegyűjtött adatok valószínűleg használható keresési 25, 28 és 35 perccel rendre, vagy más szabálytalan időközben adatfeldolgozást késés befolyásol. Ezek a késleltetések a legrosszabb esetben részletes ismertetését lásd: a [Naplóelemzési SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1), nem tartalmaz, amelyek rendszerben jelent meg a gyűjtemény gyakorisága vagy a hálózati késés korlátozza a számítógép és a Naplóelemzés szolgáltatás közötti késleltetés.


## <a name="alert-rules"></a>Riasztási szabályok

A riasztásokat riasztási szabályok hozzák létre, amelyek rendszeres időközönként automatikus naplókereséseket futtatnak. Ha a napló keresés eredményeit az adott feltételeknek megfelelő, egy riasztás rekord jön létre. A szabály ekkor automatikusan lefuttathat egy vagy több műveletet, hogy proaktívan értesítse Önt a riasztásról, vagy meghívjon egy másik folyamatot. Különböző típusú riasztási szabályok az elemzés végrehajtásához használja a másik programot.

![Log Analytics-riasztások](media/log-analytics-alerts/overview.png)

Mivel a naplóadatok adatfeldolgozást az egy várható késés, abszolút adatokat, és elérhető indexelő is lennie előre nem látható. A gyűjtött adatok közel valós idejű rendelkezésre állását a riasztási szabályok definiálása során figyelembe kell venni.

Nincs megbízhatósági riasztások és értesítések válaszképességének között. Dönthet úgy, hogy minimalizálja a téves riasztások és a hiányzó riasztások riasztási paramétereinek a konfigurálása, vagy választhat gyorsan reagál a feltételeket, amelyek figyelés alatt áll, de időnként a hamis értéket, vagy kihagyott riasztásokat generál a riasztási paraméterekkel.

A riasztási szabályok határozzák meg a következő adatokat:

- **Naplófájl-keresési**. A lekérdezés, amely futtatja a minden alkalommal, amikor a riasztási szabály következik be. Ez a lekérdezés által visszaadott rekordok segítségével meghatározhatja, hogy riasztás jöjjön létre.
- **Időablak**. Adja meg a lekérdezés időintervallumát. A lekérdezés csak azokat a rekordokat adja vissza, amelyek az aktuális idő ezen tartományában jöttek létre. Ez lehet öt perc és 24 óra közötti értéket. A tartományban kell lennie fér adatfeldolgozást ésszerű késést befogadásához. Az időszak kell lennie a leghosszabb tudja kezelni kívánt késleltetés hosszát kétszer.<br> Például ha azt szeretné, hogy a riasztásokat a 30 perces késést megbízható, majd a tartományban kell lennie egy óra.

    Nincsenek két jelenségek sikerült tapasztal, ha az időtartományt túl kicsi.

    - **Hiányzik a riasztások**. Tegyük fel, az adatfeldolgozást késleltetés néha 60 perc, de az esetek többségében 15 perc. Ha a időszak 30 percre van beállítva, majd azt gyorsítótárbeli sikertelen keresések riasztást késleltetési idő legyen a 60 perc, mert az adatok nem használható keresési, ha a riasztási lekérdezés végrehajtása során. 
   
        >[!NOTE]
        >Próbál megállapításához, hogy a rendszer nem talált meg a riasztás miért nem lehetséges. Például a fenti esetben a adatot ír a tárház 60 perc után a riasztási lekérdezés végre lett hajtva. Ha azt az első fellépése a következő napon kimaradt egy riasztást, és a következő napon a lekérdezés végrehajtása a helyes időre időszakban, a naplófájl-keresési feltételeknek megfelelő eredménye. Úgy tűnik, hogy a riasztás kell rendelkeznie lett elindítva. Valójában a riasztás volt nem indulnak el, mivel az adatok még nem voltak elérhetők a riasztási lekérdezés végrehajtásakor. 
        >
 
    - **Téves riasztások**. Egyes esetekben riasztási lekérések úgy terveztek, hogy az események hiányára. Egy példa erre van annak ellenőrzése, ha egy virtuális gép kapcsolat nélküli a kihagyott szívverések keresve. Fent, ha a szívverés nem érhető el keresési belül a riasztási időszak, majd riasztást jön létre, mert a szívverés adatok még nem volt kereshető, és ezért hiányzik. Ez az ugyanazt az eredményt, mintha a virtuális gép szabályosan offline állapotban volt, és nincs által generált szívverés adatot nem. A lekérdezés végrehajtásakor, a következő napon keresztül a megfelelő időszak jeleníti meg, hogy nincsenek-szívverés és riasztási nem sikerült. Valójában a szívverések fejeződtek nem érhető el a kereséshez, mert a riasztás időszak túl kicsi volt beállítva.

- **Gyakoriság**.  Itt adhatja meg, milyen gyakran a lekérdezés kell futtatni, és a riasztások a normál esetben gyorsabb végrehajtásához használható. Az érték 5 perc és 24 óra közötti lehet, és a riasztási időszak-nél kisebb vagy azzal egyenlőnek kell lennie.  Ha az érték nagyobb, mint az időszak, majd azzal kockáztatja alatt nem talált rekordokat.<br>A cél az, ha a megbízható késlelteti akár 30 percet és normál késleltetési idő legyen 10 perc, a időszak egy óra és a gyakoriság értéke 10 percnek kell lennie. Ez megjelenik egy figyelmeztetés, az adatok, amelyek eltérő 10 perces adatfeldolgozást késleltetést 10 és 20-percek száma, az a riasztási adatok létrehozásakor.<br>Az adatok több riasztásokat hoznak létre, mert a időszak túl széles elkerüléséhez a riasztások letiltásához beállítás segítségével legalább, amíg a időszak értesítések mellőzése.
  
- **Küszöbérték**. A naplófájl-keresési eredmények kiértékelése annak meghatározásához, hogy riasztást kell létrehozni. A küszöbérték nem azonos a különböző típusú riasztási szabályok.

A Naplóelemzési minden riasztási szabály a két típus egyike. Ezek a típusok leírását a következő szakaszok részletesen.

- **[Találatok száma](#number-of-results-alert-rules)**. Egyetlen riasztás jön létre, amikor a szám a napló keresés által visszaadott rekordok meghaladja a megadott értéket.
- **[Metrika mérési](#metric-measurement-alert-rules)**. A megadott küszöbértéket meghaladó értékek napló keresés eredményeit az egyes objektumok létre riasztást.

Riasztási szabály típusa közötti különbségek a következők:

- **Találatok száma** riasztási szabály mindig hozzon létre egy riasztás rövid **metrika mérési** riasztási szabály a minden objektumon meghaladja a küszöbértéket, riasztást hoz létre.
- **Találatok száma** riasztási szabályok riasztást hoznak létre, amikor a küszöbérték elérése esetén egy alkalommal. **Metrika mérési** riasztási szabályok riasztást hozhat létre, a küszöbérték túllépésekor bizonyos számú alkalommal keresztül egy adott időintervallumban.

## <a name="number-of-results-alert-rules"></a>Eredmények riasztási szabályok száma
**Találatok száma** riasztási szabályok egyetlen riasztás létrehozása, ha a keresési lekérdezés által visszaadott rekordok száma meghaladja a küszöbértéket.

### <a name="threshold"></a>Küszöbérték
A küszöbérték egy **eredmények száma** riasztási szabály egy adott értéknél kisebb vagy nagyobb. Ha a napló keresés által visszaadott rekordok számát a feltételeknek, egy riasztás jön létre.

### <a name="scenarios"></a>Forgatókönyvek

#### <a name="events"></a>Események
Riasztási szabály az ilyen típusú ideális munkavégzés az eseményekkel, például a Windows eseménynaplóiban keresse meg, a Syslog, és az egyéni naplózza. Érdemes lehet a riasztás létrehozása, ha egy adott hibaesemény jön létre, vagy ha több hibaesemények belül egy adott időkerete jönnek létre.

Riasztást küld, egyetlen esemény, az eredmények csak meg 0-nál nagyobbnak, és mind a gyakoriság időszak, 5 perc. A lekérdezés futó minden 5 perc és egy egyszeri esemény, a lekérdezés futtatása legutóbbi indítása óta létrehozott ellenőrzése. Hosszabb gyakorisága miatt késhet az esemény gyűjtött és a riasztás létrehozása között eltelő idő.

Egyes alkalmazások jelentkezhetnek be, amely nem feltétlenül riasztás alkalmi hiba. Az alkalmazás például újra a folyamatot a hibaesemény létrehozott és a következő alkalommal majd sikeres. Ebben az esetben nem érdemes lehet a riasztás létrehozása, kivéve, ha több esemény egy adott időpontban időszakban jönnek létre.

Bizonyos esetekben érdemes lehet egy esemény hiányában hozzon létre egy riasztást. Például egy folyamat esetleg naplózása rendszeres annak jelzésére, hogy megfelelően működik-e. Ha egy ilyen eseményt belül egy adott időkerete nem jelentkeznek, majd riasztást kell létrehozni. Ebben az esetben akkor értékre kell állítania a küszöbérték **1-nél kevesebb**.

#### <a name="performance-alerts"></a>Teljesítményével kapcsolatos riasztások
[Teljesítményadatok](log-analytics-data-sources-performance-counters.md) rögzíti a hasonló események OMS-tárházban tárolja. Ha azt szeretné, ha egy teljesítményszámláló eléri az adott riasztást küld, majd a küszöbérték szerepelnie kell a lekérdezésben.

Ha szeretné a processzor futtatásakor riasztás például több mint 90 %, használhatja a következő lekérdezést a küszöbértékkel a riasztási szabály **0-nál nagyobb**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

Riasztás, ha a processzor átlagosan több mint 90 %-át egy adott időtartomány szeretne, ha szeretné használni egy lekérdezés segítségével a `measure` parancsot a következő, a riasztási szabály a küszöbértékkel **0-nál nagyobb**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90



## <a name="metric-measurement-alert-rules"></a>Metrika mérési riasztási szabályok

>[!NOTE]
> Riasztási szabályok metrika mérési jelenleg nyilvános előzetes verziójához.

**Metrika mérési** riasztási szabályok létrehozása minden objektum egy riasztást a megadott küszöbértéket meghaladó érték lekérdezésben. A következő különböző különbségeket rendelkeznek **eredmények száma** riasztási szabályok.

#### <a name="log-search"></a>Naplókeresés
Használhatja a lekérdezés egy **eredmények száma** riasztás szabályok vonatkoznak konkrét követelmények a lekérdezés egy metrika mérési riasztási szabály.  Tartalmaznia kell egy `measure` parancs egy adott mező az eredmények csoportosításához. Ez a parancs a következő elemeket kell tartalmaznia.


- **Összesítő függvény**.  Meghatározza a számítás, amely történik, és potenciálisan egy numerikus összesítendő mező. Például **count()** rekordok számát adja vissza a lekérdezésben **avg(CounterValue)** arra az időtartamra, a ellenértéknek mező átlagát adja vissza.
- **Csoport mező**.  Az összesített érték egy rekord jön létre minden egyes példányánál ebben a mezőben, és a riasztás is generálható minden. Például, ha az egyes számítógépek riasztás létrehozása, használhatja **számítógépenként**.   
- **Időköz**.  Az időtartam alatt, amelyben az adatokat összesített értéket határoz meg.  Például, ha a megadott **5 perc**, létrehozott egy rekordot az a csoportmező, 5 perces időközönként a riasztás megadott időszak alatt összesített értéket minden egyes példányánál.

#### <a name="threshold"></a>Küszöbérték
Riasztási szabályok metrika mérési küszöbértéke összesítő érték és a behatolások határozzák meg. A naplófájl-keresési bármely adatpont meghaladja ezt az értéket, ha a jövőben éri figyelembe. Ha megszegése az összes objektum az eredmények száma meghaladja a megadott értéket, majd riasztást hoz létre, hogy az objektum.

#### <a name="example"></a>Példa
Fontolja meg egy olyan forgatókönyvet, ahol keresett riasztást, ha a számítógép számát processzorhasználat 90 %-os háromszor több mint 30 perc. Riasztási szabály okozna a következő részletekkel.

**Lekérdezés:** típus = telj ObjectName processzor CounterName = "kihasználtsága (%) = |} avg(CounterValue) mértékcsoport által számítógép időköz 5 perc<br>
**Időablak:** 30 perc<br>
**Riasztási gyakoriságot:** 5 perc<br>
**Értéket összesítő:** 90-nél több<br>
**Eseményindító riasztás alapján:** összege nagyobb, mint 5 feltöri<br>

A lekérdezés 5 perces időközönként hozna létre az egyes számítógépek átlagos értékét. Ez a lekérdezés az előző 30 perc át 5 percenként összegyűjtött adatok kell futnak. Mintaadatokat három számítógépek alább láthatók.

![A minta lekérdezés eredményei](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Ebben a példában külön riasztások létrehozott KSZLG02 és srv03 mivel azok megszegése a 90 %-os küszöbértéket 3-szor a időszak alatt. Ha a **eseményindító riasztás alapján:** értékről **folyamatos** riasztást volna létre csak a srv03 óta 3 egymást követő minták küszöbértéke megszegése azt.

## <a name="alert-records"></a>Riasztási rekordok
A Naplóelemzési riasztási szabályok által létrehozott riasztás rekordok rendelkezik egy **típus** a **riasztás** és egy **SourceSystem** a **OMS**. A Tulajdonságok rendelkeznek, az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |*Riasztás* |
| SourceSystem |*OMS* |
| *Object*  | [Metrika mérési riasztások](#metric-measurement-alert-rules) a csoport mező tulajdonsággal rendelkezik. Például ha a naplófájl-keresési csoportjait a számítógép, az értesítési rekord rendelkezik annak a számítógépnek a nevét számítógép mezővel értékeként.
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
* A forgatókönyv a [konfigurálása egy webook](log-analytics-alerts-webhooks.md) a riasztási szabályt.
* Ismerje meg, hogyan írhat [az Azure Automation runbookjai](https://azure.microsoft.com/documentation/services/automation) riasztások által azonosított problémák megoldásáról.
