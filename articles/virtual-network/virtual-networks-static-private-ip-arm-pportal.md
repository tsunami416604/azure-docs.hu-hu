---
title: Magánhálózati IP-címek konfigurálása virtuális gépek - Azure-portál |} Microsoft Docs
description: Útmutató az Azure portál használatával virtuális gépek magánhálózati IP-címek konfigurálásához.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff9d025980f80cb77246ea12dbf2e9bcedd73f86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Az Azure portál használatával virtuális gépek magánhálózati IP-címek konfigurálása

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [(Klasszikus) Azure-portálon](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (klasszikus)](virtual-networks-static-private-ip-classic-ps.md)
> * [Az Azure CLI (klasszikus)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [statikus magánhálózati IP-cím, a klasszikus üzembe helyezési modellel kezelése](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi minta lépések várhatóan a már létrehozott egy egyszerű környezetben. Ha azt szeretné, ebben a dokumentumban megjelenített futtatásához a lépéseket, először leírt tesztkörnyezet felépítéséhez [hozzon létre egy virtuális hálózatot](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Tesztelési statikus magánhálózati IP-címek egy virtuális gép létrehozása
Nem be hozzá statikus magánhálózati IP-cím a Resource Manager telepítési módban a virtuális gépek létrehozásakor az Azure portál használatával. Először hozza létre a virtuális Gépet, majd állítsa be a magánhálózati statikus IP-címe.

Nevű virtuális gép létrehozása *DNS01* a a *előtér* egy vnet nevű alhálózat *TestVNet*, kövesse az alábbi lépéseket:

1. Egy böngészőből keresse fel a(z) http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson a **hozzon létre egy erőforrást** > **számítási** > **Windows Server 2012 R2 Datacenter**, figyelje meg, hogy a **jelölje ki a telepítést modell** lista már bemutatja **erőforrás-kezelő**, és kattintson a **létrehozása**, az alábbi ábrán látható módon.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Az a **alapjai** panelen adja meg a virtuális gép létrehozása (*DNS01* forgatókönyvben), a helyi rendszergazdai fiókot és jelszót, az alábbi ábrán látható módon.
   
    ![Alapvető beállítások panelen](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Győződjön meg arról, hogy a **hely** kiválasztott *USA középső RÉGIÓJA*, kattintson a **válasszon meglévő** alatt **erőforráscsoport**, kattintson a **erőforráscsoport** újra, majd kattintson a *TestRG*, és kattintson a **OK**.
   
    ![Alapvető beállítások panelen](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Az a **méret kiválasztása** ablaktáblán válassza előbb **A1 szabványos**, és kattintson a **kiválasztása**.
   
    ![Válassza ki a méret panelen](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. Az a **beállítások** ablaktáblában győződjön meg a tulajdonságait a következő értékekkel van beállítva, és kattintson a **OK**.
   
    -**A tárfiók**: *vnetstorage*
   
   * **Hálózati**: *TestVNet*
   * **Alhálózati**: *előtér*
     
     ![Válassza ki a méret panelen](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Az a **összegzés** ablaktáblán kattintson a **OK**. Figyelje meg a következő csempe az irányítópulton látható.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Javasoljuk, hogy nem statikusan rendelje a magánhálózati IP-címe az operációs rendszerben a virtuális gépek az Azure virtuális géphez rendelt kivéve, ha szükséges, például amikor [több IP-címek hozzárendelése egy Windows virtuális gépre](virtual-network-multiple-ip-addresses-portal.md). Ha manuálisan állítsa be a magánhálózati IP-cím, az operációs rendszerből, győződjön meg arról, hogy az a magánhálózati IP-cím, az Azure rendelt megegyező címre [hálózati illesztő](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. További információ [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Manuálisan soha ne rendelje a nyilvános IP-cím, egy Azure virtuális gépen belül a virtuális gép operációs rendszerének rendelt.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan lehet lekérni a statikus magánhálózati IP-címadatok a virtuális gépek
A statikus magánhálózati IP-címadatok a virtuális gépnek a fent leírt lépésekkel létrehozott megtekintéséhez hajtsa végre az alábbi lépéseket.

1. Az Azure-portálon kattintson **összes TALLÓZÁSA** > **virtuális gépek** > **DNS01** > **mindenbeállítás**  >  **Hálózati illesztőt** és kattintson a felsorolt csak hálózati adapterén.
   
    ![Virtuális gép csempe telepítése](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. Az a **hálózati illesztő** ablaktáblán kattintson a **összes beállítás** > **IP-címek** , és figyelje meg a **hozzárendelés** és  **IP-cím** értékeket.
   
    ![Virtuális gép csempe telepítése](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>A statikus magánhálózati IP-cím hozzáadása egy meglévő virtuális Gépen
A statikus magánhálózati IP-cím hozzáadása a virtuális gép létrehozása a fenti lépések használatával, kövesse az alábbi lépéseket:

1. Az a **IP-címek** a fent látható ablaktáblán kattintson a **statikus** alatt **hozzárendelés**.
2. Típus *192.168.1.101* a **IP-cím**, és kattintson a **mentése**.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Ha kattintás után **mentése**, bizonyára észrevette, hogy a hozzárendelés még beállításai **dinamikus**, az azt jelenti, hogy a megadott IP-cím már használatban van. Próbálja meg egy másik IP-címet.
> 
> 

Javasoljuk, hogy nem statikusan rendelje a magánhálózati IP-címe az operációs rendszerben a virtuális gépek az Azure virtuális géphez rendelt kivéve, ha szükséges, például amikor [több IP-címek hozzárendelése egy Windows virtuális gépre](virtual-network-multiple-ip-addresses-portal.md). Ha manuálisan állítsa be a magánhálózati IP-cím, az operációs rendszerből, győződjön meg arról, hogy az a magánhálózati IP-cím, az Azure rendelt megegyező címre [hálózati illesztő](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. További információ [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Manuálisan soha ne rendelje a nyilvános IP-cím, egy Azure virtuális gépen belül a virtuális gép operációs rendszerének rendelt.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>A statikus magánhálózati IP-cím eltávolítása a virtuális gépek
A statikus magánhálózati IP-cím eltávolítása a fentiekben létrehozott virtuális Gépet, végezze el a következő lépést:

Az a **IP-címek** a fent látható ablaktáblán kattintson a **dinamikus** alatt **hozzárendelés**, és kattintson a **mentése**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Javasoljuk, hogy nem statikusan rendelje a magánhálózati IP-címe az operációs rendszerben a virtuális gépek az Azure virtuális géphez rendelt kivéve, ha szükséges, például amikor [több IP-címek hozzárendelése egy Windows virtuális gépre](virtual-network-multiple-ip-addresses-portal.md). Ha manuálisan állítsa be a magánhálózati IP-cím, az operációs rendszerből, győződjön meg arról, hogy az a magánhálózati IP-cím, az Azure rendelt megegyező címre [hálózati illesztő](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. További információ [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Manuálisan soha ne rendelje a nyilvános IP-cím, egy Azure virtuális gépen belül a virtuális gép operációs rendszerének rendelt.

## <a name="next-steps"></a>További lépések

Kezelésével kapcsolatos [IP-címbeállítások](virtual-network-network-interface-addresses.md).

