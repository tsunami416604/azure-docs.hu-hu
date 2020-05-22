---
title: Unity-minta üzembe helyezése az asztalon
description: Gyors útmutató, amely bemutatja, hogyan kérhető le az Unity minta asztali SZÁMÍTÓGÉPekre
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 59b235a32ae9363cc5fc804385867e140be502e2
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758996"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Gyors útmutató: Unity-minta üzembe helyezése az asztalra

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe és futtathatja az Unity-hez készült gyors üzembe helyezési minta alkalmazást az asztali SZÁMÍTÓGÉPeken.

Ebből a rövid útmutatóból megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
>* Az asztali gyors üzembe helyezési minta alkalmazás összeállítása
>* A minta üzembe helyezése SZÁMÍTÓGÉPeken
>* A minta futtatása számítógépen

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a minta projektet a gyors üzembe helyezési pontról fogjuk üzembe helyezni [: modell megjelenítése egységgel](render-model.md).

Győződjön meg arról, hogy a hitelesítő adatai megfelelően vannak mentve a jelenettel, és csatlakozhat egy munkamenethez az Unity editoron belülről.

## <a name="disable-virtual-reality-support"></a>Virtuális valóság támogatásának letiltása

Jelenleg csak a lapos asztali alkalmazások támogatottak az asztalon, így a VR-támogatást le kell tiltani.

1. Nyissa meg a *> projekt beállításainak szerkesztése...*
1. Válassza ki a bal oldali **lejátszót** .
1. Válassza a **univerzális Windows-platform beállítások** lapot.
1. Bontsa ki az **XR beállításait**.
1. Tiltsa le a **virtuális valóságot**.
    ![lejátszó beállításai](./media/unity-disable-xr.png)
1. A fenti *XR-beállítások*között bontsa ki a **közzétételi beállítások**elemet.
1. A **támogatott eszközökhöz tartozó családokban**ellenőrizze, hogy az **asztal** be van-e jelölve.

## <a name="build-the-sample-project"></a>A minta projekt összeállítása

1. Nyissa meg a *fájl > a létrehozási beállítások menüpontot*.
1. A *Platform* **univerzális Windows-platformre** való módosítása (a**PC standalone** is támogatott, de itt nem használható).
1. A *célszámítógép* beállítása a **számítógépnek**.
1. *Architektúra* beállítása **x86**-ra.
1. A *Build típusának* beállítása a **D3D-projekthez**.
  ![Összeállítási beállítások](./media/unity-build-settings-pc.png)
1. Válassza **a váltás platformra**lehetőséget.
1. A **Build** (vagy a build és a Run) gomb megnyomásakor a rendszer arra kéri, hogy válasszon ki egy mappát, ahol a megoldást tárolni szeretné.
1. Nyissa meg a generált rövid útmutató **. SLN** a Visual Studióval.
1. Módosítsa a konfigurációt **kiadásra** és **x86**-ra.
1. Kapcsolja a hibakereső üzemmódot a **helyi gépre**.
  ![Megoldás konfigurálása](./media/unity-deploy-config-pc.png)
1. Hozza létre a megoldást (F7).

> [!WARNING]
> Győződjön meg arról, hogy az **x86**lehetőséget választotta. az **x64** jelenleg nem támogatott, lásd a [platform korlátozásait](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>A minta projekt elindítása

Indítsa el a hibakeresőt a Visual Studióban (F5). A rendszer automatikusan telepíti az alkalmazást a számítógépre.

A minta alkalmazásnak el kell indítania, majd el kell indítania egy új munkamenetet. Egy idő után a munkamenet készen áll, és a távolról renderelt modell fog megjelenni az Ön előtt.
Ha később szeretné elindítani a mintát, azt a Start menüből is megtalálhatja.

## <a name="next-steps"></a>További lépések

A következő rövid útmutatóban egy egyéni modell átalakítását fogjuk megtekinteni.

> [!div class="nextstepaction"]
> [Gyors útmutató: modell átalakítása renderelésre](convert-model.md)
