---
title: Log Analytics munkaterület létrehozása – Azure PowerShell
description: 'Azure PowerShell szkript minta – Log Analytics munkaterület létrehozása a következőhöz:'
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: f6bfb3a244874f6160d34c174b6d10b9a03ca437
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87024118"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Log Analytics munkaterület létrehozása a PowerShell-lel

Ez a szkript gyorsan üzembe helyezhető egy Azure Log Analytics-munkaterülettel, amely akkor szükséges, ha meg szeretné kezdeni az adatok gyűjtését, elemzését és beavatkozását.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy új Log Analytics munkaterület létrehozásához az előfizetésben. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Információk beolvasása egy meglévő munkaterületről. |
| [Új – AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Létrehoz egy munkaterületet a megadott erőforráscsoport és hely között. |


## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

