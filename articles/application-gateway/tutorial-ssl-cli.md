---
title: Alkalmazásátjáró létrehozása az SSL leállításával – Azure CLI
description: Megismerheti, hogyan hozhat létre alkalmazásátjárót és adhat hozzá egy tanúsítványt az SSL leállításához az Azure CLI használatával.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 4/27/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 9c52eb99d76253298af858ed8748929874896523
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="tutorial-create-an-application-gateway-with-ssl-termination-using-the-azure-cli"></a>Oktatóanyag: Alkalmazásátjáró létrehozása az SSL leállításával az Azure CLI használatával

Az Azure CLI használatával létrehozhat egy [alkalmazásátjárót](overview.md) egy [SSL leállítási](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) tanúsítvánnyal, amely egy [virtuálisgép-méretezési csoportot](ssl-overview.md) használ háttérkiszolgálókként. Ebben a példában a méretezési csoport két virtuálisgép-példányt tartalmaz, amelyek hozzá lesznek adva az alkalmazásátjáró alapértelmezett háttérkészletéhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Hálózat beállítása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Igény szerint az oktatóanyag az [Azure PowerShell](tutorial-ssl-powershell.md) használatával is elvégezhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles környezetben importálnia kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt. Ebben az oktatóanyagban egy önaláírt tanúsítványt és egy PFX-fájlt hoz létre az openssl paranccsal.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Adjon meg olyan értékeket, amelyek a tanúsítvány szempontjából jelentéssel bírnak. Elfogadhatja az alapértelmezett értékeket.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Adja meg a tanúsítványhoz tartozó jelszót. Ebben a példában az *Azure123456!* jelszót használjuk.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az [az network vnet create](/cli/azure/network/vnet#az_net) paranccsal. Ezután hozzáadhatja a háttérkiszolgálók számára szükséges *myBackendSubnet* nevű alhálózatot az [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) paranccsal. Hozza létre a *myAGPublicIPAddress* elnevezésű nyilvános IP-címet az [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create) paranccsal.

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

Az [az network application-gateway create](/cli/azure/application-gateway#create) paranccsal létrehozhatja az alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. 

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

- *appGatewayBackendPool* – Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* figyelőhöz rendelt alapértelmezett útválasztási szabály.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít az alapértelmezett háttérkészlet számára az alkalmazásátjáróban. A méretezési csoportban lévő virtuális gépek a *myBackendSubnet* alhálózathoz és az *appGatewayBackendPool* készlethez vannak rendelve. A méretezési csoportot az [az vmss create](/cli/azure/vmss#az_vmss_create) paranccsal hozhatja létre.

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/vhorne/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használhatja az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Biztonsági figyelmeztetés](./media/tutorial-ssl-cli/application-gateway-secure.png)

Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget. Ekkor a biztonságos NGINX-webhely a következő példához hasonlóan jelenik meg:

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/tutorial-ssl-cli/application-gateway-nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * Hálózat beállítása
> * Alkalmazásátjáró létrehozása a tanúsítvánnyal
> * Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

> [!div class="nextstepaction"]
> [Több webhelyet üzemeltető alkalmazásátjáró létrehozása](./tutorial-multiple-sites-cli.md)
