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
ms.date: 5/31/2017
ms.author: bsiva
ms.openlocfilehash: c84a775564769ddc796aa9d75add019ef1003175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Készítse elő a cél (az Azure-bA VMware)
> [!div class="op_single_selector"]
> * [VMware – Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Fizikai az Azure-bA](./site-recovery-prepare-target-physical-to-azure.md)

A cikkből megtudhatja, hogyan készíti elő az elindítani a VMware virtuális gépek replikálása Azure-bA az Azure környezetben.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy a következő:
- A Recovery Services-tároló a VMware virtuális gépek védelmére szolgáló hozott létre. A Recovery Services-tároló a hozhat létre a [Azure-portálon](http://portal.azure.com "Azure-portálon").
- Rendelkezik [a helyszíni környezet beállítása](./site-recovery-set-up-vmware-to-azure.md) a VMware virtuális gépek replikálása az Azure-bA.

## <a name="prepare-target"></a>Készítse elő a cél

Befejezése után a **lépés 1:Select védelmi cél** és **2. lépés: Felkészülés forrás**, való **3. lépés: cél**

![Készítse elő a cél](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Előfizetés:** a legördülő menüből, jelölje ki azt az előfizetést, amelyet szeretne replikálni a virtuális gépeket.
2. **Telepítési modell:** (klasszikus vagy erőforrás-kezelő) telepítési modell kiválasztása

A választott telepítési modell alapján, egy érvényesítési van futtatásával győződjön meg arról, hogy legalább egy kompatibilis tárfiók és a célként megadott előfizetés replikálásához és feladatátvételi virtuális hálózat a virtuális gép.

Miután az ellenőrzés sikeres, kattintson az OK gombra a következő lépéssel.

Ha egy kompatibilis erőforrás-kezelő tárfiókhoz vagy a virtuális hálózat nem rendelkezik, vagy szeretne adni több, akkor ehhez kattintson a **+ Tárfiók** vagy **+ hálózat** gombok a panel tetején.

## <a name="next-steps"></a>Következő lépések
[Replikáció beállításainak konfigurálása](./site-recovery-setup-replication-settings-vmware.md).
