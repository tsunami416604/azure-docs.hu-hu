---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752154"
---
## <a name="open-the-sample-project-in-unity"></a>Nyissa meg a mintaprojektet a Unity-nél

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Egy Android-eszköz beállítása

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Egy IOS-eszköz beállítása

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcsának konfigurálása

Az a **projekt** ablaktáblán keresse meg `Assets/AzureSpatialAnchorsPlugin/Examples` , és nyissa meg a `AzureSpatialAnchorsLocalSharedDemo.unity` jelenetfájl.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Emellett a **vizsgáló** panelen adja meg a `Sharing Anchors Service url` (az ASP.NET webalkalmazását az Azure-telepítés) értékeként `Base Sharing Url`, cseréjét `index.html` a `api/anchors`. Így kell kinéznie: `https://<app_name>.azurewebsites.net/api/anchors`.

Mentse a jelenet kiválasztásával **fájl** -> **mentése**.

## <a name="to-deploy-to-an-android-device"></a>Android-eszközön való üzembe helyezéséhez

Az Android-eszközön Power, jelentkezzen be, és csatlakoztassa a Számítógépet, USB-kábel segítségével.

Nyissa meg **Build Settings** kiválasztásával **fájl** -> **Build Settings**.

A **jelenetek épít**, mellett jelölje be a `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` jelenet, és törölje az összes többi jelenetek jelek ellenőrzése.

Győződjön meg, hogy a **exportálása projekt** jelölőnégyzet nem rendelkezik a jelölőnégyzet be van jelölve. Kattintson a **létrehozásához és futtatásához**. Meg kell adnia menteni a `.apk` fájlt bármilyen nevet, kiválaszthatja.

Kövesse az utasításokat az alkalmazásban. Dönthet úgy **Létrehozás & megosztás Forráshorgony**, vagy **keresse meg a megosztott Forráshorgony**. Az első lehetőség lehetővé teszi, hogy ugyanazon az eszközön és a egy másik hozhatók létre, amelyek később található horgonyra. A második lehetőség, ha korábban már futtatta az alkalmazás ugyanazon az eszközön vagy egy másik, lehetővé teszi, hogy keresse meg a korábban megosztott horgonyok.

## <a name="to-deploy-to-an-ios-device"></a>IOS-eszközön való üzembe helyezéséhez

Nyissa meg **Build Settings** kiválasztásával **fájl** -> **Build Settings**.

A **jelenetek épít**, mellett jelölje be a `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` jelenet, és törölje az összes többi jelenetek jelek ellenőrzése.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Kövesse az utasításokat az alkalmazásban. Dönthet úgy **Létrehozás & megosztás Forráshorgony**, vagy **keresse meg a megosztott Forráshorgony**. Az első lehetőség lehetővé teszi, hogy ugyanazon az eszközön és a egy másik hozhatók létre, amelyek később található horgonyra. A második lehetőség, ha korábban már futtatta az alkalmazás ugyanazon az eszközön vagy egy másik, lehetővé teszi, hogy keresse meg a korábban megosztott horgonyok.

Az xcode-ban, állítsa le az alkalmazást a billentyű lenyomásával **leállítása**.
