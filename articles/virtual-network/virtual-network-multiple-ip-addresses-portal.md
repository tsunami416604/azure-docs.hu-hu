---
title: Több IP-címek az Azure virtual machines – portál |} A Microsoft Docs
description: Ismerje meg, hogyan több IP-cím hozzárendelése a virtuális gépek az Azure portal használatával |} Erőforrás-kezelő.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
ms.openlocfilehash: 0f36ab9ab7703dd9677b6c2989eddb91593af79f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105107"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Több IP-cím hozzárendelése a virtuális gépek az Azure portal használatával

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Ez a cikk azt ismerteti, hogyan hozhat létre egy virtuális gépet (VM) az Azure Resource Manager-alapú üzemi modellel, az Azure portal használatával. Több IP-cím nem lehet hozzárendelni a klasszikus üzemi modellel létrehozott erőforrásokat. Azure üzembehelyezési modellekkel kapcsolatos további tudnivalókért olvassa el a [üzembe helyezési modellek ismertetése](../resource-manager-deployment-model.md) cikk.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

Ha szeretne egy virtuális Gépet több IP-cím vagy egy statikus magánhálózati IP-cím létrehozásához, létre kell hoznia azt a PowerShell vagy az Azure CLI használatával. Megtudhatja, hogyan, jelölje be a PowerShell vagy parancssori felület beállításokat, ez a cikk elején. Létrehozhat egy virtuális Gépet egy dinamikus magánhálózati IP-címet, és (opcionálisan) egy nyilvános IP-címet. A portál használata a lépések a [Windows virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md) vagy [hozzon létre egy Linux rendszerű virtuális gép](../virtual-machines/linux/quick-create-portal.md) cikkeket. Miután létrehozta a virtuális gép, dinamikusról az IP-címtípust statikusra, és a portál használatával, kövesse a további IP-címek hozzáadása a [hozzáadása IP-címek egy virtuális géphez](#add) című szakaszát.

## <a name="add"></a>IP-címek hozzáadása egy virtuális Géphez

Egy Azure-beli hálózati interfészhez privát és nyilvános IP-címek az alábbi lépéseket követve adhat hozzá. Az alábbi szakaszokban található példák feltételezik, hogy már rendelkezik egy virtuális gép leírt három IP-konfigurációk a [forgatókönyv](#Scenario), de ez nem szükséges.

### <a name="coreadd"></a>Alapvető lépéseket

1. Tallózással keresse meg az Azure Portalra a https://portal.azure.com , és jelentkezzen be, szükség esetén.
2. Kattintson a portál **további szolgáltatások** > típus *virtuális gépek* a Szűrő mezőbe, és kattintson a **virtuális gépek**.
3. Az a **virtuális gépek** ablaktáblán, kattintson a virtuális Gépet szeretne hozzáadni az IP-címek. Kattintson a **hálózati adapterek** a virtuális gép panel, amely akkor jelenik meg, és válassza ki a hálózati adaptert szeretne hozzáadni az IP-címek. A példában az alábbi képen is látható, a hálózati adapter neve *myNIC* nevű virtuális gépről *myVM* van kiválasztva:

    ![Hálózati illesztő](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. A kiválasztott hálózati adapterhez megjelenő ablaktáblában kattintson **IP-konfigurációk**.

Hajtsa végre a következő szakaszokban áttekintendő, valamelyik hozzáadni kívánt IP-cím típusa alapján.

### <a name="add-a-private-ip-address"></a>**Magánhálózati IP-cím hozzáadása**

A következő lépéseket egy új privát IP-cím hozzáadása:

1. A lépések elvégzéséhez a [alapvető lépéseket](#coreadd) című szakaszát.
2. Kattintson a **Hozzáadás** parancsra. Az a **hozzáadása IP-konfiguráció** panelen megjelenő hozzon létre egy IP-konfiguráció nevű *IPConfig-4* a *10.0.0.7* , egy *statikus* magánhálózati IP-cím, majd kattintson a **OK**.

    > [!NOTE]
    > Statikus IP-cím hozzáadásakor meg kell adnia egy nem használt, érvényes címet a hálózati adapter csatlakoztatva van az alhálózaton. Ha kiválasztja a cím nem érhető el, a portál megjeleníti az IP-cím X, és jelöljön ki egy másik.

3. Miután az OK gombra kattint, a panel bezárul, és láthatja a felsorolt új IP-konfigurációja. Kattintson a **OK** gombra kattintva zárja be a **hozzáadása IP-konfiguráció** ablaktáblán.
4. Kattinthat **Hozzáadás** további IP-konfigurációk hozzáadásához, vagy IP-címek hozzáadásának befejezéséhez minden megnyitott paneljeinek a bezárásához.
5. A magánhálózati IP-címek a virtuális gép operációs rendszer által ismertetett lépéseket követve adja hozzá a [hozzáadása IP-címek a virtuális gép operációs rendszerre](#os-config) című szakaszát.

### <a name="add-a-public-ip-address"></a>Nyilvános IP-cím hozzáadása

Nyilvános IP-cím vagy egy új IP-konfigurációt, vagy egy meglévő IP-konfiguráció nyilvános IP-cím erőforrás társításával egészül ki.

> [!NOTE]
> Nyilvános IP-címek egy névleges díj rendelkezik. IP-címek díjszabása kapcsolatos további információkért olvassa el a [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses) lapot. Egy előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.
> 

### <a name="create-public-ip"></a>Hozzon létre egy nyilvános IP-cím erőforrás

Nyilvános IP-cím egy nyilvános IP-cím erőforráshoz egy beállítást. Ha egy nyilvános IP-cím erőforrás, amely nem rendelheti hozzá az IP-konfiguráció kívánt IP-konfigurációval jelenleg társítva van, hagyja ki a következő lépéseket, és hajtsa végre a egyet a következő szakaszokban áttekintendő, amennyi szükséges. Ha nem rendelkezik használható nyilvános IP-cím erőforrás, hajtsa végre a hozzon létre egyet az alábbi lépéseket:

1. Tallózással keresse meg az Azure Portalra a https://portal.azure.com , és jelentkezzen be, szükség esetén.
3. Kattintson a portál **erőforrás létrehozása** > **hálózatkezelés** > **nyilvános IP-cím**.
4. Az a **nyilvános IP-cím létrehozása** megjelenő panelen írja be a **neve**, jelölje be egy **IP-cím hozzárendelése** típus, egy **előfizetés**, amely egy **Erőforráscsoport**, és a egy **hely**, majd kattintson a **létrehozás**, ahogy az alábbi képen is látható:

    ![Hozzon létre egy nyilvános IP-cím erőforrás](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. A lépéseket követve társítsa a nyilvános IP-címerőforrás IP-konfigurációhoz a következő szakaszok egyikében.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Társítsa a nyilvános IP-cím erőforrás egy új IP-konfigurációhoz

1. A lépések elvégzéséhez a [alapvető lépéseket](#coreadd) című szakaszát.
2. Kattintson a **Hozzáadás** parancsra. Az a **hozzáadása IP-konfiguráció** panelen megjelenő hozzon létre egy IP-konfiguráció nevű *IPConfig-4*. Engedélyezze a **nyilvános IP-cím** , és válassza ki egy meglévő, elérhető nyilvános IP-cím erőforrás a a **nyilvános IP-cím választása** megjelenő panelen.

    Miután kiválasztotta a nyilvános IP-cím erőforrás, kattintson a **OK** és a panel bezárul. Ha nem rendelkezik egy meglévő nyilvános IP-cím, létrehozhat egyet a lépéseket követve a [hozzon létre egy nyilvános IP-cím erőforrás](#create-public-ip) című szakaszát. 

3. Tekintse át az új IP-konfigurációt. Annak ellenére, hogy magánhálózati IP-cím nincs explicit módon hozzá van rendelve, egy volt automatikusan IP-konfigurációja, mivel az összes IP-konfigurációk magánhálózati IP-cím kell rendelkeznie.
4. Kattinthat **Hozzáadás** további IP-konfigurációk hozzáadásához, vagy IP-címek hozzáadásának befejezéséhez minden megnyitott paneljeinek a bezárásához.
5. A magánhálózati IP-cím hozzáadása a virtuális gép operációs rendszeréhez, az operációs rendszernek a lépéseket követve a [hozzáadása IP-címek a virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegye fel a nyilvános IP-címet az operációs rendszer.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>A nyilvános IP-cím erőforrás egy meglévő IP-konfiguráció hozzárendelése

1. A lépések elvégzéséhez a [alapvető lépéseket](#coreadd) című szakaszát.
2. Kattintson a nyilvános IP-cím erőforrás a hozzáadni kívánt IP-konfigurációja.
3. A megjelenő IP-konfiguráció ablakában kattintson **IP-cím**.
4. Az a **nyilvános IP-cím választása** megjelenő panelen jelölje ki a nyilvános IP-cím.
5. Kattintson a **mentése** és panel bezárásához. Ha nem rendelkezik egy meglévő nyilvános IP-cím, létrehozhat egyet a lépéseket követve a [hozzon létre egy nyilvános IP-cím erőforrás](#create-public-ip) című szakaszát.
3. Tekintse át az új IP-konfigurációt.
4. Kattinthat **Hozzáadás** további IP-konfigurációk hozzáadásához, vagy IP-címek hozzáadásának befejezéséhez minden megnyitott paneljeinek a bezárásához. Ne vegye fel a nyilvános IP-címet az operációs rendszer.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
