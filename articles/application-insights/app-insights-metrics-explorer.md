---
title: "Az Azure Application Insightsban metrikák felfedezése |} Microsoft Docs"
description: "A metrika explorer diagramok értelmezése és metrika explorer panelek testreszabása."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: mbullwin
ms.openlocfilehash: 01b45323b74b54da157f4e9f1af783759c121be1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="exploring-metrics-in-application-insights"></a>Az Application Insightsban metrikák felfedezése
A metrikák [Application Insights] [ start] mért értékek és az alkalmazásból küldött telemetriai az események számát. Segítenek azoknak teljesítményproblémák észlelését, és tekintse meg az alkalmazás használatának alakulását. Számos különböző szabványos metrikákat, és a saját egyéni metrikákkal és eseményekkel is létrehozhat.

Metrikák és az esemény számát, például összegeket, átlagok vagy számok összesített értékek diagramokban jelennek meg.

Itt egy olyan minta diagramok:

![](./media/app-insights-metrics-explorer/01-overview.png)

Metrikák diagramok mindenhol az Application Insights portálon találja. A legtöbb esetben testre szabható, és hozzáadhat további diagramok a panelt. Áttekintés paneljén kattintson a részletesebb diagramokra (melyek címek például a "Kiszolgáló"), vagy kattintson a **Metrikaböngésző** ahol egyéni diagramokat hozhat létre egy új panel megnyitásához.

## <a name="time-range"></a>Időtartomány
Az időtartományt, a diagramok vagy rácsok bármely panelen módosíthatja.

![Nyissa meg a áttekintése panelt, az alkalmazás az Azure-portálon](./media/app-insights-metrics-explorer/03-range.png)

Ha néhány adat, amely még nem történt vár, a frissítés gombra. Diagramok időközönként frissítse magát, de az intervallumok hosszabb ideig nagyobb időtartomány megadásával. Amíg az adatok újra megjelenik a diagram alakzatot elemzés keresztül is igénybe vehet.

A diagram része nagyíthatja, húzza felett:

![Húzza a diagram része keresztül.](./media/app-insights-metrics-explorer/12-drag.png)

A Nagyítás visszavonása gombra kattintva állítsa vissza azt.

## <a name="granularity-and-point-values"></a>Granularitási és a pont értékét
Az egérrel a diagram megjelenítése ezen a ponton a mérőszámok értékeit.

![Az egér rámutat egy diagram](./media/app-insights-metrics-explorer/02-focus.png)

Adott helyen a metrika értékének az előző mintavételi időköze alatt összesített értéket.

A mintavételi időközben vagy a "granularitási" a panel tetején látható.

![A panel fejléce.](./media/app-insights-metrics-explorer/11-grain.png)

A granularitási az idő a tartomány panelen módosíthatja:

![A panel fejléce.](./media/app-insights-metrics-explorer/grain.png)

A rendelkezésre álló Granularitás van a kiválasztott időtartomány függ. Az explicit Granularitás van az "automatikus" részletességgel időtartomány alternatívák.


## <a name="editing-charts-and-grids"></a>Diagramok és táblázatok szerkesztése
Új diagram hozzáadása a panel:

![A Metrikaböngésző válassza a diagram hozzáadása](./media/app-insights-metrics-explorer/04-add.png)

Válassza ki **szerkesztése** egy meglévő vagy új diagram szerkesztése azt mutatja be:

![Válasszon egy vagy több metrikák](./media/app-insights-metrics-explorer/08-select.png)

Jelenítheti meg több mint egy metrika a diagramon, bár a kombinációk együtt megjeleníthető kapcsolatos korlátozások vonatkoznak. Amint egy metrika választja, a többi le vannak tiltva.

Ha Ön a kódolt [egyéni metrikák] [ track] az alkalmazásba (hívások TrackMetric és TrackEvent) azokat a rendszer itt listázza.

## <a name="segment-your-data"></a>Az adatok szegmens
Fel metrika - tulajdonságban például ügyfelek különböző operációs rendszerekkel oldalmegtekintéseket összehasonlítani.

Jelöljön ki egy diagram vagy a rács, váltson csoportosítás, és a csoportosítás alapját tulajdonság kiválasztása:

![Jelölje be csoportosítás a, majd a készlet válasszon ki egy tulajdonságot a Group By](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> Csoportosítás használatakor a terület és sávdiagram típusok halmozott megjelenítésre adja meg. Ez a megfelelő ahol az összesítési módszer összege. De az összesítési típusát az átlagot, válassza a vonal, vagy a rács megjelenítési típusa.
>
>

Ha Ön a kódolt [egyéni metrikák] [ track] az alkalmazásba azok tulajdonság értékével, és láthatja a listában jelölje ki a tulajdonságot.

Ez az ábra túl kicsi a szegmentált adatokat? Magasságának beállítása:

![A csúszka](./media/app-insights-metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Aggregáció típusa
Alapértelmezés szerint a oldalán a jelmagyarázat a diagram az adott időszakban általában jeleníti meg az összesített érték. Ha a diagram mutat, azt jeleníti meg az értékét ezen a ponton.

A diagramon minden adatpont az előző mintavételi időköze vagy a "granularitási" fogadott adatok értéket összesítő. A granularitási a panel tetején látható, és a diagram átfogó időskálára függően változik.

Metrikák összesíthetők különböző módon:

* **Count** a mintavételi időközben a fogadott események száma. Az események például kérések szolgál. A diagram magasságát változásait azt jelzi, hogy az események következnek be sebessége változásait. Azonban vegye figyelembe, hogy a numerikus értéket módosítja a mintavételi időközben módosításakor.
* **Sum** fogadása történik a mintavételi időközben, vagy az időtartam, a diagram adatpontjainak értékeknek hozzáadja.
* **Átlagos** összege elosztja a számú adatpontot tartalmaznak arra az időtartamra, kapott.
* **Egyedi** számát is, hogy a felhasználók és számok használhatók. A mintavételi időszakban, vagy a diagram az adott időszakban az ábrán látható, hogy inkonzisztensek különböző felhasználók száma.
* **%**-minden Összesítés százalékos verzióit csak szegmentált diagramok használt. Az összes mindig hozzáadja a 100 %-kal, majd a diagram ábrázolja a relatív hozzájárulás összesen különböző összetevői.

    ![Összesítési százalékos aránya](./media/app-insights-metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Az összesítési típusát módosítása

![A diagram szerkesztése, és válassza ki az összesítés](./media/app-insights-metrics-explorer/05-aggregation.png)

Új diagram létrehozásakor, vagy ha összes metrikát van bejelölve az alapértelmezett eszköze mindegyik metrikát látható:

![Tekintse meg az alapértelmezett beállításokat az összes metrikát kijelölésének megszüntetése](./media/app-insights-metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>PIN-kód y tengely 
Alapértelmezés szerint a diagram Y tengely értékeit az adattartomány ahhoz, hogy megkapja az értékek quantum vizuális ábrázolását maximális értékeket: nulla-től kezdődő jeleníti meg. Azonban bizonyos esetekben több, mint a quantum vizuálisan vizsgálandó érték kisebb változásokat érdekes lehet. Ehhez hasonlóan testreszabni az y tengely szerkesztési szolgáltatás kívánt hely y tengely minimális vagy maximális értékének rögzítése között.
Kattintson a "Speciális beállítások" jelölőnégyzetet az y tengelyen tartomány beállítások elindítani

![Kattintson a Speciális beállítások, egyéni tartományt, adja meg a minimális maximális értékek](./media/app-insights-metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Szűrje az adatokat
Csak a kijelölt állítja be a tulajdonságértékek metrikák megtekintéséhez:

![Kattintson a szűrő, bontsa ki a tulajdonságot, és ellenőrizze az egyes értékeket](./media/app-insights-metrics-explorer/19-filter.png)

Ha minden olyan értéket adott tulajdonság nem jelöl, megegyezik a kijelölése: nincs szűrő az adott tulajdonsághoz.

Figyelje meg a száma, események mellett minden egyes tulajdonság értéke. Egy tulajdonság értékének választja, a számok mellett egyéb tulajdonságértékek módosul.

Szűrők a panelek a diagramok vonatkozik. Ha azt szeretné, hogy más típusú diagramokkal különböző szűrőket, hozzon létre, és más metrikák paneleken mentéséhez. Ha azt szeretné, a különböző paneleken diagramokat az irányítópulton, így egymás mellett láthatja is rögzíti.

### <a name="remove-bot-and-web-test-traffic"></a>Távolítsa el a botot és webes teszt forgalom
A szűrővel **valós vagy szintetikus forgalom** , és ellenőrizze, **valós**.

Alapján is szűrheti **szintetikus forgalom forrása**.

### <a name="to-add-properties-to-the-filter-list"></a>A lista tulajdonságok hozzáadása
Szeretné szűrni a választása szerinti kategória telemetriai adatokat? Például lehet, hogy a felhasználók különböző kategóriákba fel osztani, és szeretné szegmentálni adatait ezen kategóriák szerinti.

[Hozzon létre egy saját tulajdonságot](app-insights-api-custom-events-metrics.md#properties). Állítsa be azt az egy [Telemetriai inicializáló](app-insights-api-custom-events-metrics.md#defaults) kell rendelkeznie az összes telemetriai adat - megjelennek, beleértve a szabványos telemetriai különböző SDK modulok által küldött.

## <a name="edit-the-chart-type"></a>Szerkessze a diagram típusát
Figyelje meg, hogy válthat között rácsok és diagramokat:

![A rács és a graph, majd a diagram típusát](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>A metrikák panel mentése
Amikor az egyes diagramok létrehozott, mentse azokat a Kedvencek közé. Dönthet úgy, hogy megoszthatják más csoport tagjai az egy szervezeti fiók használata.

![Kattintson a kedvenc](./media/app-insights-metrics-explorer/21-favorite-save.png)

A panel ismét megjelenik, hogy **keresse fel a áttekintése panel** , és nyissa meg a Kedvencek:

![Az Áttekintés paneljén válassza a Kedvencek közé](./media/app-insights-metrics-explorer/22-favorite-get.png)

Ha relatív időtartomány mentésekor, a panel fog frissültek a legújabb metrikák. Ha abszolút időtartomány beállítást választja, akkor minden egyes jeleníti meg ugyanazokat az adatokat.

## <a name="reset-the-blade"></a>A panel alaphelyzetbe állítása
Ha szerkeszti a panel, de szeretné, majd kattintva visszatérhet az eredeti készlet mentése, kattintson alaphelyzetbe állítása.

![A metrika Explorer tetején gombok](./media/app-insights-metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Élő metrikák adatfolyam

A telemetriai adatok sokkal azonnali megtekintéséhez nyissa meg a [élő adatfolyam](app-insights-live-stream.md). A legtöbb metrikák miatt a folyamat az összesítés jelennek meg néhány percet igénybe vehet. Ezzel szemben élő metrikákat a kis késleltetésű vannak optimalizálva. 

## <a name="set-alerts"></a>Riasztások beállítása
Rendkívüli értékek a metrika az e-mailben értesítést kapjon, adjon hozzá egy riasztást. Ön választhatja az e-mailt küldhet a rendszergazdák, vagy az adott e-mail címekre.

![A Metrikaböngészőben válassza ki a riasztási szabályok, riasztások hozzáadása](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[További információ a riasztások][alerts].


## <a name="continuous-export"></a>Folyamatos exportálás
Ha azt szeretné, hogy külsőleg is feldolgozhat folyamatosan exportált adatok, érdemes lehet [a folyamatos exportálás](app-insights-export-telemetry.md).

### <a name="power-bi"></a>Power BI
Ha azt szeretné, hogy az adatok is nézeteket, akkor [Power bi-bA exportálása](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Elemzés
[Elemzés](app-insights-analytics.md) rugalmasabb módszer a telemetriai hatékony lekérdezési nyelv használatával elemezheti a. Használnia, ha egyesítése vagy számítási metrikák származó eredmények vagy hajtsa végre az alkalmazás legújabb teljesítmény egy részletes feltárása. 

Metrika diagram kattinthat a Analytics ikonra, ha közvetlenül a megfelelő Analytics lekérdezni.

## <a name="troubleshooting"></a>Hibaelhárítás
*A diagram nem szerepel az adatokat.*

* Szűrők a panelen a diagramok vonatkozik. Győződjön meg arról, hogy egy diagram van összpontosít, amíg nem adott meg, amely nem tartalmazza az adatok egy másik szűrőt.

    Ha szeretne a különböző szűrőket különböző diagramok, hozza létre a címzetteket a különböző paneleken, mentse azokat, külön Kedvencek. Ha azt szeretné, akkor is rögzítheti őket az irányítópulton, hogy egymás mellett láthatja.
* Ha a diagram olyan tulajdonságon, amely nincs definiálva a következő metrika szerint csoportosítja, majd lesz semmi a diagramon. Próbálja meg törölni a "group by", vagy válasszon egy másik csoportosítási tulajdonságot.
* Teljesítményadatok (CPU, IO sebessége, és így tovább) Java web Services, a Windows asztali alkalmazások esetén érhető el [IIS webes alkalmazások és szolgáltatások telepítése állapotfigyelő](app-insights-monitor-performance-live-website-now.md), és [Azure Cloud Services](app-insights-azure.md). Nem érhető el az Azure-webhelyek.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Következő lépések
* [Az Application insights szolgáltatással megfigyelési kihasználtsága](app-insights-web-track-usage.md)
* [Diagnosztikai keresés](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
