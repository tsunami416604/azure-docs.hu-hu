---
title: 'Rövid útmutató: Unity iOS-alkalmazás létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan iOS-alkalmazást, amely az Unity használatával térbeli horgonyokat használ.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240589"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Gyors útmutató: Unity iOS-alkalmazás létrehozása az Azure térbeli Horgonyokkal

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Unity iOS-alkalmazást az [Azure térbeli horgonyok](../overview.md)használatával. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy olyan egységgel rendelkező ARKit iOS-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * Az egység létrehozási beállításainak előkészítése
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * A Xcode projekt exportálása
> * Üzembe helyezés és Futtatás iOS-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy <a href="https://unity3d.com/get-unity/download" target="_blank">2019,1 vagy 2019,2 egységgel</a>rendelkező MacOS-gép, amely a <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>legújabb verzióját és a <a href="https://cocoapods.org" target="_blank">CocoaPods</a> -t telepítette.
- A git telepítése a HomeBrew használatával történik. Adja meg a következő parancsot a terminál egyetlen sorában: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ezután futtassa `brew install git` és `brew install git-lfs`.
- A fejlesztők által engedélyezett <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibilis</a> IOS-eszköz.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Az Unity Sample projekt letöltése és megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A **projekt** ablaktáblán navigáljon a `Assets/AzureSpatialAnchors.Examples/Scenes` elemre, és nyissa meg a `AzureSpatialAnchorsBasicDemo.unity` jelenet fájlt.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenetet a **fájl** -> **Mentés**lehetőség kiválasztásával.

## <a name="export-the-xcode-project"></a>A Xcode projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

A horgonyok elhelyezéséhez és felidézéséhez kövesse az alkalmazás utasításait.

Ha elkészült, állítsa le az alkalmazást a **Leállítás** a Xcode gomb megnyomásával.

## <a name="troubleshooting"></a>Hibakeresés

### <a name="rendering-issues"></a>Renderelési problémák

Ha az alkalmazás futtatásakor nem látja a kamerát háttérként (például üres, kék vagy más textúrákat lát), akkor valószínűleg újra kell importálnia az eszközöket az Unity-ben. Állítsa le az alkalmazást. Az egység felső menüjében válassza az **eszközök-> az összes újraimportálása**elemet. Ezután futtassa újra az alkalmazást.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods kapcsolatos problémák macOS Catalinaen (10,15)

Ha nemrég frissítette a macOS Catalina (10,15) alkalmazást, és előzőleg telepítette a CocoaPods-t, előfordulhat, hogy a CocoaPods sérült állapotban van, és nem tudja megfelelően konfigurálni a hüvelyeket és `.xcworkspace` a Project-fájlokat. A probléma megoldásához a következő parancsok futtatásával újra kell telepítenie a CocoaPods:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Unity 2019,3

A változtatások miatt a 2019,3 egység jelenleg nem támogatott. Használja a 2019,1 vagy a 2019,2 egységet.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
