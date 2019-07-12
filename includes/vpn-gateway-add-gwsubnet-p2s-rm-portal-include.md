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
ms.openlocfilehash: b044912fd8f52f3f4fdbe1b3a74b64f9b565ddf0
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673379"
---
1. Keresse meg a [portálon](https://portal.azure.com) azt a Resource Manager-alapú virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A VNet lap **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az **Alhálózatok** oldalt.
3. Az **Alhálózatok** lap **+Átjáróalhálózat** elemére kattintva nyissa meg az **Alhálózat hozzáadása** lapot. 

   ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Az átjáró alhálózatának hozzáadása")
4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek. Ne konfigurálja az útválasztási táblázat vagy Szolgáltatásvégpontok.

   ![Az alhálózat hozzáadása](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Az alhálózat hozzáadása")
5. Kattintson a **OK** az alhálózat létrehozásához az oldal alján.
