---
title: Az Azure PowerShell-Példaszkript – rotálása tárfiók hozzáférési kulcsát |} A Microsoft Docs
description: Hozzon létre egy Azure Storage-fiókot, majd lekérdezni, és egy hozzáférési kulcsainak rotálása.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 026c399af70a0c97446fba28b5dd7ca1ed82b89c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635493"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Tárfiók létrehozása és a hozzáférési kulcsainak rotálása

Ez a szkript létrehoz egy Azure Storage-fiókot, megjeleníti az új tárfiók elsődleges hívóbetűjét, majd megújítja (rotálja) a kulcsot.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a tárfiók és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja, a storage-fiók létrehozása, lekéréséhez és rotálásához a hozzáférési kulcsok egyikét. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Minden hely és a támogatott erőforrás-szolgáltatók beolvasása minden helyen. |
| [Új AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot. |
| [Új AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Létrehoz egy tárfiókot. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Beszerzi egy Azure Storage-fiók hozzáférési kulcsait. |
| [Új AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Az Azure Storage-fiókhoz tartozó hozzáférési kulcs újragenerálása. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További PowerShell-példaszkripteket az [Azure Blob Storage Azure PowerShell-példái](../blobs/storage-samples-blobs-powershell.md) között találhat.
