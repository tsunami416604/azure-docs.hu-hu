---
title: Az Azure Batch beszédátírási API
description: Példák
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 02af95859bcbdc3dd9fdd6d6354cae9cdf99eae8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717947"
---
# <a name="batch-transcription"></a>Kötegelt átírás

Batch beszédátírási ideális, ha nagy mennyiségű hang rendelkezik. Hangfájlok mutasson, és aszinkron módú beszédátírás visszaszerzésében.

## <a name="batch-transcription-api"></a>A Batch beszédátírási API

A Batch beszédátírási API aszinkron beszédfelismerés szöveg átírást, valamint olyan kiegészítő funkciókat kínál.

> [!NOTE]
> A Batch beszédátírási API telefonos ügyfélszolgálatok, amely általában a több ezer órás hanganyagra gyűlnek ideális. Az API-t, amely megkönnyíti a nagy mennyiségű hanganyag lefényképezze egy "indul el, és felejtse el" filozófia vezérli.

### <a name="supported-formats"></a>Támogatott formátumok

A Batch beszédátírási API támogatja a következő formátumok:

Name (Név)| Csatorna  |
----|----------|
MP3 |   Mono   |   
MP3 |  Sztereó  | 
WAV |   Mono   |
WAV |  Sztereó  |

Sztereó audiostreamek lejátszásával, a Batch beszédátírási bontja a bal és jobb csatorna a beszédátírási során. A két JSON-fájlok az eredmény az egyes jönnek létre egyetlen csatornákon. Az utterance (kifejezés) / időbélyegeket köszönhetően a fejlesztő hozzon létre egy rendezett végleges átiratok. A következő JSON-mintát csatorna kimenetét mutatja be.

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
> A Batch beszédátírási API egy REST-szolgáltatás beszédátírás, állapotát és kapcsolódó eredmények kérő használ. Az API bármilyen nyelvet is használhat. Ez a szakasz azt ismerteti, hogyan használja fel azokat.

## <a name="authorization-token"></a>Engedélyezési jogkivonat

Az összes funkciót, az egyesített beszédszolgáltatás, egy előfizetési kulcsot a létrehozásakor a [az Azure portal](https://portal.azure.com). 6 egyszerű lépések végrehajtásával.

1. Létrehozott egy előfizetési kulcsot az Azure a következőket a [első lépések útmutató](get-started.md) 

2. Jelentkezzen be a [Custom Speech](https://customspeech.ai).

3. Válassza az **Előfizetések** lehetőséget.

4. Válassza ki **csatlakozás meglévő előfizetés**.

5. Az előfizetési kulcsot és a egy alias hozzáadása a felugró nézetben

    ![Képernyőkép az egyéni beszédfelismerési előfizetések lap](media/stt/Subscriptions.jpg)

6. Másolja és illessze be ezt a kulcsot a következő mintában az Ügyfélkód.

> [!NOTE]
> Ha azt tervezi, a egyéni modell, túl kell, hogy a modell azonosítója. Vegye figyelembe, hogy ez nem talált a végpont részletei nézeten hálózativégpont-azonosító. A Modellazonosító lekérhető a modell adatait kiválasztásakor.

## <a name="sample-code"></a>Mintakód

Testre szabhatja az alábbi mintakód egy előfizetési kulcsot és a egy API-kulcsot. Ez lehetővé teszi, hogy tulajdonosi jogkivonat beszerzése.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
         
            return new CrisClient(client);
        }
```

Miután beszerezte a jogkivonatot, meg kell adnia a SAS URI-t igénylő beszédátírási hangfájl mutat. A többi, a kód végighalad az állapotát, és eredményeit jeleníti meg.

```cs
   static async Task TranscribeAsync()
        { 
            private const string SubscriptionKey = "<your Speech[Preview] subscription key>";
            private const string HostName = "cris.ai";
            private const int Port = 443;
    
            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);

            // get the transcription Id from the location URI
            var createdTranscriptions = new List<Guid>();
            createdTranscriptions.Add(new Guid(transcriptionLocation.ToString().Split('/').LastOrDefault()))

            while (true)
            {
                // get all transcriptions for the user
                transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);
                completed = 0; running = 0; notStarted = 0;

                // for each transcription in the list we check the status
                foreach (var transcription in transcriptions)
                {
                    switch(transcription.Status)
                    {
                        case "Failed":
                        case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                            if (!createdTranscriptions.Contains(transcription.Id))
                            {
                                // not creted form here, continue
                                continue;
                            }
                            
                            completed++;
                            
                            // if the transcription was successfull, check the results
                            if (transcription.Status == "Succeeded")
                            {
                                var resultsUri = transcription.ResultsUrls["channel_0"];
                                WebClient webClient = new WebClient();
                                var filename = Path.GetTempFileName();
                                webClient.DownloadFile(resultsUri, filename);
                                var results = File.ReadAllText(filename);
                                Console.WriteLine("Transcription succedded. Results: ");
                                Console.WriteLine(results);
                            }
                            break;
                        case "Running":
                            running++;
                            break;
                        case "NotStarted":
                            notStarted++;
                            break;
                    }
                }

                Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.WriteLine("Press any key...");
        }
```

> [!NOTE]
> A fenti kóddal, a az előfizetési kulcs van, az Azure Portalon létrehozott Speech(Preview) erőforrásból. A Custom Speech Service erőforrás származó kulcsok nem működnek.

Figyelje meg, hogy az aszinkron beállítás könyvelési hang és a fogadás beszédátírási állapot. Az ügyfél létrehozása a .NET-Http-alapú. Van egy `PostTranscriptions` metódus küld el a hangfájl részleteit, és a egy `GetTranscriptions` metódus az eredmények. `PostTranscriptions` a fogópont adja vissza és `GetTranscriptions` hozható létre lekérni beszédátírási állapotát leíró ezt az azonosítót használja.

A jelenlegi mintakód nem ad meg minden olyan egyéni modellek. A szolgáltatás használ a kiindulási modelleket alkalmazhatnak lefényképezheti a fájlt vagy fájlokat. Adja meg a modellek, is át kell adnia az ugyanezzel a módszerrel a nyelvi modell, és az akusztikai modell azonosítóját. 

Ha nem szeretné használni az alaptervhez, akkor meg kell adnia modellazonosítóját egyaránt akusztikai és nyelvi modellek esetén.

> [!NOTE]
> Alapkonfiguráció beszédátírási nem rendelkezik a kiindulási modelleket alkalmazhatnak végpontjainak deklarálható. Egyéni modellek használni kívánt, ha megadta a végpontok azonosítók, mint a [minta](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Ha szeretne egy akusztikai alapterv használata egy alapkonfiguráció nyelvi modell, csak kell deklarálnia az egyéni modell végpontja azonosítója. A Microsoft észleli a partner alapkonfiguráció modell (lehet, akusztikai és nyelvi), és használja, amely a beszédátírási kérelem teljesítéséhez.

### <a name="supported-storage"></a>Tároló

A támogatott csak tárolási jelenleg az Azure Blob storage.

## <a name="downloading-the-sample"></a>A minta letöltése

A rendszer az itt látható minta [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> A hanganyag átírása legtöbbször megegyezik a hangfájl, valamint egy 2-3 perces terhelést időtartama meg azt az időtartományt.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
