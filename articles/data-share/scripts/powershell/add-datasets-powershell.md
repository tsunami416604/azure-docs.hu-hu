---
title: 'PowerShell-parancsfájl: Blob-adatkészlet hozzáadása azure-adatmegosztáshoz | Microsoft dokumentumok'
description: Ez a PowerShell-parancsfájl egy blob-adatkészletet ad hozzá egy meglévő megosztáshoz.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 95a10b112c9f6448c437f20ee95f808632a31d2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307295"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Adatmegosztás létrehozása az Azure-ban a PowerShell használatával

Ez a PowerShell-parancsfájl blob-adatkészletet ad hozzá egy meglévő adatmegosztáshoz.

## <a name="sample-script"></a>Példaszkript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Új-AzDataShareDataSet](/powershell/module/az.datashare/new-azdatasharedataset?view=azps-2.6.0) | Adatkészlet hozzáadása adatmegosztáshoz. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Share PowerShell-parancsfájlminták találhatók az [Azure Data Share PowerShell-mintákban.](../../samples-powershell.md)
