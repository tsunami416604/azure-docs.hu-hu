---
title: Oktatóanyag az automatikus kiegészítés, a keresési mezőbe – Azure Search hozzáadása
description: Az adatközpontú alkalmazások végfelhasználói élménye javításának példái az Azure Search automatikus kitöltésével és javaslati API-kkal.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 10f86a482fbb35e7276f8f689a7eba184a7b624b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316182"
---
# <a name="tutorial-add-auto-complete-to-your-search-box-using-azure-search"></a>Oktatóanyag: Automatikus kiegészítés, a keresési mezőbe, használja az Azure Search hozzáadása

Ez az oktatóanyag ismerteti, hogyan használhatja a [javaslatokat](https://docs.microsoft.com/rest/api/searchservice/suggestions), az [automatikus kitöltést](https://docs.microsoft.com/rest/api/searchservice/autocomplete) és az [aspektusokat](search-faceted-navigation.md) az [Azure Search REST API-ban](https://docs.microsoft.com/rest/api/searchservice/) és a [.NET SDK-ban](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) egy hatékony keresőmező felépítéséhez. A *javaslatok* a tényleges találatokat ajánlanak annak alapján, amit a felhasználó eddig begépelt. Az *automatikus kitöltés*, amely az Azure Search [új előzetes verziójú funkciója](search-api-preview.md), kifejezéseket nyújt az indexből, amelyekkel kiegészíthető a felhasználó által jelenleg beírt kifejezés. Több módszert hasonlítunk össze a felhasználói termelékenység növelése és a keresett elemek gyors és könnyű megtalálása érdekében. Ehhez közvetlenül biztosítjuk a felhasználónak a beíráskor a keresés gazdag funkcióit.

Ez az oktatóanyag végigvezeti egy ASP.NET MVC-alapú alkalmazáson, amely C# nyelven hívja meg az [Azure Search .NET-ügyfélkódtárakat](https://aka.ms/search-sdk), és JavaScript nyelven hívja meg közvetlenül az Azure Search REST API-t. Az alkalmazás ebben az oktatóanyagban [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) mintaadatokkal feltöltött indexet céloz. Használhatja az NYC Jobs bemutatóban már konfigurált indexet, vagy feltöltheti a saját indexét a NYCJobs mintamegoldásban található adatbetöltővel. A minta a [jQuery felhasználói felületet](https://jqueryui.com/autocomplete/) és az [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) JavaScript-kódtárakat használja az automatikus kitöltést támogató keresőmező felépítéséhez. Ezekkel az összetevőkkel és az Azure Search használatával több példát lát arra, hogyan támogathatja az automatikus kitöltést gépelés közbeni javaslatokkal a keresőmezőben.

A következő feladatokat fogja elvégezni:

> [!div class="checklist"]
> * A megoldás letöltése és konfigurálása
> * Keresési szolgáltatás információinak hozzáadása alkalmazásbeállításokhoz
> * Keresésbeviteli mező megvalósítása
> * Távoli forrásból lekérést végző automatikus kitöltési lista támogatásának hozzáadása 
> * Javaslatok lekérése és automatikus kitöltés a .NET SDK és a REST API használatával
> * Ügyféloldali gyorsítótárazás támogatása a hatékonyabb működés érdekében 

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2017. Használhatja az ingyenes [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t is. 

* Töltse le a minta [forráskódot](https://github.com/azure-samples/search-dotnet-getting-started) az oktatóanyagból.

* (Választható) Egy aktív Azure-fiók és egy Azure Search szolgáltatás. Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/). A szolgáltatás kiépítéséhez itt talál segítséget: [Keresési szolgáltatás létrehozása](search-create-service-portal.md). A fiók és a szolgáltatás nem kötelező, mivel ez az oktatóanyag elvégezhető egy másik bemutatóhoz már meglévő üzemeltetett NYCJobs index használatával.

* (Választható) Töltse le a [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) mintakódot, hogy a NYCJobs adatokat a saját Azure Search szolgáltatásában lévő indexbe importálja.

> [!Note]
> Az ingyenes Azure Search szolgáltatás három indexre használatára van korlátozva. A NYCJobs adatbetöltő két indexet hoz létre. Ellenőrizze, hogy a szolgáltatás elegendő hellyel rendelkezik-e az új indexek fogadásához.

### <a name="set-up-azure-search-optional"></a>Az Azure Search beállítása (választható)

Kövesse a jelen szakasz lépéseit, ha a saját indexébe szeretné importálni a NYCJobs mintaalkalmazás adatait. Ez a lépés nem kötelező.  Ha a megadott mintaadatokat kívánja használni, ugorjon a következő szakaszra a minta futtatásához.

1. A NYCJobs mintakód DataLoader mappájában nyissa meg a DataLoader.sln megoldásfájlt a Visual Studióban.

1. Töltse fel az Azure Search szolgáltatás kapcsolatadatait.  Nyissa meg az App.config fájlt a DataLoader projektben, és módosítsa a TargetSearchServiceName és a TargetSearchServiceApiKey appSettings elemeket az Azure Search szolgáltatásnak és az Azure Search szolgáltatás API-kulcsának megfelelően.  Ezek az Azure Portalon találhatók meg.

1. Az alkalmazás elindításához nyomja le az F5 billentyűt.  Ez 2 indexet hoz létre, és importálja a NYCJob mintaadatokat.

1. Az oktatóanyag mintakódjában nyissa meg az AutocompleteTutorial.sln megoldásfájlt a Visual Studióban.  Nyissa meg a Web.config fájlt az AutocompleteTutorial projektben, és módosítsa a SearchServiceName és a SearchServiceApiKey értékeket a fenti értékekre.

### <a name="running-the-sample"></a>A minta futtatása

Most már készen áll az oktatóanyag mintaalkalmazásának futtatására.  Nyissa meg az AutocompleteTutorial.sln megoldásfájlt a Visual Studióban az oktatóanyag futtatásához.  A megoldás egy ASP.NET MVC-projektet tartalmaz.  A projekt futtatásához és az oldal kiválasztott böngészőbe való betöltéséhez nyomja le az F5 billentyűt.  A képernyő felső részén látni fogja a C# vagy JavaScript kiválasztásának lehetőségét.  A C# lehetőség meghívja a HomeController elemet a böngészőből, és az Azure Search .Net SDK-val lekéri az eredményeket.  A JavaScript lehetőség közvetlenül a böngészőből hívja be az Azure Search REST API-t.  Ezen lehetőség teljesítménye általában észrevehetően jobb, mert kiveszi a vezérlőt a folyamatból.  Kiválaszthatja az igényeinek és a nyelvi beállításainak megfelelő lehetőséget.  Az oldalon szerepel néhány automatikus kitöltési példa, mindegyikhez némi útmutatással.  Mindegyik példa javasolt mintaszöveggel rendelkezik, amelyet kipróbálhat.  Próbáljon meg beírni néhány betűt a keresőmezőkbe, és figyelje meg, mi történik.

## <a name="how-this-works-in-code"></a>Ennek működése kódban

Most, hogy látta a példákat a böngészőben, nézzük meg részletesen az első példát, hogy lássa a részt vevő összetevőket és a működésüket.

### <a name="search-box"></a>Keresőmező

A keresőmező minden nyelv esetén pontosan ugyanolyan.  Nyissa meg a \Views\Home mappában lévő Index.cshtml fájlt. Maga a keresőmező egyszerű:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Ez egy egyszerű beviteli szövegmező egy stilizálási osztállyal, egy azonosítóval, amelyre a JavaScript hivatkozik, és egy helyőrző szöveggel.  A lényeg a JavaScriptben van.

### <a name="javascript-code-c"></a>JavaScript-kód (C#)

A C# nyelven írt minta JavaScriptet használ az Index.cshtml fájlban, hogy kiaknázza a jQuery felhasználói felületi automatikus kitöltési kódtár előnyeit.  Ez a kódtár adja a keresőmező automatikus kitöltési felületét. Ehhez aszinkron hívásokat végez az MVC-vezérlőhöz a javaslatok lekéréséhez.  Lássuk az első példa JavaScript-kódját:

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

Ez a kód a böngészőben fut az oldal betöltésekor az „example1a” beviteli mező automatikus kitöltése konfigurálásának érdekében.  A `minLength: 3` biztosítja, hogy a javaslatok csak akkor legyenek láthatóak, amikor legalább három karakter van a keresőmezőben.  A forrásérték nagyon fontos:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Ez a sor közli az automatikus kitöltési API-val, hogy hol találja a keresőmezőben megjelenítendő elemek listáját.  Mivel ez egy MVC-projekt, a Suggest függvényt hívja meg a HomeController.cs fájlban.  A következő szakaszban ezt részletesebben tárgyaljuk.  Néhány paramétert is továbbít a kiemelések, az intelligens egyeztetések és a kifejezés vezérléséhez.  Az automatikus kitöltés JavaScript API hozzáadja a kifejezés paraméterét.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>A minta kibővítése az intelligens egyeztetés támogatása érdekében

Az intelligens kereséssel akkor is lekérheti az eredményeket közeli találatok alapján, ha a felhasználó elírt egy szót a keresőmezőben.  Próbáljuk ki ezt úgy, hogy a forrássorban engedélyezzük az intelligens egyeztetést.

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

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

Most, hogy megtekintettük a minta JavaScript-kódját, nézzük azt a C#-vezérlőkódot, amely az Azure Search .NET SDK-val kéri le a javaslatokat.

1. Nyissa meg a Controllers könyvtárban lévő HomeController.cs fájlt. 

1. Először az osztály tetején lévő, InitSearch nevű metódust veheti észre.  Ez hitelesített HTTP-indexügyfelet hoz létre az Azure Search szolgáltatáshoz.  Ha szeretne további információt ennek működéséről, tekintse meg a következő oktatóanyaggal: [Az Azure Search szolgáltatás használata .NET-alkalmazásból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. Lépjen a Suggest függvényre.

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

A Suggest függvény két paramétert vesz fel, amelyek meghatározzák, hogy a rendszer a találatok kiemeléseit adja vissza, vagy intelligens egyeztetést használ a keresési kifejezés bevitele mellett.  A metódus egy SuggestParameters objektumot hoz létre, amelyet azután a javaslati API-nak továbbít. A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.
(Választható) Adjon hozzá egy töréspontot a Suggest függvény elejéhez, és lépegessen végig a kódon.  Figyelje meg az SDK által visszaadott választ, és hogy a rendszer hogyan alakítja át a metódusból visszaadott eredménnyé.

Az oldalon lévő egyéb példák ugyanezt a mintát követik a találatok kiemeléséhez, az automatikus kitöltés javaslatainak előre begépelt szövegéhez és az aspektusokhoz, amelyek az automatikus kitöltési eredmények ügyféloldali gyorsítótárazását támogatják.  Nézze át ezeket, hogy megértse, hogyan működnek, és hogyan használhatók a keresés során.

### <a name="javascript-language-example"></a>JavaScript nyelvű példa

A JavaScript nyelvű példához az IndexJavaScript.cshtml oldalon lévő JavaScript-kód a jQuery felhasználói felület automatikus kitöltési funkcióját használja.  Ez a kódtár elvégzi a munka nagy részét a szép keresőmező megjelenítéséhez, és megkönnyíti az aszinkron hívások Azure Searchhöz való intézését a javaslatok lekérése érdekében.  Lássuk az első példa JavaScript-kódját:

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

Ha összehasonlítja ezt a kezdőlapvezérlőt meghívó fenti példával, több hasonlóságot láthat.  A `minLength` és a `position` automatikus kitöltési konfigurációja pontosan ugyanolyan.  Itt a forrás a jelentős különbség.  A kezdőlapvezérlőben a Suggest metódus meghívása helyett egy REST-kérés jön létre egy JavaScript-függvényben, és a végrehajtása az ajax használatával történik.  A választ a rendszer ezután „sikeresen” feldolgozza, és a forrásként használja.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag az automatikus kitöltést és a javaslatokat támogató keresőmező felépítésének alapvető lépéseit ismerteti.  Látta, hogyan készíthet ASP.NET MVC-alkalmazást, és hogyan használhatja az Azure Search .Net SDK-t vagy a REST API-t a javaslatok lekéréséhez.

## <a name="next-steps"></a>További lépések

A javaslatok és az automatikus kitöltés integrálása a keresési felületre.  Annak megfontolása, hogy a .Net SDK vagy a REST API közvetlen használata hogyan segíthet abban, hogy gépelés közben biztosítsa az Azure Search funkcióit a felhasználóknak a hatékonyabb munka érdekében.

> [!div class="nextstepaction"]
> [Automatikus kitöltés REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Aspektusok indexattribútum Index létrehozása REST API-n](https://docs.microsoft.com/rest/api/searchservice/create-index)

