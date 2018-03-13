---
title: "Virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot: Azure-portál |} Microsoft Docs"
description: "Csatlakozzon egy Vnetet az Azure ExpressRoute-Kapcsolatcsoportot. Útmutató lépéseit."
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
ms.date: 03/08/2018
ms.author: cherylmc
ms.openlocfilehash: c2bef1d79d3133ea6306928a8c917e1bc3000a58
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Egy virtuális hálózathoz csatlakozni a portál használatával ExpressRoute-kapcsolatcsoportot
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

Ez a cikk segít kapcsolatot létesíthet egy virtuális hálózathoz kapcsolódik az Azure portál használatával Azure ExpressRoute-kapcsolatcsoportot. A virtuális hálózatok, hogy az Azure ExpressRoute-kapcsolatcsoportot kapcsolódni lehet ugyanazt az előfizetést, vagy azok egy másik előfizetés része lehet.

## <a name="before-you-begin"></a>Előkészületek

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoportot létrehozni](expressroute-howto-circuit-portal-resource-manager.md) és a kapcsolatcsoport szerint a kapcsolat szolgáltatójánál engedélyezve van.
  * Győződjön meg arról, hogy rendelkezik az Azure magánhálózati társviszony-létesítés a kapcsolatcsoport konfigurálva. Tekintse meg a [útválasztás konfigurálása](expressroute-howto-routing-portal-resource-manager.md) útválasztási miként.
  * Győződjön meg arról, hogy az Azure magánhálózati társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik-e, hogy engedélyezheti a végpontok közötti kapcsolat.
  * Győződjön meg arról, hogy a virtuális hálózat és a virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [hozzon létre egy virtuális hálózati átjáró ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). A virtuális hálózati átjáró ExpressRoute az "ExpressRoute", nem VPN GatewayType használja.

* Legfeljebb 10 virtuális hálózatok hozzákapcsolhatja egy szabványos ExpressRoute-kapcsolatcsoportot. Az összes virtuális hálózatot geopolitikai ugyanabban a régióban kell lennie, egy szabványos ExpressRoute-kapcsolatcsoportot használatakor.

* Egy egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok lehet társítani. Az alábbi eljárás segítségével új objektumot az egyes csatlakozik ExpressRoute-kapcsolatcsoportot létrehozni. Az ExpressRoute-Kapcsolatcsoportok ugyanahhoz az előfizetéshez, különböző előfizetésekhez vagy vegyesen is lehet.

* Egy virtuális hálózaton kívül az ExpressRoute-kapcsolatcsoport geopolitikai régiója hivatkozásra, vagy virtuális hálózatok nagyobb számú csatlakozni az ExpressRoute-kapcsolatcsoportot, ha engedélyezte a prémium szintű ExpressRoute-bővítmény. Ellenőrizze a [gyakran ismételt kérdések](expressroute-faqs.md) a prémium szintű bővítmény olvashat.

* Is [videó](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) jobb megértése érdekében a lépések megkezdése előtt.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Csatlakozás egy Vnetet egy expressroute - ugyanahhoz az előfizetéshez

> [!NOTE]
> BGP-konfigurációs információk nem jelennek meg ha a 3 rétegbeli a társviszony konfigurálva. Ha a kapcsolatcsoport kiépített állapotban van, hozhat létre kapcsolatokat kell lennie.
>

### <a name="to-create-a-connection"></a>Kapcsolat létrehozása

1. Győződjön meg arról, hogy az ExpressRoute-kapcsolatcsoportot és Azure magánhálózati társviszony-létesítés rendelkezik konfigurálása sikeresen megtörtént. Kövesse az utasításokat a [ExpressRoute-kapcsolatcsoportot létrehozni](expressroute-howto-circuit-arm.md) és [útválasztás konfigurálása](expressroute-howto-routing-arm.md). Az ExpressRoute-kapcsolatcsoportot az alábbi képen hasonlóan kell kinéznie:

  ![Az ExpressRoute-kapcsolatcsoport képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
2. Most elindíthatja a kiépítés kapcsolatot létesíthet a virtuális hálózati átjáró, és az ExpressRoute-kapcsolatcsoportot hivatkozásra. Kattintson a **kapcsolat** > **Hozzáadás** megnyitásához a **kapcsolat hozzáadása a** lapon, és adja meg az értékeket.

  ![Vegye fel a kapcsolatot képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
3. Után a kapcsolat sikeresen konfigurálva, akkor a kapcsolati objektum a kapcsolat az információk jelennek meg.

  ![Kapcsolat objektum képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Csatlakozás egy Vnetet egy expressroute - másik előfizetésben található

Több előfizetés ExpressRoute-kapcsolatcsoportot lehet megosztani. Az alábbi ábra egy egyszerű sematikus ExpressRoute-Kapcsolatcsoportok megosztási hogyan működik a több előfizetésekhez.

![Az előfizetések közötti kapcsolathoz](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- A nagy felhőben kisebb felhők egyes szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál.
- A szervezeten belüli osztályok mindegyikének saját előfizetés használhatja a szolgáltatások telepítéséhez, de egyetlen ExpressRoute-kapcsolatcsoportot való kapcsolódáshoz a helyszíni hálózaton is megoszthatják.
- Egy részleghez (ebben a példában: informatikai) az ExpressRoute-kapcsolatcsoport rendelkezhet. A szervezeten belüli más előfizetések használhatja az ExpressRoute-kapcsolatcsoportot és engedélyek a kapcsolatcsoport, beleértve az egyéb Azure Active Directory-bérlő és a nagyvállalati szerződés regisztrációkat kapcsolódó előfizetések tartoznak.

  > [!NOTE]
  > A dedikált kör kapcsolat és a sávszélesség költségek az ExpressRoute-kapcsolatcsoport tulajdonosát alkalmazandó. Az összes virtuális hálózatot megosztani a azonos sávszélességet.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Felügyeleti - áramkör tulajdonosok és a kapcsolatcsoport felhasználók

A kapcsolatcsoport tulajdonosát a ExpressRoute-kapcsolatcsoport erőforrás Power jogosultsága. A kapcsolatcsoport tulajdonosát hozhat létre, amely a "kör felhasználók" is váltható engedélyek. Kör felhasználók, amelyek nem tartoznak a ExpressRoute-kapcsolatcsoportot tárolóként ugyanazt az előfizetést virtuális hálózati átjárók tulajdonosai. Kör felhasználók is beválthatja engedélyek (virtuális hálózatonként egy engedélyezési).

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Visszavonni egy engedélyezési eredményezi, az összes hivatkozás kapcsolatok törlődnek az előfizetésből, amelyek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Egy kapcsolat hitelesítési létrehozása**

A kapcsolatcsoport tulajdonosát engedélyezési hoz létre. Az eredmény, használhatja a kapcsolatcsoport felhasználó való csatlakozáshoz a virtuális hálózati átjárók az ExpressRoute-kapcsolatcsoport engedélykulcs létrehozásához. Az engedély csak egy kapcsolat érvénytelen.

1. Az ExpressRoute oldalon kattintson **engedélyek** és írja be egy **neve** a engedélyezési, majd kattintson a **mentése**.

  ![Engedélyek](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Ha a konfiguráció mentése másolja a **erőforrás-azonosító** és a **Engedélykulcs**.

  ![Engedélykulcs](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Egy kapcsolat hitelesítési törlése**

A kapcsolat kiválasztásával törölheti a **törlése** az oldalon, a kapcsolat ikon.

### <a name="circuit-user-operations"></a>Kör felhasználói műveletek

A kapcsolatcsoport felhasználónak van szüksége, az erőforrás-azonosító és a kapcsolatcsoport tulajdonosát a engedélykulcs.

**Egy kapcsolat hitelesítési beváltani**

1. Kattintson a **+ új** gombra.

  ![Új kattintson](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Keresse meg **"Kapcsolat"** a piactéren, válassza ki azt, és kattintson a **létrehozása**.

  ![Keresse meg a kapcsolat](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Győződjön meg arról, hogy a **kapcsolattípus** "ExpressRoute" értékre van állítva.
4. Töltse ki az adatait, majd kattintson az **OK** az alapvető beállítások lapon.

  ![Alapvető beállítások lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Az a **beállítások** lapon, jelölje be a **virtuális hálózati átjáró** , és ellenőrizze a **engedélyezési beváltani** jelölőnégyzetet.
6. Adja meg a **engedélykulcs** és a **áramkör URI partnert** és nevezze el a kapcsolatot. Kattintson az **OK** gombra.

  ![Beállítások lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Tekintse át az adatokat a **összegzés** lapot, és kattintson **OK**.

**Egy kapcsolat hitelesítési felszabadítása**

Törölni kell a kapcsolat az ExpressRoute-kapcsolatcsoport a virtuális hálózatra hivatkozó engedélyezési is megjelenhetnek.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Megszünteti egy Vnetet való kapcsolat törlése

Törölje a kapcsolatot, és megszünteti a VNet ExpressRoute-kapcsolatcsoportot kiválasztásával a **törlése** az oldalon, a kapcsolat ikon.

## <a name="next-steps"></a>További lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).