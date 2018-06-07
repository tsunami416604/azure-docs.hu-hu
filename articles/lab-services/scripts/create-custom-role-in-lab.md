---
title: 'PowerShell-parancsprogram: hozzon létre egy egyéni biztonsági szerepkört a laborban a Azure DevTest Labs szolgáltatásban |} Microsoft Docs'
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
ms.openlocfilehash: 295f742342fba7d77b556724c8005f3ac4816482
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636715"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>A PowerShell szolgáltatás használatával hozzon létre egy egyéni biztonsági szerepkört a Azure DevTest Labs szolgáltatásban egy tesztkörnyezetben

A PowerShell-parancsfájlpélda hoz létre egy egyéni biztonsági szerepkört a Azure DevTest Labs szolgáltatásban egy tesztkörnyezetben használatára. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A parancsfájl szükséges hozzá egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

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