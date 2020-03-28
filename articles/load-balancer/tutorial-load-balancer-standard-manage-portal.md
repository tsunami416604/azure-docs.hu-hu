---
title: 'Oktatóanyag: A virtuális gépek internetes forgalmának terheléselosztása – Az Azure Portal'
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
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80240358"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Oktatóanyag: Az Azure Portal használatával a virtuális gépekinternetes forgalmat terheli

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ebben az oktatóanyagban megismerheti az Azure Standard Load Balancer különböző összetevőit, amelyek internetes forgalmat osztanak a virtuális gépeknek, és magas rendelkezésre állást biztosítanak. Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * Azure-os terheléselosztó létrehozása
> * Terheléselosztó-erőforrások létrehozása
> * Virtuális gépek létrehozása és IIS-kiszolgáló telepítése
> * A terheléselosztó megtekintése működés közben
> * Virtuális gépek hozzáadása és eltávolítása a terheléselosztóból

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban hozzon létre egy standard terheléselosztót, amely segít a virtuális gépek terheléselosztásában. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Amikor létrehoz egy Standard Load Balancert, létre kell hoznia egy új standard nyilvános IP-címet is, amely a Standard Load Balancer előtereként van konfigurálva (alapértelmezés szerint *LoadBalancerFrontend* néven). 

1. A képernyő bal felső részén kattintson az **Erőforrás** > **létrehozása hálózati** > **terheléselosztó**elemre.
2. A **Terheléselosztó létrehozása** lap **Alapjai** lapján adja meg vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd válassza a **Véleményezés + létrehozás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroupSLB* parancsot a szövegmezőbe.|
    | Név                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | SKU           | Válassza a **Normál**lehetőséget.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím              | Írja be a *myPublicIP szöveget* a szövegmezőbe.   |
    |Rendelkezésreállási zóna| Válassza **a Redundáns zóna**lehetőséget.    |

3. A **Véleményezés + létrehozás** lapon kattintson a **Létrehozás gombra.**

   ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Terheléselosztó-erőforrások létrehozása

Ebben a szakaszban konfigurálja a terheléselosztó beállításait egy háttércímkészlethez, egy állapotmintához, és adjon meg egy kiegyensúlyozó szabályt.

### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

A virtuális gépek revonatkozó forgalom elosztásához a háttér-címkészlet tartalmazza a terheléselosztóhoz csatlakoztatott virtuális (NIC) IP-címeket. Hozza létre a *myBackendPool* háttércímkészletet, hogy virtuális gépeket tartalmazzon a terheléselosztáshoz az internetes forgalomhoz.

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd kattintson az erőforrások listájában a **myLoadBalancer** elemre.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a **Hozzáadás** gombra.
3. A **Háttérkészlet hozzáadása** lap névmezőbe írja be a *myBackendPool*nevet a háttérkészlet neveként, majd válassza **a Hozzáadás lehetőséget.**

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Annak engedélyezéséhez, hogy a terheléselosztó figyelhesse az alkalmazás állapotát, használjon állapotmintát. Az állapotminta dinamikusan hozzáadja vagy eltávolítja a virtuális gépeket a terheléselosztó rotációjából az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd kattintson az erőforrások listájában a **myLoadBalancer** elemre.
2. A **Beállítások** területen kattintson az **Állapotminták** elemre, majd a **Hozzáadás** gombra.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
     
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg *a myHealthProbe .* |
    | Protocol (Protokoll) | Válassza a **HTTP**lehetőséget. |
    | Port | Adja meg *a 80*.|
    | Intervallum | Adja meg a *15* értéket a mintavételi kísérletek közötti **időköz** másodpercben. |
    | Nem kifogástalan állapot küszöbértéke | Válassza ki a *2-t* a **nem megfelelő küszöbértékek** vagy egymást követő mintavételi hibák, amelyek a virtuális gép nem megfelelő állapotúnak kell lennie.|
    
4. Válassza **az OK gombot.**

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy terheléselosztó szabályt *myLoadBalancerRuleWeb* a frontend *frontendloadbalancer* 80-as portjának meghallgatásához, és a terheléselosztásos hálózati forgalom küldéséhez a *myBackEndPool* háttércímkészletbe a 80-as porthasználatával is.

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd kattintson az erőforrások listájában a **myLoadBalancer** elemre.
2. A **Beállítások** területen kattintson a **Terheléselosztási szabályok** elemre, majd a **Hozzáadás** gombra.
3. Ezekkel az értékekkel konfigurálhatja a terheléselosztási szabályt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be *a myHTTPRule értéket*. |
    | Protocol (Protokoll) | Válassza a **TCP**lehetőséget. |
    | Port | Adja meg *a 80*.|
    | Háttérablak | Adja meg *a 80*. |
    | A háttérkészlet | Válassza a *myBackendPool lehetőséget.*|
    | Állapotadat-mintavétel | Válassza a *myHealthProbe lehetőséget.* |
    
4. Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban hozzon létre egy virtuális hálózatot, hozzon létre három virtuális gépet a terheléselosztó háttérkészletéhez, majd telepítse az IIS-t a virtuális gépekre a terheléselosztó teszteléséhez.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroupSLB (Meglévő erőforráscsoport kiválasztása) |
| **\<virtuális hálózat neve>** | myVNet          |
| **\<régiónév>**          | Nyugat-Európa      |
| **\<IPv4-címtér>**   | 10.1.0.0/16          |
| **\<alhálózat-név>**          | mySubnet        |
| **\<alhálózati címtartomány>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Standard load Balancer csak támogatja a virtuális gépek standard IP-címekkel a háttérkészletben. Ebben a szakaszban három virtuális gépet *(myVM1*, *myVM2*és *myVM3)* hoz létre standard nyilvános IP-címmel három különböző zónában *(1. zóna,* *2. zóna*és *3. zóna),* amelyek a korábban létrehozott standard terheléselosztó háttérkészletéhez kerülnek.

1. A portál bal felső részén válassza az **Erőforrás** > létrehozása**Számítási** > **Windows Server 2016 Datacenter lehetőséget.** 
   
1. A **Virtuális gép létrehozása**csoportban írja be vagy válassza ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupSLB**lehetőséget.
   - **Példány részletei** > **Virtuális gép neve**: Írja be a *myVM1*nevet.
   - **Példány részletei** > **régió** > válassza **nyugat-európai**lehetőséget.
   - **A példány részleteinek** > **rendelkezésre állási beállításai** > Az **Elérhetőségi zónák**kiválasztása . 
   - **Példány részletei:** > **A példány részleteinek rendelkezésre állási zónája** > **1**kiválasztása .
  
1. Válassza a **Hálózat** lapot, vagy válassza **a Tovább: Lemezek**, majd a **Tovább: Hálózat**lehetőséget. 
   
   - Győződjön meg arról, hogy a következők vannak kiválasztva:
       - **Virtuális hálózat**: **myVnet**
       - **Alhálózat**: **myBackendSubnet**
       - **Nyilvános IP >** válassza **az Új létrehozása**lehetőséget, és a Nyilvános **IP-cím létrehozása** ablakban a **Termékváltozathoz**válassza a **Normál**lehetőséget, a **Rendelkezésre állási zónában**pedig a **Zónaredundáns** lehetőséget.
      
   - Új hálózati biztonsági csoport (NSG) létrehozásához a **Hálózatbiztonsági csoport csoportban**válassza a **Speciális**lehetőséget. 
       1. A **Hálózati biztonsági csoport konfigurálása** mezőben válassza **az Új létrehozása lehetőséget.** 
       1. Írja be a *myNetworkSecurityGroup*parancsot, és válassza **az OK gombot.**

   - Ha azt szeretné, hogy a virtuális gép a terheléselosztó háttérkészletének részévé legyen, hajtsa végre az alábbi lépéseket:
        - A **Terheléselosztás**területen a **Virtuális gépet egy meglévő terheléselosztási megoldás mögé helyezze?** **Yes**
        - A **Terheléselosztás beállításai** **párbeszédpanelterhelés-elosztási beállítások**ban válassza az Azure **terheléselosztó lehetőséget.**
        - A **Load Balancer kiválasztása**esetén a *myLoadBalancer*. 
1. Válassza a **Kezelés** lapot, vagy válassza a **Következő** > **kezelés**lehetőséget. A **Figyelés**csoportban állítsa a **Rendszerindításdiadia -t** **Ki**beállításra. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.   
1. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.**
1. Kövesse a lépéseket, hogy hozzon létre két további virtuális gépek - *myVM2* és *myVM3*, egy szabványos termékváltozat nyilvános IP-címet **a rendelkezésre állási zóna** **2** és **3,** illetve az összes többi beállítás ugyanaz, mint *a myVM1*.  

### <a name="create-network-security-group-rule"></a>Hálózati biztonsági csoportszabály létrehozása

Ebben a szakaszban létrehoz egy hálózati biztonsági csoportszabályt, amely engedélyezi a http-n keresztül imázsú bejövő kapcsolatokat.

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd az erőforrások listájában kattintson a **myResourceGroupSLB** erőforráscsoportban található **myNetworkSecurityGroup** elemre.
2. A **Beállítások** területen kattintson a **Bejövő biztonsági szabályok** lehetőségre, majd a **Hozzáadás** gombra.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - Név: *myHTTPRule*
    - *HTTP engedélyezése* - leíráshoz
4. Válassza a **Hozzáadás** lehetőséget.

### <a name="install-iis-on-vms"></a>Az IIS telepítése virtuális gépekre

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd az erőforrások listájában kattintson a *myResourceGroupSLB* erőforráscsoportban található **myVM1** elemre.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. A **Csatlakozás virtuális géphez** előugró ablakban válassza az **RDP-fájl letöltése** lehetőséget, majd nyissa meg a letöltött RDP-fájlt.
4. A **Távoli asztali kapcsolat** ablakban kattintson a **Csatlakozás** gombra.
5. Jelentkezzen be a virtuális gépre az adott virtuális gép létrehozásakor megadott hitelesítő adatokkal. Ez elindít egy távoli asztali munkamenetet a *myVM1* virtuális géppel.
6. A kiszolgáló asztalán keresse meg a **Windows felügyeleti eszközök**>**a Windows PowerShell ablakot.**
7. A PowerShell-ablakban futtassa az alábbi parancsokat az IIS-kiszolgáló telepítéséhez, távolítsa el az alapértelmezett iisstart.htm fájlt, majd adjon hozzá egy új iisstart.htm fájt, amely megjeleníti a virtuális gép nevét:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Zárja be az RDP-munkamenetet a *myVM1*programmal.
7. Az 1–6. lépés megismétlésével telepítse az IIS-t és a frissített iisstart.htm fájlt a *myVM2* és *myVM3* gépeken.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd kattintson a **MyPublicIP parancsra.**

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

      ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha látni szeretné, hogy a terheléselosztó forgalmat az alkalmazást futtató három virtuális gép között ossza el, kényszerítheti a webböngésző frissítését.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Virtuális gépek háttérkészletből való eltávolítása vagy hozzáadása
Előfordulhat, hogy karbantartás kell végeznie az alkalmazást futtató virtuális gépeken (például operációsrendszer-frissítést kell telepítenie). Az alkalmazás megnövekedett forgalmának kezeléséhez szükség lehet további virtuális gépek hozzáadására. Ez a szakasz bemutatja, hogyan távolíthatja el vagy adhat hozzá virtuális gép *(myVM1)* a terheléselosztó.

### <a name="remove-vm-from-a-backend-pool"></a>Virtuális gép eltávolítása háttérkészletből
Ha el szeretné távolítani a *myVM1-et* a háttérkészletből, hajtsa végre az alábbi lépéseket:

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd kattintson az erőforrások listájában a **myLoadBalancer** elemre.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a háttérkészlet listájában kattintson a **myBackendPool** elemre.
3. A **myBackendPool** lapon a *VM1* eltávolításához jelölje ki a törlés ikont a *myVM1-et*megjelenítő sor végén, majd kattintson a **Mentés gombra.**

Most, hogy a *myVM1* már nincs a címkészletben, bármilyen karbantartási feladatot elvégezhet a *myVM1* gépen, például telepíthet szoftverfrissítéseket. *VM1*hiányában a terhelés most már kiegyensúlyozott a *myVM2* és *a myVM3 között.* 

### <a name="add-vm-to-a-backend-pool"></a>Virtuális gép hozzáadása háttérkészlethez
Ha vissza szeretné adni a *myVM1-et* a háttérkészlethez, hajtsa végre az alábbi lépéseket:

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myVM1** elemet az erőforrások listájából.
2. A **VM1** lap **Beállítások**területén válassza a **Hálózat**lehetőséget.
3. A **Hálózat lapon** válassza a **Terheléselosztás** lapot, majd a **Terheléselosztás hozzáadása**lehetőséget.
4. A **Terheléselosztás hozzáadása** lapon tegye a következőket:
   1. A **terheléselosztási beállításokban**válassza az **Azure terheléselosztó lehetőséget.**
   2. A **Terheléselosztó kiválasztása**csoportban válassza a *myLoadBalancer lehetőséget.*
   3. A **Háttérkészlet kiválasztása**csoportban válassza a *myBackendPool lehetőséget.* 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Ehhez jelölje ki a terheléselosztót tartalmazó *myResouceGroupSLB* erőforráscsoportot, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy standard terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályt, az állapotmintát, majd tesztelte a terheléselosztót. Emellett eltávolított egy virtuális gépet az elosztott terhelésű készletből, és hozzáadta a virtuális gépet a háttércímkészlethez. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
