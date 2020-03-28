---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: c92a3d1219358d7fe15cfe0ec93f1e720ee80af8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79082185"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=dotnet)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Az első lépés annak biztosítása, hogy a projekt meg legyen nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-et.
2. Töltse be a `Program.cs`projektet, és nyissa meg a programot.
3. Töltse le a <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav és <span class="docon docon-download x-hidden-focus"></span> </a> add hozzá a projekthez.
    - Mentse a *whatstheweatherlike.wav* fájlt `Program.cs` a fájl mellé.
    - A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza **a > meglévő elem hozzáadása parancsot.**
    - Válassza ki a *whatstheweatherlike.wav* fájlt, majd kattintson a **Hozzáadás** gombra.
    - Kattintson a jobb gombbal az újonnan hozzáadott fájlra, és válassza **a Tulajdonságok parancsot.**
    - Módosítsa a **Másolás a kimeneti könyvtárba** **mindig .**

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz. Vegye figyelembe, hogy létrehozott egy aszinkron metódust, amelynek neve `RecognizeSpeechAsync()`.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Egy `SpeechRecognizer` objektum inicializálása előtt létre kell hoznia egy konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja. Szúrja be ezt `RecognizeSpeechAsync()` a kódot a metódusba.

> [!NOTE]
> Ez a `FromSubscription()` minta a `SpeechConfig`módszerrel építi a. Az elérhető módszerek teljes listáját a [SpeechConfig Class ( Beszédkonfigurációs osztály](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)) tartalmazza.
> A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>Hangkonfiguráció létrehozása

Most létre kell hoznia egy `AudioConfig` objektumot, amely a hangfájlra mutat. Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt `RecognizeSpeechAsync()` a kódot a metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>Beszédfelismerő inicializálása

Most hozzuk létre `SpeechRecognizer` az objektumot a `SpeechConfig` korábban létrehozott és `AudioConfig` az objektumok használatával. Ez az objektum egy using utasításban is létrejön a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt `RecognizeSpeechAsync()` a kódot a metódusba, ```AudioConfig``` az objektumot körbefutó using utasításba.

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>Kifejezés felismerése

Az `SpeechRecognizer` objektumból meg kell adni `RecognizeOnceAsync()` a metódust. Ez a módszer lehetővé teszi a beszédszolgáltatás számára, hogy egyetlen kifejezést küld a felismeréshez, és hogy miután a kifejezés azonosítása után a beszéd felismerésének leállítására kerül.

A using utasításon belül adja hozzá ezt a kódot:

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>A felismerési eredmények (vagy hibák) megjelenítése

Amikor a beszédfelismerési szolgáltatás visszaadja az elismerés eredményét, érdemes valamit kezdenie vele. Egyszerűnek fogjuk tartani, és kinyomtatjuk az eredményt a konzolra.

Belül a használó `RecognizeOnceAsync()`utasítás, az alábbi , add meg ezt a kódot:

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"We recognized: {result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="check-your-code"></a>A kód ellenőrzése

Ezen a ponton a kódnak így kell kinéznie:

```csharp
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
            using (var recognizer = new SpeechRecognizer(config, audioInput))
            {
                Console.WriteLine("Recognizing first result...");
                var result = await recognizer.RecognizeOnceAsync();

                switch (result.Reason)
                {
                    case ResultReason.RecognizedSpeech:
                        Console.WriteLine($"We recognized: {result.Text}");
                        break;
                    case ResultReason.NoMatch:
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        break;
                    case ResultReason.Canceled:
                        var cancellation = CancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
                
                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                        break;
                }
            }
        }
    }
}
```

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll arra, hogy létrehozza az alkalmazást, és tesztelje a beszédfelismerést a Beszédszolgáltatás használatával.

1. A kód fordítása: A *Visual Studio*menüsorában válassza a Build Solution (Build Solution ) (Build Solution ) **(Összeállítási megoldás)** > **lehetőséget.**
2. Az alkalmazás indítása: A menüsorban válassza a Debug Start Debugging **(Hibakeresés** > **indítása)** lehetőséget, vagy nyomja le **az F5 billentyűt.**
3. Kezdő felismerés: A hangfájlt a rendszer elküldi a beszédfelismerési szolgáltatásnak, szövegként átírja, és a konzolon jeleníti meg.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
