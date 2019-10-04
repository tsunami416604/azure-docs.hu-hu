---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178990"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>1\. lépés: Lépjen a virtuális hálózati átjáró

1. Az a [az Azure portal](https://portal.azure.com), navigáljon a **összes erőforrás**. 
2. A virtuális hálózati átjáró oldalának megnyitásához nyissa meg a virtuális hálózati átjárót, amelyet szeretne törölni, és kattintson rá.

### <a name="step-2-delete-connections"></a>2\. lépés: Kapcsolatok törlése

1. A virtuális hálózati átjáró oldalán kattintson **kapcsolatok** megtekintéséhez az átjáró felé irányuló összes kapcsolatot.
2. Kattintson a **"..."** a sorban a kapcsolat nevét, majd válassza ki **törlése** a legördülő listából.
3. Kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e törölni a kapcsolatot. Ha több kapcsolat van, minden kapcsolat törlése.

### <a name="step-3-delete-the-virtual-network-gateway"></a>3\. lépés: A virtuális hálózati átjáró törlése

Vegye figyelembe, hogy ha egy virtuális hálózaton P2S konfiguráció mellett az S2S-konfigurációnak, a virtuális hálózati átjáró törlése folyamatban automatikusan leválasztja az összes figyelmeztetés nélkül P2S-ügyfelekhez.

1. A virtuális hálózati átjáró oldalán kattintson **áttekintése**.
2. Az a **áttekintése** kattintson **törlése** törölni az átjárót.
