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
ms.openlocfilehash: 815a217526117325ff80759701141b2b4d148514
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805301"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>A klasszikus virtuális hálózat létrehozása az Azure-portálon
A fenti forgatókönyv alapján VNet klasszikus létrehozásához kövesse az alábbi lépéseket.

1. Egy böngészőből keresse fel a(z) http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Erőforrás létrehozása** > **Hálózat** > **Virtuális hálózat** lehetőségre. Figyelje meg, hogy a **telepítési modell kiválasztása** lista már bemutatja **klasszikus**. 3. Kattintson a **létrehozása** az alábbi ábrán látható módon.
   
    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Az a **virtuális hálózati** ablak, írja be a **neve** a virtuális hálózaton, és kattintson a **Címtéren**. Konfigurálja a virtuális hálózat és az első alhálózati cím terület beállításait, majd kattintson a **OK**. Az alábbi ábrán a mi esetünkben CIDR blokk beállításait.
   
    ![Cím terület ablaktábla](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Kattintson a **erőforráscsoport** , és válasszon egy erőforráscsoportot, adja hozzá a Vnetet, vagy kattintson **hozzon létre új erőforráscsoport** a virtuális hálózat hozzáadása egy új erőforráscsoportot. Az alábbi ábrán láthatók az erőforráshoz tartozó nevű új erőforráscsoport-beállítások **TestRG**. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
   
    ![Erőforrás csoport ablaktábla létrehozása](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Szükség esetén módosítsa a VNethez tartozó **Előfizetés** és **Hely** beállításokat. 
7. Ha nem szeretné, hogy a VNet csempe megjelenjen a **Kezdőpulton**, tiltsa le **A kezdőpulton rögzít** lehetőséget. 
8. Kattintson a **létrehozása** és figyelje meg, a csempe **virtuális hálózat létrehozása** az alábbi ábrán látható módon.
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Várjon, amíg a vnet létrejön, és amikor a csempe megtekintéséhez kattintson rá a További alhálózatok felvétele.
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Megjelenik a **konfigurációs** a vnet látható módon. 
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Kattintson a **alhálózatok** > **hozzáadása**, írja be a **neve** , és adja meg egy **-címtartományt (CIDR-blokkja)** az alhálózathoz, majd Kattintson a **OK**. Az alábbi ábrán láthatók az aktuális esetünkben beállításai.
    
    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

