---
title: 'Oktatóanyag: Porttovábbítást konfigurálása az Azure Load Balancer az Azure portal használatával'
titlesuffix: Azure Load Balancer
description: Az oktatóanyag bemutatja, hogyan hozhat létre kapcsolatokat a virtuális gépek Azure-beli virtuális hálózathoz Azure Load Balancer használatával operacionalizálásra konfigurálása.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 6cb9e839b1fffd29ce1d78e82fb4ab054b92efc6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959125"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Oktatóanyag: Porttovábbítást konfigurálása az Azure Load Balancer a portál használatával

Porttovábbítást lehetővé teszi a csatlakozást a virtuális gépek (VM) az Azure-beli virtuális hálózathoz használatával egy Azure Load Balancer nyilvános IP-cím és port számát. 

Ebben az oktatóanyagban, állítson be porttovábbítást a egy Azure Load Balancert. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy nyilvános Standard load balancer a hálózati forgalom terheléselosztása virtuális gépen. 
> * Hozzon létre egy virtuális hálózatot és virtuális gépek egy hálózati biztonsági csoport (NSG alapuló) szabály. 
> * A virtuális gépeket ad hozzá a terheléselosztó háttér-címkészletét.
> * A load balancer állapotfigyelő mintavételi és a forgalom szabályokat létrehozni.
> * Hozzon létre a terheléselosztó bejövő NAT-szabályok – operacionalizálásra.
> * IIS telepítése és konfigurálása a virtuális gépeken nézet terheléselosztás és a működés operacionalizálásra.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

Ebben az oktatóanyagban az összes lépéseket, jelentkezzen be az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Standard load balancer létrehozása

Először hozzon létre egy nyilvános Standard load balancer, amely meg tudja osztani a forgalom terhelést a virtuális gépen. Standard load balancer támogatja a csak a Standard nyilvános IP-címet. Standard load balancer létrehozásakor is létrehozhat új Standard nyilvános IP-címet, amely van konfigurálva, a terheléselosztó előtérrendszerhez, és nevű **LoadBalancerFrontEnd** alapértelmezés szerint. 

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Terheléselosztó** elemre.
2. Az a **alapjai** lapján a **terheléselosztó létrehozása** lap, adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, és válassza **felülvizsgálat +létrehozása**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza ki **új létrehozása** , és írja be *MyResourceGroupLB* a szövegmezőben.|
    | Name (Név)                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Typo          | Válassza ki **nyilvános**.                                        |
    | SKU           | Válassza ki **Standard**.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím neve              | Típus *myPublicIP* a szövegmezőben.   |
    |Rendelkezésre állási zóna| Válassza ki **Zónaredundáns**.    |
     
    >[!NOTE]
     >Győződjön meg arról, hogy a terheléselosztó és az összes erőforrás létrehozásához, egy helyen, amely támogatja a rendelkezésre állási zónák. További információkért lásd: [rendelkezésre állási zónákat támogató régiók](../availability-zones/az-overview.md#regions-that-support-availability-zones). 

3. Az a **felülvizsgálat + létrehozása** lapra, majd **létrehozás**.  
  
## <a name="create-and-configure-back-end-servers"></a>Hozzon létre, és a háttér-kiszolgálók konfigurálása

Virtuális hálózat létrehozása két virtuális gépet, és a virtuális gépeket ad hozzá a terheléselosztó háttérkészlethez. 

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A portál bal felső oldalán válassza **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.
   
1. Az a **virtuális hálózat létrehozása** panelen adja meg ezeket az értékeket:
   
   - **Név**: Típus *MyVNet*.
   - **Erőforráscsoport**: Legördülő lista **válasszon meglévő** válassza **MyResourceGroupLB**. 
   - **Alhálózat** > **neve**: Típus *MyBackendSubnet*.
   
1. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-port-forwarding-portal/2-load-balancer-virtual-network.png)

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Hozzon létre virtuális gépet, és hozzáadhatja őket a load balancer háttérkészlethez

1. A portál bal felső oldalán válassza **erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter**. 
   
1. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:
   - **Előfizetés** > **erőforráscsoport**: Legördülő listára, és válassza ki **MyResourceGroupLB**.
   - **A virtuális gép neve**: Típus *MyVM1*.
   - **Régió**: Válassza a **Nyugat-Európa** régiót. 
   - **Felhasználónév**: Típus *azureuser*.
   - **Jelszó**: Típus *Azure1234567*. 
     Írja be újra a jelszót a **jelszó megerősítése** mező.
   
1. Válassza ki a **hálózatkezelés** lapon, vagy válasszon **tovább: Lemezek**, majd **tovább: Hálózatkezelés**. 
   
   Győződjön meg arról, hogy a következő kijelölt:
   - **Virtuális hálózat**: **MyVNet**
   - **Alhálózat**: **MyBackendSubnet**
   
1. Alatt **nyilvános IP-cím**válassza **új létrehozása**, jelölje be **Standard** a a **nyilvános IP-cím létrehozása** oldalra, és kattintson a **OK**. 
   
1. A **hálózati biztonsági csoport**válassza **speciális** hozhat létre egy új hálózati biztonsági csoport (NSG), egy tűzfal típusú. 
   1. Az a **konfigurálása a hálózati biztonsági csoport** mezőben válassza **új létrehozása**. 
   1. Típus *MyNetworkSecurityGroup*, és válassza ki **OK**. 
   
   >[!NOTE]
   >Figyelje meg, hogy alapértelmezés szerint az NSG-hez már rendelkezik egy bejövő szabályt a 3389-es, a távoli asztali (RDP) port megnyitásához.
   
1. Adja hozzá a virtuális gép egy load balancer háttérkészlet létrehozott:
   
   1. Alatt **LOAD BALANCING** > **helyezze a virtuális gép egy meglévő terheléselosztási megoldás mögött?** válassza **Igen**. 
   1. A **terheléselosztási beállítások**, a legördülő listára, és válassza ki **az Azure load balancer**. 
   1. A **válassza ki a terheléselosztó**, a legördülő listára, és válassza ki **MyLoadBalancer**. 
   1. Alatt **válassza ki a háttérkészlet**, jelölje be **új létrehozása**, majd írja be a *MyBackendPool*, és válassza ki **létrehozása**. 
   
   ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Válassza ki a **felügyeleti** lapon, vagy válasszon **tovább** > **felügyeleti**. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**.
   
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
1. Tekintse át a beállításokat, és ha az érvényesítés sikeres, válassza ki a **létrehozás**. 

1. A lépésekkel hozzon létre egy második virtuális Gépet nevű *MyVM2*, a többi beállítást az azonos MyVM1. 
   
   A **hálózati biztonsági csoport**, kiválasztása után **speciális**, a legördülő listára, és válassza ki a **MyNetworkSecurityGroup** már létrehozott. 
   
   A **válassza ki a háttérkészlet**, győződjön meg arról, hogy **MyBackendPool** van kiválasztva. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Hozzon létre egy NSG-szabályt a virtuális gépek

Hozzon létre egy hálózati biztonsági csoport (NSG) szabály a virtuális gépek internet (HTTP) bejövő kapcsolatok engedélyezése érdekében.

>[!NOTE]
>Alapértelmezés szerint az NSG-hez már rendelkezik egy szabályt, amely megnyitja a 3389-es, a távoli asztali (RDP) port.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájából válassza ki a **MyNetworkSecurityGroup** a a **MyResourceGroupLB** erőforráscsoportot.
   
1. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. Az a **bejövő biztonsági szabály felvétele** párbeszédpanelen adja meg vagy válassza a következőket:
   
   - **forrás**: Válassza ki **Szolgáltatáscímke**.  
   - **Forrás szolgáltatáscímkéje**: Válassza ki **Internet**. 
   - **Célporttartományok**: Típus *80-as*.
   - **Protokoll**: Válassza ki **TCP**. 
   - **A művelet**: Válassza ki **engedélyezése**.  
   - **Prioritás**: Típus *100*. 
   - **Név**: Típus *MyHTTPRule*. 
   - **Leírás**: Típus *HTTP engedélyezése*. 
   
1. Válassza a **Hozzáadás** lehetőséget. 
   
   ![NSG-szabály létrehozása](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a load balancer háttérkészlethez megvizsgálhatja és a egy load balancer állapot mintavételi és a forgalom-szabályok konfigurálása.

### <a name="view-the-back-end-address-pool"></a>A háttér-címkészletet megtekintése

A terheléselosztó osztja el a forgalmat a virtuális gépekhez, használja egy háttér-címkészlet, amely tartalmazza a terheléselosztóhoz csatlakozó virtuális hálózati adaptereket (NIC) IP-címeket. 

A load balancer háttérkészlethez létrehozott és hozzáadott virtuális gépek a virtuális gépek létrehozásakor. Is háttér-készletek létrehozása és hozzáadása vagy virtuális gépek eltávolítása a terheléselosztóból **háttérkészletek** lapot. 

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. A **Beállítások** alatt válassza ki a **Háttérkészletek** elemet.
   
1. Az a **háttérkészletek** lapon, bontsa ki a **MyBackendPool** , és győződjön meg arról, hogy mindkét **VM1** és **VM2** szerepelnek.

1. Válassza ki **MyBackendPool**. 
   
   Az a **MyBackendPool** lap **virtuális gép** és **IP-cím**, eltávolíthatja vagy elérhető virtuális gépeket ad hozzá a készlethez.

Új háttér-címkészletet is létrehozhat kiválasztásával **Hozzáadás** a a **háttérkészletek** lapot.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a load balancer monitorozhassa a virtuális gép állapotát, állapotmintát használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. 

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
   
   ![Mintavétel hozzáadása](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A szabályok meghatározzák az előtérbeli IP-konfigurációt a bejövő forgalmat, a háttérbeli IP-címkészletet, és a forgalom és a szükséges forrás és cél portok. 

A nevű terheléselosztási szabályt **MyLoadBalancerRule** az előtér-alkalmazást a 80-as porton figyel **LoadBalancerFrontEnd**. A szabály küld hálózati forgalmat a háttér-címkészletet **MyBackendPool**, szintén a 80-as porton. 

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. Az a **terheléselosztási szabály hozzáadása** lapon adja meg a következő értékeket:
   
   - **Név**: Típus *MyLoadBalancerRule*.
   - **Protokoll**: Válassza ki **TCP**.
   - **Port**: Típus *80-as*.
   - **Háttérport**: Típus *80-as*.
   - **Háttérkészlet**: Válassza ki **MyBackendPool**.
   - **Az állapotfigyelő mintavételező**: Válassza ki **MyHealthProbe**. 
   
1. Kattintson az **OK** gombra.
   
  ![Adjon hozzá egy terheléselosztó-szabályt](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Bejövő NAT-operacionalizálásra szabály létrehozása

Hozzon létre egy terheléselosztó bejövő hálózati cím címfordítás (NAT) szabályt, amely továbbítja a forgalmat egy adott portot az előtérbeli IP-cím egy háttérbeli virtuális gép egy adott portra.

1. Válassza ki **összes erőforrás** a bal oldali menüben, és válassza ki a **MyLoadBalancer** az erőforrások listájából.
   
1. Alatt **beállítások**válassza **bejövő NAT-szabályok**, majd válassza ki **Hozzáadás**. 
   
1. Az a **bejövő NAT-szabály hozzáadása** lapon adja meg a következő értékeket:
   
   - **Név**: Típus *MyNATRuleVM1*.
   - **Port**: Típus *4221*.
   - **Cél virtuális gép**: Válassza ki **MyVM1** a legördülő listából.
   - **A portleképezés**: Válassza ki **egyéni**.
   - **Port cél**: Típus *3389-es*.
   
1. Kattintson az **OK** gombra.
   
1. Ismételje meg a lépéseket adjon hozzá egy bejövő NAT-szabály nevű *MyNATRuleVM2*révén **Port**: *4222* és **céloldali virtuális gép**: **MyVM2**.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Ebben a szakaszban fog az Internet Information Services (IIS) telepítése a háttér-kiszolgálókon, és testre szabhatja az alapértelmezett weblapot, a gép nevének megjelenítéséhez. Ezt követően a terheléselosztó nyilvános IP-címet fogja használni a terheléselosztó teszteléséhez. 

Minden egyes háttérbeli virtuális gép egy másik verzióját az IIS alapértelmezett weblapjára szolgálja ki, így láthatja a kérések a két virtuális gép között osztja el a terheléselosztó.

### <a name="connect-to-the-vms-with-rdp"></a>Csatlakozhat a virtuális gépekhez RDP-vel

A távoli asztali (RDP) minden virtuális Géphez csatlakozni. 

1. Válassza a portál **összes erőforrás** a bal oldali menüben. Az erőforrások listájából válassza ki az egyes virtuális gépekre a **MyResourceGroupLB** erőforráscsoportot.
   
1. Az a **áttekintése** lapon jelölje be **Connect**, majd válassza ki **töltse le az RDP-fájl**. 
   
1. Nyissa meg az RDP letöltött, majd válasszon **Connect**.
   
1. A Windows biztonsági képernyőn válassza ki a **további lehetőségek** , majd **másik fiók használata**. 
   
   Adjon meg egy felhasználónevet *azureuser* és a jelszó *Azure1234567*, és válassza ki **OK**.
   
1. Válasz **Igen** a minden tanúsítvány-kérdésre. 
   
   A virtuális gép asztalához egy új ablakban nyílik meg. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Telepítse az IIS szolgáltatást, és cserélje le az IIS alapértelmezett weblapjára 

PowerShell használatával telepítse az IIS szolgáltatást, és cserélje le az IIS alapértelmezett weblapjára egy oldal, amely megjeleníti a virtuális gép nevét.

1. MyVM1 és MyVM2, indítsa el a **Windows PowerShell** származó a **Start** menü. 

2. Futtassa az IIS telepítéséhez, és cserélje le az IIS alapértelmezett weblapjára a következő parancsokat:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Zárja be az RDP-kapcsolatok MyVM1 és MyVM2 kiválasztásával **Disconnect**. Ne állítsa le a virtuális gépeket.

### <a name="test-load-balancing"></a>Teszt terheléselosztási

1. A portálon a a **áttekintése** lapján **MyLoadBalancer**, másolja a nyilvános IP-cím alatt **nyilvános IP-cím**. Vigye a kurzort a címet, majd válassza a **másolási** másolja azt a ikonra. Ebben a példában van **40.67.218.235**. 
   
1. Illesszen vagy írjon be a terheléselosztó nyilvános IP-cím (*40.67.218.235*) internet böngésző címsorába. 
   
   A testre szabott IIS kiszolgáló alapértelmezett weblap jelenik meg a böngészőben. Az üzenet beolvasása vagy **Hello World a MyVM1**, vagy **Hello World a MyVM2**.
   
   ![Új IIS alapértelmezett lap](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Frissítse a böngészőt a terheléselosztót, virtuális gép között osztja el a forgalmat. Egyes esetekben a **MyVM1** lap jelenik meg, vagy a **MyVM2** lap jelenik meg, mint a terheléselosztó osztja el a kérelmeket az egyes háttérbeli virtuális.
   
   >[!NOTE]
   >Szükség lehet törölni a böngésző gyorsítótárát, vagy nyisson meg egy új böngészőablakot kísérletek között.

## <a name="test-port-forwarding"></a>A porttovábbítás tesztelése

Porttovábbítást, a háttérbeli virtuális géphez a távoli asztal használatával a terheléselosztó és az előtérbeli portot érték a NAT-szabályban megadott IP-címe segítségével. 

1. A portálon a a **áttekintése** lapján **MyLoadBalancer**, másolja a nyilvános IP-címet. Vigye a kurzort a címet, majd válassza a **másolási** másolja azt a ikonra. Ebben a példában van **40.67.218.235**. 
   
1. Nyisson meg egy parancssort, és a következő paranccsal hozzon létre egy távoli asztali munkamenetet MyVM2, a terheléselosztó nyilvános IP-cím és az előtérbeli portot, amelyet NAT-szabályt a virtuális gép a megadott használatával. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Nyissa meg a letöltött RDP-fájlt, és válassza ki **Connect**.
   
1. A Windows biztonsági képernyőn válassza ki a **további lehetőségek** , majd **másik fiók használata**. 
   
   Adjon meg egy felhasználónevet *azureuser* és a jelszó *Azure1234567*, és válassza ki **OK**.
   
1. Válasz **Igen** a minden tanúsítvány-kérdésre. 
   
   A MyVM2 asztala megnyílik egy új ablakban. 

Az RDP-kapcsolat sikeresen létrejön, mert a bejövő NAT-szabály **MyNATRuleVM2** arra utasítja a 3389-es (RDP-port) MyVM2 a port, a terheléselosztó előtérbeli port 4222 irányuló forgalom továbbítására.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törli a terheléselosztó és az összes kapcsolódó erőforrást, ha már nincs szüksége, nyissa meg a **MyResourceGroupLB** erőforrás-csoport, és válassza **erőforráscsoport törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy nyilvános Standard load balancert. Létrehozott és konfigurált hálózati erőforrásokhoz, háttér-kiszolgálók, az állapotfigyelő mintavételező és a terheléselosztó szabályainak. Az IIS telepítve a háttérbeli virtuális géppel, és használja a terheléselosztó nyilvános IP-címet a terheléselosztó teszteléséhez. Állítsa be, és egy háttérbeli virtuális gép portjához a terheléselosztó a megadott port porttovábbítást tesztelése. 

Azure Load Balancer kapcsolatos további információkért folytassa a terheléselosztó oktatóanyagok több betöltése.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
