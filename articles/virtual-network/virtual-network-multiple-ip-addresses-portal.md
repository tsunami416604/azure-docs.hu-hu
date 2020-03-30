---
title: Több IP-cím az Azure virtuális gépekhez - Portal | Microsoft dokumentumok
description: Megtudhatja, hogy miként rendelhet több IP-címet egy virtuális géphez az Azure Portal használatával | Erőforrás-kezelő.
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
ms.openlocfilehash: a5bb9bb9c584aef8ac79b3c275d01d3c498da843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060607"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Több IP-cím hozzárendelése virtuális gépekhez az Azure Portal használatával

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Ez a cikk bemutatja, hogyan hozhat létre egy virtuális gépet (VM) az Azure Resource Manager üzembe helyezési modelljén keresztül az Azure Portalhasználatával. A klasszikus központi telepítési modellen keresztül létrehozott erőforrásokhoz nem rendelhető hozzá több IP-cím. Ha többet szeretne megtudni az Azure üzembe helyezési modellekről, olvassa el a [Telepítési modellek ismertetése](../resource-manager-deployment-model.md) című cikket.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

Ha több IP-címmel vagy statikus privát IP-címmel szeretne létrehozni egy virtuális gép, létre kell hoznia azt a PowerShell vagy az Azure CLI használatával. Ebből a cikkből megtudhatja, hogyan, kattintson a powershell- vagy CLI-beállításokra a cikk tetején. Létrehozhat egy virtuális gép egyetlen dinamikus privát IP-címet, és (opcionálisan) egy nyilvános IP-címet. Használja a portált a [Windows virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md) vagy [linuxos virtuális gép](../virtual-machines/linux/quick-create-portal.md) ről szóló cikkek létrehozása című lépések végrehajtásával. A virtuális gép létrehozása után módosíthatja az IP-cím típusát dinamikusról statikusra, és további IP-címeket adhat hozzá a portál használatával az [IP-címek hozzáadása a](#add) cikk virtuális géphez szakaszában leírt lépéseket követve.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-címek hozzáadása virtuális géphez

A következő lépések végrehajtásával hozzáadhat magán- és nyilvános IP-címeket egy Azure-hálózati adapterhez. A következő szakaszokban található példák feltételezik, hogy már rendelkezik egy virtuális gép a [forgatókönyvben](#scenario)leírt három IP-konfigurációval, de ez nem szükséges.

### <a name="core-steps"></a><a name="coreadd"></a>Alapvető lépések

1. Tallózzon az https://portal.azure.com Azure Portalon, és szükség esetén jelentkezzen be.
2. A portálon kattintson a **További szolgáltatások** > írja be a *virtuális gépeket* a szűrőmezőbe, majd kattintson a **Virtuális gépek**gombra.
3. A **Virtuális gépek** ablaktáblán kattintson arra a virtuális gépre, amelyhez IP-címeket szeretne hozzáadni. Keresse meg **a** Hálózat **Network interface** lapot. Amint az az alábbi képen látható: 


    ![Nyilvános IP-cím hozzáadása virtuális géphez](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. A **Hálózati adapter** ablaktáblán kattintson az **IP-konfigurációkra.**

5. A kijelölt hálózati adapterhez megjelenő ablaktáblán kattintson az **IP-konfigurációk**elemre. Kattintson a **Hozzáadás**gombra, hajtsa végre a következő szakaszok egyikének lépéseit a hozzáadni kívánt IP-cím típusától függően, majd kattintson az **OK**gombra. 

### <a name="add-a-private-ip-address"></a>**Privát IP-cím hozzáadása**

Új privát IP-cím hozzáadásához hajtsa végre az alábbi lépéseket:

1. Hajtsa végre a cikk [Alapvető lépések](#coreadd) szakaszának lépéseit.
2. Kattintson a **Hozzáadás** gombra. A megjelenő **IP-konfiguráció hozzáadása** ablaktáblán hozzon létre egy *IPConfig-4* nevű IP-konfigurációt *10.0.0.7* *statikus* privát IP-címként, majd kattintson **az OK**gombra.

    > [!NOTE]
    > Statikus IP-cím hozzáadásakor meg kell adnia egy nem használt, érvényes címet azon az alhálózaton, amelyhez a hálózati adapter csatlakozik. Ha a kiválasztott cím nem érhető el, a portál x-et jelenít meg az IP-címhez, és ki kell választania egy másikat.

3. Miután az OK gombra kattintott, az ablaktábla bezárul, és megjelenik az új IP-konfiguráció. Kattintson az **OK** gombra az **IP hozzáadása konfigurációs** ablaktábla bezárásához.
4. A **Hozzáadás** gombra kattintva további IP-konfigurációkat adhat hozzá, vagy bezárhatja az összes megnyitott panelt az IP-címek hozzáadásához.
5. Adja hozzá a privát IP-címeket a virtuális gép operációs rendszeréhez a cikk [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszában leírt lépések végrehajtásával.

### <a name="add-a-public-ip-address"></a>Nyilvános IP-cím hozzáadása

A nyilvános IP-cím hozzáadódik azáltal, hogy egy nyilvános IP-cím erőforrást társít egy új IP-konfigurációhoz vagy egy meglévő IP-konfigurációhoz.

> [!NOTE]
> A nyilvános IP-címek névleges díjkötelesek. Ha többet szeretne megtudni az IP-cím díjszabásáról, olvassa el az [IP-cím díjszabási](https://azure.microsoft.com/pricing/details/ip-addresses) oldalát. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Nyilvános IP-címerőforrás létrehozása

A nyilvános IP-cím egy nyilvános IP-cím erőforrás egyik beállítása. Ha olyan nyilvános IP-címerőforrással rendelkezik, amely jelenleg nincs ip-konfigurációhoz társítva, és amelyet egy IP-konfigurációhoz kíván társítani, hagyja ki a következő lépéseket, és végezze el a következő szakaszok egyikének lépéseit, ahogy azt szeretné. Ha nem rendelkezik elérhető nyilvános IP-címerőforrással, az alábbi lépésekkel hozzon létre egyet:

1. Tallózzon az https://portal.azure.com Azure Portalon, és szükség esetén jelentkezzen be.
3. A portálon kattintson az Erőforrás > **létrehozása nyilvános** > **IP-cím** **elemre.**
4. A megjelenő **Nyilvános IP-cím létrehozása** ablaktáblán adjon meg egy **Nevet**, válasszon egy **IP-címhozzárendelés-típust,** egy **előfizetést**, egy **erőforráscsoportot**és egy **helyet,** majd kattintson a **Létrehozás**gombra az alábbi képen látható módon:

    ![Nyilvános IP-címerőforrás létrehozása](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Hajtsa végre a nyilvános IP-cím erőforrás IP-konfigurációhoz való társítására követendő szakaszok egyikének lépéseit.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>A nyilvános IP-cím erőforrás társítása új IP-konfigurációhoz

1. Hajtsa végre a cikk [Alapvető lépések](#coreadd) szakaszának lépéseit.
2. Kattintson a **Hozzáadás** gombra. A megjelenő **IP-konfiguráció hozzáadása** ablaktáblán hozzon létre egy *IPConfig-4*nevű IP-konfigurációt. Engedélyezze a **nyilvános IP-címet,** és válasszon ki egy meglévő, elérhető nyilvános IP-címerőforrást a megjelenő **Nyilvános IP-cím kiválasztása** ablaktáblából.

    Miután kiválasztotta a nyilvános IP-cím erőforrást, kattintson az **OK** gombra, és az ablaktábla bezárul. Ha nem rendelkezik meglévő nyilvános IP-címmel, a cikk Nyilvános [IP-cím erőforrás létrehozása](#create-public-ip) című szakaszában leírt lépések végrehajtásával hozhat létre egyet. 

3. Tekintse át az új IP-konfigurációt. Annak ellenére, hogy egy privát IP-cím nincs kifejezetten hozzárendelve, az egyik automatikusan hozzá van rendelve az IP-konfigurációhoz, mert minden IP-konfigurációnak rendelkeznie kell egy privát IP-címmel.
4. A **Hozzáadás** gombra kattintva további IP-konfigurációkat adhat hozzá, vagy bezárhatja az összes megnyitott panelt az IP-címek hozzáadásához.
5. Adja hozzá a privát IP-címet a virtuális gép operációs rendszeréhez az operációs rendszer lépéseinek végrehajtásával a [cikk IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszában. Ne adja hozzá a nyilvános IP-címet az operációs rendszerhez.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>A nyilvános IP-cím erőforrás társítása meglévő IP-konfigurációhoz

1. Hajtsa végre a cikk [Alapvető lépések](#coreadd) szakaszának lépéseit.
2. Kattintson arra az IP-konfigurációra, amelyhez hozzá szeretné adni a nyilvános IP-cím erőforrást.
3. A megjelenő IPConfig ablaktáblán kattintson az **IP-cím**elemre.
4. A Megjelenő **Nyilvános IP-cím kiválasztása** ablaktáblában jelöljön ki egy nyilvános IP-címet.
5. Kattintson a **Mentés** gombra, és az ablaktáblák bezárulnak. Ha nem rendelkezik meglévő nyilvános IP-címmel, a cikk Nyilvános [IP-cím erőforrás létrehozása](#create-public-ip) című szakaszában leírt lépések végrehajtásával hozhat létre egyet.
3. Tekintse át az új IP-konfigurációt.
4. A **Hozzáadás** gombra kattintva további IP-konfigurációkat adhat hozzá, vagy bezárhatja az összes megnyitott panelt az IP-címek hozzáadásához. Ne adja hozzá a nyilvános IP-címet az operációs rendszerhez.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
