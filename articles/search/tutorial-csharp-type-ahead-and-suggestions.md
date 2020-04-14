---
title: Automatikus kiegészítés és javaslatok
titleSuffix: Azure Cognitive Search
description: Ez az oktatóanyag bemutatja az automatikus kiegészítést és a javaslatokat, hogy a legördülő listát használó felhasználók keresési kifejezésbevitelét gyűjtse össze. Egy meglévő szállodaprojektre épül.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 4391b565b684b74258b9c71da88600d4628b5c6f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259765"
---
# <a name="c-tutorial-add-autocomplete-and-suggestions---azure-cognitive-search"></a>C# oktatóanyag: Automatikus kiegészítés és javaslatok hozzáadása – Azure Cognitive Search

Megtudhatja, hogy miként valósíthatja meg az automatikus kiegészítést (typeahead lekérdezések és javasolt dokumentumok), amikor a felhasználó elkezd beírni egy keresőmezőbe. Ebben az oktatóanyagban az automatikusan befejezett lekérdezéseket és javaslatokeredményeit külön-külön jelenítjük meg, majd megmutatjuk az egyesítésük módját, hogy gazdagabb felhasználói élményt hozzanak létre. Előfordulhat, hogy a felhasználónak csak két vagy három karaktert kell beírnia az összes rendelkezésre álló eredmény megkereséséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Javaslatok hozzáadása
> * Kiemelés hozzáadása a javaslatokhoz
> * Automatikus kiegészítés hozzáadása
> * Automatikus kiegészítés és javaslatok kombinálása

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag egy sorozat része, és a C# oktatóanyagban létrehozott lapozási projektre [épül: Keresési eredmények tördelése - Azure Cognitive Search](tutorial-csharp-paging.md).

Másik lehetőségként letöltheti és futtathatja a megoldást ehhez az oktatóanyaghoz: [3-add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead).

## <a name="add-suggestions"></a>Javaslatok hozzáadása

Kezdjük a legegyszerűbb esetben kínál fel alternatívákat a felhasználó: a legördülő listát a javaslatokat.

1. Az index.cshtml fájlban módosítsa a **TextBoxFor** utasítást a következőkre.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    A kulcs itt az, hogy beállítottuk a keresőmező azonosítóját **az azureautosuggest-re.**

2. Ezt követően a ** &lt;/div&gt;** billentyűvel a záró /div kapcsolót követően adja meg ezt a parancsfájlt.

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

    Ezt a szkriptet ugyanazon az azonosítón keresztül kapcsoltuk a keresőmezőhöz. Emellett legalább két karakter szükséges a keresés elindításához, és **hívjuk** a javaslat művelet az otthoni vezérlőkét lekérdezési paraméterek: **kiemeli** és **fuzzy**, mind a beállítás hamis ebben az esetben.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Hivatkozás hozzáadása jquery parancsfájlok nézethez

Az automatikus kiegészítés funkció hívott a fenti szkript nem valami meg kell írni magunkat, mert elérhető a jquery könyvtárban. 

1. A jquery tár eléréséhez &lt;&gt; módosítsa a nézetfájl fejszakaszát a következő kódra.

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

2. El kell távolítanunk vagy ki kell fűznünk egy sorért, amely a _Layout.cshtml fájljának jquery-re hivatkozik (a **Nézetek/Megosztott** mappában). Keresse meg a következő sorokat, és fűzzön hozzá megjegyzést az első parancsfájlsorhoz az ábrán látható módon. Ezzel a módosítással elkerülhető a jqueryre való hivatkozások összeütközése.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Most már használhatjuk az előre definiált automatikus kiegészítés jquery függvényeket.

### <a name="add-the-suggest-action-to-the-controller"></a>A Javaslat művelet hozzáadása a vezérlőhöz

1. Az otthoni vezérlőben adja hozzá a **Javaslat** műveletet (például az **Oldal** művelet után).

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

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    A **Legfelső** paraméter azt adja meg, hogy hány eredményt kell visszaadni (ha nincs megadva, az alapértelmezett érték 5). A _javaslatajánló_ az Azure-indexben van megadva, amely az adatok beállításakor történik, és nem egy ügyfélalkalmazás, például ez az oktatóanyag. Ebben az esetben a szuggesztív az úgynevezett "sg", és megkeresi a **HotelName** mezőben - semmi más. 

    Fuzzy megfelelő lehetővé teszi a "közeli hiányzik" kell venni a kimenet. Ha a **kiemelési** paraméter értéke igaz, akkor a félkövér HTML-címkék hozzáadódnak a kimenethez. Ezt a két paramétert a következő szakaszban igazértékre állítjuk.

2. Előfordulhat, hogy szintaktikai hibákat észlel. Ha igen, adja hozzá a következő kettőt a fájl tetejére utasítások **használatával.**

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Futtassa az alkalmazást. Kapsz egy sor lehetőséget, ha beírja a "po", például? Most próbáld a "pa"-t.

    ![Gépelés "po" tárja fel a két javaslatot](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Figyelje meg, hogy a beírt betűknek el _kell_ kezdeniük egy szót, és nem egyszerűen bele kell foglalni a szóba.

4. A nézetparancsfájlban állítsa **&intelligens értékre,** és futtassa újra az alkalmazást. Most írja be a "po"-t. Figyelje meg, hogy a keresés feltételezi, hogy van egy betű rossz!
 
    ![A "pa" beírása true-ra állítva](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Ha érdekli, a [Lucene lekérdezés szintaxisa](https://docs.microsoft.com/azure/search/query-lucene-syntax) az Azure Cognitive Search részletesen ismerteti az intelligens keresések használt logika.

## <a name="add-highlighting-to-the-suggestions"></a>Kiemelés hozzáadása a javaslatokhoz

Javíthatjuk a javaslatok megjelenését a felhasználó számára egy kicsit, ha a **kiemeli** paramétert igazra állítjuk. Azonban először is hozzá kell adnunk néhány kódot a nézethez, hogy megjelenítsük a félkövér szöveget.

1. A nézetben (index.cshtml), adja hozzá a következő parancsfájlután az **azureautosuggest** parancsfájl t a fent megadott.

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

2. Most változtassa meg a szövegdoboz azonosítóját, így a következőképpen szól.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Futtassa újra az alkalmazást, és a beírt szöveget félkövérrel kell látnia a javaslatokban. Próbáld meg beírni, hogy "apa".
 
    !["pa" beírása kiemeléssel](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. A fenti kiemelő parancsfájlban használt logika nem üzembiztos. Ha olyan kifejezést ad meg, amely kétszer jelenik meg ugyanabban a névben, a félkövér eredmények nem egészen azok, amelyeket szeretne. Próbálja meg beírni a "mo" szót.

    Az egyik kérdés a fejlesztő kell válaszolni, amikor egy script működik "elég jól", és mikor kell a hirtelen fordulat foglalkozni. Nem fogjuk figyelembe kiemelve tovább ebben a bemutatóban, de megtalálni egy pontos algoritmust, amit figyelembe kell venni, ha tovább emeljük.

## <a name="add-autocompletion"></a>Automatikus kiegészítés hozzáadása

Egy másik változat, amely némileg eltér a javaslatoktól, az automatikus kiegészítés (néha "type-ahead"). Ismét a legegyszerűbb megvalósítással kezdjük, mielőtt a felhasználói élmény javítására térnénk át.

1. Írja be a következő parancsfájlt a nézetbe a korábbi parancsfájlok követésével.

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

2. Most változtassa meg a szövegdoboz azonosítóját, így a következőképpen szól.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Az otthoni vezérlőben be kell lépnünk az **Automatikus kiegészítés** műveletbe, például a **Javaslat** művelet alatt.

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

    Figyeljük meg, hogy ugyanazt a *szuggesztív* funkciót használjuk, az úgynevezett "sg", az automatikus kiegészítéskeresésben, mint a javaslatokesetében (ezért csak a szállodanevek automatikus kiegészítését próbáljuk elérni).

    Számos **Automatikus kiegészítésmód-beállítás** létezik, és a **OneTermWithContext -t**használjuk. Az [Azure automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) című dokumentumban található a lehetőségek tartományának leírása.

4. Futtassa az alkalmazást. Figyelje meg, hogy a legördülő listában megjelenő beállítások tartománya egyetlen szó. Próbálja meg beírni a "re" kezdetű szavakat. Figyelje meg, hogy a beállítások száma hogyan csökken, ahogy egyre több betűt ír be.

    ![Gépelés alapvető automatikus kiegészítéssel](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    A jelenlegi állás szerint a korábban futtatott javaslatok parancsfájlvalószínűleg hasznosabb, mint ez az automatikus kiegészítési parancsfájl. Ahhoz, hogy az automatikus kiegészítés felhasználóbarátabb legyen, a legjobb, ha hozzáadja a javaslatkereséshez.

## <a name="combine-autocompletion-and-suggestions"></a>Automatikus kiegészítés és javaslatok kombinálása

Az automatikus kiegészítés és a javaslatok kombinálása a legösszetettebb lehetőségünk, és valószínűleg a legjobb felhasználói élményt nyújtja. Azt akarjuk, hogy a begépelt szöveggel összhangban jelenítse meg az Azure Cognitive Search első számú választását a szöveg automatikus kitöltéséhez. Is, szeretnénk egy sor javaslatot, mint egy legördülő lista.

Vannak olyan könyvtárak, amelyek ezt a funkciót kínálják - gyakran "inline autocompletion"-nak vagy hasonló névnek nevezik. Azonban fogunk natívan végrehajtani ezt a funkciót, így láthatja, mi folyik itt. Ebben a példában először a vezérlőn kezdjük a munkát.

1. Hozzá kell adnunk egy műveletet a vezérlőhöz, amely csak egy automatikus kiegészítési eredményt ad vissza, valamint egy adott számú javaslatot. Fogjuk hívni ezt a műveletet **AutocompleteAndSuggest**. Az otthoni vezérlőben adja hozzá a következő műveletet a többi új műveletet követve.

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

    Egy automatikus kiegészítési lehetőség jelenik meg az **eredménylista** tetején, amelyet az összes javaslat követ.

2. A nézetben először egy trükköt valósítunk meg, hogy a felhasználó által beírt, merészebb szöveg alatt egy világosszürke automatikus kiegészítési szó jelenik meg. A HTML tartalmazza az erre a célra szolgáló relatív pozicionálást. Változtassa meg a **TextBoxFor** &lt;utasítást (és a környező div&gt; nyilatkozatokat) a következőkre, megjegyezve, hogy egy második keresőmező, amely **alatt** található, közvetlenül a normál keresőmező alatt található, a keresőmező 39 pixeles lehúzásával az alapértelmezett helyéről!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Megjegyzés: az azonosítót ismét módosítjuk, hogy ebben az esetben **az azureautocomplete-ra** módosítsuk.

3. Szintén a nézetben adja meg a következő szkriptet, miután az összes szkriptet beírt eddig. Elég sok minden van benne.

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

    Figyeljük meg az okos használata **intervallum** funkció mind törölje az alapul szolgáló szöveget, ha már nem felel meg, amit a felhasználó írja, és azt is, hogy állítsa be ugyanazt az esetet (felső vagy alsó), mint a felhasználó gépelés (a "pa" megfelel "PA", "pA", "Pa" keresés kor), úgy, hogy a berakott szöveg ügyes.

    Olvassa el a megjegyzéseket a forgatókönyvet, hogy egy teljesebb megértést.

4. Végül, meg kell, hogy egy kisebb kiigazítást két HTML osztály, hogy azok átlátható. Adja hozzá a következő sort a **searchBoxForm** és **searchBox** osztályokhoz a hotels.css fájlban.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Most futtassa az alkalmazást. Írja be a "pa" szót a keresőmezőbe. Ön kap "palota", mint az automatikus kiegészítés javaslatot, valamint két szálloda, amely tartalmazza a "pa"?

    ![Gépelés szövegközi automatikus kiegészítéssel és javaslatokkal](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Próbálja meg a tabbing gombot, hogy elfogadja az automatikus kiegészítés javaslatot, és próbálja meg a javaslatokat a nyílbillentyűvel és a tabbillentyűvel, majd próbálkozzon újra az egérrel, és egyetlen kattintással. Ellenőrizze, hogy a parancsfájl kezeli-e ezeket a helyzeteket.

    Dönthet úgy, hogy egyszerűbb betölteni egy olyan könyvtárat, amely ezt a funkciót kínálja önnek, de most már legalább egy módot tud a beépített automatikus kiegészítés működéséhez!

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő elvihető ételek ebből a projektből:

* Az automatikus kiegészítés (más néven "type-ahead") és a javaslatok lehetővé teszik, hogy a felhasználó csak néhány kulcsot gépeljen be, hogy pontosan megtalálja, amit akar.
* Az automatikus kiegészítés és a közös munka javaslatok gazdag felhasználói élményt nyújthatnak.
* Mindig tesztelje az automatikus kiegészítési funkciókat a bemenet minden formájával.
* A **setInterval** függvény használata hasznos lehet a felhasználói felület elemeinek ellenőrzésében és javításában.

## <a name="next-steps"></a>További lépések

A következő bemutatóban egy pillantást vetünk a felhasználói élmény javítására, a facets használatával egyetlen kattintással szűkítheti a kereséseket.

> [!div class="nextstepaction"]
> [C# oktatóanyag: A navigáció támogatásához használjon aspektusait - Azure Cognitive Search](tutorial-csharp-facets.md)


