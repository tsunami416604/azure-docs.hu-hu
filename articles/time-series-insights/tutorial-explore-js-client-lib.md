---
title: A Time Series Insights JavaScript ügyfélkódtár felderítése
description: Megismerheti a Time Series Insights JavaScript ügyfélkódtárat és a kapcsolódó programozási modellt.
documentationcenter: ''
services: time-series-insights
author: BryanLa
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: time-series-insights
ms.workload: na
ms.tgt_pltfrm: ''
ms.devlang: na
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: bryanla
ms.openlocfilehash: 3fbd4f54fb511ae737abf28ae7b1b50750ab5d69
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210477"
---
# <a name="tutorial-explore-the-time-series-insights-javascript-client-library"></a>Oktatóanyag: A Time Series Insights JavaScript ügyfélkódtár felderítése

A Time Series Insights (TSI) kódtárban tárolt adatok fejlesztők általi lekérdezésének és megjelenítésének megkönnyítése érdekében kifejlesztettünk egy JavaScript D3-alapú vezérlőelem-kódtárat. Ez az oktatóanyag egy minta webalkalmazás használatával vezeti végig a TSI JavaScript ügyfélkódtáron és a kapcsolódó programozási modellen.

A tárgyalt témakörök lehetőségeket nyújtanak a kísérletezésre, a TSI-adatok elérésének mélyebb megismerésére, és a diagram-vezérlőelemek adatrenderelésre és -megjelenítésre való használatára. A cél az, hogy elég részletet nyújtsunk ahhoz, hogy a saját webalkalmazásában használja a kódtárat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A TSI mintaalkalmazás
> * A TSI JavaScript ügyfélkódtár
> * Hogyan használja a mintaalkalmazás a kódtárat a TSI-adatok megjelenítésére

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag a „Fejlesztői eszközök” (más néven DevTools vagy F12) funkciót használja, amely a legtöbb modern webböngészőben (például az [Edge](/microsoft-edge/devtools-guide), a [Chrome](https://developers.google.com/web/tools/chrome-devtools/), a [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), a [Safari](https://developer.apple.com/safari/tools/) stb. böngészőkben) megtalálható. Ha még nem ismeri ezt a funkciót, érdemes lehet megismerni a böngészőben a folytatás előtt.

## <a name="the-time-series-insights-sample-application"></a>A Time Series Insights mintaalkalmazás

Ebben az oktatóanyagban a Time Series Insights mintaalkalmazás használatával ismerjük meg az alkalmazás mögötti forráskódot, beleértve a TSI JavaScript ügyfélkódtár használatát is. Az alkalmazás egy egyoldalas webes alkalmazás (SPA), amely a kódtár használatát mutatja be az adatok minta TSI környezetből való lekérdezéséhez és megjelenítéséhez.

1. Keresse meg a [Time Series Insights mintaalkalmazást](https://insights.timeseries.azure.com/clientsample). A következőhöz hasonló oldal jelenik meg, amely felkéri a bejelentkezésre: ![TSI-ügyfélminta bejelentkezési kérés](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Kattintson a „Bejelentkezés” gombra, és írja be vagy válassza ki a hitelesítő adatait. Vállalati/céges fiókot (Azure Active Directory) vagy személyes fiókot (Microsoft-fiók vagy MSA) is használhat.

   ![TSI-ügyfélminta hitelesítőadat-kérése](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. A sikeres bejelentkezés után a következőhöz hasonló oldal jelenik meg, amely TSI-adatokkal kitöltött különböző stílusú mintadiagramokat tartalmaz. A jobb felső sarokban lévő felhasználói fiók és „Kijelentkezés” hivatkozást is figyelje meg: ![TSI-ügyfélminta főoldal a bejelentkezés után](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Oldal forrása és struktúrája

Először tekintsük meg a böngészőben renderelt oldal mögött lévő HTML és JavaScript forráskódot. Nem ismertetjük mindegyik elemet, de megismerheti a főbb szakaszokat, így képet kaphat az oldal működéséről:

1. Nyissa meg a „Fejlesztői eszközöket” a böngészőben, és vizsgálja meg az aktuális oldalt alkotó HTML-elemeket, más néven a HTML- vagy DOM-faszerkezetet.

2. Bontsa ki a `<head>` és `<body>` elemeket, és figyelje meg a következő szakaszokat:
   - A `<head>` elem alatt keresse meg az oldal működésének elősegítése érdekében további fájlokat lekérő elemeket:
     - az Azure Active Directory Authentication Library (adal.min.js) kódtárra (más néven ADAL) hivatkozó `<script>` elem, amely egy JavaScript-kódtár, amely OAuth 2.0 hitelesítést (bejelentkezést) és az API-k hozzáféréséhez jogkivonat beszerzését biztosítja:
     - `<link>` elemek a stíluslapokhoz (sampleStyles.css, tsiclient.css), más néven CSS-ekhez, amelyekkel szabályozhatók a vizuális oldalstilizálás részletei, például a színek, a betűtípusok, a térköz stb.
     - a TSI-ügyfélkódtárra (tsiclient.js) hivatkozó `<script>` elem – az oldal által a TSI-szolgáltatási API-k behívására és az oldalon lévő diagramvezérlők renderelésére használt JavaScript-kódtár.

     >[!NOTE]
     > Az ADAL JavaScript-kódtár forráskódja az [azure-activedirectory-library-for-js adattárban](https://github.com/AzureAD/azure-activedirectory-library-for-js) érhető el.
     > A TSI-ügyfél JavaScript-kódtárának forráskódja a [tsiclient adattárban](https://github.com/Microsoft/tsiclient) érhető el.

   - A `<body>` elem alatt `<div>` elemeket talál, amelyek tárolókként szolgálnak az oldalon lévő elemek elrendezésének meghatározásához, és egy másik `<script>` elemet:
     - az első `<div>` a „Bejelentkezés” párbeszédpanelt határozza meg (`id="loginModal"`).
     - a második `<div>` a következő szülőjeként működik:
       - `<div>` fejléc, amely állapotüzenetekre és bejelentkezési információkra szolgál az oldal tetején (`class="header"`).
       - egy `<div>` az oldal törzsének többi részéhez, beleértve az összes diagramot is (`class="chartsWrapper"`).
       - egy `<script>` szakasz, amely tartalmazza az oldal vezérlésére használt összes JavaScript elemet.

   [![TSI-ügyfélminta a DevTools használatával](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Bontsa ki a `<div class="chartsWrapper">` elemet, és további alárendelt `<div>` elemet talál, amelyekkel az egyes diagramvezérlő példák helyezhetők el. Figyelje meg, hogy néhány `<div>` elempár látható, egy-egy mindegyik diagrampéldához:
   - Az első (`class="rowOfCardsTitle"`) egy leíró címet tartalmaz, amely összegzi a diagramok által bemutatott elemeket. Például: „Statikus vonaldiagramok teljes méretű jelmagyarázatokkal”
   - A második (`class="rowOfCards"`) egy szülő, amely további alárendelt `<div>` elemeket tartalmaz, amelyek elhelyezik a sorokon belüli tényleges diagramvezérlő(ke)t.

  ![A törzs „div” címkéinek megtekintése.](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Most bontsa ki a `<script type="text/javascript">` elemet, amely közvetlenül a `<div class="chartsWrapper">` elem alatt található. Láthatja az oldal szintű JavaScript-szakasz elejét, amellyel az olyan műveletek minden oldallogikája kezelhető, mint például a hitelesítés, a TSI-szolgáltatási API-k behívása, a diagramvezérlők renderelése és egyebek:

  ![A törzs szkriptjének megtekintése](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-client-javascript-library-concepts"></a>TSI-ügyfél JavaScript-kódtárának alapelvei

Bár nem tekintjük át részletesen, a TSI-ügyfélkódtár (tsclient.js) alapvetően két fontos kategória absztrakcióját nyújtja:

- **A TSI-lekérdezési API-k hívására szolgáló burkoló módszerek** – Olyan REST API-k, amelyek lehetővé teszik a TSI-adatok lekérdezését összesítő kifejezésekkel, és amelyek a kódtár `TsiClient.Server` névterében vannak rendezve.
- **A diagramvezérlők néhány típusának létrehozására és kitöltésére szolgáló módszerek** – A weboldalon lévő összesített TSI-adatok renderelésére szolgálnak, és a kódtár `TsiClient.UX` névterében vannak rendezve.

A következő alapelvek univerzálisak, és általában érvényesek a TSI-ügyfélkódtár API-kra.

### <a name="authentication"></a>Hitelesítés

Amint azt korábban említettük, ez a minta egy egyoldalas alkalmazás, amely az OAuth 2.0 támogatást használja az ADAL-ban a felhasználói hitelesítéshez. Itt láthatja a szkript ezen szakaszának néhány fontos elemét:

1. Az ADAL hitelesítéshez való használatához az ügyfélalkalmazásnak az Azure Active Directory (Azure AD) alkalmazásregisztrációs szolgáltatásában kell regisztrálnia magát. SPA-ként ez az alkalmazás az „implicit” OAuth 2.0 engedélyezési folyamat használatához van regisztrálva. Ennek megfelelően az alkalmazás a futásidőben ad meg néhány regisztrációs tulajdonságot, például az ügyfél GUID azonosítóját (`clientId`) és az átirányítási URI-címét (`postLogoutRedirectUri`), hogy részt vegyen a folyamatban.

2. Később az alkalmazás „hozzáférési jogkivonatot” kér az Azure AD-től. A hozzáférési jogkivonat az engedélyek véges készletéhez van kiadva, egy adott szolgáltatás-/API-azonosítóhoz (https://api.timeseries.azure.com), más néven a jogkivonat „célközönségéhez”. A jogkivonat engedélyei a bejelentkezett felhasználó nevében vannak kiadva. A szolgáltatás-/API-azonosító az alkalmazás Azure AD regisztrációjában szereplő egy másik tulajdonság. Miután az ADAL visszaadja a hozzáférési jogkivonatot az alkalmazásnak, az „tulajdonosi jogkivonatként” lesz átadva a TSI-szolgáltatási API-k elérésekor.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>A vezérlők azonosítása

A fentiekben taglaltak szerint a `<body>` `<div>` elemei biztosítják az oldalon bemutatott összes diagramvezérlő elrendezését. Ezek mindegyike a diagramvezérlő elhelyezésének és vizuális attribútumainak tulajdonságait határozza meg, beleértve egy `id` tulajdonságot is. Az `id` tulajdonság egy egyedi azonosítót biztosít, amellyel a JavaScript-kódban azonosíthatók és köthetők a vezérlők a rendereléshez és frissítéshez.

### <a name="aggregate-expressions"></a>Összesítő kifejezések

A TSI-ügyfélkódtár API-k erősen támaszkodnak az összesítő kifejezések használatára. Az összesítő kifejezések egy vagy több „keresési kifejezés” összeállítására nyújtanak lehetőséget. Az API-k hasonlóak ahhoz, ahogyan a [Time Series Insights Explorer](https://insights.timeseries.azure.com/demo) használja a keresés hatókörét, a hol állítást, a mértékeket és a felosztás alapját képező értéket. A legtöbb kódtár API az összesítő kifejezések tömbjét veszi, amellyel a szolgáltatás TSI-adatlekérdezéseket épít fel.

### <a name="call-pattern"></a>Hívásmintázat

A diagramvezérlők kitöltése és renderelése általános mintázatot követ. Láthatja, hogy a rendszer ezt a mintázatot használja az oldal összes JavaScript-kódjában, ami példányosítja és betölti a minta TSI-alkalmazás vezérlőit:

1. Deklaráljon egy tömböt ahhoz, hogy egy vagy több TSI összesítő kifejezést tartalmazzon.

   ```javascript
   var aes =  [];
   ```

2. Építsen fel 1–n összesítő kifejezés objektumot, és adja azokat az összesítő kifejezés tömbjéhez.

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```
   **aggregateExpression paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | predicateObject | Az adatszűrő kifejezés. |`{predicateString: "Factory = 'Factory3'"}` |
   | measureObject   | A használt mérték tulajdonságneve. | `{property: 'Temperature', type: "Double"}` |
   | measureTypes    | A mértéktulajdonság kívánt összesítései. | `['avg', 'min']` |
   | searchSpan      | Az összesítő kifejezés időtartama és az intervallum mérete. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | splitByObject   | Azon karakterlánc-tulajdonság, amely alapján fel szeretne osztani (opcionális – null értékű is lehet). | `{property: 'Station', type: 'String'}` |
   | szín           | A renderelni kívánt objektumok színe. | `'pink'` |
   | alias           | Az összesítő kifejezés rövid neve. | `'Factory3Temperature'` |
   | contextMenuActions | Egy vizualizációban a Time Series-objektumokhoz kötni kívánt műveletek tömbje. | Lásd: [Előugró helyi menük a Speciális funkciók szakaszban](#popup-context-menus). |

3. Hívjon be egy TSI-lekérdezést `TsiClient.Server` API-kkal az összesítő adatok lekéréséhez.

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```
   **getAggregates paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | jogkivonat     | A TSI API hozzáférési jogkivonata | `authContext.getTsiToken()` Lásd a [hitelesítés szakaszt](#authentication). |
   | envFQDN     | A TSI környezet teljes tartományneve | Az Azure Portalon például `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | aeTsxArray | TSI-lekérdezési kifejezések tömbje | A korábban leírt `aes` változót használja: `aes.map(function(ae){return ae.toTsx()}` |

4. Alakítsa át a TSI-lekérdezésből visszaadott tömörített eredményt JSON formátumba a vizualizációhoz.

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Hozzon létre egy diagramvezérlőt `TsiClient.UX` API-kkal, majd kösse azt az oldal egyik `<div>` eleméhez.

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Töltse ki a diagramvezérlőt az átalakított JSON-adatobjektummal/-objektumokkal, és renderelje ezt az oldalon.

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Vezérlők renderelése

A kódtár jelenleg nyolc egyedi elemzésvezérlőt tesz elérhetővé. Ezek közé tartozik egy vonaldiagram, egy tortadiagram, egy sávdiagram, egy hőtérkép, a hierarchiavezérlők, egy hozzáférhető rács, különálló esemény idővonalak és állapotváltás idővonalak.

### <a name="line-bar-pie-chart-examples"></a>Vonal-, sáv-, tortadiagram példái

Először tekintsük meg az alkalmazásban bemutatott standard diagramvezérlők mögötti kódot, és az azok létrehozására szolgáló programozási modellt/mintákat. Pontosabban, megvizsgálja az `// Example 3/4/5` megjegyzés alatt lévő HTML szakaszt, amely a `chart3`, `chart4` és `chart5` azonosítóértékekkel rendelkező vezérlőket rendereli.

Az [Oldal forrása és struktúrája szakasz](#page-source-and-structure) 3. lépésében már látta, hogy a diagramvezérlők sorokba vannak rendezve az oldalon, amelyek mindegyike rendelkezik egy-egy leíró címsorral. Ebben a példában a három kitöltött diagram mindegyike a „Multiple Chart Types From the Same Data” (Több diagramtípus ugyanazokból az adatokból) cím `<div>` alatt szerepel, amely az alatta lévő három `<div>` elemhez van kötve:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A JavaScript-kód következő szakasza a korábban leírt mintázatot használja összesítő TSI-kifejezések felépítéséhez, azok TSI-adatok lekérdezéséhez való használatához, illetve a három diagram rendereléséhez. Figyelje meg a `tsiClient.ux` névtérből a megfelelő diagramok létrehozásához és rendereléséhez használt három típust (`LineChart`, `BarChart`, `PieChart`). Azt is figyelje meg, hogy mindhárom diagram ugyanazokat az összesítőkifejezés-adatokat tudja használni (`transformedResult`):

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

A három diagram a következőképpen jelenik meg a rendereléskor:

[![Több diagramtípus ugyanazokból az adatokból](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Speciális funkciók

A kódtár néhány további speciális funkciót is elérhetővé tesz, amelyeket érdemes lehet kihasználni.

### <a name="states-and-events"></a>Állapotok és események

Az elérhetővé tett speciális funkciók egy példája, hogy állapotváltásokat és különálló eseményeket adhat a diagramokhoz. Ez a funkció az incidensek kiemeléséhez, a riasztáshoz és az állapotkapcsolók be-/kikapcsolásához hasznos.

Itt láthatja az `// Example 10` megjegyzés alatt lévő HTML-szakasz mögötti kódot. A kód egy sorvezérlőt renderel a „Line Charts with Multiple Series Types” (Vonaldiagramok több sorozattípussal) cím alatt, a `chart10` azonosítóértékkel rendelkező `<div>` elemhez kötve azt:

1. Először egy `events4` nevű struktúra van definiálva, amely a nyomon követni kívánt állapotváltási elemeket tartalmazza. A következőket tartalmazza:
   - Egy `"Component States"` nevű karakterlánckulcs
   - Az állapotokat jelző értékobjektumok tömbje, amelyek mindegyike a következőket tartalmazza:
     - JavaScript ISO időbélyeget tartalmazó karakterlánckulcs
     - Az állapot jellemzőit tartalmazó tömb
       - egy szín
       - egy leírás

2. Ezután az `events5` struktúra van definiálva az `"Incidents"` elemhez, amely a nyomon követni kívánt eseményelemek tömbjét tartalmazza. A tömb struktúrája ugyanolyan alakú, mint az `events4` elemhez leírt struktúra.

3. Végül a rendszer rendereli a vonaldiagramot, a diagram beállítási paraméterei közé helyezve a két struktúrát: `events:` és `states:`. Figyelje meg a `tooltip:`, `theme:` vagy `grid:` meghatározására szolgáló többi beállítási paramétert.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

A rombusz jelölők/előugró elemek vizuálisan jelölik az incidenseket, az idővonal mentén lévő színes sávok/előugró elemek pedig az állapotváltásokat jelölik:

[![Vonaldiagramok több sorozattípussal](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="popup-context-menus"></a>Előugró helyi menük

A speciális funkciók egy másik példája az egyéni helyi menük (a jobb gombbal kattintásra előugró menük), amelyek hasznosak a műveletek engedélyezéséhez és az alkalmazás hatókörén belüli következő logikus lépésekhez.

Itt láthatja az `// Example 13/14/15` alatt lévő HTML mögötti kódot. Ez a kód kezdetben egy vonaldiagramot renderel a „Line Chart with Context Menu to Create Pie/Bar Chart” (Vonaldiagram torta-/sávdiagram létrehozására szolgáló helyi menüvel) cím alatt, a `chart13` azonosítóértékkel rendelkező `<div>` elemhez kötve azt. A vonaldiagram a helyi menükkel lehetővé teszi egy torta- és sávdiagram dinamikus létrehozását a `chart14` és `chart15` azonosítókkal rendelkező `<div>` elemekhez kötve azokat. Ezenkívül a torta- és a sávdiagramok is helyi menüket használnak a saját funkcióik engedélyezéséhez: ahhoz, hogy adatokat másoljanak a tortadiagramból a sávdiagramba, és hogy kinyomtassák a sávdiagram adatait a böngésző konzolablakában.

1. A rendszer először egyéni műveletek sorozatát definiálja. Ezek mindegyike egy vagy több elemből álló tömböt tartalmaz, ahol mindegyik elem egyetlen helyimenü-elemet határoz meg:
   - `barChartActions`: a tortadiagram helyi menüjét határozza meg, amely egy egyetlen elemet definiáló elemet tartalmaz:
     - `name`: a menüelemhez használt szöveg: „Print parameters to console” (Paraméterek nyomtatása konzolra)
     - `action`: a menüelemmel társított művelet, amely mindig egy névtelen funkció, és három argumentumot vesz fel a diagram létrehozásához használt összesítő kifejezés alapján. Ebben az esetben ezek a böngésző konzolablakába vannak írva:
       - `ae`: az összesítő kifejezések tömbje
       - `splitBy`: a splitBy érték
       - `timestamp`: az időbélyeg
   - `pieChartActions`: a sávdiagram helyi menüjét határozza meg, amely egy egyetlen elemet definiáló elemet tartalmaz. Az alakzat és a séma megegyezik az előző `barChartActions` elemmel, de figyelje meg, hogy az `action` funkció jelentősen eltér, mert ez példányosítja és rendereli a sávdiagramot. Azt is figyelje meg, hogy az `ae` argumentum használatával határozza meg az összesítő kifejezések tömbjét, amelyet a futásidőben ad át a menüelem előugrása során. A funkció az `ae.contextMenu` tulajdonságot is beállítja a `barChartActions` helyi menüvel.
   - `contextMenuActions`: a vonaldiagram helyi menüjét határozza meg, amely a három menüelemet definiáló három elemet tartalmaz. Az egyes elemek alakja és sémája megegyezik az előzőekével. Akárcsak a `barChartActions`, az első elem a böngésző konzolablakába írja a három funkcióargumentumot. A `pieChartActions` elemhez hasonlóan a második két elem a torta- és a sávdiagramokat példányosítja és jeleníti meg. A második két elem az `ae.contextMenu` tulajdonságukat is beállítja a `pieChartActions` és `barChartActions` helyi menükkel.

2. Ezután két összesítő kifejezés kerül az `aes` összesítőkifejezés-tömbbe, meghatározva mindegyik `contextMenuActions` tömbjét. Ezeket a rendszer a vonaldiagram vezérlőjével használja.

3. Végezetül a rendszer kezdetben csak a vonaldiagramot rendereli, amelyből a torta- és a sávdiagram is renderelhető a futásidőben.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

A képernyőfelvétel a diagramokat ábrázolja a megfelelő előugró helyi menüikkel. A torta- és sávdiagramok a vonaldiagram helyi menü lehetőségeinek használatával dinamikusan jönnek létre:

[![Vonaldiagram torta-/sávdiagram létrehozására szolgáló helyi menüvel](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Ecsetek

Az ecsetekkel időtartományok korlátozhatók olyan műveletek meghatározásához, mint a nagyítás és a vizsgálat.

Az ecsetek bemutatásához használt kód az előző „Line Chart with Context Menu to Create Pie/Bar Chart” (Vonaldiagram torta-/sávdiagram létrehozására szolgáló helyi menüvel) példában is látható, az [Előugró helyi menük](#popup-context-menus-section) szakaszban.

1. Az ecsetműveletek nagyon hasonlóak a helyi menükhöz, amelyek az ecset egyéni műveleteinek sorozatát határozzák meg. Ezek mindegyike egy vagy több elemből álló tömböt tartalmaz, ahol mindegyik elem egyetlen helyimenü-elemet határoz meg:
   - `name`: a menüelemhez használt szöveg: „Print parameters to console” (Paraméterek nyomtatása konzolra)
   - `action`: a menüelemmel társított művelet, amely mindig egy névtelen funkció, és két argumentumot vesz fel. Ebben az esetben ezek a böngésző konzolablakába vannak írva:
      - `fromTime`: az ecset kiválasztásának „ettől” időbélyege
      - `toTime`: az ecset kiválasztásának „eddig” időbélyege

2. Az ecsetműveletek egy másik diagrambeállítási tulajdonságként vannak hozzáadva. Figyelje meg a `linechart.Render` hívásnak átadott `brushContextMenuActions: brushActions` tulajdonságot.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Vonaldiagram torta-/sávdiagram ecsetekkel való létrehozására szolgáló helyi menüvel](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Bejelentkezés, és a TSI-mintaalkalmazás és annak forrásának vizsgálata
> * API-k használata a TSI JavaScript ügyfélkódtárban
> * A JavaScript használata diagramvezérlők létrehozásához és kitöltéséhez TSI-adatokkal

A fentiekben taglaltak szerint a TSI-mintaalkalmazás bemutató adatkészletet használ. Saját TSI-környezete és -adatkészlete létrehozásának módjáról a következő cikkben olvashat:

> [!div class="nextstepaction"]
> [Azure Time Series Insights-környezet tervezése](time-series-insights-environment-planning.md)


