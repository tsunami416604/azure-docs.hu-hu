---
title: 'Az Azure Active Directory tartományi szolgáltatások: Hibaelhárítási hálózati biztonsági csoport konfigurációs |} Microsoft Docs'
description: Az Azure AD tartományi szolgáltatásokra vonatkozó hibaelhárítási NSG-konfiguráció
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: ce03ee0e0936cea4b96e48fbc949f40ee0fe83a0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>A felügyelt tartományok érvénytelen hálózati konfiguráció hibaelhárítása
Ez a cikk segít elhárításához és hárítsa el a hálózati konfigurációs hibákat, amelyek a következő figyelmeztető üzenet:

## <a name="alert-aadds104-network-error"></a>Riasztási AADDS104: Hálózati hiba
**Figyelmeztető üzenet:** *Microsoft nem tudja elérni a tartományvezérlőket, a felügyelt tartomány. Ez akkor fordulhat elő, ha egy hálózati biztonsági csoport (NSG) van konfigurálva. a virtuális hálózati blokkok hozzáférési a felügyelt tartományra. Egy másik lehetséges ok, hogy ha egy felhasználó által megadott útvonalat, amely blokkolja az internetről érkező bejövő forgalmat.*

Érvénytelen NSG-konfiguráció olyan hálózati hibák leggyakoribb oka az Azure AD tartományi szolgáltatásokhoz. A hálózati biztonsági csoport (NSG)-e konfigurálva ehhez a virtuális hálózati hozzáférést kell engedélyeznie [adott portok](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Ha ezeket a portokat a letiltott a Microsoft nem figyelése, vagy a felügyelt tartományok frissítése. Emellett az Azure AD-címtár és a felügyelt tartományok közötti szinkronizálás kihatással van. Az NSG létrehozásakor tartani ezeket a portokat nyissa meg a szolgáltatás megszakadásának elkerülése érdekében.


## <a name="sample-nsg"></a>minta NSG
Az alábbi táblázat mutatja be egy minta NSG-t, amely megakadályozná a felügyelt tartományok biztonságos úgy, hogy a Microsoft figyeléséhez, kezeléséhez és információk frissítése közben.

![minta NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD tartományi szolgáltatások a virtuális hálózati korlátlan kimenő hozzáférést igényel. Azt javasoljuk, nem korlátozza a virtuális hálózat kimenő hozzáférést további NSG szabályok létrehozásához.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Szabályokat adhat hozzá a hálózati biztonsági csoport az Azure portál használatával
Ha nem szeretné, ha a PowerShell segítségével, az NSG-k az Azure portál használatával manuálisan egyetlen szabályok is hozzáadhat. A hálózati biztonsági csoport szabályok létrehozásához kövesse az alábbi lépéseket:

1. Keresse meg a [hálózati biztonsági csoportok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) oldal az Azure portálon
2. A táblából válassza ki, amelyben a felügyelt tartományok engedélyezve van az alhálózathoz társított NSG.
3. A **beállítások** kattintson a bal oldali panel **bejövő biztonsági szabályok** vagy **kimenő biztonsági szabályok**.
4. A szabály létrehozása gombra kattintva **Hozzáadás** és az adatokat. Kattintson az **OK** gombra.
5. Ellenőrizze, hogy a szabály a szabályok tábla megkeresésével jött létre.


## <a name="create-an-nsg-for-azure-ad-domain-services-using-powershell"></a>Hozzon létre egy NSG-t az Azure AD tartományi szolgáltatások PowerShell használatával
Az NSG-t a porton bármely egyéb nem kívánt bejövő hozzáférés megtagadása történt Azure AD tartományi szolgáltatások által igényelt bejövő adatforgalom engedélyezésére van konfigurálva.

**Előfeltétel: Telepítse és konfigurálja az Azure Powershellt** kövesse az utasításokat [telepítse az Azure PowerShell-modult, és csatlakozzon az Azure-előfizetéshez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Azt javasoljuk, hogy az Azure PowerShell modul legújabb verzióját használja. Ha már telepítette az Azure PowerShell-modul egy régebbi verziója, frissítse a legújabb verzióra.
>

Az alábbi lépések segítségével hozzon létre egy új NSG PowerShell használatával.
1. Jelentkezzen be az Azure-előfizetéshez.

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. Hozzon létre egy NSG-t három szabályok. A következő parancsfájl meghatározza, hogy a három szabályok, amelyek lehetővé teszik a hozzáférést az Azure AD tartományi szolgáltatások futtatásához szükséges portok az NSG. Ezt követően a parancsfájl hoz létre egy új NSG-t, amely tartalmazza ezeket a szabályokat. A további szabályok, amelyek lehetővé teszik az egyéb bejövő forgalmat, szükség esetén adja hozzá a virtuális hálózat üzembe helyezett munkaterhelések által ugyanazt a formátumot használja.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  #The following two rules are optional and needed only in certain situations.

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
  $SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 636

  # Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule
  ```

3. Végül az NSG-t társítson a vnet és alhálózat választott.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Parancsfájl létrehozása és alkalmazása egy NSG-t az Azure AD tartományi szolgáltatások teljes
>[!TIP]
> Azt javasoljuk, hogy az Azure PowerShell modul legújabb verzióját használja. Ha már telepítette az Azure PowerShell-modul egy régebbi verziója, frissítse a legújabb verzióra.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Connect-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# The following two rules are optional and needed only in certain situations.

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
$SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 636

# Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```


## <a name="need-help"></a>Segítség
Lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport a [visszajelzés fájlmegosztás vagy a támogatáshoz](active-directory-ds-contact-us.md).
