---
title: 'PowerShell-parancsfájl: Az engedélyezett virtuálisgép-méretek beállítása az Azure Lab Servicesben | Microsoft dokumentumok'
description: Ez a cikk egy minta PowerShell-parancsfájlt tartalmaz, amely beállítja az engedélyezett virtuális gép (VM) méreteket az Azure Lab Servicesben.
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
ms.openlocfilehash: a1b0e9a4aed475f04ec8dcffa9bc95b7c7c713e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760456"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>A PowerShell használatával állítsa be az engedélyezett virtuálisgép-méreteket az Azure Lab Servicesben

Ez a minta PowerShell-parancsfájl lehetővé teszi a virtuális gép (VM) méreteit az Azure Lab Servicesben.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor.** A parancsfájl megköveteli, hogy egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

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
