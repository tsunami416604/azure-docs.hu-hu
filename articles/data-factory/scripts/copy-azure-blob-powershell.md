---
title: 'PowerShell-parancsfájlt: Adatok másolása a felhőben az Azure Data Factory használatával |} A Microsoft Docs'
description: Ez a PowerShell-szkript adatokat másol egy Azure Blob Storage egyik helyről egy másik helyen ugyanezen a Blobtárolón belül található.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: jingwang
ms.openlocfilehash: 19cb37ea40daa1e416f0dea82b2ddc8ad107454c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436553"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Adatok másolása a felhőben a data factory-folyamatok létrehozása a PowerShell használatával

A PowerShell-példaszkript létrehoz egy folyamatot az Azure Data Factoryben, amely adatokat másol az egyik helyről egy másik helyre egy Azure Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Az adat-előállító létrehoz egy társított szolgáltatást. A társított szolgáltatás hivatkozik egy adattárat vagy számítási, adat-előállító. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Az adat-előállító egy adatkészletet hoz létre. Adatkészlet egy folyamat egyik tevékenységének bemeneti/kimeneti jelöli. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Létrehoz egy folyamatot az adat-előállítóban. Egy folyamatot, amely egy bizonyos műveletet hajt végre egy vagy több tevékenységet tartalmaz. Ez a folyamat egy másolási tevékenység adatokat másol egy helyről egy másik helyre egy Azure Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | A folyamat futtatása hoz létre. Más szóval futtatja a folyamatot. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | A tevékenység (tevékenység-végrehajtásonként), a Futtatás részleteinek beolvasása az adatcsatorna. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell szkriptminták találhatók az [Azure Data Factory PowerShell-minták](../samples-powershell.md).
