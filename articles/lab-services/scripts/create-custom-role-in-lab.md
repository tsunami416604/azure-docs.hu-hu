---
title: 'PowerShell-parancsfájlt: Egyéni szerepkör létrehozása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs'
description: Ez a PowerShell-szkript egy külső felhasználó hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet.
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
ms.openlocfilehash: 4d0c795dffb40ab7efec9005660439f9baef9f3f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732432"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet egy egyéni szerepkör létrehozása a PowerShell használatával

A PowerShell-példaszkript létrehoz egy egyéni szerepkör használata az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A szkriptnek szüksége van, hogy rendelkezik egy meglévő lab. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Azure RBAC használatával biztosítható a műveletek egy Azure-erőforrás-szolgáltató beolvasása. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Hozzárendelés elérhető összes Azure RBAC-szerepkörök listája. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Egy egyéni szerepkört hoz létre. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell szkriptminták találhatók az [Azure Lab Services PowerShell-minták](../samples-powershell.md).
