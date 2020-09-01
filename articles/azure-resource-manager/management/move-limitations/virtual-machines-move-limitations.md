---
title: Azure-beli virtuális gépek áthelyezése új előfizetésre vagy erőforráscsoport-csoportba
description: A Azure Resource Manager használatával áthelyezheti a virtuális gépeket egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 3878113f6874c40953bec87518a89519bdc6cb1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230959"
---
# <a name="move-guidance-for-virtual-machines"></a>Útmutató a virtuális gépekhez

Ez a cikk a jelenleg nem támogatott forgatókönyveket és a virtuális gépek biztonsági mentéssel való áthelyezésének lépéseit ismerteti.

## <a name="scenarios-not-supported"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek még nem támogatottak:

* A standard SKU Load Balancer vagy standard SKU nyilvános IP-címmel rendelkező Virtual Machine Scale Sets nem helyezhető át.
* A Piactéri erőforrásokkal létrehozott virtuális gépek nem helyezhetők át az előfizetések között. Helyezze el a virtuális gépet a jelenlegi előfizetésben, majd telepítse újra az új előfizetésben.
* Meglévő virtuális hálózatban lévő virtuális gépek nem helyezhetők át új előfizetésbe, ha nem helyezi át a virtuális hálózatban lévő összes erőforrást.
* Az alacsony prioritású virtuális gépek és az alacsony prioritású virtuálisgép-méretezési csoportok nem helyezhetők át az erőforráscsoportok vagy az előfizetések között.
* Egy rendelkezésre állási csoportba tartozó virtuális gépek nem helyezhetők át egyenként.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

A Key vaulttal integrált virtuális gépek nem helyezhetők át [Azure Disk Encryption Linux](../../../virtual-machines/linux/disk-encryption-overview.md) rendszerű virtuális gépekhez vagy [Azure Disk Encryption Windows](../../../virtual-machines/windows/disk-encryption-overview.md)rendszerű virtuális gépekhez. A virtuális gép áthelyezéséhez le kell tiltania a titkosítást.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Virtuális gépek Azure Backup

Azure Backuprel konfigurált virtuális gépek áthelyezéséhez törölnie kell a visszaállítási pontokat a tárból.

Ha a helyreállítható [Törlés](../../../backup/backup-azure-security-feature-cloud.md) engedélyezve van a virtuális gépen, akkor a virtuális gép nem helyezhető át a visszaállítási pontok megtartására. [Tiltsa le a nem kötelező törlést](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) , vagy várjon 14 nappal a visszaállítási pontok törlése után.

### <a name="portal"></a>Portál

1. Átmenetileg állítsa le a biztonsági mentést, és őrizze meg a biztonsági mentési adatait.
2. Azure Backup konfigurált virtuális gépek áthelyezéséhez hajtsa végre a következő lépéseket:

   1. Keresse meg a virtuális gép helyét.
   2. Keressen egy erőforráscsoportot a következő elnevezési mintával: `AzureBackupRG_<VM location>_1` . A név például *AzureBackupRG_westus2_1*formátumú.
   3. A Azure Portalban tekintse meg a **rejtett típusok megjelenítése**részt.
   4. Keresse meg a **Microsoft. számítás/restorePointCollections** típusú erőforrást, amely az elnevezési mintával rendelkezik `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.
   6. A törlési művelet befejezése után áthelyezheti a virtuális gépet.

3. Helyezze át a virtuális gépet a célként megadott erőforrás-csoportba.
4. Folytassa a biztonsági mentést.

### <a name="powershell"></a>PowerShell

1. Keresse meg a virtuális gép helyét.

1. Keressen egy erőforráscsoportot az elnevezési mintával – `AzureBackupRG_<VM location>_1` . A név lehet például a következő: `AzureBackupRG_westus2_1` .

1. Használja a következő parancsot a visszaállítási pont gyűjteményének beszerzéséhez.

   ```azurepowershell
   $RestorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

1. Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.

   ```azurepowershell
   Remove-AzResource -ResourceId $RestorePointCollection.ResourceId -Force
   ```

### <a name="azure-cli"></a>Azure CLI

1. Keresse meg a virtuális gép helyét.

1. Keressen egy erőforráscsoportot az elnevezési mintával – `AzureBackupRG_<VM location>_1` . A név lehet például a következő: `AzureBackupRG_westus2_1` .

1. A visszaállítási pont gyűjteményének beszerzéséhez használja a következő parancsot.

   ```azurecli
   az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections
   ```

1. Az erőforrás AZONOSÍTÓjának megkeresése az elnevezési mintával `AzureBackup_<VM name>_###########`

1. Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.

   ```azurecli
   az resource delete --ids /subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.Compute/restorePointCollections/<name>
   ```

## <a name="next-steps"></a>Következő lépések

* Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../move-resource-group-and-subscription.md).

* További információ a helyreállítási tár biztonsági mentéséhez való áthelyezéséről: [Recovery Services korlátozások](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
