---
title: Azure-beli virtuális gépek áthelyezése új előfizetésre vagy erőforráscsoport-csoportba
description: A Azure Resource Manager használatával áthelyezheti a virtuális gépeket egy új erőforráscsoporthoz vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tomfitz
ms.openlocfilehash: faeba1c0d7342a4c00f19d4cee8d67b8dbde8e6a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528426"
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

Azure Backup használatával konfigurált virtuális gépek áthelyezéséhez használja a következő megkerülő megoldást:

* Keresse meg a virtuális gép helyét.
* Keressen egy erőforráscsoportot a következő elnevezési mintával: `AzureBackupRG_<location of your VM>_1` például AzureBackupRG_westus2_1
* Ha Azure Portal, akkor jelölje be a "rejtett típusok megjelenítése" lehetőséget.
* Ha a PowerShellben használja a `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` parancsmagot
* Ha a CLI-ben használja a `az resource list -g AzureBackupRG_<location of your VM>_1`
* Keresse meg az erőforrást olyan típusú `Microsoft.Compute/restorePointCollections`, amelynek elnevezési mintája `AzureBackup_<name of your VM that you're trying to move>_###########`
* Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.
* A törlés befejezése után áthelyezheti a tárolót és a virtuális gépet a cél előfizetésbe. Az áthelyezést követően folytathatja a biztonsági mentéseket az adatvesztés hiányában.
* További információ a helyreállítási tár biztonsági mentéséhez való áthelyezéséről: [Recovery Services korlátozások](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Következő lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../resource-group-move-resources.md).
