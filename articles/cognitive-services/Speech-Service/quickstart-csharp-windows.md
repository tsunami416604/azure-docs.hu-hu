---
title: 'Gyors üzembe helyezés: A kognitív szolgáltatások beszéd C# SDK esetében a Windows beszéd felismerésére |} Microsoft Docs'
description: 'Útmutató: a C# SDK használatával beszéd szolgáltatás beszéd felismerésére.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: f0a2ec617c71f9e86610017d3b6fa961bbd14322
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083086"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>Gyors üzembe helyezés: Ismeri fel a beszédfelismerés kognitív szolgáltatások beszéd C# SDK használatával

Ebben a cikkben megismerheti a C# Konzolalkalmazás létrehozása a Windows szöveg beszédfelismerés átírni kognitív szolgáltatások beszéd SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerés szolgáltatás előfizetés kulcsot. Lásd: [a beszéd service ingyenes kipróbálása](get-started.md).
* A Visual Studio 2017, Community Edition vagy újabb verzióját.
* A **.NET asztali fejlesztési** munkaterhelés a Visual Studióban. Engedélyezheti a **eszközök** \> **beolvasása eszköz és funkció**. 

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Hozzon létre egy új Visual C# Konzolalkalmazást a Visual Studio 2017. Az a **új projekt** párbeszédpanelen, a bal oldali ablaktáblán bontsa ki **telepített** majd **Konzolalkalmazás (.NET-keretrendszer)**. A projekt nevét adja meg *CsharpHelloSpeech*.

    ![Visual C# Konzolalkalmazás (.NET-keretrendszer) létrehozása](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Visual C# Konzolalkalmazás létrehozása")

2. Telepítse, és hivatkozzon a [beszéd SDK NuGet-csomag](https://aka.ms/csspeech/nuget). A Megoldáskezelőben kattintson a jobb gombbal a megoldás, és válassza **NuGet-csomagok kezelése megoldáshoz**.

    ![Kattintson a jobb gombbal a NuGet-csomagok kezelése megoldáshoz](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "NuGet-csomagok kezelése megoldáshoz")

3. A jobb felső sarokban lévő a **csomag forrása** mezőben válassza **Nuget.org**. Keresse meg és telepítse a `Microsoft.CognitiveServices.Speech` csomagot, majd telepítheti azokat a **CsharpHelloSpeech** projekt.

    ![Telepítse a NuGet-csomag Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-08-vs-cs-nuget-install.png "telepítése Nuget-csomag")

4. A licencszerződés képernyőjén ugrik fel fogadja el a licencfeltételeket:

    ![Fogadja el a licencfeltételeket](media/sdk/speechsdk-09-vs-cs-nuget-license.png "fogadnia a licencfeltételeket")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>A számítógép architektúrájának megfelelő platform-konfiguráció létrehozása

Ebben a szakaszban ad hozzá egy új platform a konfigurációt, amely megfelel a processzorarchitektúra.

1. A Configuration Manager elindítása. Válassza ki **Build** > **a Configuration Manager**.

    ![Indítsa el a configuration manager](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "indítsa el a configuration Managerben")

2. Az a **Configuration Manager** párbeszédpanelen adjon hozzá egy új platformon. Az a **aktív megoldás platform** legördülő listában válassza **új**.

    ![Adja hozzá a configuration manager ablak alatt egy új platform](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "egy új platform alatt a configuration manager-ablak hozzáadása")

3. Ha 64 bites Windows fut, hozzon létre egy új platform-konfiguráció `x64`. Ha 32 bites Windows fut, hozzon létre egy új platform-konfiguráció `x86`. Az ebben a cikkben létrehoz egy `x64` platform beállításait. 

    ![64 bites Windows esetén, vegye fel a "x64" nevű új platform](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "Hozzáadás x64 platform")

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Az a `Program.cs` a Visual Studio-projekt törzsét cserélje a `Program` a következő osztályra. Ellenőrizze, hogy az előfizetés és a régió lecseréli egy, a szolgáltatás beszerezte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. Miután a kódot a `Main()` metódust kell hasonlítania az alábbi képernyőfelvételen látható módon:

    ![Miután a kód fő metódus](media/sdk/speechsdk-17-vs-cs-paste-code.png "végső fő metódus")

3. A Visual Studio IntelliSense mutatja be, amely nem oldható fel a beszédfelismerés SDK-osztályok hivatkozásainak. Ez a hiba megoldása érdekében adja hozzá a következő `using` nyilatkozat elején a kód (akár manuálisan, vagy a Visual Studio használatával [gyors műveletek](https://docs.microsoft.com/visualstudio/ide/quick-actions)).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![A gyors művelettel adja hozzá a hiányzó utasítás használatával](media/sdk/speechsdk-18-vs-cs-add-using.png "problémák megoldásához IntelliSense")

4. Győződjön meg arról, hogy az IntelliSense kiemelés oldva, és mentse a módosításokat a projekthez.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Az alkalmazás létrehozása. A menüsávban válassza **Build** > **megoldás fordítása**. A kódot kell fordítási hibák nélkül most:

    ![Sikeres build](media/sdk/speechsdk-20-vs-cs-build.png "sikeres build")

2. Az alkalmazás indításához. A menüsávban válassza **Debug** > **Start Debugging**, vagy nyomja le az ENTER **F5**. 

    ![Indítsa el az alkalmazást a hibakeresés](media/sdk/speechsdk-21-vs-cs-f5.png "indítsa el az alkalmazást a hibakeresés")

3. A konzolablakban ugrik fel, kéri fel valamit (az angol nyelvű).
A felismerési eredményét képernyőn jelenik meg.

    ![Konzol kimeneti után sikeres felismerés](media/sdk/speechsdk-22-cs-vs-console-output.png "konzol kimeneti sikeres használatát követően")

## <a name="download-code"></a>Kód letöltése

A minták legújabb készletét, tekintse meg a [kognitív szolgáltatások beszéd SDK minta GitHub-tárházban](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>További lépések

- [Beszéd fordítása](how-to-translate-speech.md)
- [Akusztikus modellek testreszabása](how-to-customize-acoustic-models.md)
- [Nyelvi modellek testreszabása](how-to-customize-language-model.md)
