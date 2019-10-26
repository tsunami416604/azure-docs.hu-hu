---
title: Azure Application Insights GYIK | Microsoft Docs
description: Gyakori kérdések a Application Insightsról.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.openlocfilehash: 55a096cd4971664e55bb2cfd17f9f8927d7c32f5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899522"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: gyakori kérdések

## <a name="configuration-problems"></a>Konfigurációs problémák
*Nem sikerül beállítani a következőket:*

* [.NET-alkalmazás](asp-net-troubleshoot-no-data.md)
* [Már futó alkalmazás figyelése](monitor-performance-live-website-now.md#troubleshoot)
* [Azure-diagnosztika](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Java webalkalmazások](java-troubleshoot.md)

*Nem kapok adatok a kiszolgálóról*

* [Tűzfal-kivételek beállítása](ip-addresses.md)
* [ASP.NET-kiszolgáló beállítása](monitor-performance-live-website-now.md)
* [Java-kiszolgáló beállítása](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Használhatom a Application Insights...?

* [Webalkalmazások egy Azure-beli virtuális gépen vagy Azure-beli virtuálisgép-méretezési csoporton belüli IIS-kiszolgálón](azure-vm-vmss-apps.md)
* [Webalkalmazások egy helyi IIS-kiszolgálón vagy egy virtuális gépen](asp-net.md)
* [Java-webalkalmazások](java-get-started.md)
* [Node.js-alkalmazások](nodejs.md)
* [Webalkalmazások az Azure-ban](azure-web-apps.md)
* [Cloud Services az Azure-ban](cloudservices.md)
* [A Docker-ben futó alkalmazás-kiszolgálók](docker.md)
* [Egyoldalas webalkalmazások](javascript.md)
* [SharePoint](sharepoint.md)
* [Windows asztali alkalmazás](windows-desktop.md)
* [Más platformok](platforms.md)

## <a name="is-it-free"></a>Ingyenes?

Igen, kísérleti felhasználásra. Az alapszintű díjszabás keretében az alkalmazás minden hónapban díjmentesen küldhet bizonyos adatmennyiséget. Az ingyenes juttatás elég nagy a fejlesztéshez, és egy alkalmazás közzététele kis számú felhasználó számára. Beállíthat egy korlátot, amely megakadályozza a megadott mennyiségű adatok feldolgozását.

A GB-nál nagyobb mennyiségű telemetria számítunk fel. Néhány tippet adunk a [díjak korlátozására](pricing.md).

A nagyvállalati csomag minden nap esetében díjat számít fel, amelyet minden egyes webkiszolgáló-csomópont telemetria küld. Megfelelő, ha a folyamatos exportálást nagy léptékben kívánja használni.

[Olvassa el a díjszabási tervet](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-does-it-cost"></a>Mennyibe kerül?

* Nyissa meg a **használati és becsült költségek lapot** egy Application Insights erőforrásban. Van egy diagram a közelmúltbeli használatról. Ha kívánja, beállíthatja az adatmennyiség korlátját.
* Nyissa meg az [Azure számlázási](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) paneljét, és tekintse meg a számlákat az összes erőforráson.

## <a name="q14"></a>Mit Application Insights módosítani a projektben?
A részletek a projekt típusától függenek. Webalkalmazások esetén:

* Hozzáadja ezeket a fájlokat a projekthez:

  * ApplicationInsights. config.
  * ai. js
* A következő NuGet-csomagokat telepíti:

  * *Application INSIGHTS API* – a Core API
  * *Application INSIGHTS API webalkalmazásokhoz* – telemetria küldésére szolgál a kiszolgálóról
  * *Application INSIGHTS API JavaScript-alkalmazásokhoz* – telemetria küldésére szolgál az ügyféltől

    A csomagok közé tartoznak a következő szerelvények:
  * Microsoft. ApplicationInsights
  * Microsoft. ApplicationInsights. platform
* Elemek beszúrása a következőkbe:

  * Web.config
  * packages. config
* (Csak új projektek – ha [Application Insightst ad hozzá egy meglévő projekthez][start], ezt manuálisan kell elvégeznie.) Kódrészleteket szúr be az ügyfél és a kiszolgáló kódjába az Application Insights erőforrás-AZONOSÍTÓval való inicializáláshoz. Egy MVC-alkalmazásban például a kód bekerül a mesterlap nézeteibe/Shared/_Layout. cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hogyan frissíteni a régebbi SDK-verziókról?
Tekintse meg az SDK [kibocsátási megjegyzéseit](release-notes.md) , amelyek megfelelnek az adott alkalmazás típusának.

## <a name="update"></a>Hogyan változtathatom meg, hogy a projekt melyik Azure-erőforráshoz küld adatokat?
A Megoldáskezelő kattintson a jobb gombbal az `ApplicationInsights.config` elemre, majd válassza a **frissítés Application Insights**lehetőséget. Az Azure-ban egy meglévő vagy új erőforráshoz is elküldheti az adott adatforrást. A frissítési varázsló megváltoztatja a kialakítási kulcsot a ApplicationInsights. config fájlban, amely meghatározza, hogy a kiszolgáló SDK hogyan küldje el az adatokat. Ha kijelöli az "összes frissítése" lehetőséget, akkor az azt is megváltoztatja, hogy a kulcs hol jelenik meg a weblapok között.

## <a name="what-is-status-monitor"></a>Mi az Állapotfigyelő?

Egy asztali alkalmazás, amelyet az IIS-webkiszolgálóban használhat a Application Insights webalkalmazásokban való konfigurálásához. Nem gyűjt telemetria: leállíthatja, ha nem konfigurál egy alkalmazást. 

[További információk](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Milyen telemetria gyűjtenek Application Insights?

A Server Web appsből:

* HTTP-kérések
* [Függőségek](asp-net-dependencies.md). Hívások: SQL-adatbázisok; HTTP-hívások külső szolgáltatásokhoz; Azure Cosmos DB, tábla, blob Storage és üzenetsor. 
* [Kivételek](asp-net-exceptions.md) és verem-Nyomkövetések.
* [Teljesítményszámlálók](performance-counters.md) – ha [Állapotmonitort](monitor-performance-live-website-now.md)használ, a [app Services Azure-figyelést](azure-web-apps.md), a [virtuális gép vagy a virtuálisgép-méretezési csoport azure-figyelését](azure-vm-vmss-apps.md), vagy a [Application Insights gyűjtött író](java-collectd.md).
* [Egyéni események és mérőszámok](api-custom-events-metrics.md) .
* [Nyomkövetési naplók](asp-net-trace-logs.md) , ha a megfelelő gyűjtőt konfigurálja.

Az [ügyfél weblapjairól](javascript.md):

* [Oldalmegtekintések száma](usage-overview.md)
* [Ajax-hívások](asp-net-dependencies.md) Futó parancsfájlból végrehajtott kérelmek.
* Az oldal nézet betöltési adatkészlete
* Felhasználók és munkamenetek száma
* [Hitelesített felhasználói azonosítók](api-custom-events-metrics.md#authenticated-users)

Más forrásokból, ha konfigurálja őket:

* [Azure-diagnosztika](../platform/diagnostics-extension-to-application-insights.md)
* [Importálás az Analytics szolgáltatásba](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kiszűrhetők vagy módosíthatok néhány telemetria?

Igen, a kiszolgálón írhat:

* A telemetria a kiválasztott telemetria-elemekhez tartozó tulajdonságokat szűrheti vagy adja hozzá az alkalmazásból való elküldése előtt.
* A telemetria inicializáló tulajdonságot adhat hozzá a telemetria összes eleméhez.

További információ a [ASP.net](api-filtering-sampling.md) és a [Java](java-filter-telemetry.md)-ról.

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hogyan számítják ki a város, az ország/régió és az egyéb földrajzi hely adatértékét?

A [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)használatával megkeresjük a webes ügyfél IP-címét (IPv4 vagy IPv6).

* Böngésző telemetria: összegyűjtjük a küldő IP-címét.
* Kiszolgáló telemetria: a Application Insights modul gyűjti az ügyfél IP-címét. Ha `X-Forwarded-For` van beállítva, a rendszer nem gyűjti.
* Ha többet szeretne megtudni arról, hogy az IP-cím és a térinformatikai adatok hogyan kerülnek gyűjtésre Application Insights tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


A `ClientIpHeaderTelemetryInitializer` úgy is beállíthatja, hogy az IP-címet egy másik fejlécből hajtsa végre. Egyes rendszerekben például egy proxy, terheléselosztó vagy CDN áthelyezi a `X-Originating-IP`ba. [További információk](https://apmtips.com/blog/2016/07/05/client-ip-address/).

A [Power bi](export-power-bi.md ) segítségével megjelenítheti a kérések telemetria egy térképen.


## <a name="data"></a>Mennyi ideig tartanak a portálon tárolt adat? Biztonságos?
Vessen egy pillantást az [adatok megőrzésére és az adatvédelemre][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>A személyes adatküldés a telemetria?

Ez akkor lehetséges, ha a kód ilyen adatokat küld. Akkor is előfordulhat, ha a stack-nyomkövetésben szereplő változók személyes adatba tartoznak. A fejlesztési csapatnak kockázatértékelést kell végeznie, hogy a személyes adatai megfelelően legyenek kezelve. [További információ az adatmegőrzésről és az adatvédelemről](data-retention-privacy.md).

Az ügyfél-webcímek **minden** oktettje mindig 0-ra van állítva a földrajzi hely attribútumainak megvizsgálása után.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>A saját kialakítási kulcs látható a saját weblap forrásában. 

* Ez a figyelési megoldások általános gyakorlata.
* Nem használható az adatai ellopására.
* Felhasználhatja az adatait, vagy riasztásokat indíthat.
* Nem tudtuk meghallgatni, hogy minden ügyfélnek ilyen problémája volt.

A következőket teheti:

* Az ügyfél-és a kiszolgálói adatforrásokhoz két különálló rendszerkulcs (külön Application Insights erőforrás) használata szükséges. vagy
* Írjon be egy proxyt, amely a kiszolgálón fut, és hogy a webes ügyfél az adott proxyn keresztül küldje el az adatait.

## <a name="post"></a>Hogyan lásd: az adatposta a diagnosztikai keresésben?
A rendszer nem naplózza az adatpostát, de TrackTrace hívást is használhat: az üzenet paraméterében elhelyezheti az adatbevitelt. Ez hosszabb mérethatárt, mint a karakterlánc-tulajdonságok korlátai, de nem szűrheti.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Egy vagy több Application Insights-erőforrást használok?

Egyetlen erőforrás használata egyetlen üzleti rendszeren lévő összes összetevőhöz vagy szerepkörhöz. Külön erőforrásokat használhat a fejlesztési, tesztelési és kiadási verziókhoz, valamint a független alkalmazásokhoz.

* [Itt tekintheti meg a vitát](separate-resources.md)
* [Példa – felhőalapú szolgáltatás munkavégző és webes szerepkörökkel](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hogyan dinamikusan módosítja a kialakítási kulcsot?

* [Vitafórum](separate-resources.md)
* [Példa – felhőalapú szolgáltatás munkavégző és webes szerepkörökkel](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Mik a felhasználók és a munkamenetek száma?

* A JavaScript SDK beállítja a felhasználói cookie-t a webes ügyfélen, a visszatérő felhasználók azonosítására, valamint egy munkamenet-cookie-t a tevékenységek csoportosítására.
* Ha nincs ügyféloldali parancsfájl, beállíthatja [a cookie-kat a kiszolgálón](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Ha egy valós felhasználó különböző böngészőkben használja a webhelyét, vagy ha privát vagy inkognitóbani böngészést vagy különböző gépeket használ, a rendszer egynél többször veszi fel őket.
* A bejelentkezett felhasználók számítógépek és böngészők közötti azonosításához vegyen fel egy hívást a [setAuthenticatedUserContext ()](api-custom-events-metrics.md#authenticated-users)szolgáltatásba.

## <a name="q17"></a>Engedélyeztem mindent Application Insights?
| Mit kell látnia? | Útmutató | Miért szeretné |
| --- | --- | --- |
| Rendelkezésre állási diagramok |[Webes tesztek](monitor-web-app-availability.md) |Ismerje meg a webalkalmazását |
| Server app Perf: válaszidő,... |[Application Insights hozzáadása a projekthez](asp-net.md) vagy [AI-Állapotmonitor telepítése a kiszolgálón](monitor-performance-live-website-now.md) (vagy saját kód írása a [függőségek nyomon követéséhez](api-custom-events-metrics.md#trackdependency)) |A Teljesítményfigyelő hibáinak észlelése |
| Függőségi telemetria |[AI-Állapotmonitor telepítése a kiszolgálón](monitor-performance-live-website-now.md) |Az adatbázisokkal vagy más külső összetevőkkel kapcsolatos problémák diagnosztizálása |
| Verem nyomkövetésének lekérése kivételekről |[TrackException-hívások beszúrása a kódban](asp-net-exceptions.md) (de néhányat automatikusan kell jelenteni) |Kivételek észlelése és diagnosztizálása |
| Keresési naplók nyomkövetése |[Naplózási adapter hozzáadása](asp-net-trace-logs.md) |Kivételek diagnosztizálása, Perf-problémák |
| Az ügyfél használatának alapjai: oldalletöltések, munkamenetek,... |[JavaScript-inicializálás a weblapokon](javascript.md) |Használatelemzés |
| Ügyfél egyéni metrikái |[Hívások követése a weblapokon](api-custom-events-metrics.md) |A felhasználói élmény fejlesztése |
| Kiszolgáló egyéni metrikái |[Hívások követése a kiszolgálón](api-custom-events-metrics.md) |Üzleti intelligencia |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Miért nem egyenlő a keresési és a metrikák diagramjai?

A [mintavétel](sampling.md) csökkenti az alkalmazásból a portálra ténylegesen küldött telemetria elemek (kérelmek, egyéni események stb.) számát. A keresés területen láthatja a ténylegesen fogadott elemek számát. Az események számát megjelenítő mérőszám-diagramoknál megtekintheti a bekövetkezett eredeti események számát. 

Minden továbbított tétel egy `itemCount` tulajdonságot jelenít meg, amely azt mutatja, hogy hány eredeti eseményt képvisel az adott tétel. A mintavétel működés közbeni megfigyeléséhez futtassa ezt a lekérdezést az Analyticsben:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Application Insights konfigurálása

A PowerShell-szkripteket az Azure erőforrás-figyelő használatával is [megírhatja](powershell.md) :

* Application Insights erőforrások létrehozása és frissítése.
* Állítsa be a díjszabási tervet.
* Szerezze be a kialakítási kulcsot.
* Metrikai riasztás hozzáadása.
* Adja meg a rendelkezésre állási tesztet.

Nem állítható be metrikai Explorer-jelentés, vagy nem állítható be folyamatos exportálás.

### <a name="querying-the-telemetry"></a>A telemetria lekérdezése

[Analytics](analytics.md) -lekérdezések futtatásához használja a [REST API](https://dev.applicationinsights.io/) .

## <a name="how-can-i-set-an-alert-on-an-event"></a>Hogyan állíthatok be riasztást eseményre?

Az Azure-riasztások csak mérőszámokon alapulnak. Hozzon létre egy egyéni mérőszámot, amely egy érték küszöbértékét adja meg, amikor az esemény bekövetkezik. Ezután állítson be egy riasztást a metrikán. Vegye figyelembe, hogy: értesítést kap, ha a metrika mindkét irányban átlépi a küszöbértéket; nem kap értesítést az első átlépésig, függetlenül attól, hogy a kezdeti érték magas vagy alacsony; a késések mindig néhány percet vesznek igénybe.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Vannak adatátviteli díjak az Azure-webalkalmazások és a Application Insights között?

* Ha az Azure-webalkalmazás egy olyan adatközpontban található, amelyben Application Insights gyűjtemény végpontja található, díjmentesen használható. 
* Ha nem található gyűjteményi végpont a gazdagép adatközpontjában, akkor az alkalmazás telemetria az Azure-beli [kimenő díjakat](https://azure.microsoft.com/pricing/details/bandwidth/)is felmerül.

Ez nem függ attól, hogy hol található a Application Insights-erőforrás. Csak a végpontok eloszlása függ.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Küldhetek telemetria az Application Insights portálra?

Javasoljuk, hogy használja az SDK-kat, és használja az [SDK API](api-custom-events-metrics.md)-t. Különböző [platformokon](platforms.md)léteznek az SDK különféle változatai. Ezek az SDK-k pufferelést, tömörítést, szabályozást, újrapróbálkozást és így tovább kezelik. A betöltési [séma](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) és a [végpont protokoll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) azonban nyilvános.

## <a name="can-i-monitor-an-intranet-web-server"></a>Nyomon követhető az intranetes webkiszolgáló?

Igen, de engedélyeznie kell a szolgáltatásoknak a tűzfal-kivételek vagy a proxy-átirányítások által nyújtott forgalmat.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Tekintse át a szolgáltatások és IP-címek teljes listáját [itt](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Tűzfal-kivétel

Lehetővé teszi, hogy a webkiszolgáló telemetria küldjön a végpontoknak. 

### <a name="gateway-redirect"></a>Átjáró átirányítása

Átirányíthatja a forgalmat a kiszolgálóról az intranetes átjáróra a konfigurációban található végpontok felülírásával.
Ha ezek a "végpont" tulajdonságok nem szerepelnek a konfigurációban, ezek az osztályok az alapértelmezett értékeket fogják használni az alábbi példában látható ApplicationInsights. config fájlban. 

Az átjárónak a végpont alapcímeihez kell irányítani a forgalmat. A konfigurációban cserélje le az alapértelmezett értékeket `http://<your.gateway.address>/<relative path>`ra.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Példa ApplicationInsights. config alapértelmezett végpontokkal:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
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

_Megjegyzés: a ApplicationIdProvider a v 2.6.0-től kezdődően érhető el_

### <a name="proxy-passthrough"></a>Proxy átengedése

A proxy továbbítása a gépi vagy az alkalmazás szintű proxy konfigurálásával érhető el.
További információ: a [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)-ra vonatkozó DotNet-cikk.
 
 Példa web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Futtathatok rendelkezésre állási webes teszteket egy intranetes kiszolgálón?

A [webes tesztek](monitor-web-app-availability.md) a világ minden részén elosztott, jelenléti pontokon futnak. Két megoldás létezik:

* Tűzfal ajtaja – engedélyezze a kérelmeket [a kiszolgálónak a webes tesztek hosszú és módosítható listájáról](ip-addresses.md).
* Saját kód megírásával rendszeres kérelmeket küldhet a kiszolgálónak az intraneten belülről. A Visual Studio webes tesztek erre a célra is futtathatók. A Tester a TrackAvailability () API használatával küldheti el az eredményeket Application Insights.

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Mennyi időt vesz igénybe a telemetria gyűjtése?

A legtöbb Application Insights adat 5 perces késéssel rendelkezik. Egyes adatsorok hosszabb időt vehetnek igénybe; általában nagyobb naplófájlok. További információ: [Application INSIGHTS SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>További válaszok
* [Application Insights fórum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
