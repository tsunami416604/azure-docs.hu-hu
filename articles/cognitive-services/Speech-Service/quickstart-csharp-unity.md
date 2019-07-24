---
title: 'Gyors útmutató: Beszédfelismerési szolgáltatás felismerése, egység – beszéd szolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval létrehozhat egy beszéd – szöveg alkalmazást az Unity és az Unity (Beta) rendszerhez készült Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 425cacb22865e64a996c653477120a5f7f410c9a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405940"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Gyors útmutató: Beszéd felismerése az Unity (béta) Speech SDK-val

A rövid útmutatók [szövegről beszédre](quickstart-text-to-speech-csharp-unity.md)is elérhetők.

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ezzel az útmutatóval hozhat létre egy beszéd-szöveges alkalmazást az [Unity](https://unity3d.com/) és az Unity (Beta) rendszerhez készült Speech SDK használatával.
Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
Ha nem ismeri az egységet, javasoljuk, hogy az alkalmazás fejlesztésének megkezdése előtt tanulmányozza az [egység felhasználói kézikönyvét](https://docs.unity3d.com/Manual/UnityManual.html) .

> [!NOTE]
> Az egységhez készült Speech SDK jelenleg a Beta verzióban érhető el.
> Támogatja a Windows asztali (x86 és x64) vagy Univerzális Windows-platform (x86, x64, ARM/ARM64) és az Android (x86, ARM32/64) használatát.

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [2018,3-es vagy újabb egység](https://store.unity.com/) a [UWP-ARM64 támogatása a Unity 2019,1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal) -ben
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * A ARM64 támogatásához telepítse a [ARM64 választható Build-eszközeit, valamint a ARM64 készült Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) -t
* A beszédfelismerési szolgáltatáshoz tartozó előfizetési kulcs. [Szerezze be az egyiket ingyenesen](get-started.md).
* A számítógép mikrofonjának elérése.

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

A jelenethez egy minimális felhasználói felületet adunk hozzá, amely egy, a beszédfelismerést kiváltó gomb és egy szövegmező segítségével jeleníti meg az eredményt.

* A [hierarchia ablakban](https://docs.unity3d.com/Manual/Hierarchy.html) (alapértelmezés szerint a bal oldalon) megjelenik egy minta jelenet, amely az új projekttel létrehozott egységet mutatja.
* Kattintson a **create (létrehozás** ) gombra a hierarchia ablak tetején, majd válassza **a felhasználói felület** > **gombot**.
* Ez három játék objektumot hoz létre, amelyek a hierarchia ablakban láthatók: egy **vászon** objektumba beágyazott **gomb** -objektum és egy **EventSystem** objektum.
* [Navigáljon a jelenet](https://docs.unity3d.com/Manual/SceneViewNavigation.html) nézetbe, hogy jól látható legyen a vászon és a gomb a [jelenet nézetben](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Kattintson a hierarchia ablakban a **gomb** objektumra a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **POS Y** tulajdonságokat **0-ra**, így a gomb középre van állítva a vászon közepén.
* Kattintson ismét a **Létrehozás** gombra a hierarchia ablak tetején, majd válassza a **felhasználói felület** > **szövege** lehetőséget a szövegmező létrehozásához.
* Kattintson a hierarchia ablak **szöveg** objektumára a beállítások megjelenítéséhez a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa a **POS X** és **a POS Y** tulajdonságokat **0** és **120**értékre, majd állítsa a **szélesség** és **magasság** tulajdonságokat **240** és **120** értékre, hogy a szövegmező és a gomb ne legyen átfedésben.

Ha elkészült, a felhasználói felületnek a következő képernyőképhez hasonlóan kell kinéznie:

[![Képernyőkép a rövid útmutató felhasználói felületéről az Unity Editorban](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. A [projekt ablakban](https://docs.unity3d.com/Manual/ProjectView.html) (alapértelmezés szerint a bal oldalon) kattintson a **Létrehozás** gombra, majd válassza a  **C# parancsfájl**lehetőséget. Adja meg a `HelloWorld`parancsfájl nevét.

1. Szerkessze a szkriptet úgy, hogy duplán kattint rá.

   > [!NOTE]
   > Megadhatja, hogy melyik Kódszerkesztő legyen elindítva a**Beállítások** **szerkesztése** > szakaszban, lásd: [Unity felhasználói kézikönyv](https://docs.unity3d.com/Manual/Preferences.html).

1. Cserélje le az összes kódot a következőre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és cserélje le `YourSubscriptionKey` a karakterláncot a Speech Services előfizetési kulcsával.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md). Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a parancsfájl módosításait.

1. A Unity Editorban vissza kell adni a szkriptet az egyik játék-objektumhoz tartozó összetevőként.

   * A hierarchia ablakban kattintson a **vászon** objektumra. Ekkor megnyílik a beállítás a [felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
   * Kattintson az **összetevő hozzáadása** gombra a felügyelő ablakban, majd keresse meg a fent létrehozott HelloWorld-szkriptet, és adja hozzá.
   * Vegye figyelembe, hogy a "Helló világ!" alkalmazás összetevőnek két nem inicializált tulajdonsága van, a **kimeneti szöveg** és a kilépési kiindulási **gomb**, amelyek megfelelnek az `HelloWorld` osztály nyilvános tulajdonságainak.
     Ha fel kívánja őket csatlakoztatni, kattintson az objektum-választóra (a tulajdonság jobb oldalán lévő kis kör ikonra), és válassza ki a korábban létrehozott szöveg-és gomb-objektumokat.

     > [!NOTE]
     > A gomb beágyazott szöveges objektummal is rendelkezik. Ügyeljen arra, hogy ne legyen véletlenül kiválasztva szöveges kimenetre (vagy nevezze át az egyik szöveges objektumot a felügyelő ablak Name (név) mezőjéből a félreértések elkerülése érdekében).

## <a name="run-the-application-in-the-unity-editor"></a>Az alkalmazás futtatása az Unity Editorban

* Nyomja le az Unity Editor eszköztár **Lejátszás** gombját (a menüsáv alatt).

* Az alkalmazás elindítása után kattintson a gombra, és beszéljen egy angol kifejezéssel vagy mondattal a számítógép mikrofonjában. A beszéd a beszédfelismerési szolgáltatásokhoz lett továbbítva, és szövegbe kerül, amely megjelenik az ablakban.

  [![Képernyőkép a futó rövid útmutatóról az Unity játék ablakban](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* A hibakeresési üzenetekért tekintse meg a [konzol ablakát](https://docs.unity3d.com/Manual/Console.html) .

* Ha elkészült a beszéd felismerésével, kattintson az Unity Editor eszköztár **Lejátszás** gombjára az alkalmazás leállításához.

## <a name="additional-options-to-run-this-application"></a>További lehetőségek az alkalmazás futtatásához

Ez az alkalmazás az Android rendszerű, önálló alkalmazásként vagy UWP alkalmazásként is üzembe helyezhető.
Tekintse meg a [minta tárházat](https://aka.ms/csspeech/samples) a rövid útmutató/csharp-Unity mappában, amely leírja ezeknek a további céloknak a konfigurációját.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
