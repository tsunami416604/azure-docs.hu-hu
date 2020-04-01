---
title: C# oktatóanyag a keresési eredmények tördeléséhez
titleSuffix: Azure Cognitive Search
description: Ez az oktatóanyag bemutatja a keresési eredmények lapozását. Egy meglévő hotelprojektre épül, a lapozás az első, a következő, az előző, az utolsó és a számozott gombok szerint. A második lapozórendszer végtelen görgetést használ, amelyet egy függőleges görgetősáv nak az alsó határra való mozgatásával vált ki.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 9abfeb54be6e22885b8e973034a6d89df8272146
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121518"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>C# oktatóanyag: Keresési eredmények tördelése - Azure Cognitive Search

Ismerje meg, hogyan valósíthat meg két különböző lapozási rendszert, az első az oldalszámok, a második pedig a végtelen görgetés alapján. Mindkét lapozási rendszer széles körben használatos, és a megfelelő kiválasztása attól függ, hogy milyen felhasználói élményt szeretne az eredményekkel. Ez az oktatóanyag a lapozórendszereket a [C# oktatóanyagban](tutorial-csharp-create-first-app.md) létrehozott projektbe építi: Az első alkalmazás létrehozása – Azure Cognitive Search oktatóanyag.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az alkalmazás kiterjesztése számozott lapozással
> * Bővítse alkalmazását végtelen görgetéssel

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

A [C# oktatóanyag: Hozza létre az első alkalmazást – Az Azure Cognitive Search](tutorial-csharp-create-first-app.md) projekt et bekell hozni és futtatnia kell. Ez a projekt lehet a saját verziója, vagy telepítheti a GitHubról: [Az első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Az alkalmazás kiterjesztése számozott lapozással

Számozott személyhívó a személyhívó rendszer a választás a fő internetes keresőmotorok és a legtöbb más keresési honlapokon. A számozott lapozás általában tartalmaz egy "következő" és "előző" beállítást a tényleges oldalszámok tartományán kívül. Szintén egy "első oldal" és "utolsó oldal" opció is elérhető lehet. Ezek a lehetőségek minden bizonnyal ad a felhasználó ellenőrzése alatt navigálás az oldal-alapú eredményeket.

Adunk hozzá egy rendszert, amely tartalmazza az első, előző, következő és az utolsó lehetőségeket, valamint az oldalszámokat, amelyek nem indulnak 1-től, hanem körülveszik az aktuális oldalt, amelyen a felhasználó van (így például, ha a felhasználó a 10. oldalt nézi, talán 8, 9, 10, 11 és 12 oldalszám jelenik meg).

A rendszer elég rugalmas lesz ahhoz, hogy a látható oldalszámok számát globális változóban lehessen beállítani.

A rendszer a bal szélső és a jobb szélső oldalszám gombokat különlegesként kezeli, ami azt jelenti, hogy a megjelenített oldalszámok tartományának megváltoztatását indítja el. Ha például a 8., 9., 10., 11. És van egy hasonló váltás jobbra, ha a kiválasztott 12.

### <a name="add-paging-fields-to-the-model"></a>Lapozómezők hozzáadása a modellhez

Nyissa meg az alapkeresési oldal megoldását.

1. Nyissa meg a SearchData.cs modellfájlt.

2. Először adjon hozzá néhány globális változót. Az MVC-ben a globális változók deklarálva vannak a saját statikus osztályukban. **Az ResultsPerPage** beállítja az egy oldalonkénti eredmények számát. **A MaxPageRange** határozza meg a nézetben látható oldalszámok számát. **A PageRangeDelta** határozza meg, hogy hány oldalt kell eltolni az oldaltartománytól, ha a bal szélső vagy a jobb oldalszám van kiválasztva. Ez utóbbi szám általában a **MaxPageRange**körülbelül fele. Adja hozzá a következő kódot a névtérbe.

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
    >Ha a projektet kisebb képernyővel rendelkező eszközön, például laptopon futtatja, akkor fontolja meg **a ResultsPerPage 2-re** való módosítását.

3. Adja hozzá a lapozási tulajdonságokat a **SearchData** osztályhoz, például a **searchText** tulajdonság után.

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

### <a name="add-a-table-of-paging-options-to-the-view"></a>Lapozólaptábla hozzáadása a nézethez

1. Nyissa meg az index.cshtml fájlt, és adja &lt;hozzá&gt; a következő kódot közvetlenül a záró /body tag előtt. Ez az új kód egy lapozási beállításokat tartalmazó táblázatot jelenít meg: első, előző, 1, 2, 3, 4, 5, következő, utolsó.

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

    Html táblázatot használunk a dolgok szép igazításához. Azonban az összes művelet @Html.ActionLink származik a nyilatkozatok, minden hívás a vezérlő egy **új** modell létrehozott különböző bejegyzéseket a **lapozó** tulajdonság akorábban hozzáadott.

    Az első és az utolsó oldal beállítás nem az "első" és az "utolsó" karakterláncokat küldi el, hanem a megfelelő oldalszámokat küldi el.

2. Adjon hozzá néhány lapozási osztályt a hotels.css fájl HTML-stílusainak listájához. A **pageSelected** osztály azonosítja azt az oldalt, amelyet a felhasználó éppen megtekint (a szám félkövérré fordításával) az oldalszámok listájában.

    ```html
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

### <a name="add-a-page-action-to-the-controller"></a>Oldalművelet hozzáadása a vezérlőhöz

1. Nyissa meg a HomeController.cs fájlt, és adja hozzá az **Oldal** műveletet. Ez a művelet a kijelölt oldalbeállítások bármelyikére válaszol.

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

    A **RunQueryAsync** metódus most szintaktikai hibát jelenít meg a harmadik paraméter miatt, amelyhez egy kicsit meg fogunk érkezni.

    > [!Note]
    > A **TempData-hívások** egy értéket (egy **objektumot)** tárolnak az ideiglenes tárolóban, bár ez a tároló _csak_ egy hívásesetén marad meg. Ha tárolunk valamit az ideiglenes adatok, akkor elérhető lesz a következő hívás a vezérlő fellépés, de minden bizonnyal elment a hívás után! A rövid élettartam miatt a keresési szöveget és a lapozási tulajdonságokat **Page**minden egyes page-hívásban ideiglenesen tároljuk.

2. Az **Index(modell)** műveletet frissíteni kell az ideiglenes változók tárolásához, és hozzá kell adni a bal szélső lap paramétert a **RunQueryAsync** híváshoz.

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

3. A **RunQueryAsync** metódust jelentősen frissíteni kell. A **SearchParameters** osztály **Skip**, **Top**és **IncludeTotalResultCount** mezőket használva csak egy oldalnyi eredményt kérhetünk, a **Kihagyás** beállítástól kezdve. Ki kell számolnunk a lapozási változókat is a véleményünkhez. Cserélje le a teljes metódust a következő kódra.

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
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            return View("Index", model);
        }
    ```

4. Végezetül, egy kis változtatást kell tennünk a nézeten. Az **resultsList.Results.Count** változó mostantól az egy oldalon visszaadott eredmények számát fogja tartalmazni (a példában 3), nem pedig a teljes számot. Mivel az **IncludeTotalResultCount értéke** igaz, az **resultsList.Count** változó most már tartalmazza az eredmények teljes számát. Így keresse meg, ahol az eredmények száma jelenik meg a nézetben, és módosítsa azt a következő kódot.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Van egy teljesítmény találatot, bár általában nem sok egy, beállításával **IncludeTotalResultCount** true, mivel ezt az összeget kell kiszámítani az Azure Cognitive Search. Összetett adatkészletek nél figyelmeztetés van arra, hogy a visszaadott érték _közelítés._ A szállodai adatok, akkor pontos.

### <a name="compile-and-run-the-app"></a>Az alkalmazás fordítása és futtatása

Most válassza a **Start hibakeresés nélkül** lehetőséget (vagy nyomja le az F5 billentyűt).

1. Keresés néhány szöveget, hogy ad sok eredményt (mint például a "wifi"). Tud oldal szépen át az eredményeket?

    ![Számozott lapozás a "pool" eredmények között](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Kattintson a jobb szélső, majd később a bal szélső oldalszámokra. Megfelelően módosulnak az oldalszámok a bealkalmazásban lévő oldal közepére?

3. Hasznosak az "első" és az "utolsó" opciók? Egyes népszerű internetes keresések ezeket a beállításokat használják, mások nem.

4. Ugrás az eredmények utolsó oldalára. Az utolsó oldal az egyetlen olyan oldal, amely kevesebb, mint **a ResultsPerPage** eredményeket tartalmazhatja.

    ![A "wifi" utolsó oldalának vizsgálata](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Írja be a "város" szót, és kattintson a keresés gombra. Nem jelennek meg lapozási beállítások, ha egynél kevesebb oldalnyi eredmény van.

    ![A "város" kifejezés keresése](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Most mentse ki ezt a projektet, és próbáljunk meg egy alternatívát, hogy ezt a formát a személyhívó.

## <a name="extend-your-app-with-infinite-scrolling"></a>Bővítse alkalmazását végtelen görgetéssel

A végtelen görgetés akkor aktiválódik, amikor a felhasználó egy függőleges görgetősávot görget az utolsó megjelenített eredményre. Ebben az esetben a kiszolgálóhívás az eredmények következő oldalára történik. Ha nincs több eredmény, a program semmit sem ad vissza, és a függőleges görgetősáv nem változik. Ha több eredmény jelenik meg, a program hozzáfűzi őket az aktuális laphoz, és a görgetősáv megváltozik, hogy több eredmény érhető el.

A lényeg itt az, hogy a megjelenített oldal nem helyettesíti, de csatolva az új eredményeket. A felhasználó bármikor visszagörgethet a keresés első eredményéhez.

A végtelen görgetés megvalósításához kezdjük a projekttel, mielőtt az oldalszám görgetési elemeit hozzáadnák. Tehát, ha szükséges, készítsen egy másik másolatot az alapkeresési lapról a GitHubról: [Az első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Lapozómezők hozzáadása a modellhez

1. Először vegyen fel egy **lapozótulajdonságot** a **SearchData** osztályba (a SearchData.cs modellfájlban).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Ez a változó egy karakterlánc, amely a "következő" értéket tartalmazza, ha a következő találatot el kell küldeni, vagy null értékű a keresés első oldalán.

2. Ugyanabban a fájlban és a névtéren belül adjon hozzá egy globális változóosztályt egy tulajdonsággal. Az MVC-ben a globális változók deklarálva vannak a saját statikus osztályukban. **Az ResultsPerPage** beállítja az egy oldalonkénti eredmények számát. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Függőleges görgetősáv hozzáadása a nézethez

1. Keresse meg az index.cshtml fájlnak azt a részét, amely az eredményeket jeleníti meg (a ** @if (Modell != null)** értékkel kezdődik).

2. Cserélje le a szakaszt az alábbi kódra. Az ** &lt;új&gt; div** szakasz körül van a terület, hogy kell görgethető, és hozzáteszi, mind a **túlcsordulás-y** attribútum és a hívás egy **onscroll** funkció az úgynevezett "scrolled()", mint így.

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
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Közvetlenül a hurok alatt, &lt;&gt; a /div címke után adja hozzá a **görgetett** funkciót.

    ```javascript
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

    A fenti parancsfájl **"if"** utasítása teszteli, hogy a felhasználó a függőleges görgetősáv aljára görgetett-e. Ha van, **a** Home vezérlő hívása a Next nevű műveletre **történik.** A vezérlőnek nincs szüksége más információra, akkor a következő adatlapot adja vissza. Ezeket az adatokat a program ezután az eredeti oldallal megegyező HTML-stílusok használatával formázza. Ha nem ad vissza eredményt, semmi sem lesz hozzáfűzve, és a dolgok úgy maradnak, ahogy vannak.

### <a name="handle-the-next-action"></a>A Következő művelet kezelése

Csak három műveletet kell elküldeni a vezérlőnek: az alkalmazás első futtatása, amely meghívja az **Index()** programot, a felhasználó által végzett első keresést, amely meghívja az **Index(model)** programot, majd az ezt követő további eredményeket a **Next(model)** segítségével.

1. Nyissa meg az otthoni vezérlőfájlt, és törölje a **RunQueryAsync** metódust az eredeti oktatóanyagból.

2. Cserélje le az **Index(modell)** műveletet a következő kódra. Most kezeli a **lapozómezőt,** ha null, vagy "következő" értékre van állítva, és kezeli az Azure Cognitive Search hívását.

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

    A számozott lapozási módszerhez hasonlóan a **Kihagyás** és a **Felső** keresési beállításokat használjuk, hogy csak a szükséges adatokat kérjük vissza.

3. Adja hozzá a **Következő** műveletet az otthoni vezérlőhöz. Figyelje meg, hogyan ad vissza egy listát, minden szálloda két elemet ad hozzá a listához: egy szálloda nevét és egy szálloda leírását. Ez a formátum úgy van beállítva, hogy megfeleljen a visszaadott adatok **görgetett** függvényáltali használatának a nézetben.

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

4. Ha szintaktikai hibát észlel a **&lt;Lista karakterláncban,&gt;** akkor adja hozzá az alábbi **utasítást** a vezérlőfájl fejéhez.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>A projekt fordítása és futtatása

Most válassza a **Start hibakeresés nélkül** lehetőséget (vagy nyomja le az F5 billentyűt).

1. Adjon meg egy kifejezést, amely sok eredményt ad (például "pool"), majd tesztelje a függőleges görgetősávot. Új eredményoldalt indít el?

    ![Végtelen görgetés a "pool" eredmények között](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Annak érdekében, hogy a görgetősáv megjelenjen az első oldalon, az eredmények első oldalának kissé meg kell haladnia annak a területnek a magasságát, amelyen megjelenik. A példánkban **a .box1** magassága 30 képpont, **a .box2** magassága 100 képpont, alsó margója pedig 24 képpont. _and_ Tehát minden bejegyzés 154 pixelt használ. Három bejegyzés 3 x 154 = 462 képpontot vesz igénybe. A függőleges görgetősáv megjelenítésének biztosításához a kijelzőterület magasságát 462 képpontnál kisebb rekedésnek kell beállítani, még akkor is, ha a 461 működik. Ez a probléma csak az első oldalon jelentkezik, ezt követően a görgetősáv biztosan megjelenik. A frissítendő sor: ** &lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;"&gt;onscroll="scrolled()"**.

2. Görgessen le egészen az eredmények aljáig. Figyelje meg, hogy az összes információ most már az egy nézet oldalon. A kiszolgálóhívások kiváltása nélkül görgethet egészen a csúcsig.

A kifinomultabb végtelen görgetőrendszerek az egérgörgőt vagy más mechanizmust használhatnak egy új eredményoldal betöltéséhez. Mi nem vesz végtelen görgetés tovább ezeket az útmutatókat, de van egy bizonyos varázsa, hogy ez elkerüli extra kattintással, és érdemes megvizsgálni más lehetőségek tovább!

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő elvihető ételek ebből a projektből:

* Számozott személyhívó van jó részére keres hol a rend -ból eredmények van némileg önkényes, jelentés ott május jól lenni valami -ból érdekel -hoz -a használók -on később oldalak.
* Végtelen görgetés jó, ha az eredmények sorrendje különösen fontos. Ha például az eredményeket a célváros központjától való távolság alapján rendezi késedelmi döngés.
* Számozott személyhívó lehetővé teszi a jobb navigációt. Például a felhasználó emlékezhet arra, hogy egy érdekes eredmény a 6.
* Végtelen görgetés van egy egyszerű fellebbezést, görgetés fel és le nem nyűgös oldalszámokat kattintson.
* A végtelen görgetés egyik legfontosabb jellemzője, hogy az eredményeket egy meglévő oldalhoz fűzik, nem pedig az oldal cseréjéhez, ami hatékony.
* Az ideiglenes tárolás csak egy hívás esetén marad meg, és a további hívások túléléséhez alaphelyzetbe kell állítani.


## <a name="next-steps"></a>További lépések

A személyhívó szolgáltatás alapvető fontosságú az internetes kereséshez. A lapozás jól fedett, a következő lépés az, hogy javítsa a felhasználói élményt tovább, hozzáadásával típus-előre keres.

> [!div class="nextstepaction"]
> [C# oktatóanyag: Automatikus kiegészítés és javaslatok hozzáadása – Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)
