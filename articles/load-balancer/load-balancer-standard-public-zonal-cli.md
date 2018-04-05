---
title: Hozzon létre egy Load Balancer szabványos nyilvános zonal nyilvános IP-cím cím előtér Azure parancssori felületével |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Load Balancer szabványos nyilvános zonal nyilvános IP-cím cím előtér Azure parancssori felület használatával
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
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 0932195bb95ab9610f723245bfed7fedb01001f9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-cli"></a>Hozzon létre egy Load Balancer szabványos nyilvános zonal előtér Azure parancssori felület használatával

Ez a cikk lépésről-lépésre nyilvános létrehozása [Load Balancer szabványos](https://aka.ms/azureloadbalancerstandard) rendelkező egy zonal előtérbeli IP szabványos nyilvános cím segítségével. Ebben a forgatókönyvben adjon meg egy adott zónában az előtér- és példányok, akkor igazíthatók az elérési útja és erőforrások eléréséről a megadott zónában.

Rendelkezésre állási zónák használatával szabványos terheléselosztással kapcsolatos további információkért lásd: [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha telepíti, és a parancssori felület helyileg választja, győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) és az Azure-fiókkal bejelentkezett [az bejelentkezési](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Rendelkezésre állási zónák támogatása select Azure-erőforrások és a régiók és a virtuális gép mérete családok érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupLB* a a *westeurope* helye:

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>Hozzon létre egy zonal nyilvános IP Standard
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Egy nyilvános IP-címet, amely jön létre a megadott zónában mindig csak adott zónában van. Nincs lehetőség a zóna egy nyilvános IP-cím módosításához.

Hozzon létre egy nyilvános IP-címet a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) paranccsal. Az alábbi példakód létrehozza nevű zonal nyilvános IP-cím *myPublicIP* a a *myResourceGroupLoadBalancer* erőforráscsoport zónában 1.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
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
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Hozzon létre állapotmintáihoz 80-as porton

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintáihoz az hálózati lb mintavételi létrehozása a virtuális gépek állapotának figyelésére. TCP-állapotminta létrehozásához használja az [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) parancsot. Az alábbi példa egy *myHealthProbe* nevű állapotmintát hoz létre:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>A 80-as port terheléselosztási szabály létrehozása
Olyan terheléselosztó szabályhoz határozza meg, hogy az előtér-IP-konfigurációjának megadása a bejövő forgalom és a háttér IP-címkészlet, valamint a megfelelő forrás és cél portot forgalom fogadására. Hozzon létre olyan terheléselosztó szabályhoz *myLoadBalancerRuleWeb* rendelkező [az hálózati terheléselosztó szabály létrehozása](/cli/azure/network/lb/rule#az_network_lb_rule_create) az előtér-készletben a 80-as porton figyel *myFrontEndPool* és küldése hálózati forgalmat az elosztott terhelésű a háttér címkészletet *myBackEndPool* is a 80-as portot használja.

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
Mielőtt központilag az egyes virtuális gépek, és tesztelheti a terheléselosztóhoz, hozzon létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot nevű *myVnet* nevű alhálózattal *mySubnet* be a contoso.com [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hálózati biztonsági csoport nevű létrehozása *myNetworkSecurityGroup* bejövő kapcsolatok meghatározásához a virtuális hálózaton található [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Hozzon létre egy hálózati biztonsági szabály nevű *myNetworkSecurityGroupRule* a 80-as port [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

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
Hozzon létre három virtuális hálózati adapterrel [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create) , és rendelje hozzá őket a nyilvános IP-cím és a hálózati biztonsági csoport. Az alábbi példa három virtuális NIC-t hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

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
## <a name="create-backend-servers"></a>Háttér-kiszolgálókat hoz létre
Ebben a példában a terheléselosztóhoz tartozó háttér-kiszolgálóként használandó 1 található három virtuális gépet hoz létre. Ellenőrizze, hogy a terheléselosztó sikeresen létrejött-e a virtuális gépeken telepítése NGINX is.

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
A virtuális gépek létrehozása [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Az alábbi példában három virtuális gépek zóna 1 hoz létre, és SSH-kulcsokat generál, ha még nem léteznek:

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

![Node.js-alkalmazás futtatása](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Tekintse meg a terheléselosztó terjesztése a forgalmat a virtuális gépek futnak az alkalmazás 1 zónán belül, akkor is kényszerített frissítési a webböngésző.

## <a name="next-steps"></a>További lépések
- További információ [szabványos terheléselosztó](./load-balancer-standard-overview.md).



