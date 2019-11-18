---
title: A Visual Studio sablon-telepítési parancsfájljának frissítése az az PowerShell használatával
description: A Visual Studio sablon üzembe helyezési parancsfájljának frissítése a AzureRM-ből az PowerShell-be
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149062"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>A Visual Studio-sablon telepítési parancsfájljának frissítése az az PowerShell használatával

A Visual Studio 16,4 támogatja az az PowerShell használatát a sablon üzembe helyezési parancsfájljában. A Visual Studio nem telepíti az az PowerShell-t, vagy automatikusan frissíti az üzembe helyezési parancsfájlt az erőforráscsoport-projektben. Az újabb az PowerShell használatához a következő négy dolgot kell tennie:
1. AzureRM-PowerShell eltávolítása
1. Telepítés az PowerShell
1. Frissítés a Visual Studio 16,4-re
1. Frissítse az üzembe helyezési parancsfájlt a projektben.

## <a name="uninstall-azurerm-powershell"></a>AzureRM-PowerShell eltávolítása
Kövesse ezeket az [utasításokat](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module) a AzureRM PowerShell eltávolításához.

## <a name="install-az-powershell"></a>Telepítés az PowerShell
Az az PowerShell telepítéséhez kövesse az alábbi [utasításokat](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0) .

## <a name="update-visual-studio-to-164"></a>A Visual Studio frissítése 16,4-re
Frissítsen a Visual Studio 16,4-re, ha elérhető. A frissítés során győződjön meg arról, hogy az Azure PowerShell-összetevő nincs bejelölve. Mivel az az PowerShellt a katalóguson keresztül telepítette, nem szeretné, hogy a PowerShell AzureRM-verziója a Visual Studióval együtt települ.

Ha már frissített a 16,4-es verzióra, és az Azure PowerShell-összetevő be lett jelölve, a Visual Studio telepítő futtatásával távolíthatja el, és törölheti az Azure PowerShell-összetevőt az Azure-munkaterhelésben vagy az egyes összetevők lapon.

## <a name="update-the-deployment-script-in-your-project"></a>Az üzembe helyezési parancsfájl frissítése a projektben
Cserélje le a "AzureRm" karakterlánc összes előfordulását az üzembe helyezési parancsfájlban az "az" értékre. A módosítások megtekintéséhez tekintse meg [a jelen](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) témakörben található változatokat. A szkriptek az az PowerShell-re való frissítésével kapcsolatos további információkért tekintse meg ezt a [dokumentációt](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0) .


