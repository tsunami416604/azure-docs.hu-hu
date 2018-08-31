---
title: 'PowerShell-parancsprogram: egy külső felhasználó hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs'
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
ms.openlocfilehash: 0acff1eb4cee441187205b11a7e07cc718072cbf
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248189"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>PowerShell használatával ad hozzá egy külső felhasználót az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet

A PowerShell-példaszkript egy külső felhasználó hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A szkriptnek szüksége van, hogy rendelkezik egy meglévő lab. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | A user objektum az Azure active Directoryból való próbálkozások. |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | A megadott szerepkör társítása a megadott egyszerű, a megadott hatókörben. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell szkriptminták találhatók az [Azure Lab Services PowerShell-minták](../samples-powershell.md).
