---
title: Az Azure PowerShell parancsfájl-mintában – kezelt erőforráscsoport lekérése és méretezze át a virtuális gépek |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl minta - felügyelt erőforráscsoport lekérése, majd módosítsa a virtuális gépek
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: poweshell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f549f26cb3f9fdb2d805d2efb2c0e1706abe3edb
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
ms.locfileid: "23940941"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Erőforrások lekérése a felügyelt erőforráscsoportban, majd módosítsa a virtuális gépek a PowerShell használatával

Ez a parancsfájl erőforrások beolvassa a felügyelt erőforráscsoport, és átméretezi a virtuális gépeket az erőforráscsoport.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a kezelt alkalmazás központi telepítése. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermmanagedapplication) | Felügyelt alkalmazások listáját. Adja meg az eredmények figyelmet az erőforráscsoport neve. |
| [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresource) | Lista erőforrásokat. Adjon meg egy erőforráscsoportot és a erőforrástípus, az eredmény. |
| [Frissítés-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) | Frissítse a virtuális gép méretét. |


## <a name="next-steps"></a>Következő lépések

* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](../overview.md).
* A PowerShell további információkért lásd: [Azure PowerShell dokumentációs](https://docs.microsoft.com/powershell/azure/get-started-azureps).
