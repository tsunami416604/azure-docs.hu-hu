---
title: Az Azure PowerShell-szkript minták – kiszámítása blobtároló méretének |} A Microsoft Docs
description: Az Azure Blob storage-tároló méretének kiszámítása a blobok méretének összeadásával.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: d8baec875c25556f1080cdd105c7fa466ffce74e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094007"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Blob Storage-tároló méretének kiszámítása

Ez a szkript kiszámítja egy Azure-blobtároló méretét a tárolóban lévő blobok méretének összeadásával.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A PowerShell-példaszkript a tároló becsült méretét kínál, és nem használható számlázási számításokhoz. A parancsfájl, amely kiszámítja a tárolóméret számlázási célból, lásd: [számlázás szempontjából egy Blob storage-tároló méretének kiszámítása](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a tároló és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja a Blob Storage-tároló méretének kiszámításához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Lekérdezi a megadott tárfiók vagy egy erőforráscsoport vagy előfizetés tárfiókok mindegyikét. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Listázza a tárolóban lévő blobokat. |

## <a name="next-steps"></a>További lépések

A parancsfájl, amely kiszámítja a tárolóméret számlázási célból, lásd: [számlázás szempontjából egy Blob storage-tároló méretének kiszámítása](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További tárolási PowerShell szkriptminták találhatók [PowerShell-minták az Azure Storage](../blobs/storage-samples-blobs-powershell.md).
