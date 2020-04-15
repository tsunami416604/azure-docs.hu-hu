---
title: Hálózati forgalom útvonala - oktatóanyag – Azure portal
titlesuffix: Azure Virtual Network
description: Ez az oktatóanyag azt ismerteti, hogyan irányítható a hálózati forgalom egy útválasztási táblázattal az Azure Portalon.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 854bf2d1ed2155290c2ecebd17695049fdd0c8bb
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314286"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Oktatóanyag: Hálózati forgalom útválasztási táblázattal való irányítása az Azure Portalon

Az Azure alapértelmezés szerint a virtuális hálózaton belüli összes alhálózat közötti forgalmat irányítja. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak akkor hasznosak, ha például az alhálózatok közötti forgalmat egy hálózati virtuális berendezésen (NVA) keresztül szeretné irányítani. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Forgalmat irányító hálózati virtuális berendezés létrehozása
> * Útválasztási táblázat létrehozása
> * Útvonal létrehozása
> * Útválasztási táblázat társítása alhálózattal
> * Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
> * Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ez az oktatóanyag az [Azure Portalt](https://portal.azure.com)használja. Az Azure [CLI-t](tutorial-create-route-table-cli.md) vagy az [Azure PowerShellt is használhatja.](tutorial-create-route-table-powershell.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-an-nva"></a>NVA létrehozása

A hálózati virtuális készülékek (NVA-k) olyan virtuális gépek, amelyek segítenek a hálózati funkciókban, például az útválasztás és a tűzfal optimalizálásában. Ez az oktatóanyag feltételezi, hogy **Windows Server 2016 Datacenter rendszert**használ. Ha szeretné, másik operációs rendszert is választhat.

1. Az [Azure Portal](https://portal.azure.com) menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget.

1. Válassza a Security > **Windows Server 2016 Datacenter (Biztonsági Windows Server 2016 Datacenter ) lehetőséget.** **Security**

    ![Windows Server 2016 Datacenter, Virtuális gép létrehozása, Azure portál](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. A **Virtuálisgép létrehozása** lap **Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Section | Beállítás | Műveletek |
    | ------- | ------- | ----- |
    | **Projekt részletei** | Előfizetés | Válassza ki az előfizetését. |
    | | Erőforráscsoport | Válassza **az Új létrehozása**lehetőséget, írja be a *myResourceGroup*parancsot, majd kattintson **az OK gombra.** |
    | **Példány részletei** | Virtuális gép neve | Adja *myVmNva*. |
    | | Régió | Válassza **ki (US) Kelet-USA**. |
    | | Rendelkezésre állási beállítások | Válassza **a Nincs szükség infrastruktúra-redundanciára**lehetőséget. |
    | | Kép | Válassza a **Windows Server 2016 Datacenter lehetőséget.** |
    | | Méret | Tartsa meg az alapértelmezett, **Standard DS1 v2**. |
    | **Rendszergazdai fiók** | Felhasználónév | Adjon meg egy tetszőleges felhasználónevet. |
    | | Jelszó | Adjon meg egy ön által választott jelszót, amelynek legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek.](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) |
    | | Jelszó megerősítése | Írja be újra a jelszót. |
    | **Bejövőport-szabályok** | Nyilvános bejövő portok | Válassza a **Nincs (Nincs) választót.** |
    | **Takarítson meg pénzt** | Már rendelkezik Windows Server licenccel? | Válassza a **Nem (Nem) választót.** |

    ![Alapok, Hozzon létre egy virtuális gépet, Azure Portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Ezután válassza **a Tovább : Lemezek >** lehetőséget.

1. A **Lemezek csoportban**válassza ki az igényeinek megfelelő beállításokat, majd válassza **a Tovább : Hálózatkezelés >** lehetőséget.

1. A **Hálózat:**

    1. **Virtuális hálózat esetén**válassza az Új létrehozása **lehetőséget.**
    
    1. A **Virtuális hálózat létrehozása** párbeszédpanel **Neve mezőjébe**írja be a *myVirtualNetwork*című nevet.

    1. A **Címtérben**cserélje le a meglévő címtartományt *10.0.0.0/16-ra.*

    1. Az **Alhálózatok csoportban**válassza a **Törlés** ikont a meglévő alhálózat törléséhez, majd adja meg az **Alhálózat név** és **a címtartomány**következő kombinációit. Ha érvényes nevet és tartományt ad meg, egy új üres sor jelenik meg alatta.

        | Alhálózat neve | Címtartomány |
        | ----------- | ------------- |
        | *Nyilvános* | *10.0.0.0/24* |
        | *Privát* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. A párbeszédpanelből való kilépéshez kattintson az **OK gombra.**

    1. Az **Alhálózat ban**válassza a **DMZ (10.0.2.0/24) lehetőséget.**

    1. A **nyilvános IP-ben**válassza a **Nincs**lehetőséget, mivel ez a virtuális gép nem csatlakozik az interneten keresztül.

    1. Válassza **a Tovább : Felügyeleti >** lehetőséget.

1. **Irányítás alatt:**

    1. A **Diagnosztika tárfiókban**válassza **az Új létrehozása lehetőséget.**
    
    1. A **Tárfiók létrehozása** párbeszédpanelen adja meg vagy jelölje ki ezt az információt:

        | Beállítás | Érték |
        | ------- | ----- |
        | Név | *mynvastorageszámla* |
        | Fióktípus | **Tárolás (általános célú v1)** |
        | Teljesítmény | **Standard** |
        | Replikáció | **Helyileg redundáns tárolás (LRS)** |
    
    1. A párbeszédpanelből való kilépéshez kattintson az **OK gombra.**

    1. Válassza az **Áttekintés + létrehozás** lehetőséget. A rendszer a **Véleményezés + létrehozás** lapot kapja, és az Azure érvényesíti a konfigurációt.

1. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

    A virtuális gép üzembe helyezése néhány percet vesz igénybe. Várjon, amíg az Azure befejezi a virtuális gép létrehozását. A **Központi telepítés folyamatban van** lapon mutatja a központi telepítés részleteit.

1. Amikor a virtuális gép készen áll, válassza **az Ugrás az erőforrásra**lehetőséget.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

1. Az [Azure Portal](https://portal.azure.com) menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget.

2. A keresőmezőbe írja be az *Útvonal táblát*. Amikor **az Útvonal tábla** megjelenik a keresési eredmények között, jelölje ki azt.

3. Az **Útvonaltábla** lapon válassza a **Létrehozás gombot.**

4. Az **Útvonaltábla létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | *myRouteTablePublic* |
    | Előfizetés | Az Ön előfizetése |
    | Erőforráscsoport | **myResourceGroup** |
    | Hely | **(US) USA keleti része** |
    | Virtuális hálózati átjáró útvonalának propagálása | **Engedélyezve** |

    ![Útvonaltábla létrehozása, Azure Portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Kattintson a **Létrehozás** gombra.

## <a name="create-a-route"></a>Útvonal létrehozása

1. Az [útvonaltábla](https://portal.azure.com) kezeléséhez nyissa meg az Azure Portalt. Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Válassza ki az útvonaltábla nevét (**myRouteTablePublic**).

1. Válassza **az Útvonalak** > **hozzáadása lehetőséget.**

    ![Útvonal, útvonaltábla, Azure-portál hozzáadása](./media/tutorial-create-route-table-portal/add-route.png)

1. Az **Útvonal hozzáadása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Útvonal neve | *ToPrivateSubnet* |
    | Címelőtag | *10.0.1.0/24* (a korábban létrehozott *Privát* alhálózat címtartománya) |
    | A következő ugrás típusa | **Virtuális berendezés** |
    | A következő ugrás címe | *10.0.2.4* (a *DMZ* alhálózat címtartományán belüli cím) |

1. Válassza **az OK gombot.**

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

1. A virtuális hálózat kezeléséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keressen és válassza a **Virtuális hálózatok lehetőséget.**

1. Válassza ki a nevét a virtuális hálózat (**myVirtualNetwork**).

1. A virtuális hálózat menüsorában válassza az **Alhálózatok**lehetőséget.

1. A virtuális hálózat alhálózatlistájában válassza a **Nyilvános**lehetőséget.

1. Az **Útvonal táblában**válassza ki a létrehozott útvonaltáblát **(myRouteTablePublic**), majd a **Mentés** lehetőséget, ha az útvonaltáblát a *nyilvános* alhálózathoz szeretné társítani.

    ![Társútvonal-tábla, alhálózati lista, virtuális hálózat, Azure-portál](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Az IP-továbbítás bekapcsolása

Ezután kapcsolja be az IP-továbbítást az új NVA virtuális géphez, *a myVmNva-hoz.* Amikor az Azure hálózati forgalmat küld *a myVmNva,* ha a forgalom egy másik IP-címre irányul, IP-továbbítás a forgalmat a megfelelő helyre.

1. A virtuális gép kezeléséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keressen és válasszon **virtuális gépek**lehetőséget.

1. Válassza ki a nevét a virtuális gép (**myVmNva**).

1. Az NVA virtuális gép menüsorában válassza a **Hálózat lehetőséget.**

1. Válassza a **myvmnva123 lehetőséget.** Ez a virtuális géphez létrehozott, az Azure által létrehozott hálózati felület. Az Azure számokat ad hozzá az egyedi név biztosításához.

    ![Hálózatkezelés, hálózati virtuális berendezés (NVA) virtuális gép (VM), Azure portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. A hálózati csatoló menüsorában válassza az **IP-konfigurációk**lehetőséget.

1. Az **IP-konfigurációk** lapon állítsa az **IP-továbbítást** **Engedélyezve**értékre, majd válassza a **Mentés**lehetőséget.

    ![IP-továbbítás engedélyezése, IP-konfigurációk, hálózati adapter, hálózati virtuális berendezés (VVA), Azure portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Nyilvános és privát virtuális gépek létrehozása

Hozzon létre egy nyilvános virtuális gépet és egy privát virtuális gépet a virtuális hálózatban. Később fogja használni őket, hogy az Azure útvonalak a *nyilvános* alhálózati forgalmat a *magán* alhálózat az NVA-n keresztül.

A nyilvános virtuális gép és a privát virtuális gép létrehozásához kövesse az [NVA](#create-an-nva) létrehozása korábbi lépéseit. Nem kell megvárnia, hogy az üzembe helyezés befejeződjen, vagy lépjen a virtuális gép erőforrás. A legtöbb beállítást ugyanazokat a beállításokat fogja használni, kivéve az alábbiakban leírtakat.

Mielőtt **a Létrehozás** lehetőséget választja a nyilvános vagy privát virtuális gép létrehozásához, lépjen a következő két alszakaszra ([Nyilvános virtuális gép](#public-vm) és privát [virtuális gép](#private-vm)), amelyek a különböző értékeket mutatják. Folytathatja a következő szakaszt ([A forgalom átirányítása egy NVA-n keresztül)](#route-traffic-through-an-nva)után az Azure befejezi mindkét virtuális gép üzembe helyezését.

### <a name="public-vm"></a>Nyilvános virtuális gép

| Tab | Beállítás | Érték |
| --- | ------- | ----- |
| Alapvető beállítások | Erőforráscsoport | **myResourceGroup** |
| | Virtuális gép neve | *myVmPublic* |
| | Nyilvános bejövő portok | **Kijelölt portok engedélyezése** |
| | Bejövő portok kiválasztása | **RDP** |
| Hálózat | Virtuális hálózat | **myVirtualNetwork** |
| | Alhálózat | **Nyilvános (10.0.0.0/24)** |
| | Nyilvános IP-cím | Az alapértelmezett |
| Kezelés | Diagnosztikai tárfiók | **mynvastorageszámla** |

### <a name="private-vm"></a>Privát virtuális gép

| Tab | Beállítás | Érték |
| --- | ------- | ----- |
| Alapvető beállítások | Erőforráscsoport | **myResourceGroup** |
| | Virtuális gép neve | *myVmPrivate* |
| | Nyilvános bejövő portok | **Kijelölt portok engedélyezése** |
| | Bejövő portok kiválasztása | **RDP** |
| Hálózat | Virtuális hálózat | **myVirtualNetwork** |
| | Alhálózat | **Privát (10.0.1.0/24)** |
| | Nyilvános IP-cím | Az alapértelmezett |
| Kezelés | Diagnosztikai tárfiók | **mynvastorageszámla** |

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Bejelentkezés a myVmPrivate szolgáltatásba távoli asztalon keresztül

1. Nyissa meg az [Azure Portalon](https://portal.azure.com) a saját virtuális gép kezeléséhez. Keressen és válasszon **virtuális gépek**lehetőséget.

1. Válassza ki a saját virtuális gép (**myVmPrivate**) nevét.

1. A virtuális gép menüsorában válassza a **Csatlakozás** lehetőséget, ha távoli asztali kapcsolatot szeretne létrehozni a saját virtuális géppel.

1. A **Csatlakozás rdp-vel** lapon válassza **az RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépre.

1. Nyissa meg a letöltött *.rdp* fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Válassza a **További lehetőségek** > **Lehetőséget: Másik fiók használatával,** majd adja meg a saját virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

1. Válassza **az OK gombot.**

1. Ha a bejelentkezési folyamat során tanúsítványfigyelmeztetést kap, válassza az **Igen** lehetőséget a virtuális géphez való csatlakozáshoz.

### <a name="enable-icmp-through-the-windows-firewall"></a>Az ICMP engedélyezése a Windows tűzfalon keresztül

Egy későbbi lépésben a nyomkövetési útvonal eszközt fogja használni az útválasztás teszteléséhez. A nyomkövetési útvonal az Internet Control Message Protocol (ICMP) protokollt használja, amelyet a Windows tűzfal alapértelmezés szerint megtagad. Engedélyezze az ICMP-t a Windows tűzfalon keresztül.

1. A *myVmPrivate*távoli asztalán nyissa meg a PowerShellt.

1. Írja be ezt a parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ebben az oktatóanyagban nyomkövetési útvonalat fog használni az útválasztás teszteléséhez. Éles környezetben nem javasoljuk az ICMP használatát a Windows tűzfalon keresztül.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Az IP-továbbítás bekapcsolása a myVmNva-n belül

[Bekapcsolta az IP-továbbítást](#turn-on-ip-forwarding) a virtuális gép hálózati felületén az Azure használatával. A virtuális gép operációs rendszerének is továbbítania kell a hálózati forgalmat. Kapcsolja be az IP-továbbítást a *myVmNva* VM operációs rendszeréhez ezekkel a parancsokkal.

1. A *myVmPrivate* virtuális gép parancssorából nyisson meg egy távoli asztalt a *myVmNva* virtuális géphez:

    ```cmd
    mstsc /v:myvmnva
    ```

1. A *myVmNva* virtuális gép PowerShell jének használatával adja meg ezt a parancsot az IP-továbbítás bekapcsolásához:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Indítsa újra a *myVmNva* virtuális gép: A tálcán válassza a **Start** > **Power**, Egyéb **(Tervezett)** > **Folytatás**lehetőséget.

    Ez a távoli asztali munkamenetet is bontja.

1. A *myVmNva* virtuális gép újraindítása után hozzon létre egy távoli asztali munkamenetet a *myVmPublic* virtuális géphez. Miközben továbbra is a *myVmPrivate* virtuális géphez csatlakozik, nyisson meg egy parancssort, és futtassa a parancsot:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. A *myVmPublic*távoli asztalán nyissa meg a PowerShellt.

1. Engedélyezze az ICMP-t a Windows tűzfalon keresztül a következő parancs megadásával:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Hálózati forgalom útválasztásának tesztelése

Először tesztelje a hálózati forgalom útválasztását a *myVmPublic* virtuális gépről a *myVmPrivate* virtuális gépre.

1. A *MyVmPublic* virtuális gép PowerShell jének használatával adja meg ezt a parancsot:

    ```powershell
    tracert myVmPrivate
    ```

    A válasz hasonló ehhez a példához:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Láthatja, hogy az első ugrás a 10.0.2.4, amely az NVA privát IP-címe. A második ugrás a *myVmPrivate* VM privát IP-címére: 10.0.1.4. Korábban hozzáadta az útvonalat a *myRouteTablePublic* útvonaltáblához, és társította a *nyilvános* alhálózathoz. Ennek eredményeképpen az Azure az NVA-n keresztül küldte el a forgalmat, és nem közvetlenül a *privát* alhálózatnak.

1. Zárja be a *myVmPublic* virtuális gépre irányuló távoli asztali munkamenetet. Ez nem bontja a *myVmPrivate* virtuális géppel való kapcsolatot.

1. A *myVmPrivate* virtuális gép parancssorából írja be ezt a parancsot:

    ```cmd
    tracert myVmPublic
    ```

    Ez a parancs a *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre irányuló hálózati forgalom útválasztását teszteli. A válasz hasonló ehhez a példához:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Láthatja, hogy az Azure közvetlenül a *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre irányítja a forgalmat. Alapértelmezés szerint az Azure közvetlenül irányítja a forgalmat az alhálózatok között.

1. Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforráscsoportra már nincs szükség, törölje a *myResourceGroup csoportot* és az összes erőforrást:

1. Az erőforráscsoport kezeléséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és jelölje ki **az Erőforráscsoportok lehetőséget.**

1. Válassza ki az erőforráscsoport nevét (**myResourceGroup**).

1. Válassza az **Erőforráscsoport törlése** elemet.

1. A megerősítést kérő párbeszédpanelen adja meg a *myResourceGroup* parancsot **az ERŐFORRÁSCSOPORT NEVÉBE BEÍRÁShoz,** majd kattintson a **Törlés gombra.** Az Azure törli a *myResourceGroup* és az erőforráscsoporthoz kötött összes erőforrást, beleértve az útvonaltáblákat, a tárfiókokat, a virtuális hálózatokat, a virtuális gépeket, a hálózati adaptereket és a nyilvános IP-címeket.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Mostantól különböző előre konfigurált nva-kat telepíthet az [Azure Marketplace-ről,](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)amelyek számos hasznos hálózati funkciót biztosítanak. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Bár számos Azure-erőforrást telepíthet egy virtuális hálózaton belül, az Azure nem tud erőforrásokat telepíteni egyes PaaS-szolgáltatásokhoz egy virtuális hálózatba. Egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférés korlátozása lehetséges, bár a korlátozás csak virtuális hálózati alhálózatból érkező forgalom lehet. Ha tudni szeretné, hogyan korlátozhatja a hálózati hozzáférést az Azure PaaS-erőforrásokhoz, tekintse meg a következő oktatóanyag.

> [!div class="nextstepaction"]
> [PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](tutorial-restrict-network-access-to-resources.md)
