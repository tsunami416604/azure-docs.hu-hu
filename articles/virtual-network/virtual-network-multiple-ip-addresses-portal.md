---
title: "Több IP-címek az Azure virtuális gépek - portál |} Microsoft Docs"
description: "Megtudhatja, hogyan több IP-címek hozzárendelése a virtuális gép az Azure portál használatával |} Erőforrás-kezelő."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
ms.openlocfilehash: d264bd47d76db8015a64f09248c57c94572e2693
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Több IP-címek hozzárendelése a virtuális gépek az Azure portál használatával

>[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
>
Ez a cikk ismerteti (VM) virtuális gép létrehozása az Azure Resource Manager deployment használatával az Azure portál használatával. Több IP-cím nem lehet hozzárendelni a klasszikus üzembe helyezési modell használatával létrejött erőforrásokat. Az Azure üzembe helyezési modellel kapcsolatos további tudnivalókért olvassa el a [üzembe helyezési modellel megértéséhez](../resource-manager-deployment-model.md) cikk.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Több IP-címekkel rendelkező virtuális gép létrehozása

Ha szeretne létrehozni egy virtuális gép több IP-címet, vagy hozzá statikus magánhálózati IP-cím, hozza létre azt a PowerShell vagy az Azure parancssori felület használatával. Kattintson a PowerShell vagy a CLI beállításokkal Ez a cikk tetején megtudhatja, hogyan. Létrehozhat egy virtuális Gépet egy egyetlen dinamikus magánhálózati IP-cím, és (opcionálisan) a lépések a portál használatával egyetlen nyilvános IP-címet a [Windows virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md) vagy [hozzon létre egy Linux virtuális Gépet](../virtual-machines/linux/quick-create-portal.md) cikkeket. A virtuális gép létrehozása után módosítsa az IP-cím típusa a dinamikus statikus, és kövesse a portál használatával további IP-címek hozzáadása a [hozzáadása IP-címek egy virtuális géphez](#add) című szakaszát.

## <a name="add"></a>IP-címek hozzáadása a virtuális gépek

Egy hálózati adapterre privát és nyilvános IP-címeket adhat hozzá a következő lépések végrehajtásával. A következő szakaszokban szereplő példák azt feltételezik, hogy már van egy virtuális gép leírt három IP-konfigurációk a [forgatókönyv](#Scenario) ezen cikk, de nem szükséges, hogy végezzen.

### <a name="coreadd"></a>Alapvető lépéseket

1. Tallózással keresse meg az Azure-portálon https://portal.azure.com és bejelentkezési is, ha szükséges.
2. Kattintson a portál **további szolgáltatások** > típus *virtuális gépek* a Szűrő mezőbe, majd **virtuális gépek**.
3. Az a **virtuális gépek** panelen, kattintson a virtuális gép hozzá szeretné adni az IP-címek hozzárendelését. Kattintson a **hálózati illesztőt** a virtuális gép panel, amely akkor jelenik meg, és válassza a hálózati illesztő hozzá szeretné adni az IP-címek. A példában a következő ábrán látható, a hálózati adapter neve *myNIC* nevű virtuális gép *myVM* van kiválasztva:

    ![Hálózati illesztő](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. A kiválasztott hálózati adapter megjelenő panelen kattintson **IP-konfigurációk**.

Hajtsa végre a következő szakaszok, egyikében hozzáadni kívánt IP-cím típusa alapján.

### <a name="add-a-private-ip-address"></a>**Adja hozzá a magánhálózati IP-cím**

Az alábbi lépésekkel egy új magánhálózati IP-cím hozzáadása:

1. Hajtsa végre a a [alapvető lépéseket](#coreadd) című szakaszát.
2. Kattintson az **Add** (Hozzáadás) parancsra. Az a **hozzáadása IP-konfiguráció** panel, amely akkor jelenik meg, egy IP-konfiguráció létrehozásának *IPConfig-4* a *10.0.0.7* , egy *statikus* magánhálózati IP-címe címet, majd kattintson az **OK**.

    > [!NOTE]
    > Egy statikus IP-cím hozzáadásakor meg kell adnia egy nem használt, érvényes címet a hálózati adapter csatlakozik-e az alhálózaton. Ha bejelöli a cím nem érhető el, a portál megjeleníti az IP-címhez X, és válasszon egy másik kell.

3. Miután az OK gombra kattint, a panel bezárul, és látni fogja, az új IP-konfiguráció látható. Kattintson a **OK** bezárásához a **hozzáadása IP-konfiguráció** panelen.
4. Kattinthat **Hozzáadás** további IP-konfiguráció hozzáadásához vagy zárjon be minden nyitott paneleken IP-címek hozzáadásának befejezéséhez.
5. A privát IP-címek hozzáadása a virtuális gép operációs rendszer, az operációs rendszernek a lépések végrehajtásával a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát.

### <a name="add-a-public-ip-address"></a>A nyilvános IP-cím hozzáadása

Egy nyilvános IP-címet vagy egy új IP-konfiguráció, vagy egy meglévő IP-konfiguráció a nyilvános IP-cím erőforrás társításával jelenik meg.

> [!NOTE]
> Nyilvános IP-címek névleges díjat kell. IP-cím árazással kapcsolatos további tudnivalókért olvassa el a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses) lap. Nyilvános IP-címek egy előfizetésben használható száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.
> 

### <a name="create-public-ip"></a>Hozzon létre egy nyilvános IP-cím erőforrás

Egy nyilvános IP-címet az egyik beállítása egy nyilvános IP-cím erőforrás. Ha egy nyilvános IP-cím erőforrás, amely nem rendelheti hozzá az IP-konfigurációt használni kívánt IP-konfigurációt jelenleg társítva van, hagyja ki a következő lépéseket, és az összes kívánt beállítást, hajtsa végre a következő szakaszok, egyik lépéseit. Ha nincs elérhető nyilvános IP-cím erőforrás, végezze el az alábbi lépéseket követve létrehozhat egyet:

1. Tallózással keresse meg az Azure-portálon https://portal.azure.com és bejelentkezési is, ha szükséges.
3. Kattintson a portál **új** > **hálózati** > **nyilvános IP-cím**.
4. A a **nyilvános IP-cím létrehozása** panel, amelyen megjelenik, írja be egy **neve**, jelölje be egy **IP-cím hozzárendelése** típus, egy **előfizetés**, egy **erőforráscsoport**, és egy **hely**, kattintson a **létrehozása**, az alábbi ábrán látható módon:

    ![Hozzon létre egy nyilvános IP-cím erőforrás](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Hajtsa végre a társítja a nyilvános IP-cím erőforrás IP-konfigurációt a következő szakaszok egyikében.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Társítsa a nyilvános IP-cím erőforrás új IP-konfigurációhoz

1. Hajtsa végre a a [alapvető lépéseket](#coreadd) című szakaszát.
2. Kattintson az **Add** (Hozzáadás) parancsra. Az a **hozzáadása IP-konfiguráció** panel, amely akkor jelenik meg, egy IP-konfiguráció létrehozásának *IPConfig-4*. Engedélyezze a **nyilvános IP-cím** , és válasszon egy meglévő, elérhető nyilvános IP-cím erőforrás a a **nyilvános IP-cím kiválasztása** panel, amely akkor jelenik meg.

    A nyilvános IP-cím erőforrás kijelölése után kattintson **OK** és a blade bezárul. Ha egy meglévő nyilvános IP-cím nincs, létrehozhat egy, a lépések végrehajtásával a [hozzon létre egy nyilvános IP-cím erőforrás](#create-public-ip) című szakaszát. 

3. Tekintse át az új IP-konfigurációt. Annak ellenére, hogy a magánhálózati IP-cím nincs explicit módon hozzárendelt, egy lett automatikusan IP-konfigurációja, mivel az összes IP-konfigurációk rendelkeznie kell egy magánhálózati IP-cím.
4. Kattinthat **Hozzáadás** további IP-konfiguráció hozzáadásához vagy zárjon be minden nyitott paneleken IP-címek hozzáadásának befejezéséhez.
5. A magánhálózati IP-cím hozzáadása a virtuális gép operációs rendszer, az operációs rendszernek a lépések végrehajtásával a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegyen fel a nyilvános IP-cím az operációs rendszer.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>A nyilvános IP-cím erőforrás egy meglévő IP-konfiguráció való társítása

1. Hajtsa végre a a [alapvető lépéseket](#coreadd) című szakaszát.
2. Kattintson az IP-konfiguráció a nyilvános IP-cím erőforrás való hozzáadásához.
3. Az IPConfig megjelenő panelen kattintson **IP-cím**.
4. Az a **nyilvános IP-cím kiválasztása** panel, amelyen megjelenik, válassza ki a nyilvános IP-cím.
5. Kattintson a **mentése** és a paneleken bezárul. Ha egy meglévő nyilvános IP-cím nincs, létrehozhat egy, a lépések végrehajtásával a [hozzon létre egy nyilvános IP-cím erőforrás](#create-public-ip) című szakaszát.
3. Tekintse át az új IP-konfigurációt.
4. Kattinthat **Hozzáadás** további IP-konfiguráció hozzáadásához vagy zárjon be minden nyitott paneleken IP-címek hozzáadásának befejezéséhez. Ne vegyen fel a nyilvános IP-cím az operációs rendszer.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
