---
title: A terhelést zónaredundáns virtuális gépek Azure parancssori felületével |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Load Balancer szabványos nyilvános zóna redundáns előtér Azure parancssori felület használatával
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: 29dcfaad840b5498dd859082ce11655a4f1fe8af
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Terheléselosztás virtuális gépek közötti összes rendelkezésre állási zóna Azure parancssori felület használatával

Ez a cikk lépésről-lépésre nyilvános létrehozása [Load Balancer szabványos](https://aka.ms/azureloadbalancerstandard) a zóna-redundancia több DNS-rekordok függőség nélkül eléréséhez zónaredundáns időtúllépést. Egyetlen előtér-IP-címnek automatikusan zónaredundáns.  Zóna redundáns időtúllépést alkalmaz a terheléselosztó egyetlen IP-címmel most érhető el minden virtuális gép virtuális hálózatban, amely az összes rendelkezésre állási zóna között régión belül. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Rendelkezésre állási zónák használatával szabványos terheléselosztással kapcsolatos további információkért lásd: [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Rendszererőforrásokra telepíti, és a parancssori felület helyileg, ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.17 verzió vagy újabb verzióját.  A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Rendelkezésre állási zónák támogatása select Azure-erőforrások és a régiók és a virtuális gép mérete családok érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupSLB* a a *westeurope* helye:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Zóna létrehozása redundáns nyilvános IP Standard
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. A zónaredundáns előtér-által kiszolgált összes rendelkezésre állási zónák régióban egyidejűleg. Hozzon létre egy zóna redundáns nyilvános IP-címmel [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Amikor létrehoz egy szabványos nyilvános IP-címet, nem zónaredundáns alapértelmezés szerint.

Az alábbi példakód létrehozza a zóna redundáns nyilvános IP-cím nevű *myPublicIP* a a *myResourceGroupLoadBalancer* erőforráscsoportot.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-load-balancer-standard"></a>Az Azure Load Balancer Standard létrehozása
Ez a szakasz részletesen felsorolja, hogyan hozhat létre, és a terheléselosztó a következő összetevők konfigurálása:
- egy előtérbeli IP-címkészlet, amely megkapja a bejövő hálózati forgalmat a terheléselosztón.
- a háttér IP-készlet, amennyiben az előtér-készlet elküldi a terhelés eloszlik a hálózati forgalom.
- határozza meg, hogy a háttér Virtuálisgép-példányok állapotát állapotmintáihoz.
- olyan terheléselosztó szabályhoz, amely meghatározza, hogyan adatforgalom elosztása a virtuális gépekhez.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása
Hozzon létre egy szabványos terheléselosztót, [az hálózati terheléselosztó létrehozása](/cli/azure/network/lb#az_network_lb_create). Az alábbi példa létrehoz egy terhelés-kiegyenlítő nevű *myLoadBalancer* és hozzárendeli a *myPublicIP* cím előtér-IP-konfigurációhoz.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Hozzon létre állapotmintáihoz 80-as porton

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintáihoz az hálózati lb mintavételi létrehozása a virtuális gépek állapotának figyelésére. TCP-állapotminta létrehozásához használja az [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) parancsot. Az alábbi példa egy *myHealthProbe* nevű állapotmintát hoz létre:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>A 80-as port terheléselosztási szabály létrehozása
Olyan terheléselosztó szabályhoz határozza meg, hogy az előtér-IP-konfigurációjának megadása a bejövő forgalom és a háttér IP-címkészlet, valamint a megfelelő forrás és cél portot forgalom fogadására. Hozzon létre olyan terheléselosztó szabályhoz *myLoadBalancerRuleWeb* rendelkező [az hálózati terheléselosztó szabály létrehozása](/cli/azure/network/lb/rule#az_network_lb_rule_create) az előtér-készletben a 80-as porton figyel *myFrontEndPool* és küldése hálózati forgalmat az elosztott terhelésű a háttér címkészletet *myBackEndPool* is a 80-as portot használja.

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
Mielőtt központilag az egyes virtuális gépek, és tesztelheti a terheléselosztóhoz, hozzon létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot nevű *myVnet* nevű alhálózattal *mySubnet* be a contoso.com [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hálózati biztonsági csoport nevű létrehozása *myNetworkSecurityGroup* bejövő kapcsolatok meghatározásához a virtuális hálózaton található [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Hozzon létre egy hálózati biztonsági szabály nevű *myNetworkSecurityGroupRule* a 80-as port [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

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
Hozzon létre három virtuális hálózati adapterrel [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create) , és rendelje hozzá őket a nyilvános IP-cím és a hálózati biztonsági csoport. Az alábbi példakód létrehozza a hat virtuális hálózati adapter. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

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
## <a name="create-backend-servers"></a>Háttér-kiszolgálókat hoz létre
Ebben a példában a zóna 1, 2, és zónát a terheléselosztóhoz tartozó háttér-kiszolgálóként használandó 3 található három virtuális gépet hoz létre. Ellenőrizze, hogy a terheléselosztó sikeresen létrejött-e a virtuális gépeken telepítése NGINX is.

### <a name="create-cloud-init-config"></a>Cloud-init konfiguráció létrehozása

A felhő inicializálás konfigurációs fájl segítségével NGINX telepítheti és futtathatja a "Hello, World" Node.js alkalmazást egy Linux virtuális gépen. Az aktuális rendszerhéjban hozzon létre egy fájlt felhő-init.txt, másolja és illessze be a következő konfigurációs a rendszerhéj. Győződjön meg arról, hogy a teljes másolása felhő inicializálás fájl helyes, különösen az első sor:

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

### <a name="create-the-zonal-virtual-machines"></a>A zonal virtuális gépek létrehozása
A virtuális gépek létrehozása [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) a zóna 1, 2, és zónát 3. Az alábbi példa létrehoz egy virtuális gép minden egyes zónában, és SSH-kulcsokat generál, ha még nem léteznek:

A zóna 1 virtuális gépek létrehozása

```azurecli-interactive
 az vm create \
--resource-group myResourceGroupSLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone $i \
--custom-data cloud-init.txt
```
## <a name="test-the-load-balancer"></a>A load balancer tesztelése

A betöltési terheléselosztó használatával a nyilvános IP-cím beszerzése [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az_network_public_ip_show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
A nyilvános IP-címet beírhatja egy böngészőbe. Ne feledje: néhány percet igénybe vesz, hogy a virtuális gépek készen álljanak arra, hogy a terheléselosztó elkezdje elosztani köztük a forgalmat. Amikor készek, megjelenik az alkalmazás, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![Node.js-alkalmazás futtatása](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

A load balancer forgalom szét a három rendelkezésre állási zónák mindegyikében az alkalmazást futtató virtuális gépek megtekintéséhez állítsa le a virtuális Gépet egy adott zónában, és frissítse a böngészőt.

## <a name="next-steps"></a>További lépések
- További információ [szabványos terheléselosztó](./load-balancer-standard-overview.md)



