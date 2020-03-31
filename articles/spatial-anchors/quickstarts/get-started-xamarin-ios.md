---
title: 'Rövid útmutató: Xamarin iOS-alkalmazás létrehozása'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy iOS-alkalmazást a Xamarin használatával térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5e217a33c8b461a438e2d0209fe6733850634e6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465148"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Rövid útmutató: Xamarin iOS-alkalmazás létrehozása az Azure Spatial Anchors alkalmazással

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy iOS-alkalmazást a Xamarin használatával az [Azure Spatial Anchors](../overview.md)használatával. Az Azure Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóság élményeket hozzon létre olyan objektumok használatával, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, egy iOS-alkalmazással fog rendelkezni, amely képes menteni és felidézni egy térbeli horgonyt.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A Térbeli horgonyok fiókazonosítójának és a fiókkulcsának konfigurálása
> * Telepítés és futtatás iOS-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:
- MacOS High Sierra (10.13) vagy újabb rendszert futtató Mac a következőkkel:
  - Az Xcode és az iOS SDK legújabb verziója az [App Store-ból.](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)
  - A Visual Studio for <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Mac 8.1+</a>naprakész verziója.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git macOS rendszerhez</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A mintaprojekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nyissa `Xamarin/SampleXamarin.sln` meg a Visual Studio alkalmazást.

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcs konfigurálása

A következő lépés az, hogy konfigurálja az alkalmazást, hogy használja a fiókazonosító és a fiókkulcs. A [Térbeli horgonyok erőforrás beállításakor](#create-a-spatial-anchors-resource)szövegszerkesztőbe másolta őket.

Nyissa meg a következő fájlt: `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Keresse `SpatialAnchorsAccountKey` meg a `Set me` mezőt, és cserélje le a számlakulcsot.

Keresse `SpatialAnchorsAccountId` meg a `Set me` mezőt, és cserélje le a számlaazonosítóra.

## <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás telepítése iOS-készülékre

Kapcsolja be az iOS-készüléket, jelentkezzen be, és csatlakoztassa a számítógéphez USB-kábellel.

Állítsa az indítási projektet **SampleXamarin.iOS -ra,** módosítsa a **Megoldás konfigurációját** **kiadásra,** és válassza ki azt az eszközt, amelyet telepíteni szeretne az eszközválasztó legördülő legördülő menüben.

![Visual Studio konfigurációja](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Válassza **a Start** > **futtatása hibakeresés nélkül** lehetőséget az alkalmazás üzembe helyezéséhez és elindításához.

Az alkalmazásban válassza az **Alapgombok** lehetőséget a bemutató futtatásához, és kövesse az utasításokat a horgony helyének és visszahívásának helyéhez és visszahívásához.

> ![Képernyőkép](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![1](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![Képernyőkép 2 Képernyőkép 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
