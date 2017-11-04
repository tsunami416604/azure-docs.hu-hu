---
title: "Hogyan készíthetek... Azure Application insightsban |} Microsoft Docs"
description: "Az Application insights szolgáltatással kapcsolatos gyakori kérdések."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: a32127f14c93012b5ace11ff982824f9ecba7d94
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="how-do-i--in-application-insights"></a>Hogyan tegyem... az Application Insights szolgáltatásban?
## <a name="get-an-email-when-"></a>Az e-mailek Ha...
### <a name="email-if-my-site-goes-down"></a>E-mailek, ha a hely leáll
Állítsa be az [webteszt rendelkezésre állási](app-insights-monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Ha a hely túl van terhelve e-mail
Állítsa be az [riasztás](app-insights-alerts.md) a **kiszolgáló válaszideje**. A küszöbérték, 1 és 2 másodperc között kell működnie.

![](./media/app-insights-how-do-i/030-server.png)

Az alkalmazás is lehet, hogy megjelenítése a törzs jeleit hibát kódok vissza. Riasztást állíthat be **sikertelen kérelmek**.

Ha be szeretné állítani a riasztást a **kivételek**, lehetséges, hogy ehhez [néhány további telepítési](app-insights-asp-net-exceptions.md) adatok láthatók.

### <a name="email-on-exceptions"></a>E-maileket az kivételek
1. [Kivételfigyelés beállítása](app-insights-asp-net-exceptions.md)
2. [A riasztások](app-insights-alerts.md) a kivétel a metrika száma

### <a name="email-on-an-event-in-my-app"></a>E-maileket az alkalmazásom az esemény
Tegyük fel, hogy milyen egy e-mailek egy adott esemény bekövetkezésekor. Az Application Insights közvetlenül ez a lehetőség nem biztosít, de azt is [riasztás küldése metrika ebbe a küszöbérték](app-insights-alerts.md).

Riasztások beállítható [egyéni metrikák](app-insights-api-custom-events-metrics.md#trackmetric), azonban nem egyéni események. A kódírást metrika növelje az esemény bekövetkezésekor:

    telemetry.TrackMetric("Alarm", 10);

Vagy:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Mivel riasztások kétállapotú, be kell fejezni a riasztás érdemes alacsony értéket küldött rendelkezik:

    telemetry.TrackMetric("Alarm", 0.5);

A diagram létrehozása [metrika explorer](app-insights-metrics-explorer.md) a riasztás megtekintéséhez:

![](./media/app-insights-how-do-i/010-alarm.png)

Az érvényesítést, amikor egy rövid időre mid érték fölé megy a metrika riasztás most beállításához:

![](./media/app-insights-how-do-i/020-threshold.png)

Az átlagos időtartam minimális értéke.

E-mailt fog kapni, ha a mérték fölé megy, mind a küszöbérték alá.

Néhány megfontolandó szempontok:

* Riasztást két állapota ("figyelmeztetés" és "kifogástalan") van. Az állapot csak akkor, ha egy metrika érkezik értékeli.
* Az e-mail elküldésekor történik, csak akkor, ha az állapot változik. Miért kell elküldhet magas és alacsony értékű metrikák azt.
* A riasztás kiértékeléséhez, az átlagos használatban van a kapott érték az előző adott időszakban. Ez mindig megtörténik egy metrika érkezik, e-mailek küldhető gyakrabban beállított időszak.
* Mivel e-mailek küldése mind a "riasztás" és "kifogástalan", érdemes figyelembe kell venni a egyszeri esemény kétállapotú feltételeként újra végezni. Például helyett a "feladat kész" esemény "folyamatban lévő feladat" állapotba kerültek, ahol kap e-maileket a kezdő és egy feladat végén.

### <a name="set-up-alerts-automatically"></a>Riasztások beállítása automatikusan
[A PowerShell szolgáltatás használatával hozzon létre új riasztások](app-insights-alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Az Application Insights kezelése a PowerShell használatával
* [Hozzon létre új erőforrások](app-insights-powershell-script-create-resource.md)
* [Hozzon létre új riasztások](app-insights-alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Különböző verzióiból külön telemetria

* Egy alkalmazásban több szerepkört: egyetlen Application Insights-erőforrást használjon, és a szűrést végezni cloud_Rolename. [További információ](app-insights-monitor-multi-role-apps.md)
* Fejlesztői, tesztelési és verzió elválasztó: különböző Application Insights-erőforrások használatára. Vegyen fel a rendszerállapot-kulcsok a Web.config fájlból. [További információ](app-insights-separate-resources.md)
* Jelentéskészítési build verziók: a telemetriai adatok inicializáló használatával tulajdonság hozzáadása. [További információ](app-insights-separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>A figyelő háttérkiszolgálók, illetve az asztali alkalmazások
[A Windows Server SDK modullal](app-insights-windows-desktop.md).

## <a name="visualize-data"></a>Adatok megjelenítése
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>A metrikák a több alkalmazás irányítópult
* A [metrika Explorer](app-insights-metrics-explorer.md), a diagram testreszabásához, és mentse azokat a Kedvencek közé. Rögzítheti az Azure irányítópultot.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Más forrásokból és az Application Insights-adatokkal irányítópult
* [Telemetriai adatok exportálása a Power bi-bA](app-insights-export-power-bi.md).

Vagy

* A SharePoint használata az irányítópulton megjelenő adatok SharePoint-kijelzők. [A folyamatos exportálás és a Stream Analytics segítségével SQL exportálása](app-insights-code-sample-export-sql-stream-analytics.md).  Vizsgálja meg az adatbázis PowerView segítségével, és hozzon létre egy SharePoint-kijelzőt PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Hitelesített vagy névtelen felhasználók szűrése
Ha a felhasználói bejelentkezés, beállíthatja a [hitelesített felhasználói azonosító](app-insights-api-custom-events-metrics.md#authenticated-users). (Ez nem automatikusan megtörténik.)

Ezek közül:

* A megadott felhasználói azonosítók keresése

![](./media/app-insights-how-do-i/110-search.png)

* Szűrő metrikák hitelesített vagy névtelen felhasználók számára

![](./media/app-insights-how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Tulajdonság neve vagy értéke módosítása
Hozzon létre egy [szűrő](app-insights-api-filtering-sampling.md#filtering). Ez lehetővé teszi módosítása vagy a telemetriai adatok szűrése az alkalmazásból az Application Insights elküldése előtt.

## <a name="list-specific-users-and-their-usage"></a>Adott felhasználók listázása és azok használata
Ha szeretné [bizonyos felhasználók keresése](#search-specific-users), beállíthatja a [hitelesített felhasználói azonosító](app-insights-api-custom-events-metrics.md#authenticated-users).

Ha azt szeretné, hogy az adatok, például az oldalak rendelkező felhasználók listáját, megtekintik vagy milyen gyakran jelentkeznek be, két lehetőség közül választhat:

* [Hitelesített felhasználó azonosítója](app-insights-api-custom-events-metrics.md#authenticated-users), [exportálása adatbázisba](app-insights-code-sample-export-sql-stream-analytics.md) és elemezhetik a felhasználói nem megfelelő eszközök segítségével.
* Ha csak kisszámú felhasználók, küldése egyéni események és metrikák érdeklő adatok használata a metrika értékét vagy az esemény neve, és a felhasználói azonosító egy tulajdonság beállítását. Lapmegtekintések elemzéséhez, cserélje le a standard JavaScript trackPageView hívás. Kiszolgálóoldali telemetriai adatok elemzésére, használja a telemetriai adatok inicializáló a felhasználói azonosító hozzáadása a összes kiszolgáló telemetriai adat. Ezek közül és a szegmens metrikák és a keresést a felhasználói azonosító.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Az Application Insights csökkenthető a forgalom az alkalmazásból
* A [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), ilyen a teljesítmény Számláló adatgyűjtő nincs szüksége, modul letiltása.
* Használjon [mintavételi és a szűrés](app-insights-api-filtering-sampling.md) : az SDK-t.
* A weblapok minden lapmegtekintés jelentett Ajax-hívások számának korlátozása. A parancsfájl részlet után a `instrumentationKey:...` , beszúrása: `,maxAjaxCallsPerView:3` (vagy megfelelő számú).
* Ha használ [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric), a metrika értékének kötegek összesítés számítási az eredmény elküldése előtt. A trackmetric() függvény, amely biztosítja, hogy egy túlterhelése van.

További információ [tarifa- és a kvóták](app-insights-pricing.md).

## <a name="disable-telemetry"></a>Telemetria letiltása
A **dinamikusan leállítására és elindítására** gyűjtésére és átviteli telemetriai adatot a kiszolgálóról:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



A **tiltsa le a kiválasztott szabványos gyűjtők** – például teljesítményszámlálókat, HTTP-kérelmek vagy függőségek - törlése vagy a megfelelő sorok megjegyzéssé [ApplicationInsights.config](app-insights-api-custom-events-metrics.md). Sikerült ehhez, például, ha azt szeretné, hogy a saját TrackRequest adatküldéshez.

## <a name="view-system-performance-counters"></a>Nézet rendszerteljesítmény-számlálók
A metrikák megjelenítheti a metrikaböngészőben között olyan rendszer teljesítményszámlálók. Van egy előre meghatározott panel című **kiszolgálók** , amely megjeleníti, hogy ezek.

![Nyissa meg az Application Insights-erőforrást, és kattintson a kiszolgálók](./media/app-insights-how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Ha nincs teljesítményszámláló-adatok
* **IIS-kiszolgáló** a saját számítógépén vagy a virtuális gép. [Állapotmonitor telepítése](app-insights-monitor-performance-live-website-now.md).
* **Azure-webhelyre** -teljesítményszámlálók még nem támogatott. Nincsenek több metrikákat kaphat az Azure-webhelyre Vezérlőpult szabványos részeként.
* **UNIX kiszolgáló** - [collectd telepítése](app-insights-java-collectd.md)

### <a name="to-display-more-performance-counters"></a>További teljesítményszámlálók megjelenítése
* Első, [új diagram hozzáadása](app-insights-metrics-explorer.md) , és hogy van-e a számláló a kínálunk alapvető készlet.
* Ha nem, [adhatja hozzá a számláló a teljesítményszámláló modul által gyűjtött](app-insights-performance-counters.md).
