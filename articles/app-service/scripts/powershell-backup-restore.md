---
title: 'PowerShell: alkalmazás biztonsági másolatának visszaállítása'
description: Megtudhatja, hogyan használhatja a Azure PowerShell a App Service üzembe helyezésének és kezelésének automatizálására. Ez a minta azt mutatja be, hogyan lehet visszaállítani egy alkalmazást egy biztonsági másolatból.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 026c275ce4bc85ff3f964bfbb175cc01ff611df0
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685772"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Webalkalmazás visszaállítása biztonsági másolatból Azure PowerShell használatával

Ez a minta parancsfájl egy korábban befejezett biztonsági mentést kérdez le egy meglévő webalkalmazásból, és felülírja a tartalmát. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha már nincs szüksége a webalkalmazásra, az alábbi paranccsal távolíthatja el az erőforráscsoportot, a webalkalmazást és az összes kapcsolódó erőforrást.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [Visszaállítás – AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Visszaállítja egy webalkalmazást egy korábban befejezett biztonsági másolatból. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
