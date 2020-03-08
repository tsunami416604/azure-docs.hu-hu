---
title: 'Rövid útmutató: beszéd felismerése mikrofonból C# (.net) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 6d32bb6a0f1428a57f504cb7aca6ca5d89ea4c52
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669019"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md?tabs=dotnet)
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Első lépésként győződjön meg arról, hogy a projekt meg van nyitva a Visual Studióban.

1. Indítsa el a Visual Studio 2019-es kiadását.
2. Töltse be a projektet, és nyissa meg `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez. Jegyezze fel, hogy létrehozott egy `RecognizeSpeechAsync()`nevű aszinkron metódust.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

`SpeechRecognizer` objektum inicializálásához létre kell hoznia egy olyan konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja (válassza ki a régió **azonosítóját** a [régióból](https://aka.ms/speech/sdkregion). Szúrja be ezt a kódot a `RecognizeSpeechAsync()` metódusba.

> [!NOTE]
> Ez a példa a `FromSubscription()` metódust használja a `SpeechConfig`létrehozásához. Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]
> A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

## <a name="initialize-a-speechrecognizer"></a>SpeechRecognizer inicializálása

Most hozzon létre egy `SpeechRecognizer`. Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt a kódot a `RecognizeSpeechAsync()` metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Kifejezés felismerése

A `SpeechRecognizer` objektumban meg kell hívnia a `RecognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

A using utasításon belül adja hozzá ezt a kódot.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményt a konzolon.

A using utasításon belül `RecognizeOnceAsync()`alatt adja hozzá ezt a kódot.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás felépítésére és a beszédfelismerési szolgáltatás használatával történő tesztelésre.

1. **Fordítsa le a kódot** – a Visual Studio menüsávján válassza a **Build** > **Build megoldás**elemet.
2. **Indítsa el az alkalmazást** – a menüsávban válassza a **hibakeresés** > a **hibakeresés indítása** vagy az **F5**billentyű lenyomása lehetőséget.
3. **Felismerés elindítása** – felszólítja, hogy beszéljen egy angol nyelvű kifejezéssel. A rendszer elküldje a beszédet a beszédfelismerési szolgáltatásnak, amelyet szövegként leír, és a konzolon jeleníti meg.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
