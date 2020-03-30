---
title: Rövid útmutató:Nyilvános terheléselosztó létrehozása – Azure portal
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy terheléselosztót az Azure Portal használatával.
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
ms.openlocfilehash: b424fe315737b84479283eed2d77398c8ce4f148
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78898831"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy terheléselosztót a terheléselosztási virtuális gépekhez az Azure Portalon

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Az Azure Portal használatával létrehozhat egy terheléselosztót a virtuális gépek (VM-ek) terhelésének elosztása érdekében. Ez a rövid útmutató bemutatja, hogyan töltheti be a virtuális gépek et nyilvános terheléselosztó használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-a-load-balancer"></a>Load Balancer-terheléselosztó létrehozása

Ebben a szakaszban hozzon létre egy terheléselosztót, amely segít a virtuális gépek terheléselosztásában. Létrehozhat nyilvános terheléselosztót vagy belső terheléselosztót. Nyilvános terheléselosztó létrehozásakor létre kell hoznia egy új nyilvános IP-címet is, amely előtérként van konfigurálva (alapértelmezés szerint *LoadBalancerFrontend* néven elnevezett) a terheléselosztószámára.

1. A képernyő bal felső részén válassza az **Erőforrás** > **létrehozása hálózati** > **terheléselosztó**lehetőséget.
2. A **Terheléselosztó létrehozása** lap **Alapjai** lapján adja meg vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd válassza a **Véleményezés + létrehozás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroupSLB* parancsot a szövegmezőbe.|
    | Név                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | SKU           | Válassza **a Normál** vagy az **Alapszintű**lehetőséget. A Microsoft az éles számítási feladatokhoz ajánlja a Standard ot. |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. Ha van egy meglévő nyilvános IP-címe, amelyet használni szeretne, válassza **a Meglévő** |
    | Nyilvános IP-cím              | Írja be a *myPublicIP szöveget* a szövegmezőbe.   Alapszintű nyilvános IP-cím létrehozásához használható. ```-SKU Basic``` Az alapvető nyilvános IP-k nem kompatibilisek a **standard** terheléselosztóval. A Microsoft azt javasolja, hogy a **Standard** éles számítási feladatokhoz.|
    | Rendelkezésreállási zóna | A *Zónaredundáns* típussal rugalmas terheléselosztót hozhat létre. Zónaszintű terheléselosztó létrehozásához válasszon ki egy adott zónát az 1, 2 vagy 3 |

> [!IMPORTANT]
> A rövid útmutató többi része feltételezi, hogy a **standard** termékváltozat a fenti termékváltozat-kiválasztási folyamat során kerül kiválasztásra.


3. A **Véleményezés + létrehozás** lapon válassza a **Létrehozás gombot.**   

    ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Terheléselosztó-erőforrások létrehozása

Ebben a szakaszban konfigurálja a terheléselosztó beállításait egy háttércímkészlethez, egy állapotmintához, és adjon meg egy kiegyensúlyozó szabályt.

### <a name="create-a-backend-pool"></a>Háttérkészlet létrehozása

A virtuális gépek revonatkozó forgalom elosztásához a háttér-címkészlet tartalmazza a terheléselosztóhoz csatlakoztatott virtuális (NIC) IP-címeket. Hozza létre a *myBackendPool* háttércímkészletet, hogy virtuális gépeket tartalmazzon a terheléselosztáshoz az internetes forgalomhoz.

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.
2. A **Beállítások csoportban**válassza **a Háttérkészletek**lehetőséget, majd a **Hozzáadás**lehetőséget.
3. A **Háttérkészlet hozzáadása** lap névmezőbe írja be a *myBackendPool*nevet a háttérkészlet neveként, majd válassza **a Hozzáadás lehetőséget.**

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Annak engedélyezéséhez, hogy a terheléselosztó figyelhesse az alkalmazás állapotát, használjon állapotmintát. Az állapotminta dinamikusan hozzáadja vagy eltávolítja a virtuális gépeket a terheléselosztó rotációjából az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.
2. A **Beállítások csoportban**válassza **az Állapotmintavételek**lehetőséget, majd a **Hozzáadás**lehetőséget.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg *a myHealthProbe .* |
    | Protocol (Protokoll) | Válassza a **HTTP**lehetőséget. |
    | Port | Adja meg *a 80*.|
    | Intervallum | Adja meg a *15* értéket a mintavételi kísérletek közötti **időköz** másodpercben. |
    | Nem kifogástalan állapot küszöbértéke | Válassza ki a **2-t** a **nem megfelelő küszöbértékek** vagy egymást követő mintavételi hibák, amelyek a virtuális gép nem megfelelő állapotúnak kell lennie.|
    | | |
4. Válassza **az OK gombot.**

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy terheléselosztó szabályt *myLoadBalancerRuleWeb* a frontend *frontendloadbalancer* 80-as portjának meghallgatásához, és a terheléselosztásos hálózati forgalom küldéséhez a *myBackEndPool* háttércímkészletbe a 80-as porthasználatával is. 

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.
2. A **Beállítások csoportban**válassza a **Terheléselosztási szabályok**lehetőséget, majd a **Hozzáadás**lehetőséget.
3. Konfigurálja a terheléselosztási szabályt az alábbi értékekkel:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be *a myHTTPRule értéket*. |
    | Protocol (Protokoll) | Válassza a **TCP**lehetőséget. |
    | Port | Adja meg *a 80*.|
    | Háttérablak | Adja meg *a 80*. |
    | A háttérkészlet | Válassza a *myBackendPool lehetőséget.*|
    | Állapotadat-mintavétel | Válassza a *myHealthProbe lehetőséget.* |
4. Hagyja meg a többi alapértelmezést, majd kattintson az **OK gombra.**


## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban hozzon létre egy virtuális hálózatot, hozzon létre három virtuális gépet a terheléselosztó háttérkészletéhez, majd telepítse az IIS-t a virtuális gépekre a terheléselosztó teszteléséhez.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroupSLB |
| **\<virtuális hálózat neve>** | myVNet          |
| **\<régiónév>**          | Nyugat-Európa      |
| **\<IPv4-címtér>**   | 10.1.0.0\16          |
| **\<alhálózat-név>**          | myBackendSubnet        |
| **\<alhálózati címtartomány>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
A nyilvános IP-smu-knak és a terheléselosztó smuknak egyezniük kell. A standard terheléselosztó esetén használja a normál IP-címekkel rendelkező virtuális gépeket a háttérkészletben. Ebben a szakaszban három virtuális gépet *(myVM1*, *myVM2* és *myVM3)* hoz létre standard nyilvános IP-címmel három különböző zónában *(1. zóna,* *2. zóna*és *3. zóna),* amelyek később hozzáadódnak a korábban létrehozott terheléselosztó háttérkészletéhez. Ha az Alapszintű lehetőséget választotta, használja az alapszintű IP-címekkel rendelkező virtuális gépeket.

1. A portál bal felső részén válassza az **Erőforrás** > létrehozása**Számítási** > **Windows Server 2019 Datacenter lehetőséget.** 
   
1. A **Virtuális gép létrehozása**csoportban írja be vagy válassza ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupSLB**lehetőséget.
   - **Példány részletei** > **Virtuális gép neve**: Írja be a *myVM1*nevet.
   - **Példány részletei** > **régió** > válassza **nyugat-európai**lehetőséget.
   - **A példány részleteinek** > **rendelkezésre állási beállításai** > Az **Elérhetőségi zónák**kiválasztása . 
   - **Példány részletei:** > **A példány részleteinek rendelkezésre állási zónája** > **1**kiválasztása .
   - **Rendszergazdai fiók**> Adja meg a **Felhasználónevet**, **jelszó** és **jelszó megerősítése.**
   - Válassza a **Hálózat** lapot, vagy válassza **a Tovább: Lemezek**, majd a **Tovább: Hálózat**lehetőséget.
  
1. A **Hálózat** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: *myVnet*
   - **Alhálózat**: *myBackendSubnet*
   - **A nyilvános IP->** válassza **az Új létrehozása lehetőséget,** majd a **Nyilvános IP-cím létrehozása** ablakban a **Termékváltozatban**válassza a **Normál**, és a **Rendelkezésre állási zóna**lehetőséget, válassza **a Zónaredundáns**lehetőséget, majd az **OK**gombot. Ha létrehozott egy egyszerű terheléselosztót, válassza az Alapszintű lehetőséget. A Microsoft azt javasolja, hogy a standard termékváltozat éles számítási feladatokhoz.
   - Új hálózati biztonsági csoport (NSG) létrehozásához a **Hálózatbiztonsági csoport csoportban**válassza a **Speciális**lehetőséget. 
       1. A **Hálózati biztonsági csoport konfigurálása** mezőben válassza **az Új létrehozása lehetőséget.** 
       1. Írja be a *myNetworkSecurityGroup*parancsot, és válassza **az OK gombot.**
   - Ha azt szeretné, hogy a virtuális gép a terheléselosztó háttérkészletének részévé legyen, hajtsa végre az alábbi lépéseket:
        - A **Terheléselosztás**területen a **Virtuális gépet egy meglévő terheléselosztási megoldás mögé helyezze?** **Yes**
        - A **Terheléselosztás beállításai** **párbeszédpanelterhelés-elosztási beállítások**ban válassza az Azure **terheléselosztó lehetőséget.**
        - A **Load Balancer kiválasztása**esetén a *myLoadBalancer*.
        - Válassza a **Kezelés** lapot, vagy válassza a **Következő** > **kezelés**lehetőséget.
2. A **Kezelés** lap **Figyelés**csoportjában állítsa a **rendszerindítási diagnosztikát** **Ki**beállításra. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.   
1. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.**
1. A 2–6. *myVM1*

    | Beállítás | VM 2| VM 3|
    | ------- | ----- |---|
    | Név |  *myVM2* |*myVM3*|
    | Rendelkezésreállási zóna | 2 |3|
    |Nyilvános IP-cím| **Standard (standard)** Sku|**Standard (standard)** Sku|
    | Nyilvános IP - Rendelkezésre állási zóna| **Felesleges zóna** |**Felesleges zóna**|
    | Hálózati biztonsági csoport | A meglévő *MyNetworkSecurity csoport* kijelölése| A meglévő *MyNetworkSecurity csoport* kijelölése|

 ### <a name="create-nsg-rule"></a>NSG-szabály létrehozása

Ebben a szakaszban létrehoz egy hálózati biztonsági csoportszabályt, amely engedélyezi a http-n keresztül imázsú bejövő kapcsolatokat.

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd az erőforrások listájában válassza a **myResourceGroupSLB** erőforráscsoportban található **myNetworkSecurityGroup** elemet.
2. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet, majd a **Hozzáadás** lehetőséget.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *Service Tag*
    -  **Forrás szolgáltatás címke**: *Internet*
    - **Célport-tartományok**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - **Név**: *myHTTPRule* 
    - **Leírás**: "*HTTP engedélyezése* 
4. Válassza a **Hozzáadás** lehetőséget.
5. Szükség esetén ismételje meg a bejövő RDP-szabály lépéseit a következő eltérő értékekkel:
   - **Célport-tartományok**: *3389-es*típus .
   - **Prioritás**: *200-* as típus . 
   - **Név**: Írja be *a MyRDPRule*nevet. 
   - **Leírás**: Írja be az *RDP engedélyezése*. 
 
### <a name="install-iis"></a>Az IIS telepítése

1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd az erőforrások listájában válassza a *myResourceGroupSLB* erőforráscsoportban található **myVM1** elemet.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
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
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **MyPublicIP parancsot.**

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha látni szeretné, hogy a terheléselosztó mindhárom virtuális gépen elosztja a forgalmat, testreszabhatja az egyes virtuális gépek IIS webkiszolgálójának alapértelmezett lapját, majd kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Ehhez jelölje ki a terheléselosztót tartalmazó erőforráscsoportot *(myResourceGroupSLB),* majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy standard terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályt, az állapotmintát, majd tesztelte a terheléselosztót. Ha többet szeretne megtudni az Azure Load Balancer-ről, folytassa az [Azure Load Balancer oktatóanyagokkal.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

További információ a [terheléselosztóról és a rendelkezésre állási zónákról.](load-balancer-standard-availability-zones.md)
