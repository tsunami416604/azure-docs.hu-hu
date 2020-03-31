---
title: IPv6-alapú kettős veremű alkalmazás üzembe helyezése az Azure virtuális hálózatában – Erőforrás-manger sablon (előzetes verzió)
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan telepíthet egy IPv6-alapú kettős veremű alkalmazást standard terheléselosztóval az Azure Resource Manager virtuális hálózati használatával az Azure Resource Manager virtuálisgép-sablonjai használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 8f2c6bc7fb7ab0939da20932fd531c158549ce7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012851"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template-preview"></a>IPv6-alapú kettős veremű alkalmazás üzembe helyezése az Azure virtuális hálózatában – Sablon (előzetes verzió)

Ez a cikk az IPv6-konfigurációs feladatok listáját tartalmazza az Azure Resource Manager virtuálisgép-sablon nak a vonatkozó részével. A cikkben ismertetett sablon segítségével kétverű (IPv4 + IPv6) alkalmazást telepíthet az Azure Standard Load Balancer használatával, amely kétverű virtuális hálózatot tartalmaz IPv4 és IPv6 alhálózatokkal, egy standard terheléselosztót kettős (IPv4 + IPv6) előtérrel konfigurációk, hálózati adapterekkel rendelkező virtuális gépek, amelyek kettős IP-konfigurációval, hálózati biztonsági csoporttal és nyilvános IP-címekkel rendelkeznek. 

## <a name="required-configurations"></a>Szükséges konfigurációk

Keresse meg a sablon sablonszakaszait, hogy lássa, hol kell történniük.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6 addressSpace a virtuális hálózathoz

Hozzáadandó sablonszakasz:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6-alhálózat az IPv6 virtuális hálózati címbenTér

Hozzáadandó sablonszakasz:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>IPv6-konfiguráció a hálózati adapterhez

Hozzáadandó sablonszakasz:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>NSG-szabályok Az IPv6 hálózati biztonsági csoport (NSG) szabályai

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

Ha hálózati virtuális berendezést használ, adja hozzá az IPv6-útvonalakat az útvonaltáblához. Ellenkező esetben ez a konfiguráció nem kötelező.

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

## <a name="optional-configuration"></a>Választható konfiguráció

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6 internet-hozzáférés a virtuális hálózathoz

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 nyilvános IP-címek

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 előtér a terheléselosztóhoz

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6 háttércímkészlet a terheléselosztóhoz

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6-os terheléselosztó-szabályok a bejövő és kimenő portok társítására

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

## <a name="sample-vm-template-json"></a>JSON mintavmsablon
Ha iPv6-alapú kettős veremű alkalmazást szeretne telepíteni az Azure-alapú virtuális hálózatban az Azure Resource Manager-sablon használatával, tekintse meg [itt](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/)a mintasablont.

## <a name="next-steps"></a>További lépések

A [nyilvános IP-címek](https://azure.microsoft.com/pricing/details/ip-addresses/), a [hálózati sávszélesség](https://azure.microsoft.com/pricing/details/bandwidth/)vagy a [terheléselosztó](https://azure.microsoft.com/pricing/details/load-balancer/)díjszabásáról részletesen ismertheti.
