---
title: 'Útvonalalapú Azure VPN-átjáró létrehozása: CLI'
description: Gyorsan megtudhatja, hogyan hozhat létre VPN-átjárót a CLI használatával
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: 121790fce220874babedf67cd72471caa7e92ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241090"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Útvonalalapú VPN-átjáró létrehozása a CLI használatával

Ez a cikk segít gyorsan létrehozni egy útvonal-alapú Azure VPN-átjáró az Azure CLI használatával. A VPN-átjáró a helyszíni hálózattal létesített VPN-kapcsolat létrehozásakor használatos. Vpn-átjárót is használhat a virtuális hálózatok csatlakoztatásához.

A cikkben ismertetett lépések virtuális hálózatot, alhálózatot, átjáró-alhálózatot és útvonalalapú VPN-átjárót (virtuális hálózati átjárót) hoznak létre. A virtuális hálózati átjáró létrehozása akár 45 percet is igénybe vehet. Miután az átjáró létrehozása befejeződött, ezután kapcsolatokat hozhat létre. Ezek a lépések Azure-előfizetést igényelnek. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az hálózati virtuális hálózat create](/cli/azure/network/vnet) paranccsal. A következő példa létrehoz egy Virtuális hálózat nevű **Virtuális hálózat1** az **EastUS** helyen:

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Átjáróalhálózat hozzáadása

Az átjáró alhálózata tartalmazza a virtuális hálózati átjárószolgáltatások által használt fenntartott IP-címeket. Az alábbi példák segítségével vegyen fel átjáró-alhálózatot:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Nyilvános IP-cím kérése

A VPN-átjárónak dinamikusan lefoglalt nyilvános IP-címmel kell rendelkeznie. A nyilvános IP-cím a virtuális hálózathoz létrehozott VPN-átjáróhoz lesz hozzárendelve. Az alábbi példában nyilvános IP-címet kérhet:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>A VPN-átjáró létrehozása

Hozza létre a VPN Gateway-t az [az network vnet-gateway create](/cli/azure/group) paranccsal.

Ha ezt a parancsot `--no-wait` a paraméter használatával futtatja, nem jelenik meg semmilyen visszajelzés vagy kimenet. A `--no-wait` paraméter lehetővé teszi az átjáró létrehozását a háttérben. Ez nem jelenti azt, hogy a VPN-átjáró azonnal létrejön.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

A VPN-átjáró létrehozása több mint 45 percet is igénybe vehet.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>A VPN-átjáró megtekintése

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

A válasz így néz ki:

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>A nyilvános IP-cím megtekintése

Az átjáróhoz rendelt nyilvános IP-cím megtekintéséhez használja a következő példát:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Az **ipAddress** mezőhöz társított érték a VPN-átjáró nyilvános IP-címe.

Példaválasz:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott erőforrásokra, az [az csoport törlésével](/cli/azure/group) törölheti az erőforráscsoportot. Ez a parancs törli az erőforráscsoportot és a benne lévő összes erőforrást.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>További lépések

Miután az átjáró létrejötte befejeződött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik virtuális hálózat között. Vagy hozzon létre egy kapcsolatot a virtuális hálózat és a helyszíni hely között.

> [!div class="nextstepaction"]
> [Helyek közötti kapcsolat létrehozása](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Pont–hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Kapcsolat létrehozása másik virtuális hálózattal](vpn-gateway-vnet-vnet-rm-ps.md)
