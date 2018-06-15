---
title: Használja az Azure videó indexelőt API |} Microsoft Docs
description: Ez a cikk bemutatja, hogyan lásson videó indexelő API használatával.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 06/04/2018
ms.author: juliako
ms.openlocfilehash: d378934a0c085910475c366f4bdb538f09efc12b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35350106"
---
# <a name="use-azure-video-indexer-api"></a>Használja az Azure videó indexelőt API

Videó indexelő összesíti egy beépített szolgáltatás, a Microsoft által kínált számos hang- és mesterséges intelligencia (AI) technológiák fejlesztési egyszerűbbé tétele. Az API-k lehetővé teszik a fejlesztők számára helyezi a hangsúlyt Media AI technológiák fel anélkül, hogy a skála, globális elérni, a rendelkezésre állási és a megbízhatóság felhő platform tervezték. Az API használatával töltse fel a fájlokat, részletes videó nyerhet, URL-címek felmérése és player widgeteket a lekérése érdekében a beágyazása az alkalmazás és más feladatok.

> [!Note]
> Az ingyenes próbaverzióval rendelkezik egy 100 fájlok napi feltöltési korlátot. Ellenőrizheti a következő hibaüzenet jelenik a részletekért. Vegye figyelembe, hogy a napi korlátot változhatnak.

Ez a cikk bemutatja, hogy a fejlesztők kihasználhatják a a [videó indexelő API](https://api-portal.videoindexer.ai/). Olvassa el a videó indexelő szolgáltatás részletesebb áttekintése, tekintse meg a [áttekintése](video-indexer-overview.md) cikk.

## <a name="subscribe-to-the-api"></a>Előfizetés az API-hoz

1. bejelentkezés.

    Első bejelentkezés a kell elkezdje az videó indexelő a [videó indexelő](https://api-portal.videoindexer.ai/) portálon. 
    
    ![Regisztráció](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > 1. Regisztráció során a videó indexelő használja ugyanazt a szolgáltatót kell használnia.
    > 2. A tartomány felhasználóinak az Azure AD bejelentkezés, előtt az AAD tartományi rendszergazda engedélyeznie kell a tartományregisztrációs [Itt](https://api-portal.videoindexer.ai/aadadminconsent).
    > 3. Személyes Google és a Microsoft (outlook/live) fiókok csak próbaverziós fiókok használhatók. Kapcsolódik az Azure-fiók szükséges aad-ben.

2. Az előfizetés.

    Válassza ki a [termékek](https://api-portal.videoindexer.ai/products) fülre. Ezután válassza ki az engedélyezési és előfizetés. 
    
    ![Regisztráció](./media/video-indexer-use-apis/video-indexer-api02.png)
    
    Miután előfizetett, fogjuk tudni az előfizetés és az elsődleges és másodlagos kulcsok. A kulcsok meg kell védeni. A kulcsok csak a kiszolgálóoldali kódból használják. Ezek nem lehet elérhető az ügyféloldalon (.js, .html, stb.).

    ![Regisztráció](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Az engedélyezési API-val hozzáférési jogkivonat beszerzése

Miután előfizetett az engedélyezési API-t, akkor fog tudni hozzáférési tokenek beszerzése érdekében. A hozzáférési jogkivonatok segítségével hitelesítést, a műveleti API. 

Minden hívás a műveleti API hozzáférési tokent, a hitelesítési hatókör a hívás megfelelő társítva kell lennie.

- Felhasználói szintű - felhasználói hozzáférési jogkivonatok lehetővé teszik, hogy műveleteket végezhet a **felhasználói** szintjét. Például get kapcsolódó fiók.
- Fiók szintje – fiók hozzáférési jogkivonatok lehetővé teszik, hogy műveleteket végezhet a **fiók** szint vagy a **videó** szintjét. Például videó feltöltése, minden videók kilistázásához használatba videó insights, stb.
- Videó szintje – videó szintű hozzáférési jogkivonatok lehetővé teszik, hogy egy adott meg műveleteket elvégezni **videó**. Például video nyerhet, töltse le a feliratok, használatba widgeteket, stb. 

Szabályozhatja, hogy ezeket a jogkivonatokat readonly vagy azok szerkeszthető megadásával **allowEdit = true/false értéket eredményező**.

A legtöbb kiszolgálók forgatókönyvek esetén valószínűleg szüksége lesz az azonos **fiók** óta is magában foglalja a token **fiók** műveletek és **videó** műveletek. Azonban ha azt tervezi, hogy ügyféloldali hívásainak videó indexelő (pl. javascript), akkor használni kívánt egy **videó** hozzáférési jogkivonatot, hogy megakadályozza a felhasználókat a teljes fiók hozzáférést. Is ez az az oka, hogy ha VideoIndexer Ügyfélkód beágyazása az ügyfél (például az **Get Insights Widget** vagy **Player Widget beolvasása**) meg kell adnia egy **videó**hozzáférési jogkivonat.

Egyszerűbbé, használhatja a **engedélyezési** API > **GetAccounts** a fiókok nem a felhasználó jogkivonatának beszerzéséhez először. Is kérhet, lekérni a fiókok érvényes jogkivonatokkal, így lehetővé teszi egy további, a fiók tokent irányuló hívás kihagyása.

Hozzáférési jogkivonatok 1 óra után elévül. Ellenőrizze, hogy a hozzáférési jogkivonat érvénytelen a műveleti API használata előtt. Ha a lejár, az engedélyezési API újra egy új hozzáférési jogkivonat segítségével.
 
Készen áll a API integrálása elindításához. Található [minden videó indexelő REST API részletes leírása](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Hely

Az összes művelet API-k igénybe egy hely paraméter, amely megadja, hogy a régió, amely irányítani kell a hívást és a fiók létrejött.

A következő táblázat ismerteti az értékek érvényesek. A **Param érték** az API-t használ az értéket, ha át.

|**Name (Név)**|**Param érték**|**Leírás**|
|---|---|---|
|Próbaverzió|Próbaverzió|Próba fiókok használatos.|
|USA nyugati régiója|westus2|Az Azure nyugati Velünk 2 régió használatos.|
|Észak-Európa |northeurope|Használja az Észak-Európa Azure-régió.|
|Kelet-Ázsia|eastasia|Az Azure Kelet-Ázsia régió használatos.|

## <a name="account-id"></a>Fiókazonosító 

A Fiókazonosító paraméter megadása szükséges. az összes műveleti API-hívásokban. Fiók azonosítója a következő GUID érhető el a következő módszerek valamelyikével:

* A videó indexelő portál használatával a fiók beszerzése:

    1. Jelentkezzen be [videoindexer](https://www.videoindexer.ai/).
    2. Keresse meg a **beállítások** lap.
    3. Másolja a fiók azonosítójaként.

        ![Fiókazonosító](./media/video-indexer-use-apis/account-id.png)

* Használja a API-val programozott módon a fiók azonosítóját.

    Használja a [fiókok lekérése](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API.
    
    > [!TIP]
    > A fiók hozzáférési jogkivonatok definiálásával is létrehozhat `generateAccessTokens=true`.
    
* A fiók azonosító beszerzése a fiókjában player oldal URL-CÍMÉT.

    Ha a bemutató videó megtekintéséhez a azonosítója után jelenik meg a `accounts` szakasz és előtt a `videos` szakasz.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Javaslatok

Ez a szakasz néhány videó indexelő API használata esetén.

- Ha azt tervezi, hogy a videó feltöltése, javasoljuk, hogy az egyes nyilvános hálózati helyre (például a OneDrive) helyezze a fájlt. A videó hivatkozásának beszerzése, és adja meg az URL-címet a feltöltési fájl param. 

    A videó indexelőnek megadott URL-cím médiafájl (hang- vagy) kell mutatnia. A onedrive vállalati verzió által létrehozott hivatkozások vannak a fájlt tartalmazó HTML-lapot. Egy egyszerű ellenőrzése a illessze be a böngészőbe – Ha a fájl letöltése az URL-címet, valószínű helyes URL-címet. Ha a böngésző néhány képi megjelenítés megjeleníti, nincs valószínűleg egy hivatkozást egy fájlt, de a HTML-lapot.
    
- Az API-t, amely a videó insights lekérdezi a megadott videó hívásakor a válasz tartalmat kap egy részletes JSON-kimenetét. [Ebben a témakörben a visszaadott JSON vonatkozó részletes](video-indexer-output-json.md).

## <a name="code-sample"></a>Kódminta

A következő C# kódrészletet együtt bemutatja az összes a videó indexelő API-k használatát.

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

[Vizsgálja meg a kimeneti JSON részleteit](video-indexer-output-json.md).

## <a name="see-also"></a>Lásd még

[Az indexelő áttekintő videó](video-indexer-overview.md)
