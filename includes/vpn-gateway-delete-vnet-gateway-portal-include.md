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
ms.openlocfilehash: 5b2aa7fedbc203c50796a0e0c8d9cdb3895ae6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>1. lépés: Nyissa meg a virtuális hálózati átjáró

1. Az a [Azure-portálon](https://portal.azure.com), navigáljon a **összes erőforrás**. 
2. Nyissa meg a virtuális hálózati átjáró lapját, válassza a virtuális hálózati átjáró törlése, és kattintson rá a kívánt.

### <a name="step-2-delete-connections"></a>2. lépés: Delete kapcsolatok

1. Kattintson a lap a virtuális hálózati átjáró **kapcsolatok** összes kapcsolatot az átjáró megtekintéséhez.
2. Kattintson a **"..."** a sorban található a kapcsolat neve, majd válassza ki **törlése** a legördülő listából.
3. Kattintson a **Igen** annak megerősítéséhez, hogy törölni kívánja a kapcsolatot. Ha több kapcsolatot, minden kapcsolat törlése.

### <a name="step-3-delete-the-virtual-network-gateway"></a>3. lépés: A virtuális hálózati átjáró törlése

Vegye figyelembe, hogy ha egy P2S-konfigurációt a vneten mellett S2S-konfigurációjáról, a virtuális hálózati átjáró törlése folyamatban lesz automatikusan ügyfelek leválasztása a összes P2S figyelmeztetés nélkül.

1. Kattintson a virtuális hálózati átjáró lap **áttekintése**.
2. Az a **áttekintése** kattintson **törlése** törölni az átjárót.
