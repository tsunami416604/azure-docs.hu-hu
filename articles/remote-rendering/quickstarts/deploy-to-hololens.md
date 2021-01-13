---
title: Unity-minta üzembe helyezése a HoloLensben
description: Gyorsútmutató a Unity-minta a HoloLensben való üzembe helyezéséhez
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: a5a44b9d27901fcd00f8813096de6bbae5f2310d
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179945"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Gyorsútmutató: Unity-minta üzembe helyezése a HoloLensben

Ez a gyorsútmutató bemutatja, hogyan helyezheti üzembe és futtathatja a gyorsútmutató Unityhez készült mintaalkalmazását a HoloLens 2-n.

E gyorsútmutató segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
>* A gyorsútmutató HoloLenshez készült mintaalkalmazásának létrehozása
>* A minta üzembe helyezése az eszközön
>* A minta futtatása az eszközön

## <a name="prerequisites"></a>Előfeltételek

Ebben a gyorsútmutatóban a [Gyorsútmutató: Modell renderelése a Unityvel](render-model.md) című útmutatóból származó mintaprojektet fogjuk üzembe helyezni.

Győződjön meg arról, hogy a hitelesítő adatai megfelelően vannak mentve a jelenettel, és hogy képes munkamenetekhez csatlakozni a Unity szerkesztőjéből.

## <a name="build-the-sample-project"></a>A mintaprojekt létrehozása

1. Nyissa meg a *File > Build Settings* (Fájl > Létrehozási beállítások) menüpontot.
1. A *Platform* értékét módosítsa az **Universal Windows Platform** (Univerzális Windows-platform) értékre
1. A *Target Device* (Céleszköz) értékét módosítsa a **HoloLens** értékre
1. Az *Architecture* (Architektúra) értékét módosítsa az **ARM64** értékre
1. A *Build Type* (Létrehozási típus) értékét módosítsa a **D3D Project**\ (D3D-projekt) értékre
    ![Build settings](./media/unity-build-settings.png) (Létrehozási beállítások)
1. Válassza a **Switch to Platform** (Váltás platformra) lehetőséget
1. A **Build** vagy a Build and Run (Létrehozás, Létrehozás és futtatás) lehetőségre való kattintáskor a rendszer arra kéri, hogy válasszon ki egy mappát a megoldás tárolására
1. Nyissa meg a létrehozott **Quickstart.sln** fájlt a Visual Studióval
1. Módosítsa a konfigurációt a **Release** (Kiadás) és az **ARM64** értékre
1. A hibakeresési módot állítsa a **Remote Machine** (Távoli gép) értékre\
    ![A megoldás konfigurációja](media/unity-deploy-config.png)
1. A megoldás létrehozása
1. A „Quickstart” (Gyorsútmutató) projektnél lépjen a *Properties > Debugging* (Tulajdonságok > Hibakeresés) területre
    1. Győződjön meg arról, hogy a *Release* (Kiadás) konfiguráció aktív
    1. Állítsa a *Debugger to Launch* (Elindítandó hibakereső) értékét a **Remote Machine** (Távoli gép) értékre
    1. A *számítógép nevének* módosítása a **HoloLens IP-** címére

## <a name="launch-the-sample-project"></a>A mintaprojekt elindítása

1. Csatlakoztassa a HoloLenst a számítógépéhez egy USB-kábellel.
1. Indítsa el a hibakeresőt a Visual Studióban (F5). Az alkalmazás automatikusan üzembe lesz helyezve az eszközön.

Ha a mintaalkalmazás elindult, indítson egy új munkamenetet. A munkamenet egy idő után elkészül, és a távolról renderelt modell meg fog jelenni Ön előtt.
Ha később ismét el szeretné indítani a mintaalkalmazást, mostantól a HoloLens Start menüjében is megtalálhatja.

## <a name="next-steps"></a>Következő lépések

A következő gyorsútmutatóban az egyéni modellek konvertálásával foglalkozunk.

> [!div class="nextstepaction"]
> [Gyorsútmutató: Modell konvertálása a renderelés előtt](convert-model.md)
