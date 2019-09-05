---
title: 'PowerShell-parancsfájl: Meghívás elfogadása Azure-adatmegosztásból | Microsoft Docs'
description: Ez a PowerShell-parancsfájl egy meglévő adatmegosztásból fogadja a meghívókat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9ef35e0865a7c5bbfb4accc058a8d63bb6f1e80b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243073"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Adatmegosztási meghívás elfogadása a PowerShell használatával

Ez a PowerShell-parancsfájl fogadja a fogyasztóknak küldött meghívókat.

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
| [Get-AzDataShareInvitation](/powershell/module/az.resources/get-azdatashareinvitation) | Az elküldött adatmegosztási meghívások beolvasása és listázása |
| [Új – AzDataShareSubscription](/powershell/module/az.resources/get-azdatashareinvitation) | Hozzon létre egy adatmegosztási előfizetést. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.

