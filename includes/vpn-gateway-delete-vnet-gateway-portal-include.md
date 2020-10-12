---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67178990"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>1. lépés: navigáljon a virtuális hálózati átjáróhoz

1. A [Azure Portal](https://portal.azure.com)navigáljon az **összes erőforráshoz**. 
2. A virtuális hálózati átjáró megnyitásához nyissa meg a törölni kívánt virtuális hálózati átjárót, és kattintson rá.

### <a name="step-2-delete-connections"></a>2. lépés: kapcsolatok törlése

1. A virtuális hálózati átjáró lapján kattintson a **kapcsolatok** elemre az átjáró összes kapcsolatának megtekintéséhez.
2. Kattintson a **"..."** elemre a kapcsolatok nevének sorában, majd válassza a **Törlés** lehetőséget a legördülő menüből.
3. Kattintson az **Igen** gombra annak megerősítéséhez, hogy törölni kívánja a kapcsolódást. Ha több kapcsolattal rendelkezik, törölje az összes kapcsolatot.

### <a name="step-3-delete-the-virtual-network-gateway"></a>3. lépés: a virtuális hálózati átjáró törlése

Ügyeljen arra, hogy ha a S2S-konfiguráción kívül P2S-konfigurációval rendelkezik ehhez a VNet, a virtuális hálózati átjáró törlésével a rendszer figyelmeztetés nélkül automatikusan leválasztja az összes P2S-ügyfelet.

1. A virtuális hálózati átjáró lapon kattintson az **Áttekintés**elemre.
2. Az átjáró törléséhez az **Áttekintés** lapon kattintson a **Törlés** elemre.
