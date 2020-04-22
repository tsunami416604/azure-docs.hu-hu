---
title: Megosztott erőforrások hibáinak elhárítása az Azure Automationben
description: Ismerje meg, hogyan háríthatja el és oldhatja meg az Azure Automation megosztott erőforrásaival kapcsolatos problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733573"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Megosztott erőforrások hibáinak elhárítása az Azure Automationben

Ez a cikk az azure Automation megosztott [erőforrásainak](../automation-intro.md#shared-resources) használatakor futtatható problémák megoldásait ismerteti.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="modules"></a>Modulok

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Eset: Egy modul elakad az importálás során

#### <a name="issue"></a>Probléma

Egy modul beragadt az Importálás idúra, amikor importálja vagy frissíti az Azure Automation-modulokat.

#### <a name="cause"></a>Ok

Mivel a PowerShell-modulok importálása összetett többlépéses folyamat, előfordulhat, hogy a modul importálása nem megfelelően történik, és átmeneti állapotban ragadhat. Az importálási folyamatról a [PowerShell-modul importálása](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)című témakörben olvashat bővebben.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához el kell távolítania az Importálás állapotban ragadt modult az [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) parancsmag használatával. Ezután megpróbálhatja újra importálni a modult.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Eset: Az AzureRM-modulok beragadtak az importálás során egy frissítési kísérlet után

#### <a name="issue"></a>Probléma

Az alábbi üzenettel rendelkező szalagcím az AzureRM-modulok frissítése után is a fiókjában marad:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Ok

Ismert probléma van az AzureRM-modulok frissítésével egy Automation-fiókban, amely egy 0-val kezdődő numerikus névvel rendelkező erőforráscsoportban található.

#### <a name="resolution"></a>Megoldás:

Az Automation-fiókban az AzureRM-modulok frissítéséhez a fióknak alfanumerikus nevű erőforráscsoportban kell lennie. A 0-val kezdődő numerikus nevekkel rendelkező erőforráscsoportok jelenleg nem tudják frissíteni az AzureRM-modulokat.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Eset: A modul importálása sikertelen, vagy a parancsmagok importálás után nem hajthatók végre

#### <a name="issue"></a>Probléma

Egy modul importálása sikertelen, vagy sikeresen importál, de a rendszer nem bontja ki a parancsmagokat.

#### <a name="cause"></a>Ok

Néhány gyakori oka annak, hogy egy modul importálása sikertelen ül az Azure Automationbe:

* A struktúra nem felel meg az Automation által igényelt struktúrának.
* A modul egy másik modultól függ, amely még nem lett telepítve az Automation-fiókba.
* A modulból hiányoznak a függőségei a mappában.
* A [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) parancsmag a modul feltöltésére szolgál, és nem adta meg a teljes tárolási útvonalat, vagy nem töltötte be a modult nyilvánosan elérhető URL-cím használatával.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához használja ezeket a megoldásokat.

* Győződjön meg arról, hogy a modul a következő formátumot követi: ModuleName.zip -> ModuleName vagy Version Number -> (ModuleName.psm1, ModuleName.psd1).
* Nyissa meg a **.psd1** fájlt, és nézze meg, hogy a modulnak vannak-e függőségei. Ha igen, töltse fel ezeket a modulokat az Automation-fiókba.
* Győződjön meg arról, hogy a hivatkozott **.dll** fájlok szerepelnek a modulmappában.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Eset: Update-AzureModule.ps1 felfüggeszti a modulok frissítésekor

#### <a name="issue"></a>Probléma

Az [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook használatakor az Azure-modulok frissítéséhez a modulfrissítési folyamat felfüggesztésre kerül.

#### <a name="cause"></a>Ok

Az **Update-AzureModule.ps1**használatakor az alapértelmezett beállítás határozza meg, hogy hány modul frissül egyidejűleg. A frissítési folyamat hibákra hajlamos, ha túl sok modul továbbfejlesztett egy időben.

#### <a name="resolution"></a>Megoldás:

Nem gyakori, hogy az összes AzureRM- vagy Az-modul ra van szükség ugyanabban az Automation-fiókban. Javasoljuk, hogy csak a szükséges modulokat importálja.

> [!NOTE]
> Kerülje a teljes `Az.Automation` `AzureRM.Automation` vagy modul importálását, amely az összes tartalmazott modult importálja.

Ha a frissítési folyamat `SimultaneousModuleImportJobCount` felfüggesztésre kerül, adja hozzá a paramétert az **Update-AzureModules.ps1** parancsfájlhoz, és adjon meg egy alacsonyabb értéket, mint az alapértelmezett 10. Ha ezt a logikát valósítja meg, ajánlott 3 vagy 5 értékkel kezdeni. `SimultaneousModuleImportJobCount`Az **Update-AutomationAzureModulesForAccount** rendszer runbook, amely az Azure-modulok frissítésére szolgál. Ha ezt a módosítást végrehajtja, a frissítési folyamat hosszabb ideig fut, de nagyobb esélye van a befejezésre. A következő példa bemutatja a paramétert, és hová tegye a runbook:

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

A Futtatás másként fiók létrehozásához vagy frissítéséhez megfelelő [engedélyekkel](../manage-runas-account.md#permissions) kell rendelkeznie a Futtatás másként fiók által használt különböző erőforrásokhoz. 

Ha a problémát zárolás miatt okolja, ellenőrizze, hogy a zárolás eltávolítható-e. Ezután keresse meg az Azure Portalon zárolt erőforrást, kattintson a jobb gombbal a zárolásra, és válassza a **Törlés parancsot.**

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Eset: Runbook végrehajtásakor a "GetPerAdapterInfo" nevű belépési pont megkeresése az "iplpapi.dll" DLL-ben" hibaüzenet jelenik meg.

#### <a name="issue"></a>Probléma

Runbook végrehajtásakor a következő kivételt kapja:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Ok

Ezt a hibát valószínűleg egy helytelenül konfigurált [Futtatás fiók okozza.](../manage-runas-account.md)

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a Futtatás másként fiók megfelelően van konfigurálva. Ezután ellenőrizze, hogy rendelkezik-e a megfelelő kóddal a runbookban az Azure-ral való hitelesítéshez. A következő példa egy kódrészletet mutat be, amely egy Run As-fiók használatával hitelesíti az Azure-t egy runbookban.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>További lépések

Ha a fenti probléma nem jelenik meg, vagy nem tudja megoldani a problémát, próbálkozzon az alábbi csatornák egyikével további támogatásért:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
