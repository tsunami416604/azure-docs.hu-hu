---
title: Hozzon létre egy teljes körű Linux környezetet az Azure CLI 1.0 |} Microsoft Docs
description: Tárolási, Linux virtuális gép, egy virtuális hálózati és alhálózati, egy adott terheléselosztóhoz, egy olyan hálózati adapter, egy nyilvános IP-cím és a hálózati biztonsági csoport, az Azure CLI 1.0 használatával alapoktól összes létrehozása.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 4a43e138d3497e01fe9e0e5c55a4a66adac767c6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-complete-linux-environment-with-the-azure-cli-10"></a>Hozzon létre egy teljes körű Linux környezetet az Azure CLI 1.0
Ebben a cikkben azt a terheléselosztó és a virtuális gépek, amelyek hasznosak a fejlesztési és egyszerű számítástechnikai két egyszerű hálózat felépítéséhez. Azt ismerteti a folyamatot, a parancs által parancs, amíg a két üzemel, biztonságos Linux virtuális gép, amelyre lehet csatlakoztatni bárhol az interneten. Majd továbbléphet összetettebb hálózatokhoz és a környezetben.

Menet elsajátíthatja függőségi hierarchiája, hogy a Resource Manager üzembe helyezési modellel lehetővé teszi, és mekkora kapcsolatos kapcsolja be biztosít. Miután meggyőződött arról, hogyan épül fel a rendszer, újjáépíthető azt sokkal gyorsabban használatával [Azure Resource Manager-sablonok](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Emellett után megtudhatja, hogyan működnek a környezetet részei együtt, automatizálhatják is azokat-sablonok létrehozása kezdve egyszerűbbé válik.

A környezet tartalmaz:

* Két virtuális gépek rendelkezésre állási csoportok belül.
* Terheléselosztó terheléselosztási szabállyal 80-as porton.
* Hálózati biztonsági csoport (NSG) szabályok a nemkívánatos forgalom a virtuális gép védelmét.

Az egyéni környezet létrehozása a legújabb kell [Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) erőforrás-kezelő módban (`azure config mode arm`). Egy eszköz elemzése JSON is kell. Ez a példa [jq](https://stedolan.github.io/jq/).


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#quick-commands) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="quick-commands"></a>Gyors parancsok
Ha szeretné gyorsan a feladatnak a, a következő szakasz részleteit a következő parancsokat egy virtuális Gépet az Azure-bA feltölteni. Részletes információkat és a környezetben az egyes lépéseit itt található: a dokumentum többi része a, kezdési [Itt](#detailed-walkthrough).

Győződjön meg arról, hogy rendelkezik [az Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) jelentkezett be, és a Resource Manager módot használ:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.

Hozza létre az erőforráscsoportot. Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `westeurope` helye:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Az erőforráscsoport ellenőrizze a JSON-elemző használatával:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

A storage-fiók létrehozása. Az alábbi példa létrehoz egy nevű tárfiók `mystorageaccount`. (A tárfiók nevét kell egyedinek lennie, ezért adja meg a saját egyedi nevét.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Ellenőrizze a tárfiók a JSON-elemző használatával:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Hozza létre a virtuális hálózatot. Az alábbi példa létrehoz egy virtuális hálózatot nevű `myVnet`:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Hozzon létre egy alhálózatot. Az alábbi példakód létrehozza nevű alhálózat `mySubnet`:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Ellenőrizze a virtuális hálózati és alhálózati a JSON-elemző használatával:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Hozzon létre egy nyilvános IP-cím. Az alábbi példa létrehoz egy nyilvános IP-cím nevű `myPublicIP` a DNS-beli nevű `mypublicdns`. (A DNS-nevét kell egyedinek lennie, így rendelkeznek a saját egyedi nevet.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

A terheléselosztó létrehozása. Az alábbi példa létrehoz egy terhelés-kiegyenlítő nevű `myLoadBalancer`:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Hozzon létre egy előtér-IP-címkészletet a terheléselosztóhoz, és társítsa a nyilvános IP-cím. Az alábbi példa létrehoz egy előtér-IP-címkészletet nevű `mySubnetPool`:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Hozzon létre a háttér IP-címkészlet a terheléselosztóhoz. Az alábbi példakód létrehozza a háttér IP-címkészlet nevű `myBackEndPool`:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Hozzon létre SSH bejövő hálózati cím címfordítási (NAT) szabályait a terheléselosztót. Az alábbi példa létrehoz két terheléselosztási szabály, `myLoadBalancerRuleSSH1` és `myLoadBalancerRuleSSH2`:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

A webalkalmazás létrehozása bejövő NAT-szabályok a terheléselosztóhoz. Az alábbi példa létrehoz egy terheléselosztási szabály nevű `myLoadBalancerRuleWeb`:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Az állapotfigyelő terheléselosztói mintavétel létrehozása. Az alábbi példa létrehoz egy TCP-Hálózatfigyelővel nevű `myHealthProbe`:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Ellenőrizze a terheléselosztó IP-készletek és NAT-szabályok a JSON-elemző használatával:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Hozzon létre az első hálózati kártya (NIC). Cserélje le a `#####-###-###` szakaszok a saját Azure-előfizetés-azonosítóval. Az előfizetés-azonosító jelenik meg a kimeneti **jq** az erőforrások létrehozásakor vizsgálata során. Megtekintheti az előfizetés-Azonosítóval rendelkező `azure account list`.

Az alábbi példa létrehoz egy hálózati adapter nevű `myNic1`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Hozzon létre a második hálózati adaptert. Az alábbi példa létrehoz egy hálózati adapter nevű `myNic2`:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Ellenőrizze a két hálózati adaptert a JSON-elemző használatával:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

A hálózati biztonsági csoport létrehozásához. Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű `myNetworkSecurityGroup`:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Két bejövő szabály a hálózati biztonsági csoport hozzáadása. Az alábbi példa létrehoz két szabály `myNetworkSecurityGroupRuleSSH` és `myNetworkSecurityGroupRuleHTTP`:

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

A két hálózati adapter hálózati biztonsági csoport kötést létrehozni:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

A rendelkezésre állási csoport létrehozása. Az alábbi példakód létrehozza a rendelkezésre állási készlet elnevezett `myAvailabilitySet`:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Az első Linux virtuális gép létrehozása. Az alábbi példakód létrehozza a virtuális gépek nevű `myVM1`:

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

A második Linux virtuális gép létrehozása. Az alábbi példakód létrehozza a virtuális gépek nevű `myVM2`:

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

Használja a JSON-elemző ellenőrizze, hogy minden, ami készült:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Az új környezet gyorsan újból létrehozni új példányok sablon exportálása:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Részletes bemutató
A részletes következő lépések azt ismertetik, minden parancs tevékenységétől ki a környezet létrehozása. Ezek a következők hasznos a saját egyéni fejlesztési és éles környezeteket összeállításakor.

Győződjön meg arról, hogy rendelkezik [az Azure CLI 1.0](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) jelentkezett be, és a Resource Manager módot használ:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Erőforráscsoport-sablonok létrehozása és központi telepítési helyek kiválasztása
Azure erőforráscsoport-sablonok, amelyek tartalmazzák a beállítási adatokat és a metaadatok a logikai központi erőforrás-kezelés engedélyezése a logikai telepítési entitások. Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `westeurope` helye:

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

## <a name="create-a-storage-account"></a>Create a storage account
A virtuális gép lemezeit, majd bármelyik adatlemeznek, amelyeket meg szeretne adni a storage-fiókok szükség van. Szinte azonnal után létrehozhat erőforráscsoportok storage-fiókokat hozhat létre.

Jelen példában használjuk a `azure storage account create` parancsot, és a kívánt tárolási támogatás típusa átadja a fiók, az erőforráscsoport helye, amely szabályozza. Az alábbi példa létrehoz egy nevű tárfiók `mystorageaccount`:

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

Az erőforráscsoport segítségével megvizsgálhatja a `azure group show` paranccsal, most használja az [jq](https://stedolan.github.io/jq/) eszközzel együtt a `--json` Azure CLI lehetőséget. (Használható **jsawk** vagy a JSON elemezni szeretné nyelvi függvénytárat.)

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

A parancssori felület használatával vizsgálja meg a tárfiók, először a fióknevek és a kulcsok beállításához. Cserélje le egy nevet az Ön által a következő példa a tárfiók nevét:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Ezután egyszerűen megtekintheti a tárolással kapcsolatos:

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
Ezután fog létre kell hoznia az Azure-ban és a virtuális gépeket hozhat létre alhálózat futtató virtuális hálózaton. Az alábbi példa létrehoz egy virtuális hálózatot nevű `myVnet` rendelkező a `192.168.0.0/16` címelőtag:

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

Ebben az esetben használja most--json teszi a `azure group show` és `jq` hogyan dolgozunk az erőforrások megjelenítéséhez. Most már rendelkezik egy `storageAccounts` erőforrás és a `virtualNetworks` erőforrás.  

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

Most hozzuk létre az alhálózatot a `myVnet` virtuális hálózat, amelybe a virtuális gépek vannak telepítve. Használjuk a `azure network vnet subnet create` parancsot, és az erőforrások már létrehoztunk: a `myResourceGroup` erőforráscsoport és a `myVnet` virtuális hálózat. A következő példában azt nevű alhálózat hozzáadása `mySubnet` az alhálózati cím előtaggal `192.168.1.0/24`:

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

Mivel az alhálózat logikailag a virtuális hálózaton belül, azt keresse meg az alhálózati adatokat egy némileg eltérő parancs. A parancs az használjuk `azure network vnet show`, de továbbra is, hogy ellenőrizze a JSON-kimenetét a `jq`.

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
Most hozzuk létre az a nyilvános IP-cím (PIP), azt hozzárendeli a terheléselosztó hasonló adataival. Ez lehetővé teszi, hogy a virtuális gépek az internetről használatával kapcsolódik a `azure network public-ip create` parancsot. Mivel az alapértelmezett cím dinamikus, létrehozhatunk egy elnevezett DNS-bejegyzést a **cloudapp.azure.com** tartomány használatával a `--domain-name-label` lehetőséget. Az alábbi példa létrehoz egy nyilvános IP-cím nevű `myPublicIP` a DNS-beli nevű `mypublicdns`. A DNS-nevének egyedinek kell lennie, mert a saját egyedi DNS-nevet adja meg:

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

A nyilvános IP-cím egyben legfelső szintű erőforrás, így tekintheti meg a `azure group show`.

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

Használatával megvizsgálhatja a több erőforrás adatait, többek között a altartomány teljesen minősített tartománynevét (FQDN) használatával a teljes `azure network public-ip show` parancsot. A nyilvános IP-cím erőforrás logikailag van rendelve, de egy adott cím még nincs hozzárendelve. Az IP-címének megszerzéséhez fog kell egy adott terheléselosztóhoz, amely jelenleg még nem hozott létre.

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

## <a name="create-a-load-balancer-and-ip-pools"></a>Hozzon létre egy terheléselosztó és IP-készletek
Amikor létrehoz egy terhelés-kiegyenlítő, lehetővé teszi forgalom szét több virtuális gép. Az alkalmazás redundanciájának több virtuális gép karbantartás vagy túl nagy terhelés esetén a felhasználói kérésekre válaszolnak futtatásával is tartalmazza. Az alábbi példa létrehoz egy terhelés-kiegyenlítő nevű `myLoadBalancer`:

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

A terheléselosztó viszonylag üres, ezért az egyes IP-címkészletek létrehozása. Azt szeretnénk, a terheléselosztó, egyet az előtér és a hátteret két IP-címkészletek létrehozása. Az előtér-IP-címkészlet nyilvánosan láthatóvá. A helyet, amelyhez azt rendelje hozzá a korábban létrehozott PIP egyben. Majd használjuk a háttér-készlet olyan hely, a virtuális gépekhez való csatlakozáshoz. Ily módon a áramolhasson az adatforgalom a terheléselosztó a virtuális gépek keresztül.

Először hozzuk létre az előtér-IP-címkészletet. Az alábbi példa létrehoz egy előtér-készlet nevű `myFrontEndPool`:

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

Vegye figyelembe, hogyan használtuk az `--public-ip-name` felelt meg a kapcsoló a `myPublicIP` , amely a korábban létrehozott. A terheléselosztó a nyilvános IP-cím hozzárendelése lehetővé teszi a virtuális gépek eléréséhez az interneten keresztül.

Következő lépésként hozzon létre a második IP-címkészlet, ezúttal a háttér-forgalomhoz. Az alábbi példa létrehoz egy háttér címkészletet nevű `myBackEndPool`:

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

A képen látható, hogyan ennek során a terheléselosztó azáltal, hogy megtekinti a `azure network lb show` és a JSON-kimenetét vizsgálata:

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
Ahhoz, hogy a terheléselosztó átfutó forgalom, kell hálózati címet adja meg a bejövő vagy kimenő műveletek címfordítási (NAT) szabályok létrehozásához. Adja meg a használni kívánt protokollt, majd leképezik a külső tetszés szerint belső portjaihoz. A környezet hozzuk létre egyes szabályokat, amelyek lehetővé teszik az SSH a terheléselosztó a virtuális gépek keresztül. Beállítjuk 4222 és 4223 TCP-port a 22-es TCP-portot a virtuális gépeken (amely később létrehozhatunk) irányítani. Az alábbi példa létrehoz egy nevű szabályt `myLoadBalancerRuleSSH1` TCP-port 4222 hozzárendelése 22-es portot:

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

Ismételje meg az eljárást a második NAT-szabály az SSH. Az alábbi példa létrehoz egy nevű szabályt `myLoadBalancerRuleSSH2` TCP-port 4223 hozzárendelése 22-es portot:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Most is használatától és a webes forgalomban, a szabály csatlakoztatás az IP-készletek legfeljebb 80-as TCP-port NAT-szabályt. Ha azt a számítógéphez a szabály, amely egy IP-készletet, helyett a szabályt, hogy a virtuális gépek egyenkénti csatlakoztatása a Microsoft adja hozzá, vagy távolítsa el a virtuális gépek IP-készletből. A terheléselosztó automatikusan módosítja a forgalmat. Az alábbi példa létrehoz egy nevű szabályt `myLoadBalancerRuleWeb` 80-as TCP-port hozzárendelését a 80-as port:

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
A health mintavételi rendszeresen ellenőrzi a ellenőrizze, hogy most működik és válaszol a kérelmekre, meghatározott a terheléselosztó mögötti virtuális gépeken. Ha nem, most eltávolítja a biztonsági művelet, győződjön meg arról, hogy felhasználók nem irányítja őket. Megadhatja a állapotmintáihoz, valamint időközök és az időkorlát-értékei egyéni keres. Állapotteljesítmény kapcsolatos további információkért lásd: [terheléselosztó vizsgálat](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az alábbi példa létrehoz egy TCP állapotfigyelő megadott elnevezett `myHealthProbe`:

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

Itt azt az állapot-ellenőrzési eredményeire a 15 másodperces időközönként megadott. Azt is, amelyeken legfeljebb négy mintavételt (egy perces) a terheléselosztó úgy véli, hogy a gazdagép már nem működik.

## <a name="verify-the-load-balancer"></a>Ellenőrizze a terheléselosztó
Most már a terheléselosztó-konfiguráció történik. A lépéseket, a következők:

1. Létrehozott egy terheléselosztót.
2. Létrehozott egy előtér-IP-címkészletet, és egy nyilvános IP-címet hozzárendelni.
3. Létrehozott egy háttér-IP-címkészlet, amely a virtuális gépek csatlakozni tud-e.
4. NAT-szabályok, amelyek lehetővé teszik az SSH-kapcsolatot a virtuális gépek kezelésére, egy szabályt, amely lehetővé teszi, hogy a 80-as TCP-port a webalkalmazás együtt létrehozott.
5. Hozzáadta a rendszeres időnként ellenőrzik a virtuális gép állapotmintáihoz. A állapotmintáihoz biztosítja, hogy felhasználók ne próbáljon meg hozzáférni egy virtuális Gépet, amely már nem működik, vagy tartalom.

A terheléselosztó néz most tekintsük át:

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

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Egy olyan hálózati adapter használata a Linux virtuális gép létrehozása
Hálózati adapter, ezért programozott módon való használatukat alkalmazhat. Egynél több is lehet. Az alábbi `azure network nic create` parancs, a hálózati adapter a terhelés háttér IP-készlethez a számítógéphez, és társítsa azt az SSH-forgalom engedélyezése a NAT-szabályt.

Cserélje le a `#####-###-###` szakaszok a saját Azure-előfizetés-azonosítóval. Az előfizetés-azonosító jelenik meg a kimeneti `jq` az erőforrások létrehozásakor vizsgálata során. Megtekintheti az előfizetés-Azonosítóval rendelkező `azure account list`.

Az alábbi példa létrehoz egy hálózati adapter nevű `myNic1`:

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

A részletek a erőforrás közvetlenül megvizsgálásával. Az erőforrás használatával vizsgálja meg a `azure network nic show` parancs:

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

Most már tudjuk létrehozni a második hálózati adapter, csatlakoztatás a ismét a a háttér IP-címkészletet. Ez a második alkalommal NAT-szabály lehetővé teszi az SSH-forgalom. Az alábbi példa létrehoz egy hálózati adapter nevű `myNic2`:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Hálózati biztonsági csoport és a szabályok létrehozása
Most azt hozzon létre egy hálózati biztonsági csoport és a hálózati hozzáférés szabályozásához, a bejövő szabályok Hálózati biztonsági csoport egy hálózati adapter vagy az alhálózat alkalmazhatók. Szabályok, hogy a forgalmat a virtuális gépek mindkét definiálni. Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

A bejövő szabályt az NSG-t (SSH támogatásához) 22-es portot a bejövő kapcsolatok engedélyezésére az adjuk hozzá. Az alábbi példa létrehoz egy nevű szabályt `myNetworkSecurityGroupRuleSSH` TCP-port a 22-es engedélyezéséhez:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Most adjuk hozzá (a webes forgalom támogatásához) 80-as portot a bejövő kapcsolatok engedélyezésére az NSG bejövő szabályt. Az alábbi példa létrehoz egy nevű szabályt `myNetworkSecurityGroupRuleHTTP` a TCP engedélyezéséhez a 80-as port:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> A bejövő forgalomra vonatkozó szabály egy szűrő a bejövő hálózati kapcsolatok. Ebben a példában a virtuális gépek virtuális hálózati adapter, ami azt jelenti, hogy minden kérést 22-es portot áthalad a hálózati adapterre a virtuális gépen az NSG kötni azt. A bejövő szabály a hálózati kapcsolat, és nem kapcsolatos, a végpont melyik tárolókezelésének kapcsolatos a klasszikus üzembe helyezés. Port megnyitásához, meg kell hagyni a `--source-port-range` beállítása "\*" (az alapértelmezett érték) a bejövő kéréseket fogad **bármely** a kért porton. Ezek általában dinamikus.
>
>

## <a name="bind-to-the-nic"></a>Kötést létrehozni a hálózati adapter
Az NSG-t a hálózati adapterek kötni. Igazolnia kell a hálózati adaptert megoszthatja a hálózati biztonsági csoport. Mindkét parancsok, mind a hálózati adapterek bekötése futtatásával:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Rendelkezésre állási a virtuális gépek készletek súgó terjedésének tartalék tartományok és a frissítési tartományok között. Hozzon létre egy rendelkezésre állási készletét, a virtuális gépek. Az alábbi példakód létrehozza a rendelkezésre állási készlet elnevezett `myAvailabilitySet`:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Tartalék tartományok definiálása, amelyek egy közös forrás- és hálózati kikapcsolás virtuális gépek csoportja. Alapértelmezés szerint a rendelkezésre állási csoport belül konfigurált virtuális gépek egymástól legfeljebb három tartalék tartományokban. A lényege, hogy a hardver probléma a tartalék tartományok valamelyikében nem befolyásolja a minden virtuális gép, amelyen fut. az alkalmazás. Azure virtuális gépek elhelyezésekor azokat a rendelkezésre állási csoport automatikusan elosztja a tartalék tartományok.

Frissítési tartományok adja meg a virtuális gépek és a mögöttes fizikai hardver, amely egy időben újra kell indítani. A sorrendet, amelyben a frissítési tartományok újraindítása után előfordulhat, hogy nem szekvenciális tervezett karbantartás alatt, de egyszerre csak egy frissítés újraindítása után. Ebben az esetben Azure automatikusan elosztása a virtuális gépek frissítési tartományok amikor egy rendelkezésre állási helyen helyezi el őket.

Tudjon meg többet az [virtuális gépek rendelkezésre állásának kezelése](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>A Linux virtuális gépek létrehozása
A tárolási és hálózati erőforrásokat, internetről elérhető virtuális gépek támogatásához létrehozott. Most tegyük létrehozása a virtuális gépek és biztonságos őket egy SSH-kulcsban, amely nem tartozik jelszó. Ebben az esetben egy Ubuntu virtuális gép a legutóbbi LTS alapján hozzon létre programot fogjuk. Azt az kép információk keresse meg a `azure vm image list`leírtak szerint [Azure Virtuálisgép-rendszerképekről keresése](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A parancs segítségével lemezkép jelenleg kijelölt `azure vm image list westeurope canonical | grep LTS`. Ebben az esetben használjuk `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Az utolsó mező azt át `latest` így a jövőben azt mindig a legfrissebb build. (A karakterlánc használjuk `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Ez a következő lépés nem ismerős bárki, aki már létrehozta a egy ssh rsa nyilvános és titkos kulcs párosítsa a Linux- vagy Mac használatával **ssh-keygen - t rsa -b 2048**. Ha nincs a tanúsítvány kulcspárok a `~/.ssh` könyvtárában, akkor létrehozhat őket:

* Automatikus, segítségével a `azure vm create --generate-ssh-keys` lehetőséget.
* Manuálisan [útmutatást követve létrehozhatja a saját kezűleg](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Másik lehetőségként használhatja a `--admin-password` módszert az SSH-kapcsolatok a virtuális gép létrehozása után. Ez a módszer általában kevésbé biztonságos.

Azt a virtuális gép létrehozása az erőforrások és információk együtt hozásával a `azure vm create` parancs:

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

Csatlakozhat a virtuális gép azonnal az alapértelmezett SSH-kulcsok használatával. Győződjön meg arról, hogy a megfelelő portot adja meg, mivel azt még áthaladnának a terheléselosztót. (Az első virtuális gép, beállítjuk a NAT-szabály port 4222 továbbítsa a virtuális géphez.)

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

Lépjen tovább, és a második virtuális gép létrehozása az azonos módon:

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

És ezután már használhatja a `azure vm show myResourceGroup myVM1` parancs futtatásával ellenőrizze, hogy mi létrehozta. Ezen a ponton a Ubuntu virtuális gép egy terheléselosztó mögött futtatja, amely jelentkezhet be az SSH-kulcspárral csak a (mert a jelszavak le vannak tiltva) Azure-ban. Telepítse a nginx vagy httpd, webalkalmazás üzembe helyezése, és a hálózati forgalom mindkét a virtuális gépek számára a terheléselosztó keresztül.

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


## <a name="export-the-environment-as-a-template"></a>A környezet sablon exportálása
Most, hogy már létrehozta a buildet ki ebben a környezetben, mi történik, ha meg szeretné további fejlesztési környezet létrehozása, és ugyanazokkal a paraméterekkel, amely megfelel az éles környezetben? Erőforrás-kezelő a környezet összes paramétereit meghatározó JSON-sablonokat használ. A JSON-sablon Vezérlőpultjának kimenő teljes környezetek létrehozása. Is [JSON sablonok létrehozása manuálisan](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy egy meglévő környezet hozza létre a JSON-sablon exportálása:

```azurecli
azure group export --name myResourceGroup
```

Ezzel a paranccsal létrejön az `myResourceGroup.json` az aktuális munkakönyvtárban fájlban. A sablon alapján hoz létre egy olyan környezetben, amikor az összes erőforrás neveit, beleértve a terheléselosztóhoz, hálózati adapterek vagy virtuális gépek nevek kéri. Töltheti fel ezeket a neveket a sablon fájlban adja hozzá a `-p` vagy `--includeParameterDefaultValue` paramétert a `azure group export` korábban bemutatott parancsot. Az erőforrás nevének megadása a JSON-sablon szerkesztése vagy [parameters.json fájl létrehozása](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely meghatározza, hogy az erőforrás nevét.

Olyan környezetet hozhat létre a sablonból:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Előfordulhat, hogy szeretné olvasni [felügyeleticsomag-sablonok telepítésével kapcsolatos további](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). További tudnivalók Növekményesen környezetek frissítése, használja a paraméterek fájlját, és egyetlen tárolási helyen sablonok elérésére.

## <a name="next-steps"></a>További lépések
Most már készen áll a több hálózati összetevőkkel és virtuális gépek használatának megkezdése előtt. Ez a minta-környezet segítségével bevezetett alapösszetevőket itt használatával, az alkalmazás létrehozása.
