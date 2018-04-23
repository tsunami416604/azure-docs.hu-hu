---
title: Nyilvános Standard Load Balancer létrehozása – Azure CLI | Microsoft Docs
description: Megismerheti, hogyan hozható létre nyilvános terheléselosztó az Azure CLI használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: ee2e70e710be9badda7318aec307325da66e4c58
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>Standard Load Balancer létrehozása a virtuális gépek terhelésének elosztásához az Azure CLI 2.0 használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Standard Load Balancert. A terheléselosztó teszteléséhez két, Ubuntu kiszolgálót futtató virtuális gépet helyez üzembe, és elosztja ezek között egy webalkalmazás terhelését.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroupSLB* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-standard-ip-address"></a>Nyilvános standard IP-cím létrehozása

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Az [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) paranccsal hozzon létre egy standard nyilvános IP-címet *myPublicIP* néven a *myResourceGroupSLB* erőforráscsoportban.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

## <a name="create-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - a terheléselosztón a bejövő hálózati forgalmat fogadó előtérbeli IP-címkészlet;
  - a háttér-IP-címkészlet, ahová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat;
  - állapot-mintavétel, amely a háttérbeli virtuálisgép-példányok állapotát határozza meg;
  - terheléselosztási szabály, amely megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

Hozzon létre egy **myLoadBalancer** nevű nyilvános Azure terheléselosztót az [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) paranccsal, amely tartalmaz egy **myFrontEnd** nevű előtérbeli címkészletet, és egy **myBackEndPool** háttércímkészletet, amely az előző lépésben létrehozott **myPublicIP** nyilvános IP-címhez van társítva.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintát az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) paranccsal a virtuális gépek állapotának monitorozásához. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztási szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) paranccsal hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt, amely felügyeli a *myFrontEnd* előtérkészlet 80-as portját, és terhelés szempontjából elosztott forgalmat továbbít a szintén a 80-as portot használó, *myBackEndPool* nevű háttércímkészlethez. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
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

Az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) paranccsal hozzon létre a *myResourceGroup* erőforráscsoportban egy *myVnet* nevű virtuális hálózatot egy *mySubnet* nevű alhálózattal.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Standard Load Balancer használatakor a háttérkiszolgálói címen található virtuális gépeknek rendelkezniük kell a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel. Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre biztonságicsoport-szabályt a 80-as porton keresztül érkező bejövő kapcsolatok engedélyezéséhez.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
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

Hozzon létre három hálózati adaptert az [az network nic create](/cli/azure/network/nic#az_network_nic_create) paranccsal, és rendelje azokat a nyilvános IP-címhez és a hálózati biztonsági csoporthoz. 

```azurecli-interactive
for i in `seq 1 2`; do
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

Ebben a példában három virtuális gépet hoz létre, amelyeket a terheléselosztó háttérkiszolgálóiként fog használni. A terheléselosztó sikeres létrehozásának ellenőrzéséhez az NGINX-et is telepíti a virtuális gépekre.

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Hozzon létre egy rendelkezésre állási csoportot az [az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create) paranccsal.

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupSLB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

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
 
Hozza létre a virtuális gépeket az [az vm create](/cli/azure/vm#az_vm_create) paranccsal.

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    --no-wait
    done
```
A virtuális gépek üzembe helyezése eltarthat néhány percig.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A terheléselosztó nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
   ![Terheléselosztó tesztelése](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive 
  az group delete --name myResourceGroupSLB
```
## <a name="next-step"></a>Következő lépés
További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)

