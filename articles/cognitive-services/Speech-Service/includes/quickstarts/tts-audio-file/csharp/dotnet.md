---
title: 'Rövid útmutató: Beszéd szintezése hangfájllá, C# (.NET) - Beszédszolgáltatás'
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
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274747"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Az első lépés annak biztosítása, hogy a projekt meg legyen nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-et.
2. Töltse be a `Program.cs`projektet, és nyissa meg a programot.

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz. Vegye figyelembe, hogy létrehozott egy aszinkron metódust, amelynek neve `SynthesisToAudioFileAsync()`.

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

Egy `SpeechSynthesizer` objektum inicializálása előtt létre kell hoznia egy konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja. Szúrja be ezt `SynthesisToAudioFileAsync()` a kódot a metódusba.

````C#
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Hangkonfiguráció létrehozása

Most létre kell hoznia egy ````AudioConfig```` objektumot, amely a hangfájlra mutat. Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt `SynthesisToAudioFileAsync()` a kódot a metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>SpeechSynthesizer inicializálása

Most hozzuk létre `SpeechSynthesizer` az objektumot a `SpeechConfig` korábban létrehozott és `AudioConfig` az objektumok használatával. Ez az objektum egy using utasításban is létrejön a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt `SynthesisToAudioFileAsync()` a kódot a metódusba, ````AudioConfig```` az objektumot körbefutó using utasításba.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Szöveg szintetizálása a SpeakTextAsync használatával

Az `SpeechSynthesizer` objektumból meg kell adni `SpeakTextAsync()` a metódust. Ez a módszer elküldi a szöveget a beszédfelismerési szolgáltatásnak, amely hanggá alakítja. Az `SpeechSynthesizer` alapértelmezett hangot fogja `config.VoiceName` használni, ha nincs kifejezetten megadva.

A using utasításon belül adja hozzá ezt a kódot:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Hibák ellenőrzése

Amikor a beszédfelismerési szolgáltatás visszaadja a szintézis eredményt, ellenőrizze, hogy a szöveg szintetizált-e.

Belül a használó `SpeakTextAsync()`utasítás, az alábbi , add meg ezt a kódot:
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

## <a name="check-your-code"></a>A kód ellenőrzése

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

Most már készen áll az alkalmazás készítésére és a beszédszintézis tesztelésére a Beszédszolgáltatás használatával.

1. **A kód fordítása** - A Visual Studio menüsorából válassza a Build Solution **(Build** > Solution )**lehetőséget.**
2. **Az alkalmazás indítása** – A menüsorban válassza a Debug Start Debugging **(Hibakeresés** > **indítása)** lehetőséget, vagy nyomja le az **F5 billentyűt**.
3. **Szintézis indítása** – A szöveg beszédmé alakul, és a megadott hangadatokba kerül.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
