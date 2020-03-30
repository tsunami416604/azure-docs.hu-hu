---
title: 'Átjáró IP-címbeállításainak módosítása: PowerShell'
description: Ez a cikk bemutatja a helyi hálózati átjáró IP-címelőbiztosainak módosítását a PowerShell használatával
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: dbaef6e0c81a9230b24aa1e85e7fdc421444047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863995"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Helyi hálózati átjáró beállításainak módosítása a PowerShell-lel

Néha megváltoznak a helyi hálózati átjáró AddressPrefix vagy GatewayIPAddress beállításai. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszerrel is módosíthatja, ha az alábbi listából egy másik beállítást választ:

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené

Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-címelőtagok módosítása

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Az átjáró IP-címének módosítása

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>További lépések

Ellenőrizheti az átjárókapcsolatot. Lásd: [Átjárókapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).