---
title: PowerShell – Egyéni szerepkör létrehozása egy tesztkörnyezetben az Azure DevTest Labsben
description: Ez a cikk egy Azure PowerShell-parancsfájlt tartalmaz, amely egy külső felhasználót ad hozzá egy tesztkörnyezethez az Azure DevTest Labsben.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760455"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Egyéni szerepkör létrehozása az Azure DevTest Labs tesztkörnyezetében a PowerShell használatával

Ez a minta PowerShell-parancsfájl létrehoz egy egyéni szerepkört az Azure DevTest Labs egy tesztkörnyezetben. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor.** A parancsfájl megköveteli, hogy egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzProviderÜzemeltet](/powershell/module/az.resources/get-azprovideroperation) | Lekéri a műveleteket egy Azure-erőforrás-szolgáltató, amely biztonságos az Azure RBAC használatával. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | A hozzárendeléshez rendelkezésre álló összes Azure RBAC szerepkör listája. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Egyéni szerepkört hoz létre. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-parancsfájlminták az [Azure Lab Services PowerShell-mintákban](../samples-powershell.md)találhatók.
