---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 354015930170ca6466b3555c78d211c080232c82
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755992"
---
### <a name="to-view-local-network-gateways"></a>Helyi hálózati átjárók megtekintése

A helyi hálózati átjárók listájának megtekintéséhez használja az [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway) parancsot.

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>A megosztottkulcs-értékek ellenőrzése

Ellenőrizze, hogy a megosztott kulcs megegyezik-e a VPN-eszköze konfigurálásakor használt értékkel. Ha nem, futtassa a kapcsolatot újra az eszközből származó értékkel, vagy frissítse az eszközt a visszaadott értékkel. Az értékeknek meg kell egyezniük. A megosztott kulcs megtekintéséhez használja az [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection) parancsot.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>A VPN Gateway nyilvános IP-címének megtekintése

  A virtuális hálózati átjáró IP-címét az [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) paranccsal keresheti meg. Az olvashatóság érdekében a jelen példa kimenete táblázatos formában jeleníti meg a nyilvános IP-címek listáját.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
