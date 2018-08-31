---
title: 'PowerShell-parancsfájlt: Adja hozzá a Piactéri lemezképet az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs'
description: Ez a PowerShell-parancsfájl hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet Piactéri lemezképet.
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
ms.openlocfilehash: a3a007bf19a28e6f361837856f83a191a761ef9b
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247414"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Piactéri lemezképet az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet hozzáadása a PowerShell használatával

A PowerShell-példaszkript egy Piactéri rendszerkép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A szkriptnek szüksége van, hogy rendelkezik egy meglévő lab. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) | Megkeresi az erőforrásokat a megadott paraméterek alapján. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Megjeleníti az erőforrást. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Módosít egy erőforrást. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Hozzon létre egy erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell szkriptminták találhatók az [Azure Lab Services PowerShell-minták](../samples-powershell.md).
