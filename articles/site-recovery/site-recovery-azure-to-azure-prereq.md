---
title: "A replikáció Azure az Azure Site Recovery segítségével Előfeltételek |} Microsoft Docs"
description: "Ez a cikk összefoglalja az előfeltételeket virtuális gépek és fizikai gépek replikálása az Azure-bA az Azure Site Recovery szolgáltatással."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Egy másik régióban Azure virtuális gépek replikálása Azure Site Recovery segítségével előfeltételei

> [!div class="op_single_selector"]
> * [Az Azure-ból az Azure-bA replikálása](site-recovery-azure-to-azure-prereq.md)
> * [A helyszíni replikálása Azure-bA](site-recovery-prereq.md)

Az Azure Site Recovery szolgáltatás hozzájárul az üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási stratégia megvalósításában:
* Egy másik Azure-régió, az Azure virtuális gépek replikációját.
* A helyszíni fizikai kiszolgálóknak és virtuális gépek Azure-bA vagy másodlagos adatközpontba replikációját. 

Ha az elsődleges helyen valamilyen okból kimaradás lép fel, akkor átveheti egy másodlagos helyre, így az alkalmazások és számítási feladatok nem állnak. Amikor visszatér a normál műveletek sikertelen lehet az elsődleges helyre. A Site Recovery kapcsolatban bővebben lásd: [Mi az Site Recovery?](site-recovery-overview.md).

Ez a cikk az Azure Site Recovery a helyszíni replikáció megkezdéséhez szükséges előfeltételek foglalja össze.

A megjegyzéseket, a cikk alján, vagy technikai kérdéseket a a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Azure-követelmények

**Követelmény** | **Részletek**
--- | ---
**Azure-fiók** | A [Microsoft Azure](http://azure.microsoft.com/) fiók.<br/><br/> Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
**Site Recovery szolgáltatás** | A Site Recovery díjszabásáról kapcsolatban bővebben lásd: [Site Recovery díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/). Azt javasoljuk, hogy a cél Azure-régió, egy vész-helyreállítási helyként használni kívánt hozzon létre a Recovery Services-tároló. Például ha a forrás virtuális gépek futnak, az USA keleti régiója, és szeretné replikálni a központi US, azt javasoljuk, hogy Államok középső hozzon létre a tárolót.|
**Az Azure kapacitás** | A cél a vész-helyreállítási helyként használni kívánt Azure-régiót kell rendelkezik elegendő kapacitással a virtuális gépeket, a storage-fiókok és a hálózati összetevők előfizetéssel. Nagyobb kapacitású is ügyfélszolgálatához.
**Tárolási útmutatásokkal** | Győződjön meg arról, hogy kövesse a [tárolási útmutatásokkal](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) a forrás Azure virtuális gépek a teljesítménnyel kapcsolatos problémák elkerülése érdekében. Ha követi az alapértelmezett beállításokat, a Site Recovery a forrás-konfigurációtól függően szükséges tárfiókok hoz létre. Ha testre szabhatja, és válassza ki a saját beállításait, győződjön meg arról, hogy kövesse a [méretezhetőségi célok a virtuális gép lemezeinek](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Hálózati útmutató** | Kell engedélyezett a kimenő kapcsolat az Azure virtuális gép számára megadott URL-címek vagy IP-címtartományok. További részletekért tekintse meg a [útmutató az Azure virtuális gépek replikálása a hálózat](site-recovery-azure-to-azure-networking-guidance.md) cikk.
**Azure virtuális gép** | Győződjön meg arról, hogy a legújabb legfelső szintű tanúsítványokat Windows vagy Linux virtuális gép jelen-e. Ha nem találhatók a legújabb legfelső szintű tanúsítványok, a virtuális gép biztonsági korlátozások miatt a Site Recovery nem regisztrálható.

>[!NOTE]
>Specifikus konfigurációk-támogatással kapcsolatos további tudnivalókért olvassa el a [támogatási mátrix](site-recovery-support-matrix-azure-to-azure.md).

## <a name="next-steps"></a>Következő lépések
- További információ [útmutató az Azure virtuális gépek replikálása a hálózat](site-recovery-azure-to-azure-networking-guidance.md).
- A munkaterhelések számára a védelmének megkezdéséhez [Azure virtuális gépek replikálásához](site-recovery-azure-to-azure.md).
