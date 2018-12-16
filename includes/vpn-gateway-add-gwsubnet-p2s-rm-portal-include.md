---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5642533fe1015e88c3b27e83139bfd26cb0b1a53
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444171"
---
1. Keresse meg a [portálon](http://portal.azure.com) azt a Resource Manager-alapú virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A VNet lap **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az **Alhálózatok** oldalt.
3. Az **Alhálózatok** lap **+Átjáróalhálózat** elemére kattintva nyissa meg az **Alhálózat hozzáadása** lapot. 

   ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Az átjáró alhálózatának hozzáadása")
4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek. Ne konfigurálja az útválasztási táblázat vagy Szolgáltatásvégpontok.

   ![Az alhálózat hozzáadása](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Az alhálózat hozzáadása")
5. Kattintson a **OK** az alhálózat létrehozásához az oldal alján.
