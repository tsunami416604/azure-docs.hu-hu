---
title: Blob-tároló méretének kiszámítása a PowerShell használatával
titleSuffix: Azure Storage
description: Számítsa ki egy tároló méretét az Azure Blob storage-ban az egyes blobok méretének összesítésével.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: de51ed7d91ba1102f5a9cd376ab95f49dd54d9f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067078"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Blob-tároló méretének kiszámítása a PowerShell használatával

Ez a szkript kiszámítja egy Azure-blobtároló méretét a tárolóban lévő blobok méretének összeadásával.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ez a PowerShell-parancsfájl a tároló becsült méretét biztosítja, és nem használható számlázási számításokhoz. A tároló méretét számlázási célokra kiszámító parancsfájlról a [Blob storage-tároló méretének kiszámítása számlázási célokra című témakörben.](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)

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
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Beszerzi a megadott tárfiókot vagy az erőforráscsoport vagy az előfizetés összes Tárfiókját. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | A tárolóban lévő blobok listázása. |

## <a name="next-steps"></a>További lépések

A tároló méretét számlázási célokra kiszámító parancsfájlról a [Blob storage-tároló méretének kiszámítása számlázási célokra című témakörben.](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További tárolási PowerShell-parancsfájlminták találhatók az [Azure Storage PowerShell-mintáiban.](../blobs/storage-samples-blobs-powershell.md)
