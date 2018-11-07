---
title: Azure Automation megosztott erőforrásokkal kapcsolatos hibák elhárítása
description: Ismerje meg az Azure Automation megosztott erőforrásokkal kapcsolatos problémák elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263560"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Megosztott erőforrásokkal kapcsolatos hibák elhárítása

Ez a cikk ismerteti a megoldásokat, amelyek találkozhat az Azure Automationben a megosztott erőforrás használatakor problémák megoldásához.

## <a name="modules"></a>Modulok

### <a name="module-stuck-importing"></a>Forgatókönyv: Egy modul elakadt importálása

#### <a name="issue"></a>Probléma

Amikor importálja, vagy az Azure Automation-modulok frissítése, látja-e a modul, amely a elakadt a **importálása** állapota.

#### <a name="error"></a>Hiba

PowerShell-modulok importálása összetett több lépésből álló folyamat során a rendszer. Ez a folyamat előfordulhat, hogy a modul importálását nem megfelelően. Ha ez történik, a modul importálását is elakadt átmeneti állapotban. Ez a folyamat kapcsolatos további információkért lásd: [egy PowerShell-modul importálása]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához el kell távolítania a modul, amely a elakadt a **importálása** állapot használatával a [Remove-azurermautomationmodule modulba](/powershell/module/azurerm.automation/remove-azurermautomationmodule) parancsmagot. A modul importálása próbálkozzon újra.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.