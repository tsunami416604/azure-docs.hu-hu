---
title: "Magánhálózati IP-címek konfigurálása virtuális gépek (klasszikus) – az Azure portálon |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja a magánhálózati IP-címek a virtuális gépek (klasszikus) Azure portál használatával."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bde6de3495c2909b63b1f85e420a4ff5e7ac2c1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Az Azure portál használatával egy virtuális gép (klasszikus) magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [kezelése a Resource Manager üzembe helyezési modellel statikus magánhálózati IP-cím](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi lépéseket a minta egy már létrehozott egyszerű környezetben várható. Ha azt szeretné, ebben a dokumentumban megjelenített futtatásához a lépéseket, először leírt tesztkörnyezet felépítéséhez [hozhat létre egy vnetet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>A statikus magánhálózati IP-cím megadása a virtuális gép létrehozásakor
Nevű virtuális gép létrehozása *DNS01* a a *előtér* egy vnet nevű alhálózat *TestVNet* statikus magánhálózati IP-címe a *192.168.1.101*, kövesse az alábbi lépéseket:

1. Egy böngészőből keresse fel a http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson a **új** > **számítási** > **Windows Server 2012 R2 Datacenter**, figyelje meg, hogy a **telepítési modell kiválasztása** lista már bemutatja **klasszikus**, és kattintson a **létrehozása**.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Az a **hozzon létre virtuális gép** panelen adja meg a létrehozandó virtuális gép nevét (*DNS01* a mi esetünkben), a helyi rendszergazda fiókot és jelszót.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Kattintson a **opcionális konfigurációs** > **hálózati** > **virtuális hálózati**, és kattintson a **TestVNet**. Ha **TestVNet** nem érhető el, győződjön meg arról, hogy használja a *USA középső RÉGIÓJA* helyét, és ez a cikk elején leírt tesztkörnyezet hozott létre.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. A a **hálózati** panelen, győződjön meg arról, hogy a jelenleg kijelölt alhálózat *előtér*, majd kattintson **IP-címek**, a **IP-cím hozzárendelése** kattintson **statikus**, és írja be *192.168.1.101* a **IP-cím** az alább látható módon.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Kattintson a **OK** a a **IP-címek** panelen kattintson a **OK** a a **hálózati** panelen, majd kattintson **OK** a a **opcionális konfigurációs** panelen.
7. Az a **hozzon létre virtuális gép** panelen kattintson a **létrehozása**. Figyelje meg, az alábbi csempe az irányítópulton látható.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan lehet lekérni a statikus magánhálózati IP-címadatok a virtuális gépek
A statikus magánhálózati IP-címadatok a virtuális gépnek a fent leírt lépésekkel létrehozott megtekintéséhez hajtsa végre az alábbi lépéseket.

1. Az Azure Azure portálon kattintson **összes TALLÓZÁSA** > **virtuális gépek (klasszikus)** > **DNS01** > **összes beállítás** > **IP-címek** , és figyelje meg az IP-cím hozzárendelése és IP-cím, az alább látható módon.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>A statikus magánhálózati IP-cím eltávolítása a virtuális gépek
A statikus magánhálózati IP-cím eltávolítása a fentiekben létrehozott virtuális Gépet, kövesse az alábbi lépéseket.

1. A a **IP-címek** a fent látható panelen kattintson **dinamikus** jobb oldalán **IP-cím hozzárendelése**, majd kattintson a **mentése**, és kattintson a **Igen**.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>A statikus magánhálózati IP-cím hozzáadása egy meglévő virtuális Gépen
A statikus magánhálózati IP-cím hozzáadása a virtuális gép létrehozása a fenti lépések használatával, kövesse az alábbi lépéseket:

1. Az a **IP-címek** kattintson a panel látható a fenti **statikus** jobb oldalán **IP-cím hozzárendelése**.
2. Típus *192.168.1.101* a **IP-cím**, majd kattintson a **mentése**, és kattintson a **Igen**.

## <a name="next-steps"></a>Következő lépések
* További tudnivalók [foglalt nyilvános IP-cím](virtual-networks-reserved-public-ip.md) címek.
* További tudnivalók [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) címek.
* Tekintse át a [fenntartott IP-REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx).

