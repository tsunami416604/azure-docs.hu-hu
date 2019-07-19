---
title: standard Load Balancer létrehozása az Azure parancssori felületének használatával
titlesuffix: Azure Load Balancer
description: Megtudhatja, hogyan hozhat létre nyilvános standard Load Balancert az Azure CLI-vel rendelkező, Zona nyilvános IP-címekkel rendelkező előtér használatával
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 7da41456a4f4bb88d402d27b42b31f6d4adfa7f6
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274318"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>standard Load Balancer létrehozása az Azure parancssori felületének használatával

Ez a cikk egy nyilvános IP-címet használó nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) létrehozásával foglalkozik. Ennek a forgatókönyvnek a követésével meg fog adni egy meghatározott zónát az előtér- és háttérpéldányokhoz, hogy hozzáigazíthassa az adatkapcsolatot és erőforrásokat a meghatározott zónához.

A rendelkezésre állási zónák a Standard Load Balancerrel való használatáról bővebben a [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md) című cikkben tájékozódhat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület választja, győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és az Azure-fiókkal jelentkezett be [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
>  A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myresourcegrouplb erőforráscsoportban* nevű erőforráscsoportot a *westeurope* helyen:

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>A Zona Public IP standard létrehozása
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Egy adott zónában létrehozott nyilvános IP-cím mindig csak az adott zónában van. Nyilvános IP-cím zónájának módosítása nem lehetséges.

Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal. A következő példa létrehoz egy *myPublicIP* nevű Zona nyilvános IP-címet az 1. zónában található *myResourceGroupLoadBalancer* -erőforráscsoport számára.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-standard-load-balancer"></a>Azure-standard Load Balancer létrehozása
Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
- a terheléselosztón a bejövő hálózati forgalmat fogadó előtérbeli IP-címkészlet;
- a háttér-IP-címkészlet, ahová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat;
- állapot-mintavétel, amely a háttérbeli virtuálisgép-példányok állapotát határozza meg;
- terheléselosztási szabály, amely megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása
Hozzon létre egy standard Load Balancer az [az Network LB Create](/cli/azure/network/lb#az-network-lb-create)paranccsal. A következő példa létrehoz egy *myLoadBalancer* nevű terheléselosztó-nevet, és hozzárendeli a *myPublicIP* címet az előtér-IP-konfigurációhoz.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Állapot-mintavétel létrehozása a 80-es porton

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapot-mintavételt az az Network LB Probe Create paranccsal a virtuális gépek állapotának figyeléséhez. TCP-állapotminta létrehozásához használja az [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) parancsot. Az alábbi példa egy *myHealthProbe* nevű állapotmintát hoz létre:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Terheléselosztó-szabály létrehozása a 80-es porthoz
A terheléselosztási szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) paranccsal a *myFrontEndPool* nevű előtérbeli címkészlet 80-as portjának figyeléséhez és az elosztott terhelésű hálózati forgalomnak a *myBackEndPool* nevű háttércímkészletre való küldéséhez, amely a 80-as portot használja.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
Mielőtt üzembe helyezné a virtuális gépeket, és tesztelné a terheléselosztót, hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy *myVnet* nevű virtuális hálózatot egy *mySubnet* nevű alhálózattal a myResourceGroup az [az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal.


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot a virtuális hálózatra irányuló bejövő kapcsolatok definiálásához az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal.

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Hozzon létre egy *myNetworkSecurityGroupRule* nevű hálózati biztonsági csoportra vonatkozó szabályt az 80-as porthoz az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal.

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupLB \
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
Hozzon létre három virtuális hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és társítsa azokat a nyilvános IP-címhez és a hálózati biztonsági csoporthoz. Az alábbi példa három virtuális NIC-t hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása
Ebben a példában három, az 1. zónában található virtuális gépet hoz létre a terheléselosztó háttér-kiszolgálóiként való használathoz. Az NGINX-et a virtuális gépeken is telepítheti annak ellenőrzéséhez, hogy a terheléselosztó létrehozása sikeres volt-e.

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
Hozza létre a virtuális gépeket az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal. Az alábbi példa három virtuális gépet hoz létre az 1. zónában, és SSH-kulcsokat hoz létre, ha azok még nem léteznek:

```azurecli-interactive
for i in `seq 1 3`; do
 az vm create \
--resource-group myResourceGroupLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone 1 \
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

![Node.js-alkalmazás futtatása](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Ha szeretné megtekinteni, hogy a terheléselosztó az 1. zónán belüli virtuális gépekre osztja el az alkalmazást, akkor kényszerítheti a webböngésző frissítését.

## <a name="next-steps"></a>További lépések
- További tudnivalók a [Standard Load Balancerről](./load-balancer-standard-overview.md).



