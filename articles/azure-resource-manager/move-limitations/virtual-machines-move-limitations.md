---
title: Azure virtuális gépek áthelyezése új előfizetést vagy az erőforrás-csoport |} A Microsoft Docs
description: Azure Resource Manager használatával virtuális gépek áthelyezése új erőforráscsoportba vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723544"
---
# <a name="move-guidance-for-virtual-machines"></a>Útmutató a virtuális gépek áthelyezése

Ez a cikk ismerteti, hogy jelenleg nem támogatottak, és a lépések végrehajtásával helyezhetők át a virtuális gépek biztonsági mentéssel.

## <a name="scenarios-not-supported"></a>Nem támogatott forgatókönyvek

A következő esetekben még nem támogatottak:

* Felügyelt lemezek a rendelkezésre állási zónák nem helyezhető át másik előfizetésbe.
* Egy új erőforráscsoport ugyanabban az előfizetésben, de az előfizetések között nem helyezheti át virtuális gépeket a Key Vault-tanúsítvánnyal.
* Virtual Machine Scale Sets Standard Termékváltozatú terheléselosztó vagy a Standard Termékváltozat nyilvános IP-cím nem lehet áthelyezni.
* A csatolt tervek Piactéri erőforrások alapján létrehozott virtuális gépeken nem lehet áthelyezni, erőforráscsoport vagy előfizetés között. Az aktuális előfizetésben a virtuális gép megszüntetése, és telepítse újra az új előfizetés.
* Virtuális gépek a meglévő virtuális hálózattal, de nem kerül át az összes erőforrás a virtuális hálózatban.

## <a name="virtual-machines-with-azure-backup"></a>Virtuális gépek az Azure Backup szolgáltatással

Az Azure Backup szolgáltatással konfigurált virtuális gépek áthelyezéséhez használja a következő megkerülő megoldás:

* Keresse meg a virtuális gép helyét.
* Keresse meg a következő elnevezési mintának egy erőforráscsoportot: `AzureBackupRG_<location of your VM>_1` például AzureBackupRG_westus2_1
* Ha az Azure Portalon, majd ellenőrizze "rejtett típusok megjelenítése"
* Ha a PowerShellben használja a `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` parancsmag
* Ha a CLI-t, használja a `az resource list -g AzureBackupRG_<location of your VM>_1`
* Keresse meg az erőforrást típusú `Microsoft.Compute/restorePointCollections` , amely rendelkezik az elnevezési minta `AzureBackup_<name of your VM that you're trying to move>_###########`
* Törli ezt az erőforrást. Ez a művelet csak az azonnali helyreállítási pontjait, nem a tárolóban lévő biztonsági mentési adatok törlése.
* Miután befejeződött a törlés, áthelyezheti a tároló és a virtuális gép a célként megadott előfizetés. Az áthelyezés után az adatok biztonsági mentések adatvesztés nélkül folytathatja.
* Biztonsági mentés helyez át a helyreállítási szolgáltatás tárolók kapcsolatos információkért lásd: [Recovery Servicesre vonatkozó korlátozásokat](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>További lépések

Erőforrások áthelyezése parancsokért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../resource-group-move-resources.md).
