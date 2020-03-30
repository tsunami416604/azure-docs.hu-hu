---
title: 'Rövid útmutató: HoloLens-alkalmazás létrehozása unity-el'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre HoloLens-alkalmazást a Unity segítségével térbeli horgonyok használatával.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2a427eab107c70b19932b7b8ddc5a7fc531ef19a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77615425"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Rövid útmutató: Hozzon létre egy Unity HoloLens alkalmazást, amely az Azure Spatial Anchors-t használja

Ebben a rövid útmutatóban létrehozhat egy Unity HoloLens alkalmazást, amely [az Azure Spatial Anchors alkalmazást](../overview.md)használja. A Spatial Anchors egy platformfüggetlen fejlesztői szolgáltatás, amely lehetővé teszi, hogy vegyes valóságélményeket hozzon létre olyan objektumokkal, amelyek az eszközök között megőrzik helyüket az idő múlásával. Ha végzett, egy Unity-vel készült HoloLens alkalmazással rendelkezik, amely képes megmenteni és felidézni egy térbeli horgonyt.

A következőket fogja megtanulni:

- Térbeli horgonyok fiók létrehozása.
- Készítse elő az Unity buildbeállításait.
- Konfigurálja a Térbeli horgonyok fiókazonosítóját és a fiókkulcsot.
- Exportálja a HoloLens Visual Studio projektet.
- Telepítse az alkalmazást, és futtassa holoLens-eszközön.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Olyan Windows-számítógépre van szüksége, amelyen a <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 vagy 2019.2</a> vagy újabb, valamint a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019-es</a> vagy újabb verziók telepítve vannak. A Visual Studio telepítésének tartalmaznia kell az **univerzális Windows-platform fejlesztési** munkaterhelését és a **Windows 10 SDK (10.0.18362.0 vagy újabb)** összetevőt. Telepítenie kell <a href="https://git-scm.com/download/win" target="_blank">a Git for Windows</a> és a <a href="https://git-lfs.github.com/">Git LFS rendszert</a>is.
- Olyan HoloLens eszközre van szükség, amelyen a [fejlesztői mód](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) engedélyezve van. [A Windows 10 2018 októberi frissítését](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (más néven RS5-frissítést) telepíteni kell az eszközre. A HoloLens legújabb kiadására való frissítéshez nyissa meg a **Beállítások** alkalmazást, nyissa meg **a & biztonság frissítése**lehetőséget, és válassza a Frissítések **keresése**lehetőséget.
- Az alkalmazásban engedélyeznie kell a **SpatialPerception** funkciót. Ez a beállítás a **Build Settings** > **Player Settings** > **Közzétételi beállítások** > **képességei**ben található.
- Az alkalmazásban engedélyeznie kell a **Windows Mixed Reality SDK-val**támogatott virtuális **valóságot.** Ez a beállítás a Build Settings**Player Settings** > XR Settings ( **buildelési beállítások** > **) xr beállításai ban**található.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Töltse le és nyissa meg a Unity mintaprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

A **Build-beállítások** megnyitásához válassza a **Fájlösszeállítás** > **beállításai lehetőséget.**

A **Platform** szakaszban válassza az **Univerzális Windows-platform lehetőséget.** Módosítsa a **céleszközt** **HoloLens értékre.**

Válassza **a Platform váltása** lehetőséget, ha univerzális **Windows-platformra**szeretné módosítani a platformot. A Unity kérheti az UWP támogatási összetevőinek telepítését, ha hiányoznak.

![Unity buildbeállítások ablak](./media/get-started-unity-hololens/unity-build-settings.png)

Zárja be a **Build Settings ablakot.**

## <a name="configure-the-account-identifier-and-key"></a>A fiókazonosító és a kulcs konfigurálása

A **Projekt** ablaktáblán `Assets/AzureSpatialAnchors.Examples/Scenes` nyissa `AzureSpatialAnchorsBasicDemo.unity` meg a jelenetfájlt, és nyissa meg azt.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenetet a **Fájlmentés** > lehetőség**kiválasztásával.**

## <a name="export-the-hololens-visual-studio-project"></a>A HoloLens Visual Studio projekt exportálása

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza **a Build**lehetőséget. A párbeszédpanelen jelölje ki azt a mappát, amelybe exportálni szeretné a HoloLens Visual Studio projektet.

Amikor az exportálás befejeződött, megjelenik egy mappa, amely az exportált HoloLens projektet tartalmazza.

## <a name="deploy-the-hololens-application"></a>A HoloLens alkalmazás telepítése

A mappában kattintson duplán a **HelloAR U3D.sln** fájlra a projekt Visual Studio-ban való megnyitásához.

Módosítsa a **megoldás konfigurációját** **kiadásra**, módosítsa a **megoldásplatformot** **x86-ra**, és válassza az **Eszköz** lehetőséget a telepítési célbeállítások közül.

Ha holoLens 2-t használ, az **ARM64-et** használja **megoldásplatformként**az **x86**helyett.

   ![Visual Studio konfigurációja](./media/get-started-unity-hololens/visual-studio-configuration.png)

Kapcsolja be a HoloLens-eszközt, jelentkezzen be, és csatlakoztassa az eszközt a számítógéphez USB-kábellel.

Válassza **a Hibakeresés** > **indítása hibakeresés** lehetőséget az alkalmazás üzembe helyezéséhez és a hibakeresés megkezdéséhez.

Kövesse az utasításokat az alkalmazásban, hogy helyezzen el és visszahívása horgonyt.

A Visual Studio-ban állítsa le az alkalmazást a **Hibakeresés leállítása** vagy a Shift+F5 lehetőséget választva.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unity-20193"></a>Egység 2019.3

A változások megszakadása miatt a Unity 2019.3 jelenleg nem támogatott. Kérjük, használja a Unity 2019.1 vagy 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
