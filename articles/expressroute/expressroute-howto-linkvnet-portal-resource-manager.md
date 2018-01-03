---
title: "Virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot: Azure-portál |} Microsoft Docs"
description: "Ez a dokumentum áttekintést virtuális hálózatokról (Vnetekről) csatolása ExpressRoute-Kapcsolatcsoportok."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: cherylmc
ms.openlocfilehash: 34caed9323ae3067d1dc17ab9c62ebf7a9be855b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Csatlakozás a virtuális hálózati ExpressRoute-kapcsolatcsoportot
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

Ez a cikk segít virtuális hálózatokról (Vnetekről) hivatkozásra az Azure ExpressRoute-Kapcsolatcsoportok a Resource Manager üzembe helyezési modellel és az Azure-portál használatával. Virtuális hálózatok lehet ugyanazt az előfizetést, vagy egy másik előfizetés részeként is lehetnek.

## <a name="before-you-begin"></a>Előkészületek
* Tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  
  * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoportot létrehozni](expressroute-howto-circuit-portal-resource-manager.md) és a kapcsolatcsoport szerint a kapcsolat szolgáltatójánál engedélyezve van.
  * Győződjön meg arról, hogy rendelkezik az Azure magánhálózati társviszony-létesítés a kapcsolatcsoport konfigurálva. Tekintse meg a [útválasztás konfigurálása](expressroute-howto-routing-portal-resource-manager.md) útválasztási miként.
  * Győződjön meg arról, hogy az Azure magánhálózati társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik-e, hogy engedélyezheti a végpontok közötti kapcsolat.
  * Győződjön meg arról, hogy a virtuális hálózat és a virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [hozzon létre egy virtuális hálózati átjáró ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). A virtuális hálózati átjáró ExpressRoute az "ExpressRoute", nem VPN GatewayType használja.

* Legfeljebb 10 virtuális hálózatok hozzákapcsolhatja egy szabványos ExpressRoute-kapcsolatcsoportot. Az összes virtuális hálózatot geopolitikai ugyanabban a régióban kell lennie, egy szabványos ExpressRoute-kapcsolatcsoportot használatakor. 
* Egy virtuális hálózaton kívül az ExpressRoute-kapcsolatcsoport geopolitikai régiója hivatkozásra, vagy virtuális hálózatok nagyobb számú csatlakozni az ExpressRoute-kapcsolatcsoportot, ha engedélyezte a prémium szintű ExpressRoute-bővítmény. Ellenőrizze a [gyakran ismételt kérdések](expressroute-faqs.md) a prémium szintű bővítmény olvashat.
* Is [videó](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) jobb megértése érdekében a lépések megkezdése előtt.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>A virtuális hálózati ugyanahhoz az előfizetéshez csatlakozni expressroute-kapcsolatcsoporthoz

### <a name="to-create-a-connection"></a>Kapcsolat létrehozása

> [!NOTE]
> BGP-konfigurációs információk nem jelennek meg ha a 3 rétegbeli a társviszony konfigurálva. Ha a kapcsolatcsoport kiépített állapotban van, hozhat létre kapcsolatokat kell lennie.
>

1. Győződjön meg arról, hogy az ExpressRoute-kapcsolatcsoportot és Azure magánhálózati társviszony-létesítés rendelkezik konfigurálása sikeresen megtörtént. Kövesse az utasításokat a [ExpressRoute-kapcsolatcsoportot létrehozni](expressroute-howto-circuit-arm.md) és [útválasztás konfigurálása](expressroute-howto-routing-arm.md). Az ExpressRoute-kapcsolatcsoportot az alábbi képen hasonlóan kell kinéznie:

    ![Az ExpressRoute-kapcsolatcsoport képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
2. Most elindíthatja a kiépítés kapcsolatot létesíthet a virtuális hálózati átjáró, és az ExpressRoute-kapcsolatcsoportot hivatkozásra. Kattintson **kapcsolat** > **hozzáadása** megnyitásához a **kapcsolat hozzáadása a** panelen, majd adja meg az értékeket.

    ![Vegye fel a kapcsolatot képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. Után a kapcsolat sikeresen konfigurálva, akkor a kapcsolati objektum a kapcsolat az információk jelennek meg.

     ![Kapcsolat objektum képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>Kapcsolat törlése
A kapcsolat kiválasztásával törölheti a **törlése** ikon a kapcsolat panelen.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
Több előfizetés ExpressRoute-kapcsolatcsoportot lehet megosztani. Az alábbi ábra egy egyszerű sematikus ExpressRoute-Kapcsolatcsoportok megosztási hogyan működik a több előfizetésekhez.

![Az előfizetések közötti kapcsolathoz](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- A nagy felhőben kisebb felhők egyes szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál.
- A szervezeten belüli osztályok mindegyikének saját előfizetés használhatja a szolgáltatások telepítéséhez, de egyetlen ExpressRoute-kapcsolatcsoportot való kapcsolódáshoz a helyszíni hálózaton is megoszthatják.
- Egy részleghez (ebben a példában: informatikai) az ExpressRoute-kapcsolatcsoport rendelkezhet. A szervezeten belüli más előfizetések használhatja az ExpressRoute-kapcsolatcsoportot és engedélyek a kapcsolatcsoport, beleértve az egyéb Azure Active Directory-bérlő és a nagyvállalati szerződés regisztrációkat kapcsolódó előfizetések tartoznak. 

    > [!NOTE]
    > A dedikált kör kapcsolat és a sávszélesség költségek az ExpressRoute-kapcsolatcsoport tulajdonosát alkalmazandó. Az összes virtuális hálózatot megosztani a azonos sávszélességet.
    > 
    >

### <a name="administration---circuit-owners-and-circuit-users"></a>Felügyeleti - áramkör tulajdonosa és a kapcsolatcsoport felhasználók

A kapcsolatcsoport tulajdonosát a ExpressRoute-kapcsolatcsoport erőforrás Power jogosultsága. A kapcsolatcsoport tulajdonosát hozhat létre, amely a "kör felhasználók" is váltható engedélyek. Kör felhasználók, amelyek nem tartoznak a ExpressRoute-kapcsolatcsoportot tárolóként ugyanazt az előfizetést virtuális hálózati átjárók tulajdonosai. Kör felhasználók is beválthatja engedélyek (virtuális hálózatonként egy engedélyezési).

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Visszavonni egy engedélyezési eredményezi, az összes hivatkozás kapcsolatok törlődnek az előfizetésből, amelyek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Egy kapcsolat hitelesítési létrehozása**

A kapcsolatcsoport tulajdonosát engedélyezési hoz létre. Az eredmény, használhatja a kapcsolatcsoport felhasználó való csatlakozáshoz a virtuális hálózati átjárók az ExpressRoute-kapcsolatcsoport engedélykulcs létrehozásához. Az engedély csak egy kapcsolat érvénytelen.

1. Az ExpressRoute paneljén kattintson **engedélyek** és írja be egy **neve** a engedélyezési, majd kattintson a **mentése**.

    ![Engedélyek](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. Ha a konfiguráció mentése másolja a **erőforrás-azonosító** és a **Engedélykulcs**.

    ![Hitelesítési kulcs](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Egy kapcsolat hitelesítési törlése**

A kapcsolat kiválasztásával törölheti a **törlése** ikon a kapcsolat panelen.

### <a name="circuit-user-operations"></a>Kör felhasználói műveletek

A kapcsolatcsoport felhasználónak van szüksége, az erőforrás-azonosító és a kapcsolatcsoport tulajdonosát a engedélykulcs. 

**Egy kapcsolat hitelesítési beváltani**

1.  Kattintson a **+ új** gombra.

    ![Új kattintson](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.  Keresse meg **"Kapcsolat"** a piactéren, válassza ki azt, és kattintson a **létrehozása**.

    ![Keresse meg a kapcsolat](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.  Győződjön meg arról, hogy a **kapcsolattípus** "ExpressRoute" értékre van állítva.


4.  Töltse ki az adatait, majd kattintson az **OK** az alapvető beállítások panelen.

    ![Alapvető beállítások panel](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.  A a **beállítások** panelen válassza ki a **virtuális hálózati átjáró** , és ellenőrizze a **engedélyezési beváltani** jelölőnégyzetet.

6.  Adja meg a **engedélykulcs** és a **áramkör URI partnert** és nevezze el a kapcsolatot. Kattintson az **OK** gombra.

    ![Beállítások panel](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7. Tekintse át az adatokat a **összegzés** panel megnyitásához, és kattintson **OK**.


**Egy kapcsolat hitelesítési felszabadítása**

Törölni kell a kapcsolat az ExpressRoute-kapcsolatcsoport a virtuális hálózatra hivatkozó engedélyezési is megjelenhetnek.

## <a name="next-steps"></a>További lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
