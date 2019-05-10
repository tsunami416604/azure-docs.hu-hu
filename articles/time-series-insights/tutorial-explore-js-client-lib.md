---
title: 'Oktatóanyag: Ismerkedés az Azure Time Series Insights JavaScript ügyféloldali kódtár |} A Microsoft Docs'
description: Megismerheti az Azure Time Series Insights JavaScript ügyfélkódtárat és a kapcsolódó programozási modellt.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 8cb1d06872f7eae04bac934220da9d58982d0f4b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233738"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Oktatóanyag: Az Azure Time Series Insights JavaScript ügyfélkódtár felderítése

Hogy megkönnyítsük a webfejlesztőknek a Time Series Insights (TSI) szolgáltatásban tárolt adatok lekérdezését és megjelenítését, kifejlesztettünk egy JavaScript D3-alapú TSI-ügyfélkódtárat. Ez az oktatóanyag végigvezeti Önt a TSI-klienskódtár és a egy üzemeltetett mintaalkalmazással programozási modellt.

Az oktatóanyag részletesen ismerteti az működjön együtt a TSI-adatok eléréséhez, és használata a diagram-vezérlőelemeket jelennek meg, és megjelenítheti az adatokat. Kísérletezzen a különböző típusú diagramok adatok vizualizációja, hogyan is megtudhatja. Az oktatóanyag befejezésekor, fogja tudni használni az ügyféloldali kódtár TSI funkciókat építhet be webes alkalmazásába.

Pontosabban a következőkről szerezhet ismereteket:

> [!div class="checklist"]
> * A TSI mintaalkalmazás.
> * A TSI JavaScript ügyfélkódtár.
> * Hogyan használja a mintaalkalmazás a kódtárat a TSI-adatok megjelenítésére.

> [!NOTE]
> * Az oktatóanyagban egy ingyenes, üzemeltetett, [Time Series Insights web bemutatót](https://insights.timeseries.azure.com/clientsample).
> * A Time Series Insights minta app forrásfájlok szerepelnek a [GitHub-mintaadattárból](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Olvassa el a [TSI ügyfél segédanyagok](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Videó

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>Ebben a videóban a nyílt forráskódú Time Series Insights JavaScript SDK-t mutatjuk be.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a böngésző **fejlesztői eszközök** funkció. Modern böngészők ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/), és mások) általában biztosítanak hozzáférést az **Webes vizsgáló nézet** keresztül a `F12` billentyűparancsot. Ellenkező esetben is elérhető a weblapon kattintson a jobb gombbal, majd válasszon **vizsgálata elem**.

## <a name="time-series-insights-sample-application"></a>A Time Series Insights mintaalkalmazás

Ez az oktatóanyag során egy ingyenes, üzemeltetett, a Time Series Insights mintaalkalmazás segítségével ismerje meg az alkalmazást, majd a TSI JavaScript ügyféloldali kódtár forráskódját. Keresztül, akkor megtudhatja, hogyan kommunikáljanak a TSI a JavaScript és diagramokat és ábrákat keresztül adatok megjelenítése.

1. Keresse meg a [Time Series Insights mintaalkalmazást](https://insights.timeseries.azure.com/clientsample). A következő bejelentkezési üzenet jelenik meg:

   [![Bejelentkezési kérések jelenhetnek minta TSI-ügyfél](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Válassza ki **bejelentkezés** adja meg vagy válassza ki a hitelesítő adatait. Vállalati szervezeti fiók (az Azure Active Directory) és a egy személyes fiók (Microsoft Account vagy MSA) használata.

   [![A TSI-ügyfél minta hitelesítő adatokat kér](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Bejelentkezés, miután lap jelenik meg a különböző típusú diagramok TSI adatokat is tartalmaz. A felhasználói fiók és a **Kijelentkezés** hivatkozás a jobb felső sarokban láthatók:

   [![A TSI-ügyfél minta fő lapján után jelentkezzen be](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Oldal forrása és struktúrája

Először tekintse a [HTML és JavaScript forráskódját](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) a megjelenített webes lapozható:

1. Nyissa meg a böngészőben a **Fejlesztői eszközök** lapot. Vizsgálja meg az aktuális oldalt alkotó HTML-elemeket, más néven a HTML- vagy DOM-faszerkezetet.

1. Bontsa ki a `<head>` és `<body>` elemeket, és vizsgálja meg a következő szakaszokban:

   * Alatt a `<head>` elem, megtalálhatja lap azonosítóadatokat és függőségeit, amelyek lehetővé teszik az alkalmazás futtatását:
     * Az Azure Active Directory Authentication Library (**adal.min.js**) kódtárra (más néven ADAL) hivatkozó `<script>` elem. Az ADAL egy JavaScript-kódtár, amely OAuth 2.0 hitelesítést (bejelentkezést) és az API-k hozzáféréséhez jogkivonat beszerzését biztosítja.
     * Több `<link>` elem a stíluslapokhoz, más néven CSS-ekhez, például a **sampleStyles.css**-hez és a **tsiclient.css**-hez. A stíluslapokkal szabályozhatók a vizuális oldalstilizálás részletei, például a színek, a betűtípusok, a térköz stb.
     * A TSI JavaScript-ügyfélkódtárára (**tsiclient.js**) hivatkozó `<script>` elem. Ezt a kódtárt az oldal a TSI-szolgáltatási API-k behívására és az oldalon lévő diagramvezérlők renderelésére használja.

     >[!NOTE]
     > * Az ADAL JavaScript-kódtár forráskódja az [azure-activedirectory-library-for-js adattárban](https://github.com/AzureAD/azure-activedirectory-library-for-js) érhető el.
     > * A TSI-ügyfél JavaScript-kódtárának forráskódja a [tsiclient adattárban](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial) érhető el.

   * Alatt a `<body>` elemben találja `<div>` elemeket, amelyek segítséget nyújtanak a elemek elrendezésének a lapon, és a egy másik `<script>` elem:
     * Az első `<div>` elem a **Bejelentkezés** párbeszédpanelt határozza meg (`id="loginModal"`).
     * A második `<div>` elem a következő szülőjeként működik:
       * Egy `<div>` fejléc elem, amely állapotüzenetekre és bejelentkezési információkra szolgál az oldal tetején (`class="header"`).
       * Egy `<div>` elem az oldal törzsének többi részéhez, beleértve az összes diagramot is (`class="chartsWrapper"`).
       * Egy `<script>` szakasz, amely tartalmazza az oldal vezérlésére használt összes JavaScript elemet.

   [![TSI-ügyfélminta a fejlesztői eszközök használatával](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Bontsa ki a `<div class="chartsWrapper">` elemhez az itt található több alárendelt `<div>` elemeket. Ezekkel helyezhetők el az egyes diagramvezérlő példák. Figyelje meg, hogy néhány `<div>` elempár látható, egy-egy mindegyik diagrampéldához:

   * Az első (`class="rowOfCardsTitle"`) elem egy leíró címet tartalmaz, amely összegzi a diagramok által bemutatott elemeket. Például:`Static Line Charts With Full-Size Legends.`
   * A második (`class="rowOfCards"`) elem egy szülő, amely további alárendelt `<div>` elemeket tartalmaz, amelyek elhelyezik a sorokon belüli tényleges diagramvezérlő(ke)t.

   [![Törzs div elemek](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Most bontsa ki a `<script type="text/javascript">` elemet, amely közvetlenül a `<div class="chartsWrapper">` elem alatt található. Figyelje meg, amellyel kezelni tudja az oldal logikai (hitelesítés, a TSI szolgáltatás API-k, a diagram-vezérlőelemeket, és az egyéb renderelő hívása) lapszintű JavaScript szakasz elején:

   [![Parancsprogram törzse](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="tsi-javascript-client-library-concepts"></a>A TSI JavaScript ügyfélkódtár fogalmai

A TSI-klienskódtár (**tsclient.js**) absztrakt entitást két fontos JavaScript funkciókat biztosítja:

* **A TSI lekérdezési API-k meghívására szolgáló burkoló módszerek**: REST API-k, amelyek lehetővé teszik, hogy a lekérdezés a TSI-adatok összesítő kifejezések használatával. A módszerek a kódtár `TsiClient.Server` névterében vannak rendezve.

* **Módszerek a létrehozása és feltöltése számos különböző típusú vezérlők diagramkészítési**: A TSI egy weblap összesített adatok használt módszerek. A módszerek a kódtár `TsiClient.UX` névterében vannak rendezve.

Ezen egyszerűbb fejlesztők fejleszthetnek felhasználói felületi graph és a diagram összetevőket, amelyek a TSI adatokkal könnyebben működteti.

### <a name="authentication"></a>Hitelesítés

A [Time Series Insights mintaalkalmazás](https://insights.timeseries.azure.com/clientsample) egy egyoldalas alkalmazás az ADAL OAuth 2.0-s felhasználói hitelesítés támogatását:

1. Adal-t használ a hitelesítéshez, ha az ügyfélalkalmazás regisztrálni kell az Azure Active Directoryban. Sőt, az egyoldalas alkalmazás használatára regisztrálva van a [OAuth 2.0 implicit folyamat megadása](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Mint ilyen az alkalmazás adjon meg néhány futásidőben regisztrációs tulajdonságát. Ezek közé tartozik az ügyfél GUID Azonosítóját (`clientId`) és az átirányítási URI-t (`postLogoutRedirectUri`).
1. Később, kér az alkalmazás egy **hozzáférési jogkivonat** az Azure Active Directoryból. A hozzáférési jogkivonat kiadott engedélyek egy adott szolgáltatás és az API-azonosító véges készletének (`https://api.timeseries.azure.com`). A jogkivonat engedélyei a bejelentkezett felhasználó nevében vannak kiadva. A szolgáltatás és az API az azonosítója, amely az Azure Active Directory alkalmazásregisztráció szerepel egy másik tulajdonság.
1. Adal-t a hozzáférési jogkivonatot az alkalmazás ad vissza, miután átadva egy **tulajdonosi jogkivonat** mikor fér hozzá a TSI szolgáltatás API-k.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> A Microsoft által támogatott ADAL könyvtárakat kapcsolatos további információkért tekintse meg a [ADAL dokumentációja](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>A vezérlők azonosítása

A megadott példában `<div>` elemek rendezve jelennek meg a szülő `<body>` késleltetésnél elrendezés biztosít a diagram-vezérlőelemeket, az oldalon megjelenített elemet.

Minden egyes `<div>` elem azt határozza meg, az elhelyezési és a diagram-vezérlőelemeket visual attribútumai tulajdonságait. HTML-elemmel `id` tulajdonságok egyedi azonosítók kötődni a konkrét funkciók megjelenítése és teszi az adatok frissítése erre a célra.

### <a name="aggregate-expressions"></a>Összesítő kifejezések

A TSI-ügyfélkódtár API-k összesítő kifejezések használata:

* Egy összesítő kifejezés lehetővé teszi a hozhat létre egy vagy több **keresési feltételek**.

* Az ügyfél API-kat úgy tervezték, hogy egy másik adja meg a bemutató alkalmazás hasonló funkciókat nyújtanak (a [Time Series Insights explorer](https://insights.timeseries.azure.com/demo)), amely használja a keresés, a where predikátum, mértékeket és osztott érték.

* Az ügyfélkódtár API-k a legtöbb adat a TSI-lekérdezés összeállításához a szolgáltatás által használt összesítő kifejezések tömbjét igénybe vehet.

### <a name="call-pattern"></a>Hívásmintázat

Diagram-vezérlőelemeket leképezése után rögtön hozzáláthat egy általános mintát követi. Ez a minta általános figyelhető meg a mintaalkalmazást során, és segítséget, ha az ügyféloldali kódtár használatával:

1. Deklaráljon egy `array` elemet ahhoz, hogy egy vagy több TSI összesítő kifejezést tartalmazzon:

   ```javascript
   var aes =  [];
   ```

1. Build *1* való *n* összesített kifejezés objektumokat. Ezután adja hozzá őket az összesítő kifejezés tömbhöz:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | `predicateObject` | Az adatok szűrési kifejezés. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | A használt mérték tulajdonságneve. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | A mértéktulajdonság kívánt összesítései. | `['avg', 'min']` |
   | `searchSpan`      | Az összesítő kifejezés időtartama és az intervallum mérete. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Azon sztringtulajdonság, amely alapján fel szeretne osztani (opcionális – null értékű is lehet). | `{property: 'Station', type: 'String'}` |
   | `color`         | A renderelni kívánt objektumok színe. | `'pink'` |
   | `alias`           | Az összesítő kifejezés rövid neve. | `'Factory3Temperature'` |
   | `contextMenuActions` | Egy vizualizációban a Time Series-objektumokhoz kötni kívánt műveletek tömbje. | További információkért lásd: a szakasz [felugró helyi menük](#pop-up-context-menus) |

1. Hívjon be egy TSI-lekérdezést a `TsiClient.Server` API-k használatával az összesítő adatok lekéréséhez:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | `token`     | A TSI API hozzáférési jogkivonata. |  `authContext.getTsiToken()` További információkért lásd a [hitelesítéssel foglalkozó szakaszt](#authentication). |
   | `envFQDN`   | Adja meg a TSI-környezet teljes tartománynevét (FQDN). | Az Azure Portalon például: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | TSI-lekérdezési kifejezések tömbje. | A korábban leírt `aes` változót használja: `aes.map(function(ae){return ae.toTsx()}`. |

1. Alakítsa át a TSI-lekérdezésből visszaadott tömörített eredményt JSON formátumba a vizualizációhoz:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Hozzon létre egy diagramvezérlőt a `TsiClient.UX` API-k használatával, majd kösse azt az oldal egyik `<div>` eleméhez:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Töltse ki a diagramvezérlőt az átalakított JSON-adatobjektummal/-objektumokkal, és renderelje a vezérlőt az oldalon:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Vezérlők renderelése

A TSI-klienskódtára biztosítja az egyedi, a-beépített, elemzési nyolc vezérlők:

* **Vonaldiagram**
* **Tortadiagram**
* **Sávdiagram**
* **heatmap**
* **hierarchia vezérlők**
* **elérhető rács**
* **különálló esemény ütemtervek**
* **állapot átmeneti ütemtervek**

### <a name="line-bar-pie-chart-examples"></a>Vonal-, sáv-, tortadiagram példái

Tekintse meg a bemutatót kódot néhány, a standard szintű diagram vezérlőelem megjelenítéséhez. Figyelje meg, hogy a programozási modell és a vezérlőn létrehozásához. Pontosabban, vizsgálja meg a HTML formátumú, területen szakaszában a `// Example 3/4/5` megjegyzést, amely szabályozza a HTML-lel rendereli `id` értékek `chart3`, `chart4`, és `chart5`.

A visszahívás **3. lépés** , a [lapon a forrás- és struktúra szakasz](#page-source-and-structure) , hogy az oldalon, amelyek mindegyike rendelkezik egy leíró nevet sor sorok rendezve jelennek meg diagram-vezérlőelemeket. Ebben a példában a három diagramok fel van töltve a cím alatt `"Multiple Chart Types From the Same Data"` `<div>` három elemet, és a kötött `<div>` elemek, amelyek a cím alatt található:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A JavaScript-kód következő szakasza a korábban leírt mintázatokat használja: összesítő TSI-kifejezések felépítése, azok használata TSI-adatok lekérdezéséhez, illetve a három diagram renderelése. Figyelje meg a `tsiClient.ux` névtérből a megfelelő diagramok létrehozásához és rendereléséhez használt három típust (`LineChart`, `BarChart`, `PieChart`). Azt is figyelje meg, hogy mindhárom diagram ugyanazokat az összesítőkifejezés-adatokat tudja használni (`transformedResult`):

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

A három diagram a következőképpen jelenik meg a rendereléskor:

[![Több diagramtípus ugyanazokból az adatokból](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Speciális funkciók

A TSI ügyféloldali kódtár adatvizualizációkat kreatívan megvalósításához használható számos további funkciót tartalmaz.

### <a name="states-and-events"></a>Állapotok és események

Egy speciális funkciója lehetővé teszi, hogy állapotváltásra és diszkrét események diagramok. Ez a funkció hasznos kiemelését incidensek, a riasztások és az állapot kapcsolók létrehozása (be/ki például kapcsolók).

Tekintse meg a kódot a környező a `// Example 10` megjegyzést. A kód jelenik meg a cím alatt egy sor vezérlő `"Line Charts with Multiple Series Types"`, és kötést hoz létre, hogy a `<div>` elem a HTML-lel `id` érték `chart10`.

1. Először egy struktúra nevű `events4` van definiálva, amely tárolja az állapotváltozás elemek nyomon követéséhez. A struktúra a következőket tartalmazza:

   * Egy `Component States` nevű sztringkulcs.
   * Az állapotokat jelző értékobjektumok tömbje. Mindegyik objektum a következőket tartalmazza:
     * Egy JavaScript ISO időbélyeget tartalmazó sztringkulcs.
     * Az állapot jellemzőit (szín, leírás) tartalmazó tömb.

2. Ezután a `events5` struktúra van definiálva `Incidents`, ez az esemény elemeit nyomon követése egy tömbjét tartalmazza. A tömb struktúrája ugyanolyan alakú, mint az `events4` elemhez leírt struktúra.

3. Végül a rendszer rendereli a vonaldiagramot, a diagram beállítási paraméterei közé helyezve a két struktúrát: `events:` és `states:`. Figyelje meg a `tooltip:`, `theme:` vagy `grid:` meghatározására szolgáló többi beállítási paramétert.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Vizuális szempontból a rombusz jelölők/előugró elemek jelölik az incidenseket, az idővonal mentén lévő színes sávok/előugró elemek pedig az állapotváltásokat:

[![Vonaldiagramok több sorozattípussal](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Előugró helyi menük

Egy másik speciális funkciója lehetővé teszi egyéni helyi menük (kattintson a jobb gombbal a legördülő menük) létrehozását. Ezekkel engedélyezhetők különböző műveletek és a következő logikus lépések az alkalmazás hatókörén belül.

Keresse meg a kódot a `// Example 13/14/15` megjegyzést. Ez a kód először egy vonaldiagramot a cím alatt jelenik meg `"Line Chart with Context Menu to Create Pie/Bar Chart"` a diagram van kötve, és a `<div>` elem a HTML-lel `id` érték `chart13`.

A helyi menükkel a vonaldiagram lehetővé teszi torta- és sávdiagramok dinamikus létrehozását, a `chart14` és `chart15` azonosítókkal rendelkező `<div>` elemekhez kötve azokat. Ezenkívül a torta- és a sávdiagramok is helyi menüket használnak a saját funkcióik engedélyezéséhez: ahhoz, hogy adatokat másoljanak a tortadiagramból a sávdiagramba, és hogy kinyomtassák a sávdiagram adatait a böngésző konzolablakában.

1. A rendszer először egyéni műveletek sorozatát definiálja. Mindegyik művelet egy egy vagy több elemből álló tömböt tartalmaz. Mindegyik elem egyetlen helyimenü-elemet határoz meg:

   * `barChartActions`: Ez a művelet a helyi menü meghatározásához egy elem egy elemet tartalmaz kördiagram határozza meg:
     * `name`: A szöveg, amely a menüelem szolgál: "Nyomtassa ki a konzol paramétereit."
     * `action`: A menüelem tartozó művelet. Ez a művelet mindig egy névtelen funkció, amely három argumentumot vesz fel a diagram létrehozásához használt összesítő kifejezés alapján. Ebben az esetben az argumentumok a böngésző konzolablakába vannak írva:
       * `ae`: Az összesítő kifejezés tömb.
       * `splitBy`: A `splitBy` értéket.
       * `timestamp`: Az időbélyeg.

   * `pieChartActions`: Ez a művelet az oszlopdiagramot, meghatározásához egy elem egy elemet tartalmazó helyi menüjére határozza meg. Az alakzat és a séma megegyezik az előző `barChartActions` elemmel, de figyelje meg, hogy az `action` funkció jelentősen eltér: példányosítja és rendereli a sávdiagramot. Azt is figyelje meg, hogy az `ae` argumentum használatával határozható meg az összesítő kifejezések tömbje, amelyet a rendszer a futásidőben ad át a menüelem megnyílásakor. A funkció az `ae.contextMenu` tulajdonságot is beállítja a `barChartActions` helyi menüvel.
   * `contextMenuActions`: Ez a művelet határozza meg a helyi menüben a vonaldiagram, amelyek három-három menü elemeinek definiálásához elemeket tartalmazza. Az egyes elemek alakja és sémája megegyezik az előzőleg leírt elemekével. Akárcsak a `barChartActions` elem, az első elem a böngésző konzolablakába írja a három funkcióargumentumot. A `pieChartActions` elemhez hasonlóan a második két elem a torta- és a sávdiagramokat példányosítja és jeleníti meg. A második két elem az `ae.contextMenu` tulajdonságukat is beállítja a `pieChartActions` és `barChartActions` helyi menükkel.

2. Ezután két összesítő kifejezés kerül az `aes` összesítőkifejezés-tömbbe, meghatározva mindegyik `contextMenuActions` elem tömbjét. Ezeket a kifejezéseket a rendszer a vonaldiagram vezérlőjével használja.

3. Végezetül a rendszer kezdetben csak a vonaldiagramot rendereli, amelyből a torta- és a sávdiagram is renderelhető a futásidőben.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

A képernyőkép a diagramokat ábrázolja a hozzájuk tartozó előugró helyi menüikkel. A torta- és sávdiagramok a vonaldiagram helyi menü lehetőségeinek használatával dinamikusan jönnek létre.

[![Vonaldiagram torta-/sávdiagram létrehozására szolgáló helyi menüvel](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Ecsetek

Az ecsetekkel időtartományok korlátozhatók olyan műveletek meghatározásához, mint a nagyítás és a vizsgálat.

Az előző "Sor diagram a helyi menü, hozzon létre torta/sávdiagram" példa, amely leírja a felugró helyi menük jelenik meg a kódot, amely ecsetek mutatja be.

1. Az ecsetműveletek abból a szempontból hasonlítanak a helyi menükhöz, hogy egy egyéni műveletsorozatot határoznak meg az ecsethez. Mindegyik művelet egy egy vagy több elemből álló tömböt tartalmaz. Mindegyik elem egyetlen helyimenü-elemet határoz meg:
   * `name`: A szöveg, amely a menüelem szolgál: "Nyomtassa ki a konzol paramétereit."
   * `action`: A menüelem, így mindig egy névtelen-függvény, amely két argumentumot tartozó művelet. Ebben az esetben az argumentumok a böngésző konzolablakába vannak írva:
      * `fromTime`: A `from` ecset kijelölés időbélyegző.
      * `toTime`: A `to` ecset kijelölés időbélyegző.

2. Az ecsetműveletek egy másik diagrambeállítási tulajdonságként vannak hozzáadva. Figyelje meg a `linechart.Render` hívásnak átadott `brushContextMenuActions: brushActions` tulajdonságot.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Vonaldiagram a helyi menü ecsetekkel torta-vagy sávdiagram létrehozása](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Bejelentkezés, és a TSI-mintaalkalmazás és annak forrásának vizsgálata.
> * API-k használata a TSI JavaScript ügyfélkódtárban.
> * A JavaScript használata diagramvezérlők létrehozásához és kitöltéséhez TSI-adatokkal.

Ahogy látható, a TSI mintaalkalmazás bemutató adatkészlet. Ha szeretné megtudni, hogy hogyan hozhatja létre saját TSI-környezetét és -adatkészletét, lépjen tovább a következő cikkre:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Time Series Insights-környezet létrehozása](tutorial-create-populate-tsi-environment.md)

Vagy a TSI minta alkalmazás forrásfájljait megtekintése:

> [!div class="nextstepaction"]
> [A TSI mintaalkalmazás-adattár](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Olvassa el a TSI-ügyfél API referenciadokumentációt tartalmaz:

> [!div class="nextstepaction"]
> [A TSI-API dokumentációja](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
