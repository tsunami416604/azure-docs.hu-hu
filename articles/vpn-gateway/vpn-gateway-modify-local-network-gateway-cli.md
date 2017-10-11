---
title: "Módosítsa a helyi hálózati átjáró IP-cím előtagokat és a VPN-átjáró IP-címe |} Azure |} PARANCSSORI FELÜLETTEL |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan módosítása a helyi hálózati átjáró, az Azure parancssori felület használatával IP-cím előtagokat."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 7db1ad970ebb93d46d5a861f9a9b27bf121531a3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Az Azure parancssori felület használatával a helyi hálózati átjáró beállításainak módosítása

Egyes esetekben a helyi hálózati átjáró címelőtagja vagy az átjáró IP-cím beállításainak módosítása Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításokat. Emellett módosíthatja ezeket a beállításokat egy másik módszer a egy másik lehetőség kiválasztásával az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Előkészületek

Telepítse a legújabb verzióját a parancssori felület parancsait (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI 2.0-s verziójának telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>IP-cím előtagokat módosítása

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Az átjáró IP-cím módosítása

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Következő lépések

Ellenőrizheti a gateway-kapcsolatot. Lásd: [egy átjáró kapcsolat ellenőrzéséhez](vpn-gateway-verify-connection-resource-manager.md).

