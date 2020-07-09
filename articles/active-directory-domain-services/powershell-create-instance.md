---
title: Az Azure DS tartományi szolgáltatások engedélyezése a PowerShell használatával | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja és engedélyezheti Azure Active Directory Domain Services az Azure AD PowerShell és a Azure PowerShell használatával.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: 9dbb8a6011b4f2aebc73df7d37e6f43e7f27b747
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734520"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Azure Active Directory Domain Services engedélyezése a PowerShell használatával

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Ezeket a tartományi szolgáltatásokat a tartományvezérlők üzembe helyezése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi, hogy a felhasználók a vállalati hitelesítő adataikkal jelentkezzenek be, és meglévő csoportokat és felhasználói fiókokat is használhatnak az erőforrásokhoz való hozzáférés biztosításához.

Ez a cikk bemutatja, hogyan engedélyezheti az Azure AD DSt a PowerShell használatával.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Az Azure PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az utasításokat a [Azure PowerShell modul telepítéséhez és az Azure-előfizetéshez való kapcsolódáshoz](/powershell/azure/install-az-ps).
    * Győződjön meg róla, hogy bejelentkezik az Azure-előfizetésbe a [AzAccount][Connect-AzAccount] parancsmag használatával.
* Az Azure AD PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az [Azure ad PowerShell-modul telepítésére és az Azure ad-hez való kapcsolódásra](/powershell/azure/active-directory/install-adv2)vonatkozó utasításokat.
    * Győződjön meg arról, hogy bejelentkezik az Azure AD-bérlőbe a [AzureAD][Connect-AzureAD] parancsmag használatával.
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az Azure-előfizetésben *közreműködő* jogosultságokkal kell rendelkeznie a szükséges Azure AD DS-erőforrások létrehozásához.

## <a name="create-required-azure-ad-resources"></a>Szükséges Azure AD-erőforrások létrehozása

Az Azure AD DS használatához egy egyszerű szolgáltatásnév és egy Azure AD-csoport szükséges. Ezek az erőforrások lehetővé teszik, hogy az Azure AD DS felügyelt tartomány szinkronizálja az adatokat, és meghatározhatja, hogy mely felhasználók rendelkeznek rendszergazdai jogosultságokkal a felügyelt tartományban.

Először hozzon létre egy Azure AD-szolgáltatásnevet az Azure AD DS számára a kommunikációhoz és a hitelesítéshez. A rendszer egy adott alkalmazásspecifikus azonosítót használ a *2565BD9D-DA50-47D4-8B85-4C97F669DC36*azonosítóval rendelkező *tartományvezérlői szolgáltatások* névvel. Ne módosítsa az alkalmazás AZONOSÍTÓját.

Hozzon létre egy Azure AD-szolgáltatásnevet a [New-azureadserviceprincipal parancsmagot][New-AzureADServicePrincipal] parancsmag használatával:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Most hozzon létre egy *HRE DC-rendszergazdák*nevű Azure ad-csoportot. A csoportba felvett felhasználók engedélyeket kapnak az adminisztrációs feladatok végrehajtásához a felügyelt tartományon.

Hozza létre a *HRE DC rendszergazdák* csoportot a [New-AzureADGroup][New-AzureADGroup] parancsmag használatával:

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Ha létrehozta a *HRE DC-rendszergazdák* csoportot, adjon hozzá egy felhasználót a csoporthoz az [Add-AzureADGroupMember][Add-AzureADGroupMember] parancsmag használatával. Először a Get [-AzureADGroup][Get-AzureADGroup] parancsmaggal szerezheti be a *HRE DC rendszergazdák* csoportjának azonosítóját, majd a kívánt felhasználó objektumazonosítót a [Get-AzureADUser][Get-AzureADUser] parancsmag használatával.

A következő példában a fiók felhasználói objektumának azonosítója egy egyszerű felhasználónévvel `admin@aaddscontoso.onmicrosoft.com` . Cserélje le ezt a felhasználói fiókot annak a felhasználónak a UPN-fiókjára, amelyet hozzá szeretne adni a *HRE DC-rendszergazdák* csoporthoz:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Támogató Azure-erőforrások létrehozása

Először regisztrálja a Azure AD Domain Services erőforrás-szolgáltatót a [Register-AzResourceProvider][Register-AzResourceProvider] parancsmag használatával:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Ezután hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup] parancsmag használatával. A következő példában az erőforráscsoport neve *myResourceGroup* , és a *westus* régióban jön létre. Saját név és kívánt régió használata:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Hozza létre a virtuális hálózatot és az alhálózatokat a Azure AD Domain Serviceshoz. Két alhálózat jön létre – egy a *DomainServices*, egy pedig a számítási *feladatok*számára. Az Azure AD DS üzembe helyezése a dedikált *DomainServices* alhálózaton történik. Ne helyezzen üzembe más alkalmazásokat vagy munkaterheléseket ebbe az alhálózatba. A többi virtuális gép esetében használja a különálló *munkaterheléseket* vagy más alhálózatokat.

Hozza létre az alhálózatokat a [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] parancsmaggal, majd hozza létre a virtuális hálózatot a [New-AzVirtualNetwork][New-AzVirtualNetwork] parancsmag használatával.

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-a-managed-domain"></a>Felügyelt tartomány létrehozása

Most hozzon létre egy felügyelt tartományt. Állítsa be az Azure-előfizetés AZONOSÍTÓját, majd adja meg a felügyelt tartomány nevét (például *aaddscontoso.com*). Az előfizetés-azonosítót a [Get-AzSubscription][Get-AzSubscription] parancsmaggal kérheti le.

Ha olyan régiót választ, amely támogatja az Availability Zones-t, az Azure AD DS erőforrásai a további redundancia érdekében a zónák között oszlanak meg.

A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található.

Nem kell konfigurálnia az Azure AD DS a zónák közötti elosztására. Az Azure platform automatikusan kezeli az erőforrások zónájának eloszlását. További információért és a régiók rendelkezésre állásának megtekintéséhez lásd: [Mi a Availability Zones az Azure-ban?][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Az erőforrás létrehozása és a vezérlés visszaküldése a PowerShell-parancssorba néhány percet vesz igénybe. A felügyelt tartomány továbbra is a háttérben lesz kiépítve, és akár egy óráig is eltarthat a telepítés befejezéséhez. A Azure Portal a felügyelt tartomány **Áttekintés** lapja a telepítési fázis aktuális állapotát jeleníti meg.

Ha a Azure Portal azt mutatja, hogy a felügyelt tartomány befejezte az üzembe helyezést, a következő feladatokat kell elvégezni:

* A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez.
    * A DNS konfigurálásához válassza ki a felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
* Ha olyan régióban hozott létre felügyelt tartományt, amely támogatja a Availability Zonest, hozzon létre egy hálózati biztonsági csoportot a felügyelt tartományhoz tartozó virtuális hálózat forgalmának korlátozására. Létrejön egy Azure standard Load Balancer, amely megköveteli a szabályok elhelyezését. Ez a hálózati biztonsági csoport biztosítja az Azure AD DSét, és szükséges a felügyelt tartomány megfelelő működéséhez.
    * A hálózati biztonsági csoport és a szükséges szabályok létrehozásához válassza ki a felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan létrehozza és konfigurálja a hálózati biztonsági csoportot.
* [Engedélyezze a jelszó-szinkronizálást Azure ad Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , hogy a végfelhasználók a vállalati hitelesítő adataik használatával bejelentkezhetnek a felügyelt tartományba.

## <a name="complete-powershell-script"></a>PowerShell-parancsfájl befejezése

A következő teljes PowerShell-parancsfájl ötvözi a cikkben látható összes feladatot. Másolja a szkriptet, és mentse egy `.ps1` kiterjesztésű fájlba. Futtassa a szkriptet egy helyi PowerShell-konzolon vagy a [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Az Azure AD DS engedélyezéséhez globális rendszergazdának kell lennie az Azure AD-bérlő számára. Emellett az Azure-előfizetésben legalább *közreműködő* jogosultsággal is rendelkeznie kell.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

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
  -Force -Verbose
```

Az erőforrás létrehozása és a vezérlés visszaküldése a PowerShell-parancssorba néhány percet vesz igénybe. A felügyelt tartomány továbbra is a háttérben lesz kiépítve, és akár egy óráig is eltarthat a telepítés befejezéséhez. A Azure Portal a felügyelt tartomány **Áttekintés** lapja a telepítési fázis aktuális állapotát jeleníti meg.

Ha a Azure Portal azt mutatja, hogy a felügyelt tartomány befejezte az üzembe helyezést, a következő feladatokat kell elvégezni:

* A virtuális hálózat DNS-beállításainak frissítése, hogy a virtuális gépek megtalálják a felügyelt tartományt a tartományhoz való csatlakozáshoz vagy a hitelesítéshez.
    * A DNS konfigurálásához válassza ki a felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan konfigurálja ezeket a DNS-beállításokat.
* Ha olyan régióban hozott létre felügyelt tartományt, amely támogatja a Availability Zonest, hozzon létre egy hálózati biztonsági csoportot a felügyelt tartományhoz tartozó virtuális hálózat forgalmának korlátozására. Létrejön egy Azure standard Load Balancer, amely megköveteli a szabályok elhelyezését. Ez a hálózati biztonsági csoport biztosítja az Azure AD DSét, és szükséges a felügyelt tartomány megfelelő működéséhez.
    * A hálózati biztonsági csoport és a szükséges szabályok létrehozásához válassza ki a felügyelt tartományt a portálon. Az **Áttekintés** ablakban a rendszer automatikusan létrehozza és konfigurálja a hálózati biztonsági csoportot.
* [Engedélyezze a jelszó-szinkronizálást Azure ad Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , hogy a végfelhasználók a vállalati hitelesítő adataik használatával bejelentkezhetnek a felügyelt tartományba.

## <a name="next-steps"></a>További lépések

A felügyelt tartomány működés közbeni megtekintéséhez [tartományhoz csatlakoztathat egy Windows rendszerű virtuális gépet][windows-join], [konfigurálhatja a biztonságos LDAP][tutorial-ldaps]-t, és [konfigurálhatja a jelszó-kivonatok szinkronizálását][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
