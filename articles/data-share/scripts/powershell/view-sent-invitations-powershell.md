---
title: 'PowerShell-parancsfájl: A fogyasztónak küldött Azure-adatmegosztási meghívók listázása | Microsoft dokumentumok'
description: Ez a PowerShell-parancsfájl egy meglévő adatmegosztásból származó felkéréseket fogad el.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 157b844afd9d59b6c03c7ae8585bc2182370d3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307151"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Adatmegosztási meghívó bekérése a PowerShell használatával

Ez a PowerShell-parancsfájl meghívókat kap egy fogyasztónak.

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

| Parancs | Megjegyzések |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Az elküldött adatmegosztási meghívók bekérése és listázása. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Share PowerShell-parancsfájlminták találhatók az [Azure Data Share PowerShell-mintákban.](../../samples-powershell.md)
