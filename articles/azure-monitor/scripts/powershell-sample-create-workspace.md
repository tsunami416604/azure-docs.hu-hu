---
title: Azure PowerShell-Példaszkript – Log Analytics-munkaterület létrehozása |} A Microsoft Docs
description: Azure PowerShell-Példaszkript – a Log Analytics-munkaterület létrehozása
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 4dc98fc3912f9d9c659ecfcaa3df253d30bfcc78
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306969"
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
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | A megadott erőforráscsoportban és helyen hoz létre egy munkaterületet. |


## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

