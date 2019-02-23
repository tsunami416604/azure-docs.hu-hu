---
title: Azure Automation megosztott erőforrásokkal kapcsolatos hibák elhárítása
description: Ismerje meg az Azure Automation megosztott erőforrásokkal kapcsolatos problémák elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: abce40958f8d775e0a579a18cf8d1351740031ff
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671063"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Megosztott erőforrásokkal kapcsolatos hibák elhárítása

Ez a cikk ismerteti a megoldásokat, amelyek találkozhat az Azure Automationben a megosztott erőforrás használatakor problémák megoldásához.

## <a name="modules"></a>Modulok

### <a name="module-stuck-importing"></a>Forgatókönyv: Modul importálása elakadt

#### <a name="issue"></a>Probléma

Egy modul Beragadt a **importálása** állapota akkor importálhatja, vagy az Azure Automation-modulok frissítése.

#### <a name="cause"></a>Ok

PowerShell-modulok importálása összetett több lépésből álló folyamat során a rendszer. Ez a folyamat előfordulhat, hogy a modul importálását nem megfelelően. Ez a probléma akkor fordul elő, ha a modul importálását is elakadt átmeneti állapotban. Ez a folyamat kapcsolatos további információkért lásd: [egy PowerShell-modul importálása]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához el kell távolítania a modul, amely a elakadt a **importálása** állapot használatával a [Remove-azurermautomationmodule modulba](/powershell/module/azurerm.automation/remove-azurermautomationmodule) parancsmagot. A modul importálása próbálkozzon újra.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Forgatókönyv: AzureRM-modulok regisztrálásuk után próbálja frissíteni őket importálása

#### <a name="issue"></a>Probléma

A következő üzenetet tartalmazó szalag marad a fiókjában után az AzureRM-modulok frissítése közben:

```
Azure modules are being updated
```

#### <a name="cause"></a>Ok

Frissítése az AzureRM-modulokat: Automation-fiók, amely egy erőforráscsoportot a numerikus kezdetű névvel rendelkező 0-val rendelkező egy ismert probléma van.

#### <a name="resolution"></a>Megoldás:

Az Azure-modulokat: Automation-fiók frissítéséhez, amelynek a neve alfanumerikus erőforráscsoportban kell lennie. Erőforráscsoportok 0-tól induló numerikus nevek nem tudnak AzureRM-modulok frissítése most.

### <a name="module-fails-to-import"></a>Forgatókönyv: Modul importálása sikertelen, vagy a parancsmagok nem hajtható végre, az importálás után

#### <a name="issue"></a>Probléma

Modul importálása sikertelen lesz, vagy sikeresen importálja, de nincs parancsmagok ki kell olvasni.

#### <a name="cause"></a>Ok

Néhány általános oka, hogy egy modul előfordulhat, hogy nem sikerült importálni az Azure Automationhöz a következők:

* A struktúra nem felel meg az Automation kell a struktúra.
* A modul, amely még nem lett telepítve az Automation-fiók egy másik modul függ.
* A modul hiányzó függőségeit a mappában.
* A `New-AzureRmAutomationModule` parancsmagot használják, hogy a modul feltöltése és a teljes elérési útja nem végezték el, vagy a modul még nincs betöltve egy nyilvánosan elérhető URL-cím használatával.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:

* Győződjön meg arról, hogy a modul követi a következő formátumban: ModuleName.Zip **->** ModuleName vagy a verziószám **->** (ModuleName.psm1, ModuleName.psd1)
* Nyissa meg a .psd1 fájlban, és tekintse meg, ha a modul rendelkezik-e függőségek. Ha igen, töltse fel ezeket a modulokat az Automation-fiókot.
* Győződjön meg arról, hogy minden hivatkozott .dll modul mappában találhatók.

### <a name="all-modules-suspended"></a>Forgatókönyv: Update-AzureModule.ps1 suspends when updating modules

#### <a name="issue"></a>Probléma

Használatakor a [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbookot, hogy a modul frissítése a frissítési folyamatot felfüggesztett lekérdezi az Azure-modulok frissítése.

#### <a name="cause"></a>Ok

Az alapértelmezett beállítás határozza meg, hány modult egyszerre frissíti a 10 használata esetén a `Update-AzureModule.ps1` parancsfájlt. A frissítési folyamat modulok beüzemelje frissítése folyamatban van egy időben esetén a hibalehetőség.

#### <a name="resolution"></a>Megoldás:

A nem gyakori, hogy minden AzureRM-modul az azonos Automation-fiók szükséges. Azt javasoljuk, hogy az importálás kizárólag az AzureRM-modulok szükséges.

> [!NOTE]
> Kerülje a importálása a **AzureRM** modul. Importálja a **AzureRM** modulok hatására az összes **AzureRM.\***  modulok importálni, ez azonban nem leronthatja.

Ha felfüggeszti a frissítési folyamat, hozzá kell a `SimultaneousModuleImportJobCount` paramétert a `Update-AzureModules.ps1` szkriptet, és adja meg az alapértelmezett, amely 10 alacsonyabb érték. Ha a logikai értéke 3 vagy 5 használatbavételéhez megvalósítása ajánlott. `SimultaneousModuleImportJobCount` a paraméter a `Update-AutomationAzureModulesForAccount` rendszer runbook, amellyel Azure-modulok frissítése. Ez a változás lehetővé teszi a folyamat futtatási már, de befejezése, nagyobb valószínűséggel lehet. Az alábbi példa bemutatja a paraméter és helyét, a runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Futtató fiókok

### <a name="unable-create-update"></a>Forgatókönyv: Nem tud létrehozni vagy frissíteni a futtató fiók

#### <a name="issue"></a>Probléma

Amikor megpróbálja létrehozni vagy frissíteni egy futtató fiókot, az alábbihoz hasonló hibaüzenetet kap:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Ok

Nem kell az engedélyeket kell létrehozni vagy frissíteni a futtató fiókot, vagy az erőforrás zárolva van, egy erőforráscsoport szintjén.

#### <a name="resolution"></a>Megoldás:

Létrehozása vagy frissítése egy futtató fiókot, akkor a különböző erőforrások használják a futtató fiók megfelelő engedélyeket kell rendelkeznie. Létrehozni vagy frissíteni a futtató fiók szükséges engedélyekkel kapcsolatos további információkért lásd: [Futtatás mint fiók engedélyek](../manage-runas-account.md#permissions).

Ha a probléma miatt a zárolást, ellenőrizze, hogy a zárolást az ok gombra. Távolítsa el azt. Az erőforrás zárolva van, majd keresse meg, kattintson a jobb gombbal a zárolást, és válassza a **törlése** , távolítsa el a zárolást.

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.