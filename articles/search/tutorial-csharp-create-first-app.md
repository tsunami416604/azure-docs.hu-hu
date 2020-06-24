---
title: C#-oktatóanyag az első alkalmazás létrehozásához
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan hozhatja létre az első C# Search-alkalmazást lépésről lépésre. Az oktatóanyagban egy, a GitHubon működő alkalmazásra mutató hivatkozás, valamint az alkalmazás teljesen új felépítésének teljes folyamata is elérhető. Az Azure Cognitive Search alapvető összetevőinek megismerése.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 4c96dfa078ec12e0681574629276c48af2c1760d
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261460"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Oktatóanyag: az első keresőalkalmazás létrehozása a .NET SDK használatával

Megtudhatja, hogyan hozhat létre webes felületet az indexek keresési eredményeinek lekérdezéséhez és megjelenítéséhez az Azure Cognitive Search használatával. Ez az oktatóanyag egy meglévő, üzemeltetett indextel kezdődik, így a keresési oldal kiépítésére koncentrálhat. Az index fiktív szállodai adathalmazt tartalmaz. Ha már rendelkezik alapszintű oldallal, a következő leckében javíthatja a lapozást, az aspektusokat és a fajta élményt.

Az oktatóanyag a következőket ismerteti:
> [!div class="checklist"]
> * Fejlesztési környezet beállítása
> * Adatstruktúrák modellezése
> * Weblap létrehozása
> * Metódusok definiálása
> * Az alkalmazás tesztelése

Azt is megtudhatja, hogy milyen egyszerű keresési hívást végez. A kifejleszteni kívánt kódban szereplő kulcsfontosságú utasítások a következő néhány sorban vannak beágyazva.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Ez az egy hívás az Azure-adatok keresését kezdeményezi, és visszaadja az eredményeket.

![A "pool" kifejezés keresése](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

[Telepítse a Visual studiót](https://visualstudio.microsoft.com/) ide-ként való használatra.

### <a name="install-and-run-the-project-from-github"></a>A projekt telepítése és futtatása a GitHubról

1. Keresse meg a mintát a GitHubon: [első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Válassza a **klón vagy a letöltés** lehetőséget, és hozza el a projekt privát helyi példányát.
1. A Visual Studióban navigáljon a alkalmazáshoz, és nyissa meg a megoldást az alapszintű keresés oldalra, majd válassza a **Start hibakeresés nélkül** lehetőséget (vagy nyomja le az F5 billentyűt).
1. Írjon be néhány szót (például "WiFi", "View", "Bar", "parkoló"), és vizsgálja meg az eredményeket.

    ![A "WiFi" kifejezés keresése](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Remélhetőleg ez a projekt zökkenőmentesen fog futni, és az Azure-alkalmazás fut. Ebben az alkalmazásban sok olyan alapvető összetevő található, amely kifinomultabb kereséseket tartalmaz, ezért érdemes átmenni rajta, és újra létrehozni lépésről lépésre.

Ha a projektet a semmiből szeretné létrehozni, és így segít megerősíteni az Azure Cognitive Search összetevőit az Ön szemében, folytassa a következő lépésekkel.

## <a name="set-up-a-development-environment"></a>Fejlesztési környezet beállítása

1. A Visual Studio 2017 vagy újabb verziójában válassza az **új/projekt** , majd **ASP.net Core webalkalmazás**lehetőséget. Adjon nevet a projektnek, például "FirstAzureSearchApp".

    ![Felhőbeli projekt létrehozása](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Miután rákattintott az **OK gombra** ehhez a projekthez, a rendszer a projektre vonatkozó második beállításokat kap. Válassza a **webalkalmazás (Model-View-Controller)** lehetőséget.

    ![MVC-projekt létrehozása](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Ezután a Tools ( **eszközök** ) menüben válassza a **NuGet csomagkezelő** elemet, majd az **NuGet-csomagok kezelése a megoldáshoz..**. lehetőséget. A telepítéshez egy csomagra van szükség. Válassza a **Tallózás** fület, majd írja be az "Azure Cognitive Search" kifejezést a keresőmezőbe. Telepítse a **Microsoft. Azure. Search** programot, ha az megjelenik a listában (9.0.1 vagy újabb verzió). A telepítés befejezéséhez kattintson néhány további párbeszédpanelre.

    ![Azure-kódtárak hozzáadása a NuGet használatával](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Azure-Cognitive Search inicializálása

Ebben a példában nyilvánosan elérhető szállodai adatszolgáltatásokat használunk. Ezek az adat a 50 kitalált nevek és leírások tetszőleges gyűjteménye, amely kizárólag a bemutató adatának biztosítása céljából hozható létre. Az adateléréshez meg kell adnia egy nevet és egy kulcsot.

1. Nyissa meg a appsettings.jst az új projekt fájljában, és cserélje le az alapértelmezett sorokat a következő névre és kulcsra. Az itt megjelenő API-kulcs nem egy példa a kulcsra, hanem _pontosan_ a szükséges kulcsot kell elérnie. A appsettings.jsfájlnak most így kell kinéznie.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Ezt a fájlt még nem tesszük elérhetővé, válassza ki a fájl tulajdonságait, és módosítsa a **Másolás a kimeneti könyvtárba** beállítást, **Ha újabb**értékre kíván másolni.

    ![Az alkalmazás beállításainak másolása a kimenetre](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Adatstruktúrák modellezése

A modellek (C# osztályok) az ügyfél (a nézet), a kiszolgáló (a vezérlő) és az Azure-felhő közötti adatkommunikációra szolgálnak az MVC (modell, nézet, vezérlő) architektúra használatával. Ezek a modellek általában tükrözik az éppen elért adat szerkezetét. Emellett modellre van szükségünk a nézet/vezérlő kommunikáció kezeléséhez.

1. Nyissa meg a projekt **models (modellek** ) mappáját megoldáskezelő használatával, és itt egy alapértelmezett modell jelenik meg: **ErrorViewModel.cs**.

2. Kattintson a jobb gombbal a **modellek** mappára, és válassza a **Hozzáadás** , majd az **új elem elemet**. Ezután a megjelenő párbeszédpanelen válassza a **ASP.net Core** , majd az első lehetőség **osztály**elemet. Nevezze át a. cs fájlt a Hotel.cs, majd kattintson a **Hozzáadás**gombra. Cserélje le a Hotel.cs összes tartalmát a következő kódra. Figyelje meg, hogy az osztály **címe** és a **helyiség** tagja, ezek a mezők maguk az osztályok, ezért ezekre a modellekre is szükség lesz.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

3. Kövesse ugyanezt a **modellt a Address.cs** , kivéve a fájl nevét. Cserélje le a tartalmát az alábbira.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. És újra, kövesse ugyanezt a folyamatot a **Room** osztály létrehozásához, nevezze el a fájlt Room.cs. Ismét cserélje le a tartalmát az alábbira.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. A **Hotel**, a **címe**és a **szoba** osztályok készlete az Azure-ban [_összetett típusokként_](search-howto-complex-data-types.md)ismert, az Azure Cognitive Search fontos funkciója. Az összetett típusok számos szinten lehetnek osztályok és alosztályok, és lehetővé teszik a sokkal összetettebb adatstruktúrák megjelenítését, mint az _egyszerű típusok_ (csak primitív tagokat tartalmazó osztály) használatát. Még egy modellre van szükségünk, ezért ugorjon az új modell osztály létrehozásának folyamatán, de ezúttal hívja meg az osztály SearchData.cs, és cserélje le az alapértelmezett kódot az alábbira.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Ez az osztály tartalmazza a felhasználó bemenetét (**keresettszöveg**) és a keresés kimenetét (**resultList**). A kimenet típusa kritikus, **DocumentSearchResult &lt; &gt; **, mivel ez a típus pontosan megfelel a keresés eredményeinek, és át kell adni ezt a hivatkozást a nézetnek.



## <a name="create-a-web-page"></a>Weblap létrehozása

Az Ön által létrehozott projekt alapértelmezés szerint több ügyfél-nézetet hoz létre. A pontos nézetek az alapszintű .NET-verziótól függenek (ebben a példában a 2,1-es verziót használjuk). Ezek mind a projekt **views (nézetek** ) mappájában találhatók. Az index. cshtml fájlt csak a **views/Home** mappában kell módosítania.

Törölje az index. cshtml tartalmát a teljes egészében, majd hozza létre újra a fájlt a következő lépésekben.

1. A nézet két kis képet használ. Használhatja a sajátját, vagy átmásolhatja a képeket a GitHub-projektből: azure-logo.png és search.png. Ezt a két képet a **wwwroot/images** mappába kell helyezni.

2. Az index első sora. a cshtml-nek hivatkoznia kell arra a modellre, amelyet a rendszer az ügyfél (a nézet) és a kiszolgáló (a vezérlő) közötti adatkommunikációra használ, amely az általunk létrehozott **SearchData** -modell. Adja hozzá ezt a sort az index. cshtml fájlhoz.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Általános gyakorlat a nézet címének megadásához, így a következő soroknak kell lenniük:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. A címet követve adjon meg egy HTML-stíluslapra mutató hivatkozást, amelyet hamarosan létre fogunk hozni.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Most a nézet húsát. Fontos megjegyezni, hogy a nézetnek két szituációt kell kezelnie. Először is az alkalmazás első elindításakor kell kezelnie a megjelenítést, és a felhasználó még nem adott meg keresési szöveget. Másodszor, a keresés szövegmezőn kívül az eredmények megjelenítését is kezelni kell a felhasználó általi ismételt használat érdekében. Ennek a két helyzetnek a kezeléséhez meg kell vizsgálni, hogy a nézethez megadott modell null értékű-e. A Null modell azt jelzi, hogy a két helyzetben (az alkalmazás kezdeti futtatásakor) az első. Adja hozzá a következőt az index. cshtml fájlhoz, és olvassa végig a megjegyzéseket.

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Végül hozzáadjuk a stíluslapot. A Visual Studióban a **fájl** menüben válassza az **új/fájl** , majd a **stíluslapot** ( **általános** kiemelve). Cserélje le az alapértelmezett kódot az alábbira. Ezt a fájlt nem fogjuk részletesebben bevezetni, a stílusok a szabványos HTML-fájlok.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. Mentse a stíluslapot Hotels. css néven a WWWroot/CSS mappába az alapértelmezett site. css fájl mellett.

Ezzel befejezte a nézetet. Jó előrehaladást biztosítunk. A modellek és nézetek készen állnak, csak a vezérlő marad, hogy egyesítse az összeset.

## <a name="define-methods"></a>Metódusok definiálása

Az alapértelmezés szerint létrehozott egyik vezérlő (**otthoni vezérlő**) tartalmára kell módosítani.

1. Nyissa meg a HomeController.cs fájlt, és cserélje le a **using** utasításokat a következőre.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Index metódusok hozzáadása

Két **indexelési** módszerre van szükségünk, amelyek közül az egyik nem jár paraméterekkel (az alkalmazás első megnyitásakor), és az egyik modell paraméterként (ha a felhasználó beírt egy keresési szöveget). Alapértelmezés szerint az első ilyen metódust hozza létre. 

1. Adja hozzá a következő metódust az alapértelmezett **index ()** metódus után.

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Figyelje meg a metódus **aszinkron** deklarációját, és **várja** meg a **RunQueryAsync**. Ezek a kulcsszavak gondoskodnak arról, hogy a hívások aszinkron módon legyenek letiltva, ezért ne blokkolja a szálakat a kiszolgálón.

    A **Catch** blokk az alapértelmezés szerint az általunk létrehozott hibakódot használja.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Figyelje meg, hogy a hibakezelés és egyéb alapértelmezett nézetek és metódusok

Attól függően, hogy a .NET Core melyik verzióját használja, a rendszer alapértelmezés szerint az alapértelmezett nézetek némileg eltérő készletét hozza létre. A .NET Core 2,1 esetében az alapértelmezett nézetek az index, a névjegy, a kapcsolatfelvétel, az adatvédelem és a hiba. A .NET Core 2,2 esetében például az alapértelmezett nézetek az index, az adatvédelem és a hiba. Mindkét esetben megtekintheti ezeket az alapértelmezett lapokat az alkalmazás futtatásakor, és megvizsgálhatja, hogyan történjen a kezelés a vezérlőben.

Ebben az oktatóanyagban később a hiba nézetet fogjuk tesztelni.

A GitHub-mintában törölte a nem használt nézeteket, valamint a hozzájuk tartozó műveleteket.

### <a name="add-the-runqueryasync-method"></a>A RunQueryAsync metódus hozzáadása

Az Azure Cognitive Search hívást a **RunQueryAsync** metódusban ágyazjuk be.

1. Először adjon hozzá néhány statikus változót az Azure-szolgáltatás beállításához, valamint egy hívást a kezdeményezéshez.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Most adja hozzá a **RunQueryAsync** metódust.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    Ez a módszer először gondoskodik az Azure-konfiguráció kezdeményezéséről, majd beállítja a keresési paramétereket. A **Select** paraméter mezőinek nevei pontosan egyeznek a **Hotel** osztályban található tulajdonságok neveivel. A **Select** paraméter kihagyható, amely esetben a rendszer az összes tulajdonságot visszaadja. A **Select** paraméterek beállítása nem hatékony, ha csak az adatok egy részhalmazát érdeklik. A fontos tulajdonságok megadásával csak ezeket a tulajdonságokat adja vissza.

    A keresendő aszinkron hívás (**Model. resultList = vár _indexClient.Documents. A SearchAsync &lt; Hotel &gt; (Model. keresettszöveg, Parameters);**) az oktatóanyag és az alkalmazás lényege. A **DocumentSearchResult** osztály egy érdekes, és jó ötlet (ha az alkalmazás fut) itt egy töréspontot kell beállítania, és egy hibakereső használatával meg kell vizsgálnia a **Model. resultList**tartalmát. Érdemes megkeresni, hogy a rendszer intuitív módon adja meg a kért adatmennyiséget, és nem sok más.

Most az igazság pillanatában.

### <a name="test-the-app"></a>Az alkalmazás tesztelése

Most ellenőrizzük, hogy az alkalmazás megfelelően fut-e.

1. Válassza a **hibakeresés/indítás hibakeresés nélkül** lehetőséget, vagy nyomja le az F5 billentyűt. Ha helyesen kódolta a megfelelő dolgokat, a kezdeti index nézet jelenik meg.

     ![Az alkalmazás megnyitása](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Írjon be egy szöveget (például "Beach") (vagy bármilyen szöveget, amely szóba jöhet), majd kattintson a keresés ikonra. Némi eredményt kell kapnia.

     ![A "Beach" kifejezés keresése](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Próbálja meg beírni a "Five Star" kifejezést. Vegye figyelembe, hogy az eredmények nem jelennek meg. A kifinomultabb keresés a "Five Star" kifejezést fogja kezelni a "luxus" szinonimája, és visszaküldi az eredményeket. A Szinonimák használata az Azure Cognitive Searchban érhető el, de az első oktatóanyagokban nem jelennek meg.
 
4. Próbálja meg beírni a "gyors" kifejezést a keresett szövegként. _Nem_ ad vissza bejegyzéseket a (z) "Hotel" szónak. A keresés csak a teljes szavakat keresi, bár a rendszer néhány eredményt ad vissza.

5. Próbálja ki a következő szavakat: "pool", "Sunshine", "View", és bármit. Az Azure Cognitive Search a legegyszerűbb, de még mindig meggyőző szinten fog megjelenni.

## <a name="test-edge-conditions-and-errors"></a>Edge-feltételek és hibák tesztelése

Fontos ellenőrizni, hogy a hibakezelés funkciói működnek-e, még akkor is, ha a dolgok tökéletesen működnek. 

1. Az **index** metódusban, a **Try {** hívás után írja be a line **Throw New Exception ()** sort. Ez a kivétel hibát jelez, amikor szöveget keresünk.

2. Futtassa az alkalmazást, írja be a "sáv" kifejezést keresési szövegként, majd kattintson a keresés ikonra. A kivételnek a hiba nézetet kell eredményeznie.

     ![Hiba kényszerítése](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Biztonsági kockázatnak számít a belső hibák számának visszaküldése a hibás lapokon. Ha az alkalmazást általános használatra szánják, végezzen vizsgálatot a biztonságos és ajánlott eljárások között, amelyekkel a hiba bekövetkezésekor vissza lehet térni.

3. Távolítsa el az **új kivételt ()** , ha meggyőződött arról, hogy a hibakezelés működik.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* Az Azure Cognitive Search hívása tömör, és könnyen értelmezhető az eredmények.
* Az aszinkron hívások kis bonyolultságot adhatnak a vezérlőhöz, de az ajánlott eljárás, ha minőségi alkalmazásokat szeretne fejleszteni.
* Az alkalmazás egyszerű szöveges keresést hajtott végre, amelyet a **searchParameters**-ben beállított beállítások határoznak meg. Ez az osztály azonban számos olyan taggal feltölthető, amely kifinomultságot ad a kereséshez. Nem sok további munka szükséges ahhoz, hogy ez az alkalmazás lényegesen nagyobb teljesítményű legyen.

## <a name="next-steps"></a>További lépések

Az Azure Cognitive Search használatának legjobb felhasználói élménye érdekében további funkciókat kell hozzáadnia, például a lapozást (oldalszámok vagy végtelen görgetés használatával), valamint az automatikus kiegészítés/javaslatok lehetőséget. A kifinomultabb keresési paramétereket is érdemes megfontolni (például egy adott pont egy adott sugarán belüli földrajzi keresések és a keresési eredmények rendezése).

A következő lépések számos oktatóanyagban szerepelnek. Kezdjük a lapozással.

> [!div class="nextstepaction"]
> [C# oktatóanyag: keresési eredmények tördelése – Azure Cognitive Search](tutorial-csharp-paging.md)


