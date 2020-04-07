---
title: Modell renderelése unityvel
description: Rövid útmutató, amely végigvezeti a felhasználót a modell megjelenítésének lépésein
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b0af45ba4a6b1ca7f9e751af082ff0db80776ec0
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679785"
---
# <a name="quickstart-render-a-model-with-unity"></a>Rövid útmutató: Modell renderelése unityvel

Ez a rövid útmutató ismerteti, hogyan futtatja a Unity-minta, amely egy beépített modell távolról, az Azure Remote Rendering (ARR) szolgáltatás használatával.

Nem fogunk részletekbe bocsátkozni magáról az ARR API-ról vagy egy új Unity projekt beállításáról. Ezeket a témákat a [Tutorial: Setting up a Unity projekt a semmiből](../tutorials/unity/project-setup.md).

Ebben a rövid útmutatóban megtudhatja, hogyan:
> [!div class="checklist"]
>
>* A helyi fejlesztési környezet beállítása
>* Az ARR rövid útmutató mintaalkalmazásának beszerezni és megépíteni a Unity számára
>* Modell renderelése az ARR rövid útmutató mintaalkalmazásban

## <a name="prerequisites"></a>Előfeltételek

Az Azure távoli renderelési szolgáltatáshoz való hozzáféréshez először létre kell [hoznia egy fiókot.](../how-tos/create-an-account.md)

A következő szoftvereket kell telepíteni:

* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A Visual Studio 2019 legújabb verziója [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(letöltés)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(letöltés)](https://unity3d.com/get-unity/download)
  * Telepítse ezeket a modulokat unity:
    * **UWP** – Univerzális Windows-platformbuild-támogatás
    * **IL2CPP** – Windows buildtámogatás (IL2CPP)

## <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Nyisson meg egy `cmd` parancssort (írja be a Windows start menüjét), és váltson olyan könyvtárra, amelyen az ARR mintaprojektet tárolni szeretné.

Futtassa az alábbi parancsot:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Az utolsó parancs létrehoz egy alkönyvtárat az ARR könyvtárban, amely tartalmazza az Azure távoli rendereléskülönböző mintaprojektjeit.

A Unity rövid útmutató mintaalkalmazása az *Unity/Quickstart*alkönyvtárban található.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Modell renderelése az Egység mintaprojekttel

Nyissa meg a Unity Hubot, és adja hozzá a mintaprojektet, amely az *ARR\azure-remote-rendering\Unity\Quickstart* mappa.
Nyissa meg a projektet. Ha szükséges, engedélyezze a Unity számára, hogy frissítse a projektet a telepített verzióra.

Az alapértelmezett modell, amelyet renderelünk, egy [beépített mintamodell.](../samples/sample-model.md) Bemutatjuk, hogyan konvertálhatja az egyéni modellt az ARR konverziós szolgáltatás használatával a [következő rövid útmutatóban.](convert-model.md)

### <a name="enter-your-account-info"></a>Adja meg a fiók adatait

1. A Unity eszközböngészőben keresse meg a *Jelenetek mappát,* és nyissa meg a **Rövid útmutató** jelenetet.
1. A *Hierarchiából*jelölje ki a **RemoteRendering** játékobjektumot.
1. A *Felügyelő*mezőbe írja be a [fiók hitelesítő adatait.](../how-tos/create-an-account.md)

![ARR-fiók adatai](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Az Azure Portal csak *mixedreality.azure.com*jeleníti meg a fiók tartományát. Ez nem elegendő a sikeres csatlakozáshoz.
> Állítsa **a AccountDomain (AccountDomain) (Fióktartomány)** beállítását, `<region>.mixedreality.azure.com`ahol `<region>` az Ön [közelében lévő egyik elérhető terület](../reference/regions.md)található.

Később szeretnénk telepíteni ezt a projektet egy HoloLensre, és csatlakozni a távoli renderelési szolgáltatáshoz az adott eszközről. Mivel nincs egyszerű módja annak, hogy adja meg a hitelesítő adatokat az eszközön, a rövid útmutató minta **menti a hitelesítő adatokat a Unity jelenet**.

> [!WARNING]
> Győződjön meg róla, hogy ne ellenőrizze a projektet a mentett hitelesítő adatokat egy adattárba, ahol titkos bejelentkezési adatokat szivárogtatna ki!

### <a name="create-a-session-and-view-the-default-model"></a>Munkamenet létrehozása és az alapértelmezett modell megtekintése

A munkamenet elindításához nyomja meg a Unity **Lejátszás** gombját. A *Játék* panelen a nézetablak alján egy állapotszöveget tartalmazó átfedésnek kell látható. A munkamenet egy sor állapotátmeneten megy keresztül. A **kezdő** állapotban a távoli virtuális gép felvan pörgetve, ami több percet vesz igénybe. A siker után átkerül a **Ready** állapotba. Most a munkamenet belép a **csatlakozási** állapotba, ahol megpróbálja elérni a renderelési futásidejű, hogy a virtuális gép. Ha sikeres, a minta átvált a **Csatlakoztatott állapotba.** Ezen a ponton elkezdi letölteni a modellt renderelésre. A modell mérete miatt a letöltés még néhány percet vehet igénybe. Ezután megjelenik a távolról renderelt modell.

![A mintából származó kimenet](media/arr-sample-output.png)

Gratulálunk! Ön most megtekinti a távolról renderelt modell!

## <a name="inspecting-the-scene"></a>A helyszín vizsgálata

A távoli renderelési kapcsolat futását követően a Felügyelő panel további állapotinformációkat tartalmaz:

![Unity minta lejátszása](./media/arr-sample-configure-session-running.png)

Most már felfedezheti a jelenetgrafikonot az új csomópont kiválasztásával és a **Gyermekek megjelenítése** a felügyelőben elemre kattintva.

![Egységhierarchia](./media/unity-hierarchy.png)

Van egy [vágás sík](../overview/features/cut-planes.md) tárgy a jelenetben. Próbálja meg engedélyezni a tulajdonságait, és mozgassa azt:

![A vágási sík megváltoztatása](media/arr-sample-unity-cutplane.png)

Az átalakítások szinkronizálásához kattintson a **Szinkronizálás most** gombra, vagy jelölje be az **Összes képkocka szinkronizálása** jelölőnégyzetet. Az összetevők tulajdonságaihoz elegendő, ha csak módosítja őket.

## <a name="next-steps"></a>További lépések

A következő rövid útmutatóban a mintát egy HoloLensre telepítjük, hogy megtekinthesse a távolról renderelt modellt az eredeti méretében.

> [!div class="nextstepaction"]
> [Rövid útmutató: Unity-minta telepítése a HoloLensen](deploy-to-hololens.md)

Másik lehetőségként a minta asztali számítógépre is telepíthető.

> [!div class="nextstepaction"]
> [Rövid útmutató: Unity-minta telepítése az asztalra](deploy-to-desktop.md)