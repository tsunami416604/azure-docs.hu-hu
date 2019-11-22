---
title: 'Gyors útmutató: HoloLens-alkalmazás létrehozása Unity'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre HoloLens-alkalmazást az Unity használatával a térbeli Horgonyokkal.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 561b5391f125cf845eff940a0d3548fc6ade541e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277052"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Gyors útmutató: Azure térbeli horgonyokat használó Unity HoloLens-alkalmazás létrehozása

Ebben a rövid útmutatóban egy olyan Unity HoloLens-alkalmazást fog létrehozni, amely [Azure térbeli horgonyokat](../overview.md)használ. A térbeli horgonyok egy többplatformos fejlesztői szolgáltatás, amely lehetővé teszi vegyes valósági tapasztalatok létrehozását olyan objektumokkal, amelyek a helyükön maradnak az eszközökön az idő múlásával. Ha elkészült, egy olyan egységgel rendelkező HoloLens-alkalmazás fog rendelkezni, amely képes a térbeli horgonyok mentésére és visszahívására.

A következőket fogja megtanulni:

- Hozzon létre egy térbeli horgonyokat tartalmazó fiókot.
- Készítse elő az egység létrehozási beállításait.
- Konfigurálja a térbeli horgonyok fiókjának azonosítóját és a fiók kulcsát.
- Exportálja a HoloLens Visual Studio-projektet.
- Telepítse az alkalmazást, és futtassa egy HoloLens-eszközön.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

- Szükség van egy Windows rendszerű számítógépre, amelyre a 2019,1-es vagy újabb <a href="https://unity3d.com/get-unity/download" target="_blank">Unity</a> , valamint a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> -es vagy újabb verziója van telepítve. A Visual Studio-telepítésnek tartalmaznia kell a **univerzális Windows-platform fejlesztési** munkaterhelést és a **Windows 10 SDK (10.0.18362.0 vagy újabb)** összetevőt. <a href="https://git-scm.com/download/win" target="_blank">A git for Windowst</a>is telepítenie kell.
- Szüksége van egy olyan HoloLens-eszközre, amelyen engedélyezve van a [fejlesztői mód](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) . A [Windows 10 október 2018 frissítésének](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (más néven RS5) telepítve kell lennie az eszközön. A HoloLens legújabb kiadásának frissítéséhez nyissa meg a **Beállítások** alkalmazást, lépjen a **frissítés & biztonság**elemre, majd válassza a **frissítések keresése**lehetőséget.
- Az alkalmazásban engedélyeznie kell a **SpatialPerception** képességet. Ez a beállítás a > Player- **beállítások** > **közzétételi beállítások** > **képességek**című részében **található.**
- Az alkalmazásban engedélyeznie kell a **Windows Mixed Reality SDK**-val **támogatott virtuális valóságot** . Ez a beállítás a **létrehozási beállítások** > a **lejátszó beállításai** > **XR beállítások menüpontban**található.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Az Unity Sample projekt letöltése és megnyitása

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

A **Build-beállítások** megnyitásához válassza a **fájl** > **létrehozási beállítások**lehetőséget.

A **platform** szakaszban válassza a **univerzális Windows-platform**lehetőséget. Módosítsa a **céleszköz** **HoloLens**.

Válassza a **platform váltása** lehetőséget a platform **univerzális Windows-platformre**való módosításához. Az egység kérheti, hogy telepítse a UWP-támogatási összetevőket, ha hiányoznak.

![Unity-létrehozási beállítások ablak](./media/get-started-unity-hololens/unity-build-settings.png)

A **létrehozási beállítások** ablak bezárásához.

## <a name="configure-the-account-identifier-and-key"></a>A fiók azonosítójának és kulcsának konfigurálása

A **projekt** ablaktáblán lépjen a `Assets/AzureSpatialAnchors.Examples/Scenes` elemre, és nyissa meg a `AzureSpatialAnchorsBasicDemo.unity` jelenet fájlt.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenetet a **fájl** > **Mentés**lehetőség kiválasztásával.

## <a name="export-the-hololens-visual-studio-project"></a>A HoloLens Visual Studio-projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza a **Létrehozás**lehetőséget. A párbeszédpanelen válassza ki azt a mappát, amelybe exportálni szeretné a HoloLens Visual Studio-projektet.

Az Exportálás befejeztével megjelenik az exportált HoloLens projektet tartalmazó mappa.

## <a name="deploy-the-hololens-application"></a>A HoloLens alkalmazás üzembe helyezése

A mappában kattintson duplán a **HELLOAR U3D. SLN** elemre a projekt a Visual Studióban való megnyitásához.

Módosítsa a **megoldás konfigurációját** a **kiadásra**, módosítsa a **megoldás platformját** **x86**-ra, majd válassza az **eszköz** lehetőséget a telepítési cél beállításai közül.

Ha a 2. HoloLens használja, a **ARM64** -et a **megoldási platformként**használhatja az **x86**helyett.

   ![Visual Studio-konfiguráció](./media/get-started-unity-hololens/visual-studio-configuration.png)

Kapcsolja be a HoloLens eszközt, jelentkezzen be, és csatlakoztassa az eszközt a számítógéphez USB-kábellel.

Válassza **a hibakeresés > ** a **hibakeresés elindítása** az alkalmazás üzembe helyezéséhez és a hibakeresés megkezdéséhez lehetőséget.

A horgonyok elhelyezéséhez és felidézéséhez kövesse az alkalmazás utasításait.

A Visual Studióban állítsa le az alkalmazást a **hibakeresés leállítása** vagy a SHIFT + F5 billentyűkombináció kiválasztásával.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
