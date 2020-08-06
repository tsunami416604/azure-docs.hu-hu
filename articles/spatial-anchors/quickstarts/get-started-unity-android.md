---
title: 'Gyors útmutató: Unity Android-alkalmazás létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre a térbeli Horgonyokkal rendelkező Android-alkalmazást az Unity használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 608b488a04eafd01e6702850445801705f6dac81
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810353"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Gyors útmutató: Unity Android-alkalmazás létrehozása az Azure térbeli Horgonyokkal

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Unity Android-alkalmazást az [Azure térbeli horgonyok](../overview.md)használatával. Az Azure térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságot hozzon létre olyan objektumok használatával, amelyek az adott helyen maradnak a helyükön az egyes eszközökön. Ha elkészült, egy olyan egységgel rendelkező ARCore Android-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * Az egység létrehozási beállításainak előkészítése
> * A térbeli horgonyok fiókazonosító és a fiók kulcsának konfigurálása
> * A Android Studio projekt exportálása
> * Üzembe helyezés és Futtatás Android-eszközön

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy olyan Windows vagy macOS rendszerű gép, amelynek <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,4 (LTS)</a> , beleértve az Android Build support és az android SDK & NDK Tools modulokat.
  - Ha Windows rendszeren fut, <a href="https://git-scm.com/download/win" target="_blank">a git for Windows</a> és a <a href="https://git-lfs.github.com/">git LFS</a>is szüksége lesz.
  - Ha macOS rendszeren fut, a git a HomeBrew használatával telepíthető. Írja be a következő parancsot a terminál egyetlen sorába: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Ezután futtassa `brew install git` a és a parancsot `brew install git-lfs` .
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">fejlesztők számára engedélyezett</a> és <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore alkalmas</a> Android-eszköz.
  - Előfordulhat, hogy a számítógépe számára további eszközillesztők szükségesek az Android-eszközkel való kommunikációhoz. További információkért és útmutatásért lásd [itt](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Az Unity Sample projekt letöltése és megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>A Android Studio projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza ki az eszközt az **eszköz futtatása** területen, majd válassza a **Létrehozás és Futtatás**lehetőséget. A rendszer megkéri, hogy mentsen egy `.apk` fájlt, amelyből bármilyen nevet kiválaszthat.

A horgonyok elhelyezéséhez és felidézéséhez kövesse az alkalmazás utasításait.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="rendering-issues"></a>Renderelési problémák

Ha az alkalmazás futtatásakor nem látja a kamerát háttérként (például üres, kék vagy más textúra jelenik meg), akkor valószínűleg újra kell importálnia az egységben lévő eszközöket. Állítsa le az alkalmazást. Az egység felső menüjében válassza az **eszközök – > az összes újraimportálása**elemet. Ezután futtassa újra az alkalmazást.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Útmutató: Azure térbeli horgonyok konfigurálása Unity-projektben](../how-tos/setup-unity-project.md)
