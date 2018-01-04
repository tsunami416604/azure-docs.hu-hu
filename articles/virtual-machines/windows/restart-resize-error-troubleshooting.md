---
title: "Virtuális gép újraindításával és átméretezésével állít ki az Azure-ban |} Microsoft Docs"
description: "A újraindításával és átméretezésével egy meglévő Windows rendszerű virtuális gép az Azure Resource Manager telepítési problémák elhárításához"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 11/03/2017
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 482a194535cba8715b70ecff4679691515b206a9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Központi telepítés elhárítása újraindításával és átméretezésével egy meglévő Windows Azure-ban
Indítsa el a leállított Azure virtuális gép (VM), vagy egy meglévő Azure virtuális gép átméretezése megkísérlésekor előforduló gyakori hiba: egy memóriafoglalási hiba. Ez a hiba eredménye, ha a fürt vagy a régió nincs rendelkezésre álló erőforrások vagy nem támogatja a kért Virtuálisgép-méretet.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>A gyűjtés tevékenység naplói
Hibaelhárítás indítása gyűjtünk azonosítsa a hibát a probléma társított a tevékenység-naplókat. Az alábbi hivatkozások folyamat részletes információkat tartalmazza:

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Az Azure-erőforrások kezeléséhez tevékenység naplók megtekintése](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Hiba: Hiba a leállított virtuális gép indításakor
A leállt virtuális gépek elindul, de az beszerzése egy memóriafoglalási hiba próbál.

### <a name="cause"></a>Ok
A leállt virtuális gépek indítására vonatkozó kérelem rendelkezik kísérli meg a következő az eredeti fürt, amelyen a felhőalapú szolgáltatás. A fürt azonban nem rendelkezik szabad lemezterület a kérelem teljesítéséhez.

### <a name="resolution"></a>Megoldás:
* Állítsa le a rendelkezésre állási csoport összes virtuális gépet, és indítsa újra az összes virtuális Géphez.
  
  1. Kattintson a **erőforráscsoportok** > *az erőforráscsoport* > **erőforrások** > *a rendelkezésre állási csoport* > **virtuális gépek** > *a virtuális gép* > **leállítása**.
  2. Után állítsa le a virtuális gépeket, jelölje ki a leállított virtuális gépeken, és válassza az Indítás parancsot.
* Újraindítási kérés újra később.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Hiba: Hiba a meglévő virtuális átméretezése
Meglévő virtuális átméretezése, de egy foglalási hiba próbál.

### <a name="cause"></a>Ok
A kérelem átméretezni a virtuális gép ki lehet megkísérelni, az eredeti fürt, amelyen a felhőalapú szolgáltatás. A fürt azonban nem támogatja a kért Virtuálisgép-méretet.

### <a name="resolution"></a>Megoldás:
* Próbálja megismételni a kérelmet kisebb Virtuálisgép-méretet.
* Ha a kért virtuális gép mérete nem lehet módosítani:
  
  1. Állítsa le a rendelkezésre állási csoport összes virtuális gépet.
     
     * Kattintson a **erőforráscsoportok** > *az erőforráscsoport* > **erőforrások** > *a rendelkezésre állási csoport* > **virtuális gépek** > *a virtuális gép* > **leállítása**.
  2. Után állítsa le a virtuális gépeket, a nagyobb méretű a kívánt virtuális gép átméretezésével.
  3. Jelöljön ki az átméretezett virtuális Gépet, és kattintson a **Start**, majd indítsa el a leállított virtuális gépek mindegyikének.

## <a name="next-steps"></a>További lépések
Ha tapasztal, amikor új Windows virtuális gép létrehozása az Azure-ban, tekintse meg [egy új Windows virtuális gép létrehozása az Azure-ban a telepítési problémák elhárításához](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

