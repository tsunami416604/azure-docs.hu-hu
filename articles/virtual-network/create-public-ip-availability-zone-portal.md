---
title: "Egy zoned nyilvános IP-cím létrehozása az Azure portállal |} Microsoft Docs"
description: "Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónát az Azure-portálon."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 0e796c8be0a5db8cb67e4a091fa51ed89920852c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónát az Azure-portálon

A nyilvános IP-cím, egy Azure rendelkezésre állási zónában (előzetes verzió) telepítése. Egy rendelkezésre állási zóna a fizikailag különálló zónát Azure-régióban. Az alábbiak végrehajtásának módját ismerheti meg:

> * Hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában
> * A rendelkezésre állási zónában létre kapcsolódó erőforrások azonosítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> Rendelkezésre állási zónák még csak előzetes verziójúak, és készen áll a fejlesztési és forgatókönyvek tesztelése. A select Azure-erőforrások és a régiók és a virtuális gép mérete családok támogatás érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen. 

## <a name="create-a-zonal-public-ip-address"></a>Egy zonal nyilvános IP-cím létrehozása

1. Kattintson a **hozzon létre egy erőforrást** az Azure portál bal felső sarokban.
2. Válassza ki **hálózati**, majd válassza ki **nyilvános IP-cím**.
3. Adja meg vagy válassza ki a következő beállítások értékeit, jelölje ki az előfizetését, fogadja el az alapértelmezett értéket a többi beállítást, majd kattintson a **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |SKU| **Alapszintű**: rendelni, amelyben a statikus vagy dinamikus kiosztási módszerrel. Az Azure-erőforrásokkal, egy nyilvános IP-címet, például a hálózati adapterek, VPN-átjárók, Alkalmazásátjárót és internetes terheléselosztók kiosztható is hozzárendelhető. A nyilvános IP-cím hozzárendelése a megadott zónában lévő a **rendelkezésre állási zóna** beállítást. A rendszer nem zónaredundáns. A rendelkezésre állási zónákkal kapcsolatos további információkért tekintse meg [a rendelkezésre állási zónák áttekintését](https://docs.microsoft.com/azure/availability-zones/az-overview). **Standard**: rendelni, amelyben csak a statikus kiosztási módszerrel. Hálózati adapterek vagy standard internetre terheléselosztók rendelhetők. Ha a nyilvános IP-címet rendel egy szabványos internetre irányuló terheléselosztót, ki kell választania szabvány. Az Azure Load Balancer termékváltozataival kapcsolatos további információkért tekintse meg [az Azure Load Balancer standard termékváltozatáról](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) szóló cikket. Olyan zónaredundáns, alapértelmezés szerint. Létrehozhatóak zónásan, és garantálhatóak adott rendelkezésre állási zónákban. A standard termékváltozat előzetes kiadásban érhető el. Mielőtt létrehozna egy Standard Termékváltozat nyilvános IP-címet, először regisztrálnia kell az előzetes verziójára. Az előzetes kiadásra való regisztrációval kapcsolatban tekintse meg [a standard termékváltozat előzetes kiadására való regisztrációt ismertető](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up) cikket. A Standard Termékváltozat csak egy támogatott helyre is létrehozható.  A támogatott helyek (régiók) listájáért tekintse meg a [régiónkénti elérhetőséget](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability) részletező szakaszt, és a további támogatott régiókért kövesse az [Azure virtuális hálózati frissítésekkel kapcsolatos](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.|   
    |Name (Név)|A nevét, válassza ki az erőforráscsoporton belül egyedinek kell lennie.|
    |Erőforráscsoport|Az új gombra kattintva hozhatja létre, és írja be a contoso.com|
    |Hely|Nyugat-Európa|
    |Rendelkezésre állási zóna|Ha bejelölte a **szabványos** SKU, választhat *Zónaredundáns* Ha azt szeretné, hogy az IP-címek között zónák rugalmasak lehetnek. Ha bejelöli a **alapvető** , az IP-cím termékváltozata nem rugalmas zónák között. A Termékváltozat úgy dönt, függetlenül is hozzárendelhet a címet egy adott zónához, ha úgy dönt. |

    A beállítások jelennek meg a portál az alábbi ábrán látható módon:

    ![Zonal nyilvános IP-cím létrehozása](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.

## <a name="next-steps"></a>További lépések

- További információ [rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview)
- További információ [nyilvános IP-címek](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)