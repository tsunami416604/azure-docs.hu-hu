---
title: Privát IP-címek konfigurálása a virtuális gépekhez (klasszikus) - Azure portal | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek (Klasszikus) privát IP-címeket az Azure Portalhasználatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9de9fd2eb7a46719b841a885ac173f8d9c1271c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71059105"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Privát IP-címek konfigurálása egy virtuális géphez (Klasszikus) az Azure Portal használatával

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Az [Erőforrás-kezelő telepítési modelljében statikus magáncím is kezelhető.](virtual-networks-static-private-ip-arm-pportal.md)

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi mintalépések egy már létrehozott egyszerű környezetet várnak. Ha a lépéseket úgy szeretné futtatni, ahogy azok ebben a dokumentumban megjelennek, először hozza létre a vnet létrehozása című dokumentumban leírt [tesztkörnyezetet.](virtual-networks-create-vnet-classic-pportal.md)

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus privát IP-cím megadása virtuális gép létrehozásakor
*Dns01* nevű virtuális gép létrehozásához a *TestVNet* nevű virtuális hálózat *FrontEnd* alhálózatában, *192.168.1.101*statikus privát IP-címével, hajtsa végre az alábbi lépéseket:

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Válassza az **ÚJ** > **Számítás** > Windows Server**2012 R2 adatközpont lehetőséget,** vegye figyelembe, hogy a Telepítési modell **kiválasztása** lista már klasszikus , majd válassza a **Létrehozás lehetőséget.** **Classic**
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. A **Virtuális gép létrehozása**csoportban adja meg a létrehozandó virtuális gép ( a*forgatókönyvBEN DNS01)* nevét, a helyi rendszergazdai fiókot és a jelszót.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Válassza **a Választható konfigurációs** > **hálózati** > **virtuális hálózat**lehetőséget, majd a **TestVNet**lehetőséget. Ha **a TestVNet** nem érhető el, győződjön meg arról, hogy az *USA középső* helyét használja, és létrehozta a cikk elején ismertetett tesztkörnyezetet.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. A **Hálózat**csoportban győződjön meg arról, hogy az aktuálisan kijelölt alhálózat *FrontEnd*, majd válassza az **IP-címeket,** az **IP-címhozzárendelés** csoportban válassza a **Statikus**lehetőséget, majd írja be a *192.168.1.101* értéket **az IP-címhez** az alábbi módon.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Válassza **az OK** gombot az **IP-címek**csoportban, válassza az **OK** lehetőséget a **Hálózat**csoportban, majd a **Választható konfiguráció**csoportban válassza az **OK** lehetőséget.
7. A **Virtuális gép létrehozása**csoportban válassza a **Létrehozás lehetőséget.** Figyelje meg az alábbi csempét az irányítópulton:
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Statikus privát IP-címadatok beolvasása virtuális géphez
A fenti lépésekkel létrehozott virtuális gép statikus privát IP-címadatainak megtekintéséhez hajtsa végre az alábbi lépéseket.

1. Az Azure Portalon válassza **a BROWSE ALL** > **Virtual Machines (classic)** > **DNS01** > **Minden beállítás** > **IP-címét,** és figyelje meg az IP-cím hozzárendelését és IP-címét az alábbiak szerint.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Statikus privát IP-cím eltávolítása a virtuális gépről

Az **IP-címek**csoportban válassza a **Dinamikus** lehetőséget az **IP-címhozzárendelésjobb**oldalán , válassza a **Mentés**lehetőséget, majd az **Igen**lehetőséget, ahogy az az alábbi képen látható:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Statikus privát IP-cím hozzáadása meglévő virtuális géphez

1. Az **IP-címek**csoportban a korábban látható területen válassza az **IP-címhozzárendelés**jobb oldalán a **Statikus** lehetőséget.
2. Írja be a *192.168.101 parancsot* **az IP-címhez,** válassza a **Mentés**lehetőséget, majd kattintson az **Igen**gombra.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-címek beállítása az operációs rendszeren belül

Javasoljuk, hogy ne rendelje hozzá statikusan az Azure virtuális géphez rendelt privát IP-címet egy virtuális gép operációs rendszerén belül, kivéve, ha szükséges. Ha manuálisan állítja be a privát IP-címet az operációs rendszeren belül, győződjön meg arról, hogy az megegyezik az Azure virtuális géphez rendelt privát IP-címmel, vagy elveszítheti a virtuális géphez való kapcsolódást. Soha ne rendelje hozzá manuálisan az Azure virtuális géphez rendelt nyilvános IP-címet a virtuális gép operációs rendszerén belül.

## <a name="next-steps"></a>További lépések
* További információ a [fenntartott nyilvános IP-címekről.](virtual-networks-reserved-public-ip.md)
* További információ [a példányszintű nyilvános IP-címekről.](virtual-networks-instance-level-public-ip.md)
* Tekintse meg a [fenntartott IP-REST API-kat.](https://msdn.microsoft.com/library/azure/dn722420.aspx)

