---
title: Terheléselosztás és kimenő szabályok konfigurálása az Azure CLI használatával
titleSuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogyan konfigurálhatja a terheléselosztást és a kimenő szabályokat egy standard Load Balancer az Azure CLI használatával.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 2b326c6c58b4685b6e73d0f9a641a2f90807d705
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84803747"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>A standard Load Balancer terheléselosztási és kimenő szabályainak konfigurálása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a kimenő szabályokat standard Load Balancer az Azure CLI használatával.  

Ha elkészült, a Load Balancer erőforrás két előtérben és a hozzájuk társított szabályokat tartalmaz: egyet a bejövő és egy másikat a kimenő számára.  Mindegyik frontend egy nyilvános IP-címhez hivatkozik, és ez a forgatókönyv egy másik nyilvános IP-címet használ a bejövő és kimenő forgalomhoz.   A terheléselosztási szabály csak a bejövő terheléselosztást biztosítja, és a Kimenő szabály vezérli a virtuális gép számára biztosított kimenő NAT-t.  Ez a rövid útmutató két különálló háttér-készletet használ, amelyek közül az egyik a bejövő és az egyik a kimenő, a képesség szemléltetése és a rugalmasság biztosítása ehhez a forgatókönyvhöz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myresourcegroupoutbound* nevű erőforráscsoportot a *eastus2* helyen:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy *myvnetoutbound* nevű virtuális hálózatot egy *mysubnetoutbound* nevű alhálózattal a *myresourcegroupoutbound* az [az Network vnet Create](https://docs.microsoft.com/cli/azure/network/vnet)paranccsal.

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Bejövő nyilvános IP-cím létrehozása 

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) paranccsal hozzon létre egy *Mypublicipinbound* nevű szabványos nyilvános IP-címet a *myresourcegroupoutbound*-ben.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Kimenő nyilvános IP-cím létrehozása 

Hozzon létre egy szabványos IP-címet a Load Balancer előtér-kimeneti konfigurációjához az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip)paranccsal.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Azure Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:
  - Egy előtéri IP-cím, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  - Egy háttérbeli készlet, amelyben a előtéri IP-cím elküldi a terheléselosztási hálózati forgalmat.
  - Egy háttérbeli készlet a kimenő kapcsolathoz. 
  - Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  - Egy terheléselosztó bejövő szabálya, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.
  - Egy terheléselosztó kimenő szabálya, amely meghatározza, hogy a rendszer hogyan ossza szét a forgalmat a virtuális gépekről.

### <a name="create-load-balancer"></a>Load Balancer létrehozása

Hozzon létre egy Load Balancer a bejövő IP-címmel az [az Network LB Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) *LB* néven, amely tartalmaz egy bejövő előtérbeli IP-konfigurációt és egy háttér-készlet *bepoolinbound* , amely az előző lépésben létrehozott nyilvános IP- *mypublicipinbound* van társítva.

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

### <a name="create-outbound-frontend-ip"></a>Kimenő előtéri IP-cím létrehozása
Hozza létre a Load Balancer kimenő előtérbeli IP-konfigurációját az az [Network LB frontend-IP Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) paranccsal, amely tartalmazza és a *myfrontendoutbound* nevű, a nyilvános IP-címhez társított IP- *mypublicipoutbound* .

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Állapot mintavételének létrehozása

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

A terheléselosztó szabály a bejövő forgalom és a háttér-készlet előtérbeli IP-konfigurációját határozza meg a forgalom fogadásához, valamint a szükséges forrás-és célport mellett. Hozzon létre egy terheléselosztó *-szabályt az* [az Network LB Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) paranccsal, hogy a 80-es portot Hallgassa meg a frontend *-készlet* *myfrontendinbound* , és a terheléselosztási hálózati forgalmat a myinboundlbrule a 80-es porton keresztül. 

>[!NOTE]
>Ez a terheléselosztási szabály letiltja a (z)--disable-kimenő-SNAT paraméterrel a szabály eredményét az automatikus kimenő (ek) NAT-nak. A kimenő NAT-t csak a Kimenő szabály kapja meg.

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

A kimenő szabályok határozzák meg a előtér nyilvános IP-címét, amelyet az előtér- *myfrontendoutbound*képvisel, amelyet a rendszer az összes kimenő NAT-forgalomhoz és a szabály hatálya alá eső háttér-készlethez is használ.  Hozzon létre egy kimenő *myoutboundrule* az összes virtuális gép (NIC IP-konfiguráció) kimenő hálózati fordításához *a beépítő háttér-* készletben.  Az alábbi parancs a kimenő üresjárati időkorlátot 4 – 15 percet is módosítja, és 10000 SNAT-portot foglal le 1024 helyett.  További részletekért tekintse meg a [kimenő szabályokat](https://aka.ms/lboutboundrules) .

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

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes kapcsolódó erőforrás.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozta standard Load Balancer, konfigurálta a beérkező terheléselosztó forgalmi szabályait, a konfigurált és az állapot-mintavételt a háttér-készletben lévő virtuális gépekhez. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
