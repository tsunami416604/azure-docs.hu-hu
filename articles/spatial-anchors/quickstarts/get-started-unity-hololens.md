---
title: Rövid útmutató – HoloLens Unity-alkalmazás létrehozása az Azure térbeli horgonyok |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan hozhat létre egy HoloLens alkalmazást a Unity térbeli horgonyok használatával.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c8fc99426751e9aaaec53cbb6b64a84b6e7f6734
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894325"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>Gyors útmutató: Egy Azure térbeli horgonyok használatával HoloLens Unity-alkalmazás létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre a HoloLens Unity segítségével [Azure térbeli horgonyok](../overview.md). Az Azure térbeli horgonyok platformfüggetlen fejlesztői szolgáltatása lehetővé teszi, hogy a vegyes valóság élmény helyükre kivonatuk eszközök idővel-objektumok segítségével. Ha elkészült, egy HoloLens, menthet és egy térbeli kapcsolati alapot visszahívása Unity-alkalmazás lesz.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Térbeli horgonyok fiók létrehozása
> * Készítse elő a Unity-létrehozási beállítások
> * A térbeli horgonyok fiók azonosítóját és a fiókkulcsot konfigurálása
> * Exportálás a HoloLens Visual Studio-projekt
> * Üzembe helyezése és futtatása a HoloLens eszköz

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Olyan Windows-gépeken, <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a>, <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> telepített a **univerzális Windows Platform fejlesztési** számítási feladatok, és <a href="https://git-scm.com/download/win" target="_blank">Git Pro Windows</a>.
- HoloLens eszköz [fejlesztői mód](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) engedélyezve van. Ez a cikk a HoloLens eszköz van szükség a [Windows 2018. október 10. frissítés](https://docs.microsoft.com/en-us/windows/mixed-reality/release-notes-october-2018) (más néven RS5). A legújabb verziót a HoloLens frissítéséhez nyissa meg a **beállítások** alkalmazást, lépjen a **frissítés és biztonság**, majd válassza a **frissítések keresése** gombra.
- Az alkalmazás be kell állítani a **SpatialPerception** funkció alatt **Build Settings**->**Player beállítások**->**közzétételi Beállítások**->**képességek**.
- Engedélyeznie kell az alkalmazás **virtuális valóságban támogatott** a **Windows vegyes valóság SDK** alatt **Build Settings**->**lejátszóbeállításai** -> **XR beállítások**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Nyissa meg a mintaprojektet a Unity-nél

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Nyissa meg a Unity, és nyissa meg a projektet, a `Unity` mappát.

Nyissa meg **Build Settings** kiválasztásával **fájl** -> **Build Settings**.

Az a **Platform** szakaszban jelölje be **univerzális Windows Platform**. Majd módosíthatja a **céleszköz** való **HoloLens**.

Válassza ki **kapcsoló Platform** módosítása a platform **univerzális Windows Platform**. Unity megkérheti, hogy UWP támogatási összetevők telepítéséhez, ha hiányzik.

![Unity-létrehozási beállítások](./media/get-started-unity-hololens/unity-build-settings.png)

Zárja be a **Build Settings** ablak.

## <a name="configure-account-identifier-and-key"></a>Fiókazonosító és kulcsának konfigurálása

Az a **projekt** ablaktáblán keresse meg `Assets/AzureSpatialAnchorsPlugin/Examples` , és nyissa meg a `AzureSpatialAnchorsBasicDemo.unity` jelenetfájl.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Mentse a jelenet kiválasztásával **fájl** -> **mentése**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportálás a HoloLens Visual Studio-projekt

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Válassza ki **összeállítása** egy párbeszédpanel megnyitásához. Ezután válasszon egy mappát a HoloLens Visual Studio-projekt exportálása.

Az Exportálás befejeződése után egy mappa megjelenik az exportált HoloLens-projektet tartalmazó.

## <a name="deploy-the-hololens-application"></a>A HoloLens-alkalmazás üzembe helyezése

A mappában kattintson duplán a `HelloAR U3D.sln` megnyitja a projektet a Visual Studióban.

Módosítsa a **megoldás konfigurációs** a **kiadási**, módosítása **megoldás platformját** való **x86**, válassza ki **eszköz**  az üzembe helyezés cél közül.

![Visual Studio Configuration](./media/get-started-unity-hololens/visual-studio-configuration.png)

A HoloLens eszköz Power, jelentkezzen be, és csatlakoztassa a Számítógépet, USB-kábel segítségével.

Válassza ki **Debug** > **hibakeresés indítása** helyezze üzembe az alkalmazást, és a hibakeresés elindításához.

Kövesse az utasításokat az alkalmazásban kívánja helyezni horgonyra visszaírásához.

A Visual Studióban, állítsa le az alkalmazást a kiválasztásával **hibakeresés leállításához** vagy megnyomásával **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Oktatóanyag: Megosztás térbeli horgonyok eszközök között](../tutorials/tutorial-share-anchors-across-devices.md)
