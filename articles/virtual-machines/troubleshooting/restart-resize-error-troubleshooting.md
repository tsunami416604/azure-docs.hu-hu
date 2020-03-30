---
title: Virtuális gép újraindítása vagy újraméretezése problémák az Azure-ban | Microsoft dokumentumok
description: Erőforrás-kezelő telepítési problémáinak elhárítása meglévő virtuális gép újraindításával vagy újraméretezésével kapcsolatban az Azure-ban
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a6532558107463311c4225b9855bc4cd3f19eed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965614"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Üzembehelyezési hibák elhárítása meglévő Windows rendszerű virtuális gép újraindításakor vagy átméretezésekor az Azure-ban
Amikor megpróbál elindítani egy leállított Azure virtuális gépet (VM), vagy átméretezi a meglévő Azure virtuális gép, a gyakori hiba, amely egy foglalási hiba. Ez a hiba akkor lép fel, ha a fürt vagy a régió nem rendelkezik elérhető erőforrásokkal, vagy nem támogatja a kért virtuális gép méretét.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Tevékenységnaplók gyűjtése
A hibaelhárítás megkezdéséhez gyűjtse össze a tevékenységnaplókat a problémával kapcsolatos hiba azonosításához. Az alábbi linkek részletes információkat tartalmaznak a folyamatról:

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/templates/deployment-history.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezeléséhez](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Probléma: Hiba egy leállított virtuális gép indításakor
Megpróbál elindítani egy leállított virtuális gép, de foglalási hiba kap.

### <a name="cause"></a>Ok
A leállított virtuális gép indítására vonatkozó kérelmet meg kell kísérelnie a felhőszolgáltatást tartalmazó eredeti fürtnél. A fürt azonban nem rendelkezik szabad területtel a kérés teljesítéséhez.

### <a name="resolution"></a>Megoldás:
* Állítsa le az összes virtuális gépet a rendelkezésre állási csoportban, majd indítsa újra az egyes virtuális gépeket.
  
  1. Kattintson **az Erőforrás csoportok** > *az erőforráscsoport* > **Erőforrások** > *a rendelkezésre állási készlet* > **virtuális gépek** > *a virtuális gép* > **Stop**.
  2. Miután az összes virtuális gép leáll, jelölje ki az egyes leállított virtuális gépek, és kattintson a Start gombra.
* Próbálja meg újra az újraindítási kérelmet egy későbbi időpontban.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Probléma: Hiba egy meglévő virtuális gép átméretezésekor
Megpróbál átméretezni egy meglévő virtuális gép, de kap egy foglalási hiba.

### <a name="cause"></a>Ok
A virtuális gép átméretezésére vonatkozó kérést meg kell kísérelni a felhőszolgáltatást tartalmazó eredeti fürtön. A fürt azonban nem támogatja a kért virtuális gép méretét.

### <a name="resolution"></a>Megoldás:
* Próbálkozzon újra a kérelem egy kisebb virtuális gép mérete használatával.
* Ha a kért virtuális gép mérete nem módosítható:
  
  1. Állítsa le az összes virtuális gépet a rendelkezésre állási csoportban.
     
     * Kattintson **az Erőforrás csoportok** > *az erőforráscsoport* > **Erőforrások** > *a rendelkezésre állási készlet* > **virtuális gépek** > *a virtuális gép* > **Stop**.
  2. Miután az összes virtuális gép leáll, méretezze át a kívánt virtuális gépet nagyobb méretre.
  3. Jelölje ki az átméreteztetű virtuális gépet, és kattintson a **Start**gombra, majd indítsa el az egyes leállított virtuális gépeket.

## <a name="next-steps"></a>További lépések
Ha problémákba ütközik, amikor új Windows virtuális gépet hoz létre az Azure-ban, olvassa [el az Új Windows-virtuális gépek azure-beli létrehozásával kapcsolatos telepítési problémák elhárításának elhárítása című témakört.](../windows/troubleshoot-deployment-new-vm.md)

