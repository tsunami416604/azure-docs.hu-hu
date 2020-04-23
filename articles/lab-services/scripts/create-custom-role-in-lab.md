---
title: PowerShell – egyéni szerepkör létrehozása a laborban Azure DevTest Labs
description: Ez a cikk egy Azure PowerShell parancsfájlt tartalmaz, amely egy külső felhasználót ad hozzá egy laborhoz Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: a7822d6ea46b7efc21f43e944d0a96f609df389a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100182"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Egyéni szerepkör létrehozása a PowerShell használatával Azure DevTest Labs

Ez a minta PowerShell-szkript létrehoz egy egyéni szerepkört, amelyet a Azure DevTest Labsban található laborban kell használni. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Lekéri egy Azure-erőforrás-szolgáltató műveleteit, amelyek az Azure RBAC segítségével biztonságossá tehetők. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Felsorolja a hozzárendeléshez rendelkezésre álló összes Azure RBAC-szerepkört. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Létrehoz egy egyéni szerepkört. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-szkriptek is találhatók a [Azure Lab Services PowerShell-mintákban](../samples-powershell.md).
