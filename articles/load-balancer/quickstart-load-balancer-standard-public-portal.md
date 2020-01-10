---
title: 'Gyors útmutató: standard Load Balancer létrehozása Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre standard Load Balancer a Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 027e05b3fbf7163c4a1b927a2b83db84c7eef1ff
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771461"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rövid útmutató: Standard terheléselosztó létrehozása a virtuális gépek terhelésének elosztásához az Azure Portalon

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Az Azure Portal használatával létrehozhat egy terheléselosztót a virtuális gépek (VM-ek) terhelésének elosztása érdekében. Ez a rövid útmutató bemutatja, hogyan végezheti el a virtuális gépek terheléselosztását egy Standard Load Balancer használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban egy olyan standard Load Balancer hoz létre, amely a virtuális gépek terheléselosztását segíti. Létrehozhat egy nyilvános standard Load Balancer vagy egy belső standard Load Balancer is. Standard Load Balancer csak a standard nyilvános IP-címet támogatja, az alapszintű nyilvános IP-címek nem támogatottak. Ha nyilvános standard Load Balancer hoz létre, és létre kell hoznia egy új, szabványos nyilvános IP-címet is, amely a standard Load Balancer számára a előtérként van konfigurálva (alapértelmezés szerint *LoadBalancerFrontend* néven). 

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **Load Balancer**lehetőséget.
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Value (Díj)                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a *myresourcegroupslb erőforráscsoportban* szöveget a szövegmezőbe.|
    | Név                   | *myLoadBalancer*                                   |
    | Region (Régió)         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Type (Típus)          | Válassza a **Nyilvános** lehetőséget.                                        |
    | SKU (Cikkszám)           | Válassza a **standard**lehetőséget.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím              | Írja be a *myPublicIP* szöveget a szövegmezőbe.   |
    |Rendelkezésre állási zóna| Válassza ki a **felesleges zónát**.    |
3. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.   

    ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer erőforrások létrehozása

Ebben a szakaszban a háttérbeli címkészlet Load Balancer beállításait, az állapot-mintavételt és a Balancer-szabályt adja meg.

### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

A virtuális gépek felé irányuló forgalom elosztásához a háttérbeli címkészlet tartalmazza a Load Balancerhoz csatlakozó virtuális hálózati adapterek IP-címeit. Hozza létre a háttér-címkészlet *myBackendPool* , hogy a virtuális gépeket a terheléselosztási internetes forgalom számára is tartalmazza.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza a **háttér-készletek**, majd a **Hozzáadás**lehetőséget.
3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a *myBackendPool*nevet a háttérbeli készlet neveként, majd válassza a **Hozzáadás**lehetőséget.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ha engedélyezni szeretné a Load Balancer számára az alkalmazás állapotának figyelését, az állapot-mintavételt kell használnia. Az állapotfigyelő szolgáltatás dinamikusan adja hozzá vagy távolítja el a virtuális gépeket az Load Balancer forgásból az állapot-ellenőrzésekre adott válasz alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza az **állapot**-mintavételek, majd a **Hozzáadás**lehetőséget.
    
    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *myHealthProbe*. |
    | Protocol (Protokoll) | Válassza a **http**lehetőséget. |
    | Port | Adja meg a *80*értéket.|
    | Intervallum | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a *15* értéket. |
    | Nem kifogástalan állapot küszöbértéke | Válassza a **2** értéket a nem megfelelő **állapotú küszöbértékek** vagy egymást követő mintavételi hibák számának megadásához, amelyeknek a virtuális gép nem megfelelő állapotba kell kerülnie.|
    | | |
4. Kattintson az **OK** gombra.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy Load Balancer szabályt, amely a 80-es portot figyeli a *myloadbalancerruleweb nevű terheléselosztási* , és a *myBackEndPool* -porton keresztül az 80-es portot használja a háttérbeli *FrontendLoadBalancer* . 

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza a terheléselosztási **szabályok**lehetőséget, majd kattintson a **Hozzáadás**gombra.
3. Konfigurálja a terheléselosztási szabályt az alábbi értékekkel:
    
    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *: myhttprule*. |
    | Protocol (Protokoll) | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a *80*értéket.|
    | Háttérport | Adja meg a *80*értéket. |
    | Háttérkészlet | Válassza a *myBackendPool*lehetőséget.|
    | Állapotadat-mintavétel | Válassza a *myHealthProbe*lehetőséget. |
4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.


## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, három virtuális gépet hoz létre a Load Balancer háttér-készletéhez, majd az IIS-t telepíti a virtuális gépekre a Load Balancer teszteléséhez.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget.

1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *myVNet* értéket. |
    | Címtér | Adja meg a *10.1.0.0/16*értéket. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válasszon ki egy meglévő erőforrás- *myresourcegroupslb erőforráscsoportban*. |
    | Földrajzi egység | Válassza a **Nyugat-Európa** régiót.|
    | Alhálózat – név | Adja meg a *myBackendSubnet* értéket. |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24*értéket. |
1. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Létrehozás**lehetőséget.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
A standard Load Balancer csak a standard IP-címmel rendelkező virtuális gépeket támogatja a háttér-készletben. Ebben a szakaszban három virtuális gépet hoz létre (*myVM1*, *myVM2* és *myVM3*) egy szabványos nyilvános IP-címmel három különböző zónában (*1. zóna*, *2. zóna*és *3. zóna*), amelyek később a korábban létrehozott Standard Load Balancer háttér-készletéhez lesznek hozzáadva.

1. A portál bal felső részén válassza az **erőforrás létrehozása** > **számítás** > **Windows Server 2019 Datacenter**lehetőséget. 
   
1. A **virtuális gép létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés** > **erőforráscsoporthoz**: válassza a **myresourcegroupslb erőforráscsoportban**lehetőséget.
   - **Példány részletei** > **virtuális gép neve**: Type *myVM1*.
   - **Példány részletei** > **régió** > válassza a **Nyugat-Európa**lehetőséget.
   - A **példány részletei** > **rendelkezésre állási lehetőségek** > válassza a **rendelkezésre állási zónák**lehetőséget. 
   - A **példány részletei** > **rendelkezésre állási zónában** > válassza az **1**elemet.
   - **Rendszergazdai fiók**> adja meg a **felhasználónevet**, a **jelszót** és a **Jelszó megerősítése** információt.
   - Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.
  
1. A **hálózatkezelés** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: *myVnet*
   - **Alhálózat**: *myBackendSubnet*
   - **Nyilvános IP-** > válassza az **új létrehozása**lehetőséget, majd a **nyilvános IP-cím létrehozása** ablakban az **SKU**, a **standard**és a **rendelkezésre állási zóna**elemet, válassza a **zóna – redundáns**lehetőséget, majd kattintson **az OK gombra**.
   - Új hálózati biztonsági csoport (NSG) létrehozásához, amely egy tűzfal típus, a **hálózati biztonsági csoport**területen válassza a **speciális**lehetőséget. 
       1. A **hálózati biztonsági csoport konfigurálása** mezőben válassza az **új létrehozása**lehetőséget. 
       1. Írja be a *myNetworkSecurityGroup*, majd kattintson **az OK gombra**.
   - Ha a virtuális gépet a Load Balancer backend-készletének részévé szeretné tenni, hajtsa végre a következő lépéseket:
        - A **terheléselosztásban**a **virtuális gép egy meglévő terheléselosztási megoldás mögé való helyezéséhez**válassza az **Igen**lehetőséget.
        - A terheléselosztási **Beállítások** **között a terheléselosztási beállítások területen**válassza az **Azure Load Balancer**elemet.
        - A **Load Balancer kiválasztásához** *myLoadBalancer*.
        - Válassza a **kezelés** lapot, vagy válassza a **következő** > **felügyelet**lehetőséget.
2. A felügyelet **lap** **figyelés**területén a **rendszerindítási diagnosztika** beállítása **kikapcsolva**értékre. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.   
1. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.
1. A 2 – 6. lépés végrehajtásával hozzon létre két további virtuális gépet a következő értékekkel, és az összes többi olyan beállítást, amely ugyanaz, mint a *myVM1*:

    | Beállítás | VM 2| 3\. VIRTUÁLIS GÉP|
    | ------- | ----- |---|
    | Név |  *myVM2* |*myVM3*|
    | Rendelkezésre állási zóna | 2 |3|
    |Nyilvános IP-cím| **Standard szintű** SKU|**Standard szintű** SKU|
    | Nyilvános IP-cím rendelkezésre állási zónája| **Felesleges zóna** |**Felesleges zóna**|
    | Hálózati biztonsági csoport | A meglévő *myNetworkSecurity-csoport* kiválasztása| A meglévő *myNetworkSecurity-csoport* kiválasztása|

 ### <a name="create-nsg-rule"></a>NSG-szabály létrehozása

Ebben a szakaszban egy hálózati biztonsági csoportra vonatkozó szabályt hoz létre, amely engedélyezi a bejövő kapcsolatokat a HTTP használatával.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza ki a **myNetworkSecurityGroup** , amely a **myresourcegroupslb erőforráscsoportban** erőforráscsoport területén található.
2. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet, majd a **Hozzáadás** lehetőséget.
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
 
### <a name="install-iis"></a>Az IIS telepítése

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza ki a **myVM1** , amely a *myresourcegroupslb erőforráscsoportban* erőforráscsoporthoz található.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
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

## <a name="test-the-load-balancer"></a>A Load Balancer tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd válassza a **myPublicIP**lehetőséget.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha szeretné megtekinteni a Load Balancer a forgalom elosztása mindhárom virtuális gépen, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett oldalát, majd kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, Load Balancer és az összes kapcsolódó erőforrást. Ehhez válassza ki azt az erőforráscsoportot (*myresourcegroupslb erőforráscsoportban*), amely a Load Balancer tartalmazza, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy standard Load Balancer, csatlakoztatott virtuális gépeket, konfigurálta az Load Balancer forgalmi szabályt, az állapot-mintavételt, majd tesztelte a Load Balancer. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
