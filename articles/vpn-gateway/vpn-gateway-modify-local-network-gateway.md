---
title: A helyi hálózati átjáró IP-címelőtagokat, és a VPN-átjáró IP-cím módosítása |} Azure |} PowerShell |} A Microsoft Docs
description: Ez a cikk végigvezeti a PowerShell-lel helyi hálózati átjáró IP-címelőtagjainak módosítása
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 923c478909e0393f36da06802023b3f40ee63a3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419140"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Helyi hálózati átjáró beállításainak módosítása a PowerShell-lel

Néha AddressPrefix vagy GatewayIPAddress helyi hálózati átjáró beállításainak módosítása Ez a cikk bemutatja, hogyan lehet a helyi hálózati átjáró beállításainak módosítása. Ezeket a beállításokat egy másik lehetőség kiválasztásával az alábbi listából egy másik módszer a is módosíthatja:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Előkészületek

Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="ipaddprefix"></a>IP-címelőtagjainak módosítása

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>Átjáró IP-címének módosítása

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>További lépések

Ellenőrizheti, hogy az átjárókapcsolat. Lásd: [átjárókapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).