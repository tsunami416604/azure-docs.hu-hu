---
title: Az Azure-beli üzembe helyezése Linux rendszerű virtuális gép hibáinak elhárítása |} A Microsoft Docs
description: Azurethe Resource Manager-alapú üzemi modellben üzembe helyezése Linux rendszerű virtuális gép hibák elhárítása.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 45e15fa5b17f75dcc6cfdc7305861d38804f1266
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748136"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Az Azure-beli üzembe helyezése Linux rendszerű virtuális gép hibáinak elhárítása

Az Azure-beli virtuális gép (VM) üzembe helyezés hibáinak elhárítása, tekintse át a [leggyakoribb problémák](#top-issues) a gyakori hibák és megoldásaik.

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.

## <a name="top-issues"></a>Leggyakoribb problémák
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>A fürt nem támogatja a kért Virtuálisgép-méret
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Próbálja megismételni a kérést kisebb Virtuálisgép-mérettel.
- Ha a kért virtuális gép mérete nem lehet módosítani:
    - Állítsa le a rendelkezésre állási csoport összes virtuális gépen. Kattintson a **erőforráscsoportok** > az erőforráscsoport > **erőforrások** > a rendelkezésre állási csoport > **virtuális gépek** > a virtuális gép >  **Állítsa le**.
    - Miután az összes virtuális gép leállítása, a virtuális gép létrehozása a kívánt méret az.
    - Először indítsa el az új virtuális Gépet, majd válassza ki a leállított virtuális gépek mindegyike, és kattintson a Start gombra.


## <a name="the-cluster-does-not-have-free-resources"></a>A fürt nem rendelkezik ingyenes erőforrások
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Ismételje meg később a kérelmet.
- Ha az új virtuális gép egy másik rendelkezésre állási csoport része lehet.
    - Hozzon létre egy virtuális Gépet egy másik rendelkezésre állási csoportot (ugyanabban a régióban).
    - Adja hozzá az új virtuális gép ugyanazon a virtuális hálózaton.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hogyan lehet aktiválni a havi kredit a Visual studio Enterprise (BizSpark)

Aktiválja havi kreditjét, tekintse meg ezt [cikk](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Miért nem telepíthető a GPU illesztőprogramjának egy Ubuntu NV-beli virtuális gép?

Linux GPU-támogatással jelenleg csak akkor használható, az Ubuntu Server 16.04 LTS rendszert futtató Azure-NC virtuális gépekhez. További információkért lásd: [állítsa be GPU-illesztőprogramokat N-sorozatú virtuális gépek Linux rendszerű](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Az illesztőprogramok hiányoznak az N-sorozat Linux rendszerű virtuális gépemhez

A virtuális gépek Linux-alapú illesztőprogramok találhatók [Itt](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Belül az N sorozatú virtuális gépek a GPU-példány nem található

Kihasználhatja a Windows Server 2016 vagy Windows Server 2012 R2 rendszert futtató Azure N-sorozatú virtuális gépek a GPU-funkciókkal, telepítenie kell az NVIDIA grafikus illesztőprogramok az egyes virtuális Gépeken üzembe helyezés után. Illesztőprogram-telepítő adatok érhetők el a [Windows virtuális gépek](../windows/n-series-driver-setup.md) és [Linux rendszerű virtuális gépek](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Az N sorozatú virtuális gépek érhető el az én régiómban?

A rendelkezésre állási ellenőrizheti a [régió tábla által elérhető termékek](https://azure.microsoft.com/regions/services), és díjszabás [Itt](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nem tudok megtekintéséhez, amelyek szeretnék, ha a virtuális gép átméretezése VM Virtuálisgépméret-családhoz.

Ha a virtuális gép fut, egy fizikai kiszolgálón van telepítve. A fizikai kiszolgálók Azure-régióban a közös fizikai hardver fürtök szerint vannak csoportosítva. A virtuális gép áthelyezése a másik hardverfürt igénylő virtuális gép átméretezése eltér attól függően, melyik üzembe helyezési modellben a virtuális gép üzembe helyezéséhez használt.

- A klasszikus üzemi modellt, a felhőszolgáltatás üzembe helyezésének üzembe helyezett virtuális gépek távolítva, és módosítsa a virtuális gépeket egy másik virtuálisgépméret-családhoz méretet kérésének kell.

- Resource Manager-alapú üzemi modellben üzembe helyezett virtuális gépek, le kell állítania minden virtuális gép a rendelkezésre állási csoportban a rendelkezésre állási csoportban lévő összes virtuális gép méretének módosítása előtt.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>A felsorolt Virtuálisgép-méret nem támogatott a rendelkezésre állási csoportban üzembe helyezése során.

Válasszon méretet a rendelkezésre állási csoport fürt támogatott. Ajánlott létrehozása egy rendelkezésre állási beállításakor válassza a legnagyobb Virtuálisgép-méretet kell, és rendelkezik úgy gondolja, hogy az első üzembe helyezés a rendelkezésre állási csoporthoz.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Milyen Linux-disztribúciók és verziók támogatottak az Azure-ban?

A listát a Linux találhat [Azure-Endorsed Disztribúciók](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Adhatok hozzá egy meglévő klasszikus virtuális gép egy rendelkezésre állási csoportot?

Igen. Egy meglévő klasszikus virtuális gép adhat hozzá egy új vagy meglévő rendelkezésre állási csoportban. További információ: [hozzáadása egy meglévő virtuális gépet egy rendelkezésre állási csoporthoz](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>További lépések
Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/).

Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.
