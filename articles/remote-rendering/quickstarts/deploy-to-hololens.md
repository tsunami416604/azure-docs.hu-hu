---
title: Unity-minta telepítése a HoloLensen
description: Rövid útmutató, amely bemutatja, hogyan juthat el az Egység minta a HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679736"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Rövid útmutató: Unity-minta telepítése a HoloLensen

Ez a rövid útmutató bemutatja, hogyan telepítheti és futtatható a Unity rövid útmutatóminta alkalmazását a HoloLens 2-n.

Ebben a rövid útmutatóban megtudhatja, hogyan:

> [!div class="checklist"]
>
>* A HoloLens rövid útmutatómintaalkalmazásának létrehozása
>* A minta telepítése az eszközre
>* A minta futtatása az eszközön

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a mintaprojektet a [Rövid útmutatóból telepítjük: Egy modell renderelése unityvel.](render-model.md)

Győződjön meg arról, hogy a hitelesítő adatok mentése megfelelően a jelenetet, és csatlakozhat a munkamenet az Egység szerkesztő.

## <a name="build-the-sample-project"></a>A mintaprojekt létrehozása

1. Nyissa *meg a Fájl > a létrehozási beállításokat.*
1. *Platform* módosítása **univerzális Windows-platformra**
1. *A céleszköz* beállítása **holoLensre**
1. *Architektúra* beállítása **ARM64-re**
1. A *Build Type (Létrehozástípusa)* beállítás **D3D-projektbuild-beállításokra** ![](./media/unity-build-settings.png)
1. Válaszd **a Platformra váltás lehetőséget.**
1. A **Build** (vagy "Build And Run") billentyű lenyomásakor a rendszer megkéri, hogy válasszon ki egy mappát, ahol a megoldást tárolni kell
1. A létrehozott **Quickstart.sln** megnyitása a Visual Studio segítségével
1. A konfiguráció módosítása **Kiadás** és **ARM64 beállításra**
1. A hibakereső mód váltása **a Távoli gépmegoldás** ![konfigurációjára](media/unity-deploy-config.png)
1. Készítsd el a megoldást (F7)
1. A "Gyorsútmutató" projekthez nyissa meg *a Tulajdonságok > hibakeresés című projektet.*
    1. Győződjön meg arról, hogy a *konfigurációs kiadás* aktív
    1. A *Hibakereső indítása* **távoli számítógépre**
    1. *A gépnév* módosítása a **HoleLens IP-címére**

## <a name="launch-the-sample-project"></a>A mintaprojekt elindítása

1. Csatlakoztassa a HoloLenst USB-kábellel a számítógéphez.
1. Indítsa el a hibakeresőt a Visual Studio (F5) alkalmazásban. Automatikusan telepíti az alkalmazást az eszközre.

A mintaalkalmazásnak el kell indítania, majd új munkamenetet kell indítania. Egy idő után a munkamenet készen áll, és a távolról renderelt modell megjelenik ön előtt.
Ha később szeretné elindítani a mintát, akkor most a HoloLens start menüjéből is megtalálhatja.

## <a name="next-steps"></a>További lépések

A következő rövid útmutatóban egy egyéni modell konvertálását vizsgáljuk.

> [!div class="nextstepaction"]
> [Rövid útmutató: Modell konvertálása rendereléshez](convert-model.md)
