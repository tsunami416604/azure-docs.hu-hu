---
title: PowerShell – Külső felhasználó hozzáadása egy laborhoz az Azure DevTest Labsben
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
ms.openlocfilehash: e9efe5476da051d905feff3d6803cb60ea0c4beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760487"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Külső felhasználó hozzáadása az Azure DevTest Labs laborjába a PowerShell használatával

Ez a minta PowerShell-parancsfájl egy külső felhasználót ad hozzá egy tesztkörnyezethez az Azure DevTest Labs-ben. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor.** A parancsfájl megköveteli, hogy egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-Azaduser](/powershell/module/az.resources/get-azaduser) | Újrapróbálkozik a felhasználói objektumot az Azure active directoryból. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | A megadott szerepkört hozzárendeli a megadott egyszerű höz a megadott hatókörben. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-parancsfájlminták az [Azure Lab Services PowerShell-mintákban](../samples-powershell.md)találhatók.
