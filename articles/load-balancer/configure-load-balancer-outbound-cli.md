---
title: Load terheléselosztás és a kimenő szabályok konfigurálása az Azure CLI-vel Standard Load Balancer |} A Microsoft Docs
description: Ez a cikk bemutatja a Standard Load Balancer konfigurálása a load terheléselosztás és a kimenő szabályok az Azure CLI használatával.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: 0759b6a8e3deb9bc1d04e41598e4eef9304ecd83
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416409"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Terheléselosztás és a kimenő szabályok konfigurálása az Azure CLI-vel Standard Load Balancer

Ez a rövid útmutató bemutatja, hogyan kimenő szabályok konfigurálása a Standard Load Balancer az Azure CLI használatával.  

Amikor elkészült, a terheléselosztó erőforrás tartalmaz két előterek és szabály tartozik: egy a bejövő és a egy másik kimenő.  Minden egyes előtérbeli nyilvános IP-cím és a egy másik nyilvános IP-cím a bejövő és kimenő forgatókönyv használ vonatkozó hivatkozás van.   A terheléselosztási szabály csak a bejövő terheléselosztást biztosít, és a kimenő szabály szabályozza a kimenő NAT a virtuális Géphez megadott.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy erőforráscsoportot, nevű *myresourcegroupoutbound* a a *eastus2* helye:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot nevű *myvnetoutbound* nevű alhálózattal *mysubnetoutbound* a a *myresourcegroupoutbound* használatával [az network vnet Hozzon létre](https://docs.microsoft.com/cli/azure/network/vnet#create).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Bejövő nyilvános IP-cím létrehozása 

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Használat [az network public-ip létrehozása](https://docs.microsoft.com/cli/azure/network/public-ip#create) hozhat létre egy Standard nyilvános IP-címet *mypublicipinbound* a *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása 

Hozzon létre egy szabványos IP-címet a terheléselosztó előtérbeli kimenő konfiguráció használatával [az network public-ip létrehozása](https://docs.microsoft.com/cli/azure/network/public-ip#create).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```


## <a name="create-azure-load-balancer"></a>Az Azure Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - Egy előtérbeli IP-Címmel a terheléselosztón a bejövő hálózati forgalmat fogad.
  - A háttérkészlet, ahol az előtérbeli IP-címet küldi az elosztott terhelésű hálózati forgalmat.
  - az állapotfigyelő mintavételező, amely a háttérbeli Virtuálisgép-példányok állapotát határozza meg.
  - Egy terheléselosztó bejövő szabályt, amely meghatározza, hogyan ossza el a virtuális gépek forgalmat.
  - Egy terheléselosztó kimenő-szabályt, amely meghatározza, hogyan ossza a virtuális gépekről érkező forgalmat.

### <a name="create-load-balancer"></a>Load Balancer létrehozása

A bejövő IP cím használatával hozzon létre egy terheléselosztó [az network lb létrehozása](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) nevű *lb* , amely tartalmaz egy bejövő előtérbeli IP-konfigurációt és a egy háttérkészlet társított nyilvános IP-cím *mypublicipinbound* az előző lépésben létrehozott.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepool \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-frontend-ip"></a>Kimenő előtérbeli IP-cím létrehozása
A terheléselosztó kimenő előtérbeli IP-konfiguráció létrehozása [az network lb frontend-ip létrehozása](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) , amely tartalmazza, és a kimenő előtérbeli IP-konfiguráció neve *myfrontendoutbound* , amely nyilvános IP-címhez társított *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Állapotminta létrehozása

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintát az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) paranccsal a virtuális gépek állapotának monitorozásához. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Terheléselosztási szabály létrehozása

Egy terheléselosztó-szabályt az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérkészlet, a forgalom fogadásához, valamint a szükséges forrás és cél határozza meg. Hozzon létre egy terheléselosztó-szabályt *myinboundlbrule* a [az network lb-szabály létrehozása](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) előtérbeli címkészlet 80-as portját *myfrontendinbound* és küldése elosztott terhelésű hálózati forgalmat a háttércímkészlethez *bepool* szintén a 80-as port használatával. 

>[!NOTE]
>A terheléselosztási szabály automatikus kimenő (S) NAT miatt ez a szabály letiltja a--letiltása kimenő snat paraméterrel. Kimenő NAT csak a kimenő szabály által biztosított.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepool \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Kimenő szabály létrehozása

Kimenő szabály meghatározása a előtérbeli nyilvános IP-cím, az előtérbeli által képviselt *myfrontendoutbound*, amely jelzi a kimenő NAT-forgalom, valamint a háttérkészlethez, amelyhez ez a szabály vonatkozik.  Kimenő szabály létrehozása *myoutboundrule* az összes virtuális gép (NIC IP-konfigurációk) a kimenő hálózati címfordítás *bepool* háttérkészlet.  Az alábbi parancsot is változik a kimenő üresjárat időkorlátja 4 15 perc és lefoglalja a 10000 SNAT helyett 1024 portokat.  Felülvizsgálat [kimenő szabályok](https://aka.ms/lboutboundrules) további részletekért.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepool
```

Ezen a ponton, és folytassa a virtuális gép hozzáadása a háttérkészlethez *bepool* álló megfelelő hálózati adapter IP-konfiguráció frissítésével.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott Standard Load Balancer, mind a bejövő terheléselosztási forgalmi szabályokat, a konfigurált, és a állapotadat-mintavétel konfigurálva a virtuális gépek, a háttérkészletben. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

