---
title: Azure PowerShell-példaszkript – Szolgáltatás biztonsági mentése és visszaállítása | Microsoft Docs
description: Azure PowerShell-példaszkript – Szolgáltatás biztonsági mentése és visszaállítása
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 1c56aefb7b30026d1676e8a782fe2f6d394bd1ca
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008980"
---
# <a name="backup-and-restore-service"></a>Szolgáltatás biztonsági mentése és visszaállítása

A cikkben látható minta az API Management-szolgáltatáspéldány biztonsági mentését és visszaállítását mutatja be. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell modul 1,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/api-management/backup-restore-apim-service/backup_restore_apim_service.ps1 "Backup and restore the APIM service instance")]

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

Az Azure API Managementhez a [PowerShell-minták](../powershell-samples.md) között találhat további Azure PowerShell-mintákat.
