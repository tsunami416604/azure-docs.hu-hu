---
title: Jelentés a keresési forgalom elemzési adatairól
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search Search Traffic Analytics szolgáltatásának engedélyezése, a telemetria és a felhasználó által kezdeményezett események összegyűjtése Application Insights használatával, majd az eredmények elemzése egy Power BI jelentésben.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 53fd02856a805f8bb5d7261cc9e6e32861b2b4fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426992"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>A Search Traffic Analytics megvalósítása az Azure-ban Cognitive Search

A Search Traffic Analytics a keresési szolgáltatás visszajelzési ciklusának megvalósítására szolgáló minta. A cél a telemetria begyűjtése a felhasználó által kezdeményezett Click Events és Keyboard Inputs elemre. Ezen információk alapján meghatározhatja a keresési megoldás hatékonyságát, beleértve a népszerű keresési kifejezéseket, az átkattintási arányt, valamint azt, hogy mely lekérdezési bemenetek nulla eredményt adnak.

Ez a minta a felhasználói adatok gyűjtéséhez [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) ( [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/)) függőségét veszi igénybe. Emellett a jelen cikkben leírtak szerint hozzá kell adnia a rendszerállapotot az ügyfél kódjához. Végezetül szüksége lesz egy jelentéskészítési mechanizmusra az adatelemzéshez. Javasoljuk, hogy Power BI, de bármely olyan eszközt használhat, amely a Application Insightshoz csatlakozik.

> [!NOTE]
> A cikkben ismertetett minta speciális forgatókönyvekre és az ügyfél által létrehozott kattintássorozat-adatokra szolgál. Azt is megteheti, hogy jelentést készít a keresési szolgáltatás által létrehozott naplózási adatokról. További információ a Service log-jelentésekről: az [erőforrás-felhasználás és a lekérdezési tevékenységek figyelése](search-monitor-usage.md).

## <a name="identify-relevant-search-data"></a>A releváns keresési adatokat azonosítsa

Ahhoz, hogy hasznos keresési mérőszámokat lehessen használni, néhány jelet be kell jelentkeznie a keresési alkalmazás felhasználóival. Ezek a jelek azokat a tartalmakat jelentik, amelyeket a felhasználók érdeklik, és amelyeket az igényeiknek megfelelőnek tekintenek.

Két jelzést kell keresni Traffic Analytics a következőkre van szüksége:

+ Felhasználó által generált keresési események: csak a felhasználó által kezdeményezett keresési lekérdezések érdeklik. Az aspektusok, a további tartalmak vagy a belső információk kitöltéséhez használt keresési kérelmek nem fontosak, és az eredmények eldöntésére és torzítására szolgálnak.

+ Felhasználó által generált Click Events: a jelen dokumentumra kattintáskor a keresési lekérdezés által visszaadott keresési eredmény kiválasztására szolgáló felhasználóra hivatkozunk. A kattintás általában azt jelenti, hogy egy dokumentum egy adott keresési lekérdezés szempontjából releváns eredmény.

A keresés összekapcsolásával és a korrelációs AZONOSÍTÓval rendelkező események lehetőséggel elemezheti az alkalmazás felhasználóinak viselkedését. Ezek a keresési elemzések nem szerezhetők be kizárólag keresési forgalmi naplók használatával.

## <a name="add-search-traffic-analytics"></a>Keresési forgalom elemzésének hozzáadása

Az előző szakaszban említett jeleket össze kell gyűjteni a keresési alkalmazásból, mivel a felhasználó kommunikál vele. A Application Insights egy bővíthető figyelési megoldás, amely több platformon érhető el, rugalmas kialakítási lehetőségekkel. A Application Insights használata lehetővé teszi, hogy kihasználhassa az Azure Cognitive Search által létrehozott Power BI keresési jelentéseket, hogy az adatok elemzése egyszerűbb legyen.

Az Azure Cognitive Search szolgáltatásának [portál](https://portal.azure.com) lapján a keresés Traffic Analytics oldal tartalmaz egy Cheat lapot a következő telemetria-minta követéséhez. Kiválaszthat vagy létrehozhat egy Application Insights-erőforrást, és megtekintheti az összes szükséges információt egy helyen.

![Traffic Analytics utasítások keresése][1]

## <a name="1---select-a-resource"></a>1 – erőforrás kiválasztása

Ki kell választania egy használni kívánt Application Insights-erőforrást, vagy létre kell hoznia egyet, ha még nem rendelkezik ilyennel. Olyan erőforrást használhat, amely már használatban van a szükséges egyéni események naplózásához.

Új Application Insights-erőforrás létrehozásakor az összes alkalmazás típusa érvényes ehhez a forgatókönyvhöz. Válassza ki az Ön által használt platformhoz legjobban illőt.

Szüksége lesz a kialakítási kulcsra az alkalmazás telemetria-ügyfelének létrehozásához. A Application Insights-portál irányítópultján kérheti le, vagy beolvashatja a keresés Traffic Analytics oldalon, és kiválaszthatja a használni kívánt példányt.

## <a name="2---add-instrumentation"></a>2 – rendszerállapot-Hozzáadás

Ez a lépés a saját keresési alkalmazásának eszköze, amely a fenti lépésben létrehozott Application Insights erőforrást használja. A folyamat négy lépésből áll:

**1. lépés: telemetria-ügyfél létrehozása**

Ez az az objektum, amely eseményeket küld a Application Insights erőforrásnak.

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

Más nyelvekhez és platformokhoz tekintse meg a teljes [listát](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**2. lépés: keresési azonosító kérése a korrelációhoz**

Ha a keresési kérelmeket a kattintásokkal szeretné összekapcsolni, olyan korrelációs AZONOSÍTÓra van szükség, amely a két különböző eseményt érinti. Az Azure Cognitive Search keresési azonosítót biztosít, ha a fejlécet kéri:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
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

**3. lépés: keresési események naplózása**

Minden alkalommal, amikor egy felhasználó egy keresési kérelmet ad ki, a következő sémával rendelkező keresési eseményként kell bejelentkeznie egy Application Insights egyéni eseményre:

**SearchServiceName**: (karakterlánc) keresési szolgáltatás neve **SearchId**: (GUID) a keresési lekérdezés egyedi azonosítója (a keresési válaszban szerepel) **IndexName**: (karakterlánc) keresési szolgáltatási index a lekérdezhető **QueryTerms**: (karakterlánc) a felhasználói **ResultCount**által megadott keresési kifejezések: (int) a visszaadott dokumentumok száma (a keresés válasza) **ScoringProfile**: (karakterlánc) a használt pontozási profil neve, ha van ilyen

> [!NOTE]
> A kérések száma a felhasználó által generált lekérdezésekben $count = True hozzáadásával a keresési lekérdezéshez. További információk [itt](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) találhatók
>

> [!NOTE]
> Ne feledje, hogy csak a felhasználók által létrehozott keresési lekérdezéseket naplózza.
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

**4. lépés: kattintson az események naplózása elemre.**

Minden alkalommal, amikor egy felhasználó egy dokumentumra kattint, ez egy olyan jel, amelyet a keresési elemzési célokra kell naplózni. Ezeket az eseményeket a következő sémával naplózhatja Application Insights egyéni események használatával:

**Szolgáltatásnév**: (karakterlánc) keresési szolgáltatás neve **SearchId**: (GUID) a kapcsolódó keresési lekérdezés **dokumentumazonosító**: (karakterlánc) a dokumentum azonosító **pozíciója**: (int) a keresési eredmények lapon

> [!NOTE]
> A pozíció a kardinális sorrendre hivatkozik az alkalmazásban. Az összehasonlításhoz szabadon állíthatja be ezt a számot, amennyiben az mindig ugyanaz.
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

## <a name="3---analyze-in-power-bi"></a>3 – Power BI elemzése

Miután felkészítette az alkalmazást, és ellenőrizte, hogy az alkalmazás megfelelően van csatlakoztatva a Application Insightshoz, letölt egy előre definiált jelentési sablont, hogy elemezze az adatait a Power BI Desktopban. A jelentés előre definiált diagramokat és táblázatokat tartalmaz, amelyek hasznosak a Search Traffic Analytics szolgáltatásban rögzített további adatok elemzéséhez. 

1. Az Azure Cognitive Search-irányítópult bal oldali navigációs ablaktábláján, a **Beállítások**területen kattintson a **Traffic Analytics keresése**elemre.

2. A **Keresés a forgalom elemzéséhez** lapon, a 3. lépésben kattintson az **Power bi Desktop beolvasása** elemre a Power bi telepítéséhez.

   ![Power BI jelentések beolvasása](./media/search-traffic-analytics/get-use-power-bi.png "Power BI jelentések beolvasása")

2. Ugyanazon a lapon kattintson a **Power bi-jelentés letöltése**elemre.

3. A jelentés Power BI Desktopban nyílik meg, és a rendszer felszólítja, hogy kapcsolódjon Application Insightshoz, és adja meg a hitelesítő adatokat. A Application Insights-erőforrás Azure Portal lapjain a kapcsolatok adatai találhatók. A hitelesítő adatok esetében adja meg ugyanazt a felhasználónevet és jelszót, amelyet a portálon való bejelentkezéshez használ.

   ![Kapcsolódás Application Insightshoz](./media/search-traffic-analytics/connect-to-app-insights.png "Csatlakozás az Application Insightshoz")

4. Kattintson a **Betöltés** lehetőségre.

A jelentés olyan diagramokat és táblázatokat tartalmaz, amelyek segítségével jobban tájékozott döntéseket hozhat a keresési teljesítmény és a relevancia javítása érdekében.

A metrikák a következő elemeket foglalják magukban:

* Keresési mennyiség és a legnépszerűbb kifejezés – dokumentum párok: azok a feltételek, amelyek a dokumentumra kattintanak, a kattintások szerint rendezve jelennek meg.
* Kattintások nélkül végzett keresések: a nem kattintást igénylő leggyakoribb lekérdezések feltételei

Az alábbi képernyőfelvételen a beépített jelentések és diagramok láthatók a Search Traffic Analytics elemzéséhez.

![Power BI irányítópult az Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI irányítópult az Azure Cognitive Search")

## <a name="next-steps"></a>Következő lépések
A keresési alkalmazás hatékony és átgondolt adatainak beszerzése a keresési szolgáltatással.

A [Application Insightsról](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) további információt talál, és megtekintheti a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-insights/) , ahol további információkat találhat a különböző szolgáltatási szintjeiről.

További információ a csodálatos jelentések létrehozásáról. A részletekért tekintse meg [a Power bi Desktop első lépéseit](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) ismertető témakört.

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
