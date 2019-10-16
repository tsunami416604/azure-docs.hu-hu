---
title: A Update Management üzembe helyezés előtti és utáni parancsfájlok konfigurálása az Azure-ban
description: Ez a cikk bemutatja, hogyan konfigurálhatja és kezelheti a frissítési központi telepítések előtti és utáni parancsfájlokat
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84cd5db812d995f1160a02917eac5857ee076c7f
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374443"
---
# <a name="manage-pre-and-post-scripts"></a>A parancsfájlok előtti és utáni kezelése

A parancsfájl előtti és utáni parancsfájlok lehetővé teszik a PowerShell-runbookok futtatását az Automation-fiókban, mielőtt (a feladat előtt) és utána (feladat utáni) egy frissítés központi telepítését. A parancsfájlok előzetes és utáni futtatása az Azure-környezetben és nem helyileg történik. Az előzetes parancsfájlok a frissítés központi telepítésének elején futnak. A parancsfájlokat a telepítés végén, a beállított újraindítások után futtatva kell közzétenni.

## <a name="runbook-requirements"></a>Runbook-követelmények

Ahhoz, hogy egy runbook előzetes vagy post parancsfájlként lehessen használni, a runbook importálni kell az Automation-fiókjába, és közzé kell tenni. További információ erről a folyamatról: [Runbook közzététele](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Pre/post parancsfájl használata

Ha egy frissítés előtti vagy utáni parancsfájlt szeretne használni egy frissítési telepítésben, kezdje egy frissítés központi telepítésének létrehozásával. Válassza a **Pre-Scripts + szkriptek közzététele**lehetőséget. Ez a művelet megnyitja a **Select pre-Scripts + post-Scripts** oldalt.

![Parancsfájlok kiválasztása](./media/pre-post-scripts/select-scripts.png)

Válassza ki a használni kívánt parancsfájlt, ebben a példában a **UpdateManagement-TurnOnVms** runbook használta. Ha a runbook **beállítása** lap megnyílik, válassza a **Pre-script**(parancsfájl létrehozása) lehetőséget. Ha elkészült, kattintson **az OK gombra** .

Ismételje meg ezt a folyamatot a **UpdateManagement-TurnOffVms** szkriptnél. A **parancsfájl típusának**kiválasztásakor azonban válassza a **parancsfájl közzététele**lehetőséget.

A **kijelölt elemek** szakasz most már megjeleníti a kiválasztott parancsfájlokat, a pedig egy előkészítő parancsfájlt, a másik pedig egy parancsfájl utáni.

![Kijelölt elemek](./media/pre-post-scripts/selected-items.png)

Fejezze be a frissítés központi telepítésének konfigurálását.

A frissítés telepítésének befejezésekor az eredmények megtekintéséhez lépjen a **frissítés központi telepítések** lehetőségre. Amint láthatja, a pre-script és a szkript is megjelenik.

![Eredmények frissítése](./media/pre-post-scripts/update-results.png)

Ha a frissítés központi telepítésére kattint, további részleteket is megadhat az előzetes és a szkriptek futtatásához. A parancsfájl forrására mutató hivatkozás van megadva a Futtatás időpontjában.

![A telepítés futtatásának eredményei](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Átadási paraméterek

Az előfizetések előtti és utáni parancsfájlok konfigurálásakor a paramétereket ugyanúgy lehet átadni, mint a runbook ütemezését. A paraméterek meghatározása a frissítés központi telepítésének időpontjában történik. A parancsfájl előtti és utáni parancsfájlok a következő típusokat támogatják:

* char
* byte
* int
* hosszú
* decimális
* egyetlen
* duplán
* DateTime
* karakterlánc

Ha más típusú objektumra van szüksége, a runbook saját logikával elvégezheti egy másik típusra.

A standard runbook paraméterek mellett további paraméter is meg van megadva. Ez a paraméter **SoftwareUpdateConfigurationRunContext**. Ez a paraméter egy JSON-karakterlánc, és ha a paramétert az előzetes vagy utáni parancsfájlban definiálja, a rendszer automatikusan átadja a frissítés központi telepítésének. A paraméter a frissítés központi telepítésére vonatkozó információkat tartalmaz, amely a [SOFTWAREUPDATECONFIGURATIONS API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) által visszaadott információk részhalmaza. a következő táblázat a változóban megadott tulajdonságokat mutatja be:


### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext tulajdonságai

|Tulajdonság  |Leírás  |
|---------|---------|
|SoftwareUpdateConfigurationName     | A szoftverfrissítés konfigurációjának neve        |
|SoftwareUpdateConfigurationRunId     | A Futtatás egyedi azonosítója.        |
|SoftwareUpdateConfigurationSettings     | A szoftverfrissítés konfigurációjával kapcsolatos tulajdonságok gyűjteménye         |
|SoftwareUpdateConfigurationSettings. operatingSystem     | A frissítés központi telepítését célzó operációs rendszerek         |
|SoftwareUpdateConfigurationSettings. időtartam     | A frissítés központi telepítésének maximális időtartama `PT[n]H[n]M[n]S`, mint ISO8601, más néven a "karbantartási időszak"          |
|SoftwareUpdateConfigurationSettings. Windows     | Windows rendszerű számítógépekhez kapcsolódó tulajdonságok gyűjteménye         |
|SoftwareUpdateConfigurationSettings. Windows. excludedKbNumbers     | A frissítés központi telepítésből kizárt Tudásbázis listája        |
|SoftwareUpdateConfigurationSettings. Windows. includedUpdateClassifications     | A frissítés központi telepítésére kijelölt frissítési besorolások        |
|SoftwareUpdateConfigurationSettings. Windows. rebootSetting     | A frissítés telepítésének újraindítási beállításai        |
|azureVirtualMachines     | Az Azure-beli virtuális gépek ResourceId: listája a frissítés központi telepítésében        |
|nonAzureComputerNames|A nem Azure-beli számítógépek teljes tartománynevének listája a frissítés központi telepítésében|

Az alábbi példa egy, a **SoftwareUpdateConfigurationRunContext** paraméternek átadott JSON-karakterlánc:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Az összes tulajdonsággal rendelkező teljes példa a következő címen érhető el: [szoftverfrissítési konfigurációk – Get by Name](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> A `SoftwareUpdateConfigurationRunContext` objektum tartalmazhat duplikált bejegyzéseket a gépekhez. Ennek hatására előfordulhat, hogy az előfizetések előtti és utáni parancsfájlok többször is futhatnak ugyanazon a gépen. A viselkedés megkerülő megoldásához a `Sort-Object -Unique` használatával válassza ki a parancsfájlban csak az egyedi virtuális gépek nevét.


## <a name="stopping-a-deployment"></a>Központi telepítés leállítása

Ha egy előzetes parancsfájl alapján szeretné leállítani a központi telepítést, kivételt kell [eldobnia](automation-runbook-execution.md#throw) . Ha nem ad meg kivételt, az üzembe helyezés és a közzététel utáni parancsfájl továbbra is futni fog. Az alábbi kódrészlet bemutatja, hogyan lehet kivételt kidobni.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="samples"></a>Minták

Az előzetes és utáni parancsfájlokra vonatkozó minták a [parancsfájl-központot tartalmazó galériában](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), [PowerShell-galéria](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)vagy a Azure Portal keresztül importálhatók. A portálon keresztüli importáláshoz az Automation-fiókban a **folyamat automatizálása**területen válassza a **runbookok**-katalógus elemet. **Update Management** használata a szűrőhöz.

![Katalógusok listája](./media/pre-post-scripts/runbook-gallery.png)

Az alábbi listában látható módon is megkeresheti a parancsfájl nevét:

* Update Management – virtuális gépek bekapcsolása
* Update Management – virtuális gépek kikapcsolása
* Update Management parancsfájl helyi futtatása
* Update Management – sablon a Pre/post parancsfájlokhoz
* Update Management parancsfájl futtatása a Run paranccsal

> [!IMPORTANT]
> A runbookok importálása után **közzé** kell tenni azokat a használatuk előtt. Ha szeretné megkeresni a runbook az Automation-fiókban, válassza a **Szerkesztés**lehetőséget, majd kattintson a **Közzététel**elemre.

A mintákat a következő példában definiált alapszintű sablon alapján kell megadni. Ezzel a sablonnal létrehozhatók a saját runbook, amelyeket az előzetes és a parancsfájlokkal való használatra használhat. Az Azure-ban való hitelesítéshez és a `SoftwareUpdateConfigurationRunContext` paraméter felügyeletéhez szükséges logika a része.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management Pre/Post scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>Interakció a gépekkel

A feladatok előtti és utáni műveletek runbook az Automation-fiókban, és nem közvetlenül az üzemelő példányban lévő gépeken. Az előzetes és utáni feladatok az Azure-környezetben is futnak, és nem férnek hozzá a nem Azure-beli gépekhez. Az alábbi fejezetek azt mutatják be, hogyan lehet közvetlenül kommunikálni a géppel, függetlenül attól, hogy Azure-vagy nem Azure-beli virtuális gép:

### <a name="interacting-with-azure-machines"></a>Interakció az Azure Machines szolgáltatással

A tevékenységek előtti és utáni feladatok a runbookok futnak, és nem futnak natív módon az Azure-beli virtuális gépeken a telepítés során. Az Azure-beli virtuális gépekkel való kommunikációhoz a következő elemek szükségesek:

* Futtató fiók
* Egy futtatni kívánt runbook

Az Azure-gépekkel való kommunikációhoz használja a [Meghívási-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) parancsmagot az Azure-beli virtuális gépekkel való kommunikációhoz. Ennek módjáról a következő témakörben talál példát: runbook példa [Update Management – parancsfájl futtatása a Run paranccsal](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interakció a nem Azure-beli gépekkel

A feladatok előtti és utáni műveletek az Azure-környezetben futnak, és nem férnek hozzá a nem Azure-beli gépekhez. A nem Azure-beli gépekkel való kommunikációhoz a következő elemek szükségesek:

* Futtató fiók
* A gépre telepített hibrid Runbook Worker
* Helyileg futtatni kívánt runbook
* Szülő runbook

A nem Azure-beli gépekkel való kommunikációhoz egy szülő runbook fut az Azure-környezetben. Ez a runbook a [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) parancsmaggal hívja meg a gyermek runbook. Meg kell adnia a `-RunOn` paramétert, és meg kell adnia annak a hibrid Runbook-feldolgozónak a nevét, amelyen futtatni szeretné a parancsfájlt. Példaként tekintse meg a runbook példát [Update Management-szkript helyi futtatását](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Javítás telepítésének megszakítása

Ha az előkészítő parancsfájl hibát ad vissza, érdemes lehet megszakítani az üzemelő példányt. Ehhez [meg kell adnia](/powershell/module/microsoft.powershell.core/about/about_throw) egy hibát a parancsfájlban bármely olyan logikánál, amely hibát jelentene.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Ismert problémák

* Az előfizetések előtti és utáni parancsfájlok használatakor nem adhat át logikai, objektumok vagy tömböket paraméterekre. A runbook sikertelen lesz. A támogatott típusok teljes listáját lásd: [Paraméterek](#passing-parameters).

## <a name="next-steps"></a>Következő lépések

Folytassa az Oktatóanyaggal, amelyből megtudhatja, hogyan kezelheti a Windows rendszerű virtuális gépek frissítéseit.

> [!div class="nextstepaction"]
> [Az Azure Windows rendszerű virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)

