---
title: 'Rövid útmutató: a .NET-keretrendszerhez készült Speech SDK (Windows) platform beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platformot C# a Windows rendszerhez készült .net-keretrendszerben a SPEECH Service SDK-val.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: db00398efa638ac945bc188ad0c44f904059e689
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468802"
---
Ez az útmutató bemutatja, hogyan telepítheti a .NET-keretrendszerhez készült [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t (Windows).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio-projekt létrehozása és a Speech SDK telepítése

Telepítenie kell a [SPEECH SDK NuGet-csomagot](https://aka.ms/csspeech/nuget) , hogy a kód alapján lehessen hivatkozni rá. Ehhez először létre kell hoznia egy **HelloWorld** -projektet. Ha már rendelkezik egy olyan projekttel, amelyen elérhető a **.net Desktop fejlesztői** munkaterhelése, használhatja ezt a projektet, és ugorjon [a NuGet csomagkezelő használatára a Speech SDK telepítéséhez](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>HelloWorld projekt létrehozása

1. Nyissa meg a Visual Studio 2019 alkalmazást.

1. A Start ablakban válassza az **új projekt létrehozása**lehetőséget. 

1. Az **új projekt létrehozása** ablakban válassza a **Console app (.NET-keretrendszer)** lehetőséget, majd kattintson a **tovább**gombra.

1. Az **új projekt konfigurálása** ablakban írja be a *HelloWorld* nevet a **projekt neve**mezőbe, válassza ki vagy hozza létre a könyvtár elérési útját a **helyen**, majd válassza a **Létrehozás**lehetőséget.

1. A Visual Studio menüsávjában válassza az **eszközök** > eszközök **és szolgáltatások beolvasása**lehetőséget, amely megnyitja a Visual Studio telepítőjét, és megjeleníti a **módosítás** párbeszédpanelt.

1. Győződjön meg arról, hogy elérhető-e a **.net Desktop fejlesztői** munkaterhelése. Ha a munkaterhelés még nincs telepítve, jelölje be a jelölőnégyzetet, majd kattintson a **módosítás** gombra a telepítés elindításához. A letöltése és telepítése néhány percet is igénybe vehet.

   Ha a **.net Desktop-fejlesztés** melletti jelölőnégyzet már be van jelölve, kattintson a **Bezárás** gombra a párbeszédpanelből való kilépéshez.

   ![A .NET asztali fejlesztés engedélyezése](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zárjuk be a Visual Studio telepítőjét.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>A NuGet csomagkezelő használata a Speech SDK telepítéséhez

1. A Megoldáskezelő kattintson a jobb gombbal a **HelloWorld** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget a NuGet csomagkezelő megjelenítéséhez.

   ![NuGet-csomagkezelő](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. A jobb felső sarokban keresse meg a **csomag forrása** legördülő listát, és ellenőrizze, hogy a **`nuget.org`** ki van-e választva.

1. A bal felső sarokban válassza a **Tallózás**lehetőséget.

1. A keresőmezőbe írja be a *Microsoft. CognitiveServices. Speech* kifejezést, és válassza az **ENTER billentyűt**.

1. A keresési eredmények közül válassza ki a **Microsoft. CognitiveServices. Speech** csomagot, majd válassza a **telepítés** lehetőséget a legújabb stabil verzió telepítéséhez.

   ![Telepítse a Microsoft. CognitiveServices. Speech NuGet csomagot](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. A telepítés elindításához fogadja el az összes szerződést és licencet.

   A csomag telepítése után a rendszer megerősítő üzenetet jelenít meg a **Package Manager konzol** ablakban.

Ezután az alábbi [lépésekkel](#next-steps) léphet tovább.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]
