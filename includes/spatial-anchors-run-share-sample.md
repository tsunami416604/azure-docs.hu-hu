---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: b46a2b18309851bbe2934980137a53d2de6f6efc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135354"
---
## <a name="set-up-your-device-in-unity"></a>Konfigurálja az eszközt a Unity-nél

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Egy Android-eszköz beállítása

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Állítsa be a IOS rendszerű eszközzel

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>A fiókazonosító és kulcsának konfigurálása

Az a **projekt** ablaktáblán keresse meg `Assets/AzureSpatialAnchorsPlugin/Examples` , és nyissa meg a `AzureSpatialAnchorsLocalSharedDemo.unity` jelenetfájl.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Az a **vizsgáló** panelen adja meg a `Sharing Anchors Service url` (az ASP.NET webalkalmazását az Azure-telepítés) értékeként `Base Sharing Url`, cseréjét `index.html` a `api/anchors`. Ez hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors`.

Mentse a jelenet kiválasztásával **fájl** > **mentése**.

## <a name="deploy-to-your-device"></a>Üzembe helyezés az eszközön

### <a name="deploy-to-android-device"></a>Android-eszköz üzembe helyezése

Jelentkezzen be az Android-eszközön, és csatlakozzon a számítógéphez egy USB-kábel használatával.

Nyissa meg **Build Settings** kiválasztásával **fájl** > **Build Settings**.

A **jelenetek épít**, győződjön meg arról, minden a színfalak be van jelölve mellettük.

Győződjön meg arról, hogy **exportálása projekt** nem be van jelölve. Válassza ki **létrehozásához és futtatásához**. Kérni fogja menteni a `.apk` fájlt. Bármilyen nevet, kiválaszthatja.

Miután az alkalmazás elindul, a a **válassza ki A Demo** párbeszédpanelen, a jobb vagy bal nyíl segítségével válassza ki a a **LocalShare** lehetőséget, majd koppintson a **nyissa meg!** . Kövesse az utasításokat az alkalmazásban. Választhat **Létrehozás & megosztás Forráshorgony** vagy **keresse meg a megosztott Forráshorgony**.

Az első forgatókönyv, amely ugyanazon az eszközön vagy egy másik később található horgonyra létrehozását teszi lehetővé.
A második forgatókönyv szerint, ha már futtatott az alkalmazást, vagy ugyanazon az eszközön, vagy egy másik, a lehetővé teszi, hogy keresse meg a korábban megosztott horgonyok. Miután a forgatókönyvet, az alkalmazás végigvezeti az körül, mi a teendő további utasításokat. Ha például rendszer, az eszköz Navigálás környezeti információk gyűjtésére. Később a, fog horgonyra helyezze a világon, várja meg, hogy mentse, és így tovább.

### <a name="deploy-to-an-ios-device"></a>IOS-eszközön való üzembe helyezése

Nyissa meg **Build Settings** kiválasztásával **fájl** > **Build Settings**.

A **jelenetek épít**, győződjön meg arról, minden a színfalak be van jelölve mellettük.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Miután az alkalmazás elindul, a a **válassza ki A Demo** párbeszédpanelen, a jobb vagy bal nyíl segítségével válassza ki a a **LocalShare** lehetőséget, majd koppintson a **nyissa meg!** . Kövesse az utasításokat az alkalmazásban. Választhat **Létrehozás & megosztás Forráshorgony** vagy **keresse meg a megosztott Forráshorgony**.

Az első forgatókönyv, amely ugyanazon az eszközön vagy egy másik később található horgonyra létrehozását teszi lehetővé.
A második forgatókönyv szerint, ha már futtatott az alkalmazást, vagy ugyanazon az eszközön, vagy egy másik, a lehetővé teszi, hogy keresse meg a korábban megosztott horgonyok. Miután a forgatókönyvet, az alkalmazás végigvezeti az körül, mi a teendő további utasításokat. Ha például rendszer, az eszköz Navigálás környezeti információk gyűjtésére. Később a, fog horgonyra helyezze a világon, várja meg, hogy mentse, és így tovább.

Az xcode-ban, állítsa le az alkalmazást a kiválasztásával **leállítása**.
