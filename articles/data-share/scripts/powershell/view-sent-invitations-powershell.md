---
title: 'PowerShell-parancsfájl: a fogyasztóknak küldött Azure-beli adatmegosztási meghívások listázása | Microsoft Docs'
description: Megtudhatja, hogyan kapja meg a PowerShell-parancsfájl a fogyasztóknak küldött meghívókat, és megtekintheti a használható szkriptet.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 3f0374ba5b98c145359d18143584e23fc543c552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985627"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Adatmegosztási meghívás beszerzése a PowerShell használatával

Ez a PowerShell-parancsfájl lekéri a fogyasztóknak küldött meghívókat.

## <a name="sample-script"></a>Példaszkript
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Az elküldött adatmegosztási meghívások beolvasása és listázása |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
