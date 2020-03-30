---
title: 'Rövid útmutató: Közvetlen webes forgalom cli használatával'
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan használhatja az Azure CLI-t egy Azure Application Gateway létrehozásához, amely a webes forgalmat egy háttérkészletben lévő virtuális gépekre irányítja.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f60b26756c0affffbd45c8596fdf73d11ffa8e81
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239515"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI

Ebben a rövid útmutatóban az Azure CLI használatával hozzon létre egy alkalmazásátjárót. Ezután tesztelje, hogy megfelelően működik-e. 

Az alkalmazásátjáró az alkalmazás webforgalmát egy háttérkészlet ben lévő erőforrásokhoz irányítja. A figyelők portokhoz rendelhető, szabályokat hozhat létre, és erőforrásokat adhat hozzá egy háttérkészlethez. Az egyszerűség kedvéért ez a cikk egy egyszerű beállítást használ egy nyilvános előtér-IP-cím, egy alapszintű figyelő egyetlen hely üzemeltetéséhez az alkalmazásátjárón, egy alapvető kérelem-útválasztási szabályt és két virtuális gépet a háttérkészletben.

Ezt a rövid útmutatót az [Azure PowerShell](quick-create-powershell.md) vagy az Azure Portal használatával is elvégezheti. [Azure portal](quick-create-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Az Azure CLI 2.0.4-es vagy újabb verziója](/cli/azure/install-azure-cli) (ha az Azure CLI-t helyileg futtatja).

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt meg egy erőforráscsoporthoz. Erőforráscsoport létrehozása a `az group create`használatával. 

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, virtuális hálózatra van szüksége.  Az alkalmazásátjáró alhálózata csak alkalmazásátjárókat tartalmazhat. Más erőforrások nem engedélyezettek.  Létrehozhat egy új alhálózatot az Application Gateway számára, vagy használhat egy meglévőt. Ebben a példában két alhálózatot hoz létre: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz. Az alkalmazásátjáró előtér-IP-címét beállíthatja nyilvánosnak vagy privátnak a használati esetnek hasonlóan. Ebben a példában nyilvános előtér-IP-címet kell választania.

A virtuális hálózat és az `az network vnet create`alhálózat létrehozásához használja a használatát. Futtassa `az network public-ip create` a nyilvános IP-cím létrehozásához.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>Háttérkiszolgálók létrehozása

A háttér-háttérrendszerek rendelkezhetnek hálózati adapterek, virtuálisgép-méretezési készletek, nyilvános IP-k, belső IP-k, teljesen minősített tartománynevek (FQDN) és több-bérlős háttér-rendszerek, például az Azure App Service. Ebben a példában két virtuális gépet hoz létre, amelyek az alkalmazásátjáró háttérkiszolgálóiként használhatók. Az IIS-t a virtuális gépekre is telepíti az alkalmazásátjáró teszteléséhez.

#### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Telepítse az NGINX webkiszolgálót a virtuális gépekre az alkalmazásátjáró sikeres létrehozásának ellenőrzéséhez. A felhőalapú init konfigurációs fájl segítségével telepítheti az NGINX-et, és futtathat egy "Hello World" Node.js alkalmazást egy Linux virtuális gépen. A felhőalapú initről további információt az [Azure-beli virtuális gépek felhőalapú init-támogatása című témakörben talál.](../virtual-machines/linux/using-cloud-init.md)

Az Azure Cloud Shellben másolja és illessze be a következő konfigurációt egy *cloud-init.txt*nevű fájlba. A fájl létrehozásához írja be a *editor cloud-init.txt fájlt.*

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

Hozza létre a `az network nic create`hálózati adaptereket a segítségével. A virtuális gépek létrehozásához `az vm create`használja a használatát.

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

Alkalmazásátjáró létrehozása `az network application-gateway create`a használatával. Amikor létrehoz egy alkalmazásátjárót az Azure CLI-vel, megadhatja a konfigurációs adatokat, például a kapacitást, a termékváltozatot és a HTTP-beállításokat. Az Azure ezután hozzáadja a hálózati adapterek privát IP-címét az alkalmazásátjáró háttérkészletében lévő kiszolgálókként.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Az Azure-nak akár 30 percet is igénybe vehet az alkalmazásátjáró létrehozása. Létrehozása után az **alkalmazásátjáró** lap **Beállítások** szakaszában megtekintheti a következő beállításokat:

- **appGatewayBackendPool**: A **háttérkészletek** lapján található. Megadja a szükséges háttérkészletet.
- **appGatewayBackendHttpSettings**: A **HTTP-beállítások** lapon található. Azt adja meg, hogy az alkalmazásátjáró a 80-as portot és a HTTP protokollt használja a kommunikációhoz.
- **appGatewayHttpListener**: A **Hallgatók oldalon**található. Itt adható meg az **appGatewayBackendPool**szolgáltatáshoz társított alapértelmezett figyelő.
- **appGatewayFrontendIP**: A **Frontend IP-konfigurációk** lapján található. A *myAGPublicIPAddress címet* rendeli hozzá az **appGatewayHttpListener programhoz.**
- **1.** szabály : A **Szabályok** oldalon található. Megadja az **appGatewayHttpListener**protokollhoz társított alapértelmezett útválasztási szabályt.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az Azure nem igényel NGINX webkiszolgálót az alkalmazásátjáró létrehozásához, ebben a rövid útmutatóban telepítette azt annak ellenőrzésére, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót. Az új alkalmazásátjáró nyilvános IP-címének `az network public-ip show`lekért kérelméhez használja a használatát. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Másolja és illessze be a nyilvános IP-címet a böngésző címsorába.
    
![Az alkalmazásátjáró tesztelése](./media/quick-create-cli/application-gateway-nginxtest.png)

A böngésző frissítésekor meg kell jelennie a második virtuális gép nevét. Ez azt jelzi, hogy az alkalmazásátjáró sikeresen létrejött, és csatlakozhat a háttérrendszerhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az alkalmazásátjáróval létrehozott erőforrásokra, a `az group delete` paranccsal törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor az alkalmazásátjárót és annak összes kapcsolódó erőforrását is törli.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)

