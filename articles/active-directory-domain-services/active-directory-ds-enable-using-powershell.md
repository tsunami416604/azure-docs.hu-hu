---
title: Engedélyezze az Azure Active Directory Domain Services PowerShell-lel |} A Microsoft Docs
description: Engedélyezze az Azure Active Directory Domain Services PowerShell-lel
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: ergreenl
ms.openlocfilehash: a97103aa1b98d1dc93cecc8350892adafd9618ef
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156512"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Engedélyezze az Azure Active Directory Domain Services PowerShell-lel
Ez a cikk bemutatja, hogyan PowerShell-lel az Azure Active Directory (AD) Domain Services engedélyezéséhez.

## <a name="task-1-install-the-required-powershell-modules"></a>1. feladat: A szükséges PowerShell-modulok telepítéséhez.

### <a name="install-and-configure-azure-ad-powershell"></a>Az Azure AD PowerShell telepítése és konfigurálása
Kövesse a cikkben szereplő utasításokat [Azure AD PowerShell-modul telepítéséhez és az Azure AD connect](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Az Azure PowerShell telepítése és konfigurálása
Kövesse a cikkben szereplő utasításokat [az Azure PowerShell-modul telepítése és csatlakozás az Azure-előfizetéshez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>2. feladat: A szükséges szolgáltatásnév létrehozása az Azure AD-címtár
Írja be a következő PowerShell-parancsot az Azure AD tartományi szolgáltatásokat az Azure AD-címtár számára szükséges egyszerű szolgáltatás létrehozása.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>3. feladat: Hozzon létre, és az "AAD DC rendszergazdák" csoport konfigurálása
A következő feladata a felügyeleti feladatok a felügyelt tartomány delegálása használandó rendszergazdai csoport létrehozásához.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Most, hogy létrehozta a csoportot, a felhasználók több hozzáadása a csoporthoz.
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
Írja be a következő PowerShell-parancsot az Azure AD tartományi szolgáltatásokhoz az erőforrás-szolgáltató regisztrálásához:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>5. feladat: Erőforráscsoport létrehozása
Írja be a következő PowerShell-paranccsal hozzon létre egy erőforráscsoportot:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

A virtuális hálózat és az Azure AD tartományi szolgáltatásokkal felügyelt tartományban hozhat létre az erőforráscsoportban.


## <a name="task-6-create-and-configure-the-virtual-network"></a>6. feladat: Hozzon létre, és a virtuális hálózat konfigurálása
Hozza létre a virtuális hálózatot, amelyben az Azure AD tartományi szolgáltatások engedélyezése. Győződjön meg arról, hogy az Azure AD tartományi szolgáltatásokhoz hozzon létre egy dedikált alhálózatán. Ne telepítse a munkaterhelési virtuális gépek az dedikált alhálózatban.

Írja be a következő PowerShell-parancsok egy kijelölt alhálózatot a virtuális hálózat létrehozása az Azure AD tartományi szolgáltatásokhoz.

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


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>7. feladat: Az Azure AD tartományi szolgáltatásokkal felügyelt tartományban üzembe helyezése
Írja be a címtár Azure AD tartományi szolgáltatások engedélyezése a következő PowerShell-parancsot:

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
> **A felügyelt tartomány üzembe helyezés után ne felejtse el a további konfigurációs lépéseket.**
> A felügyelt tartomány üzembe, után továbbra is szeretné végrehajtani az alábbi feladatokat:
> * **[DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)**  a virtuális hálózathoz, így a virtuális gépek a felügyelt tartományhoz csatlakozás tartományhoz vagy hitelesítésre.
* **[Jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatások](active-directory-ds-getting-started-password-sync.md)**, így a végfelhasználók bejelentkezhet a felügyelt tartomány vállalati hitelesítői adataikkal.
>


## <a name="powershell-script"></a>PowerShell-szkript
A cikkben szereplő összes feladatot használt PowerShell-parancsprogram nem éri el. Másolja a szkriptet, és mentse azt egy ".ps1" kiterjesztésű fájlt. Hajtsa végre a szkriptet a PowerShellben vagy a PowerShell integrált parancsfájl-kezelési környezet (ISE) használatával.

> [!NOTE]
> **Ez a szkript futtatásához szükséges engedélyek** ahhoz, hogy az Azure AD tartományi szolgáltatásokat, kell lennie az Azure AD-címtár globális rendszergazdája. Emellett szükség legalább "Közreműködői" jogosultságokkal rendelkezik, amelyben a virtuális hálózaton az Azure AD tartományi szolgáltatások engedélyezése.
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
> **A felügyelt tartomány üzembe helyezés után ne felejtse el a további konfigurációs lépéseket.**
> A felügyelt tartomány üzembe, után továbbra is szeretné végrehajtani az alábbi feladatokat:
> * Frissítse a virtuális hálózat DNS-beállításait, hogy a virtuális gépek a tartományhoz való csatlakozás és hitelesítés talál a felügyelt tartományhoz.
* Jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokat, a végfelhasználók bejelentkezhet a felügyelt tartomány vállalati hitelesítői adataikkal.
>

## <a name="next-steps"></a>További lépések
A felügyelt tartomány létrehozása után hajtsa végre a következő konfigurációs feladatok, így használhatja a felügyelt tartomány:

* [Frissítse a DNS-kiszolgálójának beállításait a virtuális hálózathoz, hogy a felügyelt tartományra mutasson](active-directory-ds-getting-started-dns.md)
* [A felügyelt tartományhoz való szinkronizálás engedélyezése](active-directory-ds-getting-started-password-sync.md)
