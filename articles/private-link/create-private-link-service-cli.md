---
title: Azure Private link szolgáltatás létrehozása az Azure CLI használatával
description: Ismerje meg, hogyan hozhat létre Azure Private link Service-t az Azure CLI használatával
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350243"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Privát kapcsolati szolgáltatás létrehozása az Azure CLI-vel
Ebből a cikkből megtudhatja, hogyan hozhat létre egy privát hivatkozási szolgáltatást az Azure-ban az Azure CLI használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI legújabb verzióját kell használnia. A telepített verzió megkereséséhez futtassa a `az --version`. További információ: az [Azure CLI telepítése](/cli/azure/install-azure-cli) a telepítéshez vagy a frissítéshez.
## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása
### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westcentralus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) paranccsal. Ez a példa egy *myVirtualNetwork* nevű alapértelmezett virtuális hálózatot hoz létre egy *mySubnet*nevű alhálózattal:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Alhálózat létrehozása
Hozzon létre egy alhálózatot a virtuális hálózathoz az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)paranccsal. Ez a példa létrehoz egy *mySubnet* nevű alhálózatot a *myVirtualNetwork* virtuális hálózatban:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Belső Load Balancer létrehozása 
Hozzon létre egy belső Load balancert az [az Network LB Create](/cli/azure/network/lb#az-network-lb-create)paranccsal. Ez a példa egy *myILB* nevű belső terheléselosztó létrehozását hozza létre a *myResourceGroup*nevű erőforráscsoport-csoportban. 

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

Ebben a példában nem fedi le a virtuális gépek létrehozását. Kövesse a [belső terheléselosztó létrehozása](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) a virtuális gépek az Azure CLI-vel való terheléselosztásához című szakasz lépéseit, és hozzon létre két virtuális gépet a terheléselosztó háttér-kiszolgálóiként való használatához. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Magánhálózati kapcsolati szolgáltatás hálózati házirendjének letiltása az alhálózaton 
A Private link Service-nek szüksége van egy virtuális hálózaton belüli bármely alhálózat IP-címére. Jelenleg nem támogatjuk a hálózati házirendeket ezeken az IP-címeken.  Ezért le kell tiltania a hálózati házirendeket az alhálózaton. Frissítse az alhálózatot a Private link Service hálózati házirendjeinek letiltásához az [az Network vnet subnet Update paranccsal](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása  
 
Hozzon létre egy privát hivatkozási szolgáltatást standard Load Balancer előtér-IP-konfiguráció használatával az [az Network Private-link-Service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create)paranccsal. Ez a példa létrehoz egy standard Load Balancer *myPLS* nevű privát kapcsolati szolgáltatást a *myResourceGroup*nevű *myLoadBalancer* . 
 
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
A létrehozást követően jegyezze fel a Private link Service AZONOSÍTÓját. Később szüksége lesz erre a szolgáltatásra való kapcsolódás igénylésére.  
 
Ebben a szakaszban a privát kapcsolati szolgáltatás sikeresen létrejött, és készen áll a forgalom fogadására. Vegye figyelembe, hogy a fenti példa csak a privát kapcsolati szolgáltatás Azure CLI-vel történő létrehozását mutatja be.  Nem konfiguráltuk a terheléselosztó-háttér készleteit vagy bármely alkalmazást a háttér-készleteken a forgalom figyelésére. Ha szeretné megtekinteni a végpontok közötti adatforgalmat, javasoljuk, hogy az alkalmazást a standard Load Balancer mögött konfigurálja.  
 
Ezután bemutatjuk, hogyan képezheti le ezt a szolgáltatást egy másik virtuális hálózatban lévő privát végpontra az Azure CLI használatával. Ez a példa a privát végpont létrehozására és az Azure CLI használatával a fent létrehozott privát kapcsolati szolgáltatáshoz való csatlakozásra korlátozódik. Emellett a virtuális hálózatban virtuális gépeket is létrehozhat, hogy forgalmat küldjön/fogadjon a privát végpontnak.        
 
## <a name="private-endpoints"></a>Privát végpontok

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása 
Hozzon létre egy virtuális hálózatot az [az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal. Ez a példa létrehoz egy *myPEVNet* nevű virtuális hálózatot az erőforráscsoport nevű *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Az alhálózat létrehozása 
Hozzon létre egy alhálózatot a virtuális hálózatban az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)paranccsal. Ez a példa létrehoz egy *mySubnet* nevű alhálózatot a *myResourcegroup*nevű *myPEVnet* nevű virtuális hálózatban: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Privát végponti hálózati házirendek letiltása az alhálózaton 
A magánhálózati végpontok bármely, a virtuális hálózaton belül tetszőleges alhálózatban létrehozhatók. Jelenleg nem támogatjuk a hálózati házirendeket a privát végpontokon.  Ezért le kell tiltania a hálózati házirendeket az alhálózaton. Frissítse az alhálózatot a privát végpontok hálózati házirendjeinek letiltásához az [az Network vnet subnet Update paranccsal](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Privát végpont létrehozása és csatlakozás a Private link Service-hez 
Hozzon létre egy privát végpontot a virtuális hálózaton fent létrehozott privát kapcsolati szolgáltatás fogyasztásához:
  
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
A *magánhálózati kapcsolat erőforrás-azonosítóját* a `az network private-link-service show` a Private link Service szolgáltatásban kérheti le. Az azonosító a következőképpen fog kinézni:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/Providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Magánhálózati kapcsolati szolgáltatás kapcsolatainak megjelenítése 
 
Lásd: kapcsolódási kérelmek a Private link Service [-ben az az Network Private-link-Service show](/cli/azure/network/private-link-service#az-network-private-link-service-show)paranccsal.    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Következő lépések
- További információ az [Azure Private link Service](private-link-service-overview.md) -ről
 
