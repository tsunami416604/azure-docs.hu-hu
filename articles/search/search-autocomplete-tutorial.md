---
title: Automatikus kiegészítés és javaslatok hozzáadása a keresőmezőben
titleSuffix: Azure Cognitive Search
description: Engedélyezze a felhasználóként történő lekérdezési műveleteket az Azure Cognitive Search szolgáltatásban azáltal, hogy javaslatokat tesz, és olyan kéréseket készít, amelyek automatikusan kiegészítik a keresőmezőt kész kifejezésekkel vagy kifejezésekkel. A javasolt egyezéseket is visszaadhat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d6c1819366fede0b1e81e43bc92ed56af93b39fd
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114963"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Javaslatok vagy automatikus kiegészítés hozzáadása az Azure Cognitive Search alkalmazáshoz

Ebből a cikkből megtudhatja, hogy miként [használhatja](https://docs.microsoft.com/rest/api/searchservice/suggestions) a javaslatokat és [az automatikus kiegészítést](https://docs.microsoft.com/rest/api/searchservice/autocomplete) egy hatékony keresőmező létrehozásához, amely támogatja a beírási viselkedést.

+ *A javaslatok* gépelés közben keresési eredményeket hoznak létre, ahol minden javaslat egyetlen találat vagy keresési dokumentum az indexből, amely megegyezik az eddig beírt adatokkal. 

+ *Az automatikus kiegészítés* a szavak vagy kifejezések "befejezésével" hozza létre a lekérdezéseket. Az eredmények visszaadása helyett egy lekérdezést hajt végre, amelyet ezután végrehajthat az eredmények visszaadására. A javaslatokhoz ugyanúgy, mint a lekérdezésben befejezett szó vagy kifejezés, az indexben lévő egyezésen alapul. A szolgáltatás nem kínál lekérdezéseket, amelyek nulla eredményt adnak vissza az indexben.

A **dotnethowtoautocomplete** mintakódot letöltheti és futtathatja a szolgáltatások kiértékeléséhez. A mintakód egy előre összeállított indexet céloz meg, amely [et NYCJobs bemutató adatokkal töltötték](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)ki. A NYCJobs index tartalmaz egy [javaslatajánló konstrukciót,](index-add-suggesters.md)amely a javaslatok vagy az automatikus kiegészítés használatának követelménye. Használhatja az előkészített index üzemeltetett egy sandbox szolgáltatás, vagy [töltse fel a saját index](#configure-app) használatával egy adatbetöltő a NYCJobs mintamegoldás. 

A **DotNetHowToAutocomplete** minta bemutatja mind a javaslatokat, mind az automatikus kiegészítést a C# és a JavaScript nyelvi verziókban. A C# fejlesztők az [Azure Cognitive Search .NET SDK-t](https://aka.ms/search-sdk)használó ASP.NET MVC-alapú alkalmazáson keresztül léphetnek át. Az automatikus kiegészítés és a javasolt lekérdezéshívások végrehajtásának logikája a HomeController.cs fájlban található. A JavaScript-fejlesztők az IndexJavaScript.cshtml fájlban találnak egyenértékű lekérdezési logikát, amely az [Azure Cognitive Search REST API-hoz](https://docs.microsoft.com/rest/api/searchservice/)intézett közvetlen hívásokat is tartalmazza. 

Mindkét nyelvi verzió esetében az előtér-felhasználói élmény a [jQuery UI](https://jqueryui.com/autocomplete/) és [az XDSoft](https://xdsoft.net/jqplugins/autocomplete/) könyvtárakon alapul. Ezeket a könyvtárakat arra használjuk, hogy a javaslatokat és az automatikus kiegészítést támogató keresőmezőt hozzuk létre. A keresőmezőben gyűjtött bemenetek javaslatokkal és automatikus kiegészítési műveletekkel vannak párosítva, például a HomeController.cs vagy az IndexJavaScript.cshtml fájlban meghatározottakkal.

Ez a gyakorlat végigvezeti a következő feladatokon:

> [!div class="checklist"]
> * Keresőmező megvalósítása JavaScript ben, és kérelmek kiadása javasolt egyezésekre vagy automatikus anamnézisre vonatkozó anamnézisben
> * A C#-ban adja meg a javaslatokat és az automatikus kiegészítési műveleteket HomeController.cs
> * JavaScript ben hívja meg a REST API-kat közvetlenül, hogy ugyanazt a funkciót

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Search szolgáltatás nem kötelező ebben a gyakorlatban, mert a megoldás egy élő sandbox szolgáltatást használ egy előkészített NYCJobs bemutató index. Ha ezt a példát a saját keresési szolgáltatásán szeretné futtatni, az utasításokat a [NYC-feladatok indexkonfigurálása című](#configure-app) témakörben találja.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), bármilyen kiadásban. A mintakódot és az utasításokat az ingyenes közösségi kiadáson tesztelték.

* Töltse le a [DotNetHowToAutoComplete mintát.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)

A minta átfogó, amely javaslatokat, automatikus kiegészítés, köralapú navigáció és ügyféloldali gyorsítótárazás. Tekintse át a felolvasott és a megjegyzéseket a minta ajánlatainak teljes leírásáért.

## <a name="run-the-sample"></a>Minta futtatása

1. Nyissa **meg az Automatikus kiegészítéstutorial.sln alkalmazást** a Visual Studióban. A megoldás egy ASP.NET MVC-projektet tartalmaz, amely kapcsolatban áll a NYC Jobs bemutató indexével.

2. A projekt futtatásához és az oldal kiválasztott böngészőbe való betöltéséhez nyomja le az F5 billentyűt.

A képernyő felső részén látni fogja a C# vagy JavaScript kiválasztásának lehetőségét. A C# beállítás a böngészőből hívja be a HomeControllert, és az Azure Cognitive Search .NET SDK használatával olvassa be az eredményeket. 

A JavaScript-beállítás közvetlenül a böngészőből hívja meg az Azure Cognitive Search REST API-t. Ez a beállítás általában észrevehetően jobb teljesítményt nyújt, mivel kiveszi a vezérlőt a folyamatból. Kiválaszthatja az igényeinek és a nyelvi beállításainak megfelelő lehetőséget. Számos automatikus kiegészítési példa van az oldalon, amelyek mindegyikhez útmutatást tartalmaznak. Mindegyik példa javasolt mintaszöveggel rendelkezik, amelyet kipróbálhat.  

Próbáljon meg beírni néhány betűt a keresőmezőkbe, és figyelje meg, mi történik.

## <a name="search-box"></a>Keresőmező

Mind a C# és a JavaScript verziók, a keresőmező megvalósítása pontosan ugyanaz. 

Nyissa meg az **Index.cshtml** fájlt a \Views\Home mappában a kód megtekintéséhez:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Ez a példa egy egyszerű beviteli szövegdoboz, amely egy stílusosztállyal, a JavaScript által hivatkozott azonosítóval és helyőrző szöveggel rendelkezik.  A varázslat a beágyazott JavaScriptben van.

A C# nyelvminta JavaScript et használ az Index.cshtml fájlban a [jQuery ui automatikus kiegészítési könyvtárának](https://jqueryui.com/autocomplete/)kihasználásához. Ez a tár hozzáadja az automatikus kiegészítésélményét a keresőmezőhöz azáltal, hogy aszinkron hívásokat kezdeményez az MVC-vezérlőhöz a javaslatok lekéréséhez. A JavaScript nyelvi verziója az IndexJavaScript.cshtml fájlban található. Ez tartalmazza az alábbi parancsfájlt a keresősávhoz, valamint a REST API-hívásokat az Azure Cognitive Search-hez.

Nézzük meg a JavaScript-kódot az első példa, amely meghívja jQuery UI automatikus kiegészítés funkció, átadva a kérelmet a javaslatok:

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

A fenti kód fut a böngészőben az oldal betöltése konfigurálásához jQuery UI automatikus kiegészítés a "example1a" beviteli mezőbe.  A `minLength: 3` biztosítja, hogy a javaslatok csak akkor legyenek láthatóak, amikor legalább három karakter van a keresőmezőben.  A forrásérték nagyon fontos:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

A fenti sor megmondja a jQuery UI automatikus kiegészítés funkció, ahol, hogy a lista elemek jelennek meg a keresőmező alatt. Mivel ez a projekt egy MVC-projekt, meghívja a Javaslat függvényt HomeController.cs, amely a lekérdezési javaslatok visszaadásának logikáját tartalmazza (a következő szakaszban található Javaslat ról). Ez a függvény néhány paramétert is átad a kiemelések, az intelligens megfeleltetés és a kifejezés vezérléséhez. Az automatikus kiegészítés JavaScript API hozzáadja a kifejezés paraméter.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>A minta kibővítése az intelligens egyeztetés támogatása érdekében

Az intelligens kereséssel akkor is lekérheti az eredményeket közeli találatok alapján, ha a felhasználó elírt egy szót a keresőmezőben. Bár nem szükséges, jelentősen javítja a robusztusság a typeahead tapasztalat. Próbáljuk ki ezt úgy, hogy a forrássorban engedélyezzük az intelligens egyeztetést.

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

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>JQuery automatikus kiegészítése az Azure Cognitive Search automatikus kiegészítésével

Eddig a keresési UX-kód a javaslatokra összpontosult. A következő kódblokk a jQuery felhasználói felület automatikus kiegészítési funkcióját (az index.cshtml 91. sora) mutatja, amely az Azure Cognitive Search automatikus kiegészítésére vonatkozó kérést adja meg:

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

## <a name="c-example"></a>C# példa

Most, hogy áttekintettük a weboldal JavaScript-kódját, nézzük meg a C# kiszolgálóoldali vezérlőkódot, amely ténylegesen lekéri a javasolt egyezéseket az Azure Cognitive Search .NET SDK használatával.

Nyissa meg a **HomeController.cs** fájlt a Vezérlők könyvtár alatt. 

Az első dolog, amit észrevehet, egy módszer `InitSearch`az osztály tetején, amit . Ez a módszer létrehoz egy hitelesített HTTP index ügyfél az Azure Cognitive Search szolgáltatáshoz. További információ: [Az Azure Cognitive Search használata .NET alkalmazásból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)című témakörben olvashat.

A 41-es vonalon figyelje meg a Suggest funkciót. Ez alapján a [DocumentsOperationsExtensions.Suggest módszer](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

A Suggest függvény két paramétert vesz fel, amelyek meghatározzák, hogy a rendszer a találatok kiemeléseit adja vissza, vagy intelligens egyeztetést használ a keresési kifejezés bevitele mellett. A metódus létrehoz egy [SuggestParameters objektumot,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)amelyet ezután átad a Javaslat API-nak. A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

A 69-es vonalon figyelje meg az Automatikus kiegészítés funkciót. Ez a [DocumentsOperationsExtensions.Autocomplete metóduson](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)alapul.

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

Az Automatikus kiegészítés függvény a keresési kifejezés bevitelét veszi át. A metódus létrehoz egy [Automatikus KiegészítésParaméter objektumot.](https://docs.microsoft.com/rest/api/searchservice/autocomplete) A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

(Választható) Adjon hozzá egy töréspontot a Suggest függvény elejéhez, és lépegessen végig a kódon. Figyelje meg az SDK által visszaadott választ, és hogyan konvertálja azt a metódusból visszaadott eredményre.

A többi példa az oldalon kövesse ugyanazt a mintát, hogy adjunk találat kiemelés és a facets támogatja az ügyféloldali gyorsítótárazás az automatikus kiegészítés eredményeit. Nézze át ezeket, hogy megértse, hogyan működnek, és hogyan használhatók a keresés során.

## <a name="javascript-example"></a>Példa JavaScript-re

Az automatikus kiegészítés Javascript-implementációja és a javaslatok meghívja a REST API-t, az URI-t használva az index és a művelet meghatározásához. 

A JavaScript implementációjának áttekintéséhez nyissa meg az **IndexJavaScript.cshtml fájlt.** Figyelje meg, hogy a jQuery ui automatikus kiegészítés függvény is a keresőmezőbe, gyűjtése keresési kifejezés bemenetek és aszinkron hívások at Azure Cognitive Search a javasolt egyezések vagy befejezett kifejezések lekérése. 

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

Ha összehasonlítja ezt a példát a fenti példával, amely meghívja az Otthoni vezérlőt, számos hasonlóságot fog észlelni.  Az automatikus kiegészítés `minLength` `position` konfigurációja, és pontosan ugyanaz. 

Itt a forrás a jelentős különbség. Ahelyett, hogy meghívja a Javaslat metódust az otthoni vezérlőben, egy REST-kérelem jön létre egy JavaScript-függvényben, és az Ajax használatával hajtják végre. A választ a rendszer ezután „sikeresen” feldolgozza, és a forrásként használja.

A REST-hívások uri-k használatával határozzák meg, hogy [automatikus kiegészítésvagy](https://docs.microsoft.com/rest/api/searchservice/autocomplete) [javaslatok](https://docs.microsoft.com/rest/api/searchservice/suggestions) API-hívás történik-e. A következő URI-k a 9., illetve a 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

A 148-as vonalon található egy `autocompleteUri`parancsfájl, amely meghívja a . Az első `suggestUri` hívás a 39-es vonalon van.

> [!Note]
> Rest-hívások kezdeményezése a szolgáltatás JavaScript-ben itt kínálják, mint egy kényelmes bemutatása a REST API-t, de nem kell értelmezni, mint egy ajánlott eljárás vagy ajánlás. Egy API-kulcs és végpont egy parancsfájlba való belefoglalása megnyitja a szolgáltatást szolgáltatásmegtagadási támadások ra bárki számára, aki el tudja olvasni ezeket az értékeket a parancsfájlon kívül. Bár a biztonságos használata JavaScript tanulási célokra, esetleg az ingyenes szolgáltatás üzemeltetett indexek, javasoljuk, hogy java vagy C# indexelési és lekérdezési műveletek éles kód. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>NYCJobs konfigurálása a szolgáltatás futtatásához

Eddig a házigazdája NYCJobs demo indexet használta. Ha azt szeretné, hogy teljes mértékben látható legyen az összes kód, beleértve az indexet is, kövesse az alábbi utasításokat az index létrehozásához és betöltéséhez a saját keresési szolgáltatásában.

1. [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a példához ingyenes szolgáltatást is használhat. 

   > [!Note]
   > Ha az ingyenes Azure Cognitive Search szolgáltatást használja, három indexre van korlátozva. A NYCJobs adatbetöltő két indexet hoz létre. Ellenőrizze, hogy a szolgáltatás elegendő hellyel rendelkezik-e az új indexek fogadásához.

1. [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) mintakód letöltése.

1. A NYCJobs mintakód DataLoader mappájában nyissa meg a **DataLoader.sln fájlt** a Visual Studióban.

1. Adja meg az Azure Cognitive Search szolgáltatás csatlakozási adatait. Nyissa meg az App.config-ot a DataLoader projekten belül, és módosítsa a TargetSearchServiceName és a TargetSearchServiceApiKey appSettings parancsot, hogy az tükrözze az Azure Cognitive Search szolgáltatást és az Azure Cognitive Search szolgáltatás API-kulcsát. Ez az információ megtalálható az Azure Portalon.

1. Nyomja le az F5 billentyűt az alkalmazás elindításához, két index létrehozásához és a NYCJob mintaadatok importálásához.

1. Nyissa **meg az Automatikus kiegészítéstutorial.sln fájlt,** és az Automatikus **kiegészítésbemutató** projektweb.config fájlját szerkesztse. Módosítsa a SearchServiceName és a SearchServiceApiKey értékeket a keresési szolgáltatásra érvényes értékekre.

1. Az alkalmazás futtatásához nyomja le az F5 billentyűt. A mintawebalkalmazás az alapértelmezett böngészőben nyílik meg. A felhasználói felület megegyezik a sandbox verzió, csak az index és az adatok a szolgáltatás üzemelteti.

## <a name="next-steps"></a>További lépések

Ez a példa bemutatja az automatikus kiegészítést és javaslatokat támogató keresőmező létrehozásának alapvető lépéseit. Látta, hogyan hozhat létre egy ASP.NET MVC-alkalmazást, és az Azure Cognitive Search .NET SDK vagy REST API használatával javaslatokat kérhet.

Következő lépésként próbálja meg integrálni a javaslatokat és az automatikus kiegészítést a keresési élménybe. A következő cikkekkel kell segíteni.

> [!div class="nextstepaction"]
> [Rest API-javaslatok](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [rest API-mezők](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [indexattribútumának automatikus kiegészítése az Index REST API-n](https://docs.microsoft.com/rest/api/searchservice/create-index)

