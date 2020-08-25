---
title: 'Rövid útmutató: nyilvános terheléselosztó létrehozása – Azure CLI'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató ismerteti, hogyan hozható létre nyilvános Load Balancer az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: b437bfa205833594c9e76c6f0d8ff1923f51f117
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762708"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rövid útmutató: Nyilvános Load Balancer létrehozása a virtuális gépek terhelésének elosztásához az Azure CLI használatával

Ismerkedjen meg Azure Load Balancer az Azure CLI használatával, és hozzon létre egy nyilvános Load balancert és három virtuális gépet.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure CLI vagy Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)paranccsal:

* Elnevezett **myresourcegrouplb erőforráscsoportban**. 
* A **eastus** helyen.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléselosztó tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt)paranccsal:

* Elnevezett **myVNet**.
* A **10.1.0.0/16**címnek az előtagja.
* **MyBackendSubnet**nevű alhálózat.
* A **10.1.0.0/24**alhálózati előtag.
* A **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz.
* A **eastus**helye.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A standard Load Balancer esetében a háttérbeli címen lévő virtuális gépeknek a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell rendelkezniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)paranccsal:

* Elnevezett **myNSG**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt [az az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)paranccsal:

* Elnevezett **myNSGRuleHTTP**.
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Protokoll **(*)**.
* Irány **bejövő**.
* Forrás **(*)**.
* Cél **(*)**.
* Célport **portszáma 80**.
* Hozzáférés **engedélyezése**.
* **200**prioritás.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert az [az Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)paranccsal:

#### <a name="vm1"></a>VM1

* Elnevezett **myNicVM1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Elnevezett **myNicVM2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Elnevezett **myNicVM3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban a következőket hozza létre:

* Egy **cloud-init.txt** nevű Felhőbeli konfigurációs fájl a kiszolgáló konfigurációjához.
* Három virtuális gép, amely háttér-kiszolgálóként használható a terheléselosztó számára.

### <a name="create-cloud-init-configuration-file"></a>Cloud-init konfigurációs fájl létrehozása

Egy Cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy ""Helló világ!"alkalmazás" Node.js alkalmazást Linux rendszerű virtuális gépen. 

A jelenlegi rendszerhéjában hozzon létre egy cloud-init.txt nevű fájlt. Másolja és illessze be a következő konfigurációt a rendszerhéjba. Győződjön meg arról, hogy helyesen másolja a teljes Cloud-init-fájlt, különösen az első sort:

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
### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)paranccsal:

#### <a name="vm1"></a>VM1
* Elnevezett **myVM1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM1**.
* A virtuális gép rendszerképének **UbuntuLTS**.
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* **1. zóna**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Elnevezett **myVM2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM2**.
* A virtuális gép rendszerképének **UbuntuLTS**.
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* **2. zóna**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Elnevezett **myVM3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM3**.
* A virtuális gép rendszerképének **UbuntuLTS**.
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* **3. zóna**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

Használja az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) to:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIP**néven.
* A **myresourcegrouplb erőforráscsoportban**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

Zóna nélküli redundáns nyilvános IP-cím létrehozása a 1. zónaban:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)paranccsal:

* Elnevezett **myLoadBalancer**.
* Egy **myFrontEnd**nevű frontend-készlet.
* Egy **myBackEndPool**nevű háttér-készlet.
* Az előző lépésben létrehozott nyilvános IP- **myPublicIP** társítva. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP**protokoll.
* A **80**-es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [az Network LB Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)paranccsal:

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton**keresztül. 
* A Health mintavételi **myHealthProbe**használata.
* **TCP**protokoll.
* Engedélyezze a kimenő forrás hálózati címfordítást (SNAT) a előtérbeli IP-cím használatával.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása a terheléselosztó háttérbeli készletéhez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)paranccsal:

#### <a name="vm1"></a>VM1
* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM1** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM2** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM3** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

## <a name="create-outbound-rule-configuration"></a>Kimenő szabály konfigurációjának létrehozása
A terheléselosztó kimenő szabályai a háttér-készletben lévő virtuális gépek kimenő SNAT konfigurálása. 

A kimenő kapcsolatokról a [Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md)című témakörben olvashat bővebben.

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Hozzon létre kimenő nyilvános IP-címet vagy nyilvános IP-előtagot.

Az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) paranccsal hozhat létre egyetlen IP-címet a kimenő kapcsolathoz.  

Az [az Network Public-IP előtag létrehozása](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) paranccsal hozzon létre egy nyilvános IP-előtagot a kimenő kapcsolathoz.

A kimenő NAT és a kimenő kapcsolatok skálázásával kapcsolatos további információkért lásd: [kimenő NAT méretezése több IP-címmel](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale).

#### <a name="public-ip"></a>Nyilvános IP-cím

* Elnevezett **myPublicIPOutbound**.
* A **myresourcegrouplb erőforráscsoportban**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

Zóna nélküli redundáns nyilvános IP-cím létrehozása a 1. zónaban:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Nyilvános IP-előtag

* Elnevezett **myPublicIPPrefixOutbound**.
* A **myresourcegrouplb erőforráscsoportban**.
* Az előtag hossza **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Az 1. zóna-ben a zóna nélküli nyilvános IP-előtag létrehozása:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Kimenő előtéri IP-konfiguráció létrehozása

Új előtérbeli IP-konfiguráció létrehozása az [az Network LB frontend-IP Create ](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)paranccsal:

Válassza ki a nyilvános IP-cím vagy a nyilvános IP-előtag parancsait az előző lépésben hozott döntés alapján.

#### <a name="public-ip"></a>Nyilvános IP-cím

* Elnevezett **myFrontEndOutbound**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Nyilvános IP- **myPublicIPOutbound**társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Nyilvános IP-előtag

* Elnevezett **myFrontEndOutbound**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A nyilvános IP-előtag **myPublicIPPrefixOutbound**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Kimenő készlet létrehozása

Hozzon létre egy új kimenő készletet az [az Network LB-címkészlet Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)paranccsal:

* Elnevezett **myBackEndPoolOutbound**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Kimenő szabály létrehozása

Hozzon létre egy új kimenő szabályt a kimenő háttérrendszer-készlethez az [az Network LB kimenő-Rule Create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create)paranccsal:

* Elnevezett **myOutboundRule**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A terheléselosztó **myLoadBalancer** társítva
* A frontend **myFrontEndOutbound**van társítva.
* **Minden**protokoll.
* A **15**üresjárati időkorlátja.
* **10000** kimenő portok.
* A háttér-készlet **myBackEndPoolOutbound**van társítva.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Virtuális gépek hozzáadása a kimenő készlethez

Adja hozzá a virtuális gépeket a kimenő készlethez az [az Network NIC IP-config cím-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)paranccsal:


#### <a name="vm1"></a>VM1
* A háttérbeli címkészlet **myBackEndPoolOutbound**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM1** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* A háttérbeli címkészlet **myBackEndPoolOutbound**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM2** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* A háttérbeli címkészlet **myBackEndPoolOutbound**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM3** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléselosztó tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt)paranccsal:

* Elnevezett **myVNet**.
* A **10.1.0.0/16**címnek az előtagja.
* **MyBackendSubnet**nevű alhálózat.
* A **10.1.0.0/24**alhálózati előtag.
* A **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz.
* A **eastus**helye.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A standard Load Balancer esetében a háttérbeli címen lévő virtuális gépeknek a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell rendelkezniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)paranccsal:

* Elnevezett **myNSG**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt [az az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)paranccsal:

* Elnevezett **myNSGRuleHTTP**.
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Protokoll **(*)**.
* Irány **bejövő**.
* Forrás **(*)**.
* Cél **(*)**.
* Célport **portszáma 80**.
* Hozzáférés **engedélyezése**.
* **200**prioritás.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert az [az Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)paranccsal:

#### <a name="vm1"></a>VM1

* Elnevezett **myNicVM1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Elnevezett **myNicVM2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Elnevezett **myNicVM3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban a következőket hozza létre:

* Egy **cloud-init.txt** nevű Felhőbeli konfigurációs fájl a kiszolgáló konfigurációjához. 
* Rendelkezésre állási csoport a virtuális gépekhez
* Három virtuális gép, amely háttér-kiszolgálóként használható a terheléselosztó számára.


A terheléselosztó sikeres létrehozásának ellenőrzéséhez telepítse az NGINX-et a virtuális gépekre.

### <a name="create-cloud-init-configuration-file"></a>Cloud-init konfigurációs fájl létrehozása

Egy Cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy ""Helló világ!"alkalmazás" Node.js alkalmazást Linux rendszerű virtuális gépen. 

A jelenlegi rendszerhéjában hozzon létre egy cloud-init.txt nevű fájlt. Másolja és illessze be a következő konfigurációt a rendszerhéjba. Győződjön meg arról, hogy helyesen másolja a teljes Cloud-init-fájlt, különösen az első sort:

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
### <a name="create-availability-set-for-virtual-machines"></a>Rendelkezésre állási csoport létrehozása a virtuális gépekhez

Hozza létre a rendelkezésre állási készletet az [az VM rendelkezésre állása-set Create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create)paranccsal:

* Elnevezett **myAvSet**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Hely **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)paranccsal:

#### <a name="vm1"></a>VM1
* Elnevezett **myVM1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM1**.
* A virtuális gép rendszerképének **UbuntuLTS**.
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* A rendelkezésre állási csoport **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait 
```
#### <a name="vm2"></a>VM2
* Elnevezett **myVM2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM2**.
* A virtuális gép rendszerképének **UbuntuLTS**.
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* **2. zóna**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Elnevezett **myVM3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM3**.
* A virtuális gép rendszerképének **UbuntuLTS**.
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* **3. zóna**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.


## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

Használja az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) to:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIP**néven.
* A **myresourcegrouplb erőforráscsoportban**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)paranccsal:

* Elnevezett **myLoadBalancer**.
* Egy **myFrontEnd**nevű frontend-készlet.
* Egy **myBackEndPool**nevű háttér-készlet.
* Az előző lépésben létrehozott nyilvános IP- **myPublicIP** társítva. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP**protokoll.
* A **80**-es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [az Network LB Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)paranccsal:

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton**keresztül. 
* A Health mintavételi **myHealthProbe**használata.
* **TCP**protokoll.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása a terheléselosztó háttérbeli készletéhez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)paranccsal:


#### <a name="vm1"></a>VM1
* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM1** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM2** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hálózati adapter **myNicVM3** és **ipconfig1**van társítva.
* A terheléselosztó **myLoadBalancer**van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```
---

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A terheléselosztó nyilvános IP-címének lekéréséhez használja az [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) parancsot. 

Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="A terheléselosztó tesztelése" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítsa el az erőforráscsoportot, a Load balancert és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban

* Létrehozta a standard vagy a nyilvános terheléselosztó
* Csatlakoztatott virtuális gépek. 
* Konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt.
* Tesztelte a terheléselosztó.

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a [mi Azure Load Balancer?](load-balancer-overview.md) és [Load Balancer a gyakori kérdések](load-balancer-faqs.md)című témakört.

További információ a [Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).
