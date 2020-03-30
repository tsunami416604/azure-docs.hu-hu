---
title: 'ExpressRoute: Virtuális hálózat összekapcsolása egy kapcsolat: Azure Portal'
description: Virtuális hálózat csatlakoztatása egy Azure ExpressRoute-kapcsolathoz. Útmutató lépések.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4c7a24ad692086398059d1afd48c8927e9d18582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272915"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Virtuális hálózat összekapcsolása ExpressRoute-kapcsolatcsoporttal a portálon
> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

Ez a cikk segít kapcsolatot létrehozni egy virtuális hálózat ot egy Azure ExpressRoute-kapcsolattal az Azure Portalon keresztül. Az Azure ExpressRoute-kapcsolathoz csatlakozó virtuális hálózatok lehetnek ugyanabban az előfizetésben, vagy egy másik előfizetés részét is képezik.

## <a name="before-you-begin"></a>Előkészületek

* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket,](expressroute-prerequisites.md) [útválasztási követelményeket](expressroute-routing.md)és [munkafolyamatokat.](expressroute-workflows.md)

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * Kövesse az utasításokat [egy ExpressRoute-kapcsolat létrehozásához,](expressroute-howto-circuit-portal-resource-manager.md) és a kapcsolatszolgáltató által engedélyezett áramkört.
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van a kapcsolatcsoporthoz. A társviszony-létesítési és útválasztási utasítások hoz [létre és módosítsa a társviszony-létesítést egy ExpressRoute-kapcsolatlétesítési](expressroute-howto-routing-portal-resource-manager.md) cikkhez című témakörben találja.
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van, és a BGP-társviszony-létesítés a hálózat és a Microsoft között, így lehetővé teszi a végpontok közötti kapcsolatot.
  * Győződjön meg arról, hogy van egy virtuális hálózat és egy virtuális hálózati átjáró létrehozott és teljesen kiépített. Az utasításokat követve [hozzon létre egy virtuális hálózati átjárót az ExpressRoute számára.](expressroute-howto-add-gateway-resource-manager.md) Az ExpressRoute virtuális hálózati átjárója a GatewayType "ExpressRoute" típust használja, nem a VPN-t.

* Egy szabványos ExpressRoute-kapcsolathoz legfeljebb 10 virtuális hálózat kapcsolódhat. Minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie, ha szabványos ExpressRoute-áramkört használ.

* Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-kapcsolatcsoporthoz kapcsolható. Az alábbi folyamat segítségével új kapcsolatobjektumot hozhat létre minden olyan ExpressRoute-kapcsolati kapcsolathoz, amelyhez csatlakozik. Az ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetések, vagy a kettő kombinációja.

* Az ExpressRoute-kapcsolat geopolitikai régióján kívüli virtuális hálózatot összekapcsolhat, vagy nagyobb számú virtuális hálózatot csatlakoztathat az ExpressRoute-kapcsolathoz, ha engedélyezte az ExpressRoute prémium szintű bővítményt. A prémium kiegészítővel kapcsolatos további részletekért tekintse meg a [GYIK-et.](expressroute-faqs.md)

* A [lépések](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) jobb megértése érdekében a videó megtekintése az első lépések előtt megtekinthető.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Virtuális hálózat csatlakoztatása áramkörhöz - ugyanaz az előfizetés

> [!NOTE]
> A BGP konfigurációs adatai nem jelennek meg, ha a 3. Ha a kapcsolatcsoport kiépített állapotban van, képesnek kell lennie kapcsolatok létrehozására.
>

### <a name="to-create-a-connection"></a>Kapcsolat létrehozásához

1. Győződjön meg arról, hogy az ExpressRoute-kapcsolat és az Azure privát társviszony-létesítés sikeresen konfigurálva lett. Kövesse az [ExpressRoute-kapcsolat létrehozása,](expressroute-howto-circuit-arm.md) valamint [az ExpressRoute-kapcsolat létesítésének létrehozása és módosítása](expressroute-howto-routing-arm.md)című, a következő utasításokat, hogy Az ExpressRoute-áramkörnek az alábbi képhez hasonlóan kell kinéznie:

   [![ExpressRoute-áramkör képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Áramkör megtekintése")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Most már megkezdheti a kiépítése a kapcsolatot, hogy összekapcsolják a virtuális hálózati átjáró az ExpressRoute-kapcsolat. Kattintson a **Kapcsolat** > **hozzáadása gombra** a **Kapcsolat hozzáadása** lap megnyitásához, majd konfigurálja az értékeket.

   [![Kapcsolat hozzáadása képernyőkép](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Kapcsolat hozzáadása képernyőkép")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. A kapcsolat sikeres konfigurálása után a kapcsolatobjektum a kapcsolat adatait jeleníti meg.

   ![Kapcsolatobjektum képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Virtuális hálózat csatlakoztatása áramkörhöz – más előfizetés

Az ExpressRoute-áramköröket több előfizetésen keresztül is megoszthatja. Az alábbi ábra egy egyszerű vázlatot mutat be arról, hogyan működik az ExpressRoute-áramkörök megosztása több előfizetésen keresztül.

![Előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- A nagy felhőn belüli kisebb felhők mindegyike olyan előfizetéseket jelöl, amelyek a szervezet különböző részlegeihez tartoznak.
- A szervezet minden részlege használhatja a saját előfizetését a szolgáltatásaik üzembe helyezéséhez, de egyetlen ExpressRoute-kapcsolaton osztozhatnak a helyszíni hálózathoz való csatlakozáshoz.
- Egyetlen részleg (ebben a példában: IT) birtokolhatja az ExpressRoute-áramkört. A szervezeten belüli egyéb előfizetések használhatják az ExpressRoute-áramkört és a kapcsolatcsoporthoz társított engedélyeket, beleértve a más Azure Active Directory-bérlőkhöz és nagyvállalati szerződéssel kapcsolatos regisztrációkhoz kapcsolódó előfizetéseket is.

  > [!NOTE]
  > A dedikált kapcsolati és sávszélesség-díjak az ExpressRoute-kapcsolat tulajdonosa lesz alkalmazva. Minden virtuális hálózat ugyanazt a sávszélességet használja.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Felügyelet – Az áramkör-tulajdonosokról és az áramkör-felhasználókról

Az "áramkör tulajdonosa" az ExpressRoute-áramköri erőforrás jogosult Energiafelhasználója. A kapcsolatcsoport tulajdonosa olyan engedélyeket hozhat létre, amelyeket a "körfelhasználók" beválthatnak. Az áramkör-felhasználók olyan virtuális hálózati átjárók tulajdonosai, amelyek nem ugyanabban az előfizetésben vannak, mint az ExpressRoute-kapcsolat. A kapcsolatcsoport-felhasználók virtuális hálózatonként egy engedélyezést válthatnak be.Circuit users can redeem authorizations (one authorization per virtual network).

Az áramkör tulajdonosa bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedélyezés visszavonása azt eredményezi, hogy az összes kapcsolatkapcsolat törlődik attól az előfizetésből, amelynek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramkörtulajdonosi műveletek

**Kapcsolatengedélyezés létrehozása**

A kapcsolatcsoport tulajdonosa létrehoz egy engedélyt. Ennek eredményeképpen létre kell hozni egy engedélyezési kulcsot, amelyet egy áramkör-felhasználó használhat a virtuális hálózati átjárók ExpressRoute-kapcsolathoz való csatlakoztatására. Az engedélyezés csak egy kapcsolatra érvényes.

> [!NOTE]
> Minden kapcsolathoz külön engedély szükséges.
>

1. Az ExpressRoute lapon kattintson az **Engedélyezés gombra,** majd írja be az engedélyezés **nevét,** majd kattintson a **Mentés gombra.**

   ![Engedélyek](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. A konfiguráció mentése után másolja az **erőforrás-azonosítót** és az **engedélyezési kulcsot.**

   ![Engedélyezési kulcs](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Kapcsolati engedély törlése**

A kapcsolat törléséhez válassza a kapcsolat lapjának **Törlés** ikonját.

### <a name="circuit-user-operations"></a>Áramkörfelhasználói műveletek

Az áramkör-felhasználónak szüksége van az erőforrás-azonosítóra és egy engedélyezési kulcsra a kapcsolattulajdonostól.

**Kapcsolati engedély beváltása**

1. Kattintson az **+Új** gombra.

   ![Kattintson az Új gombra](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Keressen rá a **"Kapcsolat"** kifejezésre a Piactéren, jelölje ki, és kattintson a **Létrehozás gombra.**

   ![Kapcsolat keresése](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Győződjön meg arról, hogy a **kapcsolat típusa** "ExpressRoute" lesz.
4. Töltse ki a részleteket, majd kattintson az **OK** gombra az Alapok lapon.

   ![Alapvető alapok lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. A **Beállítások** lapon jelölje be a **Virtuális hálózati átjárót,** és jelölje be az **Engedélyezés beváltása** jelölőnégyzetet.
6. Adja meg az **engedélyezési kulcsot** és a **társáramkör URI-ját,** és adjon nevet a kapcsolatnak. Kattintson az **OK** gombra. A **társ-kapcsolati kör URI** az ExpressRoute-kapcsolat áramkörének erőforrásazonosítója (amely az ExpressRoute-kapcsolat tulajdonságbeállítási ablaktáblája alatt található).

   ![Beállítások lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Tekintse át az információkat az **Összegzés** lapon, és kattintson az **OK gombra.**

**Kapcsolatengedélyezési kiadás**

Az engedélyezést az ExpressRoute-kapcsolat virtuális hálózattal összekötő kapcsolat törlésével szabadíthatja fel.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Virtuális hálózat leválasztásához megszakadt kapcsolat törlése

Törölheti a kapcsolatot, és leválaszthatja a virtuális hálózatot egy ExpressRoute-kapcsolatra, ha a lapon a kapcsolat **törlésikonját választja.**

## <a name="next-steps"></a>További lépések
Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.
