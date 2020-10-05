---
title: 'Gyors útmutató: Android-alkalmazás létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Android-alkalmazást térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6b7f924c7f115e8eddda93ea0c096ab94411da72
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87810394"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Gyors útmutató: Android-alkalmazás létrehozása az Azure térbeli Horgonyokkal

Ez a rövid útmutató bemutatja, hogyan hozhat létre Android-alkalmazást az [Azure térbeli horgonyok](../overview.md) használatával Java vagy C++/NDK. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy ARCore Android-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * Üzembe helyezés és Futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Windows vagy macOS rendszerű számítógép, <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
  - Ha Windows rendszeren fut, <a href="https://git-scm.com/download/win" target="_blank">a git for Windows</a> és a <a href="https://git-lfs.github.com/">git LFS</a>is szüksége lesz.
  - Ha macOS rendszeren fut, a git a HomeBrew használatával telepíthető. Írja be a következő parancsot a terminál egyetlen sorába: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Ezután futtassa `brew install git` a és a parancsot `brew install git-lfs` .
  - A NDK minta létrehozásához telepítenie kell a NDK és a CMak 3,6-es vagy újabb SDK-eszközöket is a Android Studio-ben.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztők számára engedélyezett</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore alkalmas</a> Android-eszköz.
  - Előfordulhat, hogy a számítógépe számára további eszközillesztők szükségesek az Android-eszközkel való kommunikációhoz. További információért és útmutatásért lásd [itt](https://developer.android.com/studio/run/device.html) .
- Az alkalmazásnak meg kell céloznia a ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A minta projekt megnyitása

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Töltse `arcore_c_api.h` le [innen](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) , és helyezze el a következő helyen: `Android\NDK\libraries\include` .

Az újonnan klónozott tárházból inicializálja az almodulokat a következő parancs futtatásával:

```console
git submodule update --init --recursive
```

---

Nyissa meg az Android Studiót.

# <a name="java"></a>[Java](#tab/openproject-java)

Válassza a **meglévő Android Studio projekt megnyitása** lehetőséget, és válassza ki a projektet a következő helyen: `Android/Java/` .

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Válassza a **meglévő Android Studio projekt megnyitása** lehetőséget, és válassza ki a projektet a következő helyen: `Android/NDK/` .

---

## <a name="configure-account-identifier-and-key"></a>Fiók azonosítójának és kulcsának konfigurálása

A következő lépés az alkalmazás konfigurálása a fiók azonosítójának és a fiók kulcsának használatára. [A térbeli horgonyok erőforrásának beállításakor](#create-a-spatial-anchors-resource)egy szövegszerkesztőbe másolta őket.

# <a name="java"></a>[Java](#tab/openproject-java)

Nyissa meg a következő fájlt: `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le a `Set me` fiókot a fiók kulcsára.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le a azonosítót `Set me` a fiókazonosító értékre.

Keresse meg a `SpatialAnchorsAccountDomain` mezőt, és cserélje le a `Set me` fiókot a fiók tartományára.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Nyissa meg a következő fájlt: `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Keresse meg a `SpatialAnchorsAccountKey` mezőt, és cserélje le a `Set me` fiókot a fiók kulcsára.

Keresse meg a `SpatialAnchorsAccountId` mezőt, és cserélje le a azonosítót `Set me` a fiókazonosító értékre.

Keresse meg a `SpatialAnchorsAccountDomain` mezőt, és cserélje le a `Set me` fiókot a fiók tartományára.

---

## <a name="deploy-the-app-to-your-android-device"></a>Az alkalmazás üzembe helyezése Android-eszközön

Kapcsolja be az androidos eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez egy USB-kábellel.

Válassza a **Futtatás** lehetőséget a Android Studio eszköztáron.

![Android Studio üzembe helyezés és Futtatás](./media/get-started-android/android-studio-deploy-run.png)

Válassza ki az Android-eszközt a **telepítési cél kiválasztása** párbeszédpanelen, majd kattintson az **OK** gombra az alkalmazás Android-eszközön való futtatásához.

A horgonyok elhelyezéséhez és felidézéséhez kövesse az alkalmazás utasításait.

Állítsa le az alkalmazást a Android Studio eszköztárból válassza a **Leállítás** lehetőséget.

![Android Studio leállítása](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
