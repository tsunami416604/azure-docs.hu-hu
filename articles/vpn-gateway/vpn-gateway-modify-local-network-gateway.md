---
title: 'Átjáró IP-címének beállításainak módosítása: PowerShell'
description: Ez a cikk végigvezeti a helyi hálózati átjáró IP-címének előtagjainak a PowerShell használatával történő módosításán.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: dbaef6e0c81a9230b24aa1e85e7fdc421444047d
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863995"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Helyi hálózati átjáró beállításainak módosítása a PowerShell-lel

A helyi hálózati átjáró AddressPrefix vagy GatewayIPAddress vonatkozó beállítások néha változnak. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszer használatával is módosíthatja, ha a következő listából választ egy másik lehetőséget:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Előkészületek

Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="ipaddprefix"></a>IP-cím előtagjainak módosítása

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>Átjáró IP-címének módosítása

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Következő lépések

Ellenőrizheti az átjáró-kapcsolatokat. Lásd: [átjáró-kapcsolatok ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).