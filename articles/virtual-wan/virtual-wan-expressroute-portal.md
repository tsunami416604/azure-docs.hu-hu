---
title: Oktatóanyag – ExpressRoute-kapcsolatok létrehozása az Azure Virtual WAN használatával
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre ExpressRoute-kapcsolatokat az Azure-ba és a helyszíni környezetbe az Azure-beli és helyszíni környezetekben az Azure-beli És helyszíni környezetekben az Azure-beli Virtuális WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 35ca071cd8495611f0f350511ef9406f82c5be23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209426"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Oktatóanyag: ExpressRoute-társítás létrehozása az Azure Virtual WAN használatával

Ez az oktatóanyag bemutatja, hogyan csatlakozhat a Virtual WAN használatával az Erőforrásokhoz az Azure-ban egy ExpressRoute-kapcsolaton keresztül. A Virtual WAN és a Virtual WAN erőforrásokról a Virtual WAN áttekintése című témakörben olvashat [bővebben.](virtual-wan-about.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Hub és átjáró létrehozása
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * Áramkör csatlakoztatása központi átjáróhoz
> * Kapcsolat tesztelése
> * Átjáró méretének módosítása
> * Alapértelmezett útvonal hirdetése

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Van egy virtuális hálózat, amelyhez csatlakozni szeretne. Ellenőrizze, hogy a helyszíni hálózatok egyik alhálózata sem fedi-e át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Virtuális hálózat létrehozásához az Azure Portalon tekintse meg a [gyorsútmutatót.](../virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat rendelkezik átjáróval (VPN vagy ExpressRoute), el kell távolítania az összes átjárót. Ez a konfiguráció megköveteli, hogy a virtuális hálózatok csatlakoznak helyett, a Virtual WAN hub átjáró.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális hálózat, amelyet a Virtual WAN hoz létre és használ. A hubhoz megadott címtartomány nem fedheti át a meglévő virtuális hálózatokat, amelyekhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációban található IP-címtartományokat, egyeztessen valakivel, aki meg tudja adni ezeket az adatokat.

* Az ExpressRoute-kapcsolatcsoportnak prémium szintű áramkörnek kell lennie ahhoz, hogy a központi átjáróhoz csatlakozhasson.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

1. Nyissa meg a Virtual WAN lapot. A portálon kattintson az **+Erőforrás létrehozása** gombra. Írja be a **Virtual WAN** parancsot a keresőmezőbe, és válassza az Enter lehetőséget.
2. Válassza ki a **Virtual WAN** az eredmények közül. A Virtual WAN lapon kattintson a **Létrehozás** gombra a WAN létrehozása lap megnyitásához.
3. A **WAN létrehozása** lapon az **Alapok** lapon töltse ki a következő mezőket:

   ![WAN létrehozása](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt.
   * **Erőforráscsoport helye** – Erőforrás helyének kiválasztása a legördülő menüből. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
   * **Név** – Írja be a WAN-nak hívni kívánt nevet.
   * **Típus** - Válassza a **Standard**lehetőséget. ExpressRoute-átjáró nem hozhat létre az alapszintű termékváltozat használatával.
4. Miután befejezte a mezők kitöltését, válassza a **Véleményezés +Létrehozás**gombot.
5. Az érvényesítés sikeressítése után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Virtuális elosztó és átjáró létrehozása

A virtuális hub egy virtuális hálózat, amelyet a Virtual WAN hoz létre és használ. Különböző átjárókat tartalmazhat, például VPN-t és ExpressRoute-ot. Ebben a szakaszban egy ExpressRoute-átjárót hoz létre a virtuális hubhoz. Létrehozhatja az [átjárót új virtuális központ létrehozásakor,](#newhub)vagy létrehozhatja az átjárót egy [meglévő hubban](#existinghub) szerkesztéssel. 

Az ExpressRoute-átjárók 2 Gb/s egységben vannak kiépítve. 1 méretezési egység = 2 Gbps támogatással akár 10 skála egység = 20 Gb/s. Körülbelül 30 percet vesz igénybe a virtuális hub és átjáró teljes létrehozása.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Új virtuális központ és átjáró létrehozása

Hozzon létre egy új virtuális központot. A hub létrehozása után a hubért díjat kell fizetnie, még akkor is, ha nem csatol webhelyeket.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Átjáró létrehozása meglévő hubon

Egy meglévő hubon is létrehozhat átjárót szerkesztéssel.

1. Nyissa meg a szerkesztni kívánt virtuális központot, és jelölje ki.
2. A **Virtuális központi központ szerkesztése** lapon jelölje be az **Include ExpressRoute átjáró jelölőnégyzetet.**
3. A módosítások megerősítéséhez válassza a **Megerősítés** lehetőséget. Körülbelül 30 percet vesz igénybe a hub és a hub erőforrások teljes létrehozása.

   ![meglévő központ](./media/virtual-wan-expressroute-portal/edithub.png "hub szerkesztése")

### <a name="to-view-a-gateway"></a>Átjáró megtekintése

Miután létrehozott egy ExpressRoute-átjárót, megtekintheti az átjáró részleteit. Nyissa meg a központot, válassza az **ExpressRoute**lehetőséget, és tekintse meg az átjárót.

![Átjáró megtekintése](./media/virtual-wan-expressroute-portal/viewgw.png "átjáró megtekintése")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>A virtuális hálózat csatlakoztatása a hubhoz

Ebben a szakaszban hozza létre a társviszony-létesítési kapcsolatot a hub és a virtuális hálózat között. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolat** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval (sem VPN, sem ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Az áramkör csatlakoztatása a központi átjáróhoz

Az átjáró létrehozása után egy [ExpressRoute-áramkört](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) csatlakoztathat hozzá. Az ExpressRoute Globális elérés által támogatott helyeken található ExpressRoute Premium-áramkörök csatlakozhatnak egy Virtual WAN ExpressRoute-átjáróhoz.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Az áramkör csatlakoztatása a központi átjáróhoz

A portálon nyissa meg a **virtuális hub -> Connectivity -> ExpressRoute** lapot. Ha van hozzáférése egy ExpressRoute-kapcsolatra szóló előfizetésében, az áramkörök listájában látni fogja a használni kívánt áramkört. Ha nem lát áramköröket, de rendelkezik egy engedélyezési kulccsal és társáramkör URI-val, beválthatja és csatlakoztathatja az áramkört. Lásd: [Csatlakozás engedélyezési kulcs beváltásával](#authkey).

1. Válassza ki az áramkört.
2. Válassza **az Áramkör(ek csatlakoztatása)** lehetőséget.

   ![áramkörök csatlakoztatása](./media/virtual-wan-expressroute-portal/cktconnect.png "áramkörök csatlakoztatása")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Csatlakozás engedélyezési kulcs beváltásával

Használja a csatlakoztatáshoz megadott engedélyezési kulcsot és áramköri URI-t.

1. Az ExpressRoute lapon kattintson a **+Engedélyezéskulcs beváltása gombra.**

   ![Megváltsa](./media/virtual-wan-expressroute-portal/redeem.png "Megváltsa")
2. Az Engedélyezés kulcs beváltása lapon töltse ki az értékeket.

   ![kulcsértékek beváltása](./media/virtual-wan-expressroute-portal/redeemkey2.png "kulcsértékek beváltása")
3. A kulcs hozzáadásához válassza a **Hozzáadás** lehetőséget.
4. Nézd meg az áramkört. A beváltott áramkör csak a nevet jeleníti meg (típus, szolgáltató és egyéb adatok nélkül), mert az más előfizetésben van, mint a felhasználó.

## <a name="to-test-connectivity"></a>A kapcsolat tesztelése

A kapcsolat létrejötte után a hub kapcsolati állapota "ezt a hubot" jelzi, ami azt jelenti, hogy a kapcsolat létrejön a hub ExpressRoute átjárójával. Várjon körülbelül 5 percet, mielőtt teszteli a kapcsolatot egy ügyfél mögött az ExpressRoute-kapcsolat, például egy virtuális gép a virtuális hálózat, amely korábban létrehozott.

Ha a helyek egy Virtuális WAN VPN-átjáróhoz csatlakoznak ugyanabban a hubban, mint az ExpressRoute-átjáró, kétirányú kapcsolatot biztosíthat a VPN és az ExpressRoute végpontjai között. A dinamikus útválasztás (BGP) támogatott. A hubátjáróinak ASN-je rögzített, és jelenleg nem szerkeszthető.

## <a name="to-change-the-size-of-a-gateway"></a>Átjáró méretének módosítása

Ha módosítani szeretné az ExpressRoute-átjáró méretét, keresse meg az ExpressRoute-átjárót a hubon belül, és válassza ki a méretezési egységeket a legördülő menüből. Mentse el a visszajárót. A központi átjáró frissítése körülbelül 30 percet vesz igénybe.

![átjáró méretének módosítása](./media/virtual-wan-expressroute-portal/changescale.png "átjáró méretének módosítása")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Az alapértelmezett útvonal hirdetése 0.0.0.0/0 végpontok között

Ha azt szeretné, hogy az Azure virtuális hub az alapértelmezett 0.0.0.0/0-s útvonalat hirdesse az ExpressRoute végpontjainak, engedélyeznie kell az "Alapértelmezett útvonal propagálása" beállítást.

1. Válassza ki **a Circuit ->...-> Edit kapcsolatot**.

   ![Kapcsolat szerkesztése](./media/virtual-wan-expressroute-portal/defaultroute1.png "Kapcsolat szerkesztése")

2. Az alapértelmezett útvonal propagálásához válassza az **Engedélyezés** lehetőséget.

   ![Alapértelmezett útvonal propagálása](./media/virtual-wan-expressroute-portal/defaultroute2.png "Alapértelmezett útvonal propagálása")

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
