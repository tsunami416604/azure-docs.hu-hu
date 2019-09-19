---
title: Virtuális gépek magánhálózati IP-címeinek konfigurálása (klasszikus) – Azure Portal | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek magánhálózati IP-címeit a Azure Portal használatával.
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
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059105"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Magánhálózati IP-címek konfigurálása virtuális géphez (klasszikus) a Azure Portal használatával

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [kezelése a Resource Manager-alapú üzemi modellben statikus magánhálózati IP-cím](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi példa azt ismerteti, hogy egy egyszerű környezet már létre van hozva. Ha a jelen dokumentumban látható lépéseket szeretné futtatni, először létre kell [hoznia a vnet létrehozása](virtual-networks-create-vnet-classic-pportal.md)című témakörben leírt tesztkörnyezetben.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus magánhálózati IP-cím megadása a virtuális gép létrehozásakor
A következő lépésekkel hozhat létre egy *DNS01* nevű virtuális gépet egy *TestVNet* nevű VNet előtér- *alhálózatában* a *192.168.1.101*statikus magánhálózati IP-címével:

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Válassza az **új** > **számítás** > **Windows Server 2012 R2 Datacenter**lehetőséget, és figyelje meg, hogy a **telepítési modell kiválasztása** listában már látható a **klasszikus**, majd válassza a **Létrehozás**lehetőséget.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. A **virtuális gép létrehozása**területen adja meg a létrehozandó virtuális gép nevét (a forgatókönyv*DNS01* ), a helyi rendszergazdai fiókot és a jelszót.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Válassza a **választható konfigurációs** > **hálózati** > **Virtual Network**, majd a **TestVNet**lehetőséget. Ha a **TestVNet** nem érhető el, győződjön meg arról, hogy az *USA középső* régióját használja, és létrehozta a cikk elején leírt tesztkörnyezetben.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. A **hálózat**területen győződjön meg arról, hogy az aktuálisan kiválasztott alhálózat *előtér, majd*válassza az **IP-címek**lehetőséget, majd az **IP-cím hozzárendelése** területen válassza a **statikus**lehetőséget, majd adja meg a *192.168.1.101* az **IP-címhez** a látható módon alábbi.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Válassza **az OK** lehetőséget az **IP-címek**területen, majd kattintson **az OK** elemre a **hálózat**területen, majd válassza **az OK** lehetőséget a **választható konfiguráció**területen.
7. A **virtuális gép létrehozása**területen válassza a **Létrehozás**lehetőséget. Figyelje meg az irányítópulton az alábbi csempét:
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan kérheti le a statikus magánhálózati IP-címadatok egy virtuális géphez
A fenti lépésekkel létrehozott virtuális gép statikus magánhálózati IP-címére vonatkozó információk megtekintéséhez hajtsa végre az alábbi lépéseket.

1. A Azure Portal válassza a **Tallózás minden** > **virtuális gép (klasszikus)**  > **DNS01** > **minden beállítás** > **IP-címe** lehetőséget, és figyelje meg az IP-cím hozzárendelését, és Az alább látható IP-cím.
   
    ![Virtuális gép létrehozása Azure Portalban](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Virtuális gép statikus privát IP-cím eltávolítása

Az **IP-címek**területen válassza a **dinamikus** lehetőséget az **IP-cím hozzárendeléstől**jobbra, válassza a **Mentés**lehetőséget, majd válassza az **Igen**lehetőséget, ahogy az alábbi képen látható:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Statikus magánhálózati IP-cím hozzáadása meglévő virtuális géphez

1. A korábban megjelenített **IP-címek**területen válassza a **statikus** lehetőséget az **IP-cím-hozzárendeléstől**jobbra.
2. Írja be az **IP-cím** *192.168.1.101* , válassza a **Mentés**lehetőséget, majd válassza az **Igen**lehetőséget.

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges. Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, győződjön meg arról, hogy-e az Azure virtuális Géphez rendelt magánhálózati IP-címet a címmel, vagy a virtuális gép is megszakad a kapcsolat. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="next-steps"></a>További lépések
* Ismerje meg [lefoglalt nyilvános IP-cím](virtual-networks-reserved-public-ip.md) címeket.
* Ismerje meg [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) címeket.
* Tekintse át a [fenntartott IP-REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx).

