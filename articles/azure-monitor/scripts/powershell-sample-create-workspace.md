---
title: Azure PowerShell-Példaszkript – Log Analytics-munkaterület létrehozása |} A Microsoft Docs
description: Azure PowerShell-Példaszkript – a Log Analytics-munkaterület létrehozása
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: fc60be2364e80c288300d78cc5dd23eed4ea9e62
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658149"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Log Analytics-munkaterület létrehozása a PowerShell használatával

Ez a szkript beolvassa, és gyorsan az Azure Log Analytics-munkaterület, amely van szükség, ha az adatok gyűjtése, elemzése és az intézkedést elindítja a.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Szkript ismertetése

Hozzon létre egy új Log Analytics-munkaterületet az előfizetésében, a következő parancsokat használja ezt a szkriptet. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Egy meglévő munkaterületet adatainak beolvasása. |
| [Új – AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | A megadott erőforráscsoportban és helyen hoz létre egy munkaterületet. |


## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

