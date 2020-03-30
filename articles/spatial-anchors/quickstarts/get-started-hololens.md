---
title: 'Rövid útmutató: HoloLens-alkalmazás létrehozása DirectX-el'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre HoloLens-alkalmazást a Spatial Anchors használatával.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 01a9a8239e2997335f5123d63cfc664027cffa02
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75376358"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Rövid útmutató: HoloLens-alkalmazás létrehozása Az Azure Spatial Anchors alkalmazással C++/WinRT és DirectX nyelven

Ez a rövid útmutató ismerteti, hogyan hozhat létre HoloLens-alkalmazást az [Azure Spatial Anchors](../overview.md) használatával C++/WinRT és DirectX nyelven. Az Azure Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóság élményeket hozzon létre olyan objektumok használatával, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, lesz egy HoloLens alkalmazása, amely képes menteni és felidézni egy térbeli horgonyt.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A Térbeli horgonyok fiókazonosítójának és a fiókkulcsának konfigurálása
> * HoloLens-eszköz telepítése és futtatása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:
- Olyan Windows-gép, amelyen telepítve van a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> az **Univerzális Windows platform fejlesztési** munkaterhelésével és a Windows **10 SDK (10.0.18362.0 vagy újabb)** összetevővel. Telepítenie kell <a href="https://git-scm.com/download/win" target="_blank">a Git for Windows</a> és a <a href="https://git-lfs.github.com/">Git LFS rendszert</a>is.
- A Visual Studio [C++/WinRT Visual Studio extension (VSIX) bővítményét](https://aka.ms/cppwinrt/vsix) a [Visual Studio piactérről](https://marketplace.visualstudio.com/)kell telepíteni.
- HoloLens eszköz, amelynek [fejlesztői módja](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) engedélyezve van. Ez a cikk egy HoloLens-eszközt igényel a [Windows 10 október 2018 frissítéssel](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (más néven RS5). A HoloLens legújabb kiadására való frissítéshez nyissa meg a **Beállítások** alkalmazást, nyissa meg **a & biztonság frissítése**lehetőséget, majd válassza a Frissítések **keresése** gombot.
- Az alkalmazásnak be kell állítania a **spatialPerception** képességet az AppX-jegyzékfájlban.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A mintaprojekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nyissa `HoloLens\DirectX\SampleHoloLens.sln` meg a Visual Studio alkalmazást.

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcs konfigurálása

A következő lépés az, hogy konfigurálja az alkalmazást, hogy használja a fiókazonosító és a fiókkulcs. A [Térbeli horgonyok erőforrás beállításakor](#create-a-spatial-anchors-resource)szövegszerkesztőbe másolta őket.

Nyissa meg a következő fájlt: `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Keresse `SpatialAnchorsAccountKey` meg a `Set me` mezőt, és cserélje le a számlakulcsot.

Keresse `SpatialAnchorsAccountId` meg a `Set me` mezőt, és cserélje le a számlaazonosítóra.

## <a name="deploy-the-app-to-your-hololens"></a>Az alkalmazás telepítése a HoloLensen

Módosítsa a **megoldás konfigurációját** **kiadásra**, módosítsa a **Megoldásplatformot** **x86-ra**, és válassza az **Eszköz** lehetőséget a telepítési célbeállítások közül.

Ha holoLens 2-t használ, az **ARM64-et** használja **megoldásplatformként**az **x86**helyett.

![Visual Studio konfigurációja](./media/get-started-hololens/visual-studio-configuration.png)

Kapcsolja be a HoloLens-eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez USB-kábellel.

Válassza **a Hibakeresés** > **indítása hibakeresés** lehetőséget az alkalmazás üzembe helyezéséhez és a hibakeresés megkezdéséhez.

Kövesse az utasításokat az alkalmazásban, hogy helyezzen el és visszahívása horgonyt.

A Visual Studio-ban állítsa le az alkalmazást a **Hibakeresés leállítása** vagy a **Shift + F5**billentyű kombináció lenyomásával.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
