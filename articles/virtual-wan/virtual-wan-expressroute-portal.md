---
title: 'Azure virtuális WAN: ExpressRoute-kapcsolatok létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Virtual WAN-t az Azure-hoz és a helyszíni környezetekhez való ExpressRoute-kapcsolatok létrehozásához.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 5a394f93c5cc606c26ffa1cc64bf1d61617b05b8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014927"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Oktatóanyag: ExpressRoute-társítás létrehozása az Azure Virtual WAN használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Virtual WAN-t az Azure-beli erőforrásokhoz való kapcsolódáshoz egy ExpressRoute áramkörben. A virtuális WAN-és virtuális WAN-erőforrásokkal kapcsolatos további információkért tekintse meg a [virtuális WAN áttekintése](virtual-wan-about.md)című témakört.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Hub és átjáró létrehozása
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * Áramkör összekapcsolása egy hub-átjáróval
> * Kapcsolat tesztelése
> * Átjáró méretének módosítása
> * Alapértelmezett útvonal meghirdetése

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Rendelkezik egy virtuális hálózattal, amelyhez csatlakozni szeretne. Győződjön meg arról, hogy a helyszíni hálózatok egyik alhálózata sem fedi át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Ha virtuális hálózatot szeretne létrehozni a Azure Portalban, tekintse meg a rövid [útmutatót.](../virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat átjáróval rendelkezik (VPN vagy ExpressRoute), akkor el kell távolítania az összes átjárót. Ehhez a konfigurációhoz az szükséges, hogy a virtuális hálózatok a virtuális WAN hub-átjáróhoz legyenek csatlakoztatva.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális WAN által létrehozott és használt virtuális hálózat. Az hubhoz megadott címtartomány nem fedi át a meglévő virtuális hálózatait, amelyhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtartományok körét, akkor egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="openvwan"></a>Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

1. Navigáljon a virtuális WAN lapra. A portálon kattintson az **+Erőforrás létrehozása** gombra. Írja be a **virtuális WAN** kifejezést a keresőmezőbe, majd válassza az ENTER billentyűt.
2. Válassza ki a **virtuális WAN** elemet az eredmények közül. A virtuális WAN lapon kattintson a **Létrehozás** elemre a WAN létrehozása lap megnyitásához.
3. A **WAN létrehozása** lap **alapok** lapján töltse ki a következő mezőket:

   ![WAN létrehozása](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt.
   * **Erőforráscsoport helye** – válasszon ki egy erőforrás-helyet a legördülő listából. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
   * **Név** – írja be a WAN-híváshoz használni kívánt nevet.
   * **Típus** – válassza a **standard**lehetőséget. Nem hozhat létre ExpressRoute-átjárót az alapszintű SKU használatával.
4. Miután befejezte a mezők kitöltését, válassza a **felülvizsgálat + létrehozás**lehetőséget.
5. Az ellenőrzés után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.

## <a name="hub"></a>Virtuális központ és átjáró létrehozása

A virtuális központ egy virtuális WAN által létrehozott és használt virtuális hálózat. Több átjárót is tartalmazhat, például a VPN-t és a ExpressRoute. Ebben a szakaszban létre fog hozni egy ExpressRoute-átjárót a virtuális hubhoz. Létrehozhatja az átjárót [új virtuális központ létrehozásakor](#newhub), vagy egy [meglévő hubhoz](#existinghub) is létrehozhatja az átjárót szerkesztéssel. 

A ExpressRoute-átjárók 2 GB/s egységekben vannak kiépítve. 1 méretezési egység = 2 GB/s, amely legfeljebb 10 méretezési egységet támogat: 20 GB/s. A virtuális központ és az átjáró teljes létrehozása körülbelül 30 percet vesz igénybe.

### <a name="newhub"></a>Új virtuális központ és átjáró létrehozása

Hozzon létre egy új virtuális hubot. A hub létrehozása után a központ díjat számítunk fel, még akkor is, ha nem csatlakoztat semmilyen helyet.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="existinghub"></a>Átjáró létrehozása meglévő hubhoz

Az átjárót egy meglévő hubhoz is létrehozhatja szerkesztéssel.

1. Navigáljon a szerkeszteni kívánt virtuális hubhoz, és válassza ki azt.
2. A **virtuális központ szerkesztése** lapon jelölje be a ExpressRoute- **átjáró belefoglalása**jelölőnégyzetet.
3. A módosítások megerősítéséhez válassza a **Confirm (megerősítés** ) lehetőséget. Körülbelül 30 percet vesz igénybe, hogy a hub és a hub erőforrásai teljesen létre legyenek hozni.

   ![meglévő központ](./media/virtual-wan-expressroute-portal/edithub.png "központ szerkesztése")

### <a name="to-view-a-gateway"></a>Átjáró megtekintése

Miután létrehozott egy ExpressRoute-átjárót, megtekintheti az átjáró részleteit. Navigáljon a hubhoz, válassza a **ExpressRoute**lehetőséget, és tekintse meg az átjárót.

![Átjáró megtekintése](./media/virtual-wan-expressroute-portal/viewgw.png "átjáró megtekintése")

## <a name="connectvnet"></a>A VNet összekötése a hubhoz

Ebben a szakaszban a hub és a VNet közötti összekapcsolási kapcsolatot hozza létre. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolat** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval (sem VPN-, sem ExpressRoute).

## <a name="connectcircuit"></a>Az áramkör összekapcsolása a hub-átjáróval

Az átjáró létrehozása után összekapcsolhatja a [ExpressRoute áramkört](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) . Vegye figyelembe, hogy a ExpressRoute Global Reach által támogatott helyszíneken elérhető ExpressRoute prémium szintű áramkörök csatlakozhatnak egy virtuális WAN ExpressRoute-átjáróhoz.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Az áramkör összekapcsolása a hub-átjáróval

A portálon nyissa meg a **virtuális központ – > kapcsolat – > ExpressRoute** lapot. Ha egy ExpressRoute-áramkörhöz fér hozzá az előfizetéshez, a használni kívánt áramkör megjelenik az áramköri listán. Ha egyetlen áramkör sem jelenik meg, de egy engedélyezési kulccsal és egy társ áramköri URI azonosítóval rendelkezik, az áramkört beválthatja és összekapcsolhatja. [A kapcsolódáshoz egy engedélyezési kulcs beváltásával](#authkey)tájékozódhat.

1. Válassza ki az áramkört.
2. Válassza a **csatlakozási áramkör (eke) t**.

   ![áramkörök összekapcsolása](./media/virtual-wan-expressroute-portal/cktconnect.png "áramkörök összekapcsolása")

### <a name="authkey"></a>Kapcsolódás egy engedélyezési kulcs beváltásával

Használja a kapcsolódáshoz megadott engedélyezési kulcsot és az áramköri URI-t.

1. A ExpressRoute lapon kattintson a **+ engedélyezési kulcs beváltása** elemre.

   ![beváltása](./media/virtual-wan-expressroute-portal/redeem.png "beváltása")
2. Az engedélyezési kulcs beváltása lapon adja meg az értékeket.

   ![kulcs értékének beváltása](./media/virtual-wan-expressroute-portal/redeemkey2.png "kulcs értékének beváltása")
3. A kulcs hozzáadásához válassza a **Hozzáadás** lehetőséget.
4. Tekintse meg az áramkört. A beváltott áramkör csak a nevet jeleníti meg (típus, szolgáltató és egyéb információ nélkül), mert a felhasználótól eltérő előfizetésben szerepel.

## <a name="to-test-connectivity"></a>A kapcsolat tesztelése

Az áramköri kapcsolat létrejötte után a hub kapcsolati állapota "Ez a hub" lesz, ami azt jelenti, hogy a kapcsolat létrejött a hub ExpressRoute-átjáróhoz. Várjon körülbelül 5 percet, mielőtt teszteli a kapcsolatot a ExpressRoute-áramkör mögötti ügyféllel, például egy, a korábban létrehozott VNet található virtuális géppel.

Ha a virtuális WAN VPN-átjáróhoz olyan helyek vannak csatlakoztatva, amelyek ugyanabban a központban vannak, mint a ExpressRoute-átjáró, kétirányú kapcsolat létesíthető a VPN és a ExpressRoute végpontok között. A dinamikus útválasztás (BGP) támogatott. A hub átjáróinak ASN-je rögzített, és jelenleg nem szerkeszthető.

## <a name="to-change-the-size-of-a-gateway"></a>Átjáró méretének módosítása

Ha módosítani szeretné a ExpressRoute-átjáró méretét, keresse meg az ExpressRoute-átjárót az elosztón belül, és válassza ki a méretezési egységeket a legördülő listából. Mentse a változást. A hub-átjáró frissítése körülbelül 30 percet vesz igénybe.

![átjáró méretének módosítása](./media/virtual-wan-expressroute-portal/changescale.png "átjáró méretének módosítása")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Az alapértelmezett 0.0.0.0/0 útvonal meghirdetése végpontokra

Ha szeretné, hogy az Azure-beli virtuális központ meghirdesse az alapértelmezett 0.0.0.0/0 útvonalat a ExpressRoute, engedélyeznie kell az "alapértelmezett útvonal propagálása" beállítást.

1. Válassza ki az **áramkör->... – > a kapcsolat szerkesztése**lehetőséget.

   ![Kapcsolatok szerkesztése](./media/virtual-wan-expressroute-portal/defaultroute1.png "Kapcsolatok szerkesztése")

2. Válassza az **Engedélyezés** lehetőséget az alapértelmezett útvonal propagálásához.

   ![Alapértelmezett útvonal propagálása](./media/virtual-wan-expressroute-portal/defaultroute2.png "Alapértelmezett útvonal propagálása")

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.