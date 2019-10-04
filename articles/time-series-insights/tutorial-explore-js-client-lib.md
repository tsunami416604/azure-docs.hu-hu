---
title: 'Oktatóanyag: Ismerkedés a Azure Time Series Insights JavaScript-ügyfél függvénytárával | Microsoft Docs'
description: Oktatóanyag a Azure Time Series Insights JavaScript ügyféloldali kódtár és a kapcsolódó programozási modell megismeréséhez.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: a5184b49f4608acebbe6bf4734dde99857d16fc9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845259"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Oktatóanyag: Az Azure Time Series Insights JavaScript ügyfélkódtár felderítése

A JavaScript D3-alapú Azure Time Series Insights ügyféloldali kódtára a webfejlesztők számára a Time Series Insightsban tárolt adatlekérdezés és-megjelenítés segítése érdekében lett kifejlesztve. Ez az oktatóanyag végigvezeti a Time Series Insights ügyféloldali függvénytáron és programozási modellen az üzemeltetett minta alkalmazás használatával.

Az oktatóanyag részletesen ismerteti, hogyan használható a könyvtár, hogyan férhet hozzá Time Series Insights adatokhoz, és hogyan jelenítheti meg és jelenítheti meg a diagram vezérlőelemeket az adatok megjelenítéséhez és megjelenítéséhez. Azt is megtudhatja, hogyan lehet kísérletezni különböző típusú gráfokkal az adatmegjelenítéshez. Az oktatóanyag végén lehetősége lesz arra, hogy az ügyféloldali kódtár használatával beépítse Time Series Insights szolgáltatásokat a saját webalkalmazásba.

Pontosabban a következőket fogja tudni:

> [!div class="checklist"]
> * A Time Series Insights minta alkalmazás
> * A Time Series Insights JavaScript ügyféloldali kódtár
> * Hogyan használja a minta alkalmazás a könyvtárat a Time Series Insights-adatmegjelenítéshez

> [!NOTE]
> * Az oktatóanyag egy ingyenes, üzemeltetett [Time Series Insights webes bemutatót](https://insights.timeseries.azure.com/clientsample)használ.
> * A Time Series Insights minta alkalmazás forrásfájljait a [GitHub-minta adattárában](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)kell megadnia.
> * Olvassa el a [Time Series Insights-ügyfél dokumentációját](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

Ha még nem rendelkezik ilyennel, regisztráljon egy [ingyenes Azure-előfizetésre](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag a böngésző **fejlesztői eszközök** funkcióját használja. A modern webböngészők ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)és mások) általában hozzáférést biztosítanak a **webfelügyelői nézethez** a billentyűzeten található F12 gyorsbillentyűn keresztül. A nézet elérésének másik módja, ha a jobb gombbal egy weblapra kattint, majd kiválasztja az **elem vizsgálata elemet**.

## <a name="review-video"></a>Videó áttekintése

Ebben a videóban bemutatjuk a nyílt forráskódú Time Series Insights JavaScript SDK-t:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="sample-application"></a>Mintaalkalmazás

Ebben az oktatóanyagban egy ingyenes, üzemeltetett Time Series Insights minta alkalmazást használunk az alkalmazás mögötti forráskód megismeréséhez és a Time Series Insights JavaScript-ügyféloldali kódtár megismeréséhez. A minta alkalmazás használatával megtudhatja, hogyan használhatja a Time Series Insightst a JavaScriptben, és hogyan jelenítheti meg az adatdiagramokat és diagramokat.

1. Nyissa meg a [Time Series Insights minta alkalmazást](https://insights.timeseries.azure.com/clientsample). A következő bejelentkezési üzenet jelenik meg:

   [@no__t – 1Time adatsorozat-bepillantást kérő ügyfél-minta bejelentkezési kérés](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Válassza a **Bejelentkezés** lehetőséget a hitelesítő adatok megadásához vagy kiválasztásához. Használjon vállalati szervezeti fiókot (Azure Active Directory) vagy egy személyes fiókot (Microsoft-fiók).

   [@no__t – 1Time adatsorozat-információk ügyfél-minta hitelesítő adatainak kérése](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Miután bejelentkezett, megjelenik egy oldal, amelyen Time Series Insights adatokkal feltöltött diagramok jelennek meg. A felhasználói fiók és a **Kijelentkezés** hivatkozás a jobb felső sarokban láthatók:

   [@no__t – 1Time adatsorozat-áttekintési ügyfél minta főoldala bejelentkezés után](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Oldal forrása és struktúrája

Először tekintse meg a megjelenített weblap [HTML-és JavaScript-forráskódját](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) :

1. Nyissa meg a böngészőben a **Fejlesztői eszközök** lapot. Vizsgálja meg az aktuális oldalt alkotó HTML-elemeket, más néven a HTML- vagy DOM-faszerkezetet.

1. Bontsa ki a `<head>` és a `<body>` elemet, és figyelje meg a következő részeket:

   * A `<head>` elem alatt megtalálhatja az oldal metaadatait és függőségeit, amelyek lehetővé teszik az alkalmazás futtatását:
     * @No__t-0 elem, amely a Azure Active Directory Authentication Library (ADAL) *ADAL. min. js*fájlra való hivatkozásához használatos. Az ADAL egy JavaScript-kódtár, amely OAuth 2.0 hitelesítést (bejelentkezést) és az API-k hozzáféréséhez jogkivonat beszerzését biztosítja.
     * Több `<link>` elem a stíluslapok (más néven *CSS*) számára, például a *sampleStyles. css* és a *tsiclient. css*. A stíluslapok a vizuális oldal stílusának részletes adatait, például a színeket, a betűkészleteket és a térközt vezérlik.
     * @No__t-0 elem, amely a Time Series Insights JavaScript ügyféloldali *tsiclient. js*fájlra hivatkozik. A lap a könyvtárat használja a Time Series Insights Service API-k meghívásához és a diagram vezérlőinek megjelenítéséhez a lapon.

     >[!NOTE]
     > * Az ADAL JavaScript-kódtár forráskódja az [Azure-ActiveDirectory-Library-for-js tárházban](https://github.com/AzureAD/azure-activedirectory-library-for-js)érhető el.
     > * A Time Series Insights JavaScript-ügyféloldali kódtár forráskódja elérhető a [tsiclient-tárházban](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * A `<body>` elem alatt `<div>` elemet talál, amelyek segítenek meghatározni az oldalon lévő elemek elrendezését, és egy másik @no__t 2 elemet:
     * Az első `<div>` elem megadja a **Bejelentkezés** párbeszédpanelt (`id="loginModal"`).
     * A második `<div>` elem a következő szülőjeként működik:
       * Egy `<div>` fejléc elem, amely állapotüzenetekre és bejelentkezési információkra szolgál az oldal tetején (`class="header"`).
       * @No__t-0 elem az oldal törzse elemeinek hátralévő részéhez, beleértve a diagramokat (`class="chartsWrapper"`).
       * @No__t-0 szakasz, amely tartalmazza az oldal vezérléséhez használt JavaScriptet.

   [@no__t – 1Time sorozat-bepillantást biztosító ügyféloldali minta Fejlesztői eszközök](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Bontsa ki a `<div class="chartsWrapper">` elemet, és további gyermek `<div>` elemet talál. Ezekkel helyezhetők el az egyes diagramvezérlő példák. Több pár `<div>` elem létezik, egyet az egyes diagramokra:

   * Az első (`class="rowOfCardsTitle"`) elem egy leíró címet tartalmaz, amely összegzi a diagramok által bemutatott elemeket. Például:`Static Line Charts With Full-Size Legends.`
   * A második (`class="rowOfCards"`) elem egy olyan szülő, amely további gyermek `<div>` elemet tartalmaz, amelyek egy sorban lévő tényleges diagram vezérlőelemeket helyezik át.

   [@no__t – 1Body div elemek](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Bontsa ki a `<script type="text/javascript">` elemet, amely közvetlenül a `<div class="chartsWrapper">` elem alatt van. Az oldal szintű JavaScript szakasz elejétől az összes oldal logikája (hitelesítés, Time Series Insights szolgáltatás API-k meghívása, a diagram vezérlőelemek megjelenítése stb.) használható:

   [@no__t – 1Body parancsfájl](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="javascript-client-library"></a>JavaScript ügyféloldali kódtár

Az Time Series Insights ügyféloldali kódtár (*tsiclient. js*) a következő két fontos JavaScript-funkció absztrakcióját biztosítja:

* **A Time Series Insights lekérdezési API-k meghívására szolgáló burkoló metódusok**: REST API-k összesítő kifejezések használatával Time Series Insights adatok lekérdezésére használható. A metódusok a könyvtár TsiClient. Server névterében vannak rendszerezve.

* **Különféle típusú diagramok létrehozásának és feltöltésének módszerei**: A weblapon lévő Time Series Insights összesített adatainak megjelenítéséhez használható metódusok. A metódusok a könyvtár TsiClient. UX névterében vannak rendszerezve.

Ezen egyszerűsítések révén a fejlesztők könnyebben hozhatnak létre felhasználói felületi gráfokat és diagram-összetevőket, amelyek Time Series Insights-adatszolgáltatással rendelkeznek.

### <a name="authentication"></a>Authentication

A [Time Series Insights minta alkalmazás](https://insights.timeseries.azure.com/clientsample) egy egyoldalas alkalmazás, amely ADAL OAuth 2,0 felhasználói hitelesítési támogatást nyújt:

1. Ha ADAL használ a hitelesítéshez, az ügyfélalkalmazás regisztrálni kell Azure Active Directory (Azure AD) szolgáltatásban. Valójában az egyoldalas alkalmazás regisztrálva van a [OAuth 2,0 implicit engedélyezési folyamat](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow)használatára.
1. Az alkalmazásnak bizonyos regisztrációs tulajdonságokat kell megadnia futásidőben. A tulajdonságok közé tartozik az ügyfél GUID-ja (`clientId`) és az átirányítási URI (`postLogoutRedirectUri`).
1. Az alkalmazás később *hozzáférési jogkivonatot* kér az Azure ad-től. A hozzáférési jogkivonat egy adott szolgáltatás vagy API-azonosító (https: \//API. idősor. Azure. com) engedélyeinek egy véges készletére van kiállítva. A jogkivonat engedélyei a bejelentkezett felhasználó nevében vannak kiadva. A szolgáltatás vagy API azonosítója egy másik olyan tulajdonság, amely az alkalmazás Azure AD-regisztrációjában szerepel.
1. Miután a ADAL visszaadja a hozzáférési jogkivonatot az alkalmazásnak, *tulajdonosi jogkivonatnak* minősül, amikor hozzáfér a Time Series Insights Service API-khoz.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> A Microsoft által támogatott Azure AD-alapú hitelesítési könyvtárakkal kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory hitelesítési függvénytár dokumentációját](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>A vezérlők azonosítása

A megadott példában `<div>` elemek vannak elrendezve a szülő `<body>` elemben, hogy ésszerű elrendezést biztosítson az oldalon megjelenített diagram vezérlőelemekhez.

Minden `<div>` elem a diagram vezérlőelem elhelyezésére és vizuális attribútumaira vonatkozó tulajdonságokat adja meg. HTML-elem @no__t – 0 a tulajdonságok egyedi azonosítóként szolgálnak a vizualizációs adat megjelenítéséhez és frissítéséhez meghatározott vezérlőkhöz való kötéshez.

### <a name="aggregate-expressions"></a>Összesítő kifejezések

Az Time Series Insights ügyféloldali függvénytár API-k összesítő kifejezéseket használnak:

* Az összesítő kifejezés lehetővé teszi egy vagy több *keresési kifejezés*összeállítását.

* Az ügyféloldali API-k úgy lettek kialakítva, hogy hasonló funkciókat nyújtsanak egy másik bemutató alkalmazáshoz (a [Time Series Insights Explorer](https://insights.timeseries.azure.com/demo)), amely a keresési span, a `where` predikátumok, a mértékek és a @no__t 2 értékeket használja.

* Az ügyféloldali függvénytár-API-k többsége olyan összesítő kifejezéseket használ, amelyeket a szolgáltatás Time Series Insights adatlekérdezés létrehozásához használ.

### <a name="call-pattern"></a>Hívásmintázat

A diagram-vezérlőelemek feltöltése és renderelése általános mintát követ. Megfigyelheti az általános mintát a minta alkalmazásban, és az ügyféloldali kódtár használatakor segítséget nyújthat:

1. @No__t-0 deklarálása egy vagy több Time Series Insights összesítő kifejezés megtartásához:

   ```javascript
   var aes =  [];
   ```

1. Hozzon létre *1* – *n* összesítő kifejezés objektumot. Ezután adja hozzá őket az összesítő kifejezés tömbhöz:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | `predicateObject` | Az adatszűrési kifejezés |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | A használt mérték neve | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | A kívánt mérték tulajdonság összesítései | `['avg', 'min']` |
   | `searchSpan`      | Az összesítő kifejezés időtartamának és intervallumának mérete | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | A feldarabolni kívánt karakterlánc-tulajdonság (opcionális: NULL is lehet) | `{property: 'Station', type: 'String'}` |
   | `color`         | A megjeleníteni kívánt objektumok színe | `'pink'` |
   | `alias`           | Az összesítő kifejezés rövid neve | `'Factory3Temperature'` |
   | `contextMenuActions` | Egy vizualizációban az idősorozat-objektumokhoz kötni kívánt műveletek tömbje (nem kötelező) | További információ: [előugró helyi menük](#pop-up-context-menus). |

1. Hívja meg a Time Series Insights lekérdezést a TsiClient. Server API-k használatával az összesített adatokat az igényléshez:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | `token`     | A Time Series Insights API hozzáférési jogkivonata |  `authContext.getTsiToken()`<br />További információért lásd: [Hitelesítés](#authentication). |
   | `envFQDN`   | A Time Series Insights környezet teljes tartományneve (FQDN) | A Azure Portal. Például: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Time Series Insights lekérdezési kifejezések tömbje | Használja az `aes` változót a korábban leírtak szerint: `aes.map(function(ae){return ae.toTsx()}`. |

1. Alakítsa át a Time Series Insights lekérdezésből a JSON formátumba visszaadott tömörített eredményt a következő vizualizációra:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Hozzon létre egy diagram vezérlőelemet a TsiClient. UX API-k használatával. Kösse hozzá az oldalon található `<div>` elemek egyikéhez:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Töltse fel a diagram vezérlőelemet az átalakított JSON-adatobjektumokkal, és jelenítse meg a vezérlőt az oldalon:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Renderelési vezérlők

Az Time Series Insights ügyféloldali kódtár nyolc egyedi, beépített analitikai vezérlőt biztosít:

* **vonalas diagram**
* **tortadiagram**
* **oszlopdiagram**
* **heatmap**
* **hierarchia-vezérlők**
* **elérhető rács**
* **diszkrét események ütemezései**
* **állapot-átmeneti ütemtervek**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>A diagram, a oszlopdiagram és a tortadiagram példái

Tekintse meg a standard diagram egyes vezérlőinek megjelenítéséhez használt bemutató kódját. Jegyezze fel a programozási modellt és a vezérlők létrehozási mintáit. Pontosabban vizsgálja meg a `// Example 3/4/5` megjegyzésben szereplő HTML-kódot, amely a HTML `id` értékekkel (`chart3`, `chart4` és `chart5`) jeleníti meg a vezérlőket.

Visszahívás az [oldal forrása és szerkezete szakasz](#page-source-and-structure) 3. lépésében, hogy a diagram vezérlőelemei sorokba vannak rendezve az oldalon. Mindegyik diagram vezérlőelemhez tartozik egy leíró title sor. Ebben a példában a három diagram a `Multiple Chart Types From the Same Data` cím `<div>` elem alatt van feltöltve, és a következő három, @no__t 2 elemre van kötve, amelyek a cím alatt vannak:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A JavaScript-kód következő szakasza a korábban ismertetett mintákat használja: Time Series Insights összesített kifejezések összevonása, a Time Series Insights-adatokat lekérdező lekérdezések, majd a három diagram renderelése. Három diagramtípust használ a tsiClient. UX névtérből: `LineChart`, `BarChart` és `PieChart`. A diagramok a megfelelő diagramok létrehozásához és megjelenítéséhez használatosak. Mindhárom diagram használhatja ugyanazt az összesítő kifejezést `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

A három diagram a következőképpen jelenik meg a rendereléskor:

[![Több diagramtípus ugyanazokból az adatokból](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="learn-about-advanced-features"></a>További tudnivalók a speciális funkciókról

Az Time Series Insights ügyféloldali kódtár számos további funkcióval rendelkezik, amelyekkel kreatív módon implementálhatja az adatvizualizációkat.

### <a name="states-and-events"></a>Állapotok és események

A speciális funkciók lehetővé teszi az állapot-és a diszkrét események hozzáadását a diagramokhoz. Ez a funkció hasznos lehet az incidensek, a riasztások és az állapot-kapcsolók (például a be-és kikapcsolási kapcsolók) kiemeléséhez.

Tekintse meg a `// Example 10` Megjegyzés körüli kódot. A kód a `Line Charts with Multiple Series Types` cím alá tartozó vonal vezérlőelemet jeleníti meg, és a `<div>` elemhez köti azt a HTML `id` értékkel `chart10`.

A következő lépések ismertetik a folyamatot:

1. @No__t-0 nevű struktúra van definiálva a nyomon követett állapot-változási elemek tárolására. A struktúra a következőket tartalmazza:

   * Egy `Component States` nevű sztringkulcs.
   * Az állapotokat jelző értékobjektumok tömbje. Mindegyik objektum a következőket tartalmazza:
     * Egy JavaScript ISO időbélyeget tartalmazó sztringkulcs.
     * Az állapot jellemzőit (szín, leírás) tartalmazó tömb.

1. A `events5` struktúra a `Incidents` értékhez van definiálva, amely a nyomon követett esemény-elemek tömbjét tartalmazza. A tömb struktúrája ugyanolyan alakú, mint az `events4` elemhez leírt struktúra.

1. A diagramot a rendszer a két struktúrában jeleníti meg és adja át a diagram beállításai paraméterrel: `events:` és `states:`. Vegye figyelembe a többi paramétert `tooltip:`, `theme:` vagy `grid:` érték megadásához.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Az incidenseket és a színes sávokat/előugró ablakokat jelölő rombuszok/előugró ablakok az állapot változásaira utalnak:

[@no__t 1Line-diagramok több adatsorozat-típussal](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Előugró helyi menük

Egy másik speciális funkció az egyéni helyi menük létrehozása (a jobb gombbal az előugró menüket). Ezekkel engedélyezhetők különböző műveletek és a következő logikus lépések az alkalmazás hatókörén belül.

Tekintse meg a kódot a `// Example 13/14/15` megjegyzésben. Ez a kód kezdetben a `Line Chart with Context Menu to Create Pie/Bar Chart` cím alá tartozó diagramot jeleníti meg. A diagram a `<div>` elemhez van kötve, a HTML `id` érték `chart13`.

A helyi menükkel a vonaldiagram lehetővé teszi torta- és sávdiagramok dinamikus létrehozását, a `chart14` és `chart15` azonosítókkal rendelkező `<div>` elemekhez kötve azokat. A tortadiagram és a sávdiagram is a helyi menüket használja a saját szolgáltatásainak engedélyezéséhez: lehetővé teszi az adatok másolását a tortadiagramról a oszlopdiagramon, valamint a sávdiagram adatainak a böngészőablakba való kinyomtatását.

A következő lépések ismertetik a folyamatot:

1. Egyéni műveletek sorozata van definiálva. Mindegyik művelet egy egy vagy több elemből álló tömböt tartalmaz. Mindegyik elem egyetlen helyimenü-elemet határoz meg:

   * `barChartActions`: Ez a művelet meghatározza a tortadiagram helyi menüjét, amely egyetlen elemet tartalmaz egyetlen elem definiálásához:
     * `name`: A menüelemhez használt szöveg: "Paraméterek kinyomtatása a konzolon"
     * `action`: A menüelemhez társított művelet. Ez a művelet mindig egy névtelen funkció, amely három argumentumot vesz fel a diagram létrehozásához használt összesítő kifejezés alapján. Ebben az esetben az argumentumok a böngésző konzolablakába vannak írva:
       * `ae`: Az összesítő kifejezés tömbje.
       * `splitBy`: A `splitBy` érték.
       * `timestamp`: Az időbélyeg.

   * `pieChartActions`: Ez a művelet meghatározza a sávdiagram helyi menüjét, amely egyetlen elemet tartalmaz egyetlen elem definiálásához. Az alakzat és a séma megegyezik a korábban ismertetett `barChartActions` elemmel, de a `action` függvény drámaian eltér: a rendszer létrehozza és megjeleníti a sávdiagram diagramját. A `ae` argumentummal megadható az összesítő kifejezés tömbje, amelyet a rendszer futásidőben átadott a menüelem megnyitásakor. A funkció az `ae.contextMenu` tulajdonságot is beállítja a `barChartActions` helyi menüvel.
   * `contextMenuActions`: Ez a művelet a vonalas diagram helyi menüjét határozza meg, amely három elemet tartalmaz három menüelem definiálásához. Az egyes elemekhez tartozó alakzat és séma ugyanaz, mint a korábban leírt elemek. Akárcsak a `barChartActions` elem, az első elem a böngésző konzolablakába írja a három funkcióargumentumot. A `pieChartActions` elemhez hasonlóan a második két elem is a tortadiagram és a oszlopdiagram létrehozását és megjelenítését teszi lehetővé. A második két elem az `ae.contextMenu` tulajdonságukat is beállítja a `pieChartActions` és `barChartActions` helyi menükkel.

1. Két összesítő kifejezés van leküldve a `aes` összesítő kifejezés tömbbe. Mindegyik elemhez megadja a `contextMenuActions` tömböt. Ezeket a kifejezéseket a rendszer a vonaldiagram vezérlőjével használja.

1. A rendszer csak a diagramot jeleníti meg először, amelyből a tortadiagram és a oszlopdiagram is megjeleníthető futásidőben.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Az alábbi képernyőfelvételen a megfelelő előugró helyi menüket tartalmazó diagramok láthatók. A tortadiagram és a oszlopdiagram dinamikusan lett létrehozva a diagram helyi menüjének használatával.

[@no__t – 1Line diagram helyi menüvel kördiagram és sávdiagram létrehozásához](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Ecsetek

Használhat ecseteket az időtartomány hatókörének meghatározására olyan műveletek definiálásához, mint a nagyítás és a felfedezés.

Az ecsetek szemléltetéséhez használt kód a `Line Chart with Context Menu to Create Pie/Bar Chart` példában látható, amely leírja az előugró helyi menüket.

* Az ecsetműveletek abból a szempontból hasonlítanak a helyi menükhöz, hogy egy egyéni műveletsorozatot határoznak meg az ecsethez. Minden művelet egy tömböt tartalmaz, amelynek egy vagy több eleme van. Mindegyik elem egyetlen helyimenü-elemet határoz meg:
   * `name`: A menüelemhez használt szöveg: "Paraméterek kinyomtatása a konzolon"
   * `action`: A menüelemhez társított művelet, amely mindig egy névtelen függvény, amely két argumentumot vesz igénybe. Ebben az esetben az argumentumok a böngésző konzolablakába vannak írva:
     * `fromTime`: Az ecset kijelölésének @no__t – 0 időbélyeg
     * `toTime`: Az ecset kijelölésének @no__t – 0 időbélyeg

* Az ecsetműveletek egy másik diagrambeállítási tulajdonságként vannak hozzáadva. A `brushContextMenuActions: brushActions` tulajdonságot a `linechart.Render` hívás adja át.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Line diagram helyi menüvel tortadiagram és sávdiagram létrehozásához ecsetek használatával](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy elvégezte az oktatóanyagot, törölje a létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**lehetőséget, keresse meg a Azure Time Series Insights erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és az abban található összes erőforrást) úgy, hogy kiválasztja az egyes erőforrások **törlését** vagy eltávolítását.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Jelentkezzen be, és ismerkedjen meg a Time Series Insights minta alkalmazással és annak forrásával
> * API-k használata a Time Series Insights JavaScript ügyféloldali kódtáraban
> * A JavaScript használatával diagram vezérlőelemeket hozhat létre és tölthet fel Time Series Insights adatokkal

A Time Series Insights minta alkalmazás bemutató adatkészletet használ. Ha szeretné megismerni, hogyan hozhatja létre saját Time Series Insights környezetét és adatkészletét, olvassa el a következő cikket:

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Time Series Insights-környezet létrehozása @ no__t-0

Vagy tekintse meg a Time Series Insights minta alkalmazás forrásfájljait:

> [!div class="nextstepaction"]
> [Time Series Insights minta alkalmazás-adattár](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Olvassa el a Time Series Insights Client API-dokumentációját:

> [!div class="nextstepaction"]
> [Time Series Insights API-referenciák dokumentációja](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
