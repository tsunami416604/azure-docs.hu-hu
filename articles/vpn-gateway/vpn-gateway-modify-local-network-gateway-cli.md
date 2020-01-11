---
title: 'VPN Gateway: az átjáró IP-címének beállításainak módosítása: Azure CLI'
description: Ez a cikk végigvezeti a helyi hálózati átjáró IP-címeinek előtagjainak az Azure CLI használatával történő módosításán.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864043"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Helyi hálózati átjáró beállításainak módosítása az Azure CLI használatával

Előfordulhat, hogy a helyi hálózati átjáró címének előtagja vagy az átjáró IP-címének beállításai megváltoznak. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszer használatával is módosíthatja, ha a következő listából választ egy másik lehetőséget:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Előkészületek

Telepítse a CLI-parancsok legújabb verzióját (2,0 vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>IP-cím előtagjainak módosítása

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Átjáró IP-címének módosítása

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Következő lépések

Ellenőrizheti az átjáró-kapcsolatokat. Lásd: [átjáró-kapcsolatok ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).

