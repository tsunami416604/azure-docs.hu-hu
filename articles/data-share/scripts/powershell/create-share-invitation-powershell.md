---
title: 'PowerShell-parancsfájl: Azure-beli adatmegosztási meghívás létrehozása | Microsoft Docs'
description: Ez a PowerShell-parancsfájl adatmegosztási meghívást küld.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9fd8d6428e94007002d524d9ade99f6b368b8201
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307231"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Az elküldett adatmegosztások használatának figyelése a PowerShell használatával

Ez a PowerShell-szkript létrehoz egy adatmegosztási meghívást.

## <a name="sample-script"></a>Példaszkript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Új – AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | Hozzon létre egy adatmegosztási meghívást. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
