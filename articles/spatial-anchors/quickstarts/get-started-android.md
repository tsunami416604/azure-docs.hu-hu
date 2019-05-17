---
title: Rövid útmutató – Android-alkalmazás létrehozása az Azure térbeli horgonyok |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Android-alkalmazásokból térbeli horgonyok.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 03589745e6e9b40b937c49162e99035ce6c81423
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800051"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Gyors útmutató: Android-alkalmazás létrehozása az Azure térbeli horgonyok

Ez a rövid útmutató bemutatja, hogyan hozzon létre egy Android-alkalmazás a [Azure térbeli horgonyok](../overview.md) vagy Java nyelven vagy C++/NDK. Az Azure térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy a vegyes valóság élmény helyükre kivonatuk eszközök idővel-objektumok segítségével. Ha elkészült, egy ARCore Android-alkalmazást, amely mentheti, és egy térbeli kapcsolati alapot visszahívása lesz.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiók azonosítóját és a fiókkulcsot konfigurálása
> * Üzembe helyezése és futtatása az Android-eszközökön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- A gép egy Windows vagy MacOS rendszerű <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3-as</a>.
  - Ha fut a Windows, is kell <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>.
  - Ha macOS rendszeren fut, első keresztül a homebrew-val telepített Git. Adja meg a következő parancs a terminál egyetlen sorba: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ezután futtassa `brew install git`.
  - A Szünettel minta létrehozása, is kell az Android Studióban a Szünettel és a CMake 3.6-os SDK Tools telepítése.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">engedélyezve fejlesztői</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">képes ARCore</a> Android-eszközön.
- Az alkalmazás kell céloznia ARCore 1.7-es.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Nyissa meg a mintaprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Az Android Szünettel minta létrehozásakor, kell letölteni `arcore_c_api.h` a [Itt](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.7.0/libraries/include/arcore_c_api.h) és elhelyezheti `Android\NDK\libraries\include`.

Nyissa meg az Android Studiót.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Válassza ki **Android Studio projekt megnyitása** , és válassza ki a projekt helyén található `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Válassza ki **Android Studio projekt megnyitása** , és válassza ki a projekt helyén található `Android/NDK/`.

***

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcsának konfigurálása

A következő lépés, hogy a fiók azonosítóját és kulcsát az alkalmazás konfigurálása. Másolja őket egy szövegszerkesztőbe, amikor [beállítása a térbeli horgonyok erőforrás](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Nyissa meg `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le `Set me` és a fiókkulcsot.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le `Set me` fiók azonosítóval.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Nyissa meg `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le `Set me` és a fiókkulcsot.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le `Set me` fiók azonosítóval.

***

## <a name="deploy-the-app-to-your-android-device"></a>Az alkalmazás az Android-eszköz üzembe helyezése

Az Android-eszközön Power, jelentkezzen be, és csatlakoztassa a Számítógépet, USB-kábel segítségével.

Válassza ki **futtatása** az Android Studio eszköztáron.

![Az Android Studio üzembe helyezése és futtatása](./media/get-started-android/android-studio-deploy-run.png)

Válassza ki az Androidos eszközt a **válassza ki a központi telepítési cél** párbeszédpanel, és válassza ki **OK** futtathatják az alkalmazást az Android-eszközön.

Kövesse az utasításokat az alkalmazásban kívánja helyezni horgonyra visszaírásához.

Állítsa le az alkalmazást kiválasztásával **leállítása** az Android Studio eszköztáron.

![Android Studio Stop](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Megosztás térbeli horgonyok eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
