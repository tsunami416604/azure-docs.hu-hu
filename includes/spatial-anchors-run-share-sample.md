---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904039"
---
## <a name="set-up-your-device-in-unity"></a>Az eszköz beállítása az egységben

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android-eszköz beállítása

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>IOS-eszköz beállítása

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>A fiók azonosítójának és kulcsának konfigurálása

A **projekt** ablaktáblán navigáljon `Assets/AzureSpatialAnchorsPlugin/Examples` , és nyissa `AzureSpatialAnchorsLocalSharedDemo.unity` meg a jelenet fájlt.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

A **projekt** ablaktáblán navigáljon `Assets\AzureSpatialAnchors.Examples\Resources`a következőhöz:. Válassza a(z) `SpatialAnchorSamplesConfig` lehetőséget. Ezután az **ellenőr** ablaktáblán adja meg a `Sharing Anchors Service url` (ASP.net Web App Azure-beli üzembe helyezése) értékét `Base Sharing Url`a következő helyett `index.html` `api/anchors`:. A következőhöz hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors`.

Mentse a jelenetet a **fájl** > **mentése**lehetőség kiválasztásával.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

### <a name="deploy-to-android-device"></a>Üzembe helyezés Android-eszközön

Jelentkezzen be az Android-eszközén, és csatlakoztassa a számítógéphez USB-kábellel.

A **Build-beállítások** megnyitásához válassza a **fájl** > -**létrehozási beállítások**lehetőséget.

A **build (jelenetek**) területen győződjön meg arról, hogy az összes jelenet mellett Pipa jel van.

Győződjön meg arról, hogy az **exportálási projekt** nem rendelkezik pipa jellel. Válassza **a létrehozás és Futtatás**lehetőséget. A rendszer kérni fogja a `.apk` fájl mentését. Bármelyik nevet kiválaszthatja.

Az alkalmazás elindítása után a **bemutató kiválasztása** párbeszédpanelen a bal vagy a jobb nyílra kattintva válassza ki a **LocalShare** lehetőséget, és koppintson a **Go!** elemre. Kövesse az alkalmazás utasításait. Válassza a **létrehozás & megosztási horgony** vagy a **megosztott horgony keresése**lehetőséget.

Az első forgatókönyv lehetővé teszi, hogy olyan horgonyt hozzon létre, amely később ugyanazon az eszközön vagy egy másikon is elhelyezhető.
A második forgatókönyv, ha már futtatta az alkalmazást, vagy ugyanazon az eszközön vagy egy másikon, lehetővé teszi a korábban megosztott horgonyok megkeresését. A forgatókönyv kiválasztását követően az alkalmazás további útmutatást nyújt a teendők megoldásához. A rendszer például arra kéri, hogy helyezze át az eszközt a környezeti információk összegyűjtéséhez. Később egy horgonyt helyezünk el a világba, várja meg, hogy mentse, és így tovább.

### <a name="deploy-to-an-ios-device"></a>Üzembe helyezés iOS-eszközön

A **Build-beállítások** megnyitásához válassza a **fájl** > -**létrehozási beállítások**lehetőséget.

A **build (jelenetek**) területen győződjön meg arról, hogy az összes jelenet mellett Pipa jel van.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Az alkalmazás elindítása után a **bemutató kiválasztása** párbeszédpanelen a bal vagy a jobb nyílra kattintva válassza ki a **LocalShare** lehetőséget, és koppintson a **Go!** elemre. Kövesse az alkalmazás utasításait. Válassza a **létrehozás & megosztási horgony** vagy a **megosztott horgony keresése**lehetőséget.

Az első forgatókönyv lehetővé teszi, hogy olyan horgonyt hozzon létre, amely később ugyanazon az eszközön vagy egy másikon is elhelyezhető.
A második forgatókönyv, ha már futtatta az alkalmazást, vagy ugyanazon az eszközön vagy egy másikon, lehetővé teszi a korábban megosztott horgonyok megkeresését. A forgatókönyv kiválasztását követően az alkalmazás további útmutatást nyújt a teendők megoldásához. A rendszer például arra kéri, hogy helyezze át az eszközt a környezeti információk összegyűjtéséhez. Később egy horgonyt helyezünk el a világba, várja meg, hogy mentse, és így tovább.

A Xcode-ben állítsa le az alkalmazást a **Leállítás**lehetőség kiválasztásával.
