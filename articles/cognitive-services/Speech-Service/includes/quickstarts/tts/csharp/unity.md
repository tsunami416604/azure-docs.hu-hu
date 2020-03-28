---
title: 'Rövid útmutató: Beszéd szintetizálása, C# (Unity) - Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Az útmutatósegítségével szövegfelolvasó alkalmazást hozhat létre a Unity és a Speech SDK for Unity használatával. Ha elkészült, valós időben szintetizálhatja a szövegből a készülék hangszórójáig.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 73a4477547c562a9960fe72b352e906676eadae5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925308"
---
> [!NOTE]
> A Unity támogatja a Windows Desktop (x86 és x64) vagy az univerzális Windows platformot (x86, x64, ARM/ARM64), az Android (x86, ARM32/64) és az iOS (x64 szimulátor, ARM32 és ARM64) rendszert.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?tabs=unity)

## <a name="add-a-ui"></a>Felhasználói felület hozzáadása

Hozzáadunk egy minimális felhasználói felületet a jelenetünkhöz, amely egy beviteli mezőből áll, amely beírja a szöveget a szintézishez, egy gombot a beszédszintézis elindításához, és egy szövegmezőt az eredmény megjelenítéséhez.

* A [Hierarchia ablakban](https://docs.unity3d.com/Manual/Hierarchy.html) (alapértelmezés szerint a bal oldalon) egy mintajelenet jelenik meg, amelyet az Egység hozott létre az új projekttel.
* Válassza a **Létrehozás** gombot a **Hierarchia** ablak tetején, és válassza a **Felhasználói felület** > **beviteli mezőjét**.
* Ez a beállítás három játékobjektumot hoz létre, amelyek a **Hierarchia** ablakban láthatók: egy **vászonobjektumba** ágyazott **beviteli mezőobjektumot** és egy **EventSystem** objektumot.
* [Navigáljon a Jelenet nézetben,](https://docs.unity3d.com/Manual/SceneViewNavigation.html) hogy jó képet kaphasson a vászonról és a beviteli mezőről a [Jelenet nézetben.](https://docs.unity3d.com/Manual/UsingTheSceneView.html)
* A **Hierarchia** **ablakbemeneti mező** objektumának kijelölése a Beállítások nak a [Felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) való megjelenítéséhez (alapértelmezés szerint a jobb oldalon).
* Állítsa a **Pos X** és **pos Y** tulajdonságokat **0-ra,** hogy a beviteli mező a vászon közepén helyezkedjen el.
* Ismét válassza a **Létrehozás** gombot a **Hierarchia** ablak tetején. Gomb létrehozásához válassza a **Felhasználói** > **felület gombját.**
* A **Hierarchia** **ablakban** jelölje ki a Gomb objektumot, ha a beállításokat a [Felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) szeretné megjeleníteni (alapértelmezés szerint a jobb oldalon).
* Állítsa a **Pos X** és **Pos Y** tulajdonságokat **0** és **-48-ra.** Állítsa a **Szélesség** és **magasság** **tulajdonságokat 160** és **30-ra,** hogy a gomb és a beviteli mező ne fedje át egymást.
* Ismét válassza a **Létrehozás** gombot a **Hierarchia** ablak tetején. Szövegmező létrehozásához válassza **a Felhasználói felület** > **szövegének kiválasztását.**
* Jelölje ki a **Szöveg** objektumot a **Hierarchia** ablakban, ha a beállításokat a [Felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) szeretné megjeleníteni (alapértelmezés szerint a jobb oldalon).
* Állítsa a **Pos X** és **Pos Y** tulajdonságokat **0** és **80-ra.** Állítsa a **Szélesség** és **magasság** **tulajdonságokat 320** és **80-ra,** hogy a szövegmező és a beviteli mező ne fedje át egymást.
* Ismét válassza a **Létrehozás** gombot a **Hierarchia** ablak tetején. Hangforrás létrehozásához válassza a > **Hanghangforrás** lehetőséget. **Audio**

Ha végzett, a felhasználói felületnek a képernyőképhez hasonlóan kell kinéznie:

[![Képernyőkép a Unity szerkesztő rövid útmutató felhasználói felületéről](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. A [Project ablakban](https://docs.unity3d.com/Manual/ProjectView.html) (alapértelmezés szerint a bal alsó sarokban) válassza a **Létrehozás gombot,** majd a **C# parancsfájlt.** Nevezze el `HelloWorld`a parancsfájlt .

1. A parancsfájl szerkesztése dupla kattintással.

   > [!NOTE]
   > A Beállítások **szerkesztése** > lehetőséget választva**beállíthatja,** hogy melyik kódszerkesztő induljon el. További információt az [Unity felhasználói kézikönyvében](https://docs.unity3d.com/Manual/Preferences.html)talál.

1. Cserélje le az összes kódot a következőkre:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és `YourSubscriptionKey` cserélje le a karakterláncot a beszédszolgáltatás-előfizetési kulccsal.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](~/articles/cognitive-services/Speech-Service/regions.md). Például a régió, `westus` ha használja az ingyenes próbaverzió.

1. Mentse a parancsfájl módosításait.

1. A Unity szerkesztőben adja hozzá a szkriptet összetevőként az egyik játékobjektumhoz.

   * A **Canvas** **Hierarchia** ablakvászon-objektumának kijelölésével nyissa meg a beállítást a [Felügyelő ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
   * Válassza az **Összetevő hozzáadása** gombot a **Felügyelő** ablakban. Ezután keresse `HelloWorld` meg a korábban létrehozott parancsfájlt, és adja hozzá.
   * A HelloWorld összetevő négy inicializálatlan tulajdonsággal rendelkezik: **Kimeneti szöveg,** `HelloWorld` **Beviteli mező,** **Felolvasásgomb** és **Hangforrás**, amelyek megfelelnek az osztály nyilvános tulajdonságainak.
     A bedrótozáshoz jelölje ki az Objektumválasztót (a tulajdonságtól jobbra található kis kör ikont). Jelölje ki a korábban létrehozott szöveg- és gombobjektumokat.

     > [!NOTE]
     > A beviteli mező és a gomb beágyazott szövegobjektummal is rendelkezik. Ügyeljen arra, hogy véletlenül ne válassza ki a szövegkimenethez. A **félreértések** elkerülése érdekében átnevezheti azokat a szövegobjektumokat is, amelyek a Felügyelő ablak **Név** mezőjét használják.

## <a name="run-the-application-in-the-unity-editor"></a>Az alkalmazás futtatása az Egységszerkesztőben

* Válassza a **Lejátszás** gombot a menüsor alatt található Unity Szerkesztő eszköztáron.
* Az alkalmazás elindítása után írjon be néhány szöveget a beviteli mezőbe, és válassza ki a gombot. A szöveg továbbítása a beszédfelismerési szolgáltatásba kerül, és beszédre szintetizálódik, amely a hangszórón szól.

  [![Képernyőkép a Unity játék ablakában futó rövid útmutatóról](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Ellenőrizze a [Konzol ablakban](https://docs.unity3d.com/Manual/Console.html) a hibakeresési üzeneteket.

## <a name="additional-options-to-run-this-application"></a>Az alkalmazás futtatásának további lehetőségei

Ez az alkalmazás is telepíthető az Android, mint a Windows önálló alkalmazás vagy UWP alkalmazás.
Tekintse meg a [mintatára](https://aka.ms/csspeech/samples) a rövid útmutató/céles-unity mappában, amely leírja a konfigurációt a további célokat.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
