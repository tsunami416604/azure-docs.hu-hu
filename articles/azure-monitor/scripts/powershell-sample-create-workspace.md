---
title: Azure PowerShell szkript minta – Log Analytics munkaterület létrehozása | Microsoft Docs
description: 'Azure PowerShell szkript minta – Log Analytics munkaterület létrehozása a következőhöz:'
ms.service: azure-monitor
ms.subservice: logs
ms.topic: sample
author: MGoedtel
ms.author: magoedte
ms.date: 09/07/2017
ms.openlocfilehash: 6aad6c6f0656bd317a808de13e340d5774cea49b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931919"
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

| Parancs | Megjegyzések |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Információk beolvasása egy meglévő munkaterületről. |
| [Új – AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Létrehoz egy munkaterületet a megadott erőforráscsoport és hely között. |


## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

