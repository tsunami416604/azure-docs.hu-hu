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
ms.openlocfilehash: 0e1cb2cdd6270590def11479cc5859d996d84caa
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50749034"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Az Azure Monitor riasztások hibaelhárítása  

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, kezeli a leggyakoribb problémáinak belül az Azure monitor riasztások beállítása közben. És adja meg a megoldás funkció- vagy naplóriasztások konfigurációját kapcsolatos gyakori kérdésekre. Az előfizetési időszak **Naplóriasztások** írja le a riasztásokhoz, ahol jel alapuló egyéni lekérdezés [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application Insights](../application-insights/app-insights-analytics.md). További információ funkciót, terminológiája és származó típust [Naplóriasztások – áttekintés](monitor-alerts-unified-log.md).

> [!NOTE]
> Ez a cikk nem tekinti esetek, amikor a riasztási szabály jelenik meg, az Azure Portalon és a társított művelet (ok) ban keresztül értesítés aktiválódik. Ezekben az esetekben, olvassa el a részleteket a cikkben a [Műveletcsoportok](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Riasztás nem aktiválódik.

Részletes következő miért néhány általános oka egy konfigurált [riasztási szabály az Azure monitorban](alert-log.md) nem get aktiválódik, ha a megtekintett [Azure Alerts](monitoring-alerts-managing-alert-states.md), ha a várt fired. 

### <a name="data-ingestion-time-for-logs"></a>Naplók adatok betöltési ideje
Riasztási works jelentkezzen rendszeres időközönként fut az ügyfél a megadott lekérdezés alapján [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) vagy [Application Insights](../application-insights/app-insights-analytics.md). Analytics szolgáltatásnak, amely feldolgozza a naplóadatokat nagy mennyiségű, és ugyanazon funkciókat biztosítja hatékonyságát is működteti. Ahogy a Log Analytics szolgáltatás magában foglalja a világszerte több terabájt több ezer ügyfelünk, és a különféle forrásokból származó adatok feldolgozása – a szolgáltatás ki van téve a késést. További információkért lásd: [adatok betöltési idő a Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Fordulhat elő, a Log Analytics vagy az Application Insights-logs; adatok adatfeldolgozási késleltetés, hogy riasztás vár, és ha úgy találja, a riasztási adott időszakban nem még betöltött adatok egy kis idő múlva újrapróbálkozik. Naplóriasztások rendelkezik egy ezzel exponenciálisan növelve várakozási idő, győződjön meg arról, hogy várjon szükséges idő a Log Analytics által adatmennyiség állapotúra. Ezért ha a naplók a riasztási szabály által lekérdezett feldolgozási késedelem van hatással, majd riasztás aktiválják csak után az adatokat a Log Analytics utáni adatfeldolgozást és exponenciális kihagyást miatt log riasztási szolgáltatás többször is feldolgozza a főág újrapróbálkozás után elérhető .

### <a name="incorrect-time-period-configured"></a>Helytelen időtartam konfigurálva
A cikkben leírtak szerint [naplóriasztások terminológiája](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types), a konfigurációban megadott időszak meghatározza az időtartományt a lekérdezés. A lekérdezés csak a idő ezen tartományában jöttek létre rekordokat adja vissza. Adott időszakban korlátozza az adatokat, a visszaélések megelőzése érdekében naplólekérdezés beolvasott, és minden olyan alkalommal parancs megkerüli (például ezelőtt) napló lekérdezésben használt. 
*Például ha az adott időszakban 60 percre van beállítva, és a lekérdezés futtatásakor: 1:15-kor, csak a rekordok között 12:15-kor és 1:15-kor létrehozott ad vissza log lekérdezés végrehajtásához. Ha a napló lekérdezés paranccsal például ezelőtt időt használ (1d), a naplólekérdezés fogja futtatni a rendszer csak a 12:15-kor és 1:15 PM - adatait, mintha az adatok csak az elmúlt 60 perc alatt állnak. Hét nap adatait a lekérdezési napló esetében nem.*

A lekérdezés logika alapján, ellenőrizze, ha a konfiguráció megfelelő időtartam lett megadva. A példában korábban említettük, ha a napló lekérdezése ezelőtt használ (1d) a látható módon Zöld jelölő –, akkor az adott időszakban kell beállítani vagy 1440 perc – 24 óra (a szerint piros színnel), annak biztosítása érdekében a megadott lekérdezés végrehajtja a megfelelő felvázolt.
    ![Adott időszakban](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Beállítás riasztások mellőzése
A 8. lépés a cikkben leírtak szerint [egy riasztási szabály létrehozása az Azure Portalon](alert-log.md#managing-log-alerts-from-the-azure-portal), riasztás lehetőséget biztosít az automatikus letiltása a riasztási szabály konfigurálása és értesítési vagy trigger elkerülése az előírt időn. Riasztások mellőzése a beállítás hatására a végrehajtása során nem elindítása a megadott ideig műveletcsoport riasztás **riasztások mellőzése** lehetőséget, és ezért felhasználói úgy, hogy a riasztás nem aktiválódik, amíg a actuality, a rendszer mellőzte az konfigurálva .
    ![Riasztások mellőzése](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Helytelen metrikamérési riasztási szabályt
Metrikamérési riasztási szabály típus altípusa riasztások, amelyek speciális képességekkel rendelkeznek, de a riasztási lekérdezés szintaxisa korlátozását ezután alkalmaz. Metrikus egység naplóriasztási szabály igényel, adjon meg egy metrika idősorozat - lekérdezés kimenete a különböző egyenlő méretű időszakok együtt tartozó értékek, AggregatedValue tartalmazó tábla számított. Ezenkívül felhasználók is választja, hogy a táblázat további változókban mellett AggregatedValue, mint például a számítógép, Node stb. a táblázatban szereplő adatok segítségével lehet rendezni.

Tegyük fel, hogy metrikamérési riasztási szabály úgy lett konfigurálva, mint:
- lekérdezés:: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6 órás időtartammal
- az 50 küszöbérték
- három egymás utáni incidensek a riasztási logika
- Összesített esetén $table céltárhelyként

Mivel a parancs azt használt most összefoglalója... által és a két változót: időbélyeg & $table; szolgáltatás "Összesített esetén" – alapvetően $table választja ki a riasztás az eredményként kapott tábla mező szerint rendezni: $table – ahogy az alábbi és a több AggregatedValue minden egyes aztán nézzük típusa (például az availabilityResults) megtekintéséhez, ha 3 vagy több egymást követő megsértése történt.

   ![Több értékkel rendelkező metrika mérési lekérdezés végrehajtása](./media/monitor-alerts-unified/LogMMQuery.png)

Mivel "Összesített esetén" $table – az adatok van alapján vannak rendezve, $table oszlopot (ahogy (piros); majd azt a csoportot, és keresse meg a mező "Összesített esetén" típusú (vagyis) $table – például: availabilityResults egy diagram/entitás (a legyenek kiemelve a narancssárga) is figyelembe veszi a értékei. Az entitás/érték diagram – riasztási szolgáltatás ellenőrzi (a bemutatott zöld) előforduló három egymás utáni incidensek mely a riasztás első aktiválódik a "availabilityResults" érték. Hasonlóképpen ha három egymás utáni incidensek - $table semmilyen más érték a egy másik riasztási értesítés akkor aktiválódik, ugyanazon; a riasztási szolgáltatás automatikusan rendezési egy diagram/entitás (ahogy narancssárga) szereplő értékek időpontig.

Most tegyük fel, metrikamérési riasztási szabály módosítva lett, és a lekérdezés: `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` többi előtt, beleértve a riasztási logika három egymás utáni incidensek esetén is ugyanaz, mint a fennmaradó a config. "Aggregate során" lehetőség alapértelmezés szerint ebben az esetben lesz: időbélyeg. Mivel csak egy érték van megadva, a lekérdezés szempontja... timestamp (vagyis); hasonló végrehajtási végén a korábbi példában a kimeneti lenne, az alábbi ábra szemlélteti. 

   ![Metrika mérési lekérdezés-végrehajtás egyetlen értékkel](./media/monitor-alerts-unified/LogMMtimestamp.png)

"Összesített esetén" időbélyeg – az adatok az időbélyegző-oszlopot (ahogy (piros); van rendezve. Ezután azt csoportosítás időbélyeg – például: értékei `2018-10-17T06:00:00Z` egy diagram/entitás (a legyenek kiemelve a narancssárga) minősül. Az entitás/érték diagram – a riasztási szolgáltatás megtalálja, nem egymás utáni incidensek előforduló (az egyes időbélyegző-érték csak egy bejegyzést tartalmaz), és ezért riasztási rendszer soha nem aktiválódott beolvasása. Ezért ebben az esetben felhasználói kell vagy-
- Adjon hozzá egy helyőrző a változó vagy egy létező változó (például $table) megfelelően rendezési kész konfigurált "Aggregate esetén" mező használatával
- (Vagy) konfigurálja újra a riasztási logika alapján használandó riasztási szabályt *megsértésére teljes* inkább megfelelően
 
## <a name="log-alert-fired-unnecessarily"></a>Feleslegesen aktivált riasztás
Részletes következő miért néhány általános oka egy konfigurált [riasztási szabály az Azure monitorban](alert-log.md) megtekintve indítható [Azure Alerts](monitoring-alerts-managing-alert-states.md), amikor nem a várt fired.

### <a name="alert-triggered-by-partial-data"></a>Részleges adatok által aktivált riasztás
Kockázatszámító a Log Analytics és az Application Insights Analytics vonatkoznak rá a feldolgozási késedelmeket és -feldolgozási; amely miatt a megadott naplóriasztás-lekérdezés futtatásakor - időpontjában lehet egy esetet folyamatban van a rendelkezésre álló adatok nem vagy csak bizonyos adatok legyenek elérhetők. További információkért lásd: [adatok betöltési idő a Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

A riasztási szabály konfigurációjától függően előfordulhat, esetleg Gyújtóegységek abban az esetben, ha nincs vagy részleges naplók riasztási végrehajtás időpontjában. Ezekben az esetekben célszerű riasztási lekérdezés vagy a konfiguráció módosult. *Például ha a napló riasztási szabály úgy indítható el, ha elemzési lekérdezésből találatok száma kisebb, mint 5; (például) Ezután ha nem adatok (nulla rekord) vagy a részleges eredményeket (egy rekord) a riasztási szabály első aktiválódik. Ahol-feldolgozási késedelem után ugyanabból a lekérdezés futtatásakor az Analyticsben a lekérdezés az összes adat biztosíthatnak eredmény 10 rögzíti.*

### <a name="alert-query-output-misunderstood"></a>Böngésző riasztási lekérdezés kimenete
A riasztási logika naplóriasztásokra vonatkozó keresztül elemzési lekérdezés felhasználó által megadott. A megadott elemzési lekérdezés azonban különböző Big Data és matematikai függvények létrehozása adott szerkezeteket. A riasztási szolgáltatás; a megadott időszakra vonatkozó adatokat a megadott időközönként hajtja végre a vásárló által biztosított lekérdezés riasztási szolgáltatás lehetővé teszi a változás is módosítja a lekérdezéshez megadott – a kiválasztott riasztási típus alapján, és azonos is lehet witnessed konfigurálási jel logikájának képernyőn "Végrehajtandó lekérdezés" szakaszának alábbi képen szemléltetett módon: ![végrehajtandó lekérdezés](./media/monitor-alerts-unified/LogAlertPreview.png)
 
Mi látható **végrehajtandó lekérdezés** rész az mely log riasztási szolgáltatás fut; a felhasználó futtathatja a megadott lekérdezés, valamint a keresztül timespan [Analytics-portál](../log-analytics/log-analytics-log-search-portals.md) vagy [szövegelemzési API](https://docs.microsoft.com/en-us/rest/api/loganalytics/) -riasztás létrehozása előtt tudni szeretnék, ha lehet, milyen riasztási lekérdezés kimenetelét.
 
## <a name="next-steps"></a>További lépések

* Ismerje meg [Naplóriasztások az Azure-riasztások](monitor-alerts-unified-log.md)
* Tudjon meg többet [Application Insights](../application-insights/app-insights-analytics.md)
* Tudjon meg többet [Log Analytics](../log-analytics/log-analytics-overview.md). 