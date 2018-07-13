---
title: A helyi hálózati átjáró IP-címelőtagokat, és a VPN-átjáró IP-cím módosítása |} Azure |} Portál |} A Microsoft Docs
description: Ez a cikk végigvezeti az Azure portal használatával helyi hálózati átjáró IP-címelőtagjainak módosítása.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: bdd6f90fe97408bd45a72adf58bfdc190207de46
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651936"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Az Azure portal használatával a helyi hálózati átjáró beállításainak módosítása

Néha AddressPrefix vagy GatewayIPAddress helyi hálózati átjáró beállításainak módosítása Ez a cikk bemutatja, hogyan lehet a helyi hálózati átjáró beállításainak módosítása. Ezeket a beállításokat egy másik lehetőség kiválasztásával az alábbi listából egy másik módszer a is módosíthatja:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>IP-címelőtagjainak módosítása

Amikor IP-címelőtagjainak módosítása, a lépések függ a helyi hálózati átjáró rendelkezik-e a kapcsolat.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Átjáró IP-címének módosítása

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót. Amikor módosítja a nyilvános IP-címet, a lépések függ a helyi hálózati átjáró rendelkezik-e a kapcsolat.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>További lépések

Ellenőrizheti, hogy az átjárókapcsolat. Lásd: [átjárókapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).