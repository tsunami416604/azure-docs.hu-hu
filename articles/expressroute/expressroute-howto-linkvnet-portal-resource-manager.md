---
title: 'Virtuális hálózat összekapcsolása az ExpressRoute - kapcsolatcsoport: az Azure portal |} A Microsoft Docs'
description: Virtuális hálózat csatlakozni egy Azure ExpressRoute-Kapcsolatcsoportot. Útmutató lépéseit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f2b22eb779a8ebe1d1fd804443663abc9a4245ec
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141645"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporthoz a portál használatával
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

Ezen cikk segítségével létrehozhat egy kapcsolatot a virtuális hálózat összekapcsolása egy Azure ExpressRoute-kapcsolatcsoporttal az Azure Portalon. A virtuális hálózatok, az Azure ExpressRoute-kapcsolatcsoporthoz csatlakozik lehet ugyanabban az előfizetésben, vagy azok egy másik előfizetés része lehet.

## <a name="before-you-begin"></a>Előkészületek

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) , és a kapcsolatcsoport a kapcsolatszolgáltató által engedélyezett.
  * Gondoskodjon arról, hogy az Azure privát társviszony-létesítést a kapcsolatcsoporthoz konfigurálva. Tekintse meg a [útválasztás konfigurálását](expressroute-howto-routing-portal-resource-manager.md) cikk vonatkozó útválasztási utasításokat.
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik, így engedélyezheti a végpontok közötti kapcsolat.
  * Gondoskodjon arról, hogy egy virtuális hálózat és a egy virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [az ExpressRoute virtuális hálózati átjáró létrehozása](expressroute-howto-add-gateway-resource-manager.md). Az ExpressRoute virtuális hálózati átjáró a "ExpressRoute", nem gatewaytype VPN típust használja.

* Legfeljebb 10 virtuális hálózatok kapcsolat egy standard ExpressRoute-kapcsolatcsoporthoz. Az összes virtuális hálózatok ugyanazon geopolitikai régióban kell lennie, a standard ExpressRoute-kapcsolatcsoport használatánál.

* Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok lehet kapcsolódni. Egyes ExpressRoute-kapcsolatcsoporthoz csatlakozik egy új kapcsolat objektumot létrehozásához használja az alábbi folyamatot. Az ExpressRoute-Kapcsolatcsoportok ugyanahhoz az előfizetéshez tartozik, eltérő előfizetésekben vagy mindkét vegyesen is lehet.

* Virtuális hálózat az ExpressRoute-kapcsolatcsoport a geopolitikai régión kívül hivatkozásra, vagy nagyobb számú virtuális hálózatok csatlakozni az ExpressRoute-kapcsolatcsoportot, ha az ExpressRoute prémium bővítmény engedélyezve. Ellenőrizze a [– gyakori kérdések](expressroute-faqs.md) kapcsolatos további részletekért a premium bővítményt.

* Is [videó](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) segít jobban megérteni, a lépések megkezdése előtt.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Csatlakoztassa egy virtuális hálózatot egy áramkör - ugyanahhoz az előfizetéshez

> [!NOTE]
> A BGP-konfigurációs adatok nem jelennek meg ha a 3. rétegbeli konfigurálva, a társviszony-létesítéseket. Ha a kapcsolatcsoport állapotban, létrehozhat kapcsolatokat kell lennie.
>

### <a name="to-create-a-connection"></a>Kapcsolat létrehozása

1. Győződjön meg arról, hogy az ExpressRoute-kapcsolatcsoportot, és az Azure privát társviszony-létesítés konfigurált sikeresen megtörtént. Kövesse a [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) és [útválasztás konfigurálását](expressroute-howto-routing-arm.md). Az ExpressRoute-kapcsolatcsoportot az alábbi képhez hasonlóan kell kinéznie:

  ![Az ExpressRoute-kapcsolatcsoport képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
2. Most elkezdheti csatolni a virtuális hálózati átjáró az ExpressRoute-kapcsolatcsoportot a kapcsolat kiépítése. Kattintson a **kapcsolat** > **Hozzáadás** megnyitásához a **kapcsolat hozzáadása** lapon, és adja meg az értékeket.

  ![Képernyőfelvétel a kapcsolat hozzáadása](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
3. Miután a kapcsolat sikeresen konfigurálva lett, a kapcsolat objektumot jelennek meg a kapcsolat adatait.

  ![Kapcsolat objektum képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Virtuális hálózat csatlakozni egy áramkör - másik előfizetésre

ExpressRoute-kapcsolatcsoport több előfizetésre kiterjedő megoszthatja. Az alábbi ábra egy egyszerű az ExpressRoute-Kapcsolatcsoportok hogyan megosztási alkotások sematikus több előfizetéshez.

![Az előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- A nagyméretű felhőbeli belül a kisebb felhők mindegyike egy szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál.
- Minden, a szervezeti egységek, a szervezeten belül a saját előfizetés használata a szolgáltatások üzembe helyezése, de meg is oszthatják egyetlen ExpressRoute-kapcsolatcsoporthoz szeretne csatlakozni a helyszíni hálózathoz.
- Egyetlen részleg (ebben a példában: informatikai) is a saját ExpressRoute-kapcsolatcsoportot. Más előfizetésekre, a szervezeten belül használható az ExpressRoute-kapcsolatcsoport és a kapcsolatcsoportot, beleértve a kapcsolódó egyéb Azure Active Directory-bérlők és a nagyvállalati szerződés regisztrációk előfizetéseket társított engedélyeket.

  > [!NOTE]
  > Az ExpressRoute-kapcsolatcsoport tulajdonosát a kapcsolatot és a sávszélesség díjak a dedikált kapcsolatcsoport lépnek érvénybe. Minden virtuális hálózat ossza meg ugyanazt a sávszélesség.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Felügyelet – kapcsolatcsoport tulajdonosok és a kapcsolatcsoport felhasználóinak

A kapcsolatcsoport tulajdonosát a jogosult kiemelt felhasználó az ExpressRoute-kapcsolatcsoport erőforrás. A kapcsolatcsoport tulajdonosát engedélyeket, melyeket a "kapcsolatcsoport felhasználóinak" hozhat létre. Kapcsolatcsoport felhasználók, amelyek nem az ExpressRoute-kapcsolatcsoporthoz, az egy előfizetésen belüli virtuális hálózati átjárók tulajdonosai. Kapcsolatcsoport felhasználóinak az engedélyek (egy engedélyezési száma virtuális hálózatonként) is beváltásához.

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Visszavonása egy engedélyezési eredményez az összes hivatkozás kapcsolat törlése folyamatban az előfizetésből, amelynek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**A kapcsolati engedély létrehozása**

A kapcsolatcsoport tulajdonosát egy engedélyezési hoz létre. Az eredmény egy kapcsolatcsoport felhasználó által a virtuális hálózati átjárók az ExpressRoute-kapcsolatcsoporthoz való csatlakozáshoz használható hitelesítési kulcs létrehozását. Egy engedélyezési csak egy kapcsolat érvényességét.

1. Az ExpressRoute oldalon kattintson **engedélyek** és írja be a **neve** az engedélyezési, és kattintson a **mentése**.

  ![Engedélyek](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Másolja a konfigurációs van a mentés után a **erőforrás-azonosító** és a **engedélyezési kulcsot**.

  ![Engedélykulcs](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**A kapcsolati engedély törlése**

A kapcsolat kiválasztásával törölheti a **törlése** ikonra az oldalon, a kapcsolat.

### <a name="circuit-user-operations"></a>Kapcsolatcsoport felhasználói műveletek

A kapcsolatcsoport-felhasználó erőforrás-azonosító és a egy engedélyezési kulcsot, a kapcsolatcsoport tulajdonosát a van szüksége.

**Hogy a kapcsolati engedély beváltása**

1. Kattintson a **+ új** gombra.

  ![Kattintson az új gombra](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Keresse meg **"Kapcsolat"** a Marketplace-en, válassza ki, majd kattintson **létrehozás**.

  ![Keresse meg a kapcsolat](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Győződjön meg arról, hogy a **kapcsolattípus** "ExpressRoute" értékre van állítva.
4. Adja meg az adatokat, majd kattintson a **OK** az alapvető beállítások lapon.

  ![Alapvető beállítások lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Az a **beállítások** lapon, válassza ki a **virtuális hálózati átjáró** , és ellenőrizze a **beváltása** jelölőnégyzetet.
6. Adja meg a **engedélyezési kulcsot** és a **Társkapcsolatcsoport URI** , és adja meg a kapcsolat nevét. Kattintson az **OK** gombra.

  ![Beállítások lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Tekintse át az adatokat a a **összefoglalás** lapot, és kattintson **OK**.

**A kapcsolati engedély felszabadítása**

Egy engedélyezési fel lehet szabadítani, a kapcsolat törlésével a hivatkozásokat az ExpressRoute-kapcsolatcsoport a virtuális hálózathoz.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Leválasztja a virtuális hálózatok közötti kapcsolat törlése

A kapcsolat törlésével, és a virtuális hálózat az ExpressRoute-kapcsolatcsoport leválasztása kiválasztásával a **törlése** ikonra a kapcsolat az oldalon.

## <a name="next-steps"></a>További lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).