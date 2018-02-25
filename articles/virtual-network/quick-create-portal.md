---
title: "Virtuális hálózat létrehozása az Azure - portál |} Microsoft Docs"
description: "Gyorsan bemutatják, hogyan hozzon létre egy virtuális hálózatot az Azure portál használatával. A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások közvetlenül kommunikálhatnak egymással."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b1dbe96b9f522474cd2eeb2b63f3429f9ea4d8ed
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Virtuális hálózat létrehozása az Azure Portallal

Ebből a cikkből megismerheti, hogyan hozhat létre virtuális hálózatot. Miután létrehozta a virtuális hálózat, a két virtuális gép a köztük folyó kommunikációt magánhálózati tesztelése a virtuális hálózat telepít.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza ki **+ új** az Azure portál bal felső sarkában.

2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.

3. Ahogy az alábbi képen is látható, adja meg a *myVirtualNetwork* a **neve**, *myResourceGroup* a **erőforráscsoport**, válassza ki a  **Hely** és a **előfizetés**, fogadja el a többi alapértelmezett beállításokat, majd válassza ki **létrehozása**. 

    ![A virtuális hálózat alapszintű adatainak megadása](./media/quick-create-portal/virtual-network.png)

    A **Címtéren** CIDR-formátumban van megadva. A virtuális hálózati nulla vagy több alhálózatot tartalmaz. Az alapértelmezett alhálózati **-címtartományt** a teljes címtartomány a virtuális hálózat a 10.0.0.0/24 használ, ezért nem sikerült létrehozni egy másik alhálózatot a virtuális hálózat az alapértelmezett és a tartományon belül. A megadott címtartomány a IP-címek 10.0.0.0-10.0.0.254 tartalmazza. Csak 10.0.0.4-10.0.0.254 érhetők el, mert Azure fenntartja az első négy címeket (0 – 3) és az utolsó cím minden alhálózatban. Az elérhető IP-címek vannak hozzárendelve a virtuális hálózaton belül üzembe helyezett erőforrás.

## <a name="test-network-communication"></a>Tesztelje a hálózati kommunikációban

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások közvetlenül kommunikálhatnak egymással. A virtuális hálózatba telepítése erőforrás egy típus egy virtuális gépet. Hozzon létre két virtuális gép a virtuális hálózat, hogy ellenőrizhesse a azokat egy későbbi lépésben titkos kommunikációját.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.

3. Adja meg az alábbi ábrán látható virtuális gép adatait. A **felhasználónév** és **jelszó** meg bejelentkezni a virtuális gép egy későbbi lépésben használt. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Válassza ki a **előfizetés**, válassza ki a meglévő *myResourceGroup* erőforrás csoportot, és győződjön meg arról, hogy a **hely** kijelölt van ugyanazon a helyen létrehozott a a virtuális hálózat. Ha elkészült, válassza ki a **OK**.

    ![Adja meg a virtuális géppel kapcsolatos alapvető információk](./media/quick-create-portal/virtual-machine-basics.png)

4. Válassza ki a virtuális gép méretét majd **válasszon**. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A méret, amely megjelenik az Ön eltérhetnek az alábbi példa: 

    ![Válassza ki a virtuális gép mérete](./media/quick-create-portal/virtual-machine-size.png)

5. A **beállítások**, *myVirtualNetwork* már választható ki **virtuális hálózati**, de ha nem, adja meg **virtuális hálózati** , majd válassza ki *myVirtualNetwork*. Hagyja *alapértelmezett* a kijelölt **alhálózati**, majd válassza ki **OK**.

    ![Válasszon virtuális hálózatot](./media/quick-create-portal/virtual-machine-network-settings.png)

6. Az a **összegzés** lapon jelölje be **létrehozása** elindítani a virtuális gépek telepítése során. 

7. A virtuális gép létrehozásához néhány percet vesz igénybe. A létrehozás után a virtuális gép rögzítve van az Azure portál Irányítópultjára, és automatikusan megnyílik a virtuális gép összegzése. Válassza ki **hálózati**.

    ![Virtuálisgép-hálózati adatok](./media/quick-create-portal/virtual-machine-networking.png)

    Tekintse meg, amely a **magán IP** cím *10.0.0.4*. Az 5. lépés, a **beállítások**, jelölt ki a *myVirtualNetwork* virtuális hálózati és elfogadta a nevű alhálózat *alapértelmezett* a **alhálózati**. Ha Ön [létrehozni a virtuális hálózati](#create-a-virtual-network), az alapértelmezett érték a 10.0.0.0/24 alhálózat elfogadott **-címtartományt**. Azure DHCP-kiszolgáló az első elérhető cím az alhálózat választja rendel a virtuális gép. Mivel Azure fenntartja az egyes alhálózatokon első négy címe (0 – 3), a 10.0.0.4 áll az első elérhető IP-cím az alhálózat érhető el.

    A **nyilvános IP-cím** hozzárendelt címe különbözik a címet hozzárendelni a virtuális géphez. Azure egy nyilvános, Internet irányítható IP-címet rendel alapértelmezés szerint a virtuális gépeken. A nyilvános IP-cím hozzá van rendelve a virtuális gép egy [rendelt egyes Azure-régiókban címkészletet](https://www.microsoft.com/download/details.aspx?id=41653). Azure tudja, melyik nyilvános IP-cím hozzá van rendelve egy virtuális gépet, miközben a virtuális gépen futó operációs rendszer van nincs tájékoztatási bármely nyilvános IP-cím hozzárendelve.

8. 1-7 lépéseket újra, de a 3, a virtuális gép neve *myVm2*. 

9. A virtuális gép létrehozása után válassza ki a **hálózati**, ahogy meg volt a 7. lépés. Megjelenik a **magán IP** cím *10.0.0.5*. Mivel Azure korábban hozzárendelt első használható címét *10.0.0.4* az alhálózat a *myVm1* virtuális gép, mert hozzárendelve *10.0.0.5* számára a  *myVm2* virtuális gép, mert ez volt a következő érhető el az alhálózati cím.

### <a name="connect-to-a-virtual-machine"></a>Csatlakozzon a virtuális géphez

1. Távoli kapcsolódás a *myVm1* virtuális gépet. Adja meg az Azure portál felső részén *myVm1*. Ha **myVm1** megjelenik a keresési eredmények között, jelölje be. Válassza ki a **Connect** gombra.

    ![Virtuális gépek – áttekintés](./media/quick-create-portal/virtual-machine-overview.png)

2. Kijelölése után a **Connect** gombra, a távoli asztal protokoll (RDP) fájl jön létre, és a számítógép letölti.  

3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**. Adja meg a felhasználónevet és a virtuális gép létrehozásakor megadott jelszót, majd válassza ki **OK**. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** vagy **Folytatás** gombra a kapcsolat.

### <a name="validate-communication"></a>Kommunikációs ellenőrzése

Kísérlet történt Pingelje meg egy Windows virtuális gép leáll, mert ping keresztül nem engedélyezett a Windows tűzfal alapértelmezés szerint. Engedélyezi a ping *myVm1*, adja meg a következő parancsot a parancssorba:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Kommunikáció a érvényesítéséhez *myVm2*, adja meg a következő parancsot a parancssorba a a *myVm1* virtuális gépet. A virtuális gép létrehozásakor használt hitelesítő adatait, és fejezze be a kapcsolatot:

```
mstsc /v:myVm2
```

A távoli asztali kapcsolat nem sikeres, mert mindkét virtuális gép van rendelve a magánhálózati IP-címek a *alapértelmezett* alhálózat és a távoli asztal meg nyitva, alapértelmezés szerint a Windows tűzfalon keresztül. Biztosan csatlakozni tudjanak *myVm2* állomásnév szerint, mert az Azure DNS-névfeloldás automatikusan biztosít a virtuális hálózaton belül minden gazdagép. Egy parancssorból ping *myVm1*, a *myVm2*.

```
ping myvm1
```

A ping parancs sikeres, mert akkor engedélyezett, a Windows tűzfalon keresztül a *myVm1* az előző lépésben a virtuális gép. Erősítse meg a kimenő kommunikációt az interneten, adja meg a következő parancsot:

```
ping bing.com
```

Négy választ kap bing.com. Alapértelmezés szerint a virtuális gépek virtuális hálózatban képes kommunikálni a kimenő internetkapcsolat. 

A távoli asztali munkamenet bezárásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Már nincs szükség, ha törli az erőforráscsoportot, és annak teljes tartalmát. Adja meg az Azure portál felső részén *myResourceGroup*. Ha **myResourceGroup** megjelenik a keresési eredmények között, jelölje be. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe egy alapértelmezett virtuális hálózatot egyetlen alhálózattal. Annak megismerése, hogyan hozhat létre egyéni virtuális hálózatot, több alhálózattal, továbbra is az oktatóanyag egy egyéni virtuális hálózat létrehozásához.

> [!div class="nextstepaction"]
> [Egyéni virtuális hálózat létrehozása](virtual-networks-create-vnet-arm-pportal.md#portal)
