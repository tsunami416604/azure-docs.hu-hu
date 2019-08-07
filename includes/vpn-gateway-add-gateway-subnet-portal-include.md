---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780160"
---
Az átjáró-alhálózat tartalmazza azokat a fenntartott IP-címeket, amelyeket a Virtual Network Gateway Services használ. Hozzon létre egy átjáró-alhálózatot.

1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A virtuális hálózat lapon kattintson az alhálózatok elemre a **VNet1-** alhálózatok oldal kibontásához.
3. Kattintson a felül található **+ átjáró** alhálózatra az **alhálózat hozzáadása** lap megnyitásához.

   ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
4. Az alhálózat **neve** automatikusan ki van töltve a szükséges "GatewaySubnet" értékkel. Állítsa be az automatikusan kitöltött **címtartományt (CIDR blokk)** a következő értéknek megfelelően:

   **Címtartomány (CIDR-blokk)** : 10.1.255.0/27

   ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "Az átjáró alhálózatának hozzáadása")
5. Hagyja a többi beállítást a **nincs** vagy **0**beállítás alapértelmezett értékeiként. Ezután kattintson **az OK** gombra az átjáró-alhálózat létrehozásához.