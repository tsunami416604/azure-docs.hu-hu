---
title: 'PowerShell-parancsprogram: Piactéri rendszerkép hozzáadása az Azure labor szolgáltatások egyéni labor |} Microsoft Docs'
description: A PowerShell-parancsfájl hozzáadása egy egyéni, amikor az Azure labor szolgáltatásokban Piactéri rendszerkép.
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
ms.openlocfilehash: 64d168c132edce4ecd128b795fbfa5ab2607cb19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-custom-lab"></a>A Piactéri lemezkép hozzáadása egy egyéni, amikor a PowerShell használatával

A PowerShell-parancsfájlpélda Piactéri rendszerkép ad hozzá egy egyéni, amikor az Azure labor szolgáltatásokban.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egyéni labor**. A parancsfájl szükséges hozzá egy meglévő egyéni labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a custom lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Keresés – AzureRmResource](/module/azurerm.resources/find-azurermresource) | A megadott paraméterek alapján erőforrásokat keres. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Erőforrás lekérése. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Erőforrás módosítása. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Hozzon létre egy erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure labor szolgáltatások PowerShell-parancsfájl példák találhatók a [Azure labor szolgáltatások PowerShell-példák](../samples-powershell.md).