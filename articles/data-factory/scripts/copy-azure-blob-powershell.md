---
title: 'PowerShell-parancsprogram: adatok másolása a felhőben az Azure Data Factory használatával |} A Microsoft Docs'
description: Ez a PowerShell-szkript adatokat másol egy Azure Blob Storage egyik helyről egy másik helyen ugyanezen a Blobtárolón belül található.
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
ms.openlocfilehash: 7bb6dfcb0c2523b67df32878376cd23af325f606
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42058139"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Adatok másolása a felhőben a data factory-folyamatok létrehozása a PowerShell használatával

A PowerShell-példaszkript létrehoz egy folyamatot az Azure Data Factoryben, amely adatokat másol az egyik helyről egy másik helyre egy Azure Blob Storage.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Azure Storage-fiók** A blobtároló **forrás-** és **fogadó**adattárként lesz használatban. Ha még nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozását](../../storage/common/storage-quickstart-create-account.md) ismertető cikket. 
* Hozzon létre egy **blobtárolót** a Blob Storage alatt, majd hozzon létre egy bemeneti **mappát** a tárolóban, és töltsön fel néhány fájlt a mappába. Az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) és hozzá hasonló eszközökkel csatlakozhat az Azure Blob Storage-hoz, blobtárolókat hozhat létre, bemeneti fájlokat tölthet fel, és ellenőrizheti a kimeneti fájlokat.

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a szkript létrehoz, amelyek meghatározzák a Data Factory-entitásokat (társított szolgáltatás, adatkészlet és folyamatot) JSON-fájlokat a c:\ mappába a merevlemezen.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő parancsot használhatja, ha az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás eltávolítása:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Az erőforráscsoport az adat-előállító eltávolításához futtassa a következő parancsot: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Az adat-előállító létrehoz egy társított szolgáltatást. A társított szolgáltatás hivatkozik egy adattárat vagy számítási, adat-előállító. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Az adat-előállító egy adatkészletet hoz létre. Adatkészlet egy folyamat egyik tevékenységének bemeneti/kimeneti jelöli. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Létrehoz egy folyamatot az adat-előállítóban. Egy folyamatot, amely egy bizonyos műveletet hajt végre egy vagy több tevékenységet tartalmaz. Ez a folyamat egy másolási tevékenység adatokat másol egy helyről egy másik helyre egy Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | A folyamat futtatása hoz létre. Más szóval futtatja a folyamatot. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | A tevékenység (tevékenység-végrehajtásonként), a Futtatás részleteinek beolvasása az adatcsatorna. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell szkriptminták találhatók az [Azure Data Factory PowerShell-minták](../samples-powershell.md).