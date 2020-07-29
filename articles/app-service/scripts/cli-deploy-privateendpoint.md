---
title: 'CLI: privát végpont üzembe helyezése a webalkalmazáshoz az Azure CLI-vel'
description: Ismerje meg, hogyan helyezhet üzembe privát végpontot a webalkalmazáshoz az Azure CLI használatával
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 566307581b49922b9d47936f64beea73715f63ba
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028408"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>App Service alkalmazás létrehozása és privát végpont üzembe helyezése az Azure CLI-vel

Ez a példa egy alkalmazást hoz létre App Service a kapcsolódó erőforrásokkal együtt, majd üzembe helyez egy privát végpontot.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verzióját kell használnia. A telepített verziójának megkereséséhez futtassa a parancsot `az --version` . További információ: az [Azure CLI telepítése](/cli/azure/install-azure-cli) a telepítéshez vagy a frissítéshez.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforrások létrehozása előtt létre kell hoznia egy erőforráscsoportot a webalkalmazás, a Virtual Network és más hálózati összetevők üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *francecentral* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Létre kell hoznia egy App Service tervet a webalkalmazás üzemeltetéséhez.
Hozzon létre egy App Service tervet az [az appservice Plan Create](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)paranccsal.
Ez a példa létrehoz egy *myAppServicePlan* nevű app Service tervet a *Francecentral* helyen a *P1V2* SKU-val és csak egy feldolgozóval: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Most, hogy rendelkezik egy App Service csomaggal, üzembe helyezhet egy webalkalmazást.
Hozzon létre egy webalkalmazást a következővel: [az appservice Plan Create] (/CLI/Azure/WebApp? View = Azure-CLI-Latest # az-WebApp-Create.
Ez a példa egy *webhelynév* nevű webalkalmazást hoz létre a *myAppServicePlan* nevű tervben.

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Hozzon létre egy Virtual Network az [az Network vnet Create](/cli/azure/network/vnet)paranccsal. Ez a példa egy *myVNet* nevű alapértelmezett Virtual Network hoz létre egy *mySubnet*nevű alhálózattal:

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Az alhálózat konfigurálása 

A magánhálózati végpontok hálózati házirendjeinek letiltásához frissítenie kell az alhálózatot. Frissítsen egy *mySubnet* nevű alhálózati konfigurációt az [az Network vnet subnet Update paranccsal](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>A magánhálózati végpont létrehozása

Hozza létre a webes alkalmazáshoz tartozó privát végpontot az [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint)paranccsal. Ez a példa létrehoz egy *myPrivateEndpoint* nevű privát végpontot a VNet *MyVNet* az alhálózat *mySubnet* egy *MyConnectionName* nevű kapcsolattal a My Web App/Subscriptions/SubscriptionID/resourceGroups/myResourceGroup/Providers/Microsoft.Web/Sites/myWebApp erőforrás-azonosítójával, a Group paraméter a webalkalmazás-típushoz tartozó *helyek* . 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>A privát zóna konfigurálása

Végül létre kell hoznia egy *privatelink.azurewebsites.net* nevű privát DNS-zónát a VNet a WEBALKALMAZÁS DNS-nevének feloldásához.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>További lépések

- Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).
- További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
