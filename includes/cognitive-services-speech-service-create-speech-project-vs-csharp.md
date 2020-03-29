---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71327064"
---
Visual Studio-projekt Windows-fejlesztési létrehozásához létre kell hoznia a projektet, be kell állítania a Visual Studio for .NET asztali fejlesztését, telepítenie kell a Speech SDK-t, és ki kell választania a célarchitektúrát.

### <a name="create-the-project-and-add-the-workload"></a>A projekt létrehozása és a munkaterhelés hozzáadása

A kezdéshez hozza létre a projektet a Visual Studióban, és győződjön meg arról, hogy a Visual Studio be van állítva a .NET asztali fejlesztéshez:

1. Nyissa meg a Visual Studio 2019-et.

1. A Start ablakban válassza **az Új projekt létrehozása**lehetőséget. 

1. Az **Új projekt létrehozása** ablakban válassza a **Console App (.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Az **Új projekt konfigurálása** ablakban írja be a *helloworld* értéket a **Project nevében,** válassza vagy hozza létre a könyvtár elérési útját a **Hely területen,** majd válassza a **Create (Create) lehetőséget.**

1. A Visual Studio menüsorából válassza az **Eszközök** > **betöltődése és szolgáltatásai**lehetőséget, amely megnyitja a Visual Studio Installer programot, és megjeleníti a Módosítás **párbeszédpanelt.**

1. Ellenőrizze, hogy elérhető-e a **.NET asztali fejlesztési** munkaterhelés. Ha a munkaterhelés nincs telepítve, jelölje be a mellette lévő jelölőnégyzetet, majd a **Módosítás gombra** a telepítés elindításához. A letöltés és a telepítés eltarthat néhány percig.

   Ha a **.NET asztal fejlesztése** melletti jelölőnégyzet már be van jelölve, a **Bezárás** gombra választva lépjen ki a párbeszédpanelből.

   ![A .NET asztali fejlesztés engedélyezése](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zárja be a Visual Studio telepítőjét.

### <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

A következő lépés a [Speech SDK NuGet csomag](https://aka.ms/csspeech/nuget)telepítése, így hivatkozhat rá a kódban.

1. A Megoldáskezelőben kattintson a jobb gombbal a **helloworld** projektre, majd válassza a **NuGet csomagok kezelése parancsot** a NuGet csomagkezelő megjelenítéséhez.

   ![NuGet-csomagkezelő](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. A jobb felső sarokban keresse meg a **Csomagforrás** legördülő lista, és győződjön meg arról, hogy **nuget.org** van kiválasztva.

1. A bal felső sarokban válassza a **Tallózás gombot.**

1. A keresőmezőbe írja be a *Microsoft.CognitiveServices.Speech* parancsot, és válassza az **Enter lehetőséget.**

1. A keresési eredmények között válassza ki a **Microsoft.CognitiveServices.Speech** csomagot, majd a **Telepítés** lehetőséget a legújabb stabil verzió telepítéséhez.

   ![A Microsoft.CognitiveServices.Speech NuGet csomag telepítése](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. A telepítés elindításához fogadja el az összes megállapodást és licencet.

   A csomag telepítése után egy megerősítés jelenik meg a **Csomagkezelő konzol** ablakában.

### <a name="choose-the-target-architecture"></a>A célarchitektúra kiválasztása

Most, hogy létrehozza és futtatja a konzolalkalmazást, hozzon létre egy platformkonfigurációt, amely megfelel a számítógép architektúrájának.

1. A menüsorból válassza a **Konfigurációkezelő összeállítása** > **lehetőséget.** Megjelenik a **Configuration Manager** párbeszédpanel.

   ![A Configuration Manager párbeszédpanel](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Az **Aktív megoldás platform** legördülő lista ában válassza az **Új**lehetőséget. Megjelenik **az Új megoldásplatform** párbeszédpanel.

1. A **Típus vagy az új platform** legördülő lista mezőjében:
   - Ha 64 bites Windows rendszert futtat, válassza az **x64**lehetőséget.
   - Ha 32 bites Windows rendszert futtat, válassza az **x86**lehetőséget.

1. Válassza **az OK gombot,** majd **a Bezárás gombot.**
