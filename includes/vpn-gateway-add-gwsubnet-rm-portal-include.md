---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ec30840e550b87f83de67d52348b9723400e0f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111848"
---
1. Az a [az Azure portal](http://portal.azure.com), válassza ki a Resource Manager virtuális hálózatot, amelyhez szeretne egy virtuális hálózati átjáró létrehozása.

2. Az a **beállítások** szakaszban a virtuális hálózat lapjának, válassza ki a **alhálózatok** bontsa ki a **alhálózatok** lap.

3. Az a **alhálózatok** lapon jelölje be **átjáró-alhálózat** megnyitásához a **alhálózat hozzáadása** lapot.

  ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Az átjáró alhálózatának hozzáadása")

4. A **neve** az alhálózat nem automatikusan a értékű autofilled *GatewaySubnet*. Ez az érték megadása kötelező az Azure felismerje az alhálózat egy átjáró alhálózata. Módosítsa a autofilled **címtartomány** értékeit, hogy megfeleljenek a konfigurációs követelményeinek, majd válassza ki **OK** az alhálózat létrehozásához.

  ![Az alhálózat hozzáadása](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Az alhálózat hozzáadása")