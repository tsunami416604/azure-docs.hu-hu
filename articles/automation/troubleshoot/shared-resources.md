---
title: Hibák elhárítása az Azure Automation megosztott erőforrásaival
description: Ismerje meg, hogyan háríthatja el és oldhatja meg a runbookokat támogató Azure Automation megosztott erőforrásokkal kapcsolatos problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278323"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Megosztott erőforrásokkal kapcsolatos hibák elhárítása

Ez a cikk az azure Automation megosztott erőforrásainak használata során futtatható problémák megoldásához megoldásokat ismerteti.

## <a name="modules"></a>Modulok

### <a name="scenario-a-module-is-stuck-importing"></a><a name="module-stuck-importing"></a>Eset: A modul importálása beragadt

#### <a name="issue"></a>Probléma

A modul beragadt az **importálási** állapotban, amikor importálja vagy frissíti a modulokat az Azure-automatizálásban.

#### <a name="cause"></a>Ok

A PowerShell-modulok importálása összetett, többlépéses folyamat. Ez a folyamat bevezeti annak lehetőségét, hogy a modul nem importálja megfelelően. Ha ez a probléma jelentkezik, az importálandó modul átmeneti állapotban ragadhat. A folyamatról további információ: [PowerShell-modul importálása.](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához el kell távolítania a **modult,** amely beragadt az importálási állapotban az [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) parancsmag használatával. Ezután megpróbálhatja újra importálni a modult.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-importing-after-trying-to-update-them"></a><a name="update-azure-modules-importing"></a>Eset: Az AzureRM-modulok importálása beragadt, miután megpróbálta frissíteni őket

#### <a name="issue"></a>Probléma

Az alábbi üzenettel rendelkező szalagcím az AzureRM-modulok frissítése után is a fiókjában marad:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Ok

Ismert probléma van az AzureRM-modulok frissítésével egy Automation-fiókban, amely egy numerikus nevű, 0-val kezdődő erőforráscsoportban található.

#### <a name="resolution"></a>Megoldás:

Az Automation-fiókban lévő Azure-modulok frissítéséhez alfanumerikus nevű erőforráscsoportban kell lennie. A 0-val kezdődő numerikus nevekkel rendelkező erőforráscsoportok jelenleg nem tudják frissíteni az AzureRM-modulokat.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Eset: A modul importálása sikertelen, vagy a parancsmagok importálás után nem hajthatók végre

#### <a name="issue"></a>Probléma

Egy modul importálása sikertelen, de a rendszer nem bontja ki a parancsmagokat.

#### <a name="cause"></a>Ok

Néhány gyakori oka annak, hogy egy modul importálása sikertelen ül az Azure Automationbe:

* A struktúra nem egyezik meg azzal a struktúrával, amelyben az Automationnek szüksége van rá.
* A modul egy másik modultól függ, amely még nem lett telepítve az Automation-fiókba.
* A modulból hiányoznak a függőségei a mappában.
* A `New-AzureRmAutomationModule` parancsmag a modul feltöltésére szolgál, és nem adta meg a teljes tárolási útvonalat, vagy nem töltötte be a modult egy nyilvánosan elérhető URL használatával.

#### <a name="resolution"></a>Megoldás:

Az alábbi megoldások bármelyike megoldja a problémát:

* Győződjön meg arról, hogy a modul **->** a következő formátumot követi: ModuleName.Zip ModuleName vagy Version Number **->** (ModuleName.psm1, ModuleName.psd1)
* Nyissa meg a .psd1 fájlt, és nézze meg, hogy a modulnak vannak-e függőségei. Ha igen, töltse fel ezeket a modulokat az Automation-fiókba.
* Győződjön meg arról, hogy a modulmappában minden hivatkozott .dll szerepel.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Eset: Update-AzureModule.ps1 felfüggeszti a modulok frissítésekor

#### <a name="issue"></a>Probléma

Ha az [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook használatával frissíti az Azure-modulokat, a modulfrissítés a frissítési folyamat felfüggesztésre kerül.

#### <a name="cause"></a>Ok

A parancsfájl használatakor 10 `Update-AzureModule.ps1` az alapértelmezett beállítás annak meghatározására, hogy hány modul frissül egyidejűleg. A frissítési folyamat hibákra hajlamos, ha túl sok modul továbbfejlesztett egy időben.

#### <a name="resolution"></a>Megoldás:

Nem gyakori, hogy az összes AzureRM-modul ra van szükség ugyanabban az Automation-fiókban. Javasoljuk, hogy csak a szükséges AzureRM-modulok importálása ajánlott.

> [!NOTE]
> Kerülje az **AzureRM-modul** importálását. Az **AzureRM-modulok** importálása az összes **AzureRM-modul\* ** importálása után, ez nem ajánlott.

Ha a frissítési folyamat felfüggesztésre `SimultaneousModuleImportJobCount` kerül, `Update-AzureModules.ps1` hozzá kell adnia a paramétert a parancsfájlhoz, és alacsonyabb értéket kell megadnia, mint az alapértelmezett érték, amely 10. Javasoljuk, ha ezt a logikát valósítja meg, hogy 3 vagy 5 értékkel kezdődjön. `SimultaneousModuleImportJobCount`Az Azure-modulok `Update-AutomationAzureModulesForAccount` frissítéséhez használt rendszer runbook paramétere. Ez a változás hosszabbra teszi a folyamatot, de nagyobb esélye van a befejezésre. A következő példa bemutatja a paramétert, és hová tegye a runbook:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Eset: Nem lehet létrehozni vagy frissíteni a Futtatás másként fiókot

#### <a name="issue"></a>Probléma

Amikor run as fiókot próbál létrehozni vagy frissíteni, a következőhöz hasonló hibaüzenet jelenik meg:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Ok

Nem rendelkezik a Futtatás másként fiók létrehozásához vagy frissítéséhez szükséges engedélyekkel, vagy az erőforrás erőforráscsoport szintjén zárolva van.

#### <a name="resolution"></a>Megoldás:

A Futtatás másként fiók létrehozásához vagy frissítéséhez megfelelő engedélyekkel kell rendelkeznie a Futtatás másként fiók által használt különböző erőforrásokhoz. A Futtatás másként fiók létrehozásához vagy frissítéséhez szükséges engedélyekről a [Futtatás fiókengedélyek című](../manage-runas-account.md#permissions)témakörben olvashat.

Ha a problémát zárolás miatt okolja, ellenőrizze, hogy a zárolás rendben van-e, és távolítsa el. Ezután keresse meg a zárolt erőforrást, kattintson a jobb gombbal a zárolásra, és a **Törlés parancsra** kattintva távolítsa el a zárolást.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Eset: Runbook végrehajtásakor a "GetPerAdapterInfo" nevű belépési pont megkeresése az "iplpapi.dll" DLL-ben.

#### <a name="issue"></a>Probléma

Runbook végrehajtásakor a következő kivételt kapja:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Ok

Ezt a hibát valószínűleg egy helytelenül konfigurált [Futtatás fiók okozza.](../manage-runas-account.md)

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a [Futtatás másként fiók](../manage-runas-account.md) megfelelően van konfigurálva. Miután megfelelően konfigurálta, győződjön meg arról, hogy a megfelelő kódot a runbook az Azure-ral való hitelesítéshez. A következő példa egy kódrészletet mutat be, amely egy Run As-fiók használatával hitelesíti az Azure-t egy runbookban.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba – a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Ha további segítségre van szüksége, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
