---
title: Töltse fel, és az indexfrissítési videóit a Azure Video Indexer |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan töltse fel, és index videóit a Azure Video Indexer API-k használatával
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: ac9d3f8fd10a3b65a2af2999b8c7ade7965de912
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664445"
---
# <a name="upload-and-index-your-videos"></a>A videók feltöltése és az  

Ez a cikk bemutatja, hogyan videó feltöltése a Video Indexer – Azure. A Video Indexer API két feltöltését lehetőségeket biztosítja: 

* a (javasolt) URL-címről videó feltöltése
* fájl elküldése egy bájttömböt a kérelem törzsében.

A cikk bemutatja, hogyan használhatja a [videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API-t a videók feltöltése és az URL-címe alapján. A cikkben a példakód meg a kódot, amely bemutatja, hogyan tölthet fel a bájttömböt a megjegyzésekkel tartalmazza.  

A cikk ismerteti, hogy megadhatja azt az API-t a folyamat és a kimeneti API-paraméterek némelyike is.

> [!Note]
> A Video Indexer-fiók létrehozásakor kiválaszthatja a (, ahol megkapja ingyenes indexelési perc bizonyos számú) egy ingyenes próbafiókot vagy egy fizetős lehetőség (Ha nem korlátozza a kvóta). <br/>Az ingyenes próbaverziót a Video Indexer legfeljebb 600 perc ingyenes indexeli a webhely számára biztosít, és akár 2400 percnyi ingyenes indexelő API számára. A fizetős lehetőség a Video Indexer-fiókot, amely létrehozhat [csatlakozik az Azure-előfizetés és az Azure Media Services-fiók](connect-to-azure.md). Indexelt perc, valamint a kapcsolódó adathordozó-fiók fizet díjak. 

## <a name="uploading-considerations"></a>Feltöltésével kapcsolatos szempontok
    
- Ha a videó feltöltése a (javasolt) URL-cím alapján a végpont védeni kell a TLS 1.2-es (vagy annál újabb)
- A bájt tömb beállítás korlátozva, 4GB, és 30 perc után időtúllépés
- A megadott URL-CÍMÉT a `videoURL` param kódolni kell

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

### <a name="callbackurl"></a>callbackUrl

POST URL-címe értesítése, ha az indexelés hajtható végre. A video Indexer hozzáadja két lekérdezési karakterlánc paraméterek: azonosítója és állapota. Például, ha a visszahívási URL-címe "https://test.com/notifyme?projectName=MyProject", az értesítés jelenik meg a további paramétereket"https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed".

Is hozzáadhat további paramétereket az URL-címet a Video Indexer hívása előtt, és ezeket a paramétereket fog szerepelni a visszahívás. Később a kódban, a lekérdezési karakterlánc elemzésére és az első biztonsági összes a megadott paramétereket a lekérdezési karakterláncban (adatok meg kellett eredetileg hozzáfűzi az URL-címe és a Video Indexer a megadott adatokat.) Kódolni kell az URL-címet.

### <a name="streamingpreset"></a>streamingPreset

A videó feltöltése után a Video Indexer, igény szerint kódolja a videót. Ezt követően az indexelést és a videó elemzése abból. Mikor történik a Video Indexer elemzése, a videó azonosítójú értesítést fog kapni  

Használatakor a [videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy [videó újraindexelése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API-t, a választható paraméterek egyike `streamingPreset`. Ha `streamingPreset` való `Default`, `SingleBitrate`, vagy `AdaptiveBitrate`, a kódolási folyamat akkor indul el. Ha az indexelés és a kódolási feladat kész, a videó közzététele, így streamelheti a videót is. A folyamatos átviteli végponton, ahonnan a videó továbbításához kell lennie a **futó** állapota.

Annak érdekében, hogy az indexelést és a kódolási feladat, futtassa a [a Video Indexer-fiókot az Azure Media Services-fiók csatlakoztatva](connect-to-azure.md), fenntartott egységet igényel. További információkért lásd: [Media feldolgozás skálázása](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Mivel ezek olyan nagy számításigényű számítási feladatok, erősen ajánlott S3-egység típusa. A fenntartott egységek számát, amelyek párhuzamosan futtatható feladatok maximális száma határozza meg. Az alapkonfiguráció javasoljuk 10 S3 szintű fenntartott. 

Ha csak a videó indexeléséről kívánja, de nem kódolja azokat, állítsa be `streamingPreset`való `NoStreaming`.

### <a name="videourl"></a>videoUrl

Egy indexelendő a videó vagy hang fájl URL-címe. Az URL-cím, egy médiafájl kell mutatnia (HTML-lapok nem támogatottak). A fájl védelmét biztosíthatja úgy egy hozzáférési jogkivonatot az URI-t részeként, és a végpont szolgálja ki a fájlt kell lennie, biztonságos, a TLS 1.2-es vagy újabb. Kódolni kell az URL-címet. 

Ha a `videoUrl` nincs megadva, a Video Indexer vár, hogy át a fájlt, egy multipart/form szövegtörzse.

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
