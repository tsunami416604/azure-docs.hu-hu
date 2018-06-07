---
title: 'PowerShell-parancsprogram: létrehozhat egyéni rendszerképeket a VHD-fájl az Azure labor szolgáltatásokban |} Microsoft Docs'
description: A PowerShell-parancsfájl egy egyéni lemezképet egy VHD-fájlt az Azure labor szolgáltatásokban hoz létre.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 19b7c3c6018ec56b056761c336bc56c8b63b47a2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636402"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>A PowerShell szolgáltatás használatával létrehozhat egyéni rendszerképeket a VHD-fájl az Azure labor szolgáltatásokban

A PowerShell-parancsfájlpélda hoz létre egy egyéni lemezképet egy VHD-fájlt az Azure labor szolgáltatásokban

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A parancsfájl szükséges hozzá egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Erőforrás lekérése. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Beszerzi egy Azure Storage-fiók hozzáférési kulcsait. |
| [Új AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) | Egy Azure-telepítés hozzáadása egy erőforráscsoportot. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure labor szolgáltatások PowerShell-parancsfájl példák találhatók a [Azure labor szolgáltatások PowerShell-példák](../samples-powershell.md).