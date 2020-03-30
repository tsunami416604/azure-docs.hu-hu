---
title: 'PowerShell-parancsfájl: Új Azure Data Share-fiók létrehozása | Microsoft dokumentumok'
description: Ez a PowerShell-parancsfájl új adatmegosztási fiókot hoz létre.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307273"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Adatmegosztási fiók létrehozása az Azure-ban a PowerShell használatával

Ez a PowerShell-parancsfájl új adatmegosztási fiókot hoz létre. 

## <a name="sample-script"></a>Példaszkript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Új-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | Adatmegosztási fiókot hoz létre. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Share PowerShell-parancsfájlminták találhatók az [Azure Data Share PowerShell-mintákban.](../../samples-powershell.md)
