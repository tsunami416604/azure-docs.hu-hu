---
title: 'Oktatóanyag: Ismerkedés az Azure Time Series Insights JavaScript ügyféloldali kódtár |} A Microsoft Docs'
description: Megismerheti az Azure Time Series Insights JavaScript ügyfélkódtárat és a kapcsolódó programozási modellt.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: c6cfd2069851138d738b1533eaab74d9d7aedda6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243985"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Oktatóanyag: Az Azure Time Series Insights JavaScript ügyfélkódtár felderítése

Az Azure Time Series Insights JavaScript D3-alapú ügyféloldali kódtár célja a webes fejlesztőknek lekérdezés és a Time Series Insights-ban tárolt adatok megjelenítése. Ez az oktatóanyag végigvezeti a Time Series Insights-klienskódtár és a programozási modell üzemeltetett mintaalkalmazás használatával.

Az oktatóanyag részletesen használata a könyvtárban, a Time Series Insights-adatok elérése és használata a diagram-vezérlőelemeket jelennek meg, és megjelenítheti az adatokat. Kísérletezzen a különböző típusú diagramok adatok vizualizációja, hogyan is megtudhatja. Az oktatóanyag végén meg fogja tudni használni az ügyféloldali kódtár a Time Series Insights funkciókat építhet be a saját webes alkalmazás.

Pontosabban a következőkről szerezhet ismereteket:

> [!div class="checklist"]
> * A Time Series Insights-mintaalkalmazás
> * A Time Series Insights JavaScript-klienskódtár
> * Hogyan a mintaalkalmazás a kódtárat használja a Time Series Insights-adatok megjelenítése

> [!NOTE]
> * Az oktatóanyagban egy ingyenes, üzemeltetett [Time Series Insights web bemutatót](https://insights.timeseries.azure.com/clientsample).
> * A Time Series Insights minta app forrásfájlok szerepelnek a [GitHub-mintaadattárból](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Olvassa el a [ügyfél Time Series Insights dokumentációja](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Videó

Ebben a videóban hogy a nyílt forráskódú Time Series Insights JavaScript SDK vezetnek be:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a böngésző **fejlesztői eszközök** funkció. Modern böngészők ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/), és mások) általában biztosítanak hozzáférést az **Webes vizsgáló nézet** az F12 billentyűparancsot a billentyűzet használatával. A nézet hozzáféréséhez másik módja, kattintson a jobb gombbal valamelyik weblapon, és válassza a **vizsgálata elem**.

## <a name="time-series-insights-sample-application"></a>A Time Series Insights mintaalkalmazás

Ez az oktatóanyag során használjuk egy ingyenes, üzemeltetett Time Series Insights mintaalkalmazást megismerheti az alkalmazás mögötti forráskódját, valamint a Time Series Insights JavaScript ügyféloldali kódtár megismerése. A mintaalkalmazás segítségével, megtudhatja, hogyan kommunikáljanak a Time Series Insights JavaScript és diagramokat és ábrákat keresztül adatok megjelenítése.

1. Nyissa meg a [Time Series Insights mintaalkalmazás](https://insights.timeseries.azure.com/clientsample). A következő bejelentkezési üzenet jelenik meg:

   [![Time Series Insights ügyfél minta bejelentkezési kérések jelenhetnek](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Válassza ki **bejelentkezés** adja meg vagy válassza ki a hitelesítő adatait. Használhatja egy vállalati szervezeti fiók (az Azure Active Directory), vagy egy személyes fiók (Microsoft-fiókkal).

   [![Time Series Insights ügyfél minta hitelesítő adatok kérése](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Miután bejelentkezett, a Time Series Insights adatokkal feltöltve diagramok megjelenítő lap jelenik meg. A felhasználói fiók és a **Kijelentkezés** hivatkozás a jobb felső sarokban láthatók:

   [![Time Series Insights ügyfél minta főoldala a bejelentkezést követően](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Oldal forrása és struktúrája

Először tekintse a [HTML és JavaScript forráskódját](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) a megjelenített weblap:

1. Nyissa meg a böngészőben a **Fejlesztői eszközök** lapot. Vizsgálja meg az aktuális oldalt alkotó HTML-elemeket, más néven a HTML- vagy DOM-faszerkezetet.

1. Bontsa ki a `<head>` és `<body>` elemeket, és vizsgálja meg a következő szakaszokban:

   * Alatt a `<head>` elem, megtalálhatja lap metaadatok és a függőségek, amelyek lehetővé teszik az alkalmazás futtatását:
     * A `<script>` elem, amely az Azure Active Directory Authentication Library (ADAL) fájl hivatkozni *adal.min.js*. Az ADAL egy JavaScript-kódtár, amely OAuth 2.0 hitelesítést (bejelentkezést) és az API-k hozzáféréséhez jogkivonat beszerzését biztosítja.
     * Több `<link>` stíluslapok elemei (más néven *CSS*) például *sampleStyles.css* és *tsiclient.css*. A stíluslapok visual lap stílusának részleteit, például a színeket, betűtípusokat és térköz szabályozza.
     * A `<script>` elem, amely a Time Series Insights JavaScript ügyféloldali kódtár hivatkozni *tsiclient.js*. Az oldal kódtárat használja, a Time Series Insights szolgáltatás API-k hívásához és megjelenítése a lapon diagram-vezérlőelemeket.

     >[!NOTE]
     > * Az az ADAL JavaScript-kódtár forráskódja elérhető a [azure-activedirectory-erőforrástár-az-js-tárház](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * A Time Series Insights JavaScript ügyféloldali kódtár forráskódját érhető el a [tsiclient tárház](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * Alatt a `<body>` elemben találja `<div>` elemeket, amelyek segítséget nyújtanak a elemek elrendezésének a lapon, és a egy másik `<script>` elem:
     * Az első `<div>` elem azt határozza meg a **bejelentkezés** párbeszédpanel (`id="loginModal"`).
     * A második `<div>` elem a következő szülőjeként működik:
       * Egy `<div>` fejléc elem, amely állapotüzenetekre és bejelentkezési információkra szolgál az oldal tetején (`class="header"`).
       * A `<div>` elem a szervezet elemei, beleértve a diagramokat további részében (`class="chartsWrapper"`).
       * A `<script>` a JavaScript, amellyel szabályozhatja a lapot tartalmazó szakasz.

   [![Time Series Insights ügyfél minta fejlesztői eszközökkel](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Bontsa ki a `<div class="chartsWrapper">` elemhez az itt található több alárendelt `<div>` elemeket. Ezekkel helyezhetők el az egyes diagramvezérlő példák. Nincsenek számos `<div>` elemeket, és az egyik minden egyes diagram például:

   * Az első (`class="rowOfCardsTitle"`) elem egy leíró címet tartalmaz, amely összegzi a diagramok által bemutatott elemeket. Például:`Static Line Charts With Full-Size Legends.`
   * A második (`class="rowOfCards"`) elem egy szülő további alárendelt tartalmazó `<div>` elemeket, amelyeket a tényleges diagram-vezérlőelemeket egy soron belüli pozíció.

   [![Törzs div elemek](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Bontsa ki a `<script type="text/javascript">` elem, amely közvetlenül az alábbi a `<div class="chartsWrapper">` elemet. A lapszintű JavaScript szakasz elején segítségével kezelhető az összes lap logikát (hitelesítés, a Time Series Insights szolgáltatás API-k, a diagram-vezérlőelemeket, és az egyéb renderelő hívása):

   [![Parancsprogram törzse](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Time Series Insights JavaScript ügyfél könyvtár fogalmak

A Time Series Insights ügyféloldali kódtár (*tsclient.js*) absztrakt entitást két fontos JavaScript funkciókat biztosítja:

* **Az idő Series Insights lekérdezési API-k meghívására szolgáló burkoló módszerek**: REST API-k használatával adatokat kérdezhet le a Time Series Insights összesítő kifejezések használatával. A metódusok a könyvtár a TsiClient.Server névtérben vannak rendszerezve.

* **Módszerek a létrehozása és feltöltése számos különböző típusú vezérlők diagramkészítési**: Jelennek meg a Time Series Insights segítségével módszert származó adatokat egy weblap. A metódusok a könyvtár a TsiClient.UX névtérben vannak rendszerezve.

Ezen egyszerűbb fejlesztők egyszerűbben fejleszthetnek felhasználói felületi graph és a diagram összetevőket, amelyek a Time Series Insights adatait működteti.

### <a name="authentication"></a>Hitelesítés

A [Time Series Insights mintaalkalmazás](https://insights.timeseries.azure.com/clientsample) egy egyoldalas alkalmazás, amely rendelkezik az ADAL OAuth 2.0-s felhasználói hitelesítés támogatását:

1. Adal-t használnak a hitelesítéshez, ha az ügyfél alkalmazást regisztrálni kell az Azure Active Directoryban (Azure AD). Sőt, az egyoldalas alkalmazás használatára regisztrálva van a [OAuth 2.0 implicit folyamat megadása](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Az alkalmazás egyes futásidőben regisztrációs tulajdonságokat kell megadnia. A tulajdonságok között az ügyfél GUID Azonosítóját (`clientId`) és az átirányítási URI-t (`postLogoutRedirectUri`).
1. Később, kér az alkalmazás egy *hozzáférési jogkivonat* Azure AD-ből. A hozzáférési jogkivonat kiadott engedélyek egy adott szolgáltatás vagy az API-azonosító véges készletének (https:\//api.timeseries.azure.com). A jogkivonat engedélyei a bejelentkezett felhasználó nevében vannak kiadva. A szolgáltatás vagy -API esetében az azonosító egy másik tulajdonság, amely szerepel az alkalmazás Azure AD-regisztrációs.
1. Adal-t a hozzáférési jogkivonatot az alkalmazás ad vissza, miután átadva egy *tulajdonosi jogkivonat* mikor fér hozzá a Time Series Insights szolgáltatás API-k.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> A Microsoft által támogatott az Azure AD hitelesítési kódtárai kapcsolatos további információkért tekintse meg a [Azure Active Directory Authentication Library dokumentációjában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>A vezérlők azonosítása

A megadott példában `<div>` elemek rendezve jelennek meg a szülő `<body>` jelentenek észszerű elrendezés a diagram azt szabályozza, hogy az oldalon renderelési elemet.

Minden egyes `<div>` elem azt határozza meg, az elhelyezési és a diagram-vezérlőelemeket visual attribútumai tulajdonságait. HTML-elemmel `id` tulajdonságai jelennek meg, és frissítse az adott vezérlők kötése egyedi azonosítók ábrázolt adatok szolgálnak.

### <a name="aggregate-expressions"></a>Összesítő kifejezések

A Time Series Insights-ügyfélkódtár API-k összesítő kifejezések használata:

* Egy összesítő kifejezés lehetővé teszi a hozhat létre egy vagy több *keresési feltételek*.

* Az ügyfél API-kat úgy tervezték, hogy egy másik bemutató alkalmazás hasonló funkciókat nyújtanak (a [Time Series Insights explorer](https://insights.timeseries.azure.com/demo)), amely a keresendő az időtartományt, `where` predikátumok, intézkedéseket, és `splitBy` értékeket.

* A legtöbb ügyfélkódtár API-k a szolgáltatás által használt adatok a Time Series Insights-lekérdezés összeállításához összesítő kifejezések tömbjét igénybe vehet.

### <a name="call-pattern"></a>Hívásmintázat

Diagram-vezérlőelemeket leképezése után rögtön hozzáláthat egy általános mintát követi. Figyelheti, az általános minta során a mintaalkalmazást, és segítséget nyújthatnak az ügyféloldali kódtár használatakor:

1. Deklaráljon egy `array` tárolásához legalább egy Time Series Insights összesítő kifejezések:

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
   | `predicateObject` | Az adatok szűrési kifejezés |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | A mérték használt tulajdonság neve | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | A kívánt mértéket tulajdonság összesítések | `['avg', 'min']` |
   | `searchSpan`      | Az összesítő kifejezést időtartama és időköze mérete | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | A felosztás kívánt karakterlánc-tulajdonságot (nem kötelező: null is lehet) | `{property: 'Station', type: 'String'}` |
   | `color`         | A megjeleníteni kívánt objektumokra színe | `'pink'` |
   | `alias`           | Egy rövid nevet a összesítő kifejezés | `'Factory3Temperature'` |
   | `contextMenuActions` | Műveletek kell kötni egy vizualizációt (nem kötelező) az idő sorozat objektumok tömbje | További információkért lásd: [felugró helyi menük](#pop-up-context-menus). |

1. A Time Series Insights-lekérdezés hívja meg az összesített adatok kérése a TsiClient.Server API-k használatával:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates paraméterek**

   | Paraméter | Leírás | Példa |
   | --------- | ----------- | ------- |
   | `token`     | A hozzáférési jogkivonatot a Time Series Insights API-hoz |  `authContext.getTsiToken()`<br />További információért lásd: [Hitelesítés](#authentication). |
   | `envFQDN`   | A Time Series Insights-környezet teljesen minősített tartománynevét (FQDN) | Az Azure Portalról. Például: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | A Time Series Insights lekérdezési kifejezések tömbje | Használja a `aes` változót a korábban leírtaknak megfelelően: `aes.map(function(ae){return ae.toTsx()}`. |

1. A tömörített eredmény JSON-ba visszaadott a Time Series Insights-lekérdezés, a Vizualizáció átalakítása:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Hozzon létre egy diagram vezérlőelem a TsiClient.UX API-k használatával. Az egyik kösse a `<div>` elemeket az oldalon:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Feltölti az átalakított JSON-adatok objektumok a diagram vezérlőelem, és a vezérlő az oldalon jelennek meg:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Vezérlők megjelenítése

A Time Series Insights-klienskódtára biztosítja az egyedi, a-beépített elemzési nyolc vezérlők:

* **Vonaldiagram**
* **Tortadiagram**
* **Sávdiagram**
* **heatmap**
* **hierarchia vezérlők**
* **elérhető rács**
* **különálló esemény ütemtervek**
* **állapot átmeneti ütemtervek**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Tortadiagram példák, vonaldiagram és sávdiagram

Tekintse meg a bemutatót kódot, amely néhány szokásos diagram vezérlőelem előállítására szolgál. Vegye figyelembe a programozási modell és a vezérlőn létrehozásához. Pontosabban, vizsgálja meg a HTML formátumú, területen a `// Example 3/4/5` megjegyzést, amely szabályozza a HTML-lel rendereli `id` értékek `chart3`, `chart4`, és `chart5`.

A 3. lépés a visszahívás a [lapon a forrás- és struktúra szakasz](#page-source-and-structure) , hogy az oldalon sorok rendezve jelennek meg diagram-vezérlőelemeket. Minden egyes diagram vezérlőelem rendelkezik egy leíró nevet sort. Ebben a példában a három diagramok alatt a rendszer kitölti a `Multiple Chart Types From the Same Data` cím `<div>` elem alatt, és a három kötött `<div>` elemek, amelyek a cím alatt:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A JavaScript-kódot az alábbi szakasz használ a fentebb vázolt minták: a Time Series Insights összesítő kifejezések felépítéséhez létre, ezek segítségével a Time Series Insights-adatok lekérdezése és majd jelennek meg a három diagramok. A tsiClient.ux névtérből használt három diagram típusa: `LineChart`, `BarChart`, és `PieChart`. A diagram típusokat hozhat létre és jelennek meg a megfelelő diagramokat szolgálnak. Minden három diagram használhatja ugyanazokat az adatokat az összesítő kifejezés `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

A három diagram a következőképpen jelenik meg a rendereléskor:

[![Több diagramtípus ugyanazokból az adatokból](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Speciális funkciók

A Time Series Insights ügyféloldali kódtár néhány további funkcióval rendelkezik, amelyeket adatvizualizációkat kreatívan végrehajtásához használhatja.

### <a name="states-and-events"></a>Állapotok és események

Egy speciális funkciója lehetővé teszi, hogy állapotváltásra és diszkrét események diagramok. Ez a funkció hasznos kiemelését incidensek, a riasztások és az állapot kapcsolók létrehozása (be/ki, például kapcsolók).

Tekintse meg a kódot a környező a `// Example 10` megjegyzést. A kód jelenik meg a cím alatt egy sor vezérlő `Line Charts with Multiple Series Types` és köti, hogy a `<div>` elem a HTML-lel `id` érték `chart10`.

A folyamat a következő lépésekből áll:

1. A struktúra nevű `events4` van definiálva, amely tárolja az állapotváltozás elemek nyomon követéséhez. A struktúra a következőket tartalmazza:

   * Egy `Component States` nevű sztringkulcs.
   * Az állapotokat jelző értékobjektumok tömbje. Mindegyik objektum a következőket tartalmazza:
     * Egy JavaScript ISO időbélyeget tartalmazó sztringkulcs.
     * Az állapot jellemzőit (szín, leírás) tartalmazó tömb.

1. A `events5` struktúra van definiálva `Incidents`, ez az esemény elemeit nyomon követése egy tömbjét tartalmazza. A tömb struktúrája ugyanolyan alakú, mint az `events4` elemhez leírt struktúra.

1. A vonaldiagram jelenik meg, és a rendszer átadja a diagram beállításai a két struktúrák paraméterek: `events:` és `states:`. Vegye figyelembe a többi beállítás paraméter megadására a `tooltip:`, `theme:`, vagy `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

A gyémánt jelölők/pop-ra windows, amelyek jelzik az incidensek és a színes sávok/pop-ra windows az időskálán jelzi az állapotváltozásokat:

[![Vonaldiagramok esetében, több adatsorozat-típus](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Előugró helyi menük

Egy másik speciális funkciója lehetővé teszi egyéni helyi menük (kattintson a jobb gombbal a legördülő menük) létrehozását. Ezekkel engedélyezhetők különböző műveletek és a következő logikus lépések az alkalmazás hatókörén belül.

Tekintse meg a kódot körül a `// Example 13/14/15` megjegyzést. Ez a kód először egy vonaldiagramot a cím alatt jelenik meg `Line Chart with Context Menu to Create Pie/Bar Chart`. A diagram van kötve a `<div>` elem a HTML-lel `id` érték `chart13`.

A helyi menükkel a vonaldiagram lehetővé teszi torta- és sávdiagramok dinamikus létrehozását, a `chart14` és `chart15` azonosítókkal rendelkező `<div>` elemekhez kötve azokat. A tortadiagram mind az oszlopdiagramot is használhatja a helyi menük saját szolgáltatások engedélyezése: képes adatokat másol a tortadiagram a vonaldiagramon, és nyomtassa ki a sávdiagram adatai, a böngésző konzolablakra jelölik.

A folyamat a következő lépésekből áll:

1. Az egyéni műveletek sorozata vannak meghatározva. Mindegyik művelet egy egy vagy több elemből álló tömböt tartalmaz. Mindegyik elem egyetlen helyimenü-elemet határoz meg:

   * `barChartActions`: Ez a művelet a helyi menü meghatározásához egy elem egy elemet tartalmaz kördiagram határozza meg:
     * `name`: A szöveg, amely a menüelem szolgál: "Nyomtassa ki a konzol paramétereit."
     * `action`: A menüelem tartozó művelet. Ez a művelet mindig egy névtelen funkció, amely három argumentumot vesz fel a diagram létrehozásához használt összesítő kifejezés alapján. Ebben az esetben az argumentumok a böngésző konzolablakába vannak írva:
       * `ae`: Az összesítő kifejezés tömb.
       * `splitBy`: A `splitBy` értéket.
       * `timestamp`: Az időbélyeg.

   * `pieChartActions`: Ez a művelet az oszlopdiagramot, meghatározásához egy elem egy elemet tartalmazó helyi menüjére határozza meg. Az alakzat és a séma megegyezik a `barChartActions` elem a korábban ismertetett, de a `action` jelentősen eltér a függvény: azt példányosítja, és rendereli a sávdiagram. A `ae` argumentumként átadott futásidőben a menüelem megnyitásakor az összesítő kifejezés tömb meghatározására szolgál. A funkció az `ae.contextMenu` tulajdonságot is beállítja a `barChartActions` helyi menüvel.
   * `contextMenuActions`: Ez a művelet határozza meg a helyi menüben a vonaldiagram, amelyek három-három menü elemeinek definiálásához elemeket tartalmazza. Az alakzat és a séma minden elemhez ugyanaz, mint a korábban leírt elemeket. Akárcsak a `barChartActions` elem, az első elem a böngésző konzolablakába írja a három funkcióargumentumot. Hasonló a `pieChartActions` elem, a második két elemet hozza létre és jelennek meg a kördiagram és a sávdiagramon jelölik. A második két elem az `ae.contextMenu` tulajdonságukat is beállítja a `pieChartActions` és `barChartActions` helyi menükkel.

1. Két összesítő kifejezések leküld az alakzatot a `aes` összesítő kifejezés tömb. Adja meg, hogy a `contextMenuActions` tömböt az egyes elemekhez. Ezeket a kifejezéseket a rendszer a vonaldiagram vezérlőjével használja.

1. Csak a vonaldiagram kezdetben jelenik meg, amelyből diagram és az oszlopdiagramot is megjeleníthetők-e futásidőben.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Az alábbi képernyőfelvételen a megfelelő felugró helyi menük a diagramokat. A tortadiagram és sávdiagram dinamikusan létrehozott sorban diagram környezeti menü beállítások használatával.

[![Vonaldiagram, tortadiagram és sávdiagram létrehozása a helyi menü](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Ecsetek

Ecsetek hatókörének meghatározásához műveleteket hajthat végre, például nagyításra, és ismerje meg egy időtartományt használhatja.

A kódot, amely mutatja be ecsetek látható a `Line Chart with Context Menu to Create Pie/Bar Chart` példa, amely a felugró helyi menük ismerteti.

* Az ecsetműveletek abból a szempontból hasonlítanak a helyi menükhöz, hogy egy egyéni műveletsorozatot határoznak meg az ecsethez. Minden művelet egy tömb, amely rendelkezik egy vagy több elemet tartalmaz. Mindegyik elem egyetlen helyimenü-elemet határoz meg:
   * `name`: A szöveg, amely a menüelem szolgál: "Nyomtassa ki a konzol paramétereit."
   * `action`: A menüelem, így mindig egy névtelen-függvény, amely két argumentumot tartozó művelet. Ebben az esetben az argumentumok a böngésző konzolablakába vannak írva:
     * `fromTime`: A `from` ecset kijelölés időbélyegző.
     * `toTime`: A `to` ecset kijelölés időbélyegző.

* Az ecsetműveletek egy másik diagrambeállítási tulajdonságként vannak hozzáadva. A `brushContextMenuActions: brushActions` tulajdonság átadott a `linechart.Render` hívja.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Vonaldiagram, tortadiagram és sávdiagram létrehozása ecsetek használatával a helyi menü](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Jelentkezzen be, és Fedezze fel a Time Series Insights-mintaalkalmazást, és a forrás
> * A Time Series Insights JavaScript ügyféloldali kódtár API-k használata
> * A JavaScript használatával létrehozása és feltöltése a diagram-vezérlőelemeket a Time Series Insights adatait

A Time Series Insights mintaalkalmazás egy demó-adatkészletet használja. Ha szeretné megtudni, hogyan hozhat létre a saját Time Series Insights környezetet és az adatkészlet, olvassa el a következő cikket:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Time Series Insights-környezet létrehozása](tutorial-create-populate-tsi-environment.md)

Vagy a Time Series Insights minta alkalmazás forrásfájljait megtekintése:

> [!div class="nextstepaction"]
> [Time Series Insights mintaalkalmazás-adattár](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Olvassa el a Time Series Insights-ügyfél API referenciadokumentációt tartalmaz:

> [!div class="nextstepaction"]
> [Time Series Insights-API dokumentációja](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
