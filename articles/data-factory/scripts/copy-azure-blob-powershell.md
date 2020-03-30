---
title: Adatok másolása a felhőben a PowerShell használatával
description: Ez a PowerShell-parancsfájl adatokat másol az Egyik helyről egy Azure Blob Storage egy másik helyre ugyanabban a Blob Storage-ban.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 2e289f30ef1c1883c38884eb563a41bccc841329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79462651"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>A PowerShell használatával adatfeldolgozó folyamatot hozhat létre a felhőben lévő adatok másolásához

Ez a minta PowerShell-parancsfájl létrehoz egy folyamatot az Azure Data Factoryban, amely adatokat másol az egyik helyről egy másik helyre egy Azure Blob Storage-ban.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Az Azure Storage-fiók.** A blobtároló **forrás-** és **fogadó**adattárként lesz használatban. Ha még nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozását](../../storage/common/storage-account-create.md) ismertető cikket. 
* Hozzon létre egy **blobtárolót** a Blob Storage alatt, majd hozzon létre egy bemeneti **mappát** a tárolóban, és töltsön fel néhány fájlt a mappába. Az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) és hozzá hasonló eszközökkel csatlakozhat az Azure Blob Storage-hoz, blobtárolókat hozhat létre, bemeneti fájlokat tölthet fel, és ellenőrizheti a kimeneti fájlokat.

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a parancsfájl JSON-fájlokat hoz létre, amelyek a c:\ Mappa.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


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

További Azure Data Factory PowerShell-parancsfájlminták találhatók az [Azure Data Factory PowerShell-mintákban.](../samples-powershell.md)
