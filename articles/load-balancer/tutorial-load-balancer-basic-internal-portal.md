---
title: 'Oktatóanyag: Belső terheléselosztó létrehozása – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy belső alapszintű terheléselosztót az Azure Portal használatával.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096128"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Oktatóanyag: A belső forgalom terhelésének elosztása az Azure Portalon lévő alapszintű terheléselosztóval

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít a bejövő kérelmek virtuális gépek közötti szétosztásával. Az Azure Portal segítségével hozzon létre egy alapszintű terheléselosztót, és egyensúlyozhatja ki a belső forgalmat a virtuális gépek között. Ez az oktatóanyag bemutatja, hogyan hozhat létre és konfigurálhat belső terheléselosztót, háttérkiszolgálókat és hálózati erőforrásokat az alapszintű tarifaszinten.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

Ha szeretné, ezeket a lépéseket az [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) vagy az [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) használatával teheti meg a portál helyett.

Az oktatóanyag használatával a lépések végrehajtásához jelentkezzen [https://portal.azure.com](https://portal.azure.com)be az Azure Portalra a .

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Virtuális hálózat, háttérkiszolgálók és tesztvirtuális gép létrehozása

Először hozzon létre egy virtuális hálózatot (VNet). A virtuális hálózatban hozzon létre két virtuális gépet az alapszintű terheléselosztó háttérkészletéhez, és egy harmadik virtuális gépet a terheléselosztó teszteléséhez. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A portál bal felső részén válassza az **Erőforrás** > **létrehozása virtuális** > **hálózat**létrehozása lehetőséget.
   
1. A **Virtuális hálózat létrehozása** ablaktáblán írja be vagy jelölje ki a következő értékeket:
   
   - **Név**: Írja be a *MyVNet*nevet.
   - **ResourceGroup**: Válassza **az Új létrehozása**lehetőséget, majd írja be a *MyResourceGroupLB*parancsot, és válassza **az OK**gombot. 
   - **Alhálózat** > **neve**: Írja be a *MyBackendSubnet*nevet.
   
1. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A portál bal felső részén válassza az **Erőforrás** > létrehozása**Számítási** > **Windows Server 2016 Datacenter lehetőséget.** 
   
1. A **Virtuális gép létrehozása**csoportban írja be vagy válassza ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport:** Legördülő menü válassza a **MyResourceGroupLB**lehetőséget.
   - **Példány részletei** > **Virtuális gép neve**: Írja be a *MyVM1*nevet.
   - **Példány részleteinek** > **elérhetőségi beállításai:** 
     1. Legördülő menü válassza **az Elérhetőségi készlet lehetőséget.** 
     2. Válassza **az Új létrehozása**lehetőséget, írja be a *SajatKészlet parancsot,* és válassza az **OK**gombot.
   
1. Válassza a **Hálózat** lapot, vagy válassza **a Tovább: Lemezek**, majd a **Tovább: Hálózat**lehetőséget. 
   
   Győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **MyVNet**
   - **Alhálózat**: **MyBackendSubnet**
   
   A **Hálózati biztonsági csoport csoport csoportban:**
   1. Válassza az **Advanced** (Speciális) lehetőséget. 
   1. Legördülő **menü A hálózati biztonsági csoport konfigurálása** és a Nincs lehetőséget válassza a **Nincs**lehetőséget. 
   
1. Válassza a **Kezelés** lapot, vagy válassza a **Következő** > **kezelés**lehetőséget. A **Figyelés**csoportban állítsa a **Rendszerindításdiadia -t** **Ki**beállításra.
   
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
1. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.** 

1. Kövesse a lépéseket, hogy hozzon létre egy második virtuális gép nevű *MyVM2,* az összes többi beállítás ugyanaz, mint a MyVM1. 

1. Kövesse ismét a lépéseket, hogy hozzon létre egy harmadik virtuális gép nevű *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Alapszintű terheléselosztó létrehozása

Hozzon létre egy alapszintű belső terheléselosztót a portál használatával. A létrehozott név és IP-cím automatikusan konfigurálva lesz a terheléselosztó előtérként.

1. A portál bal felső részén válassza az **Erőforrás** > **hálózati** > **terheléselosztó**létrehozása lehetőséget.
   
2. A **Terheléselosztó létrehozása** lap **Alapjai** lapján adja meg vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd válassza a **Véleményezés + létrehozás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza **az Új létrehozása lehetőséget,** és írja be a *MyResourceGroupLB* parancsot a szövegmezőbe.|
    | Név                   | *myLoadBalancer*                                   |
    | Régió         | Válassza az **USA 2. keleti régiója** lehetőséget.                                        |
    | Típus          | Válassza a **Belső**lehetőséget.                                        |
    | SKU           | Válassza az **Alapszintű** lehetőséget.                          |
    | Virtuális hálózat           | Válassza a *MyVNet*lehetőséget.                          |    
    | IP-cím hozzárendelése              | Válassza a **Statikus**lehetőséget.   |
    | Privát IP-cím|Írja be a virtuális hálózat és az alhálózat címterében található címet, például *10.3.0.7*.  |

3. A **Véleményezés + létrehozás** lapon kattintson a **Létrehozás gombra.** 
   

## <a name="create-basic-load-balancer-resources"></a>Alapvető terheléselosztó-erőforrások létrehozása

Ebben a szakaszban konfigurálhatja a háttércímkészlet és az állapotminta terheléselosztó beállításait, és megadhatja a terheléselosztó-szabályokat.

### <a name="create-a-back-end-address-pool"></a>Háttércímkészlet létrehozása

A forgalom elosztása a virtuális gépek, a terheléselosztó egy háttér-címkészletet használ. A háttércímkészlet a terheléselosztóhoz csatlakoztatott virtuális hálózati adapterek IP-címeit tartalmazza. 

**Virtuális gép1-et és VM2-t tartalmazó háttércímkészlet létrehozása:**

1. Válassza a bal oldali menü **Összes erőforrás elemét,** majd az erőforráslistából válassza a **MyLoadBalancer** elemet.
   
1. A **Beállítások** alatt válassza a **Háttérkészletek**, majd a **Hozzáadás** lehetőséget.
   
1. A **Háttérkészlet hozzáadása** lapon írja be vagy jelölje ki a következő értékeket:
   
   - **Név**: Írja be a *MyBackendPool*nevet.
   - **Kapcsolódó**: Legördülő menü és válassza **a Virtuális gép**lehetőséget.
   
   
1. Válassza a **Virtuális gép**lehetőséget. 
   1. Adja hozzá **a MyVM1-et** és a **MyVM2-t** a háttérkészlethez.
   2. Az egyes gépek hozzáadása után válassza ki a hálózati IP-konfigurációt, és válassza ki a **hálózati IP-konfigurációt.** 
   
   >[!NOTE]
   >Ne adjon **MyTestVM-et** a készlethez. 
   
1. Válassza **az OK gombot.**
   
   ![A háttércímkészlet hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. A **háttér-készletek lapon bontsa** ki a **MyBackendPool** csomópontot, és győződjön meg arról, hogy mind a **VM1,** mind a **VM2** szerepel a listában.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a terheléselosztó figyelze a virtuális gép állapotát, használjon állapotminta. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. 

**Hozzon létre egy állapotminta a virtuális gépek állapotának figyelésére:**

1. Válassza a bal oldali menü **Összes erőforrás elemét,** majd az erőforráslistából válassza a **MyLoadBalancer** elemet.
   
1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
   
1. Az **Állapotminta hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: Írja be a *MyHealthProbe*nevet.
   - **Protokoll**: Legördülő menü válassza a **HTTP**lehetőséget. 
   - **Port**: *80-as*típus. 
   - **Elérési**út */* : Fogadja el az alapértelmezett URI-t. Ezt az értéket bármely más URI-ra lecserélheti. 
   - **Időköz**: *15-* ös típus. Időköz A mintavételi kísérletek közötti másodpercek száma.
   - **Nem megfelelő küszöbérték:** *2-* es típus . Ez az érték a virtuális gép nem megfelelő állapotúnak minősülő, egymást követő mintavételi hibák száma.
   
1. Válassza **az OK gombot.**
   
   ![Mintavétel hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A szabály határozza meg a bejövő forgalom előtér-IP-konfigurációját, a forgalom fogadásához szükséges háttér-IP-készletet, valamint a szükséges forrás- és célportokat. 

A **MyLoadBalancerRule** nevű terheléselosztó szabály a front-end **LoadBalancerFrontEnd**80-as portját figyeli. A szabály hálózati forgalmat küld a Back-end címkészlet **MyBackendPool**, szintén a port 80. 

**A terheléselosztó szabály létrehozása:**

1. Válassza a bal oldali menü **Összes erőforrás elemét,** majd az erőforráslistából válassza a **MyLoadBalancer** elemet.
   
1. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. A **Terheléselosztási szabály hozzáadása** lapon írja be vagy válassza ki a következő értékeket, ha még nincsenek jelen:
   
   - **Név**: Írja be a *MyLoadBalancerRule nevet.*
   - **Előtér IP-címe:** Írja be *a LoadBalancerFrontEnd típust,* ha nincs jelen.
   - **Protokoll**: Válassza a **TCP**lehetőséget.
   - **Port**: *80-as*típus.
   - **Háttérport**: *80-as*típus .
   - **Háttérkészlet:** Válassza a **MyBackendPool lehetőséget.**
   - **Egészségügyi szonda**: Válassza a **MyHealthProbe**lehetőséget. 
   
1. Válassza **az OK gombot.**
   
   ![Terheléselosztási szabály hozzáadása](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Telepítse az Internet Information Services (IIS) szolgáltatást a háttérkiszolgálókra, majd a MyTestVM segítségével tesztelje a terheléselosztót a saját IP-címével. Minden háttérvirtuális gép az alapértelmezett IIS-weblap egy másik verzióját szolgálja ki, így láthatja a terheléselosztó a két virtuális gép közötti elosztási kérelmeket.

A portálon a **MyLoadBalancer** **Áttekintő lapján** keresse meg az IP-címét a **Privát IP-cím**alatt. Mutasson a címre, és a **másoláshoz** válassza a Másolás ikont. Ebben a példában **ez 10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Csatlakozás a virtuális gépekhez rdp-vel

Először csatlakozzon mindhárom távoli asztallal (RDP) rendelkező virtuális géphez. 

>[!NOTE]
>Alapértelmezés szerint a virtuális gépek már rendelkeznek az **RDP** (Távoli asztal) porttal a távoli asztali hozzáférés engedélyezéséhez. 

**Távoli asztal (RDP) a virtuális gépek:**

1. A portálon válassza a bal oldali menü **Minden erőforrás parancsát.** Az erőforráslistából válassza ki a **MyResourceGroupLB** erőforráscsoport minden virtuális gépét.
   
1. Az **Áttekintés** lapon válassza a **Csatlakozás**lehetőséget, majd az **RDP-fájl letöltése**lehetőséget. 
   
1. Nyissa meg a letöltött RDP-fájlt, és válassza a **Csatlakozás**lehetőséget.
   
1. A Windows biztonság a képernyőn válassza a **További lehetőségek** lehetőséget, majd **használjon másik fiókot**. 
   
   Írja be a felhasználónevet és a jelszót, majd kattintson **az OK gombra.**
   
1. Válaszoljon **igennel** bármelyik tanúsítványkérdésre. 
   
   A virtuális gép asztala új ablakban nyílik meg. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Az IIS telepítése és az alapértelmezett IIS lap cseréje a háttérvirtuális gépeken

Minden háttérkiszolgálón a PowerShell segítségével telepítse az IIS-t, és cserélje le az alapértelmezett IIS-weblapot egy testreszabott lapra.

>[!NOTE]
>A **Kiszolgálókezelő** **Szerepkörök és szolgáltatások hozzáadása varázslójával** telepítheti az IIS-t is. 

**Az IIS telepítése és az alapértelmezett weblap frissítése a PowerShell használatával:**

1. A MyVM1 és a MyVM2 rendszeren indítsa el a **Windows PowerShellt** a **Start** menüből. 

2. Az IIS telepítéséhez és az alapértelmezett IIS-weblap cseréjéhez futtassa a következő parancsokat:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Zárja be az RDP-kapcsolatokat a MyVM1 és a MyVM2 kapcsolattal a **Kapcsolat bontása**gombra kattintva. Ne állítsa le a virtuális gépeket.

### <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. A MyTestVM-en nyissa meg **az Internet Explorer**t, és válaszoljon az OK **gombra** a konfigurációs utasításokra. 
   
1. Illessze be vagy írja be a terheléselosztó privát IP-címét (*10.3.0.7*) a böngésző címsorába. 
   
   A böngészőben megjelenik az IIS webkiszolgáló testreszabott alapértelmezett lapja. Az üzenet a **Hello World -tól MyVM1**, vagy **a Hello World a MyVM2**.
   
1. Frissítse a böngészőt, hogy a terheléselosztó elosztja a forgalmat a virtuális gépek között. Előfordulhat, hogy a kísérletek között törölnie kell a böngésző gyorsítótárát.

   Néha a **MyVM1** oldal jelenik meg, és más alkalommal a **MyVM2** oldal jelenik meg, mint a terheléselosztó osztja a kérelmeket az egyes háttér-virtuális gépek. 

   ![Új IIS alapértelmezett lap](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a terheléselosztót és az összes kapcsolódó erőforrást, amikor már nincs rájuk szüksége, nyissa meg a **MyResourceGroupLB** erőforráscsoportot, és válassza **az Erőforráscsoport törlése lehetőséget.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy alapszintű belső terheléselosztót. Hálózati erőforrásokat, háttérkiszolgálókat, állapotminta és a terheléselosztó szabályait hozott létre és konfigurált. Telepítette az IIS-t a háttérvirtuális gépeken, és egy teszt virtuális gépet használt a terheléselosztó teszteléséhez a böngészőben. 

Ezután megtudhatja, hogyan töltheti el a virtuális gépek et a rendelkezésre állási zónák között.

> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
