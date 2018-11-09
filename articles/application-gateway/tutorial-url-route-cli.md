---
title: Oktatóanyag – Webes forgalom irányítása URL-cím alapján – Azure CLI
description: Ez az oktatóanyag azt ismerteti, hogyan lehet a webes forgalmat az URL-cím alapján adott méretezhető kiszolgálókészletekre irányítani az Azure CLI segítségével.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 68532ec4ae7e6d6b496ece8d08755555f756a60e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413451"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Oktatóanyag – Webes forgalom irányítása URL-cím alapján az Azure CLI használatával

A webes forgalmat felügyelő rendszergazdaként a feladatai közé tartozik annak a biztosítása, hogy az ügyfelek és a felhasználók minél gyorsabban érhessék el a szükséges információkat. Ennek az egyik módja, ha a különböző típusú webes forgalmakat különböző kiszolgáló-erőforrásokra irányítja. Ez az oktatóanyag azt mutatja be, hogyan állíthatja be és konfigurálhatja az Azure CLI-vel az Application Gateway-útválasztást az alkalmazásból érkező különböző típusú adatforgalmakhoz. Az útválasztás ezután URL-cím alapján különböző kiszolgálókészletekre irányítja a forgalmat.

![URL-útválasztási példa](./media/tutorial-url-route-cli/scenario.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Erőforráscsoport létrehozása a szükséges hálózati erőforrások számára
> * Hálózati erőforrások létrehozása
> * Alkalmazásátjáró létrehozása az alkalmazásból érkező forgalom számára
> * Kiszolgálókészletek és útválasztásai szabályok meghatározása a különböző típusú forgalmakhoz
> * Méretezési csoport létrehozása az egyes készletekhez azok automatikus méretezésének megvalósításához
> * Teszt futtatása annak ellenőrzéséhez, hogy a forgalom különféle típusai a megfelelő készletekre irányulnak-e

Igény szerint az oktatóanyag az [Azure PowerShell](tutorial-url-route-powershell.md) vagy az [Azure Portal](create-url-route-portal.md) használatával is elvégezhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az `az group create` paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az `az network vnet create` paranccsal. Ezután adja hozzá a háttérkiszolgálók által használt *myBackendSubnet* nevű alhálózatot az `az network vnet subnet create` paranccsal. Hozza létre a *myAGPublicIPAddress* nevű nyilvános IP-címet az `az network public-ip create` paranccsal.

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

## <a name="create-the-app-gateway-with-a-url-map"></a>Az alkalmazásátjáró létrehozása URL-címleképezéssel

Az `az network application-gateway create` paranccsal hozzon létre egy alkalmazásátjárót *myAppGateway* néven. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az alkalmazásátjáró a korábban létrehozott *myAGSubnet* alhálózathoz és *myAGPublicIPAddress* IP-címhez lesz rendelve.

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

 Az alkalmazásátjáró létrehozása több percig is eltarthat. Az alkalmazásátjáró létrehozását követően a következő új funkciók láthatóak:


|Szolgáltatás  |Leírás  |
|---------|---------|
|appGatewayBackendPool     |Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel.|
|appGatewayBackendHttpSettings     |Megszabja, hogy a kommunikációhoz a rendszer egy HTTP-protokollt és a 80-as portot használja.|
|appGatewayHttpListener     |Az appGatewayBackendPool készlethez társított alapértelmezett figyelő.|
|appGatewayFrontendIP     |Hozzárendeli a myAGPublicIPAddress IP-címet az appGatewayHttpListener figyelőhöz.|
|rule1     |Az appGatewayHttpListener figyelőhöz rendelt alapértelmezett útválasztási szabály.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Kép- és videó-háttérkészletek, valamint egy port hozzáadása

Adja hozzá az alkalmazásátjáróhoz az *imagesBackendPool* és a *videoBackendPool* nevű háttérkészleteket az `az network application-gateway address-pool create` paranccsal. Adja hozzá az előtérportot a készletekhez az `az network application-gateway frontend-port create` paranccsal.

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

### <a name="add-a-backend-listener"></a>Háttérfigyelő hozzáadása

Adja hozzá a forgalom irányításához szükséges *backendListener* nevű háttérfigyelőt az `az network application-gateway http-listener create` paranccsal.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>URL-útvonaltérkép hozzáadása

Az URL-útvonaltérképek biztosítják, hogy adott URL-címek adott háttérkészletekre legyenek irányítva. Hozza létre az *imagePathRule* és a *videoPathRule* nevű URL-útvonaltérképeket az `az network application-gateway url-path-map create` és az `az network application-gateway url-path-map rule create` paranccsal.

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

### <a name="add-a-routing-rule"></a>Útválasztási szabály hozzáadása

Az útválasztási szabály az URL-címtérképeket a létrehozott figyelőhöz társítja. Adjon hozzá egy *rule2* nevű szabályt az `az network application-gateway rule create` paranccsal.

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

## <a name="create-vm-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása

Ebben az oktatóanyagban három virtuálisgép-méretezési csoportot hoz létre, amelyek a három létrehozott háttérkészletet támogatják. A *myvmss1*, *myvmss2* és *myvmss3* nevű méretezési csoportokat hozza létre. Minden méretezési csoport két virtuálisgép-példányt tartalmaz, amelyeken az NGINX-et telepíti.

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
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használja az az network public-ip show parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* vagy *http://40.121.222.19:8080/video/test.htm*.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Módosítsa az URL-címet a http://&lt;ip-cím&gt;:8080/images/test.html értékre, és az &lt;ip-cím&gt; helyére írja be a saját IP-címét. Az alábbi példához hasonlónak kell megjelennie:

![Képek URL-címének tesztelése az alkalmazásátjáróban](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Módosítsa az URL-címet a http://&lt;ip-cím&gt;:8080/video/test.html értékre, és az &lt;ip-cím&gt; helyére írja be a saját IP-címét. Az alábbi példához hasonlónak kell megjelennie.

![Videók URL-címének tesztelése az alkalmazásátjáróban](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásátjáró létrehozása URL-alapú átirányítással](./tutorial-url-redirect-cli.md)
