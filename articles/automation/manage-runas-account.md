---
title: Azure Automation futtató fiókok kezelése
description: Ez a cikk bemutatja, hogyan kezelheti a futtató fiókokat a PowerShell-lel vagy a portálról.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 68d04603ba9f0633bfa55598790b790055384fdb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648156"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation futtató fiókok kezelése

A Azure Automation futtató fiókok az Azure-parancsmagok használatával biztosítanak hitelesítést az Azure-erőforrások kezeléséhez. A futtató fiók létrehozásakor létrehoz egy új egyszerű szolgáltatásnevet a Azure Active Directory (AD) szolgáltatásban, és hozzárendeli a közreműködői szerepkört a felhasználóhoz az előfizetés szintjén.

## <a name="types-of-run-as-accounts"></a>A futtató fiókok típusai

Azure Automation két típusú futtató fiókot használ:

* Azure-beli futtató fiók
* Klasszikus Azure-beli futtató fiók

>[!NOTE]
>A Azure Cloud Solution Provider (CSP) előfizetések csak a Azure Resource Manager modellt támogatják. A nem Azure Resource Manager szolgáltatások nem érhetők el a programban. Ha CSP-előfizetést használ, a klasszikus Azure-beli futtató fiók nem jön létre, de létrejön az Azure-beli futtató fiók. A CSP-előfizetésekkel kapcsolatos további tudnivalókért tekintse meg a [CSP-előfizetésekben elérhető szolgáltatások](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)című témakört.

A futtató fiókhoz tartozó egyszerű szolgáltatásnév nem rendelkezik az Azure AD alapértelmezett olvasási engedélyeivel. Ha engedélyeket szeretne adni az Azure AD olvasásához vagy kezeléséhez, meg kell adnia az engedélyeket az egyszerű szolgáltatáshoz az **API-engedélyek**alatt. További információ: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Futtató fiók

A futtató fiók a [Resource Manager üzembe helyezési modell](../azure-resource-manager/management/deployment-models.md) erőforrásait kezeli. A következő feladatokat hajtja végre.

* Létrehoz egy önaláírt tanúsítvánnyal ellátott Azure AD-alkalmazást, továbbá létrehoz egy egyszerűszolgáltatás-fiókot az Azure AD-ben lévő alkalmazáshoz, és hozzárendeli a közreműködői szerepkört a jelenlegi előfizetésben lévő fiókhoz. A tanúsítvány beállítását a tulajdonosra vagy bármely más szerepkörre módosíthatja. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
  
* Létrehoz egy nevű Automation-tanúsítványt `AzureRunAsCertificate` a megadott Automation-fiókban. A tanúsítvány objektuma tartalmazza az Azure AD-alkalmazás által használt tanúsítvány titkos kulcsát.
  
* Létrehoz egy nevű Automation-összekötő eszközt `AzureRunAsConnection` a megadott Automation-fiókban. A szolgáltatás tartalmazza az alkalmazás AZONOSÍTÓját, a bérlő AZONOSÍTÓját, az előfizetés AZONOSÍTÓját és a tanúsítvány ujjlenyomatát.

### <a name="azure-classic-run-as-account"></a>Klasszikus Azure-futtatófiók

A klasszikus Azure-beli futtató fiók kezeli a [klasszikus üzembe helyezési modell](../azure-resource-manager/management/deployment-models.md) erőforrásait. Ilyen típusú fiók létrehozásához vagy megújításához az előfizetés egyik társ-rendszergazda tagjának kell lennie.

A klasszikus Azure-beli futtató fiók a következő feladatokat hajtja végre.

  * Létrehoz egy felügyeleti tanúsítványt az előfizetésben.

  * Létrehoz egy nevű Automation-tanúsítványt `AzureClassicRunAsCertificate` a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.

  * Létrehoz egy nevű Automation-összekötő eszközt `AzureClassicRunAsConnection` a megadott Automation-fiókban. A szolgáltatás tartalmazza az előfizetés nevét, az előfizetés AZONOSÍTÓját és a tanúsítvány-eszköz nevét.

>[!NOTE]
>A klasszikus Azure-beli futtató fiók alapértelmezés szerint nem jön létre Automation-fiók létrehozásakor. Ezt a fiókot külön hozza létre a cikk későbbi részében ismertetett lépéseket követve.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Futtató fiók engedélyei

Ez a szakasz a normál futtató fiókok és a klasszikus futtató fiókok engedélyeit határozza meg.

### <a name="permissions-to-configure-run-as-accounts"></a>A futtató fiókok konfigurálásához szükséges engedélyek

Futtató fiók létrehozásához vagy frissítéséhez konkrét jogosultságokkal és engedélyekkel kell rendelkeznie. Azure Active Directory és egy előfizetéshez tartozó egyik alkalmazás rendszergazdája elvégezheti az összes feladatot. Az alábbi táblázat a feladatok elkülönítését mutatja be, a megfelelő parancsmagot és engedélyeket, valamint a szükséges engedélyek listáját:

|Tevékenység|Parancsmag  |Minimális engedélyek  |Az engedélyek beállítása|
|---|---------|---------|---|
|Azure AD-alkalmazás létrehozása|[Új – AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Alkalmazás fejlesztői szerepköre<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja > |
|Adjon hozzá egy hitelesítő adatot az alkalmazáshoz.|[Új – AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja >|
|Azure AD-szolgáltatásnév létrehozása és beszerzése|[Új – AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Alkalmazás-rendszergazda vagy globális rendszergazda<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Az Azure AD > alkalmazásbeli regisztrációjának kezdőlapja >|
|A RBAC szerepkör kiosztása vagy beolvasása a megadott rendszerbiztonsági tag számára|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Felhasználói hozzáférés a rendszergazdához vagy a tulajdonoshoz, vagy a következő engedélyekkel rendelkezik:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Előfizetés](../role-based-access-control/role-assignments-portal.md)</br>Kezdőlap > előfizetések > \< előfizetés neve \> – Access Control (iam)|
|Automation-tanúsítvány létrehozása vagy eltávolítása|[Új – AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Közreműködő az erőforráscsoporthoz         |Automation-fiók erőforráscsoport|
|Automation-kapcsolatok létrehozása vagy eltávolítása|[Új – AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Közreműködő az erőforráscsoporthoz |Automation-fiók erőforráscsoport|

<sup>1</sup> az Azure ad-bérlőben nem rendszergazda felhasználók [regisztrálhatnak ad-alkalmazásokat](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) , ha az Azure ad-bérlő **felhasználóinak az alkalmazások regisztrálása** lehetőség a felhasználói beállítások lapon az **Igen**értékre van állítva. Ha az alkalmazás regisztrációs beállítása **nem**, a műveletet végrehajtó felhasználónak a táblázatban megadott módon kell szerepelnie.

Ha nem tagja az előfizetés Active Directory példányának, mielőtt hozzáadja az előfizetés globális rendszergazdai szerepköréhez, vendégként lesz hozzáadva. Ebben az esetben `You do not have permissions to create…` figyelmeztetést kap az Automation-fiók hozzáadása oldalon. 

Ha az előfizetés Active Directory példányának tagja a globális rendszergazdai szerepkör hozzárendelésekor, akkor `You do not have permissions to create…` az Automation-fiók hozzáadása oldalon is megjelenik egy figyelmeztetés. Ebben az esetben kérheti az előfizetés Active Directory példányának eltávolítását, majd újból felveszi a kérést, hogy teljes jogú felhasználó legyen a Active Directoryban.

Annak ellenőrzése, hogy a hibaüzenetet előállító helyzet kijavítása megtörtént-e:

1. A Azure Portal Azure Active Directory ablaktábláján válassza a **felhasználók és csoportok**lehetőséget. 
2. Válassza **a minden felhasználó**lehetőséget.
3. Válassza ki a nevét, majd válassza a **profil**lehetőséget. 
4. Győződjön meg arról, hogy a felhasználó profiljában a **felhasználó típusa** attribútum értéke nincs beállítva **vendégként**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>A klasszikus futtató fiókok konfigurálásának engedélyei

Klasszikus futtató fiókok konfigurálásához vagy megújításához az előfizetés szintjén kell lennie a társ-rendszergazda szerepkörnek. A klasszikus előfizetési engedélyekkel kapcsolatos további tudnivalókért tekintse meg a [klasszikus Azure-előfizetés rendszergazdái](../role-based-access-control/classic-administrators.md#add-a-co-administrator)című témakört.

## <a name="creating-a-run-as-account-in-azure-portal"></a>Futtató fiók létrehozása a Azure Portalban

A következő lépésekkel frissítheti Azure Automation-fiókját a Azure Portalban. Egyenként hozza létre a futtató és a klasszikus futtató fiókokat. Ha nem kell klasszikus erőforrásokat felügyelnie, egyszerűen létrehozhatja csak a futtató fiókot.

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.
2. Keresse meg és válassza ki az **Automation-fiókokat**.
3. Az Automation-fiókok lapon válassza ki az Automation-fiókját a listából.
4. A bal oldali ablaktáblán válassza a **futtató fiókok** lehetőséget a Fiókbeállítások szakaszban.
5. Attól függően, hogy melyik fiókra van szüksége, válassza az **Azure-alapú futtató fiók** vagy a **Klasszikus Azure-alapú futtató fiók** lehetőséget. 
6. Az érdeklődési fióktól függően használja az Azure-beli **futtató fiók hozzáadása** vagy a klasszikus Azure-beli **futtató fiók hozzáadása** panelt. Az áttekintő információk áttekintése után kattintson a **Létrehozás**gombra.
6. Amíg az Azure létrehozza a futtató fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát. Megjelenik egy szalagcím is, amely azt jelzi, hogy a fiók létrehozása folyamatban van. A folyamat eltarthat néhány percig.

## <a name="creating-a-run-as-account-using-powershell"></a>Futtató fiók létrehozása a PowerShell használatával

A következő lista a futtató fiók PowerShellben való létrehozásához szükséges követelményeket ismerteti. Ezek a követelmények mindkét típusú futtató fiókra érvényesek.

* Windows 10 vagy Windows Server 2016, Azure Resource Manager modul 3.4.1-es és újabb verzióival. A PowerShell-parancsfájl nem támogatja a Windows korábbi verzióit.
* Az Azure PowerShell 1.0-s és újabb verziói. Információk a PowerShell 1.0-s kiadásáról: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
* Egy Automation-fiók, amelyre a és a paraméterek értékeként hivatkozunk `AutomationAccountName` `ApplicationDisplayName` .
* A [futtató fiókok konfigurálásához szükséges engedélyekkel](#permissions)egyenértékű engedélyek.

A következő lépések végrehajtásával lekérheti a, a és a értékeit a `SubscriptionId` `ResourceGroupName` PowerShell- `AutomationAccountName` parancsfájlhoz szükséges paraméterekkel.

1. A Azure Portal válassza az **Automation-fiókok**elemet.
1. Az Automation-fiókok lapon válassza ki az Automation-fiókját.
1. A Fiókbeállítások szakaszban válassza a **Tulajdonságok**lehetőséget.
1. Jegyezze fel a **Name**, az **előfizetés-azonosító**és az **erőforráscsoport** értékét a Tulajdonságok lapon. Ezek az értékek a, a és a `AutomationAccountName` `SubscriptionId` PowerShell-parancsfájl paramétereinek értékeinek felelnek meg `ResourceGroupName` .

   ![Automation-fiók tulajdonságai lap](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-parancsfájl futtató fiók létrehozásához

Ez a szakasz egy PowerShell-parancsfájlt tartalmaz futtató fiók létrehozásához. A parancsfájl több konfigurációt is támogat.

* Futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Egy futtató fiók és egy klasszikus futtató fiók létrehozása a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvánnyal.
* Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával.

A szkript több Azure Resource Manager parancsmagot használ az erőforrások létrehozásához. A parancsmagokhoz és a szükséges engedélyekhez lásd: [engedélyek a futtató fiókok konfigurálásához](#permissions-to-configure-run-as-accounts).

Mentse a parancsfájlt a számítógépen a **New-RunAsAccount. ps1**fájlnév használatával.

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
>`Add-AzAccount`a és `Add-AzureRMAccount` a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)aliasa. Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot.

### <a name="execute-the-powershell-script"></a>A PowerShell-parancsfájl végrehajtása

1. A számítógépén indítsa el a **Windows PowerShell** alkalmazást a **Kezdőlap** képernyőről emelt szintű felhasználói jogokkal.
1. A rendszergazda jogú parancssorból nyissa meg a parancsfájlt tartalmazó mappát.
1. Futtassa a szkriptet a szükséges konfiguráció paramétereinek használatával.
1. Klasszikus futtató fiók létrehozásakor a parancsfájl végrehajtása után töltse fel a nyilvános tanúsítványt (**. cer** filename kiterjesztését) a felügyeleti tárolóba azon előfizetés esetében, amelyben az Automation-fiókot létrehozták.

A parancsfájl végrehajtása után a rendszer kéri, hogy végezzen hitelesítést az Azure-ban. Jelentkezzen be egy olyan fiókkal, amely tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés közös rendszergazdája.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Futtató fiók létrehozása önaláírt tanúsítvány használatával

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Futtató fiók és klasszikus futtató fiók létrehozása vállalati tanúsítvány használatával

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Ha a klasszikus futtató fiókot vállalati nyilvános tanúsítvánnyal (**. cer** fájllal) hozta létre, használja ezt a tanúsítványt. Lásd: [felügyeleti API-tanúsítvány feltöltése a Azure Portal](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával a Azure Government-felhőben

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Ha létrehozta a klasszikus futtató fiókot egy önaláírt nyilvános tanúsítvánnyal (**. cer** fájllal), a parancsfájl létrehozza és menti a számítógép ideiglenes fájlok mappájába. Ez a felhasználói profilban található `%USERPROFILE%\AppData\Local\Temp` , amelyet a PowerShell-munkamenet végrehajtásához használt.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Futtató vagy klasszikus futtató fiók törlése

Ez a szakasz azt ismerteti, hogyan lehet törölni egy futtató vagy klasszikus futtató fiókot. A művelet során a rendszer megőrzi az Automation-fiókot. A fiók törlése után újból létrehozhatja azt a Azure Portalban.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

2. A bal oldali ablaktáblán válassza a **futtató fiókok** lehetőséget a Fiókbeállítások szakaszban.

3. A Futtató fiókok tulajdonságlapján válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelyet törölni kíván. 

4. A kiválasztott fiók Tulajdonságok paneljén kattintson a **Törlés**elemre.

   ![Futtató fiók törlése](media/manage-runas-account/automation-account-delete-runas.png)

5. A fiók törlése során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

6. A törlés után újra létrehozhatja a fiókot a Futtató fiókok tulajdonságlapon az **Azure-alapú futtató fiók** lehetőség kiválasztásával.

   ![Automation futtató fiók újbóli létrehozása](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Önaláírt tanúsítvány megújítása

A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy évig lejár. A futtató fiók lejárata előtt egy bizonyos ponton meg kell újítania a tanúsítványt. Bármikor megújíthatja, mielőtt lejár. 

Az önaláírt tanúsítvány megújításakor a rendszer megőrzi a jelenlegi érvényes tanúsítványt, hogy biztosítsa, hogy a várólistára helyezett vagy aktívan futó runbookok és a futtató fiókkal végzett hitelesítés ne legyen negatív hatással. A tanúsítvány a lejárati dátumáig érvényes marad.

>[!NOTE]
>Ha úgy véli, hogy a futtató fiók biztonsága sérül, törölheti és újból létrehozhatja az önaláírt tanúsítványt.

>[!NOTE]
>Ha úgy állította be a futtató fiókot, hogy a vállalati hitelesítésszolgáltató által kiadott tanúsítványt használja, és az önaláírt tanúsítvány megújítására vonatkozó beállítást használja, a vállalati tanúsítvány egy önaláírt tanúsítvány helyett szerepel.

Az önaláírt tanúsítvány megújításához kövesse az alábbi lépéseket.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

1. Válassza a fiók beállításai szakaszban a **futtató fiókok** lehetőséget.

    ![Az Automation-fiók tulajdonságpanelje](media/manage-runas-account/automation-account-properties-pane.png)

1. A futtató fiókok tulajdonságai lapon válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelynek a tanúsítványát meg szeretné újítani.

1. A kiválasztott fiók Tulajdonságok paneljén kattintson a **tanúsítvány megújítása**elemre.

    ![Futtató fiók tanúsítványának megújítása](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Automatikus tanúsítvány-megújítás beállítása automatizálási runbook

A tanúsítványok automatikus megújításához használhat Automation-runbook. Ez a szkript a [githubon](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) engedélyezi ezt a funkciót az Automation-fiókjában.

>[!NOTE]
>A szkript végrehajtásához globális rendszergazdai vagy vállalati rendszergazdaként kell lennie az Azure AD-ben.

Ez a parancsfájl heti ütemtervet hoz létre a futtató fiók tanúsítványainak megújításához. Felvesz egy **Update-AutomationRunAsCredential** Runbook az Automation-fiókjába. A runbook kódját a GitHubon, a [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1)parancsfájlban tekintheti meg. A fájl PowerShell-kódjával manuálisan is megújíthatja a tanúsítványokat, szükség szerint.

A megújítási folyamat azonnali teszteléséhez kövesse az alábbi lépéseket.

1. Szerkessze az **Update-AutomationRunAsCredential** runbook, és helyezzen el egy comment karaktert (#) a 122. sorban a **kilépési (1)** parancs előtt.

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
    Kimenet:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. A teszt után szerkessze a runbook, és távolítsa el az 1. lépésben hozzáadott Megjegyzés karaktert.
6. Tegye közzé a runbook.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Futtató fiók engedélyeinek korlátozása

Az Azure-beli erőforrásokhoz való automatizálás célzásának szabályozásához futtathatja a [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) parancsfájlt. Ez a parancsfájl módosítja a meglévő futtató fiók egyszerű szolgáltatását egy egyéni szerepkör-definíció létrehozásához és használatához. A szerepkör a [Key Vault](https://docs.microsoft.com/azure/key-vault/)kivételével minden erőforráshoz rendelkezik engedéllyel.

>[!IMPORTANT]
>A **Update-AutomationRunAsAccountRoleAssignments. ps1** parancsfájl futtatása után a Runbookok a futtató fiókok használatával Key Vault való hozzáférése már nem működik. A szkript futtatása előtt tekintse át a runbookok a fiókjában, hogy meghívja a Azure Key Vault. A Key Vault Azure Automation runbookok való elérésének engedélyezéséhez hozzá kell [adnia a futtató fiókot Key Vault engedélyeihez](#add-permissions-to-key-vault).

Ha korlátoznia kell a szolgáltatást, a Futtatás mint szolgáltatás egyszerű funkciója további erőforrástípusok hozzáadásával is kiegészíthető az `NotActions` Egyéni szerepkör-definíció eleméhez. Az alábbi példa korlátozza a hozzáférését a következőhöz: `Microsoft.Compute/*` . Ha ezt az erőforrástípust hozzáadja `NotActions` a szerepkör-definícióhoz, a szerepkör nem fog tudni hozzáférni a számítási erőforrásokhoz. További információ a szerepkör-definíciókkal kapcsolatban: [Az Azure-erőforrások szerepkör-definícióinak megismerése](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Megadhatja, hogy a futtató fiók által használt egyszerű szolgáltatásnév a közreműködő szerepkör definíciójában vagy egy egyéniben legyen. 

1. Nyissa meg az Automation-fiókját, és válassza a fiók beállításai szakaszban a **futtató fiókok** elemet.
2. Válassza az Azure-beli **futtató fiók**lehetőséget. 
3. Válassza ki a **szerepkört** a használatban lévő szerepkör-definíció megkereséséhez.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

A futtató fiókok által használt szerepkör-definíciót több előfizetés vagy Automation-fiók esetében is meghatározhatja. Ezt a PowerShell-galéria [Check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) parancsfájljának használatával teheti meg.

### <a name="add-permissions-to-key-vault"></a>Engedélyek hozzáadása a Key Vaulthoz

Engedélyezheti Azure Automation annak ellenőrzését, hogy a Key Vault és a futtató fiók egyszerű szolgáltatása egyéni szerepkör-definíciót használ-e. A következőket kell tennie:

* Engedélyek megadása Key Vault számára.
* Adja meg a hozzáférési házirendet.

A PowerShell-galéria [extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) parancsfájllal engedélyezheti a futtató fiók számára a Key Vault. A Key Vault engedélyeinek beállításával kapcsolatos további információkért lásd: az [alkalmazások hozzáférésének biztosítása a Key vaulthoz](../key-vault/general/group-permissions-for-apps.md) .

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Futtató fiókok konfigurációs problémáinak elhárítása

Előfordulhat, hogy a futtató vagy klasszikus futtató fiókhoz szükséges konfigurációs elemeket törölték vagy helytelenül hozták létre a kezdeti beállítás során. A helytelen konfiguráció lehetséges példányai a következők:

* Tanúsítványobjektum
* Kapcsolatobjektum
* A futtató fiók el lett távolítva a közreműködő szerepkörből
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az ilyen hibás konfigurációs példányok esetében az Automation-fiók észleli a módosításokat, és *hiányos* állapotot jelenít meg a fiók futtató fiókok tulajdonságok paneljén.

![Hiányos futtatófiók-konfigurációs állapot](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Ha kiválasztja a futtató fiókot, a fiók tulajdonságai ablaktábla a következő hibaüzenetet jeleníti meg:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

A futtató fiókkal kapcsolatos hasonló problémákat gyorsan elháríthatja a fiók törlésével és ismételt létrehozásával.

## <a name="next-steps"></a>További lépések

* Az egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [alkalmazás-objektumok és egyszerű szolgáltatások objektumai](../active-directory/develop/app-objects-and-service-principals.md).
* A tanúsítványokkal és az Azure-szolgáltatásokkal kapcsolatos további információkért lásd: [Az Azure-beli tanúsítványok áttekintése Cloud Services](../cloud-services/cloud-services-certs-create.md).
