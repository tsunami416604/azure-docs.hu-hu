---
title: Alhálózati delegálás hozzáadása vagy eltávolítása egy Azure-beli virtuális hálózaton
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el egy delegált alhálózatot egy Azure-beli szolgáltatáshoz az Azure-ban.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 9bf44aa5ab18f94ec91650548dc13360ce1b1f3d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938498"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Alhálózati delegálás hozzáadása vagy eltávolítása

Az alhálózati delegálás explicit jogosultságokat biztosít a szolgáltatás számára, hogy a szolgáltatás telepítésekor egyedi azonosítóval hozzon létre szolgáltatás-specifikus erőforrásokat az alhálózatban. Ez a cikk azt ismerteti, hogyan lehet delegált alhálózatot hozzáadni vagy eltávolítani egy Azure-szolgáltatáshoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot, amelyet később egy Azure-szolgáltatáshoz delegál.

1. A képernyő bal felső részén válassza az **erőforrás** > létrehozása**hálózatkezelés** > **virtuális hálózat**lehetőséget.
1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *MyVirtualNetwork*. |
    | Címtartomány | Adja meg a *10.0.0.0/16*értéket. |
    | Subscription | Válassza ki előfizetését.|
    | Resource group | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Location | Válassza a **EastUS**lehetőséget.|
    | Alhálózat – név | Adja meg a *mySubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.0.0.0/24*értéket. |
    |||
1. Hagyja a többi értéket alapértelmezettként, majd válassza a **Létrehozás**lehetőséget.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Alhálózat delegálása Azure-szolgáltatásba

Ebben a szakaszban az előző szakaszban létrehozott alhálózatot delegálja egy Azure-szolgáltatáshoz.

1. A portál keresési sávján adja meg a *myVirtualNetwork*. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. A keresési eredmények között válassza a *myVirtualNetwork*lehetőséget.
3. Válassza az **alhálózatok**lehetőséget, a **Beállítások**területen, majd válassza a **mySubnet**lehetőséget.
4. A *mySubnet* lapon az **alhálózati delegálás** listáról válassza ki az **alhálózat delegálása szolgáltatáshoz** (például **Microsoft. DBforPostgreSQL/serversv2**) felsorolt szolgáltatásokat.  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Alhálózati delegálás eltávolítása egy Azure-szolgáltatásból

1. A portál keresési sávján adja meg a *myVirtualNetwork*. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
2. A keresési eredmények között válassza a *myVirtualNetwork*lehetőséget.
3. Válassza az **alhálózatok**lehetőséget, a **Beállítások**területen, majd válassza a **mySubnet**lehetőséget.
4. A *mySubnet* lapon az **alhálózati delegálás** listához válassza a **nincs** lehetőséget az **alhálózat delegálása szolgáltatásban**listában felsorolt szolgáltatások közül. 

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [kezelheti az alhálózatokat az Azure-ban](virtual-network-manage-subnet.md).
