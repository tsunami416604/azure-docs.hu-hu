---
title: Belső alapszintű terheléselosztó létrehozása – Azure CLI | Microsoft Docs
description: Ismerje meg, hogyan hozható létre belső terheléselosztó az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/27/2018
ms.author: kumud
ms.openlocfilehash: 369c47a48d49a91985f7a9534230e04cff1e7ce6
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413689"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Belső terheléselosztó létrehozása a virtuális gépek terhelésének elosztásához az Azure CLI használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre belső terheléselosztót a virtuális gépek terhelésének elosztása érdekében. A terheléselosztó teszteléséhez két, Ubuntu kiszolgálót futtató virtuális gépet helyez üzembe a webalkalmazások terhelésének elosztása érdekében.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy *myResourceGroupILB* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) paranccsal hozzon létre a *myResourceGroup* erőforráscsoportban egy *myVnet* nevű virtuális hálózatot egy *mySubnet* nevű alhálózattal.

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - a terheléselosztón a bejövő hálózati forgalmat fogadó előtérbeli IP-konfiguráció.
  - a háttér-IP-címkészlet, ahová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat;
  - állapot-mintavétel, amely a háttérbeli virtuálisgép-példányok állapotát határozza meg;
  - terheléselosztási szabály, amely megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

Hozzon létre egy **myLoadBalancer** nevű nyilvános alapszintű terheléselosztót az [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) paranccsal, amely tartalmaz egy **myFrontEnd** nevű előtérbeli IP-konfigurációt, egy **myBackEndPool** nevű háttérbeli készletet, és a **10.0.0.7 magánhálózati IP-címmel van társítva.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom fogadására. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintát az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) paranccsal a virtuális gépek állapotának monitorozásához. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztási szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myHTTPRule* nevű terheléselosztási szabályt az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) paranccsal a *myFrontEnd* nevű előtérbeli címkészlet 80-as portjának figyeléséhez és az elosztott terhelésű hálózati forgalomnak a *myBackEndPool* nevű háttércímkészletre való küldéséhez, amely a 80-as portot használja. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>Kiszolgálók létrehozása a háttércímkészlethez

Mielőtt üzembe helyezné a virtuális gépeket, és tesztelné a terheléselosztót, hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-nics"></a>Hálózati adapterek létrehozása

Hozzon létre két hálózati adaptert az [az network nic create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és rendelje hozzá őket a magánhálózati IP-címhez. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában két virtuális gépet hoz létre, amelyeket a terheléselosztó háttérkiszolgálóiként fog használni. A terheléselosztó sikeres létrehozásának ellenőrzéséhez az NGINX-et is telepíti a virtuális gépekre.

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Hozzon létre egy rendelkezésre állási csoportot az [az vm availabilityset create](/cli/azure/network/nic#az-network-availabilityset-create) paranccsal.

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
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
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
A virtuális gépek üzembe helyezése eltarthat néhány percig.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Virtuális gép létrehozása a terheléselosztó teszteléséhez

A terheléselosztó teszteléséhez hozzon létre egy *myVMTest* nevű virtuális gépet, és társítsa a *myNic3* nevű hálózati adapterrel.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>A belső terheléselosztó tesztelése

A terheléselosztó teszteléséhez először be kell szereznie a terheléselosztó magánhálózati IP-címét. Ezután jelentkezzen be a myVMTest nevű virtuális gépre, és írja be a magánhálózati IP-címet a webböngésző címsorába.

A terheléselosztó magánhálózati IP-címének lekéréséhez használja az [az network lb show](/cli/azure/network/public-ip##az-network-lb-show) parancsot. Másolja a magánhálózati IP-címet, majd illessze be azt a *myVMTest* nevű virtuális gép egy webböngészőjének címsorába.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
``` 
![Terheléselosztó tesztelése](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy belső alapszintű terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmának szabályát, az állapotmintát, majd tesztelte a terheléselosztót. Ha további információra van szüksége a terheléselosztókkal és a kapcsolódó erőforrásokkal kapcsolatban, lépjen tovább az útmutató cikkekre.
