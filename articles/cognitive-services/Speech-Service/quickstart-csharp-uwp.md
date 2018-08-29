---
title: 'Gyors útmutató: Recognize speech C#-ban egy UWP-alkalmazásban a Cognitive Services beszédfelismerő SDK használatával'
titleSuffix: Microsoft Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést egy UWP-alkalmazásban a Cognitive Services beszédfelismerő SDK használatával
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: b709b2791ae7472689cc2c7ca747f75ce0f1e6bf
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126090"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>Gyors útmutató: Recognize speech, a beszéd SDK használatával egy UWP-alkalmazás

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megismerheti, hogyan hozhat létre a Cognitive Services beszédfelismerő SDK segítségével beszédfelismerés lefényképezze az univerzális Windows Platform (UWP) alkalmazást.
Az alkalmazás össze lett a [Microsoft Cognitive Services beszédfelismerő SDK NuGet-csomag](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-ben.

> [!NOTE]
> A Universal Windows Platform lehetővé teszi, hogy fejleszthet alkalmazásokat, amelyek futtatása bármilyen eszközön, amely támogatja a Windows 10 rendszerű számítógépek, az Xbox, a Surface hubra és a más eszközök. A beszédfelismerés SDK-t használó alkalmazások még nem adnak át a Windows App Certification Kit (WACK). Az alkalmazás, de előfordulhat, hogy nem jelenleg beküldeni a Windows Store közvetlenül lehetőség.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. Lásd: [próbálja ki ingyenesen a speech service](get-started.md).
* Windows rendszerű számítógépek (Windows 10 Fall Creators Update vagy újabb) működő mikrofonnal.
* [A Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition vagy újabb verziója.
* A **univerzális Windows Platform fejlesztési** Visual Studio.You számítási feladat használatával engedélyezheti a **eszközök** \> **első eszközeivel és szolgáltatásaival**.

  ![Fejlesztés a Universal Windows Platform engedélyezése](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. A Visual Studio 2017 hozzon létre egy új Visual C# Windows Universal üres alkalmazást. Az a **új projekt** párbeszédpanelen, a bal oldali ablaktáblán, bontsa ki a **telepített** \> **Visual C#** \> **Windows Universal** majd **üres alkalmazás (univerzális Windows)**. Adja meg a projekt nevére, *helloworld*.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Az a **új univerzális Windows Platform-projekt** ablak, amely a POP, válassza a **Windows 10 Fall Creators Update (10.0; Build 16299)** , **minimális verzió**, ez vagy bármely újabb verzióra, és **Cílová verze**, majd kattintson a **OK**.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Ha egy 64 bites Windows telepítés futtatja, a build platform előfordulhat, hogy váltson át `x64`.

   ![Váltson a build platform x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Jelenleg a Speech SDK az Intel-kompatibilis processzorral, de nem ARM támogatja.

1. Telepítse, és hivatkozni a [beszéd SDK NuGet-csomagot](https://aka.ms/csspeech/nuget). A megoldáskezelőben kattintson a jobb gombbal a megoldás, és válassza ki **NuGet-csomagok kezelése megoldáshoz**.

    ![Kattintson a jobb gombbal a megoldás NuGet-csomagok kezelése](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. A jobb felső sarokban található a **csomag forrása** mezőben válassza **Nuget.org**. Keresse meg és telepítse a `Microsoft.CognitiveServices.Speech` csomagot, és telepítse őket a **helloworld** projekt.

    ![Telepítse a NuGet-csomag Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "telepítse a Nuget-csomag")

1. Fogadja el a megjelenített licencfeltételeit.

    ![Fogadja el a licencfeltételeket](media/sdk/qs-csharp-uwp-06-nuget-license.png "fogadja el a licencfeltételeket")

1. A Package Manager konzol a következő kimeneti sor jelenik meg.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.6.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. A Megoldáskezelőben kattintson duplán a **Package.appxmanifest** az alkalmazásjegyzék szerkesztése.
   Válassza ki a **képességek** lapra, jelölje be a a **mikrofon** képesség, és mentse a módosításokat.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Ehhez kattintson duplán az alkalmazás felhasználói felületén szerkessze `MainPage.xaml` a Megoldáskezelőben. 

    A Tervező XAML nézetben, helyezze be a következő XAML-kódrészlet a rács címke (közötti `<Grid>` és `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Ehhez kattintson duplán a XAML háttérkód szerkesztése `MainPage.xaml.cs` a Megoldáskezelőben (alatt vannak csoportosítva a `MainPage.xaml` elem).
   Cserélje le ezt a fájlt a kód a következő.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Az a `SpeechRecognitionFromMicrophone_ButtonClicked` kezelő, cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulccsal végzett.

1. Az a `SpeechRecognitionFromMicrophone_ButtonClicked` kezelő, cserélje le a karakterláncot `YourServiceRegion` az a [régió](regions.md) az előfizetéséhez tartozó (például `westus` az ingyenes próba-előfizetésre).

1. A projekt összes módosításainak mentése.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávban válassza **összeállítása** > **megoldás fordítása**. A kódot kell fordítási hibák nélkül most.

    ![A build sikeres létrehozása](media/sdk/qs-csharp-uwp-08-build.png "build sikeres létrehozása")

1. Indítsa el az alkalmazást. A menüsávban válassza **Debug** > **Start Debugging**, vagy nyomja le az **F5**.

    ![Az alkalmazás elindításához az into hibakeresés](media/sdk/qs-csharp-uwp-09-start-debugging.png "indítsa el az alkalmazást into hibakeresés")

1. Egy grafikus felhasználói felület ablak jelenik meg. Először kattintson az **engedélyezése mikrofon** gombra, és tudomásul veszi a felugró engedély kérelmet.

    ![Az alkalmazás elindításához az into hibakeresés](media/sdk/qs-csharp-uwp-10-access-prompt.png "indítsa el az alkalmazást into hibakeresés")

1. Kattintson a **mikrofon bemenettel beszédfelismerés** és a egy rövid kifejezés beszéljen az eszköz mikrofonját. A felismert szöveget az ablakban jelenik meg.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg az ehhez a mintához a `quickstart/csharp-uwp` mappát.

## <a name="next-steps"></a>További lépések

- [Beszéd fordítása](how-to-translate-speech-csharp.md)
- [Akusztikai modell testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modell testreszabása](how-to-customize-language-model.md)
