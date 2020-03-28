---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882313"
---
## <a name="android"></a>[Android](#tab/Android)

A Java android minta támogatja az eszközök közötti megosztást.
Nyissa meg `SharedActivity.java` a fájlt az Android Studio mintamappájából. Adja meg az előző lépésben (a ASP.NET webalkalmazás Azure-központi `SharingAnchorsServiceUrl` telepítésében) kapott URL-címet a `SharedActivity.java` fájlban lévő értékként. Cserélje `index.html` le az `api/anchors`url-t a- ra. Meg kell így `https://<app_name>.azurewebsites.net/api/anchors`néz ki: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Az Objective-C iOS minta támogatja az eszközök közötti megosztást.
Nyissa meg `SharedDemoViewController.m` a fájlt a mintamappában. Adja meg az előző lépésben (a ASP.NET webalkalmazás Azure-központi `SharingAnchorsServiceUrl` telepítésében) kapott URL-címet a `SharedActivity.java` fájlban lévő értékként. Cserélje `index.html` le az `api/anchors`url-t a- ra. Meg kell így `https://<app_name>.azurewebsites.net/api/anchors`néz ki: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Mind a Xamarin Android, mind az iOS minta támogatja az eszközök közötti megosztást.
Nyissa meg `AccountDetails.cs` a fájlt a mintamappában. Adja meg az előző lépésben (a ASP.NET webalkalmazás Azure-központi `AnchorSharingServiceUrl` telepítésében) kapott URL-címet a `SharedActivity.java` fájlban lévő értékként. Cserélje `index.html` le az `api/anchors`url-t a- ra. Meg kell így `https://<app_name>.azurewebsites.net/api/anchors`néz ki: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android-eszköz beállítása

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>IOS-eszköz beállítása

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>A fiókazonosító és a kulcs konfigurálása

A **Project** ablaktáblán `Assets/AzureSpatialAnchorsPlugin/Examples` keresse `AzureSpatialAnchorsLocalSharedDemo.unity` meg és nyissa meg a jelenetfájlt.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

A **Project** ablaktáblában `Assets\AzureSpatialAnchors.Examples\Resources`keresse meg a lehetőséget. Válassza a(z) `SpatialAnchorSamplesConfig` lehetőséget. Ezután a **Felügyelő** ablaktáblán adja `Sharing Anchors Service url` meg a (a ASP.NET `Base Sharing Url`webapp `index.html` `api/anchors`Azure deployment) értékét, a helyére. Meg kell így `https://<app_name>.azurewebsites.net/api/anchors`néz ki: .

Mentse a jelenetet a **Fájlmentés** > lehetőség**kiválasztásával.**

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

### <a name="deploy-to-android-device"></a>Telepítés Android-eszközre

Jelentkezzen be Android-eszközén, és csatlakoztassa a számítógéphez USB-kábellel.

A **Build-beállítások** megnyitásához válassza a **Fájlösszeállítás** > **beállításai lehetőséget.**

A **Jelenetek a buildben**területen területen győződjön meg arról, hogy minden jelenet mellett pipa van.

Győződjön meg arról, hogy **a Project exportálása** nem rendelkezik pipával. Válassza a **Build and Run**lehetőséget. A rendszer kéri a fájl `.apk` mentését. Bármilyen nevet választhatsz.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Telepítés iOS-eszközön

A **Build-beállítások** megnyitásához válassza a **Fájlösszeállítás** > **beállításai lehetőséget.**

A **Jelenetek a buildben**területen területen győződjön meg arról, hogy minden jelenet mellett pipa van.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Az Xcode-ban állítsa le az alkalmazást a **Leállítás**lehetőség kiválasztásával.
