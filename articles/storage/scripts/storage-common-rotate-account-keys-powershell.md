---
title: Storage-fiók hozzáférési kulcsainak elforgatása a PowerShell-lel
titleSuffix: Azure Storage
description: Hozzon létre egy Azure Storage-fiókot, majd kérje le és forgassa el a fiók hozzáférési kulcsainak egyikét.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: d532782847ec09d2a480951ecd77ae7746b000c2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089370"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Storage-fiók hozzáférési kulcsainak elforgatása a PowerShell-lel

Ez a szkript létrehoz egy Azure Storage-fiókot, megjeleníti az új Storage-fiók elsődleges elérési kulcsát, majd megújítja (elforgatja) a kulcsot.

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

Ez a szkript a következő parancsokat használja a Storage-fiók létrehozásához és az egyik hozzáférési kulcs lekéréséhez és elforgatásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Az összes hely és a támogatott erőforrás-szolgáltatók beolvasása az egyes helyekhez. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Létrehoz egy tárfiókot. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Beszerzi egy Azure Storage-fiók hozzáférési kulcsait. |
| [Új – AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Egy Azure Storage-fiókhoz tartozó hozzáférési kulcs újralétrehozása. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További PowerShell-példaszkripteket az [Azure Blob Storage Azure PowerShell-példái](../blobs/storage-samples-blobs-powershell.md) között találhat.
