---
title: A mobilitási szolgáltatás automatikus frissítése az Azure Site Recovery szolgáltatásban
description: A Mobility szolgáltatás automatikus frissítésének áttekintése az Azure-beli virtuális gépek replikálásakor az Azure Site Recovery használatával.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/24/2019
ms.author: rajanaki
ms.openlocfilehash: 3a9b0717368fa67f5a7dd477e018a68e048b6740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451408"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>A Mobilitási szolgáltatás automatikus frissítése az Azure-to Azure replikációban

Az Azure Site Recovery havi kiadási ütemben oldja meg a problémákat, és javítja a meglévő funkciókat, vagy újakat. Ahhoz, hogy naprakész maradjon a szolgáltatással, havonta meg kell terveznie a javítások telepítését. Az egyes frissítésekkel kapcsolatos többletterhelés elkerülése érdekében engedélyezheti, hogy a Site Recovery kezelje az összetevő-frissítéseket.

Ahogy az [Azure-ból Azure-ba vész-helyreállítási architektúrában említésre](azure-to-azure-architecture.md)kerül, a Mobilitás szolgáltatás minden olyan Azure-virtuális gépre (VM-re) telepítve van, amelyhez engedélyezve van a replikáció, miközben a virtuális gépeket az egyik Azure-régióból a másikba replikálja. Az automatikus frissítések használatakor minden új kiadás frissíti a Mobilitás szolgáltatás bővítményt.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Az automatikus frissítések működésének megmunkálata

Ha a Site Recovery használatával kezeli a frissítéseket, egy globális runbookot telepít (amelyet az Azure-szolgáltatások használnak) egy automatizálási fiókon keresztül telepíti, amely ugyanabban az előfizetésben jön létre, mint a tároló. Minden tároló egy automatizálási fiókot használ. A runbook ellenőrzi az egyes virtuális gépek egy tárolóban az aktív automatikus frissítések et, és frissíti a Mobility szolgáltatás bővítmény, ha egy újabb verzió érhető el.

Az alapértelmezett runbook-ütemezés naponta 12:00-kor ismétlődik a replikált virtuális gép földrajzi helyének időzónájában. A runbook ütemezését az automation-fiókon keresztül is módosíthatja.

> [!NOTE]
> A 35.összegző frissítőcsomaggal kezdve kiválaszthatja a frissítésekhez használt meglévő automatizálási fiókot. A frissítés előtt a Site Recovery alapértelmezés szerint létrehozta ezt a fiókot. Ne feledje, hogy csak akkor választhatja ezt a beállítást, ha engedélyezi a virtuális gép replikációját. Replikáló virtuális gép nem érhető el. A kiválasztott beállítás az ugyanabban a tárolóban védett összes Azure-beli virtuális gépre vonatkozik.
 
> Az automatikus frissítések bekapcsolása nem igényli az Azure virtuális gépek újraindítását, és nem befolyásolja a folyamatos replikációt.

> Az automatizálási fiókban a feladat számlázása az egy hónapban használt feladatfutási percek számán alapul. Alapértelmezés szerint 500 perc egy automatizálási fiók ingyenes egységeként szerepel. A feladat végrehajtása naponta néhány másodpercet vesz igénybe, és ingyenes egységként van lefedve.

| Ingyenes egységek benne (minden hónapban) | Price |
|---|---|
| Feladat futásideje 500 perc | 0,14/perc

## <a name="enable-automatic-updates"></a>Automatikus frissítések engedélyezése

A Site Recovery a következő módokon kezelheti a frissítéseket.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Kezelés a replikáció engedélyezése lépés részeként

Ha engedélyezi a virtuális gép replikációját [a virtuális gép nézetéből](azure-to-azure-quickstart.md) vagy a helyreállítási szolgáltatások [tárolójából,](azure-to-azure-how-to-enable-replication.md)engedélyezheti, hogy a Site Recovery kezelje a Site Recovery bővítmény frissítéseit, vagy manuálisan kezelje azt.

![Bővítmény beállításai](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>A bővítmény frissítési beállításainak be- és be- és bekapcsolása a tárolón belül

1. A tárolón belül nyissa meg a**Site Recovery Infrastructure** **kezelése** > című területet.
2. Az **Azure virtuális gépek** > **bővítményfrissítési beállításai csoportban**kapcsolja be a **Site Recovery kezelése** a kapcsolót. A manuális kezeléshez kapcsolja ki. 
3. Kattintson a **Mentés** gombra.

![Bővítményfrissítési beállítások](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> Ha a Site Recovery kezelésének engedélyezése lehetőséget **választja,** a beállítás a megfelelő tároló összes virtuális gépére vonatkozik.


> [!Note]
> Mindkét lehetőség értesíti Önt a frissítések kezeléséhez használt automatizálási fiókról. Ha ezt a funkciót először használja egy tárolóban, alapértelmezés szerint egy új automatizálási fiók jön létre. Másik lehetőségként testreszabhatja a beállítást, és kiválaszthat egy meglévő automatizálási fiókot. Minden ezt követő engedélyezésreplikák ugyanabban a tárolóban használja a korábban létrehozott egy. Jelenleg a legördülő csak azokat az Automation-fiókokat sorolja fel, amelyek ugyanabban az erőforráscsoportban vannak, mint a tároló.  

> [!IMPORTANT]
> Az alábbi parancsfájlt egy automatizálási fiók környezetében kell futtatni egyéni automatizálási fiók esetén használja a következő parancsfájlt:

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

1. Ha a mobilrendszer-kezelő szolgáltatáshoz új frissítések vannak telepítve, a következő értesítés jelenik meg: "Új hely-helyreállítási replikációs ügynök frissítés érhető el. Kattintson a telepítéshez"

     ![Replikált cikkek ablaka](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Válassza ki az értesítést a virtuális gép választólapjának megnyitásához.
3. Válassza ki a frissíteni kívánt virtuális gépeket, majd kattintson az **OK gombra.** A Mobility frissítése szolgáltatás minden egyes kiválasztott virtuális géphez elindul.

     ![Replikált elemek virtuális gépének listája](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Gyakori problémák és hibaelhárítás

Ha probléma merül fel az automatikus frissítésekkel kapcsolatban, a tároló irányítópultján a **Konfigurációs problémák** területen hibaüzenet jelenik meg.

Ha nem tudta engedélyezni az automatikus frissítéseket, olvassa el az alábbi gyakori hibákat és ajánlott műveleteket:

- **Hiba:** Nincs engedélye az Azure Run As fiók (egyszerű szolgáltatás) létrehozásához, és adja meg a közreműködői szerepkört az egyszerű szolgáltatásnak.

   **Ajánlott művelet:** Győződjön meg arról, hogy a bejelentkezett fiók közreműködőként van hozzárendelve, majd próbálkozzon újra. Tekintse meg a szükséges engedélyeket szakaszban [a portál használata azure AD-alkalmazás és egyszerű szolgáltatás, amely képes hozzáférni az erőforrásokhoz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) további információkért engedélyek hozzárendelése.
 
   Ha az automatikus frissítések engedélyezése után a legtöbb problémát meg szeretné oldani, válassza a **Javítás lehetőséget.** Ha a javításgomb nem érhető el, tekintse meg a bővítményfrissítési beállítások ablaktábláján megjelenő hibaüzenetet.

   ![Site Recovery szolgáltatás javítása gomb a bővítményfrissítési beállításokban](./media/azure-to-azure-autoupdate/repair.png)

- **Hiba:** A Futtatás másként fiók nem rendelkezik a helyreállítási szolgáltatások erőforráseléréséhez szükséges engedéllyel.

    **Ajánlott művelet**: Törölje, majd [hozza létre újra a Futtatás másként fiókot.](https://docs.microsoft.com/azure/automation/automation-create-runas-account) Vagy győződjön meg arról, hogy az Automation Run As fiók Azure Active Directory-alkalmazás hozzáfér a helyreállítási szolgáltatások erőforrás.

- **Hiba:** A futtatás nem található. Ezek közül valamelyik et törölték, vagy nem hozták létre - Az Azure Active Directory-alkalmazás, egyszerű szolgáltatás, szerepkör, automation tanúsítvány eszköz, Automation Connection eszköz - vagy az ujjlenyomat nem azonos a tanúsítvány és a kapcsolat között. 

    **Ajánlott művelet**: Törölje, majd [hozza létre újra a Futtatás másként fiókot.](https://docs.microsoft.com/azure/automation/automation-create-runas-account)

-  **Hiba:** Az Automation-fiók által használt Azure Run as Certificate hamarosan lejár. 

    A Futtatás másként fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számított egy évvel lejár. A tanúsítványt bármikor meg lehet újítani a lejárata előtt. Ha feliratkozott az e-mail értesítésekre, akkor is e-maileket kap, ha az Ön részéről műveletre van szükség. Ez a hiba két hónappal a lejárati dátum előtt jelenik meg, és kritikus hibává változik, ha a tanúsítvány lejárt. A tanúsítvány lejárta után az automatikus frissítés nem lesz működőképes, amíg meg nem újítja ugyanazt.

   **Ajánlott művelet:** A probléma megoldásához kattintson a "Javítás" gombra, majd a "Tanúsítvány megújítása" gombra.
    
   ![megújítás-cert](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> A tanúsítvány megújítása után frissítse a lapot, hogy az aktuális állapot frissüljön.
