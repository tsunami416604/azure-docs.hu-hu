---
title: 'Gyors útmutató: Egy alapszintű Load Balancer létrehozása – Azure CLI'
titlesuffix: Azure Load Balancer
description: Ez a rövid útmutató ismerteti, hogyan hozható létre nyilvános Load Balancer az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.custom: mvc
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: kumud
ms.openlocfilehash: 60cdf251d9a862f8bae80f4f782c6ad94c0b85ce
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260769"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-azure-cli"></a>Gyors útmutató: Azure CLI használatával virtuális gépek terhelésének elosztásához load balancer létrehozása

Ez a rövid útmutató bemutatja, hogyan hozzon létre egy Azure Load Balancer betöltése az Azure-beli virtuális gépek közötti internetes forgalom elosztására. A terheléselosztó teszteléséhez két, Ubuntu kiszolgálót futtató virtuális gépet helyez üzembe, és elosztja ezek között egy webalkalmazás terhelését.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroupLB* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Az [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) paranccsal hozzon létre egy nyilvános IP-címet *myPublicIP* néven a *myResourceGroupLB* erőforráscsoportban.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupLB --name myPublicIP
```

## <a name="create-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - a terheléselosztón a bejövő hálózati forgalmat fogadó előtérbeli IP-címkészlet;
  - a háttér-IP-címkészlet, ahová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat;
  - állapot-mintavétel, amely a háttérbeli virtuálisgép-példányok állapotát határozza meg;
  - terheléselosztási szabály, amely megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

Hozzon létre egy **myLoadBalancer** nevű nyilvános Azure Load Balancert az [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) paranccsal, amely tartalmaz egy **myFrontEndPool** nevű előtérbeli készletet, és egy **myBackEndPool** háttérkészletet, amely az előző lépésben létrehozott **myPublicIP** nyilvános IP-címhez van társítva.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintát az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) paranccsal a virtuális gépek állapotának monitorozásához. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztási szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalomhoz, a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) paranccsal a *myFrontEndPool* nevű előtérbeli címkészlet 80-as portjának figyeléséhez és az elosztott terhelésű hálózati forgalomnak a *myBackEndPool* nevű háttércímkészletre való küldéséhez, amely a 80-as portot használja. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
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

Az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) paranccsal hozzon létre a *myResourceGroup* erőforráscsoportban egy *myVnet* nevű virtuális hálózatot egy *mySubnet* nevű alhálózattal.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre biztonságicsoport-szabályt a 80-as porton keresztül érkező bejövő kapcsolatok engedélyezéséhez.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
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

Hozzon létre három hálózati adaptert az [az network nic create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és rendelje azokat a nyilvános IP-címhez és a hálózati biztonsági csoporthoz. 

```azurecli-interactive
for i in `seq 1 2`; do
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

Ebben a példában három virtuális gépet hoz létre, amelyeket a terheléselosztó háttérkiszolgálóiként fog használni. A terheléselosztó sikeres létrehozásának ellenőrzéséhez az NGINX-et is telepíti a virtuális gépekre.

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Hozzon létre egy rendelkezésre állási csoportot az [az vm availabilityset create](/cli/azure/network/nic#az-network-availabilityset-create) paranccsal.

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupLB \
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
 
Hozza létre a virtuális gépeket az [az vm create](/cli/azure/vm#az-vm-create) paranccsal.

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupLB \
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

A terheléselosztó nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
![Terheléselosztó tesztelése](./media/load-balancer-get-started-internet-arm-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive 
  az group delete --name myResourceGroupLB
```


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban létrehozott egy alapszintű terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt, majd tesztelte a terheléselosztót. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-basic-internal-portal.md)
