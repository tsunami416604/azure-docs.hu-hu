---
title: Hozzon létre egy alkalmazás - Azure CLI |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre olyan átjárót az Azure parancssori felület használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 791cc8bca95fc2264b485c23f30e24254067f513
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201827"
---
# <a name="create-an-application-gateway-using-the-azure-cli"></a>Az Azure parancssori felület használatával Alkalmazásátjáró létrehozása

Az Azure parancssori felület használatával létrehozása vagy kezelése alkalmazásátjárót, a parancssorban vagy parancsfájlokban. A gyors üzembe helyezés bemutatja, hogyan hozzon létre a hálózati erőforrásokhoz, a háttérkiszolgálók, illetve az Alkalmazásátjáró.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Rendszererőforrásokra telepíti, és a parancssori felület helyileg, a gyors üzembe helyezés van szükség, hogy futtassa az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforrás csoport használatával [az csoport létrehozása](/cli/azure/group#az_group_create). Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupAG* a a *eastus* helyét.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozza létre a virtuális hálózati és alhálózati használata [az hálózati vnet létrehozása](/cli/azure/vnet#az_vnet_create). A nyilvános IP cím segítségével létrehozott [létrehozása az hálózati nyilvános ip-](/cli/azure/public-ip#az_public_ip_create).

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

Ebben a példában két virtuális gép az Alkalmazásátjáró háttér-kiszolgálóként használandó hoz létre. Ellenőrizze, hogy az Alkalmazásátjáró sikeresen létrejött-e a virtuális gépeken telepítése NGINX is.

### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Egy cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy „Hello World” Node.js-alkalmazást a Linux rendszerű virtuális gépeken. Az aktuális parancshéjban hozzon létre egy cloud-init.txt nevű fájlt, majd másolja és illessze be a következő konfigurációt a parancshéjba. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

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

A hálózati adapterek létrehozása [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create). Hozza létre a virtuális gépeket az [az vm create](/cli/azure/vm#az_vm_create) paranccsal.

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

Hozzon létre egy alkalmazást átjáró, amely [az hálózati Alkalmazásátjáró létrehozása](/cli/azure/application-gateway#az_application_gateway_create). Alkalmazásátjáró az Azure parancssori felület használatával hoz létre, amikor konfigurációs adatokat, például, sku, és a HTTP-beállításait adja meg. A privát IP-címek a hálózati adapterek az Alkalmazásátjáró háttérkészlethez kiszolgálóként adja hozzá.

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

Az alkalmazás-átjáró hozható létre több percig is eltarthat. Az Alkalmazásátjáró létrehozása után ezeket a szolgáltatásokat, láthatja:

- *appGatewayBackendPool* -Alkalmazásátjáró rendelkeznie kell legalább egy háttér címkészletet.
- *appGatewayBackendHttpSettings* – Megadja, hogy 80-as porton, és olyan HTTP protokollt használja a kommunikációhoz.
- *appGatewayHttpListener* -a társított alapértelmezett figyelő *appGatewayBackendPool*.
- *appGatewayFrontendIP* -hozzárendel *myAGPublicIPAddress* való *appGatewayHttpListener*.
- *Szabály1* - útválasztási szabály társított alapértelmezett *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

A nyilvános IP-cím, az alkalmazás-átjáró használatához [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az_network_public_ip_show). Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, használhatja a [az csoport törlése](/cli/azure/group#az_group_delete) távolítsa el az erőforráscsoportot, Alkalmazásátjáró parancsot, és az összes kapcsolódó erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy erőforráscsoportot, hálózati erőforrásokat és háttérkiszolgálókat hozott létre. Alkalmazásátjáró létrehozásához használt erőforrások majd. További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.

