---
title: Unity-minta üzembe helyezése a HoloLensben
description: Gyors útmutató, amely bemutatja, hogyan kérhető le az Unity minta a HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: d6acc16780179654975d63ab2c0b04caf141510c
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85557054"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Gyors útmutató: Unity-minta üzembe helyezése a HoloLens-ben

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe és futtathatja az Unity-hez készült gyors üzembe helyezési minta alkalmazást a 2. HoloLens.

Ebből a rövid útmutatóból megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
>* A HoloLens-hez készült gyors üzembe helyezési minta alkalmazás összeállítása
>* A minta üzembe helyezése az eszközön
>* A minta futtatása az eszközön

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a minta projektet a gyors üzembe helyezési pontról fogjuk üzembe helyezni [: modell megjelenítése egységgel](render-model.md).

Győződjön meg arról, hogy a hitelesítő adatai megfelelően vannak mentve a jelenettel, és csatlakozhat egy munkamenethez az Unity editoron belülről.

## <a name="build-the-sample-project"></a>A minta projekt összeállítása

1. Nyissa meg a *fájl > a létrehozási beállítások menüpontot*.
1. *Platform* módosítása **univerzális Windows-platformre**
1. *Cél eszköz* beállítása **HoloLens**
1. *Architektúra* beállítása **ARM64**
1. *Build típusának* beállítása a **D3D-projekthez**\
    ![Összeállítási beállítások](./media/unity-build-settings.png)
1. Válassza **a váltás a platformra** lehetőséget
1. A **Build** (vagy a build és a Run) gomb megnyomásakor a rendszer arra kéri, hogy válasszon ki egy mappát, ahol a megoldást tárolni szeretné
1. A generált gyors útmutató **SLN** megnyitása a Visual Studióval
1. Konfiguráció módosítása **kiadásra** és **ARM64**
1. A hibakereső üzemmód átváltása **Távoli gépre**\
    ![Megoldás konfigurálása](media/unity-deploy-config.png)
1. Megoldás létrehozása (F7)
1. A "gyors útmutató" projekt esetében lépjen a *tulajdonságok > hibakeresés* elemre.
    1. Győződjön meg arról, hogy a konfigurációs *kiadás* aktív
    1. Hibakereső beállítása a **Távoli gépre** *való indításhoz*
    1. A *számítógép nevének* módosítása a **HoleLens IP-** címére

## <a name="launch-the-sample-project"></a>A minta projekt elindítása

1. Csatlakoztassa a HoloLens USB-kábellel a számítógéphez.
1. Indítsa el a hibakeresőt a Visual Studióban (F5). A rendszer automatikusan telepíti az alkalmazást az eszközre.

A minta alkalmazásnak el kell indítania, majd el kell indítania egy új munkamenetet. Egy idő után a munkamenet készen áll, és a távolról renderelt modell fog megjelenni az Ön előtt.
Ha később szeretné elindítani a mintát, azt a HoloLens Start menüjéből is megtalálhatja.

## <a name="next-steps"></a>További lépések

A következő rövid útmutatóban egy egyéni modell átalakítását fogjuk megtekinteni.

> [!div class="nextstepaction"]
> [Gyors útmutató: modell átalakítása renderelésre](convert-model.md)
