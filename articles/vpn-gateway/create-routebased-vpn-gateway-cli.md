---
title: 'Útválasztó-alapú Azure VPN-átjáró létrehozása: CLI |} Microsoft Docs'
description: Gyorsan megtudhatja, hogyan hozzon létre egy VPN-átjáró parancssori felület használatával
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: d0f4e292c6f5a2725b4a9efe91e78c6e634ea64e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Parancssori felület használatával útvonalalapú VPN-átjáró létrehozása

Ez a cikk segít gyorsan létrehozhat egy útválasztó-alapú Azure VPN gateway az Azure parancssori felület használatával. VPN-átjáró a helyszíni hálózathoz egy VPN-kapcsolat létrehozása során használatos. VPN-átjáró a Vnetek csatlakozhatnak is használhatja.

A cikkben leírt lépéseket hoz létre egy virtuális hálózatot, alhálózatot, egy átjáró-alhálózatot és útválasztó-alapú VPN-átjáró (virtuális hálózati átjáró). A virtuális hálózati átjáró is igénybe vehet, 45 perc vagy több létrehozásához. Miután befejezte az átjáró létrehozása, majd létrehozhat kapcsolatokat. Ezeket a lépéseket az Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk számára szükséges, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A telepített verzió megkereséséhez futtassa `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforrás csoport használata a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózat használatával a [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create) parancsot. Az alábbi példa létrehoz egy virtuális hálózatot nevű **VNet1** a a **EastUS** helye:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Egy átjáró alhálózatának hozzáadása

Az átjáró alhálózatának tartalmazza a fenntartott IP-címek, amelyek a virtuális hálózati átjáró szolgáltatások használják. Az alábbi példák segítségével egy átjáró alhálózatának hozzáadása:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>A nyilvános IP-cím kérése

VPN-átjáró rendelkeznie kell egy dinamikusan kiosztott nyilvános IP-címet. A VPN-átjáró, amely a virtuális hálózat létrehozása a nyilvános IP-címet fogja kiosztani. Az alábbi példát követve egy nyilvános IP-cím kérése:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>A VPN-átjáró létrehozása

Hozza létre a VPN Gateway-t az [az network vnet-gateway create](/cli/azure/group#az_network_vnet_gateway_create) paranccsal.

Ha ez a parancs használatával futtassa a `--no-wait` paraméter, nem jelenik meg a kimeneti vagy bármely visszajelzést. A `--no-wait` paraméter lehetővé teszi, hogy a háttérben létrehozni az átjárót. Ez nem jelenti azt, hogy a VPN-átjáró azonnal létrejött-e.

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

VPN-átjáró is igénybe vehet, 45 perc vagy több létrehozásához.

## <a name="viewgw"></a>A VPN-átjáró megtekintése

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

A válasz ehhez hasonlóan néz ki:

```
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

A nyilvános IP-cím az átjáró rendelt megtekintéséhez használja a következő példa:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

A rendelt érték, amely a **IP-cím** mező értéke a VPN-átjáró nyilvános IP-címét.

Példa egy válasz:

```
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

Ha már nincs szüksége a létrehozott erőforrások, [az csoport törlése](/cli/azure/group#az_group_delete) a csoport törléséhez. Ezzel törli az erőforráscsoportot és a benne található erőforrásokat.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>További lépések

Miután az átjáró létrehozása befejeződött, a kapcsolat a virtuális hálózat és egy másik virtuális hálózat között is létrehozhat. Vagy a virtuális hálózat és egy helyszíni hely közötti kapcsolatot.

> [!div class="nextstepaction"]
> [Pont-pont kapcsolat létrehozása](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Pont – hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Kapcsolatot létesíthet egy másik virtuális hálózatot](vpn-gateway-vnet-vnet-rm-ps.md)