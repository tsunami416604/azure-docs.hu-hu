---
title: VIRTUÁLIS gépek újraindítása vagy átméretezése az Azure-ban | Microsoft Docs
description: A Resource Manager üzembe helyezésével kapcsolatos problémák elhárítása meglévő virtuális gép újraindításával vagy átméretezésével az Azure-ban
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
ms.openlocfilehash: cd05f9f7db0db22759c5e19dbfb59cc377e63f4d
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155469"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Üzembe helyezési problémák elhárítása meglévő Windows rendszerű virtuális gépek újraindításával vagy átméretezésével az Azure-ban
Ha leállított Azure-beli virtuális gépet (VM) próbál meg elindítani, vagy átméretezni egy meglévő Azure-beli virtuális gépet, az észlelt gyakori hiba a foglalási hiba. Ez a hiba akkor jelenik meg, ha a fürt vagy régió vagy nem rendelkezik elérhető erőforrásokkal, vagy nem támogatja a kért virtuálisgép-méretet.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Tevékenységnaplók összegyűjtése
A hibaelhárítás megkezdéséhez Gyűjtse össze a tevékenység naplóit a problémához kapcsolódó hiba azonosításához. A következő hivatkozások részletes információkat tartalmaznak a folyamatról:

[Üzembe helyezési műveletek megtekintése](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Tevékenységnaplók megtekintése az Azure-erőforrások kezeléséhez](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Probléma: Hiba a leállított virtuális gép indításakor
Megpróbál elindítani egy leállított virtuális gépet, de lefoglalási hibát kap.

### <a name="cause"></a>Ok
A leállított virtuális gép elindítására irányuló kérelmet a Cloud Service-t futtató eredeti fürtön kell megkísérelni. Azonban a fürtnek nincs szabad területe a kérelem teljesítéséhez.

### <a name="resolution"></a>Megoldás:
* Állítsa le az összes virtuális gépet a rendelkezésre állási csoportból, majd indítsa újra az egyes virtuális gépeket.
  
  1. Kattintson **Az erőforráscsoportok** > *az erőforráscsoport* > **erőforrásai***a rendelkezésre állási* > csoportVirtual Machinesa > virtuálisgép > *Leállítás.*  > 
  2. Az összes virtuális gép leállítása után válassza ki a leállított virtuális gépeket, és kattintson az Indítás gombra.
* Próbálja megismételni az újraindítási kérelmet később.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Probléma: Hiba történt egy meglévő virtuális gép átméretezése közben
Megpróbál átméretezni egy meglévő virtuális gépet, de lefoglalási hibát kap.

### <a name="cause"></a>Ok
A virtuális gép átméretezésére irányuló kérelmet a Cloud Service-t futtató eredeti fürtön kell végrehajtani. A fürt azonban nem támogatja a kért VM-méretet.

### <a name="resolution"></a>Megoldás:
* Próbálja megismételni a kérést kisebb virtuálisgép-méret használatával.
* Ha a kért virtuális gép mérete nem módosítható:
  
  1. Állítsa le az összes virtuális gépet a rendelkezésre állási csoportból.
     
     * Kattintson **Az erőforráscsoportok** > *az erőforráscsoport* > **erőforrásai***a rendelkezésre állási* > csoportVirtual Machinesa > virtuálisgép > *Leállítás.*  > 
  2. Az összes virtuális gép leállítása után méretezze át a kívánt virtuális gépet nagyobb méretre.
  3. Jelölje ki az átméretezett virtuális gépet, és kattintson a **Start**gombra, majd indítsa el az egyes leállított virtuális gépeket.

## <a name="next-steps"></a>További lépések
Ha problémákba ütközik az új Windowsos virtuális gép Azure-beli létrehozásakor, tekintse meg az [üzembe helyezési problémák elhárítása új Windowsos virtuális gép létrehozása az Azure-ban](../windows/troubleshoot-deployment-new-vm.md)című témakört.

