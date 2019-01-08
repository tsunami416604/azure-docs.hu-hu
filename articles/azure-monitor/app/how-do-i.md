---
title: Hogyan tegyem... az Azure Application Insights |} A Microsoft Docs
description: Az Application Insights – gyakori kérdések.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: 5e22a3f3b362811fd87460ec41b61a990f4d83fb
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074206"
---
# <a name="how-do-i--in-application-insights"></a>Hogyan tegyem... az Application Insights szolgáltatásban?
## <a name="get-an-email-when-"></a>E-mail küldése Ha...
### <a name="email-if-my-site-goes-down"></a>E-mailben, ha a webhely leáll
Állítsa be egy [rendelkezésre állási webes teszt](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Ha saját túl van terhelve e-mail
Állítsa be egy [riasztás](../../azure-monitor/app/alerts.md) a **kiszolgáló válaszideje**. A küszöbértéket 1 és 2 másodperc között működik.

![](./media/how-do-i/030-server.png)

Az alkalmazás is előfordulhat, hogy megjelenítése a törzs jeleit hibát kódok felismerésével. Riasztások beállítása **sikertelen kérelmek**.

Ha szeretne riasztást állíthat be **kiszolgálókivételek**, lehetséges, hogy ehhez [néhány további telepítési](../../azure-monitor/app/asp-net-exceptions.md) adatok láthatók.

### <a name="email-on-exceptions"></a>E-mail küldése, ha kivételek
1. [Kivételfigyelés beállítása](../../azure-monitor/app/asp-net-exceptions.md)
2. [Riasztások beállítása](../../azure-monitor/app/alerts.md) a kivétel a metrika darabszám

### <a name="email-on-an-event-in-my-app"></a>E-mail küldése, ha egy esemény az alkalmazásom
Tegyük fel, amelyet szeretne kaphat e-mailt, ha egy adott esemény bekövetkezik. Az Application Insights közvetlenül nem biztosítja ezt a lehetőséget, de azt is [riasztás küldése, ha egy metrika átlépi a küszöbértéket](../../azure-monitor/app/alerts.md).

Riasztásokat is beállíthat [egyéni metrikákat](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), bár nem egyéni eseményeket. Írjon egy kódrészletet egy metrika növelhető, ha az esemény akkor fordul elő:

    telemetry.TrackMetric("Alarm", 10);

Vagy:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Riasztások két állapota van, mert rendelkezik alacsony értéket küldött, fontolja meg a riasztás a lejárt:

    telemetry.TrackMetric("Alarm", 0.5);

A diagram létrehozásához [metrika explorer](../../azure-monitor/app/metrics-explorer.md) a riasztás megtekintéséhez:

![](./media/how-do-i/010-alarm.png)

Most állítsa be egy riasztás értesíti, ha a metrika egy rövid ideig túllépik mid értéket:

![](./media/how-do-i/020-threshold.png)

A minimális átlagolási időtartam megadása.

E-mailt fog kapni, ha túllépik a metrika és is a küszöbérték alá.

Néhány megfontolandó szempont:

* Riasztás kétállapotú ("alert" és "kifogástalan") rendelkezik. Az állapot akkor lesz kiértékelve, csak akkor, amikor egy metrika érkezik.
* Egy e-mail lesz elküldve, csak akkor, amikor az állapot. Ez a Miért kell elküldhet magas és alacsony értékű metrikákat.
* A riasztás kiértékeléséhez, az átlagos készül a kapott érték az előző időszakban. Ez történik minden alkalommal, amikor egy metrika érkezik, így e-mailek küldhetők gyakrabban, mint a beállított időszak.
* Mivel mind a "alert" és "kifogástalan" kapnak e-mailt, érdemes újra felhőmegoldásokat kétállapotú feltételeként végeredménye események figyelembe. Például helyett a "feladat befejezve" esemény "feladat folyamatban" állapotba kerültek, ahol megkapja e-maileket a kezdő és a egy feladat végén.

### <a name="set-up-alerts-automatically"></a>Automatikus riasztások beállítása
[Új riasztások létrehozása a PowerShell használatával](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Az Application Insights kezelése PowerShell használatával
* [Új erőforrásokat hozhat létre](../../azure-monitor/app/powershell-script-create-resource.md)
* [Új riasztások létrehozása](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Különböző verzióit külön telemetria

* Több szerepkört egy alkalmazásban: Egy Application Insights-erőforrást használja, és szűrheti a [cloud_Rolename](../../azure-monitor/app/app-map.md).
* A fejlesztési, tesztelési és kiadásverziót elválasztása: Használja a különböző Application Insights-erőforrást. Vegyen fel a kialakítási kulcs, melyet a web.config fájlból. [További információ](../../azure-monitor/app/separate-resources.md)
* Jelentéskészítési build verziója: Adjon hozzá egy tulajdonságot egy telemetriainicializáló eszközzel. [További információ](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>A figyelő háttérkiszolgálók és asztali alkalmazások
[A Windows Server SDK modul használata](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Adatok vizualizációja
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Több alkalmazás a metrika az irányítópulton
* A [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md), a diagram testreszabásához, és kedvencként mentheti. Az Azure-irányítópulton rögzítheti is.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Az adatokat más forrásokból és Application Insights irányítópult
* [Telemetria exportálása a Power bi-bA](../../azure-monitor/app/export-power-bi.md ).

Vagy

* Az irányítópulton, az adatok megjelenítése a SharePoint-kijelzőket a SharePoint használja. [A folyamatos exportálás és a Stream Analytics használata SQL-exportálás](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Vizsgálja meg az adatbázis PowerView használatával, és hozzon létre egy SharePoint-kijelzőt PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Szűrje ki a névtelen vagy a hitelesített felhasználók
Ha a felhasználók bejelentkeznek, beállíthatja a [hitelesített felhasználó azonosítója](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (Ez nem valósul meg automatikusan.)

Ezek közül:

* Az adott felhasználói azonosítók keresése

![](./media/how-do-i/110-search.png)

* Hitelesített vagy névtelen felhasználók mérőszámok szűrése

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Tulajdonság neve vagy értéke módosítása
Hozzon létre egy [szűrő](../../azure-monitor/app/api-filtering-sampling.md#filtering). Ez lehetővé teszi módosítása vagy szűrőtelemetria az alkalmazástól az Application Insightsba elküldése előtt.

## <a name="list-specific-users-and-their-usage"></a>Adott felhasználók listázása és azok használata
Ha csak át szeretné [adott felhasználók keresése](#search-specific-users), beállíthatja a [hitelesített felhasználó azonosítója](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Ha azt szeretné, hogy az adatok, például hogy milyen lapok olyan felhasználó listáját, tekintse meg vagy gyakoriságát, jelentkezzen be, két lehetősége van:

* [Hitelesített felhasználó azonosítója](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [adatbázis exportálása](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) és megfelelő eszközök használatával van a felhasználói adatok elemzéséhez.
* Ha csak olyan felhasználók kis számú, egyéni eseményeket vagy mérőszámokat, az Önt érdeklő adatok használatával, a metrika értéke vagy esemény neve, valamint a felhasználói azonosító tulajdonságként küldése. Lapmegtekintések elemzéséhez, a standard JavaScript trackPageView hívás helyett. Kiszolgálóoldali telemetria elemzéséhez, hogy egy telemetriainicializáló használatával a felhasználói azonosító hozzá az összes telemetriát. Ezek közül szegmentálására és szűrésére is metrikák és a keresés felhasználói azonosító.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Forgalom csökkentése érdekében az alkalmazás az Application insights
* A [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), tiltsa le az ilyen a teljesítmény számláló gyűjtő már nincs szüksége, modulokat.
* Használat [mintavételezés és szűrés](../../azure-monitor/app/api-filtering-sampling.md) , az SDK-t.
* A weblapok minden lapmegtekintés jelentett Ajax-hívások számának korlátozása. Miután a parancsfájl kódrészletben `instrumentationKey:...` , insert: `,maxAjaxCallsPerView:3` (vagy egy megfelelő szám).
* Ha használ [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), az összesítést, metrikaértékek kötegek számítási az eredmény elküldése előtt. Nincs a trackmetric() függvény, amely biztosítja, hogy egy túlterhelésére.

Tudjon meg többet [árai és kvótái](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Telemetria letiltása
A **dinamikusan leállítására és elindítására** a gyűjtemény és továbbítását a telemetriai adatokat a kiszolgálóról:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



A **tiltsa le a kiválasztott standard naplógyűjtők** – például teljesítményszámlálók, HTTP-kérések vagy - függőségeket törölni, vagy tegye megjegyzésbe a megfelelő a [ApplicationInsights.config](../../azure-monitor/app/api-custom-events-metrics.md). Sikerült ezt megteheti, például, ha azt szeretné, a saját TrackRequest adatküldéshez.

## <a name="view-system-performance-counters"></a>Rendszerteljesítmény-számlálók megtekintése
A metrikák, megjelenítheti a metrikaböngészőben többek között olyan rendszer teljesítményszámlálók. Van egy előre meghatározott panel címe **kiszolgálók** , amely megjeleníti a többre.

![Nyissa meg az Application Insights-erőforrást, és kattintson a kiszolgálók](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Ha nincs teljesítményszámláló-adatok
* **Az IIS-kiszolgálón** a saját számítógépén vagy virtuális gépen. [Telepítse az Állapotfigyelőt](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Azure-webhely** -teljesítményszámlálók még nem támogatott. Nincsenek kap az Azure-webhely Vezérlőpult standard részeként több metrikát.
* **UNIX-kiszolgáló** - [összegyűjtött telepítése](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>További teljesítményszámlálók megjelenítése
* Először [új diagram hozzáadása](../../azure-monitor/app/metrics-explorer.md) , és hogy van-e a számláló az alapszintű készletben, amely biztosítunk.
* Ha nem, [adhatja hozzá a számláló a teljesítményszámláló modul által gyűjtött](../../azure-monitor/app/performance-counters.md).
