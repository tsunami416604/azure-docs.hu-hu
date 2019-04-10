---
title: A keresőmezőbe – Azure Search vonatkozó javaslatokat és az automatikus kiegészítés hozzáadása
description: Engedélyezze a typeahead lekérdezési műveleteket az Azure Search javaslattevők létrehozásával, és töltse ki a befejezett szavakat vagy kifejezéseket egy keresőmezőt tartalmazó kérelmek kidolgozásában.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 7c06489af7c418a437c2e7a8714c89bee1414c2b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357967"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Példa: Az Azure Search-alkalmazás javaslata vagy az automatikus kiegészítés hozzáadása

Ebből a cikkből megtudhatja, hogyan használható [javaslatok](https://docs.microsoft.com/rest/api/searchservice/suggestions) és [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) hozhat létre egy hatékony keresési mezőbe, amely támogatja a keresési –--beíráskor viselkedéseket.

+ *Javaslatok* beírása, ahol minden javaslat-e az indexből, amely megfelel a korábban beírt eddig egyetlen eredmény létrehozott javasolt eredményeket is. 

+ *Az automatikus kiegészítés*, [előzetes verziójú funkció](search-api-preview.md), "befejezése" szó vagy kifejezés, amely a felhasználó jelenleg éppen gépel. Helyett eredményt adnak vissza, befejezi a lekérdezést, amely majd eredmények hajthat végre. Javaslatok, a befejezett szót vagy kifejezést a lekérdezésben van való határozza meg a egyezés az indexben. A szolgáltatás nem fog biztosítani az indexben nulla eredményt visszaadó lekérdezések.

Töltse le, és futtassa a mintát kódot **DotNetHowToAutocomplete** értékelheti ki ezeket a funkciókat. A mintakód célozza meg benne egy előre elkészített index használatával [NYCJobs bemutató adatok](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). A NYCJobs index tartalmaz egy [javaslattevő szerkezet](index-add-suggesters.md), amelyek esetében a javaslatok vagy az automatikus kiegészítés követelmény. Használhatja az előkészített index egy tesztkörnyezet szolgáltatásban üzemeltetett vagy [saját-index feltöltéséhez](#configure-app) leltáradat-betöltő használatával a NYCJobs minta megoldásban található. 

A **DotNetHowToAutocomplete** minta azt mutatja be, javaslatokat és az automatikus kiegészítés, mindkét C# és a JavaScript nyelvű verzióit. C#a fejlesztők elolvasásával használó ASP.NET MVC-alapú alkalmazás az [Azure Search .NET SDK](https://aka.ms/search-sdk). A logika, hogy az automatikus kiegészítés és a javasolt lekérdezés meghívja a HomeController.cs fájlban található. JavaScript-fejlesztőinek megtalálja a megfelelő lekérdezés logikáját a IndexJavaScript.cshtml, amely tartalmazza a közvetlen hívások a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). 

Mindkét nyelvi verziók az előtér-felhasználói élmény alapján a [jQuery felhasználói felület](https://jqueryui.com/autocomplete/) és [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) kódtárakat. Ezek a kódtárak használatával hozhat létre a keresőmezőbe, javaslatokat és az automatikus kiegészítés támogatása. Bemeneti kifejezést a keresőmezőbe gyűjtött van párosítva vonatkozó javaslatokat és az automatikus kiegészítés műveletek, például a HomeController.cs vagy IndexJavaScript.cshtml.

Ebben a gyakorlatban mutatja be a következő feladatokat:

> [!div class="checklist"]
> * A beviteli mezőt megvalósítása a JavaScript és a probléma kérések javasolt egyezés vagy automatikus kiegészítéses feltételek
> * A C#, a HomeController.cs vonatkozó javaslatokat és az automatikus kiegészítés műveletek definiálása
> * A JavaScript közvetlenül a ugyanazokat a funkciókat biztosítanak a REST API-k meghívása

## <a name="prerequisites"></a>Előfeltételek

Az Azure Search szolgáltatást nem kötelező ehhez a gyakorlathoz, mivel a megoldás használ élő védőfal szolgáltatásüzemeltetési egy előkészített NYCJobs bemutató indexet. Ha szeretné futtatni az ebben a példában a saját keresési szolgáltatásával, [állások konfigurálása index](#configure-app) útmutatást.

* [A Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), bármely kiadás esetén. Mintakód és útmutató az ingyenes közösségi kiadása lettek tesztelve.

* Töltse le a [DotNetHowToAutoComplete minta](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

A minta, átfogó, vonatkozó javaslatokkal, automatikus kiegészítés, jellemzőalapú navigáció, valamint ügyféloldali gyorsítótárazás. Tekintse át a fontos és a Megjegyzések a minta kínál teljes leírását.

## <a name="run-the-sample"></a>Minta futtatása

1. Nyissa meg **AutocompleteTutorial.sln** a Visual Studióban. A megoldás tartalmaz, amely kapcsolódik az i állások bemutató index egy ASP.NET MVC-projektben.

2. A projekt futtatásához és az oldal kiválasztott böngészőbe való betöltéséhez nyomja le az F5 billentyűt.

A képernyő felső részén látni fogja a C# vagy JavaScript kiválasztásának lehetőségét. A C# lehetőség a böngészőből a HomeController hívások és eredmények lekérése az Azure Search .NET SDK használatával. 

A JavaScript lehetőség közvetlenül a böngészőből hívja be az Azure Search REST API-t. Ezt a beállítást általában észrevehetően jobb teljesítmény érdekében fog rendelkezni, mivel a tartományvezérlő a folyamat nem vesz igénybe. Kiválaszthatja az igényeinek és a nyelvi beállításainak megfelelő lehetőséget. Nincs olyan automatikus kiegészítés néhány példa a lapon minden egyes útmutatása alapján. Mindegyik példa javasolt mintaszöveggel rendelkezik, amelyet kipróbálhat.  

Próbáljon meg beírni néhány betűt a keresőmezőkbe, és figyelje meg, mi történik.

## <a name="search-box"></a>Keresőmező

Mindkét C# és JavaScript-verzió, a keresési mezőbe megvalósítás pontosan ugyanaz. 

Nyissa meg a **Index.cshtml** fájlt a mappába \Views\Home a kód megjelenítéséhez:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Ez a Stílusbeállítások, JavaScript és a helyőrző szöveg hivatkozik azonosító osztályát az egyszerű bemeneti szövegmezőt.  A beágyazott JavaScript a Magic Quadrant van.

A C# nyelven írt mintát Index.cshtml JavaScriptet használ kihasználhatja a [jQuery az automatikus kiegészítés felhasználói felületi kódtár](https://jqueryui.com/autocomplete/). Ebben a könyvtárban azáltal, hogy az MVC-vezérlő asynchronní volání kanálu javaslatokat beolvasni az automatikus kiegészítés élmény hozzáadja a keresőmezőbe. A JavaScript nyelvű IndexJavaScript.cshtml szerepel. Ez magában foglalja az alábbi parancsfájlt a keresősávba, csakúgy, mint az Azure Search REST API-hívások.

Lássuk a JavaScript-kódot az első példa, amely meghívja a jQuery felhasználói felület automatikus kiegészítés funkció, javaslatok a kérelem jóváhagyása:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

A fenti kód futtat a böngészőben a "example1a" beviteli mezőt a jQuery felhasználói felület automatikus kiegészítési funkciójának konfigurálása lap betöltése.  `minLength: 3` biztosítja, hogy javaslatok csak látható, amikor nincsenek legalább három karaktert a keresőmezőbe.  A forrásérték nagyon fontos:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

A fenti vonal jelzi a jQuery felhasználói felület automatikus kiegészítés funkció hová a keresőmező alatt megjelenítendő elemek listájának beolvasása. Mivel ennek MVC-projektben, a javaslat függvény meghívja a HomeController.cs, amely az adatszolgáltató (javaslat a következő szakaszban többet) lekérdezési javaslatok logikáját tartalmazza. Ez a funkció is továbbítja néhány paramétert vezérlő emeli ki, az intelligens megfelelő és az időszakot. Az automatikus kiegészítés JavaScript API hozzáadása a kifejezés paramétert.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>A minta kibővítése az intelligens egyeztetés támogatása érdekében

Az intelligens kereséssel akkor is lekérheti az eredményeket közeli találatok alapján, ha a felhasználó elírt egy szót a keresőmezőben. Bár nem kötelező, megbízhatóságát, egy typeahead élmény jelentős mértékben javítja. Próbáljuk ki ezt úgy, hogy a forrássorban engedélyezzük az intelligens egyeztetést.

A következő sort erről:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

módosítsa a következőre:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Indítsa el az alkalmazást az F5 billentyű lenyomásával.

Próbálja beírni például a „vezatő” kifejezést, és figyelje meg, hogy az eredmények a „vezető” kifejezéshez jelennek meg, bár ez nem egyezik pontosan a beírt betűkkel.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Azure Search automatikus kiegészítési funkciójának alapját automatikus kiegészítés jQuery

Eddig a keresés felhasználói kód a javaslatok rendelkezik lett ingyenes. A következő kódrészletet bemutatja a jQuery felhasználói felület automatikus kiegészítés funkció (sor 91 az index.cshtml), átadása egy kérés esetén az Azure Search automatikus kiegészítési funkciója:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C#-példa

Most, hogy áttekintette a JavaScript-kódot a weblap, nézzük meg a C# kiszolgálóoldali vezérlő kódot, amely ténylegesen kérdezi le a javasolt megegyezik az Azure Search .NET SDK használatával.

Nyissa meg a **HomeController.cs** fájlt a tartományvezérlők a könyvtárban. 

Először is Észreveheti a nevű osztály tetején módszer `InitSearch`. Ez hitelesített HTTP-indexügyfelet hoz létre az Azure Search szolgáltatáshoz. További információkért lásd: [használata az Azure Search .NET-alkalmazásból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

41. sorban figyelje meg, hogy a javaslat funkciót. -Alapú, a [DocumentsOperationsExtensions.Suggest metódus](/dotnet/api/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

A Suggest függvény két paramétert vesz fel, amelyek meghatározzák, hogy a rendszer a találatok kiemeléseit adja vissza, vagy intelligens egyeztetést használ a keresési kifejezés bevitele mellett. A módszer létrehoz egy [SuggestParameters objektum](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), amelyet majd átad a javaslat API. A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

69. sor figyelje meg, hogy az automatikus kiegészítés funkció. -Alapú, a [DocumentsOperationsExtensions.Autocomplete metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Az automatikus kiegészítés funkció a keresési kifejezés bemeneti vesz igénybe. A módszer létrehoz egy [AutoCompleteParameters objektum](https://docs.microsoft.com/rest/api/searchservice/autocomplete). A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

(Választható) Adjon hozzá egy töréspontot a Suggest függvény elejéhez, és lépegessen végig a kódon. Figyelje meg, hogy az SDK, és hogyan alakul át az eredményt adott vissza a metódus által visszaadott válasz.

Az oldalon az egyéb példák kövesse az azonos mintával való kiemelése és aspektusokat, az automatikus kiegészítés eredmények ügyféloldali gyorsítótárazás támogatása. Nézze át ezeket, hogy megértse, hogyan működnek, és hogyan használhatók a keresés során.

## <a name="javascript-example"></a>JavaScript-példa

Egy Javascript végrehajtása az automatikus kiegészítés és javaslatok meghívja a REST API, mint a forrás URI használatával adja meg az index és a művelet. 

JavaScript végrehajtása áttekintéséhez megnyitása **IndexJavaScript.cshtml**. Figyelje meg, hogy a felhasználói felület automatikus kiegészítés funkció jQuery is használja a keresőmezőbe, a keresési kifejezés bemenetek begyűjtésére és beolvasni az Azure Search aszinkron hívás megfelel a javasolt vagy használati befejeződött. 

Lássuk az első példa JavaScript-kódját:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Ha összehasonlítja ezt a kezdőlapvezérlőt meghívó fenti példával, több hasonlóságot láthat.  Az automatikus kiegészítés konfigurációja `minLength` és `position` ugyanazok, pontosan. 

Itt a forrás a jelentős különbség. Helyett a javasolt módszert hívja meg a kezdőkönyvtár-vezérlőben levő, a REST-kérés jön létre a JavaScript-függvény, és Ajax végre. A választ a rendszer ezután „sikeresen” feldolgozza, és a forrásként használja.

REST-hívások URI-k használatával adja meg e- [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) vagy [javaslatok](https://docs.microsoft.com/rest/api/searchservice/suggestions) API-hívás alatt történik. A következő URI-k rendre vannak 9 és 10, sorban.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

148. sorban, talál egy parancsfájlt, amely meghívja a `autocompleteUri`. Az első hívás `suggestUri` 39 sor van.

> [!Note]
> Így a szolgáltatás REST-hívások JavaScript érhető el itt a REST API egy kényelmes bemutató, de nem kell értelmezni a bevált gyakorlat vagy javaslat. Egy API-kulcs és a egy parancsfájlban végpont felvétele megnyílik a szolgáltatás akár szolgáltatásmegtagadási támadásokat bárki olvashatja a parancsfájl ki ezeket az értékeket. A biztonságos JavaScript tanulási célokra használandó, miközben talán lévő üzemeltetett az ingyenes szolgáltatás, az indexeket használatát javasoljuk a Java vagy C# indexelés és a lekérdezési műveletek az éles kódban. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>A szolgáltatás futtatásához NYCJobs konfigurálása

Mostanáig az üzemeltetett NYCJobs bemutató index már használja. Ha azt szeretné, hogy a teljes átláthatóságot kódja, beleértve az index kövesse ezeket az utasításokat, létrehozására és betöltésére az indexet a saját keresési szolgáltatásával.

1. [Az Azure Search szolgáltatás létrehozása](search-create-service-portal.md) vagy [keresse meg a meglévő service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az aktuális előfizetésben. Ebben a példában egy ingyenes szolgáltatás használhatja. 

   > [!Note]
   > Az ingyenes Azure Search szolgáltatás három indexre használatára van korlátozva. A NYCJobs adatbetöltő két indexet hoz létre. Ellenőrizze, hogy a szolgáltatás elegendő hellyel rendelkezik-e az új indexek fogadásához.

1. Töltse le [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) mintakódot.

1. A DataLoader mappában NYCJobs mintakódot, nyissa meg a **DataLoader.sln** a Visual Studióban.

1. Adja hozzá az Azure Search szolgáltatás azon kapcsolatadatokkal. Nyissa meg az App.config fájlt a DataLoader projektben, és módosítsa a TargetSearchServiceName és a TargetSearchServiceApiKey appSettings elemeket az Azure Search szolgáltatásnak és az Azure Search szolgáltatás API-kulcsának megfelelően. Ezek az Azure Portalon találhatók meg.

1. Nyomja le az F5 billentyűt az alkalmazás két indexek létrehozása, és importálja a NYCJob mintaadatokat indítása.

1. Nyissa meg **AutocompleteTutorial.sln** , és szerkessze a Web.config a **AutocompleteTutorial** projekt. A SearchServiceName és SearchServiceApiKey értékeket módosítsa a search szolgáltatás az érvényes értékek.

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt. A mintául szolgáló webalkalmazást az alapértelmezett böngészőben nyílik meg. A védőfal verzióra azonos azzal, csak az index és az adatokat a szolgáltatás-ban üzemelnek.

## <a name="next-steps"></a>További lépések

Ez a példa bemutatja az alapvető lépéseken, amellyel egy keresőmező, amely támogatja az automatikus kiegészítés és javaslatokat. Bemutatta, hogyan sikerült az ASP.NET MVC alkalmazás létrehozását, és javaslatokat beolvasni az Azure Search .NET SDK vagy a REST API használatával.

A következő lépésben, kísérlet vonatkozó javaslatokat és az automatikus kiegészítés integrálása a keresési funkciót. A következő hivatkozás cikkek webhelyünkre.

> [!div class="nextstepaction"]
> [Automatikus kitöltés REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Aspektusok indexattribútum Index létrehozása REST API-n](https://docs.microsoft.com/rest/api/searchservice/create-index)

