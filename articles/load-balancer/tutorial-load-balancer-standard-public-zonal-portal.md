---
title: 'Oktatóanyag: Terheléselosztó virtuális gépek egy zónában – Azure Portal | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy standard Load Balancert zónaszintű előtérrel a rendelkezésre állási zónában lévő virtuális gépek Azure Portal használatával történő terheléselosztásához
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
ms.date: 05/17/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 580015b7f8b1f894c69ddec0f26daeb524932e4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637293"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Oktatóanyag: Rendelkezésre állási zónán belüli virtuális gépek terheléselosztása standard Load Balancerrel az Azure Portal használatával

Az oktatóanyag egy nyilvános [Azure Standard Load Balancer-példányt](https://aka.ms/azureloadbalancerstandard) hoz létre zónaszintű előtérrel, amely nyilvános, szabványos IP-címet használ az Azure Portal használatával. Ennek a forgatókönyvnek a követésével megadhat egy adott zónát az előtér- és háttérpéldányokhoz, hogy hozzáigazíthassa az adatútvonalat és az erőforrásokat a meghatározott zónához. A következő függvények végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Standard Load Balancer-példány létrehozása zónaszintű előtérrel.
> * Hálózati biztonsági csoportok létrehozása bejövő forgalomra vonatkozó szabályok meghatározásához
> * Zónaszintű virtuális gépek (VM-ek) létrehozása és terheléselosztóhoz hozzájuk csatolása
> * Terheléselosztó állapotmintájának létrehozása
> * Terheléselosztó forgalmára vonatkozó szabályok létrehozása
> * Alapszintű Internet Information Services (IIS) hely létrehozása
> * Terheléselosztó megtekintése működés közben

A rendelkezésre állási zónák Standard Load Balancerrel való használatáról bővebben a [Standard Load Balancer és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md) című cikkben tájékozódhat.

Igény szerint az oktatóanyag az [Azure CLI](load-balancer-standard-public-zonal-cli.md) használatával is elvégezhető.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.

## <a name="create-a-public-standard-load-balancer-instance"></a>Nyilvános Standard Load Balancer-példány létrehozása

A Standard Load Balancer csak a szabványos nyilvános IP-címeket támogatja. Ha a terheléselosztó létrehozása során létrehoz egy új standard IP-címet, akkor az automatikusan standard termékváltozatként lesz konfigurálva. Ezenkívül automatikusan zónaredundánssá is válik.

1. A Portal bal felső sarkában kattintson az **Erőforrás létrehozása** > **Hálózat** > **Load Balancer** elemre.
2. A **Terheléselosztó létrehozása** lapon adja meg az alábbi értékeket a terheléselosztó számára:
    - A terheléselosztó neve: **myLoadBalancer**
    - A terheléselosztó típusa: **Nyilvános**
     - A létrehozott új nyilvános IP-cím: **myPublicIPZonal**. Válassza kis a **Válasszon egy nyilvános IP-címet** elemet. Ezután kattintson az **Új létrehozása** elemre. A névhez írja be a következőt: **myPublicIP**. A termékváltozat alapértelmezés szerint a standard. A **Rendelkezésre állási zóna** kiválasztásánál válassza az **1. zóna** elemet.
    - A létrehozott új erőforráscsoport neve: **myResourceGroupZLB**.
    - A hely: **westeurope**.
3. A terheléselosztó létrehozásához válassza a **Létrehozás** elemet.
   
    ![Hozzon létre egy standard Load Balancer-példányt az Azure Portal használatával](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban egy új virtuális hálózatot hozhat létre. Két, ugyanebben zónában (azaz az 1. zónában) lévő virtuális gépet is létrehoz hata régió számára, hogy hozzáadja a terheléselosztója háttérkészletéhez. Ezután a virtuális gépeken telepítheti az IIS-t a zóna redundáns terheléselosztó teszteléséhez. Ha az egyik virtuális gép meghiúsul, az azonos zónában lévő virtuális gép állapot-ellenőrzése is meghiúsul. A forgalmat továbbra is a zónában található más virtuális gépek szolgáltatják.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. A képernyő bal felső sarkában kattintson az **Erőforrás létrehozása** > **Hálózat** > **virtuális hálózat** elemre.  Adja meg a következő értékeket a virtuális hálózat számára:
    - A virtuális hálózat neve **myVNet**.
    - A létező erőforráscsoport neve: **myResourceGroupZLB**
    - Az alhálózat neve: **myBackendSubnet**
2. A virtuális hálózat létrehozásához válassza a **Létrehozás** elemet.

    ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. A képernyő bal felső sarkában kattintson az **Erőforrás létrehozása** elemre. A keresőmezőbe írja be a **Network Security csoport** kifejezést. A hálózati biztonsági csoport oldalon válassza a **Létrehozás** elemet.
2. A **Hálózati biztonsági csoport létrehozása** lapon adja meg a következő értékeket:
    - A hálózati biztonsági csoport neve: **myNetworkSecurityGroup**.
    - A létező erőforráscsoport neve: **myResourceGroupBAZ**.
   
    ![Hálózati biztonsági csoport létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>NSG-szabályok létrehozása

Ebben a szakaszban NSG-szabályokat fog létrehozni a HTTP-t és Microsoft távoli asztalt (RDP-t) használó bejövő kapcsolatok engedélyezése érdekében az Azure Portal használatával.

1. Az Azure Portalon válassza a **Minden erőforrás** elemet. Keressen rá, és válassza a **myNetworkSecurityGroup** elemet. A **MyResourceGroupZLB** erőforráscsoportban található.
2. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet. Ezután válassza a **Hozzáadás** lehetőséget.
3. Adja meg az alábbi értékeket a **myHTTPRule** nevű bejövő biztonsági szabály számára a 80-as portot használó bejövő HTTP-kapcsolatok engedélyezése érdekében.
    - **Forrás**: **Szolgáltatáscímke**
    - **Forrás szolgáltatáscímkéje**: **Internet**
    - **Célporttartomány**: **80**
    - **Protokoll**: **vTCP**
    - **Művelet**: **Engedélyezés**
    - **Prioritás**: **100**
    - **Név**: **myHTTPRule**
    - **Leírás**: **HTTP engedélyezése**
4. Kattintson az **OK** gombra.
 
 ![NSG-szabályok létrehozása](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Ismételje meg a 2–4. lépést egy másik, **myRDPRule** nevű szabály létrehozásához. Ez a szabály engedélyezi a 3389-es portot használó bejövő RDP-kapcsolatokat a következő értékekkel:
    - **Forrás**: **Szolgáltatáscímke**
    - **Forrás szolgáltatáscímkéje**: **Internet**
    - **Célporttartomány**: **3389**
    - **Protokoll**: **TCP**
    - **Művelet**: **Engedélyezés**
    - **Prioritás**: **200**
    - **Név**: **myRDPRule**
    - **Leírás**: **RDP engedélyezése**

    ![RDP-szabály létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A képernyő bal felső sarkában kattintson az **Erőforrás létrehozása** > **Számítás** > **Windows Server 2016 Datacenter** elemre. Adja meg a következő értékeket a virtuális gép számára:
    - A virtuális gép neve: **myVM1**.        
    - A rendszergazda felhasználóneve: **azureuser**.    
    - **Erőforráscsoport**: **myResourceGroupZLB**. Válassza a **Meglévő használata**, majd a **myResourceGroupZLB** lehetőséget.
2. Kattintson az **OK** gombra.
3. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget. Válassza a **Kiválasztás** lehetőséget
4. Adja meg az alábbi értékeket a virtuális gép beállításainál:
    - A rendelkezésre állási zóna, ahol elhelyezi a virtuális gépet: **1. zóna**.
    -  **myVNet**. Győződjön meg róla, hogy virtuális hálózatként ez van kiválasztva.
    - A létrehozandó standard nyilvános IP-cím: **myVM1PIP**. Válassza az **Új létrehozása** lehetőséget. A névtípushoz válassza a **myVM1PIP** lehetőséget. **Zóna**: **1**. Az IP-cím termékváltozata alapértelmezés szerint a standard.
    - **myBackendSubnet**. Győződjön meg róla, hogy alhálózatként ez van kiválasztva.
    - A már létező hálózati biztonsági csoport tűzfal neve: **myNetworkSecurityGroup**.
5. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
6. Kattintson az **OK** gombra. Az összegzés lapon ellenőrizze a beállításokat. Ezután kattintson a **Létrehozás** elemre.
7. Ismételje meg az 1–6. lépést egy másik, **myVM2** nevű, 1. zónás virtuális hálózat létrehozásához. A virtuális hálózat a **myVnet** legyen. A standard nyilvános IP-cím a **myVM2PIP** legyen. Az alhálózat a **myBackendSubnet** legyen. A hálózati biztonsági csoport a **myNetworkSecurityGroup** legyen.

    ![Virtuális gépek létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Az IIS telepítése virtuális gépekre

1. A bal oldalsó menüben válassza a **Minden erőforrás** elemet. Az erőforrás listából válassza a **myVM1** elemet. A **MyResourceGroupZLB** erőforráscsoportban található.
2. Az **Áttekintés** oldalon a **Csatlakozás** elemet kiválasztva használja az RDP-kapcsolatot a virtuális gépre lépéshez.
3. Jelentkezzen be a virtuális gép létrehozásakor megadott felhasználónévvel és jelszóval. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek** lehetőséget kell választania. Ezután válassza a **Használjon egy másik fiókot** elemet. Majd válassza az **OK** elemet. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.
4. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök** > **Windows PowerShell** elemre.
6. A **PowerShell**-ablakban futtassa az alábbi parancsokat az IIS kiszolgáló telepítéséhez: Ezek a parancsok eltávolítják az alapértelmezett iisstart.htm fájlt, majd hozzáadnak egy új iisstart.htm fájt, amely megjeleníti a virtuális gép nevét:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Szüntesse meg a távoli asztali munkamenetet a **myVM1**-gyel.
8. Ismételje meg az 1–7. lépést az IIS telepítéséhez a **myVM2** gépen.

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára. Megadja a terheléselosztó és a hálózati címfordítás szabályait is.


### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott virtuális hálózati adapterek IP-címét. Hozza létre a háttércímkészletet **myBackendPool** néven, amely magában foglalja a **VM1** és **VM2** virtuális gépeket.

1. A bal oldalsó menüben válassza a **Minden erőforrás** elemet. Majd az erőforrás listából válassza a **myLoadBalancer** elemet.
2. A **Beállítások** alatt válassza ki a **Háttérkészletek** elemet. Ezután válassza a **Hozzáadás** lehetőséget.
3. A **Háttérkészlet hozzáadása** oldalon tegye a következőket:
    - Adja a következő nevet a háttérkészletnek: **myBackEndPool**.
    - Válassza a **Virtuális hálózat** legördülő menüben a **myVNet** lehetőséget. 
    - A **Virtuális gép** és az **IP-cím** beállításához vegye fel a **myVM1** és a **myVM2** értékeket és a megfelelő nyilvános IP-címeiket.
4. Válassza a **Hozzáadás** lehetőséget.
5. Ellenőrizze, hogy a terheléselosztó háttérkészlet beállítása mindkét virtuális gépet megjeleníti-e: **myVM1** és **myVM2**.
 
    ![Háttérkészlet létrehozása](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Használja az állapotmintát ahhoz, hogy a terheléselosztó monitorozza az alkalmazás állapotát. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy **myHealthProbe** nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. A bal oldalsó menüben válassza a **Minden erőforrás** elemet. Majd az erőforrás listából válassza a **myLoadBalancer** elemet.
2. A **Beállítások** területen válassza az **Állapotminták** elemet. Ezután válassza a **Hozzáadás** lehetőséget.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
    - Az állapotminta neve: **myHealthProbe**
    - Protokoll típusa: **HTTP**
    - Portszám: **80**
    - A mintavételi kísérletek közötti **Időköz** másodpercben megadva: **15**.
    - A **Nem kifogástalan állapot küszöbértéke**, vagyis hány egymást követő meghibásodásnak kell történnie, hogy a rendszer sérültnek minősítsen egy virtuális gépet: **2**.
4. Kattintson az **OK** gombra.

   ![Állapotminta hozzáadása](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy **myLoadBalancerRuleWeb** nevű terheléselosztási szabályt a 80-as port a **FrontendLoadBalancer** előtérben való figyeléséhez. A szabály az elosztott terhelésű hálózati forgalmat a **myBackEndPool** nevű háttércímkészlethez küldi, szintén a 80-as port használatával. 

1. A bal oldalsó menüben válassza a **Minden erőforrás** elemet. Majd az erőforrás listából válassza a **myLoadBalancer** elemet.
2. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet. Ezután válassza a **Hozzáadás** lehetőséget.
3. Konfigurálja a terheléselosztási szabályt az alábbi értékekkel:
    - A terheléselosztási szabály neve: **myHTTPRule**.
    - Protokoll típusa: **TCP**
    - Portszám: **80**
    - Háttérport: **80**.
    - A háttérkészlet neve: **myBackendPool**.
    - Az állapotminta neve: **myHealthProbe**
4. Kattintson az **OK** gombra.
    
    ![Terheléselosztási szabály hozzáadása](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a terheléselosztó nyilvános IP-címét az **Áttekintés** képernyőn. Válassza az **Összes erőforrás** elemet. Ezután válassza a **MyPublicIP** elemet. 

2. Másolja le a nyilvános IP-címet. Ezután illessze be a böngésző címsorába. A böngészőben megjelenik a webkiszolgáló nevét tartalmazó alapértelmezett lap.

      ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Ha működés közben szeretné látni a terheléselosztót, kényszerítse leállításra a megjelenő virtuális gépet. A böngésző frissítése után megtekintheti a böngészőben megjelenő másik kiszolgáló nevét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Válassza ki az erőforrásokat tartalmazó erőforráscsoport. Ezután válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md).
- [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
