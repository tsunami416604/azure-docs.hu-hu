---
title: 'Az Azure Active Directory Domain Services: Hibaelhárítási hálózati biztonsági csoport konfigurálása |} A Microsoft Docs'
description: Az Azure AD tartományi szolgáltatásokhoz NSG konfigurációs hibáinak elhárítása
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: a2acbed81e323718c7d294d87ebf699c35664d02
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502645"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>A felügyelt tartomány konfigurációja érvénytelen hálózati hibaelhárítása
Ez a cikk segítséget nyújt a hibaelhárításához és megoldásához hálózati konfigurációs hibák, amelyek a következő figyelmeztető üzenet:

## <a name="alert-aadds104-network-error"></a>Riasztási AADDS104: Hálózati hiba
**Figyelmeztető üzenet:** *a Microsoft nem tudja elérni a tartományvezérlőket, a felügyelt tartományhoz. Ez akkor fordulhat elő, ha a virtuális hálózati blokkolja a hozzáférést a felügyelt tartományhoz konfigurált hálózati biztonsági csoport (NSG). Egy másik oka az lehet, ha egy felhasználó által definiált útvonal van, amely blokkolja az internetről bejövő forgalmat.*

Érvénytelen az NSG-konfiguráció olyan hálózati hibák leggyakoribb oka az Azure AD tartományi szolgáltatásokhoz. A hálózati biztonsági csoport (NSG) a virtuális hálózat engedélyeznie kell a hozzáférést konfigurált [bizonyos portokat](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Ezeket a portokat le vannak tiltva, ha a Microsoft nem figyelése és a felügyelt tartomány frissítése. Emellett az Azure AD-címtár és a felügyelt tartomány közötti szinkronizálásra van hatással. Az NSG létrehozásakor ne zárja be ezeket a portokat a szolgáltatás megszakadásának elkerülése érdekében.

### <a name="checking-your-nsg-for-compliance"></a>Az NSG-t a megfelelőség ellenőrzése

1. Keresse meg a [hálózati biztonsági csoportok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) oldal az Azure Portalon
2. A táblából válasszon, amelyben a felügyelt tartomány engedélyezve van az alhálózathoz társított NSG-t.
3. A **beállítások** kattintson a bal oldali panel **bejövő biztonsági szabályok**
4. Tekintse át a szabályokat a helyen, és azonosítsa, mely szabályok forgalomszűrők blokkolják a hozzáférést [ezeket a portokat](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)
5. Szerkessze az NSG-t a megfelelőség biztosítása a szabály törlése folyamatban van, szabály hozzáadása, vagy egy új NSG-t teljes mértékben létrehozása. A lépések [szabály hozzáadása](#add-a-rule-to-a-network-security-group-using-the-azure-portal) vagy [hozzon létre egy új, előírásoknak megfelelő NSG](#create-a-nsg-for-azure-ad-domain-services-using-powershell) az alábbi

## <a name="sample-nsg"></a>Minta NSG-t
Az alábbi táblázat mutatja be, hogy egy NSG-t, hogy az lenne a felügyelt tartomány secure miközben lehetővé teszi a figyelése, kezelése és adatok frissítése a Microsoft mintát.

![Minta NSG-t](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Az Azure AD tartományi szolgáltatások a virtuális hálózatról korlátlan kimenő hozzáféréssel kell rendelkeznie. Azt javasoljuk, hogy nem minden olyan további NSG-szabályt, amely korlátozza a kimenő hozzáférést a virtuális hálózat létrehozása.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Egy szabály hozzáadása egy hálózati biztonsági csoportot az Azure portal használatával
Ha nem szeretné, ha a PowerShell segítségével, manuálisan is hozzáadhat egyetlen szabályokat az NSG-k az Azure portal használatával. A hálózati biztonsági csoport szabályainak létrehozásához hajtsa végre az alábbi lépéseket:

1. Keresse meg a [hálózati biztonsági csoportok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) oldal az Azure Portalon.
2. A táblából válasszon, amelyben a felügyelt tartomány engedélyezve van az alhálózathoz társított NSG-t.
3. A **beállítások** kattintson a bal oldali panelen **bejövő biztonsági szabályok** vagy **kimenő biztonsági szabályok**.
4. A szabály létrehozása gombra kattintva **Hozzáadás** és kitölti az adatokat. Kattintson az **OK** gombra.
5. Ellenőrizze, hogy a szabály létrejött, a szabályok tábla megkeresésével.


## <a name="create-a-nsg-for-azure-ad-domain-services-using-powershell"></a>Hozzon létre egy NSG-t az Azure AD tartományi szolgáltatásokhoz PowerShell-lel
Ez az NSG a az Azure AD tartományi szolgáltatások, míg más kéretlen bejövő hozzáférést szükséges portokon bejövő adatforgalom engedélyezésére van konfigurálva.

**Előfeltétel: Telepítse és konfigurálja az Azure Powershellt** utasításokat követve [az Azure PowerShell-modul telepítése és csatlakozás az Azure-előfizetéshez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Azt javasoljuk, hogy az Azure PowerShell-modul legújabb verzióját használja. Ha már rendelkezik egy régebbi verzióját az Azure PowerShell-modul telepítve van, frissítse a legújabb verzióra.
>

A következő lépések segítségével hozzon létre egy új NSG-t PowerShell használatával.
1. Jelentkezzen be az Azure-előfizetéshez.

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. Hozzon létre három szabályokat az NSG-KET. A következő parancsfájl három szabályokat az NSG-t, amely engedélyezi a hozzáférést az Azure AD tartományi szolgáltatások futtatásához szükséges portokhoz határozza meg. Ezt követően az a szkript létrehoz egy új NSG-t, amely tartalmazza ezeket a szabályokat. Adja hozzá a további szabályok, amelyek lehetővé teszik az egyéb bejövő forgalmat, ha a virtuális hálózaton üzembe helyezett számítási feladatok által igényelt, ugyanazt a formátumot használja.

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

3. Végül társítsa az NSG-KET a virtuális hálózat és alhálózat választott.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Teljes szkript hozhat létre és alkalmazzon NSG-t az Azure AD tartományi szolgáltatásokhoz
>[!TIP]
> Azt javasoljuk, hogy az Azure PowerShell-modul legújabb verzióját használja. Ha már rendelkezik egy régebbi verzióját az Azure PowerShell-modul telepítve van, frissítse a legújabb verzióra.
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
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
