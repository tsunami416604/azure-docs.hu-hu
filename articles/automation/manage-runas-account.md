---
title: Azure Automation futtató fiókok kezelése
description: Ez a cikk azt ismerteti, hogy a futtató fiókok kezelése a PowerShell-lel, vagy a portálon.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/12/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7bd84b42cfa61d199d70e02345f9229a45fd7704
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726168"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation futtató fiókok kezelése

Futtató fiókok az Azure Automationben a hitelesítés az Azure-parancsmagjaival az Azure-erőforrások kezeléséhez használhatók.

Amikor létrehoz egy futtató fiókot, az Azure Active Directoryban hoz létre egy új egyszerű szolgáltatási felhasználó, és hozzárendeli a közreműködő szerepkört az előfizetés szintjén a felhasználót. A hibrid Runbook-feldolgozók használata Azure virtuális gépeken futó runbookokat, használhatja [felügyelt identitások az Azure-erőforrások](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) helyett a futtató fiókok az Azure-erőforrások hitelesítéséhez.

Futtató fiókok két típusa van:

* **Azure futtató fiók** – Ez a fiók Resource Manager üzembe helyezési modell erőforrások kezelésére használható.
  * Létrehoz egy önaláírt tanúsítvánnyal ellátott Azure AD-alkalmazást, továbbá létrehoz egy egyszerűszolgáltatás-fiókot az Azure AD-ben lévő alkalmazáshoz, és hozzárendeli a közreműködői szerepkört a jelenlegi előfizetésben lévő fiókhoz. Ezt a beállítást bármikor módosíthatja Tulajdonos értékre vagy bármely egyéb szerepkörre. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
  * Létrehoz egy *AzureRunAsCertificate* nevű Automation-tanúsítványobjektumot a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza az Azure AD-alkalmazás által használt titkos tanúsítványkulcsot.
  * Létrehoz egy *AzureRunAsConnection* nevű Automation-kapcsolatobjektumot a megadott Automation-fiókban. Ez a kapcsolatobjektum magában foglalja az alkalmazásazonosítót, a bérlőazonosítót, az előfizetés-azonosítót és a tanúsítvány ujjlenyomatát.

* **Az Azure klasszikus futtató fiók** – Ez a fiók használható a klasszikus üzembe helyezési modell erőforrások kezeléséhez.
  * Létrehoz egy *AzureClassicRunAsCertificate* nevű Automation-tanúsítványobjektumot a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.
  * Létrehoz egy *AzureClassicRunAsConnection* nevű Automation-kapcsolatobjektumot a megadott Automation-fiókban. Ez a kapcsolatobjektum tartalmazza az előfizetés nevét, a subscriptionId paramétert, valamint a tanúsítványobjektum nevét.
  
  > [!NOTE]
  > Az Azure Cloud Solution Provider (az Azure CSP)-előfizetések támogatása csak az Azure Resource Manager modellel, nem az Azure Resource Manager - szolgáltatások nem érhetők el a programban. Az Azure klasszikus futtató fiók létrehozása nem CSP-előfizetésekben használatakor. Az Azure futtató fiók továbbra is létrejön. Kriptográfiai Szolgáltató az előfizetésekkel kapcsolatos további tudnivalókért lásd: [CSP-előfizetésekben elérhető szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

## <a name="permissions"></a>Engedélyek futtató fiókok beállítása

Hozzon létre, vagy frissíteni egy futtató fiókot, jogosultságokkal és engedélyekkel kell rendelkeznie. Egy globális rendszergazdai vagy Társadminisztrátori elvégezhető összes feladatot. A feladatkörök esetében olyan helyzet az alábbi táblázat mutatja a feladatok, a varázsló használatával egyenértékű parancsmagot és a szükséges engedélyek listája:

|Tevékenység|Parancsmag  |Minimális engedélyek  |
|---|---------|---------|
|Az Azure AD-alkalmazás létrehozása|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Alkalmazás-fejlesztői szerepkör        |
|A hitelesítő adatok hozzáadása az alkalmazáshoz.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Alkalmazás-rendszergazda vagy a globális rendszergazda         |
|Hozzon létre és lekérése egy Azure AD-szolgáltatásnév|[Új AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Alkalmazás-rendszergazda vagy a globális rendszergazda        |
|Rendelje hozzá, vagy szerezze be az RBAC-szerepkört a megadott rendszerbiztonsági tag|[Új-azurermroleassignment parancsmagot](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-azurermroleassignment parancsmagot](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Felhasználói hozzáférés rendszergazdája vagy tulajdonosa        |
|Hozzon létre, vagy távolítsa el az Automation-tanúsítvány|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Az erőforráscsoporthoz közreműködő         |
|Hozzon létre vagy egy Automation-kapcsolat eltávolítása|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Az erőforráscsoporthoz közreműködő |

* Című cikkben ismertetett módon Microsoft.Automation-erőforrások közreműködői szerepkörével egyenértékű engedélyekkel rendelkezik az AD-felhasználói fiókot [szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md#contributor).  
* Az Azure AD-bérlő nem rendszergazda jogosultságú felhasználói abban az esetben végezhetik el az [AD-alkalmazások regisztrálását](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions), ha az Azure AD-bérlő **Felhasználói beállítások** oldalán található **A felhasználók regisztrálhatnak alkalmazásokat** beállítás **Igen** értékre van állítva. Ha az Alkalmazásregisztrációk beállítás értéke **Nem**, ezt a műveletet csak az Azure AD globális rendszergazdái hajthatják végre.

Ha nem tagja az előfizetéshez tartozó Active Directory-példánynak, mielőtt hozzáadják a globális rendszergazdai vagy társadminisztrátori szerepkörhöz az előfizetés, vendégként van hozzáadva. Ebben a helyzetben kap egy `You do not have permissions to create…` szóló figyelmeztetés a **Automation-fiók hozzáadása** lap. A globális rendszergazdai vagy társadminisztrátori szerepkörhöz hozzáadott felhasználók először eltávolíthatók az előfizetéshez tartozó Active Directory-példányból, majd újra hozzáadhatók, így teljes jogú felhasználók lehetnek az Active Directoryban. Ez a helyzet úgy ellenőrizhető, ha az Azure Portal **Azure Active Directory** panelén a **Felhasználók és csoportok** és a **Minden felhasználó** elemre kattint, majd a konkrét felhasználó kiválasztása után a **Profil** elemet választja. A felhasználók profilja alatti **Felhasználó típusa** attribútum értéke ne legyen **Guest** (vendég).

## <a name="create-a-run-as-account-in-the-portal"></a>Futtató fiók létrehozása a portálon

Az ebben a szakaszban szereplő lépéseket követve frissítheti Azure Automation-fiókját az Azure Portalról. Egyenként hozza létre a futtató és a klasszikus futtató fiókokat. Ha nem kell klasszikus erőforrásokat felügyelnie, egyszerűen létrehozhatja csak a futtató fiókot.  

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be az **Automation** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Automation-fiókok** elemet.
3. Az **Automation-fiókok** oldalon válassza ki az Automation-fiókját a listából.
4. A bal oldali panel **Fiókbeállítások** részén válassza a **Futtató fiókok** lehetőséget.  
5. Attól függően, hogy melyik fiókra van szüksége, válassza az **Azure-alapú futtató fiók** vagy a **Klasszikus Azure-alapú futtató fiók** lehetőséget. Miután választott, megjelenik az **Azure-alapú futtató fiók felvétele** vagy a **Klasszikus Azure-alapú futtató fiók felvétele** panel. Az áttekintési információk tanulmányozása után kattintson a **Létrehozás** gombra a futtató fiók létrehozásának folytatásához.  
6. Amíg az Azure létrehozza a futtató fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát. Megjelenik egy szalagcím is azzal az üzenettel, hogy a fiók létrehozása folyamatban van. A folyamat eltarthat pár percig.  

## <a name="create-run-as-account-using-powershell"></a>Hozzon létre futtató fiókot PowerShell-lel

## <a name="prerequisites"></a>Előfeltételek

Az alábbi lista tartalmazza a futtató fiók létrehozása a PowerShell követelményeknek:

* Windows 10-es vagy Windows Server 2016 az Azure Resource Manager 3.4.1-es vagy újabb. A PowerShell-szkript nem támogatja a Windows korábbi verzióit.
* Az Azure PowerShell 1.0-s és újabb verziói. Információk a PowerShell 1.0-s kiadásáról: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
* Egy Automation-fiók, amelyre a rendszer az *–AutomationAccountName* és az *-ApplicationDisplayName* paraméterek értékeként hivatkozik.
* Milyen szerepel, az egyenértékű engedélyekkel rendelkezik [szükséges engedélyek futtató fiókok beállítása](#permissions)

A tartozó értékeket beolvasni *SubscriptionID*, *ResourceGroup*, és *AutomationAccountName*, a parancsfájl szükséges paraméterek, a következő lépéseket:

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be az **Automation** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Automation-fiókok** elemet.
1. Az Automation-fiók oldalon válassza ki az Automation-fiókját, majd a **Fiókbeállítások** területen válassza a **Tulajdonságok** lehetőséget.  
1. Megjegyzés: a **előfizetés-azonosító**, **neve**, és **erőforráscsoport** az értékeket a **tulajdonságok** lap.

   ![Az Automation-fiók "Tulajdonságok" lap](media/manage-runas-account/automation-account-properties.png)

Ez a PowerShell-szkript a következő konfigurációk támogatását tartalmazza:

* Futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Egy futtató fiók és egy klasszikus futtató fiók létrehozása a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvánnyal.
* Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával.

>[!NOTE]
> Ha klasszikus futtató fiók létrehozását választja, a szkript végrehajtása után töltse fel a nyilvános tanúsítványt (.cer fájlnévkiterjesztéssel) annak az előfizetésnek a felügyeleti tárába, amelyben az Automation-fiókot létrehozták.

1. Mentse el a következő parancsprogramot a számítógépén. Ebben a példában mentse a következő fájlnéven: *New-RunAsAccount.ps1*.

   A szkript több Azure Resource Manager parancsmagjainak erőforrások létrehozásához. Az alábbi táblázat a parancsmagokat és a szükséges engedélyekkel.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    Connect-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

    > [!IMPORTANT]
    > **Add-AzureRmAccount** alias már **Connect-AzureRMAccount**. Ha a Keresés a szalagtár elemmel, ha nem látja, akkor **Connect-AzureRMAccount**, használható **Add-AzureRmAccount**, vagy beállíthatja a [a modulok frissítése](automation-update-azure-modules.md) az Automation Fiók.

1. A számítógépén indítsa el a **Windows PowerShell** alkalmazást a **Kezdőlap** képernyőről emelt szintű felhasználói jogokkal.
1. A rendszergazda jogú parancssori felületből lépjen abba a mappába, amely az 1. lépésben létrehozott szkriptet tartalmazza.  
1. Futtassa a szkriptet a kívánt konfigurációhoz szükséges paraméterértékekkel.

    **Futtató fiók létrehozása önaláírt tanúsítvány használatával**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Futtató fiók és klasszikus futtató fiók létrehozása vállalati tanúsítvány használatával**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával**
  
    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > A rendszer az Azure-ral történő hitelesítést fogja kérni a szkript futtatása után. Jelentkezzen be egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörnek, és emellett az előfizetés társadminisztrátorának is számít.

A szkript sikeres futtatása után jegyezze fel a következőket:

* Ha önaláírt nyilvános tanúsítvánnyal (.cer fájl) ellátott klasszikus futtató fiókot hoz létre, a szkript létrehozza és menti a tanúsítványt a számítógépen a PowerShell-munkamenet végrehajtásához használt *%USERPROFILE%\AppData\Local\Temp* felhasználói profilhoz tartozó ideiglenes mappába.

* Ha egy (.cer formátumú) vállalati tanúsítvánnyal rendelkező klasszikus futtató fiókot hozott létre, használja ezt a tanúsítványt. Kövesse az utasításokat [felügyeleti API-tanúsítványok feltöltése az Azure Portalra](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Futtató fiók vagy klasszikus futtató fiók törlése

Ez a témakör ismerteti, hogyan törölhet és hozhat újra létre futtató fiókot vagy klasszikus futtató fiókot. A művelet során a rendszer megőrzi az Automation-fiókot. A törölt futtató fiókot vagy klasszikus futtató fiókot újra létrehozhatja az Azure Portalon.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

2. Az **Automation-fiók** oldalon válassza a **Futtató fiókok** lehetőséget.

3. A **Futtató fiókok** tulajdonságlapján válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelyet törölni kíván. Ezt követően a kiválasztott fiók **Tulajdonságok** paneljén kattintson a **Törlés** elemre.

 ![Futtató fiók törlése](media/manage-runas-account/automation-account-delete-runas.png)

1. A fiók törlése során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

1. A törlés után újra létrehozhatja a fiókot a **Futtató fiókok** tulajdonságlapon az **Azure-alapú futtató fiók** lehetőség kiválasztásával.

 ![Automation futtató fiók újbóli létrehozása](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Önaláírt tanúsítvány megújítása

Később a Futtatás mint fiók lejárata előtt szüksége a tanúsítvány megújításához. Ha úgy véli, hogy a futtató fiók biztonsága sérült, akkor törölheti, majd újra létrehozhatja a fiókot. Ebben a részben ezeknek a műveleteknek a végrehajtását ismertetjük.

A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy év múlva jár le. A tanúsítványt bármikor meg lehet újítani a lejárata előtt. A Megújításkor, a rendszer megőrzi a jelenleg érvényes tanúsítványt annak érdekében, hogy a nem negatív hatással runbookokat, amelyek akár vagy aktívan futó rendszer várólistára helyezi, és hitelesítést végeznek a futtató fiókot. A tanúsítvány a lejárati dátumáig érvényes marad.

> [!NOTE]
> Ha úgy konfigurálta az Automation futtató fiókot, hogy a vállalati hitelesítésszolgáltató által kibocsátott tanúsítványt használja, és ezt a lehetőséget alkalmazza, a vállalati tanúsítvány egy önaláírt tanúsítványra lesz lecserélve.

A tanúsítvány megújításához tegye a következőket:

1. Az Azure Portalon nyissa meg az Automation-fiókot.

1. Válassza ki **futtató fiókok** alatt **Fiókbeállítások**.

    ![Az Automation-fiók tulajdonságpanelje](media/manage-runas-account/automation-account-properties-pane.png)

1. A **Futtató fiókok** tulajdonságlapján válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelynek a tanúsítványát meg kívánja újítani.

1. A kiválasztott fiók **Tulajdonságok** paneljén kattintson a **Tanúsítvány megújítása** elemre.

    ![Futtató fiók tanúsítványának megújítása](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát. 

## <a name="limiting-run-as-account-permissions"></a>Futtató fiók vonatkozó engedélyek korlátozása

Szabályozhatja, hogy az automation-erőforrásokon, az Azure Automationben célzó, a Futtatás mint fiók alapértelmezés szerint az előfizetés közreműködői jogokat kapnak. Mi a Futtatás mint szolgáltatásnév mindent korlátozni kell, ha távolítsa el a fiókot a közreműködő szerepkört az előfizetés, és közreműködője hozzáadása az erőforráscsoportok szeretne megadni.

Az Azure Portalon válassza ki a **előfizetések** , és válassza ki az előfizetést, az Automation-fiók. Válassza ki **hozzáférés-vezérlés (IAM)** majd válassza ki a **szerepkör-hozzárendelések** fülre. Keresse meg az Automation-fiókhoz tartozó egyszerű szolgáltatásról (tűnik \<AutomationAccountName\>_unique azonosítója). Válassza ki a fiókot, és kattintson a **eltávolítása** eltávolítja az előfizetésből.

![Előfizetés közreműködő](media/manage-runas-account/automation-account-remove-subscription.png)

Az egyszerű szolgáltatás hozzáadása egy erőforráscsoportot, válassza ki az erőforráscsoportot az Azure Portalon, és válassza a **hozzáférés-vezérlés (IAM)**. Válassza ki **szerepkör-hozzárendelés hozzáadása**, ekkor megnyílik a **szerepkör-hozzárendelés hozzáadása** lapot. A **szerepkör**válassza **közreműködői**. Az a **kiválasztása** szöveg mezőbe írja be az egyszerű szolgáltatás a Futtatás mint fiók nevét, majd válassza ki a listából. Kattintson a **Mentés** gombra a módosítások mentéséhez. Hajtsa végre ezeket a lépéseket az erőforráscsoportoknál, szeretne adni az Azure Automation futtató szolgáltatásnév hozzáférhessen a.

## <a name="misconfiguration"></a>Hibás konfiguráció

Előfordulhat, hogy a futtató fiók vagy klasszikus futtató fiók megfelelő működéséhez szükséges valamelyik konfigurációs elem törlődött, illetve nem megfelelően hozták létre az első beállításkor. Ilyen elemek például a következők:

* Tanúsítványobjektum
* Kapcsolatobjektum
* A futtató fiók el lett távolítva a közreműködői szerepkörből
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az előző és más hibás konfigurációk esetében az Automation-fiók észleli a módosításokat, és *Hiányos* állapotot jelez a fiók **Futtató fiókok** tulajdonságpaneljén.

![Hiányos futtatófiók-konfigurációs állapot](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Ha kiválasztja a futtató fiókot, a fiók **Tulajdonságok** paneljén a következő hibaüzenet jelenik meg:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

A futtató fiókkal kapcsolatos hasonló problémákat gyorsan elháríthatja a fiók törlésével és ismételt létrehozásával.

## <a name="next-steps"></a>További lépések

* Szolgáltatásnevekkel kapcsolatos további információkért lásd: [alkalmazásobjektumok és egyszerű szolgáltatási objektumok](../active-directory/develop/app-objects-and-service-principals.md).
* Tanúsítványokkal és az Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [tanúsítványok áttekintése az Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
