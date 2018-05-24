---
title: 'Oktatóanyag: Alapszintű nyilvános Load Balancer létrehozása – Azure Portal | Microsoft Docs'
description: Ebben az oktatóanyagban megismerheti, hogyan hozható létre alapszintű belső Load Balancer az Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a16e9ad5b72d87614f5d3630e24e6aa36def8c51
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-load-balance-internal-traffic-with-basic-load-balancer-to-vms-using-the-azure-portal"></a>Oktatóanyag: Virtuális gépek belső bejövő forgalmának terheléselosztása alapszintű Load Balancerrel az Azure Portal használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Az Azure Portal használatával eloszthatja a virtuális gépek belső bejövő forgalmát egy alapszintű Load Balancerrel. Ez az oktatóanyag ismerteti, hogyan hozhat létre hálózati erőforrásokat, háttérkiszolgálókat és egy alapszintű belső Load Balancert.

Igény szerint az oktatóanyagot az [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) vagy az [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) használatával is elvégezheti.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. A képernyő bal felső részén kattintson az **Új** > **Hálózatkezelés** > **Virtuális hálózat** elemre, és adja meg az alábbi értékeket:
    - A virtuális hálózat neve: *myVnet*.
    - A meglévő erőforráscsoport neve: *myResourceGroupILB*
    - Az alhálózat neve: *myBackendSubnet*.
2. A virtuális hálózat létrehozásához kattintson a **Létrehozás** gombra.

## <a name="create-a-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása
Hozzon létre egy belső Load Balancert a Portal használatával.

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Terheléselosztó** elemre.
2. A **Terheléselosztó létrehozása** lapon adja meg az alábbi értékeket a terheléselosztó számára:
    - A terheléselosztó neve: *myLoadBalancer*.
    - A Load Balancer típusa: **belső**.
    - Termékváltozat: **alapszintű**.
    - A statikus magánhálózati IP-cím: **10.1.0.7**.
    - A meglévő hálózatok listájáról kiválasztandó virtuális hálózat: *myVNet*.
    - A meglévő alhálózatok listájáról kiválasztandó alhálózat: *mySubnet*.
    - A létrehozott új erőforráscsoport neve: *myResourceGroupILB*.
3. A terheléselosztó létrehozásához kattintson a **Létrehozás** elemre.
   
    ![Load Balancer létrehozása](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)


## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Eben a szakaszban két virtuális gépet fog létrehozni az alapszintű Load Balancer háttérkészlete számára, majd telepíteni fogja az IIS-t a virtuális gépeken a Load Balancer tesztelése érdekében.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Számítás** > **Windows Server 2016 Datacenter** elemre, és adja meg a következő értékeket a virtuális gép számára:
    - A virtuális gép neve: *myVM1*.        
    - A rendszergazda felhasználóneve: *azureuser*.   
    - *myResourceGroupILB*: az **Erőforráscsoport** esetében válassza a **Meglévő használata** lehetőséget, majd válassza ki a *myResourceGroupILB* erőforráscsoportot.
2. Kattintson az **OK** gombra.
3. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
4. Adja meg az alábbi értékeket a virtuális gép beállításainál:
    - A létrehozandó új rendelkezésre állási csoport neve: *myAvailabilitySet*.
    -  Győződjön meg róla, hogy a következő virtuális hálózat van kiválasztva: *myVNet*.
    - Győződjön meg róla, hogy a következő alhálózat van kiválasztva: *myBackendSubnet*.
    - A kötelezően létrehozandó új hálózati biztonsági csoport (tűzfal) neve: *myNetworkSecurityGroup*.
5. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
6. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.
7. Az 1–6. lépések használatával hozzon létre egy második virtuális gépet *VM2* néven, a *myAvailabilityset* rendelkezésre állási csoporttal, a *myVnet* virtuális hálózattal, a *myBackendSubnet* alhálózattal és a *myNetworkSecurityGroup* hálózati biztonsági csoporttal. 

### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd az erőforrások listájában kattintson a *myResourceGroupILB* erőforráscsoportban található **myVM1** elemre.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. Jelentkezzen be a virtuális gépre.
4. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
5. Indítsa el a Windows PowerShellt a VM1-en, és a következő parancsok használatával telepítse az IIS-kiszolgálót, valamint frissítse az alapértelmezett htm-fájlt.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
5. Szüntesse meg a távoli asztali kapcsolatot a *myVM1*-gyel.
6. Ismételje meg az 1–5. lépéseket a *myVM2*-vel az IIS telepítéséhez és az alapértelmezett weblap testreszabásához.

## <a name="create-nsg-rules"></a>NSG-szabályok létrehozása

Ebben a szakaszban NSG-szabályokat fog létrehozni a HTTP-t és RDP-t használó bejövő kapcsolatok engedélyezése érdekében.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd az erőforrások listájában kattintson a **myResourceGroupLB** erőforráscsoportban található **myNetworkSecurityGroup** elemre.
2. A **Beállítások** területen kattintson a **Bejövő biztonsági szabályok** lehetőségre, majd a **Hozzáadás** gombra.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - Név: *myHTTPRule*
    - Leírás: *HTTP engedélyezése*
4. Kattintson az **OK** gombra.
 
5. Ismételje meg a 2–4. lépéseket egy másik, *myRDPRule* nevű szabály létrehozásához, amely engedélyezi a 3389-es portot használó, bejövő RDP-kapcsolatokat. Ehhez adja meg az alábbi értékeket:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *3389*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *200*
    - Név: *myRDPRule*
    - Leírás: *RDP engedélyezése*

## <a name="create-basic-load-balancer-resources"></a>Alapszintű Load Balancer-erőforrások létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, illetve terheléselosztási és NAT-szabályokat fog megadni.


### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét. Hozza létre a háttércímkészletet *myBackendPool* néven, amely magában foglalja a *VM1* és *VM2* virtuális gépeket.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a **Hozzáadás** gombra.
3. A **Háttérkészlet hozzáadása** oldalon tegye a következőket:
    - Adja a következő nevet a háttérkészletnek: *myBackEndPool.
    - A **Társítva ehhez** terület legördülő menüjében kattintson a **Rendelkezésre állási csoport** lehetőségre.
    - A **Rendelkezésre állási csoport** mezőben válassza ki a **myAvailabilitySet** elemet.
    - Kattintson a **Célhálózati IP-konfiguráció hozzáadása** elemre az összes létrehozott virtuális gép (*myVM1* & *myVM2*) hozzáadásához a háttérkészlethez.
    - Kattintson az **OK** gombra.

        ![Hozzáadás a háttércímkészlethez – ](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)

3. Ellenőrizze, hogy a terheléselosztó háttérkészlet beállítása mindkét virtuális gépet megjeleníti-e (**VM1** és **VM2**).

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy az alapszintű Load Balancer monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területen kattintson az **Állapotminták** elemre, majd a **Hozzáadás** gombra.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
    - Az állapotminta neve: *myHealthProbe*.
    - Protokoll típusa: **HTTP**.
    - Portszám: *80*.
    - A mintavételi kísérletek közötti **Időköz** másodpercben megadva: *15*.
    - A **Nem kifogástalan állapot küszöbértéke**, vagyis hány egymást követő meghibásodásnak kell történnie, hogy a rendszer sérültnek minősítsen egy virtuális gépet: *2*.
4. Kattintson az **OK** gombra.

   ![Mintavétel hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt, amely felügyeli a *LoadBalancerFrontEnd* előtér 80-as portját, és terhelés szempontjából elosztott forgalmat továbbít a szintén a 80-as portot használó, *myBackEndPool* nevű háttércímkészlethez. 

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
    
    ![Terheléselosztási szabály hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="create-a-virtual-machine-to-test-the-load-balancer"></a>Virtuális gép létrehozása a Load Balancer teszteléséhez
A belső Load Balancer teszteléséhez létre kell hoznia egy olyan virtuális gépet, amely ugyanazon a virtuális hálózaton található, mint a háttérkiszolgálói virtuális gépek.
1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Számítás** > **Windows Server 2016 Datacenter** elemre, és adja meg a következő értékeket a virtuális gép számára:
    - A virtuális gép neve: *myVMTest*.        
    - *myResourceGroupILB*: az **Erőforráscsoport** esetében válassza a **Meglévő használata** lehetőséget, majd válassza ki a *myResourceGroupILB* erőforráscsoportot.
2. Kattintson az **OK** gombra.
3. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
4. Adja meg az alábbi értékeket a virtuális gép beállításainál:
    -  Győződjön meg róla, hogy a következő virtuális hálózat van kiválasztva: *myVNet*.
    - Győződjön meg róla, hogy a következő alhálózat van kiválasztva: *myBackendSubnet*.
5. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
6. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Az Azure Portalon keresse meg a Load Balancer magánhálózati IP-címét az **Áttekintés** képernyőn. Ehhez tegye a következőket: a. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
    b. Az **Áttekintés** részleteket tartalmazó oldalon másolja a magánhálózati IP-címet (ebben a példában ez a cím a 10.1.0.7).

2. Hozzon létre távoli kapcsolatot a *myVMTest* virtuális géppel a következő módon: a. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd az erőforrások listájában kattintson a *myResourceGroupILB* erőforráscsoportban található **myVMTest** elemre.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva indítson távoli munkamenetet a virtuális géppel.
3. Jelentkezzen be a *myVMTest* virtuális gépre.
3. Illessze be a magánhálózati IP-címet a böngésző címsorába a *myVMTest* virtuális gépen. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

      ![IIS-webkiszolgáló](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png)

Annak megtekintéséhez, hogyan osztja el a Load Balancer az alkalmazást futtató két virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Ehhez válassza ki a terheléselosztót tartalmazó erőforráscsoportot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy erőforráscsoportot, hálózati erőforrásokat és háttérkiszolgálókat hozott létre. Ezután az erőforrások használatával létrehozott egy belső Load Balancert a virtuális gépek bejövő belső forgalmának elosztására. A következőkben megtudhatja, [hogyan oszthatja el a virtuális gépek terhelését rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
