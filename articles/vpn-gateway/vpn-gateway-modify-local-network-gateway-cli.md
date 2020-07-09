---
title: 'VPN Gateway: az átjáró IP-címének beállításainak módosítása: Azure CLI'
description: Ez a cikk végigvezeti a helyi hálózati átjáró IP-címeinek előtagjainak az Azure CLI használatával történő módosításán.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: c4809c51a59805ac996bd4c5971ec633ae6c2aed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987070"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Helyi hálózati átjáró beállításainak módosítása az Azure CLI használatával

Előfordulhat, hogy a helyi hálózati átjáró címének előtagja vagy az átjáró IP-címének beállításai megváltoznak. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszer használatával is módosíthatja, ha a következő listából választ egy másik lehetőséget:

> [!div class="op_single_selector"]
> * [Azure Portalra](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Előkészületek

Telepítse a CLI-parancsok legújabb verzióját (2,0 vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-cím előtagjainak módosítása

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Átjáró IP-címének módosítása

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>További lépések

Ellenőrizheti az átjáró-kapcsolatokat. Lásd: [átjáró-kapcsolatok ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).

