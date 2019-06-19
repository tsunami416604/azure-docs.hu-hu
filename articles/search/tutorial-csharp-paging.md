---
title: C#Az oktatóanyag a keresési eredmények tördelés – Azure Search
description: Ebben az oktatóanyagban az "Az első alkalmazás létrehozása – Azure Search" projekt a kiválasztott lapozási két típusú épül. Az első oldal száma gomb, valamint első, ezután előző, számos használ, és az utolsó oldal gombokat. A második lapozási rendszert használ, végtelen görgethető, a függőleges görgetősáv áthelyezése az alsó határ által kiváltott.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 8820794382a377cdd3907327dc9c82cc6451e2d4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166833"
---
# <a name="c-tutorial-search-results-pagination---azure-search"></a>C#Oktatóanyag: Keresési eredmények tördelés – Azure Search

Ismerje meg, hogyan valósíthat meg két különböző személyhívó, az első oldal számokat és a második a végtelen görgethető alapján. Mindkét lapozási rendszerek széles körben használják, és kiválasztja a megfelelőt függ, hogy a felhasználói élményt szeretné az eredményeket. Ebben az oktatóanyagban létrehozott a projektbe a lapozófájl rendszerek hoz létre a [ C# oktatóanyag: Hozzon létre első alkalmazását – Azure Search](tutorial-csharp-create-first-app.md) oktatóanyag.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A rendszer számokból álló lapozási alkalmazások kiterjesztése
> * Az alkalmazás végtelen görgethető kiterjesztése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

Rendelkezik a [ C# oktatóanyag: Hozzon létre első alkalmazását – Azure Search](tutorial-csharp-create-first-app.md) projektet, majd futtassa. Ez a projekt a saját verzióját, vagy telepítheti a Githubról: [Első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>A rendszer számokból álló lapozási alkalmazások kiterjesztése

Számozott lapozási kiválasztása a fő internetes keresők és a legtöbb más keresési webhelyek lapozási rendszer. Számozott lapozási általában tartalmaz egy "következő" és "előző" beállítás mellett, a tényleges oldalszámot széles. Is egy "első oldal" és "utolsó oldal" beállítás is elérhető. Ezek a beállítások biztosan alapú eredmények végiglépkedhet egy felhasználó felügyeleti lehetőséget biztosítanak.

Adjuk hozzá a rendszer, amely tartalmazza az első, az előző, tovább, és együtt oldalszám, amely 1-től, nem indulnak el, utolsó lehetőségei, de ehelyett legyen az aktuális oldal a felhasználó a (így például, ha a felhasználó fér hozzá a 10., talán oldalszám 8 lap 9, 10, 11 és 12 jelenik meg).

A rendszer lesz kellően rugalmas ahhoz, hogy egy globális változó állítani látható oldalon számok száma.

A rendszer kezeli a bal szélső és a jobb szélső oldal száma gombok, különleges, ami azt jelenti, elindítja a megjelenített oldalszám tartományának módosítása. Például ha oldalszám, 8, 9, 10, 11, és 12 jelennek meg, és a felhasználó a 8-ban, majd oldalszám tartományán fog megjelenni módosításokat a 6, 7, 8, 9 és 10. És nincs egy hasonló shift jobb, ha kiválasztották 12.

### <a name="add-paging-fields-to-the-model"></a>Lapozófájl mezőket ad hozzá a modell

Az alapszintű kereséssel lap megoldás nyissa meg a rendelkezik.

1. Nyissa meg a SearchData.cs modellfájl.

2. Először adjon hozzá a globális változókat. A globális változók az mvc-ben, a saját statikus osztályban deklarált. **ResultsPerPage** eredmények száma oldalanként számát. **MaxPageRange** határozza meg a nézetben látható oldalon számok számát. **PageRangeDelta** határozza meg, hány oldal bal vagy jobb oldal tartomány kell megjelenítjük, a bal szélső, vagy a jobb szélső oldalszám kiválasztásakor. Általában ez utóbbi a szám van-e körül fele **MaxPageRange**. Adja hozzá a következő kódot a névteret.

```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
```

>[!Tip]
>Ha futtatja a projektet az egy kisebb képernyő, például egy hordozható eszközön megfontolása módosítása **ResultsPerPage** 2-re.

3. Lapozófájl tulajdonságok hozzáadása a **SearchData** osztályhoz, például ha a **Keresettszöveg** tulajdonság.

```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
```

### <a name="add-a-table-of-paging-options-to-the-view"></a>A nézet a lapozási beállításai tábla hozzáadása

1. Nyissa meg az index.cshtml fájl, és adja hozzá a következő kódot közvetlenül a záró előtt &lt;/body&gt; címke. Az új kódot jeleníti lapozási beállításai: első, az előző, 1, 2, 3, 4, 5, ezután a legutóbbi.

```cs
@if (Model != null && Model.pageCount > 1)
{
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&lt;</p>
                }
            </td>

            @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
            {
                <td>
                    @if (Model.currentPage == pn)
                    {
                        // Convert displayed page numbers to 1-based and not 0-based.
                        <p class="pageSelected">@(pn + 1)</p>
                    }
                    else
                    {
                        <p class="pageButton">
                            @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;|</p>
                }
            </td>
        </tr>
    </table>
}
```

Egy HTML-táblázat segítségével eligazíthatja igazítása a dolgokat. Azonban az összes művelet származik a @Html.ActionLink utasítások, a vezérlő az hívása egy **új** olyan különböző bejegyzések létrehozott modellt a **lapozófájl** korábban hozzáadott tulajdonság.

Az első és utolsó oldal beállításai nem küldenek például a "first" és "last" karakterláncok, de ehelyett küldése a megfelelő oldal számokat.

2. Néhány lapozási osztályok hozzáadása a listához, a HTML-stílusok a hotels.css fájlban. A **pageSelected** osztály-e az oldal a felhasználó jelenleg megtekinti (bekapcsolásával a szám félkövér) azonosítására a lap számokat tartalmazó lista.

```cs
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
```

### <a name="add-a-page-action-to-the-controller"></a>Egy lap művelet hozzáadása a vezérlőhöz

1. Nyissa meg a HomeController.cs fájlban, és adja hozzá a **oldal** művelet. Ez a művelet válaszol a kijelölt oldal beállításokat.

```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
```

A **RunQueryAsync** metódus mostantól megjelenik egy szintaktikai hiba miatt a harmadik paraméter, és hogy fognak érkezni, a jelző bit.

> [!Note]
> A **TempData** hívások értéket tárolni (egy **objektum**) az ideiglenes tároló, azonban ez a tároló továbbra is fennáll a _csak_ egyetlen hívásával. Hiba az ideiglenes adatok tárolása, ha a következő hívást egy vezérlő művelet elérhető lesz, de fog legtöbb mindenképp kell eltűnt a hívás által ezt követően! Miatt ez rövid gyűjteményszintű tárolása a keresett szöveget, majd az ideiglenes tároló minden hívást lapozási tulajdonságai újból **oldal**.

2. A **Index(model)** művelet igényeinek megfelelően frissíteni az ideiglenes változók tárolására, majd adja hozzá az oldal bal szélső paramétert a **RunQueryAsync** hívja.

```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
```

3. A **RunQueryAsync** metódus igények jelentős mértékben frissítve. Használjuk a **kihagyása**, **felső**, és **IncludeTotalResultCount** mezőit a **SearchParameters** csak egy oldalon tekinthető meg kérelem osztályt eredmények, kezdve a **kihagyása** beállítás. Emellett kiszámításához szükséges a nézet a lapozófájl változókat. Cserélje le a teljes módszer a következő kóddal.

```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
                   SearchMode = SearchMode.All,

                   // Skip past results that have already been returned.
                   Skip = page * GlobalVariables.ResultsPerPage,

                   // Take only the next page worth of results.
                   Top = GlobalVariables.ResultsPerPage,

                   // Include the total number of results.
                   IncludeTotalResultCount = true,
               };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(leftMostPage + GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            return View("Index", model);
        }
```

4. Végezetül össze kell, hogy módosítsa a nézetet. A változó **resultsList.Results.Count** mostantól tartalmazza a nem teljes száma egy 3-ik (ebben a példában), a visszaadott eredmények száma. Mivel elkészülünk a **IncludeTotalResultCount** TRUE értéket kap, a változó **resultsList.Count** mostantól tartalmazza az eredmények teljes száma. Ezért keresse meg, ahol megjelenik a találatok száma a nézetben, és módosítsa a következő kódot.

```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
```

> [!Note]
> Van teljesítmény találat, bár általában nem sokkal az egyik beállításával **IncludeTotalResultCount** igaz, ez a mennyiség számítható ki az Azure Search az igényei szerint. Az összetett adatkészletek van arról, hogy a visszaadott érték nem egy _előállításához_. Az adatokhoz, Szálloda lesz pontos.

### <a name="compile-and-run-the-app"></a>Fordítsa le és futtassa az alkalmazást

Most válassza **Start Without Debugging** (vagy nyomja le az F5 billentyűt).

1. Keresse meg, amelyek segítségével (például "Wi-Fi") eredményeket rengeteg szöveget. Tudja meg lapon eligazíthatja az eredmények között?

    ![Számozott átlapozva "készlet" eredmények](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Próbálja meg kattintson a jobb szélső, és újabb verziók, bal szélső oldalszámot. Hajtsa végre a oldalszám módosítsa megfelelően középre az oldalon, a?

3. A "first" és "last" beállítás hasznosak? Néhány népszerű webes keresések használja az alábbi beállításokat, és mások azonban nem.

4. Ugrás az utolsó eredmények oldalát. A utolsó megjelenő lap, csak egy lapja, amely tartalmazhat kevesebb mint **ResultsPerPage** eredményeket.

    ![Utolsó oldalán "Wi-Fi" vizsgálata](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Írja be a "város", és kattintson a Keresés gombra. Nincs lapozási beállításai Ha kevesebb mint egy oldalon tekinthető meg eredmények jelennek meg.

    ![A "város" keresése](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Most mentse ki a projektet, és próbáljuk ki a lapozófájl az ilyen típusú helyett.

## <a name="extend-your-app-with-infinite-scrolling"></a>Az alkalmazás végtelen görgethető kiterjesztése

Végtelen görgethető akkor aktiválódik, ha egy felhasználó jobbra görget egy függőleges görgetősáv utolsó, az éppen megjelenített eredményeket. Ebben az esetben a kiszolgálón egy hívást kezdeményez az eredmények a következő oldalhoz tartozó. Nincsenek további eredmények, ha nem ad vissza semmit, és a függőleges görgetősáv nem változik. Ha nincsenek további találatok, azok hozzáfűzve az aktuális oldalon, és a görgetősáv sáv vált, hogy további eredmények érhetők el.

A fontos pontja, hogy a stránky zobrazené nem módosul, de hozzáfűzi a új eredményeivel. A felhasználó biztonsági mentést az első találat a keresési mindig görgetve.

Végtelen görgethető implementálásához kezdjük a projekt előtt oldal száma görgethető elemek lettek hozzáadva. Tehát ha kell, győződjön meg arról, az alapszintű kereséssel oldal egy másik példánya a Githubról: [Első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Lapozófájl mezőket ad hozzá a modell

1. Először adja hozzá a **lapozófájl** tulajdonságot a **SearchData** osztály (a SearchData.cs modell fájlban).

```cs
        // Record if the next page is requested.
        public string paging { get; set; }
```

Ez a változó nincs egy karakterláncot, amely rendelkezik "Tovább" gombra. Ha a következő lapra az eredmények kell küldeni, vagy lehet egy keresés az első oldalhoz tartozó null értékű.

2. Ugyanebben a fájlban, és a névtéren belül adjon hozzá egy globális változó osztály egyetlen tulajdonsággal. A globális változók az mvc-ben, a saját statikus osztályban deklarált. **ResultsPerPage** eredmények száma oldalanként számát. 

```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>A nézet ad hozzá egy függőleges görgetősáv

1. Keresse meg a szakasz az index.cshtml fájl, amely megjeleníti az eredményeket (kezdődik a  **@if (modell! = null)** ).

1. A szakasz cserélje le az alábbi kódot. Az új **&lt;div&gt;** szakaszban van körül, amely görgethető kell lennie, és mindkettőt hozzáadja a terület- **túlcsordulás-y** attribútum és a egy hívás egy **onscroll**"scrolled()", a hívott függvény lépések szerint.

```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea("desc", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
```

3. Közvetlenül alatta a hurok után a &lt;/div&gt; címkével, adja hozzá a **görgetéséhez** függvény.

```cs
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
```

A **Ha** utasítás teszteléssel ellenőrzi, hogy ha a felhasználó a függőleges görgetősáv alján rendelkezik görgetéséhez, fent a szkriptben. Ha már hívása a **kezdőlap** nevű művelet történik a vezérlő **tovább**. Nincs egyéb információ van szükség a vezérlő által, a következő oldalon lévő adatokat adja vissza. Ezeket az adatokat ezután van formázva, azonos HTML-stílusok használatával az eredeti oldalra. Ha nem jár eredménnyel, semmi a rendszer hozzáfűzi, és dolgokat maradjon, mivel ezek.

### <a name="handle-the-next-action"></a>Kezeli a következő elvégzendő művelet

Csak három műveletet, amely a vezérlő kell küldeni: az alkalmazás, amely meghívja az első futtató **Index()** , a felhasználó, amely meghívja az első keresési **Index(model)** , és a későbbi További eredmények via hívásainak **Next(model)** .

1. Nyissa meg az otthoni vezérlő fájlt, és törölje a **RunQueryAsync** metódus az eredeti oktatóanyag.

2. Cserélje le a **Index(model)** művelet a következő kóddal. Most már kezeli a **lapozófájl** mezőben null értékű, vagy állítsa be a "Tovább"gombra, és kezeli az Azure Search-hívás.

```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
```

A rendszer számokból álló lapozási metódus hasonlóan használjuk a **kihagyása** és **felső** keresési beállítások csak a szükséges adatok kérelmezése adja vissza.

3. Adja hozzá a **tovább** művelet az otthoni vezérlőhöz. Vegye figyelembe, hogyan adja vissza egy listában, minden egyes Szálloda két elem hozzáadása a listához: egy Szálloda neve és a egy Szálloda leírása. Ebben a formátumban van beállítva, hogy egyezzen a **görgetéséhez** függvény használatát a nézet a visszaadott adatokat.

```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
```

4. Ha szintaktikai hiba **lista&lt;karakterlánc&gt;** , majd adja hozzá a következő **használatával** direktívát a vezérlő fájlt vezetője.

```cs
using System.Collections.Generic;
```

### <a name="compile-and-run-your-project"></a>Fordítsa le és a projekt futtatása

Most válassza **Start Without Debugging** (vagy nyomja le az F5 billentyűt).

1. Adjon meg egy kifejezést adjon eredmények rengeteg (például "-készlet"), és tesztelje a függőleges görgetősáv. Nem, aktiválhat egy új lapra az eredmények?

    ![Végtelen "készlet" eredmények görgetése](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

> [!Tip]
> Győződjön meg arról, hogy egy görgetősáv az első oldal jelenik meg, hogy az eredmények első oldala kissé haladhatja meg a terület, azok megjelennek a magasságát. Ebben a példában **.box1** 30 képpont magasságú **.box2** 100 képpont magasságú _és_ alsó margó 24 képpont. Így minden bejegyzés használ 154 képpont. Három bejegyzést tarthat, amíg 3 x 154 = 462 képpont. Annak érdekében, hogy egy függőleges görgetősáv, a magasság a megjelenítési területen állítson be, amely kisebb, mint 462 képpont, akkor is 461 működik van. A probléma csak akkor történik meg az első oldalon ezt követően a görgetősáv arra, hogy jelennek meg. A sor frissítésére:  **&lt;div azonosító = "myDiv" style = "width: 800 képpont; Height: 450px; túlcsordulás-y: görgessen;"onscroll="scrolled() "&gt;** .

2. Görgessen lefelé egészen a alján az eredményeket. Figyelje meg, hogy az összes információt is tartalmaz egy nézet lapon. Görgessen webkiszolgálóit tetejére bármely kiszolgálói hívások aktiválása nélkül.

Kifinomultabb végtelen görgethető rendszerekkel használható az egér görgetőkerekével, vagy hasonló más mechanizmust, aktiválhat egy új lapra az eredmények betöltését. Azt fogja nem tart végtelen görgethető minden további, az alábbi oktatóanyagok, de ezzel elkerülheti a felesleges kattintások, és vizsgálja meg a további lehetőségekért érdemes rendelkezik egy bizonyos vállalatának rá!

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő takeaways a projekt:

* Számozott lapozási jó keresések, ahol a sorrendjét, az eredményeket az némileg tetszőleges, ami azt jelenti, hogy május jól lehet valami fontos a felhasználók számára a későbbi lapokon.
* Végtelen görgethető akkor jó, ha az eredmények sorrendje akkor különösen fontos. Ha például az eredményeket a Center cél város távolság vannak rendezve.
* Lehetővé teszi a rendszer számokból álló lapozási néhány jobb navigációs. Például egy felhasználó is ne feledje, hogy egy érdekes eredmény volt-e a 6, mivel végtelen görgethető nincs ilyen könnyű hivatkozás létezik.
* Egyszerű jogorvoslat nincs negatívokkal lap számokkal, kattintson a felfelé és lefelé görgethető végtelen görgethető rendelkezik.
* A rendszer végtelen görgethető egyik legfőbb jellemzője, hogy eredményeket egy meglévő lapon nem cseréli az adott oldal, amely hatékony lesz hozzáfűzve.
* Ideiglenes tároló csak egy hívás továbbra is fennáll, és stabilitást biztosít további hívások alaphelyzetbe kell állítani.


## <a name="next-steps"></a>További lépések

Lapozófájl esetében alapvető fontosságú, internetes keresés. A lapozófájl is érintett, a következő lépés az további, a felhasználói élmény javítása gépelés közbeni keresések hozzáadásával.

> [!div class="nextstepaction"]
> [C#Oktatóanyag: Autocompletion és javaslatok – Azure Search hozzáadása](tutorial-csharp-type-ahead-and-suggestions.md)
