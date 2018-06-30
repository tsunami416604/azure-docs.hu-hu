---
title: A C++ és a Windows beszéd SDK gyors üzembe helyezés |} Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a beszédfelismerés SDK a Windows és C++ kognitív szolgáltatásban.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 4dea1209548fb3bb4f846cdf7eaf82751f938bea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108844"
---
# <a name="quickstart-for-c-and-windows"></a>A C++ és a Windows gyors üzembe helyezés

A szolgáltatások kognitív beszéd SDK aktuális verziója `0.4.0`.

A C++-alapú Konzolalkalmazás létrehozása, amely a beszédfelismerés SDK használja a Windows asztal azt ismertetik.
Az alkalmazás alapul a [Microsoft kognitív szolgáltatások SDK NuGet-csomag](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) és a Microsoft Visual Studio 2017.

> [!NOTE]
> Ha a gyors üzembe helyezés C++ és a Linux keres, lépjen [Itt](quickstart-cpp-linux.md).<br>
> Ha a C# és a Windows egy gyors üzembe helyezés van szüksége, lépjen [Itt](quickstart-csharp-windows.md).

> [!NOTE]
> A gyors üzembe helyezés használatához a Számítógépnek működő mikrofon.<br>
> Felismeri a beszédfelismerés hang adott bemeneti fájlból minta tekintse meg a [minta](speech-to-text-sample.md#speech-recognition-from-a-file).

> [!NOTE]
> Győződjön meg arról, hogy a Visual Studio telepítése tartalmazza a **asztali fejlesztése a C++** munkaterhelés.
> Ha nem biztos benne, a ellenőrizze és javítsa ki ezeket a lépéseket használhatja: A Visual Studio 2017, válassza ki **eszközök** \> **beolvasása eszköz és funkció** és tudomásul Rákérdezés a felhasználói fiókok felügyelete kiválasztásával**Igen**.
> Az a **munkaterhelések** lapon, ha **asztali fejlesztése a C++** nem tartalmaz az set jelölőnégyzetét, állítsa be, majd kattintson a **módosítás** menti a módosításokat.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>Egy üres Konzolalkalmazás-projektet létrehozása

A Visual Studio 2017 hozzon létre egy új Visual C++ Windows asztali Windows-Konzolalkalmazás "CppHelloSpeech" nevű:

![Visual C++ Windows asztali Windows-Konzolalkalmazás létrehozása](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

Ha egy 64 bites Windows telepítés futtatja, nem kötelezően váltson a build platform `x64`:

![Váltás a build platform x64](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Telepítse, és hivatkozzon a beszédfelismerés SDK NuGet-csomag

> [!NOTE]
> Győződjön meg arról, a NuGet-Csomagkezelőt a Visual Studio 2017 telepítés engedélyezve van.
> A Visual Studio 2017, válassza ki **eszközök** \> **beolvasása eszköz és funkció** és tudomásul Rákérdezés a felhasználói fiókok felügyelete kiválasztásával **Igen**. Válassza ki a **az egyes összetevők** lapra, és keressen **NuGet-Csomagkezelő** alatt **eszközök Code**.
> Ha a jelölőnégyzet bejelölésével a bal oldalán nincs beállítva, ügyeljen arra, hogy állítsa be, majd kattintson a **módosítás** menti a módosításokat.
>
> ![A Visual Studio NuGet-Csomagkezelő engedélyezése ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

A Megoldáskezelőben, kattintson a jobb gombbal a megoldást, majd kattintson a **NuGet-csomagok kezelése megoldáshoz**.

![Kattintson a jobb gombbal a NuGet-csomagok kezelése megoldáshoz](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

A jobb felső sarokban lévő a **csomag forrása** mezőben, válassza a "Nuget.org".
Az a **Tallózás** fülre, keressen rá a "Microsoft.CognitiveServices.Speech" csomag, válassza ki azt, és ellenőrizze a **projekt** és **CppHelloSpeech** mezőbe a jobb oldalon, és Válassza ki **telepítése** az CppHelloSpeech projektben a telepítéshez.

![Microsoft.CognitiveServices.Speech NuGet-csomag telepítése](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

A licencszerződés képernyőjén ugrik fel fogadja el a licencfeltételeket:

![Fogadja el a licencfeltételeket](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

Cserélje le az alapértelmezett kezdő szintű kódot az alábbi kódra:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> Az Előfizetés kulcs cserélje le azt, amelyik kapott. <br>
> Cserélje le a [régió](regions.md) az előfizetéshez tartozó találhatóval, például cserélje `westus` az ingyenes próba-előfizetést.

![Az Előfizetés kulcs hozzáadása](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

A kódot kell fordítási hibák nélkül most:

![Sikeres build](media/sdk/speechsdk-16-vs-cpp-build.png)

Indítsa el a program az Indítás gombra vagy a F5 billentyűparanccsal hibakereső alatt:

![Indítsa el az alkalmazást a hibakeresés](media/sdk/speechsdk-17-vs-cpp-f5.png)

A konzolablakban felirat, kéri fel valamit (az angol nyelvű).
A felismerési eredményt a képernyőn jelenik meg.

![Sikeres felismerés után a konzol kimeneti](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>A minta letöltése

A minták legújabb készletét, tekintse meg a [kognitív szolgáltatások beszéd SDK minta GitHub-tárházban](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>További lépések

* Látogasson el a [minták lap](samples.md) további példák.
