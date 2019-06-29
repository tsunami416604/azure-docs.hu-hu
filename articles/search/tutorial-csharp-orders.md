---
title: C#az oktatóanyag az Azure Search – az eredmények rendezése
description: Ebben az oktatóanyagban a "Keresési eredmények tördelés – az Azure Search" projekt hozzáadása a keresési eredmények rendezése épül. Ismerje meg, hogyan eredmények megrendelési, egy elsődleges tulajdonságot, és az eredmények, amelyek ugyanazt a primary tulajdonságot, egy másodlagos tulajdonság eredmények rendezésének módját. Végül megtudhatja, hogyan sorrendjének eredményeit a pontozási profilok alapján.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 32e253b4e131d753ab6937d0aa2a49bda471e091
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466578"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>C#oktatóanyag: Az eredmények – Azure Search ORDER

Innentől sorozatunk oktatóanyagokban másnapi eredmények visszaadott és egy alapértelmezett sorrendben jelennek meg. Ez lehet a sorrendben, ahol az adatok is található, vagy akár egy alapértelmezett _relevanciaprofil_ lett meghatározva, amely használja, amikor nincs sorbarendezésre paraméterek vannak megadva. Ebben az oktatóanyagban megnyitjuk sorrendjének eredményeket egy elsődleges tulajdonságot, majd az eredmények, amelyek ugyanazt a primary tulajdonságot, hogyan lehet egy másodlagos tulajdonság a kijelölési rendezésének módját. Numerikus értékek alapján rendezés helyett, a végső példa bemutatja, hogyan sorrendjének egyéni pontozási profilok alapján. Azt is lépnek egy kicsit részletesebben megjelenítésének _komplex típusok_.

Annak érdekében, hogy a visszaadott eredmények összehasonlítása egyszerűen, a projekt alakzatot a létrehozott végtelen görgethető projektet hoz létre a [ C# oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md) oktatóanyag.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Egyetlen tulajdonság alapján eredményeket
> * Több tulajdonság alapján eredményeket
> * Szűrheti az eredményeket egy földrajzi pont távolságra alapján
> * Rendelés eredményeket a pontozási profilok

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

Végtelen görgethető verziója a [ C# oktatóanyag: Keresési eredmények tördelés – Azure Search](tutorial-csharp-paging.md) projektet, majd futtassa. Ez a projekt a saját verzióját, vagy telepítheti a Githubról: [Első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Egyetlen tulajdonság alapján eredményeket

Azt rendelés eredményeket egy tulajdonságot, például: Szálloda minősítés, hogy nem szeretné, hogy csak a rendezett eredményeket ad vissza, is szeretnénk, hogy helyesen szerepel-e a rendelés megerősítő. Más szóval azt order minősítés, ha azt kell megjelenítenie a minősítés a nézetet.

Ebben az oktatóanyagban is hozzáadjuk egy kicsit több, a megjelenített eredmények, a legolcsóbb szoba sebesség és az egyes Szálloda a legköltségesebb szoba aránya. A nézetet, hogy elmélyedhet rendelési, azt is hozzáadja, győződjön meg arról, mi a Microsoft vannak rendezés értékeket is megjelenik.

Nem, nem szükséges, módosíthatja a modellek rendezés engedélyezése. A nézet és a tartományvezérlő kell frissíteni. Először nyissa meg az otthoni vezérlő.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>A keresési paraméterek az OrderBy tulajdonság hozzáadása

1. Rendelés eredmények egy-egy numerikus tulajdonság, alapján történő csak állítsa be a **OrderBy** paramétert a tulajdonság nevét. Az a **Index (SearchData modell)** metódust, adja hozzá az alábbi sort a keresési paraméterek.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Az alapértelmezett iránya növekvő, bár hozzáadhat **asc** egyértelművé teszi, ez a tulajdonság. Csökkenő sorrendbe van megadva hozzáadásával **desc**.

2. Most futtassa az alkalmazást, és minden olyan gyakori keresési kifejezést adja meg. Előfordulhat, hogy az eredményeket, vagy nem lehet a megfelelő sorrendben sem, fejlesztőként, nem a felhasználó rendelkezik minden olyan egyszerűen megoldható az eredmények ellenőrzése!

3. Ellenőrizze, hogy törölje az eredményeket a minősítéshez vannak rendezve. Első lépésként cserélje le a **Téglalap1** és **Téglalap2** osztályok a hotels.css fájlban a következő osztályok (ezeket az osztályokat szükségünk ebben az oktatóanyagban az összes újakat).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >Böngészők általában gyorsítótárazzák a css-fájlokat, és a egy régi használt css-fájlt, és figyelmen kívül hagyja a módosítások vezet. Round Ez egy jó módja, hogy a hivatkozás hozzáadása egy verzió paramétere egy lekérdezési karakterláncot. Példa:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >A verziószám frissítéséhez, ha úgy véli, hogy a böngésző egy régi css-fájl használatban van.

4. Adja hozzá a **minősítés** tulajdonságot a **kiválasztása** paraméter, a a **Index (SearchData modell)** metódus.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Nyissa meg a nézet (index.cshtml), és cserélje le a renderelési hurok ( **&lt;!--megjelenítése az szállodában.--&gt;** ) a következő kóddal.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. A minősítés kell lennie az első megjelenő lap, és a is, amelyek segítségével a végtelen görgessen az úgynevezett következő lapjain elérhető. Az utóbbi két ezekben a helyzetekben esetében egyaránt frissíteni kell a **tovább** művelet a vezérlő és a **görgetéséhez** függvény a nézetben. A vezérlő kezdve módosítani a **tovább** metódust a következő kódot. Ez a kód létrehozza, és kommunikál a minősítés szöveget.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Most frissítse a **görgetéséhez** függvény a nézetben, a minősítő szöveg megjelenítéséhez.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. Most futtassa újra az alkalmazást. Keresés az összes gyakori kifejezés, például a "Wi-Fi", és győződjön meg arról, hogy az eredmények csökkenő sorrendben Szálloda minősítés szerint vannak rendezve:.

    ![Minősítés sorrendje alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Megfigyelheti, hogy több hotels van egy azonos minősítése, és így jelennek meg a megjelenésüket újra a sorrendet, amelyben az adatok található, amely tetszőleges.

    Mi megvizsgáljuk a második szintű rendezése hozzáadása, mielőtt adjunk néhány kódot szoba díjak tartományán megjelenítéséhez. Hozzáadjuk ezt a kódot, mind az adatok kinyerése megjelenítése egy _komplex típus_, és is így is megbeszélhetünk rendezése eredmények alapján ár (legolcsóbb első talán).

### <a name="add-the-range-of-room-rates-to-the-view"></a>A nézet szoba mértékek a tartomány hozzáadása

1. Adja hozzá a legolcsóbb és legköltségesebb szoba sebességét, a Hotel.cs modellre tartalmazó tulajdonságot.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Végén a szoba díjak számítása a **Index (SearchData modell)** az otthoni vezérlő a művelet. Adja hozzá a számítások ideiglenes adatok tárolása után.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. Adja hozzá a **termek** tulajdonságot a **kiválasztása** paramétert a **Index (SearchData modell)** a vezérlő tartozó műveleti módszer.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Módosítsa a megjelenítési ciklus a nézetben megjelenítendő eredmények első oldala arány tartományát.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Módosítsa a **tovább** metódus az otthoni vezérlő a tartományt, az eredmények azt követő lapokon az.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Frissítés a **görgetéséhez** függvény a nézetben, a hely kezeléséhez a szöveg értékeli.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. Futtassa az alkalmazást, és ellenőrizze a szoba arány tartományok jelennek meg.

    ![Hely arány címtartományok megjelenítése](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A **OrderBy** a keresési paraméterek tulajdonsága nem fogad el egy bejegyzés például **Rooms.BaseRate** biztosít a legolcsóbb szoba sebességét, még akkor is, ha a termek volt már alapján vannak rendezve, sebessége (ami nem azok). A minta adatkészletben, rendelt hely arány, a szállodák megjelenítéséhez meg kellene szűrje az eredményeket az otthoni vezérlőben, és ezeket az eredményeket elküldik a nézet a kívánt sorrendben.

## <a name="order-results-based-on-multiple-values"></a>Rendelés eredmények több érték alapján

Most már a kérdés, hogyan lehet az azonos minősítéssel rendelkező hotels megkülönböztetéséhez. Jó lenne sorrend alapján Szálloda volt felújított utoljára. Más szóval a újabban Szálloda volt felújított, annál nagyobb Szálloda megjelenik az eredmények között.

1. Adja hozzá a második szintű rendezése, módosítsa a **OrderBy** és **válassza ki** tulajdonságait a **Index (SearchData modell)** metódus tartalmazza a  **LastRenovationDate** tulajdonság.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Tetszőleges számú tulajdonságok is megadni a **OrderBy** listája. Ha hotels felújítás dátum és az azonos minősítése, egy harmadik tulajdonság különböztetheti lehetett megadni.

2. Ismét meg kell tekintse meg a felújítás dátumot a nézetben csak bizonyos, a rendezés helyességéről. Az ilyen olyan, mint egy felújítás valószínűleg csak az év szükség. Módosítsa a nézetben a renderelési ciklus a következő kódot.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Módosítsa a **tovább** metódus az otthoni a vezérlő, továbbítsa a legutóbbi felújítás dátum az év összetevőt.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Módosítsa a **görgetéséhez** függvény a nézetben a felújítás szöveg megjelenítéséhez.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. Futtassa az alkalmazást. Keressen a "készlet" vagy "view", például egy gyakori kifejezés, és ellenőrizze, hogy az azonos minősítéssel rendelkező hotels most már megjelenik felújítás dátum szerinti csökkenő sorrendben.

    ![Rendezés felújítás dátuma](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Szűrheti az eredményeket egy földrajzi pont távolságra alapján

Értékelések és felújítás dátum, csökkenő sorrendben legjobb megjelenő tulajdonságok példák. Egy betűrend szerinti lista lenne, növekvő sorrendben jól használható példa (például akkor, ha csak az egyik történt **OrderBy** tulajdonság- és állították be **Mezőmeghatározása** majd megjelenik egy betűrendbe rendezése ). Azonban a mintaadatokat a távolság egy földrajzi pont a megfelelőbb lenne.

Földrajzi távolságtól eredményeket megjeleníteni, több lépésre szükség.

1. Szűrje ki egy szűrőt hosszúság, szélesség és a radius-paraméterek megadásával az adott pont a megadott sugarú kívül esnek "Hotels". A pont függvény első elbírálása hosszúság. Adja meg kilométerben RADIUS van.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Az a fenti szűrő _nem_ távolság alapján az eredményt, a kiugró értékek csupán eltávolítja. Az eredmények sorrendjének, adja meg egy **OrderBy** beállítás, amely meghatározza a geoDistance metódust.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Bár az adott vissza eredményt Azure Search egy távolság szűrővel, van-e az adatok és az adott pont számított távolságát _nem_ adja vissza. Számítsa ki újra a nézet vagy egy tartományvezérlő, ezt az értéket, ha azt szeretné, hogy megjelenjen az eredményeket.

    Az alábbi kód kiszámítja két szél/maga pontok közötti távolság.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Már az alapelveket összekapcsolása. Azonban ezeket kódrészletek szerint létrehozásához kerül, a jelen oktatóanyagban egy térkép-alapú alkalmazás az olvasó gyakorlatként marad. Hogy ebben a példában további, fontolja meg, írja be a RADIUS-szal, egy város nevét, vagy egy pontot megkereséséhez a térképen, és válassza a radius. Ezek a lehetőségek további vizsgálatához az alábbi forrásanyagokban talál:

* [Az Azure Maps dokumentációja](https://docs.microsoft.com/azure/azure-maps/)
* [Keresse meg egy címet az Azure Maps search szolgáltatással](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Rendelés eredményeket a pontozási profilok

Az eddig az oktatóanyag során megadott példák bemutatják, hogyan numerikus értékeket (minősítés, felújítás dátum, földrajzi távolságtól), így a sorrendjének egy _pontos_ érzetét feldolgozása. Azonban néhány keresések és néhány adatot nem alkalmasak az ilyen két adatelem egy egyszerű összehasonlítása. Az Azure Search beletartoznak a _pontozási_. _Pontozási profilok_ adható meg, amelyek segítségével több összetett és minőségi összehasonlításokat, kell lennie a legértékesebb, ha tegyük fel, döntenie kell lennie, amely szöveges adatok összehasonlításával jelenik meg először adja meg egy adatkészletet.

Pontozási profilok nincsenek meghatározva a felhasználók, de általában a rendszergazdák egy adatkészlet. A "Hotels" adatok több pontozási profilok van beállítva. Most nézzük, hogyan van definiálva a relevanciaprofil, majd próbálkozzon a kódírás őket a kereséshez.

### <a name="how-scoring-profiles-are-defined"></a>Hogyan pontozási profilok vannak definiálva.

Nézzük tekintse meg a pontozási profilok három példákat, és fontolja meg a hogyan történik az egyes _kell_ eredmények sorrendje befolyásolja. Alkalmazásfejlesztőként akkor ne írja ki ezeket a profilokat, az adatok rendszergazdája megírásának, azonban hasznos lehet, tekintse meg a szintaxist.

1. Ez a relevanciaprofil használatos, ha nem ad meg bármelyik hotels adatkészlet az alapértelmezett **OrderBy** vagy **ScoringProfile** paraméter. Ez a profil felgyorsíthatók a _pontszám_ egy szállodai Vendég, ha a keresett szöveget a Szálloda neve, leírása vagy címkék (eszközök) listája megtalálható az. Figyelje meg, hogy bizonyos mezők alkalmazást hogyan állítja a pontozás súlyozását. Ha a keresett szöveg jelenik meg egy másik mező, a listában nem szereplő 1 súlyozási fog rendelkezni. Természetesen minél nagyobb a pontszám, a korábban egy eredményt a nézetben jelenik meg.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. A következő relevanciaprofil jelentősen, felgyorsíthatók a pontszám, ha egy megadott paraméter tartalmaz egy vagy több (amely hívjuk "eszközök"), a címkék listájában. A profil a lényeg, hogy a paraméter _kell_ adni, szöveget tartalmazó. Ha a paraméter üres, vagy nincs megadva, a rendszer hibajelzést hiba.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. A fenti harmadik példában a minősítés egy jelentős boost biztosít a pontszámot. A legutóbbi felújított dátuma a mai dátumtól is a pontszám, de csak ha az adatok körébe 730 napig (2 év) jelentősen növelheti.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Most ossza meg velünk működnek az Ha ezeket a profilokat, látjuk, hogy azok kell!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Adja hozzá a kódot a nézetet, hogy a profilok összehasonlítása

1. Nyissa meg az index.cshtml fájl, és cserélje le a &lt;törzs&gt; szakaszban a következő kóddal.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. Nyissa meg a SearchData.cs fájlt, és cserélje le a **SearchData** osztályban az alábbi kódra.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. Nyissa meg a hotels.css fájlt, és adja hozzá a következő HTML-osztályokat.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Kódot hozzáadnia a tartományvezérlőt a adja meg a relevanciaprofil

1. Nyissa meg az otthoni vezérlő fájlt. Adja hozzá a következő **használatával** utasítással (támogatási a listák létrehozása).

    ```cs
    using System.Linq;
    ```

2.  Ebben a példában a kezdeti hívást kell **Index** térni egy kicsit több, mint a kezdő nézete. A metódus a nézetben megjelenítendő legfeljebb 20 eszközök most már keres.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. Két privát módszerek aspektusait menteni egy ideiglenes tárolóra, és helyreállítja őket az ideiglenes storage-ból, és töltse fel a modell van szükségünk.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

4. Be kell a **OrderBy** és **ScoringProfile** paramétereket szükség szerint. Cserélje le a meglévő **Index (SearchData modell)** , a következő metódust.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Olvassa el a hozzászólások minden a **váltson** beállításokat.

5. Azt nem kell módosításokat végezni a **tovább** műveletet, ha végrehajtotta a rendezés több tulajdonság alapján az előző szakaszban további kódját.

### <a name="run-and-test-the-app"></a>Futtassa, és az alkalmazás tesztelése

1. Futtassa az alkalmazást. A nézet az eszközök teljes körű kell megjelennie.

2. Rendezéshez, "által numerikus minősítés" kiválasztásával felkínálja a numerikus rendezése már létrehozott ebben az oktatóanyagban az egyenlő értékelés hotels kiválasztani felújítás dátummal.

!["Beach" minősítés alapján rendezése](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Próbálja meg a "által eszközök" profil. Adja meg az eszközök különböző beállításokat, és győződjön meg arról, hogy ezek az eszközök a szállodák fel az eredmények listájában vannak támogatni.

!["Beach" profilon alapuló rendezése](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Próbálja ki a "Szerint Renovated dátum/minősítés profil" Ha kap várt megtekintéséhez. Egyetlen nemrégiben felújított hotels kell kapnia egy _frissessége_ boost.

### <a name="resources"></a>További források

További információkért tekintse meg a következő [relevanciaprofil hozzáadása az Azure Search-index](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő takeaways a projekt:

* Keresési eredmények kell rendelni, leginkább releváns először elvárják lesz.
* Így a rendezés igények kielégítéséhez strukturált adatok sem ördöngösség. Nem sikerült szerinti rendezéshez a "legolcsóbb" egyszerűen, mivel az adatok nem strukturált az elvégezni kívánt további kód nélkül rendezés engedélyezése.
* Rendezés, az eredmények, amelyek ugyanazzal az értékkel rendelkeznek a magasabb szintű érzetét megkülönböztetéséhez a sok szintje lehet.
* Természetes, az egyes eredmények növekvő sorrendben (pl.: egy pont távolságra), és néhány a csökkenő sorrendben kell rendezni (pl.: Vendég a minősítés).
* Pontozási profilok is lehet meghatározni, ha a numerikus összehasonlításban nem érhetők el, vagy nem elég, intelligens az adatkészlet. Minden eredmény pontozási sorrend Súgó és intelligensen az eredmények megjelenítéséhez.

## <a name="next-steps"></a>További lépések

Befejezte a sorozatát C# oktatóanyagokat – meg kell révén az Azure Search API-k értékes ismerete.

A további hivatkozás és oktatóanyagok, fontolja meg a böngészés [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), vagy a többi oktatóanyag a [Azure Search-dokumentáció](https://docs.microsoft.com/azure/search/).
