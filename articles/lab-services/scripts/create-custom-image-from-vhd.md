---
title: PowerShell – Egyéni lemezkép létrehozása vHD-fájlból az Azure Lab Servicesben
description: Ez a PowerShell-parancsfájl egy virtuális merevlemez-fájlból hoz létre egyéni lemezképet az Azure Lab Servicesben.
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
ms.openlocfilehash: 38383462a665ced1ccb6c6a2f062fab0492eee9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169981"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Egyéni lemezkép létrehozása az Azure Lab Servicesben a PowerShell használatával egyéni lemezkép létrehozása vhd-fájlból

Ez a minta PowerShell-parancsfájl egyéni lemezképet hoz létre egy vHD-fájlból az Azure Lab Servicesben

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor.** A parancsfájl megköveteli, hogy egy meglévő labor. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Megjegyzések |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Erőforrásokat kap. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Beszerzi egy Azure Storage-fiók hozzáférési kulcsait. |
| [Új-AzResourceGroupCsoportosítás](/powershell/module/az.resources/new-azresourcegroupdeployment) | Hozzáadja az Azure-telepítést egy erőforráscsoporthoz. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Lab Services PowerShell-parancsfájlminták az [Azure Lab Services PowerShell-mintákban](../samples-powershell.md)találhatók.
