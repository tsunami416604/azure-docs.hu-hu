---
title: C#értékkorlátozással használatával a navigációs és hálózati hatékonyság – Azure Search-oktatóanyag
description: Ebben az oktatóanyagban a "Keresési eredmények tördelés – az Azure Search" projekt hozzáadása értékkorlátozás keresések épül. Ismerje meg, hogy a navigációs és autocompletion értékkorlátozással használhatók.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: a81042869564533050fef42a983f2f8fb9bc7b23
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304654"
---
# <a name="c-tutorial-use-facets-for-navigation-and-network-efficiency---azure-search"></a>C#oktatóanyag: A navigációs és hálózati hatékonyság – Azure Search értékkorlátozással használata

Értékkorlátozással két különböző használja az Azure Search rendelkezik. Értékkorlátozással is használható a navigációt azáltal, hogy a felhasználói jelölőnégyzeteket, hogy mire kell összpontosítania a keresés használata vannak beállítva. Emellett ezek segítségével javítják a autocompletion használt hálózati hatékonyságát. Omezující keresések hatékonyak, mert csak egyszer minden egyes betöltés ahelyett, hogy minden billentyűleütés egyszer vannak felsorolva. 

Értékkorlátozással attribútumok (például a mintaadatokat a szállodában kategóriáját) az adatok, és legyen naprakész, a Keresés a gyűjteményszintű.

Ebben az oktatóanyagban két projektet, egyet a értékkorlátozó navigációs és a másik dimenzió autocompletion épít fel. Mindkét projekt létrehozása a lapozófájl projekt létrehozott alakzatot a [ C# oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md) oktatóanyag.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adatmodell-tulajdonságot, állítsa _IsFacetable_
> * Értékkorlátozó navigációs hozzáadása az alkalmazáshoz
> * Omezující autocompletion hozzáadása az alkalmazáshoz
> * Döntse el, hogy mikor érdemes használni a dimenzió autocompletion

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

Rendelkezik a [ C# oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md) projektet, majd futtassa. Ez a saját verzióját, vagy telepítheti a Githubról: [Első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-fields-as-isfacetable"></a>IsFacetable modell mezők beállítása

Ahhoz, hogy a modell tulajdonságai értékkorlátozás keresés (navigációs vagy autocompletion) található, akkor fel kell címkézni **IsFacetable**.

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

2. Mi lesz nem lehet módosítása bármely címke ebben az oktatóanyagban. Egy dimenzió keresési kivételt fogja kijelezni hiba, ha egy mezőt a keresési kért nem megfelelően van megjelölve.


## <a name="add-facet-navigation-to-your-app"></a>Értékkorlátozó navigációs hozzáadása az alkalmazáshoz

Ebben a példában fogjuk engedélyezése a felhasználó számára egy lista jelenik meg az eredmények a bal hotel, egy vagy több kategóriához. Meg kell ismernie a kategóriák listájában, az alkalmazás első futtatásakor, és átadni az ebben a listában jelenik meg, amikor az első képernyő jelenik meg a nézet a vezérlő. Minden lapon eredménykészletből kell győződjön meg arról, hogy értékkorlátozással listáját és következő lapjain átadandó mentén az aktuális felhasználói beállítások rendelkezik tárolja. Ideiglenes tároló újra, megőrzi az adat üzemeltetés használjuk.

!["Gyűjtő" keresés szűkítéséhez értékkorlátozó navigációs használatával](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="modify-the-searchdata-model"></a>A SearchData modell módosítása

1. Nyissa meg a SearchData.cs fájlt, és hozzá ezen további **használatával** utasítást. Engedélyezni kell a **lista&lt;karakterlánc&gt;**  hozhatnak létre.

    ```cs
    using System.Collections.Generic;
    ```

2. Ugyanebben a fájlban adja hozzá a következő sorokat a **SearchData** osztály. Ne törölje a meglévő osztály tulajdonságait, de adja meg az alábbi konstruktort módszerek és a Tulajdonságok tömbök.

    ```cs
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each facet.
        public string[] facetText { get; set; }

        // Array to hold the check box setting.
        public bool[] facetOn { get; set; }
    ```


### <a name="search-for-facets-on-the-first-index-call"></a>Első Index hívásakor értékkorlátozással keresése

A kezdőlap vezérlő egy jelentős igényeinek megfelelően. Az első hívás **Index()** már nem ad vissza semmilyen más feldolgozási tartalmazó nézetet. Szeretnénk adja meg a nézet az aspektusokat teljes listáját, és az első hívás erre a célra a megfelelőt.

1. Nyissa meg az otthoni vezérlő fájlt, és adjon hozzá két **használatával** utasításokat.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. Cserélje le a jelenlegi néhány sornyi **Index()** olyan módszer, amely Szálloda kategóriák értékkorlátozás a keresést végez metódust. A Keresés a aszinkron módon kell elvégezni, mivel azt deklarálni kell a **Index** módját **aszinkron**.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(categories);

            // Save the facet text for the next view.
            SaveFacets(model);

            // Render the view including the facets.
            return View(model);
        }
    ```

    Megjegyzés: Itt néhány mutat. Hogy konvertálni az eredményeket a keresési hívás karakterláncok listáját, majd értékkorlátozás karakterláncokra adnak hozzá egy **SearchData** nézetbe kommunikációs modellt. Is elmentjük ezek a karakterláncok egy ideiglenes tárolóra a nézet végül megjelenítése előtt. A mentés történik, hogy ez a lista érhető el a következő hívást egy vezérlő művelet.

3. Adjuk hozzá a privát kétféleképpen, mentése és visszaállítása értékkorlátozással a modellhez, és a egy ideiglenes tárolóra.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

### <a name="save-and-restore-facet-text-on-all-calls"></a>Mentése és visszaállítása értékkorlátozás szöveget az összes hívás

1. Két más műveletek az otthoni vezérlő **Index (SearchData modell)** és **(SearchData modell) lap**, mindkettő aspektusait a keresési hívás előtt helyre, és mentse őket a keresési hívása után újra kell. Módosítsa a **Index (SearchData modell)** ezen két hívásokat.

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

                // Recover the facet text.
                RecoverFacets(model);

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;

                // Facets
                SaveFacets(model, true);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

2. Most tegye meg ugyanezt a a **(SearchData modell) lap** metódust. Csak azt kell jelenik meg a megfelelő az alábbi kódot. Adja hozzá a **RecoverFacets** és **SaveFacets** körül meghívja a **RunQueryAsync** hívja.

    ```cs
                // Recover facet text and check marks.
                RecoverFacets(model, true);

                await RunQueryAsync(model, page, leftMostPage);

                // Save facets and check marks.
                SaveFacets(model, true);
    ```

### <a name="set-up-a-search-filter"></a>Keresési szűrő beállítása

Amikor egy felhasználó bizonyos értékkorlátozással választja, például tegyük fel, kattintson a a **költségvetés** és **végső megoldásként és Spa** kategóriák, akkor csak az egyik két kategóriába vissza kell adni a megadott hotels a eredmények. Ezzel a módszerrel keresés optimalizálása érdekében állítsa be kell egy _szűrő_.

1. Az a **RunQueryAsync** metódust, adja hozzá a kódot az adott modell értékkorlátozás-beállításokat, hozzon létre egy szűrési karakterláncot hurkot. És adja hozzá a szűrőt, hogy a **SearchParameters**, az alábbi kódban látható módon.

    ```cs
            // Create a filter for selected facets.
            string selectedFacets = "";

            for (int f = 0; f < model.facetText.Length; f++)
            {
                if (model.facetOn[f])
                {
                    if (selectedFacets.Length > 0)
                    {
                        // If there is more than one selected facet, logically OR them together.
                        selectedFacets += " or ";
                    }
                    selectedFacets += "(Category eq \'" + model.facetText[f] + "\')";
                }
            }

            var parameters = new SearchParameters
            {
                // Facets: add the filter.
                Filter = selectedFacets,

                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description", "Category" },
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };
    ```

    A következőkhöz adtunk hozzá a **kategória** tulajdonság listájához **válassza** , a visszaadandó elemek. Ez a tulajdonság hozzáadása nem kötelező, de ezzel a módszerrel ellenőrizni tudja, hogy megfelelően szűrés.

### <a name="define-a-few-additional-html-styles"></a>Néhány további HTML stílusok meghatározása

A nézet fog néhány jelentős módosítások szükségesek. 

1. Először nyissa meg a hotels.css fájlban (a wwwroot/css), és adja hozzá a következő osztályok.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 200px;
        background-color: lightgoldenrodyellow;
        display: normal;
        color: #666;
        margin: 10px;
    }
    ```

### <a name="add-a-list-of-facet-checkboxes-to-the-view"></a>Adja hozzá a nézethez értékkorlátozás jelölőnégyzetek listája

A nézet azt szervezheti a kimeneti tábla, az aspektusokat, a bal oldalon, és az eredményeket a jobb oldali eligazíthatja igazítása. Az index.cshtml fájl megnyitásához.

1. Cserélje le a teljes tartalma HTML &lt;törzs&gt; címkéket, a következő kóddal.

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
                        <h5>Filter by Category:</h5>
                        <ul class="facetlist">
                            @for (var i = 0; i < Model.facetText.Length; i++)
                            {
                                <li> @Html.CheckBoxFor(m => m.facetOn[i], new { @id = "check" + i.ToString() }) @Model.facetText[i] </li>
                            }
                        </ul>
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
                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea("desc", Model.resultList.Results[i].Document.Description + "\nCategory:  " +  Model.resultList.Results[i].Document.Category, new { @class = "box2" })
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

    Figyelje meg a a **CheckBoxFor** hívás feltölti a **facetOn** tömbbe, a felhasználói beállításokat. Hozzáadtuk a Szálloda kategóriáját is, a Szálloda leírás végére. Ez a szöveg egyszerűen, hogy erősítse meg, hogy a keresési megfelelően működik-e. Nincs más más megváltozott a korábbi oktatóanyagokat, azzal a különbséggel, hogy azt van rendezve a kimenetet egy táblába.

### <a name="run-and-test-the-app"></a>Futtassa, és az alkalmazás tesztelése

1. Futtassa az alkalmazást, és ellenőrizze, hogy értékkorlátozással listája a bal oldali eligazíthatja megjelenik-e.

2. Válasszon ki egy, kettő, három vagy több jelölőnégyzetet, és ellenőrizze az eredményeket.

    ![A "Wi-Fi" keresés szűkítéséhez értékkorlátozó navigációs használatával](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

3. Az értékkorlátozó navigációs egy enyhe complication van. Mi történjen, ha a felhasználó megváltoztatja a dimenzió kijelölés (kiválasztásával vagy vonja vissza a jelölőnégyzetek bejelölésével), de majd rákattint az egyik a lapozási beállításai, és nem a keresősávba? Érvényben a Kijelölés módosítása kell kezdeményezni egy új keresési, a aktuális oldal már nem megfelelő. Másik lehetőségként a felhasználó módosítja sikerült figyelmen kívül lesz hagyva, és az eredmények a következő lapon megadott, az eredeti értékkorlátozás kiválasztott beállítások alapján. Azt választotta, az utóbbi megoldást ebben a példában, de talán fontolja meg, hogyan valósíthatja meg a korábbi megoldás. Új keresés talán aktiválása, ha a kiválasztott értékkorlátozással legújabb választott nem pontosan egyezik a kijelölést az ideiglenes tároló?

Befejezte a értékkorlátozó navigációs példában. De talán is érdemes lehet előfordulhat, hogy hogyan bővíthető Ez az alkalmazás. Az értékkorlátozás lista lehetett kibontani más értékkorlátozás tudni mezőket (tegyük fel, hogy **címkék**), így a felhasználó tudta válassza ki a lehetőségek, például egy készlet, Wi-Fi, sáv, ingyenes várakozást és így tovább. 

Az értékkorlátozó navigációs a felhasználó előnye, hogy nem kell ugyanazon szöveg beírásával tartani, azok értékkorlátozás lehetőségek a gyűjteményszintű az alkalmazáshoz az aktuális munkamenet esetén is megmarad. És kiválaszthatja a kategóriák, és lehetséges, hogy más attribútumok, egyetlen kattintással, majd keressen a többi meghatározott szöveget.

Most tekintsük vizsgálja meg egy másik értékkorlátozással használatát.

## <a name="add-facet-autocompletion-to-your-app"></a>Omezující autocompletion hozzáadása az alkalmazáshoz

Omezující autocompletion azáltal, hogy egy kezdeti keresés, az alkalmazás első futtatásakor működik. A keresés gyűjti értékkorlátozással javaslatként használni, amikor a felhasználó listáját.

![Beírásával "újra" tárja fel a három aspektusokat](./media/tutorial-csharp-create-first-app/azure-search-facet-type-re.png)

Használjuk a számozott lapozási alkalmazást, előfordulhat, hogy elvégezte a második oktatóanyagban a minta alapján.

Omezující autocompletion implementálásához azt nem kell módosítani a modellek (adatosztályokat). Hozzá kell adnunk a néhány parancsprogramot a nézetet, és a egy olyan műveletet, a vezérlő.

### <a name="add-an-autocomplete-script-to-the-view"></a>A nézet Automatikus kiegészítés parancsfájl hozzáadása

Egy dimenzió keresési kezdeményez, kell küldeni egy lekérdezést. Az index.cshtml fájl hozzá a következő JavaScript biztosítja a lekérdezés logikája és bemutató van szükségünk.

1. Keresse meg a **@Html.TextBoxFor(m = > m.searchText,...)** utasítást, és adja hozzá az egyedi azonosító, az alábbihoz hasonló.

    ```cs
    <div class="searchBoxForm">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresearchfacets" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

2. Ezután adja hozzá a következő JavaScript (a záró után **&lt;/div&gt;** rendben működik fent látható).

    ```JavaScript
     <script>
            $(function () {
                $.getJSON("/Home/Facets", function (data) {

                    $("#azuresearchfacets").autocomplete({
                        source: data,
                        minLength: 2,
                        position: {
                            my: "left top",
                            at: "left-23 bottom+10"
                        }
                    });
                });
            });
        </script>
    ```

    Figyelje meg, hogy a parancsfájl meghívja a **metszettel** az otthoni vezérlő más paraméterek nélkül, ha eléri a két beírt karakterek minimális hosszát a művelet.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Adja hozzá a jquery parancsfájlok hivatkozásokat a nézet

Az automatikus kiegészítés funkció a szkriptben nevű nem áll megírása magunkat érhető el a jquery-erőforrástárban kell, hogy valami. 

1. A jquery könyvtár eléréséhez, cserélje le a &lt;fő&gt; szakasz a nézet fájl az alábbi kódra.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Facets demo</title>
        <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Távolítsa el, vagy tegye megjegyzésbe, egy sor hivatkozik a _Layout.cshtml fájl jquery is kell (a a **nézetek/megosztott** mappát). Keresse meg a következő sorokat, és tegye megjegyzésbe az első szkript sor látható módon. Eltávolítja ezt a sort, hogy kerülje a jquery nem egyértelmű hivatkozik.

    ```html
     <environment include="Development">
            <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
            <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
            <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

Most már az előre meghatározott automatikus kiegészítés jquery függvények használatával.

### <a name="add-a-facet-action-to-the-controller"></a>Egy dimenzió művelet hozzáadása a vezérlőhöz

1. Nyissa meg az otthoni vezérlő, és adja hozzá a következő két **használatával** utasításokat a fájl vezetője.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. A JavaScript, a nézet triggerek a **Értékkorlátozással** művelet található a vezérlő egészítsük a művelet az otthoni vezérlőhöz (alább tegyük fel, a **lap** művelet).

    ```cs
        public async Task<ActionResult> Facets()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search all Tags, but limit the total number to 100, and add up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Tags,count:100", "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to two lists that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Combine and return the lists.
            facets.AddRange(categories);
            return new JsonResult(facets);
        }
    ```

    Figyelje meg, hogy legfeljebb 100 aspektusokat, az azt kérő a **címkék** mezőket, és akár 20, a **kategória** mezőket. A **száma** bejegyzés megadása nem kötelező, ha nincs van beállítva az alapértelmezett érték 10.

    Igazolnia kell, majd egyesül, mert azt kéri két mezőt keresni két Számlista (**címkék** és **kategória**). Ha azt kérte, a keresendő három mezőt, hogy kellene három listák egyesítése egyet, és így tovább.

    > [!NOTE]
    > Állítsa be legalább egy, az alábbi paramétereket az egyes mezőkhöz tartozó értékkorlátozás keresés lehetséges: **száma**, **rendezési**, **időköz**, és **értékek**. További információkért lásd: [jellemzőalapú navigáció megvalósítása az Azure Search](https://docs.microsoft.com/azure/search/search-faceted-navigation).

### <a name="compile-and-run-your-project"></a>Fordítsa le és a projekt futtatása

Most tesztelheti a program.

1. Próbálja beírni az "fr" szót a keresőmezőbe, amely több találat megjelenítése a kell.

    ![Írja be a "fr" tárja fel a három aspektusokat](./media/tutorial-csharp-create-first-app/azure-search-facet-type-fr.png)

2. Adjon hozzá egy "ó" "felhasználóról", és figyelje meg, hogy egy csökken a beállítások körét.

3. Írja be a két betű egyéb kombinációit, és nézze meg mi jelenik meg. Figyelje meg, hogy amikor beírja a kiszolgáló *nem* hívott. Aspektusait helyileg lettek gyorsítótárazva, amikor az alkalmazás elindult, és most már csak kezdeményezték a kiszolgálóhoz keresés esetén.

## <a name="decide-when-to-use-a-facet-autocompletion-search"></a>Döntse el, hogy egy értékkorlátozó autocompletion keresés használata

A tiszta értékkorlátozás keresések és a vonatkozó javaslatokat és autocompletion, például más keresések közötti különbség, hogy a dimenzió keresési _tervezett_ elvégzendő csak egyszer egy oldal betöltésekor. A többi autocompletion keresések _tervezett_ minden karakter beírása után kell meghívni. Értékkorlátozással használatával ezzel a módszerrel potenciálisan menti a kiszolgálón sok hívás. 

Azonban ha értékkorlátozás autocompletion használ?

Podmínka autocompletion akkor érdemes használni:
* Elsődleges oka az, hogy más keresések, hogy minden billentyűleütés hívókiszolgáló teljesítményét a probléma.
* A visszaadott értékkorlátozással egységes ésszerű beállításlista néhány karakter beírásakor.
* A visszaadott értékkorlátozással egy gyorsan elérheti a legtöbb vagy az ideális összes, a rendelkezésre álló adatokat adja meg.
* A maximális számát engedélyezése a legtöbb értékkorlátozással fogja tartalmazni. A kódban, legfeljebb 100 értékkorlátozással számára beállított **címkék** és a 20 értékkorlátozással **kategória**. A méretkorlát set kell jól működnek a az adatkészlet méretét. Ha túl sok lehetséges értékkorlátozással vannak folyamatban kivágási, majd talán a keresést, nem hasznosként módon kell lennie.

> [!NOTE]
> Bár a dimenzió keresések kell meghívni, ha a betöltés, /, természetesen nem hívható meg sokkal gyakrabban készültek, attól függ, a JavaScript. Egyformán igaz az, hogy autocompletion/javaslat keresések legalább egyszer billentyűlenyomásra is végezni. Újra ez határozza meg a JavaScript, nem az Azure Search. Azonban dimenzió keresési célja, hogy csak egyszer hívható egy-egy lapon, az aspektusokat az Azure Search figyelembe ezt a keresett dokumentumok és változóból tevődnek. Tanácsos értékkorlátozás autocompletion keresések tekinti a felhasználói-támogatás kissé kevésbé rugalmas, de még hálózati hatékonyabban űrlap.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő takeaways a projekt:

* Jelölje meg az egyes mezők, fontos **IsFacetable**, ha azok értékkorlátozó navigációs vagy autocompletion szerepelnek.
* Értékkorlátozó navigációs rendelkező szűkítheti a keresést, egyszerű és intuitív, lehetőséget biztosít.
* Értékkorlátozó navigációs legjobb szakaszokból áll (szálloda kategóriák), a funkciók egy szállodai Vendég, ár tartományokat, stb., egy megfelelő fejlécet minden szakaszt.
* Értékkorlátozás autocompletion első nélkül az ismétlődő kiszolgálói hívások egyéb autocompletion kereséseinek hasznos felhasználói élmény hatékony módja.
* Omezující autocompletion van egy _alternatív_ automatikus kiegészítés és javaslatokat, további nem.

## <a name="next-steps"></a>További lépések

Befejezte a sorozatát C# oktatóanyagokat – meg kell révén az Azure Search API-k értékes ismerete.

A további hivatkozás és oktatóanyagok, fontolja meg a böngészés [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), vagy a többi oktatóanyag a [Azure Search-dokumentáció](https://docs.microsoft.com/azure/search/).
