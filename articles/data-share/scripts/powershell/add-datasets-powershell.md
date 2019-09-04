---
title: 'PowerShell-parancsfájl: BLOB-adatkészlet hozzáadása Azure-adatmegosztáshoz | Microsoft Docs'
description: Ez a PowerShell-parancsfájl egy blob-adatkészletet rendel hozzá egy meglévő megosztáshoz.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: ef027387d6c8e119c03fef6e4e5d7fd7a4125e51
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243034"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Adatmegosztás létrehozása az Azure-ban a PowerShell használatával

Ez a PowerShell-parancsfájl egy blob-adatkészletet rendel hozzá egy meglévő adatmegosztáshoz.

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
| [Új – AzDataShareDataSet](/powershell/module/az.resources/new-azdatasharedataset) | Adathalmazt rendel egy adatmegosztáshoz. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure-adatmegosztási PowerShell-szkriptek az Azure-beli [adatmegosztás PowerShell-mintáinak](../../samples-powershell.md)használatával találhatók.
