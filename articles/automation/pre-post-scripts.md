---
title: Előzetes parancsfájlok és utóparancsfájlok kezelése az Azure-beli frissítéskezelés központi telepítésében
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja és kezelheti az elő-parancsfájlok és a parancsfájlok utáni központi telepítések.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 00cde5255f9c9a2baa7c7042ae2a8f73448da0ae
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679992"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Elő- és utóparancsfájlok kezelése

Az elő- és post-scriptek runbookok az Azure Automation-fiókban futtatható (feladat előtti) és azt követően (utólagos) frissítési központi telepítés előtt. Az előzetes parancsfájlok és a parancsfájlok utáni parancsfájlok az Azure-környezetben futnak, nem helyileg. Az előparancsfájlok a frissítés központi telepítésének elején futnak. A parancsfájlok utáni futtatása a központi telepítés végén és a konfigurált újraindítások után történik.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="pre-script-and-post-script-requirements"></a>Elő- és utóparancsfájlra vonatkozó követelmények

Ahhoz, hogy egy runbook elő- vagy parancsfájlt használjon, importálnia kell azt az Automation-fiókba, és közzé kell [tennie a runbookot.](manage-runbooks.md#publishing-a-runbook)

## <a name="pre-script-and-post-script-parameters"></a>Elő- és utó-script paraméterek

Az előzetes parancsfájlok és a parancsfájlok utáni konfigurálásakor a paramétereket ugyanúgy átadhatja, mint a runbook ütemezését. A paraméterek a frissítés központi telepítésének létrehozásakor vannak definiálva. Az elő- és utóparancsfájlok a következő típusokat támogatják:

* - Nem, nem, nem, nem, nem,
* [bájt]
* Nem, nem, nem, nem, nem, nem, nem,
* Nem, nem, nem, nem, nem, nem
* [decimális]
* [egyedülálló]
* Nem, nem, nem, nem, nem, nem
* [DateTime]
* - Nem, nem, nem, nem, nem,

A parancsfájl előtti és a parancsfájl utáni runbook-paraméterek nem támogatják a logikai, objektum- vagy tömbtípusokat. Ezek az értékek a runbookok sikertelensét okozzák. 

Ha másik objektumtípusra van szüksége, a runbooksaját logikájával egy másik típusra is levezetheti.

A szabványos runbook-paraméterek mellett `SoftwareUpdateConfigurationRunContext` a paraméter (JSON-karakterlánc típus) is meg van adva. Ha a paramétert az elő-parancsfájl vagy a parancsfájl utáni runbook, a frissítés központi telepítése automatikusan továbbítja. A paraméter a frissítés központi telepítésével kapcsolatos információkat tartalmaz, amely a [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)által visszaadott információk egy részhalmaza. Az alábbi szakaszok határozzák meg a társított tulajdonságokat.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext tulajdonságai

|Tulajdonság  |Leírás  |
|---------|---------|
|SoftwareUpdateConfigurationName     | A szoftverfrissítési konfiguráció neve.        |
|SoftwareUpdateConfigurationRunId     | A futtatás egyedi azonosítója.        |
|SoftwareUpdateConfigurationSettings     | A szoftverfrissítés konfigurációjához kapcsolódó tulajdonságok gyűjteménye.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | A frissítés központi telepítéséhez megcélzott operációs rendszerek.         |
|SoftwareUpdateConfigurationSettings.duration     | A frissítés telepítésének maximális `PT[n]H[n]M[n]S` időtartama az ISO8601 szerint fut; más néven a karbantartási ablak.          |
|SoftwareUpdateConfigurationSettings.Windows     | Windows rendszerű számítógépekkel kapcsolatos tulajdonságok gyűjteménye.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | A frissítés központi telepítéséből kizárt kb-k listája.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | A frissítés központi telepítéséhez kijelölt besorolások frissítése.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | A frissítés központi telepítésének újraindítási beállításai.        |
|azureVirtualMachines     | Az Azure-beli virtuális gépek erőforrás-azonosítók listája a frissítés központi telepítésében.        |
|nonAzureComputerNames|A nem Azure-beli számítógépek teljes tartománynait a frissítés központi telepítésében.|

A következő példa egy JSON-karakterlánc, amely et a **SoftwareUpdateConfigurationRunContext** paraméternek adták át:

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

Egy teljes példa az összes tulajdonsággal megtalálható a következő helyen: [Szoftverfrissítés-konfiguráció beszerezni név szerint](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Az `SoftwareUpdateConfigurationRunContext` objektum tartalmazhat ismétlődő bejegyzéseket a gépekhez. Ez azt eredményezheti, hogy az elő- és utóparancsfájlok többször is futnak ugyanazon a gépen. A probléma kerülő `Sort-Object -Unique` megoldásához csak egyedi virtuálisgép-neveket jelöljön ki.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Parancsfájl előtti vagy utóparancsfájl használata központi telepítésben

Ha parancsfájl előtti vagy post-scriptet szeretne használni egy frissítési központi telepítésben, először hozzon létre egy központi telepítést. Válassza **az Előszkriptek + Parancsfájlok könyvelése lehetőséget.** Ez a művelet megnyitja az **Előszkriptek kiválasztása + Parancsfájlok utánklapot.**

![Parancsfájlok kijelölése](./media/pre-post-scripts/select-scripts.png)

Jelölje ki a használni kívánt parancsfájlt. Ebben a példában az **UpdateManagement-TurnOnVms** runbookot használjuk. A runbook kiválasztásakor megnyílik a **Parancsfájl konfigurálása** lap. Válassza **az Előszkript**lehetőséget, majd az **OK**gombot.

Ismételje meg ezt a folyamatot az **UpdateManagement-TurnOffVms** parancsfájlban. Ha azonban a **Parancsfájl típusát választja,** válassza a **Post-Script**lehetőséget.

A **Kijelölt elemek** szakasz mostantól mindkét kijelölt parancsfájlt megjeleníti. Az egyik egy előre script, a másik pedig egy post-script:

![Kijelölt elemek](./media/pre-post-scripts/selected-items.png)

A frissítés telepítésének beállítása befejezése.

Amikor a frissítés központi telepítése befejeződött, az eredmények megtekintéséhez nyissa meg a **központi telepítések frissítése.** Mint látható, az állapot meg van adva az előre-script és post-script:

![Eredmények frissítése](./media/pre-post-scripts/update-results.png)

A frissítés központi telepítésének kiválasztásával további részleteket jeleníthet meg az elő- és parancsfájlok előre- és utóparancsfájljairól. A parancsfájl forrására mutató hivatkozás a futtatás időpontjában biztosított.

![A telepítés futtatási eredményei](./media/pre-post-scripts/deployment-run.png)

a forgatókönyvedben.

## <a name="stopping-a-deployment"></a>Telepítés leállítása

Ha egy előre parancsfájlon alapuló központi telepítést szeretne leállítani, kivételt kell [tennie.](automation-runbook-execution.md#throw) Ha nem, a központi telepítés és a parancsfájl utáni továbbra is futni fog. A következő kódrészlet bemutatja, hogyan kell kivételt tenni.

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



## <a name="interacting-with-machines"></a>Kommunikáció a gépekkel

Az elő-parancsfájlok és a post-feladatok runbookként futnak az Automation-fiókban, és nem közvetlenül a központi telepítésben lévő gépeken. Az előfeladatok és az utófeladatok az Azure-környezetben is futnak, és nem férnek hozzá a nem Azure-beli gépekhez. A következő szakaszok bemutatják, hogyan kommunikálhat a gépekkel közvetlenül, függetlenül attól, hogy Azure-beli virtuális gépekről vagy nem Azure-alapú gépekről van-e szó.

### <a name="interact-with-azure-machines"></a>Együttműködés az Azure-gépekkel

Az előfeladatok és az utófeladatok runbookként futnak, és nem futnak natív módon az Azure-beli virtuális gépeken a központi telepítésben. Az Azure-beli virtuális gépek hez a következő elemekkel kell rendelkeznie:

* Futtatás másként fiók
* A futtatni kívánt runbook

Azure-gépekkel való együttműködéshez használja az [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) parancsmag az Azure-beli virtuális gépek. Ennek például a Runbook példa Update [Management – run script run paranccsal című](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)témakörben található.

### <a name="interact-with-non-azure-machines"></a>Nem Azure-alapú gépekkel való együttműködés

Az előfeladatok és az utófeladatok az Azure-környezetben futnak, és nem férnek hozzá a nem Azure-beli gépekhez. A nem Azure-alapú gépekkel való interakcióhoz a következő elemekkel kell rendelkeznie:

* Futtatás másként fiók
* A hibrid Runbook-feldolgozó telepítve van a számítógépen
* A helyileg futtatni kívánt runbook
* Szülő runbook

A nem Azure-alapú gépekkel való interakcióhoz a szülő runbook az Azure-környezetben fut. Ez a runbook meghívja a gyermek runbook a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) parancsmag. Meg kell `RunOn` adnia a paramétert, és meg kell adnia a hibrid Runbook-feldolgozó nevét a parancsfájl futtatásához. Tekintse meg a runbook példa [Update Management – parancsfájl futtatása helyileg.](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)

## <a name="aborting-patch-deployment"></a>A javítás telepítésének megszakítása

Ha az elő-parancsfájl hibát ad vissza, érdemes lehet megszakítani a központi telepítést. Ehhez hiba [kell,](/powershell/module/microsoft.powershell.core/about/about_throw) hogy a parancsfájlban hiba legyen minden olyan logika esetén, amely hibát jelentene.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Példák

Az elő- és post-scriptek mintái megtalálhatók a [Parancsfájl-tárban](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) és a [PowerShell-galériában,](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)vagy importálhatja őket az Azure Portalon keresztül. Ehhez az Automation-fiók **folyamatautomatizálási**területén válassza a **Runbooks Gallery lehetőséget.** Használja **a frissítéskezelés** a szűrőt.

![Galéria lista](./media/pre-post-scripts/runbook-gallery.png)

Vagy kereshet rájuk a parancsfájl neve alapján, ahogy az az alábbi listában látható:

* Frissítéskezelés – A virtuális gépek bekapcsolása
* Frissítéskezelés - A virtuális gépek kikapcsolása
* Frissítéskezelés – Parancsfájl helyi futtatása
* Frissítés kezelése - Sablon elő- és postaparancsfájlokhoz
* Frissítéskezelés – Parancsfájl futtatása parancsfuttatásával

> [!IMPORTANT]
> A runbookok importálása után közzé kell tennie őket, mielőtt használni lehetne őket. Ehhez keresse meg a runbookot az Automation-fiókban, válassza **a Szerkesztés**lehetőséget, majd a **Közzététel**lehetőséget.

A minták mind a következő példában definiált alapsablonon alapulnak. Ez a sablon segítségével létrehozhatja saját runbook ot előre parancsfájlokkal és utóparancsfájlokkal. Az Azure-ral való hitelesítéshez és `SoftwareUpdateConfigurationRunContext` a paraméter kezeléséhez szükséges logika is szerepel.

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
> Nem grafikus PowerShell-runbookok esetén, `Add-AzAccount` és `Add-AzureRMAccount` a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)aliasai. Használhatja ezeket a parancsmagokat, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha nemrég létrehozott egy új Automation-fiókot.

## <a name="next-steps"></a>További lépések

A Windows virtuális gépek frissítéseinek kezeléséről az alábbi oktatóanyagban olvashat:

> [!div class="nextstepaction"]
> [Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításainak kezelése](automation-tutorial-update-management.md)