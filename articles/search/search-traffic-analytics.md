---
title: forgalmi elemzések keresése – Azure Search megvalósítása
description: Forgalmi elemzések keresése az Azure Search hozzáadása a telemetriai adatokhoz és a felhasználó által kezdeményezett események a naplófájlokba engedélyezése.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c30c8bae3e76778a31cdd0695acde52b5b1c6b02
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079664"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Forgalmi elemzések keresése megvalósítása az Azure Search szolgáltatásban
Forgalmi elemzések keresése egy minta megvalósításához egy visszacsatolási hurokba kerülnek a keresési szolgáltatáshoz. Ez a minta azt ismerteti, a szükséges adatokat, és hogyan gyűjtheti az Application Insights, az iparág vezető szolgáltatójától figyeléshez, több platformon szolgáltatások használatával.

Forgalmi elemzések keresése lehetővé teszi a keresési szolgáltatás értékes információkhoz juthat, és elemezheti a felhasználók és a felhasználók viselkedését. Fel adatokat a felhasználók kiválasztása, lehetséges döntéseket, amelyek a keresési élmény javítása, és ha az eredmény nem, nem várt leállás esetén.

Az Azure Search, amely integrálható az Azure Application Insights és a Power BI biztosít részletes figyelését és követését telemetriai megoldást kínál. Mivel az Azure Search-szal csak az API-kon keresztül, a telemetriai adatokat a fejlesztők keresési, ezen a lapon lévő utasítások követése hajtja végre.

## <a name="identify-relevant-search-data"></a>Releváns keresési adatok azonosítása

Ahhoz, hogy a keresési metrikákat, fontos a keresési alkalmazást az egyes jelek bejelentkezni. Ezek a jelek jelölésére, hogy a felhasználók érdekli a tartalom és, hogy azok fontolja meg az igényeiknek megfelelő.

Nincsenek két jelekkel kell forgalmi elemzések keresése:

1. Felhasználó által események keresése: csak a keresési lekérdezések egy felhasználó által kezdeményezett érdekes. Metszettel, további tartalmat vagy belső információkat, feltöltéséhez használt keresési kérelmek nem fontos és döntés, és a bias az eredményeket.

2. Felhasználó által kattintson az események: Ebben a dokumentumban kattintással által nevezzük egy adott keresési eredményben keresési lekérdezés által visszaadott kiválasztása. Egy-két kattintással általában azt jelenti, hogy a dokumentum egy adott keresési lekérdezésnek megfelelő eredményt.

Keresés, és kattintson a korrelációs azonosítót események összekapcsolásával az alkalmazás felhasználók és a viselkedés elemzéséhez. A keresési insights nem lehetséges, az csak a keresési forgalmi naplók beszerzése.

## <a name="add-search-traffic-analytics"></a>Adja hozzá a forgalmi elemzések keresése

Az az előző szakaszban említett jelek kell gyűjthetők össze a search-alkalmazás, ahogy a felhasználó használja azt. Az Application Insights egy bővíthető figyelési megoldás is, több platformon, rugalmas kialakítási lehetőségek az elérhető. Az Application Insights használatát teszi lehetővé a Power BI-keresési könnyebbé tenni az adatok elemzése az Azure Search által létrehozott jelentések előnyeit.

Az a [portál](https://portal.azure.com) oldala az Azure Search szolgáltatást, a forgalmi elemzések keresése panel egy adatlap vonatkozó Ez a minta telemetriai adatokat tartalmaz. Válassza ki vagy hozzon létre egy Application Insights-erőforrást, és tekintse meg a szükséges adatokat, egy helyen.

![Keresés a Traffic Analytics utasítások][1]

## <a name="1---select-a-resource"></a>1 – Válasszon ki egy erőforrást

Válassza ki az Application Insights-erőforrás, vagy hozzon létre egyet, ha már nincs szüksége. Egy erőforrás, amely már használja a szükséges egyéni eseményeket is használhatja.

Amikor egy új Application Insights-erőforrást hoz létre, minden alkalmazástípus esetében érvényesek ehhez a forgatókönyvhöz. Válassza ki a legjobban a platformot használ.

A kialakítási kulcsot az alkalmazáshoz a telemetriai ügyfél létrehozásához szükség van. Az Application Insights portál irányítópultján megtekintheti, vagy beszerezheti azt a forgalmi elemzések keresése lapon válassza a használni kívánt példány.

## <a name="2---add-instrumentation"></a>2 – üzemállapot

Ebben a fázisban, ahol, ezenkívül a saját keresési alkalmazás a fenti lépésben a létrehozott Application Insights-erőforrás használatával. Ez a folyamat négy lépésben történik:

**1. lépés: Hozzon létre egy telemetriai ügyfél** Ez az az objektum, amely elküldi az eseményeket az Application Insights-erőforrás.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Egyéb nyelvekhez és platformokhoz, tekintse meg a teljes [lista](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**2. lépés: Kérelem egy korrelációs Azonosítót keresési** keresési kérések korrelációját, kattintással, ezek két eseményhez kapcsolódó korrelációs azonosítóra van szükség. Az Azure Search nyújt a Search-Id fejléccel kérése:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**3. lépés: Keresési események naplózása**

Minden alkalommal egy keresési kérelmet a felhasználó által kiadott naplózni kell, hogy az Application Insights egyéni esemény a következő sémával keresési eseményként:

**Szolgáltatásnév**: (karakterlánc) search-szolgáltatásnév **SearchId**: egyedi azonosítója (guid), a keresési lekérdezés (a keresés válaszban bekövetkezik) **IndexName**: (karakterlánc) keresési szolgáltatás index kell lekérdezett **QueryTerms**: a felhasználó által megadott (karakterlánc) keresőkifejezéseket **ResultCount**: visszaadott dokumentumok (int) száma (a keresés válaszban bekövetkezik)  **ScoringProfile**: a alkalmazni, ha bármely relevanciaprofil nevét (karakterlánc)

> [!NOTE]
> A kérelmek száma a felhasználó által lekérdezések $count hozzáadásával = igaz értéket a keresési lekérdezés. További információ [Itt](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Fontos, hogy csak a felhasználók által létrehozott keresési lekérdezések naplózása.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**4. lépés: Kattintson a bejelentkezés események**

Minden alkalommal, amikor egy felhasználó rákattint egy dokumentumot, amely egy olyan jelet, amelyek keresési elemzési célokra kell bejelentkeznie. Egyéni eseményeket az Application Insights segítségével jelentkezzen ezeket az eseményeket a következő mintát követik:

**Szolgáltatásnév**: (karakterlánc) search-szolgáltatásnév **SearchId**: egyedi azonosítója (guid), a kapcsolódó keresési lekérdezés **dokumentumazonosító**: (karakterlánc) dokumentumazonosító **pozíciója**: (int) rang a dokumentum a keresés eredményeit tartalmazó lap

> [!NOTE]
> Pozíció a megadott kardinális sorrendben az alkalmazás vonatkozik. Szabadon állítsa ezt a számot, amíg a rendszer mindig azonos, az összehasonlítást engedélyezéséhez.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - elemzése a Power bi-ban

Miután az alkalmazás kialakítva és ellenőrizte az alkalmazás megfelelően csatlakozik-e az Application Insights, használhatja az Azure Search a Power BI desktopban létrehozott előre definiált sablon. 

Egy figyelését teszi lehetővé az Azure search [Power BI-tartalomcsomag](https://app.powerbi.com/getdata/services/azure-search) így is elemezheti a naplófájlok adatait. A tartalomcsomag T hozzáadja az előre meghatározott diagramok és táblázatok forgalmi elemzések kereséséhez rögzített további adatelemzés számára hasznos. További információkért lásd: a [tartalomcsomag súgóoldalán](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. Az Azure-ban keresési irányítópult bal oldali navigációs ablaktáblán a **beállítások**, kattintson a **forgalmi elemzések keresése**.

2. Az a **forgalmi elemzések keresése** lapon, a 3. lépésében, kattintson a **lekérése a Power BI Desktop** telepíteni a Power bi-ban.

   ![A Power BI-jelentések lekérése](./media/search-traffic-analytics/get-use-power-bi.png "lekérése a Power BI-jelentések")

2. Ugyanazon az oldalon, kattintson a **töltse le a Power bi-jelentés**.

3. Ekkor megnyílik a jelentés a Power BI Desktopban, és csatlakozás az Application Insightshoz kéri. Ezt az információt az Azure portál lapjain, Application Insights-erőforrás található.

   ![Csatlakozás az Application Insightshoz](./media/search-traffic-analytics/connect-to-app-insights.png "csatlakozás az Application Insightshoz")

4. Kattintson a **terhelés**.

A jelentés diagramokat tartalmaz és táblákat, amelyek segítségével több megalapozottabb döntéseket hozhat, javíthatja a keresési teljesítmény és a relevancia alapján végzett.

Metrikák a következő elemeket tartalmazza:

* Kattintson keresztül sebessége (Parancsra): kattintson a teljes keresések száma egy adott dokumentum felhasználók aránya.
* Keresés a kattintás nélkül: készült leggyakoribb lekérdezések, amelyek egyetlen kattintással regisztrálása
* Legtöbb kattintott a dokumentumok: legfontosabb elemeire való kattintások dokumentumok azonosítója alapján az elmúlt 24 óra, 7 napban és 30 nap.
* Népszerű kifejezés-dokumentum párok: feltételek, amelyek ugyanazt a dokumentumot kattint, kattintással szerint rendezve.
* Kattintson az időt: kattintással bucketed által a keresési lekérdezés óta eltelt idő

A következő képernyőképen látható a beépített jelentéseket, és elemzésére diagramok keresse meg a traffic analytics.

![A Power BI-irányítópultot az Azure Search](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Power BI-irányítópultot az Azure Search")

## <a name="next-steps"></a>További lépések
Alakítsa ki úgy a keresési alkalmazások a search szolgáltatás hatékony és legsokatmondóbb adatokat.

További információt talál a [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) , és látogasson el a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/application-insights/) további információ a különböző szolgáltatásszintek.

További információ a lenyűgöző jelentések létrehozásához. Lásd: [Ismerkedés a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) részletekért

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
