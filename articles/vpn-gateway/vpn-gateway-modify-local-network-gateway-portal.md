---
title: 'VPN Gateway: az átjáró IP-címének beállításainak módosítása: Azure Portal'
description: Ez a cikk végigvezeti a helyi hálózati átjáró IP-címének előtagjainak a Azure Portal használatával történő módosításán.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864026"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Helyi hálózati átjáró beállításainak módosítása a Azure Portal használatával

A helyi hálózati átjáró AddressPrefix vagy GatewayIPAddress vonatkozó beállítások néha változnak. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszer használatával is módosíthatja, ha a következő listából választ egy másik lehetőséget:

A kapcsolat törlése előtt érdemes lehet letölteni a csatlakoztatott eszközök konfigurációját a megadott PSK beszerzéséhez. Így nem kell újradefiniálni a másik oldalon.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-cím előtagjainak módosítása

Az IP-címek előtagjainak módosításakor a követett lépések attól függnek, hogy a helyi hálózati átjáró rendelkezik-e kapcsolattal.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Átjáró IP-címének módosítása

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. Ha megváltoztatja a nyilvános IP-címet, a követett lépések attól függnek, hogy a helyi hálózati átjáró rendelkezik-e kapcsolódással.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>További lépések

Ellenőrizheti az átjáró-kapcsolatokat. Lásd: [átjáró-kapcsolatok ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).
