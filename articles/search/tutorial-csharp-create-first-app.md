---
title: C# oktatóanyag az első alkalmazás létrehozásához
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan hozhat létre az első C# keresőalkalmazást lépésről lépésre. Az oktatóanyag egy-egy működő alkalmazásra mutató hivatkozást biztosít a GitHubon, és a teljes folyamatot az alkalmazás nulláról történő létrehozásához. Ismerje meg az Azure Cognitive Search alapvető összetevőit.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 2b4f67fc448d98239947fd764d4926f1d590c5e2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121587"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>C# oktatóanyag: Az első alkalmazás létrehozása – Azure Cognitive Search

Ismerje meg, hogyan hozhat létre egy webes felületet a lekérdezéshez és a keresési eredmények bemutatásához egy indexből az Azure Cognitive Search használatával. Ez az oktatóanyag egy meglévő, üzemeltetett indexszel kezdődik, így a keresési oldal felépítésére összpontosíthat. Az index fiktív szállodai adatokat tartalmaz. Miután rendelkezik egy alapoldallal, a későbbi leckékben javíthatja a személyhívót, a lapozást és a típus-előre-élményét.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Fejlesztői környezet beállítása
> * Modell adatstruktúrák
> * Weblap létrehozása
> * Metódusok meghatározása
> * Az alkalmazás tesztelése

Azt is megtudhatja, hogy a keresési hívás mennyire egyszerű. A kifejleszteni kívánt kód legfontosabb kimutatásai a következő sorokba vannak beágyazva.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Ez az egy hívás elindítja az Azure-adatok keresését, és visszaadja az eredményeket.

![Keresés a "medence"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

[Telepítse](https://visualstudio.microsoft.com/) a Visual Studio alkalmazást IDE-ként való használatra.

### <a name="install-and-run-the-project-from-github"></a>A projekt telepítése és futtatása a GitHubról

1. Keresse meg a mintát a GitHubon: [Az első alkalmazás létrehozása.](https://github.com/Azure-Samples/azure-search-dotnet-samples)
1. Válassza a **Klónozás vagy letöltés** lehetőséget, és készítse el a projekt helyi magánpéldányát.
1. A Visual Studio segítségével keresse meg és nyissa meg az alapkeresési lap megoldását, és válassza a **Start hibakeresés nélkül** lehetőséget (vagy nyomja le az F5 billentyűt).
1. Írjon be néhány szót (például "wifi", "view", "bar", "parking"), és vizsgálja meg az eredményeket!

    ![Keresés a "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Remélhetőleg ez a projekt zökkenőmentesen fog futni, és az Azure-alkalmazás fut. A kifinomultabb keresések számos alapvető összetevője megtalálható ebben az alkalmazásban, ezért jó ötlet, ha végigmegy rajta, és lépésről lépésre újra létrehozza.

Ha teljesen új projektet szeretne létrehozni, és ezáltal segít megerősíteni az Azure Cognitive Search összetevőit az elméjében, hajtsa végre a következő lépéseket.

## <a name="set-up-a-development-environment"></a>Fejlesztői környezet beállítása

1. A Visual Studio 2017-ben vagy újabb verzióban válassza az **Új/Projekt** lehetőséget, majd **ASP.NET Core webalkalmazással.** Adjon a projektnek egy nevet, például "FirstAzureSearchApp".

    ![Felhőprojekt létrehozása](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Miután az **OK** gombra kattintott ehhez a projekttípushoz, kap egy második, a projektre vonatkozó beállításkészletet. Válassza a **Webapplication (Model-View-Controller) lehetőséget.**

    ![MVC-projekt létrehozása](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Ezután az **Eszközök** menüben válassza a **NuGet csomagkezelőt,** majd **a NuGet csomagok kezelése a megoldáshoz...**. Van egy csomag, amit telepítenünk kell. Válassza a **Tallózás** lapot, majd írja be az "Azure Cognitive Search" kifejezést a keresőmezőbe. Telepítse a **Microsoft.Azure.Search alkalmazást,** amikor megjelenik a listában (9.0.1-es vagy újabb verzió). A telepítés befejezéséhez néhány további párbeszédpanelre kell kattintania.

    ![Azure-könyvtárak hozzáadása a NuGet segítségével](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Az Azure Cognitive Search inicializálása

Ehhez a mintához nyilvánosan elérhető szállodai adatokat használunk. Ezek az adatok egy 50 fiktív hotelnévből és -leírásból álló tetszőleges gyűjtemény, amelyet kizárólag demo adatok szolgáltatása céljából hoztak létre. Az adatok eléréséhez meg kell adnia egy nevet és egy kulcsot.

1. Nyissa meg az appsettings.json fájlt az új projektben, és cserélje le az alapértelmezett sorokat a következő névre és kulcsra. Az itt látható API-kulcs nem egy kulcs példája, _pontosan_ ez a kulcs, amire szüksége van a szállodai adatok eléréséhez. Az appsettings.json fájlnak így kell kinéznie.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Még nem végeztünk ezzel a fájllal, válasszuk ki a fájl tulajdonságait, és változtassuk a **Másolás kimeneti könyvtárba** beállítást **másolásra, ha újabb**.

    ![Az alkalmazásbeállítások másolása a kimenetre](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modell adatstruktúrák

Modellek (C# osztályok) az ügyfél (a nézet), a kiszolgáló (a vezérlő) és az Azure-felhő közötti kommunikációra használják az MVC (modell, nézet, vezérlő) architektúra használatával. Ezek a modellek általában az elért adatok szerkezetét tükrözik. Is, szükségünk van egy modell kezelésére a nézet / vezérlő kommunikáció.

1. Nyissa meg a projekt **Modellek** mappáját a Megoldáskezelő használatával, és egy alapértelmezett modell jelenik meg: **ErrorViewModel.cs**.

2. Kattintson a jobb gombbal a **Modellek** mappára, és válassza a **Hozzáadás,** majd **az Új elem parancsot**. Ezután a megjelenő párbeszédpanelen válassza **a ASP.NET Core,** majd az első lehetőség **Osztály**. Nevezze át a .cs fájlt Hotel.cs, majd kattintson a **Hozzáadás**gombra. Cserélje le Hotel.cs teljes tartalmát a következő kódra. Figyeljük meg a **cím** és **a szoba** tagjai az osztály, ezek a mezők osztályok magukat, így szükségünk lesz modellek számukra is.

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

3. Kövesse ugyanezt a folyamatot a **címosztály** modelljének létrehozásához, kivéve a fájl nevét Address.cs. Cserélje le a tartalmát a következőkre.

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

4. És újra, kövesse ugyanazt a folyamatot, hogy megteremtse a **Szoba** osztály, elnevezése a fájl Room.cs. Ismét cserélje ki a tartalmát a következőre.

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

5. A **Hotel,** **Cím**és **Szoba** osztályok készlete az Azure-ban [_összetett típusokként_](search-howto-complex-data-types.md)ismert, az Azure Cognitive Search fontos jellemzője. Az összetett típusok több osztály- és alosztályszintű szintis lehetnek, és sokkal összetettebb adatstruktúrákat is ábrázolhatnak, mint _egyszerű típusok_ (olyan osztály, amely csak primitív tagokat tartalmaz). Szükségünk van még egy modellre, ezért menjen át újra egy új modellosztály létrehozásának folyamatán, bár ezúttal hívja meg az osztályt SearchData.cs, és cserélje le az alapértelmezett kódot a következőkre.

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

    Ez az osztály tartalmazza a felhasználó bemenetét (**searchText**) és a keresés kimenetét (**resultList**). A kimenet típusa kritikus, **&lt;DocumentSearchResult&gt;Hotel**, mivel ez a típus pontosan megegyezik a keresés eredményeit, és meg kell adni ezt a hivatkozást a nézetben.



## <a name="create-a-web-page"></a>Weblap létrehozása

A létrehozott projekt alapértelmezés szerint több ügyfélnézetet hoz létre. A pontos nézetek a Core .NET használt verziójától függenek (ebben a mintában a 2.1-et használjuk). Ezek mind a projekt **Nézetek** mappájában találhatók. Csak az Index.cshtml fájlt kell módosítania (a **Nézetek/Kezdőlap** mappában).

Törölje az Index.cshtml teljes tartalmát, és a következő lépésekben építse újra a fájlt.

1. Két kis képet használunk a nézetben. Használhatja a saját, vagy másolja át a képeket a GitHub projekt: azure-logo.png és search.png. Ezt a két képet a **wwwroot/images** mappába kell helyezni.

2. Az Index.cshtml első sorának hivatkoznia kell arra a modellre, amelyet az ügyfél (a nézet) és a kiszolgáló (a vezérlő) közötti adatközléshez fogunk használni, amely az általunk létrehozott **SearchData** modell. Adja hozzá ezt a sort az Index.cshtml fájlhoz.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Bevett gyakorlat, hogy címet ad meg a nézethez, ezért a következő sorok a következők:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. A címet követve írjon be egy HTML stíluslapra való hivatkozást, amelyet hamarosan létrehozunk.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Most a hús a kilátás. A legfontosabb dolog, hogy ne feledje, hogy a nézet kell kezelni két helyzetet. Először is, az alkalmazás első indításakor kezelnie kell a kijelzőt, és a felhasználó még nem adott meg keresési szöveget. Másodszor, a felhasználó általi ismételt használatra az eredmények megjelenítését kell kezelnie a keresőmező mellett. E két helyzet kezeléséhez ellenőriznünk kell, hogy a nézetnek biztosított modell null-e vagy sem. A null modell azt jelzi, hogy a két helyzet közül az elsőben vagyunk (az alkalmazás kezdeti futtatása). Adja hozzá az alábbiakat az Index.cshtml fájlhoz, és olvassa el a megjegyzéseket.

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

6. Végül hozzáadjuk a stíluslapot. A Visual Studio **Fájl** menüjében válassza az **Új/Fájl,** majd **a Stíluslap** lehetőséget **(kiemelve az Általános** értékkel). Cserélje le az alapértelmezett kódot a következőkre. Nem fogunk bemenni ebbe a fájlba részletesebben, a stílusok szabványos HTML.

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

7. Mentse a stíluslapfájlt hotels.css néven a wwwroot/css mappába az alapértelmezett site.css fájl mellett.

Ez kiegészíti a véleményünket. Jól haladunk. A modellek és nézetek befejeződtek, csak a vezérlő marad, hogy mindent összekössön.

## <a name="define-methods"></a>Metódusok meghatározása

Meg kell változtatni a tartalmát az egyik vezérlő (**Home Controller**), amely alapértelmezés szerint létre.

1. Nyissa meg a HomeController.cs fájlt, és cserélje le a **használatával rendelkező** állításokat a következőkre.

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

### <a name="add-index-methods"></a>Indexmetódusok hozzáadása

Szükségünk van két **Index** módszerek, az egyik nem vesz paramétereket (abban az esetben, amikor az alkalmazás először nyitott), és egy vesz egy modell, mint egy paraméter (az, ha a felhasználó belépett keresési szöveg). Ezek közül az első alapértelmezés szerint jön létre. 

1. Adja hozzá a következő módszert az alapértelmezett **Index()** metódus után.

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

    Figyelje meg a metódus **aszinkron** deklarációját, és **a** **RunQueryAsync**hívását. Ezek a kulcsszavak gondoskodnak ahívások aszinkron, és így ne blokkolja szálak a szerveren.

    A **catch** block az alapértelmezés szerint számunkra létrehozott hibamodellt használja.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Jegyezze fel a hibakezelést és más alapértelmezett nézeteket és módszereket

Attól függően, hogy a .NET Core melyik verzióját használja, alapértelmezés szerint kissé eltérő alapértelmezett nézetek jönnek létre. A .NET Core 2.1 esetében az alapértelmezett nézetek az Index, a Névjegy, a Kapcsolattartó, az Adatvédelem és a Hiba. A .NET Core 2.2 esetében például az alapértelmezett nézetek az Index, az Adatvédelem és a Hiba. Mindkét esetben megtekintheti ezeket az alapértelmezett oldalakat az alkalmazás futtatásakor, és megvizsgálhatja, hogyan kezeli őket a vezérlő.

Az oktatóanyag későbbi részében teszteljük a Hiba nézetet.

A GitHub-mintában töröltük a fel nem használt nézeteket és a hozzájuk kapcsolódó műveleteket.

### <a name="add-the-runqueryasync-method"></a>A RunQueryAsync metódus hozzáadása

Az Azure Cognitive Search hívás a **RunQueryAsync** metódusba van beágyazva.

1. Először adjon hozzá néhány statikus változót az Azure-szolgáltatás beállításához, és egy hívást azok kezdeményezéséhez.

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

2. Most adja hozzá magát a **RunQueryAsync** metódust.

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

    Ebben a módszerben először biztosítjuk az Azure-konfiguráció kezdeményezését, majd beállítunk néhány keresési paramétert. A **Select** paraméter mezőinek neve immár pontosan megegyezik a **szállodaosztály** tulajdonságneveivel. Lehetőség van kihagyni a **Select** paramétert, amely esetben az összes tulajdonságot visszaadja. Azonban a **select** paraméterek beállítása nem hatékony, ha csak az adatok egy részhalmaza érdekel minket. Az érdeklődési vagyonunk kívánt tulajdonságok megadásával csak ezeket a tulajdonságokat adja vissza.

    Az aszinkron hívás keresésre (**model.resultList = várja _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, paraméterek);**) az, amit ez a bemutató és az alkalmazás szól. A **DocumentSearchResult** osztály egy érdekes, és egy jó ötlet (ha az alkalmazás fut) az, hogy egy töréspont itt, és egy hibakereső, hogy vizsgálja meg a tartalmát **model.resultList**. Meg kell találnia, hogy intuitív, amely az Ön által kért adatokat, és nem sok más.

Most az igazság pillanatában.

### <a name="test-the-app"></a>Az alkalmazás tesztelése

Most nézzük meg, hogy az alkalmazás megfelelően fut-e.

1. Válassza **a Debug/Start Without Debugging lehetőséget,** vagy nyomja le az F5 billentyűt. Ha helyesen kódolta a dolgokat, megkapja a kezdeti Index nézetet.

     ![Az alkalmazás megnyitása](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Írja be a szöveget, például a "strand" (vagy bármely szöveg, ami eszembe jut), és kattintson a keresés ikonra. Eredményeket kellene elérnie.

     ![Keresés a "strand"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Próbáld meg beírni az "ötcsillagos" szót. Figyelje meg, hogyan nem kap eredményt. Egy kifinomultabb keresés az "ötcsillagos" kifejezést a "luxus" szinonimájaként kezelné, és visszaadná ezeket az eredményeket. A szinonimák használata elérhető az Azure Cognitive Search, bár nem lesz, amely az első oktatóanyagok.
 
4. Próbálja meg beírni a "hot" kifejezést keresési szövegként. _Nem_ tér vissza olyan bejegyzéseket, amelyekben a "hotel" szó van. Keresésünk csak a teljes szavakat találja, bár néhány eredményt visszaad.

5. Próbáljon más szavakat: "medence", "napsütés", "kilátás", és bármi. Látni fogja, hogy az Azure Cognitive Search a legegyszerűbb, de még mindig meggyőző szinten működik.

## <a name="test-edge-conditions-and-errors"></a>Vizsgálati élfeltételek és hibák

Fontos ellenőrizni, hogy hibakezelési funkcióink megfelelően működnek-e, még akkor is, ha a dolgok tökéletesen működnek. 

1. Az **Index** metódusban a **próbálkozó {** hívás után írja be a **Következő gombra való bedobás idopontot: Throw new Exception()**. Ez a kivétel hibát kényszerít ki, amikor szövegre keresünk.

2. Futtassa az alkalmazást, írja be a "sáv" kifejezést keresési szövegként, és kattintson a keresés ikonra. A kivételnek a hibanézetet kell eredményeznie.

     ![Hiba kényszerítése](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > A hibaoldalakon a belső hibaszámok visszaadása biztonsági kockázatnak minősül. Ha az alkalmazást általános használatra szánták, vizsgálja meg a hiba bekövetkeztekor visszaadandó biztonságos és ajánlott eljárásokat.

3. Távolítsa **el az Új kivétel() kidobása,** ha elégedett a hibakezelés megfelelően működik.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő elvihető ételek ebből a projektből:

* Az Azure Cognitive Search-hívás tömör, és könnyen értelmezhető az eredmények.
* Az aszinkron hívások kis mértékű összetettséget adnak a vezérlőnek, de a legjobb gyakorlat, ha minőségi alkalmazásokat kíván fejleszteni.
* Ez az alkalmazás végzett egyszerű szöveges keresés, határozza meg, mi van beállítva **searchParameters**. Ez az egy osztály azonban sok olyan taggal is feltölthető, amelyek kifinomultságot adnak a kereséshez. Nem sok további munkára van szükség ahhoz, hogy ez az alkalmazás lényegesen erősebb legyen.

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy a legjobb felhasználói élményt az Azure Cognitive Search használatával, hozzá kell adnunk további funkciók, nevezetesen a lapozás (akár oldalszámok, vagy végtelen görgetés), és az automatikus kiegészítés/javaslatok. Figyelembe kell vennünk a kifinomultabb keresési paramétereket is (például földrajzi keresés egy adott pont megadott sugarú körzetén belül, és a keresési eredmények sorrendje).

A következő lépésekkel egy sor oktatóanyag foglalkozik. Kezdjük a lapozással.

> [!div class="nextstepaction"]
> [C# oktatóanyag: Keresési eredmények tördelése - Azure Cognitive Search](tutorial-csharp-paging.md)


