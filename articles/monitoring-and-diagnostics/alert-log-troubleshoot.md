---
title: Az Azure Monitor riasztások hibaelhárítása
description: Gyakori problémák, a hibák és a megoldás a log riasztási szabályok az Azure-ban.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 94c03c9aa6e361167b396af5218b308e6cacfafe
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879808"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Az Azure Monitor riasztások hibaelhárítása  
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állítson be az Azure monitor riasztások leggyakoribb problémáinak megoldásához. Gyakori kérdés a funkció- vagy naplóriasztások konfigurációját megoldásokat is biztosít. 

Az előfizetési időszak **Naplóriasztások** ismerteti, hogy az egyéni lekérdezés alapján fire riasztások [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application Insights](../application-insights/app-insights-analytics.md). További információ funkciót, terminológia és-típusok a [Naplóriasztások – áttekintés](monitor-alerts-unified-log.md).

> [!NOTE]
> Ez a cikk nem tekinti esetekben, amikor az Azure Portalon látható és a szabály által aktivált riasztás és a egy társított művelet (ok) ban által végrehajtott értesítést. Ezekben az esetekben, olvassa el a részleteket a cikkben a [Műveletcsoportok](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Riasztás nem aktiválódik.

Az alábbiakban néhány gyakori okáról miért egy konfigurált [riasztási szabály az Azure monitorban](alert-log.md) állapota nem jelenik meg [, *aktivált* várt](monitoring-alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Naplók adatok betöltési ideje
Riasztás rendszeres időközönként fut a lekérdezés alapján [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application Insights](../application-insights/app-insights-analytics.md). A Log Analytics világszerte több ezer ügyfelünk különböző forrásokból származó adatok több terabájt feldolgozza, mert a szolgáltatás ki van téve a változó késleltetés. További információkért lásd: [adatok betöltési idő a Log Analytics](../azure-monitor/platform/data-ingestion-time.md).

Adatok Adatbetöltési késés csökkentése érdekében, a rendszer megvárja, és újrapróbálkozik a többször is feldolgozza a riasztási lekérdezés Ha megtalálja, hogy a szükséges adatok még nem elemezhető. A rendszer állítsa ezzel exponenciálisan növelve várakozási időt tartalmaz. A napló riasztási csak eseményindítók után az adatok érhető el, így azok késleltetés lassú lekérdezések naplóját adatbetöltés okozhatja. 

### <a name="incorrect-time-period-configured"></a>Helytelen időtartam konfigurálva
A cikkben leírtak szerint [naplóriasztások terminológiája](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types)az idő, a megadott időszak konfigurációs meghatározza az időtartományt a lekérdezés. A lekérdezés csak a idő ezen tartományában jöttek létre rekordokat adja vissza. Adott időszakban korlátozza az adatokat, a visszaélések megelőzése érdekében naplólekérdezés beolvasott, és minden olyan alkalommal parancs megkerüli (például *ezelőtt*) napló lekérdezésben használt. Például ha az adott időszakban 60 percre van beállítva, és a lekérdezés futtatásakor: 1:15-kor, csak a rekordok között 12:15-kor és 1:15-kor létrehozott szolgálnak a napló lekérdezés. Ha a napló-lekérdezést használ hasonló idő parancsot *ezelőtt (1d)*, a lekérdezés még mindig csak használja a 12:15-kor és 1:15-kor közötti, mert az adott időszakban, hogy interval.* értékre van állítva

Ezért mindig ellenőrizze a konfiguráció megfelel az adott időintervallumban a lekérdezést. A példához korábban említettük, ha a napló lekérdezés *ezelőtt (1d)* ahogyan a Zöld jelölő, majd az adott időszakban kell állítani 24 órás vagy 1440 perc (mint a vörös), a lekérdezés végrehajtása szánt biztosításához.

![Adott időszakban](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Beállítás riasztások mellőzése
A 8. lépés a cikkben leírtak szerint [egy riasztási szabály létrehozása az Azure Portalon](alert-log.md#managing-log-alerts-from-the-azure-portal), riasztások adjon meg egy **riasztások mellőzése** elindítása és az értesítési műveletek elrejtését konfigurált mennyisége eltelt idő. Ennek eredményeképpen előfordulhat, hogy úgy gondolja, hogy a riasztást a actuality volt, de a rendszer mellőzte az nem aktiválódik.  

![Riasztások mellőzése](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Helytelen metrikamérési riasztási szabályt
**Metrikus egység naplóriasztások** riasztások, amelyek speciális képességek és a egy korlátozott riasztási lekérdezési szintaxis altípusa vannak. Egy metrikamérési riasztási szabály kell lennie egy metrika idősorozat; kimeneti a lekérdezés egy különálló táblázat, hogy egyenlő méretű időszakoknak, valamint a megfelelő összesített értékeket. Ezenkívül felhasználók is választja, hogy a további változókat AggregatedValue mellett a táblázatban. Ezek a változók rendezi a táblát is használható. 

Tegyük fel például, egy metrikamérési riasztási szabály úgy lett konfigurálva, mint:
- lekérdezés:: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6 órás időtartammal
- az 50 küszöbérték
- három egymás utáni incidensek a riasztási logika
- Összesített esetén $table céltárhelyként

Mivel a parancs tartalmazza *összegzés szempontja* és a két változót (timestamp & $table), a rendszer $table az összesített alapján választja. Az eredményként kapott tábla mező szerint rendezi *$table* alább látható módon, és ezután megvizsgálja az egyes táblatípus (például az availabilityResults) több AggregatedValue megtekintheti, ha 3 vagy több egymást követő megsértése történt.

![Több értékkel rendelkező metrika mérési lekérdezés végrehajtása](./media/monitor-alerts-unified/LogMMQuery.png)

Mivel összesített esetén $table $table oszlopot (ahogy (piros); a rendezett adatok majd azt a csoportot, és típusú mező összesített után keresse meg (azaz) például $table: availabilityResults egy diagram/entitás (a legyenek kiemelve a narancssárga) is figyelembe veszi a értékei. Az érték diagram/entitás riasztási szolgáltatás ellenőrzi (a bemutatott zöld) előforduló három egymás utáni incidensek mely a riasztás első aktiválódik a "availabilityResults" érték. Hasonlóképpen ha három egymás utáni incidensek - $table semmilyen más érték a egy másik riasztási értesítés akkor aktiválódik, ugyanarra a dologra; a riasztási szolgáltatás automatikusan rendezési egy diagram/entitás (ahogy narancssárga) szereplő értékek időpontig.

Most tegyük fel, metrikamérési riasztási szabály módosítva lett, és a lekérdezés: `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` többi előtt, beleértve a riasztási logika három egymás utáni incidensek esetén is ugyanaz, mint a fennmaradó a config. "Aggregate során" lehetőség alapértelmezés szerint ebben az esetben lesz: időbélyeg. Mivel csak egy érték van megadva, a lekérdezés az összegzés szempontja timestamp (vagyis); hasonló végrehajtási végén a korábbi példában a kimeneti lenne, az alábbi ábra szemlélteti. 

   ![Metrika mérési lekérdezés-végrehajtás egyetlen értékkel](./media/monitor-alerts-unified/LogMMtimestamp.png)

Mivel összesített esetén az adatok időbélyegző-oszlopot (ahogy (piros); alapján vannak rendezve, van időbélyegző Ezután azt csoportosítás időbélyeg például: értékei `2018-10-17T06:00:00Z` egy diagram/entitás (a legyenek kiemelve a narancssárga) minősül. Az érték diagram/entitás riasztási szolgáltatás megtalálja, nem egymás utáni incidensek előforduló (az egyes időbélyegző-érték csak egy bejegyzést tartalmaz), és ezért riasztási rendszer soha nem aktiválódott beolvasása. Ezért ebben az esetben felhasználói kell vagy-
- Adjon hozzá egy helyőrző a változó vagy egy létező változó (például $table) megfelelően rendezési kész konfigurált "Aggregate esetén" mező használatával
- (Vagy) konfigurálja újra a riasztási logika alapján használandó riasztási szabályt *megsértésére teljes* inkább megfelelően
 
## <a name="log-alert-fired-unnecessarily"></a>Feleslegesen aktivált riasztás
Részletes következő miért néhány általános oka egy konfigurált [riasztási szabály az Azure monitorban](alert-log.md) megtekintve indítható [Azure Alerts](monitoring-alerts-managing-alert-states.md), amikor nem a várt fired.

### <a name="alert-triggered-by-partial-data"></a>Részleges adatok által aktivált riasztás
Kockázatszámító a Log Analytics és az Application Insights Analytics vonatkoznak rá a feldolgozási késedelmeket és -feldolgozási; amely miatt a megadott naplóriasztás-lekérdezés futtatásakor - időpontjában lehet egy esetet folyamatban van a rendelkezésre álló adatok nem vagy csak bizonyos adatok legyenek elérhetők. További információkért lásd: [adatok betöltési idő a Log Analytics](../azure-monitor/platform/data-ingestion-time.md).

A riasztási szabály konfigurációjától függően előfordulhat gépeltünk Gyújtóegységek esetén nincs vagy részleges naplók riasztási végrehajtás időpontjában. Ezekben az esetekben azt javasoljuk, hogy a riasztási lekérdezés vagy a konfiguráció módosítását. 

Például, ha a riasztási szabály úgy van beállítva, ha analytics-lekérdezések eredményeinek száma kisebb, mint 5 indít, majd a riasztást aktivál, ha nem adatok (nulla rekord) vagy a részleges eredményeket (egy rekord). Az adatok feldolgozási késleltetés után azonban ugyanabból a lekérdezés az összes adat biztosíthatnak 10 rekordok eredménye.

### <a name="alert-query-output-misunderstood"></a>Böngésző riasztási lekérdezés kimenete
A logic a log analytics-lekérdezések riasztásokhoz adja meg. Az elemzési lekérdezés big data- és matematikai függvények használhatja.  A riasztási szolgáltatás megadott időszakra vonatkozó adatokat tartalmazó megadott időközönként hajtja végre a lekérdezést. A riasztási szolgáltatás lehetővé teszi, hogy a megadott lekérdezés változás is történt a kiválasztott riasztási típus alapján. Ez látható a "Lekérdezés futtatandó" szakaszában *jellogika konfigurálása* képernyőn, ahogy az alábbi: ![végrehajtandó lekérdezés](./media/monitor-alerts-unified/LogAlertPreview.png)
 
Mi látható az **végrehajtandó lekérdezés** mező el a napló riasztási szolgáltatás futtatható. A megadott lekérdezés, valamint a timespan keresztül futtathatja [Analytics-portál](../azure-monitor/log-query/portals.md) vagy a [szövegelemzési API](https://docs.microsoft.com/rest/api/loganalytics/) Ha szeretné-e megismerni, mi a riasztási lekérdezés kimeneti lehet, mielőtt ténylegesen a riasztás létrehozásához.
 
## <a name="next-steps"></a>További lépések

* Ismerje meg [Naplóriasztások az Azure-riasztások](monitor-alerts-unified-log.md)
* Tudjon meg többet [Application Insights](../application-insights/app-insights-analytics.md)
* Tudjon meg többet [Log Analytics](../log-analytics/log-analytics-overview.md). 

