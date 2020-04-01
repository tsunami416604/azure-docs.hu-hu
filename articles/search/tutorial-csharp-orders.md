---
title: C# oktatóanyag az eredmények rendelése
titleSuffix: Azure Cognitive Search
description: Ez az oktatóanyag bemutatja a keresési eredmények rendelését. Egy korábbi szállodai projektre épül, elsődleges tulajdonság, másodlagos tulajdonság szerint rendelve, és tartalmaz egy pontozási profilt a kiemelési feltételek hozzáadásához.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121546"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C# oktatóanyag: Az eredmények sorrendje - Azure Cognitive Search

Egészen a sorozatunk ezen pontjáig az eredmények et visszaadják, és alapértelmezett sorrendben jelennek meg. Ez lehet az adatok helyének sorrendje, vagy esetleg egy alapértelmezett _pontozási profil_ van definiálva, amelyet akkor fog használni, ha nincs megadva rendezési paraméter. Ebben az oktatóanyagban bemutatjuk, hogyan kell megrendelni az eredményeket egy elsődleges tulajdonság alapján, majd az eredmények, amelyek azonos elsődleges tulajdonság, hogyan kell rendelni, hogy a kiválasztás egy másodlagos tulajdonság. A numerikus értékeken alapuló rendezés alternatívájaként a végső példa bemutatja, hogyan kell rendelni egy egyéni pontozási profil alapján. Mi is megy egy kicsit mélyebbre a kijelző _összetett típusok_.

A visszaadott eredmények egyszerű összehasonlításához ez a projekt a C# oktatóanyagban létrehozott végtelen görgetési projektre [épül: Keresési eredmények tördelése – Azure Cognitive Search](tutorial-csharp-paging.md) oktatóanyag.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Rendelési eredmények egy tulajdonság alapján
> * Eredmények sorrendje több tulajdonság alapján
> * Eredmények szűrése földrajzi ponttól való távolság alapján
> * A rendezési eredmények sorrendje pontozási profil alapján

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

A C# oktatóanyag végtelen görgetési verziójával [rendelkezik: Keresési eredmények tördelése – Azure Cognitive Search](tutorial-csharp-paging.md) projekt. Ez a projekt lehet a saját verziója, vagy telepítheti a GitHubról: [Az első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Rendelési eredmények egy tulajdonság alapján

Amikor egy szálláshely alapján rendelünk eredményeket, mondjuk a szállodai értékelés alapján, nem csak a megrendelt eredményeket akarjuk, hanem megerősítést is szeretnénk arról, hogy a megrendelés helyes. Más szóval, ha megrendeljük az értékelést, akkor meg kell jelenítenünk a minősítést a nézetben.

Ebben a bemutatóban egy kicsit többet adunk az eredmények megjelenítéséhez, a legolcsóbb szobaárhoz és a legdrágább szobaárhoz minden egyes szállodához. Ahogy ás a rendelés, mi is hozzá értékeket, hogy megbizonyosodjon arról, amit rendelünk is megjelenik a nézetben.

A rendezés engedélyezéséhez nincs szükség a modellek módosítására. A nézetet és a vezérlőt frissíteni kell. Kezdje az otthoni vezérlő megnyitásával.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Az OrderBy tulajdonság hozzáadása a keresési paraméterekhez

1. Az eredmények egyetlen numerikus tulajdonság alapján imitorásának megrendeléséhez mindössze annyit kell tenni, hogy az **OrderBy** paramétert a tulajdonság nevére állítja be. Az **Index (SearchData modell)** metódusban adja hozzá a következő sort a keresési paraméterekhez.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Az alapértelmezett sorrend növekvő, de **a tulajdonsághoz hozzáadhat asc-t,** hogy ez egyértelmű legyen. A csökkenő sorrendet a **desc**hozzáadása adja meg.

2. Most futtassa az alkalmazást, és adjon meg bármilyen gyakori keresési kifejezést. Az eredmények lehet, hogy nem a megfelelő sorrendben, mint sem te, mint a fejlesztő, nem a felhasználó, bármilyen egyszerű módja annak, hogy ellenőrizze az eredményeket!

3. Tisztázzuk, hogy az eredmények et a minősítés alapján rendelik el. Először cserélje ki a **box1** és **box2** osztályok a hotels.css fájlt a következő osztályok (ezek az osztályok mind az újak, szükségünk van a bemutató).

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
    >Böngészők általában cache css fájlokat, és ez ahhoz vezethet, hogy egy régi css fájlt használnak, és a szerkesztések figyelmen kívül hagyni. Ez egy jó módszer, ha egy verzióparaméterrel rendelkező lekérdezési karakterláncot ad hozzá a hivatkozáshoz. Példa:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Frissítse a verziószámot, ha úgy gondolja, egy régi css fájlt használnak a böngésző.

4. Adja hozzá a **Rating** tulajdonságot a **Select** paraméterhez az **Index(SearchData modell)** metódusban.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Nyissa meg a nézetet (index.cshtml), és cserélje ki a renderelési hurkot (**&lt;!-- A szállodai adatok megjelenítése. --&gt;**) a következő kóddal.

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

6. Az értékelésnek elérhetőnek kell lennie mind az első megjelenített oldalon, mind a következő oldalakon, amelyeket a végtelen görgetéssel hívnak meg. Az utóbbi két helyzet, meg kell frissíteni mind a **következő** művelet a vezérlő, és a **görgetett** funkció a nézetben. A vezérlővel kezdve módosítsa a **Következő** módszert a következő kódra. Ez a kód létrehozza és közli a minősítési szöveget.

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

7. Most frissítse a **görgetett** funkciót a nézetben, hogy megjelenítse a minősítésszöveget.

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

8. Most futtassa újra az alkalmazást. Keressen rá bármilyen közös kifejezésre, például a "wifi"-re, és ellenőrizze, hogy az eredményeket a szállodai minősítés csökkenő sorrendje rendezi-e.

    ![Rendelés értékelés alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Észre fogja venni, hogy több szálloda azonos minősítéssel rendelkezik, így a kijelzőn való megjelenésük ismét az adatok megállapításának sorrendje, ami tetszőleges.

    Mielőtt megvizsgálnánk egy második rendelési szint hozzáadását, adjunk hozzá néhány kódot a szobaárak tartományának megjelenítéséhez. Hozzáadjuk ezt a kódot, hogy mind azt mutatják, kinyerése adatok at egy _összetett típusú,_ és így is megtudjuk vitatni rendelési eredmények alapján az ár (legolcsóbb először talán).

### <a name="add-the-range-of-room-rates-to-the-view"></a>A szobaárak tartományának hozzáadása a nézethez

1. Add tulajdonságok, amelyek a legolcsóbb és legdrágább szobaár a Hotel.cs modell.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Számítsa ki a szobaárak végén az **Index (SearchData modell)** művelet, az otthoni vezérlő. Adja hozzá a számításokat az ideiglenes adatok tárolása után.

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

3. Adja hozzá a **Rooms** tulajdonságot a vezérlő **Index (SearchData modell)** műveletmetódusának **Select** paraméteréhez.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Módosítsa a renderelési hurkot a nézetben az eredmények első oldalának sebességtartományának megjelenítéséhez.

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

5. Módosítsa a **Next** metódust az otthoni vezérlőben a sebességtartomány közléséhez a következő találati oldalakhoz.

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

6. Frissítse a **görgetett** funkciót a nézetben, hogy kezelje a szobaárak szövegét.

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

7. Futtassa az alkalmazást, és ellenőrizze, hogy megjelennek-e a szobaár-tartományok.

    ![Szobaár-tartományok megjelenítése](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A keresési paraméterek **OrderBy** tulajdonsága nem fogad el olyan bejegyzést, mint a **Rooms.BaseRate,** hogy a legolcsóbb szobaárat biztosítsa, még akkor sem, ha a szobák már az arány alapján vannak rendezve. Ebben az esetben a szobák nincsenek az ár szerint rendezve. Annak érdekében, hogy a hotelek megjelenjenek a szobaár alapján rendezett mintaadatkészletben, az eredményeket az otthoni vezérlőben kell rendeznie, és ezeket az eredményeket a kívánt sorrendben kell elküldenie a nézetbe.

## <a name="order-results-based-on-multiple-values"></a>Eredmények sorrendje több érték alapján

A kérdés most az, hogyan lehet megkülönböztetni az azonos minősítésű szállodákat. Az egyik jó módja az lenne, hogy a szálloda legutóbbi felújítása alapján rendeljen. Más szóval, minél újabban felújították a szállodát, annál magasabban jelenik meg a szálloda az eredmények között.

1. A rendezés második szintjének hozzáadásához módosítsa az **OrderBy** és **select tulajdonságokat** az **Index (SearchData modell)** metódusban úgy, hogy tartalmazza a **LastRenovationDate** tulajdonságot.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Tetszőleges számú tulajdonság adható meg az **OrderBy** listába. Ha a szállodák nak azonos minősítésük és felújítási dátumuk lenne, egy harmadik ingatlant is be lehetne írni, hogy különbséget tegyenek közöttük.

2. Ismét meg kell látni a felújítás időpontját a nézetben, csak hogy biztosak legyünk a megrendelés helyes. Egy ilyen dolog, mint a felújítás, valószínűleg csak az év szükséges. Módosítsa a renderelési hurkot a nézetben a következő kódra.

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

3. Módosítsa a **Next** módszert az otthoni vezérlőben, hogy továbbítsa az utolsó felújítási dátum év összetevőjét.

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

4. A **kiújított** szöveg megjelenítéséhez módosítsa a görgetett funkciót a nézetben.

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

5. Futtassa az alkalmazást. Keressen egy közös kifejezéssel, például "pool" vagy "view" kifejezéssel, és ellenőrizze, hogy az azonos minősítésű szállodák a felújítás dátumának csökkenő sorrendjében jelennek-e meg.

    ![Megrendelés a felújítás napján](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Eredmények szűrése földrajzi ponttól való távolság alapján

Az értékelés és a felújítás dátuma olyan tulajdonságok, amelyek a legjobban csökkenő sorrendben jelennek meg. Az ábécé-lista példa lenne a növekvő sorrend jó használatára (például ha csak egy **OrderBy** tulajdonság lenne, és **HotelName** értékre lenne állítva, akkor egy betűrendben jelenik meg). A mintaadatok esetében azonban megfelelőbb lenne a földrajzi ponttól való távolság.

Az eredmények földrajzi távolságon alapuló megjelenítéséhez több lépésre van szükség.

1. Szűrje ki az összes olyan szállodát, amely egy adott sugáron kívül esik a megadott ponttól, a hosszúsági, szélességi és sugárparaméterekkel rendelkező szűrő megadásával. A hosszúság először a POINT függvényt kapja. A sugár kilométerben van.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. A fenti szűrő _nem_ sorrendben az eredmények alapján a távolság, csak eltávolítja a kiugró értékek. Az eredmények rendeléséhez adjon meg egy **OrderBy** beállítást, amely megadja a geoDistance metódust.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Bár az eredményeket az Azure Cognitive Search egy távolságszűrő használatával adta vissza, az adatok és a megadott pont közötti számított távolság _nem_ kerül visszaadásra. Ha meg szeretné jeleníteni az eredmények között, akkor újraszámítja ezt az értéket a nézetben vagy a vezérlőben.

    A következő kód kiszámítja a két lat/lon pont közötti távolságot.

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

4. Most össze kell kötned ezeket a fogalmakat. Azonban ezek a kódrészletek, amennyire a bemutató megy, épület egy térkép-alapú alkalmazás marad, mint egy gyakorlat az olvasó számára. Ha tovább szeretné vinni ezt a példát, fontolja meg egy sugárral rendelkező városnév megadását, vagy egy pont megkeresését a térképen, és jelöljön ki egy sugarat. A lehetőségek további vizsgálatához olvassa el az alábbi forrásokat:

* [Az Azure Maps dokumentációja](https://docs.microsoft.com/azure/azure-maps/)
* [Cím keresése az Azure Maps keresési szolgáltatásával](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>A rendezési eredmények sorrendje pontozási profil alapján

A bemutatott példák az eddigi bemutatóban azt mutatják, hogyan kell rendelni a numerikus értékeket (értékelés, felújítás dátuma, földrajzi távolság), amely _pontos_ rendelési folyamatot biztosít. Egyes keresések és egyes adatok azonban nem alkalmasak két adatelem ilyen egyszerű összehasonlítására. Az Azure Cognitive Search tartalmazza a _pontozás_fogalmát. _Pontozási profilok_ adhatók meg egy olyan adathalmazhoz, amely összetettebb és minőségibb összehasonlítások biztosításához használható, amelyek a legértékesebbek, amikor mondjuk a szövegalapú adatok összehasonlítása annak eldöntésére, hogy melyik jelenjen meg először.

A pontozási profilokat nem a felhasználók határozzák meg, hanem általában egy adatkészlet rendszergazdái. A szállodák adataihoz több pontozási profil tikulálva. Nézzük meg, hogyan definiált egy pontozási profilt, majd próbáljunk meg kódot írni a kereséshez.

### <a name="how-scoring-profiles-are-defined"></a>A pontozási profilok meghatározása

Nézzünk meg három példát a pontozási profilok, és fontolja meg, hogyan _kell_ befolyásolnia az eredmények sorrendje. Alkalmazásfejlesztőként nem írja ezeket a profilokat, azadat-rendszergazda írja őket, azonban hasznos a szintaxis áttekintése.

1. Ez a hoteladatkészlet alapértelmezett pontozási profilja, amelyakkor használatos, ha nem ad meg **OrderBy** vagy **ScoringProfile paramétert.** Ez a profil növeli a szálloda _pontszámát,_ ha a keresési szöveg szerepel a szálloda nevében, leírásában vagy a címkék listájában (kényelmi szolgáltatások). Figyeljük meg, hogy a súlyok a pontozási javára bizonyos területeken. Ha a keresési szöveg egy másik, alább nem felsorolt mezőben jelenik meg, akkor a vastagsága 1 lesz. Nyilvánvaló, hogy minél magasabb a pontszám, annál korábbi eredmény jelenik meg a nézetben.

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

2. A következő pontozási profil jelentősen növeli a pontszámot, ha a megadott paraméter tartalmaz egy vagy több címkelistát (amelyet "kényelemnek" nevezünk). A profil legfontosabb pontja, hogy meg _kell_ adni egy szöveget tartalmazó paramétert. Ha a paraméter üres, vagy nincs megadva, hiba lép fel.
 
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

3. Ebben a harmadik példában az értékelés jelentős lökést ad a pontszámnak. Az utolsó felújított dátum is növeli a pontszámot, de csak akkor, ha az adatok 730 napon (2 éven) belül az aktuális időpontban.

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

    Most lássuk, ha ezek a profilok működnek, ahogy gondoljuk, hogy kell!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Kód hozzáadása a nézethez a profilok összehasonlításához

1. Nyissa meg az index.cshtml &lt;fájlt, és cserélje le a törzsrészt&gt; a következő kódra.

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

2. Nyissa meg a SearchData.cs fájlt, és cserélje le a **SearchData** osztályt a következő kódra.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Kód hozzáadása a vezérlőhöz pontozási profil megadásához

1. Nyissa meg az otthoni vezérlőfájlt. Adja hozzá a következő **tanéven** a következő utasítást (a listák létrehozásának elősegítésére).

    ```cs
    using System.Linq;
    ```

2.  Ebben a példában szükségünk van az **Index** kezdeti hívására, hogy egy kicsit többet tegyünk, mint hogy visszaadjuk a kezdeti nézetet. A módszer most legfeljebb 20 kényelmi megjelenítést keres a nézetben.

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

3. Két privát módszerre van szükségünk, hogy az egyes dimenziókat ideiglenes tárolásra mentsük, és helyreállítsuk őket az ideiglenes tárolásból, és feltöltsünk egy modellt.

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

4. Szükség esetén be kell állítanunk az **OrderBy** és **a ScoringProfile** paramétereket. Cserélje le a meglévő **Index(SearchData modell)** metódust az alábbiakra.

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

    Olvassa el az egyes **kapcsolókhoz** fűzött megjegyzéseket.

5. Nem kell módosítani a **Következő** műveletet, ha több tulajdonság alapján történő rendezési sorrendtel végezte el az előző szakasz további kódját.

### <a name="run-and-test-the-app"></a>Az alkalmazás futtatása és tesztelése

1. Futtassa az alkalmazást. A nézetben teljes körű kényelemnek kell lennie.

2. A megrendeléshez a "Numerikus értékelés" kiválasztásával megkaphatja a numerikus rendelést, amelyet már végrehajtott ebben a bemutatóban, a felújításdátuma pedig az azonos minősítésű szállodák között dönt.

![A "strand" megrendelése a minősítés alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Most próbálja ki a "A kényelem" profilt. Készítsen különböző kényelmi szolgáltatásokat, és ellenőrizze, hogy az ilyen felszereltséggel rendelkező szállodák at előléptetik-e az eredménylistán.

![A "strand" megrendelése profil alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Próbálja ki a "Felújított dátum / Rating profil", hogy ha kapsz, amit vársz. Csak a közelmúltban felújított szállodák nak kell _frissességet szerezniük._

### <a name="resources"></a>További források

További információt a Következő pontozási profilok hozzáadása az Azure Cognitive Search indexhez című [témakörben talál.](https://docs.microsoft.com/azure/search/index-add-scoring-profiles)

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe a következő elvihető ételek ebből a projektből:

* A felhasználók elvárják, hogy a keresési eredményeket megrendeljék, a legrelevánsabbelőször.
* Az adatokat úgy kell felépíteni, hogy a rendelés egyszerű legyen. Nem tudtuk rendezni a "legolcsóbb" első könnyen, mivel az adatok nem strukturált, hogy lehetővé teszi a megrendelés nélkül kell tenni anélkül, hogy további kódot.
* Nem lehet több szinten a sorrend, különbséget tenni az eredmények, amelyek azonos értékű, magasabb szintű rendelés.
* Természetes, hogy bizonyos eredményeket növekvő sorrendben kell megrendelni (mondjuk egy ponttól távol), és néhányat csökkenő sorrendben (mondjuk a vendég értékelése).
* Pontozási profilok definiálhatók, ha a numerikus összehasonlítások nem állnak rendelkezésre, vagy nem elég okos, egy adatkészlethez. Az egyes eredmények pontozása segít az eredmények intelligens megrendeléséhez és megjelenítéséhez.

## <a name="next-steps"></a>További lépések

Befejezte a C# oktatóanyagok sorozatát – értékes ismereteket kellett volna szereznie az Azure Cognitive Search API-król.

További referenciákért és oktatóanyagokért érdemes megtekinteni a [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure)vagy az Azure Cognitive Search dokumentációjában található egyéb oktatóanyagok [böngészését.](https://docs.microsoft.com/azure/search/)
