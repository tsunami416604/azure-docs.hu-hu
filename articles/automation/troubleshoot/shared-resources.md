---
title: Azure Automation megosztott erőforrásokkal kapcsolatos hibák elhárítása
description: Ismerje meg az Azure Automation megosztott erőforrásokkal kapcsolatos problémák elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848459"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Megosztott erőforrásokkal kapcsolatos hibák elhárítása

Ez a cikk ismerteti a megoldásokat, amelyek találkozhat az Azure Automationben a megosztott erőforrás használatakor problémák megoldásához.

## <a name="modules"></a>Modulok

### <a name="module-stuck-importing"></a>Forgatókönyv: Egy modul elakadt importálása

#### <a name="issue"></a>Probléma

Egy modul Beragadt a **importálása** állapota akkor importálhatja, vagy az Azure Automation-modulok frissítése.

#### <a name="cause"></a>Ok

PowerShell-modulok importálása összetett több lépésből álló folyamat során a rendszer. Ez a folyamat előfordulhat, hogy a modul importálását nem megfelelően. Ez a probléma akkor fordul elő, ha a modul importálását is elakadt átmeneti állapotban. Ez a folyamat kapcsolatos további információkért lásd: [egy PowerShell-modul importálása]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához el kell távolítania a modul, amely a elakadt a **importálása** állapot használatával a [Remove-azurermautomationmodule modulba](/powershell/module/azurerm.automation/remove-azurermautomationmodule) parancsmagot. A modul importálása próbálkozzon újra.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="run-as-accounts"></a>Futtató fiókok

### <a name="unable-create-update"></a>Forgatókönyv: Ön nem sikerült létrehozni vagy frissíteni a futtató fiók

#### <a name="issue"></a>Probléma

Amikor megpróbálja létrehozni vagy frissíteni egy futtató fiókot, az alábbihoz hasonló hibaüzenetet kap:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Ok

Nem kell az engedélyeket kell létrehozni vagy frissíteni a futtató fiókot, vagy az erőforrás zárolva van, egy erőforráscsoport szintjén.

#### <a name="resolution"></a>Megoldás:

Létrehozása vagy frissítése egy futtató fiókot, akkor a különböző erőforrások használják a futtató fiók megfelelő engedélyeket kell rendelkeznie. Létrehozni vagy frissíteni a futtató fiók szükséges engedélyekkel kapcsolatos további információkért lásd: [Futtatás mint fiók engedélyek](../manage-runas-account.md#permissions).

Ha a probléma miatt a zárolást, ellenőrizze, hogy a zárolást az ok gombra. Távolítsa el, és keresse meg az erőforrás zárolva van, a jobb gombbal a zárolást, és válassza **törlése** , távolítsa el a zárolást.

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.