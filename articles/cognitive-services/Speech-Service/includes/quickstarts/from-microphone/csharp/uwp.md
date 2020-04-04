---
title: 'Rövid útmutató: Beszédfelismerés mikrofonból, C# (UWP) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 3e7cd96b979fb97d7b50b84907881fe59d03e295
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658988"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

Ha ezt már megtetted, remek. Menjünk tovább.

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Az első lépés annak biztosítása, hogy a projekt meg legyen nyitva a Visual Studióban.

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz.

1. A **Megoldáskezelőben**nyissa meg a programot. `MainPage.xaml`

2. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet `</Grid>`a **Rácscímkébe** (a kettő között `<Grid>` és):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. A **Megoldáskezelőben**nyissa meg a `MainPage.xaml.cs`mögötti kód forrásfájlt. (Ez alatt csoportosítva `MainPage.xaml`.)

4. Cserélje le a kódot a következő alapkódra:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Egy `SpeechRecognizer` objektum inicializálása előtt létre kell hoznia egy konfigurációt, amely az előfizetési kulcsot és az előfizetési régiót használja. Szúrja be ezt `RecognizeSpeechAsync()` a kódot a metódusba.

> [!NOTE]
> Ez a `FromSubscription()` minta a `SpeechConfig`módszerrel építi a. Az elérhető módszerek teljes listáját a [SpeechConfig Class (Beszédkonfigurációs osztály)](https://docs.microsoft.com/dotnet/api/)[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Beszédfelismerő inicializálása

Most hozzunk létre `SpeechRecognizer`egy . Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt `RecognizeSpeechAsync()` a kódot a metódusba, közvetlenül a beszédfelismerési konfiguráció alatt.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Kifejezés felismerése

Az `SpeechRecognizer` objektumból meg kell adni `RecognizeOnceAsync()` a metódust. Ez a módszer lehetővé teszi a beszédszolgáltatás számára, hogy egyetlen kifejezést küld a felismeréshez, és hogy miután a kifejezés azonosítása után a beszéd felismerésének leállítására kerül.

A using utasításon belül adja hozzá ezt a kódot.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>A felismerési eredmények (vagy hibák) megjelenítése

Amikor a beszédfelismerési szolgáltatás visszaadja az elismerés eredményét, érdemes valamit kezdenie vele. Egyszerű lesz, és kinyomtatjuk az eredményt az állapotpanelre.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Most már készen áll az alkalmazás megépítésére és tesztelésére.

1. A menüsorban válassza a Build megoldás **összeállítása** > **Build Solution** az alkalmazás létrehozásához. A kód fordításának hiba nélkül végbe kell mennie.

1. Az alkalmazás elindításához válassza a **Debug** > **Start Debugging** (vagy **az F5)** billentyűt. Megjelenik **a helloworld** ablak.

   ![Minta UWP beszédfelismerő alkalmazás C# nyelven – rövid útmutató](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Válassza **a Mikrofon engedélyezése**lehetőséget, és amikor megjelenik a hozzáférési engedélykérelem, válassza az **Igen**lehetőséget.

   ![Mikrofonhozzáférési engedély kérése](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Válassza a **Beszédfelismerés mikrofon bemenettel** lehetőséget, és mondjon egy angol kifejezést vagy mondatot a mikrofonba. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

   ![Beszédfelismerés felhasználói felülete](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](../footer.md)]
