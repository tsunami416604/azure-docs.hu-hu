---
title: 'PowerShell-parancsprogram: hozzon létre egy egyéni biztonsági szerepkört az Azure labor szolgáltatások egyéni laboratóriumi |} Microsoft Docs'
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
ms.openlocfilehash: df91c9f842d338e1725fec2734129f2f1f3d3721
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-a-custom-role-in-a-custom-lab-in-azure-lab-services"></a>A PowerShell szolgáltatás használatával hozzon létre egy egyéni biztonsági szerepkört egy egyéni, amikor az Azure labor szolgáltatásokban

A PowerShell-parancsfájlpélda hoz létre egy egyéni biztonsági szerepkört egy egyéni, amikor az Azure labor szolgáltatások használatára. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egyéni labor**. A parancsfájl szükséges hozzá egy meglévő egyéni labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) | Lekérdezi az Azure erőforrás-szolgáltató a műveleteket, amelyek biztonságos Azure RBAC használata. |
| [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) | Kiosztására használható Azure RBAC-szerepkörök listája. |
| [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) | Létrehoz egy egyéni biztonsági szerepkört. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure labor szolgáltatások PowerShell-parancsfájl példák találhatók a [Azure labor szolgáltatások PowerShell-példák](../samples-powershell.md).