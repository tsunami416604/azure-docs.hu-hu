---
title: 'Gyors útmutató: webes forgalom közvetlen továbbítása a CLI használatával'
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre Azure-Application Gateway az Azure CLI használatával, amely egy háttér-készletben lévő virtuális gépekre irányítja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: de1f30d949509ab48999d0080ccc4df74321ce01
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075201"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI

Ez a rövid útmutató bemutatja, hogyan hozhat létre Application Gatewayt az Azure CLI használatával.  Az Application Gateway létrehozása után tesztelje, hogy megfelelően működik-e. Az Azure Application Gateway használatával a figyelőket a portokhoz, a szabályok létrehozásához és az erőforrások háttér-készlethez való hozzáadásához irányíthatja az alkalmazás webes forgalmát adott erőforrásokra. Ez a cikk egy egyszerű telepítőt használ egy nyilvános előtéri IP-címmel, egy alapszintű figyelővel, amely egyetlen helyet üzemeltet az Application gatewayben, két, a háttér-készlethez használt virtuális gépet, valamint egy alapszintű kérelem-útválasztási szabályt.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-cli"></a>Azure CLI

Ha a parancssori felület helyi telepítését és használatát választja, futtassa az Azure CLI 2.0.4 vagy újabb verzióját. A verzió megkereséséhez futtassa az **az--Version**parancsot. További információ a telepítéséről és frissítéséről: az [Azure CLI telepítése]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Erőforráscsoport

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal. 

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Szükséges hálózati erőforrások 

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra.  Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.  Létrehozhat egy új alhálózatot Application Gatewayhoz, vagy használhat egy meglévőt is. Ebben a példában két alhálózatot hoz létre ebben a példában: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára. A Application Gateway előtérbeli IP-címét a használati esetnek megfelelően lehet nyilvános vagy privátként beállítani. Ebben a példában egy nyilvános előtérbeli IP-címet választ.

A virtuális hálózat és az alhálózat létrehozásához használja az [az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)lehetőséget. Futtassa az [az Network Public-IP Create](/cli/azure/network/public-ip) parancsot a nyilvános IP-cím létrehozásához.

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

### <a name="backend-servers"></a>Háttér-kiszolgálók

A háttérrendszer rendelkezhet hálózati adapterekkel, virtuálisgép-méretezési csoportokkal, nyilvános IP-címekkel, belső IP-címekkel, teljes tartománynévvel (FQDN) és több-bérlős háttérrel, például Azure App Service. Ebben a példában két virtuális gépet hoz létre, amelyeket háttér-kiszolgálóként használ az Application Gateway számára. Az IIS-t a virtuális gépeken is telepítheti az Application Gateway teszteléséhez.

#### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Telepítse az [NGINX webkiszolgálót](https://docs.nginx.com/nginx/) a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e. Egy Cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy ""Helló világ!"alkalmazás" Node. js-alkalmazást Linux rendszerű virtuális gépen. A Cloud-init szolgáltatással kapcsolatos további információkért lásd: [Cloud-init támogatás az Azure-beli virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

A Azure Cloud Shell másolja és illessze be a következő konfigurációt egy *Cloud-init. txt*nevű fájlba. A fájl létrehozásához írja be a *Cloud-init. txt szerkesztőt* .

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

Hozza létre a hálózati interfészeket az [az network nic create](/cli/azure/network/nic#az-network-nic-create) paranccsal. A virtuális gépek létrehozásához használja az [az VM Create](/cli/azure/vm#az-vm-create)lehetőséget.

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

Hozzon létre egy Application Gateway [-t az az Network Application-Gateway Create](/cli/azure/network/application-gateway)paranccsal. Amikor az Azure CLI-vel hoz létre egy Application Gateway-t, meg kell adnia a konfigurációs adatokat, például a kapacitást, az SKU-t és a HTTP-beállításokat. Az Azure Ezután hozzáadja a hálózati adapterek magánhálózati IP-címeit az Application Gateway háttérbeli készletében lévő kiszolgálóként.

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

Az Azure az Application Gateway létrehozásához akár 30 percet is igénybe vehet. A létrehozás után a következő beállításokat tekintheti meg az **Application Gateway** oldal **Beállítások** szakaszában:

- **appGatewayBackendPool**: a háttér- **készletek** lapon található. Meghatározza a szükséges háttérrendszer-készletet.
- **appGatewayBackendHttpSettings**: a http- **Beállítások** lapon található. Azt határozza meg, hogy az Application Gateway a 80-es portot és a HTTP protokollt használja a kommunikációhoz.
- **appGatewayHttpListener**: a **figyelők oldalon**található. Meghatározza a **appGatewayBackendPool**társított alapértelmezett figyelőt.
- **appGatewayFrontendIP**: az ELŐTÉRI **IP-konfigurációk** lapon található. A *myAGPublicIPAddress* a **appGatewayHttpListener**-hez rendeli hozzá.
- **rule1**: a **szabályok** lapon található. Meghatározza a **appGatewayHttpListener**társított alapértelmezett útválasztási szabályt.

## <a name="test-the-application-gateway"></a>Az Application Gateway tesztelése

Bár az Azure nem igényel NGINX-webkiszolgálót az Application Gateway létrehozásához, ezt a rövid útmutatóban telepítette annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Application Gatewayt. Az új Application Gateway nyilvános IP-címének lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)lehetőséget. 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Másolja és illessze be a nyilvános IP-címet a böngésző címsorába.
    
![Az Application Gateway tesztelése](./media/quick-create-cli/application-gateway-nginxtest.png)

A böngésző frissítésekor a második virtuális gép nevét kell megjelennie. Ez azt jelzi, hogy az Application Gateway sikeresen létrejött, és csatlakozhat a háttérrel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával az Application Gateway és az ahhoz kapcsolódó összes erőforrást is eltávolítja.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)

