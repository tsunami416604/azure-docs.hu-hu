---
title: Rövid útmutató:Nyilvános terheléselosztó létrehozása – Azure CLI
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató ismerteti, hogyan hozható létre nyilvános Load Balancer az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 1f6a05fdfc28adf412ffbd1402e37b69d1c51634
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79477765"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rövid útmutató: Hozzon létre egy standard terheléselosztót a terheléselosztási virtuális gépek hez az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre nyilvános terheléselosztót. A terheléselosztó teszteléséhez két, Ubuntu kiszolgálót futtató virtuális gépet helyez üzembe, és elosztja ezek között egy webalkalmazás terhelését.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroupSLB* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Használja [az hálózati nyilvános ip create-t](https://docs.microsoft.com/cli/azure/network/public-ip) egy standard zóna redundáns nyilvános IP-cím létrehozásához, *amelyet myPublicIP-nek* neveznek a *myResourceGroupSLB-ben.*

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Zónaszintű nyilvános IP-cím létrehozása az 1.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Alapszintű nyilvános IP-cím létrehozásához használható. `-SKU Basic` Az alapvető nyilvános IP-k nem kompatibilisek a **standard** terheléselosztóval. A Microsoft azt javasolja, hogy a **Standard** éles számítási feladatokhoz.

> [!IMPORTANT]
> A rövid útmutató többi része feltételezi, hogy a **standard** termékváltozat a fenti termékváltozat-kiválasztási folyamat során kerül kiválasztásra.

## <a name="create-azure-load-balancer"></a>Azure load-elosztó létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - a terheléselosztón a bejövő hálózati forgalmat fogadó előtérbeli IP-címkészlet;
  - a háttér-IP-címkészlet, ahová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat;
  - állapot-mintavétel, amely a háttérbeli virtuálisgép-példányok állapotát határozza meg;
  - terheléselosztási szabály, amely megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

Hozzon létre egy **myLoadBalancer** nevű nyilvános Azure Load Balancert az [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) paranccsal, amely tartalmaz egy **myFrontEnd** nevű előtérbeli készletet, és egy **myBackEndPool** háttérkészletet, amely az előző lépésben létrehozott **myPublicIP** nyilvános IP-címhez van társítva. Alapszintű nyilvános IP-cím létrehozásához használható. `--sku basic` A Microsoft standard termékváltozatot javasol az éles számítási feladatokhoz.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

> [!IMPORTANT]
> A rövid útmutató többi része feltételezi, hogy a **standard** termékváltozat a fenti termékváltozat-kiválasztási folyamat során kerül kiválasztásra.

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintát az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) paranccsal a virtuális gépek állapotának monitorozásához. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztási szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalomhoz, a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) paranccsal hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt, amely felügyeli a *myFrontEnd* előtérkészlet 80-as portját, és terhelés szempontjából elosztott forgalmat továbbít a szintén a 80-as portot használó, *myBackEndPool* nevű háttércímkészlethez. 

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

Az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) paranccsal hozzon létre a *myResourceGroup* erőforráscsoportban egy *myVnet* nevű virtuális hálózatot egy *mySubnet* nevű alhálózattal.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

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

Hozzon létre három hálózati adaptert az [az network nic create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és rendelje azokat a nyilvános IP-címhez és a hálózati biztonsági csoporthoz. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában három virtuális gépet hoz létre, amelyeket a terheléselosztó háttérkiszolgálóiként fog használni. A terheléselosztó sikeres létrehozásának ellenőrzéséhez az NGINX-et is telepíti a virtuális gépekre.

Ha alapszintű nyilvános IP-című alapszintű terheléselosztót hoz létre, létre kell hoznia egy rendelkezésre állási készletet ([az a vm availabilityset létrehozása](/cli/azure/network/nic) a virtuális gépek hozzáadásához. A standard terheléselosztók nem igénylik ezt a további lépést. A Microsoft a Standard használatát javasolja.

### <a name="create-three-virtual-machines"></a>Három virtuális gép létrehozása

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

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```

A virtuális gépek üzembe helyezése eltarthat néhány percig.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A terheléselosztó nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

   ![Terheléselosztó tesztelése](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive
  az group delete --name myResourceGroupSLB
```

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban létrehozott egy standard terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályt, az állapotmintát, majd tesztelte a terheléselosztót. Ha többet szeretne megtudni az Azure Load Balancer-ről, folytassa az [Azure Load Balancer oktatóanyagokkal.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

További információ a [terheléselosztóról és a rendelkezésre állási zónákról.](load-balancer-standard-availability-zones.md)