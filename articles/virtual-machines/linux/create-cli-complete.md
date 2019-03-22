---
title: Linux-környezet létrehozása az Azure CLI-vel |} A Microsoft Docs
description: Hozzon létre a storage, Linux rendszerű virtuális gép, virtuális hálózat és alhálózat, egy terheléselosztó, egy hálózati Adaptert, nyilvános IP-cím és egy hálózati biztonsági csoportot, az alapoktól az Azure CLI-vel az összes.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: eb4c5897cdadecd074c2764faceeed13f4c724c3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008735"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Teljes Linux rendszerű virtuális gép létrehozása az Azure CLI-vel
Gyorsan létrehozhat egy virtuális gépet (VM) az Azure-ban, használhatja egy egyetlen Azure CLI-paranccsal, amely minden szükséges támogató erőforrások létrehozása az alapértelmezett értékeket használja. Erőforrások, például egy virtuális hálózat, nyilvános IP-cím és hálózati biztonsági csoport szabályait a rendszer automatikusan létrehozza. Az éles környezet még nagyobb mértékben használja, előfordulhat, hogy a kívánt időben az erőforrások létrehozásához, és hozzáadhatja őket a virtuális gépek. Ez a cikk végigvezeti egy virtuális Gépet, és a támogató erőforrások egyenként mindegyike létrehozása.

Győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) és az Azure-fiók [az bejelentkezési](/cli/azure/reference-index).

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők *myResourceGroup*, *myVnet*, és *myVM*.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A virtuális gépek és a támogató virtuális hálózati erőforrások előtt egy erőforráscsoportot kell létrehozni. Hozzon létre az erőforráscsoportot a [az csoport létrehozása](/cli/azure/group). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Alapértelmezés szerint az Azure CLI-parancsok kimenete JSON (JavaScript Object Notation) formátumban. Ha módosítani szeretné az alapértelmezett kimeneti listában vagy táblázat, például használata [konfigurálása az--output](/cli/azure/reference-index). Azt is megteheti `--output` egy ideje semmilyen parancs kimeneti formátum változtatható meg. Az alábbi példa bemutatja a JSON-kimenet a `az group create` parancsot:

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
Tovább az Azure és a egy alhálózatot a virtuális hálózatot hoz létre, amelyhez a virtuális gépeket hozhat létre. Használat [az network vnet létrehozása](/cli/azure/network/vnet) nevű virtuális hálózat létrehozása *myVnet* az a *192.168.0.0/16* cím előtagja. Nevű alhálózatot is hozzáadhat *mySubnet* a cím előtagja *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

A kimenet mutatja, hogy az alhálózat logikailag jön létre a virtuális hálózaton belül:

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
Most hozzunk létre egy nyilvános IP-címet [az network public-ip létrehozása](/cli/azure/network/public-ip). A nyilvános IP-cím lehetővé teszi, hogy az internetről a virtuális gépek csatlakozni. Mivel az alapértelmezett cím a dinamikus, hozzon létre egy elnevezett DNS-bejegyzést a a `--domain-name-label` paraméter. Az alábbi példa létrehoz egy nyilvános IP-cím nevű *myPublicIP* a DNS-nevét *mypublicdns*. Mivel a DNS-nevének egyedinek kell lennie, adja meg a saját egyedi DNS-nevet:

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
A virtuális gépek és gépből irányuló forgalom szabályozásához, a hálózati biztonsági csoport egy virtuális hálózati adapter vagy alhálózat a alkalmazni. Az alábbi példában [az network nsg létrehozása](/cli/azure/network/nsg) hozhat létre egy hálózati biztonsági csoport nevű *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Meghatározhat szabályokat, amelyek adott adatforgalom engedélyezéséhez vagy letiltásához. (Az SSH-hozzáférés engedélyezése) 22-es port bejövő kapcsolatok engedélyezéséhez hozzon létre egy bejövő szabályt, [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule). A következő példában létrehozunk egy nevű szabályt *myNetworkSecurityGroupRuleSSH*:

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

Ahhoz, hogy a bejövő kapcsolatokat a 80-as porton (a webes forgalom), adjon hozzá egy másik hálózati biztonsági csoportra vonatkozó szabályt. A következő példában létrehozunk egy nevű szabályt *myNetworkSecurityGroupRuleHTTP*:

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

Ellenőrizze a hálózati biztonsági csoport és a szabályok [az network nsg show](/cli/azure/network/nsg):

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
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
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

## <a name="create-a-virtual-nic"></a>Hozzon létre egy virtuális hálózati Adaptert
Virtuális hálózati adapterek (NIC) programozott módon érhető el, mert használati szabályokat alkalmazhat. Attól függően, a [Virtuálisgép-méret](sizes.md), több virtuális hálózati adapter csatlakoztatása egy virtuális Gépet. A következő [az network nic létrehozása](/cli/azure/network/nic) parancs létrehoz egy hálózati Adaptert *myNic* , és társíthatja azt a hálózati biztonsági csoport. A nyilvános IP-cím *myPublicIP* is kapcsolódik a virtuális hálózati adaptert.

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
Rendelkezésre állási a virtuális gépek csoportok súgó megállítását tartalék tartományokban és frissítési tartományok között. Annak ellenére, hogy csak egy virtuális Gépet hoz létre most, hogy egyszerűbb legyen a jövőben bontsa ki a rendelkezésre állási csoportok használata ajánlott eljárás. 

A tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportját határozza meg. Alapértelmezés szerint a rendelkezésre állási csoportba konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elkülönítve. A tartalék tartományok valamelyikében hardverprobléma minden virtuális gép, amelyen fut az alkalmazás nincs hatással.

Frissítési tartományok adja meg a csoportok a virtuális gépeket és mögöttes fizikai hardvereszközöket, amelyek egyszerre indíthatók újra. A tervezett karbantartás során melyik frissítés tartományok újraindulásakor sorrendje nem feltétlenül egymást követő, de csak egyetlen frissítési tartományt lehet újraindítani egyszerre.

Azure virtuális gépek automatikusan a tartalék és frissítési tartományok között osztja el, ha egy rendelkezésre állási csoportba helyezi őket. További információkért lásd: [virtuális gépek rendelkezésre állásának kezelése](manage-availability.md).

Hozzon létre egy rendelkezésre állási csoportot a virtuális gép [az virtuális gép rendelkezésre állási csoport létrehozása](/cli/azure/vm/availability-set). Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

A kimeneti megjegyzések tartalék tartományokban és frissítési tartományok:

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
A hálózati erőforrások az internethez csatlakozó virtuális gépek támogatásához létrehozott. Most hozzon létre egy virtuális Gépet, és biztonságos, az SSH-kulcsot. Ebben a példában hozzunk létre egy Ubuntu virtuális gép a legutóbbi LTS alapján. További rendszerképeket az annak [az virtuálisgép-lemezkép lista](/cli/azure/vm/image)leírtak szerint [Azure Virtuálisgép-rendszerképek keresése](cli-ps-findimage.md).

Adjon meg egy SSH-kulcsot a hitelesítéshez használandó. Ha a nyilvános ssh-kulcs nem rendelkezik, [hozza létre őket](mac-create-ssh-keys.md) vagy használja a `--generate-ssh-keys` paramétert kell létrehoznia őket. Ha már rendelkezik egy kulcsból álló kulcspárt, ezt a paramétert a meglévő kulcsokat használ-e `~/.ssh`.

A virtuális gép létrehozásához, és az erőforrások és információk együtt a [az virtuális gép létrehozása](/cli/azure/vm) parancsot. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre:

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

A virtuális gép nyilvános IP-cím létrehozásakor megadott DNS-bejegyzést az ssh-KAPCSOLATOT. Ez `fqdn` a virtuális gép létrehozásakor a kimenet látható:

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
    https://www.ubuntu.com/business/services/cloud

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

Telepítheti az nginx-et és a hálózati forgalom a virtuális géphez a folyamat. Nginx-et a következőképpen telepítheti:

```bash
sudo apt-get install -y nginx
```

Ha szeretné látni működés közben az alapértelmezett NGINX-webhely, nyissa meg a webböngészőjét, és adja meg a teljes Tartománynevet:

![Alapértelmezett NGINX helyet a virtuális Gépen](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportálás sablonként
Mi történik, ha most szeretné hozzon létre egy további fejlesztési környezetben ugyanazokat a paramétereket, vagy éles környezetben, amely megfelel azt? Resource Manager használ a környezet összes paramétereit meghatározó JSON-sablonok. A JSON-sablon hivatkozik felépítése teljes környezetek. Is [JSON-sablonok készítése manuálisan](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy egy meglévő környezetet hozza létre a JSON-sablon exportálása. Használat [az exportálása](/cli/azure/group) módon exportálhatja az erőforráscsoport:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Ez a parancs létrehozza a `myResourceGroup.json` fájlt az aktuális munkakönyvtárba. Ebből a sablonból létrehozott egy környezetet, ha a rendszer kéri az erőforrások nevei esetében. Fel lehet tölteni ezeket a neveket a sablon fájlban adja hozzá a `--include-parameter-default-value` paramétert a `az group export` parancsot. Az erőforrás nevének megadása a JSON-sablon szerkesztése vagy [hozzon létre egy parameters.json fájlban](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely meghatározza, hogy az erőforrások nevei.

Létrehoz egy környezetet a sablonból, használja a [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment) módon:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Előfordulhat, hogy szeretné olvasni [sablonokból történő központi telepítéséről további](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ismerje meg hogyan növekményes környezetek frissítse, használhatja a paramétereket tartalmazó fájlt, és sablonok egyetlen helyről.

## <a name="next-steps"></a>További lépések
Most már készen áll a több hálózati összetevők és a virtuális gépek használatának megkezdése előtt. Ez a minta-környezet használatával hozhatja létre az alkalmazás a bevezetett alapvető összetevők ide használatával.
