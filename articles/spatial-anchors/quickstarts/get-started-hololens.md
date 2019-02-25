---
title: Rövid útmutató – HoloLens-alkalmazás létrehozása az Azure térbeli horgonyok |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan készíthetők térbeli horgonyok használatával HoloLens alkalmazások.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: a0e34ad8847ed3740af72b4c27dfbc0090cf3dfa
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752247"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Gyors útmutató: HoloLens-alkalmazás létrehozása a C + Azure térbeli horgonyok +/ WinRT és DirectX segítségével

Ez a rövid útmutató bemutatja, hogyan hozhat létre a HoloLens használatával [Azure térbeli horgonyok](../overview.md) a C + +/ WinRT és DirectX segítségével. Az Azure térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy a vegyes valóság élmény helyükre kivonatuk eszközök idővel-objektumok segítségével. Ha elkészült, a HoloLens-alkalmazást, amely mentheti, és egy térbeli kapcsolati alapot visszahívása lesz.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiók azonosítóját és a fiókkulcsot konfigurálása
> * Üzembe helyezése és futtatása a HoloLens eszköz

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Olyan Windows-gépeken, <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> telepített a **univerzális Windows Platform fejlesztési** számítási feladatok és a **Windows 10 SDK (10.0.17763.0 vagy újabb)** összetevő.
- A [C + +/ WinRT Visual Studio bővítmény (VSIX)](https://aka.ms/cppwinrt/vsix) a Visual Studióban telepítenie kell a a [Visual Studio-piactér](https://marketplace.visualstudio.com/).
- HoloLens eszköz [fejlesztői mód](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) engedélyezve van. Ez a cikk a HoloLens eszköz van szükség a [Windows 2018. október 10. frissítés](https://blogs.windows.com/windowsexperience/2018/10/02/find-out-whats-new-in-windows-and-office-in-october/) (más néven RS5). A legújabb verziót a HoloLens frissítéséhez nyissa meg a **beállítások** alkalmazást, lépjen a **frissítés és biztonság**, majd válassza a **frissítések keresése** gombra.
- Az alkalmazás be kell állítani a **spatialPerception** funkció az AppX-jegyzékfájlban.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Nyissa meg a mintaprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nyissa meg `HoloLens\DirectX\SampleHoloLens.sln` a Visual Studióban.

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcsának konfigurálása

A következő lépés, hogy a fiók azonosítóját és a térbeli horgonyok erőforrás beállításakor korábban rögzített fiókkulcs az alkalmazás konfigurálásához.

Nyissa meg `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le `Set me` és a fiókkulcsot.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le `Set me` fiók azonosítóval.

## <a name="deploy-the-app-to-your-hololens"></a>Az alkalmazás üzembe helyezése a HoloLens

Módosítsa a **megoldás konfigurációs** a **kiadási**, módosítása **megoldás platformját** való **x86**, válassza ki **eszköz**  az üzembe helyezés cél közül.

![Visual Studio Configuration](./media/get-started-hololens/visual-studio-configuration.png)

A HoloLens eszköz Power, jelentkezzen be, és csatlakoztassa a Számítógépet, USB-kábel segítségével.

Válassza ki **Debug** > **hibakeresés indítása** helyezze üzembe az alkalmazást, és a hibakeresés elindításához.

Kövesse az utasításokat az alkalmazásban kívánja helyezni horgonyra visszaírásához.

A Visual Studióban, állítsa le az alkalmazást a kiválasztásával **hibakeresés leállításához** vagy megnyomásával **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Megosztás térbeli horgonyok eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
