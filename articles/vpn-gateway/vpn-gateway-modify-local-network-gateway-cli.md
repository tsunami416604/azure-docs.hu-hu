---
title: 'VPN-átjáró: Átjáró IP-címbeállításainak módosítása: Azure CLI'
description: Ez a cikk bemutatja a helyi hálózati átjáró IP-címelőtagainak módosítása az Azure CLI használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864043"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Helyi hálózati átjáró beállításainak módosítása az Azure CLI használatával

Néha megváltoznak a helyi hálózati átjáró címelőtagja vagy IP-címe beállításai. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszerrel is módosíthatja, ha az alábbi listából egy másik beállítást választ:

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené

Telepítse a CLI parancsok legújabb verzióját (2.0 vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-címelőtagok módosítása

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Az átjáró IP-címének módosítása

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>További lépések

Ellenőrizheti az átjárókapcsolatot. Lásd: [Átjárókapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).

