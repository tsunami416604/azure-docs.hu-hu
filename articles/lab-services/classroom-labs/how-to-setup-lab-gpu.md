---
title: Tesztkörnyezet beállítása GPU-k Azure Lab Servicesban | Microsoft Docs
description: Ismerje meg, hogyan állíthat be egy labort GPU-val (grafikus feldolgozási egység) virtuális gépekkel.
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
ms.openlocfilehash: 9f4bc77366aaeaef9c66467e540219763228d317
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450772"
---
# <a name="set-up-a-lab-with-gpu-graphics-processing-unit-virtual-machine-size"></a>Tesztkörnyezet beállítása GPU-val (grafikus feldolgozó egység) virtuális gép mérete
Ez a cikk bemutatja, hogyan végezheti el a következő feladatokat:

- A virtualizáció és a számítási GPU-k közötti választás
- Győződjön meg arról, hogy a megfelelő GPU-illesztőprogramok telepítve vannak
- RDP-beállítások konfigurálása GPU virtuális géphez való kapcsolódáshoz (VM)

## <a name="choose-between-virtualization-and-compute-gpu-sizes"></a>A virtualizáció és a számítási GPU-méretek közül választhat
A labor létrehozási varázsló első oldalán kiválaszthatja az osztályhoz szükséges **virtuális gépek méretét** .  

![Virtuális gép méretének kiválasztása](../media/how-to-setup-gpu/lab-gpu-selection.png)

Itt választhatja ki a **vizualizációk** és a **számítási** GPU-k közötti választás lehetőségét.  Fontos, hogy a tanulók által használt szoftver alapján kiválassza a megfelelő típusú GPU-t.  

Az alábbi táblázatban leírtaknak megfelelően a **számítási** GPU mérete nagy számítási igényű alkalmazások számára készült.  A [természetes nyelvi feldolgozó osztály mély tanulása](./class-type-deep-learning-natural-language-processing.md) például a **kis GPU (számítási)** méretet mutatja be.  A **számítási** GPU alkalmas az ilyen típusú osztályokhoz, mivel a tanulók mély tanulási keretrendszerek és eszközök használatát a Data Science Virtual Machine- [rendszerkép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) által nyújtott mély tanulási modellek nagy adatkészletekkel való betanítására használják.

| Méret | Cores | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Kis GPU (számítás) | <ul><li>6 mag</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Ez a méret a nagy számítógépes igényű alkalmazások, például a mesterséges intelligencia és a Deep learning esetében ajánlott. |

A **vizualizációk** méretei a nagy grafikai igényű alkalmazások számára készültek.  A [SolidWorks mérnöki osztályának típusa](./class-type-solidworks.md) például a **kis GPU (vizualizáció)** méretet mutatja.  A **vizualizációs** GPU alkalmas az ilyen típusú osztályokhoz, mivel a tanulók a SolidWorks 3D számítógépes tervezési (CAD) környezetet használják a Solid Objects modellezéséhez és megjelenítéséhez.

| Méret | Cores | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Kis GPU (vizualizáció) | <ul><li>6 mag</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. |
| Közepes GPU (vizualizáció) | <ul><li>12 mag</li><li>112 GB RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. |

## <a name="ensure-the-appropriate-gpu-drivers-are-installed"></a>Győződjön meg arról, hogy a megfelelő GPU-illesztőprogramok telepítve vannak
A labor virtuális gépek GPU-képességeinek kihasználásához győződjön meg arról, hogy a megfelelő GPU-illesztőprogramok telepítve vannak.  A GPU virtuális gépek méretének kiválasztásakor a labor létrehozása varázslóban lehetőség van a GPU- **illesztőprogramok telepítésére**.  

![GPU-illesztőprogramok telepítése](../media/how-to-setup-gpu/lab-gpu-drivers.png)

Ez a beállítás alapértelmezés szerint **engedélyezve** van, és biztosítja, hogy a *legújabb* illesztőprogramok telepítve legyenek a kiválasztott GPU-hoz és lemezképhez:
- A **számítási** GPU méretének kiválasztásakor a labor virtuális gépeket az [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU látja el.  Ebben az esetben a legújabb [CUDA](https://www.nvidia.com/object/io_69526.html) -illesztőprogramok telepítve vannak a nagy teljesítményű számítástechnikai megoldások engedélyezéséhez.
- Ha kijelöl egy **vizualizációs** GPU-méretet, a labor virtuális gépeket az [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU és a [Grid technológia](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)látja el.  Ebben az esetben a legújabb GRID-illesztőprogramok vannak telepítve a grafikus igényű alkalmazások engedélyezéséhez.

> [!IMPORTANT]
> Ahhoz, hogy a legjobb felhasználói élményt biztosítsa a **vizualizációs** GPU-k számára, gondoskodnia kell arról, hogy *mindkét* illesztőprogram telepítve legyen, *és* a GPU engedélyezve legyen RDP-kapcsolaton keresztül.  Tekintse meg a " [GPU engedélyezése RDP-kapcsolaton keresztül a Windows rendszerű virtuális gépekhez](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms)" című szakaszban ismertetett lépéseket.

### <a name="manually-install-drivers"></a>Illesztőprogramok manuális telepítése
Előfordulhat, hogy olyan forgatókönyvek vannak, amelyeken az illesztőprogramok egy másik verzióját kell telepíteni, mint a legújabb verziót.  Az ebben a szakaszban ismertetett lépések bemutatják, hogyan telepítheti manuálisan a megfelelő illesztőprogramokat attól függően, hogy **számítási** vagy **vizualizációs** GPU-t használ.

#### <a name="compute-gpu-drivers"></a>Számítási GPU-illesztőprogramok
Az alábbi lépéseket követve manuálisan telepítheti az illesztőprogramokat a **számítási** GPU méretéhez:
1. A [labor létrehozásakor](./how-to-manage-classroom-labs.md) **Tiltsa le** a **GPU-illesztőprogramok telepítése** beállítást a labor létrehozási varázslóban.
1. A tesztkörnyezet létrehozása után kapcsolódjon a sablon virtuális géphez a megfelelő illesztőprogramok telepítéséhez.
   ![NVIDIA-illesztőprogramok letöltése](../media/how-to-setup-gpu/nvidia-driver-download.png) 
   1. A böngészőben nyissa meg az [NVIDIA illesztőprogram-letöltési lapját](https://www.nvidia.com/Download/index.aspx).
   2. Adja meg a **termék típusát** a **Tesla**értékre.
   3. A **termék sorozatát** állítsa a **K sorozatra**.
   4. Állítsa be a **terméket** a **Tesla K80**.
   5. Állítsa be az **operációs rendszert** a tesztkörnyezet létrehozásakor kiválasztott alaprendszerkép típusának megfelelően.
   6. Állítsa be a **CUDA-eszközkészletet** a szükséges CUDA-illesztőprogramok verziójára.
   7. Az illesztőprogramok megkereséséhez kattintson a **Keresés** gombra.
   8. A telepítő letöltéséhez kattintson a **Letöltés** gombra.
   9. Futtassa a telepítőt, hogy az illesztőprogramok telepítve legyenek a sablon virtuális gépre.  
2. Ellenőrizze, hogy az illesztőprogramok megfelelően vannak-e telepítve. Ehhez kövesse a [telepített illesztőprogramok ellenőrzése](how-to-setup-lab-gpu.md#validate-installed-drivers)nevű szakaszban leírt lépéseket. 
3. Miután telepítette az adott osztályhoz szükséges illesztőprogramokat és egyéb szoftvereket, a **Közzététel** lehetőségre kattintva hozhatja létre a tanulói virtuális gépeket.

> [!NOTE]
> Ha Linux-rendszerképet használ, a következő lépésekkel telepítheti az illesztőprogramokat a telepítő letöltése után: [telepítse a CUDA-illesztőprogramokat Linux rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="visualization-gpu-drivers"></a>Vizualizációs GPU-illesztőprogramok
Kövesse az alábbi lépéseket a **vizualizációs** GPU-méretekhez tartozó illesztőprogramok manuális telepítéséhez:
1. A [labor létrehozásakor](./how-to-manage-classroom-labs.md) **Tiltsa le** a **GPU-illesztőprogramok telepítése** beállítást a labor létrehozási varázslóban.
1. A tesztkörnyezet létrehozása után kapcsolódjon a sablon virtuális géphez a megfelelő illesztőprogramok telepítéséhez.
1. Telepítse a Microsoft által a sablon virtuális gépen biztosított GRID-illesztőprogramokat a következő lépésekkel:
   -  [Windows NVIDIA GRID-illesztőprogramok](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID-illesztőprogramok](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Indítsa újra a sablon virtuális gépet.
1. Ellenőrizze, hogy az illesztőprogramok megfelelően vannak-e telepítve. Ehhez kövesse a [telepített illesztőprogramok ellenőrzése](how-to-setup-lab-gpu.md#validate-installed-drivers)nevű szakaszban leírt lépéseket.
1. Az RDP-beállítások konfigurálásával engedélyezze a GPU-t a [Windows rendszerű virtuális gépekre vonatkozó, a GPU-t RDP-kapcsolaton keresztül](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).
1. Miután telepítette az adott osztályhoz szükséges illesztőprogramokat és egyéb szoftvereket, a **Közzététel** lehetőségre kattintva hozhatja létre a tanulói virtuális gépeket.

### <a name="validate-installed-drivers"></a>Telepített illesztőprogramok ellenőrzése
Az ebben a szakaszban ismertetett lépések azt írják le, hogyan ellenőrizhető, hogy a GPU-illesztőprogramok megfelelően vannak-e telepítve.

#### <a name="windows-images"></a>Windows-rendszerképek
1.  Kövesse a cikkben ismertetett lépéseket, amely bemutatja, hogyan [ellenőrizheti az illesztőprogram telepítését Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)rendszeren.
1.  Ha **vizualizációs** GPU-t használ, a következőket is elvégezheti:
    - A GPU-beállítások megtekintéséhez és módosításához nyissa meg az **NVIDIA Vezérlőpultot** .  A beállítások eléréséhez nyissa meg a **vezérlőpult > hardver** elemét, és válassza az **NVIDIA Vezérlőpultot**.
      ![Az NVIDIA Vezérlőpult megnyitása](../media/how-to-setup-gpu/control-panel-nvidia-settings.png) 
     - A GPU teljesítményének megtekintése a **Feladatkezelő**használatával.  A megtekintéséhez nyissa meg a **Feladatkezelőt** , és válassza a **teljesítmény** lapot. ![ Feladatkezelő – GPU-teljesítmény](../media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Az **NVIDIA Vezérlőpult** beállításai csak a **vizualizációs** GPU-k esetében érhetők el.  Ha megpróbál megnyitni egy **számítási** GPU számára az **NVIDIA Vezérlőpultot** , a következő hibaüzenet jelenik meg: az **NVIDIA megjelenítési beállításai nem érhetők el.  Jelenleg nem használ egy NVIDIA GPU-hoz csatlakoztatott kijelzőt.**  Ehhez hasonlóan a **FELADATKEZELŐ** GPU-teljesítményével kapcsolatos információk csak a **vizualizációs** GPU-k számára vannak megadva.

#### <a name="linux-images"></a>Linux-rendszerképek
Kövesse a cikkben ismertetett lépéseket, amely bemutatja, hogyan [ellenőrizheti az illesztőprogram telepítését Linux rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>A GPU engedélyezése RDP-kapcsolaton keresztül a Windows rendszerű virtuális gépeken
Ha RDP (Remote Desktop Protocol) használatával csatlakozik egy **vizualizációs** GPU-t futtató Windows rendszerű virtuális géphez, további konfigurációt kell végrehajtania, hogy a GPU a grafikus megjelenítéshez legyen használva (MÁSKÜLÖNBEN a CPU-t fogja használni).

Az alábbi lépéseket a sablon virtuális gépen kell végrehajtani.

1. Először konfigurálja az RDP-beállításokat a GPU használatával:

   1. Kövesse a cikkben ismertetett lépéseket, amely bemutatja, hogyan [konfigurálhatja a GPU-gyorsított alkalmazások megjelenítését](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering).

   2. Kövesse a cikkben ismertetett lépéseket, amely bemutatja, hogyan [konfigurálható a GPU-gyorsított keret kódolása](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding).

2. Ezután ellenőrizze a konfigurációt: 

   1. Kövesse a cikkben ismertetett lépéseket, amely bemutatja, hogyan [ellenőrizheti a GPU-gyorsított alkalmazások megjelenítését](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering).

   2.  Kövesse a cikkben ismertetett lépéseket, amely bemutatja, hogyan [ellenőrizhető a GPU-gyorsított keret kódolása](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

3. Végül már telepítette az illesztőprogramokat, és a GPU használatára konfigurált RDP-beállításokkal rendelkezik.  Miután telepítette az osztályhoz szükséges egyéb szoftvereket, a **Közzététel** gombra kattintva létrehozhatja a tanulói virtuális gépeket.  Ha a tanulók RDP-kapcsolaton keresztül csatlakoznak a virtuális gépekhez, az asztaluk a virtuális gép GPU-je alapján fog megjelenni.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Osztálytermi tesztkörnyezetek létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [SolidWorks – számítógéppel segített tervezési (CAD) osztály típusa](class-type-solidworks.md)
- [MATLAB-osztály típusa](class-type-matlab.md)



