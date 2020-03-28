---
title: 'PowerShell: Alkalmazásbiztonsági mentés visszaállítása'
description: Megtudhatja, hogyan automatizálhatja az Azure PowerShellt az App Service üzembe helyezésének és felügyeletének automatizálására. Ez a minta bemutatja, hogyan lehet visszaállítani egy alkalmazást egy biztonsági másolatból.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: a10a451bff73c081d9b0c95c494acb8b2051bd84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057729"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Webalkalmazás visszaállítása biztonsági mentésből az Azure PowerShell használatával

Ez a mintaparancsfájl egy korábban befejezett biztonsági mentést kér le egy meglévő webalkalmazásból, és visszaállítja azt a tartalom felülírásával. 

Ha szükséges, telepítse az Azure PowerShell-t az [Azure PowerShell-útmutatóban](/powershell/azure/overview)található utasítás használatával, majd futtassa `Connect-AzAccount` a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha már nincs szüksége a webalkalmazásra, a következő paranccsal távolítsa el az erőforráscsoportot, a webalkalmazást és az összes kapcsolódó erőforrást.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [Visszaállítás-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Egy webalkalmazás visszaállítása egy korábban befejezett biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
