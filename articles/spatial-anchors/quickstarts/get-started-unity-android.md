---
title: Rövid útmutató – Android Unity-alkalmazás létrehozása az Azure térbeli horgonyok |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Android-alkalmazás a Unity térbeli horgonyok használatával.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b35aafdad081a48c0d6048743f87e10c6a6b3a77
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752288"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Gyors útmutató: Egy Android Unity-alkalmazás létrehozása az Azure térbeli horgonyok

Ez a rövid útmutató bemutatja, hogyan hozzon létre egy Android Unity alkalmazás [Azure térbeli horgonyok](../overview.md). Az Azure térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy a vegyes valóság élmény helyükre kivonatuk eszközök idővel-objektumok segítségével. Ha elkészült, a Unity, menthet és egy térbeli kapcsolati alapot visszahívása használatával létrehozott ARCore Android alkalmazás lesz.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * Készítse elő a Unity-létrehozási beállítások
> * Töltse le és importálja a ARCore SDK for Unity
> * A térbeli horgonyok fiók azonosítóját és a fiókkulcsot konfigurálása
> * Az Android Studio project exportálása
> * Üzembe helyezése és futtatása az Android-eszközökön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- A gép egy Windows vagy MacOS rendszerű <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> és <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3-as</a> telepítve.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">engedélyezve fejlesztői</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">képes ARCore</a> Android-eszközön.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Nyissa meg a mintaprojektet a Unity-nél

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcsának konfigurálása

Az a **projekt** ablaktáblán keresse meg `Assets/AzureSpatialAnchorsPlugin/Examples` , és nyissa meg a `AzureSpatialAnchorsBasicDemo.unity` jelenetfájl.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenet kiválasztásával **fájl** -> **mentése**.

## <a name="export-the-android-studio-project"></a>Az Android Studio project exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza ki **exportálása** egy párbeszédpanel megnyitásához. Ezután válasszon egy mappát az Android Studio project exportálása.

Az Exportálás befejeződése után egy mappa megjelenik az exportált Android Studio-projektet tartalmazó nevű almappában **HelloAR U3D**.

## <a name="deploy-the-android-application"></a>Az Android-alkalmazás üzembe helyezése

Nyissa meg az Android Studiót, és válassza ki **Android Studio projekt megnyitása**. Ezután válassza ki a **HelloAR U3D** almappát a exportált Android Studio-projektet, és kattintson a **OK**.

Megnyitás, alapján egy értesítés fog megjelenni a Gradle-burkoló használata kéri. Válassza ki **OK** a Gradle-burkoló használata, és nyissa meg a projektet.

Az Android-eszközön Power, jelentkezzen be, és csatlakoztassa a Számítógépet, USB-kábel segítségével.

Válassza ki **futtatása** az Android Studio eszköztáron.

![Az Android Studio üzembe helyezése és futtatása](./media/get-started-unity-android/android-studio-deploy-run.png)

Válassza ki az Androidos eszközt a **válassza ki a központi telepítési cél** párbeszédpanel, és válassza ki **OK** futtathatják az alkalmazást az Android-eszközön.

Kövesse az utasításokat az alkalmazásban kívánja helyezni horgonyra visszaírásához.

Állítsa le az alkalmazást kiválasztásával **leállítása** az Android Studio eszköztáron.

![Android Studio Stop](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Megosztás térbeli horgonyok eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
