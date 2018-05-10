---
title: Forgalom Analytics keresse meg az Azure Search |} Microsoft Docs
description: Engedélyezze a keresési forgalom analytics az Azure Search, egy üzemeltetett felhőalapú keresőszolgáltatás, a Microsoft Azure-ban a felhasználók és az adatok észrevételeket feloldásához.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2017
ms.author: heidist
ms.openlocfilehash: 4b40e8c9f681b7489c0ab2ffe7b369cc869c73e2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-search-traffic-analytics"></a>Mi az a keresési forgalom analytics
Keresési forgalom analytics egy minta megvalósításához a keresési szolgáltatáshoz visszajelzés hurok. Ebben a mintában a szükséges adatokat és az Application Insights, több platformon szolgáltatások figyelésre egy iparágban vezető használatával gyűjtéséről ismerteti.

Keresési forgalom analytics lehetővé teszi, hogy, hogy lássák a keresési szolgáltatás és a felhasználók és azok viselkedését észrevételeket zárolásának feloldásához. Azzal, hogy a felhasználók válassza ki az adatait, akkor lehetséges, a keresés felhasználói élmény javítása döntések, és ha a eredményei nem várt leállás.

Az Azure Search kínál a telemetriai adatok megoldás, amely Azure Application Insights és a Power BI adja meg a részletes megfigyelését és nyomon követését. Mivel az Azure Search interakció csak az API-k segítségével, a telemetria a fejlesztők keresési, ezen a lapon utasításait követve hajtja végre.

## <a name="identify-the-relevant-search-data"></a>Azonosítsa a megfelelő keresési adatokat

Ahhoz, hogy a keresési metrikákat, fontos a keresőalkalmazás felhasználói néhány jelek bejelentkezni. Ezek a jelek felhasználók érdekli, a tartalom és azok fontolja meg, hogy az igényeiknek megfelelő jelölésére.

Nincsenek keresési forgalom Analyticsnek tudnia kell két jelek:

1. Felhasználók által létrehozott keresési események: csak a felhasználó által kezdeményezett keresési lekérdezések érdekes. Értékkorlátozás, további tartalmat vagy belső információkat, feltöltéséhez használt, a Search kérelmek nem fontosak, és döntés, és az eredmények bias.

2. Kattintson a felhasználók által létrehozott események: Ebben a dokumentumban kattintással által hivatkozunk egy felhasználó egy adott keresési eredmény egy keresési lekérdezés által visszaadott kiválasztása. Egy kattintással általában azt jelenti, hogy a dokumentum egy adott keresési lekérdezés vonatkozó eredményt.

Történő kapcsolásával végezze a keresést, és kattintson események korrelációs azonosítója, akkor az alkalmazás a felhasználói viselkedés elemzéséhez. A keresési insights nem lehetséges, csak a keresési forgalmi naplók az beszerzése.

## <a name="how-to-implement-search-traffic-analytics"></a>Keresési forgalom analytics implementálása

A jelek, az előző szakaszban említett, a felhasználó kommunikál, akkor gyűjtik a keresési alkalmazásból. Az Application Insights egy bővíthető figyelési megoldás, több platformon, a rugalmas instrumentation beállításokkal elérhető. Az Application Insights használata lehetővé teszi a Power BI-keresési megkönnyítése érdekében az adatok elemzése Azure Search által létrehozott jelentések előnyeit.

Az a [portal](https://portal.azure.com) az Azure Search szolgáltatás, a keresési forgalom elemzés panel a lap tartalmazza-e egy adatlap a következő telemetriai adat ebben a mintában. Válassza ki vagy hozzon létre egy Application Insights-erőforrást, és tekintse meg a szükséges adatokat, egyetlen helyen.

![Keresési forgalom Analytics utasításokat][1]

### <a name="1-select-an-application-insights-resource"></a>1. Válassza ki az Application Insights-erőforrás

Válassza ki az Application Insights-erőforrás használja, vagy hozzon létre egyet, ha már nincs szüksége. Használhatja a erőforrása, amely már használja a szükséges egyéni események naplózása.

Amikor hoz létre egy új Application Insights-erőforrást, minden típusok érvényesek az ebben a forgatókönyvben. Válassza ki azt, amelyik a legjobban megfelel a platformot használ.

A telemetriai ügyfél, az alkalmazás létrehozása a instrumentation kulcs szükséges. Lekérheti az Application Insights portál Irányítópultjára, vagy beszerezheti a keresési forgalom Analytics lapon válassza a használni kívánt példány.

### <a name="2-instrument-your-application"></a>2. Beállíthatják az alkalmazás

Ebben a fázisban, ahol Ön állíthatnak be a saját keresési alkalmazás használatát az Application Insights-erőforrás a létrehozott a fenti lépést. Ez a folyamat négy lépésben történik:

**I. Hozzon létre a telemetriai ügyfél** események küldése az Application Insights-erőforrást az objektum.

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

**II. A keresési azonosítójú korreláció** összefüggéseket a search kérelemmel kattintással, ezek két eseményhez kapcsolódó korrelációs azonosítója szükség. Az Azure Search tesz lehetővé a keresési azonosítót fejléccel igénylésekor:

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

**III. Keresési események naplózása**

Minden alkalommal keresési kérelem egy felhasználó által kiadott naplózni kell, hogy az Application Insights egyéni esemény a következő sémával keresési eseményként:

**Szolgáltatásnév**: (karakterlánc) keresőszolgáltatás nevének **SearchId**: a keresési lekérdezés egyedi azonosítóját (guid) (a keresési válaszul származik) **IndexName**: (karakterlánc) keresési szolgáltatás index kell lennie lekérdezett **QueryTerms**: a felhasználó által megadott (karakterlánc) keresőkifejezéseket **attribútumhoz resultcount számlálót**: (int) azon dokumentumok száma, a visszaadott (származik a keresési válaszul)  **ScoringProfile**: használja, ha van ilyen relevanciaprofil nevét (karakterlánc)

> [!NOTE]
> Kérelmek száma a felhasználók által létrehozott lekérdezések $count hozzáadásával = igaz értéket a keresési lekérdezés. További információ [Itt](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Ne feledje, hogy a felhasználók által létrehozott keresési lekérdezések csak bejelentkezni.
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

**IV. Bejelentkezéshez kattintson események**

Minden alkalommal, a felhasználó kattint a dokumentumot, keresési elemzési célokra naplózandó jel. Az Application Insights egyéni események használatával ezek az események naplózása a következő sémával:

**Szolgáltatásnév**: (karakterlánc) keresőszolgáltatás nevének **SearchId**: egyedi azonosítója (guid), a kapcsolódó keresési lekérdezés **dokumentumazonosító**: (karakterlánc) dokumentumazonosító **pozíciója**: a Keresés a dokumentum (int) rangsorát eredményeit tartalmazó lap

> [!NOTE]
> Pozíció az alkalmazás kardinális sorrendje hivatkozik. Szabadon ennyi, mindaddig, amíg a rendszer mindig azonos, az összehasonlítás engedélyezéséhez.
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

### <a name="3-analyze-with-power-bi-desktop"></a>3. A Power BI Desktop elemzése

Az alkalmazás tagolva, és ellenőrizte az alkalmazás megfelelően csatlakozik-e az Application Insights, után egy Azure Search szolgáltatást a Power BI desktopban létrehozott előre definiált sablont is használhatja.
Ez a sablon tartalmazza a diagramok és táblákat, amelyek segítenek a keresési teljesítményének és relevanciájának javítása érdekében több kérdésekre vonatkozó döntések meghozatalában.

Hozható létre a Power BI Virtuálisasztal-sablont, az Application Insights három adatokra van szükség. Ezeket az adatokat a keresési forgalom Analytics lapon találhatók, a használandó erőforrás kiválasztásakor

![Application Insights adatainak a keresési forgalom Analytics panelen][2]

A metrikák a Power BI Virtuálisasztal-sablon szerepel:

*   Kattintson keresztül gyakorisága (Parancsra): a felhasználók, akik kattintson az egy adott dokumentum teljes keresések számának aránya.
*   Keresés a kattintások nélkül: feltételei leggyakoribb lekérdezések, amelyeket egyetlen kattintással regisztrálni
*   Legtöbb kattintott a dokumentumok: legtöbb kattintott a dokumentumok azonosító által az elmúlt 24 órában, 7 napban és 30 nap.
*   Népszerű kifejezés-dokumentum párok: kattint, a dokumentumon eredményező feltételeket által gombra kell kattintania.
*   Kattintson az idő: a keresési lekérdezés óta eltelt idő által összegyűjtött kattintással

![A Power BI sablon az Application Insights olvasásra][3]


## <a name="next-steps"></a>További lépések
Állíthatnak be a keresési alkalmazások hatékony és osztályon adatait a keresési szolgáltatáshoz.

További információt az Application Insights [Itt](https://go.microsoft.com/fwlink/?linkid=842905). Látogasson el az Application Insights [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/application-insights/) további információt a különböző szolgáltatásrétegeiben használt funkciókkal.

További tudnivalók elképesztő jelentések létrehozásához. Lásd: [első lépések a Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) részletek

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
