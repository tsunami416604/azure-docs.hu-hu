---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631138"
---
## <a name="set-up-your-device"></a>Az eszköz beállítása

Nyissa meg a projektre a Unity, az `Unity` mappát.

![Unity-ablak](./media/spatial-anchors-unity/unity-window.png)

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

Alatt **jelenetek épít**, mellett jelölje be a `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` jelenet, és törölje a jelölőnégyzet az összes többi jelenetek jelöli meg.

Győződjön meg arról, hogy **exportálása projekt** nem be van jelölve. Válassza ki **létrehozásához és futtatásához**. Kérni fogja menteni a `.apk` fájlt. Bármilyen nevet, kiválaszthatja.

Kövesse az utasításokat az alkalmazásban. Választhat **Létrehozás & megosztás Forráshorgony** vagy **keresse meg a megosztott Forráshorgony**. Az első forgatókönyv, amely ugyanazon az eszközön vagy egy másik később található horgonyra létrehozását teszi lehetővé. A második forgatókönyv szerint, ha már futtatott az alkalmazást, vagy ugyanazon az eszközön, vagy egy másik, a lehetővé teszi, hogy keresse meg a korábban megosztott horgonyok. Miután a forgatókönyvet, az alkalmazás végigvezeti az tájékoztatjuk a további köré, hogy mi a teendő. Például akkor felszólítja az eszköz Navigálás környezeti információkat gyűjthet. Később a, fog horgonyra helyezze a világon, fog, várjon, amíg feltölti, és így tovább.

### <a name="deploy-to-an-ios-device"></a>IOS-eszközön való üzembe helyezése

Nyissa meg **Build Settings** kiválasztásával **fájl** > **Build Settings**.

Alatt **jelenetek épít**, mellett jelölje be a `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` jelenet, és törölje a jelölőnégyzet az összes többi jelenetek jelöli meg.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Kövesse az utasításokat az alkalmazásban. Választhat **Létrehozás & megosztás Forráshorgony** vagy **keresse meg a megosztott Forráshorgony**. Az első forgatókönyv, amely ugyanazon az eszközön vagy egy másik később található horgonyra létrehozását teszi lehetővé. A második forgatókönyv szerint, ha már futtatott az alkalmazást, vagy ugyanazon az eszközön, vagy egy másik, a lehetővé teszi, hogy keresse meg a korábban megosztott horgonyok. Miután a forgatókönyvet, az alkalmazás végigvezeti az tájékoztatjuk a további köré, hogy mi a teendő. Például akkor felszólítja az eszköz Navigálás környezeti információkat gyűjthet. Később a, fog horgonyra helyezze a világon, fog, várjon, amíg feltölti, és így tovább.

Az xcode-ban, állítsa le az alkalmazást a kiválasztásával **leállítása**.
