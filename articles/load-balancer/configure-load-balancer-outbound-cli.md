---
title: Terheléselosztás és kimenő szabályok konfigurálása az Azure CLI használatával
titleSuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogyan konfigurálhatja a terheléselosztás és a kimenő szabályok egy standard terheléselosztó az Azure CLI használatával.
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
ms.openlocfilehash: 7230b0c2b80137b068bbeacf43ab2133491a69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225470"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>A standard Load Balancer terheléselosztási és kimenő szabályainak konfigurálása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a kimenő szabályokat a standard terheléselosztóban az Azure CLI használatával.  

Ha elkészült, a terheléselosztó erőforrás két előtétet és a hozzájuk társított szabályokat tartalmaz: az egyik a bejövő, a másik pedig a kimenő.  Minden előtér egy nyilvános IP-címre mutató hivatkozást tartalmaz, és ez a forgatókönyv egy másik nyilvános IP-címet használ a bejövő és a kimenő.   A terheléselosztási szabály csak bejövő terheléselosztást biztosít, és a kimenő szabály szabályozza a virtuális gép számára biztosított kimenő nat-ot.  Ez a rövid útmutató két különálló háttérkészletet használ, egyet a bejövő, egyet a kimenőhöz, hogy bemutassa a képességeket, és rugalmasságot biztosítson ebben a forgatókönyvben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy *myresourcegroupoutbound* nevű erőforráscsoportot az *eastus2* helyen:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy *myvnetoutbound* nevű virtuális hálózatot egy *mysubnetoutbound* nevű alhálózattal a *myresourcegroupoutbound* az [az hálózati virtuális hálózat létrehozása](https://docs.microsoft.com/cli/azure/network/vnet)használatával.

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Bejövő nyilvános IP-cím létrehozása 

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Használja [az hálózati nyilvános ip létrehozása,](https://docs.microsoft.com/cli/azure/network/public-ip) hogy hozzon létre egy szabványos nyilvános IP-címet nevű *mypublicipinbound* a *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása 

Hozzon létre egy szabványos IP-címet a terheléselosztó előtér-kimenő konfigurációjához [az az hálózati nyilvános ip create](https://docs.microsoft.com/cli/azure/network/public-ip)használatával.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Azure Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - Előtér-IP,amely fogadja a bejövő hálózati forgalmat a terheléselosztó.
  - Egy háttérkészlet, ahol az előtér-IP a terheléselosztásos hálózati forgalmat küldi.
  - Háttérkészlet a kimenő kapcsolathoz. 
  - Egy állapotminta, amely meghatározza a háttér-virtuális gép példányainak állapotát.
  - A terheléselosztó bejövő szabály, amely meghatározza, hogyan forgalom a virtuális gépek re van elosztva.
  - A terheléselosztó kimenő szabály, amely meghatározza, hogyan forgalom a virtuális gépekről elosztott.

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozzon létre egy terheléselosztót a bejövő IP-címmel az [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) named *lb* használatával, amely tartalmazza a bejövő előtér-IP-konfigurációt és egy *háttérkészletbekinbound-ot,* amely az előző lépésben létrehozott *mypublicipinbound nyilvános* IP-címhez van társítva.

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

Hozzon létre egy további háttércímkészletet a hálózati [lb address-pool nevű](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) virtuális gépek készletének kimenő kapcsolatának meghatározásához, amelynek neve *bepooloutbound.*  Egy külön kimenő készlet létrehozása maximális rugalmasságot biztosít, de kihagyhatja ezt a lépést, és csak a bejövő *bepoolinbound is használhatja.*

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Kimenő előtér-IP létrehozása
Hozza létre a kimenő előtér IP-konfigurációt a terheléselosztó számára az [az hálózati lb előtér-ip létrehozással,](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) amely tartalmazza és a kimenő előtér IP-konfigurációja *myfrontendoutbound* néven, amely a *mypublicipoutbound* nyilvános IP-címhez van társítva

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

A terheléselosztó szabály határozza meg az előtér IP-konfigurációja a bejövő forgalom és a háttérkészlet a forgalom fogadására, valamint a szükséges forrás- és célport. Hozzon létre egy terheléselosztási szabályt *myinboundlbrule* with [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) for listening to port 80 in the frontend pool *myfrontendinbound* and sending load-balanced network traffic to the backend address pool *bepool* also using port 80. 

>[!NOTE]
>Ez a terheléselosztási szabály letiltja az automatikus kimenő (S)NAT-t ennek a szabálynak az eredményeként a --disable-out-snat paraméterrel. A kimenő NAT-t csak a kimenő szabály biztosítja.

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

Egy kimenő szabály határozza meg az előtér nyilvános IP-cím, amelyet az előtér *myfrontendoutbound,* amely az összes kimenő NAT-forgalom, valamint a háttérkészlet, amelyre ez a szabály vonatkozik.  Hozzon létre egy kimenő szabályt *myoutboundrule* a bepool-háttérkészletben *bepool* lévő összes virtuális gép (HÁLÓZATI IP-konfigurációk) kimenő hálózati fordításához.  Az alábbi parancs a kimenő tétlen időoutot is 4-ről 15 percre módosítja, és 1024 helyett 10000 SNAT-portot foglal le.  Tekintse át a [kimenő szabályokat](https://aka.ms/lboutboundrules) további részletekért.

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

Ha nem szeretne külön kimenő készletet használni, módosíthatja az előző parancs címkészlet-argumentumát, hogy ehelyett *bepoolinbound értéket* adjon meg.  Azt javasoljuk, hogy külön készletek et használjon az eredményül kapott konfiguráció rugalmassága és olvashatósága érdekében.

Ezen a ponton folytathatja a virtuális gép hozzáadása a háttér-készlet *bepoolinbound* __és__ *bepooloutbound* frissítésével a megfelelő hálózati adapter erőforrások IP-konfigurációját az [az hálózati ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott standard terheléselosztó, konfigurálva mind a bejövő terheléselosztó forgalmi szabályok, konfigurálva és állapotminta a háttér-készletben lévő virtuális gépek. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
