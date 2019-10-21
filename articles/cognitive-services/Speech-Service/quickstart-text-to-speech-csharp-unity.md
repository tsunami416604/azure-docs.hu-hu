---
title: 'Gyors útmutató: szintetizáló beszéd, Unity-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató egy szöveg-beszéd alapú alkalmazás létrehozására használható az Unity és a Speech SDK for Unity használatával. Ha elkészült, valós időben szintetizálhat beszédet a szövegből az eszköz hangszóróján.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: b4d329d9d3c2a259fb90b0278c54ba8590590995
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675483"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Gyors útmutató: az Unity Speech SDK-val folytatott beszédének szintézise

A [beszédfelismerés](quickstart-csharp-unity.md)is elérhetővé teszi a gyors útmutatókat.

Ezzel az útmutatóval szöveg-beszéd alkalmazást hozhat létre az Unity és az Azure Cognitive Services Speech SDK [for Unity használatával](https://unity3d.com/) .
Ha elkészült, valós időben szintetizálhat beszédet a szövegből az eszköz hangszóróján.
Ha nem ismeri az egységet, tanulmányozza az [egység felhasználói kézikönyvét](https://docs.unity3d.com/Manual/UnityManual.html) , mielőtt megkezdené az alkalmazás fejlesztését.

> [!NOTE]
> Az Unity támogatja a Windows asztali (x86 és x64) vagy a Univerzális Windows-platform (x86, x64, ARM/ARM64), az Android (x86, ARM32/64) és az iOS (x64 Simulator, ARM32 és ARM64) használatát.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [Unity 2018,3 vagy újabb](https://store.unity.com/) , az [Unity 2019,1 támogatásával UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A Visual Studio 2017 15,9-es vagy újabb verziója is elfogadható.
* A Windows ARM64 támogatásához telepítse a [választható Build-eszközöket a ARM64 és a ARM64 Windows 10 SDK-](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)ra.
* A beszédfelismerési szolgáltatáshoz tartozó előfizetési kulcs. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-unity-project"></a>Unity-projekt létrehozása

* Indítsa el az egységet, és a **projektek** lapon válassza az **új**lehetőséget.
* Adja meg a **projekt nevét** **csharp – Unity** és **sablonként** **3D**-ként, és válasszon helyet.
  Ezután válassza a **projekt létrehozása**lehetőséget.
* Egy kis idő elteltével a Unity Editor ablakának kell megjelennie.

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Az Unity (béta) Speech SDK egységként van csomagolva (. unitypackage). Töltse le a [webhelyről](https://aka.ms/csspeech/unitypackage).
* Importálja a Speech SDK-t úgy, hogy kijelöli az **eszközök**  > **csomag importálása**  > **Egyéni csomag**lehetőséget. További információ: [Unity dokumentáció](https://docs.unity3d.com/Manual/AssetPackages.html).
* A fájl kiválasztása lapon válassza ki a letöltött Speech SDK. unitypackage fájlt.
* Győződjön meg arról, hogy az összes fájl ki van választva, majd válassza az **Importálás**lehetőséget.

  ![Képernyőkép az Unity Editorról a Speech SDK Unity Asset csomag importálásakor](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-a-ui"></a>Felhasználói felület hozzáadása

Egy olyan minimális felhasználói felületet adunk hozzá a jelenethez, amely egy beviteli mezőből áll a szintézis szövegének megadásához, egy gomb a beszédfelismerési szintézis elindításához, valamint egy szöveges mező az eredmény megjelenítéséhez.

* A [hierarchia ablakban](https://docs.unity3d.com/Manual/Hierarchy.html) (alapértelmezés szerint a bal oldalon) megjelenik egy minta jelenet, amely az új projekttel létrehozott egységet mutatja.
* Válassza a **Létrehozás** gombot a **hierarchia** ablak tetején, majd válassza a **felhasználói felület**  > **beviteli mező**lehetőséget.
* Ez a lehetőség három olyan objektumot hoz létre, amelyek a **hierarchia** ablakban láthatók: egy **beviteli mező** objektum egy **vászon** objektumba ágyazva, valamint egy **EventSystem** objektum.
* [Navigáljon a jelenet](https://docs.unity3d.com/Manual/SceneViewNavigation.html) nézetbe, hogy jó képet adjon a vászonról és a beviteli mezőről a [jelenet nézetben](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* A **hierarchia** ablakban válassza a **beviteli mező** objektumot a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **POS Y** tulajdonságokat **0-ra** , hogy a bemeneti mező középre legyen állítva a vászon közepén.
* Kattintson ismét a **create (létrehozás** ) gombra a **hierarchia** ablak tetején. Kattintson a **felhasználói felület**  > **gombra** egy gomb létrehozásához.
* A **hierarchia** ablakban kattintson a **gomb** objektumra a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **POS Y** tulajdonságokat **0** és **-48**értékre. Állítsa a **szélesség** és a **magasság** tulajdonságokat **160** és **30** értékre, hogy a gomb és a beviteli mező ne legyen átfedésben.
* Kattintson ismét a **create (létrehozás** ) gombra a **hierarchia** ablak tetején. Szövegmező létrehozásához válassza a **felhasználói felület**  > **szöveg** elemet.
* A **hierarchia** ablakban válassza ki a **text (szöveg** ) objektumot a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és a **POS Y** tulajdonságokat **0** és **80**értékre. Állítsa a **szélesség** és **magasság** tulajdonságokat **320** és **80** értékre, hogy a szövegmező és a beviteli mező ne legyen átfedésben.
* Kattintson ismét a **create (létrehozás** ) gombra a **hierarchia** ablak tetején. Hangforrás létrehozásához válassza a **hang**  > **hangforrás** lehetőséget.

Ha elkészült, a felhasználói felületnek a következő képernyőképhez hasonlóan kell kinéznie:

[a rövid útmutató felhasználói felületének ![Screenshota az Unity Editorban](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. A [projekt ablakban](https://docs.unity3d.com/Manual/ProjectView.html) (alapértelmezés szerint a bal oldalon) válassza a **Létrehozás** gombot, majd válassza a  **C# parancsfájl**lehetőséget. Nevezze el a szkriptet `HelloWorld`.

1. Szerkessze a szkriptet úgy, hogy duplán kattint rá.

   > [!NOTE]
   > Az  > **beállításainak** **szerkesztése** lehetőség kiválasztásával konfigurálhatja, hogy melyik Kódszerkesztő induljon el. További információ: [Unity felhasználói kézikönyv](https://docs.unity3d.com/Manual/Preferences.html).

1. Cserélje le az összes kódot a következőre:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és cserélje le a `YourSubscriptionKey` karakterláncot a Speech Services előfizetési kulcsával.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md). Például a régió `westus`, ha az ingyenes próbaverziót használja.

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
* Az alkalmazás elindítása után írjon be egy szöveget a beviteli mezőbe, majd kattintson a gombra. A szöveg a beszédfelismerési szolgáltatásba kerül, és a beszédbe lett szintetizálva, amely a beszélőn játszik.

  [a futó rövid útmutató ![Screenshot a Unity játék ablakban](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* A hibakeresési üzenetekért tekintse meg a [konzol ablakát](https://docs.unity3d.com/Manual/Console.html) .
* Ha befejezte a beszédfelismerést, válassza a **Lejátszás** gombot az Unity Editor eszköztárán az alkalmazás leállításához.

## <a name="additional-options-to-run-this-application"></a>További lehetőségek az alkalmazás futtatásához

Ez az alkalmazás az Android rendszerre is telepíthető Windows önálló alkalmazásként vagy UWP-alkalmazásként.
Tekintse meg a [minta tárházat](https://aka.ms/csspeech/samples) a rövid útmutató/csharp-Unity mappában, amely leírja ezeknek a további céloknak a konfigurációját.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még:

- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
