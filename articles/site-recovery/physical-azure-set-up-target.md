---
title: Vészhelyreállításhoz helyszíni fizikai kiszolgálók Azure-bA a célkörnyezet beállítása |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állítható be a cél Azure-környezet a fizikai kiszolgálók Azure Site Recovery használata vész-helyreállítási.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 43276aad26bc06400c1bc4b5feaace0d5646c213
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849232"
---
# <a name="prepare-target-vmware-to-azure"></a>Cél (VMware – Azure) előkészítése

Ez a cikk ismerteti, hogyan készíti elő az Azure-környezet (x 64) fizikai kiszolgálók, Windows vagy Linux rendszerű Azure-ba való replikálásának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy:
- Létrehozott egy Recovery Services-tárolót a fizikai kiszolgálók védelme érdekében. Létrehozhat egy Recovery Services-tárolót, a [az Azure portal](http://portal.azure.com "az Azure portal").
- Rendelkezik [a helyszíni környezet beállítása](physical-azure-disaster-recovery.md) a fizikai kiszolgálók replikálása az Azure-bA.

## <a name="prepare-target"></a>Cél előkészítése

Befejezése után a **lépés 1:Select védelmi cél** és **2. lépés: a forrás előkészítése**, ekkor megnyílik **3. lépés: cél**

![Cél előkészítése](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Előfizetés:** a legördülő menüből válassza ki az előfizetést, amelyhez szeretné replikálni a fizikai kiszolgálók.
2. **Üzemi modell:** (klasszikus vagy Resource Manager) telepítési modell kiválasztása

A kiválasztott üzemi modell alapján a rendszer érvényesítési futtató gondoskodjon arról, hogy legalább egy kompatibilis tárfiók és a célként megadott előfizetés replikálásához és a feladatátvételi virtuális hálózat a fizikai kiszolgálók.

Miután az ellenőrzés sikeres, kattintson az OK gombra a következő lépéssel.

Ha egy kompatibilis a Resource Manager-tárfiókot vagy a virtuális hálózat nem rendelkezik, létrehozhat egyet, kattintson a **+ Tárfiók** vagy **+ hálózat** gombok a lap tetején.

## <a name="next-steps"></a>További lépések
[Replikációs beállítások konfigurálása](vmware-azure-set-up-replication.md).
