---
title: Terheléselosztási és az Azure CLI-vel kimenő szabályok konfigurálása
titlesuffix: Azure Load Balancer
description: Ez a cikk bemutatja a Standard Load Balancer konfigurálása a load terheléselosztás és a kimenő szabályok az Azure CLI használatával.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: kumud
ms.openlocfilehash: f28088a1a0586964092a0b5f86ce8bf0f95402cd
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281947"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Terheléselosztás és a kimenő szabályok konfigurálása az Azure CLI-vel Standard Load Balancer

Ez a rövid útmutató bemutatja, hogyan kimenő szabályok konfigurálása a Standard Load Balancer az Azure CLI használatával.  

Amikor elkészült, a terheléselosztó erőforrás tartalmaz két előterek és szabály tartozik: egy a bejövő és a egy másik kimenő.  Minden egyes előtérbeli nyilvános IP-cím és a egy másik nyilvános IP-cím a bejövő és kimenő forgatókönyv használ vonatkozó hivatkozás van.   A terheléselosztási szabály csak a bejövő terheléselosztást biztosít, és a kimenő szabály szabályozza a kimenő NAT a virtuális Géphez megadott.  Ez a rövid útmutató használ két külön háttérkészletek, egy a bejövő és a egy kimenő, képesség bemutatják és rugalmasság ehhez a forgatókönyvhöz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy erőforráscsoportot, nevű *myresourcegroupoutbound* a a *eastus2* helye:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot nevű *myvnetoutbound* nevű alhálózattal *mysubnetoutbound* a a *myresourcegroupoutbound* használatával [az network vnet Hozzon létre](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Bejövő nyilvános IP-cím létrehozása 

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Használat [az network public-ip létrehozása](https://docs.microsoft.com/cli/azure/network/public-ip) hozhat létre egy Standard nyilvános IP-címet *mypublicipinbound* a *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása 

Hozzon létre egy szabványos IP-címet a terheléselosztó előtérbeli kimenő konfiguráció használatával [az network public-ip létrehozása](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Az Azure Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - Egy előtérbeli IP-Címmel a terheléselosztón a bejövő hálózati forgalmat fogad.
  - A háttérkészlet, ahol az előtérbeli IP-címet küldi az elosztott terhelésű hálózati forgalmat.
  - A háttérkészlet, a kimenő hálózati kapcsolatot. 
  - az állapotfigyelő mintavételező, amely a háttérbeli Virtuálisgép-példányok állapotát határozza meg.
  - Egy terheléselosztó bejövő szabályt, amely meghatározza, hogyan ossza el a virtuális gépek forgalmat.
  - Egy terheléselosztó kimenő-szabályt, amely meghatározza, hogyan ossza a virtuális gépekről érkező forgalmat.

### <a name="create-load-balancer"></a>Load Balancer létrehozása

A bejövő IP cím használatával hozzon létre egy terheléselosztó [az network lb létrehozása](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) nevű *lb* , amely tartalmaz egy bejövő előtérbeli IP-konfigurációt és a egy háttérkészlet *bepoolinbound*társított nyilvános IP-cím *mypublicipinbound* az előző lépésben létrehozott.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Kimenő készlet létrehozása

Hozzon létre egy további háttércímkészlet kimenő kapcsolattal rendelkező virtuális gépek készletét meghatározásához [az network lb-címkészlet létrehozása](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) nevű *bepooloutbound*.  Készletet hoz létre egy külön kimenő maximális rugalmasságot biztosít, de ezt a lépést kihagyhatja, és csak akkor használja a bejövő *bepoolinbound* is.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Kimenő előtérbeli IP-cím létrehozása
A terheléselosztó kimenő előtérbeli IP-konfiguráció létrehozása [az network lb frontend-ip létrehozása](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) , amely tartalmazza, és a kimenő előtérbeli IP-konfiguráció neve *myfrontendoutbound* , amely nyilvános IP-címhez társított *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Állapotminta létrehozása

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintát az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) paranccsal a virtuális gépek állapotának monitorozásához. 

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

Egy terheléselosztó-szabályt az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérkészlet, a forgalom fogadásához, valamint a szükséges forrás és cél határozza meg. Hozzon létre egy terheléselosztó-szabályt *myinboundlbrule* a [az network lb-szabály létrehozása](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) előtérbeli címkészlet 80-as portját *myfrontendinbound* és küldése elosztott terhelésű hálózati forgalmat a háttércímkészlethez *bepool* szintén a 80-as port használatával. 

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
--backend-pool-name bepoolinbound \
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
 --address-pool bepooloutbound
```

Ha nem szeretné használni egy másik kimenő készlet, módosíthatja a cím készlet argumentum, adja meg az előző parancsban szereplő *bepoolinbound* helyette.  Azt javasoljuk, hogy használjon különálló készleteket rugalmasságot és az így létrejövő konfiguráció az olvashatóság érdekében.

Ezen a ponton, és folytassa a virtuális gép hozzáadása a háttérkészlethez *bepoolinbound* __és__ *bepooloutbound* szerint a megfelelő hálózati adapter IP-konfigurációjának frissítése erőforrások [az network nic ip-config-címkészlet hozzáadása](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott Standard Load Balancer, mind a bejövő terheléselosztási forgalmi szabályokat, a konfigurált, és a állapotadat-mintavétel konfigurálva a virtuális gépek, a háttérkészletben. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Az Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
