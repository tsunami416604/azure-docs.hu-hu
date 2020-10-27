---
title: 'Rövid útmutató: belső terheléselosztó létrehozása – Azure CLI'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozható létre belső terheléselosztó az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 75e37c91b9b3161d7396d94fb086c4dc567a18c1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546993"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rövid útmutató: belső terheléselosztó létrehozása a virtuális gépek terheléselosztásához az Azure CLI használatával

Ismerkedjen meg Azure Load Balancer az Azure CLI használatával, és hozzon létre egy nyilvános Load balancert és három virtuális gépet.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure CLI vagy Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)paranccsal:

* **CreateIntLBQS-RG** névvel ellátott. 
* A **eastus** helyen.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)** -ban.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléselosztó üzembe helyezése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt)paranccsal:

* Elnevezett **myVNet** .
* A **10.1.0.0/16** címnek az előtagja.
* **MyBackendSubnet** nevű alhálózat.
* A **10.1.0.0/24** alhálózati előtag.
* A **CreateIntLBQS-RG** erőforráscsoporthoz.
* A **eastus** helye.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A standard Load Balancer esetében a háttérbeli címen lévő virtuális gépeknek a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell rendelkezniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)paranccsal:

* Elnevezett **myNSG** .
* Az erőforráscsoport **CreateIntLBQS – RG** .

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt [az az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)paranccsal:

* Elnevezett **myNSGRuleHTTP** .
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* Protokoll **(*)** .
* Irány **bejövő** .
* Forrás **(*)** .
* Cél **(*)** .
* Célport **portszáma 80** .
* Hozzáférés **engedélyezése** .
* **200** prioritás.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

Hozzon létre két hálózati adaptert az [az Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)paranccsal:

#### <a name="vm1"></a>VM1

* Elnevezett **myNicVM1** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A virtuális hálózat **myVNet** .
* Az alhálózat **myBackendSubnet** .
* A hálózati biztonsági csoport **myNSG** .

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Elnevezett **myNicVM2** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A virtuális hálózat **myVNet** .
* Az alhálózat **myBackendSubnet** .
* A hálózati biztonsági csoport **myNSG** .

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban a következőket hozza létre:

* Egy **cloud-init.txt** nevű Felhőbeli konfigurációs fájl a kiszolgáló konfigurációjához.
* Két virtuális gép, amely háttér-kiszolgálóként használható a terheléselosztó számára.

### <a name="create-cloud-init-configuration-file"></a>Cloud-init konfigurációs fájl létrehozása

Egy Cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy "„Helló világ!” alkalmazás" Node.js alkalmazást Linux rendszerű virtuális gépen. 

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
* Elnevezett **myVM1** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* Csatolva a hálózati adapter **myNicVM1** .
* A virtuális gép rendszerképének **UbuntuLTS** .
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* **1. zóna** .

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Elnevezett **myVM2** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* Csatolva a hálózati adapter **myNicVM2** .
* A virtuális gép rendszerképének **UbuntuLTS** .
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* **2. zóna** .

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)paranccsal:

* Elnevezett **myLoadBalancer** .
* Egy **myFrontEnd** nevű frontend-készlet.
* Egy **myBackEndPool** nevű háttér-készlet.
* A virtuális hálózat **myVNet** társítva.
* A háttérbeli alhálózat **myBackendSubnet** van társítva.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe** .
* **TCP** protokoll.
* A **80** -es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
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
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd** .
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton** keresztül. 
* A Health mintavételi **myHealthProbe** használata.
* **TCP** protokoll.
* A **15 perc** üresjárati időkorlátja.
* Engedélyezze a TCP-visszaállítást.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>A háttér-készletben lévő virtuális gépek nem rendelkeznek kimenő internetkapcsolattal ezzel a konfigurációval. </br> A kimenő kapcsolatok nyújtásával kapcsolatos további információkért lásd: </br> **[Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md)**</br> Kapcsolatok biztosításának lehetőségei: </br> **[Csak kifelé irányuló terheléselosztó konfigurációja](egress-only.md)** </br> **[Mi az Virtual Network NAT?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása a terheléselosztó háttérbeli készletéhez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)paranccsal:


#### <a name="vm1"></a>VM1
* A háttérbeli címkészlet **myBackEndPool** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A hálózati adapter **myNicVM1** és **ipconfig1** van társítva.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* A háttérbeli címkészlet **myBackEndPool** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A hálózati adapter **myNicVM2** és **ipconfig1** van társítva.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)** -ban.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléselosztó üzembe helyezése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt)paranccsal:

* Elnevezett **myVNet** .
* A **10.1.0.0/16** címnek az előtagja.
* **MyBackendSubnet** nevű alhálózat.
* A **10.1.0.0/24** alhálózati előtag.
* A **CreateIntLBQS-RG** erőforráscsoporthoz.
* A **eastus** helye.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A standard Load Balancer esetében a háttérbeli címen lévő virtuális gépeknek a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell rendelkezniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)paranccsal:

* Elnevezett **myNSG** .
* Az erőforráscsoport **CreateIntLBQS – RG** .

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt [az az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)paranccsal:

* Elnevezett **myNSGRuleHTTP** .
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* Protokoll **(*)** .
* Irány **bejövő** .
* Forrás **(*)** .
* Cél **(*)** .
* Célport **portszáma 80** .
* Hozzáférés **engedélyezése** .
* **200** prioritás.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

Hozzon létre két hálózati adaptert az [az Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)paranccsal:

#### <a name="vm1"></a>VM1

* Elnevezett **myNicVM1** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A virtuális hálózat **myVNet** .
* Az alhálózat **myBackendSubnet** .
* A hálózati biztonsági csoport **myNSG** .

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Elnevezett **myNicVM2** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A virtuális hálózat **myVNet** .
* Az alhálózat **myBackendSubnet** .

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban a következőket hozza létre:

* Egy **cloud-init.txt** nevű Felhőbeli konfigurációs fájl a kiszolgáló konfigurációjához. 
* Rendelkezésre állási csoport a virtuális gépekhez
* Két virtuális gép, amely háttér-kiszolgálóként használható a terheléselosztó számára.

A terheléselosztó sikeres létrehozásának ellenőrzéséhez telepítse az NGINX-et a virtuális gépekre.

### <a name="create-cloud-init-configuration-file"></a>Cloud-init konfigurációs fájl létrehozása

Egy Cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy "„Helló világ!” alkalmazás" Node.js alkalmazást Linux rendszerű virtuális gépen. 

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

* Elnevezett **myAvSet** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* Hely **eastus** .

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)paranccsal:

#### <a name="vm1"></a>VM1
* Elnevezett **myVM1** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* Csatolva a hálózati adapter **myNicVM1** .
* A virtuális gép rendszerképének **UbuntuLTS** .
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* A rendelkezésre állási csoport **myAvSet** .

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Elnevezett **myVM2** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* Csatolva a hálózati adapter **myNicVM2** .
* A virtuális gép rendszerképének **UbuntuLTS** .
* A fenti lépésben létrehozott konfigurációs fájl **cloud-init.txt** .
* **2. zóna** .

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)paranccsal:

* Elnevezett **myLoadBalancer** .
* Egy **myFrontEnd** nevű frontend-készlet.
* Egy **myBackEndPool** nevű háttér-készlet.
* A virtuális hálózat **myVNet** társítva.
* A háttérbeli alhálózat **myBackendSubnet** van társítva.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe** .
* **TCP** protokoll.
* A **80** -es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
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
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd** .
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton** keresztül. 
* A Health mintavételi **myHealthProbe** használata.
* **TCP** protokoll.
* A **15 perc** üresjárati időkorlátja.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása a terheléselosztó háttérbeli készletéhez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)paranccsal:


#### <a name="vm1"></a>VM1
* A háttérbeli címkészlet **myBackEndPool** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A hálózati adapter **myNicVM1** és **ipconfig1** van társítva.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* A háttérbeli címkészlet **myBackEndPool** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A hálózati adapter **myNicVM2** és **ipconfig1** van társítva.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

### <a name="create-azure-bastion-public-ip"></a>Azure-alapú megerősített nyilvános IP-cím létrehozása

Az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myBastionIP** néven.
* **CreateIntLBQS – RG** .

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Azure Bastion-alhálózat létrehozása

Alhálózat létrehozása [az az Network vnet subnet Create](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) paranccsal:

* Elnevezett **AzureBastionSubnet** .
* A **10.1.1.0/24** címek előtagja.
* A virtuális hálózat **myVNet** .
* Az erőforráscsoport **CreateIntLBQS – RG** .

```azurecli-interactive
  az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Azure Bastion-gazdagép létrehozása
Az [az Network Bastion Create](https://docs.microsoft.com/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) paranccsal hozzon létre egy megerősített gazdagépet:

* Elnevezett **myBastionHost**
* **CreateIntLBQS – RG**
* Nyilvános IP- **myBastionIP** társítva.
* Virtuális hálózati **myVNet** társítva.
* A **eastus** helyen.

```azurecli-interactive
  az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
A megerősített gazdagép üzembe helyezése néhány percet is igénybe vehet.

### <a name="create-test-virtual-machine"></a>Teszt virtuális gép létrehozása

Hozza létre a hálózati adaptert az [az Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)paranccsal:

* Elnevezett **myNicTestVM** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* A virtuális hálózat **myVNet** .
* Az alhálózat **myBackendSubnet** .
* A hálózati biztonsági csoport **myNSG** .

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Hozza létre a virtuális gépet az [az VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)paranccsal:

* Elnevezett **myTestVM** .
* Az erőforráscsoport **CreateIntLBQS – RG** .
* Csatolva a hálózati adapter **myNicTestVM** .
* A virtuális gép rendszerképének **Win2019Datacenter** .
* Válassza a és a értékeket **\<adminpass>** **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
A virtuális gép üzembe helyezése néhány percet is igénybe vehet.

### <a name="test"></a>Tesztelés

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A terheléselosztó magánhálózati IP-címének megkeresése az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd válassza a **myLoadBalancer** lehetőséget.

2. Jegyezze fel, vagy másolja a **magánhálózati IP-cím** melletti címet a **myLoadBalancer** **áttekintésében** .

3. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myTestVM** , amely a **CreateIntLBQS-RG** erőforráscsoporthoz található.

4. Az **Áttekintés** lapon válassza a **kapcsolat** , majd a **Bastion** lehetőséget.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. Nyissa meg az **Internet Explorert** a **myTestVM** .

8. Az előző lépésben adja meg az IP-címet a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Standard belső terheléselosztó létrehozása" border="true":::
   
Ha meg szeretné tekinteni, hogy a terheléselosztó mindhárom virtuális gépen osztja el a forgalmat, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett oldalát, majd kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások felszabadítása

Ha már nincs rá szükség, az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal távolítsa el az erőforráscsoportot, a Load balancert és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban

* Létrehozta a standard vagy a nyilvános terheléselosztó
* Csatlakoztatott virtuális gépek. 
* Konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt.
* Tesztelte a terheléselosztó.

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a következővel 
> [!div class="nextstepaction"]
> [Mi az Azure Load Balancer?](load-balancer-overview.md)