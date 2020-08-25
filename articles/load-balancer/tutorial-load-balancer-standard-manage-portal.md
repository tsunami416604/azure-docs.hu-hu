---
title: 'Oktatóanyag: internetes forgalom terheléselosztása virtuális gépekre – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató ismerteti, hogyan hozhat létre és felügyelhet egy Standard Load Balancert az Azure Portalon.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "80240358"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Oktatóanyag: a virtuális gépek internetes forgalmának terheléselosztása a Azure Portal használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ebben az oktatóanyagban megismerheti az Azure-standard Load Balancer különböző összetevőit, amelyek az internetes forgalmat virtuális gépekre osztják szét, és magas rendelkezésre állást biztosítanak. Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * Azure Load Balancer létrehozása
> * Load Balancer erőforrások létrehozása
> * Virtuális gépek létrehozása és IIS-kiszolgáló telepítése
> * Load Balancer megtekintése működés közben
> * Virtuális gépek hozzáadása és eltávolítása Load Balancer

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban egy olyan standard Load Balancer hoz létre, amely a virtuális gépek terheléselosztását segíti. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Amikor létrehoz egy Standard Load Balancert, létre kell hoznia egy új standard nyilvános IP-címet is, amely a Standard Load Balancer előtereként van konfigurálva (alapértelmezés szerint *LoadBalancerFrontend* néven). 

1. A képernyő bal felső részén kattintson az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer**lehetőségre.
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a *myresourcegroupslb erőforráscsoportban* szöveget a szövegmezőbe.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | Válassza a **standard**lehetőséget.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím              | Írja be a *myPublicIP* szöveget a szövegmezőbe.   |
    |A rendelkezésre állási zóna| Válassza ki a **felesleges zónát**.    |

3. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás**gombra.

   ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer erőforrások létrehozása

Ebben a szakaszban a háttérbeli címkészlet Load Balancer beállításait, az állapot-mintavételt és a Balancer-szabályt adja meg.

### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

A virtuális gépek felé irányuló forgalom elosztásához a háttérbeli címkészlet tartalmazza a Load Balancerhoz csatlakozó virtuális hálózati adapterek IP-címeit. Hozza létre a háttér-címkészlet *myBackendPool* , hogy a virtuális gépeket a terheléselosztási internetes forgalom számára is tartalmazza.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd kattintson a **myLoadBalancer** elemre az erőforrások listából.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a **Hozzáadás** gombra.
3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a *myBackendPool*nevet a háttérbeli készlet neveként, majd válassza a **Hozzáadás**lehetőséget.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ha engedélyezni szeretné a Load Balancer számára az alkalmazás állapotának figyelését, az állapot-mintavételt kell használnia. Az állapotfigyelő szolgáltatás dinamikusan adja hozzá vagy távolítja el a virtuális gépeket az Load Balancer forgásból az állapot-ellenőrzésekre adott válasz alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd kattintson a **myLoadBalancer** elemre az erőforrások listából.
2. A **Beállítások** területen kattintson az **Állapotminták** elemre, majd a **Hozzáadás** gombra.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
     
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a *myHealthProbe*. |
    | Protokoll | Válassza a **http**lehetőséget. |
    | Port | Adja meg a *80*értéket.|
    | Időköz | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a *15* értéket. |
    | Nem kifogástalan állapot küszöbértéke | Válassza a *2* értéket a nem megfelelő **állapotú küszöbértékek** vagy egymást követő mintavételi hibák számának megadásához, amelyeknek a virtuális gép nem megfelelő állapotba kell kerülnie.|
    
4. Kattintson az **OK** gombra.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy Load Balancer szabályt, amely a 80-es portot figyeli a *myloadbalancerruleweb nevű terheléselosztási* , és a *myBackEndPool* -porton keresztül az 80-es portot használja a háttérbeli *FrontendLoadBalancer* .

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd kattintson a **myLoadBalancer** elemre az erőforrások listából.
2. A **Beállítások** területen kattintson a **Terheléselosztási szabályok** elemre, majd a **Hozzáadás** gombra.
3. Használja ezeket az értékeket a terheléselosztási szabály konfigurálásához:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a *: myhttprule*. |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a *80*értéket.|
    | Háttérport | Adja meg a *80*értéket. |
    | A háttérkészlet | Válassza a *myBackendPool*lehetőséget.|
    | Állapotadat-mintavétel | Válassza a *myHealthProbe*lehetőséget. |
    
4. Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, három virtuális gépet hoz létre a Load Balancer háttér-készletéhez, majd az IIS-t telepíti a virtuális gépekre a Load Balancer teszteléséhez.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | Myresourcegroupslb erőforráscsoportban (meglévő erőforráscsoport kiválasztása) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Nyugat-Európa      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A standard Load Balancer csak a standard IP-címmel rendelkező virtuális gépeket támogatja a háttér-készletben. Ebben a szakaszban három virtuális gépet hoz létre (*myVM1*, *myVM2*és *myVM3*) egy szabványos nyilvános IP-címmel három különböző zónában (*1. zóna*, *2. zóna*és *3. zóna*), amelyek a korábban létrehozott Standard Load Balancer háttér-készletéhez lesznek hozzáadva.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítás**  >  **Windows Server 2016 Datacenter**elemet. 
   
1. A **virtuális gép létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myresourcegroupslb erőforráscsoportban**lehetőséget.
   - **Példány részletei**  >  **Virtuális gép neve**: Type *myVM1*.
   - **Példány részletei**  >  **Régió** > válassza a **Nyugat-Európa**lehetőséget.
   - **Példány részletei**  >  **Rendelkezésre állási beállítások** > válassza a **rendelkezésre állási zónák**lehetőséget. 
   - **Példány részletei**  >  **Rendelkezésre állási zóna** > válassza az **1**elemet.
  
1. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget. 
   
   - Győződjön meg arról, hogy a következők vannak kiválasztva:
       - **Virtuális hálózat**: **myVnet**
       - **Alhálózat**: **myBackendSubnet**
       - **Nyilvános IP-** > válassza az **új létrehozása**lehetőséget, majd a **nyilvános IP-cím létrehozása** ablakban az **SKU**, a **standard**és a **rendelkezésre állási zóna**elemet, válassza a **zóna – redundáns** lehetőséget.
      
   - Új hálózati biztonsági csoport (NSG) létrehozásához, amely egy tűzfal típus, a **hálózati biztonsági csoport**területen válassza a **speciális**lehetőséget. 
       1. A **hálózati biztonsági csoport konfigurálása** mezőben válassza az **új létrehozása**lehetőséget. 
       1. Írja be a *myNetworkSecurityGroup*, majd kattintson **az OK gombra**.

   - Ha a virtuális gépet a Load Balancer backend-készletének részévé szeretné tenni, hajtsa végre a következő lépéseket:
        - A **terheléselosztásban**a **virtuális gép egy meglévő terheléselosztási megoldás mögé való helyezéséhez**válassza az **Igen**lehetőséget.
        - A terheléselosztási **Beállítások** **között a terheléselosztási beállítások területen**válassza az **Azure Load Balancer**elemet.
        - A **Load Balancer kiválasztásához** *myLoadBalancer*. 
1. Válassza a **kezelés** lapot, vagy válassza a **következő**  >  **kezelés**lehetőséget. A **figyelés**területen **kapcsolja ki**a **rendszerindítási diagnosztika** beállítást. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.   
1. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.
1. A lépéseket követve hozzon létre két további virtuális gépet – *myVM2* és *MYVM3*, standard SKU nyilvános IP-címmel a **2** . és **3** . **rendelkezésre állási zónában** , és az összes többi beállítás ugyanaz, mint a *myVM1*.  

### <a name="create-network-security-group-rule"></a>Hálózati biztonsági csoportszabály létrehozása

Ebben a szakaszban egy hálózati biztonsági csoportra vonatkozó szabályt hoz létre, amely engedélyezi a bejövő kapcsolatokat a HTTP használatával.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myNetworkSecurityGroup** , amely a **myresourcegroupslb erőforráscsoportban** erőforráscsoport területén található.
2. A **Beállítások** területen kattintson a **Bejövő biztonsági szabályok** lehetőségre, majd a **Hozzáadás** gombra.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - Név: *myHTTPRule*
    - *Http engedélyezése* – Leírás
4. Válassza a **Hozzáadás** elemet.

### <a name="install-iis-on-vms"></a>Az IIS telepítése virtuális gépekre

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myVM1** , amely a *myresourcegroupslb erőforráscsoportban* erőforráscsoport területén található.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. A **Csatlakozás virtuális géphez** előugró ablakban válassza az **RDP-fájl letöltése** lehetőséget, majd nyissa meg a letöltött RDP-fájlt.
4. A **Távoli asztali kapcsolat** ablakban kattintson a **Csatlakozás** gombra.
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
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd kattintson a **myPublicIP**elemre.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

      ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha szeretné megtekinteni a Load Balancer forgalmat az alkalmazást futtató három virtuális gép között, akkor kényszerítheti a webböngésző frissítését.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Virtuális gépek háttérkészletből való eltávolítása vagy hozzáadása
Előfordulhat, hogy karbantartás kell végeznie az alkalmazást futtató virtuális gépeken (például operációsrendszer-frissítést kell telepítenie). Az alkalmazás megnövekedett forgalmának kezeléséhez szükség lehet további virtuális gépek hozzáadására. Ebből a szakaszból megtudhatja, hogyan távolíthat el vagy adhat hozzá virtuális gépeket (*myVM1*) a Load Balancer.

### <a name="remove-vm-from-a-backend-pool"></a>Virtuális gép eltávolítása egy háttér-készletből
A *myVM1* a háttér-készletből való eltávolításához hajtsa végre a következő lépéseket:

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd kattintson a **myLoadBalancer** elemre az erőforrások listából.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a háttérkészlet listájában kattintson a **myBackendPool** elemre.
3. A **myBackendPool** lapon a *VM1* eltávolításához jelölje ki a *myVM1*megjelenítő sor végén található törlés ikont, majd kattintson a **Mentés**gombra.

Most, hogy a *myVM1* már nincs a címkészletben, bármilyen karbantartási feladatot elvégezhet a *myVM1* gépen, például telepíthet szoftverfrissítéseket. A *VM1*hiányában a terhelés most már a *MyVM2* és a *myVM3*között egyensúlyban van. 

### <a name="add-vm-to-a-backend-pool"></a>Virtuális gép hozzáadása egy háttérbeli készlethez
Ha vissza szeretné adni a *myVM1* a háttér-készlethez, hajtsa végre a következő lépéseket:

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myVM1** lehetőséget.
2. A **VM1** lap **Beállítások**területén válassza a **hálózatkezelés**lehetőséget.
3. A **hálózatkezelés** **lapon válassza a terheléselosztás fület** , majd kattintson a **terheléselosztás hozzáadása**lehetőségre.
4. A **terheléselosztás hozzáadása** lapon tegye a következőket:
   1. A terheléselosztási **beállításoknál**válassza az **Azure Load Balancer**lehetőséget.
   2. A **Load Balancer kiválasztásához**válassza a *myLoadBalancer*lehetőséget.
   3. A **háttérbeli készlet kiválasztásához**válassza a *myBackendPool*lehetőséget. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az erőforráscsoportot, Load Balancer és az összes kapcsolódó erőforrást. Ehhez válassza ki a Load Balancer tartalmazó *myResouceGroupSLB* erőforráscsoportot, majd válassza a  **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy standard Load Balancer, csatlakoztatott virtuális gépeket, konfigurálta az Load Balancer forgalmi szabályt, az állapot-mintavételt, majd tesztelte a Load Balancer. Emellett eltávolított egy virtuális gépet az elosztott terhelésű készletből, és hozzáadta a virtuális gépet a háttércímkészlethez. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
