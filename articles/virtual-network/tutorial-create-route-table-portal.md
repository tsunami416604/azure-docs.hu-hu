---
title: Hálózati forgalom irányítása – útmutató – Azure Portal | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan irányítható a hálózati forgalom egy útválasztási táblázattal az Azure Portalon.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: fbbc624bbc3d20a70a54c50296f5b74634002a67
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409071"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Oktatóanyag: Hálózati forgalom továbbítása egy útválasztási táblázathoz, az Azure portal használatával

Az Azure irányítja a forgalmat egy virtuális hálózatban, alapértelmezés szerint az összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Útválasztási táblázat létrehozása
> * Útvonal létrehozása
> * Több alhálózattal rendelkező virtuális hálózat létrehozása
> * Útválasztási táblázat társítása alhálózattal
> * Forgalmat irányító hálózati virtuális berendezés létrehozása
> * Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
> * Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ha szeretné, ez az oktatóanyag segítségével befejezheti a [Azure CLI-vel](tutorial-create-route-table-cli.md) vagy [Azure PowerShell-lel](tutorial-create-route-table-powershell.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

1. A képernyő bal felső oldalán válassza **erőforrás létrehozása** > **hálózatkezelés** > **útvonaltábla**.

1. A **útvonaltábla létrehozása**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *myRouteTablePublic*. |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza ki **új létrehozása**, adja meg *myResourceGroup*, és válassza ki *OK*. |
    | Hely | Hagyja meg az alapértelmezett **USA keleti Régiójában**.
    | BGP-útvonalpropagálás | Hagyja meg az alapértelmezett **engedélyezve**. |

1. Kattintson a **Létrehozás** gombra.

## <a name="create-a-route"></a>Útvonal létrehozása

1. Adja meg a portál keresősávjában *myRouteTablePublic*.

1. Amikor megjelenik a keresési eredmények között, válassza ki a **myRouteTablePublic** elemet.

1. A **myRouteTablePublic** alatt **beállítások**válassza **útvonalak** > **+ Hozzáadás**.

    ![Útvonal hozzáadása](./media/tutorial-create-route-table-portal/add-route.png)

1. A **útvonal hozzáadása**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Útvonal neve | Adja meg *ToPrivateSubnet*. |
    | Címelőtag | Adja meg *10.0.1.0/24*. |
    | A következő ugrás típusa | Válassza a **Virtuális berendezés** lehetőséget. |
    | A következő ugrás címe | Adja meg *10.0.2.4 cím*. |

1. Kattintson az **OK** gombra.

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Mielőtt hozzárendelhetne egy útválasztási táblázatot egy alhálózathoz, akkor hozzon létre egy virtuális hálózatot és alhálózatot.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A képernyő bal felső oldalán válassza **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.

1. A **virtuális hálózat létrehozása**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *myVirtualNetwork*. |
    | Címtér | Adja meg *10.0.0.0/16*. |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza ki ***válasszon meglévő*** > **myResourceGroup**. |
    | Hely | Hagyja meg az alapértelmezett **USA keleti Régiójában**. |
    | Alhálózat - név | Adja meg *nyilvános*. |
    | Alhálózat – címtartomány | Adja meg *10.0.0.0/24*. |

1. Hagyja meg az alapértelmezett beállításokat, és válassza ki a többi **létrehozás**.

### <a name="add-subnets-to-the-virtual-network"></a>Adjon hozzá alhálózatokat a virtuális hálózathoz

1. Adja meg a portál keresősávjában *myVirtualNetwork*.

1. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.

1. A **myVirtualNetwork**alatt **beállítások**válassza **alhálózatok** > **+ alhálózat**.

    ![Alhálózat hozzáadása](./media/tutorial-create-route-table-portal/add-subnet.png)

1. A **alhálózat hozzáadása**, adja meg ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *privát*. |
    | Címtér | Adja meg *10.0.1.0/24*. |

1. Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

1. Válassza ki **+ alhálózat** újra. Ebben az esetben adja meg ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *DMZ*. |
    | Címtér | Adja meg *10.0.2.0/24*. |

1. Az utolsó időpont, például, válassza ki és hagyja meg az alapértelmezett értékeket a többi **OK**.

    Azure látható a három alhálózatot: **Nyilvános**, **privát**, és **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Társítsa a nyilvános alhálózathoz myRouteTablePublic

1. Válassza ki **nyilvános**.

1. A **nyilvános**válassza **útvonaltábla** > **MyRouteTablePublic** > **mentése**.

    ![Útválasztási táblázat hozzárendelése](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>NVA létrehozása

Az nva-k, virtuális gépek, amelyek segítenek az útválasztó és tűzfal optimalizálási például hálózati funkciók. Kiválaszthatja egy másik operációs rendszer, ha azt szeretné. Ez az oktatóanyag feltételezi, hogy használ **Windows Server 2016 Datacenter**.

1. A képernyő bal felső oldalán válassza **erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter**.

1. A **hozzon létre egy virtuális gép – alapvető**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza ki **myResourceGroup**. |
    | **PÉLDÁNY ADATAI** |  |
    | Virtuális gép neve | Adja meg *myVmNva*. |
    | Régió | Válassza az **USA keleti régiója** lehetőséget. |
    | Rendelkezésre állási beállításai | Hagyja meg az alapértelmezett **szükséges infrastruktúra redundancia**. |
    | Kép | Hagyja meg az alapértelmezett **Windows Server 2016 Datacenter**. |
    | Méret | Hagyja meg az alapértelmezett **Standard DS1 v2**. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adjon meg egy tetszőleges felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Írja be újból a jelszót. |
    | **BEJÖVŐPORT-SZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **None**.
    | **PÉNZT TAKARÍTHAT MEG** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**. |

1. Válassza ki **tovább: Lemezek**.

1. A **hozzon létre egy virtuálisgép - lemezek**, válassza ki az igényeinek megfelelő beállításokat.

1. Válassza ki **tovább: Hálózatkezelés**.

1. A **hozzon létre egy virtuálisgép - hálózat**, jelölje be ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **myVirtualNetwork**. |
    | Alhálózat | Válassza ki **DMZ-t (10.0.2.0/24)**. |
    | Nyilvános IP-cím | Válassza ki **None**. Nyilvános IP-cím nem szükséges. A virtuális gép nem fog csatlakozni az interneten keresztül.|

1. Hagyja meg az alapértelmezett beállításokat, és válassza ki a többi **tovább: Felügyeleti**.

1. A **hozzon létre egy virtuálisgép - felügyeleti**, a **diagnosztikai tárfiók**válassza **hozzon létre új**.

1. A **storage-fiók létrehozása**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *mynvastorageaccount*. |
    | Fióktípus | Hagyja meg az alapértelmezett **Storage (általános célú v1)**. |
    | Teljesítmény | Hagyja meg az alapértelmezett **Standard**. |
    | Replikáció | Hagyja meg az alapértelmezett **helyileg redundáns tárolás (LRS)**.

1. Kattintson az **OK** gombra.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Ekkor átkerül a **felülvizsgálat + létrehozása** oldal és az Azure ellenőrzi a konfigurációt.

1. Ha azt láthatja, hogy **ellenőrzésen**válassza **létrehozás**.

    A virtuális gép üzembe helyezése néhány percet vesz igénybe. Nem folytathatja a munkát a virtuális gép létrehozása Azure befejezéséig. A **folyamatban van a központi telepítés** lapján megtekintheti, üzembe helyezés részleteit.

1. Ha a virtuális gép készen áll, válassza ki a **erőforrás megnyitása**.

## <a name="turn-on-ip-forwarding"></a>Kapcsolja be a IP-továbbítás

Kapcsolja be az IP-továbbítást a *myVmNva*. Ha az Azure küld hálózati forgalmat a *myVmNva*, ha a forgalom egy másik IP-cím szánt, IP-továbbítást a forgalom küld a megfelelő helyre.

1. A **myVmNva**alatt **beállítások**válassza **hálózatkezelés**.

1. Válassza ki **myvmnva123**. Ez az Azure a virtuális géphez létrehozott hálózati adapter. Egy karakterlánc, szám egyedi legyen, fog rendelkezni.

    ![Virtuális gépek hálózatkezelése](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. A **beállítások**válassza **IP-konfigurációk**.

1. A **myvmnva123 - IP-konfigurációk**, a **IP-továbbítás**, jelölje be **engedélyezve** majd **mentése**.

    ![IP-továbbítás engedélyezése](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Nyilvános és privát virtuális gépek létrehozása

Hozzon létre egy nyilvános virtuális gép és a egy privát virtuális Gépet a virtuális hálózatban. Később fogja használni őket, hogy az Azure irányítja lásd a *nyilvános* alhálózat forgalmát a *privát* alhálózat az nva-n keresztül.

1 – 12., lépéseit végrehajtania [NVA létrehozása](#create-an-nva). A legtöbb ugyanazokat a beállításokat használja. Ezek az értékek azok, amelyekre különböző kell:

| Beállítás | Érték |
| ------- | ----- |
| **NYILVÁNOS VIRTUÁLIS GÉP** | |
| ALAPVETŐ TUDNIVALÓK |  |
| Virtuális gép neve | Adja meg *myVmPublic*. |
| HÁLÓZATKEZELÉS | |
| Alhálózat | Válassza ki **nyilvános (10.0.0.0/24)**. |
| Nyilvános IP-cím | Az alapértelmezett érték elfogadásához. |
| Nyilvános bejövő portok | Válassza ki **lehetővé teszi a kiválasztott portok**. |
| Bejövő portok kiválasztása | Válassza ki **HTTP** és **RDP**. |
| FELÜGYELETI | |
| Storage-fiók diagnosztikája | Hagyja meg az alapértelmezett **mynvastorageaccount**. |
| **PRIVÁT VIRTUÁLIS GÉP** | |
| ALAPVETŐ TUDNIVALÓK |  |
| Virtuális gép neve | Adja meg *myVmPrivate*. |
| HÁLÓZATKEZELÉS | |
| Alhálózat | Válassza ki **privát (10.0.1.0/24)**. |
| Nyilvános IP-cím | Az alapértelmezett érték elfogadásához. |
| Nyilvános bejövő portok | Válassza ki **lehetővé teszi a kiválasztott portok**. |
| Bejövő portok kiválasztása | Válassza ki **HTTP** és **RDP**. |
| FELÜGYELETI | |
| Storage-fiók diagnosztikája | Hagyja meg az alapértelmezett **mynvastorageaccount**. |

Létrehozhatja a *myVmPrivate* virtuális gépet, miközben az Azure létrehozza a *myVmPublic* virtuális gépet. Ne folytassa a további lépésekkel mindkét virtuális gép létrehozása Azure befejezéséig.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Jelentkezzen be a myVmPrivate távoli asztali kapcsolaton keresztül

1. Adja meg a portál keresősávjában *myVmPrivate*.

1. Amikor a **myVmPrivate** virtuális gép megjelenik a keresési eredmények között, válassza ki.

1. Válassza ki **Connect** hozhat létre egy távoli asztali kapcsolatot a *myVmPrivate* virtuális Gépet.

1. A **csatlakozhat a virtuális gép**válassza **RDP-fájl letöltése**. Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépet.

1. Nyissa meg a letöltött *.rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a felhasználónevet és a privát virtuális gép létrehozásakor megadott jelszót.

    1. Előfordulhat, hogy ki kell választania **további lehetőségek** > **másik fiók használata**ahhoz, hogy a saját virtuális gép hitelesítő adatainak.

1. Kattintson az **OK** gombra.

    A bejelentkezés során egy tanúsítványfigyelmeztetés folyamat jelenhet meg.

1. Válassza ki **Igen** a virtuális Géphez való csatlakozáshoz.

### <a name="enable-icpm-through-the-windows-firewall"></a>A Windows tűzfalon keresztül ICPM engedélyezése

Egy későbbi lépésben az útvonalkövető eszközzel használni útválasztásának teszteléséhez. Használja az Internet Control üzenet Protocol (ICMP), amely a Windows tűzfal alapértelmezés szerint letiltja. Az ICMP engedélyezése a Windows tűzfalon keresztül.

1. A távoli asztali, *myVmPrivate*, nyissa meg a Powershellt.

1. Adja meg ezt a parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Ebben az oktatóanyagban útválasztásának teszteléséhez útvonalkövetést használ. Éles környezetekben nem ajánlott ICMP engedélyezése a Windows tűzfalon.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>IP-továbbítást myVmNva bekapcsolása

Ön [IP-továbbítás engedélyezve](#turn-on-ip-forwarding) az Azure használatával a virtuális gép hálózati adapteréhez. A virtuális gép operációs rendszer forgalmat is tartalmaz. Kapcsolja be az IP-továbbítást a *myVmNva* virtuális gép operációs rendszerének a következő parancsokkal.

1. A parancsot a parancssorba a *myVmPrivate* virtuális Géphez, nyissa meg a távoli asztali a *myVmNva* virtuális Géphez:

    ```cmd
    mstsc /v:myvmnva
    ```

1. A PowerShell használatával a *myVmNva*, adja meg az IP-továbbítás kapcsolja be ezt a parancsot:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Indítsa újra a *myVmNva* virtuális Gépet. A tálcán válassza **Start gomb** > **főkapcsoló**, **más (tervezett)** > **Folytatás**.

    Amely szintén bontja a kapcsolatot a távoli asztali munkamenetet.

1. Miután a *myVmNva* virtuális gép újraindul, hozzon létre egy távoli asztali munkamenetet a *myVmPublic* virtuális Gépet. Miközben továbbra is csatlakozik a *myVmPrivate* VM, nyisson meg egy parancssort, és futtassa a következő parancsot:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. A távoli asztali, *myVmPublic*, nyissa meg a Powershellt.

1. Ez a parancs megadásával engedélyezze az ICMP Protokollt a Windows tűzfalon keresztül:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>A hálózati forgalom útválasztásának teszteléséhez

Először is hozzunk származó hálózati forgalom útválasztásának teszteléséhez a *myVmPublic* a virtuális gép a *myVmPrivate* virtuális gép.

1. A PowerShell használatával a *myVmPublic* virtuális Gépet, adja meg ezt a parancsot:

    ```powershell
    tracert myVmPrivate
    ```

    A válasz a következő példához hasonlóak:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Láthatja, hogy az első Ugrás a 10.0.2.4 cím. NVA magánhálózati IP-cím. A második Ugrás a magánhálózati IP-címet, a *myVmPrivate* virtuális Géphez: 10.0.1.4 cím – ez. Korábban, az útvonal hozzáadta a *myRouteTablePublic* útválasztási táblázathoz és a kapcsolódó, hogy a *nyilvános* alhálózat. Ennek eredményeképpen az Azure küldött-e a forgalmat az nva-n keresztül, és nem közvetlenül a a *privát* alhálózat.

1. Zárja be a *myVmPublic* virtuális gépre irányuló távoli asztali munkamenetet. Ez nem bontja a *myVmPrivate* virtuális géppel való kapcsolatot.

1. A parancsot a parancssorba a *myVmPrivate* virtuális Gépet, adja meg ezt a parancsot:

    ```cmd
    tracert myVmPublic
    ```

    Teszteli, a hálózati forgalom útválasztását az *myVmPrivate* a virtuális gép a *myVmPublic* virtuális Gépet. A válasz a következő példához hasonlóak:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Láthatja, hogy közvetlenül az Azure irányítja a forgalmat a *myVmPrivate* a virtuális gép a *myVmPublic* virtuális Gépet. Alapértelmezés szerint az Azure közvetlenül irányítja a forgalmat az alhálózatok között.

1. Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és az összes erőforrás rendelkezik:

1. Adja meg a portál keresősávjában *myResourceGroup*.

1. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Most, hogy tudja, hogyan valósítható meg, különböző előre konfigurált nva-t, telepítheti a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Az általuk végzett találhat hasznos számos hálózati funkciók. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Számos Azure-erőforrások virtuális hálózaton belüli telepíteni, amíg az Azure egyes PaaS-szolgáltatások erőforrásai nem telepíthet egy virtuális hálózatban. Az egyes Azure PaaS-szolgáltatások az erőforrásokhoz való hozzáférés korlátozása, lehetőség. Azonban a korlátozás csak kell lennie egy virtuális hálózati alhálózatról származó forgalmat. Ahhoz, hogy megtudja, hogyan korlátozható az Azure PaaS-erőforrásokhoz való hozzáférés, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](tutorial-restrict-network-access-to-resources.md)
