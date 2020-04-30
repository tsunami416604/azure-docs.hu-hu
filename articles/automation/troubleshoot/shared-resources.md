---
title: Azure Automation megosztott erőforrásainak hibáinak megoldása
description: Megtudhatja, hogyan lehet elhárítani a Azure Automation megosztott erőforrásokkal kapcsolatos problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733573"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Azure Automation megosztott erőforrásainak hibáinak megoldása

Ez a cikk a Azure Automation [megosztott erőforrásainak](../automation-intro.md#shared-resources) használatakor esetlegesen futtatott problémák megoldásait ismerteti.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="modules"></a>Modulok

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Forgatókönyv: A modul az importálás során beragadt

#### <a name="issue"></a>Probléma

A modul az importálási állapotba kerül, amikor importálja vagy frissíti a Azure Automation modulokat.

#### <a name="cause"></a>Ok

Mivel a PowerShell-modulok importálása összetett többlépéses folyamat, előfordulhat, hogy egy modul nem importál megfelelően, és átmeneti állapotba kerülhet. További információ az importálási folyamatról: [PowerShell-modul importálása](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához el kell távolítania az importálási állapotba beragadt modult a [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) parancsmag használatával. Ezután újra megpróbálkozhat a modul importálásával.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Forgatókönyv: a AzureRM modulok az importálás során elakadnak a frissítési kísérlet után

#### <a name="issue"></a>Probléma

A AzureRM modulok frissítése után a következő üzenettel rendelkező szalagcím a fiókban marad:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Ok

Ismert hiba történt a AzureRM-modulok egy olyan Automation-fiókban való frissítésekor, amely egy 0 értékkel kezdődő numerikus névvel rendelkező erőforráscsoporthoz van.

#### <a name="resolution"></a>Megoldás:

Az Automation-fiókban lévő AzureRM-modulok frissítéséhez a fióknak alfanumerikus névvel rendelkező erőforráscsoporthoz kell tartoznia. A 0 értékkel kezdődő numerikus nevekkel rendelkező erőforráscsoportok jelenleg nem tudják frissíteni a AzureRM modulokat.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Forgatókönyv: a modul importálása sikertelen, vagy a parancsmagok importálás után nem hajthatók végre.

#### <a name="issue"></a>Probléma

Egy modul nem tudja importálni vagy importálni az importálást, de a rendszer nem nyer ki parancsmagokat.

#### <a name="cause"></a>Ok

Néhány gyakori ok, amiért előfordulhat, hogy egy modul nem importálható sikeresen Azure Automationre:

* A struktúra nem felel meg az Automation által igényelt struktúrának.
* A modul egy másik, az Automation-fiókba nem telepített modultól függ.
* A modulban hiányzik a függőségei a mappában.
* A [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) parancsmag a modul feltöltésére szolgál, és nem adta meg a teljes tárolási útvonalat, vagy nem töltötte be a modult nyilvánosan elérhető URL-cím használatával.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához használja az alábbi megoldások bármelyikét.

* Győződjön meg arról, hogy a modul a következő formátumot követi: ModuleName. zip-> ModuleName vagy Version Number-> (ModuleName. psm1, ModuleName. psd1).
* Nyissa meg a **. psd1** fájlt, és ellenőrizze, hogy a modul rendelkezik-e függőségekkel. Ha igen, töltse fel ezeket a modulokat az Automation-fiókba.
* Győződjön meg arról, hogy minden hivatkozott **. dll** fájl megtalálható a modul mappában.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Forgatókönyv: a Update-AzureModule. ps1 felfüggeszti a modulok frissítésekor

#### <a name="issue"></a>Probléma

Ha az [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook használja az Azure-modulok frissítésére, a modul frissítési folyamata fel lesz függesztve.

#### <a name="cause"></a>Ok

Az alapértelmezett beállítás azt határozza meg, hogy hány modult frissít egyszerre a 10 a **Update-AzureModule. ps1**használata esetén. A frissítési folyamat olyan hibákhoz van kitéve, amikor túl sok modul frissül egyidejűleg.

#### <a name="resolution"></a>Megoldás:

Nem gyakori, hogy az összes AzureRM vagy az az modulra ugyanarra az Automation-fiókra van szükség. Javasoljuk, hogy csak a szükséges modulokat importálja.

> [!NOTE]
> Ne importálja a `Az.Automation` teljes `AzureRM.Automation` vagy a modult, amely az összes befoglalt modult importálja.

Ha a frissítési folyamat felfüggeszti a műveletet, `SimultaneousModuleImportJobCount` adja hozzá a paramétert a **Update-AzureModules. ps1** parancsfájlhoz, és adjon meg egy alacsonyabb értéket, mint az alapértelmezett 10. Ha ezt a logikát alkalmazza, javasolt 3 vagy 5 értékkel kezdeni. `SimultaneousModuleImportJobCount`az az **Update-AutomationAzureModulesForAccount** System runbook paraméter, amely az Azure-modulok frissítésére szolgál. Ha ezt a beállítást választja, a frissítési folyamat tovább fut, de nagyobb eséllyel fejeződik be. A következő példa a paramétert mutatja be, és hová helyezi a runbook:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Forgatókönyv: nem lehet futtató fiókot létrehozni vagy frissíteni

#### <a name="issue"></a>Probléma

Amikor megpróbál létrehozni vagy frissíteni egy futtató fiókot, a következő hibaüzenethez hasonló hibaüzenet jelenik meg:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Ok

Nem rendelkezik a futtató fiók létrehozásához vagy frissítéséhez szükséges engedélyekkel, vagy az erőforrás zárolva van egy erőforráscsoport szintjén.

#### <a name="resolution"></a>Megoldás:

Futtató fiók létrehozásához vagy frissítéséhez megfelelő [engedélyekkel](../manage-runas-account.md#permissions) kell rendelkeznie a futtató fiók által használt különféle erőforrásokhoz. 

Ha a probléma zárolás miatt fennáll, ellenőrizze, hogy a zárolást el lehet-e távolítani. Ezután navigáljon a Azure Portal zárolt erőforráshoz, kattintson a jobb gombbal a zárolásra, majd kattintson a **Törlés**parancsra.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Forgatókönyv: "nem található" GetPerAdapterInfo "nevű belépési pont a (z) iplpapi. dll FÁJLBAN a runbook végrehajtásakor

#### <a name="issue"></a>Probléma

Runbook végrehajtásakor a következő kivétel jelenik meg:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Ok

Ezt a hibát valószínűleg egy helytelenül konfigurált [futtató fiók](../manage-runas-account.md)okozza.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a futtató fiók megfelelően van konfigurálva. Ezután ellenőrizze, hogy rendelkezik-e a megfelelő kóddal a runbook az Azure-beli hitelesítéshez. Az alábbi példa egy kódrészletet mutat be az Azure-ban való hitelesítéshez egy runbook egy futtató fiók használatával.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Az Azure [@AzureSupport](https://twitter.com/azuresupport)-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal való csatlakozással javíthatja az ügyfelek élményét: válaszokat, támogatást és szakértőket.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
