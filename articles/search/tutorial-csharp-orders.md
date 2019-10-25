---
title: C#oktatóanyag az eredmények megrendeléséről
titleSuffix: Azure Cognitive Search
description: Ez az oktatóanyag az "keresési eredmények tördelése – Azure Cognitive Search" projektre épít a keresési eredmények rendezésének hozzáadásához. Megtudhatja, hogyan rendezheti az eredményeket egy elsődleges tulajdonságra, valamint az azonos elsődleges tulajdonsággal rendelkező eredmények esetében, hogyan rendelhet hozzá eredményeket egy másodlagos tulajdonsághoz. Végezetül megtudhatja, hogyan rendezheti az eredményeket egy pontozási profil alapján.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 8d0c8e2a4467fe56cc0633a7d501af0c6aeed22a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794055"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C#Oktatóanyag: az eredmények rendezése – Azure Cognitive Search

Egészen addig, amíg ez a pont nem szerepel az oktatóanyagokban, a rendszer visszaadja az eredményeket, és megjeleníti az alapértelmezett sorrendet. Ez lehet az a sorrend, amelyben az adatok találhatók, vagy esetleg egy alapértelmezett _pontozási profil_ lett meghatározva, amelyet a rendszer akkor használ, ha nincs megadva rendezési paraméter. Ebben az oktatóanyagban bemutatjuk, hogyan lehet az eredményeket egy elsődleges tulajdonság alapján megrendelni, majd az azonos elsődleges tulajdonsággal rendelkező eredmények esetében a kijelölés sorrendjét egy másodlagos tulajdonsághoz. A numerikus értékek alapján történő rendezés alternatívájaként az utolsó példa azt szemlélteti, hogyan lehet sorrendet rendelni egy egyéni pontozási profil alapján. Az _összetett típusok_megjelenítését is mélyebben fogjuk bemutatni.

Ha a visszaadott eredményeket egyszerűen össze szeretné hasonlítani, a projekt az [ C# oktatóanyag: keresési eredmények tördelése – Azure Cognitive Search](tutorial-csharp-paging.md) oktatóanyagban létrehozott végtelen görgetési projektre épül.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Eredmények megrendelése egy tulajdonság alapján
> * Eredmények megrendelése több tulajdonság alapján
> * Eredmények szűrése földrajzi ponttól mért távolság alapján
> * Eredmények megrendelése pontozási profil alapján

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

Az oktatóanyag végtelen görgethető verziója [ C# : keresési eredmények tördelése – az Azure Cognitive Search-](tutorial-csharp-paging.md) projekt felhasználható. A projekt lehet saját verziója, vagy a GitHubról telepítheti: [első alkalmazás létrehozása](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Eredmények megrendelése egy tulajdonság alapján

Ha egy tulajdonság alapján rendelünk eredményeket, mondjuk a szállodai minősítést, nem csak a megrendelt eredményeket szeretnénk megerősíteni, azt is szeretnénk, hogy a megrendelés helyes legyen. Más szóval, ha minősítést rendelünk, a minősítést a nézetben kell megjeleníteni.

Ebben az oktatóanyagban egy kicsit többet is megadunk az eredmények, a legolcsóbb szobaár és a legdrágább szobaárak megjelenítéséhez minden egyes szállodában. A rendezés során a rendszer az értékeket is felveszi, hogy megbizonyosodjon róla, hogy a megrendelést mi is mutatja a nézetben.

Nem szükséges módosítani a modelleket a rendezés engedélyezéséhez. A nézetet és a vezérlőt frissíteni kell. Először nyissa meg a Kezdőlap vezérlőt.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Adja hozzá a OrderBy tulajdonságot a keresési paraméterekhez

1. Ahhoz, hogy az eredményeket egyetlen numerikus tulajdonság alapján kell megrendelni, a **OrderBy** paramétert a tulajdonság nevére kell beállítani. Az **index (SearchData Model)** metódusban adja hozzá a következő sort a keresési paraméterekhez.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Az alapértelmezett sorrend növekvő, de a tulajdonsághoz hozzáadhatja az **Asc** értéket, hogy ezt törölje. A csökkenő sorrendet a **desc**hozzáadásával határozhatja meg.

2. Most futtassa az alkalmazást, és adja meg a gyakori keresési kifejezéseket. Előfordulhat, hogy az eredmények nem a megfelelő sorrendben jelennek meg, mert nem a fejlesztő, hanem nem a felhasználó, egyszerűen ellenőrizheti az eredményeket!

3. Tegyük fel, hogy egyértelművé teszi, hogy az eredmények a minősítés alapján vannak rendezve. Először cserélje le a **Box1** és a **rajzolása 2** osztályt a Hotels. CSS fájlra a következő osztályokkal (ezek az osztályok az oktatóanyaghoz szükséges összes újdonság).

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
    >A böngészők általában a CSS-fájlokat gyorsítótárazzák, és ez egy régi CSS-fájl használatát eredményezi, és a módosítások figyelmen kívül lesznek hagyva. Ez egy jó módja annak, hogy egy lekérdezési karakterláncot adjon hozzá egy Version paraméterrel a hivatkozáshoz. Példa:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Frissítse a verziószámot, ha úgy gondolja, hogy a böngésző egy régi CSS-fájlt használ.

4. Adja hozzá a **minősítés** tulajdonságot a **Select** paraméterhez az **index (SearchData Model)** metódusban.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Nyissa meg a nézetet (index. cshtml), és cserélje le a renderelési ciklust ( **&lt;!--jelenítse meg a szállodai adatértékeket.--&gt;** ) a következő kóddal.

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

6. A minősítésnek mind az első megjelenített oldalon, mind a további, a végtelen görgetéssel meghívott lapokon elérhetőnek kell lennie. Az utóbbi két helyzetben frissítenie kell a vezérlő **következő** műveletét és a nézet **görgetett** függvényét. A vezérlőtől kezdődően módosítsa a **következő** metódust a következő kódra. Ez a kód létrehozza és közli a minősítési szöveget.

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

7. Most frissítse a nézet **görgetett** függvényét a minősítés szövegének megjelenítéséhez.

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

8. Most futtassa újra az alkalmazást. Keressen az általános kifejezésre, például a "WiFi" kifejezésre, és győződjön meg róla, hogy az eredmények sorrendje a Hotel minősítésének csökkenő sorrendje.

    ![Megrendelés minősítés alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Megfigyelheti, hogy több szálláshely azonos minősítéssel rendelkezik, és így a megjelenésük a képernyőn megjelenő sorrendben is megkezdődik, ami tetszőleges.

    Mielőtt megnézzük a második szintű megrendelés hozzáadását, vegyünk fel egy kódot a szobaárak megjelenítéséhez. Ezt a kódot úgy vesszük fel, hogy az adatok kinyerését _összetett típusból_is megmutassa, és így a díjszabás alapján is megbeszéljük a sorrendet (az első legolcsóbbat).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Adja hozzá a szobaárak tartományát a nézethez

1. Adja hozzá a legolcsóbb és legdrágább szobaárat tartalmazó tulajdonságokat a Hotel.cs-modellhez.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Az **index (SearchData Model)** művelet végén lévő szobaárak kiszámításához a Kezdőlap vezérlőben. Az ideiglenes adattárolást követően adja hozzá a számításokat.

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

3. Adja hozzá a **Rooms** tulajdonságot a vezérlő **index (SearchData modell)** műveleti metódusának **Select** paraméteréhez.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Módosítsa a renderelési ciklust a nézetben az eredmények első oldalának díjszabási tartományának megjelenítéséhez.

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

5. Változtassa meg a **következő** metódust a Kezdőlap vezérlőn, hogy az eredmény további oldalain kommunikáljon a ráta tartományával.

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

6. Frissítse a **görgethető** függvényt a nézetben a szobaárak szövegének kezeléséhez.

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

7. Futtassa az alkalmazást, és ellenőrizze, hogy megjelenik-e a szobaárak tartománya.

    ![A szobák díjszabási tartományának megjelenítése](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A keresési paraméterek **OrderBy** tulajdonsága nem fogad el olyan bejegyzést, mint például a **Rooms. BaseRate** , hogy a legolcsóbb szobaárat adja meg, még akkor is, ha a szobák már a díjszabás szerint vannak rendezve. Ebben az esetben a szobák nem a díjszabás szerint vannak rendezve. Ahhoz, hogy megjelenjenek a megadott adatkészletben található szállodák a szobaár alapján rendezve, rendeznie kell az eredményeket a saját vezérlőben, majd az eredményeket a kívánt sorrendben kell elküldeni a nézetnek.

## <a name="order-results-based-on-multiple-values"></a>Eredmények megrendelése több érték alapján

A kérdés most az, hogy hogyan lehet különbséget tenni a szállodák között ugyanazzal a minősítéssel. Az egyik jó módszer a Hotel legutóbbi felújítása alapján. Más szóval, a közelmúltban a szálloda fel lett újítva, annál nagyobb lesz a szálloda az eredmények között.

1. A második szintű megrendelés hozzáadásához módosítsa a **OrderBy** , és **válassza** az **index (SearchData Model)** metódus tulajdonságok elemét, hogy tartalmazza a **"lastrenovationdate** tulajdonságot.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >A **OrderBy** listájában tetszőleges számú tulajdonság adható meg. Ha a hotelek azonos minősítési és felújítási dátummal rendelkeztek, egy harmadik tulajdonságot is meg lehet adni a közöttük való különbségtételhez.

2. Újra meg kell látni a felújítási dátumot a nézetben, csak azért, hogy az adott megrendelés helyes legyen. Olyan dolgok esetében, mint a felújítás, valószínűleg csak az év szükséges. Módosítsa a megjelenítési hurkot a nézetben a következő kódra.

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

3. Változtassa meg a **következő** metódust a fővezérlőn a legutóbbi felújítás év összetevőjének továbbításához.

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

4. A felújítási szöveg megjelenítéséhez módosítsa a nézet **görgetett** függvényét.

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

5. Futtassa az alkalmazást. Keressen egy általános kifejezésre, például a "pool" vagy a "View" kifejezésre, és ellenőrizze, hogy az azonos minősítéssel rendelkező szállodák mostantól csökkenő sorrendben jelennek-e meg a felújítás dátuma alapján.

    ![Megrendelés a felújítás napján](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Eredmények szűrése földrajzi ponttól mért távolság alapján

A minősítés és a felújítás időpontja olyan tulajdonságok, amelyek a legjobban csökkenő sorrendben jelennek meg. Egy ABC-lista a növekvő sorrend megfelelő használatát mutatja be (például ha csak egy **OrderBy** tulajdonság volt, és a **pezsgő** értékre van állítva, akkor egy ABC-sorrend jelenik meg). A mintaadatok esetében azonban a földrajzi ponttól való távolság megfelelőbb lenne.

Az eredmények földrajzi távolság alapján történő megjelenítéséhez több lépésre van szükség.

1. Kiszűri az összes olyan szállodát, amely a megadott sugaron kívül esik az adott pontról, és a hosszúság, a szélesség és a sugár paraméterrel megadhat egy szűrőt. A hosszúságot a pont függvény kapja meg. A sugár kilométerben van.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. A fenti szűrő _nem_ rendezi az eredményeket a távolság alapján, csak eltávolítja a kiugró értékeket. Az eredmények rendezéséhez adjon meg egy **OrderBy** -beállítást, amely megadja a geoDistance metódust.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Bár az Azure Cognitive Search az eredményeket egy távolsági szűrő használatával adta vissza, a rendszer _nem_ adja vissza az adatmennyiség és a megadott pont közötti számított távolságot. Számítsa ki újra ezt az értéket a nézetből vagy vezérlőből, ha az eredmények között szeretné megjeleníteni.

    A következő kód a két lat/Lon pont közötti távolságot számítja ki.

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

4. Most össze kell kötnie ezeket a fogalmakat. Ezek a kódrészletek azonban a jelen oktatóanyagban is elérhetők, így a térképes alkalmazások létrehozása az olvasó számára is megmarad. Ahhoz, hogy ez a példa továbbra is megtörténjen, vegye fontolóra egy város nevének beírását egy sugárral, vagy egy pont megkeresését a térképen, és kiválaszthatja a sugarat. Ezen beállítások további vizsgálatához tekintse meg a következő forrásokat:

* [Az Azure Maps dokumentációja](https://docs.microsoft.com/azure/azure-maps/)
* [Címek keresése a Azure Maps Search szolgáltatással](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Eredmények megrendelése pontozási profil alapján

Az oktatóanyagban szereplő példák azt mutatják be, hogyan lehet megrendelni a numerikus értékeket (a minősítési, a felújítási dátumot, _a földrajzi_ távolságot). Egyes keresések és egyes adatértékek azonban nem alkalmasak arra, hogy egy ilyen egyszerű összehasonlítást végezzenek két adatelem között. Az Azure Cognitive Search a _pontozás_fogalmát is tartalmazza. A _pontozási profilok_ megadhatók olyan adathalmazokhoz, amelyek összetettebb és minőségi összehasonlítást is biztosítanak, ami a legértékesebb, ha a szöveges alapú adatmennyiséget a következőnek kell megjelennie.

A pontozási profilokat a felhasználók nem határozzák meg, de általában egy adathalmaz rendszergazdái. Több pontozási profil is be lett állítva a szállodákban tárolt értékekre. Nézzük meg, hogyan határozzák meg a pontozási profilt, majd próbáljon meg kódot írni a kereséshez.

### <a name="how-scoring-profiles-are-defined"></a>Pontozási profilok meghatározása

Lássunk három példát a pontozási profilokra, és gondolja át, _Hogyan befolyásolják az_ egyes szempontok az eredmények sorrendjét. Alkalmazás-fejlesztőként ezeket a profilokat nem kell megírnia, hanem az adatadminisztrátor írta, de hasznos lehet a szintaxis megkeresése.

1. Ez a Hotel adatkészletének alapértelmezett pontozási profilja, amelyet akkor kell használni, ha nem ad meg **OrderBy** vagy **ScoringProfile** paramétert. Ez a profil növeli a _pontszámot_ egy adott szállodában, ha a keresett szöveg szerepel a Hotel neve, leírása vagy címkék listájában (kényelmi szolgáltatás). Figyelje meg, hogy a pontozás súlyozása bizonyos mezőknél. Ha a keresett szöveg egy másik mezőben jelenik meg, amely nem szerepel az alábbi listában, akkor az 1. súlyozással fog rendelkezni. Nyilvánvaló, hogy minél magasabb a pontszám, annál korábbi eredmény jelenik meg a nézetben.

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

2. A következő pontozási profil jelentős mértékben növeli a pontszámot, ha egy megadott paraméter egy vagy több címkét tartalmaz (amely a "kényelmi" lehetőséget hívja meg). A profil legfontosabb pontja, hogy _egy paramétert kell megadni_ , amely szöveget tartalmaz. Ha a paraméter üres vagy nincs megadva, a rendszer hibát jelez.
 
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

3. Ebben a harmadik példában a minősítés jelentős lökést ad a pontszámnak. A legutóbb felújított dátum is fellendíti a pontszámot, de csak akkor, ha az adatok az aktuális dátum 730 napon (2 év) belül esik.

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

    Most nézzük meg, hogy ezek a profilok ugyanúgy működnek-e, mint gondolnánk!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Kód hozzáadása a nézethez a profilok összehasonlításához

1. Nyissa meg az index. cshtml fájlt, és cserélje le a &lt;Body&gt; szakaszt a következő kódra.

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

3. Nyissa meg a Hotels. css fájlt, és adja hozzá a következő HTML-osztályokat.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Kód hozzáadása a vezérlőhöz pontozási Profil megadásához

1. Nyissa meg a Kezdőlap vezérlő fájlját. Adja hozzá a következő **using** utasítást (a listák létrehozásához nyújtandó támogatáshoz).

    ```cs
    using System.Linq;
    ```

2.  Ebben a példában az **indexhez** a kezdeti hívást egy kicsit nagyobbra kell tenni, mint a kezdeti nézet visszaadása. A metódus most legfeljebb 20 kényelmi lehetőséget keres a nézetben való megjelenítéshez.

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

3. Két privát módszerre van szükségünk az aspektusok ideiglenes tárhelyre való mentéséhez, valamint az ideiglenes tárolóból történő helyreállításához és a modell feltöltéséhez.

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

4. Szükség szerint be kell állítania a **OrderBy** és a **ScoringProfile** paramétereket. Cserélje le a meglévő **index (SearchData Model)** metódust a következőre.

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

    Olvassa végig az egyes **kapcsolók** kiválasztásának megjegyzéseit.

5. A **következő** művelet módosítása nem szükséges, ha az előző szakaszhoz tartozó további kódokat több tulajdonság alapján hajtotta végre.

### <a name="run-and-test-the-app"></a>Az alkalmazás futtatása és tesztelése

1. Futtassa az alkalmazást. A nézetben a szolgáltatások teljes készletét kell megtekinteni.

2. A "numerikus minősítéssel" lehetőség kiválasztásával megadhatja az ebben az oktatóanyagban már megvalósított numerikus sorrendet, a felújítási dátumot pedig az egyenlő minősítésű szállodák között kell eldöntenie.

![A "Beach" rendezése minősítés alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Most próbálja ki a "by kényelmi" profilt. Tegye elérhetővé a különböző kényelmi szolgáltatásokat, és ellenőrizze, hogy az adott szolgáltatásokkal rendelkező szállodák támogatják-e az eredmények listáját.

![A "Beach" rendezése profil alapján](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Próbálja ki a "megújított dátum/minősítési profil" lehetőséget, hogy megtekintse, mire számíthat. Csak a közelmúltban felújított szállodáknak kell megszerezniük a _frissesség_ növelését.

### <a name="resources"></a>Segédanyagok és eszközök

További információt az alábbi [pontozási profilok hozzáadása Azure Cognitive Search indexhez](https://docs.microsoft.com/azure/search/index-add-scoring-profiles)című témakörben talál.

## <a name="takeaways"></a>Legfontosabb ismeretek

Vegye figyelembe az alábbi elvihetőket a projektből:

* A felhasználók a keresési eredmények sorrendjét fogják várni, ami a legfontosabb.
* Az adatigényt strukturálni kell, hogy a rendezés egyszerűen megtörténjen. Először nem tudtuk rendezni a "legolcsóbb" kifejezést, mert az adathalmaz nem strukturált, hogy a megrendelés további kód nélkül legyen végrehajtva.
* A rendezés számos szinten elvégezhető, így megkülönböztethető az olyan eredmények között, amelyek értéke magasabb a megrendelésnél.
* Természetes, hogy egyes eredmények növekvő sorrendben legyenek rendezve (azaz távolság egy ponttól), és néhány csökkenő sorrendben (azaz a vendég minősítése).
* A pontozási profilok meghatározhatók, ha a numerikus összehasonlítások nem érhetők el, vagy nem elég intelligensek egy adatkészlethez. Az egyes eredmények pontozásával intelligens módon rendezheti és jelenítheti meg az eredményeket.

## <a name="next-steps"></a>Következő lépések

Elvégezte ezt az C# oktatóanyag-sorozatot – értékes ismereteket szerzett az Azure Cognitive Search API-król.

További tudnivalókat és oktatóanyagokat az [Azure Cognitive Search dokumentációjában találhat](https://docs.microsoft.com/azure/search/) [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure)vagy más oktatóanyagokat.
