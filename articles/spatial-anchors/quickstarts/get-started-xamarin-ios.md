---
title: Rövid útmutató – Xamarin iOS-alkalmazás létrehozása az Azure térbeli Horgonyokkal | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy iOS-alkalmazást a Xamarin térbeli horgonyok használatával.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: ef25cf07326220be36ce8f67267428ffe1ac0728
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931643"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Gyors útmutató: Xamarin iOS-alkalmazás létrehozása az Azure térbeli Horgonyokkal

Ez a rövid útmutató ismerteti, hogyan hozhat létre iOS-alkalmazást az [Azure térbeli](../overview.md)Xamarin használatával. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy olyan iOS-alkalmazással fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * Üzembe helyezés és Futtatás iOS-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:
- MacOS rendszerű, magas Sierra (10,13) vagy újabb rendszert futtató Mac-kiszolgáló:
  - Az [App Store áruházból](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)telepített Xcode és iOS SDK legújabb verziója.
  - A <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio for Mac 8.1 +</a>verziójának naprakész verziója.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git macOS rendszerhez</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Megnyitás `Xamarin/SampleXamarin.sln` a Visual Studióban.

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

Nyissa meg `Xamarin/SampleXamarin.Common/AccountDetails.cs`a t.

Keresse meg `SpatialAnchorsAccountKey` a mezőt, `Set me` és cserélje le a fiókot a fiók kulcsára.

Keresse meg `SpatialAnchorsAccountId` a mezőt, `Set me` és cserélje le a azonosítót a fiókazonosító értékre.

## <a name="deploy-the-app-to-your-ios-device"></a>Az alkalmazás üzembe helyezése iOS-eszközön

Kapcsolja be az iOS-eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez USB-kábellel.

Állítsa az indítási projektet **SampleXamarin. iOS**értékre, módosítsa a **megoldás konfigurációját** a **kiadásra**, majd válassza ki azt az eszközt, amelyet telepíteni kíván az eszköz-választó legördülő menüben.

![Visual Studio-konfiguráció](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Az alkalmazás üzembe helyezéséhez és elindításához válassza a **Futtatás** > **indításkor hibakeresés nélkül** lehetőséget.

Az alkalmazásban válassza az alapszintű lehetőséget a bemutató futtatásához, és kövesse az utasításokat a horgony elhelyezéséhez és felidézéséhez.

> ![Képernyőfelvétel 1](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![képernyőkép 2](./media/get-started-xamarin-ios/screenshot-2.jpg)
> képernyőkép![3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
