---
title: Az Azure PowerShell-Példaszkript – webalkalmazás biztonsági visszaállítása |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – webalkalmazás biztonsági visszaállítása
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fe1ac9f445434507c65f87fcd423eccb1a4ffacc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136606"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Webalkalmazás visszaállítása biztonsági másolatból Azure PowerShell-lel

Ez a példaszkript egy korábban elvégzett biztonsági mentés beolvassa egy meglévő web Apps, és annak tartalmára felülírásával visszaállítására. 

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Ha többé már nincs szüksége a webalkalmazást, a következő paranccsal törölheti az erőforráscsoportot, a webalkalmazás, és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Webalkalmazás visszaállítása egy korábban elvégzett biztonsági másolatból. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../samples-powershell.md) között találhat.
