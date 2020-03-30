---
title: A VMware virtuálisgép-replikációs cél előkészítése az Azure Site Recovery szolgáltatásban
description: Ez a cikk ismerteti, hogyan készítse elő a cél Azure-környezet vmware vm replikáció az Azure-ba.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73693168"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>A célkörnyezet előkészítése a VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba való vészhelyreállítására

Ez a cikk ismerteti, hogyan készítse elő a cél Azure-környezetben a VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba replikálásának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi:
- Létrehozott egy Recovery Services Vault-ot az [Azure Portalon](https://portal.azure.com "Azure portál") a forrásgépek védelme érdekében
- A helyszíni környezetet úgy állítsa be, hogy replikálja a [vmware-i virtuális gépeket](vmware-azure-set-up-source.md) vagy [fizikai kiszolgálókat](physical-azure-set-up-source.md) az Azure-ba.

## <a name="prepare-target"></a>Cél előkészítése

**Az 1.** **Step 2: Prepare Source** **Step 3: Target**

![Cél előkészítése](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Előfizetés:** A legördülő menüben válassza ki azt az előfizetést, amelybe a virtuális gépeket vagy a fizikai kiszolgálókat replikálni szeretné.
2. **Telepítési modell:** Válassza ki a telepítési modellt (Klasszikus vagy Erőforrás-kezelő)

A kiválasztott központi telepítési modell alapján egy érvényesítési fut annak biztosítására, hogy a cél-előfizetésben legalább egy virtuális hálózat replikálható és feladatátvétela a virtuális gép vagy a fizikai kiszolgáló.

Miután az ellenőrzések sikeresen befejeződtek, kattintson az OK gombra a következő lépés lépéshez.

Ha nem rendelkezik virtuális hálózattal, a lap tetején található **+ Hálózat** gombra kattintva hozhat létre egyet.

## <a name="next-steps"></a>További lépések
[A replikációs beállítások konfigurálása](vmware-azure-set-up-replication.md).
