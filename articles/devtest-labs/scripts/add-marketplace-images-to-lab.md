---
title: PowerShell – Piactéri rendszerkép hozzáadása laborhoz Azure DevTest Labs
description: Ez a PowerShell-parancsfájl egy piactér-rendszerképet hoz létre Azure DevTest Labsban található laborba.
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
ms.openlocfilehash: d2f5d6b6b9500ccd90630e8920c09340658fb76f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84897801"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Piactéri rendszerkép hozzáadása a Azure DevTest Labshoz a PowerShell használatával

Ez a minta PowerShell-parancsfájl egy Piactéri rendszerképet hoz létre Azure DevTest Labsban található laborba. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| Keresés – AzResource | Erőforrások keresése megadott paraméterek alapján. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Erőforrások beolvasása. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Módosítja az erőforrást. |
| [Új – AzResource](/powershell/module/az.resources/new-azresource) | Erőforrásokat hozhat létre. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-szkriptek is találhatók a [Azure Lab Services PowerShell-mintákban](../samples-powershell.md).
