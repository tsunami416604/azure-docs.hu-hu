---
title: 'Gyors útmutató: beszédfelismerési szolgáltatás C# (Unity) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató egy szöveg-beszéd alapú alkalmazás létrehozására használható az Unity és a Speech SDK for Unity használatával. Ha elkészült, valós időben szintetizálhat beszédet a szövegből az eszköz hangszóróján.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 73a4477547c562a9960fe72b352e906676eadae5
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925308"
---
> [!NOTE]
> Az Unity támogatja a Windows asztali (x86 és x64) vagy a Univerzális Windows-platform (x86, x64, ARM/ARM64), az Android (x86, ARM32/64) és az iOS (x64 Simulator, ARM32 és ARM64) használatát.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=unity)

## <a name="add-a-ui"></a>Felhasználói felület hozzáadása

Egy olyan minimális felhasználói felületet adunk hozzá a jelenethez, amely egy beviteli mezőből áll a szintézis szövegének megadásához, egy gomb a beszédfelismerési szintézis elindításához, valamint egy szöveges mező az eredmény megjelenítéséhez.

* A [hierarchia ablakban](https://docs.unity3d.com/Manual/Hierarchy.html) (alapértelmezés szerint a bal oldalon) megjelenik egy minta jelenet, amely az új projekttel létrehozott egységet mutatja.
* Válassza a **Létrehozás** gombot a **hierarchia** ablak tetején, majd válassza a **felhasználói felület** > **beviteli mező**lehetőséget.
* Ez a lehetőség három olyan objektumot hoz létre, amelyek a **hierarchia** ablakban láthatók: egy **beviteli mező** objektum egy **vászon** objektumba ágyazva, valamint egy **EventSystem** objektum.
* [Navigáljon a jelenet](https://docs.unity3d.com/Manual/SceneViewNavigation.html) nézetbe, hogy jó képet adjon a vászonról és a beviteli mezőről a [jelenet nézetben](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* A **hierarchia** ablakban válassza a **beviteli mező** objektumot a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **POS Y** tulajdonságokat **0-ra** , hogy a bemeneti mező középre legyen állítva a vászon közepén.
* Kattintson ismét a **create (létrehozás** ) gombra a **hierarchia** ablak tetején. Kattintson a **felhasználói felület** > **gombra** egy gomb létrehozásához.
* A **hierarchia** ablakban kattintson a **gomb** objektumra a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **POS Y** tulajdonságokat **0** és **-48**értékre. Állítsa a **szélesség** és a **magasság** tulajdonságokat **160** és **30** értékre, hogy a gomb és a beviteli mező ne legyen átfedésben.
* Kattintson ismét a **create (létrehozás** ) gombra a **hierarchia** ablak tetején. Szövegmező létrehozásához válassza a **felhasználói felület** > **szöveg** elemet.
* A **hierarchia** ablakban válassza ki a **text (szöveg** ) objektumot a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és a **POS Y** tulajdonságokat **0** és **80**értékre. Állítsa a **szélesség** és **magasság** tulajdonságokat **320** és **80** értékre, hogy a szövegmező és a beviteli mező ne legyen átfedésben.
* Kattintson ismét a **create (létrehozás** ) gombra a **hierarchia** ablak tetején. Hangforrás létrehozásához válassza a **hang** > **hangforrás** lehetőséget.

Ha elkészült, a felhasználói felületnek a következő képernyőképhez hasonlóan kell kinéznie:

[![képernyőkép a gyors üzembe helyezés felhasználói felületéről az Unity Editorban](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. A [projekt ablakban](https://docs.unity3d.com/Manual/ProjectView.html) (alapértelmezés szerint a bal oldalon) válassza a **Létrehozás** gombot, majd válassza a  **C# parancsfájl**lehetőséget. Nevezze el a szkriptet `HelloWorld`.

1. Szerkessze a szkriptet úgy, hogy duplán kattint rá.

   > [!NOTE]
   > Az > **beállításainak** **szerkesztése** lehetőség kiválasztásával konfigurálhatja, hogy melyik Kódszerkesztő induljon el. További információ: [Unity felhasználói kézikönyv](https://docs.unity3d.com/Manual/Preferences.html).

1. Cserélje le az összes kódot a következőre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és cserélje le a `YourSubscriptionKey` karakterláncot a Speech Service előfizetési kulcsával.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md). Például a régió `westus`, ha az ingyenes próbaverziót használja.

1. Mentse a parancsfájl módosításait.

1. Vissza az Unity Editorban adja hozzá a parancsfájlt összetevőként az egyik játék-objektumhoz.

   * A **hierarchia** ablakban válassza ki a **vászon** objektumot, hogy megnyissa a beállítást a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
   * Válassza az **összetevő hozzáadása** gombot a **felügyelő** ablakban. Ezután keresse meg a korábban létrehozott `HelloWorld`-parancsfájlt, és adja hozzá.
   * A HelloWorld összetevő négy nem inicializált tulajdonsággal, **kimeneti szöveggel**, **beviteli mezővel**, **beszéd gombbal** és **hangforrással**rendelkezik, amelyek megfelelnek a `HelloWorld` osztály nyilvános tulajdonságainak.
     A kiválasztáshoz válassza ki az objektum-választót (a tulajdonság jobb oldalán lévő kis kör ikont). Válassza ki a korábban létrehozott szöveg-és gomb-objektumokat.

     > [!NOTE]
     > A beviteli mező és a gomb beágyazott szöveges objektummal is rendelkezik. Ügyeljen arra, hogy a szöveg kimenete ne legyen véletlenül kiválasztva. Vagy átnevezheti a **felügyelő** ablak **Name (név** ) mezőjét használó szöveges objektumokat a félreértések elkerülése érdekében.

## <a name="run-the-application-in-the-unity-editor"></a>Az alkalmazás futtatása az Unity Editorban

* Válassza az Unity Editor eszköztár **Lejátszás** gombját a menüsáv alatt.
* Az alkalmazás elindítása után írjon be egy szöveget a beviteli mezőbe, majd kattintson a gombra. A rendszer továbbítja a szöveget a Speech Service-nek, és szintetizálta a beszédet, amely a beszélőn játszik.

  [![képernyőkép a futó rövid útmutatóról az Unity játék ablakban](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* A hibakeresési üzenetekért tekintse meg a [konzol ablakát](https://docs.unity3d.com/Manual/Console.html) .

## <a name="additional-options-to-run-this-application"></a>További lehetőségek az alkalmazás futtatásához

Ez az alkalmazás az Android rendszerre is telepíthető Windows önálló alkalmazásként vagy UWP-alkalmazásként.
Tekintse meg a [minta tárházat](https://aka.ms/csspeech/samples) a rövid útmutató/csharp-Unity mappában, amely leírja ezeknek a további céloknak a konfigurációját.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
