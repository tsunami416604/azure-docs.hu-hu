---
title: Hogyan ... az Azure Application Insightsban | Microsoft dokumentumok
description: Gyakori kérdések az Application Insightsban.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 8d4b1e79c48b14ed7dce756468e4c48d633c3f04
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536862"
---
# <a name="how-do-i--in-application-insights"></a>Hogyan tegyem... az Application Insights szolgáltatásban?
## <a name="get-an-email-when-"></a>Kap egy e-mailt, ha ...
### <a name="email-if-my-site-goes-down"></a>E-mail, ha a webhelyem leáll
Állítson be egy [rendelkezésre állási webes tesztet.](../../azure-monitor/app/monitor-web-app-availability.md)

### <a name="email-if-my-site-is-overloaded"></a>E-mail, ha a webhelyem túlterhelt
Állítson be [riasztást](../../azure-monitor/app/alerts.md) a **kiszolgáló válaszidejére**. Az 1 és 2 másodperc közötti küszöbértéknek működnie kell.

![](./media/how-do-i/030-server.png)

Az alkalmazás a hibakódok visszaküldésével is megterhelő jeleket mutathat. Állítson be riasztást a **sikertelen kérelmekre.**

Ha riasztást szeretne beállítani a **kiszolgálókivételekkel**kapcsolatban, előfordulhat, hogy az adatok megtekintéséhez [további beállításokat](../../azure-monitor/app/asp-net-exceptions.md) kell végeznie.

### <a name="email-on-exceptions"></a>E-mail a kivételekről
1. [Kivételfigyelés beállítása](../../azure-monitor/app/asp-net-exceptions.md)
2. [Riasztás beállítása](../../azure-monitor/app/alerts.md) a Kivételek száma mérőszám mérőszámra

### <a name="email-on-an-event-in-my-app"></a>E-mail küldése egy eseményen az alkalmazásomban
Tegyük fel, hogy egy adott esemény bekövetkeztekor e-mailt szeretne kapni. Az Application Insights nem biztosítja ezt a lehetőséget közvetlenül, de [riasztást küldhet, ha egy metrika átlép egy küszöbértéket.](../../azure-monitor/app/alerts.md)

Riasztások egyéni [metrikákon](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)állítható be, de nem egyéni események. Írjon be néhány kódot, hogy növelje a metrikát, amikor az esemény bekövetkezik:

    telemetry.TrackMetric("Alarm", 10);

vagy:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Mivel a riasztások két állapotúak, alacsony értéket kell küldenie, ha figyelembe veszi, hogy a riasztás véget ért:

    telemetry.TrackMetric("Alarm", 0.5);

Hozzon létre egy diagramot a [metrikakezelőben](../../azure-monitor/platform/metrics-charts.md) az ébresztés megtekintéséhez:

![](./media/how-do-i/010-alarm.png)

Most állítsa be a riasztást, hogy tűz, ha a metrika megy egy közepes érték felett egy rövid ideig:

![](./media/how-do-i/020-threshold.png)

Állítsa az átlagolási időszakot a minimumra.

E-maileket kap, ha a mérőszám a küszöbérték fölé és alá kerül.

Néhány megfontolandó szempont:

* A riasztás nak két állapota van ("riasztás" és "kifogástalan"). Az állapot kiértékelése csak akkor történik meg, ha egy metrika érkezik.
* Az e-mail küldése csak akkor történik meg, ha az állapot megváltozik. Ez az oka annak, hogy magas és alacsony értékű mutatókat is el kell küldenie.
* A riasztás kiértékeléséhez az előző időszak fogadott értékeinek átlagát veszi figyelembe. Ez minden alkalommal előfordul, amikor egy metrika érkezik, így az e-mailek gyakrabban küldhetők, mint a beállított időszak.
* Mivel az e-maileket a rendszer "riasztás" és "egészséges" állapotban is elküldi, érdemes megfontolniaz egyesélyes esemény újragondolását kétállapotú állapotként. Például ahelyett, hogy a "feladat befejeződött" esemény, van egy "munka folyamatban van" feltétel, ahol kap e-maileket elején és végén a feladat.

### <a name="set-up-alerts-automatically"></a>Értesítések automatikus beállítása
[Új riasztások létrehozása a PowerShell használatával](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Az Application Insights kezelése a PowerShell használatával
* [Új erőforrások létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Új riasztások létrehozása](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Telemetriai adatok elkülönítése a különböző verzióktól

* Több szerepkör egy alkalmazásban: Egyetlen Application Insights-erőforrás használata, és cloud_Rolename [szűrése.](../../azure-monitor/app/app-map.md)
* Fejlesztési, tesztelési és kiadási verziók elkülönítése: Különböző Application Insights-erőforrások használata. Vedd fel a műszerezési kulcsokat web.config. [További információ](../../azure-monitor/app/separate-resources.md)
* Build-verziók jelentéskészítési verziói: Tulajdonság hozzáadása telemetriai inicializáló használatával. [További információ](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Háttérkiszolgálók és asztali alkalmazások figyelése
[Használja a Windows Server SDK modult](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Adatok vizualizációja
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Irányítópult több alkalmazás mutatóival
* A [Metrikus kezelőben](../../azure-monitor/platform/metrics-charts.md)testreszabhatja a diagramot, és kedvencként mentheti. Rögzítse az Azure irányítópultjára.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Irányítópult más forrásokból származó adatokkal és Alkalmazáselemzési adatokkal
* [Telemetriai adatok exportálása a Power BI-ba](../../azure-monitor/app/export-power-bi.md ).

Vagy

* A SharePoint használata irányítópultként, adatok megjelenítése a SharePoint-kijelzőkön. [Az SQL-fájlba való exportáláshoz használjon folyamatos exportálást és Stream Analytics-et.](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md)  A PowerView segítségével megvizsgálhatja az adatbázist, és sharePoint-kijelzőt hozhat létre a PowerView-hoz.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Névtelen vagy hitelesített felhasználók kiszűrése
Ha a felhasználók bejelentkeznek, beállíthatja a [hitelesített felhasználói azonosítót.](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) (Ez nem történik meg automatikusan.)

Ezután:

* Keresés adott felhasználói azonosítókon

![](./media/how-do-i/110-search.png)

* Mérőszámok szűrése névtelen vagy hitelesített felhasználókra

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Tulajdonságnevek vagy értékek módosítása
Hozzon létre [egy szűrőt](../../azure-monitor/app/api-filtering-sampling.md#filtering). Ez lehetővé teszi, hogy módosítsa vagy szűrje a telemetriai adatokat, mielőtt az alkalmazásból elküldené az Application Insightsnak.

## <a name="list-specific-users-and-their-usage"></a>Adott felhasználók és használatuk listázása
Ha csak [konkrét felhasználókat](#search-specific-users)szeretne keresni, beállíthatja a [hitelesített felhasználói azonosítót.](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)

Ha olyan felhasználókat szeretne felsorolni, akik olyan adatokat tartalmaznak, mint például, hogy milyen oldalakat néznek meg, vagy milyen gyakran jelentkeznek be, két lehetőség közül választhat:

* [Állítsa be a hitelesített felhasználói azonosítót,](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) [exportálja az adatbázisba,](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) és használja a megfelelő eszközöket a felhasználói adatok elemzéséhez.
* Ha csak kis számú felhasználóval rendelkezik, küldjön egyéni eseményeket vagy mutatókat, az érdekes adatokat használja metrikaértékként vagy eseménynévként, és a felhasználói azonosítót tulajdonságként. Az oldalmegtekintések elemzéséhez cserélje le a szabványos JavaScript-trackPageView hívást. A kiszolgálóoldali telemetriai adatok elemzéséhez használjon telemetriai inicializálót a felhasználói azonosító hozzáadása az összes kiszolgálótelemetriához. Ezután szűrheti és szegmentálhatja a mutatókat és a kereséseket a felhasználói azonosítón.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Az alkalmazásból az Application Insightsba irányuló forgalom csökkentése
* Az [ApplicationInsights.config alkalmazásban](../../azure-monitor/app/configuration-with-applicationinsights-config.md)tiltsa le a szükségtelen modulokat, például a teljesítményszámláló-gyűjtőt.
* Használjon [mintavételezést és szűrést](../../azure-monitor/app/api-filtering-sampling.md) az SDK-nál.
* A weboldalak, Számának korlátozása Ajax hívások jelentett minden oldalnézetben. A szkriptkódrészletután `instrumentationKey:...` , írja `,maxAjaxCallsPerView:3` be: (vagy egy megfelelő számot).
* Ha [trackmetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)használata esetén kiszámítja a metrikaértékek kötegeinek összesítését az eredmény elküldése előtt. Van egy túlterhelés trackmetric() amely előírja, hogy.

További információ [az árakról és a kvótákról.](../../azure-monitor/app/pricing.md)

## <a name="disable-telemetry"></a>Telemetriai adatok letiltása
A telemetriai adatok gyűjtésének és továbbításának **dinamikus leállítása és indítása** a kiszolgálóról:

### <a name="aspnet-classic-applications"></a>ASP.NET Klasszikus alkalmazások

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Egyéb alkalmazások
Nem ajánlott singleton használata `TelemetryConfiguration.Active` konzolon vagy ASP.NET Core alkalmazásokban.
ha saját `TelemetryConfiguration` maga hozta `DisableTelemetry` `true`létre a példányt - állítsa a -

A ASP.NET Core alkalmazások `TelemetryConfiguration` elérheti példány használatával [ASP.NET Core függőség injekció](/aspnet/core/fundamentals/dependency-injection/). További részleteket az [ApplicationInsights for ASP.NET Core applications](../../azure-monitor/app/asp-net-core.md) cikkben talál.

## <a name="disable-selected-standard-collectors"></a>Kijelölt szabványos gyűjtők letiltása
Letilthatja a szabványos gyűjtők (például teljesítményszámlálók, HTTP-kérelmek vagy függőségek)

* **alkalmazások ASP.NET** - Az [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájl megfelelő sorainak törlése vagy megjegyzése
* **ASP.NET alapvető alkalmazások** – Kövesse a telemetriai modulok konfigurációs beállításait az [ApplicationInsights ASP.NET Core alkalmazásban](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Rendszerteljesítmény-számlálók megtekintése
A metrikákban megjelenhető mérőszámok között található a rendszer teljesítményszámlálói. Van egy előre definiált panel nevű **szerverek,** amely megjeleníti több közülük.

![Az Application Insights-erőforrás megnyitása és a Kiszolgálók elemre kattintva](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Ha nem lát teljesítményszámláló-adatokat
* **IIS-kiszolgáló** saját gépen vagy virtuális gépen. [Állapotfigyelő telepítése](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Azure-webhely** – még nem támogatjuk a teljesítményszámlálókat. Számos metrika kaphat, mint az Azure webhely vezérlőpultjának szabványos része.
* **Unix szerver** - [telepítése összegyűjtve](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>További teljesítményszámlálók megjelenítése
* Először [adjon hozzá egy új diagramot,](../../azure-monitor/platform/metrics-charts.md) és nézze meg, hogy a számláló az általunk kínált alapkészletben van-e.
* Ha nem, [adja hozzá a számlálót a teljesítményszámláló modul által gyűjtött készlethez](../../azure-monitor/app/performance-counters.md).
