---
title: Alapszintű nyilvános Load Balancer létrehozása – Azure Portal | Microsoft Docs
description: Megismerheti, hogyan hozható létre alapszintű nyilvános Load Balancer az Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: c646b0b1ab0ec62cffb4f7cf7474b48c68dfabb4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-public-basic-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Alapszintű nyilvános Load Balancer létrehozása a virtuális gépek terhelésének elosztásához az Azure Portal használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Az Azure Portal használatával létrehozhat egy terheléselosztót a virtuális gépek terhelésének elosztása érdekében. Ez a rövid útmutató ismerteti, hogyan hozhat létre hálózati erőforrásokat, háttérkiszolgálókat és alapszintű nyilvános Load Balancert.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.

## <a name="create-a-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ebben a szakaszban egy nyilvános alapszintű Load Balancert fog létrehozni az Azure Portal használatával. A nyilvános IP-cím automatikusan konfigurálva van a Load Balancer előtereként (neve: *LoadBalancerFrontend*), amikor létrehozza a nyilvános IP-címet a terheléselosztó erőforrásnak az Azure Portallal történő létrehozása során.

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Terheléselosztó** elemre.
2. A **Terheléselosztó létrehozása** lapon adja meg az alábbi értékeket a terheléselosztó számára:
    - A terheléselosztó neve: *myLoadBalancer*.
    - A terheléselosztó típusa: **Nyilvános**.
    - A nyilvános IP-cím, amelyet **Alapszintű** termékváltozattal és **Dinamikus** **Hozzárendelés** értékkel kell létrehoznia: *myPublicIP*.
    - A létrehozott új erőforráscsoport neve: *myResourceGroupLB*.
3. A terheléselosztó létrehozásához kattintson a **Létrehozás** elemre.
   
    ![Load Balancer létrehozása](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Eben a szakaszban egy virtuális hálózatot fog létrehozni, illetve két virtuális gépet az alapszintű Load Balancer háttérkészlete számára, majd telepíteni fogja az IIS-t a virtuális gépeken a terheléselosztó tesztelése érdekében.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. A képernyő bal felső részén kattintson az **Új** > **Hálózatkezelés** > **Virtuális hálózat** elemre, és adja meg az alábbi értékeket:
    - A virtuális hálózat neve: *myVnet*.
    - A létező erőforráscsoport neve: *myResourceGroupLB*
    - Az alhálózat neve: *myBackendSubnet*.
2. A virtuális hálózat létrehozásához kattintson a **Létrehozás** gombra.

    ![Virtuális hálózat létrehozása](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A képernyő bal felső részén kattintson az **Új** > **Számítás** > **Windows Server 2016 Datacenter** elemre, és adja meg az alábbi értékeket a virtuális gép számára:
    - A virtuális gép neve: *myVM1*.        
    - A rendszergazda felhasználóneve: *azureuser*. -    
    - *myResourceGroupLB*: Az **Erőforráscsoport** esetében válassza a **Meglévő használata** lehetőséget, majd válassza ki a *myResourceGroupLB* erőforráscsoportot.
2. Kattintson az **OK** gombra.
3. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
4. Adja meg az alábbi értékeket a virtuális gép beállításainál:
    - A létrehozandó új rendelkezésre állási csoport neve: *myAvailabilitySet*.
    -  Győződjön meg róla, hogy a következő virtuális hálózat van kiválasztva: *myVNet*.
    - Győződjön meg róla, hogy a következő alhálózat van kiválasztva: *myBackendSubnet*.
    - A nyilvános IP-cím: *myVM1-ip*.
    - A kötelezően létrehozandó új hálózati biztonsági csoport (tűzfal) neve: *myNetworkSecurityGroup*.
5. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
6. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.
7. Az 1–6. lépések használatával hozzon létre egy második virtuális gépet *VM2* néven, a *myAvailabilityset* rendelkezésre állási csoporttal, a *myVnet* virtuális hálózattal, a *myBackendSubnet* alhálózattal és a *myNetworkSecurityGroup* hálózati biztonsági csoporttal. 

### <a name="create-nsg-rules"></a>NSG-szabályok létrehozása

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
 
 ![Virtuális hálózat létrehozása](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
5. Ismételje meg a 2–4. lépéseket egy másik, *myRDPRule* nevű szabály létrehozásához, amely engedélyezi a 3389-es portot használó, bejövő RDP-kapcsolatokat. Ehhez adja meg az alábbi értékeket:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *3389*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *200*
    - Név: *myRDPRule*
    - Leírás: *RDP engedélyezése*

   

### <a name="install-iis"></a>Az IIS telepítése

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd az erőforrások listájában kattintson a *myResourceGroupLB* erőforráscsoportban található **myVM1** elemre.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. Jelentkezzen be a virtuális gépbe az *azureuser* felhasználónévvel és az *Azure123456!* jelszóval.
4. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
5. A Kiszolgálókezelőben kattintson a Kezelés, majd a **Szerepkörök és szolgáltatások hozzáadása** elemre.
 ![Kiszolgálókezelői szerepkör hozzáadása](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. A **Szerepkörök és szolgáltatások hozzáadása varázslóban** használja az alábbi értékeket:
    - A **Telepítés típusának kiválasztása** lapon kattintson a **Szerepköralapú vagy szolgáltatásalapú telepítés** elemre.
    - A **Célkiszolgáló kijelölése** oldalon kattintson a **myVM1** lehetőségre
    - A **Kiszolgáló-szerepkör kiválasztása** lapon kattintson a **Webkiszolgáló (IIS)** lehetőségre
    - Kövesse az utasításokat és végezze el a varázsló további lépéseit 
7. Ismételje meg az 1–6. lépéseket a *myVM2* nevű virtuális gép esetében.

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

    ![Hozzáadás a háttércímkészlethez – ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

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

   ![Mintavétel hozzáadása](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

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
    
    ![Terheléselosztási szabály hozzáadása](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Kattintson a **Minden erőforrás**, majd a **myPublicIP** elemre.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

  ![IIS-webkiszolgáló](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Ehhez válassza ki a terheléselosztót tartalmazó erőforráscsoportot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy erőforráscsoportot, hálózati erőforrásokat és háttérkiszolgálókat hozott létre. Ezután az erőforrások használatával létrehozott egy terheléselosztót. Ha további információra van szüksége a terheléselosztókra és a kapcsolódó erőforrásokra vonatkozóan, lépjen tovább az oktatóanyag cikkeire.
