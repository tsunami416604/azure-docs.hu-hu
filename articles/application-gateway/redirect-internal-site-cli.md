---
title: Belső átirányítás cli használatával
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre egy alkalmazásátjárót, amely átirányítja a belső webes forgalmat a megfelelő készletaz Azure CLI használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 7d37e36a4cdfed462904e2d02871345ad89d7ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074548"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Alkalmazásátjáró létrehozása belső átirányítással az Azure CLI használatával

Az Azure CLI segítségével konfigurálhatja a [webes forgalom átirányítását,](multiple-site-overview.md) amikor létrehoz egy [alkalmazásátjárót.](overview.md) Ebben az oktatóanyagban egy háttérkészletet definiál egy virtuális gép méretezési készlet használatával. Ezután konfigurálja a figyelők és a szabályok alapján a tartományok, amelyek a saját, győződjön meg arról, hogy a webes forgalom érkezik a megfelelő készlet. Ez az oktatóanyag feltételezi, hogy több tartomány tulajdonosa, és példákat használ a *\.www contoso.com* és a www *\.contoso.org.*

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és átirányítási szabály hozzáadása
> * Virtuálisgép-méretezési készlet létrehozása a háttérkészlettel
> * CNAME rekord létrehozása a tartományban

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Ezután hozzáadhatja a *myBackendSubnet* nevű alhálózatot, amelyre a hálózati [virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet)használatával a kiszolgálók háttérkészletéhez szükség van. Hozza létre a *myAGPublicIPAddress* elnevezésű nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal.

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

Az [az network application-gateway create](/cli/azure/network/application-gateway) paranccsal létrehozhatja a *myAppGateway* nevű alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az alkalmazásátjáró a korábban létrehozott *myAGSubnet* alhálózathoz és *myAGPublicIPAddress* IP-címhez lesz rendelve. 

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

Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az alkalmazásátjáró létrehozása után a következő új funkcióit láthatja:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.


## <a name="add-listeners-and-rules"></a>Figyelők és szabályok hozzáadása 

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttérkészlethez. Ebben az oktatóanyagban két figyelőt hoz létre a két tartományhoz. Ebben a példában a hallgatók a *\.www contoso.com* és a *www\.contoso.org*domainjein jönnek létre.

Adja hozzá a forgalom irányításához szükséges háttérfigyelőket az [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) paranccsal.

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

### <a name="add-the-redirection-configuration"></a>Az átirányítási konfiguráció hozzáadása

Adja hozzá a *www\.consoto.org-től* érkező forgalmat küldő átirányítási konfigurációt az alkalmazásátjárówww *\.contoso.com* figyelőjéhez a [hálózati alkalmazás-átjáró átirányítási-konfiguráció létrehozása segítségével.](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create)

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

### <a name="add-routing-rules"></a>Útválasztási szabályok hozzáadása

A szabályok feldolgozása a létrehozásuk sorrendjében történik, és a forgalom az alkalmazásátjárónak küldött URL-címnek megfelelő első szabály használatával történik. Ha például egy adott porton egy alapszintű figyelőt használó és egy többhelyes figyelőt használó szabály is aktív, a többhelyes figyelővel rendelkező szabályt az alapszintű figyelővel rendelkező elé kell venni, hogy a többhelyes szabály a várakozásnak megfelelően működjön. 

Ebben a példában két új szabályt hoz létre, és törli a létrehozott alapértelmezett szabályt.  A szabályt az [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create) paranccsal adhatja hozzá.

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

## <a name="create-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása

Ebben a példában egy virtuálisgép-méretezési csoportot hoz létre, amely támogatja a létrehozott háttérkészletet. A létrehozott méretezési csoport neve *myvmss,* és két virtuálisgép-példányt tartalmaz, amelyekre telepíti az NGINX-et.

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

Futtassa ezt a parancsot a parancsértelmező ablakban:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>CNAME rekord létrehozása a tartományban

Az alkalmazásátjáró nyilvános IP-címmel történő létrehozása után lekérheti a DNS-címet, és a segítségével létrehozhat egy CNAME rekordot a tartományban. Az alkalmazásátjáró DNS-címét az [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) paranccsal kérheti le. Másolja a DNSSettings *fqdn* értékét, és használja a létrehozandó CNAME rekord értékeként. Az A rekordok használata nem javasolt, mivel a virtuális IP-cím változhat az alkalmazásátjáró újraindításakor.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Adja meg a tartománya nevét a böngésző címsorában. Mint például,\/http: /www.contoso.com.

![Contoso webhely tesztelése az alkalmazásátjáróban](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Módosítsa a címet a másik tartomány,\/például http: /www.contoso.org, és látnia kell, hogy\.a forgalom már átirányítva vissza a figyelő a www contoso.com.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és átirányítási szabály hozzáadása
> * Virtuálisgép-méretezési készlet létrehozása a háttérkészlettel
> * CNAME rekord létrehozása a tartományban
