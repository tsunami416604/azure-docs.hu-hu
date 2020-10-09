---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: efd5ff494620d4fab3fb904d9bcf054b57a3290b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358768"
---
## <a name="android"></a>[Android](#tab/Android)

A Java Android-minta támogatja az eszközök közötti megosztást.
Nyissa meg a fájlt `SharedActivity.java` a Android Studio Samples mappájában. Adja meg az előző lépésben beszerzett URL-címet (a ASP.NET Web App Azure-telepítésből) a `SharingAnchorsServiceUrl` fájlban lévő értékként `SharedActivity.java` . Cserélje le az `index.html` URL-címet a alkalmazásban `api/anchors` . A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

A Objective-C iOS-minta támogatja az eszközök közötti megosztást.
Nyissa meg a fájlt `SharedDemoViewController.m` a Samples mappában. Adja meg az előző lépésben beszerzett URL-címet (a ASP.NET Web App Azure-telepítésből) a `SharingAnchorsServiceUrl` fájlban lévő értékként `SharedDemoViewController.m` . Cserélje le az `index.html` URL-címet a alkalmazásban `api/anchors` . A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors` .

Telepítse az alkalmazást az eszközre. Az alkalmazás elindítása után válassza a **koppintson a megosztott bemutató indításához** lehetőségre. Kövesse az alkalmazás utasításait. Kiválaszthatja **, hogy a rögzítési szám alapján megkeresi** a horgonyt, vagy **koppintson a horgony létrehozására, és mentse a szolgáltatásba**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

A Xamarin Android-és iOS-minták egyaránt támogatják az eszközök közötti megosztást.
Nyissa meg a fájlt `AccountDetails.cs` a Samples mappában. Adja meg az előző lépésben beszerzett URL-címet (a ASP.NET Web App Azure-telepítésből) a `AnchorSharingServiceUrl` fájlban lévő értékként `AccountDetails.cs` . Cserélje le az `index.html` URL-címet a alkalmazásban `api/anchors` . A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android-eszköz beállítása

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>IOS-eszköz beállítása

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

A **projekt** ablaktáblán navigáljon a következőhöz: `Assets\AzureSpatialAnchors.Examples\Resources` . Válassza a(z) `SpatialAnchorSamplesConfig` lehetőséget. Ezután az **ellenőr** ablaktáblán adja meg a `Sharing Anchors Service url` (ASP.net Web App Azure-beli üzembe helyezése) értékét a következő `Base Sharing Url` helyett: `index.html` `api/anchors` . A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors` .

Mentse a jelenetet a **fájl**mentése lehetőség kiválasztásával  >  **Save**.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

### <a name="deploy-to-android-device"></a>Üzembe helyezés Android-eszközön

Jelentkezzen be az Android-eszközén, és csatlakoztassa a számítógéphez USB-kábellel.

A **Build-beállítások** megnyitásához válassza a **fájl**-  >  **létrehozási beállítások**lehetőséget.

A **build (jelenetek**) területen győződjön meg arról, hogy az összes jelenet mellett Pipa jel van.

Győződjön meg arról, hogy az **exportálási projekt** nem rendelkezik pipa jellel. Válassza **a létrehozás és Futtatás**lehetőséget. A rendszer kérni fogja a fájl mentését `.apk` . Bármelyik nevet kiválaszthatja.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Üzembe helyezés iOS-eszközön

A **Build-beállítások** megnyitásához válassza a **fájl**-  >  **létrehozási beállítások**lehetőséget.

A **build (jelenetek**) területen győződjön meg arról, hogy az összes jelenet mellett Pipa jel van.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

A Xcode-ben állítsa le az alkalmazást a **Leállítás**lehetőség kiválasztásával.
