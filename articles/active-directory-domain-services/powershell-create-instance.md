---
title: Az Azure DS tartományi szolgáltatások engedélyezése a PowerShell használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhatja és engedélyezheti az Azure Active Directory tartományi szolgáltatásokat az Azure AD PowerShell és az Azure PowerShell használatával.
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
ms.openlocfilehash: e99ad2d53bc26b4e13a34097baaec929058a61a0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654801"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Az Azure Active Directory tartományi szolgáltatások engedélyezése a PowerShell használatával

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM-hitelesítés, amelyek teljes mértékben kompatibilisek a Windows Server Active Directoryval. Ezeket a tartományi szolgáltatásokat a tartományvezérlők telepítése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-bérlővel. Ez az integráció lehetővé teszi a felhasználók számára, hogy a vállalati hitelesítő adataikkal jelentkezzenek be, és a meglévő csoportok és felhasználói fiókok segítségével biztosíthatja az erőforrásokhoz való hozzáférést.

Ez a cikk bemutatja, hogyan engedélyezheti az Azure AD DS-t a PowerShell használatával.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtásához a következő erőforrásokra van szükség:

* Az Azure PowerShell telepítése és konfigurálása.
    * Szükség esetén kövesse az utasításokat [az Azure PowerShell-modul telepítéséhez és az Azure-előfizetéshez való csatlakozáshoz.](/powershell/azure/install-az-ps)
    * Győződjön meg arról, hogy a [Connect-AzAccount][Connect-AzAccount] parancsmag használatával jelentkezik be az Azure-előfizetésbe.
* Telepítse és konfigurálja az Azure AD PowerShellt.
    * Szükség esetén kövesse az utasításokat [az Azure AD PowerShell-modul telepítéséhez és az Azure AD-hez való csatlakozáshoz.](/powershell/azure/active-directory/install-adv2)
    * Győződjön meg arról, hogy a [Connect-AzureAD-parancsmag][Connect-AzureAD] használatával bejelentkezik az Azure AD-bérlőbe.
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságokra van szüksége az Azure AD-bérlőben.
* A szükséges Azure AD DS-erőforrások létrehozásához *közreműködői* jogosultságokra van szüksége az Azure-előfizetésben.

## <a name="create-required-azure-ad-resources"></a>A szükséges Azure AD-erőforrások létrehozása

Az Azure AD DS használatához egyszerű szolgáltatásra és egy Azure AD-csoportra van szükség. Ezek az erőforrások lehetővé teszik, hogy az Azure AD DS felügyelt tartomány adatokat szinkronizáljon, és meghatározza, hogy mely felhasználók rendelkeznek rendszergazdai engedélyekkel a felügyelt tartományban.

Először hozzon létre egy Azure AD szolgáltatás névszerint az Azure AD DS kommunikálni és hitelesíteni magát. Egy adott alkalmazásazonosítót használ nevű *tartományvezérlő szolgáltatások* azonosítója *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Ne módosítsa az alkalmazásazonosítót.

Hozzon létre egy Azure AD szolgáltatásnév a [New-AzureADServicePrincipal][New-AzureADServicePrincipal] parancsmag használatával:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Most hozzon létre egy *AAD DC rendszergazdák*nevű Azure AD-csoportot. A csoporthoz hozzáadott felhasználók ezután engedélyt kapnak felügyeleti feladatok végrehajtására az Azure AD DS felügyelt tartományban.

Hozza létre az *AAD DC rendszergazdák* csoportot az [Új-AzureADGroup][New-AzureADGroup] parancsmag használatával:

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Az *AAD DC rendszergazdák* csoport létrehozása, hozzá egy felhasználót a csoporthoz az [Add-AzureADGroupMember][Add-AzureADGroupMember] parancsmag használatával. Először az *AAD DC rendszergazdák* csoportobjektum-azonosítót a [Get-AzureADGroup][Get-AzureADGroup] parancsmag használatával kapja meg, majd a kívánt felhasználói objektumazonosítót a [Get-AzureADUser][Get-AzureADUser] parancsmag használatával.

A következő példában a felhasználói objektum azonosítója a `admin@aaddscontoso.onmicrosoft.com`fiók hoz egy. Cserélje le ezt a felhasználói fiókot annak a felhasználónak az upn-jára, akit hozzá szeretne adni az *AAD DC Rendszergazdák* csoporthoz:

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

Először regisztrálja az Azure AD tartományi szolgáltatások erőforrás-szolgáltatót a [Register-AzResourceProvider][Register-AzResourceProvider] parancsmag használatával:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Ezután hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup] parancsmag használatával. A következő példában az erőforráscsoport neve *myResourceGroup,* és a *westus* régióban jön létre. Használja a saját nevét és a kívánt régiót:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Hozza létre a virtuális hálózatot és az alhálózatokat az Azure AD tartományi szolgáltatásokhoz. Két alhálózat jön létre - egy a *DomainServices*és egy a *számítási feladatokhoz.* Az Azure AD DS a dedikált *DomainServices-alhálózatba* van telepítve. Ne telepítsen más alkalmazásokat vagy számítási feladatokat ebbe az alhálózatba. Használja a külön *számítási feladatok* vagy más alhálózatok a többi virtuális gépek.

Hozza létre az alhálózatokat a [New-AzVirtualSubnetConfig][New-AzVirtualNetworkSubnetConfig] parancsmag használatával, majd hozza létre a virtuális hálózatot a [New-AzVirtualNetwork][New-AzVirtualNetwork] parancsmag használatával.

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>Azure AD DS felügyelt tartomány ának létrehozása

Most hozzon létre egy Azure AD DS felügyelt tartományt. Állítsa be az Azure-előfizetés-azonosítóját, majd adja meg a felügyelt tartomány nevét, például *aaddscontoso.com.* Az előfizetés-azonosítóa a [Get-AzSubscription][Get-AzSubscription] parancsmag használatával szerezhető be.

Ha olyan régiót választ, amely támogatja a rendelkezésre állási zónákat, az Azure AD DS-erőforrások zónák között vannak elosztva a további redundancia érdekében.

A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található.

Nincs mit beállítani az Azure AD DS zónák között elosztott. Az Azure platform automatikusan kezeli az erőforrások zónaelosztását. További információért és a régió elérhetőségéről a [Mik azok a rendelkezésre állási zónák az Azure-ban?][availability-zones]

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

Néhány percet vesz igénybe az erőforrás létrehozása, és adja vissza a vezérlést a PowerShell-parancssorba. Az Azure AD DS felügyelt tartomány továbbra is kivan építve a háttérben, és akár egy órát is igénybe vehet a központi telepítés befejezéséhez. Az Azure Portalon az Azure AD DS felügyelt tartomány **áttekintése** lap mutatja az aktuális állapotot ebben a telepítési szakaszban.

Ha az Azure Portal azt mutatja, hogy az Azure AD DS felügyelt tartomány a kiépítés befejeződött, a következő feladatokat kell végrehajtani:

* Frissítse a virtuális hálózat DNS-beállításait, hogy a virtuális gépek megtalálják a tartományhoz való csatlakozáshoz vagy hitelesítéshez szükséges felügyelt tartományt.
    * A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományát a portálon. Az **Áttekintés ablakban** a rendszer kéri, hogy automatikusan konfigurálja ezeket a DNS-beállításokat.
* Ha létrehozott egy Azure AD DS felügyelt tartományt egy olyan régióban, amely támogatja a rendelkezésre állási zónákat, hozzon létre egy hálózati biztonsági csoportot az Azure AD DS felügyelt tartomány virtuális hálózatának forgalom korlátozására. Létrejön egy Azure-szabvány terheléselosztó, amely megköveteli, hogy ezeket a szabályokat kell elhelyezni. Ez a hálózati biztonsági csoport biztonságossá teszi az Azure AD DS-t, és a felügyelt tartomány megfelelő működéséhez szükséges.
    * A hálózati biztonsági csoport és a szükséges szabályok létrehozásához válassza ki az Azure AD DS felügyelt tartományát a portálon. Az **Áttekintés ablakban** a rendszer kéri, hogy automatikusan hozza létre és konfigurálja a hálózati biztonsági csoportot.
* [Engedélyezze a jelszó-szinkronizálást az Azure AD tartományi szolgáltatásokkal,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) hogy a végfelhasználók vállalati hitelesítő adataikkal bejelentkezhessenek a felügyelt tartományba.

## <a name="complete-powershell-script"></a>Teljes PowerShell-parancsfájl

A következő teljes PowerShell-parancsfájl egyesíti a cikkben látható összes feladatot. Másolja a parancsfájlt a parancsfájlba, és mentse egy kiterjesztésű `.ps1` fájlba. Futtassa a parancsfájlt egy helyi PowerShell-konzolon vagy az [Azure Cloud Shellben.][cloud-shell]

> [!NOTE]
> Az Azure AD DS engedélyezéséhez az Azure AD-bérlő globális rendszergazdájának kell lennie. Emellett legalább *közreműködői* jogosultságokat is meg kell tudnia az Azure-előfizetésben.

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

Néhány percet vesz igénybe az erőforrás létrehozása, és adja vissza a vezérlést a PowerShell-parancssorba. Az Azure AD DS felügyelt tartomány továbbra is kivan építve a háttérben, és akár egy órát is igénybe vehet a központi telepítés befejezéséhez. Az Azure Portalon az Azure AD DS felügyelt tartomány **áttekintése** lap mutatja az aktuális állapotot ebben a telepítési szakaszban.

Ha az Azure Portal azt mutatja, hogy az Azure AD DS felügyelt tartomány a kiépítés befejeződött, a következő feladatokat kell végrehajtani:

* Frissítse a virtuális hálózat DNS-beállításait, hogy a virtuális gépek megtalálják a tartományhoz való csatlakozáshoz vagy hitelesítéshez szükséges felügyelt tartományt.
    * A DNS konfigurálásához válassza ki az Azure AD DS felügyelt tartományát a portálon. Az **Áttekintés ablakban** a rendszer kéri, hogy automatikusan konfigurálja ezeket a DNS-beállításokat.
* Ha létrehozott egy Azure AD DS felügyelt tartományt egy olyan régióban, amely támogatja a rendelkezésre állási zónákat, hozzon létre egy hálózati biztonsági csoportot az Azure AD DS felügyelt tartomány virtuális hálózatának forgalom korlátozására. Létrejön egy Azure-szabvány terheléselosztó, amely megköveteli, hogy ezeket a szabályokat kell elhelyezni. Ez a hálózati biztonsági csoport biztonságossá teszi az Azure AD DS-t, és a felügyelt tartomány megfelelő működéséhez szükséges.
    * A hálózati biztonsági csoport és a szükséges szabályok létrehozásához válassza ki az Azure AD DS felügyelt tartományát a portálon. Az **Áttekintés ablakban** a rendszer kéri, hogy automatikusan hozza létre és konfigurálja a hálózati biztonsági csoportot.
* [Engedélyezze a jelszó-szinkronizálást az Azure AD tartományi szolgáltatásokkal,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) hogy a végfelhasználók vállalati hitelesítő adataikkal bejelentkezhessenek a felügyelt tartományba.

## <a name="next-steps"></a>További lépések

Az Azure AD DS felügyelt tartományának működés közbeni megtekintéséhez [tartományban csatlakozhat egy Windows virtuális géphez,][windows-join] [konfigurálhatja a biztonságos LDAP-t][tutorial-ldaps], és beállíthatja a [jelszókivonat-szinkronizálást.][tutorial-phs]

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
