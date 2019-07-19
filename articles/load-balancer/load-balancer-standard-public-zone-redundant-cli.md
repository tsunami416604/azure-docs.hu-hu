---
title: Terheléselosztási zóna – redundáns virtuális gépek az Azure CLI-vel
titlesuffix: Azure Load Balancer
description: Megtudhatja, hogyan hozhat létre nyilvános standard Load Balancer a zóna redundáns felületének használatával az Azure CLI-vel
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: 6a22ac9a2727c537d98e692e67076637fe8cc457
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274333"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Virtuális gépek terheléselosztása az összes rendelkezésre állási zónában az Azure CLI használatával

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre egy nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) egy zóna-redundáns előtéri felülettel a zónák közötti redundancia érdekében, több DNS-rekordtól való függőség nélkül. Egyetlen előtéri IP-cím automatikusan redundáns.  Egy zóna redundáns előtérbeli felületének használata a terheléselosztó számára, egyetlen IP-címmel, amely az összes Availability Zonesban elérhető virtuális hálózatban lévő virtuális gépekre is felhasználható. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

A rendelkezésre állási zónák a Standard Load Balancerrel való használatáról bővebben a [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md) című cikkben tájékozódhat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.17 vagy újabb verzióját kell futtatnia.  A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

> [!NOTE]
> A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myresourcegroupslb erőforráscsoportban* nevű erőforráscsoportot a *westeurope* helyen:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Zóna redundáns nyilvános IP-cím létrehozása
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Egy zónában – a redundáns kezelőfelületet a régió minden rendelkezésre állási zónája kiszolgálja egyszerre. Hozzon létre egy zóna redundáns nyilvános IP-címét az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create)paranccsal. Szabványos nyilvános IP-cím létrehozásakor alapértelmezés szerint a zóna redundáns.

A következő példa létrehoz egy *myPublicIP* nevű zóna redundáns nyilvános IP-címet a *myResourceGroupLoadBalancer* erőforráscsoporthoz.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Azure-standard Load Balancer létrehozása
Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
- a terheléselosztón a bejövő hálózati forgalmat fogadó előtérbeli IP-címkészlet;
- a háttér-IP-címkészlet, ahová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat;
- állapot-mintavétel, amely a háttérbeli virtuálisgép-példányok állapotát határozza meg;
- terheléselosztási szabály, amely megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása
Hozzon létre egy standard Load balancert az [az Network LB Create](/cli/azure/network/lb#az-network-lb-create)paranccsal. A következő példa létrehoz egy *myLoadBalancer* nevű terheléselosztó-nevet, és hozzárendeli a *myPublicIP* címet az előtér-IP-konfigurációhoz.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Állapot-mintavétel létrehozása a 80-es porton

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapot-mintavételt az az Network LB Probe Create paranccsal a virtuális gépek állapotának figyeléséhez. TCP-állapotminta létrehozásához használja az [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) parancsot. Az alábbi példa egy *myHealthProbe* nevű állapotmintát hoz létre:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Terheléselosztó-szabály létrehozása a 80-es porthoz
A terheléselosztási szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) paranccsal a *myFrontEndPool* nevű előtérbeli címkészlet 80-as portjának figyeléséhez és az elosztott terhelésű hálózati forgalomnak a *myBackEndPool* nevű háttércímkészletre való küldéséhez, amely a 80-as portot használja.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
Mielőtt üzembe helyezné a virtuális gépeket, és tesztelné a terheléselosztót, hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy *myVnet* nevű virtuális hálózatot egy *mySubnet* nevű alhálózattal a myResourceGroup az [az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal.


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot a virtuális hálózatra irányuló bejövő kapcsolatok definiálásához az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal.

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Hozzon létre egy *myNetworkSecurityGroupRule* nevű hálózati biztonsági csoportra vonatkozó szabályt az 80-as porthoz az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal.

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>Hálózati adapterek létrehozása
Hozzon létre három virtuális hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és társítsa azokat a nyilvános IP-címhez és a hálózati biztonsági csoporthoz. Az alábbi példa hat virtuális hálózati adaptert hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása
Ebben a példában három virtuális gépet hoz létre, amelyek az 1. zónában, a 2. zónában és a 3. zónában találhatók a terheléselosztó háttér-kiszolgálóiként való használatra. Az NGINX-et a virtuális gépeken is telepítheti annak ellenőrzéséhez, hogy a terheléselosztó létrehozása sikeres volt-e.

### <a name="create-cloud-init-config"></a>Cloud-init konfiguráció létrehozása

Egy cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy „Hello World” Node.js-alkalmazást a Linux rendszerű virtuális gépeken. Az aktuális parancshéjban hozzon létre egy cloud-init.txt nevű fájlt, majd másolja és illessze be a következő konfigurációt a parancshéjba. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-the-zonal-virtual-machines"></a>A zóna virtuális gépek létrehozása
Hozza létre a virtuális gépeket az [az VM Create](/cli/azure/vm#az-vm-create) paranccsal az 1. zónában, a 2. zónában és a 3. zónában. Az alábbi példa egy virtuális gépet hoz létre minden zónában, és SSH-kulcsokat hoz létre, ha azok még nem léteznek:

Hozzon létre egy virtuális gépet a *westeurope* hely minden zónájában (1. zóna, zone2 és 3. zóna).

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Szerezze be a terheléselosztó nyilvános IP-címét az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)paranccsal. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
A nyilvános IP-címet beírhatja egy böngészőbe. Ne feledje: néhány percet igénybe vesz, hogy a virtuális gépek készen álljanak arra, hogy a terheléselosztó elkezdje elosztani köztük a forgalmat. Amikor készek, megjelenik az alkalmazás, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![Node.js-alkalmazás futtatása](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Ha látni szeretné, hogy a terheléselosztó az alkalmazást futtató mindhárom rendelkezésre állási zónában osztja szét a forgalmat a virtuális gépek között, leállíthatja a virtuális gépet egy adott zónában, és frissítheti a böngészőt.

## <a name="next-steps"></a>További lépések
- További tudnivalók a [Standard Load Balancerről](./load-balancer-standard-overview.md)



