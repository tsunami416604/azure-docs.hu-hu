---
title: A környezet előkészítése a cél a VMware replikálásához az Azure-bA |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan készíti elő a cél Azure-környezet a VMware virtuális gép replikálása az Azure-bA.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: a6f983b08415659b9a989ebed824cddd210396e1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233429"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Készítse elő a célkörnyezet vész-helyreállítási VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA

Ez a cikk ismerteti, hogyan készíti elő a cél Azure-környezet a VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba történő elindításához.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy:
- Létrehozott egy Recovery Services-tároló [az Azure portal](http://portal.azure.com "az Azure portal") a forrás-gépek védelméhez
- A telepítő a helyszíni környezet replikálni a forrás van [VMware virtuális gépek](vmware-azure-set-up-source.md) vagy [fizikai kiszolgálók](physical-azure-set-up-source.md) az Azure-bA.

## <a name="prepare-target"></a>Cél előkészítése

Befejezése után a **1. lépés: válassza ki a védelmi cél** és **2. lépés: a forrás előkészítése**, ekkor megnyílik **3. lépés: cél**

![Cél előkészítése](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Előfizetés:** a legördülő menüből válassza ki az előfizetést, amelyhez a virtuális gépek vagy fizikai kiszolgálókat replikálhat.
2. **Üzemi modell:** (klasszikus vagy Resource Manager) telepítési modell kiválasztása

A kiválasztott üzemi modell alapján, egy érvényesítési fut, gondoskodjon arról, hogy legalább egy kompatibilis tárfiók és a célként megadott előfizetés replikálásához és a feladatátvételi virtuális hálózat a virtuális gép vagy fizikai kiszolgáló.

Miután az ellenőrzés sikeres, kattintson az OK gombra a következő lépéssel.

Ha egy kompatibilis a Resource Manager-tárfiókot vagy a virtuális hálózat nem rendelkezik, létrehozhat egyet, kattintson a **+ Tárfiók** vagy **+ hálózat** gombok a lap tetején.

## <a name="next-steps"></a>További lépések
[Replikációs beállítások konfigurálása](vmware-azure-set-up-replication.md).
