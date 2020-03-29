---
title: Linux-környezet létrehozása az Azure CLI-vel
description: Hozzon létre tárolót, egy Linux virtuális gépet, egy virtuális hálózatot és egy alhálózatot, egy terheléselosztót, egy hálózati adaptert, egy nyilvános IP-t és egy hálózati biztonsági csoportot, mindezt az alapoktól kezdve az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 7ee4674f5e7c04709256459c3417a1379a65aedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969563"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Hozzon létre egy teljes Linux virtuális gépet az Azure CLI-vel
Virtuális gép (VM) gyors létrehozásához az Azure-ban, egyetlen Azure CLI-parancsot használhat, amely az alapértelmezett értékeket használja a szükséges támogató erőforrások létrehozásához. A rendszer automatikusan létrehozza az erőforrásokat, például a virtuális hálózatot, a nyilvános IP-címet és a hálózati biztonsági csoportszabályait. A környezet éles környezetben való további szabályozása érdekében előfordulhat, hogy ezeket az erőforrásokat előre, majd adja hozzá a virtuális gépeket. Ez a cikk végigvezeti, hogyan hozhat létre egy virtuális gép, és az egyes támogató erőforrások egyenként.

Győződjön meg arról, hogy telepítette a legújabb [Azure CLI-t,](/cli/azure/install-az-cli2) és bejelentkezett egy Azure-fiókba [az az bejelentkezéssel.](/cli/azure/reference-index)

A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa paraméter nevek közé *tartozik a myResourceGroup*, *myVnet*, és *myVM*.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A virtuális gép előtt létre kell hozni egy erőforráscsoportot, és támogatni a virtuális hálózati erőforrásokat. Hozza létre az erőforráscsoportot az [a csoport létrehozásával.](/cli/azure/group) A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Alapértelmezés szerint az Azure CLI-parancsok kimenete JSON (JavaScript-objektumjelölés). Ha például az alapértelmezett kimenetet listára vagy táblára szeretné módosítani, használja az [--output konfigurálása](/cli/azure/reference-index). A kimeneti `--output` formátum egyszeri módosításához bármely parancshoz hozzáadhat. A következő példa a `az group create` parancs JSON kimenetét mutatja be:

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
Ezután hozzon létre egy virtuális hálózatot az Azure-ban, és egy alhálózatot, amelyhez létrehozhatja a virtuális gépeket. Az [hálózati virtuális hálózat létrehozása](/cli/azure/network/vnet) segítségével hozzon létre egy virtuális hálózat nevű *myVnet* a *192.168.0.0/16* cím előtag. A *192.168.1.0/24*címelőtaggal egy *mySubnet* nevű alhálózatot is hozzáad:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

A kimenet azt mutatja, hogy az alhálózat logikailag jön létre a virtuális hálózaton belül:

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
Most hozzunk létre egy nyilvános IP-címet [az az hálózati nyilvános ip create](/cli/azure/network/public-ip). Ez a nyilvános IP-cím lehetővé teszi, hogy az internetről csatlakozzon a virtuális gépekhez. Mivel az alapértelmezett cím dinamikus, hozzon `--domain-name-label` létre egy elnevezett DNS-bejegyzést a paraméterrel. A következő példa létrehoz egy nyilvános IP nevű *myPublicIP* a DNS neve *mypublicdns*. Mivel a DNS-névnek egyedinek kell lennie, adja meg saját egyedi DNS-nevét:

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
A virtuális gépeken be- és kiáramló forgalom szabályozásához egy hálózati biztonsági csoportot kell alkalmazni a virtuális hálózati adapterre vagy alhálózatra. A következő példa [az az network nsg create](/cli/azure/network/nsg) segítségével hoz létre egy *myNetworkSecurityGroup*nevű hálózati biztonsági csoportot:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Olyan szabályokat határozmeg, amelyek engedélyezik vagy megtagadják az adott forgalmat. Ha engedélyezni szeretné a bejövő kapcsolatokat a 22-es porton (az SSH-hozzáférés engedélyezéséhez), hozzon létre egy bejövő szabályt [az az network nsg szabály létrehozása.](/cli/azure/network/nsg/rule) A következő példa létrehoz egy *myNetworkSecurityGroupRuleSSH*nevű szabályt:

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

Ha engedélyezni szeretné a bejövő kapcsolatokat a 80-as porton (webes forgalom esetén), adjon hozzá egy másik hálózati biztonsági csoportszabályt. A következő példa létrehoz egy *myNetworkSecurityGroupRuleHTTP*nevű szabályt:

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

Vizsgálja meg a hálózati biztonsági csoport és szabályok [az az hálózati nsg show:](/cli/azure/network/nsg)

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

## <a name="create-a-virtual-nic"></a>Virtuális hálózati adapter létrehozása
A virtuális hálózati csatolókártyák (NIC-k) programozott módon érhetők el, mivel a használatruk szabályokat alkalmazhat. A virtuális [gép méretétől](sizes.md)függően több virtuális hálózati adaptert is csatolhat egy virtuális géphez. A következő [az hálózati nic create](/cli/azure/network/nic) parancsban hozzon létre egy *myNic* nevű hálózati adaptert, és társítsa azt a hálózati biztonsági csoporthoz. A nyilvános IP-cím *myPublicIP* is kapcsolódik a virtuális NIC.

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
A rendelkezésre állási csoportok segítenek a virtuális gépek et a tartalék tartományok között és a frissítési tartományok között. Annak ellenére, hogy csak egy virtuális gép létrehozása most, célszerű a rendelkezésre állási csoportok használata, hogy megkönnyítse a bővítést a jövőben. 

A tartalék tartományok olyan virtuális gépek csoportosítását határozzák meg, amelyek közös áramforrással és hálózati kapcsolóval rendelkeznek. Alapértelmezés szerint a rendelkezésre állási csoporton belül konfigurált virtuális gépek legfeljebb három tartalék tartományban vannak elválasztva. A tartalék tartományok egyikében lévő hardveres probléma nem érinti az alkalmazást futtató összes virtuális gépre.

A frissítési tartományok a virtuális gépek és az alapul szolgáló fizikai hardverek csoportjait jelzik, amelyek egyidejűleg újraindíthatók. A tervezett karbantartás során előfordulhat, hogy a frissítési tartományok újraindításának sorrendje nem egymást követő, de egyszerre csak egy frissítési tartomány újraindul.

Az Azure automatikusan elosztja a virtuális gépeket a hiba és a tartományok frissítése, amikor azokat egy rendelkezésre állási csoportba helyezi. További információt a virtuális gépek elérhetőségének kezelése című [témakörben talál.](manage-availability.md)

Hozzon létre egy rendelkezésre állási készletet a virtuális gép hez az [virtuálisgép rendelkezésre állási készletének létrehozása.](/cli/azure/vm/availability-set) Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

A kimeneti megjegyzések tartalék tartományai és frissítési tartományai:

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
A hálózati erőforrásokat az internetről elérhető virtuális gépek támogatásához hozta létre. Most hozzon létre egy virtuális gép, és biztonságos, hogy egy SSH-kulcs. Ebben a példában hozzon létre egy Ubuntu virtuális gép a legutóbbi LTS alapján. További képeket az [az vm-lemezképlistával](/cli/azure/vm/image)talál, az [Azure VM-lemezképek keresése](cli-ps-findimage.md)című részben leírtak szerint.

Adja meg a hitelesítéshez használandó SSH-kulcsot. Ha nem rendelkezik SSH nyilvános kulcspárral, [létrehozhatja őket,](mac-create-ssh-keys.md) vagy a `--generate-ssh-keys` paraméter segítségével létrehozhatja őket. Ha már van kulcspárja, ez a `~/.ssh`paraméter a meglévő kulcsokat használja a alkalmazásban.

Hozza létre a virtuális gép azáltal, hogy az összes erőforrást és információt együtt az [az vm create](/cli/azure/vm) parancsot. A következő példa létrehoz egy *myVM*nevű virtuális gép:

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

SSH a virtuális gép a DNS-bejegyzés, amikor létrehozta a nyilvános IP-címet. Ez `fqdn` a virtuális gép létrehozásakor jelenik meg a kimenetben:

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

Telepítheti az NGINX-et, és megtekintheti a virtuális gép hez irányuló forgalmat. Telepítse az NGINX-et az alábbiak szerint:

```bash
sudo apt-get install -y nginx
```

Az alapértelmezett NGINX-webhely működés közbeni megtekintéséhez nyissa meg a webböngészőt, és adja meg a teljes tartománynértéket:

![Alapértelmezett NGINX-webhely a virtuális gépen](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportálás sablonként
Mi a teendő, ha most egy további fejlesztői környezetet szeretne létrehozni ugyanazzal a paraméterrel, vagy egy olyan éles környezetet, amely megfelel az adott nak? Az Erőforrás-kezelő JSON-sablonokat használ, amelyek meghatározzák a környezet összes paraméterét. A JSON-sablonra hivatkozva teljes környezeteket hozhat létre. A [JSON-sablonokat manuálisan is létrehozhatja,](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy exportálhatja a meglévő környezetet a JSON-sablon létrehozásához. Az [csoportexportálás](/cli/azure/group) sal exportálhatja az erőforráscsoportot az alábbiak szerint:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Ez a `myResourceGroup.json` parancs létrehozza a fájlt az aktuális munkakönyvtárban. Amikor ebből a sablonból hoz létre egy környezetet, a program az összes erőforrásnevet kéri. Ezeket a neveket a sablonfájlban `--include-parameter-default-value` úgy feltöltheti, hogy hozzáadja a paramétert a `az group export` parancshoz. A JSON-sablon szerkesztésével adja meg az erőforrásneveket, vagy [hozzon létre egy parameters.json fájlt,](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) amely megadja az erőforrásneveket.

Ha környezetet szeretne létrehozni a sablonból, használja [az az csoport központi telepítési létrehozását](/cli/azure/group/deployment) az alábbiak szerint:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Érdemes lehet többet megtudni [asablonokból történő telepítésről.](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Megtudhatja, hogyan frissítheti a környezeteket, hogyan használhatja a paraméterfájlt, és hogyan érheti el a sablonokat egyetlen tárolóhelyről.

## <a name="next-steps"></a>További lépések
Most már készen áll arra, hogy több hálózati összetevővel és virtuális gépekkel dolgozzon. Ezt a mintakörnyezetet használhatja az alkalmazás létrehozásához az itt bemutatott alapvető összetevők használatával.
