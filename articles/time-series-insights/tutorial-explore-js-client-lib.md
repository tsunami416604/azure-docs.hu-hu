---
title: Az Azure Time Series Insights JavaScript ügyfélkódtár felderítése
description: Megismerheti az Azure Time Series Insights JavaScript ügyfélkódtárat és a kapcsolódó programozási modellt.
author: ashannon7
manager: timlt
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: bryanla
ms.openlocfilehash: 70e29b1a6b8a4443ae6545ec7960f1d2370218e3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36295392"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Oktatóanyag: Az Azure Time Series Insights JavaScript ügyfélkódtár felderítése

Hogy megkönnyítsük a webfejlesztőknek a Time Series Insights (TSI) szolgáltatásban tárolt adatok lekérdezését és megjelenítését, kifejlesztettünk egy JavaScript D3-alapú TSI-ügyfélkódtárat.  Ez az oktatóanyag egy minta webalkalmazás használatával vezeti végig a TSI ügyfélkódtáron és a kapcsolódó programozási modellen.

Az oktatóanyag témakörei lehetőségeket nyújtanak a kódtárral való kísérletezésre, a TSI-adatok elérésének megismerésére, illetve a diagram-vezérlőelemek adatrenderelésre és -megjelenítésre való használatára. A cél az, hogy elég részletet nyújtsunk ahhoz, hogy a saját webalkalmazásában használja a kódtárat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A TSI mintaalkalmazás.
> * A TSI JavaScript ügyfélkódtár.
> * Hogyan használja a mintaalkalmazás a kódtárat a TSI-adatok megjelenítésére.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag a „Fejlesztői eszközök” (más néven DevTools vagy F12) funkciót használja, amely megtalálható a legtöbb modern webböngészőben, például az [Edge](/microsoft-edge/devtools-guide), a [Chrome](https://developers.google.com/web/tools/chrome-devtools/), a [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), a [Safari](https://developer.apple.com/safari/tools/) stb. böngészőkben. Ha még nem ismeri ezt a funkciót, érdemes lehet megismerni a böngészőben a folytatás előtt.

## <a name="time-series-insights-sample-application"></a>A Time Series Insights mintaalkalmazás

Ebben az oktatóanyagban a Time Series Insights mintaalkalmazás használatával ismerjük meg az alkalmazás mögötti forráskódot, beleértve a TSI JavaScript ügyfélkódtár használatát is. A minta egy egyoldalas webalkalmazás (SPA), amely a kódtár használatát mutatja be. A minta szemlélteti, hogyan lehet adatokat lekérni és megjeleníteni egy minta TSI-környezetből.

1. Keresse meg a [Time Series Insights mintaalkalmazást](https://insights.timeseries.azure.com/clientsample). A következőhöz hasonló oldal jelenik meg, rajta egy bejelentkezési kéréssel:

   ![TSI-ügyfélminta bejelentkezési kérése](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Kattintson a **Bejelentkezés** gombra, és írja be vagy válassza ki a hitelesítő adatait. Vállalati/céges fiókot (Azure Active Directory) vagy személyes fiókot (Microsoft-fiók vagy MSA) is használhat.

   ![TSI-ügyfélminta hitelesítőadat-kérése](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. A sikeres bejelentkezés után a következőhöz hasonló oldal jelenik meg. Ez az oldal TSI-adatokkal kitöltött, különböző stílusú mintadiagramokat tartalmaz. A felhasználói fiók és a **Kijelentkezés** hivatkozás a jobb felső sarokban láthatók:

   ![TSI-ügyfélminta főoldal a bejelentkezés után](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Oldal forrása és struktúrája

Először tekintsük meg a böngészőben renderelt oldal mögött lévő HTML és JavaScript forráskódot. Nem ismertetjük mindegyik elemet, de megismerheti a főbb szakaszokat, így képet kaphat az oldal működéséről:

1. Nyissa meg a böngészőben a **Fejlesztői eszközök** lapot. Vizsgálja meg az aktuális oldalt alkotó HTML-elemeket, más néven a HTML- vagy DOM-faszerkezetet.

2. Bontsa ki a `<head>` és `<body>` elemeket, és figyelje meg a következő szakaszokat:

   - A `<head>` elem alatt keresse meg az oldal működésének elősegítése érdekében további fájlokat lekérő elemeket:
     - Az Azure Active Directory Authentication Library (**adal.min.js**) kódtárra (más néven ADAL) hivatkozó `<script>` elem. Az ADAL egy JavaScript-kódtár, amely OAuth 2.0 hitelesítést (bejelentkezést) és az API-k hozzáféréséhez jogkivonat beszerzését biztosítja.
     - Több `<link>` elem a stíluslapokhoz, más néven CSS-ekhez, például a **sampleStyles.css**-hez és a **tsiclient.css**-hez. A stíluslapokkal szabályozhatók a vizuális oldalstilizálás részletei, például a színek, a betűtípusok, a térköz stb.
     - A TSI JavaScript-ügyfélkódtárára (**tsiclient.js**) hivatkozó `<script>` elem. Ezt a kódtárt az oldal a TSI-szolgáltatási API-k behívására és az oldalon lévő diagramvezérlők renderelésére használja.

     >[!NOTE]
     > Az ADAL JavaScript-kódtár forráskódja az [azure-activedirectory-library-for-js adattárban](https://github.com/AzureAD/azure-activedirectory-library-for-js) érhető el.
     > A TSI-ügyfél JavaScript-kódtárának forráskódja a [tsiclient adattárban](https://github.com/Microsoft/tsiclient) érhető el.

   - A `<body>` elem alatt `<div>` elemeket talál, amelyek tárolókként szolgálnak az oldalon lévő elemek elrendezésének meghatározásához, és egy másik `<script>` elemet:
     - Az első `<div>` elem a **Bejelentkezés** párbeszédpanelt határozza meg (`id="loginModal"`).
     - A második `<div>` elem a következő szülőjeként működik:
       - Egy `<div>` fejléc elem, amely állapotüzenetekre és bejelentkezési információkra szolgál az oldal tetején (`class="header"`).
       - Egy `<div>` elem az oldal törzsének többi részéhez, beleértve az összes diagramot is (`class="chartsWrapper"`).
       - Egy `<script>` szakasz, amely tartalmazza az oldal vezérlésére használt összes JavaScript elemet.

   [![TSI-ügyfélminta a fejlesztői eszközök használatával](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Bontsa ki a `<div class="chartsWrapper">` elemet, és további alárendelt `<div>` elemeket talál. Ezekkel helyezhetők el az egyes diagramvezérlő példák. Figyelje meg, hogy néhány `<div>` elempár látható, egy-egy mindegyik diagrampéldához:

   - Az első (`class="rowOfCardsTitle"`) elem egy leíró címet tartalmaz, amely összegzi a diagramok által bemutatott elemeket. Például: „Statikus vonaldiagramok teljes méretű jelmagyarázatokkal”.
   - A második (`class="rowOfCards"`) elem egy szülő, amely további alárendelt `<div>` elemeket tartalmaz, amelyek elhelyezik a sorokon belüli tényleges diagramvezérlő(ke)t.

   ![Törzs div elemei](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Most bontsa ki a `<script type="text/javascript">` elemet, amely közvetlenül a `<div class="chartsWrapper">` elem alatt található. Figyelje meg az oldal szintű JavaScript-szakasz elejét, amellyel az oldal teljes logikája kezelhető: a hitelesítés, a TSI-szolgáltatási API-k behívása, a diagramvezérlők renderelése és egyebek:

   ![Törzs szkript](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>A TSI JavaScript ügyfélkódtár fogalmai

Bár nem tekintjük át részletesen, a TSI-ügyfélkódtár (**tsclient.js**) alapvetően két fontos kategória absztrakcióját nyújtja:

- **A TSI-lekérdezési API-k hívására szolgáló burkoló módszerek**: Olyan REST API-k, amelyek lehetővé teszik a TSI-adatok lekérdezését összesítő kifejezésekkel. A módszerek a kódtár `TsiClient.Server` névterében vannak rendezve.
- **A diagramvezérlők néhány típusának létrehozására és kitöltésére szolgáló módszerek**: A weboldalon lévő összesített TSI-adatok renderelésére szolgálnak. A módszerek a kódtár `TsiClient.UX` névterében vannak rendezve.

A következő alapelvek univerzálisak, és általában érvényesek a TSI-ügyfélkódtár API-kra.

### <a name="authentication"></a>Hitelesítés

Ahogy korábban szerepelt, ez a minta egy egyoldalas alkalmazás (SPA), amely az OAuth 2.0 támogatást használja az ADAL-ban a felhasználói hitelesítéshez. Itt láthatja a szkript ezen szakaszának néhány fontos elemét:

1. Az ADAL hitelesítéshez való használatához az ügyfélalkalmazásnak regisztrálnia kell magát az Azure Active Directory (Azure AD) alkalmazásregisztrációs szolgáltatásában. SPA-ként ez az alkalmazás az „implicit” OAuth 2.0 engedélyezési folyamat használatához van regisztrálva. Ennek megfelelően az alkalmazás a futásidőben ad meg néhány regisztrációs tulajdonságot, például az ügyfél GUID azonosítóját (`clientId`) és az átirányítási URI-címét (`postLogoutRedirectUri`), hogy részt vegyen a folyamatban.

2. Később az alkalmazás „hozzáférési jogkivonatot” kér az Azure AD-től. A hozzáférési jogkivonat az engedélyek véges készletéhez van kiadva, egy adott szolgáltatás-/API-azonosítóhoz (https://api.timeseries.azure.com). Ez a szolgáltatás-/API-azonosító más néven a jogkivonat „célközönsége”. A jogkivonat engedélyei a bejelentkezett felhasználó nevében vannak kiadva. A szolgáltatás-/API-azonosító az alkalmazás Azure AD regisztrációjában szereplő egy másik tulajdonság. Miután az ADAL visszaadja a hozzáférési jogkivonatot az alkalmazásnak, az „tulajdonosi jogkivonatként” lesz átadva a TSI-szolgáltatási API-k elérésekor.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>A vezérlők azonosítása

A fentiekben taglaltak szerint a `<body>` `<div>` elemei biztosítják az oldalon bemutatott összes diagramvezérlő elrendezését. Mindegyik `id` elem a diagramvezérlő elhelyezésének és vizuális attribútumainak tulajdonságait határozza meg, beleértve egy `<div>` tulajdonságot is. Az `id` tulajdonság egy egyedi azonosítót biztosít, amellyel a JavaScript-kódban azonosíthatók és köthetők a vezérlők a rendereléshez és frissítéshez.

### <a name="aggregate-expressions"></a>Összesítő kifejezések

A TSI-ügyfélkódtár API-k erősen támaszkodnak az összesítő kifejezések használatára. Az összesítő kifejezések egy vagy több „keresési kifejezés” összeállítására nyújtanak lehetőséget. Az API-k hasonlóak ahhoz, ahogyan a [Time Series Insights Explorer](https://insights.timeseries.azure.com/demo) használja a keresés hatókörét, a hol állítást, a mértékeket és a felosztás alapját képező értéket. A legtöbb kódtár API az összesítő kifejezések tömbjét veszi, amellyel a szolgáltatás TSI-adatlekérdezéseket épít fel.

### <a name="call-pattern"></a>Hívásmintázat

A diagramvezérlők kitöltése és renderelése általános mintázatot követ. Láthatja, hogy a rendszer ezt a mintázatot használja az oldal összes JavaScript-kódjában, ami példányosítja és betölti a minta TSI-alkalmazás vezérlőit:

1. Deklaráljon egy `array` elemet ahhoz, hogy egy vagy több TSI összesítő kifejezést tartalmazzon:

   ```javascript
   var aes =  [];
   ```

2. Építsen fel 1–n összesítő kifejezés objektumot, és adja azokat az összesítő kifejezés tömbjéhez:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | `predicateObject` | Az adatszűrő kifejezés. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | A használt mérték tulajdonságneve. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | A mértéktulajdonság kívánt összesítései. | `['avg', 'min']` |
   | `searchSpan`      | Az összesítő kifejezés időtartama és az intervallum mérete. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Azon sztringtulajdonság, amely alapján fel szeretne osztani (opcionális – null értékű is lehet). | `{property: 'Station', type: 'String'}` |
   | `color`         | A renderelni kívánt objektumok színe. | `'pink'` |
   | `alias`           | Az összesítő kifejezés rövid neve. | `'Factory3Temperature'` |
   | `contextMenuActions` | Egy vizualizációban a Time Series-objektumokhoz kötni kívánt műveletek tömbje. | További tájékoztatásért lásd: [Előugró helyi menük a Speciális funkciók szakaszban](#popup-context-menus). |

3. Hívjon be egy TSI-lekérdezést a `TsiClient.Server` API-k használatával az összesítő adatok lekéréséhez:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | `token`     | A TSI API hozzáférési jogkivonata. |  `authContext.getTsiToken()` További információkért lásd a [hitelesítéssel foglalkozó szakaszt](#authentication). |
   | `envFQDN`   | Adja meg a TSI-környezet teljes tartománynevét (FQDN). | Az Azure Portalon például: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | TSI-lekérdezési kifejezések tömbje. | A korábban leírt `aes` változót használja: `aes.map(function(ae){return ae.toTsx()}`. |

4. Alakítsa át a TSI-lekérdezésből visszaadott tömörített eredményt JSON formátumba a vizualizációhoz:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Hozzon létre egy diagramvezérlőt a `TsiClient.UX` API-k használatával, majd kösse azt az oldal egyik `<div>` eleméhez:

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Töltse ki a diagramvezérlőt az átalakított JSON-adatobjektummal/-objektumokkal, és renderelje a vezérlőt az oldalon:

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Vezérlők renderelése

A TSI-ügyfélkódtár jelenleg nyolc egyedi elemzésvezérlőt tesz elérhetővé. Ezek közé tartozik egy vonaldiagram, egy tortadiagram, egy sávdiagram, egy hőtérkép, a hierarchiavezérlők, egy hozzáférhető rács, különálló esemény idővonalak és állapotváltás idővonalak.

### <a name="line-bar-pie-chart-examples"></a>Vonal-, sáv-, tortadiagram példái

Tekintse meg az alkalmazásban bemutatott standard diagramvezérlők mögötti kódot és az azok létrehozására szolgáló programozási modellt/mintákat. Pontosabban, vizsgálja meg az `// Example 3/4/5` megjegyzés alatt lévő HTML szakaszt, amely a `chart3`, `chart4` és `chart5` azonosítóértékekkel rendelkező vezérlőket rendereli.

Az [Oldal forrása és struktúrája szakasz](#page-source-and-structure) 3. lépésében már látta, hogy a diagramvezérlők sorokba vannak rendezve az oldalon, amelyek mindegyike rendelkezik egy-egy leíró címsorral. Ebben a példában a három kitöltött diagram a „Multiple Chart Types From the Same Data” (Több diagramtípus ugyanazokból az adatokból) cím `<div>` elem alatt szerepel, és a cím alatt található három `<div>` elemhez vannak kötve:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A JavaScript-kód következő szakasza a korábban leírt mintázatokat használja: összesítő TSI-kifejezések felépítése, azok használata TSI-adatok lekérdezéséhez, illetve a három diagram renderelése. Figyelje meg a `tsiClient.ux` névtérből a megfelelő diagramok létrehozásához és rendereléséhez használt három típust (`LineChart`, `BarChart`, `PieChart`). Azt is figyelje meg, hogy mindhárom diagram ugyanazokat az összesítőkifejezés-adatokat tudja használni (`transformedResult`):

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

A három diagram a következőképpen jelenik meg a rendereléskor:

[![Több diagramtípus ugyanazokból az adatokból](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Speciális funkciók

A TSI-ügyfélkódtár néhány további speciális funkciót is elérhetővé tesz, amelyeket érdemes lehet kihasználni.

### <a name="states-and-events"></a>Állapotok és események

Az elérhetővé tett speciális funkciók egy példája, hogy állapotváltásokat és különálló eseményeket adhat a diagramokhoz. Ez a funkció az incidensek kiemeléséhez, a riasztáshoz és az állapotkapcsolók be-/kikapcsolásához hasznos.

Tekintse meg az `// Example 10` megjegyzés alatt lévő HTML-szakasz mögötti kódot. A kód egy sorvezérlőt renderel a „Line Charts with Multiple Series Types” (Vonaldiagramok több sorozattípussal) cím alatt, és a `chart10` azonosítóértékkel rendelkező `<div>` elemhez köti azt.

1. Először egy `events4` nevű struktúra van definiálva, amely a nyomon követni kívánt állapotváltási elemeket tartalmazza. A struktúra a következőket tartalmazza:

   - Egy `Component States` nevű sztringkulcs.
   - Az állapotokat jelző értékobjektumok tömbje. Mindegyik objektum a következőket tartalmazza:
     - Egy JavaScript ISO időbélyeget tartalmazó sztringkulcs.
     - Az állapot jellemzőit (szín, leírás) tartalmazó tömb.

2. Ezután az `events5` struktúra lesz definiálva az „Incidents” (Incidensek) elemhez, amely a nyomon követni kívánt eseményelemek tömbjét tartalmazza. A tömb struktúrája ugyanolyan alakú, mint az `events4` elemhez leírt struktúra.

3. Végül a rendszer rendereli a vonaldiagramot, a diagram beállítási paraméterei közé helyezve a két struktúrát: `events:` és `states:`. Figyelje meg a `tooltip:`, `theme:` vagy `grid:` meghatározására szolgáló többi beállítási paramétert.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Vizuális szempontból a rombusz jelölők/előugró elemek jelölik az incidenseket, az idővonal mentén lévő színes sávok/előugró elemek pedig az állapotváltásokat:

[![Vonaldiagramok több sorozattípussal](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Előugró helyi menük

A speciális funkciók egy másik példája az egyéni helyi menük (a jobb gombbal kattintásra előugró menük). Ezekkel engedélyezhetők különböző műveletek és a következő logikus lépések az alkalmazás hatókörén belül.

Tekintse meg az `// Example 13/14/15` megjegyzés alatt lévő HTML-szakasz mögötti kódot. Ez a kód kezdetben egy vonaldiagramot renderel a „Line Chart with Context Menu to Create Pie/Bar Chart” (Vonaldiagram torta-/sávdiagram létrehozására szolgáló helyi menüvel) cím alatt, és a diagramot a `chart13` azonosítóértékkel rendelkező `<div>` elemhez köti. A helyi menükkel a vonaldiagram lehetővé teszi torta- és sávdiagramok dinamikus létrehozását, a `chart14` és `chart15` azonosítókkal rendelkező `<div>` elemekhez kötve azokat. Ezenkívül a torta- és a sávdiagramok is helyi menüket használnak a saját funkcióik engedélyezéséhez: ahhoz, hogy adatokat másoljanak a tortadiagramból a sávdiagramba, és hogy kinyomtassák a sávdiagram adatait a böngésző konzolablakában.

1. A rendszer először egyéni műveletek sorozatát definiálja. Mindegyik művelet egy egy vagy több elemből álló tömböt tartalmaz. Mindegyik elem egyetlen helyimenü-elemet határoz meg:

   - `barChartActions`: Ez a művelet a tortadiagram helyi menüjét határozza meg, amely egy egyetlen elemet definiáló elemet tartalmaz:
     - `name`: A menüelemhez használt szöveg: „Print parameters to console” (Paraméterek nyomtatása konzolra).
     - `action`: A menüelemhez tartozó művelet. Ez a művelet mindig egy névtelen funkció, amely három argumentumot vesz fel a diagram létrehozásához használt összesítő kifejezés alapján. Ebben az esetben az argumentumok a böngésző konzolablakába vannak írva:
       - `ae`: az összesítő kifejezések tömbje.
       - `splitBy`: a splitBy érték.
       - `timestamp`: az időbélyeg.

   - `pieChartActions`: Ez a művelet a sávdiagram helyi menüjét határozza meg, amely egy egyetlen elemet definiáló elemet tartalmaz. Az alakzat és a séma megegyezik az előző `barChartActions` elemmel, de figyelje meg, hogy az `action` funkció jelentősen eltér: példányosítja és rendereli a sávdiagramot. Azt is figyelje meg, hogy az `ae` argumentum használatával határozható meg az összesítő kifejezések tömbje, amelyet a rendszer a futásidőben ad át a menüelem megnyílásakor. A funkció az `ae.contextMenu` tulajdonságot is beállítja a `barChartActions` helyi menüvel.
   - `contextMenuActions`: Ez a művelet a vonaldiagram helyi menüjét határozza meg, amely a három menüelemet definiáló három elemet tartalmaz. Az egyes elemek alakja és sémája megegyezik az előzőleg leírt elemekével. Akárcsak a `barChartActions` elem, az első elem a böngésző konzolablakába írja a három funkcióargumentumot. A `pieChartActions` elemhez hasonlóan a második két elem a torta- és a sávdiagramokat példányosítja és jeleníti meg. A második két elem az `ae.contextMenu` tulajdonságukat is beállítja a `pieChartActions` és `barChartActions` helyi menükkel.

2. Ezután két összesítő kifejezés kerül az `aes` összesítőkifejezés-tömbbe, meghatározva mindegyik `contextMenuActions` elem tömbjét. Ezeket a kifejezéseket a rendszer a vonaldiagram vezérlőjével használja.

3. Végezetül a rendszer kezdetben csak a vonaldiagramot rendereli, amelyből a torta- és a sávdiagram is renderelhető a futásidőben.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

A képernyőkép a diagramokat ábrázolja a hozzájuk tartozó előugró helyi menüikkel. A torta- és sávdiagramok a vonaldiagram helyi menü lehetőségeinek használatával dinamikusan jönnek létre.

[![Vonaldiagram torta-/sávdiagram létrehozására szolgáló helyi menüvel](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Ecsetek

Az ecsetekkel időtartományok korlátozhatók olyan műveletek meghatározásához, mint a nagyítás és a vizsgálat.

Az ecsetek bemutatásához használt kód az előző „Line Chart with Context Menu to Create Pie/Bar Chart” (Vonaldiagram torta-/sávdiagram létrehozására szolgáló helyi menüvel) példában látható, amely az [előugró helyi menüket](#popup-context-menus-section) ismerteti.

1. Az ecsetműveletek abból a szempontból hasonlítanak a helyi menükhöz, hogy egy egyéni műveletsorozatot határoznak meg az ecsethez. Mindegyik művelet egy egy vagy több elemből álló tömböt tartalmaz. Mindegyik elem egyetlen helyimenü-elemet határoz meg:
   - `name`: A menüelemhez használt szöveg: „Print parameters to console” (Paraméterek nyomtatása konzolra).
   - `action`: A menüelemmel társított művelet, amely mindig egy névtelen funkció, és két argumentumot vesz fel. Ebben az esetben az argumentumok a böngésző konzolablakába vannak írva:
      - `fromTime`: az ecset kiválasztásának „ettől” időbélyege.
      - `toTime`: az ecset kiválasztásának „eddig” időbélyege.

2. Az ecsetműveletek egy másik diagrambeállítási tulajdonságként vannak hozzáadva. Figyelje meg a `linechart.Render` hívásnak átadott `brushContextMenuActions: brushActions` tulajdonságot.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Vonaldiagram torta-/sávdiagram ecsetekkel való létrehozására szolgáló helyi menüvel](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Bejelentkezés, és a TSI-mintaalkalmazás és annak forrásának vizsgálata.
> * API-k használata a TSI JavaScript ügyfélkódtárban.
> * A JavaScript használata diagramvezérlők létrehozásához és kitöltéséhez TSI-adatokkal.

A fentiekben taglaltak szerint a TSI-mintaalkalmazás bemutató adatkészletet használ. Ha szeretné megtudni, hogy hogyan hozhatja létre saját TSI-környezetét és -adatkészletét, lépjen tovább a következő cikkre:

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Time Series Insights-környezet létrehozása](tutorial-create-populate-tsi-environment.md)


