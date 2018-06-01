---
title: 'Első lépések: Alapszintű nyilvános terheléselosztó létrehozása az Azure Portallal | Microsoft Docs'
description: Ez az útmutató bemutatja, hogyan hozhat létre alapszintű nyilvános terheléselosztót az Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 49fa4cf9b24c432b0956f930a1429e1cdf827f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304878"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Első lépések: Alapszintű nyilvános terheléselosztó létrehozása az Azure Portallal

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Az Azure Portal használatával létrehozhat egy terheléselosztót a virtuális gépek terhelésének elosztása érdekében. Ez a rövid útmutató ismerteti, hogyan hozhat létre hálózati erőforrásokat, háttérkiszolgálókat és egy alapszintű tarifacsomaggal rendelkező terheléselosztót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az útmutató minden lépéséhez be kell jelentkeznie az [Azure Portalra](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Alapszintű terheléselosztó létrehozása

Ebben a szakaszban egy nyilvános alapszintű terheléselosztót fog létrehozni a Portal használatával. A nyilvános IP-cím automatikusan konfigurálva van a terheléselosztó előtereként, amikor létrehozza a nyilvános IP-címet és a terheléselosztó erőforrást a Portallal. Az előtér neve **LoadBalancerFrontend**.

1. A Portal bal felső sarkában kattintson az **Erőforrás létrehozása** > **Hálózat** > **Terheléselosztó** elemre.
2. A **Terheléselosztó létrehozása** panelen adja meg a következő az értékeket:
   - A terheléselosztó neve: **myLoadBalancer**
   - A terheléselosztó típusa: **Nyilvános** 
   - A nyilvános IP-cím: **myPublicIP**, amelyet **Alapszintű** **termékváltozattal** és **Dinamikus** **Hozzárendelés** értékkel kell létrehoznia
   - Az új erőforráscsoport neve: **myResourceGroupLB**
3. Kattintson a **Létrehozás** gombra.
   
![Load Balancer létrehozása](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban egy virtuális hálózatot, valamint két virtuális gépet hoz létre az alapszintű terheléselosztó háttérkészletéhez. Ezt követően telepíti az Internet Information Services (IIS) szolgáltatást a virtuális gépeken, így tesztelheti a terheléselosztót.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. A Portal bal felső sarkában kattintson az **Új** > **Hálózat** > **Virtuális hálózati** lehetőségre.
2. A **Virtuális hálózat létrehozása** panelen adja meg a következő értékeket, és válassza a **Létrehozás** lehetőséget:
   - A virtuális hálózat neve: **myVnet**
   - A létező erőforráscsoport neve: **myResourceGroupLB**
   - Az alhálózat neve: **myBackendSubnet**

   ![Virtuális hálózat létrehozása](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A Portal bal felső oldalán kattintson az **Új** > **Számítás** > **Windows Server 2016 Datacenter** lehetőségre. 
2. Adja meg a következő értékeket a virtuális géphez, majd kattintson az **OK** gombra:
   - A virtuális gép neve: **myVM1**.        
   - A rendszergazda felhasználóneve: **azureuser**.    
   - Az erőforráscsoport: **myResourceGroupLB**. (Az **Erőforráscsoport** területen válassza a **Meglévő használata** lehetőséget, majd válassza ki a **myResourceGroupLB** erőforráscsoportot.)   
3. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
4. Adja meg az alábbi értékeket a virtuális gép beállításainál:
   - A létrehozandó új rendelkezésre állási csoport neve: **myAvailabilitySet**.
   - A virtuális hálózat neve: **myVnet**. (Ügyeljen rá, hogy ki legyen jelölve.)
   - Az alhálózat neve: **myBackendSubnet**. (Ügyeljen rá, hogy ki legyen jelölve.)
   - A nyilvános IP-cím: **myVM1-ip**.
   - A kötelezően létrehozandó új hálózati biztonsági csoport (NSG, egy tűzfaltípus) neve: **myNetworkSecurityGroup**.
5. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
6. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.
7. Az 1–6. lépésekkel hozzon létre egy második virtuális gépet **VM2** névvel, melyhez az alábbi értékeket adja meg:
   - A rendelkezésre állási csoport: **myAvailabilityset**.
   - A virtuális hálózat: **myVnet**.
   - Az alhálózat: **myBackendSubnet**.
   - A hálózati biztonsági csoport: **myNetworkSecurityGroup**. 

### <a name="create-nsg-rules"></a>NSG-szabályok létrehozása

Ebben a szakaszban NSG-szabályokat fog létrehozni a HTTP-t és RDP-t használó bejövő kapcsolatok engedélyezése érdekében.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforráslistában válassza a **myNetworkSecurityGroup** elemet a **myResourceGroupLB** erőforráscsoportban.
2. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet, majd a **Hozzáadás** lehetőséget.
3. Adja meg az alábbi értékeket a **myHTTPRule** nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében. Ezután kattintson az **OK** gombra.
   - **Forrás**: **Szolgáltatáscímke**
   - **Forrás szolgáltatáscímkéje**: **Internet**
   - **Célporttartomány**: **80**
   - **Protokoll**: **TCP**
   - **Művelet**: **Engedélyezés**
   - **Prioritás**: **100**
   - **Név**: **myHTTPRule**
   - **Leírás**: **HTTP engedélyezése**
 
   ![NSG-szabály létrehozása](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. Ismételje meg a 2–3. lépéseket egy másik, **myRDPRule** nevű szabály létrehozásához, amely engedélyezi a 3389-es portot használó, bejövő RDP-kapcsolatokat. Használja a következő értékeket:
   - **Forrás**: **Szolgáltatáscímke**
   - **Forrás szolgáltatáscímkéje**: **Internet**
   - **Célporttartomány**: **3389**
   - **Protokoll**: **TCP**
   - **Művelet**: **Engedélyezés**
   - **Prioritás**: **200**
   - **Név**: **myRDPRule**
   - **Leírás**: **RDP engedélyezése**

   

### <a name="install-iis"></a>Az IIS telepítése

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforráslistában válassza a **myVM1** elemet a **myResourceGroupLB** erőforráscsoportban.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. Jelentkezzen be a virtuális gépbe az **azureuser** felhasználónévvel és az **Azure123456!** jelszóval.
4. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök** > **Kiszolgálókezelő** elemre.
5. A Kiszolgálókezelőben kattintson a **Kezelés**, majd a **Szerepkörök és szolgáltatások hozzáadása** elemre.
   ![Kiszolgálókezelői szerepkör hozzáadása](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. A Szerepkörök és szolgáltatások hozzáadása varázslóban használja az alábbi értékeket:
   - A **Telepítés típusának kiválasztása** lapon kattintson a **Szerepköralapú vagy szolgáltatásalapú telepítés** elemre.
   - A **Célkiszolgáló kijelölése** oldalon kattintson a **myVM1** lehetőségre.
   - A **Kiszolgáló-szerepkör kiválasztása** lapon kattintson a **Webkiszolgáló (IIS)** lehetőségre.
   - Kövesse az utasításokat és végezze el a varázsló további lépéseit. 
7. Ismételje meg az 1–6. lépéseket a **myVM2** nevű virtuális gép esetében.

## <a name="create-resources-for-the-basic-load-balancer"></a>Erőforrások létrehozása az alapszintű terheléselosztóhoz

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, illetve terheléselosztási és NAT-szabályokat fog megadni.


### <a name="create-a-back-end-address-pool"></a>Háttércímkészlet létrehozása

Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét. Hozza létre a háttércímkészletet **myBackendPool** néven, amely magában foglalja a **VM1** és **VM2** virtuális gépeket.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** alatt válassza a **Háttérkészletek**, majd a **Hozzáadás** lehetőséget.
3. A **Háttérkészlet hozzáadása** oldalon tegye a következőket, majd kattintson az **OK** gombra:
   - **Névnek** adja meg a **myBackEndPool** kifejezést.
   - A **Társítva ehhez** terület legördülő menüjében kattintson a **Rendelkezésre állási csoport** lehetőségre.
   - A **Rendelkezésre állási csoport** mezőben válassza ki a **myAvailabilitySet** elemet.
   - Kattintson a **Célhálózati IP-konfiguráció hozzáadása** elemre az összes létrehozott virtuális gép (**myVM1** és **myVM2**) hozzáadásához a háttérkészlethez.

   ![Hozzáadás a háttércímkészlethez](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Ellenőrizze, hogy a terheléselosztó háttérkészlet beállítása mindkét virtuális gépet megjeleníti-e (**VM1** és **VM2**).

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy az alapszintű terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy **myHealthProbe** nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
3. Adja meg a következő értékeket, majd kattintson az **OK** gombra:
   - Az állapotminta neve: **myHealthProbe**
   - Protokoll típusa: **HTTP**
   - Portszám: **80**
   - A mintavételi kísérletek közötti **Időköz** másodpercben megadva: **15**
   - A **Nem kifogástalan állapot küszöbértéke**, vagyis hány egymást követő meghibásodásnak kell történnie, hogy a rendszer sérültnek minősítsen egy virtuális gépet: **2**.

   ![Mintavétel hozzáadása](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz, a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. 

Hozzon létre egy **myLoadBalancerRuleWeb** nevű terheléselosztási szabályt a 80-as port a **LoadBalancerFrontEnd** előtérben való figyeléséhez. A szabály az elosztott terhelésű hálózati forgalom a **myBackEndPool** nevű háttércímkészlethez küldésére is vonatkozik, szintén a 80-as port használatával. 

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet, majd a **Hozzáadás** lehetőséget.
3. Adja meg a következő értékeket, majd kattintson az **OK** gombra:
   - A terheléselosztási szabály neve: **myHTTPRule**
   - Protokoll típusa: **TCP**
   - Portszám: **80**
   - Háttérport: **80**
   - A háttérkészlet neve: **myBackendPool**
   - Az állapotminta neve: **myHealthProbe**
    
   ![Terheléselosztási szabály hozzáadása](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a terheléselosztó nyilvános IP-címét az **Áttekintés** képernyőn. Kattintson a **Minden erőforrás**, majd a **myPublicIP** elemre.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala megjelenik a böngészőben.

   ![IIS-webkiszolgáló](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rászükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Válassza ki a terheléselosztót tartalmazó erőforráscsoportot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy erőforráscsoportot, hálózati erőforrásokat és háttérkiszolgálókat hozott létre. Ezután az erőforrások használatával létrehozott egy alapszintű terheléselosztót. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-basic-internal-portal.md)
