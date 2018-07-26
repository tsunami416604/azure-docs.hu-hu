---
title: 'Gyors útmutató: A C# Windows, a Cognitive Services beszédfelismerő SDK használatával a .NET-keretrendszer a beszédfelismerést |} A Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést a Windows, a Cognitive Services beszédfelismerő SDK használatával a .NET-keretrendszer C#-ban
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: f3b181c8d65cf8618b7599a999b2b8edf20ed809
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259027"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>Gyors útmutató: Recognize speech mellett a Windows, a beszéd SDK-val .NET-keretrendszer C#-ban

Ebből a cikkből elsajátíthatja, hogyan hozzon létre egy C# konzolalkalmazást a beszédfelismerés lefényképezze a Cognitive Services beszédfelismerő SDK segítségével Windows .NET-keretrendszer.
Az alkalmazás össze lett a [Microsoft Cognitive Services beszédfelismerő SDK NuGet-csomag](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-ben.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. Lásd: [próbálja ki ingyenesen a speech service](get-started.md).
* Windows rendszerű számítógépek működő mikrofonnal.
* A Visual Studio 2017, a Community Edition vagy újabb verziója.
* A **.NET asztali fejlesztés** számítási feladatot a Visual Studióban. Engedélyezheti a **eszközök** \> **első eszközeivel és szolgáltatásaival**.

  ![.NET asztali fejlesztés engedélyezése](media/sdk/vs-enable-net-desktop-workload.png)

  ![.NET Core platformfüggetlen fejlesztési engedélyezése](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. A Visual Studio 2017 hozzon létre egy új Visual C#-Konzolalkalmazást. Az a **új projekt** párbeszédpanelen, a bal oldali ablaktáblán, bontsa ki a **telepített** \> **Visual C#** \> **Windows asztali** majd **Console App (.NET Framework)**. Adja meg a projekt nevére, *helloworld*.

    ![Hozzon létre Visual C# Konzolalkalmazás (.NET keretrendszer)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "létrehozása Visual C# Konzolalkalmazás (.NET keretrendszer)")

1. Telepítse, és hivatkozni a [beszéd SDK NuGet-csomagot](https://aka.ms/csspeech/nuget). A megoldáskezelőben kattintson a jobb gombbal a megoldás, és válassza ki **NuGet-csomagok kezelése megoldáshoz**.

    ![Kattintson a jobb gombbal a NuGet-csomagok kezelése megoldáshoz](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "NuGet-csomagok kezelése megoldáshoz")

1. A jobb felső sarokban található a **csomag forrása** mezőben válassza **Nuget.org**. Keresse meg a `Microsoft.CognitiveServices.Speech` csomagot, majd telepítse őket a **helloworld** projekt.

    ![Telepítse a NuGet-csomag Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "telepítse a Nuget-csomag")

1. Fogadja el a licencet, a megjelenő párbeszédpanelen.

    ![Fogadja el a licencfeltételeket](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "fogadja el a licencfeltételeket")

1. A Package Manager konzol a következő kimeneti sor jelenik meg.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>A számítógép architektúrájának megfelelő platform konfiguráció létrehozása

Ebben a szakaszban egy új platformra a konfigurációt, amely megfelel a processzor-architektúra hozzáadhat.

1. Indítsa el a Configuration Manager. Válassza ki **összeállítása** > **a Configuration Manager**.

    ![A configuration manager indítása](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "a configuration manager indítása")

1. Az a **Configuration Manager** párbeszédpanelen adjon hozzá egy új platformra. Az a **aktív megoldás platformját** legördülő listában válassza **új**.

    ![Adjon hozzá egy új platform szerint a configuration manager ablak](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "adjon hozzá egy új platform szerint a configuration manager ablak")

1. Ha 64 bites Windows futtatja, hozzon létre egy újat platform nevű `x64`. Ha 32 bites Windows futtatja, hozzon létre egy újat platform nevű `x86`. Ebben a cikkben létrehoz egy `x64` platformregisztrációt.

    ![A Windows 64 bites, vegye fel a "x64" nevű új platformra](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Hozzáadás x64 platform")

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Nyissa meg `Program.cs` projektre a Visual studióban, és cserélje le ezt a fájlt a kód a következő.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulccsal végzett.

1. Cserélje le a karakterláncot `YourServiceRegion` együtt a [régió](regions.md) az előfizetéséhez tartozó (például `westus` az ingyenes próba-előfizetésre).

1. Mentse a módosításokat a projekthez.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávban válassza **összeállítása** > **megoldás fordítása**. A kódot kell fordítási hibák nélkül most.

    ![A build sikeres létrehozása](media/sdk/qs-csharp-dotnet-windows-08-build.png "build sikeres létrehozása")

1. Indítsa el az alkalmazást. A menüsávban válassza **Debug** > **Start Debugging**, vagy nyomja le az **F5**.

    ![Az alkalmazás elindításához az into hibakeresés](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "indítsa el az alkalmazást into hibakeresés")

1. A konzolablakban jelenik meg, például valamit (angolul), és felszólítja a. A felismert szöveget majd ugyanabban az ablakban jelenik meg.

    ![Sikeres felismerés után konzolkimenetet](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Konzolkimenetet követően sikeres felismerése")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg az ehhez a mintához a `quickstart/csharp-dotnet-windows` mappát.

## <a name="next-steps"></a>További lépések

- [Beszéd fordítása](how-to-translate-speech.md)
- [Akusztikai modell testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modell testreszabása](how-to-customize-language-model.md)
