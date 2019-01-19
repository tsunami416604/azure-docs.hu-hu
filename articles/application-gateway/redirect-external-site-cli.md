---
title: Hozzon létre egy application gateway a külső forgalom átirányítása – Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre átjáróalkalmazást, amely átirányítja a belső webes forgalom a megfelelő készlet az Azure CLI használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: bdbaa9154f12c8b66a4c1d801671e7b21756e0f7
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412733"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Külső átirányítás, az Azure CLI használatával az application gateway létrehozása

Az Azure CLI-vel való konfigurálásához használható [webes forgalom átirányítása](multiple-site-overview.md) létrehozásakor egy [az application gateway](overview.md). Ebben az oktatóanyagban konfigurál egy figyelőt és szabályt, amely átirányítja a felhasználókat az application gatewayben külső helyre érkező webes forgalmat.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Figyelő és átirányítási szabály létrehozása
> * Application Gateway létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az [az network vnet create](/cli/azure/network/vnet#az-net) paranccsal. Hozza létre a *myAGPublicIPAddress* elnevezésű nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip#az-network_public_ip_create) paranccsal. Ezek az erőforrások biztosítják az alkalmazásátjáró és a hozzá kapcsolódó erőforrások hálózati kapcsolatát.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az [az network application-gateway create](/cli/azure/network/application-gateway#create) paranccsal létrehozhatja a *myAppGateway* nevű alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az application gateway hozzá van rendelve *myAGSubnet* és *myPublicIPAddress* , amelyet korábban hozott létre. 

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
  --frontend-port 8080 \
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

### <a name="add-the-redirection-configuration"></a>Az átirányítási konfiguráció hozzáadása

Adja hozzá az átirányítási konfiguráció, amely érkező forgalmat küld *www.consoto.org* a figyelőhöz a *www.contoso.com* , az application gateway-t [az network application-gateway átirányítási konfiguráció létrehozása](/cli/azure/network/application-gateway/redirect-config#az-network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Figyelő és útválasztási szabály hozzáadása

Egy figyelő szükséges ahhoz, hogy az application gateway megfelelően irányítja a forgalmat. Hozza létre a használatával [az network application-gateway http-listener létrehozása](/cli/azure/network/application-gateway#az-network_application_gateway_http_listener_create) a frontend-port használatával létrehozott [az network application-gateway frontend-port létrehozása](/cli/azure/network/application-gateway). A szabály a figyelőt, hogy tudja, hova küldhetők a bejövő forgalom szükség. Hozzon létre egy egyszerű szabályt nevű *redirectRule* használatával [az network application-gateway-szabály létrehozása](/cli/azure/network/application-gateway#az-network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használhatja az [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

Megtekintheti az *bing.com* jelennek meg a böngészőben.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> * A hálózat beállítása
> * Figyelő és átirányítási szabály létrehozása
> * Application Gateway létrehozása
