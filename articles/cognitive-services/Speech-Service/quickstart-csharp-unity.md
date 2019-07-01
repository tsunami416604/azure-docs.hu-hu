---
title: 'Gyors útmutató: Beszédfelismerést, Unity - beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató segítségével hozzon létre egy hang-szöveg transzformációs alkalmazást Unity és a Speech SDK for Unity (bétaverzió). Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: dd8e760e658715b89bf45718ac571ccaeb5ade96
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465578"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Gyors útmutató: Ismeri fel a beszédfelismerés, beszédfelismerési SDK for Unity (bétaverzió)

Rövid útmutatók érhetők el is [szöveg-hang transzformációs](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez az útmutató segítségével hozzon létre egy hang-szöveg transzformációs alkalmazást a [Unity](https://unity3d.com/) és a Speech SDK for Unity (bétaverzió).
Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
Ha nem ismeri a Unity, javasoljuk, hogy tanulmányozza a [Unity felhasználói kézikönyv](https://docs.unity3d.com/Manual/UnityManual.html) a fejlesztés megkezdése előtt.

> [!NOTE]
> A beszédfelismerés SDK for Unity jelenleg bétaverzióban.
> Támogatja a Windows asztal (x86 és x64) vagy a Universal Windows Platform (x86, x64, ARM/ARM64) és Android (x86 ARM32/64).

## <a name="prerequisites"></a>Előfeltételek

A projekt teljesítéséhez a következők szükségesek:

* [Unity 2018.3 vagy újabb](https://store.unity.com/) a [Unity 2019.1 UWP ARM64 támogatása](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * ARM64-támogatást, telepítse a [választható build tools ARM64 és a Windows 10 SDK ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. [Igényeljen ingyenesen egy](get-started.md).
* A számítógép mikrofon való hozzáférést.

## <a name="create-a-unity-project"></a>Unity-projekt létrehozása

* Indítsa el a Unity és a **projektek** lapon válassza **új**.
* Adja meg **projektnév** , **csharp-unity**, **sablon** , **3D** , és jelölje ki a helyet.
  Válassza ki **Create project**.
* Miután egy kis időt a Unity-szerkesztő ablakban hamarosan megjelenik.

## <a name="install-the-speech-sdk"></a>A beszédfelismerés SDK telepítése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* A beszédfelismerés SDK for Unity (bétaverzió) (.unitypackage) Unity eszköz csomag van csomagolva.
  Töltse le [Itt](https://aka.ms/csspeech/unitypackage).
* A beszédfelismerés SDK importálásához válassza **eszközök** > **csomag importálása** > **egyéni csomag**.
  Tekintse meg a [Unity-dokumentáció](https://docs.unity3d.com/Manual/AssetPackages.html) részleteiről.
* A Fájlkereső válassza a fent letöltött beszéd SDK .unitypackage fájlt.
* Győződjön meg arról, hogy minden fájl ki van jelölve, és kattintson a **importálás**:

  ![Képernyőkép a Unity-szerkesztő a Speech SDK Unity-eszköz csomag importálásakor](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Adja hozzá a felhasználói felület

Hozzáadunk egy minimális felhasználói felület, a jelenet álló egy gombot, amellyel beszédfelismerés és a egy szövegmező, az eredmény megjelenítéséhez.

* Az a [hierarchia ablak](https://docs.unity3d.com/Manual/Hierarchy.html) (alapértelmezés szerint a bal oldalon), egy minta jelenet jelenik meg, hogy a Unity-az új projekt létrehozása.
* Kattintson a **létrehozás** gombra hierarchia ablak tetején, és válassza **felhasználói felület** > **gomb**.
* Ez hoz létre, amelyek a hierarchia ablakban látható három játék objektum: egy **gomb** objektum ágyazott egy **Vászonalapú** objektumot, és a egy **EventSystem** objektum.
* [Keresse meg a jelenet nézet](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , a vászonalapú és a gombra a megfelelő rálátással rendelkezik a [jelenet nézet](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Kattintson a **gomb** a hierarchia ablakának megnyitásával megjelenítheti annak beállításait az objektumot a [vizsgáló ablak](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa be a **Pos X** és **Pos Y** tulajdonságok **0**, így a gombot a vászon közepén közepén.
* Kattintson a **létrehozás** gombra hierarchia ablak tetején, és válassza **felhasználói felület** > **szöveg** hozhat létre egy szövegmezőt.
* Kattintson a **szöveg** a hierarchia ablakának megnyitásával megjelenítheti annak beállításait az objektumot a [vizsgáló ablak](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
* Állítsa be a **Pos X** és **Pos Y** tulajdonságok **0** és **120**, és állítsa be a **szélesség** és **Magasság** tulajdonságok **240** és **120** annak érdekében, hogy a szövegmezőbe, és a gomb ne legyenek átfedésben.

Ha elkészült, a felhasználói felület alábbi képernyőképhez hasonlóan kell kinéznie:

[![A rövid útmutató a Unity-szerkesztő felhasználói felület képernyőképe](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Az a [projekt ablakról](https://docs.unity3d.com/Manual/ProjectView.html) (alapértelmezés szerint a bal alsó), kattintson a **létrehozás** gombra, majd válassza ki  **C# parancsfájl**. Nevezze el a szkript `HelloWorld`.

1. Szerkessze a szkriptet a dupla kattintással.

   > [!NOTE]
   > Beállíthatja, hogy melyik Kódszerkesztő elindul a **szerkesztése** > **beállítások**, tekintse meg a [Unity felhasználói kézikönyv](https://docs.unity3d.com/Manual/Preferences.html).

1. Cserélje le az összes kód a következőket:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és cserélje le a karakterláncot `YourSubscriptionKey` az beszédszolgáltatások előfizetési kulccsal végzett.

1. Keresse meg és cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md). Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a módosításokat a parancsfájlt.

1. Vissza a Unity-szerkesztőben, a parancsfájl hozzá kell adnia egy összetevő, a játék objektumok egyikének.

   * Kattintson a **Vászonalapú** objektum a hierarchia ablakban. Ez a beállítás az megnyílik a [vizsgáló ablak](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon).
   * Kattintson a **összetevő felvétele** vizsgáló ablakban, majd keresse meg a HelloWorld parancsfájl hozunk létre felett, és adja hozzá a gombra.
   * Vegye figyelembe, hogy rendelkezik-e a "Hello World" összetevőt nem inicializált tulajdonságok **szöveget** és **Start helyreállítási gomb**, amely megfelel a nyilvános tulajdonságainak a `HelloWorld` osztály.
     Őket beállítani, kattintson az Objektumválasztóban (a kis kör ikon tulajdonság jobbra), és válassza ki a korábban létrehozott szöveg és a gomb objektumokat.

     > [!NOTE]
     > A gomb is van egy beágyazott szöveg objektumot. Győződjön meg arról, hogy ne véletlenül foglalkozzon azt szöveges kimenet (vagy nevezze át egy szöveges-objektum neve mező használatával a vizsgáló ablakban kavarodás elkerülése érdekében).

## <a name="run-the-application-in-the-unity-editor"></a>Futtassa az alkalmazást a Unity-szerkesztő

* Nyomja le az **lejátszása** a Unity eszköztáron (alább a menüsoron) gombra.

* Az alkalmazás indításakor, miután a gombra, és a egy angol nyelvű kifejezést vagy mondat beszéljen a számítógép mikrofon. A beszéd továbbítani a beszédszolgáltatások, és a megjelenített érzéseket szöveg, amely megjelenik az ablakban.

  [![A futó rövid útmutatóban a Unity-játék ablak képernyőképe](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Ellenőrizze a [konzolablakban](https://docs.unity3d.com/Manual/Console.html) hibakeresési üzeneteket.

* FELISMERVE speech elkészült, kattintson a **lejátszása** gombot, állítsa le az alkalmazást a Unity-szerkesztő eszköztár.

## <a name="additional-options-to-run-this-application"></a>Az alkalmazás futtatásához további beállítások

Ezt az alkalmazást is telepítheti az Android, Windows önálló alkalmazás, vagy UWP-alkalmazás.
Tekintse meg a [mintaadattár](https://aka.ms/csspeech/samples) ezeken a célokon konfigurációját leíró rövid/csharp-unity mappában.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a C# példák a Githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
