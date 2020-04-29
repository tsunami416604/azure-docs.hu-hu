---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72882313"
---
## <a name="android"></a>[Android](#tab/Android)

A Java Android-minta támogatja az eszközök közötti megosztást.
Nyissa meg `SharedActivity.java` a fájlt a Android Studio Samples mappájában. Adja meg az előző lépésben beszerzett URL-címet (a ASP.NET Web App Azure-telepítésből) a `SharingAnchorsServiceUrl` `SharedActivity.java` fájlban lévő értékként. Cserélje le `index.html` az URL-címet `api/anchors`a alkalmazásban. A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

A Objective-C iOS-minta támogatja az eszközök közötti megosztást.
Nyissa meg `SharedDemoViewController.m` a fájlt a Samples mappában. Adja meg az előző lépésben beszerzett URL-címet (a ASP.NET Web App Azure-telepítésből) a `SharingAnchorsServiceUrl` `SharedActivity.java` fájlban lévő értékként. Cserélje le `index.html` az URL-címet `api/anchors`a alkalmazásban. A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

A Xamarin Android-és iOS-minták egyaránt támogatják az eszközök közötti megosztást.
Nyissa meg `AccountDetails.cs` a fájlt a Samples mappában. Adja meg az előző lépésben beszerzett URL-címet (a ASP.NET Web App Azure-telepítésből) a `AnchorSharingServiceUrl` `SharedActivity.java` fájlban lévő értékként. Cserélje le `index.html` az URL-címet `api/anchors`a alkalmazásban. A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android-eszköz beállítása

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>IOS-eszköz beállítása

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>A fiók azonosítójának és kulcsának konfigurálása

A **projekt** ablaktáblán navigáljon, `Assets/AzureSpatialAnchorsPlugin/Examples` és nyissa `AzureSpatialAnchorsLocalSharedDemo.unity` meg a jelenet fájlt.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

A **projekt** ablaktáblán navigáljon a `Assets\AzureSpatialAnchors.Examples\Resources`következőhöz:. Válassza a(z) `SpatialAnchorSamplesConfig` lehetőséget. Ezután az **ellenőr** ablaktáblán adja `Sharing Anchors Service url` meg a (ASP.net Web App Azure-beli üzembe helyezése) értékét `Base Sharing Url`a következő helyett `index.html` :. `api/anchors` A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors`.

Mentse a jelenetet a **fájl** > **mentése**lehetőség kiválasztásával.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

### <a name="deploy-to-android-device"></a>Üzembe helyezés Android-eszközön

Jelentkezzen be az Android-eszközén, és csatlakoztassa a számítógéphez USB-kábellel.

A **Build-beállítások** megnyitásához válassza a **fájl** > -**létrehozási beállítások**lehetőséget.

A **build (jelenetek**) területen győződjön meg arról, hogy az összes jelenet mellett Pipa jel van.

Győződjön meg arról, hogy az **exportálási projekt** nem rendelkezik pipa jellel. Válassza **a létrehozás és Futtatás**lehetőséget. A rendszer kérni fogja a `.apk` fájl mentését. Bármelyik nevet kiválaszthatja.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Üzembe helyezés iOS-eszközön

A **Build-beállítások** megnyitásához válassza a **fájl** > -**létrehozási beállítások**lehetőséget.

A **build (jelenetek**) területen győződjön meg arról, hogy az összes jelenet mellett Pipa jel van.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

A Xcode-ben állítsa le az alkalmazást a **Leállítás**lehetőség kiválasztásával.
