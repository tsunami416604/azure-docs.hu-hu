---
title: Tesztkörnyezet beállítása GPU-k Azure Lab Servicesban | Microsoft Docs
description: Ismerje meg, hogyan állíthat be labort grafikus processzorral (GPU) rendelkező virtuális gépekkel.
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2020
ms.author: nicolela
ms.openlocfilehash: ebc3d34e23c740bc6307e26a2ac0308062367119
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84628907"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Tesztkörnyezet beállítása GPU virtuális gépekkel

Ez a cikk bemutatja, hogyan végezheti el a következő feladatokat:

- Válasszon a *vizualizációk* és a *számítási* grafikus processzorok (GPU-k) közül.
- Győződjön meg arról, hogy a megfelelő GPU-illesztőprogramok telepítve vannak.
- Konfigurálja RDP protokoll (RDP) beállításait egy GPU virtuális géphez (VM) való kapcsolódáshoz.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Választás a vizualizációk és a számítási GPU-méretek között
A labor létrehozási varázsló első lapján, amelyben a virtuálisgép- **méretre van szüksége?** legördülő listában válassza ki az osztályhoz szükséges virtuális gépek méretét.  

![A virtuális gép méretének kiválasztására szolgáló "új Labor" panel képernyőképe](../media/how-to-setup-gpu/lab-gpu-selection.png)

Ebben a folyamatban lehetősége van a **vizualizációk** vagy a **számítási** GPU-k kiválasztására.  Fontos, hogy kiválassza a tanulók által használt szoftveren alapuló GPU típusát.  

Az alábbi táblázatban leírtak szerint a *számítási* GPU-méret nagy számítási igényű alkalmazások számára készült.  A [természetes nyelvi feldolgozási osztály mély tanulása](./class-type-deep-learning-natural-language-processing.md) például a **kis GPU (számítási)** méretet használja.  A számítási GPU megfelelő az ilyen típusú osztályok számára, mivel a tanulók olyan mély tanulási keretrendszerek és eszközök használatát használják, amelyeket az [Data Science Virtual Machine-rendszerkép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) biztosít a nagy adatkészletekkel rendelkező mély tanulási modellek betanításához.

| Méret | Cores | RAM | Leírás | 
| ---- | ----- | --- | ----------- | 
| Kis GPU (számítás) | -&nbsp;6 &nbsp; mag<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Ez a méret a legmegfelelőbb a számítógép-igényes alkalmazások, például a mesterséges intelligencia (AI) és a Deep learning számára. |

A *vizualizációs* GPU-méretek a nagy grafikai igényű alkalmazások számára készültek.  A [SolidWorks mérnöki osztályának típusa](./class-type-solidworks.md) például a **kis GPU (vizualizáció)** méretet mutatja.  A vizualizációs GPU alkalmas az ilyen típusú osztályok számára, mivel a tanulók a SolidWorks 3D számítógépes tervezési (CAD) környezetet használják a Solid Objects modellezéséhez és megjelenítéséhez.

| Méret | Cores | RAM | Leírás | 
| ---- | ----- | --- | ----------- | 
| Kis GPU (vizualizáció) | -&nbsp;6 &nbsp; mag<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Ez a méret a távoli vizualizációk, a folyamatos átvitel, a játékok és az OpenGL-t és a DirectX-t használó keretrendszereket használó kódoláshoz ideális. |
| Közepes GPU (vizualizáció) | -&nbsp;12 &nbsp; mag<br>-&nbsp;112 &nbsp; GB &nbsp; RAM  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ez a méret a távoli vizualizációk, a folyamatos átvitel, a játékok és az OpenGL-t és a DirectX-t használó keretrendszereket használó kódoláshoz ideális. |

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Győződjön meg arról, hogy a megfelelő GPU-illesztőprogramok telepítve vannak
A laboratóriumi virtuális gépek GPU-képességeinek kihasználásához győződjön meg arról, hogy a megfelelő GPU-illesztőprogramok telepítve vannak.  A tesztkörnyezet létrehozása varázslóban a GPU-s virtuális gép méretének kiválasztásakor válassza a **GPU-illesztőprogramok telepítése** lehetőséget.  

![Képernyőkép az "új Laborról", amely a "GPU-illesztőprogramok telepítése" lehetőséget mutatja](../media/how-to-setup-gpu/lab-gpu-drivers.png)

Ahogy az előző képen is látható, ez a beállítás alapértelmezés szerint engedélyezve van, amely biztosítja, hogy a *legújabb* illesztőprogramok telepítve legyenek a kiválasztott GPU-hoz és lemezképhez.
- A *számítási* GPU méretének kiválasztásakor a labor virtuális gépeket az [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU látja el.  Ebben az esetben a rendszer telepíti a legújabb [számítási egységesített architektúra-(CUDA-)](https://www.nvidia.com/object/io_69526.html) illesztőprogramokat, ami lehetővé teszi a nagy teljesítményű számítástechnikai eszközök használatát.
- Ha kijelöl egy *vizualizációs* GPU-méretet, a labor virtuális gépeket az [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU és a [Grid technológia](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)látja el.  Ebben az esetben a legújabb GRID-illesztőprogramok települnek, ami lehetővé teszi a nagy teljesítményű alkalmazások használatát.

> [!IMPORTANT]
> Ahhoz, hogy a legjobb felhasználói élmény legyen a *vizualizáció* GPU-k számára, győződjön meg arról, hogy *mindkét* illesztőprogram telepítve van, *és* a GPU engedélyezve van az RDP-kapcsolatokon keresztül. További információ: a [GPU engedélyezése RDP-kapcsolaton keresztül a Windows rendszerű virtuális gépekhez](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) című rész ebben a cikkben.

### <a name="install-the-drivers-manually"></a>Az illesztőprogramok manuális telepítése
Előfordulhat, hogy a legújabb verziótól eltérő illesztőprogram-verziót kell telepítenie.  Ebből a szakaszból megtudhatja, hogyan telepítheti manuálisan a megfelelő illesztőprogramokat attól függően, hogy *számítási* GPU-t vagy *vizualizációs* GPU-t használ.

#### <a name="install-the-compute-gpu-drivers"></a>A számítási GPU-illesztőprogramok telepítése

Ha manuálisan szeretné telepíteni az illesztőprogramokat a számítási GPU-mérethez, tegye a következőket:

1. A labor létrehozási varázslóban a [tesztkörnyezet létrehozásakor](./how-to-manage-classroom-labs.md)tiltsa le a **GPU-illesztőprogramok telepítése** beállítást.

1. A tesztkörnyezet létrehozása után kapcsolódjon a sablon virtuális géphez a megfelelő illesztőprogramok telepítéséhez.

   ![Az NVIDIA-illesztőprogram letöltési oldalának képernyőképe](../media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. A böngészőben nyissa meg az [NVIDIA-illesztőprogram letöltése lapot](https://www.nvidia.com/Download/index.aspx).  
   b. Adja meg a **termék típusát** a **Tesla**értékre.  
   c. A **termék sorozatát** állítsa a **K sorozatra**.  
   d. Állítsa be az **operációs rendszert** a tesztkörnyezet létrehozásakor kiválasztott alaprendszerkép típusának megfelelően.  
   e. Állítsa be a **CUDA-eszközkészletet** a szükséges CUDA-illesztőprogram verziójára.  
   f. Kattintson a **Keresés** gombra az illesztőprogramok kereséséhez.  
   : A telepítő letöltéséhez kattintson a **Letöltés** gombra.  
   h. Futtassa a telepítőt, hogy az illesztőprogramok telepítve legyenek a sablon virtuális gépre.  
1. Ellenőrizze, hogy az illesztőprogramok megfelelően vannak-e telepítve. Ehhez kövesse a [telepített illesztőprogramok ellenőrzése](how-to-setup-lab-gpu.md#validate-the-installed-drivers) szakasz utasításait. 
1. Miután telepítette az adott osztályhoz szükséges illesztőprogramokat és más szoftvereket, válassza a **Közzététel** lehetőséget a tanulói virtuális gépek létrehozásához.

> [!NOTE]
> Ha Linux-rendszerképet használ, a telepítő letöltése után telepítse az illesztőprogramokat a következő témakörben ismertetett utasítások alapján: a [CUDA-illesztőprogramok telepítése Linux rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="install-the-visualization-gpu-drivers"></a>A vizualizációs GPU-illesztőprogramok telepítése

Ha manuálisan szeretné telepíteni az illesztőprogramokat a vizualizáció GPU-méretéhez, tegye a következőket:

1. A labor létrehozási varázslóban a [tesztkörnyezet létrehozásakor](./how-to-manage-classroom-labs.md)tiltsa le a **GPU-illesztőprogramok telepítése** beállítást.
1. A tesztkörnyezet létrehozása után kapcsolódjon a sablon virtuális géphez a megfelelő illesztőprogramok telepítéséhez.
1. Telepítse a Microsoft által a sablon virtuális gépen biztosított GRID-illesztőprogramokat az operációs rendszerére vonatkozó utasításokat követve:
   -  [Windows NVIDIA GRID-illesztőprogramok](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID-illesztőprogramok](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Indítsa újra a sablon virtuális gépet.
1. Ellenőrizze, hogy az illesztőprogramok megfelelően vannak-e telepítve. Ehhez kövesse a [telepített illesztőprogramok ellenőrzése](how-to-setup-lab-gpu.md#validate-the-installed-drivers) szakasz utasításait.
1. Konfigurálja az RDP-beállításokat a GPU-kapcsolat engedélyezéséhez a [GPU engedélyezése RDP-kapcsolaton keresztül a Windows rendszerű virtuális gépekre](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) című témakör utasításait követve.
1. Miután telepítette az adott osztályhoz szükséges illesztőprogramokat és más szoftvereket, válassza a **Közzététel** lehetőséget a tanulói virtuális gépek létrehozásához.

### <a name="validate-the-installed-drivers"></a>A telepített illesztőprogramok ellenőrzése
Ez a szakasz azt ismerteti, hogyan ellenőrizhető, hogy a GPU-illesztőprogramok megfelelően vannak-e telepítve.

#### <a name="windows-images"></a>Windows-rendszerképek
1.  Kövesse az [NVIDIA GPU-illesztőprogramok telepítése a Windows rendszerű N sorozatú virtuális gépekre](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)című témakör "az illesztőprogramok telepítésének ellenőrzése" című szakaszának utasításait.
1.  Ha *vizualizációs* GPU-t használ, a következőket is elvégezheti:
    - Az NVIDIA Vezérlőpulton tekintheti meg és módosíthatja a GPU-beállításokat. Ehhez a **Windows Vezérlőpultján**válassza a **hardver**lehetőséget, majd kattintson az **NVIDIA Vezérlőpult**elemre.

      ![Képernyőkép a Windows Vezérlőpultján, amely az NVIDIA Vezérlőpult hivatkozását mutatja](../media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Tekintse meg a GPU teljesítményét a **Feladatkezelő**használatával.  Ehhez válassza a **teljesítmény** fület, majd válassza a **GPU** lehetőséget.

       ![A Feladatkezelő GPU-teljesítmény lapját ábrázoló képernyőfelvétel](../media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Az NVIDIA Vezérlőpult beállításai csak a *vizualizációs* GPU-k számára érhetők el.  Ha megpróbál megnyitni egy számítási GPU-hoz készült NVIDIA Vezérlőpultot, a következő hibaüzenet jelenik meg: "az NVIDIA megjelenítési beállításai nem érhetők el.  Jelenleg nem használja az NVIDIA GPU-hoz csatolt kijelzőt. "  Ehhez hasonlóan a Feladatkezelő GPU-teljesítményadatokat csak a vizualizációs GPU-k számára biztosítjuk.

#### <a name="linux-images"></a>Linux-rendszerképek
Kövesse az [NVIDIA GPU-illesztőprogramok telepítése a Linux rendszerű N sorozatú virtuális gépekre](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation)című témakör "az illesztőprogramok telepítésének ellenőrzése" című szakaszának utasításait.

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>A GPU engedélyezése RDP-kapcsolaton keresztül a Windows rendszerű virtuális gépeken
Amikor RDP használatával csatlakozik egy *vizualizációs* GPU-val rendelkező Windows rendszerű virtuális géphez, további konfigurációt kell elvégeznie, hogy a GPU-t a grafikák renderelésére használják. Ellenkező esetben a rendszer a PROCESSZORt használja a grafikák megjelenítéséhez.

A sablon virtuális gépen tegye a következőket:

1. Konfigurálja az RDP-beállításokat a GPU használatára.

   a. Kövesse a [GPU-gyorsított alkalmazások renderelésének beállítása](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering)című témakör utasításait.  
   b. Kövesse a [GPU-gyorsított keret kódolásának beállítása](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding)című témakör utasításait.

1. Ellenőrizze a konfigurációt. 

   a. Kövesse a [GPU-gyorsított alkalmazások megjelenítésének ellenőrzése](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering)című témakör utasításait.  
   b. Kövesse a [GPU-gyorsított keret kódolásának ellenőrzése](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding)című témakör utasításait.

1. Most már telepítette az illesztőprogramokat és a GPU használatára konfigurált RDP-beállításokat.  Miután telepítette az osztályhoz szükséges egyéb szoftvereket, a **Közzététel** lehetőségre kattintva hozhatja létre a tanulói virtuális gépeket.  

Ha a tanulók RDP-kapcsolaton keresztül csatlakoznak a virtuális gépekhez, az asztali számítógépek a virtuális gép GPU-je szerint jelennek meg.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Osztálytermi tesztkörnyezetek létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [SolidWorks – számítógéppel segített tervezési (CAD) osztály típusa](class-type-solidworks.md)
- [MATLAB (mátrix laboratórium) osztály típusa](class-type-matlab.md)



