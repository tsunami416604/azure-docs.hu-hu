---
title: Fizikai kiszolgálók célkörnyezetének beállítása az Azure Site Recovery szolgáltatásban
description: Ez a cikk ismerteti, hogyan állíthatja be a cél Azure-környezetben a fizikai kiszolgálók vész-helyreállítási azure site recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953903"
---
# <a name="prepare-target-vmware-to-azure"></a>Cél előkészítése (VMware az Azure-ba)

Ez a cikk ismerteti, hogyan készítse elő az Azure-környezetben a Windows vagy Linux operációs rendszert futtató fizikai kiszolgálók (X64) Azure-ba történő replikálásának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi:
- Létrehozott egy Helyreállítási szolgáltatások tárolóját a fizikai kiszolgálók védelme érdekében. Az [Azure Portalon](https://portal.azure.com "Azure portál")létrehozhat egy Helyreállítási szolgáltatások trezort.
- A [helyszíni környezetet fizikai kiszolgálók Azure-ba](physical-azure-disaster-recovery.md) replikálásához beállította.

## <a name="prepare-target"></a>Cél előkészítése

Az **1.** **Step 2:Prepare Source** **Step 3: Target**

![Cél előkészítése](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Előfizetés:** A legördülő menüben válassza ki azt az előfizetést, amelybe a fizikai kiszolgálókat replikálni szeretné.
2. **Telepítési modell:** Válassza ki a telepítési modellt (Klasszikus vagy Erőforrás-kezelő)

A kiválasztott központi telepítési modell alapján egy érvényesítési fut annak érdekében, hogy a cél-előfizetésben legalább egy kompatibilis tárfiókkal és virtuális hálózattal rendelkezzen a fizikai kiszolgálók replikálásához és feladatátvételéhez.

Miután az ellenőrzések sikeresen befejeződtek, kattintson az OK gombra a következő lépés lépéshez.

Ha nem rendelkezik kompatibilis Erőforrás-kezelő tárfiókkal vagy virtuális hálózattal, a lap tetején található **+ tárfiók** vagy **+ hálózat** gombokra kattintva hozhat létre egyet.

## <a name="next-steps"></a>További lépések
[A replikációs beállítások konfigurálása](vmware-azure-set-up-replication.md).
