---
title: 'ExpressRoute: VNet csatolása egy áramkörhöz: Azure Portal'
description: Megtudhatja, hogyan hozhat létre kapcsolatot egy virtuális hálózat Azure ExpressRoute-áramkörhöz való összekapcsolásához a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/17/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 56508503c199b1f822ce8f181689a236f3a0af18
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395825"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Virtuális hálózat összekapcsolása ExpressRoute-kapcsolatcsoporttal a portálon
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy kapcsolatot egy virtuális hálózat Azure ExpressRoute-áramkörhöz való összekapcsolásához a Azure Portal használatával. Az Azure ExpressRoute-áramkörhöz csatlakozó virtuális hálózatok lehetnek ugyanahhoz az előfizetéshez, vagy egy másik előfizetés részét képezik.

## <a name="before-you-begin"></a>Előkészületek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára.
  * Győződjön meg arról, hogy az Ön áramköréhez konfigurálva van az Azure-beli magánhálózati kapcsolat. A társítási és útválasztási utasításokért tekintse meg a [társítás létrehozása és módosítása ExpressRoute áramkörhöz](expressroute-howto-routing-portal-resource-manager.md) című cikket.
  * Győződjön meg arról, hogy az Azure Private peering konfigurálva van, és a hálózat és a Microsoft közötti BGP-társak is elérhetők, így a végpontok közötti kapcsolat engedélyezhető.
  * Győződjön meg arról, hogy van egy virtuális hálózat és egy virtuális hálózati átjáró létrehozva és teljesen kiépítve. A [ExpressRoute virtuális hálózati átjárójának létrehozásához](expressroute-howto-add-gateway-resource-manager.md)kövesse az utasításokat. A ExpressRoute virtuális hálózati átjárója a "ExpressRoute" GatewayType használja, nem pedig a VPN-t.

* Akár 10 virtuális hálózatot is összekapcsolhat egy standard ExpressRoute áramkörrel. Szabványos ExpressRoute áramkör használata esetén minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie.

* Egyetlen VNet legfeljebb négy ExpressRoute-áramkörhöz lehet kapcsolni. Az alábbi folyamat használatával hozzon létre egy új kapcsolati objektumot minden olyan ExpressRoute-áramkörhöz, amelyhez csatlakozik. A ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetésekben vagy a kettő kombinációjában is.

* A ExpressRoute áramkör geopolitikai régióján kívül is összekapcsolhat egy virtuális hálózatot, vagy nagyobb számú virtuális hálózatot csatlakoztathat a ExpressRoute-áramkörhöz, ha engedélyezte a ExpressRoute prémium bővítményét. A prémium szintű bővítménysel kapcsolatos további információkért olvassa el a [Gyakori kérdések](expressroute-faqs.md) című részt.

* A lépések megkezdése előtt [megtekintheti a videót](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) .

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>VNet összekapcsolása egy áramköri előfizetéssel

> [!NOTE]
> A BGP konfigurációs adatai nem jelennek meg, ha a 3. rétegbeli szolgáltató konfigurálta a társait. Ha az áramkör kiépített állapotban van, kapcsolatokat hozhat létre.
>

### <a name="to-create-a-connection"></a>Kapcsolat létrehozásához

1. Győződjön meg arról, hogy a ExpressRoute-áramkör és az Azure Private-társítása sikeresen konfigurálva van. Kövesse a ExpressRoute- [áramkör létrehozása](expressroute-howto-circuit-arm.md) és a [ExpressRoute-kör társításának létrehozása és módosítása](expressroute-howto-routing-arm.md)című témakör utasításait. A ExpressRoute-áramkörnek a következő képhez hasonlóan kell kinéznie:

   [![Képernyőkép a ExpressRoute áramkörről](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Áramkör megtekintése")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Most már megkezdheti a kapcsolat létesítését, hogy a virtuális hálózati átjárót a ExpressRoute áramkörhöz kapcsolja. Kattintson a **Kapcsolódás**  >  **Hozzáadás** elemre a **Kapcsolódás hozzáadása** lap megnyitásához, majd konfigurálja az értékeket.

   [![A kapcsolatok hozzáadása képernyőkép](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "A kapcsolatok hozzáadása képernyőkép")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. A kapcsolat sikeres konfigurálását követően a kapcsolati objektum megjeleníti a kapcsolat információit.

   ![A kapcsolatok objektumának képernyőképe](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>VNet összekapcsolása áramkörhöz – eltérő előfizetés

Megoszthat egy ExpressRoute-áramkört több előfizetésen keresztül is. Az alábbi ábra egy egyszerű sematikus ábrát mutat be, hogy a megosztás hogyan működik a ExpressRoute-áramkörök több előfizetésen keresztül.

![Előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- A nagyméretű felhőben lévő kisebb felhőket a szervezeten belüli különböző részlegekhez tartozó előfizetések ábrázolására használják.
- A szervezeten belüli részlegek saját előfizetést használhatnak a szolgáltatásaik üzembe helyezéséhez, de egyetlen ExpressRoute áramkört is megoszthatnak a helyszíni hálózathoz való kapcsolódáshoz.
- Egyetlen részleg (ebben a példában: IT) a ExpressRoute áramkör tulajdonosa lehet. A szervezeten belüli egyéb előfizetések használhatják az áramkörhöz társított ExpressRoute áramkört és engedélyeket, beleértve a más Azure Active Directory bérlők és Nagyvállalati Szerződés beléptetésekhez kapcsolódó előfizetéseket is.

  > [!NOTE]
  > A dedikált áramkör kapcsolati és sávszélesség-díjait a rendszer a ExpressRoute Circuit tulajdonosára alkalmazza. Minden virtuális hálózat ugyanazt a sávszélességet használja.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Felügyelet – az áramkör tulajdonosai és a Circuit-felhasználók

Az "áramkör tulajdonosa" a ExpressRoute Circuit erőforrásának jogosult Kiemelt felhasználója. Az áramkör tulajdonosa létrehozhat olyan engedélyeket, amelyeket az "áramköri felhasználók" is beválthatnak. Az áramköri felhasználók olyan virtuális hálózati átjárók tulajdonosai, amelyek nem a ExpressRoute áramkörhöz tartozó előfizetésben találhatók. Az áramköri felhasználók beválthatják az engedélyeket (egy-egy hitelesítés virtuális hálózatonként).

Az áramkör tulajdonosa bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedély visszavonása az összes olyan kapcsolati kapcsolat törlését eredményezi, amelynek a hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramköri tulajdonosi műveletek

**A kapcsolódási engedély létrehozása**

Az áramkör tulajdonosa létrehoz egy engedélyt. Ez egy olyan engedélyezési kulcs létrehozását eredményezi, amelyet egy áramköri felhasználó a virtuális hálózati átjárók ExpressRoute áramkörhöz való kapcsolódására használhat. Egy engedély csak egy kapcsolatban érvényes.

> [!NOTE]
> Minden kapcsolathoz külön engedély szükséges.
>

1. A ExpressRoute lapon kattintson az **engedélyek** elemre, majd írja be az engedélyezés **nevét** , majd kattintson a **Mentés**gombra.

   ![Engedélyek](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. A konfiguráció mentése után másolja ki az **erőforrás-azonosítót** és az **engedélyezési kulcsot**.

   ![Engedélyezési kulcs](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**A kapcsolódási engedélyek törlése**

A kapcsolatok törléséhez válassza a **Törlés** ikont a kapcsolatok lapján.

### <a name="circuit-user-operations"></a>Áramkör felhasználói műveletei

Az áramkör-felhasználónak szüksége van az erőforrás-AZONOSÍTÓra és egy engedélyezési kulcsra az áramkör tulajdonosától.

**A kapcsolódási engedély beváltása**

1. Kattintson az **+ új** gombra.

   ![Kattintson az új elemre.](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Keresse meg a **"kapcsolatok"** kifejezést a piactéren, jelölje ki, majd kattintson a **Létrehozás**gombra.

   ![Kapcsolatok keresése](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Győződjön meg arról, hogy a **kapcsolattípus** "ExpressRoute" értékre van állítva.
4. Adja meg a részleteket, majd kattintson az **OK** gombra az alapvető beállítások lapon.

   ![Alapismeretek lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. A **Beállítások** lapon válassza ki a **virtuális hálózati átjárót** , és jelölje be az **Engedélyezés beváltása** jelölőnégyzetet.
6. Adja meg az **engedélyezési kulcsot** és a **társ áramköri URI** -t, és adjon nevet a kapcsolatnak. Kattintson az **OK** gombra. A **társ áramköri URI** a ExpressRoute áramkör erőforrás-azonosítója (amelyet a ExpressRoute-áramkör tulajdonságok beállítása paneljén talál).

   ![Beállítások lap](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Tekintse át az **Összegzés** lapon található információkat, majd kattintson **az OK**gombra.

**A kapcsolódási engedély felszabadítása**

Az engedélyezést a ExpressRoute áramkört a virtuális hálózathoz összekötő kapcsolat törlésével lehet felszabadítani.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Kapcsolat törlése a VNet leválasztásához

Törölheti a kapcsolatot, és leválaszthatja a VNet egy ExpressRoute-áramkörhöz úgy, hogy a kapcsolat lapján a **Törlés** ikonra kattint.

## <a name="next-steps"></a>Következő lépések
A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.
