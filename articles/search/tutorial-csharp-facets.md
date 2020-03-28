---
title: C# bemutató a laptatok használatával a navigáció elősegítésére
titleSuffix: Azure Cognitive Search
description: Ez az oktatóanyag a "Keresési eredmények tördelés – Azure Cognitive Search" projektre épül, amellyel hozzáadhat a keresési adatokhoz. Ismerje meg, hogyan használható a facets segítségével könnyen szűkíti a keresést.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: d88a9d7efdabd493fd31b961748bb6ad3bd8d738
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121566"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>C# oktatóanyag: A navigáció támogatásához használjon aspektusait - Azure Cognitive Search

A facet-ek a navigáció elősegítésére szolgálnak, azáltal, hogy a felhasználó számára egy sor hivatkozást biztosítanak a keresés összpontosítására. A facets az adatok attribútumai (például egy szálloda kategóriája vagy egy adott funkciója a mintaadatokban).

Ez az oktatóanyag a C# oktatóanyagban létrehozott lapozási projektre [épül: Keresési eredmények tördelése – Azure Cognitive Search](tutorial-csharp-paging.md) oktatóanyag.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Modelltulajdonságok beállítása _IsFacetable_ modellként
> * A felhasználók facet-navigációjának hozzáadása az alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

A [C# oktatóanyag: Keresési eredmények tördelése - Azure Cognitive Search](tutorial-csharp-paging.md) projekt, és fut. Ez a projekt lehet a saját verziója, vagy telepítheti a GitHubról: [Az első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Modelltulajdonságok beállítása IsFacetable modellként

Ahhoz, hogy egy modelltulajdonság egy facet keresésben legyen, meg kell **címkézni isFacetable**.

1. Vizsgálja meg a **hotel** osztály. **A kategória** és a **címkék**például **IsFacetable**címkével vannak ellátva, de **a HotelName** és **a Description** nem. 

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

2. Nem fogunk módosítani semmilyen címkék részeként ez a bemutató, így zárja be a hotel.cs fájl változatlan.

    > [!Note]
    > A facet keresés hibát okoz, ha a keresésben kért mező nincs megfelelően címkézve.


## <a name="add-facet-navigation-to-your-app"></a>A felhasználók facet-navigációjának hozzáadása az alkalmazáshoz

Ebben a példában lehetővé tesszük a felhasználó számára, hogy kiválasszon egy szállodai kategóriát vagy egy kényelmi kategóriát az eredmények bal oldalán látható linkek listáiból. A felhasználó egy keresési szöveg beírásával kezdi, majd szűkítheti a keresés eredményeit egy kategória kiválasztásával, és tovább szűkítheti az eredményeket a kényelem kiválasztásával, vagy először kiválaszthatja a kényelmét (a sorrend nem fontos).

Szükségünk van a vezérlő, hogy adja át a listákat a facets a nézetben. Meg kell tartani a felhasználó választás, mint a keresés előrehaladtával, és újra, használjuk ideiglenes tárolás, mint a mechanizmus az adatok megőrzésére.

![A "pool" keresésének szűkítése a facet navigációval](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Szűrőkarakterláncok hozzáadása a SearchData modellhez

1. Nyissa meg a SearchData.cs fájlt, és adjon hozzá karakterlánctulajdonságokat a **SearchData** osztályhoz a laptszűrő karakterláncok megtartásához.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>A Facet művelet módszerének hozzáadása

Az otthoni vezérlőnek szüksége van egy új műveletre, **a Facet**-re, és frissíti a meglévő **Index** és **Lap** műveleteket, valamint a **RunQueryAsync** metódus frissítéseit.

1. Nyissa meg az otthoni vezérlőfájlt, és adja hozzá a **using** utasítást a **&lt;Lista&gt; karakterlánc-szerkezet** engedélyezéséhez.

    ```cs
    using System.Collections.Generic;
    ```

2. Cserélje le az **Index(SearchData modell)** műveletmetódust.

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

3. Cserélje le a **Page(SearchData modell)** műveletmódszert.

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

4. Adjon hozzá egy **Facet (SearchData modell)** műveletmódszert, amelyakkor aktiválódik, amikor a felhasználó egy facet linkre kattint. A modell kategóriakeresési szűrőt vagy egy kényelmi keresési szűrőt fog tartalmazni. Talán add hozzá az **Oldal** művelet után.

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

Ha például egy felhasználó kiválaszt egy bizonyos adértéket, az **Üdülő és gyógyfürdő** kategóriára kattint, akkor csak az ebben a kategóriában megadott szállodákat kell visszaadni az eredményekközött. A keresés ily módon való szűkítéséhez be kell állítanunk egy _szűrőt._

1. Cserélje le a **RunQueryAsync** metódust a következő kódra. Elsősorban egy kategóriaszűrő-karakterláncot és egy kényelmi szűrőkarakterláncot vesz igénybe, és beállítja a SearchParameters **szűrő** **paraméterét.**

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

    Hozzáadtuk a **Kategória** és **címkék** tulajdonságokat a visszaadandó **elemek kijelölésének** listájához. Ez a kiegészítés nem követelmény a kiskapcsolati navigáció működéséhez, de ezt az információt arra használjuk, hogy ellenőrizzük, hogy megfelelően szűrünk-e.

### <a name="add-lists-of-facet-links-to-the-view"></a>A nézethez hivatkozási listák hozzáadása

A nézet jelentős változásokat igényel. 

1. Kezdje a hotels.css fájl megnyitásával (a wwwroot/css mappában), és adja hozzá a következő osztályokat.

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

2. A nézet, mi szervezi a kimenetet egy táblázatba, hogy szépen összehangolják a facet listák a bal oldalon, és az eredmények a jobb oldalon. Nyissa meg az index.cshtml fájlt. Cserélje le a HTML &lt;&gt; törzscímkék teljes tartalmát a következő kódra.

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

    Figyelje meg a **Html.ActionLink** hívás használatát. Ez a hívás érvényes szűrőkarakterláncokat kommunikál a vezérlővel, amikor a felhasználó egy facet hivatkozásra kattint. 

### <a name="run-and-test-the-app"></a>Az alkalmazás futtatása és tesztelése

Az előnye, hogy a facet navigáció a felhasználó számára, hogy szűkítheti keresések egyetlen kattintással, amit meg tudunk mutatni a következő sorrendben.

1. Futtassa az alkalmazást, írja be a "repülőtér" kifejezést keresési szövegként. Ellenőrizze, hogy a lapttartalmazók listája szépen megjelenik-e a bal oldalon. Ezek az aspektusak mind olyan szállodákra vonatkoznak, amelyek szöveges adataikban "repülőtér" szerepel, és megszámolják, hogy milyen gyakran fordulnak elő.

    ![A "repülőtér" keresésének szűkítése a "repülőtér" leszűkítéséhez](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Kattintson az **Üdülő és gyógyfürdő** kategóriára. Ellenőrizze, hogy minden eredmény ebbe a kategóriába tartozik-e.

    ![A keresés szűkítése az "Üdülő és gyógyfürdő" kifejezésre](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Kattintson a **kontinentális reggeli** kényelem. Ellenőrizze, hogy az összes eredmény továbbra is a "Resort and Spa" kategóriában van-e, a kiválasztott kényelemmel.

    ![A keresés leszűkítése a "kontinentális reggelire"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Próbáljon kijelölni egy másik kategóriát, majd egy kényelmi elemet, és tekintse meg a szűkülő eredményeket. Akkor próbáld meg fordítva, egy kényelem, majd egy kategória.

    >[!Note]
    > Ha egy pontlista (például a kategória) egy kijelölést hoz végre, felülírja a kategórialistán belüli korábbi kijelöléseket.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő elvihető ételek ebből a projektből:

* Elengedhetetlen, hogy minden tulajdonságot **IsFacetable-ként**jelöljön meg, ha azokat a facet navigációba szeretnék foglalni.
* A facet navigáció egyszerű és intuitív módot biztosít a felhasználó számára a keresés szűkítéséhez.
* A facet navigáció leginkább szakaszokra van osztva (hotelkategóriák, szállodai szolgáltatások, ártartományok, minősítési tartományok stb.), minden egyes szakasz megfelelő fejlécsel.

## <a name="next-steps"></a>További lépések

A következő oktatóanyagban megvizsgáljuk a rendelési eredményeket. Az eredmények et addig a pontig egyszerűen abban a sorrendben rendezi a sorrend, ahogy azok az adatbázisban találhatók.

> [!div class="nextstepaction"]
> [C# oktatóanyag: Az eredmények sorrendje - Azure Cognitive Search](tutorial-csharp-orders.md)
