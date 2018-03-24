---
title: 'PowerShell-parancsprogram: adatok másolása a felhőben Azure Data Factory használatával |} Microsoft Docs'
description: A PowerShell parancsfájl adatok egyik helyről egy Azure Blob Storage tárolóban ugyanazt a Blob Storage tárolóban egy másik helyre másolja.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: jingwang
ms.openlocfilehash: 0b7552bb981a5b5a4850778709c08323b0e9124f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>A data factory-folyamathoz adatok másolása a felhőben létrehozása a PowerShell használatával

A PowerShell-parancsfájlpélda egy folyamatot az Azure Data Factoryben, másolja az adatokat egyik helyről egy másik helyre egy Azure Blob Storage tárolóban hoz létre.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Azure Storage-fiók** A blobtároló **forrás-** és **fogadó**adattárként lesz használatban. Ha még nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozását](../../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. 
* Hozzon létre egy **blobtárolót** a Blob Storage alatt, majd hozzon létre egy bemeneti **mappát** a tárolóban, és töltsön fel néhány fájlt a mappába. Az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) és hozzá hasonló eszközökkel csatlakozhat az Azure Blob Storage-hoz, blobtárolókat hozhat létre, bemeneti fájlokat tölthet fel, és ellenőrizheti a kimeneti fájlokat.

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ezt a parancsfájlt a merevlemezen a c:\ mappában hoz létre, amelyek meghatározzák a Data Factory entitások (a társított szolgáltatás, a dataset és a feldolgozási sor) JSON-fájlokat.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A minta-parancsprogram futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrás:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Az erőforráscsoport az adat-előállítóban eltávolításához futtassa a következő parancsot: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Létrehoz egy csatolt szolgáltatást az adat-előállítóban. A társított szolgáltatás egy adat-előállító egy számítási vagy az adattárban hivatkozásokat tartalmaz. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | A data factory dataset hoz létre. A DataSet adatkészlet egy folyamat egy tevékenység bemeneti jelöli. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Az adat-előállítóban hoz létre egy folyamatot. Egy folyamatot, amely bizonyos műveletet hajt végre egy vagy több tevékenységet tartalmaz. Az adatcsatorna a másolási tevékenység adatainak másolása egyik helyről egy Azure Blob Storage tárolóban egy másik helyre. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | A tölcsér futtató hoz létre. Ez azt jelenti futtatja a folyamatot. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Lekérdezi a futtatáskor a tevékenység (tevékenységfuttatási) adatait a feldolgozási. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell parancsfájl minták megtalálhatók a [Azure Data Factory PowerShell-példák](../samples-powershell.md).