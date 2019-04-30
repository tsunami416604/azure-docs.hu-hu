---
title: Az Azure virtual network – Resource Manager-sablon (előzetes verzió) IPv6-alapú kettős verem az alkalmazások üzembe helyezése
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja hogyan Azure Resource Manager-beli Virtuálisgép-sablonok használata az Azure virtual network IPv6 kettős verem az alkalmazások üzembe helyezése.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130932"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Alkalmazás üzembe helyezése IPv6 kettős verem az Azure - sablon (előzetes verzió)

Ez a cikk az Azure Resource Manager Virtuálisgép-sablon, amelyre vonatkozik része az IPv6-alapú konfigurációs feladatok listáját tartalmazza. A kettős verem (IPv4 + IPv6) üzembe helyezése az Azure-ban, amely tartalmazza a kettős verem virtuális hálózat IPv4 és IPv6-alhálózattal rendelkező kettős (IPv4 + IPv6-alapú) előtérbeli konfigurációk esetén egy kettős IP-Címmel rendelkező hálózati adapterrel rendelkező virtuális gépek egy terheléselosztó a cikkben bemutatott sablon használatával konfiguráció, a hálózati biztonsági csoport és a nyilvános IP-címek. 

## <a name="required-configurations"></a>Szükséges konfigurációk

A sablon megtekintéséhez, ahol megtörténik a sablon szakaszokra kereshet.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6-címtartományt a virtuális hálózat

A sablonszakasznak hozzáadása:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Az IPv6-alapú virtuális hálózati címtartományt belül IPv6-alhálózatot

A sablonszakasznak hozzáadása:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>A hálózati adapter IPv6-konfigurációhoz

A sablonszakasznak hozzáadása:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6-alapú hálózati biztonsági csoport (NSG) szabályai

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Feltételes konfiguráció

Egy hálózati virtuális készüléket használja, ha az útválasztási táblázatban adja hozzá az IPv6-útvonalait. Ellenkező esetben ez a konfiguráció nem kötelező.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Választható beállítások

### <a name="ipv6-internet-access-for-the-virtual-network"></a>A virtuális hálózat IPv6-alapú Internet-hozzáférésének

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 Public IP addresses

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6-alapú előtér Load balancerhez

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Load Balancer IPv6 háttér-címkészlet

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6-alapú terheléselosztási szabályok a bejövő és kimenő portok társítása

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Minta Virtuálisgép-sablon JSON-ban
Kattintson a [Itt](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) az Azure Resource Manager-sablon használatával az Azure virtual network IPv6 kettős verem alkalmazás központi telepítése.

## <a name="next-steps"></a>További lépések

A díjszabással kapcsolatos információk [nyilvános IP-címek](https://azure.microsoft.com/pricing/details/ip-addresses/), [hálózati sávszélesség](https://azure.microsoft.com/pricing/details/bandwidth/), vagy [terheléselosztó](https://azure.microsoft.com/pricing/details/load-balancer/).
