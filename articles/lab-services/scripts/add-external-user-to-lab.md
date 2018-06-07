---
title: 'PowerShell-parancsprogram: egy külső felhasználó hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban |} Microsoft Docs'
description: A PowerShell-parancsfájl hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban egy külső felhasználó.
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
ms.openlocfilehash: bd86e44c21ca3c0b9c061f9a5c24bd19c4b207f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636443"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Külső felhasználók hozzáadása az Azure DevTest Labs labor a PowerShell használatával

A PowerShell-parancsfájlpélda a külső felhasználó hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A parancsfájl szükséges hozzá egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | A user objektum az az Azure active directory újrapróbálkozik. |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | A megadott szerepkörhöz rendel hozzá a megadott rendszerbiztonsági tag, a megadott hatókörben. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure labor szolgáltatások PowerShell-parancsfájl példák találhatók a [Azure labor szolgáltatások PowerShell-példák](../samples-powershell.md).