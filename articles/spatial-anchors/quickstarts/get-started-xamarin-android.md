---
title: 'Rövid útmutató: Xamarin Android-alkalmazás létrehozása'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre androidos alkalmazást a Xamarin használatával térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465182"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Rövid útmutató: Xamarin Android-alkalmazás létrehozása az Azure Spatial Anchors alkalmazással

Ez a rövid útmutató ismerteti, hogyan hozhat létre androidos alkalmazást a Xamarin használatával az [Azure Spatial Anchors](../overview.md)használatával. Az Azure Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóság élményeket hozzon létre olyan objektumok használatával, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, egy Android-alkalmazással rendelkezik, amely képes menteni és visszahívni egy térbeli horgonyt.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A Térbeli horgonyok fiókazonosítójának és a fiókkulcsának konfigurálása
> * Telepítés és futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:
- Windows vagy macOS rendszerű számítógép:
  - Windows használata esetén:
    - A <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2+</a>naprakész verziója.
    - <a href="https://git-scm.com/download/win" target="_blank">Git windowsos</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
  - MacOS használata esetén:
    - A Visual Studio for <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Mac 8.1+</a>naprakész verziója.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git macOS rendszerhez</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
- A legújabb xamarin.Android telepített és futó a platform a választás. A Xamarin.Android telepítésével kapcsolatos útmutatót a [Xamarin.Android telepítési](https://docs.microsoft.com/xamarin/android/get-started/installation/index) útmutatójában talál.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztő engedélyezve és</a> <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore képes</a> Android készülék.
  - További eszközillesztőkre lehet szükség ahhoz, hogy a számítógép kommunikáljon androidos eszközével. További információ: [itt](https://developer.android.com/studio/run/device.html).
- Az alkalmazásnak az ARCore **1.8-at kell megcéloznia.**

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A mintaprojekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nyissa `Xamarin/SampleXamarin.sln` meg a Visual Studio alkalmazást.

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcs konfigurálása

A következő lépés az, hogy konfigurálja az alkalmazást, hogy használja a fiókazonosító és a fiókkulcs. A [Térbeli horgonyok erőforrás beállításakor](#create-a-spatial-anchors-resource)szövegszerkesztőbe másolta őket.

Nyissa meg a következő fájlt: `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Keresse `SpatialAnchorsAccountKey` meg a `Set me` mezőt, és cserélje le a számlakulcsot.

Keresse `SpatialAnchorsAccountId` meg a `Set me` mezőt, és cserélje le a számlaazonosítóra.

## <a name="deploy-the-app-to-your-android-device"></a>Az alkalmazás telepítése Android-eszközre

Kapcsolja be az Android-eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez USB-kábellel.

Állítsa az indítási projektet **SampleXamarin.Android**, módosítsa a **megoldás konfigurációját** **kiadásra**, és válassza ki azt az eszközt, amelyet telepíteni szeretne az eszközválasztó legördülő legördülő menüben.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Visual Studio konfigurációja](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Válassza **a Hibakeresés indítása** > **hibakeresés** lehetőséget az alkalmazás üzembe helyezéséhez és elindításához.

# <a name="macos"></a>[Macos](#tab/deploy-macos)

![Visual Studio konfigurációja](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Válassza **a Start** > **futtatása hibakeresés nélkül** lehetőséget az alkalmazás üzembe helyezéséhez és elindításához.

---

Az alkalmazásban válassza az **Alapgombok** lehetőséget a bemutató futtatásához, és kövesse az utasításokat a horgony helyének és visszahívásának helyéhez és visszahívásához.

> ![Képernyőkép](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![1](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![Képernyőkép 2 Képernyőkép 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
