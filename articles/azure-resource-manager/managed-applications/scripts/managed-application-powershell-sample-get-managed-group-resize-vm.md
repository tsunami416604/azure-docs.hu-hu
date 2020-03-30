---
title: Felügyelt erőforráscsoport & átméretezése – Azure PowerShell
description: Azure PowerShell-mintaparancsfájlt biztosít, amely egy Felügyelt alkalmazás felügyelt erőforráscsoportját kapja. A parancsfájl újraméretezi a virtuális gépeket.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 695023f4700370cbe5e9b345f513e38d1cf1fc0c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650183"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Erőforrások beszereznie egy felügyelt erőforráscsoportban, és átméretezni a virtuális gépeket a PowerShell segítségével

Ez a parancsfájl erőforrásokat kér le egy felügyelt erőforráscsoportból, és átméretezi az erőforráscsoportban található virtuális gépeket.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzManagedAlkalmazás](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Listázza a felügyelt alkalmazásokat. Adja meg az erőforráscsoport nevét az eredmények összpontosításához. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Listázza az erőforrásokat. Erőforráscsoport és erőforrástípus biztosítása az eredmény összpontosításához. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Frissíti egy virtuális gép méretét. |


## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).
