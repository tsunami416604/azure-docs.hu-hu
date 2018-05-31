---
title: Webalkalmazási tűzfal engedélyezése – Azure CLI
description: Megismerheti, hogyan korlátozható a webes forgalom webalkalmazási tűzfallal egy alkalmazásátjárón az Azure CLI használatával.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 4/27/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ca00dd3b359d9aff1b987a3bef5b27732678b0a3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356222"
---
# <a name="tutorial-enable-web-application-firewall-using-the-azure-cli"></a>Oktatóanyag – Webalkalmazási tűzfal engedélyezése az Azure CLI használatával

Az [alkalmazásátjáró](overview.md) forgalmát korlátozhatja [webalkalmazási tűzfallal](waf-overview.md) (WAF). A WAF [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-szabályokkal védi az alkalmazást. Ezek a szabályok olyan támadások ellen nyújtanak védelmet, mint az SQL-injektálás, a Cross-Site Scripting támadások és a munkamenet-eltérítések. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hálózat beállítása
> * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
> * Virtuálisgép-méretezési csoport létrehozása
> * Tárfiók létrehozása és diagnosztika konfigurálása

![Példa webalkalmazási tűzfalra](./media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

Igény szerint az oktatóanyag az [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) használatával is elvégezhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy *myResourceGroupAG* nevű Azure-erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

A virtuális hálózat és annak alhálózatai biztosítják az alkalmazásátjáró és az ahhoz tartozó erőforrások hálózati kapcsolatát. Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) és az [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) paranccsal. Hozzon létre egy *myAGPublicIPAddress* nevű nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) paranccsal.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway-with-a-waf"></a>Alkalmazásátjáró létrehozása webalkalmazási tűzfallal

Az [az network application-gateway create](/cli/azure/application-gateway#az_application_gateway_create) paranccsal létrehozhatja a *myAppGateway* nevű alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az alkalmazásátjáró a korábban létrehozott *myAGSubnet* alhálózathoz és *myPublicIPSddress* IP-címhez lesz rendelve.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az alkalmazásátjáró létrehozása után a következő új funkcióit láthatja:

- *appGatewayBackendPool* – Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* figyelőhöz rendelt alapértelmezett útválasztási szabály.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely két kiszolgálót biztosít a háttérkészlet számára az alkalmazásátjáróban. A méretezési csoportban lévő virtuális gépek a *myBackendSubnet* alhálózathoz vannak rendelve. A méretezési csoportot az [az vmss create](/cli/azure/vmss#az_vmss_create) paranccsal hozhatja létre.

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tárfiók létrehozása és diagnosztika konfigurálása

Ebben az oktatóanyagban az alkalmazásátjáró tárfiókban tárolja az adatokat észlelési és megelőzési célra. Az adatok rögzítésére használható a Log Analytics vagy az Event Hub is. 

### <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre egy *myagstore1* nevű tárfiókot az [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) paranccsal.

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption blob
```

### <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

Konfigurálja a diagnosztikát az adatok az ApplicationGatewayAccessLog, az ApplicationGatewayPerformanceLog és az ApplicationGatewayFirewallLog naplóba rögzítéséhez. A `<subscriptionId>` helyére írja be az Ön előfizetés-azonosítóját, majd konfigurálja a diagnosztikát az [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create) paranccsal.

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A hálózat beállítása
> * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
> * Virtuálisgép-méretezési csoport létrehozása
> * Tárfiók létrehozása és diagnosztika konfigurálása

> [!div class="nextstepaction"]
> [Alkalmazásátjáró létrehozása az SSL leállításával](./tutorial-ssl-cli.md)