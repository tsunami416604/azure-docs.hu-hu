---
title: Az Azure PowerShell-parancsfájl minta - Naplóelemzési munkaterület létrehozása |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl minták – a Naplóelemzési munkaterület létrehozása
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 30d036ae56acc3a798d2776f292243f65cbea43d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855438"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>A Naplóelemzési munkaterület létrehozása a PowerShell használatával

Ez a parancsfájl lekérdezi akkor működik, és gyorsan egy Azure Log Analytics-munkaterület, amely pedig szükséges, ha el szeretné indítani gyűjtése, elemzése és intézkedést adatokon.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy új Naplóelemzési munkaterület létrehozni az előfizetésben. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Egy meglévő munkaterület információ lekérése. |
| [Új AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | A megadott erőforráscsoport és a hely egy munkaterület hoz létre. |


## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

