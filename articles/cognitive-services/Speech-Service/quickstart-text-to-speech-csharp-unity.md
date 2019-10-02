---
title: 'Gyors útmutató: Szintetizáló beszéd, Unity-Speech szolgáltatás'
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
ms.openlocfilehash: be5f07b8ea58d0d62c70e0e9dc8ab187ce4a0f63
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803188"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Gyors útmutató: Beszédfelismerési beszéd az Unity Speech SDK-val

A [beszédfelismerés](quickstart-csharp-unity.md)is elérhetővé teszi a gyors útmutatókat.

Ez az útmutató egy szöveg-beszéd alkalmazás létrehozására használható az Unity és a Speech SDK [for Unity használatával](https://unity3d.com/) .
Ha elkészült, valós időben szintetizálhat beszédet a szövegből az eszköz hangszóróján.
Ha nem ismeri az egységet, javasoljuk, hogy az alkalmazás fejlesztésének megkezdése előtt tanulmányozza az [egység felhasználói kézikönyvét](https://docs.unity3d.com/Manual/UnityManual.html) .

> [!NOTE]
> Támogatja a Windows Desktop (x86 és x64) vagy Univerzális Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) és iOS (x64 Simulator, ARM32 és ARM64) használatát.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [2018,3-es vagy újabb egység](https://store.unity.com/) a [UWP-ARM64 támogatása a Unity 2019,1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal) -ben
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A Visual Studio 2017 15,9-es vagy újabb verziója is elfogadható.
* A Windows ARM64 támogatásához telepítse a [ARM64 választható Build-eszközeit, valamint a ARM64 készült Windows 10 SDK-](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) t 
* A beszédfelismerési szolgáltatáshoz tartozó előfizetési kulcs. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-unity-project"></a>Unity-projekt létrehozása

* Indítsa el az egységet, és a **projektek** lapon válassza az **új**lehetőséget.
* Adja meg a **projekt nevét** **csharp-Unity**, **sablonként** **3D** -ként, és válasszon helyet.
  Ezután válassza a **projekt létrehozása**lehetőséget.
* Egy kis idő elteltével a Unity Editor ablakának kell megjelennie.

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Az Unity (béta) Speech SDK egységként van csomagolva (. unitypackage).
  Töltse le [Itt](https://aka.ms/csspeech/unitypackage).
* Importálja a Speech SDK-t úgy, hogy kiválasztja az **eszközök** > **importálása** > **Egyéni csomag**lehetőséget.
  A részletekért tekintse meg az [Unity dokumentációját](https://docs.unity3d.com/Manual/AssetPackages.html) .
* A fájl kiválasztása lapon válassza ki a fentebb letöltött Speech SDK. unitypackage fájlt.
* Győződjön meg arról, hogy minden fájl ki van választva, majd kattintson az **Importálás**elemre:

  ![Képernyőkép az Unity Editorról a Speech SDK Unity Asset csomag importálásakor](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Felhasználói felület hozzáadása

A jelenethez egy minimális felhasználói felületet adunk hozzá, amely egy beviteli mezőből áll, amely beírja a szintézis szövegét, egy gombot a beszédfelismerési szintézis elindításához és egy szövegmezőbe az eredmény megjelenítéséhez.

* A [hierarchia ablakban](https://docs.unity3d.com/Manual/Hierarchy.html) (alapértelmezés szerint a bal oldalon) megjelenik egy minta jelenet, amely az új projekttel létrehozott egységet mutatja.
* Kattintson a **create (létrehozás** ) gombra a hierarchia ablak tetején, majd válassza a **felhasználói felület**@no__t – 2**beviteli mezőt**.
* Ez három játék objektumot hoz létre, amelyek a hierarchia ablakban láthatók: egy **beviteli mező** objektum egy **vászon** objektumba ágyazva, valamint egy **EventSystem** objektum.
* [Navigáljon a jelenet](https://docs.unity3d.com/Manual/SceneViewNavigation.html) nézetbe, hogy jól látható legyen a vászon és a beviteli mező a [jelenet nézetben](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* A hierarchia ablakban a **beviteli mező** objektumra kattintva jelenítheti meg a beállításait a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **POS Y** tulajdonságokat **0-ra**, így a bemeneti mező középre van állítva a vászon közepén.
* Kattintson ismét a **Létrehozás** gombra a hierarchia ablak tetején, majd válassza a **felhasználói felület** > **gombot** a gomb létrehozásához.
* Kattintson a hierarchia ablakban a **gomb** objektumra a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **POS Y** tulajdonságokat **0** és **-48**értékre, majd állítsa a **szélesség** és **magasság** tulajdonságokat **160** és **30** értékre, hogy a gomb és a beviteli mező ne legyen átfedésben.
* Kattintson ismét a **Létrehozás** gombra a hierarchia ablak tetején, majd válassza a **felhasználói felület** > **szövege** lehetőséget a szövegmező létrehozásához.
* Kattintson a hierarchia ablak **szöveg** objektumára a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **a POS Y** tulajdonságokat **0** és **80**értékre, és állítsa a **szélesség** és **magasság** tulajdonságokat **320** és **80** értékre, hogy a szövegmező és a beviteli mező ne legyen átfedésben.
* Kattintson ismét a **create (létrehozás** ) gombra a hierarchia ablak tetején, majd válassza **a hang @no__t**– 2**hangforrást** a hangforrások létrehozásához.

Ha elkészült, a felhasználói felületnek a következő képernyőképhez hasonlóan kell kinéznie:

[![Képernyőkép a rövid útmutató felhasználói felületéről az Unity Editorban](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. A [projekt ablakban](https://docs.unity3d.com/Manual/ProjectView.html) (alapértelmezés szerint a bal oldalon) kattintson a **Létrehozás** gombra, majd válassza a  **C# parancsfájl**lehetőséget. Adja meg a `HelloWorld`parancsfájl nevét.

1. Szerkessze a szkriptet úgy, hogy duplán kattint rá.

   > [!NOTE]
   > Megadhatja, hogy melyik Kódszerkesztő legyen elindítva a**Beállítások** **szerkesztése** > szakaszban, lásd: [Unity felhasználói kézikönyv](https://docs.unity3d.com/Manual/Preferences.html).

1. Cserélje le az összes kódot a következőre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és cserélje le `YourSubscriptionKey` a karakterláncot a Speech Services előfizetési kulcsával.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md). Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a parancsfájl módosításait.

1. A Unity Editorban vissza kell adni a szkriptet az egyik játék-objektumhoz tartozó összetevőként.

   * A hierarchia ablakban kattintson a **vászon** objektumra. Ekkor megnyílik a beállítás a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
   * Kattintson az **összetevő hozzáadása** gombra a felügyelő ablakban, majd keresse meg a fent létrehozott HelloWorld-szkriptet, és adja hozzá.
   * Vegye figyelembe, hogy a "Helló világ!" alkalmazás összetevő négy nem inicializált tulajdonsággal, **kimeneti szöveggel**, **beviteli mezővel**, **beszéd gombbal** és **hangforrással**rendelkezik, amelyek megfelelnek a `HelloWorld` osztály nyilvános tulajdonságainak.
     Ha fel kívánja őket csatlakoztatni, kattintson az objektum-választóra (a tulajdonság jobb oldalán lévő kis kör ikonra), és válassza ki a korábban létrehozott szöveg-és gomb-objektumokat.

     > [!NOTE]
     > A beviteli mező és a gomb beágyazott szöveges objektummal is rendelkezik. Ügyeljen arra, hogy ne legyen véletlenül kiválasztva szöveges kimenetre (vagy nevezze át a szöveges objektumokat a felügyelő ablak név mezőjéből, hogy elkerülje a zavart).

## <a name="run-the-application-in-the-unity-editor"></a>Az alkalmazás futtatása az Unity Editorban

* Nyomja le az Unity Editor eszköztár **Lejátszás** gombját (a menüsáv alatt).

* Az alkalmazás elindítása után írjon be egy szöveget a beviteli mezőbe, majd kattintson a gombra. A rendszer továbbítja a szöveget a Speech Servicesnek, és szintetizálta a beszédet, amely a beszélőn játszik.

  [![Képernyőkép a futó rövid útmutatóról az Unity játék ablakban](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* A hibakeresési üzenetekért tekintse meg a [konzol ablakát](https://docs.unity3d.com/Manual/Console.html) .

* Ha befejezte a beszédfelismerést, kattintson a **Play (lejátszás** ) gombra az Unity Editor eszköztárán az alkalmazás leállításához.

## <a name="additional-options-to-run-this-application"></a>További lehetőségek az alkalmazás futtatásához

Ez az alkalmazás az Android rendszerű, önálló alkalmazásként vagy UWP alkalmazásként is üzembe helyezhető.
Tekintse meg a [minta tárházat](https://aka.ms/csspeech/samples) a rövid útmutató/csharp-Unity mappában, amely leírja ezeknek a további céloknak a konfigurációját.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
