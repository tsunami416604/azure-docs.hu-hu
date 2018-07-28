---
title: 'Gyors útmutató: Ismeri fel a beszéd, a C++, a Cognitive Services beszédfelismerő SDK használatával a Windows asztalon'
titleSuffix: Microsoft Cognitive Services
description: Hogyan ismerhetik fel a beszéd, a C++ Windows asztalán a Cognitive Services beszédfelismerő SDK használatával
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 7c93f9f450cc2d244a0d716401171cde02abf70b
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324571"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>Gyors útmutató: Ismeri fel a beszéd, a C++, a Speech SDK-val a Windows asztalon

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

C++-alapú Konzolalkalmazás létrehozása, amelyek a beszéd SDK-t használja a Windows asztali ismertetünk.
Az alkalmazás alapján a [Microsoft Cognitive Services beszédfelismerő SDK NuGet-csomag](https://aka.ms/csspeech/nuget) és a Microsoft Visual Studio 2017-ben.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. Lásd: [próbálja ki ingyenesen a speech service](get-started.md).
* Windows rendszerű számítógépek működő mikrofonnal.
* [A Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition vagy újabb verziója.
* A **asztali fejlesztés C++ használatával** számítási feladatot a Visual Studióban, és a **NuGet-Csomagkezelő** összetevőt a Visual Studióban.
  Engedélyezheti a is **eszközök** \> **első eszközök és szolgáltatások**alatt a **számítási feladatok** és **az egyes összetevők** lapok , illetve:

  ![Asztali fejlesztés C++ számítási feladat engedélyezése](media/sdk/vs-enable-cpp-workload.png)

  ![A Visual Studióban NuGet-Csomagkezelő engedélyezése ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

A Visual Studio 2017 hozzon létre egy új Visual C++ Windows asztali Windows-Konzolalkalmazást. Az a **új projekt** párbeszédpanelen, a bal oldali ablaktáblán, bontsa ki a **telepített** \> **Visual C++** \> **Windows asztali** majd **Windows-Konzolalkalmazást**. Adja meg a projekt nevére, *helloworld*.

![Visual C++ Windows asztali Windows-Konzolalkalmazás létrehozása](media/sdk/qs-cpp-windows-01-new-console-app.png)

Futtat egy 64 bites Windows telepítésére, ha igény szerint válthat a build platform `x64`:

![Váltson a build platform x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Telepítse, és hivatkozni a Speech SDK NuGet-csomag

A megoldáskezelőben kattintson a jobb gombbal a megoldás, és kattintson a **NuGet-csomagok kezelése megoldáshoz**.

![Kattintson a jobb gombbal a megoldás NuGet-csomagok kezelése](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

A jobb felső sarokban található a **csomag forrása** mezőben válassza ki a "Nuget.org".
Az a **Tallózás** fülre, keressen a "Microsoft.CognitiveServices.Speech" csomag, válassza ki, és ellenőrizze a **projekt** és **helloworld** mezőket jogát, és válassza **Telepítése** telepítheti a helloworld-projektbe.

> [!NOTE]
> A Cognitive Services beszédfelismerő SDK jelenlegi verziója `0.5.0`.

![Microsoft.CognitiveServices.Speech NuGet-csomag telepítése](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

A felugró licenc képernyőn fogadja el a licenc:

![Fogadja el a licencfeltételeket](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

1. Cserélje le az alapértelmezett kezdő kódot az alábbi kódra:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Cserélje le a karakterláncot `YourSubscriptionKey` az előfizetési kulccsal végzett.

1. Cserélje le a karakterláncot `YourServiceRegion` együtt a [régió](regions.md) az előfizetéséhez tartozó (például `westus` az ingyenes próba-előfizetésre).

1. Mentse a módosításokat a projekthez.

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

1. Hozza létre az alkalmazást. A menüsávban válassza **összeállítása** > **megoldás fordítása**. A kódot kell fordítási hibák nélkül most:

   ![A build sikeres létrehozása](media/sdk/qs-cpp-windows-06-build.png)

1. Indítsa el az alkalmazást. A menüsávban válassza **Debug** > **Start Debugging**, vagy nyomja le az **F5**.

   ![Indítsa el az alkalmazást into hibakeresés](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. A konzolablakban hibaikonra, azaz valami (angolul), és felszólítja a.
   A felismerés eredményét képernyőn jelennek meg.

   ![A sikeres felismerés után a konzol kimenete](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg az ehhez a mintához a `quickstart/cpp-windows` mappát.

## <a name="next-steps"></a>További lépések

* [A minták letöltése](speech-sdk.md#get-the-samples)
