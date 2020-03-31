---
title: Telemetria a keresési forgalom elemzéséhez
titleSuffix: Azure Cognitive Search
description: Engedélyezze a keresési forgalom elemzését az Azure Cognitive Search szolgáltatásban, gyűjtse össze a telemetriai adatokat és a felhasználó által kezdeményezett eseményeket az Application Insights használatával, majd elemezze a Power BI-jelentések ben talált eredményeket.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258209"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Telemetriai adatok gyűjtése a keresési forgalom elemzéséhez

A keresési forgalom elemzés egy minta a felhasználói interakciók az Azure Cognitive Search alkalmazással kapcsolatos telemetriai adatok gyűjtésére, például a felhasználó által kezdeményezett kattintási események és a billentyűzet-bevitel. Ezen információk alapján meghatározhatja a keresési megoldás hatékonyságát, beleértve a népszerű keresési kifejezéseket, az átkattintási arányt és azt, hogy mely lekérdezési bemenetek eredményeznek nulla eredményt.

Ez a minta az [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (az [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)egyik szolgáltatása) függ a felhasználói adatok gyűjtéséhez. Ehhez hozzá kell adnia a műszerezést az ügyfélkódhoz, ahogy azt ebben a cikkben ismertetjük. Végül az adatok elemzéséhez jelentési mechanizmusra lesz szüksége. Javasoljuk a Power BI-t, de használhatja az Alkalmazás irányítópultját vagy bármely olyan eszközt, amely az Application Insightshoz csatlakozik.

> [!NOTE]
> A minta ebben a cikkben a speciális forgatókönyvek és kattintási adatok által létrehozott kódot az ügyfélhez hozzáadott. Ezzel szemben a szolgáltatásnaplók könnyen beállíthatók, számos metrika biztosításához, és a portálon kód nélkül elvégezhetők. A diagnosztikai naplózás engedélyezése minden esetben ajánlott. További információt a [Naplóadatok gyűjtése és elemzése](search-monitor-logs.md)című témakörben talál.

## <a name="identify-relevant-search-data"></a>A megfelelő keresési adatok azonosítása

Ahhoz, hogy hasznos mérőszámok at keresési forgalom elemzése, szükséges, hogy jelentkezzen be néhány jelet a felhasználók a keresési alkalmazás. Ezek a jelek olyan tartalmat jelentenek, amely a felhasználókat érdekli, és amelyeket relevánsnak tartanak. A keresési forgalom elemzéséhez a következők a következők:

+ Felhasználó által létrehozott keresési események: Csak a felhasználó által kezdeményezett keresési lekérdezések érdekesek. A keresési kérelmek, amelyek a ta- és adatadatok, a további tartalom vagy a belső információk feltöltésére szolgálnak, nem fontosak, és torzítják az eredményeket.

+ Felhasználó által létrehozott kattintási események: A keresési eredményoldalon a kattintási esemény általában azt jelenti, hogy a dokumentum releváns eredmény egy adott keresési lekérdezéshez.

Ha a keresési és kattintási eseményeket korrelációs azonosítóval kapcsolja össze, mélyebben megismerheti, hogy az alkalmazás keresési funkciója milyen jól teljesít.

## <a name="add-search-traffic-analytics"></a>Keresési forgalom elemzésének hozzáadása

Az Azure Cognitive Search szolgáltatás [portállapján](https://portal.azure.com) a Keresési forgalom elemzési lap tartalmaz egy cheat lapot a telemetriai minta követésére. Ezen a lapon kiválaszthat vagy létrehozhat egy Application Insights-erőforrást, lekaphatja a műszerezési kulcsot, lemásolhatja a megoldáshoz igazítható kódrészleteket, és letölthet egy Power BI-jelentést, amely a mintában tükröződő séma fölé épül.

![Keresés a Traffic Analytics oldalon a portálon](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Keresés a Traffic Analytics oldalon a portálon")

## <a name="1---set-up-application-insights"></a>1 - Az Application Insights beállítása

Jelöljön ki egy meglévő Application Insights-erőforrást, vagy [hozzon létre egyet,](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) ha még nem rendelkezik ilyenel. Ha a Search Traffic Analytics oldalt használja, másolhatja az alkalmazás nak az Application Insightshoz való csatlakozáshoz szükséges instrumentation kulcsát.

Miután rendelkezik egy Application Insights-erőforrással, kövesse [a támogatott nyelvekre és platformokra vonatkozó utasításokat](https://docs.microsoft.com/azure/azure-monitor/app/platforms) az alkalmazás regisztrálásához. A regisztráció egyszerűen hozzáadja a instrumentation kulcsot az Application Insightsból a kódhoz, amely beállítja a társítást. A kulcsot megtalálhatja a portálon, vagy a Traffic Analytics keresése lapon, amikor kiválaszt egy meglévő erőforrást.

A Visual Studio egyes projekttípusaihoz használt parancsikon a következő lépésekben jelenik meg. Létrehoz egy erőforrást, és néhány kattintással regisztrálja az alkalmazást.

1. A Visual Studio és ASP.NET fejlesztése esetén nyissa meg a megoldást, és válassza**az Application Insights telemetria hozzáadása** **lehetőséget.** > 

1. Kattintson **az Első lépések gombra.**

1. Regisztrálja az alkalmazást egy Microsoft-fiók, az Azure-előfizetés és egy Application Insights-erőforrás biztosításával (egy új erőforrás az alapértelmezett). Kattintson a **Regisztráció gombra.**

Ezen a ponton az alkalmazás alkalmazásfigyelés, ami azt jelenti, az összes oldal betöltése az alapértelmezett metrikák nyomon követhető. Az előző lépésekről az [Application Insights kiszolgálóoldali telemetriának engedélyezése](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)című témakörben talál további információt.

## <a name="2---add-instrumentation"></a>2 - Műszerek hozzáadása

Ez a lépés az, ahol a saját keresési alkalmazás, az Application Insights erőforrás a fenti lépésben létrehozott használatával. Négy lépésből áll a folyamat, kezdve egy telemetriai ügyfél létrehozásával.

### <a name="step-1-create-a-telemetry-client"></a>1. lépés: Telemetriai ügyfél létrehozása

Hozzon létre egy objektumot, amely eseményeket küld az Application Insights. Hozzáadhat instrumentation a szerver oldali alkalmazás kód vagy ügyféloldali kód fut a böngészőben, itt kifejezett C # és JavaScript változatok (más nyelveken, lásd a teljes listát a [támogatott platformok és keretrendszerek](https://docs.microsoft.com/azure/application-insights/app-insights-platforms). Válassza ki azt a megközelítést, amely megadja a kívánt információmélységet.

A kiszolgálóoldali telemetriai adatok az alkalmazásréteg metrikáit rögzítik, például a felhőben webszolgáltatásként vagy egy vállalati hálózaton helyszíni alkalmazásként futó alkalmazásokban. A kiszolgálóoldali telemetria rögzíti a keresési és kattintási eseményeket, a dokumentum helyét a találatok között, és a lekérdezési adatokat, de az adatgyűjtés hatóköre minden olyan információ, amely elérhető az adott rétegen.

Az ügyfélen előfordulhat, hogy további kóddal rendelkezik, amely manipulálja a lekérdezésbemeneteket, navigációs beállításokat ad hozzá, vagy környezetet is tartalmaz (például a kezdőlapról kezdeményezett és egy terméklapról kezdeményezett lekérdezések). Ha ez ismerteti a megoldást, előfordulhat, hogy az ügyféloldali instrumentation, hogy a telemetriai adatok tükrözik a további részleteket. A további részletek összegyűjtésének módja túlmutat a minta hatókörén, de további útmutatásért áttekintheti [az Application Insights for web oldalakat.](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) 

**C használata #**

A C#esetében a **InstrumentationKey** megtalálható az alkalmazás konfigurációjában, például az appsettings.json ban, ha a projekt ASP.NET. Ha nem biztos a kulcs helyében, olvassa vissza a regisztrációs utasításokat.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**JavaScript használata**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>2. lépés: Keresési azonosító kérése korrelációhoz

A keresési kérelmek és a kattintások korrelációjához szükség van egy korrelációs azonosítóra, amely a két különböző eseményt kapcsolja össze. Az Azure Cognitive Search egy keresési azonosítót biztosít, amikor HTTP-fejlécet kér.

A keresési azonosító lehetővé teszi az Azure Cognitive Search által a kérelemhez kibocsátott metrikák korrelációját az Application Insightsban naplóztatott egyéni metrikákkal.  

**C használata #**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**JavaScript használata (REST API-k hívása)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>3. lépés: Keresési események naplózása

Minden alkalommal, amikor egy felhasználó keresési kérelmet ad ki, ezt az Application Insights-egyéni esemény következő sémájával keresési eseményként kell naplóznia. Ne felejtsen el csak a felhasználó által generált keresési lekérdezéseket naplózni.

+ **SearchServiceName**: (karakterlánc) keresési szolgáltatás neve
+ **SearchId**: (guid) a keresési lekérdezés egyedi azonosítója (a keresési válaszban érkezik)
+ **IndexName**: (karakterlánc) keresési szolgáltatás indexe lekérdezendő
+ **QueryTerms**: (karakterlánc) keresési kifejezések, amelyeket a felhasználó adott meg
+ **ResultCount**: (int) visszaadott dokumentumok száma (a keresési válaszban érkezik)
+ **ScoringProfile**: (karakterlánc) a használt pontozási profil neve, ha van ilyen

> [!NOTE]
> Kérje a felhasználó által létrehozott lekérdezések számát úgy, hogy hozzáadja $count=true-t a keresési lekérdezéshez. További információt a [Dokumentumok keresése (REST)](/rest/api/searchservice/search-documents#counttrue--false)című témakörben talál.
>

**C használata #**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**JavaScript használata**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>4. lépés: Naplókattintási események

Minden alkalommal, amikor a felhasználó rákattint egy dokumentumra, ez egy olyan jel, amelyet keresési elemzés céljából naplózni kell. Az Application Insights egyéni események használatával naplózhatja ezeket az eseményeket a következő sémával:

+ **Szolgáltatásnév**: (karakterlánc) keresési szolgáltatás neve
+ **SearchId**: (guid) a kapcsolódó keresési lekérdezés egyedi azonosítója
+ **Bizonylat:**(karakterlánc) dokumentumazonosító
+ **Pozíció**: (int) a dokumentum rangsorban a keresési eredmények oldalán

> [!NOTE]
> A pozíció a kérelem ben található kardinális sorrendre vonatkozik. Ön szabadon beállíthatja ezt a számot, amíg ez mindig ugyanaz, hogy az összehasonlítás.
>

**C használata #**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**JavaScript használata**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 – Elemzés a Power BI-ban

Miután meghatározta az alkalmazást, és ellenőrizte, hogy az alkalmazás megfelelően csatlakozik az Application Insightshoz, letölt egy előre definiált jelentéssablont a Power BI desktop adatainak elemzéséhez. A jelentés előre definiált diagramokat és táblázatokat tartalmaz, amelyek hasznosak a keresési forgalom elemzéséhez rögzített további adatok elemzéséhez.

1. Az Azure Cognitive Search irányítópultbal navigációs ablaktábláján, a **Beállítások**csoportban kattintson a **Keresés forgalomelemzés elemre.**

1. A **Forgalomelemzés keresése** lap 3. **Get Power BI Desktop**

   ![Power BI-jelentések beszereznie](./media/search-traffic-analytics/get-use-power-bi.png "Power BI-jelentések beszereznie")

1. Ugyanezen az lapon kattintson a **Power BI-jelentés letöltése gombra.**

1. A jelentés megnyílik a Power BI Desktopban, és a rendszer kéri, hogy csatlakozzon az Application Insightshoz, és adja meg a hitelesítő adatokat. Kapcsolati információkat az Azure Insights-erőforrás hoz az Azure Portal-lapokban talál. Hitelesítő adatok esetén adja meg ugyanazt a felhasználónevet és jelszót, amelyet a portálbejelentkezéshez használ.

   ![Csatlakozás az Application Insightshoz](./media/search-traffic-analytics/connect-to-app-insights.png "Csatlakozás az Application Insightshoz")

1. Kattintson a **Betöltés gombra.**

A jelentés olyan diagramokat és táblázatokat tartalmaz, amelyek segítségével megalapozottabb döntéseket hozhat a keresési teljesítmény és a relevancia javítása érdekében.

A mérőszámok a következő elemeket tartalmazták:

+ Keresési mennyiség és a legnépszerűbb kifejezés-dokumentum párok: kifejezések, amelyek eredményeként ugyanazt a dokumentumot kattintott, kattintással rendezett.
+ Keresések kattintás ok nélkül: a kattintásnélküli regisztrációt regisztráló legnépszerűbb lekérdezések kifejezései

A következő képernyőkép bemutatja, hogyan nézhet ki egy beépített jelentés, ha az összes sémaelemet használta.

![Power BI-irányítópult az Azure Cognitive Search szolgáltatáshoz](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI-irányítópult az Azure Cognitive Search szolgáltatáshoz")

## <a name="next-steps"></a>További lépések

Műszer a keresési alkalmazás, hogy hatékony és éleslátó adatokat a keresési szolgáltatást.

További információkat az [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) ról, és látogasson el a [tarifaoldalon,](https://azure.microsoft.com/pricing/details/application-insights/) hogy többet tudjon meg a különböző szolgáltatási szintek.

További információ a lenyűgöző jelentések létrehozásáról. További részletek a [Power BI Desktop gal kapcsolatos első lépések.](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)