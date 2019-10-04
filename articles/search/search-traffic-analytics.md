---
title: a Search Traffic Analytics megvalósítása – Azure Search
description: A telemetria és a felhasználó által kezdeményezett események naplófájlba való felvételének engedélyezése a Azure Search keresési forgalmának elemzéséhez.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb12ed2f18df100ab3f679e7a8a3ef1e7c1aca45
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647806"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>A Search Traffic Analytics megvalósítása Azure Search
A Search Traffic Analytics a keresési szolgáltatás visszajelzési ciklusának megvalósítására szolgáló minta. Ez a minta leírja a szükséges adatokat, valamint azt, hogyan gyűjtheti be a Application Insights használatával, egy iparági vezetővel a szolgáltatások figyeléséhez több platformon.

A Search Traffic Analytics lehetővé teszi, hogy megismerje a keresési szolgáltatását, és feltárja az elemzéseket a felhasználókkal és azok viselkedésével kapcsolatban. A felhasználók által választott információk alapján olyan döntéseket hozhat, amelyekkel tovább javíthatja a keresési élményt, és visszatérhet, ha az eredmények nem a vártak.

A Azure Search telemetria-megoldást kínál, amely az Azure Application Insights és Power BI integrálásával biztosítja a részletes monitorozást és nyomon követést. Mivel az Azure Search-vel való interakció csak API-k használatával történik, a telemetria a keresővel kell megvalósítani, az ezen az oldalon található utasításokat követve.

## <a name="identify-relevant-search-data"></a>A releváns keresési adatokat azonosítsa

Ahhoz, hogy hasznos keresési mérőszámokat lehessen használni, néhány jelet be kell jelentkeznie a keresési alkalmazás felhasználóival. Ezek a jelek azokat a tartalmakat jelentik, amelyeket a felhasználók érdeklik, és amelyeket az igényeiknek megfelelőnek tekintenek.

Két jelzést kell keresni Traffic Analytics a következőkre van szüksége:

1. Felhasználó által generált keresési események: a felhasználó által kezdeményezett keresési lekérdezések csak érdekesek. Az aspektusok, a további tartalmak vagy a belső információk kitöltéséhez használt keresési kérelmek nem fontosak, és az eredmények eldöntésére és torzítására szolgálnak.

2. Felhasználó által generált kattintási események: A dokumentumra kattintva egy olyan felhasználóra utalunk, amely egy keresési lekérdezés által visszaadott keresési eredményt választ. A kattintás általában azt jelenti, hogy egy dokumentum egy adott keresési lekérdezés szempontjából releváns eredmény.

A keresés összekapcsolásával és a korrelációs azonosítóval rendelkező események lehetőséggel elemezheti az alkalmazás felhasználóinak viselkedését. Ezek a keresési elemzések nem szerezhetők be kizárólag keresési forgalmi naplók használatával.

## <a name="add-search-traffic-analytics"></a>Keresési forgalom elemzésének hozzáadása

Az előző szakaszban említett jeleket össze kell gyűjteni a keresési alkalmazásból, mivel a felhasználó kommunikál vele. A Application Insights egy bővíthető figyelési megoldás, amely több platformon érhető el, rugalmas kialakítási lehetőségekkel. A Application Insights használata lehetővé teszi, hogy kihasználhassa az Azure Search által létrehozott Power BI keresési jelentéseket, hogy könnyebb legyen az adatok elemzése.

A Azure Search szolgáltatásának [portál](https://portal.azure.com) lapján a Search Traffic Analytics panel egy Cheat (keresés) lapot tartalmaz a következő telemetria-minta követéséhez. Kiválaszthat vagy létrehozhat egy Application Insights-erőforrást, és megtekintheti az összes szükséges információt egy helyen.

![Traffic Analytics utasítások keresése][1]

## <a name="1---select-a-resource"></a>1 – erőforrás kiválasztása

Ki kell választania egy használni kívánt Application Insights-erőforrást, vagy létre kell hoznia egyet, ha még nem rendelkezik ilyennel. Olyan erőforrást használhat, amely már használatban van a szükséges egyéni események naplózásához.

Új Application Insights-erőforrás létrehozásakor az összes alkalmazás típusa érvényes ehhez a forgatókönyvhöz. Válassza ki az Ön által használt platformhoz legjobban illőt.

Szüksége lesz a kialakítási kulcsra az alkalmazás telemetria-ügyfelének létrehozásához. A Application Insights-portál irányítópultján kérheti le, vagy beolvashatja a keresés Traffic Analytics oldalon, és kiválaszthatja a használni kívánt példányt.

## <a name="2---add-instrumentation"></a>2 – rendszerállapot-Hozzáadás

Ebben a fázisban a saját keresési alkalmazásra van szükség a fenti lépésben létrehozott Application Insights erőforrás használatával. A folyamat négy lépésből áll:

**1. lépés: Telemetria-ügyfél** létrehozása ez az az objektum, amely az Application Insights erőforrásnak küld eseményeket.

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

**2. lépés: Kérjen meg egy keresési azonosítót a** korrelációhoz a keresési kérelmeknek a kattintásokkal való összekapcsolásához, ezért olyan korrelációs azonosítóra van szükség, amely a két különböző eseményt érinti. A Azure Search keresési azonosítót biztosít, ha a fejlécet kéri:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

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

**3. lépés: Keresési események naplózása**

Minden alkalommal, amikor egy felhasználó egy keresési kérelmet ad ki, a következő sémával rendelkező keresési eseményként kell bejelentkeznie egy Application Insights egyéni eseményre:

**SearchServiceName**: (karakterlánc) keresési szolgáltatás neve **SearchId**: (GUID) a keresési lekérdezés egyedi azonosítója (a keresési válaszban szerepel) **IndexName**: (karakterlánc) keresési szolgáltatás indexe lekérdezési **QueryTerms**: (karakterlánc) keresés a felhasználói **ResultCount**által megadott kifejezések: (int) a visszaadott dokumentumok száma (a keresési válaszban) **ScoringProfile**: (karakterlánc) a használt pontozási profil neve, ha van ilyen

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

**4. lépés: Napló kattintási események**

Minden alkalommal, amikor egy felhasználó egy dokumentumra kattint, ez egy olyan jel, amelyet a keresési elemzési célokra kell naplózni. Ezeket az eseményeket a következő sémával naplózhatja Application Insights egyéni események használatával:

**Szolgáltatásnév**: (karakterlánc) keresési szolgáltatás neve **SearchId**: (GUID) a kapcsolódó keresési lekérdezés **dokumentumazonosító**: (karakterlánc) a dokumentum azonosító pozíciója: (int) a keresési eredmények lapon

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

Miután felkészítette az alkalmazást, és ellenőrizte, hogy az alkalmazás megfelelően van csatlakoztatva a Application Insightshoz, használhatja a Azure Search által létrehozott előre definiált sablont Power BI Desktophoz. 

Az Azure Search egy figyelési [Power bi tartalomkezelő csomag](https://app.powerbi.com/getdata/services/azure-search) , amely lehetővé teszi a naplózási adatokat. A Content Pack olyan előre definiált diagramokat és táblázatokat is biztosít, amelyek hasznosak a Search Traffic Analytics szolgáltatásban rögzített további adatok elemzéséhez. További információkért lásd: a [tartalomcsomag súgóoldalán](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. A Azure Search-irányítópult bal oldali navigációs ablaktábláján, a **Beállítások**területen kattintson a **Traffic Analytics keresése**elemre.

2. A **Keresés a forgalom elemzéséhez** lapon, a 3. lépésben kattintson az **Power bi Desktop** beolvasása elemre a Power bi telepítéséhez.

   ![Power bi jelentések] beolvasása (./media/search-traffic-analytics/get-use-power-bi.png "Power bi jelentések") beolvasása

2. Ugyanazon a lapon kattintson a **PowerBI-jelentés letöltése**elemre.

3. A jelentés Power BI Desktopban nyílik meg, és a rendszer kéri, hogy kapcsolódjon Application Insightshoz. Ezeket az információkat a Azure Portal oldalain találja Application Insights erőforráshoz.

   ![Kapcsolódás Application Insightshoz](./media/search-traffic-analytics/connect-to-app-insights.png "Kapcsolódás Application Insightshoz")

4. Kattintsona betöltés elemre.

A jelentés olyan diagramokat és táblázatokat tartalmaz, amelyek segítségével jobban tájékozott döntéseket hozhat a keresési teljesítmény és a relevancia javítása érdekében.

A metrikák a következő elemeket foglalják magukban:

* Kattintási arány (CTR): a felhasználók aránya, akik egy adott dokumentumra kattintanak a teljes keresések számán.
* Kattintások nélkül végzett keresések: a nem kattintást igénylő leggyakoribb lekérdezések feltételei
* Legtöbbször rákattintottak a dokumentumok elemre: az elmúlt 24 órában, 7 nap és 30 nap alatt a dokumentumok többsége AZONOSÍTÓval kattintott.
* Népszerű kifejezés – dokumentum párok: azok a feltételek, amelyek az adott dokumentum eredményét eredményezik, kattintásra rendezve.
* Kattintson ide a kattintáshoz: a keresési lekérdezés óta az idő a gyűjtőre kattint

Az alábbi képernyőfelvételen a beépített jelentések és diagramok láthatók a Search Traffic Analytics elemzéséhez.

![Azure Search Power bi irányítópult](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Azure Search Power bi irányítópult")

## <a name="next-steps"></a>További lépések
A keresési alkalmazás hatékony és átgondolt adatainak beszerzése a keresési szolgáltatással.

A Application Insightsról további információt talál [](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) , és megtekintheti a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-insights/) , ahol további információkat találhat a különböző szolgáltatási szintjeiről.

További információ a lenyűgöző jelentések létrehozásához. Lásd: [Ismerkedés a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) részletekért

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
