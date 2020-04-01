---
title: 'Rövid útmutató: IOS-alkalmazás létrehozása'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy iOS-alkalmazást a Térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6304077a26f5c0ecb91e1ec4936bd79b3d839d95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79471217"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Rövid útmutató: Hozzon létre egy iOS-alkalmazást az Azure Spatial Anchors segítségével swift vagy C objektív ben

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy iOS-alkalmazást az [Azure Spatial Anchors](../overview.md) használatával swift vagy Objective-C-ben. Az Azure Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóság élményeket hozzon létre olyan objektumok használatával, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, egy ARKit iOS-alkalmazással fog rendelkezni, amely képes menteni és felidézni egy térbeli horgonyt.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A Térbeli horgonyok fiókazonosítójának és a fiókkulcsának konfigurálása
> * Telepítés és futtatás iOS-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- A fejlesztő engedélyezte a macOS-gépet az <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> és a <a href="https://cocoapods.org" target="_blank">CocoaPods</a> legújabb verziójával.
- Git keresztül telepített HomeBrew:
  1. Írja be a következő parancsot egyetlen `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`sorként a terminálon: . 
  1. Futtassa a következőket: `brew install git` és `brew install git-lfs`.
  1. Frissítse a git `git lfs install` config -ot (az aktuális felhasználó esetében) vagy `git lfs install --system` (az egész rendszerre vonatkozóan).
- Egy fejlesztő engedélyezte <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">az ARKit-kompatibilis</a> iOS-eszközt.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A mintaprojekt megnyitása

A terminál segítségével hajtsa végre a következő műveleteket.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Telepítse a szükséges hüvelyeket a CocoaPods segítségével:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Nyissa meg a `iOS/Swift/` címet.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Nyissa meg a `iOS/Objective-C/` címet.

```bash
cd ./iOS/Objective-C/
```

---

Futtassa `pod install --repo-update` a CocoaPods telepítéséhez a projekthez.

Most nyissa `.xcworkspace` meg az Xcode-ot.

> [!NOTE]
> A hibaelhárítási lépéseket [itt](#cocoapods-issues-on-macos-catalina-1015) olvashatja, ha a macOS Catalina (10.15) rendszerre való frissítés után a CocoaPod-problémákkal kapcsolatos problémákat okoz.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcs konfigurálása

A következő lépés az, hogy konfigurálja az alkalmazást, hogy használja a fiókazonosító és a fiókkulcs. A [Térbeli horgonyok erőforrás beállításakor](#create-a-spatial-anchors-resource)szövegszerkesztőbe másolta őket.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Nyissa meg a következő fájlt: `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Keresse `spatialAnchorsAccountKey` meg a `Set me` mezőt, és cserélje le a számlakulcsot.

Keresse `spatialAnchorsAccountId` meg a `Set me` mezőt, és cserélje le a számlaazonosítóra.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Nyissa meg a következő fájlt: `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Keresse `SpatialAnchorsAccountKey` meg a `Set me` mezőt, és cserélje le a számlakulcsot.

Keresse `SpatialAnchorsAccountId` meg a `Set me` mezőt, és cserélje le a számlaazonosítóra.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás telepítése iOS-készülékre

Csatlakoztassa az iOS-készüléket a Machez, és állítsa be az **aktív sémát** az iOS-készülékre.

![Eszköz kiválasztása](./media/get-started-ios/select-device.png)

Válassza **a Build( Build, majd az aktuális séma) lehetőséget.**

![Telepítés és futtatás](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Ha hibaüzenetet `library not found for -lPods-SampleObjC` lát, valószínűleg `.xcodeproj` a fájl `.xcworkspace`helyett a fájlt nyitotta meg. Nyissa `.xcworkspace` meg a és próbálkozzon újra.

Az Xcode-ban állítsa le az alkalmazást a **Stop (Leállítás)** gomb lenyomásával.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods kérdések macOS Catalina (10,15)

Ha a közelmúltban frissített macOS Catalina (10,15), és cocoaPods telepítve előre, CocoaPods lehet egy törött `.xcworkspace` állapotban, és nem megfelelően beállítani a hüvelyek és a projekt fájlokat. A probléma megoldásához a következő parancsok futtatásával kell újratelepítenie a CocoaPods-ot:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Az alkalmazás összeomlik, amikor az iOS 10.3.1-re telepíti egy személyes kiépítési profilból/fejlesztői fiókból 

Ha az iOS-alkalmazást iOS 10.3.1-es rendszerre telepíti egy személyes kiépítési profilból/fejlesztői fiókból, a következő hibaüzenet jelenhet meg: `Library not loaded: @rpath/ADAL...`. 

A probléma megoldása:

- Használjon olyan létesítési profilt, amely nem személyes csoportprofil (fizetős fejlesztői fiók).
- Telepítse alkalmazását egy iOS 13.3-as vagy korábbi rendszert futtató iOS-eszközre, vagy az iOS 13.4 béta- vagy kiadási verzióját futtató rendszerre.
- További információ a [veremtúlcsordulásról.](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid)


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
