---
title: 'Oktatóanyag: Egy belső terheléselosztó létrehozása – Azure portal'
titlesuffix: Azure Load Balancer
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre belső alapszintű terheléselosztót az Azure portal használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 6abae32f5d8781735bc6a50dc888fddacbe8d0b9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105301"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Oktatóanyag: Egyenleg belső bejövő forgalmának terhelés egy alapszintű load balancer az Azure Portalon

Terheléselosztási a bejövő kérelmeket virtuális gépeken (VM) között osztja szét a magasabb rendelkezésre állást és méretezést biztosít. Alapszintű load balancer létrehozása, és többek között a virtuális gépek belső bejövő forgalmának terheléselosztása az Azure portal segítségével. Ez az oktatóanyag bemutatja, hogyan hozhat létre és konfiguráljon egy belső terheléselosztó háttér-kiszolgálók és hálózati erőforrások az alapszintű díjcsomagot.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

Igény szerint ezeket a lépéseket használatával teheti a [Azure CLI-vel](load-balancer-get-started-ilb-arm-cli.md) vagy [Azure PowerShell-lel](load-balancer-get-started-ilb-arm-ps.md) a portál helyett.

A lépések az ezen oktatóanyag segítségével jelentkezzen be az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Virtuális hálózat, a háttérkiszolgálók és a tesztelési virtuális gép létrehozása

Először hozzon létre egy virtuális hálózatot (VNet). A virtuális hálózat létrehozása két virtuális gépet a terheléselosztó teszteléséhez használja a háttér-készlet az alapszintű load balancer és a egy külső virtuális gép használatával. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A portál bal felső oldalán válassza **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.
   
1. Az a **virtuális hálózat létrehozása** panelen adja meg ezeket az értékeket:
   
   - **Név**: Típus *MyVNet*.
   - **Erőforráscsoport**: Válassza ki **új létrehozása**, majd adja meg *MyResourceGroupLB*, és válassza ki **OK**. 
   - **Alhálózat** > **neve**: Típus *MyBackendSubnet*.
   
1. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A portál bal felső oldalán válassza **erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter**. 
   
1. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:
   - **Előfizetés** > **erőforráscsoport**: Legördülő listára, és válassza ki **MyResourceGroupLB**.
   - **Példány részletei** > **virtuális gép neve**: Típus *MyVM1*.
   - **Példány részletei** > **rendelkezésre állási beállítások**: 
     1. Legördülő listára, és válassza ki **rendelkezésre állási csoport**. 
     2. Válassza ki **új létrehozása**, típus *MyAvailabilitySet*, és válassza ki **OK**.
   
1. Válassza ki a **hálózatkezelés** lapon, vagy válasszon **tovább: Lemezek**, majd **tovább: Hálózatkezelés**. 
   
   Győződjön meg arról, hogy a következő kijelölt:
   - **Virtuális hálózat**: **MyVNet**
   - **Alhálózat**: **MyBackendSubnet**
   
   A **hálózati biztonsági csoport**:
   1. Válassza az **Advanced** (Speciális) lehetőséget. 
   1. Legördülő lista **konfigurálása a hálózati biztonsági csoport** válassza **None**. 
   
1. Válassza ki a **felügyeleti** lapon, vagy válasszon **tovább** > **felügyeleti**. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**.
   
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
1. Tekintse át a beállításokat, és válassza ki **létrehozás**. 

1. A lépésekkel hozzon létre egy második virtuális Gépet nevű *MyVM2*, a többi beállítást az azonos MyVM1. 

1. Kövesse a lépéseket, hogy újra egy harmadik nevű virtuális gép létrehozása *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Alapszintű terheléselosztó létrehozása

Alapszintű belső load balancer létrehozása a portál használatával. A név és IP-címet hoz létre automatikusan megtörténik, a terheléselosztó előtérbeli.

1. A Portal bal felső sarkában kattintson az **Erőforrás létrehozása** > **Hálózat** > **Terheléselosztó** elemre.
   
2. Az a **alapjai** lapján a **terheléselosztó létrehozása** lap, adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, és válassza **felülvizsgálat +létrehozása**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza ki **új létrehozása** , és írja be *MyResourceGroupLB* a szövegmezőben.|
    | Name (Név)                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Typo          | Válassza ki **nyilvános**.                                        |
    | SKU           | Válassza az **Alapszintű** lehetőséget.                          |
    | Virtuális hálózat           | Válassza ki *MyVNet*.                          |    
| Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím hozzárendelése              | Válassza ki **statikus**.   |
    | Magánhálózati IP-cím|Írjon be egy címet, amely a címterét a virtuális hálózatot és alhálózatot, például *10.3.0.7*.  |

3. Az a **felülvizsgálat + létrehozása** lapra, majd **létrehozás**. 
   

## <a name="create-basic-load-balancer-resources"></a>Alapszintű load balancer-erőforrások létrehozása

Ebben a szakaszban konfigurálni egy háttér címkészletet, és a egy állapotminta terheléselosztó beállításai, és adja meg a load balancer-szabályok.

### <a name="create-a-back-end-address-pool"></a>Háttércímkészlet létrehozása

A terheléselosztó osztja el a forgalmat a virtuális gépekhez, használja a háttér címkészletet. A háttér-címkészletet a terheléselosztóhoz csatlakozó virtuális hálózati adaptereket (NIC) IP-címét tartalmazza. 

**A háttér-címkészlet létrehozása, amely tartalmazza a VM1, VM2 és:**

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. A **Beállítások** alatt válassza a **Háttérkészletek**, majd a **Hozzáadás** lehetőséget.
   
1. Az a **háttérkészlet hozzáadása** lapon adja meg a következő értékeket:
   
   - **Név**: Típus *MyBackendPool*.
   - **Társított**: Legördülő listára, és válassza ki **rendelkezésre állási csoport**.
   - **A rendelkezésre állási csoport**: Válassza ki **MyAvailabilitySet**.
   
1. Válassza ki **cél IP-konfiguráció hozzáadása**. 
   1. Adjon hozzá **MyVM1** és **MyVM2** a háttér-készlethez.
   2. Miután hozzáadta az egyes, a legördülő listára, és válassza ki a **hálózati IP-konfiguráció**. 
   
   >[!NOTE]
   >Ne adjon hozzá **MyTestVM** a készlethez. 
   
1. Kattintson az **OK** gombra.
   
   ![Adja hozzá a háttér-címkészletet](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Az a **háttérkészletek** lapon, bontsa ki a **MyBackendPool** , és győződjön meg arról, hogy mindkét **VM1** és **VM2** szerepelnek.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a load balancer monitorozhassa a virtuális gép állapotát, állapotmintát használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. 

**A virtuális gépek állapotának figyelése egy állapotminta létrehozásához:**

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
   
1. Az a **állapotadat-mintavétel hozzáadása** lapon adja meg a következő értékeket:
   
   - **Név**: Típus *MyHealthProbe*.
   - **Protokoll**: Legördülő listára, és válassza ki **HTTP**. 
   - **Port**: Típus *80-as*. 
   - **Elérési út**: Fogadja el */* az alapértelmezett URI-t. Ez az érték lecserélheti bármely más URI-t. 
   - **Intervallum**: Típus *15*. Az időköz a mintavételi kísérletek között eltelt másodpercek száma.
   - **Nem kifogástalan állapot küszöbértéke**: Típus *2*. Ez az érték az egymást követő mintavételi hiba, amely a virtuális gép nem megfelelő állapotúnak számít még azelőtt számát jelenti.
   
1. Kattintson az **OK** gombra.
   
   ![Mintavétel hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalmat, a háttérbeli IP-címkészletet, és a forgalom és a szükséges forrás és cél portok. 

A nevű terheléselosztási szabályt **MyLoadBalancerRule** az előtér-alkalmazást a 80-as porton figyel **LoadBalancerFrontEnd**. A szabály küld hálózati forgalmat a háttér-címkészletet **MyBackendPool**, szintén a 80-as porton. 

**A terheléselosztási szabály létrehozása:**

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. Az a **terheléselosztási szabály hozzáadása** oldalon, írja be vagy válassza ki a következő értékeket, ha még nem létezik:
   
   - **Név**: Típus *MyLoadBalancerRule*.
   - **Előtérbeli IP-cím:** Típus *LoadBalancerFrontEnd* Ha nincs jelen.
   - **Protokoll**: Válassza ki **TCP**.
   - **Port**: Típus *80-as*.
   - **Háttérport**: Típus *80-as*.
   - **Háttérkészlet**: Válassza ki **MyBackendPool**.
   - **Az állapotfigyelő mintavételező**: Válassza ki **MyHealthProbe**. 
   
1. Kattintson az **OK** gombra.
   
   ![Adjon hozzá egy terheléselosztó-szabályt](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A háttér-kiszolgálón telepíti az Internet Information Services (IIS), majd MyTestVM a magánhálózati IP-címére a terheléselosztó teszteléséhez. Minden egyes háttérbeli virtuális gép egy másik verzióját az IIS alapértelmezett weblapjára szolgálja ki, így láthatja a kérések a két virtuális gép között osztja el a terheléselosztó.

A portálon a a **áttekintése** lapján **MyLoadBalancer**, keresse meg az IP-címnek **magánhálózati IP-címet**. Vigye a kurzort a címet, majd válassza a **másolási** másolja azt a ikonra. Ebben a példában van **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Csatlakozhat a virtuális gépekhez RDP-vel

Először csatlakozzon a három virtuális gép a távoli asztali (RDP). 

>[!NOTE]
>Alapértelmezés szerint a virtuális gépek már rendelkezik a **RDP** (távoli asztali verzió) port nyissa meg a távoli asztali hozzáférés engedélyezéséhez. 

**A távoli asztali (RDP) a virtuális gépek:**

1. Válassza a portál **összes erőforrás** a bal oldali menüben. Az erőforrások listájából válassza ki az egyes virtuális gépekre a **MyResourceGroupLB** erőforráscsoportot.
   
1. Az a **áttekintése** lapon jelölje be **Connect**, majd válassza ki **töltse le az RDP-fájl**. 
   
1. Nyissa meg az RDP letöltött, majd válasszon **Connect**.
   
1. A Windows biztonsági képernyőn válassza ki a **további lehetőségek** , majd **másik fiók használata**. 
   
   Adja meg a felhasználónevet és jelszót, majd **OK**.
   
1. Válasz **Igen** a minden tanúsítvány-kérdésre. 
   
   A virtuális gép asztalához egy új ablakban nyílik meg. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Telepítse az IIS szolgáltatást, és cserélje le az alapértelmezett IIS-oldal a háttérbeli virtuális gépeken

A háttér-kiszolgálókon PowerShell használatával telepítse az IIS és az IIS alapértelmezett weblapjára cserélje le a testre szabott lapot.

>[!NOTE]
>Is használhatja a **adja hozzá szerepkörök és szolgáltatások varázsló** a **Kiszolgálókezelő** az IIS telepítése. 

**Az IIS telepítése és frissítése az alapértelmezett weblapot a PowerShell-lel:**

1. MyVM1 és MyVM2, indítsa el a **Windows PowerShell** származó a **Start** menü. 

2. Futtassa az IIS telepítéséhez, és cserélje le az IIS alapértelmezett weblapjára a következő parancsokat:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Zárja be az RDP-kapcsolatok MyVM1 és MyVM2 kiválasztásával **Disconnect**. Ne állítsa le a virtuális gépeket.

### <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. MyTestVM, nyissa meg a **az Internet Explorer**, és az azokra **OK** bármely konfiguráció kéri. 
   
1. Illesszen vagy írjon be a terheléselosztó magánhálózati IP-cím (*10.3.0.7*) a böngésző címsorába. 
   
   A testre szabott IIS kiszolgáló alapértelmezett weblap jelenik meg a böngészőben. Az üzenet beolvasása vagy **Hello World a MyVM1**, vagy **Hello World a MyVM2**.
   
1. Frissítse a böngészőt a terheléselosztót, virtuális gép között osztja el a forgalmat. Szükség lehet törölni a böngésző gyorsítótárát kísérletek között.

   Egyes esetekben a **MyVM1** lap jelenik meg, vagy a **MyVM2** lap jelenik meg, mint a terheléselosztó osztja el a kérelmeket az egyes háttérbeli virtuális. 

   ![Új IIS alapértelmezett lap](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törli a terheléselosztó és az összes kapcsolódó erőforrást, ha már nincs szüksége, nyissa meg a **MyResourceGroupLB** erőforrás-csoport, és válassza **erőforráscsoport törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Basic szintű belső load balancer. Létrehozott és konfigurált hálózati erőforrásokhoz, háttér-kiszolgálók, az állapotfigyelő mintavételező és a terheléselosztó szabályainak. Az IIS telepítve a háttérbeli virtuális géppel, és a tesztelési virtuális gép használja a terheléselosztó teszteléséhez a böngészőben. 

Ezután megtudhatja, hogyan virtuális gépek terheléselosztása rendelkezésre állási zónák között.

> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
