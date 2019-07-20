---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362525"
---
1. Nyissa meg a Visual Studio 2019 alkalmazást.

1. A Start ablakban válassza az **új projekt létrehozása**lehetőséget. 

1. Válassza a **konzol alkalmazás (.NET-keretrendszer)** elemet, majd kattintson a **tovább**gombra.

1. A **Project Name (projekt neve**) mezőben adja meg `helloworld`a nevet, majd válassza a **Létrehozás**lehetőséget.

1. A Visual Studio menüsávján válassza az **eszközök** > **eszközök és szolgáltatások**beolvasása lehetőséget, és győződjön meg arról, hogy elérhető-e a **.net Desktop-fejlesztési** feladat. Ha a munkaterhelés még nincs telepítve, jelölje be a jelölőnégyzetet, majd kattintson a **módosítás** gombra a telepítés elindításához. A letöltése és telepítése néhány percet is igénybe vehet.

   Ha a **.net Desktop-fejlesztés** melletti jelölőnégyzet be van jelölve, akkor bezárhatja a párbeszédpanelt.

   ![A .NET asztali fejlesztés engedélyezése](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

A következő lépés a [SPEECH SDK NuGet-csomag](https://aka.ms/csspeech/nuget)telepítése, így a kódban hivatkozhat rá.

1. A megoldáskezelő kattintson `helloworld`a jobb gombbal a elemre, majd válassza a **NuGet-csomagok kezelése** lehetőséget a NuGet csomagkezelő megjelenítéséhez.

   ![NuGet csomagkezelő](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. A jobb felső sarokban keresse meg a **csomag forrása** legördülő listát, és ellenőrizze, hogy a **nuget.org** van-e kiválasztva.

1. A bal felső sarokban válassza a **Tallózás**lehetőséget.

1. A keresőmezőbe írja be `Microsoft.CognitiveServices.Speech` a csomag kifejezést, majd nyomja le az ENTER billentyűt.

1. Válassza `Microsoft.CognitiveServices.Speech`a lehetőséget, majd a **telepítés** gombra kattintva telepítse a legújabb stabil verziót.

   ![Telepítse a Microsoft. CognitiveServices. Speech NuGet csomagot](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. A telepítés elindításához fogadja el az összes szerződést és licencet.

   A csomag telepítése után a rendszer megerősítő üzenetet jelenít meg a **Package Manager konzol** ablakban.

A konzol alkalmazás létrehozásához és futtatásához hozzon létre egy, a számítógép architektúrájának megfelelő platform-konfigurációt.

1. A menüsávban válassza a **Build** > **Configuration Manager**elemet. A **Configuration Manager** párbeszédpanel jelenik meg.

   ![Configuration Manager párbeszédpanel](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Az **aktív megoldási platform** legördülő listában válassza az **új**lehetőséget. Megjelenik az **új megoldás-platform** párbeszédpanel.

1. A **típus vagy a válassza ki az új platform** legördülő listát:
   - Ha 64 bites Windows rendszert futtat, válassza az **x64**elemet.
   - Ha 32 bites Windows rendszert futtat, válassza az **x86**lehetőséget.

1. Válassza **az OK** , majd a **Bezárás**lehetőséget.
