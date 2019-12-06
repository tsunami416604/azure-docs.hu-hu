---
title: Azure Automation futtató fiókok kezelése
description: Ez a cikk bemutatja, hogyan kezelheti a futtató fiókokat a PowerShell-lel vagy a portálról.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae73188fa8818c84806709dc7518e3d5760ae187
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849530"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation futtató fiókok kezelése

Az Azure-ban az Azure-parancsmagokkal a Azure Automation futtató fiókok segítségével biztosítható a hitelesítés az erőforrások kezeléséhez.

Amikor létrehoz egy futtató fiókot, létrehoz egy új egyszerű szolgáltatásnevet a Azure Active Directory, és az előfizetés szintjén hozzárendeli a közreműködői szerepkört a felhasználóhoz. Az Azure Virtual Machines hibrid Runbook-feldolgozóit használó runbookok esetében a futtató fiókok helyett [felügyelt identitásokat](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) használhat az Azure-erőforrásokhoz való hitelesítéshez.

A futtató fiókok két típusa létezik:

* **Azure-beli futtató fiók** – ez a fiók a [Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) -alapú üzemi modell erőforrásainak kezelésére szolgál.
  * Létrehoz egy önaláírt tanúsítvánnyal ellátott Azure AD-alkalmazást, továbbá létrehoz egy egyszerűszolgáltatás-fiókot az Azure AD-ben lévő alkalmazáshoz, és hozzárendeli a közreműködői szerepkört a jelenlegi előfizetésben lévő fiókhoz. Ezt a beállítást bármikor módosíthatja Tulajdonos értékre vagy bármely egyéb szerepkörre. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
  * Létrehoz egy *AzureRunAsCertificate* nevű Automation-tanúsítványobjektumot a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza az Azure AD-alkalmazás által használt titkos tanúsítványkulcsot.
  * Létrehoz egy *AzureRunAsConnection* nevű Automation-kapcsolatobjektumot a megadott Automation-fiókban. Ez a kapcsolatobjektum magában foglalja az alkalmazásazonosítót, a bérlőazonosítót, az előfizetés-azonosítót és a tanúsítvány ujjlenyomatát.

* **Klasszikus Azure-beli futtató fiók** – ez a fiók használható a [klasszikus üzembe helyezési modell](../azure-resource-manager/resource-manager-deployment-model.md) erőforrásainak kezelésére.
  * Felügyeleti tanúsítvány létrehozása az előfizetésben
  * Létrehoz egy *AzureClassicRunAsCertificate* nevű Automation-tanúsítványobjektumot a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.
  * Létrehoz egy *AzureClassicRunAsConnection* nevű Automation-kapcsolatobjektumot a megadott Automation-fiókban. Ez a kapcsolatobjektum tartalmazza az előfizetés nevét, a subscriptionId paramétert, valamint a tanúsítványobjektum nevét.
  * A létrehozásához vagy megújításához az előfizetés egyik társ-rendszergazda tagjának kell lennie

  > [!NOTE]
  > Azure Cloud Solution Provider (Azure CSP) előfizetések csak a Azure Resource Manager modellt támogatják, a nem Azure Resource Manager szolgáltatások nem érhetők el a programban. CSP-előfizetés használata esetén a klasszikus Azure-beli futtató fiókot nem hozza létre a rendszer. Az Azure-beli futtató fiók még mindig létre lesz hozva. A CSP-előfizetésekkel kapcsolatos további tudnivalókért tekintse meg a [CSP-előfizetésekben elérhető szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments)című témakört.

  > [!NOTE]
  > A futtató fiókhoz tartozó egyszerű szolgáltatásnév nem rendelkezik a Azure Active Directory alapértelmezett olvasási engedélyeivel. Ha engedélyeket szeretne adni az Azure Active Directory olvasásához vagy kezeléséhez, az **API-engedélyek**területen meg kell adnia ezt az engedélyt az egyszerű szolgáltatáshoz. További információ: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>A futtató fiókok konfigurálásához szükséges engedélyek

Futtató fiók létrehozásához vagy frissítéséhez konkrét jogosultságokkal és engedélyekkel kell rendelkeznie. Azure Active Directory és egy előfizetéshez tartozó egyik alkalmazás rendszergazdája elvégezheti az összes feladatot. Az alábbi táblázat a feladatok elkülönítését mutatja be, a szükséges parancsmagot és engedélyeket:

|Tevékenység|Parancsmag  |Minimális engedélyek  |Az engedélyek beállítása|
|---|---------|---------|---|
|Azure AD-alkalmazás létrehozása|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Alkalmazás fejlesztői szerepköre<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Kezdőlap > Azure Active Directory > alkalmazás regisztrációja |
|Adjon hozzá egy hitelesítő adatot az alkalmazáshoz.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Kezdőlap > Azure Active Directory > alkalmazás regisztrációja|
|Azure AD-szolgáltatásnév létrehozása és beszerzése|[Új – AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Kezdőlap > Azure Active Directory > alkalmazás regisztrációja|
|A RBAC szerepkör kiosztása vagy beolvasása a megadott rendszerbiztonsági tag számára|[Új – AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | A következő engedélyekkel kell rendelkeznie:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Vagy a:</br></br>Felhasználói hozzáférés a rendszergazdához vagy a tulajdonoshoz        | [Előfizetés](../role-based-access-control/role-assignments-portal.md)</br>Kezdőlap > előfizetések > \<előfizetés neve\>-Access Control (IAM)|
|Automation-tanúsítvány létrehozása vagy eltávolítása|[Új – AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Közreműködő az erőforráscsoporthoz         |Automation-fiók erőforráscsoport|
|Automation-kapcsolatok létrehozása vagy eltávolítása|[Új – AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Közreműködő az erőforráscsoporthoz |Automation-fiók erőforráscsoport|

<sup>1</sup> az Azure ad-bérlőn kívüli nem rendszergazda felhasználók [regisztrálhatnak ad-alkalmazásokat](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) , ha az Azure ad-bérlő **felhasználóinak az alkalmazások regisztrálása** lehetőség a **felhasználói beállítások** lapon az **Igen**értékre van állítva. Ha az alkalmazás regisztrációja beállítás értéke **nem**, akkor a műveletet végrehajtó felhasználónak az előző táblázatban definiált értéknek kell lennie.

Ha nem tagja az előfizetés Active Directory példányának, mielőtt hozzáadja az előfizetés **globális rendszergazdai** szerepköréhez, vendégként lesz hozzáadva. Ebben az esetben `You do not have permissions to create…` figyelmeztetés jelenik meg az **Automation-fiók hozzáadása** lapon. A **globális rendszergazdai** szerepkörhöz hozzáadott felhasználók először eltávolíthatók az előfizetéshez tartozó Active Directory példányból, és újra hozzáadhatók, így teljes jogú felhasználóként Active Directory. Ez a helyzet úgy ellenőrizhető, ha az Azure Portal **Azure Active Directory** panelén a **Felhasználók és csoportok** és a **Minden felhasználó** elemre kattint, majd a konkrét felhasználó kiválasztása után a **Profil** elemet választja. A felhasználók profilja alatti **Felhasználó típusa** attribútum értéke ne legyen **Guest** (vendég).

## <a name="permissions-classic"></a>A klasszikus futtató fiókok konfigurálásának engedélyei

Klasszikus futtató fiókok konfigurálásához vagy megújításához az előfizetés szintjén kell lennie a **társ-rendszergazda** szerepkörnek. A klasszikus engedélyekkel kapcsolatos további tudnivalókért tekintse meg a [klasszikus Azure-előfizetés rendszergazdái](../role-based-access-control/classic-administrators.md#add-a-co-administrator)című témakört.

## <a name="create-a-run-as-account-in-the-portal"></a>Futtató fiók létrehozása a portálon

Az ebben a szakaszban szereplő lépéseket követve frissítheti Azure Automation-fiókját az Azure Portalról. Egyenként hozza létre a futtató és a klasszikus futtató fiókokat. Ha nem kell klasszikus erőforrásokat felügyelnie, egyszerűen létrehozhatja csak a futtató fiókot.

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be az **Automation** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Automation-fiókok** elemet.
3. Az **Automation-fiókok** oldalon válassza ki az Automation-fiókját a listából.
4. A bal oldali panel **Fiókbeállítások** részén válassza a **Futtató fiókok** lehetőséget.
5. Attól függően, hogy melyik fiókra van szüksége, válassza az **Azure-alapú futtató fiók** vagy a **Klasszikus Azure-alapú futtató fiók** lehetőséget. Miután választott, megjelenik az **Azure-alapú futtató fiók felvétele** vagy a **Klasszikus Azure-alapú futtató fiók felvétele** panel. Az áttekintési információk tanulmányozása után kattintson a **Létrehozás** gombra a futtató fiók létrehozásának folytatásához.
6. Amíg az Azure létrehozza a futtató fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát. Megjelenik egy szalagcím is azzal az üzenettel, hogy a fiók létrehozása folyamatban van. A folyamat eltarthat pár percig.

## <a name="create-run-as-account-using-powershell"></a>Futtató fiók létrehozása a PowerShell használatával

## <a name="prerequisites"></a>Előfeltételek

A következő lista a futtató fiókok PowerShellben való létrehozásának követelményeit tartalmazza:

* Windows 10 vagy Windows Server 2016, Azure Resource Manager modul 3.4.1-es és újabb verzióival. A PowerShell-szkript nem támogatja a Windows korábbi verzióit.
* Az Azure PowerShell 1.0-s és újabb verziói. Információk a PowerShell 1.0-s kiadásáról: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
* Egy Automation-fiók, amelyre a rendszer az *–AutomationAccountName* és az *-ApplicationDisplayName* paraméterek értékeként hivatkozik.
* A [futtató fiókok konfigurálásához szükséges engedélyekben](#permissions) felsorolt jogosultságokkal egyenértékű engedélyek

A *SubscriptionID*, a *ResourceGroup*és a *AutomationAccountName*értékeinek lekéréséhez, amelyek a parancsfájl kötelező paraméterei, hajtsa végre a következő lépéseket:

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be az **Automation** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Automation-fiókok** elemet.
1. Az Automation-fiók oldalon válassza ki az Automation-fiókját, majd a **Fiókbeállítások** területen válassza a **Tulajdonságok** lehetőséget.
1. Jegyezze fel az **előfizetés-azonosító**, a **név**és az **erőforráscsoport** értékét a **Tulajdonságok** lapon.

   ![Az Automation-fiók "tulajdonságok" lapja](media/manage-runas-account/automation-account-properties.png)

Ez a PowerShell-szkript a következő konfigurációk támogatását tartalmazza:

* Futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Egy futtató fiók és egy klasszikus futtató fiók létrehozása a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvánnyal.
* Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával.

>[!NOTE]
> Ha klasszikus futtató fiók létrehozását választja, a szkript végrehajtása után töltse fel a nyilvános tanúsítványt (.cer fájlnévkiterjesztéssel) annak az előfizetésnek a felügyeleti tárába, amelyben az Automation-fiókot létrehozták.

1. Mentse el a következő parancsprogramot a számítógépén. Ebben a példában mentse a következő fájlnéven: *New-RunAsAccount.ps1*.

   A szkript több Azure Resource Manager parancsmagot használ az erőforrások létrehozásához. Az előző [engedélyek](#permissions) táblázat a parancsmagokat és a szükséges engedélyeket mutatja.

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

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


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
    > A **Add-AzureRmAccount** mostantól egy alias a **kapcsolat-AzureRmAccount**. Ha nem látja a **AzureRMAccount**, használhatja a **AzureRMAccount**, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban.

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

* Ha egy (.cer formátumú) vállalati tanúsítvánnyal rendelkező klasszikus futtató fiókot hozott létre, használja ezt a tanúsítványt. A [felügyeleti API-tanúsítványoknak a Azure Portal való feltöltéséhez](../azure-api-management-certs.md)kövesse az utasításokat.

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

A futtató fiók lejárata előtt egy bizonyos ponton meg kell újítania a tanúsítványt. Ha úgy véli, hogy a futtató fiók biztonsága sérült, akkor törölheti, majd újra létrehozhatja a fiókot. Ebben a részben ezeknek a műveleteknek a végrehajtását ismertetjük.

A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy év múlva jár le. A tanúsítványt bármikor meg lehet újítani a lejárata előtt. A megújításakor a rendszer megőrzi a jelenlegi érvényes tanúsítványt annak biztosítására, hogy a várólistára helyezett vagy aktívan futó runbookok, valamint a futtató fiókkal végzett hitelesítés ne legyen negatív hatással. A tanúsítvány a lejárati dátumáig érvényes marad.

> [!NOTE]
> Ha úgy konfigurálta az Automation futtató fiókot, hogy a vállalati hitelesítésszolgáltató által kibocsátott tanúsítványt használja, és ezt a lehetőséget alkalmazza, a vállalati tanúsítvány egy önaláírt tanúsítványra lesz lecserélve.

A tanúsítvány megújításához tegye a következőket:

1. Az Azure Portalon nyissa meg az Automation-fiókot.

1. Válassza a **fiók beállításai**alatt a **futtató fiókok** elemet.

    ![Az Automation-fiók tulajdonságpanelje](media/manage-runas-account/automation-account-properties-pane.png)

1. A **Futtató fiókok** tulajdonságlapján válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelynek a tanúsítványát meg kívánja újítani.

1. A kiválasztott fiók **Tulajdonságok** paneljén kattintson a **Tanúsítvány megújítása** elemre.

    ![Futtató fiók tanúsítványának megújítása](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="auto-cert-renewal"></a>Automatikus tanúsítvány-megújítás beállítása automatizálási runbook

A tanúsítványok automatikus megújításához használhat Automation-runbook. A [githubon](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) a következő parancsfájl engedélyezi ezt a funkciót az Automation-fiókjában.

- A `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` szkript hetente ütemezett ütemtervet hoz létre a futtató fiók tanúsítványainak megújításához.
- A szkript hozzáadja az Automation-fiókjához egy **Update-AutomationRunAsCredential** runbook.
  - A runbook kódját a GitHubon is megtekintheti a következő parancsfájlban: [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1).
  - A fájlban található PowerShell-kód használatával manuálisan is megújíthatja a tanúsítványokat, ha szükséges.

A megújítási folyamat azonnali teszteléséhez kövesse az alábbi lépéseket:

1. Szerkessze az **Update-AutomationRunAsCredential** runbook, és helyezzen el egy comment karaktert (`#`) 122 a `Exit(1)` parancs előtt az alábbi ábrán látható módon.

   ```powershell
   #Exit(1)
   ```

2. Tegye közzé a runbook.
3. Indítsa el a runbook.
4. A sikeres megújítás ellenőrzése a következő kóddal:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. A teszt után szerkessze a runbook, és távolítsa el az **1. lépésben**hozzáadott Megjegyzés karaktert.
6. **Tegye közzé** a runbook.

> [!NOTE]
> A szkript végrehajtásához a Azure Active Directory **globális rendszergazdájának** vagy **vállalati rendszergazdájának** kell lennie.

## <a name="limiting-run-as-account-permissions"></a>Futtató fiók engedélyeinek korlátozása

Az Azure-beli erőforrásokhoz való automatizálás célzásának szabályozásához a PowerShell-galériában futtathatja az [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) parancsfájlt, hogy a meglévő futtató fiók egyszerű szolgáltatását egyéni szerepkör-definíció létrehozásához és használatához módosítsa. Ez a szerepkör a [Key Vault](https://docs.microsoft.com/azure/key-vault/)kivételével minden erőforráshoz rendelkezik engedéllyel.

> [!IMPORTANT]
> A `Update-AutomationRunAsAccountRoleAssignments.ps1`-parancsfájl futtatása után a runbookok a futtató fiókok használatával nem fog működni. Tekintse át a runbookok a fiókjában az Azure kulcstartóra irányuló hívásokhoz.
>
> A kulcstartóhoz való hozzáférés engedélyezéséhez Azure Automation runbookok [hozzá kell adnia a futtató fiókot a kulcstartó engedélyeihez](#add-permissions-to-key-vault).

Ha meg kell határoznia, hogy mit tehet a RunAs szolgáltatás, további erőforrástípusok hozzáadásával az egyéni szerepkör-definíció `NotActions`. A következő példa korlátozza a `Microsoft.Compute`hoz való hozzáférést. Ha hozzáadja ezt a szerepkör-definícióhoz, akkor ez a szerepkör nem fér **hozzá a számítási** erőforrásokhoz. További információ a szerepkör-definíciókkal kapcsolatban: [Az Azure-erőforrások szerepkör-definícióinak megismerése](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Annak megállapításához, hogy a futtató fiók által használt szolgáltatásnév a **közreműködő** vagy egy egyéni szerepkör-definícióban van-e, nyissa meg az Automation-fiókját, majd a **Fiókbeállítások**területen válassza a **futtató fiókok** > Azure-beli **futtató fiók**lehetőséget. A **szerepkör** területen megtalálja a használatban lévő szerepkör-definíciót.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Az Automation futtató fiókok több előfizetés vagy Automation-fiók esetében használt szerepkör-definíciójának meghatározásához használhatja a [Check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) parancsfájlt a PowerShell-Galéria.

### <a name="add-permissions-to-key-vault"></a>Engedélyek hozzáadása a Key Vaulthoz

Ha azt szeretné, hogy a Azure Automation felügyelje Key Vault és a futtató fiók egyszerű szolgáltatásának egyéni szerepkör-definícióját használja, további lépéseket kell tennie a viselkedés engedélyezéséhez:

* Engedélyek megadása a Key Vault számára
* Hozzáférési házirend beállítása

A PowerShell-galéria [extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) parancsfájllal engedélyezheti a futtató fiók engedélyeit a kulcstartó számára, vagy megnyithatja a hozzáférést a [Key vaulthoz hozzáférési jogosultságokat biztosító alkalmazások](../key-vault/key-vault-group-permissions-for-apps.md) számára, ha további információt szeretne a kulcstároló beállításairól.

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

## <a name="next-steps"></a>Következő lépések

* Az egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [alkalmazás-objektumok és egyszerű szolgáltatások objektumai](../active-directory/develop/app-objects-and-service-principals.md).
* A tanúsítványokkal és az Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [Az Azure-beli tanúsítványok áttekintése Cloud Services](../cloud-services/cloud-services-certs-create.md).
