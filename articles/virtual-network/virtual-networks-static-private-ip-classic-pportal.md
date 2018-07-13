---
title: Magánhálózati IP-címek konfigurálása a virtuális gépek (klasszikus) – az Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a magánhálózati IP-címek a virtuális gépek (klasszikus) az Azure portal használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 1aae74d8077a75e5ab556703b1c1531f540bbdb4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698564"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Az Azure portal használatával egy virtuális gép (klasszikus) magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [kezelése a Resource Manager-alapú üzemi modellben statikus magánhálózati IP-cím](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

A minta szükséges a már létrehozott egy egyszerű környezetben várható. Ha azt szeretné, hajtsa végre a lépéseket, a jelen dokumentum megjelenített, először hozhat létre a leírt tesztkörnyezet [hozzon létre egy vnetet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus magánhálózati IP-cím megadása a virtuális gép létrehozásakor
Nevű virtuális gép létrehozása *DNS01* a a *előtérbeli* nevű virtuális hálózat alhálózatának *TestVNet* egy statikus magánhálózati IP-címét *192.168.1.101*, teljes az alábbi lépéseket:

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Válassza ki **új** > **számítási** > **Windows Server 2012 R2 Datacenter**, vegye figyelembe, hogy a **telepítésimodellkiválasztása** listájában már **klasszikus**, majd válassza ki **létrehozás**.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. A **virtuális gép létrehozása**, adja meg a létrehozandó virtuális gép nevét (*DNS01* forgatókönyvben), a helyi rendszergazdai fiókot és jelszót.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Válassza ki **opcionális konfigurációs** > **hálózati** > **virtuális hálózat**, majd válassza ki **TestVNet** . Ha **TestVNet** nem érhető el, győződjön meg arról, hogy használ a *USA középső RÉGIÓJA* helyét, és ez a cikk elején leírt tesztkörnyezet hozott létre.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Alatt **hálózati**, ellenőrizze, hogy az aktuálisan kiválasztott alhálózat *előtérbeli*, majd **IP-címek**alatt **IP-cím hozzárendelése** Válassza ki **statikus**, majd adja meg *192.168.1.101* a **IP-cím** az alább látható módon.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Válassza ki **OK** alatt **IP-címek**válassza **OK** alatt **hálózati**, majd válassza ki **OK** alatt **Választható beállítások**.
7. A **virtuális gép létrehozása**válassza **létrehozás**. Figyelje meg, hogy az irányítópulton megjelenő csempén az alábbi:
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan kérheti le a statikus magánhálózati IP-címadatok egy virtuális géphez
A statikus magánhálózati IP-címadatok a fenti lépésekkel létrehozott virtuális gép megtekintéséhez futtassa az alábbi lépéseket.

1. Az Azure Portalon, válassza ki a **összes TALLÓZÁSA** > **virtuális gépek (klasszikus)** > **DNS01** > **összes beállítások** > **IP-címek** , és figyelje meg az IP-cím hozzárendelése és IP-cím, az alább látható módon.
   
    ![Virtuális gép létrehozása az Azure Portalon](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Virtuális gép statikus privát IP-cím eltávolítása

Alatt **IP-címek**, jelölje be **dinamikus** jobb oldalán **IP-cím hozzárendelése**válassza **mentése**, majd válassza ki  **Igen**, ahogy az alábbi képen is látható:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Meglévő virtuális géphez statikus magánhálózati IP-cím hozzáadása

1. Alatt **IP-címek**, a korábban bemutatott, a select **statikus** jobb oldalán **IP-cím hozzárendelése**.
2. Típus *192.168.1.101* a **IP-cím**, jelölje be **mentése**, majd válassza ki **Igen**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges. Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, győződjön meg arról, hogy-e az Azure virtuális Géphez rendelt magánhálózati IP-címet a címmel, vagy a virtuális gép is megszakad a kapcsolat. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="next-steps"></a>További lépések
* Ismerje meg [lefoglalt nyilvános IP-cím](virtual-networks-reserved-public-ip.md) címeket.
* Ismerje meg [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) címeket.
* Tekintse át a [fenntartott IP-REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx).

