---
title: Rövid útmutató – Azure térbeli horgonyok Unity iOS-alkalmazás létrehozása |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre iOS-alkalmazás használatával térbeli horgonyok Unity.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37856c0833ecde1478d4bd588b8e3122e8eac0ca
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135229"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Gyors útmutató: Az Azure térbeli horgonyok Unity iOS-alkalmazás létrehozása

Ez a rövid útmutató bemutatja, hogyan hozzon létre egy Unity iOS app [Azure térbeli horgonyok](../overview.md). Az Azure térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy a vegyes valóság élmény helyükre kivonatuk eszközök idővel-objektumok segítségével. Ha elkészült, egy ARKit a Unity, menthet és egy térbeli kapcsolati alapot visszahívása használatával létrehozott iOS-alkalmazás lesz.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * Készítse elő a Unity-létrehozási beállítások
> * Töltse le és a Unity ARKit beépülő modul importálása
> * A térbeli horgonyok fiók azonosítóját és a fiókkulcsot konfigurálása
> * Az Xcode-projektben exportálása
> * Üzembe helyezése és futtatása az iOS-eszközökön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Olyan macOS-gépeken, <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a>, <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a>, és <a href="https://cocoapods.org" target="_blank">CocoaPods</a> telepítve.
- A Git telepítve van a HomeBrew-n keresztül. Adja meg a következő parancs a terminál egyetlen sorba: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ezután futtassa `brew install git`.
- A fejlesztő engedélyezve <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit kompatibilis</a> iOS-eszközön.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Töltse le és nyissa meg a Unity mintaprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcsának konfigurálása

Az a **projekt** ablaktáblán keresse meg `Assets/AzureSpatialAnchorsPlugin/Examples` , és nyissa meg a `AzureSpatialAnchorsBasicDemo.unity` jelenetfájl.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenet kiválasztásával **fájl** -> **mentése**.

## <a name="export-the-xcode-project"></a>Az Xcode-projektben exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Kövesse az utasításokat az alkalmazásban kívánja helyezni horgonyra visszaírásához.

> [!NOTE]
> Az alkalmazás futtatásakor, ha nem látja a kamerát a háttérben (a példányt egy üres, kék helyette tekintse meg vagy más textúrákhoz), majd valószínűleg újra importálnia kell a Unity-nél eszközök. Állítsa le az alkalmazást. Válassza a felső menüben, a Unity-nél, **eszközök -> importálja újra az összes**. Ezután futtassa újra az alkalmazást.

Az xcode-ban, állítsa le az alkalmazást a billentyű lenyomásával **leállítása**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Megosztás térbeli horgonyok eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
