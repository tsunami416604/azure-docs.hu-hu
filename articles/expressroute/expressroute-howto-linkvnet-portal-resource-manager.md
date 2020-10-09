---
title: 'Oktatóanyag: VNet összekapcsolása egy ExpressRoute-áramkörrel – Azure Portal'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozható létre kapcsolat egy virtuális hálózat Azure ExpressRoute-áramkörhöz való összekapcsolásához a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: f46ad0d45967f94191732f472b44a47de930a3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855353"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Oktatóanyag: virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel a portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

Ez az oktatóanyag segítséget nyújt egy kapcsolat létrehozásához egy virtuális hálózat Azure ExpressRoute-áramkörhöz való összekapcsolásához a Azure Portal használatával. Az Azure ExpressRoute-áramkörhöz csatlakozó virtuális hálózatok lehetnek ugyanahhoz az előfizetéshez, vagy egy másik előfizetés részét képezik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Egy virtuális hálózat összekapcsolása ugyanahhoz az előfizetéshez tartozó áramkörhöz.
> - Virtuális hálózat összekapcsolása egy másik előfizetésben található áramkörhöz.
> - Törölje a virtuális hálózat és a ExpressRoute áramkör közötti kapcsolatot.

## <a name="prerequisites"></a>Előfeltételek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára.
  * Győződjön meg arról, hogy az Ön áramköréhez konfigurálva van az Azure-beli magánhálózati kapcsolat. A társítási és útválasztási utasításokért tekintse meg a [társítás létrehozása és módosítása ExpressRoute áramkörhöz](expressroute-howto-routing-portal-resource-manager.md) című cikket.
  * Győződjön meg arról, hogy az Azure-beli privát kapcsolatok konfigurálva vannak, és a hálózat és a Microsoft között a végpontok közötti kapcsolathoz szükséges BGP-társítást hoz létre.
  * Győződjön meg arról, hogy van egy virtuális hálózat és egy virtuális hálózati átjáró létrehozva és teljesen kiépítve. A [ExpressRoute virtuális hálózati átjárójának létrehozásához](expressroute-howto-add-gateway-resource-manager.md)kövesse az utasításokat. A ExpressRoute virtuális hálózati átjárója a "ExpressRoute" GatewayType használja, nem pedig a VPN-t.

* Akár 10 virtuális hálózatot is összekapcsolhat egy standard ExpressRoute áramkörrel. Szabványos ExpressRoute áramkör használata esetén minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie.

* Egyetlen VNet legfeljebb négy ExpressRoute-áramkörhöz lehet kapcsolni. A következő folyamat használatával hozzon létre egy új kapcsolati objektumot minden olyan ExpressRoute-áramkörhöz, amelyhez csatlakozik. A ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetésekben vagy a kettő kombinációjában is.

* Ha engedélyezi a ExpressRoute prémium bővítményt, a ExpressRoute-áramkör geopolitikai régióján kívüli virtuális hálózatokat is összekapcsolhat. A prémium szintű bővítmény azt is lehetővé teszi, hogy a kiválasztott sávszélességtől függően több mint 10 virtuális hálózatot csatlakoztasson a ExpressRoute-áramkörhöz. A prémium szintű bővítménysel kapcsolatos további információkért olvassa el a [Gyakori kérdések](expressroute-faqs.md) című részt.

* A lépések megkezdése előtt [megtekintheti a videót](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) .

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>VNet összekapcsolása egy áramköri előfizetéssel

> [!NOTE]
> A BGP konfigurációs adatai nem jelennek meg, ha a 3. rétegbeli szolgáltató konfigurálta a társait. Ha az áramkör kiépített állapotban van, kapcsolatokat hozhat létre.
>

### <a name="to-create-a-connection"></a>Kapcsolat létrehozásához

1. Győződjön meg arról, hogy a ExpressRoute-áramkör és az Azure Private-társítása sikeresen konfigurálva van. Kövesse a ExpressRoute- [áramkör létrehozása](expressroute-howto-circuit-arm.md) és a [ExpressRoute-kör társításának létrehozása és módosítása](expressroute-howto-routing-arm.md)című témakör utasításait. A ExpressRoute-áramkörnek a következő képhez hasonlóan kell kinéznie:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

2. Most már megkezdheti a kapcsolat létesítését, hogy a virtuális hálózati átjárót a ExpressRoute áramkörhöz kapcsolja. Válassza a **Kapcsolódás**  >  **Hozzáadás** lehetőséget a **Kapcsolódás hozzáadása** lap megnyitásához, majd konfigurálja az értékeket.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

3. A kapcsolat sikeres konfigurálását követően a kapcsolati objektum megjeleníti a kapcsolat információit.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>VNet összekapcsolása áramkörhöz – eltérő előfizetés

Megoszthat egy ExpressRoute-áramkört több előfizetésen keresztül is. Az alábbi ábrán egy egyszerű sematikus ábrán látható, hogy a megosztás hogyan működik a ExpressRoute-áramkörök több előfizetésen keresztül.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

A nagyméretű felhőben lévő kisebb felhőket a szervezeten belüli különböző részlegekhez tartozó előfizetések ábrázolására használják. A szervezeten belüli részlegek a saját előfizetését használják szolgáltatásaik üzembe helyezéséhez – de egyetlen ExpressRoute áramkört is megoszthatnak a helyszíni hálózathoz való kapcsolódáshoz. Egyetlen részleg (ebben a példában: IT) a ExpressRoute áramkör tulajdonosa lehet. A szervezeten belüli egyéb előfizetések a ExpressRoute áramkört is használhatják.

  > [!NOTE]
  > A dedikált áramkör kapcsolati és sávszélesség-díjait a rendszer a ExpressRoute Circuit tulajdonosára alkalmazza. Minden virtuális hálózat ugyanazt a sávszélességet használja.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Felügyelet – az áramkör tulajdonosai és a Circuit-felhasználók

Az "áramkör tulajdonosa" a ExpressRoute Circuit erőforrásának jogosult Kiemelt felhasználója. Az áramkör tulajdonosa létrehozhat olyan engedélyeket, amelyeket az "áramköri felhasználók" is beválthatnak. Az áramköri felhasználók olyan virtuális hálózati átjárók tulajdonosai, amelyek nem ugyanabban az előfizetésben találhatók, mint a ExpressRoute áramkör. Az áramköri felhasználók beválthatják az engedélyeket (egy-egy hitelesítés virtuális hálózatonként).

Az áramkör tulajdonosa bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedély visszavonása az összes olyan kapcsolati kapcsolat törlését eredményezi, amelynek a hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramköri tulajdonosi műveletek

**A kapcsolódási engedély létrehozása**

Az áramkör tulajdonosa létrehoz egy engedélyt, amely létrehoz egy engedélyezési kulcsot, amelyet egy áramköri felhasználó a virtuális hálózati átjárók ExpressRoute áramkörhöz való kapcsolódásához használ. Egy engedély csak egy kapcsolatban érvényes.

> [!NOTE]
> Minden kapcsolathoz külön engedély szükséges.
>

1. A ExpressRoute lapon válassza az **engedélyek** lehetőséget, majd írja be az engedélyezés **nevét** , majd válassza a **Mentés**lehetőséget.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

2. A konfiguráció mentése után másolja ki az **erőforrás-azonosítót** és az **engedélyezési kulcsot**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

**A kapcsolódási engedélyek törlése**

A kapcsolatok törléséhez válassza a **Törlés** ikont a kapcsolatok lapján.

### <a name="circuit-user-operations"></a>Áramkör felhasználói műveletei

Az áramkör-felhasználónak szüksége van az erőforrás-AZONOSÍTÓra és egy engedélyezési kulcsra az áramkör tulajdonosától.

**A kapcsolódási engedély beváltása**

1. Kattintson az **+ új** gombra.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

2. Keresse meg a **"kapcsolatok"** kifejezést a piactéren, jelölje ki, majd válassza a **Létrehozás**lehetőséget.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/search-connection.png" alt-text="Képernyőkép a ExpressRoute áramkörről" értékre van állítva.
4. Adja meg a részleteket, majd az alapismeretek lapon kattintson az **OK gombra** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

5. A **Beállítások** lapon válassza ki a **virtuális hálózati átjárót** , és jelölje be az **Engedélyezés beváltása** jelölőnégyzetet.
6. Adja meg az **engedélyezési kulcsot** és a **társ áramköri URI** -t, és adjon nevet a kapcsolatnak. Válassza az **OK** lehetőséget. A **társ áramköri URI** a ExpressRoute áramkör erőforrás-azonosítója (amelyet a ExpressRoute-áramkör tulajdonságok beállítása paneljén talál).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Képernyőkép a ExpressRoute áramkörről":::

7. Tekintse át az **Összegzés** lapon található információkat, majd kattintson **az OK gombra**.

**A kapcsolódási engedély felszabadítása**

Az engedélyezést a ExpressRoute áramkört a virtuális hálózathoz összekötő kapcsolat törlésével lehet felszabadítani.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti a kapcsolatot, és leválaszthatja a VNet egy ExpressRoute-áramkörhöz úgy, hogy a kapcsolat lapján a **Törlés** ikonra kattint.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztatható egy virtuális hálózat egy adott előfizetésben és egy másik előfizetésben található áramkörhöz. Az ExpressRoute-átjáróval kapcsolatos további információkért lásd: 

> [!div class="nextstepaction"]
> [Tudnivalók a ExpressRoute virtuális hálózati átjárókkal kapcsolatban](expressroute-about-virtual-network-gateways.md)
