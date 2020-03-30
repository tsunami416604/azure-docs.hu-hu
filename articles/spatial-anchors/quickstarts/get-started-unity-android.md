---
title: 'Rövid útmutató: Unity Android-alkalmazás létrehozása'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre androidos alkalmazást unity használatával térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7acff7f0249cdedcebd367fc315be92cafb9ab78
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77615439"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Rövid útmutató: Unity Android-alkalmazás létrehozása az Azure Spatial Anchors alkalmazással

Ez a rövid útmutató ismerteti, hogyan hozhat létre Unity Android-alkalmazást az [Azure Spatial Anchors](../overview.md)használatával. Az Azure Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóság élményeket hozzon létre olyan objektumok használatával, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, egy Unity-vel készült ARCore Android alkalmazással rendelkezik, amely képes megmenteni és felidézni egy térbeli horgonyt.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * Unity-összeállítási beállítások előkészítése
> * A Térbeli horgonyok fiókazonosítójának és a fiókkulcsának konfigurálása
> * Az Android Studio projekt exportálása
> * Telepítés és futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Windows vagy macOS gép <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 vagy 2019.2,</a> beleértve az Android Build support-ot és az Android SDK & NDK Tools modulokat.
  - Ha Windows rendszeren fut, akkor a <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> és a <a href="https://git-lfs.github.com/">Git LFS rendszerre</a>is szüksége lesz.
  - Ha macOS rendszeren fut, a Get Git telepítve van a HomeBrew-n keresztül. Írja be a következő parancsot a `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`terminál egyetlen sorába: . Ezután `brew install git` fuss `brew install git-lfs`és .
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztő engedélyezve és</a> <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore képes</a> Android készülék.
  - További eszközillesztőkre lehet szükség ahhoz, hogy a számítógép kommunikáljon androidos eszközével. További információkat és utasításokat [itt](https://developer.android.com/studio/run/device.html) talál.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Töltse le és nyissa meg a Unity mintaprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcs konfigurálása

A **Project** ablaktáblán `Assets/AzureSpatialAnchors.Examples/Scenes` keresse `AzureSpatialAnchorsBasicDemo.unity` meg és nyissa meg a jelenetfájlt.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenetet a **Fájlmentés** -> lehetőség**kiválasztásával.**

## <a name="export-the-android-studio-project"></a>Az Android Studio projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza ki az eszközt az **Eszköz futtatása kor,** és kattintson **a Build and Run gombra.** A rendszer kéri, hogy `.apk` mentsen el egy fájlt, amelyhez bármilyen nevet választhat.

Kövesse az utasításokat az alkalmazásban, hogy helyezzen el és visszahívása horgonyt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="rendering-issues"></a>Renderelési problémák

Az alkalmazás futtatásakor, ha nem látja a kamerát háttérként (például üres, kék vagy más textúrákat lát), akkor valószínűleg újra kell importálnia az eszközöket a Unity-ben. Állítsa le az alkalmazást. A Unity felső menüjében válassza az **Eszközök -> az összes újraimportálása**lehetőséget. Ezután futtassa újra az alkalmazást.

### <a name="unity-20193"></a>Egység 2019.3

A változások megszakadása miatt a Unity 2019.3 jelenleg nem támogatott. Kérjük, használja a Unity 2019.1 vagy 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
