---
title: "Problémamegoldás telepítését Linux virtuális gép az Azure-ban |} Microsoft Docs"
description: "Problémamegoldás telepítését Linux virtuális gép Azurethe Resource Manager üzembe helyezési modellben."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 3d0e9a43d9a3c9c770c4ab6159279db1d93f5134
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Problémamegoldás telepítését Linux virtuális gép az Azure-ban

Az Azure virtuális gép (VM) telepítési problémák megoldásához tekintse át a [leggyakoribb problémák](#top-issues) a gyakori hibák és megoldására.

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.

## <a name="top-issues"></a>Problémák
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>A fürt nem támogatja a kért Virtuálisgép-méret
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Próbálja megismételni a kérelmet kisebb Virtuálisgép-méretet.
- Ha a kért virtuális gép mérete nem lehet módosítani:
    - Állítsa le a rendelkezésre állási csoport összes virtuális gépet. Kattintson a **erőforráscsoportok** > az erőforráscsoport > **erőforrások** > a rendelkezésre állási csoport > **virtuális gépek** > a virtuális gép > **leállítása**.
    - Állítsa le a virtuális gépeket, létre kell hoznia a virtuális gép a kívánt méretet.
    - Először indítsa el a új virtuális Gépet, majd válassza ki a leállított virtuális gépek mindegyikének, és válassza az Indítás parancsot.


## <a name="the-cluster-does-not-have-free-resources"></a>A fürt nem rendelkezik szabad erőforrást
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Később próbálkozzon újra a kéréssel.
- Ha az új virtuális Gépet egy másik rendelkezésre állási készlet része lehet.
    - Hozzon létre egy virtuális Gépet egy másik rendelkezésre állási csoport (ugyanabban a régióban).
    - Adja hozzá az új virtuális gép ugyanahhoz a virtuális hálózathoz.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hogyan aktiválhatja a Visual Studio Enterprise (BizSpark) a havi keretet

A havi kreditek aktiválásához megjelenik ez [cikk](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Miért nem telepíthetők a GPU illesztőprogramjának portok HV Ubuntu virtuális gép?

Linux GPU támogatja jelenleg csak Azure virtuális gépeken NC Ubuntu Server 16.04 LTS futtató érhető el. További információkért lásd: [N-sorozat linuxos virtuális gépek GPU illesztőprogramok beállítása](n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Az illesztőprogramok hiányoznak a Linux N sorozatú virtuális Gépemhez

Linux-alapú virtuális gépek illesztőprogramok találhatók [Itt](n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>A GPU példánya nem található a N sorozatú virtuális Gépen belül

Windows Server 2016 vagy a Windows Server 2012 R2 rendszert futtató Azure N sorozatú virtuális gépek GPU lehetőségeinek előnyeit, telepítenie kell NVIDIA video-illesztőprogramok minden egyes virtuális gépen a telepítés utáni. Illesztőprogram-telepítő információ [Windows virtuális gépek](../windows/n-series-driver-setup.md) és [Linux virtuális gépek](n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Érhető el N sorozatú virtuális gépek régiómban?

Ellenőrizheti a szinten rendelkezésre áll a [régió tábla által elérhető termékek](https://azure.microsoft.com/regions/services), és az árképzés terén [Itt](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nem tudom, hogy a virtuális gép átméretezésekor kívánt Virtuálisgép-méretet termékcsalád talál.

Ha egy virtuális gép fut, a fizikai kiszolgáló rendszer. A fizikai kiszolgálók Azure-régiók közös fizikai hardver fürtök vannak csoportosítva. A, amelyhez a virtuális gépek áthelyezése a másik hardverre fürtök a virtuális gépek átméretezésével eltér attól függően, hogy melyik telepítési modell használatával telepítse a virtuális Gépet.

- Klasszikus üzembe helyezési modellel, a felhő szolgáltatástelepítés telepített virtuális gépek kell eltávolítása és újratelepítése módosításához a virtuális gépeket egy másik mérete termékcsalád méretre.

- Erőforrás-kezelő üzembe helyezési modellben telepített virtuális gépek, le kell állítania minden virtuális gép rendelkezésre állási csoportban, a rendelkezésre állási csoport minden virtuális gép méretének módosítása előtt.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>A listán szereplő virtuális gép mérete nem támogatott rendelkezésre állási csoportban való telepítése közben.

A rendelkezésre állási csoport fürtön támogatott méret kiválasztása. Ha válassza ki a legnagyobb virtuális gép méretét úgy gondolja, hogy kell, és rendelkezik, amelyek az első telepített a rendelkezésre állási csoport létrehozása a rendelkezésre állási beállítása ajánlott.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Milyen Linux terjesztéseket/verziók támogatottak az Azure-on?

A listát a Linux található [Azure-Endorsed Terjesztéseket](endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Hozzáadható az létező klasszikus virtuális gép egy rendelkezésre állási csoportot?

Igen. Meglévő klasszikus virtuális adhat hozzá egy új vagy meglévő rendelkezésre állási csoportban. További információ: [hozzáadása egy meglévő virtuális gép rendelkezésre állási csoportok](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>További lépések
Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/).

Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.
