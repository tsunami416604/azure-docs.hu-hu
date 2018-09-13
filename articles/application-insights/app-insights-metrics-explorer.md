---
title: Az Azure Application Insights a metrikák felfedezése |} A Microsoft Docs
description: A metrika explorer diagramok értelmezése, és a metrika explorer panelek testreszabása.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2017
ms.author: mbullwin
ms.openlocfilehash: 66ef3330a435574405dbfb8b8c82d984bb877efe
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645919"
---
# <a name="exploring-metrics-in-application-insights"></a>Az Application Insightsban a metrikák felfedezése
A metrikák [Application Insights] [ start] mért értékek és az eseményszámok az alkalmazásból küldött telemetriai adatokat a rendszer. Ezek segítenek teljesítménybeli problémák észlelése, és tekintse meg az alkalmazás használatának trendjei. Standard mérőszámok széles skáláját, és a saját egyéni metrikákkal és eseményekkel is létrehozhat.

Metrikák és események számát az összesített értékek például összegeket, átlagokat vagy számát a diagramokban jelennek meg.

Itt egy olyan minta diagramokat:

![](./media/app-insights-metrics-explorer/01-overview.png)

Mérőszámdiagramok mindenhol az Application Insights portálon találja. A legtöbb esetben ezek testreszabhatók, és a panelen adhat hozzá további diagramokat. Az Áttekintés panelen kattintson az részletes diagramok (melyek címei, például a "Kiszolgáló"), vagy kattintson a **Metrikaböngésző** , egyéni diagramok hozható létre egy új panel megnyitásához.

## <a name="time-range"></a>Időtartomány
Módosíthatja az időtartományt, a diagramokat vagy bármely panelen rácsok hatálya alá.

![Az alkalmazás áttekintése panel megnyitásához az Azure Portalon](./media/app-insights-metrics-explorer/03-range.png)

Ha még nem történt meg még néhány adatot vár, a frissítés parancsra. A diagramok adott időközönként frissülnek, de az intervallumok hosszabb ideig nagyobb időintervallumok. Eljussanak az elemzési folyamatot, egy diagram be adatok egy ideig is eltarthat.

Nagyíthatja a diagram része, húzza át azt:

![Húzza a diagram része keresztül.](./media/app-insights-metrics-explorer/12-drag.png)

A Nagyítás visszavonása gombra kattintva állítsa vissza.

## <a name="granularity-and-point-values"></a>És a pont értékek
Az egérmutatóval rámutat a metrikák értékeinek megjelenítése ezen a ponton a diagramot.

![Vigye az egérmutatót egy diagram](./media/app-insights-metrics-explorer/02-focus.png)

A metrika egy adott időpontban értékét az előző mintavételi időszakra vonatkozó összesített érték.

A mintavételi időközben vagy a "granularitási" a panel tetején látható.

![A fejléc egy panel.](./media/app-insights-metrics-explorer/11-grain.png)

A granularitási az idő a tartomány panelen módosíthatja:

![A fejléc egy panel.](./media/app-insights-metrics-explorer/grain.png)

A rendelkezésre álló granularitással, válassza ki az időtartományt függenek. Az explicit granularitással alternatívával, az "automatikus" részletességgel az időtartományt.


## <a name="editing-charts-and-grids"></a>Diagramok és táblázatok szerkesztése
Új diagram hozzáadása a panelen:

![A Metrikaböngészőben válassza a diagram hozzáadása](./media/app-insights-metrics-explorer/04-add.png)

Válassza ki **szerkesztése** egy meglévő vagy új diagram szerkesztése azt mutatja be:

![Válassza ki egy vagy több metrikát](./media/app-insights-metrics-explorer/08-select.png)

Egynél több metrikát egy diagramot, megjelenítheti, bár a kombinációk együtt megjelenő kapcsolatos korlátozások vonatkoznak. Amint egy metrika választja, a többi le vannak tiltva.

Ha Ön kódolt [egyéni metrikákat] [ track] az alkalmazásba (hívás TrackMetric és TrackEvent), a rendszer itt listázza.

## <a name="segment-your-data"></a>Az adatok szegmentálására
Akkor is feloszthatja az metrika tulajdonság – például ügyfelek különböző operációs rendszereken az oldalmegtekintéseket összehasonlítására.

Válasszon ki egy diagram vagy a rács, a csoportosítás váltson, és válasszon egy tulajdonságot a csoportba:

![Jelölje ki a csoportosítás a készlet, válasszon ki egy tulajdonságot a Group By, majd](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> A csoportosítás használata esetén a terület- és oszlopdiagram típusok halmozott megjelenítési adja meg. Ez a megfelelő Sum összesítő metódus helyére. De ha az összesítés típusa átlagos, válassza a sor vagy a rács megjelenítési típusok.
>
>

Ha, a kódolt [egyéni metrikákat] [ track] az alkalmazásba és tulajdonságértékeket tartalmazzák, láthatja a listában jelölje ki a tulajdonságot.

A diagram az túl kicsi a szegmentált adatok? Magasság igazítása:

![A csúszka](./media/app-insights-metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Aggregáció típusa
Alapértelmezés szerint a oldalán a jelmagyarázat a diagram a időszakban általában jeleníti meg az összesített érték. Ha a kurzort a diagram, azt az értéket mutatja ezen a ponton.

A diagram az egyes adatpontok az előző mintavételi időköz vagy a "granularitási" fogadott adatok értékeinek összesítést. A granularitási a panel tetején látható, és az általános időskálán a diagram változik.

Metrikák összesíthetők különböző módon:

* **Száma** a mintavételi időközben a fogadott események száma. Az esemény, például kérelmeket szolgál. A diagram magasságát változata, amellyel az események történnek ingadozása jelzi. Azonban vegye figyelembe, hogy a numerikus értéket módosítja, amikor módosítja a mintavételi időköz.
* **Sum** összeadja a mintavételi intervallum, vagy azt az időszakot, a diagram keresztül fogadott összes adatpont értékeit.
* **Átlagos** a Sum elosztja az időtartamra, kapott adatpontok száma.
* **Egyedi** counts használt felhasználók és fiókok számát. A mintavételi időszakban, vagy a diagram az időtartam alatt az ábrán látható az az időpont látható a különböző felhasználók száma.
* **%** -minden Összesítés százalékos verziói csak szegmentált diagramokkal szolgálnak. Az összes mindig hozzáadása akár 100 %-os, valamint a diagram bemutatja a különböző összetevőihez tartozó teljes relatív hozzájárulást.

    ![Százalékos összesítést](./media/app-insights-metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Az összesítés típusa módosítható

![A diagramra a szerkesztéséhez, és válassza ki az összesítés](./media/app-insights-metrics-explorer/05-aggregation.png)

Amikor létrehoz egy új diagramot, vagy ha minden metrika sincs vannak kijelölve az alapértelmezett módszer egyes metrika látható:

![Kapcsolja ki az összes metrikák megtekintéséhez az alapértelmezett beállításokat](./media/app-insights-metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>PIN-kód y tengely 
Alapértelmezés szerint a diagram Y tengely értékből nulla eddig: vizuális megjelenítését, az értékek quantum biztosíthat az adattartomány maximális értékeket mutatja. Azonban bizonyos esetekben több, mint a quantum vizuálisan megvizsgálják a kisebb módosításokat értékeket számára érdekes lehet. Testreszabásokat hasonlóan ez a használati y tartomány rögzítése a kívánt helyen y tengely minimális vagy maximális értékének szerkesztési funkciót.
Kattintson a "Speciális beállítások" jelölőnégyzetet az y tengely tartománya beállítások megjelenítéséhez

![Kattintson a Speciális beállítások, egyéni tartományt, és adja meg a minimális, maximális értékek](./media/app-insights-metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Az adatok szűrése
Csak a kijelölt készletének tulajdonságértékek metrikák megtekintése:

![Kattintson a szűrő gombra, bontsa ki a tulajdonságot és néhány érték ellenőrzése](./media/app-insights-metrics-explorer/19-filter.png)

Minden olyan értékeket egy adott tulajdonság nem adja meg, ha ugyanaz, mint ha kiválasztja őket az összes: nincs szűrő az adott tulajdonságot.

Figyelje meg, hogy az eseményszámok mellett minden egyes tulajdonság értéke. Amikor kiválaszt egy tulajdonság értékét, az érintett mellett egyéb tulajdonságértékek módosulnak.

Szűrők panel összes diagramjai vonatkoznak. Ha azt szeretné, hogy a alkalmazni a különböző diagramok különböző szűrőket, hozzon létre és különböző mérőszámpaneleket. Ha azt szeretné, diagramok a különböző paneleket az irányítópultot, így láthatják őket egymással párhuzamosan is rögzíthet.

### <a name="remove-bot-and-web-test-traffic"></a>Távolítsa el a robot és a webes teszt forgalom
A szűrő használata **valós vagy szintetikus forgalom** , és ellenőrizze **valós**.

Szerint is szűrheti **szintetikus forgalom forrása**.

### <a name="to-add-properties-to-the-filter-list"></a>A lista tulajdonságok hozzáadása
Szeretne saját egy kategóriához szűrőtelemetria? Például a felhasználók különböző kategóriákba feloszthatja talán, és szeretné szegmentálni az adatokat, ezen kategóriák szerinti csoportosításához.

[Hozzon létre saját tulajdonságot](app-insights-api-custom-events-metrics.md#properties). Beállíthatja a egy [Telemetriainicializáló](app-insights-api-custom-events-metrics.md#defaults) , hogy az összes telemetriai adat - jelennek meg a normál telemetriai többek között küldött különböző SDK-modulok által.

## <a name="edit-the-chart-type"></a>A diagram típus szerkesztése
Figyelje meg, hogy válthat a között rácsok és diagramjait:

![Rács vagy diagram kiválasztása, majd válassza a diagram típusát](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Mentse a metrikák paneljén
Ha létrehozta az egyes diagramok, mentse őket kedvencként. Kiválaszthatja, hogy e hogy megosztja azokat más csapattagokat, ha szervezeti fiókkal.

![Válassza ki a Kedvencek közé](./media/app-insights-metrics-explorer/21-favorite-save.png)

A panel újra, hogy **lépjen az Áttekintés panelen** , és nyissa meg a Kedvencek:

![Az Áttekintés panelen válassza ki a Kedvencek közé](./media/app-insights-metrics-explorer/22-favorite-get.png)

Relatív időtartomány azt választotta, amikor mentette, ha a panel frissül a legfrissebb metrikákkal. Ha abszolút időtartomány beállítást választja, azt minden alkalommal jeleníti meg ugyanazokat az adatokat.

## <a name="reset-the-blade"></a>A panel visszaállítása
Ha a panelen szerkesztheti, de szeretne, majd az eredeti készlet mentett való visszatéréshez, csak kattintson az Alaphelyzet.

![A Metrikaböngésző tetején a gombok](./media/app-insights-metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Élő metrikastream

Nyissa meg a telemetriai adatok nézetével sokkal azonnali, [élő Stream](app-insights-live-stream.md). A legtöbb metrikák jelennek meg, a folyamat az összesítés miatt néhány percet igénybe vehet. Ezzel szemben az élő metrikákat a közel valós idejű vannak optimalizálva. 

## <a name="set-alerts"></a>Riasztások beállítása
Valamelyik metrika rendkívüli értékek az e-mailben értesítést kapjon, riasztás hozzáadásához. Választhat az e-mailt küldjön a fiók a rendszergazdák, vagy adott e-mail-címek.

![A Metrikaböngészőben válassza ki a riasztási szabályok, a riasztás hozzáadása](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[További információ a riasztásokkal kapcsolatban][alerts].


## <a name="continuous-export"></a>Folyamatos exportálás
Ha azt szeretné, hogy külsőleg feldolgozása folyamatosan exportált adatok, érdemes [a folyamatos exportálás](app-insights-export-telemetry.md).

### <a name="power-bi"></a>Power BI
Ha azt szeretné, hogy az adatok még mélyebb nézeteket, [exportálása a Power bi-bA](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Elemzés
[Analytics](app-insights-analytics.md) sokoldalúbb módja a hatékony lekérdezési nyelv a telemetria elemzését. Használnia, ha szeretné egyesítése vagy számítási mérőszámok eredményei, vagy hajtsa végre a közelebbről megismerheti a legújabb az alkalmazás teljesítményének. 

Metrikadiagram, a közvetlenül az egyenértékű elemzési lekérdezés beolvasásához az Analytics ikonra kattint.

## <a name="troubleshooting"></a>Hibaelhárítás
*A diagram nem látható semmilyen adatot.*

* A panelen a diagramok szűrők vonatkoznak. Győződjön meg arról, hogy Ön összpontosítunk, egy diagram, amíg nem állította be egy szűrőt, amely nem tartalmazza a másik az adatokat.

    Ha azt szeretné, a különböző szűrőivel beállíthatja a különböző diagramok különböző paneljein hozzon létre őket, mentse azokat, külön Kedvencek. Ha azt szeretné, is rögzítheti őket az irányítópulton, hogy tekintheti meg őket egymással párhuzamosan.
* Ha olyan tulajdonságot, amely nincs meghatározva a metrikát a diagram csoportosítás, majd kell semmi a diagramra. Törölje a "Csoportosítás", vagy válasszon egy különböző csoportosítási tulajdonságot.
* Teljesítményadatok (CPU, i/o-forgalom, és így tovább) érhető el a Java-webszolgáltatásokhoz, Windows asztali alkalmazásokban, [IIS webalkalmazások és szolgáltatások Ha telepítse az állapotfigyelőt](app-insights-monitor-performance-live-website-now.md), és [Azure Cloud Services](app-insights-azure.md). Nem Azure-webhelyekhez érhető el.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
* [Az Application Insights figyelési használat](app-insights-web-track-usage.md)
* [Diagnosztikai keresés használata](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
