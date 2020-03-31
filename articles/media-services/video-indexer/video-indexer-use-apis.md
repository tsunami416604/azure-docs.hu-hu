---
title: A Video Indexer API használata
titleSuffix: Azure Media Services
description: Ez a cikk ismerteti, hogyan lehet elkezdeni az Azure Media Services videoindexelő API-t.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 8b6d160f71bfe8b2e5c447296d511b54ce6542c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245849"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Oktatóanyag: A Video Indexer API használata

A Video Indexer egyetlen integrált szolgáltatásba egyesíti a Microsoft által kínált különböző audio- és videomesterséges intelligencia (AI) technológiákat, egyszerűbbé téve a fejlesztést. Az API-k célja, hogy lehetővé tegyék a fejlesztők számára, hogy a Media AI-technológiák fogyasztására összpontosítsanak anélkül, hogy aggódniuk kellene a felhőplatformok méretezése, globális elérése, elérhetősége és megbízhatósága miatt. Az API segítségével feltöltheti fájljait, részletes videoelemzéseket kaphat, beágyazható beágyazható betekintési és lejátszói widgeteket szerezhet be, és így tovább.

Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol bizonyos számú ingyenes indexelési percet kap) vagy egy fizetett opciót (ahol nem korlátozza a kvóta). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználóinak, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználók számára. Fizetős beállítással létrehozhat egy Video Indexer-fiókot, amely [az Azure-előfizetéséhez kapcsolódik, valamint egy Azure Media Services-fiókot.](connect-to-azure.md) Ön az indexelt perceket és az Azure Media Services-fiókkal kapcsolatos díjakat fizeti ki.

Ez a cikk azt mutatja be, hogyan használhatják ki a fejlesztők a [Video Indexer API](https://api-portal.videoindexer.ai/) előnyeit.

## <a name="subscribe-to-the-api"></a>Feliratkozás az API-ra

1. Jelentkezzen be a [Video Indexer fejlesztői portálra](https://api-portal.videoindexer.ai/).
    
    ![Bejelentkezés a Video Indexer fejlesztői portáljára](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Használja ugyanazt a szolgáltatót, amelyet a Video Indexerre való regisztráláskor használt.
   > * A személyes Google- és Microsoft-fiókok (Outlook/Live) csak próbafiókokhoz használhatók. Az Azure-hoz kapcsolódó fiókokhoz Azure AD-ra van szükség.
   > * E-mailenként csak egy aktív fiók lehet. Ha egy felhasználó megpróbál user@gmail.com bejelentkezni a LinkedIn, majd user@gmail.com a Google, az utóbbi jelenik meg egy hiba oldalt, mondván, a felhasználó már létezik.

2. Iratkozzon fel.

    Válassza a [Termékek](https://api-portal.videoindexer.ai/products) lapot. Ezután válassza az Engedélyezés lehetőséget, és iratkozzon fel.
    
    ![A Termékek lap a Video Indexer Developer Portal webhelyen](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Az új felhasználók automatikusan feliratkoznak az Engedélyezési API-ra.
    
    Miután előfizetett, láthatja az előfizetést, valamint az elsődleges és másodlagos kulcsokat. A kulcsok védelmet igényelnek. Csak a kiszolgálókód használhatja a kulcsokat. Nem szabad elérhetőnek lenniük az ügyféloldalon (.js, .html és így tovább).

    ![Előfizetés és kulcsok a Video Indexer Developer Portal webhelyen](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Egy Video Indexer-felhasználó egyetlen előfizetői kulccsal több Video Indexer-fiókhoz is csatlakozhat. Ezek a Video Indexer-fiókok pedig különböző Media Services-fiókokhoz társíthatók.

## <a name="obtain-access-token-using-the-authorization-api"></a>Hozzáférési jogkivonat beszerzése az Engedélyezési API-val

Miután előfizetett az engedélyezési API-ra, hozzáférési jogkivonatokat kaphat. Ezen hozzáférési jogkivonatok segítségével történik a hitelesítés a Műveleti API-n.

A Műveleti API-ba irányuló összes hívást társítani kell egy hozzáférési jogkivonathoz, amely megfelel a hívás engedélyezési hatókörének.

- Felhasználói szint: A felhasználói szintű hozzáférési jogkivonatok lehetővé teszik a **felhasználói** szintű műveletek elvégzését. Lekérhet például társított fiókokat.
- Fiókszint: A fiókszintű hozzáférési jogkivonatok lehetővé teszik a **fiók-** vagy **videószintű** műveletek elvégzését. Például feltöltheti a videót, felsorolhatja az összes videót, videóelemzéseket kaphat, és így tovább.
- Videószint: A videószintű hozzáférési jogkivonatok lehetővé teszik, hogy műveleteket hajtson végre egy adott **videón.** Például videóelemzéseket kaphat, letöltheti a feliratokat, widgeteket és így tovább.

Szabályozhatja, hogy ezek a tokenek írásvédettek-e, vagy engedélyezik-e a szerkesztést az **allowEdit=true/false**érték megadásával.

A legtöbb kiszolgáló-kiszolgáló forgatókönyv esetén valószínűleg ugyanazt a **fióktokent** fogja használni, mivel az a **fiókműveletekre** és a **videoműveletekre** egyaránt vonatkozik. Ha azonban azt tervezi, hogy ügyféloldali hívásokvideo indexelő (például a JavaScript), érdemes használni a **video-hozzáférési** jogkivonatot, hogy megakadályozza az ügyfelek hozzáférését a teljes fiókhoz. Ez az oka annak is, hogy amikor videoindexelő ügyfélkódot ágyaz be az ügyfélbe (például a **Get Insights Widget** vagy a Get Player **Widget**használatával), meg kell adnia egy **video-hozzáférési** tokent.

A művelet leegyszerűsítése érdekében használhatja az **Engedélyezési** API **GetAccounts** parancsát, ha a fiókjait a felhasználói jogkivonat beszerzése előtt szeretné lekérni. Lekérheti az érvényes jogkivonattal rendelkező fiókokat is, így nem kell még egy hívással fiókjogkivonatot lekérnie.

A hozzáférési jogkivonatok 1 óráig érvényesek. A Műveleti API használata előtt ellenőrizze, hogy érvényes-e a hozzáférési jogkivonata. Ha lejár, hívja meg újra az engedélyezési API-t egy új hozzáférési jogkivonat lekéréséhez.

Készen áll az API-val való integrációra. Itt megtalálhatja [az egyes Video Indexer REST API-k részletes leírását](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Fiókazonosító

A Fiókazonosító paraméterre minden műveleti API-híváshoz szükség van. A fiókazonosító egy GUID, és az alábbi módokon szerezhető be:

* A fiókazonosító lekérése a **Video Indexer webhelyen**:

    1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
    2. Lépjen a **Settings** (Beállítások) lapra.
    3. Másolja a fiókazonosítót.

        ![Videoindexelő-beállítások és fiókazonosító](./media/video-indexer-use-apis/account-id.png)

* A fiókazonosító lekérése szoftveresen a **Video Indexer fejlesztői portálon**.

    Használja a [Fiók begetése](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) API-t.

    > [!TIP]
    > A `generateAccessTokens=true` meghatározásával létrehozhat hozzáférési jogkivonatokat a fiókokhoz.

* Kérje le a fiókazonosítót a fiókja valamely lejátszóoldalának URL-címéből.

    Amikor megtekint egy videót, az azonosító megjelenik az `accounts` szakasz után, de még a `videos` szakasz előtt.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Javaslatok

Ebben a részben a Video Indexer API használatára vonatkozó javaslatokat talál.

- Ha videót szeretne feltölteni, javasoljuk, hogy a fájlt egy nyilvános hálózati helyre (például a OneDrive-ra) helyezze el. Kérje le a videóra mutatkozó hivatkozást, és adja meg az URL-címet a feltöltendő fájl paramétereként.

    A Video Indexer számára megadott URL-címnek egy médiafájlra (audió- vagy videófájlra) kell mutatnia. A OneDrive által létrehozott hivatkozások egy része a fájlt tartalmazó HTML-lapra mutat. Az URL-cím egyszerű ellenőrzése, ha beilleszti azt egy böngészőbe – ha a fájl letöltése megkezdődik, akkor valószínűleg jó URL-t kap. Ha a böngésző vizualizációt jelenít meg, akkor valószínűleg nem egy fájlra mutató hivatkozás, hanem egy HTML-oldalra mutató hivatkozás.

- Amikor meghívja az adott videóhoz tartozó videóelemzéseket lekérő API-t, részletes JSON-kimenetet kap a válasz tartalmaként. [A visszaadott JSON-ról ebben a témakörben találhat részleteket](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Kódminta

Az alábbi C#-kódrészlet a Video Indexer API-k együttes használatát mutatja be.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Lásd még

- [A Video Indexer áttekintése](video-indexer-overview.md)
- [Régiók](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>További lépések

- [Vizsgálja meg a kimeneti JSON részleteit](video-indexer-output-json-v2.md)
- Tekintse meg a [mintakódot,](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/API) amely bemutatja a videó feltöltésének és indexelésének fontos aspektusát. A kódot követve jó ötlet, hogyan kell használni az API-t az alapvető funkciókat. Győződjön meg róla, hogy olvassa el a inline megjegyzéseket, és figyelje meg a legjobb gyakorlatok tanácsok.

