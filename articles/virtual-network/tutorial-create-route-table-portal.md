---
title: Hálózati forgalom irányítása – oktatóanyag – Azure Portal
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
ms.openlocfilehash: d630a41f9b83a852605ffad2a85ad6dd14bbac73
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "86079649"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Oktatóanyag: Hálózati forgalom útválasztási táblázattal való irányítása az Azure Portalon

Az Azure alapértelmezés szerint átirányítja a forgalmat a virtuális hálózaton belüli összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak hasznosak lehetnek, ha például az alhálózatok közötti adatforgalmat egy hálózati virtuális berendezésen (NVA) keresztül szeretné irányítani. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Forgalmat irányító hálózati virtuális berendezés létrehozása
> * Útválasztási táblázat létrehozása
> * Útvonal létrehozása
> * Útválasztási táblázat társítása alhálózattal
> * Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
> * Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ez az oktatóanyag a [Azure Portal](https://portal.azure.com)használja. Használhatja az [Azure CLI](tutorial-create-route-table-cli.md) -t vagy a [Azure PowerShell](tutorial-create-route-table-powershell.md)is.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="create-an-nva"></a>NVA létrehozása

A hálózati virtuális berendezések (NVA) olyan virtuális gépek, amelyek segítik a hálózati funkciókat, például az útválasztást és a tűzfal optimalizálását. Ez az oktatóanyag feltételezi, hogy a **Windows Server 2016 Datacenter**rendszert használja. Ha kívánja, választhat másik operációs rendszert is.

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Válassza a **Biztonság**  >  **Windows Server 2016 Datacenter**lehetőséget.

    ![Windows Server 2016 Datacenter, hozzon létre egy virtuális gépet, Azure Portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. A **virtuális gép létrehozása** lap **alapismeretek**területén adja meg vagy válassza ki az alábbi adatokat:

    | Section | Beállítás | Műveletek |
    | ------- | ------- | ----- |
    | **Projekt részletei** | Előfizetés | Válassza ki az előfizetését. |
    | | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | **Példány részletei** | Virtuális gép neve | Adja meg a *myVmNva*. |
    | | Region | Válassza az USA **keleti**régióját. |
    | | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges**lehetőséget. |
    | | Kép | Válassza a **Windows Server 2016 Datacenter**lehetőséget. |
    | | Méret | Tartsa meg az alapértelmezett, **standard DS1 v2**értéket. |
    | **Rendszergazdai fiók** | Felhasználónév | Adjon meg egy tetszőleges felhasználónevet. |
    | | Jelszó | Adja meg a választott jelszót, amelynek legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **Bejövőport-szabályok** | Nyilvános bejövő portok | Válassza a **nincs lehetőséget**. |
    | **Pénz megtakarítása** | Már van Windows Server-licence? | Válassza a **nem**. |

    ![Alapismeretek, virtuális gép létrehozása, Azure Portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Ezután válassza a **Tovább: lemezek >** elemet.

1. A **lemezek**területen válassza ki az igényeinek megfelelő beállításokat, majd kattintson a **Tovább gombra: hálózatkezelés >**.

1. **Hálózat**alatt:

    1. A **Virtual Network (virtuális hálózat**) területen válassza az **új létrehozása**lehetőséget.
    
    1. A **virtuális hálózat létrehozása** párbeszédpanel Név mezőjébe írja be a *myVirtualNetwork* **nevet**.

    1. A **címterület mezőben**cserélje le a meglévő címtartományt a *10.0.0.0/16*kifejezésre.

    1. Az **alhálózatok**területen válassza a **Törlés** ikont a meglévő alhálózat törléséhez, majd adja meg az **alhálózat neve** és a **címtartomány következő kombinációit.** Az érvényes név és tartomány megadása után egy új üres sor jelenik meg.

        | Alhálózat neve | Címtartomány |
        | ----------- | ------------- |
        | *Nyilvános* | *10.0.0.0/24* |
        | *Privát* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. A párbeszédpanel bezárásához kattintson **az OK gombra** .

    1. Az **alhálózat**területen válassza a **DMZ (10.0.2.0/24)** lehetőséget.

    1. A **nyilvános IP-cím**területen válassza a **nincs**lehetőséget, mivel ez a virtuális gép nem kapcsolódik az internethez.

    1. Válassza a Next (tovább) lehetőséget **: felügyeleti >**.

1. **Felügyelet**alatt:

    1. A **diagnosztika Storage-fiók**területen válassza az **új létrehozása**lehetőséget.
    
    1. A **Storage-fiók létrehozása** párbeszédpanelen adja meg vagy válassza ki az alábbi adatokat:

        | Beállítás | Érték |
        | ------- | ----- |
        | Név | *mynvastorageaccount* |
        | Fiók altípusa | **Storage (általános célú v1)** |
        | Teljesítmény | **Standard** |
        | Replikáció | **Helyileg redundáns tárolás (LRS)** |
    
    1. A párbeszédpanel bezárásához kattintson **az OK gombra** .

    1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

    A virtuális gép üzembe helyezése néhány percet vesz igénybe. Várjon, amíg az Azure befejezi a virtuális gép létrehozását. A telepítés **folyamatban van** oldalon látható az üzembe helyezés részletei.

1. Ha a virtuális gép elkészült, válassza **az Ugrás az erőforráshoz**lehetőséget.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

1. Az [Azure Portal](https://portal.azure.com) menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. A keresőmezőbe írja be az *útválasztási táblázat*kifejezést. Ha az **útválasztási tábla** megjelenik a keresési eredmények között, válassza ki.

3. Az **útválasztási táblázat** lapon válassza a **Létrehozás**lehetőséget.

4. Az **útvonal létrehozása táblában**adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | *myRouteTablePublic* |
    | Előfizetés | Az Ön előfizetése |
    | Erőforráscsoport | **myResourceGroup** |
    | Hely | **USA USA keleti régiója** |
    | Virtuális hálózati átjáró útvonalának propagálása | **Engedélyezve** |

    ![Útválasztási táblázat létrehozása, Azure Portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Kattintson a **Létrehozás** gombra.

## <a name="create-a-route"></a>Útvonal létrehozása

1. Az útválasztási táblázat kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **útválasztási táblákat**.

1. Válassza ki az útválasztási táblázat nevét (**myroutetablepublic elemet**).

1. Válassza az **útvonalak**  >  **Hozzáadás**lehetőséget.

    ![Útvonal, útválasztási táblázat, Azure Portal hozzáadása](./media/tutorial-create-route-table-portal/add-route.png)

1. Az **útvonal hozzáadása**mezőben adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Útvonal neve | *ToPrivateSubnet* |
    | Címelőtag | *10.0.1.0/24* (a korábban létrehozott *privát* alhálózat címtartomány) |
    | A következő ugrás típusa | **Virtuális berendezés** |
    | A következő ugrás címe | *10.0.2.4* (a *DMZ* -alhálózat címtartomány tartományán belüli címe) |

1. Kattintson az **OK** gombra.

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

1. A virtuális hálózat kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális hálózatokat**.

1. Válassza ki a virtuális hálózat (**myVirtualNetwork**) nevét.

1. A virtuális hálózat menüsávján válassza az **alhálózatok**lehetőséget.

1. A virtuális hálózat alhálózati listájában válassza a **nyilvános**lehetőséget.

1. Az **útválasztási**táblázatban válassza ki a létrehozott útválasztási táblázatot (**myroutetablepublic elemet**), majd válassza a **Mentés** lehetőséget, hogy az útválasztási táblázatot a *nyilvános* alhálózathoz rendelje.

    ![Útválasztási táblázat, alhálózati lista, virtuális hálózat, Azure Portal hozzárendelése](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Az IP-továbbítás bekapcsolása

Ezután kapcsolja be az IP-továbbítást az új NVA virtuális géphez ( *myVmNva*). Ha az Azure hálózati forgalmat küld a *myVmNva*, ha a forgalmat egy másik IP-címhez szánják, akkor az IP-továbbítás a megfelelő helyre küldi a forgalmat.

1. A virtuális gép kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

1. Válassza ki a virtuális gép nevét (**myVmNva**).

1. A NVA virtuális gép menüsorában válassza a **hálózatkezelés**lehetőséget.

1. Válassza a **myvmnva123**lehetőséget. Ez az Azure-hoz létrehozott hálózati adapter a virtuális géphez. Az Azure számok hozzáadásával biztosítja az egyedi nevet.

    ![Hálózatkezelés, hálózati virtuális berendezés (NVA) virtuális gép (VM), Azure Portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. A hálózati adapter menüsávján válassza az **IP-konfigurációk**elemet.

1. Az **IP-konfigurációk** lapon állítsa be **az IP-továbbítás** **engedélyezésre**beállítást, majd kattintson a **Mentés**gombra.

    ![Engedélyezze az IP-továbbítást, az IP-konfigurációkat, a hálózati adaptert, a hálózati virtuális berendezést (NVA) virtuális gépet (VM), Azure Portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Nyilvános és privát virtuális gépek létrehozása

Hozzon létre egy nyilvános virtuális gépet és egy privát virtuális GÉPET a virtuális hálózaton. Később azt fogja látni, hogy az Azure a NVA keresztül átirányítja a *nyilvános* alhálózat forgalmát a *privát* alhálózatra.

A nyilvános virtuális gép és a privát virtuális gép létrehozásához kövesse a [NVA korábbi létrehozása](#create-an-nva) című témakör lépéseit. Nem kell megvárnia, amíg a telepítés befejeződik, vagy nyissa meg a virtuális gép erőforrását. Ugyanazokat a beállításokat fogja használni, kivéve az alább leírtakat.

Mielőtt a **Létrehozás** gombra kattint a nyilvános vagy a virtuális gép létrehozásához, lépjen a következő két alszakaszra ([nyilvános virtuális](#public-vm) gép és [privát virtuális](#private-vm)gép), amely a különböző értékeket jeleníti meg. Ha az Azure befejezte a virtuális gépek üzembe helyezését, folytathatja a következő szakaszt (a[forgalom átirányítása egy NVA](#route-traffic-through-an-nva)).

### <a name="public-vm"></a>Nyilvános virtuális gép

| Tab | Beállítás | Érték |
| --- | ------- | ----- |
| Alapvető beállítások | Erőforráscsoport | **myResourceGroup** |
| | Virtuális gép neve | *myVmPublic* |
| | Nyilvános bejövő portok | **Kijelölt portok engedélyezése** |
| | Bejövő portok kiválasztása | **RDP** |
| Hálózat | Virtuális hálózat | **myVirtualNetwork** |
| | Alhálózat | **Nyilvános (10.0.0.0/24)** |
| | Nyilvános IP-cím | Az alapértelmezett érték |
| Kezelés | Diagnosztikai Storage-fiók | **mynvastorageaccount** |

### <a name="private-vm"></a>Privát virtuális gép

| Tab | Beállítás | Érték |
| --- | ------- | ----- |
| Alapvető beállítások | Erőforráscsoport | **myResourceGroup** |
| | Virtuális gép neve | *myVmPrivate* |
| | Nyilvános bejövő portok | **Kijelölt portok engedélyezése** |
| | Bejövő portok kiválasztása | **RDP** |
| Hálózat | Virtuális hálózat | **myVirtualNetwork** |
| | Alhálózat | **Privát (10.0.1.0/24)** |
| | Nyilvános IP-cím | Az alapértelmezett érték |
| Kezelés | Diagnosztikai Storage-fiók | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Jelentkezzen be a myVmPrivate távoli asztalon keresztül

1. A saját virtuális gép kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

1. Válassza ki a saját virtuális gépe (**myVmPrivate**) nevét.

1. A virtuális gép menüsorában válassza a **Kapcsolódás** lehetőséget a távoli asztali kapcsolat létrehozásához a privát virtuális géphez.

1. Az **RDP-kapcsolat** lapon válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött *. rdp* fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Válassza a **további lehetőségek**lehetőséget  >  **egy másik fiók használata**lehetőségnél, majd adja meg a magánhálózati virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

1. Kattintson az **OK** gombra.

1. Ha a bejelentkezési folyamat során figyelmeztetést kap a tanúsítványról, válassza az **Igen** lehetőséget a virtuális géphez való csatlakozáshoz.

### <a name="enable-icmp-through-the-windows-firewall"></a>Az ICMP engedélyezése a Windows tűzfalon keresztül

Egy későbbi lépésben a nyomkövetési útvonal eszköz használatával tesztelheti az útválasztást. A nyomkövetési útvonal a Internet Control Message Protocolt (ICMP) használja, amelyet a Windows tűzfal alapértelmezés szerint megtagad. Engedélyezze az ICMP-t a Windows tűzfalon keresztül.

1. A *myVmPrivate*távoli asztal nyissa meg a PowerShellt.

1. Adja meg a következő parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ebben az oktatóanyagban nyomkövetési útvonalat használ az Útválasztás teszteléséhez. Éles környezetekben nem ajánlott az ICMP engedélyezése a Windows tűzfalon keresztül.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Az IP-továbbítás bekapcsolása a myVmNva belül

[Bekapcsolta az IP-továbbítást](#turn-on-ip-forwarding) a virtuális gép hálózati adapteréhez az Azure használatával. A virtuális gép operációs rendszerének a hálózati forgalmat is továbbítania kell. Kapcsolja be az IP-továbbítást a *myVmNva* virtuális gép operációs rendszere számára ezekkel a parancsokkal.

1. Nyisson meg egy távoli asztalt a *myVmPrivate* virtuális gépen egy parancssorból a *myVmNva* virtuális gépre:

    ```cmd
    mstsc /v:myvmnva
    ```

1. A *myVmNva* virtuális gépen a powershellből írja be ezt a parancsot az IP-továbbítás bekapcsolásához:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Indítsa újra a *myVmNva* virtuális gépet: a tálcán válassza a **Start**  >  **Power** **(tervezett)**  >  **Folytatás**lehetőséget.

    Ezzel leválasztja a távoli asztali munkamenetet is.

1. A *myVmNva* virtuális gép újraindítása után hozzon létre egy távoli asztali munkamenetet a *myVmPublic* virtuális gépre. Miközben továbbra is csatlakozik a *myVmPrivate* virtuális géphez, nyisson meg egy parancssort, és futtassa a következő parancsot:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. A *myVmPublic*távoli asztalán nyissa meg a PowerShellt.

1. Engedélyezze az ICMP-t a Windows tűzfalon a következő parancs beírásával:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>A hálózati forgalom útválasztásának tesztelése

Először is tesztelje a *myVmPublic* virtuális gépről a *myVmPrivate* virtuális gépre irányuló hálózati forgalom útválasztását.

1. A *myVmPublic* virtuális gépen a powershellből írja be a következő parancsot:

    ```powershell
    tracert myVmPrivate
    ```

    A válasz az alábbi példához hasonló:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Láthatja, hogy az első Ugrás a 10.0.2.4, amely NVA magánhálózati IP-címe. A második Ugrás a *myVmPrivate* virtuális gép magánhálózati IP-címére mutat: 10.0.1.4. Korábban felvette az útvonalat a *myroutetablepublic elemet* útválasztási táblázatba, és hozzárendelte a *nyilvános* alhálózathoz. Ennek eredményeképpen az Azure eljuttatta a forgalmat a NVA keresztül, és nem közvetlenül a *privát* alhálózatra.

1. Zárja be a *myVmPublic* virtuális gépre irányuló távoli asztali munkamenetet. Ez nem bontja a *myVmPrivate* virtuális géppel való kapcsolatot.

1. A *myVmPrivate* virtuális gépen a parancssorba írja be a következő parancsot:

    ```cmd
    tracert myVmPublic
    ```

    Ez a parancs a *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre irányuló hálózati forgalom útválasztását teszteli. A válasz az alábbi példához hasonló:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Láthatja, hogy az Azure közvetlenül a *myVmPrivate* virtuális gépről irányítja át a forgalmat a *myVmPublic* virtuális gépre. Alapértelmezés szerint az Azure közvetlenül irányítja a forgalmat az alhálózatok között.

1. Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforráscsoport már nem szükséges, törölje a *myResourceGroup* és az összes erőforrást:

1. Az erőforráscsoport kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **erőforráscsoportok**elemet.

1. Válassza ki az erőforráscsoport nevét (**myResourceGroup**).

1. Válassza az **Erőforráscsoport törlése** elemet.

1. A megerősítő párbeszédpanelen írja be *myResourceGroup* a myResourceGroup **nevet az erőforráscsoport neve**mezőbe, majd válassza a **Törlés**lehetőséget. Az Azure törli a *myResourceGroup* és az ehhez az erőforráscsoporthoz kötött összes erőforrást, beleértve az útválasztási táblákat, a Storage-fiókokat, a virtuális hálózatokat, a virtuális gépeket, a hálózati adaptereket és a nyilvános IP-címeket.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Mostantól különböző előre konfigurált NVA telepíthet az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)-ről, amely számos hasznos hálózati funkciót biztosít. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Habár számos Azure-erőforrást telepíthet egy virtuális hálózaton belül, az Azure nem tud erőforrásokat telepíteni bizonyos Péter-szolgáltatásokhoz egy virtuális hálózatban. Bizonyos Azure Pásti-szolgáltatások erőforrásaira korlátozható a hozzáférés, bár a korlátozásnak csak egy virtuális hálózati alhálózatról kell forgalmat használnia. Ha szeretné megtudni, hogyan korlátozhatja a hálózati hozzáférést az Azure Pásti-erőforrásokhoz, tekintse meg a következő oktatóanyagot.

> [!div class="nextstepaction"]
> [PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Az Azure-szolgáltatások pénzbe kerülnek. A Azure Cost Management segítségével megadhatja a költségvetéseket, és konfigurálhatja a riasztásokat, hogy megőrizze a költségek szabályozását. Cost Managementával elemezheti, kezelheti és optimalizálhatja Azure-költségeit. További információkért tekintse meg a [költségek elemzését](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ismertető rövid útmutatót.