---
title: Hálózati forgalom irányítása – oktatóanyag – Azure Portal
titlesuffix: Azure Virtual Network
description: Ez az oktatóanyag azt ismerteti, hogyan irányítható a hálózati forgalom egy útválasztási táblázattal az Azure Portalon.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 7e7a01b7fdc1a508fa19397900f8fd4f52d49c53
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164002"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Oktatóanyag: Hálózati forgalom útválasztási táblázattal való irányítása az Azure Portalon

Az Azure alapértelmezés szerint átirányítja a forgalmat a virtuális hálózaton belüli összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Útválasztási táblázat létrehozása
> * Útvonal létrehozása
> * Több alhálózattal rendelkező virtuális hálózat létrehozása
> * Útválasztási táblázat társítása alhálózattal
> * Forgalmat irányító hálózati virtuális berendezés létrehozása
> * Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
> * Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ha szeretné, az oktatóanyag az [Azure CLI](tutorial-create-route-table-cli.md) vagy a [Azure PowerShell](tutorial-create-route-table-powershell.md)használatával is befejeződik.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **útválasztási táblázat**lehetőséget.

1. Az **útvonal létrehozása táblában**adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *myroutetablepublic elemet*. |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson *az OK gombra*. |
    | Földrajzi egység | Hagyja meg az alapértelmezett **USA keleti**régióját.
    | BGP-útvonal propagálása | Hagyja meg az alapértelmezett **beállítást**. |

1. Kattintson a **Létrehozás** gombra.

## <a name="create-a-route"></a>Útvonal létrehozása

1. A portál keresési sávján adja meg a *myroutetablepublic elemet*.

1. Amikor megjelenik a keresési eredmények között, válassza ki a **myRouteTablePublic** elemet.

1. A **myroutetablepublic elemet** területen **válassza az** **útvonalak** >  **+ Hozzáadás**lehetőséget.

    ![Útvonal hozzáadása](./media/tutorial-create-route-table-portal/add-route.png)

1. Az **útvonal hozzáadása**mezőben adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Útvonal neve | Adja meg a *ToPrivateSubnet*. |
    | Címelőtag | Adja meg a *10.0.1.0/24*értéket. |
    | A következő ugrás típusa | Válassza a **Virtuális berendezés** lehetőséget. |
    | A következő ugrás címe | Adja meg a *10.0.2.4*. |

1. Kattintson az **OK** gombra.

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Ahhoz, hogy egy útválasztási táblázatot alhálózathoz lehessen rendelni, létre kell hoznia egy virtuális hálózatot és alhálózatot.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget.

1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *myVirtualNetwork*. |
    | Címtér | Adja meg a *10.0.0.0/16*értéket. |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a meglévő > **MyResourceGroup** ***kiválasztása*** lehetőséget. |
    | Földrajzi egység | Hagyja meg az alapértelmezett **USA keleti**régióját. |
    | Alhálózat – név | Adja meg a *nyilvános*értéket. |
    | Alhálózat – címtartomány | Adja meg a *10.0.0.0/24*értéket. |

1. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Létrehozás**lehetőséget.

### <a name="add-subnets-to-the-virtual-network"></a>Alhálózatok hozzáadása a virtuális hálózathoz

1. A portál keresési sávján adja meg a *myVirtualNetwork*.

1. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.

1. A **myVirtualNetwork**területen, a **Beállítások**területen válassza az **alhálózatok** >  **+ alhálózat**elemet.

    ![Alhálózat hozzáadása](./media/tutorial-create-route-table-portal/add-subnet.png)

1. Az **alhálózat hozzáadása**területen adja meg a következő adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *magánjellegű*értéket. |
    | Címtér | Adja meg a *10.0.1.0/24*értéket. |

1. Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

1. Kattintson ismét az **+ alhálózat** elemre. Ezúttal adja meg a következő adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *DMZ*-t. |
    | Címtér | Adja meg a *10.0.2.0/24*értéket. |

1. Az utolsó alkalomhoz hasonlóan hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.

    Az Azure a három alhálózatot mutatja be: **nyilvános**, **privát**és **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Myroutetablepublic elemet hozzárendelése a nyilvános alhálózathoz

1. Válassza a **nyilvános**lehetőséget.

1. A **nyilvános**területen válassza az **útválasztási táblázat** > **myroutetablepublic elemet** > **Mentés**lehetőséget.

    ![Útválasztási táblázat hozzárendelése](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>NVA létrehozása

A NVA olyan virtuális gépek, amelyek a hálózati funkciók, például az Útválasztás és a tűzfal optimalizálásához nyújtanak segítséget. Ha kívánja, választhat másik operációs rendszert is. Ez az oktatóanyag feltételezi, hogy a **Windows Server 2016 Datacenter**rendszert használja.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **számítás** > **Windows Server 2016 Datacenter**lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVmNva*. |
    | Region (Régió) | Válassza az **USA keleti régiója** lehetőséget. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Lemezkép | Hagyja meg az alapértelmezett **Windows Server 2016 Datacenter adatközpontot**. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adjon meg egy tetszőleges felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket.
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **virtuális gép létrehozása – lemezek**területen válassza ki az igényeinek megfelelő beállításokat.

1. Válassza a **Tovább: hálózatkezelés**lehetőséget.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **myVirtualNetwork**. |
    | Alhálózat | Válassza a **DMZ (10.0.2.0/24)** lehetőséget. |
    | Nyilvános IP-cím | Válassza a **nincs**lehetőséget. Nincs szüksége nyilvános IP-címekre. A virtuális gép nem kapcsolódik az internethez.|

1. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Tovább: kezelés**lehetőséget.

1. A **virtuálisgép-kezelés létrehozása** **diagnosztikai Storage-fiókhoz**területen válassza az **új létrehozása**lehetőséget.

1. A **Storage-fiók létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *mynvastorageaccount*. |
    | Fióktípus | Hagyja meg az alapértelmezett **tárolót (általános célú v1)** . |
    | Teljesítmény | Hagyja meg az alapértelmezett **standard**értéket. |
    | Replikáció | Hagyja meg az alapértelmezett **helyileg redundáns tárolást (LRS)** .

1. Kattintson az **OK** gombra.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Ekkor megjelenik a **felülvizsgálat + létrehozás** oldal, és az Azure ellenőrzi a konfigurációt.

1. Ha látja, hogy az **Érvényesítés átadott**, válassza a **Létrehozás**lehetőséget.

    A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne maradjon tovább, amíg az Azure befejezi a virtuális gép létrehozását. Az üzemelő **példány folyamatban van** oldal megjeleníti az üzembe helyezés részleteit.

1. Ha a virtuális gép elkészült, válassza **az Ugrás az erőforráshoz**lehetőséget.

## <a name="turn-on-ip-forwarding"></a>Az IP-továbbítás bekapcsolása

Kapcsolja be az IP-továbbítást a *myVmNva*. Ha az Azure hálózati forgalmat küld a *myVmNva*, ha a forgalmat egy másik IP-címhez szánják, akkor az IP-továbbítás a megfelelő helyre küldi a forgalmat.

1. A **myVmNva**területen a **Beállítások**alatt válassza a **hálózatkezelés**lehetőséget.

1. Válassza a **myvmnva123**lehetőséget. Ez az Azure-hoz létrehozott hálózati adapter a virtuális géphez. Egy számsorozattal fog rendelkezni, hogy egyedi legyen.

    ![Virtuális gépek hálózatkezelése](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. A **Beállítások**területen válassza az **IP-konfigurációk**elemet.

1. A **myvmnva123-IP-konfigurációk**esetében az **IP-továbbítás**beállításnál válassza az **engedélyezve** lehetőséget, majd kattintson a **Mentés**gombra.

    ![IP-továbbítás engedélyezése](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Nyilvános és magánhálózati virtuális gépek létrehozása

Hozzon létre egy nyilvános virtuális gépet és egy privát virtuális GÉPET a virtuális hálózaton. Később azt fogja látni, hogy az Azure a NVA keresztül átirányítja a *nyilvános* alhálózat forgalmát a *privát* alhálózatra.

Hajtsa végre az 1-12-es lépéseket a [NVA létrehozásához](#create-an-nva). Használja a legtöbb azonos beállítást. Ezek az értékek különbözőek:

| Beállítás | Value (Díj) |
| ------- | ----- |
| **NYILVÁNOS VIRTUÁLIS GÉP** | |
| ALAPJAI |  |
| Virtuális gép neve | Adja meg a *myVmPublic*. |
| HÁLÓZATI | |
| Alhálózat | Válassza a **nyilvános (10.0.0.0/24)** lehetőséget. |
| Nyilvános IP-cím | Fogadja el az alapértelmezett értéket. |
| Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
| Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget. |
| FELÜGYELETI | |
| Diagnosztikai Storage-fiók | Hagyja meg az alapértelmezett **mynvastorageaccount**. |
| **PRIVÁT VIRTUÁLIS GÉP** | |
| ALAPJAI |  |
| Virtuális gép neve | Adja meg a *myVmPrivate*. |
| HÁLÓZATI | |
| Alhálózat | Válassza a **magánjellegű (10.0.1.0/24)** lehetőséget. |
| Nyilvános IP-cím | Fogadja el az alapértelmezett értéket. |
| Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
| Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget. |
| FELÜGYELETI | |
| Diagnosztikai Storage-fiók | Hagyja meg az alapértelmezett **mynvastorageaccount**. |

Létrehozhatja a *myVmPrivate* virtuális gépet, miközben az Azure létrehozza a *myVmPublic* virtuális gépet. Ne folytassa a további lépéseket, amíg az Azure befejezi mindkét virtuális gép létrehozását.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Jelentkezzen be a myVmPrivate távoli asztalon keresztül

1. A portál keresési sávján adja meg a *myVmPrivate*.

1. Amikor a **myVmPrivate** virtuális gép megjelenik a keresési eredmények között, válassza ki.

1. A **Kapcsolódás** lehetőségre kattintva hozzon létre egy távoli asztali kapcsolatot a *myVmPrivate* virtuális géppel.

1. A **Kapcsolódás virtuális géphez**lapon válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll ( *. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött *. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a magánhálózati virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

    1. Előfordulhat, hogy a privát virtuális gép hitelesítő adatainak használatához **több választási lehetőséget** kell választania > **használjon másik fiókot**.

1. Kattintson az **OK** gombra.

    A bejelentkezési folyamat során a tanúsítványra vonatkozó figyelmeztetés jelenhet meg.

1. Válassza az **Igen** lehetőséget a virtuális géphez való kapcsolódáshoz.

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

1. Az IP-továbbítás bekapcsolásához a *myVmNva*található powershellből írja be ezt a parancsot:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Indítsa újra a *myVmNva* virtuális gépet. A tálcán válassza a **Start gomb** > **bekapcsoló gomb**, **egyéb (tervezett)**  > **Folytatás**lehetőséget.

    Ezzel leválasztja a távoli asztali munkamenetet is.

1. A *myVmNva* virtuális gép újraindítása után hozzon létre egy távoli asztali munkamenetet a *myVmPublic* virtuális gépre. Miközben továbbra is csatlakozik a *myVmPrivate* virtuális géphez, nyisson meg egy parancssort, és futtassa a következő parancsot:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. A *myVmPublic*távoli asztal nyissa meg a PowerShellt.

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

    Láthatja, hogy az első Ugrás a 10.0.2.4. NVA's magánhálózati IP-cím. A második Ugrás a *myVmPrivate* virtuális gép magánhálózati IP-címére mutat: 10.0.1.4. Korábban felvette az útvonalat a *myroutetablepublic elemet* útválasztási táblázatba, és hozzárendelte a *nyilvános* alhálózathoz. Ennek eredményeképpen az Azure eljuttatta a forgalmat a NVA keresztül, és nem közvetlenül a *privát* alhálózatra.

1. Zárja be a *myVmPublic* virtuális gépre irányuló távoli asztali munkamenetet. Ez nem bontja a *myVmPrivate* virtuális géppel való kapcsolatot.

1. A *myVmPrivate* virtuális gépen a parancssorba írja be a következő parancsot:

    ```cmd
    tracert myVmPublic
    ```

    Ellenőrzi a *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre irányuló hálózati forgalom útválasztását. A válasz az alábbi példához hasonló:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Az Azure-útvonalak forgalmát közvetlenül a *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre tekintheti meg. Alapértelmezés szerint az Azure közvetlenül irányítja a forgalmat az alhálózatok között.

1. Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. A portál keresési sávján adja meg a *myResourceGroup*.

1. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Most, hogy már tudja, hogyan teheti meg, különböző előre konfigurált NVA telepíthet az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Számos hálózati funkciót látnak el, amelyeket hasznosnak talál. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Habár számos Azure-erőforrást telepíthet egy virtuális hálózaton belül, az Azure nem tud erőforrásokat telepíteni bizonyos Péter-szolgáltatásokhoz egy virtuális hálózatban. Lehetséges az Azure Pásti-szolgáltatások erőforrásaihoz való hozzáférés korlátozása. A korlátozásnak azonban csak egy virtuális hálózati alhálózatról érkező forgalomnak kell lennie. Ahhoz, hogy megtudja, hogyan korlátozható az Azure PaaS-erőforrásokhoz való hozzáférés, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](tutorial-restrict-network-access-to-resources.md)
