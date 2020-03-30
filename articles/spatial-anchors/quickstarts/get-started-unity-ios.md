---
title: 'Rövid útmutató: Unity iOS-alkalmazás létrehozása'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy iOS-alkalmazást a Térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240589"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Rövid útmutató: Unity iOS-alkalmazás létrehozása az Azure Spatial Anchors alkalmazással

Ez a rövid útmutató bemutatja, hogyan hozhat létre Unity iOS-alkalmazást az [Azure Spatial Anchors](../overview.md)használatával. Az Azure Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóság élményeket hozzon létre olyan objektumok használatával, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, egy Unity-vel készült ARKit iOS-alkalmazással rendelkezik, amely képes menteni és felidézni egy térbeli horgonyt.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * Unity-összeállítási beállítások előkészítése
> * A Térbeli horgonyok fiókazonosítójának és a fiókkulcsának konfigurálása
> * Az Xcode projekt exportálása
> * Telepítés és futtatás iOS-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- A MacOS gép <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 vagy 2019.2</a>, a legújabb <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>, és <a href="https://cocoapods.org" target="_blank">CocoaPods</a> telepítve.
- Git keresztül telepített HomeBrew. Írja be a következő parancsot a `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`terminál egyetlen sorába: . Ezután `brew install git` fuss `brew install git-lfs`és .
- Egy fejlesztő engedélyezte <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">az ARKit-kompatibilis</a> iOS-eszközt.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Töltse le és nyissa meg a Unity mintaprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcs konfigurálása

A **Project** ablaktáblán `Assets/AzureSpatialAnchors.Examples/Scenes` keresse `AzureSpatialAnchorsBasicDemo.unity` meg és nyissa meg a jelenetfájlt.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenetet a **Fájlmentés** -> lehetőség**kiválasztásával.**

## <a name="export-the-xcode-project"></a>Az Xcode projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Kövesse az utasításokat az alkalmazásban, hogy helyezzen el és visszahívása horgonyt.

Ha végzett, állítsa le az alkalmazást a **Stop** in Xcode billentyű lenyomásával.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="rendering-issues"></a>Renderelési problémák

Az alkalmazás futtatásakor, ha nem látja a kamerát háttérként (például üres, kék vagy más textúrákat lát), akkor valószínűleg újra kell importálnia az eszközöket a Unity-ben. Állítsa le az alkalmazást. Az Egység felső menüjében válassza **az Eszközök -> Újraimportálja az összeset**lehetőséget. Ezután futtassa újra az alkalmazást.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods kérdések macOS Catalina (10,15)

Ha a közelmúltban frissített macOS Catalina (10,15), és cocoaPods telepítve előre, CocoaPods lehet egy törött `.xcworkspace` állapotban, és nem megfelelően beállítani a hüvelyek és a projekt fájlokat. A probléma megoldásához a következő parancsok futtatásával kell újratelepítenie a CocoaPods-ot:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Egység 2019.3

A változások megszakadása miatt a Unity 2019.3 jelenleg nem támogatott. Kérjük, használja a Unity 2019.1 vagy 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
