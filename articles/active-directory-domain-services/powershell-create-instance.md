---
title: Azure Active Directory Domain Services engedélyezése a PowerShell használatával | Microsoft Docs
description: Azure Active Directory Domain Services engedélyezése a PowerShell használatával
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: iainfou
ms.openlocfilehash: c6572ab8bc2a10039f327233f983c2e822fba3b0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617219"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Azure Active Directory Domain Services engedélyezése a PowerShell használatával
Ez a cikk bemutatja, hogyan engedélyezheti Azure Active Directory (AD) tartományi szolgáltatásokat a PowerShell használatával.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>1\. feladat: A szükséges PowerShell-modulok telepítése

### <a name="install-and-configure-azure-ad-powershell"></a>Az Azure AD PowerShell telepítése és konfigurálása
A cikk utasításait követve [telepítse az Azure ad PowerShell-modult, és kapcsolódjon az Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)-hez.

### <a name="install-and-configure-azure-powershell"></a>Az Azure PowerShell telepítése és konfigurálása
A cikk utasításait követve [telepítse a Azure PowerShell modult, és kapcsolódjon az Azure-](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)előfizetéséhez.


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>2\. feladat: A szükséges egyszerű szolgáltatásnév létrehozása az Azure AD-címtárban
Írja be a következő PowerShell-parancsot az Azure AD-címtárban Azure AD Domain Serviceshoz szükséges egyszerű szolgáltatásnév létrehozásához.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>3\. feladat: Az "HRE DC rendszergazdák" csoport létrehozása és konfigurálása
A következő feladat az a rendszergazda csoport létrehozása, amelyet a rendszer a felügyelt tartomány felügyeleti feladatainak delegálására fog használni.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Most, hogy létrehozta a csoportot, adjon hozzá néhány felhasználót a csoporthoz.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>4\. feladat: A Azure AD Domain Services erőforrás-szolgáltató regisztrálása
A Azure AD Domain Services erőforrás-szolgáltatójának regisztrálásához írja be a következő PowerShell-parancsot:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>5\. feladat: Hozzon létre egy erőforráscsoportot
Erőforráscsoport létrehozásához írja be a következő PowerShell-parancsot:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Ebben az erőforráscsoporthoz a virtuális hálózatot és a Azure AD Domain Services felügyelt tartományt is létrehozhatja.


## <a name="task-6-create-and-configure-the-virtual-network"></a>6\. feladat: A virtuális hálózat létrehozása és konfigurálása
Most hozza létre a virtuális hálózatot, amelyben engedélyezte Azure AD Domain Services. Győződjön meg arról, hogy létrehoz egy dedikált alhálózatot a Azure AD Domain Serviceshoz. Ne helyezzen üzembe munkaterhelési virtuális gépeket ebbe a dedikált alhálózatba.

A következő PowerShell-parancsok beírásával hozzon létre egy dedikált alhálózattal rendelkező virtuális hálózatot Azure AD Domain Services számára.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>7\. feladat: A Azure AD Domain Services felügyelt tartomány kiépítése
Írja be a következő PowerShell-parancsot a címtár Azure AD Domain Servicesának engedélyezéséhez:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **A felügyelt tartomány kiépítés után ne feledkezzen meg a további konfigurációs lépésekről.**
> A felügyelt tartomány üzembe helyezése után továbbra is végre kell hajtania a következő feladatokat:
> * A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez. A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
> * Hozza létre a szükséges hálózati biztonsági csoport szabályait a felügyelt tartomány bejövő forgalmának korlátozásához. A hálózati biztonsági csoport szabályainak létrehozásához válassza ki az Azure AD DS felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan létrehozza a megfelelő hálózati biztonsági csoportra vonatkozó szabályokat.
> * A **[Jelszó-szinkronizálás engedélyezése Azure ad domain Servicesre](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , így a végfelhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be a felügyelt tartományba.

## <a name="powershell-script"></a>PowerShell-szkript
A cikkben felsorolt feladatok végrehajtásához használt PowerShell-parancsfájl alább látható. Másolja a szkriptet, és mentse egy ". ps1" kiterjesztésű fájlba. Futtassa a szkriptet a PowerShellben vagy a PowerShell integrált parancsfájlkezelési környezet (ISE) használatával.

> [!NOTE]
> **A parancsfájl futtatásához szükséges engedélyek** A Azure AD Domain Services engedélyezéséhez az Azure AD-címtár globális rendszergazdájának kell lennie. Emellett legalább "közreműködői" jogosultsággal kell rendelkeznie azon a virtuális hálózaton, amelyben a Azure AD Domain Services engedélyezhető.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **A felügyelt tartomány kiépítés után ne feledkezzen meg a további konfigurációs lépésekről.**
> A felügyelt tartomány üzembe helyezése után továbbra is végre kell hajtania a következő feladatokat:
> * A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez. A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
> * Hozza létre a szükséges hálózati biztonsági csoport szabályait a felügyelt tartomány bejövő forgalmának korlátozásához. A hálózati biztonsági csoport szabályainak létrehozásához válassza ki az Azure AD DS felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan létrehozza a megfelelő hálózati biztonsági csoportra vonatkozó szabályokat.
> * A **[Jelszó-szinkronizálás engedélyezése Azure ad domain Servicesre](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , így a végfelhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be a felügyelt tartományba.

## <a name="next-steps"></a>További lépések
A felügyelt tartomány létrehozása után végezze el a következő konfigurációs feladatokat, hogy használhassa a felügyelt tartományt:

* [A DNS-kiszolgáló beállításainak frissítése a virtuális hálózathoz a felügyelt tartományra való Rámutatás céljából](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
* [Jelszó-szinkronizálás engedélyezése a felügyelt tartományhoz](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
