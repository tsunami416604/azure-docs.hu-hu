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
ms.openlocfilehash: c5647aa20f444d5efd36f03d813ee87ef199cc41
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621872"
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

- A gép egy Windows vagy MacOS rendszerű <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> és <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3-as</a>.
  - Ha fut a Windows, is kell <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>.
  - Ha macOS rendszeren fut, első keresztül a homebrew-val telepített Git. Adja meg a következő parancs a terminál egyetlen sorba: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ezután futtassa `brew install git`.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">engedélyezve fejlesztői</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">képes ARCore</a> Android-eszközön.
- Az alkalmazás verziót kell használnia **1.5-ös** ARCore SDK for Unity (az 1.6-os + ARCore elérhető lesz egy későbbi időpontban).

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

Győződjön meg, hogy a **exportálása projekt** jelölőnégyzet nem rendelkezik a jelölőnégyzet be van jelölve. Kattintson a **létrehozásához és futtatásához**. Meg kell adnia menteni a `.apk` fájlt bármilyen nevet, kiválaszthatja.

Kövesse az utasításokat az alkalmazásban kívánja helyezni horgonyra visszaírásához.

> [!NOTE]
> Az alkalmazás futtatásakor, ha nem látja a kamerát a háttérben (a példányt egy üres, kék helyette tekintse meg vagy más textúrákhoz), majd valószínűleg újra importálnia kell a Unity-nél eszközök. Állítsa le az alkalmazást. Válassza a felső menüben, a Unity-nél, **eszközök -> minden újraimportálása**. Ezután futtassa újra az alkalmazást.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Megosztás térbeli horgonyok eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
