---
title: "Cél (az Azure-bA fizikai) előkészítése |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan készíti elő az elindítani a Windows vagy Linux rendszerű Azure fizikai kiszolgálók replikálása az Azure környezetben."
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
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: f94d80b5c0f4f67ccf4172df22babfb364c57902
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="prepare-target-vmware-to-azure"></a>Készítse elő a cél (az Azure-bA VMware)
> [!div class="op_single_selector"]
> * [VMware – Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Fizikai az Azure-bA](./site-recovery-prepare-target-physical-to-azure.md)

A cikkből megtudhatja, hogyan készíti elő az elindítani a Windows vagy Linux rendszerű Azure (x 64) fizikai kiszolgálók replikálása az Azure környezetben.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy:
- A Recovery Services-tároló a fizikai kiszolgálók védelméhez hozott létre. A Recovery Services-tároló a hozhat létre a [Azure-portálon](http://portal.azure.com "Azure-portálon").
- Rendelkezik [a helyszíni környezet beállítása](./site-recovery-set-up-physical-to-azure.md) a fizikai kiszolgálók replikálása az Azure-bA.

## <a name="prepare-target"></a>Készítse elő a cél

Befejezése után a **lépés 1:Select védelmi cél** és **2. lépés: Felkészülés forrás**, való **3. lépés: cél**

![Készítse elő a cél](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Előfizetés:** a legördülő menüből válassza ki az előfizetést, a fizikai kiszolgálók replikálni kívánt.
2. **Telepítési modell:** (klasszikus vagy erőforrás-kezelő) telepítési modell kiválasztása

A választott telepítési modell alapján, egy érvényesítési van futtatásával győződjön meg arról, hogy legalább egy kompatibilis tárfiók és a célként megadott előfizetés replikálásához és feladatátvételi virtuális hálózat a fizikai kiszolgálók.

Miután az ellenőrzés sikeres, kattintson az OK gombra a következő lépéssel.

Ha egy kompatibilis erőforrás-kezelő tárfiókhoz vagy a virtuális hálózat nem rendelkezik, létrehozhat egyet, kattintson a **+ Tárfiók** vagy **+ hálózat** gombok az oldal tetején.

## <a name="next-steps"></a>További lépések
[Replikáció beállításainak konfigurálása](./site-recovery-setup-replication-settings-vmware.md).
