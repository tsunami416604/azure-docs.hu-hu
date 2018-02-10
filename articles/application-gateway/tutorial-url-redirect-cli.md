---
title: "Hozzon létre egy alkalmazás URL-cím elérési út-alapú átirányítási - Azure CLI |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy alkalmazás URL-cím elérési út-alapú forgalmat az Azure parancssori felület használatával."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: 73fc20cf738f584008e7d8a019b8f7012dcacab1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Hozzon létre egy alkalmazás URL-cím elérési út-alapú átirányítási az Azure parancssori felület használatával

Az Azure CLI segítségével konfigurálhatja [URL-cím elérési út-alapú útválasztási szabályok](application-gateway-url-route-overview.md) létrehozásakor egy [Alkalmazásátjáró](application-gateway-introduction.md). Ebben az oktatóanyagban létrehoz használatával háttérkészletek [virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Ezután hozzon létre az URL-cím győződjön meg arról, hogy a webes forgalom a rendszer átirányítja a megfelelő háttérkészlet útválasztási szabályokat.

Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Adja hozzá a figyelők és útválasztási szabályokat
> * A háttérkészlet virtuálisgép-méretezési csoportok létrehozása

A következő példa bemutatja a hely adatforgalma 8081 és a 8080-as portot és az azonos háttérkészletek irányítása:

![URL-cím útválasztási – példa](./media/tutorial-url-redirect-cli/scenario.png)

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

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Használhat [az hálózati Alkalmazásátjáró létrehozása](/cli/azure/application-gateway#create) myAppGateway nevű Alkalmazásátjáró létrehozása. Alkalmazásátjáró az Azure parancssori felület használatával hoz létre, amikor konfigurációs adatokat, például, sku, és a HTTP-beállításait adja meg. Az Alkalmazásátjáró hozzá van rendelve *myAGSubnet* és *myPublicIPSddress* , amelyet korábban hozott létre.  

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


### <a name="add-backend-pools-and-ports"></a>Adja hozzá a háttérkészlet és port

Hozzáadhat nevű háttércímkészletek *imagesBackendPool* és *videoBackendPool* az Alkalmazásátjáró használatával [az hálózati Alkalmazásátjáró címkészlet létrehozása](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). Hozzáadta a készleteket használ előtérbeli portjainak [az előtér-port hálózati-Alkalmazásátjáró létrehozása](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

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
  --name bport
az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Figyelők és szabályok hozzáadása

### <a name="add-listeners"></a>Figyelők hozzáadása

Adja hozzá a háttér-figyelők nevű *backendListener* és *redirectedListener* irányíthatja a forgalmat használata szükséges, amely [az hálózati Alkalmazásátjáró http-figyelő létrehozása](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Az alapértelmezett URL-cím elérési út társítás hozzáadása

URL-cím elérési út maps győződjön meg arról, hogy adott URL-címek meghatározott háttérkészletek legyenek átirányítva. URL-cím elérési út maps nevű hozhat létre *imagePathRule* és *videoPathRule* használatával [az hálózati Alkalmazásátjáró URL-cím-elérési út – térkép létrehozásához](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) és [az hálózati alkalmazás-átjáró url-elérési út-leképezési szabály létrehozása](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Átirányítás konfigurációjának hozzáadása

Konfigurálhatja a figyelővel átirányítás [az hálózati Alkalmazásátjáró átirányítási-config létrehozása](/cli/azure/application-gateway#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Az átirányítási URL-cím elérési út társítás hozzáadása

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Adjon hozzá útválasztási szabályokat

Az útválasztási szabályokat az URL-cím elérési út maps társítani a figyelők létrehozott. Nevű szabályokat adhat hozzá *defaultRule* és *redirectedRule* használatával [az hálózati Alkalmazásátjáró szabály létrehozása](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
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

Ahhoz, hogy az alkalmazás átjáró nyilvános IP-címét, használhatja a [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az_network_public_ip_show). Másolja a nyilvános IP-címet, és illessze be a böngésző címsorába. Például a *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm*, *http://40.121.222.19:8080/video/test.htm*, vagy *http:// 40.121.222.19:8081/Images/test.htm*.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alkalmazás átjáró alap URL-cím tesztelése](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Módosítsa az URL-címet http://&lt;ip-cím&gt;: 8080/video/test.html, és az IP-címe &lt;ip-cím&gt;, és az alábbihoz hasonlót kell megjelennie:

![Az alkalmazás átjáró képek URL tesztelése](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Módosítsa az URL-címet http://&lt;ip-cím&gt;: 8080/video/test.html, és az IP-címe &lt;ip-cím&gt;, és az alábbihoz hasonlót kell megjelennie.

![Az alkalmazás átjáró Videó URL tesztelése](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Módosítsa az URL-címet http://&lt;ip-cím&gt;: 8081/images/test.htm, és az IP-címe &lt;ip-cím&gt;, és visszairányítja a képek háttérkészlet http://forgalmatlátni&lt;ip-cím&gt;: 8080/képek.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Adja hozzá a figyelők és útválasztási szabályokat
> * A háttérkészlet virtuálisgép-méretezési csoportok létrehozása

> [!div class="nextstepaction"]
> [További tudnivalók az Alkalmazásátjáró teendők](application-gateway-introduction.md)