---
title: Teljes Linux-környezet létrehozása a klasszikus Azure CLI-vel | Microsoft Docs
description: Hozzon létre egy tárolót, egy Linux virtuális gépet, egy virtuális hálózatot és alhálózatot, egy Load balancert, egy hálózati adaptert, egy nyilvános IP-címet és egy hálózati biztonsági csoportot, amely az Azure klasszikus parancssori felületének használatával egészen az alapoktól.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: aaf91aa81be5fc4c5944dde804798a61ceffc5a6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083719"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Teljes Linux-környezet létrehozása a klasszikus Azure CLI-vel
Ebben a cikkben egy olyan egyszerű hálózatot hozunk létre, amely egy terheléselosztó és egy virtuális gép, amely a fejlesztéshez és az egyszerű számítástechnikahoz hasznos. Bemutatjuk a folyamat parancsát a parancs szerint, egészen addig, amíg két működő, biztonságos Linux virtuális gépet használ, amelyekhez bárhonnan csatlakozhat az interneten. Ezután az összetettebb hálózatokra és környezetekre léphet tovább.

Útközben megismerheti a Resource Manager-alapú üzemi modell által biztosított függőségi hierarchiát, és arról, hogy mekkora teljesítményt nyújt. Miután megtalálta, hogy a rendszer hogyan épül fel, sokkal gyorsabban újraépítheti [Azure Resource Manager sablonok](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával. Azt is megtudhatja, hogy a környezet egyes részei hogyan illeszkednek egymáshoz, így egyszerűbb lesz a sablonok automatizálása.

A környezet a következőket tartalmazza:

* Két virtuális gép egy rendelkezésre állási csoporton belül.
* Egy terheléselosztási szabályt tartalmazó terheléselosztó a 80-es porton.
* Hálózati biztonsági csoport (NSG) szabályai a virtuális gép nemkívánatos forgalom elleni védelméhez.

Az egyéni környezet létrehozásához a legújabb [Azure parancssori](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) felületre van szükség Resource Manager módban (`azure config mode arm`). Szükség van egy JSON-elemzési eszközre is. Ez a példa az [jQ](https://stedolan.github.io/jq/)-t használja.


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Klasszikus Azure CLI](#quick-commands) – parancssori felület a klasszikus és a Resource Management-alapú üzemi modellekhez (ez a cikk)
- [Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management-alapú üzemi modellhez tartozó parancssori felület következő generációja


## <a name="quick-commands"></a>Gyors parancsok
Ha gyorsan el kell végeznie a feladatot, a következő szakasz részletesen ismerteti a virtuális gép Azure-ba való feltöltésének alapparancsait. Az egyes lépések részletes információi és kontextusa a dokumentum többi részében található, [itt](#detailed-walkthrough)kezdődik.

Győződjön meg arról, hogy [a klasszikus Azure CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) be van jelentkezve, és Resource Manager üzemmódot használ:

```azurecli
azure config mode arm
```

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Például a paraméterek nevei `myResourceGroup`a `mystorageaccount`következők: `myVM`, és.

Hozza létre az erőforráscsoportot. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Ellenőrizze az erőforráscsoportot a JSON-elemző használatával:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Hozza létre a Storage-fiókot. A következő példa egy nevű `mystorageaccount`Storage-fiókot hoz létre. (A Storage-fiók nevének egyedinek kell lennie, ezért adja meg a saját egyedi nevét.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Ellenőrizze a Storage-fiókot a JSON-elemző használatával:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Hozza létre a virtuális hálózatot. A következő példa létrehoz egy nevű `myVnet`virtuális hálózatot:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Hozzon létre egy alhálózatot. A következő példában létrehozunk egy nevű `mySubnet`alhálózatot:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Ellenőrizze a virtuális hálózatot és az alhálózatot a JSON-elemző használatával:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Hozzon létre egy nyilvános IP-címet. A következő példa egy nevű `myPublicIP` nyilvános IP-címet hoz létre a DNS- `mypublicdns`nevével. (A DNS-névnek egyedinek kell lennie, ezért adja meg a saját egyedi nevét.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Hozza létre a Load balancert. A következő példa létrehoz egy nevű terheléselosztó- `myLoadBalancer`nevet:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Hozzon létre egy előtér-IP-készletet a terheléselosztó számára, és rendelje hozzá a nyilvános IP-címet. A következő példában létrehozunk egy nevű `mySubnetPool`előtér-IP-címkészletet:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Hozza létre a Load Balancer háttérbeli IP-készletét. Az alábbi példa egy nevű `myBackEndPool`háttérbeli IP-készletet hoz létre:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Hozzon létre SSH bejövő hálózati címfordítási (NAT) szabályokat a terheléselosztó számára. A következő példa két terheléselosztó-szabályt hoz létre `myLoadBalancerRuleSSH1` , `myLoadBalancerRuleSSH2`és:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Hozza létre a terheléselosztó webes bejövő NAT-szabályait. A következő példa egy nevű `myLoadBalancerRuleWeb`terheléselosztó szabályt hoz létre:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Hozza létre a terheléselosztó állapotának mintavételét. A következő példa egy nevű `myHealthProbe`TCP-mintavételi műveletet hoz létre:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Ellenőrizze a terheléselosztó, az IP-készletek és a NAT-szabályokat a JSON-elemző használatával:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Hozza létre az első hálózati adaptert (NIC). Cserélje le `#####-###-###` a részeket saját Azure-előfizetés-azonosítójával. Az előfizetés AZONOSÍTÓját a **jQ** kimenetében, a létrehozandó erőforrások vizsgálata során kell megállapítani. Megtekintheti az előfizetési AZONOSÍTÓját `azure account list`is a használatával.

A következő példa egy nevű `myNic1`hálózati adaptert hoz létre:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Hozza létre a második hálózati adaptert. A következő példa egy nevű `myNic2`hálózati adaptert hoz létre:

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

Hozza létre a hálózati biztonsági csoportot. A következő példa egy nevű `myNetworkSecurityGroup`hálózati biztonsági csoportot hoz létre:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Adja meg a hálózati biztonsági csoport két bejövő szabályát. Az alábbi példa két szabályt `myNetworkSecurityGroupRuleSSH` `myNetworkSecurityGroupRuleHTTP`hoz létre:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Ellenőrizze a hálózati biztonsági csoportot és a bejövő szabályokat a JSON-elemző használatával:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

A hálózati biztonsági csoport kötése a két hálózati adapterrel:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Hozza létre a rendelkezésre állási készletet. Az alábbi példa egy nevű `myAvailabilitySet`rendelkezésre állási készletet hoz létre:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Hozza létre az első linuxos virtuális gépet. Az alábbi példa egy nevű `myVM1`virtuális gépet hoz létre:

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

Hozza létre a második linuxos virtuális gépet. Az alábbi példa egy nevű `myVM2`virtuális gépet hoz létre:

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

A JSON-elemző használatával ellenőrizze, hogy a létrehozott összes elem:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportálja új környezetét egy sablonba, hogy gyorsan újra létrehozza az új példányokat:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Részletes bemutató
Az alábbi lépéseket követve megtudhatja, mit csinál az egyes parancsok a környezet kiépítésekor. Ezek a fogalmak hasznosak lehetnek, ha saját egyéni környezeteket készít a fejlesztéshez vagy a gyártáshoz.

Győződjön meg arról, hogy [a klasszikus Azure CLI](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) be van jelentkezve, és Resource Manager üzemmódot használ:

```azurecli
azure config mode arm
```

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Például a paraméterek nevei `myResourceGroup`a `mystorageaccount`következők: `myVM`, és.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Erőforráscsoportok létrehozása és a telepítési helyszínek kiválasztása
Az Azure-erőforráscsoportok olyan logikai telepítési entitások, amelyek konfigurációs adatokat és metaadatokat tartalmaznak az erőforrás-telepítések logikai kezelésének engedélyezéséhez. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen:

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
Szüksége lesz a virtuálisgép-lemezekhez és a hozzáadni kívánt további adatlemezekhez szükséges tárolási fiókokra. A Storage-fiókokat szinte azonnal létrehozhatja az erőforráscsoportok létrehozása után.

Itt a `azure storage account create` parancsot használjuk, amely átadja a fiók helyét, az azt vezérlő erőforráscsoportot, valamint a kívánt tárterület-támogatást. A következő példában létrehozunk egy nevű `mystorageaccount`Storage-fiókot:

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

Ha az `azure group show` paranccsal szeretné megvizsgálni az erőforráscsoportot, használja a [jQ](https://stedolan.github.io/jq/) `--json` eszközt az Azure CLI lehetőséggel együtt. (Használhatja a **jsawk** -t vagy bármilyen nyelvi könyvtárat, amelyet a JSON elemzéséhez szeretne használni.)

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

Ha meg szeretné vizsgálni a Storage-fiókot a parancssori felület használatával, először be kell állítania a fiók nevét és kulcsát. Cserélje le a Storage-fiók nevét a következő példában a kiválasztott névre:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Ezután egyszerűen megtekintheti a tárolási adatokat:

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
Ezután létre kell hoznia egy Azure-beli virtuális hálózatot és egy olyan alhálózatot, amelyben létre lehet hozni a virtuális gépeket. Az alábbi példa egy nevű `myVnet` virtuális hálózatot hoz létre a `192.168.0.0/16` címe előtaggal:

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

Ismét használjuk a--JSON lehetőséget `azure group show` `jq` , hogy meglássuk, hogyan épülnek fel az erőforrásaink. Most már rendelkezik egy `storageAccounts` erőforrással és `virtualNetworks` egy erőforrással.  

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

Most hozzon létre egy alhálózatot `myVnet` abban a virtuális hálózaton, amelyben a virtuális gépek üzembe vannak helyezve. A `azure network vnet subnet create` parancsot a már létrehozott erőforrásokkal együtt használjuk: az `myResourceGroup` erőforráscsoportot és a `myVnet` virtuális hálózatot. Az alábbi példában a nevű `mySubnet` alhálózatot a következő alhálózati `192.168.1.0/24`előtaggal vesszük fel:

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

Mivel az alhálózat logikailag a virtuális hálózaton belül van, az alhálózati adatokat egy kicsit más paranccsal kell megkeresni. Az általunk használt `azure network vnet show`parancs, de a használatával `jq`folytatjuk a JSON-kimenet vizsgálatát.

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
Most hozzuk létre a terheléselosztó számára hozzárendelt nyilvános IP-címet (PIP). Lehetővé teszi a virtuális gépekhez való kapcsolódást az internetről a `azure network public-ip create` parancs használatával. Mivel az alapértelmezett címek dinamikusak, a `--domain-name-label` beállítás használatával hozunk létre egy nevesített DNS-bejegyzést a **cloudapp.Azure.com** tartományban. A következő példa egy nevű `myPublicIP` nyilvános IP-címet hoz létre a DNS- `mypublicdns`nevével. Mivel a DNS-névnek egyedinek kell lennie, meg kell adnia a saját egyedi DNS-nevét:

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

A nyilvános IP-cím egyben legfelső szintű erőforrás is, így megtekintheti a következőt: `azure group show`.

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

A Complete `azure network public-ip show` paranccsal további erőforrás-részleteket is megvizsgálhat, beleértve az altartomány teljes tartománynevét (FQDN) is. A nyilvános IP-cím erőforrása logikailag lett kiosztva, de egy adott cím még nincs hozzárendelve. IP-cím beszerzéséhez szüksége lesz egy terheléselosztó, amelyet még nem hozott létre.

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

## <a name="create-a-load-balancer-and-ip-pools"></a>Terheléselosztó és IP-készletek létrehozása
A terheléselosztó létrehozásakor lehetővé teszi a forgalom elosztását több virtuális gép között. Azt is biztosítja, hogy a redundancia az alkalmazáshoz több virtuális gép futtatásával, amelyek a felhasználói kérésekre válaszolnak a karbantartás vagy a nagy terhelések esetén. A következő példa létrehoz egy nevű terheléselosztó- `myLoadBalancer`nevet:

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

A terheléselosztó meglehetősen üres, ezért hozzunk létre néhány IP-készletet. Két IP-készletet szeretnénk létrehozni a terheléselosztó számára, egyet az előtér-és egy a háttérrendszer számára. Az előtér-IP-készlet nyilvánosan látható. Emellett a korábban létrehozott PIP-t is kiosztjuk. Ezután a háttérrendszer-készletet használjuk a virtuális gépekhez való kapcsolódáshoz. Így a forgalom a terheléselosztó és a virtuális gépek között is áthaladhat.

Először hozzuk létre az előtér-IP-címkészletet. A következő példa egy nevű `myFrontEndPool`előtér-készletet hoz létre:

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

Figyelje meg, hogy a `--public-ip-name` kapcsolót a korábban létrehozott `myPublicIP` módon továbbítottuk. A nyilvános IP-cím a terheléselosztó számára való hozzárendelésével elérhetővé válik a virtuális gépek elérése az interneten keresztül.

Ezután hozzuk létre a második IP-címkészletet, ezúttal a háttér-forgalomhoz. A következő példa egy nevű `myBackEndPool`háttér-készletet hoz létre:

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

Láthatjuk, hogyan hajtja végre a terheléselosztó a JSON `azure network lb show` -kimenet megkeresésével és vizsgálatával:

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

## <a name="create-load-balancer-nat-rules"></a>Terheléselosztó NAT-szabályainak létrehozása
A terheléselosztó használatával áramló forgalom beszerzéséhez létre kell hoznia egy hálózati címfordítási (NAT) szabályokat, amelyek a bejövő vagy kimenő műveleteket határozzák meg. Megadhatja a használni kívánt protokollt, majd igény szerint leképezheti a külső portokat a belső portokra. A környezetünk számára hozzon létre néhány szabályt, amely engedélyezi az SSH-t a terheléselosztó használatával a virtuális gépeken. A 4222-es és a 4223-es TCP-portokat a virtuális gépeken a 22-es TCP-portra irányítjuk (amelyet később hozunk létre). A következő példa létrehoz egy nevű `myLoadBalancerRuleSSH1` szabályt a 4222-as TCP-port a 22-es portra való leképezéséhez:

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

Ismételje meg az eljárást az SSH második NAT-szabályához. A következő példa létrehoz egy nevű `myLoadBalancerRuleSSH2` szabályt a 4223-as TCP-port a 22-es portra való leképezéséhez:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Nézzük meg, majd hozzon létre egy NAT-szabályt a 80-as TCP-porthoz a webes forgalomhoz, és csatlakoztassa a szabályt az IP-készletekhez. Ha a szabályt egy IP-készlethez csatlakoztatjuk, ahelyett, hogy a szabályt a virtuális gépekre külön-külön csatlakoztatjuk, hozzáadhatunk vagy eltávolíthatunk virtuális gépeket az IP-készletből. A terheléselosztó automatikusan módosítja a forgalom áramlását. A következő példa létrehoz egy nevű `myLoadBalancerRuleWeb` szabályt a 80-es TCP-port a 80-es portra való leképezéséhez:

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
Az állapot-mintavétel rendszeres időközönként ellenőrzi a terheléselosztó mögött lévő virtuális gépeket, így meggyőződhet arról, hogy a rendszer működést végez, és válaszol a megadott kérelmekre. Ha nem, akkor törlődnek a műveletből, így biztosítva, hogy a felhasználók ne legyenek átirányítva. Meghatározhat egyéni ellenőrzéseket az állapot-mintavételhez, valamint az intervallumokat és az időtúllépési értékeket. További információ az állapot-mintavételekről: [Load Balancer](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)mintavétel. A következő példa egy nevű `myHealthProbe`TCP-állapotot hoz létre:

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

Itt 15 másodperces intervallumot adtunk meg az állapot-ellenőrzésekhez. A terheléselosztó előtt legfeljebb négy vizsgálatot lehet kihagyni (egy percet) úgy, hogy a gazdagép már nem működik.

## <a name="verify-the-load-balancer"></a>A terheléselosztó ellenőrzése
Most megtörtént a terheléselosztó konfigurálása. A lépések a következők:

1. Létrehozta a Load balancert.
2. Létrehozott egy előtér-IP-készletet, és hozzárendelt egy nyilvános IP-címet.
3. Létrehozott egy háttérbeli IP-készletet, amelyhez a virtuális gépek csatlakozhatnak.
4. Létrehozott egy NAT-szabályt, amely engedélyezi az SSH-t a virtuális gépek számára a felügyelethez, valamint egy olyan szabályt, amely engedélyezi a 80-es TCP-portot a webalkalmazáshoz.
5. A virtuális gépek rendszeres vizsgálatához hozzáadta az állapot-mintavételt. Ez az állapot-mintavétel biztosítja, hogy a felhasználók ne próbáljon meg olyan virtuális gépet elérni, amely már nem működik vagy nem fog tartalmat kiszolgálni.

Vizsgáljuk meg, hogyan néz ki a terheléselosztó most:

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

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Hozzon létre egy hálózati adaptert a linuxos virtuális géppel való használatra
A hálózati adapterek programozott módon elérhetők, mert szabályokat alkalmazhat a használatára. Több is lehet. A következő `azure network nic create` parancsban csatlakoztatja a hálózati adaptert a betöltési háttér IP-készletéhez, és társítja a NAT-szabállyal az SSH-forgalom engedélyezéséhez.

Cserélje le `#####-###-###` a részeket saját Azure-előfizetés-azonosítójával. Az előfizetési azonosítót a létrehozott erőforrások vizsgálata `jq` során a kimenetében kell megállapítani. Megtekintheti az előfizetési AZONOSÍTÓját `azure account list`is a használatával.

A következő példa egy nevű `myNic1`hálózati adaptert hoz létre:

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

A részleteket az erőforrás közvetlen vizsgálatával tekintheti meg. Az erőforrást a `azure network nic show` parancs használatával vizsgálja meg:

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

Most létrehozjuk a második hálózati adaptert, és újra kell csatlakoztatni a háttérbeli IP-készletet. Ezúttal a második NAT-szabály engedélyezi az SSH-forgalmat. A következő példa egy nevű `myNic2`hálózati adaptert hoz létre:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Hálózati biztonsági csoport és szabályok létrehozása
Most létrehozunk egy hálózati biztonsági csoportot és a bejövő szabályokat, amelyek a hálózati ADAPTERhez való hozzáférést szabályozzák. Hálózati biztonsági csoport is alkalmazható egy hálózati ADAPTERre vagy alhálózatra. Szabályokat határozhat meg a virtuális gépek bejövő és kimenő forgalmának szabályozásához. A következő példa egy nevű `myNetworkSecurityGroup`hálózati biztonsági csoportot hoz létre:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Adjuk hozzá a bejövő kapcsolatokat a NSG, hogy engedélyezzék a bejövő kapcsolatokat a 22-es porton (az SSH támogatásához). A következő példában létrehozunk egy nevű `myNetworkSecurityGroupRuleSSH` szabályt, amely engedélyezi a TCP-t a 22-es porton:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Most adjuk hozzá a NSG bejövő hozzáférését a 80-as porton bejövő kapcsolatok engedélyezéséhez a webes forgalom támogatásához. A következő példa létrehoz egy nevű `myNetworkSecurityGroupRuleHTTP` szabályt, amely engedélyezi a TCP-t a 80-as porton:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> A bejövő hálózati kapcsolatok esetében a Bejövő szabály egy szűrő. Ebben a példában a NSG a virtuális gépek virtuális hálózati adapteréhez köti, ami azt jelenti, hogy a 22-es portra irányuló kérést a virtuális gép hálózati adapterén keresztül továbbítja a rendszer. Ez a Bejövő szabály egy hálózati kapcsolatban szól, és nem a végpontról, hanem a klasszikus telepítések esetén. A portok megnyitásához hagyja meg a " `--source-port-range` \*" (az alapértelmezett érték) beállítást, hogy fogadja a bejövő kérelmeket a kért portokról. A portok általában dinamikusak.
>
>

## <a name="bind-to-the-nic"></a>Kötés a hálózati adapterhez
Kösse a NSG a hálózati adapterekhez. Hálózati biztonsági csoporttal kell összekötni a hálózati adaptereket. Mindkét parancs futtatása mindkét hálózati adapter csatlakoztatásához:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
A rendelkezésre állási csoportok segítenek a virtuális gépeket a tartalék tartományok és a frissítési tartományok között elosztani. Hozzon létre egy rendelkezésre állási készletet a virtuális gépek számára. Az alábbi példa egy nevű `myAvailabilitySet`rendelkezésre állási készletet hoz létre:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

A tartalék tartományok definiálják azokat a virtuális gépeket, amelyek közös áramforrással és hálózati kapcsolóval rendelkeznek. Alapértelmezés szerint a rendelkezésre állási csoporton belül konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elválasztva. Ennek az az elképzelése, hogy a hibás tartományok egyikének hardveres hibája nem befolyásolja az alkalmazást futtató összes virtuális gépet. Az Azure automatikusan elosztja a virtuális gépeket a tartalék tartományok között, amikor egy rendelkezésre állási csoportba helyezi őket.

A frissítési tartományok olyan virtuális gépek és mögöttes fizikai hardverek csoportjait jelölik, amelyek egy időben újraindíthatók. Előfordulhat, hogy a frissítési tartományok újraindítása nem szekvenciális a tervezett karbantartás során, de egyszerre csak egy frissítést indít el újra. Az Azure ismét automatikusan elosztja a virtuális gépeket a frissítési tartományok között, amikor egy rendelkezésre állási helyre helyezi őket.

További információ a [virtuális gépek rendelkezésre állásának kezeléséről](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>A Linux rendszerű virtuális gépek létrehozása
Létrehozta a tárolási és hálózati erőforrásokat az internetről elérhető virtuális gépek támogatásához. Most hozzuk létre ezeket a virtuális gépeket, és biztosítjuk azokat olyan SSH-kulccsal, amely nem rendelkezik jelszóval. Ebben az esetben a legújabb LTS alapján hozunk létre egy Ubuntu-alapú virtuális gépet. A rendszerképet az Azure virtuálisgép- `azure vm image list` [rendszerképek](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)megkeresése című témakörben leírtak alapján keressük meg.

A parancs `azure vm image list westeurope canonical | grep LTS`használatával választottunk egy rendszerképet. Ebben az esetben a következőt `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`használjuk:. Az utolsó mező esetében továbbítjuk `latest` , hogy a jövőben mindig a legfrissebb buildet fogjuk beszerezni. (Az általunk használt `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`karakterlánc:).

Ez a következő lépés mindenki számára ismerős, aki már létrehozott egy nyilvános és titkos SSH RSA-kulcspárt Linuxon vagy Mac gépen az **ssh-keygen-t RSA-b 2048**használatával. Ha nem rendelkezik tanúsítvány-kulcspár a `~/.ssh` címtárban, a következőket hozhatja létre:

* Automatikusan, a `azure vm create --generate-ssh-keys` kapcsoló használatával.
* Manuálisan, [az útmutatás használatával saját maga is létrehozhatja őket](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azt is megteheti, `--admin-password` hogy a metódus használatával hitelesíti az SSH-kapcsolatokat a virtuális gép létrehozása után. Ez a módszer általában kevésbé biztonságos.

A virtuális gépet úgy hozunk létre, hogy az összes erőforrást és információt `azure vm create` a paranccsal együtt hozza létre:

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

Az alapértelmezett SSH-kulcsok használatával azonnal csatlakozhat a virtuális géphez. Győződjön meg arról, hogy a megfelelő portot adja meg, mivel a terheléselosztó áthalad. (Az első virtuális gép esetében a NAT-szabályt úgy állítottuk be, hogy az 4222-es portot a virtuális gépre továbbítsa.)

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
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Lépjen tovább, és hozza létre a második virtuális gépet a következőképpen:

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

Mostantól a `azure vm show myResourceGroup myVM1` paranccsal is ellenőrizheti, hogy mit hozott létre. Ezen a ponton az Ubuntu-alapú virtuális gépeket egy Azure-beli terheléselosztó mögött futtatja, amely csak az SSH-kulcspár használatával jelentkezhet be (mivel a jelszavak le vannak tiltva). Telepítheti az Nginx vagy a httpd alkalmazást, üzembe helyezhet egy webalkalmazást, és megtekintheti a terheléselosztó átmenő forgalmát mindkét virtuális gépre.

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


## <a name="export-the-environment-as-a-template"></a>A környezet exportálása sablonként
Most, hogy kiépítette ezt a környezetet, mi a teendő, ha egy további fejlesztési környezetet szeretne létrehozni ugyanazzal a paraméterekkel, vagy egy olyan éles környezettel, amely megfelel a szolgáltatásnak? A Resource Manager JSON-sablonokat használ, amelyek meghatározzák a környezet összes paraméterét. A JSON-sablonra hivatkozó teljes környezeteket hozhat létre. A JSON- [sablonokat manuálisan](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) is létrehozhatja, vagy egy meglévő környezetet exportálhat a JSON-sablon létrehozásához:

```azurecli
azure group export --name myResourceGroup
```

Ez a parancs az `myResourceGroup.json` aktuális munkakönyvtárban hozza létre a fájlt. Amikor létrehoz egy környezetet a sablonból, a rendszer kéri az összes erőforrás nevét, beleértve a terheléselosztó, a hálózati adapterek vagy a virtuális gépek nevét. Ezeket a neveket feltöltheti a sablon fájljába úgy, hogy hozzáadja `-p` a `--includeParameterDefaultValue` vagy a paramétert a `azure group export` korábban bemutatott parancshoz. Szerkessze a JSON-sablont az erőforrásnevek megadásához, vagy [hozzon létre egy Parameters. JSON fájlt](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , amely megadja az erőforrások nevét.

Környezet létrehozása a sablonból:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Előfordulhat, hogy további információra van szüksége a sablonokból való [üzembe helyezésről](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). További információ a környezetek növekményes frissítéséről, a Parameters fájl használatáról és a sablonok egyetlen tárolási helyről való eléréséről.

## <a name="next-steps"></a>További lépések
Most már készen áll a több hálózati összetevő és virtuális gép használatának megkezdésére. Ezzel a minta környezettel kiépítheti az alkalmazást az itt bemutatott alapvető összetevők használatával.
