---
title: 'Gyors útmutató: Xamarin iOS-alkalmazás létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy iOS-alkalmazást a Xamarin térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5e217a33c8b461a438e2d0209fe6733850634e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465148"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Rövid útmutató: Xamarin iOS-alkalmazás létrehozása az Azure térbeli Horgonyokkal

Ez a rövid útmutató ismerteti, hogyan hozhat létre iOS-alkalmazást az [Azure térbeli](../overview.md)Xamarin használatával. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy olyan iOS-alkalmazással fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * Üzembe helyezés és Futtatás iOS-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:
- MacOS rendszerű, magas Sierra (10,13) vagy újabb rendszert futtató Mac-kiszolgáló:
  - Az [App Store áruházból](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)telepített Xcode és iOS SDK legújabb verziója.
  - A <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio for Mac 8.1 +</a>verziójának naprakész verziója.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git MacOS rendszerhez</a>.
  - <a href="https://git-lfs.github.com/">Git-LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nyissa meg `Xamarin/SampleXamarin.sln` a Visual Studióban.

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

Nyissa meg a `Xamarin/SampleXamarin.Common/AccountDetails.cs` alkalmazást.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le a `Set me`t a fiók kulcsára.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le a `Set me`t a fiók azonosítójával.

## <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás üzembe helyezése iOS-eszközön

Kapcsolja be az iOS-eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez USB-kábellel.

Állítsa az indítási projektet **SampleXamarin. iOS**értékre, módosítsa a **megoldás konfigurációját** a **kiadásra**, majd válassza ki azt az eszközt, amelyet telepíteni kíván az eszköz-választó legördülő menüben.

![Visual Studio-konfiguráció](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Az alkalmazás üzembe helyezése és elindítása előtt válassza a **futtatás** > **Indítás hibakeresés nélkül** lehetőséget.

Az alkalmazásban válassza az **alapszintű** lehetőséget a bemutató futtatásához, és kövesse az utasításokat a horgony elhelyezéséhez és felidézéséhez.

> ![képernyőkép 1](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![képernyőkép 2](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![képernyőkép 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
