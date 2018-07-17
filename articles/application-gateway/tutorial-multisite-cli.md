---
title: Application gateway létrehozása több hely üzemeltetése – Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre átjáróalkalmazást, amely több helyet az Azure CLI használatával futtatja.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: 2f78cfc9918222fd22c2894b5e920a5c07efe522
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069893"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Hozzon létre egy application gateway több hely üzemeltetése az Azure CLI használatával

Az Azure CLI-vel való konfigurálásához használható [, több webhely üzemeltetése](application-gateway-multi-site-overview.md) létrehozásakor egy [az application gateway](application-gateway-introduction.md). Ebben az oktatóanyagban háttérkészletek virtuálisgép-méretezési csoportok használatával hoz létre. Ezután az Ön tulajdonában lévő tartományok alapján konfigurálhat figyelőket és szabályokat a webes forgalom a készletekben lévő megfelelő kiszolgálókra irányításához. Ez az oktatóanyag feltételezi, hogy Ön több tartománnyal rendelkezik. Példaként a *www.contoso.com* és a *www.fabrikam.com* tartományt használja.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és az útválasztási szabályok létrehozása
> * Virtuálisgép-méretezési csoportok létrehozása a háttérkészletekkel
> * CNAME rekord létrehozása a tartományban

![Többhelyes útválasztási példa](./media/tutorial-multisite-cli/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az [az network vnet create](/cli/azure/network/vnet#az_net) paranccsal. Ezután hozzáadhatja a háttérkiszolgálók számára szükséges *myBackendSubnet* nevű alhálózatot az [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) paranccsal. Hozza létre a *myAGPublicIPAddress* elnevezésű nyilvános IP-címet az [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create) paranccsal.

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

Az [az network application-gateway create](/cli/azure/application-gateway#create) paranccsal létrehozhatja a *myAppGateway* nevű alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az alkalmazásátjáró a korábban létrehozott *myAGSubnet* alhálózathoz és *myAGPublicIPAddress* IP-címhez lesz rendelve. 

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

- *appGatewayBackendPool* – Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* figyelőhöz rendelt alapértelmezett útválasztási szabály.

### <a name="add-the-backend-pools"></a>A háttérkészletek hozzáadása

Adja hozzá a háttérkészletek nevű *contosoPool* és *fabrikamPool* , amely tartalmazza a háttérkiszolgálók használatával szükséges [az network application-gateway-címkészlet létrehozása](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Figyelők hozzáadása

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttérkészlethez. Ebben az oktatóanyagban két figyelőt hoz létre a két tartományhoz. Ebben a példában a figyelőket a *www.contoso.com* és a *www.fabrikam.com* tartományhoz hozzuk létre. 

Adja hozzá a figyelők nevű *contosoListener* és *fabrikamListener* irányíthatja a forgalmat használata szükséges, amely [az network application-gateway http-listener létrehozása](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Útválasztási szabályok hozzáadása

Szabályok létrehozása, amelyben a rendelés feldolgozása, és átirányítja a forgalmat az application gateway az első szabály, amely megfelel az URL-cím használatával küld. Ha például egy adott porton egy alapszintű figyelőt használó és egy többhelyes figyelőt használó szabály is aktív, a többhelyes figyelővel rendelkező szabályt az alapszintű figyelővel rendelkező elé kell venni, hogy a többhelyes szabály a várakozásnak megfelelően működjön. 

Ebben a példában két új szabályt hoz létre, és törli az alkalmazásátjáró létrehozásakor létrehozott alapértelmezett szabályt. A szabályt az [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create) paranccsal adhatja hozzá.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása

Ebben a példában három virtuálisgép-méretezési csoportot hoz létre, amelyek az alkalmazásátjáróban lévő három háttérkészletet támogatják. A létrehozott méretezési csoportok neve *myvmss1*, *myvmss2* és *myvmss3*. Minden méretezési csoport két virtuálisgép-példányt tartalmaz, amelyeken az NGINX-et telepíti.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>CNAME rekord létrehozása a tartományban

Az alkalmazásátjáró nyilvános IP-címmel történő létrehozása után lekérheti a DNS-címet, és a segítségével létrehozhat egy CNAME rekordot a tartományban. Az alkalmazásátjáró DNS-címét az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) paranccsal kérheti le. Másolja a DNSSettings *fqdn* értékét, és használja a létrehozandó CNAME rekord értékeként. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Az A rekordok használata nem javasolt, mivel a virtuális IP-cím változhat az alkalmazásátjáró újraindításakor.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Adja meg a tartománya nevét a böngésző címsorában. Például: http://www.contoso.com.

![Contoso webhely tesztelése az alkalmazásátjáróban](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Változtassa meg a címet a másik tartományára. Ekkor az eredmény a következő példához hasonló:

![Fabrikam webhely tesztelése az alkalmazásátjáróban](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és az útválasztási szabályok létrehozása
> * Virtuálisgép-méretezési csoportok létrehozása a háttérkészletekkel
> * CNAME rekord létrehozása a tartományban

> [!div class="nextstepaction"]
> [Ismerje meg részletesebben az alkalmazásátjárók lehetőségeit](application-gateway-introduction.md)