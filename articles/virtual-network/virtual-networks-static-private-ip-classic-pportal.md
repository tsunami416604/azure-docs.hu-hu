---
title: Magánhálózati IP-címek konfigurálása virtuális gépek (klasszikus) – az Azure portálon |} Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a magánhálózati IP-címek a virtuális gépek (klasszikus) Azure portál használatával.
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
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791899"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Az Azure portál használatával egy virtuális gép (klasszikus) magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [kezelése a Resource Manager üzembe helyezési modellel statikus magánhálózati IP-cím](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

A minta lépések várhatóan a már létrehozott egy egyszerű környezetben. Ha azt szeretné, ebben a dokumentumban megjelenített futtatásához a lépéseket, először leírt tesztkörnyezet felépítéséhez [hozhat létre egy vnetet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>A statikus magánhálózati IP-cím megadása a virtuális gép létrehozásakor
Nevű virtuális gép létrehozása *DNS01* a a *előtér* egy vnet nevű alhálózat *TestVNet* statikus magánhálózati IP-címe a *192.168.1.101*, teljes az alábbi lépéseket:

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Válassza ki **új** > **számítási** > **Windows Server 2012 R2 Datacenter**, figyelje meg, hogy a **telepítésimodellkiválasztása** lista már bemutatja **klasszikus**, majd válassza ki **létrehozása**.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. A **hozzon létre virtuális gép**, adja meg a létrehozandó virtuális gép nevét (*DNS01* forgatókönyvben), a helyi rendszergazda fiókot és jelszót.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Válassza ki **opcionális konfigurációs** > **hálózati** > **virtuális hálózati**, majd válassza ki **TestVNet** . Ha **TestVNet** nem érhető el, győződjön meg arról, hogy használja a *USA középső RÉGIÓJA* helyét, és ez a cikk elején leírt tesztkörnyezet hozott létre.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. A **hálózati**, győződjön meg arról, hogy a jelenleg kijelölt alhálózat *előtér*, majd jelölje be **IP-címek**a **IP-cím hozzárendelése** Válassza ki **statikus**, majd adja meg *192.168.1.101* a **IP-cím** az alább látható módon.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Válassza ki **OK** alatt **IP-címek**, jelölje be **OK** alatt **hálózati**, majd válassza ki **OK** alatt **Opcionális konfigurációs**.
7. A **hozzon létre virtuális gép**, jelölje be **létrehozása**. Figyelje meg, az alábbi csempe az irányítópulton látható:
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan lehet lekérni a statikus magánhálózati IP-címadatok a virtuális gépek
A statikus magánhálózati IP-címadatok a virtuális gépnek a fent leírt lépésekkel létrehozott megtekintéséhez hajtsa végre az alábbi lépéseket.

1. Válassza ki az Azure-portálon **összes TALLÓZÁSA** > **virtuális gépek (klasszikus)** > **DNS01** > **összes beállítások** > **IP-címek** , és figyelje meg az IP-cím hozzárendelése és IP-cím, az alább látható módon.
   
    ![Virtuális gép létrehozása az Azure-portálon](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>A statikus magánhálózati IP-cím eltávolítása a virtuális gépek

A **IP-címek**, jelölje be **dinamikus** jobb oldalán **IP-cím hozzárendelése**, jelölje be **mentése**, majd válassza ki  **Igen**, az alábbi ábrán látható módon:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>A statikus magánhálózati IP-cím hozzáadása egy meglévő virtuális Gépen

1. A **IP-címek**, korábban látható, jelölje be **statikus** jobb oldalán **IP-cím hozzárendelése**.
2. Típus *192.168.1.101* a **IP-cím**, jelölje be **mentése**, majd válassza ki **Igen**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Javasoljuk, hogy nem statikusan rendelje a magánhálózati IP-címe az operációs rendszerben a virtuális gépek az Azure virtuális géphez rendelt kivéve, ha szükséges. Ha manuálisan állítsa be a magánhálózati IP-cím, az operációs rendszerből, győződjön meg arról, hogy-e a magánhálózati IP-cím, az Azure virtuális Géphez rendelt megegyező címre, vagy is megszakad a kapcsolat a virtuális géphez. Manuálisan soha ne rendelje a nyilvános IP-cím, egy Azure virtuális gépen belül a virtuális gép operációs rendszerének rendelt.

## <a name="next-steps"></a>További lépések
* További tudnivalók [foglalt nyilvános IP-cím](virtual-networks-reserved-public-ip.md) címek.
* További tudnivalók [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) címek.
* Tekintse át a [fenntartott IP-REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx).

