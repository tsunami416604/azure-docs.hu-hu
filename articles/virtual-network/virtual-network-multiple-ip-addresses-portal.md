---
title: Több IP-cím az Azure Virtual Machines-portálon | Microsoft Docs
description: Megtudhatja, hogyan rendelhet hozzá több IP-címet egy virtuális géphez a Azure Portal használatával | Resource Manager.
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
ms.author: anavin
ms.openlocfilehash: 66cbb843369dee103f102c9c743da544a833ccf1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356606"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Több IP-cím hozzárendelését a virtuális gépekhez a Azure Portal használatával

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Ez a cikk azt ismerteti, hogyan hozhat létre virtuális gépet (VM) a Azure Resource Manager üzemi modellel a Azure Portal használatával. Több IP-cím nem rendelhető hozzá a klasszikus üzemi modellel létrehozott erőforrásokhoz. Ha többet szeretne megtudni az Azure-beli üzembe helyezési modellekről, olvassa el a [telepítési modellek ismertetése](../resource-manager-deployment-model.md) című cikket.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

Ha több IP-címmel vagy statikus magánhálózati IP-címmel rendelkező virtuális gépet szeretne létrehozni, akkor a PowerShell vagy az Azure CLI használatával kell létrehoznia. A cikk elején található PowerShell-vagy parancssori felületi beállításokkal kapcsolatos további információkért kattintson a. Létrehozhat egy virtuális gépet egyetlen dinamikus magánhálózati IP-címmel, és (opcionálisan) egyetlen nyilvános IP-címet is. Használja a portált a [Windows rendszerű virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md) vagy [linuxos virtuális gép létrehozása](../virtual-machines/linux/quick-create-portal.md) című cikk lépéseit követve. A virtuális gép létrehozása után megváltoztathatja az IP-cím típusát a dinamikusról statikusra, és további IP-címeket adhat hozzá a portálon az [IP-címek hozzáadása a virtuális géphez](#add) című szakaszban ismertetett lépéseket követve.

## <a name="add"></a>IP-címek hozzáadása egy virtuális géphez

A következő lépések végrehajtásával adhat hozzá privát és nyilvános IP-címeket egy Azure hálózati adapterhez. Az alábbi szakaszokban szereplő példák azt feltételezik, hogy már rendelkezik a [forgatókönyvben](#scenario)ismertetett három IP-konfigurációval rendelkező virtuális géppel, de nem kötelező.

### <a name="coreadd"></a>Alapvető lépések

1. Szükség esetén keresse meg a Azure Portal https://portal.azure.com és jelentkezzen be.
2. A portálon kattintson a **További szolgáltatások** > írja be a *virtuális gépek* értéket a szűrő mezőbe, majd kattintson a **virtuális gépek**elemre.
3. A **virtuális gépek** ablaktáblán kattintson arra a virtuális gépre, amelyhez IP-címeket szeretne hozzáadni. A megjelenő virtuális gép ablaktáblán kattintson a **hálózati adapterek** elemre, majd válassza ki azt a hálózati adaptert, amelyhez hozzá kívánja adni az IP-címeket. Az alábbi képen látható példában a *myVM* nevű virtuális gépről a *myNIC* nevű hálózati adapter van kiválasztva:

    ![Hálózati illesztő](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. A kiválasztott hálózati adapterhez megjelenő ablaktáblán kattintson az **IP-konfigurációk**elemre.

Hajtsa végre az alábbi szakaszokban ismertetett lépéseket a hozzáadni kívánt IP-cím típusától függően.

### <a name="add-a-private-ip-address"></a>**Magánhálózati IP-cím hozzáadása**

Új magánhálózati IP-cím hozzáadásához hajtsa végre a következő lépéseket:

1. Hajtsa végre a jelen cikk [alapvető lépések](#coreadd) szakaszának lépéseit.
2. Kattintson az **Hozzáadás** parancsra. A megjelenő **IP-konfiguráció hozzáadása** panelen hozzon létre egy *ipconfig-4* nevű IP-KONFIGURÁCIÓt *statikus* magánhálózati IP-címként a *10.0.0.7* , majd kattintson **az OK**gombra.

    > [!NOTE]
    > Statikus IP-cím hozzáadásakor meg kell adnia egy nem használt, érvényes címet azon az alhálózaton, amelyhez a hálózati adapter csatlakozik. Ha a kiválasztott cím nem érhető el, a portál egy X értéket jelenít meg az IP-címhez, és ki kell választania egy másikat.

3. Miután rákattintott az OK gombra, a panel bezárul, és megjelenik a felsorolt új IP-konfiguráció. Az **IP-konfiguráció hozzáadása** ablaktábla bezárásához kattintson **az OK** gombra.
4. A **Hozzáadás** gombra kattintva további IP-konfigurációkat adhat hozzá, illetve az IP-címek hozzáadásának befejezéséhez lezárhatja az összes megnyitott pengét.
5. Adja hozzá a magánhálózati IP-címeket a virtuális gép operációs rendszeréhez az [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszban ismertetett lépések végrehajtásával.

### <a name="add-a-public-ip-address"></a>Nyilvános IP-cím hozzáadása

A nyilvános IP-címet egy nyilvános IP-cím típusú erőforrás társításával vagy egy új IP-konfigurációhoz, vagy egy meglévő IP-konfigurációhoz társítja.

> [!NOTE]
> A nyilvános IP-címekhez névleges díj vonatkozik. Ha többet szeretne megtudni az IP-címek díjszabásáról, olvassa el az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.
> 

### <a name="create-public-ip"></a>Nyilvános IP-cím erőforrás létrehozása

A nyilvános IP-cím a nyilvános IP-cím erőforrásának egyik beállítása. Ha van olyan nyilvános IP-cím erőforrása, amely jelenleg nincs olyan IP-konfigurációhoz társítva, amelyet egy IP-konfigurációhoz szeretne társítani, ugorja át a következő lépéseket, és hajtsa végre az alábbi lépések egyikét, ahogy szükséges. Ha nincs elérhető nyilvános IP-cím erőforrása, a következő lépésekkel hozhat létre egyet:

1. Szükség esetén keresse meg a Azure Portal https://portal.azure.com és jelentkezzen be.
3. A portálon kattintson az **erőforrás létrehozása** > **hálózatkezelés** > **nyilvános IP-cím**elemre.
4. A megjelenő **nyilvános IP-cím létrehozása** panelen adjon meg egy **nevet**, válasszon ki egy **IP-cím-hozzárendelési** típust, egy **előfizetést**, egy **erőforráscsoportot**és egy **helyet**, majd kattintson a **Létrehozás**gombra, ahogy az alábbi képen látható:

    ![Nyilvános IP-cím erőforrás létrehozása](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. A következő szakaszokban ismertetett lépések végrehajtásával rendelje hozzá a nyilvános IP-cím erőforrást egy IP-konfigurációhoz.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>A nyilvános IP-cím erőforrásának hozzárendelése új IP-konfigurációhoz

1. Hajtsa végre a jelen cikk [alapvető lépések](#coreadd) szakaszának lépéseit.
2. Kattintson az **Hozzáadás** parancsra. A megjelenő **IP-konfiguráció hozzáadása** panelen hozzon létre egy *ipconfig-4*nevű IP-konfigurációt. Engedélyezze a **nyilvános IP-címet** , és válasszon ki egy meglévő, elérhető nyilvános IP-cím erőforrást a megjelenő **nyilvános IP-cím kiválasztása** ablaktáblán.

    Miután kiválasztotta a nyilvános IP-cím erőforrást, kattintson az **OK** gombra, és zárja be a panelt. Ha nem rendelkezik meglévő nyilvános IP-címmel, létrehozhat egyet a jelen cikk [nyilvános IP-cím létrehozása erőforrásának létrehozása](#create-public-ip) című szakaszában ismertetett lépések végrehajtásával. 

3. Tekintse át az új IP-konfigurációt. Bár a magánhálózati IP-címek nem lettek explicit módon hozzárendelve, az egyiket a rendszer automatikusan hozzárendelte az IP-konfigurációhoz, mert az összes IP-konfigurációnak magánhálózati IP-címmel kell rendelkeznie.
4. A **Hozzáadás** gombra kattintva további IP-konfigurációkat adhat hozzá, illetve az IP-címek hozzáadásának befejezéséhez lezárhatja az összes megnyitott pengét.
5. Adja hozzá a magánhálózati IP-címet a virtuális gép operációs rendszeréhez úgy, hogy végrehajtja a jelen cikk [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszának lépéseit. Ne adja hozzá a nyilvános IP-címet az operációs rendszerhez.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>A nyilvános IP-cím erőforrásának hozzárendelése meglévő IP-konfigurációhoz

1. Hajtsa végre a jelen cikk [alapvető lépések](#coreadd) szakaszának lépéseit.
2. Kattintson arra az IP-konfigurációra, amelyhez hozzá szeretné adni a nyilvános IP-cím erőforrást.
3. A megjelenő IPConfig ablaktáblán kattintson az **IP-cím**elemre.
4. A megjelenő **nyilvános IP-cím kiválasztása** panelen válassza ki a nyilvános IP-címet.
5. Kattintson a **Save (Mentés** ) gombra, és a Bezárás ablaktáblán Ha nem rendelkezik meglévő nyilvános IP-címmel, létrehozhat egyet a jelen cikk [nyilvános IP-cím létrehozása erőforrásának létrehozása](#create-public-ip) című szakaszában ismertetett lépések végrehajtásával.
3. Tekintse át az új IP-konfigurációt.
4. A **Hozzáadás** gombra kattintva további IP-konfigurációkat adhat hozzá, illetve az IP-címek hozzáadásának befejezéséhez lezárhatja az összes megnyitott pengét. Ne adja hozzá a nyilvános IP-címet az operációs rendszerhez.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
