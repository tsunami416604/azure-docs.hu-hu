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
ms.openlocfilehash: 5ad04c52da4709a7097ff7915d7af7404d6725eb
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680259"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Log Analytics-munkaterület létrehozása a PowerShell használatával

Ez a szkript beolvassa, és gyorsan az Azure Log Analytics-munkaterület, amely van szükség, ha az adatok gyűjtése, elemzése és az intézkedést elindítja a.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Szkript ismertetése

Hozzon létre egy új Log Analytics-munkaterületet az előfizetésében, a következő parancsokat használja ezt a szkriptet. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-azurermoperationalinsightsworkspace parancsmagok](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Egy meglévő munkaterületet adatainak beolvasása. |
| [Új-azurermoperationalinsightsworkspace parancsmagok](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | A megadott erőforráscsoportban és helyen hoz létre egy munkaterületet. |


## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

