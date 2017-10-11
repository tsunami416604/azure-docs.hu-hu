---
title: "Azure-ban az Azure Site Recovery (a System Center VMM) a Hyper-V virtuális gépek replikálása Azure-erőforrások előkészítése |} Microsoft Docs"
description: "Ismerteti az Azure-beli (VMM) szolgáltatással a Hyper-V virtuális gépek replikálása az Azure-bA az Azure Site Recovery elindítása előtt"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1568bdc3-e767-477b-b040-f13699ab5644
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 63b005f37ab5e15e8a1b4645446d65f1529f1bbd
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-with-vmm-to-azure"></a>5. lépés: Felkészülés az Azure-ba (VMM) szolgáltatással a Hyper-V replikáció Azure-erőforrások

Annak ellenőrzése után [hálózati vonatkozó követelmények](vmm-to-azure-walkthrough-network.md), kövesse az utasításokat ebben a cikkben az Azure-erőforrások készíti elő az, hogy az Azure-bA a replikálhatjaahelyszíniHyper-Vvirtuálisgépek,aSystemCenterVirtualMachineManager(VMM)felhők[ Az Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

A cikk elolvasása után bármely fűzhetnek alsó, vagy a műszaki kérdései a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-an-azure-account"></a>Az Azure-fiók beállítása

- Első egy [Microsoft Azure-fiók](http://azure.microsoft.com/).
- Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
- Ellenőrizze a támogatott régiók a helyreállítás alatt a cikknek a földrajzi elérhetőséggel [Azure Site Recovery – díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/).
- További tudnivalók [Site Recovery díjszabásáról](site-recovery-faq.md#pricing), és a [díjszabása](https://azure.microsoft.com/pricing/details/site-recovery/).
- Győződjön meg arról, hogy az Azure-fiókjával a megfelelő [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)Azure virtuális gépek létrehozásához. [További](../active-directory/role-based-access-built-in-roles.md) Azure szerepköralapú hozzáférés-vezérléssel kapcsolatos.


## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

- Állítson be egy [Azure hálózati](../virtual-network/virtual-network-get-started-vnet-subnet.md). Azure virtuális gépek kerülnek ehhez a hálózathoz, ha feladatátvételt követően jönnek létre.
- A hálózati és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
- Helyreállítás az Azure portálon állítsa be a hálózatokat fogja tudni használni [erőforrás-kezelő](../resource-manager-deployment-model.md), vagy a klasszikus mód.
- Javasoljuk, hogy a kezdés előtt hozza létre a hálózatot. Ha most kihagyja ezt a lépést, a Site Recovery üzembe helyezése közben kell majd elvégeznie.
- További tudnivalók [virtuális hálózati árképzési](https://azure.microsoft.com/pricing/details/virtual-network/).


## <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

- A Site Recovery a helyszíni gépeket replikál az Azure storage. Azure virtuális gépek jönnek létre a tárból, feladatátvételt követően.
- Állítsa be a standard vagy prémium [Azure storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account) az Azure-bA replikált adatok tárolásához.
- [Prémium szintű storage](../storage/common/storage-premium-storage.md) általában azoknak a virtuális gépekhez, amelyek folyamatosan magas I/O teljesítménye, és a gazdagép IO-igényes munkaterhelések kis késleltetésű van szükség.
- Ha Prémium szintű fiókot kíván használni a replikált adatok tárolására, a helyszíni adatokban bekövetkező változásokat rögzítő replikálási naplók tárolására be kell állítania egy standard szintű Storage-fiókot is.
- Attól függően, hogy a használni kívánt erőforrás-modellje az Azure virtuális gépek a feladatátvételt egy fiók beállítása a [Resource Manager módra](../storage/common/storage-create-storage-account.md), vagy [klasszikus módban](../storage/common/storage-create-storage-account.md).
- Azt javasoljuk, hogy állítsa be a tárfiók megkezdése előtt. Ha most kihagyja ezt kell tennie azt a Site Recovery üzembe helyezése során. A fiókokat és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
- Erőforráscsoportok egyazon előfizetésen belül, vagy az eltérő előfizetések a Site Recovery által használt tárfiókok nem helyezhető át.


## <a name="next-steps"></a>Következő lépések

Ugrás a [6. lépés: készítse elő a VMM](vmm-to-azure-walkthrough-vmm-hyper-v.md)
