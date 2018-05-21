---
title: Hozzon létre egy alkalmazás URL-cím elérési út-alapú útválasztási szabályokat - Azure parancssori Felülettel |} Microsoft Docs
description: Megtudhatja, hogyan URL-cím elérési út-alapú útválasztási szabályok létrehozása egy alkalmazás átjáró és a virtuális gép méretezési készletben, az Azure parancssori felület használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: ff02688d59f32641319e6816dc55744422c62eeb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Hozzon létre egy alkalmazás URL-cím elérési út-alapú útválasztási szabályokat az Azure parancssori felület használatával

Az Azure CLI segítségével konfigurálhatja [URL-cím elérési út-alapú útválasztási szabályok](application-gateway-url-route-overview.md) létrehozásakor egy [Alkalmazásátjáró](application-gateway-introduction.md). Ebben az oktatóanyagban háttérkészletek használatával létrehozhat egy [virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Ezután hozzon létre útválasztási szabályokat, győződjön meg arról, hogy a webes forgalom érkezik a készletek a megfelelő kiszolgálókat.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Hozzon létre egy alkalmazás URL-cím térkép
> * A háttérkészlet hozzon létre virtuálisgép-méretezési csoportok

![URL-cím útválasztási – példa](./media/application-gateway-create-url-route-cli/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforrás csoport használatával [az csoport létrehozása](/cli/azure/group#create).

Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupAG* a a *eastus* helyét.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Nevű a virtuális hálózat létrehozása *myVNet* és nevű alhálózat *myAGSubnet* használatával [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_net). Majd adja hozzá a nevű alhálózat *myBackendSubnet* , amely van szükség a háttérkiszolgálók használatával [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). A nyilvános IP-cím nevű létrehozása *myAGPublicIPAddress* használatával [létrehozása az hálózati nyilvános ip-](/cli/azure/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>Hozzon létre az alkalmazás URL-cím térkép

Használhat [az hálózati Alkalmazásátjáró létrehozása](/cli/azure/application-gateway#create) nevű Alkalmazásátjáró létrehozása *myAppGateway*. Alkalmazásátjáró az Azure parancssori felület használatával hoz létre, amikor konfigurációs adatokat, például, sku, és a HTTP-beállításait adja meg. Az Alkalmazásátjáró hozzá van rendelve *myAGSubnet* és *myAGPublicIPAddress* , amelyet korábban hozott létre. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Az alkalmazás-átjáró hozható létre több percig is eltarthat. Az Alkalmazásátjáró létrehozása után megtekintheti az új szolgáltatásokat is:

- *appGatewayBackendPool* -Alkalmazásátjáró rendelkeznie kell legalább egy háttér címkészletet.
- *appGatewayBackendHttpSettings* – Megadja, hogy 80-as porton, és olyan HTTP protokollt használja a kommunikációhoz.
- *appGatewayHttpListener* -a társított alapértelmezett figyelő *appGatewayBackendPool*.
- *appGatewayFrontendIP* -hozzárendel *myAGPublicIPAddress* való *appGatewayHttpListener*.
- *Szabály1* - útválasztási szabály társított alapértelmezett *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Adja hozzá a lemezkép és a videó háttérkészlet és port

Hozzáadhat nevű háttérkészletek *imagesBackendPool* és *videoBackendPool* az Alkalmazásátjáró használatával [az hálózati Alkalmazásátjáró címkészlet létrehozása](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). A készletek használatával elülső rétegbeli portot hozzáadhat [az előtér-port hálózati-Alkalmazásátjáró létrehozása](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Adja hozzá a háttér-figyelő

Adja hozzá a háttér-figyelő nevű *backendListener* irányíthatja a forgalmat használata szükséges, amely [az hálózati Alkalmazásátjáró http-figyelő létrehozása](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>URL-cím elérési út-társítás hozzáadása

URL-cím elérési út maps győződjön meg arról, hogy adott URL-címek meghatározott háttérkészletek legyenek átirányítva. URL-cím elérési út maps nevű hozhat létre *imagePathRule* és *videoPathRule* használatával [az hálózati Alkalmazásátjáró URL-cím-elérési út – térkép létrehozásához](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) és [az hálózati alkalmazás-átjáró url-elérési út-leképezési szabály létrehozása](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>-Útválasztási szabály hozzáadása

A szabály az URL-címet a maps társítja a létrehozott figyelőt. Nevű szabályt is hozzáadhat *rule2* használatával [az hálózati Alkalmazásátjáró szabály létrehozása](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Hozzon létre virtuálisgép-méretezési csoportok

Ebben a példában hoz létre, amely támogatja a három háttérkészletek létrehozott három virtuális gép méretezési készlet. A méretezési csoportok az Ön által létrehozott megnevezett *myvmss1*, *myvmss2*, és *myvmss3*. Minden egyes méretezési két virtuálisgép-példánya telepíthető, amely NGINX tartalmazza.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Az NGINX telepítése

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

Ahhoz, hogy az alkalmazás átjáró nyilvános IP-címét, használhatja a [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az_network_public_ip_show). Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például a *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm*, vagy *http://40.121.222.19:8080/video/test.htm*.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alkalmazás átjáró alap URL-cím tesztelése](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

Az alap URL-cím végére http://<ip-address>:8080/video/test.html módosítsa az URL-címet, és az alábbihoz hasonlót kell megjelennie:

![Az alkalmazás átjáró képek URL tesztelése](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

Módosítsa az URL-cím http://<ip-address>:8080/video/test.html, majd az alábbihoz hasonlót kell megjelennie.

![Az alkalmazás átjáró Videó URL tesztelése](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A hálózat beállítása
> * Hozzon létre egy alkalmazás URL-cím térkép
> * A háttérkészlet hozzon létre virtuálisgép-méretezési csoportok

További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.
