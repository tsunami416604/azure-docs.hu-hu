---
title: Az Azure PowerShell példaszkript – felügyelt erőforráscsoport lekérése és virtuális gépek átméretezése |} A Microsoft Docs
description: Azure PowerShell-példaszkript – felügyelt erőforráscsoport lekérése és virtuális gépek átméretezése
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 9e8930c95495673c0082a82757ed6d8137900b6f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110897"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Felügyelt erőforráscsoport erőforrásainak lekérése és a PowerShell-lel virtuális gépek átméretezése

Ez a parancsfájl erőforrásokat kér le egy felügyelt erőforráscsoportból, és átméretezi az erőforráscsoportban található virtuális gépeket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Listázza a felügyelt alkalmazásokat. Adja meg az erőforráscsoport-nevet az eredmények szűréséhez. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Listázza az erőforrásokat. Adjon meg egy erőforráscsoportot és az erőforrás típusa, az eredmények szűréséhez. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Frissíti egy virtuális gép méretét. |


## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).
