---
title: 'Rövid útmutató: BeszédsDK a .'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval a platformc# beállításához a .NET Framework for Windows alatt a beszédfelismerési szolgáltatásSal SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: a09b969ee3e11aeb04f338cf035b21b5da9bd952
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925655"
---
Ez az útmutató bemutatja, hogyan telepítheti a [speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) for . Ha csak azt szeretné, hogy a csomag `Install-Package Microsoft.CognitiveServices.Speech` neve a kezdéshez a saját, fuss a NuGet konzolon.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio-projekt létrehozása és a beszédfelismerési SDK telepítése

Telepítenie kell a [Speech SDK NuGet csomagot,](https://aka.ms/csspeech/nuget) hogy hivatkozhass rá a kódban. Ehhez először létre kell hoznia egy **helloworld** projektet. Ha már rendelkezik a **.NET asztali fejlesztési** munkaterheléssel rendelkező projekttel, használhatja ezt a projektet, és a NuGet csomagkezelő használata elemre [ugorhat a Speech SDK telepítéséhez.](#use-nuget-package-manager-to-install-the-speech-sdk)

### <a name="create-helloworld-project"></a>Helloworld projekt létrehozása

1. Nyissa meg a Visual Studio 2019-et.

1. A Start ablakban válassza **az Új projekt létrehozása**lehetőséget. 

1. Az **Új projekt létrehozása** ablakban válassza a **Console App (.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Az **Új projekt konfigurálása** ablakban írja be a *helloworld* értéket a **Project nevében,** válassza vagy hozza létre a könyvtár elérési útját a **Hely területen,** majd válassza a **Create (Create) lehetőséget.**

1. A Visual Studio menüsorából válassza az **Eszközök** > **betöltődése és szolgáltatásai**lehetőséget, amely megnyitja a Visual Studio Installer programot, és megjeleníti a Módosítás **párbeszédpanelt.**

1. Ellenőrizze, hogy elérhető-e a **.NET asztali fejlesztési** munkaterhelés. Ha a munkaterhelés nincs telepítve, jelölje be a mellette lévő jelölőnégyzetet, majd a **Módosítás gombra** a telepítés elindításához. A letöltés és a telepítés eltarthat néhány percig.

   Ha a **.NET asztal fejlesztése** melletti jelölőnégyzet már be van jelölve, a **Bezárás** gombra választva lépjen ki a párbeszédpanelből.

   ![A .NET asztali fejlesztés engedélyezése](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zárja be a Visual Studio telepítőjét.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>A Speech SDK telepítése a NuGet csomagkezelővel

1. A Megoldáskezelőben kattintson a jobb gombbal a **helloworld** projektre, majd válassza a **NuGet csomagok kezelése parancsot** a NuGet csomagkezelő megjelenítéséhez.

   ![NuGet-csomagkezelő](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. A jobb felső sarokban keresse meg a **Csomagforrás** legördülő **`nuget.org`** lista, és győződjön meg arról, hogy ki van jelölve.

1. A bal felső sarokban válassza a **Tallózás gombot.**

1. A keresőmezőbe írja be a *Microsoft.CognitiveServices.Speech* parancsot, és válassza az **Enter lehetőséget.**

1. A keresési eredmények között válassza ki a **Microsoft.CognitiveServices.Speech** csomagot, majd a **Telepítés** lehetőséget a legújabb stabil verzió telepítéséhez.

   ![A Microsoft.CognitiveServices.Speech NuGet csomag telepítése](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. A telepítés elindításához fogadja el az összes megállapodást és licencet.

   A csomag telepítése után egy megerősítés jelenik meg a **Csomagkezelő konzol** ablakában.

### <a name="choose-target-architecture"></a>Célarchitektúra kiválasztása

A konzolalkalmazás létrehozásához és futtatásához hozzon létre egy platformkonfigurációt, amely megfelel a számítógép architektúrájának.

1. A menüsorból válassza a **Konfigurációkezelő összeállítása** > **lehetőséget.** Megjelenik a **Configuration Manager** párbeszédpanel.

   ![A Configuration Manager párbeszédpanel](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Az **Aktív megoldás platform** legördülő lista ában válassza az **Új**lehetőséget. Megjelenik **az Új megoldásplatform** párbeszédpanel.

1. A **Típus vagy az új platform** legördülő lista mezőjében:
   - Ha 64 bites Windows rendszert futtat, válassza az **x64**lehetőséget.
   - Ha 32 bites Windows rendszert futtat, válassza az **x86**lehetőséget.

1. Válassza **az OK gombot,** majd **a Bezárás gombot.**

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list.md)]
