---
title: Növekményes adatok betöltése a PowerShell használatával
description: Ez a PowerShell-parancsfájl bemutatja, hogyan használhatja az Azure Data Factory segítségével az Azure SQL-adatbázisból az Azure Blob Storage-ba növekményes adatok másolása.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 1919f89d2e39981effe14c1203446f8f9d930f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79462489"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell-parancsfájl – Növekményes adatok betöltése az Azure Data Factory használatával

Ez a minta PowerShell-parancsfájl csak új vagy frissített rekordokat tölt be egy forrás-adattárból egy fogadó adattárba a forrásból a fogadóba származó adatok kezdeti teljes másolása után.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Lásd: [növekményes másolat](../tutorial-incremental-copy-powershell.md#prerequisites) a minta futtatásának előfeltételei. 

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a parancsfájl JSON-fájlokat hoz létre, amelyek a c:\ Mappa.

:::code language="powershell" source="~/powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1":::

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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2Linkedservice](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Csatolt szolgáltatást hoz létre az adat-előállítóban. A csatolt szolgáltatás adattort vagy számítást kapcsol össze egy adat-előállítóval. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Adatkészletet hoz létre az adat-előállítóban. Az adatkészlet egy folyamatban lévő tevékenység bemeneti/kimeneti bemenetét/kimenetét jelöli. | 
| [Set-AzDataFactoryV2pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Létrehoz egy folyamatot az adat-előállítóban. A folyamat egy vagy több olyan tevékenységet tartalmaz, amelyek egy bizonyos műveletet hajtanak végre. Ebben a folyamatban egy másolási tevékenység adatokat másol az egyik helyről egy másik helyre egy Azure Blob Storage.In this pipeline, a copy activity copys copys copys data from one location in an Azure Blob Storage. |
| [Invoke-AzDataFactoryV2pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Futást hoz létre a folyamathoz. Más szóval fut a folyamat. |
| [Get-AzDataFactoryV2Activityrun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | A folyamatban lévő tevékenység (tevékenységfuttatás) futtatásának részletei. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell-parancsfájlminták találhatók az [Azure Data Factory PowerShell-parancsfájlokban.](../samples-powershell.md)
