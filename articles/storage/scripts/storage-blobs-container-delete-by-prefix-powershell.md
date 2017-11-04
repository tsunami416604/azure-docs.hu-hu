---
title: "Az Azure PowerShell-parancsfájl minta - törlés tárolókban előtag alapján |} Microsoft Docs"
description: "Azure Storage-blob tárolók egy tárolója előtagján alapuló törlése."
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: robinsh
ms.openlocfilehash: 402958c4e2978630bc79557704a77e77a8b9a7e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Tárolója előtagján alapuló tárolók törlése

Ez a parancsfájl törli a tároló neve előtag alapján Azure Blob Storage tárolók.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Távolítsa el az erőforráscsoportot, a fennmaradó tárolók, hogy a következő parancsot, és az összes kapcsolódó erőforrásokat.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsok tárolója előtagján alapuló tárolók törlése. A tábla hivatkozások parancs vonatkozó dokumentációt összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Lekérdezi a megadott tárfiók vagy a Storage-fiók egy erőforráscsoport vagy az előfizetés mindegyikét. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | A storage-fiókhoz kapcsolódó storage tárolók listája. |
| [Remove-AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Eltávolítja a megadott tároló. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További tárhely PowerShell parancsfájl minták található [Azure Blob storage PowerShell-példák](../blobs/storage-samples-blobs-powershell.md).
