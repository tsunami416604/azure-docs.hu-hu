---
title: 'PowerShell-parancsprogram: Set engedélyezett Virtuálisgép-méretek az Azure Lab Services |} A Microsoft Docs'
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
ms.openlocfilehash: 559e74675a5d113584dca21979c20462c9cdf19c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054706"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>PowerShell-lel történő beállítása az Azure Lab Services engedélyezett a Virtuálisgép-méretek

A PowerShell-példaszkript Azure Lab Services beállítása engedélyezett virtuális gépek (VM) méretét.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Labor**. A szkriptnek szüksége van, hogy rendelkezik egy meglévő lab. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

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
