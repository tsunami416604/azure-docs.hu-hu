---
title: 'Útválasztó-alapú Azure VPN-átjáró létrehozása: CLI | Microsoft Docs'
description: Gyorsan megismerheti a CLI-vel VPN-átjáró létrehozása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: f5f62a6bfa1baa205e0496dd901f1f1eef660079
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55698190"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>CLI-vel az útvonalalapú VPN-átjáró létrehozása

Ez a cikk segít gyorsan létrehozhat egy útválasztó-alapú Azure VPN gateway az Azure CLI használatával. VPN-átjáró a helyszíni hálózathoz egy VPN-kapcsolat létrehozása során használatos. Virtuális hálózatok csatlakoztatása VPN gateway használhatja.

A jelen cikkben ismertetett lépések hoz létre virtuális hálózat, alhálózat, egy átjáró-alhálózatot és egy útvonalalapú VPN-átjáró (virtuális hálózati átjáró). A virtuális hálózati átjáró a további létrehozása akár 45 percet is igénybe vehet. Miután az átjáró létrehozása befejeződött, majd létrehozhat kapcsolatokat. Ezeket a lépéseket az Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforrás csoport a [az csoport létrehozása](/cli/azure/group) parancsot. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [az network vnet létrehozása](/cli/azure/network/vnet) parancsot. A következő példában létrehozunk egy nevű virtuális hálózatot **VNet1** a a **EastUS** helye:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Átjáró-alhálózat hozzáadása

Az átjáróalhálózat tartalmazza a fenntartott IP-címek, amelyek a virtuális hálózati átjáró-szolgáltatások használják. Használja az alábbi példák egy átjáró alhálózatának hozzáadása:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Nyilvános IP-cím kérése

VPN-átjáró dinamikusan lefoglalt nyilvános IP-címmel kell rendelkeznie. A nyilvános IP-címet oszt ki, amely hoz létre a virtuális hálózat VPN-átjáróhoz. Használja az alábbi példa egy nyilvános IP-cím:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>A VPN-átjáró létrehozása

Hozza létre a VPN Gateway-t az [az network vnet-gateway create](/cli/azure/group) paranccsal.

Ha ez a parancs használatával futtatja a `--no-wait` paramétert, nem jelenik meg semmilyen visszajelzés vagy kimenet. A `--no-wait` paraméter lehetővé teszi, hogy az átjáró a háttérben kell létrehozni. Ez nem jelenti azt, hogy a VPN-átjáró létrehozása azonnal.

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

### <a name="view-the-public-ip-address"></a>Nyilvános IP-címének megtekintése

Az átjárójához rendelt nyilvános IP-cím megtekintéséhez használja a következő példát:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Az értéket a **IP-cím** mező értéke a VPN-átjáró nyilvános IP-címét.

Példaválasz:

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

Ha már nincs szüksége a létrehozott erőforrásokat, [az csoport törlése](/cli/azure/group) , törölje az erőforráscsoportot. Ez a parancs törli az erőforráscsoportot és a benne lévő összes erőforrást.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>További lépések

Miután az átjáró létrehozása befejeződött, létrehozhat egy kapcsolatot a virtuális hálózat és a egy másik virtuális hálózat között. Vagy hozzon létre egy kapcsolatot a virtuális hálózat és a egy helyszíni hely között.

> [!div class="nextstepaction"]
> [Hozzon létre egy helyek közötti kapcsolat](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Pont – hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Hozzon létre egy kapcsolatot egy másik virtuális hálózathoz](vpn-gateway-vnet-vnet-rm-ps.md)
