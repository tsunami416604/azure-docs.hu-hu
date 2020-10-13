---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: 50e4799f09322eab05b4f8ddf7004c2e0078fdab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971383"
---
## <a name="android"></a>[Android](#tab/Android)

A Java Android-minta támogatja az eszközök közötti megosztást.

A Android Studioban nyissa meg a *SharedActivity. Java* fájlt a Samples mappából. 

Adja meg azt az URL-címet, amelyet az előző lépésben másolt (a ASP.NET Web App Azure-beli telepítésből) a `SharingAnchorsServiceUrl` *SharedActivity. Java* fájl értékeként. 

Cserélje le az `index.html` URL-címet a alkalmazásban `api/anchors` . A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

A Objective-C iOS-minta támogatja az eszközök közötti megosztást.

Nyissa meg a *SharedDemoViewController. m* fájlt a Samples mappában. 

Adja meg az előző lépésben beszerzett URL-címet (a ASP.NET Web App Azure-telepítésből) a `SharingAnchorsServiceUrl` *SharedDemoViewController. m* fájlban megadott értékként. 

Cserélje le az `index.html` URL-címet a alkalmazásban `api/anchors` . A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors` .

Telepítse az alkalmazást az eszközre. 

Az alkalmazás elindítása után jelölje be a **koppintson a megosztott bemutató elindítására** lehetőségre, majd kövesse az alkalmazás utasításait. Kiválaszthatja **, hogy a rögzítési szám alapján megkeresi** a horgonyt, vagy **koppintson a horgony létrehozására, és mentse a szolgáltatásba**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

A Xamarin Android-és iOS-minták egyaránt támogatják az eszközök közötti megosztást.

Nyissa meg a *AccountDetails.cs* fájlt a Samples mappában. 

Adja meg az előző lépésben beszerzett URL-címet (a ASP.NET Web App Azure-telepítésből) a `AnchorSharingServiceUrl` *AccountDetails.cs* -fájl értékeként. 

Cserélje le az `index.html` URL-címet a alkalmazásban `api/anchors` . A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android-eszköz beállítása

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>IOS-eszköz beállítása

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

A **projekt** ablaktáblán válassza a következőt: `Assets\AzureSpatialAnchors.Examples\Resources` . 

Válassza a **SpatialAnchorSamplesConfig**lehetőséget. Ezután a **felügyelő** ablaktáblán adja meg az `Sharing Anchors Service` URL-címet (a ASP.net Web App Azure-beli üzembe helyezése) a értékeként `Base Sharing Url` . Cserélje le a `index.html` elemet a `api/anchors` kérdésre. A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors` .

Mentse a jelenetet a **fájl**mentése lehetőség kiválasztásával  >  **Save**.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

### <a name="deploy-to-an-android-device"></a>Üzembe helyezés Android-eszközön

Jelentkezzen be az Android-eszközére, és csatlakoztassa a számítógéphez USB-kábellel.

A **Build-beállítások** megnyitásához válassza a **fájl**-  >  **létrehozási beállítások**lehetőséget.

A **létrehozási jelenetek**területen győződjön meg arról, hogy minden egyes jelenet mellett Pipa jel van.

Győződjön meg arról, hogy az **exportálási projektnek** nincs pipa. Válassza **a létrehozás és Futtatás**lehetőséget. A rendszer kérni fogja az *. apk* fájl mentését. Bármelyik nevet kiválaszthatja.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Üzembe helyezés iOS-eszközön

A **Build-beállítások** megnyitásához válassza a **fájl**-  >  **létrehozási beállítások**lehetőséget.

A **létrehozási jelenetek**területen győződjön meg arról, hogy minden egyes jelenet mellett Pipa jel van.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

A Xcode-ben állítsa le az alkalmazást a **Leállítás**lehetőség kiválasztásával.
