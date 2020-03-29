---
title: PowerShell – Marketplace-lemezkép hozzáadása egy laborhoz az Azure DevTest Labsben
description: Ez a PowerShell-parancsfájl egy piactéri lemezképet ad hozzá egy tesztkörnyezethez az Azure DevTest Labsben.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d699a22ed06022c6d9df12ade7f202fb304648c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166446"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>A PowerShell használatával marketplace-lemezképet adhat hozzá egy laborhoz az Azure DevTest Labsben

Ez a minta PowerShell-parancsfájl egy piactéri lemezképet ad hozzá egy laborhoz az Azure DevTest Labs-ben. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor.** A parancsfájl megköveteli, hogy egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| Find-AzResource | Erőforrások keresése a megadott paraméterek alapján. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Erőforrásokat kap. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Egy erőforrást módosít. |
| [Új-AzResource](/powershell/module/az.resources/new-azresource) | Erőforrásokat hozhat létre. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-parancsfájlminták az [Azure Lab Services PowerShell-mintákban](../samples-powershell.md)találhatók.
