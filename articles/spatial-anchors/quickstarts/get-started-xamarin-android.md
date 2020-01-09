---
title: 'Gyors útmutató: Xamarin Android-alkalmazás létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Android-alkalmazást a Xamarin térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465182"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Gyors útmutató: Xamarin Android-alkalmazás létrehozása az Azure térbeli Horgonyokkal

Ez a rövid útmutató ismerteti, hogyan hozhat létre Android-alkalmazást az [Azure térbeli](../overview.md)Xamarin használatával. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy olyan Android-alkalmazással fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * Üzembe helyezés és Futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:
- Windows vagy macOS rendszerű számítógép:
  - Windows használata esetén:
    - A <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 +</a>naprakész verziója.
    - <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>.
    - <a href="https://git-lfs.github.com/">Git-LFS</a>.
  - MacOS használata esetén:
    - A <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio for Mac 8.1 +</a>verziójának naprakész verziója.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git MacOS rendszerhez</a>.
    - <a href="https://git-lfs.github.com/">Git-LFS</a>.
- A Xamarin. Android legújabb verziója telepítve van és fut a választott platformon. A Xamarin. Android telepítésével kapcsolatos útmutatóért tekintse meg a [Xamarin. Android telepítési](https://docs.microsoft.com/xamarin/android/get-started/installation/index) útmutatóit.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztők számára engedélyezett</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore alkalmas</a> Android-eszköz.
  - Előfordulhat, hogy a számítógépe számára további eszközillesztők szükségesek az Android-eszközkel való kommunikációhoz. További információ: [itt](https://developer.android.com/studio/run/device.html).
- Az alkalmazásnak a **1,8**ARCore kell megcéloznia.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nyissa meg `Xamarin/SampleXamarin.sln` a Visual Studióban.

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

Nyissa meg a `Xamarin/SampleXamarin.Common/AccountDetails.cs` alkalmazást.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le a `Set me`t a fiók kulcsára.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le a `Set me`t a fiók azonosítójával.

## <a name="deploy-the-app-to-your-android-device"></a>Az alkalmazás üzembe helyezése Android-eszközön

Kapcsolja be az androidos eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez USB-kábellel.

Állítsa az indítási projektet a **SampleXamarin. Android**értékre, módosítsa a **megoldás konfigurációját** a **kiadásra**, majd válassza ki azt az eszközt, amelyet telepíteni kíván az eszköz-választó legördülő menüben.

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Visual Studio-konfiguráció](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Válassza a **hibakeresés** > a **hibakeresés elindítása** az alkalmazás üzembe helyezéséhez és elindításához lehetőséget.

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Visual Studio-konfiguráció](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Az alkalmazás üzembe helyezése és elindítása előtt válassza a **futtatás** > **Indítás hibakeresés nélkül** lehetőséget.

---

Az alkalmazásban válassza az **alapszintű** lehetőséget a bemutató futtatásához, és kövesse az utasításokat a horgony elhelyezéséhez és felidézéséhez.

> ![képernyőkép 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![képernyőkép 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![képernyőkép 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
