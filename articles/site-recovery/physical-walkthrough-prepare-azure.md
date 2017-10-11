---
title: "A helyszíni fizikai kiszolgálók replikálása az Azure-bA az Azure Site Recovery segítségével az Azure erőforrás előkészítése |} Microsoft Docs"
description: "Ismerteti az Azure-beli a helyszíni kiszolgálók replikálása az Azure-bA az Azure Site Recovery szolgáltatás elindítása előtt"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: b7411fa6aba04ffd34f3f4bd03e706ca75afc9c8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="step-5-prepare-azure-resources-for-physical-server-replication-to-azure"></a>5. lépés: Azure-erőforrások előkészítése a fizikai kiszolgáló replikációs az Azure-bA


Ez a cikk az utasításokat követve előkészítése az Azure-erőforrások, úgy, hogy a helyszíni kiszolgálók Azure használatával replikálhatja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Előkészületek

Győződjön meg arról, hogy elolvasta a [Előfeltételek](physical-walkthrough-prerequisites.md).

## <a name="set-up-an-azure-account"></a>Az Azure-fiók beállítása

- Első egy [Microsoft Azure-fiók](http://azure.microsoft.com/).
- Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
- A támogatott régiók a Site Recovery ellenőrizze **cikknek a földrajzi elérhetőséggel** a [Azure Site Recovery – díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/).
- További tudnivalók [Site Recovery díjszabásáról](site-recovery-faq.md#pricing), és a [díjszabása](https://azure.microsoft.com/pricing/details/site-recovery/).



## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

- Állítsa be az Azure-hálózatot. Azure virtuális gépek kerülnek ehhez a hálózathoz, ha feladatátvételt követően jönnek létre.
- Helyreállítás az Azure portálon állítsa be a hálózatokat fogja tudni használni [erőforrás-kezelő](../resource-manager-deployment-model.md), vagy a klasszikus mód.
- A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.
- További tudnivalók [virtuális hálózati árképzési](https://azure.microsoft.com/pricing/details/virtual-network/).
- További információ [Azure Virtuálisgép-kapcsolat](physical-walkthrough-network.md) feladatátvételt követően.


## <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

- A Site Recovery a helyszíni kiszolgálók az Azure storage replikálja. Azure virtuális gépek jönnek létre a tárból, feladatátvételt követően.
- Állítson be egy [Azure storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account) a replikált adatok tárolásához.
- Helyreállítás az Azure portálon használható storage-fiókok beállítása az erőforrás-kezelőben, vagy a klasszikus módban.
- A tárfiók lehet standard vagy [prémium](../storage/common/storage-premium-storage.md).
- Ha a prémium szintű fiók beállítása is szüksége lesz egy további standard fiók naplóadatokat.


## <a name="next-steps"></a>Következő lépések

Ugrás a [6. lépés: állítson be egy tárolóban.](physical-walkthrough-create-vault.md)
