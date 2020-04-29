---
title: Felügyelt erőforráscsoport lekérése & virtuális gépek átméretezése – Azure PowerShell
description: Azure PowerShell parancsfájlt biztosít az Azure által felügyelt alkalmazásokhoz tartozó felügyelt erőforráscsoport beszerzéséhez. A szkript átméretezi a virtuális gépeket.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 695023f4700370cbe5e9b345f513e38d1cf1fc0c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650183"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Felügyelt erőforráscsoport erőforrásainak lekérése és virtuális gépek átméretezése a PowerShell-lel

Ez a parancsfájl erőforrásokat kér le egy felügyelt erőforráscsoportból, és átméretezi az erőforráscsoportban található virtuális gépeket.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Listázza a felügyelt alkalmazásokat. Adja meg az erőforráscsoport nevét az eredmények kiépítéséhez. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Listázza az erőforrásokat. Adjon meg egy erőforráscsoportot és egy erőforrástípust az eredmény kiépítéséhez. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Frissíti egy virtuális gép méretét. |


## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).
