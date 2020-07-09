---
title: C# oktatóanyag a dimenziók használatának támogatásához a navigáláshoz
titleSuffix: Azure Cognitive Search
description: A "lapozási eredmények" lehetőséggel bővítheti a sokoldalú navigálást. Ebből a témakörből megtudhatja, hogyan használhatók a keresések egyszerű szűkítéséhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 7d91348b727c1c43f010ec9f60ae9abd33978ce8
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85257516"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Oktatóanyag: sokoldalú Navigálás hozzáadása a .NET SDK használatával

Az aspektusok a navigáláshoz nyújtanak segítséget azáltal, hogy a felhasználó számára a keresésre mutató hivatkozásokat adnak meg. A dimenziók az adathalmazok (például a kategória, vagy egy adott szolgáltatás, a mintaadatok) attribútumai.

Ez az oktatóanyag a C# oktatóanyagban létrehozott lapozási projektre épül [: keresési eredmények tördelése – Azure Cognitive Search](tutorial-csharp-paging.md) oktatóanyag.

Az oktatóanyag a következőket ismerteti:
> [!div class="checklist"]
> * Modell tulajdonságainak beállítása _IsFacetable_
> * Face-Navigálás hozzáadása az alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

A [C# oktatóanyag: keresési eredmények tördelése – az Azure Cognitive Search-](tutorial-csharp-paging.md) projekt üzembe helyezése folyamatban van. A projekt lehet saját verziója, vagy a GitHubról telepítheti: [első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Modell tulajdonságainak beállítása IsFacetable

Ahhoz, hogy egy Model tulajdonságot a faced keresési szolgáltatásban lehessen elhelyezni, a címkének **IsFacetable**kell lennie.

1. Vizsgálja meg a **szállodai** osztályt. A **Kategóriák** és **címkék**például **IsFacetable**vannak megjelölve, de a **pezsgő** és a **Leírás** nem. 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. Az oktatóanyag részeként nem módosítunk címkéket, ezért a hotel.cs fájl változatlan marad.

    > [!Note]
    > A dimenziós keresés hibát jelez, ha a keresésben kért mező nincs megfelelően címkézve.


## <a name="add-facet-navigation-to-your-app"></a>Face-Navigálás hozzáadása az alkalmazáshoz

Ebben a példában engedélyezzük a felhasználó számára, hogy kiválassza az eredmények közül az egyik kategóriát, vagy egy rekreációs elemet. A felhasználó egy adott keresési szöveg beírásával indul el, majd leszűkítheti a keresés eredményét egy kategória kiválasztásával, és az eredményeket tovább szűkítheti a megfelelőség kiválasztásával, vagy először kiválaszthatja a megfelelőt (a megrendelés nem fontos).

Szükség van a vezérlőre, hogy átadja az aspektusok listáját a nézetnek. Meg kell őrizni a felhasználói beállításokat a keresés folyamata során, és ismét az ideiglenes tárterületet használjuk az adatmegőrzési mechanizmusként.

![A "pool" keresésének szűkítése a facet navigációs használatával](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Szűrő sztringek hozzáadása a SearchData-modellhez

1. Nyissa meg a SearchData.cs fájlt, és adja hozzá a karakterlánc-tulajdonságokat a **SearchData** osztályhoz, hogy a Dimenzióérték-szűrő sztringek tárolásához.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>A dimenzió műveleti módszer hozzáadása

A Kezdőlap vezérlőnek egy új műveletre, **aspektusra**és frissítésre van szüksége a meglévő **index** és **Page** műveletekhez, valamint a **RunQueryAsync** metódus frissítéseihez.

1. Nyissa meg a Kezdőlap vezérlő fájlt, és adja hozzá a **using** utasítást a **List &lt; String &gt; ** -összeállítás engedélyezéséhez.

    ```cs
    using System.Collections.Generic;
    ```

2. Cserélje le az **index (SearchData Model)** műveleti metódusát.

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Cserélje le az **oldal (SearchData Model)** műveleti metódusát.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
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

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Adjon hozzá egy **dimenziós (SearchData Model)** műveleti metódust, amely akkor aktiválódik, amikor a felhasználó rákattint egy aspektus hivatkozására. A modell tartalmazni fog egy kategória keresési szűrőt vagy egy rekreációs keresési szűrőt. Felveheti a lapot az **oldal** művelet után.

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>A keresési szűrő beállítása

Ha a felhasználó egy adott dimenziót választ ki, például az **üdülő és a fürdő** kategóriára kattint, akkor csak az ebben a kategóriában megadott szállodák lesznek visszaadva az eredmények között. Ha így szeretne szűkíteni egy keresést, be kell állítania egy _szűrőt_.

1. Cserélje le a **RunQueryAsync** metódust a következő kódra. Elsődlegesen egy kategória-szűrő sztringet és egy rekreációs szűrő sztringet vesz igénybe, és beállítja a **SearchParameters** **Filter** paraméterét.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

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
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    Hozzáadta a **Kategória** és a **címkék** tulajdonságokat a **kijelölni** kívánt elemek listájához. Ehhez a feltételhez nem szükséges, hogy a Face navigáció működjön, de ezt az információt használjuk a megfelelő szűrés ellenőrzéséhez.

### <a name="add-lists-of-facet-links-to-the-view"></a>A nézethez tartozó aspektusi hivatkozások listáját adja hozzá

A nézet jelentős változásokat fog igényelni. 

1. Először nyissa meg a Hotels. css fájlt (a WWWroot/CSS mappában), és adja hozzá a következő osztályokat.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;    
    }
    ```

2. A nézet esetében a kimenetet egy táblázatba rendezjük, hogy a bal oldali dimenziók listáit szépen igazítsa, a jobb oldalon pedig az eredményeket. Nyissa meg az index. cshtml fájlt. Cserélje le a HTML törzs címkék teljes tartalmát a &lt; &gt; következő kódra.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
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

                                <td class="tdPage">
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
                                    <td class="tdPage">
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

                                <td class="tdPage">
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

                                <td class="tdPage">
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
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Figyelje meg a **HTML. ActionLink** hívás használatát. Ez a hívás érvényes szűrési karakterláncokat kommunikál a vezérlővel, amikor a felhasználó egy aspektus hivatkozásra kattint. 

### <a name="run-and-test-the-app"></a>Az alkalmazás futtatása és tesztelése

A felhasználó aspektusának az az előnye, hogy egyetlen kattintással szűkítheti a keresést, amelyet a következő sorozatban megjeleníthet.

1. Futtassa az alkalmazást, és írja be a "repülőtér" kifejezést a keresett szövegként. Győződjön meg arról, hogy a dimenziók listája a bal oldalon látható. Ezek az aspektusok mind azokra a szállodákra vonatkoznak, amelyekben szerepelnek a "Airport" szövegük a szöveges adattárban.

    ![A "repülőtér" keresésének szűkítése a Face navigációs használatával](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Kattintson az **üdülő és a fürdő** kategóriára. Ellenőrizze, hogy az összes eredmény ebben a kategóriában van-e.

    ![A keresés szűkítése a "Resort and Spa" kifejezésre](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Kattintson a **kontinentális reggeli** élvezete lehetőségre. Ellenőrizze, hogy az összes eredmény továbbra is az "üdülőhely és Spa" kategóriában van-e, a kiválasztott rekreációval együtt.

    ![A keresés szűkítése a "kontinentális reggeli" kifejezésre](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Próbáljon meg más kategóriát kijelölni, majd egy élvezetet, és tekintse meg a szűkített eredményeket. Ezután próbálkozzon a másik megoldással, egy kivezetővel, majd egy kategóriával.

    >[!Note]
    > Ha egy dimenziós listában (például kategória) végez egy kijelölést, akkor a rendszer felülírja az összes korábbi kijelölést a Kategória listában.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* Minden tulajdonságot **IsFacetable**kell megjelölni, ha azokat bele kell foglalni a facet navigationbe.
* A facet Navigation egyszerűen és intuitív módon teszi lehetővé a keresés szűkítéséhez.
* A dimenziós Navigálás a legmegfelelőbb szakaszokra oszlik (a szállodák kategóriái, a szállodai szolgáltatások, az árak, a minősítési tartományok stb.), és mindegyik szakasz egy megfelelő fejléccel rendelkezik.

## <a name="next-steps"></a>További lépések

A következő oktatóanyagban megtekintjük az eredmények sorrendjét. Ezen a ponton az eredmények egyszerűen sorrendbe rendezhetők az adatbázisban található sorrendben.

> [!div class="nextstepaction"]
> [C# oktatóanyag: az eredmények rendezése – Azure Cognitive Search](tutorial-csharp-orders.md)
