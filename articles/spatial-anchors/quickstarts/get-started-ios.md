---
title: Rövid útmutató – iOS-alkalmazás létrehozása az Azure térbeli horgonyok |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan hozhat létre iOS-alkalmazás használatával térbeli horgonyok.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: bb64e2b633957ca92636a03333be40aa275dfd25
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870244"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Gyors útmutató: IOS-alkalmazás létrehozása az Azure térbeli horgonyok, Objective-C vagy Swift

Ez a rövid útmutató bemutatja, hogyan hozzon létre egy IOS-es alkalmazás a [Azure térbeli horgonyok](../overview.md) Objective-c vagy Swift Az Azure térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy a vegyes valóság élmény helyükre kivonatuk eszközök idővel-objektumok segítségével. Ha elkészült, egy ARKit iOS-alkalmazást, menthet és egy térbeli kapcsolati alapot visszahívása lesz.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiók azonosítóját és a fiókkulcsot konfigurálása
> * Üzembe helyezése és futtatása az iOS-eszközökön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy fejlesztői engedélyezve van a macOS-számítógépen <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10 +</a> és <a href="https://cocoapods.org" target="_blank">CocoaPods</a> telepítve.
- A fejlesztő engedélyezve <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit kompatibilis</a> iOS-eszközön.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Nyissa meg a mintaprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Telepítse a szükséges podok CocoaPods segítségével:

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

***

Futtatás `pod install --repo-update` a projekthez a CocoaPods telepítéséhez.

Ezután nyissa meg a `.xcworkspace` az xcode-ban.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

***

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcsának konfigurálása

A következő lépés, hogy a fiók azonosítóját és a térbeli horgonyok erőforrás beállításakor korábban rögzített fiókkulcs az alkalmazás konfigurálásához.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Nyissa meg `iOS/Swift/SampleSwift/ViewController.swift`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le `Set me` és a fiókkulcsot.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le `Set me` fiók azonosítóval.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Nyissa meg `iOS/Objective-C/SampleObjC/ViewController.m`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le `Set me` és a fiókkulcsot.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le `Set me` fiók azonosítóval.

***

## <a name="deploy-the-app-to-your-ios-device"></a>Az iOS-eszközön az alkalmazás üzembe helyezése

Az IOS-es eszköz csatlakoztatása a Mac és a készlet a **aktív séma** az iOS-eszközre.

![Válassza ki az eszközt](./media/get-started-ios/select-device.png)

Válassza ki **hozhat létre és futtassa az aktuális séma**.

![Üzembe helyezése és futtatása](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Ha megjelenik egy `library not found for -lPods-SampleObjC` hiba történt, valószínűleg nyitotta meg a `.xcodeproj` fájl helyett a `.xcworkspace`. Nyissa meg a `.xcworkspace` , és próbálkozzon újra.

Az xcode-ban, állítsa le az alkalmazást a billentyű lenyomásával **leállítása**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Megosztás térbeli horgonyok eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
