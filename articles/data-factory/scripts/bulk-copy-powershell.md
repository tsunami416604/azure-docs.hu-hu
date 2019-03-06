---
title: 'PowerShell-parancsfájlt: Adatok tömeges másolása az Azure Data Factory használatával |} A Microsoft Docs'
description: Ez a PowerShell-szkript az Azure Data Factory használatával adatokat másol egy forrásadattárból egy céladattárba tömeges mutatja be.
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
ms.openlocfilehash: 5be5090af945885bc44fb9ee27d6925b8633bfef
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446554"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell parancsfájl – több táblák tömeges másolása az Azure Data Factory használatával

A PowerShell-példaszkript adatokat másol egy Azure SQL data warehouse több táblájából egy Azure SQL database-ben.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Lásd: [oktatóanyag: a tömeges másolás](../tutorial-bulk-copy.md#prerequisites) a minta futtatása az előfeltételekkel kapcsolatos.

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a szkript létrehoz, amelyek meghatározzák a Data Factory-entitásokat (társított szolgáltatás, adatkészlet és folyamatot) JSON-fájlokat a c:\ mappába a merevlemezen.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő parancsot használhatja, ha az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás eltávolítása:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Az erőforráscsoport az adat-előállító eltávolításához futtassa a következő parancsot: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-Azdatafactoryv2linkedservice) | Az adat-előállító létrehoz egy társított szolgáltatást. A társított szolgáltatás hivatkozik egy adattárat vagy számítási, adat-előállító. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-Azdatafactoryv2dataset) | Az adat-előállító egy adatkészletet hoz létre. Adatkészlet egy folyamat egyik tevékenységének bemeneti/kimeneti jelöli. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-Azdatafactoryv2pipeline) | Létrehoz egy folyamatot az adat-előállítóban. Egy folyamatot, amely egy bizonyos műveletet hajt végre egy vagy több tevékenységet tartalmaz. Ez a folyamat egy másolási tevékenység adatokat másol egy helyről egy másik helyre egy Azure Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-Azdatafactoryv2pipeline) | A folyamat futtatása hoz létre. Más szóval futtatja a folyamatot. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-Azdatafactoryv2activityrun) | A tevékenység (tevékenység-végrehajtásonként), a Futtatás részleteinek beolvasása az adatcsatorna. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell szkriptminták találhatók az [Azure Data Factory PowerShell-parancsfájlok](../samples-powershell.md).
