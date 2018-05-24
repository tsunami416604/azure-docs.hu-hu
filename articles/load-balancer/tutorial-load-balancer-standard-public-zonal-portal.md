---
title: 'Oktatóanyag: Terheléselosztó virtuális gépek egy zónában – Azure Portal | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hozzon létre egy standard terheléselosztót zónákra osztott előtérrel a rendelkezésre állási zónában lévő virtuális gépek Azure Portal használatával történő terheléselosztásához
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 9067ea350997ed0c4fc5c65dccb72f403adfa774
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorialload-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Oktatóanyag: Rendelkezésre állási zónán belüli virtuális gépek terheléselosztása standard terheléselosztóval az Azure Portal használatával

Ez az oktatóanyag végigvezeti a nyilvános [standard terheléselosztó](https://aka.ms/azureloadbalancerstandard) nyilvános standard IP-címet használó zónákra osztott előtérrel történő létrehozásának lépésein az Azure Portalon. Ennek a forgatókönyvnek a követésével meg fog adni egy meghatározott zónát az előtér- és háttérpéldányokhoz, hogy hozzáigazíthassa az adatkapcsolatot és erőforrásokat a meghatározott zónához. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Azure Standard Load Balancer létrehozása zónákra osztott előtérrel
> * Hálózati biztonsági csoportok létrehozása bejövő forgalomra vonatkozó szabályok meghatározásához
> * Zónaszintű virtuális gépek létrehozása és terheléselosztóhoz csatolása
> * Terheléselosztó állapotmintájának létrehozása
> * Terheléselosztó forgalmára vonatkozó szabályok létrehozása
> * Alapszintű IIS-webhely létrehozása
> * Terheléselosztó megtekintése működés közben

A rendelkezésre állási zónák standard rendelkezésre állási zónákkal való használatáról bővebben a [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md) című cikkben tájékozódhat.

Igény szerint az oktatóanyag az [Azure CLI](load-balancer-standard-public-zonal-cli.md) használatával is elvégezhető.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) címen.

## <a name="create-a-public-standard-load-balancer"></a>Nyilvános Standard Load Balancer létrehozása

A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Ha a terheléselosztó létrehozása során létrehoz egy új nyilvános IP-címet, akkor az automatikusan standard termékváltozatként lesz konfigurálva, és automatikusan zónaredundánssá is válik.

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Terheléselosztó** elemre.
2. A **Terheléselosztó létrehozása** lapon adja meg az alábbi értékeket a terheléselosztó számára:
    - A terheléselosztó neve: *myLoadBalancer*.
    - A terheléselosztó típusa: **Nyilvános**.
     - A létrehozandó új nyilvános IP-cím: *myPublicIPZonal*. Ehhez kattintson a **Nyilvános IP-cím kiválasztása**, majd az **Új létrehozása** lehetőségere. A névtípus megadásához alapértelmezetten a *myPublicIP* termékváltozat a szokásos, a **Rendelkezésre állási zóna** kiválasztásához pedig az **1. zóna**.
    - A létrehozott új erőforráscsoport neve: *myResourceGroupZLB*.
    - A hely: **westeurope**.
3. A terheléselosztó létrehozásához kattintson a **Létrehozás** elemre.
   
    ![Zónaszintű Standard Load Balancer létrehozása az Azure Portalon](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Eben a szakaszban egy virtuális hálózatot fog létrehozni, valamint két virtuális gépet ugyanabban a zónában (vagyis az 1. zónában) a terheléselosztó háttérkészletéhez hozzáadandó régió számára, majd telepíteni fogja az IIS-t a virtuális gépeken a zónaredundáns terheléselosztó tesztelése érdekében. Ezért, ha az egyik virtuális gép meghibásodik, az ugyanabba a zónába tartozó virtuális gép állapotmintája sikertelen lesz, és a forgalmat továbbra is szolgáltatni fogja az azonos zónába tartozó másik virtuális gép.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** elemre, és adja meg az alábbi értékeket:
    - A virtuális hálózat neve: *myVnet*.
    - A létező erőforráscsoport neve: *myResourceGroupZLB*
    - Az alhálózat neve: *myBackendSubnet*.
2. A virtuális hálózat létrehozásához kattintson a **Létrehozás** gombra.

    ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. A képernyő bal felső oldalán kattintson az **Erőforrás létrehozása** elemre, írja be a keresőmezőbe, hogy *Hálózati biztonsági csoport*, majd kattintson a hálózati biztonsági csoport lapján a **Létrehozás** elemre.
2. A Hálózati biztonsági csoport létrehozás lapon adja meg ezeket az értékeket:
    - A hálózati biztonsági csoport neve: *myNetworkSecurityGroup*.
    - A létező erőforráscsoport neve: *myResourceGroupBAZ*.
   
    ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>NSG-szabályok létrehozása

Ebben a szakaszban NSG-szabályokat fog létrehozni az Azure Portalon a HTTP-t és RDP-t használó bejövő kapcsolatok engedélyezéséhez.

1. Az Azure Portalon kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd keresse meg a **myResourceGroupZLB** erőforráscsoportban található **myNetworkSecurityGroup** elemet, és kattintson rá.
2. A **Beállítások** területen kattintson a **Bejövő biztonsági szabályok** lehetőségre, majd a **Hozzáadás** gombra.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - **Név**: *myHTTPRule*
    - **Leírás**: *HTTP engedélyezése*
4. Kattintson az **OK** gombra.
 
 ![Virtuális hálózat létrehozása](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Ismételje meg a 2–4. lépéseket egy másik, *myRDPRule* nevű szabály létrehozásához, amely engedélyezi a 3389-es portot használó, bejövő RDP-kapcsolatokat. Ehhez adja meg az alábbi értékeket:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *3389*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *200*
    - Név: *myRDPRule*
    - Leírás: *RDP engedélyezése*

    ![RDP-szabály létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Számítás** > **Windows Server 2016 Datacenter** elemre, és adja meg az alábbi értékeket a virtuális gép számára:
    - A virtuális gép neve: *myVM1*.        
    - A rendszergazda felhasználóneve: *azureuser*.    
    - *myResourceGroupZLB*: Az **Erőforráscsoport** esetében válassza a **Meglévő használata** lehetőséget, majd válassza ki a *myResourceGroupZLB* erőforráscsoportot.
2. Kattintson az **OK** gombra.
3. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
4. Adja meg az alábbi értékeket a virtuális gép beállításainál:
    - A rendelkezésre állási zóna, ahol elhelyezi a virtuális gépet: *1. zóna*.
    -  Győződjön meg róla, hogy a következő virtuális hálózat van kiválasztva: *myVNet*.
    - A létrehozandó standard nyilvános IP-cím: *myVM1PIP*. Kattintson az *Új létrehozása* elemre, majd írja be a *myVM1PIP* nevet, és válassza ki az **1** zónát. Az IP-cím termékváltozata alapértelmezés szerint a Standard.
    - Győződjön meg róla, hogy a következő alhálózat van kiválasztva: *myBackendSubnet*.
    - A már létező hálózati biztonsági csoport (tűzfal) neve: *myNetworkSecurityGroup*.
5. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
6. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.
7. Az 1–6. lépés követésével hozzon létre egy második virtuális gépet *myVM2* néven az 1. zónában *myVnet* virtuális hálózattal, *myVM2PIP* standard nyilvános IP-címmel, *myBackendSubnet* alhálózattal és **myNetworkSecurityGroup* hálózati biztonsági csoporttal.

    ![RDP-szabály létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Az IIS telepítése virtuális gépekre

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd az erőforrások listájában kattintson a *myResourceGroupZLB* erőforráscsoportban található **myVM1** elemre.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. Jelentkezzen be a virtuális gépen a virtuális gép létrehozásakor létrehozott felhasználónévvel és jelszóval (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.
4. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Windows PowerShell** elemre.
6. A PowerShell-ablakban futtassa a következő parancsokat az IIS-kiszolgáló telepítéséhez, távolítsa el a default.htm fájlt vegyen fel egy új default.htm fájt, amely megjeleníti a virtuális gép nevét:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
   ```
8. Zárja be a *myVM1* géphez létrehozott RDP-munkamenetet
9. Ismételje meg az 1–8. lépéseket az IIS *myVM2* gépen való telepítéséhez.

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, illetve terheléselosztási és NAT-szabályokat fog megadni.


### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét. Hozza létre a háttércímkészletet *myBackendPool* néven, amely magában foglalja a *VM1* és *VM2* virtuális gépeket.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer* lehetőségre az erőforráslistában.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a **Hozzáadás** gombra.
3. A **Háttérkészlet hozzáadása** oldalon tegye a következőket:
    - Adja a következő nevet a háttérkészletnek: *myBackEndPool*.
    - Válassza a **Virtuális hálózat** legördülő menüben a *myVNet* lehetőséget
    - A **Virtuális gép** és az **IP-cím** beállításához vegye fel a *myVM1* és a *myVM2* értékeket és a megfelelő nyilvános IP-címeiket.
4. Kattintson a **Hozzáadás** parancsra.
5. Ellenőrizze, hogy a terheléselosztó háttérkészlet beállítása mindkét virtuális gépet megjeleníti-e: **myVM1** és **myVM2**.
 
    ![Háttérkészlet létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területen kattintson az **Állapotminták** elemre, majd a **Hozzáadás** gombra.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
    - Az állapotminta neve: *myHealthProbe*.
    - Protokoll típusa: **HTTP**.
    - Portszám: *80*.
    - A mintavételi kísérletek közötti **Időköz** másodpercben megadva: *15*.
    - A **Nem kifogástalan állapot küszöbértéke**, vagyis hány egymást követő meghibásodásnak kell történnie, hogy a rendszer sérültnek minősítsen egy virtuális gépet: *2*.
4. Kattintson az **OK** gombra.

   ![Mintavétel hozzáadása](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt, amely felügyeli a *FrontendLoadBalancer* előtér 80-as portját, és terhelés szempontjából elosztott forgalmat továbbít a szintén a 80-as portot használó, *myBackEndPool* nevű háttércímkészlethez. 

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területen kattintson a **Terheléselosztási szabályok** elemre, majd a **Hozzáadás** gombra.
3. Konfigurálja a terheléselosztási szabályt az alábbi értékekkel:
    - A terheléselosztási szabály neve: *myHTTPRule*.
    - Protokoll típusa: **TCP**.
    - Portszám: *80*.
    - Háttérport: *80*.
    - A háttérkészlet neve: *myBackendPool*.
    - Az állapotminta neve: *myHealthProbe*.
4. Kattintson az **OK** gombra.
    
    ![Terheléselosztási szabály hozzáadása](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Kattintson a **Minden erőforrás**, majd a **myPublicIP** elemre. 

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. A böngészőben megjelenik a webkiszolgáló nevét tartalmazó alapértelmezett weblap.

      ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. A terheléselosztó műveletvégzés közbeni megtekintéséhez kényszerítheti a megjelenített virtuális gép leállítását, és frissítheti a böngészőablakot, hogy megjelenjen a másik kiszolgáló neve a böngészőben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Ehhez válassza ki a terheléselosztót tartalmazó erőforráscsoportot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md).
- [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
