---
title: A Visual Studio sablontelepítési parancsfájljának frissítése az Az PowerShell használatához
description: A Visual Studio-sablon telepítési parancsfájljának frissítése az AzureRM szolgáltatásból az Az PowerShellre
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963869"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>A Visual Studio-sablon központi telepítési parancsfájljának frissítése az Az PowerShell-modul használatához

A Visual Studio 16.4 támogatja az Az PowerShell-modul használatát a sablon központi telepítési parancsfájljában. A Visual Studio azonban nem telepíti automatikusan a modult. Az Az modul használatához négy lépést kell tennie:

1. [Az AzureRM-modul eltávolítása](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Az modul telepítése](/powershell/azure/install-az-ps)
1. A Visual Studio frissítése a 16.4-re
1. Frissítse a központi telepítési parancsfájlt a projektben.

## <a name="update-visual-studio-to-164"></a>A Visual Studio frissítése a 16.4-re

Frissítse a Visual Studio telepítését a 16.4-es vagy újabb verzióra. A frissítés során győződjön meg arról, hogy az Azure PowerShell-összetevő nincs bejelölve. Mivel az Az modult a katalóguson keresztül telepítette, nem szeretné újratelepíteni az AzureRM-modult.

Ha már frissített a 16.4-es szintre, és az Azure PowerShell-összetevő be lett jelölve, eltávolíthatja azt a Visual Studio Installer futtatásával. Ne válassza ki az Azure PowerShell-összetevőt az Azure-számítási feladatban vagy az egyes összetevők lapon.

## <a name="update-the-deployment-script-in-your-project"></a>A központi telepítési parancsfájl frissítése a projektben

Cserélje le az "AzureRm" karakterlánc összes előfordulását az "Az" kifejezésre a központi telepítési parancsfájlban. A módosítások megtekintéséhez tekintse meg a módosításokat ebben a [lényegben.](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) A parancsfájlok Az modulra való frissítéséről az [Azure PowerShell áttelepítése az AzureRM-ből az Az szolgáltatásba](/powershell/azure/migrate-from-azurerm-to-az)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

A Visual Studio-projekt használatáról az [Azure-erőforráscsoport-projektek létrehozása és üzembe helyezése a Visual Studio-n keresztül című](create-visual-studio-deployment-project.md)témakörben olvashat.
