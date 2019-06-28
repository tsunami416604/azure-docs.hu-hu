---
title: C#autocompletion és javaslatok – Azure Search-oktatóanyag
description: Ebben az oktatóanyagban épül, amely a "Keresési eredmények tördelés – az Azure Search" projekt autocompletion és javaslatokat adhat hozzá. Az célja egy gazdagabb felhasználói élmény. Ismerje meg, hogyan kombinálhatja a beágyazott autocompletion javaslatok legördülő listája.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 01c0819fd0bf525739675ad756031cafc1a51673
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434677"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-search"></a>C#oktatóanyag: Autocompletion és javaslatok – Azure Search hozzáadása

Megvalósítása autocompletion (gépelés közbeni és javaslatok) amikor a felhasználó elindítja, írja be a keresőmezőbe. Ebben az oktatóanyagban azt fogja gépelés közbeni és javaslat eredményeknek külön-külön megjelenítése, majd egy metódusa kombinálva egy gazdagabb felhasználói élmény megjelenítése. A felhasználó csak előfordulhat írja be a két vagy három kulcsok érhetők el az összes eredmény található. Ebben az oktatóanyagban az alakzatot a lapozófájl projekt létrehozott összeállítja a [ C# oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md) oktatóanyag.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adja hozzá a javaslatok
> * A javaslatok a kiemelés hozzáadása
> * Autocompletion hozzáadása
> * Autocompletion és javaslatok

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

Rendelkezik a [ C# oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md) projektet, majd futtassa. Ez a projekt a saját verziója, az előző oktatóanyag során végrehajtott, vagy telepítheti a Githubról: [Első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Adja hozzá a javaslatok

Kezdjük a legegyszerűbb eset be a felhasználó alternatívái ajánlat: javaslatok legördülő listája.

1. Az index.cshtml fájl, módosítsa a **TextBoxFor** a következő utasítást.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    A kulcs itt, hogy a keresőmező segítségével azonosítója rendelkezik beállított **azureautosuggest**.

2. A jelen nyilatkozatban a Bezárás után a következő  **&lt;/div&gt;** , adja meg ezt a szkriptet.

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

    A keresőmező segítségével ugyanezzel az azonosítóval. Ez a szkript csatlakoztatta azt Ezenkívül egy legalább két karakter van szükség, a keresés indításához, és nevezzük a **javaslat** két lekérdezési paraméterek az otthoni vezérlőre műveletét: **kiemeli** és **intelligens**, mindkettő nastavit nA hodnotu false ebben a példányban.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Adja hozzá a jquery parancsfájlok hivatkozásokat a nézet

Az automatikus kiegészítés funkció a szkriptben nevű nem áll megírása magunkat érhető el a jquery-erőforrástárban kell, hogy valami. 

1. A jquery könyvtár eléréséhez, módosítsa a &lt;fő&gt; nézet fájl az alábbi kód szakaszban.

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

2. Távolítsa el, vagy tegye megjegyzésbe, egy sor hivatkozik a _Layout.cshtml fájl jquery is kell (a a **nézetek/megosztott** mappát). Keresse meg a következő sorokat, és tegye megjegyzésbe az első szkript sor látható módon. Ez a változás elkerülhető, hogy ütköző jquery mutató hivatkozásokat.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Most már az előre meghatározott automatikus kiegészítés jquery függvények használatával.

### <a name="add-the-suggest-action-to-the-controller"></a>A javasolt művelet a vezérlő hozzáadása

1. A kezdőlap vezérlő, adja hozzá a **javaslat** művelet (tegyük fel, miután a **oldal** művelet).

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

    A **felső** paraméter határozza meg, hány eredményét adja vissza (Ha nincs megadva, az alapértelmezett érték 5). A _javaslattevő_ az Azure indexbe, amely történik, ha az adatok be van állítva, és nem a például ebben az oktatóanyagban egy ügyfélalkalmazás van megadva. Ebben az esetben a javaslattevő neve "sg", és keres a **Mezőmeghatározása** mező - semmi más. 

    Az Intelligens egyeztetés lehetővé teszi, hogy a "near-tévesztések" szerepeltetni a kimenetet. Ha a **kiemeli** paraméter értéke true, majd a félkövérrel szedett HTML-címkéket kerülnek a kimenetbe. Azt állítja be a két paraméter igaz értékre a következő szakaszban.

2. Néhány szintaktikai hibákat kaphat. Ha igen, adja hozzá a következő két **használatával** utasítások a fájl elejéhez.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Futtassa az alkalmazást. Lehetőségek "po", például megadásakor kapott? Próbálja ki most a "pa".

    ![Írja be a "hivatkozás" tárja fel a két javaslatok](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Figyelje meg, hogy a betűk megadnia _kell_ indítsa el a word, és nem egyszerűen belefoglalhatók a Wordben.

4. Állítsa be a parancsfájl megtekintése **& intelligens** igaz értékre, és futtassa újra az alkalmazást. Most adja meg a "hivatkozás". Figyelje meg, hogy a keresés feltételezi, hogy egy betűt helytelen van!
 
    ![Írja be az igaz értékre az intelligens beállítása "pa"](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Ha érdekli, a [Lucene lekérdezési szintaxis az Azure Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) azon logika az intelligens keresés részletesen ismerteti.

## <a name="add-highlighting-to-the-suggestions"></a>A javaslatok a kiemelés hozzáadása

Fejleszthetnénk tovább a javaslatok a felhasználónak a megjelenését, beállításával a **kiemeli** paraméter igaz. Azonban először kell némi kódot hozzáadni a nézet a félkövér szöveg megjelenítéséhez.

1. A nézetben (index.cshtml), adja hozzá a következő szkript után a **azureautosuggest** fent megadott parancsfájlt.

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

2. A szövegmező azonosítója módosítsa úgy a következőképpen olvas be.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Futtassa ismét az alkalmazást, és a javaslatok a beírt szöveget félkövérrel kell megjelennie. Tegyük fel írjon be "pa".
 
    ![Írja be a "pa" kiemeléssel](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. A fenti kiemelését szkriptben használt logikai nem biztos. Ha megad egy kifejezés, amely akkor jelenik meg kétszer ugyanazzal a névvel, a félkövér eredmények nem lesznek elég mit érdemes. Próbálja ki, írja be a "hónap".

    A fejlesztő igényekhez kérdések egyike az, ha működik "is elegendő" parancsfájl, és mikor kell a régi stílusú lehet képes kezelni. Mi lesz nem tart kiemelése minden további, ebben az oktatóanyagban, de a pontos algoritmus, érdemes figyelembe venni, ha tovább tart a kiemelés keresése.

## <a name="add-autocompletion"></a>Autocompletion hozzáadása

Egy másik változata, amely kissé eltér a javaslatok, autocompletion (más néven "gépelés közbeni"). Ismét hogy kezdődik a legegyszerűbb megvalósítás, mielőtt a felhasználói élmény javítására.

1. Adja meg a következő szkriptet a nézetbe, és a korábbi parancsfájlok a következő.

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

2. Szövegbeviteli mezőben azonosítója módosítsa úgy a következőképpen olvas be.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Az otthoni vezérlőben írja be kell a **automatikus kiegészítés** művelet, például alább az **javaslat** művelet.

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

    Figyelje meg, hogy használjuk azonos *javaslattevő* függvényt, mint a javaslatok (tehát Szálloda neve csak szeretne automatikus kiegészítés) nevű "sg", az automatikus kiegészítés keresési.

    Nincsenek számos **AutoCompleteMode tulajdonság** beállításait, és használja **OneTermWithContext**. Tekintse meg [Azure automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) annak leírását, a számos lehetőség közül választhat.

4. Futtassa az alkalmazást. Figyelje meg, hogy milyen lehetőségek a legördülő listában jelennek meg a tartomány egyetlen szavakat. Próbálja beírni az "újra" kezdetű szavak. Figyelje meg, hogyan csökkenti a lehetőségek száma szerint több betűt típusú.

    ![Írja be az alapszintű autocompletion](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Ebben a formában, a javaslatokat szkriptet futtatta korábban valószínűleg hasznosabb lehet, mint a autocompletion szkript. Ahhoz, hogy nagyobb mértékben felhasználóbarát autocompletion, legjobb hozzáadódik a javaslat keresés.

## <a name="combine-autocompletion-and-suggestions"></a>Autocompletion és javaslatok

Autocompletion és javaslatok a legösszetettebb beállításai, és valószínűleg nyújtja a legjobb felhasználói élményt. Szeretnénk, hogy a megjelenített, a beírt szöveget tartalmazó beágyazott, az Azure Search szolgáltatást autocompleting a szöveg első megválasztása. Javaslatok számos is, mint egy legördülő lista szeretnénk.

Nincsenek tárak, amelyek ezt a funkciót – más néven "beágyazott autocompletion" vagy hasonló nevet kínálnak. Azonban fogjuk ezt a funkciót, natív módon valósítani, így láthatja, mi is történik. Ebben a példában először indítsa el a munkát a vezérlő fogjuk.

1. Kell venni egy műveletet a tartományvezérlővel, amely csak egyetlen autocompletion eredményt, és javaslatokat megadott számú adja vissza. Ez a művelet lesz nevezzük **AutocompleteAndSuggest**. A kezdőlap vezérlő adja hozzá a következő műveletet, a többi új műveleteket követően.

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

    Az egyik autocompletion lehetőség tetején adja vissza a **eredmények** listában az összes javaslat követ.

2. A nézetben először hoznunk körben, hogy egy világos szürke autocompletion szó jogosultság bolder a felhasználó által beírt szöveg jelenik meg. HTML magában foglalja a relatív pozicionálás erre a célra. Módosítása a **TextBoxFor** utasítás (és annak körülvevő &lt;div&gt; utasítások) a következő megjegyezni, hogy azonosította az eseményt egy második keresőmezőbe **alá** jobb alatt áll az Normál keresőmezőbe, a Keresés mezőbe 39 képpont kiindulásként engedményt alapértelmezett helyére!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Megjegyzés: az ID megváltozik, ismét **azureautocomplete** ebben az esetben.

3. Is a nézetet, adja meg a következő parancsfájlt, amikor az összes az eddigi megadott parancsfájlokat. Nincs elég sokat rá.

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

    Figyelje meg a ötletes a **időköz** mindkét függvény alapul szolgáló szöveg törléséhez, amikor azt már nem egyezik, amit a felhasználó beírja, és is a (felső vagy alsó) nagybetűket állítja be a felhasználó éppen gépel (a "pa" egyezik "PA", "pA", "Pa" Amikor a keresés), hogy az átfedett szöveg mutatnak.

    Olvassa el a megjegyzéseket, a parancsfájl beolvasása egy átfogóbb ismertetése.

4. Végezetül össze kell két HTML osztályra, így átlátható kisebb beállításához. Adja hozzá a következő sort a **searchBoxForm** és **searchBox** osztályok, a hotels.css fájlban.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Most futtassa az alkalmazást. A keresőmezőbe írja be a "pa". Kapunk "palace" a javaslat, amely "pa" tartalmaz két hotels együtt?

    ![Írja be a soron belüli automatikus kiegészítés és javaslatok](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Próbálkozzon a szövegre, fogadja el a javaslat, és válassza ki a javaslatok a nyíl billentyűk és a tab billentyűt, és próbálja meg újra az egérgombot, és egyetlen kattintással. Győződjön meg arról, hogy a parancsfájl eligazíthatja kezeli-e ezekben a helyzetekben.

    Dönthet úgy, hogy egyszerűbb betölteni a könyvtárban, amely ezt a szolgáltatást kínál, de most már ismeri a beágyazott autocompletion gyorsan munkához legalább egy módja!

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő takeaways a projekt:

* Autocompletion (más néven az "gépelés közbeni"), és javaslatokat is lehetővé teszik a felhasználónak csak néhány kulcsok pontosan, amit szeretnének megkereséséhez írja be.
* Autocompletion és működnek együtt a javaslatok gazdag felhasználói élményt biztosíthat.
* Mindig tesztelje a bemenet minden űrlap autocompletion együttműködik.
* Használatával a **setInterval** funkció ellenőrzésével és javításával felhasználói felületi elemeket hasznos lehet.

## <a name="next-steps"></a>További lépések

A következő oktatóanyagban van a felhasználói élmény javítása érdekében egy másik módja egy pillantást értékkorlátozással használatával szűkítheti a keresést egyetlen kattintással.

> [!div class="nextstepaction"]
> [C#Oktatóanyag: A navigációt – Azure Search értékkorlátozással használata](tutorial-csharp-facets.md)


