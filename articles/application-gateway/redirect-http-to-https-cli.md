---
title: HTTP-RŐL HTTPS-re történő átirányítás CLI használatával
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre egy alkalmazásátjárót, és hogyan adhat hozzá tanúsítványt a TLS-megszüntetéshez az Azure CLI használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: victorh
ms.openlocfilehash: 6bf8f3b7bfb446db78f0c97a246977fec6cd54cb
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312148"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Alkalmazásátjáró létrehozása HTTP-HTTPS átirányítással az Azure CLI használatával

Az Azure CLI segítségével létrehozhat egy [alkalmazásátjárót](overview.md) a TLS/SSL-végződési tanúsítvánnyal. Az útválasztási szabály segítségével átirányítják a HTTP-forgalmat az alkalmazásátjáró HTTPS-portjára. Ebben a példában egy [virtuálisgép-méretezési csoportot](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) is létrehozhat az alkalmazásátjáró háttérkészletéhez, amely két virtuálisgép-példányt tartalmaz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Hálózat beállítása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * Figyelő és átirányítási szabály hozzáadása
> * Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles használatra egy megbízható szolgáltató által aláírt érvényes tanúsítványt kell importálnia. Ebben az oktatóanyagban egy önaláírt tanúsítványt és egy PFX-fájlt hoz létre az openssl paranccsal.

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Adjon meg olyan értékeket, amelyek a tanúsítvány szempontjából jelentéssel bírnak. Elfogadhatja az alapértelmezett értékeket.

```console
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Adja meg a tanúsítványhoz tartozó jelszót. Ebben a példában az *Azure123456!* jelszót használjuk.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Ezután hozzáadhatja a háttérkiszolgálók számára szükséges *myBackendSubnet* nevű alhálózatot az [az network vnet subnet create](/cli/azure/network/vnet/subnet) paranccsal. Hozza létre a *myAGPublicIPAddress* elnevezésű nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal.

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

Az [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) paranccsal létrehozhatja a *myAppGateway* nevű alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. 

Az alkalmazásátjáró a korábban létrehozott *myAGSubnet* alhálózathoz és *myAGPublicIPAddress* IP-címhez lesz rendelve. Ebben a példában társítja a létrehozott tanúsítványt és annak jelszavát az alkalmazásátjáró létrehozásakor. 

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

 Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az alkalmazásátjáró létrehozása után a következő új funkcióit láthatja:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.

## <a name="add-a-listener-and-redirection-rule"></a>Figyelő és átirányítási szabály hozzáadása

### <a name="add-the-http-port"></a>A HTTP-port hozzáadása

Használhatja [a hálózati alkalmazás-átjáró frontend-port létrehozása a HTTP-port](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create) hozzáadása az alkalmazás átjáróhoz.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>A HTTP-figyelő hozzáadása

Használhatja [a hálózati alkalmazás-átjáró http-figyelő létrehozása](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) a *myListener* nevű figyelő hozzáadása az alkalmazásátjáróhoz.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Az átirányítási konfiguráció hozzáadása

Adja hozzá a HTTP-HTTPS átirányítási konfigurációt az alkalmazásátjáróhoz az [az hálózati alkalmazás-átjáró átirányítás-konfiguráció létrehozása használatával.](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create)

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

### <a name="add-the-routing-rule"></a>Útválasztási szabály hozzáadása

Adja hozzá a *rule2* nevű útválasztási szabályt az átirányítási konfigurációval az alkalmazásátjáróhoz a [hálózati alkalmazás-átjáró szabály létrehozása használatával.](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create)

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

Ebben a példában egy *myvmss* nevű virtuálisgép-méretezési készletet hoz létre, amely az alkalmazásátjáró háttérkészletének kiszolgálóit biztosítja. A méretezési csoportban lévő virtuális gépek a *myBackendSubnet* alhálózathoz és az *appGatewayBackendPool* készlethez vannak rendelve. A méretezési csoportot az [az vmss create](/cli/azure/vmss#az-vmss-create) paranccsal hozhatja létre.

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használhatja az [az network public-ip show](/cli/azure/network/public-ip) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Biztonsági figyelmeztetés](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget. Ekkor a biztonságos NGINX-webhely a következő példához hasonlóan jelenik meg:

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Hálózat beállítása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * Figyelő és átirányítási szabály hozzáadása
> * Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel


