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
ms.openlocfilehash: a4101f3bfe83859eea525370b5eebcaa6e193a2d
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44168737"
---
1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A VNet lap **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok oldalt.
3. Az **Alhálózatok** lap tetején lévő **+Átjáró-alhálózat** elemre kattintva nyissa meg az **Alhálózat hozzáadása** lapot.

  ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
  
4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Az GatewaySubnet érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek.

  ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
  
5. Az alhálózat létrehozásához kattintson a lap alján található **OK** gombra.
