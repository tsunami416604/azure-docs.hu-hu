---
title: Azure Automation megosztott erőforrásokkal kapcsolatos hibák elhárítása
description: Megtudhatja, hogyan lehet elhárítani és elhárítani a runbookok támogató Azure Automation megosztott erőforrásokkal kapcsolatos problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372301"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Megosztott erőforrásokkal kapcsolatos hibák elhárítása

Ez a cikk a Azure Automation megosztott erőforrásainak használatakor esetlegesen futtatott problémák megoldását ismerteti.

## <a name="modules"></a>Modulok

### <a name="module-stuck-importing"></a>Forgatókönyv: A modulba való importálás megakadt

#### <a name="issue"></a>Probléma

Egy modul az **importálási** állapotba kerül, amikor importálja vagy frissíti a modulokat az Azure automationben.

#### <a name="cause"></a>Ok

A PowerShell-modulok importálása egy összetett, többlépéses folyamat. Ez a folyamat bevezeti a modul helytelen importálásának lehetőségét. Ha ez a probléma merül fel, az importálandó modul átmeneti állapotba helyezhető. További információ erről a folyamatról: [PowerShell-modul importálása](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához el kell távolítania az **importálási** állapotba beragadt modult a [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) parancsmag használatával. Ezután újra megpróbálkozhat a modul importálásával.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Forgatókönyv: a AzureRM-modulok a frissítés után elakadnak az importálással

#### <a name="issue"></a>Probléma

A AzureRM modulok frissítése után a következő üzenettel rendelkező szalagcím a fiókban marad:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Ok

Ismert hiba történt a AzureRM-modulok egy olyan Automation-fiókban való frissítésekor, amely egy 0 karakterrel kezdődő numerikus névvel rendelkező erőforráscsoport.

#### <a name="resolution"></a>Megoldás:

Az Automation-fiókban lévő Azure-modulok frissítéséhez egy alfanumerikus névvel rendelkező erőforráscsoporthoz kell tartoznia. A 0 értékkel kezdődő numerikus nevekkel rendelkező erőforráscsoportok jelenleg nem tudják frissíteni a AzureRM modulokat.

### <a name="module-fails-to-import"></a>Forgatókönyv: a modul importálása sikertelen, vagy a parancsmagok importálás után nem hajthatók végre.

#### <a name="issue"></a>Probléma

Egy modul nem tudja sikeresen importálni vagy importálni a parancsmagokat, de nincsenek kinyerve.

#### <a name="cause"></a>Ok

Néhány gyakori ok, amiért előfordulhat, hogy egy modul nem importálható sikeresen Azure Automationre:

* A struktúra nem egyezik az Automation által igényelt struktúrával.
* A modul egy másik, az Automation-fiókba nem telepített modultól függ.
* A modulban hiányzik a függőségei a mappában.
* A `New-AzureRmAutomationModule` parancsmag a modul feltöltésére szolgál, és nem adta meg a teljes tárolási útvonalat, vagy nem töltötte be a modult nyilvánosan elérhető URL-cím használatával.

#### <a name="resolution"></a>Megoldás:

A következő megoldások bármelyike elháríthatja a problémát:

* Győződjön meg arról, hogy a modul a következő formátumot követi: ModuleName. zip **->** ModuleName vagy verziószám **->** (ModuleName. psm1, ModuleName. psd1)
* Nyissa meg a. psd1 fájlt, és ellenőrizze, hogy a modul rendelkezik-e függőségekkel. Ha igen, töltse fel ezeket a modulokat az Automation-fiókba.
* Győződjön meg arról, hogy a hivatkozott. dll fájlok szerepelnek a modul mappában.

### <a name="all-modules-suspended"></a>Forgatókönyv: a Update-AzureModule. ps1 felfüggeszti a modulok frissítésekor

#### <a name="issue"></a>Probléma

Ha a [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook használja az Azure-modulok frissítésére, a modul frissítése felfüggeszti a frissítési folyamatot.

#### <a name="cause"></a>Ok

Az alapértelmezett beállítás azt határozza meg, hogy hány modult kell frissíteni egyszerre 10 a `Update-AzureModule.ps1` parancsfájl használata esetén. A frissítési folyamat olyan hibákhoz van kitéve, amikor túl sok modul frissül egyidejűleg.

#### <a name="resolution"></a>Megoldás:

Nem gyakori, hogy az összes AzureRM-modulnak ugyanabban az Automation-fiókban kell lennie. Javasoljuk, hogy csak a szükséges AzureRM-modulokat importálja.

> [!NOTE]
> Kerülje a **AzureRM** modul importálását. A **AzureRM** modulok importálása az összes **AzureRM okozza.\*** az importálandó modulokat, ez nem recommened.

Ha a frissítési folyamat felfüggeszti a műveletet, hozzá kell adnia a `SimultaneousModuleImportJobCount` paramétert a `Update-AzureModules.ps1` parancsfájlhoz, és kisebb értéket kell megadnia, mint a 10-es alapértelmezett érték. Ha ezt a logikát alkalmazza, a 3 vagy 5 értékkel kezdődik. a `SimultaneousModuleImportJobCount` az Azure-modulok frissítéséhez használt `Update-AutomationAzureModulesForAccount` rendszer runbook paramétere. Ez a változás a folyamat futását is lehetővé teszi, de nagyobb eséllyel fejeződik be. A következő példa a paramétert mutatja be, és hová helyezi a runbook:

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

### <a name="unable-create-update"></a>Forgatókönyv: nem lehet futtató fiókot létrehozni vagy frissíteni

#### <a name="issue"></a>Probléma

Amikor megpróbál létrehozni vagy frissíteni egy futtató fiókot, a következő hibaüzenethez hasonló hibaüzenet jelenik meg:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Ok

Nem rendelkezik a futtató fiók létrehozásához vagy frissítéséhez szükséges engedélyekkel, vagy az erőforrás zárolva van egy erőforráscsoport szintjén.

#### <a name="resolution"></a>Megoldás:

Futtató fiók létrehozásához vagy frissítéséhez megfelelő engedélyekkel kell rendelkeznie a futtató fiók által használt különféle erőforrásokhoz. A futtató fiók létrehozásához vagy frissítéséhez szükséges engedélyekről a [futtató fiók engedélyei](../manage-runas-account.md#permissions)című témakörben olvashat bővebben.

Ha a probléma egy zárolás miatt van, ellenőrizze, hogy a zárolás rendben van-e az eltávolításához. Ezután navigáljon a zárolt erőforráshoz, kattintson a jobb gombbal a zárolásra, és válassza a **Törlés** lehetőséget a zárolás eltávolításához.

### <a name="iphelper"></a>Forgatókönyv: "nem található" GetPerAdapterInfo "nevű belépési pont a (z) iplpapi. dll FÁJLBAN a runbook végrehajtásakor.

#### <a name="issue"></a>Probléma

Runbook végrehajtásakor a következő kivétel jelenik meg:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Ok

Ezt a hibát valószínűleg egy helytelenül konfigurált [futtató fiók](../manage-runas-account.md)okozza.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a [futtató fiók](../manage-runas-account.md) megfelelően van konfigurálva. Ha megfelelően van konfigurálva, ellenőrizze, hogy rendelkezik-e a megfelelő kóddal a runbook az Azure-beli hitelesítéshez. Az alábbi példa egy kódrészletet mutat be az Azure-ban való hitelesítéshez egy runbook egy futtató fiók használatával.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
