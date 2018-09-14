---
title: Az Azure Video Indexer API használata |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan kezdheti el a Video Indexer API használatával.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: bd0f9e01257bec2d39ef0c8e13b68c4a7a13637d
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542926"
---
# <a name="use-azure-video-indexer-api"></a>Az Azure Video Indexer API használata

> [!Note]
> A Video Indexer V1 API 2018. augusztus 1-től. a rendszer elavult. Most már a Video Indexer v2 API-t kell használnia. <br/>A Video Indexer v2 API-k fejlesztéséhez, tekintse meg a útmutatását [Itt](https://api-portal.videoindexer.ai/). 

Video Indexer – összesíti egy integrált szolgáltatás, a Microsoft által kínált különböző audio- és mesterséges intelligencia (AI) technológiákat, így egyszerűbb fejlesztés. Az API-k engedélyezése összpontosítani, nem kell bajlódnunk méretezhető, globális Media AI technológiákat használó fejlesztők számára érhető el, a rendelkezésre állás és a felhőalapú platform megbízhatóságát lett tervezve. Az API használatával a fájlok feltöltése, a videó részletes elemzések lekérése, annak érdekében, hogy a beágyazása az alkalmazás és más feladatok insight és a játékosok widgetek URL-címek lekérése.

A Video Indexer-fiók létrehozásakor kiválaszthatja a (, ahol megkapja ingyenes indexelési perc bizonyos számú) egy ingyenes próbafiókot vagy egy fizetős lehetőség (Ha nem korlátozza a kvóta). Az ingyenes próbaverziót a Video Indexer legfeljebb 600 perc ingyenes indexeli a webhely számára biztosít, és akár 2400 percnyi ingyenes indexelő API számára. A fizetős lehetőség a Video Indexer-fiókot, amely létrehozhat [csatlakozik az Azure-előfizetés és az Azure Media Services-fiók](connect-to-azure.md). Perc, valamint az Azure Media Services-fiók kapcsolódó költségek indexelve kell fizetnie. 

Ez a cikk bemutatja, hogy a fejlesztők kihasználhatják a [Video Indexer API](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Fizessen elő az API-hoz

1. Jelentkezzen be a [Video Indexer – fejlesztői portál](https://api-portal.videoindexer.ai/).
    
    ![Bejelentkezés](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * A Video Indexer felhasználóihoz való regisztráció során használt ugyanazt a szolgáltatót kell használnia.
    > * Személyes Google és a Microsoft (outlook vagy élő) fiókok csak a próbafiókokon használható. Fiókok kapcsolódik az Azure az Azure AD szükséges.
    > * E-mailenként csak egy aktív fiók is lehet. Ha egy felhasználó megpróbál jelentkezzen be user@gmail.com linkedinre és után, hogy a user@gmail.com a Google később a hibalap fogja megjeleníteni, arról tájékoztatja a felhasználó már létezik.

2. Az előfizetés.

    Válassza ki a [termékek](https://api-portal.videoindexer.ai/products) fülre. Ezután válassza ki az engedélyezési és az előfizetés. 
    
    ![Regisztráció](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Új felhasználókat automatikusan előfizetnek az engedélyezési.
    
    Miután előfizetett, lesz az előfizetés és az elsődleges és másodlagos kulcsot. A kulcsok meg kell védeni. A kulcsok csak a kiszolgálói kód által használható. Ezek nem lehet elérhető az ügyféloldalon (.js, .html, stb.).

    ![Regisztráció](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Az engedélyezési API-val hozzáférési jogkivonat beszerzése

Az engedélyezési API-ra előfizetett, ha tudják, hozzáférési tokenek beszerzése érdekében. Ezek a hozzáférési jogkivonatok segítségével a műveleti API-t hitelesítésre. 

A műveleti API minden meghívásához egy hozzáférési jogkivonatot, a hívás engedélyezési hatókörének megfelelő társítva kell lennie.

- Felhasználói szinten - felhasználói hozzáférési tokenek lehetővé teszik műveleteket végezhet a **felhasználói** szintjét. Például a hozzárendelt fiókok beolvasása.
- Fiók szintjén – fiók hozzáférési tokenek lehetővé teszik műveleteket végezhet a **fiók** szint vagy a **videó** szintet. Például videó feltöltése, listázása az összes videó, első feltárását, és így tovább.
- Videószintű – videó az Objektumszintű hozzáférési tokenek lehetővé teszik az adott műveletek elvégzését **videó**. Például első feltárását, feliratok letöltése, első vezérlőkhöz, stb. 

Szabályozhatja, hogy ezek a jogkivonatok readonly vagy megadásával szerkesztését teszik **szerkeszthető = igaz/hamis**.

A kiszolgálók közötti kapcsolatos legtöbb esetben valószínűleg használhat azonos **fiók** token, mert egyaránt vonatkozik **fiók** operations és **videó** műveletek. Azonban ha azt tervezi, hogy az ügyféloldali hívások (például a javascript-) Video Indexer, szeretne használni egy **videó** hozzáférési jogkivonatot, hogy megakadályozza a hozzáférést a teljes fiókra. Ez is az oka, hogy amikor VideoIndexer Ügyfélkód beágyazása az ügyfél (használata esetén például **Get Insights Widget** vagy **első Player Widget**) meg kell adnia egy **videó**hozzáférési jogkivonatot.

Egyszerűbbé tételéhez, használhatja a **engedélyezési** API > **GetAccounts** beolvasni a fiókok a felhasználó beszerzése nélkül jogkivonatot először. Is feltehet a fiókok érvényes jogkivonatokkal, lehetővé téve a fiók jogkivonatot kapjon egy további hívás kihagyása.

Hozzáférési jogkivonatok 1 óra után lejárnak. Ellenőrizze, hogy a hozzáférési jogkivonat érvénytelen a műveleti API használata előtt. Ha a lejár, az engedélyezési API-t egy új hozzáférési jogkivonat beszerzése az újra.
 
Készen áll az API-val integrálásának megkezdéséhez. Keresés [minden Video Indexer – REST API részletes leírását](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Hely

Az összes művelet API-k egy helyen paramétert, amely azt jelzi, hogy a hívás meg kell átirányítani és a fiók létrejött a régióban van szükség.

Az értékek a következő táblázat ismerteti a alkalmazni. A **Param érték** az API-t használja az értéket, ha adja át.

|**Name (Név)**|**Param érték**|**Leírás**|
|---|---|---|
|Próbaverzió|Próbaverzió|Próbafiókokon használható.|
|USA nyugati régiója|westus2|Használja az Azure West US 2 régióban.|
|Észak-Európa |northeurope|Az Azure észak-európai régión használatos.|
|Kelet-Ázsia|eastasia|Használja a Kelet-Ázsia Azure-régióban.|

## <a name="account-id"></a>Fiókazonosító 

Az összes műveleti API-hívások a Fiókazonosító paraméter megadása kötelező. Fiókazonosító egy GUID Azonosítót, amely szerezhető be a következő módszerek valamelyikével:

* Használja a **Video Indexer webhely** beolvasni a Fiókazonosító:

    1. Keresse meg a [Video Indexer](https://www.videoindexer.ai/) webhelyet, és jelentkezzen be.
    2. Keresse meg a **beállítások** lapot.
    3. Másolja az azonosítót.

        ![Fiókazonosító](./media/video-indexer-use-apis/account-id.png)

* Használat **Video Indexer fejlesztői portál** programozott módon beolvasni a fiók azonosítóját.

    Használja a [fiókok](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API-t.
    
    > [!TIP]
    > A fiókok vonatkozó hozzáférési jogkivonatainak definiálásával is létrehozhat `generateAccessTokens=true`.
    
* A Fiókazonosító lekérheti a fiókjában player oldal URL-CÍMÉT.

    Ha egy videó azonosítója után jelenik meg a `accounts` szakasz és előtt a `videos` szakaszban.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Javaslatok

Ez a szakasz felsorolja az egyes javaslatok Video Indexer API használatakor.

- Ha azt tervezi, a videó feltöltése, ajánlott egy nyilvános hálózati helyre (például a OneDrive) helyezze a fájlt. A videó hivatkozásának beszerzése, és adja meg az URL-címet, a feltöltési fájl param. 

    A Video Indexer a megadott URL-cím médiafájl (hang- vagy) kell mutatnia. A onedrive vállalati verzió által generált hivatkozások vannak egy HTML-oldalt, amely tartalmazza a fájl számára. Egy egyszerű ellenőrzési esetében az URL-címet másolja be egy böngészőbe –, ha a fájl letöltése lenne, akkor valószínű helyes URL-címet. Ha a böngésző megjeleníti néhány vizualizációt, akkor valószínűleg sem egy fájlt, de egy HTML-oldalra mutató hivatkozást.
    
- Amikor hívja az API-t, amely lekéri a megadott videó feltárását, a részletes JSON-kimenet kap, a válasz tartalma. [Ebben a témakörben a visszaküldött JSON részleteinek megtekintéséhez](video-indexer-output-json.md).

## <a name="code-sample"></a>Kódminta

Az alábbi C# kódrészlet együtt a minden a Video Indexer API-k használatát mutatja be.

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

[Vizsgálja meg a részleteket a kimenet JSON](video-indexer-output-json.md).

[Video Indexer – áttekintés](video-indexer-overview.md)
