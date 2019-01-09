---
title: Konfigurálja az Update Management üzemelő példányon, az Azure-ban (előzetes verzió) előtti és utáni szkriptek
description: Ez a cikk bemutatja, hogyan konfigurálhat és felügyelhet előtti és utáni parancsfájlokat a frissítéstelepítések
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edbb953111fb4589539369bd9b2519b48b9b70eb
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121311"
---
# <a name="manage-pre-and-post-scripts-preview"></a>Kezelése előtti és utáni parancsfájlokat (előzetes verzió)

Előtti és utáni szkriptek lehetővé teszik a PowerShell-forgatókönyvek az Automation-fiókban (előtti feladat) előtt és után (utáni) frissítés központi telepítés. Előtti és utáni parancsfájlokat futtatni, az Azure környezetben, és nem helyileg. Előtti parancsfájlok futtatása a központi telepítési elején. Parancsfájlok a bejegyzés végén az üzemelő példány, és minden konfigurált újraindítása után futtassa.

## <a name="runbook-requirements"></a>Forgatókönyv követelményei

Egy runbook előtti vagy utáni parancsfájl használható a runbook importálja az automation-fiókját, és közzé kell. Ez a folyamat kapcsolatos további információkért lásd: [runbook közzététele](automation-creating-importing-runbook.md#publishing-a-runbook).

## <a name="using-a-prepost-script"></a>Előkészítő/utólagos parancsfájl használatával

A frissítéstelepítés és előre, vagy utáni parancsfájl használata, a Frissítéstelepítések létrehozásával egyszerűen indítsa el. Válassza ki **előkészítő parancsfájljainak + utáni parancsfájlok (előzetes verzió)**. Ekkor megnyílik a **előkészítő parancsfájljainak kiválasztása + utáni parancsfájlok** lapot.  

![Jelölje be a parancsfájlok](./media/pre-post-scripts/select-scripts.png)

Válassza ki a parancsprogramot használja, ebben a példában szeretne, akkor használja a **UpdateManagement-TurnOnVms** runbook. Amikor kiválasztja a runbook a **parancsfájl konfigurálása** lap megnyitásakor, a paraméterek értékének megadására és kiválasztására **az előkészítő parancsprogram**. Kattintson a **OK** végeztével.

![Szkript konfigurálása](./media/pre-post-scripts/configure-script.png)

Ismételje meg ezt a folyamatot a a **UpdateManagement-TurnOffVms** parancsfájlt. De amikor kiválasztja a **parancsprogram típusa**, válassza a **az utólagos parancsprogram**.

A **kiválasztott cikkek** szakaszban most látható, mind a kiválasztott parancsfájlok és a egy szkript előtti és más utáni parancsfájlt.

![Kijelölt elemek](./media/pre-post-scripts/selected-items.png)

A frissítéstelepítés konfigurálásának befejezéséhez.

A frissítéstelepítés befejeződése után nyissa meg **Frissítéstelepítések** az eredmények megtekintéséhez. Amint láthatja a szkript előtti és utáni azok állapotát a megadott.

![Eredmények frissítése](./media/pre-post-scripts/update-results.png)

A frissítéstelepítés, akkor futtassa tárolóiba kattintva további részleteket a előtti és utáni parancsfájlokat szeretne állnak rendelkezésre. A futtatás idején a parancsfájl forrás találhatóak.

![Központi telepítés futtatási eredmények](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Paraméterek átadása

Konfigurálásakor például adhat át a paraméterek csak előtti és utáni parancsfájlokat a runbook ütemezése. Paraméterek vannak meghatározva a frissítés központi telepítés létrehozása idején. A standard szintű runbook paraméterek kívül egy további paraméter biztosítunk. Ez a paraméter **SoftwareUpdateConfigurationRunContext**. Ez a paraméter egy JSON-karakterlánc, és ha a előtti vagy utáni parancsfájl adja meg a paramétert, automatikusan átadott a frissítéstelepítés által. A paraméter tartalmazza a frissítések telepítését, amelynek része a információ által visszaadott információk a [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) az alábbi táblázat bemutatja, a tulajdonságok vannak megadva, a változóban:

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext tulajdonságai

|Tulajdonság  |Leírás  |
|---------|---------|
|SoftwareUpdateConfigurationName     | A szoftverfrissítési konfiguráció neve        |
|SoftwareUpdateConfigurationRunId     | A Futtatás egyedi azonosítója.        |
|SoftwareUpdateConfigurationSettings     | A szoftverfrissítési konfiguráció kapcsolódó tulajdonságok gyűjteménye         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Az operációs rendszerek, a központi telepítési megcélzott         |
|SoftwareUpdateConfigurationSettings.duration     | A frissítéstelepítés maximális időtartama futtató `PT[n]H[n]M[n]S` megfelelően ISO8601, más néven a "karbantartási időszak"          |
|SoftwareUpdateConfigurationSettings.Windows     | Windows-számítógépek kapcsolódó tulajdonságok gyűjteménye         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | A frissítéstelepítés kizárt Tudásbázis listája        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | A frissítés telepítésre kijelölt frissítési besorolások        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Indítsa újra a központi telepítési beállítások        |
|azureVirtualMachines     | Az Azure-beli virtuális gépek a frissítéstelepítés ResourceId listája        |
|nonAzureComputerNames|A frissítéstelepítés nem Azure-beli számítógépek teljes tartománynevek listája|

A következő függvénynek átadott JSON-karakterláncot példája a **SoftwareUpdateConfigurationRunContext** paramétert:

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

Az összes tulajdonság egy teljes példát tekinthet meg: [Frissítés szoftverkonfigurációjáról - Get-név alapján](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

## <a name="samples"></a>Példák

Minták előtti és utáni parancsfájlokat a találja a [Script Center-galéria](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), vagy importált az Azure Portalon keresztül. Importálhatja őket a portálon keresztül, az Automation-fiók alatt **Folyamatautomatizálás**válassza **Runbookkatalógus**. Használat **az Update Management** a szűrőhöz.

![Gyűjteménylista](./media/pre-post-scripts/runbook-gallery.png)

Vagy Ön is megkeresheti azokat a szkript neve szerint a következő listában látható módon:

* Az Update Management - es virtuális gépek
* Az Update Management – virtuális gép kikapcsolása
* Az Update Management – helyi futtatási szkript
* Update Management - előkészítő/utólagos parancsfájl sablon
* Az Update Management – futtatási szkript futtatása paranccsal

> [!IMPORTANT]
> Miután importálta a runbookokat, kell **közzététel** azok használhatók legyenek. Ehhez, keresse meg a runbook az Automation-fiókját, válassza ki a **szerkesztése**, és kattintson a **közzététel**.

A minták a egyszerű sablon, amely a következő példában definiálva van a alapulnak. Ez a sablon előtti és utáni parancsfájlok használata a saját forgatókönyv létrehozására használható. A hitelesítés az Azure-ral, valamint a kezelési szükséges logikát a `SoftwareUpdateConfigurationRunContext` paraméter részét képezik.

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
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines 
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

## <a name="interacting-with-non-azure-machines"></a>Nem Azure-gépek használata

Előtti és utáni feladatokat Azure környezetében futnak, és nem rendelkeznek hozzáféréssel a nem Azure-gépek. Annak érdekében, hogy a nem Azure-gépek kommunikáljanak az alábbiakkal kell rendelkeznie:

* Futtató fiók
* Hibrid Runbook-feldolgozó telepítve a számítógépen
* Helyileg futtatni kívánt runbook
* Szülő runbook

Együttműködhet a nem Azure-beli gépek a szülő runbook futott, az Azure környezetben. Ez a forgatókönyv a gyermek runbookot hívja a [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) parancsmagot. Meg kell adnia a `-RunOn` paramétert, és adja meg a parancsfájl futtatása hibrid Runbook-feldolgozó neve.

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="known-issues"></a>Ismert problémák

* Nem adhatók át objektum vagy tömb paraméterek előtti és utáni parancsfájlok használata esetén. A runbook sikertelen lesz.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyagban megtudhatja, hogyan kezelheti a frissítéseket a Windows virtuális gépek számára.

> [!div class="nextstepaction"]
> [Az Azure Windows rendszerű virtuális gépek frissítéseinek és javításainak kezelése](automation-tutorial-update-management.md)
