---
title: Virtuális gép újraindításával vagy átméretezésével kapcsolatos problémák az Azure-ban |} A Microsoft Docs
description: Újraindításával vagy átméretezésével, meglévő Windows virtuális gépként az Azure Resource Manager üzembe helyezés hibáinak elhárítása
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4e0c77c03856b4851ee5fe49bd6ae54d47f6c31
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35923536"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Újraindítása vagy átméretezése egy meglévő Windows Azure-beli virtuális gép üzembe helyezés hibáinak elhárítása
Próbálja meg elindítani egy leállított Azure virtuális gép (VM) vagy egy meglévő Azure virtuális gép átméretezése, tapasztal a gyakori hiba esetén egy foglalási hiba. Ez a hiba eredményeként, ha a fürt vagy a régió nem rendelkezik elérhető erőforrások vagy nem támogatja a kért Virtuálisgép-méretet.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Collect-Tevékenységnaplók
Hibaelhárítás indítása, hogy azonosítsa a hibát a probléma társított a vizsgálati naplók összegyűjtése. Az alábbi hivatkozások a folyamat részletes információt tartalmaznak:

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezelése](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Probléma: Hiba a leállított virtuális gép indítása
A leállított virtuális gép elindítása, de a foglalási hiba fordul elő, próbálja meg.

### <a name="cause"></a>Ok
A kérelem a leállított virtuális gép elindításához, lehetséges, az eredeti fürthöz, amelyen a felhőszolgáltatás rendelkezik. A fürt azonban nem rendelkezik szabad terület lesz elérhető a kérés teljesítéséhez.

### <a name="resolution"></a>Megoldás:
* Állítsa le a rendelkezésre állási csoport összes virtuális gépen, és indítsa újra az egyes virtuális gépekre.
  
  1. Kattintson a **erőforráscsoportok** > *az erőforráscsoport* > **erőforrások** > *a rendelkezésre állási csoport*  >  **Virtuális gépek** > *a virtuális gép* > **leállítása**.
  2. Után minden virtuális gép leállításához válassza ki a leállított virtuális gépek mindegyike, és kattintson a Start gombra.
* Ismételje meg az újraindítást kér egy későbbi időpontban.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Probléma: Hibaüzenet, ha egy meglévő virtuális gép átméretezése
Próbálja ki egy meglévő virtuális gép átméretezése, de a foglalási hiba fordul elő.

### <a name="cause"></a>Ok
A kérelem a virtuális gép átméretezése, lehetséges, az eredeti fürthöz, amelyen a felhőszolgáltatás rendelkezik. A fürt azonban nem támogatja a kért Virtuálisgép-méretet.

### <a name="resolution"></a>Megoldás:
* Próbálja megismételni a kérést kisebb Virtuálisgép-mérettel.
* Ha a kért virtuális gép mérete nem lehet módosítani:
  
  1. Állítsa le a rendelkezésre állási csoport összes virtuális gépen.
     
     * Kattintson a **erőforráscsoportok** > *az erőforráscsoport* > **erőforrások** > *a rendelkezésre állási csoport*  >  **Virtuális gépek** > *a virtuális gép* > **leállítása**.
  2. Miután az összes virtuális gép leállítása, méretezze át a kívánt virtuális Gépre, és a egy nagyobb méretű.
  3. Válassza ki az átméretezett virtuális Gépet, és kattintson a **Start**, majd indítsa el a leállított virtuális gépek mindegyike.

## <a name="next-steps"></a>További lépések
Ha problémái vannak az Azure-ban új Windows virtuális gép létrehozásakor, [egy új Windows virtuális gép létrehozása az Azure-ban üzembe helyezés hibáinak elhárítása](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

