---
title: Terheléselosztó zónaredundáns virtuális gépek Azure CLI használatával
titlesuffix: Azure Load Balancer
description: Az Azure CLI-vel zóna redundáns előtérbeli nyilvános Standard Load Balancer létrehozása
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: 7359be235135098779478eebc8a8927e34904ac1
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54197793"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Virtuális gépek terhelésének elosztásához az Azure CLI használatával az összes rendelkezésre állási zónák között

Ez a cikk végigvezeti egy nyilvános létrehozása [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) a zónaredundáns időtúllépést használata több DNS-rekord zóna redundancia érdekében. Egyetlen előtér-IP-cím automatikus zónaredundáns.  A terheléselosztó egyetlen IP-címmel egy zóna redundáns előtérbeli használatával most már elérheti egy virtuális hálózat ugyanabban a régióban, amely minden rendelkezésre állási zónában lévő összes virtuális gép. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

A rendelkezésre állási zónák a Standard Load Balancerrel való használatáról bővebben a [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md) című cikkben tájékozódhat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a helyi telepítése és használata a parancssori felület választja, az oktatóanyaghoz, hogy futnak-e az Azure CLI 2.0.17-es verzió vagy újabb verziója.  A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

> [!NOTE]
> A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroupSLB* a a *westeurope* helye:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Hozzon létre egy zónában zónaredundáns nyilvános IP Standard
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Egy zónaredundáns előtér által kiszolgált minden rendelkezésre állási zónában egy régióban egyszerre. Hozzon létre egy zónában zónaredundáns nyilvános IP-címet [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Amikor létrehoz egy Standard nyilvános IP-címet, alapértelmezés szerint zónaredundánsak.

A következő példában létrehozunk egy zóna zónaredundáns nyilvános IP-címet *myPublicIP* a a *myResourceGroupLoadBalancer* erőforráscsoportot.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Az Azure Standard Load Balancer létrehozása
Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
- a terheléselosztón a bejövő hálózati forgalmat fogadó előtérbeli IP-címkészlet;
- a háttér-IP-címkészlet, ahová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat;
- állapot-mintavétel, amely a háttérbeli virtuálisgép-példányok állapotát határozza meg;
- terheléselosztási szabály, amely megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása
A Standard load balancer létrehozása [az network lb létrehozása](/cli/azure/network/lb#az-network-lb-create). A következő példában létrehozunk egy nevű terheléselosztót *myLoadBalancer* és hozzárendeli a *myPublicIP* címet az előtérbeli IP-konfigurációhoz.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Állapotminta létrehozásához a 80-as porton

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintát az network lb probe a hozhat létre a virtuális gépek állapotának monitorozásához. TCP-állapotminta létrehozásához használja az [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) parancsot. Az alábbi példa egy *myHealthProbe* nevű állapotmintát hoz létre:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Hozzon létre a terheléselosztó-szabályt a 80-as port
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

Hozzon létre egy virtuális hálózatot nevű *myVnet* nevű alhálózattal *mySubnet* a myResourceGroup található [az network vnet létrehozása](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Nevű hálózati biztonsági csoport létrehozása *myNetworkSecurityGroup* bejövő kapcsolatainak meghatározásához a virtuális hálózaton való [az network nsg létrehozása](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt nevű *myNetworkSecurityGroupRule* a 80-as porthoz [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

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
Hozzon létre három virtuális NIC- [az network nic létrehozása](/cli/azure/network/nic#az-network-nic-create) , és rendelje azokat a nyilvános IP-cím és a hálózati biztonsági csoport. A következő példában létrehozunk hat virtuális hálózati adapter. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

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
Ebben a példában az 1. zóna, a 2. zónából és a terheléselosztó háttérkiszolgálóiként használandó 3. zóna található három virtuális gépet hoz létre. Az NGINX-et is telepíti a virtuális gépeket, hogy a terheléselosztó sikeres létrehozásának ellenőrzéséhez.

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

### <a name="create-the-zonal-virtual-machines"></a>A zónaszintű virtuális gépek létrehozása
Hozzon létre a virtuális gépeket a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) az 1. zóna, a 2. zónából és a 3. zóna. Az alábbi példa létrehoz egy virtuális Gépet minden zónában, és az SSH-kulcsokat hoz létre, ha azok még nem léteznének:

Minden zónában (1. zóna zone2 és 3. zóna), a virtuális gép létrehozása a *westeurope* helyét.

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

A terheléselosztó használatával a nyilvános IP-címének lekéréséhez [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
A nyilvános IP-címet beírhatja egy böngészőbe. Ne feledje: néhány percet igénybe vesz, hogy a virtuális gépek készen álljanak arra, hogy a terheléselosztó elkezdje elosztani köztük a forgalmat. Amikor készek, megjelenik az alkalmazás, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![Node.js-alkalmazás futtatása](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Tekintse meg a terheléselosztó az alkalmazást futtató összes három rendelkezésre állási zónában lévő virtuális gépek közötti, egy adott zónában lévő virtuális gép leállítása, és frissítse a böngészőt.

## <a name="next-steps"></a>További lépések
- További tudnivalók a [Standard Load Balancerről](./load-balancer-standard-overview.md)



