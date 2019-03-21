---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305117"
---
## <a name="set-up-your-device"></a>Az eszköz beállítása

### <a name="set-up-an-android-device"></a>Egy Android-eszköz beállítása

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Állítsa be a IOS rendszerű eszközzel

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>A fiókazonosító és kulcsának konfigurálása

Az a **projekt** ablaktáblán keresse meg `Assets/AzureSpatialAnchorsPlugin/Examples` , és nyissa meg a `AzureSpatialAnchorsLocalSharedDemo.unity` jelenetfájl.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Az a **vizsgáló** panelen adja meg a `Sharing Anchors Service url` (az ASP.NET webalkalmazását az Azure-telepítés) értékeként `Base Sharing Url`, cseréjét `index.html` a `api/anchors`. Ez hasonlóan kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors`.

Mentse a jelenet kiválasztásával **fájl** > **mentése**.

## <a name="to-deploy-the-app-to-an-android-device"></a>Telepítse az alkalmazást az Android-eszközön

Jelentkezzen be az Android-eszközön, és csatlakozzon a számítógéphez egy USB-kábel használatával.

Nyissa meg **Build Settings** kiválasztásával **fájl** > **Build Settings**.

Alatt **jelenetek épít**, mellett jelölje be a `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` jelenet, és törölje a jelölőnégyzet az összes többi jelenetek jelöli meg.

Győződjön meg arról, hogy **exportálása projekt** nem be van jelölve. Válassza ki **létrehozásához és futtatásához**. Kérni fogja menteni a `.apk` fájlt. Bármilyen nevet, kiválaszthatja.

Kövesse az utasításokat az alkalmazásban. Választhat **Létrehozás & megosztás Forráshorgony** vagy **keresse meg a megosztott Forráshorgony**. Az első lehetőség, hogy később ugyanazon az eszközön vagy egy másik található horgonyra létrehozását teszi lehetővé. A második lehetőség, ha korábban már futtatja az alkalmazást, vagy ugyanazon az eszközön, vagy egy másik, a lehetővé teszi, hogy keresse meg a korábban megosztott horgonyok.

## <a name="to-deploy-the-app-to-an-ios-device"></a>Az alkalmazás telepítése iOS-eszközök számára

Nyissa meg **Build Settings** kiválasztásával **fájl** > **Build Settings**.

Alatt **jelenetek épít**, mellett jelölje be a `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` jelenet, és törölje a jelölőnégyzet az összes többi jelenetek jelöli meg.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Kövesse az utasításokat az alkalmazásban. Választhat **Létrehozás & megosztás Forráshorgony** vagy **keresse meg a megosztott Forráshorgony**. Az első lehetőség, hogy később ugyanazon az eszközön vagy egy másik található horgonyra létrehozását teszi lehetővé. A második lehetőség, ha korábban már futtatja az alkalmazást, vagy ugyanazon az eszközön, vagy egy másik, a lehetővé teszi, hogy keresse meg a korábban megosztott horgonyok.

Az xcode-ban, állítsa le az alkalmazást a kiválasztásával **leállítása**.
