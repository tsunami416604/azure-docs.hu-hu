---
title: Log Analytics-munkaterület létrehozása – Azure PowerShell
description: Azure PowerShell-parancsfájlminta – Log Analytics-munkaterület létrehozása
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 62b02de5d1c08f6047052d71e3be420cceb1c5c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80054629"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Log Analytics-munkaterület létrehozása a PowerShell használatával

Ez a parancsfájl gyorsan működésbe hozza az Azure Log Analytics munkaterületet, amely szükséges, ha meg szeretné kezdeni az adatok gyűjtését, elemzését és az adatokkal kapcsolatos műveleteket.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja egy új Log Analytics-munkaterület létrehozásához az előfizetésben. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Információt kap egy meglévő munkaterületről. |
| [Új-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Munkaterületet hoz létre a megadott erőforráscsoportban és helyen. |


## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

