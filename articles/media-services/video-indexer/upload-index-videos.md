---
title: 'Példa: Videók feltöltése és indexelése a Video Indexerrel'
titlesuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan használhat API-kat a videók feltöltésére és indexelésére a Video Indexerrel.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.openlocfilehash: d6338f3840b6f8afe21f8115304ba00bba90c6ea
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372376"
---
# <a name="upload-and-index-your-videos"></a>Videók feltöltése és indexelése  

Videók Video Indexer API-val való feltöltésekor a következő feltöltési lehetőségek közül választhat: 

* videó feltöltése egy URL-címről (előnyben részesített),
* a videofájl elküldése byte-tömbként a kérelem törzsében
* Meglévő Azure Media Services eszköz használata az [eszköz azonosítójának](https://docs.microsoft.com/azure/media-services/latest/assets-concept) megadásával (csak a fizetett fiókok esetében támogatott).

A cikk bemutatja, hogyan használhatja a [Videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API-t a videók URL-cím alapján történő feltöltéséhez és indexeléséhez. A cikkben található kódminta tartalmazza a megjegyzésként szereplő kódot, amely bemutatja, hogyan lehet feltölteni a bájttömböt. <br/>A cikk emellett ismertet néhányat az API-ban beállítható paraméterek közül, amelyekkel módosíthatja az API folyamatát és kimenetét.

A videó feltöltése után Video Indexer, opcionálisan kódolja a videót (a cikkben ismertetett módon). A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős megoldással olyan Video Indexer fiókot hozhat létre, amely az [Azure-előfizetéshez és egy Azure Media Services-fiókhoz csatlakozik](connect-to-azure.md). Ön az indexelt perceket és a Media Accounttal kapcsolatos díjakat fizeti ki. 

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

> [!Tip]
> Javasoljuk, hogy a .NET-keretrendszer 4.6.2-es vagy újabb verzióját használja, mivel a régebbi verziók nem a TLS 1.2-t használják alapértelmezés szerint.
>
> Ha régebbi verziót kell használnia, adjon hozzá egy sort a kódban a REST API-hívás indítása előtt:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Konfigurációk és paraméterek

Ez a szakasz ismertet néhány választható paramétert, és leírja, hogy mikor érdemes beállítani őket.

### <a name="externalid"></a>externalID 

Ezzel a paraméterrel megadhat egy azonosítót, amely társítva lesz a videóhoz. Az azonosítót a külső „Video Content Management” (VCM) rendszer-integrációra is alkalmazni lehet. A Video Indexer portálon található videók a megadott külső azonosító használatával kereshetők.

### <a name="callbackurl"></a>callbackUrl

Egy URL-cím, amely az ügyfél (POST-kérelem használatával) értesítésére szolgál az alábbi eseményekről:

- Az indexelési állapot változása: 
    - Tulajdonságok    
    
        |Név|Leírás|
        |---|---|
        |id|A videó azonosítója|
        |state|A videó állapota|  
    - Például: https: \//test. com/Notifyme? projektnév = MyProject & id = 1234abcd & State = Processed
- A videóban azonosított személy:
  - Tulajdonságok
    
      |Név|Leírás|
      |---|---|
      |id| A videó azonosítója|
      |faceId|A videó indexében megjelenő Arcfelismerés|
      |knownPersonId|Az arc-modellen belül egyedi személy azonosítója|
      |personName|A személy neve|
        
    - Például: https: \//test. com/Notifyme? projektnév = MyProject & id = 1234abcd & faceid = 12 & knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5 & personName = Inigo_Montoya 

#### <a name="notes"></a>Megjegyzések

- Video Indexer az eredeti URL-címben megadott meglévő paramétereket adja vissza.
- A megadott URL-címet kódolni kell.

### <a name="indexingpreset"></a>indexingPreset

Akkor használja ezt a paramétert, ha a nyers vagy külső felvételek háttérzajt tartalmaznak. Ez a paraméter az indexelési folyamat konfigurálására szolgál. A következő értékeket adhatja meg:

- `Default` – Indexelés és elemzések kinyerése audio- és videotartalmak használatával
- `AudioOnly` – Indexelés és elemzések kinyerése csak audiotartalmak használatával (videotartalmak figyelmen kívül hagyása)
- `DefaultWithNoiseReduction` – Indexelés és elemzések kinyerése audio- és videotartalmakból, és zajcsökkentő algoritmusok alkalmazása az audiostreamen

Az árat a kiválasztott indexelési lehetőség határozza meg.  

### <a name="priority"></a>prioritású

A videók indexelése Video Indexer a prioritásuk szerint történik. Az index prioritásának megadásához használja a **priority** paramétert. A következő értékek érvényesek: **alacsony**, **normál** (alapértelmezett) és **magas**.

A **priority** paraméter csak a fizetős fiókok esetében támogatott.

### <a name="streamingpreset"></a>streamingPreset

A videó feltöltése után a Video Indexer opcionálisan kódolja a videót. Ezután továbblép a videó indexelésére és elemzésére. Amikor a Video Indexer végzett az elemzéssel, kapni fog egy értesítést, benne a videó azonosítójával.  

A [Videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy a [Videó újraindexelése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API használatakor a `streamingPreset` az egyik választható paraméter. Ha a `streamingPreset` paramétert `Default`, `SingleBitrate` vagy `AdaptiveBitrate` értékre állítja, a kódolási folyamat aktiválódik. Az indexelési és a kódolási feladatok befejezése után a rendszer közzéteszi a videót, amely streamelhető is. A videó streameléséhez használt streamvégpontnak **Futó** állapotban kell lennie.

Az indexelési és kódolási feladatok futtatásához a [Video Indexer-fiókjához csatlakoztatott Azure Media Services-fióknak](connect-to-azure.md) fenntartott egységekre van szüksége. További információért lásd a [médiafeldolgozás skálázását](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) ismertető cikket. Mivel ezek nagy számításigényű feladatok, határozottan ajánlott az S3-as egységtípus. A kérelemegységek száma meghatározza a párhuzamosan futtatható feladatok maximális számát. Az általános javaslat 10 S3 kérelemegység. 

Ha csak indexelni szeretné a videót, és nem kívánja kódolni, állítsa a `streamingPreset` paramétert `NoStreaming` értékre.

### <a name="videourl"></a>videoUrl

Az indexelni kívánt video-/hangfájl URL-címe. Az URL-címnek egy médiafájlra kell mutatnia (a HTML-lapok nem támogatottak). A fájl védelme az URI részeként biztosított hozzáférési jogkivonattal biztosítható, a fájlt szolgáltató végpontot pedig TLS 1.2-es vagy újabb verzióval kell védeni. Az URL-címet kódolni kell. 

Ha a `videoUrl` paraméter nincs meghatározva, a Video Indexer elvárja, hogy többrészes/többformátumú törzstartalomként adja át a fájlt.

## <a name="code-sample"></a>Kódminta

Az alábbi C#-kódrészlet a Video Indexer API-k együttes használatát mutatja be.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

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
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
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
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

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

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
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
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

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
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
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
## <a name="common-errors"></a>Gyakori hibák

A Feltöltés művelet által visszaadott lehetséges állapotkódok az alábbi táblázatban láthatók.

|Állapotkód|Hibatípus (a válasz törzsében)|Leírás|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|Ugyanannak a videónak a feldolgozása már folyamatban van az adott fiókban.|
|400|VIDEO_ALREADY_FAILED|Ugyanannak a videónak a feldolgozása már meghiúsult az adott fiókban 2 órán belül. Az API-ügyfeleknek legalább 2 órát várniuk kell a videó ismételt feltöltése előtt.|

## <a name="next-steps"></a>Következő lépések

[Az API által létrehozott Azure Video Indexer-kimenet vizsgálata](video-indexer-output-json-v2.md)
