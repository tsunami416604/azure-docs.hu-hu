---
title: Oktatóanyag – forgalom irányítása az Azure Traffic Manager webhely válasz javítása |} A Microsoft Docs
description: Ez az oktatóanyag a cikk azt ismerteti, válaszidejű webhelyet szeretne a Traffic Manager-profil létrehozása.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: kumud
ms.openlocfilehash: 89518d30b862e18fb7c989c95144ffa7f1c294fc
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40025017"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Oktatóanyag: A Traffic Managert használva a webhely válasz javítása 

Ebben az oktatóanyagban a webhelyre érkező felhasználói forgalom a legkisebb késéssel rendelkező megkérnek válaszidejű webhely létrehozása a Traffic Manager használatát ismerteti. Az adatközpontban a legkisebb késéssel rendelkező általában egy földrajzi távolság legközelebb eső.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre két virtuális gépet futtató egy alapszintű webhelyet az IIS-kiszolgálón
> * Két teszt virtuális gépek megtekintése a Traffic Manager művelet létrehozása
> * DNS-neve az IIS-t futtató virtuális gépek konfigurálása
> * Webhely jobb teljesítmény érdekében a Traffic Manager-profil létrehozása
> * Virtuális gép végpontok hozzáadása a Traffic Manager-profil
> * A művelet megtekintése a Traffic Manager

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Annak érdekében, hogy a Traffic Manager működés közben látni, ehhez az oktatóanyaghoz központi telepítését a következő:
- két példánya fut méretű alap webhely különböző Azure-régióban – **USA keleti Régiójában** és **Nyugat-Európa**.
- a Traffic Manager - egy virtuális gép a tesztelési virtuális gépek két tesztelése **USA keleti Régiójában** és a második virtuális Gépet a **Nyugat-Európa**. A teszt virtuális gépek segítségével bemutatják, hogyan Traffic Manager átirányítja a felhasználói forgalmat a a webhelyet, amely ugyanabban a régióban fut, a legkisebb késést biztosít.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányokat, amelyek a két Szolgáltatásvégpontok két Azure-régióban a Traffic Manager-profil létrehozásához. A két webhelyek létrehozása a következő lépésekből áll:
1. Hozzon létre két virtuális gép futtatásához egy egyszerű webhely - közül az egyik **USA keleti Régiójában**, és a másik **Nyugat-Európa**.
2. Telepítse az IIS-kiszolgálón az egyes virtuális Gépeken, és frissítse az alapértelmezett webhely-oldal leírja, hogy egy felhasználó csatlakozik a webhely meglátogatása során a virtuális gép neve.

#### <a name="create-vms-for-running-websites"></a>A webhelyek futó virtuális gépek létrehozása
Ebben a szakaszban két virtuális gép létrehozása *myIISVMEastUS* és *myIISVMWEurope* a a **USA keleti Régiójában** és **Nyugat-Európa** Azure-régióban.

1. A felső, jelölje be az Azure portál felső részén található left **erőforrás létrehozása** > **számítási** > **Windows Server 2016 virtuális gép**.
2. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myIISVMEastUS|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza ki **új** és írja be *myResourceGroupTM1*.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|
    |||
4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki az alábbi értékeket a **Beállítások** területen, majd kattintson az **OK** gombra:
    
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| Válassza ki **virtuális hálózati**, a **virtuális hálózat létrehozása**, a **neve**, adja meg *myVNet1*, alhálózat, adja meg a * mySubnet*.|
    |Hálózati biztonsági csoport|Válassza ki **alapszintű**, majd a **nyilvános bejövő portok kiválasztása** legördülő menüben válassza **HTTP** és **RDP** |
    |Rendszerindítási diagnosztika|Válassza ki **letiltott**.|
    |||
6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. Végezze el ismét az 1–6. lépést az alábbi módosításokkal:

    |Beállítás|Érték|
    |---|---|
    |Erőforráscsoport | Válassza ki **új**, majd írja be *myResourceGroupTM2*|
    |Hely|Nyugat-Európa|
    |Virtuális gép neve | myIISVMWEurope|
    |Virtuális hálózat | Válassza ki **virtuális hálózati**, a **virtuális hálózat létrehozása**, a **neve**, adja meg *myVNet2*, alhálózat, adja meg a * mySubnet*.|
    |||
8. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

   ![Virtuális gép létrehozása](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

Ebben a szakaszban az IIS-kiszolgáló telepíthető a két virtuális gép – *myIISVMEastUS*  & *myIISVMWEurope*, majd ezt követően az alapértelmezett webhely oldalt. Az egyéni webhely oldal a virtuális gép, amely csatlakozik egy webböngészőben a webhely megnyitásakor nevét jeleníti meg.

1. Válassza ki **összes erőforrás** a bal oldali menüben, majd az erőforrások listájában kattintson *myIISVMEastUS* található a *myResourceGroupTM1* erőforráscsoportot.
2. Az a **áttekintése** kattintson **Connect**, majd **csatlakozhat a virtuális gép**, jelölje be **töltse le az RDP-fájl**. 
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
7. Indítsa el a Windows PowerShellt a VM1-en, és a következő parancsok használatával telepítse az IIS-kiszolgálót, valamint frissítse az alapértelmezett htm-fájlt.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Az IIS telepítse és szabja testre a weblap](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Zárja be a RDP-kapcsolatot *myIISVMEastUS*.
9. Ismételje meg az 1-8 a lépéseket és a virtuális gép RDP-kapcsolatok létrehozásával *myIISVMWEurope* belül a *myResourceGroupTM2* erőforráscsoportot, amelybe az IIS telepítése és testre szabhatja a saját alapértelmezett weblapot.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurálja az IIS-alapú virtuális gépek DNS-nevek

A TRAFFIC Manager átirányítja a felhasználói forgalom a Szolgáltatásvégpontok DNS-neve alapján. Ebben a szakaszban konfigurálni az IIS-kiszolgálók – DNS-neveit *myIISVMEastUS* és *myIISVMWEurope*.

1. Kattintson a **összes erőforrás** válassza a bal oldali menüben, majd az erőforrások listájában, *myIISVMEastUS* található a *myResourceGroupTM1* erőforráscsoportot.
2. Az a **áttekintése** lap **DNS-név**válassza **konfigurálása**.
3. Az a **konfigurációs** oldalon, a DNS-névcímke, adjon hozzá egy egyedi nevet, és válassza ki **mentése**.
4. Ismételje meg az 1-3 lépéseket nevű virtuális gép *myIISVMWEurope* található a *myResourceGroupTM1* erőforráscsoportot.

### <a name="create-test-vms"></a>Hozzon létre virtuális gépek tesztelése

Ebben a szakaszban létrehoz egy virtuális Gépet (*mVMEastUS* és *myVMWestEurope*) minden egyes Azure-régióban (**USA keleti Régiójában** és **Nyugat-Európa**. Ezek a virtuális gépek használatával hogyan Traffic Manager átirányítja a forgalmat a legközelebbi IIS-kiszolgáló a webhely böngészése során tesztelheti.

1. A felső, jelölje be az Azure portál felső részén található left **erőforrás létrehozása** > **számítási** > **Windows Server 2016 virtuális gép**.
2. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVMEastUS|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza ki **meglévő** majd *myResourceGroupTM1*.|
    |||

4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki az alábbi értékeket a **Beállítások** területen, majd kattintson az **OK** gombra:
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| Válassza ki **virtuális hálózati**, a **virtuális hálózat létrehozása**, a **neve**, adja meg *myVNet3*, alhálózat, adja meg a * mySubnet*.|
    |Hálózati biztonsági csoport|Válassza ki **alapszintű**, majd a **nyilvános bejövő portok kiválasztása** legördülő menüben válassza **HTTP** és **RDP** |
    |Rendszerindítási diagnosztika|Válassza ki **letiltott**.|
    |||

6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. Végezze el ismét az 1 – 5. lépéseket a következő módosításokat:

    |Beállítás|Érték|
    |---|---|
    |Virtuális gép neve | *myVMWEurope*|
    |Erőforráscsoport | Válassza ki **meglévő**, majd írja be *myResourceGroupTM2*|
    |Virtuális hálózat | Válassza ki **virtuális hálózati**, a **virtuális hálózat létrehozása**, a **neve**, adja meg *myVNet4*, alhálózat, adja meg a * mySubnet*.|
    |||

8. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
Hozzon létre egy Traffic Manager-profilt, amely irányítja a felhasználói adatforgalmat, elküldheti a legkisebb késés a végponthoz.

1. A képernyő bal felső sarkában válassza **erőforrás létrehozása** > **hálózatkezelés** > **Traffic Manager-profil**  >  **Létrehozása**.
2. Az a **Traffic Manager-profil létrehozása**, adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, és válassza **létrehozás**:
    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Name (Név)                   | Ez a név egyedinek kell lennie a trafficmanager.net zónában és a Traffic Manager-profil eléréséhez használt trafficmanager.net DNS-nevet eredményezi.                                   |
    | Útválasztási mód          | Válassza ki a **prioritású** esetén használt útválasztási módszer.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza ki **új létrehozása** , és adja meg *myResourceGroupTM1*. |
    | Hely                | Válassza az **USA keleti régiója** lehetőséget.  Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan üzembe helyezendő Traffic Manager-profilra.                              |
    |
  
    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá a két virtuális gép fut az IIS-kiszolgálók – *myIISVMEastUS*  & *myIISVMWEurope* a legközelebbi végpontot, hogy a felhasználó a felhasználói forgalom irányítására.

1. A portál keresősávjában, keresse meg a Traffic Manager-profil neve, amely az előző szakaszban létrehozott, és válassza ki a profilt az eredmények között, amelyek jelenik meg.
2. A **Traffic Manager-profil**, a a **beállítások** területén kattintson **végpontok**, és kattintson a **Hozzáadás**.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Azure-végpont                                   |
    | Name (Név)           | myEastUSEndpoint                                        |
    | Célerőforrás típusa           | Nyilvános IP-cím                          |
    | Célerőforrás          | **Nyilvános IP-cím kiválasztása** azonos előfizetéshez tartozó nyilvános IP-címekkel rendelkező erőforrások listája megjelenítéséhez. A **erőforrás**, válassza ki a nyilvános IP-címet *myIISVMEastUS-ip*. Ez a virtuális gép az USA keleti régiójában IIS-kiszolgáló nyilvános IP-címét.|
    |        |           |

4. Ismételje meg a 2. és 3-nevű másik végpont hozzáadása *myWestEuropeEndpoint* a nyilvános IP-cím *myIISVMWEurope-ip* az IIS-kiszolgálón nevű virtuális Géphez társított *myIISVMWEurope *.
5.  Ha mindkét végpont hozzáadása befejeződött, a megjelenített **Traffic Manager-profil** azok figyelési állapotát, valamint **Online**.

    ![A Traffic Manager-végpont hozzáadása](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)
  

## <a name="test-traffic-manager-profile"></a>Teszt Traffic Manager-profil
Ebben a szakaszban tesztelni, hogyan a Traffic Manager átirányítja a felhasználói forgalmat a legközelebbi futó virtuális gépek a webhely minimális késést biztosít. A Traffic Manager megtekintése működés közben, kövesse az alábbi lépéseket:
1. A Traffic Manager-profil DNS-nevét határozza meg.
2. A Traffic Manager a következő művelet megjelenítése:
    - A teszt virtuális gép a (*myVMEastUS*) található a **USA keleti Régiójában** régió, a webböngésző, keresse meg a DNS-nevével a Traffic Manager-profil.
    - A teszt virtuális gép a (*myVMEastUS*) található a **Nyugat-Európa** régió, a webböngésző, keresse meg a DNS-nevével a Traffic Manager-profil.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Határozza meg a Traffic Manager-profil DNS-neve
Ebben az oktatóanyagban az egyszerűség kedvéért használja DNS-nevével a Traffic Manager-profil használatával keresse fel a webhelyek. 

Megadhatja, hogy a Traffic Manager-profil DNS-nevét a következő:

1.  A portál keresősávjában keressen a **Traffic Manager-profil** az előző szakaszban létrehozott nevét. A megjelenített eredmények között kattintson a traffic manager-profilt.
1. Kattintson a **áttekintése**.
2. A **Traffic Manager-profil** jeleníti meg az újonnan létrehozott Traffic Manager-profil DNS-nevét. Az éles környezetekben, hogy személyes tartománynév beállítása átirányítása a Traffic Manager szolgáltatásbeli tartománynévre, egy DNS CNAME rekord használatával.

   ![A TRAFFIC Manager DNS-név](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A művelet megtekintése a Traffic Manager
Ebben a szakaszban láthatja a Traffic Manager a művelet. 

1. Válassza ki **összes erőforrás** a bal oldali menüben, majd az erőforrások listájában kattintson *myVMEastUS* található a *myResourceGroupTM1* erőforráscsoportot.
2. Az a **áttekintése** kattintson **Connect**, majd **csatlakozhat a virtuális gép**, jelölje be **töltse le az RDP-fájl**. 
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához. 
1. Egy webböngészőben a virtuális gép *myVMEastUS*, írja be a Traffic Manager-profil megtekintéséhez a webhely DNS-nevét. Mivel a virtuális gép található **USA keleti Régiójában**, legyenek átirányítva a legközelebbi webhely legközelebbi IIS-kiszolgálón üzemeltetett *myIISVMEastUS* található **USA keleti Régiójában**.

   ![Teszt Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Ezután csatlakozhat a virtuális gép *myVMWestEurope* található **Nyugat-Európa** lépések használatával 1 – 5, és keresse meg a Traffic Manager-profil tartománynévvel a virtuális gépről.  Mivel a virtuális gép található **Nyugat-Európa**, most kapcsolódóak pedig az a webhely az IIS-kiszolgálón a legközelebbi lévő üzemeltetett *myIISVMWEurope* található **Nyugat-Európa**. 

   ![Teszt Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése
Ha már nincs rá szükség, törölje az erőforráscsoportot (**ResourceGroupTM1** és **ResourceGroupTM2**). Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Forgalom elosztása végpontok között](traffic-manager-configure-weighted-routing-method.md)


