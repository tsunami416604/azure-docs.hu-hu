---
title: Gépek indítása automation-runbookok használatával az Azure DevTest Labsben
description: Ismerje meg, hogyan indíthat virtuális gépeket egy laborban az Azure DevTest Labs az Azure Automation runbookok használatával.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166307"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Virtuális gépek indítása laborban az Azure Automation runbookok használatával
A DevTest Labs [automatikus indítási](devtest-lab-set-lab-policy.md#set-autostart) funkciója lehetővé teszi a virtuális gépek automatikus indítását egy adott időpontban. Ez a funkció azonban nem támogatja a gépek indításához egy adott sorrendben. Számos olyan forgatókönyv létezik, ahol az ilyen típusú automatizálás hasznos lehet.  Az egyik forgatókönyv az, ahol egy jumpbox virtuális gép egy tesztkörnyezetben kell kezdeni először, mielőtt a többi virtuális gépek, a Jumpbox a többi virtuális gépek hozzáférési pontként használják.  Ez a cikk bemutatja, hogyan állíthat be egy Azure Automation-fiókot egy PowerShell-runbook, amely végrehajtja a parancsfájlt. A parancsfájl címkéket használ a tesztkörnyezetben lévő virtuális gépeken, hogy lehetővé tegye az indítási sorrend szabályozását a parancsfájl módosítása nélkül.

## <a name="setup"></a>Telepítés
Ebben a példában a tesztkörnyezetben lévő virtuális gépeknek hozzá kell adniuk a **startuporder-címkét** a megfelelő értékkel (0,1,2 stb.). Jelöljön ki minden olyan gépet, amelyet nem kell -1-nek indítani.

## <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása
Hozzon létre egy Azure Automation-fiókot a [cikkben](../automation/automation-create-standalone-account.md)található utasításokat követve. A fiók létrehozásakor válassza a **Futtatás fiókként** lehetőséget. Az automatizálási fiók létrehozása után nyissa meg a **Modulok** lapot, és válassza az **Azure-modulok frissítése** lehetőséget a menüsorban. Az alapértelmezett modulok több régi verziók, és a frissítés nélkül a szkript nem fog működni.

## <a name="add-a-runbook"></a>Runbook hozzáadása
Most, hogy adjunk egy runbookot az automatizálási fiókhoz, válassza a **Runbooks** a bal oldali menüben. Válassza a **Runbook hozzáadása** a menüben, és kövesse az utasításokat [a PowerShell-runbook létrehozásához.](../automation/automation-first-runbook-textual-powershell.md)

## <a name="powershell-script"></a>PowerShell-szkript
A következő parancsfájl az előfizetés nevét, a labor nevét paraméterekként veszi fel. A parancsfájl folyamata az, hogy az összes virtuális gépet a laborban, majd elemezze ki a címke adatait a virtuális gép nevek és az indítási sorrend létrehozásához. A parancsfájl végigvezeti a virtuális gépek sorrendben, és elindítja a virtuális gépeket. Ha egy adott rendelési számban több virtuális gép van, a PowerShell-feladatok használatával aszinkron módon indulnak el. Azok a virtuális gépek, amelyek nem rendelkeznek címkével, állítsa be az indítási értéket, hogy az utolsó (10), alapértelmezés szerint ők indulnak el utoljára.  Ha a tesztkörnyezet nem szeretné, hogy a virtuális gép automatikusan elinduljon, állítsa a címke értékét 11-re, és figyelmen kívül hagyja.

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
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

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
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
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
        $returnStatus = Invoke-AzResourceAction `
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
A parancsfájl napi végrehajtásához [hozzon létre egy ütemezést](../automation/shared-resources/schedules.md#creating-a-schedule) az automatizálási fiókban. Az ütemezés létrehozása után [csatolja a runbookhoz.](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook) 

Egy nagyszabású helyzetben, ahol több előfizetéstöbb labs, tárolja a paraméter adatait egy fájlban a különböző labs, és adja át a fájlt a parancsfájlhelyett az egyes paramétereket. A parancsfájlt módosítani kell, de az alapvető végrehajtás ugyanaz lenne. Bár ez a minta az Azure Automation használatával hajtja végre a PowerShell-parancsfájlt, vannak más lehetőségek, például egy feladat használata egy build/kiadás folyamatában.

## <a name="next-steps"></a>További lépések
Az Azure Automationről az alábbi cikkben olvashat [bővebben: Bevezetés az Azure Automationbe.](../automation/automation-intro.md)
