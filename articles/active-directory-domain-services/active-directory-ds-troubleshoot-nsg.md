---
title: "Az Azure Active Directory tartományi szolgáltatások: Hibaelhárítási hálózati biztonsági csoport konfigurációs |} Microsoft Docs"
description: "Az Azure AD tartományi szolgáltatásokra vonatkozó hibaelhárítási NSG-konfiguráció"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure AD tartományi szolgáltatások - Hibaelhárítás hálózati biztonsági csoport konfigurálása



## <a name="aadds104-network-error"></a>AADDS104: Hálózati hiba

**Figyelmeztető üzenet:** *Microsoft nem tudja elérni a tartományvezérlőket, a felügyelt tartomány. Ez akkor fordulhat elő, ha egy hálózati biztonsági csoport (NSG) van konfigurálva. a virtuális hálózati blokkok hozzáférési a felügyelt tartományra. Egy másik lehetséges oka-e a felhasználó által megadott útvonal, hogy blokkolja az internetről érkező bejövő forgalmat.*

A hálózati hibák leggyakoribb oka az Azure AD tartományi szolgáltatásokhoz tulajdoníthatók helytelen NSG-konfigurációk. Győződjön meg arról, hogy a Microsoft szolgáltatást, és Ön által felügyelt tartomány karbantartása, kell használnia a hálózati biztonsági csoport (NSG) való hozzáférés engedélyezése [adott portok](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services) az alhálózaton belül. Ha ezeket a portokat a letiltott Microsoft nem férhetnek hozzá az erőforrásokhoz van szüksége, esetleg káros a szolgáltatásnak. Az NSG létrehozásakor nyitva hagyja, ezeket a portokat és a szolgáltatás megszakítás nélküli fenntartása érdekében.

## <a name="sample-nsg"></a>Minta NSG
Az alábbi táblázat mutatja be egy minta NSG-t, amely megakadályozná a felügyelt tartományok biztonságos úgy, hogy a Microsoft figyeléséhez, kezeléséhez és információk frissítése közben.

![minta NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD tartományi szolgáltatások nem korlátozott kimenő hozzáférést igényel. Azt javasoljuk, hogy ne hozzon létre semmilyen további outboud szabály, amely az NSG-k.

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>A szabály hozzáadása a hálózati biztonsági csoport az Azure portál használatával
Ha nem szeretné, ha a PowerShell segítségével, az NSG-k az Azure portál használatával manuálisan egyetlen szabályok is hozzáadhat. Kövesse az alábbi lépéseket a hálózati biztonsági csoport szabályok létrehozására.

1. Keresse meg a [hálózati biztonsági csoportok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) oldal az Azure portálon
2. Válassza ki a tartományt a táblázatban a társított NSG.
3. Kattintson a bal oldali navigációs beállításait, **bejövő biztonsági szabályok** vagy **kimenő biztonsági szabályok**.
4. A szabály létrehozása gombra kattintva **Hozzáadás** és az adatokat. Kattintson az **OK** gombra.
5. Ellenőrizze, hogy a szabály a szabályok tábla megkeresésével jött létre.


## <a name="create-a-default-nsg-using-powershell"></a>Hozzon létre egy alapértelmezett NSG PowerShell használatával

Megelőző, a lépések segítségével hozzon létre egy új NSG-t mindegyik port szolgáltatás a szükséges futtatásához Azure AD tartományi szolgáltatások nyissa meg minden egyéb nem kívánt hozzáférés megtagadása történt PowerShell használatával.


Ez a megoldás telepítéséhez és futtatásához szükséges [Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0).

1. Csatlakozás az Azure AD-címtár.

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Jelentkezzen be az Azure-előfizetéshez.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. Hozzon létre egy NSG-t három szabályok. A következő parancsfájl meghatározza, hogy a három szabályok, amelyek lehetővé teszik a hozzáférést az Azure AD tartományi szolgáltatások futtatásához szükséges portok az NSG. Ezt követően a parancsfájl hoz létre egy új NSG-t, amely tartalmazza ezeket a szabályokat. Azonban nyugodtan megadhatnak adja hozzá a további szabályok, ahogyan szeretné ugyanazt a formátumot használja.

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. Végül a parancsfájl társítja az NSG-t a vnet és alhálózat választott.

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>Teljes szkript

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>Ez az alapértelmezett NSG nem zárolni a használt biztonságos LDAP-port elérését. Megtudhatja, hogyan hozzon létre egy szabályt az ehhez a porthoz, látogasson el a [Ez a cikk](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport a [visszajelzés fájlmegosztás vagy a támogatáshoz](active-directory-ds-contact-us.md).
