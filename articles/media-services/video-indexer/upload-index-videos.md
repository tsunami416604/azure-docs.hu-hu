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
ms.date: 02/18/2020
ms.author: juliako
ms.openlocfilehash: 245eabdf4d77682c87062c2581239a554112d748
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468762"
---
# <a name="upload-and-index-your-videos"></a>Videók feltöltése és indexelése  

Videók Video Indexer API-val történő feltöltésekor a következő feltöltési lehetőségek közül választhatsz: 

* videó feltöltése egy URL-címről (előnyben részesített),
* a videofájl elküldése bájttömbként a kérelem törzsében,
* Meglévő Azure Media Services-eszköz használata az [eszközazonosító](https://docs.microsoft.com/azure/media-services/latest/assets-concept) biztosításával (csak a fizetős fiókokban támogatott).

A videó feltöltése után a Video Indexer (opcionálisan) kódolja a videót (a cikkben megtárgyaljva). A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős beállítással létrehozhat egy Video Indexer-fiókot, amely [az Azure-előfizetéséhez kapcsolódik, valamint egy Azure Media Services-fiókot.](connect-to-azure.md) Ön az indexelt perceket és a Media Accounttal kapcsolatos díjakat fizeti ki. 

A cikk bemutatja, hogyan töltheti fel és indexelheti videóit az alábbi lehetőségekkel:

* [A Video Indexer weboldala](#website) 
* [A videoindexelő API-k](#apis)

## <a name="uploading-considerations-and-limitations"></a>A feltöltésével kapcsolatos szempontok és korlátozások
 
- A videó neve nem lehet 80 karakternél hosszabb.
- Amikor az URL-cím alapján (előnyben részesített) feltöltöd a videót, a végpontot tls 1.2-es (vagy újabb) tls-sel kell védeni.
- Az URL-opcióval rendelkező feltöltési méret legfeljebb 30 GB lehet.
- A kérelem URL-címének hossza 6144 karakter, ahol a lekérdezési karakterlánc URL-jének hossza 4096 karakterre korlátozódik.
- A feltöltési méret a bájttömbbeállítással legfeljebb 2 GB lehet.
- A bájttömb beállítás 30 min után idővel időzít.
- A `videoURL` param-ban megadott URL-címet kódolni kell.
- A Media Services-eszközök indexelése ugyanolyan korlátozott, mint az URL-ből történő indexelés.
- A Video Indexer maximális időtartama 4 óra egyetlen fájl esetén.
- Az URL-címnek elérhetőnek kell lennie (például egy nyilvános URL-nek). 

    Ha ez egy privát URL-cím, a hozzáférési jogkivonatot meg kell adni a kérelemben.
- Az URL-címnek érvényes médiafájlra kell mutatnia, nem pedig `www.youtube.com` weblapra, például a lapra mutató hivatkozásra.
- Fizetős fiókban percenként legfeljebb 50 filmet tölthet fel, próbafiókban pedig percenként legfeljebb 5 filmet.

> [!Tip]
> Javasoljuk, hogy a .NET-keretrendszer 4.6.2-es vagy újabb verzióját használja, mivel a régebbi verziók nem a TLS 1.2-t használják alapértelmezés szerint.
>
> Ha régebbi verziót kell használnia, adjon hozzá egy sort a kódban a REST API-hívás indítása előtt:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>A Video Indexer támogatott fájlformátumai

A Video Indexer segítségével használható fájlformátumok listáját a [beviteli tároló/fájlformátumok](../latest/media-encoder-standard-formats.md#input-containerfile-formats) ról szóló cikkben találja.

## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a><a id="website"/>Videó feltöltése és indexelése a Video Indexer webhelyén

> [!NOTE]
> A videó neve nem lehet 80 karakternél hosszabb.

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
2. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    ![Feltöltés](./media/video-indexer-get-started/video-indexer-upload.png)

    A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót.

    ![Feltöltve](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Ha a Video Indexer befejezte az elemzést, kapni fog egy értesítést, benne a videóra mutató hivatkozással és a videóban talált tartalom rövid leírásával. Például: személyek, témák, OCR-ek.

## <a name="upload-and-index-with-api"></a><a id="apis"/>Feltöltés és indexelés API-val

A [Videó feltöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API-val url-cím alapján töltheted fel és indexelhetivideóidat. Az alábbi kódminta tartalmazza a megjegyzéssel végzett kódot, amely bemutatja a bájttömb feltöltését. 

### <a name="configurations-and-params"></a>Konfigurációk és paraméterek

Ez a szakasz ismertet néhány választható paramétert, és leírja, hogy mikor érdemes beállítani őket.

#### <a name="externalid"></a>externalID 

Ezzel a paraméterrel megadhat egy azonosítót, amely társítva lesz a videóhoz. Az azonosítót a külső „Video Content Management” (VCM) rendszer-integrációra is alkalmazni lehet. A Video Indexer portálon található videók a megadott külső azonosító használatával kereshetők.

#### <a name="callbackurl"></a>callbackUrl

Url-cím, amely (POST-kérelem használatával) értesíti az ügyfelet a következő eseményekről:

- Indexelési állapot változása: 
    - Tulajdonságok:    
    
        |Név|Leírás|
        |---|---|
        |id|A videó azonosítója|
        |state|A videó állapota|  
    - Példa: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- A videón azonosított személy:
  - Tulajdonságok
    
      |Név|Leírás|
      |---|---|
      |id| A videó azonosítója|
      |faceId|A videoindexben megjelenő arcazonosító|
      |knownPersonId|Az arcmodellen belül egyedi személyazonosító|
      |személyneve|A személy neve|
        
    - Példa: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

##### <a name="notes"></a>Megjegyzések

- A Video Indexer az eredeti URL-ben megadott paramétereket adja vissza.
- A megadott URL-címet kódolni kell.

#### <a name="indexingpreset"></a>indexingPreset

Akkor használja ezt a paramétert, ha a nyers vagy külső felvételek háttérzajt tartalmaznak. Ez a paraméter az indexelési folyamat konfigurálására szolgál. A következő értékeket adhatja meg:

- `AudioOnly` – Indexelés és elemzések kinyerése csak audiotartalmak használatával (videotartalmak figyelmen kívül hagyása)
- `VideoOnly`- Index és kivonat betekintést csak videó (figyelmen kívül hagyva audio)
- `Default` – Indexelés és elemzések kinyerése audio- és videotartalmak használatával
- `DefaultWithNoiseReduction` – Indexelés és elemzések kinyerése audio- és videotartalmakból, és zajcsökkentő algoritmusok alkalmazása az audiostreamen

> [!NOTE]
> A Video Indexer legfeljebb két hangsávot fed le. Ha több hangsáv van a fájlban, akkor azok egy sávként lesznek kezelve.<br/>
Ha külön szeretné indexelni a számokat, ki kell bontania `AudioOnly`a megfelelő hangfájlt, és indexelnie kell azt .

Az árat a kiválasztott indexelési lehetőség határozza meg.  

#### <a name="priority"></a>Prioritás

A videókat a Video Indexer a prioritásuk nak megfelelően indexeli. A **prioritási** paraméterrel adja meg az index prioritását. A következő értékek érvényesek: **Alacsony**, **Normál** (alapértelmezett) és **Magas**.

**A prioritási** paraméter csak a fizetős fiókok esetében támogatott.

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

#### <a name="instructions-for-running-this-code-sample"></a>A kódminta futtatására vonatkozó utasítások

Miután ezt a kódot a fejlesztési platformra másolta, két paramétert kell megadnia: az API Management hitelesítési kulcsát és a videó URL-címét.

* API-kulcs – AZ API-kulcs a személyes API-felügyeleti előfizetési kulcs, amely lehetővé teszi, hogy egy hozzáférési jogkivonatot kapjon a Video Indexer-fiók műveleteinek végrehajtása érdekében. 

    Az API-kulcs lehívásához menjen végig ezen a folyamaton:

    * Navigálás ahttps://api-portal.videoindexer.ai/
    * Bejelentkezés
    * Ugrás **a** -> **Termékengedélyezés engedélyezési** -> **előfizetésére**
    * Az **Elsődleges kulcs** másolása
* Videó URL - Az indexelendő video-/audiofájl URL-címe. Az URL-címnek egy médiafájlra kell mutatnia (a HTML-lapok nem támogatottak). A fájl védelme az URI részeként biztosított hozzáférési jogkivonattal biztosítható, a fájlt szolgáltató végpontot pedig TLS 1.2-es vagy újabb verzióval kell védeni. Az URL-címet kódolni kell.

Az eredmény a sikeresen futó kód minta tartalmazni fogja a betekintést widget URL-t és egy játékos widget URL-t, amely lehetővé teszi, hogy vizsgálja meg az betekintést és a feltöltött videó volt. 


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
|429||A próbafiókok percenként 5 feltöltést engedélyeznek. A fizetett fiókok percenként 50 feltöltést engedélyeznek.|

## <a name="next-steps"></a>További lépések

[Az API által létrehozott Azure Video Indexer-kimenet vizsgálata](video-indexer-output-json-v2.md)
