---
title: "Az Azure PowerShell-parancsfájl minta - kiszámításához blob-tároló mérete |} Microsoft Docs"
description: "Az Azure Blob storage-tároló mérete kiszámítása a blobjai méretétől összegzésével."
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: f6f421e780bfbb7922a4b11f758330f2a9a0b84b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>A Blob storage-tároló méretének kiszámítása

Ez a parancsfájl az Azure Blob storage-tároló mérete a tárolóban lévő blobok méretének összegzésével számítja ki.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A PowerShell-parancsfájl egy becsült mérete biztosít a tárolóhoz, és számlázási számítások nem használható. Egy parancsfájl, amely kiszámítja a tároló mérete számlázási okokból, lásd: [egy Blob storage tárolót más célra méretének kiszámításához](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, a tároló és az összes kapcsolódó erőforrások eltávolításához.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a Blob storage tárolót méretének kiszámításához. A tábla hivatkozások parancs vonatkozó dokumentációt összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Lekérdezi a megadott tárfiók vagy a Storage-fiók egy erőforráscsoport vagy az előfizetés mindegyikét. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | A tárolóban lévő blobok sorolja fel. ||

## <a name="next-steps"></a>Következő lépések

Egy parancsfájl, amely kiszámítja a tároló mérete számlázási okokból, lásd: [egy Blob storage tárolót más célra méretének kiszámításához](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További tárhely PowerShell parancsfájl minták található [Azure Storage PowerShell-példák](../blobs/storage-samples-blobs-powershell.md).
