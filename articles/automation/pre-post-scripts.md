---
title: Az Azure-ban Update Management üzembe helyezés előtti parancsfájlok és parancsfájl-közzétételi parancsfájlok kezelése
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja és kezelheti az előzetes parancsfájlokat és a parancsfájlok közzétételét a frissítések központi telepítéséhez.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: f55ebb3270fdd97a1fdbbf5a56f9703c08933f9f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855336"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Parancsfájlok előtti és utáni parancsfájlok kezelése

A szkriptek előtti és utáni parancsfájlokat a rendszer a frissítés központi telepítésének megkezdése előtt (a feladat előtt) és után (feladat után) runbookok az Azure Automation-fiókban. A parancsfájl előtti és a parancsfájl utáni Futtatás az Azure-környezetben nem helyileg történik. Az előzetes parancsfájlok a frissítés központi telepítésének elején futnak. A parancsfájlok futtatása a telepítés végén, illetve a konfigurált újraindítások után történik.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="pre-script-and-post-script-requirements"></a>Parancsfájl előtti és parancsfájl utáni követelmények

Ahhoz, hogy egy runbook előre megírt vagy post-parancsfájlként lehessen használni, importálnia kell az Automation-fiókjába, és [közzé kell tennie a runbook](manage-runbooks.md#publish-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parancsfájl előtti és parancsfájl utáni paraméterek

A parancsfájlok előtti és utáni parancsfájlok konfigurálásakor a paramétereket ugyanúgy adhatja át, mint a runbook ütemezését. A paraméterek meghatározása a frissítés központi telepítésének időpontjában történik. A parancsfájlok előtti és utáni parancsfájlok a következő típusokat támogatják:

* char
* byte
* int
* hosszú
* decimális
* egyetlen
* duplán
* DateTime
* karakterlánc

A parancsfájl előtti és a parancsfájl utáni runbook paraméterek nem támogatják a logikai, az objektum vagy a tömb típusát. Ezek az értékek miatt a runbookok sikertelen lesz. 

Ha más típusú objektumra van szüksége, a runbook saját logikával elvégezheti egy másik típusra.

A standard runbook paraméterek mellett a `SoftwareUpdateConfigurationRunContext` paraméter (JSON-karakterlánc típus) is meg van megadva. Ha a paramétert az előkészítő parancsfájlban vagy a parancsfájlt követő runbook adja meg, a rendszer automatikusan továbbítja azt a frissítés központi telepítésében. A paraméter a frissítés központi telepítésére vonatkozó információkat tartalmaz, amely a [SOFTWAREUPDATECONFIGURATIONS API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)által visszaadott információk részhalmaza. Az alábbi szakaszban megadhatja a társított tulajdonságokat.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext tulajdonságai

|Tulajdonság  |Leírás  |
|---------|---------|
|SoftwareUpdateConfigurationName     | A szoftverfrissítés konfigurációjának neve.        |
|SoftwareUpdateConfigurationRunId     | A Futtatás egyedi azonosítója.        |
|SoftwareUpdateConfigurationSettings     | A szoftverfrissítés konfigurációjával kapcsolatos tulajdonságok gyűjteménye.         |
|SoftwareUpdateConfigurationSettings. operatingSystem     | A frissítés központi telepítésére irányuló operációs rendszerek.         |
|SoftwareUpdateConfigurationSettings. időtartam     | A frissítés központi telepítésének `PT[n]H[n]M[n]S` maximális időtartama ISO8601; más néven a karbantartási időszak.          |
|SoftwareUpdateConfigurationSettings. Windows     | Windows rendszerű számítógépekhez kapcsolódó tulajdonságok gyűjteménye.         |
|SoftwareUpdateConfigurationSettings. Windows. excludedKbNumbers     | A frissítés központi telepítésből kizárt Tudásbázis listája.        |
|SoftwareUpdateConfigurationSettings. Windows. includedUpdateClassifications     | A frissítés telepítéséhez kiválasztott besorolások.        |
|SoftwareUpdateConfigurationSettings. Windows. rebootSetting     | Újraindítási beállítások a frissítés központi telepítéséhez.        |
|azureVirtualMachines     | Az Azure-beli virtuális gépek ResourceId: listája a frissítés központi telepítésében.        |
|nonAzureComputerNames|A nem Azure-beli számítógépek teljes tartománynevének listája a frissítés központi telepítésében.|

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

Az összes tulajdonsággal rendelkező teljes példa a következő helyen található: a [szoftverfrissítés konfigurációjának beolvasása név alapján](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Az `SoftwareUpdateConfigurationRunContext` objektum tartalmazhat duplikált bejegyzéseket a gépekhez. Ennek hatására előfordulhat, hogy az előparancsfájlok és a szkriptek többször is futhatnak ugyanazon a gépen. Ennek a viselkedésnek a megkerülő megoldásához a használatával `Sort-Object -Unique` válassza ki a csak egyedi virtuális gépek nevét.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>A telepítés előtti vagy utáni parancsfájl használata

Ha egy frissítés előtti vagy utáni parancsfájlt szeretne használni egy frissítési telepítésben, először hozzon létre egy frissítés központi telepítését. Válassza a **Pre-Scripts + post-Scripts**elemet. Ez a művelet megnyitja a **Select pre-Scripts + post-Scripts** oldalt.

![Parancsfájlok kiválasztása](./media/pre-post-scripts/select-scripts.png)

Válassza ki a használni kívánt parancsfájlt. Ebben a példában a **UpdateManagement-TurnOnVms** runbook használjuk. Amikor kiválasztja a runbook, megnyílik a **parancsfájl konfigurálása** oldal. Válassza az **előzetes parancsfájl**lehetőséget, majd kattintson **az OK gombra**.

Ismételje meg ezt a folyamatot a **UpdateManagement-TurnOffVms** szkriptnél. Ha azonban a **parancsfájl típusát**választja, válassza a **post-script**elemet.

A **kijelölt elemek** szakasz most már a kijelölt parancsfájlokat is megjeleníti. Az egyik egy előzetes parancsfájl, a másik pedig egy parancsfájl utáni:

![Kijelölt elemek](./media/pre-post-scripts/selected-items.png)

Fejezze be a frissítés központi telepítésének konfigurálását.

A frissítés telepítésének befejezésekor az eredmények megtekintéséhez lépjen a **frissítés központi telepítések** lehetőségre. Amint láthatja, a rendszer a parancsfájl előtti és utáni parancsfájl állapotát is megadja:

![Eredmények frissítése](./media/pre-post-scripts/update-results.png)

A frissítés központi telepítésének kiválasztásával további részleteket láthat a parancsfájlok előtti és utáni parancsfájlokban. A parancsfájl forrására mutató hivatkozás van megadva a Futtatás időpontjában.

![A telepítés futtatásának eredményei](./media/pre-post-scripts/deployment-run.png)

es a szkriptben.

## <a name="stopping-a-deployment"></a>Központi telepítés leállítása

Ha egy előzetes parancsfájl alapján szeretné leállítani a központi telepítést, kivételt kell [eldobnia](automation-runbook-execution.md#throw) . Ha nem, a telepítés és a parancsfájl utáni Futtatás továbbra is futni fog. A következő kódrészlet bemutatja, hogyan lehet kivételt kidobni.

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



## <a name="interacting-with-machines"></a>Interakció a gépekkel

A parancsfájlok előtti és utáni feladatok az Automation-fiókban runbookok futnak, és nem közvetlenül a telepítésben lévő gépekre. A feladatok előtti és utáni feladatok is az Azure-környezetben futnak, és nem férnek hozzá a nem Azure-beli gépekhez. A következő részben bemutatjuk, hogyan használhatja közvetlenül a gépeket, akár Azure-beli, akár nem Azure-beli virtuális gépekről van szó.

### <a name="interact-with-azure-machines"></a>Együttműködés az Azure-gépekkel

A feladatok előtti és utáni feladatokat futtató runbookok, és a telepítés során nem futnak natív módon az Azure-beli virtuális gépeken. Az Azure-beli virtuális gépekkel való kommunikációhoz a következő elemek szükségesek:

* Futtató fiók
* Egy futtatni kívánt runbook

Az Azure-gépekkel való kommunikációhoz használja a [Meghívási-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) parancsmagot az Azure-beli virtuális gépekkel való kommunikációhoz. Ennek módjáról a runbook példa [Update Management – parancsfájl futtatása a Run paranccsal](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)című témakörben talál példát.

### <a name="interact-with-non-azure-machines"></a>Interakció a nem Azure-beli gépekkel

A feladatok előtti és utáni feladatok az Azure-környezetben futnak, és nem férnek hozzá a nem Azure-beli gépekhez. A nem Azure-beli gépekkel való kommunikációhoz a következő elemek szükségesek:

* Futtató fiók
* A gépre telepített hibrid Runbook Worker
* Helyileg futtatni kívánt runbook
* Egy szülő runbook

A nem Azure-beli gépekkel való kommunikációhoz egy szülő runbook fut az Azure-környezetben. Ez a runbook a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) parancsmaggal hívja meg a gyermek runbook. Meg kell adnia a `RunOn` paramétert, és meg kell adnia annak a hibrid Runbook-feldolgozónak a nevét, amelyen futtatni szeretné a parancsfájlt. Tekintse meg a runbook példát [Update Management – parancsfájl helyi futtatása](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="aborting-patch-deployment"></a>A javítás telepítésének megszakítása

Ha az előkészítő parancsfájl hibát ad vissza, érdemes lehet megszakítani az üzemelő példányt. Ehhez [meg kell adnia](/powershell/module/microsoft.powershell.core/about/about_throw) egy hibát a parancsfájlban bármely olyan logikánál, amely hibát jelentene.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Példák

A parancsfájl-előkészítők és a szkriptek utáni parancsfájlok mintáit a [Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) -katalógusban és a [PowerShell-Galéria](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)is megtalálhatja, vagy importálhatja őket a Azure Portal használatával. Ehhez az Automation-fiók **folyamat automatizálása**területén válassza a **runbookok**-katalógus elemet. **Update Management** használata a szűrőhöz.

![Katalógusok listája](./media/pre-post-scripts/runbook-gallery.png)

A parancsfájl neve alapján is megkeresheti őket, ahogy az a következő listában látható:

* Update Management – virtuális gépek bekapcsolása
* Update Management – virtuális gépek kikapcsolása
* Update Management parancsfájl helyi futtatása
* Update Management – sablon a Pre/post parancsfájlokhoz
* Update Management parancsfájl futtatása a Run paranccsal

> [!IMPORTANT]
> A runbookok importálása után közzé kell tenni azokat a használatuk előtt. Ehhez keresse meg a runbook az Automation-fiókban, válassza a **Szerkesztés**lehetőséget, majd válassza a **Közzététel**lehetőséget.

A mintákat a következő példában definiált alapszintű sablon alapján kell megadni. Ez a sablon a saját runbook létrehozására használható a parancsfájlok előtti és a parancsfájlok utáni használatra. Az Azure-ban való hitelesítéshez és a `SoftwareUpdateConfigurationRunContext` paraméter kezelésére szolgáló szükséges logika beletartozik.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
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

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> A nem grafikus PowerShell-runbookok, `Add-AzAccount` valamint `Add-AzureRMAccount` a [csatlakozási-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)aliasai. Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan kezelheti a Windows rendszerű virtuális gépek frissítéseit:

> [!div class="nextstepaction"]
> [Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításainak kezelése](automation-tutorial-update-management.md)