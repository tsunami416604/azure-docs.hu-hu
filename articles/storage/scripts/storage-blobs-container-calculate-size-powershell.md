---
title: BLOB-tároló méretének kiszámítása a PowerShell-lel
titleSuffix: Azure Storage
description: Egy tároló méretének kiszámítása az Azure Blob Storage-ban az egyes Blobok méretének összesítésével.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 890da96ac1d97a2eb84f5296c74a02824d657273
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895044"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>BLOB-tároló méretének kiszámítása a PowerShell-lel

Ez a szkript kiszámítja egy Azure-blobtároló méretét a tárolóban lévő blobok méretének összeadásával.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ez a PowerShell-parancsfájl a tároló becsült méretét biztosítja, és nem használható a számlázási számításokhoz. Egy olyan parancsfájl esetében, amely kiszámítja a tároló méretét a számlázási célokra, tekintse meg [a blob Storage-tároló méretének kiszámítása számlázási célokra](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)című témakört. 

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
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | A megadott Storage-fiók vagy az erőforráscsoport vagy az előfizetés összes tárolási fiókjának beolvasása. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Egy tárolóban lévő Blobok felsorolása. |

## <a name="next-steps"></a>Következő lépések

Egy olyan parancsfájl esetében, amely kiszámítja a tároló méretét a számlázási célokra, tekintse meg [a blob Storage-tároló méretének kiszámítása számlázási célokra](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)című témakört.

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Storage PowerShell-szkriptek az Azure Storage-hoz készült [PowerShell-mintákban](../blobs/storage-samples-blobs-powershell.md)találhatók.
