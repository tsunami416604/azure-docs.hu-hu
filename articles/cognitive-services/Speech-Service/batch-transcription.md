---
title: Az Azure Batch Beszédátírási API
description: Példák
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: f21973855ceb3a257627c147490ac50465c54020
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281939"
---
# <a name="batch-transcription"></a>Kötegelt átírás

Batch beszédátírási ideális a nagy mennyiségű hang, használati esetek. Lehetővé teszi a fejlesztői hangfájlok pontjára, majd beszédátírás aszinkron módban.

## <a name="batch-transcription-api"></a>A Batch beszédátírási API

A Batch beszédátírási API lehetővé teszi a fenti forgatókönyv. Aszinkron beszédfelismerés szöveg beszédátírási valamint olyan kiegészítő funkciókat kínál.

> [!NOTE]
> A Batch API beszédátírási telefonos ügyfélszolgálatok, amely általában a több ezer órás hanganyagra gyűlnek ideális. Az API Fire & Törlés filozófia megkönnyíti a hanganyag nagy mennyiségű lefényképezze.

### <a name="supported-formats"></a>Támogatott formátumok

A Batch API beszédátírási célja, hogy minden kapcsolat nélküli hívás center kapcsolatos forgatókönyvek esetén, de-facto válnak, és minden kapcsolódó formátumokhoz támogatást. Jelenleg támogatott formátumok:

Name (Név)| Csatorna  |
----|----------|
MP3 |   Mono   |   
MP3 |  Sztereó  | 
WAV |   Mono   |
WAV |  Sztereó  |

Sztereó audiostreamek lejátszásával, a Batch beszédátírási során az átírási kettéosztjuk a bal és jobb csatorna. A két JSON-fájlok az eredmény az egyes jönnek létre egyetlen csatornákon. Az utterance (kifejezés) / időbélyegeket köszönhetően a fejlesztő hozzon létre egy rendezett végleges átiratok. A következő JSON-mintát csatorna kimenetét mutatja be.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> A Batch beszédátírási API egy REST-szolgáltatás beszédátírás, állapotát és kapcsolódó eredmények kérő használ. Az API bármilyen nyelv használható. Ez a szakasz azt ismerteti, hogyan használja fel azokat.

## <a name="authorization-token"></a>Engedélyezési jogkivonat

Mivel az összes funkciót, az egyesített Speech Service, a felhasználónak szüksége van egy előfizetési kulcsot, hozzon létre a [az Azure portal](https://portal.azure.com). Emellett egy API-kulcsot kell szerezni a Speech-portál. API-kulcs létrehozására vonatkozó lépéseket:

1. Jelentkezzen be https://customspeech.ai.

2. Kattintson az előfizetések.

3. Kattintson a beállítás a `Generate API Key`.

    ![A feltöltés megtekintése](media/stt/Subscriptions.jpg)

4. Másolja és illessze be az alábbi példában az ügyfélkód a kulcs.

> [!NOTE]
> Ha azt tervezi, hogy egyéni modellt kell, hogy a modell azonosítója túl. Vegye figyelembe, hogy ez nem talált a végpont részleteket megjelenítő nézetet, de a Modellazonosító, amely a modell adatait kattintva kérheti az üzembe helyezési vagy Hálózativégpont-azonosító

## <a name="sample-code"></a>Mintakód

Az API-t használja a viszonylag nagyon egyszerű. Igényeinek megfelelően testre kell szabni az egy előfizetési kulcsot és a egy API-kulcs, amely viszont lehetővé teszi, hogy a fejlesztő tulajdonosi jogkivonattal, mint a következő kódrészletet beszerzése alábbi mintakód bemutatja:

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

A token beszerzését követően a fejlesztő kell megadnia a SAS URI-t igénylő beszédátírási hangfájl mutató. A kód a többi egyszerűen végighalad az állapot és eredményeket jeleníti meg.

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> Az előfizetési kulcs már említettük, a fenti kódrészletben a kulcsát, az Azure Portalon létrehozott Speech(Preview) erőforrás. A Custom Speech Service erőforrás származó kulcsok nem működnek.


Figyelje meg, hogy az aszinkron beállítás könyvelési hang és a fogadás beszédátírási állapot. Az ügyfél létrehozása a .NET-Http-alapú. Van egy `PostTranscriptions` metódus küld el a hangfájl részleteit, és a egy `GetTranscriptions` metódus az eredmények. `PostTranscriptions` adja vissza egy leírót és `GetTranscriptions` módszer hozható létre lekérni beszédátírási állapotát leíró használata ezt az azonosítót.

A jelenlegi mintakód nem ad meg minden olyan egyéni modellek. A szolgáltatás fájl(ok) lefényképezheti a kiindulási modelleket alkalmazhatnak fogja használni. Ha a felhasználó által blokkolni a modellek meghatározásához, egy továbbíthatja meg ugyanezt a módszert a modelIDs az akusztikai és a nyelvi modell. 

Ha egy alapterv használata nem szeretne, egy modellazonosítóját egyaránt akusztikai és nyelvi modellek át kell adnia.

> [!NOTE]
> Az alapkonfiguráció beszédátírási a felhasználó nem rendelkezik a kiindulási modelleket alkalmazhatnak végpontjainak deklarálnia. Ha a felhasználó szeretne egyéni modellek használata azt kell biztosítania a végpontok azonosítók, mint a [minta](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Ha a felhasználó szeretne egy akusztikai alapterv használata egy alapkonfiguráció nyelvi modell majd ő csak kellene deklarálja a egyéni modell végpontja azonosítója. Belsőleg rendszerben döntse el, a partner (lehet, akusztikai és nyelvi) modell, és használja, amely a beszédátírási kérelem teljesítéséhez.

### <a name="supported-storage"></a>Tároló

A támogatott csak tárolási jelenleg Azure-blobba.

## <a name="downloading-the-sample"></a>A minta letöltése

A rendszer az itt látható minta [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> A hanganyag átírása legtöbbször megegyezik a hangfájl és a egy 2-3 perces terhelést időtartama meg azt az időtartományt.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
