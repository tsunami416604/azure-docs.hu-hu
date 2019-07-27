---
title: Rövid útmutató – Android-alkalmazás létrehozása az Azure térbeli Horgonyokkal | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Android-alkalmazást térbeli horgonyok használatával.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: f7387b210c1499ff0a19fe17667090be0c2c0933
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561411"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Gyors útmutató: Android-alkalmazás létrehozása Azure térbeli Horgonyokkal

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Android-alkalmazást az [Azure térbeli](../overview.md) horgonyok C++használatával Java-vagy/NDK.-ben Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy ARCore Android-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * Üzembe helyezés és Futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Windows vagy macOS rendszerű számítógép, <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
  - Ha Windows rendszeren fut, szüksége lesz <a href="https://git-scm.com/download/win" target="_blank">a git for Windowsra</a>is.
  - Ha macOS rendszeren fut, a git a HomeBrew használatával telepíthető. Írja be a következő parancsot a terminál egyetlen sorába: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ezután futtassa a `brew install git`parancsot.
  - A NDK minta létrehozásához telepítenie kell a NDK és a CMak 3,6-es vagy újabb SDK-eszközöket is a Android Studio-ben.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztők számára engedélyezett</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore alkalmas</a> Android-eszköz.
- Az alkalmazásnak a **1,8**ARCore kell megcéloznia.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Töltse `arcore_c_api.h` le innen [, és helyezze](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.8.0/libraries/include/arcore_c_api.h) el `Android\NDK\libraries\include`a következő helyen:.

Az újonnan klónozott tárházból inicializálja az almodulokat a következő parancs futtatásával:

```console
git submodule update --init --recursive
```

---

Nyissa meg az Android Studiót.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Válassza a **meglévő Android Studio projekt megnyitása** lehetőséget, és válassza ki a `Android/Java/`projektet a következő helyen:.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Válassza a **meglévő Android Studio projekt megnyitása** lehetőséget, és válassza ki a `Android/NDK/`projektet a következő helyen:.

---

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Nyissa meg `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`a t.

Keresse meg `SpatialAnchorsAccountKey` a mezőt, `Set me` és cserélje le a fiókot a fiók kulcsára.

Keresse meg `SpatialAnchorsAccountId` a mezőt, `Set me` és cserélje le a azonosítót a fiókazonosító értékre.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Nyissa meg `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`a t.

Keresse meg `SpatialAnchorsAccountKey` a mezőt, `Set me` és cserélje le a fiókot a fiók kulcsára.

Keresse meg `SpatialAnchorsAccountId` a mezőt, `Set me` és cserélje le a azonosítót a fiókazonosító értékre.

---

## <a name="deploy-the-app-to-your-android-device"></a>Az alkalmazás üzembe helyezése Android-eszközön

Kapcsolja be az androidos eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez egy USB-kábellel.

Válassza a **Futtatás** lehetőséget a Android Studio eszköztáron.

![Android Studio üzembe helyezés és Futtatás](./media/get-started-android/android-studio-deploy-run.png)

Válassza ki az Android-eszközt a **telepítési cél kiválasztása** párbeszédpanelen, majd kattintson az **OK** gombra az alkalmazás Android-eszközön való futtatásához.

A horgonyok elhelyezéséhez és felidézéséhez kövesse az alkalmazás utasításait.

Állítsa le az alkalmazást a Android Studio eszköztárból válassza a **Leállítás** lehetőséget.

![Android Studio Stop](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
