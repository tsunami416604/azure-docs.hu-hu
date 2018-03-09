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
ms.date: 02/02/2018
ms.author: vinagara
ms.openlocfilehash: 0cee8bf77e0facc12159b823152b8859ce5cedd8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Napló riasztások figyelése Azure - riasztások (előzetes verzió)
Ez a cikk részletesen ismerteti, hogyan riasztási szabályok elemzési lekérdezések használata az Azure-riasztások (előzetes verzió), és különböző típusú riasztási szabályok napló közötti különbségeket ismerteti. A naplók segítségével metrika riasztások leírását, [közel valós idejű metrika riasztások](monitoring-near-real-time-metric-alerts.md)

Jelenleg Azure riasztások (előzetes verzió), támogatja jelentkezzen ki riasztást a lekérdezések [Azure Naplóelemzés](../log-analytics/log-analytics-tutorial-viewdata.md) és [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events).

> [!WARNING]

> Jelenleg napló riasztással Azure riasztások (előzetes verzió) nem támogatja a kereszt-munkaterület vagy az alkalmazások közötti lekérdezések.

Emellett a felhasználók is tökéletes Analytics platform választott Azure-ban a lekérdezések, majd *importálja azokat az értesítések (előzetes verzió) használható a lekérdezés mentésével*. Lépést kell végrehajtania:
- Az Application Insights: Nyissa meg az Analytics-portálról érvényesítése a lekérdezés és az eredményeket. Mentse az egyedi névvel rendelkező *megosztott lekérdezések*.
- A Naplóelemzési: Nyissa meg a napló kereséshez, ellenőrizze a lekérdezés és az eredményeket. Majd menteni, ebbe a kategóriába egyedi neve.

Amikor [egy napló riasztás létrehozása a riasztások (előzetes verzió)](monitor-alerts-unified-usage.md), megjelenik a korábban mentett lekérdezés jel típusként felsorolt **napló (mentett lekérdezés)**; az alábbi példában ismertetett módon: ![mentett lekérdezés riasztások importálása](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Használatával **napló (mentett lekérdezés)** riasztások van egy importálási eredmények. Az elemzés után végzett módosításokat ezért nem lesz tükröző mentett riasztási szabályok és fordítva.

## <a name="log-alert-rules"></a>Napló riasztási szabályok

Riasztások (előzetes verzió) az automatikus futtatásra napló lekérdezések rendszeres időközönként Azure riasztások jönnek létre.  Ha a napló lekérdezés eredményeit az adott feltételeknek megfelelő, egy riasztás rekord jön létre. A szabály automatikusan követően futtathatja egy vagy több műveletek proaktív értesítést küldenek, a figyelmeztetés vagy meg kíván hívni egy másik folyamat, például a külső alkalmazás használatával történő adatküldés [json-alapú webhook](monitor-alerts-unified-log-webhook.md)használatával [művelet csoportok](monitoring-action-groups.md). Különböző típusú riasztási szabályok az elemzés végrehajtásához használja a másik programot.

A riasztási szabályok határozzák meg a következő adatokat:

- **Lekérdezés jelentkezzen**.  A lekérdezés, amely futtatja a minden alkalommal, amikor a riasztási szabály következik be.  Ez a lekérdezés által visszaadott rekordok segítségével meghatározhatja, hogy riasztás jöjjön létre.
- **Időablak**.  Adja meg a lekérdezés időintervallumát.  A lekérdezés csak azokat a rekordokat adja vissza, amelyek az aktuális idő ezen tartományában jöttek létre.  Időablak tetszőleges érték 5 perc és 1440 perc és 24 óra közötti lehet. Például ha a időszak 60 percre van beállítva, és a lekérdezés futtatása, 1:15 előtti, csak a 12:15 előtti és 1:15 előtti között létrejövő rekordok ad vissza.
- **Gyakoriság**.  Meghatározza, hogy milyen gyakran kell futtatni a lekérdezést. Bármely érték 5 perc és 24 óra közötti lehet. A időszak kisebbnek vagy azzal egyenlőnek kell lennie.  Ha az érték nagyobb, mint az időszak, majd azzal kockáztatja alatt nem talált rekordokat.<br>Vegye figyelembe például egy olyan időkeretet, 30 perc és 60 perc gyakorisága.  Ha a lekérdezés futtatása, 1:00, 12:30 és 1:00 PM rekordok adja vissza.  A következő szeretné futtatni a lekérdezést ideje 2:00 amikor meghaladná a 1:30 és 2:00 között rögzíti.  1:00 és 1:30 között létrejövő rekordok volna soha nem értékelhető ki.
- **Küszöbérték**.  A naplófájl-keresési eredmények kiértékelése annak meghatározásához, hogy riasztást kell létrehozni.  A küszöbérték nem azonos a különböző típusú riasztási szabályok.

A Naplóelemzési minden riasztási szabály a két típus egyike.  Ezek a típusok leírását a következő szakaszok részletesen.

- **[Találatok száma](#number-of-results-alert-rules)**. Egyetlen riasztás jön létre, amikor a szám a napló keresés által visszaadott rekordok meghaladja a megadott értéket.
- **[Metrika mérési](#metric-measurement-alert-rules)**.  A megadott küszöbértéket meghaladó értékek napló keresés eredményeit az egyes objektumok létre riasztást.

Riasztási szabály típusa közötti különbségek a következők:

- ** Eredmények riasztási szabályok száma mindig létrehoz egy riasztás rövid **metrika mérési** riasztási szabály a minden objektumon meghaladja a küszöbértéket, riasztást hoz létre.
- **Találatok száma** riasztási szabályok riasztást hoznak létre, amikor a küszöbérték elérése esetén egy alkalommal. **Metrika mérési** riasztási szabályok riasztást hozhat létre, a küszöbérték túllépésekor bizonyos számú alkalommal keresztül egy adott időintervallumban.

## <a name="number-of-results-alert-rules"></a>Eredmények riasztási szabályok száma
**Találatok száma** riasztási szabályok egyetlen riasztás létrehozása, ha a keresési lekérdezés által visszaadott rekordok száma meghaladja a küszöbértéket. Ez a riasztási szabály típus ideális, ha olyan esemény, például a Windows eseménynaplóiban keresse meg, a Syslog, webalkalmazás válasz és egyéni naplói.  Érdemes lehet a riasztás létrehozása, ha egy adott hibaesemény jön létre, vagy ha több hibaesemények belül egy adott időkerete jönnek létre.

**Küszöbérték**: küszöbértéke egy ** eredmények riasztási szabályok száma nagyobb, mint vagy egy adott értéknél kisebb.  Ha a napló keresés által visszaadott rekordok számát a feltételeknek, egy riasztás jön létre.

Riasztás egy adott eseményhez, az eredmények csak 0-nál nagyobb meg, és keressen egy egyszeri esemény, a lekérdezés futtatása legutóbbi indítása óta létrehozott. Egyes alkalmazások jelentkezhetnek be, amely nem feltétlenül riasztás alkalmi hiba.  Az alkalmazás például újra a folyamatot a hibaesemény létrehozott és a következő alkalommal majd sikeres.  Ebben az esetben nem érdemes lehet a riasztás létrehozása, kivéve, ha több esemény egy adott időpontban időszakban jönnek létre.  

Bizonyos esetekben érdemes lehet egy esemény hiányában hozzon létre egy riasztást.  Például egy folyamat esetleg naplózása rendszeres annak jelzésére, hogy megfelelően működik-e.  Ha egy ilyen eseményt belül egy adott időkerete nem jelentkeznek, majd riasztást kell létrehozni.  Ebben az esetben akkor értékre kell állítania a küszöbérték **1-nél kevesebb**.

### <a name="example"></a>Példa
Egy olyan esetet, ahol szeretné megnézni, ha a webes alkalmazás biztosítja a felhasználók 500 kód válaszul (vagyis) belső kiszolgálóhiba. Riasztási szabály okozna a következő adatokkal:  
**Lekérdezés:** kérelmek |} ahol resultCode == "500"<br>
**Időablak:** 30 perc<br>
**Riasztási gyakoriságot:** öt perc<br>
**Küszöbérték:** kiváló 0-nál<br>

Majd riasztást fog futni a lekérdezés 5 percenként, az adatok - rekordot keres, ahol eredménykódja volt 500 30 perc. Ha található ilyen is egy rekord, akkor következik be, a riasztást, és elindítja a műveleteket.

## <a name="metric-measurement-alert-rules"></a>Metrika mérési riasztási szabályok

**Metrika mérési** riasztási szabályok létrehozása minden objektum egy riasztást a megadott küszöbértéket meghaladó érték lekérdezésben.  A következő különböző különbségeket rendelkeznek **eredmények száma** riasztási szabályok.

**Összesítő függvény**: meghatározza a számítás, amely történik, és potenciálisan egy numerikus összesítendő mező.  Például **count()** rekordok számát adja vissza a lekérdezésben **avg(CounterValue)** arra az időtartamra, a ellenértéknek mező átlagát adja vissza.

> [!NOTE]

> A lekérdezés aggregátumfüggvényt nevű/nevű kell lennie: AggregatedValue, és adjon meg számértéket. 


**Csoport mező**: egy rekord, egy összesített értéket ebben a mezőben minden példánya létrejön, és riasztás is generálható minden.  Például, ha az egyes számítógépek riasztás létrehozása, használhatja **számítógépenként**   

> [!NOTE]

> Metrika mérési riasztási szabályok az Application Insights alapuló megadhatja a mező az adatok csoportosításához. Ehhez használja a **összesített a** beállítást a szabályát leíró definíció beolvasása.   

**Időköz**: az időtartam alatt, amelyben az adatokat összesített értéket határoz meg.  Például, ha a megadott **öt perc**, létrehozott egy rekordot az a csoportmező, 5 perces időközönként a riasztás megadott időszak alatt összesített értéket minden egyes példányánál.
> [!NOTE]
> Bin függvény kell használható lekérdezésben. Bin() egyenlőtlen időközök - eredményezhet, riasztás fog helyette bin_at függvény használata megfelelő idő futásidőben, és rögzített pont eredmények biztosítása

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
* Megértéséhez [webhookműveletek napló riasztások](monitor-alerts-unified-log-webhook.md)
* [Azure riasztások (előzetes verzió) áttekintése](monitoring-overview-unified-alerts.md)
* További tudnivalók [Azure riasztások használatával (előzetes verzió)](monitor-alerts-unified-usage.md)
* További információ [Application insights szolgáltatással](../application-insights/app-insights-analytics.md)
* További információ [Naplóelemzési](../log-analytics/log-analytics-overview.md).    
