---
title: Az Azure-beli virtuális gépek áthelyezése új előfizetésbe vagy erőforráscsoportba
description: Az Azure Resource Manager használatával virtuális gépeket helyezhet át egy új erőforráscsoportba vagy előfizetésbe.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 97c49f90dab2aafd89de322e57ad44ff1fc9d367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479760"
---
# <a name="move-guidance-for-virtual-machines"></a>Útmutató áthelyezése a virtuális gépekhez

Ez a cikk ismerteti a forgatókönyvek, amelyek jelenleg nem támogatottak, és a virtuális gépek áthelyezése biztonsági mentéssel.

## <a name="scenarios-not-supported"></a>Nem támogatott esetek

A következő esetek még nem támogatottak:

* A rendelkezésre állási zónákban lévő felügyelt lemezek nem helyezhetők át másik előfizetésbe.
* Virtuálisgép-méretezési készletek standard termékkészlet-elosztóval vagy szabványos termékváltozat nyilvános IP-címzéssel nem helyezhető át.
* A Marketplace-erőforrásokból létrehozott, csomaggal rendelkező virtuális gépek nem helyezhetők át erőforráscsoportok vagy előfizetések között. A virtuális gép kiépítése az aktuális előfizetésben, és újra üzembe helyezése az új előfizetésben.
* A meglévő virtuális hálózatban lévő virtuális gépek nem helyezhetők át új előfizetésbe, ha nem helyezi át a virtuális hálózat összes erőforrását.
* Alacsony prioritású virtuális gépek és alacsony prioritású virtuálisgép-méretezési csoportok nem helyezhetők át erőforráscsoportok vagy előfizetések között.
* A rendelkezésre állási csoportban lévő virtuális gépek nem helyezhetők át külön-külön.

## <a name="virtual-machines-with-azure-backup"></a>Virtuális gépek az Azure Backup segítségével

Az Azure Backup szolgáltatással konfigurált virtuális gépek áthelyezéséhez használja az alábbi kerülő megoldásokat:

* Keresse meg a helyét a virtuális gép.
* A következő elnevezési mintával `AzureBackupRG_<location of your VM>_1` rendelkező erőforráscsoport keresése: például AzureBackupRG_westus2_1
* Ha az Azure Portalon, majd jelölje be a "Rejtett típusok megjelenítése" jelölőnégyzetet.
* Ha a PowerShellben `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` használja a parancsmast
* Ha a CLI-ben használja a`az resource list -g AzureBackupRG_<location of your VM>_1`
* Az elnevezési `Microsoft.Compute/restorePointCollections` mintával rendelkező típusú erőforrás megkeresése`AzureBackup_<name of your VM that you're trying to move>_###########`
* Törölje ezt az erőforrást. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági mentési adatokat nem.
* A törlés befejezése után áthelyezheti a tárolót és a virtuális gépet a cél-előfizetésbe. Az áthelyezés után folytathatja a biztonsági mentéseket az adatok elvesztése nélkül.
* A helyreállítási szolgáltatás tárolóinak biztonsági mentéshez való áthelyezéséről a [Helyreállítási szolgáltatások korlátozásai](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére vonatkozó parancsokról az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](../move-resource-group-and-subscription.md)témakörben található.
