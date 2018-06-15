---
title: Alkalmazásátjáró létrehozása az Azure CLI - tanúsítvány |} Microsoft Docs
description: Megtudhatja, hogyan Alkalmazásátjáró létrehozása és hozzáadása egy tanúsítványt az SSL-lezárást az Azure parancssori felület használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: victorh
ms.openlocfilehash: dc3a15ea66d9ead35247bee1673d120faac631d9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355168"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>HTTP, HTTPS átirányítás az Azure parancssori felület használatával hozhat létre olyan átjárót

Az Azure parancssori felület használatával hozzon létre egy [Alkalmazásátjáró](overview.md) egy SSL-lezárást tanúsítványával. Útválasztási szabályainak használt HTTP-forgalom átirányítása a HTTPS-portot az Alkalmazásátjáró. Ebben a példában is létrehozhat egy [virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) az Alkalmazásátjáró két virtuálisgép-példányok tartalmazó háttérkészlet számára.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * A hálózat beállítása
> * Hozzon létre egy alkalmazást a tanúsítvány
> * Figyelő és átirányítási szabály hozzáadása
> * Hozzon létre egy virtuálisgép-méretezési állítható be alapértelmezett háttérkészlet

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Az éles környezetben való használathoz importálnia kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt. Ebben az oktatóanyagban létrehozhat egy önaláírt tanúsítványt és a pfx-fájlt az openssl-paranccsal.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Adja meg az értékeket, amelyeket a tanúsítvány jelentéssel bírnak. Elfogadhatja az alapértelmezett értékeket.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Adja meg a jelszót a tanúsítványhoz. Ebben a példában *Azure123456!* használatban van.

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

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Használhat [az hálózati Alkalmazásátjáró létrehozása](/cli/azure/network/application-gateway#az_network_application_gateway_create) nevű Alkalmazásátjáró létrehozása *myAppGateway*. Alkalmazásátjáró az Azure parancssori felület használatával hoz létre, amikor konfigurációs adatokat, például, sku, és a HTTP-beállításait adja meg. 

Az Alkalmazásátjáró hozzá van rendelve *myAGSubnet* és *myAGPublicIPAddress* , amelyet korábban hozott létre. Ebben a példában társít a létrehozott tanúsítvány és a jelszót az Alkalmazásátjáró létrehozásakor. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Az alkalmazás-átjáró hozható létre több percig is eltarthat. Az Alkalmazásátjáró létrehozása után megtekintheti az új szolgáltatásokat is:

- *appGatewayBackendPool* -Alkalmazásátjáró rendelkeznie kell legalább egy háttér címkészletet.
- *appGatewayBackendHttpSettings* – Megadja, hogy 80-as porton, és olyan HTTP protokollt használja a kommunikációhoz.
- *appGatewayHttpListener* -a társított alapértelmezett figyelő *appGatewayBackendPool*.
- *appGatewayFrontendIP* -hozzárendel *myAGPublicIPAddress* való *appGatewayHttpListener*.
- *Szabály1* - útválasztási szabály társított alapértelmezett *appGatewayHttpListener*.

## <a name="add-a-listener-and-redirection-rule"></a>Figyelő és átirányítási szabály hozzáadása

### <a name="add-the-http-port"></a>Adja hozzá a HTTP-port

Használhat [az előtér-port hálózati-Alkalmazásátjáró létrehozása](/cli/azure/network/application-gateway/frontend-port#az_network_application_gateway_frontend_port_create) a HTTP-port hozzáadása az Alkalmazásátjáró.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Adja hozzá a HTTP-figyelő

Használhat [az hálózati Alkalmazásátjáró http-figyelő létrehozása](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) nevű figyelő hozzáadandó *myListener* az Alkalmazásátjáró.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Az átirányítás konfigurációjának hozzáadása

Adja hozzá a HTTP HTTPS átirányítás konfigurációját, és az átjárót használó [az hálózati Alkalmazásátjáró átirányítási-config létrehozása](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Az útválasztási szabály hozzáadása

A nevű-útválasztási szabály hozzáadása *rule2* átjáró használatával átirányítási konfigurációjával [az hálózati Alkalmazásátjáró szabály létrehozása](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában hoz létre egy virtuálisgép-méretezési beállítása a nevesített *myvmss* kiszolgálók biztosít az Alkalmazásátjáró a háttérkészlet számára. A méretezési csoportban lévő virtuális gépek társított *myBackendSubnet* és *appGatewayBackendPool*. A skála létrehozásához állítsa be, használhatja [az vmss létrehozása](/cli/azure/vmss#az_vmss_create).

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

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

Ahhoz, hogy az alkalmazás átjáró nyilvános IP-címét, használhatja a [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az_network_public_ip_show). Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Biztonságos figyelmeztetés](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Válassza ki a biztonsági figyelmeztetést, ha egy önaláírt tanúsítványt használt elfogadásához **részletek** , majd **nyissa meg a képernyőn látható weblapon**. Ekkor a biztonságos NGINX-webhely a következő példához hasonlóan jelenik meg:

![Az alkalmazás átjáró alap URL-cím tesztelése](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * A hálózat beállítása
> * Hozzon létre egy alkalmazást a tanúsítvány
> * Figyelő és átirányítási szabály hozzáadása
> * Hozzon létre egy virtuálisgép-méretezési állítható be alapértelmezett háttérkészlet


