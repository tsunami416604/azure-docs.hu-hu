---
title: Hozzon létre egy Linux-környezet az Azure CLI 2.0 |} Microsoft Docs
description: Tárolási, Linux virtuális gép, egy virtuális hálózati és alhálózati, egy adott terheléselosztóhoz, egy olyan hálózati adapter, egy nyilvános IP-cím és a hálózati biztonsági csoport, az Azure CLI 2.0 használatával alapoktól összes létrehozása.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: f41bfec3c9f950893b69c90a86c2e4a254b72a8b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852132"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Teljes Linux virtuális gép létrehozása az Azure parancssori felülettel
Gyorsan létrehozhat egy virtuális gép (VM) az Azure-ban, egy Azure CLI parancs, amely az alapértelmezett értékeket használja, minden szükséges támogató erőforrások létrehozására használhatja. Erőforrások, például a virtuális hálózat, a nyilvános IP-cím és a hálózati biztonsági csoportszabályok automatikusan jönnek létre. Nagyobb mértékben vezérelheti a az éles környezetben használja, előfordulhat, hogy ezeket az erőforrásokat előre létrehozni, majd a virtuális gépek hozzá őket. Ez a cikk végigvezeti egy virtuális Gépet, és mindegyik egyenként támogató erőforrás létrehozása.

Győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) jelentkezett be az Azure-fiókot és [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők *myResourceGroup*, *myVnet*, és *myVM*.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Egy erőforráscsoportot a virtuális gépek és a támogató virtuális hálózati erőforrások előtt létre kell hozni. Az erőforráscsoport létrehozása [az csoport létrehozása](/cli/azure/group#az_group_create). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Alapértelmezés szerint az Azure parancssori felület parancsait eredménye a JSON-ban (JavaScript Object Notation). Ha módosítani szeretné az alapértelmezett való küldéséhez lista vagy táblázat, például használja [konfigurálása az – output](/cli/azure/reference-index#az_configure). Azt is megteheti `--output` kimeneti formátumban módosítsa valamelyik parancsának egy ideig. A következő példa bemutatja a JSON-kimenetét a `az group create` parancs:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása
Tovább létre virtuális hálózatot az Azure és az alhálózatot, amelyhez a virtuális gépeket hozhat létre. Használjon [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create) nevű virtuális hálózat létrehozása *myVnet* rendelkező a *192.168.0.0/16* címelőtagot. Nevű alhálózat is hozzáadhat *mySubnet* a címelőtagot rendelkező *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Az alábbiakat mutatja be, az alhálózati logikailag jön létre a virtuális hálózaton belül:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Most hozzon létre egy nyilvános IP-cím [létrehozása az hálózati nyilvános ip-](/cli/azure/network/public-ip#az_network_public_ip_create). A nyilvános IP-cím lehetővé teszi a virtuális gépek csatlakozni az internetről. Mivel az alapértelmezett cím dinamikus, hozzon létre egy elnevezett DNS-bejegyzést a a `--domain-name-label` paraméter. Az alábbi példa létrehoz egy nyilvános IP-cím nevű *myPublicIP* a DNS-nevét *mypublicdns*. Mivel a DNS-nevének egyedinek kell lennie, adja meg a saját egyedi DNS-név:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Kimenet:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
A forgalmat a virtuális gépek mindkét szabályozásához alkalmaz a hálózati biztonsági csoport a virtuális hálózati adapter vagy alhálózat. Az alábbi példában [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create) hozhat létre a hálózati biztonsági csoport nevű *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Megadhatja a szabályokat, amelyek adott adatforgalom engedélyezéséhez vagy letiltásához. (Az SSH-hozzáférés engedélyezése) 22-es portot a bejövő kapcsolatok engedélyezéséhez hozzon létre egy bejövő forgalomra vonatkozó szabály a [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Az alábbi példa létrehoz egy nevű szabályt *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

(A webes forgalomban) 80-as portot a bejövő kapcsolatok engedélyezéséhez vegyen fel egy másik hálózati biztonsági csoport szabály. Az alábbi példa létrehoz egy nevű szabályt *myNetworkSecurityGroupRuleHTTP*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Ellenőrizze a hálózati biztonsági csoport és a szabályok [az hálózati nsg megjelenítése](/cli/azure/network/nsg#az_network_nsg_show):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Kimenet:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>A virtuális hálózati adapter létrehozása
Virtuális hálózati adapterek (NIC), ezért programozott módon való használatukat alkalmazhat. Attól függően a [Virtuálisgép-méretet](sizes.md), több virtuális hálózati adapter csatlakoztatása egy virtuális Gépet. Az alábbi [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create) parancsban, létrehozhat egy hálózati adapter nevű *myNic* és rendelje hozzá azt a hálózati biztonsági csoport. A nyilvános IP-cím *myPublicIP* is kapcsolódik a virtuális hálózati adaptert.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Kimenet:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Rendelkezésre állási a virtuális gépek készletek súgó terjedésének tartalék tartományok és a frissítés tartományokban. Annak ellenére, hogy csak egy virtuális gép most létrehozni, akkor célszerű könnyebben bontsa ki a jövőben a rendelkezésre állási készletek használatával. 

Tartalék tartományok definiálása, amelyek egy közös forrás- és hálózati kikapcsolás virtuális gépek csoportja. Alapértelmezés szerint a rendelkezésre állási csoport belül konfigurált virtuális gépek egymástól legfeljebb három tartalék tartományokban. A tartalék tartományok valamelyikében egy hardver a probléma nem érinti az alkalmazást futtató minden VM.

Frissítési tartományok adja meg a virtuális gépek és a mögöttes fizikai hardver, amely egy időben újra kell indítani. Tervezett karbantartás során a melyik frissítési tartományok újraindítása van sorrendje nem feltétlenül egymást követő, de egyszerre csak egy frissítési tartományt újraindítása után.

Amikor a rendelkezésre állási csoportba helyezi őket Azure automatikusan elosztása virtuális gépek a hiba, és a frissítési tartományok. További információkért lásd: [virtuális gépek rendelkezésre állásának kezelése](manage-availability.md).

Hozzon létre egy rendelkezésre állási készletét, a virtuális Gépet a [az virtuális gép rendelkezésre állási-csoport létrehozása](/cli/azure/vm/availability-set#az_vm_availability_set_create). Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

A kimeneti megjegyzések tartalék tartományok és a frissítési tartományok:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Virtuális gép létrehozása
A hálózati erőforrásokhoz az internetről elérhető virtuális gépek támogatásához létrehozott. Most hozzon létre egy virtuális Gépet, és biztosíthatja az SSH-kulcsot. Ebben a példában hozzon létre egy Ubuntu virtuális gép a legutóbbi LTS alapján. További képekkel található [az vm képlistában](/cli/azure/vm/image#az_vm_image_list)leírtak szerint [Azure Virtuálisgép-rendszerképekről keresése](cli-ps-findimage.md).

Adjon meg egy SSH-kulcsot a hitelesítéshez használandó. Ha még nem rendelkezik az SSH nyilvános kulcsból álló kulcspárt, akkor [hozza létre a címzetteket](mac-create-ssh-keys.md) , vagy használja a `--generate-ssh-keys` paraméter kell létrehoznia őket. Ha már rendelkezik egy kulcspár, ezt a paramétert a meglévő kulcsokat használ-e `~/.ssh`.

A virtuális gép létrehozása az erőforrások és információk együtt hozásával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH-kapcsolatot a virtuális gép létrehozása után a nyilvános IP-cím a megadott DNS-bejegyzés. Ez `fqdn` a virtuális gép létrehozásakor a kimenet látható:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Kimenet:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

NGINX telepítheti, és a hálózati forgalom a virtuális gép folyamata. Telepítse a NGINX az alábbiak szerint:

```bash
sudo apt-get install -y nginx
```

A művelet alapértelmezett NGINX hely megtekintéséhez nyissa meg a webböngészőt, és adja meg a teljes Tartománynevét:

![Alapértelmezett NGINX helyet a virtuális gépen](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Sablonként exportálja
Mi történik, ha szeretné a paramétereket, vagy éles környezetben további fejlesztési környezet létrehozása, amely megfelel az? Erőforrás-kezelő a környezet összes paramétereit meghatározó JSON-sablonokat használ. A JSON-sablon Vezérlőpultjának kimenő teljes környezetek létrehozása. Is [JSON sablonok létrehozása manuálisan](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy egy meglévő környezet hozza létre a JSON-sablon exportálása. Használjon [az exportálása](/cli/azure/group#az_group_export) az erőforráscsoport exportálása az alábbiak szerint:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Ezzel a paranccsal létrejön az `myResourceGroup.json` az aktuális munkakönyvtárban fájlban. Ha a sablon alapján hoz létre egy olyan környezetben, az összes erőforrás nevét kéri. Töltheti fel ezeket a neveket a sablon fájlban adja hozzá a `--include-parameter-default-value` paramétert a `az group export` parancsot. Az erőforrás nevének megadása a JSON-sablon szerkesztése vagy [parameters.json fájl létrehozása](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely meghatározza, hogy az erőforrás nevét.

A sablon olyan környezetet hozhat létre, [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create) az alábbiak szerint:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Előfordulhat, hogy szeretné olvasni [felügyeleticsomag-sablonok telepítésével kapcsolatos további](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). További tudnivalók Növekményesen környezetek frissítése, használja a paraméterek fájlját, és egyetlen tárolási helyen sablonok elérésére.

## <a name="next-steps"></a>További lépések
Most már készen áll a több hálózati összetevőkkel és virtuális gépek használatának megkezdése előtt. Ez a minta-környezet segítségével bevezetett alapösszetevőket itt használatával, az alkalmazás létrehozása.
