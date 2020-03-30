---
title: 'PowerShell-parancsfájl: Meghívás elfogadása Azure-adatmegosztásból | Microsoft dokumentumok'
description: Ez a PowerShell-parancsfájl egy meglévő adatmegosztásból származó felkéréseket fogad el.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307333"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Adatmegosztási meghívás elfogadása a PowerShell használatával

Ez a PowerShell-parancsfájl elfogadja a fogyasztónak küldött meghívókat.

## <a name="sample-script"></a>Példaszkript
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Az elküldött adatmegosztási meghívók bekérése és listázása. |
| [Új-AzDataShare-előfizetés](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | Adatmegosztási előfizetés létrehozása. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Share PowerShell-parancsfájlminták találhatók az [Azure Data Share PowerShell-mintákban.](../../samples-powershell.md)

