---
title: 'Rövid útmutató: Beszédfelismerés mikrofonból, C# (.NET) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: c969b5e5daa4c4cfd84695fef70f0a2a5c50ce02
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925404"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Az első lépés annak biztosítása, hogy a projekt meg legyen nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-et.
2. Töltse be a `Program.cs`projektet, és nyissa meg a programot.

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz. Vegye figyelembe, hogy létrehozott egy aszinkron metódust, amelynek neve `RecognizeSpeechAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Egy `SpeechRecognizer` objektum inicializálása előtt létre kell hoznia egy konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja (válassza ki a **régió régiórégió régióból származó Régió azonosítót.** [region](https://aka.ms/speech/sdkregion) Szúrja be ezt `RecognizeSpeechAsync()` a kódot a metódusba.

> [!NOTE]
> Ez a `FromSubscription()` minta a `SpeechConfig`módszerrel építi a. Az elérhető módszerek teljes listáját a [SpeechConfig Class ( Beszédkonfigurációs osztály](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)) tartalmazza.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]
> A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

## <a name="initialize-a-speechrecognizer"></a>Beszédfelismerő inicializálása

Most hozzunk létre `SpeechRecognizer`egy . Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt `RecognizeSpeechAsync()` a kódot a metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Kifejezés felismerése

Az `SpeechRecognizer` objektumból meg kell adni `RecognizeOnceAsync()` a metódust. Ez a módszer lehetővé teszi a beszédszolgáltatás számára, hogy egyetlen kifejezést küld a felismeréshez, és hogy miután a kifejezés azonosítása után a beszéd felismerésének leállítására kerül.

A using utasításon belül adja hozzá ezt a kódot.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>A felismerési eredmények (vagy hibák) megjelenítése

Amikor a beszédfelismerési szolgáltatás visszaadja az elismerés eredményét, érdemes valamit kezdenie vele. Egyszerűnek fogjuk tartani, és kinyomtatjuk az eredményt a konzolra.

Belül a using `RecognizeOnceAsync()`utasítás, az alábbi , add ezt a kódot.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>A kód ellenőrzése

Ezen a ponton, a kódot kell kinéznie.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll arra, hogy létrehozza az alkalmazást, és tesztelje a beszédfelismerést a Beszédszolgáltatás használatával.

1. **A kód fordítása** - A Visual Studio menüsorából válassza a Build Solution **(Build** > Solution )**lehetőséget.**
2. **Az alkalmazás indítása** – A menüsorban válassza a Debug Start Debugging **(Hibakeresés** > **indítása)** lehetőséget, vagy nyomja le az **F5 billentyűt**.
3. **Start felismerés** - Ez kérni fogja, hogy beszéljen egy mondatot angolul. A rendszer elküldi a beszédfelismerési szolgáltatást, szövegként átírja, és megjeleníti a konzolon.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
