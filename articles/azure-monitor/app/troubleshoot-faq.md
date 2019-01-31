---
title: Az Azure Application Insights – gyakori kérdések |} A Microsoft Docs
description: Gyakori kérdések az Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mbullwin
ms.openlocfilehash: 115be0ad1b7dec44f036f6d50c2ac30ceba37ba7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457088"
---
# <a name="application-insights-frequently-asked-questions"></a>Az Application Insights: Gyakori kérdések

## <a name="configuration-problems"></a>Konfigurációs problémák kizárása
*Hiba történt a beállítás lépett saját:*

* [.NET-alkalmazás](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
* [Egy már futó alkalmazás](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot)
* [Az Azure diagnostics](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Java webalkalmazások](../../azure-monitor/app/java-troubleshoot.md)

*Nincs adat jelenik meg a kiszolgálón lévő*

* [Végezze el a tűzfalkivételek beállítása](../../azure-monitor/app/ip-addresses.md)
* [ASP.NET-kiszolgáló beállítása](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Egy Java-kiszolgáló beállítása](../../azure-monitor/app/java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Használható az Application Insights...?

* [Web apps egy IIS-kiszolgálón – a helyszínen vagy egy virtuális Gépre](../../azure-monitor/app/asp-net.md)
* [Java-webalkalmazások](../../azure-monitor/app/java-get-started.md)
* [Node.js-alkalmazások](../../azure-monitor/app/nodejs.md)
* [Az Azure Web apps](../../azure-monitor/app/azure-web-apps.md)
* [Az Azure cloud Services](../../azure-monitor/app/cloudservices.md)
* [Docker-ban futó kiszolgálók](../../azure-monitor/app/docker.md)
* [Egyoldalas web Apps alkalmazások](../../azure-monitor/app/javascript.md)
* [SharePoint](sharepoint.md)
* [Windows asztali alkalmazás](../../azure-monitor/app/windows-desktop.md)
* [Más platformok](../../azure-monitor/app/platforms.md)

## <a name="is-it-free"></a>Az ingyenes?

Igen, kísérleti célokra. Az alapszintű díjszabási csomaggal az alkalmazás egy bizonyos adatkeret meg minden hónapban díjmentesen is küldhet. Az ingyenes kerettel elég nagy lefedik fejlesztéshez és tegyünk közzé egy alkalmazást a felhasználók kis számú. Egy korlát, hogy több mint egy adott mennyiségű adatot feldolgozás alatt állíthatja be.

Nagyobb mennyiségű, telemetriát a GB-os alapján számítjuk fel. Néhány tipp útmutatást biztosítunk [a költségek korlátozására](../../azure-monitor/app/pricing.md).

A vállalati csomag kötelezettséggel jár, hogy minden webkiszolgáló csomópontját a telemetriai adatokat küld minden nap. Emellett akkor megfelelő, ha szeretné használni a nagy méretű folyamatos exportálása.

[Olvassa el, a díjszabással](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>IP-címek fenntartási költségszámítási azt?

* Nyissa meg a **használat és becsült költségek lapon** az Application Insights-erőforrás. Nincs legutóbbi használati diagramot. Adatmennyiségi korlát is beállíthat, ha azt szeretné.
* Nyissa meg a [panel az Azure számlázási](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) a számlák megtekintéséhez az összes erőforrás között.

## <a name="q14"></a>Mi az Application Insights módosítja a saját project?
A részletek projekt típusától függ. Egy webes alkalmazáshoz:

* Ezeket a fájlokat ad hozzá a projekthez:

  * ApplicationInsights.config.
  * ai.js
* A NuGet-csomagok telepíti:

  * *Application Insights API* – a core API
  * *Application Insights API-webalkalmazások számára* – az a kiszolgálóról a telemetria küldése
  * *Application Insights API a JavaScript-alkalmazásokhoz* – az telemetriát küldjön az ügyfélről

    A csomagok ezekkel a szerelvényekkel tartalmazza:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Beszúrja azokat:

  * Web.config
  * packages.config
* (Új csak - projektek, ha Ön [Application Insights hozzáadása egy meglévő projekt][start], ehhez manuálisan kell.) A kódrészletek szúr be az ügyfél és kiszolgáló kódot inicializálása őket az Application Insights-erőforrás azonosítója. Például egy MVC-alkalmazásban kódot szúr be a mesterweblap Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hogyan frissíthetek az SDK korábbi verziók?
Tekintse meg a [kibocsátási megjegyzések](../../azure-monitor/app/release-notes.md) az SDK az alkalmazás típusát megfelelő.

## <a name="update"></a>Hogyan válthatok az Azure erőforrás-projektemmel elküldi az adatokat?
A Megoldáskezelőben kattintson a jobb gombbal `ApplicationInsights.config` válassza **Update Application Insights**. Az adatokat küldhet egy meglévő vagy új erőforrást az Azure-ban. A frissítési varázsló módosítja a kialakítási kulcs az applicationinsights.config fájlban, amely azt határozza meg, ahol a kiszolgáló SDK-t küld-e az adatokat. Hacsak nem törli a "Frissítés az összes", a kulcs, ahol megjelenik a weblapok is megváltozik.

## <a name="what-is-status-monitor"></a>Mi az Állapotfigyelő?

Egy asztali alkalmazás, amely az IIS-webkiszolgálót a webalkalmazásokban az Application Insights konfigurálásához használhatja. Nem gyűjt telemetriai: állíthatja le, ha nem konfigurál egy alkalmazást. 

[További információk](../../azure-monitor/app/monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Milyen telemetriai adatokat gyűjt a rendszer az Application Insights?

A kiszolgáló webes alkalmazásokból:

* HTTP-kérések
* [Függőségek](../../azure-monitor/app/asp-net-dependencies.md). Hívások: SQL-adatbázisok; Külső szolgáltatások; HTTP-hívások Az Azure Cosmos DB, tábla, a blob storage és queue. 
* [Kivételek](../../azure-monitor/app/asp-net-exceptions.md) veremkiíratásokat és.
* [Teljesítményszámlálók](../../azure-monitor/app/performance-counters.md) – Ha [Állapotfigyelőt](../../azure-monitor/app/monitor-performance-live-website-now.md), [Azure monitoring](../../azure-monitor/app/azure-web-apps.md), vagy a [Application Insights összegyűjtött író](../../azure-monitor/app/java-collectd.md).
* [Egyéni események és mérőszámok](../../azure-monitor/app/api-custom-events-metrics.md) , hogy kódot.
* [Nyomkövetési naplók](../../azure-monitor/app/asp-net-trace-logs.md) , ha a megfelelő gyűjtő konfigurálásához.

A [ügyfél weblapok](../../azure-monitor/app/javascript.md):

* [Oldalmegtekintések száma](usage-overview.md)
* [AJAX-hívások](../../azure-monitor/app/asp-net-dependencies.md) kérések száma a parancsprogram futtatásához.
* Lapbetöltési adatainak megtekintése
* Felhasználók és munkamenetek száma
* [Hitelesített felhasználói azonosítók](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)

Más forrásokból, ha konfigurálja őket:

* [Az Azure diagnostics](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Importálás analyticshez](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
* [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
* [Logstash](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Szűrje ki vagy valamennyi telemetria módosítani?

Igen, a kiszolgáló írhat a:

* Telemetria processzor szűrése, vagy további tulajdonságok telemetriai kiválasztott cikkek megkövetelése azok elküldéséhez a saját alkalmazásából.
* Tulajdonságok hozzáadása a telemetria minden elem Telemetriainicializálót.

A további [ASP.NET](../../azure-monitor/app/api-filtering-sampling.md) vagy [Java](../../azure-monitor/app/java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Hogyan város, ország és egyéb földrajzi adatok kiszámítása?

Áttekintjük az IP-cím (IPv4 vagy IPv6) a webes ügyfél használatával [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Böngésző telemetriai adatokat: A küldő IP-cím gyűjtünk.
* Telemetriát: Az Application Insights-modult összegyűjti az ügyfél IP-cím. Nem gyűjtenek, ha `X-Forwarded-For` van beállítva.

Konfigurálhatja a `ClientIpHeaderTelemetryInitializer` beolvasandó az IP-cím egy másik fejlécet. Egyes rendszerekben, például áthelyezés olyan proxy, és töltse be a terheléselosztó vagy a CDN-t `X-Originating-IP`. [További információk](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Is [a Power BI használata](../../azure-monitor/app/export-power-bi.md ) a kérelmek telemetriai adatai megjelenítéséhez a térképen.


## <a name="data"></a>Mennyi ideig őrzi meg adatok a portálon? A biztonságos?
Vessen egy pillantást [adatok megőrzésére és az adatvédelmi][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Sikerült a személyes adatok küldhetők a telemetriát?

Ez akkor lehetséges, ha a kód elküldi ezeket az adatokat. Azt is történhet, ha a változók a hívásláncokat személyes adatokat tartalmaznak. A fejlesztői csapat, győződjön meg arról, hogy a személyes adatok megfelelő kezeléséről kockázatbecslést kell végezniük. [További információ az adatok megőrzése és adatvédelmi](../../azure-monitor/app/data-retention-privacy.md).

**Az összes** az ügyfél webcímet oktettjének mindig értéke 0 után a földrajzi attribútumokat kulcskeresési vannak.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>A Rendszerállapotkulcsot látható lesz a weblap forrása. 

* Ez a gyakori eljárás a figyelési megoldások.
* Az adatok ellopására nem használható.
* Az adatok vagy az eseményindító riasztások tevékenységdiagramon használható.
* A Microsoft nem hallott, hogy minden ügyfél esetében az ilyen problémákat.

Sikerült:

* Ügyfél és kiszolgáló adatokhoz (külön Application Insights-erőforrások) két külön Erőforráskulcsot használja. Vagy
* Írni a proxy, amely a kiszolgálón fut, és a webes ügyfél adatait, hogy a proxyn keresztül történő küldése.

## <a name="post"></a>Hogyan ellenőrizhetem a diagnosztikai keresésben POST data?
Automatikusan azt ne naplózza a POST data, de használhat TrackTrace hívás: helyezze az adatokat a üzenet paraméterben. Ez méretkorlátja hosszabb karakterlánc-tulajdonságok, mint a azonban nem lehet szűrni rajta.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Egy vagy több Application Insights-erőforrások érdemes használni?

Egyetlen üzleti rendszerekben használja az összetevők vagy a szerepköröket egy erőforrást. Használjon külön erőforrásokat, fejlesztési, tesztelési és változatból származnak, és független alkalmazásokhoz.

* [A hozzászólás itt talál](separate-resources.md)
* [Példa - felhőszolgáltatás munkavégző és a webes szerepkör](../../azure-monitor/app/cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hogyan dinamikusan módosítható a kialakítási kulcsot?

* [Itt vitafórum](separate-resources.md)
* [Példa - felhőszolgáltatás munkavégző és a webes szerepkör](../../azure-monitor/app/cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Mik azok a felhasználók és munkamenetek száma?

* A JavaScript SDK Csoporttevékenységek a webes ügyfél azonosítsa azokat a visszatérő felhasználókat, a felhasználói cookie-k és a egy munkamenetcookie-t állítja be.
* Ha nincsenek ügyféloldali parancsfájlok, is [állítsa be a cookie-kat a kiszolgálón](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Ha egy valós felhasználói meg a különböző böngészők vagy a privát/inkognitó böngészés használatával, vagy különböző gépek, majd egynél többször beleszámítanak.
* A bejelentkezett felhasználó azonosíthatja a gépek és a böngészők, adjon hozzá hívást [setAuthenticatedUserContext()](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Engedélyezte, e mindent az Application insights szolgáltatásban?
| Mit kell megjelennie | Hogyan kérhet | Miért célszerű az |
| --- | --- | --- |
| Rendelkezésre állási diagramok |[Webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) |Tudja, hogy a webes alkalmazás mentése |
| Server app-teljesítmény: válaszidők,... |[Az Application Insights hozzáadása a projekthez](../../azure-monitor/app/asp-net.md) vagy [AI állapotfigyelő telepítése a kiszolgálón](../../azure-monitor/app/monitor-performance-live-website-now.md) (vagy saját kód írása [függőségek nyomon](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)) |Teljesítményoptimalizált problémák észlelése |
| Függőségi telemetria |[Mesterséges Intelligencia Állapotmonitor telepítése a kiszolgálón](../../azure-monitor/app/monitor-performance-live-website-now.md) |Adatbázisok vagy más külső összetevők kapcsolatos problémák diagnosztizálása |
| Kivételek hívásláncokat beolvasása |[Szúrja be a TrackException kódját](../../azure-monitor/app/asp-net-exceptions.md) (de néhány automatikusan jelenti) |Észlelheti és diagnosztizálhatja a kivételek |
| Keresés a nyomkövetési naplók |[Naplózás adapter hozzáadása](../../azure-monitor/app/asp-net-trace-logs.md) |Kivételek, teljesítmény problémák diagnosztizálása |
| Ügyfél használatának alapjait: oldalmegtekintések, munkamenetek,... |[A weboldalak JavaScript inicializátor](../../azure-monitor/app/javascript.md) |Használatelemzés |
| Egyéni ügyfél-metrikák |[Weblapok hívások nyomon követése](../../azure-monitor/app/api-custom-events-metrics.md) |Felhasználói élmény |
| Kiszolgáló egyéni metrikák |[A kiszolgáló nyomkövetési hívások](../../azure-monitor/app/api-custom-events-metrics.md) |Üzleti intelligencia |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Miért vannak nem egyenlő a számát, a Keresés és a metrikák?

[Mintavételi](../../azure-monitor/app/sampling.md) csökkenti azon elemeinek telemetriát (kérelmeket, egyéni eseményeket és így tovább) ténylegesen a portál az alkalmazásából származó küldött. A keresés ténylegesen kapott elemek száma látható. A mérőszám-diagramok, amely megjeleníti az események számát az eredeti bekövetkezett események száma látható. 

Minden elemet, amelyet a rendszer szintén továbbíthatja a Microsoftnak végzi egy `itemCount` tulajdonságot, amely az eredeti események számát mutatja, hogy az elem jelöli. Mintavételi művelet megfigyelni, ez a lekérdezés Analytics futtathatja:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Az Application Insights konfigurálása

Is [PowerShell-parancsfájlokat írhat](../../azure-monitor/app/powershell.md) használata az Azure erőforrás-figyelő:

* Hozzon létre, és frissítse az Application Insights-erőforrást.
* Állítsa be, a díjszabással.
* A kialakítási kulcs beszerzése.
* Metrikariasztás hozzáadása.
* Adjon hozzá egy rendelkezésre állási tesztet.

Nem állítsa be a Metrikaböngésző jelentést, vagy állítsa be a folyamatos exportálást.

### <a name="querying-the-telemetry"></a>A telemetriai adatok lekérdezése

Használja a [REST API-val](https://dev.applicationinsights.io/) futtatásához [Analytics](../../azure-monitor/app/analytics.md) lekérdezéseket.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Hogyan állíthatok be egy riasztást az esemény?

Azure-riasztások a metrikák csak olyan. Hozzon létre egy egyéni metrika, amely átlép egy érték küszöbértéket, minden alkalommal, amikor az esemény akkor következik be. Majd állítsa be a metrika a riasztást. Vegye figyelembe a következőket: értesítést fog kapni, amikor a metrika átlépi a küszöbértéket bármelyik irányba; nem kap egy értesítést, amíg az első metsző, függetlenül attól, hogy-e a kezdeti értéke a magas vagy alacsony; mindig legyen a késés, néhány perc alatt.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Vannak-e az Azure-webalkalmazást és az Application Insights közötti adatátvitel?

* Ha az Azure-webalkalmazást egy adatközpontban üzemeltetett ahol az Application Insights-végpont, nem számítunk fel díjat. 
* Ha nincs végpont szerepel a gazdagép adatközpontban, akkor számítunk fel az alkalmazás telemetriai adatainak [díjak kimenő Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Nem ez függ az Application Insights-erőforrást üzemeltető. Ez csak a végpontok a terjesztési függ.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Küldhetek telemetria az Application Insights portálra?

Azt javasoljuk, hogy az SDK-kat használja, és használja a [SDK API](../../azure-monitor/app/api-custom-events-metrics.md). Nincsenek az SDK a különböző változatának [platformok](../../azure-monitor/app/platforms.md). Ezek az SDK-k kezelésére, pufferelés, tömörítés, szabályozás, az újrapróbálkozások és így tovább. Azonban a [Adatbetöltési séma](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) és [végpont protokoll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) nyilvánosak legyenek.

## <a name="can-i-monitor-an-intranet-web-server"></a>Képes megfigyelni az intranetes webkiszolgáló?

Igen, de a tűzfalkivételek vagy a proxy átirányítások szolgáltatásaink forgalom engedélyezésére kell.
- QuickPulse `rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Tekintse át a listában, szolgáltatások és IP-címek [Itt](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Tűzfal-kivétel

A webkiszolgáló, hogy küldjön telemetriát a végpontok engedélyezése. 

### <a name="proxy-redirect"></a>Proxy-átirányítás

Forgalom irányítása a kiszolgálóról, a konfiguráció végpontok felülírásával intraneten átjáró.
Ha a "Végpont" tulajdonságok nem szerepelnek a config, ezeket az osztályokat az alább látható az ApplicationInsights.config példában alapértelmezett értékeket fogja használni. 

Az átjáró kell irányítani a végpont základní adresa. Az alapértelmezett értékeket cserélje le a konfigurációs `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Az alapértelmezett végpontok ApplicationInsights.config példa:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector"/>
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_Megjegyzés ApplicationIdProvider érhető el a v2.6.0 indítása_


 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Rendelkezésre állási webes tesztek futtathatok egy intranetes kiszolgálóra?

A [webes teszteket](../../azure-monitor/app/monitor-web-app-availability.md) elosztott világszerte megtalálható jelenléti pontok futhatnak. Nincsenek két megoldást:

* Tűzfal-ajtó – a kiszolgálóhoz érkező kérések engedélyezése [a hosszú és módosítható azon ügynökök listája, webes teszt](../../azure-monitor/app/ip-addresses.md).
* Rendszeresen kéréseket küldeni a kiszolgálót az intraneten belüli a saját kódot írni. Futtatható a Visual Studio webtesztjeivel erre a célra. A tesztelő sikerült elküldeni az eredményeket az Application Insights TrackAvailability() API használatával.

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Mennyi ideig tart a telemetriai adatokat gyűjteni?

A legtöbb Application Insights-adatok egy késését 5 percen belül van. Bizonyos adatok lassabban; általában nagyobb naplófájlokat. További információkért lásd: a [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>További válaszok
* [Application Insights-fórum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: ../../azure-monitor/app/data-retention-privacy.md
[platforms]: ../../azure-monitor/app/platforms.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[windows]: app-insights-windows-get-started.md
