---
title: C#-oktatóanyag az automatikus kiegészítésről és a javaslatokról
titleSuffix: Azure Cognitive Search
description: Automatikus kiegészítés és javaslatok hozzáadása a keresési kifejezéseknek a felhasználóktól a legördülő listából való összegyűjtéséhez. Ez az oktatóanyag egy meglévő Hotels-projektre épül.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 07/15/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 47c846f0d8d1cccd91301c7424b2618fffddd565
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998441"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Oktatóanyag: automatikus kiegészítés és javaslatok hozzáadása a .NET SDK használatával

Megtudhatja, hogyan implementálhat automatikus kiegészítést (typeahead-lekérdezéseket és javasolt dokumentumokat), amikor a felhasználó elkezd begépelni egy keresőmezőt. Ebben az oktatóanyagban az automatikusan befejezett lekérdezéseket és a javaslatok eredményeit külön, majd együtt fogjuk megjeleníteni. Egy felhasználónak csak két vagy három karaktert kell beírnia az összes elérhető eredmény megkereséséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Javaslatok hozzáadása
> * Kiemelés hozzáadása a javaslatokhoz
> * Automatikus kiegészítés hozzáadása
> * Az autocomplete és a javaslatok egyesítése

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag egy sorozat részét képezi, és a C# oktatóanyagban létrehozott lapozási projektre épül [: keresési eredmények tördelése – Azure Cognitive Search](tutorial-csharp-paging.md).

Azt is megteheti, hogy letölti és futtatja a megoldást ehhez a konkrét oktatóanyaghoz: [3 – Add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead).

## <a name="add-suggestions"></a>Javaslatok hozzáadása

Kezdjük a legegyszerűbb esettel, ha alternatívákat ajánlunk fel a felhasználó számára: a javaslatok legördülő listája.

1. Az index. cshtml fájlban módosítsa `@id` a **TextBoxFor** utasítást a **azureautosuggest**értékre.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. Ezt az utasítást követve a záró ** &lt; /div &gt; **után adja meg ezt a parancsfájlt. Ez a szkript kihasználja az [automatikus kiegészítési widgetet](https://api.jqueryui.com/autocomplete/) a nyílt forráskódú jQuery UI könyvtárból, hogy bemutassa a javasolt eredmények legördülő listáját. 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    A "azureautosuggest" azonosító a fenti szkriptet csatlakoztatja a keresőmezőbe. A widget forrás opciója egy olyan javaslati metódusra van beállítva, amely meghívja az ajánlott API-t két lekérdezési paraméterrel: **kiemelések** és **fuzzy**, mindkettő hamis értékre van állítva ebben a példányban. Emellett a keresés indításához legalább két karakter szükséges.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Hivatkozások hozzáadása a jQuery-parancsfájlokhoz a nézethez

1. A jQuery-könyvtár eléréséhez módosítsa a &lt; &gt; nézet fájljának Head szakaszát a következő kódra:

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Mivel egy új jQuery-referenciát vezetünk be, az alapértelmezett jQuery-hivatkozást is el kell távolítani a _Layout. cshtml fájlban (a **nézetek/megosztott** mappában). Keresse meg a következő sorokat, és tegye megjegyzésbe az első parancsfájlt az ábrán látható módon. Ez a változás megakadályozza a jQuery-re mutató hivatkozások összeütközését.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Most már használhatjuk az előre definiált autocomplete jQuery-függvényeket.

### <a name="add-the-suggest-action-to-the-controller"></a>A javaslat művelet hozzáadása a vezérlőhöz

1. A Kezdőlap vezérlőben adja hozzá a **javaslat** műveletet (azaz az **oldal** művelet után).

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    A **felső** paraméter azt határozza meg, hogy hány eredmény legyen visszaadva (ha nincs megadva, az alapértelmezett érték 5). A rendszer az Azure indexben adja meg a _javaslatot_ , amely akkor történik meg, ha az adatkészletet, és nem egy ügyfélalkalmazás, például az oktatóanyag. Ebben az esetben a javaslat neve "SG", és megkeresi a **pezsgő** mezőt – semmi más. 

    A zavaros megfeleltetés lehetővé teszi a "közelről lemaradt" beállítás használatát a kimenetben, legfeljebb egy szerkesztési távolságot. Ha a **Highlights** paraméter értéke TRUE (igaz), akkor a félkövér HTML-címkék hozzáadódnak a kimenethez. Ezt a két paramétert igaz értékre kell állítani a következő szakaszban.

2. Előfordulhat, hogy szintaktikai hibákat talál. Ha igen, adja hozzá a következő két **using** utasítást a fájl elejéhez.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Futtassa az alkalmazást. A "Po" beírásakor számos lehetőséget kap, például? Most próbálkozzon a "PA" kifejezéssel.

    ![A "Po" beírása két javaslatot mutat be](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Figyelje meg, hogy a beírt betűknek egy szót _kell_ elkezdeniük, és nem egyszerűen bele kell foglalni a szót.

4. A View szkriptben állítsa a **&fuzzy** igaz értékre, majd futtassa újra az alkalmazást. Most írja be a "Po" kifejezést. Figyelje meg, hogy a keresés azt feltételezi, hogy egy levél nem megfelelő!
 
    ![A "PA" kifejezés beírása az igaz értékre](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Ha érdekli, a [Lucene lekérdezési szintaxisa az Azure Cognitive Search](./query-lucene-syntax.md) ismerteti a fuzzy keresésekben használt logikát.

## <a name="add-highlighting-to-the-suggestions"></a>Kiemelés hozzáadása a javaslatokhoz

Javíthatjuk a javaslatok megjelenését a felhasználó számára úgy, hogy a **Highlights** paramétert igaz értékre állítja. Először azonban hozzá kell adnia egy kódot a nézethez a félkövér szöveg megjelenítéséhez.

1. A View (index. cshtml) nézetben adja hozzá a következő parancsfájlt a fent megadott **azureautosuggest** -parancsfájl után.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. Most módosítsa a szövegmező AZONOSÍTÓját, hogy az a következőképpen legyen beolvasva.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Futtassa újra az alkalmazást, és a javaslatok között a beírt szöveg félkövérrel jelenik meg. Tegyük fel, hogy beírja a "PA" kifejezést.
 
    ![A "PA" beírása kiemeléssel](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. A fenti kiemelési parancsfájlban használt logika nem üzembiztos. Ha egy olyan kifejezést ad meg, amely ugyanabban a névben kétszer jelenik meg, a félkövérrel szedett eredmények nem eléggé, amit szeretne. Próbálja beírni a "mo" kifejezést.

    Ha a fejlesztőnek választ kell adni az egyik kérdésre, ha egy parancsfájl "elég jól" működik, és mikor kell megoldania a hirtelen fordulatot. Ebben az oktatóanyagban nem vesszük figyelembe a további lépéseket, de a pontos algoritmus megtalálásával azt érdemes figyelembe venni, hogy a kiemelés nem hatékony az adataihoz. További információ: [találatok kiemelése](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Automatikus kiegészítés hozzáadása

Egy másik változat, amely némileg eltér a javaslatoktól, az autocomplete (néha "type-Ahead"), amely egy lekérdezési kifejezést végez. A felhasználói élmény fokozása előtt a legegyszerűbb implementációval fogunk kezdeni.

1. Adja meg a következő parancsfájlt a nézetbe az előző parancsfájlok követésével.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. Módosítsa a szövegmező AZONOSÍTÓját, hogy az a következőképpen legyen beolvasva.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. A Home vezérlőben meg kell adnia az **automatikus kiegészítés** műveletet, azaz a **javasolt** művelet alatt.

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Figyelje meg, hogy ugyanazt a *javaslati* funkciót használjuk, amelyet a "SG" néven használunk az automatikus kiegészítési keresésben, ahogy a javaslatok esetében tettük (ezért csak a szállodai nevek automatikus kiegészítését próbáljuk ki).

    Számos **AutocompleteMode** -beállítás létezik, és **OneTermWithContext**használunk. További beállítások leírását az [automatikus kiegészítési API](/rest/api/searchservice/autocomplete) -ban találja.

4. Futtassa az alkalmazást. Figyelje meg, hogy a legördülő listában megjelenő beállítások köre egyetlen szó. Próbálja meg beírni a szavakat az "újra" kifejezéssel kezdődően. Figyelje meg, hogy a lehetőségek száma több karakter beírásával csökken.

    ![Alapszintű autocomplete beírása](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Ebben az esetben a korábban futtatott javaslatok szkriptje valószínűleg hasznosabb ennél az autocomplete parancsfájlnál. Ahhoz, hogy a lehető leghatékonyabban lehessen az autocomplete-t kihasználni, a legjobb megoldás a javaslat keresése.

## <a name="combine-autocompletion-and-suggestions"></a>Az autocomplete és a javaslatok egyesítése

Az autocomplete és a javaslatok együttes használata a lehető legbonyolultabb lehetőség, és valószínűleg a legjobb felhasználói élményt nyújtja. Azt szeretnénk, hogy megjelenjen a begépelt szöveg, amely az első választás az Azure Cognitive Search számára a szöveg kitöltéséhez. Emellett számos javaslatot szeretnénk legördülő listaként használni.

Vannak olyan kódtárak, amelyek ezt a funkciót használják – gyakran "beágyazott autocomplete" néven vagy hasonló névvel. Ezzel a funkcióval azonban natív módon Implementáljuk ezt a funkciót, így láthatjuk, hogy mi történik. Ebben a példában először a vezérlőn fogunk dolgozni.

1. Hozzá kell adnia egy műveletet a vezérlőhöz, amely csak egy autocomplete eredményt ad vissza, valamint megadott számú javaslatot. Ezt a műveletet **AutocompleteAndSuggest**fogjuk hívni. A Kezdőlap vezérlőben adja hozzá a következő műveletet a többi új művelet után.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    Az **eredmények** listájának tetején egy autocomplete beállítást ad vissza, amelyet a javaslatok követnek.

2. A nézetben először egy olyan trükköt teszünk elérhetővé, hogy egy világosszürke autocomplete szó közvetlenül a felhasználó által megadott merészebb-szöveg alatt legyen megjelenítve. A HTML relatív elhelyezést is tartalmaz erre a célra. Módosítsa a **TextBoxFor** utasítást (és a körülötte &lt; &gt; lévő div-utasításokat) a következőre, és ha úgy gondolja, hogy az **alatta** lévő második keresőmező közvetlenül a normál keresőmező alatt található, akkor a keresőmező 39 képpont kihúzásával állítsa le az alapértelmezett helyükről.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Figyelje meg, hogy a rendszer ismét megváltoztatja az azonosítót, hogy **azureautocomplete** ebben az esetben.

3. A nézetben is adja meg a következő parancsfájlt, miután az eddig megadott összes parancsfájlt elvégezte. Nagyon sok mindent.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Figyelje meg, hogy az **intervallum** függvény okosan használja a mögöttes szöveget, ha már nem egyezik meg a felhasználó által beírt szöveggel, és azt is, hogy a felhasználó beírásakor ugyanezt a kis-és nagybetűket (a "PA" a "PA", a "PA", a "PA" karakterláncot) adja meg, hogy az átfedett szöveg szép legyen.

    Tekintse át a szkript megjegyzéseit, és ismerkedjen meg a teljes körű ismeretekkel.

4. Végezetül egy kisebb módosítást kell végezni két HTML-osztályon, hogy azok transzparensek legyenek. Adja hozzá a következő sort a **searchBoxForm** és a **searchBox** osztályhoz a Hotels. css fájlban.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Most futtassa az alkalmazást. Írja be a "PA" kifejezést a keresőmezőbe. Az automatikus kiegészítési javaslatként "Palace"-t kap, valamint két, "PA"-t tartalmazó szállodát?

    ![Bevitel beágyazott automatikus kiegészítéssel és javaslatok](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Próbálja megismételni az automatikus kiegészítési javaslat elfogadását, majd próbálja meg kiválasztani a javaslatokat a nyílbillentyűkkel és a TAB billentyűvel, és próbálkozzon újra az egérrel és egyetlen kattintással. Győződjön meg arról, hogy a parancsfájl szépen kezeli az összes helyzetet.

    Dönthet úgy, hogy egyszerűbb betölteni egy olyan könyvtárat, amely ezt a szolgáltatást kínálja Önnek, de most már tudja, hogy van-e legalább egy módszer a beágyazott automatikus kiegészítés működéséhez.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* Az autocomplete (más néven "type-Ahead") és a javaslatok lehetővé tehetik, hogy a felhasználó csak néhány kulcsot írjon be, hogy pontosan mit szeretne.
* Az autocomplete és a közös használatú javaslatok gazdag felhasználói élményt nyújtanak.
* Mindig tesztelje az autocomplete függvényeket a bemenet összes formájával.
* A **setInterval** függvény használata hasznos lehet a felhasználói felületi elemek ellenőrzése és javítása érdekében.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyagban megvizsgáljuk a felhasználói élmény javításának egy másik módját, a dimenziók használatával szűkítheti a keresést egyetlen kattintással.

> [!div class="nextstepaction"]
> [C# oktatóanyag: dimenziók használata a navigáláshoz – Azure Cognitive Search](tutorial-csharp-facets.md)