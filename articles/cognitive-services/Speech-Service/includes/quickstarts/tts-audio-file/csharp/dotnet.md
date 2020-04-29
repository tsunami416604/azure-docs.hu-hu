---
title: 'Gyors útmutató: beszédfelismerés hangfájlba való szintézise, C# (.NET) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: erhopf
ms.openlocfilehash: d3d0a807e8742419ee321f38daebf6f70d6b0be1
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274747"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Első lépésként győződjön meg arról, hogy a projekt meg van nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-es kiadását.
2. Töltse be a projektet, `Program.cs`és nyissa meg.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez. Jegyezze fel, hogy létrehozta a nevű `SynthesisToAudioFileAsync()`aszinkron metódust.

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Az `SpeechSynthesizer` objektumok inicializálásához létre kell hoznia egy olyan konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja. Szúrja be ezt a `SynthesisToAudioFileAsync()` kódot a metódusba.

````C#
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Hang konfigurációjának létrehozása

Most létre kell hoznia egy ````AudioConfig```` objektumot, amely a hangfájlra mutat. Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt a `SynthesisToAudioFileAsync()` kódot a metódusba közvetlenül a beszédfelismerési konfiguráció alatt.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>SpeechSynthesizer inicializálása

Most hozzuk létre az `SpeechSynthesizer` objektumot a `SpeechConfig` korábban létrehozott és `AudioConfig` objektumok használatával. Ez az objektum egy using utasítás belsejében is létrejön a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt a `SynthesisToAudioFileAsync()` kódot a metódusba a using utasításban, amely ````AudioConfig```` az objektumot betakarja.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Szöveg szintetizálása SpeakTextAsync használatával

Az `SpeechSynthesizer` objektumból meg fogja hívni a `SpeakTextAsync()` metódust. Ez a metódus a beszédfelismerési szolgáltatásnak küldi el a szöveget, amely átalakítja hangra. A `SpeechSynthesizer` az alapértelmezett hangot fogja használni, `config.VoiceName` ha nincs explicit módon megadva.

A using utasításon belül adja hozzá a következő kódot:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Hibák keresése

Ha a beszédfelismerő szolgáltatás visszaadja a szintézis eredményét, győződjön meg arról, hogy a szöveg sikeresen lett szintetizálva.

Az alábbi `SpeakTextAsync()`using utasításban adja hozzá a következő kódot:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás létrehozására és a beszédfelismerési szolgáltatás használatával történő tesztelésére.

1. **Fordítsa le a kódot** – a Visual Studio menüsávján válassza a **Build** > **megoldás**létrehozása lehetőséget.
2. **Indítsa** el az alkalmazást – a menüsávban **válassza** > a hibakeresés**indítása** vagy az **F5**billentyűt.
3. **Szintézis indítása** – a rendszer beszédre konvertálja a szöveget, és a megadott hangadatokba menti.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
