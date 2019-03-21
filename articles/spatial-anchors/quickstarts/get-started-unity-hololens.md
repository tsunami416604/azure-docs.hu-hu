---
title: Rövid útmutató – a HoloLens Unity-alkalmazás létrehozása az Azure térbeli horgonyok |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan hozhat létre egy HoloLens alkalmazást a Unity térbeli horgonyok használatával.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2c29d9860f1c8fc4f0f6d9f4d84c06e8ade8dee5
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286975"
---
# <a name="quickstart-create-a-hololens-unity-app-that-uses-azure-spatial-anchors"></a>Gyors útmutató: Egy Azure térbeli horgonyok használó HoloLens Unity-alkalmazás létrehozása

Ez a rövid útmutatóban létrehozhat egy HoloLens Unity-alkalmazás által használt [Azure térbeli horgonyok](../overview.md). Térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy hozzon létre vegyes valóság tapasztalatok objektumok helyükre kivonatuk eszközök idővel. Ha elkészült, egy HoloLens, menthet és egy térbeli kapcsolati alapot visszahívása Unity-alkalmazás lesz.

A következőket fogja megtanulni:

- Hozzon létre egy térbeli horgonyok fiókot.
- Készítse elő a Unity-létrehozási beállítások.
- Konfigurálja a térbeli horgonyok fiók azonosítóját és a fiókkulcsot.
- Exportálja a HoloLens Visual Studio-projekt.
- Telepítse az alkalmazást, és futtassa a HoloLens eszközön.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:


- Egy Windows-számítógépen, amelyre szüksége <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> vagy újabb és <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> vagy újabb van telepítve. A Visual Studio telepítésének tartalmaznia kell a **univerzális Windows Platform fejlesztési** számítási feladatot. Emellett telepítenie kell <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>.
- HoloLens eszköz, amelyre szüksége [fejlesztői mód](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) engedélyezve van. [Windows-2018. október 10. frissítés](https://docs.microsoft.com/en-us/windows/mixed-reality/release-notes-october-2018) (más néven RS5) telepítve kell lennie az eszközön. A legújabb verziót a HoloLens frissítéséhez nyissa meg a **beállítások** alkalmazást, lépjen a **frissítés és biztonság**, majd válassza ki **frissítések keresése**.
- Az alkalmazásban, engedélyeznie kell a **SpatialPerception** képesség. Ez a beállítás nem található **Build Settings** > **Player beállítások** > **közzétételi beállítások**  >   **Képességek**.
- Az alkalmazásban, engedélyeznie kell a **virtuális valóságban támogatott** a **Windows vegyes valóság SDK**. Ez a beállítás nem található **Build Settings** > **Player beállítások** > **XR beállítások**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Nyissa meg a mintaprojektet a Unity-nél

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

A Unity-nél nyissa meg a projekt a Unity-mappában.

Nyissa meg **Build Settings** kiválasztásával **fájl** > **Build Settings**.

Az a **Platform** szakaszban jelölje be **univerzális Windows Platform**. Módosítsa a **eszközre** való **HoloLens**.

Válassza ki **kapcsoló Platform** módosítása a platform **univerzális Windows Platform**. Unity kérhetik a UWP támogatási összetevők telepítését, ha hiányzik.

   ![Unity-beállítások létrehozása ablak](./media/get-started-unity-hololens/unity-build-settings.png)

Zárja be a **Build Settings** ablak.

## <a name="configure-the-account-identifier-and-key"></a>A fiókazonosító és kulcsának konfigurálása

Az a **projekt** panelen lépjen a `Assets/AzureSpatialAnchorsPlugin/Examples` , és nyissa meg a `AzureSpatialAnchorsBasicDemo.unity` jelenetfájl.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenet kiválasztásával **fájl** > **mentése**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportálás a HoloLens Visual Studio-projekt

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza ki **összeállítása**. A párbeszédpanelen válassza ki a mappát, ahová exportálni a HoloLens Visual Studio-projekt.

Az Exportálás befejeződése után megjelenik az exportált HoloLens-projektet tartalmazó mappa.

## <a name="deploy-the-hololens-application"></a>A HoloLens-alkalmazás üzembe helyezése

A mappában kattintson duplán a **HelloAR U3D.sln** megnyitja a projektet a Visual Studióban.

Módosítása a **megoldás konfigurációs** való **kiadási**, módosítsa a **megoldás platformját** , **x86**, válassza ki **eszköz**  az üzembe helyezés cél közül.

   ![Visual Studio configuration](./media/get-started-unity-hololens/visual-studio-configuration.png)

Kapcsolja be a HoloLens-eszközök, jelentkezzen be, és csatlakoztassa az eszközt a Számítógéphez egy USB-kábel használatával.

Válassza ki **Debug** > **hibakeresés indítása** helyezze üzembe az alkalmazást, és a hibakeresés elindításához.

Kövesse az utasításokat az alkalmazásban kívánja helyezni horgonyra visszaírásához.

A Visual Studióban, állítsa le az alkalmazást vagy kiválasztásával **hibakeresés leállításához** vagy a Shift + F5 billentyűkombinációt.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása eszközök](../tutorials/tutorial-share-anchors-across-devices.md)
