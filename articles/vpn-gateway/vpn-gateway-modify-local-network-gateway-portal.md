---
title: 'VPN-átjáró: Az átjáró IP-címbeállításainak módosítása: Azure portal'
description: Ez a cikk bemutatja a helyi hálózati átjáró IP-címelőtagainak módosítását az Azure Portal használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864026"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Helyi hálózati átjáró beállításainak módosítása az Azure Portal használatával

Néha megváltoznak a helyi hálózati átjáró AddressPrefix vagy GatewayIPAddress beállításai. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszerrel is módosíthatja, ha az alábbi listából egy másik beállítást választ:

A kapcsolat törlése előtt érdemes letölteni a csatlakozó eszközök konfigurációját a megadott PSK lekéri. Így nem kell újradefiniálnia a másik oldalon.

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-címelőtagok módosítása

Az IP-címelőtagok módosításakor az alábbi lépések attól függnek, hogy a helyi hálózati átjáró rendelkezik-e kapcsolattal.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Az átjáró IP-címének módosítása

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. A nyilvános IP-cím módosításakor az alábbi lépések attól függnek, hogy a helyi hálózati átjáró rendelkezik-e kapcsolattal.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>További lépések

Ellenőrizheti az átjárókapcsolatot. Lásd: [Átjárókapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).
