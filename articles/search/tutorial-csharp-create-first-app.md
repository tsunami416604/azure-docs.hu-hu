---
title: C#című cikk alapján létrehozhatja első alkalmazását – Azure Search
description: Ez az oktatóanyag részletesen ismerteti az első alkalmazás létrehozása az Azure Search biztosít. Az oktatóanyagban mindkét egy hivatkozást kínál a egy működő alkalmazást a Githubról, és a teljes folyamatot hozhat létre az előzmények nélküli új alkalmazást. További információ az Azure Search alapvető összetevői.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: d569437a3e6f6f05ddb9c6fa85f62c77ac51f72b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443817"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>C#oktatóanyag: Hozzon létre első alkalmazását – Azure Search

Ismerje meg, hogyan hozhat létre egy webes felület, használja az Azure Search-index lekérdezése és a jelenlegi keresési eredmények. Ebben az oktatóanyagban egy meglévő, üzemeltetett index kezdődik, úgy, hogy a keresési lapot készítésére összpontosíthat. Az index tartalmazza a fiktív Szálloda adatokat. Ha már rendelkezik egy egyszerű lap, fejlesztheti az ezt követő leckék lapozási, a metszettel és a egy gépelés közbeni élmény.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A fejlesztési környezet beállítása
> * Modell adatstruktúrák
> * Weblap létrehozása
> * Metódusok meghatározásához
> * Az alkalmazás tesztelése

Azt is megtudhatja, hogyan könnyen érthető megjegyzésblokkok írására van egy keresési hívás. A legfontosabb utasításokat a kódban elkészít vannak ágyazva az alábbiakban néhány sort.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Ez egy hívás elindítja a keresést, az Azure-beli adat, és az eredményeket adja vissza.

![A "készlet" keresése](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

[A Visual Studio telepítése](https://visualstudio.microsoft.com/) az IDE adatokként.

### <a name="install-and-run-the-project-from-github"></a>Telepítse és futtassa a projektet a Githubról

1. Keresse meg a mintát a Githubon: [Első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Válassza ki **Klónozás vagy letöltés** , és adja meg a projekt személyes helyi példányának.
1. A Visual Studio használatával, keresse meg, és nyissa meg a megoldást, az alapszintű kereséssel oldal, és válassza ki **Indítás hibakeresés nélkül** (vagy nyomja le az F5 billentyűt).
1. Írja be az egyes szavak (például "Wi-Fi", "view", "sáv", "ideiglenes"), és vizsgálja meg az eredményeket.

    ![A "Wi-Fi" keresése](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Ez a projekt remélhetőleg a zökkenőmentesen futnak, és futó Azure app rendelkezik. Az alapvető összetevők kifinomultabb keresések számos szerepelnek az egy alkalmazáshoz, így érdemes halad át, és hozza létre újra, lépésről lépésre.

Ez a projekt létrehozása előzmények nélkül, és ezáltal segít a megerősítése az Azure Search összetevői a szem előtt, lépjen az alábbi lépéseket.

## <a name="set-up-a-development-environment"></a>A fejlesztési környezet beállítása

1. A Visual Studio 2017, vagy újabb verziójú, jelölje be a **új projektgyűjtemény** majd **ASP.NET Core-webalkalmazás**. Adjon meg egy nevet, például "FirstAzureSearchApp" a projekt.

    ![A felhő-projekt létrehozása](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Miután rákattintott a **OK** a projekt típus után kap egy második együttesét a projekt típushoz elérhető beállításokat. Válassza ki **webalkalmazás (Model-View-Controller)** .

    ![MVC projekt létrehozása](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Ezután a **eszközök** menüjében válassza **NuGet-Csomagkezelő** , majd **NuGet-csomagok kezelése megoldáshoz...** . Nincs telepíteni kell egy csomagot. Válassza ki a **Tallózás** lapfülre, majd írja be az "Azure Search" kifejezést a keresőmezőbe. Telepítés **Microsoft.Azure.Search** amikor megjelenik a listában (9.0.1, verzió vagy újabb). Kattintson végig a telepítés befejezéséhez néhány további párbeszédpanelek kell.

    ![Azure-kódtárak hozzáadása NuGet használatával](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Az Azure Search inicializálása

Ebben a példában a nyilvánosan elérhető Szálloda adatokat használjuk. Ezeket az adatokat az 50 képzeletbeli Szálloda neveket és leírásokat, kizárólag a bemutató-adatokat biztosítva célból létrehozott egy tetszőleges gyűjteménye. Ezek az adatok eléréséhez meg kell adjon meg egy nevet és a hozzá tartozó kulcs.

1. Nyissa meg a appsettings.json fájlt az új projekt, és cserélje le az alapértelmezett sorokat a következő nevére és kulcsára. Itt nem egy kulcsot egy példa látható API-kulcs van _pontosan_ a Szálloda adatok eléréséhez szükséges kulcs. Az appsettings.json fájlt kell kinéznie.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Ez a fájl nem történik, de ez a fájl tulajdonságainak kiválasztunk, és módosítása a **Copy to Output Directory** beállítást **másolás, ha újabb**.

    ![A kimenet a beállítások másolása](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modell adatstruktúrák

Modellek (C# osztályok) az ügyfél (Nézet), a kiszolgáló (vezérlő), valamint az MVC (model, megtekintése, tartományvezérlő) architektúra használatával az Azure-felhő közötti kommunikációra szolgálnak. Általában ezek a modellek, amelyek hozzáférnek az adatok struktúráját fogja tartalmazni. Továbbá meg kell kezelni a nézetvezérlője/kommunikációhoz modell.

1. Nyissa meg a **modellek** mappát a projekt használatával a Megoldáskezelőben, és egy alapértelmezett modell meg ott jelenik meg: **ErrorViewModel.cs**.

2. Kattintson a jobb gombbal a **modellek** mappára, és válassza **Hozzáadás** majd **új elem**. Majd a megjelenő párbeszédablakban válassza **ASP.NET Core** majd az első lehetőség **osztály**. Nevezze át a .cs fájlt hotel.cs fájlban, és kattintson a **Hozzáadás**. Cserélje le a hotel.cs fájlban a tartalmát az alábbira. Figyelje meg a **cím** és **szoba** tagokat az osztály, ezek a mezők találhatók osztályok magukat, szükségünk lesz modellek számukra túl.

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

3. Kövesse a modell létrehozásának eljárást a **cím** osztályhoz, azzal a különbséggel a Address.cs fájl neve. Cserélje ki annak tartalmát az alábbira.

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

4. És ismét leírtakat hozhat létre a **szoba** osztályt, a fájl Room.cs elnevezési. Ismét cserélje ki annak tartalmát az alábbira.

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

5. Készletét **Szálloda**, **cím**, és **szoba** osztályok az Azure-t néven ismert [ _komplex típusok_ ](search-howto-complex-data-types.md), az Azure Search egyik fontos szolgáltatása. Komplexní typy is hány szint mélységig az osztályok és alosztályok, és jóval összetettebb adatstruktúrákat, mint képviseltesse engedélyezése _egyszerű típusokat_ (csak a primitív tagokat tartalmazó osztály). Valóban szükséges egy további modell, ezért nyissa meg újra létrehozni egy új modellosztály folyamatát, azonban ennek az időnek hívja az osztály SearchData.cs és az alapértelmezett kód cserélje le a következő.

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

    Ez az osztály tartalmazza a felhasználói bevitel (**Keresettszöveg**), és a keresés kimeneti (**resultList**). A kimeneti típus kritikus fontosságú, **DocumentSearchResult&lt;Szálloda&gt;** , mivel ez a típus pontosan megegyezik az eredményeket a keresési, ezért ellenőriznünk kell a ezt a hivatkozást a nézet keresztül adja át.



## <a name="create-a-web-page"></a>Weblap létrehozása

A létrehozott projekt alapértelmezés szerint létrehoz egy ügyfél száma nézeteket. A pontos nézetek használata Core .NET verzióját függ (használjuk 2.1 ebben a példában). Mindezt legyenek a **nézetek** mappát a projekt. Az Index.cshtml fájl módosítása csak kell (a a **nézetek készletkövetést** mappát).

Ebben az esetben Index.cshtml tartalmának törlése, és építse újra a fájlt az alábbi lépéseket.

1. Két kis képek az nézetben használjuk. Használhatja a saját, vagy másolja át a rendszerképek között a GitHub-projekt: azure-logo.png és search.png. E két lemezképet kell helyezni a **wwwroot/képek** mappát.

2. Az első sort Index.cshtml kell hivatkoznia a modellt fogjuk használni az ügyfél (Nézet) és a kiszolgáló (vezérlő), amely közötti kommunikációhoz a **SearchData** létrehozott modellt. Adja hozzá ezt a sort a Index.cshtml fájl.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Célszerű a szabványos eljárás az, hogy a nézetben, adjon meg egy címet, a következő sorokat kell lennie:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. A cím a következő adjon meg egy HTML stíluslap, amely hamarosan létrehozunk egy hivatkozást.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Most, hogy a nézet húsa. A lényeg, ne felejtse el, hogy rendelkezik-e a nézet két helyzetek kezelésére. A megjelenített először azt kell kezelnie először az alkalmazást elindítja, és a felhasználó még nem lépett keresési szöveg. Másodszor azt kell kezelnie a megjelenített eredmények mellett a keresőmezőbe, a felhasználó ismételt felhasználásra. E két helyzetek kezelése érdekében ellenőrizze, hogy a nézet a megadott mintának null értékű vagy nem kell. Egy NULL értékű modell azt jelzi, hogy vagyunk az első két helyzetben (az alkalmazás a kezdeti futó). Adja hozzá a következőket az Index.cshtml fájl, és olvassa végig a megjegyzéseket.

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

6. Végül a stíluslap hozzáadunk. A Visual Studióban az a **fájl** menüből válassza **új/fájl** majd **stíluslap** (a **általános** kiemelt). Cserélje le az alapértelmezett kód a következő. A stílusok fog nem lehet fogjuk ebbe a fájlba bármely részletesebben, normál HTML.

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

7. Mentse a stíluslap fájlt hotels.css, mint a wwwroot/css mappába, az alapértelmezett site.css fájl mellett.

Ezzel befejezte a nézet. Igyekszünk jó folyamatban van. A modellek és nézetek végezhető el, csak a vezérlő összekapcsolása mindent marad.

## <a name="define-methods"></a>Metódusok meghatározásához

Módosítani szeretne az egyik vezérlő tartalmát kell (**kezdőlap vezérlő**), amely alapértelmezés szerint létrejön.

1. Nyissa meg a HomeController.cs fájlban, és cserélje le a **használatával** az alábbi utasításokat.

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

### <a name="add-index-methods"></a>Index módszerek hozzáadása

Igazolnia kell, hogy két **Index** módszerek egyet nincsenek paraméterei (az alkalmazás első megnyitásakor eset) tart, és egy modell véve paramétert (a, amikor a felhasználó megadott szöveg keresése). Ezen metódusok közül az első alapértelmezetten jön létre. 

1. Adja hozzá a következő metódust az alapértelmezett után **Index()** metódust.

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

                // Make the Azure Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Figyelje meg a **aszinkron** deklarace metody, és a **await** hívása **RunQueryAsync**. Ezek a kulcsszavak a hívások aszinkron gondoskodik, és ezért ne blokkolja a szálak a kiszolgálón.

    A **catch** letiltása, amely alapértelmezés szerint az USA készült hiba modellt használja.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Vegye figyelembe a hibakezelést és más alapértelmezett nézetek és módszerek

Attól függően, melyik verzióját használja, a .NET Core alapértelmezett némileg eltérő készletét nézetek alapértelmezés szerint jönnek létre. .NET Core 2.1 az alapértelmezett nézetek Index, névjegy, névjegy, adatvédelmi és hiba esetén. A .NET Core 2.2-es, például az alapértelmezett nézetek olyan Index, az adatvédelmet és a hiba. Mindkét esetben ezeket az oldalakat alapértelmezett megtekintheti az alkalmazás futtatásakor, és vizsgálja meg, hogyan kezeli azokat a vezérlő.

A Microsoft teszteli a hiba nézet később ebben az oktatóanyagban.

A GitHub-mintában a fel nem használt nézetek és a kapcsolódó lépések töröltük.

### <a name="add-the-runqueryasync-method"></a>Adja hozzá a RunQueryAsync metódust

Az Azure Search-hívásra van beágyazva a **RunQueryAsync** metódust.

1. Először adja hozzá a statikus változókat, állítsa be az Azure-szolgáltatás, és a egy hívás kezdeményezése nélkül.

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

2. Ezután adja hozzá a **RunQueryAsync** metódus magát.

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

    Ezen módszer esetében először biztosítható az Azure konfigurációs van kezdeményezett, majd állítsa be az egyes keresési paraméterek. A mezők nevei a **kiválasztása** paraméter felel meg pontosan a tulajdonságneveket a **Szálloda** osztály. Lehetséges, hogy hagyja ki a **kiválasztása** paramétert, ebben az esetben az összes tulajdonság adja vissza. Azonban a nem beállítás **kiválasztása** paraméterek nem hatékony, ha azt csak az adatok egy részét. Foglalkozunk tulajdonságainak megadásával csak ezeket a tulajdonságokat adja vissza.

    Keresés a aszinkron hívás (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Szálloda&gt;(model.searchText, paraméterek);** ) Mi ez az oktatóanyag és az alkalmazás a részletes tudnivalók. A **DocumentSearchResult** osztály egy érdekes, és (ha az alkalmazás fut) érdemes itt állítson be egy töréspontot, és vizsgálja meg a tartalmát a hibakeresőt használatával **model.resultList**. Keresse meg, hogy éppen intuitív, hogy az adatokat kéri, és sokkal más.

Most a kis időt az egyértelműség.

### <a name="test-the-app"></a>Az alkalmazás tesztelése

Most ellenőrizzük a futtatások megfelelően.

1. Válassza ki **hibakeresési/Start Without Debugging** vagy nyomja le az F5 billentyűt. Ha megfelelően dolog van kódolva, megjelenik a kezdeti Index nézet.

     ![Az alkalmazás megnyitásakor](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Adja meg például a "beach" (vagy bármilyen szöveg, amely mind) szöveget, és kattintson a keresés ikonra. Egyes eredményeket kell kapnia.

     ![A "beach" keresése](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Adjon meg "öt csillag". Ne feledje, hogyan juthat nincs eredménye. Egy bonyolultabb keresési volna "öt csillag" gyökérkönyvtárral "engedélyezhető" szinonimát, és ezeket az eredményeket adja vissza. A szinonimák használata az Azure Search szolgáltatásban elérhető, ha azt fogja nem lehet benne, az első oktatóanyagok.
 
4. Próbálja ki, írja be a "hot" keresési szövegként. Ugyanúgy _nem_ "hotel" szót tartalmazó bejegyzéseket vissza őket. A keresés csak teljes szavak van megkeresése, néhány eredményeket ad vissza, ha.

5. Használjon más szavakat: "-készlet", "babaváró", "megtekintése" és függetlenül. Látni fogja az Azure Search a legegyszerűbb működik, de továbbra is meggyőző szintet.

## <a name="test-edge-conditions-and-errors"></a>Vizsgálati edge-feltételek és hibák

Fontos győződjön meg arról, hogy a hibakezelés funkciókat, akkor kell, akkor is, ha működik-e tökéletesen működik-e. 

1. Az a **Index** metódust, miután a **próbálja {** hívja, adja meg a sor **új Exception() Throw**. Ehhez a kivételhez kényszeríti a hiba, hogy a szöveg keresése.

2. Futtassa az alkalmazást, adja meg a "sávon" szöveges keresés, és kattintson a keresés ikonra. A kivétel a hiba nézetet kell eredményeznie.

     ![Kényszerített hiba](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Belső hiba számok visszaadandó hibalapok biztonsági kockázatot minősül. Ha az alkalmazás általános használatra szánt, tegye a mi vissza, ha hiba lép fel, biztonságos és ajánlott eljárások egyes vizsgálja.

3. Távolítsa el **új Exception() Throw** Ha elégedett a hibakezelési működik, ahogyan kellene.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő takeaways a projekt:

* Egy Azure Search-hívás tömör, és egyszerűen, az eredmények értelmezéséhez.
* Asynchronní volání kanálu összetettsége kisebb mennyiségű ad hozzá a tartományvezérlő, de az ajánlott eljárás, ha szeretne olyan minőségi alkalmazásokat fejleszthet.
* Ez az alkalmazás egyszerű szöveges keresés, mi beállítása által meghatározott végrehajtása **searchParameters**. Azonban ez az osztály egy kifinomultabbak hozzáadni a keresés sok tagot tartalmazó is kell feltöltenie. Jelentősen nagyobb teljesítményű tenni az alkalmazást nem sok további munkára van szükség.

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy a legjobb felhasználói élmény, használja az Azure Search, hozzá kell adnunk a további funkciók, többek között a lapozófájl (segítségével lapon végtelen görgethető, vagy számokat), és az automatikus kiegészítés és javaslatok. Azt is figyelembe kell venni bonyolultabb keresési paraméterek (például szállodák egy adott időpontra, és a keresési eredmények rendezése a megadott sugarú körön belül földrajzi keresés).

A következő lépések oktatóanyag egy sorozat része foglalkozik. Kezdjük lapozást.

> [!div class="nextstepaction"]
> [C#Oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md)


