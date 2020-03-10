---
title: 'ExpressRoute: VNet csatolása egy áramkörhöz: Azure Portal'
description: Csatlakoztasson egy VNet egy Azure ExpressRoute-áramkörhöz. Útmutató lépéseit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384284"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporthoz a portál használatával
> [!div class="op_single_selector"]
> * [Azure Portalra](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

Ezen cikk segítségével létrehozhat egy kapcsolatot a virtuális hálózat összekapcsolása egy Azure ExpressRoute-kapcsolatcsoporttal az Azure Portalon. A virtuális hálózatok, az Azure ExpressRoute-kapcsolatcsoporthoz csatlakozik lehet ugyanabban az előfizetésben, vagy azok egy másik előfizetés része lehet.

## <a name="before-you-begin"></a>Előkészületek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára.
  * Gondoskodjon arról, hogy az Azure privát társviszony-létesítést a kapcsolatcsoporthoz konfigurálva. A társítási és útválasztási utasításokért tekintse meg a [társítás létrehozása és módosítása ExpressRoute áramkörhöz](expressroute-howto-routing-portal-resource-manager.md) című cikket.
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik, így engedélyezheti a végpontok közötti kapcsolat.
  * Gondoskodjon arról, hogy egy virtuális hálózat és a egy virtuális hálózati átjáró létrehozása, és teljesen kiépítve. A [ExpressRoute virtuális hálózati átjárójának létrehozásához](expressroute-howto-add-gateway-resource-manager.md)kövesse az utasításokat. Az ExpressRoute virtuális hálózati átjáró a "ExpressRoute", nem gatewaytype VPN típust használja.

* Legfeljebb 10 virtuális hálózatok kapcsolat egy standard ExpressRoute-kapcsolatcsoporthoz. Az összes virtuális hálózatok ugyanazon geopolitikai régióban kell lennie, a standard ExpressRoute-kapcsolatcsoport használatánál.

* Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok lehet kapcsolódni. Egyes ExpressRoute-kapcsolatcsoporthoz csatlakozik egy új kapcsolat objektumot létrehozásához használja az alábbi folyamatot. Az ExpressRoute-Kapcsolatcsoportok ugyanahhoz az előfizetéshez tartozik, eltérő előfizetésekben vagy mindkét vegyesen is lehet.

* Virtuális hálózat az ExpressRoute-kapcsolatcsoport a geopolitikai régión kívül hivatkozásra, vagy nagyobb számú virtuális hálózatok csatlakozni az ExpressRoute-kapcsolatcsoportot, ha az ExpressRoute prémium bővítmény engedélyezve. A prémium szintű bővítménysel kapcsolatos további információkért olvassa el a [Gyakori kérdések](expressroute-faqs.md) című részt.

* A lépések megkezdése előtt [megtekintheti a videót](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) .

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Csatlakoztassa egy virtuális hálózatot egy áramkör - ugyanahhoz az előfizetéshez

> [!NOTE]
> A BGP-konfigurációs adatok nem jelennek meg ha a 3. rétegbeli konfigurálva, a társviszony-létesítéseket. Ha a kapcsolatcsoport állapotban, létrehozhat kapcsolatokat kell lennie.
>

### <a name="to-create-a-connection"></a>Kapcsolat létrehozása

1. Győződjön meg arról, hogy az ExpressRoute-kapcsolatcsoportot, és az Azure privát társviszony-létesítés konfigurált sikeresen megtörtént. Kövesse a ExpressRoute- [áramkör létrehozása](expressroute-howto-circuit-arm.md) és a [ExpressRoute-kör társításának létrehozása és módosítása](expressroute-howto-routing-arm.md)című témakör utasításait. Az ExpressRoute-kapcsolatcsoportot az alábbi képhez hasonlóan kell kinéznie:

   [![Képernyőkép a ExpressRoute áramkörről](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Áramkör megtekintése")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Most elkezdheti csatolni a virtuális hálózati átjáró az ExpressRoute-kapcsolatcsoportot a kapcsolat kiépítése. Kattintson a **kapcsolódás** > **Hozzáadás** gombra a **Kapcsolódás hozzáadása** lap megnyitásához, majd konfigurálja az értékeket.

   [![A kapcsolatok hozzáadása képernyőkép](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "A kapcsolatok hozzáadása képernyőkép")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
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

**A kapcsolódási engedély létrehozása**

A kapcsolatcsoport tulajdonosát egy engedélyezési hoz létre. Az eredmény egy kapcsolatcsoport felhasználó által a virtuális hálózati átjárók az ExpressRoute-kapcsolatcsoporthoz való csatlakozáshoz használható hitelesítési kulcs létrehozását. Egy engedélyezési csak egy kapcsolat érvényességét.

> [!NOTE]
> Minden kapcsolathoz külön engedély szükséges.
>

1. A ExpressRoute lapon kattintson az **engedélyek** elemre, majd írja be az engedélyezés **nevét** , majd kattintson a **Mentés**gombra.

   ![Engedélyek](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. A konfiguráció mentése után másolja ki az **erőforrás-azonosítót** és az **engedélyezési kulcsot**.

   ![Engedélykulcs](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**A kapcsolódási engedélyek törlése**

A kapcsolatok törléséhez válassza a **Törlés** ikont a kapcsolatok lapján.

### <a name="circuit-user-operations"></a>Kapcsolatcsoport felhasználói műveletek

A kapcsolatcsoport-felhasználó erőforrás-azonosító és a egy engedélyezési kulcsot, a kapcsolatcsoport tulajdonosát a van szüksége.

**A kapcsolódási engedély beváltása**

1. Kattintson az **+ új** gombra.

   ![Kattintson az új gombra](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Keresse meg a **"kapcsolatok"** kifejezést a piactéren, jelölje ki, majd kattintson a **Létrehozás**gombra.

   ![Keresse meg a kapcsolat](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Győződjön meg arról, hogy a **kapcsolattípus** "ExpressRoute" értékre van állítva.
4. Adja meg a részleteket, majd kattintson az **OK** gombra az alapvető beállítások lapon.

   ![Alapvető beállítások lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. A **Beállítások** lapon válassza ki a **virtuális hálózati átjárót** , és jelölje be az **Engedélyezés beváltása** jelölőnégyzetet.
6. Adja meg az **engedélyezési kulcsot** és a **társ áramköri URI** -t, és adjon nevet a kapcsolatnak. Kattintson az **OK** gombra. A **társ áramköri URI** a ExpressRoute áramkör erőforrás-azonosítója (amelyet a ExpressRoute-áramkör tulajdonságok beállítása paneljén talál).

   ![Beállítások lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Tekintse át az **Összegzés** lapon található információkat, majd kattintson **az OK**gombra.

**A kapcsolódási engedély felszabadítása**

Egy engedélyezési fel lehet szabadítani, a kapcsolat törlésével a hivatkozásokat az ExpressRoute-kapcsolatcsoport a virtuális hálózathoz.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Leválasztja a virtuális hálózatok közötti kapcsolat törlése

Törölheti a kapcsolatot, és leválaszthatja a VNet egy ExpressRoute-áramkörhöz úgy, hogy a kapcsolat lapján a **Törlés** ikonra kattint.

## <a name="next-steps"></a>Következő lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
