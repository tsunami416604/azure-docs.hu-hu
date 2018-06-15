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
ms.openlocfilehash: 865f4a963dfbaa7b8392865f8b0c468734673ba0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197635"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Helyi hálózati átjáró „gatewayIpAddress” értékének módosítása

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. Az átjáró IP-címe megváltoztatható a létező VPN átjárókapcsolat eltávolítása nélkül (ha van ilyen). Az átjáró IP-címének módosításához cserélje ki a „Site2” és a „TestRG1” értékeket a sajátjaira az [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update) parancs használatakor.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Ellenőrizze, hogy az IP-cím megfelelő-e a kimenetben:

```
"gatewayIpAddress": "23.99.222.170",
```