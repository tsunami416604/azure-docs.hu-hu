---
title: 'PowerShell-parancsprogram: egy külső felhasználó hozzáadása az Azure labor szolgáltatások egyéni labor |} Microsoft Docs'
description: A PowerShell-parancsfájl hozzáadása egy egyéni, amikor az Azure labor szolgáltatásokban külső felhasználó.
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
ms.openlocfilehash: b089067a889f0ffd3b317fcc3f0784d176473b91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-an-external-user-to-a-custom-lab"></a>A külső felhasználó hozzáadása egy egyéni, amikor a PowerShell használatával

A PowerShell-parancsfájlpélda a külső felhasználó hozzáadása egy egyéni, amikor az Azure labor szolgáltatásokban. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egyéni labor**. A parancsfájl szükséges hozzá egy meglévő egyéni labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | A user objektum az az Azure active directory újrapróbálkozik. |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | A megadott szerepkörhöz rendel hozzá a megadott rendszerbiztonsági tag, a megadott hatókörben. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure labor szolgáltatások PowerShell-parancsfájl példák találhatók a [Azure labor szolgáltatások PowerShell-példák](../samples-powershell.md).