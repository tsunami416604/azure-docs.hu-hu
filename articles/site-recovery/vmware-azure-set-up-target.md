---
title: A környezet előkészítése a cél a VMware replikálásához az Azure-bA |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan készíti elő a cél Azure-környezet a VMware virtuális gép replikálása az Azure-bA.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: 238e7a26be67fcfd2a0b79a87409e5c0d57e0cbf
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338365"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Készítse elő a célkörnyezet vész-helyreállítási VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA

Ez a cikk ismerteti, hogyan készíti elő a cél Azure-környezet a VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba történő elindításához.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy:
- Létrehozott egy Recovery Services-tároló [az Azure portal](http://portal.azure.com "az Azure portal") a forrás-gépek védelméhez
- A telepítő a helyszíni környezet replikálni a forrás van [VMware virtuális gépek](vmware-azure-set-up-source.md) vagy [fizikai kiszolgálók](physical-azure-set-up-source.md) az Azure-bA.

## <a name="prepare-target"></a>Cél előkészítése

Befejezése után a **1. lépés: Válassza ki a védelmi cél** és **2. lépés: Forrás előkészítése**, ekkor megnyílik **3. lépés: Cél**

![Cél előkészítése](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Előfizetés:** A legördülő menüből válassza ki az előfizetést, amelyhez a virtuális gépek vagy fizikai kiszolgálókat replikálhat.
2. **Üzemi modell:** Válassza ki a központi telepítési modellt (klasszikus vagy Resource Manager)

Egy érvényesítési fut a kiválasztott üzemi modell alapján, ellenőrizze, hogy legalább egy virtuális hálózatot a célelőfizetésben történő replikálásához és feladatátvételéhez a virtuális gép vagy fizikai kiszolgáló.

Miután az ellenőrzés sikeres, kattintson az OK gombra a következő lépéssel.

Ha nem rendelkezik virtuális hálózattal, létrehozhat egyet, kattintson a **+ hálózat** gombra a lap tetején.

## <a name="next-steps"></a>További lépések
[Replikációs beállítások konfigurálása](vmware-azure-set-up-replication.md).
