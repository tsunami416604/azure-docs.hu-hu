---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246172"
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
