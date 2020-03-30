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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178990"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>1. lépés: Navigálás a virtuális hálózati átjáróra

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a **Minden erőforrás t.** 
2. A virtuális hálózati átjáró lap megnyitásához keresse meg a törölni kívánt virtuális hálózati átjárót, és kattintson rá.

### <a name="step-2-delete-connections"></a>2. lépés: Kapcsolatok törlése

1. A virtuális hálózati átjáró lapján kattintson a **Kapcsolatok** elemre az átjáróhoz való összes kapcsolat megtekintéséhez.
2. Kattintson a kapcsolat nevének sorában a **"..."** gombra, majd válassza a **törlés** lehetőséget a legördülő menüből.
3. Kattintson az **Igen** gombra a kapcsolat törlésének megerősítéséhez. Ha több kapcsolattal rendelkezik, törölje az egyes kapcsolatokat.

### <a name="step-3-delete-the-virtual-network-gateway"></a>3. lépés: A virtuális hálózati átjáró törlése

Ne feledje, hogy ha az S2S-konfiguráció mellett p2S-konfigurációval rendelkezik ehhez a virtuális hálózathoz, a virtuális hálózati átjáró törlése figyelmeztetés nélkül automatikusan leválasztja az összes P2S-ügyfelet.

1. A virtuális hálózati átjáró lapon kattintson az **Áttekintés gombra.**
2. Az **Áttekintő** lapon kattintson a **Törlés** gombra az átjáró törléséhez.
