---
title: 'Gyors útmutató: A C# .NET Core használata a Cognitive Services beszédfelismerő SDK Windows alatt beszédfelismerést |} A Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Ismerje meg, hogyan ismerhetik fel a beszéd, a C# .NET Core használata a Cognitive Services beszédfelismerő SDK Windows alatt
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: f00653d0c58f52c73126693422ab0a485540dd7a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072580"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Gyors útmutató: Recognize speech, a C# .NET Core, a beszéd SDK-val Windows alatt

Ebből a cikkből elsajátíthatja, hogyan hozzon létre egy C# konzolalkalmazást a .NET Core Windows beszédfelismerés lefényképezze a Cognitive Services beszédfelismerő SDK segítségével.
Az alkalmazás alapján a [Microsoft Cognitive Services beszédfelismerő SDK NuGet-csomag](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-ben.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. Lásd: [próbálja ki ingyenesen a speech service](get-started.md).
* Windows rendszerű számítógépek működő mikrofonnal.
* [A Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition vagy újabb verziója.
* A **.NET Core platformfüggetlen fejlesztési** számítási feladatot a Visual Studióban. Engedélyezheti a **eszközök** \> **első eszközeivel és szolgáltatásaival**.

  ![.NET Core platformfüggetlen fejlesztési engedélyezése](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. A Visual Studio 2017 hozzon létre egy új Visual C# .NET Core-Konzolalkalmazást. Az a **új projekt** párbeszédpanelen, a bal oldali ablaktáblán, bontsa ki a **telepített** \> **Visual C#** \> **.NET Core**majd **Console App (.NET Core)**. Adja meg a projekt nevére, *helloworld*.

    ![Hozzon létre Visual C# Konzolalkalmazás (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# Konzolalkalmazás (.NET Core) létrehozása")

1. Telepítse, és hivatkozni a [beszéd SDK NuGet-csomagot](https://aka.ms/csspeech/nuget). A megoldáskezelőben kattintson a jobb gombbal a megoldás, és válassza ki **NuGet-csomagok kezelése megoldáshoz**.

    ![Kattintson a jobb gombbal a NuGet-csomagok kezelése megoldáshoz](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "NuGet-csomagok kezelése megoldáshoz")

1. A jobb felső sarokban található a **csomag forrása** mezőben válassza **Nuget.org**. Keresse meg és telepítse a `Microsoft.CognitiveServices.Speech` csomagot, és telepítse őket a **helloworld** projekt.

    ![Telepítse a NuGet-csomag Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "telepítse a Nuget-csomag")

1. A felugró licenc képernyőn fogadja el a licenc:

    ![Fogadja el a licencfeltételeket](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "fogadja el a licencfeltételeket")

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Az a `Program.cs` fájlt a Visual Studio-projektek, cserélje ki az összes kódot a következőre:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulccsal végzett.

1. Cserélje le a karakterláncot `YourServiceRegion` együtt a [régió](regions.md) az előfizetéséhez tartozó (például `westus` az ingyenes próba-előfizetésre).

1. Mentse a módosításokat a projekthez.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávban válassza **összeállítása** > **megoldás fordítása**. A kódot kell fordítási hibák nélkül most:

    ![A build sikeres létrehozása](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "build sikeres létrehozása")

1. Indítsa el az alkalmazást. A menüsávban válassza **Debug** > **Start Debugging**, vagy nyomja le az **F5**.

    ![Az alkalmazás elindításához az into hibakeresés](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "indítsa el az alkalmazást into hibakeresés")

1. A konzolablakban hibaikonra, azaz valami (angolul), és felszólítja a.
A felismerés eredményét képernyő jelenik meg.

    ![Sikeres felismerés után konzolkimenetet](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Konzolkimenetet követően sikeres felismerése")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg az ehhez a mintához a `quickstart/csharp-dotnetcore-windows` mappát.

## <a name="next-steps"></a>További lépések

- [Beszéd fordítása](how-to-translate-speech.md)
- [Akusztikai modell testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modell testreszabása](how-to-customize-language-model.md)
