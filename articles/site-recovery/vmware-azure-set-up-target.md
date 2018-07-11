---
title: A környezet előkészítése a cél a VMware replikálásához az Azure-bA |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan készíti elő a cél Azure-környezet a VMware virtuális gép replikálása az Azure-bA.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 4b428dff1cebf353cc081696649494e6e4ec9b92
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921110"
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>A környezet előkészítése a cél a VMware – Azure replikálás

Ez a cikk ismerteti, hogyan készíti elő a cél Azure-környezet a VMware virtuális gépek Azure-ba történő elindításához.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy:
- A VMware virtuális gépek védelme Recovery Services-tárolóval hozott létre. Létrehozhat egy Recovery Services-tárolót, a [az Azure portal](http://portal.azure.com "az Azure portal").
- Rendelkezik [a helyszíni környezet beállítása](vmware-azure-set-up-source.md) a VMware virtuális gépek replikálása az Azure-bA.

## <a name="prepare-target"></a>Cél előkészítése

Befejezése után a **lépés 1:Select védelmi cél** és **2. lépés: a forrás előkészítése**, ekkor megnyílik **3. lépés: cél**

![Cél előkészítése](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Előfizetés:** a legördülő menüből válassza ki az előfizetést, amelyhez szeretné replikálni a virtuális gépeket.
2. **Üzemi modell:** (klasszikus vagy Resource Manager) telepítési modell kiválasztása

A kiválasztott üzemi modell alapján a rendszer érvényesítési futtató gondoskodjon arról, hogy legalább egy kompatibilis tárfiók és a célként megadott előfizetés replikálásához és a feladatátvételi virtuális hálózat a virtuális gép.

Miután az ellenőrzés sikeres, kattintson az OK gombra a következő lépéssel.

Ha egy kompatibilis a Resource Manager-tárfiókot vagy a virtuális hálózat nem rendelkezik, létrehozhat kattintva a **+ Tárfiók** vagy **+ hálózat** gombok a lap tetején.

## <a name="next-steps"></a>További lépések
[Replikációs beállítások konfigurálása](vmware-azure-set-up-replication.md).
