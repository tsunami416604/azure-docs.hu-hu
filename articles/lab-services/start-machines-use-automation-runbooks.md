---
title: Indítsa el a gépeket Automation-runbookok használatával az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan indítsa el a virtuális gépeket az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet Azure Automation-runbookok használatával.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: d80328943ae818b3bad9c0a275b74968ee33d4b7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789057"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Indítsa el a virtuális gépeket egy tesztkörnyezetben ahhoz az Azure Automation-runbookok használatával
A [autostart](devtest-lab-set-lab-policy.md#set-autostart) , DevTest Labs szolgáltatás lehetővé teszi virtuális gépek, egy megadott időpontban automatikus indításra konfigurálja. Ez a funkció azonban nem támogatja a gépek meghatározott sorrendben elindításához. Nincsenek számos forgatókönyv, ahol az automation ilyen típusú hasznos lenne.  Egy forgatókönyv, ahol a Jumpbox virtuális gép kulcsok tesztkörnyezetben kell elindítani, először a többi virtuális géphez, mielőtt, a Jumpboxot a többi virtuális géphez a hozzáférési pont szolgál.  Ez a cikk bemutatja, hogyan állíthat be egy PowerShell-forgatókönyvet, amely végrehajtja a parancsfájl az Azure Automation-fiókot. A szkript címkéket a virtuális gépeken a laborban lehetővé teszi, hogy az indítási sorrend módosítása a parancsfájl nélkül.

## <a name="setup"></a>Beállítás
Ebben a példában a labor virtuális gépeinek kell rendelkeznie a címke **StartupOrder** hozzáadva a megfelelő értékkel (0,1,2, stb.). Minden olyan gép, amely nem indítható el, mint -1 kell kijelölni.

## <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása
Hozzon létre egy Azure Automation-fiók található utasításokat követve [Ez a cikk](../automation/automation-create-standalone-account.md). Válassza ki a **futtató fiókok** lehetőséget egy fiók létrehozásakor. Az automation-fiók létrehozása után nyissa meg a **modulok** lapon, és válassza ki **frissítés az Azure-modulok** a menüsávon. Az alapértelmezett modulokra több verziója korábbi, és a frissítés a parancsfájl nélkül nem működnek.

## <a name="add-a-runbook"></a>Forgatókönyv hozzáadása
Most egy runbook az automation-fiók hozzáadásához kattintson **Runbookok** a bal oldali menüben. Válassza ki **forgatókönyv hozzáadása** a menüben, majd az alábbi utasítások [PowerShell-runbook létrehozásával](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>PowerShell-szkript
A következő szkriptet az előfizetés neve, a labor nevét paraméterekként vesz igénybe. A folyamat a parancsfájl, hogy a virtuális gépek beolvasása a tesztkörnyezetben, és majd elemezheti az eszközcímke-információ a virtuális gép nevét és az indítási sorrend listájának létrehozásához. A szkript a virtuális gépek sorrendben végigvezeti, és elindítja a virtuális gépek. Ha egy adott sorszám több virtuális gép is van, indulásuk aszinkron módon történik a PowerShell-feladatok használata. A virtuális gépeken, amelyek nem rendelkeznek egy címke, a készlet indítási értéket, kell lennie az utolsó (10), akkor indul utolsó, alapértelmezés szerint.  Ha a labor nem szeretné, hogy a virtuális gép hajtható végre, állítsa be a címke értéke, 11, és figyelmen kívül.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzureRmResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzureRmContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzureRmContext -Path ($profilePath)
    Invoke-AzureRmResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzureRmResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Ütemezés létrehozása
Ez a szkript végrehajtása naponta, hogy [ütemezés létrehozása](../automation/shared-resources/schedules.md#creating-a-schedule) az automation-fiókban. Az ütemezés létrehozása után [hozzákapcsolhatja a runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

Olyan nagy méretű helyzetekben, ahol több tanulhat több előfizetést, a paraméteradatoknak tárolása egy fájlban, a másik labs, és adja át a fájlt a parancsfájl helyett az egyes paramétereket. A parancsfájl kell módosítani, de a fő végrehajtása változatlan marad. Ebben a példában az Azure Automation PowerShell-parancsprogram végrehajtása, míg más lehetőség van például egy feladat használatával a Build és kiadás folyamat.

## <a name="next-steps"></a>További lépések
További információ az Azure Automation a következő cikkben talál: [Azure Automation bemutatása](../automation/automation-intro.md).
