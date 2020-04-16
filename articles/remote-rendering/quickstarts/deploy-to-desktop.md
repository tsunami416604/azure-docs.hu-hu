---
title: Unity-minta üzembe helyezése az asztalon
description: Rövid útmutató, amely bemutatja, hogyan juthat el a Unity minta egy asztali számítógépre
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 95993a54d321c9581d35f12b56d60e4ea052e505
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415726"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Rövid útmutató: Unity-minta telepítése az asztalra

Ez a rövid útmutató ismerteti, hogyan telepítheti és futtatja a Unity rövid útmutatóminta alkalmazását egy asztali számítógépre.

Ebben a rövid útmutatóban megtudhatja, hogyan:

> [!div class="checklist"]
>
>* A rövid útmutató mintaalkalmazás létrehozása asztali használatra
>* A minta központi telepítése számítógépre
>* A minta futtatása számítógépen

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a mintaprojektet a [Rövid útmutatóból telepítjük: Egy modell renderelése unityvel.](render-model.md)

Győződjön meg arról, hogy a hitelesítő adatok mentése megfelelően a jelenetet, és csatlakozhat a munkamenet az Egység szerkesztő.

## <a name="disable-virtual-reality-support"></a>A virtuális valóság támogatásának letiltása

Az asztali számítógépeken jelenleg csak a sík asztali alkalmazások támogatottak, ezért a VR-támogatást le kell tiltani.

1. A *Projektbeállítások szerkesztése > megnyitása...*
1. Válassza a bal oldali **Lejátszó** lehetőséget.
1. Válassza az **Univerzális Windows Platform beállításai** lapot.
1. Bontsa ki az **XR-beállításokat**.
1. A **virtuális valóság támogatott letiltása**.
    ![lejátszó beállításai](./media/unity-disable-xr.png)
1. Az *XR-beállítások*felett bontsa ki a **Közzétételi beállítások csomópontot.**
1. A **Támogatott eszközcsaládok területen**ellenőrizze, hogy az **Asztal** jelölőnégyzet be van-e jelölve.

## <a name="build-the-sample-project"></a>A mintaprojekt létrehozása

1. Nyissa *meg a Fájl > a létrehozási beállításokat.*
1. A *platform* módosítása **univerzális Windows platformra.**
1. Állítsa *a céleszközt* **a PC-re.**
1. Állítsa *az Architektúrát* **x86-ra.**
1. Állítsa *a Build Type* **(D3D-projekt) beállítását.**
  ![Build-beállítások](./media/unity-build-settings-pc.png)
1. Válassza **a Váltás platformra**lehetőséget.
1. A **Build** (vagy a Build and Run) billentyű lenyomásakor a rendszer megkéri, hogy válasszon ki egy mappát, ahol a megoldást tárolni kell.
1. Nyissa meg a létrehozott **Quickstart.sln alkalmazást** a Visual Studio segítségével.
1. Módosítsa a konfigurációt **Release** és **x86**.
1. Váltson a hibakereső módra **helyi gépre**.
  ![Megoldás konfigurációja](./media/unity-deploy-config-pc.png)
1. Készítsd el a megoldást (F7).

> [!WARNING]
> Győződjön meg arról, hogy **az x86 lehetőséget választja.** **X64** jelenleg nem támogatott, lásd [platform korlátozások](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>A mintaprojekt elindítása

Indítsa el a hibakeresőt a Visual Studio (F5) alkalmazásban. Automatikusan telepíti az alkalmazást a számítógépre.

A mintaalkalmazásnak el kell indítania, majd új munkamenetet kell indítania. Egy idő után a munkamenet készen áll, és a távolról renderelt modell megjelenik ön előtt.
Ha később másodszor is el szeretné indítani a mintát, akkor most már megtalálhatja a Start menüből is.

## <a name="next-steps"></a>További lépések

A következő rövid útmutatóban egy egyéni modell konvertálását vizsgáljuk.

> [!div class="nextstepaction"]
> [Rövid útmutató: Modell konvertálása rendereléshez](convert-model.md)
