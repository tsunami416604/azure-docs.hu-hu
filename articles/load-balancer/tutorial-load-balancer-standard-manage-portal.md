---
title: 'Oktatóanyag: Terhelésének elosztása az internetes forgalmat a virtuális gépek – az Azure Portalon'
titlesuffix: Azure Load Balancer
description: Ez a rövid útmutató ismerteti, hogyan hozhat létre és felügyelhet egy Standard Load Balancert az Azure Portalon.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 78266e447d1ddf6daf5a9b0ad9172ab6470bf0c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845205"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Oktatóanyag: Terhelésének elosztása az internetes forgalmat a virtuális gépekhez az Azure portal használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ebben az oktatóanyagban elsajátíthatja az Azure Standard Load Balancer különböző összetevőivel, amelyek internetes forgalmat a virtuális gépek és a magas rendelkezésre állást biztosító kapcsolatban. Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * Az Azure Load Balancer létrehozása
> * Load Balancer-erőforrások létrehozása
> * Virtuális gépek létrehozása és IIS-kiszolgáló telepítése
> * Terheléselosztó megtekintése működés közben
> * Hozzáadhat és eltávolíthat a virtuális gépek egy terheléselosztó

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban egy Standard Load Balancer, amellyel a virtuális gépek terheléselosztása hoz létre. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Amikor létrehoz egy Standard Load Balancert, létre kell hoznia egy új standard nyilvános IP-címet is, amely a Standard Load Balancer előtereként van konfigurálva (alapértelmezés szerint *LoadBalancerFrontend* néven). 

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Terheléselosztó** elemre.
2. Az a **alapjai** lapján a **terheléselosztó létrehozása** lap, adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, és válassza **felülvizsgálat +létrehozása**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza ki **új létrehozása** , és írja be *myResourceGroupSLB* a szövegmezőben.|
    | Name (Név)                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Typo          | Válassza ki **nyilvános**.                                        |
    | SKU           | Válassza ki **Standard**.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím neve              | Típus *myPublicIP* a szövegmezőben.   |
    |Rendelkezésre állási zóna| Válassza ki **Zónaredundáns**.    |

3. Az a **felülvizsgálat + létrehozása** lapra, majd **létrehozás**.

   ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer-erőforrások létrehozása

Ebben a szakaszban egy háttércímkészlet és egy állapotminta terheléselosztó beállításainak konfigurálása, és adja meg a terheléselosztó-szabályt.

### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

Szeretné elosztani a forgalmat a virtuális gépekhez, egy háttércímkészlet tartalmazza a IP-címet a terheléselosztóhoz csatlakoztatott virtuális (NIC) címét. Hozza létre a háttércímkészletet *myBackendPool* internetes forgalom terhelését elosztja a virtuális gépeket tartalmazza.

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, és kattintson a **myLoadBalancer** az erőforrások listájában.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a **Hozzáadás** gombra.
3. A a **háttérkészlet hozzáadása** neveként, írja be a lapon *myBackendPool*, a háttérkészlet, és válassza ki a nevet **hozzáadása**.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Engedélyezi a Load Balancer monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan eltávolítása vagy virtuális gépeken a terheléselosztó elforgatása az adott válaszuk alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, és kattintson a **myLoadBalancer** az erőforrások listájában.
2. A **Beállítások** területen kattintson az **Állapotminták** elemre, majd a **Hozzáadás** gombra.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
     
    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *myHealthProbe*. |
    | Protokoll | Válassza ki **HTTP**. |
    | Port | Adja meg *80-as*.|
    | Intervallum | Adja meg *15* számú **időköz** mintavételi kísérletek közötti másodpercek alatt. |
    | Nem kifogástalan állapot küszöbértéke | Válassza ki *2* számú **nem kifogástalan állapot küszöbértéke** vagy egymást követő mintavételi hiba, amely előtt a virtuális gép nem megfelelő állapotúnak számít.|
    | Állapotadat-mintavétel | Válassza ki *myHealthProbe*. |
    
4. Kattintson az **OK** gombra.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy terheléselosztó-szabályt *myLoadBalancerRuleWeb* előtér 80-as portját *FrontendLoadBalancer* és terhelés szempontjából elosztott forgalmat a háttércímkészlethez *myBackEndPool* szintén a 80-as port használatával.

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, és kattintson a **myLoadBalancer** az erőforrások listájában.
2. A **Beállítások** területen kattintson a **Terheléselosztási szabályok** elemre, majd a **Hozzáadás** gombra.
3. Ezeket az értékeket használja a terheléselosztási szabály konfigurálása:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *myHTTPRule*. |
    | Protokoll | Válassza ki **TCP**. |
    | Port | Adja meg *80-as*.|
    | Háttérport | Adja meg *80-as*. |
    | Háttérkészlet | Válassza ki *myBackendPool*.|
    | Állapotadat-mintavétel | Válassza ki *myHealthProbe*. |
    
4. Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban egy virtuális hálózat létrehozása, három virtuális gépet a terheléselosztó háttérkészlete számára, és telepítse az IIS a virtuális gépeken a terheléselosztó tesztelése érdekében.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A képernyő bal felső oldalán válassza **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.
2. A **virtuális hálózat létrehozása**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *myVNet* értéket. |
    | Címtér | Adja meg *10.1.0.0/16*. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza ki a meglévő erőforrás - *myResourceGroupSLB*. |
    | Hely | Válassza a **Nyugat-Európa** régiót.|
    | Alhálózat - név | Adja meg a *myBackendSubnet* értéket. |
    | Alhálózat – címtartomány | Adja meg *10.1.0.0/24*. |
    
3. Hagyja meg az alapértelmezett beállításokat, és válassza ki a többi **létrehozás**.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A standard Load Balancer csak támogatja a virtuális gépeket, a háttérkészletben szabványos IP-címmel. Ebben a szakaszban három virtuális gépet hoz létre (*myVM1*, *myVM2*, és *myVM3*) három különböző zónákhoz tartozhatnak a Standard nyilvános IP-cím (*1. zónában*, *2. zóna*, és *3. zóna*), amely a korábban létrehozott Standard Load Balancer háttérkészlete kerülnek.

1. A portál bal felső oldalán válassza **erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter**. 
   
1. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:
   - **Előfizetés** > **erőforráscsoport**: Select **myResourceGroupSLB**.
   - **Példány részletei** > **virtuális gép neve**: Típus *myVM1*.
   - **Példány részletei** > **régió** > Válasszon **Nyugat-Európa**.
   - **Példány részletei** > **rendelkezésre állási beállítások** > Válasszon **rendelkezésre állási zónák**. 
   - **Példány részletei** > **rendelkezésre állási zónában** > Válasszon **1**.
  
1. Válassza ki a **hálózatkezelés** lapon, vagy válasszon **tovább: Lemezek**, majd **tovább: Hálózatkezelés**. 
   
   - Győződjön meg arról, hogy a következő kijelölt:
       - **Virtuális hálózat**: **myVnet**
       - **Alhálózat**: **myBackendSubnet**
       - **Nyilvános IP-cím** > válassza **új létrehozása**, majd a a **nyilvános IP-cím létrehozása** ablakban a **Termékváltozat**, jelölje be **Standard**, és a **rendelkezésre állási zónában**válassza **zónaredundáns**
      
   - Alatt létrehozni egy új hálózati biztonsági csoport (NSG), egy tűzfal, típusú **hálózati biztonsági csoport**válassza **speciális**. 
       1. Az a **konfigurálása a hálózati biztonsági csoport** mezőben válassza **új létrehozása**. 
       1. Típus *myNetworkSecurityGroup*, és válassza ki **OK**.

   - Ahhoz, hogy a virtuális Gépet a terheléselosztó háttérkészletéhez tartozik, a következő lépéseket:
        - A **Load Balancing**, a **helyezze a virtuális gép egy meglévő terheléselosztási megoldás mögött?** válassza **Igen**.
        - A **terheléselosztási beállításokat**, a **terheléselosztási beállítások**válassza **az Azure load balancer**.
        - A **válassza ki a terheléselosztó**, *myLoadBalancer*. 
1. Válassza ki a **felügyeleti** lapon, vagy válasszon **tovább** > **felügyeleti**. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.   
1. Tekintse át a beállításokat, és válassza ki **létrehozás**.
1. A lépésekkel hozzon létre két további virtuális gépek – *myVM2* és *myVM3*, a Standard Termékváltozat nyilvános IP-cím a **rendelkezésre állási zónában** **2** és **3** , és a többi beállítás ugyanaz, mint *myVM1*.  

### <a name="create-network-security-group-rule"></a>Hálózati biztonsági csoportszabály létrehozása

Ebben a szakaszban hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt HTTP-n keresztül érkező bejövő kapcsolatok engedélyezéséhez.

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd az erőforrások listájában kattintson **myNetworkSecurityGroup** a található**myResourceGroupSLB** erőforráscsoportot.
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
4. Válassza a **Hozzáadás** lehetőséget.

### <a name="install-iis-on-vms"></a>Az IIS telepítése virtuális gépekre

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd az erőforrások listájában kattintson **myVM1** található a  *myResourceGroupSLB* erőforráscsoportot.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. A **Csatlakozás virtuális géphez** előugró ablakban válassza az **RDP-fájl letöltése** lehetőséget, majd nyissa meg a letöltött RDP-fájlt.
4. A **Távoli asztali kapcsolat** ablakban kattintson a **Csatlakozás** gombra.
5. Jelentkezzen be a virtuális gépre az adott virtuális gép létrehozásakor megadott hitelesítő adatokkal. Ez elindít egy távoli asztali munkamenetet a *myVM1* virtuális géppel.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Windows PowerShell** elemre.
7. A PowerShell-ablakban futtassa az alábbi parancsokat az IIS-kiszolgáló telepítéséhez, távolítsa el az alapértelmezett iisstart.htm fájlt, majd adjon hozzá egy új iisstart.htm fájt, amely megjeleníti a virtuális gép nevét:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Szüntesse meg a távoli asztali munkamenetet a *myVM1*-gyel.
7. Az 1–6. lépés megismétlésével telepítse az IIS-t és a frissített iisstart.htm fájlt a *myVM2* és *myVM3* gépeken.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, és kattintson a **myPublicIP**.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

      ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Az alkalmazást futtató három virtuális gép között osztja el a forgalmat a terheléselosztó megtekintéséhez, kényszerített frissítését a webböngészőt.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Virtuális gépek háttérkészletből való eltávolítása vagy hozzáadása
Előfordulhat, hogy karbantartás kell végeznie az alkalmazást futtató virtuális gépeken (például operációsrendszer-frissítést kell telepítenie). Az alkalmazás megnövekedett forgalmának kezeléséhez szükség lehet további virtuális gépek hozzáadására. Ez a szakasz bemutatja, hogyan távolítsa el, vagy adjon hozzá egy virtuális Gépet (*myVM1*) a terheléselosztóból.

### <a name="remove-vm-from-a-backend-pool"></a>Virtuális gép eltávolítása egy háttérkészlet
Az eltávolítandó *myVM1* a háttérkészlet, a következő lépéseket:

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, és kattintson a **myLoadBalancer** az erőforrások listájában.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a háttérkészlet listájában kattintson a **myBackendPool** elemre.
3. Az a **myBackendPool** eltávolításához *VM1* kattintson a Törlés ikonra a sor végén található *myVM1*, és kattintson a **mentése**.

Most, hogy a *myVM1* már nincs a címkészletben, bármilyen karbantartási feladatot elvégezhet a *myVM1* gépen, például telepíthet szoftverfrissítéseket. Hiányában *VM1*, a betöltés most kiegyensúlyozását *myVM2* és *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Virtuális gép egy háttérkészlet hozzáadása
Hozzáadandó *myVM1* vissza a háttérkészlet, a következő lépéseket:

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd válassza ki **myVM1** az erőforrások listájában.
2. Az a **VM1** lap **beállítások**válassza **hálózatkezelés**.
3. Az a **hálózatkezelés** lapon válassza ki a **terheléselosztás** lapra, és válassza ki **hozzáadása a terheléselosztás**.
4. Az a **hozzáadása a terheléselosztás** lapon, tegye a következőket:
   1. A **terheléselosztási beállítások**válassza **az Azure load balancer**.
   2. A **válassza ki a terheléselosztó**válassza *myLoadBalancer*.
   3. A **válassza ki a háttérkészlet**válassza *myBackendPool*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, törölje az erőforráscsoportot, terheléselosztó és minden kapcsolódó erőforrás. Ehhez válassza ki a *myResouceGroupSLB* erőforráscsoportot, amely tartalmazza a terheléselosztóhoz, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Standard Load Balancer, virtuális gépeket csatolt hozzá, a terheléselosztó forgalmának szabályát, az állapotadat-mintavétel konfigurálva, majd tesztelte a terheléselosztót. Emellett eltávolított egy virtuális gépet az elosztott terhelésű készletből, és hozzáadta a virtuális gépet a háttércímkészlethez. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
