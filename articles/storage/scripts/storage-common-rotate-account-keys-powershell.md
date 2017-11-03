---
title: "Az Azure PowerShell-parancsfájl minta - Elforgatás tárfiók_elérési_kulcsa |} Microsoft Docs"
description: "Hozzon létre egy Azure Storage-fiókot, majd kérje le, majd forgassa el a tárelérési kulcsok egyikét."
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
ms.openlocfilehash: fe7e12991cb59eb32e173b0552eb155bf66c6c76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Hozzon létre egy tárfiókot, és a fiók hozzáférési kulcsait elforgatása

Ez a parancsfájl létrehoz egy Azure Storage-fiókot, jeleníti meg az új tárfiók elsődleges elérési kulcsát, majd megújítja (forog) a kulcsot.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő paranccsal távolítsa el az erőforráscsoportot, storage-fiók és minden kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a storage-fiók létrehozása és beolvasása és elforgatása a tárelérési kulcsok egyikét. A tábla hivatkozások parancs vonatkozó dokumentációt összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) | Minden hely és a támogatott szolgáltatók beolvasása mindegyik helyen. |
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Egy Azure-erőforráscsoportot hoz létre. |
| [Új-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Létrehoz egy tárfiókot. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Lekérdezi az Azure-tárfiók hozzáférési kulcsainak listázása. |
| [Új AzureRmStorageAccountKey](/powershell/module/azurerm.storage/new-azurermstorageaccountkey) | Egy egy Azure Storage-fiók elérési kulcsának újragenerálása. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További tárhely PowerShell parancsfájl minták található [Azure Blob storage PowerShell-példák](../blobs/storage-samples-blobs-powershell.md).
