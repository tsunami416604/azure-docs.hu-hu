---
title: 'Oktatóanyag: belső terheléselosztó létrehozása – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre belső alapszintű terheléselosztó-t a Azure Portal használatával.
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
ms.openlocfilehash: 6f62771d707d1aebccbfaf809dee7d0dedf5fefa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79096128"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Oktatóanyag: a belső forgalom terheléselosztása egy alapszintű terheléselosztó Azure Portal

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít a bejövő kérések virtuális gépeken (VM) keresztüli terjesztésével. A Azure Portal segítségével alapszintű terheléselosztó hozható létre, és a virtuális gépek közötti belső forgalmat is kiegyensúlyozhatja. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat belső terheléselosztó-, háttér-és hálózati erőforrásokat az alapszintű díjszabási szinten.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 

Ha szeretné, hajtsa végre ezeket a lépéseket az [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) vagy a [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) használatával a portál helyett.

Az oktatóanyag lépéseinek végrehajtásához jelentkezzen be a Azure Portalba a következő [https://portal.azure.com](https://portal.azure.com)címen:.

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>VNet, háttér-kiszolgálók és tesztelési virtuális gép létrehozása

Először hozzon létre egy virtuális hálózatot (VNet). A VNet hozzon létre két virtuális gépet az alapszintű Load Balancer háttér-készletéhez, valamint egy harmadik, a terheléselosztó teszteléséhez használni kívánt virtuális gépet. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A portál bal felső részén válassza az **erőforrás** > létrehozása**hálózatkezelés** > **virtuális hálózat**lehetőséget.
   
1. A **virtuális hálózat létrehozása** panelen írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyVNet*nevet.
   - **ResourceGroup**: válassza az **új létrehozása**elemet, majd írja be a *Myresourcegrouplb erőforráscsoportban*, majd kattintson **az OK gombra**. 
   - **Alhálózat** > **neve**: írja be a következőt: *MyBackendSubnet*.
   
1. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A portál bal felső részén válassza az **erőforrás** > létrehozása**számítás** > **Windows Server 2016 Datacenter**elemet. 
   
1. A **virtuális gép létrehozása**területen írja be vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés** > -**erőforráscsoport**: legördülő menüből válassza a **myresourcegrouplb erőforráscsoportban**lehetőséget.
   - **Példány részletei** > **virtuális gép neve**: Type *MyVM1*.
   - **Példány részleteinek** > **rendelkezésre állási lehetőségei**: 
     1. Legördülő menüből válassza a **rendelkezésre állási csoport**lehetőséget. 
     2. Válassza az **új létrehozása**elemet, írja be a *MyAvailabilitySet*, majd kattintson **az OK gombra**.
   
1. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget. 
   
   Győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **MyVNet**
   - **Alhálózat**: **MyBackendSubnet**
   
   A **hálózati biztonsági csoport**területen:
   1. Válassza az **Advanced** (Speciális) lehetőséget. 
   1. Legördülő menüből **konfigurálja a hálózati biztonsági csoportot** , és válassza a **nincs**lehetőséget. 
   
1. Válassza a **kezelés** lapot, vagy válassza a **következő** > **kezelés**lehetőséget. A **figyelés**területen **kapcsolja ki**a **rendszerindítási diagnosztika** beállítást.
   
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
1. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra. 

1. A *MyVM2*nevű második virtuális gép létrehozásához kövesse a lépéseket, és az összes többi beállítással megegyező, mint a MyVM1. 

1. A *MyTestVM*nevű harmadik virtuális gép létrehozásához kövesse az alábbi lépéseket. 

## <a name="create-a-basic-load-balancer"></a>Alapszintű terheléselosztó létrehozása

Hozzon létre egy alapszintű belső terheléselosztót a portál használatával. A létrehozott név és IP-cím automatikusan a terheléselosztó előlapján lesz konfigurálva.

1. A portál bal felső részén válassza az **erőforrás** > létrehozása**hálózatkezelés** > **Load Balancer**elemet.
   
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a *myresourcegrouplb erőforráscsoportban* szöveget a szövegmezőbe.|
    | Name (Név)                   | *myLoadBalancer*                                   |
    | Régió         | Válassza az **USA 2. keleti régiója** lehetőséget.                                        |
    | Típus          | Válassza a **belső**lehetőséget.                                        |
    | SKU           | Válassza az **Alapszintű** lehetőséget.                          |
    | Virtuális hálózat           | Válassza a *MyVNet*lehetőséget.                          |    
    | IP-cím hozzárendelése              | Válassza a **statikus**lehetőséget.   |
    | Magánhálózati IP-cím|Írjon be egy olyan címeket, amely a virtuális hálózat és az alhálózat címterület területén található, például *10.3.0.7*.  |

3. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás**gombra. 
   

## <a name="create-basic-load-balancer-resources"></a>Alapszintű Load Balancer-erőforrások létrehozása

Ebben a szakaszban a terheléselosztó beállításait konfigurálja egy háttérbeli címkészlet és egy állapot-mintavétel számára, és meg kell adnia a terheléselosztó szabályait.

### <a name="create-a-back-end-address-pool"></a>Háttércímkészlet létrehozása

A forgalom a virtuális gépekre való terjesztéséhez a terheléselosztó egy háttérbeli címkészletet használ. A háttérbeli címkészlet tartalmazza a terheléselosztó számára csatlakozó virtuális hálózati adapterek (NIC-EK) IP-címeit. 

**VM1-t és VM2-t tartalmazó háttér-címkészlet létrehozása:**

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** alatt válassza a **Háttérkészletek**, majd a **Hozzáadás** lehetőséget.
   
1. A **háttérrendszer-készlet hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyBackendPool*nevet.
   - **Társított**: legördülő lista, majd válassza a **virtuális gép**lehetőséget.
   
   
1. Válassza a **virtuális gép**lehetőséget. 
   1. Adja hozzá a **MyVM1** és a **MyVM2** a háttér-készlethez.
   2. Miután hozzáadta az egyes gépeket, legördülő menüből válassza ki a **hálózati IP-konfigurációt**. 
   
   >[!NOTE]
   >Ne vegyen fel **MyTestVM** a készletbe. 
   
1. Kattintson az **OK** gombra.
   
   ![A háttérbeli Címkészlet hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. A **háttérbeli készletek** lapon bontsa ki a **MyBackendPool** elemet, és győződjön meg arról, hogy mind a **VM1** , mind a **VM2** szerepel.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ha engedélyezni szeretné a terheléselosztó számára a virtuális gép állapotának figyelését, az állapot-mintavételt kell használnia. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. 

**Állapot-mintavétel létrehozása a virtuális gépek állapotának figyeléséhez:**

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
   
1. Az **állapotfelmérés hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a *MyHealthProbe*nevet.
   - **Protokoll**: legördülő menüből válassza a **http**lehetőséget. 
   - **Port**: Type *80*. 
   - **Elérési út**: fogadja el */* az alapértelmezett URI-t. Ezt az értéket bármely más URI azonosítóval helyettesítheti. 
   - **Időköz**: Type *15*. Az intervallum a mintavételi kísérletek között eltelt másodpercek száma.
   - Nem megfelelő **állapot küszöbértéke**: Type *2*. Ez az érték azon egymást követő mintavételi hibák száma, amelyek a virtuális gép nem megfelelő állapotának tekintendők.
   
1. Kattintson az **OK** gombra.
   
   ![Mintavétel hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A szabály meghatározza a bejövő forgalom előtér-IP-konfigurációját, a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás-és célport eléréséhez. 

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
   
   ![Terheléselosztási szabály hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Telepítse a Internet Information Services (IIS) szolgáltatást a háttér-kiszolgálókon, majd a MyTestVM használatával tesztelje a terheléselosztó saját IP-címével. Minden háttérbeli virtuális gép az alapértelmezett IIS-weblap egy másik verzióját szolgálja ki, így láthatja, hogy a terheléselosztó a két virtuális gép között osztja el a kérelmeket.

A portálon, a MyLoadBalancer **Áttekintés** lapján keresse **MyLoadBalancer**meg az IP-címét a **magánhálózati IP-cím**alatt. Vigye a kurzort a címek fölé, és válassza a **Másolás** ikont a másoláshoz. Ebben a példában ez a **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Kapcsolódás a virtuális gépekhez RDP használatával

Először kapcsolódjon mindhárom virtuális géphez Távoli asztal (RDP) használatával. 

>[!NOTE]
>Alapértelmezés szerint a virtuális gépeken már nyitva van az **RDP** (távoli asztal) port a távoli asztal elérésének engedélyezéséhez. 

**Távoli asztal (RDP) a virtuális gépekre:**

1. A portálon válassza a **minden erőforrás** elemet a bal oldali menüben. Az erőforrások listájából válassza ki az egyes virtuális gépeket a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz.
   
1. Az **Áttekintés** lapon válassza a **kapcsolat**, majd az RDP- **Fájl letöltése**lehetőséget. 
   
1. Nyissa meg a letöltött RDP-fájlt, és válassza a **kapcsolat**lehetőséget.
   
1. A Windows rendszerbiztonság képernyőn válassza a **további lehetőségek lehetőséget** , majd **használjon másik fiókot**. 
   
   Adja meg a felhasználónevet és a jelszót, majd kattintson **az OK gombra**.
   
1. Válaszoljon az **Igen** értékre bármelyik tanúsítványra. 
   
   A VM Desktop új ablakban nyílik meg. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Telepítse az IIS-t, és cserélje le az alapértelmezett IIS-lapot a háttérbeli virtuális gépekre

Az egyes háttér-kiszolgálókon a PowerShell használatával telepítse az IIS-t, és cserélje le az alapértelmezett IIS-weblapot testreszabott lapra.

>[!NOTE]
>Az IIS telepítéséhez használhatja a **Kiszolgálókezelő** **szerepkörök és szolgáltatások hozzáadása varázslóját** is. 

**Az IIS telepítése és az alapértelmezett weblap frissítése a PowerShell-lel:**

1. A MyVM1 és a MyVM2 alkalmazásban indítsa el a **Windows PowerShellt** a **Start** menüből. 

2. A következő parancsok futtatásával telepítse az IIS-t, és cserélje le az alapértelmezett IIS-weblapot:
   
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
   
   Megjelenik a testreszabott IIS-webkiszolgáló alapértelmezett lapja a böngészőben. Az üzenet beolvassa **"Helló világ!" alkalmazás a MyVM1-ból**, vagy **"Helló világ!" alkalmazás a MyVM2**.
   
1. Frissítse a böngészőt, és tekintse meg, hogy a terheléselosztó osztja el a forgalmat a virtuális gépek között. Előfordulhat, hogy a próbálkozások között törölnie kell a böngésző gyorsítótárát is.

   Előfordulhat, hogy a **MyVM1** lap megjelenik, és más esetekben a **MyVM2** oldal jelenik meg, mivel a terheléselosztó az egyes háttérbeli virtuális gépekhez továbbítja a kéréseket. 

   ![Új IIS alapértelmezett lap](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A terheléselosztó és az összes kapcsolódó erőforrás törléséhez, ha már nincs szüksége rájuk, nyissa meg a **myresourcegrouplb erőforráscsoportban** erőforráscsoportot, és válassza az **erőforráscsoport törlése**elemet.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy alapszintű belső Load balancert. Létrehozta és konfigurálta a hálózati erőforrásokat, a háttér-kiszolgálókat, az állapot-mintavételt és a terheléselosztó szabályait. Telepítette az IIS-t a háttérbeli virtuális gépeken, és egy tesztelési virtuális gépet használ a terheléselosztó teszteléséhez a böngészőben. 

Ezután megismerheti a virtuális gépek terheléselosztását a rendelkezésre állási zónák között.

> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
