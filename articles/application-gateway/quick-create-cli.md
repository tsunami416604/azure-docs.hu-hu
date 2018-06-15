---
title: Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre az Azure CLI-vel egy Azure Application Gatewayt, amellyel egy háttérkészlet virtuális gépeibe irányíthatja a webes forgalmat.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99c3975c6ab2c7a20dfbab519dae575a2a61465f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32160358"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI

Az Azure Application Gateway szolgáltatással az alkalmazása webes forgalmát adott erőforrásokhoz irányíthatja figyelők portokhoz társításával, szabályok létrehozásával és erőforrások a háttérkészlethez adásával.

Ez az útmutató megmutatja, hogyan hozhat létre gyorsan az Azure CLI segítségével egy olyan Application Gatewayt, amely két virtuális géppel rendelkezik a háttérkészletben. Ezután tesztelheti, hogy megfelelően működik-e.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szüksége. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoportokban mindig létre kell hoznia erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. 

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Az Application Gateway csak akkor tud kommunikálni a többi erőforrással, ha Ön létrehoz neki egy virtuális hálózatot. Ezt megteheti az Application Gateway létrehozásakor is. Ebben a példában két alhálózatot hozunk létre: egyet az Application Gateway számára, egyet pedig a virtuális gépekhez. 

Hozza létre a virtuális hálózatot és az alhálózatot az [az network vnet create](/cli/azure/vnet#az_vnet_create) paranccsal. Hozza létre a nyilvános IP-címet az [az network public-ip create](/cli/azure/public-ip#az_public_ip_create) paranccsal.

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában két virtuális gépet hoz létre, amelyeket az Application Gateway háttérkiszolgálóiként fog használni. 

### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Az Application Gateway sikeres létrehozásának ellenőrzéséhez az NGINX-et is telepíti a virtuális gépekre. Egy cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy „Hello World” Node.js-alkalmazást a Linux rendszerű virtuális gépeken. 

Az aktuális parancshéjban hozzon létre egy cloud-init.txt nevű fájlt, majd másolja és illessze be a következő konfigurációt a parancshéjba. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Hozza létre a hálózati interfészeket az [az network nic create](/cli/azure/network/nic#az_network_nic_create) paranccsal. Hozza létre a virtuális gépeket az [az vm create](/cli/azure/vm#az_vm_create) paranccsal.

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Hozzon létre egy Application Gatewayt az [az network application-gateway create](/cli/azure/application-gateway#az_application_gateway_create) paranccsal. Amikor az Azure CLI-vel hoz létre egy Application Gatewayt, meg kell adnia bizonyos konfigurációs adatokat, például a kapacitást, az SKU-t, valamint a HTTP-beállításokat. A hálózati interfészek privát IP-címeit kiszolgálóként adjuk hozzá az Application Gateway háttérkészletéhez.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Az Application Gateway létrehozása 30 percet is igénybe vehet. Az Application Gateway létrehozása után megtekintheti a funkcióit:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Megszabja, hogy a kommunikáció a 80-as portot és egy HTTP-protokollt használjon.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* elemmel társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – A *myAGPublicIPAddress* elemet hozzárendeli az *appGatewayHttpListener* elemhez.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.

## <a name="test-the-application-gateway"></a>Az Application Gateway tesztelése

Az NGINX telepítése nem kötelező az Application Gateway létrehozásához, ebben az útmutatóban azonban megtettük, hogy ellenőrizzük az Application Gateway sikeres létrehozását. Az Application Gateway nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Az Application Gateway tesztelése](./media/quick-create-cli/application-gateway-nginxtest.png)

A böngésző frissítésekor megjelenik a másik virtuális gép neve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Először tekintse át az Application Gatewayjel együtt létrehozott erőforrásokat, majd ha már nincs rájuk szüksége, az [az group delete](/cli/azure/group#az_group_delete) paranccsal távolítsa el az eszközcsoportot, az Application Gatewayt és a kapcsolódó erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)

