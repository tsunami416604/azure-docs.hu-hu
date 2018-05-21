---
title: Hozzon létre egy alkalmazás több webhely-üzemeltetés – Azure parancssori Felülettel |} Microsoft Docs
description: Ismerje meg, amelyen az Azure parancssori felület használatával több hely Alkalmazásátjáró létrehozása.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 7728ade5f84cd97f3ff0cebd0d5545d68d0ebd79
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Hozzon létre egy alkalmazás több helyet üzemeltető az Azure parancssori felület használatával

Az Azure CLI segítségével konfigurálhatja [több webhely tárolása](application-gateway-multi-site-overview.md) létrehozásakor egy [Alkalmazásátjáró](application-gateway-introduction.md). Ebben az oktatóanyagban hoz létre a virtuálisgép-méretezési csoportok használatával háttérkészletek menüpontot. Ezután konfigurálja figyelők és szabályok alapján a tartományok, amelyek a saját győződjön meg arról, hogy a webes forgalom érkezik a készletek a megfelelő kiszolgálókat. Ez az oktatóanyag feltételezi, hogy Ön a tulajdonosa több tartományok és felhasználási mintái *www.contoso.com* és *www.fabrikam.com*.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és útválasztási szabályok létrehozása
> * A háttérkészlet hozzon létre virtuálisgép-méretezési csoportok
> * Hozzon létre egy CNAME rekordot a tartományban

![Többhelyes útválasztási – példa](./media/tutorial-multisite-cli/scenario.png)

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

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

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

### <a name="add-the-backend-pools"></a>A háttér-készletek hozzáadása

Adja hozzá a háttérkészlet nevű *contosoPool* és *fabrikamPool* , amely magában foglalja a háttérkiszolgálók használata szükséges [az hálózati Alkalmazásátjáró címkészlet létrehozása](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

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

Egy figyelő szükséges ahhoz, hogy a forgalom megfelelő irányításához a háttérkészletbe az Alkalmazásátjáró. Ebben az oktatóanyagban a két tartomány hozzon létre két figyelők. Ebben a példában figyelői jönnek létre, a tartományok *www.contoso.com* és *www.fabrikam.com*. 

Adja hozzá a figyelők nevű *contosoListener* és *fabrikamListener* irányíthatja a forgalmat használata szükséges, amely [az hálózati Alkalmazásátjáró http-figyelő létrehozása](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

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

### <a name="add-routing-rules"></a>Adjon hozzá útválasztási szabályokat

Szabályok feldolgozása, amelyben a létrehozásuk sorrendjében, és a forgalom irányítja a rendszer az első szabály, amely megfelel az URL-cím használatával küld az Alkalmazásátjáró. Például ha egy szabályt egy alapszintű figyelő és egy többhelyes figyelő mindkét ugyanazt a portot használó szabály, a szabály a többhelyes figyelőjével szerepelnie kell a szabály a vártnak megfelelően működik az alapvető figyelő ahhoz, hogy a többhelyes szabály előtt. 

Ebben a példában két új szabályt hoz létre, és törölje az alapértelmezett szabály, amely az Alkalmazásátjáró létrehozásakor jött létre. A szabály használatával adhat hozzá [az hálózati Alkalmazásátjáró szabály létrehozása](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

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

## <a name="create-virtual-machine-scale-sets"></a>Hozzon létre virtuálisgép-méretezési csoportok

Ebben a példában három virtuális gép méretezési készlet, amely támogatja a három háttérkészletek az Alkalmazásátjáró létrehozása. A méretezési csoportok az Ön által létrehozott megnevezett *myvmss1*, *myvmss2*, és *myvmss3*. Minden egyes méretezési két virtuálisgép-példánya telepíthető, amely NGINX tartalmazza.

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
  "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Hozzon létre egy CNAME rekordot a tartományban

Nyilvános IP-címmel az Alkalmazásátjáró létrehozása után lekérni a DNS-címét, és hozzon létre egy CNAME rekordot a tartomány segítségével. Használhat [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az_network_public_ip_show) lekérni a DNS-címét az Alkalmazásátjáró. Másolás a *fqdn* a DNSSettings értékének és az legyen az Ön által létrehozott CNAME rekord értékét. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

A-rekordok használata nem ajánlott, mert a VIP módosíthatja az Alkalmazásátjáró újraindításakor.

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

Adjon meg a tartománynevet a böngésző címsorába. Például a http://www.contoso.com.

![Az alkalmazás átjáró contoso hely tesztelése](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Módosítsa a címet a másik tartományban, és az alábbihoz hasonlót kell megjelennie:

![Az alkalmazás átjáró fabrikam hely tesztelése](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és útválasztási szabályok létrehozása
> * A háttérkészlet hozzon létre virtuálisgép-méretezési csoportok
> * Hozzon létre egy CNAME rekordot a tartományban

> [!div class="nextstepaction"]
> [További tudnivalók az Alkalmazásátjáró teendők](application-gateway-introduction.md)