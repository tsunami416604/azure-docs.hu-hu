---
title: "Cél (VMware az Azure-bA) előkészítése |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan készíti elő az elindítani a VMware virtuális gépek replikálása Azure-bA az Azure környezetben."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/23/2017
ms.author: bsiva
ms.openlocfilehash: 98e0a7cd77e8e6e9ce124845aad49bd03a2bf1d8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Készítse elő a cél (az Azure-bA VMware)
> [!div class="op_single_selector"]
> * [VMware – Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Fizikai az Azure-bA](./site-recovery-prepare-target-physical-to-azure.md)

A cikkből megtudhatja, hogyan készíti elő az elindítani a VMware virtuális gépek replikálása Azure-bA az Azure környezetben.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy:
- A Recovery Services-tároló a VMware virtuális gépek védelmére szolgáló hozott létre. A Recovery Services-tároló a hozhat létre a [Azure-portálon](http://portal.azure.com "Azure-portálon").
- Rendelkezik [a helyszíni környezet beállítása](./site-recovery-set-up-vmware-to-azure.md) a VMware virtuális gépek replikálása az Azure-bA.

## <a name="prepare-target"></a>Készítse elő a cél

Befejezése után a **lépés 1:Select védelmi cél** és **2. lépés: Felkészülés forrás**, való **3. lépés: cél**

![Készítse elő a cél](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Előfizetés:** a legördülő menüből válassza ki az előfizetést, amelyet szeretne replikálni a virtuális gépeket.
2. **Telepítési modell:** (klasszikus vagy erőforrás-kezelő) telepítési modell kiválasztása

A választott telepítési modell alapján, egy érvényesítési van futtatásával győződjön meg arról, hogy legalább egy kompatibilis tárfiók és a célként megadott előfizetés replikálásához és feladatátvételi virtuális hálózat a virtuális gép.

Miután az ellenőrzés sikeres, kattintson az OK gombra a következő lépéssel.

Ha egy kompatibilis erőforrás-kezelő tárfiókhoz vagy a virtuális hálózat nem rendelkezik, létrehozhat kattintva a **+ Tárfiók** vagy **+ hálózat** gombok az oldal tetején.

## <a name="next-steps"></a>További lépések
[Replikáció beállításainak konfigurálása](./site-recovery-setup-replication-settings-vmware.md).
