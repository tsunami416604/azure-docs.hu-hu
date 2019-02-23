---
title: 'PowerShell-parancsfájlt: Piactéri lemezképet hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs'
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
ms.openlocfilehash: 22c30e1c3b19e62489a08a702d8b35fbc4862e13
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728216"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Piactéri lemezképet az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet hozzáadása a PowerShell használatával

A PowerShell-példaszkript egy Piactéri rendszerkép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A szkriptnek szüksége van, hogy rendelkezik egy meglévő lab. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Find-AzResource](/powershell/module/az.resources/find-azresource) | Megkeresi az erőforrásokat a megadott paraméterek alapján. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Megjeleníti az erőforrást. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Módosít egy erőforrást. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Hozzon létre egy erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell szkriptminták találhatók az [Azure Lab Services PowerShell-minták](../samples-powershell.md).
