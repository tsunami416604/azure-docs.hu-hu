---
title: Töltse fel, és az indexfrissítési videóit a Azure Video Indexer |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan töltse fel, és index videóit a Azure Video Indexer API-k használatával
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: df3ebdcb07980c297204d6d2959cac6a759b34e2
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347431"
---
# <a name="upload-and-index-your-videos"></a>A videók feltöltése és az  

Ez a cikk bemutatja, hogyan használhatja a [videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) fel-és indexelése a videók az Azure Video Indexer API. Emellett ismerteti, hogy megadhatja azt az API-t a folyamat és a kimeneti API-paraméterek némelyike.

## <a name="configurations-and-params"></a>Konfigurációk és paraméterek

Ez a szakasz ismerteti az egyes nem kötelező paraméter, és szeretne állítani őket.

### <a name="externalid"></a>externalID 

Ez a paraméter lehetővé teszi, hogy a videó társítva lesz azonosító megadása. Az azonosító "A videó tartalomkezelési" (VCM) külső rendszer-integrációs is alkalmazható. A videók a Video Indexer-portálon található a megadott külső. is meg lehet keresni

### <a name="indexingpreset"></a>indexingPreset

Használja ezt a paramétert, ha nyers vagy külső felvételek háttérzaj tartalmazza. Ez a paraméter az indexelési folyamat konfigurálására szolgál. A következő értékeket adhatja meg:

- `Default` – Index és az elemzések hang és videó együttes használatával
- `AudioOnly` – Index és az elemzések használatával hang csak (figyelmen kívül hagyja a videó)
- `DefaultWithNoiseReduction` – Indexelése és elemzési adatokat nyerhet a hang és videó, hang streamu zaj csökkentésére algoritmusok alkalmazása közben

Díj attól függ, hogy a kiválasztott indexelési lehetőség.  

### <a name="streamingpereset"></a>streamingPereset

A videó feltöltése után a Video Indexer, igény szerint kódolja a videót. Ezt követően az indexelést és a videó elemzése abból. Mikor történik a Video Indexer elemzése, a videó azonosítójú értesítést fog kapni  

Használatakor a [videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy [videó újraindexelése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API-t, a választható paraméterek egyike `streamingPreset`. Ha `streamingPereset` való `Default`, `SingleBitrate`, vagy `AdaptiveBitrate`, a kódolási folyamat akkor indul el. Ha az indexelés és a kódolási feladat kész, a videó közzététele, így streamelheti a videót is. A folyamatos átviteli végponton, ahonnan a videó továbbításához kell lennie a **futó** állapota.

Annak érdekében, hogy az indexelést és a kódolási feladat, futtassa a [a Video Indexer-fiókot az Azure Media Services-fiók csatlakoztatva](connect-to-azure.md), fenntartott egységet igényel. További információkért lásd: [Media feldolgozás skálázása](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Mivel ezek olyan nagy számításigényű számítási feladatok, erősen ajánlott S3-egység típusa. A fenntartott egységek számát, amelyek párhuzamosan futtatható feladatok maximális száma határozza meg. Az alapkonfiguráció javasoljuk 10 S3 szintű fenntartott. 

Ha csak a videó indexeléséről kívánja, de nem kódolja azokat, állítsa be `streamingPereset`való `NoStreaming`.

## <a name="code-sample"></a>Kódminta

Az alábbi C# kódrészlet együtt a minden a Video Indexer API-k használatát mutatja be.

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



## <a name="next-steps"></a>További lépések

[Az Azure-Videóindexelő kimenetének v2 API által előállított vizsgálata](video-indexer-output-json-v2.md)
