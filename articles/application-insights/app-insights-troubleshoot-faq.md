---
title: "Az Azure Application insights szolgáltatással kapcsolatos gyakori kérdések |} Microsoft Docs"
description: "Az Application Insights vonatkozó gyakran ismételt kérdések."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: 9f6cf019d681ce6e844481ca58de1ff472b3d32c
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="application-insights-frequently-asked-questions"></a>Az Application Insights: Gyakran ismételt kérdések

## <a name="configuration-problems"></a>Konfigurációs problémák
*Hiba történt a beállítás lépett a:*

* [.NET-alkalmazás](app-insights-asp-net-troubleshoot-no-data.md)
* [Egy már futó alkalmazás figyelése](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Az Azure diagnostics](app-insights-azure-diagnostics.md)
* [Java webalkalmazások](app-insights-java-troubleshoot.md)

*Adatot nem jelenik meg a kiszolgálóról*

* [Tűzfalkivételek beállítása](app-insights-ip-addresses.md)
* [ASP.NET-kiszolgáló beállítása](app-insights-monitor-performance-live-website-now.md)
* [A Java-kiszolgáló beállítása](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Használható az Application Insights...?

* [Webalkalmazások IIS kiszolgálón – a helyi vagy egy virtuális Gépre](app-insights-asp-net.md)
* [Java-webalkalmazások](app-insights-java-get-started.md)
* [Node.js-alkalmazások](app-insights-nodejs.md)
* [Az Azure Web Apps alkalmazások](app-insights-azure-web-apps.md)
* [Azure cloud Services csomag](app-insights-cloudservices.md)
* [A Docker alkalmazást futtató](app-insights-docker.md)
* [Egyoldalas webalkalmazások](app-insights-javascript.md)
* [SharePoint](app-insights-sharepoint.md)
* [Windows asztali alkalmazás](app-insights-windows-desktop.md)
* [Más platformok](app-insights-platforms.md)

## <a name="is-it-free"></a>Az ingyenes?

Igen, kísérleti használatra. Az alapvető tarifacsomagot az alkalmazás egy bizonyos támogatás az adatok minden hónapban díjmentesen is küld. A szabad támogatásra elég nagy borítóján fejlesztési, és a felhasználók néhány alkalmazás közzététele. A tengelysapka megakadályozhatja, hogy több, mint a megadott adatmennyiséget a feldolgozás alatt állíthatja be.

Nagyobb mértékű telemetriai adatokat a GB-tal van szó. Tippek útmutatást nyújtunk [a költségek korlátozására](app-insights-pricing.md).

A vállalati terv a azt eredményezi, amelyet minden webkiszolgáló csomópontját telemetriai adatokat küld naponta díjat azok háromszorosa. Ez akkor megfelelő, ha szeretné használni a folyamatos exportálás nagy méretű.

[Olvassa el a tarifacsomag](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Mennyire van azt költségszámítás?

* Nyissa meg a **szolgáltatások + díjszabás** lap az Application Insights-erőforrást. Legutóbbi használat diagram van. Egy adatok kötet kap, ha azt szeretné, állíthatja be.
* Nyissa meg a [panel Azure számlázási](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) megtekintéséhez a váltók közötti összes erőforrást.

## <a name="q14"></a>Mi az Application Insights módosítja a projektben?
A részletek a projekt típusától függ. Egy webes alkalmazáshoz:

* Ezek a fájlok hozzáadása a projekthez:

  * ApplicationInsights.config.
  * AI.js
* A NuGet-csomagok telepíti:

  * *Application Insights API* – az alapszintű API
  * *Application Insights API-webalkalmazások számára történő* - használt telemetriai adatokat küldhet a kiszolgálóról
  * *Application Insights API-t a JavaScript alkalmazások* - használt telemetriai adatokat küldhet az ügyfélről

    A csomagok ezek szerelvényeket tartalmazza:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Beszúrja azokat:

  * Web.config
  * Packages.config
* (Új projekt csak – ha Ön [az Application Insights hozzáadása egy meglévő projektjébe][start], ehhez manuálisan kell.) Kódtöredékek szúr be az ügyfél és kiszolgáló kódot inicializálása őket az Application Insights erőforrás-azonosító. Például egy MVC alkalmazás kód bekerülnek a mesterlap Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hogyan lehet frissíteni a régebbi SDK?
Tekintse meg a [kibocsátási megjegyzéseket](app-insights-release-notes.md) az alkalmazás típusának legmegfelelőbb SDK-ban.

## <a name="update"></a>Hogyan lehet módosítani a projekt adatokat küld az Azure erőforrás?
A Megoldáskezelőben kattintson a jobb gombbal `ApplicationInsights.config` válassza **frissítés az Application Insights**. Az adatokat küldhet egy meglévő vagy új erőforrást az Azure-ban. A frissítési varázsló módosítja a instrumentation kulcsot applicationinsights.config, amely megadja, ahol a kiszolgáló SDK elküldi az adatokat. Ha törli a "Frissítés all", a kulcs, ahol megjelenik a weblapok is megváltozik.

## <a name="what-is-status-monitor"></a>Mi az Állapotfigyelő?

Egy asztali alkalmazás használható az IIS-webkiszolgálón a webalkalmazásokban konfigurálhatja az Application Insights segítségével. Azt nem gyűjthet: le lehet állítani, ha nem kívánja használni az alkalmazást. 

[További információk](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Milyen telemetriai adatokat gyűjti az Application Insights?

A kiszolgáló web apps:

* HTTP-kérések
* [Függőségek](app-insights-asp-net-dependencies.md). Hívások: SQL-adatbázisok; HTTP hívások külső szolgáltatások; Az Azure Cosmos DB, tábla, a blob storage és várólista. 
* [Kivételek](app-insights-asp-net-exceptions.md) és kivételeseményekhez megadhat veremkiíratásokat.
* [Teljesítményszámlálók](app-insights-performance-counters.md) - használatakor [állapotfigyelő](app-insights-monitor-performance-live-website-now.md), [Azure figyelési](app-insights-azure-web-apps.md) vagy a [Application Insights collectd író](app-insights-java-collectd.md).
* [Egyéni események és metrikák](app-insights-api-custom-events-metrics.md) , hogy kódaláírással.
* [Nyomkövetési naplók](app-insights-asp-net-trace-logs.md) Ha a megfelelő gyűjtőt konfigurálja.

A [ügyfél weblapok](app-insights-javascript.md):

* [Lapmegtekintés száma](app-insights-web-track-usage.md)
* [AJAX-hívások](app-insights-asp-net-dependencies.md) futó parancsfájl kérések száma.
* Lapbetöltési adatainak megtekintése
* Felhasználó és a munkamenet számát
* [Hitelesített felhasználói azonosítók](app-insights-api-custom-events-metrics.md#authenticated-users)

Más forrásokból, ha konfigurálja őket:

* [Az Azure diagnostics](app-insights-azure-diagnostics.md)
* [Docker-tároló](app-insights-docker.md)
* [Importálhatja a táblákat Analytics](app-insights-analytics-import.md)
* [OMS (Naplóelemzési)](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kiszűrhetők vagy módosíthatja az egyes telemetriai?

Igen, a kiszolgáló lehet beírni:

* Telemetria processzor való vagy tulajdonságok hozzáadása kijelölt telemetriai elemek megkövetelése azok elküldéséhez az alkalmazásból.
* Telemetria inicializáló tulajdonságok hozzáadása szereplő összes telemetriai adat.

A további [ASP.NET](app-insights-api-filtering-sampling.md) vagy [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Hogyan város, az ország és az egyéb földrajzi hely adatok kiszámítása?

Azt az IP-cím (IPv4 vagy IPv6) a webes ügyfél segítségével kereshet [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Böngésző telemetriai: az Öntől gyűjtött, a feladó IP-címet.
* Kiszolgáló telemetriai: az Application Insights modul összegyűjti az ügyfél IP-címét. Nem gyűjtenek, ha `X-Forwarded-For` van beállítva.

Konfigurálhatja a `ClientIpHeaderTelemetryInitializer` az IP-cím egy másik fejlécéből érvénybe. Az egyes rendszerek, például áthelyezés olyan proxy, és töltse be a terheléselosztót, vagy hogy a CDN `X-Originating-IP`. [További információk](http://apmtips.com/blog/2016/07/05/client-ip-address/).

Is [használja a Power BI](app-insights-export-power-bi.md) a kérelem telemetriai adatok megjelenítéséhez a térképen.


## <a name="data"></a>Mennyi ideig adatok őrződnek meg a portálon? Az biztonságos?
Vessen egy pillantást [az adatmegőrzés és az adatvédelmi][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>Előfordulhat, hogy személyes azonosításra alkalmas adatokat (PII) elküldeni a telemetriai adatok?

Ez akkor lehetséges, ha a kód elküldi ezeket az adatokat. Akkor is előfordulhat, ha változók híváslánc megjelenik a személyhez köthető adatokat tartalmazza. A fejlesztői csapat kockázatértékelések annak érdekében, hogy a rendszer megfelelően kezelje személyhez köthető adatokat kell végezniük. [További információ az adatmegőrzés és adatvédelmi tudnivalók](app-insights-data-retention-privacy.md).

Az ügyfél webcímet utolsó oktettje értéke mindig 0 bevitel után a portál által.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>A iKey látható lesz a weblap forrása. 

* Ez a figyelési megoldásoknak a gyakori eljárása.
* Az adatok ellopására nem használható.
* Az adatok vagy az eseményindító riasztások döntés felhasználhatók.
* Igazolnia kell nem hallgassa meg, hogy minden ügyfél az ilyen problémák volt.

Sikerült:

* Két külön iKeys (külön Application Insights-erőforrások), használja az ügyfél és kiszolgáló adatok. Vagy
* Írni a proxy a kiszolgálón fut, és a webes ügyféllel, hogy a proxyn keresztül történő adatküldés rendelkezik.

## <a name="post"></a>Hogyan tekinthető meg a diagnosztikai keresési POST-adatokat?
Automatikusan azt ne naplózza a POST-adatokat, de használhat TrackTrace hívás: helyezze az adatokat az üzenet paraméterben. Ez méretkorlátja hosszabb, mint kapcsolatikarakterlánc-tulajdonságokat, használati korlátait, ha nem lehet szűrni rajta.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Egy vagy több Application Insights-erőforrások érdemes használni?

Egyetlen használata az összetevők vagy a szerepkörök egyetlen üzleti rendszereken. Fejlesztői, tesztelési és verzióját, és független alkalmazások használjon külön erőforrások.

* [Tanulmányozza a Itt](app-insights-separate-resources.md)
* [Példa - munkavégző és a webes szerepkör felhőszolgáltatás](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hogyan dinamikusan módosítható a rendszerállapot-kulcsot?

* [Itt az ismertető](app-insights-separate-resources.md)
* [Példa - munkavégző és a webes szerepkör felhőszolgáltatás](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Mik azok a felhasználói és a munkamenet száma?

* A JavaScript SDK csoport tevékenységek egy felhasználói cookie-k elhelyezését a webes ügyféllel, adatszolgáltató felhasználók azonosításához és egy munkamenetcookie-t állítja be.
* Ha a nem az ügyféloldali parancsprogram [cookie-k beállítása a kiszolgálón](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Ha egy felhasználó a webhely különböző böngészők, vagy keresse meg a-private vagy incognito, vagy másik gép, majd többször beleszámítanak.
* A bejelentkezett felhasználó azonosításához gép és a böngészők között, adjon hozzá egy [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a>Engedélyezte a I mindent az Application Insights?
| Mit kell megjelennie | Az beszerzése | Miért érdemes |
| --- | --- | --- |
| Rendelkezésre állási diagramok |[Webteszt](app-insights-monitor-web-app-availability.md) |Tudja, hogy a webes alkalmazás mentése |
| Server app telj: válaszidejét,... |[Az Application Insights hozzáadása a projekthez](app-insights-asp-net.md) vagy [AI Állapotmonitor telepítése a kiszolgálón](app-insights-monitor-performance-live-website-now.md) (vagy a saját kód írása [függőségek nyomon](app-insights-api-custom-events-metrics.md#trackdependency)) |A Teljesítményfigyelő problémák észlelése |
| – Függőségi telemetria |[AI Állapotmonitor telepítése a kiszolgálón](app-insights-monitor-performance-live-website-now.md) |Az adatbázisok vagy más külső összetevők eseményadatokat |
| Kivételek híváslánc megjelenik beszerzése |[Helyezze be a kódot TrackException hívások](app-insights-asp-net-exceptions.md) (de néhány automatikusan jelenti) |Észlelheti és diagnosztizálhatja a kivételek |
| Keresési naplókivonatokat |[Naplózási adapter hozzáadása](app-insights-asp-net-trace-logs.md) |Ez alól kivétel a Teljesítményfigyelő problémák diagnosztizálása |
| Ügyfél használatának alapjai: Lapmegtekintések, munkamenetek,... |[JavaScript inicializáló a weblapok](app-insights-javascript.md) |Használatelemzés |
| Egyéni metrikák ügyfél |[Nyomkövetési meghívja a weblapok](app-insights-api-custom-events-metrics.md) |Felhasználói élmény javítása érdekében |
| Kiszolgáló egyéni metrikák |[A kiszolgáló nyomkövetési hívások](app-insights-api-custom-events-metrics.md) |Üzleti intelligencia |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Miért van egyenlőtlen a számát, a Keresés és a metrikák?

[A mintavételi](app-insights-sampling.md) csökkenti a telemetriai elemek száma (kéréseket, egyéni események és így tovább), amelyek ténylegesen a portál az alkalmazásból küldött. A keresési lásd: ténylegesen kapott elemek száma. Megjeleníti az események számát metrika diagramokat látható történt eredeti események száma. 

Minden elem által továbbított végzi egy `itemCount` bemutatja, hogy hány eredeti események, hogy az elem tulajdonság jelöli. Figyelje meg mintavételi műveletben, ez a lekérdezés Analytics futtathatja:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatizálás

### <a name="configuring-application-insights"></a>Az Application Insights konfigurálása

Is [PowerShell-parancsfájlokat írhat](app-insights-powershell.md) az Azure erőforrás-figyelő segítségével:

* Hozzon létre, és frissítse az Application Insights-erőforrások.
* Állítsa a tarifacsomagot.
* A rendszerállapot-kulcs beszerzése.
* A metrika-riasztások hozzáadása.
* Elérhetőségi teszt hozzáadása.

Nem állítja be a metrika Explorer jelentést és a folyamatos exportálás beállítása.

### <a name="querying-the-telemetry"></a>A telemetriai adatok lekérdezése

Használja a [REST API](https://dev.applicationinsights.io/) futtatásához [Analytics](app-insights-analytics.md) lekérdezések.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Hogyan állíthatja be az esemény a riasztást?

Az Azure-riasztásokat csak metrikák esetén. Egy érték küszöbérték keverve használ az esemény akkor következik be, amikor egyéni metrika létrehozása. Majd a mértéket a riasztás beállításához. Vegye figyelembe a következőket: értesítést fog kapni, amikor a metrika keverve használ a küszöbérték mindkét irányban; nem jelenik meg egy értesítés, amíg az első metsző, függetlenül attól, hogy a kezdeti értéke magas vagy alacsony; mindig legyen a késés néhány percig.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Vannak-e adatok adatátviteli díjakkal között egy Azure web app és az Application Insights?

* Ha az Azure-webalkalmazásban helyezkedik-e egy adott adatközpont Application Insights-gyűjtemény végpont esetén használata díjmentes. 
* Ha nincs figyelésgyűjtési végpont az állomás adatközpont van, akkor az alkalmazás telemetriai tájékozódnia [díjak kimenő Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Ez nem függő amelyen az Application Insights-erőforrás található. A végpontok terjesztési csak függ.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Is telemetriai adatokat is küldhet az Application Insights portálon?

Azt javasoljuk, hogy az SDK-k használata, és használja a [SDK API](app-insights-api-custom-events-metrics.md). Nincsenek az SDK a különböző változatai [platformok](app-insights-platforms.md). Ezek az SDK-k kezelni pufferelés, tömörítés, szabályozás, az újrapróbálkozások, és így tovább. Azonban a [adatfeldolgozást séma](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) és [végpont protokoll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) nyilvános.

## <a name="can-i-monitor-an-intranet-web-server"></a>Képes figyelni az intranet webkiszolgáló?

Az alábbiakban a két módszer:

### <a name="firewall-door"></a>Tűzfal ajtó

Lehetővé teszi a webkiszolgáló, telemetriai adatokat küldeni a végpontok https://dc.services.visualstudio.com:443 és https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Proxy

Útvonal-forgalom a kiszolgáló egy átjárót az intraneten, úgy, hogy ez az ApplicationInsights.config:

```XML
<TelemetryChannel>
    <EndpointAddress>your gateway endpoint</EndpointAddress>
</TelemetryChannel>
```

Az átjáró kell továbbítani a forgalmat https://dc.services.visualstudio.com:443/v2/nyomon követése

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Futtatható-e rendelkezésre állási webes tesztjeinek használatát egy intranetes kiszolgálóra?

A [webalkalmazás-tesztek](app-insights-monitor-web-app-availability.md) kapcsolódási pontok, a világ minden táján elosztott futtatnak. Nincsenek két megoldások:

* Tűzfal-ajtó - kérelmek engedélyezni szeretné a kiszolgálót a [webes tesztelési ügynökeit a hosszú és módosítható lista](app-insights-ip-addresses.md).
* A saját kérések küldeni a kiszolgálót az intraneten belüli a kód írása Futtatható a Visual Studio webtesztjeivel erre a célra. A tesztelő küldhet az eredményeket az Application Insights TrackAvailability() API használatával.

## <a name="more-answers"></a>Adott további válaszokért
* [Application Insights fórum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
