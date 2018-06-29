---
title: Az Azure Batch írjanak elő API |} Az Azure Microsoft Docs
description: Példák
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: cf58f676be52aa16ce6de59c3566613c7ee9276d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084082"
---
# <a name="batch-transcription"></a>Kötegelt írjanak elő

Kötegelt írjanak elő a használati esetek hang nagy mennyiségű ideális. Lehetővé teszi a fejlesztői hangfájlok mutasson, és vissza transcriptions aszinkron módban.

## <a name="batch-transcription-api"></a>Kötegelt írjanak elő API

A kötegelt írjanak elő API lehetővé teszi a fenti forgatókönyv. Aszinkron beszédfelismerés szöveg írjanak elő, valamint az olyan további funkciókat kínál.

> [!NOTE]
> A kötegelt API-írjanak elő telefonos ügyfélszolgálatok, amely általában a hang órányi ezer felhalmozhat ideális. Tűz & Törlés alapvetően az API-t az megkönnyíti a nagy mennyiségű hangfelvételei átírni.

### <a name="supported-formats"></a>Támogatott formátumok

A kötegelt API-írjanak elő az a célja, hogy minden offline hívás center kapcsolatos forgatókönyvek esetén, de-facto válni, és minden kapcsolódó formátumok támogatása lehetővé. Jelenleg támogatott formátumok:

Name (Név)| Csatorna  |
----|----------|
MP3 |   Monó   |   
MP3 |  Sztereó  | 
WAV |   Monó   |
WAV |  Sztereó  |

Sztereó hang adatfolyamok kötegelt írjanak elő felosztja a bal és jobb csatorna során írjanak elő. A két JSON-fájlok eredményeképpen egyes készített egy csatornán. / Utterance időbélyegeket a fejlesztő hozzon létre egy rendezett végső Beszélgetés szövegének engedélyezése. A következő JSON-mintát csatorna mutatja.

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
> A kötegelt API-írjanak elő az transcriptions, azok állapotát és a kapcsolódó eredmények igénylő REST-szolgáltatást használ. A .NET alapul, és nem rendelkezik külső függőségeit. A következő szakasz ismerteti, hogyan használja fel azokat.

## <a name="authorization-token"></a>Engedélyezési jogkivonat

Az egyesített beszéd szolgáltatás összes funkcióját, a felhasználói igények az Előfizetés kulcs létrehozására a [Azure-portálon](https://portal.azure.com). Ezenkívül API-kulcs kell a beszédfelismerés portálról lehet lekérni. A lépéseket egy API-kulcs létrehozása:

1. Jelentkezzen be https://customspeech.ai.

2. Kattintson az előfizetések.

3. Kattintson a beállítás a `Generate API Key`.

    ![A feltöltés megtekintése](media/stt/Subscriptions.jpg)

4. Másolással illessze be a kulcs az alábbi minta az ügyfél-kódban.

> [!NOTE]
> Ha tervezi olyan egyéni modellt használnak majd szüksége lesz a modell azonosítója túl. Vegye figyelembe, hogy ez nem talált a végpont részletes nézete, de a modell azonosítója, amely amikor rákattint az, hogy a modell adatait kérheti le a központi telepítés vagy Hálózativégpont-azonosító

## <a name="sample-code"></a>Mintakód

Az API felhasználásával viszonylag közvetlen van. Az alábbi példakód kell egy előfizetési és API-kulcs, ami viszont lehetővé teszi, hogy a fejlesztő szerezze be a tulajdonosi jogkivonattal, a következő kódrészletben látható kód kódú szabható testre:

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

A token beszerzését követően a fejlesztőnek el kell végeznie eszközhozzáférés írjanak elő igénylő hangfájl mutató SAS Uri. A kód a többi egyszerűen telepítéseket állapotát, és eredményeit jeleníti meg.

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
> Az Előfizetés kulcs szerepel a fenti kódrészletet a kulcsát, az Ön által létrehozott Azure-portál Speech(Preview) erőforrás. Az egyéni beszéd szolgáltatás erőforrásból kérhetők le billentyűk nem fog működni.


Figyelje meg a könyvelési hang, és írjanak elő állapot fogadásakor aszinkron telepítőprogramja. Az ügyfél létrehozott egy új Hálózathasználati Http-ügyfél. Van egy `PostTranscriptions` módszere a hangfájl részleteit, és egy `GetTranscriptions` metódust az eredményeket. `PostTranscriptions` a leíró adja vissza és `GetTranscriptions` metódus Ez a leíró segítségével hozható létre lekérni írjanak elő állapotát leíró.

Az aktuális mintakód adjon meg egyéni modellekkel. A szolgáltatás katódsugárcsövön az (oka) t a baseline modellek használni fog. Ha a felhasználó által a modellek adhatja meg, egy teljen meg ugyanezt a módszert a akusztikus és a nyelvi modell modelIDs. 

Ha egy nem kívánja használni az eredeti, egy meg kell felelnie a modellazonosítóját akusztikus és a nyelvi modellek esetén.

> [!NOTE]
> Alaptervhez írjanak elő a felhasználó nem rendelkezik a végpontok a baseline modellek deklarálnia. Ha a felhasználó egyéni modellek használni kívánja azt kell biztosítania a végpontok azonosítók, mint a [minta](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Ha a felhasználó eredeti nyelvi modell egy akusztikus alapterv használni kívánja majd ő csak kell deklarálnia az egyéni modell végpont azonosítóját. Belső rendszerünkben mérje fel, a partner modell (lehet, akusztikus vagy nyelvi), és használja a írjanak elő kérés teljesítése érdekében.

### <a name="supported-storage"></a>Támogatott tárolási

Az egyetlen támogatott tárolási jelenleg az Azure blob.

## <a name="downloading-the-sample"></a>A minta letöltése

A rendszer itt jelenik meg a minta [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Egy hang írjanak elő általában az a hangfájl és a 2-3 percet terhelés időtartama egyenlő időtartama igényelnek.

## <a name="next-steps"></a>További lépések

* [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
