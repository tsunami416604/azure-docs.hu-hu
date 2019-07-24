---
title: Terheléselosztás és kimenő szabályok konfigurálása az Azure CLI használatával
titlesuffix: Azure Load Balancer
description: Ez a cikk bemutatja a Standard Load Balancer konfigurálása a load terheléselosztás és a kimenő szabályok az Azure CLI használatával.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 837df78ea76451c7dc5e16efde0e90b780b6ee50
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275712"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Terheléselosztás és a kimenő szabályok konfigurálása az Azure CLI-vel Standard Load Balancer

Ez a rövid útmutató bemutatja, hogyan kimenő szabályok konfigurálása a Standard Load Balancer az Azure CLI használatával.  

Amikor elkészült, a terheléselosztó erőforrás tartalmaz két előterek és szabály tartozik: egy a bejövő és a egy másik kimenő.  Minden egyes előtérbeli nyilvános IP-cím és a egy másik nyilvános IP-cím a bejövő és kimenő forgatókönyv használ vonatkozó hivatkozás van.   A terheléselosztási szabály csak a bejövő terheléselosztást biztosít, és a kimenő szabály szabályozza a kimenő NAT a virtuális Géphez megadott.  Ez a rövid útmutató két különálló háttér-készletet használ, amelyek közül az egyik a bejövő és az egyik a kimenő, a képesség szemléltetése és a rugalmasság biztosítása ehhez a forgatókönyvhöz.

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
  - Egy háttérbeli készlet, amelyben a előtéri IP-cím elküldi a terheléselosztási hálózati forgalmat.
  - Egy háttérbeli készlet a kimenő kapcsolathoz. 
  - az állapotfigyelő mintavételező, amely a háttérbeli Virtuálisgép-példányok állapotát határozza meg.
  - Egy terheléselosztó bejövő szabályt, amely meghatározza, hogyan ossza el a virtuális gépek forgalmat.
  - Egy terheléselosztó kimenő-szabályt, amely meghatározza, hogyan ossza a virtuális gépekről érkező forgalmat.

### <a name="create-load-balancer"></a>Load Balancer létrehozása

Hozzon létre egy Load Balancer a bejövő IP-címmel az [az Network LB Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) *LB* néven, amely tartalmaz egy bejövő előtérbeli IP-konfigurációt és egy, a nyilvános IP-címhez  *társított bepoolinbound-készletet.* az előző lépésben létrehozott mypublicipinbound.

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

Hozzon létre egy további háttér-címkészletet a virtuális gépek készletének kimenő kapcsolatának definiálásához, az az [Network LB-címkészlet Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) nevű *bepooloutbound*.  Egy különálló kimenő készlet létrehozása maximális rugalmasságot biztosít, de kihagyhatja ezt a lépést, és csak a bejövő *bepoolinbound* is használhatja.

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

Ha nem szeretne külön kimenő készletet használni, a *bepoolinbound* megadásához módosítsa a címkészlet argumentumot az előző parancsban.  Javasoljuk, hogy használjon külön készleteket az eredményül kapott konfiguráció rugalmasságának és olvashatóságának érdekében.

Ezen a ponton folytathatja a virtuális gép hozzáadását a háttér-készlet *bepoolinbound* __és__ *bepooloutbound* , ha frissíti a megfelelő NIC-erőforrások IP-konfigurációját az [az Network NIC IP-config cím-Pool Add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)paranccsal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott Standard Load Balancer, mind a bejövő terheléselosztási forgalmi szabályokat, a konfigurált, és a állapotadat-mintavétel konfigurálva a virtuális gépek, a háttérkészletben. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
