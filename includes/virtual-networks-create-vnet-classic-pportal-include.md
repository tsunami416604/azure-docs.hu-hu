---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: aa88bf3bd6c5037b41c09e9ffe47921f1b9dc9be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171986"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Egy klasszikus virtuális hálózat létrehozása az Azure Portalon
Egy klasszikus virtuális hálózat a fenti forgatókönyv alapján létrehozásához kövesse az alábbi lépéseket.

1. Egy böngészőből keresse fel a(z) http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Erőforrás létrehozása** > **Hálózat** > **Virtuális hálózat** lehetőségre. Figyelje meg, hogy a **telepítési modell kiválasztása** listájában már **klasszikus**. 3. Kattintson a **létrehozás** az alábbi ábrán látható módon.
   
    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Az a **virtuális hálózati** ablak, írja be a **neve** a virtuális hálózathoz, és kattintson a **címtér**. A cím a virtuális hálózat és az első alhálózat tartozó lemezterület-beállítások konfigurálása, majd kattintson a **OK**. A következő ábrán látható forgatókönyvünk CIDR blokk beállításait.
   
    ![Cím terület panel](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Kattintson a **erőforráscsoport** , és válasszon ki egy erőforráscsoportot, adja hozzá a virtuális hálózat számára, vagy kattintson **hozzon létre új erőforráscsoportot** , adja hozzá a Vnetet egy új erőforráscsoportot. A következő ábrán látható az erőforrás nevű új erőforráscsoport-beállításai **TestRG**. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
   
    ![Erőforrás-csoport panel létrehozása](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Szükség esetén módosítsa a VNethez tartozó **Előfizetés** és **Hely** beállításokat. 
7. Ha nem szeretné, hogy a VNet csempe megjelenjen a **Kezdőpulton**, tiltsa le **A kezdőpulton rögzít** lehetőséget. 
8. Kattintson a **létrehozás** , és figyelje meg a csempe **virtuális hálózat létrehozása** az alábbi ábrán látható módon.
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Várjon, amíg a virtuális hálózat számára létrehozni, és ha a csempe megtekintéséhez kattintson rá a adjon hozzá további alhálózatokat.
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Megtekintheti a **konfigurációs** a virtuális hálózat látható módon. 
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Kattintson a **alhálózatok** > **Hozzáadás**, majd írja be egy **neve** , és adja meg egy **címtartomány (CIDR-blokk)** az alhálózathoz, majd Kattintson a **OK**. A következő ábrán látható, az aktuális helyzethez beállításait.
    
    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

