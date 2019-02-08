---
title: Batch Beszédátírási – beszédszolgáltatások használata
titlesuffix: Azure Cognitive Services
description: Batch beszédátírási ideális, ha azt szeretné, a storage szolgáltatással, például az Azure-Blobok hang nagy mennyiségű lefényképezze. A dedikált REST API használatával hangfájlok egy közös hozzáférésű jogosultságkód (SAS) URI-mutasson, és aszinkron módon fogadni az beszédátírás.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 0e03c388dac4a70fc45150287154406551ac2672
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867120"
---
# <a name="why-use-batch-transcription"></a>Miért érdemes használni a Batch beszédátírási?

Batch beszédátírási ideális, ha azt szeretné, a storage szolgáltatással, például az Azure-Blobok hang nagy mennyiségű lefényképezze. A dedikált REST API használatával hangfájlok egy közös hozzáférésű jogosultságkód (SAS) URI-mutasson, és aszinkron módon fogadni az beszédátírás.

>[!NOTE]
> Beszédszolgáltatások (S0) standard előfizetést kell használnia a batch beszédátírási. Ingyenes előfizetési kulcsok (F0) nem fog működni. További információkért lásd: [díjszabás és korlátok](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

## <a name="the-batch-transcription-api"></a>A Batch Beszédátírási API

A Batch Beszédátírási API aszinkron hang-szöveg transzformációs átírást, valamint olyan kiegészítő funkciókat kínál. Egy REST API-t, amely metódusokat tárja fel:

1. Kötegelt feldolgozási kérelmek létrehozása
1. Lekérdezés állapota
1. Beszédátírás letöltése

> [!NOTE]
> A Batch Beszédátírási API telefonos ügyfélszolgálatok, amely általában a több ezer órás hanganyagra gyűlnek ideális. Az API-t, amely megkönnyíti a nagy mennyiségű hanganyag lefényképezze egy "indul el, és felejtse el" filozófia vezérli.

### <a name="supported-formats"></a>Támogatott formátumok

A Batch Beszédátírási API támogatja a következő formátumok:

| Formátum | Kodek | Átviteli sebesség | Mintavételi frekvencia |
|--------|-------|---------|-------------|
| WAV | A PCM | 16-bit | 8 vagy 16 kHz, Monó, sztereó |
| MP3 | A PCM | 16-bit | 8 vagy 16 kHz, Monó, sztereó |
| OGG | OPUS | 16-bit | 8 vagy 16 kHz, Monó, sztereó |

> [!NOTE]
> A Batch Beszédátírási API egy (a szint már fizetőssé) S0 kulcs szükséges. Ingyenes (f0) kulccsal nem működik.

Sztereó audiostreamek lejátszásával, a a Batch API beszédátírási bontja a bal és jobb csatorna a beszédátírási során. A két JSON-fájlok az eredmény az egyes jönnek létre egyetlen csatornákon. Az utterance (kifejezés) / időbélyegeket köszönhetően a fejlesztő hozzon létre egy rendezett végleges átiratok. A következő JSON mutatja egy mintakérelmet includuing tulajdonságok beállításához a vulgáris szűrésére, az írásjelek modell és a word-szintű időbélyegek

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  },
```

> [!NOTE]
> A Batch Beszédátírási API egy REST-szolgáltatás beszédátírás, állapotát és kapcsolódó eredmények kérő használja. Az API bármilyen nyelvet is használhat. Ez a szakasz azt ismerteti, hogyan használja fel az API-t.

### <a name="query-parameters"></a>Lekérdezési paraméterek

Ezeket a paramétereket a lekérdezési karakterláncban a REST-kérés szerepelhet.

| Paraméter | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Adja meg a felismerési eredményeket cenzúrázása kezelése. Elfogadott értékek a következők `none` amely letiltja a vulgáris szűréshez `masked` csillag, amely lecseréli cenzúrázása `removed` amely eltávolítja az összes cenzúrázása az eredményből vagy `tags` ami ad "cenzúrázása" címkék. Az alapértelmezett beállítás `masked`. | Optional |
| `PunctuationMode` | Adja meg a felismerési eredményeket írásjelek kezelése. Elfogadott értékek a következők `none` ami letiltja az absztrakt, `dictated` explicit írásjelek, amiből `automatic` , amellyel a dekóder írásjelek, kezelése vagy `dictatedandautomatic` írásjelek vagy automatikus azt jelenti, amely során. | Optional |


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
> Nem biztosítunk szolgáltatásszint-szerződéssel idő számára audiotartalmak trascriptions batch keresztül. Azonban ha a beszédátírási feladat actioned (a futó állapotban), typially feldolgozása gyorsabb, mint a valós idejű.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
