---
title: 'Gyors útmutató: nyilvános Load Balancer létrehozása – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Load Balancer a Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 99a2de4cd8a19d3f05b9dc37f3bcd08cd84b2e68
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052746"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Gyors útmutató: Load Balancer létrehozása a virtuális gépek terheléselosztásához a Azure Portal használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Az Azure Portal használatával létrehozhat egy terheléselosztót a virtuális gépek (VM-ek) terhelésének elosztása érdekében. Ez a rövid útmutató bemutatja, hogyan lehet terheléselosztást alkalmazni a virtuális gépeket egy nyilvános Load Balancer használatával.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-load-balancer"></a>Load Balancer-terheléselosztó létrehozása

Ebben a szakaszban egy olyan Load Balancer hoz létre, amely a virtuális gépek terheléselosztását segíti. Létrehozhat egy nyilvános Load Balancer vagy egy belső Load Balancer is. Amikor nyilvános Load Balancer hoz létre, létre kell hoznia egy új nyilvános IP-címet is, amely a Load Balancer számára a (alapértelmezés szerint *LoadBalancerFrontend* ) előtérben van konfigurálva.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer**elemet.
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki az előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a *myresourcegroupslb erőforráscsoportban* szöveget a szövegmezőbe.|
    | Name                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | Válassza a **standard** vagy az **alapszintű**lehetőséget. A Microsoft a standard szintű üzemi számítási feladatokhoz ajánlja. |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. Ha meglévő nyilvános IP-címmel szeretne használni, válassza a **meglévő használata** lehetőséget. |
    | Nyilvános IP-cím              | Írja be a *myPublicIP* szöveget a szövegmezőbe.   ```-SKU Basic```Alapszintű nyilvános IP-cím létrehozásához használja. Az alapszintű nyilvános IP-címek nem kompatibilisek a **standard** Load balancerrel. A Microsoft a **standard szintű** használatot javasolja a termelési munkaterhelésekhez.|
    | Rendelkezésreállási zóna | Rugalmas Load Balancer létrehozásához írja be a következőt: *Zone-redundáns* . A zóna Load Balancer létrehozásához válasszon ki egy 1, 2 vagy 3 zónát egy adott zónából |

> [!IMPORTANT]
> A rövid útmutató további része azt feltételezi, hogy a **standard** SKU a fenti SKU kiválasztási folyamat során lett kiválasztva.


3. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.   

    ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer erőforrások létrehozása

Ebben a szakaszban a háttérbeli címkészlet Load Balancer beállításait, az állapot-mintavételt és a Balancer-szabályt adja meg.

### <a name="create-a-backend-pool"></a>Háttérbeli készlet létrehozása

A virtuális gépek felé irányuló forgalom elosztásához a háttérbeli címkészlet tartalmazza a Load Balancerhoz csatlakozó virtuális hálózati adapterek IP-címeit. Hozza létre a háttér-címkészlet *myBackendPool* , hogy a virtuális gépeket a terheléselosztási internetes forgalom számára is tartalmazza.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza a **háttér-készletek**, majd a **Hozzáadás**lehetőséget.
3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a *myBackendPool*nevet a háttérbeli készlet neveként, majd válassza a **Hozzáadás**lehetőséget.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ha engedélyezni szeretné a Load Balancer számára az alkalmazás állapotának figyelését, az állapot-mintavételt kell használnia. Az állapotfigyelő szolgáltatás dinamikusan adja hozzá vagy távolítja el a virtuális gépeket az Load Balancer forgásból az állapot-ellenőrzésekre adott válasz alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza az **állapot**-mintavételek, majd a **Hozzáadás**lehetőséget.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *myHealthProbe*. |
    | Protokoll | Válassza a **http**lehetőséget. |
    | Port | Adja meg a *80*értéket.|
    | Intervallum | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a *15* értéket. |
    | Nem kifogástalan állapot küszöbértéke | Válassza a **2** értéket a nem megfelelő **állapotú küszöbértékek** vagy egymást követő mintavételi hibák számának megadásához, amelyeknek a virtuális gép nem megfelelő állapotba kell kerülnie.|
    | | |
4. Válassza az **OK** lehetőséget.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy Load Balancer szabályt, amely a 80-es portot figyeli a *myloadbalancerruleweb nevű terheléselosztási* , és a *myBackEndPool* -porton keresztül az 80-es portot használja a háttérbeli *FrontendLoadBalancer* . 

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza a terheléselosztási **szabályok**lehetőséget, majd kattintson a **Hozzáadás**gombra.
3. Konfigurálja a terheléselosztási szabályt az alábbi értékekkel:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *: myhttprule*. |
    | Protokoll | Válassza a **TCP**lehetőséget. |
    | Port | Adja meg a *80*értéket.|
    | Háttér-port | Adja meg a *80*értéket. |
    | A háttérkészlet | Válassza a *myBackendPool*lehetőséget.|
    | Állapotadat-mintavétel | Válassza a *myHealthProbe*lehetőséget. |
4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.


## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, három virtuális gépet hoz létre a Load Balancer háttér-készletéhez, majd az IIS-t telepíti a virtuális gépekre a Load Balancer teszteléséhez.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | Myresourcegroupslb erőforráscsoportban |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Nyugat-Európa      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
A nyilvános IP-címek és a Load Balancer SKU-nak egyezniük kell. Standard Load Balancer esetén a háttér-készletben használjon szabványos IP-címmel rendelkező virtuális gépeket. Ebben a szakaszban három virtuális gépet hoz létre (*myVM1*, *myVM2* és *myVM3*) egy szabványos nyilvános IP-címmel három különböző zónában (*1. zóna*, *2. zóna*és *3. zóna*), amelyek később a korábban létrehozott Load Balancer háttér-készletéhez lesznek hozzáadva. Ha az alapszintű lehetőséget választotta, használja az alapszintű IP-címmel rendelkező virtuális gépeket.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítás**  >  **Windows Server 2019 Datacenter**elemet. 
   
1. A **virtuális gép létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myresourcegroupslb erőforráscsoportban**lehetőséget.
   - **Példány részletei**  >  **Virtuális gép neve**: Type *myVM1*.
   - **Példány részletei**  >  **Régió** > válassza a **Nyugat-Európa**lehetőséget.
   - **Példány részletei**  >  **Rendelkezésre állási beállítások** > válassza a **rendelkezésre állási zónák**lehetőséget. 
   - **Példány részletei**  >  **Rendelkezésre állási zóna** > válassza az **1**elemet.
   - **Rendszergazdai fiók**> adja meg a **felhasználónevet**, a **jelszót** és a **Jelszó megerősítése** információt.
   - Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.
  
1. A **hálózatkezelés** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: *myVnet*
   - **Alhálózat**: *myBackendSubnet*
   - **Nyilvános IP-** > válassza az **új létrehozása**lehetőséget, majd a **nyilvános IP-cím létrehozása** ablakban az **SKU**, a **standard**és a **rendelkezésre állási zóna**elemet, válassza a **zóna – redundáns**lehetőséget, majd kattintson **az OK gombra**. Ha alapszintű Load Balancer hozott létre, válassza az alapszintű lehetőséget. A Microsoft a standard SKU használatát javasolja üzemi számítási feladatokhoz.
   - Új hálózati biztonsági csoport (NSG) létrehozásához, amely egy tűzfal típus, a **hálózati biztonsági csoport**területen válassza a **speciális**lehetőséget. 
       1. A **hálózati biztonsági csoport konfigurálása** mezőben válassza az **új létrehozása**lehetőséget. 
       1. Írja be a *myNetworkSecurityGroup*, majd kattintson **az OK gombra**.
   - Ha a virtuális gépet a Load Balancer backend-készletének részévé szeretné tenni, hajtsa végre a következő lépéseket:
        - A **terheléselosztásban**a **virtuális gép egy meglévő terheléselosztási megoldás mögé való helyezéséhez**válassza az **Igen**lehetőséget.
        - A terheléselosztási **Beállítások** **között a terheléselosztási beállítások területen**válassza az **Azure Load Balancer**elemet.
        - A **Load Balancer kiválasztásához** *myLoadBalancer*.
        - Válassza a **kezelés** lapot, vagy válassza a **következő**  >  **kezelés**lehetőséget.
2. A felügyelet **lap** **figyelés**területén a **rendszerindítási diagnosztika** beállítása **kikapcsolva**értékre. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.   
1. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.
1. A 2 – 6. lépés végrehajtásával hozzon létre két további virtuális gépet a következő értékekkel, és az összes többi olyan beállítást, amely ugyanaz, mint a *myVM1*:

    | Beállítás | VM 2| 3. VIRTUÁLIS GÉP|
    | ------- | ----- |---|
    | Name |  *myVM2* |*myVM3*|
    | Rendelkezésreállási zóna | 2 |3|
    |Nyilvános IP-cím| **Standard szintű** SKU|**Standard szintű** SKU|
    | Nyilvános IP-cím rendelkezésre állási zónája| **Felesleges zóna** |**Felesleges zóna**|
    | Hálózati biztonsági csoport | Meglévő *myNetworkSecurityGroup* kiválasztása| Meglévő *myNetworkSecurityGroup* kiválasztása|

 ### <a name="create-nsg-rule"></a>NSG-szabály létrehozása

Ebben a szakaszban egy hálózati biztonsági csoportra vonatkozó szabályt hoz létre, amely engedélyezi a bejövő kapcsolatokat a HTTP használatával.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza ki a **myNetworkSecurityGroup** , amely a **myresourcegroupslb erőforráscsoportban** erőforráscsoport területén található.
2. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet, majd a **Hozzáadás** lehetőséget.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *szolgáltatás címkéje*
    -  **Forrásoldali szolgáltatás címkéje**: *Internet*
    - **Célport tartományai**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - **Név**: *: myhttprule* 
    - **Leírás**: "*http engedélyezése* 
4. Válassza a **Hozzáadás** elemet.
5. Szükség esetén ismételje meg a bejövő RDP-szabály lépéseit a következő eltérő értékekkel:
   - **Célport tartományai**: Type *3389*.
   - **Prioritás**: Type *200*. 
   - **Név**: írja be a *MyRDPRule*nevet. 
   - **Leírás**: írja be az *RDP engedélyezése lehetőséget*. 
 
### <a name="install-iis"></a>Az IIS telepítése

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza ki a **myVM1** , amely a *myresourcegroupslb erőforráscsoportban* erőforráscsoporthoz található.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
5. Jelentkezzen be a virtuális gépre az adott virtuális gép létrehozásakor megadott hitelesítő adatokkal. Ez elindít egy távoli asztali munkamenetet a *myVM1* virtuális géppel.
6. A kiszolgáló asztalán navigáljon a **Windows felügyeleti eszközök** > **Windows PowerShell**elemre.
7. A PowerShell-ablakban futtassa az alábbi parancsokat az IIS-kiszolgáló telepítéséhez, távolítsa el az alapértelmezett iisstart.htm fájlt, majd adjon hozzá egy új iisstart.htm fájt, amely megjeleníti a virtuális gép nevét:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Az RDP-munkamenet lezárása a *myVM1*.
7. Az 1–6. lépés megismétlésével telepítse az IIS-t és a frissített iisstart.htm fájlt a *myVM2* és *myVM3* gépeken.

## <a name="test-the-load-balancer"></a>A Load Balancer tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd válassza a **myPublicIP**lehetőséget.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha szeretné megtekinteni a Load Balancer a forgalom elosztása mindhárom virtuális gépen, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett oldalát, majd kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, Load Balancer és az összes kapcsolódó erőforrást. Ehhez válassza ki azt az erőforráscsoportot (*myresourcegroupslb erőforráscsoportban*), amely a Load Balancer tartalmazza, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy standard Load Balancer, csatlakoztatott virtuális gépeket, konfigurálta az Load Balancer forgalmi szabályt, az állapot-mintavételt, majd tesztelte a Load Balancer. Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a [Azure Load Balancer oktatóanyagokkal](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

További információ a [Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).
