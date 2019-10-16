---
title: Azure PowerShell parancsfájl-minta – felügyelt erőforráscsoport lekérése és virtuális gépek átméretezése | Microsoft Docs
description: Azure PowerShell parancsfájlt biztosít az Azure által felügyelt alkalmazásokhoz tartozó felügyelt erőforráscsoport beszerzéséhez. A szkript átméretezi a virtuális gépeket.
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
ms.openlocfilehash: f36ed42e0b7f8e4d1e5b092d2a14fd0cd8178ee0
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330122"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Felügyelt erőforráscsoport erőforrásainak lekérése és virtuális gépek átméretezése a PowerShell-lel

Ez a parancsfájl erőforrásokat kér le egy felügyelt erőforráscsoportból, és átméretezi az erőforráscsoportban található virtuális gépeket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Listázza a felügyelt alkalmazásokat. Adja meg az erőforráscsoport nevét az eredmények kiépítéséhez. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Listázza az erőforrásokat. Adjon meg egy erőforráscsoportot és egy erőforrástípust az eredmény kiépítéséhez. |
| [Frissítés – AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Frissíti egy virtuális gép méretét. |


## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).
