---
title: 'Példa: Videók feltöltése és indexelése a Video Indexerrel'
titlesuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan használhat API-kat a videók feltöltésére és indexelésére a Video Indexerrel.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 2261b8fa496beaf2a14c9b949047b6a5cbc6ea32
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292378"
---
# <a name="upload-and-index-your-videos"></a>Videók feltöltése és indexelése  

Ez a cikk bemutatja, hogyan tölthet fel videókat az Azure Video Indexerrel. A Video Indexer API két lehetőséget kínál a feltöltésre: 

* videó feltöltése egy URL-címről (előnyben részesített),
* küldje el a videó fájlt, egy bájttömböt a kérelem törzsében szereplő
* Használja meglévő Azure Media Services eszköz azáltal, hogy a [eszközazonosító](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (fizetős fiókok csak a támogatott).

A cikk bemutatja, hogyan használhatja a [Videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API-t a videók URL-cím alapján történő feltöltéséhez és indexeléséhez. A cikkben található kódminta tartalmazza a megjegyzésként szereplő kódot, amely bemutatja, hogyan lehet feltölteni a bájttömböt.  

A cikk emellett ismertet néhányat az API-ban beállítható paraméterek közül, amelyekkel módosíthatja az API folyamatát és kimenetét.

> [!Note]
> A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). <br/>Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős lehetőséggel Ön létrehoz egy Video Indexer-fiókot, amely [össze van kapcsolva az Azure-előfizetésével és egy Azure Media Services-fiókkal](connect-to-azure.md). Ön az indexelt perceket és a Media Accounttal kapcsolatos díjakat fizeti ki. 

## <a name="uploading-considerations"></a>Feltöltési szempontok
    
- A videó URL-cím alapján történő feltöltésekor (előnyben részesített) a végpontot a TLS 1.2-es (vagy újabb) verziójával kell védeni
- A bájttömb lehetőség 2 GB-ra van korlátozva, és 30 perc után átlépi az időkorlátot
- A `videoURL` paraméterben megadott URL-címet kódolni kell

> [!Tip]
> Javasoljuk, hogy a .NET-keretrendszer 4.6.2-es vagy újabb verzióját használja, mivel a régebbi verziók nem a TLS 1.2-t használják alapértelmezés szerint.
>
> Ha régebbi verziót kell használnia, adjon hozzá egy sort a kódban a REST API-hívás indítása előtt:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Konfigurációk és paraméterek

Ez a szakasz ismertet néhány választható paramétert, és leírja, hogy mikor érdemes beállítani őket.

### <a name="externalid"></a>externalID 

Ezzel a paraméterrel megadhat egy azonosítót, amely társítva lesz a videóhoz. Az azonosítót a külső „Video Content Management” (VCM) rendszer-integrációra is alkalmazni lehet. A Video Indexer portálon található videók a megadott külső azonosító használatával kereshetők.

### <a name="callbackurl"></a>callbackUrl

Egy URL-címet, amellyel az ügyfél (a POST-kérés használatával) a következő eseményekről kaphat értesítést:

- Az indexelő állapot módosítása: 
    - Tulajdonságok:    
    
        |Name (Név)|Leírás|
        |---|---|
        |id|A videó azonosítója|
        |state|A videó állapota|  
    - Például: https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- A videóban azonosított személy:
    - Tulajdonságok
    
        |Name (Név)|Leírás|
        |---|---|
        |id| A videó azonosítója|
        |faceId|A face ID, hogy megjelenik a videók indexe|
        |knownPersonId|A egy oldallal modellen belül egyedi személy azonosítója|
        |PersonName|Annak a személynek a nevét|
        
     - Például: https://test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Megjegyzések

- A video Indexer adja vissza az eredeti URL-címben megadott bármely meglévő paraméterek.
- A megadott URL-címet kell kódolni.

### <a name="indexingpreset"></a>indexingPreset

Akkor használja ezt a paramétert, ha a nyers vagy külső felvételek háttérzajt tartalmaznak. Ez a paraméter az indexelési folyamat konfigurálására szolgál. A következő értékeket adhatja meg:

- `Default` – Indexelés és elemzések kinyerése audio- és videotartalmak használatával
- `AudioOnly` – Indexelés és elemzések kinyerése csak audiotartalmak használatával (videotartalmak figyelmen kívül hagyása)
- `DefaultWithNoiseReduction` – Indexelés és elemzések kinyerése audio- és videotartalmakból, és zajcsökkentő algoritmusok alkalmazása az audiostreamen

Az árat a kiválasztott indexelési lehetőség határozza meg.  

### <a name="priority"></a>prioritás

Videók a Video Indexer által indexelt prioritásuk szerint. Használja a **prioritású** paraméterrel adja meg az index prioritás. A következő értékek érvényesek: **alacsony**, **normál** (alapértelmezett), és **magas**.

**Prioritás** paraméter csak a díjköteles fiók támogatott.

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
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

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

    // get the video id from the upload result
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

## <a name="next-steps"></a>További lépések

[A v2 API által előállított Azure Video Indexer kimenet vizsgálata](video-indexer-output-json-v2.md)
