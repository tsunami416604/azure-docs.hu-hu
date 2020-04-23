---
title: PowerShell – külső felhasználó hozzáadása laborhoz Azure DevTest Labs
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
ms.openlocfilehash: 40103294e2f610e3ff5879c650f835d4c3e4c207
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101722"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Külső felhasználó hozzáadása egy laborhoz a PowerShell használatával Azure DevTest Labs

Ez a PowerShell-parancsfájl külső felhasználót hoz létre egy laborban Azure DevTest Labsban. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Újrapróbálkozik a felhasználói objektumot az Azure Active Directoryból. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | A megadott szerepkört a megadott hatókörhöz rendeli a megadott rendszerbiztonsági tag számára. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-szkriptek is találhatók a [Azure Lab Services PowerShell-mintákban](../samples-powershell.md).
