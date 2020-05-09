---
title: A linuxos virtuális gépek Azure-beli üzembe helyezésével kapcsolatos problémák elhárítása | Microsoft Docs
description: A Linux rendszerű virtuális gépek problémáinak üzembe helyezésével kapcsolatos hibák elhárítása a Azure Resource Manager üzemi modellben.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: cf8fb383625ec2752264d6e5a70d8625f06689fc
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628299"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Linux rendszerű virtuális gépek Azure-beli üzembe helyezése során előforduló problémák elhárítása

A virtuális gépek üzembe helyezésével kapcsolatos problémák elhárításához az Azure-ban tekintse át a leggyakoribb hibák és megoldások [legfontosabb problémáit](#top-issues) .

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.

## <a name="top-issues"></a>Leggyakoribb problémák
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>A fürt nem támogatja a kért VM-méretet
\<Properties supportTopicIds = "123456789" resourceTags = "Windows" productPesIds = "1234, 5678"/>
- Próbálja megismételni a kérést kisebb virtuálisgép-méret használatával.
- Ha a kért virtuális gép mérete nem módosítható:
    - Állítsa le az összes virtuális gépet a rendelkezésre állási csoportból. Kattintson **Az erőforráscsoportok >** az erőforráscsoport > **erőforrások** > a rendelkezésre állási csoport > **Virtual Machines** a virtuális gép > **leállítása**elemre.
    - Az összes virtuális gép leállítása után a kívánt méretben hozza létre a virtuális gépet.
    - Először indítsa el az új virtuális gépet, majd válassza ki a leállított virtuális gépeket, és kattintson az Indítás gombra.


## <a name="the-cluster-does-not-have-free-resources"></a>A fürt nem rendelkezik szabad erőforrásokkal
\<Properties supportTopicIds = "123456789" resourceTags = "Windows" productPesIds = "1234, 5678"/>
- Később próbálja megismételni a kérést.
- Ha az új virtuális gép egy másik rendelkezésre állási csoport része lehet
    - Hozzon létre egy virtuális gépet egy másik rendelkezésre állási készletben (ugyanabban a régióban).
    - Adja hozzá az új virtuális gépet ugyanahhoz a virtuális hálózathoz.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>A havi kreditek Hogyan aktiválása a Visual Studio Enterprise (BizSpark) szolgáltatáshoz

A havi kreditek aktiválásához tekintse meg ezt a [cikket](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Miért nem lehet telepíteni a GPU-illesztőprogramot Ubuntu NV virtuális géphez?

A Linux GPU-támogatás jelenleg csak az Ubuntu Server 16,04 LTS rendszert futtató Azure NC virtuális gépeken érhető el. További információ: [GPU-illesztőprogramok beállítása a Linuxon futó N sorozatú virtuális gépekhez](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>A Linux N sorozatú virtuális gépek illesztőprogramjai hiányoznak

A Linux-alapú virtuális gépek illesztőprogramjainak telepítésére vonatkozó utasítások [itt](../sizes-gpu.md#supported-operating-systems-and-drivers)találhatók.

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nem találom az N sorozatú virtuális gépen található GPU-példányt

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához az üzembe helyezést követően minden virtuális gépen telepítenie kell a grafikus illesztőprogramokat. Az illesztőprogram beállítási információi [itt](../sizes-gpu.md#supported-operating-systems-and-drivers)érhetők el.

## <a name="are-n-series-vms-available-in-my-region"></a>Az N sorozatú virtuális gépek elérhetők a saját régióban?

A rendelkezésre álló termékek elérhetőségét a [régiók táblázata alapján](https://azure.microsoft.com/regions/services), a díjszabást pedig [itt](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)tekintheti meg.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nem látom a virtuális gép átméretezése során használni kívánt virtuálisgép-méretet.

Ha egy virtuális gép fut, a rendszer egy fizikai kiszolgálóra telepíti. Az Azure-régiókban található fizikai kiszolgálók általános fizikai hardverek csoportjaiba vannak csoportosítva. A virtuális gép különböző hardveres fürtökre való áthelyezését igénylő virtuális gépek átméretezése eltérő lehet attól függően, hogy melyik üzemi modellt használták a virtuális gép üzembe helyezéséhez.

- A klasszikus üzemi modellben üzembe helyezett virtuális gépeket el kell távolítani és újra kell telepíteni, hogy a virtuális gépeket egy másik méretű családban lévő méretre módosítsa.

- A Resource Manager-alapú üzemi modellben üzembe helyezett virtuális gépeket a rendelkezésre állási csoportba tartozó virtuális gépek méretének módosítása előtt le kell állítania a rendelkezésre állási csoport összes virtuális gépén.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>A felsorolt virtuálisgép-méret nem támogatott a rendelkezésre állási csoport telepítésekor.

Válassza ki a rendelkezésre állási csoport fürtjét támogató méretet. Azt javasoljuk, hogy egy rendelkezésre állási csoport létrehozásakor válassza ki a szükséges legnagyobb méretű virtuálisgép-méretet, és hogy az első üzembe helyezése legyen a rendelkezésre állási csoportnak.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Milyen Linux-disztribúciók/verziók támogatottak az Azure-ban?

A Linuxon megtalálhatja az [Azure által támogatott disztribúciók](../linux/endorsed-distros.md)listáját.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Felvehetek meglévő klasszikus virtuális gépet egy rendelkezésre állási csoportba?

Igen. Meglévő klasszikus virtuális gépet hozzáadhat egy új vagy meglévő rendelkezésre állási csoporthoz. További információ: [meglévő virtuális gép hozzáadása egy rendelkezésre állási csoporthoz](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>További lépések
Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is.

Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
