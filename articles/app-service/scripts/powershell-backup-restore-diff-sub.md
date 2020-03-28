---
title: 'PowerShell: Biztonsági mentés visszaállítása egy másik előfizetésbe'
description: Megtudhatja, hogyan automatizálhatja az Azure PowerShellt az App Service üzembe helyezésének és felügyeletének automatizálására. Ez a minta bemutatja, hogyan lehet visszaállítani egy biztonsági mentést egy másik előfizetésben.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 4a22148d4151fd3c80c7be439b5dd281200e5456
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80044575"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Webalkalmazás visszaállítása egy másik előfizetés biztonsági másolatából a PowerShell használatával

Ez a mintaparancsfájl egy korábban befejezett biztonsági mentést kér le egy meglévő webalkalmazásból, és visszaállítja azt egy másik előfizetésben lévő webalkalmazásba. 

Ha szükséges, telepítse az Azure PowerShell-t az [Azure PowerShell-útmutatóban](/powershell/azure/overview)található utasítás használatával, majd futtassa `Connect-AzAccount` a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha már nincs szüksége a webalkalmazásra, a következő paranccsal távolítsa el az erőforráscsoportot, a webalkalmazást és az összes kapcsolódó erőforrást.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Hozzáad egy hitelesített fiókot, amely az Azure Resource Manager-parancsmagok kéréseivel használható.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [Új-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazás létrehozása |
| [Visszaállítás-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Egy webalkalmazás visszaállítása egy korábban befejezett biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
