---
title: 'Oktatóanyag: belső terheléselosztó létrehozása – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre belső alapszintű terheléselosztót az Azure portal használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 1b9d943f540a0132abc6a70eba888aa5f8f46093
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225223"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Oktatóanyag: Egyenleg belső bejövő forgalmának terhelés egy alapszintű load balancer az Azure Portalon

Terheléselosztási a bejövő kérelmeket virtuális gépeken (VM) között osztja szét a magasabb rendelkezésre állást és méretezést biztosít. Alapszintű load balancer létrehozása, és többek között a virtuális gépek belső bejövő forgalmának terheléselosztása az Azure portal segítségével. Ez az oktatóanyag bemutatja, hogyan hozhat létre és konfiguráljon egy belső terheléselosztó háttér-kiszolgálók és hálózati erőforrások az alapszintű díjcsomagot.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

Ha szeretné, hajtsa végre ezeket a lépéseket az [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) vagy a [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) használatával a portál helyett.

Az oktatóanyag lépéseinek elvégzéséhez jelentkezzen be a Azure Portalba [https://portal.azure.com](https://portal.azure.com)címen.

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Virtuális hálózat, a háttérkiszolgálók és a tesztelési virtuális gép létrehozása

Először hozzon létre egy virtuális hálózatot (VNet). A virtuális hálózat létrehozása két virtuális gépet a terheléselosztó teszteléséhez használja a háttér-készlet az alapszintű load balancer és a egy külső virtuális gép használatával. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget.
   
1. A **virtuális hálózat létrehozása** panelen írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyVNet*nevet.
   - **ResourceGroup**: válassza az **új létrehozása**elemet, majd írja be a *Myresourcegrouplb erőforráscsoportban*, majd kattintson **az OK gombra**. 
   - **Alhálózat** > **neve**: írja be a következőt: *MyBackendSubnet*.
   
1. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása** > **számítás** > **Windows Server 2016 Datacenter**lehetőséget. 
   
1. A **virtuális gép létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés** > **erőforráscsoport**: legördülő lista, majd válassza a **myresourcegrouplb erőforráscsoportban**lehetőséget.
   - **Példány részletei** > **virtuális gép neve**: Type *MyVM1*.
   - **Példány részletei** > **rendelkezésre állási lehetőségek**: 
     1. Legördülő menüből válassza a **rendelkezésre állási csoport**lehetőséget. 
     2. Válassza az **új létrehozása**elemet, írja be a *MyAvailabilitySet*, majd kattintson **az OK gombra**.
   
1. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget. 
   
   Győződjön meg arról, hogy a következő kijelölt:
   - **Virtuális hálózat**: **MyVNet**
   - **Alhálózat**: **MyBackendSubnet**
   
   A **hálózati biztonsági csoport**területen:
   1. Válassza az **Advanced** (Speciális) lehetőséget. 
   1. Legördülő menüből **konfigurálja a hálózati biztonsági csoportot** , és válassza a **nincs**lehetőséget. 
   
1. Válassza a **kezelés** lapot, vagy válassza a **következő** > **felügyelet**lehetőséget. A **figyelés**területen **kapcsolja ki**a **rendszerindítási diagnosztika** beállítást.
   
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
1. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra. 

1. A *MyVM2*nevű második virtuális gép létrehozásához kövesse a lépéseket, és az összes többi beállítással megegyező, mint a MyVM1. 

1. A *MyTestVM*nevű harmadik virtuális gép létrehozásához kövesse az alábbi lépéseket. 

## <a name="create-a-basic-load-balancer"></a>Alapszintű terheléselosztó létrehozása

Alapszintű belső load balancer létrehozása a portál használatával. A név és IP-címet hoz létre automatikusan megtörténik, a terheléselosztó előtérbeli.

1. A Portal bal felső sarkában kattintson az **Erőforrás létrehozása** > **Hálózat** > **Terheléselosztó** elemre.
   
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a *myresourcegrouplb erőforráscsoportban* szöveget a szövegmezőbe.|
    | Név                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **belső**lehetőséget.                                        |
    | SKU           | Válassza az **Alapszintű** lehetőséget.                          |
    | Virtuális hálózat           | Válassza a *MyVNet*lehetőséget.                          |    
    | IP-cím hozzárendelése              | Válassza a **statikus**lehetőséget.   |
    | Magánhálózati IP-cím|Írjon be egy olyan címeket, amely a virtuális hálózat és az alhálózat címterület területén található, például *10.3.0.7*.  |

3. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás**gombra. 
   

## <a name="create-basic-load-balancer-resources"></a>Alapszintű load balancer-erőforrások létrehozása

Ebben a szakaszban a terheléselosztó beállításait konfigurálja egy háttérbeli címkészlet és egy állapot-mintavétel számára, és meg kell adnia a terheléselosztó szabályait.

### <a name="create-a-back-end-address-pool"></a>Háttércímkészlet létrehozása

A terheléselosztó osztja el a forgalmat a virtuális gépekhez, használja a háttér címkészletet. A háttér-címkészletet a terheléselosztóhoz csatlakozó virtuális hálózati adaptereket (NIC) IP-címét tartalmazza. 

**VM1-t és VM2-t tartalmazó háttér-címkészlet létrehozása:**

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** alatt válassza a **Háttérkészletek**, majd a **Hozzáadás** lehetőséget.
   
1. A **háttérrendszer-készlet hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyBackendPool*nevet.
   - **Társított**: legördülő lista, és válassza a **rendelkezésre állási csoport**lehetőséget.
   - **Rendelkezésre állási csoport**: válassza a **MyAvailabilitySet**lehetőséget.
   
1. Válassza **a cél hálózati IP-konfiguráció hozzáadása**lehetőséget. 
   1. Adja hozzá a **MyVM1** és a **MyVM2** a háttér-készlethez.
   2. Miután hozzáadta az egyes gépeket, legördülő menüből válassza ki a **hálózati IP-konfigurációt**. 
   
   >[!NOTE]
   >Ne vegyen fel **MyTestVM** a készletbe. 
   
1. Kattintson az **OK** gombra.
   
   ![Adja hozzá a háttér-címkészletet](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. A **háttérbeli készletek** lapon bontsa ki a **MyBackendPool** elemet, és győződjön meg arról, hogy mind a **VM1** , mind a **VM2** szerepel.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a load balancer monitorozhassa a virtuális gép állapotát, állapotmintát használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. 

**Állapot-mintavétel létrehozása a virtuális gépek állapotának figyeléséhez:**

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
   
1. Az **állapotfelmérés hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyHealthProbe*nevet.
   - **Protokoll**: legördülő menüből válassza a **http**lehetőséget. 
   - **Port**: Type *80*. 
   - **Elérési út**: fogadja */* az alapértelmezett URI-t. Ez az érték lecserélheti bármely más URI-t. 
   - **Időköz**: Type *15*. Az időköz a mintavételi kísérletek között eltelt másodpercek száma.
   - Nem megfelelő **állapot küszöbértéke**: Type *2*. Ez az érték az egymást követő mintavételi hiba, amely a virtuális gép nem megfelelő állapotúnak számít még azelőtt számát jelenti.
   
1. Kattintson az **OK** gombra.
   
   ![Mintavétel hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalmat, a háttérbeli IP-címkészletet, és a forgalom és a szükséges forrás és cél portok. 

A **MyLoadBalancerRule** nevű terheléselosztó-szabály a 80-es portot figyeli az előtér- **LoadBalancerFrontEnd**. A szabály a 80-es porton is továbbítja a hálózati forgalmat a háttérbeli címkészlet **MyBackendPool**. 

**A terheléselosztó szabály létrehozása:**

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. A **terheléselosztási szabály hozzáadása** lapon írja be vagy válassza ki a következő értékeket, ha még nem létezik:
   
   - **Név**: írja be a *MyLoadBalancerRule*nevet.
   - Előtérbeli **IP-cím:** Írja be a *LoadBalancerFrontEnd* típust, ha nincs jelen.
   - **Protokoll**: válassza a **TCP**lehetőséget.
   - **Port**: Type *80*.
   - **Háttér-port**: Type *80*.
   - **Háttér-készlet**: válassza a **MyBackendPool**lehetőséget.
   - **Állapot**-mintavétel: válassza a **MyHealthProbe**lehetőséget. 
   
1. Kattintson az **OK** gombra.
   
   ![Adjon hozzá egy terheléselosztó-szabályt](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

A háttér-kiszolgálón telepíti az Internet Information Services (IIS), majd MyTestVM a magánhálózati IP-címére a terheléselosztó teszteléséhez. Minden egyes háttérbeli virtuális gép egy másik verzióját az IIS alapértelmezett weblapjára szolgálja ki, így láthatja a kérések a két virtuális gép között osztja el a terheléselosztó.

A portálon, a MyLoadBalancer **Áttekintés** lapján keressemeg az IP-címét a **magánhálózati IP-cím**alatt. Vigye a kurzort a címek fölé, és válassza a **Másolás** ikont a másoláshoz. Ebben a példában ez a **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Csatlakozhat a virtuális gépekhez RDP-vel

Először csatlakozzon a három virtuális gép a távoli asztali (RDP). 

>[!NOTE]
>Alapértelmezés szerint a virtuális gépeken már nyitva van az **RDP** (távoli asztal) port a távoli asztal elérésének engedélyezéséhez. 

**Távoli asztal (RDP) a virtuális gépekre:**

1. A portálon válassza a **minden erőforrás** elemet a bal oldali menüben. Az erőforrások listájából válassza ki az egyes virtuális gépeket a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz.
   
1. Az **Áttekintés** lapon válassza a **kapcsolat**, majd az RDP- **Fájl letöltése**lehetőséget. 
   
1. Nyissa meg a letöltött RDP-fájlt, és válassza a **kapcsolat**lehetőséget.
   
1. A Windows rendszerbiztonság képernyőn válassza a **további lehetőségek lehetőséget** , majd **használjon másik fiókot**. 
   
   Adja meg a felhasználónevet és a jelszót, majd kattintson **az OK gombra**.
   
1. Válaszoljon az **Igen** értékre bármelyik tanúsítványra. 
   
   A virtuális gép asztalához egy új ablakban nyílik meg. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Telepítse az IIS szolgáltatást, és cserélje le az alapértelmezett IIS-oldal a háttérbeli virtuális gépeken

A háttér-kiszolgálókon PowerShell használatával telepítse az IIS és az IIS alapértelmezett weblapjára cserélje le a testre szabott lapot.

>[!NOTE]
>Az IIS telepítéséhez használhatja a **Kiszolgálókezelő** **szerepkörök és szolgáltatások hozzáadása varázslóját** is. 

**Az IIS telepítése és az alapértelmezett weblap frissítése a PowerShell-lel:**

1. A MyVM1 és a MyVM2 alkalmazásban indítsa el a **Windows PowerShellt** a **Start** menüből. 

2. Futtassa az IIS telepítéséhez, és cserélje le az IIS alapértelmezett weblapjára a következő parancsokat:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Az MyVM1 és a MyVM2 RDP-kapcsolatok bezárásához válassza a **Leválasztás**lehetőséget. Ne állítsa le a virtuális gépeket.

### <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. A MyTestVM nyissa meg az **Internet Explorert**, és válaszoljon **az OK gombra** a konfigurációs kérések esetében. 
   
1. Illessze be vagy írja be a terheléselosztó magánhálózati IP-címét (*10.3.0.7*) a böngésző címsorába. 
   
   A testre szabott IIS kiszolgáló alapértelmezett weblap jelenik meg a böngészőben. Az üzenet beolvassa **"Helló világ!" alkalmazás a MyVM1-ból**, vagy **"Helló világ!" alkalmazás a MyVM2**.
   
1. Frissítse a böngészőt a terheléselosztót, virtuális gép között osztja el a forgalmat. Szükség lehet törölni a böngésző gyorsítótárát kísérletek között.

   Előfordulhat, hogy a **MyVM1** lap megjelenik, és más esetekben a **MyVM2** oldal jelenik meg, mivel a terheléselosztó az egyes háttérbeli virtuális gépekhez továbbítja a kéréseket. 

   ![Új IIS alapértelmezett lap](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A terheléselosztó és az összes kapcsolódó erőforrás törléséhez, ha már nincs szüksége rájuk, nyissa meg a **myresourcegrouplb erőforráscsoportban** erőforráscsoportot, és válassza az **erőforráscsoport törlése**elemet.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Basic szintű belső load balancer. Létrehozott és konfigurált hálózati erőforrásokhoz, háttér-kiszolgálók, az állapotfigyelő mintavételező és a terheléselosztó szabályainak. Az IIS telepítve a háttérbeli virtuális géppel, és a tesztelési virtuális gép használja a terheléselosztó teszteléséhez a böngészőben. 

Ezután megtudhatja, hogyan virtuális gépek terheléselosztása rendelkezésre állási zónák között.

> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
