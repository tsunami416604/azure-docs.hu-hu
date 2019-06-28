---
title: C#értékkorlátozással használatával a navigációt – Azure Search-oktatóanyag
description: Ebben az oktatóanyagban a "Keresési eredmények tördelés – az Azure Search" projekt hozzáadása értékkorlátozó navigációs épül. Ismerje meg, hogy használható-e a értékkorlátozással egyszerűen szűkítheti a keresést.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: 62326ad3bc5f2d740ce744819df559bce8658eb7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443779"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-search"></a>C#oktatóanyag: A navigációt – Azure Search értékkorlátozással használata

A navigációt azáltal, hogy a felhasználói hivatkozásokat tartalmaz, hogy mire kell összpontosítania a keresés használatára az aspektusokat szolgálnak. Értékkorlátozással attribútumok az adatok (például a kategóriát, vagy egy adott szolgáltatáshoz, egy szállodáját a mintaadatokat,).

Ebben az oktatóanyagban az alakzatot a lapozófájl projekt létrehozott összeállítja a [ C# oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md) oktatóanyag.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adatmodell-tulajdonságot, állítsa _IsFacetable_
> * Értékkorlátozó navigációs hozzáadása az alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

Rendelkezik a [ C# oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md) projektet, majd futtassa. Ez a projekt a saját verzióját, vagy telepítheti a Githubról: [Első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Mint IsFacetable modell tulajdonságainak megadása

Ahhoz, hogy egy értékkorlátozó keresési található modell tulajdonság, az azt fel kell címkézni **IsFacetable**.

1. Vizsgálja meg a **Szálloda** osztály. **Kategória** és **címkék**, például címkével rendelkeznek, **IsFacetable**, de **Mezőmeghatározása** és **leírás** nem. 

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

2. Mi lesz nem kell módosítása címkéket ebben az oktatóanyagban, zárja be a hotel.cs fájlban változatlan részeként.

    > [!Note]
    > Egy dimenzió keresési kivételt fogja kijelezni hiba, ha egy mezőt a keresési kért nem megfelelően van megjelölve.


## <a name="add-facet-navigation-to-your-app"></a>Értékkorlátozó navigációs hozzáadása az alkalmazáshoz

Ebben a példában fogjuk engedélyezése a felhasználó számára Szálloda, vagy egy rekreációs egy kategóriát a bal oldalán, az eredmények-hivatkozások listája. A felhasználó indítása néhány megadásával keresett szöveg, majd a kategória kiválasztásával Szűkítse le a keresési eredmények, és egy rekreációs kiválasztásával Szűkítse le az eredmények további, vagy és kiválaszthatja a rekreációs első (sorrendje nem számít).

A vezérlő értékkorlátozással listák átadása a nézet van szükségünk. A keresés előrehaladtával a felhasználói beállítások kezelése kell, és ismét használjuk az ideiglenes tároló üzemeltetés megőrzi az adat.

!["Gyűjtő" keresés szűkítéséhez értékkorlátozó navigációs használatával](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Szűrő karakterláncok hozzáadása a SearchData modellhez

1. Nyissa meg a SearchData.cs fájlt, és a karakterlánc-tulajdonságok hozzáadása a **SearchData** osztály, amely tárolja a dimenzió szűrő karakterláncot.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Az értékkorlátozás tartozó műveleti módszer hozzáadása

A kezdőlap vezérlő szüksége van egy új művelet, **értékkorlátozás**, és a meglévő frissítések **Index** és **oldal** műveletek, valamint a frissítések a **RunQueryAsync**  metódust.

1. Nyissa meg az otthoni vezérlő fájlt, és adja hozzá a **használatával** utasítással, engedélyezze a **lista&lt;karakterlánc&gt;**  hozhatnak létre.

    ```cs
    using System.Collections.Generic;
    ```

2. Cserélje le a **Index (SearchData modell)** tartozó műveleti módszer.

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

3. Cserélje le a **(SearchData modell) lap** tartozó műveleti módszer.

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

4. Adjon hozzá egy **értékkorlátozás (SearchData modell)** műveletmetódus kell aktiválni, amikor a felhasználó értékkorlátozás hivatkozásra kattint. A modell egy kategória keresési szűrő vagy egy rekreációs keresési szűrő fogja tartalmazni. Például adja hozzá azt követően a **oldal** művelet.

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

Ha a felhasználó kiválaszt egy bizonyos dimenzió, például kattintanak a a **végső megoldásként és Spa** kategóriát, majd ezt a kategóriát a rendszer visszalépteti az eredmények között megadott csak hotels. Ezzel a módszerrel a keresés szűkítéséhez azt kell állítania egy _szűrő_.

1. Cserélje le a **RunQueryAsync** módszer a következő kóddal. Elsősorban, kategória szűrő-karakterlánc, és a egy rekreációs szűrési karakterláncot vesz igénybe, és beállítja a **szűrő** paraméterében a **SearchParameters**.

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

    Hozzáadtuk a **kategória** és **címkék** tulajdonságok listájához **kiválasztása** , a visszaadandó elemek. A Hozzáadás nem értékkorlátozó navigációs működéséhez az szükséges, de ezek az információk használatával győződjön meg arról, hogy megfelelően szűrés.

### <a name="add-lists-of-facet-links-to-the-view"></a>Adja hozzá a nézethez értékkorlátozás hivatkozások listája

A nézet fog néhány jelentős módosítások szükségesek. 

1. Először nyissa meg a hotels.css fájlban (a wwwroot/css), és adja hozzá a következő osztályok.

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

2. A nézet azt a kimeneti rendezése egy táblába, eligazíthatja igazíthatja a dimenzió sorolja fel, a bal oldalon, a jobb oldalon az eredményeket. Az index.cshtml fájl megnyitásához. Cserélje le a teljes tartalma HTML &lt;törzs&gt; címkéket, a következő kóddal.

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

    Figyelje meg a a **Html.ActionLink** hívja. Ez a hívás érvényes szűrési karakterláncot a tartományvezérlőre, amely kommunikál, amikor a felhasználó egy értékkorlátozó hivatkozásra kattint. 

### <a name="run-and-test-the-app"></a>Futtassa, és az alkalmazás tesztelése

A felhasználó értékkorlátozó navigációs előnye, hogy egyetlen kattintással, amely a következő sorrendben bemutatjuk keresések szűkíthető.

1. Az alkalmazás, a típus "repülőtér" futtató a keresett szöveg. Ellenőrizze, hogy a értékkorlátozással listája eligazíthatja a bal oldali megjelenik-e. Ezek értékkorlátozással is, amely a alkalmazni a "Hotels", "repülőtéren" szöveg adatai, számot, milyen gyakran előforduló rendelkező.

    !["Repülőtér" keresés szűkítéséhez értékkorlátozó navigációs használatával](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Kattintson a **végső megoldásként és Spa** kategória. Ellenőrizze az összes eredmény ebbe a kategóriába.

    ![Szűkítheti a keresést a "Végső megoldásként és Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Kattintson a **kontinentális reggeli** rekreációs. Ellenőrizze, hogy minden eredmény "Végső megoldásként és Spa" kategória, a kiválasztott rekreációs vannak.

    ![Szűkítheti a keresést a "kontinentális reggeli"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Tetszőleges egyéb kategóriát, majd egy rekreációs lehetőséget, és tekintse meg a szűkítő eredményt. Ismételje meg a fordítva, egy rekreációs, majd egy kategóriát.

    >[!Note]
    > Egy dimenzió listában (például kategória) egy kijelölés elküldésekor az felül fogja írni a minden előző kiválasztásának belül a kategóriák listája.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő takeaways a projekt:

* Rendkívül fontos, minden egyes tulajdonsága meg **IsFacetable**, ha azok értékkorlátozó navigációs szerepelnek.
* Értékkorlátozó navigációs rendelkező szűkítheti a keresést, egyszerű és intuitív, lehetőséget biztosít.
* Értékkorlátozó navigációs legjobb szakaszra oszlik (kategóriák hotel), az eszközök egy szállodai Vendég, ár tartományokat, minősítés tartományokat, stb., egy megfelelő fejlécet minden szakaszt.

## <a name="next-steps"></a>További lépések

A következő oktatóanyagban áttekintjük az eredmények rendezése. Eddig a pontig az eredmények egyszerűen az, hogy azok az adatbázisban található sorrendben vannak rendezve.

> [!div class="nextstepaction"]
> [C#oktatóanyag: Az eredmények – Azure Search sorrend](tutorial-csharp-orders.md)
