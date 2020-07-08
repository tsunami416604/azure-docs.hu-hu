---
title: Automatikus kiegészítés és javaslatok hozzáadása egy keresési mezőben
titleSuffix: Azure Cognitive Search
description: Az Azure-Cognitive Search keresési típusú lekérdezési műveleteinek engedélyezése a javaslatok létrehozásával és a befejezett kifejezésekkel vagy kifejezésekkel rendelkező keresőmező automatikus kiegészítésére szolgáló kérelmek összeállításával. A javasolt egyezéseket is visszaadhatja.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 004f1ea55bcda68485d8b11ed472b6cab2ca7545
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85562479"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Automatikus kiegészítés és javaslatok hozzáadása az ügyfélalkalmazások számára

A keresési típus egy gyakori módszer a felhasználó által kezdeményezett lekérdezések hatékonyságának javítására. Az Azure Cognitive Searchban ez a megoldás az *automatikus kiegészítésen*keresztül támogatott, amely egy kifejezést vagy kifejezést végez a részleges bevitel ("Micro" és "Microsoft") alapján. A *javaslatok*egy másik formája: a megfelelő dokumentumok rövid listája (a könyv címének visszaadása egy azonosítóval, amely egy részletes oldalhoz csatolható). Az automatikus kiegészítés és a javaslatok is az indexben egyeznek meg. A szolgáltatás nem kínál olyan lekérdezéseket, amelyek nulla eredményt adnak vissza.

A tapasztalatok Azure Cognitive Search-ban való megvalósításához a következőkre lesz szüksége:

+ Egy *javaslat* a háttérben.
+ Egy *lekérdezés* , amely az [automatikus kiegészítést](https://docs.microsoft.com/rest/api/searchservice/autocomplete) vagy a [javaslatok](https://docs.microsoft.com/rest/api/searchservice/suggestions) API-t határozza meg a kérelemre vonatkozóan.
+ Egy *felhasználói felületi vezérlő* , amely a keresési típusok közötti interakciókat kezeli az ügyfélalkalmazás számára. Azt javasoljuk, hogy egy meglévő JavaScript-függvénytárat használjon erre a célra.

Az Azure Cognitive Searchban az automatikusan befejezett lekérdezéseket és a javasolt eredményeket a rendszer a keresési indexből kérdezi le, a kijelölt mezőkből, amelyeket egy javaslatban regisztráltak. A javaslat az index részét képezi, és meghatározza, hogy mely mezők biztosítanak a lekérdezés befejezését, vagy a két művelet eredményét. Az index létrehozásakor és betöltésekor a rendszer belsőleg létrehoz egy szuggesztív adatstruktúrát a részleges lekérdezésekhez való megfeleltetéshez használt előtagok tárolásához. A javaslatok, amelyek egyedi, vagy legalábbis ismétlődő, megfelelő mezők kiválasztásával elengedhetetlenek a felhasználói élményhez. További információ: [javaslat létrehozása](index-add-suggesters.md).

A cikk további része a lekérdezésekre és az ügyfél kódjára összpontosít. A JavaScript és a C# használatával mutatja be a legfontosabb pontokat. REST API példákat használunk az egyes műveletek tömör bemutatása érdekében. A végpontok közötti kód mintákra mutató hivatkozásokat a [következő lépések](#next-steps)című szakaszban tekintheti meg.

## <a name="set-up-a-request"></a>Kérelem beállítása

A kérelem elemei közé tartozik az egyik keresési típusú API, egy részleges lekérdezés és egy javaslat. A következő parancsfájl egy kérelem összetevőit mutatja be, az automatikus kiegészítési REST API használatával példaként.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

A **suggesterName** megadja a feltételek vagy javaslatok végrehajtásához használt, javasolt mezőket. A konkrét javaslatok esetében a mezőlista olyan elemekből áll, amelyek a megfelelő eredmények között egyértelmű döntéseket nyújtanak. A számítógépes játékokat értékesítő webhelyeken a játék címe lehet.

A **keresési** paraméter biztosítja a részleges lekérdezést, ahol a karakterek a lekérdezési kérelemhez a jQuery automatikus kiegészítés-vezérlőn keresztül vannak betáplálva. A fenti példában a "minecraf" egy statikus ábrája annak, amit a vezérlő átadott.

Az API-k nem írnak elő minimális hosszúságú követelményeket a részleges lekérdezéshez; akár egy karakter is lehet. A jQuery automatikus kiegészítés azonban minimális hosszt biztosít. A minimum kettő vagy három karakter jellemző.

A egyezések egy kifejezés elején vannak, bárhol a bemeneti karakterláncban. A "The Quick Brown Fox", az automatikus kiegészítés és a javaslatok a "The", a "Quick", a "Brown" vagy a "Fox" részleges verzióihoz tartoznak, de nem a részleges Infix, például a "sorvégi" vagy az "Ox" kifejezéssel. Emellett az egyes egyezések az alsóbb rétegbeli bővítések hatókörét határozzák meg. A "Quick br" részleges lekérdezése megfelel a "Quick Brown" vagy a "Quick kenyér" kifejezésnek, de a "barna" vagy a "kenyér" nem egyezik meg egymással, hacsak a "gyors" megelőzi őket.

### <a name="apis-for-search-as-you-type"></a>A kereséshez használt API-k

Kövesse az alábbi hivatkozásokat a REST és a .NET SDK-referenciák oldalaihoz:

+ [Javaslatok REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Automatikus kiegészítés REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync metódus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Válasz strukturálása

Az automatikus kiegészítésre és a javaslatokra adott válaszok a következő mintában számíthatnak: az [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) a feltételek listáját adja vissza, a [javaslatok](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) pedig egy dokumentum azonosítóját, így a dokumentum beolvasása érdekében (a [keresési dokumentum](https://docs.microsoft.com/rest/api/searchservice/lookup-document) API-val lekérheti az adott dokumentumot egy részletes oldalhoz).

A válaszokat a kérés paraméterei alakítják ki. Az automatikus kiegészítés beállításnál állítsa be a [**autocompleteMode**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) annak megállapítására, hogy a szöveg befejezése egy vagy két kifejezésen történik-e. Javaslatok esetén a kiválasztott mező határozza meg a válasz tartalmát.

A javaslatok esetében tovább pontosíthatja a választ, hogy elkerülje a duplikált elemeket, vagy hogy mi úgy tűnik, hogy a nem kapcsolódó eredmények. Az eredmények ellenőrzéséhez adjon meg több paramétert a kéréshez. A következő paraméterek mind az automatikus kiegészítésre, mind a javaslatokra érvényesek, de a javaslatok esetében talán több szükséges, különösen akkor, ha egy javaslat több mezőt is tartalmaz.

| Paraméter | Használat |
|-----------|-------|
| **$select** | Ha egy javaslat több **sourceFields** rendelkezik, akkor a **$Select** használatával kiválaszthatja, hogy melyik mező járul hozzá az értékekhez ( `$select=GameTitle` ). |
| **searchFields** | A lekérdezés korlátozása adott mezőkre. |
| **$filter** | Egyezési feltételek alkalmazása az eredményhalmaz () értékre `$filter=Category eq 'ActionAdventure'` . |
| **$top** | Korlátozza az eredményeket egy adott számra ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Felhasználói interakciós kód hozzáadása

Egy lekérdezési kifejezés automatikus kitöltésével vagy a megfelelő hivatkozások listájának lebontásával a felhasználói interakciós kód (jellemzően JavaScript) szükséges a külső forrásokból érkező kérések, például az automatikus kiegészítés vagy a javaslatok lekérdezése Azure Search kognitív indexen keresztül.

Bár ezt a kódot natív módon is megírhatja, sokkal egyszerűbb a függvények használata a meglévő JavaScript-kódtár használatával. Ez a cikk két, egy javaslatot és egy másikat mutat be az automatikus kiegészítéshez. 

+ Az [automatikus kiegészítés widget (JQUERY UI)](https://jqueryui.com/autocomplete/) a javaslat példájában van használatban. Létrehozhat egy keresőmezőt, majd hivatkozhat arra egy JavaScript-függvényben, amely az automatikus kiegészítés widgetet használja. A widget tulajdonságai a forrást (automatikus kiegészítés vagy javaslatok függvény), a művelet végrehajtása előtt a bemeneti karakterek minimális hosszát és a pozicionálást is beállítják.

+ A [XDSoft automatikus kiegészítési beépülő modulja](https://xdsoft.net/jqplugins/autocomplete/) az automatikus kiegészítés példáját használja.

Ezeket a kódtárakat a javaslatok és az automatikus kiegészítések támogatását támogató keresőmező létrehozásához használjuk. A keresőmezőbe gyűjtött bemenetek a javaslatokkal és az automatikus kiegészítési műveletekkel vannak párosítva.

## <a name="suggestions"></a>Javaslatok

Ez a szakasz végigvezeti a javasolt eredmények megvalósításán, kezdve a keresőmező-definícióval. Azt is bemutatja, hogyan és szkripttel hívja meg a cikkben hivatkozott első JavaScript automatikus kiegészítési függvénytárat.

### <a name="create-a-search-box"></a>Keresőmező létrehozása

Feltételezve, hogy a [jQuery felhasználói felület automatikus kiegészítésének könyvtára](https://jqueryui.com/autocomplete/) és egy MVC-projekt a C#-ban, az **index. Cshtml** fájlban a JavaScript használatával definiálhatja a keresőmezőt. A könyvtár az MVC-vezérlőhöz a javaslatok beolvasásához aszinkron módon hívja fel a keresési típus típusú interakciót a keresőmezőbe.

Az **index. cshtml** mappa \Views\Home területén a keresőmező létrehozásához a következő sor lehet:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Ez a példa egy egyszerű beviteli szövegmező, amelynek a stílusa, a JavaScript által hivatkozott azonosító, valamint a helyőrző szövege.  

Ugyanazon a fájlon belül ágyazza be a JavaScriptet, amely a keresőmezőbe hivatkozik. A következő függvény meghívja az ajánlott API-t, amely a javasolt egyezési dokumentumokat a részleges feltételek bemenetei alapján kéri le:

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

Az `source` azt jelzi, hogy a jQuery felhasználói felületének automatikus kiegészítési funkciója a keresőmező alatt megjelenítendő elemek listájának beolvasása. Mivel ez a projekt egy MVC-projekt, meghívja a **HomeController.cs** a **javasolt** függvényt, amely tartalmazza a lekérdezési javaslatok visszaadására vonatkozó logikát. Ez a függvény néhány paramétert is továbbít a csúcsfények, a zavaros egyezés és a kifejezés szabályozásához. Az automatikus kiegészítés JavaScript API hozzáadja a kifejezés paramétert.

Az `minLength: 3` biztosítja, hogy a javaslatok csak akkor jelenjenek meg, ha legalább három karakter szerepel a keresőmezőbe.

### <a name="enable-fuzzy-matching"></a>A zavaros egyezés engedélyezése

Az intelligens kereséssel akkor is lekérheti az eredményeket közeli találatok alapján, ha a felhasználó elírt egy szót a keresőmezőben. A szerkesztési távolság 1, ami azt jelenti, hogy a felhasználói bevitel és a találatok között egy karakterből álló maximális eltérés lehet. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Kiemelés engedélyezése

A kiemelés a betűméretet az eredményben szereplő karakterekre alkalmazza, amelyek a bemenetnek felelnek meg. Ha például a részleges bemenet "Micro", az eredmény a **Micro**Soft, a **Micro**scope, és így tovább jelenik meg. A kiemelés a HighlightPreTag és a HighlightPostTag paramétereken alapul, amelyek a javaslat függvénnyel vannak meghatározva.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Javasolt függvény

Ha C#-ot és MVC-alkalmazást használ, a **HomeController.cs** -fájl a vezérlők könyvtárban található, ahol létrehozhat egy osztályt a javasolt eredményekhez. A .NET-ben a javaslati függvény a [DocumentsOperationsExtensions. javaslat metóduson](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)alapul.

A `InitSearch` metódus létrehoz egy hitelesített http-index ügyfelet az Azure Cognitive Search szolgáltatáshoz. A .NET SDK-val kapcsolatos további információkért lásd: az [Azure Cognitive Search használata .NET-alkalmazásokból](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
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

## <a name="autocomplete"></a>Automatikus kiegészítés

Eddig a Search UX-kód a javaslatokra van központosítva. A következő kódrészlet az automatikus kiegészítést jeleníti meg, amely a XDSoft jQuery felhasználói felületének automatikus kiegészítési funkciója, amely az Azure Cognitive Search automatikus kiegészítésére vonatkozó kérést küld. Ahogy a javaslatok esetében is, egy C#-alkalmazásban a felhasználói interakciót támogató kód az **index. cshtml**.

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

### <a name="autocomplete-function"></a>Automatikus kiegészítési függvény

Az automatikus kiegészítés a [DocumentsOperationsExtensions. autocomplete metóduson](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)alapul. Ahogy a javaslatok esetében, ez a kódrészlet a **HomeController.cs** -fájlban is elérhető.

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

Az automatikus kiegészítési függvény a keresési kifejezés bemenetét veszi igénybe. A metódus létrehoz egy [AutoCompleteParameters objektumot](https://docs.microsoft.com/rest/api/searchservice/autocomplete). A rendszer az eredményt ezután JSON-kifejezéssé alakítja, hogy meg lehessen jeleníteni az ügyfélnek.

## <a name="next-steps"></a>További lépések

Ezeket a hivatkozásokat követve megtekintheti a keresési lehetőségekkel kapcsolatos teljes körű útmutatást és kódot. Mindkét kód például a javaslatok és az automatikus kiegészítések hibrid implementációját tartalmazza.

+ [Oktatóanyag: az első alkalmazás létrehozása C# nyelven (3. lecke)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C#-kód minta: Azure-Search-DotNet-Samples/Create-First-app/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# és JavaScript REST-alapú kóddal minta](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)