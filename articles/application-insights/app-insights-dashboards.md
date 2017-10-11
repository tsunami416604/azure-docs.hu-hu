---
title: "Irányítópultok és az Azure Application Insights navigációs |} Microsoft Docs"
description: "A kulcs APM diagramok és a lekérdezések nézetek létrehozása."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: 9987f04e7e71df5fe10c8bc209a390cb940ec4f2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigációs és az Application Insights portálon irányítópultok
Miután [Application Insights beállítása a projekten](app-insights-overview.md), az alkalmazás teljesítmény- és használati telemetriai adatokat fog megjelenni az Application Insights-erőforrás a projekt a [Azure-portálon](https://portal.azure.com).

## <a name="find-your-telemetry"></a>A telemetriai adat található
Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és keresse meg az Application Insights-erőforrást, amelyet az alkalmazás hozott létre.

![Kattintson a Tallózás gombra, válassza ki az Application Insights, majd az alkalmazást.](./media/app-insights-dashboards/00-start.png)

Az Áttekintés panel (oldal) az alkalmazás az alkalmazás fő diagnosztikai metrikáinak összegzését jeleníti meg, és egy átjárót a portál más szolgáltatásokhoz.

![A telemetriai adatok megtekintéséhez fő útvonalak](./media/app-insights-dashboards/010-oview.png)

Testre szabhatja a diagramok és rácsok és irányítópulton rögzítheti őket. Ily módon helyezheti együtt a kulcs telemetriai adatokat más alkalmazásokból központi irányítópulton.

## <a name="dashboards"></a>Irányítópultok
Először is látni való bejelentkezés után a [Microsoft Azure-portálon](https://portal.azure.com) olyan irányítópult. Itt helyezheti együtt a diagramok, amelyek számára fontos összes az Azure-erőforrások, például a telemetriai adatok között [Azure Application Insights](app-insights-overview.md).

![Személyre szabott irányítópultot.](./media/app-insights-dashboards/31.png)

1. **Navigáljon a konkrét erőforrásokat** például az alkalmazás az Application Insightsban: a bal oldali sávon használja.
2. **Térjen vissza az aktuális irányítópulton**, vagy más használt nézetek váltani: a legördülő menü bal felső használatát.
3. **Váltás az irányítópultok**: a legördülő menü használatát az irányítópult-cím
4. **Létrehozására, szerkesztésére és irányítópultok megosztása** irányítópult eszköztárán.
5. **Az irányítópult szerkesztése**: rámutat egy csempe majd a felső sávon használatával helyezze át, testre, illetve nem távolítható el.

## <a name="add-to-a-dashboard"></a>Irányítópult felvétele
A panel vagy diagramok készletét, amely különösen fontos van szüksége, amikor rögzíthető egy másolatát az irányítópulton. Megjelenik a legközelebb van vissza.

![PIN-kód egy diagram, akkor mutat, és kattintson a "...", a fejlécben.](./media/app-insights-dashboards/33.png)

1. PIN-kód a diagram az irányítópulton. A diagram másolata megjelenik az irányítópulton.
2. PIN-kód a teljes panelen az irányítópult - csempe, amely keresztül kattintson az irányítópulton megjelenő.
3. Kattintson a bal felső az aktuális irányítópulton való visszatéréshez. A legördülő menü segítségével majd térjen vissza az aktuális nézet.

Figyelje meg, hogy diagramok csempék vannak csoportosítva: egy csempe tartalmazhat egynél több diagram. A teljes csempe az irányítópulton rögzíti.

A rendszer automatikusan frissíti a diagram, amelyek elengedhetetlenek az időtartományt a diagram gyakorisággal:

* Idő legfeljebb 1 óra között: 5 percenként frissítése
* 1-24 óránként időtartománynak: 15 percenként frissítése
* 24 óra fent időtartománynak: (időtartománynak) 60.

### <a name="pin-any-query-in-analytics"></a>A lekérdezés Analytics PIN-kód
Is [PIN-kód Analytics](app-insights-analytics-using.md#pin-to-dashboard) a diagramok egy [megosztott](#share-dashboards-with-your-team) irányítópult. Ez lehetővé teszi, hogy bármilyen tetszőleges lekérdezés mellé a standard metrikák diagramokat hozzáadni. 

Automatikusan újraszámítás óránként. Kattintson a diagram hatására újraszámította az azonnal frissítési ikonjára. (Böngésző frissítési nem újraszámítása.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Egy csempe az irányítópulton beállítása
Ha egy csempére az irányítópulton, beállíthatja azt.

![Ahhoz, hogy szerkesztheti a diagram mutasson.](./media/app-insights-dashboards/36.png)

1. A diagram hozzáadása a csempén.
2. Állítsa be a metrika, csoportosítási dimenzió és diagram stílusát (táblázat, diagramhoz).
3. Húzza a diagram a nagyításhoz; keresztül a visszavonási gombra a timespan; a csempére a diagramok szűrő tulajdonságainak beállítása.
4. Állítsa be a csempe neve.

Az Áttekintés panelen szolgáltatásból rögzített mozaiklapok-nál több szerkesztési lehetősége van a metrika explorer paneleken szolgáltatásból rögzített mozaiklapok.

Az eredeti csempét rögzített folyamatot nem befolyásolja a módosításokat.

## <a name="switch-between-dashboards"></a>Irányítópultok közötti váltáshoz
Egynél több irányítópult mentése és a kettő közötti váltás. A diagram vagy a panel rögzíti, amikor azok az aktuális irányítópulton van adva.

![Irányítópultok közötti váltáshoz kattintson az irányítópulton, és válasszon ki egy mentett irányítópultot. Hozzon létre, és mentse az új irányítópult, kattintson az új gombra. Átrendezését, kattintson a Szerkesztés gombra.](./media/app-insights-dashboards/32.png)

Például lehetséges, hogy a teljes képernyős megjelenő a csapat helyiségben, és egy másik általános fejlesztési egy irányítópultot.

Az irányítópult egy panel csempe jelenik meg: a gombra kattintva nyissa meg a paneljét. A diagram a diagram az eredeti helyére replikálja.

![Nyissa meg a panelt, amely egy csempére kattint](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Irányítópultok megosztása
Ha létrehozott egy irányítópultot, más felhasználókkal megoszthatja azt.

![Az irányítópult-fejlécben kattintson a megosztás](./media/app-insights-dashboards/41.png)

További tudnivalók [szerepkörök és hozzáférés-vezérlés](app-insights-resources-roles-access-control.md).

## <a name="app-navigation"></a>Alkalmazás navigációs
Az Áttekintés panel az alkalmazással kapcsolatos további információkat az átjárót.

* **A diagram vagy a csempe** – kattintson a csempére vagy a diagramon megjelenő információk kapcsolatos részletek megtekintéséhez.

### <a name="overview-blade-buttons"></a>Áttekintés panel gombok
![Áttekintés panel felső navigációs sáv](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Metrikaböngésző** ](app-insights-metrics-explorer.md) -teljesítményt és a használati saját diagramokat.
* [**Keresési** ](app-insights-diagnostic-search.md) - vizsgálja meg az esemény, például a kérelmekről, kivételek, olyan specifikus példányai, vagy a nyomkövetési naplófájl.
* [**Elemzés** ](app-insights-analytics.md) -hatékony a lekérdezések a telemetriai adatokat.
* **Időtartománynak** -állítsa be a a panelen a diagramok szerint jelenik meg.
* **Törlés** -törli az Application Insights-erőforrást az alkalmazáshoz. Meg kell is távolítsa el az Application Insights csomagokat az alkalmazás kódját, vagy szerkesztheti a [instrumentation kulcs](app-insights-create-new-resource.md#copy-the-instrumentation-key) át tudja irányítani a telemetria bekapcsolásával egy másik Application Insights-erőforrást az alkalmazásban.

### <a name="essentials-tab"></a>Alapvető erőforrások lapon
* [Instrumentation kulcs](app-insights-create-new-resource.md#copy-the-instrumentation-key) -azonosítja az alkalmazás-erőforrást.
* Tarifacsomag - teheti a rendelkezésre álló és beállított kötet caps szolgáltatásait.

### <a name="app-navigation-bar"></a>Alkalmazás navigációs sáv
![Bal oldali navigációs sáv](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Áttekintés** – térjen vissza az Áttekintés panelen.
* **Tevékenységnapló** -riasztások és az Azure felügyeleti események.
* [**Hozzáférés-vezérlés** ](app-insights-resources-roles-access-control.md) -hozzáférést biztosítson a csoport tagjai, míg más.
* [**Címkék** ](../azure-resource-manager/resource-group-using-tags.md) -címkék használata az alkalmazás másokkal csoportosításához.

VIZSGÁLJA MEG

* [**Alkalmazás-hozzárendelés** ](app-insights-app-map.md) -Active térkép, amely az alkalmazás összetevői a függőségi adatokat származik.
* [**Észlelési intelligens** ](app-insights-proactive-diagnostics.md) -tekintse át a legutóbbi teljesítményével kapcsolatos riasztások.
* [**Élő Stream** ](app-insights-live-stream.md) – A rögzített szinte azonnali mérőszámokat, akkor hasznos, ha egy új build telepítése vagy a hibakeresést.
* [**Rendelkezésre állás / a webalkalmazás-tesztek** ](app-insights-monitor-web-app-availability.md) -rendszeres kérelmeket küldeni a webalkalmazás a world.* körül
* [**Hibák, a teljesítmény** ](app-insights-web-monitor-performance.md) -kivételek, hiba díjszabás és válaszidejének a kéréseket az alkalmazáshoz, és az alkalmazásnak, hogy a érkező kéréseket [függőségek](app-insights-asp-net-dependencies.md).
* [**Teljesítmény** ](app-insights-web-monitor-performance.md) -válaszidőt, függőség válaszidejét.
* [Kiszolgálók](app-insights-web-monitor-performance.md) -teljesítményszámlálókat. Ha elérhető, [Állapotmonitor telepítése](app-insights-monitor-performance-live-website-now.md).
* **Böngésző** -nézet és AJAX teljesítmény lapon. Ha elérhető, [állíthatnak be a weblapok](app-insights-javascript.md).
* **Használati** -lapon nézet, a felhasználó és a munkamenet számát. Ha elérhető, [állíthatnak be a weblapok](app-insights-javascript.md).

KONFIGURÁLÁSA

* **Első lépések** -beágyazott oktatóanyag.
* **Tulajdonságok** -instrumentation kulcs, az előfizetés és az erőforrás-azonosító.
* [Riasztások](app-insights-alerts.md) -riasztási konfigurációja.
* [A folyamatos exportálás](app-insights-export-telemetry.md) -konfigurálása az Azure storage telemetriai adatainak exportálása.
* [A teljesítmény tesztelése](app-insights-monitor-web-app-availability.md#performance-tests) -állítsa be a webhely szintetikus terhelése.
* [Kvóta és árképzési](app-insights-pricing.md) és [adatfeldolgozást mintavételi](app-insights-sampling.md).
* **API-hozzáférés** -létrehozása [kiadási jegyzetek](app-insights-annotations.md) és az adatok hozzáférés az API-hoz.
* [**Munkaelemek** ](app-insights-diagnostic-search.md#create-work-item) -nyomkövetési rendszer, így hibák telemetriai vizsgálatakor hozhat létre a munkájukhoz való csatlakozásban.

BEÁLLÍTÁSOK

* [**Zárolja** ](../azure-resource-manager/resource-group-lock-resources.md) -Azure-erőforrások zárolása
* [**Automatizálási parancsfájl** ](app-insights-powershell.md) -exportálása olyan az Azure erőforrás-definícióval, így használhatja a sablont létrehozni az új erőforrásokat.


## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Következő lépések

|  |  |
| --- | --- |
| [Metrikaböngésző](app-insights-metrics-explorer.md)<br/>Szűrő és a szegmens metrikák |![Keresés – példa](./media/app-insights-dashboards/64.png) |
| [Diagnosztikai keresése](app-insights-diagnostic-search.md)<br/>Található és vizsgálja meg az események, kapcsolódó események és hibák létrehozása |![Keresés – példa](./media/app-insights-dashboards/61.png) |
| [Elemzés](app-insights-analytics.md)<br/>Hatékony lekérdezési nyelv |![Keresés – példa](./media/app-insights-dashboards/63.png) |
