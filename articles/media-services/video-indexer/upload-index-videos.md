---
title: 'Példa: Videók feltöltése és indexelése a Video Indexerrel'
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan használhat API-kat a videók feltöltésére és indexelésére a Video Indexerrel.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/13/2020
ms.author: juliako
ms.openlocfilehash: b0a4f390a3a897d14adc2944195b0c51148de495
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209273"
---
# <a name="upload-and-index-your-videos"></a>Videók feltöltése és indexelése  

Videók Video Indexer API-val való feltöltésekor a következő feltöltési lehetőségek közül választhat: 

* videó feltöltése egy URL-címről (előnyben részesített),
* a videofájl elküldése byte-tömbként a kérelem törzsében
* Meglévő Azure Media Services eszköz használata az [eszköz azonosítójának](https://docs.microsoft.com/azure/media-services/latest/assets-concept) megadásával (csak a fizetett fiókok esetében támogatott).

A videó feltöltése után Video Indexer (opcionálisan) kódolja a videót (a cikkben ismertetett módon). A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős megoldással olyan Video Indexer fiókot hozhat létre, amely az [Azure-előfizetéshez és egy Azure Media Services-fiókhoz csatlakozik](connect-to-azure.md). Ön az indexelt perceket és a Media Accounttal kapcsolatos díjakat fizeti ki. 

A cikk bemutatja, hogyan tölthet fel és indexelheti a videókat a következő beállításokkal:

* [A Video Indexer webhely](#website) 
* [A Video Indexer API-k](#apis)

## <a name="uploading-considerations-and-limitations"></a>Szempontok és korlátozások feltöltése
 
- A videó neve nem lehet nagyobb, mint 80 karakter.
- Ha a videót az URL-cím alapján tölti fel (előnyben részesített), a végpontot a TLS 1,2-as (vagy újabb) titkosítással kell védeni.
- A feltöltési méret és az URL-cím beállítás a 30 GB-ra van korlátozva.
- A kérelem URL-címének hossza legfeljebb 6144 karakter hosszúságú lehet, ahol a lekérdezési karakterlánc URL-címe legfeljebb 4096 karakter hosszúságú lehet.
- A byte Array kapcsolóval rendelkező feltöltési méret 2 GB-ra van korlátozva.
- A bájtos tömb beállítása 30 percnél hosszabb időt vesz igénybe.
- A `videoURL` paraméterben megadott URL-címet kódolni kell.
- Az indexelési Media Servicesi eszközök ugyanazzal a korlátozással rendelkeznek, mint az URL-cím indexelése.
- A Video Indexer legfeljebb 4 órát tartalmaz egyetlen fájlra vonatkozóan.
- Az URL-címnek elérhetőnek kell lennie (például egy nyilvános URL-cím). 

    Ha ez egy privát URL-cím, a hozzáférési jogkivonatot meg kell adni a kérelemben.
- Az URL-címnek egy érvényes médiafájlra kell mutatnia, nem pedig egy weboldalra, például a `www.youtube.com` lapra mutató hivatkozást.
- Fizetős fiók esetén percenként akár 50-es filmeket is feltölthet, és egy próbaverziós fiókban akár 5 filmet is beállíthat percenként.

> [!Tip]
> Javasoljuk, hogy a .NET-keretrendszer 4.6.2-es vagy újabb verzióját használja, mivel a régebbi verziók nem a TLS 1.2-t használják alapértelmezés szerint.
>
> Ha régebbi verziót kell használnia, adjon hozzá egy sort a kódban a REST API-hívás indítása előtt:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>A Video Indexer támogatott fájlformátumai

A Video Indexer használatával használható fájlformátumok listáját a [bemeneti tároló/fájlformátumok](../latest/media-encoder-standard-formats.md#input-containerfile-formats) című cikkben találja.

## <a name="a-idwebsiteupload-and-index-a-video-using-the-video-indexer-website"></a>videó feltöltése és indexelése a Video Indexer webhellyel <a id="website"/>

> [!NOTE]
> A videó neve nem lehet nagyobb, mint 80 karakter.

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
2. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    ![Feltöltés](./media/video-indexer-get-started/video-indexer-upload.png)

    A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót.

    ![Feltöltve](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Ha a Video Indexer befejezte az elemzést, kapni fog egy értesítést, benne a videóra mutató hivatkozással és a videóban talált tartalom rövid leírásával. Például: személyek, témák, OCR-ek.

## <a name="a-idapisupload-and-index-with-api"></a>Feltöltés és indexelés <a id="apis"/>API-val

A [videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API-val feltöltheti és indexelheti a videókat egy URL-cím alapján. Az alábbi mintakód tartalmazza a megjegyzésből vett kódot, amely bemutatja, hogyan tölthető fel a bájtok tömbje. 

### <a name="configurations-and-params"></a>Konfigurációk és paraméterek

Ez a szakasz ismertet néhány választható paramétert, és leírja, hogy mikor érdemes beállítani őket.

#### <a name="externalid"></a>externalID 

Ezzel a paraméterrel megadhat egy azonosítót, amely társítva lesz a videóhoz. Az azonosítót a külső „Video Content Management” (VCM) rendszer-integrációra is alkalmazni lehet. A Video Indexer portálon található videók a megadott külső azonosító használatával kereshetők.

#### <a name="callbackurl"></a>callbackUrl

Egy URL-cím, amely az ügyfél (POST-kérelem használatával) értesítésére szolgál az alábbi eseményekről:

- Az indexelési állapot változása: 
    - Tulajdonságok    
    
        |Name (Név)|Leírás|
        |---|---|
        |id|A videó azonosítója|
        |state|A videó állapota|  
    - Például: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- A videóban azonosított személy:
  - Tulajdonságok
    
      |Name (Név)|Leírás|
      |---|---|
      |id| A videó azonosítója|
      |faceId|A videó indexében megjelenő Arcfelismerés|
      |knownPersonId|Az arc-modellen belül egyedi személy azonosítója|
      |personName|A személy neve|
        
    - Például: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

##### <a name="notes"></a>Megjegyzések

- Video Indexer az eredeti URL-címben megadott meglévő paramétereket adja vissza.
- A megadott URL-címet kódolni kell.

#### <a name="indexingpreset"></a>indexingPreset

Akkor használja ezt a paramétert, ha a nyers vagy külső felvételek háttérzajt tartalmaznak. Ez a paraméter az indexelési folyamat konfigurálására szolgál. A következő értékeket adhatja meg:

- `AudioOnly` – Indexelés és elemzések kinyerése csak audiotartalmak használatával (videotartalmak figyelmen kívül hagyása)
- `VideoOnly` – az elemzések indexelése és kinyerése csak videó használatával (hang figyelmen kívül hagyása)
- `Default` – Indexelés és elemzések kinyerése audio- és videotartalmak használatával
- `DefaultWithNoiseReduction` – Indexelés és elemzések kinyerése audio- és videotartalmakból, és zajcsökkentő algoritmusok alkalmazása az audiostreamen

Az árat a kiválasztott indexelési lehetőség határozza meg.  

#### <a name="priority"></a>priority

A videók indexelése Video Indexer a prioritásuk szerint történik. Az index prioritásának megadásához használja a **priority** paramétert. A következő értékek érvényesek: **alacsony**, **normál** (alapértelmezett) és **magas**.

A **priority** paraméter csak a fizetős fiókok esetében támogatott.

#### <a name="streamingpreset"></a>streamingPreset

A videó feltöltése után a Video Indexer opcionálisan kódolja a videót. Ezután továbblép a videó indexelésére és elemzésére. Amikor a Video Indexer végzett az elemzéssel, kapni fog egy értesítést, benne a videó azonosítójával.  

A [Videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy a [Videó újraindexelése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API használatakor a `streamingPreset` az egyik választható paraméter. Ha a `streamingPreset` paramétert `Default`, `SingleBitrate` vagy `AdaptiveBitrate` értékre állítja, a kódolási folyamat aktiválódik. Az indexelési és a kódolási feladatok befejezése után a rendszer közzéteszi a videót, amely streamelhető is. A videó streameléséhez használt streamvégpontnak **Futó** állapotban kell lennie.

Az indexelési és kódolási feladatok futtatásához a [Video Indexer-fiókjához csatlakoztatott Azure Media Services-fióknak](connect-to-azure.md) fenntartott egységekre van szüksége. További információért lásd a [médiafeldolgozás skálázását](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) ismertető cikket. Mivel ezek nagy számításigényű feladatok, határozottan ajánlott az S3-as egységtípus. A kérelemegységek száma meghatározza a párhuzamosan futtatható feladatok maximális számát. Az általános javaslat 10 S3 kérelemegység. 

Ha csak indexelni szeretné a videót, és nem kívánja kódolni, állítsa a `streamingPreset` paramétert `NoStreaming` értékre.

#### <a name="videourl"></a>videoUrl

Az indexelni kívánt video-/hangfájl URL-címe. Az URL-címnek egy médiafájlra kell mutatnia (a HTML-lapok nem támogatottak). A fájl védelme az URI részeként biztosított hozzáférési jogkivonattal biztosítható, a fájlt szolgáltató végpontot pedig TLS 1.2-es vagy újabb verzióval kell védeni. Az URL-címet kódolni kell. 

Ha a `videoUrl` paraméter nincs meghatározva, a Video Indexer elvárja, hogy többrészes/többformátumú törzstartalomként adja át a fájlt.

### <a name="code-sample"></a>Kódminta

Az alábbi C#-kódrészlet a Video Indexer API-k együttes használatát mutatja be.

#### <a name="instructions-for-running-this-code-sample"></a>A mintakód futtatására vonatkozó utasítások

A kód a fejlesztői platformra való másolása után két paramétert kell megadnia: API Management hitelesítési kulcsot és a videó URL-címét.

* API-kulcs – az API-kulcs a személyes API Management-előfizetési kulcs, amely lehetővé teszi, hogy hozzáférési jogkivonatot kapjon, hogy műveleteket hajtson végre a Video Indexer-fiókjában. 

    Az API-kulcs beszerzéséhez folytassa ezt a folyamatot:

    * Navigáljon https://api-portal.videoindexer.ai/
    * Bejelentkezés
    * Ugrás a **termékekhez** -> **engedélyezési** -> **engedélyezési előfizetés**
    * Az **elsődleges kulcs** másolása
* Videó URL-címe – az indexelni kívánt videó/hangfájl URL-címe. Az URL-címnek egy médiafájlra kell mutatnia (a HTML-lapok nem támogatottak). A fájl védelme az URI részeként biztosított hozzáférési jogkivonattal biztosítható, a fájlt szolgáltató végpontot pedig TLS 1.2-es vagy újabb verzióval kell védeni. Az URL-címet kódolni kell.

A kód sikeres futtatásának eredménye tartalmaz egy Insight widget URL-címet és egy, a Player widget URL-címét, amely lehetővé teszi az elemzések és a feltöltött videók vizsgálatát. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Gyakori hibák

A Feltöltés művelet által visszaadott lehetséges állapotkódok az alábbi táblázatban láthatók.

|Állapotkód|Hibatípus (a válasz törzsében)|Leírás|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Ugyanannak a videónak a feldolgozása már folyamatban van az adott fiókban.|
|400|VIDEO_ALREADY_FAILED|Ugyanannak a videónak a feldolgozása már meghiúsult az adott fiókban 2 órán belül. Az API-ügyfeleknek legalább 2 órát várniuk kell a videó ismételt feltöltése előtt.|
|429||A próbaverziós fiókok percenként 5 feltöltést vehetnek igénybe. A fizetős fiókok percenként 50 feltöltést engedélyeznek.|

## <a name="next-steps"></a>Következő lépések

[Az API által létrehozott Azure Video Indexer-kimenet vizsgálata](video-indexer-output-json-v2.md)
