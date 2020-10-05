---
title: 'Gyors útmutató: iOS-alkalmazás létrehozása'
description: Megtudhatja, hogyan hozhat létre iOS-alkalmazást az Azure térbeli horgonyok használatával programozott módon a Swift-ben vagy a Objective-C-ben.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 2df66979d1e8f400f2630f5e531cd201244fdc6d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87809986"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Gyors útmutató: iOS-alkalmazás létrehozása Azure térbeli Horgonyokkal Swift vagy Objective-C nyelven

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy iOS-alkalmazást az [Azure térbeli horgonyok](../overview.md) használatával Swift vagy Objective-C nyelven. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy ARKit iOS-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * Üzembe helyezés és Futtatás iOS-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- A <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> és a <a href="https://cocoapods.org" target="_blank">CocoaPods</a> legújabb verziójával rendelkező, a fejlesztőknek készült MacOS-es gép.
- Git telepítése a HomeBrew használatával:
  1. Adja meg a következő parancsot a terminál egyetlen sorában: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . 
  1. Futtassa a következőket: `brew install git` és `brew install git-lfs`.
  1. Frissítse a git-konfigurációját `git lfs install` (az aktuális felhasználó esetében) vagy `git lfs install --system` (a teljes rendszer esetében).
- A fejlesztők által engedélyezett <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibilis</a> IOS-eszköz.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

A terminál használatával hajtsa végre a következő műveleteket.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

A szükséges hüvelyek telepítése a CocoaPods használatával:

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

Futtassa `pod install --repo-update` a parancsot a CocoaPods telepítéséhez a projekthez.

Most nyissa meg a `.xcworkspace` Xcode.

> [!NOTE]
> Tekintse meg a hibaelhárítási lépéseket [itt](#cocoapods-issues-on-macos-catalina-1015) , ha a MacOS Catalina (10,15) verzióra történő frissítés után CocoaPod problémák léptek fel.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Nyissa meg a következő fájlt: `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Keresse meg a `spatialAnchorsAccountKey` mezőt, és cserélje le a `Set me` fiókot a fiók kulcsára.

Keresse meg a `spatialAnchorsAccountId` mezőt, és cserélje le a azonosítót `Set me` a fiókazonosító értékre.

Keresse meg a `spatialAnchorsAccountDomain` mezőt, és cserélje le a `Set me` fiókot a fiók tartományára.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Nyissa meg a következő fájlt: `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le a `Set me` fiókot a fiók kulcsára.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le a azonosítót `Set me` a fiókazonosító értékre.

Keresse meg a `SpatialAnchorsAccountDomain` mezőt, és cserélje le a `Set me` fiókot a fiók tartományára.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás üzembe helyezése iOS-eszközön

Az iOS-eszköz csatlakoztatása a Mac számítógéphez, és az **aktív séma** beállítása az iOS-eszközre.

![Eszköz kiválasztása](./media/get-started-ios/select-device.png)

Válassza **a létrehozás lehetőséget, majd futtassa az aktuális sémát**.

![Üzembe helyezés és Futtatás](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Ha hibaüzenet jelenik meg `library not found for -lPods-SampleObjC` , akkor a fájl helyett valószínűleg megnyithatja a `.xcodeproj` fájlt `.xcworkspace` . Nyissa meg a t `.xcworkspace` , és próbálkozzon újra.

A Xcode-ben állítsa le az alkalmazást a **Leállítás**gomb megnyomásával.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods kapcsolatos problémák macOS Catalinaen (10,15)

Ha nemrég frissítette a macOS Catalina (10,15) alkalmazást, és előzőleg telepítette a CocoaPods-t, előfordulhat, hogy a CocoaPods sérült állapotban van, és nem tudja megfelelően konfigurálni a hüvelyeket és a `.xcworkspace` Project fájlokat. A probléma megoldásához a következő parancsok futtatásával újra kell telepítenie a CocoaPods:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Az alkalmazás összeomlik, amikor egy személyes létesítési profilból/fejlesztői fiókból telepít iOS-10.3.1 

Ha IOS-alapú 10.3.1 helyez üzembe egy személyes létesítési profilból/fejlesztői fiókból, a következő hibaüzenet jelenhet meg: `Library not loaded: @rpath/ADAL...` . 

A hiba megoldása érdekében:

- Olyan létesítési profilt használjon, amely nem személyes csapat profilja (fizetős fejlesztői fiók).
- Az alkalmazás üzembe helyezése iOS 13,3 vagy korábbi rendszerű iOS-eszközön, vagy az iOS 13,4 bétaverziójának vagy a kiadási verziójának futtatásával.
- További információ erről a hibáról [stack overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
