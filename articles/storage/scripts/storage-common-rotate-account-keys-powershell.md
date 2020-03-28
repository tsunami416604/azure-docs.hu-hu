---
title: Tárfiók-hozzáférési kulcsok elforgatása a PowerShell használatával
titleSuffix: Azure Storage
description: Hozzon létre egy Azure Storage-fiókot, majd olvassa be és forgassa el az egyik fiókhozzáférési kulcsát.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 52ebed3de093f15d8188ee5fec49d75d5a4a206d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060815"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Tárfiók-hozzáférési kulcsok elforgatása a PowerShell használatával

Ez a parancsfájl létrehoz egy Azure Storage-fiókot, megjeleníti az új tárfiók elsődleges hozzáférési kulcsát, majd megújítja (elforgatja) a kulcsot.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a tárfiók és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja a tárfiók létrehozásához, valamint az egyik hozzáférési kulcsának beolvasásához és elforgatásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Lekéri az összes helyet és a támogatott erőforrás-szolgáltatókat az egyes helyekhez. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Létrehoz egy tárfiókot. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Beszerzi egy Azure Storage-fiók hozzáférési kulcsait. |
| [Új-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Újragenerálja az Azure Storage-fiók hozzáférési kulcsát. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További PowerShell-példaszkripteket az [Azure Blob Storage Azure PowerShell-példái](../blobs/storage-samples-blobs-powershell.md) között találhat.
