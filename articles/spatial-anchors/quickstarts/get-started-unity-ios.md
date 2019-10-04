---
title: Rövid útmutató – Unity iOS-alkalmazás létrehozása az Azure térbeli Horgonyokkal | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan iOS-alkalmazást, amely az Unity használatával térbeli horgonyokat használ.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: be478a3f77996276f248d9b385954af813ac0397
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562489"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Gyors útmutató: Unity iOS-alkalmazás létrehozása Azure térbeli Horgonyokkal

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

- MacOS rendszerű gép, amely <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 +</a>, <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a>és <a href="https://cocoapods.org" target="_blank">CocoaPods</a> van telepítve.
- A git telepítése a HomeBrew használatával történik. Írja be a következő parancsot a terminál egyetlen sorába: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ezután futtassa a `brew install git`parancsot.
- A fejlesztők által engedélyezett <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibilis</a> IOS-eszköz.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Az Unity Sample projekt letöltése és megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A **projekt** ablaktáblán navigáljon `Assets/AzureSpatialAnchors.Examples/Scenes` , és nyissa `AzureSpatialAnchorsBasicDemo.unity` meg a jelenet fájlt.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenetet a **fájl** -> **mentése**lehetőség kiválasztásával.

## <a name="export-the-xcode-project"></a>A Xcode projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

A horgonyok elhelyezéséhez és felidézéséhez kövesse az alkalmazás utasításait.

Ha elkészült, állítsa le az alkalmazást a **Leállítás** a Xcode gomb megnyomásával.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az alkalmazás futtatásakor nem látja a kamerát háttérként (például üres, kék vagy más textúrákat lát), akkor valószínűleg újra kell importálnia az eszközöket az Unity-ben. Állítsa le az alkalmazást. Az egység felső menüjében válassza az **eszközök-> az összes újraimportálása**elemet. Ezután futtassa újra az alkalmazást.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
