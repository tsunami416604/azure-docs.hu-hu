---
title: "Napló riasztások figyelése Azure - riasztások (előzetes verzió) |} Microsoft Docs"
description: "Eseményindító e-mailek, értesítések, az összetett lekérdezések megadott feltételek (előzetes verzió) Azure riasztásokhoz webhely URL-címek (webhookok), vagy az automation hívni."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: vinagara
ms.openlocfilehash: 99d222102ab0245c7c4dc8603eaedcfc88ae7a66
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Napló riasztások figyelése Azure - riasztások (előzetes verzió)
Ez a cikk részletesen ismerteti, hogyan riasztási szabályok elemzési lekérdezések használata az Azure-riasztások (előzetes verzió), és különböző típusú riasztási szabályok napló közötti különbségeket ismerteti.
Jelenleg Azure riasztások (előzetes verzió), támogat jelentkezzen ki riasztást a lekérdezések [Azure Naplóelemzés](../log-analytics/log-analytics-tutorial-viewdata.md) írt [új Log Analytics lekérdezési nyelv](../log-analytics/log-analytics-log-search-upgrade.md)

> [!WARNING]
> Az Azure riasztások (előzetes verzió) – napló riasztások, jelenleg nem támogatja a kereszt-munkaterület vagy az alkalmazások közötti lekérdezések. 

## <a name="log-alert-rules"></a>Napló riasztási szabályok

Riasztások napló lekérdezések automatikusan futtatása rendszeres időközönként (előzetes verzió) Azure riasztások jönnek létre.  Ha a napló lekérdezés eredményeit az adott feltételeknek megfelelő, egy riasztás rekord jön létre. A szabály automatikusan követően futtathatja egy vagy több művelet proaktív a riasztás értesíti, vagy egy másik folyamat használja, a runbookok futtatása azonos hívási [művelet csoportok](monitoring-action-groups.md).  Különböző típusú riasztási szabályok az elemzés végrehajtásához használja a másik programot.

A riasztási szabályok határozzák meg a következő adatokat:

- **Lekérdezés jelentkezzen**.  A lekérdezés, amely futtatja a minden alkalommal, amikor a riasztási szabály következik be.  Ez a lekérdezés által visszaadott rekordok segítségével meghatározhatja, hogy riasztás jöjjön létre.
- **Időablak**.  Adja meg a lekérdezés időintervallumát.  A lekérdezés visszaadja csak azt jelzi, hogy az aktuális időponthoz képest ebben a tartományban jöttek létre.  Időablak tetszőleges érték 5 perc és 1440 perc és 24 óra közötti lehet. Például ha a időszak 60 percre van beállítva, és a lekérdezés futtatása, 1:15 előtti, csak a 12:15 előtti és 1:15 előtti között létrejövő rekordok ad vissza.
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

**Küszöbérték**: küszöbértéke egy **eredmények száma** riasztási szabály egy adott értéknél kisebb vagy nagyobb.  Ha a napló keresés által visszaadott rekordok számát a feltételeknek, egy riasztás jön létre.

### <a name="scenarios"></a>Forgatókönyvek

#### <a name="events"></a>Események
Riasztási szabály az ilyen típusú ideális munkavégzés az eseményekkel, például a Windows eseménynaplóiban keresse meg, a Syslog, és az egyéni naplózza.  Érdemes lehet a riasztás létrehozása, ha egy adott hibaesemény jön létre, vagy ha több hibaesemények belül egy adott időkerete jönnek létre.

Riasztást küld, egyetlen esemény, az eredmények csak meg 0-nál nagyobbnak, és mind a gyakoriság időkerete öt perc.  A lekérdezés futó minden öt perc és egy egyszeri esemény, a lekérdezés futtatása legutóbbi indítása óta létrehozott ellenőrzése.  Hosszabb gyakorisága miatt késhet az esemény gyűjtött és a riasztás létrehozása között eltelő idő.

Egyes alkalmazások jelentkezhetnek be, amely nem feltétlenül riasztás alkalmi hiba.  Az alkalmazás például újra a folyamatot a hibaesemény létrehozott és a következő alkalommal majd sikeres.  Ebben az esetben nem érdemes lehet a riasztás létrehozása, kivéve, ha több esemény egy adott időpontban időszakban jönnek létre.  

Bizonyos esetekben érdemes lehet egy esemény hiányában hozzon létre egy riasztást.  Például egy folyamat esetleg naplózása rendszeres annak jelzésére, hogy megfelelően működik-e.  Ha egy ilyen eseményt belül egy adott időkerete nem jelentkeznek, majd riasztást kell létrehozni.  Ebben az esetben akkor értékre kell állítania a küszöbérték **1-nél kevesebb**.

## <a name="metric-measurement-alert-rules"></a>Metrika mérési riasztási szabályok

**Metrika mérési** riasztási szabályok létrehozása minden objektum egy riasztást a megadott küszöbértéket meghaladó érték lekérdezésben.  A következő különböző különbségeket rendelkeznek **eredmények száma** riasztási szabályok.

**Összesítő függvény**: meghatározza a számítás, amely történik, és potenciálisan egy numerikus összesítendő mező.  Például **count()** rekordok számát adja vissza a lekérdezésben **avg(CounterValue)** arra az időtartamra, a ellenértéknek mező átlagát adja vissza.

**Csoport mező**: egy rekord, egy összesített értéket ebben a mezőben minden példánya létrejön, és riasztás is generálható minden.  Például, ha az egyes számítógépek riasztás létrehozása, használhatja **számítógépenként**   

**Időköz**: az időtartam alatt, amelyben az adatokat összesített értéket határoz meg.  Például, ha a megadott **öt perc**, létrehozott egy rekordot az a csoportmező, 5 perces időközönként a riasztás megadott időszak alatt összesített értéket minden egyes példányánál.

**Küszöbérték**: küszöbértéke figyelmeztetési metrika mérési szabályok határozzák meg az összesített érték és a behatolások.  A naplófájl-keresési bármely adatpont meghaladja ezt az értéket, ha a jövőben éri figyelembe.  Ha megszegése az összes objektum az eredmények száma meghaladja a megadott értéket, majd riasztást hoz létre, hogy az objektum.

#### <a name="example"></a>Példa
Fontolja meg egy olyan forgatókönyvet, ahol keresett riasztást, ha a számítógép számát processzorhasználat 90 %-os háromszor több mint 30 perc.  Riasztási szabály okozna a következő adatokkal:  

**Lekérdezés:** telj |} ahol ObjectName == "Processzor" és a CounterName == "kihasználtsága (%) |} AggregatedValue összefoglalója bin (TimeGenerated, 5 m), a számítógép által avg(CounterValue) =<br>
**Időablak:** 30 perc<br>
**Riasztási gyakoriságot:** öt perc<br>
**Értéket összesítő:** kiváló 90-nél<br>
**Eseményindító riasztás alapján:** összege nagyobb, mint 5 feltöri<br>

A lekérdezés 5 perces időközönként hozna létre az egyes számítógépek átlagos értékét.  Ez a lekérdezés az előző 30 perc át 5 percenként összegyűjtött adatok kell futnak.  Mintaadatokat három számítógépek alább láthatók.

![A minta lekérdezés eredményei](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Ebben a példában külön riasztások létrehozott KSZLG02 és srv03 mivel azok megszegése a 90 %-os küszöbértéket 3-szor a időszak alatt.  Ha a **eseményindító riasztás alapján:** értékről **folyamatos** riasztást volna létre csak a srv03, mivel azt a három egymást követő minták küszöbértéke megszegése.


## <a name="next-steps"></a>További lépések
* [Azure riasztások (előzetes verzió) áttekintése](monitoring-overview-unified-alerts.md) 
* További tudnivalók [Azure riasztások használatával (előzetes verzió)](monitor-alerts-unified-usage.md)
* További információ [Naplóelemzési](../log-analytics/log-analytics-overview.md).    
