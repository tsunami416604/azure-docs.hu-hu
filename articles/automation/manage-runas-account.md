---
title: Az Azure Automation futtatása fiókokként kezelésével
description: Ez a cikk ismerteti, hogyan kezelheti a Futtatás másként fiókok at PowerShell, vagy a portálon.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 8d2c2f1e7ee10153108e54649ceba45b927b0cd2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676628"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Az Azure Automation futtatása fiókokként kezelésével

Futtatás Másként fiókok az Azure Automation-ben hitelesítést biztosít az Azure-beli erőforrások azure-beli parancsmagok használatával történő kezeléséhez. Amikor létrehoz egy Futtatás másként fiókot, létrehoz egy új egyszerű szolgáltatásfelhasználót az Azure Active Directoryban (AD), és az előfizetés szintjén hozzárendeli a közreműködői szerepkört ehhez a felhasználóhoz. A hibrid runbook-feldolgozókat azure-beli virtuális gépeken használó runbookok esetében a Futtatás másként fiókok helyett [felügyelt identitásokat](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) használhat az Azure-erőforrások hitelesítéséhez.

A futtatás i fiók szolgáltatásának nem rendelkezik az Azure AD alapértelmezés szerint olvasásához szükséges engedélyekkel. Ha az Azure AD olvasására vagy kezelésére vonatkozó engedélyeket szeretne hozzáadni, az API-engedélyek alatt meg kell adnia az egyszerű szolgáltatásra vonatkozó **engedélyeket.** További információ: [Engedélyek hozzáadása webes API-k eléréséhez.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="types-of-run-as-accounts"></a>A Futtatás másként fiókok típusai

Az Azure Automation kétféle Futtatás másként fiókot használ:

* Azure-beli futtató fiók
* Azure Klasszikus futtatás mint fiók

>[!NOTE]
>Az Azure Cloud Solution Provider (CSP) előfizetések csak az Azure Resource Manager modellt támogatják. Nem Azure Resource Manager-szolgáltatások nem érhetők el a programban. Ha csp-előfizetést használ, az Azure Classic Run As fiók nem jön létre, de az Azure Run As fiók jön létre. A csp-előfizetésekről az [Elérhető szolgáltatások a CSP-előfizetésekben (CsP-előfizetések) témakörben olvashat bővebben.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)

### <a name="run-as-account"></a>Futtató fiók

A Futtatás másként fiók kezeli az [Erőforrás-kezelő telepítési modelljének](../azure-resource-manager/management/deployment-models.md) erőforrásait. A következő feladatokat végzi.

* Létrehoz egy önaláírt tanúsítvánnyal ellátott Azure AD-alkalmazást, továbbá létrehoz egy egyszerűszolgáltatás-fiókot az Azure AD-ben lévő alkalmazáshoz, és hozzárendeli a közreműködői szerepkört a jelenlegi előfizetésben lévő fiókhoz. A tanúsítvány beállítást tulajdonosra vagy bármely más szerepkörre módosíthatja. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
  
* Létrehoz egy automation `AzureRunAsCertificate` tanúsítványeszközt a megadott Automation-fiókban. A tanúsítványegység rendelkezik a tanúsítvány titkos kulcs, amely az Azure AD-alkalmazás által használt.
  
* Létrehoz egy Automation-kapcsolateszközt, amelyet a megadott Automation-fiókban neveznek el. `AzureRunAsConnection` A kapcsolati eszköz rendelkezik az alkalmazásazonosítóval, a bérlőazonosítóval, az előfizetés-azonosítóval és a tanúsítvány ujjlenyomatával.

### <a name="azure-classic-run-as-account"></a>Klasszikus Azure-futtatófiók

Az Azure Classic Run As fiók kezeli a [klasszikus üzembe helyezési modell](../azure-resource-manager/management/deployment-models.md) erőforrásait. Az ilyen típusú fiók létrehozásához vagy megújításához társadminisztrátornak kell lennie az előfizetésben.

Az Azure Classic Run As fiók a következő feladatokat hajtja végre.

  * Felügyeleti tanúsítványt hoz létre az előfizetésben.

  * Létrehoz egy automation `AzureClassicRunAsCertificate` tanúsítványeszközt a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.

  * Létrehoz egy Automation-kapcsolateszközt, amelyet a megadott Automation-fiókban neveznek el. `AzureClassicRunAsConnection` A kapcsolati eszköz rendelkezik az előfizetés neve, előfizetés-azonosító és tanúsítványeszköz neve.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Futtatás fiókként engedélyek

Ez a szakasz a rendszeres Futtatás másként fiókok és a Klasszikus futtatás másként fiókok engedélyeit határozza meg.

### <a name="permissions-to-configure-run-as-accounts"></a>A Futtatás másként fiókok konfigurálásához szükséges engedélyek

Futtatási mint fiók létrehozásához vagy frissítéséhez speciális jogosultságokkal és engedélyekkel kell rendelkeznie. Az Azure Active Directory alkalmazásrendszergazdája és egy előfizetés tulajdonosa az összes feladatot elvégezheti. Olyan helyzetben, amikor a feladatok elkülönítése van, az alábbi táblázat a feladatok, az egyenértékű parancsmag és a szükséges engedélyek felsorolását mutatja be:

|Tevékenység|Parancsmag  |Minimális engedélyek  |Az engedélyek beállításának helye|
|---|---------|---------|---|
|Azure AD-alkalmazás létrehozása|[Új AzAD alkalmazás](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Alkalmazásfejlesztői szerepkör<sup>1</sup>        |[Azure Hirdetés](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > > alkalmazásregisztrációk kezdőlapja |
|Adjon hozzá hitelesítő adatokat az alkalmazáshoz.|[Új-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Alkalmazás- vagy globális rendszergazda<sup>1</sup>         |[Azure Hirdetés](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > > alkalmazásregisztrációk kezdőlapja|
|Azure AD egyszerű szolgáltatás létrehozása és bekéselése|[Új-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Alkalmazás- vagy globális rendszergazda<sup>1</sup>        |[Azure Hirdetés](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > > alkalmazásregisztrációk kezdőlapja|
|Az RBAC szerepkör hozzárendelése vagy bekéselése a megadott egyszerű felhasználóhoz|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Felhasználói hozzáférés rendszergazdája vagy tulajdonosa, vagy rendelkezik a következő engedélyekkel:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Előfizetés](../role-based-access-control/role-assignments-portal.md)</br>Otthoni > előfizetések \<\> > előfizetés neve - Hozzáférés-vezérlés (IAM)|
|Automatizálási tanúsítvány létrehozása vagy eltávolítása|[Új-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Eltávolítás-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Közreműködő az erőforráscsoportban         |Automatizálási fiók erőforráscsoportja|
|Automatizálási kapcsolat létrehozása vagy eltávolítása|[Új-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Eltávolítás-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Közreműködő az erőforráscsoportban |Automatizálási fiók erőforráscsoportja|

<sup>1</sup> Az Azure AD-bérlő nem rendszergazdai felhasználói [regisztrálhatnak AD-alkalmazásokat,](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) ha az Azure AD-bérlő **felhasználói regisztrálhatnak alkalmazásokat** a Felhasználói beállítások lapon **Igen**beállítással. Ha az alkalmazásregisztrációs beállítás **Nem,** a műveletet végző felhasználónak meg kell egyeznie a táblázatban meghatározottakszerint.

Ha nem tagja az előfizetés Active Directory-példányának, mielőtt hozzákerülaz előfizetés globális rendszergazdai szerepkörébe, vendégként kerül hozzáadásra. Ebben az esetben `You do not have permissions to create…` figyelmeztetést kap az Automatizálási fiók hozzáadása lapon. 

Ha a globális rendszergazdai szerepkör hozzárendelésekénél az előfizetés Active Directory-példányának `You do not have permissions to create…` tagja, figyelmeztetést is kaphat az Automatizálási fiók hozzáadása lapon. Ebben az esetben kérheti az előfizetés Active Directory-példányának eltávolítását, majd kérheti a újbóli hozzáfúvást, hogy teljes felhasználóvá váljon az Active Directoryban.

Annak ellenőrzése, hogy a hibaüzenetet okozó helyzetet orvosolták-e:

1. Az Azure Active Directory ablaktáblán az Azure Portalon válassza a **Felhasználók és csoportok**lehetőséget. 
2. Válassza az **Összes felhasználó lehetőséget.**
3. Válassza ki a nevét, majd válassza **a Profil lehetőséget.** 
4. Győződjön meg arról, hogy a felhasználó profiljában lévő **Felhasználó típus** attribútum értéke nem **Vendég**értékre van állítva.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>A klasszikus futtatás másként fiókok konfigurálásához szükséges engedélyek

A Klasszikus futtatás másként fiókok konfigurálásához vagy megújításához előfizetési szinten társrendszergazdai szerepkörsel kell rendelkeznie. Ha többet szeretne megtudni a klasszikus előfizetési engedélyekről, olvassa el az [Azure klasszikus előfizetési rendszergazdái című témakört.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

## <a name="creating-a-run-as-account-in-azure-portal"></a>Futtatási fiók létrehozása az Azure Portalon

Hajtsa végre az alábbi lépéseket az Azure Automation-fiók frissítéséhez az Azure Portalon. Hozza létre a Futtatás másként és a Klasszikus futtatás mint fiókokat külön-külön. Ha nem kell klasszikus erőforrásokat felügyelnie, egyszerűen létrehozhatja csak a futtató fiókot.

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.
2. Keresse meg és válassza az **Automation-fiókok lehetőséget.**
3. Az Automation-fiókok lapon válassza ki az Automation-fiókot a listából.
4. A bal oldali ablaktáblában válassza a **Futtatás fiókokként** lehetőséget a fiókbeállítások szakaszban.
5. Attól függően, hogy melyik fiókra van szüksége, válassza az **Azure-alapú futtató fiók** vagy a **Klasszikus Azure-alapú futtató fiók** lehetőséget. 
6. Az érdeklődési fióktól függően használja az **Azure Run As vagy** az Add Azure Classic Run As **Account** ablaktáblát. Az áttekintő információk áttekintése után kattintson a **Létrehozás gombra.**
6. Amíg az Azure létrehozza a futtató fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát. Egy szalagcím is megjelenik, amely jelzi, hogy a fiók létrehozása folyamatban van. A folyamat néhány percet is igénybe vehet.

## <a name="creating-a-run-as-account-using-powershell"></a>Futtatás másként fiók létrehozása a PowerShell használatával

Az alábbi lista a PowerShell-ben a Futtatás másként fiók létrehozásának követelményeit tartalmazza. Ezek a követelmények a Futtatás másként fiókok mindkét típusára vonatkoznak.

* Windows 10 vagy Windows Server 2016 az Azure Resource Manager 3.4.1-es és újabb moduljaival. A PowerShell-parancsfájl nem támogatja a Windows korábbi verzióit.
* Az Azure PowerShell 1.0-s és újabb verziói. Információk a PowerShell 1.0-s kiadásáról: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
* Automation-fiók, amely a `AutomationAccountName` és `ApplicationDisplayName` a paraméterek értékeként hivatkozik.
* A [Futtatás másként fiók konfigurálásához szükséges engedélyekben](#permissions)felsoroltakkal egyenértékű engedélyek.

A `ResourceGroupName`:és `SubscriptionId`a , a és a értékének beírásához, amely a PowerShell-parancsfájl szükséges paraméterei, hajtsa végre a következő lépéseket.

1. Az Azure Portalon válassza az **Automation-fiókok lehetőséget.**
1. Az Automation-fiókok lapon válassza ki az Automation-fiókot.
1. A Fiókbeállítások csoportban válassza a **Tulajdonságok lehetőséget.**
1. A Tulajdonságok lapon jegyezze fel a **NÉV**, **AZ ELŐFIZETÉS-azonosító**és az **ERŐFORRÁSCSOPORT** értékét. Ezek az értékek a `AutomationAccountName`, `SubscriptionId`, `ResourceGroupName` illetve a PowerShell-parancsfájl-paraméterek értékeinek felelnek meg.

   ![Automation-fiók tulajdonságai lap](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-parancsfájl a Futtatás másként fiók létrehozásához

Ez a szakasz egy PowerShell-parancsfájlt biztosít a Futtatás másként fiók létrehozásához. A parancsfájl több konfiguráció támogatását is tartalmazza.

* Futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Egy futtató fiók és egy klasszikus futtató fiók létrehozása a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvánnyal.
* Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával.

A parancsfájl több Azure Resource Manager-parancsmazmát használ az erőforrások létrehozásához. A parancsmagok és a szükséges engedélyek tekintetében olvassa el a [Futtatás másként beállításkonfigurálása című témakört.](#permissions-to-configure-run-as-accounts)

Mentse a parancsfájlt a számítógépre a **New-RunAsAccount.ps1**fájlnév vel.

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
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

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
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
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

>[!NOTE]
>`Add-AzAccount`és `Add-AzureRMAccount` a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)aliasai. Használhatja ezeket a parancsmagokat, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha nemrég létrehozott egy új Automation-fiókot.

### <a name="execute-the-powershell-script"></a>A PowerShell-parancsfájl végrehajtása

1. A számítógépén indítsa el a **Windows PowerShell** alkalmazást a **Kezdőlap** képernyőről emelt szintű felhasználói jogokkal.
1. A rendszergazda jogú parancssori rendszerhéjból nyissa meg a parancsfájlt tartalmazó mappát.
1. A parancsfájl végrehajtása a szükséges konfiguráció paraméterértékeivel.
1. Klasszikus futtatási mint fiók létrehozásakor a parancsfájl végrehajtása után töltse fel a nyilvános tanúsítványt (**.cer** fájlnév kiterjesztés) annak az előfizetésnek a felügyeleti tárolójába, amelyben az Automation-fiók létrejött.

A parancsfájl végrehajtása után a rendszer kéri, hogy hitelesítse magát az Azure-ral. Jelentkezzen be egy olyan fiókkal, amely az előfizetés-rendszergazdák szerepkör és az előfizetés társrendszergazdája.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Futtatás másként fiók létrehozása önaláírt tanúsítvánnyal

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Futtatás másként fiók és klasszikus futtatási mint fiók létrehozása önaláírt tanúsítvánnyal

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Futtatás másként fiók és klasszikus futtatásminta fiók létrehozása vállalati tanúsítvánnyal

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Ha klasszikus futtatási mint fiókot hozott létre vállalati nyilvános tanúsítvánnyal (**.cer** fájl), használja ezt a tanúsítványt. Lásd: [Felügyeleti API-tanúsítvány feltöltése az Azure Portalra.](../azure-api-management-certs.md)

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Futás másként fiók és klasszikus futtatási fiók létrehozása önaláírt tanúsítvánnyal az Azure Government felhőben

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Ha létrehozott egy Klasszikus futtatás másként fiókot önaláírt nyilvános tanúsítvánnyal (**.cer** fájl), a parancsfájl létrehozza és menti azt a számítógép ideiglenes fájlmappájába. Megtalálható a felhasználói profilban `%USERPROFILE%\AppData\Local\Temp`, amely a PowerShell-munkamenet végrehajtásához használt.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Futtatás másként vagy Klasszikus futtatás másként fiók törlése

Ez a szakasz azt ismerteti, hogyan lehet törölni a Futtatás másként vagy a Klasszikus futtatás másként fiókot. A művelet során a rendszer megőrzi az Automation-fiókot. A fiók törlése után újra létrehozhatja azt az Azure Portalon.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

2. A bal oldali ablaktáblában válassza a **Futtatás fiókokként** lehetőséget a fiókbeállítások szakaszban.

3. A Futtató fiókok tulajdonságlapján válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelyet törölni kíván. 

4. A kijelölt fiók Tulajdonságok ablaktábláján kattintson a **Törlés gombra.**

   ![Futtató fiók törlése](media/manage-runas-account/automation-account-delete-runas.png)

5. A fiók törlése során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

6. A törlés után újra létrehozhatja a fiókot a Futtató fiókok tulajdonságlapon az **Azure-alapú futtató fiók** lehetőség kiválasztásával.

   ![Automation futtató fiók újbóli létrehozása](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Önaláírt tanúsítvány megújítása

A Futtatás másként fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számított egy évvel lejár. A Futtatás másként fiók lejárta előtt valamikor meg kell újítania a tanúsítványt. A lejárat előtt bármikor megújíthatja. 

Az önaláírt tanúsítvány megújításakor az aktuális érvényes tanúsítvány megmarad annak érdekében, hogy a várólistára helyezett vagy aktívan futó runbookok, amelyek a Futtatás másként fiókkal hitelesítve vannak, ne legyenek negatívan érintettek. A tanúsítvány a lejárati dátumáig érvényes marad.

>[!NOTE]
>Ha úgy gondolja, hogy a Futtatás másként fiók biztonsága sérült, törölheti és újra létrehozhatja az önaláírt tanúsítványt.

>[!NOTE]
>Ha úgy állította be a Futtatás másként fiókot, hogy a vállalati hitelesítésszolgáltató által kiállított tanúsítványt használjon, és az önaláírt tanúsítványbeállítás megújítására használja a lehetőséget, a vállalati tanúsítványt egy önaláírt tanúsítvány váltja fel.

Az önaláírt tanúsítvány megújításához kövesse az alábbi lépéseket.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

1. A fiókbeállítások szakaszban válassza a **Futtatás fiókokként** lehetőséget.

    ![Az Automation-fiók tulajdonságpanelje](media/manage-runas-account/automation-account-properties-pane.png)

1. A Futtatás fiókokként tulajdonságlapon válassza a Futtatás másként fiókot vagy a Klasszikus futtatás mint fiókot, amelyhez meg szeretné újítani a tanúsítványt.

1. A kijelölt fiók tulajdonságok ablaktábláján kattintson a **Tanúsítvány megújítása gombra.**

    ![Futtató fiók tanúsítványának megújítása](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Automatikus tanúsítványmegújítás beállítása automatizálási runbookkal

A tanúsítványok automatikus megújításához használhatja az automatizálási runbookot. Ez a parancsfájl a [GitHubon](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) lehetővé teszi ezt a funkciót az Automation-fiókban.

>[!NOTE]
>A parancsfájl végrehajtásához globális rendszergazdának vagy vállalati rendszergazdának kell lennie az Azure AD-ben.

Ez a parancsfájl heti ütemezést hoz létre a Futtatás mint fióktanúsítványok megújításához. Hozzáadja **az Update-AutomationRunAsCredential** runbookot az Automation-fiókhoz. A Runbook-kódot a GitHubon tekintheti meg az [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1)parancsfájlban. A fájlban lévő PowerShell-kód segítségével szükség szerint manuálisan megújíthatja a tanúsítványokat.

A megújítási folyamat azonnali teszteléséhez kövesse az alábbi lépéseket.

1. Az **Update-AutomationRunAsCredential** runbook szerkesztése és a 122-es sorhoz egy megjegyzéskarakter (#) az **Exit(1)** parancs elé helyezhető.

   ```powershell
   #Exit(1)
   ```

2. Tegye közzé a runbookot.
3. Indítsa el a runbookot.
4. Ellenőrizze a sikeres megújítást a következő kóddal:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Kimenet:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. A teszt után szerkesztheti a runbookot, és távolítsa el az 1.
6. Tegye közzé a runbookot.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>A Futtatás másként fiók engedélyeinek korlátozása

Az Automation azure-beli erőforrások kal való célzásának szabályozásához futtathatja az [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) parancsfájlt. Ez a parancsfájl módosítja a meglévő Futtatás fiókként szolgáltatásegyszerű tegyéni szerepkör-definíció létrehozásához és használatához. A szerepkör a Key [Vault](https://docs.microsoft.com/azure/key-vault/)kivételével minden erőforráshoz rendelkezik engedéllyel.

>[!IMPORTANT]
>Az **Update-AutomationRunAsAccountRoleAssignments.ps1** parancsfájl futtatása után a Key Vaultot a Futtatás másként fiókok használatával elérő runbookok már nem működnek. A parancsfájl futtatása előtt tekintse át a runbookok a fiókjában az Azure Key Vault hívások. Ahhoz, hogy hozzáférést biztosítson a Key Vaulthoz az Azure Automation-runbookokból, hozzá kell [adnia a Futtatás másként fiókot a Key Vault engedélyeihez.](#add-permissions-to-key-vault)

Ha korlátoznia kell, tovább, amit a Futtatás egyszerű szolgáltatástehető, `NotActions` hozzáadhat más erőforrástípusokat az egyéni szerepkör-definíció eleméhez. A következő példa korlátozza `Microsoft.Compute/*`a hozzáférést a hoz. Ha hozzáadja ezt `NotActions` az erőforrástípust a szerepkör-definícióhoz, a szerepkör nem fog tudni hozzáférni egyetlen számítási erőforráshoz sem. Ha többet szeretne megtudni a szerepkör-definíciókról, [olvassa el az Azure-erőforrások szerepkör-definícióinak ismertetése](../role-based-access-control/role-definitions.md)című témakört.

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Meghatározhatja, hogy a Futtatás másként fiók által használt egyszerű szolgáltatás a közreműködői szerepkör-definícióban vagy egy egyéniben található-e. 

1. Nyissa meg az Automation-fiókot, és válassza a **Futtatás fiókokként** lehetőséget a fiókbeállítások szakaszban.
2. Válassza az **Azure Futtatás fiókként lehetőséget.** 
3. Válassza **a Szerepkör lehetőséget** a használt szerepkör-definíció megkereséséhez.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Azt is meghatározhatja, hogy a Futtatás másként fiókok több előfizetéshez vagy Automation-fiókokhoz használt szerepkör-definíciót is meghatározhatja. Ehhez használja a [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) parancsfájlt a PowerShell-galériában.

### <a name="add-permissions-to-key-vault"></a>Engedélyek hozzáadása a Key Vaulthoz

Engedélyezheti, hogy az Azure Automation ellenőrizze, hogy a Key Vault és a Futtatás mint fiók egyszerű szolgáltatása egyéni szerepkör-definíciót használ-e. A következőket kell tennie:

* Engedélyek megadása a Key Vault számára.
* Állítsa be a hozzáférési szabályzatot.

Használhatja az [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) parancsfájlt a PowerShell-galériában, hogy a Futtatás másként fiók engedélyeket adjon a Key Vaultnak. A Key Vault engedélyeivel kapcsolatos további részletekért tekintse meg az alkalmazások hozzáférésének megadása [a key vaulthoz](../key-vault/general/group-permissions-for-apps.md) való hozzáférést.

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>A Futtatás másként fiókok helytelen konfigurációs problémáinak megoldása

Előfordulhat, hogy a Futtatás másként vagy a Klasszikus futtatás másként fiókhoz szükséges egyes konfigurációs elemeket törölték vagy helytelenül hozták létre a kezdeti telepítés során. A helytelen konfiguráció lehetséges példányai a következők:

* Tanúsítványobjektum
* Kapcsolatobjektum
* A közreműködői szerepkörből eltávolított futtatás mint fiók
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az ilyen helytelen konfigurációs példányok esetében az Automation-fiók `Incomplete` észleli a módosításokat, és megjeleníti a fiók Futtatás a fiókok tulajdonságai ablaktáblában az állapotát.

![Hiányos futtatófiók-konfigurációs állapot](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Ha a Futtatás másként fiókot választja, a fiók tulajdonságai ablaktábla a következő hibaüzenetet jeleníti meg:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

A futtató fiókkal kapcsolatos hasonló problémákat gyorsan elháríthatja a fiók törlésével és ismételt létrehozásával.

## <a name="next-steps"></a>További lépések

* A szolgáltatásnévi szolgáltatásokról további információt az [Alkalmazásobjektumok és egyszerű szolgáltatásobjektumok című témakörben talál.](../active-directory/develop/app-objects-and-service-principals.md)
* A tanúsítványokról és az Azure-szolgáltatásokról az Azure Cloud Services tanúsítványok – áttekintéscímű témakörben olvashat [bővebben.](../cloud-services/cloud-services-certs-create.md)
