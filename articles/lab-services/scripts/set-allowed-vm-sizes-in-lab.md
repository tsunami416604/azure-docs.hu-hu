---
title: 'PowerShell-parancsfájlt: Set engedélyezett Virtuálisgép-méretek az Azure Lab Services |} A Microsoft Docs'
description: Ez a PowerShell-szkript engedélyezett Virtuálisgép-méretek az Azure Lab Services állítja be.
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
ms.openlocfilehash: 0c82e304d3e3d8df1206c7c05883399b74229af7
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192190"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>PowerShell-lel történő beállítása az Azure Lab Services engedélyezett a Virtuálisgép-méretek

A PowerShell-példaszkript Azure Lab Services beállítása engedélyezett virtuális gépek (VM) méretét.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A szkriptnek szüksége van, hogy rendelkezik egy meglévő lab. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| Find-AzResource | Megkeresi az erőforrásokat a megadott paraméterek alapján. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Megjeleníti az erőforrást. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Módosít egy erőforrást. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Hozzon létre egy erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell szkriptminták találhatók az [Azure Lab Services PowerShell-minták](../samples-powershell.md).
