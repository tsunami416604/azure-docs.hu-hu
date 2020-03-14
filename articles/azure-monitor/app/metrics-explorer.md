---
title: Mérőszámok feltárása az Azure Application Insightsban | Microsoft Docs
description: Diagramok értelmezése a metrika-kezelőben és a metrikai Explorer-lapok testreszabása.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275892"
---
# <a name="exploring-metrics-in-application-insights"></a>A metrikák feltárása Application Insights
A [Application Insights][start] mérőszámai az alkalmazásból telemetria eljuttatott események értékeit és számát mérik. Segítenek észlelni a teljesítménnyel kapcsolatos problémákat, és figyeli az alkalmazás használatának folyamatát. A standard mérőszámok széles skáláját használhatja, és saját egyéni metrikákat és eseményeket is létrehozhat.

> [!NOTE]
> Ez a cikk a klasszikus metrikák Explorer-élményt mutatja be, amely jelenleg elavult, és végül kimarad. Javasoljuk, hogy tekintse meg az [ebben a cikkben](../platform/metrics-charts.md)ismertetett új felhasználói élményt.

A metrikák és események száma az összesített értékek (például összegek, átlagok vagy darabszámok) diagramjaiban jelenik meg.

Íme egy példa a diagramokra:

![](./media/metrics-explorer/01-overview.png)

A metrikák diagramjai mindenhol a Application Insights-portálon találhatók. A legtöbb esetben testreszabhatók, és több diagramot is hozzáadhat a panelhez. Az Áttekintés panelen kattintson a through (többek között a "kiszolgálók") részletes diagramjaira, vagy kattintson a **Metrikaböngésző** elemre egy új panel megnyitásához, ahol egyéni diagramokat hozhat létre.

## <a name="time-range"></a>Időtartomány
Bármely panelen módosíthatja a diagramok vagy rácsok által jelzett időtartományt.

![Nyissa meg az alkalmazás áttekintés paneljét a Azure Portal](./media/metrics-explorer/03-range.png)

Ha olyan adatokra van szüksége, amely még nem jelent meg, kattintson a frissítés gombra. A diagramok időközönként frissülnek, de az intervallumok hosszabbak a nagyobb időtartományok esetében. Eltarthat egy ideig, amíg az adatelemzési folyamat átkerül egy diagramra.

A diagram egy részének nagyításához húzza át a következőre:

![Húzza át a diagram egy részét.](./media/metrics-explorer/12-drag.png)

A visszaállításhoz kattintson a nagyítás visszavonása gombra.

## <a name="granularity-and-point-values"></a>Részletesség és pontok értékei
Vigye az egérmutatót a diagram fölé, és jelenítse meg a metrikák értékeit ezen a ponton.

![Vigye az egérmutatót egy diagram fölé](./media/metrics-explorer/02-focus.png)

Egy adott pont metrikájának értéke az előző mintavételi időköz szerint lesz összesítve.

A mintavételi időköz vagy a "részletesség" a panel tetején jelenik meg.

![Egy panel fejléce](./media/metrics-explorer/11-grain.png)

A részletesség az időtartomány panelen állítható be:

![Egy panel fejléce](./media/metrics-explorer/grain.png)

Az elérhető részletességi beállítások a kiválasztott időtartománytól függenek. A explicit részletesség az időtartomány "automatikus" részletességének alternatívája.


## <a name="editing-charts-and-grids"></a>Diagramok és rácsok szerkesztése
Új diagram hozzáadása a panelhez:

![A Metrikaböngésző területen válassza a diagram hozzáadása elemet.](./media/metrics-explorer/04-add.png)

Válassza a **Szerkesztés** lehetőséget egy meglévő vagy új diagramon a megjelenített elemek szerkesztéséhez:

![Válasszon ki egy vagy több mérőszámot](./media/metrics-explorer/08-select.png)

A diagramon több metrika is megjeleníthető, de a kombinációk is megjelenhetnek egymással. Amint kiválaszt egy metrikát, a többiek némelyike le van tiltva.

Ha [Egyéni metrikákat][track] kódolt az alkalmazásba (TrackMetric és TrackEvent-hívásokat hív meg), azok itt lesznek felsorolva.

## <a name="segment-your-data"></a>Az adatai szegmentálása
A mérőszámokat tulajdonság alapján is feloszthatja – például a különböző operációs rendszerekkel rendelkező ügyfeleken lévő oldalmegtekintések összehasonlításához.

Válasszon ki egy diagramot vagy rácsvonalakat, kapcsolja be a csoportosítást, és válasszon ki egy tulajdonságot a csoportosításhoz:

![Válassza a csoportosítás lehetőséget, majd állítsa be a tulajdonságot a csoportban](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> A csoportosítás használatakor a munkaterület és a sávdiagram típusa halmozott megjelenítést biztosít. Ez akkor megfelelő, ha az összesítési módszer összege. Ha azonban az összesítés típusa átlagos, válassza a vonal vagy a rács megjelenítési típusait.
>
>

Ha [Egyéni metrikákat][track] kódolt az alkalmazásba, és tulajdonságokat tartalmaz, a listában kiválaszthatja a tulajdonságot.

Túl kicsi a diagram a szegmentált adathoz? Állítsa be a magasságát:

![A csúszka igazítása](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Összesítési típusok
Az oldal jelmagyarázata alapértelmezés szerint általában az összesített értéket jeleníti meg a diagram időszakában. Ha a diagram fölé viszi a mutatót, az azt jelzi, hogy az érték ekkora.

A diagram minden adatpontja az előző mintavételi intervallumban vagy a "részletesség" során kapott adatértékek összesítése. A részletesség a panel tetején látható, és a diagram teljes időkeretén belülre változik.

A metrikák többféleképpen is összevonhatók:

* A **Count** a mintavételi időszakban fogadott események száma. Olyan eseményekhez használatos, mint a kérelmek. A diagram magasságában szereplő változatok azt jelzik, hogy az események milyen arányban történnek. Vegye figyelembe azonban, hogy a numerikus érték a Mintavételezési időköz módosításakor változik.
* Az **összeg** felveszi a mintavételi intervallumban fogadott adatpontok értékeit vagy a diagram időtartamát.
* Az **átlag** az adott intervallumban fogadott adatpontok számával osztja el az összeget.
* A felhasználók és a fiókok száma **egyedi** számokkal történik. A mintavételi időköz vagy a diagram időszaka felett az ábrán az adott időszakban látható különböző felhasználók száma látható.
* **%** – az egyes összesítések százalékos verziói csak szegmentált diagramokkal használhatók. Az összeg minden esetben akár 100%-ot is felvesz, a diagram pedig a teljes összeg különböző összetevőinek relatív hozzájárulását mutatja.

    ![Százalékos összesítés](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Az Összesítés típusának módosítása

![Szerkessze a diagramot, majd válassza az Összesítés lehetőséget.](./media/metrics-explorer/05-aggregation.png)

Az egyes mérőszámok alapértelmezett metódusa akkor jelenik meg, ha új diagramot hoz létre, vagy ha az összes metrika ki van választva:

![Az alapértelmezett értékek megjelenítéséhez törölje az összes mérőszám kijelölését](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Y tengely rögzítése 
Alapértelmezés szerint a diagram az Y tengely értékeit jeleníti meg az adattartományban a nullától egészen a maximális értéktől kezdve, hogy vizuálisan ábrázolja az értékek kvantumját. Bizonyos esetekben azonban előfordulhat, hogy az értékek kisebb változásainak vizuális vizsgálatára is érdekes lehet. Az ehhez hasonló testreszabásokhoz az Y tengely tartományának szerkesztési funkciója segítségével rögzítheti az Y tengely minimális vagy maximális értékét a kívánt helyen.
Kattintson a "speciális beállítások" jelölőnégyzetre az Y tengely tartományának beállításainak kikapcsolásához.

![Kattintson a speciális beállítások elemre, válassza az egyéni tartomány lehetőséget, és adja meg a maximális értékeket.](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Az adatai szűrése
A tulajdonságértékek kiválasztott készletéhez tartozó mérőszámok megtekintéséhez:

![Kattintson a szűrő elemre, bontsa ki a tulajdonságot, és jelölje be a kívánt értékeket.](./media/metrics-explorer/19-filter.png)

Ha nem választ ki egy adott tulajdonság értékeit, akkor ugyanaz, mint az összes kijelölése: az adott tulajdonsághoz nincs szűrő.

Figyelje meg az események számát az egyes tulajdonságértékek mellett. Ha egy tulajdonság értékeit választja, a többi tulajdonság értéke mellett a Count is módosul.

A szűrők a panel összes diagramján érvényesek. Ha eltérő szűrőket szeretne alkalmazni különböző diagramokra, hozzon létre és mentsen különböző metrikákat. Ha szeretné, a különböző pengék diagramjait rögzítheti az irányítópulton, így egymás mellett is megtekintheti őket.

### <a name="remove-bot-and-web-test-traffic"></a>A robot és a webes teszt forgalmának eltávolítása
Használja a **valós vagy szintetikus forgalom** szűrését, és győződjön meg róla, hogy **valós**.

Azt is megteheti, hogy **a szintetikus forgalom forrása**alapján szűri.

### <a name="to-add-properties-to-the-filter-list"></a>Tulajdonságok hozzáadása a szűrő listához
Szeretné szűrni a telemetria a saját választása szerint? Előfordulhat például, hogy a felhasználók különböző kategóriákba vannak osztva, és ezeket a kategóriákat szeretné szegmentálni.

[Hozzon létre egy saját tulajdonságot](../../azure-monitor/app/api-custom-events-metrics.md#properties). Állítsa be egy [telemetria-inicializálásban](../../azure-monitor/app/api-custom-events-metrics.md#defaults) úgy, hogy az minden telemetria megjelenjen – beleértve a különböző SDK-modulok által eljuttatott szabványos telemetria is.

## <a name="edit-the-chart-type"></a>A diagram típusának szerkesztése
Figyelje meg, hogy válthat a rácsok és a diagramok között:

![Válasszon ki egy rácsvonalat vagy diagramot, majd válassza ki a diagram típusát.](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>A metrikák panel mentése
Egyes diagramok létrehozásakor kedvencként mentheti őket. Ha szervezeti fiókot használ, kiválaszthatja, hogy meg kívánja-e osztani a csoport többi tagjával.

![Kedvenc kiválasztása](./media/metrics-explorer/21-favorite-save.png)

Ha ismét látni szeretné a panelt, **ugorjon az Áttekintés** panelre, és nyissa meg a kedvencek:

![Az Áttekintés panelen válassza a Kedvencek lehetőséget.](./media/metrics-explorer/22-favorite-get.png)

Ha a relatív időtartományt a mentéskor választotta, a panel a legújabb metrikákkal lesz frissítve. Ha az abszolút időtartomány lehetőséget választotta, minden alkalommal ugyanazokat az adatsorokat fogja megjeleníteni.

## <a name="reset-the-blade"></a>A panel alaphelyzetbe állítása
Ha szerkeszt egy panelt, de vissza szeretné állítani az eredeti mentett készletet, egyszerűen kattintson az Alaphelyzetbe állítás gombra.

![A metrikai Explorer felső részén található gombokkal](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Élő mérőszámok streamje

A telemetria sokkal jobb azonnali megjelenítéséhez nyissa meg [élő stream](live-stream.md). A legtöbb metrika az összesítési folyamat miatt néhány percet vesz igénybe. Ezzel szemben az élő metrikák alacsony késésre vannak optimalizálva. 

## <a name="set-alerts"></a>Riasztások beállítása
Ha bármilyen metrika szokatlan értékeit e-mailben szeretné értesíteni, adjon hozzá egy riasztást. Választhatja azt is, hogy az e-mailt a fiók rendszergazdái vagy adott e-mail-címei számára küldje el.

![A Metrikaböngésző területen válassza a riasztási szabályok, majd a riasztás hozzáadása elemet.](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[További információ a riasztásokról][alerts].


## <a name="continuous-export"></a>Folyamatos exportálás
Ha azt szeretné, hogy a rendszer folyamatosan exportálja az adatexportálást, hogy külsőleg is feldolgozza, érdemes lehet [folyamatos exportálást](../../azure-monitor/app/export-telemetry.md)használni.

### <a name="power-bi"></a>Power BI
Ha az adatait még gazdagabban szeretné megtekinteni, [exportálhatja Power BIba](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Elemzés
Az [elemzés](../../azure-monitor/app/analytics.md) sokoldalú módszert használ a telemetria hatékony lekérdezési nyelv használatával történő elemzéséhez. Akkor használja, ha a metrikák eredményeinek összevonásához vagy kiszámításához, illetve az alkalmazás legutóbbi teljesítményének részletes feltárásához is szükség van. 

Egy metrikai diagramon az elemzés ikonra kattintva közvetlenül elérheti az egyenértékű elemzési lekérdezéseket.

## <a name="troubleshooting"></a>Hibakeresés
*Nem látok semmilyen információt a diagramon.*

* A szűrők a panel összes diagramján érvényesek. Győződjön meg arról, hogy az egyik diagramra való összpontosítás közben nem állított be olyan szűrőt, amely kizárja az összes adathalmazt egy másikon.

    Ha különböző szűrőket szeretne beállítani különböző diagramokon, hozzon létre különböző lapokon, majd mentse őket külön kedvencekként. Ha szeretné, rögzítheti őket az irányítópulton, hogy azok egymás mellett is megjelenjenek.
* Ha olyan tulajdonság szerint csoportosít egy diagramot, amely nincs meghatározva a metrikán, akkor a diagramon nem lesz semmi. Próbálja meg törölni a "Group By" parancsot, vagy válasszon másik csoportosítási tulajdonságot.
* A Teljesítményadatok (CPU, IO-sebesség stb.) a Java-webszolgáltatásokhoz, a Windows asztali alkalmazásokhoz, [az IIS-webalkalmazásokhoz és-szolgáltatásokhoz](../../azure-monitor/app/monitor-performance-live-website-now.md)érhetők el, ha telepíti az állapotfigyelő szolgáltatást és az [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Az Azure-webhelyekhez nem érhető el.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Következő lépések
* [Használat figyelése Application Insights](../../azure-monitor/app/usage-overview.md)
* [A diagnosztikai keresés használata](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
