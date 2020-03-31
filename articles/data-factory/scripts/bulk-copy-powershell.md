---
title: Adatok tömeges másolása a PowerShell használatával
description: Ez a PowerShell-parancsfájl bemutatja, hogyan lehet az Azure Data Factory használatával adatokat másolni egy forrásadattárból tömegesen a céladattárba.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: efc79f82a2181099f832da0d4a17fc370bf4f7f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929866"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell-parancsfájl – több tábla másolása tömegesen az Azure Data Factory használatával

Ez a minta PowerShell-parancsfájl adatokat másol egy Azure SQL-adatbázis több táblából egy Azure SQL-adattárházba.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Lásd: [tömeges másolat](../tutorial-bulk-copy.md#prerequisites) a minta futtatásának előfeltételeit.

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a parancsfájl JSON-fájlokat hoz létre, amelyek a c:\ Mappa.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A mintaparancsfájl futtatása után a következő paranccsal eltávolíthatja az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Ha el szeretné távolítani az adat-előállítót az erőforráscsoportból, futtassa a következő parancsot: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2Linkedservice](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Csatolt szolgáltatást hoz létre az adat-előállítóban. A csatolt szolgáltatás adattort vagy számítást kapcsol össze egy adat-előállítóval. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Adatkészletet hoz létre az adat-előállítóban. Az adatkészlet egy folyamatban lévő tevékenység bemeneti/kimeneti bemenetét/kimenetét jelöli. | 
| [Set-AzDataFactoryV2pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Létrehoz egy folyamatot az adat-előállítóban. A folyamat egy vagy több olyan tevékenységet tartalmaz, amely egy bizonyos műveletet hajt végre. Ebben a folyamatban egy másolási tevékenység adatokat másol az egyik helyről egy másik helyre egy Azure Blob Storage.In this pipeline, a copy activity copys copys copys data from one location in an Azure Blob Storage. |
| [Invoke-AzDataFactoryV2pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Futást hoz létre a folyamathoz. Más szóval fut a folyamat. |
| [Get-AzDataFactoryV2Activityrun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | A folyamatban lévő tevékenység (tevékenységfuttatás) futtatásának részletei. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell-parancsfájlminták találhatók az [Azure Data Factory PowerShell-parancsfájlokban.](../samples-powershell.md)
