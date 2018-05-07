---
title: Hozzon létre egy alkalmazás belső átirányítási - Azure CLI |} Microsoft Docs
description: Ismerje meg, amely átirányítja a megfelelő készlethez, az Azure parancssori felület használatával a belső webes forgalom Alkalmazásátjáró létrehozása.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2017
ms.author: victorh
ms.openlocfilehash: a477896255baf40fd824f23498377d9677661169
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Hozzon létre egy alkalmazás belső átirányítása, az Azure parancssori felület használatával

Az Azure CLI segítségével konfigurálhatja [webes forgalom átirányítása](multiple-site-overview.md) létrehozásakor egy [Alkalmazásátjáró](overview.md). Ebben az oktatóanyagban a háttérkészlet, a virtuálisgép-méretezési csoport használatával határozza meg. Ezután konfigurálja figyelők és szabályok alapján a tartományok, amelyek a saját győződjön meg arról, hogy a webes forgalom érkezik a megfelelő készlethez. Ez az oktatóanyag feltételezi, hogy Ön a tulajdonosa több tartományok és felhasználási mintái *www.contoso.com* és *www.contoso.org*.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és átirányítási szabály hozzáadása
> * Hozzon létre egy virtuálisgép-méretezési háttérkészlet állítható be
> * Hozzon létre egy CNAME rekordot a tartományban

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

Nevű a virtuális hálózat létrehozása *myVNet* és nevű alhálózat *myAGSubnet* használatával [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_net). Majd adja hozzá a nevű alhálózat *myBackendSubnet* kiszolgálók háttérkészlet van szükség, amely [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). A nyilvános IP-cím nevű létrehozása *myAGPublicIPAddress* használatával [létrehozása az hálózati nyilvános ip-](/cli/azure/public-ip#az_network_public_ip_create).

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


## <a name="add-listeners-and-rules"></a>Figyelők és szabályok hozzáadása 

Egy figyelő szükséges ahhoz, hogy a forgalom megfelelő irányításához a háttérkészletbe az Alkalmazásátjáró. Ebben az oktatóanyagban a két tartomány hozzon létre két figyelők. Ebben a példában figyelői jönnek létre, a tartományok *www.contoso.com* és *www.contoso.org*.

Adja hozzá a háttér-figyelők, amelyek szükségesek ahhoz, hogy az útvonal-forgalom használatával [az hálózati Alkalmazásátjáró http-figyelő létrehozása](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Az átirányítás konfigurációjának hozzáadása

Adja hozzá az átirányítás konfigurációs által küldött forgalmat *www.consoto.org* a figyelőhöz a *www.contoso.com* átjáró használatával [az alkalmazás-átjáró hálózati az átirányítási-config létrehozása](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Adjon hozzá útválasztási szabályokat

Szabályok feldolgozása, amelyben a létrehozásuk sorrendjében, és a forgalom irányítja a rendszer az első szabály, amely megfelel az URL-cím használatával küld az Alkalmazásátjáró. Például ha egy szabályt egy alapszintű figyelő és egy többhelyes figyelő mindkét ugyanazt a portot használó szabály, a szabály a többhelyes figyelőjével szerepelnie kell a szabály a vártnak megfelelően működik az alapvető figyelő ahhoz, hogy a többhelyes szabály előtt. 

Ebben a példában két új szabályt hoz létre, és törölje az alapértelmezett szabály, amely hozták létre.  A szabály használatával adhat hozzá [az hálózati Alkalmazásátjáró szabály létrehozása](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Hozzon létre virtuálisgép-méretezési csoportok

Ebben a példában hozzon létre egy virtuálisgép-méretezési csoport, amely támogatja a létrehozott háttérkészlet. Az Ön által létrehozott méretezési nevű *myvmss* és telepíthető, amely NGINX két virtuális gép példányait tartalmazza.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Az NGINX telepítése

Futtassa a parancsot a rendszerhéj ablakban:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/vhorne/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>CNAME rekord létrehozására a tartományban

Nyilvános IP-címmel az Alkalmazásátjáró létrehozása után lekérni a DNS-címét, és hozzon létre egy CNAME rekordot a tartomány segítségével. Használhat [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az_network_public_ip_show) lekérni a DNS-címét az Alkalmazásátjáró. Másolás a *fqdn* a DNSSettings értékének és az legyen az Ön által létrehozott CNAME rekord értékét. A-rekordok használata nem ajánlott, mert a VIP módosíthatja az Alkalmazásátjáró újraindításakor.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

Adjon meg a tartománynevet a böngésző címsorába. Például a http://www.contoso.com.

![Az alkalmazás átjáró contoso hely tesztelése](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Módosítsa a címet a tartományhoz, például http://www.contoso.org és láthatja, hogy a forgalom átirányítva vissza a figyelő a www.contoso.com.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és átirányítási szabály hozzáadása
> * Hozzon létre egy virtuálisgép-méretezési háttérkészlet állítható be
> * Hozzon létre egy CNAME rekordot a tartományban