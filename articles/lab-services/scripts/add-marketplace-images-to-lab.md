---
title: 'PowerShell-parancsprogram: Piactéri rendszerkép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban |} Microsoft Docs'
description: A PowerShell-parancsfájl hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban Piactéri rendszerkép.
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
ms.openlocfilehash: 9a65237b3eba6c9878d73148f0143f20dd60dd79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636548"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>A PowerShell szolgáltatás használatával Piactéri rendszerkép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban

A PowerShell-parancsfájlpélda a Piactéri lemezkép hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A parancsfájl szükséges hozzá egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | A megadott paraméterek alapján erőforrásokat keres. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Erőforrás lekérése. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Erőforrás módosítása. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Hozzon létre egy erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure labor szolgáltatások PowerShell-parancsfájl példák találhatók a [Azure labor szolgáltatások PowerShell-példák](../samples-powershell.md).