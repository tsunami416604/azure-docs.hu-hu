---
title: Az Azure Batch Beszédátírási API használata
titlesuffix: Azure Cognitive Services
description: Nagy mennyiségű hanganyaga átírás minták.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: cd57e9a90b07447392fbff48017bb29f002ad29e
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035951"
---
# <a name="use-batch-transcription"></a>A batch beszédátírási használata

Batch beszédátírási ideális, ha nagy mennyiségű hang storage-ban. A REST API használatával hangfájlok átirányítása a közös hozzáférésű jogosultságkód (SAS) URI-t, és aszinkron módon fogadni az beszédátírás.

## <a name="the-batch-transcription-api"></a>A Batch Beszédátírási API

A Batch Beszédátírási API aszinkron hang-szöveg transzformációs átírást, valamint olyan kiegészítő funkciókat kínál. Egy REST API-t, amely metódusokat tárja fel:

1. Kötegelt feldolgozási kérelmek létrehozása
1. Lekérdezés állapota 
1. Beszédátírás letöltése

> [!NOTE]
> A Batch Beszédátírási API telefonos ügyfélszolgálatok, amely általában a több ezer órás hanganyagra gyűlnek ideális. Az API-t, amely megkönnyíti a nagy mennyiségű hanganyag lefényképezze egy "indul el, és felejtse el" filozófia vezérli.

### <a name="supported-formats"></a>Támogatott formátumok

A Batch Beszédátírási API támogatja a következő formátumok:

Name (Név)| Csatorna  |
----|----------|
MP3 |   Mono   |   
MP3 |  Sztereó  | 
WAV |   Mono   |
WAV |  Sztereó  |
Opus|   Mono   |
Opus|  Sztereó  |

Sztereó audiostreamek lejátszásával, a batch beszédátírási bontja a bal és jobb csatorna a beszédátírási során. A két JSON-fájlok az eredmény az egyes jönnek létre egyetlen csatornákon. Az utterance (kifejezés) / időbélyegeket köszönhetően a fejlesztő hozzon létre egy rendezett végleges átiratok. A kimenet egy csatorna, beleértve a profanitásszűrőjének és a központozási modell beállításának tulajdonságai a következő JSON-mintában látható:

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> A Batch Beszédátírási API egy REST-szolgáltatás beszédátírás, állapotát és kapcsolódó eredmények kérő használja. Az API bármilyen nyelvet is használhat. Ez a szakasz azt ismerteti, hogyan használja fel az API-t.

## <a name="authorization-token"></a>Engedélyezési jogkivonat

A beszédfelismerési szolgáltatás összes funkcióját, és egy előfizetési kulcsot a létrehozásakor a [az Azure portal](https://portal.azure.com) a következő a [útmutató első lépésekhez](get-started.md). Ha azt tervezi, a beszédátírás kérhet az eredeti modellt, a kulcs létrehozása még minden kell tennie. 

Ha azt tervezi, testreszabása és a egy egyéni modellt használja, hozzá az előfizetési kulcsot a custom speech-portál a következő módon:

1. Jelentkezzen be a [Custom Speech](https://customspeech.ai).

2. Jobb felső sarokban, válassza ki a **előfizetések**.

3. Válassza ki **csatlakozás meglévő előfizetés**.

4. Az előugró ablakban adja hozzá az előfizetési kulcsot és a egy aliast.

    ![Az előfizetés hozzáadása ablak](media/stt/Subscriptions.jpg)

5. Másolja és illessze be ezt a kulcsot a következő mintában az Ügyfélkód.

> [!NOTE]
> Ha azt tervezi, a egyéni modell, túl kell, hogy a modell azonosítója. Ez az azonosító nem a végpont-azonosító a végpont részletei nézeten talált. A Modellazonosító lekérhető a modell adatait kiválasztásakor.

## <a name="sample-code"></a>Mintakód

Testre szabhatja az alábbi mintakód egy előfizetési kulcsot és a egy API-kulcsot. Ez a művelet lehetővé teszi egy tulajdonosi jogkivonat beolvasása.

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

Miután a jogkivonatot kap, adja meg a SAS URI-t, hogy a hangfájl beszédátírási igénylő mutat. A többi, a kód végighalad a állapotát, és megjeleníti az eredményeket. Először beállíthatja a kulcsot, régió, modellek használata, és a biztonsági Társítás az alábbi kódrészletben látható módon. Következő lépésként hozza létre az ügyfél és a POST-kérés. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Most, hogy a kérelem végrehajtott, lekérdezheti, és töltse le az átírási eredmények az alábbi kódrészletben látható módon:

```cs
  
            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

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
                            // not created from here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
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
            }
        }
```

Részletes információ az előző hívás, tekintse meg a [swagger-dokumentumok](https://westus.cris.ai/swagger/ui/index). Itt látható a teljes minta, nyissa meg [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> A fenti kóddal, a az előfizetési kulcs van, a beszéd erőforrás, amely az Azure Portalon hoz létre. A Custom Speech Service erőforrás kap kulcsok nem működnek.

Jegyezze fel a könyvelési hang és a fogadás beszédátírási állapot aszinkron beállítása. Az ügyfél, amely létrehoz egy .NET-HTTP-alapú. Van egy `PostTranscriptions` módszer küldi a hangfájl részleteit és a egy `GetTranscriptions` metódus az eredményeket fogadására. `PostTranscriptions` a fogópont adja vissza és `GetTranscriptions` annak használatával hozzon létre egy leírót beszédátírási állapotának beolvasása.

A jelenlegi mintakód nem adja meg egy egyéni modell. A szolgáltatás használ a kiindulási modelleket alkalmazhatnak lefényképezheti a fájlt vagy fájlokat. A modellek megadásához meg ugyanezt a módszert, a modell azonosítóját a akusztikai és a nyelvi modell adhat át. 

Ha nem szeretné használni az alaptervhez, írjon be modellazonosítóját egyaránt akusztikai és nyelvi modellek esetén.

> [!NOTE]
> Alapkonfiguráció beszédátírás nem rendelkezik a kiindulási modelleket alkalmazhatnak végpontjainak deklarálható. Egyéni modellek használni kívánt, ha megadta a végpontok azonosítók, mint a [minta](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Ha szeretne egy akusztikai alapterv használata egy alapkonfiguráció nyelvi modell, deklarálnia csak a egyéni modell végpontja azonosítóval rendelkezik A Microsoft észleli a partner modell&mdash;e akusztikai és nyelvi&mdash;, és használja a beszédátírási kérés teljesítéséhez.

### <a name="supported-storage"></a>Tároló

A támogatott csak tárolási jelenleg az Azure Blob storage.

## <a name="download-the-sample"></a>A minta letöltése

Találhatja meg a mintában Ez a cikk a [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> A hanganyag átírása általában szükséges a hangfájl, valamint a két - a három perces terhelést időtartama annál meg azt az időtartományt.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
