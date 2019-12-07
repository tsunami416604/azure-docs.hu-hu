---
title: Storage-fiók hozzáférési kulcsainak elforgatása a PowerShell-lel
titleSuffix: Azure Storage
description: Hozzon létre egy Azure Storage-fiókot, majd kérje le és forgassa el a fiók hozzáférési kulcsainak egyikét.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 54a7a6cb89c88726853a39ab5b2a61e1f0cde0b5
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894922"
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

| Parancs | Megjegyzések |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Az összes hely és a támogatott erőforrás-szolgáltatók beolvasása az egyes helyekhez. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy Azure-erőforráscsoportot. |
| [Új – AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Létrehoz egy tárfiókot. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Beszerzi egy Azure Storage-fiók hozzáférési kulcsait. |
| [Új – AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Egy Azure Storage-fiókhoz tartozó hozzáférési kulcs újralétrehozása. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További PowerShell-példaszkripteket az [Azure Blob Storage Azure PowerShell-példái](../blobs/storage-samples-blobs-powershell.md) között találhat.
