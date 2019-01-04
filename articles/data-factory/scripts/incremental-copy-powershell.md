---
title: 'PowerShell-parancsfájlt: Adatok növekményes betöltése az Azure Data Factory használatával |} A Microsoft Docs'
description: A PowerShell-példaszkript bemutatja, hogyan másolhat adatokat növekményes módon egy Azure SQL Database-ből egy Azure Blob Storage Azure Data Factory használata...
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: a9866061396d3cd7548f7caa2ef0c7b32c1a1980
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015729"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell-szkript – adatok növekményes betöltése az Azure Data Factory használatával
A PowerShell-példaszkript tölt be csak az új és frissített rekord egy forrásadattárból egy fogadó adattárba után az adatok a forrásból a fogadóba kezdeti teljes másolata.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Lásd: [oktatóanyag: növekményes másolat](../tutorial-incremental-copy-powershell.md#prerequisites) a minta futtatása az előfeltételekkel kapcsolatos. 

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a szkript létrehoz, amelyek meghatározzák a Data Factory-entitásokat (társított szolgáltatás, adatkészlet és folyamatot) JSON-fájlokat a c:\ mappába a merevlemezen.

[!code-powershell[main](../../../powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1 "Incremental copy from Azure SQL Database to Azure Blob Storage")]

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
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2pipeline) | Létrehoz egy folyamatot az adat-előállítóban. Egy folyamatot, amely egy bizonyos műveletet hajt végre egy vagy több tevékenységet tartalmaz. Ez a folyamat egy másolási tevékenység adatokat másol egy helyről egy másik helyre egy Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipeline) | A folyamat futtatása hoz létre. Más szóval futtatja a folyamatot. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | A tevékenység (tevékenység-végrehajtásonként), a Futtatás részleteinek beolvasása az adatcsatorna. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell szkriptminták találhatók az [Azure Data Factory PowerShell-parancsfájlok](../samples-powershell.md).
