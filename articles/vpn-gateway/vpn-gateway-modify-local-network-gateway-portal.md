---
title: "Módosítsa a helyi hálózati átjáró IP-cím előtagokat és a VPN-átjáró IP-címe |} Azure |} Portál |} Microsoft Docs"
description: "Ez a cikk végigvezeti az Azure portál használatával helyi hálózati átjáró IP-cím előtagokat módosítása."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: bdd6f90fe97408bd45a72adf58bfdc190207de46
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Az Azure portál használatával a helyi hálózati átjáró beállításainak módosítása

Egyes esetekben a helyi hálózati átjáró címelőtagja vagy GatewayIPAddress beállítások módosítása. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításokat. Emellett módosíthatja ezeket a beállításokat egy másik módszer a egy másik lehetőség kiválasztásával az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>IP-cím előtagokat módosítása

IP-cím előtagokat módosításakor követi függ, hogy rendelkezik-e a helyi hálózati átjáró kapcsolatot.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Az átjáró IP-cím módosítása

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. Ha megváltoztatja a nyilvános IP-címet, akkor kövesse függ, hogy rendelkezik-e a helyi hálózati átjáró kapcsolat.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Következő lépések

Ellenőrizheti a gateway-kapcsolatot. Lásd: [egy átjáró kapcsolat ellenőrzéséhez](vpn-gateway-verify-connection-resource-manager.md).