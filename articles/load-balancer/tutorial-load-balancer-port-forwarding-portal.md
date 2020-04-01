---
title: 'Oktatóanyag: Portforwarding konfigurálása – Azure portal'
titleSuffix: Azure Load Balancer
description: This tutorial shows how to configure port forwarding using Azure Load Balancer to create connections to VMs in an Azure virtual network.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: e740a65d453a69a987e938a5170ae8e04c7bfe40
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78249877"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Oktatóanyag: Portforwarding konfigurálása az Azure Load Balancer-ben a portál használatával

A porttovábbítás lehetővé teszi, hogy egy Azure-beli virtuális hálózatban lévő virtuális gépekhez csatlakozzon egy Azure Load Balancer nyilvános IP-cím és portszám használatával. 

Ebben az oktatóanyagban egy Azure Load Balancer porttovábbítást állít be. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy nyilvános Standard terheléselosztót a virtuális gépek hálózati forgalmának kiegyensúlyozása érdekében. 
> * Hozzon létre egy virtuális hálózatot és virtuális gépeket egy hálózati biztonsági csoport (NSG) szabállyal. 
> * Adja hozzá a virtuális gépeket a terheléselosztó háttér-címkészletéhez.
> * Hozzon létre egy terheléselosztó állapotminta és a forgalmi szabályok.
> * Hozzon létre terheléselosztó bejövő NAT port-továbbítási szabályokat.
> * Telepítse és konfigurálja az IIS-t a virtuális gépeken a terheléselosztás és a porttovábbítás működés közbeni megtekintéséhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

Az oktatóanyag összes lépéséhez jelentkezzen be [https://portal.azure.com](https://portal.azure.com)az Azure Portalra a .

## <a name="create-a-standard-load-balancer"></a>Standard terheléselosztó létrehozása

Először hozzon létre egy nyilvános Standard terheléselosztót, amely kiegyenlítheti a virtuális gépek forgalmi terhelését. A standard terheléselosztó csak egy szabványos nyilvános IP-címet támogat. Standard terheléselosztó létrehozásakor egy új standard nyilvános IP-címet is létrehoz, amely a terheléselosztó előtér-beállításaként van konfigurálva, és alapértelmezés szerint **LoadBalancerFrontEnd** néven van elnevezve. 

1. A képernyő bal felső részén kattintson az **Erőforrás** > **létrehozása hálózati** > **terheléselosztó**elemre.
2. A **Terheléselosztó létrehozása** lap **Alapjai** lapján adja meg vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd válassza a **Véleményezés + létrehozás**lehetőséget:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza **az Új létrehozása lehetőséget,** és írja be a *MyResourceGroupLB* parancsot a szövegmezőbe.|
    | Név                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | SKU           | Válassza a **Normál**lehetőséget.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím              | Írja be a *myPublicIP szöveget* a szövegmezőbe.   |
    |Rendelkezésreállási zóna| Válassza **a Redundáns zóna**lehetőséget.    |
     
    >[!NOTE]
     >Győződjön meg arról, hogy a terheléselosztót és az összes erőforrást olyan helyen hozza létre, amely támogatja a rendelkezésre állási zónákat. További információt a [Rendelkezésre állási zónákat támogató régiók című témakörben talál.](../availability-zones/az-overview.md#services-support-by-region) 

3. A **Véleményezés + létrehozás** lapon kattintson a **Létrehozás gombra.**  
  
## <a name="create-and-configure-back-end-servers"></a>Háttérkiszolgálók létrehozása és konfigurálása

Hozzon létre egy virtuális hálózatot két virtuális géppel, és adja hozzá a virtuális gépeket a terheléselosztó háttérkészletéhez. 

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroupLB (Meglévő erőforráscsoport kiválasztása) |
| **\<virtuális hálózat neve>** | myVNet          |
| **\<régiónév>**          | Nyugat-Európa      |
| **\<IPv4-címtér>**   | 10.3.0.0\16          |
| **\<alhálózat-név>**          | myBackendSubnet        |
| **\<alhálózati címtartomány>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Virtuális gépek létrehozása és hozzáadása a terheléselosztó háttérkészletéhez

1. A portál bal felső részén válassza az **Erőforrás** > létrehozása**Számítási** > **Windows Server 2016 Datacenter lehetőséget.** 
   
1. A **Virtuális gép létrehozása**csoportban írja be vagy válassza ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport:** Legördülő menü válassza a **MyResourceGroupLB**lehetőséget.
   - **Virtuális gép neve**: Típus *MyVM1*.
   - **Régió**: Válassza **Nyugat-Európa**lehetőséget. 
   - **Felhasználónév**: Írja be *az azureuser*nevet.
   - **Jelszó**: Írja be *az Azure1234567 típust.* 
     Írja be újra a jelszót a **Jelszó megerősítése** mezőbe.
   
1. Válassza a **Hálózat** lapot, vagy válassza **a Tovább: Lemezek**, majd a **Tovább: Hálózat**lehetőséget. 
   
   Győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **MyVNet**
   - **Alhálózat**: **MyBackendSubnet**
   
1. A **Nyilvános IP csoportban**válassza **az Új létrehozása**lehetőséget, válassza a Nyilvános **IP-cím létrehozása** lapon a **Szokásos** lehetőséget, majd kattintson az **OK gombra.** 
   
1. A **Hálózati biztonsági csoport csoportban**válassza a **Speciális** lehetőséget egy új hálózati biztonsági csoport (NSG) létrehozásához, amely egy tűzfaltípus. 
   1. A **Hálózati biztonsági csoport konfigurálása** mezőben válassza **az Új létrehozása lehetőséget.** 
   1. Írja be a *MyNetworkSecurityGroup*parancsot, és válassza **az OK gombot.** 
   
   >[!NOTE]
   >Vegye figyelembe, hogy alapértelmezés szerint az NSG már rendelkezik egy bejövő szabállyal a 3389-es port, a távoli asztali (RDP) port megnyitásához.
   
1. Adja hozzá a virtuális gép egy terheléselosztó háttérkészlet, amely létrehozta:
   
   1. **Yes**Terheléselosztás: **LOAD BALANCING** > **Helyezze ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögé?** 
   1. A **Terheléselosztási lehetőségek**esetében legördülő menü válassza az **Azure terheléselosztó lehetőséget.** 
   1. A **Terheléselosztó kiválasztása**területen válassza a **MyLoadBalancer**lehetőséget. 
   1. A **Háttérkészlet kiválasztása**csoportban válassza **az Új létrehozása**lehetőséget, majd írja be a *MyBackendPool*parancsot, és válassza a **Létrehozás lehetőséget.** 
   
   ![Virtuális hálózat létrehozása](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Válassza a **Kezelés** lapot, vagy válassza a **Következő** > **kezelés**lehetőséget. A **Figyelés**csoportban állítsa a **Rendszerindításdiadia -t** **Ki**beállításra.
   
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
1. Tekintse át a beállításokat, és ha az ellenőrzés sikeres, válassza a **Létrehozás gombot.** 

1. Kövesse a lépéseket, hogy hozzon létre egy második virtuális gép nevű *MyVM2,* az összes többi beállítás ugyanaz, mint a MyVM1. 
   
   A **Hálózati biztonsági csoport**csoport esetén a **Speciális**lehetőséget követően válassza a legördülő menüt, és válassza a már létrehozott **MyNetworkSecurityGroup csoportot.** 
   
   A **Háttérkészlet kiválasztása**csoportban győződjön meg arról, hogy a **MyBackendPool** jelölőnégyzet be van jelölve. 

### <a name="create-an-nsg-rule-for-the-vms"></a>NSG-szabály létrehozása a virtuális gépekhez

Hozzon létre egy hálózati biztonsági csoport (NSG) szabályt a virtuális gépek számára a bejövő internetes (HTTP) kapcsolatok engedélyezéséhez.

>[!NOTE]
>Alapértelmezés szerint az NSG már rendelkezik egy olyan szabállyal, amely megnyitja a 3389-es portot, a távoli asztali (RDP) portot.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforráslistában válassza a **MyResourceGroupLB** erőforráscsoport **MyNetworkSecurityGroup** elemét.
   
1. A **Beállítások** területen válassza a **Bejövő biztonsági szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. A **Bejövő biztonsági szabály hozzáadása** párbeszédpanelen írja be vagy válassza a következőt:
   
   - **Forrás**: Válassza a **Service Tag lehetőséget.**  
   - **Forrásszolgáltatás címke**: Válassza az **Internet**lehetőséget. 
   - **Célport tartományok**: *80-as*típus .
   - **Protokoll**: Válassza a **TCP**lehetőséget. 
   - **Művelet**: Válassza az **Allow**lehetőséget.  
   - **Prioritás**: *100-as*típus . 
   - **Név**: Írja be a *MyHTTPRule nevet*. 
   - **Leírás**: Írja be *a HTTP engedélyezése*. 
   
1. Válassza a **Hozzáadás** lehetőséget. 
   
   ![NSG-szabály létrehozása](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban megvizsgálja a terheléselosztó háttérkészletét, és konfigurálja a terheléselosztó állapotminta és a forgalmi szabályok.

### <a name="view-the-back-end-address-pool"></a>A háttércímkészlet megtekintése

A virtuális gépek közötti forgalom elosztásához a terheléselosztó egy háttér-címkészletet használ, amely a terheléselosztóhoz csatlakoztatott virtuális hálózati adapterek (NIC-k) IP-címeit tartalmazza. 

A terheléselosztó háttérkészletét hozta létre, és virtuális gépeket adott hozzá a virtuális gépek létrehozásakor. Háttérkészleteket is létrehozhat, és virtuális gépeket is hozzáadhat vagy eltávolíthat a terheléselosztó **háttérkészletek** lapjáról. 

1. Válassza a bal oldali menü **Összes erőforrás elemét,** majd az erőforráslistából válassza a **MyLoadBalancer** elemet.
   
1. A **Beállítások csoportban**válassza **a Háttérkészletek lehetőséget.**
   
1. A **háttér-készletek lapon bontsa** ki a **MyBackendPool** csomópontot, és győződjön meg arról, hogy mind a **VM1,** mind a **VM2** szerepel a listában.

1. Válassza a **MyBackendPool lehetőséget.** 
   
   A **MyBackendPool** lapon a **VIRTUÁLIS GÉP** és **AZ IP-CÍM**csoportban eltávolíthatja vagy hozzáadhatja a készlethez az elérhető virtuális gépeket.

Új háttérkészleteket úgy hozhat létre, hogy a Háttérkészletek lapon a **Hozzáadás** lehetőséget **választja.**

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a terheléselosztó figyelze a virtuális gép állapotát, használjon állapotminta. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. 

1. Válassza a bal oldali menü **Összes erőforrás elemét,** majd az erőforráslistából válassza a **MyLoadBalancer** elemet.
   
1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
   
1. Az **Állapot hozzáadása mintavétel** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: Írja be a *MyHealthProbe*nevet.
   - **Protokoll**: Legördülő menü válassza a **HTTP**lehetőséget. 
   - **Port**: *80-as*típus. 
   - **Elérési**út */* : Fogadja el az alapértelmezett URI-t. Ezt az értéket bármely más URI-ra lecserélheti. 
   - **Időköz**: *15-* ös típus. Időköz A mintavételi kísérletek közötti másodpercek száma.
   - **Nem megfelelő küszöbérték:** *2-* es típus . Ez az érték a virtuális gép nem megfelelő állapotúnak minősülő, egymást követő mintavételi hibák száma.
   
1. Válassza **az OK gombot.**
   
   ![Mintavétel hozzáadása](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabály megadja, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A szabály határozza meg a bejövő forgalom előtér-IP-konfigurációját, a forgalom fogadásához szükséges háttér-IP-készletet, valamint a szükséges forrás- és célportokat. 

A **MyLoadBalancerRule** nevű terheléselosztó szabály a front-end **LoadBalancerFrontEnd**80-as portját figyeli. A szabály hálózati forgalmat küld a Back-end címkészlet **MyBackendPool**, szintén a port 80. 

1. Válassza a bal oldali menü **Összes erőforrás elemét,** majd az erőforráslistából válassza a **MyLoadBalancer** elemet.
   
1. A **Beállítások** területen válassza a **Terheléselosztási szabályok** elemet, majd a **Hozzáadás** lehetőséget.
   
1. A **Terheléselosztási szabály hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: Írja be a *MyLoadBalancerRule nevet.*
   - **Protokoll**: Válassza a **TCP**lehetőséget.
   - **Port**: *80-as*típus.
   - **Háttérport**: *80-as*típus .
   - **Háttérkészlet:** Válassza a **MyBackendPool lehetőséget.**
   - **Egészségügyi szonda**: Válassza a **MyHealthProbe**lehetőséget. 
   
1. Válassza **az OK gombot.**
   
   ![Terheléselosztási szabály hozzáadása](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Bejövő NAT porttovábbítási szabály létrehozása

Hozzon létre egy terheléselosztó bejövő hálózati címfordítási (NAT) szabályt az előtér-IP-cím adott portjáról egy háttérvirtuális gép adott portjára történő továbbításhoz.

1. Válassza a bal oldali menü **Minden erőforrás elemét,** majd válassza a **MyLoadBalancer** elemet az erőforráslistából.
   
1. A **Beállítások csoportban**válassza **a Bejövő NAT-szabályok**lehetőséget, majd a **Hozzáadás**lehetőséget. 
   
1. A **Bejövő NAT-szabály hozzáadása** lapon írja be vagy jelölje ki a következő értékeket:
   
   - **Név**: Írja be *a MyNATRuleVM1 nevet.*
   - **Port**: *4221-es*típus.
   - **Cél virtuális gép**: Válassza a **MyVM1** elemet a legördülő menüből.
   - **Hálózati IP-konfiguráció**: Válassza ki **az ipconfig1** elemet a legördülő menüből.
   - **Portleképezés**: Válassza az **Egyéni**lehetőséget.
   - **Célport:** *3389-es*típus.
   
1. Válassza **az OK gombot.**
   
1. Ismételje meg a lépéseket a *MyNATRuleVM2*nevű bejövő NAT-szabály hozzáadásához a **Port**: *4222* és **a Cél virtuális gép**: **MyVM2**használatával.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Ebben a szakaszban az Internet Information Services (IIS) szolgáltatást a háttérkiszolgálókra telepíti, és testreszabja az alapértelmezett weblapot a számítógép nevének megjelenítéséhez. Ezután a terheléselosztó nyilvános IP-címét fogja használni a terheléselosztó teszteléséhez. 

Minden háttérvirtuális gép az alapértelmezett IIS-weblap egy másik verzióját szolgálja ki, így láthatja a terheléselosztó a két virtuális gép közötti elosztási kérelmeket.

### <a name="connect-to-the-vms-with-rdp"></a>Csatlakozás a virtuális gépekhez rdp-vel

Csatlakozzon az egyes virtuális gépektávoli asztal (RDP). 

1. A portálon válassza a bal oldali menü **Minden erőforrás parancsát.** Az erőforráslistából válassza ki a **MyResourceGroupLB** erőforráscsoport minden virtuális gépét.
   
1. Az **Áttekintés** lapon válassza a **Csatlakozás**lehetőséget, majd az **RDP-fájl letöltése**lehetőséget. 
   
1. Nyissa meg a letöltött RDP-fájlt, és válassza a **Csatlakozás**lehetőséget.
   
1. A Windows biztonság a képernyőn válassza a **További lehetőségek** lehetőséget, majd **használjon másik fiókot**. 
   
   Írja be az *azureuser* felhasználó és jelszó *azure1234567*felhasználónevét, és válassza **az OK gombot.**
   
1. Válaszoljon **igennel** bármelyik tanúsítványkérdésre. 
   
   A virtuális gép asztala új ablakban nyílik meg. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Az IIS telepítése és az alapértelmezett IIS-weblap cseréje 

A PowerShell segítségével telepítse az IIS-t, és cserélje le az alapértelmezett IIS-weblapot egy olyan lapra, amely a virtuális gép nevét jeleníti meg.

1. A MyVM1 és a MyVM2 rendszeren indítsa el a **Windows PowerShellt** a **Start** menüből. 

2. Az IIS telepítéséhez és az alapértelmezett IIS-weblap cseréjéhez futtassa a következő parancsokat:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Zárja be az RDP-kapcsolatokat a MyVM1 és a MyVM2 kapcsolattal a **Kapcsolat bontása**gombra kattintva. Ne állítsa le a virtuális gépeket.

### <a name="test-load-balancing"></a>Terheléselosztás tesztelése

1. A portálon a **MyLoadBalancer** **Áttekintés** lapján másolja a nyilvános IP-címet a **Nyilvános IP-cím**alá. Mutasson a címre, és a **másoláshoz** válassza a Másolás ikont. Ebben a példában **ez 40.67.218.235**. 
   
1. Illessze be vagy írja be a terheléselosztó nyilvános IP-címét (*40.67.218.235*) az internetböngésző címsorába. 
   
   A böngészőben megjelenik az IIS webkiszolgáló testreszabott alapértelmezett lapja. Az üzenet a **Hello World -tól MyVM1**, vagy **a Hello World a MyVM2**.
   
   ![Új IIS alapértelmezett lap](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Frissítse a böngészőt, hogy a terheléselosztó elosztja a forgalmat a virtuális gépek között. Néha a **MyVM1** oldal jelenik meg, és más alkalommal a **MyVM2** oldal jelenik meg, mint a terheléselosztó osztja a kérelmeket az egyes háttér-virtuális gépek.
   
   >[!NOTE]
   >Előfordulhat, hogy törölnie kell a böngésző gyorsítótárát, vagy meg kell nyitnia egy új böngészőablakot a kísérletek között.

## <a name="test-port-forwarding"></a>A porttovábbítás tesztelése

A porttovábbítással távoli asztalt egy háttérvirtuális gépre irányíthatja a terheléselosztó IP-címével és a NAT-szabályban meghatározott előtér-portértékkel. 

1. A portálon a **MyLoadBalancer** **áttekintése** lapon másolja a nyilvános IP-címét. Mutasson a címre, és a **másoláshoz** válassza a Másolás ikont. Ebben a példában **ez 40.67.218.235**. 
   
1. Nyisson meg egy parancssort, és a következő paranccsal hozzon létre egy távoli asztali munkamenetet a MyVM2-vel a terheléselosztó nyilvános IP-címével és a virtuális gép NAT-szabályában megadott előtér-porttal. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Nyissa meg a letöltött RDP-fájlt, és válassza a **Csatlakozás**lehetőséget.
   
1. A Windows biztonság a képernyőn válassza a **További lehetőségek** lehetőséget, majd **használjon másik fiókot**. 
   
   Írja be az *azureuser* felhasználó és jelszó *azure1234567*felhasználónevét, és válassza **az OK gombot.**
   
1. Válaszoljon **igennel** bármelyik tanúsítványkérdésre. 
   
   A MyVM2 asztal új ablakban nyílik meg. 

Az RDP-kapcsolat sikeres, mert a bejövő NAT szabály **MyNATRuleVM2** irányítja a forgalmat a terheléselosztó előtér-port 4222 a MyVM2 port 3389 (az RDP port).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a terheléselosztót és az összes kapcsolódó erőforrást, amikor már nincs rájuk szüksége, nyissa meg a **MyResourceGroupLB** erőforráscsoportot, és válassza **az Erőforráscsoport törlése lehetőséget.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy standard nyilvános terheléselosztót. Hálózati erőforrásokat, háttérkiszolgálókat, állapotminta és a terheléselosztó szabályait hozott létre és konfigurált. Az IIS-t a háttérvirtuális gépekre telepítette, és a terheléselosztó nyilvános IP-címét használta a terheléselosztó teszteléséhez. Beállíthatja és tesztelte a porttovábbítást a terheléselosztó egy adott portjáról egy háttérvirtuális gép egy portjára. 

Ha többet szeretne megtudni az Azure Load Balancer- ről, folytassa a további terheléselosztó-oktatóanyagokat.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
