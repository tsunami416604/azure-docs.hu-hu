---
title: 'PowerShell-parancsfájl: új Azure-adatmegosztás létrehozása | Microsoft Docs'
description: Ez a PowerShell-parancsfájl egy új adatmegosztást hoz létre egy meglévő adatmegosztási fiókon belül.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307248"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Adatmegosztás létrehozása az Azure-ban a PowerShell használatával

Ez a PowerShell-parancsfájl egy új adatmegosztást hoz létre egy meglévő adatmegosztási fiókon belül.

## <a name="sample-script"></a>Példaszkript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Új – AzDataShare](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Létrehoz egy adatmegosztást. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
