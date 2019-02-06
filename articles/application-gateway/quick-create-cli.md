---
title: Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre az Azure CLI-vel egy Azure Application Gatewayt, amellyel egy háttérkészlet virtuális gépeibe irányíthatja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0ba18b1ef0ba6c0a73759577c83ab80550baa6f8
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754744"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Gyors útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure CLI-vel

Ez a rövid útmutató bemutatja, hogyan hozhat létre gyorsan egy application gateway a háttérkészlet két virtuális gép az Azure CLI használatával. Ezután tesztelheti, hogy megfelelően működik-e. Az Azure Application Gatewayjel, az alkalmazás webes forgalom meghatározott forrásokhoz való közvetlen: figyelői portok, szabályok létrehozásával és hozzárendelésével hozzáadunk erőforrásokat egy háttérkészlet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori Felületet, futtassa az Azure CLI 2.0.4-es vagy újabb. A verzió megkereséséhez futtassa **az--verzió**. Telepítésekor vagy verziófrissítésekor kapcsolatos információkért lásd: [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoportba foglalhat. Hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az-group-create). 

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Amikor egy virtuális hálózatot hoz létre, az application gateway kommunikálhatnak más erőforrásokkal. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat. Ebben a példában két alhálózattal hoz létre: egyet az application gateway, a másik pedig a virtuális gépek. Az application gateway alhálózatának csak az application Gateway-átjárókon is tartalmazhat. Egyéb erőforrások nem engedélyezettek.

A virtuális hálózatot és alhálózatot létrehozni, használjon [az network vnet létrehozása](/cli/azure/network/vnet#az-network-vnet-create). Futtatás [az network public-ip létrehozása](/cli/azure/network/public-ip) nyilvános IP-cím létrehozásához.

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

Ebben a példában az application gateway számára háttérkiszolgálóiként használja az Azure két virtuális gépet hoz létre. 

### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Telepítse a [NGINX-webkiszolgálót](https://docs.nginx.com/nginx/) az alkalmazás ellenőrzéséhez a virtuális gépek átjáró sikeresen létrejött. A cloud-init konfigurációs fájl használatával telepíti az nginx-et és a egy "Hello World" Node.js-alkalmazás futtatása Linux rendszerű virtuális gépen. Cloud-Init használatával kapcsolatos további információkért lásd: [Cloud-init támogatása az Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

Az Azure Cloud shellben, másolja és illessze be a következő konfigurációs fájlba *cloud-init.txt*. Adja meg *szerkesztő cloud-init.txt* a fájl létrehozásához.

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

Hozza létre a hálózati interfészeket az [az network nic create](/cli/azure/network/nic#az-network-nic-create) paranccsal. A virtuális gépek létrehozására használhatja [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create).

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

Hozzon létre egy application gateway használatával [az network application-gateway létrehozása](/cli/azure/network/application-gateway). Egy application gateway az Azure CLI-vel való létrehozásakor megadhatja a konfigurációs adatokat, például a kapacitás, a Termékváltozat és a HTTP beállításait. Az Azure magánhálózati IP-címet a hálózati adapterek ezt követően hozzáadja az application Gateway háttérkészlet-kiszolgálóként.

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

Az application gateway létrehozása akár 30 percet is igénybe vehet. A létrehozást követően a következő beállításokat is megtekintheti a **beállítások** szakaszában a **az Application gateway** oldalon:

- **appGatewayBackendPool**: Található a **háttérkészletek** lapot. Azt adja meg a szükséges háttérkészlethez.
- **appGatewayBackendHttpSettings**: Található a **HTTP-beállítások** lapot. Meghatározza, hogy az application gateway 80-as portot és a HTTP protokollt használja a kommunikációhoz.
- **appGatewayHttpListener**: Található a **figyelői lap**. Azt adja meg a társított alapértelmezett figyelő **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Található a **előtérbeli IP-konfigurációk** lapot. Rendeli hozzá a *myAGPublicIPAddress* való **appGatewayHttpListener**.
- **rule1**: Található a **szabályok** lapot. Azt adja meg az alapértelmezett útválasztási szabályt, amely társítva van **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az Azure nem szükséges egy NGINX-webkiszolgálót az application gateway létrehozása, telepítése, ebben a rövid, ellenőrizze, hogy az Azure sikeresen létrejött-e az application gateway. Az új application gateway nyilvános IP-címét használja [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

Másolja és illessze be a nyilvános IP-címet a böngésző címsorába.
    
![Az alkalmazásátjáró tesztelése](./media/quick-create-cli/application-gateway-nginxtest.png)

Amikor frissíti a böngészőben, megtekintheti a második virtuális gép nevét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az application gateway segítségével létrehozott erőforrásokat, használja a [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport. Az erőforráscsoport eltávolításával is eltávolítja az application gateway és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure CLI használatával](./tutorial-manage-web-traffic-cli.md)

