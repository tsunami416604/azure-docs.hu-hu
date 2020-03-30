---
title: Metrikák feltárása az Azure Application Insightsban | Microsoft dokumentumok
description: A metrikus kezelő diagramjainak értelmezése, valamint a metrikus kezelő panelek testreszabása.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275892"
---
# <a name="exploring-metrics-in-application-insights"></a>Metrikák feltárása az Application Insightsban
Metrikák az [Application Insights][start] mért értékek és az alkalmazás telemetriai adatokban küldött események száma. Segítenek a teljesítményproblémák észlelésében és az alkalmazás használati módjának trendjeinek megtekintésében. A szabványos metrikák széles skálája áll rendelkezésedre, és saját egyéni mutatókat és eseményeket is létrehozhatsz.

> [!NOTE]
> Ez a cikk ismerteti a klasszikus metrikák felfedező tapasztalat, amely jelenleg elavult, és végül kivonva. Javasoljuk, hogy nézze meg az új élményt, amely et ebben a [cikkben ismertetjük.](../platform/metrics-charts.md)

A mérőszámok és az eseményszámok az összesített értékek, például összegek, átlagok vagy számok diagramjaiban jelennek meg.

Íme egy diagramminta:

![](./media/metrics-explorer/01-overview.png)

Metrikadiagramokat talál mindenhol az Application Insights portálon. A legtöbb esetben testre szabhatók, és további diagramokat adhat hozzá a panelhez. Az Áttekintés panelen kattintson a részletesebb diagramokra (amelyek címe például "Kiszolgálók" van), vagy kattintson a **Metrikák intézőre** kattintva egy új panel megnyitásához, ahol egyéni diagramokat hozhat létre.

## <a name="time-range"></a>Időtartomány
A diagramok vagy rácsok által lefedett időtartományt bármely panelen módosíthatja.

![Nyissa meg az alkalmazás áttekintő paneljét az Azure Portalon](./media/metrics-explorer/03-range.png)

Ha olyan adatokat vár, amelyek még nem jelentek meg, kattintson a Frissítés gombra. A diagramok időnként frissülnek, de az intervallumok hosszabbak a nagyobb időtartományokesetében. Eltarthat egy ideig, amíg az adatok az elemzési folyamaton keresztül egy diagramra kerülnek.

A diagram egy részének nagyításához húzza az egérmutatót a diagram fölé:

![Húzza az egérmutatót a diagram egy részén.](./media/metrics-explorer/12-drag.png)

A nagyítás visszavonása gombra kattintva visszaállíthatja azt.

## <a name="granularity-and-point-values"></a>Részletesség és pontértékek
Vigye az egeret a diagram fölé a mérőszámok értékeinek megjelenítéséhez.

![Vigye az egérmutatót egy diagram fölé](./media/metrics-explorer/02-focus.png)

A metrika értéke egy adott ponton összesíti az előző mintavételi időközt.

A mintavételi időköz vagy a "granularitás" a fűrészlap tetején látható.

![Egy penge fejléce.](./media/metrics-explorer/11-grain.png)

A részletességet az Időtartomány panelen állíthatja be:

![Egy penge fejléce.](./media/metrics-explorer/grain.png)

A rendelkezésre álló részletességek a kiválasztott időtartománytól függenek. Az explicit részletességek az időtartomány "automatikus" granularitásának alternatívái.


## <a name="editing-charts-and-grids"></a>Diagramok és rácsok szerkesztése
Új diagram hozzáadása a panelhez:

![A Metrikakezelőben válassza a Diagram hozzáadása lehetőséget.](./media/metrics-explorer/04-add.png)

Válassza a **Szerkesztés** lehetőséget egy meglévő vagy új diagramon a jelenlévők szerkesztéséhez:

![Válasszon ki egy vagy több mutatót](./media/metrics-explorer/08-select.png)

A diagramon több mutatóis megjeleníthető, bár a kombinációkra vonatkozóan korlátozások vannak érvényben. Amint kiválaszt egy metrikát, a többi közül néhány le van tiltva.

Ha egyéni [mutatókat][track] kódolt az alkalmazásba (a TrackMetric és a TrackEvent hívásai), azok itt jelennek meg.

## <a name="segment-your-data"></a>Az adatok szegmentálása
A metrikát tulajdonság szerint feloszthatja – például összehasonlíthatja a különböző operációs rendszerekkel rendelkező ügyfelek oldalmegtekintéseit.

Jelöljön ki egy diagramot vagy rácsot, kapcsolja be a csoportosítást, és válasszon ki egy tulajdonságot a csoportosításhoz:

![Válassza a Csoportosítás be lehetőséget, majd jelölje ki a tulajdonságot a Csoportosítás szerint beállítással](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Csoportosítás használatakor a Terület- és sávdiagram-típusok halmozott megjelenítést biztosítanak. Ez akkor alkalmas, ha az aggregációs módszer Összege. Ha azonban az összesítés típusa Átlag, válassza a Vonal vagy a Rács megjelenítési típusokat.
>
>

Ha egyéni [mutatókat][track] kódolt az alkalmazásba, és azok tulajdonságértékeket tartalmaznak, kiválaszthatja a tulajdonságot a listában.

Túl kicsi a diagram a szegmentált adatokhoz? Állítsa be a magasságát:

![A csúszka beállítása](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Összesítési típusok
Alapértelmezés szerint az oldalmellékjelmagyarázat általában a diagram időszakának összesített értékét mutatja. Ha az egérmutatót a diagram fölé viszi, akkor az adott ponton az értéket jeleníti meg.

A diagram minden adatpontja az előző mintavételi időközben vagy "részletességben" kapott adatértékek összesítése. A granularitás a penge tetején látható, és a diagram teljes időskálájával függ.

A mérőszámok különböző módokon összesíthetők:

* **A mintavételi** időközben kapott események száma. Eseményekhez, például kérésekhez használatos. A diagram magasságának változásai az események bekövetkezési sebességének változásait jelzik. De vegye figyelembe, hogy a numerikus érték megváltozik, ha módosítja a mintavételi időközt.
* **Az Összeg** összeadja a mintavételi időköz vagy a diagram időszaka során fogadott összes adatpont értékét.
* **Az Átlag** elosztja az Összeget az intervallum alatt kapott adatpontok számával.
* **Az egyedi** számok a felhasználók és a fiókok számaihoz használatosak. A mintavételi időköz, vagy a diagram időtartama alatt az ábra a különböző felhasználók számát mutatja, akik et az adott időszakban tapasztaltak.
* **%**- az egyes összesítések százalékos változatai csak szegmentált diagramokkal használatosak. Az összeg mindig 100%-ot tesz ki, és a diagram az összes különböző összetevőinek relatív hozzájárulását mutatja.

    ![Százalékos összesítés](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Az összesítéstípus módosítása

![A diagram szerkesztése, majd az Összesítés kiválasztása](./media/metrics-explorer/05-aggregation.png)

Az egyes mutatók alapértelmezett módszere akkor jelenik meg, amikor új diagramot hoz létre, vagy ha az összes mutató nincs bejelölve:

![Törölje az összes mutató kijelölését az alapértelmezett értékek megtekintéséhez](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Y tengely rögzítése 
Alapértelmezés szerint a diagram az Y tengely értékeit mutatja, nullától az adattartomány maximális értékéig, hogy vizuálisan ábrázolja az értékek kvantumát. De bizonyos esetekben több, mint a kvantum érdekes lehet, hogy vizuálisan vizsgálja kisebb változások értékek. Az ehhez hasonló testreszabások esetén használja az Y tengely tartományszerkesztési funkcióját az Y tengely minimális vagy maximális értékének kívánt helyre rögzítéséhez.
Kattintson a "Speciális beállítások" jelölőnégyzetre az Y tengely tartomány beállításainak beállításához

![Kattintson a Speciális beállítások gombra, válassza az Egyéni tartomány lehetőséget, és adja meg a min maximális értékeket](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Adatok szűrése
Csak a kiválasztott tulajdonságértékek mutatóinak megtekintéséhez:

![Kattintson a Szűrő gombra, bontson ki egy tulajdonságot, és ellenőrizzen néhány értéket](./media/metrics-explorer/19-filter.png)

Ha nem ad ki értékeket egy adott tulajdonsághoz, az ugyanaz, mint ha mindegyiket kijelöli: nincs szűrő az adott tulajdonságon.

Figyelje meg az egyes tulajdonságértékek melletti események számát. Ha egy tulajdonság értékeit választja ki, a függvény a többi tulajdonságérték mellett a számokat is módosítja.

A szűrők a penge összes diagramjára vonatkoznak. Ha különböző szűrőket szeretne alkalmazni a különböző diagramokra, hozzon létre és mentsen különböző mérőszámokat. Ha szeretné, különböző pengeből rögzítheti a diagramokat az irányítópultra, hogy egymás mellett láthassa őket.

### <a name="remove-bot-and-web-test-traffic"></a>Bot- és webes tesztforgalom eltávolítása
Használja a szűrőt **Valós vagy szintetikus forgalom** és ellenőrizze **Real**.

Szűrhet **a szintetikus forgalom forrása**szerint is.

### <a name="to-add-properties-to-the-filter-list"></a>Tulajdonságok hozzáadása a szűrőlistához
Szeretné szűrni a telemetriai adatokat egy saját általválasztott kategóriára? Előfordulhat például, hogy a felhasználókat különböző kategóriákra osztja fel, és az adatokat ezek szerint szeretné szegmentálni.

[Hozzon létre saját tulajdont.](../../azure-monitor/app/api-custom-events-metrics.md#properties) Állítsa be a [telemetriai initializer,](../../azure-monitor/app/api-custom-events-metrics.md#defaults) hogy úgy jelenik meg az összes telemetriai adatok , beleértve a különböző SDK-modulok által küldött szabványos telemetriai adatokat.

## <a name="edit-the-chart-type"></a>A diagramtípus szerkesztése
Figyelje meg, hogy válthat a rácsok és grafikonok között:

![Rács vagy diagram kijelölése, majd diagramtípus kiválasztása](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>A mérőszámok lapka mentése
Miután létrehozott néhány diagramot, mentse őket kedvencként. Szervezeti fiók használata esetén megadhatja, hogy megosztja-e a többi csapattaggal.

![Kedvenc kiválasztása](./media/metrics-explorer/21-favorite-save.png)

A panel újbóli megtekintéséhez **nyissa meg az áttekintő panelt,** és nyissa meg a Kedvencek:

![Az Áttekintés panelen válassza a Kedvencek lehetőséget.](./media/metrics-explorer/22-favorite-get.png)

Ha a mentéskor a Relatív időtartományt választotta, a panel a legújabb mérőszámokkal frissül. Ha az Abszolút időtartományt választotta, akkor minden alkalommal ugyanazokat az adatokat jeleníti meg.

## <a name="reset-the-blade"></a>A panel alaphelyzetbe állítása
Ha szerkeszt egy panelt, de vissza szeretne térülni az eredeti mentett készlethez, kattintson az Alaphelyzetbe állítás gombra.

![A Metric Explorer tetején lévő gombokon](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Élő mérőszámok stream

A telemetrias adatok sokkal közvetlenebb megtekintéséhez nyissa meg az [Élő közvetítés telemetrist.](live-stream.md) A legtöbb metrika néhány percet vesz igénybe, az összesítés folyamata miatt. Ezzel szemben az élő metrikák alacsony késésre vannak optimalizálva. 

## <a name="set-alerts"></a>Riasztások beállítása
Ha e-mailben szeretne értesítést kapni bármely mutató szokatlan értékéről, adjon hozzá egy riasztást. Választhat, hogy az e-mailt elküldi a fiók rendszergazdáinak, vagy adott e-mail címekre.

![A Metrikakezelőben válassza a Riasztási szabályok lehetőséget, majd válassza a Riasztás hozzáadása](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[További információ a riasztásokról.][alerts]


## <a name="continuous-export"></a>Folyamatos exportálás
Ha azt szeretné, hogy az adatok folyamatosan exportálhatók legyenek, hogy külsőleg is feldolgozhassa őket, fontolja meg a [Folyamatos exportálás](../../azure-monitor/app/export-telemetry.md)használatát.

### <a name="power-bi"></a>Power BI
Ha még gazdagabb nézeteket szeretne az adatokról, [exportálhat a Power BI-ba.](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)

## <a name="analytics"></a>Elemzés
[Az Analytics](../../azure-monitor/app/analytics.md) egy sokoldalúbb módja a telemetriai adatok hatékony lekérdezési nyelv használatával történő elemzésének. Akkor használja, ha a mérőszámok eredményeit kombinálni vagy kiszámítani szeretné, vagy részletesen fel szeretné késni az alkalmazás legutóbbi teljesítményét. 

Egy mérőszámdiagramon az Analytics ikonra kattintva közvetlenül az egyenértékű Analytics-lekérdezéshez juthat.

## <a name="troubleshooting"></a>Hibaelhárítás
*Nem látok adatokat a diagramon.*

* A szűrők a panel összes diagramjára vonatkoznak. Győződjön meg arról, hogy miközben egy diagramra összpontosít, nem állított be olyan szűrőt, amely kizárja a másik összes adatát.

    Ha különböző szűrőket szeretne beállítani a különböző diagramokon, hozza létre őket különböző pengelapokban, mentse őket külön kedvencekként. Ha szeretné, rögzítheti őket az irányítópulton, hogy egymás mellett láthassa őket.
* Ha a diagramot olyan tulajdonság szerint csoportosítja, amely nincs definiálva a mutatón, akkor a diagramon nem lesz semmi. Próbálja meg törölni a "csoportosítás" parancsot, vagy válasszon másik csoportosítási tulajdonságot.
* A teljesítményadatok (CPU, IO-sebesség és így tovább) java webszolgáltatásokhoz, asztali Windows-alkalmazásokhoz, [IIS-webalkalmazásokhoz és -szolgáltatásokhoz](../../azure-monitor/app/monitor-performance-live-website-now.md)érhetők el az állapotfigyelő telepítésekor és az [Azure Cloud Services szolgáltatáshoz.](../../azure-monitor/app/app-insights-overview.md) Nem érhető el az Azure-webhelyeken.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
* [Használat figyelése az Application Insights segítségével](../../azure-monitor/app/usage-overview.md)
* [A diagnosztikai keresés használata](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
