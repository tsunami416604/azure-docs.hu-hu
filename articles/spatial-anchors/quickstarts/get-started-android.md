---
title: 'Rövid útmutató: Android-alkalmazás létrehozása'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre androidos alkalmazást térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0501c8bb1d71c6cff6033fc937cda019c8890056
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75376460"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Rövid útmutató: Android-alkalmazás létrehozása az Azure Spatial Anchors alkalmazással

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy [Android-alkalmazást](../overview.md) az Azure Spatial Anchors használatával Java vagy C++/NDK-ban. Az Azure Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóság élményeket hozzon létre olyan objektumok használatával, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, lesz egy ARCore Android-alkalmazása, amely képes menteni és visszahívni egy térbeli horgonyt.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * A Térbeli horgonyok fiókazonosítójának és a fiókkulcsának konfigurálása
> * Telepítés és futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Windows vagy macOS rendszerű készülék <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+ rendszerrel.</a>
  - Ha Windows rendszeren fut, akkor a <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> és a <a href="https://git-lfs.github.com/">Git LFS rendszerre</a>is szüksége lesz.
  - Ha macOS rendszeren fut, a Get Git telepítve van a HomeBrew-n keresztül. Írja be a következő parancsot a `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`terminál egyetlen sorába: . Ezután `brew install git` fuss `brew install git-lfs`és .
  - Az NDK-minta létrehozásához telepítenie kell az NDK és a CMake 3.6 vagy nagyobb SDK-eszközöket az Android Studio-ban.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztő engedélyezve és</a> <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore képes</a> Android készülék.
  - További eszközillesztőkre lehet szükség ahhoz, hogy a számítógép kommunikáljon androidos eszközével. További információkat és utasításokat [itt](https://developer.android.com/studio/run/device.html) talál.
- Az alkalmazásnak az ARCore **1.11.0-t**kell megcéloznia.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>A mintaprojekt megnyitása

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK között](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Töltse `arcore_c_api.h` le [innen,](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) `Android\NDK\libraries\include`és tegyük a .

Az újonnan klónozott tárházból inicializálhatja az almodulokat a következő parancs futtatásával:

```console
git submodule update --init --recursive
```

---

Nyissa meg az Android Studiót.

# <a name="java"></a>[Java](#tab/openproject-java)

Válassza **a Meglévő Android Studio-projekt** megnyitása `Android/Java/`lehetőséget, és válassza ki a projektet a helyen.

# <a name="ndk"></a>[NDK között](#tab/openproject-ndk)

Válassza **a Meglévő Android Studio-projekt** megnyitása `Android/NDK/`lehetőséget, és válassza ki a projektet a helyen.

---

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcs konfigurálása

A következő lépés az, hogy konfigurálja az alkalmazást, hogy használja a fiókazonosító és a fiókkulcs. A [Térbeli horgonyok erőforrás beállításakor](#create-a-spatial-anchors-resource)szövegszerkesztőbe másolta őket.

# <a name="java"></a>[Java](#tab/openproject-java)

Nyissa meg a következő fájlt: `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Keresse `SpatialAnchorsAccountKey` meg a `Set me` mezőt, és cserélje le a számlakulcsot.

Keresse `SpatialAnchorsAccountId` meg a `Set me` mezőt, és cserélje le a számlaazonosítóra.

# <a name="ndk"></a>[NDK között](#tab/openproject-ndk)

Nyissa meg a következő fájlt: `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Keresse `SpatialAnchorsAccountKey` meg a `Set me` mezőt, és cserélje le a számlakulcsot.

Keresse `SpatialAnchorsAccountId` meg a `Set me` mezőt, és cserélje le a számlaazonosítóra.

---

## <a name="deploy-the-app-to-your-android-device"></a>Az alkalmazás telepítése Android-eszközre

Kapcsolja be az Android-eszközt, jelentkezzen be, és csatlakoztassa a számítógéphez USB-kábellel.

Válassza a **Futtatás** lehetőséget az Android Studio eszköztárán.

![Az Android Studio telepítése és futtatása](./media/get-started-android/android-studio-deploy-run.png)

Válassza ki az Android-eszközt a **Telepítési cél kiválasztása** párbeszédpanelen, és az **OK** gombra az alkalmazás Android-eszközön való futtatásához.

Kövesse az utasításokat az alkalmazásban, hogy helyezzen el és visszahívása horgonyt.

Állítsa le az alkalmazást az Android Studio eszköztár **Leállítás** gombjának kiválasztásával.

![Android Stúdió leállítása](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
