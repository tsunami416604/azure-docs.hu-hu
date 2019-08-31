---
title: Javaslatok és automatikus kiegészítés hozzáadása a keresőmezőbe – Azure Search
description: Typeahead-lekérdezési műveletek engedélyezése a Azure Search a javaslatok létrehozásával és a befejezett kifejezésekkel vagy kifejezésekkel rendelkező keresőmező kitöltésével kapcsolatos kérelmek összeállításával.
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183290"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Javaslatok vagy automatikus kiegészítés hozzáadása a Azure Search alkalmazáshoz

Ebből a cikkből megtudhatja, hogyan használhatók a [javaslatok](https://docs.microsoft.com/rest/api/searchservice/suggestions) és az [automatikus kiegészítések](https://docs.microsoft.com/rest/api/searchservice/autocomplete) egy olyan hatékony keresőmező létrehozásához, amely támogatja a keresési típusos viselkedéseket.

+ A *javaslatok* a beírt eredmények alapján jönnek létre, ahol az egyes javaslatok egyetlen eredmény az indextől, amely megfelel az eddig beírt adatoknak. 

+ *Automatikus kiegészítés* "befejezi" a felhasználó által jelenleg begépelt szót vagy kifejezést. Az eredmények visszaadása helyett egy lekérdezést végez, amelyet aztán végrehajthat az eredmények visszaadásához. Ahogy a javaslatok esetében, a lekérdezésben szereplő befejezett szó vagy kifejezés az indexben található egyezésen alapul. A szolgáltatás nem kínál olyan lekérdezéseket, amelyek nulla eredményt adnak vissza az indexben.

A funkciók kiértékeléséhez letöltheti és futtathatja a **DotNetHowToAutocomplete** a mintakód használatával. A mintakód egy előre elkészített indexet céloz meg, amely [NYCJobs bemutató adatokkal](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)van feltöltve. A NYCJobs index tartalmaz egy [](index-add-suggesters.md)javaslat-összeállítást, amely a javaslatok vagy az automatikus kiegészítések használatára vonatkozó követelmény. Használhatja a sandbox szolgáltatásban üzemeltetett előkészített indexet, vagy [feltöltheti saját indexét](#configure-app) egy adatbetöltő használatával a NYCJobs-minta megoldásban. 

A **DotNetHowToAutocomplete** minta mindkét javaslatot és az automatikus kiegészítést egyaránt mutatja C# mind a, mind a JavaScript nyelvi verzióban. C#a fejlesztők átadhatnak egy ASP.NET MVC-alapú alkalmazást, amely a [Azure Search .net SDK](https://aka.ms/search-sdk)-t használja. Az automatikus kiegészítés és a javasolt lekérdezési hívások logikája a HomeController.cs fájlban található. A JavaScript-fejlesztők egyenértékű lekérdezési logikát találnak a IndexJavaScript. cshtml, amely közvetlen hívásokat tartalmaz a [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). 

Mindkét nyelvi verzió esetében az előtér-felhasználói élmény a [jQuery felhasználói felületén](https://jqueryui.com/autocomplete/) és a [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) -könyvtárakon alapul. Ezeket a kódtárakat a javaslatok és az automatikus kiegészítések támogatását támogató keresőmező létrehozásához használjuk. A keresőmezőbe gyűjtött bemenetek a javaslatokkal és az automatikus kiegészítési műveletekkel (például a HomeController.cs vagy a IndexJavaScript. cshtml definiált módon) vannak párosítva.

Ez a gyakorlat végigvezeti a következő feladatokon:

> [!div class="checklist"]
> * Keresési beviteli mező implementálása a JavaScriptben, és a javasolt egyezések vagy az autocompleted-feltételekre vonatkozó kérések kiadása
> * A C#alkalmazásban a javaslatok és az automatikus kiegészítési műveletek meghatározása a HomeController.cs
> * A JavaScriptben a REST API-k közvetlen meghívásával adja meg ugyanazt a funkciót

## <a name="prerequisites"></a>Előfeltételek

Egy Azure Search szolgáltatás nem kötelező ehhez a gyakorlathoz, mert a megoldás egy élő sandbox szolgáltatást használ, amely előkészített NYCJobs-bemutató indexet üzemeltet. Ha ezt a példát saját keresési szolgáltatásán szeretné futtatni, tekintse meg a [NYC-feladatok indexének konfigurálása](#configure-app) című témakört.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), bármilyen kiadás. A mintakód és az utasítások tesztelése az ingyenes közösségi kiadásban történt.

* Töltse le a [DotNetHowToAutoComplete mintát](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

A minta átfogó, a javaslatokat, az automatikus kiegészítést, a sokoldalú navigációt és az ügyféloldali gyorsítótárazást is magában foglalja. Tekintse át a további tudnivalókat és megjegyzéseket a minta kínálatának teljes leírásához.

## <a name="run-the-sample"></a>Minta futtatása

1. Nyissa meg a **AutocompleteTutorial. SLN** a Visual Studióban. A megoldás egy ASP.NET MVC-projektet tartalmaz, amely a New York Jobs bemutató indexével létesített kapcsolatban áll.

2. A projekt futtatásához és az oldal kiválasztott böngészőbe való betöltéséhez nyomja le az F5 billentyűt.

A képernyő felső részén látni fogja a C# vagy JavaScript kiválasztásának lehetőségét. A C# kapcsoló meghívja a HomeController a böngészőből, és a Azure Search .net SDK-t használja az eredmények lekéréséhez. 

A JavaScript lehetőség közvetlenül a böngészőből hívja be az Azure Search REST API-t. Ez a lehetőség általában észrevehetően jobb teljesítményt nyújt, mivel a vezérlőt a folyamaton kívülre viszi. Kiválaszthatja az igényeinek és a nyelvi beállításainak megfelelő lehetőséget. Az oldalon több automatikus kiegészítési példa is található. Mindegyik példa javasolt mintaszöveggel rendelkezik, amelyet kipróbálhat.  

Próbáljon meg beírni néhány betűt a keresőmezőkbe, és figyelje meg, mi történik.

## <a name="search-box"></a>Keresőmező

Mind a C# , mind a JavaScript-verziók esetében a keresőmező implementációja pontosan ugyanaz. 

A kód megtekintéséhez nyissa meg az **index. cshtml** fájlt a \Views\Home mappában:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Ez a példa egy egyszerű beviteli szövegmező, amelynek a stílusa, a JavaScript által hivatkozott azonosító, valamint a helyőrző szövege.  A Magic a beágyazott JavaScriptben található.

A C# nyelvi minta a JavaScriptet használja az index. cshtml, hogy kihasználja a [jQuery felhasználói felületének automatikus kiegészítésének könyvtárát](https://jqueryui.com/autocomplete/). Ez a kódtár hozzáadja az automatikus kiegészítési funkciót a keresőmezőbe, hogy aszinkron hívásokat kezdeményez az MVC-vezérlőnek a javaslatok lekéréséhez. A JavaScript nyelvi verziója a IndexJavaScript. cshtml. Magában foglalja az alábbi parancsfájlt a keresősáv számára, valamint REST API a Azure Search hívásait.

Nézzük meg az első példához tartozó JavaScript-kódot, amely a jQuery felhasználói felület automatikus kiegészítése funkciót hívja meg, amely a javaslatok iránti kérelemben szerepel:

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

A fenti kód az oldal betöltése böngészőben fut a "example1a" beviteli mezőhöz tartozó jQuery felhasználói felület automatikus kiegészítésének konfigurálásához.  A `minLength: 3` biztosítja, hogy a javaslatok csak akkor legyenek láthatóak, amikor legalább három karakter van a keresőmezőben.  A forrásérték nagyon fontos:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

A fenti sor megadja a jQuery felhasználói felület automatikus kiegészítése funkciót, ahol lekérdezheti a keresőmező alatt megjelenítendő elemek listáját. Mivel ez a projekt egy MVC-projekt, meghívja a HomeController.cs található javaslati függvényt, amely a lekérdezési javaslatok visszaadására vonatkozó logikát tartalmazza (További információ a következő szakaszban javasolt). Ez a függvény néhány paramétert is továbbít a csúcsfények, a zavaros egyezés és a kifejezés szabályozásához. Az automatikus kiegészítés JavaScript API hozzáadja a kifejezés paramétert.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>A minta kibővítése az intelligens egyeztetés támogatása érdekében

Az intelligens kereséssel akkor is lekérheti az eredményeket közeli találatok alapján, ha a felhasználó elírt egy szót a keresőmezőben. Habár nem szükséges, jelentősen javítja a typeahead-élmény megbízhatóságát. Próbáljuk ki ezt úgy, hogy a forrássorban engedélyezzük az intelligens egyeztetést.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>a jQuery automatikus kiegészítése Azure Search automatikus kiegészítéssel

Eddig a keresési UX-kód a javaslatokra van központilag beállítva. A következő kódrészlet a jQuery felhasználói felület automatikus kiegészítése funkciót mutatja (az index. cshtml-ben a 91. sor), Azure Search automatikus kiegészítésre vonatkozó kérelemben:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
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

## <a name="c-example"></a>C#például

Most, hogy áttekintette a weboldalhoz tartozó JavaScript-kódot, nézzük meg a C# kiszolgálóoldali vezérlő kódját, amely ténylegesen lekéri a javasolt egyezéseket a Azure Search .net SDK használatával.

Nyissa meg a **HomeController.cs** fájlt a vezérlők könyvtárban. 

Az első dolog, amit észrevesz, egy metódus a nevű `InitSearch`osztály tetején. Ez a metódus egy hitelesített HTTP-index ügyfelet hoz létre a Azure Search szolgáltatáshoz. További információ: a [Azure Search használata .NET-alkalmazásokból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Az 41. sorban figyelje meg az ajánlott funkciót. Ez a [DocumentsOperationsExtensions. javaslat metóduson](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)alapul.

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

A Suggest függvény két paramétert vesz fel, amelyek meghatározzák, hogy a rendszer a találatok kiemeléseit adja vissza, vagy intelligens egyeztetést használ a keresési kifejezés bevitele mellett. A metódus létrehoz egy [SuggestParameters objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), amelyet a rendszer továbbít az ajánlott API-nak. A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

Az 69. sorban figyelje meg az automatikus kiegészítési függvényt. Ez a [DocumentsOperationsExtensions.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)autocomplete metóduson alapul.

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

Az automatikus kiegészítési függvény a keresési kifejezés bemenetét veszi igénybe. A metódus létrehoz egy [AutoCompleteParameters objektumot](https://docs.microsoft.com/rest/api/searchservice/autocomplete). A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

(Választható) Adjon hozzá egy töréspontot a Suggest függvény elejéhez, és lépegessen végig a kódon. Figyelje meg az SDK által visszaadott választ, valamint azt, hogy hogyan történik a metódus által visszaadott eredmény konvertálása.

Az oldalon található további példák ugyanazt a mintát követik, hogy a találatok kiemelését és aspektusait is hozzáadja az automatikus kiegészítési eredmények ügyféloldali gyorsítótárazásának támogatásához. Nézze át ezeket, hogy megértse, hogyan működnek, és hogyan használhatók a keresés során.

## <a name="javascript-example"></a>JavaScript-példa

Az automatikus kiegészítés és a javaslatok JavaScript-implementációja meghívja a REST API, egy URI-t használva a forrásként az index és a művelet megadásához. 

A JavaScript megvalósításának áttekintéséhez nyissa meg a **IndexJavaScript. cshtml**. Figyelje meg, hogy a jQuery felhasználói felület automatikus kiegészítési funkciója a keresőmező esetében is használatos, a keresési kifejezés bemenetének összegyűjtése és az aszinkron hívások Azure Search a javasolt egyezések vagy befejezett feltételek beolvasása érdekében. 

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

Ha összehasonlítja a fenti példával, amely meghívja a vezérlőt, több hasonlóságot láthat.  A `minLength` és`position` az automatikus kiegészítés konfigurációja pontosan ugyanaz. 

Itt a forrás a jelentős különbség. Ahelyett, hogy meghívja a javasolt metódust a Kezdőlap vezérlőben, egy REST-kérést hoz létre egy JavaScript-függvényben, és az Ajax használatával hajtja végre. A választ a rendszer ezután „sikeresen” feldolgozza, és a forrásként használja.

A REST-hívások URI-k használatával határozzák meg, hogy van-e [automatikus kiegészítési](https://docs.microsoft.com/rest/api/searchservice/autocomplete) vagy [javaslatok](https://docs.microsoft.com/rest/api/searchservice/suggestions) API-hívás. A következő URI-k a 9. és a 10. sorban találhatók.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

A 148. sorban egy olyan parancsfájlt talál, amely meghívja a `autocompleteUri`-t. Az első hívás `suggestUri` a 39. sorban.

> [!Note]
> A szolgáltatás a JavaScriptben való REST-hívásait a REST API kényelmes bemutatásaként ajánljuk, de nem ajánlott eljárásként vagy javaslatként értelmezni. Az API-kulcs és a végpont egy parancsfájlba való belefoglalásával a szolgáltatás akár szolgáltatásmegtagadási támadásokat is megnyithat mindazoknak, akik a parancsfájlból elolvashatják ezeket az értékeket. Noha a JavaScript használata a tanulás szempontjából biztonságos, például az ingyenes szolgáltatásban üzemeltetett indexeken, javasoljuk a Java használatát, illetve C# az indexelési és lekérdezési műveletekhez a termelési kódban. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>NYCJobs konfigurálása a szolgáltatáson való futtatáshoz

Eddig az üzemeltetett NYCJobs-bemutató indexet használta. Ha teljes körű láthatóságot szeretne a kódban, beleértve az indexet is, kövesse az alábbi utasításokat az index saját keresési szolgáltatásbeli létrehozásához és betöltéséhez.

1. [Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a példához használhatja az ingyenes szolgáltatást. 

   > [!Note]
   > Az ingyenes Azure Search szolgáltatás három indexre használatára van korlátozva. A NYCJobs adatbetöltő két indexet hoz létre. Ellenőrizze, hogy a szolgáltatás elegendő hellyel rendelkezik-e az új indexek fogadásához.

1. [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) -mintakód letöltése.

1. A NYCJobs-mintakód DataLoader mappájában nyissa meg a **DataLoader. SLN** mappát a Visual Studióban.

1. Adja hozzá a Azure Search szolgáltatáshoz tartozó kapcsolatbeállításokat. Nyissa meg az App.config fájlt a DataLoader projektben, és módosítsa a TargetSearchServiceName és a TargetSearchServiceApiKey appSettings elemeket az Azure Search szolgáltatásnak és az Azure Search szolgáltatás API-kulcsának megfelelően. Ezek az információk a Azure Portal találhatók.

1. Nyomja le az F5 billentyűt az alkalmazás elindításához, két index létrehozásához és a NYCJob-mintaadatok importálásához.

1. Nyissa meg a **AutocompleteTutorial. SLN** fájlt, és szerkessze a web. config fájlt a **AutocompleteTutorial** projektben. Módosítsa a SearchServiceName és a SearchServiceApiKey értékeket a keresési szolgáltatás számára érvényes értékekre.

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt. Megnyílik a minta webalkalmazás az alapértelmezett böngészőben. A felhasználói élmény megegyezik a sandbox verziójával, csak az index és az adatai futnak a szolgáltatásban.

## <a name="next-steps"></a>További lépések

Ez a példa az automatikus kiegészítést és a javaslatokat támogató keresőmező létrehozásához szükséges alapvető lépéseket mutatja be. Megismerte, hogyan építhet ki egy ASP.NET MVC-alkalmazást, és hogyan használhatja a Azure Search .NET SDK-t vagy REST API a javaslatok lekéréséhez.

A következő lépés a javaslatok és az automatikus kiegészítés integrálásának megkísérlése a keresési élménybe. A következő cikkek segítséget nyújtanak.

> [!div class="nextstepaction"]
> [Automatikus kitöltés REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Aspektusok indexattribútum Index létrehozása REST API-n](https://docs.microsoft.com/rest/api/searchservice/create-index)

