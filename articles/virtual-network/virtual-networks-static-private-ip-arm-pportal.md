---
title: Magánhálózati IP-címek konfigurálása virtuális gépekhez – Azure Portal
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek magánhálózati IP-címeit a Azure Portal használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: bd734f171f4e10c4227fbab77485a788f02848b3
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196638"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>A virtuális gép magánhálózati IP-címeinek konfigurálása a Azure Portal használatával

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure Portal (klasszikus)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (klasszikus)](virtual-networks-static-private-ip-classic-ps.md)
> * [Azure CLI (klasszikus)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. [A statikus magánhálózati IP-címeket a klasszikus üzemi modellben is kezelheti](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

A következő példa egy egyszerű, már létrehozott környezetet vár. Ha a jelen dokumentumban látható lépéseket szeretné futtatni, először létre kell [hoznia a virtuális hálózat létrehozása](quick-create-portal.md)című témakörben leírt tesztkörnyezetben.

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Virtuális gép létrehozása statikus magánhálózati IP-címek teszteléséhez
Nem állíthat be statikus magánhálózati IP-címet a virtuális gép létrehozásakor a Resource Manager-alapú telepítési módban a Azure Portal használatával. Először létre kell hoznia a virtuális gépet, majd a magánhálózati IP-címet statikusra kell állítania.

Az alábbi lépéseket követve hozhat létre egy *DNS01* nevű virtuális gépet egy *TestVNet*nevű VNet előtér- *alhálózatában* :

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **erőforrás létrehozása** > **számítási** > **Windows Server 2012 R2 Datacenter**elemre, és figyelje meg, hogy a **telepítési modell kiválasztása** listában már látható a **Resource Manager**, majd kattintson a **Létrehozás**gombra, ahogy az alábbi ábrán látható.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Az **alapvető beállítások** panelen adja meg a létrehozandó virtuális gép nevét (a forgatókönyv*DNS01* ), a helyi rendszergazdai fiókot és a jelszót az alábbi ábrán látható módon.
   
    ![Alapvető beállítások panel](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Győződjön meg arról, hogy a kiválasztott hely az *USA középső* **régiója** , majd kattintson a **meglévő kijelölése** az **erőforráscsoport**területen lehetőségre, majd az **erőforráscsoport** elemre, végül pedig a *TestRG*elemre, végül pedig **az OK**gombra.
   
    ![Alapvető beállítások panel](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. A **méret kiválasztása** panelen válassza az **a1 standard**lehetőséget, majd kattintson a **kiválasztás**elemre.
   
    ![Méret panel kiválasztása](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. A **Beállítások** ablaktáblán győződjön meg arról, hogy a tulajdonságok a következő értékekkel vannak megadva, majd kattintson az **OK**gombra.
   
    -**Storage-fiók**: *vnetstorage*
   
   * **Hálózat**: *TestVNet*
   * **Alhálózat**: *előtér*
     
     ![Méret panel kiválasztása](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Az **Összefoglalás** ablaktáblán kattintson az **OK**gombra. Figyelje meg az irányítópulton megjelenő alábbi csempét.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Javasoljuk, hogy a virtuális gép operációs rendszerén belül ne rendeljen statikusan az Azure virtuális géphez rendelt magánhálózati IP-címet, ha szükséges, például ha [több IP-címet rendel egy Windows rendszerű virtuális géphez](virtual-network-multiple-ip-addresses-portal.md). Ha a magánhálózati IP-címet manuálisan állítja be az operációs rendszeren belül, győződjön meg arról, hogy a cím megegyezik az Azure [hálózati adapterhez](virtual-network-network-interface-addresses.md#change-ip-address-settings)rendelt magánhálózati IP-címmel, vagy megszakadhat a kapcsolat a virtuális géppel. További információ a [magánhálózati IP-címek](virtual-network-network-interface-addresses.md#private) beállításairól. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan kérheti le a statikus magánhálózati IP-címadatok egy virtuális géphez
A fenti lépésekkel létrehozott virtuális gép statikus magánhálózati IP-címére vonatkozó információk megtekintéséhez hajtsa végre a következő lépéseket.

1. A Azure Portal kattintson a **Tallózás minden** > **Virtual Machines** > **DNS01** > **minden beállítás** > **hálózati adapterek** elemre, majd kattintson az egyetlen felsorolt hálózati adapterre.
   
    ![VM-csempe üzembe helyezése](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. A **hálózati adapter** ablaktáblán kattintson a **minden beállítás** > **IP-címek** elemre, és figyelje meg a **hozzárendelés** és az **IP-cím** értékét.
   
    ![VM-csempe üzembe helyezése](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Statikus magánhálózati IP-cím hozzáadása meglévő virtuális géphez
A fenti lépésekkel létrehozott virtuális géphez statikus magánhálózati IP-cím hozzáadásához kövesse az alábbi lépéseket:

1. A fent látható **IP-címek** ablaktáblán kattintson a **statikus** elemre a **hozzárendelés**alatt.
2. Írja be az **IP-cím** *192.168.1.101* , majd kattintson a **Mentés**gombra.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Ha a **Mentés**gombra kattintva megfigyelheti, hogy a hozzárendelés továbbra is **dinamikus**értékre van állítva, akkor a beírt IP-cím már használatban van. Próbálkozzon másik IP-címmel.
> 
> 

Javasoljuk, hogy a virtuális gép operációs rendszerén belül ne rendeljen statikusan az Azure virtuális géphez rendelt magánhálózati IP-címet, ha szükséges, például ha [több IP-címet rendel egy Windows rendszerű virtuális géphez](virtual-network-multiple-ip-addresses-portal.md). Ha a magánhálózati IP-címet manuálisan állítja be az operációs rendszeren belül, győződjön meg arról, hogy a cím megegyezik az Azure [hálózati adapterhez](virtual-network-network-interface-addresses.md#change-ip-address-settings)rendelt magánhálózati IP-címmel, vagy megszakadhat a kapcsolat a virtuális géppel. További információ a [magánhálózati IP-címek](virtual-network-network-interface-addresses.md#private) beállításairól. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Virtuális gép statikus privát IP-cím eltávolítása
Ha el szeretné távolítani a statikus magánhálózati IP-címet a fent létrehozott virtuális gépről, hajtsa végre a következő lépést:

A fent látható **IP-címek** ablaktáblán kattintson a **dinamikus** elemre a **hozzárendelés**területen, majd kattintson a **Mentés**gombra.

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Javasoljuk, hogy a virtuális gép operációs rendszerén belül ne rendeljen statikusan az Azure virtuális géphez rendelt magánhálózati IP-címet, ha szükséges, például ha [több IP-címet rendel egy Windows rendszerű virtuális géphez](virtual-network-multiple-ip-addresses-portal.md). Ha a magánhálózati IP-címet manuálisan állítja be az operációs rendszeren belül, győződjön meg arról, hogy a cím megegyezik az Azure [hálózati adapterhez](virtual-network-network-interface-addresses.md#change-ip-address-settings)rendelt magánhálózati IP-címmel, vagy megszakadhat a kapcsolat a virtuális géppel. További információ a [magánhálózati IP-címek](virtual-network-network-interface-addresses.md#private) beállításairól. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="next-steps"></a>Következő lépések

További információ az [IP-címek beállításainak](virtual-network-network-interface-addresses.md)kezeléséről.

