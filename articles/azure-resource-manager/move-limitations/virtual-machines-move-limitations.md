---
title: Azure-Virtual Machines áthelyezése új előfizetésre vagy erőforráscsoporthoz | Microsoft Docs
description: A Azure Resource Manager használatával áthelyezheti a virtuális gépeket egy új erőforráscsoporthoz vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 7b9cce7ac367f42329e3198c75a7640a205d01fe
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035527"
---
# <a name="move-guidance-for-virtual-machines"></a>Útmutató a virtuális gépekhez

Ez a cikk a jelenleg nem támogatott forgatókönyveket és a virtuális gépek biztonsági mentéssel való áthelyezésének lépéseit ismerteti.

## <a name="scenarios-not-supported"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek még nem támogatottak:

* A Availability Zones Managed Disks nem helyezhető át másik előfizetésbe.
* A standard SKU Load Balancer vagy standard SKU nyilvános IP-címmel rendelkező Virtual Machine Scale Sets nem helyezhető át.
* A csatolt tervek Piactéri erőforrások alapján létrehozott virtuális gépeken nem lehet áthelyezni, erőforráscsoport vagy előfizetés között. Helyezze el a virtuális gépet a jelenlegi előfizetésben, majd telepítse újra az új előfizetésben.
* Virtuális gépek egy meglévő virtuális hálózaton, de nem helyezi át a virtuális hálózatban lévő összes erőforrást.
* Az alacsony prioritású virtuális gépek és az alacsony prioritású virtuálisgép-méretezési csoportok nem helyezhetők át az erőforráscsoportok vagy az előfizetések között.

## <a name="virtual-machines-with-azure-backup"></a>Virtuális gépek Azure Backup

Azure Backup használatával konfigurált virtuális gépek áthelyezéséhez használja a következő megkerülő megoldást:

* Keresse meg a virtuális gép helyét.
* Keressen egy erőforráscsoportot a következő elnevezési mintával: `AzureBackupRG_<location of your VM>_1` például AzureBackupRG_westus2_1
* Ha az Azure Portalon, majd ellenőrizze "rejtett típusok megjelenítése"
* Ha a PowerShellben használja a `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` parancsmag
* Ha a CLI-t, használja a `az resource list -g AzureBackupRG_<location of your VM>_1`
* Keresse meg azt az erőforrást, amelynek a típusa `Microsoft.Compute/restorePointCollections` az elnevezési minta`AzureBackup_<name of your VM that you're trying to move>_###########`
* Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.
* A törlés befejezése után áthelyezheti a tárolót és a virtuális gépet a cél előfizetésbe. Az áthelyezést követően folytathatja a biztonsági mentéseket az adatvesztés hiányában.
* További információ a helyreállítási tár biztonsági mentéséhez való áthelyezéséről: [Recovery Services korlátozások](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy](../resource-group-move-resources.md)előfizetésbe.
