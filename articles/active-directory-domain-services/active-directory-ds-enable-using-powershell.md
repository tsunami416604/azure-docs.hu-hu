---
title: Azure Active Directory tartományi szolgáltatások PowerShell-lel engedélyezze |} Microsoft Docs
description: Engedélyezze az Azure Active Directory tartományi szolgáltatások PowerShell használatával
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: 386157360e8349c3df3b7351ae25b34ae9bdcc4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588505"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Engedélyezze az Azure Active Directory tartományi szolgáltatások PowerShell használatával
Ez a cikk bemutatja, hogyan engedélyezze az Azure Active Directory (AD) tartományi szolgáltatásokban a PowerShell használatával.

## <a name="task-1-install-the-required-powershell-modules"></a>1. feladat: A szükséges PowerShell-modulok telepítése

### <a name="install-and-configure-azure-ad-powershell"></a>Azure AD PowerShell telepítése és konfigurálása
Kövesse a cikkben [telepítse az Azure AD PowerShell modult, és az Azure AD connect](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Az Azure PowerShell telepítése és konfigurálása
Kövesse a cikkben [telepítse az Azure PowerShell-modult, és csatlakozzon az Azure-előfizetéshez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>2. feladat: A szükséges szolgáltatásnevet létrehozni az az Azure AD-címtár
Írja be az Azure AD tartományi szolgáltatásokat az Azure AD-címtár számára szükséges egyszerű szolgáltatás létrehozása a következő PowerShell-parancsot.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>3. feladat: Hozzon létre és konfigurálja a "AAD DC rendszergazdák" csoportot
A következő feladathoz, ha a felügyeleti csoport felügyeleti feladatokat a felügyelt tartomány delegálása használandó.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Most, hogy a csoport létrehozott, a felhasználók több felvétele a csoportba.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>4. feladat: Az Azure AD tartományi szolgáltatások erőforrás-szolgáltató regisztrálása
Írja be a következő PowerShell-parancsot az Azure AD tartományi szolgáltatásokhoz erőforrás-szolgáltató regisztrálása:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>5. feladat: Hozzon létre egy erőforráscsoportot
Írja be a következő PowerShell-parancs futtatásával hozzon létre egy erőforráscsoportot:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

A virtuális hálózat és az Azure AD tartományi szolgáltatások által kezelt tartomány hozhat létre az erőforráscsoportban.


## <a name="task-6-create-and-configure-the-virtual-network"></a>6. feladat: Hozza létre, és konfigurálja a virtuális hálózat
Most hozzon létre a virtuális hálózatot, amelyben Azure AD tartományi szolgáltatások engedélyezése. Győződjön meg arról, hogy hozzon létre egy dedikált alhálózat az Azure AD tartományi szolgáltatásokhoz. Ne telepítse a munkaterhelési virtuális gépek dedikált alhálózat be.

Írja be a következő PowerShell-parancsok dedikált alhálózattal virtuális hálózat létrehozása az Azure AD tartományi szolgáltatásokhoz.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>7. feladat: Kiépíteni az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
Írja be az Azure AD tartományi szolgáltatásokat a címtáron engedélyezéséhez a következő PowerShell-parancsot:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Ne felejtse el a további konfigurációs lépéseket a felügyelt tartományok kiépítése után.**
> A felügyelt tartományok üzembe helyezése után továbbra is szeretné hajtsa végre a következő feladatokat:
> * **[DNS-beállítások frissítése](active-directory-ds-getting-started-dns.md)**  a virtuális hálózat, így a virtuális gépek a tartományhoz való csatlakozást vagy hitelesítési megtalálhatja a felügyelt tartományra.
* **[Az Azure AD tartományi szolgáltatásokra jelszó-szinkronizálás engedélyezése](active-directory-ds-getting-started-password-sync.md)**, így a végfelhasználók bármikor beléphet a vállalati hitelesítő adatok a felügyelt tartományra.
>


## <a name="powershell-script"></a>PowerShell-szkript
A cikkben szereplő műveletek elvégzéséhez használt PowerShell-parancsfájl nem éri el. Másolja a parancsfájlt, és mentse egy ".ps1" kiterjesztésű fájl. Hajtsa végre a parancsfájlt a PowerShell vagy a PowerShell integrált parancsfájlkezelési környezet (ISE) használatával.

> [!NOTE]
> **Ez a parancsfájl futtatásához szükséges engedélyek** ahhoz, hogy az Azure AD tartományi szolgáltatásokat, kell lennie az Azure AD-címtár globális rendszergazdája. Továbbá szüksége legalább "Közreműködői" jogosultsággal, amelyben a virtuális hálózaton Azure AD tartományi szolgáltatások engedélyezése.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

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
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Ne felejtse el a további konfigurációs lépéseket a felügyelt tartományok kiépítése után.**
> A felügyelt tartományok üzembe helyezése után továbbra is szeretné hajtsa végre a következő feladatokat:
> * Frissítse a virtuális hálózat DNS-beállításait, a virtuális gépek a tartományhoz való csatlakozást vagy hitelesítési megtalálhatja a felügyelt tartományra.
* Azure AD tartományi szolgáltatások jelszavak szinkronizálásának engedélyezése, így a végfelhasználók bármikor beléphet a vállalati hitelesítő adatok a felügyelt tartományra.
>

## <a name="next-steps"></a>További lépések
A felügyelt tartományok létrehozását követően, hogy használhassa a felügyelt tartományra hajtsa végre a következő konfigurációs feladatok:

* [A DNS-kiszolgáló beállításait, a virtuális hálózat, a felügyelt tartományra mutasson frissítése](active-directory-ds-getting-started-dns.md)
* [A felügyelt tartományok jelszavak szinkronizálásának engedélyezése](active-directory-ds-getting-started-password-sync.md)
