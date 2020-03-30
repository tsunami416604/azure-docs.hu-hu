---
title: Statikus IP-cím a tárolócsoporthoz
description: Tárolócsoport létrehozása virtuális hálózatban, és egy Azure-alkalmazásátjáró használatával statikus előtér-IP-cím egy tárolóba kötött webalkalmazás
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481789"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Tárolócsoport statikus IP-címének felfedése

Ez a cikk egy [tárolócsoport](container-instances-container-groups.md) statikus, nyilvános IP-címének egy [Azure-alkalmazásátjáró](../application-gateway/overview.md)használatával történő elérhetővé tételével jeleníti meg az egyik módot. Kövesse ezeket a lépéseket, ha statikus belépési pontra van szüksége egy külső fekvésű, az Azure Container Instances-ben futó, külső fekvésű tárolós alkalmazáshoz. 

Ebben a cikkben az Azure CLI segítségével hozza létre az erőforrásokat ebben a forgatókönyvben:

* Azure virtuális hálózat
* A virtuális hálózatban telepített tárolócsoport [(előzetes verzió),](container-instances-vnet.md) amely egy kis webalkalmazást tartalmaz
* Egy alkalmazásátjáró egy nyilvános előtér-IP-címmel, egy figyelő egy webhely üzemeltetéséhez az átjárón, és egy útvonalat a háttér-tároló csoport

Mindaddig, amíg az alkalmazásátjáró fut, és a tárolócsoport elérhetővé tesz egy stabil privát IP-címet a hálózat delegált alhálózatában, a tárolócsoport elérhető ezen a nyilvános IP-címen.

> [!NOTE]
> Az alkalmazásátjáró azure-díjai az átjáró kiépített és elérhető ideje, valamint az általa feldolgozott adatok mennyisége alapján. Lásd: [árképzés](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Egy tipikus esetben előfordulhat, hogy már rendelkezik egy Azure virtuális hálózat. Ha még nem rendelkezik ilyennel, hozzon létre egyet a következő példaparancsokkal látható módon. A virtuális hálózatnak külön alhálózatokra van szüksége az alkalmazásátjáróhoz és a tárolócsoporthoz.

Ha szüksége van rá, hozzon létre egy Azure-erőforráscsoportot. Példa:

```azureci
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az hálózati vnet create][az-network-vnet-create] paranccsal. Ez a parancs létrehozza a *myAGSubnet alhálózatot* a hálózatban.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Használja az [az hálózati virtuálishálózat create][az-network-vnet-subnet-create] parancsot a háttértároló-csoport alhálózatának létrehozásához. Itt ez a neve *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Használja az [az hálózati nyilvános ip create][az-network-public-ip-create] parancsot statikus nyilvános IP-erőforrás létrehozásához. Egy későbbi lépésben ez a cím az alkalmazásátjáró előtér-végeként van konfigurálva.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Tárolócsoport létrehozása

Futtassa a következő [az container create][az-container-create] egy tárolócsoportot az előző lépésben konfigurált virtuális hálózatban. 

A csoport telepítve van a *myACISubnet* alhálózatban, és tartalmaz egy `aci-helloworld` appcontainer nevű *példányt,* amely lekéri a lemezképet. Amint azt a dokumentációban szereplő egyéb cikkek is mutatják, ez a lemezkép egy node.js nyelven írt kis webalkalmazást csomagol, amely statikus HTML-oldalt szolgál ki. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Sikeres üzembe helyezés után a tárolócsoport hoz egy privát IP-címet a virtuális hálózatban. Futtassa például a következő [az container show][az-container-show] parancsot a csoport IP-címének beolvasásához:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Az eredmény ehhez hasonlóan fog kinézni: `10.0.2.4`.

Későbbi lépésben történő használatra mentse az IP-címet egy környezeti változóban:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Alkalmazásátjáró létrehozása

Hozzon létre egy alkalmazásátjárót a virtuális hálózatban az [alkalmazásátjáró rövid útmutatójának lépéseit követve.](../application-gateway/quick-create-cli.md) A következő [hálózati alkalmazás-átjáró létrehozása][az-network-application-gateway-create] parancs létrehoz egy átjárót egy nyilvános előtér-IP-címmel és egy útvonalat a háttértároló-csoporthoz. Az átjáró beállításaival kapcsolatos részleteket az [Application Gateway dokumentációjában](/azure/application-gateway/) találja.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Az Azure-nak akár 15 percet is igénybe vehet az alkalmazásátjáró létrehozása. 

## <a name="test-public-ip-address"></a>Nyilvános IP-cím tesztelése
  
Most tesztelheti a hozzáférést a webalkalmazás fut a tárolócsoport mögött az alkalmazásátjáró.

Futtassa az [az hálózati nyilvános ip show][az-network-public-ip-show] parancsot az átjáró előtér-nyilvános IP-címének lekéréséhez:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

A kimenet nyilvános IP-cím, `52.142.18.133`hasonlóan a következőkhöz: .

Ha sikeresen szeretné megtekinteni a futó webalkalmazást, keresse meg az átjáró nyilvános IP-címét a böngészőben. A sikeres hozzáférés hasonló a következőkhöz:

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>További lépések

* Tekintse meg a [rövid útmutató sablont,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) amely egy WordPress-tárolópéldányt tartalmazó tárolócsoportot hoz létre egy alkalmazásátjáró mögötti háttérkiszolgálóként.
* Az alkalmazásátjárót ssl-végződési tanúsítvánnyal is konfigurálhatja. Lásd az [áttekintést](../application-gateway/ssl-overview.md) és a [bemutató](../application-gateway/create-ssl-portal.md).
* A forgatókönyvtől függően fontolja meg más Azure-terheléselosztási megoldások használatát az Azure Container Instances használatával. Például az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) használatával több tárolópéldányok és több régió közötti forgalmat oszthatja el. Lásd ezt a [blogbejegyzést](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show