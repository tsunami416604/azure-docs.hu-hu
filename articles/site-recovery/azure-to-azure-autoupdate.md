---
title: A mobilitási szolgáltatás automatikus frissítése az Azure-bA vész-helyreállítási |} A Microsoft Docs
description: A mobilitási szolgáltatás automatikus frissítése áttekintést nyújt, ha az Azure Site Recovery használatával az Azure virtuális gépek replikálása.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 3f0f28ca22321b537ab7e8911c5cbb513a1ade81
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818923"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Az Azure-bA replikálása a mobilitási szolgáltatás automatikus frissítése

Az Azure Site Recovery egy havi tempót, ahol hozzá meglévő funkciók fejlesztéseivel, vagy újakat ad, és az esetleges ismert probléma elhárítása rendelkezik. Ez azt jelentené, hogy továbbra is a szolgáltatás naprakész, meg kell terveznie a javítások havi központi telepítésére vonatkozóan. Annak érdekében, hogy a frissítés társított over vezetője, felhasználók ehelyett eldönthetik, hogy a Site Recovery összetevőinek frissítéseinek kezelésére. A részletes a [architektúra referencia](azure-to-azure-architecture.md) vészhelyreállítás Azure-ba, a mobilitási szolgáltatást telepíti, amelynek a replikáció engedélyezve van egy Azure virtuális gépek replikálása során minden Azure virtuális gépeken -régióból a másikba. Miután engedélyezte az automatikus frissítés, a mobilitási szolgáltatás bővítmény frissül, és minden egyes új kiadással. Ez a dokumentum részletesen a következőket:

- Hogyan működik az automatikus frissítési?
- Automatikus frissítések engedélyezése
- Gyakori problémák és hibaelhárítás
 
## <a name="how-does-automatic-update-work"></a>Hogyan működik az automatikus frissítési

Ha engedélyezi a Site Recovery kezelheti a frissítéseket, globális runbookot (ami Azure-szolgáltatások által használt) egy automation-fiókot, és a tárolónak ugyanabban az előfizetésben létrehozott keresztül van telepítve. Egy adott tároló egy automation-fiók szolgál. A runbook minden virtuális gép automatikusan frissül, amelyhez be vannak kapcsolva tárolóban keres, és kezdeményezi a mobilitási szolgáltatás bővítmény frissítését, ha egy újabb verzió érhető el. A forgatókönyv az alapértelmezett ütemezés szerint naponta 12:00 órakor a replikált virtuális gép földrajzi időzónájának megfelelően továbbra is megjelenik. A runbook-ütemezés is módosítható az automation-fiók segítségével a felhasználó által szükség esetén. 

> [!NOTE]
> Az automatikus frissítések engedélyezése az Azure-beli virtuális gépek újraindítása nem szükséges, és nem befolyásolja a folyamatban lévő replikációkat.

> [!NOTE]
> Automation-fiókot használja a feladatok díjszabása a feladat futási ideje a hónap és alapértelmezés szerint 500 perc alatt felhasznált percek is tartozó automation-fiók ingyenes egységek számát alapul. A feladat napi összegeket a végrehajtása egy **néhány másodperc alatt körülbelül egy percig** és lesz **szereplő az ingyenes krediteket**.

Az ingyenes egységek (HAVONTA) foglalt ** ár feladat futtatása idő 500 perc ₹0.14 / perc

## <a name="enable-automatic-updates"></a>Automatikus frissítések engedélyezése

Dönthet úgy, hogy engedélyezze a következő módokon kezelheti a frissítéseket a Site Recovery:-

- [A replikáció engedélyezése lépés részeként](#as-part-of-the-enable-replication-step)
- [Váltás a bővítményt a táron belüli beállításainak frissítése](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>A replikáció engedélyezése lépés részeként:

Ha engedélyezi a replikációt a egy virtuális gép elindítása vagy [a virtuálisgép-nézetből](azure-to-azure-quickstart.md), vagy [a recovery services-tárolót a](azure-to-azure-how-to-enable-replication.md), kap, vagy engedélyezheti a Site Recovery az lehetőség a Site Recovery-bővítmény vagy kézi kezelését azonos frissítéseinek kezelése.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Váltás a bővítményt a táron belüli beállításainak frissítése

1. Lépjen a táron belüli **kezelés**-> **Site Recovery-infrastruktúra**
2. Alatt **Azure-beli virtuális gépek**-> **bővítmény frissítési beállítások**, kattintással válassza ki, hogy szeretné-e engedélyezése *kezelheti a frissítéseket az ASR* vagy *manuális kezelése*. Kattintson a **Save** (Mentés) gombra.

![vault-toggle-auto-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Ha úgy dönt *lehetővé teszik az ASR kezeléséhez*, a beállítást alkalmazza a megfelelő tárolóban lévő összes virtuális gépet.


> [!Note] 
> A két lehetőség értesíteni fogjuk, az automation-fiók, amely a frissítések kezelésére szolgál. Ha egy tárolót az első alkalommal engedélyezi ezt a szolgáltatást, létrejön egy új automation-fiókot. Minden ezt követő engedélyezése replikációk ugyanahhoz a tárolóhoz a korábban létrehozott fogja használni.

**Ha egy egyéni automation-fiókot használni szeretne, használja az alábbi szkriptet:-**

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
                Write-InformationTracing ("Ignoring container mapping: {0} as the the state is not paired." -f ($Mapping.Id))
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

            # Rate controlling the get calls to maximum 120 calls per minute.
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

### <a name="manage-manually"></a>Manuális kezelés

1. Ha vannak a mobilitási szolgáltatás az Azure virtuális gépeken telepített új frissítéseket, megjelenik egy értesítés, olvasó, "új Site recovery replikációs ügynökének frissítése érhető el. Telepítéséhez kattintson ide."

     ![Replikált elemek ablak](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. Válassza ki az értesítés a virtuális gép kiválasztására szolgáló lap megnyitásához.
4. Válassza ki a futó mobilitási szolgáltatás frissítése, és válassza ki a kívánt virtuális gépeket **OK**.

     ![Replikált elemek Virtuálisgép-lista](./media/vmware-azure-install-mobility-service/update-okpng.png)

A mobilitási szolgáltatás frissítése feladat elindul, a kiválasztott virtuális gépek mindegyikéhez.


## <a name="common-issues--troubleshooting"></a>Gyakori problémák és hibaelhárítás

Ha az automatikus frissítések problémáját, értesítést is ugyanannak a tároló irányítópultján konfigurációs problémákat. 

Abban az esetben próbálta meg engedélyezni az automatikus frissítések, és nem sikerült, tekintse meg alább a hibaelhárításhoz.

**Hiba**: Nem jogosult Azure-beli futtató fiók (szolgáltatásnév) létrhozására és a Közreműködő szerepkör szolgáltatásnévhez való hozzárendelésére. 
- Javasolt művelet: Győződjön meg arról, hogy a bejelentkezett fiók hozzá van rendelve a "közreműködői", és próbálja megismételni a műveletet. Tekintse meg [ez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) további információt a megfelelő engedélyek hozzárendelése a dokumentumot.
 
Után az automatikus frissítések is be van kapcsolva, a problémák a legtöbb is lehet kezelte a Site Recovery szolgáltatás, és megköveteli, hogy az a "**javítási**" gombra.

![javítás-gomb](./media/azure-to-azure-autoupdate/repair.png)

Amennyiben a javítás gomb nem érhető el, tekintse meg a bővítmény beállítások panel alatt jelenik meg hibaüzenet.

 - **Hiba**: A futtató fióknak nincs engedélye a recovery services-erőforrás eléréséhez.

    **Javasolt művelet**: Törölje, majd [hozza létre újból a futtató fiók](https://docs.microsoft.com/azure/automation/automation-create-runas-account) vagy győződjön meg róla, hogy az Automation futtató fiókot az Azure Active Directory-alkalmazás hozzáfér a recovery services-erőforrás.

- **Hiba**: Futtató fiók nem található. Vagy ezek egyikét törölték, vagy nem lett létrehozva: Azure Active Directory-alkalmazás, szolgáltatásnév, szerepkör, Automation-tanúsítvány, Automation-kapcsolatobjektum - vagy az ujjlenyomat nem azonos a tanúsítvány és a kapcsolat. 

    **Javasolt művelet**: Törlése és [hozza létre újra a futtató fiókot](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
