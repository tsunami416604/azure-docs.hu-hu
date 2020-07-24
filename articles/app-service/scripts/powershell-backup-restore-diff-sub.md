---
title: 'PowerShell: biztonsági másolat visszaállítása másik előfizetésre'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta azt mutatja be, hogyan lehet visszaállítani egy másik előfizetésben lévő biztonsági mentést.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: c9dda1f3e011006d1b5072c5b71accb28963aedb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004868"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Webalkalmazás visszaállítása egy másik előfizetésben lévő biztonsági másolatból a PowerShell használatával

Ez a parancsfájl egy korábban befejezett biztonsági mentést kérdez le egy meglévő webalkalmazásból, és visszaállítja egy másik előfizetésben lévő webalkalmazásba. 

Szükség esetén telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/)található utasítással, majd futtassa a parancsot az `Connect-AzAccount` Azure-hoz való kapcsolódáshoz. 

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha már nincs szüksége a webalkalmazásra, az alábbi paranccsal távolíthatja el az erőforráscsoportot, a webalkalmazást és az összes kapcsolódó erőforrást.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Hozzáad egy hitelesített fiókot, amely az Azure Resource Manager-parancsmagok kéréseivel használható.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [Új – AzWebApp](/powershell/module/az.websites/new-azwebapp) | Létrehoz egy webalkalmazást |
| [Visszaállítás – AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Visszaállítja egy webalkalmazást egy korábban befejezett biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
