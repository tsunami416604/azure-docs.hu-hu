---
title: Visual keresési mobilalkalmazás oktatóanyag
description: Nyissa meg a forrás C#-alkalmazás megvalósító visual keresési kognitív szolgáltatások számítógép Látástechnológiai API, a Bing webes keresés API és a Xamarin.Forms-platformok keretrendszerrel.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347627"
---
# <a name="visual-search-mobile-app-tutorial"></a>Visual keresési mobilalkalmazás oktatóanyag

## <a name="introduction"></a>Bevezetés  
Ez az oktatóanyag ismerteti a [számítógép Látástechnológiai API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) és [Bing webes keresés API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) végpontok és mutat be, hogyan felhasználásuk az alapszintű visual keresőalkalmazás létrehozásához [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/).  A teljes Ez az oktatóanyag témakörei a következők: 

> [!div class="checklist"]
> * Xamarin.Forms-alkalmazások fejlesztéséhez a Visual Studio beállítása
> * Használja a [Xamarin Media beépülő modul](https://github.com/jamesmontemagno/MediaPlugin) rögzítéséhez és lemezkép adatok importálása
> * A képet a számítógépes Látástechnológiai API-k szöveg elemzése
> * Keresési kérelmeket küld a webes Bing keresési API
> * Mindkét API-k a JSON-válaszainak elemzése [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (LINQ és modell objektum deszerializálása)
> * A vizuális keresés platformfüggetlen Xamarin.Forms felhasználói felület létrehozása 

## <a name="prerequisites"></a>Előfeltételek

Ebben a példában a Xamarin.Forms segítségével fejlesztették [Visual Studio 2017](https://www.visualstudio.com/downloads/). A közösségi Edition a Visual Studio 2017 is használható. Xamarin a Visual Studio használatával kapcsolatos további információkért lásd: a [Xamarin dokumentáció](https://developer.xamarin.com/guides/cross-platform/getting_started/).

Ez a minta lehetővé teszi, hogy a következő NuGet-csomagok használata:

> [!div class="checklist"]
> * [Xamarin Media beépülő modul](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

Az alkalmazás a következő kognitív szolgáltatások API-kat használ:

> [!div class="checklist"]
> * [Bing webes keresés API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

30 napos próba kulcsok beszerzése ezen API-k, lásd: [kognitív szolgáltatások próbálja](https://azure.microsoft.com/try/cognitive-services/). Kereskedelmi célra kulcsok beszerzésével kapcsolatos további információkért lásd: [árazás](https://azure.microsoft.com/pricing/calculator/).

## <a name="setup-for-development"></a>A telepítő fejlesztési  

### <a name="installing-xamarin-on-windows"></a>Xamarin Windows telepítése
A telepített Visual Studio 2017 bármely kiadása, nyissa meg a Visual Studio telepítő, válassza ki a Visual Studio telepítési társított Hamburg menüt, és válassza **módosítás**.  

![A Visual Studio-telepítő](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

Görgessen le & játék és engedélyezése **Mobile fejlesztése a .NET**, ha az még nincs engedélyezve.

![A kiválasztott Xamarin.Forms Visual Studio-telepítő](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

Végül kattintson **módosítás** a a jobb alsó sarkában az ablakot, és várja meg a Xamarin telepítéséhez.

### <a name="installing-xamarin-on-macos"></a>MacOS Xamarin telepítése
Xamarin származik a Visual Studio előcsomagolt for Mac csomagokkal. Telepítés nem kell feltétlenül szükség.

## <a name="building-and-running-the-app"></a>Kialakításához és futtatásához az alkalmazás

A Visual Studio megoldás fájl *(.sln)* a vizuális keresés alkalmazás letölthető [Visual fájlkeresés alkalmazás kognitív szolgáltatásokkal](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/). Töltse le a webböngésző segítségével ZIP-archívumot, klónozza a munkaállomására a Githubból, vagy töltse le a Visual Studio használatával.

A minta használata elindításához `VisualSearchApp.sln` a Visual Studióban.  A szükséges összetevők inicializálása eltarthat egy ideig.

Az alkalmazás futtatásához szükséges két külső szalagtárat: **Json.NET** és a **Xamarin Media beépülő modul**. A szalagtárak jobbra a Visual Studióban a NuGet Package Manager telepítése. Válassza ki **eszközök > NuGet-Csomagkezelő > kezelése NuGet csomagjainak a megoldás**, vagy kattintson a jobb gombbal a megoldás a Megoldáskezelőben, és válassza a **NuGet-csomagok kezelése** a helyi menüből.

A NuGet-ablakban, keresse meg és telepítse **Xamarin Media beépülő modul** (Xam.Plugin.Media) verzió 2.6.2 és **Json.NET** (Newtonsoft.Json) 10.0.3. Győződjön meg arról, jelölje be az összes projekt telepítésekor.

Építenie az alkalmazást az összes rendelkezésre álló platformra, nyomja meg az **Ctrl + Shift + B**, vagy kattintson a **Build** a menüszalag menü, és válassza **megoldás fordítása**.  Tekintse meg az IOS rendszerhez készült Windows rendszerről kód összeállításához és teszteléséhez [Ez az útmutató](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/) segítségét.

Az alkalmazás futtatása előtt ki kell választania egy cél konfigurációs, a Platform és a projektet.  Xamarin.Forms-alkalmazások natív kód lefordítani a Windows-, Android és iOS. Ez az oktatóanyag magában foglalja a Windows-verzióhoz, a minta példaként bemutató képernyőképeket láthat, de az összes verzió funkcionális szempontból egyenértékű. Az útmutatóban használt központi telepítési beállítások itt látható.  

![Az Androidos telefonnal fordítási konfigurálva a Visual Studio](./media/computer-vision-web-search-tutorial/configuration-selection.png)

Ha a felépítési folyamat sikeresen befejeződött, és egy célplatformot választotta, kattintson a **Start** gombra a eszköztár vagy nyomja le az **F5**. A Visual Studio telepíti a megoldás a célplatform, és elindítja azt.

A kulcsok hozzáadása lap jelenik meg. (Ezen a lapon definiált `AddKeysPage.xaml`.)  

![A kulcsok lap hozzáadása képe](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

Itt illessze be a számítógép stratégiai és a Bing webes keresés API kulcsokat. A számítógép Látástechnológiai API megköveteli, hogy válassza ki a kiszolgálót, amelyen a végpont található.

> [!TIP]
> E lap kihagyása, írja be az adatokat a megfelelő helyre `App.xaml.cs`. 

Az alkalmazás ellenőrzi a kulcsokat a lekérdezés tesztelése elvégzésével, majd vesz igénybe, hogy OCR kiválasztása lap (definiált `OcrSelectPage.xaml`).
   
![A OCR válassza lapjának képe](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

Ez a képernyő tetején dönthet úgy, hogy ismeri fel a kívánt szöveg nyomtatás vagy kézzel.

> [!TIP]
> Az elem, amelyből próbál ismeri fel a szint a lehető szöveg, és ellenőrizze, hogy azt egyenletesen a rendelkező nincs visszaverődések bekapcsolásával tárolásához. Azt, hogy kézzel OCR néha működik jobban parancsfájl betűtípusok vagy más "extra" szöveget talált.

Ezután kattintson **érvénybe fénykép** vagy **importálási fénykép** egy fénykép az eszközön a kamera használatával, vagy válassza ki az eszközön tárolt fénykép.

Egy fénykép venni, vagy a kiválasztott után a lemezképet a számítógép Látástechnológiai API lett átadva. A szavakat található lap (definiált `OcrResultsPage.xaml`) ismeri fel a kép szavak jeleníti meg.

![A OCR eredmények lapjának képe](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> A kép ezekkel az eredményekkel használtuk van, a tárházban lévő `SamplePhotos\TestImage.jpg`.

Kattintson a megfelelő elemre a szavakat található a lapon, a webes találatokat tartalmazó lap (`WebResultsPage.xaml`) jelenik meg, hogy bemutatja a Keresés eredménye Bing felső.

![A weblap eredmények képe](./media/computer-vision-web-search-tutorial/web-results-page.png)  
Végül válassza a webes eredmények lapon nyissa meg a hivatkozás egy beágyazott webes nézet egy elemet. (Vagy lépjen vissza válasszon egy másik eredménye.)

![A webes nézet lapjának képe](./media/computer-vision-web-search-tutorial/web-view-page.png)  

A lap kommunikálhat, bármely webböngészőben volna, vagy lépjen vissza a webes találatokat tartalmazó lap. 

## <a name="review-and-learn"></a>Tekintse át, és ismerje meg

Most, hogy a Visual keresési alkalmazást egy léptetéses venni most megismerkedhet pontosan hogyan használunk a két Microsoft kognitív szolgáltatások API-k.  Használ a minta kiindulási pontként saját alkalmazáshoz, vagy egyszerűen csak egy útmutató az API-k esetében, hogy az értékes bízná alkalmazás képernyő-által-képernyőn ellenőrizze, hogy pontosan hogyan működik.

### <a name="add-keys-page"></a>Kulcsok-weblap hozzáadása
A kulcsok hozzáadása lap felhasználói felületi meghatározott `AddKeysPage.xaml`, és meghatározott elsődleges logikája `AddKeysPage.xaml.cs`. Mivel a kulcsok tesztelési kérelem érvényesíti, az idő az érett kognitív Services végpontjainak használata a C# létrehozásához.  

Kommunikáció alapvető szerkezete van: 

1. Példányosítható *HttpResponseMessage* és *HttpClient* a következő helyről objektumokat *System.Net.Http*
2. Bármely kívánt fejléceket (definiált minden egyes végpont API-referencia) csatolása a *HttpClient* objektum
3. Az adatait, a szükséges paramétereket ad a végpont URI GET vagy POST kérés küldése
4. Győződjön meg arról, hogy sikeres volt-e a válasz
5. A válasz át további feldolgozás

A funkció, amely ellenőrzi a Bing keresési API-kulcs érvényességét `CheckBingSearchKey()`, itt látható.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

Egy hasonló függvény `CheckComputerVisionKey()`, a számítógép stratégiai kulcs ellenőrzésére szolgál.

### <a name="ocr-select-page"></a>OCR kiválasztása lap

A OCR kiválasztása lap (`OcrSelectPage.xaml`) két fontos szerepet tölt. Először meghatározza, hogy milyen típusú OCR a cél fénykép hajtja végre. Második lehetővé teszi, a felhasználó rögzítheti, vagy nyissa meg a feldolgozni kívánt lemezképet.

A második feladata a hagyományosan nehézkes platformfüggetlen alkalmazásokban, mert minden egyes platform különböző kód szükséges. A Xamarin Media beépülő modul kezeli a pár sornyi kód.

A következő függvényt egy példát a Xamarin Media beépülő modul fénykép rögzítési magában foglalja.  Azt hogy:

1. Győződjön meg arról, hogy az aktuális eszközön elérhető legyen-e a fényképezőgép
2. Hozható létre egy `StoreCameraMediaOptions` objektumot a rögzített lemezkép mentési helyének megadása
3. Rögzítsen egy rendszerképet, szerezzen be egy `MediaFile` kép adatot tartalmazó objektum
4. Bontsa ki a `MediaFile` be egy bájttömbben.
5. Térjen vissza a bájttömböt további feldolgozás

Itt `TakePhoto()`, a függvény a Xamarin Media beépülő modul által a fénykép rögzítése.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
A fénykép segédprogram works hasonló módon importálja. Mindkét adatot funkció található `OcrSelectPage.xaml.cs`. 
 
> [!NOTE]
> A kézzel OCR végpont csak kezelni tud a fényképek, amelyek 4 MB-nál kisebb. Fájlok méretéből adódó problémákat elkerülése érdekében csökkenteni a kép eredeti méretének 50 %-ára úgy, hogy a beállítás `PhotoSize = PhotoSize.Medium` a a `StoreCameraMediaOptions` objektum.  Ha az eszköz tart rendkívül magas színvonalú fényképek, és hibákat kap, próbálja meg `PhotoSize = PhotoSize.Small` helyette.

Ez a segédprogram függvényt használja egy *MediaFile* be egy bájttömböt.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>OCR találatokat tartalmazó lap

A OCR eredmények oldalát jeleníti meg a lemezkép használatával kinyert szöveg a **Json.NET** [SelectToken metódus](http://www.newtonsoft.com/json/help/html/SelectToken.htm).  A két OCR végpontok, hogy értékes, és beszéljék meg, mindkét kissé eltérően működik.  

Mivel a számítógép Látástechnológiai API csak néhány server helyeken, a végpont futásidőben dinamikusan kell kialakítani. A függvény `SetOcrLocation` (a statikus részének *AppConstants* osztály található `AppConstants.cs`) beállítja az URI-végpont helyét. A felhasználó kiválasztása a kulcsok hozzáadása lapon megfelel-e, vagy beállított értéket használja, `App.xaml.cs`. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

Megtudhatja, hogy ezek a API-kérelmek részletes bemutatása. A számítógép stratégiai OCR API képes a szöveget egy meghatározott nyelv értelmezése, de eredmények javításához angol szöveg keresése biztosítunk. Azt is észleli az USA tájolását API segítségével. A rögzített tájolás segítségével javíthatja az elemzési eredményeket, de a tájolás észlelési mobilalkalmazás hasznos lehet.

Ehhez a végponthoz, a rendelkezésre álló paraméterekről többet is megtudhat a [nyomtatás optikai API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc).  

A kézzel OCR API jelenleg még előzetes, és jelenleg csak angol szöveg együttműködni.  Ezért egyetlen paramétert jelenleg egy jelzőt, amely megadja, hogy minden elemezni kézzel írt szöveg-e. A kézzel OCR API egyaránt tudja értelmezni a gép nyomtatott és kézzel írt szöveg, de `handwriting=false` hozamok jobb eredmények nyomtatott szöveg. 

Mivel ez az alkalmazás angol nyelven, azt sikerült rendelkezik OCR csak kézzel ezt a mintát használja, és állítsa be a `handwriting` jelző megfelelően Mi a felhasználó azt ismerteti, hogy ismeri fel.  Mindkét végpont szemléltető célokra használtuk.  

Ehhez a végponthoz, a rendelkezésre álló paraméterekről többet is megtudhat a [kézzel optikai API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).

Most a Funkciók, amelyek e felületek vizsgáljuk meg.

`FetchPrintedWordList()` a nyomtató OCR végpont használatával képek nyomtatott szöveg elemzése. A HTTP-kérelem a következő a híváshoz, amely szerepel a kulcsok hozzáadása oldalon, a kulcs érvényesítéséhez hasonló struktúrával, de el kell küldeni egy fénykép. Egy fénykép mérete túl nagy, így helyett egy GET kérelmet kell használjuk a HTTP POST-kérelmet adjon át egy lekérdezési karakterlánc. Igazolnia kell a fénykép (amely létezik a memóriában, egy bájttömböt) kódolni be egy `ByteArrayContent` objektumra, és egy fejléc hozzáadását definiálása az adatok azért küldjük ezt az objektumot. 

Az elfogadható tartalomtípusokat olvashat a [számítógép Látástechnológiai API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> Vegye figyelembe, hogyan használunk a **Json.NET** [SelectToken metódus](http://www.newtonsoft.com/json/help/html/SelectToken.htm) szöveg kibontani a válasz objektum. `SelectToken` az ideális, ha igazolnia kell, hogy a JSON-választ, amely azt is akkor továbbítja következő függvény csak egy adott részét. A kód többi részének azt deszerializálni JSON válaszok definiált modell objektumokba `ModelObjects.cs`.

A legfontosabb különbség a nyomtató és a kézzel OCR kérés, hogy a nyomtató OCR szolgáltatás felismert formátumú szöveget adja vissza a HTTP-válasz részeként a kézzel OCR szolgáltatásnak van szüksége további kérelmet a adatainak megszerzése során. A kezdeti kézzel OCR kérelem adja vissza egy HTTP 202 állapota, amely csak jelzi, hogy feldolgozása megkezdődött. Ez a válasz egy végpontot, amely az ügyfélnek ellenőriznie kell az beszerzése a választ, a rendelkezésre álló tartalmazza. Lásd: `FetchHandwrittenWordList()` hogyan működik mindez megjelenítéséhez.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` a kézírás-felismerés karakterfelismerést második részét képezi. Azt a kinyert metaadatokat a kezdeti válasz URI pingeli vagy addig, amíg egy eredmény esetén, vagy a függvény túllépi az időkorlátot.  Fontos, hogy ez a funkció a saját szálon aszinkron módon neve. Ellenkező esetben ez a módszer akkor zárolja a felhasználói felület amíg feldolgozása befejeződött.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Webalkalmazás-találatokat tartalmazó lap
Miután a felhasználó kiválasztja a kívánt keresőkifejezést a OCR eredménye lapon jelenik meg, azt áthelyezése a webes találatokat tartalmazó lap.  Itt azt létrehozni egy [Bing webes keresés API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) kérelem, küldje el a szolgáltatási végpont és deszerializálni használatával JSON-választ a **Json.NET** [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) metódus.  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

A webes Bing keresési API akkor működik a legjobban, ha ad meg, hogy minél több információt arról, hogy a felhasználó érdemes is. Különösen szinte mindig használjon a `mkt` és `setLang` paramétereket (az angol itt meg) a felhasználó helye és a kívánt nyelvet azonosításához.

> [!NOTE]
> Egyszerű ahhoz, hogy ellenőrizze, hogy a forráskód lett megértse a Bing keresést a lekérdezés azt tartani.  Egy valós alkalmazás a következő fejlécek kell hozzá a HTTP-kérelem a jobb eredmények elérése érdekében. 
> * Felhasználói ügynök  
> * X-MSEdge-ClientID  
> * X-keresési-Ügyfélip  
> * X keresése  
>
> A fejléc értékeiről kapcsolatos részletesebb a [Bing webes keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)

## <a name="next-steps"></a>További lépések
Microsoft kognitív szolgáltatásokat nyújthatnak sok további, amelyek könnyen integrálja sikerült Ez az alkalmazás.  Például hogy a következőket teheti:

* Adja hozzá [Bing entitás keresési](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) révén a webes keresés eredménye
* A felcserélés [Bing egyéni keresés](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/) helyett, a Bing webes keresés
* Használja a [Bing kép keresési](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) insights képes további információ a rögzített lemezképet, és a weben találhatók hasonló lemezképek kép
* Alkalmaz [Bing helyesírás-ellenőrzés](https://azure.microsoft.com/services/cognitive-services/spell-check/) további az elemzett szöveg minőségének javítása érdekében
* Integrálható a [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) különböző nyelveken a kibontott szöveg
* Az egyéb szolgáltatások kombinációs a [kognitív szolgáltatások portálja](https://azure.microsoft.com/services/cognitive-services/); a égbolt tartozó korlát!
