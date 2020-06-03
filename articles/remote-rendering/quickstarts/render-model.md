---
title: Modell renderelése a Unityvel
description: Útmutató, amely végigvezeti a felhasználót a modell megjelenítésének lépésein
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: ed533f571ada4cacf053157b397cca07cd65e6f8
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300572"
---
# <a name="quickstart-render-a-model-with-unity"></a>Gyors útmutató: modell megjelenítése egységgel

Ez a rövid útmutató azt ismerteti, hogyan futtathat egy olyan Unity-mintát, amely távolról, az Azure Remote rendering (ARR) szolgáltatás használatával jeleníti meg a beépített modellt.

Nem fogjuk részletesen bemutatni az ARR API-t, vagy egy új Unity-projekt beállítását. Ezek a témakörök a következő [oktatóanyagban találhatók: Unity-projekt létrehozása a semmiből](../tutorials/unity/project-setup.md).

Ebből a rövid útmutatóból megtudhatja, hogyan végezheti el a következőket:
> [!div class="checklist"]
>
>* A helyi fejlesztési környezet beállítása
>* Az egységhez készült ARR gyors minta alkalmazás beszerzése és összeállítása
>* Modell renderelése az ARR Gyorsindítás minta alkalmazásban

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy hozzáférjen az Azure távoli renderelési szolgáltatáshoz, először [létre kell hoznia egy fiókot](../how-tos/create-an-account.md).

A következő szoftvereket kell telepíteni:

* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A Visual Studio 2019 legújabb verziója [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio-eszközök vegyes valósághoz](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). A következő számítási *feladatok* telepítése kötelező:
  * **Asztali fejlesztés C++ nyelven**
  * **Univerzális Windows-platform (UWP) fejlesztése**
* GIT [(letöltés)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(letöltés)](https://unity3d.com/get-unity/download)
  * Telepítse ezeket a modulokat az Unity-ben:
    * **UWP** – univerzális Windows-platform-Build támogatása
    * **IL2CPP** – Windows Build-támogatás (IL2CPP)

## <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Nyisson meg egy parancssort (írja be `cmd` a Windows Start menüjét), és váltson arra a könyvtárba, ahol az ARR-minta projektet tárolni szeretné.

Futtassa az alábbi parancsot:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Az utolsó parancs létrehoz egy alkönyvtárat az ARR könyvtárban, amely az Azure távoli renderelés különböző mintáit tartalmazza.

Az egységhez készült gyors üzembe helyezési minta alkalmazás az alkönyvtári *egység/* gyors útmutatóban található.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Modell megjelenítése az Unity Sample projekttel

Nyissa meg az Unity hubot, és adja hozzá a minta projektet, amely a *ARR\azure-Remote-rendering\Unity\Quickstart* mappa.
Nyissa meg a projektet. Ha szükséges, engedélyezze az egységnek a projekt frissítését a telepített verzióra.

A megjelenített alapértelmezett modell egy [beépített minta modell](../samples/sample-model.md). A [következő](convert-model.md)rövid útmutatóban bemutatjuk, hogyan lehet egyéni modellt ÁTALAKÍTANI az ARR konverziós szolgáltatás használatával.

### <a name="enter-your-account-info"></a>Adja meg a fiók adatait

1. Az Unity-eszköz böngészőben navigáljon a *jelenetek* mappára, és nyissa meg a gyors üzembe helyezési **jelenetet.**
1. A *hierarchiában*válassza ki a **RemoteRendering** játék objektumot.
1. Az *ellenőrben*adja meg a [fiókja hitelesítő adatait](../how-tos/create-an-account.md). Ha még nem rendelkezik fiókkal, [hozzon létre egyet](../how-tos/create-an-account.md).

![ARR-fiók adatai](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Az Azure Portalon a fiók tartománya csak *mixedreality.Azure.com*-ként jelenik meg. Ez nem elegendő a sikeres csatlakozáshoz.
> Állítsa be a **AccountDomain** a értékre `<region>.mixedreality.azure.com` , ahol az `<region>` az [elérhető régiók egyike](../reference/regions.md).

Később telepíteni szeretnénk ezt a projektet egy HoloLens, és az adott eszközről csatlakozunk a távoli renderelési szolgáltatáshoz. Mivel a hitelesítő adatok nem írhatók be az eszközön, a gyors üzembe helyezési minta a **hitelesítő adatokat az egység jelenetében fogja menteni**.

> [!WARNING]
> Győződjön meg arról, hogy nem ellenőrzi a projektet a mentett hitelesítő adatokkal egy olyan adattárba, ahol a titkos bejelentkezési adatok szivárognak ki.

### <a name="create-a-session-and-view-the-default-model"></a>Munkamenet létrehozása és az alapértelmezett modell megtekintése

A munkamenet elindításához nyomja meg az egység **Lejátszás** gombját. Meg kell jelennie egy, az állapotjelző szöveggel rendelkező átfedésnek a *játék* paneljén a nézetablak alján. A munkamenet az állapot-váltások sorozatán keresztül fog esni. A **kezdő** állapotban a távoli virtuális gép megpördült, ami több percet vesz igénybe. A művelet sikere esetén a **kész** állapotra vált. A munkamenet most a **Csatlakozás** állapotba kerül, ahol a virtuális gépen lévő renderelési futtatókörnyezetet próbálja elérni. Sikeres művelet esetén a minta a **csatlakoztatott** állapotra vált. Ekkor a rendszer elindítja a modell letöltését a rendereléshez. A modell mérete miatt a letöltés több percet is igénybe vehet. Ekkor megjelenik a távolról renderelt modell.

![A minta kimenete](media/arr-sample-output.png)

Gratulálunk! Most megtekint egy távolról renderelt modellt!

## <a name="inspecting-the-scene"></a>A jelenet vizsgálata

A távoli renderelési kapcsolatok futtatása után a felügyelő panel további állapotinformációkat is frissít:

![Unity minta lejátszása](./media/arr-sample-configure-session-running.png)

Most már felfedezheti a jelenet gráfot úgy, hogy kijelöli az új csomópontot, és rákattint a **gyermekek megjelenítése** lehetőségre a felügyelőben.

![Unity-hierarchia](./media/unity-hierarchy.png)

A jelenetben van egy [kivágott sík](../overview/features/cut-planes.md) objektum. Próbálja meg engedélyezni a tulajdonságait, és helyezze át a következőre:

![A kivágási sík módosítása](media/arr-sample-unity-cutplane.png)

Az átalakítások szinkronizálásához kattintson a **szinkronizálás most** lehetőségre, vagy jelölje be a **minden keret szinkronizálása** beállítást. Összetevő-tulajdonságok esetében csak a módosítása elég.

## <a name="next-steps"></a>További lépések

A következő rövid útmutatóban a mintát egy HoloLens fogjuk üzembe helyezni, hogy az eredeti méretben megtekintse a távolról renderelt modellt.

> [!div class="nextstepaction"]
> [Gyors útmutató: Unity-minta üzembe helyezése a HoloLens-ben](deploy-to-hololens.md)

Azt is megteheti, hogy a minta asztali SZÁMÍTÓGÉPekre is telepíthető.

> [!div class="nextstepaction"]
> [Gyors útmutató: Unity-minta üzembe helyezése az asztalra](deploy-to-desktop.md)