---
title: 'PowerShell-parancsfájl: engedélyezett virtuálisgép-méretek beállítása a Azure Lab Servicesban | Microsoft Docs'
description: Ez a cikk egy PowerShell-parancsfájlt tartalmaz, amely az engedélyezett virtuális gépek (VM) méretét állítja be Azure Lab Servicesban.
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760456"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Az engedélyezett virtuálisgép-méretek beállítása a PowerShell használatával Azure Lab Services

Ez a PowerShell-parancsfájl az engedélyezett virtuális gépek (VM) méretét állítja be Azure Lab Servicesban.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| Keresés – AzResource | Erőforrások keresése megadott paraméterek alapján. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Erőforrások beolvasása. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Módosítja az erőforrást. |
| [Új – AzResource](/powershell/module/az.resources/new-azresource) | Hozzon létre egy erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-szkriptek is találhatók a [Azure Lab Services PowerShell-mintákban](../samples-powershell.md).
