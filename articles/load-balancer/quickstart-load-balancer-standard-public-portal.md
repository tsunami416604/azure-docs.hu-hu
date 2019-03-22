---
title: 'A rövid útmutató: egy Standard Load Balancer létrehozása – Azure Portalon'
titlesuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Standard Load Balancer az Azure portal használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 77e322e32d19433d9ce4629c2e04c8bbd7e17f3f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858235"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Gyors útmutató: Standard Load Balancer létrehozása a virtuális gépek terhelésének elosztásához az Azure Portal használatával.

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Az Azure Portal használatával létrehozhat egy terheléselosztót a virtuális gépek (VM-ek) terhelésének elosztása érdekében. Ez a rövid útmutató bemutatja, hogyan végezheti el a virtuális gépek terheléselosztását egy Standard Load Balancer használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ebben a szakaszban egy Standard Load Balancer, amellyel a virtuális gépek terheléselosztása hoz létre. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Amikor létrehoz egy Standard Load Balancert, létre kell hoznia egy új standard nyilvános IP-címet is, amely a Standard Load Balancer előtereként van konfigurálva (alapértelmezés szerint *LoadBalancerFrontend* néven). 

1. A képernyő bal felső sarkában válassza **erőforrás létrehozása** > **hálózatkezelés** > **terheléselosztó**.
2. Az a **alapjai** lapján a **terheléselosztó létrehozása** lap, adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, és válassza **felülvizsgálat +létrehozása**:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza ki **új létrehozása** , és írja be *myResourceGroupSLB* a szövegmezőben.|
    | Name (Név)                   | *myLoadBalancer*                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Typo          | Válassza ki **nyilvános**.                                        |
    | SKU           | Válassza ki **Standard**.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím neve              | Típus *myPublicIP* a szövegmezőben.   |
    |Rendelkezésre állási zóna| Válassza ki **Zónaredundáns**.    |
3. Az a **felülvizsgálat + létrehozása** lapon jelölje be **létrehozás**.   

    ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer-erőforrások létrehozása

Ebben a szakaszban egy háttércímkészlet és egy állapotminta terheléselosztó beállításainak konfigurálása, és adja meg a terheléselosztó-szabályt.

### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

Szeretné elosztani a forgalmat a virtuális gépekhez, egy háttércímkészlet tartalmazza a IP-címet a terheléselosztóhoz csatlakoztatott virtuális (NIC) címét. Hozza létre a háttércímkészletet *myBackendPool* internetes forgalom terhelését elosztja a virtuális gépeket tartalmazza.

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd válassza ki **myLoadBalancer** az erőforrások listájában.
2. Alatt **beállítások**válassza **háttérkészletek**, majd **Hozzáadás**.
3. A a **háttérkészlet hozzáadása** neveként, írja be a lapon *myBackendPool*, a háttérkészlet, és válassza ki a nevet **hozzáadása**.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Engedélyezi a Load Balancer monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan eltávolítása vagy virtuális gépeken a terheléselosztó elforgatása az adott válaszuk alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd válassza ki **myLoadBalancer** az erőforrások listájában.
2. Alatt **beállítások**, jelölje be **állapotadat-mintavételek**, majd **Hozzáadás**.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *myHealthProbe*. |
    | Protokoll | Válassza ki **HTTP**. |
    | Port | Adja meg *80-as*.|
    | Intervallum | Adja meg *15* számú **időköz** mintavételi kísérletek közötti másodpercek alatt. |
    | Nem kifogástalan állapot küszöbértéke | Válassza ki *2* számú **nem kifogástalan állapot küszöbértéke** vagy egymást követő mintavételi hiba, amely előtt a virtuális gép nem megfelelő állapotúnak számít.|
    | Állapotadat-mintavétel | Válassza ki *myHealthProbe*. |
4. Kattintson az **OK** gombra.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy terheléselosztó-szabályt *myLoadBalancerRuleWeb* előtér 80-as portját *FrontendLoadBalancer* és terhelés szempontjából elosztott forgalmat a háttércímkészlethez *myBackEndPool* szintén a 80-as port használatával. 

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd válassza ki **myLoadBalancer** az erőforrások listájában.
2. Alatt **beállítások**válassza **terheléselosztási szabályok**, majd **Hozzáadás**.
3. Konfigurálja a terheléselosztási szabályt az alábbi értékekkel:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *myHTTPRule*. |
    | Protokoll | Válassza ki **TCP**. |
    | Port | Adja meg *80-as*.|
    | Háttérport | Adja meg *80-as*. |
    | Háttérkészlet | Válassza ki *myBackendPool*.|
    | Állapotadat-mintavétel | Válassza ki *myHealthProbe*. |
4. Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
4. Kattintson az **OK** gombra.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban egy virtuális hálózat létrehozása, hozzon létre két virtuális gépet a terheléselosztó háttérkészlete számára, és telepítse az IIS a virtuális gépeken a terheléselosztó tesztelése érdekében.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. A képernyő bal felső oldalán válassza **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.

1. A **virtuális hálózat létrehozása**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *myVNet* értéket. |
    | Címtér | Adja meg *10.1.0.0/16*. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza ki a meglévő erőforrás - *myResourceGroupSLB*. |
    | Hely | Válassza a **Nyugat-Európa** régiót.|
    | Alhálózat - név | Adja meg a *myBackendSubnet* értéket. |
    | Alhálózat – címtartomány | Adja meg *10.1.0.0/24*. |
1. Hagyja meg az alapértelmezett beállításokat, és válassza ki a többi **létrehozás**.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
A standard Load Balancer csak támogatja a virtuális gépeket, a háttérkészletben szabványos IP-címmel. Ebben a szakaszban két virtuális gépet hoz létre (*myVM1* és *myVM2*) két különböző zónákhoz tartozhatnak a Standard nyilvános IP-cím (*1. zónában* és *2. zóna*), amely a korábban létrehozott Standard Load Balancer háttérkészlete kerülnek.

1. A portál bal felső oldalán válassza **erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter**. 
   
1. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:
   - **Előfizetés** > **erőforráscsoport**: Select **myResourceGroupSLB**.
   - **Példány részletei** > **virtuális gép neve**: Típus *myVM1*.
   - **Példány részletei** > **régió** > Válasszon **Nyugat-Európa**.
   - **Példány részletei** > **rendelkezésre állási beállítások** > Válasszon **rendelkezésre állási zónák**. 
   - **Példány részletei** > **rendelkezésre állási zónában** > Válasszon **1**.
  
1. Válassza ki a **hálózatkezelés** lapon, vagy válasszon **tovább: Lemezek**, majd **tovább: Hálózatkezelés**. 
   
   - Győződjön meg arról, hogy a következő kijelölt:
       - **Virtuális hálózat**: *myVnet*
       - **Alhálózat**: *myBackendSubnet*
       - **Nyilvános IP-cím** > válassza **új létrehozása**, majd a a **nyilvános IP-cím létrehozása** ablakban a **Termékváltozat**, jelölje be **Standard**, és a **rendelkezésre állási zónában**válassza **zónaredundáns**, majd válassza ki **OK**.
   - Alatt létrehozni egy új hálózati biztonsági csoport (NSG), egy tűzfal, típusú **hálózati biztonsági csoport**válassza **speciális**. 
       1. Az a **konfigurálása a hálózati biztonsági csoport** mezőben válassza **új létrehozása**. 
       1. Típus *myNetworkSecurityGroup*, és válassza ki **OK**.
   - Ahhoz, hogy a virtuális Gépet a terheléselosztó háttérkészletéhez tartozik, a következő lépéseket:
        - A **Load Balancing**, a **helyezze a virtuális gép egy meglévő terheléselosztási megoldás mögött?** válassza **Igen**.
        - A **terheléselosztási beállításokat**, a **terheléselosztási beállítások**válassza **az Azure load balancer**.
        - A **válassza ki a terheléselosztó**, *myLoadBalancer*. 
1. Válassza ki a **felügyeleti** lapon, vagy válasszon **tovább** > **felügyeleti**. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.   
1. Tekintse át a beállításokat, és válassza ki **létrehozás**.
1. A lépésekkel hozzon létre egy második virtuális Gépet nevű *myVM2*, a Standard Termékváltozat nyilvános IP-cím nevű *myVM2-ip*, és **rendelkezésre állási zónában** beállításaként **2** és a többi beállítás ugyanaz, mint *myVM1*. 

### <a name="create-nsg-rule"></a>NSG-szabály létrehozása

Ebben a szakaszban hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt HTTP-n keresztül érkező bejövő kapcsolatok engedélyezéséhez.

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd az erőforrások listájában válassza **myNetworkSecurityGroup** a található**myResourceGroupSLB** erőforráscsoportot.
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
4. Kattintson az **OK** gombra.
 
### <a name="install-iis"></a>Az IIS telepítése

1. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd az erőforrások listájában válassza ki **myVM1** található a  *myResourceGroupSLB* erőforráscsoportot.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. Jelentkezzen be a virtuális gépbe az *azureuser* felhasználónévvel.
4. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
5. A Kiszolgálókezelőben válasza **szerepkörök és szolgáltatások hozzáadása**.
6. A **Szerepkörök és szolgáltatások hozzáadása varázslóban** használja az alábbi értékeket:
    - Az a **telepítés típusának kiválasztása** lapra, jelölje be **szerepköralapú vagy szolgáltatásalapú telepítés**.
    - Az a **célkiszolgáló kijelölése** lapra, jelölje be **myVM1**
    - Az a **kiszolgáló-szerepkör kiválasztása** lapra, jelölje be **webkiszolgáló (IIS)**
    - Kövesse az utasításokat és végezze el a varázsló további lépéseit 
7. Ismételje meg az 1–6. lépéseket a *myVM2* nevű virtuális gép esetében.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Válassza ki **minden szolgáltatás** a bal oldali menüben válassza ki a **összes erőforrás**, majd válassza ki **myPublicIP**.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

      ![IIS-webkiszolgáló](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

Ha szeretné a terheléselosztó az alkalmazást futtató összes három virtuális gép között osztja el a forgalmat, kényszerített frissítését a webböngésző.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, terheléselosztó és minden kapcsolódó erőforrás. Ehhez válassza ki az erőforráscsoportot (*myResourceGroupSLB*), amely tartalmazza a terheléselosztóhoz, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Standard Load Balancer, virtuális gépeket csatolt hozzá, a terheléselosztó forgalmának szabályát, az állapotadat-mintavétel konfigurálva, majd tesztelte a terheléselosztót. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
