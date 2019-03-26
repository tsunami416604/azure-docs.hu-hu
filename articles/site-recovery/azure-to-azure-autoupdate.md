---
title: Az Azure-bA vész-helyreállítási a mobilitási szolgáltatás automatikus frissítése |} A Microsoft Docs
description: Ha az Azure virtuális gépek replikálása az Azure Site Recovery mobilitási szolgáltatás automatikus frissítése áttekintése.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6e1a9b2fd34d915716225c6a1bda6e0371a510a9
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438835"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Az Azure – Azure replikációs a mobilitási szolgáltatás automatikus frissítése

Az Azure Site Recovery egy havi tempót használatával javítsa ki a hibákat, és javíthatja a meglévő funkciók, vagy újakat vehet fel. Maradjon naprakész a szolgáltatás, meg kell terveznie javítás központi telepítés minden hónapban. Minden egyes frissítés kapcsolódó terhelés elkerülése érdekében a Site Recovery összetevőinek frissítései kezeléséhez inkább engedélyezheti.

Az említett [Azure – Azure vészhelyreállítási recovery architektúrájáról](azure-to-azure-architecture.md), a mobilitási szolgáltatás telepítve van az összes Azure virtuális gépeken (VM), amelynek replikálás engedélyezve van, miközben a virtuális gépek replikálása egyik Azure-régióból a másikba. Ha az automatikus frissítéseket használja, minden egyes új kiadással frissíti a mobilitási szolgáltatás bővítmény.
 
## <a name="how-automatic-updates-work"></a>Automatikus frissítések munka

A Site Recovery segítségével kezelheti a frissítéseket, amikor automation-fiók, és a tárolónak ugyanabban az előfizetésben létrehozott keresztül globális (az Azure-szolgáltatások által használt) runbook helyez üzembe. Az egyes tárolókban egy automation-fiókot használ. A runbook egy tárolót az active automatikusan frissül az egyes virtuális gépekre keres, és frissíti a mobilitási szolgáltatás bővítmény, ha egy újabb verzió érhető el.

Az alapértelmezett runbook-ütemezés szerint naponta 12:00 órakor, a replikált virtuális gép földrajzi időzónájában továbbra is megjelenik. A runbook-ütemezés az automation-fiók használatával is módosíthatja.

> [!NOTE]
> Automatikus frissítés bekapcsolása nem kell indítani az Azure-beli virtuális gépek vagy hatással vannak a folyamatban lévő replikáció.

> [!NOTE]
> Az automation-fiókban számlázási feladat az adott hónapban felhasznált feladat modul percben alapul. Alapértelmezés szerint 500 perc is tartozó automation-fiók ingyenes egységek. Feladat végrehajtása néhány másodperc alatt körülbelül egy percig minden nap, és az ingyenes egységek képezi.

| (Havonta) a szolgáltatásban foglalt ingyenes egységek | Ár |
|---|---|
| Feladat futási ideje 500 perc | ₹0.14 / perc

## <a name="enable-automatic-updates"></a>Automatikus frissítések engedélyezése

Engedélyezheti, hogy a Site Recovery a következő módokon kezelheti a frissítéseket.

### <a name="manage-as-part-of-the-enable-replication-step"></a>A replikáció engedélyezése lépés részeként kezelése

Amikor engedélyezi egy virtuális gép replikációját vagy indítása [a Virtuálisgép-nézetből](azure-to-azure-quickstart.md) vagy [a recovery services-tárolót a](azure-to-azure-how-to-enable-replication.md), vagy engedélyezheti a Site Recovery-bővítmény frissítéseinek kezeléséhez, vagy kezelheti, a Site Recovery manuálisan.

![Bővítmény beállításai](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Váltás a bővítményt a táron belüli beállításainak frissítése

1. Lépjen a táron belüli **kezelés** > **Site Recovery-infrastruktúra**.
2. A **az Azure Virtual Machines** > **bővítmény frissítési beállítások**, kapcsolja be a **kezelése lehetővé teszi a Site Recovery** be-vagy kikapcsolása. Manuálisan kezeléséhez, kapcsolja ki. 
3. Kattintson a **Mentés** gombra.

![Bővítmény frissítési beállításai](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> Ha úgy dönt **kezelése lehetővé teszi a Site Recovery**, a beállítást alkalmazza a megfelelő tárolóban lévő összes virtuális gépet.


> [!Note]
> Bármelyik beállítást is választja értesíti a frissítések felügyeletére szolgáló automation-fiók. Ha ez a funkció egy tárolót az első alkalommal használ, egy új automation-fiók jön létre. Minden ezt követő engedélyezése replikációk az ugyanahhoz a tárolóhoz használja a korábban létrehozott.

Egy egyéni automation-fiókot használja a következő parancsfájlt:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Frissítések manuális kezelése

1. Ha a mobilitási szolgáltatás a virtuális gépeken telepített új frissítések érhetők el, akkor a következő értesítés jelenik meg: "Új Site recovery replikációs ügynökének frissítése érhető el. Történő telepítéséhez kattintson a"

     ![Replikált elemek ablak](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Válassza ki az értesítés a virtuális gép kiválasztására szolgáló lap megnyitásához.
3. Válassza ki a virtuális gépeket szeretne frissíteni, és válassza ki **OK**. A frissítés a mobilitási szolgáltatás minden egyes kiválasztott virtuális gép elindul.

     ![Replikált elemek Virtuálisgép-lista](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Gyakori problémák és hibaelhárítás

Ha az automatikus frissítések problémáját, látni fogja a alatt egy Hibaértesítések **konfigurációs problémák** a tároló irányítópultján.

Ha nem engedélyezi az automatikus frissítések, tekintse meg a következő gyakori hibák és a javasolt művelet:

- **Hiba**: Nem jogosult Azure-beli futtató fiók (szolgáltatásnév) létrhozására és a Közreműködő szerepkör szolgáltatásnévhez való hozzárendelésére.

   **Javasolt művelet**: Győződjön meg arról, hogy a bejelentkezett fiók hozzá van rendelve közreműködő, és próbálkozzon újra. Tekintse meg a szükséges engedélyek szakaszában [a portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) engedélyek hozzárendelése a további információt.
 
   A legtöbb problémák megoldásához, miután engedélyezte az automatikus frissítések, válassza ki a **javítási**. A javítás gomb nem érhető el, ha a bővítmény frissítési beállítások ablaktáblában jelenik meg hibaüzenet jelenik meg.

   ![Site Recovery szolgáltatás javítása gomb a bővítmény frissítési beállítások](./media/azure-to-azure-autoupdate/repair.png)

- **Hiba**: A futtató fióknak nincs engedélye a recovery services-erőforrás eléréséhez.

    **Javasolt művelet**: Törölje, majd [hozza létre újból a futtató fiók](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Vagy, győződjön meg arról, hogy az Automation futtató fiókot az Azure Active Directory-alkalmazás hozzáfér a recovery services-erőforrás.

- **Hiba**: Futtató fiók nem található. Vagy ezek egyikét törölték, vagy nem lett létrehozva: Azure Active Directory-alkalmazás, szolgáltatásnév, szerepkör, Automation-tanúsítvány, Automation-kapcsolatobjektum - vagy az ujjlenyomat nem azonos a tanúsítvány és a kapcsolat. 

    **Javasolt művelet**: Törölje, majd [hozza létre újból a futtató fiók](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
