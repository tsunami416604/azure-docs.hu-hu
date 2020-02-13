---
title: 'Gyors útmutató: hangfelismerés a hangfájlba C# (.net) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 17e5f35e83f5cb6e8115a9d344f93abf84e5b38a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77155972"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Első lépésként győződjön meg arról, hogy a projekt meg van nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-es kiadását.
2. Töltse be a projektet, és nyissa meg `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez. Jegyezze fel, hogy létrehozott egy `SynthesisToAudioFileAsync()`nevű aszinkron metódust.

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

`SpeechSynthesizer` objektum inicializálásához létre kell hoznia egy olyan konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja. Szúrja be ezt a kódot a `SynthesisToAudioFileAsync()` metódusba.

````C#
// Replace with your own subscription key and service region (e.g., "westus", use the one of SpeechSDKParameters
// from here: https://aka.ms/speech/sdkregion).
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Hang konfigurációjának létrehozása

Most létre kell hoznia egy ````AudioConfig```` objektumot, amely a hangfájlra mutat. Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt a kódot a `SynthesisToAudioFileAsync()` metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>SpeechSynthesizer inicializálása

Most hozzuk létre a `SpeechSynthesizer` objektumot a korábban létrehozott `SpeechConfig` és `AudioConfig` objektumok használatával. Ez az objektum egy using utasítás belsejében is létrejön a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt a kódot a `SynthesisToAudioFileAsync()` metódusba a using utasításban, amely a ````AudioConfig```` objektumot betakarja.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Szöveg szintetizálása SpeakTextAsync használatával

A `SpeechSynthesizer` objektumban meg kell hívnia a `SpeakTextAsync()` metódust. Ez a metódus a beszédfelismerési szolgáltatásnak küldi el a szöveget, amely átalakítja hangra. Ha a `config.VoiceName` nincs explicit módon megadva, a `SpeechSynthesizer` az alapértelmezett hangot fogja használni.

A using utasításon belül adja hozzá a következő kódot:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Hibák keresése

Ha a beszédfelismerő szolgáltatás visszaadja a szintézis eredményét, győződjön meg arról, hogy a szöveg sikeresen lett szintetizálva.

A using utasításon belül `SpeakTextAsync()`alatt adja hozzá a következő kódot:
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

1. **Fordítsa le a kódot** – a Visual Studio menüsávján válassza a **Build** > **Build megoldás**elemet.
2. **Indítsa el az alkalmazást** – a menüsávban válassza a **hibakeresés** > a **hibakeresés indítása** vagy az **F5**billentyű lenyomása lehetőséget.
3. **Szintézis indítása** – a rendszer beszédre konvertálja a szöveget, és a megadott hangadatokba menti.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
