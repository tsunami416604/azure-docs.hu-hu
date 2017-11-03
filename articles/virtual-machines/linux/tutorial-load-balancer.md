---
title: "Betöltése kiegyenlítheti a Linux virtuális gépek Azure-ban |} Microsoft Docs"
description: "Az Azure load balancer használata magas rendelkezésre állású és biztonságos-alkalmazás létrehozásának három Linux virtuális gépek között"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 25e2538e220327a078a6527e667dfcd6cb838b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Betöltése kiegyenlítheti a Linux virtuális gépek magas rendelkezésre állású alkalmazás létrehozása az Azure-ban
Terheléselosztás biztosít a rendelkezésre állási magasabb szintű bejövő kérelmek elosztásával el több virtuális gépre. Ebben az oktatóanyagban elsajátíthatja az Azure load balancer különböző összetevőit, ossza el a forgalmat, és magas rendelkezésre állás biztosításához. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy Azure terheléselosztó
> * A health terheléselosztói mintavétel létrehozása
> * Terheléselosztó forgalomra vonatkozó szabályok létrehozása
> * Egy alapszintű Node.js-alkalmazás létrehozásához használja a felhő inicializálás
> * Virtuális gépek létrehozása és csatolása a terheléselosztó
> * A művelet egy terhelés-kiegyenlítő megtekintése
> * Adja hozzá, és távolítsa el a virtuális gépek a terheléselosztó


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="azure-load-balancer-overview"></a>Az Azure load balancer áttekintése
Egy Azure terheléselosztó a réteg-4 (TCP, UDP) terheléselosztóhoz, amely a magas rendelkezésre állást biztosít azáltal, hogy a bejövő forgalom kifogástalan állapotú virtuális gépek között. Terheléselosztói állapotfigyelő mintavétel az egyes virtuális gépek egy adott portot figyeli, és csak osztja el a forgalmat egy operatív virtuális gépre.

Egy előtér-IP-konfigurációja, amely tartalmaz egy vagy több nyilvános IP-címeket adhat meg. Az előtér-IP-konfiguráció lehetővé teszi, hogy a terheléselosztó és az alkalmazások elérhetők lesznek az interneten keresztül. 

Virtuális gépek csatlakozni a virtuális hálózati kártya (NIC) használatával egy terheléselosztó. A virtuális gépek felé irányuló forgalom terjesztéséhez, egy háttér címkészletet tartalmaz az IP-cím, a virtuális (NIC) címét csatlakozik a terheléselosztóhoz.

A forgalmat szabályozásához adhat meg terhelés terheléselosztó szabályt adott portok és protokollok, amelyek a virtuális gépekre van leképezve.

Ha követte az előző oktatóanyag [hozzon létre egy virtuálisgép-méretezési csoport](tutorial-create-vmss.md), a terheléselosztó lett létrehozva. Ezek az összetevők lettek konfigurálva, a méretezési csoport részeként.


## <a name="create-azure-load-balancer"></a>Az Azure terheléselosztó létrehozása
Ez a szakasz részletesen, hogyan hozhat létre, és minden összetevője a terheléselosztó konfigurálásához. A terheléselosztó létrehozása előtt hozzon létre egy erőforráscsoportot, a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupLoadBalancer* a a *eastus* helye:

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Az alkalmazás az Internet eléréséhez a terheléselosztó szükség van egy nyilvános IP-cím. Hozzon létre egy nyilvános IP-cím [létrehozása az hálózati nyilvános ip-](/cli/azure/network/public-ip#create). Az alábbi példa létrehoz egy nyilvános IP-cím nevű *myPublicIP* a a *myResourceGroupLoadBalancer* erőforráscsoport:

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása
Hozzon létre egy terheléselosztót, [az hálózati terheléselosztó létrehozása](/cli/azure/network/lb#create). Az alábbi példa létrehoz egy terhelés-kiegyenlítő nevű *myLoadBalancer* és hozzárendeli a *myPublicIP* címét, hogy az előtér-IP-konfiguráció:

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Hozzon létre egy állapotmintáihoz
Ahhoz, hogy a terheléselosztó a figyelheti az alkalmazás állapotát, használja a állapotmintáihoz. A állapotmintáihoz dinamikusan eltávolítása vagy virtuális gépek állapotát ellenőrzi a válasz alapján terhelés terheléselosztó elforgatási. Alapértelmezés szerint a virtuális gép törlődik a terheléselosztó terheléselosztási 15 másodperces időközönként két egymást követő hibák után. Létrehozhat egy állapotmintáihoz protokoll vagy egy meghatározott állapottal ellenőrzése lapon, az alkalmazás alapján. 

Az alábbi példa létrehoz egy TCP-Hálózatfigyelővel. Egyéni HTTP mintavételt további részletes állapotának ellenőrzésére is létrehozhat. Ha egy egyéni HTTP-vizsgálatot, létre kell hoznia az állapot ellenőrzése lapon, például a *healthcheck.js*. A mintavétel kell visszaadnia egy **HTTP 200 OK** válasz megtartja a gazdagép Elforgatás a terheléselosztóhoz.

A TCP állapotmintáihoz létrehozásához használhatja [az hálózati lb mintavétel létrehozása](/cli/azure/network/lb/probe#create). Az alábbi példa létrehoz egy nevű állapotmintáihoz *myHealthProbe*:

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Hozzon létre olyan terheléselosztó szabályhoz
Olyan terheléselosztó szabályhoz hogyan adatforgalom elosztása a virtuális gépek azonosítására szolgál. Megadhatja az előtér-IP-konfigurációjának megadása a bejövő forgalom és a háttér IP-címkészlet, valamint a megfelelő forrás és cél portot forgalom fogadására. Győződjön meg arról, hogy csak a megfelelő virtuális gépek forgalom fogadására, hogy is megadhatja a használandó állapotmintáihoz.

Hozzon létre olyan terheléselosztó szabályhoz rendelkező [az hálózati terheléselosztó szabály létrehozása](/cli/azure/network/lb/rule#create). Az alábbi példa létrehoz egy nevű szabályt *myLoadBalancerRule*, használja a *myHealthProbe* állapotmintáihoz és porton kiegyensúlyozza forgalom *80*:

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
Mielőtt központilag az egyes virtuális gépek, és tesztelheti a terheléselosztó, hozzon létre a támogató virtuális hálózati erőforrásokat. Virtuális hálózatok kapcsolatos további információkért tekintse meg a [Azure virtuális hálózatok kezelése](tutorial-virtual-network.md) oktatóanyag.

### <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
A virtuális hálózat létrehozása [az hálózati vnet létrehozása](/cli/azure/network/vnet#create). Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVnet* nevű alhálózattal *mySubnet*:

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Hálózati biztonsági csoport hozzáadása, használhat [az hálózati nsg létrehozása](/cli/azure/network/nsg#create). Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNetworkSecurityGroup*:

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

A hálózati biztonsági csoport szabály létrehozása [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#create). Az alábbi példa létrehoz egy hálózati biztonsági szabály nevű *myNetworkSecurityGroupRule*:

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

Virtuális hálózati adapter jönnek létre [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#create). Az alábbi példakód létrehozza a három virtuális hálózati adapter. (Az egyes virtuális gépek virtuális hálózati adapter egy hoz létre az alkalmazáshoz az alábbi lépéseket a). További virtuális hálózati adapterek és virtuális gépek létrehozása tetszőleges időpontban, és adja hozzá a terheléselosztó:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

### <a name="create-cloud-init-config"></a>Felhő inicializálás konfiguráció létrehozása
Az oktatóanyag előző [első indításakor Linux virtuális gépek testreszabása](tutorial-automate-vm-deployment.md), megtudta, hogyan automatizálható a felhő inicializálás a virtuális gép testreszabása. Az azonos felhő inicializálás konfigurációs fájl segítségével NGINX telepítheti és futtathatja egy egyszerű "Hello, World" Node.js alkalmazást.

Hozzon létre egy fájlt az aktuális rendszerhéjban *felhő-init.txt* , majd illessze be a következő konfigurációt. A felhő rendszerhéj nem a helyi számítógépen hozzon létre például a fájlt. Adja meg `sensible-editor cloud-init.txt` hozza létre a fájlt, és elérhető szerkesztők listájának megtekintéséhez. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor:

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
Az alkalmazás a magas rendelkezésre állás javítása érdekében helyezze a virtuális gépek rendelkezésre állási csoportba. További információ a rendelkezésre állási csoportok: a korábbi [magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md) oktatóanyag.

Állítsa be a rendelkezésre állási létrehozása [az virtuális gép rendelkezésre állási-csoport létrehozása](/cli/azure/vm/availability-set#create). Az alábbi példakód létrehozza a rendelkezésre állási készlet elnevezett *myAvailabilitySet*:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Most a virtuális gépek is létrehozhat [az virtuális gép létrehozása](/cli/azure/vm#create). Az alábbi példában három virtuális gépeket hoz létre, és SSH-kulcsokat generál, ha még nem léteznek:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Nincsenek háttérfeladatok, hogy végezze el az Azure parancssori felület visszatér a parancssorba. A `--no-wait` paraméter nem Várjon, amíg befejeződik a feladatokat. Elképzelhető, hogy egy másik néhány percet, mielőtt hozzáférhetne az alkalmazáshoz. A load balancer állapotmintáihoz automatikusan észleli, ha az alkalmazás futtatása az egyes virtuális gépek. A webalkalmazás működik, ha a terheléselosztó szabályhoz forgalom terjeszteni elindul.


## <a name="test-load-balancer"></a>Teszt terheléselosztó
Szerezze be a terheléselosztó a nyilvános IP-címe [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#show). Az alábbi példa beolvassa az IP-címek *myPublicIP* korábban létrehozott:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Beírhatja a nyilvános IP-címet a webböngésző. Ne feledje - néhány percet vesz igénybe a készen álljon a terheléselosztó terjesztéséhez azokat felé irányuló forgalom megkezdése előtt a virtuális gépeket. Az alkalmazás megjelenik, beleértve az állomásnevet, a virtuális gép, amelyek a terheléselosztó felé irányuló forgalom az alábbi példában látható módon:

![Futó Node.js-alkalmazás](./media/tutorial-load-balancer/running-nodejs-app.png)

Tekintse meg a terheléselosztó forgalom szét az alkalmazást futtató összes három virtuális gépet, akkor is kényszerített frissítési a webböngésző.


## <a name="add-and-remove-vms"></a>Hozzá és távolíthat el a virtuális gépek
Szükség lehet a az alkalmazás, például az operációs rendszer frissítéseinek telepítése futó virtuális gépeket karbantartásához. Az alkalmazás megnövekedett forgalom kezelésére, szükség lehet további virtuális gépek hozzáadása. Ez a szakasz bemutatja, hogyan távolítsa el, vagy adja hozzá a virtuális gépek a terheléselosztóról.

### <a name="remove-a-vm-from-the-load-balancer"></a>A virtuális gép eltávolítása a terheléselosztó
A virtuális gép eltávolítása a háttér-címkészlet, amely [az hálózati hálózati adapter ip-config-címkészlet eltávolítása](/cli/azure/network/nic/ip-config/address-pool#remove). A következő példában eltávolítjuk a virtuális hálózati Adapternek **myVM2** a *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Tekintse meg a terheléselosztó elosztják a forgalom a fennmaradó két futó virtuális gépeket az alkalmazás akkor is kényszerített frissítési a webböngésző. Karbantartási is képes lemezvizsgálatok elvégzésére, hogy a virtuális Gépen, például az operációs rendszer frissítéseinek telepítése vagy a virtuális gép újraindítása végrehajtása.

### <a name="add-a-vm-to-the-load-balancer"></a>A virtuális gépek hozzáadása a terheléselosztó
Virtuális gép karbantartásának végrehajtása után, vagy ha szüksége kapacitás bővítése céljából, adhat hozzá egy virtuális Gépet a háttér-címkészlet, amely [az hálózati hálózati adapter ip-config-címkészlet hozzáadása](/cli/azure/network/nic/ip-config/address-pool#add). A következő példakóddal felveheti a virtuális hálózati Adapternek **myVM2** való *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy terhelés-kiegyenlítő létrehozott és a virtuális gépek csatolva. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy Azure terheléselosztó
> * A health terheléselosztói mintavétel létrehozása
> * Terheléselosztó forgalomra vonatkozó szabályok létrehozása
> * Egy alapszintű Node.js-alkalmazás létrehozásához használja a felhő inicializálás
> * Virtuális gépek létrehozása és csatolása a terheléselosztó
> * A művelet egy terhelés-kiegyenlítő megtekintése
> * Adja hozzá, és távolítsa el a virtuális gépek a terheléselosztó

További információt az Azure virtuális hálózati összetevők a következő oktatóanyag továbblépés.

> [!div class="nextstepaction"]
> [Virtuális gépek és virtuális hálózatok kezelése](tutorial-virtual-network.md)
