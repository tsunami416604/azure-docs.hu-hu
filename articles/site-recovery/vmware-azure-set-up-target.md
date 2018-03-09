---
title: "A VMware-replikáció az Azure a célkörnyezet előkészítése |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan készíti elő a cél Azure-alapú környezetben az Azure VMware virtuális gép replikációját."
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 5f14bbed7ae59737f62fb736591775cb7ba495c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>A VMware-replikáció Azure cél környezet előkészítése

A cikkből megtudhatja, hogyan készíti elő a cél Azure-alapú környezetben elindítani a VMware virtuális gépek replikálása Azure-bA.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy:
- A Recovery Services-tároló a VMware virtuális gépek védelmére szolgáló hozott létre. A Recovery Services-tároló a hozhat létre a [Azure-portálon](http://portal.azure.com "Azure-portálon").
- Rendelkezik [a helyszíni környezet beállítása](vmware-azure-set-up-source.md) a VMware virtuális gépek replikálása az Azure-bA.

## <a name="prepare-target"></a>Készítse elő a cél

Befejezése után a **lépés 1:Select védelmi cél** és **2. lépés: Felkészülés forrás**, való **3. lépés: cél**

![Készítse elő a cél](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Előfizetés:** a legördülő menüből válassza ki az előfizetést, amelyet szeretne replikálni a virtuális gépeket.
2. **Telepítési modell:** (klasszikus vagy erőforrás-kezelő) telepítési modell kiválasztása

A választott telepítési modell alapján, egy érvényesítési van futtatásával győződjön meg arról, hogy legalább egy kompatibilis tárfiók és a célként megadott előfizetés replikálásához és feladatátvételi virtuális hálózat a virtuális gép.

Miután az ellenőrzés sikeres, kattintson az OK gombra a következő lépéssel.

Ha egy kompatibilis erőforrás-kezelő tárfiókhoz vagy a virtuális hálózat nem rendelkezik, létrehozhat kattintva a **+ Tárfiók** vagy **+ hálózat** gombok az oldal tetején.

## <a name="next-steps"></a>További lépések
[Replikáció beállításainak konfigurálása](vmware-azure-set-up-replication.md).
