---
title: Magánhálózati IP-címek konfigurálása virtuális gépek – az Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a magánhálózati IP-címek a virtuális gépek az Azure portal használatával.
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
ms.openlocfilehash: e5efe0516d1b2dd387532d31a0a6654e6651fe41
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011739"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Az Azure portal használatával virtuális gép magánhálózati IP-címek konfigurálása

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Az Azure Portalon (klasszikus)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (klasszikus)](virtual-networks-static-private-ip-classic-ps.md)
> * [Az Azure parancssori felület (klasszikus)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [kezelése a klasszikus üzemi modellben statikus magánhálózati IP-cím](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi minta lépéseket már létrehozott egy egyszerű környezetben várható. Ha azt szeretné, hajtsa végre a lépéseket, a jelen dokumentum megjelenített, először hozhat létre a leírt tesztkörnyezet [hozzon létre egy virtuális hálózatot](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Statikus magánhálózati IP-címek tesztelési virtuális gép létrehozása
Statikus magánhálózati IP-cím nem állítható a Resource Manager üzembe helyezési mód a virtuális gép létrehozása során az Azure portal használatával. Először hozza létre a virtuális Gépet, majd állítsa be a magánhálózati IP-cím lehet statikus.

Nevű virtuális gép létrehozása *DNS01* a a *előtérbeli* nevű virtuális hálózat alhálózatának *TestVNet*, kövesse az alábbi lépéseket:

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson a **erőforrás létrehozása** > **számítási** > **Windows Server 2012 R2 Datacenter**, vegye figyelembe, hogy a **jelölje ki a telepítést modell** listájában már **Resource Manager**, és kattintson a **létrehozás**, az alábbi ábrán látható módon.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Az a **alapjai** panelen adja meg a nevét, a virtuális gép létrehozása (*DNS01* forgatókönyvben), a helyi rendszergazdai fiókot és jelszót, az alábbi ábrán látható módon.
   
    ![Alapvető beállítások panel](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Győződjön meg arról, a **helye** kiválasztott *USA középső RÉGIÓJA*, kattintson a **válasszon meglévő** alatt **erőforráscsoport**, kattintson **Erőforráscsoport** újra, majd kattintson a *TestRG*, és kattintson a **OK**.
   
    ![Alapvető beállítások panel](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Az a **méret kiválasztása** ablaktáblán válassza **A1 Standard**, és kattintson a **kiválasztása**.
   
    ![Válassza ki a méret panel](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. Az a **beállítások** ablaktáblán győződjön meg a tulajdonságok vannak beállítva a következő értékeket, és kattintson a **OK**.
   
    -**Storage-fiók**: *vnetstorage*
   
   * **Hálózati**: *TestVNet*
   * **Alhálózat**: *Előtér*
     
     ![Válassza ki a méret panel](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Az a **összefoglalás** ablaktáblán kattintson a **OK**. Figyelje meg a következő csempét az irányítópulton jelenik meg.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-portal.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan kérheti le a statikus magánhálózati IP-címadatok egy virtuális géphez
A statikus magánhálózati IP-címadatok a fenti lépésekkel létrehozott virtuális gép megtekintéséhez futtassa az alábbi lépéseket.

1. Az Azure Portalon kattintson **összes TALLÓZÁSA** > **virtuális gépek** > **DNS01** > **mindenbeállítás**  >  **Hálózati adapterek** és kattintson a listában egyetlen hálózati adapteren.
   
    ![Virtuális gép üzembe helyezése csempe](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. Az a **hálózati adapter** ablaktáblán kattintson a **minden beállítás** > **IP-címek** , és figyelje meg a **hozzárendelés** és  **IP-cím** értékeket.
   
    ![Virtuális gép üzembe helyezése csempe](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Meglévő virtuális géphez statikus magánhálózati IP-cím hozzáadása
A fenti lépésekkel létrehozott virtuális géphez statikus magánhálózati IP-cím hozzáadásához kövesse az alábbi lépéseket:

1. Az a **IP-címek** ablaktáblán a fenti **statikus** alatt **hozzárendelés**.
2. Típus *192.168.1.101* a **IP-cím**, és kattintson a **mentése**.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Ha kattintás után **mentése**, láthatja, hogy a hozzárendelés még értéke **dinamikus**, ez azt jelenti, hogy a megadott IP-cím már használatban van. Próbálkozzon egy másik IP-címet.
> 
> 

Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-portal.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Virtuális gép statikus privát IP-cím eltávolítása
Távolítsa el a statikus magánhálózati IP-cím a fent létrehozott virtuális gépről, hajtsa végre a következő lépést:

Az a **IP-címek** ablaktáblán a fenti **dinamikus** alatt **hozzárendelés**, és kattintson a **mentése**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-portal.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="next-steps"></a>További lépések

Kezelésével kapcsolatos [IP-címbeállítások](virtual-network-network-interface-addresses.md).

