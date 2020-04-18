---
title: Automatikus kiegészítés és javaslatok hozzáadása a keresőmezőben
titleSuffix: Azure Cognitive Search
description: Engedélyezze a felhasználóként történő lekérdezési műveleteket az Azure Cognitive Search szolgáltatásban azáltal, hogy javaslatokat tesz, és olyan kéréseket készít, amelyek automatikusan kiegészítik a keresőmezőt kész kifejezésekkel vagy kifejezésekkel. A javasolt egyezéseket is visszaadhat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 1d8085c6056cb0d2541999c3e9c249cde3da8834
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641254"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Automatikus kiegészítés és javaslatok hozzáadása az ügyfélalkalmazásokhoz

A felhasználó által kezdeményezett lekérdezések hatékonyságának javítására szolgáló közös módszer a felhasználó által kezdeményezett lekérdezések hatékonyságának javítására. Az Azure Cognitive Search szolgáltatásban ezt a felületet az *automatikus kiegészítés*támogatja, amely részleges bemeneten alapuló kifejezést vagy kifejezést fejez be (a "mikro" befejezése a "microsoft"). Egy másik űrlap a *javaslatok:* az egyező dokumentumok rövid listája (azonosítóval rendelkező könyvcímek visszaadása, hogy egy részletoldalra mutató hivatkozást csatolhassunk). Mind az automatikus kiegészítés, mind a javaslatok az indexben lévő egyezésen alapulnak. A szolgáltatás nem kínál olyan lekérdezéseket, amelyek nulla eredményt adnak vissza.

Ahhoz, hogy ezeket a tapasztalatokat megvalósítsa az Azure Cognitive Search szolgáltatásban, a következőkre lesz szüksége:

+ Egy *szuggesztő* a hátsó oldalon.
+ Automatikus *query* kiegészítést vagy javaslatok API-t a kérelemben megadva.
+ A *felhasználói felület vezérlője* a keresés önkénti keresésének kezelésére az ügyfélalkalmazásban. Ehhez azt javasoljuk, hogy egy meglévő JavaScript-kódtárat használjon.

Az Azure Cognitive Search, automatikusan kitöltött lekérdezések és a javasolt eredmények lekérése a keresési index, a kiválasztott mezők, amelyek regisztrált a javaslatajánló. A javaslatajánló az index része, és meghatározza, hogy mely mezők biztosítják a lekérdezést letöltő, eredményt sugalló vagy mindkettőt tartalmazó tartalmat. Az index létrehozásakor és betöltésekor egy javaslatajánló adatstruktúra jön létre belsőleg a részleges lekérdezések egyeztetéséhez használt előtagok tárolására. A javaslatok, kiválasztása megfelelő területeken, amelyek egyedi, vagy legalábbis nem ismétlődő, elengedhetetlen, hogy a tapasztalat. További információt a [Javaslatajánló létrehozása](index-add-suggesters.md)című témakörben talál.

A cikk további része a lekérdezésekre és az ügyfélkódra összpontosít. JavaScript et és C# -ot használ a főbb pontok szemléltetésére. REST API-példák segítségével az egyes műveletek tömören bemutatják. A végpontok közötti kódmintákra mutató hivatkozásokat a [Következő lépések (További lépések) (További lépések) témakörben tésszel kapcsolatban.](#next-steps)

## <a name="set-up-a-request"></a>Kérelem beállítása

A kérelem elemei közé tartozik az API[(automatikus kiegészítés REST](https://docs.microsoft.com/rest/api/searchservice/autocomplete) vagy [javaslat REST),](https://docs.microsoft.com/rest/api/searchservice/suggestions)egy részleges lekérdezés, és egy javaslatajánló.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

A **suggesterName** a kifejezések vagy javaslatok teljesítéséhez használt javaslatírói mezőket adja meg. Különösen a javaslatok esetében a mezőlistának azokból kell állnia, amelyek egyértelmű választási lehetőségeket kínálnak az egyező eredmények között. A számítógépes játékokat értékesítő webhelyeken a mező lehet a játék címe.

A **keresési** paraméter biztosítja a részleges lekérdezést, ahol a karakterek etetik a lekérdezési kérelmet a jQuery automatikus kiegészítés vezérlőn keresztül. A fenti példában a "minecraf" egy statikus illusztrációja annak, hogy a vezérlő mit adhatott be.

Az API-k nem írnak elő minimális hosszkövetelményeket a részleges lekérdezésre; lehet olyan kicsi, mint egy karakter. A jQuery automatikus kiegészítés azonban minimális hosszt biztosít. Legalább két vagy három karakter jellemző.

Az egyezések a kifejezés elején vannak bárhol a bemeneti karakterláncban. Mivel a "gyors barna róka", mind az automatikus kiegészítés és javaslatok egyezik a részleges változatai "a", "gyors", "barna", vagy "róka", de nem a részleges infix kifejezések, mint a "rown" vagy "ökör". Továbbá minden egyezés meghatározza a downstream bővítések hatókörét. A részleges lekérdezés "gyors br" egyezik a "gyors barna" vagy "gyors kenyér", de sem a "barna" vagy a "kenyér" önmagukban lenne egyezik, kivéve, ha "gyors" megelőzi őket.

### <a name="apis"></a>API-k

Kövesse az alábbi hivatkozásokat a REST és a .NET SDK referencialapokhoz:

+ [Javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Rest automatikus kiegészítésapi](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Automatikus kiegészítésHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Válasz strukturálása

Az automatikus kiegészítésre és a javaslatokra adott válaszok az, amire számíthat a mintában: Az [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) a kifejezések listáját adja vissza, a Javaslatok feltételeket és egy dokumentumazonosítót, hogy lehívhassa a dokumentumot (a [Lookup Document](https://docs.microsoft.com/rest/api/searchservice/lookup-document) API segítségével lekéri az adott dokumentumot egy részletlaphoz). [Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions#response)

A válaszokat a kérés paraméterei alakítják. Az automatikus kiegészítés mezőben állítsa be az [**automatikus kiegészítésmód**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) beállítását annak meghatározására, hogy a szöveg kiegészítése egy vagy két feltétel szerint történjen-e. A Javaslatok mezőben a kiválasztott mező határozza meg a válasz tartalmát.

A válasz további finomítása érdekében adjon meg további paramétereket a kéréshez. A következő paraméterek az automatikus kiegészítésre és a javaslatokra egyaránt vonatkoznak.

| Paraméter | Használat |
|-----------|-------|
| **$select** | Ha több **forrásmezővel**rendelkezik, **$select** segítségével választhatja ki, hogy melyik mező járul hozzá az értékekhez (`$select=GameTitle`). |
| **$filter** | Egyezési feltételek alkalmazása`$filter=ActionAdventure`az eredményhalmazra ( ). |
| **$top** | Az eredményeket egy adott`$top=5`számra ( ) korlátozza.|

## <a name="add-user-interaction-code"></a>Felhasználói beavatkozási kód hozzáadása

A lekérdezési kifejezés automatikus kitöltése vagy az egyező hivatkozások listájának ledobásához felhasználói beavatkozási kódra , általában JavaScript-re van szükség, amely felhasználhatja a külső forrásokból származó kérelmeket, például az automatikus kiegészítést vagy az Azure Search Cognitive-indexen lévő javaslatlekérdezéseket.

Bár lehet írni ezt a kódot natívan, ez sokkal könnyebb használni funkciókmeglévő JavaScript könyvtár. Ez a cikk bemutatja két, az egyik a javaslatok és a másik az automatikus kiegészítés. 

+ [Az automatikus kiegészítésvezérlő (jQuery UI)](https://jqueryui.com/autocomplete/) a Javaslat példában használatos. Létrehozhat egy keresőmezőt, majd hivatkozhat rá egy JavaScript-függvényben, amely az Automatikus kiegészítés widgetet használja. A vezérlő tulajdonságai beállítják a forrást (automatikus kiegészítés vagy javaslatfunkció), a beviteli karakterek minimális hosszát a művelet megkezdése előtt, valamint a pozicionálást.

+ [XDSoft automatikus kiegészítés plug-in](https://xdsoft.net/jqplugins/autocomplete/) használják az automatikus kiegészítés példa.

Ezeket a könyvtárakat arra használjuk, hogy a javaslatokat és az automatikus kiegészítést támogató keresőmezőt hozzuk létre. A keresőmezőben gyűjtött bemenetek javaslatokkal és automatikus kiegészítési műveletekkel vannak párosítva.

## <a name="suggestions"></a>Javaslatok

Ez a szakasz végigvezeti a javasolt találatok megvalósításán, kezdve a keresőmező definíciójával. Azt is bemutatja, hogyan és script, amely meghívja az első JavaScript automatikus kiegészítés könyvtár hivatkozott ebben a cikkben.

### <a name="create-a-search-box"></a>Keresőmező létrehozása

Feltételezve, hogy a [jQuery ui automatikus kiegészítéskönyvtára](https://jqueryui.com/autocomplete/) és egy MVC-projekt C#-ban van, az **Index.cshtml** fájlban javascript használatával definiálhatja a keresőmezőt. A tár hozzáadja a keresés használata közbenső kapcsolati tevékenységet a keresőmezőhöz azáltal, hogy aszinkron hívásokat kezdeményez az MVC-vezérlőhöz a javaslatok lekéréséhez.

Az **Index.cshtml** fájlban a \Views\Home mappában a keresőmező létrehozásához a következő lehet:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Ez a példa egy egyszerű beviteli szövegdoboz, amely egy stílusosztállyal, a JavaScript által hivatkozott azonosítóval és helyőrző szöveggel rendelkezik.  

Ugyanabban a fájlban ágyazzon be a keresőmezőre hivatkozó JavaScriptet. A következő függvény meghívja a Javaslat API-t, amely részleges bevitelalapján javasolja a javasolt egyezési dokumentumokat:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

A `source` megmondja a jQuery UI automatikus kiegészítés funkció, ahol, hogy a lista elemek jelennek meg a keresőmező alatt. Mivel ez a projekt egy MVC-projekt, meghívja a **javaslat** **függvényt HomeController.cs,** amely a lekérdezési javaslatok visszaadásának logikáját tartalmazza. Ez a függvény néhány paramétert is átad a kiemelések, az intelligens megfeleltetés és a kifejezés vezérléséhez. Az automatikus kiegészítés JavaScript API hozzáadja a kifejezés paraméter.

A `minLength: 3` biztosítja, hogy a javaslatok csak akkor jelenjenek meg, ha legalább három karakter van a keresőmezőben.

### <a name="enable-fuzzy-matching"></a>Intelligens megfeleltetés engedélyezése

Az intelligens kereséssel akkor is lekérheti az eredményeket közeli találatok alapján, ha a felhasználó elírt egy szót a keresőmezőben. A szerkesztési távolság 1, ami azt jelenti, hogy a felhasználói bemenet és az egyezés között egy karakter maximális eltérése lehet. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Kiemelés engedélyezése

A kiemelés betűstílust alkalmaz a bemenetnek megfelelő eredmény karaktereire. Ha például a részleges bemenet "mikro", az eredmény **mikrolágy,** **mikrohatókörként**és így tovább jelenik meg. A kiemelés a HighlightPreTag és a HighlightPostTag paramétereken alapul, amelyek a Javaslat függvénnyel összhangban vannak definiálva.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Javaslat funkció

Ha C# és Egy MVC-alkalmazást használ, **HomeController.cs** a Vezérlők könyvtár alatti fájl, ahol létrehozhat egy osztályt a javasolt eredményekhez. A .NET-ben a Javaslat függvény a [DocumentsOperationsExtensions.Suggest metóduson](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)alapul.

A `InitSearch` metódus létrehoz egy hitelesített HTTP index ügyfél az Azure Cognitive Search szolgáltatáshoz. A .NET SDK-ról további információt az [Azure Cognitive Search használata .NET alkalmazásból című témakörben talál.](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

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

## <a name="autocomplete"></a>Automatikus kiegészítés

Eddig a keresési UX-kód a javaslatokra összpontosult. A következő kódblokk automatikus kiegészítést jelenít meg az XDSoft jQuery ui automatikus kiegészítési funkciójával, és az Azure Cognitive Search automatikus kiegészítésre vonatkozó kérést továbbítja. Csakúgy, mint a javaslatokat, a C # alkalmazás, kódot, amely támogatja a felhasználói beavatkozás megy **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
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
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Automatikus kiegészítés i

Az automatikus kiegészítés a [DocumentsOperationsExtensions.Autocomplete metóduson](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)alapul. A javaslatokhoz ugyanúgy, mint a kódblokk, a **HomeController.cs** fájlba kerülne.

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

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Az Automatikus kiegészítés függvény a keresési kifejezés bevitelét veszi át. A metódus létrehoz egy [Automatikus KiegészítésParaméter objektumot.](https://docs.microsoft.com/rest/api/searchservice/autocomplete) A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

## <a name="next-steps"></a>További lépések

Kövesse ezeket a hivatkozásokat a végpontok közötti utasításokért vagy a keresési-as-you-type élményt bemutató kódért. Mindkét kódpélda a javaslatok hibrid implementációi és az automatikus kiegészítés együttesen.

+ [Bemutató: Az első alkalmazás létrehozása C# nyelven (3. lecke)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C# kódminta: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# és JavaScript REST egymás melletti kódmintával](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)