---
title: Teljes Linux-környezet létrehozása a klasszikus Azure CLI-vel |} A Microsoft Docs
description: Hozzon létre a storage, Linux rendszerű virtuális gép, virtuális hálózat és alhálózat, egy terheléselosztó, egy hálózati Adaptert, nyilvános IP-cím és egy hálózati biztonsági csoportot, mind az alapoktól az Azure klasszikus parancssori felület használatával.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: d0a38defe41ea7c4e0da32cb73cf2bd73fd80950
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498210"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Teljes Linux-környezet létrehozása a klasszikus Azure CLI-vel
Ebben a cikkben egy egyszerű hálózati terheléselosztó és a virtuális gépek, amelyek hasznosak a fejlesztési és egyszerű számítástechnika párjai készítünk. A folyamat által parancs parancsot, amíg nincs két működő, biztonságos Linux virtuális gépet, amelyhez csatlakozhat bárhol az interneten azt bemutatására. Majd továbbléphet összetettebb hálózatokhoz és környezeteket.

A vizualizáción, ismerje meg a függőség-hierarchia, hogy a Resource Manager üzembe helyezési modellt biztosít, és mekkora kapcsolatos meghajtójába biztosít. Miután látta, hogyan épül fel a rendszer, újraépítését, sokkal gyorsabban használatával [Azure Resource Manager-sablonok](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Emellett után megtudhatja, hogyan működnek a környezet részeit együtt, egyszerűbb automatizálhatják is azokat a sablonok létrehozásával válik.

A környezet tartalmazza:

* Két virtuális gép rendelkezésre állási csoportban.
* Egy terheléselosztó, terheléselosztási szabállyal a 80-as porton.
* Hálózati biztonsági csoport (NSG) szabályai a kéretlen forgalmat a virtuális gép védelmét.

Az egyéni környezet létrehozásához, a legújabb kell [Azure klasszikus parancssori felület](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) erőforrás-kezelő módban (`azure config mode arm`). Egy JSON-elemzési eszközt is szükséges. Ez a példa [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure klasszikus parancssori felület](#quick-commands) – parancssori felületünk a klasszikus és a resource management üzemi modellekhez (a jelen cikkben)
- [Az Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a parancssori következő generációját képviseli a resource management üzemi modellhez tartozó


## <a name="quick-commands"></a>Gyors parancsok
Ha szeretne gyorsan elvégezni a feladatot, a következő szakaszban részletek alap parancsok VM feltöltése az Azure-bA. Részletes információkat és a környezetben az egyes lépések indítása, a dokumentum többi részén található [Itt](#detailed-walkthrough).

Győződjön meg arról, hogy rendelkezik-e [a klasszikus Azure-CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) bejelentkezett, és a Resource Manager-módban:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.

Hozza létre az erőforráscsoportot. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Az erőforráscsoport ellenőrizze a JSON-elemző használatával:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

A tárfiók létrehozásához. Az alábbi példa létrehoz egy tárfiókot, nevű `mystorageaccount`. (A tárfiók nevének egyedinek kell lenniük, így a saját egyedi névre rendelkeznek.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Ellenőrizze a tárfiók a JSON-elemző használatával:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Hozza létre a virtuális hálózatot. A következő példában létrehozunk egy nevű virtuális hálózatot `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Hozzon létre egy alhálózatot. A következő példában létrehozunk egy nevű alhálózatot `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

A JSON-elemző, ellenőrizze a virtuális hálózatot és alhálózatot:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Hozzon létre egy nyilvános IP-címet. Az alábbi példa létrehoz egy nyilvános IP-cím nevű `myPublicIP` a DNS-nevét `mypublicdns`. (A DNS-név egyedinek kell lenniük, így a saját egyedi névre rendelkeznek.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

A load balancer létrehozása. A következő példában létrehozunk egy nevű terheléselosztót `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

A terheléselosztó előtérbeli IP-címkészlet létrehozása, és társítsa a nyilvános IP-cím. A következő példában létrehozunk egy nevű előtérbeli IP-címkészlet `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

A terheléselosztó háttér-IP-készlet létrehozása. A következő példában létrehozunk egy nevű háttérbeli IP-címkészlet `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Bejövő SSH hálózati címfordítás (NAT) szabályait a terheléselosztó létrehozásához. Az alábbi példa létrehoz két load balancer-szabályok, `myLoadBalancerRuleSSH1` és `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

A webes bejövő NAT-szabályok a terheléselosztó számára. A következő példában létrehozunk egy nevű terheléselosztási szabályt `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

A terheléselosztó állapotmintája létrehozása. A következő példában létrehozunk egy TCP-mintavétel nevű `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

A JSON-elemző, ellenőrizze a terheléselosztó IP-készletek és a NAT-szabályok:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Hozzon létre az első hálózati kártya (NIC). Cserélje le a `#####-###-###` szakaszok a saját Azure-előfizetés azonosítójára. Az előfizetés-azonosító kimenetét megadó **jq** hoz létre az erőforrások vizsgálata során. Megtekintheti a saját előfizetés-Azonosítójára `azure account list`.

A következő példában létrehozunk egy hálózati Adaptert `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Hozzon létre a második hálózati adaptert. A következő példában létrehozunk egy hálózati Adaptert `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

A JSON-elemző, ellenőrizze a két hálózati adapter:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

A hálózati biztonsági csoport létrehozása. A következő példában létrehozunk egy hálózati biztonsági csoport nevű `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Adjon hozzá két bejövő szabályt a hálózati biztonsági csoport. Az alábbi példa létrehoz két szabály `myNetworkSecurityGroupRuleSSH` és `myNetworkSecurityGroupRuleHTTP`:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Ellenőrizze a hálózati biztonsági csoport és a bejövő szabályok a JSON-elemző használatával:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

A hálózati biztonsági csoport kötést létrehozni a két hálózati adapter:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

A rendelkezésre állási csoport létrehozása. A következő példában létrehozunk egy rendelkezésre állási csoport elnevezett `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Az első Linux virtuális gép létrehozása. A következő példában létrehozunk egy nevű virtuális Gépet `myVM1`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

A második Linux rendszerű virtuális gép létrehozásához. A következő példában létrehozunk egy nevű virtuális Gépet `myVM2`:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Használja a JSON-elemző, ellenőrizze, hogy mindent, ami lett létrehozva:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Az új környezet gyorsan új példányokat újra létre sablon exportálása:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Részletes bemutató
A részletes lépések ismertetik, hogy milyen minden parancs módon ki a környezet létrehozása. Ezek a fogalmak hasznosak, a saját egyéni fejlesztési vagy éles üzemi környezetek készítése során.

Győződjön meg arról, hogy rendelkezik-e [a klasszikus Azure-CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) bejelentkezett, és a Resource Manager-módban:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Erőforráscsoportok létrehozása és központi telepítési helyek kiválasztása
Azure-erőforráscsoportok logikai telepítési entitások, amelyek tartalmazzák a konfigurációs adatokat és a metaadatok a logikai központi erőforrás-kezelés engedélyezése a rendszer. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Kimenet:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
Storage-fiókok VM-lemezeit, és a hozzáadni kívánt további adatlemezt kell. Storage-fiókok szinte azonnal erőforráscsoportok létrehozását követően hozzon létre.

Jelen példában használjuk a `azure storage account create` parancsot, és átadja a fiók, az erőforráscsoport helye, amely szabályozza, és a kívánt storage-támogatás típusát. Az alábbi példa létrehoz egy tárfiókot, nevű `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Kimenet:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Segítségével megvizsgálhatja a létrehoztuk az erőforráscsoportot a `azure group show` parancshoz, használja a [jq](https://stedolan.github.io/jq/) az eszköz a `--json` Azure CLI-vel lehetőséget. (Használható **jsawk** vagy bármilyen nyelv tár inkább a JSON elemzéséhez.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Kimenet:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

A parancssori felület használatával a tárfiók vizsgálatához, először állítsa be a neveivel és kulcsaival. Az alábbi példában a tárfiók nevére cserélje le egy Ön által választott név:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Ezután egyszerűen megtekintheti a tároló adatait:

```azurecli
azure storage container list
```

Kimenet:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása
Ezután fog létre kell hoznia egy virtuális hálózaton futó Azure és a egy alhálózatot, amelyben a virtuális gépeket hozhat létre. A következő példában létrehozunk egy nevű virtuális hálózatot `myVnet` együtt a `192.168.0.0/16` cím előtagja:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Kimenet:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Újra, használja a--json lehetőség a `azure group show` és `jq` hogyan hozunk létre az erőforrások megtekintéséhez. Most már rendelkezik egy `storageAccounts` erőforrás és a egy `virtualNetworks` erőforrás.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Kimenet:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Most hozzunk létre egy alhálózatot az a `myVnet` virtuális hálózatot, amelybe a virtuális gépek vannak telepítve. Használjuk a `azure network vnet subnet create` parancsot, valamint az erőforrásokat, hogy már létrehozott: a `myResourceGroup` erőforráscsoportot és a `myVnet` virtuális hálózatot. A következő példában hozzáadjuk a nevű alhálózat `mySubnet` a megadott alhálózati cím előtagja a `192.168.1.0/24`:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Kimenet:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Mivel az alhálózat logikailag a virtuális hálózaton belül, az alhálózati információ egy némileg eltérő parancs áttekintjük. A parancs az általunk használt `azure network vnet show`, de folyamatosan vizsgálja meg a JSON-kimenet segítségével `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Kimenet:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Most hozzunk létre nyilvános IP-cím (PIP), hogy a terheléselosztó rendel. Ez lehetővé teszi, hogy csatlakozhat a virtuális gépek az internetről a `azure network public-ip create` parancsot. Mivel az alapértelmezett cím a dinamikus, létrehozunk egy elnevezett DNS-bejegyzést a **cloudapp.azure.com** tartomány használatával a `--domain-name-label` lehetőséget. Az alábbi példa létrehoz egy nyilvános IP-cím nevű `myPublicIP` a DNS-nevét `mypublicdns`. A DNS-nevének egyedinek kell lennie, mert a saját egyedi DNS-nevet adja meg:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Kimenet:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

A nyilvános IP-cím is legfelsőbb szintű erőforráshoz, így láthatja azt `azure group show`.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Kimenet:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Segítségével megvizsgálhatja, további erőforrás részletei, beleértve a együtt, teljesen minősített tartománynevét (FQDN) használatával a teljes `azure network public-ip show` parancsot. A nyilvános IP-cím erőforrás logikailag lett lefoglalva, de egy adott cím még nem rendelt. IP-cím beszerzéséhez fog kell egy terheléselosztó, amely azt még nem hozott létre.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Kimenet:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Hozzon létre egy terheléselosztó és az IP-készletek
Amikor létrehoz egy terheléselosztót, lehetővé teszi több virtuális gép között osztja el a forgalmat. Az alkalmazás redundanciájának karbantartási vagy nagy terheléseket esetén a felhasználói kérésekre válaszol több virtuális gép futtatásával is tartalmazza. A következő példában létrehozunk egy nevű terheléselosztót `myLoadBalancer`:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Kimenet:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

A terheléselosztó viszonylag üres, így most már létrehozhatja az egyes IP-címkészleteket. Hozzon létre két IP-címkészleteket a terheléselosztóhoz, egyet az előtér és a egy, a háttér szeretnénk. Az előtérbeli IP-címkészlet nyilvánosan látható. Emellett akkor is, amelyhez hozzárendeljük a korábban létrehozott PIP helyét. Ezután használjuk a háttérkészlet olyan hely, a virtuális gépekhez való csatlakozáshoz. Ezzel a módszerrel a forgalmat a virtuális gépeket, hogy a terheléselosztó áthaladhat.

Először is hozzunk létre az előtérbeli IP-címkészlet. A következő példában létrehozunk egy nevű előtérbeli címkészletet `myFrontEndPool`:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Kimenet:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Vegye figyelembe, hogyan használtuk a `--public-ip-name` váltson át a `myPublicIP` korábban létrehozott. A terheléselosztó nyilvános IP-cím hozzárendelése lehetővé teszi, hogy elérje a virtuális gépek az interneten keresztül.

Következő lépésként hozzunk létre a második IP-készlet, ezúttal a háttér-forgalom számára. A következő példában létrehozunk egy háttér-címkészletet `myBackEndPool`:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Kimenet:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Láthatjuk, hogy a terheléselosztó állapotát hogyan azáltal, hogy megtekinti a `azure network lb show` és vizsgálata a JSON-kimenetet:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Kimenet:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Terheléselosztó NAT-szabályok létrehozása
Az adatforgalom a terheléselosztón keresztül lekéréséhez kell létrehozni a hálózati cím címfordítás (NAT) szabályokat adja meg a bejövő vagy kimenő műveletek. Adja meg a használni kívánt protokollt, majd külső leképezik a belső portokra igény szerint. A környezetben hozzunk létre bizonyos szabályokat, amelyek lehetővé teszik az SSH a virtuális gépekhez a terheléselosztón keresztül. Beállítottuk 4222 és 4223 TCP-portok számára a 22-es TCP-portot, a virtuális gépeken (ez később hozunk létre). A következő példában létrehozunk egy nevű szabályt `myLoadBalancerRuleSSH1` TCP-port 4222 leképezése a 22-es port:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Kimenet:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Ismételje meg az eljárást a második NAT-szabály az SSH-hoz. A következő példában létrehozunk egy nevű szabályt `myLoadBalancerRuleSSH2` TCP-port 4223 leképezése a 22-es port:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Nézzük is lépjen tovább, és hozzon létre egy NAT-szabályt a 80-as TCP-portot a webes forgalom, a szabály összekapcsolja az IP-készletek legfeljebb. Ha tudjuk kapcsolni a szabályt, hogy egy IP-címkészletet, ahelyett, hogy külön-külön, a szabály, amely a virtuális gépek csatlakoztatása a Microsoft adja hozzá, vagy távolítsa el a virtuális gépek IP-készletből. A load balancer automatikusan beállítja a forgalmat. A következő példában létrehozunk egy nevű szabályt `myLoadBalancerRuleWeb` 80-as porton leképezése a 80-as port:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Kimenet:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Terheléselosztó állapotmintájának létrehozása
Egy-állapotminta rendszeres időközönként ellenőrzi, hogy van működő és válaszol a kérelmekre, ahogyan a terheléselosztó mögötti virtuális gépeken. Ha nem, a műveletet, győződjön meg arról, hogy felhasználók nem irányítja őket törlik őket. Egyéni keres az állapotminta időközönként és időkorlát-értékei együtt adhatja meg. Állapot-mintavételei kapcsolatos további információkért lásd: [terheléselosztó a mintavételezők](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A következő példában létrehozunk egy TCP állapotát a vizsgált elnevezett `myHealthProbe`:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Kimenet:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Itt hogy az állapot-ellenőrzések 15 másodperces intervallumban megadott. Azt is hagyja ki a legfeljebb négy mintavételek (egy perces) a terheléselosztót úgy véli, hogy a gazdagép már nem működik.

## <a name="verify-the-load-balancer"></a>Ellenőrizze a terheléselosztó
Most már a terheléselosztói konfigurációban történik. A lépéseket, az alábbiakban:

1. Létrehozott egy terheléselosztót.
2. Előtérbeli IP-címkészlet létrehozott és hozzárendelt nyilvános IP-cím.
3. Létrehozott egy háttér-IP-címkészlet, amely a virtuális gépek csatlakozhatnak.
4. NAT-szabályok, amelyek lehetővé teszik a virtuális gépek kezelésére, és a egy szabályt, amely lehetővé teszi, hogy a 80-as TCP-port webalkalmazásunkhoz tartozó ssh-KAPCSOLATOT hozott létre.
5. A virtuális gépek rendszeres időnként ellenőrzik az állapotfigyelő mintavételező hozzáadott. Ez az állapotfigyelő mintavételező biztosítja, hogy felhasználók ne próbálja meg elérni a virtuális gép már nem működik vagy a tartalmat szolgáltató.

A terheléselosztó néz egyelőre tekintsük át:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Kimenet:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Hozzon létre egy hálózati Adaptert a Linux rendszerű virtuális gép használata
Hálózati adapterek programozott módon érhető el, mert használati szabályokat alkalmazhat. Egynél több is rendelkezhet. A következő `azure network nic create` parancsot, a hálózati Adaptert a terhelés háttér-IP-címkészlethez kapcsolni, és társítsa a NAT-szabály engedélyezi az SSH-forgalmat.

Cserélje le a `#####-###-###` szakaszok a saját Azure-előfizetés azonosítójára. Az előfizetés-azonosító kimenetét megadó `jq` hoz létre az erőforrások vizsgálata során. Megtekintheti a saját előfizetés-Azonosítójára `azure account list`.

A következő példában létrehozunk egy hálózati Adaptert `myNic1`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Kimenet:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

A részletek láthatja az erőforrás közvetlenül megvizsgálásával. Az erőforrás használatával vizsgálja meg a `azure network nic show` parancsot:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Kimenet:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Most létre a második hálózati adapter, összekapcsolja a ismét a a háttérbeli IP-címkészletet. Ez alkalommal a második NAT-szabály lehetővé teszi, hogy SSH-forgalmat. A következő példában létrehozunk egy hálózati Adaptert `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>A hálózati biztonsági csoport és a szabályok létrehozása
Most létrehozunk egy hálózati biztonsági csoport és a bejövő szabályok, amelyek szabályozzák a hozzáférést a hálózati adaptert. Egy hálózati adapter vagy az alhálózat egy hálózati biztonsági csoport is alkalmazható. Meghatározhat szabályokat a virtuális gépek és gépből irányuló forgalom szabályozásához. A következő példában létrehozunk egy hálózati biztonsági csoport nevű `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vegyünk fel a bejövő szabályt az NSG (a támogatásához SSH) 22-es port bejövő kapcsolatok engedélyezése érdekében. A következő példában létrehozunk egy nevű szabályt `myNetworkSecurityGroupRuleSSH` számára a TCP 22-es port engedélyezéséhez:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Most adjuk hozzá a bejövő szabályt az NSG (a webes forgalom támogatásához) 80-as porton bejövő kapcsolatok engedélyezése érdekében. A következő példában létrehozunk egy nevű szabályt `myNetworkSecurityGroupRuleHTTP` számára a TCP 80-as port engedélyezéséhez:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> A bejövő szabály a bejövő hálózati kapcsolatok szűrő. Ebben a példában a virtuális gépek virtuális hálózati adapterre, ami azt jelenti, hogy a 22-es port bármely kérelem kerül át a hálózati Adaptert a virtuális gépen azt köthető az NSG-t. A bejövő szabály a hálózati kapcsolat, és nem kapcsolatos, a végpont melyik tárolókezelésének kapcsolatban a klasszikus üzemi modellben. Port megnyitásához, meg kell hagyni a `--source-port-range` beállítása "\*" (az alapértelmezett érték) a bejövő kérelmek fogadásához **bármely** port kér. Ezek általában a dinamikus.
>
>

## <a name="bind-to-the-nic"></a>A hálózati Adaptert kösse
Az NSG-t a hálózati adapterekhez kötni. A hálózati adapter csatlakoztatása a hálózati biztonsági csoporttal kell. Futtassa mindkét parancsokat, mind a hálózati adapterek kapcsolni:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Rendelkezésre állási a virtuális gépek csoportok súgó megállítását tartalék tartományokban és frissítési tartományok között. Hozzunk létre egy rendelkezésre állási csoportot a virtuális gépek számára. A következő példában létrehozunk egy rendelkezésre állási csoport elnevezett `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

A tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportját határozza meg. Alapértelmezés szerint a rendelkezésre állási csoportba konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elkülönítve. A cél pedig az, hogy a tartalék tartományok valamelyikében hardverprobléma minden virtuális gép, amelyen fut az alkalmazás nem befolyásolja. Azure virtuális gépek automatikusan a tartalék tartomány között osztja el, ha egy rendelkezésre állási csoportba helyezi őket.

Frissítési tartományok adja meg a csoportok a virtuális gépeket és mögöttes fizikai hardvereszközöket, amelyek egyszerre indíthatók újra. A sorrendet, amelyben a frissítési tartományok újraindítása is lehetséges, hogy nem szekvenciális tervezett karbantartás során, de csak egy frissítés lehet újraindítani egyszerre. Ismét az Azure automatikusan elosztja a virtuális gépek frissítési tartományok között, ha egy rendelkezésre állási helyen helyezi őket.

Tudjon meg többet [virtuális gépek rendelkezésre állásának kezelése](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>A Linux rendszerű virtuális gépek létrehozása
A tárolási és hálózati erőforrások internethez csatlakozó virtuális gépek támogatásához létrehozott. Most nézzük azokat, virtuális gépek létrehozása és védheti meg az SSH-kulcsot, amely nem rendelkezik a jelszót. Ebben az esetben fogunk létrehozni egy Ubuntu virtuális gép a legutóbbi LTS alapján. Tudjuk, hogy a kép adatait keresse meg a `azure vm image list`leírtak szerint [Azure Virtuálisgép-rendszerképek keresése](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A parancs segítségével képet kiválasztott `azure vm image list westeurope canonical | grep LTS`. Ebben az esetben használjuk `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Adjuk át a legutóbbi mezőben `latest` , hogy a későbbiekben mindig lekérjük a legújabb buildre. (Az általunk használt karakterlánc `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

A következő lépéssel tisztában van-e mindenkinek, aki már létrehozott egy ssh rsa nyilvános és titkos kulcs párosítsa a Linux vagy Mac használatával **az ssh-keygen - t az rsa -b 2048**. Ha nem kell minden olyan tanúsítvány kulcspárok a `~/.ssh` könyvtár, létrehozhatja őket:

* Használatával automatikusan, a `azure vm create --generate-ssh-keys` lehetőséget.
* Manuálisan [létrehozhat utasításokat](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Másik lehetőségként használhatja a `--admin-password` módszert az SSH-kapcsolatokat a virtuális gép létrehozása után. Ez a módszer akkor általában kevésbé biztonságos.

A virtuális Gépet hozunk létre, és az erőforrások és információk együtt a `azure vm create` parancsot:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Kimenet:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Csatlakozhat a virtuális gép közvetlenül az alapértelmezett SSH-kulcsok használatával. Győződjön meg arról, hogy a megfelelő portot adja meg azt a terheléselosztón keresztül használt átadása óta. (Az első virtuális gép, beállítjuk a NAT-szabályt úgy, hogy a virtuális gép továbbítsa a port 4222.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Kimenet:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Lépjen tovább, és a második virtuális gép létrehozása a azonos módon:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

És most már használhatja a `azure vm show myResourceGroup myVM1` paranccsal vizsgálja meg, amit létrehozott. Ezen a ponton a Ubuntu virtuális gépet egy terheléselosztó mögé futtat az Azure-ban, hogy be tud jelentkezni az csak az SSH-kulcspár a (mivel a jelszavak le vannak tiltva). Telepítse az nginx-et vagy httpd, egy webalkalmazás üzembe helyezése és a hálózati forgalom mindkét virtuális gépet a terheléselosztón keresztül folyamat.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Kimenet:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>A környezet exportálás sablonként
Most, hogy ki ebben a környezetben létrehozott, mi történik, ha biztosan hozzon létre egy további fejlesztési környezetben ugyanazokat a paramétereket, vagy a megfelelő, éles környezetben? Resource Manager használ a környezet összes paramétereit meghatározó JSON-sablonok. A JSON-sablon hivatkozik felépítése teljes környezetek. Is [JSON-sablonok készítése manuálisan](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy egy meglévő környezetet hozza létre a JSON-sablon exportálása:

```azurecli
azure group export --name myResourceGroup
```

Ez a parancs létrehozza a `myResourceGroup.json` fájlt az aktuális munkakönyvtárba. Ha ebből a sablonból létrehozott egy környezetet, a rendszer kéri az összes erőforrás nevét, beleértve a terheléselosztót, a hálózati illesztőkre vagy virtuális gépek nevét. Fel lehet tölteni ezeket a neveket a sablon fájlban adja hozzá a `-p` vagy `--includeParameterDefaultValue` paramétert a `azure group export` parancsot, amely korábban látható volt. Az erőforrás nevének megadása a JSON-sablon szerkesztése vagy [hozzon létre egy parameters.json fájlban](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely meghatározza, hogy az erőforrások nevei.

Olyan környezetet hozhat létre a sablonból:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Előfordulhat, hogy szeretné olvasni [sablonokból történő központi telepítéséről további](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ismerje meg hogyan növekményes környezetek frissítse, használhatja a paramétereket tartalmazó fájlt, és sablonok egyetlen helyről.

## <a name="next-steps"></a>További lépések
Most már készen áll a több hálózati összetevők és a virtuális gépek használatának megkezdése előtt. Ez a minta-környezet használatával hozhatja létre az alkalmazás a bevezetett alapvető összetevők ide használatával.
