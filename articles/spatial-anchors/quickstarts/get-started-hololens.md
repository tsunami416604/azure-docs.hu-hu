---
title: Rövid útmutató – HoloLens-alkalmazás létrehozása az Azure térbeli Horgonyokkal | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre HoloLens-alkalmazást térbeli horgonyok használatával.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94cae186cee099618772f53d2b820e12f20cad64
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562402"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Gyors útmutató: HoloLens-alkalmazás létrehozása az Azure térbeli Horgonyokkal, a C++/WinRT és a DirectX szolgáltatásban

Ez a rövid útmutató ismerteti, hogyan hozhat létre HoloLens-alkalmazást az [Azure térbeli](../overview.md) /WinRT és a C++DirectX használatával. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy HoloLens-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * Üzembe helyezés és Futtatás HoloLens-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:
- Egy Windows rendszerű gép, amelyen a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> telepítve van a **univerzális Windows-platform-fejlesztési** számítási feladattal és a **Windows 10 SDK (10.0.18362.0 vagy újabb)** összetevővel, valamint a <a href="https://git-scm.com/download/win" target="_blank">git for Windows</a>rendszerrel.
- A [ C++Visual studióhoz készült/WinRT Visual Studio-bővítményt (VSIX)](https://aka.ms/cppwinrt/vsix) a [Visual Studio piactérről](https://marketplace.visualstudio.com/)kell telepíteni.
- HoloLens-eszköz, amelyen engedélyezve van a [fejlesztői mód](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) . Ehhez a cikkhez egy HoloLens-eszközre van szükség, amely a [Windows 10 október 2018 frissítését](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (más néven RS5) ismerteti. A HoloLens legújabb kiadásának frissítéséhez nyissa meg a **Beállítások** alkalmazást, lépjen a **frissítés & biztonság**elemre, majd kattintson a **frissítések keresése** gombra.
- Az alkalmazásnak a AppX-jegyzékben kell beállítania a **spatialPerception** képességet.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Megnyitás `HoloLens\DirectX\SampleHoloLens.sln` a Visual Studióban.

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

Nyissa meg `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`a t.

Keresse meg `SpatialAnchorsAccountKey` a mezőt, `Set me` és cserélje le a fiókot a fiók kulcsára.

Keresse meg `SpatialAnchorsAccountId` a mezőt, `Set me` és cserélje le a azonosítót a fiókazonosító értékre.

## <a name="deploy-the-app-to-your-hololens"></a>Az alkalmazás üzembe helyezése a HoloLens

Módosítsa a **megoldás** konfigurációját a kiadásra, módosítsa a megoldási **platformot** **x86**-ra, majd válassza az **eszköz** lehetőséget a telepítési cél beállításai közül.

Ha a 2. HoloLens használja, az **ARM** -et használja a **megoldási platformként**az **x86**helyett.

![Visual Studio-konfiguráció](./media/get-started-hololens/visual-studio-configuration.png)

Kapcsolja be a HoloLens eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez egy USB-kábellel.

Válassza a hibakeresés**indítása** az alkalmazás üzembe helyezéséhez és a hibakeresés megkezdéséhez lehetőséget.  > 

A horgonyok elhelyezéséhez és felidézéséhez kövesse az alkalmazás utasításait.

A Visual Studióban állítsa le az alkalmazást úgy, hogy kiválasztja a **hibakeresés leállítása** vagy a **SHIFT + F5**billentyűkombinációt.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
