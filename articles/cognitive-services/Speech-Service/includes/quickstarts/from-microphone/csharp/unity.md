---
title: 'Rövid útmutató: Beszédfelismerés mikrofonból, C# (Unity)- Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 951ae2c48bcdd92f640a37ddbb6430ca62a3b816
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275229"
---
> [!NOTE]
> A Speech SDK for Unity támogatja a Windows Desktop (x86 és x64) vagy az Univerzális Windows platformot (x86, x64, ARM/ARM64), az Android (x86, ARM32/64) és az iOS (x64 szimulátor, ARM32 és ARM64)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene járni:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * Győződjön meg arról, hogy rendelkezik a mikrofonhoz a hangrögzítéshez

Ha ezt már megtetted, remek. Menjünk tovább.

## <a name="create-a-unity-project"></a>Unity-projekt létrehozása

1. Nyílt Egység. Ha első alkalommal használja a Unity-t, megjelenik a **Unity Hub** *<version number>* ablak. (A Unity Hubot közvetlenül is megnyithatja, hogy eljusson ehhez az ablakhoz.)

   [![A Unity Hub ablaka](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Válassza az **Új**lehetőséget. Megjelenik az Új projekt létrehozása unity *<version number>* **ablakkal.**

   [![Új projekt létrehozása a Unity Hubban](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. A **Projekt neve**mezőbe írja be a **csharp-unity**értéket .
1. **Ha a Sablonok alkalmazásban**a **3D** nincs kijelölve, jelölje ki.
1. A **Hely területen**jelölje ki vagy hozzon létre egy mappát a projekt mentéséhez.
1. Kattintson a **Létrehozás** gombra.

Egy kis idő múlva megjelenik a Unity Szerkesztő ablak.



## <a name="add-ui"></a>Felhasználói felület hozzáadása

Most adjunk hozzá egy minimális felhasználói felületet a jelenetünkhöz. Ez a felhasználói felület egy gombból áll, amely elindítja a beszédfelismerést, és egy szövegmezőből az eredmény megjelenítéséhez. A [ **Hierarchia** ablakban](https://docs.unity3d.com/Manual/Hierarchy.html)egy mintajelenet jelenik meg, amelyet az Egység hozott létre az új projekttel.

1. A **Hierarchia** ablak tetején válassza a**Felhasználói felület** >  **létrehozása** > **gomb lehetőséget.**

   Ez a művelet három játékobjektumot hoz létre, amelyek a **Hierarchia** ablakban láthatók: egy **gombobjektum,** egy gombot tartalmazó **vászonobjektum** és egy **EventSystem** objektum.

   [![Unity-szerkesztő környezet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigáljon a **Jelenet** nézetben,](https://docs.unity3d.com/Manual/SceneViewNavigation.html) hogy jól látható legyen a vászon és a Gomb a [ **Jelenet** nézetben](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. A [ **Felügyelő** ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon) állítsa a **Pos X** és **pos Y** tulajdonságokat **0-ra,** így a gomb a vászon közepén helyezkedik el.

1. A **Hierarchia** ablakban jelölje be**a Felhasználói felület** > **szövegének** **létrehozása** >  **jelölőnégyzetet szövegobjektum** létrehozásához.

1. A **Felügyelő** ablakban állítsa a **Pos X** és **Pos Y** tulajdonságokat **0** és **120-ra,** és állítsa a **Szélesség** és **magasság** tulajdonságot **240-re** és **120-ra.** Ezek az értékek biztosítják, hogy a szövegmező és a gomb ne fedje át egymást.

Ha elkészült, a **Jelenet** nézetnek a képernyőképhez hasonlóan kell kinéznie:

[![Jelenetnézet az Egységszerkesztőben](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

Az Unity projekt mintaparancsfájl-kódjának hozzáadásához hajtsa végre az alábbi lépéseket:

1. A [Project ablakban](https://docs.unity3d.com/Manual/ProjectView.html)válassza a**C# parancsfájl** **létrehozása lehetőséget** > új C# parancsfájl hozzáadásához.

   [![Projekt ablak az Egységszerkesztőben](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nevezze el `HelloWorld`a parancsfájlt .

1. Kattintson `HelloWorld` duplán az újonnan létrehozott parancsfájl szerkesztéséhez.

   > [!NOTE]
   > Ha a Unity által szerkesztésre használt kódszerkesztőt szeretné beállítani, válassza a > **Beállítások** **szerkesztése**lehetőséget, majd lépjen a **Külső eszközök** beállításai ra. További információt az [Unity felhasználói kézikönyvében](https://docs.unity3d.com/Manual/Preferences.html)talál.

1. Cserélje le a meglévő parancsfájlt a következő kódra:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és `YourSubscriptionKey` cserélje le a karakterláncot a beszédszolgáltatás-előfizetési kulccsal.

1. Keresse meg és `YourServiceRegion` cserélje le a karakterláncot az előfizetéshez társított [régió](https://aka.ms/speech/sdkregion) régiórégiórégióból származó **régióra.** Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a parancsfájl módosításait.

Most térjen vissza a Unity Szerkesztő, és adja hozzá a forgatókönyvet, mint egy összetevő az egyik játék objektum:

1. A **Hierarchia** ablakban jelölje ki a **Vászon** objektumot.

1. A **Felügyelő** ablakban válassza az **Összetevő hozzáadása** gombot.

   [![Felügyelő ablak az Egységszerkesztőben](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. A legördülő listában keresse meg `HelloWorld` a fenti szkriptet, és adja hozzá. A **Felügyelő** ablakban megjelenik egy **Hello World (Script)** szakasz, amely két inicializálatlan tulajdonságot sorol fel, **a Kimeneti szöveget** és **a Start Reco gombot.** Ezek a Unity összetevő tulajdonságok `HelloWorld` megfelelnek az osztály nyilvános tulajdonságainak.

1. Jelölje ki a **Start Reco Button** tulajdonság objektumválasztóját (a tulajdonságtól jobbra található kis kör ikont), és válassza ki a korábban létrehozott **Button** objektumot.

1. Jelölje ki a **Kimeneti szöveg** tulajdonság objektumválasztóját, és válassza ki a korábban létrehozott **Szöveg** objektumot.

   > [!NOTE]
   > A gombhoz beágyazott szövegobjektum is található. Győződjön meg arról, hogy véletlenül nem választja ki a szövegkimenethez (vagy nevezze át az egyik szövegobjektumot a **Felügyelő** ablak **Név** mezőjével a félreértések elkerülése érdekében).

## <a name="run-the-application-in-the-unity-editor"></a>Az alkalmazás futtatása az Egységszerkesztőben

Most már készen áll az alkalmazás futtatására a Unity Szerkesztőben.

1. A Unity Szerkesztő eszköztárán (a menüsor alatt) válassza a **Lejátszás** gombot (jobbra mutató háromszög).

1. Nyissa meg a [ **Játék** nézetet,](https://docs.unity3d.com/Manual/GameView.html)és várja meg, amíg a **Szöveg** objektum megjelenik, **a Kattintás gombra kattintva felismeri a beszédet.** (Az **új szöveg akkor** jelenik meg, ha az alkalmazás még nem indult el, vagy nem áll készen a válaszadásra.)

1. Jelölje ki a gombot, és beszéljen egy angol kifejezést vagy mondatot a számítógép mikrofonjába. A beszéd a beszédfelismerési szolgáltatásba kerül, és átíródik szöveggé, amely a **Játék** nézetben jelenik meg.

   [![Játék nézet az Egységszerkesztőben](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Ellenőrizze a [ **Konzol** ablakban](https://docs.unity3d.com/Manual/Console.html) a hibakeresési üzeneteket. Ha a **Konzol** ablak nem jelenik meg, lépjen a menüsorba, és a **Windows** > **Általános** > **konzol** lehetőséget választva jelenítse meg.

1. Ha végzett a beszéd felismerésével, az alkalmazás leállításához válassza a **Lejátszás** gombot a Unity Szerkesztő eszköztárán.

## <a name="additional-options-to-run-this-application"></a>Az alkalmazás futtatásának további lehetőségei

Ez az alkalmazás androidos alkalmazásként, Windows-önálló alkalmazásként vagy UWP-alkalmazásként is telepíthető.
További információkért tekintse meg [a mintatárunkat.](https://aka.ms/csspeech/samples) A `quickstart/csharp-unity` mappa a további példányok konfigurációját ismerteti.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

