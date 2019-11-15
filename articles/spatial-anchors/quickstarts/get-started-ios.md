---
title: Rövid útmutató – iOS-alkalmazás létrehozása az Azure térbeli Horgonyokkal | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy iOS-alkalmazást térbeli horgonyok használatával.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: a1d2fa083c0083423767484b20e296a3080e4ebe
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092118"
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
- A git telepítése a HomeBrew használatával történik. Adja meg a következő parancsot a terminál egyetlen sorában: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ezután futtassa `brew install git`.
- A fejlesztők által engedélyezett <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibilis</a> IOS-eszköz.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

A terminál használatával hajtsa végre a következő műveleteket.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

A szükséges hüvelyek telepítése a CocoaPods használatával:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Nyissa meg a `iOS/Swift/` címet.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Nyissa meg a `iOS/Objective-C/` címet.

```bash
cd ./iOS/Objective-C/
```

---

`pod install --repo-update` futtatásával telepítse a projekthez tartozó CocoaPods.

Most nyissa meg a `.xcworkspace`t a Xcode-ben.

> [!NOTE]
> Tekintse meg a hibaelhárítási lépéseket [itt](#cocoapods-issues-on-macos-catalina-1015) , ha a MacOS Catalina (10,15) verzióra történő frissítés után CocoaPod problémák léptek fel.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Nyissa meg `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Keresse meg a `spatialAnchorsAccountKey` mezőt, és cserélje le a `Set me`t a fiók kulcsára.

Keresse meg a `spatialAnchorsAccountId` mezőt, és cserélje le a `Set me`t a fiók azonosítójával.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Nyissa meg `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le a `Set me`t a fiók kulcsára.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le a `Set me`t a fiók azonosítójával.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás üzembe helyezése iOS-eszközön

Az iOS-eszköz csatlakoztatása a Mac számítógéphez, és az **aktív séma** beállítása az iOS-eszközre.

![Válassza ki az eszközt](./media/get-started-ios/select-device.png)

Válassza **a létrehozás lehetőséget, majd futtassa az aktuális sémát**.

![Üzembe helyezés és Futtatás](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Ha `library not found for -lPods-SampleObjC` hiba jelenik meg, akkor a `.xcworkspace`helyett valószínűleg megnyitotta a `.xcodeproj` fájlt. Nyissa meg a `.xcworkspace`, és próbálkozzon újra.

A Xcode-ben állítsa le az alkalmazást a **Leállítás**gomb megnyomásával.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods kapcsolatos problémák macOS Catalinaen (10,15)

Ha nemrég frissítette a macOS Catalina (10,15) alkalmazást, és előzőleg telepítette a CocoaPods-t, előfordulhat, hogy a CocoaPods sérült állapotban van, és nem tudja megfelelően konfigurálni a hüvelyeket és `.xcworkspace` a Project-fájlokat. A probléma megoldásához a következő parancsok futtatásával újra kell telepítenie a CocoaPods:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
