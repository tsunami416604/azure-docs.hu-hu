---
title: Azure-beli virtuális gépek áthelyezése új előfizetésre vagy erőforráscsoport-csoportba
description: A Azure Resource Manager használatával áthelyezheti a virtuális gépeket egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: e5bd004b6619db9c9882b8e9e6005309317b8ca5
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744638"
---
# <a name="move-guidance-for-virtual-machines"></a>Útmutató a virtuális gépekhez

Ez a cikk a jelenleg nem támogatott forgatókönyveket és a virtuális gépek biztonsági mentéssel való áthelyezésének lépéseit ismerteti.

## <a name="scenarios-not-supported"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek még nem támogatottak:

* A Availability Zones Managed Disks nem helyezhető át másik előfizetésbe.
* A standard SKU Load Balancer vagy standard SKU nyilvános IP-címmel rendelkező Virtual Machine Scale Sets nem helyezhető át.
* A Piactéri erőforrásokkal létrehozott virtuális gépek nem helyezhetők át az erőforráscsoportok vagy előfizetések között. Helyezze el a virtuális gépet a jelenlegi előfizetésben, majd telepítse újra az új előfizetésben.
* Meglévő virtuális hálózatban lévő virtuális gépek nem helyezhetők át új előfizetésbe, ha nem helyezi át a virtuális hálózatban lévő összes erőforrást.
* Az alacsony prioritású virtuális gépek és az alacsony prioritású virtuálisgép-méretezési csoportok nem helyezhetők át az erőforráscsoportok vagy az előfizetések között.
* Egy rendelkezésre állási csoportba tartozó virtuális gépek nem helyezhetők át egyenként.

## <a name="virtual-machines-with-azure-backup"></a>Virtuális gépek Azure Backup

Azure Backuprel konfigurált virtuális gépek áthelyezéséhez törölnie kell a visszaállítási pontokat a tárból.

Ha a helyreállítható [Törlés](../../../backup/backup-azure-security-feature-cloud.md) engedélyezve van a virtuális gépen, akkor a virtuális gép nem helyezhető át a visszaállítási pontok megtartására. [Tiltsa le a nem kötelező törlést](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) , vagy várjon 14 nappal a visszaállítási pontok törlése után.

### <a name="portal"></a>Portál

1. Átmenetileg állítsa le a biztonsági mentést, és őrizze meg a biztonsági mentési adatait.
2. Azure Backup konfigurált virtuális gépek áthelyezéséhez hajtsa végre a következő lépéseket:

   1. Keresse meg a virtuális gép helyét.
   2. Keressen egy erőforráscsoportot a következő elnevezési mintával: `AzureBackupRG_<location of your VM>_1`. Például *AzureBackupRG_westus2_1*
   3. A Azure Portalban tekintse meg a **rejtett típusok megjelenítése**részt.
   4. Keresse meg a **Microsoft. számítás/restorePointCollections** típusú erőforrást, amely az elnevezési `AzureBackup_<name of your VM that you're trying to move>_###########`mintával rendelkezik.
   5. Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.
   6. A törlési művelet befejezése után áthelyezheti a virtuális gépet.

3. Helyezze át a virtuális gépet a célként megadott erőforrás-csoportba.
4. Folytassa a biztonsági mentést.

### <a name="powershell"></a>PowerShell

* Keresse meg a virtuális gép helyét.
* Keressen egy erőforráscsoportot a következő elnevezési mintával: `AzureBackupRG_<location of your VM>_1` például AzureBackupRG_westus2_1
* Ha a PowerShellben használja a `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` parancsmagot
* Keresse meg azt az erőforrást, amelynek a típusa `Microsoft.Compute/restorePointCollections` az elnevezési minta`AzureBackup_<name of your VM that you're trying to move>_###########`
* Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.

### <a name="azure-cli"></a>Azure CLI

* Keresse meg a virtuális gép helyét.
* Keressen egy erőforráscsoportot a következő elnevezési mintával: `AzureBackupRG_<location of your VM>_1` például AzureBackupRG_westus2_1
* Ha a CLI-ben használja a`az resource list -g AzureBackupRG_<location of your VM>_1`
* Keresse meg azt az erőforrást, amelynek a típusa `Microsoft.Compute/restorePointCollections` az elnevezési minta`AzureBackup_<name of your VM that you're trying to move>_###########`
* Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.

## <a name="next-steps"></a>További lépések

* Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../move-resource-group-and-subscription.md).

* További információ a helyreállítási tár biztonsági mentéséhez való áthelyezéséről: [Recovery Services korlátozások](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
