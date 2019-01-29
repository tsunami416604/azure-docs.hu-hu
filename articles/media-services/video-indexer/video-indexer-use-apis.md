---
title: Használja a Video Indexer API – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja a Video Indexer API használatának első lépéseit.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: d162f8caef0a14d86547539681f1da9b1b07ea1d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190496"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Oktatóanyag: A Video Indexer API használata

> [!Note]
> A Video Indexer API 1-es verziója 2018. augusztus 1-jével elavult. Használja a Video Indexer API 2-es verzióját. <br/>A Video Indexer API-k 2-es verziójával való fejlesztéshez tekintse át az [itt](https://api-portal.videoindexer.ai/) található útmutatót. 

A Video Indexer számos, a Microsoft által kínált audió- és videótartalmakkal kapcsolatos mesterséges intelligenciai (MI) technológiát egyesít egy integrált szolgáltatásban, megkönnyítve ezzel a fejlesztést. Az API-k úgy lettek kialakítva, hogy a fejlesztők a médiával kapcsolatos MI-technológiák felhasználására összpontosíthassanak, és ne kelljen a felhőplatform skálázása, globális elérhetősége, rendelkezésre állása és megbízhatósága miatt aggódniuk. Az API segítségével feltöltheti a fájljait, részletes videóelemzéseket kérhet, megkaphatja az elemzés és a lejátszóvezérlők URL-címét, hogy beágyazhassa őket az alkalmazásba, valamint egyéb feladatokat is végezhet.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős lehetőséggel Ön létrehoz egy Video Indexer-fiókot, amely [össze van kapcsolva az Azure-előfizetésével és egy Azure Media Services-fiókkal](connect-to-azure.md). Ön az indexelt perceket és az Azure Media Services-fiókkal kapcsolatos díjakat fizeti ki. 

Ez a cikk azt mutatja be, hogyan használhatják ki a fejlesztők a [Video Indexer API](https://api-portal.videoindexer.ai/) előnyeit.

## <a name="subscribe-to-the-api"></a>Feliratkozás az API-ra

1. Jelentkezzen be a [Video Indexer fejlesztői portálra](https://api-portal.videoindexer.ai/).
    
    ![Bejelentkezés](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * Használja ugyanazt a szolgáltatót, amelyet a Video Indexerre való regisztráláskor használt.
    > * A személyes Google-fiókok és Microsoft- (outlook-/live-) fiókok csak a próbafiókokhoz használhatók. Az Azure-hoz kapcsolódó fiókokhoz Azure AD-ra van szükség.
    > * Egy e-mail-címhez csak egy aktív fiók tartozhat. Ha egy felhasználó megpróbál bejelentkezni a LinkedIn-re a user@gmail.com címmel, majd később a Google-re ugyanazzal a user@gmail.com címmel, akkor az utóbbi megjelenít egy hibaüzenetet, amely szerint a felhasználó már létezik.

2. Iratkozzon fel.

    Válassza a [Products](https://api-portal.videoindexer.ai/products) (Termékek) lapot. Ezután válassza az Authorization (Engedélyezés), majd a Subscribe (Feliratkozás) lehetőséget. 
    
    ![Regisztráció](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Az új felhasználók automatikusan feliratkoznak az Engedélyezési API-ra.
    
    Ha feliratkozott, megtekintheti a feliratkozását, valamint az elsődleges és másodlagos kulcsokat. A kulcsok védelmet igényelnek. Csak a kiszolgálókód használhatja a kulcsokat. Az ügyféloldalon (.js, .html stb.) nem lehetnek elérhetők.

    ![Regisztráció](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Egy Video Indexer-felhasználó egyetlen előfizetői kulccsal több Video Indexer-fiókhoz is csatlakozhat. Ezek a Video Indexer-fiókok pedig különböző Media Services-fiókokhoz társíthatók.

## <a name="obtain-access-token-using-the-authorization-api"></a>Hozzáférési jogkivonat beszerzése az Engedélyezési API-val

Ha feliratkozott az Engedélyezési API-ra, lekérhet hozzáférési jogkivonatokat. Ezen hozzáférési jogkivonatok segítségével történik a hitelesítés a Műveleti API-n. 

A Műveleti API-ba irányuló összes hívást társítani kell egy hozzáférési jogkivonathoz, amely megfelel a hívás engedélyezési hatókörének.

- Felhasználói szint – felhasználói szintű hozzáférési jogkivonatok segítségével **felhasználói** szinten végezhet műveleteket. Lekérhet például társított fiókokat.
- Fiókszint – fiókszintű hozzáférési jogkivonatok segítségével **fiók-** vagy **videószinten** végezhet műveleteket. Feltölthet például videókat, listázhatja az összes videót, lekérhet videoelemzéseket stb.
- Videószint – videószintű hozzáférési jogkivonatok segítségével egy meghatározott **videón** végezhet műveleteket. Lekérhet például videóelemzéseket, letölthet feliratokat, kérhet vezérlőket stb. 

Az **allowEdit=true/false** megadásával szabályozhatja, hogy ezek a jogkivonatok írásvédettek legyenek, vagy lehessen szerkeszteni őket.

A legtöbb kiszolgálók közötti forgatókönyvben valószínűleg ugyanazt a **fiókszintű** jogkivonatot fogja használni, mert azzal a **fiókokkal** és a **videókkal** kapcsolatos műveletek is végezhetők. Ha azonban ügyféloldali hívásokat indítana a Video Indexerre (például JavaScriptből), **videószintű** hozzáférési jogkivonatot érdemes használnia, hogy az ügyfelek ne férjenek hozzá a teljes fiókhoz. Ez az oka annak is, hogy amikor Video Indexer-ügyfélkódot ágyaz be az ügyfélbe (például az **Elemzések lekérése vezérlő** vagy a **Lejátszó lekérése vezérlő**), **videószintű** hozzáférési jogkivonatot kell biztosítania.

A művelet leegyszerűsítése érdekében használhatja az **Engedélyezési** API **GetAccounts** parancsát, ha a fiókjait a felhasználói jogkivonat beszerzése előtt szeretné lekérni. Lekérheti az érvényes jogkivonattal rendelkező fiókokat is, így nem kell még egy hívással fiókjogkivonatot lekérnie.

A hozzáférési jogkivonatok 1 óráig érvényesek. A Műveleti API használata előtt ellenőrizze, hogy érvényes-e a hozzáférési jogkivonata. Ha lejárt, hívja meg az Engedélyezési API-t ismét egy új hozzáférési jogkivonat kéréséhez.
 
Készen áll az API integrálásának megkezdésére. Itt megtalálhatja [az egyes Video Indexer REST API-k részletes leírását](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Hely

Minden Műveleti API-hoz szükség van egy Hely paraméterre, amely jelzi, melyik régióba kell irányítani a hívást, és melyikben jött létre a fiók.

Az alábbi táblázatban szereplő értékek érvényesek. A **Paraméter értéke** az API használatakor megadott érték.

|**Name (Név)**|**Paraméter értéke**|**Leírás**|
|---|---|---|
|Próbaverzió|trial|Próbafiókokhoz használható.|
|USA nyugati régiója|westus2|Az Azure USA 2. nyugati régiójában használható.|
|Észak-Európa |northeurope|Az Azure Észak-Európa régiójában használható.|
|Kelet-Ázsia|eastasia|Az Azure Kelet-Ázsia régiójában használható.|

## <a name="account-id"></a>Fiókazonosító 

A Fiókazonosító paraméterre minden műveleti API-híváshoz szükség van. A fiókazonosító egy GUID, és az alábbi módokon szerezhető be:

* A fiókazonosító lekérése a **Video Indexer webhelyen**:

    1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
    2. Lépjen a **Settings** (Beállítások) lapra.
    3. Másolja a fiókazonosítót.

        ![Fiókazonosító](./media/video-indexer-use-apis/account-id.png)

* A fiókazonosító lekérése szoftveresen a **Video Indexer fejlesztői portálon**.

    Használja a [Fiókok lekérése](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API-t.
    
    > [!TIP]
    > A `generateAccessTokens=true` meghatározásával létrehozhat hozzáférési jogkivonatokat a fiókokhoz.
    
* Kérje le a fiókazonosítót a fiókja valamely lejátszóoldalának URL-címéből.

    Amikor megtekint egy videót, az azonosító megjelenik az `accounts` szakasz után, de még a `videos` szakasz előtt.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Javaslatok

Ebben a részben a Video Indexer API használatára vonatkozó javaslatokat talál.

- Ha videót szeretne feltölteni, javasoljuk, hogy a fájlt egy nyilvános hálózati helyen (például a OneDrive-ban) helyezze el. Kérje le a videóra mutatkozó hivatkozást, és adja meg az URL-címet a feltöltendő fájl paramétereként. 

    A Video Indexer számára megadott URL-címnek egy médiafájlra (audió- vagy videófájlra) kell mutatnia. A OneDrive által létrehozott hivatkozások egy része a fájlt tartalmazó HTML-lapra mutat. Az URL-cím könnyen ellenőrizhető, ha beilleszti egy böngészőbe: ha elindul a fájl letöltése, az URL-cím minden bizonnyal működik. Ha a böngésző valamilyen vizuális elemet jelenít meg, a hivatkozás valószínűleg egy HTML-lapra mutat.
    
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

## <a name="next-steps"></a>További lépések

[A kimeneti JSON adatainak vizsgálata](video-indexer-output-json-v2.md).

[A Video Indexer áttekintése](video-indexer-overview.md)
