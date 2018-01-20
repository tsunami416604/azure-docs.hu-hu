---
title: "Azure Automation-fiók konfigurációjának ellenőrzése | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan lehet ellenőrizni, hogy az Automation-fiók konfigurációja megfelelő-e."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 1413b5d9625ebc2e3b2419f50e7e78be994d8d68
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Azure Automation futtató fiók hitelesítésének tesztelése
Az Automation-fiók sikeres létrehozását követően végrehajthat egy egyszerű tesztet annak megállapítására, hogy az újonnan létrehozott vagy frissített Automation futtató fiók alkalmas-e a sikeres hitelesítésre az Azure Resource Managerben vagy a klasszikus Azure üzembe helyezési modellben.    

## <a name="automation-run-as-authentication"></a>Hitelesítés Automation futtató fiókkal
Az alábbi mintakód segítségével egy [PowerShell-runbook létrehozásával](automation-creating-importing-runbook.md) ellenőrizheti a futtató fiókkal történő hitelesítést, valamint az egyéni runbookokban hitelesítheti és kezelheti a Resource Manager-erőforrásokat az Automation-fiókkal.   

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Add-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Figyelje meg, hogy a forgatókönyvben való hitelesítést biztosító parancsmag, (**Add-AzureRmAccount**) a *ServicePrincipalCertificate* paraméterkészletet használja.  Ez a szolgáltatásnév segítségével, és nem hitelesítő adatokkal végzi el a hitelesítést.  

Ha Ön [a runbook futtatásához](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) a Futtatás mint fiók érvényesítéséhez egy [runbook-feladat](automation-runbook-execution.md) jön létre, a feladat lap jelenik meg, és a feladat állapota megjelenik a **feladat összegzése** csempére. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  Ha befejeződik a forgatókönyv-feladat, normál esetben a **Befejezve** állapotnak kell megjelennie.

Ha szeretné megtekinteni a forgatókönyv részletes eredményeit, kattintson a **Kimenet** csempére.  A **Kimenet** oldalon azt kell látnia, hogy a runbook sikeresen elvégezte a hitelesítést, és visszaadta az előfizetésben elérhető összes erőforráscsoport listáját.  

Ne felejtse el eltávolítani a `#Get all ARM resources from all resource groups` megjegyzéssel kezdődő kódblokkot, amikor újból felhasználja a kódot a runbookokban.

## <a name="classic-run-as-authentication"></a>Hitelesítés klasszikus futtató fiókkal
Az alábbi mintakód segítségével egy [PowerShell-runbook létrehozásával](automation-creating-importing-runbook.md) ellenőrizheti a klasszikus futtató fiókkal történő hitelesítést, valamint az egyéni runbookokban hitelesítheti és kezelheti az erőforrásokat a klasszikus üzemi modellben.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

Amikor [futtatja a runbookot](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) a futtató fiók érvényesítéséhez, létrejön egy [runbook-feladat](automation-runbook-execution.md), megjelenik a Feladat oldal, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  Ha befejeződik a forgatókönyv-feladat, normál esetben a **Befejezve** állapotnak kell megjelennie.

Ha szeretné megtekinteni a forgatókönyv részletes eredményeit, kattintson a **Kimenet** csempére.  A **Kimenet** oldalon azt kell látnia, hogy a runbook sikeresen elvégezte a hitelesítést, és visszaadta az előfizetésben telepített Azure-beli virtuális gépek a virtuális gépek neve szerinti listáját.  

Ne felejtse el eltávolítani a **Get-AzureVM** parancsmagot, amikor újból felhasználja a kódot a runbookban.

## <a name="next-steps"></a>További lépések
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* További információk a grafikus létrehozásról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).
