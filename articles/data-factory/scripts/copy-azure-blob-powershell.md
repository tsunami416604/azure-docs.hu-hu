---
title: "PowerShell-parancsprogram: adatok másolása a felhőben Azure Data Factory használatával |} Microsoft Docs"
description: "A PowerShell parancsfájl adatok egyik helyről egy Azure Blob Storage tárolóban ugyanazt a Blob Storage tárolóban egy másik helyre másolja."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.openlocfilehash: c564491e0f30e0e898bc03eecb29710edeb267f6
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>A data factory-folyamathoz adatok másolása a felhőben létrehozása a PowerShell használatával

A PowerShell-parancsfájlpélda egy folyamatot az Azure Data Factoryben, másolja az adatokat egyik helyről egy másik helyre egy Azure Blob Storage tárolóban hoz létre.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Azure Storage-fiók** A blobtároló **forrás-** és **fogadó**adattárként lesz használatban. Ha még nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozását](../../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. 
* Hozzon létre egy **blobtárolót** a Blob Storage alatt, majd hozzon létre egy bemeneti **mappát** a tárolóban, és töltsön fel néhány fájlt a mappába. Az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) és hozzá hasonló eszközökkel csatlakozhat az Azure Blob Storage-hoz, blobtárolókat hozhat létre, bemeneti fájlokat tölthet fel, és ellenőrizheti a kimeneti fájlokat.

## <a name="sample-script"></a>Mintaparancsfájl

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

## <a name="script-explanation"></a>Parancsfájl ismertetése

Ezt a parancsfájlt az alábbi parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Létrehoz egy csatolt szolgáltatást az adat-előállítóban. A társított szolgáltatás egy adat-előállító egy számítási vagy az adattárban hivatkozásokat tartalmaz. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | A data factory dataset hoz létre. A DataSet adatkészlet egy folyamat egy tevékenység bemeneti jelöli. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Az adat-előállítóban hoz létre egy folyamatot. Egy folyamatot, amely bizonyos műveletet hajt végre egy vagy több tevékenységet tartalmaz. Az adatcsatorna a másolási tevékenység adatainak másolása egyik helyről egy Azure Blob Storage tárolóban egy másik helyre. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | A tölcsér futtató hoz létre. Ez azt jelenti futtatja a folyamatot. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Lekérdezi a futtatáskor a tevékenység (tevékenységfuttatási) adatait a feldolgozási. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell további információkért lásd: [Azure PowerShell dokumentációs](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell parancsfájl minták megtalálhatók a [Azure Data Factory PowerShell-példák](../samples-powershell.md).