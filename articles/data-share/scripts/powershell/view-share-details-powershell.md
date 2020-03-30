---
title: 'PowerShell-parancsfájl: Meglévő megosztások listázása az Azure Data Share-ben | Microsoft dokumentumok'
description: Ez a PowerShell-parancsfájl felsorolja és megjeleníti a megosztások részleteit.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307127"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Az elküldött adatmegosztás részleteinek megtekintése a PowerShell használatával

Ez a PowerShell-parancsfájl egy meglévő fiók adatmegosztásait sorolja fel, és egy adott megosztás részleteit kapja meg.


## <a name="sample-script"></a>Példaszkript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Leveszi és listázza a részvényeket egy számlán. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Share PowerShell-parancsfájlminták találhatók az [Azure Data Share PowerShell-mintákban.](../../samples-powershell.md)
