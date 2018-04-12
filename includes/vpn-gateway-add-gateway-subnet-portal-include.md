---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 158400645423d2e9fe92768786b570e917907d98
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A VNet lap **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok oldalt.
3. Az **Alhálózatok** lap tetején lévő **+Átjáró-alhálózat** elemre kattintva nyissa meg az **Alhálózat hozzáadása** lapot.

  ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Az GatewaySubnet érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek.

  ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
5. Az alhálózat létrehozásához kattintson a lap alján található **OK** gombra.