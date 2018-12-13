---
title: Azure PowerShell-példaszkript – Webalkalmazás biztonsági másolatának törlése | Microsoft Docs
description: Azure PowerShell-példaszkript – Webalkalmazás biztonsági másolatának törlése
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 4a2abe05b6781358355cc264b66673671bc52557
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185647"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Azure PowerShell-lel webes biztonsági másolat törlése

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd egyszeri biztonsági mentést hajt végre. 

A szkript futtatásához szükség van egy webalkalmazás meglévő biztonsági másolatára. Ennek létrehozásáról lásd: [Webalkalmazások biztonsági mentése](app-service-powershell-backup-onetime.md) vagy [Ütemezett biztonsági másolat létrehozása egy webalkalmazásról](app-service-powershell-backup-scheduled.md).

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a webalkalmazás és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [Remove-AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Eltávolítja a webalkalmazás megadott biztonsági másolatát. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure App Service Web Appshez az [Azure PowerShell-példák](../app-service-powershell-samples.md) között találhat.
