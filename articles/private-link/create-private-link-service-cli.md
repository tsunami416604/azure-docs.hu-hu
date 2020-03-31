---
title: Azure Private Link szolgáltatás létrehozása az Azure CLI használatával
description: Ismerje meg, hogyan hozhat létre Azure Private Link szolgáltatást az Azure CLI használatával
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350243"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Privát kapcsolatszolgáltatás létrehozása az Azure CLI használatával
Ez a cikk bemutatja, hogyan hozhat létre egy privát kapcsolat szolgáltatás az Azure-ban az Azure CLI használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy az Azure CLI telepítése és használata helyett, ez a rövid útmutató megköveteli, hogy a legújabb Azure CLI-verzió. A telepített verzió megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a program `az --version` A telepítési vagy frissítési információkért tekintse meg az [Azure CLI telepítése](/cli/azure/install-azure-cli) című témakört.
## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása
### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westcentralus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) paranccsal. Ez a példa létrehoz egy alapértelmezett virtuális hálózatnevű *myVirtualNetwork* egy alhálózat nevű *mySubnet:*

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Alhálózat létrehozása
Hozzon létre egy alhálózatot a virtuális hálózathoz az [az hálózati virtuálishálózat alhálózatának létrehozása.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) Ez a példa létrehoz egy *mySubnet* nevű alhálózatot a *myVirtualNetwork* virtuális hálózatban:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Belső terheléselosztó létrehozása 
Hozzon létre egy belső terheléselosztót [az hálózati lb create](/cli/azure/network/lb#az-network-lb-create)segítségével. Ez a példa létrehoz egy *myILB* nevű belső terheléselosztót a *myResourceGroup*nevű erőforráscsoportban. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Terheléselosztó állapotmintájának létrehozása

Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom fogadására. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk. Hozzon létre egy állapotmintát az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) paranccsal a virtuális gépek állapotának monitorozásához. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myHTTPRule* nevű terheléselosztási szabályt az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) paranccsal a *myFrontEnd* nevű előtérbeli címkészlet 80-as portjának figyeléséhez és az elosztott terhelésű hálózati forgalomnak a *myBackEndPool* nevű háttércímkészletre való küldéséhez, amely a 80-as portot használja. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában nem terjed ki a virtuális gép létrehozása. A Belső terheléselosztó létrehozása a [terheléselosztási virtuális gépek Azure CLI használatával](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) két virtuális gép létrehozásához a terheléselosztó háttérkiszolgálóként használandó lépéseit hajthatja végre. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>A Private Link szolgáltatás hálózati házirendjeinek letiltása az alhálózaton 
A Private Link szolgáltatás hoz létre egy IP-t bármely, a virtuális hálózaton belül kiválasztott alhálózatról. Jelenleg nem támogatjuk a hálózati házirendeket ezeken az IP-ken.  Ezért le kell tiltanunk az alhálózat hálózati házirendjeit. Frissítse az alhálózatot a Private Link szolgáltatás hálózati házirendjeinek letiltásához [az az hálózati virtuális hálózat alhálózati frissítésével.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása  
 
Hozzon létre egy privát kapcsolat szolgáltatást a Standard Load Balancer előtér-IP-konfigurációhasználatával [az hálózati magánkapcsolat-szolgáltatás létrehozása segítségével.](/cli/azure/network/private-link-service#az-network-private-link-service-create) Ebben a példában létrehoz egy *myPLS* nevű privát kapcsolatszolgáltatást a *myResourceGroup*nevű erőforráscsoportban *a myLoadBalancer* nevű standard terheléselosztó használatával. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
A létrehozás után vegye figyelembe a Privát kapcsolat szolgáltatás azonosítóját. Erre később szüksége lesz a szolgáltatáshoz való csatlakozás igényléséhez.  
 
Ebben a szakaszban a Privát kapcsolat szolgáltatás sikeresen létrejön, és készen áll a forgalom fogadására. Vegye figyelembe, hogy a fenti példa csak az Azure CLI használatával létrehozott Private Link szolgáltatás bemutatása.  Még nem konfigurálta a terheléselosztó háttérkészletek vagy bármely alkalmazás a háttérkészletek a forgalom figyelésére. Ha azt szeretné, hogy a végpontok közötti forgalom folyamatok, javasoljuk, hogy konfigurálja az alkalmazást a standard terheléselosztó mögött.  
 
Ezután bemutatjuk, hogyan képezheti le ezt a szolgáltatást egy privát végponthoz különböző virtuális hálózatban az Azure CLI használatával. Ismét a példa a privát végpont létrehozására és a fent az Azure CLI használatával létrehozott Private Link szolgáltatáshoz való csatlakozásra korlátozódik. Emellett virtuális gépeket is létrehozhat a virtuális hálózatban a forgalom küldéséhez/fogadásához a privát végpontra.        
 
## <a name="private-endpoints"></a>Privát végpontok

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása 
Hozzon létre egy virtuális [hálózatot az hálózati virtuális hálózat létrehozása .](/cli/azure/network/vnet#az-network-vnet-create) Ez a példa létrehoz egy *myPEVNet* nevű virtuális hálózatot a *myResourcegroup*nevű erőforráscsoportban: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Az alhálózat létrehozása 
Hozzon létre egy alhálózatot a virtuális hálózatban [az hálózati virtuális hálózat alhálózatának létrehozása.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) Ez a példa létrehoz egy *mySubnet* nevű alhálózatot *a myPEVnet* nevű virtuális hálózatban a *myResourcegroup*nevű erőforráscsoportban: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Magánvégpont hálózati házirendjeinek letiltása az alhálózaton 
A privát végpont bármely tetszőleges alhálózatban létrehozható egy virtuális hálózaton belül. Jelenleg nem támogatjuk a hálózati házirendeket a privát végpontokon.  Ezért le kell tiltanunk az alhálózat hálózati házirendjeit. Frissítse az alhálózatot a magánvégpont hálózati házirendjeinek letiltásához [az az hálózati virtuális hálózat alhálózati frissítésével.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Privát végpont létrehozása és csatlakozás privát kapcsolatszolgáltatáshoz 
Hozzon létre egy privát végpontot a virtuális hálózatban a fent létrehozott Private Link szolgáltatás elhasználódásához:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
A *private-connection-resource-id* a `az network private-link-service show` Private Link szolgáltatás. Az azonosító így fog kinézni:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Privát kapcsolat szolgáltatáskapcsolatainak megjelenítése 
 
Tekintse meg a kapcsolatkérelmeket a Private Link szolgáltatáson az [az network private-link-service show használatával.](/cli/azure/network/private-link-service#az-network-private-link-service-show)    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>További lépések
- További információ az [Azure Private Link szolgáltatásról](private-link-service-overview.md)
 
