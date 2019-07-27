---
title: 'Gyors útmutató: Beszéd fordítása C# , (UWP) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy egyszerű Univerzális Windows-platform-(UWP-) alkalmazást fog létrehozni a felhasználói beszéd rögzítéséhez, lefordítani egy másik nyelvre, és kiírja a szöveget a parancssorba. Ez az útmutató Windows-felhasználók számára készült.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 07/23/2019
ms.author: erhopf
ms.openlocfilehash: e73f15525aa679a3ba0242dab897adf999fe7e09
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559268"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Gyors útmutató: Beszéd fordítása a Speech SDK for C# (UWP) használatával

A gyors üzembe helyezési lehetőség a [beszéd-szöveg](quickstart-csharp-uwp.md), a [szöveg-beszéd](quickstart-text-to-speech-csharp-uwp.md) és a [hang – első virtuális asszisztens](quickstart-virtual-assistant-csharp-uwp.md)számára is elérhető.

Ebben a rövid útmutatóban egy egyszerű Univerzális Windows-platform (UWP) alkalmazást fog létrehozni, amely rögzíti a számítógép mikrofonjának felhasználói beszédét, lefordítja a beszédet, és valós időben írja át a lefordított szöveget a parancssorba. Ez az alkalmazás a 64 bites Windows rendszeren fut, és a [SPEECH SDK NuGet csomaggal](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-mel készült.

A beszédfelismeréshez elérhető nyelvek teljes listáját a [nyelvi támogatás](language-support.md)című témakörben tekintheti meg.

> [!NOTE]
> A UWP lehetővé teszi olyan alkalmazások fejlesztését, amelyek a Windows 10 rendszert támogató bármely eszközön futnak, beleértve a PC-ket, az Xboxot, a Surface Hubt és az egyéb eszközöket.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Az alkalmazás felhasználói felületét az XAML-lel lehet definiálni. Nyissa meg a `MainPage.xaml` fájlt a Megoldáskezelőben. A tervező XAML nézetében szúrja be a következő XAML-kódrészletet `</Grid>`a és a közé `<Grid>` .

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Nyissa meg a `MainPage.xaml.cs` háttérkód-forrásfájlt (a `MainPage.xaml` alatt található meg). Cserélje a teljes kódot a következőre.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. A fájl `SpeechTranslationFromMicrophone_ButtonClicked` kezelőjében cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. A `SpeechTranslationFromMicrophone_ButtonClicked` kezelőben cserélje le a `YourServiceRegion` sztringet az előfizetéséhez társított [régióra](regions.md) (ez a `westus` régió, ha az ingyenes próbaverzióra regisztrált).

1. Mentse a projekten végrehajtott összes módosítást.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávon válassza a **Létrehozás** > **Megoldás fordítása** elemet. A kód fordításának hiba nélkül végbe kell mennie.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Megoldás fordítása lehetőség](media/sdk/qs-csharp-uwp-08-build.png "Sikeres létrehozás")

1. Indítsa el az alkalmazást. A menüsávon válassza a **Hibakeresés** > **Hibakeresés indítása** elemet, vagy nyomja le az **F5** billentyűt.

    ![A Visual Studio képernyőképe, amelyen ki van emelve a Hibakeresés indítása lehetőség](media/sdk/qs-csharp-uwp-09-start-debugging.png "Hibakeresés indítása az alkalmazáson")

1. Felugrik egy ablak. Jelölje be a **Mikrofon engedélyezése** lehetőséget, majd hagyja jóvá a felugró engedélykérést.

    ![Az engedélykérés képernyőképe](media/sdk/qs-csharp-uwp-10-access-prompt.png "Hibakeresés indítása az alkalmazáson")

1. Válassza a **Beszédfelismerés mikrofon bemenettel** lehetőséget, és mondjon egy angol kifejezést vagy mondatot a mikrofonba. A rendszer továbbítja a beszédet a Speech Service-be, majd szöveggé alakítja át, amely ugyanabban az ablakban meg is jelenik.

    ![A beszédfelismerés felhasználói felületének képernyőképe](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták C# feltárása a githubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
