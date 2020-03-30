---
title: Webes forgalom kezelése – Azure CLI
description: Megismerheti, hogyan hozhat létre egy alkalmazásátjárót egy virtuálisgép-méretezési csoporttal a webes forgalom kezelésére az Azure CLI használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/20/2019
ms.author: victorh
ms.openlocfilehash: 3064def2eac0aaee5c04f7ab736cf539ae372cb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359884"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-the-azure-cli"></a>Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével

Az alkalmazásátjáró kezeli az Ön által fenntartott kiszolgálókra irányuló webes forgalmat, és védelmet biztosít ahhoz. Az Azure CLI segítségével hozzon létre egy [alkalmazásátjárót,](overview.md) amely [egy virtuálisgép-méretezési készletet](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) használ a háttérkiszolgálókhoz. Ebben a példában a méretezési csoport két virtuálisgép-példányt tartalmaz. A méretezési csoport hozzáadódik az alkalmazásátjáró alapértelmezett háttérkészletéhez.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Ha szeretné, ezt az eljárást az Azure PowerShell használatával is [elvégezheti.](tutorial-manage-web-traffic-powershell.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a rövid útmutató megköveteli az Azure CLI 2.0.4-es vagy újabb verziójának futtatását. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az [az network application-gateway create](/cli/azure/network/application-gateway) paranccsal hozza létre a *myAppGateway* alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az alkalmazásátjáró a korábban létrehozott *myAGSubnet* és *myPublicIPAddress* címhez van rendelve. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az alkalmazásátjáró létrehozása után a következő új funkciók jelennek meg:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban. A méretezési csoportban lévő virtuális gépek a *myBackendSubnet* alhálózathoz és az *appGatewayBackendPool* készlethez vannak rendelve. A méretezési csoport létrehozásához használja az [az vmss create](/cli/azure/vmss#az-vmss-create) parancsot.

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

Most telepítheti az NGINX-et a virtuálisgép-méretezési csoportra, hogy tesztelhesse a háttérkészlet HTTP-kapcsolatát.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/tutorial-manage-web-traffic-cli/tutorial-nginxtest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>További lépések

[Webes forgalom korlátozása webalkalmazási tűzfallal](./tutorial-restrict-web-traffic-cli.md)
